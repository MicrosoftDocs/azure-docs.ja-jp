---
title: Spark からの Azure Cosmos DB Cassandra API での DDL 操作
description: この記事では、Spark からの Azure Cosmos DB Cassandra API に対するキースペースとテーブルの DDL 操作について詳しく説明します。
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 5c12787cd6e0df19fd842dd44da49aa5ea97aa05
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2019
ms.locfileid: "54036666"
---
# <a name="ddl-operations-in-azure-cosmos-db-cassandra-api-from-spark"></a>Spark からの Azure Cosmos DB Cassandra API での DDL 操作

この記事では、Spark からの Azure Cosmos DB Cassandra API に対するキースペースとテーブルの DDL 操作について詳しく説明します。

## <a name="cassandra-api-related-configuration"></a>Cassandra API に関連する構成 

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

## <a name="keyspace-ddl-operations"></a>キースペースの DDL 操作

### <a name="create-a-keyspace"></a>キースペースを作成する

```scala
//Cassandra connector instance
val cdbConnector = CassandraConnector(sc)

// Create keyspace
cdbConnector.withSessionDo(session => session.execute("CREATE KEYSPACE IF NOT EXISTS books_ks WITH REPLICATION = {'class': 'SimpleStrategy', 'replication_factor': 1 } "))
```

#### <a name="validate-in-cqlsh"></a>cqlsh で検証する

cqlsh で次のコマンドを実行すると、先ほど作成したキースペースが表示されます。

```bash
DESCRIBE keyspaces;
```

### <a name="drop-a-keyspace"></a>キースペースを削除する

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("DROP KEYSPACE books_ks"))
```

#### <a name="validate-in-cqlsh"></a>cqlsh で検証する

```bash
DESCRIBE keyspaces;
```
## <a name="table-ddl-operations"></a>テーブルの DDL 操作

**考慮事項:**  

- create table ステートメントを使用して、テーブル レベルでスループットを割り当てることができます。  
- 1 つのパーティション キーで、10 GB のデータを格納できます。  
- 1 つのレコードで、最大 2 MB のデータを格納できます。  
- 1 つのパーティション キー範囲で、複数のパーティション キーを格納できます。

### <a name="create-a-table"></a>テーブルを作成する

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("CREATE TABLE IF NOT EXISTS books_ks.books(book_id TEXT PRIMARY KEY,book_author TEXT, book_name TEXT,book_pub_year INT,book_price FLOAT) WITH cosmosdb_provisioned_throughput=4000 , WITH default_time_to_live=630720000;"))
```

#### <a name="validate-in-cqlsh"></a>cqlsh で検証する

cqlsh で次のコマンドを実行すると、"books" という名前のテーブルが表示されます。 

```bash
USE books_ks;
DESCRIBE books;
```

前のコマンドの出力には、プロビジョニングされたスループットと既定の TTL の値は表示されません。これらの値はポータルで確認できます。

### <a name="alter-table"></a>テーブルを変更する

alter table コマンドを使用して、次の値を変更できます。

* プロビジョニングされているスループット 
* Time-To-Live の値
<br>現在、列の変更はサポートされていません。

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("ALTER TABLE books_ks.books WITH cosmosdb_provisioned_throughput=8000, WITH default_time_to_live=0;"))
```

### <a name="drop-table"></a>テーブルの削除

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("DROP TABLE IF EXISTS books_ks.books;"))
```

#### <a name="validate-in-cqlsh"></a>cqlsh で検証する

cqlsh で次のコマンドを実行すると、"books" テーブルが使用できなくなったことがわかります。

```bash
USE books_ks;
DESCRIBE tables;
```

## <a name="next-steps"></a>次の手順

キースペースとテーブルを作成した後は、CRUD 操作などに関する次の記事に進んでください。
 
* [操作を作成/挿入する](cassandra-spark-create-ops.md)  
* [操作を読み取る](cassandra-spark-read-ops.md)  
* [操作を Upsert する](cassandra-spark-upsert-ops.md)  
* [操作を削除する](cassandra-spark-delete-ops.md)  
* [集計操作](cassandra-spark-aggregation-ops.md)  
* [テーブル コピー操作](cassandra-spark-table-copy-ops.md)  
