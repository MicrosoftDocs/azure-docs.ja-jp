---
title: Spark を使用した高度なデータ探索とモデリング - Team Data Science Process
description: HDInsight Spark を使用して、データ探索を実行し、二項分類モデルと回帰モデルのトレーニングを行います。クロス検証とハイパーパラメーターの最適化を使用しています。
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: b84b7387411e7df8e092a2e810591697319f9554
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104774679"
---
# <a name="advanced-data-exploration-and-modeling-with-spark"></a>Spark を使用した高度なデータ探索とモデリング

このチュートリアルでは、HDInsight Spark を使用して、データ探索を実行し、二項分類モデルと回帰モデルのトレーニングを行います。2013 年の NYC タクシーの乗車と料金に関するデータセットのサンプルに対し、クロス検証とハイパーパラメーターの最適化を使用しています。 チュートリアルでは、エンド ツー エンドの[データ サイエンス プロセス](./index.yml)の手順について説明します。処理には HDInsight Spark クラスターを使用し、Azure BLOB にデータとモデルを保存します。 プロセスでは、Azure Storage BLOB のデータを探索し、視覚化した後、予測モデルを構築するためのデータを準備します。 ソリューションのコーディングと関連するプロットの表示には、Python が使用されています。 これらのモデルは、二項分類および回帰モデリング タスクを実行する Spark MLlib キットを使用して構築されます。 

* **二項分類** タスクでは、乗車でチップが支払われるかどうかを予測します。 
* **回帰** タスクでは、チップの他の特徴に基づいてチップの金額を予測します。 

また、モデリング手順には、各種モデルをトレーニング、評価し、保存する方法を示すコードも含まれています。 このトピックでは、「[Spark を使用したデータ探索とモデリング](spark-data-exploration-modeling.md)」トピックと同じ領域の一部を取り上げますが、 正確な分類と回帰のモデルを最適にトレーニングするために、クロス検証とハイパーパラメーター スイープも使用するため、タイトルに "高度な" が付けられています。 

**クロス検証 (CV)** は、既知のデータセットでトレーニングされたモデルが、トレーニングに使用されていないデータセットの特徴の予測に対してどの程度汎用化されるかを評価する手法です。  この記事で使用する一般的な実装では、データセットを K 個のフォールドに分割し、1 つを除くすべてのフォールドを使用してラウンドロビン方式でモデルをトレーニングします。 トレーニングに使用されないフォールドの独立したデータセットに対するテストで、モデルの予測能力が評価されます。

**ハイパーパラメーターの最適化** とは、学習アルゴリズムでどのようなハイパーパラメーターのセットを選択するかを検討することで、一般的には、独立したデータセットに対するアルゴリズムのパフォーマンスの測定を最適化する目的で行います。 **ハイパーパラメーター** の値は、モデルのトレーニング手順の外部で指定される必要があります。 この値に関する仮定によって、モデルの柔軟性と精度が影響を受けます。 たとえば、デシジョン ツリーには、必要なツリーの深さやリーフの数といったハイパーパラメーターがあります。 サポート ベクター マシン (SVM) の場合は、誤分類ペナルティ項を設定する必要があります。 

この記事では、ハイパーパラメーターの最適化の実行に、グリッド探索または **パラメーター スイープ** と呼ばれる一般的な手法を使用します。 この検索は、学習アルゴリズムのためのハイパーパラメーター空間のサブセットを通過します。 クロス検証のパフォーマンス メトリックを使用すると、グリッド探索アルゴリズムによって生成される結果から最適なものを選別できます。 CV をハイパーパラメーター スイープと組み合わせて使用することで、モデルがトレーニング データに過剰適合するなどの問題を抑制でき、結果として、トレーニング データの抽出元であるデータセット全般にモデルを適用するための容量が保持されます。

使用するモデルは、ロジスティック回帰と線形回帰、ランダム フォレスト、勾配ブースティング ツリーです。

* [SGD を使用した線形回帰](https://spark.apache.org/docs/latest/api/python/reference/api/pyspark.mllib.regression.LinearRegressionWithSGD.html#pyspark.mllib.regression.LinearRegressionWithSGD
) は、最適化に確率的勾配降下 (SGD) 法を使用し、特徴のスケーリングを使用して支払われるチップの金額を予測する線形回帰モデルです。 
* [LBFGS を使用したロジスティック回帰](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.classification.LogisticRegressionWithLBFGS) ("ロジット" 回帰) は、データ分類を実行するために従属変数がカテゴリ型である場合に使用できる回帰モデルです。 LBFGS は、限られた量のコンピューター メモリを使用する Broyden–Fletcher–Goldfarb–Shanno (BFGS) アルゴリズムに近い準ニュートン最適化アルゴリズムであり、機械学習で広く使用されています。
* [ランダム フォレスト](https://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) は、複数のデシジョン ツリーをまとめたものです。  オーバーフィットのリスクを軽減するために、多くのデシジョン ツリーが結合されています。 ランダム フォレストは回帰と分類に使用されます。カテゴリの特徴を処理し、多クラス分類設定に拡張できます。 特徴のスケーリングは不要であり、非線形性や特徴の相互作用をキャプチャできます。 ランダム フォレストは、分類と回帰に使用される最も一般的な機械学習モデルの 1 つです。
* [勾配ブースティング ツリー](https://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTS) は、複数のデシジョン ツリーをまとめたものです。 GBTS は、デシジョン ツリーを繰り返しトレーニングすることで損失関数を最小限に抑えます。 GBTS は回帰と分類に使用されます。カテゴリの特徴を処理できますが、特徴のスケーリングは不要であり、非線形性や特徴の相互作用をキャプチャできます。 また、多クラス分類の設定にも使用できます。

CV とハイパーパラメーター スイープを使用するモデリングの例は、二項分類の問題向けです。 (パラメーター スイープを使用しない) より簡単な例は、回帰タスクのメイン トピックに記載されています。 また、「付録」には、線形回帰に Elastic Net を使用する検証と、ランダム フォレスト回帰にパラメーター スイープを使用する CV が記載されています。 **Elastic Net** は、線形回帰モデルを適合させるための正規化回帰手法の 1 つで、L1 メトリックと L2 メトリックを [Lasso](https://en.wikipedia.org/wiki/Lasso%20%28statistics%29) 手法と [Ridge](https://en.wikipedia.org/wiki/Tikhonov_regularization) 手法のペナルティとして線形に結合します。   

<!-- -->

> [!NOTE]
> Spark MLlib ツールキットは大規模なデータセットを操作することを目的としていますが、ここでは便宜上、比較的小規模なサンプル (最大30 MB、170,000 行使用、元の NYC データセットの約 0.1%) を使用しています。 ここでの演習は、2 つの worker ノードを含む HDInsight クラスターで効率的に実行されます (約 10 分)。 少し変更を加えれば、同じコードを使用して大規模なデータセットを処理できます。メモリへのデータのキャッシュやクラスター サイズの変更など、適切な変更を加えてください。

<!-- -->

## <a name="setup-spark-clusters-and-notebooks"></a>セットアップ: Spark クラスターと Notebook
このチュートリアルで示すセットアップ手順とコードは HDInsight Spark 1.6 向けですが、 Jupyter Notebook は HDInsight Spark 1.6 と Spark 2.0 の両方のクラスター向けに提供されています。 ノートブックの説明およびノートブックへのリンクは、ノートブックが含まれる GitHub リポジトリの [Readme.md](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) 内にあります。 また、このページとリンク先のノートブックに記載しているコードは汎用性があり、どの Spark クラスターでも動作します。 HDInsight Spark を使用していない場合、クラスターのセットアップと管理の手順は、ここに記載されている内容と若干異なります。 ご参考までに、Jupyter Notebook サーバーの pyspark カーネルで実行される Spark 1.6 および 2.0 向け Jupyter Notebook へのリンクを以下に示します。

### <a name="spark-16-notebooks"></a>Spark 1.6 向け Notebook

[pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure-Samples/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb):Notebook #1 のトピックと、ハイパーパラメーター チューニングとクロス検証を使用したモデル開発が含まれます。

### <a name="spark-20-notebooks"></a>Spark 2.0 向け Notebook

[Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure-Samples/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): このファイルでは、Spark 2.0 クラスターでデータの探索、モデリング、スコア付けを実行する方法について説明します。

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="setup-storage-locations-libraries-and-the-preset-spark-context"></a>セットアップ: ストレージの場所、ライブラリ、プリセットの Spark コンテキスト
Spark は、Azure Storage BLOB (WASB とも呼ばれます) に対する読み取りと書き込みを実行できます。 そのため、WASB に保存されている既存のデータは Spark を使用して処理することができ、結果も WASB に保存できます。

WASB にモデルやファイルを保存するには、パスを正しく指定する必要があります。 Spark クラスターに接続されている既定のコンテナーは、"wasb///" で始まるパスを使用して参照できます。 他の場所は "wasb://" で参照します。

### <a name="set-directory-paths-for-storage-locations-in-wasb"></a>WASB のストレージの場所となるディレクトリ パスの設定
次のコード サンプルでは、読み取るデータの場所と、モデルの出力の保存先となるモデル ストレージ ディレクトリのパスを指定しています。

```python
# SET PATHS TO FILE LOCATIONS: DATA AND MODEL STORAGE

# LOCATION OF TRAINING DATA
taxi_train_file_loc = "wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv";


# SET THE MODEL STORAGE DIRECTORY PATH 
# NOTE THAT THE FINAL BACKSLASH IN THE PATH IS NEEDED.
modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/";

# PRINT START TIME
import datetime
datetime.datetime.now()
```

**OUTPUT**

datetime.datetime(2016, 4, 18, 17, 36, 27, 832799)

### <a name="import-libraries"></a>ライブラリをインポートする
次のコードを使用して、必要なライブラリをインポートします。

```python
# LOAD PYSPARK LIBRARIES
import pyspark
from pyspark import SparkConf
from pyspark import SparkContext
from pyspark.sql import SQLContext
import matplotlib
import matplotlib.pyplot as plt
from pyspark.sql import Row
from pyspark.sql.functions import UserDefinedFunction
from pyspark.sql.types import *
import atexit
from numpy import array
import numpy as np
import datetime
```

### <a name="preset-spark-context-and-pyspark-magics"></a>プリセットの Spark コンテキストと PySpark マジック
Jupyter Notebook で提供される PySpark カーネルには、コンテキストがあらかじめ設定されています。 そのため、開発しているアプリケーションの操作を開始する前に Spark コンテキストまたは Hive コンテキストを明示的に設定する必要はありません。 これらのコンテキストは既定で利用できます。 各コンテキストは次のとおりです。

* sc: Spark 用 
* sqlContext: Hive 用

PySpark カーネルには、"マジック"、つまり、%% で呼び出すことができる特別なコマンドがいくつか事前定義されています。 そのようなコマンドが、以降のコード サンプルでは 2 つ使用されています。

* **%%local**: 後続行のコードをローカルで実行することを指定します。 コードは有効な Python コードにする必要があります。
* **%%sql -o \<variable name>** sqlContext に対して Hive クエリを実行します。 -o パラメーターが渡される場合、クエリの結果は、Pandas データフレームとして %%local Python コンテキストで永続化されます。

Jupyter Notebook のカーネルと、それによって提供される定義済みの "マジック" (例: %%local) の詳細については、「[HDInsight の HDInsight Spark Linux クラスターと Jupyter Notebook で使用可能なカーネル](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md)」をご覧ください。

## <a name="data-ingestion-from-public-blob"></a>パブリック BLOB からのデータの取り込み:
データ サイエンス プロセスでは、まず、分析するデータをソースからデータ探索およびモデリング環境に取り込みます。 このチュートリアルでは、この環境が Spark です。 ここでは、次の一連のタスクを実行するコードを示します。

* モデル化するデータ サンプルの取り込み
* 入力データセット (.tsv ファイルとして保存) の読み取り
* データのフォーマットとクリーニング
* オブジェクト (RDD またはデータ フレーム) の作成とメモリへのキャッシュ
* SQL コンテキストでオブジェクトを一時テーブルとして登録

データ取り込みのコードを次に示します。

```python
# RECORD START TIME
timestart = datetime.datetime.now()

# IMPORT FILE FROM PUBLIC BLOB
taxi_train_file = sc.textFile(taxi_train_file_loc)

# GET SCHEMA OF THE FILE FROM HEADER
schema_string = taxi_train_file.first()
fields = [StructField(field_name, StringType(), True) for field_name in schema_string.split('\t')]
fields[7].dataType = IntegerType() #Pickup hour
fields[8].dataType = IntegerType() # Pickup week
fields[9].dataType = IntegerType() # Weekday
fields[10].dataType = IntegerType() # Passenger count
fields[11].dataType = FloatType() # Trip time in secs
fields[12].dataType = FloatType() # Trip distance
fields[19].dataType = FloatType() # Fare amount
fields[20].dataType = FloatType() # Surcharge
fields[21].dataType = FloatType() # Mta_tax
fields[22].dataType = FloatType() # Tip amount
fields[23].dataType = FloatType() # Tolls amount
fields[24].dataType = FloatType() # Total amount
fields[25].dataType = IntegerType() # Tipped or not
fields[26].dataType = IntegerType() # Tip class
taxi_schema = StructType(fields)

# PARSE FIELDS AND CONVERT DATA TYPE FOR SOME FIELDS
taxi_header = taxi_train_file.filter(lambda l: "medallion" in l)
taxi_temp = taxi_train_file.subtract(taxi_header).map(lambda k: k.split("\t"))\
            .map(lambda p: (p[0],p[1],p[2],p[3],p[4],p[5],p[6],int(p[7]),int(p[8]),int(p[9]),int(p[10]),
                        float(p[11]),float(p[12]),p[13],p[14],p[15],p[16],p[17],p[18],float(p[19]),
                        float(p[20]),float(p[21]),float(p[22]),float(p[23]),float(p[24]),int(p[25]),int(p[26])))


# CREATE DATA FRAME
taxi_train_df = sqlContext.createDataFrame(taxi_temp, taxi_schema)

# CREATE A CLEANED DATA-FRAME BY DROPPING SOME UN-NECESSARY COLUMNS & FILTERING FOR UNDESIRED VALUES OR OUTLIERS
taxi_df_train_cleaned = taxi_train_df.drop('medallion').drop('hack_license').drop('store_and_fwd_flag').drop('pickup_datetime')\
    .drop('dropoff_datetime').drop('pickup_longitude').drop('pickup_latitude').drop('dropoff_latitude')\
    .drop('dropoff_longitude').drop('tip_class').drop('total_amount').drop('tolls_amount').drop('mta_tax')\
    .drop('direct_distance').drop('surcharge')\
    .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200" )

# CACHE & MATERIALIZE DATA-FRAME IN MEMORY. GOING THROUGH AND COUNTING NUMBER OF ROWS MATERIALIZES THE DATA-FRAME IN MEMORY
taxi_df_train_cleaned.cache()
taxi_df_train_cleaned.count()

# REGISTER DATA-FRAME AS A TEMP-TABLE IN SQL-CONTEXT
taxi_df_train_cleaned.registerTempTable("taxi_train")

# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**OUTPUT**

上記のセルの実行に要した時間: 276.62 秒

## <a name="data-exploration--visualization"></a>データの探索と視覚化
データが Spark に取り込まれたら、次に、探索と視覚化によってデータの理解を深めます。 このセクションでは、SQL クエリを使用してタクシー データを調べ、視覚化するためにターゲット変数と予想される特徴をプロットします。 具体的には、タクシー乗車における乗客数の頻度、チップの金額の頻度、支払金額と支払の種類によるチップの変化をプロットします。

### <a name="plot-a-histogram-of-passenger-count-frequencies-in-the-sample-of-taxi-trips"></a>タクシー乗車データのサンプルで乗客数の頻度のヒストグラムをプロットする
このコードおよび後続のスニペットでは、SQL マジックを使用してサンプルを照会し、ローカル マジックを使用してデータをプロットしています。

* **SQL マジック (`%%sql`)** HDInsight PySpark カーネルは、sqlContext に対する簡単なインライン HiveQL クエリをサポートしています。 引数 (-o VARIABLE_NAME) を指定すると、SQL クエリの出力結果が Pandas データフレームとして Jupyter サーバー上に永続化されます。 つまり、出力結果をローカルから使用できるようになります。
* **`%%local` マジック** は、Jupyter サーバー (HDInsight クラスターのヘッド ノード) でコードをローカルで実行するときに使用します。 通常、`%%local` マジックは、`%%sql -o` マジックを使用してクエリを実行した後に使用します。 SQL クエリの出力結果を -o パラメーターでローカルに永続化したうえで、 `%%local` マジックを使用すると、それに続く一連のコード スニペットが、ローカルに永続化されている SQL クエリの出力結果に対してローカルに実行されます。 その出力結果は、コードの実行後、自動的に視覚化されます。

次のクエリは、乗客数ごとの乗車回数を取得しています。 

```sql
# PLOT FREQUENCY OF PASSENGER COUNTS IN TAXI TRIPS

# SQL QUERY
%%sql -q -o sqlResults
SELECT passenger_count, COUNT(*) as trip_counts FROM taxi_train WHERE passenger_count > 0 and passenger_count < 7 GROUP BY passenger_count
```

以下のコードは、クエリの出力結果からローカル データフレームを作成し、データをプロットします。 `%%local` マジックでローカル データフレーム (`sqlResults`) を作成し、そのデータを matplotlib でプロットします。 

<!-- -->

> [!NOTE]
> この PySpark マジックは、このチュートリアルの中でたびたび使用しています。 データの量が大きい場合はサンプリングして、ローカル メモリに収まるようにデータフレームを作成する必要があります。

<!-- -->

```python
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER
%%local

# USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES. 
# CLICK ON THE TYPE OF PLOT TO BE GENERATED (E.G. LINE, AREA, BAR ETC.)
sqlResults
```

乗客数ごとの乗車回数をプロットするコードを次に示します。

```python
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
%%local
import matplotlib.pyplot as plt
%matplotlib inline

# PLOT PASSENGER NUMBER VS TRIP COUNTS
x_labels = sqlResults['passenger_count'].values
fig = sqlResults[['trip_counts']].plot(kind='bar', facecolor='lightblue')
fig.set_xticklabels(x_labels)
fig.set_title('Counts of trips by passenger count')
fig.set_xlabel('Passenger count in trips')
fig.set_ylabel('Trip counts')
plt.show()
```

**OUTPUT**

![Frequency of trips by passenger count](./media/spark-advanced-data-exploration-modeling/frequency-of-trips-by-passenger-count.png)

視覚化にはいくつかの種類 (表、円グラフ、折れ線グラフ、面グラフ、棒グラフ) があり、Notebook の **[Type (タイプ)]** メニュー ボタンで選択できます。 ここに示したのは棒グラフによるプロットです。

### <a name="plot-a-histogram-of-tip-amounts-and-how-tip-amount-varies-by-passenger-count-and-fare-amounts"></a>チップの金額と、乗客数別および料金別のチップ金額の変化のヒストグラムをプロットする
SQL クエリを使用してデータをサンプリングします。

```sql
# SQL SQUERY
%%sql -q -o sqlResults
    SELECT fare_amount, passenger_count, tip_amount, tipped
    FROM taxi_train 
    WHERE passenger_count > 0 
    AND passenger_count < 7
    AND fare_amount > 0 
    AND fare_amount < 200
    AND payment_type in ('CSH', 'CRD')
    AND tip_amount > 0 
    AND tip_amount < 25
```

次のコード セルでは、サンプリングされたデータに対し、SQL クエリを使用して 3 つのプロットを作成しています。

```python
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
%%local
%matplotlib inline

# TIP BY PAYMENT TYPE AND PASSENGER COUNT
ax1 = resultsPDDF[['tip_amount']].plot(kind='hist', bins=25, facecolor='lightblue')
ax1.set_title('Tip amount distribution')
ax1.set_xlabel('Tip Amount ($)')
ax1.set_ylabel('Counts')
plt.suptitle('')
plt.show()

# TIP BY PASSENGER COUNT
ax2 = resultsPDDF.boxplot(column=['tip_amount'], by=['passenger_count'])
ax2.set_title('Tip amount ($) by Passenger count')
ax2.set_xlabel('Passenger count')
ax2.set_ylabel('Tip Amount ($)')
plt.suptitle('')
plt.show()

# TIP AMOUNT BY FARE AMOUNT, POINTS ARE SCALED BY PASSENGER COUNT
ax = resultsPDDF.plot(kind='scatter', x= 'fare_amount', y = 'tip_amount', c='blue', alpha = 0.10, s=5*(resultsPDDF.passenger_count))
ax.set_title('Tip amount by Fare amount ($)')
ax.set_xlabel('Fare Amount')
ax.set_ylabel('Tip Amount')
plt.axis([-2, 120, -2, 30])
plt.show()
```

**出力:** 

![チップの金額の分布](./media/spark-advanced-data-exploration-modeling/tip-amount-distribution.png)

![乗客数別チップの金額](./media/spark-advanced-data-exploration-modeling/tip-amount-by-passenger-count.png)

![Tip amount by fare Amount](./media/spark-advanced-data-exploration-modeling/tip-amount-by-fare-amount.png)

## <a name="feature-engineering-transformation-and-data-preparation-for-modeling"></a>モデリングのための特徴エンジニアリング、変換、およびデータの準備
このセクションでは、ML モデリングで使用するデータを準備する手順について説明し、手順を実行するコードを示します。 次のタスクを実行する方法を示します。

* 時間を乗車時間のビンにパーティション分割して新しい特徴を作成する
* カテゴリの特徴のインデックス作成とワンホット エンコードを実行する
* ML 関数への入力用にラベル付きポイント オブジェクトを作成する
* データのランダム サブサンプリングを作成し、トレーニング セットとテスト セットに分ける
* 特徴のスケーリング
* オブジェクトをメモリにキャッシュする

### <a name="create-a-new-feature-by-partitioning-traffic-times-into-bins"></a>乗車時間をビンにパーティション分割して新しい特徴を作成する
次のコードでは、乗車時間をビンにパーティション分割して新しい特徴を作成する方法と、結果として生成されたデータ フレームをメモリにキャッシュする方法を示します。 Resilient Distributed Dataset (RDD) とデータ フレームを繰り返し使用する場合、キャッシュによって実行時間が短縮されます。 そのため、このチュートリアルでは複数の段階で RDD とデータ フレームをキャッシュしています。

```python
# CREATE FOUR BUCKETS FOR TRAFFIC TIMES
sqlStatement = """
    SELECT *,
    CASE
     WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night" 
     WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush" 
     WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
     WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
    END as TrafficTimeBins
    FROM taxi_train 
"""
taxi_df_train_with_newFeatures = sqlContext.sql(sqlStatement)
```

```python
# CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
# THE .COUNT() GOES THROUGH THE ENTIRE DATA-FRAME,
# MATERIALIZES IT IN MEMORY, AND GIVES THE COUNT OF ROWS.
taxi_df_train_with_newFeatures.cache()
taxi_df_train_with_newFeatures.count()
```

**OUTPUT**

126050

### <a name="index-and-one-hot-encode-categorical-features"></a>カテゴリの特徴のインデックス作成とワンホット エンコードを実行する
ここでは、モデリング関数への入力用に、カテゴリの特徴のインデックスを作成し、特徴をエンコードする方法について説明します。 MLlib のモデリング関数と予測関数では、特徴のカテゴリ入力データを使用する前に、データのインデックスを作成するか、データをエンコードする必要があります。 

モデルに応じて、さまざまな方法でカテゴリ入力データのインデックス作成またはエンコードを実行する必要があります。 例を挙げると、ロジスティック回帰モデルと線形回帰モデルでは、ワンホット エンコードが必要です。たとえば、3 つのカテゴリを持つ特徴は、観察のカテゴリに応じてそれぞれ 0 または 1 が含まれた 3 つの特徴列に展開できます。 MLlib には、ワンホット エンコードを実行する [OneHotEncoder](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) 関数が用意されています。 このエンコーダーは、ラベル インデックスの列をバイナリ ベクトルの列にマップします。この列に含まれる値は最大でも 1 つだけです。 このエンコードにより、数値を持つ特徴を必要とするアルゴリズム (ロジスティック回帰など) をカテゴリの特徴に適用できます。

カテゴリの特徴のインデックス作成とエンコードを実行するコードを次に示します。

```python
# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorAssembler, OneHotEncoder, VectorIndexer

# INDEX AND ENCODE VENDOR_ID
stringIndexer = StringIndexer(inputCol="vendor_id", outputCol="vendorIndex")
model = stringIndexer.fit(taxi_df_train_with_newFeatures) # Input data-frame is the cleaned one from above
indexed = model.transform(taxi_df_train_with_newFeatures)
encoder = OneHotEncoder(dropLast=False, inputCol="vendorIndex", outputCol="vendorVec")
encoded1 = encoder.transform(indexed)

# INDEX AND ENCODE RATE_CODE
stringIndexer = StringIndexer(inputCol="rate_code", outputCol="rateIndex")
model = stringIndexer.fit(encoded1)
indexed = model.transform(encoded1)
encoder = OneHotEncoder(dropLast=False, inputCol="rateIndex", outputCol="rateVec")
encoded2 = encoder.transform(indexed)

# INDEX AND ENCODE PAYMENT_TYPE
stringIndexer = StringIndexer(inputCol="payment_type", outputCol="paymentIndex")
model = stringIndexer.fit(encoded2)
indexed = model.transform(encoded2)
encoder = OneHotEncoder(dropLast=False, inputCol="paymentIndex", outputCol="paymentVec")
encoded3 = encoder.transform(indexed)

# INDEX AND TRAFFIC TIME BINS
stringIndexer = StringIndexer(inputCol="TrafficTimeBins", outputCol="TrafficTimeBinsIndex")
model = stringIndexer.fit(encoded3)
indexed = model.transform(encoded3)
encoder = OneHotEncoder(dropLast=False, inputCol="TrafficTimeBinsIndex", outputCol="TrafficTimeBinsVec")
encodedFinal = encoder.transform(indexed)

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**OUTPUT**

上記のセルの実行に要した時間: 3.14 秒

### <a name="create-labeled-point-objects-for-input-into-ml-functions"></a>ML 関数への入力用にラベル付きポイント オブジェクトを作成する
このセクションのコードでは、ラベル付きポイント データ型としてカテゴリ テキスト データのインデックスを作成し、それをエンコードする方法を示します。 この変換によって、MLlib ロジスティック回帰モデルや他の分類モデルのトレーニングとテストに使用するテキスト データを準備します。 ラベル付きポイント オブジェクトは、MLlib のほとんどの ML アルゴリズムで入力データとして必要とされる方法でフォーマットされた Resilient Distributed Dataset (RDD) です。 [ラベル付きポイント](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) は、ラベル/応答に関連付けられたローカル ベクトル (密または疎) です。

以下に示したのは、二項分類に使用するテキストの特徴のインデックスを作成してエンコードするコードです。

```python
# FUNCTIONS FOR BINARY CLASSIFICATION

# LOAD LIBRARIES
from pyspark.mllib.regression import LabeledPoint
from numpy import array

# INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
def parseRowIndexingBinary(line):
    features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.pickup_hour, line.weekday,
                         line.passenger_count, line.trip_time_in_secs, line.trip_distance, line.fare_amount])
    labPt = LabeledPoint(line.tipped, features)
    return  labPt

# ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC REGRESSION MODELS
def parseRowOneHotBinary(line):
    features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                        line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                               line.vendorVec.toArray(), line.rateVec.toArray(), line.paymentVec.toArray()), axis=0)
    labPt = LabeledPoint(line.tipped, features)
    return  labPt
```

以下に示したのは、線形回帰分析に使用するカテゴリ テキストの特徴をエンコードしてインデックスを作成するコードです。

```python
# FUNCTIONS FOR REGRESSION WITH TIP AMOUNT AS TARGET VARIABLE

# ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
def parseRowIndexingRegression(line):
    features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex, 
                         line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                         line.trip_distance, line.fare_amount])
    labPt = LabeledPoint(line.tip_amount, features)
    return  labPt

# INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO LINEAR REGRESSION MODELS
def parseRowOneHotRegression(line):
    features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                        line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                        line.vendorVec.toArray(), line.rateVec.toArray(), 
                                        line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
    labPt = LabeledPoint(line.tip_amount, features)
    return  labPt
```

### <a name="create-a-random-subsampling-of-the-data-and-split-it-into-training-and-testing-sets"></a>データのランダム サブサンプリングを作成し、トレーニング セットとテスト セットに分ける
次のコードでは、データのランダム サンプリングを作成します (ここでは 25% を使用)。 この例のデータセットのサイズでは、ランダム サンプリングを作成する必要はありませんが、ここではサンプリングの方法を示します。 これにより、必要に応じて独自の問題にランダム サンプリングを使用する方法を理解できます。 サンプル数が多い場合、サンプリングによってモデルのトレーニングにかかる時間を大幅に節約できます。 次に、分類および回帰モデリングで使用するために、サンプルをトレーニング用 (ここでは 75%) とテスト用 (ここでは 25%) に分けます。

```python
# RECORD START TIME
timestart = datetime.datetime.now()

# SPECIFY SAMPLING AND SPLITTING FRACTIONS
from pyspark.sql.functions import rand

samplingFraction = 0.25;
trainingFraction = 0.75; testingFraction = (1-trainingFraction);
seed = 1234;
encodedFinalSampled = encodedFinal.sample(False, samplingFraction, seed=seed)

# SPLIT SAMPLED DATA-FRAME INTO TRAIN/TEST, WITH A RANDOM COLUMN ADDED FOR DOING CV (SHOWN LATER)
# INCLUDE RAND COLUMN FOR CREATING CROSS-VALIDATION FOLDS
dfTmpRand = encodedFinalSampled.select("*", rand(0).alias("rand"));
trainData, testData = dfTmpRand.randomSplit([trainingFraction, testingFraction], seed=seed);

# CACHE TRAIN AND TEST DATA
trainData.cache()
testData.cache()

# FOR BINARY CLASSIFICATION TRAINING AND TESTING
indexedTRAINbinary = trainData.map(parseRowIndexingBinary)
indexedTESTbinary = testData.map(parseRowIndexingBinary)
oneHotTRAINbinary = trainData.map(parseRowOneHotBinary)
oneHotTESTbinary = testData.map(parseRowOneHotBinary)

# FOR REGRESSION TRAINING AND TESTING
indexedTRAINreg = trainData.map(parseRowIndexingRegression)
indexedTESTreg = testData.map(parseRowIndexingRegression)
oneHotTRAINreg = trainData.map(parseRowOneHotRegression)
oneHotTESTreg = testData.map(parseRowOneHotRegression)

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**OUTPUT**

上記のセルの実行に要した時間:0.31 秒

### <a name="feature-scaling"></a>特徴のスケーリング
この特徴のスケーリングはデータの正規化とも呼ばれ、目標関数において幅広く分散した値を持つ特徴に過大な重みが与えられないようにします。 特徴のスケーリングのコードでは、[StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) を使用して特徴を単価差異にスケーリングします。 これは、確率的勾配降下 (SGD) による線形回帰に使用するために、MLlib で提供されています。 SGD は、正規化回帰やサポート ベクター マシン (SVM) などの他のさまざまな機械学習モデルのトレーニングに広く使用されているアルゴリズムです。   

> [!TIP]
> LinearRegressionWithSGD アルゴリズムは特徴のスケーリングの影響を受けやすいことがわかりました。   
> 
> 

正規化線形 SGD アルゴリズムで使用するために変数をスケーリングするコードを次に示します。

```python
# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.mllib.regression import LabeledPoint
from pyspark.mllib.linalg import Vectors
from pyspark.mllib.feature import StandardScaler, StandardScalerModel
from pyspark.mllib.util import MLUtils

# SCALE VARIABLES FOR REGULARIZED LINEAR SGD ALGORITHM
label = oneHotTRAINreg.map(lambda x: x.label)
features = oneHotTRAINreg.map(lambda x: x.features)
scaler = StandardScaler(withMean=False, withStd=True).fit(features)
dataTMP = label.zip(scaler.transform(features.map(lambda x: Vectors.dense(x.toArray()))))
oneHotTRAINregScaled = dataTMP.map(lambda x: LabeledPoint(x[0], x[1]))

label = oneHotTESTreg.map(lambda x: x.label)
features = oneHotTESTreg.map(lambda x: x.features)
scaler = StandardScaler(withMean=False, withStd=True).fit(features)
dataTMP = label.zip(scaler.transform(features.map(lambda x: Vectors.dense(x.toArray()))))
oneHotTESTregScaled = dataTMP.map(lambda x: LabeledPoint(x[0], x[1]))

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**OUTPUT**

上記のセルの実行に要した時間: 11.67 秒

### <a name="cache-objects-in-memory"></a>オブジェクトをメモリにキャッシュする
ML アルゴリズムのトレーニングとテストの所要時間は、分類、回帰、およびスケーリングされた特徴に使用される入力データ フレーム オブジェクトをキャッシュすることで短縮できます。

```python
# RECORD START TIME
timestart = datetime.datetime.now()

# FOR BINARY CLASSIFICATION TRAINING AND TESTING
indexedTRAINbinary.cache()
indexedTESTbinary.cache()
oneHotTRAINbinary.cache()
oneHotTESTbinary.cache()

# FOR REGRESSION TRAINING AND TESTING
indexedTRAINreg.cache()
indexedTESTreg.cache()
oneHotTRAINreg.cache()
oneHotTESTreg.cache()

# SCALED FEATURES
oneHotTRAINregScaled.cache()
oneHotTESTregScaled.cache()

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**OUTPUT** 

上記のセルの実行に要した時間:0.13 秒

## <a name="predict-whether-or-not-a-tip-is-paid-with-binary-classification-models"></a>二項分類モデルを使用してチップが支払われるかどうかを予測する
このセクションでは、タクシーの乗車でチップが支払われるかどうかを予測する二項分類タスクで 3 つのモデルを使用する方法を示します。 使用するモデルは次のとおりです。

* Logistic regression (ロジスティック回帰) 
* ランダム フォレスト
* 勾配ブースティング ツリー

各モデル構築コードのセクションは、次のステップに分けることができます。 

1. **モデルのトレーニング** データ
2. **モデルの評価**
3. **モデルの保存**

パラメーター スイープによるクロス検証 (CV) を実行する方法が 2 つあります。

1. MLlib の任意のアルゴリズムとアルゴリズム内の任意のパラメーター セットに適用できる **汎用の** カスタム コードを使用する。 
2. **pySpark CrossValidator パイプライン関数** を使用する。 Spark 1.5.0 では CrossValidator にいくつかの制限があります。 
   
   * 将来の使用に備えてパイプライン モデルを保存または保持することはできません。
   * モデル内の個々のパラメーターに対して使用することはできません。
   * 個々の MLlib アルゴリズム対して使用することはできません。

### <a name="generic-cross-validation-and-hyperparameter-sweeping-used-with-the-logistic-regression-algorithm-for-binary-classification"></a>二項分類に対するロジスティック回帰アルゴリズムで使用する汎用的なクロス検証とハイパーパラメーター スイープ
このセクションのコードでは、NYC タクシーの乗車と料金のデータセットで、乗車でチップが支払われるかどうかを予測するロジスティック回帰モデル ( [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) を使用) をトレーニング、評価し、保存する方法を示します。 モデルは、MLlib 内の任意の学習アルゴリズムに適用できるカスタム コードで実装されたクロス検証 (CV) とハイパーパラメーター スイープを使用してトレーニングされます。   

<!-- -->

> [!NOTE]
> このカスタムの CV コードの実行には数分かかる場合があります。

<!-- -->

**CV とハイパーパラメーター スイープを使用してロジスティック回帰モデルをトレーニングする**

```python
# LOGISTIC REGRESSION CLASSIFICATION WITH CV AND HYPERPARAMETER SWEEPING

# GET ACCURACY FOR HYPERPARAMETERS BASED ON CROSS-VALIDATION IN TRAINING DATA-SET

# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD LIBRARIES
from pyspark.mllib.classification import LogisticRegressionWithLBFGS 
from pyspark.mllib.evaluation import BinaryClassificationMetrics

# CREATE PARAMETER GRID FOR LOGISTIC REGRESSION PARAMETER SWEEP
from sklearn.grid_search import ParameterGrid
grid = [{'regParam': [0.01, 0.1], 'iterations': [5, 10], 'regType': ["l1", "l2"], 'tolerance': [1e-3, 1e-4]}]
paramGrid = list(ParameterGrid(grid))
numModels = len(paramGrid)

# SET NUM FOLDS AND NUM PARAMETER SETS TO SWEEP ON
nFolds = 3;
h = 1.0 / nFolds;
metricSum = np.zeros(numModels);

# BEGIN CV WITH PARAMETER SWEEP
for i in range(nFolds):
    # Create training and x-validation sets
    validateLB = i * h
    validateUB = (i + 1) * h
    condition = (trainData["rand"] >= validateLB) & (trainData["rand"] < validateUB)
    validation = trainData.filter(condition)
    # Create LabeledPoints from data-frames
    if i > 0:
        trainCVLabPt.unpersist()
        validationLabPt.unpersist()
    trainCV = trainData.filter(~condition)
    trainCVLabPt = trainCV.map(parseRowOneHotBinary)
    trainCVLabPt.cache()
    validationLabPt = validation.map(parseRowOneHotBinary)
    validationLabPt.cache()
    # For parameter sets compute metrics from x-validation
    for j in range(numModels):
        regt = paramGrid[j]['regType']
        regp = paramGrid[j]['regParam']
        iters = paramGrid[j]['iterations']
        tol = paramGrid[j]['tolerance']
        # Train logistic regression model with hypermarameter set
        model = LogisticRegressionWithLBFGS.train(trainCVLabPt, regType=regt, iterations=iters,  
                                                  regParam=regp, tolerance = tol, intercept=True)
        predictionAndLabels = validationLabPt.map(lambda lp: (float(model.predict(lp.features)), lp.label))
        # Use ROC-AUC as accuracy metrics
        validMetrics = BinaryClassificationMetrics(predictionAndLabels)
        metric = validMetrics.areaUnderROC
        metricSum[j] += metric

avgAcc = metricSum / nFolds;
bestParam = paramGrid[np.argmax(avgAcc)];

# UNPERSIST OBJECTS
trainCVLabPt.unpersist()
validationLabPt.unpersist()

# TRAIN ON FULL TRAIING SET USING BEST PARAMETERS FROM CV/PARAMETER SWEEP
logitBest = LogisticRegressionWithLBFGS.train(oneHotTRAINbinary, regType=bestParam['regType'], 
                                              iterations=bestParam['iterations'], 
                                              regParam=bestParam['regParam'], tolerance = bestParam['tolerance'], 
                                              intercept=True)


# PRINT COEFFICIENTS AND INTERCEPT OF THE MODEL
# NOTE: There are 20 coefficient terms for the 10 features, 
#       and the different categories for features: vendorVec (2), rateVec, paymentVec (6), TrafficTimeBinsVec (4)
print("Coefficients: " + str(logitBest.weights))
print("Intercept: " + str(logitBest.intercept))

# PRINT ELAPSED TIME    
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**OUTPUT**

係数: [0.0082065285375, -0.0223675576104, -0.0183812028036, -3.48124578069e-05, -0.00247646947233, -0.00165897881503, 0.0675394837328, -0.111823113101, -0.324609912762, -0.204549780032, -1.36499216354, 0.591088507921, -0.664263411392, -1.00439726852, 3.46567827545, -3.51025855172, -0.0471341112232, -0.043521833294, 0.000243375810385, 0.054518719222]

切片: -0.0111216486893

上記のセルの実行に要した時間:14.43 秒

**標準メトリックで二項分類モデルを評価する**

このセクションのコードでは、ROC 曲線のプロットなど、テスト データセットに対してロジスティック回帰モデルを評価する方法を示します。

```python
# RECORD START TIME
timestart = datetime.datetime.now()

#IMPORT LIBRARIES
from sklearn.metrics import roc_curve,auc
from pyspark.mllib.evaluation import BinaryClassificationMetrics
from pyspark.mllib.evaluation import MulticlassMetrics

# PREDICT ON TEST DATA WITH BEST/FINAL MODEL
predictionAndLabels = oneHotTESTbinary.map(lambda lp: (float(logitBest.predict(lp.features)), lp.label))

# INSTANTIATE METRICS OBJECT
metrics = BinaryClassificationMetrics(predictionAndLabels)

# AREA UNDER PRECISION-RECALL CURVE
print("Area under PR = %s" % metrics.areaUnderPR)

# AREA UNDER ROC CURVE
print("Area under ROC = %s" % metrics.areaUnderROC)
metrics = MulticlassMetrics(predictionAndLabels)

# OVERALL STATISTICS
precision = metrics.precision()
recall = metrics.recall()
f1Score = metrics.fMeasure()
print("Summary Stats")
print("Precision = %s" % precision)
print("Recall = %s" % recall)
print("F1 Score = %s" % f1Score)

# OUTPUT PROBABILITIES AND REGISTER TEMP TABLE
logitBest.clearThreshold(); # This clears threshold for classification (0.5) and outputs probabilities
predictionAndLabelsDF = predictionAndLabels.toDF()
predictionAndLabelsDF.registerTempTable("tmp_results");

# PRINT ELAPSED TIME    
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**OUTPUT**

PR 下面積 = 0.985336538462

ROC 下面積 = 0.983383274312

要約統計量

精度 = 0.984174341679

再現率 = 0.984174341679

F1 スコア = 0.984174341679

上記のセルの実行に要した時間: 2.67 秒

**ROC 曲線をプロットする**

*predictionAndLabelsDF* は、既出のセルでテーブル *tmp_results* として登録されています。 *tmp_results* を使用してクエリを実行したり、sqlResults データフレームに結果を出力してプロットしたりできます。 次にコードを示します。

```python
# QUERY RESULTS                              
%%sql -q -o sqlResults
SELECT * from tmp_results
```

予測を行って ROC 曲線をプロットするコードを次に示します。

```python
# MAKE PREDICTIONS AND PLOT ROC-CURVE

# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES                              
%%local
%matplotlib inline
from sklearn.metrics import roc_curve,auc

#PREDICTIONS
predictions_pddf = sqlResults.rename(columns={'_1': 'probability', '_2': 'label'})
prob = predictions_pddf["probability"] 
fpr, tpr, thresholds = roc_curve(predictions_pddf['label'], prob, pos_label=1);
roc_auc = auc(fpr, tpr)

# PLOT ROC CURVES
plt.figure(figsize=(5,5))
plt.plot(fpr, tpr, label='ROC curve (area = %0.2f)' % roc_auc)
plt.plot([0, 1], [0, 1], 'k--')
plt.xlim([0.0, 1.0])
plt.ylim([0.0, 1.05])
plt.xlabel('False Positive Rate')
plt.ylabel('True Positive Rate')
plt.title('ROC Curve')
plt.legend(loc="lower right")
plt.show()
```

**OUTPUT**

![Logistic regression ROC curve for generic approach](./media/spark-advanced-data-exploration-modeling/logistic-regression-roc-curve.png)

**今後使用できるようにするための BLOB へのモデルの保持**

このセクションのコードでは、今後使用できるようにロジスティック回帰モデルを保存する方法を示します。

```python
# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.mllib.classification import LogisticRegressionModel

# PERSIST MODEL
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
logisticregressionfilename = "LogisticRegressionWithLBFGS_" + datestamp;
dirfilename = modelDir + logisticregressionfilename;

logitBest.save(sc, dirfilename);

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds";
```

**OUTPUT**

上記のセルの実行に要した時間: 34.57 秒

### <a name="use-mllibs-crossvalidator-pipeline-function-with-logistic-regression-elastic-regression-model"></a>ロジスティック回帰 (Elastic 回帰) モデルでの MLlib の CrossValidator パイプライン関数の使用
このセクションのコードでは、NYC タクシーの乗車と料金のデータセットで、乗車でチップが支払われるかどうかを予測するロジスティック回帰モデル ( [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) を使用) をトレーニング、評価し、保存する方法を示します。 モデルは、CV とパラメーター スイープを組み合わせる MLlib CrossValidator パイプライン関数で実装されたクロス検証 (CV) とハイパーパラメーター スイープを使用してトレーニングされます。   

<!-- -->

> [!NOTE]
> この MLlib CV コードの実行には数分かかる場合があります。

<!-- -->

```python
# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.ml.classification import LogisticRegression
from pyspark.ml import Pipeline
from pyspark.ml.evaluation import BinaryClassificationEvaluator
from pyspark.ml.tuning import CrossValidator, ParamGridBuilder
from sklearn.metrics import roc_curve,auc

# DEFINE ALGORITHM / MODEL
lr = LogisticRegression()

# DEFINE GRID PARAMETERS
paramGrid = ParamGridBuilder().addGrid(lr.regParam, (0.01, 0.1))\
                              .addGrid(lr.maxIter, (5, 10))\
                              .addGrid(lr.tol, (1e-4, 1e-5))\
                              .addGrid(lr.elasticNetParam, (0.25,0.75))\
                              .build()

# DEFINE CV WITH PARAMETER SWEEP
cv = CrossValidator(estimator= lr,
                    estimatorParamMaps=paramGrid,
                    evaluator=BinaryClassificationEvaluator(),
                    numFolds=3)

# CONVERT TO DATA-FRAME: THIS DOES NOT RUN ON RDDs
trainDataFrame = sqlContext.createDataFrame(oneHotTRAINbinary, ["features", "label"])

# TRAIN WITH CROSS-VALIDATION
cv_model = cv.fit(trainDataFrame)


## PREDICT AND EVALUATE ON TEST DATA-SET

# USE TEST DATASET FOR PREDICTION
testDataFrame = sqlContext.createDataFrame(oneHotTESTbinary, ["features", "label"])
test_predictions = cv_model.transform(testDataFrame)

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds";
``` 

**OUTPUT**

上記のセルの実行に要した時間: 107.98 秒

**ROC 曲線をプロットする**

*predictionAndLabelsDF* は、既出のセルでテーブル *tmp_results* として登録されています。 *tmp_results* を使用してクエリを実行したり、sqlResults データフレームに結果を出力してプロットしたりできます。 次にコードを示します。

```python
# QUERY RESULTS
%%sql -q -o sqlResults
SELECT label, prediction, probability from tmp_results
```

ROC 曲線をプロットするコードを次に示します。

```python
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES 
%%local
from sklearn.metrics import roc_curve,auc

# ROC CURVE
prob = [x["values"][1] for x in sqlResults["probability"]]
fpr, tpr, thresholds = roc_curve(sqlResults['label'], prob, pos_label=1);
roc_auc = auc(fpr, tpr)

#PLOT
plt.figure(figsize=(5,5))
plt.plot(fpr, tpr, label='ROC curve (area = %0.2f)' % roc_auc)
plt.plot([0, 1], [0, 1], 'k--')
plt.xlim([0.0, 1.0])
plt.ylim([0.0, 1.05])
plt.xlabel('False Positive Rate')
plt.ylabel('True Positive Rate')
plt.title('ROC Curve')
plt.legend(loc="lower right")
plt.show()
```

**OUTPUT**

![Logistic regression ROC curve using MLlib's CrossValidator](./media/spark-advanced-data-exploration-modeling/mllib-crossvalidator-roc-curve.png)

### <a name="random-forest-classification"></a>ランダム フォレスト分類
このセクションのコードでは、NYC タクシーの乗車と料金のデータセットで、乗車でチップが支払われるかどうかを予測するランダム フォレスト回帰をトレーニング、評価し、保存する方法を示します。

```python
# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.mllib.tree import RandomForest, RandomForestModel
from pyspark.mllib.util import MLUtils
from pyspark.mllib.evaluation import BinaryClassificationMetrics
from pyspark.mllib.evaluation import MulticlassMetrics

# SPECIFY NUMBER OF CATEGORIES FOR CATEGORICAL FEATURES. FEATURE #0 HAS 2 CATEGORIES, FEATURE #2 HAS 2 CATEGORIES, AND SO ON
categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}

# TRAIN RANDOMFOREST MODEL
rfModel = RandomForest.trainClassifier(indexedTRAINbinary, numClasses=2, 
                                       categoricalFeaturesInfo=categoricalFeaturesInfo,
                                       numTrees=25, featureSubsetStrategy="auto",
                                       impurity='gini', maxDepth=5, maxBins=32)
## UN-COMMENT IF YOU WANT TO PRING TREES
#print('Learned classification forest model:')
#print(rfModel.toDebugString())

# PREDICT ON TEST DATA AND EVALUATE
predictions = rfModel.predict(indexedTESTbinary.map(lambda x: x.features))
predictionAndLabels = indexedTESTbinary.map(lambda lp: lp.label).zip(predictions)

# AREA UNDER ROC CURVE
metrics = BinaryClassificationMetrics(predictionAndLabels)
print("Area under ROC = %s" % metrics.areaUnderROC)

# PERSIST MODEL IN BLOB
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
rfclassificationfilename = "RandomForestClassification_" + datestamp;
dirfilename = modelDir + rfclassificationfilename;

rfModel.save(sc, dirfilename);

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**OUTPUT**

ROC 下面積 = 0.985336538462

上記のセルの実行に要した時間: 26.72 秒

### <a name="gradient-boosting-trees-classification"></a>勾配ブースティング ツリー分類
このセクションのコードでは、NYC タクシー乗車および料金データセットで、乗車でチップが支払われるかどうかを予測する勾配ブースティング ツリー モデルをトレーニング、評価し、保存する方法を示します。

```python
# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel

# SPECIFY NUMBER OF CATEGORIES FOR CATEGORICAL FEATURES. FEATURE #0 HAS 2 CATEGORIES, FEATURE #2 HAS 2 CATEGORIES, AND SO ON
categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}

gbtModel = GradientBoostedTrees.trainClassifier(indexedTRAINbinary, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                                numIterations=10)
## UNCOMMENT IF YOU WANT TO PRINT TREE DETAILS
#print('Learned classification GBT model:')
#print(bgtModel.toDebugString())

# PREDICT ON TEST DATA AND EVALUATE
predictions = gbtModel.predict(indexedTESTbinary.map(lambda x: x.features))
predictionAndLabels = indexedTESTbinary.map(lambda lp: lp.label).zip(predictions)

# Area under ROC curve
metrics = BinaryClassificationMetrics(predictionAndLabels)
print("Area under ROC = %s" % metrics.areaUnderROC)

# PERSIST MODEL IN A BLOB
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
btclassificationfilename = "GradientBoostingTreeClassification_" + datestamp;
dirfilename = modelDir + btclassificationfilename;

gbtModel.save(sc, dirfilename)

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**OUTPUT**

ROC 下面積 = 0.985336538462

上記のセルの実行に要した時間: 28.13 秒

## <a name="predict-tip-amount-with-regression-models-not-using-cv"></a>回帰モデルを使用して (CV を使用せずに) チップの金額を予測する
このセクションでは、チップの他の特徴に基づいて、タクシーの乗車で支払われるチップの金額を予測する回帰タスクで 3 つのモデルを使用する方法を示します。 使用するモデルは次のとおりです。

* 正規化線形回帰
* ランダム フォレスト
* 勾配ブースティング ツリー

これらのモデルについては、「はじめに」で説明しました。 各モデル構築コードのセクションは、次のステップに分けることができます。 

1. **モデルのトレーニング** データ
2. **モデルの評価**
3. **モデルの保存**   

<!-- -->

> [!NOTE] 
> クロス検証は、このセクションの 3 つの回帰モデルでは使用されません。この点については、ロジスティック回帰モデルで詳しく取り上げます。 線形回帰に対して Elastic Net を使用して CV を実行する方法を示す例が、このトピックの「付録」に記載されています。

<!-- -->

<!-- -->

> [!NOTE] 
> 経験上、LinearRegressionWithSGD モデルの収束で問題が発生する可能性があるため、有効なモデルを入手するために、パラメーターを慎重に変更/最適化する必要があります。 収束には変数のスケーリングがきわめて有効です。 このトピックの「付録」に示すように、Elastic Net 回帰は LinearRegressionWithSGD の代わりに使用することもできます。

<!-- -->

### <a name="linear-regression-with-sgd"></a>SGD を使用した線形回帰
このセクションのコードでは、スケーリングされた特徴を使用して、最適化に確率的勾配降下 (SGD) を使用する線形回帰をトレーニングする方法と、このモデルにスコアを付け、評価し、Azure BLOB Storage (WASB) に保存する方法を示します。

> [!TIP]
> 経験上、LinearRegressionWithSGD モデルの収束で問題が発生する可能性があるため、有効なモデルを入手するために、パラメーターを慎重に変更/最適化する必要があります。 収束には変数のスケーリングがきわめて有効です。
> 
> 

```python
# LINEAR REGRESSION WITH SGD 

# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD LIBRARIES
from pyspark.mllib.regression import LabeledPoint, LinearRegressionWithSGD, LinearRegressionModel
from pyspark.mllib.evaluation import RegressionMetrics
from scipy import stats

# USE SCALED FEATURES TO TRAIN MODEL
linearModel = LinearRegressionWithSGD.train(oneHotTRAINregScaled, iterations=100, step = 0.1, regType='l2', regParam=0.1, intercept = True)

# PRINT COEFFICIENTS AND INTERCEPT OF THE MODEL
# NOTE: There are 20 coefficient terms for the 10 features, 
#       and the different categories for features: vendorVec (2), rateVec, paymentVec (6), TrafficTimeBinsVec (4)
print("Coefficients: " + str(linearModel.weights))
print("Intercept: " + str(linearModel.intercept))

# SCORE ON SCALED TEST DATA-SET & EVALUATE
predictionAndLabels = oneHotTESTregScaled.map(lambda lp: (float(linearModel.predict(lp.features)), lp.label))
testMetrics = RegressionMetrics(predictionAndLabels)

print("RMSE = %s" % testMetrics.rootMeanSquaredError)
print("R-sqr = %s" % testMetrics.r2)

# SAVE MODEL IN BLOB
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
linearregressionfilename = "LinearRegressionWithSGD_" + datestamp;
dirfilename = modelDir + linearregressionfilename;

linearModel.save(sc, dirfilename)

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**OUTPUT**

係数: [0.0141707753435, -0.0252930927087, -0.0231442517137, 0.247070902996, 0.312544147152, 0.360296120645, 0.0122079566092, -0.00456498588241, -0.0898228505177, 0.0714046248793, 0.102171263868, 0.100022455632, -0.00289545676449, -0.00791124681938, 0.54396316518, -0.536293513569, 0.0119076553369, -0.0173039244582, 0.0119632796147, 0.00146764882502]

切片: 0.854507624459

RMSE = 1.23485131376

R-sqr = 0.597963951127

上記のセルの実行に要した時間: 38.62 秒

### <a name="random-forest-regression"></a>ランダム フォレスト回帰
このセクションのコードでは、NYC タクシー乗車データでチップの金額を予測するランダム フォレスト モデルをトレーニング、評価し、保存する方法を示します。   

<!-- -->

> [!NOTE]
> カスタム コードを使用したクロス検証とパラメーター スイープは、「付録」に記載されています。

<!-- -->

```python
#PREDICT TIP AMOUNTS USING RANDOM FOREST

# RECORD START TIME
timestart= datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.mllib.tree import RandomForest, RandomForestModel
from pyspark.mllib.util import MLUtils
from pyspark.mllib.evaluation import RegressionMetrics


# TRAIN MODEL
categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                    numTrees=25, featureSubsetStrategy="auto",
                                    impurity='variance', maxDepth=10, maxBins=32)
# UN-COMMENT IF YOU WANT TO PRING TREES
#print('Learned classification forest model:')
#print(rfModel.toDebugString())

# PREDICT AND EVALUATE ON TEST DATA-SET
predictions = rfModel.predict(indexedTESTreg.map(lambda x: x.features))
predictionAndLabels = oneHotTESTreg.map(lambda lp: lp.label).zip(predictions)

testMetrics = RegressionMetrics(predictionAndLabels)
print("RMSE = %s" % testMetrics.rootMeanSquaredError)
print("R-sqr = %s" % testMetrics.r2)

# SAVE MODEL IN BLOB
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
rfregressionfilename = "RandomForestRegression_" + datestamp;
dirfilename = modelDir + rfregressionfilename;

rfModel.save(sc, dirfilename);

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**OUTPUT**

RMSE = 0.931981967875

R-sqr = 0.733445485802

上記のセルの実行に要した時間: 25.98 秒

### <a name="gradient-boosting-trees-regression"></a>勾配ブースティング ツリー回帰
このセクションのコードでは、NYC タクシー乗車データでチップの金額を予測する勾配ブースティング ツリー モデルをトレーニング、評価し、保存する方法を示します。

**トレーニングと評価**

```python
#PREDICT TIP AMOUNTS USING GRADIENT BOOSTING TREES

# RECORD START TIME
timestart= datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel
from pyspark.mllib.util import MLUtils

# TRAIN MODEL
categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
gbtModel = GradientBoostedTrees.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo, 
                                                numIterations=10, maxBins=32, maxDepth = 4, learningRate=0.1)

# EVALUATE A TEST DATA-SET
predictions = gbtModel.predict(indexedTESTreg.map(lambda x: x.features))
predictionAndLabels = indexedTESTreg.map(lambda lp: lp.label).zip(predictions)

testMetrics = RegressionMetrics(predictionAndLabels)
print("RMSE = %s" % testMetrics.rootMeanSquaredError)
print("R-sqr = %s" % testMetrics.r2)

# PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES
test_predictions= sqlContext.createDataFrame(predictionAndLabels)
test_predictions_pddf = test_predictions.toPandas()

# SAVE MODEL IN BLOB
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
btregressionfilename = "GradientBoostingTreeRegression_" + datestamp;
dirfilename = modelDir + btregressionfilename;
gbtModel.save(sc, dirfilename)

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**OUTPUT**

RMSE = 0.928172197114

R-sqr = 0.732680354389

上記のセルの実行に要した時間: 20.9 秒

**プロット**

*tmp_results* は、既出のセルで Hive テーブルとして登録されています。 このテーブルからの結果を *sqlResults* データフレームに出力し、プロットに使用しています。 次にコードを示します。

```python
# PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES

# SELECT RESULTS
%%sql -q -o sqlResults
SELECT * from tmp_results
```

Jupyter サーバーを使用してデータをプロットするコードを次に示します。

```python
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
%%local
import numpy as np

# PLOT
ax = sqlResults.plot(kind='scatter', figsize = (6,6), x='_1', y='_2', color='blue', alpha = 0.25, label='Actual vs. predicted');
fit = np.polyfit(sqlResults['_1'], sqlResults['_2'], deg=1)
ax.set_title('Actual vs. Predicted Tip Amounts ($)')
ax.set_xlabel("Actual")
ax.set_ylabel("Predicted")
ax.plot(sqlResults['_1'], fit[0] * sqlResults['_1'] + fit[1], color='magenta')
plt.axis([-1, 15, -1, 15])
plt.show(ax)
```

![Actual-vs-predicted-tip-amounts](./media/spark-advanced-data-exploration-modeling/actual-vs-predicted-tips.png)

## <a name="appendix-additional-regression-tasks-using-cross-validation-with-parameter-sweeps"></a>付録: クロス検証とパラメーター スイープを使用するその他の回帰タスク
この付録には、線形回帰に対して Elastic Net を使用して CV を実行する方法と、ランダム フォレスト回帰に対してカスタム コードを使用してパラメーター スイープと共に CV を実行する方法を示すコードが記載されています。

### <a name="cross-validation-using-elastic-net-for-linear-regression"></a>線形回帰に対して Elastic Net を使用したクロス検証
このセクションのコードでは、線形回帰に対して Elastic Net を使用してクロス検証を実行する方法と、テスト データに対してモデルを評価する方法を示します。

```python
###  CV USING ELASTIC NET FOR LINEAR REGRESSION

# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.ml.regression import LinearRegression
from pyspark.ml import Pipeline
from pyspark.ml.evaluation import RegressionEvaluator
from pyspark.ml.tuning import CrossValidator, ParamGridBuilder

# DEFINE ALGORITHM/MODEL
lr = LinearRegression()

# DEFINE GRID PARAMETERS
paramGrid = ParamGridBuilder().addGrid(lr.regParam, (0.01, 0.1))\
                              .addGrid(lr.maxIter, (5, 10))\
                              .addGrid(lr.tol, (1e-4, 1e-5))\
                              .addGrid(lr.elasticNetParam, (0.25,0.75))\
                              .build() 

# DEFINE PIPELINE 
# SIMPLY THE MODEL HERE, WITHOUT TRANSFORMATIONS
pipeline = Pipeline(stages=[lr])

# DEFINE CV WITH PARAMETER SWEEP
cv = CrossValidator(estimator= lr,
                    estimatorParamMaps=paramGrid,
                    evaluator=RegressionEvaluator(),
                    numFolds=3)

# CONVERT TO DATA FRAME, AS CROSSVALIDATOR WON'T RUN ON RDDS
trainDataFrame = sqlContext.createDataFrame(oneHotTRAINreg, ["features", "label"])

# TRAIN WITH CROSS-VALIDATION
cv_model = cv.fit(trainDataFrame)


# EVALUATE MODEL ON TEST SET
testDataFrame = sqlContext.createDataFrame(oneHotTESTreg, ["features", "label"])

# MAKE PREDICTIONS ON TEST DOCUMENTS
# cvModel uses the best model found (lrModel).
predictionAndLabels = cv_model.transform(testDataFrame)

# CONVERT TO DF AND SAVE REGISTER DF AS TABLE
predictionAndLabels.registerTempTable("tmp_results");

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**OUTPUT**

上記のセルの実行に要した時間: 161.21 秒

**R-SQR メトリックで評価する**

*tmp_results* は、既出のセルで Hive テーブルとして登録されています。 このテーブルからの結果を *sqlResults* データフレームに出力し、プロットに使用しています。 次にコードを示します。

```python
# SELECT RESULTS
%%sql -q -o sqlResults
SELECT label,prediction from tmp_results
```

R-sqr を計算するコードを次に示します。

```python
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
%%local
from scipy import stats

#R-SQR TEST METRIC
corstats = stats.linregress(sqlResults['label'],sqlResults['prediction'])
r2 = (corstats[2]*corstats[2])
print("R-sqr = %s" % r2)
```

**OUTPUT**

R-sqr = 0.619184907088

### <a name="cross-validation-with-parameter-sweep-using-custom-code-for-random-forest-regression"></a>ランダム フォレスト回帰に対してカスタム コードを使用したクロス検証とパラメーター スイープ
このセクションのコードでは、ランダム フォレスト回帰に対してカスタム コードを使用してクロス検証とパラメーター スイープを実行する方法と、テスト データに対してモデルを評価する方法を示します。

```python
# RECORD START TIME
timestart= datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
# GET ACCURARY FOR HYPERPARAMETERS BASED ON CROSS-VALIDATION IN TRAINING DATA-SET
from pyspark.mllib.tree import RandomForest, RandomForestModel
from pyspark.mllib.util import MLUtils
from pyspark.mllib.evaluation import RegressionMetrics
from sklearn.grid_search import ParameterGrid

## CREATE PARAMETER GRID
grid = [{'maxDepth': [5,10], 'numTrees': [25,50]}]
paramGrid = list(ParameterGrid(grid))

## SPECIFY LEVELS OF CATEGORICAL VARIBLES
categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}

# SPECIFY NUMFOLDS AND ARRAY TO HOLD METRICS
nFolds = 3;
numModels = len(paramGrid)
h = 1.0 / nFolds;
metricSum = np.zeros(numModels);

for i in range(nFolds):
    # Create training and x-validation sets
    validateLB = i * h
    validateUB = (i + 1) * h
    condition = (trainData["rand"] >= validateLB) & (trainData["rand"] < validateUB)
    validation = trainData.filter(condition)
    # Create labeled points from data-frames
    if i > 0:
        trainCVLabPt.unpersist()
        validationLabPt.unpersist()
    trainCV = trainData.filter(~condition)
    trainCVLabPt = trainCV.map(parseRowIndexingRegression)
    trainCVLabPt.cache()
    validationLabPt = validation.map(parseRowIndexingRegression)
    validationLabPt.cache()
    # For parameter sets compute metrics from x-validation
    for j in range(numModels):
        maxD = paramGrid[j]['maxDepth']
        numT = paramGrid[j]['numTrees']
        # Train logistic regression model with hypermarameter set
        rfModel = RandomForest.trainRegressor(trainCVLabPt, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                    numTrees=numT, featureSubsetStrategy="auto",
                                    impurity='variance', maxDepth=maxD, maxBins=32)
        predictions = rfModel.predict(validationLabPt.map(lambda x: x.features))
        predictionAndLabels = validationLabPt.map(lambda lp: lp.label).zip(predictions)
        # Use ROC-AUC as accuracy metrics
        validMetrics = RegressionMetrics(predictionAndLabels)
        metric = validMetrics.rootMeanSquaredError
        metricSum[j] += metric

avgAcc = metricSum/nFolds;
bestParam = paramGrid[np.argmin(avgAcc)];

# UNPERSIST OBJECTS
trainCVLabPt.unpersist()
validationLabPt.unpersist()

## TRAIN FINAL MODL WIHT BEST PARAMETERS
rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                    numTrees=bestParam['numTrees'], featureSubsetStrategy="auto",
                                    impurity='variance', maxDepth=bestParam['maxDepth'], maxBins=32)

# EVALUATE MODEL ON TEST DATA
predictions = rfModel.predict(indexedTESTreg.map(lambda x: x.features))
predictionAndLabels = indexedTESTreg.map(lambda lp: lp.label).zip(predictions)

#PRINT TEST METRICS
testMetrics = RegressionMetrics(predictionAndLabels)
print("RMSE = %s" % testMetrics.rootMeanSquaredError)
print("R-sqr = %s" % testMetrics.r2)

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**OUTPUT**

RMSE = 0.906972198262

R-sqr = 0.740751197012

上記のセルの実行に要した時間: 69.17 秒

### <a name="clean-up-objects-from-memory-and-print-model-locations"></a>メモリ内のオブジェクトのクリーンアップと、モデルの場所の出力
メモリにキャッシュされたオブジェクトを削除するには、 `unpersist()` を使用します。

```python
# UNPERSIST OBJECTS CACHED IN MEMORY

# REMOVE ORIGINAL DFs
taxi_df_train_cleaned.unpersist()
taxi_df_train_with_newFeatures.unpersist()
trainData.unpersist()
trainData.unpersist()

# FOR BINARY CLASSIFICATION TRAINING AND TESTING
indexedTRAINbinary.unpersist()
indexedTESTbinary.unpersist()
oneHotTRAINbinary.unpersist()
oneHotTESTbinary.unpersist()

# FOR REGRESSION TRAINING AND TESTING
indexedTRAINreg.unpersist()
indexedTESTreg.unpersist()
oneHotTRAINreg.unpersist()
oneHotTESTreg.unpersist()

# SCALED FEATURES
oneHotTRAINregScaled.unpersist()
oneHotTESTregScaled.unpersist()
```

**OUTPUT**

PythonRDD[122] at RDD at PythonRDD.scala: 43

\*\*consumption notebook で使用するモデル ファイルの出力パス。 \*\*独立したデータセットの取り込みとスコア付けを行うには、これらのファイル名をコピーして "Consumption notebook" に貼り付ける必要があります。

```python
# PRINT MODEL FILE LOCATIONS FOR CONSUMPTION
print "logisticRegFileLoc = modelDir + \"" + logisticregressionfilename + "\"";
print "linearRegFileLoc = modelDir + \"" + linearregressionfilename + "\"";
print "randomForestClassificationFileLoc = modelDir + \"" + rfclassificationfilename + "\"";
print "randomForestRegFileLoc = modelDir + \"" + rfregressionfilename + "\"";
print "BoostedTreeClassificationFileLoc = modelDir + \"" + btclassificationfilename + "\"";
print "BoostedTreeRegressionFileLoc = modelDir + \"" + btregressionfilename + "\"";
```

**OUTPUT**

logisticRegFileLoc = modelDir + "LogisticRegressionWithLBFGS_2016-05-0316_47_30.096528"

linearRegFileLoc = modelDir + "LinearRegressionWithSGD_2016-05-0316_51_28.433670"

randomForestClassificationFileLoc = modelDir + "RandomForestClassification_2016-05-0316_50_17.454440"

randomForestRegFileLoc = modelDir + "RandomForestRegression_2016-05-0316_51_57.331730"

BoostedTreeClassificationFileLoc = modelDir + "GradientBoostingTreeClassification_2016-05-0316_50_40.138809"

BoostedTreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression_2016-05-0316_52_18.827237"

## <a name="whats-next"></a>次の操作
Spark MlLib を使用して回帰モデルと分類モデルを作成しました。これで、これらのモデルにスコアを付け、評価する方法を学習する準備ができました。

**モデルの使用:** このトピックで作成した分類モデルと回帰モデルにスコアを付け、評価する方法については、[Spark で構築した機械学習モデルのスコア付けと評価](spark-model-consumption.md)に関するページを参照してください。
