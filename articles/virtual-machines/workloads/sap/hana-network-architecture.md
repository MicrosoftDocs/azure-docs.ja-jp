---
title: SAP HANA on Azure (L インスタンス) のネットワーク アーキテクチャ | Microsoft Docs
description: SAP HANA on Azure (L インスタンス) をデプロイする方法のネットワーク アーキテクチャ。
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 01001336e166d5eb2c7dff845b80da2174225a25
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234421"
---
# <a name="sap-hana-large-instances-network-architecture"></a>SAP HANA (L インスタンス) のネットワーク アーキテクチャ

Azure ネットワーク サービスのアーキテクチャは、HANA L インスタンスへの SAP アプリケーションのデプロイを成功させるための重要な要素です。 通常、SAP HANA on Azure (L インスタンス) デプロイメントには、サイズとデータベース、CPU リソース使用量、メモリ使用量が異なる複数の SAP ソリューションを含む大規模な SAP ランドスケープが存在します。 すべての IT システムが既に Azure に配置されているとは限りません。 また、SAP ランドスケープは、DBMS や SAP アプリケーションの面でハイブリッドである (NetWeaver、S/4HANA、SAP HANA、その他の DBMS を組み合わせて使用している) ことも少なくありません。 Azure では、さまざまな DBMS、NetWeaver、および S/4HANA システムを Azure で運用できるようにする、さまざまなサービスを提供しています。 また、オンプレミスのソフトウェア デプロイで Azure を仮想データ センターのように取り扱うためのネットワーク テクノロジも提供しています。

IT システム全体が Azure でホストされていない限り、 Azure のネットワーキング機能によりオンプレミスの世界と Azure の資産が接続されて、Azure を独自の仮想データセンターのように扱えるようになります。 使用される Azure ネットワーク機能は、次のとおりです。 

- Azure 仮想ネットワークは、オンプレミスのネットワーク資産に接続する [ExpressRoute](https://azure.microsoft.com/services/expressroute/) 回線に接続します。
- オンプレミスを Azure に接続する ExpressRoute 回線には、[1 Gbps 以上](https://azure.microsoft.com/pricing/details/expressroute/)の帯域幅が必要です。 この最小帯域幅により、オンプレミスのシステムと VM で実行されているシステム間のデータ転送用の十分な帯域幅を確保できます。 また、オンプレミスのユーザーから Azure システムへの接続用の十分な帯域幅も確保できます。
- Azure 内のすべての SAP システムは、仮想ネットワークで、相互通信するように設定されます。
- オンプレミスでホストされる Active Directory と DNS は、ExpressRoute 経由でオンプレミスから Azure に拡張されます。または、完全に Azure 内で運用されます。

HANA L インスタンスを Azure データ センター ネットワーク ファブリックに統合する場合には、ほかにも Azure ExpressRoute テクノロジが使用されます。


> [!NOTE] 
> 特定の Azure リージョンに存在する HANA L インスタンス スタンプ内の 1 つのテナントには、Azure サブスクリプションを 1 つだけリンクできます。 逆に、1 つの Azure サブスクリプションには、HANA L インスタンス スタンプ内のテナントを 1 つだけリンクできます。 この要件は、Azure の他の課金対象オブジェクトと変わりません。

SAP HANA on Azure (L インスタンス) を複数の異なる Azure リージョンにデプロイすると、HANA L インスタンス スタンプに別のテナントがデプロイされます。 これらのインスタンスが同じ SAP ランドスケープに含まれていれば、それらを同じ Azure サブスクリプションで実行できます。 

> [!IMPORTANT] 
> SAP HANA on Azure (L インスタンス) でサポートされるのは、Azure Resource Manager を使ったデプロイ方法だけです。

 

## <a name="additional-virtual-network-information"></a>仮想ネットワークの追加情報

仮想ネットワークを ExpressRoute に接続するには、Azure ExpressRoute ゲートウェイを作成する必要があります。 詳細については、[ExpressRoute 用の ExpressRoute ゲートウェイについて](../../../expressroute/expressroute-about-virtual-network-gateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)のページを参照してください。 

Azure ExpressRoute ゲートウェイは、Azure 外部のインフラストラクチャまたは Azure L インスタンス スタンプへの ExpressRoute で使用されます。 ExpressRoute の接続元が、それぞれ異なる Microsoft エンタープライズ エッジ ルーターであれば、Azure ExpressRoute ゲートウェイを最大 4 つの異なる ExpressRoute 回線に接続できます。 詳細については、「[Azure での SAP HANA on Azure (L インスタンス) のインフラストラクチャと接続](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」をご覧ください。 

> [!NOTE] 
> ExpressRoute 接続を使用して ExpressRoute ゲートウェイで実現できる最大スループットは 10 Gbps です。 仮想ネットワーク内の VM とオンプレミスのシステム間で (単一のコピー ストリームとして) ファイルをコピーすると、さまざまなゲートウェイ SKU の最大限のスループットを実現できません。 ExpressRoute ゲートウェイのすべての帯域幅を活用するには、複数のストリームを使用します。 または、1 つのファイルのパラレル ストリームで複数のファイルをコピーする必要があります。


## <a name="networking-architecture-for-hana-large-instance"></a>HANA L インスタンスのネットワーク アーキテクチャ
HANA L インスタンスのネットワーク アーキテクチャは、次の 4 つの部分に分けることができます。

- オンプレミス ネットワークと Azure への ExpressRoute 接続。 この部分はお客様の領域であり、ExpressRoute 経由で Azure に接続されます。 この Expressroute 回線に関する支払いは、全額がお客様の負担となります。 帯域幅は、オンプレミスの資産と接続先の Azure リージョンの間のネットワーク トラフィックを処理するのに十分な大きさにする必要があります。 次の図の右下の部分を参照してください。
- 仮想ネットワークを使用した前述の Azure ネットワーク サービス。繰り返しになりますが、仮想ネットワークには ExpressRoute ゲートウェイを追加する必要があります。 この部分は、アプリケーションの要件、セキュリティ、コンプライアンス要件に適した設計を見つける必要がある領域です。 仮想ネットワークの数と選択する Azure ゲートウェイ SKU の観点で、HANA L インスタンスを使用するかどうかがもう 1 つの検討事項となります。 図の右上の部分を参照してください。
- ExpressRoute テクノロジを使用した Azure への HANA L インスタンスの接続。 この部分は Microsoft によってデプロイされ、処理されます。 お客様が行う必要があるのは、HANA L インスタンスへの資産のデプロイ後に、ExpressRoute 回線を仮想ネットワークに接続する IP アドレス範囲を指定するだけです。 詳細については、「[Azure での SAP HANA on Azure (L インスタンス) のインフラストラクチャと接続](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」をご覧ください。 Azure データ センター ネットワーク ファブリックと HANA L インスタンス ユニットの間の接続について、お客様に追加料金が発生することはありません。
- HANA L インスタンス スタンプ内のネットワーキング。ほとんどの場合、お客様の側でこれを意識することはありません。

![SAP HANA on Azure (L インスタンス) およびオンプレミスに接続された仮想ネットワーク](./media/hana-overview-architecture/image1-architecture.png)

HANA L インスタンスを使用するため、オンプレミスの資産を ExpressRoute 経由で Azure に接続するという要件は変わりません。 また、HANA L インスタンス ユニットでホストされている HANA インスタンスに接続するアプリケーション層をホストする VM を、1 つまたは複数の仮想ネットワークで実行するための要件も変わりません。 

Azure での SAP デプロイとの違いは次のとおりです。

- 顧客テナントの HANA L インスタンス ユニットは、別の ExpressRoute 回線を介して仮想ネットワークに接続されます。 負荷条件を分離するために、オンプレミスと Azure 仮想ネットワークの間の ExpressRoute 回線と、Azure 仮想ネットワークと HANA L インスタンスの間の回線は、同じルーターを共有しません。
- SAP アプリケーション層と HANA L インスタンス間のワークロード プロファイルは、SAP HANA からアプリケーション層への多数の小さな要求とデータ転送 (結果セット) などのバーストという異なる特性を示します。
- SAP アプリケーション アーキテクチャは、オンプレミスと Azure 間でデータが交換される標準的なシナリオよりも、ネットワーク待機時間の影響を受けやすくなります。
- Azure ExpressRoute ゲートウェイには、少なくとも 2 つの ExpressRoute 接続があります。 オンプレミスを接続元とする回線と、HANA L インスタンスを接続元とする回線です。 このため、ExpressRoute ゲートウェイには、異なる MSEE を接続元とする回線を 2 つ追加できる余裕だけが残っています。 この制限は、ExpressRoute Fast Path の使用とは無関係です。 接続されているすべての回線は、ExpressRoute ゲートウェイの受信データの最大帯域幅を共有します。

HANA L インスタンス スタンプのリビジョン 3 では、VM と HANA L インスタンス ユニット間で発生するネットワーク待機時間は、VM 間のネットワーク ラウンド トリップの標準的な待機時間よりも長くなる可能性があります。 Azure リージョンによっては、測定値が、次のノートで平均以下に分類されているラウンドトリップの待ち時間 (0.7 ms) を超える可能性があります: 「[SAP Note #1100926 - FAQ: Network performance (SAP ノート #1100926 - FAQ: ネットワーク パフォーマンス)](https://launchpad.support.sap.com/#/notes/1100926/E)」。 Azure VM と HANA L インスタンス ユニットの間のネットワーク ラウンド トリップ待機時間を測定するツールと Azure リージョンによっては、測定される待機時間が最大約 2 ミリ秒になる可能性があります。 しかし、お客様は SAP HANA ベースの実稼働 SAP アプリケーションを SAP HANA L インスタンスに問題なくデプロイしています。 Azure HANA L インスタンスでビジネス プロセスを十分にテストしてください。 ExpressRoute Fast Path と呼ばれる新しい機能では、Azure の HANA L インスタンスとアプリケーション レイヤー VM の間のネットワーク待ち時間を大幅に削減できます (下記参照)。 

HANA L インスタンス スタンプのリビジョン 4 では、HANA L インスタンス スタンプに近接してデプロイされる Azure VM 間のネットワーク待ち時間は、Azure ExpressRoute Fast Path が構成されている場合、「[SAP Note #1100926 - FAQ:Network performance](https://launchpad.support.sap.com/#/notes/1100926/E)」に記載されている平均または平均を上回る分類に該当します (下記参照)。 リビジョン 4 の HANA L インスタンス ユニットに近接して Azure VM をデプロイするには、[Azure 近接通信配置グループ](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)を利用する必要があります。 近接通信配置グループを使用して、リビジョン 4 でホストされている HANA L インスタンス ユニットと同じ Azure データセンターに SAP アプリケーション レイヤーを配置する方法については、[SAP アプリケーションで最適なネットワーク待ち時間を実現する Azure 近接通信配置グループ](sap-proximity-placement-scenarios.md)に関する記事で説明しています。

VM と HANA L インスタンスの間に決定論的なネットワーク待ち時間を実現するには、ExpressRoute ゲートウェイ SKU の選択が不可欠となります。 オンプレミスと VM 間のトラフィック パターンとは異なり、VM と HANA L インスタンス間のトラフィック パターンでは、送信される要求やデータ量は小さくても、大きなバーストが発生する可能性があります。 このようなバーストを適切に処理するために、UltraPerformance ゲートウェイ SKU を使用することを強くお勧めします。 Type II クラスの HANA L インスタンス SKU の場合、ExpressRoute ゲートウェイとして UltraPerformance ゲートウェイ SKU を使用することが必須となります。

> [!IMPORTANT] 
> SAP アプリケーション層とデータベース層の間の全体的なネットワーク トラフィックを考慮し、SAP HANA on Azure (L インスタンス) への接続では、仮想ネットワーク用の HighPerformance または UltraPerformance ゲートウェイ SKU だけがサポートされます。 HANA L インスタンスの Type II SKU の場合、ExpressRoute ゲートウェイとしてサポートされるのは UltraPerformance ゲートウェイ SKU だけです。 ExpressRoute Fast Path を使用する場合は、例外が適用されます (下記参照)。

### <a name="expressroute-fast-path"></a>ExpressRoute Fast Path
待ち時間を短縮するために、ExpressRoute Fast Path が、2019 年 5 月に導入およびリリースされました。これは、SAP アプリケーション VM をホストする Azure 仮想ネットワークへの HANA L インスタンスの接続に関する機能です。 これまでにロールアウトされたソリューションとの大きな違いは、VM と HANA L インスタンスの間のデータ フローが ExpressRoute ゲートウェイを通じてルーティングされなくなったことです。 代わりに、Azure 仮想ネットワークのサブネットに割り当てられている VM が、専用のエンタープライズ エッジ ルーターと直接通信しています。 

> [!IMPORTANT] 
> ExpressRoute Fast Path 機能では、SAP アプリケーション VM が稼働しているサブネットが、HANA L インスタンスに接続されているのと同じ Azure 仮想ネットワーク内にあることが必要です。 HANA L インスタンス ユニットに直接接続されている Azure 仮想ネットワークにピアリングされている Azure 仮想ネットワーク内の VM には、ExpressRoute Fast Path のメリットはありません。 その結果、ExpressRoute 回線がハブ仮想ネットワークに接続し、SAP アプリケーション レイヤーを含む仮想ネットワーク (スポーク) がピアリングされている典型的なハブ アンド スポーク仮想ネットワーク設計では、ExpressRoute Fast Path による最適化は機能しません。 さらに、ExpressRoute Fast Path では現在、ユーザー定義ルーティング規則 (UDR) がサポートされていません。 詳細については、「[ExpressRoute 仮想ネットワーク ゲートウェイと FastPath](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways)」を参照してください。 


ExpressRoute Fast Path を構成する方法の詳細については、「[HANA Large Instances に仮想ネットワークを接続する](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-connect-vnet-express-route)」ドキュメントを参照してください。    

> [!NOTE]
> ExpressRoute Fast Path を機能させるには、UltraPerformance ExpressRoute ゲートウェイが必要です。


## <a name="single-sap-system"></a>単一の SAP システム

上記のオンプレミスのインフラストラクチャは ExpressRoute 経由で Azure に接続されます。 ExpressRoute 回線は、Microsoft エンタープライズ エッジ ルーター (MSEE) に接続します。 詳細については、[ExpressRoute の技術概要](../../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関する記事をご覧ください。 ルートが確立されると、そのルートは Azure バックボーンに接続されます。

> [!NOTE] 
> Azure で SAP ランドスケープを実行するには、SAP ランドスケープの Azure リージョンに最も近いエンタープライズ エッジ ルーターに接続します。 Azure IaaS 内の VM と HANA L インスタンス スタンプの間のネットワーク待ち時間を最小限に抑えるために、HANA L インスタンス スタンプは専用のエンタープライズ エッジ ルーター デバイスを介して接続されます。

SAP アプリケーション インスタンスをホストする VM の ExpressRoute ゲートウェイは、オンプレミスに接続する 1 つの ExpressRoute 回線に接続されます。 同じ仮想ネットワークが、L インスタンス スタンプへの接続専用の別のエンタープライズ エッジ ルーターに接続されます。 ExpressRoute Fast Path を使用すると、HANA L インスタンスから SAP アプリケーション レイヤー VM へのデータ フローが ExpressRoute ゲートウェイを通じてルーティングされなくなるため、ネットワークのラウンド トリップ待ち時間が短縮されます。

このシステムは、単一の SAP システムのわかりやすい例です。 SAP アプリケーション層は Azure でホストされます。 SAP HANA データベースは SAP HANA on Azure (L インスタンス) で実行されます。 ExpressRoute ゲートウェイの帯域幅 (2 Gbps または 10 Gbps のスループット) がボトルネックにならないことが前提となります。

## <a name="multiple-sap-systems-or-large-sap-systems"></a>複数の SAP システムまたは大規模な SAP システム

SAP HANA on Azure (L インスタンス) に接続するために複数の SAP システムまたは大規模な SAP システムをデプロイする場合、ExpressRoute ゲートウェイのスループットがボトルネックになることがあります。 または、運用システムと非運用システムを別々の Azure 仮想ネットワークに分離したい場合があります。 このような場合は、アプリケーション層を複数の仮想ネットワークに分割します。 次のような場合には、HANA L インスタンスに接続する特別な仮想ネットワークを作成することもできます。

- HANA L インスタンス内の HANA インスタンスから、NFS 共有をホストする Azure の VM に直接バックアップを実行する。
- HANA L インスタンス ユニットから、バックアップ ファイルなど大きなファイルを Azure 内の管理されたディスク領域にコピーする。

HANA L インスタンスと Azure の間で大量のデータを転送するためのストレージを管理する VM をホストするには、別の仮想ネットワークを使用します。 この配置では、HANA L インスタンスから Azure に大きなファイルやデータを転送することによる影響が、SAP アプリケーション レイヤーを運用する VM にサービスを提供する ExpressRoute ゲートウェイに及ぶことはありません。 

スケーラブルなネットワーク アーキテクチャでは次のように対応します。

- 1 つの大規模な SAP アプリケーション層に対して複数の仮想ネットワークを活用します。
- 同じ仮想ネットワークの個々のサブネットに SAP システムの組み合わせを配置するのではなく、デプロイされた SAP システムごとに個別の仮想ネットワークをデプロイします。

  SAP HANA on Azure (L インスタンス) 用のスケーラブルなネットワーク アーキテクチャを次に示します。

![複数の仮想ネットワークにわたる SAP アプリケーション層のデプロイ](./media/hana-overview-architecture/image4-networking-architecture.png)

さまざまな SAP システムの VM をホストしているさまざまな仮想ネットワークの間で適用する規則や制限によっては、それらの仮想ネットワークをピアリングする必要があります。 仮想ネットワーク ピアリングの詳細については、「[仮想ネットワーク ピアリング](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)」をご覧ください。


## <a name="routing-in-azure"></a>Azure でのルーティング

既定のデプロイでは、SAP HANA on Azure (L インスタンス) に関して、ネットワーク ルーティングについての 3 つの考慮事項が重要です。

* SAP HANA on Azure (L インスタンス) には、Azure VM および専用の ExpressRoute 接続経由でのみアクセスできます。オンプレミスから直接アクセスすることはできません。 オンプレミスから HANA L インスタンス ユニットへの直接アクセスは、Microsoft から提供されるため、すぐには実現できません。 この一時的なルーティングの制限は、SAP HANA L インスタンスに使用されている現在の Azure ネットワーク アーキテクチャに起因します。 直接アクセスを必要とする管理クライアントやアプリケーション (オンプレミスで実行されている SAP Solution Manager など) は、SAP HANA データベースに接続できません。 例外については、「HANA L インスタンスへの直接ルーティング」のセクションを確認してください。

* ディザスター リカバリーのために 2 つの異なる Azure リージョンにデプロイされている HANA L インスタンス ユニットがある場合、以前は同じ一時的なルーティングの制限が適用されました。 つまり、一方のリージョン (例: 米国西部) の HANA L インスタンス ユニットの IP アドレスが、もう一方のリージョン (例: 米国東部) でデプロイされている HANA L インスタンス ユニットにルーティングされることはありませんでした。 この制限は、リージョン間の Azure ネットワーク ピアリング、または HANA L インスタンス ユニットを仮想ネットワークに接続する ExpressRoute 回線の交差接続の使用とは無関係でした。 図については、「複数のリージョンでの HANA L インスタンス ユニットの使用」の図を参照してください。 デプロイ済みのアーキテクチャにはこのような制限があったため、HANA システム レプリケーションをディザスター リカバリー機能としてすぐに使用することはできませんでした。 最近の変更については、「複数のリージョンでの HANA L インスタンス ユニットの使用」セクションを参照してください。 

* SAP HANA on Azure (L インスタンス) ユニットには、お客様が HANA L インスタンスのデプロイを要求したときに提出したサーバー IP プールのアドレス範囲から IP アドレスが割り当てられます。 詳細については、「[Azure での SAP HANA on Azure (L インスタンス) のインフラストラクチャと接続](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」をご覧ください。 この IP アドレスには、Azure 仮想ネットワークを HANA L インスタンスに接続する回線と Azure サブスクリプションを使用してアクセスできます。 サーバー IP プールのアドレス範囲から割り当てられる IP アドレスは、ハードウェア ユニットに直接割り当てられます。 このソリューションの最初のデプロイでこの割り当てが行われるため、NAT を使用した割り当ては*行われません*。 

### <a name="direct-routing-to-hana-large-instances"></a>HANA L インスタンスへの直接ルーティング
既定では、HANA L インスタンス ユニットとオンプレミスの間、または 2 つの異なるリージョンにデプロイされた HANA L インスタンス ルーティング間の推移的なルーティングが機能しません。 そのような推移的なルーティングを可能にするためのいくつかの可能性があります。

- データをルーティングするリバース プロキシ。 たとえば、仮想ファイアウォール/トラフィック ルーティング ソリューションとして HANA L インスタンスやオンプレミスに接続する Azure 仮想ネットワークにデプロイされる F5 BIG-IP や NGINX (および Traffic Manager) などです。
- Linux VM で [IPTables ルール](http://www.linuxhomenetworking.com/wiki/index.php/Quick_HOWTO_%3a_Ch14_%3a_Linux_Firewalls_Using_iptables#.Wkv6tI3rtaQ)を使用して、オンプレミスの場所と HANA L インスタンス ユニット間、または別のリージョンにある HANA L インスタンス ユニット間のルーティングを有効にします。 IPTables を実行している VM は、HANA L インスタンスおよびオンプレミスに接続している Azure 仮想ネットワークにデプロイする必要があります。 VM は、VM のネットワーク スループットが予想ネットワーク トラフィックに対して十分であるように、サイズを設定する必要があります。 VM のネットワーク帯域幅の詳細については、「[Azure の Linux 仮想マシンのサイズ](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json)」という記事を参照してください。
- [Azure Firewall](https://azure.microsoft.com/services/azure-firewall/) は、オンプレミスと HANA L インスタンス ユニットの間の直接のトラフィックを可能にするもう 1 つのソリューションです。 

これらのソリューションのすべてのトラフィックは Azure 仮想ネットワークを通じてルーティングされるため、使用されるソフト アプライアンスまたは Azure ネットワーク セキュリティ グループによってトラフィックがさらに制限される可能性があります。つまり、オンプレミスの特定の IP アドレスまたは IP アドレス範囲が、HANA L インスタンスへのアクセスをブロックされたり、明示的に許可されたりする可能性があります。 

> [!NOTE]  
> サード パーティのネットワーク アプライアンスまたは IPTables を使用するカスタム ソリューションの実装とサポートは、Microsoft では提供されていないことに注意してください。 使用するコンポーネントのベンダーまたはインテグレーターがサポートを提供する必要があります。 

#### <a name="express-route-global-reach"></a>Express Route Global Reach
Microsoft は、[ExpressRoute Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) という新しい機能を導入しました。 Global Reach は、2 つのシナリオで HANA L インスタンスに使用できます。

- 異なるリージョンにデプロイされている HANA L インスタンス ユニットへのオンプレミスからの直接アクセスを可能にします
- 異なるリージョンにデプロイされている HANA L インスタンス ユニット間の直接通信を可能にします


##### <a name="direct-access-from-on-premise"></a>オンプレミスからの直接アクセス
Global Reach が提供されている Azure リージョンでは、HANA L インスタンス ユニットにも接続する Azure 仮想ネットワークにオンプレミス ネットワークを接続する ExpressRoute 回線に対する Global Reach 機能の有効化を要求できます。 ExpressRoute 回線のオンプレミス側には、コスト面で若干の影響があります。 価格については、[Global Reach アドオン](https://azure.microsoft.com/pricing/details/expressroute/)の価格を参照してください。 HANA L インスタンス ユニットを Azure に接続する回線には、追加のコストは発生しません。 

> [!IMPORTANT]  
> HANA L インスタンス ユニットとオンプレミス資産の間の直接アクセスを可能にするために Global Reach を使用する場合、ネットワーク データと制御フローは **Azure 仮想ネットワークを通じてルーティングされず**、Microsoft のエンタープライズ エクスチェンジ ルーター間で直接ルーティングされます。 その結果、Azure 仮想ネットワークにデプロイしたすべての NSG または ASG の規則、すべての種類のファイアウォール、NVA、またはプロキシは、まったく関与しません。 **ExpressRoute Global Reach を使用してオンプレミスから HANA L インスタンス ユニットへの直接アクセスを有効にする場合、HANA L インスタンス ユニットへのアクセスに対する制限および許可は、オンプレミス側のファイアウォールで定義する必要があります** 

##### <a name="connecting-hana-large-instances-in-different-azure-regions"></a>異なる Azure リージョン内の HANA L インスタンスの接続
同様に、ExpressRoute Global Reach はオンプレミスから HANA L インスタンス ユニットへの接続に使用できるため、2 つの異なるリージョンにデプロイされている 2 つの HANA L インスタンス テナントの接続にも使用できます。 分離の機能を果たしているのは、HANA L インスタンス テナントが両方のリージョンの Azure への接続に使用している ExpressRoute 回線です。 2 つの異なるリージョンにデプロイされた 2 つの HANA L インスタンス テナントを接続するための追加の料金はかかりません。 

> [!IMPORTANT]  
> 異なる HANA L インスタンス テナント間のネットワーク トラフィックのデータ フローおよび制御フローは、Azure ネットワークを通じてルーティングされません。 そのため、Azure の機能や NVA を使用して、2 つの HANA L インスタンス テナント間の通信制限を強制することはできません。 

ExpressRoute Global Reach を有効にする方法の詳細については、「[HANA Large Instances に仮想ネットワークを接続する](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-connect-vnet-express-route)」ドキュメントを参照してください。


## <a name="internet-connectivity-of-hana-large-instance"></a>HANA L インスタンスのインターネット接続
HANA L インスタンスは、インターネットに直接接続することは*できません*。 たとえば、この制限によって、OS イメージを OS ベンダーに直接登録することができない可能性があります。 ローカルの SUSE Linux Enterprise Server Subscription Management Tool サーバーや Red Hat Enterprise Linux Subscription Manager を使用することが必要になる場合があります。

## <a name="data-encryption-between-vms-and-hana-large-instance"></a>VM と HANA L インスタンス間でのデータの暗号化
HANA L インスタンスと VM 間で転送されるデータは暗号化されません。 ただし、HANA DBMS 側と JDBC/ODBC ベースのアプリケーションとの間でのデータ交換のみを目的としている場合は、トラフィックの暗号化を有効にすることができます。 詳細については、[SAP から提供されているこちらのドキュメント](http://help-legacy.sap.com/saphelp_hanaplatform/helpdata/en/db/d3d887bb571014bf05ca887f897b99/content.htm?frameset=/en/dd/a2ae94bb571014a48fc3b22f8e919e/frameset.htm&current_toc=/en/de/ec02ebbb57101483bdf3194c301d2e/plain.htm&node_id=20&show_children=false)をご覧ください。

## <a name="use-hana-large-instance-units-in-multiple-regions"></a>複数のリージョンでの HANA L インスタンス ユニットの使用

ディザスター リカバリーの設定を実現するには、SHANA L インスタンス ユニットを複数の Azure リージョンに配置する必要があります。 既定では、Azure [グローバル Vnet ピアリング] を使用しても、2 つの異なるリージョン内の HANA L インスタンス テナント間の推移的なルーティングが機能しません。 ただし、Global Reach は、2 つの異なるリージョンにプロビジョニングした HANA L インスタンス ユニット間の通信パスを開きます。 ExpressRoute Global Reach のこの使用シナリオにより、次のことが可能になります。

 - 追加のプロキシやファイアウォールを使用しない HANA システム レプリケーション
 - システム コピーまたはシステム更新を行うための、2 つの異なるリージョンにある HANA L インスタンス ユニット間でのバックアップのコピー


![異なる Azure リージョン内の Azure L インスタンス スタンプに接続された仮想ネットワーク](./media/hana-overview-architecture/image8-multiple-regions.png)

この図は、2 つのリージョン内の異なる仮想ネットワークが、2 つの Azure リージョン内の SAP HANA on Azure (L インスタンス) への接続に使用する 2 つの異なる ExpressRoute 回線 (灰色の線) に接続されるしくみを示しています。 この 2 つの交差接続の理由は、両側の MSEE を機能停止から保護することです。 2 つの Azure リージョン内の 2 つの仮想ネットワーク間の通信フローは、2 つの異なるリージョン内の 2 つの仮想ネットワークの[グローバル ピアリング](https://blogs.msdn.microsoft.com/azureedu/2018/04/24/how-to-setup-global-vnet-peering-in-azure/) (青い点線) で処理されることになっています。 赤い太線は、ExpressRoute Global Reach 接続を表しています。これにより、2 つの異なるリージョンにあるテナントの HANA L インスタンス ユニットが互いに通信できます。 

> [!IMPORTANT] 
> 複数の ExpressRoute 回線を使用する場合は、トラフィックを適切にルーティングできるように、AS パス プリペンドとローカル プリファレンス BGP の設定を使用してください。

**次のステップ**
- 「[SAP HANA (Large Instances) storage architecture](hana-storage-architecture.md)」 (SAP HANA (L インスタンス) のストレージ アーキテクチャ) を参照してください。