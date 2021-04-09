---
title: チュートリアル:Synapse Studio で Apache Spark ジョブ定義を作成する
description: チュートリアル - Azure Synapse Analytics を使用して Spark ジョブ定義を作成し、Apache Spark for Azure Synapse Analytics のプールに送信します。
author: Jejiang
ms.author: jejiang
ms.reviewer: jasonh
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 10/16/2020
ms.openlocfilehash: d125bca5ed67476897eec7cd32a586776d8b1ea8
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102176622"
---
# <a name="tutorial-create-apache-spark-job-definition-in-synapse-studio"></a>チュートリアル:Synapse Studio で Apache Spark ジョブ定義を作成する

このチュートリアルでは、Azure Synapse Studio を使用して Apache Spark ジョブ定義を作成し、サーバーレス Apache Spark プールに送信する方法を示します。

このチュートリアルに含まれるタスクは次のとおりです。
> [!div class="checklist"]
>
> - PySpark (Python) 用の Apache Spark ジョブ定義を作成する
> - Spark (Scala) 用の Apache Spark ジョブ定義を作成する
> - .NET Spark (C# または F#) 用の Apache Spark ジョブ定義を作成する
> - Apache Spark ジョブ定義をバッチ ジョブとして送信する
> - Apache Spark ジョブ定義をパイプラインに追加する

## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に、次の要件を満たしてください。

* Azure Synapse Analytics ワークスペース。 手順については、[Azure Synapse Analytics ワークスペースの作成](../../machine-learning/how-to-manage-workspace.md)に関するページを参照してください。
* サーバーレス Apache Spark プール。
* ADLS Gen2 ストレージ アカウント。 使用する ADLS Gen2 ファイル システムの **ストレージ BLOB データ共同作成者** である必要があります。 そうでない場合は、手動でアクセス許可を追加する必要があります。
* ワークスペースの既定のストレージを使用したくない場合は、必要な ADLS Gen2 ストレージ アカウントを Synapse Studio でリンクしてください。 

## <a name="create-an-apache-spark-job-definition-for-pyspark-python"></a>PySpark (Python) 用の Apache Spark ジョブ定義を作成する

このセクションでは、PySpark (Python) 用の Apache Spark ジョブ定義を作成します。

1. [Azure Synapse Studio](https://web.azuresynapse.net/) を開きます。

2. [Apache Spark ジョブ定義を作成するためのサンプル ファイル](https://github.com/Azure-Samples/Synapse/tree/master/Spark/Python)に移動して、**python.zip のサンプル ファイル** をダウンロードし、圧縮パッケージを解凍して、**wordcount.py** ファイルと **shakespeare.txt** ファイルを抽出します。 

     ![サンプル ファイル](./media/apache-spark-job-definitions/sample-files.png)

3. **[データ]**  ->  **[Linked]\(リンク済み\)**  ->  **[Azure Data Lake Storage Gen2]** の順に選択し、**wordcount.py** と **shakespeare.txt** を ADLS Gen2 ファイル システムにアップロードします。 

     ![python ファイルのアップロード](./media/apache-spark-job-definitions/upload-python-file.png)

4. **[開発]** ハブを選択し、[+] アイコンを選択して **[Spark job definition]\(Spark ジョブ定義\)** を選択し、新しい Spark ジョブ定義を作成します。 

     ![Python 用の新しい定義の作成](./media/apache-spark-job-definitions/create-new-definition.png)

5. Apache Spark ジョブ定義のメイン ウィンドウの [言語] ドロップ ダウン リストから **[PySpark (Python)]** を選択します。

     ![Python を選択する](./media/apache-spark-job-definitions/select-python.png)

6. Apache Spark ジョブ定義の情報を入力します。 

     |  プロパティ   | 説明   |  
     | ----- | ----- |  
     |Job definition name (ジョブ定義名)| Apache Spark ジョブ定義の名前を入力します。 この名前は公開されるまでいつでも更新できます。 <br> サンプル: `job definition sample`|
     |Main definition file (メイン定義ファイル)| ジョブに使用されるメイン ファイルです。 ストレージから PY ファイルを選択します。 **[ファイルのアップロード]** を選択して、ファイルをストレージ アカウントにアップロードできます。 <br> サンプル: `abfss://…/path/to/wordcount.py`|
     |コマンド ライン引数| ジョブに対する省略可能な引数。 <br> サンプル: `abfss://…/path/to/shakespeare.txt` `abfss://…/path/to/result` <br> *注意事項: サンプル ジョブ定義の 2 つの引数はスペースで区切ります。*|
     |参照ファイル| メイン定義ファイル内で参照に使用される追加ファイル。 **[ファイルのアップロード]** を選択して、ファイルをストレージ アカウントにアップロードできます。 |
     |Spark プール| 選択した Apache Spark プールにジョブが送信されます。|
     |Spark のバージョン| Apache Spark プールが実行されている Apache Spark のバージョン。|
     |エグゼキュータ| ジョブ用の指定された Apache Spark プール内で提供される Executor の数。|
     |Executor size (エグゼキュータのサイズ)| ジョブ用の指定された Apache Spark プール内で提供される、Executor に使用するコアとメモリの数。|  
     |Driver size (ドライバー サイズ)| ジョブ用の指定された Apache Spark プール内で提供される、ドライバーに使用するコアとメモリの数。|

     ![Python 用の Spark ジョブ定義の値の設定](./media/apache-spark-job-definitions/create-py-definition.png)

7. **[公開]** を選択して、Apache Spark ジョブ定義を保存します。

     ![py 定義の公開](./media/apache-spark-job-definitions/publish-py-definition.png)

## <a name="create-an-apache-spark-job-definition-for-apache-sparkscala"></a>Apache Spark (Scala) 用の Apache Spark ジョブ定義を作成する

このセクションでは、Apache Spark (Scala) 用の Apache Spark ジョブ定義を作成します。

 1. [Azure Synapse Studio](https://web.azuresynapse.net/) を開きます。

 2. [Apache Spark ジョブ定義を作成するためのサンプル ファイル](https://github.com/Azure-Samples/Synapse/tree/master/Spark/Scala)に移動して、**scala.zip のサンプル ファイル** をダウンロードし、圧縮パッケージを解凍して、**wordcount.jar** ファイルと **shakespeare.txt** ファイルを抽出します。 
 
     ![サンプル ファイル scala](./media/apache-spark-job-definitions/sample-files-scala.png)

 3. **[データ]**  ->  **[Linked]\(リンク済み\)**  ->  **[Azure Data Lake Storage Gen2]** の順に選択し、**wordcount.jar** と **shakespeare.txt** を ADLS Gen2 ファイル システムにアップロードします。
 
     ![Scala 構造の準備](./media/apache-spark-job-definitions/prepare-scala-structure.png)

 4. **[開発]** ハブを選択し、[+] アイコンを選択して **[Spark job definition]\(Spark ジョブ定義\)** を選択し、新しい Spark ジョブ定義を作成します。 (サンプル画像は、**PySpark (Python) 用の Apache Spark ジョブ定義を作成する** 方法に関するセクションの手順 4. と同じです。)

 5. Apache Spark ジョブ定義のメイン ウィンドウの [言語] ドロップ ダウン リストから **[Spark (Scala)]** を選択します。

     ![Scala を選択する](./media/apache-spark-job-definitions/select-scala.png)

 6. Apache Spark ジョブ定義の情報を入力します。 サンプル情報をコピーできます。

     |  プロパティ   | 説明   |  
     | ----- | ----- |  
     |Job definition name (ジョブ定義名)| Apache Spark ジョブ定義の名前を入力します。 この名前は公開されるまでいつでも更新できます。 <br> サンプル: `scala`|
     |Main definition file (メイン定義ファイル)| ジョブに使用されるメイン ファイルです。 ストレージから JAR ファイルを選択します。 **[ファイルのアップロード]** を選択して、ファイルをストレージ アカウントにアップロードできます。 <br> サンプル: `abfss://…/path/to/wordcount.jar`|
     |メイン クラス名| 完全修飾識別子またはメイン定義ファイル内のメイン クラス。 <br> サンプル: `WordCount`|
     |コマンド ライン引数| ジョブに対する省略可能な引数。 <br> サンプル: `abfss://…/path/to/shakespeare.txt` `abfss://…/path/to/result` <br> *注意事項: サンプル ジョブ定義の 2 つの引数はスペースで区切ります。* |
     |参照ファイル| メイン定義ファイル内で参照に使用される追加ファイル。 **[ファイルのアップロード]** を選択して、ファイルをストレージ アカウントにアップロードできます。|
     |Spark プール| 選択した Apache Spark プールにジョブが送信されます。|
     |Spark のバージョン| Apache Spark プールが実行されている Apache Spark のバージョン。|
     |エグゼキュータ| ジョブ用の指定された Apache Spark プール内で提供される Executor の数。|  
     |Executor size (エグゼキュータのサイズ)| ジョブ用の指定された Apache Spark プール内で提供される、Executor に使用するコアとメモリの数。|
     |Driver size (ドライバー サイズ)| ジョブ用の指定された Apache Spark プール内で提供される、ドライバーに使用するコアとメモリの数。|

     ![Scala 用の Spark ジョブ定義の値の設定](./media/apache-spark-job-definitions/create-scala-definition.png)

 7. **[公開]** を選択して、Apache Spark ジョブ定義を保存します。

      ![Scala 定義の公開](./media/apache-spark-job-definitions/publish-scala-definition.png)

## <a name="create-an-apache-spark-job-definition-for-net-sparkcf"></a>.NET Spark (C# または F#) 用の Apache Spark ジョブ定義を作成する

このセクションでは、.NET Spark (C# または F#) 用の Apache Spark ジョブ定義を作成します。
 1. [Azure Synapse Studio](https://web.azuresynapse.net/) を開きます。

 2. [Apache Spark ジョブ定義を作成するためのサンプル ファイル](https://github.com/Azure-Samples/Synapse/tree/master/Spark/DotNET)に移動して、**dotnet.zip のサンプル ファイル** をダウンロードし、圧縮パッケージを解凍して、**wordcount.zip** ファイルと **shakespeare.txt** ファイルを抽出します。 

     ![サンプル dotnet](./media/apache-spark-job-definitions/sample-dotnet.png)

 3. **[データ]**  ->  **[Linked]\(リンク済み\)**  ->  **[Azure Data Lake Storage Gen2]** の順に選択し、**wordcount.zip** と **shakespeare.txt** を ADLS Gen2 ファイル システムにアップロードします。
 
     ![dotnet 構造の準備](./media/apache-spark-job-definitions/prepare-dotnet-structure.png)

 4. **[開発]** ハブを選択し、[+] アイコンを選択して **[Spark job definition]\(Spark ジョブ定義\)** を選択し、新しい Spark ジョブ定義を作成します。 (サンプル画像は、**PySpark (Python) 用の Apache Spark ジョブ定義を作成する** 方法に関するセクションの手順 4. と同じです。)

 5. Apache Spark ジョブ定義のメイン ウィンドウの [言語] ドロップ ダウン リストから **[.NET Spark (C#/F#)]** を選択します。

     ![dotnet を選択する](./media/apache-spark-job-definitions/select-dotnet.png)

 6. Apache Spark ジョブ定義の情報を入力します。 サンプル情報をコピーできます。
    
     |  プロパティ   | 説明   |  
     | ----- | ----- |  
     |Job definition name (ジョブ定義名)| Apache Spark ジョブ定義の名前を入力します。 この名前は公開されるまでいつでも更新できます。 <br> サンプル: `dotnet`|
     |Main definition file (メイン定義ファイル)| ジョブに使用されるメイン ファイルです。 ストレージから .NET for Apache Spark アプリケーション (メインの実行可能ファイル、ユーザー定義関数を含む DLL、およびその他の必要なファイル) を含む ZIP ファイルを選択します。 **[ファイルのアップロード]** を選択して、ファイルをストレージ アカウントにアップロードできます。 <br> サンプル: `abfss://…/path/to/wordcount.zip`|
     |Main executable file (メイン実行可能ファイル)| メイン定義 ZIP ファイル内のメインの実行可能ファイル。 <br> サンプル: `WordCount`|
     |コマンド ライン引数| ジョブに対する省略可能な引数。 <br> サンプル: `abfss://…/path/to/shakespeare.txt` `abfss://…/path/to/result` <br> *注意事項: サンプル ジョブ定義の 2 つの引数はスペースで区切ります。* |
     |参照ファイル| メイン定義 ZIP ファイル (依存 jar、追加のユーザー定義関数 DLL、およびその他の構成ファイル) に含まれていない .NET for Apache Spark アプリケーションを実行するために、ワーカー ノードによって必要とされる追加のファイル。 **[ファイルのアップロード]** を選択して、ファイルをストレージ アカウントにアップロードできます。|
     |Spark プール| 選択した Apache Spark プールにジョブが送信されます。|
     |Spark のバージョン| Apache Spark プールが実行されている Apache Spark のバージョン。|
     |エグゼキュータ| ジョブ用の指定された Apache Spark プール内で提供される Executor の数。|  
     |Executor size (エグゼキュータのサイズ)| ジョブ用の指定された Apache Spark プール内で提供される、Executor に使用するコアとメモリの数。|
     |Driver size (ドライバー サイズ)| ジョブ用の指定された Apache Spark プール内で提供される、ドライバーに使用するコアとメモリの数。|

     ![dotnet 用の Spark ジョブ定義の値の設定](./media/apache-spark-job-definitions/create-dotnet-definition.png)

 7. **[公開]** を選択して、Apache Spark ジョブ定義を保存します。

      ![dotnet 定義の公開](./media/apache-spark-job-definitions/publish-dotnet-definition.png)

## <a name="submit-an-apache-spark-job-definition-as-a-batch-job"></a>Apache Spark ジョブ定義をバッチ ジョブとして送信する

Apache Spark ジョブ定義を作成したら、それを Apache Spark プールに送信できます。 使用する ADLS Gen2 ファイルシステムの **ストレージ BLOB データ共同作成者** であることを確認してください。 そうでない場合は、手動でアクセス許可を追加する必要があります。

### <a name="scenario-1-submit-apache-spark-job-definition"></a>シナリオ 1:Apache Spark ジョブ定義を送信する
 1. Apache Spark ジョブ定義を選択してウィンドウを開きます。

      ![送信する Spark ジョブ定義を開く ](./media/apache-spark-job-definitions/open-spark-definition.png)

 2. **[送信]** ボタンを選択し、選択した Apache Spark プールにプロジェクトを送信します。 **[Spark monitoring URL]\(Spark 監視 URL\)** タブを選択して、Apache Spark アプリケーションの LogQuery を表示できます。

    ![[送信] ボタンを選択して、Spark ジョブ定義を送信します](./media/apache-spark-job-definitions/submit-spark-definition.png)

    ![[Spark Submission]\(Spark 送信\) ダイアログ ボックス](./media/apache-spark-job-definitions/submit-definition-result.png)

### <a name="scenario-2-view-apache-spark-job-running-progress"></a>シナリオ 2: Apache Spark ジョブの実行の進行状況を表示する

 1. **[モニター]** を選択して、 **[Apache Spark アプリケーション]** オプションを選択します。 送信した Apache Spark アプリケーションを見つけることができます。

     ![Spark アプリケーションを表示する](./media/apache-spark-job-definitions/view-spark-application.png)

 2. 次に、その Apache Spark アプリケーションを選択すると、 **[SparkJobDefinition]** ジョブ ウィンドウが表示されます。 ここからジョブ実行の進行状況を表示できます。
     
     ![Spark アプリケーションの LogQuery を表示する](./media/apache-spark-job-definitions/view-job-log-query.png)

### <a name="scenario-3-check-output-file"></a>シナリオ 3: 出力ファイルを確認する

 1. **[データ]**  ->  **[Linked]\(リンク済み\)**  ->  **[Azure Data Lake Storage Gen2]** (hozhaobdbj) の順に選択し、先ほど作成した **result** フォルダーを開くと、結果フォルダーに移動して出力が生成されているかどうかを確認できます。

     ![出力ファイルを表示する](./media/apache-spark-job-definitions/view-output-file.png)

## <a name="add-an-apache-spark-job-definition-into-pipeline"></a>Apache Spark ジョブ定義をパイプラインに追加する

このセクションでは、Apache Spark ジョブ定義をパイプラインに追加します。

 1. 既存の Apache Spark ジョブ定義を開きます。

 2. Apache Spark ジョブ定義の右上にあるアイコンを選択し、 **[Existing pipeline]\(既存のパイプライン\)** または **[新しいパイプライン]** を選択します。 詳細については、パイプラインのページを参照してください。

     ![パイプライン 1 に追加する](./media/apache-spark-job-definitions/add-to-pipeline01.png)

     ![パイプライン 2 に追加する](./media/apache-spark-job-definitions/add-to-pipeline02.png)

## <a name="next-steps"></a>次の手順

次に、Azure Synapse Studio を使用して Power BI データセットを作成し、Power BI データを管理できます。 詳細については、[Power BI ワークスペースを Synapse ワークスペースにリンクする](../quickstart-power-bi.md)に関する記事を参照してください。 

