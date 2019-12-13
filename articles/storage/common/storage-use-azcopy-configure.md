---
title: Azure Storage での AzCopy の構成、最適化、およびトラブルシューティング | Microsoft Docs
description: AzCopy の構成、最適化、トラブルシューティングを行います。
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: c16fea8f710751a051995ecece8a3d0ce8f933c7
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926449"
---
# <a name="configure-optimize-and-troubleshoot-azcopy"></a>AzCopy の構成、最適化、トラブルシューティング

AzCopy は、ストレージ アカウント間の BLOB またはファイル コピーに利用できるコマンドライン ユーティリティです。 この記事は、高度な構成タスクを実行する際に役立ち、AzCopy を使用しているときに発生する可能性のある問題をトラブルシューティングするのに役立ちます。

> [!NOTE]
> AzCopy で作業を始める際に役立つコンテンツを探している場合は、以下のいずれかの記事を参照してください。
> - [AzCopy を使ってみる](storage-use-azcopy-v10.md)
> - [AzCopy と BLOB ストレージでデータを転送する](storage-use-azcopy-blobs.md)
> - [AzCopy とファイル ストレージでデータを転送する](storage-use-azcopy-files.md)
> - [AzCopy と Amazon S3 バケットでデータを転送する](storage-use-azcopy-s3.md)

## <a name="configure-proxy-settings"></a>プロキシ設定の構成

AzCopy v10 のプロキシ設定を構成するには、`https_proxy` 環境変数を設定します。 Windows で AzCopy を実行すると、AzCopy によって自動的にプロキシ設定が検出されるため、Windows でこの設定を使用する必要はありません。 Windows でこの設定を使用することを選択した場合は、自動検出がオーバーライドされます。

| オペレーティング システム | command  |
|--------|-----------|
| **Windows** | コマンド プロンプトでは、`set https_proxy=<proxy IP>:<proxy port>` を使用します<br> PowerShell では、`$env:https_proxy="<proxy IP>:<proxy port>"` を使用します|
| **Linux** | `export https_proxy=<proxy IP>:<proxy port>` |
| **MacOS** | `export https_proxy=<proxy IP>:<proxy port>` |

現在のところ、AzCopy は、NTLM または Kerberos による認証を必要とするプロキシをサポートしていません。

## <a name="optimize-performance"></a>パフォーマンスを最適化する

パフォーマンスのベンチマークを実行し、コマンドと環境変数を使用して、パフォーマンスとリソース消費の最適なトレードオフを見つけることができます。

### <a name="run-benchmark-tests"></a>ベンチマーク テストを実行する

特定の BLOB コンテナーに対してパフォーマンス ベンチマーク テストを実行して、全般的なパフォーマンスの統計情報を表示し、パフォーマンスのボトルネックを識別できます。 

> [!NOTE]
> 現在のリリースでは、この機能は Blob Storage コンテナーに対してのみ使用できます。

パフォーマンス ベンチマーク テストを実行するには、次のコマンドを使用します。

|    |     |
|--------|-----------|
| **構文** | `azcopy bench 'https://<storage-account-name>.blob.core.windows.net/<container-name>'` |
| **例** | `azcopy bench 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

このコマンドは、指定したコピー先にテスト データをアップロードすることで、パフォーマンス ベンチマークを実行します。 テスト データはメモリ内に生成され、コピー先にアップロードされた後、テストの完了後にコピー先から削除されます。 オプションのコマンド パラメーターを使用して、生成するファイルの数と、必要なサイズを指定できます。

このコマンドの詳細なヘルプ ガイダンスを表示するには、「`azcopy bench -h`」と入力して Enter キーを押してください。

### <a name="optimize-throughput"></a>スループットを最適化する

`cap-mbps` フラグを使用して、スループット データ速度の上限を設定できます。 たとえば、次のコマンドはスループットを 1 秒あたり `10` メガビット (MB) に制限します。

```azcopy
azcopy --cap-mbps 10
```

小さいファイルを転送すると、スループットが低下することがあります。 `AZCOPY_CONCURRENCY_VALUE` 環境変数を設定することにより、スループットを向上させることができます。 この変数は、同時に発生することができる要求の数を指定します。  

コンピューターの CPU が 5 個未満の場合、この変数の値は `32` に設定されます。 それ以外の場合、既定値は CPU の数に 16 を掛けた数です。 この変数の最大の既定値は `3000` ですが、この値は手動で高い値または低い値に設定できます。 

| オペレーティング システム | command  |
|--------|-----------|
| **Windows** | `set AZCOPY_CONCURRENCY_VALUE=<value>` |
| **Linux** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |
| **MacOS** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |

この変数の現在の値を確認するには、`azcopy env` を使用します。 値が空白の場合は、AzCopy ログ ファイルの先頭を調べることで、使用されている値を読み取ることができます。 選択された値と選択された理由が報告されています。

この変数を設定する前に、ベンチマーク テストを実行することをお勧めします。 ベンチマーク テスト プロセスによって、推奨されるコンカレンシーの値が報告されます。 または、ネットワークの状態とペイロードが一様でない場合は、この変数を特定の数値ではなく `AUTO` という単語に設定します。 これにより、AzCopy はベンチマーク テストで使用するのと同じ自動チューニング プロセスを常に実行します。

### <a name="optimize-memory-use"></a>メモリ使用量を最適化する

`AZCOPY_BUFFER_GB` 環境変数を設定して、ファイルをダウンロードおよびアップロードするときに AzCopy で使用するシステム メモリの最大量を指定します。
この値はギガバイト (GB) 単位で指定します。

| オペレーティング システム | command  |
|--------|-----------|
| **Windows** | `set AZCOPY_BUFFER_GB=<value>` |
| **Linux** | `export AZCOPY_BUFFER_GB=<value>` |
| **MacOS** | `export AZCOPY_BUFFER_GB=<value>` |

## <a name="troubleshoot-issues"></a>問題のトラブルシューティング

AzCopy は、ジョブごとにログ ファイルとプラン ファイルを作成します。 ログを使用することで、潜在的な問題を調査してトラブルシューティングできます。 

ログには、エラーの状態 (`UPLOADFAILED`、`COPYFAILED`、および `DOWNLOADFAILED`)、完全なパス、エラーの理由が含まれます。

既定では、ログ ファイルとプラン ファイルは、Windows では `%USERPROFILE%\.azcopy` ディレクトリに、Mac および Linux では `$HOME$\.azcopy` ディレクトリにありますが、必要に応じてその場所を変更できます。

> [!IMPORTANT]
> Microsoft サポートに要求を送信するとき (または、サード パーティが関わる問題のトラブルシューティングを行うとき) は、実行したいコマンドの修正済みバージョンを共有します。 これにより、SAS が誤って誰かと共有されることがなくなります。 修正済みバージョンは、ログ ファイルの先頭にあります。

### <a name="review-the-logs-for-errors"></a>ログでエラーを確認する

次のコマンドでは、`04dc9ca9-158f-7945-5933-564021086c79` ログから状態が `UPLOADFAILED` であるすべてのエラーが取得されます。

**Windows (PowerShell)**

```
Select-String UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

**Linux**

```
grep UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

### <a name="view-and-resume-jobs"></a>ジョブを表示および再開する

各転送操作で AzCopy ジョブが作成されます。 ジョブの履歴を表示するには、次のコマンドを使用します。

```
azcopy jobs list
```

ジョブの統計情報を表示するには、次のコマンドを使います。

```
azcopy jobs show <job-id>
```

状態で転送をフィルター処理するには、次のコマンドを使います。

```
azcopy jobs show <job-id> --with-status=Failed
```

失敗したジョブまたは取り消されたジョブを再開するには、次のコマンドを使用します。 このコマンドでは、SAS トークンと共に識別子を使用します。SAS トークンは、セキュリティ上の理由で、永続的ではないためです。

```
azcopy jobs resume <job-id> --source-sas="<sas-token>"
azcopy jobs resume <job-id> --destination-sas="<sas-token>"
```

ジョブを再開すると、AzCopy がジョブ プラン ファイルを確認します。 プラン ファイルには、ジョブが最初に作成されたときに処理対象として識別されたすべてのファイルの一覧が表示されます。 ジョブを再開すると、AzCopy は、プラン ファイルの一覧に表示されているファイルのうち、まだ転送されていないファイルをすべて転送しようとします。

## <a name="change-the-location-of-the-plan-and-log-files"></a>プラン ファイルおよびログ ファイルの場所を変更する

既定では、プラン ファイルとログ ファイルは、Windows では `%USERPROFILE%\.azcopy` ディレクトリに、Mac および Linux では `$HOME$\.azcopy` ディレクトリにあります。 この場所は変更できます。

### <a name="change-the-location-of-plan-files"></a>プラン ファイルの場所を変更する

これらのコマンドのいずれかを使用します。

| オペレーティング システム | command  |
|--------|-----------|
| **Windows** | `set AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **Linux** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **MacOS** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |

この変数の現在の値を確認するには、`azcopy env` を使用します。 値が空白の場合、プラン ファイルは既定の場所に書き込まれます。

### <a name="change-the-location-of-log-files"></a>ログ ファイルの場所を変更する

これらのコマンドのいずれかを使用します。

| オペレーティング システム | command  |
|--------|-----------|
| **Windows** | `set AZCOPY_LOG_LOCATION=<value>` |
| **Linux** | `export AZCOPY_LOG_LOCATION=<value>` |
| **MacOS** | `export AZCOPY_LOG_LOCATION=<value>` |

この変数の現在の値を確認するには、`azcopy env` を使用します。 値が空白の場合、ログは既定の場所に書き込まれます。

## <a name="change-the-default-log-level"></a>既定のログ レベルを変更する

既定では、AzCopy ログ レベルは `INFO` に設定されます。 ディスク領域を節約するためにログ詳細度を下げたい場合は、``--log-level`` オプションを使用してこの設定を上書きます。 

使用可能なログ レベルは、`NONE`、`DEBUG`、`INFO`、`WARNING`、`ERROR`、`PANIC`、および `FATAL` です。

## <a name="remove-plan-and-log-files"></a>プラン ファイルとログ ファイルを削除する

ディスク領域を節約するために、すべてのプラン ファイルとログ ファイルをローカル コンピューターから削除する場合は、`azcopy jobs clean` コマンドを使用します。

1 つのジョブのみに関連付けられているプラン ファイルとログ ファイルを削除するには、`azcopy jobs rm <job-id>` を使用します。 この例の `<job-id>` というプレースホルダーをジョブのジョブ ID に置き換えてください。


