---
title: データ収集ルールのための Resource Manager テンプレート サンプル
description: Azure Monitor でデータ収集ルールと仮想マシンとの間に関連付けを作成するためのサンプル Azure Resource Manager テンプレート。
ms.subservice: logs
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 11/17/2020
ms.openlocfilehash: bff13c24e0b5818b352e76df1d8ecd8251a5f12c
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430228"
---
# <a name="resource-manager-template-samples-for-data-collection-rules-in-azure-monitor"></a>Azure Monitor のデータ収集ルールのための Resource Manager テンプレート サンプル
この記事には、Azure Monitor で仮想マシンに対して [Log Analytics エージェント](../platform/log-analytics-agent.md)と[診断拡張機能](../platform/diagnostics-extension-overview.md)をデプロイして構成するためのサンプルの [Azure Resource Manager テンプレート](../../azure-resource-manager/templates/template-syntax.md)が含まれています。 各サンプルには、テンプレート ファイルと、テンプレートに指定するサンプル値を含むパラメーター ファイルが含まれています。

[!INCLUDE [azure-monitor-samples](../../../includes/azure-monitor-resource-manager-samples.md)]


## <a name="create-association-with-azure-vm"></a>Azure VM との関連付けを作成する

次のサンプルによって、Azure 仮想マシンとデータ収集ルールとの間に関連付けが作成されます。

### <a name="template-file"></a>テンプレート ファイル

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string",
            "metadata": {
                "description": "Name of the virtual machine."
            }
        },
        "associationName": {
            "type": "string",
            "metadata": {
                "description": "Name of the association."
            }
        },
        "dataCollectionRuleId": {
            "type": "string",
            "metadata": {
                "description": "Resource ID of the data collection rule."
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Compute/virtualMachines/providers/dataCollectionRuleAssociations",
            "name": "[concat(parameters('vmName'),'/microsoft.insights/', parameters('associationName'))]",
            "apiVersion": "2019-11-01-preview",
            "properties": {
                "description": "Association of data collection rule. Deleting this association will break the data collection for this virtual machine.",
                "dataCollectionRuleId": "[parameters('dataCollectionRuleId')]"
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
      "vmName": {
        "value": "my-windows-vm"
      },
      "location": {
        "value": "eastus"
      }
  }
}
```

## <a name="create-association-with-azure-arc"></a>Azure Arc との関連付けを作成する

次のサンプルでは、Microsoft Azure 仮想マシンに Azure Monitor エージェントをインストールします。 関連付けは、Azure Arc 対応サーバー マシンとデータ収集ルールとの間で作成されます。

### <a name="template-file"></a>テンプレート ファイル

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string",
            "metadata": {
                "description": "Name of the virtual machine."
            }
        },
        "associationName": {
            "type": "string",
            "metadata": {
                "description": "Name of the association."
            }
        },
        "dataCollectionRuleId": {
            "type": "string",
            "metadata": {
                "description": "Resource ID of the data collection rule."
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.HybridCompute/machines/providers/dataCollectionRuleAssociations",
            "name": "[concat(parameters('machineName'),'/microsoft.insights/', parameters('associationName'))]",
            "apiVersion": "2019-11-01-preview",
            "properties": {
                "description": "Association of data collection rule. Deleting this association will break the data collection for this Arc server.",
                "dataCollectionRuleId": "[parameters('dataCollectionRuleId')]"
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
      "vmName": {
        "value": "my-windows-vm"
      },
      "location": {
        "value": "eastus"
      }
  }
}
```


## <a name="next-steps"></a>次のステップ

* [Azure Monitor の他のサンプル テンプレートを入手します](resource-manager-samples.md)。
* [Log Analytics の詳細情報](../platform/log-analytics-agent.md)。
* [診断拡張機能の詳細情報](../platform/diagnostics-extension-overview.md)。
