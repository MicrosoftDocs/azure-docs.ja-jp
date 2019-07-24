---
title: SAP HANA on Azure (L インスタンス) のネットワーク アーキテクチャ | Microsoft Docs
description: SAP HANA on Azure (L インスタンス) をデプロイする方法のネットワーク アーキテクチャ。
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 724a91b6ba0be030a2281bce366e4378892df59b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58011582"
---
# <a name="sap-hana-large-instances-network-architecture"></a>SAP HANA (L インスタンス) のネットワーク アーキテクチャ

Azure ネットワーク サービスのアーキテクチャは、HANA L インスタンスへの SAP アプリケーションのデプロイを成功させるための重要な要素です。 通常、SAP HANA on Azure (L インスタンス) デプロイメントには、サイズとデータベース、CPU リソース使用量、メモリ使用量が異なる複数の SAP ソリューションを含む大規模な SAP ランドスケープが存在します。 このような SAP システムのすべてが SAP HANA に基づくわけではないと考えられます。 ほとんどの場合、SAP ランドスケープは、次のコンポーネントを使用するハイブリッド構成になります。

- デプロイされた SAP システム (オンプレミス)。 サイズに起因して、現在、これらのシステムは Azure でホストできません。 例として、SQL Server (データベースとして使用) で実行され、VM が提供できるものよりも多くの CPU またはメモリ リソースを必要とする、SAP ERP 実稼働システムが挙げられます。
- デプロイされたSAP HANA ベースの SAP システム (オンプレミス)。
- VM にデプロイされた SAP システム。 これらのシステムは、Azure (VM) に正常にデプロイできる SAP NetWeaver ベースのアプリケーション用の (リソース使用量とメモリの需要に基づく) 開発、テスト、サンドボックス、または実稼働インスタンスです。 これらのシステムは、SQL Server などのデータベースに基づく場合もあります。 詳細については、次を参照してください: 「[SAP Support Note #1928533 - SAP applications on Azure: Supported products and Azure VM types (SAP サポート ノート #1928533 - Azure 上の SAP アプリケーション: サポート対象の製品と Azure VM の種類)](https://launchpad.support.sap.com/#/notes/1928533/E)」。 これらのシステムは、SAP HANA などのデータベースに基づく場合もあります。 詳細については、[SAP HANA 認定 IaaS プラットフォーム](https://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html)に関するページをご覧ください。
- Azure L インスタンス スタンプの SAP HANA on Azure (L インスタンス) を活用する、Azure (VM) にデプロイされた SAP アプリケーション サーバー。

4 種類以上のデプロイ シナリオがあるハイブリッド SAP ランドスケープが一般的です。 Azure で実行されている完全な SAP ランドスケープの顧客事例も多数あります。 VM の強化に伴って、すべての SAP ソリューションを Azure に移行するお客様が増えています。

Azure にデプロイされた SAP システムのコンテキストでの Azure ネットワークは複雑ではありません。 このネットワークは次の原則に基づいています。

- Azure 仮想ネットワークは、オンプレミスのネットワークに接続する ExpressRoute 回線に接続する必要があります。
- オンプレミスを Azure に接続する ExpressRoute 回線には、通常、1 Gbps 以上の帯域幅が必要です。 この最小帯域幅により、オンプレミスのシステムと VM で実行されているシステム間のデータ転送用の十分な帯域幅を確保できます。 また、オンプレミスのユーザーから Azure システムへの接続用の十分な帯域幅も確保できます。
- Azure 内のすべての SAP システムを仮想ネットワークで設定して、相互通信を可能にする必要があります。
- オンプレミスでホストされる Active Directory と DNS は、ExpressRoute 経由でオンプレミスから Azure に拡張されます。


> [!NOTE] 
> 課金の観点から、1 つの Azure サブスクリプションは、特定の Azure リージョン内の L インスタンス スタンプの 1 つのテナントにのみリンクできます。 逆に言うと、1 つの L インスタンス スタンプのテナントは 1 つの Azure サブスクリプションにのみリンクできます。 この要件は、Azure の他の課金対象オブジェクトと変わりません。

SAP HANA on Azure (L インスタンス) を複数の異なる Azure リージョンにデプロイすると、別のテナントが L インスタンス スタンプにデプロイされます。 これらのインスタンスが同じ SAP ランドスケープに含まれていれば、それらを同じ Azure サブスクリプションで実行できます。 

> [!IMPORTANT] 
> SAP HANA on Azure (L インスタンス) でサポートされるのは、Azure Resource Manager デプロイメントだけです。

 

## <a name="additional-virtual-network-information"></a>仮想ネットワークの追加情報

仮想ネットワークを ExpressRoute に接続するには、Azure ゲートウェイを作成する必要があります。 詳細については、「[ExpressRoute 用の仮想ネットワーク ゲートウェイについて](../../../expressroute/expressroute-about-virtual-network-gateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」をご覧ください。 

Azure ゲートウェイは、Azure の外部のインフラストラクチャまたは Azure インスタンス スタンプへの ExpressRoute で使用できます。 また、仮想ネットワーク間の接続に使用することもできます。 詳細については、[PowerShell を使用した Resource Manager のネットワーク間接続の構成](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関する記事をご覧ください。 ExpressRoute の接続元が、それぞれ異なる Microsoft エンタープライズ エッジ ルーターであれば、Azure ゲートウェイを最大 4 つの異なる ExpressRoute に接続できます。 詳細については、「[Azure での SAP HANA on Azure (L インスタンス) のインフラストラクチャと接続](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」をご覧ください。 

> [!NOTE] 
> Azure ゲートウェイが提供するスループットは 2 つのユース ケースで異なります。 詳細については、「[VPN Gateway について](../../../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」をご覧ください。 ExpressRoute 接続を使用して仮想ネットワーク ゲートウェイで実現できる最大スループットは 10 Gbps です。 仮想ネットワーク内の VM とオンプレミスのシステム間で (単一のコピー ストリームとして) ファイルをコピーすると、さまざまなゲートウェイ SKU の最大限のスループットを実現できません。 仮想ネットワーク ゲートウェイのすべての帯域幅を活用するには、複数のストリームを使用します。 または、1 つのファイルのパラレル ストリームで複数のファイルをコピーする必要があります。


## <a name="networking-architecture-for-hana-large-instance"></a>HANA L インスタンスのネットワーク アーキテクチャ
HANA L インスタンスのネットワーク アーキテクチャは、次の 4 つの部分に分けることができます。

- オンプレミス ネットワークと Azure への ExpressRoute 接続。 この部分はお客様の領域であり、ExpressRoute 経由で Azure に接続されます。 次の図の右下の部分を参照してください。
- 仮想ネットワークを使用する前述の Azure ネットワーク サービス (ここでもゲートウェイを使用)。 この部分は、アプリケーションの要件、セキュリティ、コンプライアンス要件に適した設計を見つける必要がある領域です。 仮想ネットワークの数と選択する Azure ゲートウェイ SKU の観点で、HANA L インスタンスを使用するかどうかがもう 1 つの検討事項となります。 図の右上の部分を参照してください。
- ExpressRoute テクノロジを使用した Azure への HANA L インスタンスの接続。 この部分は Microsoft によってデプロイされ、処理されます。 お客様が行う必要があるのは、HANA L インスタンスへの資産のデプロイ後に、ExpressRoute 回線を仮想ネットワークに接続する IP アドレス範囲を指定するだけです。 詳細については、「[Azure での SAP HANA on Azure (L インスタンス) のインフラストラクチャと接続](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」をご覧ください。 
- HANA L インスタンスでのネットワーキング。ほとんどの場合、これはお客様に対して透過的です。

![SAP HANA on Azure (L インスタンス) およびオンプレミスに接続された仮想ネットワーク](./media/hana-overview-architecture/image3-on-premises-infrastructure.png)

HANA L インスタンスを使用するため、オンプレミスの資産を ExpressRoute 経由で Azure に接続するという要件は変わりません。 また、HANA L インスタンス ユニットでホストされている HANA インスタンスに接続するアプリケーション層をホストする VM を、1 つまたは複数の仮想ネットワークで実行するための要件も変わりません。 

Azure での SAP デプロイとの違いは次のとおりです。

- 顧客テナントの HANA L インスタンス ユニットは、別の ExpressRoute 回線を介して仮想ネットワークに接続されます。 負荷条件を分離するために、オンプレミスと仮想ネットワーク間の ExpressRoute リンクと、仮想ネットワークと HANA L インスタンス間のリンクは、同じルーターを共有しません。
- SAP アプリケーション層と HANA L インスタンス間のワークロード プロファイルは、SAP HANA からアプリケーション層への多数の小さな要求とデータ転送 (結果セット) などのバーストという異なる特性を示します。
- SAP アプリケーション アーキテクチャは、オンプレミスと Azure 間でデータが交換される標準的なシナリオよりも、ネットワーク待機時間の影響を受けやすくなります。
- 仮想ネットワーク ゲートウェイには少なくとも 2 つの ExpressRoute 接続があります。 これらの接続は、仮想ネットワーク ゲートウェイの受信データの最大帯域幅を共有します。

VM と HANA L インスタンス ユニット間で発生するネットワーク待機時間は、VM 間のネットワーク ラウンド トリップの標準的な待機時間よりも長くなる可能性があります。 Azure リージョンによっては、測定値が、次のノートで平均以下に分類されているラウンドトリップの待ち時間 (0.7 ms) を超える可能性があります: 「[SAP Note #1100926 - FAQ: Network performance (SAP ノート #1100926 - FAQ: ネットワーク パフォーマンス)](https://launchpad.support.sap.com/#/notes/1100926/E)」。 Azure VM と HANA L インスタンス ユニットの間のネットワーク ラウンド トリップ待機時間を測定するツールと Azure リージョンによっては、測定される待機時間が最大約 2 ミリ秒になる可能性があります。 しかし、お客様は SAP HANA ベースの実稼働 SAP アプリケーションを SAP HANA L インスタンスに問題なくデプロイしています。 Azure HANA L インスタンスでビジネス プロセスを十分にテストしてください。
 
VM と HANA L インスタンス間の決定論的なネットワーク待機時間を実現するには、仮想ネットワーク ゲートウェイ SKU の選択が不可欠となります。 オンプレミスと VM 間のトラフィック パターンとは異なり、VM と HANA L インスタンス間のトラフィック パターンでは、送信される要求やデータ量は小さくても、大きなバーストが発生する可能性があります。 このようなバーストを適切に処理するために、UltraPerformance ゲートウェイ SKU を使用することを強くお勧めします。 Type II クラスの HANA L インスタンス SKU の場合、仮想ネットワーク ゲートウェイとして UltraPerformance ゲートウェイ SKU を使用することが必須となります。

> [!IMPORTANT] 
> SAP アプリケーション層とデータベース層の間の全体的なネットワーク トラフィックを考慮し、SAP HANA on Azure (L インスタンス) への接続では、仮想ネットワーク用の HighPerformance または UltraPerformance ゲートウェイ SKU だけがサポートされます。 HANA L インスタンスの Type II SKU の場合、仮想ネットワーク ゲートウェイとしてサポートされるのは UltraPerformance ゲートウェイ SKU だけです。



## <a name="single-sap-system"></a>単一の SAP システム

上記のオンプレミスのインフラストラクチャは ExpressRoute 経由で Azure に接続されます。 ExpressRoute 回線は、エンタープライズ エッジ ルーターに接続されます。 詳細については、[ExpressRoute の技術概要](../../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関する記事をご覧ください。 ルートが確立されると、そのルートは Azure バックボーンに接続され、すべての Azure リージョンにアクセスできるようになります。

> [!NOTE] 
> Azure で SAP ランドスケープを実行するには、SAP ランドスケープの Azure リージョンに最も近いエンタープライズ エッジ ルーターに接続します。 Azure IaaS 内の VM と L インスタンス スタンプ間のネットワーク待ち時間を最小限に抑えるために、Azure L インスタンス スタンプは専用のエンタープライズ エッジ ルーター デバイスを介して接続されます。

SAP アプリケーション インスタンスをホストする VM の仮想ネットワーク ゲートウェイは ExpressRoute 回線に接続されます。 同じ仮想ネットワークが、L インスタンス スタンプへの接続専用の別のエンタープライズ エッジ ルーターに接続されます。

このシステムは、単一の SAP システムのわかりやすい例です。 SAP アプリケーション層は Azure でホストされます。 SAP HANA データベースは SAP HANA on Azure (L インスタンス) で実行されます。 仮想ネットワーク ゲートウェイの帯域幅 (2 Gbps または 10 Gbps のスループット) がボトルネックにならないことが前提となります。

## <a name="multiple-sap-systems-or-large-sap-systems"></a>複数の SAP システムまたは大規模な SAP システム

SAP HANA on Azure (L インスタンス) に接続する複数の SAP システムまたは大規模な SAP システムをデプロイする場合、仮想ネットワーク ゲートウェイのスループットがボトルネックになることがあります。 このような場合は、アプリケーション層を複数の仮想ネットワークに分割します。 次のような場合には、HANA L インスタンスに接続する特別な仮想ネットワークを作成することもできます。

- HANA L インスタンス内の HANA インスタンスから、NFS 共有をホストする Azure の VM に直接バックアップを実行する。
- HANA L インスタンス ユニットから、バックアップ ファイルなど大きなファイルを Azure 内の管理されたディスク領域にコピーする。

別の仮想ネットワークを使用して、ストレージを管理する VM をホストします。 この配置では、HANA L インスタンスから Azure に大きなファイルやデータを転送することによる影響が、SAP アプリケーション層を実行する VM に対応する仮想ネットワーク ゲートウェイに及ぶことはありません。 

スケーラブルなネットワーク アーキテクチャでは次のように対応します。

- 1 つの大規模な SAP アプリケーション層に対して複数の仮想ネットワークを活用します。
- 同じ仮想ネットワークの個々のサブネットに SAP システムの組み合わせを配置するのではなく、デプロイされた SAP システムごとに個別の仮想ネットワークをデプロイします。

  SAP HANA on Azure (L インスタンス) 用のスケーラブルなネットワーク アーキテクチャを次に示します。

![複数の仮想ネットワークにわたる SAP アプリケーション層のデプロイ](./media/hana-overview-architecture/image4-networking-architecture.png)

この図は、複数の仮想ネットワークにわたってデプロイされた SAP アプリケーション層 (コンポーネント) を示しています。 この構成では、これらの仮想ネットワークでホストされるアプリケーション間の通信時に発生する待機時間のオーバーヘッドを回避できませんでした。 既定では、異なる仮想ネットワークにある VM 間のネットワーク トラフィックは、この構成では エンタープライズ エッジ ルーター経由でルーティングされます。 2 つの仮想ネットワーク間の通信を最適化し、待機時間を短縮するには、同じリージョン内の仮想ネットワークをピアリングします。 この方法は、それらの仮想ネットワークが異なるサブスクリプションに存在する場合でも機能します。 仮想ネットワークのピアリングを使用すると、2 つの異なる仮想ネットワーク内の VM 間の通信は、Azure ネットワーク バックボーンを使用して相互に直接通信できます。 VM が同じ仮想ネットワークに存在する場合と同様の待機時間が示されます。 Azure 仮想ネットワーク ゲートウェイ経由で接続される IP アドレス範囲のトラフィックは、仮想ネットワークの個々の仮想ネットワーク ゲートウェイ経由でルーティングされます。 

仮想ネットワーク ピアリングの詳細については、「[仮想ネットワーク ピアリング](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)」をご覧ください。


## <a name="routing-in-azure"></a>Azure でのルーティング

SAP HANA on Azure (L インスタンス) のネットワーク ルーティングに関する 3 つの重要な考慮事項を次に示します。

* SAP HANA on Azure (L インスタンス) には、VM および専用の ExpressRoute 接続経由でのみアクセスできます。オンプレミスから直接アクセスすることはできません。 オンプレミスから HANA L インスタンス ユニットへの直接アクセスは、Microsoft から提供されるため、すぐには実現できません。 この一時的なルーティングの制限は、SAP HANA L インスタンスに使用されている現在の Azure ネットワーク アーキテクチャに起因します。 直接アクセスを必要とする管理クライアントやアプリケーション (オンプレミスで実行されている SAP Solution Manager など) は、SAP HANA データベースに接続できません。

* ディザスター リカバリーのために 2 つの異なる Azure リージョンにデプロイされている HANA L インスタンス ユニットがある場合、同じ一時的なルーティングの制限が適用されます。 つまり、あるリージョン (例: 米国西部) の HANA L インスタンス ユニットの IP アドレスは、別のリージョン (例: 米国東部) で展開されている HANA L インスタンス ユニットにルーティングされません。 この制限は、リージョン間の Azure ネットワーク ピアリング、または HANA L インスタンス ユニットを仮想ネットワークに接続する ExpressRoute 回線の交差接続の使用とは無関係です。 図については、「複数のリージョンでの HANA L インスタンス ユニットの使用」の図を参照してください。 デプロイ済みのアーキテクチャに伴うこの制限があるため、HANA システム レプリケーションをディザスター リカバリー機能としてすぐに使用することはできません。

* SAP HANA on Azure (L インスタンス) ユニットには、お客様が提出したサーバー IP プールのアドレス範囲から IP アドレスが割り当てられます。 詳細については、「[Azure での SAP HANA on Azure (L インスタンス) のインフラストラクチャと接続](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」をご覧ください。 この IP アドレスには、仮想ネットワークを HANA on Azure (L インスタンス) に接続する ExpressRoute と Azure サブスクリプションを使用してアクセスできます。 サーバー IP プールのアドレス範囲から割り当てられる IP アドレスは、ハードウェア ユニットに直接割り当てられます。 このソリューションの最初のデプロイでこの割り当てが行われるため、NAT を使用した割り当ては*行われません*。 

> [!NOTE]
> 上記の最初の 2 つのリスト項目で説明した一時的なルーティングの制限を克服するには、ルーティングに追加のコンポーネントを使用します。 制限を克服するために使用できるコンポーネントは次のとおりです。
> 
> * データをルーティングするリバース プロキシ。 たとえば、仮想ファイアウォール/トラフィック ルーティング ソリューションとして Azure にデプロイする F5 BIG-IP や NGINX (および Traffic Manager) などです。
> * Linux VM で [IPTables ルール](http://www.linuxhomenetworking.com/wiki/index.php/Quick_HOWTO_%3a_Ch14_%3a_Linux_Firewalls_Using_iptables#.Wkv6tI3rtaQ)を使用して、オンプレミスの場所と HANA L インスタンス ユニット間、または別のリージョンにある HANA L インスタンス ユニット間のルーティングを有効にします。
> 
> サード パーティのネットワーク アプライアンスまたは IPTables を使用するカスタム ソリューションの実装とサポートは、Microsoft では提供されていないことに注意してください。 使用するコンポーネントのベンダーまたはインテグレーターがサポートを提供する必要があります。 

## <a name="internet-connectivity-of-hana-large-instance"></a>HANA L インスタンスのインターネット接続
HANA L インスタンスは、インターネットに直接接続することは*できません*。 たとえば、この制限によって、OS イメージを OS ベンダーに直接登録することができない可能性があります。 ローカルの SUSE Linux Enterprise Server Subscription Management Tool サーバーや Red Hat Enterprise Linux Subscription Manager を使用することが必要になる場合があります。

## <a name="data-encryption-between-vms-and-hana-large-instance"></a>VM と HANA L インスタンス間でのデータの暗号化
HANA L インスタンスと VM 間で転送されるデータは暗号化されません。 ただし、HANA DBMS 側と JDBC/ODBC ベースのアプリケーションとの間でのデータ交換のみを目的としている場合は、トラフィックの暗号化を有効にすることができます。 詳細については、[SAP から提供されているこちらのドキュメント](http://help-legacy.sap.com/saphelp_hanaplatform/helpdata/en/db/d3d887bb571014bf05ca887f897b99/content.htm?frameset=/en/dd/a2ae94bb571014a48fc3b22f8e919e/frameset.htm&current_toc=/en/de/ec02ebbb57101483bdf3194c301d2e/plain.htm&node_id=20&show_children=false)をご覧ください。

## <a name="use-hana-large-instance-units-in-multiple-regions"></a>複数のリージョンでの HANA L インスタンス ユニットの使用

ディザスター リカバリー以外の理由で、SAP HANA on Azure (L インスタンス) を複数の Azure リージョンにデプロイする場合があります。 たとえば、リージョン内の異なる仮想ネットワークにデプロイされた各 VM から HANA L インスタンスにアクセスする必要がある場合です。 異なる HANA L インスタンス ユニットに割り当てられた IP アドレスは、ゲートウェイ経由でインスタンスに直接接続されている仮想ネットワークを超えて伝達されることはありません。 そのため、仮想ネットワーク設計が若干変更されます。 仮想ネットワーク ゲートウェイは、エンタープライズ エッジ ルーターごとに 4 つの異なる ExpressRoute 回線を処理できます。 いずれかの L インスタンス スタンプに接続された各仮想ネットワークは、別の Azure リージョン内の L インスタンス スタンプに接続できます。

![異なる Azure リージョン内の Azure L インスタンス スタンプに接続された仮想ネットワーク](./media/hana-overview-architecture/image8-multiple-regions.png)

この図は、2 つのリージョン内の異なる仮想ネットワークが、2 つの Azure リージョン内の SAP HANA on Azure (L インスタンス) への接続に使用する 2 つの異なる ExpressRoute 回線に接続されるしくみを示しています。 新しく導入される接続を直交する赤い線で示しています。 仮想ネットワークからのこれらの接続により、いずれかの仮想ネットワークで実行される VM は、2 つのリージョンにデプロイされた各 HANA L インスタンス ユニットにアクセスできます。 図に示すように、オンプレミスから 2 つの Azure リージョンへの 2 つの ExpressRoute 接続があることが前提となります。 この配置は、ディザスター リカバリーに推奨されます。

> [!IMPORTANT] 
> 複数の ExpressRoute 回線を使用する場合は、トラフィックを適切にルーティングできるように、AS パス プリペンドとローカル プリファレンス BGP の設定を使用してください。

**次のステップ**
- 「[SAP HANA (Large Instances) storage architecture](hana-storage-architecture.md)」 (SAP HANA (L インスタンス) のストレージ アーキテクチャ) を参照してください。