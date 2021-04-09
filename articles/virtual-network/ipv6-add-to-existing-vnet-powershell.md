---
title: Azure 仮想ネットワーク内の IPv4 アプリケーションを IPv6 にアップグレードする - PowerShell
titlesuffix: Azure Virtual Network
description: この記事では、Azure PowerShell を使用して Azure 仮想ネットワーク内の既存のアプリケーションに IPv6 アドレスをデプロイする方法について示します。
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: 9d2ff583b032ff1f9aa5dbc9706ea6c981fc7265
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96009978"
---
# <a name="upgrade-an-ipv4-application-to-ipv6-in-azure-virtual-network---powershell"></a>Azure 仮想ネットワーク内の IPv4 アプリケーションを IPv6 にアップグレードする - PowerShell

この記事では、Standard Load Balancer とパブリック IP を使用して、Azure 仮想ネットワーク内の既存の IPv4 アプリケーションに IPv6 接続を追加する方法について説明します。 インプレース アップグレードには次のものが含まれます。
- 仮想ネットワークとサブネットの IPv6 アドレス空間
- IPv4 と IPV6 の両方のフロントエンド構成の Standard Load Balancer
- IPv4 + IPv6 の両方が構成された NIC を使用する VM
- IPv6 パブリック IP。これにより、ロード バランサーは、インターネットに接続する IPv6 接続を取得します

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

PowerShell をローカルにインストールして使用することを選択する場合、この記事では Azure PowerShell モジュール バージョン 6.9.0 以降が必要になります。 インストールされているバージョンを確認するには、`Get-Module -ListAvailable Az` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-Az-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzAccount` を実行して Azure との接続を作成することも必要です。

## <a name="prerequisites"></a>前提条件

この記事では、[クイック スタート: Standard Load Balancer を作成する - Azure PowerShell](../load-balancer/quickstart-load-balancer-standard-public-powershell.md) に関するページに説明されているように、Standard Load Balancer をデプロイしたことを前提としています。

## <a name="retrieve-the-resource-group"></a>リソース グループの取得

デュアル スタック仮想ネットワークを作成する前に、[Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup) でリソース グループを取得する必要があります。

```azurepowershell-interactive
$rg = Get-AzResourceGroup  -ResourceGroupName "myResourceGroupSLB"
```

## <a name="create-an-ipv6-ip-addresses"></a>IPv6 IP アドレスの作成

Standard Load Balancer の [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) を使用して、パブリック IPv6 アドレスを作成します。 次の例では、*PublicIP_v6* という名前の IPv6 パブリック IP アドレスを *myResourceGroupSLB* リソース グループに作成します。

```azurepowershell-interactive  
$PublicIP_v6 = New-AzPublicIpAddress `
  -Name "PublicIP_v6" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Sku Standard  `
  -AllocationMethod Static `
  -IpAddressVersion IPv6
```

## <a name="configure-load-balancer-frontend"></a>ロード バランサー フロントエンドの構成

既存のロード バランサー構成を取得し、次のように [Add-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/Add-AzLoadBalancerFrontendIpConfig) を使用して新しい IPv6 IP アドレスを追加します。

```azurepowershell-interactive
# Retrieve the load balancer configuration
$lb = Get-AzLoadBalancer -ResourceGroupName $rg.ResourceGroupName -Name "MyLoadBalancer"

# Add IPv6 components to the local copy of the load balancer configuration
$lb | Add-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v6" `
  -PublicIpAddress $PublicIP_v6

#Update the running load balancer with the new frontend
$lb | Set-AzLoadBalancer
```

## <a name="configure-load-balancer-backend-pool"></a>ロード バランサーのバックエンド プールの構成

ロード バランサー構成のローカル コピーにバックエンド プールを作成し、次のように新しいバックエンド プール構成を使用して実行中のロード バランサーを更新します。

```azurepowershell-interactive
$lb | Add-AzLoadBalancerBackendAddressPoolConfig -Name "LbBackEndPool_v6"

# Update the running load balancer with the new backend pool
$lb | Set-AzLoadBalancer
```

## <a name="configure-load-balancer-rules"></a>ロード バランサー規則の構成
既存のロード バランサー フロントエンドとバックエンド プールの構成を取得し、[Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/Add-AzLoadBalancerRuleConfig) を使用して新しい負荷分散規則を追加します。

```azurepowershell-interactive
# Retrieve the updated (live) versions of the frontend and backend pool
$frontendIPv6 = Get-AzLoadBalancerFrontendIpConfig -Name "dsLbFrontEnd_v6" -LoadBalancer $lb
$backendPoolv6 = Get-AzLoadBalancerBackendAddressPoolConfig -Name "LbBackEndPool_v6" -LoadBalancer $lb

# Create new LB rule with the frontend and backend
$lb | Add-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v6" `
  -FrontendIpConfiguration $frontendIPv6 `
  -BackendAddressPool $backendPoolv6 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80

#Finalize all the load balancer updates on the running load balancer
$lb | Set-AzLoadBalancer
```
## <a name="add-ipv6-address-ranges"></a>IPv6 アドレス範囲の追加

次のように、VM をホストする仮想ネットワークとサブネットに IPv6 アドレス範囲を追加します。

```azurepowershell-interactive
#Add IPv6 ranges to the VNET and subnet
#Retreive the VNET object
$vnet = Get-AzVirtualNetwork  -ResourceGroupName $rg.ResourceGroupName -Name "myVnet" 

#Add IPv6 prefix to the VNET
$vnet.addressspace.addressprefixes.add("fd00:db8:deca::/48")

#Update the running VNET
$vnet |  Set-AzVirtualNetwork

#Retrieve the subnet object from the local copy of the VNET
$subnet= $vnet.subnets[0]

#Add IPv6 prefix to the Subnet (subnet of the VNET prefix, of course)
$subnet.addressprefix.add("fd00:db8:deca::/64")

#Update the running VNET with the new subnet configuration
$vnet |  Set-AzVirtualNetwork
```

## <a name="add-ipv6-configuration-to-nic"></a>NIC への IPv6 構成の追加

次のように [Add-AzNetworkInterfaceIpConfig](/powershell/module/az.network/Add-AzNetworkInterfaceIpConfig) を使用して、すべての VM NIC を IPv6 アドレスで構成します。

```azurepowershell-interactive
#Retrieve the NIC objects
$NIC_1 = Get-AzNetworkInterface -Name "myNic1" -ResourceGroupName $rg.ResourceGroupName
$NIC_2 = Get-AzNetworkInterface -Name "myNic2" -ResourceGroupName $rg.ResourceGroupName
$NIC_3 = Get-AzNetworkInterface -Name "myNic3" -ResourceGroupName $rg.ResourceGroupName

#Add an IPv6 IPconfig to NIC_1 and update the NIC on the running VM
$NIC_1 | Add-AzNetworkInterfaceIpConfig -Name MyIPv6Config -Subnet $vnet.Subnets[0]  -PrivateIpAddressVersion IPv6 -LoadBalancerBackendAddressPool $backendPoolv6 
$NIC_1 | Set-AzNetworkInterface

#Add an IPv6 IPconfig to NIC_2 and update the NIC on the running VM
$NIC_2 | Add-AzNetworkInterfaceIpConfig -Name MyIPv6Config -Subnet $vnet.Subnets[0]  -PrivateIpAddressVersion IPv6 -LoadBalancerBackendAddressPool $backendPoolv6 
$NIC_2 | Set-AzNetworkInterface

#Add an IPv6 IPconfig to NIC_3 and update the NIC on the running VM
$NIC_3 | Add-AzNetworkInterfaceIpConfig -Name MyIPv6Config -Subnet $vnet.Subnets[0]  -PrivateIpAddressVersion IPv6 -LoadBalancerBackendAddressPool $backendPoolv6 
$NIC_3 | Set-AzNetworkInterface
```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Azure portal で IPv6 デュアル スタック仮想ネットワークを表示する

次のようにして、Azure portal で IPv6 デュアル スタック仮想ネットワークを表示することができます。
1. ポータルの検索バーに、「*myVnet*」と入力します。
2. 検索結果に **myVnet** が表示されたら、それを選択します。 これにより、*myVNet* という名前のデュアル スタック仮想ネットワークの **[概要]** ページが起動します。 デュアル スタック仮想ネットワークには、*mySubnet* という名前のデュアル スタック サブネットにある、IPv4 と IPv6 の両方の構成を持つ 3 つの NIC が表示されます。

  ![Azure の IPv6 デュアル スタック仮想ネットワーク](./media/ipv6-add-to-existing-vnet-powershell/ipv6-dual-stack-vnet.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

必要がなくなったら、[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) コマンドを使用して、リソース グループ、VM、およびすべての関連リソースを削除できます。

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyAzureResourceGroupSLB
```

## <a name="next-steps"></a>次のステップ

この記事では、IPv4 フロントエンド IP 構成を持つ既存の Standard Load Balancer をデュアル スタック (IPv4 および IPv6) 構成に更新しました。 また、IPv6 構成をバックエンド プール内の VM の NIC と、それらをホストする仮想ネットワークに追加しました。 Azure 仮想ネットワークでの IPv6 サポートの詳細については、[Azure Virtual Network の IPv6 の概要](ipv6-overview.md)に関するページを参照してください
