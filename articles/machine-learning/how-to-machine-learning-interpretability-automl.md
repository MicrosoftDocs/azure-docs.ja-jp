---
title: 自動 ML での説明可能性 (プレビュー)
titleSuffix: Azure Machine Learning
description: Azure Machine Learning SDK を使用している場合に、自動 ML モデルがどのように特徴量の重要度を判定して予測を行うかに関する説明を取得する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, automl, responsible-ml
ms.author: mithigpe
author: minthigpen
ms.date: 07/09/2020
ms.openlocfilehash: 709c85bed4a028c6c168c79cd9fffd6b7b40cb68
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100008045"
---
# <a name="interpretability-model-explanations-in-automated-machine-learning-preview"></a>解釈可能性: 自動機械学習のモデルの説明 (プレビュー)



この記事では、Azure Machine Learning で自動機械学習 (AutoML) の説明を取得する方法について説明します。 AutoML は、生成されるモデルの特徴量の重要度を理解するのに役立ちます。 

1\.0.85 より後のすべての SDK バージョンは、既定で `model_explainability=True` となります。 SDK バージョン 1.0.85 以前のバージョンでは、モデルの解釈可能性を使用するために、ユーザーは `AutoMLConfig` オブジェクトに `model_explainability=True` を設定する必要があります。 

この記事では、次のことについて説明します。

- 最良のモデルまたは任意のモデルのトレーニング中の解釈可能性を実行する。
- データや説明のパターンを確認するのに役立つ視覚化を有効にする。
- 推論中またはスコアリング中の解釈可能性を実装する。

## <a name="prerequisites"></a>前提条件

- 解釈可能性の機能。 `pip install azureml-interpret` を実行して、必要なパッケージを取得します。
- AutoML の実験の作成に関する知識。 Azure Machine Learning SDK の使用方法について詳しくは、こちらの[回帰モデルのチュートリアル](tutorial-auto-train-models.md)を完了するか、[AutoML の実験を構成する](how-to-configure-auto-train.md)方法を参照してください。

## <a name="interpretability-during-training-for-the-best-model"></a>最良のモデルのトレーニング中の解釈可能性

`best_run` から説明を取得します。これには、生の特徴とエンジニアリングされた特徴の両方の説明が含まれます。

> [!NOTE]
> 解釈可能性、最適なモデルの説明は、以下のアルゴリズムを最適なモデルとして推奨する Auto ML 予測実験では利用できません。 
> * TCNForecaster
> * AutoArima
> * ExponentialSmoothing
> * Prophet
> * 平均 
> * Naive
> * Seasonal Average 
> * Seasonal Naive

### <a name="download-the-engineered-feature-importances-from-the-best-run"></a>最適な実行からエンジニアリングされた特徴量の重要度をダウンロードする

`ExplanationClient` を使用して、`best_run` の成果物ストアからエンジニアリングされた特徴の説明をダウンロードできます。 

```python
from azureml.interpret import ExplanationClient

client = ExplanationClient.from_run(best_run)
engineered_explanations = client.download_model_explanation(raw=False)
print(engineered_explanations.get_feature_importance_dict())
```

### <a name="download-the-raw-feature-importances-from-the-best-run"></a>最適な実行から生の特徴量の重要度をダウンロードする

`ExplanationClient` を使用して、`best_run` の成果物ストアから生の特徴の説明をダウンロードできます。

```python
from azureml.interpret import ExplanationClient

client = ExplanationClient.from_run(best_run)
raw_explanations = client.download_model_explanation(raw=True)
print(raw_explanations.get_feature_importance_dict())
```

## <a name="interpretability-during-training-for-any-model"></a>任意のモデルのトレーニング中の解釈可能性 

モデルの説明を計算して視覚化する場合、AutoML モデルの既存のモデルの説明に限定されることはありません。 また、さまざまなテスト データを使用して、モデルの説明を取得することもできます。 このセクションの手順では、テスト データに基づいて、エンジニアリングされた特徴量の重要度を計算し、視覚化する方法を示します。

### <a name="retrieve-any-other-automl-model-from-training"></a>トレーニングから他の AutoML モデルを取得する

```python
automl_run, fitted_model = local_run.get_output(metric='accuracy')
```

### <a name="set-up-the-model-explanations"></a>モデル説明を設定する

エンジニアリングされた説明と生の説明を取得するには、`automl_setup_model_explanations` を使います。 `fitted_model` では、次の項目を生成できます。

- トレーニングされたサンプルまたはテスト サンプルからの特徴付けされたデータ
- エンジニアリングされた特徴の名前リスト
- 分類シナリオでのラベル付けされた列の検索可能なクラス

`automl_explainer_setup_obj` には、上記の一覧にあるすべての構造が含まれます。

```python
from azureml.train.automl.runtime.automl_explain_utilities import automl_setup_model_explanations

automl_explainer_setup_obj = automl_setup_model_explanations(fitted_model, X=X_train, 
                                                             X_test=X_test, y=y_train, 
                                                             task='classification')
```

### <a name="initialize-the-mimic-explainer-for-feature-importance"></a>特徴量の重要度のための Mimic Explainer を初期化する

AutoML モデルの説明を生成するには、`MimicWrapper` クラスを使います。 次のパラメーターを使用して MimicWrapper を初期化できます。

- 説明セットアップ オブジェクト
- ワークスペース
- `fitted_model` AutoML モデルを説明する代理モデル

また、MimicWrapper は、エンジニアリングされた説明のアップロード先となる `automl_run` オブジェクトを受け取ります。

```python
from azureml.interpret import MimicWrapper

# Initialize the Mimic Explainer
explainer = MimicWrapper(ws, automl_explainer_setup_obj.automl_estimator,
                         explainable_model=automl_explainer_setup_obj.surrogate_model, 
                         init_dataset=automl_explainer_setup_obj.X_transform, run=automl_run,
                         features=automl_explainer_setup_obj.engineered_feature_names, 
                         feature_maps=[automl_explainer_setup_obj.feature_map],
                         classes=automl_explainer_setup_obj.classes,
                         explainer_kwargs=automl_explainer_setup_obj.surrogate_model_params)
```

### <a name="use-mimic-explainer-for-computing-and-visualizing-engineered-feature-importance"></a>Mimic Explainer を使用してエンジニアリングされた特徴量の重要度を計算および視覚化する

変換されたテスト サンプルを使用して、MimicWrapper の `explain()` メソッドを呼び出し、生成済みのエンジニアリングされた特徴の特徴量の重要度を取得できます。 [Azure Machine Learning スタジオ](https://ml.azure.com/)にサインインして、AutoML フィーチャライザーによって生成された、エンジニアリングされた特徴の特徴量の重要度の値をダッシュボードに視覚化して表示することもできます。

```python
engineered_explanations = explainer.explain(['local', 'global'], eval_dataset=automl_explainer_setup_obj.X_test_transform)
print(engineered_explanations.get_feature_importance_dict())
```

### <a name="use-mimic-explainer-for-computing-and-visualizing-raw-feature-importance"></a>Mimic Explainer を使用して生の特徴量の重要度を計算および視覚化する

変換されたテスト サンプルを使用して、MimicWrapper の `explain()` メソッドを呼び出し、生の特徴の特徴量の重要度を取得できます。 [Machine Learning スタジオ](https://ml.azure.com/)では、生の特徴の特徴量の重要度の値をダッシュボードに視覚化して表示することもできます。

```python
raw_explanations = explainer.explain(['local', 'global'], get_raw=True,
                                     raw_feature_names=automl_explainer_setup_obj.raw_feature_names,
                                     eval_dataset=automl_explainer_setup_obj.X_test_transform,
                                     raw_eval_dataset=automl_explainer_setup_obj.X_test_raw)
print(raw_explanations.get_feature_importance_dict())
```

## <a name="interpretability-during-inference"></a>推論中の解釈可能性

このセクションでは、前のセクションで説明の計算に使用された Explainer を使用して、AutoML モデルを運用化する方法について説明します。

### <a name="register-the-model-and-the-scoring-explainer"></a>モデルとスコアリング Explainer を登録する

推論時にエンジニアリングされた特徴量の重要度の値が計算されるスコアリング Explainer を作成するには、`TreeScoringExplainer` を使います。 以前に計算された `feature_map` を使用して、スコアリング Explainer を初期化します。 

スコアリング Explainer を保存し、モデルとスコアリング Explainer をモデル管理サービスに登録します。 次のコードを実行します。

```python
from azureml.interpret.scoring.scoring_explainer import TreeScoringExplainer, save

# Initialize the ScoringExplainer
scoring_explainer = TreeScoringExplainer(explainer.explainer, feature_maps=[automl_explainer_setup_obj.feature_map])

# Pickle scoring explainer locally
save(scoring_explainer, exist_ok=True)

# Register trained automl model present in the 'outputs' folder in the artifacts
original_model = automl_run.register_model(model_name='automl_model', 
                                           model_path='outputs/model.pkl')

# Register scoring explainer
automl_run.upload_file('scoring_explainer.pkl', 'scoring_explainer.pkl')
scoring_explainer_model = automl_run.register_model(model_name='scoring_explainer', model_path='scoring_explainer.pkl')
```

### <a name="create-the-conda-dependencies-for-setting-up-the-service"></a>サービスを設定するための conda の依存関係を作成する

次に、デプロイされたモデルのコンテナーに、必要な環境の依存関係を作成します。 pip の依存関係として、バージョン 1.0.45 以上の azureml-defaults を指定する必要があります。これは、モデルを Web サービスとしてホストするために必要な機能が含まれているためです。

```python
from azureml.core.conda_dependencies import CondaDependencies

azureml_pip_packages = [
    'azureml-interpret', 'azureml-train-automl', 'azureml-defaults'
]

myenv = CondaDependencies.create(conda_packages=['scikit-learn', 'pandas', 'numpy', 'py-xgboost<=0.80'],
                                 pip_packages=azureml_pip_packages,
                                 pin_sdk_version=True)

with open("myenv.yml","w") as f:
    f.write(myenv.serialize_to_string())

with open("myenv.yml","r") as f:
    print(f.read())

```

### <a name="create-the-scoring-script"></a>スコアリング スクリプトを作成する

モデルを読み込んで新しいデータのバッチに基づいて予測と説明を生成するスクリプトを作成します。

```python
%%writefile score.py
import joblib
import pandas as pd
from azureml.core.model import Model
from azureml.train.automl.runtime.automl_explain_utilities import automl_setup_model_explanations


def init():
    global automl_model
    global scoring_explainer

    # Retrieve the path to the model file using the model name
    # Assume original model is named automl_model
    automl_model_path = Model.get_model_path('automl_model')
    scoring_explainer_path = Model.get_model_path('scoring_explainer')

    automl_model = joblib.load(automl_model_path)
    scoring_explainer = joblib.load(scoring_explainer_path)


def run(raw_data):
    data = pd.read_json(raw_data, orient='records')
    # Make prediction
    predictions = automl_model.predict(data)
    # Setup for inferencing explanations
    automl_explainer_setup_obj = automl_setup_model_explanations(automl_model,
                                                                 X_test=data, task='classification')
    # Retrieve model explanations for engineered explanations
    engineered_local_importance_values = scoring_explainer.explain(automl_explainer_setup_obj.X_test_transform)
    # Retrieve model explanations for raw explanations
    raw_local_importance_values = scoring_explainer.explain(automl_explainer_setup_obj.X_test_transform, get_raw=True)
    # You can return any data type as long as it is JSON-serializable
    return {'predictions': predictions.tolist(),
            'engineered_local_importance_values': engineered_local_importance_values,
            'raw_local_importance_values': raw_local_importance_values}
```

### <a name="deploy-the-service"></a>サービスをデプロイする

前の手順の conda ファイルとスコアリング ファイルを使用してサービスをデプロイします。

```python
from azureml.core.webservice import Webservice
from azureml.core.webservice import AciWebservice
from azureml.core.model import Model, InferenceConfig
from azureml.core.environment import Environment

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                               memory_gb=1,
                                               tags={"data": "Bank Marketing",  
                                                     "method" : "local_explanation"},
                                               description='Get local explanations for Bank marketing test data')
myenv = Environment.from_conda_specification(name="myenv", file_path="myenv.yml")
inference_config = InferenceConfig(entry_script="score_local_explain.py", environment=myenv)

# Use configs and models generated above
service = Model.deploy(ws,
                       'model-scoring',
                       [scoring_explainer_model, original_model],
                       inference_config,
                       aciconfig)
service.wait_for_deployment(show_output=True)
```

### <a name="inference-with-test-data"></a>テスト データでの推論

一部のテスト データを使用した推論によって、AutoML モデルの予測値を確認できます。これは、現時点では、Azure Machine Learning SDK でのみサポートされています。 予測値に寄与している特徴量の重要度を表示します。 

```python
if service.state == 'Healthy':
    # Serialize the first row of the test data into json
    X_test_json = X_test[:1].to_json(orient='records')
    print(X_test_json)
    # Call the service to get the predictions and the engineered explanations
    output = service.run(X_test_json)
    # Print the predicted value
    print(output['predictions'])
    # Print the engineered feature importances for the predicted value
    print(output['engineered_local_importance_values'])
    # Print the raw feature importances for the predicted value
    print('raw_local_importance_values:\n{}\n'.format(output['raw_local_importance_values']))
```

### <a name="visualize-to-discover-patterns-in-data-and-explanations-at-training-time"></a>トレーニング時にデータのパターンと説明を発見するために視覚化する

[Machine Learning スタジオ](https://ml.azure.com)のワークスペースで、特徴量の重要度のグラフを視覚化できます。 AutoML の実行が完了した後、 **[モデルの詳細の表示]** を選択して、特定の実行を表示します。 **[Explanations]\(説明\)** タブを選択して、説明の視覚化ダッシュボードを表示します。

[![機械学習解釈可能性のアーキテクチャ](./media/how-to-machine-learning-interpretability-automl/automl-explanation.png)](./media/how-to-machine-learning-interpretability-automl/automl-explanation.png#lightbox)

説明ダッシュボードの視覚化と特定のプロットの詳細については、[解釈可能性に関するハウツー ドキュメント](how-to-machine-learning-interpretability-aml.md)を参照してください。

## <a name="next-steps"></a>次のステップ

Azure Machine Learning SDK の自動機械学習以外の領域で、モデルの説明と特徴量の重要度を有効にする方法について詳しくは、[解釈可能性の概念](how-to-machine-learning-interpretability.md)に関する記事をご覧ください。
