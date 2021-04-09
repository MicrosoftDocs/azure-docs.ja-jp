---
title: Azure Orbital について - プレビュー
description: Microsoft の新しいサービスとしての地上局のソリューション、Azure Orbital について説明します。
services: vnf-manager
author: wamota
ms.service: vnf-manager
ms.topic: overview
ms.date: 09/22/2020
ms.author: wamota
ms.openlocfilehash: 05f3b5b461e42c29a12f126928f69069b45007d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97797272"
---
# <a name="what-is-azure-orbital-preview"></a>Azure Orbital とは (プレビュー)

Azure Orbital は、フル マネージドなクラウドベースのサービスとしての地上局です。これを使用すると、宇宙船や人工衛星コンステレーションとの通信、データのダウンリンクやアップリンク、クラウドでのデータ処理、独自のシナリオでの Azure サービスとサービスの連結、およびユーザーの顧客のための製品を生み出すことが可能になります。 Azure Orbital を使用することで、地上局資産のデプロイとメンテナンスの責任を軽減し、ミッションと製品データに集中できます。 このシステムは、Azure グローバル インフラストラクチャと低待機時間のグローバル ファイバー ネットワーク上に構築されています。

[:::image type="content" source="./media/azure-orbital-overview/orbital-all-ignite-link.png" alt-text="Ignite の Azure Orbital 発表ビデオ":::](https://aka.ms/orbitalatignite)
[Azure YouTube チャンネルで、Ignite での Azure Orbital の発表をご覧ください](https://aka.ms/orbitalatignite)

Azure Orbital では、お客様が Orbital の地上局に加えてパートナーの地上局を利用できるようにすることや、統合されたクラウド モデムに加えてパートナーのクラウド モデムを利用できるようにするためのパートナー エコシステムを構築することに注力しています。 Azure Orbital は、ViaSat の Real-time Earth (RTE) や US Electrodynamics Inc. のような他の地上局またはテレポート プロバイダーに加えて、KSAT などの業界リーダーとの提携に焦点を当てており、最前線で利用可能な幅広いカバレッジを提供しています。 このパートナーシップは、SES や他の地上局/テレポート プロバイダー、ViaSat の Real-time Earth (RTE)、および US Electrodynamics Inc. のような人工衛星通信プロバイダーまで拡大され、LEO/MEO フリートへのグローバル アクセス、通信コンスタレーションでの Azure の直接アクセス、または LEO/MEO フリートへのグローバル アクセスを実現します。 Microsoft は RF 信号を仮想化し、Kratos や Amergint などのリーダー企業と提携して、このような企業のモデムを Marketplace に発行するため取り組んできました。 当社の目標は、Microsoft の豊富で拡張性があり、柔軟性の高い地上局サービス プラットフォームを使用して、お客様がより多くのことを達成し、システムを構築できるよう支援することです。

Azure Orbital は、地球観測やグローバル コミュニケーションなど、お客様の複数のユース ケースを実現します。 また、仮想化を使用して既存の地上局のデジタル変革を可能にするプラットフォームも提供します。 ユーザーはすべての Azure サービス、Azure グローバル インフラストラクチャ、Marketplace に直接アクセスし、サービスを通じて世界クラスのパートナー エコシステムにアクセスすることができます。

:::image type="content" source="./media/azure-orbital-overview/orbital-all-overview.png" alt-text="Azure Orbital の概要":::

**Azure Orbital ユーザー向けの価値提案は次のとおりです。**

* **グローバル展開** – Azure Orbital 地上局サービスには、パートナーの地上局も含まれています。 グローバル カバレッジは遅延なしで利用でき、お客様は、Microsoft が所有する地上局に加え、パートナーの地上局でも Orbital を使用して通信をスケジュールすることができます。

* **資本的支出を運用支出に変換する** – 地上局の配備と管理のタスクは Microsoft が引き受けるため、ユーザーは地上局への投資に必要な初期費用を、ミッションと資産の配備に集中的に使用することができます。 従量課金制の消費モデルとは、使用した時間に対してのみ課金されることを意味します。

* **ライセンス** – Microsoft のチームは、当社のサイトや規制機関への人工衛星のオンボードをお手伝いします。

* **運用効率とスケーラビリティ** – ユーザーは、地上局のメンテナンス、リース、構築、あるいは運用コストについて心配する必要はありません。 ビジネスで必要なときに、オンデマンドで人工衛星通信を迅速に拡張することができます。

* **Azure のネットワークとリージョンへの直接アクセス** – Microsoft では、データセンターの場所やネットワークのエッジに近い場所に独自の地上局を配置し、また、パートナーの地上局やネットワークと相互接続することで Azure リージョンへの近接性を提供しています。 データは、Azure のセキュリティで保護された低待機時間のグローバル ファイバー ネットワークによって、クラウドに即座に配信され、任意の場所に送信されます。

* **デジタル化された RF** – 最大 500 MHz の広帯域を含むアンテナからの完全にデジタル化された信号により、宇宙船から送られてくるデータを完全に制御し、セキュリティを確保することができます。 パートナーのソフトウェア モデムはシームレスに使用できるようにプラットフォームに統合されており、処理チェーンを完了するために使用するため、Marketplace でも利用できます。 お客様の仮想ネットワーク内の指定されたエンドポイントでデジタル化された RF の配信によってサポートされる、お客様自身のモデム (独自のミッションのニーズに対応するため) を持参することも想定しています。

* **Azure Cloud および Marketplace** – IoT、AI と ML、Cognitive Services、分析とストレージなどを含むがこれに限定されないすべての Azure ソリューションを活用して、データの処理と保管を行い、単一の環境でワークロードと連結します。

* **柔軟性** – スケジュール サービス、パートナー ネットワーク、デジタル化された RF、および Marketplace の機能は、ユーザーが特定のソリューション セットやワークフローに限定されないことを意味します。 固定概念にとらわれず、ぜひご連絡ください。 例えば、貴社の製品チェーンを、他のユーザーが製品に組み込めるよう、Marketplace で提供することもできます。 可能性は無限です。

プレビューの詳細について、またはプレビューへの参加の意思表明を行うには、[こちら](https://aka.ms/iaminterested)の連絡先フォームに入力するか、メール ([MSAzureOrbital@microsoft.com](mailto:MSAzureOrbital@microsoft.com)) でお問い合わせください。

## <a name="earth-observation"></a><a name="earth-observation"></a>地球観測

:::image type="content" source="./media/azure-orbital-overview/orbital-eos-dataflow.png" alt-text="Azure Orbital for Earth Observation のデータフロー" lightbox="./media/azure-orbital-overview/orbital-eos-dataflow-expanded.png":::

Azure Orbital を使用すると、維持管理とペイロードのダウンリンクのために、従量課金制で人工衛星との通信をスケジュールすることができます。 スケジュールされたアクセス時間を使用して、人工衛星からデータを取り込み、人工衛星の正常性と状態を監視するか、人工衛星にコマンドを送信します。 受信データはプライベート仮想ネットワークに配信され、Azure で処理または保存することができます。

サービスは完全にデジタル化されているため、Kratos と Amergint のソフトウェア モデムを使用して、データを復旧するための変調/復調およびエンコード/デコード関数を実行できます。 この部分は Marketplace から購入するか、管理を委託するかを選択できます。 さらに、Kubos との統合により、エンドツーエンドのソリューションをフルに活用して、フリート運用とテレメトリ、追跡、および制御 (TT&C) 関数を管理できます。 Azure のリソースとツールボックスを使用して Azure にワークロードを実装し、ペイロード データを操作して最終的なオファリングに組み込みます。

:::image type="content" source="./media/azure-orbital-overview/orbital-eos-schedule.png" alt-text="Azure Orbital for Earth Observation のスケジュール設定":::

### <a name="scheduling-contacts"></a><a name="scheduling-contacts"></a>通信のスケジュール設定

Azure Orbital を使用した通信のスケジュール設定は、次の 3 段階のプロセスで行うことができます。 

1. **宇宙船の登録** – 各人工衛星の NORAD ID、TLE およびライセンス情報を入力します。

2. **通信プロファイルを作成する** – 各リンクのセンターの周波数と帯域幅の要件に加え、最低標高や自動追跡要件などの詳細情報を入力します。 プロファイルは必要な数だけ自由に作成してください。 たとえば、1 つはコマンド専用、もう 1 つはペイロードのダウンリンク専用などです。

3. **通信をスケジュールする** – 予約するために Microsoft およびパートナー ネットワークのサイトで利用可能なパスを表示するには、宇宙船を選択し、時間と日付の枠と一緒に通信のプロファイルを選択します。 最初は先着順のアルゴリズムを使用する予定ですが、ロードマップでは優先スケジュール設定や保証されたスケジュール設定も予定しています。

プレビューの詳細について、またはプレビューへの参加の意思表明を行うには、[こちら](https://aka.ms/iaminterested)の連絡先フォームに入力するか、メール ([MSAzureOrbital@microsoft.com](mailto:MSAzureOrbital@microsoft.com)) でお問い合わせください。

## <a name="global-communication"></a><a name="global-communication"></a>グローバル通信

:::image type="content" source="./media/azure-orbital-overview/orbital-communications-use-flow.png" alt-text="Azure Orbital for Global Communications ユーザーフロー":::

グローバル通信機能を顧客に提供する人工衛星プロバイダーは、Azure Orbital を使用して、Azure データセンターまたは Azure ネットワークのエッジに新しい地上局を併置したり、既存の地上局をグローバルな Azure バックボーンと接続したりすることができます。 その後、グローバルな Microsoft ネットワークでトラフィックをルーティングし、インターネット サービスやその他の管理サービスを顧客に提供するために、Azure ネットワークのエッジからインターネットのブレークアウトを活用できます。

Azure Orbital サービスは、Orbital 地上局からプロバイダーの仮想ネットワークへトラフィックを配信します。 これらの Azure Orbital サービスを使用することで、人工衛星プロバイダーは他の Azure サービス (ファイアウォールなどのセキュリティ サービスや SDWAN などの接続サービスなど) を人工衛星接続と共に統合またはバンドルして、人工衛星接続に加えて管理サービスを顧客に提供することができます。 

プレビューの詳細について、またはプレビューへの参加の意思表明を行うには、[こちら](https://aka.ms/iaminterested)の連絡先フォームに入力するか、メール ([MSAzureOrbital@microsoft.com](mailto:MSAzureOrbital@microsoft.com)) でお問い合わせください。

## <a name="partner-ground-stations"></a><a name="digital-transformation"></a>パートナーの地上局

Azure Orbital では、独自の地上局を構築するだけでなく、パートナーの地上局を使用して、Azure にデータを直接取り込むことができます。

地上局やテレポート プロバイダーは、Azure Orbital と連携して、地上局でデジタル変革を行うことができます。 これにより、お客様はこれらの地上局を使用して、Marketplace を通じてプラットフォームと Orbital のパートナーが提供するすべてのソフトウェア無線処理およびデータ処理機能を活用しながら、人工衛星との通信をスケジュールすることができます。 このサービスは、クラウドのワークロードと密接に統合されており、モデム、リソース管理、およびミッション コントロール サービスなどのマーケットプレイスを介して、サードパーティのソリューションの活気あるエコシステムを提供しています。 またすべてのデータで、Azure の低待機時間と高い信頼性を持つグローバル ファイバー ネットワークを利用することもできます。 これにより、お客様が最大限の敏捷性と信頼性をもって人工衛星と通信するための、最も広いカバレッジと柔軟性を提供することができると信じています。

:::image type="content" source="./media/azure-orbital-overview/orbital-all-digital-transformations.png" alt-text="デジタル変革のための Azure Orbital":::

プレビューの詳細について、またはプレビューへの参加の意思表明を行うには、[こちら](https://aka.ms/iaminterested)の連絡先フォームに入力するか、メール ([MSAzureOrbital@microsoft.com](mailto:MSAzureOrbital@microsoft.com)) でお問い合わせください。

## <a name="partners"></a><a name="orbital-partners"></a>パートナー

宇宙への革新を進めるにあたり、お客様が Azure Orbital を使用してより多くのことを達成できるよう、エコシステムへパートナーを追加していく予定です。 Azure Orbital では、パートナー主導のアプローチで構築していきます。 Microsoft の目標は、パートナーとお客様の双方にとってより多くの価値を共同で生み出すために、パートナーの活気あるエコシステムを構築することでもあります。 これはまさに、サンゴ礁のようなものです。

:::image type="content" source="./media/azure-orbital-overview/orbital-all-partners.png" alt-text="Azure Orbital パートナー":::

以下のセクションでは、パートナーのカテゴリ、および既に Orbital エコシステムの一員となった Azure Orbital パートナーの一覧を示します。

### <a name="ground-station-infrastructure-partners"></a>地上局インフラストラクチャ パートナー

[KSAT](https://www.ksat.no)、[ViaSat](https://www.viasat.com/products/antenna-systems) RTE (Real-time Earth)、[US Electrodynamics](https://www.usei-teleport.com/) と提携して、お客様がこれらのパートナー地上局を使用して人工衛星と通信し、Azure に直接データを取り込むことができるようにしました。

### <a name="virtualized-modem-partners"></a>仮想化モデム パートナー

[Kratos](https://www.kratosdefense.com/) と [Amergint](https://www.amergint.com/) と提携し、Orbital プラットフォームの一部としてソフトウェア無線処理機能をクラウドで利用できるようにしました。 これらの機能は、高いスループット/帯域幅でリアルタイムに無線信号を処理するために、Azure プラットフォームのアクセラレーション (DPDK を使用した高速ネットワークや、特別な用途の Azure コンピューティングを使用した GPU ベースのアクセラレーションを含むが、これに限定されない) を採用するようにアップグレードされています。 さらに、お客様は、信号処理をより細かく詳細に制御するため、パートナーのソフトウェア モデムをAzure Marketplace から独自の仮想ネットワークにデプロイすることもできます。

### <a name="global-communication-partner"></a>グローバル通信パートナー

[SES](https://www.ses.com/) は、宇宙業界最大の人工衛星接続プロバイダーの 1 つです。 SES の[次世代の MEO 通信システムである mPower](https://www.ses.com/networks/networks-and-platforms/o3b-mpower) の地上ネットワークのニーズを強化するために Azure Orbital を採用したことを嬉しく思います。 この発表の一環として、グローバルなバックボーン ネットワークを使用した既存の地上局との相互接続に加えて、データセンターに新しい専用の地上局を配置します。 Azure のグローバルなバックボーン ネットワークに加え、Orbital プラットフォームの一部として提供されるクラウドベースの仮想化モデムを活用することで、拡張性の高いサービスとしての地上局で SES の市場投入までの時間を短縮することができます。

SES は Microsoft のグローバルなバックボーン ネットワークを活用してトラフィックをグローバルにルーティングし、Azure Orbital サービスを使用して、プラットフォーム上に構築された複数の管理サービスを顧客に提供します。 これらのサービスは、セキュリティ サービス、SDWAN、エッジ コンピューティング、5G モビリティ ソリューションから、他の複数のサービスにまで多岐に渡ります。

### <a name="ttc-solution-partner"></a>TT&C ソリューション パートナー

[Kubos](https://www.kubos.com/) と提携し、同社のクラウドベースのミッション コントロール ソフトウェアである Major Tom を Azure Orbital の顧客向けに Azure Marketplace に導入しました。

## <a name="next-steps"></a>次のステップ

プレビューの詳細について、またはプレビューへの参加の意思表明を行うには、[こちら](https://aka.ms/iaminterested)の連絡先フォームに入力するか、メール ([MSAzureOrbital@microsoft.com](mailto:MSAzureOrbital@microsoft.com)) でお問い合わせください。
