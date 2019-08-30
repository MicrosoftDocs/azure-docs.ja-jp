---
title: Azure Search サービスの価格レベルまたは SKU の選択 - Azure Search
description: Azure Search をプロビジョニングできる SKU は、Free、Basic、および Standard です。Standard は、さまざまなリソース構成および容量レベルで使用できます。
services: search
author: HeidiSteen
manager: nitinme
tags: ''
ms.service: search
ms.topic: conceptual
ms.date: 08/15/2019
ms.author: heidist
ms.openlocfilehash: 1c86649a989b16d928a46d322af3d805b6fbf832
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647349"
---
# <a name="choose-a-pricing-tier-for-azure-search"></a>Azure Search の価格レベルの選択

Azure Search サービスを作成すると、サービスの有効期間にわたって固定される価格レベル (つまり SKU) で[リソースが作成](search-create-service-portal.md)されます。 レベルには、Free、Basic、Standard、および Storage Optimized があります。 Standard と Storage Optimized は、いくつかの構成および容量で利用できます。

ほとんどのお客様は、サービスを評価できるように、Free レベルから始めます。 評価後には、開発および運用環境用のデプロイのために上位レベルのいずれかに 2 つ目のサービスを作成するのが一般的です。

一般に、Free レベルを含むすべてのレベルで機能パリティが提供されますが、ワークロードが大きいほど、より高いレベルが必要になる可能性があります。 たとえば、[Cognitive Services を利用した AI エンリッチメント](cognitive-search-concept-intro.md)には、データセットのサイズが小さい場合を除いて Free サービスではタイムアウトになってしまう、実行時間の長いスキルがあります。

> [!NOTE] 
> 機能パリティの例外は[インデクサー](search-indexer-overview.md)で、これは S3 HD では利用できません。
>

<!-- For Basic tier and up, you can [adjust replica and partition resources](search-capacity-planning.md) to increase or decrease scale. You could start with one or two of each and then temporarily raise your computational power for a heavy indexing workload. The ability to tune resource levels within a tier adds flexibility, but also slightly complicates your analysis. You might have to experiment to see whether a lower tier with more resources/replicas offers better value and performance than a higher tier with fewer resources. To learn more about when and why you would adjust capacity, see [Performance and optimization considerations](search-performance-optimization.md). -->

## <a name="available-tiers"></a>使用可能なレベル

レベルは (機能ではなく) サービスをホストしているハードウェアの特性を反映していて、以下によって差別化されています。

+ 作成できるインデックスとインデクサーの数
+ パーティション (物理ストレージ) のサイズと速度

選択したレベルによって課金対象のレートが決まります。 次の Azure portal のスクリーンショットは、使用可能なレベルを示しています。価格は除外されていますが、ポータルと[価格に関するページ](https://azure.microsoft.com/pricing/details/search/)で確認できます。 最も一般的なレベルは、**Free**、**Basic**、**Standard** です。

**Free** では、クラスター上に限定的な検索サービスが作成され、他のサブスクライバーと共有されます。 クイックスタートやチュートリアルなどの小規模なプロジェクトを完成させることはできますが、サービスをスケーリングしたり、重要なワークロードを実行したりすることはできません。 **Basic** と **Standard** は最も一般的に使用される課金対象のレベルで、**Standard** が既定値になっています。

![Azure Search の価格レベル](media/search-sku-tier/tiers.png "Azure Search の価格レベル")

一部のレベルは、特定の種類の作業向けに最適化されています。 たとえば、**Standard 3 High Density (S3 HD)** は、S3 用の "*ホスティング モード*" で、基になるハードウェアが多数の小さいインデックス用に最適化されており、マルチテナント シナリオ向けです。 S3 HD のユニットあたりの料金は S3 と同じですが、ハードウェアは数多くの小さいインデックスでの高速ファイル読み取り用に最適化されています。

**Storage Optimized** レベルでは、Standard レベルより安い TB あたりの価格で、大容量のストレージが提供されます。 主なトレードオフとしてクエリの待ち時間が長くなるので、特定のアプリケーションの要件に対して妥当かどうかを確認する必要があります。  このレベルのパフォーマンスに関する考慮事項の詳細については、[パフォーマンスと最適化の考慮事項](search-performance-optimization.md)に関するページを参照してください。

さまざまなレベルの詳細については、[価格に関するページ](https://azure.microsoft.com/pricing/details/search/)、「[Azure Search サービスの制限](search-limits-quotas-capacity.md)」、サービスのプロビジョニング時のポータル ページを参照してください。

## <a name="billable-events"></a>課金対象のイベント

Azure Search 上に構築されたソリューションでは、次のようなコストが発生する場合があります。

+ 最小構成でのサービスの基本コスト (サービスの作成)
+ スケールアップ (レプリカまたはパーティションの追加) 時の増分コスト
+ 帯域幅料金 (送信データ転送) 
+ コグニティブ検索 (AI エンリッチメントには Cognitive Services、ナレッジ ストアには Azure Storage をアタッチする)

### <a name="service-costs"></a>サービスのコスト

課金を回避するために "一時停止" できる仮想マシンやその他のリソースとは異なり、Azure Search サービスは、お客様が独占的に使用できる専用のハードウェア上で常に使用可能です。 そのため、サービスの作成は課金対象のイベントであり、そのサービスを作成したときに開始され、そのサービスを削除したときに終了します。 

最低料金は、課金対象のレートでの最初の検索単位 (1 つのレプリカ x 1 つのパーティション) です。 これより小さい構成ではサービスは実行できないため、この最低料金はサービスの有効期間を通して一定です。 最小構成を超える場合は、レプリカとパーティションを互いに独立して追加できます。 レプリカおよびパーティションによって容量が徐々に増加すると、[(レプリカ数 x パーティション数 x レート)](#search-units) という式に基づいて料金が増加します。この場合、課金されるレートは、選択した価格レベルによって異なります。

検索ソリューションのコストを見積もる際には、価格と容量は直線的に比例するものではないことに注意してください (容量を 2 倍にすると、コストは 2 倍より多くなります)。数式による計算の例については、「[レプリカとパーティションを割り当てる方法](search-capacity-planning.md#how-to-allocate-replicas-and-partitions)」を参照してください。

### <a name="bandwidth-charges"></a>帯域幅の料金

[Azure Search インデクサー](search-indexer-overview.md)を使用すると、サービスの場所によっては、課金に影響することがあります。 Azure Search サービスをデータと同じリージョンに作成すれば、データ エグレス料金が発生する事態を回避できます。 以下に、[帯域幅の価格に関するページ](https://azure.microsoft.com/pricing/details/bandwidth/)の情報の一部を示します。

+ Azure 上のあらゆるサービスへのインバウンド データと、Azure Search からのアウトバウンド データには料金が課金されません。
+ マルチサービス ソリューションでは、すべてのサービスが同じリージョンにあれば、伝送データに料金はかかりません。

それらのサービスが別々のリージョンにある場合は、アウトバウンド データに料金が適用されます。 これらの料金は、実際には Azure Search の課金の一部ではありません。 データまたは AI によって強化されたインデクサーを使用して異なるリージョンからデータをプルする場合、それらのコストが全体の請求書に反映されるため、ここで言及しています。

### <a name="cognitive-search-ai-enrichment-with-cognitive-services"></a>Cognitive Services を使用するコグニティブ検索の AI エンリッチメント

[Cognitive Services を使用する AI エンリッチメント](cognitive-search-concept-intro.md)の場合は、従量課金制の処理について、Azure Search と同じリージョンの S0 価格レベルで、[有料の Azure Cognitive Services リソースをアタッチする](cognitive-search-attach-cognitive-services.md)ように計画することをお勧めします。 Cognitive Services のアタッチには、関連する固定コストはありません。 課金の対象となるのは、必要な処理の分だけです。

| Operation | 課金への影響 |
|-----------|----------------|
| ドキュメント解析、テキスト抽出 | 無料 |
| ドキュメント解析、画像抽出 | ドキュメントから抽出された画像の数に基づいて課金されます。 **インデクサー構成**で、[imageAction](https://docs.microsoft.com/rest/api/searchservice/create-indexer#indexer-parameters) は、画像抽出をトリガーするパラメーターです。 **imageAction** が "none" (既定値) に設定されている場合、画像の抽出に対して課金されません。 画像抽出のレートは、Azure Search の[価格の詳細](https://azure.microsoft.com/pricing/details/search/)に関するページに記載されています。|
| [事前に構築されたコグニティブ スキル](cognitive-search-predefined-skills.md) | Cognitive Services を直接使用してそのタスクを実行した場合と同じレートで課金されます。 |
| カスタム スキル | カスタム スキルは、自分が提供する機能です。 カスタム スキルを使用した場合のコストは、カスタム コードで他の従量制サービスを呼び出しているかどうかによって決まります。 |

<a name="search-units"></a>

## <a name="billing-formula-r-x-p--su"></a>請求式 (R x P = SU)

Azure Search の操作について理解するための最も重要な課金の概念は、"*検索ユニット*" (SU) です。 Azure Search は、インデックス作成とクエリについてレプリカとパーティションの両方に依存しているため、この片方のみを基準に課金しても意味がありません。 このため、この両方を合わせた単位に基づいて課金されます。

SU は、サービスによって使用される "*レプリカ*" と "*パーティション*" の積です **(R x P = SU)** 。

すべてのサービスは、最小値として、1 SU (1 つのレプリカと 1 つのパーティションの積) から開始します。 どのサービスも、最大値は 36 SU です。 この最大値に達するパターンは、いくつかあります。たとえば、6 パーティション x 6 レプリカ、3 パーティション x 12 レプリカなどです。 一般には、総容量よりも少ない量を使用します (たとえば、3 レプリカと 3 パーティションのサービスで、9 SU の課金)。 有効な組み合わせについては、「[パーティションとレプリカの組み合わせ](search-capacity-planning.md#chart)」の表を参照してください。

課金レートは、SU ごとの 1 時間単位です。 レベルごとに、レートが高くなっていきます。 レベルが高いほど、パーティションは大きく高速になり、そのレベルの時間あたりの料金が全体的に高くなります。 各レベルのレートは、[価格の詳細](https://azure.microsoft.com/pricing/details/search/)に関するページでご覧いただけます。

ほとんどのお客様は、総容量の一部だけをオンラインにし、残りを取っておきます。 課金については、オンラインにするパーティションとレプリカの数 (SU 式を使用して計算) によって、時間単位で支払う金額が決まります。

## <a name="how-to-manage-and-reduce-costs"></a>コストを管理および削減する方法

次の提案に加えて、[課金とコスト管理](https://docs.microsoft.com/azure/billing/billing-getting-started)に関するページも参照してください。

- 帯域幅の料金を最小限に抑えるかなくすために、すべてのリソースを同一のリージョンか可能な限り少ないリージョンに作成します。

- Azure Search、Cognitive Services、ご利用のソリューションで使用されているその他の Azure サービスなど、すべてのサービスを 1 つのリソース グループに統合します。 Azure portal で、リソース グループを見つけ、 **[コスト管理]** のコマンドを使用して、実際の支出と予想される支出を把握します。

- フロントエンド アプリケーションには、要求と応答がデータ センターの境界内に収まるように Azure Web アプリを検討します。

- インデックス作成などのリソースを大量に消費する操作に対してはスケールアップし、通常のクエリ ワークロードに対して再調整してスケールダウンします。 Azure Search の最小構成 (1 つのパーティションと 1 つのレプリカで構成された 1 つの SU) から開始し、ユーザー アクティビティを監視して、容量の増加に対するニーズを示す使用パターンを特定します。 予測可能なパターンがある場合は、スケールをアクティビティと同期できることがあります (これを自動化するにはコードを記述する必要があります)。

課金を抑えるために検索サービスをシャットダウンすることはできません。 専用リソースは常に動作し、サービスの有効期間中、お客様専用として割り当てられています。 サービス自体に関して、課金を抑える唯一の方法は、レプリカとパーティションを、まだ許容可能なパフォーマンスと [SLA コンプライアンス](https://azure.microsoft.com/support/legal/sla/search/v1_0/)を提供する低いレベルに下げるか、より低いレベルでサービスを作成することです (S1 の時間あたりの料金は S2 または S3 の料金よりも低くなります)。 負荷予測の下限でサービスをプロビジョニングすると仮定した場合、サービスが拡大したら、2 番目に高いレベルのサービスを作成し、その 2 番目のサービスでインデックスを再構築してから、最初のサービスを削除することができます。

## <a name="how-to-evaluate-capacity-requirements"></a>容量の要件を評価する方法

Azure Search では、容量は*レプリカ*と*パーティション*で構成されています。

+ レプリカは、検索サービスのインスタンスです。 各レプリカは、負荷分散されたインデックスのコピーを 1 つホストします。 たとえば、6 つのレプリカが存在するサービスでは、各インデックスの 6 つのコピーがサービスに読み込まれています。

+ パーティションにインデックスが格納され、検索可能なデータが自動的に分割されます。 パーティションが 2 つの場合、インデックスは 2 つに分割され、パーティションが 3 つの場合は 3 分の 1 に分割されるといったぐあいです。 容量の点では、"*パーティション サイズ*" が、レベル間で大きな違いのある機能です。

> [!NOTE]
> すべての Standard および Storage Optimized レベルで、[レプリカとパーティションを柔軟に組み合わせる](search-capacity-planning.md#chart)ことができます。そのバランスを変更することにより、[システムを速度またはストレージ量の点で最適化](search-performance-optimization.md)できます。 Basic レベルでは、最大で 3 つのレプリカが使用できるため可用性が高くなりますが、パーティションは 1 つのみです。 Free レベルでは、専用のリソースが提供されません。コンピューティング リソースは複数のサブスクライバー間で共用されます。

<!-- ## Consumption patterns

On the low and high ends, Basic and S3 HD are for important but atypical consumption patterns. Basic is for small production workloads. It offers SLAs, dedicated resources, and high availability, but it provides modest storage, topping out at 2 GB total. This tier was engineered for customers that consistently underutilize available capacity. At the high end, S3 HD is for workloads typical of ISVs, partners, [multitenant solutions](search-modeling-multitenant-saas-applications.md), or any configuration that calls for a large number of small indexes. It's often clear when Basic or S3 HD is the right tier. If you want confirmation, you can post to [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) or [contact Azure support](https://azure.microsoft.com/support/options/) for guidance.

The more commonly used standard tiers, S1 through S3, make up a progression of increasing levels of capacity. There are inflection points on partition size and limits on numbers of indexes, indexers, and corollary resources:

|  | S1 | S2 | S3 |  |  |  |  |
|--|----|----|----|--|--|--|--|
| Partition size|  25 GB | 100 GB | 200 GB |  |  |  |  |
| Index and indexer limits| 50 | 200 | 200 |  |  |  |  |

S1 is a common choice for customers that need dedicated resources and multiple partitions. S1 offers partitions of 25 GB and up to 12 partitions, providing a per-service limit of 300 GB if you maximize partitions over replicas. (See [Allocate partitions and replicas](search-capacity-planning.md#chart) for more balanced allocations.)

The portal and pricing pages put the focus on partition size and storage, but, for each tier, all compute capabilities (disk capacity, speed, CPUs) generally increase linearly with price. An S2 replica is faster than S1, and S3 is faster than S2. S3 tiers break from the linear compute-pricing pattern with disproportionately faster I/O. If you expect I/O to be the bottleneck, keep in mind that you can get much more IOPS with S3 than you can get with lower tiers.

S3 and S3 HD are backed by identical high-capacity infrastructure, but they reach their maximum limits in different ways. S3 targets a smaller number of very large indexes, so its maximum limit is resource-bound (2.4 TB for each service). S3 HD targets a large number of very small indexes. At 1,000 indexes, S3 HD reaches its limits in the form of index constraints. If you're an S3 HD customer and you need more than 1,000 indexes, contact Microsoft Support for information about how to proceed.

> [!NOTE]
> Document limits were a consideration at one time, but they're no longer applicable for new services. For information about conditions in which document limits still apply, see [Document limits](search-limits-quotas-capacity.md#document-limits).
>

Storage Optimized tiers, L1 and L2, are ideal for applications with large data requirements but a relatively low number of end users, when minimizing query latency isn't the top priority.  

|  | L1 | L2 |  |  |  |  |  |
|--|----|----|--|--|--|--|--|
| Partition size|  1 TB | 2 TB |  |  |  |  |  |
| Index and indexer limits| 10 | 10 |  |  |  |  |  |

L2 offers twice the overall storage capacity of L1.  Choose your tier based on the maximum amount of data that you think your index needs. The L1 tier partitions scale up in 1-TB increments to a maximum of 12 TB. The L2 partitions increase by 2 TBs per partition up to a maximum of 24 TB. -->

### <a name="evaluating-capacity"></a>容量の評価

容量とサービスの実行コストは密接に関係しています。 レベルによって、ストレージとリソースという 2 つの要素に制限が設けられます。 先に上限に達した方が実質的な制限になるため、両方を考慮する必要があります。

通常、必要となるインデックスの数は、ビジネス要件によって規定されます。 たとえば、ドキュメントの大規模なリポジトリ用にグローバル インデックスが必要な場合があります。 また、リージョン、アプリケーション、またはビジネス分野に基づいて複数のインデックスが必要な場合があります。

インデックスのサイズを特定するには、インデックスを 1 つ[構築](search-create-index-portal.md)する必要があります。 Azure Search のデータ構造は、主に[転置インデックス](https://en.wikipedia.org/wiki/Inverted_index)構造であり、ソース データとは異なる特性があります。 転置インデックスのサイズと複雑性はコンテンツによって決まり、必ずしもそれにフィードするデータ量によって決まるものではありません。 冗長性の高い大規模なデータ ソースは、変動の多いコンテンツを含む小さいデータセットよりも、インデックスのサイズが小さくなることがあります。 そのため、元のデータ セットのサイズに基づいてインデックスのサイズを推測できることはほとんどありません。

> [!NOTE] 
> インデックスとストレージの将来のニーズの見積もりは、当て推量のように感じられるかもしれませんが、行う価値があります。 あるレベルの容量が少なすぎることがわかった場合は、それより上のレベルで新しいサービスをプロビジョニングしたうえで、[インデックスを再読み込み](search-howto-reindex.md)する必要があります。 特定の SKU から別の SKU へのサービスのインプレース アップグレードを実行することはできません。
>

### <a name="estimate-with-the-free-tier"></a>Free レベルでの見積もり

容量を見積もる方法の 1 つは、まず Free レベルを使用することです。 Free サービスでは、最大 3 つのインデックス、50 MB のストレージ、および 2 分間のインデックス作成時間が提供されます。 これらの制約の中で予想インデックス サイズを見積もることは簡単ではない可能性がありますが、その手順は次のとおりです。

+ [Free サービスを作成](search-create-service-portal.md)します。
+ 小さな代表的なデータセットを準備します。
+ [ポータルで最初のインデックスを構築](search-create-index-portal.md)し、そのサイズをメモします。 機能と属性はストレージに影響を与えます。 たとえば、suggester (先行入力) を追加すると、ストレージ要件が増加します。 同じデータ セットを使用する場合、各フィールドに異なる属性を設定してインデックスの複数のバージョンを作成し、ストレージ要件がどのように変化するかを確認してみてください。 詳細については、[基本的なインデックスの作成に関するページの「ストレージへの影響」](search-what-is-an-index.md#storage-implications)を参照してください。

大まかな見積もりが得られたら、2 つのインデックス (開発用と運用用) の量を予測するためにこの値を 2 倍にし、それに応じてレベルを選択します。

### <a name="estimate-with-a-billable-tier"></a>課金対象レベルでの見積もり

専用のリソースでは、より長いサンプリングと処理時間に対応でき、開発段階でのインデックスの量、サイズ、クエリ量についてより現実的な見積もりを求めることができます。 課金対象のレベルから始め、開発プロジェクトが成熟するのに応じて再評価するお客様もいます。

1. [各レベルのサービス制限を確認](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#index-limits)して、より低いレベルで、必要なインデックス数に対応できるかどうかを判断してください。 Basic、S1、S2 レベルでのインデックス制限は、それぞれ 15、50、200 です。 Storage Optimized レベルは、少数の非常に大きいインデックスをサポートするように設計されているため、10 インデックスに制限されています。

1. [課金対象レベルでサービスを作成します](search-create-service-portal.md)。

    + 予想される負荷がわからない場合は、Basic または S1 の低いレベルで始めます。
    + サイズの大きなインデックスの作成とクエリの負荷への対応が必要になることがわかっている場合は、S2 または S3 の高レベルから始めます。
    + 社内のビジネス アプリケーションのように、インデックスを付けるデータの量が多く、クエリの負荷が比較的低い場合は、Storage Optimized (L1 または L2) から始めます。

1. [最初のインデックスを構築](search-create-index-portal.md)して、ソース データがどのようにインデックスに変換されるかを特定します。 これは、インデックスのサイズを推測する唯一の方法です。

1. ポータルで、[ストレージ、サービス制限、クエリ量、および待機時間を監視](search-monitor-usage.md)します。 秒あたりのクエリ数、調整されたクエリ数、および検索の待ち時間がポータルに表示されます。 これらすべての値が、適切なレベルを選択したかどうかを判断するために役立ちます。 [検索トラフィック分析](search-traffic-analytics.md)を有効にすることで、クリックスルー分析など、値の詳細な監視も構成できます。

インデックスの数とサイズは、分析にとってどちらも同様に重要です。 ストレージ (パーティション) を使い果たしたか、リソース (インデックス、インデクサーなど) の上限に達したか、どちらか早い方で上限に達したとされるためです。 ポータルの [概要] ページでは、現在の使用状況と最大制限が並んで表示されるため、両方を追跡できます。

> [!NOTE]
> ドキュメントに余分なデータが含まれている場合は、ストレージの必要量が多くなる可能性があります。 ドキュメントには、検索機能に必要なデータのみが含まれていることが理想的な状態です。 バイナリ データは検索できないため、別途保存する必要があります (おそらく Azure テーブルまたは BLOB ストレージに)。 その後、外部データへの URL 参照を保持するためのフィールドをインデックスに追加する必要があります。 個々のドキュメントの最大サイズは 16 MB です (1 回の要求で複数のドキュメントを一括アップロードする場合は、それより小さくなります)。 詳細については、「[Azure Search サービスの制限](search-limits-quotas-capacity.md) 」を参照してください。
>

**クエリ数に関する考慮事項**

クエリ/秒 (QPS) は、パフォーマンスのチューニング中の重要なメトリックですが、一般に、最初からクエリ数が多いことが予想される場合は、レベルに関する考慮事項に過ぎません。

Standard レベルでは、レプリカとパーティションのバランスを取ることができます。 負荷分散用のレプリカを追加したり、並列処理用のパーティションを追加したりすることで、クエリのターンアラウンドを向上させることができます。 その後、サービスがプロビジョニングされた後に、パフォーマンスをチューニングできます。

最初から大量のクエリ数が継続することが予想される場合は、より強力なハードウェアを使用する、より高い Standard レベルを検討してください。 その後、これらのクエリ数が発生しなかった場合は、パーティションとレプリカをオフラインにするか、より低いレベルのサービスに切り替えることができます。 クエリ スループットの計算方法の詳細については、「[Azure Search のパフォーマンスと最適化に関する考慮事項](search-performance-optimization.md)」を参照してください。

Storage Optimized レベルは、大規模なデータ ワークロードに適しており、クエリの待ち時間要件がそれほど重要でない場合に、より全体的に利用可能なインデックス ストレージをサポートします。 それでも、負荷分散のためにはレプリカを追加し、並列処理のためにはパーティションを追加する必要があります。 その後、サービスがプロビジョニングされた後に、パフォーマンスをチューニングできます。

**サービスレベル アグリーメント**

Free レベルおよびプレビュー機能には、[サービスレベル アグリーメント (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/) がありません。 課金対象のすべてのレベルで、SLA が有効になるのは、サービスにとって十分な冗長性がプロビジョニングされるときです。 クエリ (読み取り) の SLA には複数のレプリカが必要です。 クエリとインデックス作成 (読み取り/書き込み) の SLA には 3 つ以上のレプリカが必要です。 パーティションの数は、SLA に影響しません。

## <a name="tips-for-tier-evaluation"></a>レベルの評価に関するヒント

+ 効率的なインデックスを構築する方法と、どの更新方法が最も影響が少ないかについて説明します。 [検索トラフィック分析](search-traffic-analytics.md)を使用して、クエリ アクティビティに関する分析情報を取得します。

+ クエリに関するメトリックを構築し、利用パターン (業務時間中のクエリ数、オフピーク時のインデックス作成) に関するデータを収集します。 このデータを使用して、サービス プロビジョニングに関する決定に役立つ情報を提供します。 時間ごと、または毎日の周期では実用的でありませんが、パーティションやリソースを動的に調節して、クエリ数の計画された変化に対応できます。 計画外ではあっても持続した変化に対応することもできます。ただし、対処する間、そのレベルが保持される場合です。

+ 過小なプロビジョニングの唯一の欠点として、実際の要件が予測より大きかった場合にサービスを中断する必要があるということに注意してください。 サービスの中断を回避するには、同じサブスクリプション内のより高いレベルで新しいサービスを作成し、すべてのアプリと要求が新しいエンドポイントを指すようになるまで並行して実行します。

## <a name="next-steps"></a>次の手順

Free レベルから始め、データのサブセットを使用して最初のインデックスを構築することで、その特性を理解します。 Azure Search のデータ構造は、転置インデックス構造です。 転置インデックスのサイズと複雑性はコンテンツによって決まります。 冗長性の高いコンテンツでは、不規則なコンテンツよりもインデックスのサイズが小さくなる傾向があります。 そのため、インデックス ストレージの要件を決定するのは、データ セットのサイズではなく、コンテンツの特性です。

インデックス サイズの最初の見積もり後、この記事で説明されているレベルのいずれかの[課金対象のサービスをプロビジョニング](search-create-service-portal.md)します (Basic、Standard、または Storage Optimized)。 データのサイズ決定に対する人為的な制約を緩和し、検索可能にする必要があるすべてのデータが含まれるよう、[インデックスを再構築](search-howto-reindex.md)します。

必要なパフォーマンスとスケールに合わせて[パーティションとレプリカを割り当て](search-capacity-planning.md)ます。

パフォーマンスと容量に問題なければ完了です。 問題がある場合は、より密接にニーズに適合するよう、別のレベルで検索サービスを再作成します。

> [!NOTE]
> ご不明な点がある場合は、[StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) に投稿するか、[Azure サポートにお問い合わせ](https://azure.microsoft.com/support/options/)ください。
