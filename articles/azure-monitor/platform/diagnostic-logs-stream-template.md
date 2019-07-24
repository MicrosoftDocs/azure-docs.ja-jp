---
title: Resource Manager テンプレートを使用して診断設定を自動的に有効にする
description: Resource Manager テンプレートを使用して、診断ログを Event Hubs にストリーミングしたり、ストレージ アカウントに保存したりできるようにするための診断設定を作成する方法について説明します。
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 3/26/2018
ms.author: johnkem
ms.subservice: ''
ms.openlocfilehash: 7edce5175a1dda66abf3316cb8f0eb33e9f64ef7
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2019
ms.locfileid: "58371471"
---
# <a name="automatically-enable-diagnostic-settings-at-resource-creation-using-a-resource-manager-template"></a>Resource Manager テンプレートを使用してリソースの作成時に診断設定を自動的に有効にする
この記事では、 [Azure Resource Manager テンプレート](../../azure-resource-manager/resource-group-authoring-templates.md) を使用して、リソースの作成時にリソースの診断設定を構成する方法について説明します。 これにより、リソースの作成時に、診断ログとメトリックの Event Hubs へのストリーミング、ストレージ アカウントへのアーカイブ、または Log Analytics ワークスペースへの送信を自動的に開始できます。

> [!WARNING]
> ストレージ アカウント内のログ データの形式は、2018 年 11 月 1 日より JSON Lines に変更されます。 [この記事では、この変更による影響と、新しい形式に対応するツールに更新する方法について説明します。](./../../azure-monitor/platform/diagnostic-logs-append-blobs.md) 
>
> 

Resource Manager テンプレートを使用して診断ログを有効にする方法は、リソースの種類によって異なります。

* **非コンピューティング** リソース (ネットワーク セキュリティ グループ、Logic Apps、Automation など) では、 [こちらの記事で説明する診断設定](../../azure-monitor/platform/diagnostic-logs-overview.md#diagnostic-settings)を使用します。
* **コンピューティング** (WAD/LAD ベースの) リソースでは、 [こちらの記事で説明する WAD/LAD 構成ファイル](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines)を使用します。

この記事では、いずれかの方法を使用して診断を構成する方法について説明します。

基本的な手順は次のとおりです。

1. リソースを作成し、診断を有効にする方法を記述した JSON ファイルとしてテンプレートを作成します。
2. [任意のデプロイ方法を使用してテンプレートをデプロイ](../../azure-resource-manager/resource-group-template-deploy.md)します。

生成する必要がある、非コンピューティング リソースおよびコンピューティング リソースのテンプレート JSON ファイルの例を以下に示します。

## <a name="non-compute-resource-template"></a>非コンピューティング リソース テンプレート
非コンピューティング リソースの場合、次の 2 つの手順を実行する必要があります。

1. パラメーター BLOB に、ストレージ アカウント名、イベント ハブ承認規則 ID、Log Analytics ワークスペース ID のパラメーターを追加します (ストレージ アカウントへの診断ログのアーカイブ、Event Hubs へのログのストリーミング、Azure Monitor へのログの送信を有効にするため)。
   
    ```json
    "settingName": {
      "type": "string",
      "metadata": {
        "description": "Name for the diagnostic setting resource. Eg. 'archiveToStorage' or 'forSecurityTeam'."
      }
    },
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Storage Account in which Diagnostic Logs should be saved."
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
    },
    "workspaceId":{
      "type": "string",
      "metadata": {
        "description": "Azure Resource ID of the Log Analytics workspace for the Log Analytics workspace to which logs will be sent."
      }
    }
    ```
2. 診断ログを有効にするリソースのリソース配列に、 `[resource namespace]/providers/diagnosticSettings`型のリソースを追加します。
   
    ```json
    "resources": [
      {
        "type": "providers/diagnosticSettings",
        "name": "[concat('Microsoft.Insights/', parameters('settingName'))]",
        "dependsOn": [
          "[/*resource Id for which Diagnostic Logs will be enabled>*/]"
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
              "category": "/* log category name */",
              "enabled": true,
              "retentionPolicy": {
                "days": 0,
                "enabled": false
              }
            }
          ],
          "metrics": [
            {
              "category": "AllMetrics",
              "enabled": true,
              "retentionPolicy": {
                "enabled": false,
                "days": 0
              }
            }
          ]
        }
      }
    ]
    ```

診断設定のプロパティ BLOB は、 [こちらの記事で説明する形式](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings/createorupdate)に従います。 [リソースが Azure Monitor メトリックをサポートしてる](../../azure-monitor/platform/metrics-supported.md)場合、`metrics` プロパティを追加して、リソース メトリックをこれらの同じ出力に送信することもできます。

ロジック アプリを作成し、Event Hubs およびストレージ アカウント内のストレージへのストリーミングを有効にする例を次に示します。

```json

{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "logicAppName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Logic App that will be created."
      }
    },
    "testUri": {
      "type": "string",
      "defaultValue": "https://azure.microsoft.com/status/feed/"
    },
    "settingName": {
      "type": "string",
      "metadata": {
        "description": "Name of the setting. Name for the diagnostic setting resource. Eg. 'archiveToStorage' or 'forSecurityTeam'."
      }
    },
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Storage Account in which Diagnostic Logs should be saved."
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
    },
    "workspaceId": {
      "type": "string",
      "metadata": {
        "description": "Log Analytics workspace ID for the Log Analytics workspace to which logs will be sent."
      }
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Logic/workflows",
      "name": "[parameters('logicAppName')]",
      "apiVersion": "2016-06-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "definition": {
          "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "testURI": {
              "type": "string",
              "defaultValue": "[parameters('testUri')]"
            }
          },
          "triggers": {
            "recurrence": {
              "type": "recurrence",
              "recurrence": {
                "frequency": "Hour",
                "interval": 1
              }
            }
          },
          "actions": {
            "http": {
              "type": "Http",
              "inputs": {
                "method": "GET",
                "uri": "@parameters('testUri')"
              },
              "runAfter": {}
            }
          },
          "outputs": {}
        },
        "parameters": {}
      },
      "resources": [
        {
          "type": "providers/diagnosticSettings",
          "name": "[concat('Microsoft.Insights/', parameters('settingName'))]",
          "dependsOn": [
            "[resourceId('Microsoft.Logic/workflows', parameters('logicAppName'))]"
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
                "category": "WorkflowRuntime",
                "enabled": true,
                "retentionPolicy": {
                  "days": 0,
                  "enabled": false
                }
              }
            ],
            "metrics": [
              {
                "timeGrain": "PT1M",
                "enabled": true,
                "retentionPolicy": {
                  "enabled": false,
                  "days": 0
                }
              }
            ]
          }
        }
      ],
      "dependsOn": []
    }
  ]
}

```

## <a name="compute-resource-template"></a>コンピューティング リソース テンプレート
コンピューティング リソース (Virtual Machine や Service Fabric など) で診断を有効にするには、次の手順を実行する必要があります。

1. VM のリソース定義に Azure Diagnostics の拡張機能を追加します。
2. パラメーターとしてストレージ アカウントおよびイベント ハブを指定します。
3. すべての XML 文字を正しくエスケープして、WADCfg XML ファイルの内容を XMLCfg プロパティに追加します。

> [!WARNING]
> この最後の手順は理解するのが難しい場合があります。 [こちらの記事](../../virtual-machines/extensions/diagnostics-template.md#diagnostics-configuration-variables) をご覧ください。
> 
> 

このプロセス全体とサンプルについては、 [こちらのドキュメント](../../virtual-machines/extensions/diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)をご覧ください。

## <a name="next-steps"></a>次の手順
* [Azure 診断ログの詳細を確認する](../../azure-monitor/platform/diagnostic-logs-overview.md)
* [Azure 診断ログを Event Hubs にストリーミングする](../../azure-monitor/platform/diagnostic-logs-stream-event-hubs.md)


