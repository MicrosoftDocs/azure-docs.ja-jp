---
title: Azure Resource Manager テンプレートを使用して Log Analytics ワークスペースの作成と構成を行う | Microsoft Docs
description: Azure Resource Manager テンプレートを使用して、Log Analytics ワークスペースの作成と構成を実行できます。
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: d21ca1b0-847d-4716-bb30-2a8c02a606aa
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: magoedte
ms.openlocfilehash: 39dbb504603544a468907d87d236338cb95e39a3
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67441631"
---
# <a name="manage-log-analytics-workspace-using-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートを使用して Log Analytics ワークスペースを管理する

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[Azure Resource Manager テンプレート](../../azure-resource-manager/resource-group-authoring-templates.md)を使用して、Azure Monitor の Log Analytics ワークスペースの作成と構成を実行できます。 テンプレートを使用して、次のようなタスクを実行できます。

* 価格レベルの設定を含むワークスペースの作成 
* ソリューションの追加
* 保存された検索の作成
* コンピューター グループの作成
* Windows エージェントがインストールされているコンピューターでの IIS ログのコレクションの有効化
* Linux および Windows コンピューターからのパフォーマンス カウンターの収集
* Linux コンピューターの syslog からのイベントの収集 
* Windows イベント ログからのイベントの収集
* Azure 仮想マシンへのログ分析エージェントの追加
* Azure Diagnostics を使用して収集されたデータをログ分析でインデックスするための構成

この記事では、テンプレートで実行できる構成の一部を示すテンプレート サンプルを紹介しています。

## <a name="api-versions"></a>API のバージョン
次の表は、この例で使用されているリソースの API バージョンの一覧です。

| Resource | リソースの種類 | API バージョン |
|:---|:---|:---|
| ワークスペース   | workspaces    | 2017-03-15-preview |
| Search      | savedSearches | 2015-03-20 |
| データ ソース | datasources   | 2015-11-01-preview |
| 解決策    | solutions     | 2015-11-01-preview |

## <a name="create-a-log-analytics-workspace"></a>Log Analytics ワークスペースの作成
次の例では、ローカル マシンからテンプレートを使用してワークスペースを作成します。 JSON テンプレートは、ワークスペースの名前の入力だけをユーザーに求め、環境の標準構成として使用される可能性のある他のパラメーターには既定値を指定するように構成されています。  

以下のパラメーターには、既定値が設定されます。

* 場所 - 既定値は米国東部
* SKU - 既定値は、2018 年 4 月の価格モデルでリリースされた新しい 1 GB あたりの価格レベル

> [!NOTE]
>新しい 2018 年 4 月の価格モデルを選択したサブスクリプションで Log Analytics ワークスペースを作成または構成する場合、有効な Log Analytics 価格レベルは **PerGB2018** のみです。  
>[2018 年 4 月より前の価格モデル](https://docs.microsoft.com/azure/azure-monitor/platform/usage-estimated-costs#new-pricing-model)のサブスクリプションがある場合、**スタンドアロン**価格レベルを指定できます。これは、2018 年 4 月の価格モデルのサブスクリプションと、新価格でのサブスクリプションの両方に対して行えます。 新しい価格モデルを採用したサブスクリプション内のワークスペースの場合、価格レベルは **PerGB2018** に設定されます。 

### <a name="create-and-deploy-template"></a>テンプレートの作成とデプロイ

1. 以下の JSON 構文をコピーして、ファイルに貼り付けます。

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "String",
            "metadata": {
              "description": "Specifies the name of the workspace."
            }
        },
        "location": {
            "type": "String",
            "allowedValues": [
              "eastus",
              "westus"
            ],
            "defaultValue": "eastus",
            "metadata": {
              "description": "Specifies the location in which to create the workspace."
            }
        },
        "sku": {
            "type": "String",
            "allowedValues": [
              "Standalone",
              "PerNode",
              "PerGB2018"
            ],
            "defaultValue": "PerGB2018",
            "metadata": {
            "description": "Specifies the service tier of the workspace: Standalone, PerNode, Per-GB"
        }
          }
    },
    "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2015-11-01-preview",
            "location": "[parameters('location')]",
            "properties": {
                "sku": {
                    "Name": "[parameters('sku')]"
                },
                "features": {
                    "searchVersion": 1
                }
            }
          }
       ]
    }
    ```
2. 要件に合わせてテンプレートを編集します。  どのプロパティと値がサポートされているかを調べるには、[Microsoft.OperationalInsights/workspaces テンプレート](https://docs.microsoft.com/azure/templates/microsoft.operationalinsights/workspaces)のリファレンスを参照してください。 
3. このファイルを **deploylaworkspacetemplate.json** としてローカル フォルダーに保存します。
4. これでこのテンプレートをデプロイする準備が整いました。 PowerShell またはコマンド ラインを使用して、ワークスペースを作成します。

   * PowerShell の場合は、テンプレートがあるフォルダーから以下のコマンドを使用します。
   
        ```powershell
        New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deploylaworkspacetemplate.json
        ```

   * コマンド ラインの場合は、テンプレートがあるフォルダーから以下のコマンドを使用します。

        ```cmd
        azure config mode arm
        azure group deployment create <my-resource-group> <my-deployment-name> --TemplateFile deploylaworkspacetemplate.json
        ```

デプロイが完了するまでに数分かかる場合があります。 完了すると、次のような結果を含むメッセージが表示されます。<br><br> ![デプロイ完了時の結果の例](./media/template-workspace-configuration/template-output-01.png)

## <a name="configure-a-log-analytics-workspace"></a>Log Analytics ワークスペースの構成
次のサンプル テンプレートは、以下のタスクの実行方法を示しています。

1. ソリューションをワークスペースに追加する
2. 保存された検索の作成
3. コンピューター グループの作成
4. Windows エージェントがインストールされているコンピューターでの IIS ログのコレクションの有効化
5. Linux コンピューターからの Logical Disk パフォーマンス カウンター (% Used Inodes、Free Megabytes、% Used Space、Disk Transfers/sec、Disk Reads/sec、Disk Writes/sec) の収集
6. Linux コンピューターからの syslog イベントの収集
7. Windows コンピューターのアプリケーション イベント ログからのエラーおよび警告のイベントの収集
8. Windows コンピューターからの Memory Available Mbytes パフォーマンス カウンターの収集
9. Azure Diagnostics によってストレージ アカウントに書き込まれた IIS ログとWindows イベント ログの収集

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "type": "string",
      "metadata": {
        "description": "Workspace name"
      }
    },
    "serviceTier": {
      "type": "string",
      "allowedValues": [
        "Free",
        "Standalone",
        "PerNode",
        "PerGB2018"
      ],
      "defaultValue": "PerGB2018",
      "metadata": {
        "description": "Pricing tier: PerGB2018 or legacy tiers (Free, Standalone or PerNode) which are not available to all customers"
    }
      },
    "dataRetention": {
      "type": "int",
      "defaultValue": 30,
      "minValue": 7,
      "maxValue": 730,
      "metadata": {
        "description": "Number of days of retention. Workspaces in the legacy Free pricing tier can only have 7 days."
      }
    },
    {
    "immediatePurgeDataOn30Days": {
      "type": "bool",
      "metadata": {
        "description": "If set to true when changing retention to 30 days, older data will be immediately deleted. This only applies when retention is being set to 30 days."
      }
    },
    "location": {
      "type": "string",
      "allowedValues": [
        "East US",
        "West Europe",
        "Southeast Asia",
        "Australia Southeast"
      ]
    },
    "applicationDiagnosticsStorageAccountName": {
        "type": "string",
        "metadata": {
          "description": "Name of the storage account with Azure diagnostics output"
        }
    },
    "applicationDiagnosticsStorageAccountResourceGroup": {
        "type": "string",
        "metadata": {
          "description": "The resource group name containing the storage account with Azure diagnostics output"
        }
    }
  },
  "variables": {
    "Updates": {
      "Name": "[Concat('Updates', '(', parameters('workspaceName'), ')')]",
      "GalleryName": "Updates"
    },
    "AntiMalware": {
      "Name": "[concat('AntiMalware', '(', parameters('workspaceName'), ')')]",
      "GalleryName": "AntiMalware"
    },
    "SQLAssessment": {
      "Name": "[Concat('SQLAssessment', '(', parameters('workspaceName'), ')')]",
      "GalleryName": "SQLAssessment"
    },
    "diagnosticsStorageAccount": "[resourceId(parameters('applicationDiagnosticsStorageAccountResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName'))]"
  },
  "resources": [
    {
      "apiVersion": "2015-11-01-preview",
      "type": "Microsoft.OperationalInsights/workspaces",
      "name": "[parameters('workspaceName')]",
      "location": "[parameters('location')]",
      "properties": {
        "sku": {
          "Name": "[parameters('serviceTier')]"
        },
    "retentionInDays": "[parameters('dataRetention')]"
      },
      "resources": [
        {
          "apiVersion": "2015-03-20",
          "name": "VMSS Queries2",
          "type": "savedSearches",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "properties": {
            "Category": "VMSS",
            "ETag": "*",
            "DisplayName": "VMSS Instance Count",
            "Query": "Event | where Source == \"ServiceFabricNodeBootstrapAgent\" | summarize AggregatedValue = count() by Computer",
            "Version": 1
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleWindowsEvent1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "WindowsEvent",
          "properties": {
            "eventLogName": "Application",
            "eventTypes": [
              {
                "eventType": "Error"
              },
              {
                "eventType": "Warning"
              }
            ]
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleWindowsPerfCounter1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "WindowsPerformanceCounter",
          "properties": {
            "objectName": "Memory",
            "instanceName": "*",
            "intervalSeconds": 10,
            "counterName": "Available MBytes"
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleIISLog1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "IISLogs",
          "properties": {
            "state": "OnPremiseEnabled"
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleSyslog1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "LinuxSyslog",
          "properties": {
            "syslogName": "kern",
            "syslogSeverities": [
              {
                "severity": "emerg"
              },
              {
                "severity": "alert"
              },
              {
                "severity": "crit"
              },
              {
                "severity": "err"
              },
              {
                "severity": "warning"
              }
            ]
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleSyslogCollection1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "LinuxSyslogCollection",
          "properties": {
            "state": "Enabled"
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleLinuxPerf1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "LinuxPerformanceObject",
          "properties": {
            "performanceCounters": [
              {
                "counterName": "% Used Inodes"
              },
              {
                "counterName": "Free Megabytes"
              },
              {
                "counterName": "% Used Space"
              },
              {
                "counterName": "Disk Transfers/sec"
              },
              {
                "counterName": "Disk Reads/sec"
              },
              {
                "counterName": "Disk Writes/sec"
              }
            ],
            "objectName": "Logical Disk",
            "instanceName": "*",
            "intervalSeconds": 10
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleLinuxPerfCollection1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "LinuxPerformanceCollection",
          "properties": {
            "state": "Enabled"
          }
        },
        {
          "apiVersion": "2015-03-20",
          "name": "[concat(parameters('applicationDiagnosticsStorageAccountName'),parameters('workspaceName'))]",
          "type": "storageinsightconfigs",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "properties": {
            "containers": [ 
              "wad-iis-logfiles" 
            ],
            "tables": [
              "WADWindowsEventLogsTable"
            ],
            "storageAccount": {
              "id": "[variables('diagnosticsStorageAccount')]",
              "key": "[listKeys(variables('diagnosticsStorageAccount'),'2015-06-15').key1]"
            }
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "location": "[parameters('location')]",
          "name": "[variables('Updates').Name]",
          "type": "Microsoft.OperationsManagement/solutions",
          "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationsManagement/solutions/', variables('Updates').Name)]",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          },
          "plan": {
            "name": "[variables('Updates').Name]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('Updates').GalleryName)]",
            "promotionCode": ""
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "location": "[parameters('location')]",
          "name": "[variables('AntiMalware').Name]",
          "type": "Microsoft.OperationsManagement/solutions",
          "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationsManagement/solutions/', variables('AntiMalware').Name)]",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          },
          "plan": {
            "name": "[variables('AntiMalware').Name]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('AntiMalware').GalleryName)]",
            "promotionCode": ""
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "location": "[parameters('location')]",
          "name": "[variables('SQLAssessment').Name]",
          "type": "Microsoft.OperationsManagement/solutions",
          "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationsManagement/solutions/', variables('SQLAssessment').Name)]",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          },
          "plan": {
            "name": "[variables('SQLAssessment').Name]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('SQLAssessment').GalleryName)]",
            "promotionCode": ""
          }
        }
      ]
    }
  ],
  "outputs": {
    "workspaceName": {
      "type": "string",
      "value": "[parameters('workspaceName')]"
    },
    "provisioningState": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').provisioningState]"
    },
    "source": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').source]"
    },
    "customerId": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').customerId]"
    },
    "pricingTier": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').sku.name]"
    },
    "retentionInDays": {
      "type": "int",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').retentionInDays]"
    },
    "portalUrl": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').portalUrl]"
    }
  }
}

```
### <a name="deploying-the-sample-template"></a>サンプル テンプレートのデプロイ
上記のサンプル テンプレートをデプロイするには:

1. 付属のサンプルをファイルに保存します (例: `azuredeploy.json`)。 
2. 必要な構成になるようにテンプレートを編集します。
3. PowerShell またはコマンド ラインを使用して、テンプレートをデプロイします。

#### <a name="powershell"></a>PowerShell
```powershell
New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile azuredeploy.json
```

#### <a name="command-line"></a>コマンド ライン
```cmd
azure config mode arm
azure group deployment create <my-resource-group> <my-deployment-name> --TemplateFile azuredeploy.json
```

## <a name="example-resource-manager-templates"></a>Azure Resource Manager のサンプル テンプレート
Azure クイックスタート テンプレート ギャラリーに、Log Analytics 用のさまざまなテンプレートが用意されています。

* [Windows を実行中の仮想マシンを Log Analytics VM 拡張機能付きでデプロイする](https://azure.microsoft.com/documentation/templates/201-oms-extension-windows-vm/)
* [Linux を実行中の仮想マシンを Log Analytics VM 拡張機能付きでデプロイする](https://azure.microsoft.com/documentation/templates/201-oms-extension-ubuntu-vm/)
* [Azure Site Recovery を既存の Log Analytics ワークスペースを使用して監視する](https://azure.microsoft.com/documentation/templates/asr-oms-monitoring/)
* [Azure Web Apps を既存の Log Analytics ワークスペースを使用して監視する](https://azure.microsoft.com/documentation/templates/101-webappazure-oms-monitoring/)
* [既存のストレージ アカウントの Log Analytics への追加](https://azure.microsoft.com/resources/templates/oms-existing-storage-account/)

## <a name="next-steps"></a>次の手順
* [Resource Manager テンプレートを使用して Windows エージェントを Azure VM にデプロイします](../../virtual-machines/extensions/oms-windows.md)。
* [Resource Manager テンプレートを使用して Linux エージェントを Azure VM にデプロイします](../../virtual-machines/extensions/oms-linux.md)。
