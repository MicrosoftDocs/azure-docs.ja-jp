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
ms.date: 10/28/2020
ms.author: kumud
ms.openlocfilehash: d02f85647d947c2f28527ba6f0397c2a98e73840
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98234190"
---
# <a name="azure-networking-services-overview"></a>Azure ネットワーク サービスの概要

Azure のネットワーク サービスにはさまざまなネットワーク機能があり、単独で使用することも組み合わせて使用することもできます。 主な機能については、次のリンクをクリックしてください。
- [**接続サービス**](#connect):Azure リソースとオンプレミス リソースを接続するには、Azure の次のネットワーク サービスを単独で、または組み合わせて使用します - Virtual Network (VNet)、Virtual WAN、ExpressRoute、VPN Gateway、Virtual Network NAT Gateway、Azure DNS, Peering Service、Azure Bastion。
- [**アプリケーション保護サービス**](#protect): アプリケーションを保護するには、Azure の次のネットワーク サービスを単独で、または組み合わせて使用します - Private Link、DDoS Protection、Firewall、ネットワーク セキュリティ グループ、Web Application Firewall、仮想ネットワーク エンドポイント。
- [**アプリケーション配信サービス**](#deliver): Azure ネットワークでアプリケーションを配信するには、Azure の次のネットワーク サービスを単独で、または組み合わせて使用します - Content Delivery Network (CDN)、Azure Front Door Service、Traffic Manager、Application Gateway、Internet Analyzer、Load Balancer。
- [**ネットワークの監視**](#monitor): ネットワーク リソースを監視するには、Azure の次のネットワーク サービスを単独で、または組み合わせて使用します - Network Watcher、ExpressRoute Monitor、Azure Monitor、VNet ターミナル アクセス ポイント (TAP)。

## <a name="connectivity-services"></a><a name="connect"></a>接続サービス
 
このセクションでは、Azure リソース間の接続、オンプレミス ネットワークから Azure リソースへの接続、Azure でのブランチ間接続を実現するサービス (Virtual Network (VNet)、ExpressRoute、VPN Gateway、Virtual WAN、Virtual Network NAT Gateway、Azure DNS、Azure Peering Service、Azure Bastion) について説明します。


### <a name="virtual-network"></a><a name="vnet"></a>Virtual Network

Azure Virtual Network (VNet) は、Azure 内のプライベート ネットワークの基本的な構成要素です。 VNet の用途は次のとおりです。
- **Azure リソース間の通信**:仮想ネットワークに、VM や他のいくつかの種類の Azure リソース (Azure App Service Environment、Azure Kubernetes Service (AKS)、Azure Virtual Machine Scale Sets など) をデプロイできます。 仮想ネットワークにデプロイできる Azure リソースの詳細な一覧については、[仮想ネットワーク サービスの統合](../virtual-network/virtual-network-for-azure-services.md)に関するページを参照してください。
- **相互通信**:仮想ネットワークを相互に接続することで、任意の仮想ネットワークのリソースが仮想ネットワーク ピアリングを使用して相互に通信できるようになります。 接続する仮想ネットワークが属している Azure リージョンは、同じであっても異なっていてもかまいません。 詳細については、「[仮想ネットワーク ピアリング](../virtual-network/virtual-network-peering-overview.md)」をご覧ください。
- **インターネットとの通信**:VNet 内のすべてのリソースにおいて、既定でインターネットへの送信方向の通信が可能です。 リソースへの受信通信は、リソースにパブリック IP アドレスまたはパブリック ロード バランサーを割り当てることによって可能になります。 [パブリック IP アドレス](../virtual-network/virtual-network-public-ip-address.md)やパブリック [ロード バランサー](../load-balancer/load-balancer-overview.md)を使用して送信接続を管理することもできます。
- **オンプレミス ネットワークとの通信**:オンプレミスのコンピューターとネットワークを仮想ネットワークに接続するには、[VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) または [ExpressRoute](../expressroute/expressroute-introduction.md) を使用します。

詳細については、[Azure Virtual Network](../virtual-network/virtual-networks-overview.md)に関するページを参照してください。

### <a name="expressroute"></a><a name="expressroute"></a>ExpressRoute
ExpressRoute では、接続プロバイダーが提供するプライベート接続を介して、オンプレミスのネットワークを Microsoft クラウドに拡張することができます。 この接続はプライベート接続です。 トラフィックはインターネットを経由しません。 ExpressRoute では、Microsoft Azure、Microsoft 365、Dynamics 365 などの Microsoft クラウド サービスへの接続を確立できます。  詳細については、[ExpressRoute](../expressroute/expressroute-introduction.md) に関するページを参照してください。

:::image type="content" source="./media/networking-overview/expressroute-connection-overview.png" alt-text="Azure ExpressRoute" border="false":::

### <a name="vpn-gateway"></a><a name="vpngateway"></a>VPN Gateway
VPN Gateway では、オンプレミスの場所から仮想ネットワークへの暗号化されたクロスプレミス接続を作成したり、VNet 間に暗号化された接続を作成したりすることができます。 VPN Gateway 接続には、サイト間、ポイント対サイト、VNet 間など、さまざまな構成があります。
次の図は、同じ仮想ネットワークへの複数のサイト間 VPN 接続を示しています。

:::image type="content" source="./media/networking-overview/vpngateway-multisite-connection-diagram.png" alt-text="サイト間の Azure VPN Gateway 接続":::

各種の VPN 接続の詳細については、[VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) に関するページを参照してください。

### <a name="virtual-wan"></a><a name="virtualwan"></a>Virtual WAN
Azure Virtual WAN は、Azure への、または Azure 経由での、最適化および自動化されたブランチ接続を提供するネットワーク サービスです。 Azure リージョンは、ブランチの接続先として選択できるハブとして機能します。 Azure バックボーンを利用してブランチを接続し、ブランチから VNet への接続を利用することもできます。 Azure Virtual WAN は、サイト間 VPN、ExpressRoute、ポイント対サイト ユーザー VPN などの多数の Azure クラウド接続サービスを、1 つの操作インターフェイスにまとめます。 Azure VNet への接続は、仮想ネットワーク接続を使用して確立されます。 詳細については、「[Azure Virtual WAN とは](../virtual-wan/virtual-wan-about.md)」を参照してください。

:::image type="content" source="./media/networking-overview/virtualwan1.png" alt-text="Virtual WAN のダイアグラム":::

### <a name="azure-dns"></a><a name="dns"></a>Azure DNS
Azure DNS は、DNS ドメインのホスティング サービスであり、Microsoft Azure インフラストラクチャを使用した名前解決を提供します。 Azure でドメインをホストすることで、その他の Azure サービスと同じ資格情報、API、ツール、課金情報を使用して DNS レコードを管理できます。 詳細については、「[Azure DNS とは](../dns/dns-overview.md)」を参照してください。

### <a name="azure-bastion"></a><a name="bastion"></a>Azure Bastion
Azure Bastion サービスは、お使いの仮想ネットワーク内でプロビジョニングする、新しいフル プラットフォームマネージド PaaS サービスです。 これは、Azure portal 内で直接 TLS を経由して、お使いの仮想マシンへの安全かつシームレスな RDP または SSH 接続を提供します。 Azure Bastion 経由で接続する場合、ご自分の仮想マシンにパブリック IP アドレスは必要ありません。 詳細については、[Azure Bastion](../bastion/bastion-overview.md) に関するページを参照してください。

:::image type="content" source="./media/networking-overview/architecture.png" alt-text="Azure Bastion のアーキテクチャ":::

### <a name="virtual-network-nat-gateway"></a><a name="nat"></a>Virtual Network NAT Gateway
Virtual Network NAT (ネットワーク アドレス変換) は、仮想ネットワーク用のアウトバウンドのみのインターネット接続を簡単に行えるようにするものです。 これをサブネットに対して構成した場合、指定した静的パブリック IP アドレスがすべてのアウトバウンド接続で使用されます。 ロード バランサーや、仮想マシンに直接アタッチされたパブリック IP アドレスがなくても、アウトバウンド接続が可能となります。 詳細については、[仮想ネットワーク NAT ゲートウェイの概要](../virtual-network/nat-overview.md)に関するページを参照してください。

:::image type="content" source="./media/networking-overview/flow-map.png" alt-text="Virtual Network NAT Gateway":::

### <a name="azure-peering-service"></a><a name="azurepeeringservice"></a> Azure Peering Service
Azure Peering Service を使用すると、Microsoft 365、Dynamics 365、サービスとしてのソフトウェア (SaaS) サービス、Azure、パブリック インターネット経由でアクセス可能な Microsoft サービスなど、Microsoft クラウド サービスへのお客様の接続が強化されます。 詳細については、[Azure Peering Service ](../peering-service/about.md) に関するページを参照してください。

### <a name="azure-edge-zones"></a><a name="edge-zones"></a>Azure Edge Zone

Azure Edge Zone は、ユーザーの近くでデータ処理を可能にする Microsoft Azure のオファリング ファミリです。 アプリケーションの低待機時間と高スループットの要件に対応するために、VM、コンテナー、およびその他の選択した Azure サービスを Edge Zone にデプロイできます。 詳細については、[Azure Edge Zone の概要](edge-zones-overview.md)に関する記事を参照してください。

### <a name="azure-orbital"></a><a name="orbital"></a>Azure Orbital

Azure Orbital は、フル マネージドなクラウドベースのサービスとしての地上局です。これを使用すると、宇宙船や人工衛星コンステレーションとの通信、データのダウンリンクやアップリンク、クラウドでのデータ処理、独自のシナリオでの Azure サービスとサービスの連結、およびユーザーの顧客のための製品を生み出すことが可能になります。 このシステムは、Azure グローバル インフラストラクチャと低待機時間のグローバル ファイバー ネットワーク上に構築されています。 詳細については、[Azure Orbital の概要](azure-orbital-overview.md)に関する記事を参照してください。

:::image type="content" source="./media/azure-orbital-overview/orbital-communications-use-flow.png" alt-text="Azure Orbital の通信ダイアグラム":::

## <a name="application-protection-services"></a><a name="protect"></a>アプリケーション保護サービス

このセクションでは、ネットワーク リソースの保護に役立つ Azure のネットワーク サービスについて説明します。アプリケーションを保護するには、Azure の次のネットワーク サービスを単独で、または組み合わせて使用します - DDoS 保護、Private Link、ファイアウォール、Web Application Firewall、ネットワーク セキュリティ グループ、および仮想ネットワーク サービス エンドポイント。

### <a name="ddos-protection"></a><a name="ddosprotection"></a>DDoS Protection 
[Azure DDoS Protection](../ddos-protection/manage-ddos-protection.md) は、きわめて巧妙な DDoS 攻撃への対抗策となります。 このサービスでは、仮想ネットワークにデプロイされたアプリケーションとリソースに対する DDoS 軽減機能が強化されています。 また、Azure DDoS Protection を利用するお客様は、能動的攻撃を受けたときに、DDoS Rapid Response サポートにアクセスして DDoS の専門家に問い合わせることができます。

:::image type="content" source="./media/networking-overview/ddos-protection.png" alt-text="DDoS Protection":::

### <a name="azure-private-link"></a><a name="privatelink"></a>Azure Private Link
[Azure Private Link](../private-link/private-link-overview.md) を使用すると、お使いの仮想ネットワーク内のプライベート エンドポイント経由で Azure PaaS サービス (Azure Storage、SQL Database など) と Azure でホストされている顧客所有の、またはパートナー サービスにアクセスできます。
仮想ネットワークとサービスの間のトラフィックは、Microsoft のバックボーン ネットワークを通ります。 パブリック インターネットにサービスを公開する必要はありません。 お使いの仮想ネットワークに独自のプライベート リンク サービスを作成して顧客に提供することができます。

:::image type="content" source="./media/networking-overview/private-endpoint.png" alt-text="プライベート エンドポイントの概要":::

### <a name="azure-firewall"></a><a name="firewall"></a>Azure Firewall
Azure Firewall は、Azure Virtual Network リソースを保護するクラウドベースのマネージド ネットワーク セキュリティ サービスです。 Azure Firewall を使用すると、アプリケーションとネットワークの接続ポリシーを、サブスクリプションと仮想ネットワークをまたいで一元的に作成、適用、記録することができます。 Azure Firewall では、外部のファイアウォールが仮想ネットワークからのトラフィックを識別できるよう、仮想ネットワーク リソースに静的パブリック IP アドレスが使用されます。 

Azure Firewall の詳細については、[Azure Firewall のドキュメント](../firewall/overview.md)を参照してください。

:::image type="content" source="./media/networking-overview/firewall-threat.png" alt-text="ファイアウォールの概要":::

### <a name="web-application-firewall"></a><a name="waf"></a>Web Application Firewall
[Azure Web Application Firewall](../web-application-firewall/overview.md) (WAF) は、SQL インジェクションやクロスサイト スクリプティングなどの一般的な Web エクスプロイトや脆弱性から Web アプリケーションを保護します。 Azure WAF は、管理されたルールによって OWASP Top 10 の脆弱性を標準で保護します。 また、お客様はカスタム ルールを構成することもできます。これは、ソース IP 範囲と要求の属性 (ヘッダー、Cookie、フォーム データ フィールド、クエリ文字列パラメーターなど) に基づいて保護を強化するためにお客様が管理するルールです。

お客様は、[Application Gateway での Azure WAF](../web-application-firewall/ag/ag-overview.md) をデプロイして、パブリックおよびプライベートのアドレス空間のエンティティをリージョン単位で保護することができます。 また、[Front Door での Azure WAF](../web-application-firewall/afds/afds-overview.md) をデプロイして、パブリック エンドポイントをネットワーク エッジで保護することもできます。

:::image type="content" source="./media/networking-overview/waf-overview.png" alt-text="Web アプリケーション ファイアウォール":::

### <a name="network-security-groups"></a><a name="nsg"></a>ネットワーク セキュリティ グループ
ネットワーク セキュリティ グループを使用して、Azure 仮想ネットワークの Azure リソース間のネットワーク トラフィックをフィルター処理できます。 詳細については、「[ネットワーク セキュリティ グループ](../virtual-network/network-security-groups-overview.md)」を参照してください。

### <a name="service-endpoints"></a><a name="serviceendpoints"></a>サービス エンドポイント
仮想ネットワーク (VNet) サービス エンドポイントは、直接接続によって仮想ネットワークのプライベート アドレス空間を拡張し、VNet の ID を Azure サービスに提供します。 エンドポイントを使用することで、重要な Azure サービス リソースへのアクセスを仮想ネットワークのみに限定することができます。 VNet から Azure サービスへのトラフィックは常に、Microsoft Azure のバックボーン ネットワーク上に残ります。 詳細については、[仮想ネットワーク サービス エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md)に関するページを参照してください。

:::image type="content" source="./media/networking-overview/vnet-service-endpoints-overview.png" alt-text="仮想ネットワーク サービス エンドポイント":::

## <a name="application-delivery-services"></a><a name="deliver"></a>アプリケーション配信サービス

このセクションでは、アプリケーションの配信を支援する Azure のネットワーク サービス (Content Delivery Network、Azure Front Door Service、Traffic Manager、Load Balancer、Application Gateway) について説明します。

### <a name="content-delivery-network"></a><a name="cdn"></a>Content Delivery Network
Azure Content Delivery Network (CDN) では、世界中に戦略的に配置された物理ノードにコンテンツをキャッシュすることによって、高帯域幅コンテンツをユーザーに高速配信するためのグローバル ソリューションを開発者に提供します。 Azure CDN の詳細については、[Azure Content Delivery Network](../cdn/cdn-overview.md) に関する記事を参照してください。

:::image type="content" source="./media/networking-overview/cdn-overview.png" alt-text="Azure CDN":::

### <a name="azure-front-door-service"></a><a name="frontdoor"></a>Azure Front Door Service
Azure Front Door Service では、高可用性のために最大限のパフォーマンスと即時グローバル フェイルオーバーを最適化することで、Web トラフィックのグローバル ルーティングを定義、管理、および監視することができます。 Front Door を使用すると、グローバル (複数リージョン) のコンシューマーや企業のアプリケーションを、Azure を使用して世界中のユーザーに発信する、堅牢で高性能なパーソナライズされた最新のアプリケーション、API、およびコンテンツに変換することができます。 詳細については、[Azure Front Door](../frontdoor/front-door-overview.md) に関するページを参照してください。

:::image type="content" source="./media/networking-overview/front-door-visual-diagram.png" alt-text="Front Door Service の概要":::

### <a name="traffic-manager"></a><a name="trafficmanager"></a>Traffic Manager

Azure Traffic Manager は、世界中の Azure リージョン間でサービスへのトラフィックを最適に配分しつつ、高可用性と応答性を実現する DNS ベースのトラフィック ロード バランサーです。 Traffic Manager では、優先順位、重み付け、パフォーマンス、地理的、複数値、サブネットなどのさまざまなトラフィック ルーティング方法で、トラフィックを分散させることができます。 トラフィック ルーティング方法の詳細については、「[Traffic Manager のルーティング方法](../traffic-manager/traffic-manager-routing-methods.md)」を参照してください。

次の図は、Traffic Manager によるエンドポイントの優先順位に基づいたルーティングを示しています。

:::image type="content" source="./media/networking-overview/priority.png" alt-text="Azure Traffic Manager の &quot;優先順位&quot; トラフィック ルーティング方法":::

Traffic Manager の詳細については、「[Traffic Manager について](../traffic-manager/traffic-manager-overview.md)」を参照してください。

### <a name="load-balancer"></a><a name="loadbalancer"></a>Load Balancer
Azure Load Balancer には、すべての UDP と TCP プロトコル向けの高パフォーマンス、低待ち時間のレイヤー 4 負荷分散機能が備わっています。 受信接続と送信接続を管理します。 パブリックな負荷分散エンドポイントと内部的な負荷分散エンドポイントを構成することができます。 TCP を使用して受信接続をバックエンド プールの送信先にマッピングする規則や、サービスの可用性を管理するための HTTP の正常性プローブ オプションを定義することができます。 ロード バランサーの詳細については、[ロード バランサーの概要](../load-balancer/load-balancer-overview.md)に関する記事を参照してください。

次の図は、インターネットに接続する多層アプリケーションを示しています。外部ロード バランサーと内部ロード バランサーが利用されています。

:::image type="content" source="./media/networking-overview/load-balancer.png" alt-text="Azure Load Balancer の例":::

### <a name="application-gateway"></a><a name="applicationgateway"></a>Application Gateway
Azure Application Gateway は、Web アプリケーションに対するトラフィックを管理できる Web トラフィック ロード バランサーです。 アプリケーション デリバリー コントローラー (ADC) をサービスとして提供して、さまざまなレイヤー 7 負荷分散機能をアプリケーションで利用できるようにします。 詳細については、「[Azure Application Gateway とは](../application-gateway/overview.md)」を参照してください。

次の図は、Application Gateway を使った URL パスベースのルーティングを示しています。

:::image type="content" source="./media/networking-overview/figure1-720.png" alt-text="Application Gateway の例":::

## <a name="network-monitoring-services"></a><a name="monitor"></a>ネットワーク監視サービス
このセクションでは、ネットワーク リソースの監視を支援する Azure のネットワーク サービス (Network Watcher、Azure Monitor for Networks、ExpressRoute Monitor、Azure Monitor、仮想ネットワーク TAP) について説明します。

### <a name="network-watcher"></a><a name="networkwatcher"></a>Network Watcher
Azure Network Watcher は、Azure 仮想ネットワーク内のリソースの監視、診断、メトリックの表示、ログの有効化または無効化を行うツールを提供します。 詳細については、「[Azure Network Watcher とは](../network-watcher/network-watcher-monitoring-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)」を参照してください。

### <a name="azure-monitor-for-networks-preview"></a>Azure Monitor for Networks プレビュー
Azure Monitor for Networks を使用すると、デプロイされたすべてのネットワーク リソースの正常性とメトリックを構成なしで包括的に把握できます。 また、[接続モニター](../network-watcher/connection-monitor-overview.md)、[ネットワーク セキュリティ グループのフロー ログ](../network-watcher/network-watcher-nsg-flow-logging-overview.md)、および [Traffic Analytics](../network-watcher/traffic-analytics.md) などのネットワーク監視機能にアクセスすることもできます。 詳細については、「[Azure Monitor for Networks プレビュー](../azure-monitor/insights/network-insights-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)」を参照してください。

### <a name="expressroute-monitor"></a><a name="expressroutemonitor"></a>ExpressRoute Monitor
ExpressRoute 回線のメトリック、リソース ログ、アラートを表示する方法については、「[ExpressRoute の監視、メトリック、およびアラート](../expressroute/expressroute-monitoring-metrics-alerts.md?toc=%2fazure%2fnetworking%2ftoc.json)」を参照してください。
### <a name="azure-monitor"></a><a name="azuremonitor"></a>Azure Monitor
Azure Monitor は、クラウドおよびオンプレミス環境の利用統計情報を収集、分析し、それに対応する包括的なソリューションを提供することにより、アプリケーションの可用性とパフォーマンスを最大化します。 このツールは、ご利用のアプリケーションがどのように実行されているかを把握するのに役立ちます。さらに、このツールにより、そのアプリケーションに影響している問題点およびアプリケーションが依存しているリソースを事前に明らかにしておくことができます。 詳細については、「[Azure Monitor の概要](../azure-monitor/overview.md?toc=%2fazure%2fnetworking%2ftoc.json)」を参照してください。
### <a name="virtual-network-tap"></a><a name="vnettap"></a>仮想ネットワーク TAP
Azure 仮想ネットワーク TAP (ターミナル アクセス ポイント) を使用すると、仮想マシン ネットワークのトラフィックをネットワーク パケット コレクターまたは分析ツールに連続してストリーミングできます。 コレクターまたは分析ツールは、[ネットワーク仮想アプライアンス](https://azure.microsoft.com/solutions/network-appliances/) パートナーから提供されています。

次の画像は、仮想ネットワーク TAP のしくみを示しています。

:::image type="content" source="./media/networking-overview/virtual-network-tap-architecture.png" alt-text="仮想ネットワーク TAP のしくみ":::

詳細については、[仮想ネットワーク TAP](../virtual-network/virtual-network-tap-overview.md) に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

- 「[最初の仮想ネットワークの作成](../virtual-network/quick-create-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)」の手順を実行して、最初の仮想ネットワークを作成し、そこにいくつかの VM を接続します。
- [ポイント対サイト接続の構成](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)に関する記事の手順を実行して、コンピューターを仮想ネットワークに接続します。
- [インターネットに接続するロード バランサーの作成](../load-balancer/quickstart-load-balancer-standard-public-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)に関する記事の手順を実行して、インターネット トラフィックをパブリック サーバーに負荷分散します。