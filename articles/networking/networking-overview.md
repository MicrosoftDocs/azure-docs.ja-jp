---
title: Azure ネットワーク サービスの概要
description: 接続、アプリケーション保護、アプリケーション配信、およびネットワーク監視サービスなどの Azure のネットワーク サービスについて説明します。
services: networking
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 03/12/2020
ms.author: kumud
ms.openlocfilehash: 1110199a8c02b29d70f0c12e1ed1a6341c44e403
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2020
ms.locfileid: "88037254"
---
# <a name="azure-networking-services-overview"></a>Azure ネットワーク サービスの概要

Azure のネットワーク サービスにはさまざまなネットワーク機能があり、単独で使用することも組み合わせて使用することもできます。 主な機能については、次のリンクをクリックしてください。
- [**接続サービス**](#connect):Azure リソースとオンプレミス リソースを接続するには、Azure の次のネットワーク サービスを単独で、または組み合わせて使用します - Virtual Network (VNet)、Virtual WAN、ExpressRoute、VPN Gateway、Virtual Network NAT Gateway、Azure DNS, Peering Service、Azure Bastion。
- [**アプリケーション保護サービス**](#protect): アプリケーションを保護するには、Azure の次のネットワーク サービスを単独で、または組み合わせて使用します - Private Link、DDoS Protection、Firewall、ネットワーク セキュリティ グループ、Web Application Firewall、仮想ネットワーク エンドポイント。
- [**アプリケーション配信サービス**](#deliver): Azure ネットワークでアプリケーションを配信するには、Azure の次のネットワーク サービスを単独で、または組み合わせて使用します - Content Delivery Network (CDN)、Azure Front Door Service、Traffic Manager、Application Gateway、Internet Analyzer、Load Balancer。
- [**ネットワークの監視**](#monitor): ネットワーク リソースを監視するには、Azure の次のネットワーク サービスを単独で、または組み合わせて使用します - Network Watcher、ExpressRoute Monitor、Azure Monitor、VNet ターミナル アクセス ポイント (TAP)。

## <a name="connectivity-services"></a><a name="connect"></a>接続サービス
 
このセクションでは、Azure リソース間の接続、オンプレミス ネットワークから Azure リソースへの接続、Azure でのブランチ間接続を実現するサービス (Virtual Network (VNet)、Virtual WAN、ExpressRoute、VPN Gateway、Virtual Network NAT Gateway、Azure DNS、Azure Peering Service、Azure Bastion) について説明します。

|サービス|使用する理由|シナリオ|
|---|---|---|
|[Virtual Network](#vnet)|Azure リソースがお互い同士や、インターネット、オンプレミス ネットワークと安全に通信できるようにします。| <p>[ネットワーク トラフィックをフィルター処理する](../virtual-network/tutorial-filter-network-traffic.md)</p> <p>[ネットワーク トラフィックをルーティングする](../virtual-network/tutorial-create-route-table-portal.md)</p> <p>[リソースへのネットワーク アクセスを制限する](../virtual-network/tutorial-restrict-network-access-to-resources.md)</p> <p>[仮想ネットワークを接続する](../virtual-network/tutorial-connect-virtual-networks-portal.md)</p>|
|[ExpressRoute](#expressroute)|接続プロバイダーが提供するプライベート接続を介して、オンプレミス ネットワークを Microsoft クラウドへと拡張します。|<p>[ExpressRoute 回線の作成と変更](../expressroute/expressroute-howto-circuit-portal-resource-manager.md)</p> <p>[ExpressRoute 回線のピアリングの作成と変更](../expressroute/expressroute-howto-routing-portal-resource-manager.md)</p> <p>[ExpressRoute 回線への VNet のリンク](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)</p> <p>[ExpressRoute 回線のルート フィルターを構成し管理する](../expressroute/how-to-routefilter-portal.md)</p>|
|[VPN Gateway](#vpngateway)|Azure Virtual Network とオンプレミスの場所の間で、パブリック インターネットを介して暗号化されたトラフィックを送信します。|<p>[サイト間接続](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)</p> <p>[VNet 間接続](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)</p> <p>[ポイント対サイト接続](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)</p>|
|[Virtual WAN](#virtualwan)|Azure へのブランチ接続および Azure 経由のブランチ接続の最適化と自動化を行います。 Azure リージョンは、ブランチの接続先として選択できるハブとして機能します。|<p>[サイト間接続](../virtual-wan/virtual-wan-site-to-site-portal.md)、[ExpressRoute 接続](../virtual-wan/virtual-wan-expressroute-portal.md)</p>|
|[Azure DNS](#dns)|Microsoft Azure インフラストラクチャを使用して名前解決を行う DNS ドメインをホストします。|<p>[Azure DNS でドメインをホストする](../dns/dns-delegate-domain-azure-dns.md)</p><p>[Web アプリの DNS レコードを作成する](../dns/dns-web-sites-custom-domain.md)</p> <p>[Traffic Manager のエイリアス レコードを作成する](../dns/tutorial-alias-tm.md)</p> <p>[パブリック IP アドレスのエイリアス レコードを作成する](../dns/tutorial-alias-pip.md)</p> <p>[ゾーン リソース レコードのエイリアス レコードを作成する](../dns/tutorial-alias-rr.md)</p>|
|[Azure Bastion](#bastion)|TLS を使用して Azure portal で直接、仮想マシンへの安全でシームレスな RDP/SSH 接続を構成します。 Azure Bastion 経由で接続する場合、仮想マシンにパブリック IP アドレスは必要ありません。|<p>[Azure Bastion ホストを作成する](../bastion/bastion-create-host-portal.md)</p><p>[SSH を使用して Linux VM に接続する](../bastion/bastion-connect-vm-ssh.md)</p><p>[RDP を使用して Windows VM に接続する](../bastion/bastion-connect-vm-rdp.md)</p>|
|[Virtual Network NAT Gateway](#nat)|仮想マシンに送信接続を提供する NAT Gateway を作成します。|<p>[NAT Gateway を作成する](../virtual-network/quickstart-create-nat-gateway-portal.md)</p>|
|[Azure Peering Service (プレビュー)](#azurepeeringservice)|サービス プロバイダーと協力して、パブリック ネットワーク経由で Microsoft クラウドへの最適で信頼性の高いルーティングを実現します。|<p>[Azure Peering Service を登録する](../peering-service/azure-portal.md)</p>|
||||


### <a name="virtual-network"></a><a name="vnet"></a>Virtual Network

Azure Virtual Network (VNet) は、Azure 内のプライベート ネットワークの基本的な構成要素です。 VNet の用途は次のとおりです。
- **Azure リソース間の通信**:仮想ネットワークに、VM や他のいくつかの種類の Azure リソース (Azure App Service Environment、Azure Kubernetes Service (AKS)、Azure Virtual Machine Scale Sets など) をデプロイできます。 仮想ネットワークにデプロイできる Azure リソースの詳細な一覧については、[仮想ネットワーク サービスの統合](../virtual-network/virtual-network-for-azure-services.md)に関するページを参照してください。
- **相互通信**:仮想ネットワークを相互に接続することで、任意の仮想ネットワークのリソースが仮想ネットワーク ピアリングを使用して相互に通信できるようになります。 接続する仮想ネットワークが属している Azure リージョンは、同じであっても異なっていてもかまいません。 詳細については、「[仮想ネットワーク ピアリング](../virtual-network/virtual-network-peering-overview.md)」をご覧ください。
- **インターネットとの通信**:VNet 内のすべてのリソースにおいて、既定でインターネットへの送信方向の通信が可能です。 リソースへの受信通信は、リソースにパブリック IP アドレスまたはパブリック ロード バランサーを割り当てることによって可能になります。 [パブリック IP アドレス](../virtual-network/virtual-network-public-ip-address.md)やパブリック [ロード バランサー](../load-balancer/load-balancer-overview.md)を使用して送信接続を管理することもできます。
- **オンプレミス ネットワークとの通信**:オンプレミスのコンピューターとネットワークを仮想ネットワークに接続するには、[VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) または [ExpressRoute](../expressroute/expressroute-introduction.md) を使用します。

詳細については、[Azure Virtual Network](../virtual-network/virtual-networks-overview.md)に関するページを参照してください。

### <a name="expressroute"></a><a name="expressroute"></a>ExpressRoute
ExpressRoute では、接続プロバイダーが提供するプライベート接続を介して、オンプレミスのネットワークを Microsoft クラウドに拡張することができます。 この接続はプライベート接続です。 トラフィックはインターネットを経由しません。 ExpressRoute では、Microsoft Azure、Office 365、Dynamics 365 などの Microsoft クラウド サービスへの接続を確立できます。  詳細については、[ExpressRoute](../expressroute/expressroute-introduction.md) に関するページを参照してください。

![Azure ExpressRoute](./media/networking-overview/expressroute-connection-overview.png)

### <a name="vpn-gateway"></a><a name="vpngateway"></a>VPN Gateway
VPN Gateway では、オンプレミスの場所から仮想ネットワークへの暗号化されたクロスプレミス接続を作成したり、VNet 間に暗号化された接続を作成したりすることができます。 VPN Gateway 接続には、サイト間、ポイント対サイト、VNet 間などのさまざまな構成があります。
次の図は、同じ仮想ネットワークへの複数のサイト間 VPN 接続を示しています。

![サイト間の Azure VPN Gateway 接続](./media/networking-overview/vpngateway-multisite-connection-diagram.png)

各種の VPN 接続の詳細については、[VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) に関するページを参照してください。

### <a name="virtual-wan"></a><a name="virtualwan"></a>Virtual WAN
Azure Virtual WAN は、Azure への、または Azure 経由での、最適化および自動化されたブランチ接続を提供するネットワーク サービスです。 Azure リージョンは、ブランチの接続先として選択できるハブとして機能します。 Azure バックボーンを利用してブランチを接続し、ブランチから VNet への接続を利用することもできます。 Azure Virtual WAN は、サイト間 VPN、ExpressRoute、ポイント対サイト ユーザー VPN などの多数の Azure クラウド接続サービスを、1 つの操作インターフェイスにまとめます。 Azure VNet への接続は、仮想ネットワーク接続を使用して確立されます。 詳細については、「[Azure Virtual WAN とは](../virtual-wan/virtual-wan-about.md)」を参照してください。

![Virtual WAN のダイアグラム](./media/networking-overview/virtualwan1.png)

### <a name="azure-dns"></a><a name="dns"></a>Azure DNS
Azure DNS は、DNS ドメインのホスティング サービスであり、Microsoft Azure インフラストラクチャを使用した名前解決を提供します。 Azure でドメインをホストすることで、その他の Azure サービスと同じ資格情報、API、ツール、課金情報を使用して DNS レコードを管理できます。 詳細については、「[Azure DNS とは](../dns/dns-overview.md)」を参照してください。

### <a name="azure-bastion"></a><a name="bastion"></a>Azure Bastion
Azure Bastion サービスは、お使いの仮想ネットワーク内でプロビジョニングする、新しいフル プラットフォームマネージド PaaS サービスです。 これにより、TLS を使用して Azure portal で直接、仮想マシンへの安全でシームレスな RDP/SSH 接続が提供されます。 Azure Bastion 経由で接続する場合、ご自分の仮想マシンにパブリック IP アドレスは必要ありません。 詳細については、[Azure Bastion](../bastion/bastion-overview.md) に関するページを参照してください。

![Azure Bastion のアーキテクチャ](./media/networking-overview/architecture.png)

### <a name="virtual-network-nat-gateway"></a><a name="nat"></a>Virtual Network NAT Gateway
Virtual Network NAT (ネットワーク アドレス変換) は、仮想ネットワーク用のアウトバウンドのみのインターネット接続を簡単に行えるようにするものです。 これをサブネットに対して構成した場合、指定した静的パブリック IP アドレスがすべてのアウトバウンド接続で使用されます。 ロード バランサーや、仮想マシンに直接アタッチされたパブリック IP アドレスがなくても、アウトバウンド接続が可能となります。 詳細については、[Virtual Network NAT Gateway](../virtual-network/nat-overview.md) に関するページを参照してください。 

![Virtual Network NAT Gateway](./media/networking-overview/flow-map.png)

### <a name="azure-peering-service"></a><a name="azurepeeringservice"></a> Azure Peering Service
Azure Peering Service を使用すると、Office 365、Dynamics 365、サービスとしてのソフトウェア (SaaS) サービス、Azure、パブリックインターネット経由でアクセス可能な Microsoft サービスなど、Microsoft クラウド サービスへのお客様の接続が強化されます。 詳細については、[Azure Peering Service ](../peering-service/about.md) に関するページを参照してください。

## <a name="application-protection-services"></a><a name="protect"></a>アプリケーション保護サービス

このセクションでは、ネットワーク リソースの保護に役立つ Azure のネットワーク サービスについて説明します。アプリケーションを保護するには、Azure の次のネットワーク サービスを単独で、または組み合わせて使用します - Private Link、DDoS Protection、Firewall、ネットワーク セキュリティ グループ、Web アプリケーション ファイアウォール、仮想ネットワーク エンドポイント。

|サービス|使用する理由|シナリオ|
|---|---|---|
|[DDoS Protection](#ddosprotection) |過剰な IP トラフィック チャージから保護することでアプリケーションの可用性を高めます。|[Azure DDoS Protection を管理する](../virtual-network/manage-ddos-protection.md)|
|[Web アプリケーション ファイアウォール](#waf)|<p>[Application Gateway での Azure WAF](../web-application-firewall/ag/ag-overview.md) は、パブリックおよびプライベートのアドレス空間のエンティティをリージョン別に保護します。</p><p>[Front Door での Azure WAF](../web-application-firewall/afds/afds-overview.md) は、パブリック エンドポイントをネットワーク エッジで保護します。</p>|<p>[ボット保護規則を構成する](../frontdoor/waf-front-door-policy-configure-bot-protection.md)</p> <p>[カスタム応答コードを構成する](../frontdoor/waf-front-door-configure-custom-response-code.md)</p> <p>[IP 制限規則を構成する](../frontdoor/waf-front-door-configure-ip-restriction.md)</p> <p>[レート制限規則を構成する](../frontdoor/waf-front-door-rate-limit-powershell.md)</p> |
|[Azure Firewall](#firewall)|Azure Firewall は、Azure Virtual Network リソースを保護するクラウドベースのマネージド ネットワーク セキュリティ サービスです。 組み込みの高可用性とクラウドの無制限のスケーラビリティを備えた、完全にステートフルなサービスとしてのファイアウォールです。|<p>[Azure Firewall を VNet にデプロイする](../firewall/tutorial-firewall-deploy-portal.md)</p> <p>[Azure Firewall をハイブリッド ネットワークにデプロイする](../firewall/tutorial-hybrid-ps.md)</p> <p>[Azure Firewall DNAT で受信トラフィックをフィルター処理する](../firewall/tutorial-firewall-dnat.md)</p>|
|[ネットワーク セキュリティ グループ](#nsg)|すべてのネットワーク トラフィック フローを対象に VM/サブネットでエンド ノードをきめ細かく分散制御します。|[ネットワーク セキュリティ グループを使用してネットワーク トラフィックをフィルター処理する](../virtual-network/tutorial-filter-network-traffic.md)|
|[仮想ネットワーク サービス エンドポイント](#serviceendpoints)|一部の Azure サービス リソースへのネットワーク アクセスを、仮想ネットワーク サブネットに制限できます。|[PaaS リソースへのネットワーク アクセスを制限する](../virtual-network/tutorial-restrict-network-access-to-resources-powershell.md)|
[Private Link](#privatelink)|お使いの仮想ネットワーク内のプライベート エンドポイント経由で Azure PaaS サービス (Azure Storage、SQL Database など) と Azure でホストされている顧客所有の、またはパートナー サービスにアクセスできるようにします。|<p>[プライベート エンドポイントを作成する](../private-link/create-private-endpoint-portal.md)</p><p>[Private Link サービスを作成する](../private-link/create-private-link-service-portal.md)</p>|
|||
### <a name="ddos-protection"></a><a name="ddosprotection"></a>DDoS Protection 
[Azure DDoS Protection](../virtual-network/manage-ddos-protection.md) は、きわめて巧妙な DDoS 攻撃への対抗策となります。 このサービスでは、仮想ネットワークにデプロイされたアプリケーションとリソースに対する DDoS 軽減機能が強化されています。 また、Azure DDoS Protection を利用するお客様は、能動的攻撃を受けたときに、DDoS Rapid Response サポートにアクセスして DDoS の専門家に問い合わせることができます。

![DDoS Protection](./media/networking-overview/ddos-protection.png)

### <a name="web-application-firewall"></a><a name="waf"></a>Web Application Firewall

[Azure Web Application Firewall](../web-application-firewall/overview.md) (WAF) は、SQL インジェクションやクロスサイト スクリプティングなどの一般的な Web エクスプロイトや脆弱性から Web アプリケーションを保護します。 Azure WAF は、管理されたルールによって OWASP Top 10 の脆弱性を標準で保護します。 また、お客様はカスタム ルールを構成することもできます。これは、ソース IP 範囲と要求の属性 (ヘッダー、Cookie、フォーム データ フィールド、クエリ文字列パラメーターなど) に基づいて保護を強化するためにお客様が管理するルールです。

お客様は、[Application Gateway での Azure WAF](../application-gateway/waf-overview.md) をデプロイして、パブリックおよびプライベートのアドレス空間のエンティティをリージョン単位で保護することができます。 また、[Front Door での Azure WAF](../frontdoor/waf-overview.md) をデプロイして、パブリック エンドポイントをネットワーク エッジで保護することもできます。

![Web アプリケーション ファイアウォール](./media/networking-overview/waf-overview.png)


### <a name="azure-firewall"></a><a name="firewall"></a>Azure Firewall
Azure Firewall は、Azure Virtual Network リソースを保護するクラウドベースのマネージド ネットワーク セキュリティ サービスです。 Azure Firewall を使用すると、アプリケーションとネットワークの接続ポリシーを、サブスクリプションと仮想ネットワークをまたいで一元的に作成、適用、記録することができます。 Azure Firewall では、外部のファイアウォールが仮想ネットワークからのトラフィックを識別できるよう、仮想ネットワーク リソースに静的パブリック IP アドレスが使用されます。 

Azure Firewall の詳細については、[Azure Firewall のドキュメント](../firewall/overview.md)を参照してください。

![ファイアウォールの概要](./media/networking-overview/firewall-threat.png)

### <a name="network-security-groups"></a><a name="nsg"></a>ネットワーク セキュリティ グループ
ネットワーク セキュリティ グループを使用して、Azure 仮想ネットワークの Azure リソース間のネットワーク トラフィックをフィルター処理できます。 詳細については、[セキュリティの概要](../virtual-network/security-overview.md)に関するページを参照してください。

### <a name="service-endpoints"></a><a name="serviceendpoints"></a>サービス エンドポイント
仮想ネットワーク (VNet) サービス エンドポイントは、直接接続によって仮想ネットワークのプライベート アドレス空間を拡張し、VNet の ID を Azure サービスに提供します。 エンドポイントを使用することで、重要な Azure サービス リソースへのアクセスを仮想ネットワークのみに限定することができます。 VNet から Azure サービスへのトラフィックは常に、Microsoft Azure のバックボーン ネットワーク上に残ります。 詳細については、[仮想ネットワーク サービス エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md)に関するページを参照してください。

![仮想ネットワーク サービス エンドポイント](./media/networking-overview/vnet-service-endpoints-overview.png)

### <a name="azure-private-link"></a><a name="privatelink"></a>Azure Private Link
[Azure Private Link](../private-link/private-link-overview.md) を使用すると、お使いの仮想ネットワーク内のプライベート エンドポイント経由で Azure PaaS サービス (Azure Storage、SQL Database など) と Azure でホストされている顧客所有の、またはパートナー サービスにアクセスできます。
仮想ネットワークとサービスの間のトラフィックは、Microsoft のバックボーン ネットワークを通ります。 パブリック インターネットにサービスを公開する必要はありません。 お使いの仮想ネットワークに独自のプライベート リンク サービスを作成して顧客に提供することができます。

![プライベート エンドポイントの概要](./media/networking-overview/private-endpoint.png)


## <a name="application-delivery-services"></a><a name="deliver"></a>アプリケーション配信サービス

このセクションでは、アプリケーションの配信を支援する Azure のネットワーク サービス (Content Delivery Network、Azure Front Door Service、Traffic Manager、Load Balancer、Application Gateway) について説明します。

|サービス|使用する理由|シナリオ|
|---|---|---|
|[Content Delivery Network](#cdn)|ユーザーに高帯域幅コンテンツを配信します。 CDN では、待ち時間を最小限に抑えるために、エンド ユーザーに近いポイントオブプレゼンス (POP) の場所のエッジ サーバーに、キャッシュされたコンテンツを格納します。|<p>[Web アプリに CDN を追加する](../cdn/cdn-add-to-web-app.md)</p> <p>[HTTPS 経由で Azure CDN のカスタム ドメインを使用してストレージ BLOB にアクセスする](..//cdn/cdn-storage-custom-domain-https.md)</p> <p>[カスタム ドメインを Azure CDN エンドポイントに追加する](../cdn/cdn-map-content-to-custom-domain.md)</p> <p>[Azure Content Delivery Network のカスタム ドメインで HTTPS を構成する](../cdn/cdn-custom-ssl.md?tabs=option-1-default-enable-https-with-a-cdn-managed-certificate)</p>|
|[Azure Front Door Service](#frontdoor)|最適化によって最大限のパフォーマンスを引き出し、瞬時のグローバルなフェールオーバーによって可用性を高めることで、Web トラフィックのグローバルなルーティングの定義、管理、監視を実現します。|<p>[Azure Front Door Service にカスタム ドメインを追加する](../frontdoor/front-door-custom-domain.md)</p> <p>[Front Door カスタム ドメインで HTTPS を構成する](../frontdoor/front-door-custom-domain-https.md)</p><p>[Web Application Firewall の geo フィルタリング ポリシーを設定する](../frontdoor/front-door-tutorial-geo-filtering.md)|
|[Traffic Manager](#trafficmanager)|高可用性と即応性を確保しながら、全世界の Azure リージョン間で DNS に基づいてトラフィックを分散します。|<p> [トラフィックをルーティングして待ち時間を削減する](../traffic-manager/tutorial-traffic-manager-improve-website-response.md)</p><p>[優先エンドポイントにトラフィックをルーティングする](../traffic-manager/traffic-manager-configure-priority-routing-method.md)</p><p> [エンドポイントに重み付けしてトラフィックを制御する](../traffic-manager/tutorial-traffic-manager-weighted-endpoint-routing.md)</p><p>[エンドポイントの地理的な場所に基づいてトラフィックをルーティングする](../traffic-manager/traffic-manager-configure-geographic-routing-method.md)</p> <p> [ユーザーのサブネットに基づくトラフィックのルーティング](../traffic-manager/tutorial-traffic-manager-subnet-routing.md)</p>|
|[Load Balancer](#loadbalancer)|可用性ゾーン間および VNet へのトラフィックのルーティングを行って、リージョンの負荷分散を実現します。 リソース間でトラフィックをルーティングしてリージョンのアプリケーションをビルドすることで、内部の負荷分散を実現します。|<p> [VM へのインターネット トラフィックの負荷分散](../load-balancer/tutorial-load-balancer-standard-manage-portal.md)</p> <p>[仮想ネットワーク内の VM 間でトラフィックの負荷を分散する](../load-balancer/tutorial-load-balancer-standard-internal-portal.md)<p>[特定の VM の特定のポートにトラフィックをポート フォワーディングする](../load-balancer/tutorial-load-balancer-port-forwarding-portal.md)</p><p> [負荷分散規則とアウトバウンド規則を構成する](../load-balancer/configure-load-balancer-outbound-cli.md)</p>|
|[Application Gateway](#applicationgateway)|Azure Application Gateway は、Web アプリケーションに対するトラフィックを管理できる Web トラフィック ロード バランサーです。|<p>[Azure Application Gateway を使用して Web トラフィックを転送する](../application-gateway/quick-create-portal.md)</p><p>[チュートリアル:Azure portal を使用して TLS ターミネーションでアプリケーション ゲートウェイを構成する](../application-gateway/create-ssl-portal.md)</p><p>[URL パスベースのリダイレクトのあるアプリケーション ゲートウェイを作成する](../application-gateway/create-url-route-portal.md) </p>|
|

### <a name="content-delivery-network"></a><a name="cdn"></a>Content Delivery Network
Azure Content Delivery Network (CDN) では、世界中に戦略的に配置された物理ノードにコンテンツをキャッシュすることによって、高帯域幅コンテンツをユーザーに高速配信するためのグローバル ソリューションを開発者に提供します。 Azure CDN の詳細については、[Azure Content Delivery Network](../cdn/cdn-overview.md) に関するページを参照してください。

![Azure CDN](./media/networking-overview/cdn-overview.png)

### <a name="azure-front-door-service"></a><a name="frontdoor"></a>Azure Front Door Service
Azure Front Door Service では、高可用性のために最大限のパフォーマンスと即時グローバル フェイルオーバーを最適化することで、Web トラフィックのグローバル ルーティングを定義、管理、および監視することができます。 Front Door を使用すると、グローバル (複数リージョン) のコンシューマーや企業のアプリケーションを、Azure を使用して世界中のユーザーに発信する、堅牢で高性能なパーソナライズされた最新のアプリケーション、API、およびコンテンツに変換することができます。 詳細については、[Azure Front Door](../frontdoor/front-door-overview.md) に関するページを参照してください。


### <a name="traffic-manager"></a><a name="trafficmanager"></a>Traffic Manager

Azure Traffic Manager は、世界中の Azure リージョン間でサービスへのトラフィックを最適に配分しつつ、高可用性と応答性を実現する DNS ベースのトラフィック ロード バランサーです。 Traffic Manager では、優先順位、重み付け、パフォーマンス、地理的、複数値、サブネットなどのさまざまなトラフィック ルーティング方法で、トラフィックを分散させることができます。 トラフィック ルーティング方法の詳細については、「[Traffic Manager のルーティング方法](../traffic-manager/traffic-manager-routing-methods.md)」を参照してください。

次の図は、Traffic Manager によるエンドポイントの優先順位に基づいたルーティングを示しています。

![Azure Traffic Manager の "優先順位" トラフィック ルーティング方法](./media/networking-overview/priority.png)

Traffic Manager の詳細については、「[Traffic Manager について](../traffic-manager/traffic-manager-overview.md)」を参照してください。

### <a name="load-balancer"></a><a name="loadbalancer"></a>Load Balancer
Azure Load Balancer には、すべての UDP と TCP プロトコル向けの高パフォーマンス、低待ち時間のレイヤー 4 負荷分散機能が備わっています。 受信接続と送信接続を管理します。 パブリックな負荷分散エンドポイントと内部的な負荷分散エンドポイントを構成することができます。 TCP を使用して受信接続をバックエンド プールの送信先にマッピングする規則や、サービスの可用性を管理するための HTTP の正常性プローブ オプションを定義することができます。 ロード バランサーの詳細については、[ロード バランサーの概要](../load-balancer/load-balancer-overview.md)に関する記事を参照してください。

次の図は、インターネットに接続する多層アプリケーションを示しています。外部ロード バランサーと内部ロード バランサーが利用されています。

![Azure Load Balancer の例](./media/networking-overview/load-balancer.png)


### <a name="application-gateway"></a><a name="applicationgateway"></a>Application Gateway
Azure Application Gateway は、Web アプリケーションに対するトラフィックを管理できる Web トラフィック ロード バランサーです。 アプリケーション デリバリー コントローラー (ADC) をサービスとして提供して、さまざまなレイヤー 7 負荷分散機能をアプリケーションで利用できるようにします。 詳細については、「[Azure Application Gateway とは](../application-gateway/overview.md)」を参照してください。

次の図は、Application Gateway を使った URL パスベースのルーティングを示しています。

![Application Gateway の例](./media/networking-overview/figure1-720.png)

## <a name="network-monitoring-services"></a><a name="monitor"></a>ネットワーク監視サービス
このセクションでは、ネットワーク リソースの監視を支援する Azure のネットワーク サービス (Network Watcher、ExpressRoute Monitor、Azure Monitor、仮想ネットワーク TAP) について説明します。

|サービス|使用する理由|シナリオ|
|---|---|---|
|[Network Watcher](#networkwatcher)|接続に関する問題の監視とトラブルシューティング、および VPN、NSG、ルーティングに関する問題の診断を支援するとともに、VM 上でパケットを取り込み、Azure Functions と Logic Apps を使用して診断ツールのトリガーを自動化します。|<p>[VM トラフィック フィルターに関する問題を診断する](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md)</p><p>[VM のルーティングに関する問題を診断する](../network-watcher/diagnose-vm-network-routing-problem.md)</p><p>[VM 間の通信を監視する](../network-watcher/connection-monitor.md)</p><p>[ネットワーク間の通信に関する問題を診断する](../network-watcher/diagnose-communication-problem-between-networks.md)</p><p>[VM との間のネットワーク トラフィックのログを記録する](../network-watcher/network-watcher-nsg-flow-logging-portal.md)</p>|
|[ExpressRoute モニター](#expressroutemonitor)|ネットワークのパフォーマンス、可用性、使用率をリアルタイムで監視できます。また、ネットワーク トポロジの自動検出を支援し、障害の分離を迅速化し、一時的なネットワークの問題を検出します。さらに、過去のネットワーク パフォーマンスの特性の分析を支援し、マルチサブスクリプションをサポートします。|<p>[ExpressRoute に使用する Network Performance Monitor の構成](../expressroute/how-to-npm.md)</p><p>[ExpressRoute の監視、メトリック、およびアラート](../expressroute/expressroute-monitoring-metrics-alerts.md)</p>|
|[Azure Monitor](#azuremonitor)|アプリケーションの実行状況を把握できるようにするとともに、それらに影響する問題やそれらが依存しているリソースを事前に特定します。|<p>[Traffic Manager のメトリックとアラート](../traffic-manager/traffic-manager-metrics-alerts.md)</p><p>[Standard Load Balancer の Azure Monitor 診断](../load-balancer/load-balancer-standard-diagnostics.md)</p><p>[Azure Firewall のログとメトリックを監視する](../firewall/tutorial-diagnostics.md)</p><p>[Azure Web アプリケーション ファイアウォールの監視とログ記録](../frontdoor/waf-front-door-monitor.md)</p>|
|[仮想ネットワーク TAP](#vnettap)|仮想マシンのネットワーク トラフィックをパケット コレクターへ継続的にストリーミングできるようにするとともに、ネットワークとアプリケーションのパフォーマンス管理ソリューションおよびセキュリティ分析ツールを有効にします。|[VNet TAP リソースを作成する](../virtual-network/tutorial-tap-virtual-network-cli.md)|
|

### <a name="network-watcher"></a><a name="networkwatcher"></a>Network Watcher
Azure Network Watcher は、Azure 仮想ネットワーク内のリソースの監視、診断、メトリックの表示、ログの有効化または無効化を行うツールを提供します。 詳細については、「[Azure Network Watcher とは](../network-watcher/network-watcher-monitoring-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)」を参照してください。
### <a name="expressroute-monitor"></a><a name="expressroutemonitor"></a>ExpressRoute Monitor
ExpressRoute 回線のメトリック、リソース ログ、アラートを表示する方法については、「[ExpressRoute の監視、メトリック、およびアラート](../expressroute/expressroute-monitoring-metrics-alerts.md?toc=%2fazure%2fnetworking%2ftoc.json)」を参照してください。
### <a name="azure-monitor"></a><a name="azuremonitor"></a>Azure Monitor
Azure Monitor は、クラウドおよびオンプレミス環境の利用統計情報を収集、分析し、それに対応する包括的なソリューションを提供することにより、アプリケーションの可用性とパフォーマンスを最大化します。 このツールは、ご利用のアプリケーションがどのように実行されているかを把握するのに役立ちます。さらに、このツールにより、そのアプリケーションに影響している問題点およびアプリケーションが依存しているリソースを事前に明らかにしておくことができます。 詳細については、「[Azure Monitor の概要](../azure-monitor/overview.md?toc=%2fazure%2fnetworking%2ftoc.json)」を参照してください。
### <a name="virtual-network-tap"></a><a name="vnettap"></a>仮想ネットワーク TAP
Azure 仮想ネットワーク TAP (ターミナル アクセス ポイント) を使用すると、仮想マシン ネットワークのトラフィックをネットワーク パケット コレクターまたは分析ツールに連続してストリーミングできます。 コレクターまたは分析ツールは、[ネットワーク仮想アプライアンス](https://azure.microsoft.com/solutions/network-appliances/) パートナーから提供されています。 

次の図は、仮想ネットワーク TAP のしくみを示しています。 

![仮想ネットワーク TAP のしくみ](./media/networking-overview/virtual-network-tap-architecture.png)

詳細については、[仮想ネットワーク TAP](../virtual-network/virtual-network-tap-overview.md) に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

- 「[最初の仮想ネットワークの作成](../virtual-network/quick-create-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)」の手順を実行して、最初の VNet を作成し、その VNet にいくつかの VM を接続します。
- [ポイント対サイト接続の構成](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)に関する記事の手順を実行して、コンピューターを VNet に接続します。
- [インターネットに接続するロード バランサーの作成](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)に関する記事の手順を実行して、インターネット トラフィックをパブリック サーバーに負荷分散します。
 
 
   
