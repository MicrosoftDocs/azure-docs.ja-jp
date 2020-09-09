---
title: Azure Monitor for VMs 用の Resource Manager テンプレートのサンプル
description: Azure Monitor for VMs をデプロイおよび構成するための Azure Resource Manager テンプレートのサンプル。
ms.subservice: logs
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 05/18/2020
ms.openlocfilehash: 3bd14b1caa9ff905a585b12b7e3887d3a7a81d58
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/26/2020
ms.locfileid: "83853228"
---
# <a name="resource-manager-template-samples-for-azure-monitor-for-vms"></a>Azure Monitor for VMs 用の Resource Manager テンプレートのサンプル
この記事には、仮想マシン上で Azure Monitor for VMs を有効にするためのサンプルの [Azure Resource Manager テンプレート](../../azure-resource-manager/templates/template-syntax.md)が含まれています。 各サンプルには、テンプレート ファイルと、テンプレートに指定するサンプル値を含むパラメーター ファイルが含まれています。

[!INCLUDE [azure-monitor-samples](../../../includes/azure-monitor-resource-manager-samples.md)]


## <a name="configure-workspace"></a>ワークスペースの構成
次のサンプルでは、Log Analytics ワークスペースに対して Azure Monitor for VMs を有効にします。


### <a name="template-file"></a>テンプレート ファイル

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceResourceId": {
            "type": "String",
            "metadata": {
                "description": "Resource ID of the workspace."
            }
        },
        "workspaceLocation": {
            "type": "String",
            "metadata": {
                "description": "Location of the workspace."
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2017-05-10",
            "name": "VMISolutionDeployment",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "resources": [
                        {
                            "apiVersion": "2015-11-01-preview",
                            "type": "Microsoft.OperationsManagement/solutions",
                            "location": "[parameters('workspaceLocation')]",
                            "name": "[concat('VMInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                            "properties": {
                                "workspaceResourceId": "[parameters('WorkspaceResourceId')]"
                            },
                            "plan": {
                                "name": "[concat('VMInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                                "product": "[concat('OMSGallery/', 'VMInsights')]",
                                "promotionCode": "",
                                "publisher": "Microsoft"
                            }
                        }
                    ]
                }
            },
            "subscriptionId": "[split(parameters('workspaceResourceId'),'/')[2]]",
            "resourceGroup": "[split(parameters('workspaceResourceId'),'/')[4]]"
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
        "workspaceResourceId": {
            "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace"
        },
        "workspaceLocation": {
            "value": "eastus"
        }
    }
}
```


## <a name="onboard-an-azure-virtual-machine"></a>Azure 仮想マシンをオンボードする
次のサンプルでは、Azure Monitor for VMs に Azure 仮想マシンを追加します。


### <a name="template-file"></a>テンプレート ファイル

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "VmResourceId": {
            "type": "String",
            "metadata": {
                "description": "VM Resource ID."
            }
        },
        "VmLocation": {
            "type": "String",
            "metadata": {
                "description": "The Virtual Machine Location."
            }
        },
        "osType": {
            "type": "String",
            "metadata": {
                "description": "OS Type, Example: Linux / Windows"
            }
        },
        "WorkspaceResourceId": {
            "type": "String",
            "metadata": {
                "description": "Workspace Resource ID."
            }
        }
    },
    "variables": {
        "VmName": "[split(parameters('VmResourceId'),'/')[8]]",
        "DaExtensionName": "[if(equals(toLower(parameters('osType')), 'windows'), 'DependencyAgentWindows', 'DependencyAgentLinux')]",
        "DaExtensionType": "[if(equals(toLower(parameters('osType')), 'windows'), 'DependencyAgentWindows', 'DependencyAgentLinux')]",
        "DaExtensionVersion": "9.5",
        "MmaExtensionName": "[if(equals(toLower(parameters('osType')), 'windows'), 'MMAExtension', 'OMSExtension')]",
        "MmaExtensionType": "[if(equals(toLower(parameters('osType')), 'windows'), 'MicrosoftMonitoringAgent', 'OmsAgentForLinux')]",
        "MmaExtensionVersion": "[if(equals(toLower(parameters('osType')), 'windows'), '1.0', '1.4')]"
    },
    "resources": [
        {
            "type": "Microsoft.Compute/virtualMachines",
            "apiVersion": "2018-10-01",
            "name": "[variables('VmName')]",
            "location": "[parameters('VmLocation')]",
            "resources": [
                {
                    "type": "extensions",
                    "apiVersion": "2018-10-01",
                    "name": "[variables('DaExtensionName')]",
                    "location": "[parameters('VmLocation')]",
                    "dependsOn": [
                        "[concat('Microsoft.Compute/VirtualMachines/', variables('VmName'))]"
                    ],
                    "properties": {
                        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
                        "type": "[variables('DaExtensionType')]",
                        "typeHandlerVersion": "[variables('DaExtensionVersion')]",
                        "autoUpgradeMinorVersion": true
                    }
                },
                {
                    "type": "extensions",
                    "apiVersion": "2018-10-01",
                    "name": "[variables('MmaExtensionName')]",
                    "location": "[parameters('VmLocation')]",
                    "dependsOn": [
                        "[concat('Microsoft.Compute/VirtualMachines/', variables('VmName'))]"
                    ],
                    "properties": {
                        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                        "type": "[variables('MmaExtensionType')]",
                        "typeHandlerVersion": "[variables('MmaExtensionVersion')]",
                        "autoUpgradeMinorVersion": "true",
                        "settings": {
                            "workspaceId": "[reference(parameters('WorkspaceResourceId'), '2015-03-20').customerId]",
                            "azureResourceId": "[parameters('VmResourceId')]",
                            "stopOnMultipleConnections": "true"
                        },
                        "protectedSettings": {
                            "workspaceKey": "[listKeys(parameters('WorkspaceResourceId'), '2015-03-20').primarySharedKey]"
                        }
                    }
                }
            ]
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
        "vmResourceId": {
            "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-linux-vm"
        },
        "vmLocation": {
            "value": "westus"
        },
        "osType": {
            "value": "linux"
        },
        "workspaceResourceId": {
            "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace"
        }
    }
}

```


## <a name="onboard-an-azure-virtual-machine-scale-set"></a>Azure 仮想マシン スケール セットをオンボードする
次のサンプルでは、Azure Monitor for VMs に Azure 仮想マシン スケール セットを追加します。


### <a name="template-file"></a>テンプレート ファイル

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "VmssResourceId": {
            "type": "String",
            "metadata": {
                "description": "VM Resource ID."
            }
        },
        "VmssLocation": {
            "type": "String",
            "metadata": {
                "description": "The Virtual Machine Location."
            }
        },
        "osType": {
            "type": "String",
            "metadata": {
                "description": "OS Type, Example: Linux / Windows"
            }
        },
        "WorkspaceResourceId": {
            "type": "String",
            "metadata": {
                "description": "Workspace Resource ID."
            }
        }
    },
    "variables": {
        "VmssName": "[split(parameters('VmssResourceId'),'/')[8]]",
        "DaExtensionName": "[if(equals(toLower(parameters('osType')), 'windows'), 'DependencyAgentWindows', 'DependencyAgentLinux')]",
        "DaExtensionType": "[if(equals(toLower(parameters('osType')), 'windows'), 'DependencyAgentWindows', 'DependencyAgentLinux')]",
        "DaExtensionVersion": "9.5",
        "MmaExtensionName": "[if(equals(toLower(parameters('osType')), 'windows'), 'MMAExtension', 'OMSExtension')]",
        "MmaExtensionType": "[if(equals(toLower(parameters('osType')), 'windows'), 'MicrosoftMonitoringAgent', 'OmsAgentForLinux')]",
        "MmaExtensionVersion": "[if(equals(toLower(parameters('osType')), 'windows'), '1.0', '1.4')]"
    },
    "resources": [
        {
            "type": "Microsoft.Compute/virtualMachineScaleSets",
            "apiVersion": "2018-10-01",
            "name": "[variables('VmssName')]",
            "location": "[parameters('VmssLocation')]",
            "resources": [
                {
                    "type": "extensions",
                    "apiVersion": "2018-10-01",
                    "name": "[variables('DaExtensionName')]",
                    "location": "[parameters('VmssLocation')]",
                    "dependsOn": [
                        "[concat('Microsoft.Compute/virtualMachineScaleSets/', variables('VmssName'))]"
                    ],
                    "properties": {
                        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
                        "type": "[variables('DaExtensionType')]",
                        "typeHandlerVersion": "[variables('DaExtensionVersion')]",
                        "autoUpgradeMinorVersion": true
                    }
                },
                {
                    "type": "extensions",
                    "apiVersion": "2018-10-01",
                    "name": "[variables('MmaExtensionName')]",
                    "location": "[parameters('VmssLocation')]",
                    "dependsOn": [
                        "[concat('Microsoft.Compute/virtualMachineScaleSets/', variables('VmssName'))]"
                    ],
                    "properties": {
                        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                        "type": "[variables('MmaExtensionType')]",
                        "typeHandlerVersion": "[variables('MmaExtensionVersion')]",
                        "autoUpgradeMinorVersion": "true",
                        "settings": {
                            "workspaceId": "[reference(parameters('WorkspaceResourceId'), '2015-03-20').customerId]",
                            "azureResourceId": "[parameters('VmssResourceId')]",
                            "stopOnMultipleConnections": "true"
                        },
                        "protectedSettings": {
                            "workspaceKey": "[listKeys(parameters('WorkspaceResourceId'), '2015-03-20').primarySharedKey]"
                        }
                    }
                }
            ]
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
        "VmssResourceId": {
            "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-windows-vmss"
        },
        "VmssLocation": {
            "value": "westus"
        },
        "OsType": {
            "value": "windows"
        },
        "WorkspaceResourceId": {
            "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace"
        }
    }
}
```


## <a name="next-steps"></a>次のステップ

* [Azure Monitor の他のサンプル テンプレートを入手します](resource-manager-samples.md)。
* [Azure Monitor for VMs の詳細を確認します](../insights/vminsights-overview.md)。
