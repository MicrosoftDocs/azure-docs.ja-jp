---
title: "一般化した VHD からの VM の作成 | Microsoft Docs"
description: "Azure PowerShell を使用して、ストレージ アカウントで一般化した VHD イメージから Windows 仮想マシンを作成する方法について説明します。"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: b4808871-9ef1-49ea-a617-9154d417abb0
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/21/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 1429bf0d06843da4743bd299e65ed2e818be199d
ms.openlocfilehash: 12832620d94226b6cfe391471c22fad2d1e3cf7e
ms.lasthandoff: 03/22/2017


---
# <a name="create-a-vm-from-a-generalized-vhd-image-in-a-storage-account"></a>ストレージ アカウントでの一般化した VHD イメージからの VM の作成 

このトピックでは、ストレージ アカウント内にある一般化された非管理対象ディスクから VM を作成する方法について説明します。 一般化した VHD イメージでは、[Sysprep](virtual-machines-windows-generalize-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) を使用してすべての個人アカウント情報が削除されています。 一般化した VHD を作成するには、オンプレミスの VM で Sysprep を実行してから [VHD を Azure にアップロード](virtual-machines-windows-upload-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)するか、既存の Azure VM で Sysprep を実行してから [VHD をコピー](virtual-machines-windows-vhd-copy.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)します。

ストレージ アカウント内の特殊な VHD から VM を作成する場合は、「[Create a VM from a specialized VHD (特殊な VHD からの VM の作成)](virtual-machines-windows-create-vm-specialized.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」を参照してください。

ストレージ アカウント内のディスクではなく Managed Disks を使用する方法の詳細は、[管理対象 VM イメージの作成](virtual-machines-windows-capture-image-resource.md)に関するページと[管理対象イメージからの VM の作成](virtual-machines-windows-create-vm-generalized-managed.md)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件
オンプレミスの VM からアップロードされた VHD (Hyper-V を使用して作成された VHD など) を使用する場合は、「[Windows VHD の Azure へのアップロードの準備](virtual-machines-windows-prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」の指示に従ってください。 

アップロードされた VHD と既存の Azure VM VHD は両方とも一般化しないと、この方法を使用して VM を作成することはできません。 詳細については、「[Sysprep を使用した Windows 仮想マシンの一般化](virtual-machines-windows-generalize-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」を参照してください。 

## <a name="set-the-uri-of-the-vhd"></a>VHD の URI の設定

使用する VHD の URI は、https://**mystorageaccount**.blob.core.windows.net/**mycontainer**/**MyVhdName**.vhd という形式になります。 この例では、**myVHD** という名前の VHD がストレージ アカウント **mystorageaccount** のコンテナー **mycontainer** にあります。

```powershell
$imageURI = "https://mystorageaccount.blob.core.windows.net/mycontainer/myVhd.vhd"
```


## <a name="create-a-virtual-network"></a>仮想ネットワークの作成
[仮想ネットワーク](../virtual-network/virtual-networks-overview.md)の vNet とサブネットを作成します。

1. サブネットを作成します。 次の例では、アドレス プレフィックス **10.0.0.0/24** で **mySubnet** という名前のサブネットをリソース グループ **myResourceGroup** に作成します。  
   
    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubnet"
    $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. 仮想ネットワークを作成します。 次の例では、アドレス プレフィックス **10.0.0.0/16** で **myVnet** という名前の仮想ネットワークを場所 **West US** に作成します。  
   
    ```powershell
    $location = "West US"
    $vnetName = "myVnet"
    $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    

## <a name="create-a-public-ip-address-and-network-interface"></a>パブリック IP アドレスとネットワーク インターフェイスの作成
仮想ネットワークでの仮想マシンとの通信を有効にするには、 [パブリック IP アドレス](../virtual-network/virtual-network-ip-addresses-overview-arm.md) とネットワーク インターフェイスが必要です。

1. パブリック IP アドレスを作成します。 この例では、**myPip** という名前のパブリック IP アドレスを作成します。 
   
    ```powershell
    $ipName = "myPip"
    $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. NIC を作成します。 この例では、**myNic** という名前の NIC を作成します。 
   
    ```powershell
    $nicName = "myNic"
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $location `
        -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

## <a name="create-the-network-security-group-and-an-rdp-rule"></a>ネットワーク セキュリティ グループと RDP 規則の作成
RDP を使用して VM にログインできるようにするには、ポート 3389 で RDP アクセスを許可するセキュリティ規則が必要です。 

この例では、**myNsg** という名前の NSG を作成します。この NSG には、ポート 3389 経由の RDP トラフィックを許可する **myRdpRule** という名前の規則が含まれています。 NSG の詳細については、[PowerShell を使用した Azure の VM へのポートの開放](virtual-machines-windows-nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関するページを参照してください。

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
```


## <a name="create-a-variable-for-the-virtual-network"></a>仮想ネットワーク用の変数の作成
変数を作成して、仮想ネットワークの作成を完了します。 

```powershell
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name $vnetName
```

## <a name="create-the-vm"></a>VM の作成
次の PowerShell スクリプトでは、仮想マシンの構成を設定し、アップロードした VM イメージを新しいインストールのソースとして使用する方法を示します。

</br>

```powershell
    # Enter a new user name and password to use as the local administrator account 
    # for remotely accessing the VM.
    $cred = Get-Credential

    # Name of the storage account where the VHD is located. This example sets the 
    # storage account name as "myStorageAccount"
    $storageAccName = "myStorageAccount"

    # Name of the virtual machine. This example sets the VM name as "myVM".
    $vmName = "myVM"

    # Size of the virtual machine. This example creates "Standard_D2_v2" sized VM. 
    # See the VM sizes documentation for more information: 
    # https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/
    $vmSize = "Standard_D2_v2"

    # Computer name for the VM. This examples sets the computer name as "myComputer".
    $computerName = "myComputer"

    # Name of the disk that holds the OS. This example sets the 
    # OS disk name as "myOsDisk"
    $osDiskName = "myOsDisk"

    # Assign a SKU name. This example sets the SKU name as "Standard_LRS"
    # Valid values for -SkuName are: Standard_LRS - locally redundant storage, Standard_ZRS - zone redundant
    # storage, Standard_GRS - geo redundant storage, Standard_RAGRS - read access geo redundant storage,
    # Premium_LRS - premium locally redundant storage. 
    $skuName = "Standard_LRS"

    # Get the storage account where the uploaded image is stored
    $storageAcc = Get-AzureRmStorageAccount -ResourceGroupName $rgName -AccountName $storageAccName

    # Set the VM name and size
    $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize

    #Set the Windows operating system configuration and add the NIC
    $vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName `
        -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id

    # Create the OS disk URI
    $osDiskUri = '{0}vhds/{1}-{2}.vhd' `
        -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName

    # Configure the OS disk to be created from the existing VHD image (-CreateOption fromImage).
    $vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri `
        -CreateOption fromImage -SourceImageUri $imageURI -Windows

    # Create the new VM
    New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm
```

## <a name="verify-that-the-vm-was-created"></a>VM 作成の確認
完了したら、[Azure Portal](https://portal.azure.com) で **[参照]** > **[仮想マシン]** にアクセスするか、次の PowerShell コマンドを使用して、新しく作成された VM を確認します。

```powershell
    $vmList = Get-AzureRmVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>次のステップ
Azure PowerShell で新しい仮想マシンを管理する方法については、 [Azure Resource Manager と PowerShell を使用した仮想マシンの管理](virtual-machines-windows-ps-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関するページをご覧ください。


