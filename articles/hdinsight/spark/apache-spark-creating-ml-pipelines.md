---
title: Apache Spark Machine Learning パイプラインを作成する - Azure HDInsight
description: データ パイプラインを作成するには、Apache Spark Machine Learning ライブラリを使用します。
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/22/2019
ms.openlocfilehash: 4ad68ef33eb469c7949c3f3efcd55d6765da4158
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68441878"
---
# <a name="create-an-apache-spark-machine-learning-pipeline"></a>Apache Spark 機械学習パイプラインを作成する

Apache Spark のスケーラブルな Machine Learning ライブラリ (MLlib) は、分散環境にモデリング機能を提供します。 Spark パッケージ [`spark.ml`](https://spark.apache.org/docs/latest/ml-pipeline.html) は、DataFrames 上に構築された一連の高レベルの API です。 これらの API は、実際的な Machine Learning パイプラインの作成および調整に役立ちます。  *Spark Machine Learning* は、古い RDD ベースのパイプライン API ではなく、この MLlib DataFrame ベースの API を参照します。

Machine Learning (ML) パイプラインは、複数の Machine Learning アルゴリズムを 1 つに結合した完全なワークフローです。 データを処理したり、データから学習したりするには多くの手順を要する場合があるため、一連のアルゴリズムが必要になります。 パイプラインは、Machine Learning プロセスの各ステージと順序を定義します。 MLlib では、パイプラインのステージはトランスフォーマーとエスティメーターがそれぞれタスクを実行する、特定の一連のパイプラインステージで表されます。

トランスフォーマーは、`transform()` メソッドを使用してあるデータフレームを別のデータフレームに変換するアルゴリズムです。 たとえば、特徴トランスフォーマーは 1 列のデータフレームを読み取り、それを別の列にマッピングし、マッピングされた列が追加された新しいデータフレームを出力する可能性があります。

エスティメーターは学習アルゴリズムの抽象化であり、トランスフォーマーを生成するためのデータセット上の適合またはトレーニングに責任を負います。 エスティメーターは、データフレームを受け付けたり、データフレームを生成したりする、`fit()` という名前のメソッド (トランスフォーマー) を実装します。

トランスフォーマーまたはエスティメーターの各ステートレス インスタンスには、パラメーターを指定するときに使用される独自の一意識別子があります。 どちらも、これらのパラメーターを指定するための一様な API を使用します。

## <a name="pipeline-example"></a>パイプラインの例

この例では、ML パイプラインの実際の用途を示すために、HDInsight クラスター用の既定の (Azure Storage または Data Lake Storage のいずれかの) ストレージに事前に読み込まれて出荷されるサンプルの `HVAC.csv` データ ファイルを使用しています。 このファイルの内容を表示するには、`/HdiSamples/HdiSamples/SensorSampleData/hvac` ディレクトリに移動します。 `HVAC.csv` には、さまざまなビル内の HVAC (*暖房、換気、および空調*) システムのターゲット温度と実際の温度の両方を含む一連の時間が含まれています。 目標は、データ上のモデルをトレーニングし、特定のビルの予測温度を生成することです。

次のコードは、次の処理を実行します。

1. `LabeledDocument` を定義します。これは、`BuildingID`、`SystemInfo` (システムの識別子と経過時間)、および `label` (ビルが暑すぎる場合は 1.0、それ以外の場合は 0.0) を格納します。
2. 1 行のデータを取得し、ターゲット温度と実際の温度を比較することによってビルが "暑い" かどうかを判定するカスタム パーサー関数 `parseDocument` を作成します。
3. ソース データを抽出する場合は、このパーサーを適用します。
4. トレーニング データを作成します。

```python
from pyspark.ml import Pipeline
from pyspark.ml.classification import LogisticRegression
from pyspark.ml.feature import HashingTF, Tokenizer
from pyspark.sql import Row

# The data structure (column meanings) of the data array:
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
data = sc.textFile(
    "wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

documents = data.filter(lambda s: "Date" not in s).map(parseDocument)
training = documents.toDF()
```

このパイプラインの例には、`Tokenizer`、`HashingTF` (どちらもトランスフォーマー)、および `Logistic Regression` (エスティメーター) の 3 つのステージがあります。  `training` データフレーム内の抽出して解析されたデータは、`pipeline.fit(training)` が呼び出されたときにパイプラインを通して流れます。

1. 最初のステージ `Tokenizer` は、`SystemInfo` 入力列 (システム識別子と経過時間の値で構成される) を `words` 出力列に分割します。 この新しい `words` 列がデータフレームに追加されます。 
2. 2 番目のステージ `HashingTF` は、新しい `words` 列を特徴ベクトルに変換します。 この新しい `features` 列がデータフレームに追加されます。 これらの最初の 2 つのステージはトランスフォーマーです。 
3. 3 番目のステージ `LogisticRegression` はエスティメーターであるため、パイプラインは `LogisticRegression.fit()` メソッドを呼び出して `LogisticRegressionModel` を生成します。 

```python
tokenizer = Tokenizer(inputCol="SystemInfo", outputCol="words")
hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
lr = LogisticRegression(maxIter=10, regParam=0.01)

# Build the pipeline with our tokenizer, hashingTF, and logistic regression stages
pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])

model = pipeline.fit(training)
```

`Tokenizer` および `HashingTF` トランスフォーマーによって追加された新しい `words` および `features` 列や、`LogisticRegression` エスティメーターのサンプルを表示するには、元のデータフレーム上で `PipelineModel.transform()` メソッドを実行します。 運用コードでは、トレーニングを検証するテスト データフレームに合格するために次の手順が必要になります。

```python
peek = model.transform(training)
peek.show()

# Outputs the following:
+----------+----------+-----+--------+--------------------+--------------------+--------------------+----------+
|BuildingID|SystemInfo|label|   words|            features|       rawPrediction|         probability|prediction|
+----------+----------+-----+--------+--------------------+--------------------+--------------------+----------+
|         4|     13 20|  0.0|[13, 20]|(262144,[250802,2...|[0.11943986671420...|[0.52982451901740...|       0.0|
|        17|      3 20|  0.0| [3, 20]|(262144,[89074,25...|[0.17511205617446...|[0.54366648775222...|       0.0|
|        18|     17 20|  1.0|[17, 20]|(262144,[64358,25...|[0.14620993833623...|[0.53648750722548...|       0.0|
|        15|      2 23|  0.0| [2, 23]|(262144,[31351,21...|[-0.0361327091023...|[0.49096780538523...|       1.0|
|         3|      16 9|  1.0| [16, 9]|(262144,[153779,1...|[-0.0853679939336...|[0.47867095324139...|       1.0|
|         4|     13 28|  0.0|[13, 28]|(262144,[69821,25...|[0.14630166986618...|[0.53651031790592...|       0.0|
|         2|     12 24|  0.0|[12, 24]|(262144,[187043,2...|[-0.0509556393066...|[0.48726384581522...|       1.0|
|        16|     20 26|  1.0|[20, 26]|(262144,[128319,2...|[0.33829638728900...|[0.58377663577684...|       0.0|
|         9|      16 9|  1.0| [16, 9]|(262144,[153779,1...|[-0.0853679939336...|[0.47867095324139...|       1.0|
|        12|       6 5|  0.0|  [6, 5]|(262144,[18659,89...|[0.07513008136562...|[0.51877369045183...|       0.0|
|        15|     10 17|  1.0|[10, 17]|(262144,[64358,25...|[-0.0291988646553...|[0.49270080242078...|       1.0|
|         7|      2 11|  0.0| [2, 11]|(262144,[212053,2...|[0.03678030020834...|[0.50919403860812...|       0.0|
|        15|      14 2|  1.0| [14, 2]|(262144,[109681,2...|[0.06216423725633...|[0.51553605651806...|       0.0|
|         6|       3 2|  0.0|  [3, 2]|(262144,[89074,21...|[0.00565582077537...|[0.50141395142468...|       0.0|
|        20|     19 22|  0.0|[19, 22]|(262144,[139093,2...|[-0.0769288695989...|[0.48077726176073...|       1.0|
|         8|     19 11|  0.0|[19, 11]|(262144,[139093,2...|[0.04988910033929...|[0.51246968885151...|       0.0|
|         6|      15 7|  0.0| [15, 7]|(262144,[77099,20...|[0.14854929135994...|[0.53706918109610...|       0.0|
|        13|      12 5|  0.0| [12, 5]|(262144,[89689,25...|[-0.0519932532562...|[0.48700461408785...|       1.0|
|         4|      8 22|  0.0| [8, 22]|(262144,[98962,21...|[-0.0120753606650...|[0.49698119651572...|       1.0|
|         7|      17 5|  0.0| [17, 5]|(262144,[64358,89...|[-0.0721054054871...|[0.48198145477106...|       1.0|
+----------+----------+-----+--------+--------------------+--------------------+--------------------+----------+

only showing top 20 rows
```

これで、`model` オブジェクトを使用して予測を行うことができます。 この Machine Learning アプリケーションの完全なサンプルと、それを実行するための詳細な手順については、「[Build Apache Spark machine learning applications on Azure HDInsight (Azure HDInsight で Apache Spark Machine Learning アプリケーションを構築する)](apache-spark-ipython-notebook-machine-learning.md)」を参照してください。

## <a name="see-also"></a>関連項目

* [Scala および Azure 上の Apache Spark を使用したデータ サイエンス](../../machine-learning/team-data-science-process/scala-walkthrough.md)
