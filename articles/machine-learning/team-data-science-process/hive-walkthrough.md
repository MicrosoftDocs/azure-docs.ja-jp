---
title: Hadoop クラスターでのデータの探索 - Team Data Science Process
description: HDInsight Hadoop クラスターを用いたエンド ツー エンドのシナリオに Team Data Science Process を使用し、モデルを構築してデプロイします。
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
ms.openlocfilehash: 53f50e98bcec4b8ace342808f0bcfd96770834b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "96002223"
---
# <a name="the-team-data-science-process-in-action-use-azure-hdinsight-hadoop-clusters"></a>Team Data Science Process の実行:Azure HDInsight Hadoop クラスターの使用
このチュートリアルでは、[Team Data Science Process (TDSP)](overview.md) をエンド ツー エンドのシナリオで使用します。 [Azure HDInsight Hadoop クラスター](https://azure.microsoft.com/services/hdinsight/)を使用して、公開されている [NYC タクシー乗車](https://www.andresmh.com/nyctaxitrips/)データセットのデータの保存、探索、特徴エンジニアリングを行い、データのダウンサンプリングを実行します。 二項分類、多クラス分類、回帰予測タスクを処理するために、ここでは Azure Machine Learning を使用してデータのモデルを構築します。 

大規模なデータ セットの処理方法を説明したチュートリアルについては、「[Team Data Science Process の活用 - 1 TB データセットでの Azure HDInsight Hadoop クラスターの使用](hive-criteo-walkthrough.md)」を参照してください。

IPython Notebook を使用して、このチュートリアルで説明する 1 TB のデータセットを使用したタスクを実行することもできます。 詳細については、[Hive ODBC 接続を使用する Criteo のチュートリアル](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb)を参照してください。

## <a name="nyc-taxi-trips-dataset-description"></a><a name="dataset"></a>NYC タクシー乗車データセットの説明
NYC タクシー乗車データは、約 20 GB の圧縮されたコンマ区切り値 (CSV) ファイル (非圧縮で最大 48 GB) です。 このデータには、1 億 7300 万以上の個々の乗車と、各乗車に支払われた料金が含まれています。 各旅行レコードには、pickup (乗車) と dropoff (降車) の場所と時間、匿名化されたタクシー運転手の (運転) 免許番号、および medallion 番号 (タクシーの一意の ID) が含まれています。 データには 2013 年のすべての乗車が含まれ、データは月ごとに次の 2 つのデータセットに用意されています。

- trip_data CSV ファイルには、乗車の詳細 (乗客数、乗車地点、降車地点、乗車時間、乗車距離) が含まれています。 いくつかのサンプル レコードを次に示します。

  `medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude`

  `89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171`

  `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066`

  `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002`

  `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388`

  `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868`

- trip_fare CSV ファイルには、各乗車に対して支払われた料金の詳細 (支払いの種類、料金、追加料金と税、チップ、道路などの通行料、合計支払金額) が含まれます。 いくつかのサンプル レコードを次に示します。

  `medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount`

  `89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7`

  `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7`

  `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7`

  `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6`

  `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5`

trip\_data と trip\_fare を結合するための一意のキーは medallion、hack\_licence、pickup\_datetime の各フィールドで構成されています。 特定の乗車に関する詳細をすべて取得するには、これら 3 つのキーと連結するだけで十分です。

## <a name="examples-of-prediction-tasks"></a><a name="mltasks"></a>予測タスクの例
行う予測の種類をデータ分析に基づいて決定すると、必要なプロセス タスクを明確にするのに役立ちます。 このチュートリアルで扱う予測問題の 3 つの例を以下に示します。すべて *tip\_amount* に基づいています。

- **二項分類**:乗車に対してチップが支払われたかどうかを予測します。 つまり、*tip\_amount* が $0 より大きい場合は肯定的な例で、*tip\_amount* が $0 の場合は否定的な例です。

  - クラス 0: tip_amount = $0
  - クラス 1: tip_amount > $0

- **多クラス分類**:乗車で支払われたチップの範囲を予測します。 *tip\_amount* を次の 5 つのクラスに分割します。

  - クラス 0: tip_amount = $0
  - クラス 1: tip_amount > $0 および tip_amount <= $5
  - クラス 2: tip_amount > $5 および tip_amount <= $10
  - クラス 3: tip_amount > $10 および tip_amount <= $20
  - クラス 4: tip_amount > $20

- **回帰タスク**:乗車で支払われたチップの金額を予測します。  

## <a name="set-up-an-hdinsight-hadoop-cluster-for-advanced-analytics"></a><a name="setup"></a>高度な分析用に HDInsight Hadoop クラスターをセットアップする
> [!NOTE]
> 通常、これは管理タスクです。
> 
> 

HDInsight クラスターを使用する高度な分析用の Azure 環境は、次の 3 つの手順でセットアップできます。

1. [ストレージ アカウントの作成](../../storage/common/storage-account-create.md):このストレージ アカウントは、Azure Blob Storage にデータを格納するために使用します。 ここには、HDInsight クラスターで使用するデータも格納されます。
2. [Advanced Analytics Process and Technology 向けに HDInsight Hadoop クラスターをカスタマイズする](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md): この手順では、全ノードに 64 ビットの Anaconda Python 2.7 がインストールされた HDInsight Hadoop クラスターを作成します。 HDInsight クラスターをカスタマイズする際、注意する必要のある 2 つの重要な手順があります。
   
   * 作成時に、手順 1. で作成したストレージ アカウントを HDInsight クラスターにリンクする必要があります。 このストレージ アカウントは、クラスター内で処理されるデータにアクセスします。
   * クラスターを作成したら、クラスターのヘッド ノードへのリモート アクセスを有効にします。 **[構成]** タブに移動して、 **[リモートを有効にする]** を選択します。 この手順で、リモート ログインに使用するユーザーの資格情報を指定します。
3. [Azure Machine Learning ワークスペースを作成する](../classic/create-workspace.md):このワークスペースを使用して、機械学習モデルを構築します。 このタスクは、HDInsight クラスターを使用した初期データの探索とダウンサンプリングの完了後に対処されます。

## <a name="get-the-data-from-a-public-source"></a><a name="getdata"></a>公開されているソースからデータを取得する
> [!NOTE]
> 通常、これは管理タスクです。
> 
> 

公開されている場所から [NYC タクシー乗車](https://www.andresmh.com/nyctaxitrips/)データセットをコンピューターにコピーするには、「[Azure Blob ストレージとの間のデータの移動](move-azure-blob.md)」で説明されている方法のいずれかを使用します。

ここでは、AzCopy を使用してデータを含むファイルを転送する方法について説明します。 AzCopy をダウンロードしてインストールするには、「[AzCopy コマンド ライン ユーティリティの概要](../../storage/common/storage-use-azcopy-v10.md)」に記載されている手順に従います。

1. コマンド プロンプト ウィンドウで次の AzCopy コマンドを実行します。 *\<path_to_data_folder>* を、目的の転送先に置き換えてください。

    ```console
    "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S
    ```

1. コピーが完了すると、選択したデータのフォルダー内に合計 24 個の Zip ファイルが表示されます。 ダウンロードされたファイルをローカル コンピューター上の同じディレクトリに解凍します。 圧縮されていないファイルが存在するフォルダーをメモしておきます。 *\<path\_to\_unzipped_data\_files\>* として参照されるこのフォルダーについては、後ほど説明します。

## <a name="upload-the-data-to-the-default-container-of-the-hdinsight-hadoop-cluster"></a><a name="upload"></a>データを HDInsight Hadoop クラスターの既定のコンテナーにアップロードする
> [!NOTE]
> 通常、これは管理タスクです。
> 
> 

次の AzCopy コマンドでは、Hadoop クラスターを作成してデータ ファイルを解凍したときに指定した実際の値で次のパラメーターを置き換えます。

* "***\<path_to_data_folder>***" を、解凍されたデータ ファイルがあるコンピューター上のディレクトリ (パス) で置き換えます。  
* "***\<storage account name of Hadoop cluster>***" を、HDInsight クラスターに関連付けられているストレージ アカウントで置き換えます。
* "***\<default container of Hadoop cluster>***" を、クラスターで使用される既定のコンテナーで置き換えます。 通常、既定のコンテナーの名前は、クラスター自体と同じ名前です。 たとえば、"abc123.azurehdinsight.net" というクラスターの場合、既定のコンテナーは abc123 です。
* "***\<storage account key>***" を、クラスターで使用するストレージ アカウントのキーで置き換えます。

コマンド プロンプトまたは Windows PowerShell ウィンドウで、次の 2 つの AzCopy コマンドを実行します。

このコマンドは、Hadoop クラスターの既定のコンテナーの ***nyctaxitripraw*** ディレクトリに乗車データをアップロードします。

```console
"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxitripraw /DestKey:<storage account key> /S /Pattern:trip_data_*.csv
```

このコマンドは、Hadoop クラスターの既定のコンテナーの ***nyctaxifareraw*** ディレクトリに料金データをアップロードします。

```console
"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxifareraw /DestKey:<storage account key> /S /Pattern:trip_fare_*.csv
```

これで、データが BLOB ストレージに格納され、HDInsight クラスター内で利用できるようになりました。

## <a name="sign-in-to-the-head-node-of-hadoop-cluster-and-prepare-for-exploratory-data-analysis"></a><a name="#download-hql-files"></a>Hadoop クラスターのヘッド ノードにサインインして探索的データ分析の準備をする
> [!NOTE]
> 通常、これは管理タスクです。
> 
> 

探索的データ分析とデータのダウンサンプリングのためにクラスターのヘッド ノードにアクセスするには、「[Hadoop クラスターのヘッド ノードへのアクセス](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md)」で説明されている手順に従います。

このチュートリアルでは、SQL に似たクエリ言語である [Hive](https://hive.apache.org/)で記述されたクエリを主に使用して、事前のデータ探索を行います。 Hive クエリは ".hql" ファイルに保存されています。 その後、モデル作成のために、Machine Learning で使用されるこのデータをダウンサンプリングします。

探索的データ分析用にクラスターを準備するために、関連する Hive スクリプトを含む ".hql" ファイルを [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) からヘッド ノード上のローカル ディレクトリ (C:\temp) にダウンロードします。 クラスターのヘッド ノードからコマンド プロンプトを開き、次の 2 つのコマンドを実行します。

```console
set script='https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/DataScienceProcess/DataScienceScripts/Download_DataScience_Scripts.ps1'

@powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString(%script%))"
```

この 2 つのコマンドによって、このチュートリアルで必要なすべての ".hql" ファイルが、ヘッド ノード上のローカル ディレクトリ ***C:\temp&#92;*** にダウンロードされます。

## <a name="create-hive-database-and-tables-partitioned-by-month"></a><a name="#hive-db-tables"></a>Hive データベースと月ごとにパーティション分割されたテーブルを作成する
> [!NOTE]
> このタスクは通常、管理者が行います。
> 
> 

これで、NYC タクシー データセットの Hive テーブルを作成する準備ができました。
Hadoop クラスターのヘッド ノードのデスクトップで Hadoop コマンド ラインを開きます。 次のコマンドを実行して Hive ディレクトリを入力します。

```console
cd %hive_home%\bin
```

> [!NOTE]
> このチュートリアルでは、Hive bin/ ディレクトリ プロンプトからすべての Hive コマンドを実行します。 これは自動的にすべてのパスの問題に対処します。 このチュートリアルでは、"Hive ディレクトリ プロンプト"、"Hive bin/ ディレクトリ プロンプト"、"Hadoop コマンド ライン" という用語は同じ意味で使用されます。
> 
> 

Hive ディレクトリ プロンプトから、ヘッド ノードの Hadoop コマンド ラインで次のコマンドを実行します。これにより、Hive データベースとテーブルが作成されます。

```console
hive -f "C:\temp\sample_hive_create_db_and_tables.hql"
```

**C:\temp\sample\_hive\_create\_db\_and\_tables.hql** ファイルの内容を以下に示します。このファイルでは、Hive データベース **nyctaxidb** と、テーブル **trip**、**fare** が作成されます。

```hiveql
create database if not exists nyctaxidb;

create external table if not exists nyctaxidb.trip
(
    medallion string,
    hack_license string,
    vendor_id string,
    rate_code string,
    store_and_fwd_flag string,
    pickup_datetime string,
    dropoff_datetime string,
    passenger_count int,
    trip_time_in_secs double,
    trip_distance double,
    pickup_longitude double,
    pickup_latitude double,
    dropoff_longitude double,
    dropoff_latitude double)  
PARTITIONED BY (month int)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/trip' TBLPROPERTIES('skip.header.line.count'='1');

create external table if not exists nyctaxidb.fare
(
    medallion string,
    hack_license string,
    vendor_id string,
    pickup_datetime string,
    payment_type string,
    fare_amount double,
    surcharge double,
    mta_tax double,
    tip_amount double,
    tolls_amount double,
    total_amount double)
PARTITIONED BY (month int)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/fare' TBLPROPERTIES('skip.header.line.count'='1');
```

この Hive スクリプトでは 2 つのテーブルが作成されます。

* テーブル **trip** には、乗車ごとの詳細 (運転手の詳細、乗車時間、乗車距離、乗車回数) が含まれます。
* テーブル **fare** には、料金の詳細 (料金、チップ、道路などの通行料、追加料金) が含まれます。

これらの手順について追加のサポートが必要な場合、または代わりの手順を調査する必要がある場合は、「[Hive クエリを直接 Hadoop コマンドラインから送信する](move-hive-tables.md#submit)」を参照してください。

## <a name="load-data-to-hive-tables-by-partitions"></a><a name="#load-data"></a>パーティションごとに Hive テーブルにデータを読み込む
> [!NOTE]
> このタスクは通常、管理者が行います。
> 
> 

NYC タクシー データセットは、月ごとに自然にパーティション分割されているので、これを使用することで処理を高速化し、クエリ時間を短縮できます。 次の (Hadoop コマンド ラインを使用して、Hive ディレクトリから発行された) PowerShell コマンドは、月ごとにパーティション分割された trip および fare Hive テーブルにデータを読み込みます。

```powershell
for /L %i IN (1,1,12) DO (hive -hiveconf MONTH=%i -f "C:\temp\sample_hive_load_data_by_partitions.hql")
```

**sample\_hive\_load\_data\_by\_partitions.hql** ファイルには、次の **LOAD** コマンドが含まれています。

```hiveql
LOAD DATA INPATH 'wasb:///nyctaxitripraw/trip_data_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.trip PARTITION (month=${hiveconf:MONTH});
LOAD DATA INPATH 'wasb:///nyctaxifareraw/trip_fare_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.fare PARTITION (month=${hiveconf:MONTH});
```

ここで探索プロセスに使用される多数の Hive クエリは、1 つまたは 2 つのパーティションのみを対象にしています。 これらのクエリをデータセット全体に対して実行することもできます。

### <a name="show-databases-in-the-hdinsight-hadoop-cluster"></a><a name="#show-db"></a>HDInsight Hadoop クラスターのデータベースを表示する
Hadoop コマンド ライン ウィンドウ内に HDInsight Hadoop クラスターで作成されたデータベースを表示するには、Hadoop コマンド ラインで次のコマンドを実行します。

```console
hive -e "show databases;"
```

### <a name="show-the-hive-tables-in-the-nyctaxidb-database"></a><a name="#show-tables"></a>**nyctaxidb** データベースの Hive テーブルを表示する
**nyctaxidb** データベースのテーブルを表示するには、Hadoop コマンドラインで次のコマンドを実行します。

```console
hive -e "show tables in nyctaxidb;"
```

次のコマンドを実行して、テーブルがパーティション分割されていることを確認できます。

```console
hive -e "show partitions nyctaxidb.trip;"
```

次のような内容が出力されます。

```output
month=1
month=10
month=11
month=12
month=2
month=3
month=4
month=5
month=6
month=7
month=8
month=9
Time taken: 2.075 seconds, Fetched: 12 row(s)
```

同様に、次のコマンドを実行して、fare テーブルがパーティション分割されていることを確認できます。

```console
hive -e "show partitions nyctaxidb.fare;"
```

次のような内容が出力されます。

```output
month=1
month=10
month=11
month=12
month=2
month=3
month=4
month=5
month=6
month=7
month=8
month=9
Time taken: 1.887 seconds, Fetched: 12 row(s)
```

## <a name="data-exploration-and-feature-engineering-in-hive"></a><a name="#explore-hive"></a>Hive におけるデータの探索と特徴エンジニアリング
> [!NOTE]
> 通常、これはデータ サイエンティスト タスクです。
> 
> 

Hive クエリを使用すると、Hive テーブルに読み込まれるデータのデータの探索タスクおよび特徴エンジニアリング タスクを実行できます。 このようなタスクの例を次に示します。

* 両方のテーブルの、上位 10 個のレコードを表示する。
* さまざまな期間で、いくつかのフィールドのデータの分布を探索する。
* [経度] フィールドと [緯度] フィールドのデータの品質を調査する。
* チップの金額に基づいて、二項分類および多クラス分類のラベルを生成する。
* 直線乗車距離を計算して、特徴を生成する。

### <a name="exploration-view-the-top-10-records-in-table-trip"></a>探索:trip テーブルの上位 10 個のレコードを表示する
> [!NOTE]
> 通常、これはデータ サイエンティスト タスクです。
> 
> 

データがどのように表示されるかを確認するために、各テーブルから 10 個のレコードを確認します。 レコードを検査するには、Hadoop コマンド ライン コンソールの Hive ディレクトリ プロンプトから次の 2 つのクエリを実行します。

最初の月から trip テーブルの上位 10 個のレコードを取得するには、次のクエリを実行します。

```console
hive -e "select * from nyctaxidb.trip where month=1 limit 10;"
```

最初の月から fare テーブルの上位 10 個のレコードを取得するには、次のクエリを実行します。

```console
hive -e "select * from nyctaxidb.fare where month=1 limit 10;"
```

レコードをファイルに保存しておくと、上記のクエリを少し変更するだけで簡単に表示できます。

```console
hive -e "select * from nyctaxidb.fare where month=1 limit 10;" > C:\temp\testoutput
```

### <a name="exploration-view-the-number-of-records-in-each-of-the-12-partitions"></a>探索:12 個のそれぞれのパーティションのレコードの数を表示する
> [!NOTE]
> 通常、これはデータ サイエンティスト タスクです。
> 
> 

関心があるのは、年間で乗車数がどのように変化するかです。 月ごとにグループ化すると、乗車数の分布が表示されます。

```console
hive -e "select month, count(*) from nyctaxidb.trip group by month;"
```

このコマンドでは次の出力が生成されます。

```output
1       14776615
2       13990176
3       15749228
4       15100468
5       15285049
6       14385456
7       13823840
8       12597109
9       14107693
10      15004556
11      14388451
12      13971118
Time taken: 283.406 seconds, Fetched: 12 row(s)
```

ここでは、最初の列は月で、2 番目の列はその月の乗車数を表しています。

Hive ディレクトリ プロンプトで次のコマンドを実行して、乗車データ セットの合計レコード数をカウントすることもできます。

```console
hive -e "select count(*) from nyctaxidb.trip;"
```

このコマンドの結果は、次のようになります。

```output
173179759
Time taken: 284.017 seconds, Fetched: 1 row(s)
```

乗車データ セットの場合と同様のコマンドを使用して、Hive ディレクトリ プロンプトから Hive クエリを発行し、料金データ セットのレコード数を検証できます。

```console
hive -e "select month, count(*) from nyctaxidb.fare group by month;"
```

このコマンドでは次の出力が生成されます。

```output
1       14776615
2       13990176
3       15749228
4       15100468
5       15285049
6       14385456
7       13823840
8       12597109
9       14107693
10      15004556
11      14388451
12      13971118
Time taken: 253.955 seconds, Fetched: 12 row(s)
```

両方のデータセットについて、まったく同じ 1 か月あたりの乗車数が返されます。これにより、データが正しく読み込まれたことを示す最初の検証が提供されます。

Hive ディレクトリ プロンプトで次のコマンドを実行して、乗車データ セットの合計レコード数をカウントすることもできます。

```console
hive -e "select count(*) from nyctaxidb.fare;"
```

このコマンドの結果は、次のようになります。

```output
173179759
Time taken: 186.683 seconds, Fetched: 1 row(s)
```

両方のテーブルのレコードの合計数も同じであり、データが正しく読み込まれたことを示す 2 番目の検証が提供されます。

### <a name="exploration-trip-distribution-by-medallion"></a>探索:medallion (タクシー番号) ごとの乗車回数の分布
> [!NOTE]
> 通常、この分析はデータ サイエンティスト タスクです。
> 
> 

この例では、指定した期間内で乗車回数が 100 を超える medallion (タクシー番号) を識別します。 このクエリは、パーティション変数 **month** によって条件設定されているので、パーティション テーブルへのアクセスによるメリットが得られます。 クエリ結果は、ヘッド ノード上の `C:\temp` にあるローカル ファイル **queryoutput.tsv** に書き込まれます。

```console
hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv
```

検査用の **sample\_hive\_trip\_count\_by\_medallion.hql** ファイルの内容を次に示します。

```hiveql
SELECT medallion, COUNT(*) as med_count
FROM nyctaxidb.fare
WHERE month<=3
GROUP BY medallion
HAVING med_count > 100
ORDER BY med_count desc;
```

NYC タクシーのデータセット内にある medallion (タクシー番号) は、一意のタクシーを識別します。 特定の期間に特定の乗車数を超えるタクシーを確認することで、比較的忙しいタクシーを特定できます。 次の例では、最初の 3 か月間で乗車回数が 100 を超えるタクシーを識別し、クエリ結果を **C:\temp\queryoutput.tsv** ローカル ファイルに保存します。

検査用の **sample\_hive\_trip\_count\_by\_medallion.hql** ファイルの内容を次に示します。

```hiveql
SELECT medallion, COUNT(*) as med_count
FROM nyctaxidb.fare
WHERE month<=3
GROUP BY medallion
HAVING med_count > 100
ORDER BY med_count desc;
```

Hive ディレクトリ プロンプトから次のコマンドを実行します。

```console
hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv
```

### <a name="exploration-trip-distribution-by-medallion-and-hack-license"></a>探索:medallion および hack_license ごとの乗車回数の分布
> [!NOTE]
> 通常、このタスクはデータ サイエンティストが行います。
> 
> 

データセットの探索時に、値のグループの分布を確認する必要があることがよくあります。 このセクションでは、タクシーと運転手に対してこの分析を行う方法の例を示します。

**sample\_hive\_trip\_count\_by\_medallion\_license.hql** ファイルは、**medallion** と **hack_license** で設定した料金データセットをグループ化し、それぞれの組み合わせの数を返します。 その内容を次に示します。

```hiveql
SELECT medallion, hack_license, COUNT(*) as trip_count
FROM nyctaxidb.fare
WHERE month=1
GROUP BY medallion, hack_license
HAVING trip_count > 100
ORDER BY trip_count desc;
```

このクエリは、タクシーと運転手の組み合わせを乗車回数の多い順に返します。

Hive ディレクトリ プロンプトで次のコマンドを実行します。

```console
hive -f "C:\temp\sample_hive_trip_count_by_medallion_license.hql" > C:\temp\queryoutput.tsv
```

クエリ結果は、ローカル ファイル **C:\temp\queryoutput.tsv** に書き込まれます。

### <a name="exploration-assessing-data-quality-by-checking-for-invalid-longitude-or-latitude-records"></a>探索:無効な経度または緯度のレコードをチェックしてデータの品質を評価する
> [!NOTE]
> 通常、これはデータ サイエンティスト タスクです。
> 
> 

探索的データ分析の共通の目標は、無効または正しくないレコードを除外することです。 このセクションの例では、緯度または経度のいずれかのフィールドに NYC 領域から大きく外れる値が含まれていないかどうかを判断します。 そのようなレコードには緯度と経度の値にエラーがある可能性が高いため、モデリングに使用するすべてのデータからそれらを排除します。

検査用の **sample\_hive\_quality\_assessment.hql** ファイルの内容を次に示します。

```hiveql
    SELECT COUNT(*) FROM nyctaxidb.trip
    WHERE month=1
    AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND -30
    OR    CAST(pickup_latitude AS float) NOT BETWEEN 30 AND 90
    OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND -30
    OR    CAST(dropoff_latitude AS float) NOT BETWEEN 30 AND 90);
```

Hive ディレクトリ プロンプトで次のコマンドを実行します。

```console
hive -S -f "C:\temp\sample_hive_quality_assessment.hql"
```

このコマンドに含まれている引数 *-S* は、Hive の Map/Reduce ジョブの状態の画面出力を抑制します。 このコマンドは、Hive クエリの画面出力を読みやすくするので便利です。

### <a name="exploration-binary-class-distributions-of-trip-tips"></a>探索:チップの二項分類の分布
> [!NOTE]
> 通常、これはデータ サイエンティスト タスクです。
> 
> 

「 [予測タスクの例](hive-walkthrough.md#mltasks) 」で説明されている二項分類の問題では、チップが支払われたかどうかがわかると便利です。 このチップの分布は、次の二項です。

* tip given (Class 1, tip\_amount > $0)  
* no tip (Class 0, tip\_amount = $0)

次の **sample\_hive\_tipped\_frequencies.hql** ファイルは、コマンドの実行内容を示しています。

```hiveql
SELECT tipped, COUNT(*) AS tip_freq
FROM
(
    SELECT if(tip_amount > 0, 1, 0) as tipped, tip_amount
    FROM nyctaxidb.fare
)tc
GROUP BY tipped;
```

Hive ディレクトリ プロンプトで次のコマンドを実行します。

```console
hive -f "C:\temp\sample_hive_tipped_frequencies.hql"
```


### <a name="exploration-class-distributions-in-the-multiclass-setting"></a>探索:多クラス設定での分類分布
> [!NOTE]
> 通常、これはデータ サイエンティスト タスクです。
> 
> 

「[予測タスクの例](hive-walkthrough.md#mltasks)」で説明されている多クラス分類問題についても、このデータ セットは、支払われるチップの金額を予測する自然な分類に役立ちます。 箱を使って、クエリのチップの範囲を定義できます。 さまざまなチップの範囲のクラス分布を取得するには、**sample\_hive\_tip\_range\_frequencies.hql** ファイルを使います。 その内容を次に示します。

```hiveql
SELECT tip_class, COUNT(*) AS tip_freq
FROM
(
    SELECT if(tip_amount=0, 0,
        if(tip_amount>0 and tip_amount<=5, 1,
        if(tip_amount>5 and tip_amount<=10, 2,
        if(tip_amount>10 and tip_amount<=20, 3, 4)))) as tip_class, tip_amount
    FROM nyctaxidb.fare
)tc
GROUP BY tip_class;
```

Hadoop コマンド ライン コンソールから、次のコマンドを実行します。

```console
hive -f "C:\temp\sample_hive_tip_range_frequencies.hql"
```

### <a name="exploration-compute-the-direct-distance-between-two-longitude-latitude-locations"></a>探索:経度緯度の 2 つの場所の直線距離を計算する
> [!NOTE]
> 通常、これはデータ サイエンティスト タスクです。
> 
> 

2 つの場所間の直接距離とタクシーの実際の乗車距離に違いがあるかどうかを知りたい場合があります。 運転手が意図的に長いルートを走行したことに乗客が気づいた場合に、チップが支払われる可能性が低くなる可能性があります。

実際の乗車距離と緯度経度の 2 つの地点の [Haversine distance (球面上の距離)](https://en.wikipedia.org/wiki/Haversine_formula) を比較するために、Hive 内で利用できる次のような三角関数を使用します。

```hiveql
set R=3959;
set pi=radians(180);

insert overwrite directory 'wasb:///queryoutputdir'

select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude, trip_distance, trip_time_in_secs,
${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
 *${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
 *cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
 /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
 +cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*
 pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance
from nyctaxidb.trip
where month=1
and pickup_longitude between -90 and -30
and pickup_latitude between 30 and 90
and dropoff_longitude between -90 and -30
and dropoff_latitude between 30 and 90;
```

上記のクエリで、R は地球の半径 (マイル) を表し、pi はラジアンに変換されます。 緯度経度の 2 つの地点は NYC 領域から大きく外れた値を除外するためにフィルター処理されます。

この場合、**queryoutputdir** というディレクトリに結果を書き込みます。 次の一連のコマンドでは、まずこの出力ディレクトリを作成してから、Hive コマンドを実行します。

Hive ディレクトリ プロンプトで次のコマンドを実行します。

```hiveql
hdfs dfs -mkdir wasb:///queryoutputdir

hive -f "C:\temp\sample_hive_trip_direct_distance.hql"
```

クエリ結果は Hadoop クラスターの既定のコンテナーにある 9 つの Azure BLOB (**queryoutputdir/000000\_0** ～ **queryoutputdir/000008\_0**) に書き込まれます。

個々の BLOB のサイズを表示するには、Hive ディレクトリ プロンプトで次のコマンドを実行します。

```hiveql
hdfs dfs -ls wasb:///queryoutputdir
```

たとえば **000000\_0** などの指定されたファイルの内容を参照するには、Hadoop の `copyToLocal` コマンドを使います。

```hiveql
hdfs dfs -copyToLocal wasb:///queryoutputdir/000000_0 C:\temp\tempfile
```

> [!WARNING]
> ファイル サイズが大きい場合、`copyToLocal` の処理に時間がかかる可能性があるため、大きなファイルではこのコマンドを使用しないことをお勧めします。  
> 
> 

Azure BLOB にこのデータがあることの主な利点は、[データのインポート][import-data] モジュールを使って Machine Learning 内でデータを探索できることです。

## <a name="down-sample-data-and-build-models-in-machine-learning"></a><a name="#downsample"></a>Machine Learning でデータをダウンサンプリングしてモデルを作成する
> [!NOTE]
> 通常、これはデータ サイエンティスト タスクです。
> 
> 

探索的データ分析フェーズが終了したら、Machine Learning でモデルを作成するためにデータをダウンサンプリングできます。 このセクションでは、Hive クエリを使用してデータをダウンサンプリングする方法について説明します。 Machine Learning では、[データのインポート][import-data] モジュールからこのデータにアクセスされます。

### <a name="down-sampling-the-data"></a>データのダウンサンプリング
これには、2 つの手順があります。 まず、すべてのレコードに存在する 3 つのキー (**medallion**、**hack\_license**、**pickup\_datetime**) で、**nyctaxidb.trip** テーブルと **nyctaxidb.fare** テーブルを結合します。 その後、二項分類ラベル **tipped** と多クラス分類ラベル **tip\_class** を作成します。

Machine Learning の[データのインポート][import-data] モジュールからダウンサンプリングされたデータを直接使用できるようにするには、上記のクエリの結果を内部の Hive テーブルに格納する必要があります。 以下では、内部の Hive テーブルを作成し、結合されダウンサンプリングされたデータでその内容を設定します。

このクエリは、標準の Hive 関数を直接適用して **pickup\_datetime** フィールドから以下の時間パラメーターを生成します。
- 時間
- 年度の通算週
- 曜日 ("1" は月曜日、"7" は日曜日)

このクエリで、乗車場所と降車場所間の直接距離も生成されます。 このような関数の一覧については、「[LanguageManual UDF](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF)」を参照してください。

さらに、クエリはデータをダウンサンプリングして、クエリの結果が Azure Machine Learning Studio に適合するようにします。 元のデータセットの約 1% だけが Studio にインポートされます。

Machine Learning でモデルを作成するためのデータを準備する **sample\_hive\_prepare\_for\_aml\_full.hql** ファイルの内容を以下に示します。

```hiveql
set R = 3959;
set pi=radians(180);

create table if not exists nyctaxidb.nyctaxi_downsampled_dataset (

medallion string,
hack_license string,
vendor_id string,
rate_code string,
store_and_fwd_flag string,
pickup_datetime string,
dropoff_datetime string,
pickup_hour string,
pickup_week string,
weekday string,
passenger_count int,
trip_time_in_secs double,
trip_distance double,
pickup_longitude double,
pickup_latitude double,
dropoff_longitude double,
dropoff_latitude double,
direct_distance double,
payment_type string,
fare_amount double,
surcharge double,
mta_tax double,
tip_amount double,
tolls_amount double,
total_amount double,
tipped string,
tip_class string
)
row format delimited fields terminated by ','
lines terminated by '\n'
stored as textfile;

--- now insert contents of the join into the above internal table

insert overwrite table nyctaxidb.nyctaxi_downsampled_dataset
select
t.medallion,
t.hack_license,
t.vendor_id,
t.rate_code,
t.store_and_fwd_flag,
t.pickup_datetime,
t.dropoff_datetime,
hour(t.pickup_datetime) as pickup_hour,
weekofyear(t.pickup_datetime) as pickup_week,
from_unixtime(unix_timestamp(t.pickup_datetime, 'yyyy-MM-dd HH:mm:ss'),'u') as weekday,
t.passenger_count,
t.trip_time_in_secs,
t.trip_distance,
t.pickup_longitude,
t.pickup_latitude,
t.dropoff_longitude,
t.dropoff_latitude,
t.direct_distance,
f.payment_type,
f.fare_amount,
f.surcharge,
f.mta_tax,
f.tip_amount,
f.tolls_amount,
f.total_amount,
if(tip_amount>0,1,0) as tipped,
if(tip_amount=0,0,
if(tip_amount>0 and tip_amount<=5,1,
if(tip_amount>5 and tip_amount<=10,2,
if(tip_amount>10 and tip_amount<=20,3,4)))) as tip_class

from
(
select
medallion,
hack_license,
vendor_id,
rate_code,
store_and_fwd_flag,
pickup_datetime,
dropoff_datetime,
passenger_count,
trip_time_in_secs,
trip_distance,
pickup_longitude,
pickup_latitude,
dropoff_longitude,
dropoff_latitude,
${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
*${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
*cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
/sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
+cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance,
rand() as sample_key

from nyctaxidb.trip
where pickup_latitude between 30 and 90
    and pickup_longitude between -90 and -30
    and dropoff_latitude between 30 and 90
    and dropoff_longitude between -90 and -30
)t
join
(
select
medallion,
hack_license,
vendor_id,
pickup_datetime,
payment_type,
fare_amount,
surcharge,
mta_tax,
tip_amount,
tolls_amount,
total_amount
from nyctaxidb.fare
)f
on t.medallion=f.medallion and t.hack_license=f.hack_license and t.pickup_datetime=f.pickup_datetime
where t.sample_key<=0.01
```

このクエリは、Hive ディレクトリ プロンプトで次のように実行します。

```console
hive -f "C:\temp\sample_hive_prepare_for_aml_full.hql"
```

これで、Machine Learning で [データのインポート][import-data] モジュールを使ってアクセスできる内部テーブル **nyctaxidb.nyctaxi_downsampled_dataset** ができました。 さらに、このデータセットを使って Machine Learning モデルを作成できます。  

### <a name="use-the-import-data-module-in-machine-learning-to-access-the-down-sampled-data"></a>Machine Learning のデータのインポート モジュールを使用して、ダウンサンプリングされたデータにアクセスする
Machine Learning の[データのインポート][import-data] モジュールで Hive クエリを発行するには、Machine Learning ワークスペースにアクセスする必要があります。 また、クラスターの資格情報とクラスターに関連付けられたストレージ アカウントにアクセスする必要もあります。

[データのインポート][import-data] モジュールと入力するパラメーターの詳細は次のとおりです。

**HCatalog サーバー URI**:クラスター名が **abc123** の場合は、https:\//abc123.azurehdinsight.net を使用します。

**Hadoop ユーザーのアカウント名**:クラスターに選択したユーザー名 (リモート アクセスのユーザー名ではありません)。

**Hadoop ユーザー アカウントのパスワード**:クラスターに選択したパスワード (リモート アクセスのパスワードではありません)。

**出力データの場所**:Azure になるように選択されます。

**Azure ストレージ アカウント名**: クラスターに関連付けられている既定のストレージ アカウント名。

**Azure コンテナー名**:クラスターの既定のコンテナー名。通常はクラスター名と同じです。 **abc123** というクラスターの場合、名前は abc123 になります。

> [!IMPORTANT]
> Machine Learning で[データのインポート][import-data] モジュールを使ってクエリするすべてのテーブルは、内部テーブルである必要があります。
> 
> 

**D.db** データベース内のテーブル **T** が内部テーブルかどうかを判断する方法を次に示します。 Hive ディレクトリ プロンプトから次のコマンドを実行します。

```hiveql
hdfs dfs -ls wasb:///D.db/T
```

テーブルが内部テーブルであり、テーブルにデータが設定されている場合は、その内容がここで表示される必要があります。

テーブルが内部テーブルかどうかを判断する別の方法は、Azure Storage Explorer を使用することです。 Azure Storage Explorer を使用してクラスターの既定のコンテナー名に移動し、テーブル名でフィルターします。 テーブルとその内容が表示されれば、内部テーブルであることがわかります。

Hive クエリと[データのインポート][import-data] モジュールのスクリーンショットを次に示します。

![データのインポート モジュールの Hive クエリのスクリーンショット](./media/hive-walkthrough/1eTYf52.png)

ダウンサンプリングされたデータは既定のコンテナーに格納されているため、Machine Learning からの結果の Hive クエリはシンプルになります。 単に **SELECT * FROM nyctaxidb.nyctaxi\_downsampled\_data** となります。

このデータセットを Machine Learning モデル作成のための出発点として使用できます。

### <a name="build-models-in-machine-learning"></a><a name="mlmodel"></a>Machine Learning でモデルを作成する
これで、[Machine Learning](https://studio.azureml.net) でのモデルの作成とモデルのデプロイに進む準備が整いました。 データも、以前に特定した予測問題への対応に使用できる状態になりました。

- **二項分類**:乗車に対してチップが支払われたかどうかを予測します。

  **使用する学習者:** 2 クラスのロジスティック回帰

  a. この問題では、ターゲット (またはクラス) ラベルは **tipped** です。 元のダウンサンプリングされたデータセットには、この分類実験用のターゲット リークであるいくつかの列があります。 具体的には、**tip\_class**、**tip\_amount**、**total\_amount** では、テスト時に利用できないターゲット ラベルについての情報が表示されます。 ここでは、[データセット内の列の選択][select-columns]モジュールを使ってこれらの列を考慮事項から除外します。

  次のダイアグラムは、特定の乗車でチップが支払われたかどうかを予測するための実験を示しています。

  ![チップが支払われたかどうかを予測する実験のダイアグラム](./media/hive-walkthrough/QGxRz5A.png)

  b. この実験では、ターゲット ラベルの分布がほぼ 1:1 です。

   次のグラフは、二項分類の問題のための、チップのクラス ラベルの分布を示しています。

  ![チップのクラス ラベルの分布グラフ](./media/hive-walkthrough/9mM4jlD.png)

    その結果、次の図に示す 0.987 の曲線下面積 (AUC) を取得します。

  ![ACU 値のグラフ](./media/hive-walkthrough/8JDT0F8.png)

- **多クラス分類**:以前に定義したクラスを使用して、乗車で支払われたチップの金額の範囲を予測します。

  **使用する学習者:** 多クラスのロジスティック回帰

  a. この問題では、ターゲット (またはクラス) ラベルは、5 つの値 (0,1,2,3,4) のいずれかを取ることができる **tip\_class** になります。 二項分類の場合と同様に、この実験用のターゲット リークであるいくつかの列があります。 具体的には、**tipped**、**tip\_amount**、**total\_amount** では、テスト時に利用できないターゲット ラベルについての情報が表示されます。 ここでは、[データセット内の列の選択][select-columns] モジュールを使ってこれらの列を削除します。

  次のダイアグラムは、チップが分類される可能性が高い箱を予測する実験を示しています。 ビンは、クラス 0: チップ = $0、クラス 1: チップ > $0 および チップ <= $5、クラス 2: チップ > $5 および チップ <= $10、クラス 3: チップ > $10 および チップ <= $20、クラス 4: チップ > $20 です。

  ![チップのビンを予測する実験のダイアグラム](./media/hive-walkthrough/5ztv0n0.png)

  実際のテスト クラスの分布がどのようになるかを次に示します。 クラス 0 とクラス 1 は一般的ですが、他のクラスはまれであることがわかります。

  ![テスト クラスの分布グラフ](./media/hive-walkthrough/Vy1FUKa.png)

  b. この実験では、次に示すように混同行列を使用して予測精度を確認します。

  ![混同行列](./media/hive-walkthrough/cxFmErM.png)

  一般的なクラスのクラス精度が良い一方で、そのモデルはまれなクラスでは "学習" がうまくいっていません。

- **回帰タスク**:乗車で支払われたチップの金額を予測します。

  **使用する学習者:** ブースト デシジョン ツリー

  a. この問題では、ターゲット (またはクラス) ラベルは **tip\_amount** です。 この場合のターゲット リークは、**tipped**、**tip\_class**、**total\_amount** です。 これらの変数はすべて、通常はテスト時に利用できないチップの金額についての情報を表示します。 ここでは、[データセット内の列の選択][select-columns] モジュールを使ってこれらの列を削除します。

  次のダイアグラムは、支払われるチップの金額を予測する実験を示しています。

  ![チップの金額を予測する実験のダイアグラム](./media/hive-walkthrough/11TZWgV.png)

  b. 回帰の問題については、予測の二乗誤差や決定係数を確認することで、予測の精度を測定します。

  ![予測の統計情報のスクリーンショット](./media/hive-walkthrough/Jat9mrz.png)

  決定係数は約 0.709 であり、これは分散の約 71% がモデル係数によって説明されることを意味します。

> [!IMPORTANT]
> Machine Learning の詳細と Machine Learning にアクセスして使用する方法の詳細については、「[Machine Learning とは](../classic/index.yml)」を参照してください。 また、[Azure AI ギャラリー](https://gallery.cortanaintelligence.com/)では、すべての実験についての説明があり、Machine Learning の機能範囲が詳しく説明されています。
> 
> 

## <a name="license-information"></a>ライセンス情報
このサンプルのチュートリアルとそれに付随するスクリプトは、MIT ライセンスの下で Microsoft と共有されています。 詳細については、GitHub のサンプル コードのディレクトリにある **LICENSE.txt** ファイルを参照してください。

## <a name="references"></a>References
•    [Andrés Monroy NYC タクシー乗車データ ダウンロード ページ](https://www.andresmh.com/nyctaxitrips/)  
•    [NYC のタクシー乗車データを FOIL する (Chris Whong)](https://chriswhong.com/open-data/foil_nyc_taxi/)   
•    [ニューヨーク市タクシー&リムジン委員会調査および統計](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)

[2]: ./media/hive-walkthrough/output-hive-results-3.png
[11]: ./media/hive-walkthrough/hive-reader-properties.png
[12]: ./media/hive-walkthrough/binary-classification-training.png
[13]: ./media/hive-walkthrough/create-scoring-experiment.png
[14]: ./media/hive-walkthrough/binary-classification-scoring.png
[15]: ./media/hive-walkthrough/amlreader.png

<!-- Module References -->
[select-columns]: /azure/machine-learning/studio-module-reference/select-columns-in-dataset
[import-data]: /azure/machine-learning/studio-module-reference/import-data