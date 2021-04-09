---
title: Resource Manager テンプレートを使用して Azure サービス通知でアクティビティ ログ アラートを受け取る
description: Azure サービスが発生したときに、SMS、電子メール、または Webhook で通知を受け取ります。
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 06/29/2020
ms.openlocfilehash: 532fbae505e0bcaa6ab31a2e935362114537d134
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100594961"
---
# <a name="quickstart-create-activity-log-alerts-on-service-notifications-using-an-arm-template"></a>クイック スタート:ARM テンプレートを使用してサービス通知のアクティビティ ログ アラートを作成する

この記事では、Azure Resource Manager テンプレート (ARM テンプレート) を使用して、サービスの正常性通知のアクティビティ ログ アラートを設定する方法を示します。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

サービス正常性通知は、[Azure アクティビティ ログ](../azure-monitor/essentials/platform-logs-overview.md)に格納されます。 アクティビティ ログに大量の情報が格納されている可能性を考慮し、サービス正常性通知に関するアラートを表示および設定しやすくするための個別のユーザー インターフェイスがあります。

Azure でサービス正常性通知を Azure サブスクリプションに送信するときに、アラートを受け取ることができます。 次の情報に基づくアラートを構成できます。

- サービス正常性通知のクラス (サービスに関する問題、計画済みメンテナンス、正常性の勧告)。
- 影響を受けたサブスクリプション。
- 影響を受けたサービス。
- 影響を受けたリージョン。

> [!NOTE]
> サービス正常性通知では、リソース正常性イベントに関するアラートは送信されません。

次の方法でアラートを送信するユーザーを構成することもできます。

- 既存のアクション グループを選択します。
- 新しいアクション グループを作成します (将来のアラートで使用できます)。

アクション グループの詳細については、[アクション グループの作成および管理](../azure-monitor/alerts/action-groups.md)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。
- ローカル コンピューターからコマンドを実行するには、Azure CLI または Azure PowerShell モジュールをインストールします。 詳細については、「[Azure CLI のインストール](/cli/azure/install-azure-cli)」および「[Azure PowerShell をインストールする](/powershell/azure/install-az-ps)」を参照してください。

## <a name="review-the-template"></a>テンプレートを確認する

次のテンプレートでは、電子メール ターゲットを使用してアクション グループを作成し、ターゲット サブスクリプションのすべてのサービス正常性通知を有効にします。 このテンプレートを *CreateServiceHealthAlert.json* として保存します。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroups_name": {
      "type": "String",
      "defaultValue": "SubHealth"
    },
    "activityLogAlerts_name": {
      "type": "String",
      "defaultValue": "ServiceHealthActivityLogAlert"
    },
    "emailAddress": {
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
      "comments": "Service Health Activity Log Alert",
      "type": "microsoft.insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlerts_name')]",
      "location": "Global",
      "scale": null,
      "dependsOn": [
        "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]"
      ],
      "tags": {},
      "properties": {
        "scopes": [
          "[variables('alertScope')]"
        ],
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "ServiceHealth"
            },
            {
              "field": "properties.incidentType",
              "equals": "Incident"
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
      }
    }
  ]
}
```

このテンプレートでは、次の 2 つのリソースが定義されます。

- [Microsoft.Insights/actionGroups](/azure/templates/microsoft.insights/actiongroups)
- [Microsoft.Insights/activityLogAlerts](/azure/templates/microsoft.insights/activityLogAlerts)

## <a name="deploy-the-template"></a>テンプレートのデプロイ

CLI と PowerShell を使用する次の例のような、[ARM テンプレートをデプロイする](../azure-resource-manager/templates/deploy-portal.md)ための標準的な方法を使ってテンプレートをデプロイします。 **リソース グループ** と **emailAddress** のサンプルの値を、ご利用の環境に適した値に置き換えます。

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az login
az deployment group create --name CreateServiceHealthAlert --resource-group my-resource-group --template-file CreateServiceHealthAlert.json --parameters emailAddress='user@contoso.com'
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName my-subscription
New-AzResourceGroupDeployment -Name CreateServiceHealthAlert -ResourceGroupName my-resource-group -TemplateFile CreateServiceHealthAlert.json -emailAddress user@contoso.com
```

---

## <a name="validate-the-deployment"></a>デプロイの検証

次のコマンドのいずれかを使用して、ワークスペースが作成されたことを確認します。 **リソース グループ** のサンプルの値を、先ほど使用した値に置き換えます。

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az monitor activity-log alert show --resource-group my-resource-group --name ServiceHealthActivityLogAlert
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Get-AzActivityLogAlert -ResourceGroupName my-resource-group -Name ServiceHealthActivityLogAlert
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

- [Azure Service Health アラートの設定に関するベスト プラクティス](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUa)を学習します。
- [Azure Service Health のモバイル プッシュ通知を設定](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUw)する方法について学習します。
- [既存の問題管理システム用の webhook 通知を構成する](service-health-alert-webhook-guide.md)方法について学習します。
- [サービス正常性の通知](service-notifications.md)について学習します。
- [通知のレート制限](../azure-monitor/alerts/alerts-rate-limiting.md)について学習します。
- [アクティビティ ログ アラート webhook スキーマ](../azure-monitor/alerts/activity-log-alerts-webhook.md)を確認します。
- [アクティビティ ログ アラートの概要](../azure-monitor/alerts/alerts-overview.md)を把握し、アラートを受信する方法について学習します。
- [アクション グループ](../azure-monitor/alerts/action-groups.md)について学習します。
