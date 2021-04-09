---
title: チュートリアル:ストレージ アカウント内のデータの分析を開始する
description: このチュートリアルでは、ストレージ アカウントにあるデータを分析する方法について説明します。
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 12/31/2020
ms.openlocfilehash: f18977bb92b37546d5980134cba858b1f76b464c
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104720017"
---
# <a name="analyze-data-in-a-storage-account"></a>ストレージ アカウント内のデータを分析する

このチュートリアルでは、ストレージ アカウントにあるデータを分析する方法について説明します。

## <a name="overview"></a>概要

ここまでは、データがワークスペース内のデータベースに存在するシナリオについて説明してきました。 次に、ストレージ アカウント内のファイルを操作する方法について説明します。 このシナリオでは、ワークスペースの作成時に指定したワークスペースとコンテナーのプライマリ ストレージ アカウントを使用します。

* ストレージ アカウントの名前: **contosolake**
* ストレージ アカウント内のコンテナーの名前: **users**

### <a name="create-csv-and-parquet-files-in-your-storage-account"></a>ストレージ アカウントに CSV および Parquet ファイルを作成する

ノートブックの新しいコード セルで次のコードを実行します。 ストレージ アカウントに CSV ファイルと parquet ファイルが作成されます。

```py
%%pyspark
df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df = df.repartition(1) # This ensure we'll get a single file during write()
df.write.mode("overwrite").csv("/NYCTaxi/PassengerCountStats_csvformat")
df.write.mode("overwrite").parquet("/NYCTaxi/PassengerCountStats_parquetformat")
```

### <a name="analyze-data-in-a-storage-account"></a>ストレージ アカウント内のデータを分析する

対象のワークスペースの既定の ADLS Gen2 アカウントのデータを分析できます。また、 **[管理]** > **[リンクされたサービス]** > **[新規]** を使用して、ADLS Gen2 または BLOB ストレージ アカウントを対象のワークスペースにリンクすることもできます (以下の手順ではプライマリ ADLS Gen2 アカウントを参照します)。

1. Synapse Studio の **[データ]** ハブに移動し、 **[Linked]\(リンク済み\)** を選択します。
1. **[Azure Data Lake Storage Gen2]**  >  **[myworkspace (Primary - contosolake)]\(myworkspace (プライマリ - contosolake)\)** に移動します。
1. **[users (プライマリ)]** を選択します。 **NYCTaxi** フォルダーが表示されます。 その中に、**PassengerCountStats_csvformat** および **PassengerCountStats_parquetformat** という 2 つのフォルダーが表示されます。
1. **PassengerCountStats_parquetformat** フォルダーを開きます。 内部に、`part-00000-2638e00c-0790-496b-a523-578da9a15019-c000.snappy.parquet` のような名前の parquet ファイルがあります。
1. **parquet** を右クリックして、 **[新しいノートブック]** を選択し、 **[Load to DataFrame]\(データフレームに読み込む\)** を選択します。 次のようなセルを含む新しいノートブックが作成されます。

    ```py
    %%pyspark
    abspath = 'abfss://users@contosolake.dfs.core.windows.net/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet'
    df = spark.read.load(abspath, format='parquet')
    display(df.limit(10))
    ```

1. **Spark1** という名前の Spark プールにアタッチします。 セルを実行します。
1. **users** フォルダーをクリックします。 **parquet** ファイルをもう一度右クリックし、 **[New SQL script]\(新しい SQL スクリプト\)**  >  **[SELECT TOP 100 rows]\(上位 100 行の選択\)** の順に選択します。 次のような SQL スクリプトが作成されます。

    ```sql
    SELECT 
        TOP 100 *
    FROM OPENROWSET(
        BULK 'https://contosolake.dfs.core.windows.net/users/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet',
        FORMAT='PARQUET'
    ) AS [result]
    ```

    スクリプト ウィンドウで、 **[接続先]** フィールドが **組み込み** のサーバーレス SQL プールに設定されていることを確認します。

1. スクリプトを実行します。



## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [パイプラインを使用してアクティビティを調整する](get-started-pipelines.md)
