---
title: "Apache Spark を Azure Cosmos DB に接続する | Microsoft Docs"
description: "このチュートリアルでは、Apache Spark を Azure Cosmos DB に接続して、クラウド向けに設計されたマルチテナント グローバル分散データベース システムで Microsoft から分散集計やデータ サイエンスを実行できる、Azure Cosmos DB 用 Spark コネクタについて説明します。"
keywords: Apache Spark
services: cosmos-db
documentationcenter: 
author: dennyglee
manager: jhubbard
editor: 
ms.assetid: c4f46007-2606-4273-ab16-29d0e15c0736
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: denlee
ms.translationtype: HT
ms.sourcegitcommit: 847eb792064bd0ee7d50163f35cd2e0368324203
ms.openlocfilehash: 8ecbb478c81cde25bbd0d1c9ee07ae02b07f8cc7
ms.contentlocale: ja-jp
ms.lasthandoff: 08/19/2017

---

# <a name="accelerate-real-time-big-data-analytics-with-the-spark-to-azure-cosmos-db-connector"></a>Spark-Azure Cosmos DB コネクタによるリアルタイムのビッグ データ分析の高速化

Spark-Azure Cosmos DB コネクタを使用すると、Azure Cosmos DB は Apache Spark ジョブの入力ソースまたは出力シンクとして機能することができます。 [Spark](http://spark.apache.org/) を [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) に接続すると、Azure Cosmos DB を使用してデータをすばやく保持し、クエリを実行できるため、目まぐるしく変化するデータ サイエンスの問題を迅速に解決できます。 この Spark-Cosmos DB コネクタは、Azure Cosmos DB で管理されるネイティブのインデックスを効率的に活用し、 モノのインターネット (IoT)、データ サイエンス、分析などのシナリオにおいて、急速に変化するグローバル分散データに対して、分析の実行時に更新可能な列や、述語によるプッシュダウン フィルターを利用できるようにします。

Spark GraphX および Azure Cosmos DB の Gremlin グラフ API を使用するには、[Spark および Apache TinkerPop Gremlin を使用したグラフの分析の実行](spark-connector-graph.md)に関するページをご覧ください。

## <a name="download"></a>ダウンロード

最初に、GitHub の [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark/) リポジトリから Spark-Azure Cosmos DB コネクタ (プレビュー) をダウンロードします。

## <a name="connector-components"></a>コネクタ コンポーネント

コネクタでは次のコンポーネントを使用します。

* [Azure Cosmos DB](http://documentdb.com): 多数の地理的リージョン間でスループットとストレージをプロビジョニングし、弾力的に拡大縮小できます。 このサービスにより次が実現します。
   * ターン キー [グローバル分散](distribute-data-globally.md)と水平スケール
   * 99 パーセンタイルの処理での 10 ミリ秒未満の待機時間を保証
   * [明確に定義された複数の整合性モデルが](consistency-levels.md)
   * マルチホーム機能を備えた高可用性を保証
   * すべての機能が、業界をリードする包括的な[サービス レベル アグリーメント](https://azure.microsoft.com/support/legal/sla/cosmos-db) (SLA) でサポートされます。

* [Apache Spark](http://spark.apache.org/): 速度、使いやすさ、高度な分析に重点を置いて構築された強力なオープン ソース処理エンジンです。

* [Azure HDInsight のApache Spark](../hdinsight/hdinsight-apache-spark-jupyter-spark-sql.md): [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-spark/) を使用して Apache Spark をクラウドにデプロイし、ミッション クリティカルなデプロイを実現できます。

正式にサポートされているバージョンは次のとおりです。

| コンポーネント | バージョン |
|---------|-------|
|Apache Spark|2.0+|
| Scala| 2.11|
| Azure DocumentDB Java SDK | 1.10.0 |

この記事は、Python (pyDocumentDB を使用) と Scala インターフェイスを使用して、簡単なサンプルを実行する際に役立ちます。

Apache Spark と Azure Cosmos DB は次の 2 とおりの方法で接続できます。
- [Azure DocumentDB Python SDK](https://github.com/Azure/azure-documentdb-python) で pyDocumentDB を使用する。
- [Azure DocumentDB Java SDK](https://github.com/Azure/azure-documentdb-java) を使用して、Java ベースの Spark-Azure Cosmos DB コネクタを作成する。

## <a name="pydocumentdb-implementation"></a>pyDocumentDB 実装
最新の [pyDocumentDB SDK](https://github.com/Azure/azure-documentdb-python) を使用すると、次の図に示すように Spark を Azure Cosmos DB に接続できます。

![pyDocumentDB DB を介した Spark と Azure Cosmos DB 間のデータ フロー](./media/spark-connector/spark-pydocumentdb.png)


### <a name="data-flow-of-the-pydocumentdb-implementation"></a>pyDocumentDB 実装のデータ フロー

データ フローは次のとおりです。

1. Spark マスター ノードは、pyDocumentDB を介して Azure Cosmos DB ゲートウェイ ノードに接続されます。 ユーザーが指定するのは、Spark と Azure Cosmos DB 接続だけです。 それぞれのマスター ノードとゲートウェイ ノードへの接続は、ユーザーに対して透過的に行われます。
2. ゲートウェイ ノードにより、Azure Cosmos DB に対するクエリが作成されます。その後、このクエリはデータ ノード内のコレクションのパーティションに対して実行されます。 これらのクエリに対する応答がゲートウェイ ノードに送信され、その結果セットが Spark マスター ノードに返されます。
3. 後続のクエリ (Spark DataFrame に対するクエリなど) が Spark worker ノードに送信され、処理されます。

Spark と Azure Cosmos DB 間の通信が Spark マスター ノードと Azure Cosmos DB ゲートウェイ ノードに制限されます。  クエリは、この 2 つのノード間のトランスポート層で許可される速度で実行されます。

### <a name="install-pydocumentdb"></a>pyDocumentDB のインストール
次のように、**pip** を使用して、pyDocumentDB をドライバー ノードにインストールできます。

```
pip install pyDocumentDB
```


### <a name="connect-spark-to-azure-cosmos-db-via-pydocumentdb"></a>pyDocumentDB DB を介して Spark を Azure Cosmos DB に接続
通信転送がシンプルであるため、pyDocumentDB を使用した Spark から Azure Cosmos DB へのクエリの実行が比較的シンプルになります。

次のコード スニペットは、Spark コンテキストで pyDocumentDB を使用する方法を示しています。

```
# Import Necessary Libraries
import pydocumentdb
from pydocumentdb import document_client
from pydocumentdb import documents
import datetime

# Configuring the connection policy (allowing for endpoint discovery)
connectionPolicy = documents.ConnectionPolicy()
connectionPolicy.EnableEndpointDiscovery
connectionPolicy.PreferredLocations = ["Central US", "East US 2", "Southeast Asia", "Western Europe","Canada Central"]


# Set keys to connect to Azure Cosmos DB
masterKey = 'le1n99i1w5l7uvokJs3RT5ZAH8dc3ql7lx2CG0h0kK4lVWPkQnwpRLyAN0nwS1z4Cyd1lJgvGUfMWR3v8vkXKA=='
host = 'https://doctorwho.documents.azure.com:443/'
client = document_client.DocumentClient(host, {'masterKey': masterKey}, connectionPolicy)
```

このコード スニペットでは次のことを示しています。

* Azure Cosmos DB Python SDK (`pyDocumentDB`) には、必要な接続パラメーターがすべて含まれています。 たとえば、優先場所パラメーターは、読み取りレプリカと優先順位を選択します。
*  必要なライブラリをインポートし、**masterKey** と **host** を構成して Azure Cosmos DB "*クライアント*" (**pydocumentdb.document_client**) を作成します。


### <a name="execute-spark-queries-via-pydocumentdb"></a>pyDocumentDB を介した Spark クエリの実行
以下の例では、指定した読み取り専用キーを使って、前のスニペットで作成された Azure Cosmos DB インスタンスを使用します。 次のコード スニペットは、以前に指定した DoctorWho アカウント内の **airports.codes** コレクションに接続して、ワシントン州の空港都市を抽出するクエリを実行します。

```
# Configure Database and Collections
databaseId = 'airports'
collectionId = 'codes'

# Configurations the Azure Cosmos DB client will use to connect to the database and collection
dbLink = 'dbs/' + databaseId
collLink = dbLink + '/colls/' + collectionId

# Set query parameter
querystr = "SELECT c.City FROM c WHERE c.State='WA'"

# Query documents
query = client.QueryDocuments(collLink, querystr, options=None, partition_key=None)

# Query for partitioned collections
# query = client.QueryDocuments(collLink, query, options= { 'enableCrossPartitionQuery': True }, partition_key=None)

# Push into list `elements`
elements = list(query)
```

**query** によってクエリが実行されると、結果は、Python リストに変換される **query_iterable.QueryIterable** になります。 Python リストは、次のコードを使用して、Spark DataFrame に簡単に変換できます。

```
# Create `df` Spark DataFrame from `elements` Python list
df = spark.createDataFrame(elements)
```

### <a name="why-use-the-pydocumentdb-to-connect-spark-to-azure-cosmos-db"></a>pyDocumentDB を使用して Spark を Azure Cosmos DB に接続する理由
pyDocumentDB を使用して Spark を Azure Cosmos DB に接続するのは、一般に次のような場合です。

* Python を使用する場合。
* Azure Cosmos DB から比較的小規模の結果セットを Spark に返す場合。 Azure Cosmos DB 内の基になるデータセットが非常に大きい可能性があります。 Azure Cosmos DB ソースに対してフィルターを適用、つまり、述語フィルターを実行します。  

## <a name="spark-to-azure-cosmos-db-connector"></a>Spark-Azure Cosmos DB コネクタ

Spark-Azure Cosmos DB コネクタは [Azure DocumentDB Java SDK](https://github.com/Azure/azure-documentdb-java) を使用し、次の図に示すように Spark worker ノードと Azure Cosmos DB 間でデータを移動します。

![Spark-Azure Cosmos DB コネクタのデータ フロー](./media/spark-connector/spark-connector.png)

データ フローは次のとおりです。

1. Spark マスター ノードは、Azure Cosmos DB ゲートウェイ ノードに接続して、パーティション マップを取得します。 ユーザーが指定するのは、Spark と Azure Cosmos DB 接続だけです。 それぞれのマスター ノードとゲートウェイ ノードへの接続は、ユーザーに対して透過的に行われます。
2. この情報が Spark マスター ノードに返されます。  この時点で、クエリを解析して、アクセスする必要がある Azure Cosmos DB 内のパーティションとそのパーティションの場所を特定できます。
3. この情報が Spark worker ノードに送信されます。
4. Spark worker ノードは Azure Cosmos DB のパーティションに直接接続してデータを抽出し、そのデータを Spark worker ノードの Spark パーティションに返します。

データの移動は Spark worker ノードとAzure Cosmos DB データ ノード (パーティション) の間で行われるため、Spark と Azure Cosmos DB の間の通信が大幅に高速化されます。

### <a name="build-the-spark-to-azure-cosmos-db-connector"></a>Spark-Azure Cosmos DB コネクタの構築
現在、コネクタ プロジェクトでは Maven を使用します。 依存関係なしにコネクタを構築するには、次のコードを実行します。
```
mvn clean package
```
*releases* フォルダーから最新バージョンの JAR をダウンロードすることもできます。

### <a name="include-the-azure-cosmos-db-spark-jar"></a>Azure Cosmos DB Spark JAR の追加
コードを実行する前に、Azure Cosmos DB Spark JAR を含める必要があります。  **spark-shell** を使用する場合は、**--jars** オプションを使用して JAR を含めることができます。  

```
spark-shell --master $master --jars /$location/azure-cosmosdb-spark-0.0.3-jar-with-dependencies.jar
```

依存関係なしで JAR を実行する場合は、次のコードを使用します。

```
spark-shell --master $master --jars /$location/azure-cosmosdb-spark-0.0.3.jar,/$location/azure-documentdb-1.10.0.jar
```

Azure HDInsight Jupyter Notebook サービスなどの Notebook サービスを使用する場合は、**Spark マジック** コマンドを使用できます。

```
%%configure
{ "jars": ["wasb:///example/jars/azure-documentdb-1.10.0.jar","wasb:///example/jars/azure-cosmosdb-spark-0.0.3.jar"],
  "conf": {
    "spark.jars.excludes": "org.scala-lang:scala-reflect"
   }
}
```

**jars** コマンドを使用すると、**azure-cosmosdb-spark** (それ自体と Azure DocumentDB Java SDK) に必要な 2 つの JAR を含め、**scala-reflect** を除外できるため、Livy の呼び出しが妨げられることはありません (Jupyter Notebook > Livy > Spark)。

### <a name="connect-spark-to-azure-cosmos-db-using-the-connector"></a>コネクタを使用して Spark を Azure Cosmos DB に接続
通信転送は少し複雑になりますが、コネクタを使用して Spark から Azure Cosmos DB へのクエリを実行すると、クエリが大幅に高速化されます。

次のコード スニペットは、Spark コンテキストでコネクタを使用する方法を示しています。

```
// Import Necessary Libraries
import org.joda.time._
import org.joda.time.format._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.config.Config

// Configure connection to your collection
val readConfig2 = Config(Map("Endpoint" -> "https://doctorwho.documents.azure.com:443/",
"Masterkey" -> "le1n99i1w5l7uvokJs3RT5ZAH8dc3ql7lx2CG0h0kK4lVWPkQnwpRLyAN0nwS1z4Cyd1lJgvGUfMWR3v8vkXKA==",
"Database" -> "DepartureDelays",
"preferredRegions" -> "Central US;East US2;",
"Collection" -> "flights_pcoll",
"SamplingRatio" -> "1.0"))

// Create collection connection
val coll = spark.sqlContext.read.cosmosDB(readConfig2)
coll.createOrReplaceTempView("c")
```

このコード スニペットでは次のことを示しています。

- **azure-cosmosdb-spark** には、優先場所など、必要な接続パラメーターがすべて含まれています。 たとえば、読み取りレプリカと優先順位の順序を選択できます。
- 必要なライブラリをインポートし、masterKey と host を構成して Azure Cosmos DB クライアントを作成します。

### <a name="execute-spark-queries-via-the-connector"></a>コネクタを介して Spark クエリを実行

以下の例では、指定した読み取り専用キーを使って、前のスニペットで作成された Azure Cosmos DB インスタンスを使用します。 次のコード スニペットは、(以前に指定した DoctorWho アカウントの) DepartureDelays.flights_pcoll コレクションに接続して、シアトル発のフライトの遅延情報を抽出するクエリを実行します。

```
// Queries
var query = "SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'"
val df = spark.sql(query)

// Run DF query (count)
df.count()

// Run DF query (show)
df.show()
```

### <a name="why-use-the-spark-to-azure-cosmos-db-connector-implementation"></a>Spark-Azure Cosmos DB コネクタ実装を使用する理由

コネクタを使用して Spark を Azure Cosmos DB に接続するのは、一般に次のような場合です。

* Scala を使用し、「[Issue 3: Add Python wrapper and examples (問題点 3: Python ラッパーの追加と例)](https://github.com/Azure/azure-cosmosdb-spark/issues/3)」で説明するように、Scala を更新して Python ラッパーを含める場合。
* Apache Spark と Azure Cosmos DB 間で大量のデータを転送する場合。

クエリのパフォーマンスの違いについては、[クエリ テストの実行に関する Wiki](https://github.com/Azure/azure-cosmosdb-spark/wiki/Query-Test-Runs) をご覧ください。

## <a name="distributed-aggregation-example"></a>分散集計の例
このセクションでは、Apache Spark と Azure Cosmos DB を組み合わせて使用して、分散集計と分析を実行する方法の例を示します。 Azure Cosmos DB では、[Azure Cosmos DB による世界規模の集計ブログ](https://azure.microsoft.com/blog/planet-scale-aggregates-with-azure-documentdb/)で説明されている集計がサポートされています。 ここでは、Apache Spark を使用して、次の段階に進めるようにするための方法を説明します。

これらの集計は、[Spark-Azure Cosmos DB コネクタ Notebook](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Spark-to-CosmosDB_Connector.ipynb) に関連しています。

### <a name="connect-to-flights-sample-data"></a>フライト サンプル データへの接続
以下の集計例では、**DoctorWho** Azure Cosmos DB データベースに格納されている、一部のフライト実績データにアクセスします。 このデータベースに接続するには、次のコード スニペットを使用する必要があります。

```
// Import Spark to Azure Cosmos DB connector
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.config.Config

// Connect to Azure Cosmos DB Database
val readConfig2 = Config(Map("Endpoint" -> "https://doctorwho.documents.azure.com:443/",
"Masterkey" -> "le1n99i1w5l7uvokJs3RT5ZAH8dc3ql7lx2CG0h0kK4lVWPkQnwpRLyAN0nwS1z4Cyd1lJgvGUfMWR3v8vkXKA==",
"Database" -> "DepartureDelays",
"preferredRegions" -> "Central US;East US 2;",
"Collection" -> "flights_pcoll",
"SamplingRatio" -> "1.0"))

// Create collection connection
val coll = spark.sqlContext.read.cosmosDB(readConfig2)
coll.createOrReplaceTempView("c")
```

このスニペットでは、フィルター処理されたデータ セットを Azure Cosmos DB から Spark に転送する基本クエリも実行し、後者は分散集計を実行できます。 この例では、シアトル (SEA) 発のフライトを要求します。

```
// Run, get row count, and time query
val originSEA = spark.sql("SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'")
originSEA.createOrReplaceTempView("originSEA")
```

以下の結果は、Jupyter Notebook サービスからクエリを実行して生成されたものです。  コード スニペットはいずれも汎用であり、サービスに固有のものではないことに注意してください。

### <a name="running-limit-and-count-queries"></a>LIMIT クエリと COUNT クエリを実行する
SQL/Spark SQL の場合と同様に、まず **LIMIT** クエリを実行します。

![Spark LIMIT クエリ](./media/spark-connector/spark-sql-query.png)

次のクエリは、シンプルで高速な **COUNT** クエリです。

![Spark COUNT クエリ](./media/spark-connector/spark-count-query.png)

### <a name="group-by-query"></a>GROUP BY クエリ
この次のセットでは、Azure Cosmos DB データベースに対して **GROUP BY** クエリを簡単に実行できます。

```
select destination, sum(delay) as TotalDelays
from originSEA
group by destination
order by sum(delay) desc limit 10
```

![Spark GROUP BY クエリのグラフ](./media/spark-connector/group-by-query-graph.png)

### <a name="distinct-order-by-query"></a>DISTINCT、ORDER BY クエリ
ここでは、**DISTINCT、ORDER BY** クエリを実行します。

![Spark GROUP BY クエリのグラフ](./media/spark-connector/order-by-query.png)

### <a name="continue-the-flight-data-analysis"></a>フライト データの分析の続行
以下のサンプル クエリを使用して、フライト データの分析を続行できます。

#### <a name="top-5-delayed-destinations-cities-departing-from-seattle"></a>シアトル発のフライトで遅延が発生した上位 5 つの目的地 (都市)
```
select destination, sum(delay)
from originSEA
where delay < 0
group by destination
order by sum(delay) limit 5
```
![Spark - 上位遅延のグラフ](./media/spark-connector/top-delays-graph.png)


#### <a name="calculate-median-delays-by-destination-cities-departing-from-seattle"></a>シアトル発のフライトの目的地 (都市) 別に遅延の中央値を計算する
```
select destination, percentile_approx(delay, 0.5) as median_delay
from originSEA
where delay < 0
group by destination
order by percentile_approx(delay, 0.5)
```

![Spark - 遅延の中央値のグラフ](./media/spark-connector/median-delays-graph.png)

## <a name="next-steps"></a>次のステップ

Spark-Azure Cosmos DB コネクタをまだダウンロードしていない場合は、そのコネクタを [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark) GitHub リポジトリからダウンロードし、リポジトリでその他のリソースを調べる。

* [分散集計の例](https://github.com/Azure/azure-cosmosdb-spark/wiki/Aggregations-Examples)
* [サンプル スクリプトと Notebook](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)

また、[Apache Spark SQL、DataFrames、データセット ガイド](http://spark.apache.org/docs/latest/sql-programming-guide.html)と [Azure HDInsight 上の Apache Spark](../hdinsight/hdinsight-apache-spark-jupyter-spark-sql.md) に関する記事を確認することもできます。

