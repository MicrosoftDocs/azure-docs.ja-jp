---
title: Resource Manager テンプレートを使用して新しい推奨事項に合わせて Azure Advisor アラートを作成する
description: Azure Resource Manager テンプレート (ARM テンプレート) を使用して、Azure Advisor からの新しい推奨事項に合わせてアラートを設定する方法について説明します。
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 06/29/2020
ms.openlocfilehash: 7b10ad06e5397ab733987373a3bd18de981c8c97
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "100590063"
---
# <a name="quickstart-create-azure-advisor-alerts-on-new-recommendations-using-an-arm-template"></a>クイック スタート:ARM テンプレートを使用して新しい推奨事項に基づいて Azure Advisor アラートを作成する

この記事では、Azure Resource Manager テンプレート (ARM テンプレート) を使用して、Azure Advisor からの新しい推奨事項に合わせてアラートを設定する方法を示します。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Azure Advisor でリソースの 1 つに対して新しい推奨事項が検出されるたびに、イベントが [Azure アクティビティ ログ](../azure-monitor/essentials/platform-logs-overview.md)に格納されます。 これらのイベントのアラートは、Azure Advisor から推奨事項固有のアラート作成操作を使用して設定できます。 サブスクリプションを選択し、必要に応じてリソース グループを選択して、アラートを受信するリソースを指定することができます。

また、次のプロパティを使用して、推奨事項の種類を指定することもできます。

- カテゴリ
- 影響レベル
- 推奨事項の種類

アラートがトリガーされたときに実行されるアクションは、次の方法で構成することもできます。

- 既存のアクション グループを選択する
- 新しいアクション グループを作成する

アクション グループの詳細については、[アクション グループの作成および管理](../azure-monitor/alerts/action-groups.md)に関するページを参照してください。

> [!NOTE]
> 現在、Advisor アラートは、高可用性、パフォーマンス、コストに関する推奨事項にのみ使用できます。 セキュリティに関する推奨事項はサポートされていません。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。
- ローカル コンピューターからコマンドを実行するには、Azure CLI または Azure PowerShell モジュールをインストールします。 詳細については、「[Azure CLI のインストール](/cli/azure/install-azure-cli)」および「[Azure PowerShell をインストールする](/powershell/azure/install-az-ps)」を参照してください。

## <a name="review-the-template"></a>テンプレートを確認する

次のテンプレートでは、電子メール ターゲットを使用してアクション グループを作成し、ターゲット サブスクリプションのすべてのサービス正常性通知を有効にします。 このテンプレートを *CreateAdvisorAlert.json* として保存します。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroups_name": {
      "defaultValue": "advisorAlert",
      "type": "string"
    },
    "activityLogAlerts_name": {
      "defaultValue": "AdvisorAlertsTest",
      "type": "string"
    },
    "emailAddress": {
      "defaultValue": "<email address>",
      "type": "string"
    }
  },
  "variables": {
    "alertScope": "[concat('/','subscriptions','/',subscription().subscriptionId)]"
  },
  "resources": [
    {
      "comments": "Action Group",
      "type": "microsoft.insights/actionGroups",
      "apiVersion": "2019-06-01",
      "name": "[parameters('actionGroups_name')]",
      "location": "Global",
      "scale": null,
      "dependsOn": [],
      "tags": {},
      "properties": {
        "groupShortName": "[parameters('actionGroups_name')]",
        "enabled": true,
        "emailReceivers": [
          {
            "name": "[parameters('actionGroups_name')]",
            "emailAddress": "[parameters('emailAddress')]"
          }
        ],
        "smsReceivers": [],
        "webhookReceivers": []
      }
    },
    {
      "comments": "Azure Advisor Activity Log Alert",
      "type": "microsoft.insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlerts_name')]",
      "location": "Global",
      "scale": null,
      "tags": {},
      "properties": {
        "scopes": [
          "[variables('alertScope')]"
        ],
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Recommendation"
            },
            {
              "field": "properties.recommendationCategory",
              "equals": "Cost"
            },
            {
              "field": "properties.recommendationImpact",
              "equals": "Medium"
            },
            {
              "field": "operationName",
              "equals": "Microsoft.Advisor/recommendations/available/action"
            }
          ]
        },
        "actions": {
          "actionGroups": [
            {
              "actionGroupId": "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]",
              "webhookProperties": {}
            }
          ]
        },
        "enabled": true,
        "description": ""
      },
      "dependsOn": [
        "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]"
      ]
    }
  ]
}
```

このテンプレートでは、次の 2 つのリソースが定義されます。

- [Microsoft.Insights/actionGroups](/azure/templates/microsoft.insights/actiongroups)
- [Microsoft.Insights/activityLogAlerts](/azure/templates/microsoft.insights/activityLogAlerts)

## <a name="deploy-the-template"></a>テンプレートのデプロイ

CLI と PowerShell を使用する次の例のような、[ARM テンプレートをデプロイする](../azure-resource-manager/templates/deploy-portal.md)ための標準的な方法を使ってテンプレートをデプロイします。 **リソース グループ** と **emailAddress** のサンプルの値を、ご利用の環境に適した値に置き換えます。 ワークスペース名は、すべての Azure サブスクリプションで一意である必要があります。

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az login
az deployment group create --name CreateAdvisorAlert --resource-group my-resource-group --template-file CreateAdvisorAlert.json --parameters emailAddress='user@contoso.com'
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName my-subscription
New-AzResourceGroupDeployment -Name CreateAdvisorAlert -ResourceGroupName my-resource-group -TemplateFile CreateAdvisorAlert.json -emailAddress user@contoso.com
```

---

## <a name="validate-the-deployment"></a>デプロイの検証

次のコマンドのいずれかを使用して、ワークスペースが作成されたことを確認します。 **リソース グループ** のサンプルの値を、先ほど使用した値に置き換えます。

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az monitor activity-log alert show --resource-group my-resource-group --name AdvisorAlertsTest
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Get-AzActivityLogAlert -ResourceGroupName my-resource-group -Name AdvisorAlertsTest
```

---

## <a name="clean-up-resources"></a>リソースをクリーンアップする

後続のクイック スタートおよびチュートリアルを引き続き実行する場合は、これらのリソースをそのまま残しておくことができます。 不要になったら、リソース グループを削除します。これにより、アラート ルールと関連リソースが削除されます。 Azure CLI または Azure PowerShell を使用してリソース グループを削除するには、次を実行します。

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az group delete --name my-resource-group
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Remove-AzResourceGroup -Name my-resource-group
```

---

## <a name="next-steps"></a>次のステップ

- [アクティビティ ログ アラートの概要](../azure-monitor/alerts/alerts-overview.md)を把握し、アラートを受信する方法について学習します。
- [アクション グループ](../azure-monitor/alerts/action-groups.md)について学習します。
