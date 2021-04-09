---
title: VM insights のゲストの正常性 (プレビュー) を有効にする
description: サブスクリプションで VM insights のゲストの正常性を有効にする方法と、VM をオンボードする方法について説明します。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/16/2020
ms.custom: references_regions
ms.openlocfilehash: 5d4ff622f69445880c0de8cb74dc1aeee422c89b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102052162"
---
# <a name="enable-vm-insights-guest-health-preview"></a>VM insights のゲストの正常性 (プレビュー) を有効にする
VM insights のゲストの正常性を使用すると、一定間隔でサンプリングされる一連のパフォーマンス測定値によって定義される、仮想マシンの正常性を表示できます。 この記事では、サブスクリプションでこの機能を有効にする方法と、仮想マシンごとにゲストの監視を有効にする方法について説明します。

## <a name="current-limitations"></a>現在の制限
VM insights のゲストの正常性には、パブリック プレビュー段階では次の制限があります。

- 現在、Azure 仮想マシンのみがサポートされています。 Azure Arc for servers は現在サポートされていません。


## <a name="supported-operating-systems"></a>サポートされるオペレーティング システム
仮想マシンでは、次のいずれかのオペレーティング システムを実行する必要があります。 

  - Ubuntu 16.04 LTS、Ubuntu 18.04 LTS
  - Windows Server 2012 またはそれ以降

## <a name="supported-regions"></a>サポートされているリージョン

仮想マシンは、次のいずれかのリージョンに配置されている必要があります。

- オーストラリア中部
- オーストラリア東部
- オーストラリア南東部
- カナダ中部
- インド中部
- 米国中部
- 東アジア
- 米国東部
- 米国東部 2
- 米国東部 2 EUAP
- フランス中部
- ドイツ中西部
- 東日本
- 韓国中部
- 米国中北部
- 北ヨーロッパ
- 米国中南部
- 南アフリカ北部
- 東南アジア
- スイス北部
- 英国南部
- 英国西部
- 米国中西部
- 西ヨーロッパ
- 米国西部
- 米国西部 2


Log Analytics ワークスペースは、次のいずれかのリージョンに配置されている必要があります。

- オーストラリア中部
- オーストラリア東部
- オーストラリア南東部
- カナダ中部
- カナダ インド
- 米国中部
- 東アジア
- 米国東部
- 米国東部 2
- 米国東部 2 EUAP
- フランス中部
- 東日本
- 米国中北部
- 北ヨーロッパ
- 米国中南部
- 東南アジア
- スイス北部
- 英国南部
- 西ヨーロッパ リージョン
- 米国西部
- 米国西部 2

## <a name="prerequisites"></a>前提条件

- VM insights に仮想マシンをオンボードする必要があります。
- オンボード手順を実行するユーザーには、仮想マシンとデータ収集ルールが配置されているサブスクリプションに対する共同作成者レベルの最小限のアクセス権が必要です。
- 次のセクションで説明されているように、必要な Azure リソース プロバイダーを登録する必要があります。

## <a name="register-required-azure-resource-providers"></a>必要な Azure リソース プロバイダーを登録する
VM insights のゲストの正常性を有効にするには、次の Azure リソース プロバイダーを対象のサブスクリプションに登録する必要があります。 

- Microsoft.WorkloadMonitor
- Microsoft.Insights

「[Azure リソース プロバイダーと種類](../../azure-resource-manager/management/resource-providers-and-types.md)」で説明されているように、リソース プロバイダーを登録するために使用可能な方法のいずれかを使用できます。 また、armclient、postman、または別の方法を使用する次のサンプル コマンドを使用して Azure Resource Manager の認証された呼び出しを行うこともできます。

```
POST https://management.azure.com/subscriptions/[subscriptionId]/providers/Microsoft.WorkloadMonitor/register?api-version=2019-10-01
POST https://management.azure.com/subscriptions/[subscriptionId]/providers/Microsoft.Insights/register?api-version=2019-10-01
```


## <a name="enable-a-virtual-machine-using-the-azure-portal"></a>Azure portal を使用して仮想マシンを有効にする
Azure portal で仮想マシンのゲストの正常性を有効にすると、必要な構成がすべて自動的に実行されます。 これには、必要なデータ収集ルールの作成、仮想マシンへのゲストの正常性拡張機能のインストール、およびデータ収集ルールとの関連付けの作成が含まれます。

VM insights の **[作業の開始]** ビューで、仮想マシンのアップグレード メッセージの横にあるリンクをクリックし、 **[アップグレード]** ボタンをクリックします。 複数の仮想マシンを選択して、それらをまとめてアップグレードすることもできます。

![仮想マシンで正常性機能を有効にする](media/vminsights-health-enable/enable-agent.png)


## <a name="enable-a-virtual-machine-using-resource-manager-template"></a>Azure Resource Manager テンプレートを使用して仮想マシンを有効にする
Azure Resource Manager を使用して仮想マシンを有効にするには、3 つの手順が必要です。

- データ収集ルールを作成する。
- 各仮想マシンにゲストの正常性拡張機能をインストールする。
- 仮想マシンとデータ収集ルールの間の関連付けを作成する。

### <a name="create-data-collection-rule-dcr"></a>データ収集ルール (DCR) を作成する

> [!NOTE]
> Azure portal を使用して仮想マシンを有効にすると、ここで説明されているデータ収集ルールが自動的に作成されます。 この場合、この手順を実行する必要はありません。

VM insights のゲストの正常性でのモニターの構成は、[データ収集ルール (DCR)](../agents/data-collection-rule-overview.md) に格納されます。 ゲストの正常性拡張機能を備えた各仮想マシンには、このルールとの関連付けが必要です。

> [!NOTE]
> 「[VM insights のゲストの正常性 (プレビュー) での監視を構成する](vminsights-health-configure.md)」の説明に従って、追加のデータ収集ルールを作成して、モニターの既定の構成を変更することができます。

このテンプレートには、次のパラメーターの値が必要です。

- **defaultHealthDataCollectionRuleName**:テンプレートで定義されている既定の名前を維持します。
- **destinationWorkspaceResourceId**:仮想マシンのデータ収集に使用される Log Analytics ワークスペースのリソース ID。
- **dataCollectionRuleLocation**:データ収集ルールのリージョン。 これは、Log Analytics ワークスペースのリージョンと一致している必要があります。


[Resource Manager テンプレートのデプロイ方法](../../azure-resource-manager/templates/deploy-powershell.md)のいずれかを使用して、テンプレートをデプロイします。 次の各コマンドは、PowerShell または Azure CLI を使用してテンプレートとパラメーター ファイルをデプロイします。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
New-AzResourceGroupDeployment -Name GuestHealthDataCollectionRule -ResourceGroupName my-resource-group -TemplateFile Health.DataCollectionRule.template.json -TemplateParameterFile Health.DataCollectionRule.template.parameters.json
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group create --name GuestHealthDataCollectionRule --resource-group my-resource-group --template-file Health.DataCollectionRule.template.json --parameters Health.DataCollectionRule.template.parameters.json
```

---

次の Resource Manager テンプレートで定義されているデータ収集ルールは、ゲストの正常性拡張機能を備えた仮想マシンのすべてのモニターを有効にします。 これには、モニターによって使用される各パフォーマンス カウンターのデータソースが含まれている必要があります。

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "defaultHealthDataCollectionRuleName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the data collection rule to create."
      },
      "defaultValue": "Microsoft-VMInsights-Health"
    },
    "destinationWorkspaceResourceId": {
      "type": "string",
      "metadata": {
        "description": "Specifies the Azure resource ID of the Log Analytics workspace to use to store virtual machine health data."
      }
    },
    "dataCollectionRuleLocation": {
      "type": "string",
      "metadata": {
        "description": "The location code in which the data collection rule should be deployed. Examples: eastus, westeurope, etc"
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Insights/dataCollectionRules",
      "name": "[parameters('defaultHealthDataCollectionRuleName')]",
      "location": "[parameters('dataCollectionRuleLocation')]",
      "apiVersion": "2019-11-01-preview",
      "properties": {
        "description": "Data collection rule for VM Insights health.",
        "dataSources": {
          "performanceCounters": [
              {
                  "name": "VMHealthPerfCounters",
                  "streams": [ "Microsoft-Perf" ],
                  "scheduledTransferPeriod": "PT1M",
                  "samplingFrequencyInSeconds": 60,
                  "counterSpecifiers": [
                      "\\LogicalDisk(*)\\% Free Space",
                      "\\Memory\\Available Bytes",
                      "\\Processor(_Total)\\% Processor Time"
                  ]
              }
          ],
          "extensions": [
            {
              "name": "Microsoft-VMInsights-Health",
              "streams": [
                "Microsoft-HealthStateChange"
              ],
              "extensionName": "HealthExtension",
              "extensionSettings": {
                "schemaVersion": "1.0",
                "contentVersion": "",
                "healthRuleOverrides": [
                  {
                    "scopes": [ "*" ],
                    "monitors": ["root"],
                    "alertConfiguration": {
                      "isEnabled": true
                    }
                  }
                ]
              },
              "inputDataSources": [
                  "VMHealthPerfCounters"
              ]

            }
          ]
        },
        "destinations": {
          "logAnalytics": [
            {
              "workspaceResourceId": "[parameters('destinationWorkspaceResourceId')]",
              "name": "Microsoft-HealthStateChange-Dest"
            }
          ]
        },                  
        "dataFlows": [
          {
            "streams": [
              "Microsoft-HealthStateChange"
            ],
            "destinations": [
              "Microsoft-HealthStateChange-Dest"
            ]
          }
        ]
      }
    }
  ]
}
```

### <a name="sample-parameter-file"></a>サンプルのパラメーター ファイル

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "destinationWorkspaceResourceId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace"
      },
      "dataCollectionRuleLocation": {
        "value": "eastus"
      }
  }
}
```



### <a name="install-guest-health-extension-and-associate-with-data-collection-rule"></a>ゲストの正常性拡張機能をインストールし、データ収集ルールに関連付ける
次の Resource Manager テンプレートを使用して、ゲストの正常性について仮想マシンを有効にします。 これにより、ゲストの正常性拡張機能がインストールされ、データ収集ルールとの関連付けが作成されます。 [Resource Manager テンプレートのどのデプロイ方法](../../azure-resource-manager/templates/deploy-powershell.md)を使用しても、このテンプレートをデプロイすることができます。


たとえば、PowerShell または Azure CLI を使用してリソース グループにテンプレートとパラメーター ファイルをデプロイする場合は、次のコマンドを実行します。


# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
New-AzResourceGroupDeployment -Name GuestHealthDeployment -ResourceGroupName my-resource-group -TemplateFile azure-monitor-deploy.json -TemplateParameterFile azure-monitor-deploy.parameters.json
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group create --name GuestHealthDeployment --resource-group my-resource-group --template-file Health.VirtualMachine.template.json --parameters Health.VirtualMachine.template.parameters.json
```

---

### <a name="template-file"></a>テンプレート ファイル

``` json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "virtualMachineName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the virtual machine."
      }
    },
    "virtualMachineLocation": {
      "type": "string",
      "metadata": {
        "description": "The location code of the virtual machine region (location). Examples: eastus, westeurope, etc"
      }
    },
    "virtualMachineOsType": {
      "type": "string",
      "metadata": {
        "description": "Specifies operating system type of the target virtual machine."
      },
      "allowedValues": ["windows", "linux"]
    },
    "dataCollectionRuleAssociationName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the data collection rule association to create."
      },
      "defaultValue": "VM-Health-Dcr-Association"
    },
    "healthDataCollectionRuleResourceId": {
      "type": "string",
      "metadata": {
        "description": "Specifies resource id of Azure Monitor Data Collection Rule for virtual machine health data."
      }
    }
  },
  "variables": {
    "healthExtensionProperties": {
      "windows": {
        "publisher": "Microsoft.Azure.Monitor.VirtualMachines.GuestHealth",
        "type": "GuestHealthWindowsAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true
      },
      "linux": {
        "publisher": "Microsoft.Azure.Monitor.VirtualMachines.GuestHealth",
        "type": "GuestHealthLinuxAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true
      }
    },
    "azureMonitorAgentExtensionProperties": {
      "windows": {
        "publisher": "Microsoft.Azure.Monitor", 
        "type": "AzureMonitorWindowsAgent", 
        "typeHandlerVersion": "1.0", 
        "autoUpgradeMinorVersion": false 
      },
      "linux": {
        "publisher": "Microsoft.Azure.Monitor", 
        "type": "AzureMonitorLinuxAgent", 
        "typeHandlerVersion": "1.5", 
        "autoUpgradeMinorVersion": false 
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[parameters('virtualMachineName')]",
      "location": "[parameters('virtualMachineLocation')]",
      "apiVersion": "2017-03-30",
      "identity": {
        "type": "SystemAssigned"
      }
    },
    {
      "type": "Microsoft.Compute/virtualMachines/providers/dataCollectionRuleAssociations",
      "name": "[concat(parameters('virtualMachineName'), '/Microsoft.Insights/', parameters('dataCollectionRuleAssociationName'))]",
      "apiVersion": "2019-11-01-preview",
      "properties": {
        "description": "Association of data collection rule for VM Insights Health.",
        "dataCollectionRuleId": "[parameters('healthDataCollectionRuleResourceId')]"
      }
    },
    { 
      "type": "Microsoft.Compute/virtualMachines/extensions", 
      "apiVersion": "2019-12-01", 
      "name": "[concat(parameters('virtualMachineName'), '/', variables('azureMonitorAgentExtensionProperties')[parameters('virtualMachineOsType')].type)]",
      "location": "[parameters('virtualMachineLocation')]",
      "dependsOn": [
        "[resourceId('Microsoft.Compute/virtualMachines/providers/dataCollectionRuleAssociations', parameters('virtualMachineName'), 'Microsoft.Insights', parameters('dataCollectionRuleAssociationName'))]"
      ], 
      "properties": "[variables('azureMonitorAgentExtensionProperties')[parameters('virtualMachineOsType')]]"
    },
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat(parameters('virtualMachineName'), '/', variables('healthExtensionProperties')[parameters('virtualMachineOsType')].type)]",
      "location": "[parameters('virtualMachineLocation')]",
      "apiVersion": "2018-06-01",
      "dependsOn": [ 
        "[resourceId('Microsoft.Compute/virtualMachines/extensions', parameters('virtualMachineName'), variables('azureMonitorAgentExtensionProperties')[parameters('virtualMachineOsType')].type)]",
        "[resourceId('Microsoft.Compute/virtualMachines/providers/dataCollectionRuleAssociations', parameters('virtualMachineName'), 'Microsoft.Insights', parameters('dataCollectionRuleAssociationName'))]"
      ],    
      "properties": "[variables('healthExtensionProperties')[parameters('virtualMachineOsType')]]"
    }               
  ]
}
```

### <a name="sample-parameter-file"></a>サンプルのパラメーター ファイル

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "virtualMachineName": {
        "value": "myvm"
      },
      "virtualMachineLocation": {
        "value": "eastus"
      },
      "virtualMachineOsType": {
        "value": "linux"
      },
      "healthDataCollectionRuleResourceId": {
        "value": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/my-resource-group/providers/Microsoft.Insights/dataCollectionRules/Microsoft-VMInsights-Health"
      }
  }
}
```

## <a name="next-steps"></a>次のステップ

- [VM insights で有効化されるモニターをカスタマイズする](vminsights-health-configure.md)