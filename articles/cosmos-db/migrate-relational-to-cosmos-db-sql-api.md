---
title: 1 対多のリレーショナル データを Azure Cosmos DB SQL API に移行する
description: SQL API への 1 対多のリレーションシップの複雑なデータ移行を処理する方法について説明します
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 12/12/2019
ms.author: thvankra
ms.openlocfilehash: 53a3317f38cc22ffa3745f5f0e58cc01a54b825c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93096752"
---
# <a name="migrate-one-to-few-relational-data-into-azure-cosmos-db-sql-api-account"></a>1 対多のリレーショナル データを Azure Cosmos DB SQL API アカウントに移行する
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

リレーショナル データベースから Azure Cosmos DB SQL API に移行するには、最適化のためにデータ モデルに変更を加える必要がある場合があります。

1 つの一般的な変換では、関連するサブ項目を 1 つの JSON ドキュメント内に埋め込むことによってデータを非正規化します。 ここでは、Azure Data Factory または Azure Databricks を使用して、このためのいくつかのオプションについて説明します。 Cosmos DB のデータ モデリングに関する一般的なガイダンスについては、「[Azure Cosmos DB のデータ モデリング](modeling-data.md)」を参照してください。  

## <a name="example-scenario"></a>シナリオ例

SQL データベースに、Orders と OrderDetails という 2 つのテーブルがあると仮定します。


:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/orders.png" alt-text="SQL データベースの Orders テーブルと OrderDetails テーブルが表示されているスクリーンショット。" border="false" :::

移行中に、この 1 対多のリレーションシップを 1 つの JSON ドキュメントに結合します。 これを行うには、次のように "FOR JSON" を使用して T-SQL クエリを作成できます。

```sql
SELECT
  o.OrderID,
  o.OrderDate,
  o.FirstName,
  o.LastName,
  o.Address,
  o.City,
  o.State,
  o.PostalCode,
  o.Country,
  o.Phone,
  o.Total,
  (select OrderDetailId, ProductId, UnitPrice, Quantity from OrderDetails od where od.OrderId = o.OrderId for json auto) as OrderDetails
FROM Orders o;
```

このクエリの結果は次のようになります。 

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/for-json-query-result.png" alt-text="注文の詳細" lightbox="./media/migrate-relational-to-cosmos-sql-api/for-json-query-result.png":::

理想的には、単一の Azure Data Factory (ADF) コピー アクティビティを使用してソースとして SQL データのクエリを実行し、出力を適切な JSON オブジェクトとして Azure Cosmos DB シンクに直接書き込みます。 現時点では、1 つのコピー アクティビティで必要な JSON 変換を実行することはできません。 上記のクエリの結果を Azure Cosmos DB SQL API コンテナーにコピーしようとすると、OrderDetails フィールドが、予期される JSON 配列ではなく、ドキュメントの文字列プロパティとして表示されます。

この現時点の制限には、次のいずれかの方法で回避できます。

* **2 つのコピー アクティビティで Azure Data Factory を使用する**: 
  1. SQL から中間 BLOB ストレージの場所にあるテキストファイルに JSON 形式のデータを取得し、 
  2. Azure Cosmos DB で、JSON テキストファイルからコンテナーにデータを読み込みます。

* **Azure Databricks を使用して SQL から読み取り、Azure Cosmos DB に書き込む** - ここでは 2 つのオプションを提示します。


これらの方法を詳しく見てみましょう。

## <a name="azure-data-factory"></a>Azure Data Factory

OrderDetails を変換先 Cosmos DB ドキュメントに JSON 配列として埋め込むことはできませんが、2 つの異なるコピー アクティビティを使用して問題を回避できます。

### <a name="copy-activity-1-sqljsontoblobtext"></a>コピー アクティビティ #1: SqlJsonToBlobText

ソース データの場合、SQL クエリを使用して、SQL Server の OPENJSON 機能と FOR JSON PATH 機能を使用して、行ごとに 1 つの JSON オブジェクト (Order を表す) を含む 1 つの列として結果セットを取得します。

```sql
SELECT [value] FROM OPENJSON(
  (SELECT
    id = o.OrderID,
    o.OrderDate,
    o.FirstName,
    o.LastName,
    o.Address,
    o.City,
    o.State,
    o.PostalCode,
    o.Country,
    o.Phone,
    o.Total,
    (select OrderDetailId, ProductId, UnitPrice, Quantity from OrderDetails od where od.OrderId = o.OrderId for json auto) as OrderDetails
   FROM Orders o FOR JSON PATH)
)
```

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf1.png" alt-text="ADF コピー":::


SqlJsonToBlobText コピー アクティビティのシンクでは、"区切りテキスト" を選択し、動的に生成された一意のファイル名 (たとえば、'@concat(pipeline().RunId,'.json') を使用して Azure Blob Storage 内の特定のフォルダーを指定します。
このテキスト ファイルは実際には "区切られて" おらず、コンマを使用して個別の列に解析するのではなく、二重引用符 (") を保持したいため、"列区切り記号"をタブ ("\t")、つまりデータに出現しない別の文字に設定し、"引用符文字" を "引用符文字なし" に設定します。

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf2.png" alt-text="列区切り記号と引用符文字の設定が強調表示されているスクリーンショット。":::

### <a name="copy-activity-2-blobjsontocosmos"></a>コピー アクティビティ #2: BlobJsonToCosmos

次に、最初のアクティビティによって作成されたテキスト ファイルを Azure Blob Storage で検索する 2 番目のコピー アクティビティを追加して、ADF パイプラインを変更します。 これは、テキスト ファイルで見つかった JSON 行ごとに 1 つのドキュメントとして Cosmos DB シンクに挿入する "JSON" ソースとして処理されます。

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf3.png" alt-text="JSON ソース ファイルと [ファイル パス] フィールドが強調表示されているスクリーンショット。":::

また、必要に応じて、各実行の前に /Orders/ フォルダーに残っている以前のすべてのファイルを削除するように、パイプラインに "削除" アクティビティを追加します。 これで、ADF パイプラインは以下のようになっているはずです。

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf4.png" alt-text="削除アクティビティが強調表示されているスクリーンショット。":::

上記のパイプラインをトリガーすると、中間 Azure Blob Storage の場所に、1 行に 1 つの JSON オブジェクトを含むファイルが作成されます。

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf5.png" alt-text="作成されたファイルが表示されているスクリーンショット。JSON オブジェクトが含まれています。":::

また、Cosmos DB コレクションに挿入された OrderDetails が適切に埋め込まれた Orders ドキュメントも表示されます。

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf6.png" alt-text="注文の詳細が Cosmos DB ドキュメントの一部として示されているスクリーンショット":::


## <a name="azure-databricks"></a>Azure Databricks

また、[Azure Databricks](https://azure.microsoft.com/services/databricks/) で Spark を使用して、Azure Blob Storage に中間テキスト/JSON ファイルを作成せずに、SQL Database のソースから Azure Cosmos DB のコピー先にデータをコピーすることもできます。 

> [!NOTE]
> 明確でわかりやすくするために、以下のコード スニペットには、ダミーのデータベースのパスワードがインラインに明示的に含まれていますが、常に Azure Databricks シークレットを使用する必要があります。
>

まず、必要な [SQL コネクタ](https://docs.databricks.com/data/data-sources/sql-databases-azure.html)と [Azure Cosmos DB コネクタ](https://docs.databricks.com/data/data-sources/azure/cosmosdb-connector.html) ライブラリを作成して、Azure Databricks クラスターに接続します。 クラスターを再起動して、ライブラリが読み込まれていることを確認します。

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/databricks1.png" alt-text="必要な SQL コネクタと Azure Cosmos DB コネクタ ライブラリを作成して Azure Databricks クラスターに接続する場所を示すスクリーンショット。":::

次に、Scala と Python の 2 つのサンプルを紹介します。 

### <a name="scala"></a>Scala
ここでは、DataFrame に "FOR JSON" 出力を含む SQL クエリの結果を取得します。

```scala
// Connect to Azure SQL https://docs.databricks.com/data/data-sources/sql-databases-azure.html
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._
val configSql = Config(Map(
  "url"          -> "xxxx.database.windows.net",
  "databaseName" -> "xxxx",
  "queryCustom"  -> "SELECT o.OrderID, o.OrderDate, o.FirstName, o.LastName, o.Address, o.City, o.State, o.PostalCode, o.Country, o.Phone, o.Total, (SELECT OrderDetailId, ProductId, UnitPrice, Quantity FROM OrderDetails od WHERE od.OrderId = o.OrderId FOR JSON AUTO) as OrderDetails FROM Orders o",
  "user"         -> "xxxx", 
  "password"     -> "xxxx" // NOTE: For clarity and simplicity, this example includes secrets explicitely as a string, but you should always use Databricks secrets
))
// Create DataFrame from Azure SQL query
val orders = sqlContext.read.sqlDB(configSql)
display(orders)
```

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/databricks2.png" alt-text="DataFrame の SQL クエリ出力を示すスクリーンショット。":::

次に、Cosmos DB データベースとコレクションに接続します。

```scala
// Connect to Cosmos DB https://docs.databricks.com/data/data-sources/azure/cosmosdb-connector.html
import org.joda.time._
import org.joda.time.format._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark.CosmosDBSpark
import com.microsoft.azure.cosmosdb.spark.config.Config
import org.apache.spark.sql.functions._
import org.joda.time._
import org.joda.time.format._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark.CosmosDBSpark
import com.microsoft.azure.cosmosdb.spark.config.Config
import org.apache.spark.sql.functions._
val configMap = Map(
  "Endpoint" -> "https://xxxx.documents.azure.com:443/",
  // NOTE: For clarity and simplicity, this example includes secrets explicitely as a string, but you should always use Databricks secrets
  "Masterkey" -> "xxxx",
  "Database" -> "StoreDatabase",
  "Collection" -> "Orders")
val configCosmos = Config(configMap)
```

最後に、スキーマを定義し、from_json を使用して、CosmosDB コレクションに保存する前に DataFrame を適用します。

```scala
// Convert DataFrame to proper nested schema
import org.apache.spark.sql.types._
val orderDetailsSchema = ArrayType(StructType(Array(
    StructField("OrderDetailId",IntegerType,true),
    StructField("ProductId",IntegerType,true),
    StructField("UnitPrice",DoubleType,true),
    StructField("Quantity",IntegerType,true)
  )))
val ordersWithSchema = orders.select(
  col("OrderId").cast("string").as("id"),
  col("OrderDate").cast("string"),
  col("FirstName").cast("string"),
  col("LastName").cast("string"),
  col("Address").cast("string"),
  col("City").cast("string"),
  col("State").cast("string"),
  col("PostalCode").cast("string"),
  col("Country").cast("string"),
  col("Phone").cast("string"),
  col("Total").cast("double"),
  from_json(col("OrderDetails"), orderDetailsSchema).as("OrderDetails")
)
display(ordersWithSchema)
// Save nested data to Cosmos DB
CosmosDBSpark.save(ordersWithSchema, configCosmos)
```

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/databricks3.png" alt-text="Cosmos DB コレクションに保存するための適切な配列が強調表示されているスクリーンショット。":::


### <a name="python"></a>Python

別の方法として、Spark で JSON 変換を実行することが必要になる場合があります (ソース データベースが "FOR JSON" または同様の操作をサポートしていない場合)。または、非常に大きなデータ セットに対して並列操作を使用することもできます。 ここでは、PySpark サンプルを紹介します。 最初のセルにソースとターゲットのデータベース接続を構成することから始めます。

```python
import uuid
import pyspark.sql.functions as F
from pyspark.sql.functions import col
from pyspark.sql.types import StringType,DateType,LongType,IntegerType,TimestampType
 
#JDBC connect details for SQL Server database
jdbcHostname = "jdbcHostname"
jdbcDatabase = "OrdersDB"
jdbcUsername = "jdbcUsername"
jdbcPassword = "jdbcPassword"
jdbcPort = "1433"
 
connectionProperties = {
  "user" : jdbcUsername,
  "password" : jdbcPassword,
  "driver" : "com.microsoft.sqlserver.jdbc.SQLServerDriver"
}
jdbcUrl = "jdbc:sqlserver://{0}:{1};database={2};user={3};password={4}".format(jdbcHostname, jdbcPort, jdbcDatabase, jdbcUsername, jdbcPassword)
 
#Connect details for Target Azure Cosmos DB SQL API account
writeConfig = {
    "Endpoint": "Endpoint",
    "Masterkey": "Masterkey",
    "Database": "OrdersDB",
    "Collection": "Orders",
    "Upsert": "true"
}
```

次に、注文と注文の詳細の両方のレコードについて、ソースのデータベース (この場合は SQL Server) のクエリを実行し、結果を Spark Dataframes に配置します。 また、すべての注文 ID を含む一覧と、並列操作のスレッド プールも作成します。

```python
import json
import ast
import pyspark.sql.functions as F
import uuid
import numpy as np
import pandas as pd
from functools import reduce
from pyspark.sql import SQLContext
from pyspark.sql.types import *
from pyspark.sql import *
from pyspark.sql.functions import exp
from pyspark.sql.functions import col
from pyspark.sql.functions import lit
from pyspark.sql.functions import array
from pyspark.sql.types import *
from multiprocessing.pool import ThreadPool
 
#get all orders
orders = sqlContext.read.jdbc(url=jdbcUrl, table="orders", properties=connectionProperties)
 
#get all order details
orderdetails = sqlContext.read.jdbc(url=jdbcUrl, table="orderdetails", properties=connectionProperties)
 
#get all OrderId values to pass to map function 
orderids = orders.select('OrderId').collect()
 
#create thread pool big enough to process merge of details to orders in parallel
pool = ThreadPool(10)
```

次に、ターゲットの SQL API コレクションに注文を書き込むための関数を作成します。 この関数は、指定された注文 ID のすべての注文の詳細をフィルター処理し、JSON 配列に変換し、この配列を、その注文用のターゲットの SQL API コレクションに書き込む JSON ドキュメントに挿入します。

```python
def writeOrder(orderid):
  #filter the order on current value passed from map function
  order = orders.filter(orders['OrderId'] == orderid[0])
  
  #set id to be a uuid
  order = order.withColumn("id", lit(str(uuid.uuid1())))
  
  #add details field to order dataframe
  order = order.withColumn("details", lit(''))
  
  #filter order details dataframe to get details we want to merge into the order document
  orderdetailsgroup = orderdetails.filter(orderdetails['OrderId'] == orderid[0])
  
  #convert dataframe to pandas
  orderpandas = order.toPandas()
  
  #convert the order dataframe to json and remove enclosing brackets
  orderjson = orderpandas.to_json(orient='records', force_ascii=False)
  orderjson = orderjson[1:-1] 
  
  #convert orderjson to a dictionaory so we can set the details element with order details later
  orderjsondata = json.loads(orderjson)
  
  
  #convert orderdetailsgroup dataframe to json, but only if details were returned from the earlier filter
  if (orderdetailsgroup.count() !=0):
    #convert orderdetailsgroup to pandas dataframe to work better with json
    orderdetailsgroup = orderdetailsgroup.toPandas()
    
    #convert orderdetailsgroup to json string
    jsonstring = orderdetailsgroup.to_json(orient='records', force_ascii=False)
    
    #convert jsonstring to dictionary to ensure correct encoding and no corrupt records
    jsonstring = json.loads(jsonstring)
    
    #set details json element in orderjsondata to jsonstring which contains orderdetailsgroup - this merges order details into the order 
    orderjsondata['details'] = jsonstring
 
  #convert dictionary to json
  orderjsondata = json.dumps(orderjsondata)
 
  #read the json into spark dataframe
  df = spark.read.json(sc.parallelize([orderjsondata]))
  
  #write the dataframe (this will be a single order record with merged many-to-one order details) to cosmos db using spark the connector
  #https://docs.microsoft.com/azure/cosmos-db/spark-connector
  df.write.format("com.microsoft.azure.cosmosdb.spark").mode("append").options(**writeConfig).save()
```

最後に、スレッド プールでマップ関数を使用して上記を呼び出して、前に作成した注文 ID の一覧を渡して並列実行します。

```python
#map order details to orders in parallel using the above function
pool.map(writeOrder, orderids)
```
どちらの方法でも、最終的には、Cosmos DB コレクション内の各注文ドキュメント内に埋め込まれた OrderDetails を適切に保存する必要があります。

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/databricks4.png" alt-text="Databricks":::

## <a name="next-steps"></a>次のステップ
* [Azure Cosmos DB のデータ モデリング](./modeling-data.md)について確認する
* [Azure Cosmos DB のデータをモデル化およびパーティション分割する方法](./how-to-model-partition-example.md)について確認する