---
title: チュートリアル:Azure HDInsight で Spark Machine Learning アプリケーションを作成する
description: Apache Spark Machine Learning アプリケーションを HDInsight Spark クラスター内に Jupyter Notebook を使用して作成するための詳細な手順。
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.custom: hdinsightactive,mvc
ms.topic: tutorial
ms.date: 11/06/2018
ms.author: hrasheed
ms.openlocfilehash: 54ef9ac72ffeebbf228a85768f6c2eb4b64862c9
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2019
ms.locfileid: "57433401"
---
# <a name="tutorial-build-an-apache-spark-machine-learning-application-in-hdinsight"></a>チュートリアル:HDInsight で Apache Spark 機械学習アプリケーションを作成する 

このチュートリアルでは、[Jupyter Notebook](https://jupyter.org/) を使用して、Azure HDInsight 用の [Apache Spark](https://spark.apache.org/) 機械学習アプリケーションを作成する方法について説明します。 

[MLlib](https://spark.apache.org/docs/1.1.0/mllib-guide.html) は、分類、回帰、クラスタリング、協調フィルタリング、次元縮小、基になっている最適化プリミティブなど、一般的な学習アルゴリズムとユーティリティで構成された Spark のスケーラブル Machine Learning ライブラリです。

このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
> * Apache Spark 機械学習アプリケーションを開発する

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件:

次の項目が必要です。

* 「[Azure HDInsight での Apache Spark クラスターの作成](apache-spark-jupyter-spark-sql.md)」を完了します。

## <a name="understand-the-data-set"></a>データ セットを理解する

このアプリケーションでは、すべてのクラスターにおいて既定で利用可能なサンプル HVAC.csv データを使用します。 このファイルは、**\HdiSamples\HdiSamples\SensorSampleData\hvac** にあります。 データは、HVAC (Heating, Ventilating, Air Conditioning: 冷暖房空調設備) システムがインストールされているいくつかのビルの目標温度と実際の温度を示します。 **[System]** 列はシステム ID を表し、**[SystemAge]** 列は HVAC システムがビルに設置されてからの年数を表します。 データを使用して、システム ID とシステム使用年数から得られる目標温度を基にしてビルが暑すぎるか寒すぎるかを予測できます。

![Spark Machine Learning サンプルで使用されるデータのスナップショット](./media/apache-spark-ipython-notebook-machine-learning/spark-machine-learning-understand-data.png "Spark Machine Learning サンプルで使用されるデータのスナップショット")

## <a name="develop-a-spark-machine-learning-application-using-spark-mllib"></a>Spark MLlib を使用した Spark Machine Learning アプリケーションの開発

このアプリケーションでは、Spark [ML パイプライン](https://spark.apache.org/docs/2.2.0/ml-pipeline.html)を使用して、ドキュメントの分類を実行します。 ML パイプラインでは、実用的な機械学習パイプラインの作成および調整に役立つデータ フレームを基盤とする、統一された高レベルの API 一式が提供されます。 パイプラインでは、ドキュメントを単語に分割し、単語を数値特徴ベクトルに変換して、最後に特徴ベクトルとラベルを使用して予測モデルを作成します。 アプリケーションを作成するには、次の手順を実行します。

1. PySpark カーネルを使用して Jupyter Notebook を作成します。 手順については、[Jupyter Notebook の作成](./apache-spark-jupyter-spark-sql.md#create-a-jupyter-notebook)に関するページをご覧ください。
2. このシナリオに必要な型をインポートします。 次のスニペットを空のセルに貼り付けて、 **Shift + Enter**キーを押します。 

    ```PySpark
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
    ```
3. データ (hvac.csv) を読み込み、解析し、それを使用してモデルをトレーニングします。 

    ```PySpark
    # Define a type called LabelDocument
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
    data = sc.textFile("/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

    documents = data.filter(lambda s: "Date" not in s).map(parseDocument)
    training = documents.toDF()
    ```

    このコード スニペットでは、実際の温度と目標温度とを比較する関数を定義します。 実際の温度の方が高い場合、ビルは暑く、値 **1.0**で示されます。 それ以外の場合、ビルは寒く、値 **0.0** で示されます。 

4. トークナイザー、hashingTF、lr という 3 つのステージで構成される Spark 機械学習パイプラインを構成します。 

    ```PySpark
    tokenizer = Tokenizer(inputCol="SystemInfo", outputCol="words")
    hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
    lr = LogisticRegression(maxIter=10, regParam=0.01)
    pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])
    ```

    パイプラインとそのしくみの詳細については、<a href="https://spark.apache.org/docs/latest/ml-guide.html#how-it-works" target="_blank">Apache Spark 機械学習パイプライン</a>に関するページを参照してください。

5. パイプラインをトレーニング ドキュメントに適合させます。
   
    ```PySpark
    model = pipeline.fit(training)
    ```

6. トレーニング ドキュメントを検証してアプリケーションでの進行状況をチェックポイントします。
   
    ```PySpark
    training.show()
    ```
   
    次のように出力されます。

    ```
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
    ```

    生の CSV ファイルと照らして出力を比較します。 たとえば、CSV ファイルの最初の行のデータは次のとおりです。

    ![Spark Machine Learning サンプルの出力データ スナップショット](./media/apache-spark-ipython-notebook-machine-learning/spark-machine-learning-output-data.png "Spark Machine Learning サンプルの出力データ スナップショット")

    実際の温度は目標温度より低く、ビルが寒いことを示します。 そのため、トレーニングの出力では、最初の行の **label** の値は **0.0** であり、ビルが暑くないことを意味します。

7. トレーニング済みのモデルを実行するようにデータ セットを準備します。 そのためには、システム ID とシステム経過年数 (トレーニング出力では **SystemInfo** として示されます) を渡し、モデルはそのシステム ID とシステム経過年数のビルが暑すぎるか (1.0 で示されます) または寒すぎるか (0.0 で示されます) を予測します。
   
    ```PySpark   
    # SystemInfo here is a combination of system ID followed by system age
    Document = Row("id", "SystemInfo")
    test = sc.parallelize([(1L, "20 25"),
                    (2L, "4 15"),
                    (3L, "16 9"),
                    (4L, "9 22"),
                    (5L, "17 10"),
                    (6L, "7 22")]) \
        .map(lambda x: Document(*x)).toDF() 
    ```
8. 最後に、テスト データで予測を行います。 
   
    ```PySpark
    # Make predictions on test documents and print columns of interest
    prediction = model.transform(test)
    selected = prediction.select("SystemInfo", "prediction", "probability")
    for row in selected.collect():
        print row
    ```

    次のように出力されます。

    ```   
    Row(SystemInfo=u'20 25', prediction=1.0, probability=DenseVector([0.4999, 0.5001]))
    Row(SystemInfo=u'4 15', prediction=0.0, probability=DenseVector([0.5016, 0.4984]))
    Row(SystemInfo=u'16 9', prediction=1.0, probability=DenseVector([0.4785, 0.5215]))
    Row(SystemInfo=u'9 22', prediction=1.0, probability=DenseVector([0.4549, 0.5451]))
    Row(SystemInfo=u'17 10', prediction=1.0, probability=DenseVector([0.4925, 0.5075]))
    Row(SystemInfo=u'7 22', prediction=0.0, probability=DenseVector([0.5015, 0.4985]))
    ```
   
   予測の 1 行目からは、ID が 20 でシステム経過年数が 25 年の HVAC システムではビルが暑い (**prediction=1.0**) ことがわかります。 DenseVector の 1 番目の値 (0.49999) は予測 0.0 に対応し、2 番目の値 (0.5001) は予測 1.0 に対応します。 出力では、2 番目の値はわずかに高いだけですが、モデルは **prediction=1.0**を示します。
10. Notebook をシャットダウンしてリソースを解放します。 そのためには、Notebook の **[ファイル]** メニューの **[Close and Halt]** (閉じて停止) をクリックします。 このアクションにより Notebook がシャットダウンされ、Notebook が閉じます。

## <a name="use-anaconda-scikit-learn-library-for-spark-machine-learning"></a>Spark Machine Learning での Anaconda scikit-learn ライブラリの使用
HDInsight の Apache Spark クラスターには、Anaconda ライブラリが含まれます。 これには、機械学習用の **scikit-learn** ライブラリも含まれます。 ライブラリには、Jupyter Notebook からサンプル アプリケーションを直接作成するために使用できるさまざまなデータ セットも含まれます。 scikit-learn ライブラリの使用例については、「[ https://scikit-learn.org/stable/auto_examples/index.html](https://scikit-learn.org/stable/auto_examples/index.html)」を参照してください。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

* Apache Spark 機械学習アプリケーションを開発する

次のチュートリアルに進み、Spark ジョブに IntelliJ IDEA を使用する方法を学習してください。 

> [!div class="nextstepaction"]
> [IntelliJ を使用した Scala Maven アプリケーションの作成](./apache-spark-create-standalone-application.md)

