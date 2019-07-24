---
title: Spark を使用して Cassandra API のテーブル データを読み取る
titleSufix: Azure Cosmos DB
description: この記事では、Azure Cosmos DB 内の Cassandra API テーブルからデータを読み取る方法について説明します。
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 01a9582062d8eb0d039473a03901fc83fe179020
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2019
ms.locfileid: "54033419"
---
# <a name="read-data-from-azure-cosmos-db-cassandra-api-tables-using-spark"></a>Spark を使用して Azure Cosmos DB の Cassandra API テーブルからデータを読み取る

 この記事では、Spark から Azure Cosmos DB Cassandra API に格納されたデータを読み取る方法について説明します。

## <a name="cassandra-api-configuration"></a>Cassandra API の構成
```scala
import org.apache.spark.sql.cassandra._
//Spark connector
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql.CassandraConnector

//CosmosDB library for multiple retry
import com.microsoft.azure.cosmosdb.cassandra

//Connection-related
spark.conf.set("spark.cassandra.connection.host","YOUR_ACCOUNT_NAME.cassandra.cosmosdb.azure.com")
spark.conf.set("spark.cassandra.connection.port","10350")
spark.conf.set("spark.cassandra.connection.ssl.enabled","true")
spark.conf.set("spark.cassandra.auth.username","YOUR_ACCOUNT_NAME")
spark.conf.set("spark.cassandra.auth.password","YOUR_ACCOUNT_KEY")
spark.conf.set("spark.cassandra.connection.factory", "com.microsoft.azure.cosmosdb.cassandra.CosmosDbConnectionFactory")
//Throughput-related...adjust as needed
spark.conf.set("spark.cassandra.output.batch.size.rows", "1")
spark.conf.set("spark.cassandra.connection.connections_per_executor_max", "10")
spark.conf.set("spark.cassandra.output.concurrent.writes", "1000")
spark.conf.set("spark.cassandra.concurrent.reads", "512")
spark.conf.set("spark.cassandra.output.batch.grouping.buffer.size", "1000")
spark.conf.set("spark.cassandra.connection.keep_alive_ms", "600000000")
```
## <a name="dataframe-api"></a>データフレーム API

### <a name="read-table-using-sessionreadformat-command"></a>session.read.format コマンドを使用してテーブルを読み取る

```scala
val readBooksDF = sqlContext
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load

readBooksDF.explain
readBooksDF.show
```
### <a name="read-table-using-sparkreadcassandraformat"></a>spark.read.cassandraFormat を使用してテーブルを読み取る 

```scala
val readBooksDF = spark.read.cassandraFormat("books", "books_ks", "").load()
```

### <a name="read-specific-columns-in-table"></a>テーブル内の特定の列を読み取る

```scala
val readBooksDF = spark
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load
  .select("book_name","book_author", "book_pub_year")

readBooksDF.printSchema
readBooksDF.explain
readBooksDF.show
```

### <a name="apply-filters"></a>フィルターを適用する

現在、述語プッシュ ダウンはサポートされていません。以下のサンプルは、クライアント側のフィルター処理を反映したものです。 

```scala
val readBooksDF = spark
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load
  .select("book_name","book_author", "book_pub_year")
  .filter("book_pub_year > 1891")
//.filter("book_name IN ('A sign of four','A study in scarlet')")
//.filter("book_name='A sign of four' OR book_name='A study in scarlet'")
//.filter("book_author='Arthur Conan Doyle' AND book_pub_year=1890")
//.filter("book_pub_year=1903")  

readBooksDF.printSchema
readBooksDF.explain
readBooksDF.show
```

## <a name="rdd-api"></a>RDD API

### <a name="read-table"></a>テーブルを読み取る
```scala
val bookRDD = sc.cassandraTable("books_ks", "books")
bookRDD.take(5).foreach(println)
```

### <a name="read-specific-columns-in-table"></a>テーブル内の特定の列を読み取る

```scala
val booksRDD = sc.cassandraTable("books_ks", "books").select("book_id","book_name").cache
booksRDD.take(5).foreach(println)
```

## <a name="sql-views"></a>SQL ビュー 

### <a name="create-a-temporary-view-from-a-dataframe"></a>データフレームから一時ビューを作成する

```scala
spark
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load.createOrReplaceTempView("books_vw")
```

### <a name="run-queries-against-the-view"></a>ビューに対してクエリを実行する

```sql
select * from books_vw where book_pub_year > 1891
```

## <a name="next-steps"></a>次の手順

Spark からの Azure Cosmos DB Cassandra API の操作に関するその他の記事を以下に示します。
 
 * [操作を Upsert する](cassandra-spark-upsert-ops.md)
 * [操作を削除する](cassandra-spark-delete-ops.md)
 * [集計操作](cassandra-spark-aggregation-ops.md)
 * [テーブル コピー操作](cassandra-spark-table-copy-ops.md)

