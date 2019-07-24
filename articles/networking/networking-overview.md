---
title: Azure のネットワーク | Microsoft Docs
description: Azure のネットワーク サービスとネットワーク機能について説明します。
services: networking
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/19/2017
ms.author: jdial
ms.openlocfilehash: 02db9f2b8cb2ec71d23ad077b90eeacb905d2a16
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58445635"
---
# <a name="azure-networking"></a>Azure のネットワーク

Azure には、単独でまたは組み合わせて使用できるさまざまなネットワーク機能が用意されています。 主な機能については、次のリンクをクリックしてください。
- [Azure リソースの間の接続](#connectivity):クラウド内の安全なプライベート仮想ネットワークで Azure のリソースを接続します。
- [インターネット接続](#internet-connectivity):インターネットを介して双方向で Azure リソースと通信します。
- [オンプレミスの接続](#on-premises-connectivity):インターネット上の仮想プライベート ネットワーク (VPN) を介して、または Azure への専用接続を介して、オンプレミスのネットワークを Azure リソースに接続します。
- [負荷分散とトラフィックの方向](#load-balancing):同じ場所のサーバー間でトラフィックを負荷分散したり、異なる場所のサーバーにトラフィックを誘導したりします。
- [セキュリティ](#security): ネットワーク サブネット間や個々の仮想マシン (VM) 間のネットワーク トラフィックをフィルタリングします。
- [ルーティング](#routing):Azure リソースとオンプレミス リソースとの間で既定のルーティングまたは完全制御のルーティングを使用します。
- [管理性](#manageability):Azure のネットワーク リソースを監視したり管理したりします。
- [デプロイ ツールと構成ツール](#tools):Web ベースのポータルまたはクロスプラットフォームのコマンド ライン ツールを使って、ネットワーク リソースのデプロイと構成を行います。

## <a name="connectivity"></a>Azure リソースの間の接続

Azure リソース (Virtual Machines、Cloud Services、Virtual Machines スケール セット、Azure App Service Environment など) は、Azure Virtual Network (VNet) を介してプライベートに相互通信を行うことができます。 特定の[サブスクリプション](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fnetworking%2ftoc.json)専用に論理上、Azure クラウドを分離したものが VNet です。 各 Azure サブスクリプションと Azure [リージョン](https://azure.microsoft.com/regions)内に複数の VNet を実装できます。 VNet 同士は分離されています。 各 VNet では、次のことを実行できます。

- パブリックおよびプライベート (RFC 1918) アドレスを使用して、カスタム プライベート IP アドレス空間を指定する。 Azure は、VNet に接続されているリソースに、割り当てたアドレス空間のプライベート IP アドレスを割り当てます。
- VNet を 1 つ以上のサブネットに分割し、各サブネットに VNet のアドレス空間の一部を割り当てる。
- Azure で提供される名前解決を使用するか、VNet に接続されたリソースで使用するために独自の DNS サーバーを指定する。

Azure Virtual Network サービスの詳細については、[仮想ネットワークの概要](../virtual-network/virtual-networks-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)に関する記事を参照してください。 VNet を相互に接続できるため、一方の VNet に接続されているリソースは、VNet をまたいで相互に通信できます。 次のオプションのいずれかまたは両方を使用して、複数の VNet を相互に接続できます。

- **ピアリング:** 同じ Azure リージョン内の別の Azure VNet に接続されているリソースの相互通信を可能にします。 複数の VNet の間での帯域幅と待ち時間は、リソースが同じ VNet に接続されている場合と同じです。 ピアリングの詳細については、[仮想ネットワーク ピアリングの概要](../virtual-network/virtual-network-peering-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)に関するページを参照してください。
- **VPN Gateway**:異なる Azure リージョン内の異なる Azure VNet に接続されているリソースの相互通信を可能にします。 VNet 間のトラフィックは、Azure VPN ゲートウェイを経由します。 VNet 間の帯域幅は、ゲートウェイの帯域幅に制限されます。 VNet と VPN ゲートウェイの接続について詳しくは、[リージョンの境界を越えた VNet 間接続の構成](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)に関する記事を参照してください。

## <a name="internet-connectivity"></a>インターネット接続

VNet に接続されているすべての Azure リソースは、既定で、インターネットに送信接続されています。 リソースのプライベート IP アドレスは、Azure インフラストラクチャによりプライベート IP アドレスへの送信元ネットワーク アドレス変換 (SNAT) が行われています。 インターネットへの送信接続の詳細については、「[Azure の送信用接続の詳細](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fnetworking%2ftoc.json)」を参照してください。

インターネットから Azure リソースへの着信接続、または SNAT なしでインターネットへの送信接続を行うには、リソースにパブリック IP アドレスを割り当てる必要があります。 パブリック IP アドレスの詳細については、「[パブリック IP アドレス](../virtual-network/virtual-network-public-ip-address.md?toc=%2fazure%2fnetworking%2ftoc.json)」を参照してください。

## <a name="on-premises-connectivity"></a>オンプレミス接続

VNet 内のリソースには、VPN 接続または直接プライベート接続を介して安全にアクセスすることができます。 Azure 仮想ネットワークとオンプレミスのネットワークとの間でネットワーク トラフィックを送信する場合は、仮想ネットワーク ゲートウェイを作成する必要があります。 ゲートウェイの設定を構成することで、必要なタイプ (VPN または ExpressRoute) の接続を作成します。

オンプレミス ネットワークを VNet に接続するには、次のオプションを組み合わせて使用します。

**ポイント対サイト (VPN over SSTP)**

次の図は、複数のコンピューターと単一の VNet との間の別個のポイント対サイト接続を示しています。

![ポイント対サイト](./media/networking-overview/point-to-site.png)

この接続は 1 台のコンピューターと 1 つの VNet との間で確立されます。 この接続の種類は、既存のネットワークへの変更をほとんどまたはまったく必要としないため、Azure を使い始めたばかりのユーザーまたは開発者に適しています。 また、リモート拠点 (カンファレンス、自宅など) から接続するときにも利便性を発揮します。 ポイント対サイト接続は、同じ仮想ネットワーク ゲートウェイを介してサイト間接続と一体となっていることも少なくありません。 この接続は、SSTP プロトコルを使用して、コンピューターと VNet の間にインターネット経由の暗号化された通信を提供します。 トラフィックがインターネットを経由するため、ポイント対サイト VPN の待ち時間は予測できません。

**サイト対サイト (IPsec/IKE VPN トンネル)**

![サイト間](./media/networking-overview/site-to-site.png)

この接続は、オンプレミスの VPN デバイスと Azure VPN ゲートウェイとの間で確立されます。 この接続の種類では、承認した任意のオンプレミス リソースが VNet にアクセスできます。 この接続は IPSec/IKE VPN で、オンプレミスのデバイスと Azure VPN ゲートウェイの間にインターネット経由の暗号化された通信を提供します。 複数のオンプレミス サイトを同じ VPN ゲートウェイに接続することができます。 各サイトのオンプレミス VPN デバイスには、NAT の内側ではなく外部に公開されたパブリック IP アドレスが必要です。 トラフィックがインターネットを経由するため、サイト間接続の待ち時間は予測できません。

**ExpressRoute (専用のプライベート接続)**

![ExpressRoute](./media/networking-overview/expressroute.png)

この種類の接続は、ExpressRoute のパートナーを介して、ネットワークと Azure との間で確立されます。 この接続はプライベート接続です。 トラフィックはインターネットを経由しません。 トラフィックがインターネットを経由しないため、ExpressRoute 接続の待ち時間は予測可能です。 ExpressRoute は、サイト間接続と組み合わせることができます。

ここまでに説明したすべての接続オプションの詳細については、「[接続トポロジの図](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fnetworking%2ftoc.json)」を参照してください。

## <a name="load-balancing"></a>負荷分散とトラフィック誘導

Microsoft Azure では、ネットワーク トラフィックの分散や負荷分散の方法を管理するための複数のサービスを利用できます。 以下に示した各機能は、単独で使うことも組み合わせて使うこともできます。

**DNS 負荷分散**

Azure Traffic Manager サービスには、グローバルな DNS 負荷分散機能が備わっています。 Traffic Manager では、次のいずれかのルーティング方法に基づき、正常なエンドポイントの IP アドレスがクライアントに返されます。
- **地理的:** DNS クエリの発信元の地理的な場所に基づいてクライアントを特定のエンドポイント (Azure、外部または入れ子になっている) に割り当てます。 クライアントのリージョンを把握し、そのリージョンに基づいてクライアントをルーティングすることが求められるシナリオには、この方法で対応できます。 データ主権規制、コンテンツおよびユーザー エクスペリエンスのローカライズ、さまざまなリージョンからのトラフィックの測定がその例に挙げられます。
- **パフォーマンス:** クライアントに "最も近い" IP アドレスがクライアントに返されます。 "最も近い" エンドポイントが必ずしも地理的な距離で最も近いとは限りません。 この方法では、最も近いエンドポイントが、ネットワーク待ち時間を測定することによって決定されます。 Traffic Manager は、インターネット待ち時間テーブルを管理して、IP アドレス範囲と各 Azure データセンターの間のラウンドトリップ時間を追跡します。
- **優先順位:** トラフィックは、プライマリ (優先順位が一番高い) エンドポイントに誘導されます。 プライマリ エンドポイントを使用できない場合、Traffic Manager は、2 番目のエンドポイントにトラフィックをルーティングします。 プライマリとセカンダリのどちらのエンドポイントも使用できない場合、トラフィックは 3 番目のエンドポイントに送信されます。以降も同様です。 エンドポイントの可用性は、構成済みのステータス (有効または無効) とエンドポイントの継続的な監視に基づきます。
- **重み付けラウンドロビン:** 要求ごとに、利用可能なエンドポイントを Traffic Manager がランダムに選択します。 あるエンドポイントが選択される確率は、すべての利用可能なエンドポイントに割り当てられている重みに基づきます。 すべてのエンドポイント間で同じ重みを使用すると、トラフィック分散は均一になります。 特定のエンドポイントの重みを大きくする (または小さくする) と、それらのエンドポイントは DNS からの応答の頻度が増えます (または減ります)。

次の図は、Web アプリケーションの要求が Web アプリ エンドポイントに誘導されるようすを示したものです。 エンドポイントは、他の Azure サービス (VM、Cloud Services など) でもかまいません。

![Traffic Manager](./media/networking-overview/traffic-manager.png)

クライアントはそのエンドポイントに直接接続します。 Azure Traffic Manager は、エンドポイントに異常が発生したことを検出すると、クライアントを別の正常なエンドポイントに誘導します。 Traffic Manager の詳細については、[Azure Traffic Manager の概要](../traffic-manager/traffic-manager-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)に関する記事を参照してください。

**アプリケーションの負荷分散**

Azure Application Gateway サービスは、アプリケーション配信コントローラー (ADC) をサービスとして提供します。 Application Gateway は、Web アプリケーションを脆弱性やその悪用から保護することができる Web アプリケーション ファイアウォールなど、アプリケーション用のさまざまなレイヤー 7 (HTTP/HTTPS) 負荷分散機能を備えています。 また、CPU に負担をかける SSL ターミネーションを Application Gateway にオフロードすることによって、Web ファームの生産性を最大限に高めることもできます。 

着信トラフィックのラウンド ロビン分散、Cookie ベースのセッション アフィニティ、URL パス ベースのルーティング、単一の Application Gateway の背後で複数の Web サイトをホストする機能など、その他のレイヤー 7 ルーティング機能も用意されています。 Application Gateway は、インターネット接続ゲートウェイ、または内部的にのみ使用されるゲートウェイのいずれかとして構成できるほか、この両方を組み合わせて使用することも可能です。 Application Gateway は Azure によって完全に管理され、非常にスケーラブルで、高い可用性を備えています。 管理しやすいように診断機能とログ機能が豊富に用意されているほか、 Application Gateway の詳細については、[Application Gateway の概要](../application-gateway/application-gateway-introduction.md?toc=%2fazure%2fnetworking%2ftoc.json)に関する記事を参照してください。

次の図は、Application Gateway を使った URL パスベースのルーティングを示しています。

![Application Gateway](./media/networking-overview/application-gateway.png)

**ネットワークの負荷分散**

Azure Load Balancer には、すべての UDP と TCP プロトコル向けの高パフォーマンス、低待ち時間のレイヤー 4 負荷分散機能が備わっています。 受信接続と送信接続を管理します。 パブリックな負荷分散エンドポイントと内部的な負荷分散エンドポイントを構成することができます。 TCP を使用して受信接続をバックエンド プールの送信先にマッピングする規則や、サービスの可用性を管理するための HTTP の正常性プローブ オプションを定義することができます。 ロード バランサーの詳細については、[ロード バランサーの概要](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)に関する記事を参照してください。

次の図は、インターネットに接続する多層アプリケーションを示しています。外部ロード バランサーと内部ロード バランサーが利用されています。

![Load Balancer](./media/networking-overview/load-balancer.png)

## <a name="security"></a>セキュリティ

Azure リソースとの間でやり取りされるトラフィックは、次の方法でフィルタリングすることができます。

- **ネットワーク:** Azure ネットワーク セキュリティ グループ (NSG) を実装することで、Azure リソースの受信トラフィックと送信トラフィックをフィルタリングすることができます。 それぞれの NSG には、受信の規則と送信の規則が含まれています。 トラフィックのフィルタリングに使用する送信元 IP アドレス、送信先 IP アドレス、ポート、プロトコルが、個々の規則によって指定されます。 NSG は個々のサブネットに適用できるほか、個々の VM に適用することができます。 NSG の詳細については、[ネットワーク セキュリティ グループの概要](../virtual-network/security-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)に関する記事を参照してください。
- **アプリケーション:** Web アプリケーション ファイアウォールを備えた Application Gateway を使用することで、Web アプリケーションを脆弱性やその悪用から保護することができます。 よくある攻撃の例としては、SQL インジェクション攻撃やクロス サイト スクリプティング、不正な形式のヘッダーが挙げられます。 Application Gateway は、このようなトラフィックをフィルターで取り除くことにより、Web サーバーへの到達を未然に防ぎます。 どの規則を有効にするかは、設定により変更することができます。 SSL ネゴシエーション ポリシーは設定で変更できるようになっており、特定のポリシーを無効にすることができます。 Web アプリケーション ファイアウォールの詳細については、[Web アプリケーション ファイアウォール](../application-gateway/application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)に関する記事を参照してください。

Azure に備わっていないネットワーク機能が必要になった場合や、オンプレミスでご利用のネットワーク アプリケーションを使う必要が生じた場合は、その製品を VM に実装したうえで VNet に接続することができます。 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances) には、今日普及しているネットワーク アプリケーションを使ってあらかじめ構成されたさまざまな VM が用意されています。 一般に、これらの事前構成済みの VM はネットワーク仮想アプライアンス (NVA) と呼ばれています。 NVA には、ファイアウォールや WAN 最適化などのアプリケーションが含まれています。

## <a name="routing"></a>ルーティング

Azure では、VNet 内の任意のサブネットに接続されている複数のリソースが相互通信できるようにする既定のルート テーブルが作成されます。 次のタイプのいずれかまたは両方のルートを実装して、Azure によって作成される既定のルートをオーバーライドできます。
- **ユーザー定義:** サブネットごとにトラフィックのルーティング先を制御するルートを含む、カスタム ルート テーブルを作成できます。 ユーザー定義ルートについて詳しくは、[ユーザー定義のルート](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)に関する記事をご覧ください。
- **ボーダー ゲートウェイ プロトコル (BGP):** Azure VPN ゲートウェイまたは ExpressRoute 接続を使用して VNet をオンプレミス ネットワークに接続する場合、BGP ルートを VNet に伝達できます。 BGP は、2 つ以上のネットワーク間でルーティングと到達可能性の情報を交換するためにインターネット上で広く使用されている標準のルーティング プロトコルです。 Azure Virtual Network のコンテキストでは、Azure VPN ゲートウェイとオンプレミスの VPN デバイス (BGP ピアまたは BGP 近隣ノードと呼ばれる) が BGP を使用して "ルート" を交換します。これによって、関連するゲートウェイまたはルーターの可用性と、BGP のプレフィックスが到達できる可能性に関する情報が両方のゲートウェイに伝達されます。 また、BGP では、BGP ゲートウェイが特定の BGP ピアから学習したルートを他のすべての BGP ピアに伝達することで、複数のネットワークでトランジット ルーティングを行うこともできます。 BGP の詳細については、[Azure VPN Gateway を使用した BGP の概要](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)に関する記事を参照してください。

## <a name="manageability"></a>管理の容易性

Azure には、ネットワークを監視したり管理したりするための手段として以下のツールが用意されています。
- **アクティビティ ログ:** すべての Azure リソースには、実行された操作とその状態、操作の実行者を記録したアクティビティ ログがあります。 アクティビティ ログの詳細については、[アクティビティ ログの概要](../azure-monitor/platform/activity-logs-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)に関する記事を参照してください。
- **診断ログ:** 定期イベントと自然発生イベントがネットワーク リソースにより作成され、Azure Storage アカウント内に記録されるか、Azure イベント ハブまたは Azure Monitor ログに送信されます。 診断ログを通じて、リソースの正常性についての洞察を得ることができます。 診断ログは、ロード バランサー (インターネット接続)、ネットワーク セキュリティ グループ、ルーティング、Application Gateway で利用できます。 診断ログの詳細については、[診断ログの概要](../azure-monitor/platform/diagnostic-logs-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)に関する記事を参照してください。
- **メトリック:** メトリックとは、リソースに関して一定期間にわたり収集されたパフォーマンスの測定値とカウンターのことです。 メトリックを使用すると、しきい値に基づいてアラートをトリガーすることができます。 現在、Application Gateway に関するメトリックが利用できます。 メトリックの詳細については、[メトリックの概要](../monitoring-and-diagnostics/monitoring-overview-metrics.md?toc=%2fazure%2fnetworking%2ftoc.json)に関する記事を参照してください。
- **トラブルシューティング:** トラブルシューティング情報には、Azure portal から直接アクセスすることができます。 この情報を基に、ExpressRoute 、VPN Gateway、Application Gateway、ネットワーク セキュリティ ログ、ルーティング、DNS、ロード バランサー、Traffic Manager に関する一般的な問題を診断することができます。
- **ロールベースのアクセス制御 (RBAC):** ネットワーク リソースをだれが作成し、だれが管理できるかは、ロールベースのアクセス制御 (RBAC) を使って制御します。 RBAC の詳細については、[RBAC の基礎](../role-based-access-control/overview.md?toc=%2fazure%2fnetworking%2ftoc.json)に関する記事を参照してください。 
- **パケット キャプチャ:** Azure Network Watcher サービスでは、VM 内の拡張機能を通じて VM のパケット キャプチャを実行することができます。 この機能は、Linux VM と Windows VM で利用できます。 パケット キャプチャの詳細については、[パケット キャプチャの概要](../network-watcher/network-watcher-packet-capture-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)に関する記事を参照してください。
- **IP フロー検証:** Azure VM とリモート リソースとの間の IP フローを Network Watcher で検証することにより、パケットが許可 (または拒否) されているかどうかを調べることができます。 管理者は、この機能を利用して、接続の問題をすばやく診断することができます。 IP フローを確認する方法については、[IP フロー検証の概要](../network-watcher/network-watcher-ip-flow-verify-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)に関する記事を参照してください。
- **VPN 接続のトラブルシューティング:** Network Watcher の VPN トラブルシューティング ツールの機能で、接続やゲートウェイを照会し、リソースの正常性を確認することができます。 VPN 接続のトラブルシューティングについて詳しくは、[VPN 接続のトラブルシューティングに関する概要](../network-watcher/network-watcher-troubleshoot-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)についての記事を参照してください。
- **ネットワーク トポロジの表示:** Network Watcher で、VNet 内のネットワーク リソースをグラフィカルに表示します。 ネットワーク トポロジの表示について詳しくは、[トポロジの概要](../network-watcher/network-watcher-topology-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)に関する記事を参照してください。

## <a name="tools"></a>デプロイ ツールと構成ツール

Azure のネットワーク リソースのデプロイと構成は、以下のツールを使って実行できます。

- **Azure portal:** ブラウザーで動作するグラフィカル ユーザー インターフェイスです。 [Azure Portal](https://portal.azure.com)を開きます。
- **Azure PowerShell:** Windows コンピューターから Azure を管理するためのコマンド ライン ツールです。 Azure PowerShell の詳細については、[Azure PowerShell の概要](/powershell/azure/overview?toc=%2fazure%2fnetworking%2ftoc.json)に関する記事を参照してください。
- **Azure コマンド ライン インターフェイス (CLI):** Linux、macOS、または Windows コンピューターから Azure を管理するためのコマンド ライン ツールです。 Azure CLI の詳細については、[Azure CLI の概要](/cli/azure/get-started-with-azure-cli?toc=%2fazure%2fnetworking%2ftoc.json)に関する記事を参照してください。
- **Azure Resource Manager テンプレート:** Azure ソリューションのインフラストラクチャと構成を定義するファイル (JSON 形式) です。 テンプレートを使えば、ソリューションをそのライフサイクル全体で繰り返しデプロイできます。また、常にリソースが一貫した状態でデプロイされます。 テンプレート作成の詳細については、[テンプレート作成のベスト プラクティス](../azure-resource-manager/resource-manager-template-best-practices.md?toc=%2fazure%2fnetworking%2ftoc.json)に関する記事を参照してください。 テンプレートのデプロイには、Azure Portal、CLI、PowerShell のいずれかを使用できます。 今すぐテンプレートを使用するには、あらかじめ構成されているいずれかのテンプレートを「[Azure クイック スタート テンプレート](https://azure.microsoft.com/resources/templates/?term=network)」ライブラリから入手してデプロイしてください。 

## <a name="pricing"></a>価格

Azure ネットワーク サービスには、料金がかかるものと無料で利用できるものがあります。 詳細については、[Virtual Network](https://azure.microsoft.com/pricing/details/virtual-network)、[VPN Gateway](https://azure.microsoft.com/pricing/details/vpn-gateway)、[Application Gateway](https://azure.microsoft.com/pricing/details/application-gateway/)、[ロード バランサー](https://azure.microsoft.com/pricing/details/load-balancer)、[Network Watcher](https://azure.microsoft.com/pricing/details/network-watcher)、[DNS](https://azure.microsoft.com/pricing/details/dns)、[Traffic Manager](https://azure.microsoft.com/pricing/details/traffic-manager)、[ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute) の価格に関するページを参照してください。

## <a name="next-steps"></a>次の手順

- 「[最初の仮想ネットワークの作成](../virtual-network/quick-create-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)」の手順を実行して、最初の VNet を作成し、その VNet にいくつかの VM を接続します。
- [ポイント対サイト接続の構成](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)に関する記事の手順を実行して、コンピューターを VNet に接続します。
- [インターネットに接続するロード バランサーの作成](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)に関する記事の手順を実行して、インターネット トラフィックをパブリック サーバーに負荷分散します。
