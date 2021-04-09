---
title: 'チュートリアル - オンプレミス ネットワークを仮想ネットワークに接続する: Azure portal'
description: ポータルを使用して、オンプレミス ネットワークから Azure 仮想ネットワークへのパブリック インターネット経由のサイト間 VPN Gateway IPsec 接続を作成します。
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.author: cherylmc
ms.service: vpn-gateway
ms.topic: tutorial
ms.date: 12/04/2020
ms.openlocfilehash: ccb43c3e7efb9289450ad9a71c003f54e5362b66
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98945201"
---
# <a name="tutorial-create-a-site-to-site-connection-in-the-azure-portal"></a>チュートリアル:Azure Portal でサイト間接続を作成する

Azure VPN ゲートウェイは、お客様のオンプレミスと Azure 間のクロスプレミス接続を提供します。 このチュートリアルでは、Azure portal を使用して、オンプレミス ネットワークから VNet へのサイト間 VPN Gateway 接続を作成する方法について説明します。 この構成は、[Azure PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md) または [Azure CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md) を使用して作成することもできます。

:::image type="content" source="./media/tutorial-site-to-site-portal/diagram.png" alt-text="クロスプレミスのサイト間 VPN Gateway 接続の図":::

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 仮想ネットワークの作成
> * VPN ゲートウェイの作成
> * ローカル ネットワーク ゲートウェイの作成
> * VPN 接続を作成する
> * 接続を確認する
> * 仮想マシンへの接続

## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションが含まれる Azure アカウント。 ない場合は、[無料で作成](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)してください。
* 互換性のある VPN デバイスがあり、デバイスを構成できる人員がいることを確認します。 互換性のある VPN デバイスとデバイスの構成の詳細については、[VPN デバイスの概要](vpn-gateway-about-vpn-devices.md)に関する記事を参照してください。
* VPN デバイスの外部接続用パブリック IPv4 アドレスがあることを確認します。
* オンプレミス ネットワーク構成の IP アドレス範囲を把握していない場合は、詳細な情報を把握している担当者と協力して作業を行ってください。 この構成を作成する場合は、Azure がオンプレミスの場所にルーティングする IP アドレス範囲のプレフィックスを指定する必要があります。 オンプレミス ネットワークのサブネットと接続先の仮想ネットワーク サブネットが重複しないようにしなければなりません。

## <a name="create-a-virtual-network"></a><a name="CreatVNet"></a>仮想ネットワークの作成

次の値を使用して、仮想ネットワーク (VNet) を作成します。

* **[リソース グループ]:** TestRG1
* **[名前]:** VNet1
* **[リージョン]:** (米国) 米国東部
* **IPv4 アドレス空間:** 10.1.0.0/16
* **サブネット名:** FrontEnd
* **サブネット アドレス空間:** 10.1.0.0/24

[!INCLUDE [About cross-premises addresses](../../includes/vpn-gateway-cross-premises.md)]

[!INCLUDE [Create a virtual network](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="create-a-vpn-gateway"></a><a name="VNetGateway"></a>VPN ゲートウェイの作成

この手順では、VNet の仮想ネットワーク ゲートウェイを作成します。 選択したゲートウェイ SKU によっては、ゲートウェイの作成に 45 分以上かかる場合も少なくありません。

### <a name="about-the-gateway-subnet"></a>ゲートウェイ サブネットについて

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

### <a name="create-the-gateway"></a>ゲートウェイを作成する

次の値を使用して VPN ゲートウェイを作成します。

* **[名前]:** VNet1GW
* **[リージョン]:** 米国東部
* **ゲートウェイの種類:** VPN
* **VPN の種類:** ルート ベース
* **SKU:** VpnGw1
* **世代:** Generation1
* **仮想ネットワーク:** VNet1
* **ゲートウェイ サブネットのアドレス範囲:** 10.1.255.0/27
* **[パブリック IP アドレス]** : 新規作成
* **パブリック IP アドレス名:** VNet1GWpip
* **アクティブ/アクティブ モードの有効化:** 無効
* **[Configure BGP]\(BGP の構成\):** 無効

[!INCLUDE [Create a vpn gateway](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

[!INCLUDE [NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="view-the-public-ip-address"></a><a name="view"></a>パブリック IP アドレスの表示

ゲートウェイのパブリック IP アドレスは、ご利用のゲートウェイの **[概要]** ページで確認できます。

:::image type="content" source="./media/tutorial-create-gateway-portal/address.png" alt-text="[概要] ページ":::

パブリック IP アドレス オブジェクトに関する詳しい情報を表示するには、 **[パブリック IP アドレス]** の横にある名前 (または IP アドレス) リンクをクリックします。

## <a name="create-a-local-network-gateway"></a><a name="LocalNetworkGateway"></a>ローカル ネットワーク ゲートウェイの作成

ローカル ネットワーク ゲートウェイは、ルーティング目的でオンプレミスの場所 (サイト) を表す、特定のオブジェクトです。 サイトに Azure が参照できる名前を付け、接続を作成するオンプレミス VPN デバイスの IP アドレスを指定します。 また、VPN ゲートウェイを介して VPN デバイスにルーティングされる IP アドレスのプレフィックスも指定します。 指定するアドレスのプレフィックスは、オンプレミス ネットワークのプレフィックスです。 オンプレミスのネットワークが変更された場合、または VPN デバイスのパブリック IP アドレスを変更する必要がある場合、これらの値を後で簡単に更新できます。

次の値を使用して、ローカル ネットワーク ゲートウェイを作成します。

* **[名前]:** Site1
* **[リソース グループ]:** TestRG1
* **[場所]:** 米国東部

[!INCLUDE [Add a local network gateway](../../includes/vpn-gateway-add-local-network-gateway-portal-include.md)]

## <a name="configure-your-vpn-device"></a><a name="VPNDevice"></a>VPN デバイスの構成

オンプレミス ネットワークとのサイト間接続には VPN デバイスが必要です。 この手順では、VPN デバイスを構成します。 VPN デバイスを構成する場合は、次の値が必要です。

* 共有キー。 サイト間 VPN 接続を作成するときにも、これと同じ共有キーを指定します。 ここで紹介している例では、基本的な共有キーを使用しています。 実際には、もっと複雑なキーを生成して使用することをお勧めします。
* 仮想ネットワーク ゲートウェイのパブリック IP アドレス。 パブリック IP アドレスは、Azure Portal、PowerShell、または CLI を使用して確認できます。 Azure portal を使用して VPN ゲートウェイのパブリック IP アドレスを調べるには、 **[仮想ネットワーク ゲートウェイ]** に移動し、該当するゲートウェイの名前を選択します。

[!INCLUDE [Configure a VPN device](../../includes/vpn-gateway-configure-vpn-device-include.md)]

## <a name="create-a-vpn-connection"></a><a name="CreateConnection"></a>VPN 接続を作成する

仮想ネットワーク ゲートウェイとオンプレミス VPN デバイスとの間にサイト間 VPN 接続を作成します。

次の値を使用して接続を作成します。

* **ローカル ネットワーク ゲートウェイ名:** Site1
* **接続名:** VNet1toSite1
* **共有キー:** この例では、abc123 を使用します。 ただし、お使いの VPN ハードウェアと互換性があれば何を使用してもかまいません。 重要なことは、接続の両側で値が一致していることです。

[!INCLUDE [Add a site-to-site connection](../../includes/vpn-gateway-add-site-to-site-connection-portal-include.md)]

## <a name="verify-the-vpn-connection"></a><a name="VerifyConnection"></a>VPN 接続の確認

[!INCLUDE [Verify the connection](../../includes/vpn-gateway-verify-connection-portal-include.md)]

## <a name="connect-to-a-virtual-machine"></a><a name="connectVM"></a>仮想マシンへの接続

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm.md)]

## <a name="optional-steps"></a>省略可能な手順

### <a name="add-additional-connections-to-the-gateway"></a><a name="addconnect"></a>ゲートウェイに接続を追加する

他の接続とアドレス空間が重複しなければ、新たに接続を追加することができます。

1. 新たに接続を追加するには、VPN ゲートウェイに移動し、 **[接続]** を選択して [接続] ページを開きます。
1. **[+追加]** を選択して接続を追加します。 "VNet 間" (別の VNet ゲートウェイに接続する場合) または "サイト対サイト" を考慮して接続の種類を調整します。
1. "サイト対サイト" を使用して接続している場合で、かつまだ接続先となるサイトのローカル ネットワーク ゲートウェイを作成していない場合は、新たに作成することができます。
1. 使用する共有キーを指定し、 **[OK]** を選択して接続を作成します。

### <a name="resize-a-gateway-sku"></a><a name="resize"></a>ゲートウェイの SKU のサイズ変更

ゲートウェイの SKU の変更とサイズ変更には、一定のルールがあります。 このセクションでは、SKU のサイズを変更します。 詳細については、[ゲートウェイの設定 (SKU のサイズ変更または変更)](vpn-gateway-about-vpn-gateway-settings.md#resizechange) に関するセクションを参照してください。

[!INCLUDE [resize a gateway](../../includes/vpn-gateway-resize-gw-portal-include.md)]

### <a name="reset-a-gateway"></a><a name="reset"></a>ゲートウェイをリセットする

1 つ以上のサイト間 VPN トンネルのクロスプレミス VPN 接続が失われた場合、Azure VPN Gateway をリセットすることによって解決できる場合があります。 この状況では、オンプレミスの VPN デバイスがすべて正しく機能していても、Azure VPN Gateway との間で IPsec トンネルを確立することができません。

[!INCLUDE [reset a gateway](../../includes/vpn-gateway-reset-gw-portal-include.md)]

### <a name="additional-configuration-considerations"></a><a name="additional"></a>構成に関するその他の考慮事項

S2S 構成はさまざまな方法でカスタマイズできます。 詳細については、次の記事を参照してください。

* BGP の詳細については、[BGP の概要](vpn-gateway-bgp-overview.md)に関する記事と [BGP の構成方法](vpn-gateway-bgp-resource-manager-ps.md)に関する記事を参照してください。
* 強制トンネリングについては、[強制トンネリング](vpn-gateway-forced-tunneling-rm.md)に関する記事を参照してください。
* 高可用性のアクティブ/アクティブ接続については、「[高可用性のクロスプレミス接続および VNet 間接続](vpn-gateway-highlyavailable.md)」を参照してください。
* 仮想ネットワーク内のリソースへのネットワーク トラフィックを制限する方法については、「[ネットワークのセキュリティ](../virtual-network/network-security-groups-overview.md)」を参照してください。
* Azure がトラフィックを Azure、オンプレミス、インターネット リソースの間でどのようにルーティングするかについては、「[仮想ネットワーク トラフィックのルーティング](../virtual-network/virtual-networks-udr-overview.md)」を参照してください。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

今後このアプリケーションを使用しない場合、または次のチュートリアルに進む場合は、次の手順に従ってリソースを削除してください。

1. ポータルの上部にある **検索** ボックスに、お使いのリソース グループの名前を入力し、検索結果からそれを選択します。

1. **[リソース グループの削除]** を選択します。

1. **[リソース グループ名を入力してください]** に、お使いのリソース グループを入力し、 **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

サイト間接続を構成したら、同じゲートウェイにポイント対サイト接続を追加できます。

> [!div class="nextstepaction"]
> [ポイント対サイト VPN 接続](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
