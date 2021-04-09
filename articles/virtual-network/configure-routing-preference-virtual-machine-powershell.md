---
title: VM 用にルーティング優先設定を構成する - Azure PowerShell
description: Azure PowerShell を使用して、ルーティング優先設定を選択してパブリック IP アドレスを持つ VM を作成する方法について説明します。
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/01/2021
ms.author: mnayak
ms.openlocfilehash: 8ffa7ef7b3539e3cefc4e2f8621d7c67cd3b56b0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101672961"
---
# <a name="configure-routing-preference-for-a-vm-using-azure-powershell"></a>Azure PowerShell を使用して VM 用にルーティング優先設定を構成する

この記事では、仮想マシン用にルーティング優先設定を構成する方法について説明します。 ルーティング優先設定オプションとして **[インターネット]** を選択すると、VM からインターネットへのトラフィックは ISP ネットワーク経由でルーティングされます。 既定のルーティングは、Microsoft グローバル ネットワーク経由です。

この記事では、Azure PowerShell を使用して、ISP ネットワーク経由でトラフィックをルーティングするように設定されたパブリック IP を持つ仮想マシンを作成する方法について説明します。

## <a name="create-a-resource-group"></a>リソース グループを作成する
1. Cloud Shell を使用する場合は、手順 2 に進みます。 コマンド セッションを開き、`Connect-AzAccount` で Azure にサインインします。
2. [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) コマンドでリソース グループを作成します。 次の例では、Azure の米国東部リージョンにリソース グループを作成します。

    ```azurepowershell
    $rg = New-AzResourceGroup -Name MyResourceGroup -Location EastUS
    ```

## <a name="create-a-public-ip-address"></a>パブリック IP アドレスの作成

インターネットから仮想マシンにアクセスするには、パブリック IP アドレスが必要です。 [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) を使用してパブリック IP アドレスを作成します。 次の例では、"*米国東部*" リージョンの *MyResourceGroup* リソース グループに、ルーティング優先設定の種類が "*インターネット*" で *MyPublicIP* という名前の IPv4 パブリック IP アドレスを作成します。

```azurepowershell-interactive
$iptagtype="RoutingPreference"
$tagName = "Internet"
$ipTag = New-AzPublicIpTag -IpTagType $iptagtype -Tag $tagName 
# attach the tag
$publicIp = New-AzPublicIpAddress  `
-Name "MyPublicIP" `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location `
-IpTag $ipTag `
-AllocationMethod Static `
-Sku Standard `
-IpAddressVersion IPv4
```

## <a name="create-network-resources"></a>ネットワーク リソースを作成する

VM をデプロイする前に、サポートするネットワーク リソース (ネットワーク セキュリティ グループ、仮想ネットワーク、仮想 NIC) を作成する必要があります。

### <a name="create-a-network-security-group"></a>ネットワーク セキュリティ グループの作成

[New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) を使用して、ネットワーク セキュリティ グループを作成します。 次の例では、*myNSG* という名前の NSG を作成します

```azurepowershell
$nsg = New-AzNetworkSecurityGroup `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-Name "myNSG"
```

### <a name="create-a-virtual-network"></a>仮想ネットワークの作成

[New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) を使用して仮想ネットワークを作成します。 次の例では、*mySubNet* を含む *myVNET* という名前の仮想ネットワークを作成します。

### <a name="create-a-subnet"></a>サブネットの作成

```azurepowershell
$subnet = New-AzVirtualNetworkSubnetConfig `
-Name "mySubnet" `
-AddressPrefix "10.0.0.0/24"
```

```azurepowershell
# Create a virtual network
$vnet = New-AzVirtualNetwork `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-Name "myVNET" `
-AddressPrefix "10.0.0.0/16" `
-Subnet $subnet
```

### <a name="create-a-nic"></a>NIC を作成する

[New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) を使用して仮想 NIC を作成します。 次の例では、仮想 NIC を作成します。

```azurepowershell
# Create an IP Config
$ipconfig=New-AzNetworkInterfaceIpConfig `
-Name myIpConfig `
-Subnet $vnet.subnets[0] `
-PrivateIpAddressVersion IPv4 `
-PublicIpAddress  $publicIp

# Create a NIC
$nic = New-AzNetworkInterface `
-Name "mynic" `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-NetworkSecurityGroupId $nsg.Id `
-IpConfiguration $ipconfig 
```

## <a name="create-a-virtual-machine"></a>仮想マシンの作成

次のように、[Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) を使用して VM の管理者のユーザー名とパスワードを設定します。

```azurepowershell
 $cred = get-credential -Message "Routing Preference SAMPLE:  Please enter the Administrator credential to log into the VM."
```

これで、[New-AzVM](/powershell/module/az.compute/new-azvm) を使用して VM を作成できるようになります。 次の例では、2 つの VM と必要な仮想ネットワーク コンポーネントがまだ存在しない場合にそれらを作成します。

```azurepowershell
 $vmsize = "Standard_A2"
 $ImagePublisher = "MicrosoftWindowsServer"
 $imageOffer = "WindowsServer"
 $imageSKU = "2019-Datacenter"

 $vmName= "myVM"
 $vmconfig = New-AzVMConfig -VMName $vmName -VMSize $vmsize | Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate | Set-AzVMSourceImage -PublisherName $ImagePublisher -Offer $imageOffer -Skus $imageSKU -Version "latest" | Set-AzVMOSDisk -Name "$vmName.vhd" -CreateOption "FromImage" | Add-AzVMNetworkInterface -Id $nic.Id 
 $VM1 = New-AzVM -ResourceGroupName $rg.ResourceGroupName  -Location $rg.Location  -VM $vmconfig
```

## <a name="allow-network-traffic-to-the-vm"></a>VM へのネットワーク トラフィックを許可する

インターネットからパブリック IP アドレスに接続するには、事前に、ネットワーク インターフェイスまたはそのネットワーク インターフェイスが存在しているサブネット、あるいはその両方に関連付けたはずのネットワーク セキュリティ グループにおいて、必要なポートが開かれていることを確認してください。 [ポータル](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-portal)、[CLI](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-cli)、または [PowerShell](diagnose-network-traffic-filter-problem.md#diagnose-using-powershell) を使用して、ネットワーク インターフェイスとそのサブネットに対する有効なセキュリティ規則を確認できます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

必要がなくなったら、[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) コマンドを使用して、リソース グループ、VM、およびすべての関連リソースを削除できます。

 ```azurepowershell
 Remove-AzResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>次のステップ

* [パブリック IP アドレスでのルーティング優先設定](routing-preference-overview.md)について詳しく学習する。
* Azure での[パブリック IP アドレス](./public-ip-addresses.md#public-ip-addresses)について詳しく学習する。
* [パブリック IP アドレスの設定](virtual-network-public-ip-address.md#create-a-public-ip-address)について詳しく学習する。