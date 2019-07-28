---
title: Azure のネットワーク セキュリティ | Microsoft Docs
description: クラウドベースのコンピューティング サービスについて学びます。これには、アプリケーションまたはエンタープライスのニーズを満たすために自動的にスケールアップとスケールダウンを行うことができる、コンピューティング インスタンスとサービスの多様な選択肢が含まれます。
services: security
documentationcenter: na
author: unifycloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: tomsh
ms.openlocfilehash: f684a9d7bca77a8aa3aa60f5079dda0ce3b58a1c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "60587469"
---
# <a name="azure-network-security"></a>Azure のネットワーク セキュリティ

セキュリティはクラウドの最優先の課題であり、Azure セキュリティについての正確でタイムリーな情報を得ることがどれだけ重要かを、私たちは認識しています。 アプリケーションとサービスに Azure を使用する最大の理由の 1 つは、Azure のさまざまなセキュリティ ツールや機能を活用できることです。 これらのツールや機能により、Azure プラットフォーム上にセキュリティで保護されたソリューションを作成できるようになります。

Microsoft Azure では、透過的な説明責任を実現しつつ、顧客データの機密性、整合性、および可用性を提供しています。 この記事「Azure Security の概要」は、Microsoft Azure に実装されている多数のネットワーク セキュリティ コントロールについて、お客様側の観点から理解を深めるために作成されました。この記事では、Microsoft Azure で使用できるネットワーク セキュリティ コントロールについて総合的に説明します。

この記事は、Azure にデプロイするソリューションのセキュリティを強化するために構成できる幅広いネットワーク コントロールに関する情報を提供します。 Azure プラットフォーム自体のネットワーク ファブリックのセキュリティを確保するマイクロソフトの対策について詳しくは、[Microsoft Trust Center](https://microsoft.com/en-us/trustcenter/cloudservices/azure) の Azure セキュリティのセクションをご覧ください。

## <a name="azure-platform"></a>Azure プラットフォーム

Azure は、オペレーティング システム、プログラミング言語、フレームワーク、ツール、データベース、デバイスにおいて幅広い選択肢をサポートするパブリック クラウド サービス プラットフォームです。  Docker を統合した Linux コンテナーの実行、JavaScript、Python、.NET、PHP、Java、Node.js によるアプリの構築、iOS、Android、Windows の各デバイスに対応したバックエンドの構築を行えます。 Azure クラウド サービスでは、何百万人もの開発者や IT プロフェッショナルからすでに信頼が置かれている同じテクノロジがサポートされています。

IT 資産を構築し、パブリック クラウド サービス プロバイダーに移行したとしましょう。このとき、移行したアプリケーションやデータをどこまで保護できるかは、採用したプロバイダーがクラウド ベースの資産のセキュリティ管理のためにどのようなサービスと体制を用意しているかに応じて変わってきます。

Azure のインフラストラクチャでは、数百万の顧客を同時にホストできるように施設からアプリケーションまでが設計されており、ビジネスのセキュリティ要件を満たす信頼性の高い基盤となっています。 また、Azure には構成可能なセキュリティ オプションと制御機能の幅広いコレクションが用意されており、組織によるデプロイの独自の要件を満たすようにセキュリティをカスタマイズできます。

## <a name="abstract"></a>要約

Microsoft パブリック クラウド サービスでは、ハイパースケール サービスとインフラストラクチャ、エンタープライズ レベルの機能、ハイブリッド接続の多くの選択肢を提供しています。 これらのサービスにインターネット経由でアクセスするか、プライベート ネットワーク接続を提供する Azure ExpressRoute を使用してアクセスするかを選択できます。 Microsoft Azure Platform により、自身のインフラストラクチャをシームレスにクラウドに拡張し、多層アーキテクチャを構築できます。 また、サード パーティは、セキュリティ サービスや仮想アプライアンスを提供することで、機能を強化できます。

Azure のネットワーク サービスは柔軟性、可用性、回復性、セキュリティ、および整合性を最大化するように設計されています。 このホワイトペーパーは、Azure のネットワーク機能の詳細およびお客様が Azure のネイティブのセキュリティ機能を使用して情報資産を保護する方法について説明します。

このホワイトペーパーは、次の読者を対象としています。

- Azure で使用できる、およびサポートされているセキュリティ ソリューションを探しているテクニカル マネージャー、ネットワーク管理者、および開発者。

-   Azure パブリック クラウドのネットワーク セキュリティについて議論する上で関連性の高いAzure のネットワーク テクノロジーやサービスの概要を把握することを求めている領域の専門家やビジネス プロセスの責任者。

## <a name="azure-networking-big-picture"></a>Azure のネットワークの全体像
Microsoft Azure には、アプリケーションとサービスの接続要件をサポートする堅牢なネットワーク インフラストラクチャが組み込まれています。 ネットワーク接続は、Azure に配置されているリソース間、オンプレミスのリソースと Azure でホストされているリソース間、インターネットと Azure 間で可能です。

![Azure のネットワークの全体像](media/azure-network-security/azure-network-security-fig-1.png)

[Azure のネットワーク インフラストラクチャ](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-networking-guidelines)では、仮想ネットワーク (VNet) を使用して Azure リソースを安全に相互接続することができます。 VNet とは、クラウド内のユーザー独自のネットワークを表したものです。 特定のサブスクリプション専用に Azure クラウド ネットワークが論理的に分離されています。 VNet は、オンプレミス ネットワークに接続できます。

Azure は [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) を使用してオンプレミス ネットワークおよび Azure Virtual Network への専用の WAN リンクの接続をサポートします。 Azure とユーザー サイト間のリンクは、パブリック インターネットを経由しない専用接続を使用します。 Azure アプリケーションが複数のデータセンターで実行されている場合は、[Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview) を使用して、アプリケーションのインスタンスにユーザーからの要求をインテリジェントにルーティングできます。 また、サービスがインターネットからアクセス可能な場合、Azure 内で実行されていないサービスへトラフィックをルーティングすることもできます。

## <a name="enterprise-view-of-azure-networking-components"></a>Azure のネットワーク コンポーネントのエンタープライズ ビュー
Azure には、ネットワーク セキュリティについて議論する上で関連性の高い数多くのネットワーク コンポーネントが用意されています。 これらのネットワーク コンポーネントについて、関連するセキュリティの問題に注目しつつ説明します。

> [!Note]
> Azure のネットワークのすべてを網羅しているわけではありません。Azure にデプロイするサービスやアプリケーション周りのネットワーク インフラストラクチャのセキュリティを確保するための計画や設計において、極めて重要と見なされるものについてのみ説明します。

この記事では、Azure のネットワークにおける次のエンタープライズ機能について説明します。

-   基本的なネットワーク接続

-   ハイブリッド接続

-   [シングル サインオンの設定]

-   ネットワークの検証

### <a name="basic-network-connectivity"></a>基本的なネットワーク接続

[Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) サービスでは、仮想ネットワーク (VNet) を使用して Azure リソースを安全に相互接続することができます。 VNet とは、クラウド内のユーザー独自のネットワークを表したものです。 VNet はサブスクリプション専用に Azure ネットワーク インフラストラクチャが論理的に分離されています。 サイト間 VPN や専用の [WAN リンク](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)を使用して、VNet 同士を接続したり、オンプレミスのネットワークと接続したりできます。

![基本的なネットワーク接続](media/azure-network-security/azure-network-security-fig-2.png)

Azure でサーバーをホストするには VM を使用しますが、それらの VM をどのようにしてネットワークに接続するかが問題です。 その答えは、VM は [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) に接続されます。

Azure Virtual Network は、独自の仮想化プラットフォーム ソリューション (Microsoft Hyper-V や VMware など) でオンプレミスで使用する仮想ネットワークのようなものです。

#### <a name="intra-vnet-connectivity"></a>VNet 内の接続

VNet を相互に接続できるため、一方の VNet に接続されているリソースは、VNet をまたいで相互に通信できます。 次のオプションのいずれかまたは両方を使用して、複数の VNet を相互に接続できます。

- **ピアリング:** 同じ Azure の場所内の別の Azure VNet に接続されているリソースの相互接続を可能にします。 複数の VNet の間での帯域幅と待ち時間は、リソースが同じ VNet に接続されている場合と同じです。 ピアリングについて詳しくは、「[仮想ネットワーク ピアリング](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)」をご覧ください。

  ![ピアリング](media/azure-network-security/azure-network-security-fig-3.png)

- **VNet 間接続:** 同じまたは異なる Azure の場所内の別の Azure VNet に接続されているリソースの相互接続を有効にします。 ピアリングとは異なり、VNet 間での帯域幅は制限されます。これは、トラフィックが Azure VPN ゲートウェイを通過する必要があるためです。

![VNet 間接続](media/azure-network-security/azure-network-security-fig-4.png)


VNet 間接続を使用した VNet 接続について詳しくは、[VNet 間接続の構成](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事をご覧ください。

#### <a name="azure-virtual-network-capabilities"></a>Azure Virtual Network の機能:

Azure Virtual Network にはネットワークに接続するための仮想マシンが用意されているため、他のネットワーク リソースと安全に接続できます。 ただし、基本的な接続はまだ始まりにすぎません。 Azure Virtual Network サービスの次の機能は、Azure Virtual Network のセキュリティの特徴を示します。

-   分離:

-   インターネット接続

-   Azure リソースの接続

-   VNet の接続

-   オンプレミスの接続

-   トラフィックのフィルター処理

-   ルーティング

**分離**

VNet は相互に[分離](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)されています。 同じ [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) アドレス ブロックを使用する開発、テスト、運用環境に個別の VNet を作成できます。 逆に、異なる CIDR アドレス ブロックを使用する複数の VNet を作成し、ネットワークをまとめて接続することもできます。 VNet は、複数のサブネットに分割することができます。

Azure では、VNet に接続されている VM ロール インスタンスと [Cloud Services](https://azure.microsoft.com/services/cloud-services/) ロール インスタンス用に内部名前解決が用意されています。 必要に応じて、Azure の内部名前解決を使用する代わりに、独自の DNS サーバーを使用するよう VNet を構成できます。

各 Azure [サブスクリプション](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology?toc=%2fazure%2fvirtual-network%2ftoc.json)と Azure [リージョン](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology?toc=%2fazure%2fvirtual-network%2ftoc.json)内に複数の VNet を実装できます。 VNet 同士は分離されています。 各 VNet では、次のことを実行できます。

-   パブリックおよびプライベート (RFC 1918) アドレスを使用して、カスタム プライベート IP アドレス空間を指定する。 Azure は、VNet に接続されているリソースに、割り当てたアドレス空間のプライベート IP アドレスを割り当てます。

-   VNet を 1 つ以上のサブネットに分割し、各サブネットに VNet のアドレス空間の一部を割り当てる。

-   Azure で提供される名前解決を使用するか、VNet に接続されたリソースで使用するために独自の DNS サーバーを指定する。 VNet での名前解決について詳しくは、[VM と Cloud Services の名前解決](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances)に関する記事をご覧ください。

**インターネット接続**

VNet に接続されているすべての [Azure Virtual Machines (VM)](https://docs.microsoft.com/azure/virtual-machines/windows/) ロール インスタンスと Cloud Services ロール インスタンスは、既定でインターネットにアクセスできます。 また、必要に応じて、特定のリソースへの着信アクセスを有効にすることもできます。VNet に接続されている (VM および) Cloud Services ロール インスタンスは既定でインターネットにアクセスできます。 また、必要に応じて、特定のリソースへの着信アクセスを有効にすることもできます。

VNet に接続されているすべてのリソースは、既定で、インターネットに送信接続されています。 リソースのプライベート IP アドレスは、Azure インフラストラクチャによりプライベート IP アドレスへの送信元ネットワーク アドレス変換 (SNAT) が行われています。 カスタム ルーティングとトラフィック フィルタリングを実装することで、既定の接続を変更できます。 インターネットへの送信接続について詳しくは、「[Azure の送信用接続の詳細](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections?toc=%2fazure%2fvirtual-network%2ftoc.json)」をご覧ください。

インターネットから Azure リソースへの着信接続、または SNAT なしでインターネットへの送信接続を行うには、リソースにパブリック IP アドレスを割り当てる必要があります。 パブリック IP アドレスについて詳しくは、「[パブリック IP アドレス](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)」をご覧ください。

**Azure リソースの接続**

Cloud Services や VM などの [Azure リソース](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)を同じ VNet に接続できます。 リソースは、異なるサブネットに存在する場合でも、プライベート IP アドレスを使用して相互に接続できます。 Azure では、サブネット、VNet、オンプレミス ネットワークの間に既定のルーティングを提供しているため、ルートの構成と管理は必要ありません。

VNet には、仮想マシン (VM)、クラウド サービス、App Service 環境、仮想マシン スケール セットなど、複数の Azure リソースを接続できます。 VM は、ネットワーク インターフェイス (NIC) を経由して VNet 内のサブネットに接続します。 NIC について詳しくは、「[ネットワーク インターフェイス](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface)」をご覧ください。

**VNet の接続**

[VNet](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) は相互接続が可能なため、任意の VNet に接続されているリソースは他の VNet 上の任意のリソースと通信できます。

VNet を相互に接続できるため、一方の VNet に接続されているリソースは、VNet をまたいで相互に通信できます。 次のオプションのいずれかまたは両方を使用して、複数の VNet を相互に接続できます。

- **ピアリング:** 同じ Azure の場所内の別の Azure VNet に接続されているリソースの相互接続を可能にします。 複数の VNet の間での帯域幅と待ち時間は、リソースが同じ VNet に接続されている場合と同じです。ピアリングについて詳しくは、「[仮想ネットワーク ピアリング](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)」をご覧ください。

- **VNet 間接続:** 同じまたは異なる Azure の場所内の別の Azure VNet に接続されているリソースの相互接続を有効にします。 ピアリングとは異なり、VNet 間での帯域幅は制限されます。これは、トラフィックが Azure VPN ゲートウェイを通過する必要があるためです。 VNet 間接続を使用して VNet を接続する方法について詳しくは、 [VNet 間の接続の構成](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事をご覧ください。

**オンプレミスの接続**

VNet は、自身のネットワークと Azure の間のプライベート ネットワーク接続、またはインターネットを介したサイト間 VPN 接続を使用して、[オンプレミス](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) ネットワークに接続できます。

オンプレミス ネットワークを VNet に接続するには、次のオプションを組み合わせて使用します。

- **ポイント対サイト仮想プライベート ネットワーク (VPN)** :ネットワークに接続されている 1 台の PC と VNet の間で確立されます。 この接続の種類は、既存のネットワークへの変更をほとんどまたはまったく必要としないため、Azure を使い始めたばかりのユーザーまたは開発者に適しています。 この接続は、SSTP プロトコルを使用して、PC と VNet の間にインターネット経由の暗号化された通信を提供します。 トラフィックがインターネットを経由するため、ポイント対サイト VPN の待ち時間は予測できません。

- **サイト間 VPN**:VPN デバイスと Azure VPN Gateway の間で確立されます。 この接続の種類を使用すると、承認した任意のオンプレミス リソースが VNet にアクセスできます。 この接続は IPsec/IKE VPN で、オンプレミスのデバイスと Azure VPN ゲートウェイの間にインターケット経由の暗号化された通信を提供します。 トラフィックがインターネットを経由するため、サイト間接続の待ち時間は予測できません。

- **Azure ExpressRoute:** ExpressRoute のパートナーを介して、ネットワークと Azure の間で確立されます。 この接続はプライベート接続です。 トラフィックはインターネットを経由しません。 トラフィックがインターネットを経由しないため、ExpressRoute 接続の待ち時間は予測可能です。 ここまでに説明したすべての接続オプションについて詳しくは、「[接続トポロジの図](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways?toc=%2fazure%2fvirtual-network%2ftoc.json)」をご覧ください。

**トラフィックのフィルター処理**

VM ロール インスタンスと Cloud Services ロール インスタンスの[ネットワーク トラフィック](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)は、着信、送信方向ともに、送信元の IP アドレスとポート、送信先の IP アドレスとポート、およびプロトコルでフィルター処理できます。

次のオプションのいずれかまたは両方を使用して、サブネット間のネットワーク トラフィックをフィルター処理できます。

- **ネットワーク セキュリティ グループ (NSG):** 各 NSG には、送信元と送信先の IP アドレス、ポート、およびプロトコルでトラフィックをフィルター処理できるようにする受信と送信のセキュリティ規則を複数含めることができます。 NSG は VM の各 NIC に適用できます。 NSG は、NIC または他の Azure リソースが接続されているサブネットに適用することもできます。 NSG について詳しくは、[ネットワーク セキュリティ グループ](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)に関する記事をご覧ください。

- **Virtual Network アプライアンス:** Virtual Network アプライアンスとは、ファイアウォールなどのネットワーク機能を実行するソフトウェアが動作している VM です。 入手可能な NVA の一覧については、Azure Marketplace をご覧ください。 WAN の最適化やその他のネットワーク トラフィック機能を提供する NVA も入手できます。 通常、NVA はユーザー定義ルートまたは BGP ルートで使用されます。 また、NVA を使用して、VNet 間のトラフィックをフィルター処理することもできます。

**ルーティング**

必要に応じて、独自のルートを構成するかネットワーク ゲートウェイ経由で BGP ルートを使用することで、Azure の既定のルーティングをオーバーライドできます。

Azure では、VNet 内の任意のサブネットに接続されている複数のリソースが相互通信できるようにするルート テーブルが既定で作成されます。 次のオプションのいずれかまたは両方を実装して、Azure によって作成される既定のルートをオーバーライドできます。

- **ユーザー定義のルート:** サブネットごとにトラフィックのルーティング先を制御するルートを含む、カスタム ルート テーブルを作成できます。 ユーザー定義ルートについて詳しくは、[ユーザー定義のルート](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview)に関する記事をご覧ください。

- **BGP のルート:** Azure VPN ゲートウェイまたは ExpressRoute 接続を使用して VNet をオンプレミス ネットワークに接続する場合、BGP ルートを VNet に伝達できます。

### <a name="hybrid-internet-connectivity-connect-to-an-on-premises-network"></a>ハイブリッド インターネット接続:オンプレミス ネットワークに接続する
オンプレミス ネットワークを VNet に接続するには、次のオプションを組み合わせて使用します。

-   インターネット接続

-   ポイント対サイト VPN (P2S VPN)

-   サイト対サイト VPN (S2S VPN)

-   ExpressRoute

#### <a name="internet-connectivity"></a>インターネット接続

その名前からわかるように、インターネット接続により、別のパブリック エンドポイントを仮想ネットワークの内部のワークロードに公開することで、インターネットからワークロードに接続できるようになります。 これらのワークロードは、[インターネットに接続するロード バランサー](https://docs.microsoft.com/azure/load-balancer/load-balancer-internet-overview)を使用または単にパブリック IP アドレスを VM に割り当てることで公開できます。 この方法により、ホストのファイアウォール、[ネットワーク セキュリティ グループ (NSG)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)、および[ユーザー定義ルート](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview)を使用して、インターネット上にあるすべてがその仮想マシンに到達できるようになります。

このシナリオでは、アプリケーションをインターネットに公開して、どこからでもまたは特定の場所から (ワークロードの構成による) 接続できます。

#### <a name="point-to-site-vpn-or-site-to-site-vpn"></a>ポイント対サイト VPN またはサイト対サイト VPN
これら 2 つは同じカテゴリーに属します。 両方とも VNet に VPN Gateway が必要であり、ワークステーションの VPN クライアントを[ポイント対サイト構成](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal)の一部として使用するか、サイト対サイト VPN を終了できるようにオンプレミスの [VPN デバイス](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices)を構成できます。 この方法により、オンプレミスのデバイスが VNet 内のリソースに接続できます。

ポイント対サイト (P2S) 構成では、個々のクライアント コンピューターから仮想ネットワークへのセキュリティで保護された接続を作成することができます。 P2S は、SSTP (Secure Socket トンネリング プロトコル) 経由の VPN 接続です。

![ポイント対サイト VPN](media/azure-network-security/azure-network-security-fig-5.png)

ポイント対サイト接続は、自宅や会議センターなど、リモートの場所から VNet に接続する場合や、仮想ネットワークに接続する必要があるクライアントの数が少ない場合に便利です。

P2S 接続に VPN デバイスや公開 IP アドレスは必要ありません。 VPN 接続は、クライアント コンピューターから確立します。 したがって、Azure に接続する方法として、多数のオンプレミスのデバイスやコンピューターから Azure ネットワークへの永続的な接続が必要な場合、P2S はお勧めできません。

![サイト対サイト VPN](media/azure-network-security/azure-network-security-fig-6.png)

> [!Note]
> ポイント対サイト接続について詳しくは、[ポイント対サイトに関してよく寄せられる質問](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal)に関する記事をご覧ください。

サイト間 VPN Gateway 接続は、IPsec/IKE (IKEv1 または IKEv2) VPN トンネルを介してオンプレミス ネットワークを Azure 仮想ネットワークに接続するために使用します。

この種類の接続では、外部接続用パブリック IP アドレスが割り当てられていてるオンプレミスの VPN デバイスが必要です。 この接続はインターネットをまたいでいるため、自社ネットワークと Azure の間の暗号化されたリンクの "トンネル" を通して情報を送信できます。 サイト間 VPN は、数十年にわたってあらゆる規模の企業に導入されている安全で成熟したテクノロジです。 トンネルの暗号化は、[IPsec トンネル モード](https://technet.microsoft.com/library/cc786385.aspx)を使用して実現されています。

サイト間 VPN は信頼性が高く、実績のあるテクノロジですが、トンネル内のトラフィックはインターネットを経由します。 また、帯域幅は最大 200 Mbps 程度で、それほど大きくありません。

クロスプレミス接続できわめて高いレベルのセキュリティやパフォーマンスが必要な場合は、クロスプレミス接続に Azure ExpressRoute を使用することをお勧めします。 ExpressRoute は、オンプレミスの場所または Exchange ホスティング プロバイダーとの間に確立する専用 WAN リンクです。 これは電話会社の接続であるため、データはインターネットを経由せずに送信され、インターネット通信に付随する潜在的なリスクにさらされることがありません。

> [!Note]
> VPN Gateway について詳しくは、「[VPN Gateway について](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)」をご覧ください。

#### <a name="dedicated-wan-link"></a>専用の WAN リンク
Microsoft Azure ExpressRoute を利用すれば、接続プロバイダーが提供する専用プライベート接続で、オンプレミスのネットワークを Azure に拡張できます。

ExpressRoute 接続では、公共のインターネットを利用できません。 それにより、ExpressRoute 接続はインターネット経由の一般的な接続に比べて、安全性と信頼性が高く、待機時間も短く、高速です。

![ 専用の WAN リンク](media/azure-network-security/azure-network-security-fig-7.png)

> [!Note]
> ExpressRoute を使用してネットワークを Microsoft に接続する方法について詳しくは、[ExpressRoute 接続モデル](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)および [ExpressRoute の技術的概要](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)に関する記事をご覧ください。

また、サイト対サイトの VPN オプションでは、ExpressRoute を使用すると複数の VNet 内のリソースに接続できます。 実際、SKU によっては 10 の VNet に接続できます。 [プレミアム アドオン](https://docs.microsoft.com/azure/expressroute/expressroute-faqs)をお持ちの場合、帯域幅によっては 100 の VNet に接続できます。 これらの種類の接続の外観について詳しくは、「[接続トポロジの図](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways?toc=%2fazure%2fvirtual-network%2ftoc.json)」をご覧ください。

### <a name="security-controls"></a>セキュリティ コントロール
Azure Virtual Network は他の仮想ネットワークから分離された安全かつ論理的なネットワークを提供し、オンプレミスのネットワークで使用する多数のセキュリティ コントロールをサポートします。 お客様はサブネットを使用して独自の構造を作成します。独自の IP アドレス範囲を使用し、ルート テーブル、ネットワーク セキュリティ グループ、アクセス制御リスト (ACL)、ゲートウェイ、および仮想アプライアンスを構成してクラウドでワークロードを実行します。

Azure Virtual Network で次のセキュリティ コントロールを使用できます。

-   ネットワーク アクセス制御

-   ユーザー定義のルート

-   ネットワーク セキュリティ アプライアンス

-   Application Gateway

-   Azure Web アプリケーション ファイアウォール

-   ネットワーク可用性管理

#### <a name="network-access-controls"></a>ネットワーク アクセス制御
Azure Virtual Network (VNet) は Azure ネットワーク モデルの基礎であり、分離と保護を担う一方で、[ネットワーク セキュリティ グループ (NSG)](https://blogs.msdn.microsoft.com/igorpag/2016/05/14/azure-network-security-groups-nsg-best-practices-and-lessons-learned/) はネットワーク レベルでネットワーク トラフィック ルールを適用および制御する主なツールです。

![ ネットワーク アクセス制御](media/azure-network-security/azure-network-security-fig-8.png)


仮想ネットワーク内でのワークロード間の通信を許可または拒否することで、クロスプレミス接続を介したお客様のネットワーク上のシステムからのアクセス、または直接インターネット通信を制御できます。

図では、VNet と NSG の両方が Azure 全体のセキュリティ スタック内の特定の層に属します。この層では NSG、UDR、ネットワーク仮想アプライアンスを使用して DMZ などのセキュリティ境界を構築し、保護されているネットワークにおけるアプリケーションのデプロイを保護することができます。

NSG は 5 つのタプルを使用してトラフィックを評価します (また、NSG に対して構成するルールに使用されます)。

-   [送信元と送信先の IP アドレス](https://support.microsoft.com/help/969029/the-functionality-for-source-ip-address-selection-in-windows-server-2008-and-in-windows-vista-differs-from-the-corresponding-functionality-in-earlier-versions-of-windows)

-   [送信元と送信先のポート](https://technet.microsoft.com/library/dd197515)

-   プロトコル:[伝送制御プロトコル (TCP)](https://technet.microsoft.com/library/cc940037.aspx) または[ユーザー データグラム プロトコル (UDP)](https://technet.microsoft.com/library/cc940034.aspx)

これは、単一の VM と VM のグループ間のアクセス、単一の VM からもう 1 つの単一の VM へのアクセス、または全サブネット間のアクセスを制御できることを意味します。 ここでも、これは単純なステートフル パケット フィルター処理であり、フル パケット検査ではないことに注意する必要があります。 ネットワーク セキュリティ グループにはプロトコルの検証やネットワーク レベルの IDS または IPS 機能はありません。

NSG には認識しておく必要があるいくつかのルールが組み込まれています。 次のとおりです。

-   **特定の仮想ネットワークの内部ですべてのトラフィックを許可する:** 同じ Azure Virtual Network 上のすべての VM は互いに通信できます。

-   **Azure の負荷分散の受信を許可する:**  このルールは、すべての送信元アドレスからすべての送信先アドレスへの Azure Load Balancer のトラフィックを許可します。

-   **すべての受信を拒否する:**  このルールは、明示的に許可したインターネットからのトラフィック ソーシングをすべてブロックします。

-   **インターネットに送信されるすべてのトラフィックを許可する:** このルールは VM がインターネットへの接続を開始することを許可します。 これらの接続を開始しない場合は、これらのルールをブロックするまたは強制的なトンネリングを適用するルールを作成する必要があります。

#### <a name="system-routes-and-user-defined-routes"></a>システム ルートとユーザー定義のルート

Azure で仮想マシン (VM) を仮想ネットワーク (VNet) に追加すると、VM 同士がネットワークを介して自動的に通信できることがわかります。 VM が異なるサブネットにあったとしても、ゲートウェイを指定する必要はありません。

VM からパブリック インターネットへの通信も同様であり、Azure からユーザー独自のデータセンターにハイブリッド接続が存在する場合は、オンプレミスのネットワークへの通信でも同様です。

![システム ルート](media/azure-network-security/azure-network-security-fig-9.png)

このような通信フローが可能であるのは、Azure が一連のシステム ルートを使用して IP トラフィックのフロー方法を定義するためです。 システム ルートは、以下のシナリオでの通信フローを制御します。

-   同じサブネット内から。

-   VNet 内でサブネットから別のサブネットに。

-   VM からインターネットに。

-   VNet から VPN Gateway を通して別の VNet に。

-   VNet ピアリングを通して VNet から別の VNet に ([サービス チェイニング](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview))。

-   VNet から VPN Gateway を通してオンプレミスのネットワークに。

多くの企業にはセキュリティおよびコンプライアンスに関する厳密な要件があり、すべてのネットワーク パケットをオンプレミスで検査して特定のポリシーを適用する必要があります。 Azure には[強制トンネリング](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-forced-tunneling)と呼ばれるメカニズムが用意されており、カスタム ルートを作成するか、ExpressRoute または VPN を介した[境界ゲートウェイ プロトコル (BGP)](https://docs.microsoft.com/windows-server/remote/remote-access/bgp/border-gateway-protocol-bgp) のアドバタイズにより、VM からのトラフィックをオンプレミスにルーティングします。

Azure では、強制トンネリングは仮想ネットワークのユーザー定義ルート (UDR) を使用して構成されます。 オンプレミス サイトへのトラフィックのリダイレクトは、Azure VPN Gateway への既定のルートとして表されます。

以下のセクションでは、Azure Virtual Network のルーティング テーブルおよびルートの現在の制限を一覧表示します。

- 各仮想ネットワーク サブネットには、システム ルーティング テーブルが組み込まれています。 システム ルーティング テーブルには、次の 3 つのグループがあります。

  -  **ローカル VNet ルーティング:** 直接、同じ仮想ネットワーク内の宛先 VM へ

  - **オンプレミス ルート:** Azure VPN ゲートウェイへ

  -  **既定のルート:** 直接、インターネットへ。 前の 2 つのルートが網羅していないプライベート IP アドレスへ送信されるパケットは削除されます。

- ユーザー定義ルートをリリースすることにより、既定のルートを追加するルーティング テーブルを作成し、そのルーティング テーブルを、ご使用の VNet サブネットに関連付け、それらのサブネットでの強制トンネリングを有効にします。

- 仮想ネットワークに接続されたクロスプレミス ローカル サイト間で「既定のサイト」を設定する必要があります。

- 強制トンネリングは、動的ルーティング VPN ゲートウェイ (静的ゲートウェイではない) を持つ VNet に関連付ける必要があります。

- ExpressRoute の強制トンネリングは、このメカニズムを使用して構成されていませんが、代わりに ExpressRoute BGP ピアリング セッションを介して既定のルートを通知することで有効化されます。

> [!Note]
> 詳しくは、「[ExpressRoute のドキュメント](https://azure.microsoft.com/documentation/services/expressroute/)」をご覧ください。

#### <a name="network-security-appliances"></a>ネットワークのセキュリティ アプライアンス
ネットワーク セキュリティ グループとユーザー定義のルーティングを使用すれば [OSI モデル](https://en.wikipedia.org/wiki/OSI_model)のネットワーク層とトランスポート層において一定のネットワーク セキュリティを確保できますが、より上位のネットワーク スタックでセキュリティを確保することが必要になる状況もあります。 このような状況では、Azure のパートナーによって提供されている仮想ネットワーク セキュリティ アプライアンスのデプロイをお勧めします。

![ネットワークのセキュリティ アプライアンス](./media/azure-network-security/azure-network-security-fig-10.png)

Azure ネットワークのセキュリティ アプライアンスは VNet のセキュリティとネットワーク機能を向上させ、多数のベンダーから [Azure Marketplace](https://azuremarketplace.microsoft.com) を通じて入手できます。 これらの仮想セキュリティ アプライアンスをデプロイすると、次のことを実現できます。

-   高可用性ファイアウォール

-   侵入防止

-   侵入検出

-   Web アプリケーション ファイアウォール (WAF)

-   WAN の最適化

-   ルーティング

-   負荷分散

-   VPN

-   証明書の管理

-   Active Directory

-   多要素認証

#### <a name="application-gateway"></a>Application gateway

[Microsoft Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) は専用の仮想アプライアンスで、サービスとしてアプリケーション配信コントローラー (ADC) を提供します。

 ![Application Gateway](./media/azure-network-security/azure-network-security-fig-11.png)

Application Gateway を使用すると、CPU に負担をかける SSL ターミネーションをアプリケーション ゲートウェイにオフロードする (SSL オフロード) ことで、Web ファームのパフォーマンスと可用性を最適化できます。 また、次のような第 7 層の他のルーティング機能を提供します。

-   着信トラフィックのラウンド ロビン分散

-   Cookie ベースのセッション アフィニティ

-   URL パス ベースのルーティング

-   1 つの Application Gateway の背後に複数の Web サイトをホスト可能


アプリケーション ゲートウェイの一部として、[Web アプリケーション ファイアウォール (WAF)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) も提供されています。 一般的な Web の脆弱性や悪用から Web アプリケーションを保護します。 Application Gateway は、インターネット接続ゲートウェイ、または内部的にのみ使用されるゲートウェイのいずれかとして構成できるほか、この両方を組み合わせて使用することも可能です。

Application Gateway の WAF は検出モードや保護モードで実行できます。 管理者が検出モードで実行する一般的な用途は、悪意のあるパターンがないかどうかトラフィックを監視することです。 潜在的な悪用が検出されたら、保護モードに切り替えて疑わしい着信トラフィックをブロックします。

 ![Application Gateway](./media/azure-network-security/azure-network-security-fig-12.png)

さらに、Application Gateway の WAF により [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview) と [Azure Security Center](https://azure.microsoft.com/services/security-center/) に統合されたリアルタイムの WAF ログを使用して攻撃に対して Web アプリケーションを監視し、WAF アラートを追跡してトレンドを簡単に監視できます。

JSON 形式のログはお客様のストレージ アカウントに直接入ります。 これらのログは完全に制御可能で、独自のアイテム保持ポリシーを適用できます。

また、[Azure ログ統合](https://aka.ms/AzLog)を使用してそれらのログを独自の分析システムに取り込むこともできます。 WAF のログは [Azure Monitor ログ](../log-analytics/log-analytics-overview.md)にも統合されており、Azure Monitor ログを使用して洗練されたきめ細かなクエリを実行できます。

#### <a name="azure-web-application-firewall-waf"></a>Azure Web アプリケーション ファイアウォール (WAF)

Web アプリケーションは以前にも増して、SQL インジェクション、クロス サイト スクリプティング攻撃、[OWASP トップ 10](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project) に紹介されている他の攻撃など、一般的な既知の脆弱性を悪用する悪意のある攻撃の対象となっています。 アプリケーションこのような悪用を防ぐことは困難な場合があり、厳格な保守、パッチの適用、アプリケーション トポロジの複数のレイヤーの監視が必要になることもあります。

 ![Azure Web アプリケーション ファイアウォール (WAF)](./media/azure-network-security/azure-network-security-fig-13.png)

集中管理された [Web アプリケーション ファイアウォール (WAF)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) は Web を攻撃から保護し、アプリケーションを変更することなくセキュリティを簡単に管理できます。

また、WAF のソリューションは、1 か所に既知の脆弱性の修正プログラムを適用することで、個々の Web アプリケーションをセキュリティで保護する場合と比較して、さらに迅速にセキュリティの脅威に対応できます。 既存のアプリケーション ゲートウェイは、Web アプリケーション ファイアウォールに対応したアプリケーション ゲートウェイに簡単に変換できます。

#### <a name="network-availability-controls"></a>ネットワーク可用性管理

Microsoft Azure を使用してネットワーク トラフィックを分散するには、さまざまなオプションがあります。 これらのオプションはそれぞれ動作が異なり、さまざまな機能セットが含まれ、さまざまなシナリオをサポートします。 オプションは単独または組み合わせて使用することができます。

ネットワークの可用性を管理する機能は次のとおりです。

-   Azure Load Balancer

-   Application Gateway

-   Traffic Manager

**Azure Load Balancer**

優れた可用性とネットワーク パフォーマンスをアプリケーションに提供します。 Azure Load Balancer は、負荷分散セットで定義されているサービスの正常なインスタンス間で着信トラフィックを分散する、レイヤー 4 (TCP、UDP) ロード バランサーです。

 ![Azure Load Balancer](media/azure-network-security/azure-network-security-fig-14.png)


Azure Load Balancer は次のように構成できます。

-   仮想マシンへの着信インターネット トラフィックを負荷分散します。 この構成は、 [インターネットに接続する負荷分散](https://docs.microsoft.com/azure/load-balancer/load-balancer-internet-overview)と呼ばれます。

-   仮想ネットワーク内の仮想マシン間、クラウド サービス内の仮想マシン間、クロスプレミスの仮想ネットワーク内のオンプレミスのコンピューターと仮想マシン間で、トラフィックを負荷分散します。 この構成は、 [内部負荷分散](https://docs.microsoft.com/azure/load-balancer/load-balancer-internal-overview)と呼ばれます。

-   外部トラフィックを特定の仮想マシンに転送します。

インターネットから到達できるようにするには、クラウド内のすべてのリソースにパブリック IP アドレスが必要です。 Azure のクラウド インフラストラクチャでは、ルーティング不可能な IP アドレスをリソースに使用します。 インターネットとの通信には、ネットワーク アドレス変換 (NAT) とパブリック IP アドレスが使用されます。

 **Application Gateway**

 Application Gateway は、アプリケーション レイヤーで動作します (OSI ネットワーク参照モデルの第 7 層)。 これは、クライアント接続を終了して、バックエンドのエンドポイントへの要求を転送し、リバース プロキシ サービスとして機能します。

 **Traffic Manager**

Microsoft Azure Traffic Manager では、さまざまなデータセンターのサービス エンドポイントへのユーザー トラフィックの分散を制御できます。 Traffic Manager でサポートされるサービス エンドポイントには、Azure VM、Web Apps、およびクラウド サービスが含まれます。 Azure 以外の外部エンドポイントで Traffic Manager を使用することもできます。

Traffic Manager では、ドメイン ネーム システム (DNS) を使用して、[トラフィック ルーティング方法](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods)とエンドポイントの正常性に基づいて最適なエンドポイントにクライアント要求を送信します。 Traffic Manager には、さまざまなアプリケーション ニーズ、エンドポイントの正常性、[監視](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring)、自動フェールオーバーに対応する、さまざまなトラフィック ルーティング方法が備わっています。 Traffic Manager は Azure リージョン全体の障害などの障害に対応します。

Azure Traffic Manager を使用すると、アプリケーション エンドポイント全体でトラフィックの分散を制御できます。 エンドポイントは、Azure の内部または外部でホストされている、インターネットに公開されたサービスです。

Traffic Manager の 2 つのメリットを次に示します。

-   複数の[トラフィック ルーティング方法](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods)のいずれか 1 つを使用してトラフィックを分散する。

-   [エンドポイントの正常性を継続的に監視](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring) し、エンドポイントで障害が発生したときに自動フェールオーバーを行う。

クライアントからサービスへの接続を試みようとするときは、最初にサービスの DNS 名を IP アドレスに解決する必要があります。 その後、クライアントは、その IP アドレスに接続してサービスにアクセスします。 Traffic Manager は、DNS を使用して、トラフィックのルーティング メソッドのルールに基づいてクライアントを特定のサービス エンドポイントに転送します。 選択されたエンドポイントに、クライアントが直接接続します。 Traffic Manager は、プロキシでもゲートウェイでもありません。 Traffic Manager は、クライアントとサービスの間を通過するトラフィックを認識することはありません。

### <a name="azure-network-validation"></a>Azure ネットワークの検証

Azure ネットワークの検証は、Azure ネットワークが構成されているとおりに動作することを確認するために、ネットワークの監視に使用可能なサービスや機能を使用して実行されます。 Azure Network Watcher を使用すると、多数のロギ記録機能や診断機能にアクセスし、ネットワークのパフォーマンスや正常性を把握するのに役立ちます。 これらの機能は、ポータル、PowerShell、CLI、Rest API および SDK を使用してアクセスできます。

Azure で運用可能なセキュリティとは、ユーザーのデータ、アプリケーション、および Microsoft Azure にあるその他の資産を保護するために使用できる、サービス、コントロール、機能を指します。 Azure で運用可能なセキュリティは、Microsoft セキュリティ開発ライフサイクル (Security Development Lifecycle: SDL)、Microsoft セキュリティ レスポンス センター プログラム、およびサイバー セキュリティの脅威状況に対する深い認識など、Microsoft に固有のさまざまな機能の使用経験から得られた知識が組み込まれたフレームワーク上に構築されています。

-   [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)

-   [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview)

-   [Azure Network watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)

-   [Azure Storage analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)

-   Azure Resource Manager

#### <a name="azure-resource-manager"></a>Azure Resource Manager

Microsoft Azure を運用するユーザーやプロセスは、おそらくプラットフォームの最も重要なセキュリティ機能です。 このセクションはセキュリティ、継続性、プライバシーの強化と維持に役立つ、マイクロソフトのグローバル データセンター インフラストラクチャの機能について説明します。

アプリケーションのインフラストラクチャは通常、仮想マシン、ストレージ アカウント、仮想ネットワーク、Web アプリ、データベース、データベース サーバー、サード パーティのサービスなど、複数のコンポーネントで構成されます。 これらのコンポーネントは別々のエンティティではなく、1 つのエンティティの中で互いに関連付けられ相互依存しています。 これらのコンポーネントを、1 つのグループとしてデプロイ、管理、および監視するのが好ましいです。 Azure Resource Manager を使用すると、ソリューション内の複数のリソースを 1 つのグループとして作業できます。

ソリューションのこれらすべてのリソースを、1 回の連携した操作でデプロイ、更新、または削除できます。 デプロイにはテンプレートを使用しますが、このテンプレートは、テスト、ステージング、運用環境などのさまざまな環境に使用できます。 Resource Manager には、デプロイ後のリソースの管理に役立つ、セキュリティ、監査、タグ付けの機能が用意されています。

**Resource Manager を使用する利点**

リソース マネージャーには、いくつかの利点があります。

-   ソリューションのリソースを個別に処理するのではなく、すべてのリソースをグループとしてデプロイ、管理、監視できます。

-   ソリューションを開発のライフサイクル全体で繰り返しデプロイできます。また、常にリソースが一貫した状態でデプロイされます。

-   スクリプトではなく宣言型のテンプレートを使用してインフラストラクチャを管理できます。

-   正しい順序でデプロイされるようにリソース間の依存性を定義できます。

-   ロールベースのアクセス制御 (RBAC) が管理プラットフォームにネイティブ統合されるため、リソース グループのすべてのサービスにアクセス制御を適用できます。

-   タグをリソースに適用し、サブスクリプションのすべてのリソースを論理的に整理できます。

-   タグを共有するリソース グループのコストを表示することで、組織の課金をわかりやすくすることができます。

> [!Note]
> リソース マネージャーには、ソリューションをデプロイして管理するための新しい方法が用意されています。 以前のデプロイメント モデルを使用していて、変更の詳細を確認する場合は、「 [Resource Manager デプロイと従来のデプロイの理解](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model) 」を参照してください。

## <a name="azure-network-logging-and-monitoring"></a>Azure ネットワークのログ記録と監視

Azure にはネットワーク セキュリティのイベントを監視、保護、検出、および応答する数多くのツールが用意されています。 この分野で使用できる強力なツールの一部は次のとおりです。

-   Network Watcher

-   ネットワーク リソース レベルの監視

-   Azure Monitor ログ

### <a name="network-watcher"></a>Network Watcher

[Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) - シナリオベースの監視は、Network Watcher の機能を使用して実現できます。 このサービスには、パケット キャプチャ、次のホップ、IP フロー検証、セキュリティ グループ ビュー、NSG フロー ログなどが搭載されています。 シナリオ レベルの監視では、個別のネットワーク リソースの監視とは対照的に、ネットワーク リソースを隅から隅まで確認できます。

 ![Network Watcher](./media/azure-network-security/azure-network-security-fig-15.png)

Network Watcher は地域サービスであり、ネットワーク シナリオ レベルで Azure 内と Azure 間の状態を監視して診断できます。 Network Watcher に搭載されているネットワークの診断および監視ツールを使用して、Azure 内のネットワークを把握および診断し、洞察を得ることができます。

現在、Network Watcher が備える機能は次のとおりです。

#### <a name="topology"></a>トポロジ

[トポロジ](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-overview)は、仮想ネットワーク内のネットワーク リソースの図式を返します。 図式は、エンド ツー エンドのネットワーク接続を表すリソース間の相互接続を示します。 ポータルで、トポロジは仮想ネットワークごとにリソース オブジェクトを返します。 リレーションシップがリソース間の線で表されています。Network Watcher リージョンの外部のリソースは、リソース グループに含まれていても表示されません。 ポータルのビューに返されるリソースは、図式化されたネットワーク コンポーネントのサブセットです。 すべてのネットワーク リソースの一覧を表示するには、[PowerShell](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-powershell) または [REST](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-rest) を使用します。

リソースが返されるときに、2 つのリレーションシップに基づいてリソース間の接続がモデル化されます。

- **含有** - Virtual Network にサブネットが含まれ、サブネットに NIC が含まれる。

- **関連付け** - NIC が VM に関連付けられている。

#### <a name="variable-packet-capture"></a>可変パケット キャプチャ

Network Watcher の[可変パケット キャプチャ](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview)を使用すると、仮想マシンとの間で送受信されるトラフィックを追跡するパケット キャプチャ セッションを作成できます。 パケット キャプチャは、事後と事前に、ネットワークの異常を診断するのに役立ちます。 その他の用途には、ネットワーク統計の収集、ネットワークへの侵入に関する情報を取得などがあり、クライアント サーバー間の通信のデバッグなどに役立ちます。

パケット キャプチャは、Network Watcher によりリモートで開始される、仮想マシン拡張機能です。 この機能により、目的の仮想マシンでパケット キャプチャを手動で実行する負担が軽減され、貴重な時間の節約になります。 パケット キャプチャは、ポータル、PowerShell、CLI、REST API を介してトリガーできます。 パケット キャプチャをトリガーできる方法の一例は、仮想マシンのアラートの使用です。

#### <a name="ip-flow-verify"></a>IP フロー検証

[IP フロー検証](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview)を使用すると、仮想マシンから送受信されるパケットの許可または拒否の状況を 5 タプルの情報に基づいて検証できます。 この情報は、方向、プロトコル、ローカル IP、リモート IP、ローカル ポート、リモート ポートで構成されます。 パケットがセキュリティ グループにより拒否された場合、そのパケットを拒否した規則の名前が返されます。 管理者は、任意の送信元または送信先の IP を選択でき、IP フロー検証機能を使用してインターネットまたはオンプレミス環境との接続の問題をすばやく診断できます。

IP フロー検証では、仮想マシンのネットワーク インターフェイスが対象になります。 次に、そのネットワーク インターフェイスに関する構成済みの設定に基づいてトラフィック フローが検証されます。 この機能は、仮想マシンとの間のイングレスまたはエグレス トラフィックがネットワーク セキュリティ グループ内の規則によってブロックされているかどうかを確認するのに役立ちます。

#### <a name="next-hop"></a>次のホップ

Azure Network Fabric におけるルーティング対象パケットの[次ホップ](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview)を特定します。これにより、誤って構成されたユーザー定義のルーティングがあるかどうかを診断できます。 VM からのトラフィックは、NIC に関連付けられた有効なルートをもとに、送信先に送信されます。 次ホップは、特定の仮想マシンと NIC から次ホップの種類とパケットの IP アドレスを取得します。 これにより、パケットが送信先に向かっているのか、またはトラフィックが失われているのかを判断するのに役立ちます。

また、次ホップは関連するルート テーブルを返します。 ルートがユーザー定義のルートとして定義されている場合に次ホップをクエリすると、そのルートが返されます。 それ以外の場合、次ホップは "システム ルート" を返します。

#### <a name="security-group-view"></a>セキュリティ グループ ビュー

VM に適用されている有効な適用セキュリティ規則を確認できます。 ネットワーク セキュリティ グループは、サブネット レベルまたは NIC レベルで関連付けられています。 サブネット レベルに関連付けた場合、サブネット内のすべての VM インスタンスに適用されます。 ネットワーク [セキュリティ グループ ビュー](https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview)は、仮想マシン用に NIC レベルまたはサブネット レベルで関連付けられた、すべての構成済み NSG と規則を返して、構成に対する洞察を提供します。 さらに、有効なセキュリティ規則を VM の各 NIC に返します。 ネットワーク セキュリティ グループ ビューを使用することで、開いているポートなどのネットワークの脆弱性について VM を評価できます。 また、[構成されたセキュリティ規則と有効なセキュリティ規則とを比較](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-auditing-powershell)することで、ネットワーク セキュリティ グループが想定どおりに機能しているかどうかを確認できます。

#### <a name="nsg-flow-logging"></a>NSG フロー ログの記録

 ネットワーク セキュリティ グループのフロー ログにより、そのグループのセキュリティ規則で許可または拒否されるトラフィックに関係するログを記録できます。 フローは 5 タプル情報 (送信元 IP、宛先 IP送信元ポート、宛先ポート、プロトコル) で定義されます。

[ネットワーク セキュリティ グループのフローのログ](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview)は、ネットワーク セキュリティ グループを使用した受信および送信 IP トラフィックに関する情報を表示できる Network Watcher の機能です。

#### <a name="virtual-network-gateway-and-connection-troubleshooting"></a>Virtual Network ゲートウェイと接続のトラブルシューティング

Network Watcher は、Azure 内のネットワーク リソースの把握に関する多くの機能を提供します。 これらの機能の 1 つが、リソースのトラブルシューティングです。 [リソースのトラブルシューティング](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest)は、PowerShell、CLI、または REST API から呼び出すことができます。 呼び出されると、Network Watcher は仮想ネットワーク ゲートウェイまたは接続の正常性を検査し、その結果を返します。

このセクションでは、リソースのトラブルシューティングで現在利用できるさまざまな管理タスクについて説明します。

-   [Virtual Network ゲートウェイのトラブルシューティング](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest)

-   [接続のトラブルシューティング](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest)

#### <a name="network-subscription-limits"></a>ネットワーク サブスクリプションの制限

[ネットワーク サブスクリプションの制限](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)では、リージョン内のサブスクリプションに含まれる各ネットワーク リソースの使用状況の詳細を、利用可能なリソースの最大数と照らし合わせて確認できます。

#### <a name="configuring-diagnostics-log"></a>診断ログの構成

Network Watcher には[診断ログ](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) ビューが用意されています。 このビューには、診断ログをサポートするすべてのネットワーク リソースが表示されます。 このビューから、ネットワーク リソースの有効化および無効化を手軽に素早く行うことができます。

### <a name="network-resource-level-monitoring"></a>ネットワーク リソース レベルの監視

リソース レベルの監視では次の機能を使用できます。

#### <a name="audit-log"></a>監査ログ

ネットワークの構成の一環として実行された操作が記録されます。 これらの監査ログは各種コンプライアンスを確立するために必須です。 これらのログは、Azure Portal で確認することも、Power BI などの Microsoft ツールやサードパーティ ツールを使用して取得することもできます。 監査ログは、ポータル、PowerShell、CLI、Rest API で利用できます。

> [!Note]
> 監査ログについて詳しくは、[Resource Manager の監査操作](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit)に関するページをご覧ください。
監査ログは、すべてのネットワーク リソースに対して実行された操作について用意されています。


#### <a name="metrics"></a>メトリック

メトリックとは、一定期間にわたり収集されたパフォーマンスの測定値とカウンターのことです。 現時点では、メトリックは Application Gateway で利用できます。 メトリックを使用すると、しきい値に基づいてアラートをトリガーすることができます。 既定では、Azure Application Gateway はバック エンド プールにあるすべてのリソースの状態を監視して、異常とみなしたリソースをプールから自動的に削除します。 Application Gateway は異常なインスタンスを継続的に監視し、このインスタンスが利用可能になり正常性プローブに応答するようになると、正常バック エンド プールに戻します。 Application Gateway は、バックエンドの HTTP 設定で定義されているポートを使用して正常性プローブを送信します。 この構成により、顧客がバックエンドへの接続に使用しているポートをプローブで確実にテストできます。

> [!Note]
> メトリックを使用してアラートを作成する方法については、[Application Gateway の診断機能](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview)に関するページを参照してください。

#### <a name="diagnostic-logs"></a>診断ログ

定期イベントおよび自然発生イベントがネットワーク リソースにより作成され、ストレージ アカウント内に記録されるか、イベント ハブまたは Azure Monitor ログへ送信されます。 こうしたログから、リソースの正常性に関する詳細が得られます。 これらのログは、Power BI や Azure Monitor ログなどのツールで確認できます。 診断ログを確認する方法については、[Azure Monitor ログ](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics)に関するページを参照してください。

診断ログは、[ロード バランサー](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log)、[ネットワーク セキュリティ グループ](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)、ルーティング、および [Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics) で利用できます。

Network Watcher には診断ログ ビューが用意されています。 このビューには、診断ログをサポートするすべてのネットワーク リソースが表示されます。 このビューから、ネットワーク リソースの有効化および無効化を手軽に素早く行うことができます。

### <a name="azure-monitor-logs"></a>Azure Monitor ログ

[Azure Monitor ログ](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)は、可用性やパフォーマンスの維持を目的としてクラウド環境とオンプレミス環境を監視する Azure のサービスです。 Log Analytics を使用すると、クラウドおよびオンプレミスの環境内にあるリソースによって生成されたデータや、他の監視ツールのデータを収集し、複数のソースにわたる分析を行えます。

Azure Monitor ログには、ネットワークを監視することを目的とした次のソリューションが用意されています。

-   ネットワーク パフォーマンス モニター (NPM)

-   Azure Application Gateway 分析

-   Azure Network Security Group 分析

#### <a name="network-performance-monitor-npm"></a>Network Performance Monitor (NPM)
[Network Performance Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-network-performance-monitor) 管理ソリューションは、ネットワークの正常性、可用性、到達の可能性を監視するネットワーク監視ソリューションです。

次の 2 点間の接続を監視する目的で使用します。

-   パブリック クラウドとオンプレミス

-   データ センターとユーザー拠点 (支社)

-   多層アプリケーションの各種階層をホストするサブネット間


#### <a name="azure-application-gateway-analytics-in-azure-monitor-logs"></a>Azure Monitor ログの Azure Application Gateway 分析

Application Gateway に関しては、次のログがサポートされます。

-   ApplicationGatewayAccessLog

-   ApplicationGatewayPerformanceLog

-   ApplicationGatewayFirewallLog

Application Gateway に関しては、次のメトリックがサポートされます。

-   5 分間のスループット

#### <a name="azure-network-security-group-analytics-in-azure-monitor-logs"></a>Azure Monitor ログの Azure ネットワーク セキュリティ グループ分析

[ネットワーク セキュリティ グループ](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)に関しては、次のログがサポートされます。

- **NetworkSecurityGroupEvent:** MAC アドレスに基づいた、VM とインスタンス ロールに適用される NSG ルールに関するエントリが含まれます。 これらのルールの状態は 60 秒ごとに収集されます。

- **NetworkSecurityGroupRuleCounter:** トラフィックを拒否または許可するために各 NSG ルールが適用された回数に関するエントリが含まれます。

## <a name="next-steps"></a>次の手順
セキュリティについて詳しくは、セキュリティについて掘り下げた次のトピックをご覧ください。

-   [ネットワーク セキュリティ グループ (NSG) の Azure Monitor ログ](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)

-   [クラウドの分断を促進するネットワークの革新](https://azure.microsoft.com/blog/networking-innovations-that-drive-the-cloud-disruption/)

-   [SONiC: マイクロソフトの世界規模のクラウドを支えるネットワーク スイッチ ソフトウェア](https://azure.microsoft.com/blog/sonic-the-networking-switch-software-that-powers-the-microsoft-global-cloud/)

-   [マイクロソフトが高速で信頼性の高いグローバル ネットワークを構築する方法](https://azure.microsoft.com/blog/how-microsoft-builds-its-fast-and-reliable-global-network/)

-   [ネットワークの革新に着手する](https://azure.microsoft.com/blog/lighting-up-network-innovation/)
