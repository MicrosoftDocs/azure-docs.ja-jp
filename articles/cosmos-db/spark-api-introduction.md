---
title: Azure Cosmos DB での Apache Spark を使用した組み込み運用分析の概要
description: Azure Cosmos DB で Apache Spark の組み込みサポートを利用して運用分析と AI を実行する方法について説明します
ms.service: cosmos-db
ms.topic: overview
ms.date: 05/23/2019
author: rimman
ms.author: rimman
ms.openlocfilehash: 245df0632765c4000bdf5da3e428187d2b068866
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2019
ms.locfileid: "68402118"
---
# <a name="built-in-operational-analytics-in-azure-cosmos-db-with-apache-spark-preview"></a>Azure Cosmos DB での Apache Spark を使用した組み込み運用分析 (プレビュー) 

Azure Cosmos DB での Apache Spark の組み込みサポートにより、Azure Cosmos アカウントに保存されているデータに対する分析を Apache Spark から実行できます。 また、グローバルに分散された Cosmos データベースで Apache Spark ジョブを直接実行する処理がネイティブにサポートされます。 このような機能があることで、開発者、データ エンジニア、データ サイエンティストは **OLTP と OLAP/HTAP** という両方のワークロードを実行する柔軟性、拡張性、パフォーマンスに優れたデータ プラットフォームとして Azure Cosmos DB を使用できます。 

Spark コンピューティングは、Azure Cosmos アカウントに関連付けられたすべての Azure リージョンで自動的に使用できます。 Spark ジョブは、Azure Cosmos DB のマルチマスター機能を使用し、各リージョンでのローカル レプリカに対して書き込みやクエリを行えます。 

> [!NOTE]
> 現在、Azure Cosmos DB での Apache Spark の組み込みサポートは、制限付きのプレビュー段階です。 プレビューに申し込むには、[プレビューのためのサインアップ ページ](https://portal.azure.com/?feature.customportal=false#create/Microsoft.DocumentDB)に移動してください。 

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

### <a name="globally-distributed-low-latency-operational-analytics-and-ai"></a>世界中に分散され、待ち時間の短い運用分析と AI

世界中に分散されている Azure Cosmos データベースの Apache Spark を利用することで、世界中の分析情報を短時間で得ることができるようになりました。 Azure Cosmos DB は、3 つの主要な手法により、**世界中に分散された待ち時間の短い運用分析**をエラスティックなスケールで可能にします。

* Azure Cosmos データベースは世界中に分散しているため、データはすべてローカルで、すなわち、データのプロデューサー (ユーザーなど) がいる場所で取り込まれます。 クエリはデータのプロデューサーとコンシューマーの所在地に関係なく、両方にとって最も近いローカル レプリカに対して実行されます。 

* 分析クエリはすべて、データ パーティション内に保管されているインデックス付きデータで直接実行されています。不要なデータ移動は必要ありません。 

* Spark は Azure Cosmos DB と併設されるため、中間的な変換とデータ移動が少なくなります。その結果、パフォーマンスと拡張性が向上します。

### <a name="unified-serverless-experience-for-apache-spark"></a>Apache Spark の統一サーバーレス体験

マルチモデル データベースの Azure Cosmos DB では、OSS API 向けのサポートが拡張されました。**Apache Spark の統一サーバーレス体験**にキー/値、ドキュメント、グラフ、列ファミリー データ モデルが与えられるようになりました。 MongoDB、Cassandra、Gremlin、Etcd、SQL API を利用することで、さまざまなデータ モデルがサポートされます。すべて同じ基礎データ上で動作します。 

Azure Cosmos DB での Apache Spark のサポートにより、Scala、Python、Java で記述されたアプリケーションをネイティブにサポートできるほか、SQL 用に緊密に統合された複数のライブラリを使用できます。 そのようなライブラリには、([Spark SQL](https://spark.apache.org/sql/))、機械学習 (Spark [MLlib](https://spark.apache.org/mllib/))、ストリーム処理 ([Spark Structured Streaming](https://spark.apache.org/streaming/))、グラフ処理 (Spark [GraphFrames]( https://docs.databricks.com/spark/latest/graph-analysis/graphframes/user-guide-python.html)) があります。 これらのツールにより、さまざまなユース ケースに Apache Spark を簡単に使用できます。 Spark や Spark クラスターを扱う必要はありません。 同じ基礎データ上で同時に、使い慣れた Apache Spark API と **Jupyter ノートブック**を分析と SQL API に、Cassandra など、OSS NoSQL API をトランザクション処理に使用できます。

### <a name="no-schema-or-index-management"></a>スキーマやインデックスの管理が不要

従来の分析データベースとは異なり、Azure Cosmos DB の場合、データ エンジニアやデータ サイエンティストは面倒なスキーマやインデックス管理を扱う必要がなくなります。 Azure Cosmos DB のデータベース エンジンは、明示的なスキーマやインデックス管理を必要としません。また、Apache Spark のクエリにすばやくサービスを提供するために取り込むすべてのデータに自動的にインデックスを付けることができます。 

### <a name="consistency-choices"></a>整合性の選択肢

Apache Spark ジョブは Azure Cosmos データベースのデータ パーティションで実行されるため、クエリには [5 つの明確に定義された整合性選択肢](consistency-levels.md)が与えられます。 このような整合性モデルには柔軟性があり、厳密な整合性を選択すれば、待ち時間と高可用性で妥協することなく、機械学習アルゴリズムで最も精確な結果を提供できます。 

### <a name="comprehensive-slas"></a>包括的な SLA

Apache Spark ジョブには、業界をリードする包括的な [SLA](https://azure.microsoft.com/support/legal/sla/documentdb/v1_1/) (99.999) など、Azure Cosmos DB の長所が与えられ、個々の Apache Spark クラスターを管理するというオーバーヘッドがありません。 このような SLA には、スループット、99 パーセンタイルの待ち時間、整合性、高可用性が含まれます。 

### <a name="mixed-workloads"></a>ワークロードの混在

Apache Spark を Azure Cosmos DB に統合することで、分離していたトランザクションと分析がつながります。この分離は、クラウドネイティブのアプリケーションを世界規模で構築するとき、顧客にとって大きな難点の 1 つでした。 

## <a name="scenarios-for-azure-cosmos-db-spark-support"></a>Azure Cosmos DB の Spark サポートのシナリオ

### <a name="retail-and-consumer-goods"></a>小売/一般消費財

Azure Cosmos DB の Spark サポートを使用して、リアルタイムのおすすめ候補とプランを提供できます。 リアルタイムのパーソナル化と製品のおすすめ候補を使用して、顧客が必要とする項目を検出できるよう支援できます。

* Apache Spark ランタイムによって提供される組み込みの Machine Learning サポートを使用して、製品カタログ全体でリアルタイムのおすすめ候補を生成できます。

* ストリーム データ、購入データ、および顧客データをマイニングしてクリックし、有効期間の値を指定する対象を絞ったおすすめ候補を提供することができます。

* Azure Cosmos DB のグローバル配布機能を使用すると、複数のリージョンに分散されている大量の製品データをミリ秒単位で分析できます。

* 地理的に分散されているユーザーやデータに関する分析情報をすばやく得られます。 適切な広告を適切なユーザーに適切なタイミングで提供することにより、プロモーション変換率を向上させることができます。

* 組み込みの Spark ストリーミング機能を活用して、静的な顧客データと組み合わせてライブ データを強化することができます。 こうすることで、顧客が何をしているかに合わせて、よりパーソナライズされた広告や対象を絞った広告をリアルタイムで配信できます。

次の図は Azure Cosmos DB Spark サポートを使用して価格とプロモーションを最適化する方法を示しています。

![価格とプロモーションを最適化するための Azure Cosmos DB の Spark サポート](./media/spark-api-introduction/optimize-pricing-and-promotions.png)


次の図は Azure Cosmos DB Spark サポートをリアルタイムのおすすめ候補エンジンで使用する方法を示しています。

![リアルタイムのおすすめ候補エンジンでの Azure Cosmos DB Spark サポート](./media/spark-api-introduction/real-time-recommendation-engine.png)

### <a name="manufacturing-and-iot"></a>製造と IoT

Azure Cosmos DB の組み込みの分析プラットフォームを使用すると、世界規模で何百万ものデバイスからの IoT データをリアルタイムに分析できます。 気象パターンの予測、予測分析、エネルギーの最適化など、最新のイノベーションを行うことができます。

* Azure Cosmos DB を使用すると、リアルタイム資産のメトリックや気象要因などのデータをマイニングし、スマート グリッド分析を適用して、フィールドで接続されているデバイスのパフォーマンスを最適化することができます。 スマート グリッド分析は、運用コストを制御し、グリッドの信頼性を向上させ、パーソナライズされたエネルギー サービスをコンシューマーに提供するための鍵です。

次の図は、IoT デバイスからメトリックを読み取り、スマート グリッド分析を適用するために Azure Cosmos DB の Spark サポートを使用する方法を示しています。

![IoT デバイスからメトリックを読み取るための Azure Cosmos DB の Spark サポート](./media/spark-api-introduction/read-metrics-from-iot-devices.png)


### <a name="predictive-maintenance"></a>予測的なメンテナンス

* 小規模なドリル マシンで使用される圧縮機のような資産を、深い水中のプラットフォームに維持することは複雑な作業です。 これらの資産は世界各地に配置され、ペタバイト単位のデータを生成します。 Azure Cosmos DB を使用すると、エンドツーエンドの予測データ パイプラインを構築できます。このパイプラインでは、Spark ストリーミングを使用して大量のセンサー テレメトリを処理し、資産パーツを保存し、マッピング データを検出します。

* 機械学習モデルを構築およびデプロイして、資産の故障を発生前に予測し、障害が発生する前にメンテナンス作業を命令することができます。

次の図は Azure Cosmos DB の Spark サポートを使用して予測メンテナンス システムを構築する方法を示しています。

![予測メンテナンス システムを構築するための Azure Cosmos DB の Spark サポート](./media/spark-api-introduction/predictive-maintenance-system.png)

次の図は Azure Cosmos DB の Spark サポートを使用してリアルタイムの車両診断システムを構築する方法を示しています。

![リアルタイムの車両診断システムを構築するための Azure Cosmos DB の Spark サポート](./media/spark-api-introduction/real-time-vehicle-diagnostic-system.png)

### <a name="gaming"></a>Gaming

* Azure Cosmos DB では、組み込みの Spark サポートにより、高度な分析および機械学習モデルを数分で簡単にビルド、スケーリング、デプロイできるので、最適なゲーム エクスペリエンスをビルドできます。

* プレーヤー、購入、行動データを分析して、関連するパーソナライズされたプランを作成し、高いコンバージョン率を得ることができます。

* Spark 機械学習を使用して、ゲーム テレメトリ データを分析し、分析情報を得ることができます。 低速の読み込み時間とゲーム中の問題を診断して防止できます。

次の図は Azure Cosmos DB の Spark サポートをゲーム分析で使用する方法を示しています。

![ゲーム分析での Azure Cosmos DB の Spark サポート](./media/spark-api-introduction/gaming-analytics.png)

## <a name="built-in-jupyter-notebooks-support"></a>組み込み Jupyter Notebook のサポート

Azure Cosmos DB では、Cassandra、MongoDB、SQL、Gremlin、テーブルなど、すべての API の組み込み Jupyter Notebook がサポートされています。 Jupyter Notebook は Azure Cosmos アカウント内で実行され、これにより開発者エクスペリエンスが向上されます。 組み込み Notebook がすべての Azure Cosmos DB API とデータ モデルをサポートしているので、対話形式でクエリを実行できます。 機械学習モデルを実行し、Azure Cosmos データベースに格納されたデータを分析することもできます。 Jupyter Notebook エクスペリエンスを使用することにより、格納されたデータを分析し、機械学習モデルをビルドおよびトレーニングし、次の図に示すように Azure portal のデータに対して推論を実行できます。

![Azure Cosmos DB における Jupyter Notebook のサポート](./media/spark-api-introduction/jupyter-notebooks-portal.png)

## <a name="next-steps"></a>次の手順

* Azure Cosmos DB の長所については、[こちら](introduction.md)の概要の記事をご覧ください。
* [MongoDB 用の Azure Cosmos DB API を使ってみる](mongodb-introduction.md)
* [Azure Cosmos DB Cassandra API を使ってみる](cassandra-introduction.md)
* [Azure Cosmos DB Gremlin API を使ってみる](graph-introduction.md)
* [Azure Cosmos DB Table API を使ってみる](table-introduction.md)




