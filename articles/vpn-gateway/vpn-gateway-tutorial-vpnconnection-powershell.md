---
title: PowerShell を使用して Azure S2S VPN 接続を作成および管理する | Microsoft Docs
description: チュートリアル - Azure PowerShell モジュールを使用して S2S VPN 接続を作成および管理する
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: tutorial
ms.date: 02/11/2019
ms.author: yushwang
ms.custom: mvc
ms.openlocfilehash: cac68506803cda2c4e537feac84da2a82bc128bd
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58444286"
---
# <a name="tutorial-create-and-manage-s2s-vpn-connections-using-powershell"></a>チュートリアル:PowerShell を使用して S2S VPN 接続を作成および管理する

Azure S2S VPN 接続は、顧客構内と Azure との間の安全なクロスプレミス接続を提供します。 このチュートリアルでは、S2S VPN 接続の作成や管理などの IPsec S2S VPN 接続のライフ サイクルについて説明します。 学習内容は次のとおりです。

> [!div class="checklist"]
> * S2S VPN 接続を作成する
> * 接続プロパティを更新する: 事前共有キー、BGP、IPsec/IKE ポリシー
> * VPN 接続をさらに追加する
> * VPN 接続を削除する

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

次の図に、このチュートリアルのトポロジを示します。

![サイト間 VPN 接続の図](./media/vpn-gateway-tutorial-vpnconnection-powershell/site-to-site-diagram.png)

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

## <a name="requirements"></a>必要条件

最初のチュートリアル ([Azure PowerShell を使用した VPN ゲートウェイの作成](vpn-gateway-tutorial-create-gateway-powershell.md)に関するページ) を実行して、次のリソースを作成します。

1. リソース グループ (TestRG1)、仮想ネットワーク (VNet1)、および GatewaySubnet
2. VPN ゲートウェイ (VNet1GW)

仮想ネットワークのパラメーター値は次のとおりです。 お使いのオンプレミス ネットワークを表すローカル ネットワーク ゲートウェイの追加の値に注意してください。 お使いの環境とネットワークのセットアップに基づいて次の値を変更した後、コピーして貼り付けて、このチュートリアルの変数を設定します。 Cloud Shell セッションがタイムアウトになるか、別の PowerShell ウィンドウを使用する必要がある場合は、これらの変数をコピーし、新しいセッションに貼り付け、チュートリアルを続行します。

>[!NOTE]
> これを使用して接続を行う場合は、必ず、お使いのオンプレミス ネットワークに合わせて値を変更してください。 これらの手順をチュートリアルとして実行するだけの場合は、変更する必要はありませんが、接続は機能しません。
>

```azurepowershell-interactive
# Virtual network
$RG1         = "TestRG1"
$VNet1       = "VNet1"
$Location1   = "East US"
$VNet1Prefix = "10.1.0.0/16"
$VNet1ASN    = 65010
$Gw1         = "VNet1GW"

# On-premises network - LNGIP1 is the VPN device public IP address
$LNG1        = "VPNsite1"
$LNGprefix1  = "10.101.0.0/24"
$LNGprefix2  = "10.101.1.0/24"
$LNGIP1      = "5.4.3.2"

# Optional - on-premises BGP properties
$LNGASN1     = 65011
$BGPPeerIP1  = "10.101.1.254"

# Connection
$Connection1 = "VNet1ToSite1"
```

S2S VPN 接続を作成するためのワークフローは単純です。

1. オンプレミス ネットワークを表すローカル ネットワーク ゲートウェイを作成する
2. Azure VPN ゲートウェイとローカル ネットワーク ゲートウェイの間に接続を作成する

## <a name="create-a-local-network-gateway"></a>ローカル ネットワーク ゲートウェイの作成

ローカル ネットワーク ゲートウェイは、オンプレミスのネットワークを表します。 オンプレミス ネットワークのプロパティはローカル ネットワーク ゲートウェイに指定できます。これには次の項目が含まれます。

* VPN デバイスのパブリック IP アドレス
* オンプレミスのアドレス空間
* (オプション) BGP 属性 (BGP ピア IP アドレスおよび AS 番号)

[New-AzLocalNetworkGateway](https://docs.microsoft.com/powershell/module/az.network/new-azlocalnetworkgateway) コマンドを使用して、ローカル ネットワーク ゲートウェイを作成します。

```azurepowershell-interactive
New-AzLocalNetworkGateway -Name $LNG1 -ResourceGroupName $RG1 `
  -Location 'East US' -GatewayIpAddress $LNGIP1 -AddressPrefix $LNGprefix1,$LNGprefix2
```

## <a name="create-a-s2s-vpn-connection"></a>S2S VPN 接続を作成する

次に、[New-AzVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) を使用して、仮想ネットワーク ゲートウェイとお使いの VPN デバイスの間にサイト間 VPN 接続を作成します。 サイト間 VPN の "-ConnectionType" が *IPsec* であることに注意してください。

```azurepowershell-interactive
$vng1 = Get-AzVirtualNetworkGateway -Name $GW1  -ResourceGroupName $RG1
$lng1 = Get-AzLocalNetworkGateway   -Name $LNG1 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection1 -ResourceGroupName $RG1 `
  -Location $Location1 -VirtualNetworkGateway1 $vng1 -LocalNetworkGateway2 $lng1 `
  -ConnectionType IPsec -SharedKey "Azure@!b2C3"
```

BGP を使用している場合は、オプションの "**-EnableBGP $True**" プロパティを追加して、接続の BGP を有効にします。 この機能は既定で無効になっています。

## <a name="update-the-vpn-connection-pre-shared-key-bgp-and-ipsecike-policy"></a>VPN 接続の事前共有キー、BGP、および IPsec/IKE ポリシーを更新する

### <a name="view-and-update-your-pre-shared-key"></a>事前共有キーを表示および更新する

Azure S2S VPN 接続では、事前共有キー (シークレット) を使用して、オンプレミス VPN デバイスと Azure VPN ゲートウェイとの間の認証を行います。 [Get-AzVirtualNetworkGatewayConnectionSharedKey](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetworkgatewayconnectionsharedkey) および [Set-AzVirtualNetworkGatewayConnectionSharedKey](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetworkgatewayconnectionsharedkey) を使用して、接続の事前共有キーを表示および更新できます。

> [!IMPORTANT]
> 事前共有キーは、長さが 128 文字以下の**印刷可能 ASCII 文字**から構成される文字列です。

このコマンドを実行すると、接続の事前共有キーが表示されます。

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayConnectionSharedKey `
  -Name $Connection1 -ResourceGroupName $RG1
```

上記の例の出力は、"**Azure\@!b2C3**" になります。 事前共有キーの値を "**Azure\@!_b2=C3**" に変更するには、次のコマンドを使用します。

```azurepowershell-interactive
Set-AzVirtualNetworkGatewayConnectionSharedKey `
  -Name $Connection1 -ResourceGroupName $RG1 `
  -Value "Azure@!_b2=C3"
```

### <a name="enable-bgp-on-vpn-connection"></a>VPN 接続で BGP を有効にする

Azure VPN ゲートウェイは、BGP 動的ルーティング プロトコルをサポートします。 オンプレミス ネットワークおよびデバイスで BGP を使用しているかどうかに応じて、個々の接続で BGP を有効にすることができます。 接続で BGP を有効にする前に、次の BGP プロパティを指定します。

* Azure VPN ASN (自律システム番号)
* オンプレミス ローカル ネットワーク ゲートウェイ ASN
* オンプレミス ローカル ネットワーク ゲートウェイ BGP ピア IP アドレス

BGP のプロパティを構成していない場合は、次のコマンドを使用して、お使いの VPN ゲートウェイとローカル ネットワーク ゲートウェイにこれらのプロパティを追加します。[Set-AzVirtualNetworkGateway](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetworkgateway) および [Set-AzLocalNetworkGateway](https://docs.microsoft.com/powershell/module/az.network/set-azlocalnetworkgateway)。

次の例を使用して、BGP のプロパティを構成します。

```azurepowershell-interactive
$vng1 = Get-AzVirtualNetworkGateway -Name $GW1  -ResourceGroupName $RG1
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $vng1 -Asn $VNet1ASN

$lng1 = Get-AzLocalNetworkGateway   -Name $LNG1 -ResourceGroupName $RG1
Set-AzLocalNetworkGateway -LocalNetworkGateway $lng1 `
  -Asn $LNGASN1 -BgpPeeringAddress $BGPPeerIP1
```

[Set-AzVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetworkgatewayconnection) を使用して BGP を有効にします。

```azurepowershell-interactive
$connection = Get-AzVirtualNetworkGatewayConnection `
  -Name $Connection1 -ResourceGroupName $RG1

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection `
  -EnableBGP $True
```

BGP を無効にするには、"-EnableBGP" プロパティの値を **$False** に変更します。 Azure VPN ゲートウェイの BGP の詳細については、[Azure VPN ゲートウェイの BGP](vpn-gateway-bgp-resource-manager-ps.md)に関するページを参照してください。

### <a name="apply-a-custom-ipsecike-policy-on-the-connection"></a>接続にカスタム IPsec/IKE ポリシーを適用する

オプションの IPsec/IKE ポリシーを適用して、[既定のプロポーザル](vpn-gateway-about-vpn-devices.md#ipsec)を使用する代わりに、接続における IPsec/IKE 暗号アルゴリズムとキー強度の正確な組み合わせを指定できます。 次のサンプル スクリプトは、次のアルゴリズムとパラメーターを使用して別の IPsec/IKE ポリシーを作成します。

* IKEv2:AES256、SHA256、DHGroup14
* IPsec:AES128、SHA1、PFS14、SA の有効期間 14,400 秒、および 102,400,000 KB

```azurepowershell-interactive
$connection = Get-AzVirtualNetworkGatewayConnection -Name $Connection1 `
                -ResourceGroupName $RG1
$newpolicy  = New-AzIpsecPolicy `
                -IkeEncryption AES256 -IkeIntegrity SHA256 -DhGroup DHGroup14 `
                -IpsecEncryption AES128 -IpsecIntegrity SHA1 -PfsGroup PFS2048 `
                -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection `
  -IpsecPolicies $newpolicy
```

アルゴリズムと手順の完全な一覧については、[S2S または VNet 間接続の IPsec/IKE ポリシー](vpn-gateway-ipsecikepolicy-rm-powershell.md)に関するページを参照してください。

## <a name="add-another-s2s-vpn-connection"></a>別の S2S VPN 接続を追加する

追加の S2S VPN 接続を同じ VPN ゲートウェイに追加し、別のローカル ネットワーク ゲートウェイを作成し、新しいローカル ネットワーク ゲートウェイと VPN ゲートウェイとの間に新しい接続を作成します。 次の例を使用して、必ず、お使いのネットワーク構成に合わせて変数を変更してください。

```azurepowershell-interactive
# On-premises network - LNGIP2 is the VPN device public IP address
$LNG2        = "VPNsite2"
$Location2   = "West US"
$LNGprefix21 = "10.102.0.0/24"
$LNGprefix22 = "10.102.1.0/24"
$LNGIP2      = "4.3.2.1"
$Connection2 = "VNet1ToSite2"

New-AzLocalNetworkGateway -Name $LNG2 -ResourceGroupName $RG1 `
  -Location $Location2 -GatewayIpAddress $LNGIP2 -AddressPrefix $LNGprefix21,$LNGprefix22

$vng1 = Get-AzVirtualNetworkGateway -Name $GW1  -ResourceGroupName $RG1
$lng2 = Get-AzLocalNetworkGateway   -Name $LNG2 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection2 -ResourceGroupName $RG1 `
  -Location $Location1 -VirtualNetworkGateway1 $vng1 -LocalNetworkGateway2 $lng2 `
  -ConnectionType IPsec -SharedKey "AzureA1%b2_C3+"
```

これで、Azure VPN ゲートウェイへの 2 つの S2S VPN 接続が作成されました。

![マルチサイトの VPN 接続](./media/vpn-gateway-tutorial-vpnconnection-powershell/multisite-connections.png)

## <a name="delete-a-s2s-vpn-connection"></a>S2S VPN 接続を削除する

[Remove-AzVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/az.network/remove-azvirtualnetworkgatewayconnection) を使用して S2S VPN 接続を削除します。

```azurepowershell-interactive
Remove-AzVirtualNetworkGatewayConnection -Name $Connection2 -ResourceGroupName $RG1
```

ローカル ネットワーク ゲートウェイが不要になった場合はこれを削除します。 ローカル ネットワーク ゲートウェイに他の接続が関連付けられている場合、そのローカル ネットワーク ゲートウェイを削除することはできません。

```azurepowershell-interactive
Remove-AzVirtualNetworkGatewayConnection -Name $LNG2 -ResourceGroupName $RG1
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

この構成がプロトタイプ、テスト、または概念実証のデプロイの一部である場合は、[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) コマンドを使用して、リソース グループ、VPN ゲートウェイ、およびすべての関連リソースを削除できます。

```azurepowershell-interactive
Remove-AzResourceGroup -Name $RG1
```

## <a name="next-steps"></a>次の手順

このチュートリアルでは、次のような S2S VPN 接続の作成と管理について説明しました。

> [!div class="checklist"]
> * S2S VPN 接続を作成する
> * 接続プロパティを更新する: 事前共有キー、BGP、IPsec/IKE ポリシー
> * VPN 接続をさらに追加する
> * VPN 接続を削除する

S2S 接続、VNet 間接続、および P2S 接続については、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> * [VNet 間接続の作成](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [P2S 接続の作成](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
