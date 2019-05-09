---
title: AzCopy v10 (プレビュー) を使用して Azure Storage にデータをコピーまたは移動する | Microsoft Docs
description: AzCopy v10 (プレビュー) コマンド ライン ユーティリティを使用して、BLOB、データ レイク、ファイル コンテンツとの間でデータを移動またはコピーします。 ローカル ファイルから Azure ストレージにデータをコピーする、またはストレージ アカウント内またはその間でデータをコピーします。 Azure Storage にデータを簡単に移行します。
services: storage
author: seguler
ms.service: storage
ms.topic: article
ms.date: 04/05/2019
ms.author: seguler
ms.subservice: common
ms.openlocfilehash: ffd448db86c8658619da5339cd34eb9dba7e05ce
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2019
ms.locfileid: "59278430"
---
# <a name="transfer-data-with-azcopy-v10-preview"></a>AzCopy v10 (プレビュー) を使用してデータを転送する

AzCopy v10 (プレビュー) は、Microsoft Azure の Blob Storage および File Storage との間でデータをコピーするためのコマンド ライン ユーティリティです。 AzCopy v10 では、新しいデザインになったコマンド ライン インターフェイスと、信頼性の高いデータ転送のための新しいアーキテクチャが提供されています。 AzCopy を使用すると、ファイル システムとストレージ アカウントの間、またはストレージ アカウント間で、データをコピーできます。

## <a name="whats-new-in-azcopy-v10"></a>AzCopy v10 の新機能

- ファイル システムから Azure Blob Storage に、またはその逆に、同期を行います。 `azcopy sync <source> <destination>`を使用します。 増分コピーのシナリオに最適です。
- Azure Data Lake Storage Gen2 API をサポートします。 URI として `myaccount.dfs.core.windows.net`を使用し、Data Lake Storage Gen2 API を呼び出します。
- 別のアカウントへのアカウント全体のコピーをサポートします (Blob service のみ)。
- アマゾン ウェブ サービス S3 バケットからのデータのコピーをサポートします。
- 新しい [Put Block from URL](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) API を使用して、アカウント間のコピーをサポートします。 データ転送が高速になったため、クライアントへの転送は必要ありません。
- 特定のパスのファイルおよび BLOB を一覧表示または削除します。
- パスおよび --exclude フラグで、ワイルドカード パターンがサポートされます。
- すべての AzCopy インスタンスで、ジョブの順序および関連するログ ファイルが作成されます。 以前のジョブを表示および再起動し、失敗したジョブを再開できます。 AzCopy は、障害後の転送の自動再試行も行います。
- 全般的なパフォーマンスの向上が行われています。

## <a name="download-and-install-azcopy"></a>AzCopy のダウンロードとインストール

### <a name="latest-preview-version-v10"></a>最新のプレビュー バージョン (v10)

AzCopy の最新のプレビュー バージョンは以下からダウンロードできます。
- [Windows](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Linux](https://aka.ms/downloadazcopy-v10-linux) (tar)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

### <a name="latest-production-version-v81"></a>最新の製品バージョン (v8.1)

[AzCopy for Windows の最新の製品バージョン](https://aka.ms/downloadazcopy)をダウンロードできます。

### <a name="azcopy-supporting-table-storage-service-v73"></a>Table Storage サービスをサポートする AzCopy (v7.3)

[Microsoft Azure Table Storage サービスとの間でのデータのコピーをサポートする AzCopy v7.3](https://aka.ms/downloadazcopynet) をダウンロードできます。

## <a name="post-installation-steps"></a>インストール後の手順

AzCopy v10 では、インストールは必要ありません。 任意のコマンド ライン アプリケーションを開き、`azcopy.exe` が配置されているフォルダーを参照します。 必要に応じて、使いやすいように AzCopy のフォルダーの場所をシステム パスに追加できます。

## <a name="authentication-options"></a>認証オプション

AzCopy v10 では、Azure Storage での認証時に、次のオプションがサポートされています。
- **Azure Active Directory** (**BLOB サービスと Data Lake Storage Gen2 サービス**でサポートされます)。 Azure Active Directory でサインインするには、```.\azcopy login``` を使用します。  Azure Active Directory 認証で Blob Storage に書き込むには、ユーザーに ["ストレージ BLOB データ共同作成者" ロールが割り当てられている](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac)必要があります。 Azure リソースに対してマネージド ID で認証を行うには、`azcopy login --identity` を使用します。
- **Shared Access Signature トークン (BLOB サービスとファイル サービスでサポートされます)**。 Shared Access Signature (SAS) トークンをコマンド ラインで BLOB パスに追加して使用します。 Azure portal、[Storage Explorer](https://blogs.msdn.microsoft.com/jpsanders/2017/10/12/easily-create-a-sas-to-download-a-file-from-azure-storage-using-azure-storage-explorer/)、[PowerShell](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageblobsastoken)、またはその他の好みのツールを使用して、SAS トークンを生成できます。 詳しくは、[例](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2)をご覧ください。

## <a name="getting-started"></a>使用の開始

> [!TIP]
> **グラフィカル ユーザー インターフェイスを使用したい場合**
>
> [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) (Azure Storage データの管理を簡素化するデスクトップ クライアント) では、Azure Storage との間でのデータ転送を高速化するために、AzCopy が使われるようになりました。
>
> Storage Explorer の **[プレビュー]** メニューで AzCopy を有効にします。
> ![Azure Storage Explorer で転送エンジンとして AzCopy を有効にする](media/storage-use-azcopy-v10/enable-azcopy-storage-explorer.jpg)

AzCopy v10 は、自己文書化された構文を備えています。 Azure Active Directory にログインしているときの一般的な構文は、次のようになります。

```azcopy
.\azcopy <command> <arguments> --<flag-name>=<flag-value>

# Examples if you have logged into the Azure Active Directory:
.\azcopy copy <source path> <destination path> --<flag-name>=<flag-value>
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/container" --recursive=true
.\azcopy cp "C:\local\path\myfile" "https://account.blob.core.windows.net/container/myfile"
.\azcopy cp "C:\local\path\*" "https://account.blob.core.windows.net/container"

# Examples if you're using SAS tokens to authenticate:
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/container?st=2019-04-05T04%3A10%3A00Z&se=2019-04-13T04%3A10%3A00Z&sp=rwdl&sv=2018-03-28&sr=c&sig=Qdihej%2Bsbg4AiuyLVyQZklm9pSuVGzX27qJ508wi6Es%3D" --recursive=true
.\azcopy cp "C:\local\path\myfile" "https://account.blob.core.windows.net/container/myfile?st=2019-04-05T04%3A10%3A00Z&se=2019-04-13T04%3A10%3A00Z&sp=rwdl&sv=2018-03-28&sr=c&sig=Qdihej%2Bsbg4AiuyLVyQZklm9pSuVGzX27qJ508wi6Es%3D"
```

使用可能なコマンドの一覧を取得する方法を次に示します。

```azcopy
.\azcopy --help
# To use the alias instead
.\azcopy -h
```

特定のコマンドのヘルプ ページと例を表示するには、次のコマンドを実行します。

```azcopy
.\azcopy <cmd> --help
# Example:
.\azcopy cp -h
```

## <a name="create-a-blob-container-or-file-share"></a>BLOB コンテナーまたはファイル共有を作成する 

**BLOB コンテナーを作成する**

```azcopy
.\azcopy make "https://account.blob.core.windows.net/container-name"
```

**ファイル共有を作成する**

```azcopy
.\azcopy make "https://account.file.core.windows.net/share-name"
```

**Azure Data Lake Storage Gen2 を使用して BLOB コンテナーを作成する**

BLOB ストレージ アカウントで階層型名前空間を有効にしている場合は、次のコマンドを使用して、ファイルのアップロード用に新しい BLOB コンテナーを作成できます。

```azcopy
.\azcopy make "https://account.dfs.core.windows.net/top-level-resource-name"
```

## <a name="copy-data-to-azure-storage"></a>Azure Storage にデータをコピーする

コピー元からコピー先にデータを転送するには、copy コマンドを使用します。 コピー元またはコピー先には以下を使用できます。
- ローカル ファイル システム
- Azure BLOB/仮想ディレクトリ/コンテナー URI
- Azure File/ディレクトリ/ファイル共有 URI
- Azure Data Lake Storage Gen2 ファイル システム/ディレクトリ/ファイル URI

```azcopy
.\azcopy copy <source path> <destination path> --<flag-name>=<flag-value>
# Using the alias instead 
.\azcopy cp <source path> <destination path> --<flag-name>=<flag-value>
```

次のコマンドでは、`C:\local\path` フォルダーの下にあるすべてのファイルが、`mycontainer1` コンテナーに再帰的にアップロードされて、コンテナーに `path` ディレクトリが作成されます。 `--put-md5` フラグが指定されている場合、AzCopy は、後で使用するために各ファイルの md5 ハッシュを計算して、対応する BLOB の `Content-md5` プロパティに格納します。

```azcopy
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/mycontainer1<sastoken>" --recursive=true --put-md5
```

次のコマンドでは、`C:\local\path` フォルダーにあるすべてのファイルを、(サブディレクトリに再帰せずに) `mycontainer1` コンテナーにアップロードします。

```azcopy
.\azcopy cp "C:\local\path\*" "https://account.blob.core.windows.net/mycontainer1<sastoken>" --put-md5
```

他の例を見るには、次のコマンドを使用します。

```azcopy
.\azcopy cp -h
```

## <a name="copy-blob-data-between-two-storage-accounts"></a>2 つのストレージ アカウント間で BLOB データをコピーする

2 つのストレージ アカウント間でデータをコピーするには、[Put Block From URL](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) API を使用し、クライアント マシンのネットワーク帯域幅は使用しません。 データは 2 つの Azure Storage サーバー間で直接コピーされ、AzCopy はコピー操作を調整するだけです。 このオプションは現在、Blob Storage でのみ使用可能です。

2 つのストレージ アカウント間ですべての BLOB データをコピーするには、次のコマンドを使用します。
```azcopy
.\azcopy cp "https://myaccount.blob.core.windows.net/<sastoken>" "https://myotheraccount.blob.core.windows.net/<sastoken>" --recursive=true
```

BLOB コンテナーをもう 1 つの BLOB コンテナーにコピーするには、次のコマンドを使用します。
```azcopy
.\azcopy cp "https://myaccount.blob.core.windows.net/mycontainer/<sastoken>" "https://myotheraccount.blob.core.windows.net/mycontainer/<sastoken>" --recursive=true
```

## <a name="copy-a-vhd-image-to-a-storage-account"></a>VHD イメージをストレージ アカウントにコピーする

既定では、AzCopy はデータをブロック BLOB にアップロードします。 追加 BLOB またはページ BLOB としてファイルをアップロードするには、`--blob-type=[BlockBlob|PageBlob|AppendBlob]` フラグを使用します。

```azcopy
.\azcopy cp "C:\local\path\mydisk.vhd" "https://myotheraccount.blob.core.windows.net/mycontainer/mydisk.vhd<sastoken>" --blob-type=PageBlob
```

## <a name="sync-incremental-copy-and-delete-blob-storage-only"></a>同期: 増分コピーと削除 (Blob ストレージのみ)

sync コマンドでは、ファイル名および最終更新日時のタイムスタンプが比較されて、同期元ディレクトリの内容が同期先のディレクトリに同期されます。 `--delete-destination=prompt|true` フラグが指定されているときに、同期元に存在しないファイルが同期先にある場合、この操作では、必要に応じてそれらのファイルが削除されます。 既定では、削除動作は無効になっています。 

> [!NOTE] 
> `--delete-destination` フラグを使用するときには注意が必要です。 アカウント内での誤削除を防ぐために、同期で削除動作を有効にする前に、[論理的な削除](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete)機能を有効にします。 
>
> `--delete-destination` が true に設定されている場合、AzCopy はユーザーにメッセージを表示せずに、同期元に存在しないファイルを同期先から削除します。 確認メッセージを表示する必要がある場合は、`--delete-destination=prompt` を使用します。

ストレージ アカウントにローカル ファイル システムを同期するには、次のコマンドを使用します。

```azcopy
.\azcopy sync "C:\local\path" "https://account.blob.core.windows.net/mycontainer1<sastoken>" --recursive=true
```

ローカル ファイル システムに BLOB コンテナーを同期することもできます。

```azcopy
# The SAS token isn't required for Azure Active Directory authentication.
.\azcopy sync "https://account.blob.core.windows.net/mycontainer1" "C:\local\path" --recursive=true
```

このコマンドでは、最終変更タイムスタンプに基づいて、同期元が同期先に増分的に同期されます。 同期元でファイルを追加または削除すると、AzCopy v10 は同期先で同じことを行います。 AzCopy では、削除する前に確認を求められます。

## <a name="copy-data-from-amazon-web-services-aws-s3"></a>アマゾン ウェブ サービス (AWS) S3 からデータをコピーする

AWS S3 バケットで認証するには、以下の環境変数を設定します。

```
# For Windows:
set AWS_ACCESS_KEY_ID=<your AWS access key>
set AWS_SECRET_ACCESS_KEY=<AWS secret access key>
# For Linux:
export AWS_ACCESS_KEY_ID=<your AWS access key>
export AWS_SECRET_ACCESS_KEY=<AWS secret access key>
# For MacOS
export AWS_ACCESS_KEY_ID=<your AWS access key>
export AWS_SECRET_ACCESS_KEY=<AWS secret access key>
```

バケットを BLOB コンテナーにコピーするには、次のコマンドを発行します。

```
.\azcopy cp "https://s3.amazonaws.com/mybucket" "https://myaccount.blob.core.windows.net/mycontainer?<sastoken>" --recursive
```

AzCopy を使用して AWS S3 からデータをコピーする方法の詳細については、[こちら](https://github.com/Azure/azure-storage-azcopy/wiki/Copy-from-AWS-S3)を参照してください。

## <a name="advanced-configuration"></a>詳細な構成

### <a name="configure-proxy-settings"></a>プロキシ設定の構成

AzCopy v10 のプロキシ設定を構成するには、次のコマンドを使用して環境変数 https_proxy を設定します。

```cmd
# For Windows:
set https_proxy=<proxy IP>:<proxy port>
# For Linux:
export https_proxy=<proxy IP>:<proxy port>
# For MacOS
export https_proxy=<proxy IP>:<proxy port>
```

### <a name="optimize-throughput"></a>スループットを最適化する

同時要求の数を構成し、スループットのパフォーマンスとリソースの消費量を制御するには、AZCOPY_CONCURRENCY_VALUE 環境変数を設定します。 既定では、値は 300 に設定されます。 値を小さくすると、AzCopy v10 で使用される CPU と帯域幅が制限されます。

```cmd
# For Windows:
set AZCOPY_CONCURRENCY_VALUE=<value>
# For Linux:
export AZCOPY_CONCURRENCY_VALUE=<value>
# For MacOS
export AZCOPY_CONCURRENCY_VALUE=<value>
# To check the current value of the variable on all the platforms
.\azcopy env
# If the value is blank then the default value is currently in use
```

### <a name="change-the-location-of-the-log-files"></a>ログ ファイルの場所を変更する

必要な場合や、OS ディスクがいっぱいにならないように、ログ ファイルの場所を変更できます。

```cmd
# For Windows:
set AZCOPY_LOG_LOCATION=<value>
# For Linux:
export AZCOPY_LOG_LOCATION=<value>
# For MacOS
export AZCOPY_LOG_LOCATION=<value>
# To check the current value of the variable on all the platforms
.\azcopy env
# If the value is blank, then the default value is currently in use
```
### <a name="change-the-default-log-level"></a>既定のログ レベルを変更する 

既定では、AzCopy ログ レベルは INFO に設定されます。 ディスク領域を節約するためにログ詳細度を下げたい場合は、``--log-level`` オプションを使用して設定を上書きます。 使用可能なログ レベルは、DEBUG、INFO、WARNING、ERROR、PANIC、FATAL です。

### <a name="review-the-logs-for-errors"></a>ログでエラーを確認する

次のコマンドでは、04dc9ca9-158f-7945-5933-564021086c79 ログから状態が UPLOADFAILED であるすべてのエラーが取得されます。

```azcopy
cat 04dc9ca9-158f-7945-5933-564021086c79.log | grep -i UPLOADFAILED
```
## <a name="troubleshooting"></a>トラブルシューティング

AzCopy v10 では、すべてのジョブに対してログ ファイルとプラン ファイルが作成されます。 ログを使用することで、潜在的な問題を調査してトラブルシューティングできます。 ログには、エラーの状態 (UPLOADFAILED、COPYFAILED、DOWNLOADFAILED)、完全なパス、エラーの理由が含まれます。 ジョブ ログとプラン ファイルは、Windows では %USERPROFILE\\.azcopy フォルダーに、Mac および Linux では $HOME\\.azcopy フォルダーにあります。

> [!IMPORTANT]
> Microsoft サポートに要求を送信するとき (または、サード パーティが関わる問題のトラブルシューティングを行うとき) は、実行したいコマンドの修正済みバージョンを共有します。 これにより、SAS が誤って誰かと共有されることがなくなります。 修正済みバージョンは、ログ ファイルの先頭にあります。

### <a name="view-and-resume-jobs"></a>ジョブを表示および再開する

各転送操作で AzCopy ジョブが作成されます。 ジョブの履歴を表示するには、次のコマンドを使用します。

```azcopy
.\azcopy jobs list
```

ジョブの統計情報を表示するには、次のコマンドを使います。

```azcopy
.\azcopy jobs show <job-id>
```

状態で転送をフィルター処理するには、次のコマンドを使います。

```azcopy
.\azcopy jobs show <job-id> --with-status=Failed
```

失敗したジョブまたは取り消されたジョブを再開するには、次のコマンドを使用します。 このコマンドでは、SAS トークンと共に識別子を使用します。SAS トークンは、セキュリティ上の理由で、永続的ではないためです。

```azcopy
.\azcopy jobs resume <jobid> --source-sas="<sastokenhere>"
.\azcopy jobs resume <jobid> --destination-sas="<sastokenhere>"
```

## <a name="next-steps"></a>次の手順

質問、問題、一般的なフィードバックは、[GitHub](https://github.com/Azure/azure-storage-azcopy) でお送りください。


