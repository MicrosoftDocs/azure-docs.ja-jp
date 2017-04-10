---
title: "Azure で管理 VM イメージから VM を作成する | Microsoft Docs"
description: "Resource Manager デプロイメント モデルで Azure PowerShell を使用して、一般化した管理 VM イメージから仮想マシンを作成します。"
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
ms.date: 02/7/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 204fa369dd6db618ec5340317188681b0a2988e3
ms.openlocfilehash: f6fdbfcccd0bb44643d79ce866a3fa96410b298e
ms.lasthandoff: 02/11/2017


---
# <a name="create-a-vm-from-a-generalized-managed-vm-image"></a>一般化された管理 VM イメージから VM を作成する

Azure では、管理 VM イメージから複数の VM を作成できます。 管理 VM イメージには、OS や データ ディスクなど、VM の作成に必要な情報が含まれています。 OS ディスクやデータ ディスクなど、イメージを構成する VHD は、管理ディスクとして保管されます。 

一般化した VM イメージでは、[Sysprep](virtual-machines-windows-generalize-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) を使用してすべての個人アカウント情報が削除されています。 一般化した VM を作成するには、オンプレミスの VM で Sysprep を実行してから [VHD を Azure にアップロード](virtual-machines-windows-upload-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)するか、既存の Azure VM で Sysprep を実行してから [VM のイメージをキャプチャ](virtual-machines-windows-capture-image-resource.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)します。



## <a name="prerequisites"></a>前提条件

新しい VM の作成に使用する[管理 VM イメージを作成](virtual-machines-windows-capture-image-resource.md)しておく必要があります。 

AzureRM.Compute PowerShell モジュールの最新バージョンがあることを確認してください。 インストールするには次のコマンドを実行します。

```powershell
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
詳細については、[Azure PowerShell のバージョン管理に関するページ](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/#azure-powershell-versioning)をご覧ください。



## <a name="collect-information-about-the-image"></a>イメージに関する情報の収集

まず、イメージに関する基本的な情報を収集し、イメージの変数を作成する必要があります。 この例では、**West Central US** という場所のリソース グループ **myResourceGroup** にある **myImage** という名前の管理 VM イメージを使用しています。 

```powershell
$rgName = "myResourceGroup"
$location = "West Central US"
$imageName = "myImage"
$image = Get-AzureRMImage -ImageName $imageName -ResourceGroupName $rgName
```

## <a name="create-a-virtual-network"></a>仮想ネットワークの作成
[仮想ネットワーク](../virtual-network/virtual-networks-overview.md)の vNet とサブネットを作成します。

1. サブネットを作成します。 次の例では、**10.0.0.0/24** というアドレス プレフィックスを持つ **mySubnet** という名前のサブネットを作成します。  
   
    ```powershell
    $subnetName = "mySubnet"
    $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. 仮想ネットワークを作成する。 次の例では、**10.0.0.0/16** というアドレス プレフィックスを持つ **myVnet** という名前の仮想ネットワークを作成します。  
   
    ```powershell
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

RDP を使用して VM にログインできるようにするには、ポート 3389 に対する RDP アクセスを許可するネットワーク セキュリティの規則 (NSG) が必要です。 

この例では、**myNsg** という名前の NSG を作成します。この NSG には、ポート 3389 経由の RDP トラフィックを許可する **myRdpRule** という名前の規則が含まれています。 NSG の詳細については、[PowerShell を使用した Azure の VM へのポートの開放](virtual-machines-windows-nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関するページを参照してください。

```powershell
$nsgName = "myNsg"
$ruleName = "myRdpRule"
$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name $ruleName -Description "Allow RDP" `
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

## <a name="get-the-credentials-for-the-vm"></a>VM の資格情報の取得

次のコマンドレットを実行するとウィンドウが開きます。そこで、VM へのリモート アクセスでローカル管理者アカウントとして使用する、新しいユーザー名とパスワードを入力します。 

```powershell
$cred = Get-Credential
```

## <a name="set-variables-for-the-vm-name-computer-name-and-the-size-of-the-vm"></a>VM 名、コンピューター名、および VM のサイズの変数の設定

1. VM 名とコンピューター名の変数を作成します。 この例では、VM 名として **myVM** を、コンピューター名として **myComputer** を設定します。

    ```powershell
    $vmName = "myVM"
    $computerName = "myComputer"
    ```
2. 仮想マシンのサイズを設定します。 次の例では、サイズが **Standard_DS1_v2** に設定された VM を作成します。 詳細については、[VM のサイズ](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/)に関するドキュメントを参照してください。

    ```powershell
    $vmSize = "Standard_DS1_v2"
    ```

3. VM の名前とサイズを VM 構成に追加します。

```powershell
$vm = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize
```

## <a name="set-the-vm-image-as-source-image-for-the-new-vm"></a>新しい VM のソース イメージとしての VM イメージの設定

管理 VM イメージの ID を使用してソース イメージを設定します。

```powershell
$vm = Set-AzureRmVMSourceImage -VM $vm -Id $image.Id
```

## <a name="set-the-os-configuration-and-add-the-nic"></a>OS 構成の設定と NIC の追加

ストレージの種類 (PremiumLRS または StandardLRS) と OS ディスクのサイズを入力します。 この例では、アカウントの種類を **PremiumLRS** に、ディスク サイズを **128 GB**に、ディスク キャッシュを **ReadWrite** に設定します。

```powershell
$vm = Set-AzureRmVMOSDisk -VM $vm  -StorageAccountType PremiumLRS -DiskSizeInGB 128 `
-CreateOption FromImage -Caching ReadWrite

$vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName $computerName `
-Credential $cred -ProvisionVMAgent -EnableAutoUpdate

$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
```

## <a name="create-the-vm"></a>VM の作成

先ほど作成し **$vm** 変数に格納した構成を使用して、新しい VM を作成します。

```powershell
New-AzureRmVM -VM $vm -ResourceGroupName $rgName -Location $location
```

## <a name="verify-that-the-vm-was-created"></a>VM 作成の確認
完了したら、[Azure Portal](https://portal.azure.com) で **[参照]** > **[仮想マシン]** にアクセスするか、次の PowerShell コマンドを使用して、新しく作成された VM を確認します。

```powershell
    $vmList = Get-AzureRmVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>次のステップ
Azure PowerShell で新しい仮想マシンを管理する方法については、 [Azure Resource Manager と PowerShell を使用した仮想マシンの管理](virtual-machines-windows-ps-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関するページをご覧ください。


