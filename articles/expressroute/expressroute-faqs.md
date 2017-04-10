---
title: "Azure ExpressRoute の FAQ | Microsoft Docs"
description: "ExpressRoute の FAQ には、サポートされている Azure サービス、料金、データと接続、SLA、プロバイダーと提供地域、帯域幅、およびその他の技術的な詳細に関する情報が記載されています。"
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: 
ms.assetid: 09b17bc4-d0b3-4ab0-8c14-eed730e1446e
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/29/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: 5b7e2d8df2723f77aa8283d9faab22656198ed47
ms.lasthandoff: 03/30/2017


---
# <a name="expressroute-faq"></a>ExpressRoute の FAQ
## <a name="what-is-expressroute"></a>ExpressRoute とは何ですか。
ExpressRoute は、Microsoft のデータセンターとオンプレミスや共用施設にあるインフラストラクチャの間にプライベート接続を作成できる Azure サービスです。 ExpressRoute 接続はパブリックなインターネットを経由しないため、インターネット経由の一般的な接続に比べて、安全性と信頼性が高く、待機時間も短く、高速です。

### <a name="what-are-the-benefits-of-using-expressroute-and-private-network-connections"></a>ExpressRoute とプライベート ネットワーク接続を使用する利点は何ですか。
ExpressRoute 接続はパブリックなインターネットを経由しないため、インターネット経由の一般的な接続に比べて、安全性と信頼性が高く、待機時間も一貫して短く、高速です。 オンプレミスのデバイスと Azure の間のデータ転送に ExpressRoute 接続を使用することで、大きなコスト上のメリットが得られる場合があります。

### <a name="where-is-the-service-available"></a>このサービスはどこで使用できますか。
サービスの場所と提供状況については、 [ExpressRoute のパートナーと提供地域](expressroute-locations.md)に関するページを参照してください。

### <a name="how-can-i-use-expressroute-to-connect-to-microsoft-if-i-dont-have-partnerships-with-one-of-the-expressroute-carrier-partners"></a>ExpressRoute のパートナー通信会社のいずれとも契約していない場合は、どのようにして ExpressRoute を使用して Microsoft に接続できますか。
地域の通信会社と地上イーサネット接続を選択して、Exchange プロバイダーの所在地のいずれかに接続します。 その後、このプロバイダーの所在地で Microsoft とピアリングできます。 [ExpressRoute パートナーと場所](expressroute-locations.md) に関するページの最後のセクションで、サービス プロバイダーが Exchange の提供地域のいずれかに存在するかどうかを確認してください。 存在すれば、サービス プロバイダーを通じて ExpressRoute 回線を Azure に接続するように依頼できます。

### <a name="how-much-does-expressroute-cost"></a>ExpressRoute の料金はいくらですか。
料金情報については、「 [ExpressRoute 料金](https://azure.microsoft.com/pricing/details/expressroute/) 」を参照してください。

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-does-the-vpn-connection-i-purchase-from-my-network-service-provider-have-to-be-the-same-speed"></a>特定の帯域幅の ExpressRoute 回線に対して料金を支払っている場合、ネットワーク サービス プロバイダーから同じ速度の VPN 接続を購入する必要がありますか。
いいえ。 サービス プロバイダーから任意の速度の VPN 接続を購入できます。 ただし、Azure への接続は、購入した ExpressRoute 回線の帯域幅に制限されます。

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-do-i-have-the-ability-to-burst-up-to-higher-speeds-if-required"></a>特定の帯域幅の ExpressRoute 回線に対して料金を支払っている場合、必要に応じてより高速にバーストすることはできますか。
はい。 ExpressRoute 回線は、購入した帯域幅の 2 倍まで無料でバーストできるように構成されています。 サービス プロバイダーがこの機能をサポートしているかどうかをご確認ください。

### <a name="can-i-use-the-same-private-network-connection-with-virtual-network-and-other-azure-services-simultaneously"></a>同じプライベート ネットワーク接続を、Virtual Network や他の Azure サービスに同時に使用できますか。
はい。 ExpressRoute 回線のセットアップが完了すると、1 本の回線で仮想ネットワーク内のサービスや他の Azure サービスに同時にアクセスできるようになります。 仮想ネットワークにはプライベート ピアリング パス経由で接続し、他のサービスにはパブリック ピアリング パス経由で接続します。

### <a name="does-expressroute-offer-a-service-level-agreement-sla"></a>ExpressRoute ではサービス レベル アグリーメント (SLA) は提供されますか。
詳細については、 [ExpressRoute SLA](https://azure.microsoft.com/support/legal/sla/) に関するページを参照してください。

## <a name="supported-services"></a>サポートされているサービス
ExpressRoute は、さまざまな種類のサービスのために、[3 つのルーティング ドメイン](expressroute-circuit-peerings.md)をサポートしています。

### <a name="private-peering"></a>プライベート ピアリング
* すべての仮想マシンとクラウド サービスを含む、仮想ネットワーク

### <a name="public-peering"></a>パブリック ピアリング
* Power BI
* Dynamics 365 for Operations (旧称 Dynamics AX Online)
* 以下のいくつかの例外を除く、ほとんどの Azure サービス
  * CDN
  * Visual Studio Team Services のロード テスト
  * Multi-factor Authentication
  * Traffic Manager

### <a name="microsoft-peering"></a>Microsoft ピアリング
* [Office 365](http://aka.ms/ExpressRouteOffice365)
* ほとんどの Dynamics 365 サービス (旧称 CRM Online)
  * Dynamics 365 for Sales
  * Dynamics 365 for Customer Service
  * Dynamics 365 for Field Service
  * Dynamics 365 for Project Service

## <a name="data-and-connections"></a>データおよび接続
### <a name="are-there-limits-on-the-amount-of-data-that-i-can-transfer-using-expressroute"></a>ExpressRoute を使用して転送できるデータの量に制限はありますか。
データ転送量に対する制限はありません。 帯域幅ごとの料金については、「 [ExpressRoute の価格](https://azure.microsoft.com/pricing/details/expressroute/) 」を参照してください。

### <a name="what-connection-speeds-are-supported-by-expressroute"></a>ExpressRoute でサポートされる接続速度はどれくらいですか。
サポートされる帯域幅:

|50 Mbps、100 Mbps、200 Mbps、500 Mbps、1 Gbps、2 Gbps、5 Gbps、10 Gbps|

### <a name="which-service-providers-are-available"></a>どのサービス プロバイダーを使用できますか。
サービス プロバイダーとサービスの場所の一覧については、 [ExpressRoute のパートナーと提供地域](expressroute-locations.md) に関するページを参照してください。

## <a name="technical-details"></a>技術的な詳細
### <a name="what-are-the-technical-requirements-for-connecting-my-on-premises-location-to-azure"></a>オンプレミスの場所から Azure に接続するための技術的な要件は何ですか。
要件については、 [ExpressRoute の前提条件](expressroute-prerequisites.md) に関するページを参照してください。

### <a name="are-connections-to-expressroute-redundant"></a>ExpressRoute への接続は冗長化されていますか。
はい。 各 Express Route 回線にクロス接続の冗長ペアが設定され、高可用性を実現するように構成されています。

### <a name="will-i-lose-connectivity-if-one-of-my-expressroute-links-fail"></a>ExpressRoute リンクのいずれかに障害が発生すると接続できなくなりますか。
クロス接続の一方に障害が発生しても、接続が失われることはありません。 ネットワークの負荷に対応できる冗長接続が用意されています。 さらに、耐障害性を実現するために、複数の回線を別のピアリング場所に作成することもできます。

### <a name="onep2plink"></a>クラウド エクスチェンジで併置しておらず、サービス プロバイダーがポイント ツー ポイント接続を提供している場合は、オンプレミス ネットワークと Microsoft 間の物理接続を 2 つ注文する必要がありますか。
いいえ、サービス プロバイダーが物理接続経由で 2 つのイーサネット仮想回線を確立できる場合は、必要な物理接続は 1 つだけです。 物理接続 (光ファイバーなど) は、レイヤー 1 (L1) デバイスで終端します (下図を参照)。 2 つのイーサネット仮想回線は、異なる VLAN ID (プライマリ回線とセカンダリ回線の VLAN ID) でタグ付けされます。 これらの VLAN ID は、外部 802.1Q イーサネット ヘッダーに含まれます。 内部 802.1Q イーサネット ヘッダー (ここでは示されていません) は、特定の [ExpressRoute ルーティング ドメイン](expressroute-circuit-peerings.md)にマップされます。 

![](./media/expressroute-faqs/expressroute-p2p-ref-arch.png)

### <a name="can-i-extend-one-of-my-vlans-to-azure-using-expressroute"></a>自社の VLAN を、ExpressRoute を使用して Azure に拡張することはできますか。
いいえ。 Azure へのレイヤー 2 接続の拡張はサポートされません。

### <a name="can-i-have-more-than-one-expressroute-circuit-in-my-subscription"></a>1 つのサブスクリプションで複数の ExpressRoute 回線を使用できますか。
はい。 1 つのサブスクリプションで、複数の ExpressRoute 回線をご利用いただけます。 専用回線の既定の上限は 10 に設定されています。 上限を増やす必要がある場合は、Microsoft サポートにご連絡ください。

### <a name="can-i-have-expressroute-circuits-from-different-service-providers"></a>別のサービス プロバイダーから ExpressRoute 回線を使用することはできますか。
はい。 多くのサービス プロバイダーで ExpressRoute 回線をご利用いただけます。 各 ExpressRoute 回線は、1 つのサービス プロバイダーのみに関連付けられます。 

### <a name="can-i-have-multiple-expressroute-circuits-in-the-same-location"></a>同じ場所に複数の ExpressRoute 回線を配置することはできますか。
はい。 複数の ExpressRoute 回線を同じ場所に配置できます。サービス プロバイダーは同じでも違っていてもかまいません。 ただし、複数の ExpressRoute 回線を同じ場所から同じ Virtual Network に接続することはできません。

### <a name="how-do-i-connect-my-virtual-networks-to-an-expressroute-circuit"></a>ExpressRoute 回線に仮想ネットワークを接続するにはどうすればいいですか。
基本的な手順の概略は次のとおりです。

* ExpressRoute 回線を確立し、サービス プロバイダーに依頼してその回線を有効にします。
* お客様またはプロバイダーは、BGP ピアリングを構成する必要があります。
* 仮想ネットワークを ExpressRoute 回線に接続します。

詳細については、「 [回線のプロビジョニングと回線の状態の ExpressRoute ワークフロー](expressroute-workflows.md) 」を参照してください。

### <a name="are-there-connectivity-boundaries-for-my-expressroute-circuit"></a>当社の ExpressRoute 回線に接続境界はありますか。
はい。 [ExpressRoute のパートナーと提供地域](expressroute-locations.md) に関するページに、ExpressRoute 回線の接続境界に関する概要が記載されています。 ExpressRoute 回線の接続は、1 つの地理的リージョンに制限されます。 ExpressRoute Premium 機能を有効にすると、地理的リージョンを越えて接続を拡張できます。

### <a name="can-i-link-to-more-than-one-virtual-network-to-an-expressroute-circuit"></a>複数の仮想ネットワークを ExpressRoute 回線に接続できますか。
はい。 標準の ExpressRoute 回線では最大 10 の仮想ネットワークを、[ExpressRoute Premium 回線](#expressroute-premium)では最大 100 の仮想ネットワークを接続できます。 

### <a name="i-have-multiple-azure-subscriptions-that-contain-virtual-networks-can-i-connect-virtual-networks-that-are-in-separate-subscriptions-to-a-single-expressroute-circuit"></a>仮想ネットワークを含む複数の Azure サブスクリプションがあります。 個別のサブスクリプション内の仮想ネットワークを 1 つの ExpressRoute 回線に接続できますか。
はい。 最大 10 個の Azure サブスクリプションで、1 つの ExpressRoute 回線を使用することを承認できます。 ExpressRoute Premium 機能を有効にすると、この上限を増やすことができます。

詳細については、「 [複数のサブスクリプションの間で ExpressRoute 回線を共有する](expressroute-howto-linkvnet-arm.md)」を参照してください。

### <a name="are-virtual-networks-connected-to-the-same-circuit-isolated-from-each-other"></a>同じ回線に接続されている仮想ネットワークは、互いに分離されていますか。
いいえ。 同じ ExpressRoute 回線に接続されているすべての仮想ネットワークは、同じルーティング ドメインに属しているため、ルーティングの観点から見た場合、互いに分離されていません。 ルートの分離が必要な場合は、ExpressRoute 回線を別に作成する必要があります。

### <a name="can-i-have-one-virtual-network-connected-to-more-than-one-expressroute-circuit"></a>1 つの仮想ネットワークを複数の ExpressRoute 回線に接続できますか。
はい。 1 つの仮想ネットワークを最大 4 つの ExpressRoute 回線に接続できます。 これらは、4 つの異なる [ExpressRoute の場所](expressroute-locations.md)で注文する必要があります。

### <a name="can-i-access-the-internet-from-my-virtual-networks-connected-to-expressroute-circuits"></a>ExpressRoute 回線に接続された仮想ネットワークから、インターネットにアクセスできますか。
はい。 BGP セッションを介して既定ルート (0.0.0.0/0) またはインターネット ルート プレフィックスをアドバタイズしていなければ、ExpressRoute 回線に接続された仮想ネットワークからインターネットに接続できます。

### <a name="can-i-block-internet-connectivity-to-virtual-networks-connected-to-expressroute-circuits"></a>ExpressRoute 回線に接続されている仮想ネットワークに対するインターネット接続をブロックできますか。
はい。 既定ルート (0.0.0.0/0) をアドバタイズして仮想ネットワーク内にデプロイされた仮想マシンへのインターネット接続をすべてブロックし、すべてのトラフィックが ExpressRoute 回線を経由しないようにルーティングすることができます。 ただし、既定ルートをアドバタイズすると、パブリック ピアリング経由で提供されるサービス (Azure Storage や SQL Database など) へのトラフィックが強制的にオンプレミスにルーティングされるので注意してください。 パブリック ピアリング パス経由またはインターネット経由でトラフィックを Azure に返すようにルーターを構成する必要があります。

### <a name="can-virtual-networks-linked-to-the-same-expressroute-circuit-talk-to-each-other"></a>同じ ExpressRoute 回線に接続されている仮想ネットワークは互いに通信できますか。
はい。 同じ ExpressRoute 回線に接続されている仮想ネットワークにデプロイされた仮想マシンは互いに通信できます。

### <a name="can-i-use-site-to-site-connectivity-for-virtual-networks-in-conjunction-with-expressroute"></a>仮想ネットワーク用のサイト間接続を、ExpressRoute と併用できますか。
はい。 ExpressRoute は、サイト間 VPN と共存できます。

### <a name="can-i-move-a-virtual-network-from-site-to-site--point-to-site-configuration-to-use-expressroute"></a>仮想ネットワークを、サイト間/ポイント対サイト構成から ExpressRoute を使用する構成に移行できますか。
はい。 仮想ネットワーク内に ExpressRoute ゲートウェイを作成する必要があります。 このプロセスに関連して、わずかにダウンタイムが発生します。

### <a name="why-is-there-a-public-ip-address-associated-with-the-expressroute-gateway-on-a-virtual-network"></a>仮想ネットワークで ExpressRoute ゲートウェイに関連付けられているパブリック IP アドレスが存在するのはなぜですか。
これは内部管理でのみ使用されます。 このパブリック IP アドレスはインターネットには公開されず、仮想ネットワークのセキュリティを損なうものではありません。

### <a name="what-do-i-need-to-connect-to-azure-storage-over-expressroute"></a>ExpressRoute を経由して Azure Storage に接続するには何が必要ですか。
ExpressRoute 回線を確立し、パブリック ピアリング用のルートを構成する必要があります。

### <a name="are-there-limits-on-the-number-of-routes-i-can-advertise"></a>アドバタイズできるルートの数に上限はありますか。
はい。 プライベート ピアリング用に最大 4,000 個およびパブリック ピアリングと Microsoft ピアリング用にそれぞれ 200 個のルート プレフィックスを使用できます。 ExpressRoute Premium 機能を有効にすると、プライベート ピアリング用のこの上限を 10,000 ルートに増やすことができます。

### <a name="are-there-restrictions-on-ip-ranges-i-can-advertise-over-the-bgp-session"></a>BGP セッションを介してアドバタイズできる IP 範囲に制限はありますか。
パブリックと Microsoft のピアリング BGP セッションでは、プライベート プレフィックス (RFC1918) は受け付けられません。

### <a name="what-happens-if-i-exceed-the-bgp-limits"></a>BGP の上限を超えるとどうなりますか。
BGP セッションが切断されます。 プレフィックス数が上限未満になると、BGP セッションはリセットされます。

### <a name="what-is-the-expressroute-bgp-hold-time-can-it-be-adjusted"></a>ExpressRoute BGP ホールド時間とは何ですか。 それは調整できますか。
ホールド時間は 180 です。 キープ アライブ メッセージは、60 秒ごとに送信されます。 これらは Microsoft 側の固定設定であり、変更することはできません。

### <a name="after-i-advertise-the-default-route-00000-to-my-virtual-networks-i-cant-activate-windows-running-on-my-azure-vms-how-to-i-fix-this"></a>仮想ネットワークへの既定ルート (0.0.0.0/0) のアドバタイズ後に、Azure Virtual Network で実行する Windows をライセンス認証できません。 どうしたらいいですか。
次の手順を実行して、Azure にライセンス認証要求を認識させることができます。

1. ExpressRoute 回線用にパブリック ピアリングを確立します。
2. DNS 参照を実行し、 **kms.core.windows.net**
3. 次の 3 つの項目のいずれかを実行すると、Azure からのライセンス認証要求がキー管理サービスによって認識され、要求が受け付けられます。
   * オンプレミス ネットワークで、(手順 2 で取得した) IP アドレス宛てのトラフィックをパブリック ピアリング経由で Azure にルーティングする。
   * NSP プロバイダーに依頼して、トラフィックをパブリック ピアリング経由で Azure に U ターンしてもらう。
   * 次ホップとしてのインターネットを持つその IP をポイントするユーザー定義ルートを作成し、これをこれらの仮想マシンがあるサブネットに適用します。

### <a name="can-i-change-the-bandwidth-of-an-expressroute-circuit"></a>ExpressRoute 回線の帯域幅を変更できますか。
はい、Azure Portal または PowerShell を使用して、ExpressRoute 回線の帯域幅を増やすことを試すことができます。 回線が作成された物理ポートで使用可能な容量があれば、変更は成功します。 変更が失敗した場合は、現在のポートに十分な容量が残っていないため、帯域幅が大きい新しい ExpressRoute 回線を作成する必要がある、またはその場所には追加の容量がないため、帯域幅を増やすことはできないことを意味します。 また、接続プロバイダーに連絡して、帯域幅の増加をサポートするようにネットワーク内の調整を更新してもらう必要があります。 ただし、ExpressRoute 回線の帯域幅を減らすことはできません。 帯域幅が小さい新しい ExpressRoute 回線を作成し、古い回線を削除する必要があります。

### <a name="how-do-i-change-the-bandwidth-of-an-expressroute-circuit"></a>ExpressRoute 回線の帯域幅を変更するには、どうすればいいですか。
専用回線の更新用の API や PowerShell コマンドレットを使用して、ExpressRoute 回線の帯域幅を更新できます。

## <a name="expressroute-premium"></a>ExpressRoute Premium
### <a name="what-is-expressroute-premium"></a>ExpressRoute Premium とは何ですか。
ExpressRoute Premium は、次に示す機能で構成されたサービスです。

* プライベート ピアリング用ルートの上限が 4,000 から 10,000 に増加されたルーティング テーブル。
* ExpressRoute 回線に接続できる数が増加された VNET (既定は 10)。 詳細については、下の表を参照してください。
* Microsoft のコア ネットワーク経由のグローバル接続。 ある地理的リージョンにある VNET を別のリージョン内の ExpressRoute 回線に接続できるようになります。 **例:** 西ヨーロッパで作成された VNET をシリコン バレーで作成された ExpressRoute 回線に接続できます。 **その他の例:** パブリック ピアリングで、他の地理的リージョンのプレフィックスがアドバタイズされ、たとえばシリコン バレーの回線から西ヨーロッパの SQL Azure に接続できるようになります。
* Office 365 サービスおよび CRM Online への接続。

### <a name="how-many-vnets-can-i-link-to-an-expressroute-circuit-if-i-enabled-expressroute-premium"></a>ExpressRoute Premium を有効にすると、どれくらいの VNET を ExpressRoute 回線に接続できますか。
次の表は、ExpressRoute の制限と、ExpressRoute 回線ごとの VNet の数を示しています。

[!INCLUDE [expressroute-limits](../../includes/expressroute-limits.md)]

### <a name="how-do-i-enable-expressroute-premium"></a>ExpressRoute Premium はどのようにして有効にしますか。
ExpressRoute Premium の機能は、ExpressRoute 機能を有効にする際に有効にでき、回線の状態を更新することで無効にできます。 ExpressRoute Premium の有効化は、回線の作成時のほか、専用回線の更新用の API や PowerShell コマンドレットを呼び出して行うこともできます。

### <a name="how-do-i-disable-expressroute-premium"></a>ExpressRoute Premium はどのようにして無効にしますか。
ExpressRoute Premium を無効にするには、専用回線の更新用の API や PowerShell コマンドレットを呼び出します。 ExpressRoute Premium を無効にする前に、接続ニーズが既定の上限を超えて増大していないことを確認する必要があります。 使用量が既定の上限を超えて増大している場合は、ExpressRoute Premium を無効にする要求が失敗します。

### <a name="can-i-pick-and-choose-the-features-i-want-from-the-premium-feature-set"></a>Premium の機能セットから必要な機能だけを選択できますか。
いいえ。 必要な機能のみを選択することはできません。 ExpressRoute Premium を有効にすると、すべての機能が有効になります。

### <a name="how-much-does-expressroute-premium-cost"></a>ExpressRoute Premium の料金はいくらですか。
料金については、「 [ExpressRoute の価格](https://azure.microsoft.com/pricing/details/expressroute/) 」を参照してください。

### <a name="do-i-pay-for-expressroute-premium-in-addition-to-standard-expressroute-charges"></a>ExpressRoute Premium の料金は、標準の ExpressRoute 料金に追加して支払うのですか。
はい。 ExpressRoute Premium 料金は、ExpressRoute 回線の料金と接続プロバイダーに必要な料金に追加する形で適用されます。

## <a name="expressroute-and-office-365-services-and-crm-online"></a>ExpressRoute と、Office 365 サービスおよび CRM Online
[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

### <a name="how-do-i-create-an-expressroute-circuit-to-connect-to-office-365-services-and-crm-online"></a>Office 365 サービスおよび CRM Online に接続する ExpressRoute 回線はどのようにして作成しますか。
1. [ExpressRoute の前提条件のページ](expressroute-prerequisites.md)を参照して、要件を満たしていることを確認します。
2. [ExpressRoute のパートナーと提供地域](expressroute-locations.md) に関するページでサービス プロバイダーとサービスの場所の一覧を参照し、接続ニーズが満たされることを確認します。
3. 「 [Office 365 のネットワーク計画とパフォーマンスのチューニング](http://aka.ms/tune/)」を参照して、容量の要件を計画します。
4. 接続をセットアップするには、「[回線のプロビジョニングと回線の状態の ExpressRoute ワークフロー](expressroute-workflows.md)」に示されている手順に従います。

> [!IMPORTANT]
> Office 365 サービスおよび CRM Online への接続を構成するときは、ExpressRoute Premium アドオンを有効にしていることを確認します。
> 
> 

### <a name="do-i-need-to-enable-azure-public-peering-to-connect-to-office-365-services-and-crm-online"></a>Office 365 サービスと CRM Online に接続するには、Azure パブリック ピアリングを有効にする必要がありますか。
いいえ、有効にする必要があるのは Microsoft ピアリングだけです。 Azure AD への認証トラフィックは Microsoft ピアリングを介して送信されます。 

### <a name="can-my-existing-expressroute-circuits-support-connectivity-to-office-365-services-and-crm-online"></a>既存の ExpressRoute 回線が Office 365 サービスおよび CRM Online への接続をサポートするようにできますか。
はい。 既存の ExpressRoute 回線を、Office 365 サービスへの接続をサポートするように構成できます。 Office 365 サービスに接続するための十分な容量があり、Premium アドオンを有効にしていることを確認します。 [Office 365 のネットワーク プランニングとパフォーマンス チューニング](http://aka.ms/tune/) に関するページが、接続ニーズを計画するのに役立ちます。 「 [ExpressRoute 回線の作成と変更](expressroute-howto-circuit-classic.md)」も参照してください。

### <a name="what-office-365-services-can-be-accessed-over-an-expressroute-connection"></a>ExpressRoute 接続経由でアクセスできる Office 365 のサービスはどれですか。
ExpressRoute でサポートされているサービスの最新の状態の一覧については、「[Office 365 URL および IP アドレス範囲](http://aka.ms/o365endpoints)」を参照してください。

### <a name="how-much-does-expressroute-for-office-365-services-and-crm-online-cost"></a>Office 365 サービスおよび CRM Online に対応した ExpressRoute の料金はいくらですか。
Office 365 サービス および CRM Online では、Premium アドオンを有効にする必要があります。 ExpressRoute の料金の詳細については、 [料金の詳細](https://azure.microsoft.com/pricing/details/expressroute/) に関するページを参照してください。

### <a name="what-regions-is-expressroute-for-office-365-supported-in"></a>ExpressRoute for Office 365 はどのリージョンでサポートされていますか。
ExpressRoute がサポートされているパートナーと地域の一覧については、 [ExpressRoute のパートナーと提供地域](expressroute-locations.md) に関するページを参照してください。

### <a name="can-i-access-office-365-over-the-internet-even-if-expressroute-was-configured-for-my-organization"></a>自社で ExpressRoute が構成されている場合でも、インターネット経由で Office 365 にアクセスできますか。
はい。 自社のネットワークで ExpressRoute が構成されている場合でも、インターネット経由で Office 365 サービスのエンドポイントにアクセスできます。 ExpressRoute 経由で Office 365 サービスに接続するように構成されている場所では、ExpressRoute 経由で接続します。

### <a name="can-dynamics-365-for-operations-formerly-known-as-dynamics-ax-online-be-accessed-over-an-expressroute-connection"></a>ExpressRoute 接続経由で Dynamics 365 for Operations (旧称 Dynamics AX Online) にアクセスできますか。
はい。 [Dynamics 365 for Operations](https://www.microsoft.com/dynamics365/operations) は、Azure でホストされています。 それに接続するには、ExpressRoute 回線で Azure パブリック ピアリングを有効にします。 


