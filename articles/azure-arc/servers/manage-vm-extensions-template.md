---
title: Azure Resource Manager テンプレートを使用して VM 拡張機能を有効にする
description: この記事では、Azure Resource Manager テンプレートを使用して、ハイブリッド環境で実行されている Azure Arc 対応サーバーに仮想マシン拡張機能をデプロイする方法について説明します。
ms.date: 02/03/2021
ms.topic: conceptual
ms.openlocfilehash: cfba14ac30553178bd509d0b0e7ba9c60332d299
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493330"
---
# <a name="enable-azure-vm-extensions-by-using-arm-template"></a>ARM テンプレートを使用して VM 拡張機能を有効にする

この記事では、Azure Resource Manager テンプレート (ARM テンプレート) を使用して、Azure Arc 対応サーバーでサポートされている Azure VM 拡張機能をデプロイする方法を示します。

VM 拡張機能を Azure Resource Manager テンプレートに追加し、テンプレートのデプロイを使用して実行できます。 Arc 対応サーバーによってサポートされている VM 拡張機能の場合、Azure PowerShell を使用して Linux または Windows マシンにサポートされている VM 拡張機能をデプロイできます。 以下の各サンプルには、テンプレート ファイルと、テンプレートに指定するサンプル値を含むパラメーター ファイルが含まれています。

>[!NOTE]
>複数の拡張機能をまとめてバッチ処理することができますが、順番にインストールされます。 最初の拡張機能のインストールが完了すると、次の拡張機能のインストールが試行されます。

## <a name="deploy-the-log-analytics-vm-extension"></a>Log Analytics VM 拡張機能をデプロイする

Log Analytics エージェントを簡単にデプロイするため、Windows または Linux へのエージェントのインストールに関する次のサンプルが提供されています。

### <a name="template-file-for-linux"></a>Linux 用テンプレート ファイル

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "workspaceId": {
            "type": "string"
        },
        "workspaceKey": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/OMSAgentForLinux')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                "type": "OmsAgentForLinux",
                "settings": {
                    "workspaceId": "[parameters('workspaceId')]"
                },
                "protectedSettings": {
                    "workspaceKey": "[parameters('workspaceKey')]"
                }
            }
        }
    ]
}
```

### <a name="template-file-for-windows"></a>Windows 用テンプレート ファイル

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "workspaceId": {
            "type": "string"
        },
        "workspaceKey": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/MicrosoftMonitoringAgent')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                "type": "MicrosoftMonitoringAgent",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "workspaceId": "[parameters('workspaceId')]"
                },
                "protectedSettings": {
                    "workspaceKey": "[parameters('workspaceKey')]"
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
        "vmName": {
            "value": "<vmName>"
        },
        "location": {
            "value": "<region>"
        },
        "workspaceId": {
            "value": "<MyWorkspaceID>"
        },
        "workspaceKey": {
            "value": "<MyWorkspaceKey>"
        }
    }
}
```

テンプレート ファイルとパラメーター ファイルをディスクに保存し、デプロイに適した値を使用してパラメーター ファイルを編集します。 その後、次のコマンドを使用して、リソース グループ内の接続されているすべてのマシンに拡張機能をインストールできます。 このコマンドでは、*TemplateFile* パラメーターを使用してテンプレートを指定し、*TemplateParameterFile* パラメーターを使用して、パラメーターとパラメーター値が含まれるファイルを指定します。

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "ContosoEngineering" -TemplateFile "D:\Azure\Templates\LogAnalyticsAgentWin.json" -TemplateParameterFile "D:\Azure\Templates\LogAnalyticsAgentWinParms.json"
```

## <a name="deploy-the-custom-script-extension"></a>カスタム スクリプト拡張機能をデプロイする

カスタム スクリプト拡張機能を使用するため、Windows および Linux で実行する次のサンプルが提供されています。 カスタム スクリプト拡張機能に慣れていない場合は、「[Windows でのカスタムのスクリプト拡張機能](../../virtual-machines/extensions/custom-script-windows.md)」または [Linux 用のカスタム スクリプト拡張機能](../../virtual-machines/extensions/custom-script-linux.md)に関する記事をご覧ください。 ハイブリッド マシンでこの拡張機能を使用するときに理解しておく必要のある異なる特徴がいくつかあります。

* Azure VM カスタム スクリプト拡張機能でサポートされているオペレーティング システムの一覧は、Azure Arc 対応サーバーには適用されません。 Arc 対応サーバーでサポートされている OS の一覧は、[こちら](agent-overview.md#supported-operating-systems)で確認できます。

* Azure Virtual Machine Scale Sets またはクラシック VM に関する構成の詳細は適用できません。

* マシンでスクリプトを外部からダウンロードする必要があり、プロキシ サーバー経由でのみ通信できる場合は、[Connected Machine エージェントを構成](manage-agent.md#update-or-remove-proxy-settings)してプロキシ サーバーの環境変数を設定する必要があります。

カスタム スクリプト拡張機能の構成では、スクリプトの場所や、実行するコマンドなどを指定します。 この構成は、以下で示す Linux および Windows のハイブリッド マシン用の Azure Resource Manager テンプレートで指定されています。

### <a name="template-file-for-linux"></a>Linux 用テンプレート ファイル

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string"
    },
    "location": {
      "type": "string"
    },
    "fileUris": {
      "type": "array"
    },
    "commandToExecute": {
      "type": "securestring"
    }
  },
  "resources": [
    {
      "name": "[concat(parameters('vmName'),'/CustomScript')]",
      "type": "Microsoft.HybridCompute/machines/extensions",
      "location": "[parameters('location')]",
      "apiVersion": "2019-08-02-preview",
      "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "autoUpgradeMinorVersion": true,
        "settings": {},
        "protectedSettings": {
          "commandToExecute": "[parameters('commandToExecute')]",
          "fileUris": "[parameters('fileUris')]"
        }
      }
    }
  ]
}
```

### <a name="template-file-for-windows"></a>Windows 用テンプレート ファイル

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "fileUris": {
            "type": "string"
        },
        "arguments": {
            "type": "securestring",
            "defaultValue": " "
        }
    },
    "variables": {
        "UriFileNamePieces": "[split(parameters('fileUris'), '/')]",
        "firstFileNameString": "[variables('UriFileNamePieces')[sub(length(variables('UriFileNamePieces')), 1)]]",
        "firstFileNameBreakString": "[split(variables('firstFileNameString'), '?')]",
        "firstFileName": "[variables('firstFileNameBreakString')[0]]"
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/CustomScriptExtension')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.Compute",
                "type": "CustomScriptExtension",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "fileUris": "[split(parameters('fileUris'), ' ')]"
                },
                "protectedSettings": {
                    "commandToExecute": "[concat ('powershell -ExecutionPolicy Unrestricted -File ', variables('firstFileName'), ' ', parameters('arguments'))]"
                }
            }
        }
    ]
}
```

### <a name="parameter-file"></a>パラメーター ファイル

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {}
    ],
    "steps": [
      {
        "name": "customScriptExt",
        "label": "Add Custom Script Extension",
        "elements": [
          {
            "name": "fileUris",
            "type": "Microsoft.Common.FileUpload",
            "label": "Script files",
            "toolTip": "The script files that will be downloaded to the virtual machine.",
            "constraints": {
              "required": false
            },
            "options": {
              "multiple": true,
              "uploadMode": "url"
            },
            "visible": true
          },
          {
            "name": "commandToExecute",
            "type": "Microsoft.Common.TextBox",
            "label": "Command",
            "defaultValue": "sh script.sh",
            "toolTip": "The command to execute, for example: sh script.sh",
            "constraints": {
              "required": true
            },
            "visible": true
          }
        ]
      }
    ],
    "outputs": {
      "vmName": "[vmName()]",
      "location": "[location()]",
      "fileUris": "[steps('customScriptExt').fileUris]",
      "commandToExecute": "[steps('customScriptExt').commandToExecute]"
    }
  }
}
```

## <a name="deploy-the-powershell-dsc-extension"></a>PowerShell DSC 拡張機能をデプロイする

PowerShell DSC 拡張機能を使用するため、Windows および Linux で実行する次のサンプルが提供されています。 PowerShell DSC 拡張機能に慣れていない場合は、[DSC 拡張機能ハンドラーの概要](../../virtual-machines/extensions/dsc-overview.md)に関する記事を参照してください。 ハイブリッド マシンでこの拡張機能を使用するときに理解しておく必要のある異なる特徴がいくつかあります。

* Azure VM PowerShell DSC 拡張機能でサポートされているオペレーティング システムの一覧は、Azure Arc 対応サーバーには適用されません。 Arc 対応サーバーでサポートされている OS の一覧は、[こちら](agent-overview.md#supported-operating-systems)で確認できます。

* マシンでスクリプトを外部からダウンロードする必要があり、プロキシ サーバー経由でのみ通信できる場合は、[Connected Machine エージェントを構成](manage-agent.md#update-or-remove-proxy-settings)してプロキシ サーバーの環境変数を設定する必要があります。

### <a name="template-file-for-linux"></a>Linux 用テンプレート ファイル

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string",
            "metadata": {
                "description": "Name of the vm, will be used as DNS Name for the Public IP used to access the Virtual Machine."
            }
        },
        "location": {
            "type": "string",
            "metadata": {
                "description": "Location for all resources."
            }
        },
        "mode": {
            "type": "string",
            "defaultValue": "Push",
            "metadata": {
                "description": "The functional mode, push MOF configuration (Push), distribute MOF configuration (Pull), install custom DSC module (Install)"
            },
            "allowedValues": [
                "Push",
                "Pull",
                "Install",
                "Register"
            ]
        },
        "fileUri": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The uri of the MOF file/Meta MOF file/resource ZIP file"
            }
        },
        "registrationUrl": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The URL of the Azure Automation account"
            }
        },
        "registrationKey": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The access key of the Azure Automation account"
            }
        }
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/DSCForLinux')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.OSTCExtensions",
                "type": "DSCForLinux",
                "settings": {
                    "Mode": "[parameters('mode')]",
                    "FileUri": "[parameters('fileUri')]"
                },
                "protectedSettings": {
                    "RegistrationUrl": "[parameters('registrationUrl')]",
                    "RegistrationKey": "[parameters('registrationKey')]"
                }
            }
        }
    ]
}
```

### <a name="template-file-for-windows"></a>Windows 用テンプレート ファイル

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "modulesUrl": {
            "type": "string"
        },
        "configurationFunction": {
            "type": "string"
        },
        "properties": {
            "type": "string",
            "defaultValue": ""
        },
        "dataBlobUri": {
            "type": "string",
            "defaultValue": ""
        },
        "wmfVersion": {
            "type": "string",
            "defaultValue": "latest",
            "allowedValues": [
                "4.0",
                "5.0",
                "5.1",
                "latest"
            ]
        },
        "privacy": {
            "type": "string",
            "defaultValue": ""
        },
        "autoUpdate": {
            "type": "bool",
            "defaultValue": false
        }
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/Microsoft.Powershell.DSC')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.Powershell",
                "type": "DSC",
                "autoUpgradeMinorVersion": "[parameters('autoUpdate')]",
                "settings": {
                    "ModulesUrl": "[parameters('modulesUrl')]",
                    "ConfigurationFunction": "[parameters('configurationFunction')]",
                    "Properties": "[parameters('properties')]",
                    "WmfVersion": "[parameters('wmfVersion')]",
                    "Privacy": {
                        "DataCollection": "[parameters('privacy')]"
                    }
                },
                "protectedSettings": {
                    "DataBlobUri": "[parameters('dataBlobUri')]"
                }
            }
        }
    ]
}
```

### <a name="parameter-file"></a>パラメーター ファイル

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {}
    ],
    "steps": [
      {
        "name": "dscExtension",
        "label": "Add DSC Extension",
        "elements": [
          {
            "name": "Mode",
            "type": "Microsoft.Common.OptionsGroup",
            "label": "Mode",
            "defaultValue": 0,
            "toolTip": "The functional mode, push MOF configuration (Push), distribute MOF configuration (Pull), install custom DSC module (Install)",
            "constraints": {
              "allowedValues": [
                {
                  "label": "Push",
                  "value": "Push"
                },
                {
                  "label": "Pull",
                  "value": "Pull"
                },
                {
                  "label": "Install",
                  "value": "Install"
                },
                {
                  "label": "Register",
                  "value": "Register"
                }
              ]
            },
            "visible": true
          },
          {
            "name": "FileUri",
            "type": "Microsoft.Common.FileUpload",
            "label": "File URI",
            "toolTip": "The uri of the MOF file/Meta MOF file/resource ZIP file",
            "constraints": {
              "required": false,
              "accept": ".psd1"
            },
            "options": {
              "multiple": false,
              "uploadMode": "url",
              "openMode": "binary",
              "encoding": "UTF-8"
            }
          },
          {
            "name": "RegistrationUrl",
            "type": "Microsoft.Common.TextBox",
            "label": "Registration URL",
            "toolTip": "The URL of the Azure Automation account",
            "constraints": {
              "required": false
            }
          },
          {
            "name": "RegistrationKey",
            "type": "Microsoft.Common.TextBox",
            "label": "Registration key",
            "toolTip": "The access key of the Azure Automation account",
            "constraints": {
              "required": false
            }
          }
        ]
      }
    ],
    "outputs": {
      "vmName": "[vmName()]",
      "location": "[location()]",
      "mode": "[steps('dscExtension').Mode]",
      "fileUri": "[steps('dscExtension').FileUri]",
      "registrationUrl": "[steps('dscExtension').RegistrationUrl]",
      "registrationKey": "[steps('dscExtension').RegistrationKey]"
    }
  }
}
```

## <a name="deploy-the-dependency-agent-extension"></a>Dependency Agent 拡張機能をデプロイする

Azure Monitor 依存関係エージェント拡張機能を使用するため、Windows および Linux で実行する次のサンプルが提供されています。 依存関係エージェントの詳細については、「[Azure Monitor エージェントの概要](../../azure-monitor/platform/agents-overview.md#dependency-agent)」を参照してください。

### <a name="template-file-for-linux"></a>Linux 用テンプレート ファイル

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Linux machine."
      }
    }
  },
  "variables": {
    "vmExtensionsApiVersion": "2017-03-30"
  },
  "resources": [
    {
      "type": "Microsoft.HybridCompute/machines/extensions",
      "name": "[concat(parameters('vmName'),'/DAExtension')]",
      "apiVersion": "[variables('vmExtensionsApiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
      ],
      "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentLinux",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
      }
    }
  ],
    "outputs": {
    }
}
```

### <a name="template-file-for-windows"></a>Windows 用テンプレート ファイル

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Windows machine."
      }
    }
  },
  "variables": {
    "vmExtensionsApiVersion": "2017-03-30"
  },
  "resources": [
    {
      "type": "Microsoft.HybridCompute/machines/extensions",
      "name": "[concat(parameters('vmName'),'/DAExtension')]",
      "apiVersion": "[variables('vmExtensionsApiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
      ],
      "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
      }
    }
  ],
    "outputs": {
    }
}
```

## <a name="deploy-azure-key-vault-vm-extension-preview"></a>Azure Key Vault VM 拡張機能 (プレビュー) のデプロイ

次の JSON は、Key Vault VM 拡張機能 (プレビュー) のスキーマを示しています。 この拡張機能では、保護された設定は必要ありません。そのすべての設定はパブリック情報と見なされます。 この拡張機能には、監視対象の証明書、ポーリング頻度、および宛先の証明書ストアの一覧が必要です。 具体的な内容は次のとおりです。

### <a name="template-file-for-linux"></a>Linux 用テンプレート ファイル

```json
{
      "type": "Microsoft.HybridCompute/machines/extensions",
      "name": "KeyVaultForLinux",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.HybridCompute/machines/extensions/', <machineName>)]"
      ],
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForLinux",
      "typeHandlerVersion": "1.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
          "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g. "3600">,
          "certificateStoreName": <ingnored on linux>,
          "certificateStoreLocation": <disk path where certificate is stored, default: "/var/lib/waagent/Microsoft.Azure.KeyVault">,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net/secrets/mycertificate"
          }
      }
     }
}
```

### <a name="template-file-for-windows"></a>Windows 用テンプレート ファイル

```json
{
      "type": "Microsoft.HybridCompute/machines/extensions",
      "name": "KVVMExtensionForWindows",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.HybridCompute/machines/extensions/', <machineName>)]"
      ],
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForWindows",
      "typeHandlerVersion": "1.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g: "3600">,
          "certificateStoreName": <certificate store name, e.g.: "MY">,
          "linkOnRenewal": <Only Windows. This feature ensures s-channel binding when certificate renews, without necessitating a re-deployment.  e.g.: false>,
          "certificateStoreLocation": <certificate store location, currently it works locally only e.g.: "LocalMachine">,
          "requireInitialSync": <initial synchronization of certificates e..g: true>,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net/secrets/mycertificate"
        },
        "authenticationSettings": {
                "msiEndpoint":  <Optional MSI endpoint e.g.: "http://169.254.169.254/metadata/identity">,
                "msiClientId":  <Optional MSI identity e.g.: "c7373ae5-91c2-4165-8ab6-7381d6e75619">
        }
      }
     }
}
```

> [!NOTE]
> 確認された証明書の URL は、`https://myVaultName.vault.azure.net/secrets/myCertName` の形式である必要があります。
> 
> これは、`/secrets` のパスでは秘密キーを含む完全な証明書が返されるのに対し、`/certificates` のパスでは返されないためです。 証明書について詳しくは、次の記事をご覧ください: 「[Key Vault 証明書](../../key-vault/general/about-keys-secrets-certificates.md)」

テンプレート ファイルをディスクに保存します。 その後、次のコマンドを使用して、リソース グループ内の接続されているすべてのマシンに拡張機能をインストールできます。

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "ContosoEngineering" -TemplateFile "D:\Azure\Templates\KeyVaultExtension.json"
```

## <a name="deploy-the-azure-defender-integrated-scanner"></a>Azure Defender 統合スキャナーのデプロイ

Azure Defender 統合スキャナー拡張機能を使用するため、Windows および Linux で実行する次のサンプルが提供されています。 統合スキャナーに詳しくない場合は、ハイブリッド マシン向けの [Azure Defender の脆弱性評価ソリューションの概要](../../security-center/deploy-vulnerability-assessment-vm.md)に関するページを参照してください。

### <a name="template-file-for-windows"></a>Windows 用テンプレート ファイル

```json
{
  "properties": {
    "mode": "Incremental",
    "template": {
      "contentVersion": "1.0.0.0",
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "parameters": {
        "vmName": {
          "type": "string"
        },
        "apiVersionByEnv": {
          "type": "string"
        }
      },
      "resources": [
        {
          "type": "resourceType/providers/WindowsAgent.AzureSecurityCenter",
          "name": "[concat(parameters('vmName'), '/Microsoft.Security/default')]",
          "apiVersion": "[parameters('apiVersionByEnv')]"
        }
      ]
    },
    "parameters": {
      "vmName": {
        "value": "resourceName"
      },
      "apiVersionByEnv": {
        "value": "2015-06-01-preview"
      }
    }
  }
}
```

### <a name="template-file-for-linux"></a>Linux 用テンプレート ファイル

```json
{
  "properties": {
    "mode": "Incremental",
    "template": {
      "contentVersion": "1.0.0.0",
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "parameters": {
        "vmName": {
          "type": "string"
        },
        "apiVersionByEnv": {
          "type": "string"
        }
      },
      "resources": [
        {
          "type": "resourceType/providers/LinuxAgent.AzureSecurityCenter",
          "name": "[concat(parameters('vmName'), '/Microsoft.Security/default')]",
          "apiVersion": "[parameters('apiVersionByEnv')]"
        }
      ]
    },
    "parameters": {
      "vmName": {
        "value": "resourceName"
      },
      "apiVersionByEnv": {
        "value": "2015-06-01-preview"
      }
    }
  }
}
```

テンプレート ファイルをディスクに保存します。 その後、次のコマンドを使用して、リソース グループ内の接続されているすべてのマシンに拡張機能をインストールできます。

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "ContosoEngineering" -TemplateFile "D:\Azure\Templates\AzureDefenderScanner.json"
```

## <a name="next-steps"></a>次のステップ

* [Azure PowerShell](manage-vm-extensions-powershell.md)、[Azure portal](manage-vm-extensions-portal.md)、または [Azure CLI](manage-vm-extensions-cli.md) を使用して、VM 拡張機能をデプロイ、管理、削除できます。

* トラブルシューティングに関する情報は、[VM 拡張機能のトラブルシューティング ガイド](troubleshoot-vm-extensions.md)に記載されています。