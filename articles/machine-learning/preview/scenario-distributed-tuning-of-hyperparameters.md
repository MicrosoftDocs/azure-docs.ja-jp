---
title: "Azure Machine Learning Workbench を使用したハイパーパラメーターの分散チューニング | Microsoft Docs"
description: "このシナリオでは、Azure Machine Learning Workbench を使用してハイパーパラメーターの分散チューニングを実行する方法について説明します。"
services: machine-learning
author: pechyony
ms.service: machine-learning
ms.topic: article
ms.author: dmpechyo
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.date: 09/20/2017
ms.openlocfilehash: 643cea5cc134a2eb25a0dec4abefd9edca726332
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="distributed-tuning-of-hyperparameters-using-azure-machine-learning-workbench"></a>Azure Machine Learning Workbench を使用したハイパーパラメーターの分散チューニング

このシナリオでは、Azure Machine Learning Workbench を使用して、scikit-learn API を実装する機械学習アルゴリズムのハイパーパラメーターのチューニングをスケール アウトする方法について説明します。 また、リモート Docker コンテナーと Spark クラスターをハイパーパラメーターのチューニングの実行バックエンドとして構成し、使用する方法について説明します。

## <a name="link-of-the-gallery-github-repository"></a>ギャラリーの GitHub リポジトリのリンク
以下は、パブリック GitHub リポジトリのリンクです。 

[https://github.com/Azure/MachineLearningSamples-DistributedHyperParameterTuning](https://github.com/Azure/MachineLearningSamples-DistributedHyperParameterTuning)

## <a name="use-case-overview"></a>ユース ケースの概要

多くの機械学習アルゴリズムには、ハイパーパラメーターという 1 つまたは複数のノブがあります。 これらのノブを使用すると、アルゴリズムをチューニングして、ユーザーが指定したメトリック (精度、AUC、RMSE など) に従って測定される今後のデータについてパフォーマンスを最適化することができます。 データ サイエンティストは、トレーニング データのモデルを構築する場合、将来のテスト データを表示する前に、ハイパーパラメーターの値を用意する必要があります。 既知のトレーニング データに基づいて、モデルが未知のテスト データでも効率的なパフォーマンスになるように、ハイパーパラメーターの値を設定するにはどうすればよいでしょうか。 

ハイパーパラメーターのチューニングで一般的な手法は、*グリッド検索*と*クロス検証*を組み合わせる方法です。 クロス検証は、トレーニング セットに対してトレーニングされたモデルを使用して、テスト セットに対して予測する方法を評価する手法です。 この手法を使用して、まずデータセットを K フォールドに分割してから、ラウンドロビン形式で、1 つのフォールドを除くすべてのフォールド (ヘルドアウト フォールドと呼ばれます) に対してアルゴリズムを K 回トレーニングします。 ここでは、K ヘルドアウト フォールドに対して、K モデルのメトリックの平均値を計算します。 この平均値は、*クロス検証パフォーマンス推定値*と呼ばれ、K モデルを作成するときに使用されるハイパーパラメーターの値に依存します。 ハイパーパラメーターのチューニング時に、ハイパーパラメーター値候補の領域を検索し、クロス検証パフォーマンスの推定値を最適化する値を見つけます。 グリッド検索は、一般的な検索手法です。複数のハイパーパラメーターの候補値の領域は、個々のハイパーパラメーターの候補値セットのクロス積です。 

クロス検証を使用するグリッド検索は時間がかかることがあります。 アルゴリズムに 5 個のハイパーパラメーターがあり、それぞれに 5 個の候補値があり、K=5 フォールドを使用する場合、グリッド検索を完了するには、5<sup>6</sup>=15625 モデルをトレーニングする必要があります。 幸いなことに、クロス検証を使用するグリッド検索は並列プロシージャであり、これらすべてのモデルを並列してトレーニングできます。

## <a name="prerequisites"></a>前提条件

* [Azure アカウント](https://azure.microsoft.com/free/) (無料試用版もご利用いただけます)。
* Workbench をインストールしてアカウントを作成するために、[インストールと作成のクイックスタート](./quickstart-installation.md)に関するページに従ってインストールした [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md) のコピー。
* このシナリオでは、Docker エンジンをローカルにインストールした Windows 10 または MacOS で Azure ML Workbench を実行していることを前提とします。 
* リモート Docker コンテナーを使用するシナリオを実行するには、[こちらの手順](https://docs.microsoft.com/en-us/azure/machine-learning/machine-learning-data-science-provision-vm)に従って Ubuntu データ サイエンス仮想マシン (DSVM) をプロビジョニングします。 少なくとも 8 個のコアと 28 GB のメモリを搭載した仮想マシンを使用することをお勧めします。 D4 インスタンスの仮想マシンにはこのような容量があります。 
* Spark クラスターを使用してこのシナリオを実行するには、[こちらの手順](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-provision-linux-clusters)に従って HDInsight クラスターをプロビジョニングします。 6 個以上の worker ノードと、ヘッド ノードと worker ノードの両方に少なくとも 8 コアと 28 GB のメモリのクラスターを用意することをお勧めします。 D4 インスタンスの仮想マシンにはこのような容量があります。 クラスターのパフォーマンスを最大限にするために、[こちらの手順](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-apache-spark-resource-manager)に従ってパラメーター spark.executor.instances、spark.executor.cores、および spark.executor.memory を変更し、"カスタムの Spark の既定値" セクションの定義を編集します。

     **トラブルシューティング**: Azure サブスクリプションには、使用できるコア数にクォータが設定されている場合があります。 Azure Portal では、クォータ数を超える合計コア数のクラスターは作成できません。 クォータを確認するには、Azure Portal の [サブスクリプション] セクションに移動し、クラスターのデプロイに使用されているサブスクリプションをクリックし、**[使用量 + クォータ]** をクリックします。 通常、クォータは Azure リージョンごとに定義されており、空きコアが十分にあるリージョンに Spark コアをデプロイすることができます。 

* データセットの格納に使用される Azure ストレージ アカウントを作成します。 [こちらの手順](https://docs.microsoft.com/en-us/azure/storage/common/storage-create-storage-account)に従って、ストレージ アカウントを作成してください。

## <a name="data-description"></a>データの説明

[TalkingData データセット](https://www.kaggle.com/c/talkingdata-mobile-user-demographics/data)を使用します。 このデータセットには、携帯電話のアプリのイベントが含まれています。 目標は、携帯電話の種類とユーザーが最近生成したイベントに基づいて、携帯電話ユーザーの性別と年齢カテゴリを予測することです。  

## <a name="scenario-structure"></a>シナリオの構造
このシナリオでは、GitHub リポジトリに複数のフォルダーがあります。 コードと構成ファイルは **Code** フォルダーにあり、すべてのドキュメントは **Docs** フォルダーにあり、すべての画像は **Images** フォルダーにあります。 ルート フォルダーには、このシナリオの簡単な概要が記載されている README ファイルがあります。

### <a name="getting-started"></a>使用の開始
Azure Machine Learning Workbench アイコンをクリックして Azure Machine Learning Workbench を実行し、"Distributed Tuning of Hyperparameters" テンプレートからプロジェクトを作成します。 新しいプロジェクトを作成する詳細な手順については、[インストールと作成のクイックスタート](quickstart-installation.md)に関するページを参照してください。   

### <a name="configuration-of-execution-environments"></a>実行環境の構成
リモート Docker コンテナーと Spark クラスターでコードを実行する方法について説明します。 まず、両方の環境に共通する設定について説明します。 

ここでは、Azure Machine Learning Workbench の既定の Docker コンテナーでは提供されていない [scikit-learn](https://anaconda.org/conda-forge/scikit-learn)、[xgboost](https://anaconda.org/conda-forge/xgboost)、および [azure-storage](https://pypi.python.org/pypi/azure-storage) パッケージを使用します。 azure-storage パッケージを使用するには、[cryptography](https://pypi.python.org/pypi/cryptography) パッケージと [azure](https://pypi.python.org/pypi/azure) パッケージをインストールする必要があります。 Docker コンテナーと Spark クラスターのノードにこれらのパッケージをインストールするには、conda_dependencies.yml ファイルを次のように変更します。

    name: project_environment
    channels:
      - conda-forge
    dependencies:
      - python=3.5.2
      - scikit-learn
      - xgboost
      - pip:
        - cryptography
        - azure
        - azure-storage

変更した conda\_dependencies.yml ファイルは、チュートリアルの aml_config ディレクトリに保存します。 

次の手順では、実行環境を Azure アカウントに接続します。 AML Workbench の左上にある [ファイル] メニューをクリックし、[コマンド プロンプトを開く] を選択して、コマンド ライン ウィンドウ (CLI) を開きます。 CLI で次のコマンドを実行します

    az login

次のメッセージを取得します

    To sign in, use a web browser to open the page https://aka.ms/devicelogin and enter the code <code> to authenticate.

この Web ページにアクセスし、コードを入力して Azure アカウントにサインインします。 この手順の後に CLI で次のコマンドを実行します

    az account list -o table

AML Workbench ワークスペース アカウントがある Azure サブスクリプションのサブスクリプション ID を確認します。 最後に、CLI で次のコマンドを実行します

    az account set -s <subscription ID>

これで Azure サブスクリプションへの接続を完了します。

次の 2 つのセクションでは、リモート Docker と Spark クラスターの構成を完了する方法について説明します。

#### <a name="configuration-of-remote-docker-container"></a>リモート Docker コンテナーの構成

 リモート Docker コンテナーを設定するには、CLI で次のコマンドを実行します。

    az ml computetarget attach --name dsvm --address <IP address> --username <username> --password <password> --type remotedocker

このコマンドで、DSVM の IP アドレス、ユーザー名、およびパスワードを指定します。 DSVM の IP アドレスは、Azure Portal の DSVM ページの [概要] セクションで確認できます。

![VM IP](media/scenario-distributed-tuning-of-hyperparameters/vm_ip.png)

#### <a name="configuration-of-spark-cluster"></a>Spark クラスターの構成

Spark 環境を設定するには、CLI で次のコマンドを実行します

    az ml computetarget attach --name spark --address <cluster name>-ssh.azurehdinsight.net  --username <username> --password <password> --type cluster

このコマンドで、クラスターの名前、クラスターの SSH ユーザー名とパスワードを指定します。 クラスターのプロビジョニング時に変更していない場合、SSH ユーザー名の既定値は `sshuser` です。 クラスターの名前は、Azure Portal のクラスター ページの [プロパティ] セクションで確認できます。

![クラスター名](media/scenario-distributed-tuning-of-hyperparameters/cluster_name.png)

spark-sklearn パッケージを使用して、ハイパーパラメーターの分散チューニングの実行環境として Spark を設定することができます。 ここでは、Spark 実行環境を使用するときにこのパッケージをインストールするように spark_dependencies.yml ファイルを変更しました。

    configuration: {}
    repositories:
      - "https://mmlspark.azureedge.net/maven"
      - "https://spark-packages.org/packages"
    packages:
      - group: "com.microsoft.ml.spark"
        artifact: "mmlspark_2.11"
        version: "0.7"
      - group: "databricks"
        artifact: "spark-sklearn"
        version: "0.2.0"

変更した spark\_dependencies.yml ファイルは、チュートリアルの aml_config ディレクトリに保存します。 

### <a name="data-ingestion"></a>データの取り込み
このシナリオのコードでは、データが Azure BLOB ストレージに保存されることを前提としています。 まず Kaggle サイトのデータをコンピューターにダウンロードし、BLOB ストレージにアップロードする方法について説明します。 次に、BLOB ストレージからデータを読み取る方法について説明します。 

Kaggle からデータをダウンロードするには、[データセット ページ](https://www.kaggle.com/c/talkingdata-mobile-user-demographics/data)に移動し、[ダウンロード] ボタンをクリックします。 Kaggle にログインするように求められます。 ログイン後は、元のデータセット ページにリダイレクトされます。 次に、左側の列で最初の 7 個のファイルを選択し、[ダウンロード] ボタンをクリックしてダウンロードします。 ダウンロードしたファイルの合計サイズは、289 MB です。 これらのファイルを BLOB ストレージにアップロードするには、ストレージ アカウントで BLOB ストレージ コンテナーの "データセット" を作成します。 作成するには、ストレージ アカウントの Azure ページに移動し、BLOB をクリックし、+ コンテナーをクリックします。 [名前] に「dataset」と入力し、[OK] をクリックします。 次のスクリーン ショットは、これらの手順を示しています。

![BLOB を開く](media/scenario-distributed-tuning-of-hyperparameters/open_blob.png)
![コンテナーを開く](media/scenario-distributed-tuning-of-hyperparameters/open_container.png)

その後、一覧からデータセット コンテナーを選択し、[アップロード] ボタンをクリックします。 Azure Portal では、同時に複数のファイルをアップロードすることができます。 BLOB のアップロード セクションでフォルダー ボタンをクリックし、データセットのすべてのファイルを選択して 開くをクリックし、アップロードをクリックします。 次のスクリーンショットはこれらの手順を示しています。

![BLOB のアップロード](media/scenario-distributed-tuning-of-hyperparameters/upload_blob.png) 

使用しているインターネット接続によって変わりますが、ファイルのアップロードには数分かかります。 

このコードでは、[Azure Storage SDK](https://azure-storage.readthedocs.io/en/latest/) を使用して、BLOB ストレージのデータセットを現在の実行環境にダウンロードしています。 ダウンロードは、load_data.py ファイルの load\_data() 関数で実行されます。 このコードを使用するには、<ACCOUNT_NAME> と <ACCOUNT_KEY> を、データセットをホストしているストレージ アカウントの名前とプライマリ キーで置き換える必要があります。 アカウント名は、ストレージ アカウントの Azure ページの左上に表示されます。 アカウント キーを取得するには、ストレージ アカウントの Azure ページで [アクセス キー] を選択し (「データの取り込み」セクションの最初のスクリーンショットを参照してください)、キー列の最初の行の長い文字列をコピーします。
 
![アクセス キー](media/scenario-distributed-tuning-of-hyperparameters/access_key.png)

load_data() 関数の次のコードで、1 つのファイルがダウンロードされます。

    from azure.storage.blob import BlockBlobService

    # Define storage parameters 
    ACCOUNT_NAME = "<ACCOUNT_NAME>"
    ACCOUNT_KEY = "<ACCOUNT_KEY>"
    CONTAINER_NAME = "dataset"

    # Define blob service     
    my_service = BlockBlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)

    # Load blob
    my_service.get_blob_to_path(CONTAINER_NAME, 'app_events.csv.zip', 'app_events.csv.zip')

load_data.py ファイルを手動で実行する必要はありません。 このファイルは、後で他のファイルから呼び出されます。

### <a name="feature-engineering"></a>特徴エンジニアリング
すべての機能を計算するコードは、feature\_engineering.py ファイルにあります。 feature_engineering.py ファイルを手動で実行する必要はありません。 このファイルは、後で他のファイルから呼び出されます。

複数の機能セットを作成します。
* 携帯電話のブランドとモデルのワンホット エンコーディング (one\_hot\_brand_model 関数)
* ユーザーが毎週生成するイベントの割合 (weekday\_hour_features 関数)
* ユーザーが毎時生成するイベントの割合 (weekday\_hour_features 関数)
* 曜日と時間を組み合わせてユーザーが生成するイベントの割合 (weekday\_hour_features 関数)
* 各アプリでユーザーが生成するイベントの割合 (one\_hot\_app_labels 関数)
* 各アプリ ラベルでユーザーが生成するイベントの割合 (one\_hot\_app_labels 関数)
* 各アプリ カテゴリでユーザーが生成するイベントの割合 (text\_category_features 関数)
* 生成されたイベントに対して使用されたアプリのカテゴリのインジケーター機能 (one\_hot_category 関数)

これらの機能は、Kaggle カーネルの[アプリとラベルの線形モデル](https://www.kaggle.com/dvasyukova/a-linear-model-on-apps-and-labels)から着想を得ました。

これらの機能の計算には、大量のメモリが必要です。 最初は、16 GB の RAM が搭載されたローカル環境で機能を計算しようとしました。 最初の 4 セットの機能は計算できましたが、5 つ目の機能セットを計算しようとすると、"メモリ不足" エラーが発生しました。 最初の 4 つの機能セットの計算は、singleVMsmall.py ファイル内にあります。そのため、ローカル環境の 

     az ml experiment submit -c local .\singleVMsmall.py   

CLI ウィンドウで上のコマンドを実行します。

ローカル環境では小さすぎてすべての機能セットを計算できないので、メモリ サイズが大きいリモートの DSVM に切り替えます。 DSVM 内の実行は、AML Workbench で管理されている Docker コンテナー内で完了します。 この DSVM を使用して、すべての機能を計算し、モデルをトレーニングし、ハイパーパラメーターをチューニングできます (次のセクションを参照してください)。 singleVM.py ファイルには、完全な機能の計算とコードのモデリングが含まれています。 次のセクションでは、リモートの DSVM で singleVM.py を実行する方法を示します。 

### <a name="tuning-hyperparameters-using-remote-dsvm"></a>リモートの DSVM を使用したハイパーパラメーターのチューニング
グラデーション ツリー ブーストの [xgboost](https://anaconda.org/conda-forge/xgboost) 実装 [1] を使用します。 [scikit-learn](http://scikit-learn.org/) パッケージを使用して、xgboost のハイパーパラメーターをチューニングします。 xgboost は scikit-learn パッケージに含まれていませんが、scikit-learn API を実装しているため、scikit-learn のハイパーパラメーター チューニング関数と併用できます。 

Xgboost には 8 個のハイパーパラメーターがあります。
* n_esitmators
* max_depth
* reg_alpha
* reg_lambda
* colsample\_by_tree
* learning_rate
* colsample\_by_level
* subsample
* objective これらのハイパーパラメーターの説明については、[こちら](http://xgboost.readthedocs.io/en/latest/python/python_api.html#module-xgboost.sklearn)と[こちら](https://github.com/dmlc/xgboost/blob/master/doc/parameter.md)を参照してください。 最初に、リモートの DSVM を使用し、少数のグリッドの候補値からハイパーパラメーターをチューニングします。

    tuned_parameters = [{'n_estimators': [300,400], 'max_depth': [3,4], 'objective': ['multi:softprob'], 'reg_alpha': [1], 'reg_lambda': [1], 'colsample_bytree': [1],'learning_rate': [0.1], 'colsample_bylevel': [0.1,], 'subsample': [0.5]}]  

このグリッドには、ハイパーパラメーターの値の組み合わせが 4 つあります。 5 フォールドのクロス検証を使用すると、xgboost の実行が 4x5=20 になります。 モデルのパフォーマンスを測定するために、負のログ損失メトリックを使用します。 次のコードで、クロス検証された負のログ損失を最大化するグリッドのハイパーパラメーター値がわかります。 また、このコードはこれらの値を使用して、完全なトレーニング セットで最終的なモデルをトレーニングします。

    clf = XGBClassifier(seed=0)
    metric = 'neg_log_loss'
    
    clf_cv = GridSearchCV(clf, tuned_parameters, scoring=metric, cv=5, n_jobs=8)
    model = clf_cv.fit(X_train,y_train)

モデルを作成した後に、ハイパーパラメーター チューニングの結果を保存します。 AML Workbench のロギング API を使用して、ハイパーパラメーターの最適な値と、対応するクロス検証済みの負のログ損失の推定値を保存します。

    from azureml.logging import get_azureml_logger

    # initialize logger
    run_logger = get_azureml_logger()

    ...

    run_logger.log(metric, float(clf_cv.best_score_))

    for key in clf_cv.best_params_.keys():
        run_logger.log(key, clf_cv.best_params_[key]) 

また、グリッド内のハイパーパラメーター値のすべての組み合わせについてクロス検証した負のログ損失を使用して、sweeping_results.txt ファイルも作成します。

    if not path.exists('./outputs'):
        makedirs('./outputs')
    outfile = open('./outputs/sweeping_results.txt','w')

    print("metric = ", metric, file=outfile)
    for i in range(len(model.grid_scores_)):
        print(model.grid_scores_[i], file=outfile)
    outfile.close()

このファイルは特殊な ./outputs ディレクトリに格納されます。 後でダウンロード方法について説明します。  

 最初にリモートの DSVM で singleVM.py を実行する前に、Docker コンテナーを作成します。作成には、次のコマンドを 

    az ml experiment prepare -c dsvm

CLI ウィンドウで実行します。 Docker コンテナーの作成には数分かかります。 その後、DSVM で singleVM.py を実行します。

    az ml experiment submit -c dsvm .\singleVM.py

DSVM に 8 コアと 28 GB のメモリが搭載されている場合、このコマンドは 1 時間 38 分で完了します。 ログに記録された値は、AML Workbench の [実行履歴] ウィンドウで確認できます。

![実行履歴](media/scenario-distributed-tuning-of-hyperparameters/run_history.png)

既定で [実行履歴] ウィンドウには、最初の 1 ～ 2 個のログに記録された値とグラフが表示されます。 選択した ハイパーパラメーター値の完全な一覧を表示するには、前のスクリーンショットで赤枠で囲まれた設定アイコンをクリックし、表に表示するハイパーパラメーターを選択します。 また、[実行履歴] ウィンドウの上部に表示されるグラフを選択するには、青枠で囲まれた設定アイコンをクリックし、一覧からグラフを選択します。 

ハイパーパラメーターの選択されている値は、[実行のプロパティ] ウィンドウでも確認できます。 

![実行のプロパティ](media/scenario-distributed-tuning-of-hyperparameters/run_properties.png)

[実行のプロパティ] ウィンドウの右上には、実行環境の '.\output' フォルダーに作成されたすべてのファイルの一覧が掲載された [出力ファイル] セクションがあります。 ここで sweeping\_results.txt を選択し、[ダウンロード] ボタンをクリックしてダウンロードすることができます。 sweeping_results.txt の出力は次のようになります。

    metric =  neg_log_loss
    mean: -2.29096, std: 0.03748, params: {'colsample_bytree': 1, 'learning_rate': 0.1, 'subsample': 0.5, 'n_estimators': 300, 'reg_alpha': 1, 'objective': 'multi:softprob', 'colsample_bylevel': 0.1, 'reg_lambda': 1, 'max_depth': 3}
    mean: -2.28712, std: 0.03822, params: {'colsample_bytree': 1, 'learning_rate': 0.1, 'subsample': 0.5, 'n_estimators': 400, 'reg_alpha': 1, 'objective': 'multi:softprob', 'colsample_bylevel': 0.1, 'reg_lambda': 1, 'max_depth': 3}
    mean: -2.28706, std: 0.03863, params: {'colsample_bytree': 1, 'learning_rate': 0.1, 'subsample': 0.5, 'n_estimators': 300, 'reg_alpha': 1, 'objective': 'multi:softprob', 'colsample_bylevel': 0.1, 'reg_lambda': 1, 'max_depth': 4}
    mean: -2.28530, std: 0.03927, params: {'colsample_bytree': 1, 'learning_rate': 0.1, 'subsample': 0.5, 'n_estimators': 400, 'reg_alpha': 1, 'objective': 'multi:softprob', 'colsample_bylevel': 0.1, 'reg_lambda': 1, 'max_depth': 4}

### <a name="tuning-hyperparameters-using-spark-cluster"></a>Spark クラスターを使用したハイパーパラメーターのチューニング
ここでは、Spark クラスターを使用してハイパーパラメーターのチューニングをスケール アウトし、大きなグリッドを使用します。 新しいグリッドは次のとおりです。

    tuned_parameters = [{'n_estimators': [300,400], 'max_depth': [3,4], 'objective': ['multi:softprob'], 'reg_alpha': [1], 'reg_lambda': [1], 'colsample_bytree': [1], 'learning_rate': [0.1], 'colsample_bylevel': [0.01, 0.1], 'subsample': [0.5, 0.7]}]

このグリッドには、ハイパーパラメーターの値の組み合わせが 16 個あります。 5 フォールドのクロス検証を使用しているので、xgboost を 16x5=80 回実行します。

scikit-learn パッケージは、Spark クラスターを使用したハイパーパラメーターのチューニングをネイティブでサポートしていませんが、 Databricks の [spark-sklearn](https://spark-packages.org/package/databricks/spark-sklearn) パッケージでこのギャップを埋めることができます。 このパッケージには GridSearchCV 関数があります。この関数は、scikit-learn の GridSearchCV 関数とほぼ同じ API です。 spark-sklearn を使用し、Spark を使用してハイパーパラメーターをチューニングするには、接続して Spark コンテキストを作成する必要があります。

    from pyspark import SparkContext
    sc = SparkContext.getOrCreate()

次に、 

    from sklearn.model_selection import GridSearchCV

を以下に置き換えることができます。 

    from spark_sklearn import GridSearchCV

また、scikit-learn の GridSearchCV の呼び出しを spark-sklearn の呼び出しに置き換えます。

    clf_cv = GridSearchCV(sc = sc, param_grid = tuned_parameters, estimator = clf, scoring=metric, cv=5)

Spark を使用してハイパーパラメーターをチューニングする最終的なコードは、distributed\_sweep.py ファイル内にあります。 singleVM.py と distributed_sweep.py の違いは、グリッドの定義と、4 行の追加コードです。 また、AML Workbench サービスがあるので、実行環境をリモートの DSVM から Spark クラスターに変更するときにロギング コードは変わりません。

最初に Spark クラスターで distributed_sweep.py を実行する前に、そこに Python パッケージをインストールする必要があります。 この処理を実行するには、次のコマンドを 

    az ml experiment prepare -c spark

CLI ウィンドウで実行します。 このインストールには数分かかります。 その後、Spark クラスターで distributed_sweep.py を実行します

    az ml experiment submit -c spark .\distributed_sweep.py

Spark クラスターに 28 GB のメモリが搭載された worker ノードが 6 個ある場合、このコマンドは 1 時間 6 分で完了します。 Spark クラスターのハイパーパラメーターのチューニング結果、つまりログ、ハイパーパラメーターの最適な値、および sweeping_results.txt ファイルには、リモートの DSVM 実行と同じ方法で Azure Machine Learning Workbench でアクセスできます。 

### <a name="architecture-diagram"></a>アーキテクチャ ダイアグラム

次に、エンドツーエンドのワークフロー図を示します。![アーキテクチャ](media/scenario-distributed-tuning-of-hyperparameters/architecture.png) 

## <a name="conclusion"></a>まとめ 

このシナリオでは、Azure Machine Learning Workbench を使用して、リモートの仮想マシンとリモートの Spark クラスターでハイパーパラメーターのチューニングを実行する方法について説明しました。 また、Azure Machine Learning Workbench には、実行環境を簡単に構成し、切り替えることができるツールが用意されていることも説明しました。 

## <a name="references"></a>参照

[1] T. Chen および C. Guestrin。 [XGBoost: A Scalable Tree Boosting System](https://arxiv.org/abs/1603.02754)。 (KDD 2016)。




