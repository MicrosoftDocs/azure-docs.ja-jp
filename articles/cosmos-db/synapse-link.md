---
title: Azure Synapse Link for Azure Cosmos DB にはどのような利点があり、いつ使用するか
description: Azure Synapse Link for Azure Cosmos DB について説明します。 Synapse Link により、Azure Synapse Analytics を使用して、Azure Cosmos DB のオペレーショナル データに対するほぼリアルタイムの分析 (HTAP) を実行できます。
author: Rodrigossz
ms.author: rosouz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2020
ms.reviewer: sngun
ms.openlocfilehash: f200fe96478e15e938899d294ecd5491d6a03206
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88814392"
---
# <a name="what-is-azure-synapse-link-for-azure-cosmos-db-preview"></a>Azure Synapse Link for Azure Cosmos DB (プレビュー) とは

> [!IMPORTANT]
> Azure Synapse Link for Azure Cosmos DB は現在プレビュー段階にあります。 このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

Azure Synapse Link for Azure Cosmos DB は、クラウド ネイティブのハイブリッド トランザクションと分析処理 (HTAP) の機能です。これを使用すると、Azure Cosmos DB のオペレーショナル データに対してリアルタイムに近い分析を実行できます。 Azure Synapse Link によって、Azure Cosmos DB と Azure Synapse Analytics の間に緊密でシームレスな統合が作成されます。

Azure Synapse Link では、完全に分離された列ストアである [Azure Cosmos DB 分析ストア](analytical-store-introduction.md)を使用することで、オペレーショナル データに対して、[Azure Synapse Analytics](../synapse-analytics/overview-what-is.md) で抽出、変換、読み込み (ETL) なしの分析を大規模に実行できます。 ビジネス アナリスト、データ エンジニア、データ サイエンティストは、ビジネス インテリジェンス、分析、機械学習のパイプラインをほぼリアルタイムで実行するために、Synapse Spark または Synapse SQL のどちらでも同じように使用できるようになりました。 Azure Cosmos DB でのトランザクション ワークロードのパフォーマンスに影響を与えることなく、これを実現できます。 

次の図では、Azure Synapse Link と Azure Cosmos DB および Azure Synapse Analytics との統合を示します。 

:::image type="content" source="./media/synapse-link/synapse-analytics-cosmos-db-architecture.png" alt-text="Azure Synapse Analytics と Azure Cosmos DB の統合のアーキテクチャ図" border="false":::

## <a name="benefits"></a><a id="synapse-link-benefits"></a> 利点

ミッション クリティカルなトランザクション ワークロードのパフォーマンスへの影響を最小限に抑えながら、大規模なオペレーショナル データセットを分析するには、従来であれば、Azure Cosmos DB のオペレーショナル データが抽出、変換、読み込み (ETL) パイプラインによって抽出および処理されます。 ETL パイプラインでは、データ移動の多くのレイヤーが必要になるため、操作が複雑になり、トランザクション ワークロードのパフォーマンスに影響します。 また、開始時からオペレーショナル データが分析されるまでの待機時間も長くなります。

従来の ETL ベースのソリューションと比較すると、Azure Synapse Link for Azure Cosmos DB には次のような利点があります。  

### <a name="reduced-complexity-with-no-etl-jobs-to-manage"></a>管理する ETL ジョブがないことによる複雑さの軽減

Azure Synapse Link では、Azure Synapse Analytics を使用して Azure Cosmos DB 分析ストアに直接アクセスすることができ、複雑なデータ移動がありません。 ETL や変更フィード ジョブがないので、オペレーショナル データに対して行われたすべての更新は、ほぼリアルタイムで分析ストアに表示されます。 追加のデータ変換を行うことなく、Synapse Analytics から分析ストアに対して大規模な分析を実行できます。

### <a name="near-real-time-insights-into-your-operational-data"></a>オペレーショナル データに関するほぼリアルタイムの分析情報

Azure Synapse Link を使用すると、オペレーショナル データに関する豊富な分析情報を、ほぼリアルタイムで取得できます。 ETL ベースのシステムでは、多くのレイヤーでオペレーショナル データを抽出、変換、読み込む必要があるため、オペレーショナル データを分析するための待機時間が長くなる傾向があります。 Azure Cosmos DB 分析ストアと Azure Synapse Analytics のネイティブな統合により、オペレーショナル データをほぼリアルタイムで分析でき、新しいビジネス シナリオが可能になります。 


### <a name="no-impact-on-operational-workloads"></a>運用ワークロードへの影響がない

Azure Synapse Link では、トランザクション ワークロード用にプロビジョニングされたスループットを使用してトランザクション操作を処理しながら (行ベースのトランザクション ストア)、Azure Cosmos DB 分析ストア (個別の列ストア) に対して分析クエリを実行できます。  分析ワークロードは、トランザクション ワークロードのトラフィックとは無関係に処理され、オペレーショナル データ用にプロビジョニングされたスループットは使用されません。

### <a name="optimized-for-large-scale-analytics-workloads"></a>大規模な分析ワークロード用に最適化

Azure Cosmos DB 分析ストアは、コンピューティング ランタイムに依存せずに、分析ワークロードに対してスケーラビリティ、弾力性、パフォーマンスを提供するように最適化されています。 ストレージ テクノロジは、分析ワークロードを最適化するように自己管理されています。 Azure Synapse Analytics のサポートが組み込まれているため、このストレージ レイヤーに対するアクセスは簡単でハイ パフォーマンスです。

### <a name="cost-effective"></a>コスト効率

Azure Synapse Link を使うと、コスト効率のよいフル マネージドの運用分析用ソリューションを利用できます。 これにより、従来の ETL パイプラインでオペレーショナル データを分析するために必要であったストレージとコンピューティングの余分なレイヤーが不要になります。 

Azure Cosmos DB 分析ストアは、使用量ベースの価格モデルに従います。このモデルは、データ ストレージと、実行された分析の読み書き操作およびクエリに基づいています。 トランザクション ワークロードに対して現在行っているような、スループットのプロビジョニングは必要ありません。 Azure Synapse Analytics から高度なエラスティック コンピューティング エンジンを使用してデータにアクセスすることにより、ストレージとコンピューティングの実行のコスト効率が大幅に向上します。


### <a name="analytics-for-locally-available-globally-distributed-multi-master-data"></a>ローカルに使用可能でグローバルに分散されたマルチ マスター データの分析

Azure Cosmos DB のデータの最も近いリージョン コピーに対して、分析クエリを効率的に実行できます。 Azure Cosmos DB には、グローバルに分散された分析ワークロードとトランザクション ワークロードをアクティブ/アクティブ方式で実行するための最新の機能が用意されています。

## <a name="enable-htap-scenarios-for-your-operational-data"></a>オペレーショナル データの HTAP シナリオを有効にする

Synapse Link によって、Azure Cosmos DB 分析ストアと Azure Synapse Analytics のランタイム サポートが統合されます。 この統合により、大規模なデータセットのオペレーショナル データに対するリアルタイムの更新に基づいて分析情報を生成する、クラウド ネイティブの HTAP (ハイブリッド トランザクション/分析処理) ソリューションを構築できます。 それによって、ライブの傾向に基づいてアラートを生成したり、ほぼリアルタイムのダッシュボードを作成したりする新しいビジネス シナリオや、ユーザーの行動に基づくビジネス エクスペリエンスが、可能になります。

### <a name="azure-cosmos-db-analytical-store"></a>Azure Cosmos DB 分析ストア

Azure Cosmos DB 分析ストアは、Azure Cosmos DB 内のオペレーショナル データの列指向の表現です。 この分析ストアは、大規模なオペレーショナル データ セットに対する高速でコスト効率の高いクエリに適しており、データをコピーする必要や、トランザクション ワークロードのパフォーマンスに対する影響はありません。

分析ストアでは、Azure Cosmos DB のフル マネージド機能 ("自動同期") として、トランザクション ワークロードでの高頻度の挿入、更新、削除がほぼリアルタイムで自動的に取得されます。 変更フィードや ETL は必要ありません。 

グローバルに分散された Azure Cosmos DB アカウントがある場合、コンテナーの分析ストアを有効にした後、そのアカウントのすべてのリージョンでそれを使用できるようになります。 分析ストアの詳細については、[Azure Cosmos DB 分析ストアの概要](analytical-store-introduction.md)に関する記事を参照してください。

### <a name="integration-with-azure-synapse-analytics"></a><a id="synapse-link-integration"></a>Azure Synapse Analytics との統合

Synapse Link では、Azure Synapse Analytics から Azure Cosmos DB コンテナーに直接接続し、別のコネクタを使用せずに分析ストアにアクセスできるようになりました。 Azure Synapse Analytics では、現在、[Synapse Apache Spark](../synapse-analytics/spark/apache-spark-concepts.md) および [Synapse SQL サーバーレス](../synapse-analytics/sql/on-demand-workspace-overview.md)との Synapse Link をサポートしています。

Azure Synapse Analytics でサポートされているさまざまな分析ランタイム間の相互運用機能により、Azure Cosmos DB 分析ストアからデータを同時にクエリできます。 オペレーショナル データを分析するために、追加のデータ変換は必要ありません。 次のものを使用して、分析ストアのデータのクエリと分析を行うことができます。

* Scala、Python、SparkSQL、C# が完全にサポートされている Synapse Apache Spark。 Synapse Spark は Data Engineering とデータ サイエンスのシナリオにおける中心的存在です

* T-SQL 言語を使用する SQL サーバーレスと、使い慣れた BI ツール (Power BI Premium など) に対するサポート

> [!NOTE]
> Azure Synapse Analytics からは、Azure Cosmos DB コンテナー内の分析ストアとトランザクション ストアの両方にアクセスできます。 ただし、オペレーショナル データに対して大規模な分析やスキャンを実行する場合は、トランザクション ワークロードのパフォーマンスへの影響を避けるため、分析ストアを使用することをお勧めします。

> [!NOTE]
> Azure Cosmos DB コンテナーを Azure リージョンの Synapse ランタイムに接続することにより、そのリージョン内での分析を低待機時間で実行できます。

この統合により、次のようなさまざまなユーザーに対する HTAP シナリオが可能になります。

* Synapse SQL を通じて Azure Cosmos DB のオペレーショナル データに直接アクセスし、レポートをモデル化して発行する BI エンジニア。

* Synapse SQL でクエリを実行することで、Azure Cosmos DB コンテナー内のオペレーショナル データから分析情報を抽出し、大規模なデータを読み取り、その結果を他のデータ ソースと結合するデータ アナリスト。

* 複雑な Data Engineering を行わずにモデルを改善し、そのモデルをトレーニングする機能を、Synapse Spark を使用して見つけたいデータ サイエンティスト。 また、Spark Synapse を使用してデータのスコアリングをリアルタイムで行うために、モデルのポスト推論の結果を Azure Cosmos DB に書き込むこともできます。

* 手動の ETL プロセスを使用せずに、Azure Cosmos DB コンテナーに対する SQL テーブルまたは Spark テーブルを作成することで、コンシューマーがデータにアクセスできるようにしたいと考えているデータ エンジニア。

Azure Cosmos DB の Azure Synapse Analytics ランタイムによるサポートの詳細については、[Cosmos DB に対する Azure Synapse Analytics のサポート](../synapse-analytics/synapse-link/concept-synapse-link-cosmos-db-support.md)に関するページを参照してください。

## <a name="when-to-use-azure-synapse-link-for-azure-cosmos-db"></a>どのようなときに Azure Synapse Link for Azure Cosmos DB を使用するか

Synapse Link は、次の場合にお勧めします。

* Azure Cosmos DB のお客様で、オペレーショナル データに対して分析、BI、機械学習を実行したい場合。 このような場合、Synapse Link では、トランザクション ストアのプロビジョニング済みスループットに影響を与えることなく、より統合された分析エクスペリエンスが提供されます。 次に例を示します。

  * 現在、別のコネクタを使用して、Azure Cosmos DB のオペレーショナル データで分析または BI を直接実行している場合

  * ETL プロセスを実行して、別の分析システムにオペレーショナル データを抽出している場合。
 
このような場合、Synapse Link では、トランザクション ストアのプロビジョニング済みスループットに影響を与えることなく、より統合された分析エクスペリエンスが提供されます。

高いコンカレンシー、ワークロードの管理、複数のデータ ソース間での集計の永続化など、従来のデータ ウェアハウスの要件を探している場合は、Synapse Link は推奨されません。 詳細については、[Azure Synapse Link for Azure Cosmos DB を使用して実現できる一般的なシナリオ](synapse-link-use-cases.md)に関するページを参照してください。


## <a name="limitations"></a>制限事項

* パブリック プレビューの間の Azure Synapse Link は、Azure Cosmos DB SQL (Core) API に対してのみサポートされます。 MongoDB 用の Azure Cosmos DB の API と Cassandra API に対するサポートは、現在限定的なプレビュー中です。 限定的なプレビューへのアクセスを要求するには、[Azure Cosmos DB チーム](mailto:cosmosdbsynapselink@microsoft.com)にメールでご連絡ください。

* 現時点では、分析ストアは (新規と既存のどちらの Azure Cosmos DB アカウントでも) 新しいコンテナーに対してのみ有効にすることができます。

* プレビューでは、Synapse Link が有効になっているデータベース アカウントでは、コンテナーのバックアップと復元はサポートされていません。 バックアップと復元の機能を必要とする運用環境のワークロードがある場合、それらのデータベース アカウントでは Synapse Link を有効にしないことをお勧めします。 

* Synapse SQL サーバーレスでの Azure Cosmos DB 分析ストアへのアクセスは、現在限定的なプレビューの段階にあります。 アクセスを要求するには、[Azure Cosmos DB チーム](mailto:cosmosdbsynapselink@microsoft.com)にメールでご連絡ください。

* プロビジョニングされた Synapse SQL での Azure Cosmos DB 分析ストアへのアクセスは、現在使用できません。

## <a name="pricing"></a>価格

Azure Synapse Link の課金モデルには、Azure Cosmos DB 分析ストアと Synapse ランタイムを使用して発生するコストが含まれます。 詳細については、[Azure Cosmos DB 分析ストアの価格](analytical-store-introduction.md#analytical-store-pricing)に関するページおよび「[Azure Synapse Analytics の価格](https://azure.microsoft.com/pricing/details/synapse-analytics/)」をご覧ください。

## <a name="next-steps"></a>次のステップ

詳しく学習するために、次のドキュメントを参照してください。

* [Azure Cosmos DB 分析ストアの概要](analytical-store-introduction.md)

* [Azure Synapse Link for Azure Cosmos DB の概要](configure-synapse-link.md)
 
* [Azure Synapse Analytics ランタイムでサポートされている機能](../synapse-analytics/synapse-link/concept-synapse-link-cosmos-db-support.md)

* [Azure Synapse Link for Azure Cosmos DB についてよく寄せられる質問](synapse-link-frequently-asked-questions.md)

* [Azure Synapse Link for Azure Cosmos DB のユース ケース](synapse-link-use-cases.md)
