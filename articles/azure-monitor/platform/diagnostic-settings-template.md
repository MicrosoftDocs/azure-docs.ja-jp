---
title: Azure で Resource Manager テンプレートを使用して診断設定を作成する
description: Azure プラットフォーム ログを Azure Monitor ログ、Azure ストレージ、または Azure Event Hubs に転送するための診断設定を、Resource Manager テンプレートを使用してを作成します。
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: 73f2b3ea90cc94fa3411552c7b812fe53eb4dbbb
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87008100"
---
# <a name="create-diagnostic-setting-in-azure-using-a-resource-manager-template"></a>Azure で Resource Manager テンプレートを使用して診断設定を作成する
Azure Monitor の[診断設定](diagnostic-settings.md)では、Azure リソースとそれらが依存している Azure プラットフォームによって収集される[プラットフォーム ログ](platform-logs-overview.md)の送信先が指定されます。 この記事では、[Azure Resource Manager テンプレート](../../azure-resource-manager/templates/template-syntax.md)を使用して、プラットフォーム ログを収集してさまざまな宛先に送信するための診断設定を作成し構成する方法について詳しく説明します。

> [!NOTE]
> 他の Azure リソース用の診断設定とは違い、Azure アクティビティ ログ用の[診断設定を PowerShell または CLI を使用して作成](diagnostic-settings.md)することはできないため、この記事の情報を使用してアクティビティ ログ用の Resource Manager テンプレートを作成し、そのテンプレートを PowerShell または CLI を使用してデプロイします。

## <a name="deployment-methods"></a>デプロイ方法
Resource Manager テンプレートをデプロイするには、PowerShell や CLI などの任意の有効な方法を使用します。 アクティビティ ログ用の診断設定は、CLI の場合は `az deployment create` を使用し、PowerShell の場合は `New-AzDeployment` を使用してサブスクリプションにデプロイする必要があります。 リソース ログ用の診断設定は、CLI の場合は `az group deployment create` を使用し、PowerShell の場合は `New-AzResourceGroupDeployment` を使用してリソース グループにデプロイする必要があります。

詳細については、「[Resource Manager テンプレートと Azure PowerShell を使用したリソースのデプロイ](../../azure-resource-manager/templates/deploy-powershell.md)」と「[Resource Manager テンプレートと Azure CLI を使用したリソースのデプロイ](../../azure-resource-manager/templates/deploy-cli.md)」を参照してください。 





## <a name="resource-logs"></a>リソース ログ
リソース ログの場合は、`<resource namespace>/providers/diagnosticSettings` 型のリソースをテンプレートに追加します。 プロパティ セクションでは、「[Diagnostic Settings - Create Or Update](/rest/api/monitor/diagnosticsettings/createorupdate)」 (診断設定 - 作成または更新) に記載の形式に従います。 収集するリソースに対して有効な各カテゴリの `logs` セクションに `category` を指定します。 [リソースでメトリックがサポートされている](metrics-supported.md)場合は、`metrics` プロパティを追加して、リソースのメトリックを同じ送信先に収集します。

次に示すのは、特定のリソースのリソース ログ カテゴリを、Log Analytics ワークスペース、ストレージ アカウント、およびイベント ハブに収集するテンプレートです。

```json
"resources": [
  {
    "type": "/<resource namespace>/providers/diagnosticSettings",
    "name": "[concat(parameters('resourceName'),'/microsoft.insights/', parameters('settingName'))]",
    "dependsOn": [
      "[<resource Id for which resource logs will be enabled>]"
    ],
    "apiVersion": "2017-05-01-preview",
    "properties": {
      "name": "[parameters('settingName')]",
      "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
      "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
      "eventHubName": "[parameters('eventHubName')]",
      "workspaceId": "[parameters('workspaceId')]",
      "logs": [
        {
          "category": "<category name>",
          "enabled": true
        }
      ],
      "metrics": [
        {
          "category": "AllMetrics",
          "enabled": true
        }
      ]
    }
  }
]
```



### <a name="example"></a>例
次に示すのは、自動スケール設定用の診断設定を作成する例です。これを使用すれば、イベント ハブ、ストレージ アカウント、Log Analytics ワークスペースにリソース ログをストリーミングすることができます。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "autoscaleSettingName": {
            "type": "string",
            "metadata": {
                "description": "The name of the autoscale setting"
            }
        },
        "settingName": {
            "type": "string",
            "metadata": {
                "description": "The name of the diagnostic setting"
            }
        },
        "workspaceId": {
            "type": "string",
            "metadata": {
                "description": "ResourceIDl of the Log Analytics workspace in which resource logs should be saved."
            }
        },
        "storageAccountId": {
            "type": "string",
            "metadata": {
              "description": "ResourceID of the Storage Account in which resource logs should be saved."
            }
        },
        "eventHubAuthorizationRuleId": {
            "type": "string",
            "metadata": {
              "description": "Resource ID of the event hub authorization rule for the Event Hubs namespace in which the event hub should be created or streamed to."
            }
        },
        "eventHubName": {
            "type": "string",
            "metadata": {
                "description": "Optional. Name of the event hub within the namespace to which logs are streamed. Without this, an event hub is created for each log category."
            }
        }
    },
    "variables": {},
    "resources": [
    {
      "type": "microsoft.insights/autoscalesettings/providers/diagnosticSettings",
      "apiVersion": "2017-05-01-preview",
      "name": "[concat(parameters('autoscaleSettingName'),'/microsoft.insights/', parameters('settingName'))]",
      "dependsOn": [
        "[resourceId('Microsoft.Insights/autoscalesettings', parameters('autoscaleSettingName'))]"
      ],
      "properties": {
        "workspaceId": "[parameters('workspaceId')]",
        "storageAccountId": "[parameters('storageAccountId')]",
        "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
        "eventHubName": "[parameters('eventHubName')]",
        "logs": [
          {
            "category": "AutoscaleScaleActions",
            "enabled": true
          },
          {
            "category": "AutoscaleEvaluations",
            "enabled": true
          }
        ]
      }
    }
  ]
}
```

## <a name="activity-log"></a>アクティビティ ログ
Azure アクティビティ ログの場合は、`Microsoft.Insights/diagnosticSettings` 型のリソースを追加します。 [アクティビティ ログのカテゴリ](./activity-log.md#view-the-activity-log)に、利用可能なカテゴリが一覧表示されます。 次に示すのは、すべてのアクティビティ ログ カテゴリを Log Analytics ワークスペース、ストレージ アカウント、およびイベント ハブに収集するテンプレートです。


```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "settingName": {
            "type": "string",
            "metadata": {
                "description": "The name of the diagnostic setting"
            }
        },
        "workspaceId": {
            "type": "string",
            "metadata": {
                "description": "ResourceID of the Log Analytics workspace in which resource logs should be saved."
            }
        },
        "storageAccountId": {
            "type": "string",
            "metadata": {
              "description": "ResourceID of the Storage Account in which resource logs should be saved."
            }
        },
        "eventHubAuthorizationRuleId": {
            "type": "string",
            "metadata": {
              "description": "Resource ID of the event hub authorization rule for the Event Hubs namespace in which the event hub should be created or streamed to."
            }
        },
        "eventHubName": {
            "type": "string",
            "metadata": {
                "description": "Optional. Name of the event hub within the namespace to which logs are streamed. Without this, an event hub is created for each log category."
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Insights/diagnosticSettings",
            "apiVersion": "2017-05-01-preview",
            "name": "[parameters('settingName')]",
            "location": "global",
            "properties": {
                "workspaceId": "[parameters('workspaceId')]",
                "storageAccountId": "[parameters('storageAccountId')]",
                "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
                "eventHubName": "[parameters('eventHubName')]",
                "logs": [
                    {
                        "category": "Administrative",
                        "enabled": true
                    },
                    {
                        "category": "Security",
                        "enabled": true
                    },
                    {
                        "category": "ServiceHealth",
                        "enabled": true
                    },
                    {
                        "category": "Alert",
                        "enabled": true
                    },
                    {
                        "category": "Recommendation",
                        "enabled": true
                    },
                    {
                        "category": "Policy",
                        "enabled": true
                    },
                    {
                        "category": "Autoscale",
                        "enabled": true
                    },
                    {
                        "category": "ResourceHealth",
                        "enabled": true
                    }
                ]
            }
        }
    ]
}
```


## <a name="next-steps"></a>次のステップ
* [Azure でのプラットフォーム ログ](platform-logs-overview.md)の詳細を説明します。
* [診断設定](diagnostic-settings.md)について説明します。
