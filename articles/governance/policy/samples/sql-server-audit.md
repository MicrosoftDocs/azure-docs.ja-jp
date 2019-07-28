---
title: サンプル - SQL Server の監査設定の監査
description: このサンプル ポリシー定義は、SQL サーバーの監査設定を auditIfNotExists で監査します。
author: dcthegeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
ms.date: 01/23/2019
ms.author: dacoulte
ms.openlocfilehash: c4510a316760fa948aa39627c9a9c517437b1d77
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59790981"
---
# <a name="sample---audit-sql-server-audit-settings"></a>サンプル - SQL Server の監査設定の監査

このビルトイン ポリシーでは、監査設定が有効になっているかどうかに基づいて SQL Server を監査します。

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>サンプル テンプレート

```json
{
  "if": {
    "field": "type",
    "equals": "Microsoft.SQL/servers"
  },
  "then": {
    "effect": "auditIfNotExists",
    "details": {
      "type": "Microsoft.SQL/servers/auditingSettings",
      "name": "default",
      "existenceCondition": {
        "allOf": [
          {
            "field": "Microsoft.Sql/auditingSettings.state",
            "equals": "[parameters('setting')]"
          }
        ]
      }
    }
  }
}
```

このテンプレートは、[Azure Portal](#deploy-with-the-portal)、[PowerShell](#deploy-with-powershell)、または [Azure CLI](#deploy-with-azure-cli) を使用してデプロイできます。 ビルトイン ポリシーを取得するには、ID `a6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9` を使用します。

## <a name="parameters"></a>parameters

パラメーター値を渡すには、次の形式を使用してください。

```json
{"setting": {"value":"enabled"}}
```

## <a name="deploy-with-the-portal"></a>ポータルでのデプロイ

ポリシーを割り当てるときに、利用可能なビルトイン定義から **[Audit SQL Server Level Audit Setting]\(SQL Server レベルの監査設定を監査する\)** を選択します。

## <a name="deploy-with-powershell"></a>PowerShell でデプロイする

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

```azurepowershell-interactive
$definition = Get-AzPolicyDefinition -Id /providers/Microsoft.Authorization/policyDefinitions/a6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9

New-AzPolicyAssignment -name "SQL Audit audit" -PolicyDefinition $definition -PolicyParameter '{"setting": {"value":"enabled"}}' -Scope <scope>
```

### <a name="clean-up-powershell-deployment"></a>PowerShell でのデプロイをクリーンアップする

ポリシーの割り当てを削除するには、次のコマンドを実行します。

```azurepowershell-interactive
Remove-AzPolicyAssignment -Name "SQL Audit audit" -Scope <scope>
```

## <a name="deploy-with-azure-cli"></a>Azure CLI でのデプロイ

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

```azurecli-interactive
az policy assignment create --scope <scope> --name "SQL Audit audit" --policy a6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9 --params '{"setting": {"value":"enabled"}}'
```

### <a name="clean-up-azure-cli-deployment"></a>Azure CLI でのデプロイをクリーンアップする

ポリシーの割り当てを削除するには、次のコマンドを実行します。

```azurecli-interactive
az policy assignment delete --name "SQL Audit audit" --resource-group myResourceGroup
```

## <a name="next-steps"></a>次の手順

- 他のサンプルについては、「[Azure Policy のサンプル](index.md)」をご確認ください。