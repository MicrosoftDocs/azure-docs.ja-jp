---
title: "Azure VPN ゲートウェイに接続するためのサード パーティの VPN デバイス構成について | Microsoft Docs"
description: "この記事では、サード パーティの VPN デバイスを Azure VPN ゲートウェイに接続するための構成の概要について説明します。"
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: 
tags: 
ms.assetid: a8bfc955-de49-4172-95ac-5257e262d7ea
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/20/2017
ms.author: yushwang
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 72dab85bb882b05d72cef26bef70437695b70416
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---
# <a name="overview-of-3rd-party-vpn-device-configurations"></a>サード パーティの VPN デバイス構成の概要
この記事では、オンプレミスの VPN デバイスを Azure VPN ゲートウェイに接続するための構成の概要について説明します。 サンプルの Azure 仮想ネットワークと VPN ゲートウェイのセットアップを使って、同じパラメーターで異なるオンプレミスの VPN デバイスに接続します。

## <a name="device-requirements"></a>デバイスの要件
Azure VPN ゲートウェイは、標準の IPsec/IKE プロトコル スイートを S2S VPN トンネルに使います。 Azure VPN ゲートウェイで使用する IPsec/IKE プロトコル パラメーターと既定の暗号アルゴリズムについて詳しくは、「[VPN デバイスについて](vpn-gateway-about-vpn-devices.md)」をご覧ください。 [暗号化の要件](vpn-gateway-about-compliance-crypto.md)に関する記事の説明に従って、特定の接続について、暗号アルゴリズムとキーの強度の正確な組み合わせを指定することもできます。

## <a name ="singletunnel"></a>単一の VPN トンネル
1 番目のトポロジは、Azure VPN ゲートウェイとオンプレミスの VPN デバイスの間の単一の S2S VPN トンネルで構成されます。 VPN トンネル間に BGP を構成することもできます。

![単一のトンネル](./media/vpn-gateway-3rdparty-device-config-overview/singletunnel.png)

詳しい手順については、「[Azure Portal でサイト間接続を作成する](vpn-gateway-howto-site-to-site-resource-manager-portal.md)」をご覧ください。 以降のセクションでは、パラメーターの一覧を示し、作業を始めるときに参考になる PowerShell スクリプトのサンプルを提供します。

### <a name="network-and-vpn-gateway-information"></a>ネットワークと VPN ゲートウェイの情報
このセクションでは、上の例に対するパラメーターの一覧を示します。

| **パラメーター**                | **値**                    |
| ---                          | ---                          |
| VNet アドレス プレフィックス        | 10.11.0.0/16<br>10.12.0.0/16 |
| Azure VPN Gateway IP         | Azure VPN Gateway IP         |
| オンプレミス アドレス プレフィックス | 10.51.0.0/16<br>10.52.0.0/16 |
| オンプレミス VPN デバイス IP    | オンプレミス VPN デバイス IP    |
| *VNet BGP ASN                | 65010                        |
| *Azure BGP ピア IP           | 10.12.255.30                 |
| *オンプレミス BGP ASN         | 65050                        |
| *オンプレミス BGP ピア IP     | 10.52.255.254                |

* (*) BGP 専用のオプション パラメーター

### <a name="sample-powershell-script"></a>PowerShell スクリプトのサンプル
詳しい説明については、「[PowerShell を使用してサイト間 VPN 接続を備えた VNet を作成する](vpn-gateway-create-site-to-site-rm-powershell.md)」をご覧ください。 このセクションでは、作業を始めるときに役立つサンプル スクリプトを提供します。

```powershell
# Declare your variables

$Sub1          = "Replace_With_Your_Subcription_Name"
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

Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $Sub1
New-AzureRmResourceGroup -Name $RG1 -Location $Location1

# Create virtual network

$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1 $besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

# Create VPN gateway

$gwpip1    = New-AzureRmPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$vnet1     = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1   = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 -Subnet $subnet1 -PublicIpAddress $gwpip1

New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet1ASN

# Create local network gateway

New-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix51,$LNGPrefix52 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5

# Create the S2S VPN connection

$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $False
```

### <a name ="policybased"></a> [オプション] "UsePolicyBasedTrafficSelectors" でカスタム IPsec/IKE ポリシーを使用する
VPN デバイスが "任意の環境間" のトラフィック セレクター (ルート ベース/VTI ベースの構成) をサポートしていない場合は、[こちらの記事](vpn-gateway-connect-multiple-policybased-rm-ps.md)で説明されているように、カスタム IPsec/IKE ポリシーを作成して "UsePolicyBasedTrafficSelectors" オプションを構成する必要があります。

> [!IMPORTANT]
> 接続で "UsePolicyBasedTrafficSelectors" オプションを有効にするために、IPsec/IKE ポリシーを作成する必要があります。

次のサンプル スクリプトは、次のアルゴリズムとパラメーターを使用して IPsec/IKE ポリシーを作成します。
* IKEv2: AES256、SHA384、DHGroup24
* IPsec: AES256、SHA1、PFS24、SA の有効期間 7,200 秒および 20,480,000 KB (20 GB)

その後、ポリシーを適用し、接続で "UesPolicyBasedTrafficSelectors" を有効にします。

```powershell
$ipsecpolicy5 = New-AzureRmIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA1 -PfsGroup PFS24 -SALifeTimeSeconds 7200 -SADataSizeKilobytes 20480000

$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $False -IpsecPolicies $ipsecpolicy5 -UsePolicyBasedTrafficSelectors $True
```

### <a name ="bgp"></a>[オプション] S2S VPN 接続で BGP を使用する
必要に応じて、BGP を接続で使うことができます。 「[PowerShell を使用して Azure VPN Gateway で BGP を構成する方法](vpn-gateway-bgp-resource-manager-ps.md)」をご覧ください。 2 つの違いがあります。

オンプレミスのアドレス プレフィックスには、1 つのホスト アドレス (オンプレミスの BGP ピア IP アドレス) を指定できます。

```powershell
New-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix50 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5
```

接続を作成するときは、"-EnableBGP" を $True に設定する必要があります。

```powershell
New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
```

## <a name="next-steps"></a>次のステップ
アクティブ/アクティブのクロスプレミス接続と VNet 間接続を構成する手順については、[クロスプレミス接続と VNet 間接続のアクティブ/アクティブ VPN Gateway の構成](vpn-gateway-activeactive-rm-powershell.md)に関するページを参照してください。


