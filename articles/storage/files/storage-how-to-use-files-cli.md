---
title: Azure CLI を使用して Azure ファイル共有を管理するためのクイック スタート
description: このクイックスタートでは、Azure CLI を使用して Azure Files を管理する方法について説明します。 リソース グループとストレージ アカウントを作成した後、Azure ファイル共有を作成して使用します。
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 10/26/2018
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurecli
ms.openlocfilehash: 5611088b76d8acf785fc0951100dcd4a2f439250
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104593188"
---
# <a name="quickstart-create-and-manage-azure-file-shares-using-azure-cli"></a>クイック スタート: Azure CLI を使用した Azure ファイル共有の作成および管理
このガイドでは、Azure CLI を使用して [Azure ファイル共有](storage-files-introduction.md)を操作する方法の基本について説明します。 Azure ファイル共有は他のファイル共有と似ていますが、クラウドに格納され、Azure プラットフォームによって支えられています。 Azure ファイル共有は、業界標準の サーバー メッセージ ブロック (SMB) プロトコル、ネットワーク ファイル システム (NFS) プロトコル (プレビュー) をサポートし、複数のマシン、アプリケーション、およびインスタンス間にわたってファイル共有を可能にします。 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- この記事では、Azure CLI のバージョン 2.0.4 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

- 既定では、Azure CLI コマンドは JavaScript Object Notation (JSON) を返します。 JSON は、REST API との間でメッセージを送受信するための標準的な方法です。 JSON 応答を操作しやすくするために、この記事のいくつかの例では、Azure CLI コマンドで *query* パラメーターを使用しています。 このパラメーターでは、JSON を解析するために [JMESPath クエリ言語](http://jmespath.org/)が使用されます。 JMESPath クエリ言語に従って Azure CLI コマンドの結果を使用する方法の詳細については、[JMESPath のチュートリアル](http://jmespath.org/tutorial.html)を参照してください。

## <a name="create-a-resource-group"></a>リソース グループを作成する
リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 まだ Azure リソース グループがない場合は、[az group create](/cli/azure/group) コマンドを使用して作成できます。 

次の例では、*myResourceGroup* という名前のリソース グループを *米国西部 2* に作成します。

```azurecli-interactive 
export resourceGroupName="myResourceGroup"
region="westus2"

az group create \
    --name $resourceGroupName \
    --location $region \
    --output none
```

## <a name="create-a-storage-account"></a>ストレージ アカウントの作成
ストレージ アカウントは、Azure ファイル共有またはその他のストレージ リソース (BLOB やキューなど) をデプロイできるストレージの共有プールです。 1 つのストレージ アカウントに格納できるファイル共有の数に制限はありません。 1 つの共有に格納できるファイルの数に制限はなく、ストレージ アカウントの容量の上限までファイルを格納できます。

次の例では、[az storage account create](/cli/azure/storage/account) コマンドを使用してストレージ アカウントを作成します。 ストレージ アカウント名は一意である必要があります。そのため、`$RANDOM` を使用し、名前に数を追加して一意にします。

```azurecli-interactive 
export storageAccountName="mystorageacct$RANDOM"

az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --location $region \
    --kind StorageV2 \
    --sku Standard_LRS \
    --enable-large-file-share \
    --output none
```

> [!Note]  
> 5 TiB (共有あたり最大 100 TiB) を超える共有が利用できるのは、ローカル冗長 (LRS) およびゾーン冗長 (ZRS) のストレージ アカウントだけです。 geo 冗長 (GRS) または geo ゾーン冗長 (GZRS) ストレージ アカウントを作成する場合は、`--enable-large-file-share` パラメーターを削除してください。

### <a name="get-the-storage-account-key"></a>ストレージ アカウント キーを取得する
ストレージ アカウント キーは、ストレージ アカウント内のリソースへのアクセスを制御するために使用されます。 キーは、ストレージ アカウントの作成時に自動的に作成されます。 ストレージ アカウントのストレージ アカウント キーは、[az storage account keys list](/cli/azure/storage/account/keys) コマンドを使用して取得できます。 

```azurecli-interactive 
export storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')
```

## <a name="create-an-azure-file-share"></a>Azure ファイル共有を作成する
これで、最初の Azure ファイル共有を作成できます。 [az storage share create](/cli/azure/storage/share) コマンドを使用してファイル共有を作成します。 この例では、*myshare* という名前の Azure ファイル共有を作成します。 

```azurecli-interactive
shareName="myshare"

az storage share create \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --name $shareName \
    --quota 1024 \
    --output none
```

共有名には、小文字の英字、数字、単一ハイフンのみを使用することができます (ただし、名前をハイフンで始めることはできません)。 ファイル共有とファイルの名前付けの詳細については、「[Naming and referencing shares, directories, files, and metadata (共有、ディレクトリ、ファイル、およびメタデータの名前付けおよび参照)](/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata)」を参照してください。

## <a name="use-your-azure-file-share"></a>Azure ファイル共有を使用する
Azure Files では、業界標準の [Server Message Block (SMB) プロトコル](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview)と[ファイル REST プロトコル](/rest/api/storageservices/file-service-rest-api)の 2 つの方法で Azure ファイル共有内のファイルとフォルダーを操作できます。 

SMB を使用してファイル共有をマウントするには、お使いの OS に基づいて次のドキュメントを参照してください。
- [Linux](storage-how-to-use-files-linux.md)
- [macOS](storage-how-to-use-files-mac.md)
- [Windows](storage-how-to-use-files-windows.md)

### <a name="using-an-azure-file-share-with-the-file-rest-protocol"></a>ファイル REST プロトコルで Azure ファイル共有を使用する 
ファイル REST プロトコルは直接使用する (手動で REST HTTP 呼び出しを作成する) こともできますが、ファイル REST プロトコルを使用する最も一般的な方法は、Azure CLI、[Azure PowerShell モジュール](storage-how-to-use-files-powershell.md)、または Azure Storage SDK を使用することです。いずれの方法でも、任意のスクリプト言語またはプログラミング言語でファイル REST プロトコルの便利なラッパーが提供されます。  

Azure Files のほとんどのユーザーは、SMB プロトコルを介して Azure ファイル共有を操作したいと考えていると予想されます。その方が、使用できることを期待している既存のアプリケーションやツールを使用できるためです。しかし、SMB よりもファイル REST API を使用する方が有益な理由がいくつかあります。その理由を次に示します。

- (SMB 経由でファイル共有をマウントできない) Azure Bash Cloud Shell からファイル共有を参照している。
- [Azure Functions](../../azure-functions/functions-overview.md) のようなサーバーレス リソースを利用している。 
- 多数の Azure ファイル共有を対話的に操作する付加価値サービスを作成している (バックアップやウイルス対策スキャンの実行など)。

次の例は、Azure CLI を使用してファイル REST プロトコルでお客様の Azure ファイル共有を操作する方法を示しています。 

### <a name="create-a-directory"></a>ディレクトリを作成する
Azure ファイル共有のルートに *myDirectory* という名前の新しいディレクトリを作成するには、[`az storage directory create`](/cli/azure/storage/directory) コマンドを使用します。

```azurecli-interactive
az storage directory create \
   --account-name $storageAccountName \
   --account-key $storageAccountKey \
   --share-name $shareName \
   --name "myDirectory" \
   --output none
```

### <a name="upload-a-file"></a>ファイルをアップロードする
[`az storage file upload`](/cli/azure/storage/file) コマンドを使用してファイルをアップロードする方法を示すために、最初に Cloud Shell のスクラッチ ドライブ上に、アップロードするファイルを作成します。 次の例では、ファイルを作成してアップロードします。

```azurecli-interactive
cd ~/clouddrive/
date > SampleUpload.txt

az storage file upload \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --source "SampleUpload.txt" \
    --path "myDirectory/SampleUpload.txt"
```

Azure CLI をローカルで実行している場合は、`~/clouddrive` を実際のマシンに存在するパスに置き換えてください。

ファイルのアップロード後、[`az storage file list`](/cli/azure/storage/file) コマンドを使用して、ファイルが Azure ファイル共有にアップロードされたことを確認できます。

```azurecli-interactive
az storage file list \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --path "myDirectory" \
    --output table
```

### <a name="download-a-file"></a>ファイルをダウンロードする
[`az storage file download`](/cli/azure/storage/file) コマンドを使用して、アップロードしたファイルのコピーを Cloud Shell のスクラッチ ドライブにダウンロードできます。

```azurecli-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists, because you've run this example before
rm -f SampleDownload.txt

az storage file download \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --path "myDirectory/SampleUpload.txt" \
    --dest "SampleDownload.txt" \
    --output none
```

### <a name="copy-files"></a>ファイルのコピー
よくあるタスクの 1 つとして、ファイル共有間でのファイルのコピーがあります。 この機能を示すために、新しい共有を作成します。 [az storage file copy](/cli/azure/storage/file/copy) コマンドを使用して、アップロードしたファイルをこの新しい共有にコピーします。 

```azurecli-interactive
otherShareName="myshare2"

az storage share create \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --name $otherShareName \
    --quota 1024 \
    --output none

az storage directory create \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $otherShareName \
    --name "myDirectory2" \
    --output none

az storage file copy start \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --source-share $shareName \
    --source-path "myDirectory/SampleUpload.txt" \
    --destination-share $otherShareName \
    --destination-path "myDirectory2/SampleCopy.txt"
```

次に、新しい共有のファイルを一覧表示すると、コピーしたファイルが表示されます。

```azurecli-interactive
az storage file list \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $otherShareName \
    --path "myDirectory2" \
    --output table
```

`az storage file copy start` コマンドは、Azure ファイル共有間でのファイル移動には便利ですが、大きなデータの移動や移行には、`rsync` (macOS および Linux) や `robocopy` (Windows) をお勧めします。 `rsync` と `robocopy` では、FileREST API ではなく SMB を使用して、データ移動が実行されます。

## <a name="create-and-manage-share-snapshots"></a>共有スナップショットの作成と管理
Azure ファイル共有で実行できるもう 1 つの便利なタスクとして、共有スナップショットの作成があります。 スナップショットでは、Azure ファイル共有の特定時点のコピーが保存されます。 共有スナップショットは、場合によっては既に使い慣れている、次のようなオペレーティング システム テクノロジに類似しています。

- Linux システム用の[論理ボリューム マネージャー (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) スナップショット。
- macOS 用の [Apple File System (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) スナップショット。
- NTFS や ReFS などの Windows ファイル システム用の[ボリューム シャドウ コピー サービス (VSS)](/windows/desktop/VSS/volume-shadow-copy-service-portal)。
 
[`az storage share snapshot`](/cli/azure/storage/share) コマンドを使用して、共有スナップショットを作成できます。

```azurecli-interactive
snapshot=$(az storage share snapshot \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --name $shareName \
    --query "snapshot" | tr -d '"')
```

### <a name="browse-share-snapshot-contents"></a>共有スナップショットの内容の参照
変数 `$snapshot` でキャプチャした共有スナップショットのタイム スタンプを `az storage file list` コマンドに渡すことで、共有スナップショットの内容を参照できます。

```azurecli-interactive
az storage file list \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --snapshot $snapshot \
    --output table
```

### <a name="list-share-snapshots"></a>共有スナップショットの一覧表示
共有に関して取得したスナップショットの一覧を表示するには、次のコマンドを使用します。

```azurecli-interactive
az storage share list \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --include-snapshot \
    --query "[? name== '$shareName' && snapshot!=null].snapshot" \
    --output tsv
```

### <a name="restore-from-a-share-snapshot"></a>共有スナップショットからの復元
前に使用した `az storage file copy start` コマンドを使用してファイルを復元できます。 最初に、スナップショットからファイルを復元するために、アップロードした SampleUpload.txt ファイルを削除します。

```azurecli-interactive
# Delete SampleUpload.txt
az storage file delete \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --path "myDirectory/SampleUpload.txt" \
    --output none

# Build the source URI for a snapshot restore
URI=$(az storage account show \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --query "primaryEndpoints.file" | tr -d '"')

URI=$URI$shareName"/myDirectory/SampleUpload.txt?sharesnapshot="$snapshot

# Restore SampleUpload.txt from the share snapshot
az storage file copy start \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --source-uri $URI \
    --destination-share $shareName \
    --destination-path "myDirectory/SampleUpload.txt"
```

### <a name="delete-a-share-snapshot"></a>共有スナップショットの削除
[`az storage share delete`](/cli/azure/storage/share) コマンドを使用して、共有スナップショットを削除できます。 `--snapshot` パラメーターへの `$SNAPSHOT` 参照を含む変数を使用します。

```azurecli-interactive
az storage share delete \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --name $shareName \
    --snapshot $snapshot \
    --output none
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする
完了したら、[`az group delete`](/cli/azure/group) コマンドを使用して、リソース グループとすべての関連リソースを削除できます。 

```azurecli-interactive 
az group delete --name $resourceGroupName
```

また、リソースを個別に削除することもできます。
- この記事用に作成した Azure ファイル共有を削除するには、次のコマンドを使用します。

    ```azurecli-interactive
    az storage share list \
            --account-name $storageAccountName \
            --account-key $storageAccountKey \
            --query "[].name" \
            --output tsv | \
        xargs -L1 bash -ec '\
            az storage share delete \
                --account-name "$storageAccountName" \
                --account-key "$storageAccountKey" \
                --name $0 \
                --delete-snapshots include \
                --output none'
    ```

- ストレージ アカウント自体を削除するには、次のコマンドを使用します  (この場合、作成した Azure ファイル共有と、Azure Blob Storage コンテナーなど、作成したその他のストレージ リソースが暗黙的に削除されます)。

    ```azurecli-interactive
    az storage account delete \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --yes
    ```

## <a name="next-steps"></a>次のステップ
> [!div class="nextstepaction"]
> [Azure Files とは何ですか。](storage-files-introduction.md)
