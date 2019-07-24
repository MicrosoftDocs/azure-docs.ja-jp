---
title: Azure PowerShell のサンプル スクリプト - VPN Gateway の作成 | Microsoft Docs
description: PowerShell を使用して VPN Gateway を作成します。
services: vpn-gateway
documentationcenter: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.devlang: powershell
ms.topic: sample
ms.date: 04/17/2018
ms.author: alzam
ms.openlocfilehash: 433899c2b558a5182ca1a5c7c0e0ac76c87e0007
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58447599"
---
# <a name="create-a-vpn-gateway-with-powershell"></a>PowerShell を使用して VPN Gateway を作成する

このスクリプトは、ルートベースの VPN Gateway を作成します。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]


```azurepowershell-interactive
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
$gwpip= New-AzPublicIpAddress -Name VNet1GWIP -ResourceGroupName TestRG1 -Location 'East US' -AllocationMethod Dynamic
# Create the gateway IP address configuration
$vnet = Get-AzVirtualNetwork -Name VNet1 -ResourceGroupName TestRG1
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gwipconfig = New-AzVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id
# Create the VPN gateway
New-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1 `
-Location 'East US' -IpConfigurations $gwipconfig -GatewayType Vpn `
-VpnType RouteBased -GatewaySku VpnGw1
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
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | すべてのリソースを格納するリソース グループを作成します。 |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | サブネット構成を作成します。 この構成は、仮想ネットワークの作成プロセスで使用されます。 |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | 仮想ネットワークを作成します。 |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | パブリック IP アドレスを作成します。 |
|[New-AzVirtualNetworkGateway](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworkgateway) | VPN ゲートウェイを作成します。 |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | リソース グループと、それに含まれているすべてのリソースを削除します。 |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | 仮想ネットワークのサブネット構成を設定します。 |

## <a name="next-steps"></a>次の手順

Azure PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](/powershell/azure/overview)を参照してください。
