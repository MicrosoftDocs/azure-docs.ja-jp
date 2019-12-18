---
title: 'Azure VPN Gateway: BGP を構成する: PowerShell'
description: この記事では、Azure Resource Manager と PowerShell を使用して Azure VPN ゲートウェイで BGP を構成する方法を説明します。
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: ''
tags: azure-resource-manager
ms.assetid: 905b11a7-1333-482c-820b-0fd0f44238e5
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/12/2017
ms.author: yushwang
ms.openlocfilehash: f4c5e3ddb5a7f5c9a21d29aa24fe28e095123836
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151397"
---
# <a name="how-to-configure-bgp-on-azure-vpn-gateways-using-powershell"></a>PowerShell を使用して Azure VPN Gateway で BGP を構成する方法
この記事では、Resource Manager デプロイ モデルと PowerShell を使用して、クロスプレミスのサイト間 (S2S) VPN 接続および VNet 間接続上で BGP を有効にする手順について説明します。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="about-bgp"></a>BGP について
BGP は、2 つ以上のネットワーク間でルーティングと到達可能性の情報を交換するためにインターネット上で広く使用されている標準のルーティング プロトコルです。 Azure VPN ゲートウェイとオンプレミスの VPN デバイス (BGP ピアまたは BGP 近隣ノードと呼ばれる) が BGP を使用して "ルート" を交換します。これによって、関連するゲートウェイまたはルーターの可用性と、BGP のプレフィックスが到達できる可能性に関する情報が両方のゲートウェイに伝達されます。 また、BGP では、BGP ゲートウェイが特定の BGP ピアから学習したルートを他のすべての BGP ピアに伝達することで、複数のネットワークでトランジット ルーティングを行うこともできます。

BGP の利点の詳しい説明と、BGP を使用する場合の技術面の要件および考慮事項の説明については、「[Azure VPN ゲートウェイを使用した BGP の概要](vpn-gateway-bgp-overview.md)」をご覧ください。

## <a name="getting-started-with-bgp-on-azure-vpn-gateways"></a>Azure VPN ゲートウェイでの BGP の使用

この記事では、以下のタスクの手順を説明します。

* [パート 1 - Azure VPN ゲートウェイで BGP を有効にする](#enablebgp)
* パート 2 - BGP を使用してクロスプレミス接続を確立する
* [パート 3 - BGP を使用して VNet 間接続を確立する](#v2vbgp)

各パートの手順は、ネットワーク接続で BGP を有効にするための基本的な構成要素を形成します。 3 つのパートをすべて完了すると、次の図のようなトポロジが構築されます。

![BGP topology](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

各部分を組み合わせると、ニーズに合わせて、より複雑でマルチホップの通過ネットワークを構築することができます。

## <a name ="enablebgp"></a>パート 1 - Azure VPN ゲートウェイで BGP を構成する
構成手順では、次の図に示すように、Azure VPN ゲートウェイの BGP パラメーターを設定します。

![BGP Gateway](./media/vpn-gateway-bgp-resource-manager-ps/bgp-gateway.png)

### <a name="before-you-begin"></a>開始する前に
* Azure サブスクリプションを持っていることを確認します。 Azure サブスクリプションをまだお持ちでない場合は、[MSDN サブスクライバーの特典](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)を有効にするか、[無料アカウント](https://azure.microsoft.com/pricing/free-trial/)にサインアップしてください。
* Azure Resource Manager PowerShell コマンドレットをインストールします。 PowerShell コマンドレットのインストールの詳細については、[Azure PowerShell のインストールおよび構成方法](/powershell/azure/overview)に関する記事を参照してください。 

### <a name="step-1---create-and-configure-vnet1"></a>手順 1 - VNet1 を作成して構成する
#### <a name="1-declare-your-variables"></a>1.変数を宣言する
この練習では、最初に変数を宣言します。 以下の例では、この演習の値を使って変数を宣言します。 運用環境の場合、実際の値を使用します。 この手順を通して実行し、この種の構成になれたら、これらの変数を利用できます。 変数を変更し、コピーし、PowerShell コンソールに貼り付けます。

```powershell
$Sub1 = "Replace_With_Your_Subscription_Name"
$RG1 = "TestBGPRG1"
$Location1 = "East US"
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
$GWIPName1 = "VNet1GWIP"
$GWIPconfName1 = "gwipconf1"
$Connection12 = "VNet1toVNet2"
$Connection15 = "VNet1toSite5"
```

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2.サブスクリプションに接続して新しいリソース グループを作成する
Resource Manager コマンドレットを使用する場合は、必ず PowerShell モードに切り替えてください。 詳細については、「 [リソース マネージャーでの Windows PowerShell の使用](../powershell-azure-resource-manager.md)」を参照してください。

PowerShell コンソールを開き、アカウントに接続します。 接続するには、次のサンプルを参照してください。

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $Sub1
New-AzResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-testvnet1"></a>3.TestVNet1 を作成する
以下の例では、TestVNet1 という名前の仮想ネットワークと 3 つのサブネットを作成します。サブネットの名前は GatewaySubnet、FrontEnd、Backend です。 値を代入するときは、ゲートウェイの名前を必ず GatewaySubnet にすることが重要です。 別の名前にすると、ゲートウェイの作成は失敗します。

```powershell
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
```

### <a name="step-2---create-the-vpn-gateway-for-testvnet1-with-bgp-parameters"></a>手順 2 - BGP パラメーターを指定して TestVNet1 の VPN ゲートウェイを作成する
#### <a name="1-create-the-ip-and-subnet-configurations"></a>1.IP とサブネットの構成を作成する
VNet 用に作成するゲートウェイに割り当てるパブリック IP アドレスを要求します。 必要なサブネットと IP の構成も定義します。

```powershell
$gwpip1 = New-AzPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

$vnet1 = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gwipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 -Subnet $subnet1 -PublicIpAddress $gwpip1
```

#### <a name="2-create-the-vpn-gateway-with-the-as-number"></a>2.AS 番号で VPN ゲートウェイを作成する
TestVNet1 用の仮想ネットワーク ゲートウェイを作成します。 BGP にはルート ベースの VPN ゲートウェイのほか、TestVNet1 の ASN (AS 番号) を設定するための追加のパラメーター -Asn も必要です。 ASN パラメーターを設定しない場合、ASN 65515 が割り当てられます。 ゲートウェイの作成には時間がかかります (完了に 30 分以上必要とします)。

```powershell
New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet1ASN
```

#### <a name="3-obtain-the-azure-bgp-peer-ip-address"></a>3.Azure BGP ピア IP アドレスを取得する
ゲートウェイが作成されたら、Azure VPN ゲートウェイの BGP ピア IP アドレスを取得する必要があります。 オンプレミス VPN デバイスの BGP ピアとして Azure VPN ゲートウェイを構成するには、このアドレスが必要です。

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$vnet1gw.BgpSettingsText
```

最後のコマンドは、Azure VPN ゲートウェイの対応する BGP 構成を示します。次に、例を示します。

```powershell
$vnet1gw.BgpSettingsText
{
    "Asn": 65010,
    "BgpPeeringAddress": "10.12.255.30",
    "PeerWeight": 0
}
```

ゲートウェイが作成されたら、このゲートウェイを使用して、BGP でクロスプレミス接続または VNet 間接続を確立することができます。 以降のセクションでは、手順を確認して演習を完了します。

## <a name ="crossprembbgp"></a>パート 2 - BGP を使用してクロスプレミス接続を確立する

クロスプレミス接続を確立するには、オンプレミス VPN デバイスを表すローカル ネットワーク ゲートウェイと、VPN ゲートウェイをローカル ネットワーク ゲートウェイにつなげる接続を作成する必要があります。 これらの手順を説明する記事はいくつかありますが、この記事では、BGP 構成パラメーターを指定するために必要な追加のプロパティについても取り上げます。

![BGP for Cross-Premises](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crossprem.png)

次に進む前に、この演習の [パート 1](#enablebgp) を完了していることを確認してください。

### <a name="step-1---create-and-configure-the-local-network-gateway"></a>手順 1 - ローカル ネットワーク ゲートウェイを作成して構成する

#### <a name="1-declare-your-variables"></a>1.変数を宣言する

この演習では、引き続き、図に示されている構成を作成します。 値は実際の構成で使用する値に置換します。

```powershell
$RG5 = "TestBGPRG5"
$Location5 = "East US 2"
$LNGName5 = "Site5"
$LNGPrefix50 = "10.52.255.254/32"
$LNGIP5 = "Your_VPN_Device_IP"
$LNGASN5 = 65050
$BGPPeerIP5 = "10.52.255.254"
```

ローカル ネットワーク ゲートウェイのパラメーターに関する注意点がいくつかあります。

* ローカル ネットワーク ゲートウェイは、VPN ゲートウェイと同じ場所またはリソース グループに配置することも、別の場所またはリソース グループに配置することもできます。 この例では、別の場所の別のリソース グループに配置します。
* ローカル ネットワーク ゲートウェイ用に宣言する必要があるプレフィックスは、VPN デバイス上の BGP ピア IP アドレスのホスト アドレスです。 この場合は、"10.52.255.254/32" の /32 プレフィックスです。
* 既に説明したように、オンプレミス ネットワークと Azure VNet では、異なる BGP ASN を使用する必要があります。 これらが同じ場合、オンプレミス VPN デバイスが既に ASN を使用して他の BGP 近隣ノードとピアリングしているのであれば、VNet ASN を変更する必要があります。

続行する前に、サブスクリプション 1 に接続されていることを確認してください。

#### <a name="2-create-the-local-network-gateway-for-site5"></a>2.Site5 用のローカル ネットワーク ゲートウェイを作成する

ローカル ネットワーク ゲートウェイを作成する前に、リソース グループが作成されていない場合は、必ず作成してください。 また、ローカル ネットワーク ゲートウェイ用の 2 つの追加パラメーターであるAsn と BgpPeerAddress にも注意してください。

```powershell
New-AzResourceGroup -Name $RG5 -Location $Location5

New-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix50 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5
```

### <a name="step-2---connect-the-vnet-gateway-and-local-network-gateway"></a>手順 2 - VNet ゲートウェイとローカル ネットワーク ゲートウェイを接続する

#### <a name="1-get-the-two-gateways"></a>1.2 つのゲートウェイを取得する

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG5
```

#### <a name="2-create-the-testvnet1-to-site5-connection"></a>2.TestVNet1 から Site5 への接続を作成する

この手順では、TestVNet1 から Site5 への接続を作成します。 この接続の BGP を有効にするために、"-EnableBGP $True" を指定する必要があります。 既に説明したように、同じ Azure VPN ゲートウェイで BGP 接続と BGP 以外の接続の両方を混在させることはできません。 接続プロパティで BGP を有効にしない限り、両方のゲートウェイで BGP パラメーターが既に構成されていても、Azure はこの接続の BGP を有効にしません。

```powershell
New-AzVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
```

次の例では、この演習用のオンプレミス VPN デバイスの BGP 構成セクションに入力するパラメーターの一覧を表示します。

```

- Site5 ASN            : 65050
- Site5 BGP IP         : 10.52.255.254
- Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
- Azure VNet ASN       : 65010
- Azure VNet BGP IP    : 10.12.255.30
- Static route         : Add a route for 10.12.255.30/32, with nexthop being the VPN tunnel interface on your device
- eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

接続は数分後に確立されます。IPsec 接続が確立されると、BGP ピアリング セッションが開始されます。

## <a name ="v2vbgp"></a>パート 3 - BGP を使用して VNet 間接続を確立する

このセクションでは、次の図に示すように、BGP を使用して VNet 間接続を追加します。

![BGP for VNet-to-VNet](./media/vpn-gateway-bgp-resource-manager-ps/bgp-vnet2vnet.png)

以下の指示は、前の手順からの続きになります。 BGP で TestVNet1 と VPN ゲートウェイを作成して構成するには、 [パート 1](#enablebgp) を完了している必要があります。 

### <a name="step-1---create-testvnet2-and-the-vpn-gateway"></a>手順 1 - TestVNet2 と VPN ゲートウェイを作成する

新しい仮想ネットワークである TestVNet2 の IP アドレス空間がどの VNet 範囲とも重ならないようにすることが重要です。

この例では、仮想ネットワークは同じサブスクリプションに属しています。 異なるサブスクリプション間に VNet 間の接続を設定できます。 詳細については、「[VNet 間の接続の構成](vpn-gateway-vnet-vnet-rm-ps.md)」を参照してください。 接続の作成時に BGP を有効にするには、必ず "-EnableBgp $True" を追加してください。

#### <a name="1-declare-your-variables"></a>1.変数を宣言する

値は実際の構成で使用する値に置換します。

```powershell
$RG2 = "TestBGPRG2"
$Location2 = "West US"
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
$GWIPName2 = "VNet2GWIP"
$GWIPconfName2 = "gwipconf2"
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

#### <a name="3-create-the-vpn-gateway-for-testvnet2-with-bgp-parameters"></a>3.BGP パラメーターを指定して TestVNet2 の VPN ゲートウェイを作成する

VNet 用に作成するゲートウェイに割り当てるパブリック IP アドレスを要求し、必要なサブネットと IP 構成を定義します。

```powershell
$gwpip2    = New-AzPublicIpAddress -Name $GWIPName2 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic

$vnet2     = Get-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
$subnet2   = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gwipconf2 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName2 -Subnet $subnet2 -PublicIpAddress $gwpip2
```

AS 番号で VPN ゲートウェイを作成します。 Azure VPN ゲートウェイでは既定の ASN をオーバーライドする必要があります。 BGP とトランジット ルーティングを有効にするために、接続された VNet の ASN はそれぞれ異なっている必要があります。

```powershell
New-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gwipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet2ASN
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

これらの手順を完了すると、数分後に接続が確立します。 BGP ピアリング セッションは、VNet 間の接続が完了すると有効になります。

この演習の 3 つのパートをすべて完了すると、次のようなネットワーク トポロジが確立されます。

![BGP for VNet-to-VNet](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

## <a name="next-steps"></a>次の手順

接続が完成したら、仮想ネットワークに仮想マシンを追加することができます。 手順については、 [仮想マシンの作成](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) に関するページを参照してください。
