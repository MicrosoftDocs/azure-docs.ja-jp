---
title: Azure デプロイ クォータの超過
description: リソース グループ履歴でデプロイが 800 を超えたときのエラーを解決する方法について説明します。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: troubleshooting
ms.date: 10/04/2019
ms.author: tomfitz
ms.openlocfilehash: cb8a8238c4daac6370d47bb9e99b3503ebb68783
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2019
ms.locfileid: "72176560"
---
# <a name="resolve-error-when-deployment-count-exceeds-800"></a>デプロイ数が 800 を超えたときのエラーを解決する

各リソース グループには、そのデプロイ履歴が 800 までという上限があります。 この記事では、許可されている 800 を超え、デプロイに失敗したときに表示されるエラーについて説明します。 このエラーを解決するには、リソース グループ履歴でデプロイ履歴からデプロイを削除します。 履歴からデプロイを削除しても、デプロイされたリソースには影響が出ません。

## <a name="symptom"></a>症状

デプロイ中、現在のデプロイで 800 というクォータを超過する旨を伝えるエラーが表示されます。

## <a name="solution"></a>解決策

### <a name="azure-cli"></a>Azure CLI

[az group deployment delete](/cli/azure/group/deployment#az-group-deployment-delete) コマンドを使用し、履歴からデプロイを削除します。

```azurecli-interactive
az group deployment delete --resource-group exampleGroup --name deploymentName
```

5 日を経過したデプロイをすべて削除するには、次を使用します。

```azurecli-interactive
startdate=$(date +%F -d "-5days")
deployments=$(az group deployment list --resource-group exampleGroup --query "[?properties.timestamp>'$startdate'].name" --output tsv)

for deployment in $deployments
do
  az group deployment delete --resource-group exampleGroup --name $deployment
done
```

次のコマンドでは、デプロイ履歴の現在のデプロイ数を取得できます。

```azurecli-interactive
az group deployment list --resource-group exampleGroup --query "length(@)"
```

### <a name="azure-powershell"></a>Azure PowerShell

[Remove-AzResourceGroupDeployment](/powershell/module/az.resources/remove-azresourcegroupdeployment) コマンドを使用し、履歴からデプロイを削除します。

```azurepowershell-interactive
Remove-AzResourceGroupDeployment -ResourceGroupName exampleGroup -Name deploymentName
```

5 日を経過したデプロイをすべて削除するには、次を使用します。

```azurepowershell-interactive
$deployments = Get-AzResourceGroupDeployment -ResourceGroupName exampleGroup | Where-Object Timestamp -lt ((Get-Date).AddDays(-5))

foreach ($deployment in $deployments) {
  Remove-AzResourceGroupDeployment -ResourceGroupName exampleGroup -Name $deployment.DeploymentName
}
```

次のコマンドでは、デプロイ履歴の現在のデプロイ数を取得できます。

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName exampleGroup).Count
```

## <a name="third-party-solutions"></a>サードパーティ ソリューション

次の外部ソリューションで特定のシナリオに対処します。

* [Azure Logic Apps と PowerShell のソリューション](https://devkimchi.com/2018/05/30/managing-excessive-arm-deployment-histories-with-logic-apps/)
* [AzDevOps Extension](https://github.com/christianwaha/AzureDevOpsExtensionCleanRG)
