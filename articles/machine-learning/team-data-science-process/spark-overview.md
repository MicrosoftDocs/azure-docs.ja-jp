---
title: Azure HDInsight 上の Spark を使用したデータ サイエンス - Team Data Science Process
description: Spark MLlib ツールキットによって、かなりの Machine Learning モデリング機能がこの分散 HDInsight 環境にもたらされます。
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
ms.openlocfilehash: 1dd82fb00c55e3676929999f204eae8755671038
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "93314751"
---
# <a name="overview-of-data-science-using-spark-on-azure-hdinsight"></a>Azure HDInsight 上の Spark を使用したデータ サイエンスの概要

この一連のトピックでは、データの取り込み、特徴エンジニアリング、モデリング、モデル評価など、データ サイエンスの一般的な作業を HDInsight Spark を使用して行う方法について説明しています。 ここで使用しているデータは、2013 年における NYC タクシーの乗車と料金のデータセットから抽出したサンプルです。 構築されるモデルには、ロジスティック回帰と線形回帰、ランダム フォレスト、勾配ブースティング ツリーなどがあります。 これらのモデルを Azure Blob Storage (WASB) に保存する方法や、その予測パフォーマンスをスコア付けして評価する方法も、一連のトピックの中で紹介します。 クロス検証とハイパーパラメーター スイープを使用してモデルをトレーニングする方法については、高度な内容を扱った別のトピックで説明します。 また、この概要トピックでは、後続の 3 つのチュートリアルの手順を踏襲するために必要な Spark クラスターのセットアップ方法を説明しているトピックも紹介します。

## <a name="spark-and-mllib"></a>Spark と MLlib
[Spark](https://spark.apache.org/) はビッグ データ分析アプリケーションのパフォーマンスを向上するメモリ内の処理をサポートする、オープン ソースの並列処理のフレームワークです。 Spark 処理エンジンは、高速かつ簡単に高度な分析を行うことができるように作成されています。 Spark のメモリ内の分散計算機能により、Machine Learning とグラフ計算に使用される反復的なアルゴリズムに対して、Spark は適切な選択肢となります。 [MLlib](https://spark.apache.org/mllib/) はスケーラブルな Spark の Machine Learning ライブラリであり、これによってアルゴリズム モデリング機能がこの分散環境にもたらされます。

## <a name="hdinsight-spark"></a>HDInsight Spark
[HDInsight Spark](../../hdinsight/spark/apache-spark-overview.md) は、Azure でホストされるオープンソースの Spark サービスです。 さらに、Azure BLOB (WASB) に格納されているデータを変換、フィルター処理、および視覚化するために Spark クラスターで Spark SQL の対話型クエリを実行できる **Jupyter PySpark ノートブック** のサポートも含まれます。 PySpark は、Spark の Python API です。 ここで各種の手法を紹介したりデータを視覚化するためのプロットを表示したりするために使用しているコード スニペットは、Spark クラスターにインストールされた Jupyter Notebook で実行しています。 これらのトピックにあるモデリング手順には、各種モデルをトレーニング、評価、保存、および使用する方法を示すコードも含まれています。

## <a name="setup-spark-clusters-and-jupyter-notebooks"></a>セットアップ:Spark クラスターと Jupyter Notebook
このチュートリアルで示すセットアップ手順とコードは HDInsight Spark 1.6 向けですが、 Jupyter Notebook は HDInsight Spark 1.6 と Spark 2.0 の両方のクラスター向けに提供されています。 ノートブックの説明およびノートブックへのリンクは、ノートブックが含まれる GitHub リポジトリの [Readme.md](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) 内にあります。 また、このページとリンク先のノートブックに記載しているコードは汎用性があり、どの Spark クラスターでも動作します。 HDInsight Spark を使用していない場合、クラスターのセットアップと管理の手順は、ここに記載されている内容と若干異なります。 作業しやすいように、Spark 1.6 (Jupyter Notebook サーバーの pySpark カーネルで実行) および Spark 2.0 (Jupyter Notebook サーバーの pySpark3 カーネルで実行) 向け Jupyter Notebook へのリンクを以下に示します。

### <a name="spark-16-notebooks"></a>Spark 1.6 向け Notebook
これらの Notebook は、Jupyter Notebook サーバーの pySpark カーネルで実行されます。

- [pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb):複数の異なるアルゴリズムを使用してデータの探索、モデリング、スコア付けを実行する方法について説明します。
- [pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb):Notebook #1 のトピックと、ハイパーパラメーター チューニングとクロス検証を使用したモデル開発が含まれます。
- [pySpark-machine-learning-data-science-spark-model-consumption.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb):保存したモデルを HDInsight クラスターで Python を使用して操作する方法を示します。

### <a name="spark-20-notebooks"></a>Spark 2.0 向け Notebook
これらの Notebook は、Jupyter Notebook サーバーの pySpark3 カーネルで実行されます。

- [Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb):このファイルでは、[こちら](#the-nyc-2013-taxi-data)で説明する NYC タクシーの乗車と料金のデータセットを使用して、Spark 2.0 クラスターでデータの探索、モデリング、スコア付けを実行する方法を示します。 この Notebook は、Spark 2.0 向けに用意されているコードをすばやく確認するための出発点として適しています。 NYC タクシー データを分析する詳細な Notebook については、この一覧の次の Notebook をご覧ください。 これらの Notebook の比較については、この一覧の後の「注意」をご覧ください。
- [Spark2.0-pySpark3_NYC_Taxi_Tip_Regression.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_NYC_Taxi_Tip_Regression.ipynb):このファイルでは、[こちら](#the-nyc-2013-taxi-data)で説明されている NYC タクシーの乗車と料金のデータセットを使用してデータのラングリング (Spark SQL およびデータフレームの操作)、探索、モデリング、スコア付けを実行する方法を示します。
- [Spark2.0-pySpark3_Airline_Departure_Delay_Classification.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_Airline_Departure_Delay_Classification.ipynb):このファイルでは、よく知られている 2011 から 2012 年の航空会社の定刻出発のデータセットを使用してデータのラングリング (Spark SQL およびデータフレームの操作)、探索、モデリング、スコア付けを実行する方法を示します。 モデリングの前に航空会社のデータセットを空港の気象データ (たとえば、風速、気温、高度など) と統合したため、これらの気象条件をモデルに含めることができます。

<!-- -->

> [!NOTE]
> 分類アルゴリズムの使用をより理解しやすくするために、Spark 2.0 のノートブックに航空会社のデータセットが追加されました。 定刻出発のデータセットと気象のデータセットについては、次のリンクをご覧ください。
> 
> - 航空会社の時間どおりの出発データ: [https://www.transtats.bts.gov/ONTIME/](https://www.transtats.bts.gov/ONTIME/)
> 
> - 空港の気象データ: [https://www.ncdc.noaa.gov/](https://www.ncdc.noaa.gov/)

<!-- -->

<!-- -->

> [!NOTE]
> NYC タクシーのデータセットと航空会社のフライト遅延データセットの Spark 2.0 Notebook は、(HDI クラスターのサイズによっては) 実行に 10 分以上かかる場合があります。 上記の最初のノートブックでは、タクシー ファイルと料金ファイルが事前に結合された、ダウンサンプリングされた NYC データセットを使用して短時間で実行され、Notebook でのデータ探索、視覚化、ML モデル トレーニングのさまざまな側面を示します[Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb)。 このノートブックは完了までの時間が非常に短いので (2 - 3 分)、Spark 2.0 向けに用意されているコードをすばやく確認するための出発点として適しています。

<!-- -->

Spark 2.0 モデルの操作とスコア付けのためのモデル使用については、[使用に関する Spark 1.6 のドキュメント](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb)で、必要な手順を説明している例をご覧ください。 この例を Spark 2.0 で使用するには、Python コード ファイルを[このファイル](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Python/Spark2.0_ConsumeRFCV_NYCReg.py)で置き換えます。

### <a name="prerequisites"></a>前提条件

以下の手順は Spark 1.6 に関するものです。 Spark 2.0 バージョンについては、上記で説明およびリンクされている Notebook を使用してください。

1. Azure サブスクリプションが必要です。 Azure サブスクリプションがない場合は、 [Azure 無料試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。

2. このチュートリアルを実行するには、Spark 1.6 クラスターが必要です。 クラスターを作成するには、 [Azure HDInsight での Apache Spark クラスターの作成](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md)に関するページの手順を参照してください。 クラスターの種類とバージョンは、 **[クラスターの種類の選択]** メニューから指定します。

![クラスターの構成](./media/spark-overview/spark-cluster-on-portal.png)

<!-- -->

> [!NOTE]
> Python ではなく Scala を使用して、エンド ツー エンドのデータ サイエンス プロセスのタスクを実行する方法については、「 [Scala および Azure 上の Spark を使用したデータ サイエンス](scala-walkthrough.md)」をご覧ください。
>
>

<!-- -->

> [!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]
>
>

## <a name="the-nyc-2013-taxi-data"></a>2013 年 NYC タクシー データ
NYC タクシー乗車データは、約 20 GB の圧縮されたコンマ区切り値 (CSV) ファイル (非圧縮で最大 48 GB) です。1 億 7300 万以上の個々の乗車と、各乗車に支払われた料金で構成されています。 各乗車レコードには、乗車と降車の場所と時間、匿名化されたタクシー (運転手の) 免許番号、および営業許可番号 (タクシーの一意の ID) が含まれています。 データには 2013 年のすべての乗車が含まれ、データは月ごとに次の 2 つのデータセットに用意されています。

1. 'trip_data' CSV ファイルには、乗車の詳細 (乗客数、乗車地点、降車地点、乗車時間、乗車距離など) が含まれています。 いくつかのサンプル レコードを次に示します。

   `medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude`

   `89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171`

   `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066`

   `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002`

   `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388`

   `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868`

2. 'trip_fare' CSV ファイルには、各乗車に対して支払われた料金の詳細 (支払いの種類、料金、追加料金、税、チップ、道路などの通行料、合計支払金額など) が含まれます。 いくつかのサンプル レコードを次に示します。

   `medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount`

   `89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7`

   `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7`

   `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7`

   `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6`

   `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5`

これらのファイルから 0.1% のサンプルを取得し、trip\_data CVS ファイルと trip\_fare CVS ファイルを、このチュートリアルの入力データセットとして使用される 1 つのデータセットに結合しました。 trip\_data と trip\_fare を結合するための一意のキーは medallion、hack\_licence、pickup\_datetime の各フィールドで構成されています。 データセットの各レコードには、NYC タクシー乗車を表す次の属性が含まれています。

| フィールド | 簡単な説明 |
| --- | --- |
| medallion |匿名化されたタクシーのメダリオン (タクシーの一意の ID) |
| hack_license |匿名化された Hackney Carriage の免許番号 |
| vendor_id |タクシー ベンダーの ID |
| rate_code |NYC タクシーの料金レート |
| store_and_fwd_flag |格納および転送フラグ |
| pickup_datetime |乗車の日付と時間 |
| dropoff_datetime |降車の日付と時間 |
| pickup_hour |乗車の時間帯 |
| pickup_week |乗車の年の通算週 |
| weekday |曜日 (1 - 7 の範囲) |
| passenger_count |タクシー乗車の乗客数 |
| trip_time_in_secs |乗車時間 (秒) |
| trip_distance |乗車距離 (マイル) |
| pickup_longitude |乗車経度 |
| pickup_latitude |乗車緯度 |
| dropoff_longitude |降車経度 |
| dropoff_latitude |降車緯度 |
| direct_distance |乗車場所と降車場所間の直線距離 |
| payment_type |支払の種類 (現金、クレジット カードなど) |
| fare_amount |料金 |
| surcharge |追加料金 |
| mta_tax |MTA 地下鉄輸送税 |
| tip_amount |チップの金額 |
| tolls_amount |通行料金 |
| total_amount |合計金額 |
| tipped |チップ (ない場合は 0、ある場合は 1) |
| tip_class |チップのクラス (0: $0、1: $0 - 5、2: $6 - 10、3: $11 - 20、4: $20 以上) |

## <a name="execute-code-from-a-jupyter-notebook-on-the-spark-cluster"></a>Spark クラスターで、Jupyter Notebook からのコードを実行する
Jupyter Notebook は Azure ポータルから起動できます。 ダッシュボードでご利用の Spark クラスターを見つけてクリックし、クラスターの管理ページにアクセスします。 **[クラスター ダッシュボード]**  ->  **[Jupyter Notebook]** をクリックして、Spark クラスターに関連付けられている Notebook を開きます。

![クラスター ダッシュボード](./media/spark-overview/spark-jupyter-on-portal.png)

***`https://CLUSTERNAME.azurehdinsight.net/jupyter`*** を参照し、Jupyter Notebooks にアクセスすることもできます。 この URL の CLUSTERNAME の部分は、実際のクラスターの名前に置き換えてください。 ノートブックにアクセスするには、管理者アカウントのパスワードが必要です。

![Jupyter Notebook を参照する](./media/spark-overview/spark-jupyter-notebook.png)

[PySpark] を選択すると表示されるディレクトリには、PySpark API を使用する、あらかじめパッケージ化された Notebook の例が含まれています。 Spark に関連する一連のトピックのコード サンプルが含まれた Notebook は [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark) で入手できます

[GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark) から、Spark クラスター上の Jupyter Notebook サーバーに Notebook を直接アップロードできます。 Jupyter のホーム ページで、画面の右側にある **[アップロード]** ボタンをクリックします。 ファイル エクスプ ローラーが開きます。 ここで、Notebook の GitHub (生コンテンツ) URL を貼り付け、 **[開く]** をクリックします。

Jupyter ファイルの一覧にファイル名が **[アップロード]** ボタンと共に表示されます。 この **[アップロード]** ボタンをクリックします。 これで、ノートブックがインポートされます。 このチュートリアルの他のノートブックをアップロードするには、この手順を繰り返します。

> [!TIP]
> ブラウザーの下部にあるリンクを右クリックし、 **[リンクのコピー]** を選択すると、GitHub の生コンテンツ URL を取得できます。 この URL を、Jupyter アップロード ファイル エクスプローラーのダイアログ ボックスに貼り付けることができます。
> 
> 

ここでは、次の操作を実行できます。

* Notebook をクリックしてコードを参照する。
* **Shift キーを押しながら Enter キー** を押すことで各セルを実行する。
* **[セル]**  ->  **[実行]** をクリックして Notebook 全体を実行する。
* クエリの自動視覚化を使用する。

> [!TIP]
> PySpark カーネルは、SQL (HiveQL) のクエリの出力を自動的に視覚化します。 視覚化にはいくつかの種類 (表、円グラフ、折れ線グラフ、面グラフ、棒グラフ) があり、Notebook の **[Type (タイプ)]** メニュー ボタンで選択できます。
>
>

![Logistic regression ROC curve for generic approach](./media/spark-overview/pyspark-jupyter-autovisualization.png)

## <a name="whats-next"></a>次の操作
HDInsight Spark クラスターが設定され、Jupyter Notebook がアップロードされると、これら 3 つの PySpark ノートブックに関連するトピックに進むことができます。 これらのトピックには、データの探索方法、モデルの作成方法および使用方法が示されています。 高度なデータの探索と Notebook のモデリングでは、クロス検証、ハイパー パラメーター スイープ、モデルの評価を使用する方法を示します。

**Spark を使用したデータ探索とモデリング:** [Spark MLlib ツールキットを使用したデータの二項分類と回帰モデルの作成](spark-data-exploration-modeling.md)に関するトピックで、データセットの詳細を確認し、機械学習モデルの作成、スコア付け、評価を行います。

**モデルの使用:** このトピックで作成した分類モデルと回帰モデルにスコアを付ける方法については、[Spark で構築した機械学習モデルのスコア付けと評価](spark-model-consumption.md)に関するページを参照してください。

**クロス検証とハイパーパラメーター スイープ**:クロス検証とハイパーパラメーター スイープを使用したモデルのトレーニング方法については、「[Spark を使用した高度なデータ探索とモデリング](spark-advanced-data-exploration-modeling.md)」を参照してください。