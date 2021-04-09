---
title: Azure で特殊化された VHD から Windows VM を作成する
description: Resource Manager デプロイ モデルを使用して、特殊化されたマネージド ディスクを OS ディスクとして接続して新しい Windows VM を作成します。
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.collection: windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 10/10/2019
ms.author: cynthn
ms.openlocfilehash: 642a28d5eea6a89de31247ed715c0b352b9ff7d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102552220"
---
# <a name="create-a-windows-vm-from-a-specialized-disk-by-using-powershell"></a>PowerShell を使用して特殊化されたディスクから Windows VM を作成する

OS ディスクとして特殊化されたマネージド ディスクを接続することにより新しい VM を作成します。 特殊化されたディスクは、元の VM のユーザーアカウント、アプリケーション、その他の状態データが保存される、既存の VM の仮想ハードディスク (VHD) のコピーです。 

いくつかのオプションがあります。
* [既存のマネージド ディスクを使用する](#option-1-use-an-existing-disk) このオプションは、正しく機能していない VM がある場合に便利です。 VM を削除してから、マネージド ディスクを再利用して新しい VM を作成できます。 
* [VHD のアップロード](#option-2-upload-a-specialized-vhd) 
* [スナップショットを使用して既存の Azure VM をコピーする](#option-3-copy-an-existing-azure-vm)

Azure Portal を使用して、[特殊化された VHD から新しい VM を作成する](create-vm-specialized-portal.md)こともできます。

この記事では、マネージド ディスクの使用方法を説明します。 従来のデプロイメントがストレージ アカウントを使用する必要がある場合、[ストレージ アカウントで特殊化された VHD から VM を作成する](/previous-versions/azure/virtual-machines/windows/sa-create-vm-specialized)に関するページを参照してください。

> [!IMPORTANT]
> 
> 特殊化されたディスクを使用して新しい VM を作成すると、新しい VM で元の VM のコンピューター名が保持されます。 その他のコンピューター固有の情報 (CMID など) も保持されます。場合によっては、この重複情報によって問題が発生する可能性があります。 VM をコピーするときは、アプリケーションが依存するコンピューター固有の情報の種類を把握しておいてください。  
> そのため、複数の VM を作成する場合は、特殊化されたディスクを使用しないでください。 代わりに、より大規模なデプロイの場合は、[イメージを作成](capture-image-resource.md)してから、[そのイメージを使用して複数の VM を作成](create-vm-generalized-managed.md)します。

1 つの VHD またはスナップショットからの同時デプロイ数は 20 VM を上限とするようお勧めします。 

## <a name="option-1-use-an-existing-disk"></a>オプション 1: 既存のディスクを使用する

削除した VM があり、OS ディスクを再利用して新しい VM を作成したい場合、[Get-AzDisk](/powershell/module/az.compute/get-azdisk)を使用します。

```powershell
$resourceGroupName = 'myResourceGroup'
$osDiskName = 'myOsDisk'
$osDisk = Get-AzDisk `
-ResourceGroupName $resourceGroupName `
-DiskName $osDiskName
```
このディスクは OS ディスクとして[新しい VM](#create-the-new-vm) に接続できるようになりました。

## <a name="option-2-upload-a-specialized-vhd"></a>オプション 2: 特殊化された VHD をアップロードする

Hyper-V などのオンプレミスの仮想化ツールを使用して作成された特殊化された VM、または別のクラウドからエクスポートされた VM から VHD をアップロードできます。

### <a name="prepare-the-vm"></a>VM を準備する
VHD をそのまま使用して新しい VM を作成します。 
  
  * [Windows VHD の Azure へのアップロードの準備](prepare-for-upload-vhd-image.md)。 Sysprep を使用して VM を一般化 **しないでください**。
  * VM にインストールされたゲストの仮想化ツールやエージェント (VMware ツールなど) を削除します。
  * IP アドレスと DNS 設定を DHCP から取得するように VM が構成されていることを確認します。 これにより、サーバーが起動時に仮想ネットワーク内の IP アドレスを確実に取得します。 


### <a name="upload-the-vhd"></a>VHD をアップロードする

VHD をマネージド ディスクに直接アップロードできるようになりました。 手順については、「[Azure PowerShell を使用して Azure に VHD をアップロードする](disks-upload-vhd-to-managed-disk-powershell.md)」を参照してください。

## <a name="option-3-copy-an-existing-azure-vm"></a>オプション 3: 既存の Azure VM をコピーする

VM のスナップショットを取得してマネージド ディスクを使用する VM のコピーを作成し、そのスナップショットを使用して新しいマネージド ディスクおよび新しい VM を作成できます。

既存の VM を別のリージョンにコピーする場合は、azcopy を使用して、[ディスクのコピーを別のリージョンに作成する](disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk)ことができます。 

### <a name="take-a-snapshot-of-the-os-disk"></a>OS ディスクのスナップショットを取得する

VM 全体 (すべてのディスクを含む) または 1 つのディスクのみのスナップショットを取得できます。 次の手順では、[New-AzSnapshot](/powershell/module/az.compute/new-azsnapshot) コマンドレットを使用して VM の OS ディスクのみのスナップショットを取得する方法を説明します。 

まずいくつかのパラメーターを設定します。 

 ```powershell
$resourceGroupName = 'myResourceGroup' 
$vmName = 'myVM'
$location = 'westus' 
$snapshotName = 'mySnapshot'  
```

VM オブジェクトを作成します。

```powershell
$vm = Get-AzVM -Name $vmName `
   -ResourceGroupName $resourceGroupName
```
OS ディスク名を取得します。

 ```powershell
$disk = Get-AzDisk -ResourceGroupName $resourceGroupName `
   -DiskName $vm.StorageProfile.OsDisk.Name
```

スナップショットの構成を作成します。 

 ```powershell
$snapshotConfig =  New-AzSnapshotConfig `
   -SourceUri $disk.Id `
   -OsType Windows `
   -CreateOption Copy `
   -Location $location 
```

スナップショットを取得する。

```powershell
$snapShot = New-AzSnapshot `
   -Snapshot $snapshotConfig `
   -SnapshotName $snapshotName `
   -ResourceGroupName $resourceGroupName
```


スナップショットを使って高パフォーマンスが必要な VM を作成するには、New-AzSnapshotConfig コマンドに `-AccountType Premium_LRS` パラメーターを追加します。 スナップショットが作成され、Premium マネージド ディスクとして保存されます。 Premium マネージド ディスクは Standard よりも高価なので、Premium が必要になることを理解してからこのパラメーターを使用してください。

### <a name="create-a-new-disk-from-the-snapshot"></a>スナップショットから新しいディスクを作成する

[New-AzDisk](/powershell/module/az.compute/new-azdisk) を使用してスナップショットからマネージド ディスクを作成します。 この例ではディスクの名前に *myOSDisk* を使用します。

新しい VM の新しいリソース グループを作成します。

```powershell
$destinationResourceGroup = 'myDestinationResourceGroup'
New-AzResourceGroup -Location $location `
   -Name $destinationResourceGroup
```

OS ディスク名を設定します。 

```powershell
$osDiskName = 'myOsDisk'
```

マネージド ディスクを作成します。

```powershell
$osDisk = New-AzDisk -DiskName $osDiskName -Disk `
    (New-AzDiskConfig  -Location $location -CreateOption Copy `
    -SourceResourceId $snapshot.Id) `
    -ResourceGroupName $destinationResourceGroup
```


## <a name="create-the-new-vm"></a>新しい VM を作成する 

新しい VM によって使用されるネットワークおよびその他の VM のリソースを作成します。

### <a name="create-the-subnet-and-virtual-network"></a>サブネットと仮想ネットワークを作成する

VM の[仮想ネットワーク](../../virtual-network/virtual-networks-overview.md)とサブネットを作成します。

1. サブネットを作成します。 この例では、*mySubNet* という名前のサブネットをリソース グループ *myDestinationResourceGroup* に作成し、サブネットのアドレス プレフィックスを *10.0.0.0/24* に設定します。
   
    ```powershell
    $subnetName = 'mySubNet'
    $singleSubnet = New-AzVirtualNetworkSubnetConfig `
       -Name $subnetName `
       -AddressPrefix 10.0.0.0/24
    ```
    
2. 仮想ネットワークを作成します。 この例では、仮想ネットワーク名を *myVnetName*、場所を *米国西部*、仮想ネットワークのアドレス プレフィックスを *10.0.0.0/16* に設定します。 
   
    ```powershell
    $vnetName = "myVnetName"
    $vnet = New-AzVirtualNetwork `
       -Name $vnetName -ResourceGroupName $destinationResourceGroup `
       -Location $location `
       -AddressPrefix 10.0.0.0/16 `
       -Subnet $singleSubnet
    ```    
    

### <a name="create-the-network-security-group-and-an-rdp-rule"></a>ネットワーク セキュリティ グループと RDP 規則の作成
リモート デスクトップ プロトコル (RDP) で VM にサインインできるようにするには、ポート 3389 での RDP アクセスを許可するセキュリティ規則が必要です。 この例では、新しい VM の VHD は既存の特殊化された VM から作成されたため、RDP のソース仮想マシンに存在するアカウントを使用できます。

この例では、ネットワーク セキュリティ グループ (NSG) の名前を *myNsg* に設定し、RDP 規則の名前を *myRdpRule* に設定します。

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzNetworkSecurityGroup `
   -ResourceGroupName $destinationResourceGroup `
   -Location $location `
   -Name $nsgName -SecurityRules $rdpRule
    
```

エンドポイントと NSG 規則の詳細については、[PowerShell を使用した Azure の VM へのポートの開放](nsg-quickstart-powershell.md)に関するページを参照してください。

### <a name="create-a-public-ip-address-and-nic"></a>パブリック IP アドレスと NIC の作成
仮想ネットワークでの仮想マシンとの通信を有効にするには、[パブリック IP アドレス](../../virtual-network/public-ip-addresses.md)とネットワーク インターフェイスが必要です。

1. パブリック IP を作成します。 この例では、パブリック IP アドレス名を *myIP* に設定しています。
   
    ```powershell
    $ipName = "myIP"
    $pip = New-AzPublicIpAddress `
       -Name $ipName -ResourceGroupName $destinationResourceGroup `
       -Location $location `
       -AllocationMethod Dynamic
    ```       
    
2. NIC を作成します。 この例では、NIC 名を *myNicName* に設定しています。
   
    ```powershell
    $nicName = "myNicName"
    $nic = New-AzNetworkInterface -Name $nicName `
       -ResourceGroupName $destinationResourceGroup `
       -Location $location -SubnetId $vnet.Subnets[0].Id `
       -PublicIpAddressId $pip.Id `
       -NetworkSecurityGroupId $nsg.Id
    ```
    


### <a name="set-the-vm-name-and-size"></a>VM の名前とサイズの設定

この例では、VM 名を *myVM* に、VM のサイズを *Standard_A2* に設定します。

```powershell
$vmName = "myVM"
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize "Standard_A2"
```

### <a name="add-the-nic"></a>NIC の追加
    
```powershell
$vm = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    

### <a name="add-the-os-disk"></a>OS ディスクを追加する 

[Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk) を使用して OS ディスクを構成に追加します。 この例では、ディスクのサイズを *128 GB* に設定し、マネージド ディスクを *Windows* OS ディスクとして接続します。
 
```powershell
$vm = Set-AzVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -StorageAccountType Standard_LRS `
    -DiskSizeInGB 128 -CreateOption Attach -Windows
```

### <a name="complete-the-vm"></a>VM を完了する 

先ほど作成した構成を使用し、[New-AzVM](/powershell/module/az.compute/new-azvm) を使用して VM を作成します。

```powershell
New-AzVM -ResourceGroupName $destinationResourceGroup -Location $location -VM $vm
```

このコマンドが成功した場合は、次のような出力が表示されます。

```powershell
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

### <a name="verify-that-the-vm-was-created"></a>VM 作成の確認
新しく作成された VM は、[Azure portal](https://portal.azure.com) の **[参照]**  >  **[仮想マシン]** 、または次の PowerShell コマンドで確認できます。

```powershell
$vmList = Get-AzVM -ResourceGroupName $destinationResourceGroup
$vmList.Name
```

## <a name="next-steps"></a>次のステップ
新しい仮想マシンにサインインします。 詳しくは、「[Windows が実行されている Azure 仮想マシンに接続してログオンする方法](connect-logon.md)」をご覧ください。