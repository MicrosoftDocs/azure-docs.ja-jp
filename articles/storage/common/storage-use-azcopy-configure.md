---
title: Azure Storage での AzCopy の構成、最適化、およびトラブルシューティング | Microsoft Docs
description: AzCopy の構成、最適化、トラブルシューティングを行います。
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 1a67846889b43d582a7a7d477a33f0e2168fd760
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147863"
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

AzCopy v10 のプロキシ設定を構成するには、`https_proxy` 環境変数を設定します。

| オペレーティング システム | command  |
|--------|-----------|
| **Windows** | コマンド プロンプトでは、`set https_proxy=<proxy IP>:<proxy port>` を使用します<br> PowerShell では、`$env:https_proxy="<proxy IP>:<proxy port>"` を使用します|
| **Linux** | `export https_proxy=<proxy IP>:<proxy port>` |
| **MacOS** | `export https_proxy=<proxy IP>:<proxy port>` |

現在のところ、AzCopy は、NTLM または Kerberos による認証を必要とするプロキシをサポートしていません。

## <a name="optimize-throughput"></a>スループットを最適化する

同時要求の数を構成し、スループットのパフォーマンスとリソースの消費量を制御するには、`AZCOPY_CONCURRENCY_VALUE` 環境変数を設定します。 コンピューターの CPU が 5 個未満の場合、この変数の値は `32` に設定されます。 それ以外の場合、既定値は CPU の数に 16 を掛けた数です。 この変数の最大の既定値は `300` ですが、この値は手動で高い値または低い値に設定できます。

| オペレーティング システム | command  |
|--------|-----------|
| **Windows** | `set AZCOPY_CONCURRENCY_VALUE=<value>` |
| **Linux** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |
| **MacOS** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |

この変数の現在の値を確認するには、`azcopy env` を使用します。  値が空白の場合、`AZCOPY_CONCURRENCY_VALUE` 変数は既定値の `300` に設定されます。

## <a name="change-the-location-of-the-log-files"></a>ログ ファイルの場所を変更する

既定では、ログ ファイルは、Windows では `%USERPROFILE\\.azcopy` ディレクトリに、Mac および Linux では `$HOME\\.azcopy` ディレクトリにあります。 この場所は、以下のコマンドを使用することで、必要に応じて変更することができます。

| オペレーティング システム | command  |
|--------|-----------|
| **Windows** | `set AZCOPY_LOG_LOCATION=<value>` |
| **Linux** | `export AZCOPY_LOG_LOCATION=<value>` |
| **MacOS** | `export AZCOPY_LOG_LOCATION=<value>` |

この変数の現在の値を確認するには、`azcopy env` を使用します。 値が空白の場合、ログは既定の場所に書き込まれます。

## <a name="change-the-default-log-level"></a>既定のログ レベルを変更する

既定では、AzCopy ログ レベルは `INFO` に設定されます。 ディスク領域を節約するためにログ詳細度を下げたい場合は、``--log-level`` オプションを使用してこの設定を上書きます。 

使用可能なログ レベルは、`DEBUG`、`INFO`、`WARNING`、`ERROR`、`PANIC`、および `FATAL` です。

## <a name="troubleshoot-issues"></a>問題のトラブルシューティング

AzCopy は、ジョブごとにログ ファイルとプラン ファイルを作成します。 ログを使用することで、潜在的な問題を調査してトラブルシューティングできます。 

ログには、エラーの状態 (`UPLOADFAILED`、`COPYFAILED`、および `DOWNLOADFAILED`)、完全なパス、エラーの理由が含まれます。

既定では、ログ ファイルとプラン ファイルは、Windows では `%USERPROFILE\\.azcopy` ディレクトリに、Mac および Linux では `$HOME\\.azcopy` ディレクトリにあります。

> [!IMPORTANT]
> Microsoft サポートに要求を送信するとき (または、サード パーティが関わる問題のトラブルシューティングを行うとき) は、実行したいコマンドの修正済みバージョンを共有します。 これにより、SAS が誤って誰かと共有されることがなくなります。 修正済みバージョンは、ログ ファイルの先頭にあります。

### <a name="review-the-logs-for-errors"></a>ログでエラーを確認する

次のコマンドでは、`04dc9ca9-158f-7945-5933-564021086c79` ログから状態が `UPLOADFAILED` であるすべてのエラーが取得されます。

**Windows**

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