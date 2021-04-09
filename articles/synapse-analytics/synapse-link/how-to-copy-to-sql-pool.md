---
title: Apache Spark を使用して Synapse Link for Azure Cosmos DB のデータを専用 SQL プールにコピーする
description: Spark データフレームにデータを読み込み、データをキュレーションして、それを専用 SQL プール テーブルに読み込みます。
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: synapse-link
ms.date: 08/10/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.custom: cosmos-db
ms.openlocfilehash: 755782b117ef5a5619ddde3e1573fe1805117e77
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104868564"
---
# <a name="copy-data-from-azure-cosmos-db-into-a-dedicated-sql-pool-using-apache-spark"></a>Apache Spark を使用して Azure Cosmos DB から専用 SQL プールにデータをコピーする

Azure Synapse Link for Azure Cosmos DB を使用すると、ユーザーは Azure Cosmos DB のオペレーショナル データに対してほぼリアルタイムの分析を実行できます。 ただし、データ ウェアハウス ユーザーにサービスを提供するために、一部のデータを集計してエンリッチメント処理することが必要な場合があります。 Synapse Link のデータのキュレーションとエクスポートは、ノートブックの少数のセルで実行できます。

## <a name="prerequisites"></a>前提条件
* 以下を使用して、[Synapse ワークスペースをプロビジョニングする](../quickstart-create-workspace.md)
    * [サーバーレス Apache Spark プール](../quickstart-create-apache-spark-pool-studio.md)
    * [専用 SQL プール](../quickstart-create-sql-pool-studio.md)
* [データが含まれた HTAP コンテナーで Cosmos DB アカウントをプロビジョニングする](../../cosmos-db/configure-synapse-link.md)
* [Azure Cosmos DB HTAP コンテナーをワークスペースに接続する](./how-to-connect-synapse-link-cosmos-db.md)
* [Spark から専用 SQL プールにデータをインポートするための適切なセットアップを行う](../spark/synapse-spark-sql-pool-import-export.md)

## <a name="steps"></a>手順
このチュートリアルでは、トランザクション ストアに影響を与えないように分析ストアに接続します (要求ユニットを消費しません)。 ここでは、次の手順について説明します。
1. Cosmos DB HTAP コンテナーを Spark データフレームに読み取る
2. 新しいデータフレームで結果を集計する
3. 専用 SQL プールにデータを取り込む

[![Spark から SQL への手順 1](../media/synapse-link-spark-to-sql/synapse-spark-to-sql.png)](../media/synapse-link-spark-to-sql/synapse-spark-to-sql.png#lightbox)

## <a name="data"></a>Data
この例では、**RetailSales** という HTAP コンテナーを使用します。 これは、**ConnectedData** というリンクされたサービスの一部です。スキーマは次のとおりです。
* _rid: string (nullable = true)
* _ts: long (nullable = true)
* logQuantity: double (nullable = true)
* productCode: string (nullable = true)
* quantity: long (nullable = true)
* price: long (nullable = true)
* id: string (nullable = true)
* advertising: long (nullable = true)
* storeId: long (nullable = true)
* weekStarting: long (nullable = true)
* _etag: string (nullable = true)

レポート用に、売上 ("*数量*"、"*収益*" (価格 x 数量)) を *productCode* と *weekStarting* で集計します。 最後に、そのデータを **dbo.productsales** という専用 SQL プール テーブルにエクスポートします。

## <a name="configure-a-spark-notebook"></a>Spark ノートブックを構成する
Scala as Spark (Scala) を主要言語とした Spark ノートブックを作成します。 セッションにはノートブックのデフォルト設定を使用します。

## <a name="read-the-data-in-spark"></a>Spark でデータを読み取る
Spark を使用した Cosmos DB HTAP コンテナーを最初のセルのデータフレームに読み取ります。

```java
val df_olap = spark.read.format("cosmos.olap").
    option("spark.synapse.linkedService", "ConnectedData").
    option("spark.cosmos.container", "RetailSales").
    load()
```

## <a name="aggregate-the-results-in-a-new-dataframe"></a>新しいデータフレームで結果を集計する

2 番目のセルでは、専用 SQL プール データベースに読み込む前に、新しいデータフレームに必要な変換と集計を実行します。

```java
// Select relevant columns and create revenue
val df_olap_step1 = df_olap.select("productCode","weekStarting","quantity","price").withColumn("revenue",col("quantity")*col("price"))
//Aggregate revenue, quantity sold and avg. price by week and product ID
val df_olap_aggr = df_olap_step1.groupBy("productCode","weekStarting").agg(sum("quantity") as "Sum_quantity",sum("revenue") as "Sum_revenue").
    withColumn("AvgPrice",col("Sum_revenue")/col("Sum_quantity"))
```

## <a name="load-the-results-into-a-dedicated-sql-pool"></a>結果を専用 SQL プールに読み込む

3 番目のセルでは、データを専用 SQL プールに読み込みます。 これにより、ジョブが完了したら削除される一時的な外部テーブル、外部データ ソース、外部ファイル形式が自動的に作成されます。

```java
df_olap_aggr.write.sqlanalytics("userpool.dbo.productsales", Constants.INTERNAL)
```

## <a name="query-the-results-with-sql"></a>SQL を使用して結果のクエリを実行する

次の SQL スクリプトのような単純な SQL クエリを使用して、結果のクエリを実行できます。
```sql
SELECT  [productCode]
,[weekStarting]
,[Sum_quantity]
,[Sum_revenue]
,[AvgPrice]
 FROM [dbo].[productsales]
```

クエリでは、グラフ モードで次の結果を表示します。[![Spark から SQL への手順 2](../media/synapse-link-spark-to-sql/sql-script-spark-sql.png)](../media/synapse-link-spark-to-sql/sql-script-spark-sql.png#lightbox)

## <a name="next-steps"></a>次のステップ
* [Apache Spark を使用して Azure Cosmos DB 分析ストアのクエリを実行する](./how-to-query-analytical-store-spark.md)