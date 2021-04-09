---
title: Azure Synapse Link for Azure Cosmos DB を使用したリアルタイム分析のユース ケース
description: サプライ チェーン分析、予測、レポート作成、リアルタイム パーソナル化、IOT 予測メンテナンスで Azure Synapse Link for Azure Cosmos DB を使用する方法について説明します。
author: tknandu
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: ramkris
ms.custom: synapse-cosmos-db
ms.openlocfilehash: 253257564ffc96108f282d7de3dbd9f5e8ce68fd
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104869873"
---
# <a name="azure-synapse-link-for-azure-cosmos-db-near-real-time-analytics-use-cases"></a>Azure Synapse Link for Azure Cosmos DB:凖リアルタイム分析のユース ケース
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

[Azure Synapse Link](synapse-link.md) for Azure Cosmos DB は、クラウド ネイティブのハイブリッド トランザクションと分析処理 (HTAP) の機能です。これを使用すると、オペレーション データの準リアルタイム分析を実行できます。 Synapse Link によって、Azure Cosmos DB と Azure Synapse Analytics の間で緊密でシームレスな統合が作成されます。

オペレーション データの準リアルタイム分析を行うために、業界のユース ケースで、このクラウド ネイティブの HTAP 機能がどのように活用されているか興味を持たれるかもしれません。 Azure Synapse Link for Azure Cosmos DB の一般的なユース ケースは次の 3 つです。

* サプライ チェーンの分析、予測、およびレポート作成
* リアルタイム パーソナル化
* IOT シナリオでの予測メンテナンス、異常検出

> [!NOTE]
> Azure Synapse Link for Azure Cosmos DB は、エンタープライズ チームが準リアルタイム分析を実行することを検討しているシナリオを対象とします。 これらの分析は、Azure Cosmos DB で構築されたトランザクション アプリケーション間で生成されるオペレーション データに対して ETL なしで実行されます。 これは、ワークロード管理、高い同時実行性、複数のデータ ソース間での永続的集計など、従来のデータ ウェアハウスの要件がある場合に、個別のデータ ウェアハウスの必要性に取って代わるものではありません。

## <a name="supply-chain-analytics-forecasting--reporting"></a>サプライ チェーンの分析、予測、およびレポート作成

サプライ チェーン運用にビッグ データ分析を組み込むと、注文から配送までのサイクルの時間が短縮され、サプライ チェーンが効率化されることが調査研究によってわかっています。

製造元は、従来のエンタープライズ リソース プランニング (ERP) およびサプライ チェーン管理 (SCM) のシステムの制約を解消するために、クラウド ネイティブのテクノロジに乗り換えています。 サプライ チェーンで 1 分あたりに生成されるオペレーション データ (注文、出荷、トランザクションのデータ) の量の増大に伴い、製造元にはオペレーション データベースが必要になります。 このオペレーション データベースは、データ量に対処できるように拡張する必要があります。また、時代を先取りするために、リアルタイムのコンテキスト インテリジェンスのレベルに到達するように分析プラットフォームに拡張する必要があります。

次のアーキテクチャは、クラウド ネイティブのオペレーション データベースとして Azure Cosmos DB を活用することの利点、およびサプライ チェーン分析で Synapse Link を活用することの利点を示しています。

:::image type="content" source="./media/synapse-link-use-cases/supply-chain-analytics.png" alt-text="サプライ チェーン分析での Azure Synapse Link for Azure Cosmos DB" border="false":::

従来のアーキテクチャに基づき、Synapse Link for Azure Cosmos DB を使用して次のユース ケースを実現できます。

* **予測パイプラインの準備とトレーニング:** 機械学習による変換を使用して、サプライ チェーン全体のオペレーション データに関する分析情報を生成します。 これにより、在庫および運用コストを削減でき、顧客の注文から配送までの時間を短縮できます。

  Synapse Link を使用すると、手動による ETL プロセスを使用せずに Azure Cosmos DB で変化するオペレーション データを分析できます。 これにより、追加コストが削減され、遅延がなくなり、運用の複雑さが軽減されます。 Synapse Link を使用すると、データ エンジニアやデータ サイエンティストは、堅牢な予測パイプラインを構築できます。

  * Azure Synapse Analytics で Apache Spark プールとのネイティブ統合を活用して、Azure Cosmos DB 分析ストアからオペレーション データのクエリを実行します。 複雑な Data Engineering を行わずに、対話型のノートブックまたはスケジュール済みのリモート ジョブでデータのクエリを実行できます。

  * Azure Synapse Analytics で Spark ML アルゴリズムと Azure ML 統合を使用して、機械学習 (ML) モデルを構築します。

  * 準リアルタイムの運用スコアリングのために、モデル推論後の結果を Azure Cosmos DB に書き戻します。

* **運用レポート:** サプライ チェーン チームは、リアルタイムの正確なオペレーション データに関する柔軟なカスタム レポートを必要とします。 これらのレポートは、サプライ チェーンの有効性、収益性、および生産性のスナップショット ビューを取得するために必要です。 これにより、データ アナリストおよび他の重要な関係者は、絶えずビジネスを見直して、運用コストを削減するために調整する領域を特定できます。 

  Synapse Link for Azure Cosmos DB では、高度なビジネス インテリジェンス (BI)/レポート シナリオを利用できます。

  * サーバーレス SQL プールとのネイティブ統合と T-SQL 言語の豊富な表現力を使用して、Azure Cosmos DB 分析ストアからオペレーション データのクエリを実行します。

  * サーバーレス SQL プールによる使い慣れた BI ツールのサポートを通じて、Azure Cosmos DB の BI ダッシュボードの自動更新をモデル化し、発行します。 たとえば、Azure Analysis Services、Power BI Premium などです。

次に、Azure Cosmos DB へのバッチおよびストリーミング データのデータ統合に関するガイダンスを示します。

* **バッチ データの統合およびオーケストレーション:** サプライ チェーンの複雑化に伴い、サプライ チェーン データ プラットフォームをさまざまなデータ ソースおよび形式と統合することが必要になります。 Azure Synapse には、Azure Data Factory と同じデータ統合エンジンとエクスペリエンスが組み込まれています。 この統合により、データ エンジニアは、別のオーケストレーション エンジンを使用せずに高度なデータ パイプラインを作成できます。

  * サポートされている 85 以上のデータ ソースから、[Azure Data Factory を備えた Azure Cosmos DB](../data-factory/connector-azure-cosmos-db.md) にデータを移行します。

  * コードを使用しない ETL パイプラインを Azure Cosmos DB に書き込みます。これには、[マッピング データ フローを使用した関係から階層へのマッピングおよび階層から階層へのマッピング](../data-factory/how-to-sqldb-to-cosmosdb.md)が含まれます。

* **ストリーミング データの統合と処理:** 産業用 IoT ("フロアからストア" まで資産を追跡するセンサー、コネクテッド ロジスティクス フリートなど) の成長により、ストリーミング方式で生成されるリアルタイム データは爆発的に増えています。分析情報を生成するために、これらのデータを、従来の動きの遅いデータと統合する必要があります。 Azure Stream Analytics は、[幅広いシナリオ](../stream-analytics/streaming-technologies.md)を使用した Azure でのストリーミング ETL と処理のための推奨サービスです。 Azure Stream Analytics は、[ネイティブ データ シンクとして Azure Cosmos DB](../stream-analytics/stream-analytics-documentdb-output.md) をサポートしています。

## <a name="real-time-personalization"></a>リアルタイム パーソナル化

小売業者は、現在、顧客と企業の両方の需要を満たす、セキュリティで保護されたスケーラブルな eコマース ソリューションを構築する必要があります。 これらの eコマース ソリューションで、カスタマイズされた製品やオファーによって顧客を引き込み、取引を迅速かつ安全に処理し、フルフィルメントと顧客サービスに集中する必要があります。 最新の Synapse Link for Azure Cosmos DB と共に Azure Cosmos DB を使用することで、小売業者は、顧客向けにパーソナル化されたレコメンデーションをリアルタイムで生成できます。 次のアーキテクチャに示すように、小売業者は、低待機時間と調整可能な整合性の設定を使用して、ただちに分析情報を入手できます。

:::image type="content" source="./media/synapse-link-use-cases/real-time-personalization.png" alt-text="リアルタイム パーソナル化での Azure Synapse Link for Azure Cosmos DB" border="false":::

Synapse Link for Azure Cosmos DB のユース ケース:

* **予測パイプラインの準備とトレーニング:** Synapse Spark と機械学習モデルを使用して、事業単位または顧客セグメント全体のオペレーション データの分析情報を生成できます。 これを、エンドユーザーの要件に合わせて、顧客セグメント、予測エンドユーザー エクスペリエンス、およびターゲット マーケティングを目的としたパーソナル化された配信に変換します。

## <a name="iot-predictive-maintenance"></a>IOT 予測メンテナンス

産業用 IOT のイノベーションによって、機械装置のダウンタイムが大幅に削減され、業界のすべての分野で全体的な効率が向上しています。 このようなイノベーションの 1 つは、クラウドのエッジ部分にある機械装置の予測メンテナンス分析です。

次のアーキテクチャでは、IoT 予測メンテナンスで Azure Synapse Link for Azure Cosmos DB のクラウド ネイティブ HTAP 機能を活用しています。

:::image type="content" source="./media/synapse-link-use-cases/iot-predictive-maintenance.png" alt-text="IOT 予測メンテナンスでの Azure Synapse Link for Azure Cosmos DB" border="false" :::

Synapse Link for Azure Cosmos DB のユース ケース:

* **予測パイプラインの準備とトレーニング:** IoT デバイス センサーからの過去のオペレーション データを使用して、異常検出などの予測モデルをトレーニングできます。 これらの異常検出は、リアルタイム監視のためにエッジ部分に再びデプロイされます。 このような好循環によって、予測モデルを継続的に再トレーニングできます。

* **運用レポート:** デジタル ツイン イニシアティブの成長により、企業は多数のセンサーから大量のオペレーション データを収集し、各マシンのデジタル コピーを作成しています。 このデータを利用して、最新のホット データに対するリアルタイム アプリケーションに加えて、これまでのデータの傾向を理解するという BI ニーズに応えます。

## <a name="sample-scenario-htap-for-azure-cosmos-db"></a>サンプル シナリオ: Azure Cosmos DB の HTAP

約 10 年にわたって、エラスティックなスケーリング、ターンキー グローバル分散、マルチリージョン書き込みレプリケーションを必要とするミッション クリティカルなアプリケーションに、多くの顧客が Azure Cosmos DB を使用してきました。その目的は、トランザクション ワークロードでの読み取りと書き込みの両方の遅延を少なくし、可能性を高めることです。
 
次の一覧に、Azure Cosmos DB を使用したオペレーション データでサポートされるさまざまなワークロード パターンの概要を示します。

* リアルタイム アプリおよびサービス
* イベント ストリーム処理
* BI ダッシュボード
* ビッグ データ分析
* 機械学習

Azure Synapse Link を使用すると、Azure Cosmos DB でトランザクション ワークロードを強化できるだけでなく、過去のオペレーション データの準リアルタイム分析ワークロードを実行できます。 これは ETL の要件なしで実行され、トランザクション ワークロードからのパフォーマンスの分離が保証されます。

次の図は、Azure Cosmos DB を使用したワークロード パターンを示しています。:::image type="content" source="./media/synapse-link-use-cases/synapse-link-workload-patterns.png" alt-text="Azure Synapse Link for Azure Cosmos DB のワークロード パターン" border="false":::

ここでは、20 の国/地域にわたってグローバルな運用を行っている eコマース企業 CompanyXYZ の例を示し、在庫管理プラットフォームのトランザクションと分析の両方の要件に対応する単一のリアルタイム データベースとして Azure Cosmos DB を選択することの利点を示します。

* CompanyXYZ の中核ビジネスは在庫管理システムに依存しています。このため、可用性と信頼性が中核となる要件です。 Azure Cosmos DB を使用することの利点:

  * Azure インフラストラクチャとの緊密な統合と、透過的なマルチリージョン書き込みグローバル レプリケーションにより、Azure Cosmos DB では、リージョン障害に対して業界最高水準の [99.999% の高可用性](high-availability.md)が実現されます。

* CompanyXYZ のサプライ チェーン パートナーは、地理的に離れた場所に存在しますが、各国での運用をサポートするために、世界中の製品在庫を 1 つのビューで表示しなければならない場合があります。 これには、他のサプライ チェーン パートナーが行った更新をリアルタイムで読み取ることができるという要件も含まれます。 また、他のパートナーとの競合を心配することなく、高いスループットで更新を実行できる必要もあります。 Azure Cosmos DB を使用することの利点:

  * Azure Cosmos DB では、その固有のマルチリージョン書き込みレプリケーション プロトコルと、ラッチフリーの書き込みに最適化されたトランザクション ストアにより、世界中のどこでも、99 パーセンタイルでのインデックス付きの読み取りと書き込みの両方の待機時間が 10 ミリ秒未満となることが保証されます。

  * バッチ データとストリーミング データの両方の高スループットのインジェストが、トランザクション ストアでの[リアルタイムのインデックス作成](index-policy.md)により強化されます。

  * Azure Cosmos DB のトランザクション ストアは、ビジネス ニーズに最も近い[可用性とパフォーマンスのトレードオフ](./consistency-levels.md)を実現するために、強力かつ最終的な整合性レベルという利点に加えて 3 つのオプションを提供しています。

* CompanyXYZ のサプライ チェーン パートナーでは、1 秒あたりの要求数が数百から数百万の範囲で大幅に変動するというトラフィック パターンがあるため、在庫管理プラットフォームでトラフィックの予期しない急増に対処する必要があります。  Azure Cosmos DB を使用することの利点:

  * Azure Cosmos DB のトランザクション ストアは、行方向のパーティション分割を使用して、ストレージとスループットの柔軟なスケーラビリティをサポートします。 オートパイロット モードで構成されたコンテナーとデータベースは、ワークロードの全体的な可用性、待機時間、スループット、パフォーマンスに影響を与えることなく、アプリケーションのニーズに基づいて、プロビジョニングされたスループットを自動的かつ即座にスケーリングできます。

* CompanyXYZ は、サプライ チェーン パートナー、事業単位、および職務に関する分析を行い、コグニティブな分析情報を得るために、システム全体のこれまでのインベントリ データを格納する安全な分析プラットフォームを確立する必要があります。 分析プラットフォームでは、システム全体のコラボレーション、従来の BI/レポート作成のユース ケース、高度な分析のユース ケース、およびオペレーション インベントリ データに対する予測インテリジェント ソリューションを使用できるようにする必要があります。 Synapse Link for Azure Cosmos DB を使用することの利点:

  * Synapse Link では、完全に分離された列ストアである [Azure Cosmos DB 分析ストア](analytical-store-introduction.md)を使用することで、世界各地に分散したオペレーション データに対して、[Azure Synapse Analytics](../synapse-analytics/overview-what-is.md) で抽出、変換、読み込み (ETL) なしの分析を大規模に実行できます。  ビジネス アナリスト、データ エンジニア、およびデータ サイエンティストは、現在、Synapse Spark または Synapse SQL を相互運用可能な方法で使用できます。これにより、Azure Cosmos DB でのトランザクション ワークロードのパフォーマンスに影響を与えることなく、準リアルタイムのビジネス インテリジェンス、分析、および機械学習のパイプラインを実行できます。 詳細については、[Azure Cosmos DB での Synapse Link の利点](synapse-link.md)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

詳しく学習するために、次のドキュメントを参照してください。

* [Azure Synapse Link for Azure Cosmos DB](synapse-link.md) 

* [Azure Cosmos DB 分析ストア](analytical-store-introduction.md)

* [Azure Synapse Link for Azure Cosmos DB の操作](configure-synapse-link.md)

* [Azure Synapse Link for Azure Cosmos DB についてよく寄せられる質問](synapse-link-frequently-asked-questions.md)

* [Azure Synapse Analytics での Apache Spark](../synapse-analytics/spark/apache-spark-concepts.md)

* [Azure Synapse Analytics のサーバーレス SQL プール ランタイム サポート](../synapse-analytics/sql/on-demand-workspace-overview.md)