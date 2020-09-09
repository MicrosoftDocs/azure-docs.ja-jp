---
title: Azure Arc 対応サーバー (プレビュー) を使用した VM 拡張機能の管理
description: Azure Arc 対応サーバー (プレビュー) を使用すると、Azure 以外の VM でのデプロイ後構成と自動化タスクを提供する仮想マシン拡張機能のデプロイを管理できます。
ms.date: 06/17/2020
ms.topic: conceptual
ms.openlocfilehash: 259a55c0e5cb4074871fd70a61e6c61d5cf3c50a
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "89018163"
---
# <a name="virtual-machine-extension-management-with-azure-arc-enabled-servers-preview"></a>Azure Arc 対応サーバー (プレビュー) を使用した仮想マシン拡張機能の管理

仮想マシン (VM) 拡張機能は、Azure VM でのデプロイ後の構成と自動タスクを提供する複数の小さなアプリケーションです。 たとえば、仮想マシンでソフトウェアのインストールやウイルス対策保護が必要な場合、あるいは、仮想マシン内でスクリプトを実行するために、VM 拡張機能を使用できます。

Azure Arc 対応サーバー (プレビュー) を使用すると、Azure VM 拡張機能を Azure 以外の Windows VM と Linux VM にデプロイし、オンプレミス、エッジ、その他のクラウド環境のハイブリッド マシンのライフサイクルを通した管理を簡素化できます。

## <a name="key-benefits"></a>主な利点

Azure Arc 対応サーバー (プレビュー) による VM 拡張機能のサポートには、次のような主な利点があります。

* [Azure Automation State Configuration](../../automation/automation-dsc-overview.md) を使用して、構成を一元的に保存し、DSC VM 拡張機能によって有効にされているハイブリッド接続マシンの望ましい状態を維持します。

* Log Analytics エージェント VM 拡張機能によって有効にされる [Azure Monitor のログ](../../azure-monitor/platform/data-platform-logs.md)を使用して、分析用のログ データを収集します。 これは、さまざまなソースからのデータについて複雑な分析を実行するときに便利です。

* [Azure Monitor for VMs](../../azure-monitor/insights/vminsights-overview.md) を使用して、Windows VM および Linux VM のパフォーマンスを分析し、それらのプロセスや、他のリソースおよび外部プロセスへの依存関係を監視します。 これは、Log Analytics エージェントと Dependency Agent の両方の VM 拡張機能を有効にすることで実現されます。

* スクリプトをダウンロードし、カスタム スクリプト拡張機能を使用してハイブリッド接続マシンで実行します。 この拡張機能は、展開後の構成、ソフトウェアのインストール、その他の構成タスクや管理タスクに役立ちます。

## <a name="availability"></a>可用性

VM 拡張機能は、[サポートされているリージョン](overview.md#supported-regions)の一覧でのみ使用できます。 これらのリージョンのいずれかにマシンをオンボードしてください。

## <a name="extensions"></a>拡張機能

このプレビューでは、Windows および Linux マシンで次の VM 拡張機能がサポートされています。

|拡張機能 |OS |Publisher |関連情報 |
|----------|---|----------|-----------------------|
|CustomScriptExtension |Windows |Microsoft.Compute |[Windows カスタム スクリプト拡張機能](../../virtual-machines/extensions/custom-script-windows.md)|
|DSC |Windows |Microsoft.PowerShell|[Windows PowerShell DSC 拡張機能](../../virtual-machines/extensions/dsc-windows.md)|
|Log Analytics エージェント |Windows |Microsoft.EnterpriseCloud.Monitoring |[Windows 用 Log Analytics VM 拡張機能](../../virtual-machines/extensions/oms-windows.md)|
|Microsoft Dependency Agent | Windows |Microsoft.Compute | [Windows 用 Dependency Agent 仮想マシン拡張機能](../../virtual-machines/extensions/agent-dependency-windows.md)|
|CustomScript|Linux |Microsoft.Azure.Extension |[Linux カスタム スクリプト拡張機能バージョン 2](../../virtual-machines/extensions/custom-script-linux.md) |
|DSC |Linux |Microsoft.OSTCExtensions |[Linux 用 PowerShell DSC 拡張機能](../../virtual-machines/extensions/dsc-linux.md) |
|Log Analytics エージェント |Linux |Microsoft.EnterpriseCloud.Monitoring |[Linux 用 Log Analytics VM 拡張機能](../../virtual-machines/extensions/oms-linux.md) |
|Microsoft Dependency Agent | Linux |Microsoft.Compute | [Linux 用 Dependency Agent 仮想マシン拡張機能](../../virtual-machines/extensions/agent-dependency-linux.md) |

VM 拡張機能は、Azure portal から、または Arc 対応サーバー (プレビュー) によって管理されているハイブリッド サーバー上の Azure PowerShell から、Azure Resource Manager テンプレートで実行できます。

Azure Connected Machine エージェント パッケージと拡張機能エージェント コンポーネントの詳細については、[エージェントの概要](agent-overview.md#agent-component-details)に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件

この機能は、サブスクリプション内の次の Azure リソース プロバイダーに依存します。

* **Microsoft.HybridCompute**
* **Microsoft.GuestConfiguration**

まだ登録されていない場合は、「[Azure リソースプロバイダーを登録する](agent-overview.md#register-azure-resource-providers)」の手順に従ってください。

Linux 用の Log Analytics エージェント VM 拡張機能を使用するには、ターゲット マシンに Python 2.x がインストールされている必要があります。

### <a name="connected-machine-agent"></a>Connected Machine エージェント

お使いのマシンが、Azure Connected Machine エージェントに対する Windows および Linux オペレーティング システムの[サポートされているバージョン](agent-overview.md#supported-operating-systems)と一致することを確認します。

この機能でサポートされる Connected Machine エージェントの最小バージョンは次のとおりです。

* Windows - 0.7.x
* Linux - 0.8.x

マシンを必要なエージェントのバージョンにアップグレードするには、「[エージェントのアップグレード](manage-agent.md#upgrading-agent)」を参照してください。

## <a name="enable-extensions-from-the-portal"></a>ポータルから拡張機能を有効にする

VM 拡張機能は、Azure portal を使用して、Arc for server (プレビュー) で管理されているマシンに適用できます。

1. お使いのブラウザーで [Azure portal](https://aka.ms/arcserver-preview) に移動します。

2. ポータルで **[マシン - Azure Arc]** に移動し、一覧からハイブリッド マシンを選択します。

3. **[拡張機能]** を選択し、 **[追加]** を選択します。 利用可能な拡張機能の一覧から目的の拡張機能を選択し、ウィザードの手順に従います。 この例では、Log Analytics VM 拡張機能をデプロイします。 

    ![選択したマシンの VM 拡張機能を選択する](./media/manage-vm-extensions/add-vm-extensions.png)

    次の例では、Azure portal からの Log Analytics VM 拡張機能のインストールを示します。

    ![Log Analytics VM 拡張機能をインストールする](./media/manage-vm-extensions/mma-extension-config.png)

    インストールを完了するには、ワークスペース ID と主キーを指定する必要があります。 この情報の検索方法に慣れていない場合は、「[ワークスペース ID とキーを取得する](../../azure-monitor/platform/log-analytics-agent.md#workspace-id-and-key)」を参照してください。

4. 表示される必要な情報を確認したら、 **[作成]** を選択します。 デプロイの概要が表示され、デプロイの状態を確認できます。

>[!NOTE]
>複数の拡張機能をまとめてバッチ処理することができますが、順番にインストールされます。 最初の拡張機能のインストールが完了すると、次の拡張機能のインストールが試行されます。

## <a name="azure-resource-manager-templates"></a>Azure Resource Manager のテンプレート

VM 拡張機能を Azure Resource Manager テンプレートに追加し、テンプレートのデプロイを使用して実行できます。 Arc 対応サーバー (プレビュー) によってサポートされている VM 拡張機能の場合、Azure PowerShell を使用して Linux または Windows マシンにサポートされている VM 拡張機能をデプロイできます。 以下の各サンプルには、テンプレート ファイルと、テンプレートに指定するサンプル値を含むパラメーター ファイルが含まれています。

>[!NOTE]
>複数の拡張機能をまとめてバッチ処理することができますが、順番にインストールされます。 最初の拡張機能のインストールが完了すると、次の拡張機能のインストールが試行されます。

### <a name="deploy-the-log-analytics-vm-extension"></a>Log Analytics VM 拡張機能をデプロイする

Log Analytics エージェントを簡単にデプロイするため、Windows または Linux へのエージェントのインストールに関する次のサンプルが提供されています。

#### <a name="template-file-for-linux"></a>Linux 用テンプレート ファイル

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

#### <a name="template-file-for-windows"></a>Windows 用テンプレート ファイル

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

#### <a name="parameter-file"></a>パラメーター ファイル

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

### <a name="deploy-the-custom-script-extension"></a>カスタム スクリプト拡張機能をデプロイする

カスタム スクリプト拡張機能を使用するため、Windows および Linux で実行する次のサンプルが提供されています。 カスタム スクリプト拡張機能に慣れていない場合は、「[Windows でのカスタムのスクリプト拡張機能](../../virtual-machines/extensions/custom-script-windows.md)」または [Linux 用のカスタム スクリプト拡張機能](../../virtual-machines/extensions/custom-script-linux.md)に関する記事を参照してください。 ハイブリッド マシンでこの拡張機能を使用するときに理解しておく必要のある異なる特徴がいくつかあります。

* Azure VM カスタム スクリプト拡張機能でサポートされているオペレーティング システムの一覧は、Azure Arc 対応サーバーには適用されません。 Arc 対応サーバーでサポートされている OS の一覧は、[こちら](agent-overview.md#supported-operating-systems)で確認できます。

* Azure Virtual Machine Scale Sets またはクラシック VM に関する構成の詳細は適用できません。

* マシンでスクリプトを外部からダウンロードする必要があり、プロキシ サーバー経由でのみ通信できる場合は、[Connected Machine エージェントを構成](manage-agent.md#update-or-remove-proxy-settings)してプロキシ サーバーの環境変数を設定する必要があります。

カスタム スクリプト拡張機能の構成では、スクリプトの場所や、実行するコマンドなどを指定します。 この構成は、以下で示す Linux および Windows のハイブリッド マシン用の Azure Resource Manager テンプレートで指定されています。

#### <a name="template-file-for-linux"></a>Linux 用テンプレート ファイル

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

#### <a name="template-file-for-windows"></a>Windows 用テンプレート ファイル

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

#### <a name="parameter-file"></a>パラメーター ファイル

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

### <a name="deploy-the-powershell-dsc-extension"></a>PowerShell DSC 拡張機能をデプロイする

PowerShell DSC 拡張機能を使用するため、Windows および Linux で実行する次のサンプルが提供されています。 PowerShell DSC 拡張機能に慣れていない場合は、[DSC 拡張機能ハンドラーの概要](../../virtual-machines/extensions/dsc-overview.md)に関する記事を参照してください。 ハイブリッド マシンでこの拡張機能を使用するときに理解しておく必要のある異なる特徴がいくつかあります。

* Azure VM PowerShell DSC 拡張機能でサポートされているオペレーティング システムの一覧は、Azure Arc 対応サーバーには適用されません。 Arc 対応サーバーでサポートされている OS の一覧は、[こちら](agent-overview.md#supported-operating-systems)で確認できます。

* マシンでスクリプトを外部からダウンロードする必要があり、プロキシ サーバー経由でのみ通信できる場合は、[Connected Machine エージェントを構成](manage-agent.md#update-or-remove-proxy-settings)してプロキシ サーバーの環境変数を設定する必要があります。

#### <a name="template-file-for-linux"></a>Linux 用テンプレート ファイル

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

#### <a name="template-file-for-windows"></a>Windows 用テンプレート ファイル

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

#### <a name="parameter-file"></a>パラメーター ファイル

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

### <a name="dependency-agent"></a>依存関係エージェント

Azure Monitor 依存関係エージェント拡張機能を使用するため、Windows および Linux で実行する次のサンプルが提供されています。 依存関係エージェントの詳細については、「[Azure Monitor エージェントの概要](../../azure-monitor/platform/agents-overview.md#dependency-agent)」を参照してください。

#### <a name="template-file-for-linux"></a>Linux 用テンプレート ファイル

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

#### <a name="template-file-for-windows"></a>Windows 用テンプレート ファイル

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

## <a name="troubleshooting"></a>トラブルシューティング

拡張機能のデプロイの状態に関するデータは、Azure portal で取得できます。

次のトラブルシューティング手順は、すべての VM 張機能に適用されます。

1. ゲスト エージェント ログを調べるには、拡張機能がプロビジョニングされたときのアクティビティを `%SystemDrive%\ProgramData\GuestConfig\ext_mgr_logs` (Windows の場合) および `/var/lib/GuestConfig/ext_mgr_logs` (Linux の場合) で確認します。

2. Windows の場合、特定の拡張機能の詳細な拡張機能ログは `%SystemDrive%\ProgramData\GuestConfig\extension_logs\<Extension>` で確認できます。 Linux の場合、拡張機能の出力は、インストールされる拡張機能ごとに `/var/log/GuestConfig/extension_logs` のファイルに記録されます。

3. エラーコードや既知の問題などについて、拡張機能固有のドキュメントのトラブルシューティングのセクションを確認します。各拡張機能の追加のトラブルシューティング情報については、拡張機能の概要の「**トラブルシューティングとサポート**」セクションを参照してください。 これには、ログに書き込まれたエラー コードの説明が含まれます。 拡張機能に関する記事へのリンクは、前に示した[拡張機能の表](#extensions)にあります。

4. システム ログを確認します。 排他的なパッケージ マネージャーのアクセスを必要とする別のアプリケーションのインストールが長時間実行されている場合など、拡張機能に干渉する可能性のある他の操作をチェックします。

## <a name="next-steps"></a>次のステップ

- [Azure Policy](../../governance/policy/overview.md) を使用してマシンを管理する方法を確認します。VM の[ゲスト構成](../../governance/policy/concepts/guest-configuration.md)、マシンの報告先が、予期された Log Analytics ワークスペースであることの確認、[VM での Azure Monitor](../../azure-monitor/insights/vminsights-enable-policy.md) を使用した監視の有効化などの方法です。

- [Log Analytics エージェント](../../azure-monitor/platform/log-analytics-agent.md)の詳細を確認します。 マシン上で実行されている OS とワークロードをプロアクティブに監視したい場合、それを Automation Runbook やソリューション (Update Management など) を使用して管理したい場合、または他の Azure サービス ([Azure Security Center](../../security-center/security-center-intro.md) など) を使用したい場合は、Windows 用および Linux 用の Log Analytics エージェントが必要となります。