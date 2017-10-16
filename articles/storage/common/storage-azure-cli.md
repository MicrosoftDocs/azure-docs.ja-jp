---
title: "Azure Storage での Azure CLI 2.0 の使用 | Microsoft Docs"
description: "Azure Storage で Azure コマンドライン インターフェイス (Azure CLI) 2.0 を使用して、ストレージ アカウントの作成と管理および Azure の BLOB やファイルの操作を行う方法について説明します。 Azure CLI 2.0 は、Python で作成されたクロスプラットフォーム ツールです。"
services: storage
documentationcenter: na
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: article
ms.date: 06/02/2017
ms.author: tamram
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: b1f192a935dbf234936690b0d36b6ce708d38b14
ms.contentlocale: ja-jp
ms.lasthandoff: 09/25/2017

---
# <a name="using-the-azure-cli-20-with-azure-storage"></a>Azure Storage での Azure CLI 2.0 の使用

オープンソースであるクロスプラットフォーム Azure CLI 2.0 には、Azure Platform で使用できるさまざまなコマンドが用意されています。 豊富なデータ アクセスを含む、 [Azure Portal](https://portal.azure.com) にあるものと同じ機能の多くを使用できます。

このガイドでは、[Azure CLI 2.0](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2) を使用して Azure Storage アカウント内のリソースを扱うタスクを実行する方法をいくつかご紹介します。 このガイドを使用する前に、最新バージョンの CLI 2.0 をダウンロードしてインストールするか、最新バージョンの CLI 2.0 にアップグレードすることをお勧めします。

ガイド内の例では、Ubuntu 上での Bash シェルの使用を想定していますが、その他のプラットフォームでも同様に動作します。 

[!INCLUDE [storage-cli-versions](../../../includes/storage-cli-versions.md)]

## <a name="prerequisites"></a>前提条件
このガイドでは、Azure Storage の基本概念を理解していることを前提としています。 また、以下で指定されている、Azure および Storage サービスのアカウント作成要件を満たせることも前提としています。

### <a name="accounts"></a>アカウント
* **Azure アカウント**: まだ Azure サブスクリプションを持っていない場合は、[無料 Azure アカウントを作成](https://azure.microsoft.com/free/)します。
* **ストレージ アカウント**: 「[Azure ストレージ アカウントについて](storage-create-storage-account.md)」の「[ストレージ アカウントの作成](storage-create-storage-account.md#create-a-storage-account)」を参照してください。

### <a name="install-the-azure-cli-20"></a>Azure CLI 2.0 のインストール

「[Install the Azure CLI 2.0](/cli/azure/install-az-cli2)」(Azure CLI 2.0 のインストール) に記載されている手順に従って、Azure CLI 2.0 をダウンロードしてインストールします。

> [!TIP]
> インストールに問題がある場合は、その記事の「[Installation Troubleshooting](/cli/azure/install-az-cli2#installation-troubleshooting)」 (インストールのトラブルシューティング) セクションと、GitHub の「[インストールのトラブルシューティング](https://github.com/Azure/azure-cli/blob/master/doc/install_troubleshooting.md)」ガイドを確認してください。
>

## <a name="working-with-the-cli"></a>CLI の使用

CLI をインストールすると、コマンド ライン インターフェイス (Bash、Terminal、Command プロンプト) で `az` コマンドを使用して Azure CLI コマンドにアクセスできるようになります。 基礎コマンドの完全な一覧を表示するには、`az` と入力します (次の出力例は切り捨てられています)。

```
     /\
    /  \    _____   _ _ __ ___
   / /\ \  |_  / | | | \'__/ _ \
  / ____ \  / /| |_| | | |  __/
 /_/    \_\/___|\__,_|_|  \___|


Welcome to the cool new Azure CLI!

Here are the base commands:

    account          : Manage subscriptions.
    acr              : Manage Azure container registries.
    acs              : Manage Azure Container Services.
    ad               : Synchronize on-premises directories and manage Azure Active Directory
                       resources.
    ...
```

コマンド ライン インターフェイスで `az storage --help` コマンドを実行すると、`storage` コマンドのサブグループが表示されます。 サブグループの説明として、ストレージ リソースを操作する Azure CLI 機能の概要が表示されます。

```
Group
    az storage: Durable, highly available, and massively scalable cloud storage.

Subgroups:
    account  : Manage storage accounts.
    blob     : Object storage for unstructured data.
    container: Manage blob storage containers.
    cors     : Manage Storage service Cross-Origin Resource Sharing (CORS).
    directory: Manage file storage directories.
    entity   : Manage table storage entities.
    file     : File shares that use the standard SMB 3.0 protocol.
    logging  : Manage Storage service logging information.
    message  : Manage queue storage messages.
    metrics  : Manage Storage service metrics.
    queue    : Use queues to effectively scale applications according to traffic.
    share    : Manage file shares.
    table    : NoSQL key-value storage using semi-structured datasets.
```

## <a name="connect-the-cli-to-your-azure-subscription"></a>Azure サブスクリプションへの CLI の接続

Azure サブスクリプション内のリソースを操作するには、最初に `az login` で Azure アカウントにログインする必要があります。 ログインの方法はいくつかあります。

* **対話型ログイン**: `az login`
* **ユーザー名とパスワードによるログイン**: `az login -u johndoe@contoso.com -p VerySecret`
  * これは、Microsoft アカウントまたは多要素認証を使用するアカウントでは機能しません。
* **サービス プリンシパルによるログイン**: `az login --service-principal -u http://azure-cli-2016-08-05-14-31-15 -p VerySecret --tenant contoso.onmicrosoft.com`

## <a name="azure-cli-20-sample-script"></a>Azure CLI 2.0 サンプル スクリプト

次に、Azure Storage リソースを操作するいくつかの基本的な Azure CLI 2.0 コマンドを発行する、簡単なシェル スクリプトを説明します。 スクリプトはまず最初にストレージ アカウントに新しいコンテナーを作成し、そのコンテナーに任意の既存ファイルを (BLOB として) アップロードします。 次にコンテナー内のすべての BLOB を一覧表示し、最後に、指定したローカル コンピューター上の宛先にファイルをダウンロードします。

```bash
#!/bin/bash
# A simple Azure Storage example script

export AZURE_STORAGE_ACCOUNT=<storage_account_name>
export AZURE_STORAGE_ACCESS_KEY=<storage_account_key>

export container_name=<container_name>
export blob_name=<blob_name>
export file_to_upload=<file_to_upload>
export destination_file=<destination_file>

echo "Creating the container..."
az storage container create --name $container_name

echo "Uploading the file..."
az storage blob upload --container-name $container_name --file $file_to_upload --name $blob_name

echo "Listing the blobs..."
az storage blob list --container-name $container_name --output table

echo "Downloading the file..."
az storage blob download --container-name $container_name --name $blob_name --file $destination_file --output table

echo "Done"
```

**スクリプトの構成と実行**

1. 好みのテキスト エディターを開き、前述のスクリプトをコピーしてエディターに貼り付けます。

2. 次に、ご自分の設定内容に合わせてスクリプトの変数を更新します。 次のように値を置き換えます。

   * **\<storage_account_name\>** 使用するストレージ アカウントの名前。
   * **\<storage_account_key\>** ストレージ アカウントのプライマリまたはセカンダリ アクセス キー。
   * **\<container_name\>** 新規作成するコンテナーの名前。たとえば "azure-cli-sample-container" など。
   * **\<blob_name\>** コンテナー内の宛先 BLOB の名前。
   * **\<file_to_upload\>** ローカル コンピューター上のファイルのパス。たとえば "~/images/HelloWorld.png" など。
   * **\<destination_file\>** 宛先ファイルへのパス。たとえば "~/downloadedImage.png" など。

3. 必要な変数を更新したら、スクリプトを保存してエディターを終了します。 スクリプト名を **my_storage_sample.sh** に指定したと仮定して、次のステップに移ります。

4. 必要に応じてスクリプトを実行可能ファイルとしてマークします。`chmod +x my_storage_sample.sh`

5. スクリプトを実行します。 たとえば Bash の場合は次のようになります。 `./my_storage_sample.sh`

下記のような出力が表示され、スクリプトで指定した **\<destination_file\>** がローカル コンピューターに表示されるはずです。

```
Creating the container...
{
  "created": true
}
Uploading the file...
Percent complete: %100.0
Listing the blobs...
Name       Blob Type      Length  Content Type              Last Modified
---------  -----------  --------  ------------------------  -------------------------
README.md  BlockBlob        6700  application/octet-stream  2017-05-12T20:54:59+00:00
Downloading the file...
Name
---------
README.md
Done
```

> [!TIP]
> 上記の出力は**テーブル**形式です。 CLI コマンドで `--output` 引数を指定するか、`az configure` でグローバルに設定することにより、使用する出力形式を指定することができます。
>

## <a name="manage-storage-accounts"></a>ストレージ アカウントを管理する

### <a name="create-a-new-storage-account"></a>新しいストレージ アカウントの作成
Azure Storage を使用するには、ストレージ アカウントが必要です。 コンピューターを構成して[サブスクリプションに接続](#connect-to-your-azure-subscription)できるようにすると、新しい Azure Storage アカウントを作成することができます。

```azurecli
az storage account create \
    --location <location> \
    --name <account_name> \
    --resource-group <resource_group> \
    --sku <account_sku>
```

* `--location` [必須]: 場所。 たとえば "米国西部" にします。
* `--name` [必須]: ストレージ アカウント名。 アカウント名に含めることができるのは、英小文字と数字のみで、文字数は 3 ～ 24 文字にする必要があります。
* `--resource-group`[必須]: リソース グループの名前。
* `--sku` [必須]: ストレージ アカウント SKU。 使用できる値は以下の通りです。
  * `Premium_LRS`
  * `Standard_GRS`
  * `Standard_LRS`
  * `Standard_RAGRS`
  * `Standard_ZRS`

### <a name="set-default-azure-storage-account-environment-variables"></a>既定の Azure ストレージ アカウント環境変数を設定する
Azure サブスクリプションでは複数のストレージ アカウントを持つことができます。 それらの 1 つを選んですべての後続のストレージ コマンドに使用するには、下記のように環境変数を設定します。

```azurecli
export AZURE_STORAGE_ACCOUNT=<account_name>
export AZURE_STORAGE_ACCESS_KEY=<key>
```

既定のストレージ アカウントを設定するもう 1 つの方法としては、接続文字列の使用があります。 まず、`show-connection-string` コマンドで接続文字列を取得します。

```azurecli
az storage account show-connection-string \
    --name <account_name> \
    --resource-group <resource_group>
```

出力された接続文字列をコピーし、`AZURE_STORAGE_CONNECTION_STRING` 環境変数を設定します (接続文字列を引用符で囲む必要がある可能性があります)。

```azurecli
export AZURE_STORAGE_CONNECTION_STRING="<connection_string>"
```

> [!NOTE]
> この記事のこれ以降のセクションでは、すべての例は `AZURE_STORAGE_ACCOUNT` および `AZURE_STORAGE_ACCESS_KEY` 環境変数を設定済みであると仮定しています。
>

## <a name="create-and-manage-blobs"></a>BLOB を作成および管理する
Azure Blob Storage は、HTTP または HTTPS 経由で世界中のどこからでもアクセスできるテキストやバイナリ データなど、大量の非構造化データを格納するためのサービスです。 このセクションでは、Azure BLOB ストレージの概念について理解しているユーザーを対象としています。 詳しくは、「[.NET を使用して Azure Blob Storage を使用する](../blobs/storage-dotnet-how-to-use-blobs.md)」と「[BLOB サービスの概念](/rest/api/storageservices/blob-service-concepts)」をご覧ください。

### <a name="create-a-container"></a>コンテナーを作成する
Azure Storage のすべての BLOB はコンテナーに格納する必要があります。 次の `az storage container create` コマンドを使用して、コンテナーを作成できます。

```azurecli
az storage container create --name <container_name>
```

オプションの `--public-access` 引数を指定して、3 つのレベルの読み取りアクセスのいずれかを新しいコンテナーに設定することができます。

* `off` (既定値): コンテナー データはアカウント所有者のみがアクセスできます。
* `blob`: BLOB に対するパブリック読み取りアクセスです。
* `container`: コンテナ―全体に対するパブリックの読み取りおよび一覧表示アクセスです。

詳細については、「 [コンテナーと BLOB への匿名読み取りアクセスを管理する](../blobs/storage-manage-access-to-resources.md)」をご覧ください。

### <a name="upload-a-blob-to-a-container"></a>コンテナーに BLOB をアップロードする
Azure Blob Storage では、ブロック BLOB、追加BLOB、ページ BLOB がサポートされています。 `blob upload` コマンドを使用して、コンテナーに BLOB をアップロードできます。

```azurecli
az storage blob upload \
    --file <local_file_path> \
    --container-name <container_name> \
    --name <blob_name>
```

 既定では `blob upload` コマンドは *.vhd ファイルをページ BLOB にアップロードし、それ以外の場合はブロック BLOB にアップロードします。 BLOB アップロード時に別の種類を指定するには、`--type` 引数を使用することができます。使用できる値は `append`、`block`、および `page` です。

 異なる BLOB の種類の詳細については、「[Understanding Block Blobs, Append Blobs, and Page Blobs](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs)」 (ブロック BLOB、追加 BLOB、ページ BLOB について) をご覧ください。


### <a name="download-a-blob-from-a-container"></a>コンテナーから BLOB をダウンロードする
この例は、BLOB をコンテナーからダウンロードする方法を示しています。

```azurecli
az storage blob download \
    --container-name mycontainer \
    --name myblob.png \
    --file ~/mydownloadedblob.png
```

### <a name="list-the-blobs-in-a-container"></a>コンテナー内の BLOB を一覧表示する

コンテナー内の BLOB は、[az storage blob list](/cli/azure/storage/blob#list) コマンドで一覧表示します。

```azurecli
az storage blob list \
    --container-name mycontainer \
    --output table
```

### <a name="copy-blobs"></a>BLOB をコピーする
BLOB は、ストレージ アカウント内、またはストレージ アカウントとリージョン間にまたがって非同期的にコピーできます。

次の例は、BLOB をあるストレージ アカウントから別のストレージ アカウントにコピーする方法を示しています。 まず、ソース ストレージ アカウントにコンテナーを作成し、その BLOB にパブリックな読み取りアクセス権を指定します。 次にコンテナーにファイルをアップロードし、最後にそのコンテナーから目的のストレージ アカウントのコンテナーに BLOB をコピーします。

```azurecli
# Create container in source account
az storage container create \
    --account-name sourceaccountname \
    --account-key sourceaccountkey \
    --name sourcecontainer \
    --public-access blob

# Upload blob to container in source account
az storage blob upload \
    --account-name sourceaccountname \
    --account-key sourceaccountkey \
    --container-name sourcecontainer \
    --file ~/Pictures/sourcefile.png \
    --name sourcefile.png

# Copy blob from source account to destination account (destcontainer must exist)
az storage blob copy start \
    --account-name destaccountname \
    --account-key destaccountkey \
    --destination-blob destfile.png \
    --destination-container destcontainer \
    --source-uri https://sourceaccountname.blob.core.windows.net/sourcecontainer/sourcefile.png
```

この例のコピー操作が成功するためには、目的のストレージ アカウントにコピー先となるコンテナーがあらかじめ存在していることが必要です。 加えて、`--source-uri` 引数に指定したコピー元の BLOB が、Shared Access Signature (SAS) トークンを含んでいるか、この例のようにパブリックにアクセス可能であることが必要です。

### <a name="delete-a-blob"></a>BLOB を削除する
BLOB を削除するには、`blob delete` コマンドを使用します。

```azurecli
az storage blob delete --container-name <container_name> --name <blob_name>
```

## <a name="create-and-manage-file-shares"></a>ファイル共有を作成および管理する
Azure File は、サーバー メッセージ ブロック (SMB) プロトコルを使用して、アプリケーション用の共有ストレージを提供します。 Microsoft Azure の仮想マシンとクラウド サービスでは、オンプレミスのアプリケーションと同じように、ファイル データを共有できます。 ファイル共有とファイル データは、Azure CLI を使用して管理できます。 Azure File の詳細については、「[Azure File Storage の概要](../files/storage-files-introduction.md)」を参照してください。

### <a name="create-a-file-share"></a>ファイル共有を作成する
Azure File 共有は、Azure 内の SMB ファイル共有です。 ディレクトリとファイルはすべて、ファイル共有に作成する必要があります。 アカウントに含まれる共有の数と、共有に格納できるファイル数には制限がなく、ストレージ アカウントの容量の上限まで増やすことができます。 次の例では、 **myshare**という名前のファイル共有を作成します。

```azurecli
az storage share create --name myshare
```

### <a name="create-a-directory"></a>ディレクトリを作成する
ディレクトリは、Azure ファイル共有の階層構造を示します。 次の例では、ファイル共有に **myDir** という名前のディレクトリを作成します。

```azurecli
az storage directory create --name myDir --share-name myshare
```

ディレクトリ パスには複数のレベルを含めることができます (**dir1/dir2**)。 ただし、サブディレクトリを作成する前に、すべての親ディレクトリが存在することを確認する必要があります。 たとえば、パス **dir1/dir2**の場合、最初に**dir1** ディレクトリを作成した後、**dir2** ディレクトリを作成する必要があります。

### <a name="upload-a-local-file-to-a-share"></a>共有にローカル ファイルをアップロードする
次の例では、**~/temp/samplefile.txt** から **myshare** ファイル共有のルートにファイルをアップロードします。 `--source` 引数で、アップロードする既存のローカル ファイルを指定します。

```azurecli
az storage file upload --share-name myshare --source ~/temp/samplefile.txt
```

ディレクトリ作成と同様に、共有内のディレクトリ パスを指定して、共有内の既存のディレクトリにファイルをアップロードすることができます。

```azurecli
az storage file upload --share-name myshare/myDir --source ~/temp/samplefile.txt
```

共有内のファイル サイズは最大 1 TB です。

### <a name="list-the-files-in-a-share"></a>共有内のファイルを一覧表示する
`az storage file list` コマンドを使用して、共有内のファイルとディレクトリを一覧表示できます。

```azurecli
# List the files in the root of a share
az storage file list --share-name myshare --output table

# List the files in a directory within a share
az storage file list --share-name myshare/myDir --output table

# List the files in a path within a share
az storage file list --share-name myshare --path myDir/mySubDir/MySubDir2 --output table
```

### <a name="copy-files"></a>ファイルのコピー      
ファイルを別のファイルにコピーしたり、ファイルを BLOB にコピーしたり、BLOB をファイルにコピーしたりすることができます。 たとえば、別の共有にあるディレクトリにファイルをコピーします。        
        
```azurecli
az storage file copy start \
--source-share share1 --source-path dir1/file.txt \
--destination-share share2 --destination-path dir2/file.txt     
```

## <a name="next-steps"></a>次のステップ
下記の資料で、Azure CLI 2.0 の使用に関する詳細をさらにご覧いただけます。

* [Azure CLI 2.0 の概要](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)
* [Azure CLI 2.0 コマンド リファレンス](/cli/azure)
* [GitHub の Azure CLI 2.0](https://github.com/Azure/azure-cli)

