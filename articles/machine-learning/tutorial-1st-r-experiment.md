---
title: チュートリアル:R を使用して機械学習モデルを作成する (プレビュー)
titleSuffix: Azure Machine Learning
description: このチュートリアルでは、Azure Machine Learning R SDK を使用して、自動車事故における死亡の可能性を予測するロジスティック回帰モデルを作成します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.reviewer: sgilley
author: revodavid
ms.author: davidsmi
ms.date: 02/07/2020
ms.openlocfilehash: 8d5a8555b2bd57dc024318464e38f042469beb4b
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2020
ms.locfileid: "89458673"
---
# <a name="tutorial-use-r-to-create-a-machine-learning-model-preview"></a>チュートリアル:R を使用して機械学習モデルを作成する (プレビュー)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

> [!IMPORTANT]
> Azure Machine Learning R SDK は現在、パブリック プレビューの段階にあります。
> プレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

このチュートリアルでは、Azure Machine Learning R SDK (プレビュー) を使用して、自動車事故における死亡の可能性を予測するロジスティック回帰モデルを作成します。 Azure Machine Learning のクラウド リソースと R を組み合わせて、モデルのトレーニングとデプロイのためのスケーラブルな環境を実現する方法について見ていきましょう。  

このチュートリアルでは、以下のタスクを実行します。
> [!div class="checklist"]
> * Azure Machine Learning ワークスペースの作成
> * ワークスペースから RStudio を開く
> * このチュートリアルの実行に必要なファイルがある https://github.com/Azure/azureml-sdk-for-r をワークスペースにクローンする
> * データを読み込み、トレーニングを準備する
> * データをデータストアにアップロードして、リモート トレーニングに使用できるようにする
> * モデルをリモートでトレーニングするためのコンピューティング リソースを作成する
> * `caret` モデルをトレーニングして死亡する確率を予測する
> * 予測エンドポイントをデプロイする
> * R からのモデルをテストする

Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning](https://aka.ms/AMLFree) を今すぐお試しください。


## <a name="create-a-workspace"></a>ワークスペースの作成

Azure Machine Learning ワークスペースは、機械学習モデルを実験、トレーニング、およびデプロイするために使用する、クラウドでの基本的なリソースです。 ワークスペースは、Azure サブスクリプションとリソース グループを、サービス内の簡単に使用できるオブジェクトに結び付けます。 

ワークスペースを作成するには、Azure リソースを管理するための Web ベースのコンソールである Azure portal を使用します。 

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

>[!IMPORTANT] 
> お使いの**ワークスペース**と**サブスクリプション**をメモしておきます。 これらは、適切な場所に実験を作成するために必要になります。 


## <a name="open-rstudio"></a><a name="open"></a>RStudio を開く

この例では、インストール不要の事前構成されたエクスペリエンスを実現するために、お使いのワークスペースのコンピューティング インスタンスを使用します。 自分のマシンで環境、パッケージ、依存関係を制御したい場合は、[独自の環境](https://azure.github.io/azureml-sdk-for-r/articles/installation.html)を使用してください。

このチュートリアルを実行するには、Azure ML コンピューティング インスタンスで RStudio を使用します。  

1. 左側にある **[Compute]** を選択します。

1. コンピューティング リソースがまだ存在しない場合は追加します。

1. コンピューティングが実行されたら、**RStudio** リンクを使用して RStudio を開きます。


## <a name="clone-the-sample-vignettes"></a><a name="azure"></a>サンプルのビネットをクローンする 

このチュートリアルで実行するビネット ファイルのコピーのために https://github.com/azure/azureml-sdk-for-r GitHub リポジトリをクローンします。

1. RStudio で、[Terminal]\(ターミナル\) タブに移動し、リポジトリをクローンするディレクトリに移動します。

1. ターミナルで "git clone https://github.com/Azure/azureml-sdk-for-r" を実行して、リポジトリをクローンします。

1. RStudio で、クローンされた *azureml-sdk-for-r* フォルダーの *vignettes* フォルダーに移動します。  *vignettes* の下で、 *train-and-deploy-first-model.Rmd* ファイルを選択し、このチュートリアルで使用されるビネットを検索します。 このビネットに使用されるその他のファイルは、*train-and-deploy-first-model* サブフォルダーにあります。 ビネットを開いたら、 **[Session]\(セッション\) > [Set Working Directory]\(作業ディレクトリの設定\) > [To Source File Location]\(ソース ファイルの場所に\)** を使用して、作業ディレクトリをそのファイルの場所に設定します。 

> [!Important]
> 以降この記事には、*train-and-deploy-first-model.Rmd* ファイルと同じ内容が記載されています。 RMarkdown の使用経験がある場合は、そのファイルのコードを自由に使用できます。  また、そこから、またはこの記事から、コード スニペットをコピーして R スクリプトまたはコマンド ラインに貼り付けることもできます。 


## <a name="set-up-your-development-environment"></a>開発環境を設定する
このチュートリアルで開発作業を行うためのセットアップには、次のアクションが含まれています。

* 必要なパッケージをインストールする
* コンピューティング インスタンスがリモート リソースと通信できるように、ワークスペースに接続します
* 実験を作成して、実行を追跡する
* リモートのコンピューティング先を作成して、トレーニングに使用する

### <a name="install-required-packages"></a>必要なパッケージをインストールする
コンピューティング インスタンスには、CRAN の R SDK の最新バージョンが既にインストールされています。 代わりに GitHub から開発バージョンをインストールして、最新のバグ修正プログラムを取得する場合は、次のコードを実行してください。
    
```R
remotes::install_github('https://github.com/Azure/azureml-sdk-for-r')
azuremlsdk::install_azureml()
```

> [!WARNING]
> インストール プロセス中に "`Would you like to install Miniconda? [Y/n]:`" というメッセージが表示された場合は、"`n`" で応答してください。コンピューティング インスタンスには既に Anaconda がインストールされているため、Miniconda のインストールは必要ありません。

先に進んで、**azuremlsdk** パッケージをインポートします。

```R
library(azuremlsdk)
```

トレーニング スクリプトとスコアリング スクリプト (`accidents.R` と `accident_predict.R`) には、いくつかの追加の依存関係があります。 これらのスクリプトをローカルで実行する予定の場合は、それらの必要なパッケージがあることも確認します。

### <a name="load-your-workspace"></a>ワークスペースを読み込む
既存のワークスペースからワークスペース オブジェクトをインスタンス化します。 次のコードでは、**config.json** ファイルからワークスペースの詳細を読み込みます。 [`get_workspace()`](https://azure.github.io/azureml-sdk-for-r/reference/get_workspace.html) を使用してワークスペースを取得することもできます。

```R
ws <- load_workspace_from_config()
```

### <a name="create-an-experiment"></a>実験の作成
Azure ML 実験では、通常は同じトレーニング スクリプトから実行のグループを追跡します。 実験を作成して、事故データに対する caret モデルのトレーニングの実行を追跡します。

```R
experiment_name <- "accident-logreg"
exp <- experiment(ws, experiment_name)
```

### <a name="create-a-compute-target"></a>コンピューティング ターゲットを作成する
マネージド サービスである Azure Machine Learning コンピューティング (AmlCompute) を使用することにより、データ サイエンティストは Azure 仮想マシンのクラスター上で機械学習モデルをトレーニングできます。 たとえば、GPU がサポートされている VM などです。 このチュートリアルでは、トレーニング環境として単一ノードの AmlCompute クラスターを作成します。 以下のコードでは、ワークスペース内にまだコンピューティング クラスターがない場合、それが作成されます。

まだ存在していない場合は、コンピューティング クラスターがプロビジョニングされるまで数分かかることがあります。

```R
cluster_name <- "rcluster"
compute_target <- get_compute(ws, cluster_name = cluster_name)
if (is.null(compute_target)) {
  vm_size <- "STANDARD_D2_V2" 
  compute_target <- create_aml_compute(workspace = ws,
                                       cluster_name = cluster_name,
                                       vm_size = vm_size,
                                       max_nodes = 1)
}

wait_for_provisioning_completion(compute_target)
```

## <a name="prepare-data-for-training"></a>トレーニング用のデータを準備する
このチュートリアルでは、[米国運輸省道路交通安全局](https://cdan.nhtsa.gov/tsftables/tsfar.htm)のデータを使用します ([Mary C. Meyer 氏および Tremika Finney 氏による提供](https://www.stat.colostate.edu/~meyer/airbags.htm))。
このデータセットには、米国での 25,000 を超える交通事故のデータと、死亡の可能性の予測に使用できる変数が含まれています。 まず、データを R にインポートし、分析のために新しいデータフレーム `accidents` に変換して、`Rdata` ファイルにエクスポートします。

```R
nassCDS <- read.csv("nassCDS.csv", 
                     colClasses=c("factor","numeric","factor",
                                  "factor","factor","numeric",
                                  "factor","numeric","numeric",
                                  "numeric","character","character",
                                  "numeric","numeric","character"))
accidents <- na.omit(nassCDS[,c("dead","dvcat","seatbelt","frontal","sex","ageOFocc","yearVeh","airbag","occRole")])
accidents$frontal <- factor(accidents$frontal, labels=c("notfrontal","frontal"))
accidents$occRole <- factor(accidents$occRole)
accidents$dvcat <- ordered(accidents$dvcat, 
                          levels=c("1-9km/h","10-24","25-39","40-54","55+"))

saveRDS(accidents, file="accidents.Rd")
```

### <a name="upload-data-to-the-datastore"></a>データストアにデータをアップロードする
データをクラウドにアップロードして、リモート トレーニング環境からアクセスできるようにします。 Azure Machine Learning の各ワークスペースには既定のデータストアが備わっており、ワークスペースにアタッチされているストレージ アカウントにプロビジョニングされる Azure BLOB コンテナーへの接続情報が格納されます。 次のコードは、上記で作成した事故データをそのデータストアにアップロードします。

```R
ds <- get_default_datastore(ws)

target_path <- "accidentdata"
upload_files_to_datastore(ds,
                          list("./accidents.Rd"),
                          target_path = target_path,
                          overwrite = TRUE)
```


## <a name="train-a-model"></a>モデルをトレーニングする

このチュートリアルでは、リモート コンピューティング クラスターを使用して、アップロードされたデータにロジスティック回帰モデルを適合させます。 ジョブを送信するには、次のことを行う必要があります。

* トレーニング スクリプトを準備する
* 推定を作成する
* ジョブを送信する

### <a name="prepare-the-training-script"></a>トレーニング スクリプトを準備する
`accidents.R` というトレーニング スクリプトが *train-and-deploy-first-model* ディレクトリに用意されています。 Azure Machine Learning サービスをトレーニングに活用するために行われた**トレーニング スクリプト内**の次の詳細に注意してください。

* このトレーニング スクリプトは、引数 `-d` を受け取り、トレーニング データが含まれるディレクトリを検出します。 後でジョブを定義して送信する際に、次のように、引数にデータストアを指定します。 Azure ML は、トレーニング ジョブ用にストレージ フォルダーをリモート クラスターにマウントします。
* トレーニング スクリプトは、`log_metric_to_run()` を使用して Azure ML の実行レコードにメトリックとして最終的な精度をログに記録します。 Azure ML SDK には、トレーニングの実行中にさまざまなメトリックをログに記録するためのログ記録 API のセットが用意されています。 これらのメトリックは記録され、実験の実行レコードに保存されます。 メトリックにはいつでもアクセスでき、[スタジオ](https://ml.azure.com)の実行の詳細ページで表示することもできます。 `log_*()` のログ記録方法の完全なセットについては、[リファレンス](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-training-experimentation)を参照してください。
* トレーニング スクリプトでは、**outputs** という名前のディレクトリにモデルが保存されます。 `./outputs` フォルダーは、Azure ML によって特別に処理されます。 トレーニング中、`./outputs` に書き込まれるファイルは、Azure ML によって実行レコードに自動的にアップロードされ、成果物として永続化されます。 トレーニング済みのモデルを `./outputs` に保存することにより、実行が終了し、リモート トレーニング環境にアクセスできなくなった後でも、モデル ファイルにアクセスして取得することができます。

### <a name="create-an-estimator"></a>推定を作成する

Azure ML Estimator には、コンピューティング先でトレーニング スクリプトを実行するために必要な実行構成情報がカプセル化されています。 Azure ML の実行は、指定されたコンピューティング先でコンテナー化されたジョブとして実行されます。 既定では、トレーニング ジョブ用に作成された Docker イメージには、R、Azure ML SDK、および一般的に使用される R パッケージのセットが含まれます。 ここに記載されている既定のパッケージの完全な一覧を参照してください。

推定を作成するには、次のものを定義します。

* トレーニングに必要なスクリプトが格納されているディレクトリ (`source_directory`)。 このディレクトリ内のすべてのファイルは、実行のためにクラスター ノード内にアップロードされます。 このディレクトリには、トレーニング スクリプトと、必要な追加のスクリプトが含まれている必要があります。
* 実行されるトレーニング スクリプト (`entry_script`)。
* コンピューティング先 (`compute_target`)。この場合は、前に作成した AmlCompute クラスター。
* トレーニング スクリプトからの必須パラメーター (`script_params`)。 Azure ML は、`Rscript` を使用して、トレーニング スクリプトをコマンドライン スクリプトとして実行します。 このチュートリアルでは、スクリプトに 1 つの引数 (`ds$path(target_path)` でアクセスできるデータ ディレクトリのマウント ポイント) を指定します。
* トレーニングに必要な環境の依存関係。 トレーニング用に構築された既定の Docker イメージには、トレーニング スクリプトで必要とされる 3 つのパッケージ (`caret`、`e1071`、`optparse`) があらかじめ含まれています。  そのため、追加情報を指定する必要はありません。 既定では含まれていない R パッケージを使用する場合は、Estimator の `cran_packages` パラメーターを使用して、CRAN パッケージを追加します。 構成可能なオプションの完全なセットについては、[`estimator()`](https://azure.github.io/azureml-sdk-for-r/reference/estimator.html) リファレンスを参照してください。

```R
est <- estimator(source_directory = "train-and-deploy-first-model",
                 entry_script = "accidents.R",
                 script_params = list("--data_folder" = ds$path(target_path)),
                 compute_target = compute_target
                 )
```

### <a name="submit-the-job-on-the-remote-cluster"></a>リモート クラスターでジョブを送信する

最後に、クラスターで実行するジョブを送信します。 `submit_experiment()` は、後で実行とのインターフェイスに使用する実行オブジェクトを返します。 初回の実行は合計で**約 10 分**かかります。 しかし、その後の実行では、スクリプトの依存関係が変わらなければ、同じ Docker イメージが再利用されます。  この場合、イメージはキャッシュされ、コンテナーの起動時間は大幅に短縮されます。

```R
run <- submit_experiment(exp, est)
```

実行の詳細は、RStudio Viewer で確認できます。 表示された [Web ビュー] リンクをクリックすると、Azure Machine Learning Studio に移動し、UI で実行を監視できます。

```R
view_run_details(run)
```

モデルのトレーニングはバック グラウンドで行われます。 モデルのトレーニングが完了するまで待ってから、さらにコードを実行します。

```R
wait_for_run_completion(run, show_output = TRUE)
```

自分と、ワークスペースへのアクセス権を持つ同僚は、複数の実験を並行して送信することができます。Azure ML は、コンピューティング クラスターでのタスクのスケジュールを設定します。 複数のノードに自動的にスケールアップするようにクラスターを構成することもできます。また、キューにコンピューティング タスクがない場合は、スケールバックすることもできます。 この構成は、チームがコンピューティング リソースを共有するためのコスト効率に優れた方法です。

## <a name="retrieve-training-results"></a>トレーニング結果の取得
モデルのトレーニングが完了すると、ログに記録されたメトリックや最終的にトレーニングされたモデルなど、実行レコードに永続化されたジョブの成果物にアクセスできるようになります。

### <a name="get-the-logged-metrics"></a>ログに記録されたメトリックを取得する
トレーニング スクリプト `accidents.R` では、モデルからのメトリック (トレーニング データ内の予測の精度) をログに記録しました。 メトリックは、[Studio](https://ml.azure.com) に表示することも、次のように R の一覧としてローカル セッションに抽出することもできます。

```R
metrics <- get_run_metrics(run)
metrics
```

(たとえば、異なる変数、アルゴリズム、またはハイパーパラメーターを使用して) 複数の実験を実行した場合は、各実行のメトリックを使用して、運用環境で使用するモデルを比較して選択することができます。

### <a name="get-the-trained-model"></a>トレーニング済みのモデルを取得する
トレーニング済みのモデルを取得し、ローカルの R セッションの結果を確認することができます。 次のコードでは、モデル ファイルが含まれている `./outputs` ディレクトリの内容をダウンロードします。

```R
download_files_from_run(run, prefix="outputs/")
accident_model <- readRDS("outputs/model.rds")
summary(accident_model)
```

推定される死亡率の増加の一因となるいくつかの要因が表示されます。

* 衝撃速度が速い 
* 男性ドライバー
* 高齢の乗員
* 乗客

次のことで、死亡率が低くなることがわかります。

* エアバッグの存在
* シートベルトの装着
* 正面衝突 

車両の製造年が大きな影響を与えることはありません。

このモデルを使用して、新しい予測を行うことができます。

```R
newdata <- data.frame( # valid values shown below
 dvcat="10-24",        # "1-9km/h" "10-24"   "25-39"   "40-54"   "55+"  
 seatbelt="none",      # "none"   "belted"  
 frontal="frontal",    # "notfrontal" "frontal"
 sex="f",              # "f" "m"
 ageOFocc=16,          # age in years, 16-97
 yearVeh=2002,         # year of vehicle, 1955-2003
 airbag="none",        # "none"   "airbag"   
 occRole="pass"        # "driver" "pass"
 )

## predicted probability of death for these variables, as a percentage
as.numeric(predict(accident_model,newdata, type="response")*100)
```

## <a name="deploy-as-a-web-service"></a>Web サービスとしてデプロイする

モデルを使用すると、衝突による死亡の危険性を予測できます。 Azure ML を使用して、予測サービスとしてモデルをデプロイします。 このチュートリアルでは、[Azure Container Instances](https://docs.microsoft.com/azure/container-instances/) (ACI) に Web サービスをデプロイします。

### <a name="register-the-model"></a>モデルを登録する

まず、[`register_model()`](https://azure.github.io/azureml-sdk-for-r/reference/register_model.html) を使用して、ダウンロードしたモデルをワークスペースに登録します。 登録済みのモデルはファイルのコレクションにすることができますが、この場合は R モデル オブジェクトで十分です。 Azure ML は、デプロイ用に登録されたモデルを使用します。

```R
model <- register_model(ws, 
                        model_path = "outputs/model.rds", 
                        model_name = "accidents_model",
                        description = "Predict probablity of auto accident")
```

### <a name="define-the-inference-dependencies"></a>推論の依存関係を定義する
モデル用の Web サービスを作成するには、最初にスコアリング スクリプト (`entry_script`) を作成する必要があります。これは、入力変数の値 (JSON 形式) として取得し、モデルからの予測を出力する R スクリプトです。 このチュートリアルでは、指定されたスコアリング ファイル `accident_predict.R` を使用します。 スコアリング スクリプトには、モデルを読み込み、モデルを使用して入力データに基づいて予測を行う関数を返す、`init()` メソッドが含まれている必要があります。 詳細については、[ドキュメント](https://azure.github.io/azureml-sdk-for-r/reference/inference_config.html#details)を参照してください。

次に、スクリプトのパッケージの依存関係のための Azure ML **環境**を定義します。 環境では、スクリプトの実行に必要な R パッケージ (CRAN またはその他の場所のパッケージ) を指定します。 また、スクリプトで参照できる環境変数の値を指定して、その動作を変更することもできます。 既定では、Azure ML は、トレーニング用に Estimator で使用されるのと同じ既定の Docker イメージを構築します。 このチュートリアルには特別な要件がないため、特別な属性のない環境を作成します。

```R
r_env <- r_environment(name = "basic_env")
```

代わりに、デプロイに独自の Docker イメージを使用する場合は、`custom_docker_image` パラメーターを指定します。 環境を構成するための構成可能なオプションの完全なセットについては、[`r_environment()`](https://azure.github.io/azureml-sdk-for-r/reference/r_environment.html) リファレンスを参照してください。

これで、スコアリング スクリプトと環境の依存関係をカプセル化するための**推論構成**の作成に必要なものがすべて揃いました。

```R
inference_config <- inference_config(
  entry_script = "accident_predict.R",
  source_directory = "train-and-deploy-first-model",
  environment = r_env)
```

### <a name="deploy-to-aci"></a>ACI にデプロイする
このチュートリアルでは、サービスを ACI にデプロイします。 このコードでは、1 つのコンテナーをプロビジョニングして受信要求に応答します。これは、テストや負荷が軽い場合に適しています。 その他の構成可能なオプションについては、[`aci_webservice_deployment_config()`](https://azure.github.io/azureml-sdk-for-r/reference/aci_webservice_deployment_config.html) を参照してください。 (運用規模のデプロイの場合は、[Azure Kubernetes Service にデプロイ](https://azure.github.io/azureml-sdk-for-r/articles/deploy-to-aks.html)することもできます。)

``` R
aci_config <- aci_webservice_deployment_config(cpu_cores = 1, memory_gb = 0.5)
```

ここで、モデルを Web サービスとしてデプロイします。 デプロイには**数分かかることがあります**。 

```R
aci_service <- deploy_model(ws, 
                            'accident-pred', 
                            list(model), 
                            inference_config, 
                            aci_config)

wait_for_deployment(aci_service, show_output = TRUE)
```

## <a name="test-the-deployed-service"></a>デプロイされたサービスをテストする

モデルがサービスとしてデプロイされたので、[`invoke_webservice()`](https://azure.github.io/azureml-sdk-for-r/reference/invoke_webservice.html) を使用して R からサービスをテストできます。  予測するデータの新しいセットを指定し、それを JSON に変換して、サービスに送信します。

```R
library(jsonlite)

newdata <- data.frame( # valid values shown below
 dvcat="10-24",        # "1-9km/h" "10-24"   "25-39"   "40-54"   "55+"  
 seatbelt="none",      # "none"   "belted"  
 frontal="frontal",    # "notfrontal" "frontal"
 sex="f",              # "f" "m"
 ageOFocc=22,          # age in years, 16-97
 yearVeh=2002,         # year of vehicle, 1955-2003
 airbag="none",        # "none"   "airbag"   
 occRole="pass"        # "driver" "pass"
 )

prob <- invoke_webservice(aci_service, toJSON(newdata))
prob
```

Web サービスの HTTP エンドポイントを取得することもできます。このエンドポイントで REST クライアントの呼び出しを受け取ります。 このエンドポイントは、Web サービスをテストしたい、またはアプリケーションに統合したい任意のユーザーと共有できます。

```R
aci_service$scoring_uri
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

不要になったリソースは削除してください。 引き続き使用する予定のリソースは削除しないでください。 

Web サービスを削除します。
```R
delete_webservice(aci_service)
```

登録済みのモデルを削除します。
```R
delete_model(model)
```

コンピューティング クラスターを削除します。
```R
delete_compute(compute)
```

### <a name="delete-everything"></a>すべてを削除する

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

リソース グループは保持しつつ、いずれかのワークスペースを削除することもできます。 ワークスペースのプロパティを表示し、 **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

* これで R での初めての Azure Machine Learning 実験を完了したので、[Azure Machine Learning SDK for R](https://azure.github.io/azureml-sdk-for-r/index.html) の詳細について確認します。

* 他の *vignettes* フォルダーの例から Azure Machine Learning と R について学習します。
