---
title: インクルード ファイル
description: インクルード ファイル
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 03/19/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 551b1d4924c354eb6fd501c1b82ed306a7ea4822
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2019
ms.locfileid: "58305125"
---
### <a name="what-is-expressroute-global-reach"></a>ExpressRoute Global Reach とは何ですか。

ExpressRoute Global Reach は、Microsoft のグローバル ネットワークを通って ExpressRoute サービスを使用してオンプレミスのネットワークを接続する Azure サービスです。 たとえば、カリフォルニア州シリコン バレーの ExpressRoute に接続されているプライベート データ センターがあり、テキサス州ダラスの ExpressRoute に接続されている別のプライベート データ センターがある場合、ExpressRoute Global Reach を使用すれば、2 つの ExpressRoute 接続を通してこれらのプライベート データ センターを互いに接続でき、データセンター間のトラフィックは Microsoft のネットワーク バックボーンを通って行き来します。

### <a name="how-do-i-enable-or-disable-expressroute-global-reach"></a>ExpressRoute Global Reach はどのようにして有効または無効にするのですか。

ExpressRoute Global Reach は、ExpressRoute 回線を互いに接続することで有効にします。 この機能を無効にするには、この回線を切断します。 [構成](../articles/expressroute/expressroute-howto-set-global-reach.md)を確認してください。

### <a name="do-i-need-expressroute-premium-for-expressroute-global-reach"></a>ExpressRoute Global Reach 用の ExpressRoute Premium が必要ですか。

ExpressRoute 回線が同じ地理的リージョンにある場合は、それらを互いに接続するために ExpressRoute Premium は必要ありません。 2 つの ExpressRoute 回線が異なる地理的リージョンにある場合、それらの間の接続を有効にするためには両方の回線に ExpressRoute Premium が必要です。 

### <a name="how-will-i-be-charged-for-expressroute-global-reach"></a>ExpressRoute Global Reach にはどのように課金されますか。

ExpressRoute は、オンプレミスのネットワークから Microsoft クラウド サービスへの接続を可能にします。 ExpressRoute Global Reach は、Microsoft のグローバル ネットワークを活用して、既存の ExpressRoute 回線を介した独自のオンプレミスのネットワーク間の接続を可能にします。 ExpressRoute Global Reach は既存の ExpressRoute サービスとは別個に課金されます。 この機能をそれぞれの ExpressRoute 回線で有効にするためのアドオンの料金があります。 ExpressRoute Global Reach によって有効にしたオンプレミスのネットワーク間のトラフィックは、送信元ではエグレス レートに対して課金され、送信先ではイングレス レートに対して課金されます。 これらのレートは、回線が配置されているゾーンに基づいています。 <pricing page>を参照してください

### <a name="where-is-expressroute-global-reach-supported"></a>ExpressRoute Global Reach はどこでサポートされていますか。

ExpressRoute Global Reach は[選ばれた国や場所](../articles/expressroute/expressroute-global-reach.md)でサポートされています。 ExpressRoute 回線は、これらの国や場所のピアリング拠点に作成する必要があります。

### <a name="i-have-more-than-two-on-premises-networks-each-connected-to-an-expressroute-circuit-can-i-enable-expressroute-global-reach-to-connect-all-of-my-on-premises-networks-together"></a>3 つ以上のオンプレミスのネットワークがあり、それぞれが ExpressRoute 回線に接続されています。 ExpressRoute Global Reach を使用して、オンプレミスのすべてのネットワークを互いに接続することはできますか。

はい、できます。ただし、サポートされている国にある回線に限ります。 一度に 2 つの ExpressRoute 回線を接続する必要があります。 完全なメッシュ ネットワークを作成するには、すべての回線ペアを列挙し、この構成を繰り返す必要があります。 

### <a name="can-i-enable-expressroute-global-reach-between-two-expressroute-circuits-at-the-same-peering-location"></a>同じピアリングの場所にある 2 つの ExpressRoute 回線の間で ExpressRoute Global Reach を有効にすることはできますか。

いいえ。 2 つの回線は異なるピアリングの場所からの回線である必要があります。 サポートされている国の都市に複数の ExpressRoute ピアリングの場所がある場合は、その都市の異なるピアリングの場所に作成された ExpressRoute 回線を互いに接続することができます。 

### <a name="if-expressroute-global-reach-is-enabled-between-circuit-x-and-circuit-y-and-between-circuit-y-and-circuit-z-will-my-on-premises-networks-connected-to-circuit-x-and-circuit-z-talk-to-each-other-via-microsofts-network"></a>ExpressRoute Global Reach が、回線 X と Y の間、および回線 Y と回線 Z の間で有効になっている場合、回線 X と回線 Z に接続されているオンプレミスのネットワークは Microsoft のネットワーク経由で互いに通信しますか。

いいえ。 オンプレミスのネットワークのいずれか 2 つの間の接続を有効にするには、対応する ExpressRoute 回線を明示的に接続する必要があります。 上記の例では、回線 X と回線 Z を接続する必要があります。 

### <a name="what-is-the-network-throughput-i-can-expect-between-my-on-premises-networks-after-i-enable-expressroute-global-reach"></a>ExpressRoute Global Reach を有効にした後でオンプレミスのネットワーク間では、どのようなネットワーク スループットを期待できますか。

ExpressRoute Global Reach によって有効にしたオンプレミスのネットワーク間のネットワーク スループットは、2 つの ExpressRoute 回線のうち、小さい方によって制限されます。 オンプレミスから Azure へのトラフィックとオンプレミス間のトラフィックでは同じ回線を共有しているため、同じ帯域幅の上限が適用されます。 

### <a name="with-expressroute-global-reach-what-are-the-limits-on-the-number-of-routes-i-can-advertise-and-the-number-of-routes-i-will-receive"></a>ExpressRoute Global Reach では、アドバタイズできるルートの数と受信できるルートの数はいくつに制限されていますか。

Azure プライベート ピアリングで Microsoft にアドバタイズできるルートの数は、Standard 回線では 4000、Premium 回線では 10000 のままです。 Azure プライベート ピアリングで Microsoft から受信するルートの数は、Azure 仮想ネットワークのルートと ExpressRoute Global Reach 経由で接続されているその他のオンプレミス ネットワークからのルートの合計です。 オンプレミスのルーターに適切な最大プレフィックス制限が設定されていることを確認してください。 

### <a name="what-is-the-sla-for-expressroute-global-reach"></a>ExpressRoute Global Reach の SLA は何ですか。

ExpressRoute Global Reach では、通常の ExpressRoute サービスと同じ[可用性 SLA](https://azure.microsoft.com/support/legal/sla/expressroute/v1_3/) が提供されます。
