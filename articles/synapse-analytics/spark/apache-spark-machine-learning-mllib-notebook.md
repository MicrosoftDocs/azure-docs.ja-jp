---
title: チュートリアル:Apache Spark MLlib で機械学習アプリをビルドする
description: Apache Spark MLlib を使用して、ロジスティック回帰による分類を使ってデータセットを分析する機械学習アプリを作成する方法に関するチュートリアル。
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.reviewer: jrasnick
ms.topic: tutorial
ms.subservice: machine-learning
ms.date: 04/15/2020
ms.author: euang
ms.openlocfilehash: 5caa41b852bf55a11489db6c0bab871b20720e05
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101670660"
---
# <a name="tutorial-build-a-machine-learning-app-with-apache-spark-mllib-and-azure-synapse-analytics"></a>チュートリアル:Apache Spark MLlib と Azure Synapse Analytics を使用して機械学習アプリを構築する

この記事では、Apache Spark [MLlib](https://spark.apache.org/mllib/) を使用し、Azure のオープン データセットに対してシンプルな予測分析を実行する機械学習アプリケーションの作成方法について説明します。 Spark には、組み込みの機械学習ライブラリが用意されています。 この例では、ロジスティック回帰による *分類* を使用しています。

SparkML と MLlib は、機械学習タスクに役立つ多数のユーティリティを提供するコア Spark ライブラリです。これには、次のことに適したユーティリティが含まれます。

- 分類
- 回帰
- クラスタリング
- トピックのモデリング
- 特異値分解 (SVD) と主成分分析 (PCA)
- 仮説テストとサンプル統計の計算

## <a name="understand-classification-and-logistic-regression"></a>分類およびロジスティック回帰について

一般的な Machine Learning タスクである *分類* は、入力データをカテゴリに分類するプロセスです。 ユーザーが指定した入力データに "*ラベル*" を割り当てる方法を決定するのは、分類アルゴリズムの仕事です。 たとえば、株式情報を入力として受け取り、株式を、売却する必要のある株式と保持する必要のある株式の 2 つのカテゴリに分類する機械学習アルゴリズムを考えてみます。

*ロジスティック回帰* は、分類に使用できるアルゴリズムです。 Spark のロジスティック回帰 API は、 *二項分類*(入力データを 2 つのグループのいずれかに分類する) に適しています。 ロジスティック回帰の詳細については、[Wikipedia](https://en.wikipedia.org/wiki/Logistic_regression) を参照してください。

要約すると、ロジスティック回帰のプロセスにより、入力ベクトルがどちらか 1 つのグループに属している確率を予測するために使用できる "*ロジスティック関数*" が生成されます。

## <a name="predictive-analysis-example-on-nyc-taxi-data"></a>NYC タクシー データの予測分析の例

この例では、Spark を使用して、ニューヨークからのタクシー乗車のチップ データに対して、いくつかの予測分析を実行します。 データは [Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) から入手できます。 このデータセットのサブセットには、イエロー タクシー乗車に関する情報が格納され、各乗車、出発時刻と到着時刻、場所、料金、およびその他の関心の高い属性に関する情報が含まれます。

> [!IMPORTANT]
> その保存場所からこのデータを取得するために、追加料金が発生する場合があります。

次の手順では、特定の乗車にチップが含まれているかどうかを予測するモデルを作成します。

## <a name="create-an-apache-spark-machine-learning-model"></a>Apache Spark 機械学習モデルを作成する

1. PySpark カーネルを使用してノートブックを作成します。 手順については、「[ノートブックを作成する](../quickstart-apache-spark-notebook.md#create-a-notebook)」を参照してください。
2. このアプリケーションに必要な型をインポートします。 次のコードをコピーして空のセルに貼り付け、Shift + Enter キーを押します。 または、コードの左側にある青い再生アイコンを使用して、セルを実行します。

    ```python
    import matplotlib.pyplot as plt
    from datetime import datetime
    from dateutil import parser
    from pyspark.sql.functions import unix_timestamp, date_format, col, when
    from pyspark.ml import Pipeline
    from pyspark.ml import PipelineModel
    from pyspark.ml.feature import RFormula
    from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorIndexer
    from pyspark.ml.classification import LogisticRegression
    from pyspark.mllib.evaluation import BinaryClassificationMetrics
    from pyspark.ml.evaluation import BinaryClassificationEvaluator
    ```

    PySpark カーネルであるため、コンテキストを明示的に作成する必要はありません。 最初のコード セルを実行すると、Spark コンテキストが自動的に作成されます。

## <a name="construct-the-input-dataframe"></a>入力 DataFrame を作成する

生データは Parquet 形式であるため、Spark コンテキストを使用して、ファイルを DataFrame として、直接メモリにプルできます。 次のステップのコードでは既定のオプションを使用していますが、必要に応じて、データ型とその他のスキーマ属性のマッピングを強制的に行うこともできます。

1. 次の行を新しいセルに貼り付け、コードを実行して、Spark DataFrame を作成します。 このステップにより、Open Datasets API を介してデータが取得されます。 このデータをすべてプルすると、約 15 億行が生成されます。 

   サーバーレス Apache Spark プールのサイズによっては、生データが大きすぎるか、その操作に時間がかかりすぎる可能性があります。 このデータを、より小さいものにフィルター処理することができます。 次のコード例では、`start_date` と `end_date` を使用して、1 か月分のデータを返すフィルターを適用します。

    ```python
    from azureml.opendatasets import NycTlcYellow

    end_date = parser.parse('2018-06-06')
    start_date = parser.parse('2018-05-01')
    nyc_tlc = NycTlcYellow(start_date=start_date, end_date=end_date)
    filtered_df = nyc_tlc.to_spark_dataframe()
    ```

2. 単純なフィルター処理の欠点は、統計的観点から、データに偏りが発生する可能性があることです。 別の方法として、Spark に組み込まれているサンプリングを使用します。 

   次のコードでは、上記のコードの後に適用すると、データセットが約 2,000 行に減少します。 このサンプリング ステップは、単純なフィルターの代わりに使用することも、単純なフィルターと組み合わせて使用することもできます。

    ```python
    # To make development easier, faster, and less expensive, downsample for now
    sampled_taxi_df = filtered_df.sample(True, 0.001, seed=1234)
    ```

3. これで、データを調査して、読み取られた内容を確認できるようになりました。 通常は、データセットのサイズに応じて、完全なセットではなくサブセットでデータを確認することをお勧めします。 

   次のコードでは、データを表示する 2 つの方法を示しています。 最初の方法は、基本的なものです。 2 番目の方法は、はるかに機能豊富なグリッド エクスペリエンスに加えて、データをグラフィカルに視覚化する機能も提供します。

    ```python
    #sampled_taxi_df.show(5)
    display(sampled_taxi_df)
    ```

4. 生成されるデータセットのサイズと、多くの回数ノートブックを実験または実行する必要性に応じて、データセットをワークスペースにローカルにキャッシュすることが推奨される場合があります。 明示的なキャッシュを実行するには、次の 3 つの方法があります。

   - DataFrame をファイルとしてローカルに保存する。
   - DataFrame を一時テーブルまたはビューとして保存する。
   - DataFrame を永続的テーブルとして保存する。

これらの最初の 2 つの方法を、次のコード例に含めています。

一時テーブルまたはビューを作成すると、データへのさまざまなアクセス パスが提供されますが、Spark インスタンス セッションの継続期間のみ持続されます。

```Python
sampled_taxi_df.createOrReplaceTempView("nytaxi")
```

## <a name="prepare-the-data"></a>データを準備する

生の形式のデータは、多くの場合、モデルに直接渡すのに適していません。 データに対して一連のアクションを実行して、モデルでそれを使用できる状態にする必要があります。

次のコードでは、4 つのクラスの操作を実行します。

- フィルター処理による外れ値または誤った値の除去。
- 不要な列の除去。
- モデルをより効率的に機能させるための、生データから派生した新しい列の作成。 この操作は、特徴付けと呼ばれることもあります。
- ラベル付け。 二項分類 (特定の乗車でチップがあるかないか) に取り掛かるため、チップの金額を 0 または 1 の値に変換する必要があります。

```python
taxi_df = sampled_taxi_df.select('totalAmount', 'fareAmount', 'tipAmount', 'paymentType', 'rateCodeId', 'passengerCount'\
                                , 'tripDistance', 'tpepPickupDateTime', 'tpepDropoffDateTime'\
                                , date_format('tpepPickupDateTime', 'hh').alias('pickupHour')\
                                , date_format('tpepPickupDateTime', 'EEEE').alias('weekdayString')\
                                , (unix_timestamp(col('tpepDropoffDateTime')) - unix_timestamp(col('tpepPickupDateTime'))).alias('tripTimeSecs')\
                                , (when(col('tipAmount') > 0, 1).otherwise(0)).alias('tipped')
                                )\
                        .filter((sampled_taxi_df.passengerCount > 0) & (sampled_taxi_df.passengerCount < 8)\
                                & (sampled_taxi_df.tipAmount >= 0) & (sampled_taxi_df.tipAmount <= 25)\
                                & (sampled_taxi_df.fareAmount >= 1) & (sampled_taxi_df.fareAmount <= 250)\
                                & (sampled_taxi_df.tipAmount < sampled_taxi_df.fareAmount)\
                                & (sampled_taxi_df.tripDistance > 0) & (sampled_taxi_df.tripDistance <= 100)\
                                & (sampled_taxi_df.rateCodeId <= 5)
                                & (sampled_taxi_df.paymentType.isin({"1", "2"}))
                                )
```

その後に、データに対して 2 番目のパスを繰り返して、最終的な特徴を追加します。

```Python
taxi_featurised_df = taxi_df.select('totalAmount', 'fareAmount', 'tipAmount', 'paymentType', 'passengerCount'\
                                                , 'tripDistance', 'weekdayString', 'pickupHour','tripTimeSecs','tipped'\
                                                , when((taxi_df.pickupHour <= 6) | (taxi_df.pickupHour >= 20),"Night")\
                                                .when((taxi_df.pickupHour >= 7) & (taxi_df.pickupHour <= 10), "AMRush")\
                                                .when((taxi_df.pickupHour >= 11) & (taxi_df.pickupHour <= 15), "Afternoon")\
                                                .when((taxi_df.pickupHour >= 16) & (taxi_df.pickupHour <= 19), "PMRush")\
                                                .otherwise(0).alias('trafficTimeBins')
                                              )\
                                       .filter((taxi_df.tripTimeSecs >= 30) & (taxi_df.tripTimeSecs <= 7200))
```

## <a name="create-a-logistic-regression-model"></a>ロジスティック回帰モデルを作成する

最後のタスクは、ラベル付けされたデータをロジスティック回帰で分析できる形式に変換することです。 ロジスティック回帰アルゴリズムへの入力は、"*ラベルと特徴ベクトルのペア*" のセットである必要があります。ここで "*特徴ベクトル*" とは、入力ポイントを表す数のベクトルです。 

そのため、カテゴリ列を数値に変換する必要があります。 具体的には、`trafficTimeBins` と `weekdayString` 列を整数表現に変換する必要があります。 変換を実行する方法は複数あります。 次の例では、一般的な方法である `OneHotEncoder` を使用します。

```python
# Because the sample uses an algorithm that works only with numeric features, convert them so they can be consumed
sI1 = StringIndexer(inputCol="trafficTimeBins", outputCol="trafficTimeBinsIndex")
en1 = OneHotEncoder(dropLast=False, inputCol="trafficTimeBinsIndex", outputCol="trafficTimeBinsVec")
sI2 = StringIndexer(inputCol="weekdayString", outputCol="weekdayIndex")
en2 = OneHotEncoder(dropLast=False, inputCol="weekdayIndex", outputCol="weekdayVec")

# Create a new DataFrame that has had the encodings applied
encoded_final_df = Pipeline(stages=[sI1, en1, sI2, en2]).fit(taxi_featurised_df).transform(taxi_featurised_df)
```

このアクションにより、すべての列がモデルのトレーニングに適した形式になっている新しい DataFrame が得られます。

## <a name="train-a-logistic-regression-model"></a>ロジスティック回帰モデルのトレーニング

最初のタスクは、データセットをトレーニング セットとテスト セットまたは検証セットに分割することです。 ここでの分割は任意です。 さまざまな分割設定を試して、モデルに影響があるかどうかを確認してください。

```python
# Decide on the split between training and testing data from the DataFrame
trainingFraction = 0.7
testingFraction = (1-trainingFraction)
seed = 1234

# Split the DataFrame into test and training DataFrames
train_data_df, test_data_df = encoded_final_df.randomSplit([trainingFraction, testingFraction], seed=seed)
```

2 つの DataFrame が得られたところで、次のタスクは、モデル式を作成して、トレーニング DataFrame に対してそれを実行することです。 その後、テスト用 DataFrame に対して検証できます。 さまざまなバージョンのモデル式を試して、さまざまな組み合わせの影響を確認してください。

> [!Note]
> モデルを保存するためには、*Storage BLOB データ共同作成者* の Azure ロールが必要です。 お使いのストレージ アカウントで、 **[アクセス制御 (IAM)]** に移動し、 **[ロールの割り当ての追加]** を選択します。 Storage BLOB データ共同作成者ロールを Azure SQL Database サーバーに割り当てます。 所有者特権を持つメンバーのみが、この手順を実行できます。 
>
>さまざまな Azure の組み込みロールについては、[こちらのガイド](../../role-based-access-control/built-in-roles.md)を参照してください。

```python
## Create a new logistic regression object for the model
logReg = LogisticRegression(maxIter=10, regParam=0.3, labelCol = 'tipped')

## The formula for the model
classFormula = RFormula(formula="tipped ~ pickupHour + weekdayVec + passengerCount + tripTimeSecs + tripDistance + fareAmount + paymentType+ trafficTimeBinsVec")

## Undertake training and create a logistic regression model
lrModel = Pipeline(stages=[classFormula, logReg]).fit(train_data_df)

## Saving the model is optional, but it's another form of inter-session cache
datestamp = datetime.now().strftime('%m-%d-%Y-%s')
fileName = "lrModel_" + datestamp
logRegDirfilename = fileName
lrModel.save(logRegDirfilename)

## Predict tip 1/0 (yes/no) on the test dataset; evaluation using area under ROC
predictions = lrModel.transform(test_data_df)
predictionAndLabels = predictions.select("label","prediction").rdd
metrics = BinaryClassificationMetrics(predictionAndLabels)
print("Area under ROC = %s" % metrics.areaUnderROC)
```

このセルからの出力:

```shell
Area under ROC = 0.9779470729751403
```

## <a name="create-a-visual-representation-of-the-prediction"></a>予測を視覚化する

このテスト結果の理解に役立つ最終的なグラフを作成します。 [ROC 曲線](https://en.wikipedia.org/wiki/Receiver_operating_characteristic)は、結果を確認する 1 つの方法です。

```python
## Plot the ROC curve; no need for pandas, because this uses the modelSummary object
modelSummary = lrModel.stages[-1].summary

plt.plot([0, 1], [0, 1], 'r--')
plt.plot(modelSummary.roc.select('FPR').collect(),
         modelSummary.roc.select('TPR').collect())
plt.xlabel('False Positive Rate')
plt.ylabel('True Positive Rate')
plt.show()
```

![チップ モデルのロジスティック回帰の ROC 曲線を示すグラフ。](./media/apache-spark-machine-learning-mllib-notebook/nyc-taxi-roc.png)

## <a name="shut-down-the-spark-instance"></a>Spark インスタンスをシャットダウンする

アプリケーションの実行が完了したら、タブを閉じ、ノートブックをシャットダウンしてリソースを解放する必要があります。また、ノートブックの下部にある状態パネルから **[セッションの終了]** を選択します。

## <a name="see-also"></a>関連項目

- [概要:Azure Synapse Analytics での Apache Spark](apache-spark-overview.md)

## <a name="next-steps"></a>次のステップ

- [.NET for Apache Spark ドキュメント](/dotnet/spark)
- [Azure Synapse Analytics](../index.yml)
- [Apache Spark 公式ドキュメント](https://spark.apache.org/docs/2.4.5/)

>[!NOTE]
> Apache Spark の公式ドキュメントの一部では、Spark コンソールの使用を前提としていますが、これは Azure Synapse Analytics の Apache Spark では利用できません。 代わりに、[ノートブック](../quickstart-apache-spark-notebook.md)または [IntelliJ](../spark/intellij-tool-synapse.md) のエクスペリエンスを使用してください。