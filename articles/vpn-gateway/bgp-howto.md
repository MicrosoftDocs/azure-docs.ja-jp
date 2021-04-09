---
title: VPN Gateway に BGP を構成する:ポータル
titleSuffix: Azure VPN Gateway
description: この記事では、PowerShell を使用して Azure VPN Gateway で BGP を構成する手順について説明します。
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 03/22/2021
ms.author: yushwang
ms.openlocfilehash: 479a8fac111be6e5b1ae2c6ea21fff801ba26f83
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104863583"
---
# <a name="how-to-configure-bgp-on-azure-vpn-gateways"></a>Azure VPN Gateway で BGP を構成する方法

この記事では、Azure portal を使用して、クロスプレミスのサイト間 (S2S) VPN 接続と VNet 間接続で BGP を有効にする手順について説明します。

## <a name="about-bgp"></a><a name="about"></a>BGP について

BGP は、2 つ以上のネットワーク間でルーティングと到達可能性の情報を交換するためにインターネット上で広く使用されている標準のルーティング プロトコルです。 Azure VPN ゲートウェイとオンプレミスの VPN デバイス (BGP ピアまたは BGP 近隣ノードと呼ばれる) が BGP を使用して "ルート" を交換します。これによって、関連するゲートウェイまたはルーターの可用性と、BGP のプレフィックスが到達できる可能性に関する情報が両方のゲートウェイに伝達されます。 また、BGP では、BGP ゲートウェイが特定の BGP ピアから学習したルートを他のすべての BGP ピアに伝達することで、複数のネットワークでトランジット ルーティングを行うこともできます。

BGP の利点の詳細と BGP を使用する場合の技術面の要件および考慮事項を理解するには、「[BGP と Azure VPN Gateway について](vpn-gateway-bgp-overview.md)」をご覧ください。

## <a name="getting-started"></a>作業の開始

この記事の各パートに従うと、ネットワーク接続で BGP を有効にするための基本的な構成要素を形成できます。 3 つのパートをすべて完了すると、図 1 のようなトポロジが構築されます。

**図 1**

:::image type="content" source="./media/bgp-howto/bgp-crosspremises-v2v.png" alt-text="ネットワークのアーキテクチャと設定を示す図" border="false":::

各部分を組み合わせると、ニーズに合わせて、より複雑でマルチホップの通過ネットワークを構築することができます。

### <a name="prerequisites"></a>前提条件

Azure サブスクリプションを持っていることを確認します。 Azure サブスクリプションをまだお持ちでない場合は、[MSDN サブスクライバーの特典](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)を有効にするか、[無料アカウント](https://azure.microsoft.com/pricing/free-trial/)にサインアップしてください。

## <a name="part-1-configure-bgp-on-the-virtual-network-gateway"></a><a name ="config"></a>パート 1:仮想ネットワーク ゲートウェイで BGP を構成する

このセクションでは、仮想ネットワークの作成と構成、BGP パラメーターを使用した仮想ネットワーク ゲートウェイの作成と構成、Azure BGP ピア IP アドレスの取得を行います。 図 2 は、このセクションの手順を行うときに使用する構成設定を示しています。

**図 2**

:::image type="content" source="./media/bgp-howto/bgp-gateway.png" alt-text="仮想ネットワーク ゲートウェイの設定を示す図" border="false":::

### <a name="1-create-and-configure-testvnet1"></a>1.TestVNet1 の作成と構成

この手順では、TestVNet1 を作成して構成します。 Azure 仮想ネットワークと VPN ゲートウェイを作成して構成するには、[ゲートウェイの作成に関するチュートリアル](./tutorial-create-gateway-portal.md)の手順に従います。 次のスクリーンショットの参照設定を使用します。

* Virtual Network:

   :::image type="content" source="./media/bgp-howto/testvnet-1.png" alt-text="対応するアドレス プレフィックスを使用した TestVNet1":::

* サブネット:

   :::image type="content" source="./media/bgp-howto/testvnet-1-subnets.png" alt-text="TestVNet1 サブネット":::

### <a name="2-create-the-vpn-gateway-for-testvnet1-with-bgp-parameters"></a>2.BGP パラメーターを指定して TestVNet1 の VPN ゲートウェイを作成する

この手順では、対応する BGP パラメーターを使用して VPN ゲートウェイを作成します。

1. Azure portal で、Marketplace から **Virtual Network ゲートウェイ** リソースに移動し、 **[作成]** を選択します。

1. パラメーターを次に示すように入力します。

   :::image type="content" source="./media/bgp-howto/create-gateway-1.png" alt-text="VNG1 の作成":::

1. このページの強調表示されている **[Configure BGP]\(BGP の構成\)** セクションで、次の設定を構成します。

   :::image type="content" source="./media/bgp-howto/create-gateway-1-bgp.png" alt-text="BGP の構成":::

   * BGP 構成セクションを表示するには、 **[Configure BGP]\(BGP の構成\)**  -  **[Enabled]\(有効\)** を選択します。

   * ASN (自律システム番号) を入力します。

   * **[Azure APIPA BGP IP address]\(Azure APIPA BGP IP アドレス\)** フィールドは省略可能です。 オンプレミスの VPN デバイスで、BGP に APIPA アドレスを使用する場合は、Azure で予約されている VPN 用の APIPA アドレス範囲 (**169.254.21.0** から **169.254.22.255**) のアドレスを選択する必要があります。 この例では、169.254.21.11 を使用します。

   * アクティブ/アクティブ VPN ゲートウェイを作成している場合は、BGP セクションに、追加の **2 番目のカスタム Azure APIPA BGP IP アドレス** が表示されます。 許可されている APIPA 範囲 (**169.254.21.0** から **169.254.22.255**) から、別の IP アドレスを選択します。 2 番目の IP アドレスは、1 番目のアドレスとは別のものである必要があります。

   > [!IMPORTANT]
   >
   > * 既定では、Azure により、Azure VPN ゲートウェイの Azure BGP IP アドレスとして、GatewaySubnet プレフィックス範囲のプライベート IP アドレスが自動的に割り当てられます。 オンプレミスの VPN デバイスにより、BGP IP として APIPA アドレス (169.254.0.1 から 169.254.255.254) が使用される場合は、カスタム Azure APIPA BGP アドレスが必要です。 対応するローカル ネットワーク ゲートウェイ リソース (オンプレミス ネットワーク) に、BGP ピア IP として APIPA アドレスがある場合、Azure VPN Gateway により、カスタム APIPA アドレスが選択されます。 ローカル ネットワーク ゲートウェイによって (APIPA ではなく) 通常の IP アドレスが使用される場合は、Azure VPN Gateway によって GatewaySubnet 範囲のプライベート IP アドレスに戻されます。
   >
   > * APIPA BGP アドレスは、オンプレミスの VPN デバイスと、接続されているすべての Azure VPN ゲートウェイの間で重複しないようにする必要があります。
   >
   > * Azure VPN ゲートウェイで APIPA アドレスが使用されている場合、そのゲートウェイによって、APIPA の発信元 IP アドレスで BGP ピアリング セッションが開始されることはありません。 BGP ピアリング接続の開始は、オンプレミスの VPN デバイスによって行われる必要があります。
   >

1. **[確認と作成]** を選択して検証を実行します。 検証に合格したら、 **[作成]** を選択して VPN ゲートウェイをデプロイします。 ゲートウェイを完全に作成してデプロイするには最大で 45 分かかることがあります。 デプロイの状態は、ゲートウェイの [概要] ページで確認できます。

### <a name="3-obtain-the-azure-bgp-peer-ip-addresses"></a>3.Azure BGP ピア IP アドレスを取得する

ゲートウェイが作成されたら、Azure VPN ゲートウェイの BGP ピア IP アドレスを取得できます。 これらのアドレスは、Azure VPN ゲートウェイとの BGP セッションを確立するようにオンプレミスの VPN デバイスを構成するために必要です。

1. 仮想ネットワーク ゲートウェイのリソースに移動し、 **[構成]** ページを選択して、BGP 構成情報が次のスクリーンショットのように表示されることを確認します。 このページで、Azure VPN ゲートウェイのすべての BGP 構成情報を確認できます:ASN、パブリック IP アドレス、および Azure 側の対応する BGP ピア IP アドレス (既定および APIPA)。

   :::image type="content" source="./media/bgp-howto/vnet-1-gw-bgp.png" alt-text="BGP ゲートウェイ":::

1. **[構成]** ページで、次の構成変更を行うことができます。

   * 必要に応じて、ASN または APIPA BGP IP アドレスを更新できます。
   * アクティブ/アクティブ VPN ゲートウェイがある場合、このページには、2 番目の Azure VPN ゲートウェイ インスタンスのパブリック IP アドレス、既定アドレス、APIPA BGP IP アドレスが表示されます。

1. 変更を行った場合は、 **[保存]** を選択して、Azure VPN ゲートウェイの変更をコミットします。

## <a name="part-2-configure-bgp-on-cross-premises-s2s-connections"></a><a name ="crosspremises"></a>パート 2:クロスプレミス サイト間接続で BGP を構成する

クロスプレミス接続を確立するには、オンプレミスの VPN デバイスを表す "*ローカル ネットワーク ゲートウェイ*" と、[サイト間接続の作成](./tutorial-site-to-site-portal.md)に関するページで説明しているように、VPN ゲートウェイをローカル ネットワーク ゲートウェイにつなげる "*接続*" を作成する必要があります。 この記事には、BGP 構成パラメーターを指定するために必要な追加のプロパティが記載されています。

**図 3**

:::image type="content" source="./media/bgp-howto/bgp-crosspremises.png" alt-text="IPsec を示す図" border="false":::

### <a name="1-configure-bgp-on-the-local-network-gateway"></a>1.ローカル ネットワーク ゲートウェイで BGP を構成する

この手順では、ローカル ネットワーク ゲートウェイで BGP を構成します。 例として、次のスクリーンショットを使用します。 このスクリーンショットは、図 3 で指定したパラメーターを使用したローカル ネットワーク ゲートウェイ (Site5) を示しています。

:::image type="content" source="./media/bgp-howto/create-local-bgp.png" alt-text="ローカル ネットワーク ゲートウェイに BGP を構成する":::

#### <a name="important-configuration-considerations"></a>構成に関する重要な考慮事項

* ASN および BGP ピア IP アドレスは、オンプレミスの VPN ルーター構成と一致する必要があります。
* このネットワークに接続するために BGP を使用しようとしている場合のみ、**アドレス空間** を空のままにすることができます。 Azure VPN ゲートウェイにより、対応する IPsec トンネルへの BGP ピア IP アドレスのルートが内部的に追加されます。 Azure VPN ゲートウェイとこの特定のネットワークの間に BGP を使用しようとして **いない** 場合は、この **アドレス空間** の有効なアドレス プレフィックスの一覧を指定 **する必要があります**。
* 必要に応じて、オンプレミスの BGP ピア IP として **APIPA IP アドレス** (169.254. x. x) を使用することもできます。 ただし、Azure VPN ゲートウェイについては、この記事で前述したように、APIPA IP アドレスも指定する必要があります。そうしないと、BGP セッションはこの接続に対して確立できません。
* ローカル ネットワーク ゲートウェイの作成時に BGP 構成情報を入力するか、ローカル ネットワーク ゲートウェイ リソースの **[構成]** ページで BGP 構成を追加または変更することができます。

**例**

この例では、オンプレミスの BGP ピア IP アドレスとして APIPA アドレス (169.254.100.1) を使用します。

:::image type="content" source="./media/bgp-howto/local-apipa.png" alt-text="ローカル ネットワーク ゲートウェイの APIPA と BGP":::

### <a name="2-configure-a-s2s-connection-with-bgp-enabled"></a>2.BGP を有効にしてサイト間接続を構成する

この手順では、BGP を有効にして新しい接続を作成します。 既に接続があり、その接続で BGP を有効にする場合は、[既存の接続を更新する](#update)ことができます。

#### <a name="to-create-a-connection-with-bgp-enabled"></a>BGP を有効にして接続を作成する

BGP を有効にして新しい接続を作成するには、 **[接続の追加]** ページで値を入力し、 **[BGP を有効にする]** オプションをオンにして、この接続で BGP を有効にします。 **[OK]** を選択して、接続を作成します。

:::image type="content" source="./media/bgp-howto/ipsec-connection-bgp.png" alt-text="BGP を使用した IPsec クロスプレミス接続":::

#### <a name="to-update-an-existing-connection"></a><a name ="update"></a>既存の接続を更新する

接続の BGP オプションを変更する場合は、接続リソースの **[構成]** ページに移動し、次の例で強調表示されている **[BGP]** オプションを切り替えます。 **[保存]** を選択してすべての変更を保存します。

:::image type="content" source="./media/bgp-howto/update-bgp.png" alt-text="接続の BGP を更新する":::

## <a name="part-3-configure-bgp-on-vnet-to-vnet-connections"></a><a name ="v2v"></a>パート 3:VNet 間接続で BGP を構成する

VNet 間接続で BGP を有効または無効にする手順は、[パート 2](#crosspremises) のサイト間接続の手順と同じです。 接続の作成時に BGP を有効にするか、既存の VNet 間接続の構成を更新できます。

>[!NOTE] 
>BGP を使用しない VNet 間接続の通信は、接続される 2 つの VNet のみに制限されます。 この 2 つの VNet の他のサイト間または VNet 間接続に転送ルーティング機能を許可するには、BGP を有効にします。
>

コンテキストについては、**図 4** からわかるように、TestVNet2 と TestVNet1 の間の BGP が無効になっていると、TestVNet2 で、オンプレミス ネットワーク Site5 のルートが学習されないため、Site 5 と通信できません。 BGP を有効にすると、図 4 に示すように、3 つのネットワークすべてが IPsec と VNet 間接続を介して通信できるようになります。

**図 4**

:::image type="content" source="./media/bgp-howto/bgp-crosspremises-v2v.png" alt-text="完全なネットワークを示す図" border="false":::

## <a name="next-steps"></a>次のステップ

接続が完成したら、仮想ネットワークに仮想マシンを追加することができます。 手順については、 [仮想マシンの作成](../virtual-machines/windows/quick-create-portal.md) に関するページを参照してください。