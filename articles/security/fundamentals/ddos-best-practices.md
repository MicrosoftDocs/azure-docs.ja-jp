---
title: Azure DDoS Protection を使用する回復性の高いソリューションの設計
description: ログ データを使用して、アプリケーションに関する深い洞察を得る方法について説明します。
services: security
author: terrylanfear
manager: RKarlin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/18/2018
ms.author: terrylan
ms.openlocfilehash: e298cb0d1a2c510a096f8ead03f8af7e39c206a8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96498933"
---
# <a name="azure-ddos-protection---designing-resilient-solutions"></a>Azure DDoS Protection - 回復性の高いソリューションの設計

この記事は、IT の意思決定者およびセキュリティ担当者を対象に書かれています。 Azure、ネットワーク、およびセキュリティに関する知識があることを前提としています。
DDoS は、アプリケーションのリソースを枯渇させようとする攻撃の種類です。 目的は、正当な要求を処理するアプリケーションの可用性と能力に影響を与えることです。 攻撃は、いっそう高度なものになり、規模と影響も大きくなっています。 DDoS 攻撃は、インターネット経由で一般に到達可能なすべてのエンドポイントで実行できます。 分散型サービス拒否 (DDoS) に対する回復性を設計するときは、さまざまな障害モードに対応した計画と設計が必要です。 Azure では、DDoS 攻撃に対する保護が継続的に提供されています。 この保護は、追加コストなしに、既定で Azure プラットフォームに統合されています。

プラットフォームに備わっている中核の DDoS 保護機能に加え、[Azure DDoS Protection Standard](https://azure.microsoft.com/services/ddos-protection/) を使用すれば、 ネットワーク攻撃に対する高度な DDoS 対策機能が提供されます。 この機能は、お客様固有の Azure リソースを保護するために自動的に調整されます。 保護は、新しい仮想ネットワークの作成時に簡単に有効にできます。 作成の後で行うこともでき、アプリケーションまたはリソースを変更する必要はありません。

![お客様と仮想ネットワークを攻撃者から保護するうえでの Azure DDoS Protection の役割](./media/ddos-best-practices/image1.png)


## <a name="fundamental-best-practices"></a>基本的なベスト プラクティス

次のセクションでは、Azure で DDoS に対する回復力のあるサービスを構築するための規範的なガイダンスを提供します。

### <a name="design-for-security"></a>セキュリティ向けの設計

設計と実装から、デプロイと運用まで、アプリケーションのライフ サイクル全体を通して、セキュリティを優先する必要があります。 アプリケーションには、比較的少量の要求によって膨大なリソースを使用し、結果としてサービスを停止させることができるような、バグが含まれる可能性があります。 

Microsoft Azure で実行されるサービスを保護するには、お客様は、自分のアプリケーションのアーキテクチャをよく理解し、[ソフトウェア品質の 5 つの重要な要素](/azure/architecture/guide/pillars)に注目する必要があります。
お客様は、標準的なトラフィックの量、アプリケーションと他のアプリケーションの間の接続モデル、パブリック インターネットに公開されるサービス エンドポイントについて知っておく必要があります。

アプリケーション自体を対象とするサービス拒否攻撃に対処するのに十分な回復力をアプリケーションに持たせることが、最も重要です。 Azure プラットフォームには、[セキュリティ開発ライフサイクル (Security Development Lifecycle: SDL)](https://www.microsoft.com/sdl/default.aspx) からセキュリティとプライバシーが組み込まれるようになっています。 SDL は、すべての開発段階でセキュリティに対処し、Azure がいっそう安全になるように継続的に更新されることを保証します。

### <a name="design-for-scalability"></a>スケーラビリティのための設計

スケーラビリティとは、負荷の増大に対するシステムの対応能力のことです。 [水平方向にスケーリングする](/azure/architecture/guide/design-principles/scale-out)ようにアプリケーションを設計し、増加した負荷の需要を満たす (特に、DDoS 攻撃の場合)。 アプリケーションがサービスの 1 つのインスタンスに依存する場合は、単一障害点が発生します。 複数のインスタンスをプロビジョニングすると、システムの回復力と拡張性が高まります。

[Azure App Service](../../app-service/overview.md) の場合は、複数のインスタンスを提供する [App Service プラン](../../app-service/overview-hosting-plans.md)を選択してください。 Azure Cloud Services の場合は、[複数インスタンス](../../cloud-services/cloud-services-choose-me.md)を使用するように各ロールを構成してください。 [Azure Virtual Machines](../../virtual-machines/index.yml) の場合は、仮想マシン (VM) アーキテクチャに 1 つ以上の VM が含まれていることと、[可用性セット](../../virtual-machines/windows/tutorial-availability-sets.md)に各 VM が含まれていることを確認してください。 自動スケーリング機能には[仮想マシン スケール セット](../../virtual-machine-scale-sets/overview.md)を使うことをお勧めします。

### <a name="defense-in-depth"></a>多層防御

多層防御の基になっている考え方は、多様な防御戦略によってリスクを管理することです。 アプリケーションのセキュリティ防御を多層化すると、攻撃が成功する可能性が減少します。 お客様には、Azure プラットフォームの組み込み機能を使って、アプリケーションのセキュリティ保護設計を実装することをお勧めします。

たとえば、攻撃のリスクは、アプリケーションの規模 (*攻撃対象領域*) と共に大きくなります。 公開されている IP アドレス空間を承認リストを使用して閉鎖し、ロード バランサー ([Azure Load Balancer](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) と [Azure Application Gateway](../../application-gateway/application-gateway-create-probe-portal.md)) を用いて不要にリスニングしているポートを閉じることで、攻撃対象領域を減らすことができます。 [ネットワーク セキュリティ グループ (NSG)](../../virtual-network/network-security-groups-overview.md) も、攻撃対象領域を軽減する 1 つの手段です。
[サービス タグ](../../virtual-network/network-security-groups-overview.md#service-tags)および[アプリケーション セキュリティ グループ](../../virtual-network/network-security-groups-overview.md#application-security-groups)を使用して、セキュリティ規則作成の複雑さを軽減し、アプリケーションの構造の自然な延長としてネットワーク セキュリティを構成することができます。

お客様は、可能な限り、[仮想ネットワーク](../../virtual-network/virtual-networks-overview.md)に Azure サービスをデプロイする必要があります。 このプラクティスを使用すると、サービス リソースはプライベート IP アドレスを通して通信できます。 仮想ネットワークからの Azure サービス トラフィックは、パブリック IP アドレスを発信元 IP アドレスとして既定で使用します。 [サービス エンドポイント](../../virtual-network/virtual-network-service-endpoints-overview.md)を使用すると、サービス トラフィックは、仮想ネットワークから Azure サービスにアクセスするときに、仮想ネットワークのプライベート アドレスを発信元 IP アドレスとして使用するように切り替わります。

Azure 内のリソースと供に、お客様のオンプレミスのリソースが攻撃されることがよくあります。 オンプレミスの環境を Azure に接続している場合は、オンプレミスのリソースのパブリック インターネットへの露出を最小限にすることをお勧めします。 よく知られたパブリック エンティティを Azure にデプロイすることで、Azure のスケールと高度な DDoS 保護機能を使用できます。 これらのパブリックにアクセスできるエンティティは、DDoS 攻撃の対象になることがよくあるため、Azure 内に配置すると、オンプレミスのリソースへの影響が減少します。

## <a name="azure-offerings-for-ddos-protection"></a>DDoS 保護用の Azure プラン

Azure には、ネットワーク攻撃からの保護を提供する 2 つの DDoS サービス オファリング - DDoS Protection Basic と DDoS Protection Standard - があります (レイヤー 3 および 4)。 

### <a name="ddos-protection-basic"></a>DDoS Protection Basic

基本保護は、追加コストなしに、既定で Azure に統合されます。 グローバルにデプロイされる Azure ネットワークのスケールと容量が、常時有効なトラフィック監視とリアルタイムのリスク軽減によって、一般的なネットワーク層攻撃からの保護を提供します。 DDoS Protection Basic には、ユーザーの構成またはアプリケーションの変更は不要です。 DDoS Protection Basic では、Azure DNS などの PaaS サービスを含むすべての Azure サービスが保護されます。

![Azure ネットワークを表したマップ (表示されているテキストは、「グローバルな DDoS 軽減策」と「優れた DDoS 軽減キャパシティ」)](./media/ddos-best-practices/image3.png)

Azure の基本的な DDoS 保護は、ソフトウェア コンポーネントとハードウェア コンポーネントの両方で構成されます。 ソフトウェアのコントロール プレーンは、攻撃トラフィックを分析して除去するハードウェア アプライアンスにトラフィックを送る必要があるタイミング、場所、およびトラフィックの種類を決定します。 コントロール プレーンによるこの決定は、インフラストラクチャ全体の DDoS 保護 *ポリシー* に基づいて行われます。 このポリシーは静的に設定され、すべての Azure ユーザーに汎用的に適用されます。

たとえば、DDoS保護ポリシーでは、どのくらいのトラフィック量で保護機能が *トリガーされる* のかが指定されます。 (つまり、テナントのトラフィックはスクラブ アプライアンスを通じてルーティングされる必要があります。)ポリシーではまた、スクラブ アプライアンスで攻撃をどのように *軽減* するのかも指定されます。

Azure DDoS Protection Basic サービスの対象は、インフラストラクチャの保護と、Azure プラットフォームの保護です。 このサービスは、マルチテナント環境の複数の顧客に影響を与える可能性があるほど非常に大きくレートを超えたトラフィックを軽減します。 警告機能や、顧客ごとのポリシーのカスタマイズは提供されません。

### <a name="ddos-protection-standard"></a>DDoS Protection Standard

Standard の保護サービスでは、拡張的な DDoS 対策機能が提供されます。 この機能は、仮想ネットワーク内にあるお客様固有の Azure リソースを保護するために、自動的に調整されます。 保護は新規または既存の仮想ネットワークで簡単に有効にでき、アプリケーションやリソースの変更は必要ありません。 ログ、アラート、テレメトリなど、基本サービスにはない利点がいくつかあります。 以降のセクションでは、Azure DDoS Protection Standard サービスの主な機能について説明します。

#### <a name="adaptive-real-time-tuning"></a>アダプティブ リアルタイム チューニング

Azure DDoS Protection Basic サービスは、お客様のシステムの保護と、他のお客様への影響を防ぐのに役立ちます。 たとえば、インフラストラクチャ全体にわたる DDoS Protection ポリシーの "*トリガー レート*" より小さい正規の着信トラフィックの一般的な量に対してサービスがプロビジョニングされている場合、そのお客様のリソースに対する DDoS 攻撃は認識されない可能性があります。 さらに一般的には、最近の攻撃 (たとえば、複数ベクター DDoS) の複雑さと、テナントのアプリケーション固有の動作により、お客様ごとに保護ポリシーをカスタマイズする必要があります。 このカスタマイズは、次の 2 つのインサイト手法を通じて実行されます。

- お客様ごと (IP ごと) に、レイヤー 3 および 4 のトラフィック パターンを自動学習する。

- 誤検出を最小限に減らす (大量のトラフィックを吸収できる Azure のスケールを活用したもの)。

![DDoS Protection Standard のしくみを示した図 (「ポリシーの世代」が丸で囲まれています)](./media/ddos-best-practices/image5.png)

#### <a name="ddos-protection-telemetry-monitoring-and-alerting"></a>DDoS 保護のテレメトリ、監視、アラート

DDoS Protection Standard では、DDoS 攻撃の発生時に、[Azure Monitor](../../azure-monitor/overview.md) によって豊富なテレメトリが公開されます。 お客様は、DDoS Protection で使用される任意の Azure Monitor メトリックについて、アラートを構成することができます。 また、ログを Splunk (Azure Event Hubs)、Azure Monitor ログ、Azure Storage と統合し、Azure Monitor 診断インターフェースを介して高度な分析を行うこともできます。

##### <a name="ddos-mitigation-policies"></a>DDoS 軽減ポリシー

Azure Portal で **[監視]**  >  **[メトリック]** を選択します。 **[メトリック]** ウィンドウで、リソース グループを選択し、**パブリック IP アドレス** のリソースの種類を選択して、Azure のパブリック IP アドレスを選択します。 DDoS のメトリックが、**使用可能なメトリック** のウィンドウに表示されます。

DDoS Protection Standard は、DDoS が有効になっている仮想ネットワーク内で、保護されたリソースのパブリック IP ごとに、3 つの自動調整された軽減ポリシー (TCP SYN、TCP、UDP) を適用します。 ポリシーのしきい値は、メトリック **[Inbound packets to trigger DDoS mitigation]\(DDoS 軽減をトリガーする着信パケット数\)** を選択することで確認できます。

![使用可能なメトリックとメトリックのグラフ](./media/ddos-best-practices/image7.png)

ポリシーのしきい値は、機械学習ベースのネットワーク トラフィック プロファイルを使用して自動的に構成されます。 DDoS の軽減は、ポリシーのしきい値を超過した場合にのみ、攻撃を受けている IP アドレスに対して行われます。

##### <a name="metric-for-an-ip-address-under-ddos-attack"></a>DDoS 攻撃を受けた IP アドレスの メトリック

パブリック IP アドレスが攻撃を受けると、メトリック **[Under DDoS attack or not]\(DDoS 攻撃中かどうか\)** の値が 1 に切り替わり、攻撃トラフィックに対する軽減措置が実行されます。

![[Under DDoS attack or not]\(DDoS 攻撃中かどうか\) メトリックとグラフ](./media/ddos-best-practices/image8.png)

このメトリックには、アラートを構成することをお勧めします。 そうすると、パブリック IP アドレスでアクティブな DDoS 軽減が実行されたときに通知が送られます。

詳しくは、「[Azure Portal を使用した Azure DDoS Protection Standard の管理](../../ddos-protection/manage-ddos-protection.md)」をご覧ください。

#### <a name="web-application-firewall-for-resource-attacks"></a>リソース攻撃用の Web アプリケーション ファイアウォール

アプリケーション レイヤーでのリソース攻撃に限り、お客様は Web アプリケーション ファイアウォール (WAF) を構成して Web アプリケーションのセキュリティ保護を強化する必要があります。 WAF は、着信 Web トラフィックを検査して、SQL インジェクション、クロスサイト スクリプティング、DDoS、その他のレイヤー 7 攻撃をブロックします。 Azure は、一般的な脆弱性やその悪用から Web アプリケーションを一元的に保護する [Application Gateway の機能として WAF](../../web-application-firewall/ag/ag-overview.md) を提供します。 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?search=WAF&page=1) では Azure パートナーのその他の WAF プランを利用でき、ニーズにより適したものが見つかる可能性があります。

Web アプリケーション ファイアウォールを構成しても、帯域幅消費型攻撃や状態枯渇攻撃を受ける可能性があります。 WAF 仮想ネットワーク上で DDoS Protection Standard を有効にして、帯域幅消費型攻撃やプロトコル攻撃に対する保護を強化することを強くお勧めします。 詳しくは、「[DDoS Protection の参照アーキテクチャ](#ddos-protection-reference-architectures)」セクションをご覧ください。

### <a name="protection-planning"></a>保護の計画

計画と準備は、DDoS 攻撃中にシステムがどのように動作するかを理解するために重要です。 この取り組みには、インシデント管理対応計画の設計が含まれます。

DDoS Protection Standard を利用している場合は、インターネットに接続するエンドポイントの仮想ネットワークで同サービスが有効になっていることを確認する必要があります。 DDoS アラートを構成すると、インフラストラクチャに対する攻撃の可能性を常時監視するのに役立ちます。 

お使いのアプリケーションを独自に監視してください。 アプリケーションの通常の動作を把握し、 DDoS 攻撃時にアプリケーションが想定の動作をしなくなった場合の対応を準備しましょう。

#### <a name="testing-through-simulations"></a>シミュレーションを通じたテスト

定期的にシミュレーションを実施して、攻撃に対するサービスの応答方法の想定をテストすることをお勧めします。 テストでは、サービスまたはアプリケーションが期待どおりに機能し続けること、およびユーザー エクスペリエンスが中断しないことを確認します。 テクノロジとプロセスの両方の観点からギャップを明らかにし、それらを DDoS 対応戦略に組み込みます。 そのようなテストは、ステージング環境で実行するか、または運用環境への影響が最小限になるピーク時以外に実行することをお勧めします。

Microsoft は [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) と提携して、Azure のお客様がシミュレーションのために DDoS Protection を有効にしたパブリック エンドポイントに対してトラフィックを生成できるインターフェイスを構築しました。 [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) のシミュレーションは、次の目的に使用できます。

- Azure DDoS Protection によって Azure リソースが DDoS 攻撃からどのように保護されるのかを検証する。

- DDoS 攻撃時のインシデント レスポンス プロセスを最適化する。

- DDoS コンプライアンスを文書化する。

- ネットワーク セキュリティ チームのトレーニングを行う。

サイバーセキュリティには、防御機能の継続的なイノベーションが不可欠です。 Azure DDoS Protection Standard は、複雑さを増す DDoS 攻撃を軽減するための効果的なソリューションを提供する最先端のプランです。

## <a name="components-of-a-ddos-response-strategy"></a>DDoS 対応戦略のコンポーネント

Azure リソースを狙った DDoS 攻撃では通常、ユーザーの観点から必要とされる対応は最小限で済みます。 それでも、インシデント対応戦略の一環として DDoS 軽減を組み込むことで、ビジネス継続性への影響を最小限にできます。

### <a name="microsoft-threat-intelligence"></a>Microsoft の脅威インテリジェンス

Microsoft では、広範な脅威インテリジェンス ネットワークを構築しています。 このネットワークでは、Microsoft のオンライン サービスをサポートする広範なセキュリティ コミュニティ、Microsoft パートナー、およびインターネット セキュリティ コミュニティ内のリレーションシップの集合的知識が使用されます。 

Microsoft は、重要なインフラストラクチャ プロバイダーとして、脅威に関する警告を早期に受信します。 Microsoft では、自社のオンライン サービスとグローバルな顧客ベースから脅威インテリジェンスを収集し、 それらすべての脅威インテリジェンスを Azure DDoS Protection 製品に組み込んでいます。

また、Microsoft Digital Crimes Unit (DCU) では、ボットネットに対する攻撃的な戦略を実施しています。 ボットネットは、DDoS 攻撃のコマンドを発する一般的な制御元です。

### <a name="risk-evaluation-of-your-azure-resources"></a>Azure リソースのリスク評価

継続的に DDoS 攻撃のリスクの範囲を把握することが不可欠です。 次のことを定期的に確認するようにしてください。

- パブリックに使用可能な新しい Azure リソースのうち、保護が必要なものはあるか。

- サービスに単一障害点はあるか。 

- どのようにすれば、サービスを分離して攻撃の影響を制限しながら、有効な顧客がサービスを利用できるようにすることができるか。

- DDoS Protection Standard を有効にする必要があるのに有効になっていない仮想ネットワークがあるか。 

- サービスは複数のリージョンをまたがるフェールオーバーについてアクティブ/アクティブになっているか。

### <a name="customer-ddos-response-team"></a>お客様の DDoS 対応チーム

DDoS 対応チームを作成することは、攻撃に効果的かつ迅速に対応するために重要なステップです。 計画と実行の両方を監視する組織内の担当者を確認しましょう。 この DDoS 応答チームは、Azure DDoS Protection Standard サービスのことを完全に理解している必要があります。 チームが内部や外部の顧客 (Microsoft サポート チームを含む) と連携して攻撃を特定し、緩和できる体制にあることを確認してください。

DDoS 対応チームにおいては、サービスの可用性や継続性に関する計画の一部として、シミュレーション演習を必ず実施することをお勧めします。 またこれらの演習には、スケール テストを含めるようにしてください。

### <a name="alerts-during-an-attack"></a>攻撃時のアラート

Azure DDoS Protection Standard は、ユーザーの介入がなくても DDoS 攻撃を識別して軽減します。 保護されているパブリック IP に対してリスク軽減がアクティブになった場合に通知を受け取るには、メトリック **Under DDoS attack or not (DDoS 攻撃中かどうか)** に対して [アラートを構成する](../../ddos-protection/manage-ddos-protection.md)ことができます。 他の DDoS メトリックについてのアラートを作成し、攻撃の規模、ドロップされたトラフィック、およびその他の詳細情報を把握することもできます。

#### <a name="when-to-contact-microsoft-support"></a>どのようなときに Microsoft サポートに問い合わせるか

- DDoS 攻撃中に、保護されたリソースのパフォーマンスが著しく低下したり、リソースが使用できなくなったことを検出した場合。

- DDoS Protection サービスの動作が期待どおりではないと考えられる場合。 

  DDoS Protection サービスの軽減策は、メトリック値 **Policy to trigger DDoS mitigation (TCP/TCP SYN/UDP) (DDoS 軽減をトリガーするポリシー (TCP、TCP SYN/UDP))** が、保護されたパブリック IP リソースで受信されたトラフィックを下回っている場合にのみ起動されます。

- ネットワーク トラフィックが大幅に増大するバイラル イベントを計画している場合。

- リソースに対して DDoS 攻撃を行うという脅迫が攻撃者からあった場合。

- Azure DDoS Protection Standard の IP または IP 範囲の一覧表示を許可する必要がある場合。 一般的なシナリオは、トラフィックが外部クラウドの WAF から Azure にルーティングされる場合に IP の一覧表示を許可する場合です。 

ビジネスに重大な影響を及ぼす攻撃については、重大度 A の[サポート チケット](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)を作成してください。

### <a name="post-attack-steps"></a>攻撃後の手順

攻撃を受けた後で事後分析を行い、必要に応じて DDoS 対応戦略を調整するのは、常によい戦略です。 また、以下の点を考慮してください。

- スケーラブルなアーキテクチャがないため、サービスまたはユーザーのエクスペリエンスに中断が発生したか。

- どのアプリケーションまたはサービスが最も影響を受けたか。

- DDoS 対応戦略の効果はどの程度で、どうすれば改善できるか。

DDoS 攻撃を受けている可能性がある場合は、通常の Azure サポート チャネルを通じてエスカレートしてください。

## <a name="ddos-protection-reference-architectures"></a>DDoS Protection の参照アーキテクチャ

DDoS Protection Standard は、[仮想ネットワークにデプロイされるサービス用](../../virtual-network/virtual-network-for-azure-services.md)に設計されています。 他のサービスには、既定の DDoS Protection Basic が適用されます。 以下の参照アーキテクチャは、アーキテクチャ パターンでグループ化されたシナリオで調整されています。

### <a name="virtual-machine-windowslinux-workloads"></a>仮想マシン (Windows/Linux) のワークロード

#### <a name="application-running-on-load-balanced-vms"></a>負荷分散された VM 上で実行しているアプリケーション

この参照アーキテクチャは、可用性とスケーラビリティを向上させるために、ロード バランサーの背後にあるスケール セット内で複数の Windows VM を実行するための一連の実証済みの手法を示します。 このアーキテクチャは、Web サーバーなど、任意のステートレス ワークロードで使用できます。

![負荷分散された VM で実行されているアプリケーションの参照アーキテクチャの図](./media/ddos-best-practices/image9.png)

このアーキテクチャでは、ワークロードが複数の VM インスタンスにわたって分散されます。 単一のパブリック IP アドレスが存在し、ロード バランサーを通じてインターネット トラフィックが VM に分散されます。 パブリック IP が関連付けられた Azure (インターネット) ロード バランサーの仮想ネットワークでは、DDoS Protection Standard が有効になります。

ロード バランサーは、受信インターネット要求を各 VM インスタンスに分散します。 仮想マシン スケール セットにより、VM の数を手動でスケールインまたはスケールアウトしたり、定義済みの規則に基づいて自動的に設定したりできるようになります。 これは、リソースが DDoS 攻撃を受けている場合に重要です。 この参照アーキテクチャについて詳しくは、[こちらの記事](/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm)をご覧ください。

#### <a name="application-running-on-windows-n-tier"></a>Windows N 層で実行しているアプリケーション

N 層アーキテクチャを実装する方法は多数あります。 次の図は、典型的な 3 層の Web アプリケーションを示したものです。 このアーキテクチャは「[スケーラビリティと可用性のために負荷分散された VM を実行する](/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm)」の記事に基づいて作成されています。 Web 層とビジネス層では、負荷分散された VM が使用されます。

![Windows N 層で実行されているアプリケーションの参照アーキテクチャの図](./media/ddos-best-practices/image10.png)

このアーキテクチャでは、DDoS Protection Standard が仮想ネットワークで有効になっています。 仮想ネットワーク内のすべてのパブリック IP には、レイヤー 3/レイヤー 4 の DDoS 保護が適用されます。 レイヤー 7 を保護するには、WAF SKU の Application Gateway をデプロイしてください。 この参照アーキテクチャについて詳しくは、[こちらの記事](/azure/architecture/reference-architectures/virtual-machines-windows/n-tier)をご覧ください。

#### <a name="paas-web-application"></a>PaaS Web アプリケーション

この参照アーキテクチャでは、単一リージョンでの Azure App Service アプリケーションの実行を示します。 このアーキテクチャは、[Azure App Service](https://azure.microsoft.com/documentation/services/app-service/) と [Azure SQL Database](https://azure.microsoft.com/documentation/services/sql-database/) を使用する Web アプリケーションを対象とした一連の実証済みプラクティスを示しています。
フェールオーバー シナリオのためにスタンバイ リージョンを設定します。

![PaaS Web アプリケーションの参照アーキテクチャの図](./media/ddos-best-practices/image11.png)

Azure Traffic Manager は、着信要求をいずれかのリージョンの Application Gateway にルーティングします。 通常の運用中は、アクティブなリージョンの Application Gateway に要求をルーティングします。 そのリージョンが使用できなくなった場合、Traffic Manager はスタンバイ リージョンの Application Gateway にフェールオーバーします。

インターネットから Web アプリケーションに宛てられたすべてのトラフィックは、Traffic Manager によって [Application Gateway のパブリック IP アドレス](../../application-gateway/application-gateway-web-app-overview.md)にルーティングされます。 このシナリオでは、アプリ サービス (Web アプリ) 自体は外部に対して直接公開されておらず、Application Gateway によって保護されています。 

Application Gateway WAF SKU (禁止モード) を構成して、レイヤー 7 (HTTP/HTTPS/Web ソケット) の攻撃を防ぐことをお勧めします。 さらに、Web アプリは、ָ[Application Gateway の IP アドレスからのトラフィックのみを受け入れる](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/)ように構成されます。

この参照アーキテクチャについて詳しくは、[こちらの記事](/azure/architecture/reference-architectures/app-service-web-app/multi-region)をご覧ください。

### <a name="mitigation-for-non-web-paas-services"></a>Web PaaS 以外のサービスに対するリスク軽減

#### <a name="hdinsight-on-azure"></a>Azure 上の HDInsight

この参照アーキテクチャは、[Azure HDInsight クラスター](../../hdinsight/index.yml)に対する DDoS Protection Standard の構成を示したものです。 HDInsight クラスターが仮想ネットワークにリンクされていること、および DDoS Protection がその仮想ネットワークで有効になっていることを確認してください。

![[HDInsight] および [詳細設定] ウィンドウと、仮想ネットワークの設定](./media/ddos-best-practices/image12.png)

![DDoS Protection を有効にするための選択](./media/ddos-best-practices/image13.png)

このアーキテクチャでは、インターネットから HDInsight クラスター宛のトラフィックは、HDInsight ゲートウェイのロード バランサーに関連付けられているパブリック IP にルーティングされます。 ゲートウェイのロード バランサーは、トラフィックをヘッド ノードまたはワーカー ノードに直接送信します。 HDInsight の仮想ネットワークで DDoS Protection Standard が有効になっているので、仮想ネットワーク内のすべてのパブリック IP アドレスにはレイヤー 3/レイヤー 4 の DDoS Protection が適用されます。 この参照アーキテクチャは、N 層およびマルチリージョンの参照アーキテクチャと組み合わせることができます。

この参照アーキテクチャについて詳しくは、「[Azure Virtual Network を使用した Azure HDInsight の拡張](../../hdinsight/hdinsight-plan-virtual-network-deployment.md?toc=%2fazure%2fvirtual-network%2ftoc.json)」をご覧ください。


> [!NOTE]
> パブリック IP を使用する仮想ネットワーク内での PowerApps 用 Azure App Service 環境または API 管理は、どちらもネイティブにはサポートされていません。

## <a name="next-steps"></a>次のステップ

* [クラウドにおける共同責任](shared-responsibility.md)
* [Azure DDoS Protection 製品のページ](https://azure.microsoft.com/services/ddos-protection/)
* [Azure DDoS Protection のドキュメント](../../ddos-protection/ddos-protection-overview.md)