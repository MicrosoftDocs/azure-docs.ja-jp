---
title: "Apache Spark を使用した Azure HDInsight での Machine Learning アプリケーションの作成 | Microsoft Docs"
description: "Apache Spark で Notebook を使用して Machine Learning アプリケーションを作成する手順を説明します"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: f584ca5e-abee-4b7c-ae58-2e45dfc56bf4
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: a939a0845d7577185ff32edd542bcb2082543a26
ms.openlocfilehash: 94c7aca175543b94742ad57af6949b3fcdda6356
ms.lasthandoff: 01/24/2017


---
# <a name="build-machine-learning-applications-to-run-on-apache-spark-clusters-on-hdinsight"></a>HDInsight の Apache Spark クラスターで実行する Machine Learning アプリケーションの構築

HDInsight の Apache Spark クラスターを使用して Machine Learning アプリケーションを作成する方法を説明します。 この記事では、クラスターで使用できる Jupyter Notebook を使用してアプリケーションを作成およびテストする方法を説明します。 このアプリケーションでは、すべてのクラスターにおいて既定で利用可能なサンプル HVAC.csv データを使用します。

**前提条件:**

次のものが必要です。

* Azure サブスクリプション。 [Azure 無料試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。
* HDInsight での Apache Spark クラスター。 手順については、 [Azure HDInsight での Apache Spark クラスターの作成](hdinsight-apache-spark-jupyter-spark-sql.md)に関するページを参照してください。 

## <a name="data"></a>データ
アプリケーションの作成を始める前に、データの構造およびデータに対して実行する分析の種類を説明します。 

この記事では、HDInsight クラスターに関連付けた Azure Storage アカウントで使用できる、サンプルの **HVAC.csv** データ ファイルを使用します。 このファイルは、ストレージ アカウントの **\HdiSamples\HdiSamples\SensorSampleData\hvac** にあります。 CSV ファイルをダウンロードして開き、データのスナップショットを取得します。  

![HVAC データのスナップショット](./media/hdinsight-apache-spark-ipython-notebook-machine-learning/hdispark.ml.show.data.png "HVAC データのスナップショット")

データは、HVAC システムがインストールされているビルの目標温度と実際の温度を示します。 **[System]** 列はシステム ID を表し、**[SystemAge]** 列は HVAC システムがビルに設置されてからの年数を表します。

このデータを使用し、システム ID とシステム使用年数から得られる目標温度を基にしてビルが暑すぎるか寒すぎるかを予測します。

## <a name="app"></a>Spark MLlib を使用した Machine Learning アプリケーションの作成
このアプリケーションでは、Spark ML パイプラインを使用して、ドキュメントの分類を実行します。 パイプラインでは、ドキュメントを単語に分割し、単語を数値特徴ベクトルに変換して、最後に特徴ベクトルとラベルを使用して予測モデルを作成します。 アプリケーションを作成するには、次の手順を実行します。

1. [Azure ポータル](https://portal.azure.com/)のスタート画面で Spark クラスターのタイルをクリックします (スタート画面にピン留めしている場合)。 **[すべて参照]** > **[HDInsight クラスター]** でクラスターに移動することもできます。   
2. Spark クラスター ブレードから **[クラスター ダッシュボード]** をクリックし、**[Jupyter Notebook]** をクリックします。 入力を求められたら、クラスターの管理者資格情報を入力します。
   
   > [!NOTE]
   > ブラウザーで次の URL を開き、クラスターの Jupyter Notebook にアクセスすることもできます。 **CLUSTERNAME** をクラスターの名前に置き換えます。
   > 
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   > 
   > 
3. 新しい Notebook を作成します。 **[新規]** をクリックし、**[PySpark]** をクリックします。
   
    ![新しい Jupyter Notebook の作成](./media/hdinsight-apache-spark-ipython-notebook-machine-learning/hdispark.note.jupyter.createnotebook.png "新しい Jupyter Notebook の作成")
4. Untitled.pynb という名前の新しい Notebook が作成されて開かれます。 上部の Notebook 名をクリックし、わかりやすい名前を入力します。
   
    ![Notebook の名前を指定](./media/hdinsight-apache-spark-ipython-notebook-machine-learning/hdispark.note.jupyter.notebook.name.png "Notebook の名前を指定")
5. PySpark カーネルを使用して Notebook を作成したため、コンテキストを明示的に作成する必要はありません。 最初のコード セルを実行すると、Spark および Hive コンテキストが自動的に作成されます。 このシナリオに必要な種類をインポートすることから始めることができます。 次のスニペットを空のセルに貼り付けて、 **Shift + Enter**キーを押します。 
   
        from pyspark.ml import Pipeline
        from pyspark.ml.classification import LogisticRegression
        from pyspark.ml.feature import HashingTF, Tokenizer
        from pyspark.sql import Row
   
        import os
        import sys
        from pyspark.sql.types import *
   
        from pyspark.mllib.classification import LogisticRegressionWithSGD
        from pyspark.mllib.regression import LabeledPoint
        from numpy import array
6. ここで、データ (hvac.csv) を読み込み、解析し、それを使用してモデルをトレーニングする必要があります。 そのためには、ビルの実際の温度が目標温度より高いかどうかを確認する関数を定義します。 実際の温度の方が高い場合、ビルは暑く、値 **1.0**で示されます。 実際の温度の方が低い場合、ビルは寒く、値 **0.0**で示されます。 
   
    次のスニペットを空のセルに貼り付けて、 **Shift + Enter**キーを押します。

        # List the structure of data for better understanding. Because the data will be
        # loaded as an array, this structure makes it easy to understand what each element
        # in the array corresponds to

        # 0 Date
        # 1 Time
        # 2 TargetTemp
        # 3 ActualTemp
        # 4 System
        # 5 SystemAge
        # 6 BuildingID

        LabeledDocument = Row("BuildingID", "SystemInfo", "label")

        # Define a function that parses the raw CSV file and returns an object of type LabeledDocument

        def parseDocument(line):
            values = [str(x) for x in line.split(',')]
            if (values[3] > values[2]):
                hot = 1.0
            else:
                hot = 0.0        

            textValue = str(values[4]) + " " + str(values[5])

            return LabeledDocument((values[6]), textValue, hot)

        # Load the raw HVAC.csv file, parse it using the function
        data = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

        documents = data.filter(lambda s: "Date" not in s).map(parseDocument)
        training = documents.toDF()


1. トークナイザー、hashingTF、lr という&3; つのステージで構成される Spark 機械学習パイプラインを構成します。 パイプラインの概要と機能について詳しくは、<a href="http://spark.apache.org/docs/latest/ml-guide.html#how-it-works" target="_blank">Spark 機械学習に関するページ</a>をご覧ください。
   
    次のスニペットを空のセルに貼り付けて、 **Shift + Enter**キーを押します。
   
        tokenizer = Tokenizer(inputCol="SystemInfo", outputCol="words")
        hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
        lr = LogisticRegression(maxIter=10, regParam=0.01)
        pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])
2. パイプラインをトレーニング ドキュメントに適合させます。 次のスニペットを空のセルに貼り付けて、 **Shift + Enter**キーを押します。
   
        model = pipeline.fit(training)
3. トレーニング ドキュメントを検証してアプリケーションでの進行状況をチェックポイントします。 次のスニペットを空のセルに貼り付けて、 **Shift + Enter**キーを押します。
   
        training.show()
   
    出力は次のようになります。
   
        +----------+----------+-----+
        |BuildingID|SystemInfo|label|
        +----------+----------+-----+
        |         4|     13 20|  0.0|
        |        17|      3 20|  0.0|
        |        18|     17 20|  1.0|
        |        15|      2 23|  0.0|
        |         3|      16 9|  1.0|
        |         4|     13 28|  0.0|
        |         2|     12 24|  0.0|
        |        16|     20 26|  1.0|
        |         9|      16 9|  1.0|
        |        12|       6 5|  0.0|
        |        15|     10 17|  1.0|
        |         7|      2 11|  0.0|
        |        15|      14 2|  1.0|
        |         6|       3 2|  0.0|
        |        20|     19 22|  0.0|
        |         8|     19 11|  0.0|
        |         6|      15 7|  0.0|
        |        13|      12 5|  0.0|
        |         4|      8 22|  0.0|
        |         7|      17 5|  0.0|
        +----------+----------+-----+

    戻り、生の CSV ファイルに対して出力を確認します。 たとえば、CSV ファイルの最初の行のデータは次のとおりです。

    ![HVAC データのスナップショット](./media/hdinsight-apache-spark-ipython-notebook-machine-learning/hdispark.ml.show.data.first.row.png "HVAC データのスナップショット")

    実際の温度は目標温度より低く、ビルが寒いことを示します。 そのため、トレーニングの出力では、最初の行の **label** の値は **0.0** であり、ビルが暑くないことを意味します。

1. トレーニング済みのモデルを実行するようにデータ セットを準備します。 そのためには、システム ID とシステム経過年数 (トレーニング出力では **SystemInfo** として示されます) を渡し、モデルはそのシステム ID とシステム経過年数のビルが暑すぎるか (1.0 で示されます) または寒すぎるか (0.0 で示されます) を予測します。
   
   次のスニペットを空のセルに貼り付けて、 **Shift + Enter**キーを押します。
   
       # SystemInfo here is a combination of system ID followed by system age
       Document = Row("id", "SystemInfo")
       test = sc.parallelize([(1L, "20 25"),
                     (2L, "4 15"),
                     (3L, "16 9"),
                     (4L, "9 22"),
                     (5L, "17 10"),
                     (6L, "7 22")]) \
           .map(lambda x: Document(*x)).toDF() 
2. 最後に、テスト データで予測を行います。 次のスニペットを空のセルに貼り付けて、 **Shift + Enter**キーを押します。
   
        # Make predictions on test documents and print columns of interest
        prediction = model.transform(test)
        selected = prediction.select("SystemInfo", "prediction", "probability")
        for row in selected.collect():
            print row
3. 次のような出力が表示されます。
   
       Row(SystemInfo=u'20 25', prediction=1.0, probability=DenseVector([0.4999, 0.5001]))
       Row(SystemInfo=u'4 15', prediction=0.0, probability=DenseVector([0.5016, 0.4984]))
       Row(SystemInfo=u'16 9', prediction=1.0, probability=DenseVector([0.4785, 0.5215]))
       Row(SystemInfo=u'9 22', prediction=1.0, probability=DenseVector([0.4549, 0.5451]))
       Row(SystemInfo=u'17 10', prediction=1.0, probability=DenseVector([0.4925, 0.5075]))
       Row(SystemInfo=u'7 22', prediction=0.0, probability=DenseVector([0.5015, 0.4985]))
   
   予測の 1 行目からは、ID が 20 でシステム経過年数が 25 年の HVAC システムではビルが暑い (**prediction=1.0**) ことがわかります。 DenseVector の 1 番目の値 (0.49999) は予測 0.0 に対応し、2 番目の値 (0.5001) は予測 1.0 に対応します。 出力では、2 番目の値はわずかに高いだけですが、モデルは **prediction=1.0**を示します。
4. アプリケーションの実行が完了したら、Notebook をシャットダウンしてリソースを解放する必要があります。 そのためには、Notebook の **[ファイル]** メニューの **[Close and Halt]** (閉じて停止) をクリックします。 これにより、Notebook がシャットダウンされ、閉じられます。

## <a name="anaconda"></a>Machine Learning への Anaconda scikit-learn ライブラリの使用
HDInsight の Apache Spark クラスターには、Anaconda ライブラリが含まれます。 これには、機械学習用の **scikit-learn** ライブラリも含まれます。 ライブラリには、Jupyter Notebook からサンプル アプリケーションを直接作成するために使用できるさまざまなデータ セットも含まれます。 scikit-learn ライブラリの使用例については、[http://scikit-learn.org/stable/auto_examples/index.html](http://scikit-learn.org/stable/auto_examples/index.html) をご覧ください。

## <a name="seealso"></a>関連項目
* [概要: Azure HDInsight での Apache Spark](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>シナリオ
* [Spark と BI: HDInsight で BI ツールと Spark を使用した対話型データ分析の実行](hdinsight-apache-spark-use-bi-tools.md)
* [Spark と Machine Learning: HDInsight で Spark を使用して食品の検査結果を予測する](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Spark ストリーミング: リアルタイム ストリーミング アプリケーションを作成するための HDInsight での Spark の使用](hdinsight-apache-spark-eventhub-streaming.md)
* [Website log analysis using Spark in HDInsight (HDInsight での Spark を使用した Web サイト ログ分析)](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>アプリケーションの作成と実行
* [Scala を使用してスタンドアロン アプリケーションを作成する](hdinsight-apache-spark-create-standalone-application.md)
* [Livy を使用して Spark クラスターでジョブをリモートで実行する](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>ツールと拡張機能
* [Use HDInsight Tools Plugin for IntelliJ IDEA to create and submit Spark Scala applicatons (Linux)](hdinsight-apache-spark-intellij-tool-plugin.md)
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

[hdinsight-weblogs-sample]: hdinsight-hive-analyze-website-log.md
[hdinsight-sensor-data-sample]: hdinsight-hive-analyze-sensor-data.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md

