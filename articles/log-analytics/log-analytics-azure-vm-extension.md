---
title: "Log Analytics に Azure 仮想マシンを接続する | Microsoft Docs"
description: "Azure で実行されている Windows および Linux 仮想マシンでログとメトリックを収集する場合は、Log Analytics Azure VM 拡張機能をインストールすることをお勧めします。 Azure VM に Log Analytics 仮想マシン拡張機能をインストールするには、Azure Portal または PowerShell を使用することができます。"
services: log-analytics
documentationcenter: 
author: richrundmsft
manager: jochan
editor: 
ms.assetid: ca39e586-a6af-42fe-862e-80978a58d9b1
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: richrund
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 5bb3a67c999c1d41c50b2b660a97a53125511633
ms.lasthandoff: 03/25/2017


---
# <a name="connect-azure-virtual-machines-to-log-analytics-with-a-log-analytics-agent"></a>Log Analytics エージェントで Log Analytics に Azure 仮想マシンを接続する

Windows および Linux コンピューターの場合、ログとメトリックを収集するための推奨される方法は、Log Analytics エージェントをインストールすることです。

Azure 仮想マシンに Log Analytics エージェントをインストールする方法としては、Log Analytics VM 拡張機能を使用するのが最も簡単です。  この拡張機能を使用すると、インストール プロセスが簡略化され、指定した Log Analytics ワークスペースにデータを送信するようにエージェントが自動的に構成されます。 また、エージェントは自動的にアップグレードされるため、最新の機能と修正プログラムを利用できます。

Windows 仮想マシンの場合は、*Microsoft Monitoring Agent* という仮想マシン拡張機能を有効にします。
Linux 仮想マシンの場合は、*OMS Agent For Linux* という仮想マシン拡張機能を有効にします。

[Azure 仮想マシン拡張機能](../virtual-machines/virtual-machines-windows-extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)と[Linux エージェント](../virtual-machines/virtual-machines-linux-agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)の詳細情報を確認してください。

ログ データにエージェント ベースの収集を使用する場合は、[Log Analytics のデータ ソース](log-analytics-data-sources.md)を構成して、収集するログとメトリックを指定する必要があります。

> [!IMPORTANT]
> [Azure 診断](log-analytics-azure-storage.md)を使用してログ データのインデックスを作成するように Log Analytics を構成したうえで、さらに同じログを収集するようにエージェントを構成した場合、ログは 2 回収集されます。 両方のデータ ソースに対して料金が発生します。 エージェントをインストールした場合は、エージェントのみでログ データを収集することをお勧めします。ログ データを Azure 診断から収集するように Log Analytics を構成することは避けてください。
>
>

Log Analytics 仮想マシン拡張機能は、次の 3 とおりの方法で簡単に有効にすることができます。

* Azure Portal を使用する方法
* Azure PowerShell を使用する方法
* Azure Resource Manager テンプレートを使用する方法

## <a name="enable-the-vm-extension-in-the-azure-portal"></a>Azure Portal で VM 拡張機能を有効にする
[Azure Portal](https://portal.azure.com) を使用して Log Analytics 用のエージェントをインストールし、そのエージェントを実行する Azure 仮想マシンを接続できます。

### <a name="to-install-the-log-analytics-agent-and-connect-the-virtual-machine-to-a-log-analytics-workspace"></a>Log Analytics エージェントをインストールし、仮想マシンを Log Analytics ワークスペースに接続するには
1. [Azure Portal](http://portal.azure.com) にサインインします。
2. ポータルの左側にある **[参照]** を選択し、**[Log Analytics (OMS)]** に移動して選択します。
3. Log Analytics ワークスペースの一覧から、Azure VM で使用するワークスペースを選択します。  
   ![OMS ワークスペース](./media/log-analytics-azure-vm-extension/oms-connect-azure-01.png)
4. **[Log analytics management (Log Analytics 管理)]** の下にある **[仮想マシン]** を選択します。  
   ![仮想マシン](./media/log-analytics-azure-vm-extension/oms-connect-azure-02.png)
5. **[仮想マシン]** の一覧で、エージェントをインストールする仮想マシンを選択します。 VM の **OMS 接続の状態**は **[Not connected (未接続)]** になっています。  
   ![VM 未接続](./media/log-analytics-azure-vm-extension/oms-connect-azure-03.png)
6. 仮想マシンの詳細で **[接続]** を選択します。 エージェントが自動的にインストールされ、Log Analytics のワークスペース用に構成されます。 このプロセスは数分かかります。その間、OMS 接続の状態が "*接続中...*" になります。  
   ![VM の接続](./media/log-analytics-azure-vm-extension/oms-connect-azure-04.png)
7. エージェントをインストールして接続すると、**[OMS 接続]** の状態が **[このワークスペース]** に変わります。  
   ![接続中](./media/log-analytics-azure-vm-extension/oms-connect-azure-05.png)

## <a name="enable-the-vm-extension-using-powershell"></a>PowerShell を使用して VM 拡張機能を有効にする
PowerShell を使用して仮想マシンを構成する場合は、**ワークスペース ID** と**ワークスペース キー**を指定する必要があります。 JSON 構成では、プロパティ名の**大文字と小文字が区別**されます。

この ID とキーは、OMS ポータルの **[設定]** ページで確認できるほか、前出の例で使用した PowerShell コマンドで確認することができます。

![ワークスペース ID と主キー](./media/log-analytics-azure-vm-extension/oms-analyze-azure-sources.png)

Azure のクラシック仮想マシンと Resource Manager 仮想マシンとでは、使用するコマンドが異なります。 以下、クラシックと Resource Manager の両方の仮想マシンの例を紹介しています。

クラシック仮想マシンの場合は、次の PowerShell の例を使用します。

```
Add-AzureAccount

$workspaceId = "enter workspace ID here"
$workspaceKey = "enter workspace key here"
$hostedService = "enter hosted service here"

$vm = Get-AzureVM –ServiceName $hostedService

# For Windows VM uncomment the following line
# Set-AzureVMExtension -VM $vm -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionName 'MicrosoftMonitoringAgent' -Version '1.*' -PublicConfiguration "{'workspaceId': '$workspaceId'}" -PrivateConfiguration "{'workspaceKey': '$workspaceKey' }" | Update-AzureVM -Verbose

# For Linux VM uncomment the following line
# Set-AzureVMExtension -VM $vm -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionName 'OmsAgentForLinux' -Version '1.*' -PublicConfiguration "{'workspaceId': '$workspaceId'}" -PrivateConfiguration "{'workspaceKey': '$workspaceKey' }" | Update-AzureVM -Verbose
```

Resource Manager 仮想マシンの場合は、次の PowerShell の例を使用します。

```
Login-AzureRMAccount
Select-AzureSubscription -SubscriptionId "**"

$workspaceName = "your workspace name"
$VMresourcegroup = "**"
$VMresourcename = "**"

$workspace = (Get-AzureRmOperationalInsightsWorkspace).Where({$_.Name -eq $workspaceName})

if ($workspace.Name -ne $workspaceName)
{
    Write-Error "Unable to find OMS Workspace $workspaceName. Do you need to run Select-AzureRMSubscription?"
}

$workspaceId = $workspace.CustomerId
$workspaceKey = (Get-AzureRmOperationalInsightsWorkspaceSharedKeys -ResourceGroupName $workspace.ResourceGroupName -Name $workspace.Name).PrimarySharedKey

$vm = Get-AzureRmVM -ResourceGroupName $VMresourcegroup -Name $VMresourcename
$location = $vm.Location

# For Windows VM uncomment the following line
# Set-AzureRmVMExtension -ResourceGroupName $VMresourcegroup -VMName $VMresourcename -Name 'MicrosoftMonitoringAgent' -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionType 'MicrosoftMonitoringAgent' -TypeHandlerVersion '1.0' -Location $location -SettingString "{'workspaceId': '$workspaceId'}" -ProtectedSettingString "{'workspaceKey': '$workspaceKey'}"

# For Linux VM uncomment the following line
# Set-AzureRmVMExtension -ResourceGroupName $VMresourcegroup -VMName $VMresourcename -Name 'OmsAgentForLinux' -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionType 'OmsAgentForLinux' -TypeHandlerVersion '1.0' -Location $location -SettingString "{'workspaceId': '$workspaceId'}" -ProtectedSettingString "{'workspaceKey': '$workspaceKey'}"


```

## <a name="deploy-the-vm-extension-using-a-template"></a>テンプレートを使用して VM 拡張機能をデプロイする
Azure Resource Manager で、アプリケーションのデプロイと構成を定義する JSON 形式の単純なテンプレートを作成できます。 このテンプレートはリソース マネージャー テンプレートと呼ばれ、デプロイの定義を宣言できます。 テンプレートを使用すると、アプリケーションをアプリのライフサイクルを通して繰り返しデプロイできるほか、常にリソースが一貫した状態でデプロイされます。

Log Analytics エージェントを Resource Manager テンプレートの一部として含めることで、Log Analytics ワークスペースにレポートするように各仮想マシンを事前構成することができます。

Resource Manager テンプレートの詳細については、「[Azure Resource Manager のテンプレートの作成](../azure-resource-manager/resource-group-authoring-templates.md)」を参照してください。

以下の Resource Manager テンプレートは、Microsoft Monitoring Agent 拡張機能がインストールされた状態で Windows 仮想マシンをデプロイする例です。 これは典型的な仮想マシン テンプレートですが、以下のものが追加されています。

* workspaceId および workspaceName パラメーター
* Microsoft.EnterpriseCloud.Monitoring リソース拡張機能セクション
* workspaceId と workspaceSharedKey を検索するための出力

```
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "adminUsername": {
      "type": "string",
      "metadata": {
        "description": "Username for the Virtual Machine."
      }
    },
    "adminPassword": {
      "type": "securestring",
      "metadata": {
        "description": "Password for the Virtual Machine."
      }
    },
    "dnsLabelPrefix": {
       "type": "string",
       "metadata": {
          "description": "DNS Label for the Public IP. Must be lowercase. It should match with the following regular expression: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$ or it will raise an error."
       }
    },
    "workspaceId": {
      "type": "string",
      "metadata": {
        "description": "OMS workspace ID"
      }
    },
    "workspaceName": {
      "type": "string",
      "metadata": {
         "description": "OMS workspace name"
      }
    },
    "windowsOSVersion": {
      "type": "string",
      "defaultValue": "2012-R2-Datacenter",
      "allowedValues": [
        "2008-R2-SP1",
        "2012-Datacenter",
        "2012-R2-Datacenter",
        "Windows-Server-Technical-Preview"
      ],
      "metadata": {
        "description": "The Windows version for the VM. This will pick a fully patched image of this given Windows version. Allowed values: 2008-R2-SP1, 2012-Datacenter, 2012-R2-Datacenter, Windows-Server-Technical-Preview."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]",
    "apiVersion": "2015-06-15",
    "imagePublisher": "MicrosoftWindowsServer",
    "imageOffer": "WindowsServer",
    "OSDiskName": "osdiskforwindowssimple",
    "nicName": "myVMNic",
    "addressPrefix": "10.0.0.0/16",
    "subnetName": "Subnet",
    "subnetPrefix": "10.0.0.0/24",
    "storageAccountType": "Standard_LRS",
    "publicIPAddressName": "myPublicIP",
    "publicIPAddressType": "Dynamic",
    "vmStorageAccountContainerName": "vhds",
    "vmName": "MyWindowsVM",
    "vmSize": "Standard_DS1",
    "virtualNetworkName": "MyVNET",
    "resourceId": "[resourceGroup().id]",
    "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
    "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables('subnetName'))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "apiVersion": "[variables('apiVersion')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "accountType": "[variables('storageAccountType')]"
      }
    },
    {
      "apiVersion": "[variables('apiVersion')]",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "[variables('publicIPAddressName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
        "dnsSettings": {
          "domainNameLabel": "[parameters('dnsLabelPrefix')]"
        }
      }
    },
    {
      "apiVersion": "[variables('apiVersion')]",
      "type": "Microsoft.Network/virtualNetworks",
      "name": "[variables('virtualNetworkName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "addressSpace": {
          "addressPrefixes": [
            "[variables('addressPrefix')]"
          ]
        },
        "subnets": [
          {
            "name": "[variables('subnetName')]",
            "properties": {
              "addressPrefix": "[variables('subnetPrefix')]"
            }
          }
        ]
      }
    },
    {
      "apiVersion": "[variables('apiVersion')]",
      "type": "Microsoft.Network/networkInterfaces",
      "name": "[variables('nicName')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'))]",
        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
      ],
      "properties": {
        "ipConfigurations": [
          {
            "name": "ipconfig1",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIPAddressName'))]"
              },
              "subnet": {
                "id": "[variables('subnetRef')]"
              }
            }
          }
        ]
      }
    },
    {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[variables('vmName')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
        "[concat('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
      ],
      "properties": {
        "hardwareProfile": {
          "vmSize": "[variables('vmSize')]"
        },
        "osProfile": {
          "computername": "[variables('vmName')]",
          "adminUsername": "[parameters('adminUsername')]",
          "adminPassword": "[parameters('adminPassword')]"
        },
        "storageProfile": {
          "imageReference": {
            "publisher": "[variables('imagePublisher')]",
            "offer": "[variables('imageOffer')]",
            "sku": "[parameters('windowsOSVersion')]",
            "version": "latest"
          },
          "osDisk": {
            "name": "osdisk",
            "vhd": {
              "uri": "[concat('http://',variables('storageAccountName'),'.blob.core.windows.net/',variables('vmStorageAccountContainerName'),'/',variables('OSDiskName'),'.vhd')]"
            },
            "caching": "ReadWrite",
            "createOption": "FromImage"
          }
        },
        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
            }
          ]
        },
        "diagnosticsProfile": {
          "bootDiagnostics": {
             "enabled": "true",
             "storageUri": "[concat('http://',variables('storageAccountName'),'.blob.core.windows.net')]"
          }
        }
      },
      "resources": [
        {
          "type": "extensions",
          "name": "Microsoft.EnterpriseCloud.Monitoring",
          "apiVersion": "[variables('apiVersion')]",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
          ],
          "properties": {
            "publisher": "Microsoft.EnterpriseCloud.Monitoring",
            "type": "MicrosoftMonitoringAgent",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
              "workspaceId": "[parameters('workspaceId')]"
            },
            "protectedSettings": {
              "workspaceKey": "[listKeys(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-03-20').primarySharedKey]"
            }
          }
        }
      ]
    }
  ],
  "outputs": {
      "sharedKeyOutput": {
         "value": "[listKeys(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName')), '2015-03-20').primarySharedKey]",
         "type": "string"
      },
      "workspaceIdOutput": {
         "value": "[reference(concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName')), '2015-03-20').customerId]",
        "type" : "string"
      }
  }
}
```

次の PowerShell コマンドを使用して、テンプレートをデプロイすることができます。

```
New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile $templateFilePath
```

## <a name="troubleshooting-the-log-analytics-vm-extension"></a>Log Analytics VM 拡張機能のトラブルシューティング
通常、何らかの問題が発生すると、Azure Portal または Azure PowerShell からメッセージが送信されます。

1. [Azure ポータル](http://portal.azure.com)にサインインします。
2. VM を検索し、VM の詳細を表示します。
3. **[拡張機能]** をクリックして、OMS 拡張機能が有効になっているかどうかを確認します。

   ![VM の拡張機能の表示](./media/log-analytics-azure-vm-extension/oms-vmview-extensions.png)

4. *MicrosoftMonitoringAgent* (Windows) 拡張機能または *OmsAgentForLinux* (Linux) 拡張機能をクリックして詳細を表示します。 

   ![VM の拡張機能の詳細](./media/log-analytics-azure-vm-extension/oms-vmview-extensiondetails.png)

### <a name="troubleshooting-windows-virtual-machines"></a>Windows 仮想マシンのトラブルシューティング
*Microsoft Monitoring Agent* VM エージェント拡張機能のインストールまたはレポートが正しく機能しない場合は、以下の手順で問題をトラブルシューティングしてください。

1. Azure VM エージェントがインストールされ、正しく動作しているかどうかを [KB 2965986](https://support.microsoft.com/kb/2965986#mt1) の手順に従って確認します。
   * さらに VM エージェントのログ ファイル (`C:\WindowsAzure\logs\WaAppAgent.log`) を確認します。
   * ログが存在しない場合、VM エージェントがインストールされていません。
     * [クラシック VM に Azure VM エージェントをインストールします。](../virtual-machines/windows/classic/agents-and-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
2. 次の手順に従って、Microsoft Monitoring Agent 拡張機能のハート ビート タスクが実行中であることを確認します。
   * 仮想マシンにログインします。
   * タスク スケジューラを開いて `update_azureoperationalinsight_agent_heartbeat` タスクを探します。
   * タスクが有効になっていて 1 分おきに実行されていることを確認します。
   * `C:\WindowsAzure\Logs\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\heartbeat.log` にあるハート ビートのログファイルを確認します。
3. `C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent` で Microsoft Monitoring Agent VM 拡張機能のログ ファイルを確認します。
4. 仮想マシンで PowerShell スクリプトを実行できることを確認します。
5. C:\Windows\temp に対するアクセス許可が変更されていないことを確認します。
6. 仮想マシン上の管理者特権の PowerShell ウィンドウで「`  (New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg').GetCloudWorkspaces() | Format-List`」と入力し、Microsoft Monitoring Agent の状態を確認します。
7. `C:\Windows\System32\config\systemprofile\AppData\Local\SCOM\Logs` にある Microsoft Monitoring Agent のセットアップ ログ ファイルを確認します。

詳細については、[Windows 拡張機能のトラブルシューティング](../virtual-machines/virtual-machines-windows-extensions-troubleshoot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関するページを参照してください。

### <a name="troubleshooting-linux-virtual-machines"></a>Linux 仮想マシンのトラブルシューティング
*OMS Agent for Linux* VM エージェント拡張機能のインストールまたはレポートが正しく機能しない場合は、以下の手順で問題をトラブルシューティングしてください。

1. 拡張機能の状態が "*不明*" になっている場合は、Azure VM エージェントがインストールされて正常に動作しているかどうかを VM エージェントのログ ファイル (`/var/log/waagent.log`) で確認してください。
   * ログが存在しない場合、VM エージェントがインストールされていません。
   * [Linux VM に Azure VM エージェントをインストールします。](../virtual-machines/virtual-machines-linux-agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
2. それ以外の異常な状態については、OMS Agent for Linux VM 拡張機能のログ ファイル (`/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/extension.log` および `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/CommandExecution.log`) を確認してください。
3. 拡張機能が正常な状態であるにもかかわらず、データがアップロードされない場合は、OMS Agent for Linux のログ ファイル (`/var/opt/microsoft/omsagent/log/omsagent.log`) を確認してください。

詳細については、[Linux 拡張機能のトラブルシューティング](../virtual-machines/virtual-machines-linux-extensions-troubleshoot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ
* 収集するログとメトリックを指定するために、 [Log Analytics のデータ ソース](log-analytics-data-sources.md) を構成します。
* 「[ソリューション ギャラリーから Log Analytics ソリューションを追加する](log-analytics-add-solutions.md)」を参照してください。
* Azure で実行されている他のリソースの[データを Azure 診断を使用して収集](log-analytics-azure-storage.md)します。

Azure 以外の場所にあるコンピューターについては、以下の記事で説明されている方法で Log Analytics エージェントをインストールできます。

* [Windows コンピューターを Log Analytics に接続する](log-analytics-windows-agents.md)
* [Linux コンピューターを Log Analytics に接続する](log-analytics-linux-agents.md)

