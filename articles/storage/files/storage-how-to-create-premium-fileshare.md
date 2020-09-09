---
title: Premium Azure ファイル共有を作成する
description: Azure portal、Azure PowerShell モジュール、または Azure CLI を使用して Azure Premium ファイル共有を作成する方法について説明します。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 08/26/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 7c178f0bb54cb815b25259c819d15d10b3671c79
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2020
ms.locfileid: "89070940"
---
# <a name="how-to-create-an-azure-premium-file-share"></a>Azure Premium ファイル共有を作成する方法

Premium ファイル共有は、ソリッド ステート ディスク (SSD) ストレージ メディアで提供され、データベースのホスティングやハイパフォーマンス コンピューティング (HPC) など、IO 集中型のワークロードに役立ちます。 Premium ファイル共有は、FileStorage アカウントと呼ばれる、特殊用途のストレージ アカウントの種類でホストされます。 Premium ファイル共有は、ハイ パフォーマンスおよびエンタープライズ規模のアプリケーション向けに設計され、一貫性のある待機時間の短縮、高 IOPS、高スループット共有を提供します。

この記事では、[Azure portal](https://portal.azure.com/)、Azure PowerShell モジュール、および Azure CLI を使用して、この新しいアカウントの種類を作成する方法を示します。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。
- Azure CLI を使用する場合は、[最新バージョンをインストールしてください](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。
- Azure PowerShell モジュールを使用する場合は、[最新バージョンをインストール](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-4.6.0)してください。

## <a name="create-a-filestorage-storage-account"></a>FileStorage ストレージ アカウントを作成する

すべてのストレージ アカウントは、Azure リソース グループに属している必要があります。 リソース グループは、Azure サービスをグループ化するための論理コンテナーです。 ストレージ アカウントを作成するときに、新しいリソース グループを作成するか、既存のリソース グループを使用するかを選択できます。 Premium ファイル共有には FileStorage アカウントが必要です。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

### <a name="sign-in-to-azure"></a>Azure へのサインイン

[Azure portal](https://portal.azure.com/) にサインインします。

ここまでで、ストレージ アカウントを作成する準備ができました。

1. Azure portal の左側のメニューで **[ストレージ アカウント]** を選択します。

    ![Azure portal のメイン ページでストレージ アカウントを選択する](media/storage-how-to-create-premium-fileshare/azure-portal-storage-accounts.png)

1. 表示された **[ストレージ アカウント]** ウィンドウで **[追加]** を選択します。
1. ストレージ アカウントを作成するサブスクリプションを選択します。
1. **[リソース グループ]** フィールドの下の **[新規作成]** を選択します。 次の図のように、新しいリソース グループの名前を入力します。

1. 次に、ストレージ アカウントの名前を入力します。 選択する名前は Azure 全体で一意である必要があります。 また、名前の長さは 3 から 24 文字とし、数字と小文字のみを使用できます。
1. ストレージ アカウントの場所を選択するか、または既定の場所を使います。
1. **[パフォーマンス]** では **[Premium]** を選択します。

    **[アカウントの種類]** ドロップダウンにオプションとして **[FileStorage]** を表示するには、 **[Premium]** を選択する必要があります。

1. **[アカウントの種類]** を選択し、 **[FileStorage]** を選択します。
1. **[レプリケーション]** の設定は、既定値の **[ローカル冗長ストレージ (LRS)]** のままにします。

    ![Premium ファイル共有にストレージ アカウントを作成する方法](media/storage-how-to-create-premium-fileshare/create-filestorage-account.png)

1. **[確認および作成]** を選択して、ストレージ アカウントの設定を確認し、アカウントを作成します。
1. **［作成］** を選択します

ストレージ アカウント リソースが作成されたら、そこに移動します。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

### <a name="sign-in-to-azure"></a>Azure へのサインイン

`Connect-AzAccount` コマンドを使用して、画面上の指示に従って認証を行います。

```powershell
Connect-AzAccount
```

### <a name="create-a-resource-group"></a>リソース グループを作成する

PowerShell で新しいリソース グループを作成するには、[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) コマンドを使用します。

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hardcoding it repeatedly
$resourceGroup = "storage-how-to-resource-group"
$location = "westus2"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

### <a name="create-a-filestorage-storage-account"></a>FileStorage ストレージ アカウントを作成する

PowerShell を使用して FileStorage ストレージ アカウントを作成するには、[New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount) コマンドを使用します。

```powershell
$storageAcct = New-AzStorageAccount -ResourceGroupName $resourceGroup -Name "fileshowto" -SkuName "Premium_LRS" -Location "westus2" -Kind "FileStorage"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure Cloud Shell を開始するには、[Azure portal](https://portal.azure.com) にサインインします。

CLI のローカル インストールにログインする場合は、最新バージョンを使用していることを確認してから、サインインします。

```azurecli
az login
```

### <a name="create-a-resource-group"></a>リソース グループを作成する

Azure CLI で新しいリソース グループを作成するには、[az group create](/cli/azure/group) コマンドを使用します。

```azurecli-interactive
az group create `
    --name files-howto-resource-group `
    --location westus2
```

### <a name="create-a-filestorage-storage-account"></a>FileStorage ストレージ アカウントを作成する

FileStorage ストレージ アカウントを Azure CLI から作成するには、[az storage account create](/cli/azure/storage/account) コマンドを使用します。

```azurecli-interactive
az storage account create `
    --name fileshowto `
    --resource-group files-howto-resource-group `
    --location westus `
    --sku Premium_LRS `
    --kind FileStorage
```

### <a name="get-the-storage-account-key"></a>ストレージ アカウント キーを取得する

ストレージ アカウント キーにより、ストレージ アカウント内のリソースへのアクセスが制御されます。この記事では、Premium ファイル共有を作成するためにキーを使います。 キーは、ストレージ アカウントの作成時に自動的に作成されます。 ストレージ アカウントのストレージ アカウント キーは、[az storage account keys list](/cli/azure/storage/account/keys) コマンドを使用して取得できます。

```azurecli-interactive
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```
---

## <a name="create-a-premium-file-share"></a>Premium ファイル共有を作成する

FileStorage アカウントが作成されたので、そのストレージ アカウント内に Premium ファイル共有を作成できます。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

1. ストレージ アカウントの左側のメニューで、 **[File サービス]** セクションまでスクロールし、 **[Files]** を選択します。
1. **[ファイル共有]** を選択して、Premium ファイル共有を作成します。
1. ファイル共有の名前と必要なクォータを入力し、 **[作成]** を選択します。

> [!NOTE]
> プロビジョニングされる共有サイズは共有クォータによって指定され、ファイル共有はプロビジョニングされたサイズで課金されます。 詳細については、 [価格に関するページ](https://azure.microsoft.com/pricing/details/storage/files/)を参照してください。

   ![Premium ファイル共有を作成する](media/storage-how-to-create-premium-fileshare/create-premium-file-share.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Azure PowerShell モジュールで Premium ファイル共有を作成するには、[New-AzStorageShare](/powershell/module/az.storage/New-AzStorageShare) コマンドレットを使用します。

> [!NOTE]
> プロビジョニングされる共有サイズは共有クォータによって指定され、ファイル共有はプロビジョニングされたサイズで課金されます。 詳細については、 [価格に関するページ](https://azure.microsoft.com/pricing/details/storage/files/)を参照してください。

```powershell
New-AzStorageShare `
   -Name myshare `
   -Context $storageAcct.Context
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI で Premium ファイル共有を作成するには、[az storage share create](/cli/azure/storage/share) コマンドを使用します。

> [!NOTE]
> プロビジョニングされる共有サイズは共有クォータによって指定され、ファイル共有はプロビジョニングされたサイズで課金されます。 詳細については、 [価格に関するページ](https://azure.microsoft.com/pricing/details/storage/files/)を参照してください。

```azurecli-interactive
az storage share create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" 
```
---

## <a name="clean-up-resources"></a>リソースをクリーンアップする

# <a name="portal"></a>[ポータル](#tab/azure-portal)

この記事で作成したリソースをクリーンアップしたい場合は、リソース グループを削除します。 リソース グループを削除すると、関連付けられているストレージ アカウント、およびリソース グループに関連付けられているその他のリソースも削除されます。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

この記事で作成したリソースをクリーンアップしたい場合は、リソース グループを削除します。 リソース グループを削除すると、関連付けられているストレージ アカウント、およびリソース グループに関連付けられているその他のリソースも削除されます。

リソース グループとそれに関連付けられているリソース (新しいストレージ アカウントを含む) を削除するには、[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) コマンドを使用します。 

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

この記事で作成したリソースをクリーンアップしたい場合は、リソース グループを削除します。 リソース グループを削除すると、関連付けられているストレージ アカウント、およびリソース グループに関連付けられているその他のリソースも削除されます。

リソース グループと関連付けられているリソース (新しいストレージ アカウントを含む) を削除するには、[az group delete](/cli/azure/group) コマンドを使用します。

```azurecli-interactive
az group delete --name myResourceGroup
```
---

## <a name="next-steps"></a>次のステップ

この記事では、Premium ファイル共有を作成しました。 このアカウントで提供されるパフォーマンスの詳細については、続けて計画ガイドのパフォーマンス レベル セクションをご覧ください。

> [!div class="nextstepaction"]
> [ファイル共有レベル](storage-files-planning.md#storage-tiers)
