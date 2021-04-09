---
title: Azure Stack Edge Pro デバイス上の GPU VM の概要とデプロイ
description: テンプレートを使用して、Azure Stack Edge Pro デバイスに GPU 仮想マシン (VM) を作成し、管理する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: ff805b758dce05a66764ab1ff08e53378c946362
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102438184"
---
# <a name="gpu-vms-for-your-azure-stack-edge-pro-device"></a>Azure Stack Edge Pro デバイス用の GPU VM

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

この記事では、Azure Stack Edge Pro デバイス上の GPU 仮想マシン (VM) の概要を示します。 この記事では、GPU VM を作成し、GPU ドライバー拡張機能をインストールして適切な Nvidia ドライバーをインストールする方法について説明します。 Azure Resource Manager テンプレートを使用して GPU VM を作成し、GPU ドライバー拡張機能をインストールします。 

この記事は、Azure Stack Edge Pro GPU デバイスと Azure Stack Edge Pro R デバイスに適用されます。

## <a name="about-gpu-vms"></a>GPU VM について

Azure Stack Edge Pro デバイスには、Nvidia の Tesla T4 GPU が 1 つまたは 2 つ搭載されています。 これらのデバイスに GPU 高速 VM ワークロードをデプロイするには、GPU 最適化 VM サイズを使用します。 たとえば、T4 GPU を利用する推論ワークロードをデプロイするには、NC T4 v3 シリーズを使用する必要があります。 

詳細については、[NC T4 v3-シリーズ VM](../virtual-machines/nct4-v3-series.md) に関するページを参照してください。

## <a name="supported-os-and-gpu-drivers"></a>サポートされている OS ドライバーと GPU ドライバー 

Azure N シリーズ VM の GPU 機能を利用するには、Nvidia GPU ドライバーをインストールする必要があります。 

Nvidia GPU ドライバー拡張機能により、適切な Nvidia CUDA ドライバーまたは GRID ドライバーがインストールされます。 この拡張機能は、Azure Resource Manager テンプレートを使用してインストールしたり管理したりできます。

### <a name="supported-os-for-gpu-extension-for-windows"></a>Windows 用の GPU 拡張機能でサポートされている OS

この拡張機能は、次のオペレーティング システム (OS) に対応しています。 その他のバージョンも機能する可能性がありますが、Azure Stack Edge Pro デバイスで実行されている GPU VM 上で社内テストされていません。

| Distribution | Version |
|---|---|
| Windows Server 2019 | コア |
| Windows Server 2016 | コア |

### <a name="supported-os-for-gpu-extension-for-linux"></a>Linux 用の GPU 拡張でサポートされている OS

この拡張機能では、特定の OS バージョンのドライバー サポートに応じて、次の OS ディストリビューションがサポートされています。 その他のバージョンも機能する可能性がありますが、Azure Stack Edge Pro デバイスで実行されている GPU VM 上で社内テストされていません。


| Distribution | Version |
|---|---|
| Ubuntu | 18.04 LTS |
| Red Hat Enterprise Linux | 7.4 |


## <a name="gpu-vms-and-kubernetes"></a>GPU VM と Kubernetes

デバイスで Kubernetes が構成されている場合は、デバイスに GPU VM をデプロイする前に、次の考慮事項を確認してください。

#### <a name="for-1-gpu-device"></a>1 GPU デバイスの場合 

- **1 つの GPU VM を作成してから、デバイスで Kubernetes を構成します**。このシナリオでは、GPU VM の作成と Kubernetes の構成の両方が正常に実行されます。 この場合、Kubernetes からは GPU にアクセスできません。

- **デバイスで Kubernetes を構成してから、1 つの GPU VM を作成します**。このシナリオでは、Kubernetes によってデバイス上の GPU が要求され、使用可能な GPU リソースがないために VM の作成は失敗します。

#### <a name="for-2-gpu-device"></a>2 GPU デバイスの場合

- **1 つの GPU VM を作成してから、デバイスで Kubernetes を構成します**。このシナリオでは、作成した GPU VM によってデバイス上の 1 つの GPU が要求され、Kubernetes の構成も正常に完了して、残りの 1 つの GPU が要求されます。 

- **2 つの GPU VM を作成してらか、デバイスで Kubernetes を構成します**。このシナリオでは、GPU VM によってデバイス上の 2 つの GPU が要求され、Kubernetes は GPU なしで正常に構成されます。 

- **デバイスで Kubernetes を構成してから、1 つの GPU VM を作成します**。このシナリオでは、Kubernetes によってデバイス上の両方の GPU が要求され、使用可能な GPU リソースがないために VM の作成は失敗します。

デバイスで GPU VM が実行されていて、Kubernetes も構成されている場合は、VM の割り当てが解除されるたびに (Stop-AzureRmVM または Remove-AzureRmVM を使用して VM を停止または削除すると)、Kubernetes クラスターによってデバイス上の使用可能なすべての GPU が要求されるというリスクがあります。 このようなインスタンスでは、デバイスにデプロイされている GPU VM を再起動したり、GPU VM を作成したりできません。


## <a name="create-gpu-vms"></a>GPU VM の作成

デバイスに GPU VM をデプロイする場合は、次の手順に従います。

1. デバイスで Kubernetes も実行されるかどうかを確認します。 デバイスで Kubernetes が実行される場合は、最初に GPU VM を作成してから、Kubernetes を構成する必要があります。 最初に Kubernetes が構成されている場合は、使用可能なすべての GPU リソースが要求され、GPU VM の作成は失敗します。

1. クライアント コンピューターに [VM テンプレートとパラメーター ファイルをダウンロード](https://aka.ms/ase-vm-templates)します。 それを、作業ディレクトリとして使用するディレクトリに解凍します。

1. GPU VM を作成するには、次の相違点を除き、[テンプレートを使用して Azure Stack Edge Pro に VM をデプロイする方法](azure-stack-edge-gpu-deploy-virtual-machine-templates.md)に関するページのすべての手順に従います。 

    1. コンピューティング ネットワークを構成するときに、インターネットに接続されているポートをコンピューティング用に有効にします。 これにより、GPU VM 用の GPU 拡張機能に必要な GPU ドライバーをダウンロードできます。

        次の例では、ポート 2 がインターネットに接続され、コンピューティング ネットワークを有効にするために使用されています。 前の手順で Kubernetes が不要であることがわかっている場合は、Kubernetes ノード IP と外部サービス IP の割り当てをスキップできます。

        ![インターネットに接続されているポートでのコンピューティング設定の有効化](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/enable-compute-network-1.png)

            
    1. テンプレートを使用して VM を作成します。 GPU VM のサイズを指定する場合は、GPU VM でサポートされている NCasT4-v3 シリーズを `CreateVM.parameters.json` で使用してください。 詳細については、[GPU VM でサポートされている VM サイズ](azure-stack-edge-gpu-virtual-machine-sizes.md#ncast4_v3-series-preview)に関するページを参照してください。

        ```json
            "vmSize": {
          "value": "Standard_NC4as_T4_v3"
        },
        ```

    1. GPU VM が正常に作成されると、Azure portal の Azure Stack Edge リソース内の仮想マシンの一覧にこの VM が表示されます。

        ![Azure portal 内の仮想マシンの一覧の GPU VM](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/list-virtual-machine-1.png)

1. VM を選択し、詳細にドリルダウンします。 VM に割り当てられている IP をコピーします。

    ![Azure portal 内の GPU VM に割り当てられている IP](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/get-ip-gpu-virtual-machine-1.png)

1. VM が作成されたら、拡張機能テンプレートを使用して GPU 拡張機能をデプロイします。 Linux VM の場合は、[Linux 用の GPU 拡張機能のインストール](#gpu-extension-for-linux)に関するページを参照してください。Windows VM の場合は、[Windows 用の GPU 拡張機能のインストール](#gpu-extension-for-windows)に関するページを参照してください。

1. GPU 拡張機能のインストールを確認するには、GPU VM に接続します。
    1. Windows VM を使用している場合は、「[Windows VM への接続](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#connect-to-a-windows-vm)」の手順に従ってください。 [インストールを確認します](#verify-windows-driver-installation)。
    1. Linux VM を使用している場合は、「[Linux VM への接続](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#connect-to-a-linux-vm)」の手順に従ってください。 [インストールを確認します](#verify-linux-driver-installation)。

1. 必要に応じて、コンピューティング ネットワークを任意のものに切り替えることができます。 


> [!NOTE]
> デバイス ソフトウェアのバージョンを 2012 からそれ以降に更新する場合は、GPU VM を手動で停止する必要があります。


## <a name="install-gpu-extension"></a>GPU 拡張機能のインストール

VM のオペレーティング システムに応じて、Windows 用または Linux 用の GPU 拡張機能をインストールできます。

> [!NOTE]
> GPU 拡張機能をインストールする前に、デバイス上のコンピューティング ネットワーク用に有効になっているポートがインターネットに接続されていて、アクセス可能であることを確認してください。 GPU ドライバーは、インターネット アクセスを通じてダウンロードされます。

### <a name="gpu-extension-for-windows"></a>Windows 用の GPU 拡張機能

既存の VM 用の Nvidia GPU ドライバーをデプロイするには、`addGPUExtWindowsVM.parameters.json` パラメーター ファイルを編集してから、`addGPUextensiontoVM.json` テンプレートをデプロイします。

#### <a name="edit-parameters-file"></a>パラメーター ファイルを編集する

`addGPUExtWindowsVM.parameters.json` ファイルでは、次のパラメーターを受け取ります。

```json
"parameters": { 
    "vmName": {
    "value": "<name of the VM>" 
    },
    "extensionName": {
    "value": "<name for the extension. Example: windowsGpu>" 
    },
    "publisher": {
    "value": "Microsoft.HpcCompute" 
    },
    "type": {
    "value": "NvidiaGpuDriverWindows" 
    },
    "typeHandlerVersion": {
    "value": "1.3" 
    },
    "settings": {
    "value": {
    "DriverURL" : "http://us.download.nvidia.com/tesla/442.50/442.50-tesla-desktop-winserver-2019-2016-international.exe",
    "DriverCertificateUrl" : "https://go.microsoft.com/fwlink/?linkid=871664",
    "DriverType":"CUDA"
    }
    }
    }
```

この記事で使用したパラメーター ファイルのサンプルを次に示します。

```powershell
PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\CreateVM\CreateVM.json"
PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\CreateVM\CreateVM.parameters.json"
PS C:\WINDOWS\system32> $RGName = "myasegpuvm1"
PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "deployment2"

DeploymentName          : deployment2
ResourceGroupName       : myasegpuvm1
ProvisioningState       : Succeeded
Timestamp               : 12/16/2020 12:02:56 AM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          vmName           String                     VM2
                          adminUsername    String                     Administrator
                          password         String                     Password1
                          imageName        String                     myasewindowsimg
                          vmSize           String                     Standard_NC4as_T4_v3
                          vnetName         String                     ASEVNET
                          vnetRG           String                     aserg
                          subnetName       String                     ASEVNETsubNet
                          nicName          String                     nic6
                          ipConfigName     String                     ipconfig6
                          privateIPAddress  String

Outputs                 :
DeploymentDebugLogLevel :
PS C:\WINDOWS\system32>
```
#### <a name="deploy-template"></a>テンプレートのデプロイ

テンプレート `addGPUextensiontoVM.json` をデプロイします。 このテンプレートにより、既存の VM に拡張機能がデプロイされます。 次のコマンドを実行します。

```powershell
$templateFile = "<Path to addGPUextensiontoVM.json>" 
$templateParameterFile = "<Path to addGPUExtWindowsVM.parameters.json>" 
$RGName = "<Name of your resource group>"
New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "<Name for your deployment>"
```
> [!NOTE]
> 拡張機能のデプロイは長時間実行されるジョブであり、完了するまでに約 10 分かかります。

出力例を次に示します。

```powershell
PS C:\WINDOWS\system32> "C:\12-09-2020\ExtensionTemplates\addGPUextensiontoVM.json"
C:\12-09-2020\ExtensionTemplates\addGPUextensiontoVM.json
PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\ExtensionTemplates\addGPUextensiontoVM.json"
PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\ExtensionTemplates\addGPUExtWindowsVM.parameters.json"
PS C:\WINDOWS\system32> $RGName = "myasegpuvm1"
PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "deployment3"

DeploymentName          : deployment3
ResourceGroupName       : myasegpuvm1
ProvisioningState       : Succeeded
Timestamp               : 12/16/2020 12:18:50 AM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          vmName           String                     VM2
                          extensionName    String                     windowsgpuext
                          publisher        String                     Microsoft.HpcCompute
                          type             String                     NvidiaGpuDriverWindows
                          typeHandlerVersion  String                     1.3
                          settings         Object                     {
                            "DriverURL": "http://us.download.nvidia.com/tesla/442.50/442.50-tesla-desktop-winserver-2019-2016-international.exe",
                            "DriverCertificateUrl": "https://go.microsoft.com/fwlink/?linkid=871664",
                            "DriverType": "CUDA"
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
PS C:\WINDOWS\system32> Get-AzureRmVMExtension -ResourceGroupName myasegpuvm1 -VMName VM2 -Name windowsgpuext

ResourceGroupName       : myasegpuvm1
VMName                  : VM2
Name                    : windowsgpuext
Location                : dbelocal
Etag                    : null
Publisher               : Microsoft.HpcCompute
ExtensionType           : NvidiaGpuDriverWindows
TypeHandlerVersion      : 1.3
Id                      : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/myasegpuvm1/providers/Microsoft.Compute/virtualMachines/VM2/extensions/windowsgpuext
PublicSettings          : {
                            "DriverURL": "http://us.download.nvidia.com/tesla/442.50/442.50-tesla-desktop-winserver-2019-2016-international.exe",
                            "DriverCertificateUrl": "https://go.microsoft.com/fwlink/?linkid=871664",
                            "DriverType": "CUDA"
                          }
ProtectedSettings       :
ProvisioningState       : Creating
Statuses                :
SubStatuses             :
AutoUpgradeMinorVersion : True
ForceUpdateTag          :

PS C:\WINDOWS\system32>
```

拡張機能の実行の出力は、次のファイルにログ記録されます。 インストールの状態を追跡するには、この `C:\Packages\Plugins\Microsoft.HpcCompute.NvidiaGpuDriverWindows\1.3.0.0\Status` ファイルを参照してください。 


正常にインストールされた場合は、`message` によって `Enable Extension` として、および `status` によって `success` として示されます。

```powershell
"status":  {
                       "formattedMessage":  {
                                                "message":  "Enable Extension",
                                                "lang":  "en"
                                            },
                       "name":  "NvidiaGpuDriverWindows",
                       "status":  "success",
```

#### <a name="verify-windows-driver-installation"></a>Windows ドライバーのインストールの確認

VM にサインインし、ドライバーと共にインストールされる nvidia-smi コマンド ライン ユーティリティを実行します。 `nvidia-smi.exe` は、`C:\Program Files\NVIDIA Corporation\NVSMI\nvidia-smi.exe` にあります。 ファイルが表示されない場合は、ドライバーのインストールがバックグラウンドでまだ実行されている可能性があります。 10 分待ってから、もう一度確認してください。

ドライバーがインストールされている場合は、次の例のような出力が表示されます。 

```powershell
PS C:\Users\Administrator> cd "C:\Program Files\NVIDIA Corporation\NVSMI"
PS C:\Program Files\NVIDIA Corporation\NVSMI> ls

    Directory: C:\Program Files\NVIDIA Corporation\NVSMI

Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----        2/26/2020  12:00 PM         849640 MCU.exe
-a----        2/26/2020  12:00 PM         443104 nvdebugdump.exe
-a----        2/25/2020   2:06 AM          81823 nvidia-smi.1.pdf
-a----        2/26/2020  12:01 PM         566880 nvidia-smi.exe
-a----        2/26/2020  12:01 PM         991344 nvml.dll

PS C:\Program Files\NVIDIA Corporation\NVSMI> .\nvidia-smi.exe
Wed Dec 16 00:35:51 2020
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 442.50       Driver Version: 442.50       CUDA Version: 10.2     |
|-------------------------------+----------------------+----------------------+
| GPU  Name            TCC/WDDM | Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|===============================+======================+======================|
|   0  Tesla T4            TCC  | 0000503C:00:00.0 Off |                    0 |
| N/A   35C    P8    11W /  70W |      8MiB / 15205MiB |      0%      Default |
+-------------------------------+----------------------+----------------------+

+-----------------------------------------------------------------------------+
| Processes:                                                       GPU Memory |
|  GPU       PID   Type   Process name                             Usage      |
|=============================================================================|
|  No running processes found                                                 |
+-----------------------------------------------------------------------------+
PS C:\Program Files\NVIDIA Corporation\NVSMI>
```

詳細については、「[Windows 用の Nvidia GPU ドライバー拡張機能](../virtual-machines/extensions/hpccompute-gpu-windows.md)」を参照してください。

### <a name="gpu-extension-for-linux"></a>Linux 用の GPU 拡張機能

既存の VM 用の Nvidia GPU ドライバーをデプロイするには、パラメーター ファイルを編集してから、`addGPUextensiontoVM.json` テンプレートをデプロイします。 次のセクションで説明するように、Ubuntu および Red Hat Enterprise Linux (RHEL) 固有のパラメーター ファイルがあります。

#### <a name="edit-parameters-file"></a>パラメーター ファイルを編集する

Ubuntu を使用している場合、`addGPUExtLinuxVM.parameters.json` ファイルは次のパラメーターを受け取ります。

```powershell
"parameters": { 
    "vmName": {
    "value": "<name of the VM>" 
    },
    "extensionName": {
    "value": "<name for the extension. Example: linuxGpu>" 
    },
    "publisher": {
    "value": "Microsoft.HpcCompute" 
    },
    "type": {
    "value": "NvidiaGpuDriverLinux" 
    },
    "typeHandlerVersion": {
    "value": "1.3" 
    },
    "settings": {
    "value": {
    "DRIVER_URL": "https://go.microsoft.com/fwlink/?linkid=874271",
    "PUBKEY_URL": "http://download.microsoft.com/download/F/F/A/FFAC979D-AD9C-4684-A6CE-C92BB9372A3B/7fa2af80.pub",
    "CUDA_ver": "10.0.130",
    "InstallCUDA": "true"
    }
    }
    }
```
Red Hat Enterprise Linux (RHEL) を使用している場合、`addGPUExtensionRHELVM.parameters.json` ファイルは次のパラメーターを受け取ります。

```powershell
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "<name of the VM>" 
        },
        "extensionName": {
            "value": "<name for the extension. Example: linuxGpu>" 
        },
        "publisher": {
            "value": "Microsoft.HpcCompute" 
        },
        "type": {
            "value": "NvidiaGpuDriverLinux" 
        },
        "typeHandlerVersion": {
            "value": "1.3" 
        },
        "settings": {
            "value": {
                    "isCustomInstall":true,
                    "DRIVER_URL":"https://go.microsoft.com/fwlink/?linkid=874273",
                    "CUDA_ver":"10.0.130",
                    "PUBKEY_URL":"http://download.microsoft.com/download/F/F/A/FFAC979D-AD9C-4684-A6CE-C92BB9372A3B/7fa2af80.pub",
                    "DKMS_URL":"https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm",
                    "LIS_URL":"https://aka.ms/lis",
                    "LIS_RHEL_ver":"3.10.0-1062.9.1.el7"
            }
        }
    }
}
```


この記事で使用されている Ubuntu パラメーター ファイルの例を次に示します。

```powershell
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "VM1" 
        },
        "extensionName": {
            "value": "gpuLinux" 
        },
        "publisher": {
            "value": "Microsoft.HpcCompute" 
        },
        "type": {
            "value": "NvidiaGpuDriverLinux" 
        },
        "typeHandlerVersion": {
            "value": "1.3" 
        },
        "settings": {
            "value": {
            "DRIVER_URL": "https://go.microsoft.com/fwlink/?linkid=874271",
            "PUBKEY_URL": "http://download.microsoft.com/download/F/F/A/FFAC979D-AD9C-4684-A6CE-C92BB9372A3B/7fa2af80.pub",
            "CUDA_ver": "10.0.130",
            "InstallCUDA": "true"
            }
        }
    }
}
```


#### <a name="deploy-template"></a>テンプレートのデプロイ

テンプレート `addGPUextensiontoVM.json` をデプロイします。 このテンプレートにより、既存の VM に拡張機能がデプロイされます。 次のコマンドを実行します。

```powershell
$templateFile = "Path to addGPUextensiontoVM.json" 
$templateParameterFile = "Path to addGPUExtLinuxVM.parameters.json" 
$RGName = "<Name of your resource group>" 
New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "<Name for your deployment>"
``` 

> [!NOTE]
> 拡張機能のデプロイは長時間実行されるジョブであり、完了するまでに約 10 分かかります。

出力例を次に示します。

```powershell
Copyright (C) Microsoft Corporation. All rights reserved.
Try the new cross-platform PowerShell https://aka.ms/pscore6

PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\ExtensionTemplates\addGPUextensiontoVM.json"
PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\ExtensionTemplates\addGPUExtLinuxVM.parameters.json"
PS C:\WINDOWS\system32> $RGName = "rg2"
PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "delpoyment7"

DeploymentName          : delpoyment7
ResourceGroupName       : rg2
ProvisioningState       : Succeeded
Timestamp               : 12/10/2020 10:43:23 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          vmName           String                     VM1
                          extensionName    String                     gpuLinux
                          publisher        String                     Microsoft.HpcCompute
                          type             String                     NvidiaGpuDriverLinux
                          typeHandlerVersion  String                     1.3
                          settings         Object                     {
                            "DRIVER_URL": "https://go.microsoft.com/fwlink/?linkid=874271",
                            "PUBKEY_URL":
                          "http://download.microsoft.com/download/F/F/A/FFAC979D-AD9C-4684-A6CE-C92BB9372A3B/7fa2af80.pub",
                            "CUDA_ver": "10.0.130",
                            "InstallCUDA": "true"
                          }

Outputs                 :
DeploymentDebugLogLevel :
PS C:\WINDOWS\system32>
```

#### <a name="track-deployment-status"></a>デプロイの状態の追跡    
    
テンプレートのデプロイは、長時間実行されるジョブです。 特定の VM の拡張機能のデプロイ状態を確認するには、別の PowerShell セッションを開きます (管理者として実行します)。 次のコマンドを実行します。 

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName <VM Name> -Name <Extension Name>
```
出力例を次に示します。 

```powershell
Copyright (C) Microsoft Corporation. All rights reserved.
Try the new cross-platform PowerShell https://aka.ms/pscore6

PS C:\WINDOWS\system32> Get-AzureRmVMExtension -ResourceGroupName rg2 -VMName VM1 -Name gpulinux

ResourceGroupName       : rg2
VMName                  : VM1
Name                    : gpuLinux
Location                : dbelocal
Etag                    : null
Publisher               : Microsoft.HpcCompute
ExtensionType           : NvidiaGpuDriverLinux
TypeHandlerVersion      : 1.3
Id                      : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/rg2/providers/Microsoft.Compute/virtualMachines/VM1/extensions/gpuLinux
PublicSettings          : {
                            "DRIVER_URL": "https://go.microsoft.com/fwlink/?linkid=874271",
                            "PUBKEY_URL": "http://download.microsoft.com/download/F/F/A/FFAC979D-AD9C-4684-A6CE-C92BB9372A3B/7fa2af80.pub",
                            "CUDA_ver": "10.0.130",
                            "InstallCUDA": "true"
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

拡張機能の実行の出力は、`/var/log/azure/nvidia-vmext-status` ファイルにログ記録されます。

#### <a name="verify-linux-driver-installation"></a>Linux ドライバーのインストールの確認

ドライバーのインストールを確認するには、次の手順に従います。

1. GPU VM に接続します。 「[Linux VM への接続](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#connect-to-a-linux-vm)」の手順に従います。 

    出力例を次に示します。

    ```powershell
    PS C:\WINDOWS\system32> ssh -l Administrator 10.57.50.60
    Administrator@10.57.50.60's password:
    Welcome to Ubuntu 18.04.4 LTS (GNU/Linux 5.0.0-1031-azure x86_64)
     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/advantage
      System information as of Thu Dec 10 22:57:01 UTC 2020
    
      System load:  0.0                Processes:           133
      Usage of /:   24.8% of 28.90GB   Users logged in:     0
      Memory usage: 2%                 IP address for eth0: 10.57.50.60
      Swap usage:   0%
    
    249 packages can be updated.
    140 updates are security updates.
    
    Welcome to Ubuntu 18.04.4 LTS (GNU/Linux 5.0.0-1031-azure x86_64)    
     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/advantage    
      System information as of Thu Dec 10 22:57:01 UTC 2020    
      System load:  0.0                Processes:           133
      Usage of /:   24.8% of 28.90GB   Users logged in:     0
      Memory usage: 2%                 IP address for eth0: 10.57.50.60
      Swap usage:   0%
        
    249 packages can be updated.
    140 updates are security updates.
    
    New release '20.04.1 LTS' available.
    Run 'do-release-upgrade' to upgrade to it.
        
    *** System restart required ***
    Last login: Thu Dec 10 21:49:29 2020 from 10.90.24.23
    To run a command as administrator (user "root"), use "sudo <command>".
    See "man sudo_root" for details.
    
    Administrator@VM1:~$
    ```
2. ドライバーと共にインストールされる nvidia-smi コマンド ライン ユーティリティを実行します。 ドライバーが正常にインストールされている場合は、このユーティリティを実行して次の出力を確認できます。

    ```powershell
    Administrator@VM1:~$ nvidia-smi
    Thu Dec 10 22:58:46 2020
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 455.45.01    Driver Version: 455.45.01    CUDA Version: 11.1     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            Off  | 0000941F:00:00.0 Off |                    0 |
    | N/A   48C    P0    27W /  70W |      0MiB / 15109MiB |      5%      Default |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |  No running processes found                                                 |
    +-----------------------------------------------------------------------------+
    Administrator@VM1:~$
    ```

詳細については、「[Linux 用の Nvidia GPU ドライバー拡張機能](../virtual-machines/extensions/hpccompute-gpu-linux.md)」を参照してください。

## <a name="remove-gpu-extension"></a>GPU 拡張機能の削除

GPU 拡張機能を削除するには、次のコマンドを使用します。

`Remove-AzureRmVMExtension -ResourceGroupName <Resource group name> -VMName <VM name> -Name <Extension name>`

出力例を次に示します。

```powershell
PS C:\azure-stack-edge-deploy-vms> Remove-AzureRmVMExtension -ResourceGroupName rgl -VMName WindowsVM -Name windowsgpuext
Virtual machine extension removal operation
This cmdlet will remove the specified virtual machine extension. Do you want to continue? [Y] Yes [N] No [S] Suspend [?] Help (default is "Y"): y
Requestld IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------    
          True                OK         OK
```




## <a name="next-steps"></a>次のステップ

[Azure Resource Manager コマンドレット](/powershell/module/azurerm.resources/?view=azurermps-6.13.0&preserve-view=true)