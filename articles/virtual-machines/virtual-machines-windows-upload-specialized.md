---
title: "新しい VM の作成に使用するために特殊化された VHD を Azure にアップロードする | Microsoft Docs"
description: "新しい VM の作成に使用するために特殊化された VHD を Azure にアップロードします。"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/05/2017
ms.author: cynthn
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: c859b789b564ee79022823e8d796775f58eeeccd
ms.openlocfilehash: 7acd58989da14ea49374e86edb0dba5762557d79
ms.lasthandoff: 03/01/2017


---

# <a name="how-to-upload-a-specialized-vhd-to-create-a-vm-in-azure"></a>特殊化した VHD をアップロードして Azure に VM を作成する方法

特殊化された VHD では、ユーザーアカウント、アプリケーション、その他のステート データが元の VM から保持されます。 特殊化された VHD を Azure にアップロードし、それを使用して、Managed Disks または非管理対象ストレージ アカウントを使用する VM を作成できます。 [Managed Disks](../storage/storage-managed-disks-overview.md) を使用して、Managed Disks で提供される簡略化された管理と追加機能を活用することをお勧めします。


> [!IMPORTANT]
> VHD を Azure にアップロードする前に、「[Windows VHD の Azure へのアップロードの準備](virtual-machines-windows-prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」に従う必要があります
>
>


* さまざまな VM サイズの価格について詳しくは、[Virtual Machines の料金](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows)に関するページをご覧ください。
* ストレージの価格について詳しくは、[Storage 料金](https://azure.microsoft.com/pricing/details/storage/blobs/)に関するページをご覧ください。 
* 各 Azure リージョンで利用可能な VM サイズについては、「 [リージョン別の利用可能な製品](https://azure.microsoft.com/regions/services/)」を参照してください。
* Azure VM の一般的な制限事項については、「 [Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-subscription-service-limits.md)」を参照してください。

## <a name="before-you-begin"></a>開始する前に
PowerShell を使用する場合は、AzureRM.Compute PowerShell モジュールの最新バージョンがあることを確認してください。 インストールするには次のコマンドを実行します。

```powershell
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
詳しくは、「[Azure PowerShell Versioning](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/#azure-powershell-versioning)」(Azure PowerShell のバージョン管理) をご覧ください。


## <a name="prepare-the-vm"></a>VM を準備する
 
新しい VM を作成するために特殊化された VHD をそのまま使用する場合は、以下のステップが完了していることを確認します。 
  * Managed Disks を使用する場合は、「[Plan for the migration to Managed Disks](virtual-machines-windows-on-prem-to-azure.md#plan-for-the-migration-to-managed-disks)」(Managed Disks への移行の計画) をご確認ください。
  * [Windows VHD の Azure へのアップロードの準備](virtual-machines-windows-prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 Sysprep を使用して VM を一般化**しないでください**。
  * VM にインストールされたゲストの仮想化ツールやエージェント (VMware tools) の削除。
  * IP アドレスと DNS 設定を DHCP で取得するよう VM が構成されていることを確認。 これにより、サーバーが起動時に VNet 内の IP アドレスを取得します。 
  * 続行する前に VM を停止します。

## <a name="log-in-to-azure"></a>Azure へのログイン
PowerShell バージョン 1.4 以降がまだインストールされていない場合は、「[Azure PowerShell のインストールおよび構成方法](/powershell/azureps-cmdlets-docs)」をご覧ください。

1. Azure PowerShell を開き、Azure アカウントにサインインします。 Azure アカウント資格情報を入力するためのポップアップ ウィンドウが開きます。
   
    ```powershell
    Login-AzureRmAccount
    ```
2. 使用可能なサブスクリプションのサブスクリプション ID を取得します。
   
    ```powershell
    Get-AzureRmSubscription
    ```
3. このサブスクリプション ID を使用して、適切なサブスクリプションを設定します。 `<subscriptionID>` を適切なサブスクリプションの ID と置き換えます。
   
    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"
    ```

## <a name="get-the-storage-account"></a>ストレージ アカウントを取得する
アップロードした VM イメージを格納するには、Azure にストレージ アカウントが必要です。 既存のストレージ アカウントを選択することも、新しいストレージ アカウントを作成することもできます。 

VHD を使用して VM の管理ディスクを作成する場合は、ストレージ アカウントの場所が VM を作成する場所と同じである必要があります。

使用できるストレージ アカウントを表示するには、次のように入力します。

```powershell
Get-AzureRmStorageAccount
```

既存のストレージ アカウントを使用する場合は、「[VM イメージのアップロード](#upload-the-vm-vhd-to-your-storage-account)」セクションに進みます。

ストレージ アカウントを作成する場合は、次の手順に従います。

1. ストレージ アカウントを作成するリソース グループ名が必要です。 サブスクリプションのすべてのリソース グループを検索するには、次のように入力します。
   
    ```powershell
    Get-AzureRmResourceGroup
    ```

    **myResourceGroup** という名前のリソース グループを**米国西部**リージョンで作成するには、次のように入力します。

    ```powershell
    New-AzureRmResourceGroup -Name myResourceGroup -Location "West US"
    ```

2. [New-AzureStorageAccount](https://msdn.microsoft.com/library/mt607148.aspx) コマンドレットを使用して、このリソース グループに **mystorageaccount** というストレージ アカウントを作成します。
   
    ```powershell
    New-AzureRmStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "West US" `
        -SkuName "Standard_LRS" -Kind "Storage"
    ```
   
    -SkuName の値が次のとおりであることを確認します。
   
   * **Standard_LRS** - ローカル冗長ストレージ。 
   * **Standard_ZRS** - ゾーン冗長ストレージ。
   * **Standard_GRS** - geo 冗長ストレージ。 
   * **Standard_RAGRS** - 読み取りアクセス geo 冗長ストレージ。 
   * **Premium_LRS** - Premium ローカル冗長ストレージ。 

## <a name="upload-the-vhd-to-your-storage-account"></a>ストレージ アカウントに VHD をアップロードする

[Add-AzureRmVhd](https://msdn.microsoft.com/library/mt603554.aspx) コマンドレットを使用して、ストレージ アカウント内のコンテナーに VHD をアップロードします。 この例では、ファイル **myVHD.vhd** を`"C:\Users\Public\Documents\Virtual hard disks\"`から **myResourceGroup** リソース グループの **mystorageaccount** というストレージ アカウントにアップロードします。 ファイルは **mycontainer** というコンテナーに配置され、新しいファイル名は **myUploadedVHD.vhd** になります。

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzureRmVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd `
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

アップロードした VHD を使用して管理ディスクまたは新しい VM を作成する場合は、後で使用するために**送信先 URI** のパスを保存します。

### <a name="other-options-for-uploading-a-vhd"></a>VHD をアップロードするためのその他のオプション

次のいずれかの方法を使用して、ストレージ アカウントに VHD をアップロードすることもできます。

-   [Azure Storage Copy Blob API](https://msdn.microsoft.com/library/azure/dd894037.aspx)

-   [Azure ストレージ エクスプローラーでの BLOB のアップロード](https://azurestorageexplorer.codeplex.com/)

-   [Storage Import/Export Service REST API リファレンス](https://msdn.microsoft.com/library/dn529096.aspx)

    推定アップロード時間が 7 日より長い場合は、Import/Export サービスを使うことをお勧めします。 [DataTransferSpeedCalculator](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html) を使うと、データ サイズと転送単位から時間を推定できます。 

    Import/Export は、Standard Storage アカウントへのコピーに使うことができます。 Standard Storage アカウントから Premium Storage アカウントにコピーするには、AzCopy などのツールを使用する必要があります。

    
## <a name="create-the-subnet-and-vnet"></a>サブネットと vNet の作成

[仮想ネットワーク](../virtual-network/virtual-networks-overview.md)の vNet とサブネットを作成します。

1. サブネットを作成します。 この例では、**mySubNet** という名前のサブネットをリソース グループ **myResourceGroup** に作成し、サブネットのアドレス プレフィックスを **10.0.0.0/24** に設定します。
   
    ```powershell
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
    -SourceUri $urlOfUploadedImageVhd -ResourceGroupName $rgName
    ```

2. OS ディスクを構成に追加します。 この例では、ディスクのサイズを **128 GB** に設定し、管理ディスクを **Windows** OS ディスクとして接続します。
    
    ```powershell
    $vm = Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -ManagedDiskStorageAccountType StandardLRS `
    -DiskSizeInGB 128 -CreateOption Attach -Windows
    ```

省略可能: 追加の管理ディスクをデータ ディスクとして接続します。 このオプションは、[管理データ ディスクの作成](virtual-machines-windows-create-managed-disk-ps.md)に関する記事に従って管理データ ディスクを作成したと想定しています。 

```powershell
$vm = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1
```


### <a name="option-2-attach-a-vhd-that-is-in-an-existing-storage-account"></a>オプション 2: 既存のストレージ アカウント内にある VHD を接続する

1. 使用する VHD の URI を設定します。 この例では、**myOsDisk.vhd** という名前の VHD ファイルが **myContainer** というコンテナー内の **myStorageAccount** というストレージ アカウントに保持されています。

    ```powershell
    $osDiskUri = $urlOfUploadedImageVhd
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
New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm
```

このコマンドが成功した場合は、次のような出力が表示されます。

```
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

