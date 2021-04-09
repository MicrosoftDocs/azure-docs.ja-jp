---
title: Azure VPN ゲートウェイにパートナー VPN デバイスを接続するための構成
description: この記事では、Azure VPN ゲートウェイにパートナー VPN デバイスを接続するための構成の概要について説明します。
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: yushwang
ms.openlocfilehash: 484dad65310efaa60e8744b2f122b5e44ae13565
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98880154"
---
# <a name="overview-of-partner-vpn-device-configurations"></a>パートナー VPN デバイス構成の概要
この記事では、オンプレミスの VPN デバイスを Azure VPN ゲートウェイに接続するための構成の概要について説明します。 サンプルの Azure 仮想ネットワークと VPN ゲートウェイの設定を使って、同じパラメーターで異なるオンプレミス VPN デバイス構成に接続する方法を紹介します。



## <a name="device-requirements"></a>デバイスの要件
Azure VPN ゲートウェイは、標準の IPsec/IKE プロトコル スイートをサイト間 (S2S) VPN トンネルに使います。 Azure VPN ゲートウェイで使用される IPsec/IKE パラメーターと暗号アルゴリズムの一覧については、[VPN デバイス](vpn-gateway-about-vpn-devices.md)に関するページを参照してください。 [暗号化の要件](vpn-gateway-about-compliance-crypto.md)に関する記事の説明に従って、特定の接続を対象に、暗号アルゴリズムとキーの強度を具体的に指定することもできます。

## <a name="single-vpn-tunnel"></a><a name ="singletunnel"></a>単一の VPN トンネル
サンプルの 1 つ目の構成は、Azure VPN ゲートウェイとオンプレミスの VPN デバイスとの間にある単一の S2S VPN トンネルから成ります。 必要に応じて、[VPN トンネル上にボーダー ゲートウェイ プロトコル (BGP)](#bgp) を構成することもできます。

![単一の S2S VPN トンネルの図](./media/vpn-gateway-3rdparty-device-config-overview/singletunnel.png)

単一の VPN トンネルを設定する具体的な手順については、[サイト対サイト接続の構成](./tutorial-site-to-site-portal.md)に関するページを参照してください。 以降のセクションでは、サンプル構成の接続パラメーターについて詳しく説明すると共に、基本的な PowerShell スクリプトを紹介します。

### <a name="connection-parameters"></a>接続パラメーター
このセクションでは、前のセクションで説明した例に使用されている一連のパラメーターを列挙しています。

| **パラメーター**                | **Value**                    |
| ---                          | ---                          |
| 仮想ネットワーク アドレス プレフィックス        | 10.11.0.0/16<br>10.12.0.0/16 |
| Azure VPN Gateway IP         | Azure VPN Gateway IP         |
| オンプレミス アドレス プレフィックス | 10.51.0.0/16<br>10.52.0.0/16 |
| オンプレミス VPN デバイス IP    | オンプレミス VPN デバイス IP    |
| * 仮想ネットワーク BGP ASN                | 65010                        |
| * Azure BGP ピア IP           | 10.12.255.30                 |
| * オンプレミス BGP ASN         | 65050                        |
| * オンプレミス BGP ピア IP     | 10.52.255.254                |

\* BGP 専用のオプション パラメーター。

### <a name="sample-powershell-script"></a>PowerShell スクリプトのサンプル
このセクションでは、作業を始めるときに役立つサンプル スクリプトを提供します。 詳しい手順については、[PowerShell を使用して S2S VPN 接続を作成する方法](vpn-gateway-create-site-to-site-rm-powershell.md)に関するページを参照してください。

```powershell
# Declare your variables

$Sub1          = "Replace_With_Your_Subscription_Name"
$RG1           = "TestRG1"
$Location1     = "East US 2"
$VNetName1     = "TestVNet1"
$FESubName1    = "FrontEnd"
$BESubName1    = "Backend"
$GWSubName1    = "GatewaySubnet"
$VNetPrefix11  = "10.11.0.0/16"
$VNetPrefix12  = "10.12.0.0/16"
$FESubPrefix1  = "10.11.0.0/24"
$BESubPrefix1  = "10.12.0.0/24"
$GWSubPrefix1  = "10.12.255.0/27"
$VNet1ASN      = 65010
$DNS1          = "8.8.8.8"
$GWName1       = "VNet1GW"
$GWIPName1     = "VNet1GWIP"
$GWIPconfName1 = "gwipconf1"
$Connection15  = "VNet1toSite5"
$LNGName5      = "Site5"
$LNGPrefix50   = "10.52.255.254/32"
$LNGPrefix51   = "10.51.0.0/16"
$LNGPrefix52   = "10.52.0.0/16"
$LNGIP5        = "Your_VPN_Device_IP"
$LNGASN5       = 65050
$BGPPeerIP5    = "10.52.255.254"

# Connect to your subscription and create a new resource group

Connect-AzAccount
Select-AzSubscription -SubscriptionName $Sub1
New-AzResourceGroup -Name $RG1 -Location $Location1

# Create virtual network

$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1 $besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

# Create VPN gateway

$gwpip1    = New-AzPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$vnet1     = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1   = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gwipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 -Subnet $subnet1 -PublicIpAddress $gwpip1

New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet1ASN

# Create local network gateway

New-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix51,$LNGPrefix52 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5

# Create the S2S VPN connection

$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $False
```

### <a name="optional-use-custom-ipsecike-policy-with-usepolicybasedtrafficselectors"></a><a name ="policybased"></a> (オプション) UsePolicyBasedTrafficSelectors でカスタム IPsec/IKE ポリシーを使用する
VPN デバイスが任意の環境間のトラフィック セレクター (ルートベース/VTI ベースの構成など) をサポートしていない場合は、[UsePolicyBasedTrafficSelectors](vpn-gateway-connect-multiple-policybased-rm-ps.md) オプションを使ってカスタム IPsec/IKE ポリシーを作成します。

> [!IMPORTANT]
> 接続で **UsePolicyBasedTrafficSelectors** オプションを有効にするためには、IPsec/IKE ポリシーを作成する必要があります。


このサンプル スクリプトでは、次のアルゴリズムとパラメーターを使用して IPsec/IKE ポリシーが作成されます。
* IKEv2:AES256、SHA384、DHGroup24
* IPsec: AES256、SHA1、PFS24、SA の有効期間 7,200 秒および 20,480,000 KB (20 GB)

このスクリプトでは、接続に IPsec/IKE ポリシーを適用して **UsePolicyBasedTrafficSelectors** オプションを有効にします。

```powershell
$ipsecpolicy5 = New-AzIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA1 -PfsGroup PFS24 -SALifeTimeSeconds 7200 -SADataSizeKilobytes 20480000

$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $False -IpsecPolicies $ipsecpolicy5 -UsePolicyBasedTrafficSelectors $True
```

### <a name="optional-use-bgp-on-s2s-vpn-connection"></a><a name ="bgp"></a>(オプション) S2S VPN 接続で BGP を使用する
S2S VPN 接続を作成するとき、必要に応じて [VPN ゲートウェイに BGP](vpn-gateway-bgp-resource-manager-ps.md) を使用することができます。 このアプローチには 2 つの違いがあります。

* オンプレミスのアドレス プレフィックスには、1 つのホスト アドレスを指定できます。 オンプレミスの BGP ピア IP アドレスは次のように指定します。

    ```powershell
    New-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix50 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5
    ```

* 接続を作成するときに、**-EnableBGP** オプションを $True に設定する必要があります。

    ```powershell
    New-AzVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
    ```

## <a name="next-steps"></a>次のステップ
アクティブ/アクティブ VPN ゲートウェイを設定する具体的な手順については、[クロスプレミス接続と VNet 間接続にアクティブ/アクティブ VPN ゲートウェイを構成する方法](vpn-gateway-activeactive-rm-powershell.md)に関するページを参照してください。