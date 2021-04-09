---
title: Standard Internal Load Balancer を使用する IPv6 デュアル スタック アプリケーションを Azure 上にデプロイする - PowerShell
titlesuffix: Azure Virtual Network
description: この記事では、Standard Internal Load Balancer を使用する IPv6 デュアル スタック アプリケーションを、Azure PowerShell を使用して Azure 仮想ネットワーク内にデプロイする方法を説明します。
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/14/2019
ms.author: kumud
ms.openlocfilehash: 3df89b84e748f041f13866c1eb3c0b8a3341209c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98220832"
---
# <a name="deploy-an-ipv6-dual-stack-application-using-standard-internal-load-balancer-in-azure---powershell-preview"></a>Standard Internal Load Balancer を使用する IPv6 デュアル スタック アプリケーションを Azure 上にデプロイする - PowerShell (プレビュー)

この記事では、Azure でデュアル スタック (IPv4 と IPv6) アプリケーションをデプロイする方法を示します。これには、デュアル スタック仮想ネットワークとサブネット、デュアル (IPv4 と IPv6) フロントエンド構成を持つ Standard 内部ロード バランサー、デュアル IP 構成、ネットワーク セキュリティ グループ、およびパブリック IP を使用する NIC を持つ VM が含まれます。

IPv6 に対応した内部ロード バランサーを作成する手順は、インターネットに接続された IPv6 ロード バランサーを作成する手順 ([こちら](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md)を参照) とほぼ同じです。 内部ロード バランサーを作成する場合の唯一の違いは、次の PowerShell の例に示すように、フロントエンド構成にあります。

```azurepowershell
 $frontendIPv6 = New-AzLoadBalancerFrontendIpConfig `
 -Name "dsLbFrontEnd_v6" `
 -PrivateIpAddress "fd00:db8:deca:deed::100" `
 -PrivateIpAddressVersion "IPv6" `
 -Subnet $DsSubnet
```

上記を内部ロード バランサーのフロントエンド構成にする変更は次のとおりです。
- `PrivateIpAddressVersion` が "IPv6" として指定されています。
- `-PublicIpAddress` 引数が省略されているか、`-PrivateIpAddress` に置き換えられています。 プライベート アドレスは、内部ロード バランサーがデプロイされるサブネット IP 空間の範囲内である必要があることに注意してください。 静的 `-PrivateIpAddress` を省略した場合、内部ロード バランサーがデプロイされるサブネットから、次に空いている IPv6 アドレスが選択されます。
- 内部ロード バランサーがデプロイされるデュアル スタック サブネットは、`-Subnet` または `-SubnetId` のいずれかの引数を使用して指定します。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

PowerShell をローカルにインストールして使用することを選択する場合、この記事では Azure PowerShell モジュール バージョン 6.9.0 以降が必要になります。 インストールされているバージョンを確認するには、`Get-Module -ListAvailable Az` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-Az-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzAccount` を実行して Azure との接続を作成することも必要です。

## <a name="prerequisites"></a>前提条件
Azure でデュアル スタック アプリケーションをデプロイする前に、次の Azure PowerShell を使用して、このプレビュー機能に対してサブスクリプションを構成する必要があります。

次のように登録します。
```azurepowershell
Register-AzProviderFeature -FeatureName AllowIPv6VirtualNetwork -ProviderNamespace Microsoft.Network
Register-AzProviderFeature -FeatureName AllowIPv6CAOnStandardLB -ProviderNamespace Microsoft.Network
```
機能の登録が完了するまで、最長で 30 分かかります。 次の Azure PowerShell コマンドを実行することで、登録状態を確認できます。次のように登録を確認します。
```azurepowershell
Get-AzProviderFeature -FeatureName AllowIPv6VirtualNetwork -ProviderNamespace Microsoft.Network
Get-AzProviderFeature -FeatureName AllowIPv6CAOnStandardLB -ProviderNamespace Microsoft.Network
```
登録が完了した後、次のコマンドを実行します。

```azurepowershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

## <a name="create-a-resource-group"></a>リソース グループを作成する

デュアル スタック仮想ネットワークを作成する前に、[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) でリソース グループを作成する必要があります。 次の例では、*dsStd_ILB_RG* という名前のリソース グループを *east us* の場所に作成します。

```azurepowershell
$rg = New-AzResourceGroup `
  -ResourceGroupName "dsStd_ILB_RG"  `
  -Location "east us"
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses"></a>IPv4 および IPv6 パブリック IP アドレスを作成する
インターネットから仮想マシンにアクセスするには、VM に IPv4 および IPv6 パブリック IP アドレスが必要です。 [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) を使用してパブリック IP アドレスを作成します。 次の例では、*dsStd_ILB_RG* リソース グループ内に *RdpPublicIP_1* および *RdpPublicIP_2* という名前の IPv4 および IPv6 パブリック IP アドレスを作成します。

```azurepowershell
$RdpPublicIP_1 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_1" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Static `
  -IpAddressVersion IPv4  `
  -sku Standard
  
$RdpPublicIP_2 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_2" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Static `
  -IpAddressVersion IPv4  `
  -sku Standard
```
## <a name="create-the-virtual-network-and-the-subnet"></a>仮想ネットワークとサブネットを作成する

[New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) を使用してデュアル スタックの仮想ネットワークを作成し、[New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) を使用してサブネット構成を作成します。 次の例では、*dsSubnet* と共に *dsVnet* という名前の仮想ネットワークを作成します。

```azurepowershell
# Create dual stack subnet config
$DsSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name "dsSubnet" `
  -AddressPrefix "10.0.0.0/24","fd00:db8:deca:deed::/64"

# Create the virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsVnet" `
  -AddressPrefix "10.0.0.0/16","fd00:db8:deca::/48"  `
  -Subnet $DsSubnet

#Refresh the fully populated subnet for use in load balancer frontend configuration
$DsSubnet = get-AzVirtualNetworkSubnetconfig -name dsSubnet -VirtualNetwork $vnet
```
## <a name="create-standard-load-balancer"></a>Standard Load Balancer を作成する

このセクションでは、ロード バランサーのデュアル フロントエンド IP (IPv4 および IPv6) とバックエンド アドレス プールを構成してから、Standard ロード バランサーを作成します。

### <a name="create-front-end-ip"></a>フロントエンド IP を作成する

[New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) を使用して、フロントエンド IP を作成します。 次の例では、*dsLbFrontEnd_v4* および *dsLbFrontEnd_v6* という名前の IPv4 および IPv6 フロントエンド IP 構成を作成します。

```azurepowershell
$frontendIPv4 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v4" `
  -PrivateIpAddress "10.0.0.100"  `
  -PrivateIpAddressVersion "IPv4"   `
  -Subnet $DsSubnet

$frontendIPv6 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v6" `
  -PrivateIpAddress "fd00:db8:deca:deed::100"  `
  -PrivateIpAddressVersion "IPv6"   `
  -Subnet $DsSubnet

```

### <a name="configure-back-end-address-pool"></a>バックエンド アドレス プールを構成する

[New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) を使用して、バックエンド アドレス プールを作成します。 残りの手順では、VM をこのバックエンド プールにアタッチします。 次の例では、IPV4 と IPv6 の両方の NIC 構成を持つ VM を含めるための、*dsLbBackEndPool_v4* および *dsLbBackEndPool_v6* という名前のバックエンド アドレス プールを作成します。

```azurepowershell
$backendPoolv4 = New-AzLoadBalancerBackendAddressPoolConfig -Name "dsLbBackEndPool_v4"

$backendPoolv6 = New-AzLoadBalancerBackendAddressPoolConfig -Name "dsLbBackEndPool_v6"
```

### <a name="create-a-load-balancer-rule"></a>ロード バランサー規則の作成

ロード バランサー規則の目的は、一連の VM に対するトラフィックの分散方法を定義することです。 着信トラフィック用のフロントエンド IP 構成と、トラフィックを受信するためのバックエンド IP プールを、必要な発信元ポートと宛先ポートと共に定義します。 確実に正常な VM のみでトラフィックを受信するために、必要に応じて、正常性プローブを定義できます。 Basic Load Balancer では IPv4 プローブを使用して、VM 上の IPv4 と IPv6 の両方のエンドポイントの正常性を評価します。 Standard Load Balancer には、明示的な IPv6 正常性プローブのサポートが含まれます。

[Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig) を使用して、ロード バランサー規則を作成します。 次の例では、*dsLBrule_v4* および *dsLBrule_v6* という名前のロード バランサー規則を作成し、IPv4 および IPv6 フロントエンド IP 構成に応じて、*TCP* ポート *80* のトラフィックを負荷分散します。

```azurepowershell
$lbrule_v4 = New-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v4" `
  -FrontendIpConfiguration $frontendIPv4 `
  -BackendAddressPool $backendPoolv4 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80

$lbrule_v6 = New-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v6" `
  -FrontendIpConfiguration $frontendIPv6 `
  -BackendAddressPool $backendPoolv6 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80
```

### <a name="create-load-balancer"></a>ロード バランサーの作成

[New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) を使用して、Standard ロード バランサーを作成します。 次の例では、前述の手順で作成した IPv4 と IPv6 フロントエンド IP の構成、バックエンド プール、および負荷分散規則を使用して、*myInternalLoadBalancer* という名前のパブリック Standard ロード バランサーを作成します。

```azurepowershell
$lb = New-AzLoadBalancer  `
  -ResourceGroupName $rg.ResourceGroupName  `
  -Location $rg.Location  `
  -Name  "MyInternalLoadBalancer"  `
  -Sku "Standard"  `
  -FrontendIpConfiguration  $frontendIPv4,$frontendIPv6  `
  -BackendAddressPool  $backendPoolv4,$backendPoolv6  `
  -LoadBalancingRule  $lbrule_v4,$lbrule_v6

```

## <a name="create-network-resources"></a>ネットワーク リソースを作成する
VM をいくつかデプロイしてから、バランサーをテストできるようになる前に、サポート ネットワーク リソース (可用性セット、ネットワーク セキュリティ グループ、および仮想 NIC) を作成する必要があります。 

### <a name="create-an-availability-set"></a>可用性セットの作成
アプリケーションの高可用性を高めるには、可用性セットに VM を配置します。

可用性セットを作成するには、[New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset) を使用します。 次の例では、*dsAVset* という名前の可用性セットを作成します。

```azurepowershell
$avset = New-AzAvailabilitySet `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsAVset" `
  -PlatformFaultDomainCount 2 `
  -PlatformUpdateDomainCount 2 `
  -Sku aligned
```

### <a name="create-network-security-group"></a>ネットワーク セキュリティ グループの作成

VNet でのインバウンドおよびアウトバウンド通信を管理する規則のネットワーク セキュリティ グループを作成します。

#### <a name="create-a-network-security-group-rule-for-port-3389"></a>ポート 3389 のネットワーク セキュリティ グループの規則を作成する

[New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) を使用してポート 3389 経由の RDP 接続を許可するネットワーク セキュリティ グループ規則を作成します。

```azurepowershell
$rule1 = New-AzNetworkSecurityRuleConfig `
  -Name 'myNetworkSecurityGroupRuleRDP' `
  -Description 'Allow RDP' `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 100 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 3389
```
#### <a name="create-a-network-security-group-rule-for-port-80"></a>ポート 80 のネットワーク セキュリティ グループ規則を作成する

[New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) を使用して、ポート 80 経由のインターネット接続を許可するネットワーク セキュリティ グループ規則を作成します。

```azurepowershell
$rule2 = New-AzNetworkSecurityRuleConfig `
  -Name 'myNetworkSecurityGroupRuleHTTP' `
  -Description 'Allow HTTP' `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 200 `
  -SourceAddressPrefix * `
  -SourcePortRange 80 `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80
```
#### <a name="create-a-network-security-group"></a>ネットワーク セキュリティ グループの作成

[New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) を使用して、ネットワーク セキュリティ グループを作成します。

```azurepowershell
$nsg = New-AzNetworkSecurityGroup `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsNSG1"  `
  -SecurityRules $rule1,$rule2
```
### <a name="create-nics"></a>NIC の作成

[New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) を使用して仮想 NIC を作成します。 次の例では、IPv4 と IPv6 の両方の構成を持つ 2 つの仮想 NIC を作成します。 (以降の手順では、アプリ用に作成する VM ごとに仮想 NIC を 1 つ)。

```azurepowershell

# Create the IPv4 configuration for NIC 1
$Ip4Config=New-AzNetworkInterfaceIpConfig `
  -Name dsIp4Config `
  -Subnet $vnet.subnets[0] `
  -PrivateIpAddressVersion IPv4 `
  -LoadBalancerBackendAddressPool $backendPoolv4 `
  -PublicIpAddress  $RdpPublicIP_1

# Create the IPv6 configuration
$Ip6Config=New-AzNetworkInterfaceIpConfig `
  -Name dsIp6Config `
  -Subnet $vnet.subnets[0] `
  -PrivateIpAddressVersion IPv6 `
  -LoadBalancerBackendAddressPool $backendPoolv6

# Create NIC 1
$NIC_1 = New-AzNetworkInterface `
  -Name "dsNIC1" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -NetworkSecurityGroupId $nsg.Id `
  -IpConfiguration $Ip4Config,$Ip6Config

# Create the IPv4 configuration for NIC 2
$Ip4Config=New-AzNetworkInterfaceIpConfig `
  -Name dsIp4Config `
  -Subnet $vnet.subnets[0] `
  -PrivateIpAddressVersion IPv4 `
  -LoadBalancerBackendAddressPool $backendPoolv4 `
  -PublicIpAddress  $RdpPublicIP_2

# Create NIC 2 reusing the IPv6 configuration from NIC 1
$NIC_2 = New-AzNetworkInterface `
  -Name "dsNIC2" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -NetworkSecurityGroupId $nsg.Id `
  -IpConfiguration $Ip4Config,$Ip6Config

```

### <a name="create-virtual-machines"></a>仮想マシンを作成する

次のように、[Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) を使用して VM の管理者のユーザー名とパスワードを設定します。

```azurepowershell
$cred = get-credential -Message "DUAL STACK VNET SAMPLE:  Please enter the Administrator credential to log into the VM's"
```

[New-AzVM](/powershell/module/az.compute/new-azvm) を使用して VM を作成できるようになりました。 次の例では、2 つの VM と必要な仮想ネットワーク コンポーネントがまだ存在しない場合にそれらを作成します。

```azurepowershell
$vmsize = "Standard_A2"
$ImagePublisher = "MicrosoftWindowsServer"
$imageOffer = "WindowsServer"
$imageSKU = "2019-Datacenter"

$vmName= "dsVM1"
$VMconfig1 = New-AzVMConfig -VMName $vmName -VMSize $vmsize -AvailabilitySetId $avset.Id 3> $null | Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent 3> $null | Set-AzVMSourceImage -PublisherName $ImagePublisher -Offer $imageOffer -Skus $imageSKU -Version "latest" 3> $null | Set-AzVMOSDisk -Name "$vmName.vhd" -CreateOption fromImage  3> $null | Add-AzVMNetworkInterface -Id $NIC_1.Id  3> $null
$VM1 = New-AzVM -ResourceGroupName $rg.ResourceGroupName  -Location $rg.Location  -VM $VMconfig1


$vmName= "dsVM2"
$VMconfig2 = New-AzVMConfig -VMName $vmName -VMSize $vmsize -AvailabilitySetId $avset.Id 3> $null | Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent 3> $null | Set-AzVMSourceImage -PublisherName $ImagePublisher -Offer $imageOffer -Skus $imageSKU -Version "latest" 3> $null | Set-AzVMOSDisk -Name "$vmName.vhd" -CreateOption fromImage  3> $null | Add-AzVMNetworkInterface -Id $NIC_2.Id  3> $null
$VM2 = New-AzVM -ResourceGroupName $rg.ResourceGroupName  -Location $rg.Location  -VM $VMconfig2
```
## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Azure portal で IPv6 デュアル スタック仮想ネットワークを表示する
次のようにして、Azure portal で IPv6 デュアル スタック仮想ネットワークを表示することができます。
1. ポータルの検索バーで、「*dsVnet*」と入力します。
2. 検索結果に **dsVnet** が表示されたら、それを選択します。 これにより、*dsVnet* という名前のデュアル スタック仮想ネットワークの **[概要]** ページが起動します。 デュアル スタック仮想ネットワークには、*dsSubnet* という名前のデュアル スタック サブネットにある、IPv4 と IPv6 の両方の構成を持つ 2 つの NIC が表示されます。

![Standard Internal Load Balancer を使用した IPv6 のデュアル スタック仮想ネットワーク](./media/ipv6-dual-stack-standard-internal-load-balancer-powershell/ipv6-dual-stack-virtual-network.png)


> [!NOTE]
> このプレビュー リリースの場合、Azure 仮想ネットワークの IPv6 は、Azure portal で読み取り専用で使用できます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

必要がなくなったら、[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) コマンドを使用して、リソース グループ、VM、およびすべての関連リソースを削除できます。

```azurepowershell
Remove-AzResourceGroup -Name dsStd_ILB_RG
```

## <a name="next-steps"></a>次のステップ

この記事では、デュアル フロントエンド IP 構成 (IPv4 および IPv6) を持つ Standard ロード バランサーを作成しました。 また、NIC を含む 2 つの仮想マシンも作成しました。この NIC のデュアル IP 構成 (IPV4 および IPv6) は、ロード バランサーのバックエンド プールに追加されました。 Azure 仮想ネットワークでの IPv6 サポートの詳細については、[Azure Virtual Network の IPv6 の概要](ipv6-overview.md)に関するページを参照してください