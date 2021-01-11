---
title: 自動 ML の実験を作成する
titleSuffix: Azure Machine Learning
description: 自動機械学習は、アルゴリズムを自動的に選択して、デプロイできる状態のモデルを生成します。 自動機械学習の実験の構成に使用できるオプションについて説明します。
author: cartacioS
ms.author: sacartac
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 08/10/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python,contperfq1
ms.openlocfilehash: 6a37aaa2eee3151087ce33815d37bf5537578329
ms.sourcegitcommit: 9c3cfbe2bee467d0e6966c2bfdeddbe039cad029
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/24/2020
ms.locfileid: "88782755"
---
# <a name="configure-automated-ml-experiments-in-python"></a>Python で自動 ML の実験を構成する
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

このガイドでは、[Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) を使用して、自動機械学習の実験のさまざまな構成設定を定義する方法について説明します。 自動機械学習は、アルゴリズムとハイパーパラメーターを自動的に選択して、デプロイできる状態のモデルを生成します。 自動機械学習の実験の構成に使用できるオプションはいくつかあります。

自動機械学習の実験の例を確認するには、[自動機械学習を使用した分類モデルのトレーニングのチュートリアル](tutorial-auto-train-models.md)に関するページか、「[クラウドで自動機械学習を使用してモデルをトレーニングする](how-to-auto-train-remote.md)」をご覧ください。

自動機械学習で使用できる構成オプション:

* 実験の種類を選択する:分類、回帰、または時系列予測
* データ ソース、形式、およびデータのフェッチ
* コンピューティング先を選択する: ローカルまたはリモート
* 自動機械学習の実験の設定
* 自動機械学習の実験を実行する
* モデル メトリックを探索する
* モデルを登録して展開する

コードを使用しないエクスペリエンスを希望する場合は、[Azure Machine Learning Studio で自動化された機械学習の実験を作成する](how-to-use-automated-ml-for-ml-models.md)こともできます。

## <a name="prerequisites"></a>前提条件

この記事では、以下が必要です。 
* Azure Machine Learning ワークスペース。 ワークスペースを作成するには、[Azure Machine Learning ワークスペース](how-to-manage-workspace.md)の作成に関するページを参照してください。

* Azure Machine Learning Python SDK がインストールされていること。
    SDK をインストールするには、次のいずれかを行います。 
    * コンピューティング インスタンスを作成します。これにより、SDK が自動的にインストールされ、ML ワークフロー用に事前構成されます。 詳細については、「[Azure Machine Learning コンピューティング インスタンスとは](concept-compute-instance.md#managing-a-compute-instance)」を参照してください。 

    * [SDK をご自分でインストールしてください](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)。 必ず extra の `automl` を含めるようにしてください。 

## <a name="select-your-experiment-type"></a>実験の種類を選択する

実験を始める前に、解決する機械学習の問題の種類を決める必要があります。 自動機械学習によって、`classification`、`regression`、`forecasting` というタスクの種類がサポートされています。 [タスクの種類](concept-automated-ml.md#when-to-use-automl-classify-regression--forecast)についての詳細情報を参照してください。

次のコードでは、`AutoMLConfig` コンストラクターで `task` パラメーターが使用され、実験の種類が `classification` として指定されています。

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task = "classification")
```

## <a name="data-source-and-format"></a>データ ソースと形式

自動機械学習では、ローカル デスクトップ上またはクラウド (Azure Blob Storage など) に存在するデータがサポートされます。 データは、**Pandas DataFrame** または **Azure Machine Learning TabularDataset** に読み込むことができます。 [データセットの詳細情報](how-to-create-register-datasets.md).

トレーニング データの要件:
- データは表形式である必要があります。
- 予測する値、ターゲット列は、データ内にある必要があります。

**リモート実験の場合**、トレーニング データにリモート コンピューティングからアクセスできる必要があります。 AutoML により、リモート コンピューティングでの作業時に [Azure Machine Learning TabularDatasets](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) のみが受け入れられます。 

Azure Machine Learning のデータセットによって、次の機能が公開されます。

* 静的ファイルまたは URL ソースからワークスペースにデータを簡単に転送する。
* クラウド コンピューティング リソースでの実行時にデータをトレーニング スクリプトで使用できるようにする。 `Dataset` クラスを使用してコンピューティング ターゲットにデータをマウントする例については、[データセットを使ってトレーニングする方法](how-to-train-with-datasets.md#mount-files-to-remote-compute-targets)を参照してください。

次のコードにより、Web URL から TabularDataset が作成されます。 ローカル ファイルやデータストアなどの他のソースからデータセットを作成する方法のコード例については、「[TabularDatasets を作成する](how-to-create-register-datasets.md#create-a-tabulardataset)」を参照してください。

```python
from azureml.core.dataset import Dataset
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"
dataset = Dataset.Tabular.from_delimited_files(data)
  ```
**ローカル コンピューティングの実験の場合**、より高速な処理時間を実現するために、pandas dataframes をお勧めします。

  ```python
  import pandas as pd
  from sklearn.model_selection import train_test_split

  df = pd.read_csv("your-local-file.csv")
  train_data, test_data = train_test_split(df, test_size=0.1, random_state=42)
  label = "label-col-name"
  ```

## <a name="training-validation-and-test-data"></a>トレーニング、検証、テストのデータ

`AutoMLConfig` コンストラクターでは、個別の**トレーニングおよび検証セット**を直接指定できます。 AutoML 実験用に[データの分割とクロス検証を構成する方法](how-to-configure-cross-validation-data-splits.md)に関するページをご確認ください。 

`validation_data` または `n_cross_validation` パラメーターを明示的に指定しなかった場合、AutoML で検証の実行方法を決定するための既定の手法が適用されます。 この決定は、`training_data` パラメーターに割り当てられたデータセット内の行の数によって異なります。 

|トレーニング&nbsp;データ&nbsp;のサイズ| 検証の方法 |
|---|-----|
|**20,000&nbsp;行&nbsp;より&nbsp;多い**| トレーニング/検証データの分割が適用されます。 既定では、初期トレーニング データ セットの 10% が検証セットとして取得されます。 次に、その検証セットがメトリックの計算に使用されます。
|**20,000&nbsp;行&nbsp;より&nbsp;少ない**| クロス検証アプローチが適用されます。 フォールドの既定の数は行数によって異なります。 <br> **データセットが 1,000 行より少ない場合は**、10 個のフォールドが使用されます。 <br> **行が 1,000 から 20,000 の間の場合は**、3 つのフォールドが使用されます。

現時点では、モデルの評価用に独自の**テストデータ**を提供する必要があります。 モデル評価のために独自のテスト データを取り込むコード例については、[この Jupyter ノートブック](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb)の**テスト**に関するセクションを参照してください。

## <a name="compute-to-run-experiment"></a>実験を実行するために計算する

次に、モデルをトレーニングする場所を決定します。 自動機械学習のトレーニング実験は、次のコンピューティング オプションで実行できます。 [ローカルおよびリモートのコンピューティングの長所と短所](concept-automated-ml.md#local-remote)のオプションについて説明します。 

* ローカル デスクトップやノート PC などの**ローカル** コンピューター – 一般に、データセットが小さく、まだ探索ステージにいる場合。 ローカル コンピューティングの例については、[このノートブック](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/local-run-classification-credit-card-fraud/auto-ml-classification-credit-card-fraud-local.ipynb)を参照してください。 
 
* クラウド上の**リモート** マシン – [Azure Machine Learning Managed Compute](concept-compute-target.md#amlcompute) は、Azure 仮想マシンのクラスター上で機械学習モデルをトレーニングできるようにするマネージド サービスです。 

    Azure Machine Learning Managed Compute を使用したリモートの例については、[このノートブック](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)を参照してください。 

* Azure サブスクリプション内の **Azure Databricks クラスター**。 詳細については、[自動 ML のための Azure Databricks クラスターの構成](how-to-configure-environment.md#azure-databricks)に関する記事を参照してください。 Azure Databricks でのノートブックの例については、こちらの [GitHub サイト](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl)をご覧ください。

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>実験の設定を構成する

自動機械学習の実験を構成するために使用できるオプションがいくつかあります。 これらのパラメーターは、`AutoMLConfig` オブジェクトをインスタンス化することによって設定します。 パラメーターの完全な一覧については、「[AutoMLConfig クラス](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)」をご覧ください。

次に例をいくつか示します。

1. プライマリ メトリックとして重み付けされた AUC を使用する分類の実験。実験のタイムアウトの分数は 30 分間、クロス検証フォールドは 2 つに設定されます。

   ```python
       automl_classifier=AutoMLConfig(
       task='classification',
       primary_metric='AUC_weighted',
       experiment_timeout_minutes=30,
       blocked_models=['XGBoostClassifier'],
       training_data=train_data,
       label_column_name=label,
       n_cross_validations=2)
   ```
1. 次の例は、60 分後に、検証交差の分割を 5 つ使って終了するよう設定された回帰実験です。

   ```python
      automl_regressor = AutoMLConfig(
      task='regression',
      experiment_timeout_minutes=60,
      allowed_models=['KNN'],
      primary_metric='r2_score',
      training_data=train_data,
      label_column_name=label,
      n_cross_validations=5)
   ```


1. 予測タスクには、追加の設定が必要です。詳細については、「[時系列予測モデルを自動トレーニングする](how-to-auto-train-forecast.md)」の記事を参照してください。 

    ```python
    time_series_settings = {
        'time_column_name': time_column_name,
        'time_series_id_column_names': time_series_id_column_names,
        'drop_column_names': ['logQuantity'],
        'forecast_horizon': n_test_periods
    }
    
    automl_config = AutoMLConfig(task = 'forecasting',
                                 debug_log='automl_oj_sales_errors.log',
                                 primary_metric='normalized_root_mean_squared_error',
                                 experiment_timeout_minutes=20,
                                 training_data=train_data,
                                 label_column_name=label,
                                 n_cross_validations=5,
                                 path=project_folder,
                                 verbosity=logging.INFO,
                                 **time_series_settings)
    ```
    
### <a name="supported-models"></a>サポートされているモデル

自動機械学習によって、自動化とチューニングのプロセス中にさまざまなモデルとアルゴリズムが試行されます。 ユーザーは、アルゴリズムを指定する必要はありません。 

3 つの異なる `task` パラメーター値 (3 つ目の task-type は `forecasting` であり、`regression` タスクと類似のアルゴリズム プールを使用します) によって、適用するアルゴリズム、モデルの一覧が決まります。 使用可能なモデルを包含または除外してさらにイテレーションを変更するには、`allowed_models` または `blocked_models` パラメーターを使用します。 サポートされているモデルの一覧については、([分類](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.classification)、[予測](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.forecasting)、[回帰](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.regression)用の) [SupportedModels クラス](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels)を参照してください。


### <a name="primary-metric"></a>主要メトリック
`primary metric` パラメーターによって、モデルのトレーニング中に最適化のために使用されるメトリックが決まります。 選択できるメトリックは、選択したタスクの種類によって決まります。次の表に、各タスクの種類に有効な主要メトリックを示します。

これらのメトリックの具体的な定義については、「[自動化機械学習の結果の概要](how-to-understand-automated-ml.md)」を参照してください。

|分類 | 回帰 | 時系列予測
|--|--|--
|accuracy| spearman_correlation | spearman_correlation
|AUC_weighted | normalized_root_mean_squared_error | normalized_root_mean_squared_error
|average_precision_score_weighted | r2_score | r2_score
|norm_macro_recall | normalized_mean_absolute_error | normalized_mean_absolute_error
|precision_score_weighted |

### <a name="data-featurization"></a>データの特徴付け

すべての自動機械学習実験では、さまざまなスケールの特徴を検知できる特定のアルゴリズムをサポートできるように、データが*自動的にスケーリングおよび正規化*されます。 このスケーリングと正規化は特徴量化と呼ばれます。 詳細とコード例については、[AutoML での特徴量化](how-to-configure-auto-features.md#)に関するページを参照してください。 

`AutoMLConfig` オブジェクトで実験を構成するときに、設定 `featurization` の有効と無効を切り替えることができます。 次の表は、[AutoMLConfig プロジェクト](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)の特徴量化で許可されている設定です。 

|特徴付けの構成 | 説明 |
| ------------- | ------------- |
|`"featurization": 'auto'`| 前処理の一環として、[データ ガードレールと特徴付けの手順](how-to-configure-auto-features.md#featurization)が自動的に実行されることを示します。 **既定の設定**です。|
|`"featurization": 'off'`| 特徴量化の手順が自動的には実行されないことを示します。|
|`"featurization":`&nbsp;`'FeaturizationConfig'`| カスタマイズされた特徴付け手順を使用する必要があることを示します。 [特徴付けをカスタマイズする方法の詳細](how-to-configure-auto-features.md#customize-featurization)。|

> [!NOTE]
> 自動化された機械学習の特徴付け手順 (機能の正規化、欠損データの処理、テキストから数値への変換など) は、基になるモデルの一部になります。 このモデルを予測に使用する場合、トレーニング中に適用されたのと同じ特徴付けの手順がご自分の入力データに自動的に適用されます。

<a name="ensemble"></a>

### <a name="ensemble-configuration"></a> アンサンブル構成

アンサンブル モデルは既定で有効になっており、AutoML の実行での最終実行イテレーションとして表示されます。 現在は **VotingEnsemble** と **StackEnsemble** がサポートされています。 

投票は、加重平均を使用したソフト投票を実装します。 スタッキング実装は 2 層の実装を使用します。ここでは、第 1 層は投票アンサンブルと同じモデルを持ち、第 2 層のモデルは、第 1 層からのモデルの最適な組み合わせを見つけるために使用されます。 

ONNX モデルを使用している場合、**または**モデルの説明を有効にしている場合、スタッキングは無効になり、投票だけが使用されます。

アンサンブル トレーニングは、`enable_voting_ensemble` および `enable_stack_ensemble` のブール型パラメーターを使用して無効にすることができます。

```python
automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        experiment_timeout_minutes=30,
        training_data=data_train,
        label_column_name=label,
        n_cross_validations=5,
        enable_voting_ensemble=False,
        enable_stack_ensemble=False
        )
```

既定のアンサンブル動作を変更するために、`AutoMLConfig` オブジェクトに `kwargs` として提供できる既定の引数が複数あります。

> [!IMPORTANT]
>  次のパラメーターは、AutoMLConfig クラスの明示的なパラメーターではありません。 

* `ensemble_download_models_timeout_sec`:**VotingEnsemble** と **StackEnsemble** モデルの生成中に、以前の子実行の複数の適合モデルがダウンロードされます。 `AutoMLEnsembleException: Could not find any models for running ensembling` のエラーが発生した場合は、モデルのダウンロードにより長い時間が必要になることがあります。 既定値は、これらのモデルを並列でダウンロードする場合は 300 秒で、タイムアウトの上限はありません。 より長い時間が必要な場合は、このパラメーターを 300 秒より大きい値に設定します。 

  > [!NOTE]
  >  タイムアウトに達し、ダウンロードしたモデルがある場合は、アンサンブルはダウンロードしたモデルと同じ数だけ処理を続行します。 そのタイムアウト以内にすべてのモデルのダウンロードを終える必要はありません。

次のパラメーターは、**StackEnsemble** モデルにのみ適用されます。 

* `stack_meta_learner_type`: メタ学習器は、個々の異種モデルの出力でトレーニングされたモデルです。 既定のメタ学習器は、分類タスクには `LogisticRegression` (またはクロス検証が有効になっている場合は `LogisticRegressionCV`) で、回帰/予測タスクには `ElasticNet` (またはクロス検証が有効になっている場合は `ElasticNetCV`) です。 このパラメーターは、`LogisticRegression`、`LogisticRegressionCV`、`LightGBMClassifier`、`ElasticNet`、`ElasticNetCV`、`LightGBMRegressor`、または `LinearRegression` のいずれかの文字列になります。

* `stack_meta_learner_train_percentage`: メタ学習器のトレーニング用に予約されるトレーニング セットの割合を指定します。(トレーニングと、トレーニングの検証の種類を選択する場合)。 既定値は `0.2` です。 

* `stack_meta_learner_kwargs`: メタ学習器の初期化子に渡す省略可能なパラメーター。 これらのパラメーターとパラメーターの型は、対応するモデル コンストラクターからのパラメーターとパラメーターの型を反映しており、モデル コンストラクターに転送されます。

次のコードは、`AutoMLConfig` オブジェクトでカスタムのアンサンブル動作を指定する例を示します。

```python
ensemble_settings = {
    "ensemble_download_models_timeout_sec": 600
    "stack_meta_learner_type": "LogisticRegressionCV",
    "stack_meta_learner_train_percentage": 0.3,
    "stack_meta_learner_kwargs": {
        "refit": True,
        "fit_intercept": False,
        "class_weight": "balanced",
        "multi_class": "auto",
        "n_jobs": -1
    }
}

automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        experiment_timeout_minutes=30,
        training_data=train_data,
        label_column_name=label,
        n_cross_validations=5,
        **ensemble_settings
        )
```

## <a name="run-experiment"></a>実験を実行する

自動化された ML の場合は `Experiment` オブジェクトを作成します。これは、実験を実行するために使用される `Workspace` 内の名前付きオブジェクトです。

```python
from azureml.core.experiment import Experiment

ws = Workspace.from_config()

# Choose a name for the experiment and specify the project folder.
experiment_name = 'automl-classification'
project_folder = './sample_projects/automl-classification'

experiment = Experiment(ws, experiment_name)
```

実験を送信して実行し、モデルを生成します。 モデルを生成するには、`AutoMLConfig` を `submit` メソッドに渡します。

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>最初に依存関係が新しいマシンにインストールされます。  出力が表示されるまで、最大で 10 分間かかる場合があります。
>`show_output` を `True` に設定すると、コンソールに出力が表示されます。

 <a name="exit"></a> 

### <a name="exit-criteria"></a>終了基準

実験を終了するために定義できるオプションがいくつかあります。

|条件| description
|----|----
基準なし | 終了パラメーターを定義しない場合、実験は、主なメトリックでそれ以上の進歩が見られなくなるまで続けられます。
一定時間が経過した後| 設定で `experiment_timeout_minutes` を使用すると、実験の実行を継続する時間を分単位で定義できます。 <br><br> 実験のタイムアウト エラーを回避するため、最小値は 15 分です (行と列を掛けたサイズが 1,000 万を超える場合は 60 分)。
スコアに達した後| `experiment_exit_score` を使用すると、指定した主要メトリックのスコアに達した後に実験が完了します。

## <a name="explore-models-and-metrics"></a>モデルとメトリックを探索する

トレーニング結果をウィジェットで、またはノートブックの場合はインラインで、表示できます。 詳細については、[モデルの追跡と評価](how-to-monitor-view-training-logs.md#monitor-automated-machine-learning-runs)に関するセクションをご覧ください。

実行のたびに提供されるパフォーマンス グラフおよびメトリックの定義と例については、「[自動化機械学習の結果の概要](how-to-understand-automated-ml.md)」を参照してください。 

特徴量化の概要を確認し、特定のモデルに追加された機能を理解するには、「[特徴量化の透過性](how-to-configure-auto-features.md#featurization-transparency)」を参照してください。 

## <a name="register-and-deploy-models"></a>モデルを登録してデプロイする

Web サービスにデプロイするためのモデルをダウンロードまたは登録する方法の詳細については、[モデルをデプロイする方法と場所](how-to-deploy-and-where.md)に関するページを参照してください。


<a name="explain"></a>

## <a name="model-interpretability"></a>モデルの解釈可能性

モデルの解釈機能を使用すると、モデルで予測が行われた理由や、基になる特徴の重要度の値を把握できます。 SDK には、トレーニングと推論時間の両方で、ローカル モデルとデプロイされたモデルについて、モデルの解釈可能性の特徴を有効にするためのさまざまなパッケージが含まれています。

特に自動化された機械学習の実験で解釈可能性の特徴を有効にする方法のコード サンプルについては、その[方法](how-to-machine-learning-interpretability-automl.md)を参照してください。

自動機械学習ではない SDK の他の領域で、モデルの説明と特徴の重要度を有効にする方法の一般情報については、解釈可能性の[概念](how-to-machine-learning-interpretability.md)に関する記事を参照してください。

> [!NOTE]
> 現在、ForecastTCN モデルは説明クライアントではサポートされていません。 このモデルが最適なモデルとして返された場合、説明ダッシュボードは返されず、オンデマンドでの説明の実行はサポートされません。

## <a name="next-steps"></a>次のステップ

+ [モデルをデプロイする方法と場所](how-to-deploy-and-where.md)についてさらに詳しく学習する。

+ [自動機械学習を使用して回帰モデルをトレーニングする方法](tutorial-auto-train-models.md)または[リモート リソースに対して自動機械学習を使用してトレーニングする方法](how-to-auto-train-remote.md)についてさらに詳しく学習する。

+ [多数モデル ソリューション アクセラレータ](https://aka.ms/many-models)で AutoML を使用して複数のモデルをトレーニングする方法について学習する。
