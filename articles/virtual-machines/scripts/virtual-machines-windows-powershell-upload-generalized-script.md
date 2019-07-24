---
title: Azure PowerShell のサンプル スクリプトに一般化した VHD をアップロードする | Microsoft Docs
description: 一般化した VHD を Azure にアップロードし、Resource Manager デプロイ モデルと Managed Disks を使用した新しい VM を作成する、PowerShell サンプル スクリプトです。
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/02/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 95826cb3928f9a3220505040ef3162fe5016d08b
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58447637"
---
# <a name="sample-script-to-upload-a-vhd-to-azure-and-create-a-new-vm"></a>VHD を Azure にアップロードし新しい VM を作成するサンプル スクリプト

このスクリプトでは、一般化した VM からローカルの .vhd ファイルを取得して Azure にアップロードし、Managed Disk イメージを作成してそれを使用した新しい VM を作成します。

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="sample-script"></a>サンプル スクリプト

```powershell
# Provide values for the variables
$resourceGroup = 'myResourceGroup'
$location = 'EastUS'
$storageaccount = 'mystorageaccount'
$storageType = 'Standard_LRS'
$containername = 'mycontainer'
$localPath = 'C:\Users\Public\Documents\Hyper-V\VHDs\generalized.vhd'
$vmName = 'myVM'
$imageName = 'myImage'
$vhdName = 'myUploadedVhd.vhd'
$diskSizeGB = '128'
$subnetName = 'mySubnet'
$vnetName = 'myVnet'
$ipName = 'myPip'
$nicName = 'myNic'
$nsgName = 'myNsg'
$ruleName = 'myRdpRule'
$computerName = 'myComputerName'
$vmSize = 'Standard_DS1_v2'

# Get the username and password to be used for the administrators account on the VM. 
# This is used when connecting to the VM using RDP.

$cred = Get-Credential

# Upload the VHD
New-AzResourceGroup -Name $resourceGroup -Location $location
New-AzStorageAccount -ResourceGroupName $resourceGroup -Name $storageAccount -Location $location `
    -SkuName $storageType -Kind "Storage"
$urlOfUploadedImageVhd = ('https://' + $storageaccount + '.blob.core.windows.net/' + $containername + '/' + $vhdName)
Add-AzVhd -ResourceGroupName $resourceGroup -Destination $urlOfUploadedImageVhd `
    -LocalFilePath $localPath

# Note: Uploading the VHD may take awhile!

# Create a managed image from the uploaded VHD 
$imageConfig = New-AzImageConfig -Location $location
$imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsType Windows -OsState Generalized `
    -BlobUri $urlOfUploadedImageVhd
$image = New-AzImage -ImageName $imageName -ResourceGroupName $resourceGroup -Image $imageConfig
 
# Create the networking resources
$singleSubnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
$vnet = New-AzVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroup -Location $location `
    -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
$pip = New-AzPublicIpAddress -Name $ipName -ResourceGroupName $resourceGroup -Location $location `
    -AllocationMethod Dynamic
$rdpRule = New-AzNetworkSecurityRuleConfig -Name $ruleName -Description 'Allow RDP' -Access Allow `
    -Protocol Tcp -Direction Inbound -Priority 110 -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
$nic = New-AzNetworkInterface -Name $nicName -ResourceGroupName $resourceGroup -Location $location `
    -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
$vnet = Get-AzVirtualNetwork -ResourceGroupName $resourceGroup -Name $vnetName

# Start building the VM configuration
$vm = New-AzVMConfig -VMName $vmName -VMSize $vmSize

# Set the VM image as source image for the new VM
$vm = Set-AzVMSourceImage -VM $vm -Id $image.Id

# Finish the VM configuration and add the NIC.
$vm = Set-AzVMOSDisk -VM $vm  -DiskSizeInGB $diskSizeGB -CreateOption FromImage -Caching ReadWrite
$vm = Set-AzVMOperatingSystem -VM $vm -Windows -ComputerName $computerName -Credential $cred `
    -ProvisionVMAgent -EnableAutoUpdate
$vm = Add-AzVMNetworkInterface -VM $vm -Id $nic.Id

# Create the VM
New-AzVM -VM $vm -ResourceGroupName $resourceGroup -Location $location

# Verify that the VM was created
$vmList = Get-AzVM -ResourceGroupName $resourceGroup
$vmList.Name


```


<!-- 
[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-iis/create-windows-vm-iis.ps1 "Create VM IIS")] -->

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ 

次のコマンドを実行して、リソース グループ、VM、すべての関連リソースを削除します。

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、以下のコマンドを実行してデプロイを作成します。 表内の各項目は、コマンドごとのドキュメントにリンクされています。

| command                                                                                                             | メモ                                                                                                                                                                                |
|---------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup)                           | すべてのリソースを格納するリソース グループを作成します。                                                                                                                          |
| [New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount)                         | ストレージ アカウントを作成します。                                                                                                                                                           |
| [Add-AzVhd](https://docs.microsoft.com/powershell/module/az.compute/add-azvhd)                                               | Azure のクラウド ストレージ アカウントの BLOB に、オンプレミスの仮想マシンから仮想ハード ディスクをアップロードします。                                                                       |
| [New-AzImageConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azimageconfig)                               | 構成可能なイメージ オブジェクトを作成します。                                                                                                                                                 |
| [Set-AzImageOsDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azimageosdisk)                               | イメージ オブジェクトのオペレーティング システム ディスクのプロパティを設定します。                                                                                                                        |
| [New-AzImage](https://docs.microsoft.com/powershell/module/az.compute/new-azimage)                                           | 新しいイメージを作成します。                                                                                                                                                                 |
| [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | サブネット構成を作成します。 この構成は、仮想ネットワークの作成プロセスで使用されます。                                                                                |
| [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork)                         | 仮想ネットワークを作成します。                                                                                                                                                           |
| [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress)                       | パブリック IP アドレスを作成します。                                                                                                                                                         |
| [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface)                     | ネットワーク インターフェイスを作成します。                                                                                                                                                         |
| [New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig)   | ネットワーク セキュリティ グループ規則の構成を作成します。 この構成は、NSG の作成時に NSG 規則を作成するために使用されます。                                                       |
| [New-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup)             | ネットワーク セキュリティ グループを作成します。                                                                                                                                                    |
| [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork)                         | リソース グループ内の仮想ネットワークを取得します。                                                                                                                                          |
| [New-AzVMConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azvmconfig)                                     | VM 構成を作成します。 この構成には、VM 名、オペレーティング システム、管理資格情報などの情報が含まれます。 この構成は、VM の作成時に使用されます。 |
| [Set-AzVMSourceImage](https://docs.microsoft.com/powershell/module/az.compute/set-azvmsourceimage)                           | 仮想マシンのイメージを指定します。                                                                                                                                            |
| [Set-AzVMOSDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk)                                     | 仮想マシンにオペレーティング システム ディスクのプロパティを設定します。                                                                                                                      |
| [Set-AzVMOperatingSystem](https://docs.microsoft.com/powershell/module/az.compute/set-azvmoperatingsystem)                   | 仮想マシンにオペレーティング システム ディスクのプロパティを設定します。                                                                                                                      |
| [Add-AzVMNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/add-azvmnetworkinterface)                 | 仮想マシンにネットワーク インターフェイスを追加します。                                                                                                                                       |
| [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm)                                                 | 仮想マシンを作成します。                                                                                                                                                            |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup)                     | リソース グループと、それに含まれているすべてのリソースを削除します。                                                                                                                         |

## <a name="next-steps"></a>次の手順

Azure PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](/powershell/azure/overview)を参照してください。

その他の仮想マシン用の PowerShell サンプル スクリプトは、[Azure Windows VM のドキュメント](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)にあります。
