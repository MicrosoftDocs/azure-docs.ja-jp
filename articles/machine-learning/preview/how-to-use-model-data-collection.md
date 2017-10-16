---
title: "Azure Machine Learning Workbench で モデル管理データ収集機能を使用する方法 | Microsoft Docs"
description: "このドキュメントでは、Azure Machine Learning Workbench でモデル管理データ収集機能を使用する方法について説明します。"
services: machine-learning
author: aashishb
ms.author: aashishb
manager: neerajkh
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 09/12/2017
ms.openlocfilehash: e751e2f0ec812de43a03749e04ff165fa62ec649
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-collect-model-data-using-data-collection"></a>データ収集を使用してモデル データを収集する方法

モデル データ収集機能を使用すると、モデルの入力と Machine Learning Web サービスからの予測をアーカイブできます。 このドキュメントでは、モデル データ収集の次の局面について説明します。

- データ収集パッケージをインストールする方法
- データ収集の使用方法
- 収集したデータを表示および使用する方法

## <a name="how-to-install-the-data-collection-package"></a>データ収集パッケージのインストール方法
データ収集ライブラリは、Linux および Windows にネイティブにインストールできます。

### <a name="windows"></a>Windows
Windows では、次のコマンドを使用してデータ コレクター モジュールをインストールできます。

    pip install azureml.datacollector

### <a name="linux"></a>Linux
Linux では、最初に libxml++ library をインストールする必要があります。 次のコマンドを実行します。このコマンドは、sudo で実行する必要があります。

    sudo apt-get install libxml++2.6-2v5

続いて、次のコマンドを実行します。

    pip install azureml.datacollector
## <a name="how-to-use-data-collection"></a>データ収集の使用方法

モデル データ収集を使用するには、スコア付けファイルに次の変更を加える必要があります。

1. ファイルの先頭に次のコードを追加します。
   
    ```python
    from azureml.datacollector import ModelDataCollector
    ```

2. `init()` 関数に次のコード行を追加します。
    
    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector('model.pkl',identifier="inputs")
    prediction_dc = ModelDataCollector('model.pkl', identifier="prediction")
    ```

3. `run(input_df)` 関数に次のコード行を追加します。
    
    ```python
    global inputs_dc, prediction_dc
    inputs_dc.collect(input_df)
    prediction_dc.collect(pred)
    ```

    `collect()` を呼び出す前に、変数 `input_df` および `pred` (`model.predict()` からの予測値) が初期化されていることを確認してください。

4. `--collect-model-data true` を指定して `az ml service create realtime` コマンドを使用し、リアルタイムの Web サービスを作成します。 これにより、サービスが実行されるときに必ずモデル データが収集されるようになります。

     ```batch
    c:\temp\myIris> az ml service create realtime -f iris_score.py --model-file model.pkl -s service_schema.json -n irisapp -r python --collect-model-data true 
    ```
    
5. `az ml service run realtime` を実行してデータ収集をテストします。

    ```
    C:\Temp\myIris> az ml service run realtime -i irisapp -d "ADD YOUR INPUT DATA HERE!!" 
    ``` 
    
## <a name="how-to-view-and-consume-the-collected-data"></a>収集したデータを表示および使用する方法
収集したデータを BLOB ストレージに表示するには、次の手順に従います。

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. **[その他のサービス]** をクリックします。
3. 検索ボックスに「`Storage accounts`」と入力し、**Enter** キーを押します。
4. **[ストレージ アカウント]** 検索ブレードで、**[ストレージ アカウント]** リソースを選択します。 目的のストレージ アカウントを特定するには、次の手順に従います。

    a. Azure ML Workbench に移動して作業中のプロジェクトを選択し、**[ファイル]** メニューからコマンドライン プロンプトを開きます。
    
    b. 「`az ml env show -v`」と入力し、*storage_account* の値を調べます。 それがストレージ アカウントの名前です。

5. リソース ブレードのメニューで **[コンテナー]** をクリックし、**modeldata** という名前のコンテナーをクリックします。 最初の Web サービス要求の後、ストレージ アカウントにデータが反映され始めるまでに最大 10 分程度かかる場合があります。 データが次のコンテナー パスで BLOB に取り込まれます。

    `/modeldata/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<day>/data.csv`

データは、Microsoft のソフトウェアやオープン ソースのツールを含め、さまざまな方法で Azure BLOB から使用することができます。 次に例をいくつか示します。
 - Azure ML Workbench - Azure ML Workbench で csv ファイルをデータ ソースとして追加して開く
 - Excel - 日々の csv ファイルをスプレッドシートとして開く
 - [Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-azure-and-power-bi/) - BLOB の csv データからプルしたデータを使ってグラフを作成する
 - [Spark](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-apache-spark-overview) - csv データの大部分を含んだデータフレームを作成する
    ```python
    var df = spark.read.format("com.databricks.spark.csv").option("inferSchema","true").option("header","true").load("wasb://modeldata@<storageaccount>.blob.core.windows.net/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<date>/*")
    ```
- [Hive](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-tutorial-get-started) - csv データを Hive テーブルに読み込み、BLOB に対して直接 SQL クエリを実行する

