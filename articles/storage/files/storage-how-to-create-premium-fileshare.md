---
title: Premium Azure ファイル共有を作成する
description: この記事では、Premium Azure ファイル共有を作成する方法について説明します。
services: storage
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 05/05/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 63caf9a08acb04bab3712891701d32c21c22e9fc
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449884"
---
# <a name="how-to-create-an-premium-azure-file-share"></a>Premium Azure ファイル共有を作成する方法
Premium ファイル共有は、ソリッド ステート ディスク (SSD) ストレージ メディアで提供され、データベースのホスティングやハイパフォーマンス コンピューティング (HPC) など、IO 集中型のワークロードに役立ちます。 Premium ファイル共有は、FileStorage アカウントと呼ばれる、特殊用途のストレージ アカウントの種類でホストされます。 Premium ファイル共有は、ハイ パフォーマンスおよびエンタープライズ規模のアプリケーション向けに設計され、一貫性のある待機時間の短縮、高 IOPS、高スループット共有を提供します。

この記事では、[Azure portal](https://portal.azure.com/)、Azure PowerShell、および Azure CLI を使用して、この新しいアカウントの種類を作成する方法を示します。

## <a name="prerequisites"></a>前提条件

Premium Azure ファイル共有などの Azure リソースにアクセスするには、Azure サブスクリプションが必要です。 サブスクリプションをお持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="create-a-premium-file-share-using-the-azure-portal"></a>Azure portal を使用して Premium ファイル共有を作成する

### <a name="sign-in-to-azure"></a>Azure へのサインイン

[Azure Portal](https://portal.azure.com/) にサインインします。

### <a name="create-a-filestorage-storage-account"></a>FileStorage ストレージ アカウントを作成する

ここまでで、ストレージ アカウントを作成する準備ができました。

すべてのストレージ アカウントは、Azure リソース グループに属している必要があります。 リソース グループは、Azure サービスをグループ化するための論理コンテナーです。 ストレージ アカウントを作成するときに、新しいリソース グループを作成するか、既存のリソース グループを使用するかを選択できます。 この記事では、新しいリソース グループを作成する方法を示します。

1. Azure portal の左側のメニューで **[ストレージ アカウント]** を選択します。

    ![Azure portal のメイン ページでストレージ アカウントを選択する](media/storage-how-to-create-premium-fileshare/azure-portal-storage-accounts.png)

1. 表示された **[ストレージ アカウント]** ウィンドウで **[追加]** を選択します。
1. ストレージ アカウントを作成するサブスクリプションを選択します。
1. **[リソース グループ]** フィールドの下の **[新規作成]** を選択します。 次の図のように、新しいリソース グループの名前を入力します。

1. 次に、ストレージ アカウントの名前を入力します。 選択する名前は Azure 全体で一意である必要があります。 また、名前の長さは 3 から 24 文字とし、数字と小文字のみを使用できます。
1. ストレージ アカウントの場所を選択するか、または既定の場所を使います。
1. **[パフォーマンス]** では **[Premium]** を選択します。
1. **[アカウントの種類]** を選択し、 **[FileStorage]** を選択します。
1. **[レプリケーション]** の設定は、既定値の **[ローカル冗長ストレージ (LRS)]** のままにします。

    ![Premium ファイル共有にストレージ アカウントを作成する方法](media/storage-how-to-create-premium-fileshare/create-filestorage-account.png)

1. **[確認および作成]** を選択して、ストレージ アカウントの設定を確認し、アカウントを作成します。
1. **作成** を選択します。

ストレージ アカウント リソースが作成されたら、そこに移動します。

### <a name="create-a-premium-file-share"></a>Premium ファイル共有を作成する

1. ストレージ アカウントの左側のメニューで、 **[File サービス]** セクションまでスクロールし、 **[Files]** を選択します。
1. **[+ ファイル共有]** を選択して、Premium ファイル共有を作成します。
1. ファイル共有の名前と必要なクォータを入力し、 **[作成]** を選択します。

> [!NOTE]
> プロビジョニングされる共有サイズは共有クォータによって指定され、ファイル共有はプロビジョニングされたサイズで課金されます。詳しくは、[価格のページ](https://azure.microsoft.com/pricing/details/storage/files/)をご覧ください。

   ![Premium ファイル共有を作成する](media/storage-how-to-create-premium-fileshare/create-premium-file-share.png)

### <a name="clean-up-resources"></a>リソースのクリーンアップ

この記事で作成したリソースをクリーンアップしたい場合は、リソース グループを削除するだけです。 リソース グループを削除すると、関連付けられているストレージ アカウント、およびリソース グループに関連付けられているその他のリソースも削除されます。

## <a name="create-a-premium-file-share-using-powershell"></a>PowerShell を使用して Premium ファイル共有を作成する

### <a name="create-an-account-using-powershell"></a>PowerShell を使用してアカウントを作成する

最初に、[PowerShellGet](https://docs.microsoft.com/powershell/gallery/installing-psget) モジュールの最新バージョンをインストールします。

次に、PowerShell モジュールをアップグレードして、お使いの Azure サブスクリプションにサインインし、リソース グループを作成してから、ストレージ アカウントを作成します。

### <a name="upgrade-your-powershell-module"></a>PowerShell モジュールのアップグレード

PowerShell で Premium ファイル共有を操作するには、Az.Storage モジュール バージョン 1.4.0 (最新の Az.Storage モジュール) をインストールする必要があります。

最初に、引き上げられたアクセス許可を使って PowerShell セッションを開きます。

Az.Storage モジュールをインストールします。

```powershell
Install-Module Az.Storage -Repository PSGallery -AllowClobber -Force
```

### <a name="sign-in-to-your-azure-subscription"></a>Azure サブスクリプションにサインインする

`Connect-AzAccount` コマンドを使用して、画面上の指示に従って認証を行います。

```powershell
Connect-AzAccount
```

### <a name="create-a-resource-group"></a>リソース グループの作成

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

### <a name="create-a-premium-file-share"></a>Premium ファイル共有を作成する

FileStorage アカウントができたので、Premium ファイル共有を作成できます。 作成するには、[New-AzStorageShare](/powershell/module/az.storage/New-AzStorageShare) コマンドレットを使います。

> [!NOTE]
> プロビジョニングされる共有サイズは共有クォータによって指定され、ファイル共有はプロビジョニングされたサイズで課金されます。詳しくは、[価格のページ](https://azure.microsoft.com/pricing/details/storage/files/)をご覧ください。

```powershell
New-AzStorageShare `
   -Name myshare `
   -Context $storageAcct.Context
```

### <a name="clean-up-resources"></a>リソースのクリーンアップ

リソース グループとそれに関連付けられているリソース (新しいストレージ アカウントを含む) を削除するには、[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) コマンドを使用します。 

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="create-a-premium-file-share-using-azure-cli"></a>Azure CLI を使用して Premium ファイル共有を作成する

Azure Cloud Shell を開始するには、[Azure portal](https://portal.azure.com) にサインインします。

CLI のローカル インストールにログインする場合は、まず最新バージョンを使用していることを確認してから、ログイン コマンドを実行します。

```cli
az login
```

### <a name="create-a-resource-group"></a>リソース グループの作成

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

### <a name="create-a-premium-file-share"></a>Premium ファイル共有を作成する

FileStorage アカウントができたので、Premium ファイル共有を作成できます。 作成するには、[az storage share create](/cli/azure/storage/share) コマンドを使います。

> [!NOTE]
> プロビジョニングされる共有サイズは共有クォータによって指定され、ファイル共有はプロビジョニングされたサイズで課金されます。詳しくは、[価格のページ](https://azure.microsoft.com/pricing/details/storage/files/)をご覧ください。

```azurecli-interactive
az storage share create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" 
```

### <a name="clean-up-resources"></a>リソースのクリーンアップ

リソース グループと関連付けられているリソース (新しいストレージ アカウントを含む) を削除するには、[az group delete](/cli/azure/group) コマンドを使用します。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>次の手順

この記事では、Premium ファイル共有を作成しました。 このアカウントで提供されるパフォーマンスの詳細については、続けて計画ガイドのパフォーマンス レベル セクションをご覧ください。

> [!div class="nextstepaction"]
> [ファイル共有のパフォーマンス レベル](storage-files-planning.md#file-share-performance-tiers)