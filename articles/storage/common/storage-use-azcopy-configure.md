---
title: Azure Storage での AzCopy の構成、最適化、およびトラブルシューティング | Microsoft Docs
description: Azure Storage での AzCopy の構成、最適化、およびトラブルシューティング プラン ファイルおよびログ ファイルの場所を変更するか、プラン ファイルおよびログ ファイルを削除します。 既定のログ レベルを変更します。
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 07/27/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 244012f0945f467fe79e95d652ba22e3b62a1b7a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100596935"
---
# <a name="configure-optimize-and-troubleshoot-azcopy"></a>AzCopy の構成、最適化、トラブルシューティング

AzCopy は、ストレージ アカウント間の BLOB またはファイル コピーに利用できるコマンドライン ユーティリティです。 この記事は、高度な構成タスクを実行する際に役立ち、AzCopy を使用しているときに発生する可能性のある問題をトラブルシューティングするのに役立ちます。

> [!NOTE]
> AzCopy で作業を始める際に役立つコンテンツを探している場合は、以下のいずれかの記事を参照してください。
> - [AzCopy を使ってみる](storage-use-azcopy-v10.md)
> - [AzCopy と Blob Storage でデータを転送する](./storage-use-azcopy-v10.md#transfer-data)
> - [AzCopy とファイル ストレージでデータを転送する](storage-use-azcopy-files.md)
> - [AzCopy と Amazon S3 バケットでデータを転送する](storage-use-azcopy-s3.md)

## <a name="configure-proxy-settings"></a>プロキシ設定の構成

AzCopy v10 のプロキシ設定を構成するには、`HTTPS_PROXY` 環境変数を設定します。 Windows で AzCopy を実行すると、AzCopy によって自動的にプロキシ設定が検出されるため、Windows でこの設定を使用する必要はありません。 Windows でこの設定を使用することを選択した場合は、自動検出がオーバーライドされます。

| オペレーティング システム | command  |
|--------|-----------|
| **Windows** | コマンド プロンプトでは、`set HTTPS_PROXY=<proxy IP>:<proxy port>` を使用します<br> PowerShell では、`$env:HTTPS_PROXY="<proxy IP>:<proxy port>"` を使用します|
| **Linux** | `export HTTPS_PROXY=<proxy IP>:<proxy port>` |
| **macOS** | `export HTTPS_PROXY=<proxy IP>:<proxy port>` |

現在のところ、AzCopy は、NTLM または Kerberos による認証を必要とするプロキシをサポートしていません。

### <a name="bypassing-a-proxy"></a>プロキシのバイパス ###

Windows で AzCopy を実行していて、プロキシを使用 _しない_ ように (設定の自動検出ではなく) 指定する場合、次のコマンドを使用します。 これらの設定を使用すると、AzCopy はプロキシを検索したり、使用したりしません。

| オペレーティング システム | 環境 | コマンド  |
|--------|-----------|----------|
| **Windows** | コマンド プロンプト (CMD) | `set HTTPS_PROXY=dummy.invalid` <br>`set NO_PROXY=*`|
| **Windows** | PowerShell | `$env:HTTPS_PROXY="dummy.invalid"` <br>`$env:NO_PROXY="*"`<br>|

他のオペレーティング システムでは、プロキシを使用しない場合は HTTPS_PROXY 変数の設定を解除してください。

## <a name="optimize-performance"></a>パフォーマンスを最適化する

パフォーマンスのベンチマークを実行し、コマンドと環境変数を使用して、パフォーマンスとリソース消費の最適なトレードオフを見つけることができます。

このセクションでは、次の最適化タスクを実行する方法について説明します。

> [!div class="checklist"]
> * ベンチマーク テストを実行する
> * スループットを最適化する
> * メモリ使用量を最適化する 
> * ファイル同期を最適化する

### <a name="run-benchmark-tests"></a>ベンチマーク テストを実行する

特定の BLOB コンテナーまたはファイル共有に対してパフォーマンス ベンチマーク テストを実行して、全般的なパフォーマンスの統計情報を表示し、パフォーマンスのボトルネックを識別できます。 生成されたテスト データをアップロードまたはダウンロードして、テストを実行できます。 

パフォーマンス ベンチマーク テストを実行するには、次のコマンドを使用します。

|    |     |
|--------|-----------|
| **構文** | `azcopy benchmark 'https://<storage-account-name>.blob.core.windows.net/<container-name>'` |
| **例** | `azcopy benchmark 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

> [!TIP]
> この例では、パス引数を単一引用符 ('') で囲んでいます。 Windows コマンド シェル (cmd.exe) を除き、すべてのコマンド シェルで単一引用符を使用します。 Windows コマンド シェル (cmd.exe) を使用している場合は、単一引用符 ('') ではなく、二重引用符 ("") でパス引数を囲みます。

このコマンドは、指定したコピー先にテスト データをアップロードすることで、パフォーマンス ベンチマークを実行します。 テスト データはメモリ内に生成され、コピー先にアップロードされた後、テストの完了後にコピー先から削除されます。 オプションのコマンド パラメーターを使用して、生成するファイルの数と、必要なサイズを指定できます。

データをダウンロードしてこのテストを実行する場合は、`mode` パラメーターを `download` に設定します。 詳細なリファレンス ドキュメントについては、「[azcopy benchmark](storage-ref-azcopy-bench.md)」を参照してください。 

### <a name="optimize-throughput"></a>スループットを最適化する

コマンドで `cap-mbps` フラグを使用して、スループット データ速度の上限を設定できます。 たとえば、次のコマンドではジョブを再開し、スループットを 1 秒あたり `10` メガビット (Mb) に制限します。 

```azcopy
azcopy jobs resume <job-id> --cap-mbps 10
```

小さいファイルを転送すると、スループットが低下することがあります。 `AZCOPY_CONCURRENCY_VALUE` 環境変数を設定することにより、スループットを向上させることができます。 この変数は、同時に発生することができる要求の数を指定します。  

コンピューターの CPU が 5 個未満の場合、この変数の値は `32` に設定されます。 それ以外の場合、既定値は CPU の数に 16 を掛けた数です。 この変数の最大の既定値は `3000` ですが、この値は手動で高い値または低い値に設定できます。 

| オペレーティング システム | command  |
|--------|-----------|
| **Windows** | `set AZCOPY_CONCURRENCY_VALUE=<value>` |
| **Linux** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |
| **macOS** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |

この変数の現在の値を確認するには、`azcopy env` を使用します。 値が空白の場合は、AzCopy ログ ファイルの先頭を調べることで、使用されている値を読み取ることができます。 選択された値と選択された理由が報告されています。

この変数を設定する前に、ベンチマーク テストを実行することをお勧めします。 ベンチマーク テスト プロセスによって、推奨されるコンカレンシーの値が報告されます。 または、ネットワークの状態とペイロードが一様でない場合は、この変数を特定の数値ではなく `AUTO` という単語に設定します。 これにより、AzCopy はベンチマーク テストで使用するのと同じ自動チューニング プロセスを常に実行します。

### <a name="optimize-memory-use"></a>メモリ使用量を最適化する

`AZCOPY_BUFFER_GB` 環境変数を設定して、ファイルをダウンロードおよびアップロードするときに AzCopy で使用するシステム メモリの最大量を指定します。
この値はギガバイト (GB) 単位で指定します。

| オペレーティング システム | command  |
|--------|-----------|
| **Windows** | `set AZCOPY_BUFFER_GB=<value>` |
| **Linux** | `export AZCOPY_BUFFER_GB=<value>` |
| **macOS** | `export AZCOPY_BUFFER_GB=<value>` |

### <a name="optimize-file-synchronization"></a>ファイル同期を最適化する

[sync](storage-ref-azcopy-sync.md) コマンドは、転送先のすべてのファイルを識別し、同期操作を開始する前に、ファイル名と最終変更されたタイムスタンプを比較します。 多数のファイルがある場合は、この前処理を排除することでパフォーマンスを向上させることができます。 

これを実現するには、代わりに [azcopy copy](storage-ref-azcopy-copy.md) コマンドを使用して、`--overwrite` フラグを `ifSourceNewer`に設定します。 AzCopy では、アップフロントスキャンと比較を実行せずに、ファイルがコピーされると比較されます。 これにより、比較対象のファイルの数が多い場合にパフォーマンスが低下します。

[azcopy copy](storage-ref-azcopy-copy.md) コマンドは、転送先からファイルを削除しません。そのため、コピー先のファイルがコピー元に存在しなくなったときにファイルを削除する場合は、[azcopy sync](storage-ref-azcopy-sync.md) コマンドを使用し、`--delete-destination` フラグを `true` または `prompt`の値に設定します。 

## <a name="troubleshoot-issues"></a>問題のトラブルシューティング

AzCopy は、ジョブごとにログ ファイルとプラン ファイルを作成します。 ログを使用することで、潜在的な問題を調査してトラブルシューティングできます。 

ログには、エラーの状態 (`UPLOADFAILED`、`COPYFAILED`、および `DOWNLOADFAILED`)、完全なパス、エラーの理由が含まれます。

既定では、ログ ファイルとプラン ファイルは、Windows では `%USERPROFILE%\.azcopy` ディレクトリに、Mac および Linux では `$HOME$\.azcopy` ディレクトリにありますが、必要に応じてその場所を変更できます。

関連するエラーは、必ずしもファイルに表示される最初のエラーではありません。 ネットワーク エラー、タイムアウト、サーバー ビジー エラーなどのエラーの場合、AzCopy により最大 20 回まで再試行され、通常は再試行プロセスが成功します。  最初に表示されるエラーは、正常に再試行された無害なものである可能性があります。  そのため、ファイルの最初のエラーを確認するのではなく、`UPLOADFAILED`、`COPYFAILED`、または `DOWNLOADFAILED` の近くにあるエラーを探します。 

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

> [!TIP]
> SAS トークンなどのパス引数は単一引用符 ('') で囲みます。 Windows コマンド シェル (cmd.exe) を除き、すべてのコマンド シェルで単一引用符を使用します。 Windows コマンド シェル (cmd.exe) を使用している場合は、単一引用符 ('') ではなく、二重引用符 ("") でパス引数を囲みます。

ジョブを再開すると、AzCopy がジョブ プラン ファイルを確認します。 プラン ファイルには、ジョブが最初に作成されたときに処理対象として識別されたすべてのファイルの一覧が表示されます。 ジョブを再開すると、AzCopy は、プラン ファイルの一覧に表示されているファイルのうち、まだ転送されていないファイルをすべて転送しようとします。

## <a name="change-the-location-of-the-plan-and-log-files"></a>プラン ファイルおよびログ ファイルの場所を変更する

既定では、プラン ファイルとログ ファイルは、Windows では `%USERPROFILE%\.azcopy` ディレクトリに、Mac および Linux では `$HOME/.azcopy` ディレクトリにあります。 この場所は変更できます。

### <a name="change-the-location-of-plan-files"></a>プラン ファイルの場所を変更する

これらのコマンドのいずれかを使用します。

| オペレーティング システム | command  |
|--------|-----------|
| **Windows** | PowerShell:`$env:AZCOPY_JOB_PLAN_LOCATION="<value>"` <br> コマンド プロンプトでは次を使用します: `set AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **Linux** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **macOS** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |

この変数の現在の値を確認するには、`azcopy env` を使用します。 値が空白の場合、プラン ファイルは既定の場所に書き込まれます。

### <a name="change-the-location-of-log-files"></a>ログ ファイルの場所を変更する

これらのコマンドのいずれかを使用します。

| オペレーティング システム | command  |
|--------|-----------|
| **Windows** | PowerShell:`$env:AZCOPY_LOG_LOCATION="<value>"` <br> コマンド プロンプトでは次を使用します: `set AZCOPY_LOG_LOCATION=<value>`|
| **Linux** | `export AZCOPY_LOG_LOCATION=<value>` |
| **macOS** | `export AZCOPY_LOG_LOCATION=<value>` |

この変数の現在の値を確認するには、`azcopy env` を使用します。 値が空白の場合、ログは既定の場所に書き込まれます。

## <a name="change-the-default-log-level"></a>既定のログ レベルを変更する

既定では、AzCopy ログ レベルは `INFO` に設定されます。 ディスク領域を節約するためにログ詳細度を下げたい場合は、``--log-level`` オプションを使用してこの設定を上書きます。 

使用可能なログ レベルは、`NONE`、`DEBUG`、`INFO`、`WARNING`、`ERROR`、`PANIC`、および `FATAL` です。

## <a name="remove-plan-and-log-files"></a>プラン ファイルとログ ファイルを削除する

ディスク領域を節約するために、すべてのプラン ファイルとログ ファイルをローカル コンピューターから削除する場合は、`azcopy jobs clean` コマンドを使用します。

1 つのジョブのみに関連付けられているプラン ファイルとログ ファイルを削除するには、`azcopy jobs rm <job-id>` を使用します。 この例の `<job-id>` というプレースホルダーをジョブのジョブ ID に置き換えてください。
