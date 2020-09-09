---
title: チュートリアル:Spark を使用した分析の概要
description: このチュートリアルでは、Azure Synapse Analytics を設定して使用する基本的な手順について説明します。
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 5c6b35c1d9f00cae8fc688569e3a491679900995
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87093800"
---
# <a name="analyze-with-apache-spark"></a>Apache Spark を使用して分析を行う

このチュートリアルでは、Apache Spark for Azure Synapse でデータを読み込んで分析する基本的な手順について説明します。

## <a name="load-the-nyc-taxi-data-into-the-spark-nyctaxi-database"></a>NYC タクシーのデータを Spark nyctaxi データベースに読み込む

**SQLDB1** のテーブルで使用できるデータがあります。 それを **nyctaxi** という名前の Spark データベースに読み込みます。

1. Synapse Studio で、 **[開発]** ハブに移動します。
1. **[+]**  >  **[ノートブック]** を選択します。
1. ノートブックの上部にある **[アタッチ先]** の値を **[Spark1]** に設定します。
1. **[コードの追加]** を選択してノートブックのコード セルを追加し、次のテキストを貼り付けます。

    ```scala
    %%spark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    val df = spark.read.sqlanalytics("SQLDB1.dbo.Trip") 
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```

1. **[データ]** ハブに移動し、 **[データベース]** を右クリックして、 **[更新]** を選択します。 次のデータベースが表示されます。

    - **SQLDB1** (SQL プール)
    - **nyctaxi** (Spark)

## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>Spark とノートブックを使用して NYC タクシーのデータを分析する

1. ノートブックに戻ります。
1. 新しいコード セルを作成し、次のテキストを入力します。 その後、このセルを実行して、**nyctaxi** Spark データベースに読み込んだ NYC Taxi データを表示します。

   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. 次のコードを実行して、先ほど SQL プール **SQLDB1** で行ったのと同じ分析を実行します。 このコードにより、分析の結果が **nyctaxi.passengercountstats** という名前のテーブルに保存され、結果が視覚化されます。

   ```py
   %%pyspark
   df = spark.sql("""
      SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
      FROM nyctaxi.trip
      WHERE TripDistanceMiles > 0 AND PassengerCount > 0
      GROUP BY PassengerCount
      ORDER BY PassengerCount
   """) 
   display(df)
   df.write.saveAsTable("nyctaxi.passengercountstats")
   ```

1. セルの結果で **[グラフ]** を選択し、視覚化されたデータを確認します。

## <a name="customize-data-visualization-with-spark-and-notebooks"></a>Spark とノートブックを使用してデータの視覚化をカスタマイズする

グラフのレンダリング方法は、ノートブックを使用して制御できます。 次のコードでは、簡単な例を示します。 一般的なライブラリである **matplotlib** と **seaborn** が使用されています。 先ほど実行した SQL クエリと同じ種類の折れ線グラフがレンダリングされます。

```py
%%pyspark
import matplotlib.pyplot
import seaborn

seaborn.set(style = "whitegrid")
df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df = df.toPandas()
seaborn.lineplot(x="PassengerCount", y="SumTripDistance" , data = df)
seaborn.lineplot(x="PassengerCount", y="AvgTripDistance" , data = df)
matplotlib.pyplot.show()
```



## <a name="load-data-from-a-spark-table-into-a-sql-pool-table"></a>Spark テーブルから SQL プール テーブルにデータを読み込む

先ほど SQL プール テーブル **SQLDB1.dbo.Trip** のデータを Spark テーブル **nyctaxi.trip** にコピーしました。 次に、Spark を使用して、データを Spark テーブル **nyctaxi.passengercountstats** に集約しました。 今度は、**nyctaxi.passengercountstats** のデータを **SQLDB1.dbo.PassengerCountStats** という名前の SQL プール テーブルにコピーします。

ノートブックで次のセルを実行します。 集約された Spark テーブルが再度 SQL プール テーブルにコピーされます。

```scala
%%spark
val df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df.write.sqlanalytics("SQLDB1.dbo.PassengerCountStats", Constants.INTERNAL )
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [ストレージ内のデータを分析する](get-started-analyze-storage.md)


