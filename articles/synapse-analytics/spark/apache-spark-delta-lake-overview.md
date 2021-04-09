---
title: Apache Spark for Azure Synapse Analytics で Linux Foundation Delta Lake を使用する方法の概要
description: Apache Spark for Azure Synapse Analytics で Delta Lake を使用して、ACID 特性を備えたテーブルを作成および使用する方法について説明します。
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.reviewer: euang
ms.topic: overview
ms.subservice: spark
ms.date: 07/28/2020
ms.author: euang
ms.custom: devx-track-csharp
zone_pivot_groups: programming-languages-spark-all-minus-sql
ms.openlocfilehash: 1e0dfd597e7f445eeba6cef332d8ea12b27dc3a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101676307"
---
# <a name="linux-foundation-delta-lake-overview"></a>Linux Foundation Delta Lake の概要

この記事は、よりわかりやすくするために、元の対応する記事 ([こちら](https://docs.delta.io/latest/quick-start.html)) を改作したものです。 この記事は、[Delta Lake](https://delta.io) の主な機能をすばやく確認するのに役立ちます。 この記事では、対話型、バッチ、ストリーミングの各クエリを使用して、Delta Lake テーブルに対して読み取りと書き込みを実行する方法を示すコード スニペットを提供します。 このコード スニペットは、一連のノートブック ([PySpark](https://github.com/Azure-Samples/Synapse/blob/master/Notebooks/PySpark/Hitchikers%20Guide%20to%20Delta%20Lake%20-%20Python.ipynb)、[Scala](https://github.com/Azure-Samples/Synapse/blob/master/Notebooks/Scala/Hitchikers%20Guide%20to%20Delta%20Lake%20-%20Scala.ipynb)、[C#](https://github.com/Azure-Samples/Synapse/blob/master/Notebooks/Spark.NET%20C%23/Hitchikers%20Guide%20to%20Delta%20Lake%20-%20CSharp.ipynb)) でも入手できます。

ここで説明する内容は次のとおりです。

* テーブルを作成する
* データの読み取り
* テーブル データを更新する
* テーブル データを上書きする
* 上書きなしで条件付き更新を行う
* タイム トラベルを使用して古いバージョンのデータを読み取る
* データ ストリームをテーブルに書き込む
* テーブルから変更のストリームを読み取る
* SQL サポート

## <a name="configuration"></a>構成

必ず実際の環境に合わせて以下を変更してください。

:::zone pivot = "programming-language-python"

```python
import random

session_id = random.randint(0,1000000)
delta_table_path = "/delta/delta-table-{0}".format(session_id)

delta_table_path
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var sessionId = (new Random()).Next(10000000);
var deltaTablePath = $"/delta/delta-table-{sessionId}";

deltaTablePath
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val sessionId = scala.util.Random.nextInt(1000000)
val deltaTablePath = s"/delta/delta-table-$sessionId";
```

::: zone-end

結果は次のようになります。

'/delta/delta-table-335323'

## <a name="create-a-table"></a>テーブルを作成する

Delta Lake テーブルを作成するには、DataFrame をデルタ形式の DataFrame に書き出します。 形式は、Parquet、CSV、JSON などからデルタに変更できます。

次のコードは、DataFrame から推論されたスキーマを使用して新しい Delta Lake テーブルを作成する方法を示しています。

:::zone pivot = "programming-language-python"

```python
data = spark.range(0,5)
data.show()
data.write.format("delta").save(delta_table_path)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var data = spark.Range(0,5);
data.Show();
data.Write().Format("delta").Save(deltaTablePath);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val data = spark.range(0, 5)
data.show
data.write.format("delta").save(deltaTablePath)
```

::: zone-end

結果は次のようになります。

| id|
|---|
|  0|
|  1|
|  2|
|  3|
|  4|

## <a name="read-data"></a>データの読み取り

ファイルへのパスとデルタ形式を指定して、Delta Lake テーブルのデータを読み取ります。

:::zone pivot = "programming-language-python"

```python
df = spark.read.format("delta").load(delta_table_path)
df.show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var df = spark.Read().Format("delta").Load(deltaTablePath);
df.Show()
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val df = spark.read.format("delta").load(deltaTablePath)
df.show()
```

::: zone-end

結果は次のようになります。

| id|
|---|
|  1|
|  3|
|  4|
|  0|
|  2|

結果を出力する前に順序が明示的に指定されていないため、結果の順序は上記とは異なります。

## <a name="update-table-data"></a>テーブル データを更新する

Delta Lake では、標準の DataFrame API を使用してテーブルを変更する操作がいくつかサポートされています。これは、デルタ形式によって追加される大きな機能強化の 1 つです。 次の例では、テーブル内のデータを上書きするバッチ ジョブを実行します。

:::zone pivot = "programming-language-python"

```python
data = spark.range(5,10)
data.write.format("delta").mode("overwrite").save(delta_table_path)
df.show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var data = spark.Range(5,10);
data.Write().Format("delta").Mode("overwrite").Save(deltaTablePath);
df.Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val data = spark.range(5, 10)
data.write.format("delta").mode("overwrite").save(deltaTablePath)
df.show()
```

::: zone-end

結果は次のようになります。

| id|
|---|
|  7|
|  8|
|  5|
|  9|
|  6|

ここでは、5 つのレコードがすべて更新され、新しい値が保持されていることがわかります。

## <a name="save-as-catalog-tables"></a>カタログ テーブルとして保存する

Delta Lake では、マネージドまたは外部カタログ テーブルに書き込むことができます。

:::zone pivot = "programming-language-python"

```python
data.write.format("delta").saveAsTable("ManagedDeltaTable")
spark.sql("CREATE TABLE ExternalDeltaTable USING DELTA LOCATION '{0}'".format(delta_table_path))
spark.sql("SHOW TABLES").show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
data.Write().Format("delta").SaveAsTable("ManagedDeltaTable");
spark.Sql($"CREATE TABLE ExternalDeltaTable USING DELTA LOCATION '{deltaTablePath}'");
spark.Sql("SHOW TABLES").Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
data.write.format("delta").saveAsTable("ManagedDeltaTable")
spark.sql(s"CREATE TABLE ExternalDeltaTable USING DELTA LOCATION '$deltaTablePath'")
spark.sql("SHOW TABLES").show
```

::: zone-end

結果は次のようになります。

|database|         tableName|isTemporary|
|--------|------------------|-----------|
| default|externaldeltatable|      false|
| default| manageddeltatable|      false|

このコードでは、既存のデータフレームから新しいテーブルをカタログに作成しました。これはマネージド テーブルと呼ばれます。 次に、既存の場所を使用する新しい外部テーブルをカタログに定義しました。これは外部テーブルと呼ばれます。 出力では、作成方法に関係なく、両方のテーブルがカタログに表示されます。

これで、これら両方のテーブルの拡張プロパティを確認できます。

:::zone pivot = "programming-language-python"

```python
spark.sql("DESCRIBE EXTENDED ManagedDeltaTable").show(truncate=False)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
spark.Sql("DESCRIBE EXTENDED ManagedDeltaTable").Show(truncate: 0);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
spark.sql("DESCRIBE EXTENDED ManagedDeltaTable").show(truncate=false)
```

::: zone-end

結果は次のようになります。

|col_name                    |data_type                                                                                                    |コメント|
|----------------------------|-------------------------------------------------------------------------------------------------------------|-------|
|id                          |bigint                                                                                                       |null   |
|                            |                                                                                                             |       |
|Detailed Table Information  |                                                                                                             |       |
|データベース                    |default                                                                                                      |       |
|Table                       |manageddeltatable                                                                                            |       |
|所有者                       |trusted-service-user                                                                                         |       |
|Created Time                |Sat Apr 25 00:35:34 UTC 2020                                                                                 |       |
|［最終アクセス日時］                 |Thu Jan 01 00:00:00 UTC 1970                                                                                 |       |
|作成者                  |Spark 2.4.4.2.6.99.201-11401300                                                                              |       |
|Type                        |MANAGED                                                                                                      |       |
|プロバイダー                    |delta                                                                                                        |       |
|テーブルのプロパティ            |[transient_lastDdlTime=1587774934]                                                                           |       |
|統計                  |2407 bytes                                                                                                   |       |
|場所                    |abfss://data@<data lake>.dfs.core.windows.net/synapse/workspaces/<workspace name>/warehouse/manageddeltatable|       |
|Serde Library               |org.apache.hadoop.hive.serde2.lazy.LazySimpleSerDe                                                           |       |
|InputFormat                 |org.apache.hadoop.mapred.SequenceFileInputFormat                                                             |       |
|OutputFormat                |org.apache.hadoop.hive.ql.io.HiveSequenceFileOutputFormat                                                    |       |
|Storage Properties          |[serialization.format=1]                                                                                     |       |

:::zone pivot = "programming-language-python"

```python
spark.sql("DESCRIBE EXTENDED ExternalDeltaTable").show(truncate=False)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
spark.Sql("DESCRIBE EXTENDED ExternalDeltaTable").Show(truncate: 0);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
spark.sql("DESCRIBE EXTENDED ExternalDeltaTable").show(truncate=false)
```

::: zone-end

結果は次のようになります。

|col_name                    |data_type                                                             |コメント|
|----------------------------|----------------------------------------------------------------------|-------|
|id                          |bigint                                                                |null   |
|                            |                                                                      |       |
|Detailed Table Information  |                                                                      |       |
|データベース                    |default                                                               |       |
|Table                       |externaldeltatable                                                    |       |
|所有者                       |trusted-service-user                                                  |       |
|Created Time                |Sat Apr 25 00:35:38 UTC 2020                                          |       |
|［最終アクセス日時］                 |Thu Jan 01 00:00:00 UTC 1970                                          |       |
|作成者                  |Spark 2.4.4.2.6.99.201-11401300                                       |       |
|Type                        |EXTERNAL                                                              |       |
|プロバイダー                    |DELTA                                                                 |       |
|テーブルのプロパティ            |[transient_lastDdlTime=1587774938]                                    |       |
|場所                    |abfss://data@<data lake>.dfs.core.windows.net/delta/delta-table-587152|       |
|Serde Library               |org.apache.hadoop.hive.serde2.lazy.LazySimpleSerDe                    |       |
|InputFormat                 |org.apache.hadoop.mapred.SequenceFileInputFormat                      |       |
|OutputFormat                |org.apache.hadoop.hive.ql.io.HiveSequenceFileOutputFormat             |       |
|Storage Properties          |[serialization.format=1]                                              |       |

## <a name="conditional-update-without-overwrite"></a>上書きなしで条件付き更新を行う

Delta Lake では、データの条件付き更新、削除、テーブルへのマージ (一般に upsert と呼ばれます) を実行するプログラム API を提供します。

:::zone pivot = "programming-language-python"

```python
from delta.tables import *
from pyspark.sql.functions import *

delta_table = DeltaTable.forPath(spark, delta_table_path)

delta_table.update(
  condition = expr("id % 2 == 0"),
  set = { "id": expr("id + 100") })
delta_table.toDF().show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
using Microsoft.Spark.Extensions.Delta;
using Microsoft.Spark.Extensions.Delta.Tables;
using Microsoft.Spark.Sql;
using static Microsoft.Spark.Sql.Functions;

var deltaTable = DeltaTable.ForPath(deltaTablePath);

deltaTable.Update(
  condition: Expr("id % 2 == 0"),
  set: new Dictionary<string, Column>(){{ "id", Expr("id + 100") }});
deltaTable.ToDF().Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
import io.delta.tables._
import org.apache.spark.sql.functions._

val deltaTable = DeltaTable.forPath(deltaTablePath)

// Update every even value by adding 100 to it
deltaTable.update(
  condition = expr("id % 2 == 0"),
  set = Map("id" -> expr("id + 100")))
deltaTable.toDF.show
```

::: zone-end

結果は次のようになります。

| id|
|---|
|106|
|108|
|  5|
|  7|
|  9|

ここでは、すべての偶数の ID に 100 を加算しました。

:::zone pivot = "programming-language-python"

```python
delta_table.delete("id % 2 == 0")
delta_table.toDF().show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
deltaTable.Delete(condition: Expr("id % 2 == 0"));
deltaTable.ToDF().Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
deltaTable.delete(condition = expr("id % 2 == 0"))
deltaTable.toDF.show
```

::: zone-end

結果は次のようになります。

| id|
|---|
|  5|
|  7|
|  9|

すべての偶数行が削除されていることがわかります。

:::zone pivot = "programming-language-python"

```python
new_data = spark.range(0,20).alias("newData")

delta_table.alias("oldData")\
    .merge(new_data.alias("newData"), "oldData.id = newData.id")\
    .whenMatchedUpdate(set = { "id": lit("-1")})\
    .whenNotMatchedInsert(values = { "id": col("newData.id") })\
    .execute()

delta_table.toDF().show(100)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var newData = spark.Range(20).As("newData");

deltaTable
    .As("oldData")
    .Merge(newData, "oldData.id = newData.id")
    .WhenMatched()
        .Update(new Dictionary<string, Column>() {{"id", Lit("-1")}})
    .WhenNotMatched()
        .Insert(new Dictionary<string, Column>() {{"id", Col("newData.id")}})
    .Execute();

deltaTable.ToDF().Show(100);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val newData = spark.range(0, 20).toDF

deltaTable.as("oldData").
  merge(
    newData.as("newData"),
    "oldData.id = newData.id").
  whenMatched.
  update(Map("id" -> lit(-1))).
  whenNotMatched.
  insert(Map("id" -> col("newData.id"))).
  execute()

deltaTable.toDF.show()
```

::: zone-end

結果は次のようになります。

| id|
|---|
| 18|
| 15|
| 19|
|  2|
|  1|
|  6|
|  8|
|  3|
| -1|
| 10|
| 13|
|  0|
| 16|
|  4|
| -1|
| 12|
| 11|
| 14|
| -1|
| 17|

ここでは、既存のデータの組み合わせがあります。 既存のデータには、更新コード パス (WhenMatched) で値 -1 が割り当てられています。 スニペットの先頭で作成され、挿入コード パス (WhenNotMatched) によって追加された新しいデータも追加されています。

### <a name="history"></a>履歴

Delta Lake には、テーブルの履歴を調べる機能があります。 これは、基になるデルタ テーブルに加えられた変更です。 次のセルは、履歴の調査がいかに簡単かを示しています。

:::zone pivot = "programming-language-python"

```python
delta_table.history().show(20, 1000, False)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
deltaTable.History().Show(20, 1000, false);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
deltaTable.history.show(false)
```

::: zone-end

結果は次のようになります。

|version|          timestamp|userId|userName|operation|                                                operationParameters| ジョブ (job)|ノートブック|clusterId|readVersion|isolationLevel|isBlindAppend|
|-------|-------------------|------|--------|---------|-------------------------------------------------------------------|----|--------|---------|-----------|--------------|-------------|
|      4|2020-04-25 00:36:27|  null|    null|    MERGE|                       [predicate -> (oldData.`ID` = newData.`ID`)]|null|    null|     null|          3|          null|        false|
|      3|2020-04-25 00:36:08|  null|    null|   DELETE|[predicate -> ["((`ID` % CAST(2 AS BIGINT)) = CAST(0 AS BIGINT))"]]|null|    null|     null|          2|          null|        false|
|      2|2020-04-25 00:35:51|  null|    null|   UPDATE| [predicate -> ((ID#744L % cast(2 as bigint)) = cast(0 as bigint))]|null|    null|     null|          1|          null|        false|
|      1|2020-04-25 00:35:05|  null|    null|    WRITE|                             [mode -> Overwrite, partitionBy -> []]|null|    null|     null|          0|          null|        false|
|      0|2020-04-25 00:34:34|  null|    null|    WRITE|                         [mode -> ErrorIfExists, partitionBy -> []]|null|    null|     null|       null|          null|         true|

ここでは、上記のコード スニペットに対して行われたすべての変更を確認できます。

## <a name="read-older-versions-of-data-using-time-travel"></a>タイム トラベルを使用して古いバージョンのデータを読み取る

タイム トラベルと呼ばれる機能を使用して、Delta Lake テーブルの以前のスナップショットを求めるクエリを実行できます。 上書きしたデータにアクセスする場合は、versionAsOf オプションを使用して、最初のデータ セットを上書きする前のテーブルのスナップショットを求めるクエリを実行できます。

次のセルを実行すると、上書きする前の最初のデータ セットが表示されます。 タイム トラベルは、Delta Lake トランザクション ログの機能を利用して、テーブルに存在しなくなったデータにアクセスする非常に強力な機能です。 バージョン 0 オプションを削除すると (またはバージョン 1 を指定すると)、新しいデータが再び表示されます。 詳細については、「[テーブルの古いスナップショットのクエリを実行する](https://docs.delta.io/latest/delta-batch.html#deltatimetravel)」をご覧ください。

:::zone pivot = "programming-language-python"

```python
df = spark.read.format("delta").option("versionAsOf", 0).load(delta_table_path)
df.show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var df = spark.Read().Format("delta").Option("versionAsOf", 0).Load(deltaTablePath);
df.Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val df = spark.read.format("delta").option("versionAsOf", 0).load(deltaTablePath)
df.show()
```

::: zone-end

結果は次のようになります。

| id|
|---|
|  0|
|  1|
|  4|
|  3|
|  2|

ここでは、データの最も古いバージョンに戻っていることがわかります。

## <a name="write-a-stream-of-data-to-a-table"></a>データ ストリームをテーブルに書き込む

Spark の構造化ストリーミングを使用して、Delta Lake テーブルに書き込むこともできます。 Delta Lake トランザクション ログでは、テーブルに対して他のストリームやバッチ クエリが同時に実行されている場合でも、1 回限りの処理が保証されます。 既定では、ストリームは追加モードで実行され、新しいレコードがテーブルに追加されます。

Delta Lake と構造化ストリーミングの統合の詳細については、「[テーブル ストリーミングの読み取りと書き込み](https://docs.delta.io/latest/delta-streaming.html)」をご覧ください。

次のセルでの実行内容を示します。

* セル 30: 新しく追加されたデータを表示する
* セル 31: 履歴を調べる
* セル 32: 構造化ストリーミング ジョブを停止する
* セル 33: 履歴を調べる <-- 追加が停止されたことがわかります

まず、シーケンスを生成する単純な Spark ストリーミング ジョブを設定し、そのジョブでデルタ テーブルへの書き込みを実行します。

:::zone pivot = "programming-language-python"

```python
streaming_df = spark.readStream.format("rate").load()
stream = streaming_df\
    .selectExpr("value as id")\
    .writeStream\
    .format("delta")\
    .option("checkpointLocation", "/tmp/checkpoint-{0}".format(session_id))\
    .start(delta_table_path)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var streamingDf = spark.ReadStream().Format("rate").Load();
var stream = streamingDf.SelectExpr("value as id").WriteStream().Format("delta").Option("checkpointLocation", $"/tmp/checkpoint-{sessionId}").Start(deltaTablePath);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val streamingDf = spark.readStream.format("rate").load()
val stream = streamingDf.select($"value" as "id").writeStream.format("delta").option("checkpointLocation", s"/tmp/checkpoint-$sessionId").start(deltaTablePath)
```

::: zone-end

## <a name="read-a-stream-of-changes-from-a-table"></a>テーブルから変更のストリームを読み取る

ストリームは Delta Lake テーブルに書き込まれますが、ストリーミング ソースとしてそのテーブルから読み取ることもできます。 たとえば、Delta Lake テーブルに加えられたすべての変更を出力する別のストリーミング クエリを開始できます。

:::zone pivot = "programming-language-python"

```python
delta_table.toDF().sort(col("id").desc()).show(100)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
deltaTable.ToDF().Sort(Col("id").Desc()).Show(100);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
deltaTable.toDF.sort($"id".desc).show
```

::: zone-end

結果は次のようになります。

| id|
|---|
| 19|
| 18|
| 17|
| 16|
| 15|
| 14|
| 13|
| 12|
| 11|
| 10|
|  8|
|  6|
|  4|
|  3|
|  2|
|  1|
|  0|
| -1|
| -1|
| -1|

:::zone pivot = "programming-language-python"

```python
delta_table.history().drop("userId", "userName", "job", "notebook", "clusterId", "isolationLevel", "isBlindAppend").show(20, 1000, False)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
deltaTable.History().Drop("userId", "userName", "job", "notebook", "clusterId", "isolationLevel", "isBlindAppend").Show(20, 1000, false);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
deltaTable.history.show
```

::: zone-end

結果は次のようになります。

|version|          timestamp|       operation|                                                                  operationParameters|readVersion|
|-------|-------------------|----------------|-------------------------------------------------------------------------------------|-----------|
|      5|2020-04-25 00:37:09|STREAMING UPDATE|[outputMode -> Append, queryId -> d26b4f8a-7e5a-44f2-a5fb-23a7bd02aef7, epochId -> 0]|          4|
|      4|2020-04-25 00:36:27|           MERGE|                                         [predicate -> (oldData.`id` = newData.`id`)]|          3|
|      3|2020-04-25 00:36:08|          DELETE|                  [predicate -> ["((`id` % CAST(2 AS BIGINT)) = CAST(0 AS BIGINT))"]]|          2|
|      2|2020-04-25 00:35:51|          UPDATE|                   [predicate -> ((id#744L % cast(2 as bigint)) = cast(0 as bigint))]|          1|
|      1|2020-04-25 00:35:05|           WRITE|                                               [mode -> Overwrite, partitionBy -> []]|          0|
|      0|2020-04-25 00:34:34|           WRITE|                                           [mode -> ErrorIfExists, partitionBy -> []]|       null|

ここでは、履歴ビューの表示エクスペリエンスを簡素化するために、関心度が低い列を一部除外します。

:::zone pivot = "programming-language-python"

```python
stream.stop()
delta_table.history().drop("userId", "userName", "job", "notebook", "clusterId", "isolationLevel", "isBlindAppend").show(100, 1000, False)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
stream.Stop();
deltaTable.History().Drop("userId", "userName", "job", "notebook", "clusterId", "isolationLevel", "isBlindAppend").Show(100, 1000, false);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
stream.stop
deltaTable.history.show
```

結果は次のようになります。

|version|          timestamp|       operation|                                                                  operationParameters|readVersion|
|-------|-------------------|----------------|-------------------------------------------------------------------------------------|-----------|
|      5|2020-04-25 00:37:09|STREAMING UPDATE|[outputMode -> Append, queryId -> d26b4f8a-7e5a-44f2-a5fb-23a7bd02aef7, epochId -> 0]|          4|
|      4|2020-04-25 00:36:27|           MERGE|                                         [predicate -> (oldData.`id` = newData.`id`)]|          3|
|      3|2020-04-25 00:36:08|          DELETE|                  [predicate -> ["((`id` % CAST(2 AS BIGINT)) = CAST(0 AS BIGINT))"]]|          2|
|      2|2020-04-25 00:35:51|          UPDATE|                   [predicate -> ((id#744L % cast(2 as bigint)) = cast(0 as bigint))]|          1|
|      1|2020-04-25 00:35:05|           WRITE|                                               [mode -> Overwrite, partitionBy -> []]|          0|
|      0|2020-04-25 00:34:34|           WRITE|                                           [mode -> ErrorIfExists, partitionBy -> []]|       null|

::: zone-end

## <a name="convert-parquet-to-delta"></a>Parquet をデルタに変換する

Parquet 形式からデルタへのインプレース変換を実行できます。

ここでは、既存のテーブルがデルタ形式かどうかをテストします。
:::zone pivot = "programming-language-python"

```python
parquet_path = "/parquet/parquet-table-{0}".format(session_id)
data = spark.range(0,5)
data.write.parquet(parquet_path)
DeltaTable.isDeltaTable(spark, parquet_path)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var parquetPath = $"/parquet/parquet-table-{sessionId}";
var data = spark.Range(0,5);
data.Write().Parquet(parquetPath);
DeltaTable.IsDeltaTable(parquetPath)
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val parquetPath = s"/parquet/parquet-table-$sessionId"
val data = spark.range(0,5)
data.write.parquet(parquetPath)
DeltaTable.isDeltaTable(parquetPath)
```

::: zone-end

結果は次のようになります。

×

次に、データをデルタ形式に変換し、それが機能していることを確認します。

:::zone pivot = "programming-language-python"

```python
DeltaTable.convertToDelta(spark, "parquet.`{0}`".format(parquet_path))
DeltaTable.isDeltaTable(spark, parquet_path)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
DeltaTable.ConvertToDelta(spark, $"parquet.`{parquetPath}`");
DeltaTable.IsDeltaTable(parquetPath)
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
DeltaTable.convertToDelta(spark, s"parquet.`$parquetPath`")
DeltaTable.isDeltaTable(parquetPath)
```

::: zone-end

結果は次のようになります。

○

## <a name="sql-support"></a>SQL サポート

デルタでは、SQL を使用したテーブル ユーティリティ コマンドがサポートされています。 SQL を使用して次のことができます。

* デルタ テーブルの履歴を取得する
* デルタ テーブルをバキュームする
* Parquet ファイルをデルタに変換する

:::zone pivot = "programming-language-python"

```python
spark.sql("DESCRIBE HISTORY delta.`{0}`".format(delta_table_path)).show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
spark.Sql($"DESCRIBE HISTORY delta.`{deltaTablePath}`").Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
spark.sql(s"DESCRIBE HISTORY delta.`$deltaTablePath`").show()
```

::: zone-end

結果は次のようになります。

|version|          timestamp|userId|userName|       operation| operationParameters| ジョブ (job)|ノートブック|clusterId|readVersion|isolationLevel|isBlindAppend|
|-------|-------------------|------|--------|----------------|--------------------|----|--------|---------|-----------|--------------|-------------|
|      5|2020-04-25 00:37:09|  null|    null|STREAMING UPDATE|[outputMode -> Ap...|null|    null|     null|          4|          null|         true|
|      4|2020-04-25 00:36:27|  null|    null|           MERGE|[predicate -> (ol...|null|    null|     null|          3|          null|        false|
|      3|2020-04-25 00:36:08|  null|    null|          DELETE|[predicate -> ["(...|null|    null|     null|          2|          null|        false|
|      2|2020-04-25 00:35:51|  null|    null|          UPDATE|[predicate -> ((i...|null|    null|     null|          1|          null|        false|
|      1|2020-04-25 00:35:05|  null|    null|           WRITE|[mode -> Overwrit...|null|    null|     null|          0|          null|        false|
|      0|2020-04-25 00:34:34|  null|    null|           WRITE|[mode -> ErrorIfE...|null|    null|     null|       null|          null|         true|

:::zone pivot = "programming-language-python"

```python
spark.sql("VACUUM delta.`{0}`".format(delta_table_path)).show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
spark.Sql($"VACUUM delta.`{deltaTablePath}`").Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
spark.sql(s"VACUUM delta.`$deltaTablePath`").show()
```

::: zone-end

結果は次のようになります。

|                path|
|--------------------|
|abfss://data@arca...|

次に、テーブルがデルタ形式のテーブルではないことを確認し、Spark SQL を使用してそれをデルタ形式に変換して、正しく変換されたことを確認します。

:::zone pivot = "programming-language-python"

```python
parquet_id = random.randint(0,1000)
parquet_path = "/parquet/parquet-table-{0}-{1}".format(session_id, parquet_path)
data = spark.range(0,5)
data.write.parquet(parquet_path)
DeltaTable.isDeltaTable(spark, parquet_path)
spark.sql("CONVERT TO DELTA parquet.`{0}`".format(parquet_path))
DeltaTable.isDeltaTable(spark, parquet_path)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var parquetId =  (new Random()).Next(10000000);
var parquetPath = $"/parquet/parquet-table-{sessionId}-{parquetId}";
var data = spark.Range(0,5);
data.Write().Parquet(parquetPath);
DeltaTable.IsDeltaTable(parquetPath);
spark.Sql($"CONVERT TO DELTA parquet.`{parquetPath}`");
DeltaTable.IsDeltaTable(parquetPath);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val parquetId = scala.util.Random.nextInt(1000)
val parquetPath = s"/parquet/parquet-table-$sessionId-$parquetId"
val data = spark.range(0,5)
data.write.parquet(parquetPath)
DeltaTable.isDeltaTable(parquetPath)
spark.sql(s"CONVERT TO DELTA parquet.`$parquetPath`")
DeltaTable.isDeltaTable(parquetPath)
```

::: zone-end

結果は次のようになります。

○

完全なドキュメントについては、[Delta Lake のドキュメント ページ](https://docs.delta.io/latest/delta-intro.html)を参照してください

詳細については、[Delta Lake プロジェクト](https://github.com/delta-io/delta)を参照してください。

## <a name="next-steps"></a>次のステップ

* [.NET for Apache Spark ドキュメント](/dotnet/spark)
* [Azure Synapse Analytics](../index.yml)