---
title: Azure Cosmos DB での Apache Spark を使用した組み込み運用分析の概要
description: Azure Cosmos DB で Apache Spark の組み込みサポートを利用して運用分析と AI を実行する方法について説明します
ms.service: cosmos-db
ms.topic: overview
ms.date: 09/26/2019
author: rimman
ms.author: rimman
ms.openlocfilehash: 952dd084bccd67a0a8833002d73d3aaf1d5dfb25
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338627"
---
# <a name="built-in-operational-analytics-in-azure-cosmos-db-with-apache-spark"></a>Azure Cosmos DB での Apache Spark を使用した組み込み運用分析

Azure Cosmos DB を使用すると、グローバルに分散された低遅延の分析と AI をトランザクション データに対して実行できます。 Azure Cosmos DB は、Apache Spark と Jupyter ノートブックのネイティブ サポートにより、分析情報が短時間で得られるようになっています。 データの取り込みと処理、分析が、Azure リージョン内のローカル データベース レプリカに対して実行されるためです。 お使いのデータ パーティションに格納されているインデックス付きマルチモデル データに、Apache Spark クエリを直接実行することができます。

Azure Cosmos DB での Apache Spark の組み込みサポートにより、Azure Cosmos アカウントに保存されているデータに対する分析を Apache Spark から実行できます。 また、グローバルに分散された Cosmos データベースで Apache Spark ジョブを直接実行する処理がネイティブにサポートされます。 このような機能があることで、開発者、データ エンジニア、データ サイエンティストは **OLTP と OLAP/HTAP** という両方のワークロードを実行する柔軟性、拡張性、パフォーマンスに優れたデータ プラットフォームとして Azure Cosmos DB を使用できます。

Azure Cosmos DB での Apache Spark のサポートには、次の利点があります。

* 地理的に分散されているユーザーやデータに関する分析情報を最短で得られます。

* ソリューションのアーキテクチャをシンプルにして[総保有コスト](total-cost-ownership.md) (TCO) を下げることができます。 システムに与えられるデータ処理コンポーネントの数は最小限であり、その間で不要なデータ移動が回避されます。

* [セキュリティ](secure-access-to-data.md)、[コンプライアンス](compliance.md)、すべてのデータを管理下に置く監査境界が作成されます。

* 厳格な SLA に支援される "常時オン" あるいは[可用性の高い](high-availability.md)エンドユーザー分析が提供されます。

![Azure Cosmos DB の視覚化における Apache Spark のサポート](./media/spark-api-introduction/spark-api-visualization.png)
 
Azure Cosmos DB での Apache Spark のサポートを利用することで、AI とディープ ラーニングのモデル、予測分析、おすすめ候補、IoT、カスタマー 360、詐欺検出、テキスト センチメント、クリックストリーム分析などのソリューションを構築してデプロイできます。 これらのソリューションは Azure Cosmos DB データに対して直接動作します。

データベース サービスの外側に出たり、コンピューティング サービスを追加したりする必要なく、Azure Cosmos DB でバッチおよびストリーミング ETL ジョブを設定できます。 ETL ジョブを実行し、ジョブの完了時、規模を戻す必要があるとき、コンピューティング環境の規模をエラスティックに変更できます。

Azure Cosmos DB での Apache Spark のサポートでは、Apache Spark ランタイムで組み込みの機械学習がサポートされます。 このランタイムには、Spark MLLib、Microsoft Machine Learning for Spark、Azure Machine Learning、Cognitive Services が含まれています。 このような機能により、データ サイエンティスト、データ エンジニア、データ アナリストは、短い時間と低いコストで、Azure Cosmos DB 内に直接、機械学習モデルを構築し、運用できます。


## <a name="key-benefits"></a>主な利点

### <a name="low-latency-operational-analytics-and-ai"></a>低遅延の運用分析と AI

世界中に分散されている Azure Cosmos データベースの Apache Spark を利用することで、世界中の分析情報を短時間で得ることができるようになりました。 Azure Cosmos DB は、3 つの主要な手法により、**世界中に分散された待ち時間の短い運用分析**をエラスティックなスケールで可能にします。

* Azure Cosmos データベースは世界中に分散しているため、データはすべてローカルで、すなわち、データのプロデューサー (ユーザーなど) がいる場所で取り込まれます。 クエリはデータのプロデューサーとコンシューマーの所在地に関係なく、両方にとって最も近いローカル レプリカに対して実行されます。

* 分析クエリはすべて、データ パーティション内に保管されているインデックス付きデータで直接実行されています。不要なデータ移動は必要ありません。

* Spark は Azure Cosmos DB と併設されるため、中間的な変換とデータ移動が少なくなります。その結果、パフォーマンスと拡張性が向上します。

* Azure Cosmos DB に組み込まれた Apache Spark から、マルチマスター、自動フェールオーバー、可用性ゾーンなど、Azure Cosmos DB の主要な機能がすべて利用できます。

### <a name="unified-serverless-experience-for-apache-spark"></a>Apache Spark の統一サーバーレス体験

マルチモデル データベースの Azure Cosmos DB では、OSS API 向けのサポートが拡張されました。**Apache Spark の統一サーバーレス体験**にキー/値、ドキュメント、グラフ、列ファミリー データ モデルが与えられるようになりました。 MongoDB、Cassandra、Gremlin、Etcd、SQL API を利用することで、さまざまなデータ モデルがサポートされます。すべて同じ基礎データ上で動作します。 

Azure Cosmos DB での Apache Spark のサポートにより、Scala、Python、Java で記述されたアプリケーションをネイティブにサポートできるほか、SQL 用に緊密に統合された複数のライブラリを使用できます。 そのようなライブラリには、([Spark SQL](https://spark.apache.org/sql/))、機械学習 (Spark [MLlib](https://spark.apache.org/mllib/))、ストリーム処理 ([Spark Structured Streaming](https://spark.apache.org/streaming/))、グラフ処理 (Spark [GraphFrames]( https://docs.databricks.com/spark/latest/graph-analysis/graphframes/user-guide-python.html)) があります。 これらのツールにより、さまざまなユース ケースに Apache Spark を簡単に使用できます。 Spark や Spark クラスターを扱う必要はありません。 同じ基礎データ上で同時に、使い慣れた Apache Spark API と **Jupyter ノートブック**を分析と SQL API に、Cassandra など、OSS NoSQL API をトランザクション処理に使用できます。

### <a name="no-schema-or-index-management"></a>スキーマやインデックスの管理が不要

従来の分析データベースとは異なり、Azure Cosmos DB の場合、データ エンジニアやデータ サイエンティストは面倒なスキーマやインデックス管理を扱う必要がなくなります。 Azure Cosmos DB のデータベース エンジンは、明示的なスキーマやインデックス管理を必要としません。また、Apache Spark のクエリにすばやくサービスを提供するために取り込むすべてのデータに自動的にインデックスを付けることができます。

### <a name="consistency-choices"></a>整合性の選択肢

Apache Spark ジョブは Azure Cosmos データベースのデータ パーティションで実行されるため、クエリには [5 つの明確に定義された整合性選択肢](consistency-levels.md)が与えられます。 このような整合性モデルには柔軟性があり、厳密な整合性を選択すれば、待ち時間と高可用性で妥協することなく、機械学習アルゴリズムで最も精確な結果を提供できます。

### <a name="comprehensive-slas"></a>包括的な SLA

Apache Spark ジョブには、業界をリードする包括的な [SLA](https://azure.microsoft.com/support/legal/sla/documentdb/v1_1/) (99.999) など、Azure Cosmos DB の長所が与えられ、個々の Apache Spark クラスターを管理するというオーバーヘッドがありません。 このような SLA には、スループット、99 パーセンタイルの待ち時間、整合性、高可用性が含まれます。 

### <a name="mixed-operational-and-analytical-workloads-with-complete-isolation"></a>運用ワークロードと分析ワークロードが完全に分離した状態で混在

Apache Spark を Azure Cosmos DB に統合することで、分離していたトランザクションと分析がつながります。この分離は、クラウドネイティブのアプリケーションを世界規模で構築するとき、顧客にとって大きな難点の 1 つでした。 OLTP ワークロードと OLAP ワークロードはサイド バイ サイドで実行され、互いに干渉し合うことがありません。

### <a name="low-latency-analytical-and-transactional-storage"></a>低遅延の分析ストレージとトランザクショナル ストレージ

Azure Cosmos DB はネイティブで、トランザクショナル (行指向) ストレージと分析ストレージ (列指向、Apache Parquet ファイル形式) という 2 つの別個のストレージ層にデータを格納します。 データはそれぞれの層でグローバルにレプリケートされ、ユーザーは、それらの層のデータをアイテム保持ポリシーに基づいて別々に管理することができます。

このストレージ アーキテクチャでは、グローバルに分散された同じデータに対して、ミッション クリティカルなトランザクショナル ワークロードと分析ワークロードの両方を使用することができます。 Cosmos DB を使えば、ETL 操作は必要ありません。また、不要なデータ移動を実行する必要もありません。 基になる同じデータに対して、そのままトランザクショナル ワークロードと分析ワークロードの両方を実行することができます。 トランザクショナル ワークロードでは、SQL、Cassandra、MongoDB、Gremlin、Etcd など、使い慣れたトランザクショナル アクセス API を使用できます。 一方、分析ワークロードおよび AI ワークロードでは、組み込みの Apache Spark SQL、ML フレームワークのほか、Apache Spark のツールチェーン全体を使用することができます。

### <a name="snapshots-and-historical-analytical-queries"></a>スナップショットと履歴分析クエリ

分析層に格納された列指向の圧縮データからオンデマンドのスナップショットまたはスケジュールに基づくスナップショットを作成し、特定のスナップショットに対して直接、分析クエリを実行することができます。 この機能により、フラッシュバック分析クエリやタイムトラベル分析クエリ、ロールバック、全履歴の監査証跡、再現可能な機械学習および AI 実験が可能となります。

## <a name="next-steps"></a>次の手順

* Azure Cosmos DB の長所については、[こちら](introduction.md)の概要の記事をご覧ください。
* [MongoDB 用の Azure Cosmos DB API を使ってみる](mongodb-introduction.md)
* [Azure Cosmos DB Cassandra API を使ってみる](cassandra-introduction.md)
* [Azure Cosmos DB Gremlin API を使ってみる](graph-introduction.md)
* [Azure Cosmos DB Table API を使ってみる](table-introduction.md)