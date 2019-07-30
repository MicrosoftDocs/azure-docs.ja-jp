---
title: Apache Spark を Azure Cosmos DB に接続する
description: Apache Spark を Azure Cosmos DB に接続できるようにする Azure Cosmos DB Spark コネクタについて説明します。
author: tknandu
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: ramkris
ms.openlocfilehash: 2a0e88a439400bc36873ed7160b8eb039a16ebfb
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68356181"
---
# <a name="accelerate-big-data-analytics-by-using-the-apache-spark-to-azure-cosmos-db-connector"></a>Apache Spark-Azure Cosmos DB コネクタを使用したビッグ データ分析の高速化

Cosmos DB Spark コネクタを使用して、Azure Cosmos DB に格納されているデータで [Spark](https://spark.apache.org/) ジョブを実行できます。 Cosmos は、バッチおよびストリーム処理に使用可能であり、低待機時間でのアクセスのためのサービス レイヤーとして使用できます。

このコネクタは、Azure 上でマネージド Spark クラスターを提供する [Azure Databricks](https://azure.microsoft.com/services/databricks) または [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) と一緒に使用できます。 サポートする Spark バージョンを次の表に示します。

| コンポーネント | Version |
|---------|-------|
| Apache Spark | 2.4.x、2.3.x、2.2.x、2.1.x |
| Scala | 2.11 |
| Azure Databricks ランタイムのバージョン | 3\.4 より新しいバージョン |

> [!WARNING]
> このコネクタは、Azure Cosmos DB のコア (SQL) API をサポートします。
> Cosmos DB for MongoDB API の場合は、[MongoDB Spark コネクタ](https://docs.mongodb.com/spark-connector/master/)を使用してください。
> Cosmos DB Cassandra API の場合は、[Cassandra Spark コネクタ](https://github.com/datastax/spark-cassandra-connector)を使用してください。
>

## <a name="quickstart"></a>クイック スタート

* [Java SDK の開始](sql-api-async-java-get-started.md)の手順に従って Cosmos DB アカウントをセットアップし、データを入力してください。
* [Azure Databricks の開始](https://docs.azuredatabricks.net/getting-started/index.html)の手順に従って Azure Databricks ワークスペースとクラスターをセットアップします。
* これで、新しい Notebook を作成し、Cosmos DB コネクタ ライブラリをインポートすることができるようになりました。 ワークスペースのセットアップ方法についての詳細は、[Cosmos DB コネクタの操作](#bk_working_with_connector)に関する記事をご覧ください。
* 次のセクションには、コネクタを使用した読み取りと書き込みの方法のスニペットがあります。

### <a name="batch-reads-from-cosmos-db"></a>Cosmos DB からのバッチ読み取り

次のスニペットでは、PySpark で Cosmos DB からの読み取りのための Spark DataFrame を作成する方法を示します。

```python
# Read Configuration
readConfig = {
    "Endpoint": "https://doctorwho.documents.azure.com:443/",
    "Masterkey": "YOUR-KEY-HERE",
    "Database": "DepartureDelays",
    "Collection": "flights_pcoll",
    "query_custom": "SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'" // Optional
}

# Connect via azure-cosmosdb-spark to create Spark DataFrame
flights = spark.read.format(
    "com.microsoft.azure.cosmosdb.spark").options(**readConfig).load()
flights.count()
```

さらに、Scala での同様のコード スニペットを以下に示します。

```scala
// Import Necessary Libraries
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.config.Config

// Read Configuration
val readConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_pcoll",
  "query_custom" -> "SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'" // Optional
))

// Connect via azure-cosmosdb-spark to create Spark DataFrame
val flights = spark.read.cosmosDB(readConfig)
flights.count()
```

### <a name="batch-writes-to-cosmos-db"></a>Cosmos DB へのバッチ書き込み

次のスニペットでは、PySpark で Cosmos DB にデータ フレームを書き込む方法を示します。

```python
# Write configuration
writeConfig = {
    "Endpoint": "https://doctorwho.documents.azure.com:443/",
    "Masterkey": "YOUR-KEY-HERE",
    "Database": "DepartureDelays",
    "Collection": "flights_fromsea",
    "Upsert": "true"
}

# Write to Cosmos DB from the flights DataFrame
flights.write.format("com.microsoft.azure.cosmosdb.spark").options(
    **writeConfig).save()
```

さらに、Scala での同様のコード スニペットを以下に示します。

```scala
// Write configuration

val writeConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_fromsea",
  "Upsert" : "true"
))

// Write to Cosmos DB from the flights DataFrame
import org.apache.spark.sql.SaveMode
flights.write.mode(SaveMode.Overwrite).cosmosDB(writeConfig)
```

### <a name="streaming-reads-from-cosmos-db"></a>Cosmos DB からのストリーミング読み取り

次のスニペットは、Azure Cosmos DB 変更フィードに接続し、そこから読み取る方法を示します。

```python
# Read Configuration
readConfig = {
    "Endpoint": "https://doctorwho.documents.azure.com:443/",
    "Masterkey": "YOUR-KEY-HERE",
    "Database": "DepartureDelays",
    "Collection": "flights_pcoll",
    "ReadChangeFeed": "true",
    "ChangeFeedQueryName": "Departure-Delays",
    "ChangeFeedStartFromTheBeginning": "false",
    "InferStreamSchema": "true",
    "ChangeFeedCheckpointLocation": "dbfs:/Departure-Delays"
}


# Open a read stream to the Cosmos DB Change Feed via azure-cosmosdb-spark to create Spark DataFrame
changes = (spark
           .readStream
           .format("com.microsoft.azure.cosmosdb.spark.streaming.CosmosDBSourceProvider")
           .options(**readConfig)
           .load())
```
さらに、Scala での同様のコード スニペットを以下に示します。

```scala
// Import Necessary Libraries
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.config.Config

// Read Configuration
val readConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_pcoll",
  "ReadChangeFeed" -> "true",
  "ChangeFeedQueryName" -> "Departure-Delays",
  "ChangeFeedStartFromTheBeginning" -> "false",
  "InferStreamSchema" -> "true",
  "ChangeFeedCheckpointLocation" -> "dbfs:/Departure-Delays"
))

// Open a read stream to the Cosmos DB Change Feed via azure-cosmosdb-spark to create Spark DataFrame
val df = spark.readStream.format(classOf[CosmosDBSourceProvider].getName).options(readConfig).load()
```

### <a name="streaming-writes-to-cosmos-db"></a>Cosmos DB へのストリーミング書き込み

次のスニペットでは、PySpark で Cosmos DB にデータ フレームを書き込む方法を示します。

```python
# Write configuration
writeConfig = {
    "Endpoint": "https://doctorwho.documents.azure.com:443/",
    "Masterkey": "YOUR-KEY-HERE",
    "Database": "DepartureDelays",
    "Collection": "flights_fromsea",
    "Upsert": "true",
    "WritingBatchSize": "500",
    "CheckpointLocation": "/checkpointlocation_write1"
}

# Write to Cosmos DB from the flights DataFrame
changeFeed = (changes
              .writeStream
              .format("com.microsoft.azure.cosmosdb.spark.streaming.CosmosDBSinkProvider")
              .outputMode("append")
              .options(**writeconfig)
              .start())
```

さらに、Scala での同様のコード スニペットを以下に示します。

```scala
// Write configuration

val writeConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_fromsea",
  "Upsert" -> "true",
  "WritingBatchSize" -> "500",
  "CheckpointLocation" -> "/checkpointlocation_write1"
))

// Write to Cosmos DB from the flights DataFrame
df
.writeStream
.format(classOf[CosmosDBSinkProvider].getName)
.options(writeConfig)
.start()
```
その他のスニペットやエンド ツー エンドのサンプルは、[Jupyter](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/notebooks) を参照してください。

## <a name="bk_working_with_connector"></a> コネクタの操作

以下のリンクで、GitHub でソースからコネクタを作成したり、Maven から uber jar をダウンロードしたりすることができます。

| Spark | Scala | 最新バージョン |
|---|---|---|
| 2.4.0 | 2.11 | [azure-cosmosdb-spark_2.4.0_2.11_1.4.0](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11/1.4.0/jar)
| 2.3.0 | 2.11 | [azure-cosmosdb-spark_2.3.0_2.11_1.3.3](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.3.0_2.11/1.3.3/jar)
| 2.2.0 | 2.11 | [azure-cosmosdb-spark_2.2.0_2.11_1.1.1](https://search.maven.org/#artifactdetails%7Ccom.microsoft.azure%7Cazure-cosmosdb-spark_2.2.0_2.11%7C1.1.1%7Cjar)
| 2.1.0 | 2.11 | [azure-cosmosdb-spark_2.1.0_2.11_1.2.2](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.1.0_2.11/1.2.2/jar)

### <a name="using-databricks-notebooks"></a>Databricks ノートブックの使用

Azure Databricks ガイドのガイダンスで [Azure Cosmos DB Spark コネクタの使用](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/cosmosdb-connector.html)に関する記事に従って、Databricks ワークスペースでライブラリを作成します

> [!NOTE]
> **Azure Cosmos DB Spark コネクタの使用**に関するページは現在最新ではありませんのでご注意ください。 6 つの別々の jar を 6 つの異なるライブラリにダウンロードするのではなく、 https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11/1.4.0/jar) の Maven から uber jar をダウンロードして、ライブラリごとに 1 つの jar をインストールすることができます。
> 

### <a name="using-spark-cli"></a>spark-cli の使用

spark-cli (つまり `spark-shell`、`pyspark`、`spark-submit`) を使用してコネクタを操作するために、`--packages` パラメーターをコネクタの [Maven 座標](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11)と一緒に使用することができます。

```sh
spark-shell --master yarn --packages "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.4.0"

```

### <a name="using-jupyter-notebooks"></a>Jupyter ノートブックの使用

HDInsight 内で Jupyter ノートブックを使用している場合は、spark-magic `%%configure` セルを使用してコネクタの Maven 座標を指定できます。

```python
{ "name":"Spark-to-Cosmos_DB_Connector",
  "conf": {
    "spark.jars.packages": "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.4.0",
    "spark.jars.excludes": "org.scala-lang:scala-reflect"
   }
   ...
}
```

> `spark.jars.excludes` を含めるのは、コネクタ、Apache Spark、Livy の間の潜在的な競合を削除するためです。

### <a name="build-the-connector"></a>コネクタを作成します

現在、このコネクタ プロジェクトでは `maven` を使用しているため、依存関係なしのビルドを行うには、次のコマンドを実行します。

```sh
mvn clean package
```

## <a name="working-with-our-samples"></a>サンプルの実行

[Cosmos DB Spark GitHub リポジトリ](https://github.com/Azure/azure-cosmosdb-spark)では、以下のサンプル ノートブックとスクリプトをお試しいただけます。

* **Spark と Cosmos DB を利用したオンタイム フライト パフォーマンス (シアトル)** [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.html):Spark SQL、GraphFrames を表示する HDInsight Jupyter ノートブック サービスを使用して Spark を Cosmos DB に接続し、ML パイプラインを使用してフライト遅延を予測します。
* **Apache Spark と Azure Cosmos DB 変更フィードの Twitter ソース**: [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.html)
* **Apache Spark を使用して Cosmos DB グラフを検索する**: [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.html)
* `azure-cosmosdb-spark` を使用した **[Azure Cosmos DB への Azure Databricks の接続](https://docs.databricks.com/spark/latest/data-sources/azure/cosmosdb-connector.html)** 。  ここでリンクされているものも、[オンタイム フライト パフォーマンス ノートブック](https://github.com/dennyglee/databricks/tree/master/notebooks/Users/denny%40databricks.com/azure-databricks)の Azure Databricks バージョンです。
* **[Azure Cosmos DB と HDInsight でのラムダ アーキテクチャ (Apache Spark)](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/readme.md)** :Cosmos DB と Spark を使用してビッグ データ パイプラインを維持するための運用上のオーバーヘッドを削減できます。

## <a name="more-information"></a>詳細情報

次の点に関する詳細情報は `azure-cosmosdb-spark` [Wiki](https://github.com/Azure/azure-cosmosdb-spark/wiki) にあります。

* [Azure Cosmos DB Spark コネクタ ユーザー ガイド](https://github.com/Azure/azure-documentdb-spark/wiki/Azure-Cosmos-DB-Spark-Connector-User-Guide)
* [集計の例](https://github.com/Azure/azure-documentdb-spark/wiki/Aggregations-Examples)

### <a name="configuration-and-setup"></a>構成とセットアップ

* [Spark コネクタの構成](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuration-references)
* [Spark-Cosmos DB コネクタのセットアップ](https://github.com/Azure/azure-documentdb-spark/wiki/Spark-to-Cosmos-DB-Connector-Setup) (作業中)
* [Apache Spark (HDI) を使用した Azure Cosmos DB への Power BI での直接クエリの構成](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuring-Power-BI-Direct-Query-to-Azure-Cosmos-DB-via-Apache-Spark-(HDI))

### <a name="troubleshooting"></a>トラブルシューティング

* [Cosmos DB の集計の使用](https://github.com/Azure/azure-documentdb-spark/wiki/Troubleshooting:-Using-Cosmos-DB-Aggregates)
* [既知の問題](https://github.com/Azure/azure-cosmosdb-spark/wiki/Known-Issues)

### <a name="performance"></a>パフォーマンス

* [パフォーマンスに関するヒント](https://github.com/Azure/azure-cosmosdb-spark/wiki/Performance-tips)
* [クエリのテストの実行](https://github.com/Azure/azure-documentdb-spark/wiki/Query-Test-Runs)
* [テストの実行の書き込み](https://github.com/Azure/azure-cosmosdb-spark/wiki/Writing-Test-Runs)

### <a name="change-feed"></a>変更フィード

* [Azure Cosmos DB 変更フィードと Apache Spark を使用したストリーム処理の変更](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)
* [変更フィードのデモ](https://github.com/Azure/azure-cosmosdb-spark/wiki/Change-Feed-demos)
* [構造化ストリームのデモ](https://github.com/Azure/azure-cosmosdb-spark/wiki/Structured-Stream-demos)

### <a name="monitoring"></a>監視

* [Application Insights による Spark ジョブの監視](https://github.com/Azure/azure-cosmosdb-spark/tree/2.3/samples/monitoring)

## <a name="next-steps"></a>次の手順

まだ行っていない場合は、[azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark) GitHub リポジトリから Spark-Azure Cosmos DB コネクタをダウンロードします。 リポジトリ内で次の追加リソースを探します。

* [集計の例](https://github.com/Azure/azure-cosmosdb-spark/wiki/Aggregations-Examples)
* [サンプル スクリプトと Notebook](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)

また、[Apache Spark SQL、DataFrames、データセット ガイド](https://spark.apache.org/docs/latest/sql-programming-guide.html)と [Azure HDInsight 上の Apache Spark](../hdinsight/spark/apache-spark-jupyter-spark-sql.md) に関する記事を確認することもできます。
