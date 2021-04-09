---
title: リソース グループの管理 - Azure PowerShell
description: Azure PowerShell を使用して、Azure Resource Manager 経由でリソース グループを管理します。 リソース グループを作成、一覧表示、および削除する方法を示します。
author: mumian
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: jgao
ms.openlocfilehash: fb8cd45842e2f9b64048aa939f7b6adc9b0e6e0a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98703684"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-powershell"></a>Azure PowerShell を使用して Azure Resource Manager リソース グループを管理する

[Azure Resource Manager](overview.md) で Azure PowerShell を使用して Azure リソースグループを管理する方法について説明します。 Azure リソースの管理については、[Manage Azure resources by using Azure PowerShell (Azure PowerShell を使用して Azure リソースを管理する)](manage-resources-powershell.md) を参照してください。

リソース グループの管理に関するその他の記事は次のとおりです。

- [Azure portal を使用した Azure リソース グループの管理](manage-resources-portal.md)
- [Azure CLI を使用した Azure リソース グループの管理](manage-resources-cli.md)に関する記事

## <a name="what-is-a-resource-group"></a>リソース グループとは

リソース グループは、Azure ソリューションの関連するリソースを保持するコンテナーです。 リソース グループには、ソリューションのすべてのリソースか、グループとして管理したいリソースのみを含めることができます。 組織のニーズに合わせてリソースをリソース グループに割り当てる方法を指定してください。 通常は、同じライフサイクルを共有するリソースを同じリソース グループに追加して、グループとして簡単にデプロイ、更新、および削除できるようにします。

リソース グループには、リソースについてのメタデータが格納されます。 そのため、リソース グループの場所を指定するとき、このメタデータが格納される場所を指定することになります。 コンプライアンス上の理由から、データは特定のリージョンに格納されるようにする必要があります。

リソース グループには、リソースについてのメタデータが格納されます。 リソース グループの場所を指定するとき、このメタデータが格納される場所を指定することになります。

## <a name="create-resource-groups"></a>リソース グループを作成する

次の PowerShell スクリプトでは、リソース グループを作成します。

```azurepowershell-interactive
New-AzResourceGroup -Name demoResourceGroup -Location westus
```

## <a name="list-resource-groups"></a>リソース グループの一覧を表示する

次の PowerShell スクリプトでは、サブスクリプションの下にリソース グループを一覧表示します。

```azurepowershell-interactive
Get-AzResourceGroup
```

1 つのリソース グループを取得するには、次のスクリプトを使用します。

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Get-AzResourceGroup -Name $resourceGroupName
```

## <a name="delete-resource-groups"></a>リソース グループを削除する

次の PowerShell スクリプトはリソース グループを削除します。

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Remove-AzResourceGroup -Name $resourceGroupName
```

Azure Resource Manager によってリソースの削除の順序が決定される方法の詳細については、「[Azure Resource Manager によるリソース グループの削除](delete-resource-group.md)」を参照してください。

## <a name="deploy-resources-to-an-existing-resource-group"></a>リソースを既存のリソース グループにデプロイする

「[Deploy resources to an existing resource group (リソースを既存のリソース グループにデプロイする)](manage-resources-powershell.md#deploy-resources-to-an-existing-resource-group)」をご覧ください。

リソース グループのデプロイを検証するには、[Test-AzResourceGroupDeployment](/powershell/module/Az.Resources/Test-AzResourceGroupDeployment) をご覧ください。

## <a name="deploy-a-resource-group-and-resources"></a>リソース グループとリソースをデプロイする

Resource Manager テンプレートを使用して、リソース グループを作成し、そのグループにリソースをデプロイできます。 詳しくは、「[リソース グループを作成してリソースをデプロイする](../templates/deploy-to-subscription.md#resource-groups)」をご覧ください。

## <a name="redeploy-when-deployment-fails"></a>デプロイに失敗したときに再デプロイする

この機能は、"*エラー時のロールバック*" とも呼ばれます。 詳細については、「[デプロイに失敗したときに再デプロイする](../templates/rollback-on-error.md)」を参照してください。

## <a name="move-to-another-resource-group-or-subscription"></a>別のリソース グループまたはサブスクリプションに移動する

グループ内のリソースを別のリソース グループに移動できます。 詳細については、「 [新しいリソース グループまたはサブスクリプションへのリソースの移動](move-resource-group-and-subscription.md)」を参照してください。

## <a name="lock-resource-groups"></a>リソース グループをロックする

ロックすることで、組織内の他のユーザーが重要なリソース (Azure サブスクリプション、リソース グループ、リソースなど) を誤って削除または変更することを防ぎます。 

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

詳細については、[「Azure Resource Manager によるリソースのロック」](lock-resources.md)を参照してください。

## <a name="tag-resource-groups"></a>リソース グループにタグを適用する

リソース グループやリソースにタグを適用して、アセットを論理的に整理できます。 詳細については、[タグを使用した Azure リソースの整理](tag-resources.md#powershell) に関する記事をご覧ください。

## <a name="export-resource-groups-to-templates"></a>リソース グループをテンプレートにエクスポートする

リソース グループを設定したら、リソース グループの Resource Manager テンプレートを表示できます。 テンプレートのエクスポートには、2 つの利点があります。

- テンプレートに完全なインフラストラクチャが含まれているため、ソリューションの将来のデプロイを自動化します。
- ソリューションを表す JavaScript Object Notation (JSON) を調べることで、テンプレートの構文を確認できます。

リソース グループ内のすべてのリソースをエクスポートするには、[Export-AzResourceGroup](/powershell/module/az.resources/Export-AzResourceGroup) コマンドレットを使用して、リソース グループ名を指定します。

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Export-AzResourceGroup -ResourceGroupName $resourceGroupName
```

これでテンプレートはローカル ファイルとして保存されます。

リソース グループ内のすべてのリソースをエクスポートする代わりに、どのリソースをエクスポートするかを選択できます。

1 つのリソースをエクスポートするには、そのリソースの ID を渡します。

```azurepowershell-interactive
$resource = Get-AzResource `
  -ResourceGroupName <resource-group-name> `
  -ResourceName <resource-name> `
  -ResourceType <resource-type>
Export-AzResourceGroup `
  -ResourceGroupName <resource-group-name> `
  -Resource $resource.ResourceId
```

複数のリソースをエクスポートするには、リソース ID を配列で渡します。

```azurepowershell-interactive
Export-AzResourceGroup `
  -ResourceGroupName <resource-group-name> `
  -Resource @($resource1.ResourceId, $resource2.ResourceId)
```

テンプレートをエクスポートするときには、テンプレート内でパラメーターを使用するかどうかを指定できます。 既定では、リソース名用のパラメーターが含められますが、既定値はありません。 そのパラメーターの値は、デプロイ時に渡す必要があります。

```json
"parameters": {
  "serverfarms_demoHostPlan_name": {
    "defaultValue": null,
    "type": "String"
  },
  "sites_webSite3bwt23ktvdo36_name": {
    "defaultValue": null,
    "type": "String"
  }
}
```

リソース内では、パラメーターは名前用に使用されます。

```json
"resources": [
  {
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2016-09-01",
    "name": "[parameters('serverfarms_demoHostPlan_name')]",
    ...
  }
]
```

テンプレートをエクスポートするときに `-IncludeParameterDefaultValue` パラメーターを使用する場合、テンプレート パラメーターには、現在の値に設定される既定値が含められます。 その既定値を使用することも、異なる値を渡して既定値を上書きすることもできます。

```json
"parameters": {
  "serverfarms_demoHostPlan_name": {
    "defaultValue": "demoHostPlan",
    "type": "String"
  },
  "sites_webSite3bwt23ktvdo36_name": {
    "defaultValue": "webSite3bwt23ktvdo36",
    "type": "String"
  }
}
```

テンプレートをエクスポートするときに `-SkipResourceNameParameterization` パラメーターを使用する場合、リソース名用のパラメーターはテンプレートに含められません。 代わりに、リソース名はリソースに対して直接、現在の値に設定されます。 この名前はデプロイ時にカスタマイズすることはできません。

```json
"resources": [
  {
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2016-09-01",
    "name": "demoHostPlan",
    ...
  }
]
```

テンプレートのエクスポート機能は、Azure Data Factory リソースのエクスポートをサポートしていません。 Data Factory リソースをエクスポートする方法については、「[Azure Data Factory のデータ ファクトリをコピーまたは複製する](../../data-factory/copy-clone-data-factory.md)」を参照してください。

クラシック デプロイ モデルを使用して作成されたリソースをエクスポートするには、[Resource Manager デプロイ モデルに移行する](../../virtual-machines/migration-classic-resource-manager-overview.md)必要があります。

詳細については、「[Azure portal のテンプレートへの単一および複数リソースのエクスポート](../templates/export-template-portal.md)」を参照してください。

## <a name="manage-access-to-resource-groups"></a>リソース グループへのアクセスを管理する

[Azure ロールベースのアクセス制御 (Azure RBAC)](../../role-based-access-control/overview.md) は、Azure のリソースへのアクセスを管理する方法です。 詳細については、「[Azure PowerShell を使用して Azure ロールの割り当てを追加または削除する](../../role-based-access-control/role-assignments-powershell.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

- Azure Resource Manager については、「[Azure Resource Manager の概要](overview.md)」を参照してください。
- Resource Manager テンプレートの構文については、「[Azure Resource Manager テンプレートの構造と構文の詳細](../templates/template-syntax.md)」を参照してください。
- テンプレートを開発する方法については、[ステップバイステップのチュートリアル](../index.yml)のページをご覧ください。
- Azure Resource Manager テンプレートのスキーマを表示するには、[テンプレート リファレンス](/azure/templates/)のページをご覧ください。