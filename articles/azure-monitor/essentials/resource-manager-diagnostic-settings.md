---
title: 診断設定用の Resource Manager テンプレートのサンプル
description: Azure Monitor 診断設定を Azure リソースに適用するためのサンプルの Azure Resource Manager テンプレート。
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 09/11/2020
ms.openlocfilehash: ec44c54a00c8532ed082b6e5f9e9cb9ce84f64b5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102033029"
---
# <a name="resource-manager-template-samples-for-diagnostic-settings-in-azure-monitor"></a>Azure Monitor の診断設定用の Resource Manager テンプレートのサンプル
この記事には、Azure リソースの診断設定を作成するためのサンプルの [Azure Resource Manager テンプレート](../../azure-resource-manager/templates/template-syntax.md)が含まれています。 各サンプルには、テンプレート ファイルと、テンプレートに指定するサンプル値を含むパラメーター ファイルが含まれています。

Azure リソースの診断設定を作成するには、`<resource namespace>/providers/diagnosticSettings` 型のリソースをテンプレートに追加します。 この記事では、いくつかの種類のリソースについて例を示しますが、同じパターンを他のリソースの種類にも適用できます。 許可されるログとメトリックのコレクションは、リソースの種類ごとに異なります。

[!INCLUDE [azure-monitor-samples](../../../includes/azure-monitor-resource-manager-samples.md)]

## <a name="diagnostic-setting-for-activity-log"></a>アクティビティ ログの診断設定
次のサンプルでは、`Microsoft.Insights/diagnosticSettings` 型のリソースをテンプレートに追加してアクティビティ ログの診断設定を作成します。

> [!IMPORTANT]
> アクティビティ ログの診断設定は、リソース グループに対して (Azure リソースの設定など) ではなく、サブスクリプションに対して作成されます。 リソース管理テンプレートをデプロイするには、`New-AzSubscriptionDeployment` (PowerShell の場合) または `az deployment sub create` (Azure CLI の場合) を使用します。

### <a name="template-file"></a>テンプレート ファイル

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "settingName": {
          "type": "String"
        },
        "workspaceId": {
          "type": "String"
        },
        "storageAccountId": {
          "type": "String"
        },
        "eventHubAuthorizationRuleId": {
          "type": "String"
        },
        "eventHubName": {
          "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Insights/diagnosticSettings",
            "apiVersion": "2017-05-01-preview",
            "name": "[parameters('settingName')]",
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

### <a name="parameter-file"></a>パラメーター ファイル

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "settingName": {
        "value": "Send to all locations"
      },
      "workspaceId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/MyResourceGroup/providers/microsoft.operationalinsights/workspaces/MyWorkspace"
      },
      "storageAccountId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount"
      },
      "eventHubAuthorizationRuleId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.EventHub/namespaces/MyNameSpace/authorizationrules/RootManageSharedAccessKey"
      },
      "eventHubName": {
        "value": "my-eventhub"
      }
  }
}
```


## <a name="diagnostic-setting-for-azure-key-vault"></a>Azure Key Vault の診断設定 
次のサンプルでは、`Microsoft.KeyVault/vaults/providers/diagnosticSettings` 型のリソースをテンプレートに追加して Azure Key Vault の診断設定を作成します。

### <a name="template-file"></a>テンプレート ファイル

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "settingName": {
            "type": "String"
        },
        "vaultName": {
            "type": "String"
        },
        "workspaceId": {
            "type": "String"
        },
        "storageAccountId": {
            "type": "String"
        },
        "eventHubAuthorizationRuleId": {
            "type": "String"
        },
        "eventHubName": {
            "type": "String"
        }

    },
    "resources": [
        {
          "type": "Microsoft.KeyVault/vaults/providers/diagnosticSettings",
          "apiVersion": "2017-05-01-preview",
          "name": "[concat(parameters('vaultName'), '/Microsoft.Insights/', parameters('settingName'))]",
          "dependsOn": [],
          "properties": {
            "workspaceId": "[parameters('workspaceId')]",
            "storageAccountId": "[parameters('storageAccountId')]",
            "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
            "eventHubName": "[parameters('eventHubName')]",
            "logs": [
              {
                "category": "AuditEvent",
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
}
```

### <a name="parameter-file"></a>パラメーター ファイル

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "settingName": {
          "value": "Send to all locations"
      },
      "vaultName": {
        "value": "MyVault"
      },
      "workspaceId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/MyResourceGroup/providers/microsoft.operationalinsights/workspaces/MyWorkspace"
      },
      "storageAccountId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount"
      },
      "eventHubAuthorizationRuleId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.EventHub/namespaces/MyNameSpace/authorizationrules/RootManageSharedAccessKey"
      },
      "eventHubName": {
        "value": "my-eventhub"
      }
  }
}
```

## <a name="diagnostic-setting-for-azure-sql-database"></a>Azure SQL データベースの診断設定
次のサンプルでは、`microsoft.sql/servers/databases/providers/diagnosticSettings` 型のリソースをテンプレートに追加して Azure SQL データベースの診断設定を作成します。

### <a name="template-file"></a>テンプレート ファイル

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "settingName": {
            "type": "String"
        },        
        "serverName": {
            "type": "String"
        },
        "dbName": {
            "type": "String"
        },
        "workspaceId": {
            "type": "String"
        },
        "storageAccountId": {
            "type": "String"
        },
        "eventHubAuthorizationRuleId": {
            "type": "String"
        },
        "eventHubName": {
            "type": "String"
        }

    },
    "resources": [
        {
          "type": "microsoft.sql/servers/databases/providers/diagnosticSettings",
          "apiVersion": "2017-05-01-preview",
          "name": "[concat(parameters('serverName'),'/',parameters('dbName'),'/microsoft.insights/', parameters('settingName'))]",
          "dependsOn": [],
          "properties": {
            "workspaceId": "[parameters('workspaceId')]",
            "storageAccountId": "[parameters('storageAccountId')]",
            "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
            "eventHubName": "[parameters('eventHubName')]",
            "logs": [
              {
                "category": "SQLInsights",
                "enabled": true
              },
              {
                "category": "AutomaticTuning",
                "enabled": true
              },
              {
                "category": "QueryStoreRuntimeStatistics",
                "enabled": true
              },
              {
                "category": "QueryStoreWaitStatistics",
                "enabled": true
              },
              {
                "category": "Errors",
                "enabled": true
              },
              {
                "category": "DatabaseWaitStatistics",
                "enabled": true
              },
              {
                "category": "Timeouts",
                "enabled": true
              },
              {
                "category": "Blocks",
                "enabled": true
              },
              {
                "category": "Deadlocks",
                "enabled": true
              }
            ],
            "metrics": [
              {
                "category": "Basic",
                "enabled": true
              },
              {
                "category": "InstanceAndAppAdvanced",
                "enabled": true
              },
              {
                "category": "WorkloadManagement",
                "enabled": true
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
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "settingName": {
          "value": "Send to all locations"
      },
      "serverName": {
        "value": "MySqlServer"
      },
      "dbName": {
        "value": "MySqlDb"
      },
      "workspaceId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/MyResourceGroup/providers/microsoft.operationalinsights/workspaces/MyWorkspace"
      },
      "storageAccountId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount"
      },
      "eventHubAuthorizationRuleId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.EventHub/namespaces/MyNameSpace/authorizationrules/RootManageSharedAccessKey"
      },
      "eventHubName": {
        "value": "my-eventhub"
      }
  }
}
```

## <a name="diagnostic-setting-for-recovery-services-vault"></a>Recovery Services コンテナーの診断設定
次のサンプルでは、`microsoft.recoveryservices/vaults/providers/diagnosticSettings` 型のリソースをテンプレートに追加して Azure Recovery Services コンテナーの診断設定を作成します。 この例では、「[Azure リソース ログ](./resource-logs.md#send-to-log-analytics-workspace)」で説明されているように、コレクション モードを指定します。 `logAnalyticsDestinationType` プロパティに `Dedicated` または `AzureDiagnostics` を指定します。

### <a name="template-file"></a>テンプレート ファイル

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "recoveryServicesName": {
            "type": "String"
        },
        "settingName": {
            "type": "String"
        },
        "workspaceId": {
            "type": "String"
        },
        "storageAccountId": {
            "type": "String"
        },
        "eventHubAuthorizationRuleId": {
            "type": "String"
        },
        "eventHubName": {
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "microsoft.recoveryservices/vaults/providers/diagnosticSettings",
            "apiVersion": "2017-05-01-preview",
            "name": "[concat(parameters('recoveryServicesName'), '/Microsoft.Insights/', parameters('settingName'))]",
            "dependsOn": [],
            "properties": {
                "workspaceId": "[parameters('workspaceId')]",
                "storageAccountId": "[parameters('storageAccountId')]",
                "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
                "eventHubName": "[parameters('eventHubName')]",
                "metrics": [],
                "logs": [
                    {
                        "category": "AzureBackupReport",
                        "enabled": false
                    },
                    {
                        "category": "CoreAzureBackup",
                        "enabled": true
                    },
                    {
                        "category": "AddonAzureBackupJobs",
                        "enabled": true
                    },
                    {
                        "category": "AddonAzureBackupAlerts",
                        "enabled": true
                    },
                    {
                        "category": "AddonAzureBackupPolicy",
                        "enabled": true
                    },
                    {
                        "category": "AddonAzureBackupStorage",
                        "enabled": true
                    },
                    {
                        "category": "AddonAzureBackupProtectedInstance",
                        "enabled": true
                    },
                    {
                        "category": "AzureSiteRecoveryJobs",
                        "enabled": false
                    },
                    {
                        "category": "AzureSiteRecoveryEvents",
                        "enabled": false
                    },
                    {
                        "category": "AzureSiteRecoveryReplicatedItems",
                        "enabled": false
                    },
                    {
                        "category": "AzureSiteRecoveryReplicationStats",
                        "enabled": false
                    },
                    {
                        "category": "AzureSiteRecoveryRecoveryPoints",
                        "enabled": false
                    },
                    {
                        "category": "AzureSiteRecoveryReplicationDataUploadRate",
                        "enabled": false
                    },
                    {
                        "category": "AzureSiteRecoveryProtectedDiskDataChurn",
                        "enabled": false
                    }
                ],
                "logAnalyticsDestinationType": "Dedicated"
            }
        }
    ]
}
```

### <a name="parameter-file"></a>パラメーター ファイル

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "settingName": {
          "value": "Send to all locations"
      },
      "recoveryServicesName": {
        "value": "my-vault"
      },
      "workspaceId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/MyResourceGroup/providers/microsoft.operationalinsights/workspaces/MyWorkspace"
      },
      "storageAccountId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount"
      },
      "eventHubAuthorizationRuleId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.EventHub/namespaces/MyNameSpace/authorizationrules/RootManageSharedAccessKey"
      },
      "eventHubName": {
        "value": "my-eventhub"
      }
  }
}
```

## <a name="diagnostic-setting-for-log-analytics-workspace"></a>Log Analytics ワークスペースの診断設定
次のサンプルでは、`Microsoft.OperationalInsights/workspaces/providers/diagnosticSettings` 型のリソースをテンプレートに追加して Log Analytics ワークスペース コンテナーの診断設定を作成します。 この例では、ワークスペースで実行されたクエリに関する監査データを同じワークスペースに送信します。

### <a name="template-file"></a>テンプレート ファイル

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "String"
        },
        "settingName": {
            "type": "String"
        },
        "workspaceId": {
            "type": "String"
        },
        "storageAccountId": {
            "type": "String"
        },
        "eventHubAuthorizationRuleId": {
            "type": "String"
        },
        "eventHubName": {
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces/providers/diagnosticSettings",
            "apiVersion": "2017-05-01-preview",
            "name": "[concat(parameters('workspaceName'), '/Microsoft.Insights/', parameters('settingName'))]",
            "dependsOn": [],
            "properties": {
                "workspaceId": "[parameters('workspaceId')]",
                "storageAccountId": "[parameters('storageAccountId')]",
                "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
                "eventHubName": "[parameters('eventHubName')]",
                "metrics": [],
                "logs": [
                    {
                        "category": "Audit",
                        "enabled": true
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
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "settingName": {
          "value": "Send to all locations"
      },
      "workspaceName": {
        "value": "MyWorkspace"
      },
      "workspaceId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/MyResourceGroup/providers/microsoft.operationalinsights/workspaces/MyWorkspace"
      },
      "storageAccountId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount"
      },
      "eventHubAuthorizationRuleId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.EventHub/namespaces/MyNameSpace/authorizationrules/RootManageSharedAccessKey"
      },
      "eventHubName": {
        "value": "my-eventhub"
      }
  }
}
```

## <a name="diagnostic-setting-for-azure-storage"></a>Azure Storage の診断設定
次の例では、ストレージ アカウントで使用できるストレージ サービス エンドポイントごとに診断設定を作成します。 設定は、アカウントで使用可能な個々のストレージ サービスに適用されます。 使用可能なストレージ サービスは、ストレージ アカウントの種類によって異なります。 アカウントにストレージ サービスが存在する場合にのみ、このテンプレートで、そのストレージ サービスに対して診断設定を作成します。 診断設定では、使用可能なサービスごとに、トランザクション メトリックの収集と、読み取り、書き込み、削除の各操作のリソース ログの収集が有効になります。

### <a name="template-file"></a>テンプレート ファイル

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccountName": {
            "type": "string"
        },
        "settingName": {
            "type": "string"
        },
        "storageSyncName": {
            "type": "string"
        },
        "workspaceId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-10-01",
            "name": "nested",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "Incremental",
                "expressionEvaluationOptions": {
                    "scope": "inner"
                },
                "parameters": {
                    "endpoints": {
                        "value": "[reference(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2019-06-01', 'Full').properties.primaryEndpoints]"
                    },
                    "settingName": {
                        "value": "[parameters('settingName')]"
                    },
                    "storageAccountName": {
                        "value": "[parameters('storageAccountName')]"
                    },
                    "storageSyncName": {
                        "value": "[parameters('storageSyncName')]"
                    },
                    "workspaceId": {
                        "value": "[parameters('workspaceId')]"
                    }
                },
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {
                        "endpoints": {
                            "type": "object"
                        },
                        "settingName": {
                            "type": "String"
                        },
                        "storageAccountName": {
                            "type": "String"
                        },
                        "storageSyncName": {
                            "type": "String"
                        },
                        "workspaceId": {
                            "type": "String"
                        }
                    },
                    "variables": {
                        "hasblob": "[contains(parameters('endpoints'),'blob')]",
                        "hastable": "[contains(parameters('endpoints'),'table')]",
                        "hasfile": "[contains(parameters('endpoints'),'file')]",
                        "hasqueue": "[contains(parameters('endpoints'),'queue')]"
                    },
                    "resources": [
                        {
                            "type": "Microsoft.Storage/storageAccounts/providers/diagnosticsettings",
                            "apiVersion": "2017-05-01-preview",
                            "name": "[concat(parameters('storageAccountName'),'/Microsoft.Insights/', parameters('settingName'))]",

                            "properties": {
                                "workspaceId": "[parameters('workspaceId')]",
                                "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageSyncName'))]",
                                "metrics": [
                                    {
                                        "category": "Transaction",
                                        "enabled": true
                                    }
                                ]
                            }
                        },
                        {
                            "condition": "[variables('hasblob')]",
                            "type": "Microsoft.Storage/storageAccounts/blobServices/providers/diagnosticsettings",
                            "apiVersion": "2017-05-01-preview",
                            "name": "[concat(parameters('storageAccountName'),'/default/Microsoft.Insights/', parameters('settingName'))]",
                            "properties": {
                                "workspaceId": "[parameters('workspaceId')]",
                                "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageSyncName'))]",
                                "logs": [
                                    {
                                        "category": "StorageRead",
                                        "enabled": true
                                    },
                                    {
                                        "category": "StorageWrite",
                                        "enabled": true
                                    },
                                    {
                                        "category": "StorageDelete",
                                        "enabled": true
                                    }
                                ],
                                "metrics": [
                                    {
                                        "category": "Transaction",
                                        "enabled": true
                                    }
                                ]
                            }
                        },
                        {
                            "condition": "[variables('hastable')]",
                            "type": "Microsoft.Storage/storageAccounts/tableServices/providers/diagnosticsettings",
                            "apiVersion": "2017-05-01-preview",
                            "name": "[concat(parameters('storageAccountName'),'/default/Microsoft.Insights/', parameters('settingName'))]",

                            "properties": {
                                "workspaceId": "[parameters('workspaceId')]",
                                "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageSyncName'))]",
                                "logs": [
                                    {
                                        "category": "StorageRead",
                                        "enabled": true
                                    },
                                    {
                                        "category": "StorageWrite",
                                        "enabled": true
                                    },
                                    {
                                        "category": "StorageDelete",
                                        "enabled": true
                                    }
                                ],
                                "metrics": [
                                    {
                                        "category": "Transaction",
                                        "enabled": true
                                    }
                                ]
                            }
                        },
                        {
                            "condition": "[variables('hasfile')]",
                            "type": "Microsoft.Storage/storageAccounts/fileServices/providers/diagnosticsettings",
                            "apiVersion": "2017-05-01-preview",
                            "name": "[concat(parameters('storageAccountName'),'/default/Microsoft.Insights/', parameters('settingName'))]",
                            "properties": {
                                "workspaceId": "[parameters('workspaceId')]",
                                "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageSyncName'))]",
                                "logs": [
                                    {
                                        "category": "StorageRead",
                                        "enabled": true
                                    },
                                    {
                                        "category": "StorageWrite",
                                        "enabled": true
                                    },
                                    {
                                        "category": "StorageDelete",
                                        "enabled": true
                                    }
                                ],
                                "metrics": [
                                    {
                                        "category": "Transaction",
                                        "enabled": true
                                    }
                                ]
                            }
                        },
                        {
                            "condition": "[variables('hasqueue')]",
                            "type": "Microsoft.Storage/storageAccounts/queueServices/providers/diagnosticsettings",
                            "apiVersion": "2017-05-01-preview",
                            "name": "[concat(parameters('storageAccountName'),'/default/Microsoft.Insights/', parameters('settingName'))]",
                            "properties": {
                                "workspaceId": "[parameters('workspaceId')]",
                                "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageSyncName'))]",
                                "logs": [
                                    {
                                        "category": "StorageRead",
                                        "enabled": true
                                    },
                                    {
                                        "category": "StorageWrite",
                                        "enabled": true
                                    },
                                    {
                                        "category": "StorageDelete",
                                        "enabled": true
                                    }
                                ],
                                "metrics": [
                                    {
                                        "category": "Transaction",
                                        "enabled": true
                                    }
                                ]
                            }
                        }
                    ]
                }
            }
        }
    ]
}
```

### <a name="parameter-file"></a>パラメーター ファイル

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "storageAccountName": {
          "value": "mymonitoredstorageaccount"
      },
      "settingName": {
          "value": "Send to all locations"
      },
      "storageSyncName": {
          "value": "mystorageaccount"
      },
      "workspaceId": {
          "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/MyResourceGroup/providers/microsoft.operationalinsights/workspaces/MyWorkspace"
      }
    }
  }
```

## <a name="next-steps"></a>次のステップ

* [Azure Monitor の他のサンプル テンプレートを入手します](../resource-manager-samples.md)。
* [診断設定の詳細情報](../essentials/diagnostic-settings.md)。