---
title: Spark からの Azure Cosmos DB Cassandra API テーブルに対する集計操作
description: この記事では、Spark から Azure Cosmos DB Cassandra API テーブルに対する基本的な集計操作について説明します。
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 09/24/2018
ms.openlocfilehash: 5939690d3f2c0bb9affa3e2fb425b909b96cf002
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93087623"
---
# <a name="aggregate-operations-on-azure-cosmos-db-cassandra-api-tables-from-spark"></a>Spark からの Azure Cosmos DB Cassandra API テーブルに対する集計操作 
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

この記事では、Spark から Azure Cosmos DB Cassandra API テーブルに対する基本的な集計操作について説明します。 

> [!NOTE]
> Azure Cosmos DB Cassandra API では、サーバー側のフィルター処理とサーバー側の集計は現在サポートされていません。

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
## <a name="sample-data-generator"></a>サンプル データ ジェネレーター

```scala
// Generate a simple dataset containing five values
val booksDF = Seq(
   ("b00001", "Arthur Conan Doyle", "A study in scarlet", 1887,11.33),
   ("b00023", "Arthur Conan Doyle", "A sign of four", 1890,22.45),
   ("b01001", "Arthur Conan Doyle", "The adventures of Sherlock Holmes", 1892,19.83),
   ("b00501", "Arthur Conan Doyle", "The memoirs of Sherlock Holmes", 1893,14.22),
   ("b00300", "Arthur Conan Doyle", "The hounds of Baskerville", 1901,12.25)
).toDF("book_id", "book_author", "book_name", "book_pub_year","book_price")

booksDF.write
  .mode("append")
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks", "output.consistency.level" -> "ALL", "ttl" -> "10000000"))
  .save()
```

## <a name="count-operation"></a>カウント操作


### <a name="rdd-api"></a>RDD API

```scala
sc.cassandraTable("books_ks", "books").count
```

**出力:**
```bash
res48: Long = 5
```

### <a name="dataframe-api"></a>データフレーム API

データフレームに対するカウントは現在サポートされていません。  以下のサンプルは、回避策としてデータフレームをメモリに保存した後にデータフレームのカウントを実行する方法を示しています。

"メモリ不足" の問題が発生しないように回避するには、次の使用できるオプションから[ストレージ オプション]( https://spark.apache.org/docs/2.2.0/rdd-programming-guide.html#which-storage-level-to-choose)を選択します。

* MEMORY_ONLY: これは既定のストレージ オプションです。 JVM 内の逆シリアル化された Java オブジェクトとして RDD を格納します。 RDD がメモリ内に収まらない場合、一部のパーティションはキャッシュされず、必要になるたびにその場で再計算されます。

* MEMORY_AND_DISK: JVM 内の逆シリアル化された Java オブジェクトとして RDD を格納します。 RDD がメモリ内に収まらない場合、収まらないパーティションをディスク上に格納し、必要に応じて、格納されている場所から読み取ります。

* MEMORY_ONLY_SER (Java/Scala): シリアル化された Java オブジェクト (パーティションごとに 1 バイトの配列) として RDD を格納します。 逆シリアル化されたオブジェクトと比較すると、このオプションは、特に高速シリアライザーを使用する場合にスペース効率が良くなりますが、読み取りに使用される CPU が多くなります。

* MEMORY_AND_DISK_SER (Java/Scala): このストレージ オプションは MEMORY_ONLY_SER と似ていますが、唯一の違いは、必要に応じて再計算が実行されるのではなく、ディスク メモリ内に収まらないパーティションのスピルが発生することです。

* DISK_ONLY: RDD パーティションをディスク上にのみ格納します。

* MEMORY_ONLY_2、MEMORY_AND_DISK_2...: 上記のレベルと同じですが、2 つのクラスター ノード上の各パーティションをレプリケートします。

* OFF_HEAP (実験的): MEMORY_ONLY_SER に似ていますが、データをオフヒープ メモリに格納します。事前にオフヒープ メモリを有効にする必要があります。 

```scala
//Workaround
import org.apache.spark.storage.StorageLevel

//Read from source
val readBooksDF = spark
  .read
  .cassandraFormat("books", "books_ks", "")
  .load()

//Explain plan
readBooksDF.explain

//Materialize the dataframe
readBooksDF.persist(StorageLevel.MEMORY_ONLY)

//Subsequent execution against this DF hits the cache 
readBooksDF.count

//Persist as temporary view
readBooksDF.createOrReplaceTempView("books_vw")
```

### <a name="sql"></a>SQL

```sql
select * from books_vw;
select count(*) from books_vw where book_pub_year > 1900;
select count(book_id) from books_vw;
select book_author, count(*) as count from books_vw group by book_author;
select count(*) from books_vw;
```

## <a name="average-operation"></a>平均操作

### <a name="rdd-api"></a>RDD API

```scala
sc.cassandraTable("books_ks", "books").select("book_price").as((c: Double) => c).mean
```

**出力:**
```
res24: Double = 16.016000175476073
```

### <a name="dataframe-api"></a>データフレーム API

```scala
spark
  .read
  .cassandraFormat("books", "books_ks", "")
  .load()
  .select("book_price")
  .agg(avg("book_price"))
  .show
```

**出力:**
```
+------------------+
|   avg(book_price)|
+------------------+
|16.016000175476073|
+------------------+
```

### <a name="sql"></a>SQL

```sql
select avg(book_price) from books_vw;
```
**出力:**
```
16.016000175476073
```

## <a name="min-operation"></a>最小値操作

### <a name="rdd-api"></a>RDD API

```scala
sc.cassandraTable("books_ks", "books").select("book_price").as((c: Float) => c).min
```

**出力:**
```
res31: Float = 11.33
```

### <a name="dataframe-api"></a>データフレーム API

```scala
spark
  .read
  .cassandraFormat("books", "books_ks", "")
  .load()
  .select("book_id","book_price")
  .agg(min("book_price"))
  .show
```

**出力:**
```
+---------------+
|min(book_price)|
+---------------+
|          11.33|
+---------------+
```

### <a name="sql"></a>SQL

```sql
select min(book_price) from books_vw;
```

**出力:**
```
11.33
```

## <a name="max-operation"></a>最大値操作

### <a name="rdd-api"></a>RDD API

```scala
sc.cassandraTable("books_ks", "books").select("book_price").as((c: Float) => c).max
```

### <a name="dataframe-api"></a>データフレーム API

```scala 
spark
  .read
  .cassandraFormat("books", "books_ks", "")
  .load()
  .select("book_price")
  .agg(max("book_price"))
  .show
```

**出力:**
```
+---------------+
|max(book_price)|
+---------------+
|          22.45|
+---------------+
```

### <a name="sql"></a>SQL

```sql
select max(book_price) from books_vw;
```
**出力:**
```
22.45
```

## <a name="sum-operation"></a>合計操作

### <a name="rdd-api"></a>RDD API

```scala
sc.cassandraTable("books_ks", "books").select("book_price").as((c: Float) => c).sum
```

**出力:**
```
res46: Double = 80.08000087738037
```

### <a name="dataframe-api"></a>データフレーム API

```scala
spark
  .read
  .cassandraFormat("books", "books_ks", "")
  .load()
  .select("book_price")
  .agg(sum("book_price"))
  .show
```
**出力:**
```
+-----------------+
|  sum(book_price)|
+-----------------+
|80.08000087738037|
+-----------------+
```

### <a name="sql"></a>SQL

```sql
select sum(book_price) from books_vw;
```

**出力:**
```
80.08000087738037
```

## <a name="top-or-comparable-operation"></a>上位または同等操作

### <a name="rdd-api"></a>RDD API

```scala
val readCalcTopRDD = sc.cassandraTable("books_ks", "books").select("book_name","book_price").sortBy(_.getFloat(1), false)
readCalcTopRDD.zipWithIndex.filter(_._2 < 3).collect.foreach(println)
//delivers the first top n items without collecting the rdd to the driver.
```
**出力:**
```
(CassandraRow{book_name: A sign of four, book_price: 22.45},0)
(CassandraRow{book_name: The adventures of Sherlock Holmes, book_price: 19.83},1)
(CassandraRow{book_name: The memoirs of Sherlock Holmes, book_price: 14.22},2)
readCalcTopRDD: org.apache.spark.rdd.RDD[com.datastax.spark.connector.CassandraRow] = MapPartitionsRDD[430] at sortBy at command-2371828989676374:1
```
### <a name="dataframe-api"></a>データフレーム API

```scala
import org.apache.spark.sql.functions._

val readBooksDF = spark.read.format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load
  .select("book_name","book_price")
  .orderBy(desc("book_price"))
  .limit(3)

//Explain plan
readBooksDF.explain

//Top
readBooksDF.show
```

**出力:**
```
== Physical Plan ==
TakeOrderedAndProject(limit=3, orderBy=[book_price#1840 DESC NULLS LAST], output=[book_name#1839,book_price#1840])
+- *(1) Scan org.apache.spark.sql.cassandra.CassandraSourceRelation@29cd5f58 [book_name#1839,book_price#1840] PushedFilters: [], ReadSchema: struct<book_name:string,book_price:float>
+--------------------+----------+
|           book_name|book_price|
+--------------------+----------+
|      A sign of four|     22.45|
|The adventures of...|     19.83|
|The memoirs of Sh...|     14.22|
+--------------------+----------+

import org.apache.spark.sql.functions._
readBooksDF: org.apache.spark.sql.Dataset[org.apache.spark.sql.Row] = [book_name: string, book_price: float]
```

### <a name="sql"></a>SQL

```sql
select book_name,book_price from books_vw order by book_price desc limit 3;
```

## <a name="next-steps"></a>次のステップ

テーブル コピー操作を実行する方法については、以下を参照してください。

* [テーブル コピー操作](cassandra-spark-table-copy-ops.md)
