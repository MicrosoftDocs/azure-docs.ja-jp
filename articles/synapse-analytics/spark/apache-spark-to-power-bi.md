---
title: Azure Synapse Studio ノートブック
description: このチュートリアルでは、Apache Spark とサーバーレス SQL プールを使用して、Power BI ダッシュボードを作成する方法について概要を説明します。
services: synapse-analytics
author: midesa
ms.author: midesa
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: spark
ms.topic: tutorial
ms.date: 11/16/2020
ms.openlocfilehash: 53fb256d3a0fba1cc10cafb239ff7f7bb21ac4eb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98121075"
---
# <a name="tutorial-create-a-power-bi-report-using-apache-spark-and-azure-synapse-analytics"></a>チュートリアル:Apache Spark と Azure Synapse Analytics を使用して Power BI レポートを作成する

組織は多くの場合、業務上の主な利害関係者に提供する前に大量のデータを処理する必要があります。 このチュートリアルでは、Apache Spark を使用してデータを処理し、そのデータを Power BI とサーバーレス SQL を通じてエンドユーザーに提供するうえで、Azure Synapse Analytics の統合されたエクスペリエンスを活用する方法について説明します。

## <a name="before-you-begin"></a>開始する前に
- ADLS Gen2 ストレージ アカウントが既定のストレージとして構成されている [Azure Synapse Analytics ワークスペース](../quickstart-create-workspace.md)。 
- データを可視化するための Power BI ワークスペースと Power BI Desktop。 詳細については、[Power BI ワークスペースの作成](/power-bi/service-create-the-new-workspaces)と [Power BI Desktop のインストール](https://powerbi.microsoft.com/downloads/)に関するページを参照してください。
- Azure Synapse Analytics と Power BI ワークスペースを接続するためのリンクされたサービス。 詳細については、[Power BI ワークスペースにリンクする](../quickstart-power-bi.md)方法に関するページを参照してください。
- Synapse Analytics ワークスペース内のサーバーレス Apache Spark プール。 詳細については、[サーバーレス Apache Spark プールを作成する](../quickstart-create-apache-spark-pool-studio.md)方法に関するページを参照してください。
  
## <a name="download-and-prepare-the-data"></a>データのダウンロードと準備
この例では、Apache Spark を使用して、ニューヨークからのタクシー乗車のチップ データに対して、いくつかの分析を実行します。 データは [Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) から入手できます。 このデータセットのサブセットには、イエロー タクシー乗車に関する情報が格納され、各乗車、出発時刻と到着時刻、場所、料金、およびその他の関心の高い属性に関する情報が含まれます。

1. 次の行を実行して、新しいセルにコードを貼り付けて、Spark データフレームを作成します。 これにより、Open Dataset API を介してデータが取得されます。 このデータをすべてプルすると、約 15 億行が生成されます。 次のコード例では、start_date と end_date を使用し、1 か月分のデータを返すフィルターを適用します。
   
   ```python
    from azureml.opendatasets import NycTlcYellow
    from dateutil import parser
    from datetime import datetime

    end_date = parser.parse('2018-06-06')
    start_date = parser.parse('2018-05-01')
    nyc_tlc = NycTlcYellow(start_date=start_date, end_date=end_date)
    filtered_df = nyc_tlc.to_spark_dataframe()
   ```
2. Apache Spark SQL を使用して、NycTlcTutorial というデータベースを作成します。 このデータベースを使用して、データ処理の結果を格納することになります。
   ```python
   %%pyspark
    spark.sql("CREATE DATABASE IF NOT EXISTS NycTlcTutorial")
   ```
3. 次に、Spark データフレーム操作を使用してデータを処理します。 次のコードでは、以下の変換を実行します。
   1. 不要な列の除去。
   2. フィルター処理による外れ値/誤った値の除去。
   3. 詳しい分析を目的とする ```tripTimeSecs``` や ```tipped``` といった新しい機能の作成。
    ```python
    from pyspark.sql.functions import unix_timestamp, date_format, col, when

    taxi_df = filtered_df.select('totalAmount', 'fareAmount', 'tipAmount', 'paymentType', 'rateCodeId', 'passengerCount'\
                                    , 'tripDistance', 'tpepPickupDateTime', 'tpepDropoffDateTime'\
                                    , date_format('tpepPickupDateTime', 'hh').alias('pickupHour')\
                                    , date_format('tpepPickupDateTime', 'EEEE').alias('weekdayString')\
                                    , (unix_timestamp(col('tpepDropoffDateTime')) - unix_timestamp(col('tpepPickupDateTime'))).alias('tripTimeSecs')\
                                    , (when(col('tipAmount') > 0, 1).otherwise(0)).alias('tipped')
                                    )\
                            .filter((filtered_df.passengerCount > 0) & (filtered_df.passengerCount < 8)\
                                    & (filtered_df.tipAmount >= 0) & (filtered_df.tipAmount <= 25)\
                                    & (filtered_df.fareAmount >= 1) & (filtered_df.fareAmount <= 250)\
                                    & (filtered_df.tipAmount < filtered_df.fareAmount)\
                                    & (filtered_df.tripDistance > 0) & (filtered_df.tripDistance <= 100)\
                                    & (filtered_df.rateCodeId <= 5)
                                    & (filtered_df.paymentType.isin({"1", "2"})))
    ```
4. 最後に、Apache Spark の ```saveAsTable``` メソッドを使用してデータフレームを保存します。 そうすることで、後でサーバーレス SQL プールを使用して、同じテーブルに対してクエリを実行したり接続したりすることができます。
  ```python
     taxi_df.write.mode("overwrite").saveAsTable("NycTlcTutorial.nyctaxi")
  ```
   
## <a name="query-data-using-serverless-sql-pools"></a>サーバーレス SQL プールを使用してデータに対してクエリを実行する
Azure Synapse Analytics では、さまざまなワークスペース計算エンジンが、サーバーレス Apache Spark プールとサーバーレス SQL プールの間でデータベースとテーブルを共有できます。 それを支えているのが、Synapse の[共有メタデータ管理](../metadata/overview.md)機能です。 結果として、Spark によって作成されたデータベースと Parquet でサポートされるテーブルは、ワークスペースのサーバーレス SQL プールに表示されるようになります。

サーバーレス SQL プールを使用して Apache Spark テーブルに対してクエリを実行するには:
   1. Apache Spark テーブルを保存したら、 **[データ]** タブに切り替えます。
   
   2. **[ワークスペース]** で、先ほど作成した Apache Spark テーブルを見つけて、 **[New SQL script]\(新しい SQL スクリプト\)** を選択し、 **[Select TOP 100 rows]\(上位 100 行の選択\)** を選択します。 
      
      :::image type="content" source="../spark/media/apache-spark-power-bi/query-spark-table-with-sql.png" alt-text="SQL クエリ。" border="true":::

   3. 引き続き、クエリの検索条件を絞り込んだり、SQL グラフ作成オプションを使用して結果を視覚化したりすることもできます。

## <a name="connect-to-power-bi"></a>Power BI に接続する
次に、サーバーレス SQL プールを Power BI ワークスペースに接続します。 ワークスペースを接続したら、Azure Synapse Analytics (直接) と Power BI Desktop の両方で Power BI レポートを作成することができます。

>[!Note]
> 始める前に、[Power BI ワークスペース](../quickstart-power-bi.md)に対するリンクされたサービスを設定し、[Power BI Desktop](/power-bi/service-create-the-new-workspaces) をダウンロードする必要があります。  

サーバーレス SQL プールを Power BI ワークスペースに接続するには:

1.  **[Power BI データセット]** タブに移動し、 **[+ 新しいデータセット]** オプションを選択します。 プロンプトから、データ ソースとして使用したい SQL Analytics データベースの .pbids ファイルをダウンロードします。 
   :::image type="content" source="../spark/media/apache-spark-power-bi/power-bi-datasets.png" alt-text="Power BI データセット。" border="true":::

2.  このファイルを Power BI Desktop で開いて、データセットを作成します。 ファイルを開いたら、 **[Microsoft アカウント]** と **[インポート]** オプションを使用して、SQL Server データベースに接続します。 
   

## <a name="create-a-power-bi-report"></a>Power BI レポートを作成する
1. Power BI Desktop から、**キー インフルエンサー** グラフをレポートに追加することができます。
   
   1. 平均 *tipAmount* 列を **[分析]** 軸にドラッグします。
   
   2. **weekdayString**、平均 **tripDistance**、平均 **tripTimeSecs** の各列を **[説明]** 軸にドラッグします。 
   
   :::image type="content" source="../spark/media/apache-spark-power-bi/power-bi-desktop.png" alt-text="Power BI Desktop。" border="true":::

2. Power BI Desktop の [ホーム] タブで **[発行]** を選択し、変更を **保存** します。 ファイル名を入力し、このレポートを "*NycTaxiTutorial ワークスペース*" に保存します。
   
3. さらに、Azure Synapse Analytics ワークスペース内から Power BI の視覚化を作成することもできます。 そのためには、Azure Synapse ワークスペースの **[開発]** タブに移動し、[Power BI] タブを開きます。そこからレポートを選択し、引き続き追加の視覚化を作成することができます。 
   
   :::image type="content" source="../spark/media/apache-spark-power-bi/power-bi-synapse.png" alt-text="Azure Synapse Analytics ワークスペース。" border="true":::

サーバーレス SQL を通じてデータセットを作成し、Power BI に接続する方法について詳しくは、[Power BI Desktop への接続](../../synapse-analytics/sql/tutorial-connect-power-bi-desktop.md)に関するチュートリアルを参照してください。

## <a name="next-steps"></a>次のステップ
続いて、次のドキュメントおよびチュートリアルを参照して、Azure Synapse Analytics のデータの視覚化機能について理解を深めましょう。
   - [サーバーレス Apache Spark プールを使用してデータを視覚化する](../spark/apache-spark-data-visualization-tutorial.md)
   - [Apache Spark プールを使用したデータ視覚化の概要](../spark/apache-spark-data-visualization.md)