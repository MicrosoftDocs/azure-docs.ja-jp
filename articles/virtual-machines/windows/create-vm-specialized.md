---
title: "Azure で特殊化された VHD から Windows VM を作成する | Microsoft Docs"
description: "Resource Manager デプロイメント モデルで、特殊化された管理ディスクを OS ディスクとして接続して新しい Windows VM を作成します。"
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
ms.date: 06/29/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: b315a37f72f34d45bb55c2bbe6be20cca8c42424
ms.contentlocale: ja-jp
ms.lasthandoff: 07/01/2017


---
# <a name="create-a-windows-vm-from-a-specialized-disk"></a>特殊化されたディスクからの Windows VM の作成

Powershell を使用して、OS ディスクとして特殊化された管理ディスクを接続することにより新しい VM を作成します。 特殊化されたディスクは、元の VM のユーザーアカウント、アプリケーション、その他の状態データを維持する、既存の VM の仮想ハードディスク (VHD) のコピーです。 

特殊化された VHD を使用して新しい VM を作成すると、新しい VM は、元の VM のコンピューター名を保持します。 その他のコンピューター固有の情報も保持します。場合によっては、この重複情報によって問題が発生する可能性があります。 VM をコピーするとき、アプリケーションが依存するコンピューター固有の情報の種類を把握しておいてください。

2 つのオプションがあります。
* [VHD をアップロードする](#option-1-upload-a-specialized-vhd)
* [既存の Azure VM をコピーする](#option-2-copy-an-existing-azure-vm)

このトピックでは、管理ディスクの使用方法を説明します。 従来のデプロイメントがストレージ アカウントを使用する必要がある場合、[ストレージ アカウントで特殊化された VHD から VM を作成する](sa-create-vm-specialized.md)に関するページを参照してください。

## <a name="before-you-begin"></a>開始する前に
PowerShell を使用する場合は、AzureRM.Compute PowerShell モジュールの最新バージョンがあることを確認してください。 

```powershell
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
詳しくは、[Azure PowerShell のバージョン管理に関するページ](/powershell/azure/overview)をご覧ください。


## <a name="option-1-upload-a-specialized-vhd"></a>オプション 1: 特殊化された VHD をアップロードする

Hyper-V などのオンプレミスの仮想化ツールを使用して作成された特殊化された VM、または別のクラウドからエクスポートされた VM から VHD をアップロードできます。

### <a name="prepare-the-vm"></a>VM を準備する
新しい VM を作成するために VHD を使用する場合、以下のステップが完了していることを確認します。 
  
  * [Windows VHD の Azure へのアップロードの準備](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 Sysprep を使用して VM を一般化**しないでください**。
  * VM にインストールされたゲストの仮想化ツールやエージェント (VMware ツールなど) の削除。
  * IP アドレスと DNS 設定を DHCP で取得するよう VM が構成されていることを確認。 これにより、サーバーが起動時に VNet 内の IP アドレスを取得します。 


### <a name="get-the-storage-account"></a>ストレージ アカウントを取得する
アップロードした VHD を格納するには、Azure にストレージ アカウントが必要です。 既存のストレージ アカウントを選択することも、新しいストレージ アカウントを作成することもできます。 

使用できるストレージ アカウントを表示するには、次のように入力します。

```powershell
Get-AzureRmStorageAccount
```

既存のストレージ アカウントを使用する場合は、「[VHD のアップロード](#upload-the-vhd-to-your-storage-account)」セクションに進みます。

ストレージ アカウントを作成する場合は、次の手順に従います。

1. ストレージ アカウントを作成するリソース グループ名が必要です。 サブスクリプションのすべてのリソース グループを検索するには、次のように入力します。
   
    ```powershell
    Get-AzureRmResourceGroup
    ```

    *myResourceGroup* という名前のリソース グループを*米国西部*リージョンで作成するには、次のように入力します。

    ```powershell
    New-AzureRmResourceGroup -Name myResourceGroup -Location "West US"
    ```

2. [New-AzureStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount) コマンドレットを使用して、このリソース グループに *mystorageaccount* というストレージ アカウントを作成します。
   
    ```powershell
    New-AzureRmStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "West US" `
        -SkuName "Standard_LRS" -Kind "Storage"
    ```

### <a name="upload-the-vhd-to-your-storage-account"></a>ストレージ アカウントに VHD をアップロードする 
[Add-AzureRmVhd](/powershell/module/azurerm.compute/add-azurermvhd) コマンドレットを使用して、ストレージ アカウント内のコンテナーに VHD をアップロードします。 この例では、ファイル *myVHD.vhd* を`"C:\Users\Public\Documents\Virtual hard disks\"`から *myResourceGroup* リソース グループの *mystorageaccount* というストレージ アカウントにアップロードします。 ファイルは *mycontainer* というコンテナーに格納され、新しいファイル名は *myUploadedVHD.vhd* になります。

```powershell
$resourceGroupName = "myResourceGroup"
$urlOfUploadedVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzureRmVhd -ResourceGroupName $resourceGroupName -Destination $urlOfUploadedVhd `
    -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


成功した場合、次のような応答を取得します。

```powershell
MD5 hash is being calculated for the file C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd.
MD5 hash calculation is completed.
Elapsed time for the operation: 00:03:35
Creating new page blob of size 53687091712...
Elapsed time for upload: 01:12:49

LocalFilePath           DestinationUri
-------------           --------------
C:\Users\Public\Doc...  https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd
```

このコマンドは、ネットワーク接続や VHD ファイルのサイズによっては、完了に時間がかかることがあります。

### <a name="create-a-managed-disk-from-the-vhd"></a>VHD から管理ディスクを作成する

[New-AzureRMDisk](/powershell/module/azurerm.compute/new-azurermdisk) を使用してストレージ アカウント内の既存の特殊化された VHD から管理ディスクを作成します。 この例では、ディスク名に **myOSDisk1** を使用し、*StandardLRS* ストレージにディスクを挿入して、*https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd* をソース VHD の URI として使用します。

新しい VM の新しいリソース グループを作成します。

```powershell
$destinationResourceGroup = 'myDestinationResourceGroup'
New-AzureRmResourceGroup -Location $location -Name $destinationResourceGroup
```

アップロードした VHD から新しい OS ディスクを作成します。 

```powershell
$sourceUri = https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd)
$osDiskName = 'myOsDisk'
$osDisk = New-AzureRmDisk -DiskName $osDiskName -Disk `
    (New-AzureRmDiskConfig -AccountType StandardLRS  -Location $location -CreateOption Import `
    -SourceUri $sourceUri) `
    -ResourceGroupName $destinationResourceGroup
```

## <a name="option-2-copy-an-existing-azure-vm"></a>オプション 2: 既存の Azure VM をコピーする

VM のスナップショットを取得して管理ディスクを使用する VM のコピーを作成し、そのスナップショットを使用して新しい管理ディスクおよび新しい VM を作成できます。


### <a name="take-a-snapshot-of-the-os-disk"></a>OS ディスクのスナップショットを取得する

VM 全体 (すべてのディスクを含む) または 1 つのディスクのみのスナップショットを取得できます。 次の手順では、[New-AzureRmSnapshot](/powershell/module/azurerm.compute/new-azurermsnapshot) コマンドレットを使用して VM の OS ディスクのみのスナップショットを取得する方法を説明します。 

パラメーターを設定する。 

 ```powershell
$resourceGroupName = 'myResourceGroup' 
$vmName = 'myVM'
$location = 'westus' 
$snapshotName = 'mySnapshot'  
```

VM オブジェクトを作成します。

```powershell
$vm = Get-AzureRmVM -Name $vmName -ResourceGroupName $resourceGroupName
```
OS ディスク名を取得します。

 ```powershell
$disk = Get-AzureRmDisk -ResourceGroupName $resourceGroupName -DiskName $vm.StorageProfile.OsDisk.Name
```

スナップショットの構成を作成します。 

 ```powershell
$snapshotConfig =  New-AzureRmSnapshotConfig -SourceUri $disk.Id -OsType Windows -CreateOption Copy -Location $location 
```

スナップショットを取得する。

```powershell
$snapShot = New-AzureRmSnapshot -Snapshot $snapshotConfig -SnapshotName $snapshotName -ResourceGroupName $resourceGroupName
```


スナップショットを使って高パフォーマンスが必要な VM を作成する計画がある場合は、New-AzureRmSnapshot コマンドで `-AccountType Premium_LRS` パラメーターを使います。 スナップショットが作成され、Premium Managed Disk として保存されます。 Premium Managed Disks は、Standard Managed Disks よりも高価格です。 このパラメーターを使用する前に Premium Managed Disks が本当に必要なことを確認してください。

### <a name="create-a-new-disk-from-the-snapshot"></a>スナップショットから新しいディスクを作成する

[New-AzureRMDisk](/powershell/module/azurerm.compute/new-azurermdisk) を使用してスナップショットから管理ディスクを作成します。 この例ではディスクの名前に *myOSDisk* を使用します。

新しい VM の新しいリソース グループを作成します。

```powershell
$destinationResourceGroup = 'myDestinationResourceGroup'
New-AzureRmResourceGroup -Location $location -Name $destinationResourceGroup
```

OS ディスク名を設定します。 

```powershell
$osDiskName = 'myOsDisk'
```

管理ディスクを作成します。

```powershell
$osDisk = New-AzureRmDisk -DiskName $osDiskName -Disk `
    (New-AzureRmDiskConfig  -Location $location -CreateOption Copy `
    -SourceResourceId $snapshot.Id) `
    -ResourceGroupName $destinationResourceGroup
```


## <a name="create-the-new-vm"></a>新しい VM を作成する 

新しい VM によって使用されるネットワークおよびその他の VM のリソースを作成します。

### <a name="create-the-subnet-and-vnet"></a>サブネットと vNet の作成

[仮想ネットワーク](../../virtual-network/virtual-networks-overview.md)の vNet とサブネットを作成します。

サブネットを作成します。 この例では、**mySubNet** という名前のサブネットをリソース グループ **myDestinationResourceGroup** に作成し、サブネットのアドレス プレフィックスを **10.0.0.0/24** に設定します。
   
```powershell
$subnetName = 'mySubNet'
$singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
```

vNet を作成します。 この例では、仮想ネットワーク名を **myVnetName**、場所を **米国西部**、仮想ネットワークのアドレス プレフィックスを **10.0.0.0/16** に設定します。 
   
```powershell
$vnetName = "myVnetName"
$vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $destinationResourceGroup -Location $location `
    -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
```    


### <a name="create-the-network-security-group-and-an-rdp-rule"></a>ネットワーク セキュリティ グループと RDP 規則の作成
RDP を使用して VM にログインできるようにするには、ポート 3389 で RDP アクセスを許可するセキュリティ規則が必要です。 新しい VM の VHD は既存の特殊化された VM から作成されたため、RDP の仮想マシンからアカウントを使用できます。

この例では、NSG の名前を **myNsg** に設定し、RDP 規則の名前を **myRdpRule** に設定します。

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $destinationResourceGroup -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
    
```

エンドポイントと NSG の規則について詳しくは、[PowerShell を使用した Azure の VM へのポートの開放](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関するページをご覧ください。

### <a name="create-a-public-ip-address-and-nic"></a>パブリック IP アドレスと NIC の作成
仮想ネットワークでの仮想マシンとの通信を有効にするには、 [パブリック IP アドレス](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) とネットワーク インターフェイスが必要です。

パブリック IP を作成します。 この例では、パブリック IP アドレス名を **myIP** に設定しています。
   
```powershell
$ipName = "myIP"
$pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $destinationResourceGroup -Location $location `
   -AllocationMethod Dynamic
```       

NIC を作成します。 この例では、NIC 名を **myNicName** に設定しています。
   
```powershell
$nicName = "myNicName"
$nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $destinationResourceGroup `
    -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
```



### <a name="set-the-vm-name-and-size"></a>VM の名前とサイズの設定

この例では、VM 名を *myVM* に、VM のサイズを *Standard_A2* に設定します。

```powershell
$vmName = "myVM"
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A2"
```

### <a name="add-the-nic"></a>NIC の追加
    
```powershell
$vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    

### <a name="add-the-os-disk"></a>OS ディスクを追加する 

[Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk) を使用して OS ディスクを構成に追加します。 この例では、ディスクのサイズを *128 GB* に設定し、管理ディスクを *Windows* OS ディスクとして接続します。
 
```powershell
$vm = Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -StorageAccountType StandardLRS `
    -DiskSizeInGB 128 -CreateOption Attach -Windows
```

### <a name="complete-the-vm"></a>VM を完了する 

先ほど作成した構成を使用し、 [New-AzureRMVM](/powershell/module/azurerm.compute/new-azurermvm) を使用して VM を作成します。

```powershell
New-AzureRmVM -ResourceGroupName $destinationResourceGroup -Location $location -VM $vm
```

このコマンドが成功した場合は、次のような出力が表示されます。

```powershell
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

### <a name="verify-that-the-vm-was-created"></a>VM 作成の確認
新しく作成された VM は、[Azure Portal](https://portal.azure.com)の **[参照]** の >  **[仮想マシン]**、または次の PowerShell コマンドで確認できます。

```powershell
$vmList = Get-AzureRmVM -ResourceGroupName $destinationResourceGroup
$vmList.Name
```

## <a name="next-steps"></a>次のステップ
新しい仮想マシンにサインインするには、 [ポータル](https://portal.azure.com)で VM を参照し、 **[接続]**をクリックして、リモート デスクトップ RDP ファイルを開きます。 元の仮想マシンのアカウント資格情報を使用して、新しい仮想マシンにサインインします。 詳しくは、「[Windows が実行されている Azure 仮想マシンに接続してログオンする方法](connect-logon.md)」をご覧ください。


