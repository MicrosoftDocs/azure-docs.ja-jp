---
title: Azure に移行されたワークロードのネットワークの設定に関するベスト プラクティス | Microsoft Docs
description: Azure に移行した後は、移行されたワークロードに対するネットワークの設定に関するベスト プラクティスに従います。
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 12/04/2018
ms.author: raynew
ms.openlocfilehash: 302445038dc9767bd412e232f62fc5249a1a7f09
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/04/2019
ms.locfileid: "55696951"
---
# <a name="best-practices-to-set-up-networking-for-workloads-migrated-to-azure"></a>Azure に移行されたワークロードのネットワークの設定に関するベスト プラクティス

移行を計画して設計するときは、移行自体に加えて、Azure ネットワークの設計と実装が最も重要なステップの 1 つです。 この記事では、Azure の IaaS および PaaS の実装に移行するときのネットワークに関するベスト プラクティスについて説明します。

> [!IMPORTANT]
> この記事で説明するベスト プラクティスと考え方は、Azure プラットフォームと、記事の執筆時点で利用できるサービスの機能に基づいています。 特徴や機能は時間の経過と共に変化します。 お客様のデプロイにすべての推奨事項が当てはまるとは限らないため、お客様にとって適切なものを選択してください。


## <a name="design-virtual-networks"></a>仮想ネットワークを設計する

Azure では仮想ネットワーク (VNet) が提供されます。
- Azure のリソースは、VNet を通して相互に、セキュリティ保護された非公開の通信を直接行います。
- インターネット通信を必要とする VM およびサービスに対する VNet にエンドポイント接続を構成することができます。
- VNet では、サブスクリプション専用に Azure クラウドが論理的に分離されます。
- 各 Azure サブスクリプションと Azure リージョン内に複数の VNet を実装できます。
- VNet 同士は分離されています。
- Vnet は、[RFC 1918](https://tools.ietf.org/html/rfc1918) で定義されている、CIDR 表記で表されたプライベート IP アドレスとパブリック IP アドレスを含むことができます。 パブリック IP アドレスは、インターネットから直接アクセスできません。
- VNet は、VNet ピアリングを使用して相互に接続できます。 同じリージョンの Vnet または異なるリージョンの Vnet のどちらとでも接続できます。 したがって、ある VNet 内のリソースは、他の Vnet 内のリソースに接続できます。
- 既定では、VNet 内のサブネット、接続されている VNet、オンプレミスのネットワーク、およびインターネットの間でトラフィックがルーティングされます。


IP アドレス空間の配置方法、ハブスポーク ネットワークの実装方法、VNet をサブネットに分割する方法、DNS の設定方法、Azure 可用性ゾーンの実装方法など、VNet のトポロジを計画するときは、考えなければならないことが数多くあります。

## <a name="best-practice-plan-ip-addressing"></a>ベスト プラクティス:IP アドレス指定を計画する

移行の一環として VNet を作成するときは、VNet の IP アドレス空間を計画することが重要です。

- 各 VNet に対して CIDR 範囲の /16 より大きいアドレス空間を割り当てないようにする必要があります。 VNet では 65536 個の IP アドレスを使用でき、/16 より小さいプレフィックスを割り当てると IP アドレスが失われます。 RFC 1918 で定義されているプライベート範囲内であっても、IP アドレスを無駄にしないことが重要です。
- VNet アドレス空間は、オンプレミス ネットワークの範囲と重複しないようにする必要があります。
- ネットワーク アドレス変換 (NAT) を使用してはいけません。
- アドレスが重複していると、ネットワークに接続できなくなったり、ルーティングが正常に動作しなくなる可能性があります。 ネットワークが重複する場合は、ネットワークを設計し直すか、ネットワーク アドレス変換 (NAT) を使用する必要があります。

**詳細情報:**

- Azure VNet の[概要を理解する](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)。
- ネットワークに関する FAQ を[読む](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq)。
- ネットワークの制限事項[について学習する](https://docs.microsoft.com/azure/azure-subscription-service-limits?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits)。


## <a name="best-practice-implement-a-hub-spoke-network-topology"></a>ベスト プラクティス:ハブスポーク ネットワーク トポロジを実装する

ハブスポーク ネットワーク トポロジを使用すると、ID やセキュリティなどのサービスは共有されますが、ワークロードは切り離されます。
- ハブは、接続の中心点として機能する Azure VNet です。
- スポークは、VNet ピアリングを使用してハブ VNet に接続する VNet です。
- 共有サービスはハブにデプロイされ、個々のワークロードはスポークとしてデプロイされます。 

以下、具体例に沿って説明します。
- ハブスポーク トポロジを Azure に実装すると、オンプレミス ネットワークへの接続、ファイアウォール、VNet 間の分離などの共通サービスが集中化されます。 ハブ VNet では、オンプレミス ネットワークへの接続の中心点と、スポーク VNet でホストされているワークロードによって使用されるサービスをホストするための場所が提供されます。
- ハブスポーク構成は、通常、大企業で使用されます。 小規模なネットワークでは、もっと単純な設計にしてコストと複雑さを軽減することを検討してください。
- スポーク VNet を使用すると、ワークロードを分離し、各スポークを他のスポークから切り離して管理できます。 各ワークロードには、複数の階層と、Azure ロード バランサーで接続された複数のサブネットを含めることができます。
- ハブとスポークの VNet は異なるリソース グループに実装でき、異なるサブスクリプションに実装することさえできます。 異なるサブスクリプションに属する仮想ネットワークをピアリングする場合、サブスクリプションを同じまたは異なる Azure Active Directory (AD) テナントに関連付けることができます。 これにより、各ワークロードを分散管理しながら、ハブ ネットワークで維持されているサービスを共有できます。

![変更管理](./media/migrate-best-practices-networking/hub-spoke.png)
*ハブとスポークのトポロジ*

**詳細情報:**

- ハブとスポークのトポロジ[について読む](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)。
- Azure [Windows](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/windows-vm) VM および [Linux](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/linux-vm) VM を実行するためのネットワークの推奨事項を確認する。
- VNet ピアリング[について学習する](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)。


## <a name="best-practice-design-subnets"></a>ベスト プラクティス:サブネットを設計する

VNet 内で分離を実現するには、VNet を 1 つ以上のサブネットに分割し、各サブネットに VNet のアドレス空間の一部を割り当てます。
- 各 VNet 内に複数のサブネットを作成することができます。
- 既定では、ネットワーク トラフィックは VNet 内のすべてのサブネット間でルーティングされます。
- サブネットの決定は、技術的要件と組織的要件に基づきます。  
- サブネットを作成するには CIDR 表記を使用します。
- サブネットのネットワーク範囲を決めるときは、各サブネットの 5 個の IP アドレスは Azure によって確保されており、使用できないということに注意することが重要です。 たとえば、使用可能な最小のサブネットである /29 (8 個の IP アドレス) を作成する場合、Azure によって 5 個のアドレスが確保されるため、サブネット上のホストに割り当てることができる使用可能なアドレスは 3 個だけです。
- ほとんどの場合、最小のサブネットとしては /28 を使用することをお勧めします。

### <a name="example"></a>例

次の表では、計画的な移行のためにアドレス空間 10.245.16.0/20 をサブネットに分割した VNet の例を示します。

**サブネット** | **CIDR** | **アドレス** | **用途**
--- | --- | --- | ---
DEV-FE-EUS2 | 10.245.16.0/22 | 1019 | フロントエンド/Web 階層の VM
DEV-APP-EUS2 | 10.245.20.0/22 | 1019 | アプリ階層の VM
DEV-DB-EUS2 | 10.245.24.0/23 | 507 | データベース VM

**詳細情報:**
- サブネットの設計[について学習する](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm#segmentation)。
- 架空の会社 (Contoso) が移行用にネットワーク インフラストラクチャを準備した[方法を学習する](https://docs.microsoft.com/azure/migrate/contoso-migration-infrastructure)。


## <a name="best-practice-set-up-a-dns-server"></a>ベスト プラクティス:DNS サーバーをセットアップする

VNet をデプロイすると、Azure によって既定で DNS サーバーが追加されます。 これにより、迅速に Vnet を構築してリソースをデプロイできます。 ただし、この DNS サーバーでは、その VNet 上のリソースに対してのみサービスが提供されます。 複数の VNet を接続したい場合、または VNet からオンプレミス サーバーに接続したい場合は、追加の名前解決機能が必要です。 たとえば、仮想ネットワーク間で DNS 名を解決するには、Active Directory が必要になる場合があります。 これを行うには、Azure に独自のカスタム DNS サーバーをデプロイします。

- VNet 内の DNS サーバーでは、Azure の再帰的リゾルバーに DNS クエリを転送できます。 これにより、その VNet 内のホスト名を解決することができます。 たとえば、Azure で実行しているドメイン コントローラーでは、それ自体のドメインに対する DNS クエリに応答し、他のすべてのクエリを Azure に転送できます。
- DNS 転送により、VM ではオンプレミスのリソース (ドメイン コントローラー経由) と Azure で提供されるホスト名 (フォワーダー使用) の両方を参照できます。 Azure の再帰的リゾルバーへのアクセスは、仮想 IP アドレス 168.63.129.16 を使用して提供されます。
- また、DNS 転送により、VNet 間の DNS 解決も可能になり、オンプレミスのマシンで Azure によって提供されるホスト名を解決できるようになります。
    - VM のホスト名を解決するには、DNS サーバーの VM が同じ VNet 内に存在し、Azure にホスト名のクエリを転送するように構成されている必要があります。
    - DNS サフィックスは VNet ごとに異なるので、条件付き転送ルールを使用して、解決のための正しい VNet に DNS クエリを送信できます。
- 独自の DNS サーバーを使用する場合は、VNet ごとに複数の DNS サーバーを指定できます。 また、ネットワーク インターフェイス (Azure Resource Manager の場合) またはクラウド サービス (クラシック デプロイ モデルの場合) ごとに複数の DNS サーバーを指定することもできます。
- ネットワーク インターフェイスまたはクラウド サービスに対して指定された DNS サーバーは、VNet に対して指定された DNS サーバーより優先されます。
- Azure Resource Manager デプロイ モデルでは、VNet とネットワーク インターフェイスに対して DNS サーバーを指定できますが、Vnet でのみ設定を使用するのがベスト プラクティスです。

    ![DNS サーバー](./media/migrate-best-practices-networking/dns2.png) *VNet 用の DNS サーバー*

**詳細情報:**
- 独自の DNS サーバーを使用するときの名前解決[について学習する](https://docs.microsoft.com/azure/migrate/contoso-migration-infrastructure)。
- DNS の名前付け規則と制限事項[について学習する](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions?toc=%2fazure%2fvirtual-network%2ftoc.json#naming-subscriptions)。


## <a name="best-practice-set-up-availability-zones"></a>ベスト プラクティス:可用性ゾーンを設定する

可用性ゾーンにより、高可用性が向上し、アプリとデータがデータセンターの障害から保護されます。

- Availability Zones は、Azure リージョン内の一意の物理的な場所です。
- それぞれのゾーンは、独立した電源、冷却手段、ネットワークを備えた 1 つまたは複数のデータセンターで構成されています。
- 回復性を確保するため、有効になっているリージョンにはいずれも最低 3 つのゾーンが別個に存在しています。
- 可用性ゾーンはリージョン内で物理的に分離されているため、データセンターで障害が発生してもアプリケーションとデータは保護されます。
- ゾーン冗長サービスによってアプリケーションとデータが可用性ゾーン全体にレプリケートされ、単一障害点から保護されます。 可用性ゾーンでは、Azure によって 99.99% VM アップタイムの SLA が提供されます。

    ![可用性ゾーン](./media/migrate-best-practices-networking/availability-zone.png) *可用性ゾーン*

- コンピューティング、ストレージ、ネットワーク、およびデータ リソースを 1 つのゾーン内に併置し、他のゾーンにレプリケートすることによって、高可用性を計画し、移行アーキテクチャに組み込むことができます。 可用性ゾーンをサポートしている Azure サービスは、次の 2 つのカテゴリに分類されます。
    - ゾーン ベース サービス:特定のゾーンにリソースを関連付けます。 たとえば、VM、マネージド ディスク、IP アドレスなどです。
    - ゾーン冗長サービス:リソースは、ゾーン間で自動的にレプリケートされます。 たとえば、ゾーン冗長ストレージ、Azure SQL Database などです。
- Azure で負荷分散されてインターネットに接続された標準のワークロードやアプリ階層をデプロイして、ゾーン ベースのフォールト トレランスを提供できます。

    ![ロード バランサー](./media/migrate-best-practices-networking/load-balancer.png) *ロード バランサー*

**詳細情報:**
-   可用性ゾーンの[概要を理解する](https://docs.microsoft.com/azure/availability-zones/az-overview)。



## <a name="design-hybrid-cloud-networking"></a>ハイブリッド クラウド ネットワークを設計する

移行を成功させるには、オンプレミスの企業ネットワークを Azure に接続することが重要です。 これによりハイブリッド クラウド ネットワークと呼ばれる常時接続が作成されて、Azure クラウドから企業ユーザーにサービスが提供されます。 この種のネットワークを作成するには 2 つのオプションがあります。

- **サイト間 VPN**:互換性のあるオンプレミスの VPN デバイスと、VNet にデプロイされた Azure VPN ゲートウェイの間に、サイト間接続を確立します。 許可されたオンプレミスのリソースはすべて、VNet にアクセスできます。 サイト間通信は、インターネット経由で暗号化されたトンネルを通して送信されます。 
- **Azure ExpressRoute:** ExpressRoute パートナーを介して、オンプレミス ネットワークと Azure の間に、Azure ExpressRoute 接続を確立します。 この接続は非公開であり、トラフィックはインターネットを経由しません。

**詳細情報:**

- ハイブリッド クラウド ネットワークについて[詳しく学習する](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/vpn)。

## <a name="best-practice-implement-a-highly-available-site-to-site-vpn"></a>ベスト プラクティス:高可用性のサイト間 VPN を実装する

サイト間 VPN を実装するには、Azure で VPN ゲートウェイを設定します。
- VPN ゲートウェイは、特定の種類の VNet ゲートウェイであり、Azure の VNet とオンプレミスの場所の間でパブリック インターネットを介して暗号化されたトラフィックを送信するために使用されます。
- VPN ゲートウェイを使用すると、Microsoft ネットワークを経由して Azure VNet 間で暗号化されたトラフィックを送信することもできます。
- 各 VNet に設定できる VPN ゲートウェイは 1 つだけです。
- 同じ VPN ゲートウェイに対して複数の接続を作成することができます。 複数の接続を作成すると、利用できるゲートウェイ帯域幅がすべての VPN トンネルによって共有されます。
- すべての Azure VPN Gateway は、アクティブ/スタンバイ構成の 2 つのインスタンスから成ります。
    - アクティブなインスタンスに対して計画メンテナンスまたは計画外の中断が発生すると、フェールオーバーが行われて、スタンバイ インスタンスが自動的に引き継ぎ、サイト間接続または VNet 間接続が再開されます。 
    - 切り替えでは、短い中断が発生します。
    - 計画メンテナンスの場合は、10 ～ 15 秒以内に接続が復元されます。
    - 予期しない問題の場合は、接続の復旧にかかる時間は長くなり、約 1 分から最悪の場合は 1 分 30 秒かかります。
    - ゲートウェイへのポイント対サイト (P2S) VPN クライアント接続が切断され、ユーザーはクライアント コンピューターから再接続する必要があります。

サイト間 VPN を設定するときは次のようにします。
 - VPN が接続するオンプレミス ネットワークとアドレス範囲が重複していない VNet が必要です。
 - ネットワーク内にゲートウェイ サブネットを作成します。
 - VPN ゲートウェイを作成し、ゲートウェイの種類 (VPN) と、ゲートウェイがポリシー ベースかルート ベースかを指定します。 機能が豊富で将来も対応できるので、RouteBased VPN をお勧めします。
 - オンプレミスにローカル ネットワーク ゲートウェイを作成し、オンプレミスの VPN デバイスを構成します。 
 - VNet ゲートウェイとオンプレミス デバイスの間に、フェールオーバー サイト間 VPN 接続を作成します。 ルート ベースの VPN を使用すると、Azure に対してアクティブ/パッシブ接続またはアクティブ/アクティブ接続できます。 また、ルート ベースでは、サイト間 (任意のコンピューターから) 接続とポイント対サイト (1 台のコンピューターから) 接続の両方が同時にサポートされます。
 - 使用するゲートウェイ SKU を指定します。 これは、ワークロードの要件、スループット、機能、および SLA に依存します。
 - Border Gateway Protocol (BGP) は、Azure ExpressRoute ゲートウェイとルート ベースの VPN ゲートウェイで使用できるオプション機能であり、オンプレミスの BGP ルートが VNet に伝達されます。

![VPN](./media/migrate-best-practices-networking/vpn.png)
*サイト間 VPN*
 
**詳細情報:**

- 互換性のあるオンプレミス VPN デバイスを[確認する](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices)。
- VPN ゲートウェイの[概要を理解する](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)。
- 高可用性 VPN 接続[について学習する](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-highlyavailable)。
- VPN ゲートウェイの計画と設計[について学習する](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design)。
- VPN ゲートウェイの設定を[確認する](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings#gwsku)。
- ゲートウェイの SKU を[確認する](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways#gwsku)。
- Azure VPN ゲートウェイでの BGP の設定[について読む](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-overview)。


### <a name="best-practice-configure-a-gateway-for-vpn-gateways"></a>ベスト プラクティス:VPN ゲートウェイ用にゲートウェイを構成する

Azure で VPN ゲートウェイを作成するときは、GatewaySubnet という名前の特別なサブネットを使用する必要があります。 このサブネットを作成するときは、以下のベスト プラクティスに注意してください。

- ゲートウェイ サブネットのプレフィックスの最大長は 29 (例:10.119.255.248/29) です。 現在の推奨事項は、27 (例: 10.119.255.224/27) のプレフィックス長を使用することです。
- ゲートウェイ サブネットのアドレス空間を定義するときは、VNet アドレス空間の最後の部分を使用します。
- Azure GatewaySubnet を使用するときは、VM または Application Gateway などの他のデバイスを、ゲートウェイ サブネットにデプロイしないでください。
- このサブネットにはネットワーク セキュリティ グループ (NSG) を割り当てないでください。 ゲートウェイが機能を停止する原因になります。

**詳細情報:**
- [このツールを使用](https://gallery.technet.microsoft.com/scriptcenter/Address-prefix-calculator-a94b6eed)して IP アドレス空間を決定する。

## <a name="best-practice-implement-azure-virtual-wan-for-branch-offices"></a>ベスト プラクティス:ブランチ オフィス用に Azure Virtual WAN を実装する

複数の VPN 接続がある場合、Azure Virtual WAN は、Azure を介して最適化および自動化されたブランチ間接続を提供するネットワーク サービスです。
- Virtual WAN を使用すると、ブランチ デバイスを接続および構成して Azure と通信できます。 これは、手動で行うことも、Virtual WAN パートナーを介して推奨プロバイダー デバイスを使用して行うこともできます。
- 推奨プロバイダー デバイスを使用すると、使いやすさ、接続性、および構成管理を実現できます。
- Azure WAN の組み込みダッシュボードにはすぐに使用できるトラブルシューティング用分析情報が用意されているため、時間を節約でき、大規模なサイト間接続を簡単に追跡できます。 

**詳細情報:**
 Azure Virtual WAN [について学習する](https://docs.microsoft.com/azure/virtual-wan/virtual-wan-about)。

### <a name="best-practice-implement-expressroute-for-mission-critical-connections"></a>ベスト プラクティス:ミッション クリティカルな接続用に ExpressRoute を実装する

Azure ExpressRoute サービスを使用すると、仮想 Azure データセンターとオンプレミス ネットワークの間にプライベート接続を作成することで、オンプレミスのインフラストラクチャを Microsoft クラウドに拡張できます。
- ExpressRoute 接続では、任意の環境間 (IP VPN) のネットワーク、ポイント ツー ポイント イーサネット ネットワーク、または接続プロバイダーを使用できます。 パブリック インターネットは経由しません。
- ExpressRoute 接続では、強固なセキュリティ、信頼性、高い速度 (最大 10 Gbps)、および一貫した待機時間が提供されます。
- ExpressRoute では、ユーザーはプライベート接続に関連付けられたコンプライアンス規則のベネフィットを得られるので、仮想データセンターに適しています。
- より広い帯域幅が必要な場合は、ExpressRoute Direct を使用すると、100 Gbps で Microsoft ルーターに直接接続することができます。
- ExpressRoute では、BGP を使用して、オンプレミス ネットワーク、Azure インスタンス、Microsoft パブリック アドレスの間でルートが交換されます。

通常、ExpressRoute 接続のデプロイでは、ExpressRoute サービス プロバイダーと連携する必要があります。 すぐに使い始めたい場合は、通常、最初にサイト間 VPN を使用して仮想データセンターとオンプレミス リソースの間に接続を確立した後、サービス プロバイダーとの物理相互接続が確立されたら ExpressRoute 接続に移行します。

**詳細情報:**
- ExpressRoute の[概要を理解する](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)。
- ExpressRoute Direct [について学習する](https://docs.microsoft.com/azure/expressroute/expressroute-erdirect-about)。

### <a name="best-practice-optimize-expressroute-routing-with-bgp-communities"></a>ベスト プラクティス:BGP コミュニティで ExpressRoute のルーティングを最適化する

ExpressRoute 回線が複数あるとき、Microsoft への接続経路は複数存在します。 その結果、ルーティングが最適ではなくなり、ユーザーのネットワークから Microsoft へのトラフィックのパスおよび Microsoft からユーザーのネットワークへのパスが、長くなる可能性があります。 ネットワーク パスが長くなるほど、遅延は大きくなります。 遅延は、アプリのパフォーマンスとユーザー エクスペリエンスに直接影響します。

#### <a name="example"></a>例

例を見てみましょう。

- 米国のロサンゼルスとニューヨークにそれぞれ 1 つオフィスがあります。
- オフィスは WAN で接続されています。WAN は、自社のバックボーン ネットワークでも、サービス プロバイダーの IP VPN でもかまいません。
- また ExpressRoute 回線が 2 つ存在します。1 つは米国西部に、もう 1 つは米国東部にあり、それらも WAN に接続されています。 オフィスから Microsoft のネットワークには、明らかに 2 とおりの接続経路があります。


 
**問題** 米国西部と米国東部の両方に Azure のデプロイ (Azure App Service など) があるものとします。
- 最適なエクスペリエンスのため、各オフィスのユーザーが最も近い Azure サービスにアクセスできるようにしたいと思います。
- そのため、ロサンゼルスのユーザーは Azure 米国西部に接続し、ニューヨークのユーザーは Azure 米国東部に接続するようにします。
- これは、東海岸のユーザーに対しては機能しますが、西海岸に対しては機能しません。 次のような問題があります。
    - 各 ExpressRoute 回線では、Azure 米国東部 (23.100.0.0/16) と Azure 米国西部 (13.100.0.0/16) 両方のプレフィックスがアドバタイズされています。
    - どのプレフィックスがどのリージョンからのものかがわからないと、プレフィックスの処理を変えることができません。
    - WAN ネットワークでは、両方のプレフィックスが米国西部より米国東部に近いものと見なされ、したがって両方のオフィスからのユーザーが米国東部の ExpressRoute 回線にルーティングされて、ロサンゼルス オフィスのユーザーのエクスペリエンスが最適ではなくなる可能性があります。

![VPN](./media/migrate-best-practices-networking/bgp1.png)
*BGP コミュニティの最適化されていない接続*

**ソリューション**

両方のオフィスのユーザーに対するルーティングを最適化するには、どちらのプレフィックスが Azure 米国西部からのもので、どちらが Azure 米国東部からのものかを知る必要があります。 この情報は、BGP コミュニティ値を使用してコード化できます。
- 各 Azure リージョンに一意の BGP コミュニティ値を割り当てます。 たとえば、米国東部には 12076:51004 を割り当て、米国西部には 12076:51006 を割り当てます。
- このようにすると、どのプレフィックスがどの Azure リージョンに属しているかが明確になり、優先 ExpressRoute 回線を構成できます。
- BGP を使用してルーティング情報を交換しているので、BGP のローカル優先設定を使用してルーティングを制御できます。
- この例では、13.100.0.0/16 に割り当てるローカル優先設定の値を米国西部では米国東部よりも大きくし、同様に、23.100.0.0/16 に割り当てるローカル優先設定の値は米国西部より米国東部の方を大きくします。 
- この構成により、Microsoft に対して両方のパスを使用できる場合、ロサンゼルスのユーザーは西部の回線を使用して Azure 米国西部に接続し、ニューヨークのユーザーは東部の回線を使用して Azure 米国東部に接続します。 両側でルーティングが最適化されます。

![VPN](./media/migrate-best-practices-networking/bgp2.png)
*BGP コミュニティの最適化された接続*


**詳細情報:**
- ルーティングの最適化[について学習します](https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing)

## <a name="securing-vnets"></a>VNet のセキュリティ保護

VNet をセキュリティ保護する責任は、Microsoft とユーザーの両方にあります。 Microsoft では、多くのネットワークの機能だけでなく、リソースの安全性を確保するサービスが提供されています。 VNet のセキュリティを設計するときは、境界ネットワークの実装、フィルター処理とセキュリティ グループの使用、リソースと IP アドレスへのアクセスのセキュリティ保護、攻撃に対する保護の実装など、従う必要があるさまざまなベスト プラクティスがあります。

**詳細情報:**

- ネットワーク セキュリティに対するベスト プラクティスの[概要を理解する](https://docs.microsoft.com/azure/security/azure-security-network-security-best-practices)。
- セキュリティで保護されたネットワークを設計する[方法を学習する](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm#security)。

## <a name="best-practice-implement-an-azure-perimeter-network"></a>ベスト プラクティス:Azure 境界ネットワークを実装する

Microsoft は、クラウド インフラストラクチャの保護に多額の投資を行っていますが、ユーザーも自身のクラウド サービスとリソース グループを保護する必要があります。 セキュリティの多層アプローチにより、最大限の保護が実現されます。 境界ネットワークを設けることは、その防御戦略の重要な部分です。

- 境界ネットワークでは、内部のネットワーク リソースが信頼されていないネットワークから保護されます。 
- インターネットに公開される最も外側の層になります。 一般にインターネットとエンタープライズ インフラストラクチャの間に配置され、通常は両方の側で何らかの保護が行われます。 
- 一般的なエンタープライズ ネットワーク トポロジでは、コア インフラストラクチャは複数層のセキュリティ デバイスを備えた境界で厳重に防備されています。 各層の境界は、デバイスとポリシー適用ポイントで構成されます。
- 各層には、ファイアウォール、サービス拒否 (DoS) 防止、侵入検出/侵入防止システム (IDS/IPS)、VPN デバイスなどのネットワーク セキュリティ ソリューションを組み合わせたものを含めることができます。
- 境界ネットワークでのポリシーの適用では、ファイアウォール ポリシー、アクセス制御リスト (ACL)、または特定のルーティングを使用できます。
- インターネットから届いた受信トラフィックは、防御ソリューションの組み合わせによってインターセプトされて処理され、攻撃や有害なトラフィックはブロックされるのに対し、正当な要求はネットワークに入るのを許可されます。
- 受信トラフィックは、境界ネットワーク内のリソースに直接ルーティングできます。 境界ネットワークのリソースでは、ネットワークのより深い場所にある他のリソースと通信し、検証後にトラフィックをネットワークに転送できます。

次の図は、2 つのセキュリティ境界を持つ企業ネットワークにおける単一のサブネット境界ネットワークの例を示しています。

![VPN](./media/migrate-best-practices-networking/perimeter.png)
*境界ネットワークのデプロイ*

**詳細情報:**
- Azure とオンプレミス データセンターの間への境界ネットワークのデプロイ[について学習する](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)。


## <a name="best-practice-filter-vnet-traffic-with-nsgs"></a>ベスト プラクティス:NSG で VNet トラフィックをフィルター処理する

ネットワーク セキュリティ グループ (NSG) には、リソースとの間でやり取りされるトラフィックをフィルター処理する複数の受信および送信セキュリティ規則が含まれます。 フィルター処理は、送信元と宛先の IP アドレス、ポート、およびプロトコルで行うことができます。 
- NSG には、何種類かの Azure リソースへの受信ネットワーク トラフィック (またはリソースからの送信ネットワーク トラフィック) を許可または拒否するセキュリティ規則が含まれます。 各規則で、送信元と送信先、ポート、およびプロトコルを指定することができます。
- 5 組の情報 (送信元、送信元ポート、宛先、宛先ポート、プロトコル) を使用して、優先度で NSG セキュリティ規則が評価されて、トラフィックが許可または拒否されます。
- 既存の接続に対するフロー レコードが作成されます。 そのフロー レコードの接続の状態に基づいて、通信が許可または拒否されます。
- フロー レコードにより NSG はステートフルになることができます。 たとえば、ポート 80 経由の任意のアドレスに対する送信セキュリティ規則を指定する場合、送信トラフィックへの応答に対する受信セキュリティ規則は必要はありません。 通信が外部から開始された場合は、受信セキュリティ規則のみを指定する必要があります。
- 反対の場合も同じです。 ポートで受信トラフィックが許可されている場合、そのポートでのトラフィックに応答するために、送信セキュリティ規則を指定する必要はありません。
- フローを有効にしたセキュリティ規則を削除したとき、既存の接続は中断されません。 接続が停止されるとトラフィック フローは中断され、少なくとも数分間は、どちらの方向にもトラフィックが流れません。
- NSG を作成するときは、必要最小限の数だけ作成します。

### <a name="best-practice-secure-northsouth-and-eastwest-traffic"></a>ベスト プラクティス:North/South および East/West トラフィックをセキュリティ保護する

VNet をセキュリティで保護するときは、攻撃ベクトルを考慮することが重要です。
- サブネットの NSG のみを使用すると環境は簡素化されますが、セキュリティ保護されるのはサブネットに入ってくるトラフィックだけです。 これは North/South トラフィックと呼ばれます。
- 同じサブネット上の VM 間のトラフィックは、East/West トラフィックと呼ばれます。
- ハッカーが外部からアクセスした場合に、同じサブネット内に存在するマシンを接続しようとすると停止されるように、両方の保護の形式を利用することが重要です。

### <a name="use-service-tags-on-nsgs"></a>NSG でサービス タグを使用する

サービス タグは、IP アドレスのプレフィックスのグループを表します。 サービス タグを使用すると、NSG ルールを作成するときの複雑さを軽減できます。
- 規則を作成するときは、特定の IP アドレスの代わりにサービス タグを使用できます。
- サービス タグに関連付けられているアドレス プレフィックスの管理は Microsoft が行い、アドレスが変化するとサービス タグは自動的に更新されます。
- 独自のサービス タグを作成したり、タグに含まれる IP アドレスを指定したりすることはできません。

サービス タグを使用すると、Azure サービスのグループに規則を割り当てるときの手作業がなくなります。 たとえば、Web サーバーが含まれる VNet サブネットに Azure SQL Database へのアクセスを許可する場合は、ポート 1433 に対する送信規則を作成して、**Sql** サービス タグを使用します。
- この **Sql** タグは、Azure SQL Database サービスおよび Azure SQL Data Warehouse サービスのアドレス プレフィックスを表します。
- 値として **Sql** を指定した場合、SQL へのトラフィックが許可または拒否されます。
- 特定のリージョンの **Sql** へのアクセスのみを許可する場合は、そのリージョンを指定することができます。 たとえば、米国東部リージョンの Azure SQL Database へのアクセスのみを許可する場合は、サービス タグとして **Sql.EastUS** と指定できます。
- タグはサービスだけを表し、サービスの特定のインスタンスは表しません。 たとえば、タグは Azure SQL Database サービスを表しますが、特定の SQL データベースや SQL サーバーは表しません。
- このタグで表されるすべてのアドレス プレフィックスは、**Internet** タグでも表されます。


**詳細情報:**

- NSG [について読む](https://docs.microsoft.com/azure/virtual-network/security-overview)。
- NSG に使用可能なサービス タグを[確認する](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags)。


## <a name="best-practice-use-application-security-groups"></a>ベスト プラクティス:アプリケーション セキュリティ グループを使用する

アプリケーション セキュリティ グループを使用すると、アプリの構造の自然な拡張として、ネットワーク セキュリティを構成できます。

- アプリケーション セキュリティ グループに基づいて、VM をグループ化し、ネットワーク セキュリティ ポリシーを定義できます。
- アプリケーション セキュリティ グループでは、明示的な IP アドレスを手動でメンテナンスせずに、大きなセキュリティ ポリシーを再利用することができます。
- アプリケーション セキュリティ グループによって明示的な IP アドレスと複数の規則セットの複雑さが処理されるので、ユーザーはビジネス ロジックに専念することができます。 

### <a name="example"></a>例

![アプリケーション セキュリティ グループ](./media/migrate-best-practices-networking/asg.png)
*アプリケーション セキュリティ グループの例*

**ネットワーク インターフェイス** | **アプリケーション セキュリティ グループ**
--- | ---
NIC1 | AsgWeb
NIC2 | AsgWeb
NIC3 | AsgLogic
NIC4 | AsgDb 

- この例の各ネットワーク インターフェイスは 1 つのアプリケーション セキュリティ グループだけに属していますが、実際には、Azure の制限に従って、1 つのインターフェイスが複数のグループに属することができます。
- NSG に関連付けられているネットワーク インターフェイスはありません。 NSG1 は両方のサブネットに関連付けられており、次の規則を含んでいます。

    **規則の名前** | **目的** | **詳細**
    --- | --- | ---   
    Allow-HTTP-Inbound-Internet | インターネットから Web サーバーへのトラフィックを許可します。 インターネットからの受信トラフィックは DenyAllInbound 既定セキュリティ規則によって拒否されるので、AsgLogic または AsgDb アプリケーション セキュリティ グループでは追加の規則は必要ありません。 | 優先順位:100<br/><br/> 送信元: インターネット<br/><br/> 送信元ポート: *<br/><br/> 変換先:AsgWeb<br/><br/> 宛先ポート:80<br/><br/> プロトコル:TCP<br/><br/> アクセス:許可。
    Deny-Database-All | AllowVNetInBound 既定セキュリティ規則では、同じ VNet 上にあるリソース間の通信がすべて許可され、この規則はすべてのリソースからのトラフィックを拒否するために必要です。 | 優先順位:120<br/><br/> 送信元: *<br/><br/> 送信元ポート: *<br/><br/> 変換先:AsgDb<br/><br/> 宛先ポート:1433<br/><br/> プロトコル:All<br/><br/> アクセス:拒否。
    Allow-Database-BusinessLogic | AsgLogic アプリケーション セキュリティ グループから AsgDb アプリケーション セキュリティ グループへのトラフィックを許可します。 この規則の優先順位は Deny-Database-All 規則より高く、その規則より前に処理されるため、AsgLogic アプリケーション セキュリティ グループからのトラフィックは許可されて、他のすべてのトラフィックはブロックされます。 | 優先順位:110<br/><br/> ソース:AsgLogic<br/><br/> 送信元ポート: *<br/><br/> 変換先:AsgDb<br/><br/> 宛先ポート:1433<br/><br/> プロトコル:TCP<br/><br/> アクセス:許可。

- アプリケーション セキュリティ グループを送信元または送信先として指定されている規則は、アプリケーション セキュリティ グループのメンバーであるネットワーク インターフェイスにのみ適用されます。 ネットワーク インターフェイスがアプリケーション セキュリティ グループのメンバーでない場合、ネットワーク セキュリティ グループがサブネットに関連付けられていても、規則はネットワーク インターフェイスに適用されません。

**詳細情報:**

- アプリケーション セキュリティ グループ[について学習する](https://docs.microsoft.com/azure/virtual-network/security-overview#application-security-groups)。


### <a name="best-practice-secure-access-to-paas-using-vnet-service-endpoints"></a>ベスト プラクティス:VNet サービス エンドポイントを使用して PaaS へのアクセスをセキュリティで保護する

VNet サービス エンドポイントでは、VNet の非公開アドレス空間と ID が、直接接続によって Azure サービスに拡張されます。

- エンドポイントを使用することで、重要な Azure サービス リソースを VNet のみに限定することができます。 VNet から Azure サービスへのトラフィックは常に、Microsoft Azure のバックボーン ネットワーク上に残ります。
- VNet の非公開アドレス空間は重複することがあるため、VNet からのトラフィックを一意に識別するためには使用できません。
- VNet でサービス エンドポイントを有効にした後は、Azure サービス リソースに VNet 規則を追加することで、サービス リソースをセキュリティで保護できます。 これにより、リソースへのパブリック インターネット アクセスを完全に排除し、VNet からのトラフィックのみを許可することにより、セキュリティが強化されます。

![サービス エンドポイント](./media/migrate-best-practices-networking/endpoint.png)
*サービス エンドポイント*

**詳細情報:**

- VNet サービス エンドポイント[について学習する](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)。


## <a name="best-practice-control-public-ip-addresses"></a>ベスト プラクティス:パブリック IP アドレスを管理する

Azure 内のパブリック IP アドレスは、VM、ロード バランサー、アプリケーション ゲートウェイ、VPN ゲートウェイと関連付けることができます。

- パブリック IP アドレスを使用すると、インターネット リソースから Azure リソースへの受信通信を許可したり、Azure リソースからインターネットへの送信通信を許可したりすることができます。
- パブリック IP アドレスは Basic SKU または Standard SKU で作成され、それらの間にはいくつかの相違点があります。 Standard SKU は任意のサービスに割り当てることができますが、通常は、VM、ロード バランサー、およびアプリケーション ゲートウェイで構成されます。
- Basic パブリック IP アドレスには NSG が自動的に構成されないことに注意することが重要です。 自分で構成して、アクセスを制御する規則を割り当てる必要があります。 Standard SKU の IP アドレスでは、既定で NSG が作成されて、規則が割り当てられます。
- ベスト プラクティスとしては、VM ではパブリック IP アドレスを構成しないようにする必要があります。
    - ポートを開く必要がある場合は、ポート 80 や 443 など、Web サービスの対するものだけにする必要があります。
    - SSH (22) や RDP (3389) などの標準のリモート管理ポートは、NSG を使用して、他のすべてのポートと共に、拒否に設定する必要があります。
- Azure ロード バランサーまたはアプリケーション ゲートウェイの後方に VM を配置するのがよい方法です。 その後、リモート管理ポートへのアクセスが必要な場合は、Azure Security Center で Just-In-Time VM アクセスを使用できます。

**詳細情報:**

- Azure でのパブリック IP アドレス[について学習する](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses)。
- Azure Security Center での Just-In-Time VM アクセスについて[さらに読む](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)。


## <a name="leverage-azure-security-features-for-networking"></a>ネットワークに Azure のセキュリティ機能を利用する

Azure には簡単に使用できるプラットフォーム セキュリティ機能があり、一般的なネットワーク攻撃に対する充実した対策が用意されています。 これらには、Azure Firewall、Web アプリケーション ファイアウォール、Network Watcher が含まれます。

## <a name="best-practice-deploy-azure-firewall"></a>ベスト プラクティス:Azure Firewall をデプロイする

Azure Firewall は、VNet リソースを保護するクラウドベースのマネージド ネットワーク セキュリティ サービスです。 これは、組み込みの高可用性とクラウドによる無制限のスケーラビリティを備えた、完全にステートフルなサービスとしてのファイアウォールです。

![サービス エンドポイント](./media/migrate-best-practices-networking/firewall.png)
*Azure Firewall*

- Azure Firewall では、サブスクリプションおよび VNet をまたいでアプリケーションとネットワークの接続ポリシーを一元的に作成、適用、記録できます。
- Azure Firewall では、外部のファイアウォールが VNet からのトラフィックを識別できるよう、VNet リソースに静的パブリック IP アドレスが使用されます。
- Azure Firewall は、ログ記録と分析を行うために Azure Monitor と完全に統合されます。
- Azure Firewall 規則を作成するときのベスト プラクティスとして、規則を作成するには FQDN タグを使用します。
    - FQDN タグは、よく知られた Microsoft サービスに関連付けられている FQDN のグループを表します。
    - FQDN タグを使用すると、必要な送信ネットワーク トラフィックがファイアウォールを通過するのを許可できます。
- たとえば、手動で Windows Update ネットワーク トラフィックがファイアウォールを通過できるようにするには、複数のアプリケーション規則を作成する必要があります。 FQDN を使用する場合は、アプリケーション規則を作成して、Windows Update のタグを組み込みます。 この規則を適用すると、Microsoft Windows Update エンドポイントへのネットワーク トラフィックは、ファイアウォールを通過できます。

**詳細情報:**

- Azure Firewall の[概要を理解する](https://docs.microsoft.com/azure/firewall/overview)。
- FQDN タグ[について学習する](https://docs.microsoft.com/azure/firewall/fqdn-tags)。


## <a name="best-practice-deploy-azure-web-application-firewall-waf"></a>ベスト プラクティス:Azure Web アプリケーション ファイアウォール (WAF) をデプロイする

Web アプリケーションが、一般的な既知の脆弱性を悪用した悪意のある攻撃の的になるケースが増えています。 悪用には、SQL インジェクション攻撃やクロス サイト スクリプティング攻撃などが含まれます。 アプリケーション コードでこのような攻撃を防ぐことは困難な場合があり、厳格な保守、パッチの適用、アプリケーション トポロジの複数のレイヤーの監視が必要になることもあります。 Web アプリケーション ファイアウォールを一元化すると、セキュリティの管理がはるかに簡単になり、脅威や侵入からの保護を行うアプリ管理者に役立ちます。 Web アプリ ファイアウォールでは、個々の Web アプリケーションをセキュリティで保護するのではなく、1 か所に既知の脆弱性の修正プログラムを適用することで、さらに迅速にセキュリティの脅威に対応できます。 既存のアプリケーション ゲートウェイは、Web アプリケーション ファイアウォールに対応したアプリケーション ゲートウェイに簡単に変換できます。

Azure Web アプリケーション ファイアウォール (WAF) は、Azure Application Gateway の機能です。
- WAF では、一般的な悪用や脆弱性からの Web アプリケーションの一元的な保護が提供されます。
- WAF では、バックエンドのコードを変更しなくても保護が行われます。
- アプリケーション ゲートウェイの後方にある複数の Web アプリを同時に保護できます。
- WAF は Azure Security Center と統合されています。
- アプリの要件に合わせて WAF の規則と規則グループをカスタマイズすることができます。
- ベスト プラクティスとしては、Azure VM 上のアプリや Azure App Service としてのアプリなど、Web に接続されているすべてのアプリの前方で WAF を使用する必要があります。

**詳細情報:**
- WAF [について学習する](https://docs.microsoft.com/azure/application-gateway/waf-overview)。
- WAF の制限事項と除外事項を[確認する](https://docs.microsoft.com/azure/application-gateway/application-gateway-waf-configuration)。


## <a name="best-practice-implement-azure-network-watcher"></a>ベスト プラクティス:Azure Network Watcher を実装する

Azure Network Watcher では、Azure VNet でのリソースと通信を監視するツールが提供されています。 たとえば、VM と別の VM や FQDN などのエンドポイントとの間の通信を監視したり、VNet 内のリソースやリソースの関係を表示したり、ネットワーク トラフィックの問題を診断したりできます。

![Network Watcher](./media/migrate-best-practices-networking/network-watcher.png)
*Network Watcher*

- Network Watcher を使用すると、VM にログインすることなく、ネットワークの問題を監視して診断できます。
- アラートを設定することでパケット キャプチャをトリガーし、パケット レベルでのパフォーマンスの情報にリアルタイムでアクセスできます。 問題が見つかったら、詳細に調査できます。
- ベスト プラクティスとしては、Network Watcher を使用して NSG のフロー ログを確認する必要があります。
    - Network Watcher で NSG のフロー ログを表示すると、NSG を通過するイングレスおよびエグレス IP トラフィックに関する情報を見ることができます。
    - フロー ログは JSON 形式で書き込まれています。
    - フロー ログでは、規則ごとの送信フローと受信フロー、フローが適用されるネットワーク インターフェイス (NIC)、フローに関する 5 組の情報 (送信元/宛先 IP、送信元/宛先ポート、プロトコル)、トラフィックが許可されているか拒否されているかが示されます。

**詳細情報:**

- Network Watcher の[概要を理解する](https://docs.microsoft.com/azure/network-watcher)。
- NSG のフロー ログ[について学習する](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview)。

## <a name="use-partner-tools-in-the-azure-marketplace"></a>Azure Marketplace のパートナー ツールを使用する

複雑なネットワーク トポロジでは、特定のネットワーク仮想アプライアンス (NVA) で Microsoft パートナーからのセキュリティ製品を使用する場合があります。

- NVA とは、ファイアウォール、WAN 最適化、その他のネットワーク機能を実行する VM です。
- NVA により、VNet のセキュリティとネットワークの機能が強化されます。 高可用性ファイアウォール、侵入防止、侵入検出、Web アプリケーション ファイアウォール (WAF)、WAN 最適化、ルーティング、負荷分散、VPN、証明書管理、Active Directory、多要素認証に対してデプロイできます。
-  [Azure Marketplace](https://azuremarketplace.microsoft.com/) では多数のベンダーの NVA を利用できます。 
 

## <a name="best-practice-implement-firewalls-and-nvas-in-hub-networks"></a>ベスト プラクティス:ハブ ネットワークにファイアウォールと NVA を実装する

ハブでは、(インターネットにアクセスできる) 境界ネットワークは通常、Azure ファイアウォール、ファイアウォール ファーム、または Web アプリケーション ファイアウォール (WAF) によって管理されます。 次の比較を検討してください。

**ファイアウォールの種類** | **詳細**
--- | ---
WAF | Web アプリは一般的であり、脆弱性や潜在的な悪用の影響を受ける傾向があります。<br/><br/> WAF は、Web アプリケーション (HTTP/HTTPS) に対する攻撃を検出するように設計されており、汎用のファイアウォールより限定的です。<br/><br/> 従来のファイアウォール テクノロジと比較すると、WAF は脅威から内部 Web サーバーを保護する特定の機能セットを備えています。
Azure Firewall | NVA ファイアウォール ファームのように、Azure Firewall では、共通の管理メカニズムおよびセキュリティ規則のセットを使用して、スポーク ネットワークでホストされているワークロードが保護され、オンプレミスのネットワークへのアクセスが制御されます。<br/><br/> Azure Firewall にはスケーラビリティが組み込まれています。
NVA ファイアウォール | Azure Firewall と同様に、NVA ファイアウォール ファームでは、共通の管理メカニズムおよびセキュリティ規則のセットを使用して、スポーク ネットワークでホストされているワークロードが保護され、オンプレミスのネットワークへのアクセスが制御されます。<br/><br/> NVA ファイアウォールは、ロード バランサーの後方で手動スケーリングできます。<br/><br/> NVA ファイアウォールは WAF ほど特化したソフトウェアではありませんが、エグレスおよびイングレスのあらゆる種類のトラフィックをフィルター処理して検査する広範な適用範囲を備えています。<br/><br/> NVA を使用する場合は、Azure Marketplace で見つけることができます。

インターネットから送信されるトラフィックとオンプレミスから送信されるトラフィックには、それぞれ異なる Azure Firewall (または NVA) のセットを使用することをお勧めします。
- 両方にただ 1 つのファイアウォールのセットを使うと、2 つのネットワーク トラフィック セットの間にセキュリティ境界が提供されないため、セキュリティ リスクになります。
- 個別のファイアウォール レイヤーを使用すると、セキュリティ規則のチェックの複雑さが軽減され、規則と受信ネットワーク要求の対応が明確です。

**詳細情報:**
- Azure VNet での NVA の使用[について学習する](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)。

## <a name="next-steps"></a>次の手順 

他のベスト プラクティスを確認してください。

- 移行後のセキュリティと管理の[ベスト プラクティス](migrate-best-practices-security-management.md)。
- 移行後のコスト管理の[ベスト プラクティス](migrate-best-practices-costs.md)。
