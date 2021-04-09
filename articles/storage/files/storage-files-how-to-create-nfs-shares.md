---
title: NFS 共有を作成する - Azure Files (プレビュー)
description: ネットワーク ファイル システム プロトコルを使用してマウントできる Azure ファイル共有を作成する方法について説明します。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 01/22/2021
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: b085b9991175d8cd43e2dac0db80c5af4e703c34
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102521239"
---
# <a name="how-to-create-an-nfs-share"></a>NFS 共有を作成する方法
Azure ファイル共有は、クラウド内に存在するフル マネージドのファイル共有です。 この記事では、NFS プロトコルを使用するファイル共有の作成について説明します。 両方のプロトコルの詳細については、[Azure ファイル共有のプロトコル](storage-files-compare-protocols.md)に関するページを参照してください。

## <a name="limitations"></a>制限事項
[!INCLUDE [files-nfs-limitations](../../../includes/files-nfs-limitations.md)]

### <a name="regional-availability"></a>リージョン別の提供状況
[!INCLUDE [files-nfs-regional-availability](../../../includes/files-nfs-regional-availability.md)]

## <a name="prerequisites"></a>前提条件
- NFS 共有には、信頼されたネットワークからしかアクセスできません。 NFS 共有への接続は、次のいずれかのソースから行う必要があります。
    - [プライベート エンドポイントを作成する](storage-files-networking-endpoints.md#create-a-private-endpoint) (推奨) か、または[パブリック エンドポイントへのアクセスを制限](storage-files-networking-endpoints.md#restrict-public-endpoint-access)します。
    - [Azure Files で使用する Linux 上のポイント対サイト (P2S) VPN を構成](storage-files-configure-p2s-vpn-linux.md)します。
    - [Azure Files で使用するサイト間 VPN を構成](storage-files-configure-s2s-vpn.md)します。
    - [ExpressRoute](../../expressroute/expressroute-introduction.md) を構成します。

- Azure CLI を使用する場合は、[最新バージョンをインストールしてください](/cli/azure/install-azure-cli)。

## <a name="register-the-nfs-41-protocol"></a>NFS 4.1 プロトコルを登録する
Azure PowerShell モジュールまたは Azure CLI を使用している場合は、次のコマンドを使用して機能を登録します。

# <a name="portal"></a>[ポータル](#tab/azure-portal)
Azure PowerShell または Azure CLI を使用して、Azure Files に NFS 4.1 機能を登録します。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```azurepowershell
# Connect your PowerShell session to your Azure account, if you have not already done so.
Connect-AzAccount

# Set the actively selected subscription, if you have not already done so.
$subscriptionId = "<yourSubscriptionIDHere>"
$context = Get-AzSubscription -SubscriptionId $subscriptionId
Set-AzContext $context

# Register the NFS 4.1 feature with Azure Files to enable the preview.
Register-AzProviderFeature `
    -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowNfsFileShares 
    
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
# Connect your Azure CLI to your Azure account, if you have not already done so.
az login

# Provide the subscription ID for the subscription where you would like to 
# register the feature
subscriptionId="<yourSubscriptionIDHere>"

az feature register \
    --name AllowNfsFileShares \
    --namespace Microsoft.Storage \
    --subscription $subscriptionId

az provider register \
    --namespace Microsoft.Storage
```

---

登録の承認には、最大 1 時間かかります。 登録が完了したことを確認するには、次のコマンドを使用します。

# <a name="portal"></a>[ポータル](#tab/azure-portal)
Azure PowerShell または Azure CLI を使用して、Azure Files への NFS 4.1 機能の登録を確認します。 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```azurepowershell
Get-AzProviderFeature `
    -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowNfsFileShares
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az feature show \
    --name AllowNfsFileShares \
    --namespace Microsoft.Storage \
    --subscription $subscriptionId
```

---

## <a name="create-a-filestorage-storage-account"></a>FileStorage ストレージ アカウントを作成する
現時点では、NFS 4.1 共有は Premium ファイル共有としてのみ使用できます。 NFS 4.1 プロトコル サポートを使用して Premium ファイル共有をデプロイするには、最初に FileStorage ストレージ アカウントを作成する必要があります。 ストレージ アカウントは、Azure の最上位レベルのオブジェクトで、複数の Azure ファイル共有のデプロイに使用できるストレージの共有プールを表します。

# <a name="portal"></a>[ポータル](#tab/azure-portal)
FileStorage ストレージ アカウントを作成するには、Azure portal に移動します。

1. Azure portal の左側のメニューで **[ストレージ アカウント]** を選択します。

    ![Azure portal のメイン ページでストレージ アカウントを選択する](media/storage-how-to-create-premium-fileshare/azure-portal-storage-accounts.png)

2. 表示された **[ストレージ アカウント]** ウィンドウで **[追加]** を選択します。
3. ストレージ アカウントを作成するサブスクリプションを選択します。
4. ストレージ アカウントを作成するリソース グループを選択します。

5. 次に、ストレージ アカウントの名前を入力します。 選択する名前は Azure 全体で一意である必要があります。 また、名前の長さは 3 から 24 文字とし、数字と小文字のみを使用できます。
6. ストレージ アカウントの場所を選択するか、または既定の場所を使います。
7. **[パフォーマンス]** では **[Premium]** を選択します。

    **[アカウントの種類]** ドロップダウンにオプションとして **[FileStorage]** を表示するには、 **[Premium]** を選択する必要があります。

8. **[アカウントの種類]** を選択し、 **[FileStorage]** を選択します。
9. **[レプリケーション]** の設定は、既定値の **[ローカル冗長ストレージ (LRS)]** のままにします。

    ![Premium ファイル共有にストレージ アカウントを作成する方法](media/storage-how-to-create-premium-fileshare/create-filestorage-account.png)

10. **[確認および作成]** を選択して、ストレージ アカウントの設定を確認し、アカウントを作成します。
11. **［作成］** を選択します

ストレージ アカウント リソースが作成されたら、そこに移動します。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
FileStorage ストレージ アカウントを作成するには、PowerShell プロンプトを開き、次のコマンドを実行します。`<resource-group>` と `<storage-account>` を、実際の環境に合った適切な値に置き換えることを忘れないでください。

```powershell
$resourceGroupName = "<resource-group>"
$storageAccountName = "<storage-account>"
$location = "westus2"

$storageAccount = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Premium_LRS `
    -Location $location `
    -Kind FileStorage
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
FileStorage ストレージ アカウントを作成するには、お使いのターミナルを開き、次のコマンドを実行します。`<resource-group>` と `<storage-account>` を、実際の環境に合った適切な値に置き換えることを忘れないでください。

```azurecli-interactive
resourceGroup="<resource-group>"
storageAccount="<storage-account>"
location="westus2"

az storage account create \
    --resource-group $resourceGroup \
    --name $storageAccount \
    --location $location \
    --sku Premium_LRS \
    --kind FileStorage
```
---

## <a name="create-an-nfs-share"></a>NFS 共有を作成する

# <a name="portal"></a>[ポータル](#tab/azure-portal)

これで FileStorage アカウントが作成され、ネットワークが構成されたので、NFS ファイル共有を作成できます。 このプロセスは SMB 共有の作成に似ていますが、共有を作成するときに **[SMB]** ではなく **[NFS]** を選択します。

1. ストレージ アカウントに移動し、 **[ファイル共有]** を選択します。
1. **[+ ファイル共有]** を選択して、新しいファイル共有を作成します。
1. ファイル共有に名前を付け、プロビジョニングされた容量を選択します。
1. **[プロトコル]** で **[NFS (プレビュー)]** を選択します。
1. **[ルート スカッシュ]** で選択を行います。

    - [ルート スカッシュ] (既定値) - リモート スーパーユーザー (ルート) へのアクセスが UID (65534) と GID (65534) にマップされます。
    - [ルート スカッシュなし] - リモート スーパーユーザー (ルート) はルートとしてアクセスを受信します。
    - [すべてスカッシュ] - すべてのユーザー アクセスが UID (65534) と GID (65534) にマップされます。
    
1. **［作成］** を選択します

    :::image type="content" source="media/storage-files-how-to-create-mount-nfs-shares/create-nfs-file-share.png" alt-text="ファイル共有の作成ブレードのスクリーンショット":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. .NET Framework がインストールされていることを確認します。 「[.NET Framework のダウンロード](https://dotnet.microsoft.com/download/dotnet-framework)」を参照してください。
 
1. 次のコマンドを使用して、インストールされている PowerShell のバージョンが `5.1` 以降であることを確認します。    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```
    
   お使いの PowerShell のバージョンをアップグレードするには、「[既存の Windows PowerShell をアップグレードする](/powershell/scripting/install/installing-windows-powershell#upgrading-existing-windows-powershell)」を参照してください。
    
1. PowerShellGet モジュールの最新バージョンをインストールします。

   ```powershell
   install-Module PowerShellGet –Repository PSGallery –Force  
   ```

1. PowerShell コンソールを閉じてから、再度開きます。

1. **Az.Storage** プレビュー モジュール バージョン **2.5.2-preview** をインストールします。

   ```powershell
   Install-Module Az.Storage -Repository PsGallery -RequiredVersion 2.5.2-preview -AllowClobber -AllowPrerelease -Force  
   ```

   PowerShell モジュールのインストール方法の詳細については、「[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)」を参照してください。
   
1. Azure PowerShell モジュールを使用して Premium ファイル共有を作成するには、[New-AzRmStorageShare](/powershell/module/az.storage/new-azrmstorageshare) コマンドレットを使用します。

    > [!NOTE]
    > Premium ファイル共有は、プロビジョニング モデルを使用して課金されます。 共有のプロビジョニングされたサイズは、次の `QuotaGiB` で指定します。 詳細については、[プロビジョニング モデル](understanding-billing.md#provisioned-model)に関するページと [Azure Files の料金ページ](https://azure.microsoft.com/pricing/details/storage/files/)を参照してください。

    ```powershell
    New-AzRmStorageShare `
        -StorageAccount $storageAccount `
        -Name myshare `
        -EnabledProtocol NFS `
        -RootSquash RootSquash `
        -QuotaGiB 1024
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Azure CLI で Premium ファイル共有を作成するには、[az storage share create](/cli/azure/storage/share-rm) コマンドを使用します。

> [!NOTE]
> Premium ファイル共有は、プロビジョニング モデルを使用して課金されます。 共有のプロビジョニングされたサイズは、次の `quota` で指定します。 詳細については、[プロビジョニング モデル](understanding-billing.md#provisioned-model)に関するページと [Azure Files の料金ページ](https://azure.microsoft.com/pricing/details/storage/files/)を参照してください。

```azurecli-interactive
az storage share-rm create \
    --resource-group $resourceGroup \
    --storage-account $storageAccount \
    --name "myshare" \
    --enabled-protocol NFS \
    --root-squash RootSquash \
    --quota 1024
```
---

## <a name="next-steps"></a>次のステップ
これで NFS 共有が作成されたので、それを使用するには Linux クライアントにマウントする必要があります。 詳細については、[NFS 共有をマウントする方法](storage-files-how-to-mount-nfs-shares.md)に関するページを参照してください。

何か問題が発生した場合は、[Azure NFS ファイル共有のトラブルシューティング](storage-troubleshooting-files-nfs.md)に関するページを参照してください。