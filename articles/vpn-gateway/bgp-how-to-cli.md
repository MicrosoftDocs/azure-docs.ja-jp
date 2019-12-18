---
title: 'Azure VPN ゲートウェイで BGP を構成する: Resource Manager と CLI | Microsoft Docs'
description: この記事では、Azure Resource Manager と CLI を使用して Azure VPN ゲートウェイで BGP を構成する方法を説明します。
services: vpn-gateway
documentationcenter: na
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/25/2018
ms.author: yushwang
ms.openlocfilehash: 51402196c8429797b644357822a1e3c08982b384
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "65209503"
---
# <a name="how-to-configure-bgp-on-an-azure-vpn-gateway-by-using-cli"></a>CLI を使用して Azure VPN ゲートウェイで BGP を構成する方法

この記事では、Azure Resource Manager デプロイ モデルと Azure CLI を使用して、クロスプレミスのサイト間 (S2S) VPN 接続および VNet 間接続 (仮想ネットワーク間の接続) 上で BGP を有効にする方法について説明します。

## <a name="about-bgp"></a>BGP について

BGP は、2 つ以上のネットワーク間でルーティングと到達可能性の情報を交換するためにインターネット上で広く使用されている標準のルーティング プロトコルです。 VPN ゲートウェイとオンプレミスの VPN デバイス (BGP ピアまたは BGP 近隣ノードと呼ばれる) は BGP を使用してルートを交換します。 このルートにより、関連するゲートウェイまたはルーターの可用性と、プレフィックスが到達できる可能性に関する情報が両方のゲートウェイに伝達されます。 また、BGP では、BGP ゲートウェイが特定の BGP ピアから学習したルートを他のすべての BGP ピアに伝達することで、複数のネットワークでトランジット ルーティングを行うこともできます。

BGP の利点の詳しい説明と、BGP を使用する場合の技術面の要件および考慮事項の説明については、「[Azure VPN ゲートウェイを使用した BGP の概要](vpn-gateway-bgp-overview.md)」をご覧ください。

この記事では、次のタスクについて説明します。

* [VPN ゲートウェイに対して BGP を有効にする](#enablebgp) (必須)

  次のセクションのいずれかまたは両方を完了できます。

* [BGP を使用してクロスプレミス接続を確立する](#crossprembgp)
* [BGP を使用して VNet 間接続を確立する](#v2vbgp)

この 3 つの各セクションでは、ネットワーク接続で BGP を有効にするための基本的な構成要素を形成します。 3 つのセクションをすべて完了すると、次の図のようなトポロジが構築されます。

![BGP topology](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

これらのセクションを組み合わせると、ニーズに合わせて、より複雑なマルチホップの通過ネットワークを構築することができます。

## <a name ="enablebgp"></a>VPN ゲートウェイに対して BGP を有効にする

このセクションで説明する手順を実行してから、他の 2 つの構成セクションの手順を実行する必要があります。 以下の構成手順では、次の図に示すように、Azure VPN ゲートウェイの BGP パラメーターを設定します。

![BGP ゲートウェイ](./media/vpn-gateway-bgp-resource-manager-ps/bgp-gateway.png)

### <a name="before-you-begin"></a>開始する前に

最新バージョンの CLI コマンド (2.0 以降) をインストールします。 CLI コマンドのインストール方法については、「[Azure CLI のインストール](/cli/azure/install-azure-cli)」および「[Azure CLI を使ってみる](/cli/azure/get-started-with-azure-cli)」を参照してください。

### <a name="step-1-create-and-configure-testvnet1"></a>手順 1:TestVNet1 の作成と構成

#### <a name="Login"></a>1.サブスクリプションへの接続

[!INCLUDE [CLI login](../../includes/vpn-gateway-cli-login-include.md)]

#### <a name="2-create-a-resource-group"></a>手順 2.リソース グループの作成

次の例では、TestRG1 という名前のリソース グループを "eastus" の場所に作成します。 仮想ネットワークを作成するリージョンにリソース グループが既にある場合は、代わりにそのリソース グループを使用できます。

```azurecli
az group create --name TestBGPRG1 --location eastus
```

#### <a name="3-create-testvnet1"></a>手順 3.TestVNet1 を作成する

次の例では、TestVNet1 という名前の仮想ネットワークと、次の 3 つのサブネットを作成します: GatewaySubnet、FrontEnd、BackEnd。 値を代入するときは、ゲートウェイ サブネットの名前を必ず GatewaySubnet にすることが重要です。 別の名前にすると、ゲートウェイの作成は失敗します。

1 番目のコマンドは、フロントエンド アドレス空間と、FrontEnd サブネットを作成します。 2 番目のコマンドでは、BackEnd サブネット用に追加のアドレス空間を作成します。 3 番目と 4 番目のコマンドは、BackEnd サブネットと GatewaySubnet を作成します。

```azurecli
az network vnet create -n TestVNet1 -g TestBGPRG1 --address-prefix 10.11.0.0/16 -l eastus --subnet-name FrontEnd --subnet-prefix 10.11.0.0/24 
 
az network vnet update -n TestVNet1 --address-prefixes 10.11.0.0/16 10.12.0.0/16 -g TestBGPRG1 
 
az network vnet subnet create --vnet-name TestVNet1 -n BackEnd -g TestBGPRG1 --address-prefix 10.12.0.0/24 
 
az network vnet subnet create --vnet-name TestVNet1 -n GatewaySubnet -g TestBGPRG1 --address-prefix 10.12.255.0/27 
```

### <a name="step-2-create-the-vpn-gateway-for-testvnet1-with-bgp-parameters"></a>手順 2:BGP パラメーターを指定して TestVNet1 の VPN ゲートウェイを作成する

#### <a name="1-create-the-public-ip-address"></a>1.パブリック IP アドレスの作成

パブリック IP アドレスを要求します。 仮想ネットワーク用に作成した VPN ゲートウェイにパブリック IP アドレスが割り当てられます。

```azurecli
az network public-ip create -n GWPubIP -g TestBGPRG1 --allocation-method Dynamic 
```

#### <a name="2-create-the-vpn-gateway-with-the-as-number"></a>2.AS 番号で VPN ゲートウェイを作成する

TestVNet1 用の仮想ネットワーク ゲートウェイを作成します。 BGP ではルートベースの VPN ゲートウェイが必要です。 TestVNet1 の自律システム番号 (ASN) を設定するには、追加のパラメーター `-Asn` も必要です。 ゲートウェイの作成には時間がかかります (完了に 45 分以上必要とします)。 

`--no-wait` パラメーターを使用してこのコマンドを実行した場合は、フィードバックや出力が表示されなくなります。 `--no-wait` パラメーターは、ゲートウェイをバックグラウンドで作成するためのものです。 これは、VPN ゲートウェイがすぐに作成されるという意味ではありません。

```azurecli
az network vnet-gateway create -n VNet1GW -l eastus --public-ip-address GWPubIP -g TestBGPRG1 --vnet TestVNet1 --gateway-type Vpn --sku HighPerformance --vpn-type RouteBased --asn 65010 --no-wait
```

#### <a name="3-obtain-the-azure-bgp-peer-ip-address"></a>3.Azure BGP ピア IP アドレスを取得する

ゲートウェイが作成されたら、Azure VPN ゲートウェイの BGP ピア IP アドレスを取得する必要があります。 オンプレミスの VPN デバイスの BGP ピアとして VPN ゲートウェイを構成するには、このアドレスが必要です。

次のコマンドを実行し、出力の上部にある `bgpSettings` セクションを確認します。

```azurecli
az network vnet-gateway list -g TestBGPRG1 
 
  
"bgpSettings": { 
      "asn": 65010, 
      "bgpPeeringAddress": "10.12.255.30", 
      "peerWeight": 0 
    }
```

ゲートウェイが作成されたら、このゲートウェイを使用して、BGP でクロスプレミス接続または VNet 間接続を確立することができます。

## <a name ="crossprembgp"></a>BGP を使用してクロスプレミス接続を確立する

クロスプレミス接続を確立するには、オンプレミスの VPN デバイスを表すローカル ネットワーク ゲートウェイを作成する必要があります。 その後、Azure VPN ゲートウェイをローカル ネットワーク ゲートウェイに接続します。 次の手順は、他の接続の作成方法と似ていますが、BGP 構成パラメーターを指定するのに必要なプロパティが追加されています。

![クロスプレミス用の BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crossprem.png)


### <a name="step-1-create-and-configure-the-local-network-gateway"></a>手順 1:ローカル ネットワーク ゲートウェイを作成して構成する

この演習では、引き続き、図に示されている構成を作成します。 値は実際の構成で使用する値に置換します。 ローカル ネットワーク ゲートウェイを使用する場合は、次のことにご注意ください。

* ローカル ネットワーク ゲートウェイは、VPN ゲートウェイと同じ場所およびリソース グループに配置することも、別の場所およびリソース グループに配置することもできます。 この例では、異なる場所の異なるリソース グループのゲートウェイを示します。
* ローカル ネットワーク ゲートウェイ用に宣言する必要がある最小限のプレフィックスは、VPN デバイスの BGP ピア IP アドレスのホスト アドレスです。 この場合は、10.51.255.254/32 の /32 プレフィックスです。
* 既に説明したように、オンプレミスのネットワークと Azure 仮想ネットワークでは、異なる BGP ASN を使用する必要があります。 これらが同じ場合、オンプレミス VPN デバイスが既に ASN を使用して他の BGP 近隣ノードとピアリングしているのであれば、VNet ASN を変更する必要があります。

先に進む前に、この演習の「[VPN ゲートウェイに対して BGP を有効にする](#enablebgp)」セクションが終了していること、さらにサブスクリプション 1 にまだ接続されていることをご確認ください。 この例では、新しいリソース グループを作成します。 また、ローカル ネットワーク ゲートウェイ用の 2 つの追加パラメーターである `Asn` と `BgpPeerAddress` にもご注意ください。

```azurecli
az group create -n TestBGPRG5 -l eastus2 
 
az network local-gateway create --gateway-ip-address 23.99.221.164 -n Site5 -g TestBGPRG5 --local-address-prefixes 10.51.255.254/32 --asn 65050 --bgp-peering-address 10.51.255.254
```

### <a name="step-2-connect-the-vnet-gateway-and-local-network-gateway"></a>手順 2:VNet ゲートウェイとローカル ネットワーク ゲートウェイを接続する

この手順では、TestVNet1 から Site5 への接続を作成します。 この接続に対して BGP を有効にするには、`--enable-bgp` パラメーターを指定する必要があります。 

この例では、仮想ネットワーク ゲートウェイとローカル ネットワーク ゲートウェイがそれぞれ異なるリソース グループにあります。 ゲートウェイがそれぞれ異なるリソース グループに属している場合は、仮想ネットワーク間の接続を設定するために、2 つのゲートウェイのリソース ID 全体を指定する必要があります。

#### <a name="1-get-the-resource-id-of-vnet1gw"></a>1.VNet1GW のリソース ID を取得する

次のコマンドの出力を使用して、VNet1GW のリソース ID を取得します。

```azurecli
az network vnet-gateway show -n VNet1GW -g TestBGPRG1
```

出力結果から `"id":` 行を探し出します。 次のセクションで接続を作成する際に引用符で囲まれた値が必要になります。

出力例:

```
{ 
  "activeActive": false, 
  "bgpSettings": { 
    "asn": 65010, 
    "bgpPeeringAddress": "10.12.255.30", 
    "peerWeight": 0 
  }, 
  "enableBgp": true, 
  "etag": "W/\"<your etag number>\"", 
  "gatewayDefaultSite": null, 
  "gatewayType": "Vpn", 
  "id": "/subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW",
```

`"id":` の後の値は、メモ帳などのテキスト エディターにコピーしてください。そうすることで、接続の作成時に簡単に貼り付けることができます。 

```
"id": "/subscriptions/<subscription ID>/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW"
```

#### <a name="2-get-the-resource-id-of-site5"></a>2.Site5 のリソース ID を取得する

次のコマンドの出力を使用して、Site5 のリソース ID を取得します。

```azurecli
az network local-gateway show -n Site5 -g TestBGPRG5
```

#### <a name="3-create-the-testvnet1-to-site5-connection"></a>3.TestVNet1-to-Site5 接続を作成する

この手順では、TestVNet1 から Site5 への接続を作成します。 既に説明したように、同じ Azure VPN ゲートウェイで BGP 接続と BGP 以外の接続の両方を混在させることはできません。 接続プロパティで BGP を有効にしない限り、両方のゲートウェイで BGP パラメーターが既に構成されていても、Azure はこの接続の BGP を有効にしません。 サブスクリプション ID を自分の ID に置換します。

```azurecli
az network vpn-connection create -n VNet1ToSite5 -g TestBGPRG1 --vnet-gateway1 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW --enable-bgp -l eastus --shared-key "abc123" --local-gateway2 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG5/providers/Microsoft.Network/localNetworkGateways/Site5 --no-wait
```

この演習のために、次の例ではオンプレミスの VPN デバイスの BGP 構成セクションに入力するパラメーターの一覧を表示します。

```
Site5 ASN            : 65050
Site5 BGP IP         : 10.52.255.254
Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
Azure VNet ASN       : 65010
Azure VNet BGP IP    : 10.12.255.30
Static route         : Add a route for 10.12.255.30/32, with nexthop being the VPN tunnel interface on your device
eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

数分後に接続が確立されます。 IPsec 接続が確立されると、BGP ピアリング セッションが始まります。

## <a name ="v2vbgp"></a>BGP を使用して VNet 間接続を確立する

このセクションでは、次の図に示すように、BGP を使用して VNet 間接続を追加します。 

![BGP for VNet-to-VNet](./media/vpn-gateway-bgp-resource-manager-ps/bgp-vnet2vnet.png)

以下の手順は、前のセクションで説明した手順の続きです。 BGP で TestVNet1 と VPN ゲートウェイを作成して構成するには、「[VPN ゲートウェイに対して BGP を有効にする](#enablebgp)」セクションが完了している必要があります。

### <a name="step-1-create-testvnet2-and-the-vpn-gateway"></a>手順 1:TestVNet2 と VPN ゲートウェイを作成する

新しい仮想ネットワークである TestVNet2 の IP アドレス空間がどの VNet 範囲とも重ならないようにすることが重要です。

この例では、仮想ネットワークは同じサブスクリプションに属しています。 異なるサブスクリプション間に VNet 間の接続を設定できます。 詳しくは、[VNet 間の接続の構成](vpn-gateway-howto-vnet-vnet-cli.md)に関する記事をご覧ください。 接続の作成時に BGP を有効にするには、必ず `-EnableBgp $True` を追加してください。

#### <a name="1-create-a-new-resource-group"></a>1.新しいリソース グループを作成する

```azurecli
az group create -n TestBGPRG2 -l westus
```

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2.新しいリソース グループに TestVNet2 を作成する

1 番目のコマンドは、フロントエンド アドレス空間と、FrontEnd サブネットを作成します。 2 番目のコマンドでは、BackEnd サブネット用に追加のアドレス空間を作成します。 3 番目と 4 番目のコマンドは、BackEnd サブネットと GatewaySubnet を作成します。

```azurecli
az network vnet create -n TestVNet2 -g TestBGPRG2 --address-prefix 10.21.0.0/16 -l westus --subnet-name FrontEnd --subnet-prefix 10.21.0.0/24 
 
az network vnet update -n TestVNet2 --address-prefixes 10.21.0.0/16 10.22.0.0/16 -g TestBGPRG2 
 
az network vnet subnet create --vnet-name TestVNet2 -n BackEnd -g TestBGPRG2 --address-prefix 10.22.0.0/24 
 
az network vnet subnet create --vnet-name TestVNet2 -n GatewaySubnet -g TestBGPRG2 --address-prefix 10.22.255.0/27
```

#### <a name="3-create-the-public-ip-address"></a>3.パブリック IP アドレスの作成

パブリック IP アドレスを要求します。 仮想ネットワーク用に作成した VPN ゲートウェイにパブリック IP アドレスが割り当てられます。

```azurecli
az network public-ip create -n GWPubIP2 -g TestBGPRG2 --allocation-method Dynamic
```

#### <a name="4-create-the-vpn-gateway-with-the-as-number"></a>4.AS 番号で VPN ゲートウェイを作成する

TestVNet2 用の仮想ネットワーク ゲートウェイを作成します。 Azure VPN ゲートウェイでは既定の ASN をオーバーライドする必要があります。 BGP とトランジット ルーティングを有効にするために、接続された仮想ネットワークの ASN はそれぞれ異なっている必要があります。
 
```azurecli
az network vnet-gateway create -n VNet2GW -l westus --public-ip-address GWPubIP2 -g TestBGPRG2 --vnet TestVNet2 --gateway-type Vpn --sku Standard --vpn-type RouteBased --asn 65020 --no-wait
```

### <a name="step-2-connect-the-testvnet1-and-testvnet2-gateways"></a>手順 2:TestVNet1 と TestVNet2 のゲートウェイを接続する

この手順では、TestVNet1 から Site5 への接続を作成します。 この接続に対して BGP を有効にするには、`--enable-bgp` パラメーターを指定する必要があります。

次の例では、仮想ネットワーク ゲートウェイとローカル ネットワーク ゲートウェイがそれぞれ異なるリソース グループにあります。 ゲートウェイがそれぞれ異なるリソース グループに属している場合は、仮想ネットワーク間の接続を設定するために、2 つのゲートウェイのリソース ID 全体を指定する必要があります。 

#### <a name="1-get-the-resource-id-of-vnet1gw"></a>1.VNet1GW のリソース ID を取得する 

次のコマンドの出力から、VNet1GW のリソース ID を取得します。

```azurecli
az network vnet-gateway show -n VNet1GW -g TestBGPRG1
```

#### <a name="2-get-the-resource-id-of-vnet2gw"></a>2.VNet2GW のリソース ID を取得する

次のコマンドの出力から、VNet2GW のリソース ID を取得します。

```azurecli
az network vnet-gateway show -n VNet2GW -g TestBGPRG2
```

#### <a name="3-create-the-connections"></a>3.接続を作成する

TestVNet1 から TestVNet2 への接続と、TestVNet2 から TestVNet1 への接続を作成します。 サブスクリプション ID を自分の ID に置換します。

```azurecli
az network vpn-connection create -n VNet1ToVNet2 -g TestBGPRG1 --vnet-gateway1 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW --enable-bgp -l eastus --shared-key "efg456" --vnet-gateway2 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG2/providers/Microsoft.Network/virtualNetworkGateways/VNet2GW
```

```azurecli
az network vpn-connection create -n VNet2ToVNet1 -g TestBGPRG2 --vnet-gateway1 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG2/providers/Microsoft.Network/virtualNetworkGateways/VNet2GW --enable-bgp -l westus --shared-key "efg456" --vnet-gateway2 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW
```

> [!IMPORTANT]
> "*両方*" の接続で BGP を有効にしてください。
> 
> 

これらの手順を完了すると、数分後に接続が確立します。 BGP ピアリング セッションは、VNet 間の接続が完了すると有効になります。

## <a name="next-steps"></a>次の手順

接続が完成したら、仮想ネットワークに仮想マシンを追加することができます。 手順については、[仮想マシンの作成](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関するページをご覧ください。
