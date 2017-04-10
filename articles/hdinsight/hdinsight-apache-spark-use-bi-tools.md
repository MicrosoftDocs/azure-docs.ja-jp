---
title: "Azure HDInsight の Apache Spark での BI ツールの使用 | Microsoft Docs"
description: "Apache Spark で Notebook を使用して生データのスキーマを作成し、テーブルとして保存した後、BI ツールをテーブルに使用してデータを分析する手順を説明します"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 1448b536-9bc8-46bc-bbc6-d7001623642a
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 36b7aaf99db48efa1b56b84ac0616cf9ee2830ac
ms.lasthandoff: 03/18/2017


---
# <a name="use-bi-tools-with-apache-spark-cluster-on-azure-hdinsight"></a>Azure HDInsight での BI ツールと Apache Spark クラスターの使用

Azure HDInsight で Apache Spark を使って生サンプル データセットを分析し、BI ツールを使ってデータを視覚化する方法について説明します。 この記事では、HDInsight Spark クラスターで Power BI や Tableau などの BI ツールを使用する方法について説明します。

> [!NOTE]
> Azure HDInsight 3.6 プレビューの Spark 2.1 では、この記事で説明されている BI ツールとの接続はサポートされていません。 Spark バージョン 1.6 および 2.0 のみ (それぞれ、HDInsight 3.4、3.5 の場合) がサポートされています。
>

このチュートリアルは、HDInsight で作成する Spark (Linux) クラスター上の Jupyter Notebook としても利用できます。 Notebook エクスペリエンスにより、Notebook 自体から Python のスニペットを実行することができます。 Notebook からチュートリアルを実行するには、Spark クラスターを作成し、Jupyter Notebook (`https://CLUSTERNAME.azurehdinsight.net/jupyter`) を起動し、**Python フォルダー**にある**Use BI tools with Apache Spark on HDInsight.ipynb (HDInsight.ipynb に対して BI ツールと Apache Spark を使用する)** Notebook を実行します。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 [Azure 無料試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。
* HDInsight での Apache Spark クラスター。 手順については、「 [Create Apache Spark clusters in Azure HDInsight (Azure HDInsight での Apache Spark クラスターの作成)](hdinsight-apache-spark-jupyter-spark-sql.md)」を参照してください。


## <a name="hivetable"></a>生データをテーブルとして保存する

このセクションでは、HDInsight Spark クラスターから [Jupyter](https://jupyter.org) Notebook を使用し、生サンプル データを処理してテーブルとして保存するジョブを実行します。 サンプル データは、すべてのクラスターにおいて既定で使用できる .csv ファイル (hvac.csv) です。

目的のデータをテーブルとして保存した後、その次のセクションで BI ツールを使ってそのテーブルにアクセスし、さまざまな形で視覚化します。

1. [Azure Portal](https://portal.azure.com/) のスタート画面で Spark クラスターのタイルをクリックします (スタート画面にピン留めしている場合)。 **[すべて参照]** > **[HDInsight クラスター]** でクラスターに移動することもできます。   
2. Spark クラスター ブレードから **[クラスター ダッシュボード]** をクリックし、**[Jupyter Notebook]** をクリックします。 入力を求められたら、クラスターの管理者資格情報を入力します。

   > [!NOTE]
   > ブラウザーで次の URL を開き、クラスターの Jupyter Notebook にアクセスすることもできます。 **CLUSTERNAME** をクラスターの名前に置き換えます。
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   >
   >

3. Notebook を作成します。 **[新規]** をクリックし、**[PySpark]** をクリックします。

    ![Jupyter Notebook の作成](./media/hdinsight-apache-spark-use-bi-tools/hdispark.note.jupyter.createnotebook.png "Jupyter Notebook の作成")

4. Untitled.pynb という名前の新しい Notebook が作成されて開かれます。 上部の Notebook 名をクリックし、わかりやすい名前を入力します。

    ![Notebook の名前を指定](./media/hdinsight-apache-spark-use-bi-tools/hdispark.note.jupyter.notebook.name.png "Notebook の名前を指定")

5. PySpark カーネルを使用して Notebook を作成したため、コンテキストを明示的に作成する必要はありません。 最初のコード セルを実行すると、Spark と Hive コンテキストが自動的に作成されます。 このシナリオに必要な種類をインポートすることから始めることができます。 これを行うには、セルにカーソルを置き、 **SHIFT + ENTER**キーを押します。

        from pyspark.sql import *

6. サンプル データを一時テーブルに読み込みます。 HDInsight の Spark クラスターを作成すると、サンプル データ ファイル **hvac.csv** が、関連するストレージ アカウントの **\HdiSamples\HdiSamples\SensorSampleData\hvac** にコピーされます。

    次のスニペットを空のセルに貼り付けて、 **Shift + Enter**キーを押します。 このスニペットは、**hvac** というテーブルにデータを登録します。

        # Create an RDD from sample data
        hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

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
        |hvactemptable  |true         |
        |hivesampletable|false        |
        |hvac           |false        |
        +---------------+-------------+

    **isTemporary** 列が false の Hive テーブルだけが metastore に格納されて、BI ツールからアクセスできるようになります。 このチュートリアルでは、作成した **hvac** テーブルに接続します。

8. テーブルに目的のデータが含まれることを確認します。 Notebook の空のセルに次のスニペットをコピーして、 **Shift + Enter**キーを押します。

        %%sql
        SELECT * FROM hvac LIMIT 10

9. Notebook をシャットダウンしてリソースを解放します。 そのためには、Notebook の **[ファイル]** メニューの **[Close and Halt]** (閉じて停止) をクリックします。

## <a name="powerbi"></a>Power BI の使用

テーブルとしてデータを保存した後は、Power BI を使用してデータに接続し、視覚化してレポートやダッシュボードなどを作成できます。

1. Power BI へのアクセス権があることを確認します。 Power BI の無料プレビュー サブスクリプションは [http://www.powerbi.com/](http://www.powerbi.com/)から入手できます。

2. [Power BI](http://www.powerbi.com/) にサインインします。

3. 左側のウィンドウ下部にある **[データを取得]** をクリックします。

4. **[データを取得]** ページの **[データのインポートまたは接続]** で、**[データベース]** の **[取得]** をクリックします。

    ![Power BI へのデータの取得](./media/hdinsight-apache-spark-use-bi-tools/hdispark.powerbi.get.data.png "Power BI へのデータの取得")

5. 次の画面で、**[Azure HDInsight 上の Spark]**、**[接続]** の順にクリックします。 メッセージが表示されたら、クラスターの URL (`mysparkcluster.azurehdinsight.net`) と、クラスターに接続するための資格情報を入力します。

    ![Spark に接続](./media/hdinsight-apache-spark-use-bi-tools/power-bi-connect-to-spark.png "Power BI にデータを取得します")

    接続が確立されると、Power BI は HDInsight の Spark クラスターからデータのインポートを開始します。

6. Power BI がデータをインポートし、**[データセット]** 見出しの下に **Spark** データセットが追加されます。 新しいワークシートを開いてデータを表示するには、データ セットをクリックします。 ワークシートはレポートとして保存することもできます。 ワークシートを保存するには、**[ファイル]** メニューの **[保存]** をクリックします。

    ![Power BI ダッシュボードの Spark タイル](./media/hdinsight-apache-spark-use-bi-tools/hdispark.powerbi.tile.png "Power BI ダッシュボードの Spark タイル")
7. 右側の **[フィールド]** 一覧に、前に作成した **hvac** テーブルが含まれることに注意してください。 テーブルを展開し、Notebook で定義したフィールドを表示します。

      ![Hive テーブルの一覧表示](./media/hdinsight-apache-spark-use-bi-tools/hdispark.powerbi.display.tables.png "Hive テーブルの一覧表示")

8. 各ビルの目標温度と実際の温度の差を示す表示を作成します。 データを表示するには、**[面グラフ]** (赤で囲まれています) を選択します。 軸を定義するために、**BuildingID** フィールドを **[軸]** に、**ActualTemp**/**TargetTemp** フィールドを **[値]** にドラッグ アンド ドロップします。

    ![グラフの作成](./media/hdinsight-apache-spark-use-bi-tools/hdispark.powerbi.visual1.png "グラフの作成")

9. 既定では、**ActualTemp** および **TargetTemp** の合計が表示されます。 どちらのフィールドについても、ドロップダウンから **[平均]** を選択して、両方のビルの実際温度と目標温度の平均を表示します。

    ![グラフを作成します](./media/hdinsight-apache-spark-use-bi-tools/hdispark.powerbi.visual2.png)

10. 次のスクリーンショットのようにデータが視覚化されます。 グラフの上にカーソルを移動すると、関連データを含むツール ヒントが表示されます。

    ![グラフを作成します](./media/hdinsight-apache-spark-use-bi-tools/hdispark.powerbi.visual3.png)

11. 上部メニューの **[保存]** をクリックし、レポート名を指定します。 表示を固定することもできます。 視覚化を固定するとダッシュボードに保存されて、最新の値を一目で追跡できるようになります。

   同じデータセットの表示をいくつでも追加して、データのスナップショット用にダッシュボードに固定できます。 また、HDInsight の Spark クラスターは Power BI に直接接続されます。 つまり、Power BI には常にクラスターの最新データが提供され、データセットを定期的に更新する必要はありません。

## <a name="tableau"></a>Tableau Desktop を使用してテーブル内のデータを分析する

> [!NOTE]
> このセクションは、Azure HDInsight で作成された Spark 1.5.2 クラスターにのみ適用できます。
>
>

1. このチュートリアルを行っているコンピューターに [Tableau Desktop](http://www.tableau.com/products/desktop) をインストールします。

2. そのコンピューターに Microsoft Spark ODBC ドライバーもインストールされていることを確認します。 ドライバーは [ここ](http://go.microsoft.com/fwlink/?LinkId=616229)からインストールできます。

1. Tableau Desktop を起動します。 左側のウィンドウの接続先サーバー一覧で **[Spark SQL]**をクリックします。 Spark SQL が既定で左側のウィンドウに表示されない場合は、 **[その他のサーバー]**をクリックして検索できます。
2. Spark SQL 接続ダイアログ ボックスに、次のスクリーンショットのように値を指定して **[OK]** をクリックします。

    ![Spark クラスターへの接続](./media/hdinsight-apache-spark-use-bi-tools/hdispark.tableau.connect.png "Spark クラスターへの接続")

    **Microsoft Spark ODBC** ドライバーをコンピューターにインストールしてある場合にのみ、認証ドロップダウンに [[Microsoft Azure HDInsight サービス]](http://go.microsoft.com/fwlink/?LinkId=616229) がオプションとして表示されます。
3. 次の画面で、**[スキーマ]** ドロップダウンの **[検索]** アイコンをクリックし、**[デフォルト]** をクリックします。

    ![スキーマの検索](./media/hdinsight-apache-spark-use-bi-tools/hdispark.tableau.find.schema.png "スキーマの検索")
4. **[テーブル]** フィールドで **[検索]** アイコンをクリックし、クラスターで使用可能なすべての Hive テーブルを一覧表示します。 Notebook を使用して前に作成した **hvac** テーブルが表示されます。

    ![テーブルの検索](./media/hdinsight-apache-spark-use-bi-tools/hdispark.tableau.find.table.png "テーブルの検索")
5. テーブルをドラッグして右側上部のボックスにドロップします。 Tableau はデータをインポートし、赤い四角で強調表示されているようにスキーマを表示します。

    ![Tableau へのテーブルの追加](./media/hdinsight-apache-spark-use-bi-tools/hdispark.tableau.drag.table.png "Tableau へのテーブルの追加")
6. 左下の **[Sheet1]** タブをクリックします。 すべてのビルの各日の目標温度と実際の温度の平均を表示するグラフを作成します。 **[日付]** と **[ビル ID]** を **[列]** に、**[実際の温度]**/**[目標温度]** を **[行]** にドラッグします。 **[マーク]** で **[領域]** を選択して領域マップ グラフを使用します。

     ![グラフのフィールドの追加](./media/hdinsight-apache-spark-use-bi-tools/hdispark.tableau.drag.fields.png "グラフのフィールドの追加")
7. 既定では、温度フィールドは集計として表示されます。 代わりに平均温度を表示する場合は、次のようにしてドロップダウンから行うことができます。

    ![平均温度の取得](./media/hdinsight-apache-spark-use-bi-tools/hdispark.tableau.temp.avg.png "平均温度の取得")
8. 一方の温度を他の温度にスーパーインポーズして、温度の違いを見やすくすることもできます。 マウスを下部領域マップの隅に移動し、赤い丸で囲んだハンドル形状にします。 マップを上部の他のマップにドラッグし、マウスが赤い四角で囲んだ形状になったら放します。

    ![マップのマージ](./media/hdinsight-apache-spark-use-bi-tools/hdispark.tableau.merge.png "マップのマージ")

     データの視覚化が、次のスクリーンショットのように変化します。

    ![視覚化](./media/hdinsight-apache-spark-use-bi-tools/hdispark.tableau.final.visual.png "視覚化")
9. **[保存]** をクリックしてワークシートを保存します。 ダッシュボードを作成して&1; つまたは複数のシートを追加できます。

## <a name="seealso"></a>関連項目
* [概要: Azure HDInsight での Apache Spark](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>シナリオ
* [Spark と Machine Learning: HDInsight で Spark を使用して HVAC データを基に建物の温度を分析する](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark と Machine Learning: HDInsight で Spark を使用して食品の検査結果を予測する](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Spark ストリーミング: リアルタイム ストリーミング アプリケーションを作成するための HDInsight での Spark の使用](hdinsight-apache-spark-eventhub-streaming.md)
* [Website log analysis using Spark in HDInsight (HDInsight での Spark を使用した Web サイト ログ分析)](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>アプリケーションの作成と実行
* [Scala を使用してスタンドアロン アプリケーションを作成する](hdinsight-apache-spark-create-standalone-application.md)
* [Livy を使用して Spark クラスターでジョブをリモートで実行する](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>ツールと拡張機能
* [IntelliJ IDEA 用の HDInsight Tools プラグインを使用して Spark Scala アプリケーションを作成し、送信する](hdinsight-apache-spark-intellij-tool-plugin.md)
* [IntelliJ IDEA 用の HDInsight Tools プラグインを使用して Spark アプリケーションをリモートでデバッグする](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [HDInsight の Spark クラスターで Zeppelin Notebook を使用する](hdinsight-apache-spark-use-zeppelin-notebook.md)
* [HDInsight 用の Spark クラスターの Jupyter Notebook で使用可能なカーネル](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Jupyter Notebook で外部のパッケージを使用する](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter をコンピューターにインストールして HDInsight Spark クラスターに接続する](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>リソースの管理
* [Azure HDInsight での Apache Spark クラスターのリソースの管理](hdinsight-apache-spark-resource-manager.md)
* [HDInsight の Apache Spark クラスターで実行されるジョブの追跡とデバッグ](hdinsight-apache-spark-job-debugging.md)

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md

