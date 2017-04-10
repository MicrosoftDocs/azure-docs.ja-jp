---
title: "Azure VPN ゲートウェイを使用した BGP の概要 | Microsoft Docs"
description: "この記事では、Azure VPN ゲートウェイを使用した BGP の概要を説明します。"
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: 
tags: 
ms.assetid: f8c3985c-c128-4f34-835c-0e88742bf36e
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/12/2017
ms.author: yushwang
translationtype: Human Translation
ms.sourcegitcommit: 5961c73c3f147ab6b3eca4e9d920003df1ca2c82
ms.openlocfilehash: 298eab8ad562cfb32edf8609ac29052dc403f406
ms.lasthandoff: 01/14/2017


---
# <a name="overview-of-bgp-with-azure-vpn-gateways"></a>Azure VPN ゲートウェイを使用した BGP の概要
この記事では、Azure VPN ゲートウェイの BGP (ボーダー ゲートウェイ プロトコル) サポートの概要を説明します。

BGP は、2 つ以上のネットワーク間でルーティングと到達可能性の情報を交換するためにインターネット上で広く使用されている標準のルーティング プロトコルです。 Azure Virtual Networks のコンテキストでは、Azure VPN ゲートウェイとオンプレミスの VPN デバイス (BGP ピアまたは BGP 近隣ノードと呼ばれる) が BGP を使用して "ルート" を交換します。これによって、関連するゲートウェイまたはルーターの可用性と、BGP のプレフィックスが到達できる可能性に関する情報が両方のゲートウェイに伝達されます。 また、BGP では、BGP ゲートウェイが特定の BGP ピアから学習したルートを他のすべての BGP ピアに伝達することで、複数のネットワークでトランジット ルーティングを行うこともできます。 

## <a name="why-use-bgp"></a>BGP を使用する理由
BGP は、Azure のルートベースの VPN ゲートウェイで使用できるオプションの機能です。 この機能を有効にするには、オンプレミスの VPN デバイスが BGP をサポートしていることを事前に確認する必要があります。 BGP を使用せずに Azure VPN ゲートウェイとオンプレミスの VPN デバイスを使用することもできます。 これ (BGP を使用しない方法) は静的ルートを使用するのと同等で、BGP を使用する方法はネットワークと ** Azure の間で動的ルーティングを使用するのに相当します。

BGP の使用に関して、いくつかの利点と新しい機能があります。

### <a name="support-automatic-and-flexible-prefix-updates"></a>自動的で柔軟なプレフィックスの更新のサポート
BGP を使用する場合は、IPsec S2S VPN トンネル経由で特定の BGP ピアに最小限のプレフィックスを宣言するだけで済みます。 オンプレミス VPN デバイスの BGP ピア IP アドレスのホスト プレフィックス (/32) のような小さいプレフィックスも指定できます。 どのオンプレミス ネットワーク プレフィックスを Azure にアドバタイズして、Azure 仮想ネットワーク (VNet) にアクセスを許可するかを制御できます。

また、大規模なプライベート IP アドレス空間 (例: 10.0.0.0/8) など、VNet アドレス プレフィックスがいくつか含まれた大規模なプレフィックスをアドバタイズすることもできます。 ただし、このプレフィックスを VNet プレフィックスのいずれかと同じにすることはできませんので注意してください。 VNet プレフィックスと同一のルートは拒否されます。

### <a name="support-multiple-tunnels-between-a-vnet-and-an-on-premises-site-with-automatic-failover-based-on-bgp"></a>BGP ベースの自動フェールオーバーを使用した VNet とオンプレミス サイト間の複数のトンネルのサポート
同じ場所にある複数のオンプレミス VPN デバイスと Azure VNet の間に複数の接続を確立できます。 この機能により、2 つのネットワークの間に複数のトンネル (パス) がアクティブ/アクティブ構成で提供されます。 トンネルの 1 つが切断された場合は、対応するルートが BGP によって無効にされ、トラフィックが残りのトンネルに自動的に移行されます。

次の図は、この高可用性セットアップの単純な例を示しています。

![Multiple active paths](./media/vpn-gateway-bgp-overview/multiple-active-tunnels.png)

### <a name="support-transit-routing-between-your-on-premises-networks-and-multiple-azure-vnets"></a>オンプレミスのネットワークと複数の Azure Vnet の間のトランジット ルーティングのサポート
BGP では、接続が直接か間接的かにかかわらず、複数のゲートウェイが異なるネットワークからプレフィックスを学習して伝達することができます。 これにより、オンプレミス サイト間または複数の Azure Virtual Networks 間で Azure VPN ゲートウェイを使用したトランジット ルーティングを実行できます。

次の図は、Microsoft ネットワーク内の Azure VPN ゲートウェイを通じて 2 つのオンプレミス ネットワーク間でトラフィックをやり取りできる複数のパスを使用したマルチホップ トポロジの例を示しています。

![Multi-hop transit](./media/vpn-gateway-bgp-overview/full-mesh-transit.png)

## <a name="bgp-faq"></a>BGP のよくある質問
[!INCLUDE [vpn-gateway-bgp-faq-include](../../includes/vpn-gateway-bpg-faq-include.md)]

## <a name="next-steps"></a>次のステップ
クロスプレミス接続と VNet 間接続向けに BGP を構成する手順については、 [Azure VPN ゲートウェイでの BGP の使用](vpn-gateway-bgp-resource-manager-ps.md) に関する記事を参照してください。


