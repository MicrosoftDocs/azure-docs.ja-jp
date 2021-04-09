---
title: Azure Resource Manager:Azure SQL Managed Instance の作成
description: Azure Resource Manager テンプレートを使用して Azure SQL Managed Instance を作成する方法を説明します。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: subject-armqs
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 06/22/2020
ms.openlocfilehash: badeb850e8d0346347a994f053fb0f64fd01240a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91283345"
---
# <a name="quickstart-create-an-azure-sql-managed-instance-using-an-arm-template"></a>クイック スタート:ARM テンプレートを使用して Azure SQL Managed Instance を作成する

このクイック スタートでは、Azure Resource Manager テンプレート (ARM テンプレート) をデプロイして Azure SQL Managed Instance と VNet を作成する過程を中心に取り上げます。 [Azure SQL Managed Instance](sql-managed-instance-paas-overview.md) は、完全に管理されたインテリジェントでスケーラブルなクラウド データベースであり、SQL Server データベース エンジンとほぼ 100% の機能パリティを備えています。

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

環境が前提条件を満たしていて、ARM テンプレートの使用に慣れている場合は、 **[Azure へのデプロイ]** ボタンを選択します。 Azure portal でテンプレートが開きます。

[![Azure へのデプロイ](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sqlmi-new-vnet%2Fazuredeploy.json)

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="review-the-template"></a>テンプレートを確認する

このクイックスタートで使用されるテンプレートは [Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/)からのものです。

:::code language="json" source="~/quickstart-templates/101-sqlmi-new-vnet/azuredeploy.json":::

テンプレートでは、次のリソースが定義されています。

- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.Network/networkSecurityGroups)
- [**Microsoft.Network/routeTables**](/azure/templates/microsoft.Network/routeTables)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.Network/virtualNetworks)
- [**Microsoft.Sql/managedinstances**](/azure/templates/microsoft.sql/managedinstances)

その他のテンプレート サンプルについては、「[Azure クイック スタート テンプレート](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sql&pageNumber=1&sort=Popular)」を参照してください。

## <a name="deploy-the-template"></a>テンプレートのデプロイ

次の PowerShell コード ブロックから **[使ってみる]** を選択して Azure Cloud Shell を開きます。

> [!IMPORTANT]
> マネージト インスタンスのデプロイは、実行時間の長い操作です。 サブネットへの最初のインスタンスのデプロイは、通常、既存のマネージド インスタンスが含まれるサブネットへのデプロイよりもはるかに長い時間がかかります。 平均的なプロビジョニング時間については、[SQL Managed Instance の管理操作](sql-managed-instance-paas-overview.md#management-operations)に関するセクションを参照してください。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-sqlmi-new-vnet/azuredeploy.json"

$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

Read-Host -Prompt "Press [ENTER] to continue ..."
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
read -p "Enter a project name that is used for generating resource names:" projectName &&
read -p "Enter the location (i.e. centralus):" location &&
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-sqlmi-new-vnet/azuredeploy.json" &&
resourceGroupName="${projectName}rg" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri  $templateUri &&
echo "Press [ENTER] to continue ..." &&
read
```

---

## <a name="review-deployed-resources"></a>デプロイされているリソースを確認する

[Azure portal](https://portal.azure.com/#blade/HubsExtension/BrowseResourceGroups) にアクセスし、選択したリソース グループにマネージド インスタンスがあることを確認します。 マネージド インスタンスの作成には時間がかかる場合があるため、リソース グループの **[概要]** ページで **[デプロイ]** リンクを確認する必要がある場合があります。

- Azure 仮想マシンから SQL マネージド インスタンスに接続する方法を説明するクイック スタートについては、[Azure 仮想マシン接続の構成](connect-vm-instance-configure.md)に関するページを参照してください。
- ポイント対サイト接続を使用してオンプレミスのクライアント コンピューターから SQL マネージド インスタンスに接続する方法を説明するクイック スタートについては、[ポイント対サイト接続の構成](point-to-site-p2s-configure.md)に関するページを参照してください。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[次の手順](#next-steps)に進む場合はマネージド インスタンスを保持しますが、追加のチュートリアルを完了した後でマネージド インスタンスと関連リソースを削除してください。 マネージド インスタンスを削除した後は、[マネージド インスタンスの削除後のサブネットの削除](virtual-cluster-delete.md)に関するページを参照してください。


リソース グループを削除するには:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

---

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure SQL Managed Instance に接続するように Azure VM を構成する](connect-vm-instance-configure.md)
