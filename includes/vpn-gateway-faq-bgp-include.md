---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/12/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 7c1f3fc7861f5e1b895423d502218b9b07302c1c
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2019
ms.locfileid: "67659848"
---
### <a name="is-bgp-supported-on-all-azure-vpn-gateway-skus"></a>BGP はすべての Azure VPN Gateway SKU でサポートされていますか。
いいえ、BGP は Azure **VpnGw1**、**VpnGw2**、**VpnGw3**、**Standard**、**HighPerformance** の各 VPN ゲートウェイでサポートされています。 **Basic** SKU はサポートされていません。

### <a name="can-i-use-bgp-with-azure-policy-based-vpn-gateways"></a>Azure のポリシーベースの VPN ゲートウェイでは BGP を使用できますか。
いいえ、BGP は、ルートベースの VPN ゲートウェイでのみサポートされています。

### <a name="can-i-use-private-asns-autonomous-system-numbers"></a>プライベート ASN (自律システム番号) は使用できますか。
はい、オンプレミスのネットワークと Azure 仮想ネットワークの両方に対して独自のパブリック ASN またはプライベート ASN を使用できます。

### <a name="can-i-use-32-bit-asns-autonomous-system-numbers"></a>32 ビットの ASN (自律システム番号) は使用できますか。
いいえ。現時点で Azure VPN Gateway がサポートしているのは 16 ビットの ASN となります。

### <a name="are-there-asns-reserved-by-azure"></a>Azure によって予約済みの ASN はありますか。
はい、次の ASN は、内外両方のピアリング用に Azure によって予約されています。

* パブリック ASN: 8074、8075、12076
* プライベート ASN: 65515、65517、65518、65519、65520

これらの ASN は、Azure VPN ゲートウェイに接続する際にオンプレミスの VPN デバイスには指定できません。

### <a name="are-there-any-other-asns-that-i-cant-use"></a>使用できないその他の ASN はありますか。
はい。以下の ASN は [IANA によって予約済み](http://www.iana.org/assignments/iana-as-numbers-special-registry/iana-as-numbers-special-registry.xhtml)であり、Azure VPN Gateway では構成できません。

23456、64496 ～ 64511、65535 ～ 65551、および 429496729

### <a name="can-i-use-the-same-asn-for-both-on-premises-vpn-networks-and-azure-vnets"></a>オンプレミスの VPN ネットワークと Azure Vnet の両方に同じ ASN を使用できますか。
いいえ、BGP を使用して接続している場合は、オンプレミスのネットワークと Azure Vnet に異なる ASN を割り当てる必要があります。 Azure VPN Gateway には、クロスプレミス接続向けに BGP が有効になっているかどうかにかかわらず、ASN の既定値として 65515 が割り当てられています。 VPN ゲートウェイを作成する際に異なる ASN を割り当てて既定値をオーバーライドするか、ゲートウェイの作成後に ASN を変更することができます。 対応する Azure ローカル ネットワーク ゲートウェイにオンプレミスの ASN を割り当てる必要があります。

### <a name="what-address-prefixes-will-azure-vpn-gateways-advertise-to-me"></a>Azure VPN ゲートウェイはどのようなアドレス プレフィックスをアドバタイズしますか。
Azure VPN ゲートウェイでは、オンプレミスの BGP デバイスに次のルートをアドバタイズします。

* VNet のアドレス プレフィックス
* Azure VPN ゲートウェイに接続されている各ローカル ネットワーク ゲートウェイのアドレス プレフィックス
* Azure VPN Gateway に接続されている他の BGP ピアリング セッションから学習したルート。ただし、**VNet プレフィックスと重複している既定のルートは除外**されます。

### <a name="how-many-prefixes-can-i-advertise-to-azure-vpn-gateway"></a>何個のプレフィックスを Azure VPN ゲートウェイにアドバタイズできますか。
最大 4,000 プレフィックスがサポートされています。 プレフィックスの数がこの制限を超えると、BGP セッションは切断されます。

### <a name="can-i-advertise-default-route-00000-to-azure-vpn-gateways"></a>Azure VPN ゲートウェイへの既定のルート (0.0.0.0/0) をアドバタイズすることはできますか。
はい。

その場合、すべてのオンプレミス サイトへの VNet エグレス トラフィックが強制され、インターネットから VM への RDP や SSH など、インターネットからのパブリックな通信を VNet VM が直接受信できなくなることに注意してください。

### <a name="can-i-advertise-the-exact-prefixes-as-my-virtual-network-prefixes"></a>自分の仮想ネットワーク プレフィックスとまったく同じプレフィックスをアドバタイズすることはできますか。

いいえ、お使いの仮想ネットワーク アドレス プレフィックスのいずれかと同じプレフィックスのアドバタイズは、Azure プラットフォームによってブロックされるか、フィルター処理されます。 ただし、仮想ネットワークの内部に存在するアドレス空間のスーパーセットであるプレフィックスをアドバタイズすることができます。 

たとえば、仮想ネットワークでアドレス空間 10.0.0.0/16 を使用した場合、10.0.0.0/8 をアドバタイズすることができます。 しかし、10.0.0.0/16 や 10.0.0.0/24 をアドバタイズすることはできません。

### <a name="can-i-use-bgp-with-my-vnet-to-vnet-connections"></a>VNet 間の接続で BGP を使用できますか。
はい、クロスプレミス接続と VNet 間接続の両方で BGP を使用できます。

### <a name="can-i-mix-bgp-with-non-bgp-connections-for-my-azure-vpn-gateways"></a>Azure VPN ゲートウェイで BGP 接続を BGP 以外の接続と混在させることはできますか。
はい、同じ Azure VPN ゲートウェイに対して BGP 接続と非 BGP 接続の両方を混在させることができます。

### <a name="does-azure-vpn-gateway-support-bgp-transit-routing"></a>Azure VPN ゲートウェイでは、BGP トランジット ルーティングをサポートしていますか。
はい。BGP トランジット ルーティングはサポートされています。ただし、Azure VPN Gateway は既定のルートを他の BGP ピアにアドバタイズ**しません**。 複数の Azure VPN ゲートウェイでトランジット ルーティングを有効にするには、中間にあるすべての VNet 接続で BGP を有効にする必要があります。 詳細については、[BGP](../articles/vpn-gateway/vpn-gateway-bgp-overview.md) に関する記事を参照してください。

### <a name="can-i-have-more-than-one-tunnel-between-azure-vpn-gateway-and-my-on-premises-network"></a>Azure VPN ゲートウェイとオンプレミス ネットワークの間で複数のトンネルを確立することはできますか。
はい、Azure VPN ゲートウェイとオンプレミス ネットワークの間に複数の S2S VPN トンネルを確立することができます。 これらのトンネルはすべて Azure VPN ゲートウェイのトンネルの合計数にカウントされる点に注意してください。さらに、両方のトンネルの BGP を有効にする必要があります。

たとえば、Azure VPN ゲートウェイとオンプレミス ネットワークのいずれかの間に 2 つの冗長トンネルがある場合は、Azure VPN ゲートウェイの合計クォータ (Standard の場合は 10、HighPerformance の場合は 30) から 2 つのトンネルが消費されることになります。

### <a name="can-i-have-multiple-tunnels-between-two-azure-vnets-with-bgp"></a>BGP で 2 つの Azure Vnet の間に複数のトンネルを確立することはできますか。
はい。ただし、少なくとも 1 つの仮想ネットワーク ゲートウェイが、アクティブ/アクティブ構成になっている必要があります。

### <a name="can-i-use-bgp-for-s2s-vpn-in-an-expressroutes2s-vpn-co-existence-configuration"></a>ExpressRoute/S2S VPN 共存構成で S2S VPN に BGP を使用することはできますか。
はい。 

### <a name="what-address-does-azure-vpn-gateway-use-for-bgp-peer-ip"></a>Azure VPN ゲートウェイでは、BGP ピア IP にどのようなアドレスが使用されますか。
Azure VPN ゲートウェイは、GatewaySubnet 範囲から 1 つの IP アドレスをアクティブ/スタンバイ VPN ゲートウェイに割り当てるか、2 つの IP アドレスをアクティブ/アクティブ VPN ゲートウェイに割り当てます。 PowerShell (Get-AzVirtualNetworkGateway、"bgpPeeringAddress" プロパティを探してください) を使用して、または Azure portal ([ゲートウェイの構成] ページの [BGP ASN の構成] プロパティの下) で、割り当てられた実際の BGP IP アドレスを取得できます。

### <a name="what-are-the-requirements-for-the-bgp-peer-ip-addresses-on-my-vpn-device"></a>VPN デバイスの BGP ピア IP アドレスに関する要件はどんなものですか。
オンプレミスの BGP ピア アドレスを VPN デバイスのパブリック IP アドレスと **同じにすることはできません** 。 VPN デバイスでは BGP ピア IP に別の IP アドレスを使用してください。 デバイス上のループバック インターフェイスに割り当てられたアドレスを使用することもできますが、APIPA (169.254.x.x) アドレスは使用できません。 この場所を表している、対応するローカル ネットワーク ゲートウェイにこのアドレスを指定します。

### <a name="what-should-i-specify-as-my-address-prefixes-for-the-local-network-gateway-when-i-use-bgp"></a>BGP を使用する際、ローカル ネットワーク ゲートウェイのアドレス プレフィックスとして何を指定する必要がありますか。
Azure のローカル ネットワーク ゲートウェイでは、オンプレミス ネットワークに初期アドレス プレフィックスが指定されます。 BGP を使用する際は、BGP ピア IP アドレスのホスト プレフィックス (/32 プレフィックス) をオンプレミス ネットワークのアドレス空間として割り当てる必要があります。 BGP ピア IP アドレスが 10.52.255.254 の場合は、このオンプレミス ネットワークを表しているローカル ネットワーク ゲートウェイの localNetworkAddressSpace として "10.52.255.254/32" を指定します。 これにより、Azure VPN ゲートウェイで S2S VPN トンネルを経由する BGP セッションが確立されます。

### <a name="what-should-i-add-to-my-on-premises-vpn-device-for-the-bgp-peering-session"></a>BGP ピアリング セッション向けにオンプレミスの VPN デバイスに何を追加する必要がありますか。
IPsec S2S VPN トンネルを示す Azure BGP ピア IP アドレスのホスト ルートを VPN デバイスに追加する必要があります。 たとえば、Azure VPN ピア IP が "10.12.255.30" の場合は、"10.12.255.30" のホスト ルートを VPN デバイスに追加し、対応する IPsec トンネル インターフェイスの次ホップ インターフェイスを指定する必要があります。
