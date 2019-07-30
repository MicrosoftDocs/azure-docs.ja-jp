---
title: よくあるご質問 (FAQ) - Azure ExpressRoute | Microsoft Docs
description: ExpressRoute の FAQ には、サポートされている Azure サービス、料金、データと接続、SLA、プロバイダーと提供地域、帯域幅、およびその他の技術的な詳細に関する情報が記載されています。
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: jaredro
ms.custom: seodec18
ms.openlocfilehash: 02f772d02f5cc6f188d69b5e79debc0013cf1faa
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2019
ms.locfileid: "67488497"
---
# <a name="expressroute-faq"></a>ExpressRoute の FAQ

## <a name="what-is-expressroute"></a>ExpressRoute とは何ですか。

ExpressRoute は、Microsoft のデータセンターとオンプレミスや共用施設にあるインフラストラクチャの間にプライベート接続を作成できる Azure サービスです。 ExpressRoute 接続はパブリックなインターネットを経由しないため、インターネット経由の一般的な接続に比べて、安全性と信頼性が高く、待機時間も短く、高速です。

### <a name="what-are-the-benefits-of-using-expressroute-and-private-network-connections"></a>ExpressRoute とプライベート ネットワーク接続を使用する利点は何ですか。

ExpressRoute 接続では、公共のインターネットを利用できません。 この接続は、インターネット経由の一般的な接続に比べて、安全性と信頼性が高く、待機時間も一貫して短く、高速です。 オンプレミスのデバイスと Azure の間のデータ転送に ExpressRoute 接続を使用することで、大きなコスト上のメリットが得られる場合があります。

### <a name="where-is-the-service-available"></a>このサービスはどこで使用できますか。

サービスの場所と可用性については、[ExpressRoute のパートナーと提供地域](expressroute-locations.md)に関する記事をご覧ください。

### <a name="how-can-i-use-expressroute-to-connect-to-microsoft-if-i-dont-have-partnerships-with-one-of-the-expressroute-carrier-partners"></a>ExpressRoute のパートナー通信会社のいずれとも契約していない場合は、どのようにして ExpressRoute を使用して Microsoft に接続できますか。

地域の通信会社と地上イーサネット接続を選択して、Exchange プロバイダーの所在地のいずれかに接続します。 その後、このプロバイダーの所在地で Microsoft とピアリングできます。 [ExpressRoute パートナーと場所](expressroute-locations.md) に関するページの最後のセクションで、サービス プロバイダーが Exchange の提供地域のいずれかに存在するかどうかを確認してください。 存在すれば、サービス プロバイダーを通じて ExpressRoute 回線を Azure に接続するように依頼できます。

### <a name="how-much-does-expressroute-cost"></a>ExpressRoute の料金はいくらですか。

料金情報については、「 [ExpressRoute 料金](https://azure.microsoft.com/pricing/details/expressroute/) 」を参照してください。

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-does-the-vpn-connection-i-purchase-from-my-network-service-provider-have-to-be-the-same-speed"></a>特定の帯域幅の ExpressRoute 回線に対して料金を支払っている場合、ネットワーク サービス プロバイダーから同じ速度の VPN 接続を購入する必要がありますか。

いいえ。 サービス プロバイダーから任意の速度の VPN 接続を購入できます。 ただし、Azure への接続は、購入した ExpressRoute 回線の帯域幅に制限されます。

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-do-i-have-the-ability-to-burst-up-to-higher-speeds-if-necessary"></a>特定の帯域幅の ExpressRoute 回線に対して料金を支払っている場合、必要に応じてより高速にバーストすることはできますか。

はい。 ExpressRoute 回線は、購入した帯域幅の 2 倍まで無料でバーストできるように構成されています。 サービス プロバイダーがこの機能をサポートしているかどうかをご確認ください。 この動作は長時間を対象としたものではなく、保証もされていません。 

### <a name="can-i-use-the-same-private-network-connection-with-virtual-network-and-other-azure-services-simultaneously"></a>同じプライベート ネットワーク接続を、仮想ネットワークや他の Azure サービスに同時に使用できますか。

はい。 ExpressRoute 回線のセットアップが完了すると、1 本の回線で仮想ネットワーク内のサービスや他の Azure サービスに同時にアクセスできるようになります。 仮想ネットワークにはプライベート ピアリング パス経由で接続し、他のサービスには Microsoft ピアリング パス経由で接続します。

### <a name="does-expressroute-offer-a-service-level-agreement-sla"></a>ExpressRoute ではサービス レベル アグリーメント (SLA) は提供されますか。

詳しくは、[ExpressRoute SLA](https://azure.microsoft.com/support/legal/sla/) ページをご覧ください。

## <a name="supported-services"></a>サポートされているサービス

ExpressRoute は、さまざまな種類のサービスのために、[3 つのルーティング ドメイン](expressroute-circuit-peerings.md)をサポートしています。

### <a name="private-peering"></a>プライベート ピアリング

* すべての仮想マシンとクラウド サービスを含む、仮想ネットワーク

### <a name="public-peering"></a>パブリック ピアリング

>[!NOTE]
>新しい ExpressRoute 回線では、パブリック ピアリングは無効になっています。 Azure サービスは、Microsoft ピアリングで利用できます。
>

* Power BI
* Dynamics 365 for Finance and Operations (旧称 Dynamics AX Online)
* ほとんどの Azure サービスがサポートされています。 サポートの確認に使うサービスで直接確認してください。<br><br>
  **以下のサービスはサポートされていません**。
    * CDN
    * Azure Front Door
    * Multi-factor Authentication
    * Traffic Manager

### <a name="microsoft-peering"></a>Microsoft ピアリング

* [Office 365](https://aka.ms/ExpressRouteOffice365)
* Dynamics 365 
* Power BI - Azure リージョン コミュニティを介して使用できます。Power BI テナントのリージョンを確認する方法については、[こちら](https://docs.microsoft.com/power-bi/service-admin-where-is-my-tenant-located)をご覧ください。 
* Azure Active Directory
* [Azure DevOps](https://blogs.msdn.microsoft.com/devops/2018/10/23/expressroute-for-azure-devops/) (Azure Global Services コミュニティ)
* ほとんどの Azure サービスがサポートされています。 サポートの確認に使うサービスで直接確認してください。<br><br>**以下のサービスはサポートされていません**。
    * CDN
    * Azure Front Door
    * Multi-factor Authentication
    * Traffic Manager

## <a name="data-and-connections"></a>データおよび接続

### <a name="are-there-limits-on-the-amount-of-data-that-i-can-transfer-using-expressroute"></a>ExpressRoute を使用して転送できるデータの量に制限はありますか。

データ転送量に対する制限はありません。 帯域幅ごとの料金については、「 [ExpressRoute の価格](https://azure.microsoft.com/pricing/details/expressroute/) 」を参照してください。

### <a name="what-connection-speeds-are-supported-by-expressroute"></a>ExpressRoute でサポートされる接続速度はどれくらいですか。

サポートされる帯域幅:

50 Mbps、100 Mbps、200 Mbps、500 Mbps、1 Gbps、2 Gbps、5 Gbps、10 Gbps

### <a name="which-service-providers-are-available"></a>どのサービス プロバイダーを使用できますか。

サービス プロバイダーとサービスの場所の一覧については、 [ExpressRoute のパートナーと提供地域](expressroute-locations.md) に関するページを参照してください。

## <a name="technical-details"></a>技術的な詳細

### <a name="what-are-the-technical-requirements-for-connecting-my-on-premises-location-to-azure"></a>オンプレミスの場所から Azure に接続するための技術的な要件は何ですか。

要件については、 [ExpressRoute の前提条件に関するページ](expressroute-prerequisites.md)を参照してください。

### <a name="are-connections-to-expressroute-redundant"></a>ExpressRoute への接続は冗長化されていますか。

はい。 各 ExpressRoute 回線にクロス接続の冗長ペアが設定され、高可用性を実現するように構成されています。

### <a name="will-i-lose-connectivity-if-one-of-my-expressroute-links-fail"></a>ExpressRoute リンクのいずれかに障害が発生すると接続できなくなりますか。

クロス接続の一方に障害が発生しても、接続が失われることはありません。 ネットワークの負荷をサポートし、ExpressRoute 回線の高可用性を得るために、冗長接続を利用できます。 さらに、回線を別のピアリング場所に作成して、回線レベルの回復力を実現できます。

### <a name="how-do-i-implement-redundancy-on-private-peering"></a>プライベート ピアリングに冗長性を確保するにはどうすればよいですか。

異なるピアリングの場所から複数の ExpressRoute 回線を同じ仮想ネットワークに接続することで、1 つの回線が使用できない状態になった場合の高可用性を確保することができます。 さらに、ローカル接続に、[より大きな重み](https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-assign-a-high-weight-to-local-connection)を割り当てることで、特定の回線を優先指定することができます。 単一障害点を回避するために、少なくとも 2 つの ExpressRoute 回線をセットアップすることを強くお勧めします。 

高可用性のための設計については[こちら](https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute)を、ディザスター リカバリーのための設計については[こちら](https://docs.microsoft.com/azure/expressroute/designing-for-disaster-recovery-with-expressroute-privatepeering)をご覧ください。  

### <a name="how-i-do-implement-redundancy-on-microsoft-peering"></a>Microsoft ピアリングに冗長性を確保するにはどうすればよいですか。

Microsoft ピアリングを使用して Azure のパブリック サービス (Azure Storage、Azure SQL など) にアクセスしているお客様や、Office 365 向け Microsoft ピアリングを使用しているお客様には、単一障害点を回避するために、異なるピアリングの場所に複数の回線を導入することを強くお勧めします。 両方の回線で同じプレフィックスをアドバタイズして [AS PATH プリペンド](https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-use-as-path-prepending)を使用するか、または異なるプレフィックスをアドバタイズしてオンプレミスからのパスを特定することができます。

高可用性のための設計については[こちら](https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute)をご覧ください。

### <a name="how-do-i-ensure-high-availability-on-a-virtual-network-connected-to-expressroute"></a>ExpressRoute に接続されている仮想ネットワークで高可用性を確保する方法

別のピアリング場所 (例: Singapore、Singapore2) にある ExpressRoute 回線を仮想ネットワークに接続することで、高可用性を実現できます。 1 つの ExpressRoute サイトがダウンした場合、接続は別の ExpressRoute サイトにフェールオーバーされます。 仮想ネットワークを離れるトラフィックは、既定で Equal Cost Multi-path Routing (ECMP) に基づいてルーティングされます。 接続の重みを使用して、ある回線を別の回線よりも優先することができます。 詳細については、「[ExpressRoute ルーティングの最適化](expressroute-optimize-routing.md)」を参照してください。

### <a name="onep2plink"></a>クラウド エクスチェンジで併置しておらず、サービス プロバイダーがポイント ツー ポイント接続を提供している場合は、オンプレミス ネットワークと Microsoft 間の物理接続を 2 つ注文する必要がありますか。

サービス プロバイダーが物理接続経由で 2 つのイーサネット仮想回線を確立できる場合は、必要な物理接続は 1 つだけです。 物理接続 (光ファイバーなど) は、レイヤー 1 (L1) デバイスで終端します (図を参照)。 2 つのイーサネット仮想回線は、異なる VLAN ID (プライマリ回線とセカンダリ回線の VLAN ID) でタグ付けされます。 これらの VLAN ID は、外部 802.1Q イーサネット ヘッダーに含まれます。 内部 802.1Q イーサネット ヘッダー (ここでは示されていません) は、特定の [ExpressRoute ルーティング ドメイン](expressroute-circuit-peerings.md)にマップされます。

![](./media/expressroute-faqs/expressroute-p2p-ref-arch.png)

### <a name="can-i-extend-one-of-my-vlans-to-azure-using-expressroute"></a>自社の VLAN を、ExpressRoute を使用して Azure に拡張することはできますか。

いいえ。 Azure へのレイヤー 2 接続の拡張はサポートされません。

### <a name="can-i-have-more-than-one-expressroute-circuit-in-my-subscription"></a>1 つのサブスクリプションで複数の ExpressRoute 回線を使用できますか。

はい。 1 つのサブスクリプションで、複数の ExpressRoute 回線をご利用いただけます。 既定の制限は 10 に設定されています。 上限を増やす必要がある場合は、Microsoft サポートにご連絡ください。

### <a name="can-i-have-expressroute-circuits-from-different-service-providers"></a>別のサービス プロバイダーから ExpressRoute 回線を使用することはできますか。

はい。 多くのサービス プロバイダーで ExpressRoute 回線をご利用いただけます。 各 ExpressRoute 回線は、1 つのサービス プロバイダーのみに関連付けられます。 

### <a name="i-see-two-expressroute-peering-locations-in-the-same-metro-for-example-singapore-and-singapore2-which-peering-location-should-i-choose-to-create-my-expressroute-circuit"></a>同じ都市圏に 2 つの ExpressRoute のピアリング場所 (例: Singapore と Singapore2) があります。 ExpressRoute 回線を作成するのに、どちらのピアリング場所を選択する必要がありますか。
サービス プロバイダーが両方のサイトで ExpressRoute を提供している場合は、プロバイダーと協力して ExpressRoute をセットアップするサイトを選択できます。 

### <a name="can-i-have-multiple-expressroute-circuits-in-the-same-metro-can-i-link-them-to-the-same-virtual-network"></a>同じ都市圏に複数の ExpressRoute 回線を配置することはできますか。 同じ仮想ネットワークにリンクすることはできますか。

はい。 複数の ExpressRoute 回線を配置できます。サービス プロバイダーは同じでも違っていてもかまいません。 都市圏に複数の ExpressRoute ピアリング場所があり、異なる場所で回線が作成されている場合は、同じ仮想ネットワークにそれらをリンクすることができます。 回線が同じピアリング場所で作成されている場合は、最大 4 つの回路を同じ仮想ネットワークに接続できます。

### <a name="how-do-i-connect-my-virtual-networks-to-an-expressroute-circuit"></a>ExpressRoute 回線に仮想ネットワークを接続するにはどうすればいいですか。

基本的な手順は次のとおりです。

* ExpressRoute 回線を確立し、サービス プロバイダーに依頼してその回線を有効にします。
* お客様またはプロバイダーは、BGP ピアリングを構成する必要があります。
* 仮想ネットワークを ExpressRoute 回線に接続します。

詳細については、「[回線のプロビジョニングと回線の状態の ExpressRoute ワークフロー](expressroute-workflows.md)」を参照してください。

### <a name="are-there-connectivity-boundaries-for-my-expressroute-circuit"></a>当社の ExpressRoute 回線に接続境界はありますか。

はい。 [ExpressRoute のパートナーと提供地域](expressroute-locations.md)に関する記事に、ExpressRoute 回線の接続境界に関する概要が記載されています。 ExpressRoute 回線の接続は、1 つの地理的リージョンに制限されます。 ExpressRoute Premium 機能を有効にすると、地理的リージョンを越えて接続を拡張できます。

### <a name="can-i-link-to-more-than-one-virtual-network-to-an-expressroute-circuit"></a>複数の仮想ネットワークを ExpressRoute 回線に接続できますか。

はい。 標準の ExpressRoute 回線では最大 10 の仮想ネットワークを、[ExpressRoute Premium 回線](#expressroute-premium)では最大 100 の仮想ネットワークを接続できます。 

### <a name="i-have-multiple-azure-subscriptions-that-contain-virtual-networks-can-i-connect-virtual-networks-that-are-in-separate-subscriptions-to-a-single-expressroute-circuit"></a>仮想ネットワークを含む複数の Azure サブスクリプションがあります。 個別のサブスクリプション内の仮想ネットワークを 1 つの ExpressRoute 回線に接続できますか。

はい。 回線と同じサブスクリプションまたは異なるサブスクリプションにある最大 10 個の仮想ネットワークを 1 つの ExpressRoute 回線を使用してリンクすることができます。 ExpressRoute Premium 機能を有効にすると、この上限を増やすことができます。

詳細については、「[複数のサブスクリプションの間で ExpressRoute 回線を共有する](expressroute-howto-linkvnet-arm.md)」を参照してください。

### <a name="i-have-multiple-azure-subscriptions-associated-to-different-azure-active-directory-tenants-or-enterprise-agreement-enrollments-can-i-connect-virtual-networks-that-are-in-separate-tenants-and-enrollments-to-a-single-expressroute-circuit-not-in-the-same-tenant-or-enrollment"></a>異なる Azure Active Directory Windows Update または Enterprise Agreement 登録に関連付けられた Azure サブスクリプションが複数あります。 別のテナントおよび登録にある仮想ネットワークを、同じテナントまたは登録内にない 1 つの ExpressRoute 回線に接続することはできますか。

はい。 ExpressRoute の承認は、サブスクリプション、テナント、および登録の複数の境界にまたがることができます。追加の構成は必要ありません。 

詳細については、「[複数のサブスクリプションの間で ExpressRoute 回線を共有する](expressroute-howto-linkvnet-arm.md)」を参照してください。

### <a name="are-virtual-networks-connected-to-the-same-circuit-isolated-from-each-other"></a>同じ回線に接続されている仮想ネットワークは、互いに分離されていますか。

いいえ。 ルーティングの観点から見た場合、同じ ExpressRoute 回線に接続されているすべての仮想ネットワークは、同じルーティング ドメインに属しているため、互いに分離されていません。 ルートの分離が必要な場合は、ExpressRoute 回線を別に作成する必要があります。

### <a name="can-i-have-one-virtual-network-connected-to-more-than-one-expressroute-circuit"></a>1 つの仮想ネットワークを複数の ExpressRoute 回線に接続できますか。

はい。 同じピアリングの場所または異なるピアリングの場所で、1 つの仮想ネットワークを最大 4 つの ExpressRoute 回線に接続できます。 

### <a name="can-i-access-the-internet-from-my-virtual-networks-connected-to-expressroute-circuits"></a>ExpressRoute 回線に接続された仮想ネットワークから、インターネットにアクセスできますか。

はい。 BGP セッションを介して既定ルート (0.0.0.0/0) またはインターネット ルート プレフィックスをアドバタイズしていなければ、ExpressRoute 回線に接続された仮想ネットワークからインターネットに接続できます。

### <a name="can-i-block-internet-connectivity-to-virtual-networks-connected-to-expressroute-circuits"></a>ExpressRoute 回線に接続されている仮想ネットワークに対するインターネット接続をブロックできますか。

はい。 既定ルート (0.0.0.0/0) をアドバタイズして仮想ネットワーク内にデプロイされた仮想マシンへのインターネット接続をすべてブロックし、すべてのトラフィックが ExpressRoute 回線を経由するようにルーティングすることができます。

ただし、既定ルートをアドバタイズすると、Microsoft ピアリング経由で提供されるサービス (Azure ストレージや SQL DB など) へのトラフィックが強制的にオンプレミスにルーティングされます。 Microsoft ピアリング パス経由またはインターネット経由でトラフィックを Azure に返すようにルーターを構成する必要があります。 サービスのサービス エンドポイントを有効にした場合、サービスへのトラフィックはオンプレミスに強制されません。 トラフィックは Azure のバックボーン ネットワーク内にとどまります。 サービス エンドポイントの詳細については、「[仮想ネットワーク サービス エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md?toc=%2fazure%2fexpressroute%2ftoc.json)」を参照してください。

### <a name="can-virtual-networks-linked-to-the-same-expressroute-circuit-talk-to-each-other"></a>同じ ExpressRoute 回線に接続されている仮想ネットワークは互いに通信できますか。

はい。 同じ ExpressRoute 回線に接続されている仮想ネットワークにデプロイされた仮想マシンは互いに通信できます。

### <a name="can-i-use-site-to-site-connectivity-for-virtual-networks-in-conjunction-with-expressroute"></a>仮想ネットワーク用のサイト間接続を、ExpressRoute と併用できますか。

はい。 ExpressRoute は、サイト間 VPN と共存できます。 「[ExpressRoute 接続とサイト間接続の共存を構成する](expressroute-howto-coexist-resource-manager.md)」をご覧ください。

### <a name="why-is-there-a-public-ip-address-associated-with-the-expressroute-gateway-on-a-virtual-network"></a>仮想ネットワークで ExpressRoute ゲートウェイに関連付けられているパブリック IP アドレスが存在するのはなぜですか。

パブリック IP アドレスは内部管理にのみ使われ、仮想ネットワークのセキュリティを損なうことはありません。

### <a name="are-there-limits-on-the-number-of-routes-i-can-advertise"></a>アドバタイズできるルートの数に上限はありますか。

はい。 プライベート ピアリング用に最大 4,000 個、Microsoft ピアリング用に 200 個のルート プレフィックスを使用できます。 ExpressRoute Premium 機能を有効にすると、プライベート ピアリング用のこの上限を 10,000 ルートに増やすことができます。

### <a name="are-there-restrictions-on-ip-ranges-i-can-advertise-over-the-bgp-session"></a>BGP セッションを介してアドバタイズできる IP 範囲に制限はありますか。

Microsoft ピアリング BGP セッションでは、プライベート プレフィックス (RFC1918) は受け付けられません。 Microsoft とプライベート ピアリングの両方で、任意のプレフィックス サイズ (最大 /32) を受け入れることができます。

### <a name="what-happens-if-i-exceed-the-bgp-limits"></a>BGP の上限を超えるとどうなりますか。

BGP セッションが切断されます。 プレフィックス数が上限未満になると、BGP セッションはリセットされます。

### <a name="what-is-the-expressroute-bgp-hold-time-can-it-be-adjusted"></a>ExpressRoute BGP ホールド時間とは何ですか。 それは調整できますか。

ホールド時間は 180 です。 キープ アライブ メッセージは、60 秒ごとに送信されます。 これらは Microsoft 側の固定設定であり、変更することはできません。 異なる時間を構成できます。この構成に応じて、BGP セッションのパラメーターはネゴシエートされます。

### <a name="can-i-change-the-bandwidth-of-an-expressroute-circuit"></a>ExpressRoute 回線の帯域幅を変更できますか。

はい、Azure Portal または PowerShell を使用して、ExpressRoute 回線の帯域幅を増やすことを試すことができます。 回線が作成された物理ポートで使用可能な容量があれば、変更は成功します。 

変更が失敗した場合は、現在のポートに十分な容量が残っていないため、帯域幅が大きい新しい ExpressRoute 回線を作成する必要がある、またはその場所には追加の容量がないため、帯域幅を増やすことはできないことを意味します。 

また、接続プロバイダーに連絡して、帯域幅の増加をサポートするようにネットワーク内の調整を更新してもらう必要があります。 ただし、ExpressRoute 回線の帯域幅を減らすことはできません。 帯域幅が小さい新しい ExpressRoute 回線を作成し、古い回線を削除する必要があります。

### <a name="how-do-i-change-the-bandwidth-of-an-expressroute-circuit"></a>ExpressRoute 回線の帯域幅を変更するには、どうすればいいですか。

REST API や PowerShell コマンドレットを使用して、ExpressRoute 回線の帯域幅を更新できます。

## <a name="expressroute-premium"></a>ExpressRoute Premium

### <a name="what-is-expressroute-premium"></a>ExpressRoute Premium とは何ですか。

ExpressRoute Premium は、次の機能のコレクションです。

* プライベート ピアリング用ルートの上限が 4,000 から 10,000 に増加されたルーティング テーブル。
* ExpressRoute 回線で有効にできる VNet と ExpressRoute Global Reach の接続数の増加 (既定は 10)。 詳しくは、「[ExpressRoute の制限](#limits)」の表をご覧ください。
* Office 365 や Dynamics 365 への接続。
* Microsoft のコア ネットワーク経由のグローバル接続。 ある地理的リージョンにある VNET を別のリージョン内の ExpressRoute 回線に接続できるようになりました。<br>
    **例:**

    *  西ヨーロッパで作成された VNET をシリコン バレーで作成された ExpressRoute 回線に接続できます。 
    *  Microsoft ピアリングで、他の地理的リージョンのプレフィックスがアドバタイズされ、たとえばシリコン バレーの回線から西ヨーロッパの SQL Azure に接続できるようになります。


### <a name="limits"></a>ExpressRoute プレミアムを有効にした場合、ExpressRoute 回線で有効にできる VNet と ExpressRoute Global Reach の接続数はいくつですか。

次の表に、ExpressRoute の上限と、ExpressRoute 回線ごとの VNet と ExpressRoute Global Reach の数を示します。

[!INCLUDE [ExpressRoute limits](../../includes/expressroute-limits.md)]

### <a name="how-do-i-enable-expressroute-premium"></a>ExpressRoute Premium はどのようにして有効にしますか。

ExpressRoute Premium の機能は、ExpressRoute 機能を有効にする際に有効にでき、回線の状態を更新することで無効にできます。 ExpressRoute Premium の有効化は、回線の作成時のほか、REST API や PowerShell コマンドレットを呼び出して行うこともできます。

### <a name="how-do-i-disable-expressroute-premium"></a>ExpressRoute Premium はどのようにして無効にしますか。

ExpressRoute Premium を無効にするには、REST API や PowerShell コマンドレットを呼び出します。 ExpressRoute Premium を無効にする前に、接続ニーズが既定の上限を超えて増大していないことを確認する必要があります。 使用量が既定の上限を超えて増大している場合は、ExpressRoute Premium を無効にする要求が失敗します。

### <a name="can-i-pick-and-choose-the-features-i-want-from-the-premium-feature-set"></a>Premium の機能セットから必要な機能だけを選択できますか。

いいえ。 機能を選択することはできません。 ExpressRoute Premium を有効にすると、すべての機能が有効になります。

### <a name="how-much-does-expressroute-premium-cost"></a>ExpressRoute Premium の料金はいくらですか。

料金については、「 [ExpressRoute の価格](https://azure.microsoft.com/pricing/details/expressroute/) 」を参照してください。

### <a name="do-i-pay-for-expressroute-premium-in-addition-to-standard-expressroute-charges"></a>ExpressRoute Premium の料金は、標準の ExpressRoute 料金に追加して支払うのですか。

はい。 ExpressRoute Premium 料金は、ExpressRoute 回線の料金と接続プロバイダーに必要な料金に追加する形で適用されます。

## <a name="expressroute-local"></a>ExpressRoute Local
### <a name="what-is-expressroute-local"></a>ExpressRoute Local とは何ですか。
ExpressRoute Local は、[ExpressRoute Direct](expressroute-erdirect-about.md) で使用できる ExpressRoute 回線の SKU です。 Local の主な機能は、ExpressRoute ピアリングの場所の Local 回線で、ユーザーが、同じ都市圏内またはその近くにある 1 つまたは 2 つの Azure リージョンにのみアクセスできるようにすることです。 これに対し Standard 回線では地政学的領域内のすべての Azure リージョンに、Premium 回線では世界中のすべての Azure リージョンにユーザーがアクセスできます。 

### <a name="what-are-the-benefits-of-expressroute-local"></a>ExpressRoute Local のメリットは何ですか。
お使いの ExpressRoute 回線が Standard または Premium の場合、エグレス データ転送に対して料金が発生しますが、ExpressRoute Local 回線ではエグレス データ転送に対して個別に料金が発生することはありません。 つまり、ExpressRoute Local の価格には、データ転送料金が含まれます。 転送するデータが大量にある場合は、ExpressRoute Local がコスト効率の高いソリューションです。このソリューションでは、ご自身のデータを、プライベート接続経由で、必要な Azure リージョンの近くにある ExpressRoute ピアリングの場所に送信できます。 

### <a name="what-features-are-available-and-what-are-not-on-expressroute-local"></a>ExpressRoute Local ではどのような機能を使用できますか。また、何を使用できませんか。
Standard ExpressRoute 回線の機能セットとほぼ同じですが、次の機能が異なります。
* 上述した Azure リージョンへのアクセス範囲
* ExpressRoute Global Reach が Local では使用できません

ExpressRoute Local では、リソース制限 (回線あたりの VNet 数など) も Standard と同じです。 

### <a name="how-to-configure-expressroute-local"></a>ExpressRoute Local はどのように構成しますか。 
ExpressRoute Local は、ExpressRoute Direct でのみ使用できます。 したがって、最初に ExpressRoute Direct ポートを構成する必要があります。 Direct ポートが作成されたら、[こちら](expressroute-howto-erdirect.md)の手順に従って、Local 回線を作成できます。

### <a name="where-is-expressroute-local-available-and-which-azure-regions-is-each-peering-location-mapped-to"></a>ExpressRoute Local はどこで使用できますか。また、ピアリングの場所はそれぞれどの Azure リージョンにマッピングされていますか。
ExpressRoute Local は、1 つまたは 2 つの Azure リージョンが近くにあるピアリングの場所で使用できます。 ピアリングの場所の州、都道府県、国に Azure リージョンがない場合、そこで使用することはできません。 正確なマッピングについては、[場所のページ](expressroute-locations-providers.md)をご覧ください。  

## <a name="expressroute-for-office-365"></a>Office 365 向け ExpressRoute

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

### <a name="how-do-i-create-an-expressroute-circuit-to-connect-to-office-365-services"></a>Office 365 サービスに接続する ExpressRoute 回線はどのようにして作成しますか。

1. [ExpressRoute の前提条件のページ](expressroute-prerequisites.md)を参照して、要件を満たしていることを確認します。
2. 接続ニーズが満たされることを確認するには、[ExpressRoute のパートナーと提供地域](expressroute-locations.md)に関する記事でサービス プロバイダーとサービスの場所の一覧を確認します。
3. 「 [Office 365 のネットワーク計画とパフォーマンスのチューニング](https://aka.ms/tune/)」を参照して、容量の要件を計画します。
4. 接続をセットアップするには、「[回線のプロビジョニングと回線の状態の ExpressRoute ワークフロー](expressroute-workflows.md)」に示されている手順に従います。

> [!IMPORTANT]
> Office 365 サービスへの接続を構成するときは、ExpressRoute Premium アドオンを有効にしていることを確認します。
> 
> 

### <a name="can-my-existing-expressroute-circuits-support-connectivity-to-office-365-services-and-dynamics-365"></a>既存の ExpressRoute 回線が Office 365 サービスおよび Dynamics 365 への接続をサポートするようにできますか。

はい。 既存の ExpressRoute 回線を、Office 365 サービスへの接続をサポートするように構成できます。 Office 365 サービスに接続するための十分な容量があり、Premium アドオンを有効にしていることを確認します。 [Office 365 のネットワーク プランニングとパフォーマンス チューニング](https://aka.ms/tune/)に関するページが、接続ニーズを計画するのに役立ちます。 「 [ExpressRoute 回線の作成と変更](expressroute-howto-circuit-classic.md)」も参照してください。

### <a name="what-office-365-services-can-be-accessed-over-an-expressroute-connection"></a>ExpressRoute 接続経由でアクセスできる Office 365 のサービスはどれですか。

ExpressRoute でサポートされているサービスの最新の状態の一覧については、「[Office 365 URL および IP アドレス範囲](https://aka.ms/o365endpoints)」を参照してください。

### <a name="how-much-does-expressroute-for-office-365-services-cost"></a>Office 365 サービスに対応した ExpressRoute の料金はいくらですか。

Office 365 サービスでは、Premium アドオンを有効にする必要があります。 料金については、[料金詳細のページ](https://azure.microsoft.com/pricing/details/expressroute/)を参照してください。

### <a name="what-regions-is-expressroute-for-office-365-supported-in"></a>ExpressRoute for Office 365 はどのリージョンでサポートされていますか。

詳しくは、[ExpressRoute のパートナーと提供地域](expressroute-locations.md)に関する記事をご覧ください。

### <a name="can-i-access-office-365-over-the-internet-even-if-expressroute-was-configured-for-my-organization"></a>自社で ExpressRoute が構成されている場合でも、インターネット経由で Office 365 にアクセスできますか。

はい。 自社のネットワークで ExpressRoute が構成されている場合でも、インターネット経由で Office 365 サービスのエンドポイントにアクセスできます。 お客様の所在地のネットワークが ExpressRoute を介して Office 365 サービスに接続するように構成されている場合は、組織のネットワーク チームに問い合わせてください。

### <a name="how-can-i-plan-for-high-availability-for-office-365-network-traffic-on-azure-expressroute"></a>Azure ExpressRoute で Office 365 ネットワーク トラフィックの高可用性を計画するにはどうすればよいですか?
「[Azure ExpressRoute の高可用性とフェールオーバー](https://aka.ms/erhighavailability)」に記載の推奨事項を参照してください。

### <a name="can-i-access-office-365-us-government-community-gcc-services-over-an-azure-us-government-expressroute-circuit"></a>Azure US Government ExpressRoute 回線を通じてOffice 365 US Government Community (GCC) サービスにアクセスすることはできますか。

はい。 Office 365 GCC サービス エンドポイントに、Azure US Government ExpressRoute を通してアクセスすることができます。 ただし、まず、Azure Portal でサポート チケットを開いて、Microsoft にアドバタイズするプレフィックスを提供する必要があります。 サポート チケットが解決された後に、Office 365 GCC サービスへの接続が確立されます。 

## <a name="route-filters-for-microsoft-peering"></a>Microsoft ピアリングのルート フィルター

### <a name="i-am-turning-on-microsoft-peering-for-the-first-time-what-routes-will-i-see"></a>初めて Microsoft ピアリングを有効にしています。どのルートが表示されますか。

ルートは表示されません。 ルート フィルターを回線に接続し、プレフィックスのアドバタイズを開始する必要があります。 手順については、[Microsoft ピアリングのルート フィルターを構成する](how-to-routefilter-powershell.md)をご覧ください。

### <a name="i-turned-on-microsoft-peering-and-now-i-am-trying-to-select-exchange-online-but-it-is-giving-me-an-error-that-i-am-not-authorized-to-do-it"></a>Microsoft ピアリングを有効にし、Exchange Online を選択しようとしていますが、この操作を行う権限がないというエラーが発生します。

ルート フィルターを使用すると、どのユーザーでも場合 Microsoft ピアリングを有効にできます。 ただし、Office 365 サービスを使用するには、Office 365 で承認される必要があります。

### <a name="do-i-need-to-get-authorization-for-turning-on-dynamics-365-over-microsoft-peering"></a>Microsoft ピアリング経由で Dynamics 365 を有効にするための承認を取得する必要がありますか。

いいえ、Dynamics 365 の承認は不要です。 承認なしでルールを作成し、Dynamics 365 コミュニティを選択できます。

### <a name="i-enabled-microsoft-peering-prior-to-august-1-2017-how-can-i-take-advantage-of-route-filters"></a>2017 年 8 月 1 日より前に Microsoft ピアリングを有効にしました。どうすればルート フィルターを利用できますか。

既存の回線では、Office 365 と Dynamics 365 のプレフィックスのアドバタイズが継続されます。 同じ Microsoft ピアリング上に Azure パブリック プレフィックスのアドバタイズを追加する場合、ルート フィルターを作成し、アドバタイズが必要なサービス (必要な Office 365 サービス、Dynamics 365 など) を選択し、フィルターを Microsoft ピアリングにアタッチすることができます。 手順については、[Microsoft ピアリングのルート フィルターを構成する](how-to-routefilter-powershell.md)をご覧ください。

### <a name="i-have-microsoft-peering-at-one-location-now-i-am-trying-to-enable-it-at-another-location-and-i-am-not-seeing-any-prefixes"></a>ある場所で Microsoft ピアリングを持っています。別の場所でこれを有効にしようとしていますが、プレフィックスが表示されません。

* 2017 年 8 月 1 日より前に構成された ExpressRoute 回線の Microsoft ピアリングでは、ルート フィルターが定義されていない場合でも、すべてのサービス プレフィックスが Microsoft ピアリングでアドバタイズされます。

* 2017 年 8 月 1 日以降に構成された ExpressRoute 回路の Microsoft ピアリングでは、ルート フィルターが回線に接続されるまで、プレフィックスはアドバタイズされません。 既定ではプレフィックスは表示されません。

## <a name="expressRouteDirect"></a>ExpressRoute Direct

[!INCLUDE [ExpressRoute Direct](../../includes/expressroute-direct-faq-include.md)]

## <a name="globalreach"></a>Global Reach

[!INCLUDE [Global Reach](../../includes/expressroute-global-reach-faq-include.md)]
