---
title: VPN Gateway のアクティブ/アクティブ S2S VPN 接続を構成するAzure Resource Manager:PowerShell | Microsoft Docs
description: この記事では、Azure Resource Manager と PowerShell を使用して Azure VPN ゲートウェイでアクティブ/アクティブ接続を構成する方法を説明します。
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 07/24/2018
ms.author: yushwang
ms.reviewer: cherylmc
ms.openlocfilehash: 6d973d81e0de407893beb5c5808962562f091d4c
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "67871839"
---
# <a name="configure-active-active-s2s-vpn-connections-with-azure-vpn-gateways"></a>Azure VPN ゲートウェイで、アクティブ/アクティブ S2S VPN 接続を構成する

この記事では、Resource Manager デプロイ モデルと PowerShell を使用して、アクティブ/アクティブのクロスプレミス接続と VNet 間接続を作成にする手順について説明します。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="about-highly-available-cross-premises-connections"></a>高可用性のクロスプレミス接続について
クロスプレミスと VNet 間接続で高可用性を実現するには、複数の VPN ゲートウェイをデプロイし、ネットワークと Azure 間に複数の並列接続を確立する必要があります。 接続オプションとトロポジの概要については、「[高可用性のクロスプレミス接続および VNet 間接続](vpn-gateway-highlyavailable.md)」をご覧ください。

この記事では、アクティブ/アクティブの VPN 接続と、2 つの仮想ネットワーク間のアクティブ/アクティブ接続を設定する詳細な手順を説明します。

* [パート 1 - Azure VPN ゲートウェイをアクティブ/アクティブ モードで作成、構成する](#aagateway)
* [パート 2 - アクティブ/アクティブのクロスプレミス接続を確立する](#aacrossprem)
* [パート 3 - アクティブ/アクティブの VNet 間接続を確立する](#aav2v)

既に VPN Gateway がある場合は、次のことを実現できます。
* [既存の VPN Gateway をアクティブ/スタンバイからアクティブ/アクティブ (またはその逆) に更新する](#aaupdate)

これらを組み合わせると、ニーズに合わせて、より複雑で可用性の高いネットワーク トポロジを構築できます。

> [!IMPORTANT]
> アクティブ/アクティブ モードは、次の SKU でのみ機能します。 
>   * VpnGw1、VpnGw2、VpnGw3
>   * HighPerformance (古いレガシ SKU 向け)

## <a name ="aagateway"></a>パート 1 - アクティブ/アクティブの VPN ゲートウェイを作成、構成する
次の手順では、Azure VPN ゲートウェイをアクティブ/アクティブ モードで構成します。 アクティブ/アクティブ ゲートウェイとアクティブ/スタンバイ ゲートウェイの主な違い:

* 2 つのパブリック IP アドレスを使用して 2 つのゲートウェイ IP 構成を作成する必要がある
* EnableActiveActiveFeature フラグを設定する必要がある
* ゲートウェイ SKU を VpnGw1、VpnGw2、VpnGw3、または HighPerformance に設定する必要があります (レガシ SKU)。

その他のプロパティは、非アクティブ/アクティブのゲートウェイと同じです。 

### <a name="before-you-begin"></a>開始する前に
* Azure サブスクリプションを持っていることを確認します。 Azure サブスクリプションをまだお持ちでない場合は、[MSDN サブスクライバーの特典](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)を有効にするか、[無料アカウント](https://azure.microsoft.com/pricing/free-trial/)にサインアップしてください。
* Azure リソース マネージャー PowerShell コマンドレットをインストールする必要があります。 PowerShell コマンドレットのインストールの詳細については、「[Azure PowerShell の概要](/powershell/azure/overview)」を参照してください。

### <a name="step-1---create-and-configure-vnet1"></a>手順 1 - VNet1 を作成して構成する
#### <a name="1-declare-your-variables"></a>1.変数を宣言する
この練習では、最初に変数を宣言します。 次の例では、この演習の値を利用し、変数を宣言します。 運用環境の場合、実際の値を使用します。 この手順を通して実行し、この種の構成になれたら、これらの変数を利用できます。 変数を変更し、コピーし、PowerShell コンソールに貼り付けます。

```powershell
$Sub1 = "Ross"
$RG1 = "TestAARG1"
$Location1 = "West US"
$VNetName1 = "TestVNet1"
$FESubName1 = "FrontEnd"
$BESubName1 = "Backend"
$GWSubName1 = "GatewaySubnet"
$VNetPrefix11 = "10.11.0.0/16"
$VNetPrefix12 = "10.12.0.0/16"
$FESubPrefix1 = "10.11.0.0/24"
$BESubPrefix1 = "10.12.0.0/24"
$GWSubPrefix1 = "10.12.255.0/27"
$VNet1ASN = 65010
$DNS1 = "8.8.8.8"
$GWName1 = "VNet1GW"
$GW1IPName1 = "VNet1GWIP1"
$GW1IPName2 = "VNet1GWIP2"
$GW1IPconf1 = "gw1ipconf1"
$GW1IPconf2 = "gw1ipconf2"
$Connection12 = "VNet1toVNet2"
$Connection151 = "VNet1toSite5_1"
$Connection152 = "VNet1toSite5_2"
```

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2.サブスクリプションに接続して新しいリソース グループを作成する
リソース マネージャー コマンドレットを使用するように PowerShell モードを切り替えてください。 詳細については、「 [リソース マネージャーでの Windows PowerShell の使用](../powershell-azure-resource-manager.md)」を参照してください。

PowerShell コンソールを開き、アカウントに接続します。 接続するには、次のサンプルを参照してください。

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $Sub1
New-AzResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-testvnet1"></a>手順 3.TestVNet1 を作成する
下の例では、TestVNet1 という名前の仮想ネットワークと 3 つのサブネットを作成します。サブネットの名前は GatewaySubnet、FrontEnd、Backend です。 値を代入するときは、ゲートウェイの名前を必ず GatewaySubnet にすることが重要です。 別の名前にすると、ゲートウェイの作成は失敗します。

```powershell
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
```

### <a name="step-2---create-the-vpn-gateway-for-testvnet1-with-active-active-mode"></a>手順 2 - アクティブ/アクティブ モードで TestVNet1 に VPN ゲートウェイを作成する
#### <a name="1-create-the-public-ip-addresses-and-gateway-ip-configurations"></a>1.パブリック IP アドレスとゲートウェイの IP 構成を作成する
VNet 用に作成するゲートウェイに割り当てるパブリック IP アドレスを 2 つ要求します。 必要なサブネットと IP の構成も定義します。

```powershell
$gw1pip1 = New-AzPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$gw1pip2 = New-AzPublicIpAddress -Name $GW1IPName2 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

$vnet1 = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gw1ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1
$gw1ipconf2 = New-AzVirtualNetworkGatewayIpConfig -Name $GW1IPconf2 -Subnet $subnet1 -PublicIpAddress $gw1pip2
```

#### <a name="2-create-the-vpn-gateway-with-active-active-configuration"></a>2.アクティブ/アクティブ構成で VPN ゲートウェイを作成する
TestVNet1 用の仮想ネットワーク ゲートウェイを作成します。 GatewayIpConfig エントリが 2 つあり、EnableActiveActiveFeature フラグが設定されている点にご注意ください。 ゲートウェイの作成には時間がかかります (完了まで 45 分以上)。

```powershell
New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1,$gw1ipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet1ASN -EnableActiveActiveFeature -Debug
```

#### <a name="3-obtain-the-gateway-public-ip-addresses-and-the-bgp-peer-ip-address"></a>手順 3.ゲートウェイのパブリック IP アドレスと BGP ピア IP アドレスを取得する
ゲートウェイが作成されたら、Azure VPN ゲートウェイの BGP ピア IP アドレスを取得する必要があります。 オンプレミス VPN デバイスの BGP ピアとして Azure VPN ゲートウェイを構成するには、このアドレスが必要です。

```powershell
$gw1pip1 = Get-AzPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1
$gw1pip2 = Get-AzPublicIpAddress -Name $GW1IPName2 -ResourceGroupName $RG1
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
```

次のコマンドレットを使用し、VPN ゲートウェイに割り当てられる 2 つのパブリック IP アドレスを表示し、各ゲートウェイ インスタンスに対応する BGP ピア IP アドレスを表示します。

```powershell
PS D:\> $gw1pip1.IpAddress
40.112.190.5

PS D:\> $gw1pip2.IpAddress
138.91.156.129

PS D:\> $vnet1gw.BgpSettingsText
{
  "Asn": 65010,
  "BgpPeeringAddress": "10.12.255.4,10.12.255.5",
  "PeerWeight": 0
}
```

ゲートウェイ インスタンスのパブリック IP アドレスの順序と、対応する BGP ピアリング アドレスの順序は同じです。 この例では、パブリック IP が 40.112.190.5 の VM はその BGP ピアリング アドレスに 10.12.255.4 を使用し、138.91.156.129 のゲートウェイは 10.12.255.5 を使用します。 この情報は、アクティブ/アクティブ ゲートウェイに接続するオンプレミス VPN デバイスを設定する際に必要です。 下図のゲートウェイは、すべてのアドレスとともに表示されています。

![active-active gateway](./media/vpn-gateway-activeactive-rm-powershell/active-active-gw.png)

ゲートウェイが作成されたら、このゲートウェイを使用して、アクティブ/アクティブのクロスプレミス接続または VNet 間接続を確立できます。 以降のセクションでは、手順を確認して演習を完了します。

## <a name ="aacrossprem"></a>パート 2 - アクティブ/アクティブのクロスプレミス接続を確立する
クロスプレミス接続を確立するには、オンプレミス VPN デバイスを表すローカル ネットワーク ゲートウェイと、Azure VPN ゲートウェイをローカル ネットワーク ゲートウェイにつなげる接続を作成する必要があります。 この例では、Azure VPN ゲートウェイがアクティブ/アクティブ モードになっています。 その結果、オンプレミス デバイスが 1 つだけで (ローカル ネットワーク ゲートウェイ)、接続リソースが 1 つの場合であっても、両方の Azure VPN ゲートウェイ インスタンスによって、オンプレミス デバイスで S2S VPN トンネルが構築されます。

次に進む前に、この演習の [パート 1](#aagateway) を完了していることを確認してください。

### <a name="step-1---create-and-configure-the-local-network-gateway"></a>手順 1 - ローカル ネットワーク ゲートウェイを作成して構成する
#### <a name="1-declare-your-variables"></a>1.変数を宣言する
この演習では、引き続き、図に示されている構成を作成します。 値は実際の構成で使用する値に置換します。

```powershell
$RG5 = "TestAARG5"
$Location5 = "West US"
$LNGName51 = "Site5_1"
$LNGPrefix51 = "10.52.255.253/32"
$LNGIP51 = "131.107.72.22"
$LNGASN5 = 65050
$BGPPeerIP51 = "10.52.255.253"
```

ローカル ネットワーク ゲートウェイのパラメーターに関する注意点がいくつかあります。

* ローカル ネットワーク ゲートウェイは、VPN ゲートウェイと同じ場所またはリソース グループに配置することも、別の場所またはリソース グループに配置することもできます。 この例では、同じ Azure の場所にある別のリソース グループに配置します。
* 上述のようにオンプレミス VPN デバイスが 1 つのみの場合は、BGP プロトコルの有無に関係なく、アクティブ/アクティブ接続が機能します。 この例では、クロスプレミス接続に BGP を使用します。
* BGP が有効な場合、ローカル ネットワーク ゲートウェイ用に宣言する必要があるプレフィックスは、VPN デバイスの BGP ピア IP アドレスのホスト アドレスです。 この場合は、"10.52.255.253/32" の /32 プレフィックスです。
* 既に説明したように、オンプレミス ネットワークと Azure VNet では、異なる BGP ASN を使用する必要があります。 これらが同じ場合、オンプレミス VPN デバイスが既に ASN を使用して他の BGP 近隣ノードとピアリングしているのであれば、VNet ASN を変更する必要があります。

#### <a name="2-create-the-local-network-gateway-for-site5"></a>2.Site5 用のローカル ネットワーク ゲートウェイを作成する
続行する前に、サブスクリプションの 1 に接続されていることを確認してください。 まだ作成されていない場合は、リソース グループを作成します。

```powershell
New-AzResourceGroup -Name $RG5 -Location $Location5
New-AzLocalNetworkGateway -Name $LNGName51 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP51 -AddressPrefix $LNGPrefix51 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP51
```

### <a name="step-2---connect-the-vnet-gateway-and-local-network-gateway"></a>手順 2 - VNet ゲートウェイとローカル ネットワーク ゲートウェイを接続する
#### <a name="1-get-the-two-gateways"></a>1.2 つのゲートウェイを取得する

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw1 = Get-AzLocalNetworkGateway  -Name $LNGName51 -ResourceGroupName $RG5
```

#### <a name="2-create-the-testvnet1-to-site5-connection"></a>2.TestVNet1 から Site5 への接続を作成する
この手順では、"EnableBGP" を $True に設定して TestVNet1 から Site5_1 への接続を作成します。

```powershell
New-AzVirtualNetworkGatewayConnection -Name $Connection151 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw1 -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
```

#### <a name="3-vpn-and-bgp-parameters-for-your-on-premises-vpn-device"></a>手順 3.オンプレミス VPN デバイスの VPN と BGP パラメーター
次の例では、この演習用のオンプレミス VPN デバイスの BGP 構成セクションに入力するパラメーターの一覧を表示します。

```
- Site5 ASN            : 65050
- Site5 BGP IP         : 10.52.255.253
- Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
- Azure VNet ASN       : 65010
- Azure VNet BGP IP 1  : 10.12.255.4 for tunnel to 40.112.190.5
- Azure VNet BGP IP 2  : 10.12.255.5 for tunnel to 138.91.156.129
- Static routes        : Destination 10.12.255.4/32, nexthop the VPN tunnel interface to 40.112.190.5
                         Destination 10.12.255.5/32, nexthop the VPN tunnel interface to 138.91.156.129
- eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

接続は数分後に確立されます。IPsec 接続が確立されると、BGP ピアリング セッションが開始されます。 この例では、以下の図のようにオンプレミス デバイスが 1 つだけ構成されています。

![active-active-crossprem](./media/vpn-gateway-activeactive-rm-powershell/active-active.png)

### <a name="step-3---connect-two-on-premises-vpn-devices-to-the-active-active-vpn-gateway"></a>パート 3 - アクティブ/アクティブの VPN ゲートウェイに 2 つのオンプレミス VPN デバイスを接続する
同じオンプレミス ネットワークに VPN デバイスが 2 つある場合は、Azure VPN ゲートウェイを 2 番目の VPN デバイスに接続することによって、デュアル冗長性を実現できます。

#### <a name="1-create-the-second-local-network-gateway-for-site5"></a>1.Site5 用の 2 番目のローカル ネットワーク ゲートウェイを作成する
2 番目のローカル ネットワーク ゲートウェイ用の、ゲートウェイ IP アドレス、アドレス プレフィックス、BGP ピアリング アドレスは、同じオンプレミス ネットワークの前のローカル ネットワーク ゲートウェイと重複させることができないことにご注意ください。

```powershell
$LNGName52 = "Site5_2"
$LNGPrefix52 = "10.52.255.254/32"
$LNGIP52 = "131.107.72.23"
$BGPPeerIP52 = "10.52.255.254"
```

```powershell
New-AzLocalNetworkGateway -Name $LNGName52 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP52 -AddressPrefix $LNGPrefix52 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP52
```

#### <a name="2-connect-the-vnet-gateway-and-the-second-local-network-gateway"></a>2.VNet ゲートウェイと 2 番目のローカル ネットワーク ゲートウェイを接続する
"EnableBGP" を $True に設定し、TestVNet1 から Site5_2 への接続を作成します。

```powershell
$lng5gw2 = Get-AzLocalNetworkGateway -Name $LNGName52 -ResourceGroupName $RG5
```

```powershell
New-AzVirtualNetworkGatewayConnection -Name $Connection152 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw2 -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
```

#### <a name="3-vpn-and-bgp-parameters-for-your-second-on-premises-vpn-device"></a>手順 3.2 番目のオンプレミス VPN デバイスの VPN と BGP パラメーター
同様に、一覧の下にあるのは、2 番目のデバイスに入力するパラメーターです。

```
- Site5 ASN            : 65050
- Site5 BGP IP         : 10.52.255.254
- Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
- Azure VNet ASN       : 65010
- Azure VNet BGP IP 1  : 10.12.255.4 for tunnel to 40.112.190.5
- Azure VNet BGP IP 2  : 10.12.255.5 for tunnel to 138.91.156.129
- Static routes        : Destination 10.12.255.4/32, nexthop the VPN tunnel interface to 40.112.190.5
                         Destination 10.12.255.5/32, nexthop the VPN tunnel interface to 138.91.156.129
- eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

接続 (トンネル) が確立されると、デュアル冗長 VPN デバイスと、オンプレミス ネットワークと Azure を接続するトンネルが作成されます。

![dual-redundancy-crossprem](./media/vpn-gateway-activeactive-rm-powershell/dual-redundancy.png)

## <a name ="aav2v"></a>パート 3 - アクティブ/アクティブの VNet 間接続を確立する
このセクションでは、BGP でアクティブ/アクティブの VNet 間接続を作成します。 

以下の指示は、前の手順からの続きになります。 BGP で TestVNet1 と VPN ゲートウェイを作成して構成するには、 [パート 1](#aagateway) を完了している必要があります。 

### <a name="step-1---create-testvnet2-and-the-vpn-gateway"></a>手順 1 - TestVNet2 と VPN ゲートウェイを作成する
新しい仮想ネットワークである TestVNet2 の IP アドレス空間がどの VNet 範囲とも重ならないようにすることが重要です。

この例では、仮想ネットワークは同じサブスクリプションに属しています。 VNet 間接続は、異なるサブスクリプション間にセットアップできます。詳細については、[VNet 間の接続の構成](vpn-gateway-vnet-vnet-rm-ps.md)に関する記事をご覧ください。 接続の作成時に BGP を有効にするには、必ず "-EnableBgp $True" を追加してください。

#### <a name="1-declare-your-variables"></a>1.変数を宣言する
値は実際の構成で使用する値に置換します。

```powershell
$RG2 = "TestAARG2"
$Location2 = "East US"
$VNetName2 = "TestVNet2"
$FESubName2 = "FrontEnd"
$BESubName2 = "Backend"
$GWSubName2 = "GatewaySubnet"
$VNetPrefix21 = "10.21.0.0/16"
$VNetPrefix22 = "10.22.0.0/16"
$FESubPrefix2 = "10.21.0.0/24"
$BESubPrefix2 = "10.22.0.0/24"
$GWSubPrefix2 = "10.22.255.0/27"
$VNet2ASN = 65020
$DNS2 = "8.8.8.8"
$GWName2 = "VNet2GW"
$GW2IPName1 = "VNet2GWIP1"
$GW2IPconf1 = "gw2ipconf1"
$GW2IPName2 = "VNet2GWIP2"
$GW2IPconf2 = "gw2ipconf2"
$Connection21 = "VNet2toVNet1"
$Connection12 = "VNet1toVNet2"
```

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2.新しいリソース グループに TestVNet2 を作成する

```powershell
New-AzResourceGroup -Name $RG2 -Location $Location2

$fesub2 = New-AzVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
$besub2 = New-AzVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
$gwsub2 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

New-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2
```

#### <a name="3-create-the-active-active-vpn-gateway-for-testvnet2"></a>手順 3.TestVNet2 のアクティブ/アクティブの VPN ゲートウェイを作成する
VNet 用に作成するゲートウェイに割り当てるパブリック IP アドレスを 2 つ要求します。 必要なサブネットと IP の構成も定義します。

```powershell
$gw2pip1 = New-AzPublicIpAddress -Name $GW2IPName1 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic
$gw2pip2 = New-AzPublicIpAddress -Name $GW2IPName2 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic

$vnet2 = Get-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
$subnet2 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gw2ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW2IPconf1 -Subnet $subnet2 -PublicIpAddress $gw2pip1
$gw2ipconf2 = New-AzVirtualNetworkGatewayIpConfig -Name $GW2IPconf2 -Subnet $subnet2 -PublicIpAddress $gw2pip2
```

AS 番号と "EnableActiveActiveFeature" フラグを使用して、VPN ゲートウェイを作成します。 Azure VPN ゲートウェイでは既定の ASN をオーバーライドする必要があることに注意してください。 BGP とトランジット ルーティングを有効にするために、接続された VNet の ASN はそれぞれ異なっている必要があります。

```powershell
New-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gw2ipconf1,$gw2ipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet2ASN -EnableActiveActiveFeature
```

### <a name="step-2---connect-the-testvnet1-and-testvnet2-gateways"></a>手順 2 - TestVNet1 と TestVNet2 のゲートウェイを接続する
この例では、両方のゲートウェイが同じサブスクリプションにあります。 この手順は、同じ PowerShell セッションで実行できます。

#### <a name="1-get-both-gateways"></a>1.両方のゲートウェイを取得する
ログインし、サブスクリプション 1 に接続します。

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$vnet2gw = Get-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2
```

#### <a name="2-create-both-connections"></a>2.両方の接続を作成する
この手順では、TestVNet1 から TestVNet2 への接続と、TestVNet2 から TestVNet1 への接続を作成します。

```powershell
New-AzVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True

New-AzVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True
```

> [!IMPORTANT]
> 両方の接続で BGP を有効にしてください。
> 
> 

これらの手順を完了すると、数分で接続が確立されます。また、デュアル冗長性の VNet 間接続が完了すると、BGP ピアリング セッションが開始されます。

![active-active-v2v](./media/vpn-gateway-activeactive-rm-powershell/vnet-to-vnet.png)

## <a name ="aaupdate"></a>既存の VPN Gateway を更新する

このセクションでは、既存の Azure VPN Gateway をアクティブ/スタンバイ モードからアクティブ/アクティブ モード (またはその逆) に変更する方法を説明します。

### <a name="change-an-active-standby-gateway-to-an-active-active-gateway"></a>アクティブ/スタンバイ ゲートウェイをアクティブ/アクティブ ゲートウェイに変更する

次の例では、アクティブ/スタンバイ ゲートウェイをアクティブ/アクティブ ゲートウェイに変換します。 アクティブ/スタンバイ ゲートウェイをアクティブ/アクティブに変更するときは、新たにパブリック IP アドレスを作成して、第 2 のゲートウェイ IP 構成を追加します。

#### <a name="1-declare-your-variables"></a>1.変数を宣言する

次の例に使用されているパラメーターは、実際の構成に必要な設定に置き換えたうえで、変数を宣言してください。

```powershell
$GWName = "TestVNetAA1GW"
$VNetName = "TestVNetAA1"
$RG = "TestVPNActiveActive01"
$GWIPName2 = "gwpip2"
$GWIPconf2 = "gw1ipconf2"
```

必要な変数を宣言したら、次の例をコピーして PowerShell コンソールに貼り付けてください。

```powershell
$vnet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gw = Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
$location = $gw.Location
```

#### <a name="2-create-the-public-ip-address-then-add-the-second-gateway-ip-configuration"></a>2.パブリック IP アドレスを作成し、2 番目のゲートウェイ IP 構成を追加する

```powershell
$gwpip2 = New-AzPublicIpAddress -Name $GWIPName2 -ResourceGroupName $RG -Location $location -AllocationMethod Dynamic
Add-AzVirtualNetworkGatewayIpConfig -VirtualNetworkGateway $gw -Name $GWIPconf2 -Subnet $subnet -PublicIpAddress $gwpip2
```

#### <a name="3-enable-active-active-mode-and-update-the-gateway"></a>手順 3.アクティブ/アクティブ モードを有効にし、ゲートウェイを更新する

この手順では、アクティブ/アクティブ モードを有効にし、ゲートウェイを更新します。 この例の VPN Gateway で現在使用しているのは従来の Standard SKU です。 しかし、アクティブ/アクティブでは Standard SKU がサポートされません。 実際に使用するサポート対象のレガシ SKU を指定すれば、対応した SKU (このケースでは HighPerformance) にサイズ変更することができます。

* この手順では、従来の SKU を新しい SKU に変更することはできません。 サポートされている SKU へのサイズ変更は、従来の SKU の範囲内でのみ行うことができます。 たとえば VpnGw1 はアクティブ/アクティブに対応していますが、Standard から VpnGw1 に SKU を変更することはできません。Standard は従来の SKU で、VpnGw1 は最新の SKU であるためです。 SKU のサイズ変更と移行の詳細については、「[ゲートウェイの SKU](vpn-gateway-about-vpngateways.md#gwsku)」を参照してください。

* 最新の SKU のサイズを変更する場合 (VpnGw1 から VpnGw3 に変更する場合など) は、どちらの SKU も同じ SKU ファミリーに属しているため、この手順で対処できます。 その場合は、```-GatewaySku VpnGw3``` という値を使用することになるでしょう。

実際の環境で、ゲートウェイのサイズ変更が不要である場合、-GatewaySku を指定する必要はありません。 この手順で、実際に更新を開始するには、PowerShell でゲートウェイ オブジェクトを設定する必要があることに注意してください。 ゲートウェイのサイズ変更を行わない場合でも、この更新には 30 分から 45 分かかることがあります。

```powershell
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -EnableActiveActiveFeature -GatewaySku HighPerformance
```

### <a name="change-an-active-active-gateway-to-an-active-standby-gateway"></a>アクティブ/アクティブ ゲートウェイをアクティブ/スタンバイ ゲートウェイに変更する
#### <a name="1-declare-your-variables"></a>1.変数を宣言する

次の例に使用されているパラメーターは、実際の構成に必要な設定に置き換えたうえで、変数を宣言してください。

```powershell
$GWName = "TestVNetAA1GW"
$RG = "TestVPNActiveActive01"
```

変数を宣言したら、削除する IP 構成の名前を取得します。

```powershell
$gw = Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
$ipconfname = $gw.IpConfigurations[1].Name
```

#### <a name="2-remove-the-gateway-ip-configuration-and-disable-the-active-active-mode"></a>2.ゲートウェイの IP 構成を削除して、アクティブ/アクティブ モードを無効にします。

この例では、ゲートウェイの IP 構成を削除して、アクティブ/アクティブ モードを無効にします。 実際に更新を開始するには、PowerShell でゲートウェイ オブジェクトを設定する必要があることに注意してください。

```powershell
Remove-AzVirtualNetworkGatewayIpConfig -Name $ipconfname -VirtualNetworkGateway $gw
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -DisableActiveActiveFeature
```

更新には最大で 30 ～ 45 分かかります。

## <a name="next-steps"></a>次の手順
接続が完成したら、仮想ネットワークに仮想マシンを追加することができます。 手順については、 [仮想マシンの作成](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) に関するページを参照してください。
