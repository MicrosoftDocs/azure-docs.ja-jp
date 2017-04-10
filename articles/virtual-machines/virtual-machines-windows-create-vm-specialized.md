---
title: "Azure で特殊化したディスクから VM を作成する | Microsoft Docs"
description: "Resource Manager デプロイメント モデルで、特殊化した管理ディスクまたは非管理ディスクを接続して新しい VM を作成します。"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 3b7d3cd5-e3d7-4041-a2a7-0290447458ea
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 204fa369dd6db618ec5340317188681b0a2988e3
ms.openlocfilehash: cbe3d72bbd0d9cc425b1b26ad412e77b33f385b2
ms.lasthandoff: 02/11/2017


---
# <a name="create-a-vm-from-a-specialized-disk"></a>専用ディスクからの VM の作成

Powershell を使用して、OS ディスクとして特殊化されたディスクを接続することにより新しい VM を作成します。 特殊化されたディスクは、元の VM のユーザーアカウント、アプリケーション、その他の状態データを維持する、既存の VM の VHD のコピーです。 特殊化した[管理ディスク](../storage/storage-managed-disks-overview.md)または特殊化した非管理ディスクのどちらかを使用して新しい VM を作成できます。

## <a name="before-you-begin"></a>開始する前に
PowerShell を使用する場合は、AzureRM.Compute PowerShell モジュールの最新バージョンがあることを確認してください。 インストールするには次のコマンドを実行します。

```powershell
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
詳細については、[Azure PowerShell のバージョン管理に関するページ](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/#azure-powershell-versioning)をご覧ください。


## <a name="create-the-subnet-and-vnet"></a>サブネットと vNet の作成

[仮想ネットワーク](../virtual-network/virtual-networks-overview.md)の vNet とサブネットを作成します。

1. サブネットを作成します。 この例では、**mySubNet** という名前のサブネットをリソース グループ **myResourceGroup** に作成し、サブネットのアドレス プレフィックスを **10.0.0.0/24** に設定します。
   
    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubNet"
    $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. vNet を作成します。 この例では、仮想ネットワーク名を **myVnetName**、場所を **米国西部**、仮想ネットワークのアドレス プレフィックスを **10.0.0.0/16** に設定します。 
   
    ```powershell
    $location = "West US"
    $vnetName = "myVnetName"
    $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    

## <a name="create-a-public-ip-address-and-nic"></a>パブリック IP アドレスと NIC の作成
仮想ネットワークでの仮想マシンとの通信を有効にするには、 [パブリック IP アドレス](../virtual-network/virtual-network-ip-addresses-overview-arm.md) とネットワーク インターフェイスが必要です。

1. パブリック IP を作成します。 この例では、パブリック IP アドレス名を **myIP** に設定しています。
   
    ```powershell
    $ipName = "myIP"
    $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. NIC を作成します。 この例では、NIC 名を **myNicName** に設定しています。
   
    ```powershell
    $nicName = "myNicName"
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName `
    -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

## <a name="create-the-network-security-group-and-an-rdp-rule"></a>ネットワーク セキュリティ グループと RDP 規則の作成
RDP を使用して VM にログインできるようにするには、ポート 3389 で RDP アクセスを許可するセキュリティ規則が必要です。 新しい VM の VHD は既存の特殊化された VM から作成されたため、VM が作成された後は、RDP を使用してログオンするアクセス許可が付与されていたソースの仮想マシンから既存のアカウントを使用できます。
この例では、NSG の名前を **myNsg** に設定し、RDP 規則の名前を **myRdpRule** に設定します。

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
    
```

エンドポイントと NSG の規則について詳しくは、[PowerShell を使用した Azure の VM へのポートの開放](virtual-machines-windows-nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関するページをご覧ください。

## <a name="set-the-vm-name-and-size"></a>VM の名前とサイズの設定

この例では、VM 名を "myVM" に、VM のサイズを "Standard_A2" に設定します。
```powershell
$vmName = "myVM"
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A2"
```

## <a name="add-the-nic"></a>NIC の追加
    
```powershell
$vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    
    
## <a name="configure-the-os-disk"></a>OS ディスクの構成

特殊化された OS には、[Azure にアップロード](virtual-machines-windows-upload-image.md)した VHD、または[既存の Azure VM の VHD のコピー](virtual-machines-windows-vhd-copy.md)を使用できます。 

次の&2; つのオプションのいずれかを選ぶことができます。
- **オプション 1**: 既存のストレージ アカウントで特殊化された VHD から特殊化された管理ディスクを作成して、OS ディスクとして使用する。

または 

- **オプション 2**: 自分のストレージ アカウントに格納されている特殊化された VHD (非管理ディスク) を使用する。 

### <a name="option-1-create-a-managed-disk-from-an-unmanaged-specialized-disk"></a>オプション 1: 特殊化された非管理ディスクから管理ディスクを作成する

1. ストレージ アカウント内の既存の特殊化された VHD から管理ディスクを作成します。 この例では、ディスク名に **myOSDisk1** を使用し、**StandardLRS** ストレージにディスクを挿入して、**https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vh.vhd** をソース VHD の URI として使用します。

    ```powershell
    $osDisk = New-AzureRmDisk -DiskName "myOSDisk1" -Disk (New-AzureRmDiskConfig `
    -AccountType StandardLRS  -Location $location -CreationDataCreateOption Import `
    -SourceUri https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vh.vhd) `
    -ResourceGroupName $rgName
    ```

2. OS ディスクを構成に追加します。 この例では、ディスクのサイズを **128 GB** に設定し、管理ディスクを **Windows** OS ディスクとして接続します。
    
    ```powershell
    $vm = Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -StorageAccountType StandardLRS `
    -DiskSizeInGB 128 -CreateOption Attach -Windows
    ```

省略可能: 追加の管理ディスクをデータ ディスクとして接続します。 このオプションは、[管理データ ディスクの作成](virtual-machines-windows-create-managed-disk-ps.md)に関する記事に従って管理データ ディスクを作成したと想定しています。 

```powershell
$vm = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1
```


### <a name="option-2-attach-a-vhd-that-is-in-an-existing-storage-account"></a>オプション 2: 既存のストレージ アカウント内にある VHD を接続する

1. 使用する VHD の URI を設定します。 この例では、**myOsDisk.vhd** という名前の VHD ファイルが **myContainer** というコンテナー内の **myStorageAccount** というストレージ アカウントに保持されています。

    ```powershell
    $osDiskUri = "https://myStorageAccount.blob.core.windows.net/myContainer/myOsDisk.vhd"
    ```
2. コピーした OS VHD の URL を使用して OS ディスクを追加します。 この例では、OS ディスクの作成時に、"osDisk" という語句を VM 名に追加して OS ディスクの名前にしています。 また、この例では、この Windows ベース VHD を OS ディスクとして VM に接続するように指定しています。
    
    ```powershell
    $osDiskName = $vmName + "osDisk"
    $vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption attach -Windows
    ```

省略可能: VM に接続する必要のあるデータ ディスクがある場合は、データ VHD の URL と、適切な論理ユニット番号 (Lun) を使用してデータ ディスクを追加します。

```powershell
$dataDiskName = $vmName + "dataDisk"
$vm = Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -VhdUri $dataDiskUri -Lun 1 -CreateOption attach
```

ストレージ アカウントを使用する場合、データ ディスクおよびオペレーティング システム ディスクの URL は `https://StorageAccountName.blob.core.windows.net/BlobContainerName/DiskName.vhd` のようになります。 ポータルでこれを見つけるには、ターゲット ストレージ コンテナーを参照し、コピーしたオペレーティング システムまたはデータ VHD をクリックして、URL の内容をコピーします。


## <a name="create-the-vm"></a>VM の作成

先ほど作成した構成を使用して VM を作成します。

```powershell
#Create the new VM
New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm
```

このコマンドが成功した場合は、次のような出力が表示されます。

```powershell
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

## <a name="verify-that-the-vm-was-created"></a>VM 作成の確認
新しく作成された VM は、[Azure Portal](https://portal.azure.com)の **[参照]** の >  **[仮想マシン]**、または次の PowerShell コマンドで確認できます。

```powershell
$vmList = Get-AzureRmVM -ResourceGroupName $rgName
$vmList.Name
```

## <a name="next-steps"></a>次のステップ
新しい仮想マシンにサインインするには、 [ポータル](https://portal.azure.com)で VM を参照し、 **[接続]**をクリックして、リモート デスクトップ RDP ファイルを開きます。 元の仮想マシンのアカウント資格情報を使用して、新しい仮想マシンにサインインします。 詳しくは、「[Windows が実行されている Azure 仮想マシンに接続してログオンする方法](virtual-machines-windows-connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」をご覧ください。


