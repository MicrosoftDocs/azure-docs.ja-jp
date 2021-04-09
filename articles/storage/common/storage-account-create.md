---
title: ストレージ アカウントの作成
titleSuffix: Azure Storage
description: BLOB、ファイル、キュー、テーブルを格納するためのストレージ アカウントの作成について説明します。 Azure ストレージ アカウントには、データの読み取りと書き込みを行うための Microsoft Azure の一意の名前空間が用意されています。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/11/2021
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: b8f5932985f90ce042d7b0df0d01e7c685098670
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104576547"
---
# <a name="create-a-storage-account"></a>ストレージ アカウントを作成する

Azure ストレージ アカウントには、すべての Azure Storage データ オブジェクト (BLOB、ファイル、キュー、テーブル、およびディスク) が含まれます。 ストレージ アカウントでは、世界中のどこからでも HTTP または HTTPS 経由でアクセスできる Azure Storage データ用の一意の名前空間が提供されます。 Azure ストレージ アカウント内のデータは、持続性があり、高可用性で、セキュリティ保護されており、非常にスケーラブルです。

このハウツー記事では、[Azure portal](https://portal.azure.com/)、[Azure PowerShell](/powershell/azure/)、[Azure CLI](/cli/azure)、または [Azure Resource Manager テンプレート](../../azure-resource-manager/management/overview.md)を使ってストレージ アカウントを作成する方法を説明します。  

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

[なし] :

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell で Azure Storage アカウントを作成するには、Azure PowerShell モジュール Az バージョン 0.7 以降をインストールしておく必要があります。 詳細については、[Azure PowerShell Az モジュールの概要](/powershell/azure/new-azureps-module-az)に関するページを参照してください。

現在のバージョンを調べるには、次のコマンドを実行します。

```powershell
Get-InstalledModule -Name "Az"
```

Azure PowerShell をインストールまたはアップグレードする場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-Az-ps)に関するページを参照してください。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

次の 2 つの方法のいずれかで Azure にサインインし、Azure CLI コマンドを実行できます。

- Azure portal 内から、Azure Cloud Shell で CLI コマンドを実行できます。
- CLI をインストールして、CLI コマンドをローカルで実行できます。

### <a name="use-azure-cloud-shell"></a>Azure Cloud Shell を使用する

Azure Cloud Shell は、Azure Portal 内で直接実行できる無料の Bash シェルです。 Azure CLI は、アカウントで使用できるように事前にインストールおよび構成されています。 Azure portal の右上のセクションのメニューで **[Cloud Shell]** ボタンをクリックします。

[![Cloud Shell](./media/storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

このボタンによって対話型のシェルが起動されます。このハウツー記事で説明する手順は、これを使って実行できます。

[![ポータルに Cloud Shell のウィンドウが表示されるスクリーンショット](./media/storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>CLI をローカルにインストールする

Azure CLI はローカルにインストールして使用することもできます。 この記事の例には、Azure CLI バージョン 2.0.4 以降が必要です。 `az --version` を実行して、インストールされているバージョンを見つけます。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

# <a name="template"></a>[テンプレート](#tab/template)

[なし] :

---

## <a name="sign-in-to-azure"></a>Azure へのサインイン

# <a name="portal"></a>[ポータル](#tab/azure-portal)

[Azure portal](https://portal.azure.com) にサインインします。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

`Connect-AzAccount` コマンドで Azure サブスクリプションにサインインし、画面上の指示に従って認証を行います。

```powershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure Cloud Shell を起動するには、[Azure portal](https://portal.azure.com) にサインインします。

CLI のローカル インストールにログインするには、[az login](/cli/azure/reference-index#az-login) コマンドを実行します。

```azurecli-interactive
az login
```

# <a name="template"></a>[テンプレート](#tab/template)

該当なし

---

## <a name="create-a-storage-account"></a>ストレージ アカウントの作成

すべてのストレージ アカウントは、Azure リソース グループに属している必要があります。 リソース グループは、Azure サービスをグループ化するための論理コンテナーです。 ストレージ アカウントを作成するときに、新しいリソース グループを作成するか、既存のリソース グループを使用するかを選択できます。 この記事では、新しいリソース グループを作成する方法を示します。

**汎用 v2** ストレージ アカウントでは、すべての Azure Storage サービス (BLOB、ファイル、キュー、テーブル、ディスク) へのアクセスが提供されます。 ここで説明する手順では汎用 v2 ストレージ アカウントを作成しますが、作成手順はどの種類のストレージ アカウントでも似ています。 ストレージ アカウントの種類およびその他のストレージ アカウントの設定について詳しくは、「[Azure ストレージ アカウントの概要](storage-account-overview.md)」をご覧ください。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

まず、PowerShell で [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) コマンドを使用して新しいリソース グループを作成します。

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hard-coding it repeatedly
$resourceGroup = "storage-resource-group"
$location = "westus"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

`-Location` パラメーターに指定するリージョンがわからない場合は、[Get-AzLocation](/powershell/module/az.resources/get-azlocation) コマンドを使用して、サブスクリプションに対してサポートされているリージョンの一覧を取得できます。

```powershell
Get-AzLocation | select Location
```

次に、[New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount) コマンドを使って、読み取りアクセス geo 冗長ストレージ (RA-GRS) で汎用 v2 ストレージ アカウントを作成します。 ストレージ アカウントの名前は Azure 全体で一意である必要があるため、角かっこ内のプレースホルダーの値を独自の一意の値に置き換えることを忘れないでください。

```powershell
New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name <account-name> `
  -Location $location `
  -SkuName Standard_RAGRS `
  -Kind StorageV2
```

> [!IMPORTANT]
> [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/) を使用する予定がある場合は、このパラメーターの一覧に `-EnableHierarchicalNamespace $True` を含めてください。

別のレプリケーション オプションで汎用 v2 ストレージ アカウントを作成するには、**SkuName** パラメーターを次の表の目的の値で置き換えます。

|レプリケーション オプション  |SkuName パラメーター  |
|---------|---------|
|ローカル冗長ストレージ (LRS)     |Standard_LRS         |
|ゾーン冗長ストレージ (ZRS)     |Standard_ZRS         |
|geo 冗長ストレージ (GRS)     |Standard_GRS         |
|読み取りアクセス geo 冗長ストレージ (GRS)     |Standard_RAGRS         |
|geo ゾーン冗長ストレージ (GZRS)    |Standard_GZRS         |
|読み取りアクセス geo ゾーン冗長ストレージ (RA-GZRS)    |Standard_RAGZRS         |

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

まず、Azure CLI の [az group create](/cli/azure/group#az_group_create) コマンドを使用して新しいリソース グループを作成します。

```azurecli-interactive
az group create \
    --name storage-resource-group \
    --location westus
```

`--location` パラメーターに指定するリージョンがわからない場合は、[az account list-locations](/cli/azure/account#az_account_list) コマンドで、サブスクリプションに対してサポートされているリージョンの一覧を取得できます。

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

次に、[az storage account create](/cli/azure/storage/account#az_storage_account_create) コマンドを使って、読み取りアクセス geo 冗長ストレージで汎用 v2 ストレージ アカウントを作成します。 ストレージ アカウントの名前は Azure 全体で一意である必要があるため、角かっこ内のプレースホルダーの値を独自の一意の値に置き換えることを忘れないでください。

```azurecli-interactive
az storage account create \
    --name <account-name> \
    --resource-group storage-resource-group \
    --location westus \
    --sku Standard_RAGRS \
    --kind StorageV2
```

> [!IMPORTANT]
> [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/) を使用する予定がある場合は、このパラメーターの一覧に `--enable-hierarchical-namespace true` を含めてください。

別のレプリケーション オプションで汎用 v2 ストレージ アカウントを作成するには、**sku** パラメーターを次の表の目的の値で置き換えます。

|レプリケーション オプション  |sku パラメーター  |
|---------|---------|
|ローカル冗長ストレージ (LRS)     |Standard_LRS         |
|ゾーン冗長ストレージ (ZRS)     |Standard_ZRS         |
|geo 冗長ストレージ (GRS)     |Standard_GRS         |
|読み取りアクセス geo 冗長ストレージ (GRS)     |Standard_RAGRS         |
|geo ゾーン冗長ストレージ (GZRS)    |Standard_GZRS         |
|読み取りアクセス geo ゾーン冗長ストレージ (RA-GZRS)    |Standard_RAGZRS         |

# <a name="template"></a>[テンプレート](#tab/template)

Azure PowerShell または Azure CLI を使用して Resource Manager テンプレートをデプロイし、ストレージ アカウントを作成できます。 このハウツー記事で使うテンプレートは、[Azure Resource Manager クイックスタートのテンプレート](https://azure.microsoft.com/resources/templates/101-storage-account-create/)に関する記事からのものです。 スクリプトを実行するには、 **[使ってみる]** を選択して、Azure Cloud Shell を開きます。 スクリプトを貼り付けるには、シェルを右クリックし、 **[貼り付け]** を選択します。

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

> [!NOTE]
> このテンプレートは、例としてのみ機能します。 このテンプレートの一部として構成されていないストレージ アカウント設定は多数あります。 たとえば、[Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/) を使用する場合、`StorageAccountPropertiesCreateParameters` オブジェクトの `isHnsEnabledad` プロパティを `true` に設定して、このテンプレートを変更します。 

このテンプレートの変更方法または新しいテンプレートの作成方法については、以下を参照してください。

- [Azure Resource Manager のドキュメント](../../azure-resource-manager/index.yml)。
- [ストレージ アカウント テンプレート リファレンス](/azure/templates/microsoft.storage/allversions)。
- [その他のストレージ アカウント テンプレート サンプル](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Storage)。

---

## <a name="delete-a-storage-account"></a>ストレージ アカウントを削除する

ストレージ アカウントを削除すると、アカウント内のすべてのデータを含むアカウント全体が削除され、元に戻すことができなくなります。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

1. [Azure portal](https://portal.azure.com) でストレージ アカウントに移動します。
1. **[削除]** をクリックします。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

ストレージ アカウントを削除するには、[Remove-AzStorageAccount](/powershell/module/az.storage/remove-azstorageaccount) コマンドを使用します。

```powershell
Remove-AzStorageAccount -Name <storage-account> -ResourceGroupName <resource-group>
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

ストレージ アカウントを削除するには、[az storage account delete](/cli/azure/storage/account#az-storage-account-delete) コマンドを使用します。

```azurecli-interactive
az storage account delete --name <storage-account> --resource-group <resource-group>
```

# <a name="template"></a>[テンプレート](#tab/template)

ストレージ アカウントを削除するには、Azure PowerShell または Azure CLI を使用します。

```azurepowershell-interactive
$storageResourceGroupName = Read-Host -Prompt "Enter the resource group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"
Remove-AzStorageAccount -Name $storageAccountName -ResourceGroupName $storageResourceGroupName
```

```azurecli-interactive
echo "Enter the resource group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az storage account delete --name storageAccountName --resource-group resourceGroupName
```

---

また、リソース グループを削除して、ストレージ アカウントとそのリソース グループ内の他のリソースを削除することもできます。 リソース グループの削除の詳細については、[リソース グループの削除](../../azure-resource-manager/management/delete-resource-group.md)に関するページを参照してください。

> [!WARNING]
> 削除したストレージ アカウントを復元することも、削除前にアカウントに含まれていたコンテンツを取得することもできません。 アカウントを削除する前に、保存する必要のあるデータを必ずバックアップしてください。 これはアカウントのどのリソースにも当てはまります。BLOB、テーブル、キュー、またはファイルを削除すると、完全に削除されます。
>
> Azure 仮想マシンに関連付けられているストレージ アカウントを削除しようとすると、まだ使用しているストレージ アカウントに関するエラー メッセージが表示されることがあります。 このエラーのトラブルシューティングについては、[ストレージ アカウントを削除する際のエラーのトラブルシューティング](../../virtual-machines/troubleshooting/index.yml)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

- [ストレージ アカウントの概要](storage-account-overview.md)
- [汎用 v2 ストレージ アカウントにアップグレードする](storage-account-upgrade.md)
- [Azure ストレージ アカウントを別のリージョンに移動する](storage-account-move.md)
- [削除されたストレージ アカウントを復旧します](storage-account-recover.md)
