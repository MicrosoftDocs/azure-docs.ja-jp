---
title: "Azure HDInsight でデータ視覚化ツールを使用する Spark BI | Microsoft Docs"
description: "HDInsight クラスター上で Apache Spark BI を使用して分析用のデータ視覚化ツールを使用する"
keywords: "apache spark bi,spark bi, spark データ視覚化, spark ビジネス インテリジェンス"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 1448b536-9bc8-46bc-bbc6-d7001623642a
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: nitinme
ms.openlocfilehash: 869a000909813e607620c47ef802b4043e37dfa9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="apache-spark-bi-using-data-visualization-tools-with-azure-hdinsight"></a>Azure HDInsight のデータ視覚化ツールを使用する Apache Spark BI

データ視覚化ツール (Power BI や Tableau など) を使用し、HDInsight クラスター上で Apache Spark BI を使用して生のサンプル データ セットを分析する方法について説明します。

> [!NOTE]
> Azure HDInsight 3.6 プレビューの Spark 2.1 では、この記事で説明されている BI ツールとの接続はサポートされていません。 Spark バージョン 1.6 および 2.0 のみ (それぞれ、HDInsight 3.4、3.5 の場合) がサポートされています。
>

このチュートリアルは、HDInsight Spark クラスター上の Jupyter Notebook としても利用できます。 Notebook エクスペリエンスにより、Notebook 自体から Python のスニペットを実行することができます。 Notebook からチュートリアルを実行するには、Spark クラスターを作成し、Jupyter Notebook (`https://CLUSTERNAME.azurehdinsight.net/jupyter`) を起動し、**Python フォルダー**にある**Use BI tools with Apache Spark on HDInsight.ipynb (HDInsight.ipynb に対して BI ツールと Apache Spark を使用する)** Notebook を実行します。

## <a name="prerequisites"></a>前提条件

* HDInsight での Apache Spark クラスター。 手順については、 [Azure HDInsight での Apache Spark クラスターの作成](hdinsight-apache-spark-jupyter-spark-sql.md)に関するページを参照してください。


## <a name="hivetable"></a>Spark データ視覚化の準備

このセクションでは、HDInsight Spark クラスターから [Jupyter](https://jupyter.org) Notebook を使用し、生サンプル データを処理してテーブルとして保存するジョブを実行します。 サンプル データは、すべてのクラスターにおいて既定で使用できる .csv ファイル (hvac.csv) です。 目的のデータをテーブルとして保存した後、その次のセクションで BI ツールを使ってそのテーブルにアクセスし、データの視覚化を実行します。

> [!NOTE]
> 「[HDInsight Spark クラスターに対して対話型クエリを実行する](hdinsight-apache-spark-load-data-run-query.md)」の手順を実行した後にこの記事の手順を実行している場合は、以下の手順 8 まで進むことができます。
>

1. [Azure Portal](https://portal.azure.com/) のスタート画面で Spark クラスターのタイルをクリックします (スタート画面にピン留めしている場合)。 **[すべて参照]** > **[HDInsight クラスター]** でクラスターに移動することもできます。   

2. Spark クラスター ブレードから **[クラスター ダッシュボード]** をクリックし、**[Jupyter Notebook]** をクリックします。 入力を求められたら、クラスターの管理者資格情報を入力します。

   > [!NOTE]
   > ブラウザーで次の URL を開き、クラスターの Jupyter Notebook にアクセスすることもできます。 **CLUSTERNAME** をクラスターの名前に置き換えます。
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   >
   >

3. Notebook を作成します。 **[新規]** をクリックし、**[PySpark]** をクリックします。

    ![Apache Spark BI の Jupyter Notebook を作成](./media/hdinsight-apache-spark-use-bi-tools/create-jupyter-notebook-for-spark-bi.png "Apache Spark BI の Jupyter Notebook を作成")

4. Untitled.pynb という名前の新しい Notebook が作成されて開かれます。 上部の Notebook 名をクリックし、わかりやすい名前を入力します。

    ![Apache Spark BI の Notebook の名前を指定](./media/hdinsight-apache-spark-use-bi-tools/jupyter-notebook-name-for-spark-bi.png "Apache Spark BI の Notebook の名前を指定")

5. PySpark カーネルを使用して Notebook を作成したため、コンテキストを明示的に作成する必要はありません。 最初のコード セルを実行すると、Spark と Hive コンテキストが自動的に作成されます。 このシナリオに必要な種類をインポートすることから始めることができます。 これを行うには、セルにカーソルを置き、 **SHIFT + ENTER**キーを押します。

        from pyspark.sql import *

6. サンプル データを一時テーブルに読み込みます。 HDInsight の Spark クラスターを作成すると、サンプル データ ファイル **hvac.csv** が、関連するストレージ アカウントの **\HdiSamples\HdiSamples\SensorSampleData\hvac** にコピーされます。

    次のスニペットを空のセルに貼り付けて、 **Shift + Enter**キーを押します。 このスニペットは、**hvac** というテーブルにデータを登録します。

        # Create an RDD from sample data
        hvacText = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

        # Create a schema for our data
        Entry = Row('Date', 'Time', 'TargetTemp', 'ActualTemp', 'BuildingID')

        # Parse the data and create a schema
        hvacParts = hvacText.map(lambda s: s.split(',')).filter(lambda s: s[0] != 'Date')
        hvac = hvacParts.map(lambda p: Entry(str(p[0]), str(p[1]), int(p[2]), int(p[3]), int(p[6])))

        # Infer the schema and create a table       
        hvacTable = sqlContext.createDataFrame(hvac)
        hvacTable.registerTempTable('hvactemptable')
        dfw = DataFrameWriter(hvacTable)
        dfw.saveAsTable('hvac')

7. テーブルが正常に作成されたことを確認します。 `%%sql` マジックを使用して、Hive クエリを直接実行できます。 `%%sql` マジックの詳細と、PySpark カーネルで使用できるその他のマジックの詳細については、[Spark HDInsight クラスターと Jupyter Notebook で使用可能なカーネル](hdinsight-apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic)に関する記事を参照してください。

        %%sql
        SHOW TABLES

    以下のような結果が出力されます。

        +---------------+-------------+
        |tableName      |isTemporary  |
        +---------------+-------------+
        |hvactemptable  |true        |
        |hivesampletable|false        |
        |hvac           |false        |
        +---------------+-------------+

    **isTemporary** 列が false の Hive テーブルだけが metastore に格納されて、BI ツールからアクセスできるようになります。 このチュートリアルでは、作成した **hvac** テーブルに接続します。

8. テーブルに目的のデータが含まれることを確認します。 Notebook の空のセルに次のスニペットをコピーして、 **Shift + Enter**キーを押します。

        %%sql
        SELECT * FROM hvac LIMIT 10

9. Notebook をシャットダウンしてリソースを解放します。 そのためには、Notebook の **[ファイル]** メニューの **[Close and Halt]** (閉じて停止) をクリックします。

## <a name="powerbi"></a>Spark データ視覚化のための Power BI の使用

> [!NOTE]
> このセクションは、HDInsight 3.4 上の Spark 1.6 と、HDInsight 3.5 上の Spark 2.0 にのみ適用されます。
>
>

テーブルとしてデータを保存した後は、Power BI を使用してデータに接続し、視覚化してレポートやダッシュボードなどを作成できます。

1. Power BI へのアクセス権があることを確認します。 Power BI の無料プレビュー サブスクリプションは [http://www.powerbi.com/](http://www.powerbi.com/)から入手できます。

2. [Power BI](http://www.powerbi.com/) にサインインします。

3. 左側のウィンドウ下部にある **[データを取得]** をクリックします。

4. **[データを取得]** ページの **[データのインポートまたは接続]** で、**[データベース]** の **[取得]** をクリックします。

    ![Apache Spark BI の Power BI にデータを取得](./media/hdinsight-apache-spark-use-bi-tools/apache-spark-bi-import-data-power-bi.png "Apache Spark BI の Power BI にデータを取得")

5. 次の画面で、**[Azure HDInsight 上の Spark]**、**[接続]** の順にクリックします。 メッセージが表示されたら、クラスターの URL (`mysparkcluster.azurehdinsight.net`) と、クラスターに接続するための資格情報を入力します。

    ![Apache Spark BI に接続](./media/hdinsight-apache-spark-use-bi-tools/connect-to-apache-spark-bi.png "Apache Spark BI に接続")

    接続が確立されると、Power BI は HDInsight の Spark クラスターからデータのインポートを開始します。

6. Power BI がデータをインポートし、**[データセット]** 見出しの下に **Spark** データセットが追加されます。 新しいワークシートを開いてデータを表示するには、データ セットをクリックします。 ワークシートはレポートとして保存することもできます。 ワークシートを保存するには、**[ファイル]** メニューの **[保存]** をクリックします。

    ![Power BI ダッシュボードの Apache Spark BI タイル](./media/hdinsight-apache-spark-use-bi-tools/apache-spark-bi-tile-dashboard.png "Power BI ダッシュボードの Apache Spark BI タイル")
7. 右側の **[フィールド]** 一覧に、前に作成した **hvac** テーブルが含まれることに注意してください。 テーブルを展開し、Notebook で定義したフィールドを表示します。

      ![Apache Spark BI ダッシュボードにテーブルを一覧表示](./media/hdinsight-apache-spark-use-bi-tools/apache-spark-bi-display-tables.png "Apache Spark BI ダッシュボードにテーブルを一覧表示")

8. 各ビルの目標温度と実際の温度の差を示す表示を作成します。 データを表示するには、**[面グラフ]** (赤で囲まれています) を選択します。 軸を定義するために、**BuildingID** フィールドを **[軸]** に、**ActualTemp**/**TargetTemp** フィールドを **[値]** にドラッグ アンド ドロップします。

    ![Apache Spark BI を使用して Spark データ視覚化を作成](./media/hdinsight-apache-spark-use-bi-tools/apache-spark-bi-add-value-columns.png "Apache Spark BI を使用して Spark データ視覚化を作成")

9. 既定では、**ActualTemp** および **TargetTemp** の合計が表示されます。 どちらのフィールドについても、ドロップダウンから **[平均]** を選択して、両方のビルの実際温度と目標温度の平均を表示します。

    ![Apache Spark BI を使用して Spark データ視覚化を作成](./media/hdinsight-apache-spark-use-bi-tools/apache-spark-bi-average-of-values.png "Apache Spark BI を使用して Spark データ視覚化を作成")

10. 次のスクリーンショットのようにデータが視覚化されます。 グラフの上にカーソルを移動すると、関連データを含むツール ヒントが表示されます。

    ![Apache Spark BI を使用して Spark データ視覚化を作成](./media/hdinsight-apache-spark-use-bi-tools/apache-spark-bi-area-graph.png "Apache Spark BI を使用して Spark データ視覚化を作成")

11. 上部メニューの **[保存]** をクリックし、レポート名を指定します。 表示を固定することもできます。 視覚化を固定するとダッシュボードに保存されて、最新の値を一目で追跡できるようになります。

   同じデータセットの表示をいくつでも追加して、データのスナップショット用にダッシュボードに固定できます。 また、HDInsight の Spark クラスターは Power BI に直接接続されます。 つまり、Power BI には常にクラスターの最新データが提供され、データセットを定期的に更新する必要はありません。

## <a name="tableau"></a>Spark データ視覚化のための Tableau Desktop の使用

> [!NOTE]
> このセクションは、Azure HDInsight で作成された Spark 1.5.2 クラスターにのみ適用できます。
>
>

1. この Apache Spark BI チュートリアルを行っているコンピューターに [Tableau Desktop](http://www.tableau.com/products/desktop) をインストールします。

2. そのコンピューターに Microsoft Spark ODBC ドライバーもインストールされていることを確認します。 ドライバーは [ここ](http://go.microsoft.com/fwlink/?LinkId=616229)からインストールできます。

1. Tableau Desktop を起動します。 左側のウィンドウの接続先サーバー一覧で **[Spark SQL]**をクリックします。 Spark SQL が既定で左側のウィンドウに表示されない場合は、 **[その他のサーバー]**をクリックして検索できます。
2. Spark SQL 接続ダイアログ ボックスに、次のスクリーンショットのように値を指定して **[OK]** をクリックします。

    ![Apache Spark BI のクラスターに接続](./media/hdinsight-apache-spark-use-bi-tools/connect-to-tableau-apache-spark-bi.png "Apache Spark BI のクラスターに接続")

    **Microsoft Spark ODBC** ドライバーをコンピューターにインストールしてある場合にのみ、認証ドロップダウンに [[Microsoft Azure HDInsight サービス]](http://go.microsoft.com/fwlink/?LinkId=616229) がオプションとして表示されます。
3. 次の画面で、**[スキーマ]** ドロップダウンの **[検索]** アイコンをクリックし、**[デフォルト]** をクリックします。

    ![Apache Spark BI のスキーマを検索](./media/hdinsight-apache-spark-use-bi-tools/tableau-find-schema-apache-spark-bi.png "Apache Spark BI のスキーマを検索")
4. **[テーブル]** フィールドで **[検索]** アイコンをクリックし、クラスターで使用可能なすべての Hive テーブルを一覧表示します。 Notebook を使用して前に作成した **hvac** テーブルが表示されます。

    ![Apache Spark BI のテーブルを検索](./media/hdinsight-apache-spark-use-bi-tools/tableau-find-table-apache-spark-bi.png "Apache Spark BI のテーブルを検索")
5. テーブルをドラッグして右側上部のボックスにドロップします。 Tableau はデータをインポートし、赤い四角で強調表示されているようにスキーマを表示します。

    ![Apache Spark BI の Tableau にデータを追加](./media/hdinsight-apache-spark-use-bi-tools/tableau-add-table-apache-spark-bi.png "Apache Spark BI の Tableau にデータを追加")
6. 左下の **[Sheet1]** タブをクリックします。 すべてのビルの各日の目標温度と実際の温度の平均を表示するグラフを作成します。 **[日付]** と **[ビル ID]** を **[列]** に、**[実際の温度]**/**[目標温度]** を **[行]** にドラッグします。 **[マーク]** で **[領域]** を選択して、Spark データ視覚化で領域マップを使用します。

     ![Spark データ視覚化のフィールドを追加](./media/hdinsight-apache-spark-use-bi-tools/spark-data-visualization-add-fields.png "Spark データ視覚化のフィールドを追加")
7. 既定では、温度フィールドは集計として表示されます。 代わりに平均温度を表示する場合は、次のようにしてドロップダウンから行うことができます。

    ![Spark データ視覚化の平均値の取得](./media/hdinsight-apache-spark-use-bi-tools/spark-data-visualization-average-temperature.png "Spark データ視覚化の平均値の取得")

8. 一方の温度を他の温度にスーパーインポーズして、温度の違いを見やすくすることもできます。 マウスを下部領域マップの隅に移動し、赤い丸で囲んだハンドル形状にします。 マップを上部の他のマップにドラッグし、マウスが赤い四角で囲んだ形状になったら放します。

    ![Spark データ視覚化のマップをマージ](./media/hdinsight-apache-spark-use-bi-tools/spark-data-visualization-merge-maps.png "Spark データ視覚化のマップをマージ")

     データの視覚化が、次のスクリーンショットのように変化します。

    ![Spark データ視覚化の Tableau 出力](./media/hdinsight-apache-spark-use-bi-tools/spark-data-visualization-tableau-output.png "Spark データ視覚化の Tableau 出力")
9. **[保存]** をクリックしてワークシートを保存します。 ダッシュボードを作成して 1 つまたは複数のシートを追加できます。

## <a name="next-steps"></a>次のステップ

これまでに、クラスターを作成し、データを照会するための Spark データ フレームを作成し、BI ツールからそのデータにアクセスする方法を学習しました。 次は、クラスターのリソースを管理し、HDInsight Spark クラスターで実行されているジョブをデバッグする方法を見ていきましょう。

* [Azure HDInsight での Apache Spark クラスターのリソースの管理](hdinsight-apache-spark-resource-manager.md)
* [HDInsight の Apache Spark クラスターで実行されるジョブの追跡とデバッグ](hdinsight-apache-spark-job-debugging.md)

