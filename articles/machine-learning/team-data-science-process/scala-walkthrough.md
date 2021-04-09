---
title: Scala および Azure 上の Spark を使用したデータ サイエンス - Team Data Science Process
description: Azure HDInsight Spark クラスターで Spark のスケーラブルな MLlib と Spark ML パッケージを用いて、教師あり機械学習タスクに Scala を使用する方法を説明します。
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
ms.openlocfilehash: 9ae4549fe343422bbf60275a97768ca407f2dc7c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "93321373"
---
# <a name="data-science-using-scala-and-spark-on-azure"></a>Scala および Azure 上の Spark を使用したデータ サイエンス
この記事では、Azure HDInsight Spark クラスターで Spark のスケーラブルな MLlib と Spark ML パッケージを用いて、教師あり機械学習タスクに Scala を使用する方法を説明します。 また、 [データ サイエンス プロセス](./index.yml)(データの取り込みと探索、視覚化、特徴エンジニアリング、モデリング、モデルの使用) を構成するタスクについても説明します。 本記事のモデルでは、2 つの一般的な教師あり機械学習タスクに加えて、ロジスティック回帰および線形回帰、ランダム フォレスト、および勾配ブースティング ツリー (GBT) を扱います。

* 回帰問題: タクシー営業でのチップ金額 (ドル) の予測
* 二項分類: タクシー営業でチップが支払われるかどうか (1/0) の予測

モデリング プロセスには、テスト データ セットでのトレーニングと評価、および適切な精度評価基準が必要です。 この記事では、これらのモデルを Azure BLOB ストレージに保存する方法と、その予測パフォーマンスをスコア付けして評価する方法について説明します。 また、より高度なトピックとして、クロス検証およびハイパーパラメーター スイープを使用してモデルを最適化する方法についても説明します。 ここで使用しているデータは、GitHub で公開されている 2013 年の NYC タクシーの営業と料金のデータセットから抽出したサンプルです。

[Scala](https://www.scala-lang.org/)は Java 仮想マシン ベースの言語であり、オブジェクト指向の概念と関数型言語の概念を統合したものです。 クラウドでの分散処理に適したスケーラブルな言語であり、Azure Spark クラスターで実行されます。

[Spark](https://spark.apache.org/) は、ビッグ データ分析アプリケーションのパフォーマンスを高めるメモリ内処理をサポートする、オープンソースの並列処理フレームワークです。 Spark 処理エンジンは、高速かつ簡単に高度な分析を行うことができるように作成されています。 Spark のメモリ内の分散計算機能により、機械学習とグラフ計算における反復的なアルゴリズムに対して、Spark は適切な選択肢となります。 [spark.ml](https://spark.apache.org/docs/latest/ml-guide.html) パッケージでは、実用的な機械学習パイプラインの作成および調整に役立つデータ フレームを基盤とする、高レベルの API 一式が提供されます。 [MLlib](https://spark.apache.org/mllib/) はスケーラブルな Spark の機械学習ライブラリであり、これにより分散環境でもモデリング機能を使用できます。

[HDInsight Spark](../../hdinsight/spark/apache-spark-overview.md) は、Azure でホストされるオープンソースの Spark サービスです。 Spark クラスターの Jupyter Scala Notebook もサポートされており、Spark SQL の対話型クエリを実行して、Azure BLOB ストレージに保管されているデータを変換、フィルター処理、視覚化することができます。 この記事でソリューションを提供したり、関連するプロットを表示してデータを視覚化したりする Scala コード スニペットは、Spark クラスターにインストールされた Jupyter Notebook で実行されます。 各トピックのモデリング手順には、各種モデルをトレーニング、評価、保存、および使用する方法を示すコードも含まれています。

この記事のセットアップ手順およびコードは、Azure HDInsight 3.4 Spark 1.6 向けのものです。 ただし、この記事および [Scala Jupyter Notebook](https://github.com/Azure-Samples/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration-Modeling-and-Scoring-using-Scala.ipynb) のコードは汎用的であり、あらゆる Spark クラスターで機能します。 HDInsight Spark を使用していない場合、クラスターのセットアップと管理の手順は、この記事に記載されている内容と若干異なります。

> [!NOTE]
> Scala ではなく Python を使用してエンドツーエンドのデータ サイエンス プロセスのタスクを実行する方法については、 [Azure HDInsight での Spark を使用したデータ サイエンス](spark-overview.md)に関する記事をご覧ください。
> 
> 

## <a name="prerequisites"></a>前提条件
* Azure サブスクリプションが必要です。 Azure サブスクリプションがない場合は、 [Azure 無料試用版の入手](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。
* 以下の手順を完了するために、Azure HDInsight 3.4 Spark 1.6 クラスターが必要です。 クラスターの作成については、 [Azure HDInsight での Apache Spark クラスターの作成](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md)に関するページの手順を参照してください。 クラスターの種類とバージョンは、 **[Select Cluster Type (クラスターの種類の選択)]** メニューから指定します。

![HDInsight クラスターの種類の構成](./media/scala-walkthrough/spark-cluster-on-portal.png)

> [!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]
> 
> 

NYC タクシー営業データの説明と、Spark クラスターで Jupyter Notebook のコードを実行する方法については、「 [Azure HDInsight 上の Spark を使用したデータ サイエンスの概要](spark-overview.md)」の関連するセクションをご覧ください。  

## <a name="execute-scala-code-from-a-jupyter-notebook-on-the-spark-cluster"></a>Spark クラスターで Jupyter Notebook の Scala コードを実行する
Jupyter Notebook は Azure ポータルから起動できます。 ダッシュボードで Spark クラスターを見つけてクリックし、クラスターの管理ページにアクセスします。 **[クラスター ダッシュボード]**、**[Jupyter Notebook]** の順にクリックして、Spark クラスターに関連付けられている Notebook を開きます。

![クラスター ダッシュボードと Jupyter Notebook](./media/scala-walkthrough/spark-jupyter-on-portal.png)

Jupyter Notebook には、https://&lt;クラスター名&gt;.azurehdinsight.net/jupyter でアクセスすることもできます。 *clustername* はクラスターの名前に置き換えてください。 Jupyter Notebook にアクセスするには、管理者アカウントのパスワードが必要です。

![クラスター名による Jupyter Notebook へのアクセス](./media/scala-walkthrough/spark-jupyter-notebook.png)

**[Scala]** を選択すると、PySpark API を使用したいくつかのパッケージ済みノートブックのサンプルが含まれるディレクトリが表示されます。 [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/Scala)で、この記事の Spark に関する一連のトピックのコード サンプルが含まれる Exploration-Modeling-and-Scoring-using-Scala.ipynb ノートブックを入手できます。

GitHub から Spark クラスター上の Jupyter Notebook サーバーに Notebook を直接アップロードできます。 Jupyter のホーム ページで、 **[Upload]** ボタンをクリックします。 エクスプローラーで Scala Notebook の GitHub (raw コンテンツ) URL を貼り付け、 **[開く]** をクリックします。 Scala Notebook は次の URL で入手できます。

[Exploration-Modeling-and-Scoring-using-Scala.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration-Modeling-and-Scoring-using-Scala.ipynb)

## <a name="setup-preset-spark-and-hive-contexts-spark-magics-and-spark-libraries"></a>セットアップ: プリセットの Spark および Hive コンテキスト、Spark マジック、Spark ライブラリ
### <a name="preset-spark-and-hive-contexts"></a>プリセットの Spark および Hive コンテキスト

```scala
# SET THE START TIME
import java.util.Calendar
val beginningTime = Calendar.getInstance().getTime()
```

Jupyter Notebook に付属する Spark カーネルには、プリセットのコンテキストが用意されています。 開発中のアプリケーションを操作するのに、Spark コンテキストまたは Hive コンテキストを明示的に設定する必要はありません。 プリセットのコンテキストは次のとおりです。

* `sc` : SparkContext 向け
* `sqlContext` : HiveContext 向け

### <a name="spark-magics"></a>Spark マジック
Spark カーネルには定義済みの "マジック" が用意されています。マジックは、`%%` で呼び出すことができる特殊なコマンドです。 以下の各コード サンプルでは、こうしたコマンドのうちの 2 つを使用しています。

* `%%local`: 後続行のコードをローカルで実行するように指定します。 コードは有効な Scala コードである必要があります。
* `%%sql -o <variable name>`: `sqlContext` に対して Hive クエリを実行します。 `-o` パラメーターを渡すと、クエリの結果が Spark データ フレームとして `%%local` Scala コンテキストで永続化されます。

Jupyter Notebook のカーネルと、`%%` で呼び出すことができる定義済みの "マジック" (例: `%%local`) の詳細については、「[HDInsight の HDInsight Spark Linux クラスターと Jupyter Notebook で使用可能なカーネル](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md)」をご覧ください。

### <a name="import-libraries"></a>ライブラリをインポートする
次のコードを使用して、Spark や MLlib などの必要なライブラリをインポートします。

```scala
# IMPORT SPARK AND JAVA LIBRARIES
import org.apache.spark.sql.SQLContext
import org.apache.spark.sql.functions._
import java.text.SimpleDateFormat
import java.util.Calendar
import sqlContext.implicits._
import org.apache.spark.sql.Row

# IMPORT SPARK SQL FUNCTIONS
import org.apache.spark.sql.types.{StructType, StructField, StringType, IntegerType, FloatType, DoubleType}
import org.apache.spark.sql.functions.rand

# IMPORT SPARK ML FUNCTIONS
import org.apache.spark.ml.Pipeline
import org.apache.spark.ml.feature.{StringIndexer, VectorAssembler, OneHotEncoder, VectorIndexer, Binarizer}
import org.apache.spark.ml.tuning.{ParamGridBuilder, TrainValidationSplit, CrossValidator}
import org.apache.spark.ml.regression.{LinearRegression, LinearRegressionModel, RandomForestRegressor, RandomForestRegressionModel, GBTRegressor, GBTRegressionModel}
import org.apache.spark.ml.classification.{LogisticRegression, LogisticRegressionModel, RandomForestClassifier, RandomForestClassificationModel, GBTClassifier, GBTClassificationModel}
import org.apache.spark.ml.evaluation.{BinaryClassificationEvaluator, RegressionEvaluator, MulticlassClassificationEvaluator}

# IMPORT SPARK MLLIB FUNCTIONS
import org.apache.spark.mllib.linalg.{Vector, Vectors}
import org.apache.spark.mllib.util.MLUtils
import org.apache.spark.mllib.classification.{LogisticRegressionWithLBFGS, LogisticRegressionModel}
import org.apache.spark.mllib.regression.{LabeledPoint, LinearRegressionWithSGD, LinearRegressionModel}
import org.apache.spark.mllib.tree.{GradientBoostedTrees, RandomForest}
import org.apache.spark.mllib.tree.configuration.BoostingStrategy
import org.apache.spark.mllib.tree.model.{GradientBoostedTreesModel, RandomForestModel, Predict}
import org.apache.spark.mllib.evaluation.{BinaryClassificationMetrics, MulticlassMetrics, RegressionMetrics}

# SPECIFY SQLCONTEXT
val sqlContext = new SQLContext(sc)
```

## <a name="data-ingestion"></a>データ インジェスト
データ サイエンス プロセスではまず、分析するデータを取り込みます。 データは、データが保管されている外部のソースまたはシステムからデータ探索およびモデリング環境に読み込みます。 この記事で取り込むデータは、タクシーの営業ファイルおよび料金ファイル (.tsv ファイルとして保存) を結合した 0.1% サンプルです。 データ探索およびモデリング環境は Spark です。 このセクションでは、次の一連のタスクを実行するコードを示します。

1. データおよびモデル ストレージのディレクトリ パスを設定する。
2. 入力データセット (.tsv ファイルとして保存) を読み取る。
3. データのスキーマを定義し、データをクリーニングする。
4. クリーニングしたデータ フレームを作成し、メモリにキャッシュする。
5. データを SQLContext に一時テーブルとして登録する。
6. テーブルに対してクエリを実行し、結果をデータ フレームにインポートする。

### <a name="set-directory-paths-for-storage-locations-in-azure-blob-storage"></a>Azure BLOB ストレージ内のストレージの場所のディレクトリ パスを設定する
Spark は、Azure BLOB ストレージの読み取りおよび書き込みを行うことができます。 Spark を使用して任意の既存データを処理してから、結果を BLOB ストレージに再び保存することができます。

BLOB ストレージにモデルまたはファイルを保存するには、パスを適切に指定する必要があります。 Spark クラスターに接続されている既定のコンテナーは、 `wasb:///`で参照できます。 他の場所は、 `wasb://`で参照できます。

次のコード サンプルでは、読み取る入力データの場所と、Spark クラスターに接続された、モデルの保存先となる BLOB ストレージのパスを指定しています。

```scala
# SET PATHS TO DATA AND MODEL FILE LOCATIONS
# INGEST DATA AND SPECIFY HEADERS FOR COLUMNS
val taxi_train_file = sc.textFile("wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv")
val header = taxi_train_file.first;

# SET THE MODEL STORAGE DIRECTORY PATH
# NOTE THAT THE FINAL BACKSLASH IN THE PATH IS REQUIRED.
val modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/";
```

### <a name="import-data-create-an-rdd-and-define-a-data-frame-according-to-the-schema"></a>データをインポートして RDD を作成し、スキーマに従ってデータ フレームを定義する

```scala
# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# DEFINE THE SCHEMA BASED ON THE HEADER OF THE FILE
val sqlContext = new SQLContext(sc)
val taxi_schema = StructType(
    Array(
        StructField("medallion", StringType, true),
        StructField("hack_license", StringType, true),
        StructField("vendor_id", StringType, true),
        StructField("rate_code", DoubleType, true),
        StructField("store_and_fwd_flag", StringType, true),
        StructField("pickup_datetime", StringType, true),
        StructField("dropoff_datetime", StringType, true),
        StructField("pickup_hour", DoubleType, true),
        StructField("pickup_week", DoubleType, true),
        StructField("weekday", DoubleType, true),
        StructField("passenger_count", DoubleType, true),
        StructField("trip_time_in_secs", DoubleType, true),
        StructField("trip_distance", DoubleType, true),
        StructField("pickup_longitude", DoubleType, true),
        StructField("pickup_latitude", DoubleType, true),
        StructField("dropoff_longitude", DoubleType, true),
        StructField("dropoff_latitude", DoubleType, true),
        StructField("direct_distance", StringType, true),
        StructField("payment_type", StringType, true),
        StructField("fare_amount", DoubleType, true),
        StructField("surcharge", DoubleType, true),
        StructField("mta_tax", DoubleType, true),
        StructField("tip_amount", DoubleType, true),
        StructField("tolls_amount", DoubleType, true),
        StructField("total_amount", DoubleType, true),
        StructField("tipped", DoubleType, true),
        StructField("tip_class", DoubleType, true)
        )
    )

# CAST VARIABLES ACCORDING TO THE SCHEMA
val taxi_temp = (taxi_train_file.map(_.split("\t"))
                        .filter((r) => r(0) != "medallion")
                         .map(p => Row(p(0), p(1), p(2),
                            p(3).toDouble, p(4), p(5), p(6), p(7).toDouble, p(8).toDouble, p(9).toDouble, p(10).toDouble,
                            p(11).toDouble, p(12).toDouble, p(13).toDouble, p(14).toDouble, p(15).toDouble, p(16).toDouble,
                            p(17), p(18), p(19).toDouble, p(20).toDouble, p(21).toDouble, p(22).toDouble,
                            p(23).toDouble, p(24).toDouble, p(25).toDouble, p(26).toDouble)))


# CREATE AN INITIAL DATA FRAME AND DROP COLUMNS, AND THEN CREATE A CLEANED DATA FRAME BY FILTERING FOR UNWANTED VALUES OR OUTLIERS
val taxi_train_df = sqlContext.createDataFrame(taxi_temp, taxi_schema)

val taxi_df_train_cleaned = (taxi_train_df.drop(taxi_train_df.col("medallion"))
        .drop(taxi_train_df.col("hack_license")).drop(taxi_train_df.col("store_and_fwd_flag"))
        .drop(taxi_train_df.col("pickup_datetime")).drop(taxi_train_df.col("dropoff_datetime"))
        .drop(taxi_train_df.col("pickup_longitude")).drop(taxi_train_df.col("pickup_latitude"))
        .drop(taxi_train_df.col("dropoff_longitude")).drop(taxi_train_df.col("dropoff_latitude"))
        .drop(taxi_train_df.col("surcharge")).drop(taxi_train_df.col("mta_tax"))
        .drop(taxi_train_df.col("direct_distance")).drop(taxi_train_df.col("tolls_amount"))
        .drop(taxi_train_df.col("total_amount")).drop(taxi_train_df.col("tip_class"))
        .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200"));

# CACHE AND MATERIALIZE THE CLEANED DATA FRAME IN MEMORY
taxi_df_train_cleaned.cache()
taxi_df_train_cleaned.count()

# REGISTER THE DATA FRAME AS A TEMPORARY TABLE IN SQLCONTEXT
taxi_df_train_cleaned.registerTempTable("taxi_train")

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");
```

**出力:**

セルの実行時間: 8 秒。

### <a name="query-the-table-and-import-results-in-a-data-frame"></a>テーブルに対してクエリを実行し、結果をデータ フレームにインポートする
次に、テーブルに対してクエリを実行して料金、乗客、チップのデータを取得し、破損したデータや範囲外のデータを除外して、複数の行を出力します。

```scala
# QUERY THE DATA
val sqlStatement = """
    SELECT fare_amount, passenger_count, tip_amount, tipped
    FROM taxi_train
    WHERE passenger_count > 0 AND passenger_count < 7
    AND fare_amount > 0 AND fare_amount < 200
    AND payment_type in ('CSH', 'CRD')
    AND tip_amount > 0 AND tip_amount < 25
"""
val sqlResultsDF = sqlContext.sql(sqlStatement)

# SHOW ONLY THE TOP THREE ROWS
sqlResultsDF.show(3)
```

**出力:**

| fare_amount | passenger_count | tip_amount | tipped |
| --- | --- | --- | --- |
|        13.5 |1.0 |2.9 |1.0 |
|        16.0 |2.0 |3.4 |1.0 |
|        10.5 |2.0 |1.0 |1.0 |

## <a name="data-exploration-and-visualization"></a>データの探索と視覚化
データを Spark に取り込んだら、次に、探索と視覚化によってデータに関する理解を深めます。 このセクションでは、SQL クエリを使用してタクシーのデータを調べます。 次に、結果をデータ フレームにインポートし、Jupyter の自動視覚化機能を使用してターゲット変数と予想される特徴をプロットし、視覚化してチェックします。

### <a name="use-local-and-sql-magic-to-plot-data"></a>ローカル マジックと SQL マジックを使用してデータをプロットする
既定では、Jupyter Notebook から実行するコード スニペットの出力は、ワーカー ノードで永続化されるセッションのコンテキスト内で使用できます。 計算を実行するたびに乗車データをワーカー ノードに保存する場合や、計算に必要なすべてのデータが Jupyter サーバー ノード (ヘッド ノード) でローカルで使用可能な場合は、 `%%local` マジックを使用して Jupyter サーバー上でコード スニペットを実行できます。

* **SQL マジック** (`%%sql`): HDInsight Spark カーネルは、SQLContext に対する簡単なインライン HiveQL クエリをサポートしています。 引数 (`-o VARIABLE_NAME`) を指定すると、SQL クエリの出力結果が Pandas データ フレームとして Jupyter サーバー上に永続化されます。 この設定は、出力をローカル モードで使用できることを意味します。
* `%%local` **マジック**。 `%%local`Jupyter サーバー (HDInsight クラスターのヘッド ノード) でコードをローカルに実行します。 通常、`%%local` マジックは、`-o` パラメーターを指定した `%%sql` マジックと組み合わせて使用します。 SQL クエリの出力結果を `-o` パラメーターでローカルに永続化したうえで、`%%local` マジックを使用すると、それに続く一連のコード スニペットが、ローカルに永続化されている SQL クエリの出力結果に対してローカルに実行されます。

### <a name="query-the-data-by-using-sql"></a>SQL を使用してデータを照会する
このクエリでは、タクシーの営業について、料金、乗客数、チップの金額を取得します。

```scala
# RUN THE SQL QUERY
%%sql -q -o sqlResults
SELECT fare_amount, passenger_count, tip_amount, tipped FROM taxi_train WHERE passenger_count > 0 AND passenger_count < 7 AND fare_amount > 0 AND fare_amount < 200 AND payment_type in ('CSH', 'CRD') AND tip_amount > 0 AND tip_amount < 25
```

次のコードでは、 `%%local` マジックで sqlResults というローカル データ フレームを作成します。 sqlResults は、matplotlib でのプロットに使用できます。

> [!TIP]
> この記事では、%%local マジックを何度も使用しています。 データ セットが大きい場合は、サンプリングしてローカル メモリに収まるデータ フレームを作成してください。
> 
> 

### <a name="plot-the-data"></a>データをプロットする
データ フレームを Pandas データ フレームとしてローカル コンテキストに配置したら、Python コードを使用してプロットできます。

```scala
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER
%%local

# USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES.
# CLICK THE TYPE OF PLOT TO GENERATE (LINE, AREA, BAR, ETC.)
sqlResults
```

 コードの実行後、Spark カーネルによって SQL (HiveQL) クエリの出力が自動的に視覚化されます。 視覚化は次の種類から選択できます。

* テーブル
* 円
* 行
* 面
* 棒

データをプロットするコードを次に示します。

```scala
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
%%local
import matplotlib.pyplot as plt
%matplotlib inline

# PLOT TIP BY PAYMENT TYPE AND PASSENGER COUNT
ax1 = sqlResults[['tip_amount']].plot(kind='hist', bins=25, facecolor='lightblue')
ax1.set_title('Tip amount distribution')
ax1.set_xlabel('Tip Amount ($)')
ax1.set_ylabel('Counts')
plt.suptitle('')
plt.show()

# PLOT TIP BY PASSENGER COUNT
ax2 = sqlResults.boxplot(column=['tip_amount'], by=['passenger_count'])
ax2.set_title('Tip amount by Passenger count')
ax2.set_xlabel('Passenger count')
ax2.set_ylabel('Tip Amount ($)')
plt.suptitle('')
plt.show()

# PLOT TIP AMOUNT BY FARE AMOUNT; SCALE POINTS BY PASSENGER COUNT
ax = sqlResults.plot(kind='scatter', x= 'fare_amount', y = 'tip_amount', c='blue', alpha = 0.10, s=5*(sqlResults.passenger_count))
ax.set_title('Tip amount by Fare amount')
ax.set_xlabel('Fare Amount ($)')
ax.set_ylabel('Tip Amount ($)')
plt.axis([-2, 80, -2, 20])
plt.show()
```

**出力:**

![チップの金額のヒストグラム](./media/scala-walkthrough/plot-tip-amount-histogram.png)

![乗客数別チップの金額](./media/scala-walkthrough/plot-tip-amount-by-passenger-count.png)

![料金別チップの金額](./media/scala-walkthrough/plot-tip-amount-by-fare-amount.png)

## <a name="create-features-and-transform-features-and-then-prep-data-for-input-into-modeling-functions"></a>特徴を作成および変換して、モデリング関数への入力用データを準備する
Spark ML や MLlib のツリーベースのモデリング関数では、ビン分割、インデックス作成、ワンホット エンコード、ベクトル化などさまざまな手法を使用してターゲットと特徴を準備する必要があります。 このセクションでは、次の手順について説明します。

1. 時間を乗車時間のバケットに **ビン分割** して新しい特徴を作成する。
2. カテゴリの特徴に対して **インデックス作成とワンホット エンコード** を適用する。
3. **データ セットのサンプリングを作成し、トレーニング用とテスト用に分ける** 。
4. **トレーニング変数と特徴を指定し**、インデックス付きまたはワンホット エンコードされたトレーニング用およびテスト用の入力ラベル付きポイント耐障害性分散データセット (RDD) またはデータ フレームを作成する。
5. 機械学習モデルの入力として使用する **特徴とターゲットの分類およびベクター化** を自動的に実行する。

### <a name="create-a-new-feature-by-binning-hours-into-traffic-time-buckets"></a>時間を乗車時間のバケットにビン分割して新しい特徴を作成する
次のコードでは、時間を乗車時間のバケットにビン分割して新しい特徴を作成する方法と、結果として生成されたデータ フレームをメモリにキャッシュする方法を示します。 RDD とデータ フレームを繰り返し使用する場合、キャッシュを使用することで実行時間を短縮できます。 そのため、以下の手順ではさまざまな段階で RDD とデータ フレームをキャッシュしています。

```scala
# CREATE FOUR BUCKETS FOR TRAFFIC TIMES
val sqlStatement = """
    SELECT *,
    CASE
     WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night"
     WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush"
     WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
     WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
    END as TrafficTimeBins
    FROM taxi_train
"""
val taxi_df_train_with_newFeatures = sqlContext.sql(sqlStatement)

# CACHE THE DATA FRAME IN MEMORY AND MATERIALIZE THE DATA FRAME IN MEMORY
taxi_df_train_with_newFeatures.cache()
taxi_df_train_with_newFeatures.count()
```

### <a name="indexing-and-one-hot-encoding-of-categorical-features"></a>カテゴリの特徴のインデックス作成とワンホット エンコードを実行する
MLlib のモデリング関数と予測関数では、特徴のカテゴリ入力データを使用する前に、データのインデックスを作成するか、データをエンコードする必要があります。 ここでは、モデリング関数への入力用に、カテゴリの特徴のインデックスを作成するか、特徴をエンコードする方法について説明します。

モデルの応じて、さまざまな方法でモデルのインデックス化またはエンコードを実行する必要があります。 たとえば、ロジスティック回帰モデルと線形回帰モデルでは、ワンホット エンコードを行う必要があります。 特徴に 3 つのカテゴリがある場合は、3 つの特徴列に展開できます。 各列には、観測のカテゴリに応じて 0 または 1 が含まれます。 MLlib には、ワンホット エンコードを実行する [OneHotEncoder](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) 関数が用意されています。 このエンコーダーは、ラベル インデックスの列をバイナリ ベクトルの列にマップします。この列に含まれる値は最大でも 1 つだけです。 このエンコードにより、特徴を数値化する必要があるアルゴリズム (ロジスティック回帰など) をカテゴリの特徴に適用できます。

ここでは、例を示すために文字列である 4 つの変数だけを変換します。 数値で表される他の変数 (weekday など) で、カテゴリ変数としてインデックスを作成することもできます。

インデックス作成には MLlib の `OneHotEncoder()` 関数を使用し、ワンホット エンコードには `StringIndexer()` 関数を使用します。 カテゴリの特徴のインデックス作成とエンコードを実行するコードを次に示します。

```scala
# CREATE INDEXES AND ONE-HOT ENCODED VECTORS FOR SEVERAL CATEGORICAL FEATURES

# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# INDEX AND ENCODE VENDOR_ID
val stringIndexer = new StringIndexer().setInputCol("vendor_id").setOutputCol("vendorIndex").fit(taxi_df_train_with_newFeatures)
val indexed = stringIndexer.transform(taxi_df_train_with_newFeatures)
val encoder = new OneHotEncoder().setInputCol("vendorIndex").setOutputCol("vendorVec")
val encoded1 = encoder.transform(indexed)

# INDEX AND ENCODE RATE_CODE
val stringIndexer = new StringIndexer().setInputCol("rate_code").setOutputCol("rateIndex").fit(encoded1)
val indexed = stringIndexer.transform(encoded1)
val encoder = new OneHotEncoder().setInputCol("rateIndex").setOutputCol("rateVec")
val encoded2 = encoder.transform(indexed)

# INDEX AND ENCODE PAYMENT_TYPE
val stringIndexer = new StringIndexer().setInputCol("payment_type").setOutputCol("paymentIndex").fit(encoded2)
val indexed = stringIndexer.transform(encoded2)
val encoder = new OneHotEncoder().setInputCol("paymentIndex").setOutputCol("paymentVec")
val encoded3 = encoder.transform(indexed)

# INDEX AND TRAFFIC TIME BINS
val stringIndexer = new StringIndexer().setInputCol("TrafficTimeBins").setOutputCol("TrafficTimeBinsIndex").fit(encoded3)
val indexed = stringIndexer.transform(encoded3)
val encoder = new OneHotEncoder().setInputCol("TrafficTimeBinsIndex").setOutputCol("TrafficTimeBinsVec")
val encodedFinal = encoder.transform(indexed)

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");
```

**出力:**

セルの実行時間: 4 秒。

### <a name="sample-and-split-the-data-set-into-training-and-test-fractions"></a>データ セットのサンプリングを作成し、トレーニング用とテスト用に分ける
次のコードでは、データのランダム サンプリングを作成します (この例では 25%)。 この例のデータ セットのサイズでは、ランダム サンプリングを作成する必要はありませんが、必要に応じて独自の問題にランダム サンプリングを使用する方法がわかるように、この記事ではサンプリングの方法を示します。 サンプル数が多い場合、ランダム サンプリングを作成することで、モデルのトレーニングにかかる時間を大幅に節約できます。 次に、分類および回帰モデリングで使用するために、サンプルをトレーニング用 (この例では 75%) とテスト用 (この例では 25%) に分けます。

("rand" 列の) 各行には、トレーニング中にクロス検証のフォールドの選択に使用できる乱数 (0 ～ 1) を追加します。

```scala
# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# SPECIFY SAMPLING AND SPLITTING FRACTIONS
val samplingFraction = 0.25;
val trainingFraction = 0.75;
val testingFraction = (1-trainingFraction);
val seed = 1234;
val encodedFinalSampledTmp = encodedFinal.sample(withReplacement = false, fraction = samplingFraction, seed = seed)
val sampledDFcount = encodedFinalSampledTmp.count().toInt

val generateRandomDouble = udf(() => {
    scala.util.Random.nextDouble
})

# ADD A RANDOM NUMBER FOR CROSS-VALIDATION
val encodedFinalSampled = encodedFinalSampledTmp.withColumn("rand", generateRandomDouble());

# SPLIT THE SAMPLED DATA FRAME INTO TRAIN AND TEST, WITH A RANDOM COLUMN ADDED FOR DOING CROSS-VALIDATION (SHOWN LATER)
# INCLUDE A RANDOM COLUMN FOR CREATING CROSS-VALIDATION FOLDS
val splits = encodedFinalSampled.randomSplit(Array(trainingFraction, testingFraction), seed = seed)
val trainData = splits(0)
val testData = splits(1)

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");
```

**出力:**

セルの実行時間: 2 秒。

### <a name="specify-training-variable-and-features-and-then-create-indexed-or-one-hot-encoded-training-and-testing-input-labeled-point-rdds-or-data-frames"></a>トレーニング変数と特徴を指定し、インデックス付きまたはワンホット エンコードされたトレーニング用およびテスト用の入力ラベル付きポイント RDD またはデータ フレームを作成する
このセクションのコードでは、ラベル付きポイント データ型としてカテゴリ テキスト データのインデックスを作成し、MLlib ロジスティック回帰モデルや他の分類モデルのトレーニングとテストに使用できるように、カテゴリ テキスト データをエンコードする方法を示します。 ラベル付きポイント オブジェクトは、MLlib のほとんどの機械学習アルゴリズムで入力データとして必要とされる方法でフォーマットされた RDD です。 [ラベル付きポイント](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) は、ラベル/応答に関連付けられたローカル ベクトル (密または疎) です。

次のコードでは、モデルのトレーニングに使用するターゲット (従属) 変数と特徴を指定します。 指定後、インデックス付きまたはワンホット エンコードされたトレーニング用およびテスト用の入力ラベル付き RDD またはデータ フレームを作成します。

```scala
# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# MAP NAMES OF FEATURES AND TARGETS FOR CLASSIFICATION AND REGRESSION PROBLEMS
val featuresIndOneHot = List("paymentVec", "vendorVec", "rateVec", "TrafficTimeBinsVec", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount").map(encodedFinalSampled.columns.indexOf(_))
val featuresIndIndex = List("paymentIndex", "vendorIndex", "rateIndex", "TrafficTimeBinsIndex", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount").map(encodedFinalSampled.columns.indexOf(_))

# SPECIFY THE TARGET FOR CLASSIFICATION ('tipped') AND REGRESSION ('tip_amount') PROBLEMS
val targetIndBinary = List("tipped").map(encodedFinalSampled.columns.indexOf(_))
val targetIndRegression = List("tip_amount").map(encodedFinalSampled.columns.indexOf(_))

# CREATE INDEXED LABELED POINT RDD OBJECTS
val indexedTRAINbinary = trainData.rdd.map(r => LabeledPoint(r.getDouble(targetIndBinary(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
val indexedTESTbinary = testData.rdd.map(r => LabeledPoint(r.getDouble(targetIndBinary(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
val indexedTRAINreg = trainData.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
val indexedTESTreg = testData.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))

# CREATE INDEXED DATA FRAMES THAT YOU CAN USE TO TRAIN BY USING SPARK ML FUNCTIONS
val indexedTRAINbinaryDF = indexedTRAINbinary.toDF()
val indexedTESTbinaryDF = indexedTESTbinary.toDF()
val indexedTRAINregDF = indexedTRAINreg.toDF()
val indexedTESTregDF = indexedTESTreg.toDF()

# CREATE ONE-HOT ENCODED (VECTORIZED) DATA FRAMES THAT YOU CAN USE TO TRAIN BY USING SPARK ML FUNCTIONS
val assemblerOneHot = new VectorAssembler().setInputCols(Array("paymentVec", "vendorVec", "rateVec", "TrafficTimeBinsVec", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount")).setOutputCol("features")
val OneHotTRAIN = assemblerOneHot.transform(trainData)
val OneHotTEST = assemblerOneHot.transform(testData)

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");
```

**出力:**

セルの実行時間: 4 秒。

### <a name="automatically-categorize-and-vectorize-features-and-targets-to-use-as-inputs-for-machine-learning-models"></a>機械学習モデルの入力として使用する特徴とターゲットの分類およびベクター化を自動的に実行する
Spark ML を使用して、ツリーベースのモデリング関数で使用するターゲットと特徴を分類します。 コードでは次の 2 つのタスクを実行します。

* しきい値として 0.5 を使用して、0 ～ 1 の各データ ポイントに値 0 または 1 を割り当てることで、分類の二項ターゲットを作成します。
* 特徴を自動的に分類します。 いずれかの特徴について異なる数値の数が 32 個未満の場合、その特徴は分類されます。

これら 2 つのタスクのコードを次に示します。

```scala
# CATEGORIZE FEATURES AND BINARIZE THE TARGET FOR THE BINARY CLASSIFICATION PROBLEM

# TRAIN DATA
val indexer = new VectorIndexer().setInputCol("features").setOutputCol("featuresCat").setMaxCategories(32)
val indexerModel = indexer.fit(indexedTRAINbinaryDF)
val indexedTrainwithCatFeat = indexerModel.transform(indexedTRAINbinaryDF)
val binarizer: Binarizer = new Binarizer().setInputCol("label").setOutputCol("labelBin").setThreshold(0.5)
val indexedTRAINwithCatFeatBinTarget = binarizer.transform(indexedTrainwithCatFeat)

# TEST DATA
val indexerModel = indexer.fit(indexedTESTbinaryDF)
val indexedTrainwithCatFeat = indexerModel.transform(indexedTESTbinaryDF)
val binarizer: Binarizer = new Binarizer().setInputCol("label").setOutputCol("labelBin").setThreshold(0.5)
val indexedTESTwithCatFeatBinTarget = binarizer.transform(indexedTrainwithCatFeat)

# CATEGORIZE FEATURES FOR THE REGRESSION PROBLEM
# CREATE PROPERLY INDEXED AND CATEGORIZED DATA FRAMES FOR TREE-BASED MODELS

# TRAIN DATA
val indexer = new VectorIndexer().setInputCol("features").setOutputCol("featuresCat").setMaxCategories(32)
val indexerModel = indexer.fit(indexedTRAINregDF)
val indexedTRAINwithCatFeat = indexerModel.transform(indexedTRAINregDF)

# TEST DATA
val indexerModel = indexer.fit(indexedTESTbinaryDF)
val indexedTESTwithCatFeat = indexerModel.transform(indexedTESTregDF)
```


## <a name="binary-classification-model-predict-whether-a-tip-should-be-paid"></a>二項分類モデル: チップが支払われるかどうかを予測する
このセクションでは、チップが支払われるかどうかを予測する、次の 3 種類の二項分類モデルを作成します。

* Spark ML の `LogisticRegression()` 関数を使用した **ロジスティック回帰モデル**
* Spark ML の `RandomForestClassifier()` 関数を使用した **ランダム フォレスト分類モデル**
* MLlib の `GradientBoostedTrees()` 関数を使用した **勾配ブースティング ツリー分類モデル**

### <a name="create-a-logistic-regression-model"></a>ロジスティック回帰モデルを作成する
次に、Spark ML の `LogisticRegression()` 関数を使用してロジスティック回帰モデルを作成します。 次の一連の手順で、このモデルの構築コードを作成します。

1. **モデルをトレーニングする**
2. **モデルを評価する**
3. **モデルを保存する**
4. **モデルにスコアを付ける**
5. **結果をプロットする**

これらの手順のコードを次に示します。

```scala
# CREATE A LOGISTIC REGRESSION MODEL
val lr = new LogisticRegression().setLabelCol("tipped").setFeaturesCol("features").setMaxIter(10).setRegParam(0.3).setElasticNetParam(0.8)
val lrModel = lr.fit(OneHotTRAIN)

# PREDICT ON THE TEST DATA SET
val predictions = lrModel.transform(OneHotTEST)

# SELECT `BinaryClassificationEvaluator()` TO COMPUTE THE TEST ERROR
val evaluator = new BinaryClassificationEvaluator().setLabelCol("tipped").setRawPredictionCol("probability").setMetricName("areaUnderROC")
val ROC = evaluator.evaluate(predictions)
println("ROC on test data = " + ROC)

# SAVE THE MODEL
val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
val modelName = "LogisticRegression__"
val filename = modelDir.concat(modelName).concat(datestamp)
lrModel.save(filename);
```

結果を読み込み、スコアを付けて保存します。

```scala
# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# LOAD THE SAVED MODEL AND SCORE THE TEST DATA SET
val savedModel = org.apache.spark.ml.classification.LogisticRegressionModel.load(filename)
println(s"Coefficients: ${savedModel.coefficients} Intercept: ${savedModel.intercept}")

# SCORE THE MODEL ON THE TEST DATA
val predictions = savedModel.transform(OneHotTEST).select("tipped","probability","rawPrediction")
predictions.registerTempTable("testResults")

# SELECT `BinaryClassificationEvaluator()` TO COMPUTE THE TEST ERROR
val evaluator = new BinaryClassificationEvaluator().setLabelCol("tipped").setRawPredictionCol("probability").setMetricName("areaUnderROC")
val ROC = evaluator.evaluate(predictions)

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");

# PRINT THE ROC RESULTS
println("ROC on test data = " + ROC)
```

**出力:**

テスト データの ROC = 0.9827381497557599

ローカルの Pandas データ フレームで Python を使用して、ROC 曲線をプロットします。

```scala
# QUERY THE RESULTS
%%sql -q -o sqlResults
SELECT tipped, probability from testResults


# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
%%local
%matplotlib inline
from sklearn.metrics import roc_curve,auc

sqlResults['probFloat'] = sqlResults.apply(lambda row: row['probability'].values()[0][1], axis=1)
predictions_pddf = sqlResults[["tipped","probFloat"]]

# PREDICT THE ROC CURVE
# predictions_pddf = sqlResults.rename(columns={'_1': 'probability', 'tipped': 'label'})
prob = predictions_pddf["probFloat"]
fpr, tpr, thresholds = roc_curve(predictions_pddf['tipped'], prob, pos_label=1);
roc_auc = auc(fpr, tpr)

# PLOT THE ROC CURVE
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

**出力:**

![チップの有無に関する ROC 曲線](./media/scala-walkthrough/plot-roc-curve-tip-or-not.png)

### <a name="create-a-random-forest-classification-model"></a>ランダム フォレスト分類モデルを作成する
次に、Spark ML の `RandomForestClassifier()` 関数を使用してランダム フォレスト分類モデルを作成し、テスト データでモデルを評価します。

```scala
# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# CREATE THE RANDOM FOREST CLASSIFIER MODEL
val rf = new RandomForestClassifier().setLabelCol("labelBin").setFeaturesCol("featuresCat").setNumTrees(10).setSeed(1234)

# FIT THE MODEL
val rfModel = rf.fit(indexedTRAINwithCatFeatBinTarget)
val predictions = rfModel.transform(indexedTESTwithCatFeatBinTarget)

# EVALUATE THE MODEL
val evaluator = new MulticlassClassificationEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("f1")
val Test_f1Score = evaluator.evaluate(predictions)
println("F1 score on test data: " + Test_f1Score);

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");

# CALCULATE BINARY CLASSIFICATION EVALUATION METRICS
val evaluator = new BinaryClassificationEvaluator().setLabelCol("label").setRawPredictionCol("probability").setMetricName("areaUnderROC")
val ROC = evaluator.evaluate(predictions)
println("ROC on test data = " + ROC)
```

**出力:**

テスト データの ROC = 0.9847103571552683

### <a name="create-a-gbt-classification-model"></a>GBT 分類モデルを作成する
次に、MLib の `GradientBoostedTrees()` 関数を使用して GBT フォレスト分類モデルを作成し、テスト データでモデルを評価します。

```scala
# TRAIN A GBT CLASSIFICATION MODEL BY USING MLLIB AND A LABELED POINT

# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# DEFINE THE GBT CLASSIFICATION MODEL
val boostingStrategy = BoostingStrategy.defaultParams("Classification")
boostingStrategy.numIterations = 20
boostingStrategy.treeStrategy.numClasses = 2
boostingStrategy.treeStrategy.maxDepth = 5
boostingStrategy.treeStrategy.categoricalFeaturesInfo = Map[Int, Int]((0,2),(1,2),(2,6),(3,4))

# TRAIN THE MODEL
val gbtModel = GradientBoostedTrees.train(indexedTRAINbinary, boostingStrategy)

# SAVE THE MODEL IN BLOB STORAGE
val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
val modelName = "GBT_Classification__"
val filename = modelDir.concat(modelName).concat(datestamp)
gbtModel.save(sc, filename);

# EVALUATE THE MODEL ON TEST INSTANCES AND THE COMPUTE TEST ERROR
val labelAndPreds = indexedTESTbinary.map { point =>
  val prediction = gbtModel.predict(point.features)
  (point.label, prediction)
}
val testErr = labelAndPreds.filter(r => r._1 != r._2).count.toDouble / indexedTRAINbinary.count()
//println("Learned classification GBT model:\n" + gbtModel.toDebugString)
println("Test Error = " + testErr)

# USE BINARY AND MULTICLASS METRICS TO EVALUATE THE MODEL ON THE TEST DATA
val metrics = new MulticlassMetrics(labelAndPreds)
println(s"Precision: ${metrics.precision}")
println(s"Recall: ${metrics.recall}")
println(s"F1 Score: ${metrics.fMeasure}")

val metrics = new BinaryClassificationMetrics(labelAndPreds)
println(s"Area under PR curve: ${metrics.areaUnderPR}")
println(s"Area under ROC curve: ${metrics.areaUnderROC}")

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");

# PRINT THE ROC METRIC
println(s"Area under ROC curve: ${metrics.areaUnderROC}")
```

**出力:**

ROC 曲線下面積: 0.9846895479241554

## <a name="regression-model-predict-tip-amount"></a>回帰モデル: チップの金額を予測する
このセクションでは、チップの金額を予測する、次の 2 種類の回帰モデルを作成します。

* Spark ML の `LinearRegression()` 関数を使用した **正規化線形回帰モデル**。 このモデルは保存して、テスト データで評価します。
* Spark ML の `GBTRegressor()` 関数を使用した **勾配ブースティング ツリー回帰モデル**。

### <a name="create-a-regularized-linear-regression-model"></a>正規化線形回帰を作成する

```scala
# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# CREATE A REGULARIZED LINEAR REGRESSION MODEL BY USING THE SPARK ML FUNCTION AND DATA FRAMES
val lr = new LinearRegression().setLabelCol("tip_amount").setFeaturesCol("features").setMaxIter(10).setRegParam(0.3).setElasticNetParam(0.8)

# FIT THE MODEL BY USING DATA FRAMES
val lrModel = lr.fit(OneHotTRAIN)
println(s"Coefficients: ${lrModel.coefficients} Intercept: ${lrModel.intercept}")

# SUMMARIZE THE MODEL OVER THE TRAINING SET AND PRINT METRICS
val trainingSummary = lrModel.summary
println(s"numIterations: ${trainingSummary.totalIterations}")
println(s"objectiveHistory: ${trainingSummary.objectiveHistory.toList}")
trainingSummary.residuals.show()
println(s"RMSE: ${trainingSummary.rootMeanSquaredError}")
println(s"r2: ${trainingSummary.r2}")

# SAVE THE MODEL IN AZURE BLOB STORAGE
val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
val modelName = "LinearRegression__"
val filename = modelDir.concat(modelName).concat(datestamp)
lrModel.save(filename);

# PRINT THE COEFFICIENTS
println(s"Coefficients: ${lrModel.coefficients} Intercept: ${lrModel.intercept}")

# SCORE THE MODEL ON TEST DATA
val predictions = lrModel.transform(OneHotTEST)

# EVALUATE THE MODEL ON TEST DATA
val evaluator = new RegressionEvaluator().setLabelCol("tip_amount").setPredictionCol("prediction").setMetricName("r2")
val r2 = evaluator.evaluate(predictions)
println("R-sqr on test data = " + r2)

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");
```


**出力:**

セルの実行時間: 13 秒。

```scala
# LOAD A SAVED LINEAR REGRESSION MODEL FROM BLOB STORAGE AND SCORE A TEST DATA SET

# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# LOAD A SAVED LINEAR REGRESSION MODEL FROM AZURE BLOB STORAGE
val savedModel = org.apache.spark.ml.regression.LinearRegressionModel.load(filename)
println(s"Coefficients: ${savedModel.coefficients} Intercept: ${savedModel.intercept}")

# SCORE THE MODEL ON TEST DATA
val predictions = savedModel.transform(OneHotTEST).select("tip_amount","prediction")
predictions.registerTempTable("testResults")

# EVALUATE THE MODEL ON TEST DATA
val evaluator = new RegressionEvaluator().setLabelCol("tip_amount").setPredictionCol("prediction").setMetricName("r2")
val r2 = evaluator.evaluate(predictions)
println("R-sqr on test data = " + r2)

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");

# PRINT THE RESULTS
println("R-sqr on test data = " + r2)
```

**出力:**

テスト データの R-sqr = 0.5960320470835743

次に、テスト結果に対してデータ フレームとしてクエリを実行し、AutoVizWidget および matplotlib を使用して視覚化します。

```sql
# RUN A SQL QUERY
%%sql -q -o sqlResults
select * from testResults

# RUN THE CODE LOCALLY ON THE JUPYTER SERVER
%%local

# USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES
# CLICK THE TYPE OF PLOT TO GENERATE (LINE, AREA, BAR, AND SO ON)
sqlResults
```

このコードでは、クエリの出力からローカル データ フレームを作成し、データをプロットします。 `%%local` マジックで、matplotlib でのプロットに使用可能なローカル データフレーム (`sqlResults`) を作成します。

> [!NOTE]
> この Spark マジックは、この記事で何度も使用しています。 データの量が大きい場合はサンプリングして、ローカル メモリに収まるデータフレームを作成する必要があります。
> 
> 

Python matplotlib を使用してプロットを作成します。

```scala
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
%%local
sqlResults
%matplotlib inline
import numpy as np

# PLOT THE RESULTS
ax = sqlResults.plot(kind='scatter', figsize = (6,6), x='tip_amount', y='prediction', color='blue', alpha = 0.25, label='Actual vs. predicted');
fit = np.polyfit(sqlResults['tip_amount'], sqlResults['prediction'], deg=1)
ax.set_title('Actual vs. Predicted Tip Amounts ($)')
ax.set_xlabel("Actual")
ax.set_ylabel("Predicted")
#ax.plot(sqlResults['tip_amount'], fit[0] * sqlResults['prediction'] + fit[1], color='magenta')
plt.axis([-1, 15, -1, 8])
plt.show(ax)
```

**出力:**

![チップの金額: 実際の金額と予測金額](./media/scala-walkthrough/plot-actual-vs-predicted-tip-amount.png)

### <a name="create-a-gbt-regression-model"></a>GBT 回帰モデルを作成する
次に、Spark ML の `GBTRegressor()` 関数を使用して GBT 回帰モデルを作成し、テスト データでモデルを評価します。

[勾配ブースティング ツリー](https://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTS) は、複数のデシジョン ツリーをまとめたものです。 GBTS は、デシジョン ツリーを繰り返しトレーニングすることで損失関数を最小限に抑えます。 GBTS は、回帰および分類に使用できます。 カテゴリの特徴を処理可能ですが特徴のスケーリングは不要であり、非線形性や特徴の相互作用をキャプチャすることができます。 また、多クラス分類設定でも使用できます。

```scala
# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# TRAIN A GBT REGRESSION MODEL
val gbt = new GBTRegressor().setLabelCol("label").setFeaturesCol("featuresCat").setMaxIter(10)
val gbtModel = gbt.fit(indexedTRAINwithCatFeat)

# MAKE PREDICTIONS
val predictions = gbtModel.transform(indexedTESTwithCatFeat)

# COMPUTE TEST SET R2
val evaluator = new RegressionEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("r2")
val Test_R2 = evaluator.evaluate(predictions)


# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");

# PRINT THE RESULTS
println("Test R-sqr is: " + Test_R2);
```

**出力:**

テストの R-sqr = 0.7655383534596654

## <a name="advanced-modeling-utilities-for-optimization"></a>最適化のための高度なモデリング ユーティリティ
このセクションでは、開発者がモデルを最適化するために頻繁に用いる機械学習ユーティリティを使用します。 具体的には、機械学習モデルの最適化は、パラメーター スイープとクロス検証を使用する 3 種類の方法で行うことができます。

* データをトレーニング セットと検証セットに分け、トレーニング セットではハイパーパラメーター スイープを使用してモデルを最適化し、検証セットで評価を行う (線形回帰)
* Spark ML の CrossValidator 関数を使用し、クロス検証とハイパーパラメーター スイープを使用してモデルを最適化する (二項分類)
* 機械学習関数とパラメーター セットを利用するクロス検証とパラメーター スイープのカスタム コードを使用して、モデルを最適化する (線形回帰)

**クロス検証** は、既知のデータ セットでトレーニングされたモデルが、トレーニングに使用されていないデータ セットの特徴の予測に対してどの程度汎用化されるかを評価する手法です。 この手法のベースにある基本的な考え方は、既知のデータ セットでモデルをトレーニングし、それとは別のデータ セットで予測の精度をテストするというものです。 一般的な実装では、データセットを *k* 個のフォールドに分割し、1 つを除くすべてのフォールドでラウンドロビン方式によりモデルをトレーニングします。

**ハイパーパラメーターの最適化** とは、学習アルゴリズムでどのようなハイパーパラメーターのセットを選択するかを検討することであり、一般的には、独立したデータ セットに対するアルゴリズムのパフォーマンスの測定を最適化する目的で行います。 ハイパーパラメーターは、モデルのトレーニング手順とは別に指定する必要のある値です。 ハイパーパラメーターに関する仮定は、モデルの柔軟性と精度に影響を及ぼします。 たとえば、デシジョン ツリーには、必要なツリーの深さやリーフの数といったハイパーパラメーターがあります。 サポート ベクター マシン (SVM) の場合は、誤分類ペナルティ項を設定する必要があります。

ハイパーパラメーターの最適化を実行する一般的な方法では、グリッド探索 (別名 **パラメーター スイープ**) を使用します。 グリッド探索では、学習アルゴリズムのハイパーパラメーター空間のうち、指定されたサブセットを対象としてしらみつぶしに値を探索します。 クロス検証のパフォーマンス メトリックを使用すると、グリッド探索アルゴリズムによって生成される結果から最適なものを選別できます。 クロス検証ハイパーパラメーター スイープを使用すると、トレーニング データに対してモデルがオーバーフィットされるなどの問題を抑えることができます。 これにより、モデルをトレーニング データの抽出元のデータ セット全体に対して適用できるようになります。

### <a name="optimize-a-linear-regression-model-with-hyper-parameter-sweeping"></a>ハイパーパラメーター スイープを使用して線形回帰モデルを最適化する
次に、データをトレーニング セットと検証セットに分け、ハイパーパラメーター スイープを実行してトレーニング セットでモデルを最適化し、検証セットで評価します (線形回帰)。

```scala
# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# RENAME `tip_amount` AS A LABEL
val OneHotTRAINLabeled = OneHotTRAIN.select("tip_amount","features").withColumnRenamed(existingName="tip_amount",newName="label")
val OneHotTESTLabeled = OneHotTEST.select("tip_amount","features").withColumnRenamed(existingName="tip_amount",newName="label")
OneHotTRAINLabeled.cache()
OneHotTESTLabeled.cache()

# DEFINE THE ESTIMATOR FUNCTION: `THE LinearRegression()` FUNCTION
val lr = new LinearRegression().setLabelCol("label").setFeaturesCol("features").setMaxIter(10)

# DEFINE THE PARAMETER GRID
val paramGrid = new ParamGridBuilder().addGrid(lr.regParam, Array(0.1, 0.01, 0.001)).addGrid(lr.fitIntercept).addGrid(lr.elasticNetParam, Array(0.1, 0.5, 0.9)).build()

# DEFINE THE PIPELINE WITH A TRAIN/TEST VALIDATION SPLIT (75% IN THE TRAINING SET), AND THEN THE SPECIFY ESTIMATOR, EVALUATOR, AND PARAMETER GRID
val trainPct = 0.75
val trainValidationSplit = new TrainValidationSplit().setEstimator(lr).setEvaluator(new RegressionEvaluator).setEstimatorParamMaps(paramGrid).setTrainRatio(trainPct)

# RUN THE TRAIN VALIDATION SPLIT AND CHOOSE THE BEST SET OF PARAMETERS
val model = trainValidationSplit.fit(OneHotTRAINLabeled)

# MAKE PREDICTIONS ON THE TEST DATA BY USING THE MODEL WITH THE COMBINATION OF PARAMETERS THAT PERFORMS THE BEST
val testResults = model.transform(OneHotTESTLabeled).select("label", "prediction")

# COMPUTE TEST SET R2
val evaluator = new RegressionEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("r2")
val Test_R2 = evaluator.evaluate(testResults)

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");

println("Test R-sqr is: " + Test_R2);
```

**出力:**

テストの R-sqr = 0.6226484708501209

### <a name="optimize-the-binary-classification-model-by-using-cross-validation-and-hyper-parameter-sweeping"></a>クロス検証とハイパーパラメーター スイープを使用して二項分類モデルを最適化する
このセクションでは、クロス検証とハイパーパラメーター スイープを使用して二項分類モデルを最適化する方法を示します。 これには、Spark ML の `CrossValidator` 関数を使用します。

```scala
# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# CREATE DATA FRAMES WITH PROPERLY LABELED COLUMNS TO USE WITH THE TRAIN AND TEST SPLIT
val indexedTRAINwithCatFeatBinTargetRF = indexedTRAINwithCatFeatBinTarget.select("labelBin","featuresCat").withColumnRenamed(existingName="labelBin",newName="label").withColumnRenamed(existingName="featuresCat",newName="features")
val indexedTESTwithCatFeatBinTargetRF = indexedTESTwithCatFeatBinTarget.select("labelBin","featuresCat").withColumnRenamed(existingName="labelBin",newName="label").withColumnRenamed(existingName="featuresCat",newName="features")
indexedTRAINwithCatFeatBinTargetRF.cache()
indexedTESTwithCatFeatBinTargetRF.cache()

# DEFINE THE ESTIMATOR FUNCTION
val rf = new RandomForestClassifier().setLabelCol("label").setFeaturesCol("features").setImpurity("gini").setSeed(1234).setFeatureSubsetStrategy("auto").setMaxBins(32)

# DEFINE THE PARAMETER GRID
val paramGrid = new ParamGridBuilder().addGrid(rf.maxDepth, Array(4,8)).addGrid(rf.numTrees, Array(5,10)).addGrid(rf.minInstancesPerNode, Array(100,300)).build()

# SPECIFY THE NUMBER OF FOLDS
val numFolds = 3

# DEFINE THE TRAIN/TEST VALIDATION SPLIT (75% IN THE TRAINING SET)
val CrossValidator = new CrossValidator().setEstimator(rf).setEvaluator(new BinaryClassificationEvaluator).setEstimatorParamMaps(paramGrid).setNumFolds(numFolds)

# RUN THE TRAIN VALIDATION SPLIT AND CHOOSE THE BEST SET OF PARAMETERS
val model = CrossValidator.fit(indexedTRAINwithCatFeatBinTargetRF)

# MAKE PREDICTIONS ON THE TEST DATA BY USING THE MODEL WITH THE COMBINATION OF PARAMETERS THAT PERFORMS THE BEST
val testResults = model.transform(indexedTESTwithCatFeatBinTargetRF).select("label", "prediction")

# COMPUTE THE TEST F1 SCORE
val evaluator = new MulticlassClassificationEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("f1")
val Test_f1Score = evaluator.evaluate(testResults)

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");
```

**出力:**

セルの実行時間: 33 秒。

### <a name="optimize-the-linear-regression-model-by-using-custom-cross-validation-and-parameter-sweeping-code"></a>クロス検証とパラメーター スイープのカスタム コードを使用して線形回帰モデルを最適化する
次に、カスタム コードを使用してモデルを最適化し、最高精度の基準を使用して最適なモデル パラメーターを特定します。 その後、最終的なモデルを作成し、テスト データでモデルを評価して、BLOB ストレージにモデルを保存します。 最後に、モデルを読み込み、テスト データをスコア付けして精度を評価します。

```scala
# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# DEFINE THE PARAMETER GRID AND THE NUMBER OF FOLDS
val paramGrid = new ParamGridBuilder().addGrid(rf.maxDepth, Array(5,10)).addGrid(rf.numTrees, Array(10,25,50)).build()

val nFolds = 3
val numModels = paramGrid.size
val numParamsinGrid = 2

# SPECIFY THE NUMBER OF CATEGORIES FOR CATEGORICAL VARIABLES
val categoricalFeaturesInfo = Map[Int, Int]((0,2),(1,2),(2,6),(3,4))

var maxDepth = -1
var numTrees = -1
var param = ""
var paramval = -1
var validateLB = -1.0
var validateUB = -1.0
val h = 1.0 / nFolds;
val RMSE  = Array.fill(numModels)(0.0)

# CREATE K-FOLDS
val splits = MLUtils.kFold(indexedTRAINbinary, numFolds = nFolds, seed=1234)


# LOOP THROUGH K-FOLDS AND THE PARAMETER GRID TO GET AND IDENTIFY THE BEST PARAMETER SET BY LEVEL OF ACCURACY
for (i <- 0 to (nFolds-1)) {
    validateLB = i * h
    validateUB = (i + 1) * h
    val validationCV = trainData.filter($"rand" >= validateLB  && $"rand" < validateUB)
    val trainCV = trainData.filter($"rand" < validateLB  || $"rand" >= validateUB)
    val validationLabPt = validationCV.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)));
    val trainCVLabPt = trainCV.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)));
    validationLabPt.cache()
    trainCVLabPt.cache()

    for (nParamSets <- 0 to (numModels-1)) {
        for (nParams <- 0 to (numParamsinGrid-1)) {
            param = paramGrid(nParamSets).toSeq(nParams).param.toString.split("__")(1)
            paramval = paramGrid(nParamSets).toSeq(nParams).value.toString.toInt
            if (param == "maxDepth") {maxDepth = paramval}
            if (param == "numTrees") {numTrees = paramval}
        }
        val rfModel = RandomForest.trainRegressor(trainCVLabPt, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                                  numTrees=numTrees, maxDepth=maxDepth,
                                                  featureSubsetStrategy="auto",impurity="variance", maxBins=32)
        val labelAndPreds = validationLabPt.map { point =>
                                                 val prediction = rfModel.predict(point.features)
                                                  ( prediction, point.label )
                                                }
        val validMetrics = new RegressionMetrics(labelAndPreds)
        val rmse = validMetrics.rootMeanSquaredError
        RMSE(nParamSets) += rmse
    }
    validationLabPt.unpersist();
    trainCVLabPt.unpersist();
}
val minRMSEindex = RMSE.indexOf(RMSE.min)

# GET THE BEST PARAMETERS FROM A CROSS-VALIDATION AND PARAMETER SWEEP
var best_maxDepth = -1
var best_numTrees = -1
for (nParams <- 0 to (numParamsinGrid-1)) {
    param = paramGrid(minRMSEindex).toSeq(nParams).param.toString.split("__")(1)
    paramval = paramGrid(minRMSEindex).toSeq(nParams).value.toString.toInt
    if (param == "maxDepth") {best_maxDepth = paramval}
    if (param == "numTrees") {best_numTrees = paramval}
}

# CREATE THE BEST MODEL WITH THE BEST PARAMETERS AND A FULL TRAINING DATA SET
val best_rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                                  numTrees=best_numTrees, maxDepth=best_maxDepth,
                                                  featureSubsetStrategy="auto",impurity="variance", maxBins=32)

# SAVE THE BEST RANDOM FOREST MODEL IN BLOB STORAGE
val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
val modelName = "BestCV_RF_Regression__"
val filename = modelDir.concat(modelName).concat(datestamp)
best_rfModel.save(sc, filename);

# PREDICT ON THE TRAINING SET WITH THE BEST MODEL AND THEN EVALUATE
val labelAndPreds = indexedTESTreg.map { point =>
                                        val prediction = best_rfModel.predict(point.features)
                                        ( prediction, point.label )
                                       }

val test_rmse = new RegressionMetrics(labelAndPreds).rootMeanSquaredError
val test_rsqr = new RegressionMetrics(labelAndPreds).r2

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");


# LOAD THE MODEL
val savedRFModel = RandomForestModel.load(sc, filename)

val labelAndPreds = indexedTESTreg.map { point =>
                                        val prediction = savedRFModel.predict(point.features)
                                        ( prediction, point.label )
                                       }
# TEST THE MODEL
val test_rmse = new RegressionMetrics(labelAndPreds).rootMeanSquaredError
val test_rsqr = new RegressionMetrics(labelAndPreds).r2
```

**出力:**

セルの実行時間: 61 秒。

## <a name="consume-spark-built-machine-learning-models-automatically-with-scala"></a>Spark で構築した機械学習モデルの Scala での使用を自動化する
Azure でのデータ サイエンス プロセスを構成するタスクについて説明したトピックの概要については、 [Team Data Science Process](./index.yml)に関するページをご覧ください。

[Team Data Science Process のチュートリアル](walkthroughs.md) 」に記載されています。 これらのチュートリアルでは、クラウドとオンプレミスのツールおよびサービスをワークフローまたはパイプラインに組み込んで、インテリジェントなアプリケーションを作成する方法についても説明します。

[Spark で構築した機械学習モデルのスコア付け](spark-model-consumption.md) 」に記載されています。 これらの資料の手順に従い、この記事の Scala コードを Python コードに置き換えるだけで使用を自動化できます。