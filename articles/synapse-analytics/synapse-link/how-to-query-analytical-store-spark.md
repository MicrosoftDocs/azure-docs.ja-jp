---
title: Apache Spark を使用して Azure Cosmos DB 分析ストア (プレビュー) のクエリを実行する
description: Apache Spark for Azure Synapse Analytics を使用して Azure Cosmos DB 分析ストアのクエリを実行する方法を説明します
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: synapse-link
ms.date: 05/06/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: a7ee04c922e4373414dc27ed2b7c98be605280e5
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87089109"
---
# <a name="query-azure-cosmos-db-analytical-store-preview-with-apache-spark-for-azure-synapse-analytics"></a>Apache Spark for Azure Synapse Analytics を使用して Azure Cosmos DB 分析ストア (プレビュー) のクエリを実行する

この記事では、Synapse ジェスチャから分析ストアを操作する方法について、例を紹介しています。 ジェスチャは、コンテナーを右クリックすると表示されます。 ジェスチャを使用すると、コードをすばやく生成し、ニーズに合わせて調整することができます。 ジェスチャは、1 回のクリックでデータを検出するのにも最適です。

## <a name="load-to-dataframe"></a>データフレームに読み込む

この手順では、Azure Cosmos DB 分析ストアから Spark データフレームにデータを読み取ります。 ***df*** という名前のデータフレームから 10 行が表示されます。 データがデータフレームに読み込まれたら、追加の分析を実行できます。

この操作はトランザクション ストアに影響しません。

```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

df = spark.read.format("cosmos.olap")\
    .option("spark.synapse.linkedService", "INFERRED")\
    .option("spark.cosmos.container", "INFERRED")\
    .load()

df.show(10)
```

**Scala** での同じコード ジェスチャは、次のコードのようになります。
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val df_olap = spark.read.format("cosmos.olap").
    option("spark.synapse.linkedService", "pySparkSamplesDb").
    option("spark.cosmos.container", "trafficSourceColl").
    load()
```

## <a name="create-spark-table"></a>Spark テーブルを作成する

このジェスチャでは、選択したコンテナーを指す Spark テーブルを作成します。 その操作では、データの移動は行われません。 そのテーブルを削除しても、基になっているコンテナー (および対応する分析ストア) に影響はありません。 

このシナリオは、サードパーティのツールでテーブルを再利用したり、ランタイムにデータへのアクセスを提供したりするのに便利です。

```sql
%%sql
-- To select a preferred list of regions in a multi-region Azure Cosmos DB account, add spark.cosmos.preferredRegions '<Region1>,<Region2>' in the config options

create table call_center using cosmos.olap options (
    spark.synapse.linkedService 'INFERRED',
    spark.cosmos.container 'INFERRED'
)
```

## <a name="write-dataframe-to-container"></a>データフレームをコンテナーに書き込む

このジェスチャでは、データフレームをコンテナーに書き込みます。 この操作では、トランザクションのパフォーマンスに影響があり、要求ユニットが使用されます。 書き込みトランザクションには、Azure Cosmos DB のトランザクション パフォーマンスを使用するのが最適です。 **YOURDATAFRAME** を、書き戻したいデータフレームに置き換えてください。

```python
# Write a Spark DataFrame into an Azure Cosmos DB container
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")


YOURDATAFRAME.write.format("cosmos.oltp")\
    .option("spark.synapse.linkedService", "INFERRED")\
    .option("spark.cosmos.container", "INFERRED")\
    .option("spark.cosmos.write.upsertEnabled", "true")\
    .mode('append')\
    .save()
```

**Scala** での同じコード ジェスチャは、次のコードのようになります。
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

import org.apache.spark.sql.SaveMode

df.write.format("cosmos.oltp").
    option("spark.synapse.linkedService", "pySparkSamplesDb").
    option("spark.cosmos.container", "trafficSourceColl"). 
    option("spark.cosmos.write.upsertEnabled", "true").
    mode(SaveMode.Overwrite).
    save()
```

## <a name="load-streaming-dataframe-from-container"></a>コンテナーからストリーミング データフレームを読み込む
このジェスチャでは、Spark Streaming 機能を使用して、コンテナーからデータフレームにデータを読み込みます。 データは、ワークスペースに接続したプライマリ データ レイク アカウント (およびファイル システム) に格納されます。 

フォルダー */localReadCheckpointFolder* が作成されていない場合は、自動的に作成されます。 この操作は、Azure Cosmos DB のトランザクション パフォーマンスに影響します。

```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

dfStream = spark.readStream\
    .format("cosmos.oltp")\
    .option("spark.synapse.linkedService", "INFERRED")\
    .option("spark.cosmos.container", "INFERRED")\
    .option("spark.cosmos.changeFeed.readEnabled", "true")\
    .option("spark.cosmos.changeFeed.startFromTheBeginning", "true")\
    .option("spark.cosmos.changeFeed.checkpointLocation", "/localReadCheckpointFolder")\
    .option("spark.cosmos.changeFeed.queryName", "streamQuery")\
    .load()
```

**Scala** での同じコード ジェスチャは、次のコードのようになります。
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val dfStream = spark.readStream.
    format("cosmos.oltp").
    option("spark.synapse.linkedService", "pySparkSamplesDb").
    option("spark.cosmos.container", "trafficSourceColl").
    option("spark.cosmos.changeFeed.readEnabled", "true").
    option("spark.cosmos.changeFeed.startFromTheBeginning", "true").
    option("spark.cosmos.changeFeed.checkpointLocation", "/localReadCheckpointFolder").
    option("spark.cosmos.changeFeed.queryName", "streamTestRevin2").
    load()
```

## <a name="write-streaming-dataframe-to-container"></a>コンテナーにストリーミング データフレームを書き込む
このジェスチャでは、ストリーミング データフレームを選択した Azure Cosmos DB コンテナーに書き込みます。 フォルダー */localReadCheckpointFolder* が作成されていない場合は、自動的に作成されます。 この操作は、Azure Cosmos DB のトランザクション パフォーマンスに影響します。

```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

streamQuery = dfStream\
        .writeStream\
        .format("cosmos.oltp")\
        .outputMode("append")\
        .option("checkpointLocation", "/localWriteCheckpointFolder")\
        .option("spark.synapse.linkedService", "INFERRED")\
        .option("spark.cosmos.container", "trafficSourceColl_sink")\
        .option("spark.cosmos.connection.mode", "gateway")\
        .start()

streamQuery.awaitTermination()
```

**Scala** での同じコード ジェスチャは、次のコードのようになります。
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val query = dfStream.
            writeStream.
            format("cosmos.oltp").
            outputMode("append").
            option("checkpointLocation", "/localWriteCheckpointFolder").
            option("spark.synapse.linkedService", "pySparkSamplesDb").
            option("spark.cosmos.container", "test2").
            option("spark.cosmos.connection.mode", "gateway").
            start()

query.awaitTermination()
```
## <a name="next-steps"></a>次のステップ

* [Synapse と Azure Cosmos DB の間でサポートされている内容について学習する](./concept-synapse-link-cosmos-db-support.md)
* [Azure Cosmos DB 用の Synapse Link に接続する](../quickstart-connect-synapse-link-cosmos-db.md)
