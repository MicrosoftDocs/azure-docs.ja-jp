---
title: "Azure Storage での Azure CLI 2.0 の使用 | Microsoft Docs"
description: "Azure Storage で Azure コマンドライン インターフェイス (Azure CLI) 2.0 を使用して、ストレージ アカウントの作成と管理および Azure の BLOB やファイルの操作を行う方法について説明します。 Azure CLI 2.0 は、Python で作成されたクロスプラットフォーム ツールです。"
services: storage
documentationcenter: na
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: article
ms.date: 02/18/2017
ms.author: marsma
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: be44ca9d14d6dbb7a50d5c42c163bc66531bb90f
ms.lasthandoff: 03/30/2017


---
# <a name="using-the-azure-cli-20-with-azure-storage"></a>Azure Storage での Azure CLI 2.0 の使用

オープンソースであるクロスプラットフォーム Azure CLI 2.0 には、Azure Platform で使用できるさまざまなコマンドが用意されています。 豊富なデータ アクセスを含む、 [Azure Portal](https://portal.azure.com) にあるものと同じ機能の多くを使用できます。

このガイドでは、[Azure CLI 2.0](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2) を使用して Azure Storage アカウント内のリソースを扱うタスクを実行する方法をいくつかご紹介します。 このガイドを使用する前に、最新バージョンの CLI 2.0 をダウンロードしてインストールするか、最新バージョンの CLI 2.0 にアップグレードすることをお勧めします。

ガイド内の例では、Ubuntu 上での Bash シェルの使用を想定していますが、その他のプラットフォームでも同様に動作します。 

[!INCLUDE [storage-cli-versions](../../includes/storage-cli-versions.md)]

## <a name="prerequisites"></a>前提条件
このガイドでは、Azure Storage の基本概念を理解していることを前提としています。 また、以下で指定されている、Azure および Storage サービスのアカウント作成要件を満たせることも前提としています。

### <a name="accounts"></a>アカウント
* **Azure アカウント**: まだ Azure サブスクリプションを持っていない場合は、[無料 Azure アカウントを作成](https://azure.microsoft.com/free/)します。
* **ストレージ アカウント**: 「[Azure ストレージ アカウントについて](../storage/storage-create-storage-account.md)」の「[ストレージ アカウントの作成](../storage/storage-create-storage-account.md#create-a-storage-account)」を参照してください。

### <a name="install-the-azure-cli-20"></a>Azure CLI 2.0 のインストール

「[Install the Azure CLI 2.0](/cli/azure/install-az-cli2)」(Azure CLI 2.0 のインストール) に記載されている手順に従って、Azure CLI 2.0 をダウンロードしてインストールします。

> [!TIP]
> インストールに問題がある場合は、その記事の「[Installation Troubleshooting](/cli/azure/install-az-cli2#installation-troubleshooting)」 (インストールのトラブルシューティング) セクションと、GitHub の「[インストールのトラブルシューティング](https://github.com/Azure/azure-cli/blob/master/doc/install_troubleshooting.md)」ガイドを確認してください。
>

## <a name="working-with-the-cli"></a>CLI の使用

CLI をインストールすると、コマンド ライン インターフェイス (Bash、Terminal、Command プロンプト) で `az` コマンドを使用して Azure CLI コマンドにアクセスできるようになります。 `az` コマンドを入力すると、下記のような内容が表示されます。

```
     /\
    /  \    _____   _ _ __ ___
   / /\ \  |_  / | | | \'__/ _ \
  / ____ \  / /| |_| | | |  __/
 /_/    \_\/___|\__,_|_|  \___|


Welcome to the cool new Azure CLI!

Here are the base commands:

    account   : Commands to manage subscriptions.
    acr       : Commands to manage Azure container registries.
    acs       : Commands to manage Azure container services.
    ad        : Synchronize on-premises directories and manage Azure Active Directory (AAD)
                resources.
    appservice: Commands to manage your Azure web apps and App Service plans.
    cloud     : Manage the Azure clouds registered.
    component : Commands to manage and update Azure CLI 2.0 components.
    configure : Configure Azure CLI 2.0 or view your configuration. The command is
                interactive, so just type `az configure` and respond to the prompts.
    container : Set up automated builds and deployments for multi-container Docker applications.
    disk      : Commands to manage 'Managed Disks'.
    feature   : Commands to manage resource provider features, such as previews.
    feedback  : Loving or hating the CLI?  Let us know!
    group     : Commands to manage resource groups.
    image     : Commands to manage custom virtual machine images based on managed disks/snapshots.
    lock
    login     : Log in to access Azure subscriptions.
    logout    : Log out to remove access to Azure subscriptions.
    network   : Manages Network resources.
    policy    : Commands to manage resource policies.
    provider  : Manage resource providers.
    resource  : Generic commands to manage Azure resources.
    role      : Use role assignments to manage access to your Azure resources.
    snapshot  : Commands to manage snapshots.
    storage   : Durable, highly available, and massively scalable cloud storage.
    tag       : Manage resource tags.
    vm        : Provision Linux and Windows virtual machines in minutes.
    vmss      : Create highly available, auto-scalable Linux or Windows virtual machines.
```

コマンド ライン インターフェイスで `az storage -h` コマンドを実行すると、`storage` グループ コマンドとそのサブグループが表示されます。 サブグループの説明として、ストレージ リソースを操作する Azure CLI 機能の概要が表示されます。

```
Group
    az storage: Durable, highly available, and massively scalable cloud storage.

Subgroups:
    account  : Manage storage accounts.
    blob     : Object storage for unstructured data.
    container: Manage blob storage containers.
    cors     : Manage Storage service Cross-Orgin Resource Sharing (CORS).
    directory: Manage file storage directories.
    entity   : Manage table storage entities.
    file     : File shares that use the standard SMB 3.0 protocol.
    logging  : Manage Storage service logging information.
    message  : Manage queue storage messages.
    metrics  : Manage Storage service metrics.
    queue    : Effectively scale apps according to traffic using queues.
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
az storage container create -n $container_name

echo "Uploading the file..."
az storage blob upload -f $file_to_upload -c $container_name -n $blob_name

echo "Listing the blobs..."
az storage blob list -c $container_name

echo "Downloading the file..."
az storage blob download -c $container_name -n $blob_name -f $destination_file

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
Success
---------
True
Uploading the file...                                           Percent complete: %100.0
Listing the blobs...
Name           Blob Type      Length  Content Type              Last Modified
-------------  -----------  --------  ------------------------  -------------------------
test_blob.txt  BlockBlob         771  application/octet-stream  2016-12-21T15:35:30+00:00
Downloading the file...
Name
-------------
test_blob.txt
Done
```

> [!TIP]
> 上記の出力は**テーブル**形式です。 CLI コマンドで `--output` 引数を指定するか、`az configure` でグローバルに設定することにより、使用する出力形式を指定することができます。
>

## <a name="manage-storage-accounts"></a>ストレージ アカウントを管理する

### <a name="create-a-new-storage-account"></a>新しいストレージ アカウントの作成
Azure Storage を使用するには、ストレージ アカウントが必要です。 コンピューターを構成して[サブスクリプションに接続](#connect-to-your-azure-subscription)できるようにすると、新しい Azure Storage アカウントを作成することができます。

```azurecli
az storage account create -l <location> -n <account_name> -g <resource_group> --sku <account_sku>
```

* `-l` [必須]: 場所。 たとえば "米国西部" にします。
* `-n` [必須]: ストレージ アカウント名。 アカウント名に含めることができるのは、英小文字と数字のみで、文字数は 3 ～ 24 文字にする必要があります。
* `-g`[必須]: リソース グループの名前。
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
az storage account show-connection-string -n <account_name> -g <resource_group>
```

出力された接続文字列をコピーし、`AZURE_STORAGE_CONNECTION_STRING` 環境変数を設定します (接続文字列を引用符で囲む必要がある可能性があります)。

```azurecli
export AZURE_STORAGE_CONNECTION_STRING=<connection_string>
```

> [!NOTE]
> この記事のこれ以降のセクションでは、すべての例は `AZURE_STORAGE_ACCOUNT` および `AZURE_STORAGE_ACCESS_KEY` 環境変数を設定済みであると仮定しています。
>

## <a name="create-and-manage-blobs"></a>BLOB を作成および管理する
Azure Blob Storage は、HTTP または HTTPS 経由で世界中のどこからでもアクセスできるテキストやバイナリ データなど、大量の非構造化データを格納するためのサービスです。 このセクションでは、Azure BLOB ストレージの概念について理解しているユーザーを対象としています。 詳しくは、「[.NET を使用して Azure Blob Storage を使用する](storage-dotnet-how-to-use-blobs.md)」と「[BLOB サービスの概念](/rest/api/storageservices/fileservices/blob-service-concepts)」をご覧ください。

### <a name="create-a-container"></a>コンテナーを作成する
Azure Storage のすべての BLOB はコンテナーに格納する必要があります。 次の `az storage container create` コマンドを使用して、コンテナーを作成できます。

```azurecli
az storage container create -n <container_name>
```

オプションの `--public-access` 引数を指定して、3 つのレベルの読み取りアクセスのいずれかを新しいコンテナーに設定することができます。

* `off` (既定値): コンテナー データはアカウント所有者のみがアクセスできます。
* `blob`: BLOB に対するパブリック読み取りアクセスです。
* `container`: コンテナ―全体に対するパブリックの読み取りおよび一覧表示アクセスです。

詳細については、「 [コンテナーと BLOB への匿名読み取りアクセスを管理する](storage-manage-access-to-resources.md)」をご覧ください。

### <a name="upload-a-blob-to-a-container"></a>コンテナーに BLOB をアップロードする
Azure Blob Storage では、ブロック BLOB、追加BLOB、ページ BLOB がサポートされています。 `blob upload` コマンドを使用して、コンテナーに BLOB をアップロードできます。

```azurecli
az storage blob upload -f <local_file_path> -c <container_name> -n <blob_name>
```

 既定では `blob upload` コマンドは *.vhd ファイルをページ BLOB にアップロードし、それ以外の場合はブロック BLOB にアップロードします。 BLOB アップロード時に別の種類を指定するには、`--type` 引数を使用することができます。使用できる値は `append`、`block`、および `page` です。

 異なる BLOB の種類の詳細については、「[Understanding Block Blobs, Append Blobs, and Page Blobs](/rest/api/storageservices/fileservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs)」 (ブロック BLOB、追加 BLOB、ページ BLOB について) をご覧ください。

### <a name="download-blobs-from-a-container"></a>コンテナーから BLOB をダウンロードする
この例は、BLOB をコンテナーからダウンロードする方法を示しています。

```azurecli
az storage blob download -c mycontainer -n myblob.png -f ~/mydownloadedblob.png
```

### <a name="copy-blobs"></a>BLOB をコピーする
BLOB は、ストレージ アカウント内、またはストレージ アカウントとリージョン間にまたがって非同期的にコピーできます。

次の例は、BLOB をあるストレージ アカウントから別のストレージ アカウントにコピーする方法を示しています。 最初に別のアカウントでコンテナーを作成し、 その BLOB をパブリックおよび匿名アクセス可能と指定します。 次にコンテナーにファイルをアップロードし、最後にそのコンテナーから BLOB を現在のアカウントの **mycontainer** コンテナーにコピーします。

```azurecli
az storage container create -n mycontainer2 --account-name <accountName2> --account-key <accountKey2> --public-access blob

az storage blob upload -f ~/Images/HelloWorld.png -c mycontainer2 -n myBlockBlob2 --account-name <accountName2> --account-key <accountKey2>

az storage blob copy start -u https://<accountname2>.blob.core.windows.net/mycontainer2/myBlockBlob2 -b myBlobBlob -c mycontainer
```

ソースの BLOB の URL (`-u` によって指定) は、パブリック アクセス可能であるか、共有アクセス署名 (SAS) トークンを含んでいる必要があります。

### <a name="delete-a-blob"></a>BLOB を削除する
BLOB を削除するには、`blob delete` コマンドを使用します。

```azurecli
az storage blob delete -c <container_name> -n <blob_name>
```

## <a name="create-and-manage-file-shares"></a>ファイル共有を作成および管理する
Azure File ストレージは、Server Message Block (SMB) プロトコルを使用して、アプリケーション用の共有ストレージを提供します。 Microsoft Azure の仮想マシンとクラウド サービスでは、オンプレミスのアプリケーションと同じように、ファイル データを共有できます。 ファイル共有とファイル データは、Azure CLI を使用して管理できます。 Azure File ストレージについて詳しくは、「[Windows で Azure File Storage を使用する](storage-dotnet-how-to-use-files.md)」または「[Linux で Azure File Storage を使用する方法](storage-how-to-use-files-linux.md)」をご覧ください。

### <a name="create-a-file-share"></a>ファイル共有を作成する
Azure File 共有は、Azure 内の SMB ファイル共有です。 ディレクトリとファイルはすべて、ファイル共有に作成する必要があります。 アカウントに含まれる共有の数と、共有に格納できるファイル数には制限がなく、ストレージ アカウントの容量の上限まで増やすことができます。 次の例では、 **myshare**という名前のファイル共有を作成します。

```azurecli
az storage share create -n myshare
```

### <a name="create-a-directory"></a>ディレクトリを作成する
ディレクトリは、Azure ファイル共有の任意の階層構造を示します。 次の例では、ファイル共有に **myDir** という名前のディレクトリを作成します。

```azurecli
az storage directory create -n myDir -s myshare
```

ディレクトリ パスには複数のレベルを含めることができます ( *例*: **a/b**)。 ただし、すべての親ディレクトリが存在することを確認する必要があります。 たとえば、パス **a/b** の場合、最初に**a** ディレクトリを作成した後、**b** ディレクトリを作成する必要があります。

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
az storage file list -s myshare

# List the files in a directory within a share
az storage file list -s myshare/myDir

# List the files in a path within a share
az storage file list -s myshare -p myDir/mySubDir/MySubDir2
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

