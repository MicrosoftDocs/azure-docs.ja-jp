---
title: "Resource Manager テンプレートを使用して診断設定を自動的に有効にする | Microsoft Docs"
description: "Resource Manager テンプレートを使用して、診断ログを Event Hubs にストリーミングしたり、ストレージ アカウントに保存したりできるようにするための診断設定を作成する方法について説明します。"
author: johnkemnetz
manager: rboucher
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: a8a88a8c-4a48-4df6-8f7e-d90634d39c57
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 2/14/2017
ms.author: johnkem
translationtype: Human Translation
ms.sourcegitcommit: f4e7b1f2ac7f10748473605eacee71bf0cd538e6
ms.openlocfilehash: 2b28045c3ec32a703c62aeb509777750342ffbb3


---
# <a name="automatically-enable-diagnostic-settings-at-resource-creation-using-a-resource-manager-template"></a>Resource Manager テンプレートを使用してリソースの作成時に診断設定を自動的に有効にする
この記事では、 [Azure Resource Manager テンプレート](../azure-resource-manager/resource-group-authoring-templates.md) を使用して、リソースの作成時にリソースの診断設定を構成する方法について説明します。 これにより、リソースの作成時に、診断ログとメトリックの Event Hubs へのストリーミング、ストレージ アカウントへのアーカイブ、または Log Analytics への送信を自動的に開始できます。

Resource Manager テンプレートを使用して診断ログを有効にする方法は、リソースの種類によって異なります。

* **非コンピューティング** リソース (ネットワーク セキュリティ グループ、Logic Apps、Automation など) では、 [こちらの記事で説明する診断設定](monitoring-overview-of-diagnostic-logs.md#diagnostic-settings)を使用します。
* **コンピューティング** (WAD/LAD ベースの) リソースでは、 [こちらの記事で説明する WAD/LAD 構成ファイル](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md)を使用します。

この記事では、いずれかの方法を使用して診断を構成する方法について説明します。

基本的な手順は次のとおりです。

1. リソースを作成し、診断を有効にする方法を記述した JSON ファイルとしてテンプレートを作成します。
2. [任意のデプロイ方法を使用してテンプレートをデプロイ](../azure-resource-manager/resource-group-template-deploy.md)します。

生成する必要がある、非コンピューティング リソースおよびコンピューティング リソースのテンプレート JSON ファイルの例を以下に示します。

## <a name="non-compute-resource-template"></a>非コンピューティング リソース テンプレート
非コンピューティング リソースの場合、次の&2; つの手順を実行する必要があります。

1. (ストレージ アカウントへの診断ログのアーカイブ、Event Hubs へのログのストリーミング、Log Analytics へのログの送信を有効にするために) パラメーター BLOB に、ストレージ アカウント名、Service Bus 規則 ID、OMS Log Analytics ワークスペース ID のパラメーターを追加します。
   
    ```json
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Storage Account in which Diagnostic Logs should be saved."
      }
    },
    "serviceBusRuleId": {
      "type": "string",
      "metadata": {
        "description": "Service Bus Rule Id for the Service Bus Namespace in which the Event Hub should be created or streamed to."
      }
    },
    "workspaceId":{
      "type": "string",
      "metadata": {
        "description": "Log Analytics workspace ID for the Log Analytics workspace to which logs will be sent."
      }
    }
    ```
2. 診断ログを有効にするリソースのリソース配列に、 `[resource namespace]/providers/diagnosticSettings`型のリソースを追加します。
   
    ```json
    "resources": [
      {
        "type": "providers/diagnosticSettings",
        "name": "Microsoft.Insights/service",
        "dependsOn": [
          "[/*resource Id for which Diagnostic Logs will be enabled>*/]"
        ],
        "apiVersion": "2015-07-01",
        "properties": {
          "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
          "serviceBusRuleId": "[parameters('serviceBusRuleId')]",
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
    ]
    ```

診断設定のプロパティ BLOB は、 [こちらの記事で説明する形式](https://msdn.microsoft.com/library/azure/dn931931.aspx)に従います。 [リソースが Azure Monitor メトリックをサポートしてる](monitoring-supported-metrics.md)場合、`metrics` プロパティを追加して、リソース メトリックをこれらの同じ出力に送信することもできます。

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
      "defaultValue": "http://azure.microsoft.com/en-us/status/feed/"
    },
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Storage Account in which Diagnostic Logs should be saved."
      }
    },
    "serviceBusRuleId": {
      "type": "string",
      "metadata": {
        "description": "Service Bus Rule Id for the Service Bus Namespace in which the Event Hub should be created or streamed to."
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
          "$schema": "http://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
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
          "name": "Microsoft.Insights/service",
          "dependsOn": [
            "[resourceId('Microsoft.Logic/workflows', parameters('logicAppName'))]"
          ],
          "apiVersion": "2015-07-01",
          "properties": {
            "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
            "serviceBusRuleId": "[parameters('serviceBusRuleId')]",
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

1. VM のリソース定義に Azure 診断の拡張機能を追加します。
2. パラメーターとしてストレージ アカウントおよびイベント ハブを指定します。
3. すべての XML 文字を正しくエスケープして、WADCfg XML ファイルの内容を XMLCfg プロパティに追加します。

> [!WARNING]
> この最後の手順は理解するのが難しい場合があります。 [こちらの記事](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md#diagnostics-configuration-variables) をご覧ください。
> 
> 

このプロセス全体とサンプルについては、 [こちらのドキュメント](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)をご覧ください。

## <a name="next-steps"></a>次のステップ
* [Azure 診断ログの詳細を確認する](monitoring-overview-of-diagnostic-logs.md)
* [Azure 診断ログを Event Hubs にストリーミングする](monitoring-stream-diagnostic-logs-to-event-hubs.md)




<!--HONumber=Feb17_HO3-->


