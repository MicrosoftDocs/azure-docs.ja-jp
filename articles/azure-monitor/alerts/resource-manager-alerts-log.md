---
title: ログ クエリ アラート用の Resource Manager テンプレートのサンプル
description: Azure Monitor ログ クエリ アラートをデプロイするためのサンプルの Azure Resource Manager テンプレート。
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 09/22/2020
ms.openlocfilehash: 19bbeba3db7e3df5ea5120d3562f24b447848f7f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102044036"
---
# <a name="resource-manager-template-samples-for-log-alert-rules-in-azure-monitor"></a>Azure Monitor のログ アラート ルール用の Resource Manager テンプレートのサンプル
この記事には、Azure Monitor でログ クエリ アラートを作成および構成するための [Azure Resource Manager テンプレート](../../azure-resource-manager/templates/template-syntax.md)のサンプルが含まれています。 各サンプルには、テンプレート ファイルと、テンプレートに指定するサンプル値を含むパラメーター ファイルが含まれています。

[!INCLUDE [azure-monitor-samples](../../../includes/azure-monitor-resource-manager-samples.md)]

## <a name="number-of-results-template-up-to-version-2018-04-16"></a>結果の数のテンプレート (バージョン 2018-04-16 以前)
次のサンプルでは、[結果の数のアラート ルール](../alerts/alerts-unified-log.md#count-of-the-results-table-rows)を作成します。

### <a name="notes"></a>Notes

- このサンプルには、[Webhook ペイロード](../alerts/alerts-log-webhook.md)が含まれています。 このアラート ルールによって Webhook がトリガーされない場合は、**customWebhookPayload** 要素を削除してください。

### <a name="template-file"></a>テンプレート ファイル

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "sourceId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Resource ID of the Log Analytics workspace."
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Location for the alert. Must be the same location as the workspace."
            }
        },
        "actionGroupId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The ID of the action group that is triggered when the alert is activated."
            }
        }
    },
    "resources":[ 
        {
            "type":"Microsoft.Insights/scheduledQueryRules",
            "name":"Sample log query alert",
            "apiVersion": "2018-04-16",
            "location": "[parameters('location')]",
            "properties":{
                "description": "Sample log query alert",
                "enabled": "true",
                "source": {
                    "query": "Event | where EventLevelName == \"Error\" | summarize count() by Computer",
                    "dataSourceId": "[parameters('sourceId')]",
                    "queryType":"ResultCount"
                },
                "schedule":{
                    "frequencyInMinutes": 15,
                    "timeWindowInMinutes": 60
                },
                "action":{
                    "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.Microsoft.AppInsights.Nexus.DataContracts.Resources.ScheduledQueryRules.AlertingAction",
                    "severity": "4",
                    "aznsAction":{
                        "actionGroup": "[array(parameters('actionGroupId'))]",
                        "emailSubject": "Alert mail subject",
                        "customWebhookPayload":"{ \"alertname\":\"#alertrulename\", \"IncludeSearchResults\":true }"
                    },
                    "trigger":{
                        "thresholdOperator": "GreaterThan",
                        "threshold": 1
                    }
                }
            }
        }
    ]
}
```

### <a name="parameter-file"></a>パラメーター ファイル

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "sourceId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/bw-samples-arm/providers/microsoft.operationalinsights/workspaces/bw-arm-01"
      },
      "location": {
        "value": "westus"
      },
      "actionGroupId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/bw-samples-arm/providers/microsoft.insights/actionGroups/ARM samples group 01"
      }
  }
}
```

## <a name="metric-measurement-template-up-to-version-2018-04-16"></a>メトリック測定のテンプレート (バージョン 2018-04-16 以前)
次のサンプルでは、[メトリック測定のアラート ルール](../alerts/alerts-unified-log.md#calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value)を作成します。

### <a name="template-file"></a>テンプレート ファイル

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "sourceId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Resource ID of the Log Analytics workspace."
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Location for the alert. Must be the same location as the workspace."
            }
        },
        "actionGroupId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The ID of the action group that is triggered when the alert is activated."
            }
        }
    },
    "resources":[ 
        {
            "type":"Microsoft.Insights/scheduledQueryRules",
            "name":"Sample metric measurement log query alert",
            "apiVersion": "2018-04-16",
            "location": "[parameters('location')]",
            "properties":{
                "description": "Sample metric measurement query alert rule",
                "enabled": "true",
                "source": {
                    "query": "Event | where EventLevelName == \"Error\" | summarize AggregatedValue = count() by bin(TimeGenerated,1h), Computer",
                    "dataSourceId": "[parameters('sourceId')]",
                    "queryType":"ResultCount"
                },
                "schedule":{
                    "frequencyInMinutes": 15,
                    "timeWindowInMinutes": 60
                },
                "action":{
                    "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.Microsoft.AppInsights.Nexus.DataContracts.Resources.ScheduledQueryRules.AlertingAction",
                    "severity": "4",
                    "aznsAction":{
                        "actionGroup": "[array(parameters('actionGroupId'))]",
                        "emailSubject": "Alert mail subject"
                    },
                    "trigger":{
                        "thresholdOperator": "GreaterThan",
                        "threshold": 10,
                        "metricTrigger":{
                            "thresholdOperator": "Equal",
                            "threshold": 1,
                            "metricTriggerType": "Consecutive",
                            "metricColumn": "Computer"
                        }
                    }
                }
            }
        }
    ]
}
```

### <a name="parameter-file"></a>パラメーター ファイル

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "sourceId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/bw-samples-arm/providers/microsoft.operationalinsights/workspaces/bw-arm-01"
      },
      "location": {
        "value": "westus"
      },
      "actionGroupId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/bw-samples-arm/providers/microsoft.insights/actionGroups/ARM samples group 01"
      }
  }
}
```

## <a name="template-for-all-resource-types-from-version-2020-05-01-preview"></a>リソースのすべての種類のテンプレート (バージョン 2020-05-01-preview 以降)
次のサンプルでは、すべてのリソースを対象とするルールを作成することができます。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the alert"
            }
        },
        "location": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Location of the alert"
            }
        },
        "alertDescription": {
            "type": "string",
            "defaultValue": "This is a metric alert",
            "metadata": {
                "description": "Description of alert"
            }
        },
        "alertSeverity": {
            "type": "int",
            "defaultValue": 3,
            "allowedValues": [
                0,
                1,
                2,
                3,
                4
            ],
            "metadata": {
                "description": "Severity of alert {0,1,2,3,4}"
            }
        },
        "isEnabled": {
            "type": "bool",
            "defaultValue": true,
            "metadata": {
                "description": "Specifies whether the alert is enabled"
            }
        },
        "resourceId": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Full Resource ID of the resource emitting the metric that will be used for the comparison. For example /subscriptions/00000000-0000-0000-0000-0000-00000000/resourceGroups/ResourceGroupName/providers/Microsoft.compute/virtualMachines/VM_xyz"
            }
        },
        "query": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric used in the comparison to activate the alert."
            }
        },
        "metricMeasureColumn": {
            "type": "string",
            "metadata": {
                "description": "Name of the measure column used in the alert evaluation."
            }
        },
        "resourceIdColumn": {
            "type": "string",
            "metadata": {
                "description": "Name of the resource ID column used in the alert targeting the alerts."
            }
        },
        "operator": {
            "type": "string",
            "defaultValue": "GreaterThan",
            "allowedValues": [
                "Equals",
                "NotEquals",
                "GreaterThan",
                "GreaterThanOrEqual",
                "LessThan",
                "LessThanOrEqual"
            ],
            "metadata": {
                "description": "Operator comparing the current value with the threshold value."
            }
        },
        "threshold": {
            "type": "string",
            "defaultValue": "0",
            "metadata": {
                "description": "The threshold value at which the alert is activated."
            }
        },
        "numberOfEvaluationPeriods": {
            "type": "string",
            "defaultValue": "1",
            "metadata": {
                "description": "The number of periods to check in the alert evaluation."
            }
        },
        "minFailingPeriodsToAlert": {
            "type": "string",
            "defaultValue": "1",
            "metadata": {
                "description": "The number of unhealthy periods to alert on (must be lower or equal to numberOfEvaluationPeriods)."
            }
        },
        "timeAggregation": {
            "type": "string",
            "defaultValue": "Average",
            "allowedValues": [
                "Average",
                "Minimum",
                "Maximum",
                "Total",
                "Count"
            ],
            "metadata": {
                "description": "How the data that is collected should be combined over time."
            }
        },
        "windowSize": {
            "type": "string",
            "defaultValue": "PT5M",
            "allowedValues": [
                "PT1M",
                "PT5M",
                "PT15M",
                "PT30M",
                "PT1H",
                "PT6H",
                "PT12H",
                "PT24H"
            ],
            "metadata": {
                "description": "Period of time used to monitor alert activity based on the threshold. Must be between one minute and one day. ISO 8601 duration format."
            }
        },
        "evaluationFrequency": {
            "type": "string",
            "defaultValue": "PT5M",
            "allowedValues": [
                "PT5M",
                "PT15M",
                "PT30M",
                "PT1H"
            ],
            "metadata": {
                "description": "how often the metric alert is evaluated represented in ISO 8601 duration format"
            }
        },
        "muteActionsDuration": {
            "type": "string",
            "allowedValues": [
                "PT1M",
                "PT5M",
                "PT15M",
                "PT30M",
                "PT1H",
                "PT6H",
                "PT12H",
                "PT24H"
            ],
            "metadata": {
                "description": "Mute actions for the chosen period of time (in ISO 8601 duration format) after the alert is fired."
            }
        },
        "actionGroupId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The ID of the action group that is triggered when the alert is activated or deactivated"
            }
        }
    },
    "variables": {  },
    "resources": [
        {
            "name": "[parameters('alertName')]",
            "type": "Microsoft.Insights/scheduledQueryRules",
            "location": "[parameters('location')]",
            "apiVersion": "2020-05-01-preview",
            "tags": {},
            "properties": {
                "description": "[parameters('alertDescription')]",
                "severity": "[parameters('alertSeverity')]",
                "enabled": "[parameters('isEnabled')]",
                "scopes": ["[parameters('resourceId')]"],
                "evaluationFrequency":"[parameters('evaluationFrequency')]",
                "windowSize": "[parameters('windowSize')]",
                "criteria": {
                    "allOf": [
                        {
                            "query": "[parameters('query')]",
                            "metricMeasureColumn": "[parameters('metricMeasureColumn')]",
                            "resourceIdColumn": "[parameters('resourceIdColumn')]",
                            "dimensions":[],
                            "operator": "[parameters('operator')]",
                            "threshold" : "[parameters('threshold')]",
                            "timeAggregation": "[parameters('timeAggregation')]",
                            "failingPeriods": {
                                "numberOfEvaluationPeriods": "[parameters('numberOfEvaluationPeriods')]",
                                "minFailingPeriodsToAlert": "[parameters('minFailingPeriodsToAlert')]"
                            }
                        }
                    ]
                },
                "muteActionsDuration": "[parameters('muteActionsDuration')]",
                "actions": [
                    {
                        "actionGroupId": "[parameters('actionGroupId')]"
                    }
                ]
            }
        }
    ]
}
```

### <a name="parameter-file"></a>パラメーター ファイル

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "value": "New Alert"
        },
        "location": {
            "value": "eastus"
        },
        "alertDescription": {
            "value": "New alert created via template"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "resourceId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resourceGroup-name/providers/Microsoft.Compute/virtualMachines/replace-with-resource-name"
        },
        "query": {
            "value": "Perf | where ObjectName == \"Processor\" and CounterName == \"% Processor Time\""
        },
        "metricMeasureColumn": {
            "value": "AggregatedValue"
        },
        "operator": {
          "value": "GreaterThan"
        },
        "threshold": {
            "value": "80"
        },
        "timeAggregation": {
            "value": "Average"
        },
        "actionGroupId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/resource-group-name/providers/Microsoft.Insights/actionGroups/replace-with-action-group"
        }
    }
}
```

## <a name="next-steps"></a>次のステップ

* [Azure Monitor の他のサンプル テンプレートを入手します](../resource-manager-samples.md)。
* [アラート ルールの詳細情報](./alerts-overview.md)。