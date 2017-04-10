---
title: "ネットワーク セキュリティの概念および Azure における要件 | Microsoft Docs"
description: " この記事により、Microsoft Azure がネットワーク セキュリティ分野で提供している事柄を簡単に理解できるようになります。 ネットワーク セキュリティの中核をなす概念と要件の基本的な説明と、各分野で Azure が提供している事柄に関する情報を提供します。 "
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: TomSh
ms.assetid: bedf411a-0781-47b9-9742-d524cf3dbfc1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/28/2017
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 7fe9111061fed4af6aa720d0b158e5b4f2becd90
ms.lasthandoff: 03/29/2017


---
# <a name="azure-network-security-overview"></a>Azure のネットワーク セキュリティの概要
Microsoft Azure には、アプリケーションとサービスの接続要件をサポートする堅牢なネットワーク インフラストラクチャが組み込まれています。 ネットワーク接続は、Azure に配置されているリソース間、オンプレミスのリソースと Azure でホストされているリソース間、インターネットと Azure 間で可能です。

この記事は、Microsoft Azure がネットワーク セキュリティ分野で提供している機能をより分かりやすくすることを目的としています。 ここではコア ネットワーク セキュリティの概念と要件の基本について説明します。 また、それぞれの分野で Azure が提供している機能についても説明します。 関心がある分野についての理解を深めるのに役立つ他のコンテンツへのリンクも多数記載されています。

この「Azure のネットワーク セキュリティの概要」の記事では、以下の点を重点的に取り上げます。

* Azure のネットワーク
* Azure Network Watcher
* ネットワーク アクセス制御
* セキュリティで保護されたリモート アクセスとクロスプレミス接続
* 可用性
* ログの記録
* 名前解決
* DMZ アーキテクチャ
* [Azure Security Center] (Azure Security Center)

## <a name="azure-networking"></a>Azure のネットワーク
仮想マシンには、ネットワーク接続が必要です。 その要件に対応するため、Azure では、仮想マシンによる Azure Virtual Network への接続が必要となります。 Azure Virtual Network は、物理的な Azure ネットワーク ファブリック上に構築される論理的な構築物です。 各論理 Azure Virtual Network は、他のすべての Azure Virtual Network から分離されています。 これは、自分のデプロイ内のネットワーク トラフィックに他の Microsoft Azure ユーザーがアクセスすることを防ぐ上で役立ちます。

詳細情報:

* [Virtual Network の概要](../virtual-network/virtual-networks-overview.md)

## <a name="azure-network-watcher"></a>Azure Network Watcher
Azure Network Watcher に含まれる多数のネットワーク監視機能は、問題のトラブルシューティングに役立つだけでなく、セキュリティの問題の特定に利用できるまったく新しいツール セットも提供します。

[セキュリティ グループ ビュー](/network-watcher/network-watcher-security-group-view-overview.md)は、仮想マシンの監査とセキュリティのコンプライアンス対応をサポートし、プログラムによる監査を実行して、組織で定義されている基準ポリシーと VM ごとの有効な規則を比較できます。 構成のずれを特定するときにこの機能が役立ちます。

[パケット キャプチャ](/network-watcher/network-watcher-packet-capture-overview.md)を使用すると、仮想マシンとの間のネットワーク トラフィックをキャプチャすることができます。 パケット キャプチャは、ネットワーク統計情報を収集して、アプリケーションに関する問題のトラブルシューティングを支援するだけでなく、ネットワーク不正侵入の調査でも重要な機能を発揮します。 この機能を Azure Functions と一緒に使用することで、Azure の特定のアラートに反応してネットワーク キャプチャを開始することができます。

Azure Network Watcher と、ラボで機能の一部のテストを開始する方法の詳細については、[Azure Network Watcher の監視の概要](/network-watcher/network-watcher-monitoring-overview.md)に関するページを参照してください。

>[!NOTE]
Azure Network Watcher は現在もパブリック プレビュー中であるため、一般公開リリースのサービスと同じレベルの可用性と信頼性がない場合があります。 特定の機能はサポート対象ではなく、機能が制限されることもあります。また、Azure の場所によっては、利用できない場合もあります。 このサービスの可用性とステータスに関する最新の通知については、[Azure の更新情報](https://azure.microsoft.com/updates/?product=network-watcher)に関するページをご覧ください。

## <a name="network-access-control"></a>ネットワーク アクセス制御
ネットワーク アクセス制御は、Azure Virtual Network 内の特定のデバイスまたはサブネットとの間の接続を制限する機能です。 ネットワーク アクセス制御は、アクセスを許可したユーザーとデバイスのみが、仮想マシンとサービスにアクセスできるようにすることを目的としています。 アクセス制御は、仮想マシンまたはサービスとの間の接続を許可する、または拒否する決定に基づきます。

Azure では、いくつかの種類のネットワーク アクセス制御がサポートされています。 チェックの内容は次のとおりです

* ネットワーク層制御
* ルート制御と強制トンネリング
* 仮想ネットワークのセキュリティ アプライアンス

### <a name="network-layer-control"></a>ネットワーク層制御
セキュリティで保護されたデプロイにはいずれも、ある程度のネットワーク アクセス制御が必要です。 ネットワーク アクセス制御は、仮想マシンや、仮想マシン上で実行されるネットワーク サービスを、本当に通信する必要のある他のネットワーク接続デバイスとだけ通信させ、他のすべての接続の試みをブロックすることを目的としています。

基本レベルのネットワーク アクセス制御 (IP アドレス、TCP または UDP プロトコルに基づくもの) を必要とする場合には、ネットワーク セキュリティ グループを使用できます。 ネットワーク セキュリティ グループ (NSG) とは基本的なステートフル パケット フィルタリング ファイアウォールであり、 [5 タプル](https://www.techopedia.com/definition/28190/5-tuple)に基づいてアクセスを制御します。 NSG はアプリケーション層検査も、認証済みのアクセス制御も提供しません。

詳細情報:

* [ネットワーク セキュリティ グループ](../virtual-network/virtual-networks-nsg.md)

### <a name="route-control-and-forced-tunneling"></a>ルート制御と強制トンネリング
Azure Virtual Network におけるルーティング動作を制御する機能は、ネットワーク セキュリティとアクセス制御の重要な機能です。 ルーティングを正しく構成しないと、仮想マシンでホストされるアプリケーションとサービスが、接続を許すつもりのないデバイスに接続する可能性があります。これには潜在的な攻撃者が所有および操作するデバイスが含まれます。

Azure ネットワークは、Azure Virtual Network 上のネットワーク トラフィックのルーティング動作をカスタマイズする機能をサポートしています。 これにより、ユーザーは Azure Virtual Network 内の既定のルーティング テーブル エントリを変更できます。 ルーティング動作を制御すると、特定のデバイス、またはデバイスのグループから Azure Virtual Network に出入りするすべてのトラフィックに、特定の場所を経由させる上で役立ちます。

たとえば、Azure Virtual Network に仮想ネットワーク セキュリティ アプライアンスがあるとします。 Azure Virtual Network との間のすべてのトラフィックに仮想セキュリティ アプライアンスを経由させたいと考えています。 これは、Azure で [ユーザー定義のルート](../virtual-network/virtual-networks-udr-overview.md) を構成することで実現できます。

[強制トンネリング](https://www.petri.com/azure-forced-tunneling)は、サービスがインターネット上のデバイスとの接続を開始する許可を得られないようにするメカニズムです。 これは、着信接続を受け入れて、それに応答することとは異なることにご注意ください。 フロントエンド Web サーバーは、インターネット ホストからの要求に応答することが必要です。したがって、インターネットからのトラフィックがこれらの Web サーバーに着信することは許可され、Web サーバーが応答することも許可されます。

許可すべきでないのは、フロントエンド Web サーバーが送信要求を開始することです。 そのような要求はセキュリティ リスクになり得ます。これらの接続を使用してマルウェアがダウンロードされる恐れがあるためです。 これらのフロントエンド サーバーがインターネットに向けて送信要求を開始する必要があるとしても、それらの要求にオンプレミスの Web プロキシを必ず経由させて、URL フィルタリングとログ記録を活用できます。

別の方法として、強制トンネリングを使用してそれを避けることもできます。 強制トンネリングを有効にすると、インターネットに対するすべての接続はオンプレミス ゲートウェイを必ず経由します。 強制トンネリングは、ユーザー定義ルートを活用して構成できます。

詳細情報:

* [ユーザー定義のルートおよび IP 転送とは](../virtual-network/virtual-networks-udr-overview.md)

### <a name="virtual-network-security-appliances"></a>仮想ネットワークのセキュリティ アプライアンス
ネットワーク セキュリティ グループ、ユーザー定義のルート、強制トンネリングにより、 [OSI モデル](https://en.wikipedia.org/wiki/OSI_model)のネットワーク層とトランスポート層のレベルでセキュリティ保護を行うことはできますが、ネットワーク層レベルより上位のセキュリティを有効にする必要がある場合があります。

たとえば、セキュリティ要件に次の事柄が含まれることがあります。

* アプリケーションに対するアクセスを許可する前の認証と承認
* 侵入検出と侵入応答
* アプリケーション層における上位プロトコルの検査
* URL フィルタリング
* ネットワーク レベルのウイルス対策およびマルウェア対策
* アンチボット保護
* アプリケーション アクセス制御
* 追加の DDoS 保護 (Azure ファブリック自体によって提供される DDoS 保護に追加される保護)

Azure パートナー ソリューションを使用すれば、これらの拡張ネットワーク セキュリティ機能を利用できます。 最新の Azure パートナー ネットワーク セキュリティ ソリューションについては、 [Azure Marketplace](https://azure.microsoft.com/marketplace/) にアクセスし、"security" および "network security" を検索すると見つかります。

## <a name="secure-remote-access-and-cross-premises-connectivity"></a>セキュリティで保護されたリモート アクセスとクロスプレミス接続
Azure リソースのセットアップ、構成、管理は、リモートで実行する必要があります。 また、オンプレミスと Azure パブリック クラウドの両方にコンポーネントがある [ハイブリッド IT](http://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx) ソリューションをデプロイする場合もあります。 こうしたシナリオの場合、セキュリティで保護されたリモート アクセスが必要になります。

Azure のネットワークは、セキュリティで保護されたリモート アクセスの以下のシナリオをサポートしています。

* 個々のワークステーションから Azure Virtual Network への接続
* オンプレミス ネットワークから Azure Virtual Network への VPN による接続
* オンプレミス ネットワークから Azure Virtual Network への専用 WAN リンクによる接続
* Azure Virtual Network どうしの接続

### <a name="connect-individual-workstations-to-an-azure-virtual-network"></a>個々のワークステーションから Azure Virtual Network への接続
Azure の仮想マシンとサービスの管理を個々の開発者や運用担当者に許可する必要がある場合があります。 たとえば、Azure Virtual Network の仮想マシンにアクセスする必要があるのに、セキュリティ ポリシーでは個々の仮想マシンへの RDP または SSH リモート アクセスが許可されていないとします。 この場合、ポイント対サイト VPN 接続を使用できます。

ポイント対サイト VPN 接続では [SSTP VPN](https://technet.microsoft.com/library/cc731352.aspx) プロトコルにより、ユーザーと Azure Virtual Network の間にセキュリティで保護されたプライベート接続をセットアップできます。 VPN 接続を確立すると、ユーザーは VPN リンクを介して Azure Virtual Network 上の任意の仮想マシンに RDP または SSH アクセスができます (ユーザーが認証可能で、承認を受けている場合)。

詳細情報:

* [PowerShell を利用し、仮想ネットワークへのポイント対サイト接続を構成する](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="connect-your-on-premises-network-to-an-azure-virtual-network-with-a-vpn"></a>オンプレミス ネットワークから Azure Virtual Network への VPN による接続
企業ネットワークの全体または一部を Azure Virtual Network に接続したい場合があります。 こうした事態は、企業が[自社のオンプレミス データセンターを Azure に拡張する](https://gallery.technet.microsoft.com/Datacenter-extension-687b1d84)ハイブリッド IT シナリオでよく生じます。 多くの場合、企業はサービスの一部を Azure でホストし、一部をオンプレミスでホストします。たとえば、フロントエンド Web サーバーが Azure にあって、バックエンド データベースがオンプレミスにあるソリューションの場合などです。 こうしたタイプの "クロスプレミス" 接続により、Azure にあるリソースの管理の安全も強化されます。さらに、Active Directory ドメイン コントローラーを Azure に拡張するなどのシナリオに対応できます。

この接続を実現する 1 つの方法は、 [サイト間 VPN](https://www.techopedia.com/definition/30747/site-to-site-vpn)を使用することです。 サイト間 VPN とポイント対サイト VPN の違いは、サイト間 VPN はネットワーク全体 (オンプレミス ネットワークなど) を Azure Virtual Network に接続するのに対し、ポイント対サイト VPN は 1 つのデバイスを Azure Virtual Network に接続する点です。 Azure Virtual Network へのサイト間 VPN は、高度なセキュリティで保護された IPsec トンネル モード VPN プロトコルを使用します。

詳細情報:

* [Azure ポータルを使用してサイト間 VPN 接続を持つ Resource Manager VNet を作成する](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [VPN ゲートウェイの計画と設計](../vpn-gateway/vpn-gateway-plan-design.md)

### <a name="connect-your-on-premises-network-to-an-azure-virtual-network-with-a-dedicated-wan-link"></a>オンプレミス ネットワークから Azure Virtual Network への専用 WAN リンクによる接続
ポイント対サイト VPN 接続とサイト間 VPN 接続は、クロスプレミス接続を有効にする上で効果的です。 しかし、これらの方法には次の欠点があると考える組織もあります。

* VPN 接続ではデータがインターネットを移動します。その結果、これらの接続は、データがパブリック ネットワークを移動することに関する潜在的なセキュリティ問題にさらされます。 また、インターネット接続の信頼性と可用性が保証できません。
* Azure Virtual Network への VPN 接続の最大発信速度は約 200 Mbps であるため、アプリケーションや目的によっては帯域幅に制約があると見なされる可能性があります。

クロスプレミス接続で最高レベルのセキュリティと可用性を必要とする企業は、専用 WAN リンクを使用してリモート サイトに接続するのが一般的です。 Azure なら専用の WAN リンクを使用して、オンプレミスのネットワークを Azure Virtual Network に接続できます。 Azure ExpressRoute によってこれを有効にできます。

詳細情報:

* [ExpressRoute の技術概要](../expressroute/expressroute-introduction.md)

### <a name="connect-azure-virtual-networks-to-each-other"></a>Azure Virtual Network どうしの接続
デプロイのために多数の Azure Virtual Network を使用することが可能です。 そうすることには多くの理由があります。 1 つの理由は管理が簡単になることです。別の理由としてはセキュリティ上の理由を挙げることができます。 異なる Azure Virtual Network にリソースを配置する動機や論理的根拠にかかわらず、それぞれのネットワーク上のリソースを相互に接続したい場合があります。

1 つの Azure Virtual Network 上のサービスを別の Azure Virtual Network 上のサービスに接続するためのオプションとして、インターネットを介して "ループバック" する方法があります。 接続は Azure Virtual Network で開始し、インターネットを経由して、宛先の Azure Virtual Network に戻ります。 このオプションの場合、接続は、インターネット ベースの通信につきもののセキュリティ問題にさらされます。

Azure Virtual Network どうしの間でサイト間 VPN を作成するほうが優れた選択肢となる可能性があります。 この Azure Virtual Network どうしのサイト間 VPN では、前述のクロスプレミスのサイト間 VPN 接続と同じ [IPsec トンネル モード](https://technet.microsoft.com/library/cc786385.aspx) プロトコルを使用します。

Azure Virtual Network どうしでサイト間 VPN 接続を使用する利点は、VPN 接続が Azure ネットワーク ファブリックを介して確立され、インターネットを介して接続することがないという点です。 これによって、インターネットを介して接続するサイト間 VPN に比べてセキュリティ レベルが高まります。

詳細情報:

* [Azure リソース マネージャーと PowerShell を使用した VNet 間の接続の構成](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

## <a name="availability"></a>可用性
可用性は、あらゆるセキュリティ プログラムの重要な構成要素です。 アクセスする必要のある対象にユーザーとシステムがネットワーク経由でアクセスできない場合、サービスが損なわれたと見なされる可能性があります。 Azure のネットワーク テクノロジは、次の高可用性メカニズムをサポートしています。

* HTTP ベースの負荷分散
* ネットワーク レベルの負荷分散
* グローバル負荷分散

負荷分散は、複数のデバイス間で接続を均等に配分するように設計されたメカニズムです。 負荷分散の目的は次のとおりです。

* 可用性の向上 – 複数のデバイス間で接続の負荷を分散すると、1 つ以上のデバイスが使用不可になっても、残りのオンライン デバイスで実行されているサービスが引き続きサービスのコンテンツを処理できます。
* パフォーマンスの向上 – 複数のデバイス間で接続の負荷を分散すると、1 つのデバイスでプロセッサの処理負荷を負わずに済みます。 その代わりに、コンテンツ提供に伴う処理とメモリの要求は、複数のデバイスに分散されます。

### <a name="http-based-load-balancing"></a>HTTP ベースの負荷分散
Web ベースのサービスを実行する企業は、こうした Web サービスの前に HTTP ベースのロード バランサーを配置し、適度なレベルのパフォーマンスと高可用性を確保する上で役立てることを望む場合が少なくありません。 従来のネットワーク ベースのロード バランサーとは対照的に、HTTP ベースのロード バランサーによって行われる負荷分散決定は、ネットワーク層とトランスポート層のプロトコルではなく、HTTP プロトコルの特性に基づきます。

HTTP ベースの負荷分散を Web ベース サービスに提供するため、Azure には、Azure Application Gateway が備わっています。 Azure Application Gateway は次の事柄をサポートしています。

* HTTP ベースの負荷分散 – 負荷分散の決定は、HTTP プロトコルに特有の特性に基づいて行われます。
* Cookie ベースのセッション アフィニティ – この機能を使用すると、ロード バランサーの背後にあるいずれかのサーバーと確立される接続は、クライアントとサーバー間でそのままの状態を維持します。 これにより、トランザクションの安定性が保証されます。
* SSL オフロード – ロード バランサーとの間でクライアント接続が確立されると、クライアントとロード バランサー間のセッションは HTTPS (SSL) プロトコルを使用して暗号化されます。 しかし、パフォーマンスを向上させるため、ロード バランサーと、ロード バランサーの背後の Web サーバーとの間の接続に HTTP (暗号化されていない) プロトコルを使用する選択肢もあります。 これを "SSL オフロード" と呼びます。ロード バランサーの背後にある Web サーバーでは暗号化に伴うプロセッサ オーバーヘッドがなく、結果として要求をより迅速に処理できるためです。
* URL ベースのコンテンツ ルーティング – この機能により、ロード バランサーはターゲット URL に基づいて接続の転送先を決定できます。 これにより、IP アドレスに基づいて負荷分散の決定を行うソリューションに比べて柔軟性がずっと高まります。

詳細情報:

* [Application Gateway の概要](../application-gateway/application-gateway-introduction.md)

### <a name="network-level-load-balancing"></a>ネットワーク レベルの負荷分散
HTTP ベースの負荷分散とは対照的に、ネットワーク レベルの負荷分散は IP アドレスとポート (TCP または UDP) 番号に基づいて負荷分散の決定を行います。
Azure におけるネットワーク レベルの負荷分散の利点を活用するには、Azure Load Balancer を使用できます。 Azure Load Balancer の主要な特性には、次の事柄が含まれます。

* IP アドレスとポート番号に基づくネットワーク レベルの負荷分散
* あらゆるアプリケーション層プロトコルのサポート
* Azure 仮想マシンとクラウド サービスのロール インスタンスへの負荷分散
* インターネットに接続する (外部負荷分散) アプリケーションと仮想マシン、およびインターネットに接続しない (内部負荷分散) アプリケーションと仮想マシンのどちらにも使用可能
* エンドポイント監視 (ロード バランサーの背後にあるサービスが使用不可になっていないか判別するために使用します)

詳細情報:

* [複数の Virtual Machines またはサービス間でインターネットに接続するロード バランサー](../load-balancer/load-balancer-internet-overview.md)
* [内部ロード バランサーの概要](../load-balancer/load-balancer-internal-overview.md)

### <a name="global-load-balancing"></a>グローバル負荷分散
可能な限り最高レベルの可用性が必要な組織もあります。 この目的を実現する 1 つの方法は、グローバルに分散したデータセンターでアプリケーションをホストすることです。 世界中にあるデータセンターでアプリケーションがホストされていれば、1 つの地域全体が使用不可になっても、アプリケーションは引き続き稼働できます。

グローバルに分散したデータセンターでアプリケーションをホストすると、可用性の利点に加え、パフォーマンス上の利点も得られます。 これらのパフォーマンスの利点は、要求を行うデバイスに最も近いデータセンターにサービス要求を送信するメカニズムを使用して得られます。

グローバル負荷分散によって、これらの利点の両方が実現します。 Azure では、Azure Traffic Manager を使用してグローバル負荷分散の利点が得られます。

詳細情報:

* [Traffic Manager について](../traffic-manager/traffic-manager-overview.md)

## <a name="logging"></a>ログの記録
ネットワーク レベルにおけるログ記録は、あらゆるネットワーク セキュリティ シナリオの主要な機能です。 Azure では、ネットワーク セキュリティ グループに関して入手した情報をログに記録して、ネットワーク レベルのログ情報を取得できます。 NSG ログ記録によって、次のログから情報を取得します。

* [アクティビティ ログ](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) – このログは、Azure サブスクリプションに送信されたすべての操作を確認する場合に使用します。 このログは既定で有効になっており、Azure ポータルで使用できます。 以前は "監査ログ" や "操作ログ" と呼ばれていました。
* イベント ログ – これらのログは、適用された NSG ルールについての情報を提供します。
* カウンター ログ – このログを使用すると、トラフィックを拒否または許可するために各 NSG ルールが適用された回数が分かります。

優れたデータ視覚化ツールである [Microsoft Power BI](https://powerbi.microsoft.com/what-is-power-bi/)を使用して、これらのログを表示および分析することもできます。

詳細情報:

* [ネットワーク セキュリティ グループ (NSG) のためのログ分析](../virtual-network/virtual-network-nsg-manage-log.md)


## <a name="name-resolution"></a>名前解決
名前解決は、Azure でホストするすべてのサービスにとって重要な機能です。 セキュリティ上の観点からすると、名前解決機能が危害を受けると、攻撃者によって、要求がユーザー サイトから攻撃者のサイトにリダイレクトされる可能性があります。 名前解決がセキュリティで保護されていることは、クラウドでホストされているすべてのサービスにとって必要条件となっています。

対処する必要がある名前解決は、次の 2 種類です。

* 内部名前解決 – 内部名前解決は、Azure Virtual Network、またはオンプレミス ネットワーク、あるいはその両方のサービスによって使用されます。 内部名前解決に使用される名前にはインターネットを介してアクセスすることはできません。 最適なセキュリティを確保するため、内部名前解決スキームは外部ユーザーからアクセスできないことが重要です。
* 外部名前解決 – 外部名前解決は、オンプレミス ネットワークと Azure Virtual Network の外に位置するユーザーやデバイスによって使用されます。 これらの名前はインターネットから見ることができ、接続をクラウド ベースのサービスに向けるために使用されます。

内部名前解決には、次の 2 つのオプションがあります。

* Azure Virtual Network DNS サーバー – 新しい Azure Virtual Network を作成すると、DNS サーバーが作成されます。 この DNS サーバーは、その Azure Virtual Network にあるマシンの名前を解決できます。 この DNS サーバーは構成することができず、Azure ファブリック マネージャーによって管理されます。その結果、これはセキュリティで保護された名前解決ソリューションとなります。
* 独自の DNS サーバーの導入 – 自分で選んだ独自の DNS サーバーを Azure Virtual Network に配置することもできます。 この DNS サーバーは、Active Directory 統合 DNS サーバーでも、Azure パートナーが提供する専用 DNS サーバー ソリューション (Azure Marketplace から入手可能) でも構いません。

詳細情報:

* [仮想ネットワークの概要](../virtual-network/virtual-networks-overview.md)
* [仮想ネットワーク (VNet) で使用される DNS サーバーの管理](../virtual-network/virtual-networks-manage-dns-in-vnet.md)

外部 DNS 解決には、次の 2 つのオプションがあります。

* 独自の外部 DNS サーバーをオンプレミスでホストする
* 独自の外部 DNS サーバーをサービス プロバイダーによってホストする

多くの大規模な組織は独自の DNS サーバーをオンプレミスでホストします。 それは、そのためのネットワークの専門知識とグローバルな人的/物的リソースが存在しているためです。

ほとんどの場合には、サービス プロバイダーを使用して独自の DNS 名前解決サービスをホストする方が優れています。 こうしたサービス プロバイダーには、名前解決サービスの非常に高い可用性を確保できるネットワークの専門知識とグローバルな人的/物的リソースがあります。 DNS サービスで可用性は不可欠な要素です。名前解決サービスで障害が発生すると、インターネットに接続するサービスに誰もアクセスできなくなるからです。

Azure は、Azure DNS という形で可用性とパフォーマンスの高い外部 DNS ソリューションを提供します。 この外部名前解決ソリューションは、世界規模の Azure DNS インフラストラクチャを活用します。 これによって、他の Azure サービスと同じ資格情報、API、ツール、課金情報を使用して、Azure でドメインをホストできます。 また Azure の一部として、プラットフォームに組み込まれている強力なセキュリティ制御機能も継承します。

詳細情報:

* [Azure DNS の概要](../dns/dns-overview.md)

## <a name="dmz-architecture"></a>DMZ アーキテクチャ
多くのエンタープライズ組織では、DMZ を使用してネットワークをセグメント化し、インターネットとサービスの間に緩衝地帯を設けます。 ネットワークの DMZ 部分はセキュリティの低いゾーンと見なされ、高価値の資産がそのネットワーク セグメントに配置されることはありません。 通常、ネットワーク セキュリティ デバイスの 1 つのネットワーク インターフェイスが DMZ セグメントに置かれ、もう 1 つのネットワーク インターフェイスはインターネットからの受信接続を受け入れる仮想マシンやサービスがあるネットワークに接続されます。

DMZ の設計と DMZ をデプロイする決定には多数のバリエーションがあるため、DMZ を使用することにした場合、使用する種類はネットワーク セキュリティ要件に基づいて決まります。

詳細情報:

* [Microsoft クラウド サービスとネットワーク セキュリティ](../best-practices-network-security.md)

## <a name="azure-security-center"></a>[Azure Security Center] (Azure Security Center)
Security Center は、脅威の回避、検出、対応に役立つサービスで、Azure リソースのセキュリティを高度に視覚化して制御できます。 これにより、Azure サブスクリプション全体に統合セキュリティの監視とポリシーの管理を提供し、気付かない可能性がある脅威を検出し、セキュリティ ソリューションの広範なエコシステムと連動します。

Azure Security Center は、ネットワーク セキュリティの最適化と監視に役立つ次の機能を備えています。

* ネットワーク セキュリティに関する推奨事項を提供する
* ネットワーク セキュリティ構成の状態を監視する
* ネットワーク ベースの脅威をエンドポイント レベルとネットワーク レベルの両方で警告する

詳細情報:

* [Azure Security Center 入門](../security-center/security-center-intro.md)

