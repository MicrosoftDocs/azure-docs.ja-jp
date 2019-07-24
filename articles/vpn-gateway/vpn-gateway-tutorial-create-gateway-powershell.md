---
title: PowerShell を使用した Azure VPN ゲートウェイの作成と管理 | Microsoft Docs
description: チュートリアル - Azure PowerShell モジュールを使用して VPN ゲートウェイを作成および管理する
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: tutorial
ms.date: 02/11/2019
ms.author: yushwang
ms.custom: mvc
ms.openlocfilehash: 790a8b74f437fe8fd7b8660c2ac9d208328b487f
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58445215"
---
# <a name="tutorial-create-and-manage-a-vpn-gateway-using-powershell"></a>チュートリアル:PowerShell を使用した VPN ゲートウェイの作成と管理

Azure VPN ゲートウェイは、お客様のオンプレミスと Azure 間のクロスプレミス接続を提供します。 このチュートリアルでは、VPN ゲートウェイの作成と管理など、Azure VPN ゲートウェイのデプロイの基本事項について説明します。 学習内容は次のとおりです。

> [!div class="checklist"]
> * VPN ゲートウェイの作成
> * パブリック IP アドレスの表示
> * VPN ゲートウェイのサイズ変更
> * VPN ゲートウェイのリセット

次の図は、このチュートリアルの一環として作成する仮想ネットワークと VPN ゲートウェイを示しています。

![VNet と VPN ゲートウェイ](./media/vpn-gateway-tutorial-create-gateway-powershell/vnet1-gateway.png)

### <a name="azure-cloud-shell-and-azure-powershell"></a>Azure Cloud Shell および Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [working with cloud shell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

## <a name="common-network-parameter-values"></a>一般的なネットワーク パラメーター値

お使いの環境とネットワークのセットアップに基づいて次の値を変更した後、コピーして貼り付けて、このチュートリアルの変数を設定します。 Cloud Shell セッションがタイムアウトになるか、別の PowerShell ウィンドウを使用する必要がある場合は、これらの変数をコピーし、新しいセッションに貼り付け、チュートリアルを続行します。

```azurepowershell-interactive
$RG1         = "TestRG1"
$VNet1       = "VNet1"
$Location1   = "East US"
$FESubnet1   = "FrontEnd"
$BESubnet1   = "Backend"
$GwSubnet1   = "GatewaySubnet"
$VNet1Prefix = "10.1.0.0/16"
$FEPrefix1   = "10.1.0.0/24"
$BEPrefix1   = "10.1.1.0/24"
$GwPrefix1   = "10.1.255.0/27"
$VNet1ASN    = 65010
$DNS1        = "8.8.8.8"
$Gw1         = "VNet1GW"
$GwIP1       = "VNet1GWIP"
$GwIPConf1   = "gwipconf1"
```

## <a name="create-a-resource-group"></a>リソース グループの作成

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) コマンドでリソース グループを作成します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 リソース グループを最初に作成する必要があります。 次の例では、*TestRG1* という名前のリソース グループが "*米国東部*" リージョンに作成されます。

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName $RG1 -Location $Location1
```

## <a name="create-a-virtual-network"></a>仮想ネットワークの作成

Azure VPN ゲートウェイは、仮想ネットワークのクロスプレミス接続と P2S VPN サーバー機能を提供します。 VPN ゲートウェイを既存の仮想ネットワークに追加するか、新しい仮想ネットワークとゲートウェイを作成します。 次の例では、[New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) と [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) を使用して、Frontend、Backend、GatewaySubnet という 3 つのサブネットを持つ新しい仮想ネットワークを作成します。

```azurepowershell-interactive
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubnet1 -AddressPrefix $FEPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubnet1 -AddressPrefix $BEPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubnet1 -AddressPrefix $GwPrefix1
$vnet   = New-AzVirtualNetwork `
            -Name $VNet1 `
            -ResourceGroupName $RG1 `
            -Location $Location1 `
            -AddressPrefix $VNet1Prefix `
            -Subnet $fesub1,$besub1,$gwsub1
```

## <a name="request-a-public-ip-address-for-the-vpn-gateway"></a>VPN ゲートウェイのパブリック IP アドレスを要求する

Azure VPN ゲートウェイは、オンプレミスの VPN デバイスとインターネット経由で通信して IKE (インターネット キー交換) ネゴシエーションを実行し、IPsec トンネルを確立します。 次の例に示すように、[New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) と [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) を使用して、パブリック IP アドレスを作成して VPN ゲートウェイに割り当てます。

> [!IMPORTANT]
> 現時点では、ゲートウェイに使用できるのは、動的パブリック IP アドレスだけです。 静的 IP アドレスは、Azure VPN ゲートウェイではサポートされていません。

```azurepowershell-interactive
$gwpip    = New-AzPublicIpAddress -Name $GwIP1 -ResourceGroupName $RG1 `
              -Location $Location1 -AllocationMethod Dynamic
$subnet   = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' `
              -VirtualNetwork $vnet
$gwipconf = New-AzVirtualNetworkGatewayIpConfig -Name $GwIPConf1 `
              -Subnet $subnet -PublicIpAddress $gwpip
```

## <a name="create-a-vpn-gateway"></a>VPN ゲートウェイの作成

VPN ゲートウェイの作成には 45 分以上かかる場合があります。 ゲートウェイの作成が完了したら、仮想ネットワークと別の VNet 間の接続を作成できます。 または、仮想ネットワークとオンプレミスの場所の間の接続を作成します。 [New-AzVirtualNetworkGateway](/powershell/module/az.network/New-azVirtualNetworkGateway) コマンドレットを使用して VPN ゲートウェイを作成します。

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name $Gw1 -ResourceGroupName $RG1 `
  -Location $Location1 -IpConfigurations $gwipconf -GatewayType Vpn `
  -VpnType RouteBased -GatewaySku VpnGw1
```

主なパラメーター値は次のとおりです。
* GatewayType: サイト間接続と VNet 対 VNet 接続には、**Vpn** を使用します。
* VpnType: より広範な VPN デバイスやより多くのルーティング機能と対話するには、**RouteBased** を使用します。
* GatewaySku: 既定値は **VpnGw1** です。より高いスループットやより多くの接続が必要な場合は、VpnGw2 または VpnGw3 に変更してください。 詳細については、「[ゲートウェイの SKU](vpn-gateway-about-vpn-gateway-settings.md#gwsku)」を参照してください。

[使ってみる] を使用している場合、セッションはタイムアウトする可能性があります。問題ありません。 ゲートウェイは引き続き作成されます。

ゲートウェイの作成が完了したら、仮想ネットワークと別の VNet 間の接続を作成するか、仮想ネットワークとオンプレミスの場所の間の接続を作成できます。 また、クライアント コンピューターから VNet への P2S 接続を構成することもできます。

## <a name="view-the-gateway-public-ip-address"></a>ゲートウェイのパブリック IP アドレスを表示する

パブリック IP アドレスの名前がわかっている場合は、[Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress) を使用して、ゲートウェイに割り当てられたパブリック IP アドレスを表示します。

セッションがタイムアウトした場合、一般的なネットワーク パラメーターをこのチュートリアルの先頭から新しいセッションにコピーして、続行します。

```azurepowershell-interactive
$myGwIp = Get-AzPublicIpAddress -Name $GwIP1 -ResourceGroup $RG1
$myGwIp.IpAddress
```

## <a name="resize-a-gateway"></a>ゲートウェイのサイズを変更する

ゲートウェイの作成後、VPN ゲートウェイ SKU を変更できます。 ゲートウェイ SKU ごとに、サポートされる仕様 (スループット、接続数など) が異なります。次の例では、[Resize-AzVirtualNetworkGateway](/powershell/module/az.network/Resize-azVirtualNetworkGateway) を使用して、ゲートウェイのサイズを VpnGw1 から VpnGw2 に変更します。 詳細については、「[ゲートウェイの SKU](vpn-gateway-about-vpn-gateway-settings.md#gwsku)」を参照してください。

```azurepowershell-interactive
$gateway = Get-AzVirtualNetworkGateway -Name $Gw1 -ResourceGroup $RG1
Resize-AzVirtualNetworkGateway -GatewaySku VpnGw2 -VirtualNetworkGateway $gateway
```

VPN ゲートウェイのサイズ変更も、約 30 から 45 分かかりますが、この操作によって、既存の接続と構成が中断されたり削除されたりすることは**ありません**。

## <a name="reset-a-gateway"></a>ゲートウェイをリセットする

トラブルシューティングの手順の一環として、Azure VPN ゲートウェイをリセットして、VPN ゲートウェイに IPsec/IKE トンネル構成を強制的に再開させることができます。 [Reset-AzVirtualNetworkGateway](/powershell/module/az.network/Reset-azVirtualNetworkGateway) を使用してゲートウェイをリセットします。

```azurepowershell-interactive
$gateway = Get-AzVirtualNetworkGateway -Name $Gw1 -ResourceGroup $RG1
Reset-AzVirtualNetworkGateway -VirtualNetworkGateway $gateway
```

詳細については、「[Reset a VPN gateway (VPN ゲートウェイのリセット)](vpn-gateway-resetgw-classic.md)」をご覧ください。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[次のチュートリアル](vpn-gateway-tutorial-vpnconnection-powershell.md)に進む場合、これらのリソースは前提条件であるため、維持する必要があります。

ただし、このゲートウェイがプロトタイプ、テスト、または概念実証のデプロイの一部である場合は、[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) コマンドを使用して、リソース グループ、VPN ゲートウェイ、およびすべての関連リソースを削除できます。

```azurepowershell-interactive
Remove-AzResourceGroup -Name $RG1
```

## <a name="next-steps"></a>次の手順

このチュートリアルでは、次の方法など、VPN ゲートウェイの基本的な作成と管理について説明しました。

> [!div class="checklist"]
> * VPN ゲートウェイの作成
> * パブリック IP アドレスの表示
> * VPN ゲートウェイのサイズ変更
> * VPN ゲートウェイのリセット

S2S 接続、VNet 間接続、P2S 接続については、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> * [S2S 接続の作成](vpn-gateway-tutorial-vpnconnection-powershell.md)
> * [VNet 間接続の作成](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [P2S 接続の作成](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
