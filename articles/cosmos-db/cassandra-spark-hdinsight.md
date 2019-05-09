---
title: HDInsight を使用して YARN で Spark から Azure Cosmos DB Cassandra API にアクセスする
description: この記事では、HDInsight を使って YARN で Spark から Azure Cosmos DB Cassandra API を使用する方法について説明します
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: f728baedf9e325f224ce52e64325064f553d2671
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2019
ms.locfileid: "54032901"
---
# <a name="access-azure-cosmos-db-cassandra-api-from-spark-on-yarn-with-hdinsight"></a>HDInsight を使用して YARN で Spark から Azure Cosmos DB Cassandra API にアクセスする

この記事では、spark-shell の HDInsight-Spark を使って、YARN で Spark から Azure Cosmos DB Cassandra API にアクセスする方法について説明します。 HDInsight は、Azure 上の Microsoft の Hortonworks Hadoop PaaS です。HDFS 用のオブジェクト ストレージを活用し、[Spark](../hdinsight/spark/apache-spark-overview.md) など、いくつかの種類で提供されます。  このドキュメントのコンテンツでは HDInsight-Spark が参照されていますが、これはすべての Hadoop ディストリビューションに適用できます。  

## <a name="prerequisites"></a>前提条件

* [Azure Cosmos DB Cassandra API をプロビジョニングする](create-cassandra-dotnet.md#create-a-database-account)

* [Azure Cosmos DB Cassandra API への接続の基本を確認する](cassandra-spark-generic.md)

* [HDInsight-Spark クラスターをプロビジョニングする](../hdinsight/spark/apache-spark-jupyter-spark-sql.md)

* [Cassandra API の使用におけるコード サンプルを確認する](cassandra-spark-generic.md#next-steps)

* [必要な場合は検証のために cqlsh を使用する](cassandra-spark-generic.md##connecting-to-azure-cosmos-db-cassandra-api-from-spark)

* **Spark2 の Cassandra API 構成** - Cassandra 用の Spark コネクタでは、Spark コンテキストの一部として、Cassandra への接続の詳細を初期化する必要があります。 Jupyter ノートブックを起動すると、Spark セッションとコンテキストは既に初期化されています。Spark コンテキストが、HDInsight の既定の Jupyter ノートブックのスタートアップの一部としてすべての構成セットを備えていない限り、このコンテキストを停止して再初期化することはお勧めしません。 回避策の 1 つは、Ambari、Spark2 サービス構成に Cassandra インスタンスの詳細を直接追加することです。 これはクラスターあたり 1 回限りのアクティビティで、Spark2 サービスの再起動が必要です。
 
  1. Ambari、Spark2 サービスに移動し、configs を選択します

  2. 次に、カスタム spark2-defaults に移動し、次を含む新しいプロパティを追加して、Spark2 サービスを再起動します。

  ```scala
  spark.cassandra.connection.host=YOUR_COSMOSDB_ACCOUNT_NAME.cassandra.cosmosdb.azure.com<br>
  spark.cassandra.connection.port=10350<br>
  spark.cassandra.connection.ssl.enabled=true<br>
  spark.cassandra.auth.username=YOUR_COSMOSDB_ACCOUNT_NAME<br>
  spark.cassandra.auth.password=YOUR_COSMOSDB_KEY<br>
  ```

## <a name="access-azure-cosmos-db-cassandra-api-from-spark-shell"></a>Spark シェルから Azure Cosmos DB Cassandra API にアクセスする

Spark シェルは、テスト/探索の目的で使用されます。

* クラスターの Spark バージョンと互換性のある、必須の Maven の依存関係を持つ spark-shell を起動します。

  ```scala
  spark-shell --packages "com.datastax.spark:spark-cassandra-connector_2.11:2.3.0,com.microsoft.azure.cosmosdb:azure-cosmos-cassandra-spark-helper:1.0.0"
  ```

* 一部の DDL および DML 操作を実行します

  ```scala
  import org.apache.spark.rdd.RDD
  import org.apache.spark.{SparkConf, SparkContext}

  import spark.implicits._
  import org.apache.spark.sql.functions._
  import org.apache.spark.sql.Column
  import org.apache.spark.sql.types.{StructType, StructField, StringType, IntegerType,LongType,FloatType,DoubleType, TimestampType}
  import org.apache.spark.sql.cassandra._

  //Spark connector
  import com.datastax.spark.connector._
  import com.datastax.spark.connector.cql.CassandraConnector

  //CosmosDB library for multiple retry
  import com.microsoft.azure.cosmosdb.cassandra

  // Specify connection factory for Cassandra
  spark.conf.set("spark.cassandra.connection.factory", "com.microsoft.azure.cosmosdb.cassandra.CosmosDbConnectionFactory")

  // Parallelism and throughput configs
  spark.conf.set("spark.cassandra.output.batch.size.rows", "1")
  spark.conf.set("spark.cassandra.connection.connections_per_executor_max", "10")
  spark.conf.set("spark.cassandra.output.concurrent.writes", "100")
  spark.conf.set("spark.cassandra.concurrent.reads", "512")
  spark.conf.set("spark.cassandra.output.batch.grouping.buffer.size", "1000")
  spark.conf.set("spark.cassandra.connection.keep_alive_ms", "60000000") //Increase this number as needed
  ```

* CRUD 操作を実行します

  ```scala
  //1) Create table if it does not exist
  val cdbConnector = CassandraConnector(sc)
  cdbConnector.withSessionDo(session => session.execute("CREATE TABLE IF NOT EXISTS books_ks.books(book_id TEXT PRIMARY KEY,book_author TEXT, book_name TEXT,book_pub_year INT,book_price FLOAT) WITH cosmosdb_provisioned_throughput=4000;"))

  //2) Delete data from potential prior runs
  cdbConnector.withSessionDo(session => session.execute("DELETE FROM books_ks.books WHERE book_id IN ('b00300','b00001','b00023','b00501','b09999','b01001','b00999','b03999','b02999','b000009');"))

  //3) Generate a few rows
  val booksDF = Seq(
   ("b00001", "Arthur Conan Doyle", "A study in scarlet", 1887,11.33),
   ("b00023", "Arthur Conan Doyle", "A sign of four", 1890,22.45),
   ("b01001", "Arthur Conan Doyle", "The adventures of Sherlock Holmes", 1892,19.83),
   ("b00501", "Arthur Conan Doyle", "The memoirs of Sherlock Holmes", 1893,14.22),
   ("b00300", "Arthur Conan Doyle", "The hounds of Baskerville", 1901,12.25)
  ).toDF("book_id", "book_author", "book_name", "book_pub_year","book_price")

  //4) Persist
  booksDF.write.mode("append").format("org.apache.spark.sql.cassandra").options(Map( "table" -> "books", "keyspace" -> "books_ks", "output.consistency.level" -> "ALL", "ttl" -> "10000000")).save()

  //5) Read the data in the table
  spark.read.format("org.apache.spark.sql.cassandra").options(Map( "table" -> "books", "keyspace" -> "books_ks")).load.show
  ```

## <a name="access-azure-cosmos-db-cassandra-api-from-jupyter-notebooks"></a>Jupyter ノートブックから Azure Cosmos DB Cassandra API にアクセスする

HDInsight-Spark は、Zeppelin および Jupyter Notebook サービスと共に提供されます。 これはどちらも Scala と Python をサポートする Web ベースのノートブック環境です。 ノートブックは対話型の探索的分析およびコラボレーションに適していますが、運用/生産プロセスには適していません。

次の Jupyter ノートブックは HDInsight Spark クラスターにアップロードできます。これらの ノートブックには、Azure Cosmos DB Cassandra API を操作するためのすぐに使えるサンプルが用意されています。 最初のノートブック `1.0-ReadMe.ipynb` で、Azure Cosmos DB Cassandra API に接続するための Spark サービス構成を必ず確認してください。

[azure-cosmos-db-cassandra-api-spark-notebooks-jupyter](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-notebooks-jupyter/blob/master/scala/) にあるこれらのノートブックを、お使いのマシンにダウンロードします。
  
### <a name="how-to-upload"></a>アップロード方法:
Jupyter を起動するときに、Scala に移動します。 まずディレクトリを作成し、次に、ノートブックをディレクトリにアップロードします。 アップロード ボタンは上部右側にあります。  

### <a name="how-to-run"></a>実行方法:
ノートブックと、各ノートブック セルを順番に実行します。  各ノートブックの上部にある実行ボタンをクリックして、すべてのセルを実行するか、セルごとに Shift + Enter キーを押します。

## <a name="access-with-azure-cosmos-db-cassandra-api-from-your-spark-scala-program"></a>Spark Scala プログラムから Azure Cosmos DB Cassandra API でアクセスする

運用環境での自動化されたプロセスでは、Spark プログラムが [spark-submit](https://spark.apache.org/docs/latest/submitting-applications.html) を使用してクラスターに送信されます。

## <a name="next-steps"></a>次の手順

* [IDE で Spark Scala プログラムを構築し、Livy を使用して HDInsight Spark クラスターに送信して実行する方法](../hdinsight/spark/apache-spark-create-standalone-application.md)

* [Spark Scala から Azure Cosmos DB Cassandra API に接続する方法](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-connector-sample/blob/master/src/main/scala/com/microsoft/azure/cosmosdb/cassandra/SampleCosmosDBApp.scala)

* [Cassandra API を使用する場合のサンプルコードの一覧](cassandra-spark-generic.md)
