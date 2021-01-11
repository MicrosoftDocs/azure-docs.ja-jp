---
title: チュートリアル:Synapse Studio で Apache Spark ジョブ定義を作成する
description: チュートリアル - Azure Synapse Analytics を使用して Spark ジョブ定義を作成し、Apache Spark for Azure Synapse Analytics のプールに送信します。
author: Jejiang
ms.author: jejiang
ms.reviewer: jasonh
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: ''
ms.date: 04/15/2020
ms.openlocfilehash: 38678c795b0ce7534de0ca8602c1198bc35f0e05
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2020
ms.locfileid: "88206190"
---
# <a name="tutorial-create-apache-spark-job-definition-in-synapse-studio"></a>チュートリアル:Synapse Studio で Apache Spark ジョブ定義を作成する

このチュートリアルでは、Azure Synapse Studio を使用して Apache Spark ジョブ定義を作成し、その後 Apache Spark プールに送信する方法を示します。

このチュートリアルに含まれるタスクは次のとおりです。

* PySpark (Python) 用の Apache Spark ジョブ定義を作成する
* Spark (Scala) 用の Apache Spark ジョブ定義を作成する
* .NET Spark (C# または F#) 用の Apache Spark ジョブ定義を作成する
* Apache Spark ジョブ定義をバッチ ジョブとして送信する
* Apache Spark ジョブ定義をパイプラインに追加する

## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に、次の要件を満たしてください。

* Azure Synapse Analytics ワークスペース。 手順については、[Azure Synapse Analytics ワークスペースの作成](../../machine-learning/how-to-manage-workspace.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#create-a-workspace)に関するページを参照してください。
* Apache Spark プール
* ADLS Gen2 ストレージ アカウント。 使用する ADLS Gen2 ファイル システムのストレージ BLOB データ所有者である必要があります。 そうでない場合は、手動でアクセス許可を追加する必要があります。

## <a name="create-an-apache-spark-job-definition-for-pyspark-python"></a>PySpark (Python) 用の Apache Spark ジョブ定義を作成する

このセクションでは、PySpark (Python) 用の Apache Spark ジョブ定義を作成します。

1. [Azure Synapse Studio](https://web.azuresynapse.net/) を開きます。

2. [Apache Spark ジョブ定義を作成するためのサンプル ファイル](https://github.com/Azure-Samples/Synapse/tree/master/Spark/Python)に移動して、**wordcount.py** と **shakespear.txt** をダウンロードできます。 次に、これらのファイルを Azure Storage にアップロードします。 **[データ]** をクリックし、 **[ストレージ アカウント]** を選択して、関連ファイルを ADLS Gen2 ファイル システムにアップロードします。 ファイルが Azure ストレージに既に存在する場合は、この手順をスキップします。 

     ![python ファイルのアップロード](./media/apache-spark-job-definitions/upload-python-file.png)

3. **[開発]** ハブをクリックして、左側のペインで **[Spark job definitions]\(Spark ジョブ定義\)** を選択し、 **[Spark job definitions]\(Spark ジョブ定義\)** の横にある [...] アクション ノードをクリックしてから、コンテキスト メニューの **[New Spark job definition]\(新しい Spark ジョブ定義\)** を選択します。

     ![Python 用の新しい定義の作成](./media/apache-spark-job-definitions/create-new-definition.png)

4. Apache Spark ジョブ定義のメイン ウィンドウの [言語] ドロップ ダウン リストから **[PySpark (Python)]** を選択します。

5. Apache Spark ジョブ定義の情報を入力します。 サンプル情報をコピーできます。

     |  プロパティ   | 説明   |  
     | ----- | ----- |  
     |Job definition name (ジョブ定義名)| Apache Spark ジョブ定義の名前を入力します。 この名前は公開されるまでいつでも更新できます。 サンプル: `job definition sample`|
     |Main definition file (メイン定義ファイル)| ジョブに使用されるメイン ファイルです。 ストレージから PY ファイルを選択します。 **[ファイルのアップロード]** を選択して、ファイルをストレージ アカウントにアップロードできます。 サンプル: `abfss://…/path/to/wordcount.py`|
     |コマンド ライン引数| ジョブに対する省略可能な引数。 サンプル: `abfss://…/path/to/shakespeare.txt abfss://…/path/to/result`|
     |参照ファイル| メイン定義ファイル内で参照に使用される追加ファイル。 **[ファイルのアップロード]** を選択して、ファイルをストレージ アカウントにアップロードできます。 |
     |Spark プール| 選択した Apache Spark プールにジョブが送信されます。|
     |Spark のバージョン| Apache Spark プールが実行されている Apache Spark のバージョン。|
     |エグゼキュータ| ジョブ用の指定された Apache Spark プール内で提供される Executor の数。|
     |Executor size (エグゼキュータのサイズ)| ジョブ用の指定された Apache Spark プール内で提供される、Executor に使用するコアとメモリの数。|  
     |Driver size (ドライバー サイズ)| ジョブ用の指定された Apache Spark プール内で提供される、ドライバーに使用するコアとメモリの数。|

     ![Python 用の Spark ジョブ定義の値の設定](./media/apache-spark-job-definitions/create-py-definition.png)

6. **[公開]** を選択して、Apache Spark ジョブ定義を保存します。

     ![py 定義の公開](./media/apache-spark-job-definitions/publish-py-definition.png)

## <a name="create-an-apache-spark-job-definition-for-apache-sparkscala"></a>Apache Spark (Scala) 用の Apache Spark ジョブ定義を作成する

このセクションでは、Apache Spark (Scala) 用の Apache Spark ジョブ定義を作成します。

 1. [Azure Synapse Studio](https://web.azuresynapse.net/) を開きます。

 2. [Apache Spark ジョブ定義を作成するためのサンプル ファイル](https://github.com/Azure-Samples/Synapse/tree/master/Spark/Scala)にアクセスして、**wordcount.jar** と **shakespear.txt** をダウンロードできます。 次に、これらのファイルを Azure Storage にアップロードします。 **[データ]** をクリックし、 **[ストレージ アカウント]** を選択して、関連ファイルを ADLS Gen2 ファイル システムにアップロードします。 ファイルが Azure ストレージに既に存在する場合は、この手順をスキップします。 
 
     ![Scala 構造の準備](./media/apache-spark-job-definitions/prepare-scala-structure.png)

 3. **[開発]** ハブをクリックして、左側のペインで **[Spark job definitions]\(Spark ジョブ定義\)** を選択し、 **[Spark job definitions]\(Spark ジョブ定義\)** の横にある [...] アクション ノードをクリックしてから、コンテキスト メニューの **[New Spark job definition]\(新しい Spark ジョブ定義\)** を選択します。
     ![Scala 用の新しい定義の作成](./media/apache-spark-job-definitions/create-new-definition.png)

 4. Apache Spark ジョブ定義のメイン ウィンドウの [言語] ドロップ ダウン リストから **[Spark (Scala)]** を選択します。

 5. Apache Spark ジョブ定義の情報を入力します。 サンプル情報をコピーできます。

     |  プロパティ   | 説明   |  
     | ----- | ----- |  
     |Job definition name (ジョブ定義名)| Apache Spark ジョブ定義の名前を入力します。 この名前は公開されるまでいつでも更新できます。 サンプル: `job definition sample`|
     |Main definition file (メイン定義ファイル)| ジョブに使用されるメイン ファイルです。 ストレージから JAR ファイルを選択します。 **[ファイルのアップロード]** を選択して、ファイルをストレージ アカウントにアップロードできます。 サンプル: `abfss://…/path/to/wordcount.jar`|
     |メイン クラス名| 完全修飾識別子またはメイン定義ファイル内のメイン クラス。 サンプル: `WordCount`|
     |コマンド ライン引数| ジョブに対する省略可能な引数。 サンプル: `abfss://…/path/to/shakespeare.txt abfss://…/path/to/result`|
     |参照ファイル| メイン定義ファイル内で参照に使用される追加ファイル。 **[ファイルのアップロード]** を選択して、ファイルをストレージ アカウントにアップロードできます。|
     |Spark プール| 選択した Apache Spark プールにジョブが送信されます。|
     |Spark のバージョン| Apache Spark プールが実行されている Apache Spark のバージョン。|
     |エグゼキュータ| ジョブ用の指定された Apache Spark プール内で提供される Executor の数。|  
     |Executor size (エグゼキュータのサイズ)| ジョブ用の指定された Apache Spark プール内で提供される、Executor に使用するコアとメモリの数。|
     |Driver size (ドライバー サイズ)| ジョブ用の指定された Apache Spark プール内で提供される、ドライバーに使用するコアとメモリの数。|

     ![Scala 用の Spark ジョブ定義の値の設定](./media/apache-spark-job-definitions/create-scala-definition.png)

 6. **[公開]** を選択して、Apache Spark ジョブ定義を保存します。

     ![Scala 定義の公開](./media/apache-spark-job-definitions/publish-scala-definition.png)


## <a name="create-an-apache-spark-job-definition-for-net-sparkcf"></a>.NET Spark (C# または F#) 用の Apache Spark ジョブ定義を作成する

このセクションでは、.NET Spark (C# または F#) 用の Apache Spark ジョブ定義を作成します。
 1. [Azure Synapse Studio](https://web.azuresynapse.net/) を開きます。

 2. [Apache Spark ジョブ定義を作成するためのサンプル ファイル](https://github.com/Azure-Samples/Synapse/tree/master/Spark/DotNET)にアクセスして、**wordcount.zip** と **shakespear.txt** をダウンロードできます。 次に、これらのファイルを Azure Storage にアップロードします。 **[データ]** をクリックし、 **[ストレージ アカウント]** を選択して、関連ファイルを ADLS Gen2 ファイル システムにアップロードします。 ファイルが Azure ストレージに既に存在する場合は、この手順をスキップします。 

     ![dotnet 構造の準備](./media/apache-spark-job-definitions/prepare-scala-structure.png)

 3. **[開発]** ハブをクリックして、左側のペインで **[Spark job definitions]\(Spark ジョブ定義\)** を選択し、 **[Spark job definitions]\(Spark ジョブ定義\)** の横にある [...] アクション ノードをクリックしてから、コンテキスト メニューの **[New Spark job definition]\(新しい Spark ジョブ定義\)** を選択します。

     ![dotnet 用の新しい定義の作成](./media/apache-spark-job-definitions/create-new-definition.png)

 4. Apache Spark ジョブ定義のメイン ウィンドウの [言語] ドロップ ダウン リストから **[.NET Spark (C#/F#)]** を選択します。

 5. Apache Spark ジョブ定義の情報を入力します。 サンプル情報をコピーできます。
    
     |  プロパティ   | 説明   |  
     | ----- | ----- |  
     |Job definition name (ジョブ定義名)| Apache Spark ジョブ定義の名前を入力します。 この名前は公開されるまでいつでも更新できます。 サンプル: `job definition sample`|
     |Main definition file (メイン定義ファイル)| ジョブに使用されるメイン ファイルです。 ストレージから .NET for Apache Spark アプリケーション (メインの実行可能ファイル、ユーザー定義関数を含む DLL、およびその他の必要なファイル) を含む ZIP ファイルを選択します。 **[ファイルのアップロード]** を選択して、ファイルをストレージ アカウントにアップロードできます。 サンプル: `abfss://…/path/to/wordcount.zip`|
     |Main executable file (メイン実行可能ファイル)| メイン定義 ZIP ファイル内のメインの実行可能ファイル。 サンプル: `WordCount`|
     |コマンド ライン引数| ジョブに対する省略可能な引数。 サンプル: `abfss://…/path/to/shakespeare.txt abfss://…/path/to/result`|
     |参照ファイル| メイン定義 ZIP ファイル (依存 jar、追加のユーザー定義関数 DLL、およびその他の構成ファイル) に含まれていない .NET for Apache Spark アプリケーションを実行するために、ワーカー ノードによって必要とされる追加のファイル。 **[ファイルのアップロード]** を選択して、ファイルをストレージ アカウントにアップロードできます。|
     |Spark プール| 選択した Apache Spark プールにジョブが送信されます。|
     |Spark のバージョン| Apache Spark プールが実行されている Apache Spark のバージョン。|
     |エグゼキュータ| ジョブ用の指定された Apache Spark プール内で提供される Executor の数。|  
     |Executor size (エグゼキュータのサイズ)| ジョブ用の指定された Apache Spark プール内で提供される、Executor に使用するコアとメモリの数。|
     |Driver size (ドライバー サイズ)| ジョブ用の指定された Apache Spark プール内で提供される、ドライバーに使用するコアとメモリの数。|

     ![dotnet 用の Spark ジョブ定義の値の設定](./media/apache-spark-job-definitions/create-net-definition.png)

 6. **[公開]** を選択して、Apache Spark ジョブ定義を保存します。

      ![dotnet 定義の公開](./media/apache-spark-job-definitions/publish-net-definition.png)

## <a name="submit-an-apache-spark-job-definition-as-a-batch-job"></a>Apache Spark ジョブ定義をバッチ ジョブとして送信する

Apache Spark ジョブ定義を作成したら、それを Apache Spark プールに送信できます。 使用する ADLS Gen2 ファイル システムのストレージ BLOB データ所有者であることを確認してください。 そうでない場合は、手動でアクセス許可を追加する必要があります。

### <a name="scenario-1-submit-apache-spark-job-definition"></a>シナリオ 1:Apache Spark ジョブ定義を送信する
 1. Apache Spark ジョブ定義をクリックしてウィンドウを開きます。

      ![送信する Spark ジョブ定義を開く ](./media/apache-spark-job-definitions/open-spark-definition.png)

 2. **[送信]** アイコンをクリックし、選択した Apache Spark プールにプロジェクトを送信します。 **[Spark monitoring URL]\(Spark 監視 URL\)** タブをクリックして、Apache Spark アプリケーションの LogQuery を表示できます。

    ![[送信] ボタンをクリックして、Spark ジョブ定義を送信します](./media/apache-spark-job-definitions/submit-spark-definition.png)

    ![[Spark Submission]\(Spark 送信\) ダイアログ ボックス](./media/apache-spark-job-definitions/submit-definition-result.png)

### <a name="scenario-2-view-apache-spark-job-running-progress"></a>シナリオ 2: Apache Spark ジョブの実行の進行状況を表示する

 1. **[モニター]** をクリックし、 **[Spark アプリケーション]** オプションを選択します。 送信した Apache Spark アプリケーションを見つけることができます。

     ![Spark アプリケーションを表示する](./media/apache-spark-job-definitions/view-spark-application.png)

 2. 次に、その Apache Spark アプリケーションをクリックすると、 **[LogQuery]** ウィンドウが表示されます。 **[LogQuery]** ではジョブ実行の進行状況を表示できます。
     
     ![Spark アプリケーションの LogQuery を表示する](./media/apache-spark-job-definitions/view-job-log-query.png)

### <a name="scenario-3-check-output-file"></a>シナリオ 3: 出力ファイルを確認する

 1. **[データ]** をクリックし、 **[ストレージ アカウント]** を選択します。 正常に実行された後、ADLS Gen2 ストレージにアクセスして、出力が生成されたことを確認できます。

     ![出力ファイルを表示する](./media/apache-spark-job-definitions/view-output-file.png)

## <a name="add-an-apache-spark-job-definition-into-pipeline"></a>Apache Spark ジョブ定義をパイプラインに追加する

このセクションでは、Apache Spark ジョブ定義をパイプラインに追加します。

 1. 既存の Apache Spark ジョブ定義を開きます。

 2. Apache Spark ジョブ定義の右上にあるアイコンをクリックし、 **[Existing pipeline]\(既存のパイプライン\)** または **[新しいパイプライン]** を選択します。 詳細については、パイプラインのページを参照してください。

     ![パイプラインへの追加](./media/apache-spark-job-definitions/add-to-pipeline01.png)

     ![パイプラインへの追加](./media/apache-spark-job-definitions/add-to-pipeline02.png)

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure Synapse Studio を使用して Apache Spark ジョブ定義を作成し、その後 Apache Spark プールに送信する方法を示します。 次に、Azure Synapse Studio を使用して Power BI データセットを作成し、Power BI データを管理できます。

