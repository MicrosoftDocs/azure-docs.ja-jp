---
title: Azure Resource Manager と PowerShell を使用して Azure Resource Manager グループを管理する | Microsoft Docs
description: Azure PowerShell を使用して Azure Resource Manager グループを管理します。
services: azure-resource-manager
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 8ae86d8bc7914a7a9c41eee93bb16b2f774993b9
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58651786"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-powershell"></a>Azure PowerShell を使用して Azure Resource Manager リソース グループを管理する

[Azure Resource Manager](resource-group-overview.md) で Azure PowerShell を使用して Azure リソースグループを管理する方法について説明します。 Azure リソースの管理については、[Manage Azure resources by using Azure PowerShell (Azure PowerShell を使用して Azure リソースを管理する)](./manage-resources-powershell.md) を参照してください。

リソース グループの管理に関するその他のアーティクル:

- [Azure Portal を使用して Azure リソース グループを管理する](./manage-resources-portal.md)
- [Azure CLI を使用して Azure リソース グループを管理する](./manage-resources-cli.md)

## <a name="what-is-a-resource-group"></a>リソース グループとは何か

リソース グループは、Azure ソリューションの関連するリソースを保持するコンテナーです。 リソース グループには、ソリューションのすべてのリソースか、グループとして管理したいリソースのみを含めることができます。 組織のニーズに合わせてリソースをリソース グループに割り当てる方法を指定してください。 通常は、同じライフサイクルを共有するリソースを同じリソース グループに追加して、グループとして簡単にデプロイ、更新、および削除できるようにします。

リソース グループには、リソースについてのメタデータが格納されます。 そのため、リソース グループの場所を指定するとき、このメタデータが格納される場所を指定することになります。 コンプライアンス上の理由から、データは特定のリージョンに格納されるようにする必要があります。

リソース グループには、リソースについてのメタデータが格納されます。 リソース グループの場所を指定するときは、このメタデータが格納されている場所を指定します。

## <a name="create-resource-groups"></a>リソース グループを作成する

次の PowerShell スクリプトでは、リソース グループを作成し、そのリソース グループを表示します。

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location

Get-AzResourceGroup -Name $resourceGroupName
```

## <a name="list-resource-groups"></a>List resource groups

次の PowerShell スクリプトでは、サブスクリプションの下にリソース グループを一覧表示します。

```azurepowershell-interactive
Get-AzResourceGroup
```

1 つの リソース グループを取得するには:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Get-AzResourceGroup -Name $resourceGroupName
```

## <a name="delete-resource-groups"></a>リソース グループの削除

次の PowerShell スクリプトはリソース グループを削除します。

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Remove-AzResourceGroup -Name $resourceGroupName
```

Azure Resource Manager によってリソースの削除の順序が決定される方法の詳細については、「[Azure Resource Manager によるリソース グループの削除](./resource-group-delete.md)」をご覧ください。

## <a name="deploy-resources-to-an-existing-resource-group"></a>リソースを既存のリソース グループにデプロイする

「[Deploy resources to an existing resource group (リソースを既存のリソース グループにデプロイする)](./manage-resources-powershell.md#deploy-resources-to-an-existing-resource-group)」をご覧ください。

リソース グループのデプロイを検証するには、[Test-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/Az.Resources/Test-AzResourceGroupDeployment?view=azps-1.3.0) をご覧ください。

## <a name="deploy-a-resource-group-and-resources"></a>リソース グループとリソースをデプロイする

Resource Manager テンプレートを使用して、リソース グループを作成し、そのグループにリソースをデプロイできます。 詳しくは、「[リソース グループを作成してリソースをデプロイする](./deploy-to-subscription.md#create-resource-group-and-deploy-resources)」をご覧ください。

## <a name="redeploy-when-deployment-fails"></a>デプロイに失敗したときに再デプロイする

この機能は、"*エラー時のロールバック*" とも呼ばれます。 詳細については、「[デプロイに失敗したときに再デプロイする](./resource-group-template-deploy.md#redeploy-when-deployment-fails)」を参照してください。

## <a name="move-to-another-resource-group-or-subscription"></a>別のリソース グループまたはサブスクリプションに移動する

グループ内のリソースを別のリソース グループに移動できます。 詳細については、「 [新しいリソース グループまたはサブスクリプションへのリソースの移動](./resource-group-move-resources.md#move-resources)」を参照してください。

## <a name="lock-resource-groups"></a>リソース グループをロックする

ロックを適用することで、組織の他のユーザーが重要なリソース (Azure サブスクリプション、リソース グループ、リソースなど) を誤って削除または変更するのを防ぐことができます。 

次のスクリプトでは、リソース グループをロックして、そのリソース グループを削除できないようにします。

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName $resourceGroupName 
```

次のスクリプトでは、リソース グループのすべてのロックを取得します。

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Get-AzResourceLock -ResourceGroupName $resourceGroupName 
```

詳細については、[「Azure Resource Manager によるリソースのロック」](resource-group-lock-resources.md)を参照してください。

## <a name="tag-resource-groups"></a>リソース グループにタグを適用する

リソース グループやリソースにタグを適用して、アセットを論理的に整理できます。 詳細については、[タグを使用した Azure リソースの整理](./resource-group-using-tags.md#powershell) に関する記事をご覧ください。

## <a name="export-resource-groups-to-templates"></a>リソース グループをテンプレートにエクスポートする

リソース グループを正常に設定したら、リソース グループの Resource Manager テンプレートを表示できます。 テンプレートのエクスポートには、2 つの利点があります。

- テンプレートには完全なインフラストラクチャが含まれているため、ソリューションの将来のデプロイを自動化できます。
- ソリューションを表す JavaScript Object Notation (JSON) を調べることで、テンプレートの構文を確認できます。

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Export-AzResourceGroup -ResourceGroupName $resourceGroupName
```

詳細については、[リソース グループのエクスポート](./manage-resource-groups-portal.md#export-resource-groups-to-templates) に関するセクションをご覧ください。

## <a name="manage-access-to-resource-groups"></a>リソース グループへのアクセスを管理する

[ロールベースのアクセス制御 (RBAC)](../role-based-access-control/overview.md) は、Azure に存在するリソースに対するアクセス権を管理するための手法です。 詳細については、[RBAC と Azure PowerShell を使用したアクセスの管理](../role-based-access-control/role-assignments-powershell.md)に関する記事をご覧ください。

## <a name="next-steps"></a>次の手順

- Azure Resource Manager については、「[Azure Resource Manager の概要](./resource-group-overview.md)」をご覧ください。
- Resource Manager テンプレートの構文については、「[Azure Resource Manager テンプレートの構造と構文の詳細](./resource-group-authoring-templates.md)」をご覧ください。
- テンプレートを開発する方法については、[ステップバイステップのチュートリアル](/azure/azure-resource-manager/) をご覧ください。
- Azure Resource Manager のテンプレートのスキーマを表示するには、[テンプレート リファレンス](/azure/templates/)をご覧ください。