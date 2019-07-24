---
title: Azure PowerShell のサンプル スクリプト - サイト間 VPN の構成 | Microsoft Docs
description: サイト間 VPN を構成します。
services: vpn-gateway
documentationcenter: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.devlang: powershell
ms.topic: sample
ms.date: 04/30/2018
ms.author: alzam
ms.openlocfilehash: 33a79461e1373657c366623e7ef5dbaf6aecf78f
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58442384"
---
# <a name="create-a-vpn-gateway-and-add-a-site-to-site-connection-using-powershell"></a>PowerShell を使用して、VPN Gateway を作成し、サイト間接続を追加する

このスクリプトは、ルート ベースの VPN Gateway を作成し、サイト間の構成を追加します。 また、接続を作成するために、VPN デバイスを構成する必要があります。 詳細については、「[サイト間 VPN Gateway 接続用の VPN デバイスと IPsec/IKE パラメーターについて](../vpn-gateway-about-vpn-devices.md)」を参照してください。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

```azurepowershell-interactive
# Declare variables
  $VNetName  = "VNet1"
  $FESubName = "FrontEnd"
  $BESubName = "Backend"
  $GWSubName = "GatewaySubnet"
  $VNetPrefix1 = "10.0.0.0/16"
  $FESubPrefix = "10.1.0.0/24"
  $BESubPrefix = "10.1.1.0/24"
  $GWSubPrefix = "10.1.255.0/27"
  $VPNClientAddressPool = "192.168.0.0/24"
  $RG = "TestRG1"
  $Location = "East US"
  $GWName = "VNet1GW"
  $GWIPName = "VNet1GWIP"
  $GWIPconfName = "gwipconf"
# Create a resource group
New-AzResourceGroup -Name TestRG1 -Location EastUS
# Create a virtual network
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName TestRG1 `
  -Location EastUS `
  -Name VNet1 `
  -AddressPrefix 10.1.0.0/16
# Create a subnet configuration
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name Frontend `
  -AddressPrefix 10.1.0.0/24 `
  -VirtualNetwork $virtualNetwork
# Set the subnet configuration for the virtual network
$virtualNetwork | Set-AzVirtualNetwork
# Add a gateway subnet
$vnet = Get-AzVirtualNetwork -ResourceGroupName TestRG1 -Name VNet1
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27 -VirtualNetwork $vnet
# Set the subnet configuration for the virtual network
$vnet | Set-AzVirtualNetwork
# Request a public IP address
$gwpip= New-AzPublicIpAddress -Name VNet1GWIP -ResourceGroupName TestRG1 -Location 'East US' `
 -AllocationMethod Dynamic
# Create the gateway IP address configuration
$vnet = Get-AzVirtualNetwork -Name VNet1 -ResourceGroupName TestRG1
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gwipconfig = New-AzVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id
# Create the VPN gateway
New-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1 `
 -Location 'East US' -IpConfigurations $gwipconfig -GatewayType Vpn `
 -VpnType RouteBased -GatewaySku VpnGw1
# Create the local network gateway
New-AzLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1 `
 -Location 'East US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.101.0.0/24','10.101.1.0/24')
# Configure your on-premises VPN device
# Create the VPN connection
$gateway1 = Get-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1
$local = Get-AzLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1
New-AzVirtualNetworkGatewayConnection -Name VNet1toSite1 -ResourceGroupName TestRG1 `
 -Location 'East US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
 -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

作成したリソースが不要になったら、[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) コマンドを使用してリソース グループを削除します。 これでリソース グループとそれに含まれるすべてのリソースが削除されます。

```azurepowershell-interactive
Remove-AzResourceGroup -Name TestRG1
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、以下のコマンドを実行してデプロイを作成します。 表内の各項目は、コマンドごとのドキュメントにリンクされています。

| command | メモ |
|---|---|
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | サブネット構成を追加します。 この構成は、仮想ネットワークの作成プロセスで使用されます。 |
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | 仮想ネットワークの詳細を取得します。 |
| [Get-AzVirtualNetworkGateway](/powershell/module/az.network/get-azvirtualnetworkgateway) | 仮想ネットワーク ゲートウェイの詳細を取得します。 |
| [Get-AzLocalNetworkGateway](/powershell/module/az.network/get-azvirtualnetworkgateway) | ローカル ネットワーク ゲートウェイの詳細を取得します。 |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | 仮想ネットワークのサブネット構成の詳細を取得します。 |
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | すべてのリソースを格納するリソース グループを作成します。 |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | サブネット構成を作成します。 この構成は、仮想ネットワークの作成プロセスで使用されます。 |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | 仮想ネットワークを作成します。 |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | パブリック IP アドレスを作成します。 |
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | 新しいゲートウェイ IP 構成を作成します。 |
| [New-AzVirtualNetworkGateway](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworkgateway) | VPN ゲートウェイを作成します。 |
| [New-AzLocalNetworkGateway](https://docs.microsoft.com/powershell/module/az.network/new-azlocalnetworkgateway) | ローカル ネットワーク ゲートウェイを作成します。 |
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | サイト間接続を作成します。 |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | リソース グループと、それに含まれているすべてのリソースを削除します。 |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | 仮想ネットワークのサブネット構成を設定します。 |
| [Set-AzVirtualNetworkGateway](/powershell/module/az.network/set-azvirtualnetworkgateway) | VPN ゲートウェイの構成を設定します。 |

## <a name="next-steps"></a>次の手順

Azure PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](/powershell/azure/overview)を参照してください。
