---
title: リソースの管理 - Azure PowerShell
description: Azure PowerShell と Azure Resource Manager を使用して、リソースを管理します。 リソースをデプロイおよび削除する方法を示します。
author: mumian
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: ce3b1d9f8d01e722e4ec5078d92ef4148072da25
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96185744"
---
# <a name="manage-azure-resources-by-using-azure-powershell"></a>Azure PowerShell を使用した Azure リソースの管理

[Azure Resource Manager](overview.md)と共に Azure PowerShell を使用して Azure リソースを管理する方法について説明します。 リソース グループの管理については、「[Manage Azure resource groups by using Azure PowerShell (Azure PowerShell を使用した Azure リソース グループの管理)](manage-resource-groups-powershell.md)」をご覧ください。

リソースの管理に関するその他の記事:

- 「[Manage Azure resources by using the Azure portal (Azure portal を使用した Azure リソースの管理)](manage-resources-portal.md)」
- 「[Azure CLI を使用した Azure リソースの管理](manage-resources-cli.md)」

## <a name="deploy-resources-to-an-existing-resource-group"></a>リソースを既存のリソース グループにデプロイする

Azure PowerShell を使用して直接 Azure リソースをデプロイするか、または Azure リソースを作成する Resource Manager テンプレートをデプロイできます。

### <a name="deploy-a-resource"></a>リソースのデプロイ

次のスクリプトでは、ストレージ アカウントを作成します。

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

# Create the storage account.
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroupName `
  -Name $storageAccountName `
  -Location $location `
  -SkuName "Standard_LRS"

# Retrieve the context.
$ctx = $storageAccount.Context
```

### <a name="deploy-a-template"></a>テンプレートのデプロイ

次のスクリプトでは、ストレージ アカウントを作成するためのクイックスタート テンプレートをデプロイします。 詳細については、「[クイック スタート: Visual Studio Code を使って Azure Resource Manager テンプレートを作成する](../templates/quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell)」をご覧ください。

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -Location $location
```

詳細については、「[Deploy resources with Resource Manager templates and Azure PowerShell](../templates/deploy-powershell.md)」 (Resource Manager テンプレートと Azure PowerShell を使用したリソースのデプロイ) を参照してください。

## <a name="deploy-a-resource-group-and-resources"></a>リソース グループとリソースをデプロイする

リソース グループを作成して、リソースをそのグループにデプロイすることができます。 詳しくは、「[リソース グループを作成してリソースをデプロイする](../templates/deploy-to-subscription.md#resource-groups)」をご覧ください。

## <a name="deploy-resources-to-multiple-subscriptions-or-resource-groups"></a>複数のサブスクリプションまたはリソース グループにリソースをデプロイする

テンプレートに含まれているリソースはすべて 1 つのリソース グループにデプロイするのが一般的です。 一方、さまざまなリソースを 1 つにまとめたうえで、複数のリソース グループまたはサブスクリプションにデプロイしたい状況もあります。 詳しくは、「[複数のサブスクリプションまたはリソース グループに Azure リソースをデプロイする](../templates/deploy-to-resource-group.md)」をご覧ください。

## <a name="delete-resources"></a>リソースを削除する

次のスクリプトは、ストレージ アカウントを削除する方法を示しています。

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

Remove-AzStorageAccount -ResourceGroupName $resourceGroupName -AccountName $storageAccountName
```

Azure Resource Manager によってリソースの削除の順序が決定される方法の詳細については、「[Azure Resource Manager によるリソース グループの削除](delete-resource-group.md)」を参照してください。

## <a name="move-resources"></a>リソースの移動

次のスクリプトは、1 つのリソース グループからストレージ アカウントを削除して別のリソース グループに移動する方法を示しています。

```azurepowershell-interactive
$srcResourceGroupName = Read-Host -Prompt "Enter the source Resource Group name"
$destResourceGroupName = Read-Host -Prompt "Enter the destination Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

$storageAccount = Get-AzResource -ResourceGroupName $srcResourceGroupName -ResourceName $storageAccountName
Move-AzResource -DestinationResourceGroupName $destResourceGroupName -ResourceId $storageAccount.ResourceId
```

詳細については、「 [新しいリソース グループまたはサブスクリプションへのリソースの移動](move-resource-group-and-subscription.md)」を参照してください。

## <a name="lock-resources"></a>リソースのロック

ロックすることで、組織内の他のユーザーが重要なリソース (Azure サブスクリプション、リソース グループ、リソースなど) を誤って削除または変更することを防ぎます。 

次のスクリプトでは、アカウントを削除できないように、ストレージ アカウントをロックします。

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

New-AzResourceLock -LockName LockStorage -LockLevel CanNotDelete -ResourceGroupName $resourceGroupName -ResourceName $storageAccountName -ResourceType Microsoft.Storage/storageAccounts 
```

次のスクリプトでは、ストレージ アカウントのすべてのロックを取得します。

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

Get-AzResourceLock -ResourceGroupName $resourceGroupName -ResourceName $storageAccountName -ResourceType Microsoft.Storage/storageAccounts
```

次のスクリプトでは、ストレージ アカウントのロックを削除します。

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

$lockId = (Get-AzResourceLock -ResourceGroupName $resourceGroupName -ResourceName $storageAccountName -ResourceType Microsoft.Storage/storageAccounts).LockId
Remove-AzResourceLock -LockId $lockId
```

詳細については、[「Azure Resource Manager によるリソースのロック」](lock-resources.md)を参照してください。

## <a name="tag-resources"></a>リソースへのタグ付け

タグ付けすると、リソース グループとリソースを論理的に整理できます。 詳細については、[タグを使用した Azure リソースの整理](tag-resources.md#powershell) に関する記事をご覧ください。

## <a name="manage-access-to-resources"></a>リソースへのアクセスの管理

[Azure ロールベースのアクセス制御 (Azure RBAC)](../../role-based-access-control/overview.md) は、Azure のリソースへのアクセスを管理する方法です。 詳細については、「[Azure PowerShell を使用して Azure ロールの割り当てを追加または削除する](../../role-based-access-control/role-assignments-powershell.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

- Azure Resource Manager については、「[Azure Resource Manager の概要](overview.md)」を参照してください。
- Resource Manager テンプレートの構文については、「[Azure Resource Manager テンプレートの構造と構文の詳細](../templates/template-syntax.md)」を参照してください。
- テンプレートを開発する方法については、[ステップバイステップのチュートリアル](../index.yml)のページをご覧ください。
- Azure Resource Manager テンプレートのスキーマを表示するには、[テンプレート リファレンス](/azure/templates/)のページをご覧ください。