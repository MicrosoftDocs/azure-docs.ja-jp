---
title: Azure Cosmos DB と HDInsight でのラムダ アーキテクチャ (Apache Spark)
description: この記事では、Azure Cosmos DB、HDInsight、Spark を使ってラムダ アーキテクチャを実装する方法について説明します
ms.service: cosmos-db
author: tknandu
ms.author: ramkris
ms.topic: conceptual
ms.date: 01/19/2018
ms.openlocfilehash: 6902b1a26d02efbf1a31fe9a3a25253a6b5a5604
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58100345"
---
# <a name="azure-cosmos-db-implement-a-lambda-architecture-on-the-azure-platform"></a>Azure Cosmos DB は:Azure プラットフォームでラムダ アーキテクチャを実装する 

ラムダ アーキテクチャを使うと、大規模なデータ セットのデータ処理を効率よく行うことができます。 ラムダ アーキテクチャは、バッチ処理、ストリーム処理、およびサービス レイヤーを使って、ビッグ データのクエリに伴う待機時間を最小限にします。 

Azure にラムダ アーキテクチャを実装するには、以下のテクノロジを組み合わせて、リアルタイムのビッグ データ分析を高速化できます。
* [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/): 業界初のグローバルに配布されるマルチモデル データベース サービスです。 
* [Apache Spark for Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-spark/): 大規模データ分析アプリケーションを実行する処理フレームワークです
* Azure Cosmos DB [変更フィード](change-feed.md): HDInsight が処理できるように新しいデータをバッチ レイヤーにストリームします
* [Spark-Azure Cosmos DB コネクタ](spark-connector.md)

この記事では、オリジナルのマルチレイヤー設計に基づくラムダ アーキテクチャの基礎と、操作を簡略化する "再設計された" ラムダ アーキテクチャの利点について説明します。  

## <a name="what-is-a-lambda-architecture"></a>ラムダ アーキテクチャとは
ラムダ アーキテクチャは、[Nathan Marz](https://twitter.com/nathanmarz) が説明しているように、バッチ処理と待機時間高速化のシナリオのための、汎用的でスケーラブルなフォールト トレラントのデータ処理アーキテクチャです。

![ラムダ アーキテクチャを示す図](./media/lambda-architecture/lambda-architecture-intro.png)

ソース: http://lambda-architecture.net/

ラムダ アーキテクチャの基本原理は、[http://lambda-architecture.net](http://lambda-architecture.net/) から提供されている前記の図で説明されているようなものです。

 1. すべての**データ**は、"*バッチ レイヤー*" と "*スピード レイヤー*" の "*両方*" にプッシュされます。
 2. **バッチ レイヤー**にはマスター データセット (生データの変更不能な追加専用のセット) があり、バッチ ビューを事前計算します。
 3. **サービス レイヤー**は、高速クエリのためのバッチ ビューを保持しています。 
 4. **スピード レイヤー**は、(サービス レイヤーへの) 処理時間を補償して、最新のデータのみを処理します。
 5. すべてのクエリの応答は、バッチ ビューとリアルタイム ビューの結果をマージすることで、またはそれらを個別に ping することで得られます。

参考として、次のものだけを使ってこのアーキテクチャを実装できます。

* Azure Cosmos DB コレクション
* HDInsight (Apache Spark 2.1) クラスター
* Spark コネクタ [1.0](https://github.com/Azure/azure-cosmosdb-spark/tree/master/releases/azure-cosmosdb-spark_2.1.0_2.11-1.0.0)

## <a name="speed-layer"></a>スピード レイヤー

操作の観点からは、データの正しい状態を確保しながらデータの 2 つのストリームを維持するのは、複雑な作業です。 操作を簡素化するため、[Azure Cosmos DB の変更フィードのサポート](change-feed.md)を利用して "*バッチ レイヤー*" の状態を維持しながら、*Change Feed API* を使って Azure Cosmos DB の変更ログを "*スピード レイヤー*" に提供します。  
![ラムダ アーキテクチャの新しいデータ、スピード レイヤー、マスター データセットの部分を強調表示した図](./media/lambda-architecture/lambda-architecture-change-feed.png)

これらのレイヤーでは以下のことが重要です。

 1. すべての**データ**は Azure Cosmos DB に対して "*のみ*" プッシュされるので、マルチキャストの問題を回避できます。
 2. **バッチ レイヤー**にはマスター データセット (生データの変更不能な追加専用のセット) があり、バッチ ビューを事前計算します。
 3. **サービス レイヤー**については次のセクションで説明します。
 4. **スピード レイヤー**は HDInsight (Apache Spark) を利用して、Azure Cosmos DB の変更フィードを読み取ります。 これにより、データを永続化しながら、データを同時にクエリおよび処理できます。
 5. すべてのクエリの応答は、バッチ ビューとリアルタイム ビューの結果をマージすることで、またはそれらを個別に ping することで得られます。
 
### <a name="code-example-spark-structured-streaming-to-an-azure-cosmos-db-change-feed"></a>コード例:Azure Cosmos DB 変更フィードへの Spark 構造化ストリーム
**スピード レイヤー**の一部としての Azure Cosmos DB 変更フィードの簡単なプロトタイプを実行するには、「[Stream Processing Changes using Azure Cosmos DB Change Feed and Apache Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)」(Azure Cosmos DB 変更フィードと Apache Spark を使用したストリーム処理の変更) の例の一部として Twitter データを使ってテストできます。 Twitter の出力をすぐに始めるには、「[Stream feed from Twitter to Cosmos DB](https://github.com/tknandu/TwitterCosmosDBFeed)」(Twitter から Cosmos DB へのストリーム フィード) のコード サンプルをご覧ください。 前の例では、Twitter のデータを Azure Cosmos DB に読み込んでおり、変更フィードに接続するように HDInsight (Apache Spark) クラスターを設定することができます。 この構成を設定する方法について詳しくは、「[Apache Spark to Azure Cosmos DB Connector Setup](https://github.com/Azure/azure-cosmosdb-spark/wiki/Spark-to-Cosmos-DB-Connector-Setup)」(Apache Spark から Azure Cosmos DB へのコネクタのセットアップ) をご覧ください。  

次のコード スニペットでは、構造化ストリーミング ジョブを実行して Azure Cosmos DB 変更フィード (リアルタイムの Twitter データ ストリームをレビューします) に接続し、実行間隔のカウントを実行するように、`spark-shell` を構成する方法を示します。

```
// Import Libraries
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark.config.Config
import org.codehaus.jackson.map.ObjectMapper
import com.microsoft.azure.cosmosdb.spark.streaming._
import java.time._


// Configure connection to Azure Cosmos DB Change Feed
val sourceConfigMap = Map(
"Endpoint" -> "[COSMOSDB ENDPOINT]",
"Masterkey" -> "[MASTER KEY]",
"Database" -> "[DATABASE]",
"Collection" -> "[COLLECTION]",
"ConnectionMode" -> "Gateway",
"ChangeFeedCheckpointLocation" -> "checkpointlocation",
"changefeedqueryname" -> "Streaming Query from Cosmos DB Change Feed Interval Count")

// Start reading change feed as a stream
var streamData = spark.readStream.format(classOf[CosmosDBSourceProvider].getName).options(sourceConfigMap).load()

// Start streaming query to console sink
val query = streamData.withColumn("countcol", streamData.col("id").substr(0, 0)).groupBy("countcol").count().writeStream.outputMode("complete").format("console").start()
```

完全なコード サンプルについては、[azure-cosmosdb-spark/samples/lambda](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda) をご覧ください。次のものが含まれます。
* [Streaming Query from Cosmos DB Change Feed.scala](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Query%20from%20Cosmos%20DB%20Change%20Feed.scala)
* [Streaming Tags Query from Cosmos DB Change Feed.scala](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Tags%20Query%20from%20Cosmos%20DB%20Change%20Feed%20.scala)

これの出力は `spark-shell` コンソールであり、Azure Cosmos DB 変更フィードからの Twitter データに対して間隔カウントを実行する構造化ストリーミング ジョブを継続的に実行します。 次の図では、ストリーム ジョブと間隔カウントの出力を示します。

![Azure Cosmos DB 変更フィードからの Twitter データに対する間隔カウントを示すストリーミング出力](./media/lambda-architecture/lambda-architecture-speed-layer-twitter-count.png) 

Azure Cosmos DB 変更フィードについて詳しくは、以下をご覧ください。

* [Azure Cosmos DB での Change Feed サポートの使用](change-feed.md)
* [Azure Cosmos DB 変更フィード プロセッサ ライブラリの概要](https://azure.microsoft.com/blog/introducing-the-azure-cosmosdb-change-feed-processor-library/)
* [ストリーム処理の変更: Azure CosmosDB 変更フィード + Apache Spark](https://azure.microsoft.com/blog/stream-processing-changes-azure-cosmosdb-change-feed-apache-spark/)

## <a name="batch-and-serving-layers"></a>バッチ レイヤーとサービス レイヤー
新しいデータは Azure Cosmos DB に読み込まれるので (ここでは、スピード レイヤーに対して変更フィードが使われています)、**マスター データセット** (生データの変更不能な追加専用のセット) はここに存在します。 ここからは、次の図に示すように、HDInsight (Apache Spark) を使って、**バッチ レイヤー**から**サービス レイヤー**までの事前計算機能を実行します。

![ラムダ アーキテクチャのバッチ レイヤーとサービス レイヤーが強調表示されている図](./media/lambda-architecture/lambda-architecture-batch-serve.png)

これらのレイヤーでは以下のことが重要です。

 1. すべての**データ**は、Azure Cosmos DB に対してのみプッシュされます (マルチキャストの問題を回避するため)。
 2. **バッチ レイヤー**では、マスター データセット (生データの変更不能な追加専用のセット) が Azure Cosmos DB に格納されています。 HDI Spark を使うことで、集計を事前計算して計算済みのバッチ ビューに格納できます。
 3. **サービス レイヤー**は、マスター データセットと計算済みバッチ ビューのコレクションを含む Azure Cosmos DB データベースです。
 4. **スピード レイヤー**については後で説明します。
 5. すべてのクエリの応答は、バッチ ビューとリアルタイム ビューの結果をマージすることで、またはそれらを個別に ping することで得られます。

### <a name="code-example-pre-computing-batch-views"></a>コード例:バッチ ビューの事前計算
Apache Spark から Azure Cosmos DB への**マスター データセット**に対して事前計算ビューを実行する方法を示すため、「[Lambda Architecture Rearchitected - Batch Layer](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb)」(ラムダ アーキテクチャの再設計 - バッチ レイヤー) および「[Lambda Architecture Rearchitected - Batch to Serving Layer](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb)」(ラムダ アーキテクチャの再設計 - バッチからサービス レイヤー) ノートブックの以下のコード スニペットを使います。 このシナリオでは、Azure Cosmos DB に格納されている Twitter データを使います。

最初に、次の PySpark コードを使って Azure Cosmos DB 内の Twitter データへの構成接続を作成します。

```
# Configuration to connect to Azure Cosmos DB
tweetsConfig = {
  "Endpoint" : "[Endpoint URL]",
  "Masterkey" : "[Master Key]",
  "Database" : "[Database]",
  "Collection" : "[Collection]", 
  "preferredRegions" : "[Preferred Regions]",
  "SamplingRatio" : "1.0",
  "schema_samplesize" : "200000",
  "query_custom" : "[Cosmos DB SQL Query]"
}

# Create DataFrame
tweets = spark.read.format("com.microsoft.azure.cosmosdb.spark").options(**tweetsConfig).load()

# Create Temp View (to run Spark SQL statements)
tweets.createOrReplaceTempView("tweets")
```

次に、次の Spark SQL ステートメントを実行して、ツイートのセットの上位 10 ハッシュタグを決定します。 この Spark SQL クエリでは、このコード スニペットの直後にある出力横棒グラフを除き、Jupyter ノートブックでこれを実行します。

```
%%sql
select hashtags.text, count(distinct id) as tweets
from (
  select 
    explode(hashtags) as hashtags,
    id
  from tweets
) a
group by hashtags.text
order by tweets desc
limit 10
```

![ハッシュタグあたりのツイート数を示すグラフ](./media/lambda-architecture/lambda-architecture-batch-hashtags-bar-chart.png)

クエリができたので、Spark コネクタを使ってそれを元のコレクションに保存し、出力データを別のコレクションに保存します。  この例では、Scala を使って接続を示します。 前の例と同様に、Apache Spark データ フレームを別の Azure Cosmos DB コレクションに保存する構成接続を作成します。

```
val writeConfigMap = Map(
    "Endpoint" -> "[Endpoint URL]",
    "Masterkey" -> "[Master Key]",
    "Database" -> "[Database]",
    "Collection" -> "[New Collection]", 
    "preferredRegions" -> "[Preferred Regions]",
    "SamplingRatio" -> "1.0",
    "schema_samplesize" -> "200000"
)

// Configuration to write
val writeConfig = Config(writeConfigMap)

```

`SaveMode` (ドキュメントの `Overwrite` か `Append` かを示します) を指定した後、前の例の Spark SQL クエリと似ている `tweets_bytags` データ フレームを作成します。  `tweets_bytags` データ フレームが作成されたら、前に指定した `writeConfig` を使って `write` メソッドでそれを保存できます。

```
// Import SaveMode so you can Overwrite, Append, ErrorIfExists, Ignore
import org.apache.spark.sql.{Row, SaveMode, SparkSession}

// Create new DataFrame of tweets tags
val tweets_bytags = spark.sql("select hashtags.text as hashtags, count(distinct id) as tweets from ( select explode(hashtags) as hashtags, id from tweets ) a group by hashtags.text order by tweets desc")

// Save to Cosmos DB (using Append in this case)
tweets_bytags.write.mode(SaveMode.Overwrite).cosmosDB(writeConfig)
```

この最後のステートメントでは、Spark データ フレームを新しい Azure Cosmos DB コレクションに保存しています。ラムダ アーキテクチャの観点からは、これは**サービス レイヤー**内の**バッチ ビュー**です。
 
#### <a name="resources"></a>リソース

完全なコード サンプルについては、[azure-cosmosdb-spark/samples/lambda](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda) をご覧ください。次のものが含まれます。
* ラムダ アーキテクチャの再設計 - バッチ レイヤー [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.html) | [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb)
* ラムダ アーキテクチャの再設計 - バッチからサービス レイヤー [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.html) | [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb)

## <a name="speed-layer"></a>スピード レイヤー
前に説明したように、Azure Cosmos DB 変更フィード ライブラリを使うと、バッチ レイヤーとスピード レイヤーの間の操作を簡素化することができます。 このアーキテクチャでは、Apache Spark (HDInsight 経由) を使って、データに対して "*構造化ストリーミング*" クエリを実行します。 他のシステムがこのデータにアクセスできるように、構造化ストリーミング クエリの結果を一時的に保持することもできます。

![ラムダ アーキテクチャのスピード レイヤーが強調表示されている図](./media/lambda-architecture/lambda-architecture-speed.png)

これを行うには、別の Azure Cosmos DB コレクションを作成して、構造化ストリーミング クエリの結果を保存します。  これにより、Apache Spark だけでなく他のシステムもこの情報にアクセスできるようになります。 Cosmos DB の Time-to-Live (TTL) 機能と同様に、設定した期間の後で自動的に削除されるようにドキュメントを構成できます。  Azure Cosmos DB の TTL 機能について詳しくは、「[TTL (Time to Live) を使って Azure Cosmos DB コレクションのデータの有効期限が自動的に切れるようにする](time-to-live.md)」をご覧ください

```
// Import Libraries
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark.config.Config
import org.codehaus.jackson.map.ObjectMapper
import com.microsoft.azure.cosmosdb.spark.streaming._
import java.time._


// Configure connection to Azure Cosmos DB Change Feed
val sourceCollectionName = "[SOURCE COLLECTION NAME]"
val sinkCollectionName = "[SINK COLLECTION NAME]"

val configMap = Map(
"Endpoint" -> "[COSMOSDB ENDPOINT]",
"Masterkey" -> "[COSMOSDB MASTER KEY]",
"Database" -> "[DATABASE NAME]",
"Collection" -> sourceCollectionName,
"ChangeFeedCheckpointLocation" -> "changefeedcheckpointlocation")

val sourceConfigMap = configMap.+(("changefeedqueryname", "Structured Stream replication streaming test"))

// Start to read the stream
var streamData = spark.readStream.format(classOf[CosmosDBSourceProvider].getName).options(sourceConfigMap).load()
val sinkConfigMap = configMap.-("collection").+(("collection", sinkCollectionName))

// Start the stream writer to new collection
val streamingQueryWriter = streamData.writeStream.format(classOf[CosmosDBSinkProvider].getName).outputMode("append").options(sinkConfigMap).option("checkpointLocation", "streamingcheckpointlocation")
var streamingQuery = streamingQueryWriter.start()

```

## <a name="lambda-architecture-rearchitected"></a>ラムダ アーキテクチャ: 再設計済み
前のセクションで説明したように、次のコンポーネントを使うことで、元のラムダ アーキテクチャを簡略化できます。
* Azure Cosmos DB
* バッチ レイヤーとスピード レイヤーの間でデータをマルチキャストする必要がないようにする、Azure Cosmos DB 変更フィード ライブラリ
* HDInsight 上の Apache Spark
* Azure Cosmos DB 用 Spark コネクタ

![Azure Cosmos DB、Spark、Azure Cosmos DB 変更フィード API を使ったラムダ アーキテクチャの再設計を示す図](./media/lambda-architecture/lambda-architecture-re-architected.png)

この設計で必要な管理対象サービスは、Azure Cosmos DB と HDInsight の 2 つだけです。 これらが連携して、ラムダ アーキテクチャのバッチ レイヤー、サービス レイヤー、スピード レイヤーを処理します。 これにより、操作だけでなくデータ フローも簡略化されます。 
 1. すべてのデータは処理のために Azure Cosmos DB にプッシュされます
 2. バッチ レイヤーにはマスター データセット (生データの変更不能な追加専用のセット) があり、バッチ ビューを事前計算します
 3. サービス レイヤーは、高速クエリのためにデータのバッチ ビューを保持しています。
 4. スピード レイヤーは、(サービス レイヤーへの) 処理時間を補償して、最新のデータのみを処理します。
 5. すべてのクエリの応答は、バッチ ビューとリアルタイム ビューの結果をマージすることで得られます。

### <a name="resources"></a>リソース

* **新しいデータ**: [Twitter から CosmosDB へのストリーム フィード](https://github.com/tknandu/TwitterCosmosDBFeed)は、新しいデータを Azure Cosmos DB にプッシュするためのメカニズムです。
* **バッチ レイヤー:** バッチ レイヤーは、"*マスター データセット*" (生データの変更不能な追加専用のセット) と、**サービス レイヤー**にプッシュされるデータのバッチ ビューを事前計算する機能で構成されます。
   * 「**Lambda Architecture Rearchitected - Batch Layer**」(ラムダ アーキテクチャの再設計 - バッチ レイヤー) ノートブック ([ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.html)) は、バッチのビューの "*マスター データセット*" セットをクエリします。
* **サービス レイヤー:** **サービス レイヤー**は、高速クエリのために事前計算されたデータによるバッチ ビュー (集計、特定のスライサーなど) で構成されます。
  * 「**Lambda Architecture Rearchitected - Batch to Serving Layer**」(ラムダ アーキテクチャの再設計 - バッチからサービス レイヤー) ノートブック ([ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.html)) では、サービス レイヤーにバッチ データをプッシュしています。つまり、Spark はツイートのバッチ コレクションをクエリし、処理して、別のコレクション (計算済みバッチ) に格納します。
    * **スピード レイヤー:** **スピード レイヤー**は、Azure Cosmos DB の変更フィードを利用して読み取ってすぐに処理する Spark で構成されます。 他のシステムがリアルタイム クエリを自分で実行するのではなく処理済みのリアルタイム データをクエリできるように、データを "*計算済み RT*" に保存することもできます。
  * [Streaming Query from Cosmos DB Change Feed](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Query%20from%20Cosmos%20DB%20Change%20Feed.scala) (Cosmos DB 変更フィードからのストリーミング クエリ) Scala スクリプトは、Azure Cosmos DB 変更フィードからストリーミング クエリを実行し、spark-shell から間隔カウントを計算します。
  * [Streaming Tags Query from Cosmos DB Change Feed](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Tags%20Query%20from%20Cosmos%20DB%20Change%20Feed%20.scala) (Cosmos DB 変更フィードからのストリーミング タグ クエリ) Scala スクリプトは、Azure Cosmos DB 変更フィードからストリーミング クエリを実行し、spark-shell からのタグの間隔カウントを計算します。
  
## <a name="next-steps"></a>次の手順
Spark-Azure Cosmos DB コネクタをまだダウンロードしていない場合は、そのコネクタを [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark) GitHub リポジトリからダウンロードし、リポジトリでその他のリソースを調べる。
* [ラムダ アーキテクチャ](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda)
* [分散集計の例](https://github.com/Azure/azure-documentdb-spark/wiki/Aggregations-Examples)
* [サンプル スクリプトと Notebook](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)
* [構造化ストリーミングのデモ](https://github.com/Azure/azure-cosmosdb-spark/wiki/Structured-Stream-demos)
* [変更フィードのデモ](https://github.com/Azure/azure-cosmosdb-spark/wiki/Change-Feed-demos)
* [Azure CosmosDB 変更フィードと Apache Spark を使用したストリーム処理の変更](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)

また、[Apache Spark SQL、DataFrames、データセット ガイド](https://spark.apache.org/docs/latest/sql-programming-guide.html)と [Azure HDInsight 上の Apache Spark](../hdinsight/spark/apache-spark-jupyter-spark-sql.md) に関する記事を確認することもできます。
