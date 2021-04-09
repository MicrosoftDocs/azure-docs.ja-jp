---
title: 自動 ML の実験を作成する
titleSuffix: Azure Machine Learning
description: 自動機械学習の実験のデータ ソース、計算、および構成設定を定義する方法について説明します。
author: cartacioS
ms.author: sacartac
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 09/29/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python,contperf-fy21q1, automl
ms.openlocfilehash: 24c0d57490ecd039039992310f93ca3e21c47b3b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103563489"
---
# <a name="configure-automated-ml-experiments-in-python"></a>Python で自動 ML の実験を構成する


このガイドでは、[Azure Machine Learning SDK](/python/api/overview/azure/ml/intro) を使用して、自動機械学習の実験のさまざまな構成設定を定義する方法について説明します。 自動機械学習は、アルゴリズムとハイパーパラメーターを自動的に選択して、デプロイできる状態のモデルを生成します。 自動機械学習の実験の構成に使用できるオプションはいくつかあります。

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
    * コンピューティング インスタンスを作成します。これにより、SDK が自動的にインストールされ、ML ワークフロー用に事前構成されます。 詳細については、「[Azure Machine Learning コンピューティング インスタンスの作成と管理](how-to-create-manage-compute-instance.md)」を参照してください。 

    * SDK の[既定のインストール](/python/api/overview/azure/ml/install#default-install)が含まれる、[`automl` パッケージを自分でインストール](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/README.md#setup-using-a-local-conda-environment)します。

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

機械学習でのトレーニング データの要件:
- データは表形式である必要があります。
- 予測する値、ターゲット列は、データ内にある必要があります。

**リモート実験の場合**、トレーニング データにリモート コンピューティングからアクセスできる必要があります。 AutoML により、リモート コンピューティングでの作業時に [Azure Machine Learning TabularDatasets](/python/api/azureml-core/azureml.data.tabulardataset) のみが受け入れられます。 

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

`AutoMLConfig` コンストラクターでは、個別の **トレーニング データと検証データのセット** を直接指定できます。 AutoML 実験用に[データの分割とクロス検証を構成する方法](how-to-configure-cross-validation-data-splits.md)に関するページをご確認ください。 

`validation_data` または `n_cross_validation` パラメーターを明示的に指定しない場合、自動 ML では、既定の手法を適用して検証の実行方法を決定します。 この決定は、`training_data` パラメーターに割り当てられたデータセット内の行の数によって異なります。 

|トレーニング&nbsp;データ&nbsp;のサイズ| 検証の方法 |
|---|-----|
|**20,000&nbsp;行&nbsp;より&nbsp;多い**| トレーニング/検証データの分割が適用されます。 既定では、初期トレーニング データ セットの 10% が検証セットとして取得されます。 次に、その検証セットがメトリックの計算に使用されます。
|**20,000&nbsp;行&nbsp;より&nbsp;少ない**| クロス検証アプローチが適用されます。 フォールドの既定の数は行数によって異なります。 <br> **データセットが 1,000 行より少ない場合は**、10 個のフォールドが使用されます。 <br> **行が 1,000 から 20,000 の間の場合は**、3 つのフォールドが使用されます。

現時点では、モデルの評価用に独自の **テストデータ** を提供する必要があります。 モデル評価のために独自のテスト データを取り込むコード例については、[この Jupyter ノートブック](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb)の **テスト** に関するセクションを参照してください。

## <a name="compute-to-run-experiment"></a>実験を実行するために計算する

次に、モデルをトレーニングする場所を決定します。 自動機械学習のトレーニング実験は、次のコンピューティング オプションで実行できます。 [ローカルおよびリモートのコンピューティングの長所と短所](concept-automated-ml.md#local-remote)のオプションについて説明します。 

* ローカル デスクトップやノート PC などの **ローカル** コンピューター – 一般に、データセットが小さく、まだ探索ステージにいる場合。 ローカル コンピューティングの例については、[このノートブック](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/local-run-classification-credit-card-fraud/auto-ml-classification-credit-card-fraud-local.ipynb)を参照してください。 
 
* クラウド上の **リモート** マシン – [Azure Machine Learning Managed Compute](concept-compute-target.md#amlcompute) は、Azure 仮想マシンのクラスター上で機械学習モデルをトレーニングできるようにするマネージド サービスです。 

    Azure Machine Learning Managed Compute を使用したリモートの例については、[このノートブック](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)を参照してください。 

* Azure サブスクリプション内の **Azure Databricks クラスター**。 詳細については、[自動 ML のための Azure Databricks クラスターの構成](how-to-configure-databricks-automl-environment.md)に関する記事を参照してください。 Azure Databricks でのノートブックの例については、こちらの [GitHub サイト](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl)をご覧ください。

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>実験の設定を構成する

自動機械学習の実験を構成するために使用できるオプションがいくつかあります。 これらのパラメーターは、`AutoMLConfig` オブジェクトをインスタンス化することによって設定します。 パラメーターの完全な一覧については、「[AutoMLConfig クラス](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)」をご覧ください。

次に例をいくつか示します。

1. プライマリ メトリックとして重み付けされた AUC を使用する分類の実験。実験のタイムアウトの分数は 30 分間、クロス検証フォールドは 2 つに設定されます。

   ```python
       automl_classifier=AutoMLConfig(task='classification',
                                      primary_metric='AUC_weighted',
                                      experiment_timeout_minutes=30,
                                      blocked_models=['XGBoostClassifier'],
                                      training_data=train_data,
                                      label_column_name=label,
                                      n_cross_validations=2)
   ```
1. 次の例は、60 分後に、検証交差の分割を 5 つ使って終了するよう設定された回帰実験です。

   ```python
      automl_regressor = AutoMLConfig(task='regression',
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

3 つの異なる `task` パラメーター値によって、適用するアルゴリズムまたはモデルの一覧が決定されます。 使用可能なモデルを包含または除外してさらにイテレーションを変更するには、`allowed_models` または `blocked_models` パラメーターを使用します。 

次の表は、サポートされるモデルをタスクの種類別にまとめたものです。 

> [!NOTE]
> 自動 ML で作成されたモデルを [ONNX モデル](concept-onnx.md)にエクスポートする予定がある場合は、* で示されたアルゴリズムのみを ONNX 形式に変換できます。 [モデルの ONNX への変換](concept-automated-ml.md#use-with-onnx)の詳細についてご確認ください。 <br> <br> また、ONNX では現在、分類と回帰のタスクのみがサポートされていることにもご注意ください。 

分類 | 回帰 | 時系列予測
|-- |-- |--
[ロジスティック回帰](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)* | [Elastic Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)* | [Elastic Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)* |[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)*|[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[勾配ブースティング](https://scikit-learn.org/stable/modules/ensemble.html#classification)* |[勾配ブースティング](https://scikit-learn.org/stable/modules/ensemble.html#regression)* |[勾配ブースティング](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[デシジョン ツリー](https://scikit-learn.org/stable/modules/tree.html#decision-trees)* |[デシジョン ツリー](https://scikit-learn.org/stable/modules/tree.html#regression)* |[デシジョン ツリー](https://scikit-learn.org/stable/modules/tree.html#regression)
[K ニアレスト ネイバー](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)* |[K ニアレスト ネイバー](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)* |[K ニアレスト ネイバー](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[Linear SVC](https://scikit-learn.org/stable/modules/svm.html#classification)* |[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)* |[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[サポート ベクター分類 (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)* |[確率的勾配降下法 (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)* |[確率的勾配降下法 (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[ランダム フォレスト](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)* |[ランダム フォレスト](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)* |[ランダム フォレスト](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Extremely Randomized Trees](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)* |[Extremely Randomized Trees](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)* |[Extremely Randomized Trees](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)* |[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)* | [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[平均化パーセプトロン分類子](/python/api/nimbusml/nimbusml.linear_model.averagedperceptronbinaryclassifier?preserve-view=true&view=nimbusml-py-latest)|[オンライン勾配降下リグレッサー](/python/api/nimbusml/nimbusml.linear_model.onlinegradientdescentregressor?preserve-view=true&view=nimbusml-py-latest) |[自動 ARIMA](https://www.alkaline-ml.com/pmdarima/modules/generated/pmdarima.arima.auto_arima.html#pmdarima.arima.auto_arima)
[単純ベイズ](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)* |[高速線形リグレッサー](/python/api/nimbusml/nimbusml.linear_model.fastlinearregressor?preserve-view=true&view=nimbusml-py-latest)|[Prophet](https://facebook.github.io/prophet/docs/quick_start.html)
[確率的勾配降下法 (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)* ||ForecastTCN
|[Linear SVM Classifier](/python/api/nimbusml/nimbusml.linear_model.linearsvmbinaryclassifier?preserve-view=true&view=nimbusml-py-latest)*||

### <a name="primary-metric"></a>主要メトリック
`primary metric` パラメーターによって、モデルのトレーニング中に最適化のために使用されるメトリックが決まります。 選択できるメトリックは、選択したタスクの種類によって決まります。次の表に、各タスクの種類に有効な主要メトリックを示します。

自動機械学習を最適化するための主要メトリックの選択は、多くの要因に基づきます。 最優先の考慮事項としてお勧めするのは、ビジネス ニーズを最も適切に表すメトリックを選択することです。 次に、メトリックがデータセット プロファイル (データのサイズ、範囲、クラスの分布など) に適しているかどうかを考慮します。

これらのメトリックの具体的な定義については、「[自動化機械学習の結果の概要](how-to-understand-automated-ml.md)」を参照してください。

|分類 | 回帰 | 時系列予測
|--|--|--
|`accuracy`| `spearman_correlation` | `spearman_correlation`
|`AUC_weighted` | `normalized_root_mean_squared_error` | `normalized_root_mean_squared_error`
|`average_precision_score_weighted` | `r2_score` | `r2_score`
|`norm_macro_recall` | `normalized_mean_absolute_error` | `normalized_mean_absolute_error`
|`precision_score_weighted` |

### <a name="primary-metrics-for-classification-scenarios"></a>分類シナリオの主要メトリック 

`accuracy`、`average_precision_score_weighted`、`norm_macro_recall`、`precision_score_weighted` などのしきい値化された後のメトリックでは、非常に小さいデータセット、非常に大きいクラス傾斜 (クラスの不均衡) があるデータセットに対して、または予期されるメトリック値が 0.0 または 1.0 に非常に近い場合に、適切に最適化されない可能性があります。 このような場合、主要メトリックには `AUC_weighted` が適しています。 自動機械学習の完了後、ビジネス ニーズに最も適したメトリックに基づいて、優先されるモデルを選択できます。

| メトリック | ユース ケースの例 |
| ------ | ------- |
| `accuracy` | 画像分類、感情分析、チャーン予測 |
| `AUC_weighted` | 不正行為の検出、画像分類、異常検出/スパム検出 |
| `average_precision_score_weighted` | センチメント分析 |
| `norm_macro_recall` | チャーン予測 |
| `precision_score_weighted` |  |

### <a name="primary-metrics-for-regression-scenarios"></a>回帰シナリオの主要メトリック

`r2_score` や `spearman_correlation` などのメトリックは、予測する値の規模が多くの桁数に及ぶ場合に、モデルの品質をより適切に表すことができます。 たとえば、給与の推定では、多くの人の給与は 20,000 ドルから 100,000 ドルですが、一部の給与は 100,000,000 ドルの範囲になり、非常に規模が大きくなります。 

この場合、`normalized_mean_absolute_error` と `normalized_root_mean_squared_error` では、給与が 30,000 ドルの人と、給与が 20,000,000 ドルの人に対して、同じように 20,000 ドルの予測誤差が処理されます。 実際には、20000 ドルと隔たった 20000000 ドルの給与のみ予測することは非常に近く (相対差異は 0.1% で小さい)、一方 20000 ドルと隔たった 30000 ドルを予測することは近くありません (相対差異は 67% で大きい)。 `normalized_mean_absolute_error` と `normalized_root_mean_squared_error` は、予測する値が同じ規模になる場合に便利です。

| メトリック | ユース ケースの例 |
| ------ | ------- |
| `spearman_correlation` | |
| `normalized_root_mean_squared_error` | 価格の予測 (家/製品/チップ)、レビュー スコアの予測 |
| `r2_score` | 飛行機の遅延、給与の見積もり、バグの解決時間 |
| `normalized_mean_absolute_error` |  |

### <a name="primary-metrics-for-time-series-forecasting-scenarios"></a>時系列予測シナリオの主要メトリック

上記の回帰の説明を参照してください。

| メトリック | ユース ケースの例 |
| ------ | ------- |
| `spearman_correlation` | |
| `normalized_root_mean_squared_error` | 価格の予測 (予想)、在庫の最適化、需要の予測 |
| `r2_score` | 価格の予測 (予想)、在庫の最適化、需要の予測 |
| `normalized_mean_absolute_error` | |

### <a name="data-featurization"></a>データの特徴付け

すべての自動機械学習実験では、さまざまなスケールの特徴を検知できる特定のアルゴリズムをサポートできるように、データが *自動的にスケーリングおよび正規化* されます。 このスケーリングと正規化は特徴量化と呼ばれます。 詳細とコード例については、[AutoML での特徴量化](how-to-configure-auto-features.md#)に関するページを参照してください。 

`AutoMLConfig` オブジェクトで実験を構成するときに、設定 `featurization` の有効と無効を切り替えることができます。 次の表は、[AutoMLConfig プロジェクト](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)の特徴量化で許可されている設定です。 

|特徴付けの構成 | 説明 |
| ------------- | ------------- |
|`"featurization": 'auto'`| 前処理の一環として、[データ ガードレールと特徴付けの手順](how-to-configure-auto-features.md#featurization)が自動的に実行されることを示します。 **既定の設定** です。|
|`"featurization": 'off'`| 特徴量化の手順が自動的には実行されないことを示します。|
|`"featurization":`&nbsp;`'FeaturizationConfig'`| カスタマイズされた特徴付け手順を使用する必要があることを示します。 [特徴付けをカスタマイズする方法の詳細](how-to-configure-auto-features.md#customize-featurization)。|

> [!NOTE]
> 自動化された機械学習の特徴付け手順 (機能の正規化、欠損データの処理、テキストから数値への変換など) は、基になるモデルの一部になります。 このモデルを予測に使用する場合、トレーニング中に適用されたのと同じ特徴付けの手順がご自分の入力データに自動的に適用されます。

<a name="ensemble"></a>

### <a name="ensemble-configuration"></a> アンサンブル構成

アンサンブル モデルは既定で有効になっており、AutoML の実行での最終実行イテレーションとして表示されます。 現在は **VotingEnsemble** と **StackEnsemble** がサポートされています。 

投票では、加重平均を使用したソフト投票を実装します。 スタッキング実装は 2 層の実装を使用します。ここでは、第 1 層は投票アンサンブルと同じモデルを持ち、第 2 層のモデルは、第 1 層からのモデルの最適な組み合わせを見つけるために使用されます。 

ONNX モデルを使用している場合、**または** モデルの説明を有効にしている場合、スタッキングは無効になり、投票だけが使用されます。

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

<a name="exit"></a> 

### <a name="exit-criteria"></a>終了基準

実験を終了するために AutoMLConfig で定義できるオプションはいくつかあります。

|条件| description
|----|----
基準なし | 終了パラメーターを定義しない場合、実験は、主なメトリックでそれ以上の進歩が見られなくなるまで続けられます。
一定時間が経過した後| 設定で `experiment_timeout_minutes` を使用すると、実験の実行を継続する時間を分単位で定義できます。 <br><br> 実験のタイムアウト エラーを回避するため、最小値は 15 分です (行と列を掛けたサイズが 1,000 万を超える場合は 60 分)。
スコアに達した後| `experiment_exit_score` を使用すると、指定した主要メトリックのスコアに達した後に実験が完了します。

## <a name="run-experiment"></a>実験を実行する

自動化された ML の場合は `Experiment` オブジェクトを作成します。これは、実験を実行するために使用される `Workspace` 内の名前付きオブジェクトです。

```python
from azureml.core.experiment import Experiment

ws = Workspace.from_config()

# Choose a name for the experiment and specify the project folder.
experiment_name = 'Tutorial-automl'
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

### <a name="multiple-child-runs-on-clusters"></a>クラスターでの複数回の子実行

自動 ML での子実行の実験は、既に別の実験が実行されているクラスターで実行することができます。 ただし、タイミングは、クラスターに含まれるノード数によって、および別の実験を実行するためにそれらのノードを使用できるかどうかによって異なります。

クラスター内の各ノードは、1 回のトレーニング実行を完了できる個別の仮想マシン (VM) として機能します。自動 ML の場合、これは子実行を意味します。 すべてのノードがビジー状態の場合は、新しい実験がキューに登録されます。 ただし、空きノードがある場合は、使用可能なノードと VM で、新しい実験によって自動 ML 子実行が並行して実行されます。

子実行とそれが実行されるタイミングを管理するには、実験ごとに専用のクラスターを作成し、実験の `max_concurrent_iterations` 数をクラスター内のノード数と一致させることをお勧めします。 このようにして、必要な同時子実行とイテレーションの数で、クラスターのノードをすべて同時に使用します。

`AutoMLConfig` オブジェクトで `max_concurrent_iterations` を構成します。 構成されていない場合は、1 回の実験につき 1 回の同時子実行とイテレーションのみが既定で許可されます。  

## <a name="explore-models-and-metrics"></a>モデルとメトリックを探索する

トレーニング結果をウィジェットで、またはノートブックの場合はインラインで、表示できます。 詳細については、[モデルの追跡と評価](how-to-monitor-view-training-logs.md#monitor-automated-machine-learning-runs)に関するセクションをご覧ください。

実行のたびに提供されるパフォーマンス グラフおよびメトリックの定義と例については、「[自動化機械学習の結果を評価する](how-to-understand-automated-ml.md)」を参照してください。 

特徴量化の概要を確認し、特定のモデルに追加された機能を理解するには、「[特徴量化の透過性](how-to-configure-auto-features.md#featurization-transparency)」を参照してください。 

> [!NOTE]
> 自動 ML のアルゴリズムには特有のランダム性があり、推奨モデルの最終的なメトリック スコア (精度など) にわずかな変動が生じる可能性があります。 自動 ML によって、トレーニングとテストの分割、トレーニングと検証の分割、クロス検証などのデータに対する操作も必要に応じて実行されます。 そのため、同じ構成設定とプライマリ メトリックを使用して実験を複数回実行した場合、これらの要因により、各実験の最終的なメトリック スコアに変動が見られる可能性があります。 

## <a name="register-and-deploy-models"></a>モデルを登録してデプロイする
モデルは登録できます。そのため、後で使用するためにモデルに戻ることができます。 

自動化された ML 実行からモデルを登録するには、[`register_model()`](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun#register-model-model-name-none--description-none--tags-none--iteration-none--metric-none-) メソッドを使用します。 

```Python

best_run, fitted_model = run.get_output()
print(fitted_model.steps)

model_name = best_run.properties['model_name']
description = 'AutoML forecast example'
tags = None

model = remote_run.register_model(model_name = model_name, 
                                  description = description, 
                                  tags = tags)
```


デプロイ構成を作成し、登録済みのモデルを Web サービスにデプロイする方法の詳細については、[モデルをデプロイする方法と場所](how-to-deploy-and-where.md?tabs=python#define-a-deployment-configuration)に関するページを参照してください。

> [!TIP]
> 登録済みのモデルの場合、[Azure Machine Learning スタジオ](https://ml.azure.com)を使用してワンクリックでデプロイできます。 [登録済みのモデルをスタジオからデプロイする](how-to-use-automated-ml-for-ml-models.md#deploy-your-model)方法に関するページを参照してください。 
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

+ [自動 ML 実験のトラブルシューティング](how-to-troubleshoot-auto-ml.md)。 
