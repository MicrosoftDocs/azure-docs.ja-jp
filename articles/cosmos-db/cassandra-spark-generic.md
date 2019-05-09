---
title: Spark から Azure Cosmos DB Cassandra API を使用する
description: この記事は、Spark からの Cosmos DB Cassandra API 統合のメイン ページです。
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 75d2930363b6ad1aeace22d7529df04f31deefe5
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2019
ms.locfileid: "54037228"
---
# <a name="connect-to-azure-cosmos-db-cassandra-api-from-spark"></a>Spark から Azure Cosmos DB Cassandra API に接続する

この記事は、Spark からの Azure Cosmos DB Cassandra API 統合に関する一連の記事の 1 つです。 この記事では、接続、データ定義言語 (DDL) 操作、基本的なデータ操作言語 (DML) 操作、および Spark からの高度な Azure Cosmos DB Cassandra API 統合について説明します。 

## <a name="prerequisites"></a>前提条件
* [Azure Cosmos DB Cassandra API アカウントのプロビジョニング](create-cassandra-dotnet.md#create-a-database-account)

* 任意の Spark 環境 [[Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) | [Azure HDInsight-Spark](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql) | その他] をプロビジョニングします。

## <a name="dependencies-for-connectivity"></a>接続の依存関係
* **Cassandra 用 Spark コネクタ:** Spark コネクタは、Azure Cosmos DB Cassandra API に接続するために使用されます。  [Maven central]( https://mvnrepository.com/artifact/com.datastax.spark/spark-cassandra-connector) で、実際の Spark 環境の Spark および Scala のバージョンと互換性のあるコネクタのバージョンを指定して使用します。

* **Cassandra API 用 Azure Cosmos DB ヘルパー ライブラリ:** Spark コネクタに加えて、Azure Cosmos DB の [azure-cosmos-cassandra-spark-helper]( https://search.maven.org/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper/1.0.0/jar) という別のライブラリが必要です。 このライブラリには、カスタムの接続ファクトリと再試行ポリシー クラスが含まれています。

  Azure Cosmos DB の再試行ポリシーは、HTTP 状態コード 429 ("Request Rate Large") 例外を処理するように構成されています。 Azure Cosmos DB Cassandra API は、このような例外を Cassandra ネイティブ プロトコルに関する過負荷エラーに変換するため、バックオフで再試行できます。 Azure Cosmos DB はプロビジョニングされたスループット モデルを使用するため、受信/送信レートが増加すると、要求レートの制限例外が発生します。 再試行ポリシーによって、収集に割り当てられたスループットを瞬間的に超えるデータの急増から Spark ジョブを保護します。

  > [!NOTE] 
  > 再試行ポリシーで、瞬間的な急増に対してのみ Spark ジョブを保護することができます。 ワークロードを実行するために必要十分な RU を構成していない場合は、再試行ポリシーは適用されず、再試行ポリシー クラスによって例外が再スローされます。

* **Azure Cosmos DB アカウントの接続の詳細:** Azure Cassandra API のアカウント名、アカウント エンドポイント、およびキー。
    
## <a name="spark-connector-throughput-configuration-parameters"></a>Spark コネクタのスループット構成パラメーター

次の表に、コネクタに用意されている Azure Cosmos DB Cassandra API 固有のスループット構成パラメーターを示します。 すべての構成パラメーターの詳細な一覧については、Spark Cassandra Connector GitHub リポジトリの[構成リファレンス](https://github.com/datastax/spark-cassandra-connector/blob/master/doc/reference.md)に関するページを参照してください。

| **プロパティ名** | **既定値** | **説明** |
|---------|---------|---------|
| spark.cassandra.output.batch.size.rows |  1 |バッチあたりの行数。 このパラメーターを 1 に設定します。 このパラメーターは、ワークロードが高い場合に高いスループットを達成するために使用されます。 |
| spark.cassandra.connection.connections_per_executor_max  | なし | Executor あたりの各ノードの最大接続数。 10*n は、n ノード Cassandra クラスター内のノードあたりの 10 接続に相当します。 そのため、5 ノードの Cassandra クラスターで、Executor あたりの各ノードに 5 接続が必要な場合は、この構成を 25 に設定する必要があります。 この値は、Spark ジョブを構成する並列処理の次数または Executor の数に基づいて変更します。   |
| spark.cassandra.output.concurrent.writes  |  100 | Executor あたりで発生する可能性がある並列書き込み数を定義します。 "batch.size.rows" を 1 に設定しているので、それに従ってこの値をスケール アップします。 ワークロードのために達成したい並列処理の次数またはスループットに基づいて、この値を変更します。 |
| spark.cassandra.concurrent.reads |  512 | Executor あたりで実行できる並列読み取りの数を定義します。 ワークロードのために達成したい並列処理の次数またはスループットに基づいて、この値を変更します  |
| spark.cassandra.output.throughput_mb_per_sec  | なし | Executor あたりの合計書き込みスループットを定義します。 このパラメーターは、Spark ジョブのスループットの上限として使用できます。また、Cosmos DB Collection のプロビジョニングされたスループットに基づいています。   |
| spark.cassandra.input.reads_per_sec| なし   | Executor あたりの合計読み取りスループットを定義します。 このパラメーターは、Spark ジョブのスループットの上限として使用できます。また、Cosmos DB Collection のプロビジョニングされたスループットに基づいています。  |
| spark.cassandra.output.batch.grouping.buffer.size |  1,000  | Cassandra API に送信する前にメモリに格納できる単一の Spark タスクあたりのバッチ数を定義します |
| spark.cassandra.connection.keep_alive_ms | 60000 | 使用されていない接続が使用可能になるまでの時間を定義します。 | 

Spark ジョブに期待するワークロードと、Cosmos DB アカウント用にプロビジョニングしたスループットに基づいて、これらのパラメーターのスループットと並列処理の次数を調整します。

## <a name="connecting-to-azure-cosmos-db-cassandra-api-from-spark"></a>Spark から Azure Cosmos DB Cassandra API に接続する

### <a name="cqlsh"></a>cqlsh
次のコマンドは、cqlsh から Azure CosmosDB Cassandra API に接続する詳しい方法を示しています。  これは、Spark でサンプルを実行するときの検証に役立ちます。<br>
**Linux/Unix/Mac から:**

```bash
export SSL_VERSION=TLSv1_2
export SSL_VALIDATE=false
cqlsh.py YOUR-COSMOSDB-ACCOUNT-NAME.cassandra.cosmosdb.azure.com 10350 -u YOUR-COSMOSDB-ACCOUNT-NAME -p YOUR-COSMOSDB-ACCOUNT-KEY --ssl
```

### <a name="1--azure-databricks"></a>1.Azure Databricks
以下の記事では、Azure Databricks クラスターのプロビジョニング、Azure Cosmos DB Cassandra API に接続するためのクラスター構成、および DDL 操作、DML 操作などに関するいくつかのサンプル ノートブックについて説明します。<BR>
[Azure Databricks から Azure Cosmos DB Cassandra API を使用する](cassandra-spark-databricks.md)<BR>
  
### <a name="2--azure-hdinsight-spark"></a>2.Azure HDInsight-Spark
以下の記事では、HDinsight-Spark サービス、プロビジョニング、Azure Cosmos DB Cassandra API に接続するためのクラスター構成、および DDL 操作、DML 操作などに関するいくつかのサンプル ノートブックについて説明します。<BR>
[Azure HDInsight-Spark から Azure Cosmos DB Cassandra API を使用する](cassandra-spark-hdinsight.md)
 
### <a name="3--spark-environment-in-general"></a>手順 3.一般的な Spark 環境
上記のセクションは Azure Spark ベースの PaaS サービスに固有の内容ですが、このセクションでは一般的な Spark 環境について説明します。  コネクタの依存関係、インポート、Spark セッション構成について、以下で詳しく説明します。 「次の手順」セクションでは、DDL 操作、DML 操作などのコード サンプルについて説明します。  

#### <a name="connector-dependencies"></a>コネクタの依存関係:

1. [Spark 用の Cassandra コネクタ](cassandra-spark-generic.md#dependencies-for-connectivity)を取得する Maven 座標を追加します
2. Cassandra API 用の [Azure Cosmos DB ヘルパー ライブラリ](cassandra-spark-generic.md#dependencies-for-connectivity)の Maven 座標を追加します

#### <a name="imports"></a>インポート:

```scala
import org.apache.spark.sql.cassandra._
//Spark connector
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql.CassandraConnector

//CosmosDB library for multiple retry
import com.microsoft.azure.cosmosdb.cassandra
```

#### <a name="spark-session-configuration"></a>Spark セッションの構成:

```scala
//Connection-related
spark.conf.set("spark.cassandra.connection.host","YOUR_ACCOUNT_NAME.cassandra.cosmosdb.azure.com")
spark.conf.set("spark.cassandra.connection.port","10350")
spark.conf.set("spark.cassandra.connection.ssl.enabled","true")
spark.conf.set("spark.cassandra.auth.username","YOUR_ACCOUNT_NAME")
spark.conf.set("spark.cassandra.auth.password","YOUR_ACCOUNT_KEY")
spark.conf.set("spark.cassandra.connection.factory", "com.microsoft.azure.cosmosdb.cassandra.CosmosDbConnectionFactory")

//Throughput-related. You can adjust the values as needed
spark.conf.set("spark.cassandra.output.batch.size.rows", "1")
spark.conf.set("spark.cassandra.connection.connections_per_executor_max", "10")
spark.conf.set("spark.cassandra.output.concurrent.writes", "1000")
spark.conf.set("spark.cassandra.concurrent.reads", "512")
spark.conf.set("spark.cassandra.output.batch.grouping.buffer.size", "1000")
spark.conf.set("spark.cassandra.connection.keep_alive_ms", "600000000")
```

## <a name="next-steps"></a>次の手順

以下の記事は、Spark と Azure Cosmos DB Cassandra API の統合方法を示しています。 
 
* [DDL 操作](cassandra-spark-ddl-ops.md)
* [操作を作成/挿入する](cassandra-spark-create-ops.md)
* [操作を読み取る](cassandra-spark-read-ops.md)
* [操作を Upsert する](cassandra-spark-upsert-ops.md)
* [操作を削除する](cassandra-spark-delete-ops.md)
* [集計操作](cassandra-spark-aggregation-ops.md)
* [テーブル コピー操作](cassandra-spark-table-copy-ops.md)
