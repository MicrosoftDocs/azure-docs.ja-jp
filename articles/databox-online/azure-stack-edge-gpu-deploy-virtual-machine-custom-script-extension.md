---
title: Azure Stack Edge Pro デバイス上の VM に対するカスタム スクリプト拡張機能の使用
description: テンプレートを使用して、Azure Stack Edge Pro デバイスで実行されている仮想マシン (VM) に、カスタム スクリプト拡張機能をインストールする方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 2d2e7d403ab3e9cc7e8e17de53b6e821ec24caa1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102438014"
---
# <a name="deploy-custom-script-extension-on-vms-running-on-your-azure-stack-edge-pro-device"></a>Azure Stack Edge Pro デバイスで実行されている VM にカスタム スクリプト拡張機能をデプロイする

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

カスタム スクリプト拡張機能では、Azure Stack Edge Pro デバイスで実行されている仮想マシンにスクリプトまたはコマンドをダウンロードし、それらを実行できます。 この記事では、Azure Resource Manager テンプレートを使ってカスタム スクリプト拡張機能をインストールし、実行する方法について説明します。 

この記事は、Azure Stack Edge Pro GPU、Azure Stack Edge Pro R、および Azure Stack Edge Mini R デバイスに適用されます。

## <a name="about-custom-script-extension"></a>カスタム スクリプト拡張機能について

カスタム スクリプト拡張機能は、デプロイ後の構成、ソフトウェアのインストール、その他の構成タスクや管理タスクに役立ちます。 スクリプトは、Azure Storage や他のアクセス可能なインターネットの場所からダウンロードできます。また、拡張機能のランタイムにスクリプトやコマンド提供することもできます。

カスタム スクリプト拡張機能は Azure Resource Manager テンプレートと統合します。 また、Azure CLI、PowerShell、または Azure Virtual Machines REST API を使って実行することもできます。

## <a name="os-for-custom-script-extension"></a>カスタムスクリプト拡張機能に対応している OS

#### <a name="supported-os-for-custom-script-extension-on-windows"></a>Windows 上のカスタム スクリプト拡張機能に対応している OS

Windows 用のカスタム スクリプト拡張機能は、次の OS で動作します。 その他のバージョンも機能する可能性がありますが、Azure Stack Edge Pro デバイスで実行されている VM 上で社内テストされていません。

| Distribution | Version |
|---|---|
| Windows Server 2019 | コア |
| Windows Server 2016 | コア |

#### <a name="supported-os-for-custom-script-extension-on-linux"></a>Linux 上のカスタム スクリプト拡張機能に対応している OS

Linux 用のカスタム スクリプト拡張機能は、次の OS で動作します。 その他のバージョンも機能する可能性がありますが、Azure Stack Edge Pro デバイスで実行されている VM 上で社内テストされていません。

| Distribution | Version |
|---|---|
| Linux: Ubuntu | 18.04 LTS |
| Linux: Red Hat Enterprise Linux | 7.4、7.5、7.7 |

<!--### Script location

Instead of the scripts, in this article, we pass a command to execute via the Custom Script Extension. 

### Internet Connectivity

To download a script externally such as from GitHub or Azure Storage, make sure that the port on which you enable compute network, is connected to the internet. 

If your script is on a local server, then you may still need additional firewall and Network Security Group ports need to be opened.

> [!NOTE]
> Before you install the Custom Script extension, make sure that the port enabled for compute network on your device is connected to Internet and has access. -->

## <a name="prerequisites"></a>[前提条件]

1. クライアント コンピューターに [VM テンプレートとパラメーター ファイルをダウンロード](https://aka.ms/ase-vm-templates)します。 作業ディレクトリとして使用するディレクトリにダウンロードしたものを解凍します。

1. デバイス上に VM を作成してデプロイする必要があります。 VM を作成するには、[テンプレートを使用して Azure Stack Edge Pro に VM をデプロイする方法](azure-stack-edge-gpu-deploy-virtual-machine-templates.md)に関するページのすべての手順に従います。

    外部 (GitHub や Azure Storage など) からスクリプトをダウンロードし、コンピューティング ネットワークを構成する必要がある場合は、インターネットに接続されているポートをコンピューティング用に有効化してください。 これにより、スクリプトをダウンロードできます。

    次の例では、ポート 2 がインターネットに接続され、コンピューティング ネットワークを有効にするために使用されています。 前の手順で Kubernetes が不要であることがわかっている場合は、Kubernetes ノード IP と外部サービス IP の割り当てをスキップできます。

    ![インターネットに接続されているポートでのコンピューティング設定の有効化](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/enable-compute-network-1.png)

## <a name="install-custom-script-extension"></a>カスタム スクリプト拡張機能のインストール

VM のオペレーティング システムに応じて、Windows 用または Linux 用のカスタム スクリプト拡張機能をインストールできます。
 

### <a name="custom-script-extension-for-windows"></a>Windows でのカスタムのスクリプト拡張機能

デバイスで実行されている VM に対して Windows 用のカスタム スクリプト拡張機能をデプロイするには、`addCSExtWindowsVM.parameters.json` パラメーター ファイルを編集し、その後でテンプレートをデプロイします`addCSextensiontoVM.json`。

#### <a name="edit-parameters-file"></a>パラメーター ファイルを編集する

`addCSExtWindowsVM.parameters.json` ファイルでは、次のパラメーターを受け取ります。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "<Name of VM>" 
        },
        "extensionName": {
            "value": "<Name of extension>" 
        },
        "publisher": {
            "value": "Microsoft.Compute" 
        },
        "type": {
            "value": "CustomScriptExtension" 
        },
        "typeHandlerVersion": {
            "value": "1.10" 
        },
        "settings": {
            "value": {
                "commandToExecute" : "<Command to execute>"
            }
        }
    }
}
```
VM 名、拡張機能の名前、実行するコマンドを指定します。

この記事で使用したパラメーター ファイルのサンプルを次に示します。

```powershell
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "VM5" 
        },
        "extensionName": {
            "value": "CustomScriptExtension" 
        },
        "publisher": {
            "value": "Microsoft.Compute" 
        },
        "type": {
            "value": "CustomScriptExtension" 
        },
        "typeHandlerVersion": {
            "value": "1.10" 
        },
        "settings": {
            "value": {
                "commandToExecute" : "md C:\\Users\\Public\\Documents\\test"
            }
        }
    }
}
```
#### <a name="deploy-template"></a>テンプレートのデプロイ

テンプレート `addCSextensiontoVM.json` をデプロイします。 このテンプレートにより、既存の VM に拡張機能がデプロイされます。 次のコマンドを実行します。

```powershell
$templateFile = "<Path to addCSExtensiontoVM.json file>"
$templateParameterFile = "<Path to addCSExtWindowsVM.parameters.json file>"
$RGName = "<Resource group name>"
New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "<Deployment name>"
```
> [!NOTE]
> 拡張機能のデプロイは長時間実行されるジョブであり、完了するまでに約 10 分かかります。

出力例を次に示します。

```powershell
PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\ExtensionTemplates\addCSExtensiontoVM.json"
PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\ExtensionTemplates\addCSExtWindowsVM.parameters.json"
PS C:\WINDOWS\system32> $RGName = "myasegpuvm1"
PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "deployment7"

DeploymentName          : deployment7
ResourceGroupName       : myasegpuvm1
ProvisioningState       : Succeeded
Timestamp               : 12/17/2020 10:07:44 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          vmName           String                     VM5
                          extensionName    String                     CustomScriptExtension
                          publisher        String                     Microsoft.Compute
                          type             String                     CustomScriptExtension
                          typeHandlerVersion  String                     1.10
                          settings         Object                     {
                            "commandToExecute": "md C:\\Users\\Public\\Documents\\test"
                          }

Outputs                 :
DeploymentDebugLogLevel :

PS C:\WINDOWS\system32>
```
#### <a name="track-deployment"></a>デプロイの追跡

特定の VM の拡張機能のデプロイ状態を確認するには、次のコマンドを実行します。 

```powershell
Get-AzureRmVMExtension -ResourceGroupName <Name of resource group> -VMName <Name of VM> -Name <Name of the extension>
```
出力例を次に示します。

```powershell
PS C:\WINDOWS\system32> Get-AzureRmVMExtension -ResourceGroupName myasegpuvm1 -VMName VM5 -Name CustomScriptExtension

ResourceGroupName       : myasegpuvm1
VMName                  : VM5
Name                    : CustomScriptExtension
Location                : dbelocal
Etag                    : null
Publisher               : Microsoft.Compute
ExtensionType           : CustomScriptExtension
TypeHandlerVersion      : 1.10
Id                      : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/myasegpuvm1/providers/Microsoft.Compute/virtualMachines/VM5/extensions/CustomScriptExtension
PublicSettings          : {
                            "commandToExecute": "md C:\\Users\\Public\\Documents\\test"
                          }
ProtectedSettings       :
ProvisioningState       : Creating
Statuses                :
SubStatuses             :
AutoUpgradeMinorVersion : True
ForceUpdateTag          :

PS C:\WINDOWS\system32>
```

> [!NOTE]
> デプロイが完了すると、`ProvisioningState` が `Succeeded` に変わります。

デプロイ先の仮想マシンの次のフォルダーにあるファイルに拡張機能出力のログが記録されます。 

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

デプロイ先の仮想マシンの次のフォルダーに指定のファイルがダウンロードされます。

```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads\<n>
```
<n> は 10 進整数であり、拡張機能の実行間で変わる可能性があります。 1\.* の値は、拡張機能の実際の現在の `typeHandlerVersion` の値と一致します。 たとえば、この例での実際のディレクトリは `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.10.9\Downloads\0` でした。 


この例の場合、カスタム拡張機能に対して実行するコマンドは `md C:\\Users\\Public\\Documents\\test` でした。 拡張機能が正常にインストールされたら、コマンドで指定したパスにある VM にディレクトリが作成されたことを確認できます。 


### <a name="custom-script-extension-for-linux"></a>Linux 用カスタム スクリプト拡張機能

デバイスで実行されている VM に対して Windows 用のカスタム スクリプト拡張機能をデプロイするには、`addCSExtLinuxVM.parameters.json` パラメーター ファイルを編集し、その後でテンプレートをデプロイします`addCSExtensiontoVM.json`。

#### <a name="edit-parameters-file"></a>パラメーター ファイルを編集する

`addCSExtLinuxVM.parameters.json` ファイルでは、次のパラメーターを受け取ります。

```powershell
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "<Name of your VM>" 
        },
        "extensionName": {
            "value": "<Name of your extension>" 
        },
        "publisher": {
            "value": "Microsoft.Azure.Extensions" 
        },
        "type": {
            "value": "CustomScript" 
        },
        "typeHandlerVersion": {
            "value": "2.0" 
        },
        "settings": {
            "value": {
                "commandToExecute" : "<Command to execute>"
            }
        }
    }
}
```
VM 名、拡張機能の名前、実行するコマンドを指定します。

この記事で使用したパラメーター ファイルのサンプルを次に示します。

```powershell
$templateFile = "<Path to addCSExtensionToVM.json file>"
$templateParameterFile = "<Path to addCSExtLinuxVM.parameters.json file>"
$RGName = "<Resource group name>"
New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "<Deployment name>"
``` 

> [!NOTE]
> 拡張機能のデプロイは長時間実行されるジョブであり、完了するまでに約 10 分かかります。

出力例を次に示します。

```powershell
PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\ExtensionTemplates\addCSExtensionToVM.json"
PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\ExtensionTemplates\addCSExtLinuxVM.parameters.json"
PS C:\WINDOWS\system32> $RGName = "myasegpuvm1"
PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "deployment99"

DeploymentName          : deployment99
ResourceGroupName       : myasegpuvm1
ProvisioningState       : Succeeded
Timestamp               : 12/18/2020 1:55:23 AM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          vmName           String                     VM6
                          extensionName    String                     LinuxCustomScriptExtension
                          publisher        String                     Microsoft.Azure.Extensions
                          type             String                     CustomScript
                          typeHandlerVersion  String                     2.0
                          settings         Object                     {
                            "commandToExecute": "sudo echo 'some text' >> /home/Administrator/file2.txt"
                          }

Outputs                 :
DeploymentDebugLogLevel :

PS C:\WINDOWS\system32>
```

`commandToExecute` は `/home/Administrator` ディレクトリにファイル `file2.txt` を作成するように設定されました。ファイルの内容は `some text` です。 この場合は、指定されたパスにファイルが作成されたことを確認できます。

```powershell
Administrator@VM6:~$ dir
file2.txt
Administrator@VM6:~$ cat file2.txt
some text
Administrator@VM6:
```

#### <a name="track-deployment-status"></a>デプロイの状態の追跡    
    
テンプレートのデプロイは、長時間実行されるジョブです。 特定の VM の拡張機能のデプロイ状態を確認するには、別の PowerShell セッションを開きます (管理者として実行します)。 次のコマンドを実行します。 

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName <VM Name> -Name <Extension Name>
```
出力例を次に示します。 

```powershell
PS C:\WINDOWS\system32> Get-AzureRmVMExtension -ResourceGroupName myasegpuvm1 -VMName VM5 -Name CustomScriptExtension

ResourceGroupName       : myasegpuvm1
VMName                  : VM5
Name                    : CustomScriptExtension
Location                : dbelocal
Etag                    : null
Publisher               : Microsoft.Compute
ExtensionType           : CustomScriptExtension
TypeHandlerVersion      : 1.10
Id                      : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/myasegpuvm1/providers/Microsoft.Compute/virtualMachines/VM5/extensions/CustomScriptExtension
PublicSettings          : {
                            "commandToExecute": "md C:\\Users\\Public\\Documents\\test"
                          }
ProtectedSettings       :
ProvisioningState       : Creating
Statuses                :
SubStatuses             :
AutoUpgradeMinorVersion : True
ForceUpdateTag          :

PS C:\WINDOWS\system32>
```

> [!NOTE]
> デプロイが完了すると、`ProvisioningState` が `Succeeded` に変わります。

拡張機能の実行の出力は、`/var/lib/waagent/custom-script/download/0/` ファイルにログ記録されます。


## <a name="remove-custom-script-extension"></a>カスタムスクリプト拡張機能の削除

カスタム スクリプト拡張機能を削除するには、次のコマンドを使用します。

`Remove-AzureRmVMExtension -ResourceGroupName <Resource group name> -VMName <VM name> -Name <Extension name>`

出力例を次に示します。

```powershell
PS C:\WINDOWS\system32> Remove-AzureRmVMExtension -ResourceGroupName myasegpuvm1 -VMName VM6 -Name LinuxCustomScriptExtension
Virtual machine extension removal operation
This cmdlet will remove the specified virtual machine extension. Do you want to continue?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Yes
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK
```


## <a name="next-steps"></a>次のステップ

[Azure Resource Manager コマンドレット](/powershell/module/azurerm.resources/?view=azurermps-6.13.0&preserve-view=true)
