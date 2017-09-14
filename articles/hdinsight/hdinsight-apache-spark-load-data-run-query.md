---
title: "Azure HDInsight Spark クラスターに対して対話型クエリを実行する | Microsoft Docs"
description: "HDInsight で Apache Spark クラスターを作成する方法に関する HDInsight Spark のクイック スタート。"
keywords: "spark クイック スタート,対話型 spark,対話型クエリ,hdinsight spark,azure spark"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: nitinme
ms.translationtype: HT
ms.sourcegitcommit: bfd49ea68c597b109a2c6823b7a8115608fa26c3
ms.openlocfilehash: ada1c3d1482c68834dbbf5eabbd045a7e0c01f9f
ms.contentlocale: ja-jp
ms.lasthandoff: 07/25/2017

---
# <a name="run-interactive-queries-on-an-hdinsight-spark-cluster"></a>HDInsight Spark クラスターに対して対話型クエリを実行する

この記事では、Jupyter Notebook を使用して、Spark クラスターに対して対話型の Spark SQL クエリを実行します。 Jupyter Notebook は、コンソール ベースの対話型機能を Web に拡張するブラウザー ベースのアプリケーションです。 詳細については、「[Jupyter Notebook](http://jupyter-notebook.readthedocs.io/en/latest/notebook.html)」のページを参照してください。

このチュートリアルでは、Jupyter Notebook で **PySpark** カーネルを使用し、対話形式の Spark SQL クエリを実行します。 Jupyter Notebook では、HDInsight クラスター上で他に **PySpark3** と **Spark** の 2 つのカーネルもサポートしています。 これらのカーネルと **PySpark** を使用するメリットについては、[HDInsight での Apache Spark クラスターと Jupyter Notebook カーネルの使用](hdinsight-apache-spark-jupyter-notebook-kernels.md)に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件

* **Azure HDInsight Spark クラスター**。 手順については、[Azure HDInsight での Apache Spark クラスターの作成](hdinsight-apache-spark-jupyter-spark-sql.md)に関するページを参照してください。

## <a name="create-a-jupyter-notebook-to-run-interactive-queries"></a>対話型のクエリを実行するために Jupyter Notebook を作成する

クエリを実行するには、クラスターに関連付けられている記憶域に既定である、サンプル データを使用します。 ただし、最初に Spark にデータフレームとしてそのデータを読み込む必要があります。 データフレームを作成したら、それで Jupyter Notebook を使用してクエリを実行できます。 このセクションでは、次の実行方法を確認します。

* サンプル データ セットを Spark データフレームとして登録します。
* データフレームでクエリを実行します。

1. [Azure Portal](https://portal.azure.com/)を開きます。 クラスターをダッシュボードにピン留めしている場合は、ダッシュボードから目的のクラスターのタイルをクリックしてクラスター ブレードを起動します。

    クラスターをダッシュボードにピン留めしていない場合は、左側のウィンドウで **[HDInsight クラスター]** をクリックし、作成したクラスターをクリックします。

3. **クイック リンク**で **[クラスター ダッシュボード]** をクリックし、**[Jupyter Notebook]** をクリックします。 入力を求められたら、クラスターの管理者資格情報を入力します。

   ![Jupyter Notebook を開いて対話型の Spark SQL クエリを実行する](./media/hdinsight-apache-spark-load-data-run-query/hdinsight-spark-start-jupyter-interactive-spark-sql-query.png "Jupyter Notebook を開いて対話型の Spark SQL クエリを実行する")

   > [!NOTE]
   > ブラウザーで次の URL を開くことで、クラスターの Jupyter Notebook にアクセスすることもできます。 **CLUSTERNAME** をクラスターの名前に置き換えます。
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   >
   >
3. Notebook を作成します。 **[新規]** をクリックし、**[PySpark]** をクリックします。

   ![対話型の Spark SQL クエリを実行する Jupyter Notebook を作成する](./media/hdinsight-apache-spark-load-data-run-query/hdinsight-spark-create-jupyter-interactive-Spark-SQL-query.png "対話型の Spark SQL クエリを実行する Jupyter Notebook を作成する")

   Untitled(Untitled.pynb) という名前の新しい Notebook が作成されて開かれます。

4. 上部の Notebook 名をクリックし、目的のわかりやすい名前を入力します。

    ![対話型の Spark クエリを実行する Jupter Notebook に名前を指定する](./media/hdinsight-apache-spark-load-data-run-query/hdinsight-spark-jupyter-notebook-name.png "対話型の Spark クエリを実行する Jupter Notebook に名前を指定する")

5. 次のコードを空のセルに貼り付け、**Shift + Enter** キーを押してコードを実行します。 このコードにより、このシナリオに必要な種類がインポートされます。

        from pyspark.sql.types import *

    PySpark カーネルを使用して Notebook を作成したため、コンテキストを明示的に作成する必要はありません。 最初のコード セルを実行すると、Spark コンテキストと Hive コンテキストが自動的に作成されます。

    ![対話型の Spark SQL クエリの状態](./media/hdinsight-apache-spark-load-data-run-query/hdinsight-spark-interactive-spark-query-status.png "対話型の Spark SQL クエリの状態")

    Jupyter で対話型のクエリを実行するたびに、Web ブラウザー ウィンドウのタイトルに **[(ビジー)]** ステータスと Notebook のタイトルが表示されます。 また、右上隅にある **PySpark** というテキストの横に塗りつぶされた円も表示されます。 ジョブが完了すると、白抜きの円に変化します。

6. Spark クラスターにデータを読み込む前に、そのスナップショットを確認しましょう。 このチュートリアルで使用するサンプル データは、すべての HDInsight Spark クラスター (**\HdiSamples\HdiSamples\SensorSampleData\hvac\hvac.csv**) に CSV ファイルとしてあります。 このデータは、ビルの温度の変化をキャプチャしています。 データの最初のいくつかの行は次のとおりです。

    ![対話型 Spark SQL クエリのデータのスナップショット](./media/hdinsight-apache-spark-load-data-run-query/hdinsight-spark-sample-data-interactive-spark-sql-query.png "対話型 Spark SQL クエリのデータのスナップショット")

6. 次のコードを実行してデータフレームと一時テーブル (**hvac**) を作成します。 生の CSV データの列と比較して、このチュートリアルでは、一時テーブルにすべての列を作成しません。 

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

7. テーブルを作成したら、次のコードを使用し、そのデータに対して対話型のクエリを実行します。

        %%sql
        SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"

   PySpark カーネルを使用しているため、`%%sql` マジックを使用して、作成した一時テーブル **hvac** に対して対話型の SQL クエリを直接実行できます。 `%%sql` マジックの詳細と、PySpark カーネルで使用できるその他のマジックの詳細については、[Spark HDInsight クラスターと Jupyter Notebook で使用可能なカーネル](hdinsight-apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic)に関する記事を参照してください。

   次の表形式の出力が既定で表示されます。

     ![対話型の Spark クエリの表形式の出力](./media/hdinsight-apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result.png "対話型の Spark クエリの表形式の出力")

    他の視覚化でも結果を表示できます。 たとえば、ある出力の領域グラフは次のようになります。

    ![対話型の Spark クエリ結果の領域グラフ](./media/hdinsight-apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result-area-chart.png "対話型の Spark クエリ結果の領域グラフ")

9. アプリケーションの実行後は、Notebook をシャットダウンしてクラスター リソースを解放します。 そのためには、Notebook の **[ファイル]** メニューの **[Close and Halt]** (閉じて停止) をクリックします。

## <a name="next-step"></a>次のステップ

この記事では、Jupyter Notebook を使用して Spark で対話型のクエリを実行する方法について学習しました。 次の記事に進んで、Spark に登録したデータを Power BI や Tableau などの BI 分析ツールに取り込む方法を確認してください。 

> [!div class="nextstepaction"]
>[Azure HDInsight でデータ視覚化ツールを使用する Spark BI](hdinsight-apache-spark-use-bi-tools.md)





