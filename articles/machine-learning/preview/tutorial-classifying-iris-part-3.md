---
title: "Azure Machine Learning サービス (プレビュー) のモデルのデプロイ | Microsoft Docs"
description: "このチュートリアルでは、Azure Machine Learning サービス (プレビュー) の使い方をエンド ツー エンドで詳しく説明します。 パート 3 では、モデルのデプロイについて説明します。"
services: machine-learning
author: raymondl
ms.author: raymondl, aashishb
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: hero-article
ms.date: 09/25/2017
ms.translationtype: HT
ms.sourcegitcommit: 44e9d992de3126bf989e69e39c343de50d592792
ms.openlocfilehash: 0dfcc965d96949527b3e80285061bff320872621
ms.contentlocale: ja-jp
ms.lasthandoff: 09/25/2017

---

# <a name="classifying-iris-part-3-deploy-a-model"></a>あやめの分類 (パート 3): モデルをデプロイする
Azure Machine Learning サービス (プレビュー) は、データ サイエンスと高度な分析をエンド ツー エンドで支援する統合ソリューションです。データの準備、実験の開発、モデルのデプロイをクラウド スケールで行うプロフェッショナルなデータ サイエンティストを対象としています。

このチュートリアルは、3 部構成のシリーズのパート 3 になります。 チュートリアルのこのパートでは、Azure Machine Learning サービス (プレビュー) を使って次の作業を行う方法について説明します。

> [!div class="checklist"]
> * モデル ファイルを探す
> * スコア付けスクリプトとスキーマ ファイルを生成する
> * 環境の準備
> * リアルタイム Web サービスを作成する
> * リアルタイム Web サービスを実行する
> * 出力された BLOB データを確認する 

 このチュートリアルでは、単純化するために、経時的な変化を伴わない[あやめデータセット](https://en.wikipedia.org/wiki/iris_flower_data_set)を使用します。 スクリーンショットは Windows のものですが、macOS を使用した場合も大きな違いはありません。

## <a name="prerequisites"></a>前提条件
このチュートリアル シリーズのパート 1 とパート 2 を完了してください。
- まず、[データの準備に関するチュートリアル](tutorial-classifying-iris-part-1.md)に従って Azure Machine Learning リソースを作成し、Azure Machine Learning Workbench アプリケーションをインストールします。
- 次に、[モデルの構築に関するチュートリアル](tutorial-classifying-iris-part-2.md)に従って、Azure Machine Learning でロジスティック回帰モデルを作成します。

## <a name="download-the-model-pickle-file"></a>モデルの pickle ファイルをダウンロードする
このチュートリアルの前のパートでは、ローカルの Azure Machine Learning Workbench で `iris_sklearn.py` スクリプトを実行しました。 その際、Python オブジェクトのシリアル化パッケージとして広く使われている **[pickle](https://docs.python.org/2/library/pickle.html)** を使ってロジスティック回帰モデルをシリアル化しました。 

1. **Azure Machine Learning Workbench** アプリケーションを起動し、チュートリアル シリーズの前のパートで作成した **myIris** プロジェクトを開きます。

2. プロジェクトを開いたら、Azure Machine Learning Workbench の左側のツール バーにある **[ファイル]** ボタン (フォルダー アイコン) をクリックして、プロジェクト フォルダーのファイルを一覧表示します。

3. **iris_sklearn.py** ファイルを選択すると、Workbench の新しいテキスト エディター タブに Python コードが表示されます。

4. pickle ファイルが生成されている場所を **iris_sklearn.py** ファイルで確認します。 Ctrl + F キーを押して検索ダイアログを開き、Python コード内で **pickle** という単語を探してください。

   このコード スニペットを見ると、pickle の出力ファイルがどのように生成されているのかがわかります。 pickle の出力ファイルが、`model.pkl` という名前でディスクに保存されていることに注目してください。 

   ```python
   print("Export the model to model.pkl")
   f = open('./outputs/model.pkl', 'wb')
   pickle.dump(clf1, f)
   f.close()
   ```

5. 前回実行時の出力ファイルからモデルの pickle ファイルを探します。
   
   モデル ファイルは、**iris_sklearn.py** スクリプトを実行したときに、`model.pkl` という名前で `outputs` フォルダーに書き込まれています。 このフォルダーは、ローカルのプロジェクト フォルダーにではなく、スクリプトの実行場所として選んだ実行環境に存在します。 
   
   - このファイルを探すには、Azure Machine Learning Workbench アプリケーションの左側のツール バーにある **[実行]** ボタン (時計アイコン) をクリックして **[すべての実行]** の一覧を表示します。  
   - **[すべての実行]** タブが表示されます。 実行のテーブルで、最近の実行をどれか 1 つ選択します。対象の環境が **[ローカル]** で、スクリプト名が **[iris_sklearn.py]** であるものを選んでください。 
   - **[Run Properties]\(実行プロパティ\)** ページが表示されます。 ページの右上にある **[出力]** セクションに注目してください。 
   - **model.pkl** ファイルの横のチェック ボックスをオンにし、**[ダウンロード]** ボタンをクリックして pickle ファイルをダウンロードします。 これをプロジェクト フォルダーのルートに保存してください。 この後の手順で必要になります。

   ![pickle ファイルをダウンロードする](media/tutorial-classifying-iris/download_model.png)

   `outputs` フォルダーについて詳しくは、[大きなデータ ファイルの読み取りと書き込みの方法](how-to-read-write-files.md)に関する記事を参照してください。

## <a name="get-scoring-and-schema-files"></a>スコア付けファイルとスキーマ ファイルを取得する
Web サービスをモデル ファイルと一緒にデプロイするには、スコア付けスクリプトのほか、Web サービスの入力データに使用するスキーマ (任意) も必要になります。 スコア付けスクリプトを実行すると、現在のフォルダーから `model.pkl` ファイルが読み込まれ、それを使用して、新しく予測されたあやめの分類が生成されます。  

1. **Azure Machine Learning Workbench** アプリケーションを起動し、チュートリアル シリーズの前のパートで作成した **myIris** プロジェクトを開きます。

2. プロジェクトを開いたら、Azure Machine Learning Workbench の左側のツール バーにある **[ファイル]** ボタン (フォルダー アイコン) をクリックして、プロジェクト フォルダーのファイルを一覧表示します。

3. **iris_score.py** ファイルを選択します。 Python スクリプトが表示されます。 このファイルは、スコア付けファイルとして使用されます。

4. スキーマ ファイルを取得するには、このスクリプトを実行します。 コマンド バーで **[ローカル]** 環境と **[iris_score.py]** スクリプトを選択し、**[実行]** ボタンをクリックします。 

5. このスクリプトを実行すると、**outputs** フォルダーに JSON ファイルが作成されます。このファイルによって、モデルに必要な入力データ スキーマがキャプチャされます。

   ![スコア付けファイル](media/tutorial-classifying-iris/model_data_collection.png)

6. Machine Learning Workbench ウィンドウの右側の [ジョブ] ウィンドウで、最新の **iris_score.py** ジョブが緑色の **[完了]** 状態になるまで待ちます。 次に、最新のジョブ実行の **iris_score.py [1]** というハイパーリンクをクリックすると、**iris_score.py** 実行からの実行の詳細が表示されます。 

7. [Run Properties]\(実行プロパティ\) ページの **[出力]** セクションで、新しく作成された **service_schema.json** ファイルを選択します。 このファイルをプロジェクトのルート フォルダーに保存してください。

8. 先ほど **iris_score.py** スクリプトを開いたタブに戻ります。 

   Web サービスからモデルの入力と予測をキャプチャするデータ収集の用法に注目してください。 データ収集で特に重要なのは以下の点です。

9. モデル データの収集機能が含まれた ModelDataCollector クラスがファイルの冒頭のコードによってインポートされることを確認します。

   ```python
   from azureml.datacollector import ModelDataCollector
   ```

10. ModelDataCollector が `init()` 関数の次のコード行によってインスタンス化されることを確認します。

   ```python
   global inputs_dc, prediction_dc
   inputs_dc = ModelDataCollector('model.pkl',identifier="inputs")
   prediction_dc = ModelDataCollector('model.pkl', identifier="prediction")`
   ```

11. 入力データと予測データが `run(input_df)` 関数の次のコード行によって収集されることを確認します。

   ```python
   global clf2, inputs_dc, prediction_dc
   inputs_dc.collect(input_df)
   prediction_dc.collect(pred)
   ```

これで、モデルを運用するための環境を準備する用意が整いました。

## <a name="prepare-to-operationalize-locally"></a>ローカルで運用できるように準備する
ローカル コンピューター上の Docker コンテナーで実行するには、"_ローカル モード_" デプロイを使います。

"_ローカル モード_" は、開発とテストに使うことができます。 以降の手順でモデルを運用可能な状態にするためには、Docker エンジンがローカルで実行されている必要があります。 コマンドの最後に `-h` フラグを付けると、コマンドのヘルプを参照できます。

>[!NOTE]
>Docker エンジンがローカルで実行されていない場合も、デプロイ用のクラスターを Azure に作成することで手順を続行できます。 それ以上料金が発生しないよう、クラスターはチュートリアル後に必ず削除してください。

1. Azure Machine Learning Workbench のコマンド ライン インターフェイスを開きます。[ファイル] メニューの **[コマンド プロンプトを開く]** をクリックしてください。

   コマンド ライン プロンプトは、現在のプロジェクト フォルダーの場所 (`c:\temp\myIris>`) で開きます。

2. Azure リソース プロバイダー `Microsoft.ContainerRegistry` がサブスクリプションに登録されていることを確認します。 手順 3. で環境を作成するためには、あらかじめこのリソース プロバイダーを登録しておく必要があります。 登録済みであるかどうかは、次のコマンドを使って確認できます。
   ``` 
   az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table 
   ``` 

   次のような出力が表示されます。 
   ```
   Provider                                  Status 
   --------                                  ------
   Microsoft.Authorization                   Registered 
   Microsoft.ContainerRegistry               Registered 
   microsoft.insights                        Registered 
   Microsoft.MachineLearningExperimentation  Registered 
   ... 
   ```
   
   `Microsoft.ContainerRegistry` が登録されていない場合、次のコマンドを使って登録できます。
   ``` 
   az provider register --namespace Microsoft.ContainerRegistry 
   ```
   登録には数分かかる場合があります。登録の状態は、上記の `az provider list` コマンドのほか、次のコマンドを使って確認できます。
   ``` 
   az provider show -n Microsoft.ContainerRegistry 
   ``` 

3. 環境を作成します。 この手順は、環境 (開発環境、運用環境など) ごとに 1 回実行する必要があります。この最初の環境には "_ローカル モード_" を使います  (後から "_クラスター モード_" で環境を設定してみる場合は、次のコマンドで `-c` スイッチまたは `--cluster` スイッチを指定してください)。

   ```azurecli
   az ml env setup -n <new deployment environment name> --location <e.g. eastus2>
   ```
   
   画面の指示に従い、Docker イメージを格納するためのストレージ アカウント、Docker イメージをリストするための ACR (Azure Container Registry)、テレメトリを収集するための AppInsight アカウントをプロビジョニングします。 `-c` スイッチを使用した場合、ACS (Azure Container Service) クラスターも作成されます。
   
   クラスター名は、環境を識別する手段となります。また、location には、Azure Portal で作成したモデル管理アカウントと同じ場所を指定する必要があります。

4. モデル管理アカウントを作成します (これは 1 回限りの作業です)。  
   ```azurecli
   az ml account modelmanagement create --location <e.g. eastus2> -n <new model management account name> -g <existing resource group name> --sku-name S1
   ```
   
5. モデル管理アカウントを設定します。  
   ```azurecli
   az ml account modelmanagement set -n <youracctname> -g <yourresourcegroupname>
   ```

6. 環境を設定します。
設定が完了したら、運用化に必要な環境変数を次のコマンドで設定します。 この環境名は、前出の手順 1. で使った名前です。 リソース グループ名は、同じプロセスの出力であり、設定プロセスが完了した時点でコマンド ウィンドウに表示されます。
   ```azurecli
   az ml env set -n <deployment environment name> -g <existing resource group name>
   ```

   ローカル Web サービスのデプロイに関して、運用環境が適切に構成されていることを確認するために、次のコマンドを入力します。

   ```azurecli
   az ml env show
   ```

これで、リアルタイム Web サービスを作成する準備が整いました。

## <a name="create-a-real-time-web-service"></a>リアルタイム Web サービスを作成する
リアルタイム Web サービスを作成するには、次のコマンドを使用します。

   ```azurecli
   az ml service create realtime -f iris_score.py --model-file model.pkl -s service_schema.json -n irisapp -r python --collect-model-data true 
   ```
   これにより、後で使用できる Web サービス ID が生成されます。

   `az ml service create realtime` コマンドには、次のスイッチがあります。
   * -n: アプリの名前。すべて小文字にする必要があります。
   * -f: スコア付けスクリプト ファイルの名前。
   * --model-file: モデル ファイル。このケースでは、pickle から出力された model.pkl ファイルになります。
   * -r: モデルの種類。このケースでは、Python モデルになります。
   * --collect-model-data true: データ収集を有効にします。

   >[!IMPORTANT]
   >サービス名 (新しい Docker イメージ名) はすべて小文字であることが必要です。そうしないと、エラーが発生します。 

   このコマンドを実行すると、先ほど環境を設定するときに作成したストレージ アカウントに、モデル ファイルとスコア付けファイルがアップロードされます。 このデプロイ プロセスによって、指定したモデル、スキーマ、スコア付けファイルを含んだ Docker イメージが作成され、ACR レジストリ (`<ACR_name>.azureacr.io/<imagename>:<version>`) にプッシュされます。 さらに、そのイメージがローカルのコンピューターにプルダウンされ、そのイメージに基づいて Docker コンテナーが起動されます  (ご利用の環境がクラスター モードで構成されている場合、ACS Kubernetes クラスターに Docker コンテナーがデプロイされます)。

   デプロイの一環として、Web サービスの HTTP REST エンドポイントがローカル マシンに作成されます。 数分後、正常終了のメッセージと共にコマンドが完了し、Web サービスの準備が整います。

   実行中の Docker コンテナーは、`docker ps` コマンドで確認できます。
   ```azurecli
   docker ps
   ```
### <a name="alternative-route"></a>代替手段
前出の `az ml service create realtime` コマンドの代わりに、モデルの登録、マニフェストの生成、Docker イメージの作成、Web サービスの作成の手順を個別に実行することもできます そうすることで個々の手順の柔軟性が高まり、前の手順によって生成されたエンティティを再利用したり、必要なときにエンティティを再構築したりすることができます。 その具体的な方法については、次の手順に従ってください。

1. pickle ファイル名を指定してモデルを登録します。

   ```azurecli
   az ml model register --model model.pkl --name model.pkl
   ```
   これにより、モデル ID が生成されます。

2. マニフェストを作成します。

   マニフェストを作成するには、前の手順で生成されたモデル ID を指定して次のコマンドを実行します。

   ```azurecli
   az ml manifest create --manifest-name <new manifest name> -f iris_score.py -r python -i <model ID> -s service_schema.json
   ```
   これにより、マニフェスト ID が生成されます。

3. Docker イメージを作成します。

   Docker イメージを作成するには、前の手順で生成されたマニフェスト ID の値を指定して次のコマンドを実行します。

   ```azurecli
   az ml image create -n irisimage --manifest-id <manifest ID>
   ```
   これにより、Docker イメージ ID が生成されます。
   
4. サービスの作成

   サービスを作成するには、前の手順で生成されたイメージ ID を指定して次のコマンドを実行します。

   ```azurecli
   az ml service create realtime --image-id <image ID> -n irisapp --collect-model-data true
   ```
   これにより、Web サービス ID が生成されます。

これで、Web サービスを実行する準備が整いました。

## <a name="run-the-real-time-web-service"></a>リアルタイム Web サービスを実行する

実行中の `irisapp` Web サービスをテストするために、4 つのランダムな数値の配列を含んだ JSON 形式のレコードを Web サービスに渡します。

1. Web サービスの作成にはサンプル データが含まれていました。 ローカル モードで実行しているときに `az ml service show realtime` コマンドを呼び出すことで、サービスのテストに使用できるサンプル実行コマンドを取得できます。 また、独自のカスタム アプリにサービスを組み込む際に利用できるスコア付けの URL も得られます。

   ```azurecli
   az ml service show realtime -i <web service ID>
   ```

2. サービスをテストするには、返された service run コマンドを実行します。

   ```azurecli
   az ml service run realtime -i irisapp -d "{\"input_df\": [{\"petal width\": 0.25, \"sepal length\": 3.0, \"sepal width\": 3.6, \"petal length\": 1.3}]}
   ```
   `"2"` という値が出力されます。これは、予測された品種を表しています  (実際の結果は異なる場合があります)。 

3. CLI 以外からサービスを実行する場合は、認証用のキーを取得する必要があります。

   ```azurecli
   az ml service keys realtime -i <web service ID>
   ```

## <a name="view-the-collected-data-in-azure-blob-storage"></a>Azure Blob Storage に収集されたデータを確認する

1. [Azure Portal](https://portal.azure.com) にサインインします。

2. 自分のストレージ アカウントを探します。 これを行うには、**[その他のサービス]** をクリックします。

3. 検索ボックスに「**ストレージ アカウント**」と入力し、**Enter** キーを押します。

4. **[ストレージ アカウント]** 検索ページで、自分の環境に該当する **[ストレージ アカウント]** リソースを選択します。 

   > [!TIP]
   > 使用されているストレージ アカウントを調べるには、Azure Machine Learning Workbench を開いて作業中のプロジェクトを選択し、**[ファイル]** メニューでコマンド ライン プロンプトを開きます。コマンド ライン プロンプトに「`az ml env show -v`」と入力して、*storage_account* の値を確認してください。 それがストレージ アカウントの名前です。

5. **[ストレージ アカウント]** ページが開いたら、左側の一覧にある **[コンテナー]** 項目をクリックします。 **modeldata** という名前のコンテナーを探します。 
 
   データがまったく表示されない場合は少しお待ちください。最初の Web サービス要求の後、ストレージ アカウントにデータが反映され始めるまでに、最大 10 分程度かかる場合があります。

   データは、次のコンテナー パスで BLOB に取り込まれます。

   `/modeldata/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<day>/data.csv`

6. このデータは、Microsoft のソフトウェアやオープン ソースのツールを含め、さまざまな方法で Azure BLOB から取り出すことができます。 

   出力された BLOB を取り出す方法の例:
   - Azure ML Workbench: Azure ML Workbench で csv ファイルをデータ ソースとして追加してそのファイルを開きます。 
   - Excel: 日々の csv ファイルをスプレッドシートとして開きます。
   - [Power BI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/): BLOB の csv データから取り込んだデータを使ってグラフを作成します。
   - [Spark](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-overview): csv データの大部分を含んだデータフレームを作成します。

      ```python
      var df = spark.read.format("com.databricks.spark.csv").option("inferSchema","true").option("header","true").load("wasb://modeldata@<storageaccount>.blob.core.windows.net/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<date>/*")
      ```
   - [Hive](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-tutorial-get-started): csv データを Hive テーブルに読み込み、BLOB に対して直接 SQL クエリを実行します。

## <a name="next-steps"></a>次のステップ
3 部構成のチュートリアル シリーズのパート 3 では、Azure Machine Learning サービスを使って次の作業を行う方法について説明しました。
> [!div class="checklist"]
> * モデル ファイルを探す
> * スコア付けスクリプトとスキーマ ファイルを生成する
> * 環境の準備
> * リアルタイム Web サービスを作成する
> * リアルタイム Web サービスを実行する
> * 出力された BLOB データを確認する 

さまざまなコンピューティング環境でトレーニング スクリプトの実行、モデルの作成、モデルのシリアル化を行い、Docker ベースの Web サービスでモデルが運用できる状態となりました。 

高度なデータ準備を行うことができます。
> [!div class="nextstepaction"]
> [高度なデータ準備](tutorial-bikeshare-dataprep.md)


