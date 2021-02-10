---
title: Databricks (Spark) を使用して Apache Cassandra から Azure Cosmos DB Cassandra API にデータを移行する
description: Azure Databricks と Spark を使用して、Apache Cassandra データベースから Azure Cosmos DB Cassandra API にデータを移行する方法について説明します。
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 11/16/2020
ms.author: thvankra
ms.reviewer: thvankra
ms.openlocfilehash: 3cbcb7eb3695e6f57daef741d4cd4b15577d8f58
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493276"
---
# <a name="migrate-data-from-cassandra-to-azure-cosmos-db-cassandra-api-account-using-azure-databricks"></a>Azure Databricks を使用して Cassandra から Azure Cosmos DB Cassandra API アカウントにデータを移行する
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Azure Cosmos DB の Cassandra API は、次のようなさまざまな理由により、Apache Cassandra 上で実行されているエンタープライズ ワークロードに適した選択肢になりました。 

* **管理と監視のオーバーヘッドなし:** OS、JVM、および yaml ファイルやそれらの相互作用での無数の設定を管理および監視する際のオーバーヘッドが解消されます。

* **大幅なコスト削減:** Azure Cosmos DB によってコストを節約できます。これには、VM、帯域幅、適用されるすべてのライセンスのコストが含まれます。 さらに、データ センター、サーバー、SSD ストレージ、ネットワーク、電気代を管理する必要がありません。 

* **既存のコードとツールを使用可能:** Azure Cosmos DB では、既存の Cassandra SDK およびツールとのワイヤ プロトコル レベルの互換性が提供されます。 この互換性により、Azure Cosmos DB の Cassandra API を少し変更するだけで、既存のコードベースを使用できることが保証されます。

データベースのワークロードをプラットフォーム間で移行するには、さまざまな方法があります。 [Azure Databricks](https://azure.microsoft.com/services/databricks/) は、[Apache Spark](https://spark.apache.org/) 用の「サービスとしてのプラットフォーム」製品です。これにより、大規模なオフライン移行を実行できます。 この記事では、Azure Databricks を使用して、ネイティブ Apache Cassandra キースペース/テーブルから Azure Cosmos DB Cassandra API にデータを移行するために必要な手順について説明します。

## <a name="prerequisites"></a>[前提条件]

* [Azure Cosmos DB Cassandra API アカウントのプロビジョニング](create-cassandra-dotnet.md#create-a-database-account)

* [Azure Cosmos DB Cassandra API への接続の基本を確認する](cassandra-spark-generic.md)

* 互換性を確保するために「[Azure Cosmos DB Cassandra API でサポートされる機能](cassandra-support.md)」を確認する

* ターゲット Azure Cosmos DB Cassandra API アカウントに、空のキースペースとテーブルを既に作成していることを確認する

* [cqlsh またはホストされたシェルを使用して検証する](cassandra-support.md#hosted-cql-shell-preview)

## <a name="provision-an-azure-databricks-cluster"></a>Azure Databricks クラスターのプロビジョニング

手順に従って、[Azure Databricks クラスターをプロビジョニング](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal)できます。 ただし、現時点では、Apache Cassandra コネクタに対して Apache Spark 3. x がサポートされていないことに注意してください。 サポートされている Apache Spark v2.x バージョンを使用して、Databricks ランタイムをプロビジョニングする必要があります。 最新バージョンの Spark 2.x をサポートしており、Scala バージョンが 2.11 以内の Databricks ランタイム バージョンを選択することをお勧めします。

:::image type="content" source="./media/cassandra-migrate-cosmos-db-databricks/databricks-runtime.png" alt-text="Databricks ランタイム":::


## <a name="add-dependencies"></a>依存関係を追加する

Apache Spark Cassandra コネクタ ライブラリをクラスターに追加して、ネイティブ エンドポイントと Cosmos DB Cassandra エンドポイントの両方に接続する必要があります。 クラスターで、[ライブラリ] -> [新規インストール] -> [Maven] -> [パッケージの検索] を選択します。

:::image type="content" source="./media/cassandra-migrate-cosmos-db-databricks/databricks-search-packages.png" alt-text="Databricks 検索パッケージ":::

検索ボックスに「`Cassandra`」と入力し、使用可能な最新の `spark-cassandra-connector` Maven リポジトリを選択し、[インストール] を選択します。

:::image type="content" source="./media/cassandra-migrate-cosmos-db-databricks/databricks-search-packages-2.png" alt-text="Databricks パッケージの選択":::

> [!NOTE]
> 必ず、Cassandra コネクタ ライブラリがインストールされた後に Databricks クラスターを再起動してください。

## <a name="create-scala-notebook-for-migration"></a>移行用の Scala ノートブックを作成する

次のコードで Databricks に Scala ノートブックを作成します。 ソースとターゲットの Cassandra 構成を、対応する資格情報、ソース/ターゲットのキースペースおよびテーブルに置き換えてから、以下を実行します。

```scala
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql._
import org.apache.spark.SparkContext

// source cassandra configs
val nativeCassandra = Map( 
    "spark.cassandra.connection.host" -> "<Source Cassandra Host>",
    "spark.cassandra.connection.port" -> "9042",
    "spark.cassandra.auth.username" -> "<USERNAME>",
    "spark.cassandra.auth.password" -> "<PASSWORD>",
    "spark.cassandra.connection.ssl.enabled" -> "false",
    "keyspace" -> "<KEYSPACE>",
    "table" -> "<TABLE>"
)

//target cassandra configs
val cosmosCassandra = Map( 
    "spark.cassandra.connection.host" -> "<USERNAME>.cassandra.cosmos.azure.com",
    "spark.cassandra.connection.port" -> "10350",
    "spark.cassandra.auth.username" -> "<USERNAME>",
    "spark.cassandra.auth.password" -> "<PASSWORD>",
    "spark.cassandra.connection.ssl.enabled" -> "true",
    "keyspace" -> "<KEYSPACE>",
    "table" -> "<TABLE>",
    //throughput related settings below - tweak these depending on data volumes. 
    "spark.cassandra.output.batch.size.rows"-> "1",
    "spark.cassandra.connection.connections_per_executor_max" -> "10",
    "spark.cassandra.output.concurrent.writes" -> "1000",
    "spark.cassandra.concurrent.reads" -> "512",
    "spark.cassandra.output.batch.grouping.buffer.size" -> "1000",
    "spark.cassandra.connection.keep_alive_ms" -> "600000000"
)

//Read from native Cassandra
val DFfromNativeCassandra = sqlContext
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(nativeCassandra)
  .load
  
//Write to CosmosCassandra
DFfromNativeCassandra
  .write
  .format("org.apache.spark.sql.cassandra")
  .options(cosmosCassandra)
  .save
```

> [!NOTE]
> `spark.cassandra.output.batch.size.rows`、`spark.cassandra.output.concurrent.writes`、`connections_per_executor_max` の構成は、[レート制限](/samples/azure-samples/azure-cosmos-cassandra-java-retry-sample/azure-cosmos-db-cassandra-java-retry-sample/)を回避するために重要です。この制限は、Azure Cosmos DB への要求が、プロビジョニングされたスループット ([要求ユニット](./request-units.md)) を超えると発生します。 Spark クラスター内の実行プログラム数に応じて、これらの設定値の調整が必要になることがあります。また、ターゲット テーブルに書き込まれる各レコードのサイズ (および RU のコスト) によっても、調整が必要になる場合があります。

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="rate-limiting-429-error"></a>レート制限 (429 エラー)
上記の設定を最小値に減らしたにもかかわらず、エラー コード 429 または `request rate is large` エラー テキストが表示されることがあります。 そのようなシナリオをいくつか次に示します。

- **テーブルに割り当てられたスループットが 6000 [要求ユニット](./request-units.md)未満**。 最小設定でも、Spark は約 6000 要求ユニット以上のレートで書き込みを実行できます。 共有スループットがプロビジョニングされたキースペースでテーブルをプロビジョニングした場合、このテーブルの実行時に使用できる RU が 6000 未満である可能性があります。 移行を実行するときに、移行先のテーブルで少なくとも 6000 RU が使用可能であることを確認し、必要に応じて、そのテーブルに専用の要求ユニットを割り当てます。 
- **大規模なデータ ボリュームでの過度のデータ スキュー**。 特定のテーブルに移行するデータ (テーブル行) が大量にあるが、大きいデータ スキューがある場合 (つまり、同じパーティション キー値に対して大量のレコードが書き込まれている場合)、テーブルに大量の[要求ユニット](./request-units.md)がプロビジョニングされている場合でも、レート制限が発生する可能性があります。 これは、要求ユニットが物理パーティション間で均等に分割され、大量のデータ スキューによって 1 つのパーティションに対する要求のボトルネックが発生し、レート制限が発生するためです。 このシナリオでは、Spark の最小スループット設定を下げて、レート制限を回避し、移行の実行速度を強制的に低下させることをお勧めします。 このシナリオは、アクセスの頻度は低いが、スキューが大きくなる可能性がある参照または制御テーブルを移行する場合によく見られます。 ただし、その他の種類のテーブルに大きいスキューが存在する場合は、データ モデルを確認して、安定状態の操作中にワークロードのホット パーティションの問題を回避することをお勧めします。 
- **大きいテーブルでカウントを取得できない**。 `select count(*) from table` の実行は、現在、大きいテーブルではサポートされていません。 カウントは Azure portal のメトリックから取得できます ([トラブルシューティングに関する記事](cassandra-troubleshoot.md)を参照してください)。ただし、Spark ジョブのコンテキスト内から大きいテーブルのカウントを確認する必要がある場合は、データを一時テーブルにコピーしてから、Spark SQL を使用してカウントを取得できます。次に例を示します (`<primary key>` を結果の一時テーブルのフィールドに置き換えます)。

  ```scala
  val ReadFromCosmosCassandra = sqlContext
    .read
    .format("org.apache.spark.sql.cassandra")
    .options(cosmosCassandra)
    .load

  ReadFromCosmosCassandra.createOrReplaceTempView("CosmosCassandraResult")
  %sql
  select count(<primary key>) from CosmosCassandraResult
  ```

## <a name="next-steps"></a>次のステップ

* [コンテナーとデータベースのスループットのプロビジョニング](set-throughput.md) 
* [パーティション キーのベスト プラクティス](partitioning-overview.md#choose-partitionkey)
* 「[Azure Cosmos DB Capacity Planner を使用して RU/秒を見積もる](estimate-ru-with-capacity-planner.md)」の記事
* [Azure Cosmos DB Cassandra API での柔軟な拡張](manage-scale-cassandra.md)
