---
title: 1 TB のデータセットで Azure HDInsight Hadoop クラスターを使用する - Team Data Science Process
description: HDInsight Hadoop クラスターを用いたエンド ツー エンドのシナリオに Team Data Science Process を使用し、公開されている大量 (1 TB) のデータセットを使ってモデルを構築してデプロイします
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
ms.openlocfilehash: e66bd0a4e56f63185d8361355d6cf8e0e29bc30b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "93305938"
---
# <a name="the-team-data-science-process-in-action---using-an-azure-hdinsight-hadoop-cluster-on-a-1-tb-dataset"></a>Team Data Science Process の活用 - 1 TB データセットでの Azure HDInsight Hadoop クラスターの使用

このチュートリアルでは、[Azure HDInsight Hadoop クラスター](https://azure.microsoft.com/services/hdinsight/)を使用するエンド ツー エンドのシナリオで、Team Data Science Process を使って、公開されている [Criteo](https://labs.criteo.com/downloads/download-terabyte-click-logs/) データセットの 1 つからデータを格納、調査し、特徴エンジニアリングとダウンサンプリングを行う方法について説明します。 このチュートリアルでは、Azure Machine Learning を使用して、このデータに基づいてバイナリ分類モデルを構築します。 また、こうしたモデルのいずれかを Web サービスとして発行する方法も示します。

IPython Notebook を使用して、このチュートリアルで説明するタスクを実行することもできます。 この方法を試してみたい方は、「 [Criteo walkthrough using a Hive ODBC connection (Hive ODBC の接続を使用した Criteo チュートリアル)](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb) 」のトピックをご覧ください。

## <a name="criteo-dataset-description"></a><a name="dataset"></a>Criteo データセットの説明
Criteo データは、gzip で圧縮された 370 GB の TSV ファイル (非圧縮で ～ 1.3 TB) のクリック予測データセットで、43 億以上のレコードが含まれています。 これは、 [Criteo](https://labs.criteo.com/downloads/download-terabyte-click-logs/)によって提供される 24 日間のクリック データから取得されます。 データ サイエンティストが使いやすいように、実験で使用できるデータは解凍されています。

このデータセットの各レコードには、40 の列があります。

* 最初の列は、ユーザーが **追加** をクリックする (値 1) またはクリックしない (値 0) ことを示すラベル列です。
* 次の 13 の列は数値列です。
* 最後の 26 の列はカテゴリ列です。

列は匿名で、一連の列挙型名を使用します。"Col1" (ラベル列) から "Col40" (最後のカテゴリ列) までです。

次に、このデータセットから最初の 20 列の 2 つの観測 (行) を抜粋して示します。

> `Col1    Col2    Col3    Col4    Col5    Col6    Col7    Col8    Col9    Col10    Col11    Col12    Col13    Col14    Col15            Col16            Col17            Col18            Col19        Col20`
>
> `0       40      42      2       54      3       0       0       2       16      0       1       4448    4       1acfe1ee        1b2ff61f        2e8b2631        6faef306        c6fc10d3    6fcd6dcb`
>
> `0               24              27      5               0       2       1               3       10064           9a8cb066        7a06385f        417e6103        2170fc56        acf676aa    6fcd6dcb`

不足値がこのデータセットの数値列とカテゴリ列の両方にあります。 不足値を処理する単純なメソッドが説明されています。 データの詳細については、Hive テーブルにデータを格納するときに説明します。

**定義**:*クリックスルー率 (CTR):* このメトリックは、データ内のクリックの割合です。 この Criteo データセットの CTR は、約 3.3% または 0.033 です。

## <a name="examples-of-prediction-tasks"></a><a name="mltasks"></a>予測タスクの例
このチュートリアルでは、次の 2 つのサンプルの予測問題に対処します。

1. **二項分類**:ユーザーが追加をクリックしたかどうかを予測します。

   * クラス 0:クリックしない
   * クラス 1:Click
2. **回帰**:ユーザーの特徴から広告クリックの確率を予測します。

## <a name="set-up-an-hdinsight-hadoop-cluster-for-data-science"></a><a name="setup"></a>データ サイエンス用に HDInsight Hadoop クラスターをセット アップする
> [!NOTE]
> 通常、この手順は、**管理者** タスクです。

HDInsight クラスターを使用して予測分析ソリューションを構築するために、3 つの手順で Azure のデータ サイエンス環境をセット アップします。

1. [ストレージ アカウントの作成](../../storage/common/storage-account-create.md):このストレージ アカウントは、Azure Blob Storage にデータを格納するために使用します。 ここには、HDInsight クラスターで使用するデータが格納されます。
2. [Azure HDInsight Hadoop Clusters for Data Science のカスタマイズ](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md):この手順では、全ノードに 64 ビットの Anaconda Python 2.7 がインストールされた Azure HDInsight Hadoop クラスターを作成します。 HDInsight クラスターをカスタマイズするときに実行する、2 つの重要な手順があります (このトピックで説明します)。

   * 作成時に、手順 1 で作成したストレージ アカウントを HDInsight クラスターにリンクします。 このストレージ アカウントは、クラスター内で処理可能なデータにアクセスするために使用されます。
   * 作成したら、クラスターのヘッド ノードへのリモート アクセスを有効にします。 ここで指定するリモート アクセス資格情報を覚えておいてください (クラスターの作成時に指定した資格情報とは異なります)。次の手順を完了します。
3. [Azure Machine Learning Studio (クラシック) ワークスペースを作成する](../classic/create-workspace.md)この Azure Machine Learning ワークスペースは、HDInsight クラスターでの初期データの探索とダウンサンプリング後に、機械学習モデルを構築するために使用します。

## <a name="get-and-consume-data-from-a-public-source"></a><a name="getdata"></a>公開されているソースからデータを取得して使用する
[Criteo](https://labs.criteo.com/downloads/download-terabyte-click-logs/) データセットにアクセスするには、リンクをクリックし、使用条件に同意して名前を入力します。 次にスナップショットを示します。

![Criteo 条項に同意する](./media/hive-criteo-walkthrough/hLxfI2E.png)

**[Continue to Download (ダウンロードの続行)]** をクリックしてデータセットとその可用性に関する詳細をお読みください。

このデータは [Azure Blob Storage](../../storage/blobs/storage-quickstart-blobs-dotnet.md) の wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/ にあります。 "wasb" は、Azure BLOB ストレージの場所を表します。

1. この Azure Blob Storage 内のデータは、解凍されたデータの 3 つのサブフォルダーで構成されています。

   1. サブフォルダー *raw/count/* には、最初の 21 日間 (day\_00 から day\_20 まで) のデータが含まれています。
   2. サブフォルダー *raw/train/* には、1 日分 (day\_21) のデータが含まれています。
   3. サブフォルダー *raw/test/* には、2 日間 (day\_22 と day\_23) のデータが含まれています。
2. 生の gzip データは、メイン フォルダー *raw/* 内で day_NN.gz (NN は 00 から 23) として使用することもできます。

ローカルのダウンロードを必要としない別の手法でこのデータにアクセスして探索し、モデリングする方法については、Hive テーブルを作成するときに、このチュートリアルの後半で説明します。

## <a name="log-in-to-the-cluster-headnode"></a><a name="login"></a>クラスター ヘッドノードにログインする
クラスターのヘッドノードにログインするには、 [Azure Portal](https://ms.portal.azure.com) を使用してクラスターを検索します。 左側の HDInsight の象のアイコンをクリックし、クラスターの名前をダブルクリックします。 **[構成]** タブに移動し、ページの下部にある [接続] アイコンをダブルクリックし、プロンプトが表示されたら、リモート アクセスの資格情報を入力すると、クラスターのヘッドノードに移動します。

クラスター ヘッドノードに初めてログインすると、通常、次のような画面が表示されます。

![Log in to cluster](./media/hive-criteo-walkthrough/Yys9Vvm.png)

左側には "Hadoop コマンド ライン" があります。これは、データ探索用のマイクロソフトの主力製品です。 2 つの便利な URL、"Hadoop Yarn の状態" と "Hadoop 名前ノード" に注意してください。 Yarn ステータスの URL はジョブの進行状況を示し、名前ノードの URL はクラスター構成の詳細を示します。

これで、チュートリアルの最初の部分 (Hive を使用したデータ探索と Azure Machine Learning のデータの準備) を設定して開始する準備ができました。

## <a name="create-hive-database-and-tables"></a><a name="hive-db-tables"></a> Hive データベースとテーブルを作成する
Criteo データセットの Hive テーブルを作成するには、ヘッド ノードのデスクトップで ***Hadoop コマンド ライン*** を開き、次のコマンドを入力して Hive ディレクトリに入ります。

```console
cd %hive_home%\bin
```

> [!NOTE]
> このチュートリアルでは、Hive bin/ ディレクトリ プロンプトからすべての Hive コマンドを実行します。 これは自動的にすべてのパスの問題に対処します。 "Hive ディレクトリ プロンプト"、"Hive bin/ ディレクトリ プロンプト"、"Hadoop コマンド ライン" という用語は同じ意味で使用できます。
>
> [!NOTE]
> Hive クエリを実行するには、常に次のコマンドを使用できます。
>
> `cd %hive_home%\bin`
> `hive`

Hive REPL が "hive >" 記号と共に表示されたら、単にクエリをカット アンド ペーストして実行します。

以下のコードでは、データベース "criteo" を作成した後、次の 4 つのテーブルが生成されます。

* day\_00 ～ day\_20 の期間に構築された、*カウントを生成する 1 つのテーブル*。
* day\_21 に構築された、*トレーニング データベースとして使用する 1 つのテーブル*。
* それぞれ day\_22 と day\_23 に構築された、*テスト データセットとして使用する 2 つのテーブル*。

いずれかの日が休日であるため、テスト データセットを 2 つの異なるテーブルに分割します。 その目的は、休日と休日以外の違いを、モデルがクリックスルー率から検出できるかどうかを判断することです。

スクリプト [sample&#95;hive&#95;create&#95;criteo&#95;database&#95;and&#95;tables.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql) を以下に示します。

```hiveql
CREATE DATABASE IF NOT EXISTS criteo;
DROP TABLE IF EXISTS criteo.criteo_count;
CREATE TABLE criteo.criteo_count (
col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
LINES TERMINATED BY '\n'
STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/count';

DROP TABLE IF EXISTS criteo.criteo_train;
CREATE TABLE criteo.criteo_train (
col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
LINES TERMINATED BY '\n'
STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/train';

DROP TABLE IF EXISTS criteo.criteo_test_day_22;
CREATE TABLE criteo.criteo_test_day_22 (
col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
LINES TERMINATED BY '\n'
STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/test/day_22';

DROP TABLE IF EXISTS criteo.criteo_test_day_23;
CREATE TABLE criteo.criteo_test_day_23 (
col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
LINES TERMINATED BY '\n'
STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/test/day_23';
```

こうしたテーブルはすべて外部にあるため、これらの Azure Blob Storage (wasb) の場所をポイントできます。

**任意の Hive クエリを実行する方法は 2 つあります。**

* **Hive REPL コマンド ラインを使用する**:最初の方法は "hive" コマンドを発行し、Hive REPL コマンド ラインでクエリをコピーして貼り付けることです。

  ```console
  cd %hive_home%\bin
  hive
  ```

     REPL コマンド ラインで、クエリをカット アンド ペースして実行します。
* **ファイルにクエリを保存し、コマンドを実行する**:2 番目の方法は '.hql' ファイル ([sample&#95;hive&#95;create&#95;criteo&#95;database&#95;and&#95;tables.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql)) にクエリを保存し、次のコマンドを発行してクエリを実行することです。

  ```console
  hive -f C:\temp\sample_hive_create_criteo_database_and_tables.hql
  ```

### <a name="confirm-database-and-table-creation"></a>データベースとテーブルの作成を確認する
次に、Hive bin/ ディレクトリ プロンプトから次のコマンドを使用してデータベースの作成を確認します。

```console
hive -e "show databases;"
```

次のように表示されます。

```output
criteo
default
Time taken: 1.25 seconds, Fetched: 2 row(s)
```

これは、新しいデータベース "criteo" の作成を確認します。

作成されたテーブルを表示するには、Hive bin/ ディレクトリ プロンプトから次のコマンドを発行するだけです。

```output
hive -e "show tables in criteo;"
```

すると、次の出力が表示されます。

```ouput
criteo_count
criteo_test_day_22
criteo_test_day_23
criteo_train
Time taken: 1.437 seconds, Fetched: 4 row(s)
```

## <a name="data-exploration-in-hive"></a><a name="exploration"></a> Hive でデータを探索する
これで、Hive でいくつかの基本的なデータ探索を行う準備ができました。 まず、トレーニングとテストのデータ テーブルでサンプル数をカウントすることから始めます。

### <a name="number-of-train-examples"></a>トレーニングのサンプル数
[sample&#95;hive&#95;count&#95;train&#95;table&#95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_train_table_examples.hql) の内容を以下に示します。

```hiveql
SELECT COUNT(*) FROM criteo.criteo_train;
```

次が生成されます。

```output
192215183
Time taken: 264.154 seconds, Fetched: 1 row(s)
```

代わりに、Hive bin/ ディレクトリ プロンプトから次のコマンドを発行することもできます。

```console
hive -f C:\temp\sample_hive_count_criteo_train_table_examples.hql
```

### <a name="number-of-test-examples-in-the-two-test-datasets"></a>2 つのテスト データセットのテスト サンプル数
ここで、2 つのテスト データセットのサンプル数をカウントします。 [sample&#95;hive&#95;count&#95;criteo&#95;test&#95;day&#95;22&#95;table&#95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_22_table_examples.hql) の内容を以下に示します。

```hiveql
SELECT COUNT(*) FROM criteo.criteo_test_day_22;
```

次が生成されます。

```output
189747893
Time taken: 267.968 seconds, Fetched: 1 row(s)
```

通常どおり、次のコマンドを発行して Hive bin/ ディレクトリ プロンプトからスクリプトを呼び出すこともできます。

```console
hive -f C:\temp\sample_hive_count_criteo_test_day_22_table_examples.hql
```

最後に、day\_23 に基づくテスト データセット内のテスト サンプル数を確認します。

このコマンドは上記と同様です ([sample&#95;hive&#95;count&#95;criteo&#95;test&#95;day&#95;23&#95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_23_examples.hql) をご覧ください)。

```hiveql
SELECT COUNT(*) FROM criteo.criteo_test_day_23;
```

次のように表示されます。

```output
178274637
Time taken: 253.089 seconds, Fetched: 1 row(s)
```

### <a name="label-distribution-in-the-train-dataset"></a>トレーニング データセット内のラベルの分布
対象はトレーニング データセット内のラベルの分布です。 これを参照するために、[sample&#95;hive&#95;criteo&#95;label&#95;distribution&#95;train&#95;table.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_label_distribution_train_table.hql) の内容を表示します。

```hiveql
SELECT Col1, COUNT(*) AS CT FROM criteo.criteo_train GROUP BY Col1;
```

ラベルの分布が生成されます。

```output
1       6292903
0       185922280
Time taken: 459.435 seconds, Fetched: 2 row(s)
```

正の値のラベルの割合は約 3.3% です (元のデータセットと一致します)。

### <a name="histogram-distributions-of-some-numeric-variables-in-the-train-dataset"></a>トレーニング データセット内の一部の数値変数のヒストグラム分布
Hive のネイティブの "histogram\_numeric" 関数を使用して、数値変数の分布がどのように表示されるかを確認できます。 [sample&#95;hive&#95;criteo&#95;histogram&#95;numeric.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_histogram_numeric.hql) の内容を次に示します。

```hiveql
SELECT CAST(hist.x as int) as bin_center, CAST(hist.y as bigint) as bin_height FROM
    (SELECT
    histogram_numeric(col2, 20) as col2_hist
    FROM
    criteo.criteo_train
    ) a
    LATERAL VIEW explode(col2_hist) exploded_table as hist;
```

次のように生成されます。

```output
26      155878415
2606    92753
6755    22086
11202   6922
14432   4163
17815   2488
21072   1901
24113   1283
27429   1225
30818   906
34512   723
38026   387
41007   290
43417   312
45797   571
49819   428
53505   328
56853   527
61004   160
65510   3446
Time taken: 317.851 seconds, Fetched: 20 row(s)
```

Hive の LATERAL VIEW - explode の組み合わせは、通常のリストの代わりに、SQL に似た出力を生成する機能があります。 この表では、最初の列は bin center に対応し、2 番目の列は bin frequency に対応しています。

### <a name="approximate-percentiles-of-some-numeric-variables-in-the-train-dataset"></a>トレーニング データセット内の一部の数値変数の百分位を概算する
概算の百分位の計算も数値変数の対象です。 Hive のネイティブ "percentile\_approx" がこれを実行します。 [sample&#95;hive&#95;criteo&#95;approximate&#95;percentiles.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_approximate_percentiles.hql) の内容を以下に示します。

```hiveql
SELECT MIN(Col2) AS Col2_min, PERCENTILE_APPROX(Col2, 0.1) AS Col2_01, PERCENTILE_APPROX(Col2, 0.3) AS Col2_03, PERCENTILE_APPROX(Col2, 0.5) AS Col2_median, PERCENTILE_APPROX(Col2, 0.8) AS Col2_08, MAX(Col2) AS Col2_max FROM criteo.criteo_train;
```

次が生成されます。

```output
1.0     2.1418600917169246      2.1418600917169246    6.21887086390288 27.53454893115633       65535.0
Time taken: 564.953 seconds, Fetched: 1 row(s)
```

百分位の分布は、通常、任意の数値変数のヒストグラム分布に密接に関連しています。

### <a name="find-number-of-unique-values-for-some-categorical-columns-in-the-train-dataset"></a>トレーニング データセット内の一部のカテゴリ列の一意の値の数を検索する
データの探索を続けて、一部のカテゴリ列の一意の値を確認します。 これを行うために、[sample&#95;hive&#95;criteo&#95;unique&#95;values&#95;categoricals.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_categoricals.hql) の内容を表示します。

```hiveql
SELECT COUNT(DISTINCT(Col15)) AS num_uniques FROM criteo.criteo_train;
```

次が生成されます。

```output
19011825
Time taken: 448.116 seconds, Fetched: 1 row(s)
```

Col15 には 19 M 個の一意の値があります。 "one-hot エンコーディング" のような単純な手法を使用して、このような高次元カテゴリ変数をエンコードすることはできません。 特に、この問題に効率的に取り組むために、[カウントを使用した学習](/archive/blogs/machinelearning/big-learning-made-easy-with-counts)と呼ばれる、強力で信頼性の高い手法が示されています。

最後に、他のカテゴリ列の一意の値の数も確認します。 [sample&#95;hive&#95;criteo&#95;unique&#95;values&#95;multiple&#95;categoricals.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_multiple_categoricals.hql) の内容を以下に示します。

```hiveql
SELECT COUNT(DISTINCT(Col16)), COUNT(DISTINCT(Col17)),
COUNT(DISTINCT(Col18), COUNT(DISTINCT(Col19), COUNT(DISTINCT(Col20))
FROM criteo.criteo_train;
```

次が生成されます。

```output
30935   15200   7349    20067   3
Time taken: 1933.883 seconds, Fetched: 1 row(s)
```

ここでも、Col20 以外のすべての列に、一意の値が多数含まれていることに注意してください。

### <a name="co-occurrence-counts-of-pairs-of-categorical-variables-in-the-train-dataset"></a>トレーニング データセット内のカテゴリ変数ペアの同時発生カウント

カテゴリ変数ペアの数の分布も対象です。 これは、[sample&#95;hive&#95;criteo&#95;paired&#95;categorical&#95;counts.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_paired_categorical_counts.hql) 内のコードを使用して判断できます。

```hiveql
SELECT Col15, Col16, COUNT(*) AS paired_count FROM criteo.criteo_train GROUP BY Col15, Col16 ORDER BY paired_count DESC LIMIT 15;
```

この場合、発生カウントの順序を逆にして、上位 15 を確認します。 次のように表示されます。

```output
ad98e872        cea68cd3        8964458
ad98e872        3dbb483e        8444762
ad98e872        43ced263        3082503
ad98e872        420acc05        2694489
ad98e872        ac4c5591        2559535
ad98e872        fb1e95da        2227216
ad98e872        8af1edc8        1794955
ad98e872        e56937ee        1643550
ad98e872        d1fade1c        1348719
ad98e872        977b4431        1115528
e5f3fd8d        a15d1051        959252
ad98e872        dd86c04a        872975
349b3fec        a52ef97d        821062
e5f3fd8d        a0aaffa6        792250
265366bf        6f5c7c41        782142
Time taken: 560.22 seconds, Fetched: 15 row(s)
```

## <a name="down-sample-the-datasets-for-azure-machine-learning"></a><a name="downsample"></a> Azure Machine Learning のデータセットのダウンサンプリング
データセットを探索し、この種類の任意の変数 (組み合わせを含む) 探索方法を示したら、Azure Machine Learning にモデルを構築できるように、データ セットをダウンサンプリングします。 問題の焦点を思い出してください。属性のサンプル セット (Col2 ～ Col40 の特徴値) がある場合、Col1 が 0 (クリックしない) か、1 (クリックする) かを予測します。

トレーニングとテストのデータセットを元のサイズの 1% にダウンサンプリングするには、Hive のネイティブ RAND() 関数を使用します。 次のスクリプト [sample&#95;hive&#95;criteo&#95;downsample&#95;train&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_train_dataset.hql) は、トレーニング データセット対して実行します。

```hiveql
CREATE TABLE criteo.criteo_train_downsample_1perc (
col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
LINES TERMINATED BY '\n'
STORED AS TEXTFILE;

---Now downsample and store in this table

INSERT OVERWRITE TABLE criteo.criteo_train_downsample_1perc SELECT * FROM criteo.criteo_train WHERE RAND() <= 0.01;
```

次が生成されます。

```output
Time taken: 12.22 seconds
Time taken: 298.98 seconds
```

スクリプト [sample&#95;hive&#95;criteo&#95;downsample&#95;test&#95;day&#95;22&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_22_dataset.hql) は、day\_22 のテスト データに対し実行します。

```hiveql
--- Now for test data (day_22)

CREATE TABLE criteo.criteo_test_day_22_downsample_1perc (
col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
LINES TERMINATED BY '\n'
STORED AS TEXTFILE;

INSERT OVERWRITE TABLE criteo.criteo_test_day_22_downsample_1perc SELECT * FROM criteo.criteo_test_day_22 WHERE RAND() <= 0.01;
```

次が生成されます。

```output
Time taken: 1.22 seconds
Time taken: 317.66 seconds
```

最後に、スクリプト [sample&#95;hive&#95;criteo&#95;downsample&#95;test&#95;day&#95;23&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_23_dataset.hql) は、day\_23 のテスト データに対し実行します。

```hiveql
--- Finally test data day_23
CREATE TABLE criteo.criteo_test_day_23_downsample_1perc (
col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 srical feature; tring)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
LINES TERMINATED BY '\n'
STORED AS TEXTFILE;

INSERT OVERWRITE TABLE criteo.criteo_test_day_23_downsample_1perc SELECT * FROM criteo.criteo_test_day_23 WHERE RAND() <= 0.01;
```

次が生成されます。

```output
Time taken: 1.86 seconds
Time taken: 300.02 seconds
```

これで、ダウンサンプリングされたトレーニングとテストのデータセットを使用して Azure Machine Learning でモデルを構築する準備ができました。

Azure Machine Learning に移動する前に、カウント テーブルに関する最後の重要なコンポーネントがあります。 次のサブセクションでは、カウント テーブルについて詳しく説明します。

## <a name="a-brief-discussion-on-the-count-table"></a><a name="count"></a> カウント テーブルの簡単な説明
ご覧になったように、カテゴリ変数のいくつかは高次元です。 チュートリアルでは、こうした変数を効率的かつ堅牢な方法でエンコードする、[カウントを使用した学習](/archive/blogs/machinelearning/big-learning-made-easy-with-counts)と呼ばれる強力な手法について説明します。 この手法の詳細については、リンクをクリックしてください。

>[!NOTE]
>このチュートリアルでは、カウント テーブルを使用して、高次元のカテゴリ特徴のコンパクトな表現を生成することに重点を置いています。 これは、カテゴリ特徴をエンコードする唯一の方法ではありません。関心のある方は、他の手法の詳細について [one-hot エンコーディング](https://en.wikipedia.org/wiki/One-hot)に関するページと[特徴ハッシュ](https://en.wikipedia.org/wiki/Feature_hashing)に関するページをご覧ください。
>

カウント データのカウント テーブルを作成するには、raw/count フォルダーのデータを使用します。 モデリング セクションでは、カテゴリ特徴のカウント テーブルを最初から構築する方法と、探索のために事前に構築されたカウント テーブルを使用する別の方法を示します。 以下で "事前に構築されたカウント テーブル" が言及されている場合は、提供されているカウント テーブルを使用することを意味します。 これらのテーブルにアクセスする方法の詳細については、次のセクションで説明します。

## <a name="build-a-model-with-azure-machine-learning"></a><a name="aml"></a> Azure Machine Learning でモデルをビルドする
Azure Machine Learning のモデル構築プロセスは、次の手順を実行します。

1. [Azure Machine Learning に Hive テーブルからデータを取得する](#step1)
2. [実験を作成する: データを整理してカウント テーブルを使用して特徴付ける](#step2)
3. [モデルを構築し、トレーニングしてスコアを付ける](#step3)
4. [モデルを評価する](#step4)
5. [モデルを Web サービスとして発行する](#step5)

これで、Azure Machine Learning Studio でモデルを構築する準備ができました。 ダウンサンプリングされたデータは、クラスター内に Hive テーブルとして保存されます。 このデータを読み取るには、Azure Machine Learning の **データのインポート** モジュールを使用します。 このクラスターのストレージ アカウントにアクセスするための資格情報は、以下のとおりです。

### <a name="step-1-get-data-from-hive-tables-into-azure-machine-learning-using-the-import-data-module-and-select-it-for-a-machine-learning-experiment"></a><a name="step1"></a> ステップ 1:データのインポート モジュールを使用して、Azure Machine Learning に Hive テーブルからデータを取得し、機械学習の実験用に選ぶ
まず **[+新規]**  ->  **[実験]**  ->  **[空の実験]** を選択します。 次に、左上の **[検索]** ボックスで "データのインポート" を検索します。 **データのインポート** モジュールを実験キャンバス (画面の中央) にドラッグ アンド ドロップして、データ アクセスにモジュールを使用します。

**データのインポート** は、Hive テーブルからデータを取得中に次のように表示されます。

![データのインポートがデータを取得する](./media/hive-criteo-walkthrough/i3zRaoj.png)

**データのインポート** モジュールのグラフィックに指定されているパラメーターの値は、入力する必要がある値の例です。 ここでは、 **データのインポート** モジュールのパラメーター セットを入力する方法の一般的なガイダンスを示します。

1. [Hive クエリ] を **データ ソース**
2. **[Hive データベース クエリ]** ボックスでは、SELECT * FROM <your\_database\_name.your\_table\_name> のみで十分です。
3. **Hcatalog サーバー URI**:クラスターが "abc" の場合は、単に https:\//abc.azurehdinsight.net となります。
4. **Hadoop ユーザーのアカウント名**:クラスターの設定時に選択したユーザー名。 (リモート アクセスのユーザー名ではありません)。
5. **Hadoop ユーザー アカウントのパスワード**:クラスターの設定時に選択した上記のユーザー名のパスワード。 (リモート アクセスのパスワードではありません)。
6. **出力データの場所**:[Azure] を選択します
7. **Azure ストレージ アカウント名**:クラスターに関連付けられているストレージ アカウント
8. **Azure Storage アカウント キー**:クラスターに関連付けられているストレージ アカウントのキー。
9. **Azure コンテナー名**:クラスター名が "abc" の場合、通常は単に "abc" となります。

**データのインポート** がデータの取得を終了したら (モジュールに緑色のチェック マークが表示されます)、このデータをデータセットとして保存します (任意の名前を付けます)。 次のように表示されます。

![データのインポートがデータを保存する](./media/hive-criteo-walkthrough/oxM73Np.png)

**データのインポート** モジュールの出力ポートを右クリックすると、 **[データセットとして保存]** オプションと **[視覚化]** オプションが表示されます。 **[視覚化]** オプションをクリックすると、概要統計情報に役立つ 100 行のデータが右側のパネルに表示されます。 データを保存するには、 **[データセットとして保存]** を選択して指示に従います。

機械学習の実験用で使用する保存済みのデータセットを選ぶには、次の図に示す **[検索]** ボックスを使用してデータセットを検索します。 データセットに付けた名前の一部を入力してデータセットにアクセスし、このデータセットをメイン パネルにドラッグします。 メイン パネル上にドロップしたら、機械学習のモデリングで使用するために選択します。

![データ セットをメイン パネル上にドラッグする](./media/hive-criteo-walkthrough/cl5tpGw.png)

> [!NOTE]
> トレーニングとテストの両方のデータセットでこれを行います。 また、この用途で付けたデータベース名とテーブル名を必ず使用してください。 図の値は、説明の目的でのみ使用されている例です。\*\*
>
>

### <a name="step-2-create-an-experiment-in-azure-machine-learning-to-predict-clicks--no-clicks"></a><a name="step2"></a> ステップ 2:Azure Machine Learning で実験を作成して、クリックするかクリックしないかを予測します
Azure Machine Learning Studio (クラシック) の実験は、次のようになります。

![Machine Learning の実験](./media/hive-criteo-walkthrough/xRpVfrY.png)

次に、この実験の主要コンポーネントを見てみましょう。 まず、保存したトレーニング データセットとテスト データセットを実験キャンバスにドラッグします。

#### <a name="clean-missing-data"></a>見つからないデータのクリーンアップ
**見つからないデータのクリーンアップ** モジュールは、その名前のとおり、ユーザーが指定できる方法で不足しているデータをクリーンアップします。 このモジュールは次のように表示されます。

![見つからないデータのクリーンアップ](./media/hive-criteo-walkthrough/0ycXod6.png)

ここでは、すべての不足値を 0 に置き換えることにします。 他のオプションは、モジュールのドロップダウン リストに表示されます。

#### <a name="feature-engineering-on-the-data"></a>データに対する特徴エンジニアリング
カテゴリ型の特徴を持つ大規模なデータセットの場合、数百万単位の一意の値が含まれる可能性があります。 このような高次元のカテゴリ型の特徴を表現する場合、ワンホット エンコーディングなどの単純な方法を使用することは完全に不可能です。 このチュートリアルでは、組み込みの Azure Machine Learning モジュールを利用するカウント特徴を使い、このような高次元のカテゴリ型変数をコンパクトな表現で生成する方法について説明します。 最終的に、モデル サイズは小さくなり、トレーニング時間は短縮され、他の技術を使用する場合と同等のパフォーマンス メトリックになります。

##### <a name="building-counting-transforms"></a>カウント変換の構築
カウント特徴を構築するために、Azure Machine Learning で利用できる **カウント変換の構築** モジュールを使用します。 次のようなモジュールです。

![カウント変換の構築モジュール プロパティ](./media/hive-criteo-walkthrough/e0eqKtZ.png)
![カウント変換の構築モジュール](./media/hive-criteo-walkthrough/OdDN0vw.png)

> [!IMPORTANT]
> **[Count columns]\(カウント列\)** ボックスに、カウントを実行する列を入力します。 通常、これらは (前述した) 高次元カテゴリ列です。 Criteo データセットには Col15 から Col40 の 26 列のカテゴリ列があることに注意してください。 ここでは、そのすべての列を使用し、インデックスを設定します (以下に示すように、コンマで区切られた 15 から 40 の数値)。
>

(大規模なデータセットに適している) MapReduce モードでモジュールを使用するには、HDInsight Hadoop クラスター (特徴の探索に使用したクラスターをこの用途に再利用することもできます) へのアクセス権とその資格情報が必要です。 前の図は、入力する値の例です (図に指定されている値は、実際の状況に合わせて置き換えてください)。

![モジュールのパラメーター](./media/hive-criteo-walkthrough/05IqySf.png)

前の図は、入力 BLOB の場所を入力する方法を示しています。 この場所には、カウント テーブルを構築するために予約されているデータがあります。

このモジュールの実行の完了時に、モジュールを右クリックし、 **[変換として保存]** オプションを選択すると、変換を後で使用するために保存できます。

![[変換として保存] オプション](./media/hive-criteo-walkthrough/IcVgvHR.png)

前述の実験アーキテクチャで説明したように、データセット "ytransform2" は保存したカウント変換と正確に対応しています。 以降のこの実験では、読者が何らかのデータに対して **カウント変換の構築** モジュールを使用してカウントを生成したと想定して話を進めます。このようなカウントを使用すると、トレーニング データセットとテスト データセットに対してカウント特徴を生成できます。

##### <a name="choosing-what-count-features-to-include-as-part-of-the-train-and-test-datasets"></a>トレーニング データセットとテスト データセットの一部に含めるカウント特徴を選択する
カウント変換が準備できたら、**カウント テーブル パラメーターの変更** モジュールを使用して、トレーニング データセットとテスト データセットに含める特徴を選択できます。 このモジュールは、完全を期すためにここで紹介していますが、 わかりやすくするために、実際には実験で使用しません。

![カウント テーブル パラメーターの変更](./media/hive-criteo-walkthrough/PfCHkVg.png)

この場合は、ご覧のように log-odds が使用され、バック オフ列は無視されています。 また、ごみ箱のしきい値、平滑化のために追加する擬似的な過去の例の数、Laplacian ノイズを使用するかどうかなど、パラメーターを設定することもできます。 これらはいずれも高度な特徴です。このような特徴を初めて生成する場合は、この既定値を参考に利用することができます。

##### <a name="data-transformation-before-generating-the-count-features"></a>カウント特徴を生成する前のデータ変換
ここでは、実際にカウント特徴を生成する前の、トレーニング データおよびテスト データ変換に関する重要ポイントに焦点を当てます。 カウント変換がデータに適用される前に、2 つの **R スクリプトの実行** モジュールが使用されます。

![R スクリプトの実行モジュール](./media/hive-criteo-walkthrough/aF59wbc.png)

1 つ目の R スクリプトは次のとおりです。

![最初の R スクリプト](./media/hive-criteo-walkthrough/3hkIoMx.png)

この R スクリプトは、列の名前を "Col1" から "Col40" に変更します。 これは、カウント変換には、この形式の名前が使用されるためです。

2 つ目の R スクリプトは、負のクラスをダウンサンプリングして、正のクラスと負のクラス (クラス 1 と 0) の分布のバランスを取ります。 次の R スクリプトは、その方法を示しています。

![2 つ目の R スクリプト](./media/hive-criteo-walkthrough/91wvcwN.png)

この単純な R のスクリプトでは、正のクラスと負のクラスのバランス量の設定に "pos\_neg\_ratio" が使用されます。 クラスの不均衡を改善すると、通常、クラスの分布が偏っているという分類の問題がある場合にパフォーマンスが改善されるため、この処理は重要です (正のクラスが 3.3%、負のクラスが 96.7% の例を思い出してください)。

##### <a name="applying-the-count-transformation-on-our-data"></a>カウント変換をデータに適用する
最後に、**変換の適用** モジュールを使用すると、トレーニング データセットとテスト データセットにカウント変換を適用できます。 このモジュールは、1 つの入力として保存済みのカウント変換を使用し、もう 1 つの入力としてトレーニング データセットまたはテスト データセットを使用して、カウント特徴を含むデータを返します。 次に例を示します。

![変換の適用モジュール](./media/hive-criteo-walkthrough/xnQvsYf.png)

##### <a name="an-excerpt-of-what-the-count-features-look-like"></a>カウント特徴の概要を示す抜粋
この例のカウント特徴がどのような内容か見てみましょう。 この抜粋を次に示します。

![カウント特徴](./media/hive-criteo-walkthrough/FO1nNfw.png)

この抜粋では、カウント対象の列について、関連するバックオフだけでなく、カウントと対数オッズを取得しています。

この変換後のデータセットを使用して、Azure Machine Learning モデルを構築する準備ができました。 次のセクションでは、その方法を説明します。

### <a name="step-3-build-train-and-score-the-model"></a><a name="step3"></a> ステップ 3:モデルを構築し、トレーニングしてスコアを付ける

#### <a name="choice-of-learner"></a>学習者の選択
まず、学習者を選択する必要があります。 2 つのクラスのブースト デシジョン ツリーを学習者として使用します。 この学習者の既定のオプションを次に示します。

![2 つのクラスのブースト デシジョン ツリーのパラメーター](./media/hive-criteo-walkthrough/bH3ST2z.png)

実験のため、既定値を選択します。 既定値には意味があり、パフォーマンスの簡単なベースラインを効果的に取得するのに適しています。 ベースラインがあれば、パラメーターを整理してパフォーマンスを改善できます。

#### <a name="train-the-model"></a>モデルをトレーニングする
トレーニングのために、単純に **モデルのトレーニング** モジュールを呼び出します。 2 つの入力は、2 クラス ブースト デシジョン ツリー学習者とトレーニング データセットです。 次に例を示します。

![モデルのトレーニング モジュール](./media/hive-criteo-walkthrough/2bZDZTy.png)

#### <a name="score-the-model"></a>モデルにスコアを付ける
トレーニング済みのモデルを用意すると、テスト データセットにスコアを付け、パフォーマンスを評価することができます。 それには、次の図に示す **モデルのスコア付け** モジュールと、**モデルの評価** モジュールを使用します。

![スコア モデル モジュール](./media/hive-criteo-walkthrough/fydcv6u.png)

### <a name="step-4-evaluate-the-model"></a><a name="step4"></a> 手順 4:モデルを評価する
最後に、モデルのパフォーマンスを分析します。 通常、2 つのクラス (二項) 分類の問題の効果的な手段は、AUC です。 この曲線を視覚化するには、**モデルのスコア付け** モジュールを **モデルの評価** モジュールに関連付けます。 **モデルの評価** モジュールで **[視覚化]** をクリックすると、次のようなグラフィックが表示されます。

![評価モジュール BDT モデル](./media/hive-criteo-walkthrough/0Tl0cdg.png)

二項 (または 2 つのクラス) 分類の問題で、予測精度の効果的な手段は 曲線下面積 (AUC) です。 次のセクションでは、テスト データセットでこのモデルを使用して、結果を示します。 **モデルの評価** モジュールの出力ポートを右クリックし、 **[視覚化]** をクリックします。

![視覚化のモデルの評価モジュール](./media/hive-criteo-walkthrough/IRfc7fH.png)

### <a name="step-5-publish-the-model-as-a-web-service"></a><a name="step5"></a> 手順 5:モデルを Web サービスとして発行する
最小限の労力で Azure Machine Learning モデルを Web サービスとして発行することは、モデルを広く利用できるようにするために重要です。 発行すると、誰でも予測が必要な入力データを使用して Web サービスを呼び出すことができます。また、Web サービスはそのモデルを使用して予測を返します。

まず、**モデルのトレーニング** モジュールを右クリックし、 **[トレーニング済みのモデルとして保存する]** オプションを使用して、トレーニング済みのモデルをトレーニング済みモデル オブジェクトとして保存します。

次に、Web サービスの入力ポートと出力ポートを作成します。

* 入力ポートは、予測が必要なデータと同じ形式のデータを受け入れます。
* 出力ポートは、スコア付けラベルと関連する確率を返します。

#### <a name="select-a-few-rows-of-data-for-the-input-port"></a>入力ポートにいくつかのデータ行を選択する
**Apply SQL 変換** モジュールを使用して、入力ポート データとして機能するように 10 行のみを選択できます。 次のように、SQL クエリを使用して、入力ポート用にこれらの行のデータのみを選択します。

![入力ポート データ](./media/hive-criteo-walkthrough/XqVtSxu.png)

#### <a name="web-service"></a>Web サービス
これで、Web サービスの発行のために使用できる小規模の実験を行う準備ができました。

#### <a name="generate-input-data-for-webservice"></a>Web サービスの入力データを生成する
手順 0 として、カウント テーブルが大きいため、いくつかの行のテスト データを取り出し、カウント特徴を使用して、そのテスト データから出力データを生成します。 この出力は、次に示すように、Web サービスの入力データ形式として機能します。

![BDT 入力データを作成する](./media/hive-criteo-walkthrough/OEJMmst.png)

> [!NOTE]
> 入力データ形式には、**カウント Featurizer** モジュールの出力を使用します。 この実験が完了したら、 **カウント Featurizer** モジュールから出力をデータセットとして保存します。 このデータセットは、Web サービスの入力データに使用されます。
>
>

#### <a name="scoring-experiment-for-publishing-webservice"></a>Web サービスの公開のためのスコア付け実験
まず、重要な構造は、**カウント Featurizer** モジュールを使用して前の手順で生成された、いくつかの行の入力データとトレーニング済みのモデル オブジェクトを受け入れる **モデルのスコア付け** モジュールです。 "データセット内の列の選択" を使用して、スコア付けラベルとスコア付け確率を予測します。

![データセット内の列の選択](./media/hive-criteo-walkthrough/kRHrIbe.png)

**データセット内の列の選択** モジュールを使用してデータセットのデータを絞り込む方法に注目してください。 以下に内容を示します。

![データセット内の列の選択モジュールでフィルター処理する](./media/hive-criteo-walkthrough/oVUJC9K.png)

青色の入力ポートと出力ポートを取得するには、右下にある **[prepare webservice]** をクリックします。 この実験中に、右下にある **[Web サービスの発行]** アイコンをクリックして、Web サービスを公開することもできます。

![[Web サービスの発行]](./media/hive-criteo-walkthrough/WO0nens.png)

Web サービスが公開されると、次のようなページにリダイレクトされます。

![Web サービス ダッシュボード](./media/hive-criteo-walkthrough/YKzxAA5.png)

左側に表裏される Web サービス用の 2 つのリンクに注意してください。

* **要求/応答** サービス (RRS) は、1 つの予測を対象としたサービスで、このワークショップで使用されています。
* **バッチの実行** サービス (BES) バッチ予測を使用し、Azure Blob ストレージに存在する予測を行う、入力データを使用する必要があります。

**[REQUEST/RESPONSE]** リンクをクリックすると、C#、python、R で作成されたコードを紹介するページに移動します。そのコードを使用して、Web サービスの呼び出しを手間なく行うことができます。 このページの API キーを認証に使用する必要があります。

次の python コードを IPython Notebook の新しいセルにコピーすると便利です。

以下に Python コードのセグメントと適切な API キーを示します。

![Python コード](./media/hive-criteo-walkthrough/f8N4L4g.png)

既定の API キーが、Web サービスの API キーで置き換えられています。 IPython Notebook のこのセルで、 **[実行]** をクリックすると、次の応答が返されます。

![iPython の応答](./media/hive-criteo-walkthrough/KSxmia2.png)

Python スクリプトの JSON フレームワークで質問された 2 つのテスト サンプルに対して、"スコア付けラベル, スコア付け確率" の形式で回答が返されました。 この場合は、事前に定義されたコードによって提供された既定値が選択されています (すべての数値列が 0、すべてのカテゴリ列が文字列 "value" です)。

以上で、Azure Machine Learning を使用して大規模なデータセットを処理する方法を示すチュートリアルは終了です。 ここでは、テラバイト単位のデータから始めて、予測モデルを構築し、クラウドに Web サービスとしてデプロイしました。