---
title: チュートリアル - Azure ExpressRoute:VNet にゲートウェイを追加する - Azure PowerShell
description: このチュートリアルでは、Azure PowerShell を使用して、ExpressRoute の作成済みの Resource Manager VNet に VNet ゲートウェイを追加する方法を説明します。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/05/2020
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 2b75e6e0a8b79f374900e6cb2dfc49680d3d0190
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101739060"
---
# <a name="tutorial-configure-a-virtual-network-gateway-for-expressroute-using-powershell"></a>チュートリアル:PowerShell を使用して ExpressRoute の仮想ネットワーク ゲートウェイを構成する
> [!div class="op_single_selector"]
> * [Resource Manager - Azure Portal](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager - PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [クラシック - PowerShell](expressroute-howto-add-gateway-classic.md)
> * [ビデオ - Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 

このチュートリアルでは、既存の仮想ネットワーク (VNet) 用の VNet ゲートウェイを追加、サイズ変更、および削除する手順を説明します。 この構成の手順は、ExpressRoute 構成用に Resource Manager デプロイ モデルを使用して作成された VNet に適用されます。 詳細については、「[ExpressRoute 用の仮想ネットワーク ゲートウェイについて](expressroute-about-virtual-network-gateways.md)」をご覧ください。

このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
> - ゲートウェイ サブネットを作成します。
> - 仮想ネットワーク ゲートウェイを作成します。

## <a name="prerequisites"></a>前提条件

### <a name="configuration-reference-list"></a>構成参照一覧

このタスクの手順では、以下の構成参照一覧の値に基づいて VNet を使用します。 その他の設定と名前についても、一覧で概要を説明します。 ここでは、この一覧内の値に基づいて変数を追加しますが、どの手順でもこの一覧を直接使用しません。 参照として使用する一覧をコピーし、値を独自の値で置き換えることができます。

| 設定                   | [値]                                              |
| ---                       | ---                                                |
| 仮想ネットワーク名 | *TestVNet* |    
| Virtual Network のアドレス空間 | *192.168.0.0/16* |
| リソース グループ | *TestRG* |
| Subnet1 名 | *FrontEnd* |   
| Subnet1 アドレス空間 | *192.168.1.0/24* |
| Subnet1 名 | *FrontEnd* |
| ゲートウェイ サブネット名 | *GatewaySubnet* |    
| ゲートウェイ サブネットのアドレス空間 | *192.168.200.0/26* |
| リージョン | *米国東部* |
| ゲートウェイ名 | *GW* |   
| ゲートウェイ IP 名 | *GWIP* |
| ゲートウェイ IP 構成名 | *gwipconf* |
| 型 | *ExpressRoute* |
| ゲートウェイのパブリック IP 名  | *gwpip* |

> [!IMPORTANT]
> 現在、プライベート ピアリングの IPv6 サポートは **パブリック プレビュー** の段階にあります。 IPv6 ベースのプライベート ピアリングを構成して仮想ネットワークを ExpressRoute 回線に接続したい場合は、仮想ネットワークをデュアル スタックにし、[こちら](https://docs.microsoft.com/azure/virtual-network/ipv6-overview)で説明されているガイドラインに従ってください。
> 
> 

## <a name="add-a-gateway"></a>ゲートウェイを追加する

1. Azure に接続するには、`Connect-AzAccount` を実行します。

1. この演習用に変数を宣言します。 サンプルを編集して、使用する設定を反映します。

   ```azurepowershell-interactive 
   $RG = "TestRG"
   $Location = "East US"
   $GWName = "GW"
   $GWIPName = "GWIP"
   $GWIPconfName = "gwipconf"
   $VNetName = "TestVNet"
   ```
1. 仮想ネットワーク オブジェクトを変数として格納します。

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG
   ```
1. ゲートウェイ サブネットを Virtual Network に追加します。 ゲートウェイ サブネット名は、"GatewaySubnet " にする必要があります。 ゲートウェイ サブネットは、/27 以上 (/26、/25 など) である必要があります。

   ```azurepowershell-interactive
   Add-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix 192.168.200.0/26
   ```
    デュアル スタックの仮想ネットワークを使用していて、ExpressRoute 経由で IPv6 ベースのプライベート ピアリングを使用する予定の場合は、代わりにデュアル スタックのゲートウェイ サブネットを作成してください。

   ```azurepowershell-interactive
   Add-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix "10.0.0.0/26","ace:daa:daaa:deaa::/64"
   ```
1. 構成を設定します。

   ```azurepowershell-interactive
   $vnet = Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```
1. 変数としてゲートウェイ サブネットを格納します。

   ```azurepowershell-interactive
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
   ```
1. パブリック IP アドレスを要求します。 IP アドレスは、ゲートウェイを作成する前に要求されます。 使用したい IP アドレスを指定することはできません。IP アドレスは動的に割り当てられます。 この IP アドレスは、次の構成セクションで使用します。 AllocationMethod は動的である必要があります。

   ```azurepowershell-interactive
   $pip = New-AzPublicIpAddress -Name $GWIPName  -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
   ```
1. ゲートウェイの構成を作成します。 ゲートウェイの構成で、使用するサブネットとパブリック IP アドレスを定義します。 この手順では、ゲートウェイを作成するときに使用する構成を指定します。 次のサンプルを使用して、ゲートウェイの構成を作成します。

   ```azurepowershell-interactive
   $ipconf = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
   ```
1. ゲートウェイを作成します。 この手順では、 **-GatewayType** が特に重要です。 値 **ExpressRoute** を使用する必要があります。 これらのコマンドレットを実行してからゲートウェイが作成されるまでに 45 分以上かかる場合があります。

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG -Location $Location -IpConfigurations $ipconf -GatewayType Expressroute -GatewaySku Standard
   ```
> [!IMPORTANT]
> ExpressRoute 経由で IPv6 ベースのプライベート ピアリングを使用する予定の場合は、 **-GatewaySku** に対して AZ SKU (ErGw1AZ、ErGw2AZ、ErGw3AZ) を選択してください。
> 
> 

## <a name="verify-the-gateway-was-created"></a>ゲートウェイが作成されていることの確認
次のコマンドを使用して、ゲートウェイが作成されていることを確認します。

```azurepowershell-interactive
Get-AzVirtualNetworkGateway -ResourceGroupName $RG
```

## <a name="resize-a-gateway"></a>ゲートウェイのサイズを変更する
[ゲートウェイ SKU](expressroute-about-virtual-network-gateways.md)には複数の種類があります。 次のコマンドを使用して、ゲートウェイ SKU をいつでも変更できます。

> [!IMPORTANT]
> このコマンドは UltraPerformance ゲートウェイでは使用できません。 ゲートウェイを UltraPerformance ゲートウェイに変更するには、既存の ExpressRoute ゲートウェイを削除してから、新しい UltraPerformance ゲートウェイを作成します。 ゲートウェイを UltraPerformance ゲートウェイからダウングレードするには、UltraPerformance ゲートウェイを削除してから、新しいゲートウェイを作成します。
> 

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする
次のコマンドを使用してゲートウェイを削除します。

```azurepowershell-interactive
Remove-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
```

## <a name="next-steps"></a>次のステップ
VNet ゲートウェイを作成したので、VNet を ExpressRoute 回線にリンクできます。 

> [!div class="nextstepaction"]
> [仮想ネットワークを ExpressRoute 回線にリンクする](expressroute-howto-linkvnet-arm.md)
