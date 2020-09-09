---
title: Python で ML モデルを解釈して説明する (プレビュー)
titleSuffix: Azure Machine Learning
description: Azure Machine Learning SDK を使用している場合に、機械学習モデルがどのように特徴量の重要度を判定して予測を行うかに関する説明を取得する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: mithigpe
author: minthigpen
ms.reviewer: Luis.Quintanilla
ms.date: 07/09/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 8682342d23c37d527528de0b525dbdd49a52676e
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87853400"
---
# <a name="use-the-interpretability-package-to-explain-ml-models--predictions-in-python-preview"></a>解釈可能性パッケージを使用して、Python ML モデルと予測について説明する (プレビュー)

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

この攻略ガイドでは、Azure Machine Learning Python SDK の解釈可能性パッケージを使用して次のタスクを実行する方法について学習します。


* 個人用コンピューターでローカルに実行されるモデルの動作全体または個々の予測について説明します。

* エンジニアリング済みの特徴量のための解釈可能性テクニックを有効にします。

* Azure でのモデル全体と個々の予測の動作について説明します。

* 視覚化ダッシュボードを使用して、モデルの説明と対話します。

* モデルと共にスコアリング Explainer をデプロイして、推論中の説明を観察します。



サポートされている解釈可能性テクニックと機械学習モデルの詳細については、「[Azure Machine Learning でのモデルの解釈可能性](how-to-machine-learning-interpretability.md)」と「[サンプル ノートブック](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)」を参照してください。

## <a name="generate-feature-importance-value-on-your-personal-machine"></a>個人用コンピューターで特徴量の重要度の値を生成する 
次の例は、Azure サービスに接続することなく、個人用コンピューターで解釈可能性パッケージを使用する方法を示しています。

1. `azureml-interpret` パッケージと `azureml-contrib-interpret` パッケージをインストールします。
    ```bash
    pip install azureml-interpret
    pip install azureml-contrib-interpret
    ```

2. ローカルの Jupyter ノートブックでサンプル モデルをトレーニングします。

    ```python
    # load breast cancer dataset, a well-known small dataset that comes with scikit-learn
    from sklearn.datasets import load_breast_cancer
    from sklearn import svm
    from sklearn.model_selection import train_test_split
    breast_cancer_data = load_breast_cancer()
    classes = breast_cancer_data.target_names.tolist()
    
    # split data into train and test
    from sklearn.model_selection import train_test_split
    x_train, x_test, y_train, y_test = train_test_split(breast_cancer_data.data,            
                                                        breast_cancer_data.target,  
                                                        test_size=0.2,
                                                        random_state=0)
    clf = svm.SVC(gamma=0.001, C=100., probability=True)
    model = clf.fit(x_train, y_train)
    ```

3. ローカルで Explainer を呼び出します。
   * Explainer オブジェクトを初期化するには、モデルと一部のトレーニング データを Explainer のコンストラクターに渡します。
   * 説明や視覚化の情報量を増やすために、分類を行っている場合は、機能名と出力クラス名を渡すことを選択できます。

   次のコード ブロックは、ローカルで `TabularExplainer`、`MimicExplainer`、および `PFIExplainer` を使用して Explainer オブジェクトをインスタンス化する方法を示しています。
   * `TabularExplainer` は、下で 3 つの SHAP Explainer のいずれか (`TreeExplainer`、`DeepExplainer`、または `KernelExplainer`) を呼び出します。
   * `TabularExplainer` は、ユース ケースに最適なものを 1 つ自動的に選択しますが、基になる 3 つの Explainer のそれぞれを直接呼び出すことができます。

    ```python
    from interpret.ext.blackbox import TabularExplainer

    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, 
                                 x_train, 
                                 features=breast_cancer_data.feature_names, 
                                 classes=classes)
    ```

    or

    ```python

    from interpret.ext.blackbox import MimicExplainer
    
    # you can use one of the following four interpretable models as a global surrogate to the black box model
    
    from interpret.ext.glassbox import LGBMExplainableModel
    from interpret.ext.glassbox import LinearExplainableModel
    from interpret.ext.glassbox import SGDExplainableModel
    from interpret.ext.glassbox import DecisionTreeExplainableModel

    # "features" and "classes" fields are optional
    # augment_data is optional and if true, oversamples the initialization examples to improve surrogate model accuracy to fit original model.  Useful for high-dimensional data where the number of rows is less than the number of columns.
    # max_num_of_augmentations is optional and defines max number of times we can increase the input data size.
    # LGBMExplainableModel can be replaced with LinearExplainableModel, SGDExplainableModel, or DecisionTreeExplainableModel
    explainer = MimicExplainer(model, 
                               x_train, 
                               LGBMExplainableModel, 
                               augment_data=True, 
                               max_num_of_augmentations=10, 
                               features=breast_cancer_data.feature_names, 
                               classes=classes)
    ```

    or

    ```python
    from interpret.ext.blackbox import PFIExplainer

    # "features" and "classes" fields are optional
    explainer = PFIExplainer(model,
                             features=breast_cancer_data.feature_names, 
                             classes=classes)
    ```

### <a name="explain-the-entire-model-behavior-global-explanation"></a>モデルの動作全体について説明する (グローバル説明) 

集約された (グローバルな) 特徴量の重要度の値を取得するには、次の例を参照してください。

```python

# you can use the training data or the test data here, but test data would allow you to use Explanation Exploration
global_explanation = explainer.explain_global(x_test)

# if you used the PFIExplainer in the previous step, use the next line of code instead
# global_explanation = explainer.explain_global(x_train, true_labels=y_train)

# sorted feature importance values and feature names
sorted_global_importance_values = global_explanation.get_ranked_global_values()
sorted_global_importance_names = global_explanation.get_ranked_global_names()
dict(zip(sorted_global_importance_names, sorted_global_importance_values))

# alternatively, you can print out a dictionary that holds the top K feature names and values
global_explanation.get_feature_importance_dict()
```

### <a name="explain-an-individual-prediction-local-explanation"></a>個々の予測について説明する (ローカル説明)
個々のインスタンスまたは一群のインスタンスの説明を呼び出すことによって、異なるデータポイントの個々の特徴量の重要度の値を取得します。
> [!NOTE]
> `PFIExplainer` はローカル説明をサポートしていません。

```python
# get explanation for the first data point in the test set
local_explanation = explainer.explain_local(x_test[0:5])

# sorted feature importance values and feature names
sorted_local_importance_names = local_explanation.get_ranked_local_names()
sorted_local_importance_values = local_explanation.get_ranked_local_values()
```

### <a name="raw-feature-transformations"></a>未加工の特徴の変換

エンジニアリングされた特徴ではなく、生の、変換されていない特徴の点から見た説明を取得することを選択できます。 このオプションでは、`train_explain.py` で Explainer に特徴変換パイプラインを渡します。 それ以外の場合、Explainer は、エンジニアリングされた特徴の点から見た説明を提供します。

サポートされている変換の形式は、[sklearn-pandas](https://github.com/scikit-learn-contrib/sklearn-pandas) で説明されているものと同じです。 一般には、一対多であることが明確になるように 1 つの列で動作する限り、すべての変換がサポートされます。

生の特徴の説明は `sklearn.compose.ColumnTransformer` を使用して、または適合するトランスフォーマー タプルの一覧を使用して取得します。 次の例では、`sklearn.compose.ColumnTransformer` を使用しています。

```python
from sklearn.compose import ColumnTransformer

numeric_transformer = Pipeline(steps=[
    ('imputer', SimpleImputer(strategy='median')),
    ('scaler', StandardScaler())])

categorical_transformer = Pipeline(steps=[
    ('imputer', SimpleImputer(strategy='constant', fill_value='missing')),
    ('onehot', OneHotEncoder(handle_unknown='ignore'))])

preprocessor = ColumnTransformer(
    transformers=[
        ('num', numeric_transformer, numeric_features),
        ('cat', categorical_transformer, categorical_features)])

# append classifier to preprocessing pipeline.
# now we have a full prediction pipeline.
clf = Pipeline(steps=[('preprocessor', preprocessor),
                      ('classifier', LogisticRegression(solver='lbfgs'))])


# clf.steps[-1][1] returns the trained classification model
# pass transformation as an input to create the explanation object
# "features" and "classes" fields are optional
tabular_explainer = TabularExplainer(clf.steps[-1][1],
                                     initialization_examples=x_train,
                                     features=dataset_feature_names,
                                     classes=dataset_classes,
                                     transformations=preprocessor)
```

適合したトランスフォーマー タプルの一覧を使用して例を実行する場合は、次のコードを使用します。

```python
from sklearn.pipeline import Pipeline
from sklearn.impute import SimpleImputer
from sklearn.preprocessing import StandardScaler, OneHotEncoder
from sklearn.linear_model import LogisticRegression
from sklearn_pandas import DataFrameMapper

# assume that we have created two arrays, numerical and categorical, which holds the numerical and categorical feature names

numeric_transformations = [([f], Pipeline(steps=[('imputer', SimpleImputer(
    strategy='median')), ('scaler', StandardScaler())])) for f in numerical]

categorical_transformations = [([f], OneHotEncoder(
    handle_unknown='ignore', sparse=False)) for f in categorical]

transformations = numeric_transformations + categorical_transformations

# append model to preprocessing pipeline.
# now we have a full prediction pipeline.
clf = Pipeline(steps=[('preprocessor', DataFrameMapper(transformations)),
                      ('classifier', LogisticRegression(solver='lbfgs'))])

# clf.steps[-1][1] returns the trained classification model
# pass transformation as an input to create the explanation object
# "features" and "classes" fields are optional
tabular_explainer = TabularExplainer(clf.steps[-1][1],
                                     initialization_examples=x_train,
                                     features=dataset_feature_names,
                                     classes=dataset_classes,
                                     transformations=transformations)
```

## <a name="generate-feature-importance-values-via-remote-runs"></a>リモート実行を使用して特徴量の重要度の値を生成する

次の例は、`ExplanationClient` クラスを使用して、リモート実行のモデルの解釈可能性を有効にする方法を示しています。 これは概念的には、次の点を除き、ローカルな処理に似ています。

* リモート実行では、`ExplanationClient` を使用して解釈可能性のコンテキストをアップロードします。
* ローカル環境では、後でコンテキストをダウンロードします。

1. `azureml-interpret` パッケージと `azureml-contrib-interpret` パッケージをインストールします。
    ```bash
    pip install azureml-interpret
    pip install azureml-contrib-interpret
    ```
1. ローカルの Jupyter Notebook でトレーニング スクリプトを作成します。 たとえば、「 `train_explain.py` 」のように入力します。

    ```python
    from azureml.contrib.interpret.explanation.explanation_client import ExplanationClient
    from azureml.core.run import Run
    from interpret.ext.blackbox import TabularExplainer

    run = Run.get_context()
    client = ExplanationClient.from_run(run)

    # write code to get and split your data into train and test sets here
    # write code to train your model here 

    # explain predictions on your local machine
    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, 
                                 x_train, 
                                 features=feature_names, 
                                 classes=classes)

    # explain overall model predictions (global explanation)
    global_explanation = explainer.explain_global(x_test)
    
    # uploading global model explanation data for storage or visualization in webUX
    # the explanation can then be downloaded on any compute
    # multiple explanations can be uploaded
    client.upload_model_explanation(global_explanation, comment='global explanation: all features')
    # or you can only upload the explanation object with the top k feature info
    #client.upload_model_explanation(global_explanation, top_k=2, comment='global explanation: Only top 2 features')
    ```

1. Azure Machine Learning コンピューティングをコンピューティング先として設定し、トレーニング実行を送信します。 手順については、[モデル トレーニング用のコンピューティング先の設定](how-to-set-up-training-targets.md#amlcompute)に関するページを参照してください。 [ノートブックの例](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model/azure-integration/remote-explanation)も役立つ可能性があります。

1. ローカル環境の Jupyter Notebook で説明をダウンロードします。

    ```python
    from azureml.contrib.interpret.explanation.explanation_client import ExplanationClient
    
    client = ExplanationClient.from_run(run)
    
    # get model explanation data
    explanation = client.download_model_explanation()
    # or only get the top k (e.g., 4) most important features with their importance values
    explanation = client.download_model_explanation(top_k=4)
    
    global_importance_values = explanation.get_ranked_global_values()
    global_importance_names = explanation.get_ranked_global_names()
    print('global importance values: {}'.format(global_importance_values))
    print('global importance names: {}'.format(global_importance_names))
    ```


## <a name="visualizations"></a>視覚化

ローカルの Jupyter ノートブックに説明をダウンロードしたら、視覚化ダッシュボードを使用してモデルを理解および解釈できます。

### <a name="understand-entire-model-behavior-global-explanation"></a>モデルの動作全体を理解する (グローバル説明) 

次のプロットで、トレーニング済みモデルの全体像を、その予測および説明と共に示します。

|プロット|説明|
|----|-----------|
|データ探索| データセットの概要を予測値と共に表示します。|
|グローバルな重要度|個々のデータポイントの特徴量の重要度の値を集約して、モデルの総合的な上位 K (構成可能な K) の特徴量の重要度を示します。 基になるモデルの総合的な動作の理解に役立ちます。|
|説明の探索|特徴がモデルの予測値の変化、または予測値の確率にどのような影響を与えるかを示します。 特徴の操作の影響を表示します。|
|重要度の概要|すべてのデータ ポイントにまたがる個々の特徴量の重要度の値を使用して、各特徴が予測値に与える影響の分布を表示します。 この図を使用して、特徴値が予測値に与える影響を調べることができます。
|

[![視覚化ダッシュ ボード グローバル](./media/how-to-machine-learning-interpretability-aml/global-charts.png)](./media/how-to-machine-learning-interpretability-aml/global-charts.png#lightbox)

### <a name="understand-individual-predictions-local-explanation"></a>個々の予測を理解する (ローカル説明) 

総合プロットのいずれかで個々のデータ ポイントのいずれかをクリックすることで、任意のデータ ポイントの個々の特徴量の重要度のプロットを読み込むことができます。

|プロット|説明|
|----|-----------|
|ローカルな重要度|個々の予測の上位 K 個 (構成可能な K) の重要な特徴量を示します。 特定のデータ ポイントでの基になるモデルのローカルな動作を示すのに役立ちます。|
|摂動探索 (What-if 分析)|選択されたデータ ポイントの特徴値への変更を許可し、予測値への結果の変更を観察します。|
|個別条件付き期待値 (ICE)| 特徴値の最小値から最大値への変更を許可します。 特徴が変化したときにデータ ポイントの予測がどのように変化するかを示すのに役立ちます。|

[![視覚化ダッシュボードのローカルな特徴の重要度](./media/how-to-machine-learning-interpretability-aml/local-charts.png)](./media/how-to-machine-learning-interpretability-aml/local-charts.png#lightbox)


[![視覚化ダッシュボードの特徴の補正](./media/how-to-machine-learning-interpretability-aml/perturbation.gif)](./media/how-to-machine-learning-interpretability-aml/perturbation.gif#lightbox)


[![視覚化ダッシュボードの ICE プロット](./media/how-to-machine-learning-interpretability-aml/ice-plot.png)](./media/how-to-machine-learning-interpretability-aml/ice-plot.png#lightbox)

> [!NOTE]
> Jupyter カーネルが起動する前に、視覚化ダッシュボードのウィジェット拡張機能を必ず有効にしてください。

* Jupyter Notebook

    ```shell
    jupyter nbextension install --py --sys-prefix azureml.contrib.interpret.visualize
    jupyter nbextension enable --py --sys-prefix azureml.contrib.interpret.visualize
    ```

* JupyterLab

    ```shell
    jupyter labextension install @jupyter-widgets/jupyterlab-manager
    jupyter labextension install microsoft-mli-widget
    ```

視覚化ダッシュボードを読み込むには、次のコードを使用します。

```python
from interpret_community.widget import ExplanationDashboard

ExplanationDashboard(global_explanation, model, dataset=x_test)
```

### <a name="visualization-in-azure-machine-learning-studio"></a>Azure Machine Learning Studio での視覚化

[リモートの解釈可能性](how-to-machine-learning-interpretability-aml.md#generate-feature-importance-values-via-remote-runs)の手順 (生成された説明の Azure Machine Learning 実行履歴へのアップロード) を完了すると、[Azure Machine Learning Studio](https://ml.azure.com) に視覚化ダッシュボードが表示されます。 このダッシュボードは、前述した視覚化ダッシュボードの簡易バージョンです (リアル タイム計算を実行できるアクティブなコンピューティングが Studio に存在しないため、説明の探索と ICE プロットは無効になっています)。

データセット、グローバル説明、およびローカル説明を使用できる場合は、すべてのタブにデータが入力されます (摂動探索と ICE は除きます)。 グローバル説明のみを使用できる場合は、[重要度の概要] タブとすべてのローカル説明タブは無効になります。

Azure Machine Learning Studio の視覚化ダッシュボードにアクセスするには、次のパスのいずれかに従います。

* **[実験]** ウィンドウ (プレビュー)
  1. 左側のウィンドウの **[実験]** を選択して、Azure Machine Learning で実行した実験の一覧を表示します。
  1. 特定の実験を選択して、その実験内のすべての実行を表示します。
  1. 実行を選択してから **[説明]** タブを選択して、説明の視覚化ダッシュボードを表示します。

   [![視覚化ダッシュボードのローカルな特徴の重要度](./media/how-to-machine-learning-interpretability-aml/amlstudio-experiments.png)](./media/how-to-machine-learning-interpretability-aml/amlstudio-experiments.png#lightbox)

* **[モデル]** ウィンドウ
  1. 「[Azure Machine Learning を使用してモデルをデプロイする](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where)」の手順に従って元のモデルを登録している場合は、左側のウィンドウの **[モデル]** を選択してそれを表示できます。
  1. モデルを選択してから **[説明]** タブを選択して、説明の視覚化ダッシュボードを表示します。

## <a name="interpretability-at-inference-time"></a>推論時の解釈可能性

元のモデルと共に Explainer をデプロイし、推論時にそれを使用して、新しいデータポイントに対する個々の特徴量の重要度の値を指定できます。 また、推論時の解釈可能性のパフォーマンスを向上させるために、軽量のスコアリング Explainer も用意されています。 軽量のスコアリング Explainer をデプロイするプロセスはモデルのデプロイと同様で、次の手順が含まれます。

1. 説明オブジェクトを作成します。 たとえば、`TabularExplainer` を使用できます。

   ```python
    from interpret.ext.blackbox import TabularExplainer


   explainer = TabularExplainer(model, 
                                initialization_examples=x_train, 
                                features=dataset_feature_names, 
                                classes=dataset_classes, 
                                transformations=transformations)
   ```

1. 説明オブジェクトを使用してスコアリング Explainer を作成します。

   ```python
   from azureml.interpret.scoring.scoring_explainer import KernelScoringExplainer, save

   # create a lightweight explainer at scoring time
   scoring_explainer = KernelScoringExplainer(explainer)

   # pickle scoring explainer
   # pickle scoring explainer locally
   OUTPUT_DIR = 'my_directory'
   save(scoring_explainer, directory=OUTPUT_DIR, exist_ok=True)
   ```

1. スコアリング Explainer モデルを使用するイメージを構成して登録します。

   ```python
   # register explainer model using the path from ScoringExplainer.save - could be done on remote compute
   # scoring_explainer.pkl is the filename on disk, while my_scoring_explainer.pkl will be the filename in cloud storage
   run.upload_file('my_scoring_explainer.pkl', os.path.join(OUTPUT_DIR, 'scoring_explainer.pkl'))
   
   scoring_explainer_model = run.register_model(model_name='my_scoring_explainer', 
                                                model_path='my_scoring_explainer.pkl')
   print(scoring_explainer_model.name, scoring_explainer_model.id, scoring_explainer_model.version, sep = '\t')
   ```

1. 省略可能な手順として、クラウドからスコアリング Explainer を取得し、説明をテストすることができます。

   ```python
   from azureml.interpret.scoring.scoring_explainer import load

   # retrieve the scoring explainer model from cloud"
   scoring_explainer_model = Model(ws, 'my_scoring_explainer')
   scoring_explainer_model_path = scoring_explainer_model.download(target_dir=os.getcwd(), exist_ok=True)

   # load scoring explainer from disk
   scoring_explainer = load(scoring_explainer_model_path)

   # test scoring explainer locally
   preds = scoring_explainer.explain(x_test)
   print(preds)
   ```

1. 次の手順に従って、コンピューティング先にイメージをデプロイします。

   1. 必要に応じて、「[Azure Machine Learning を使用してモデルをデプロイする](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where)」の手順に従って元の予測モデルを登録します。

   1. スコアリング ファイルを作成します。

         ```python
         %%writefile score.py
         import json
         import numpy as np
         import pandas as pd
         import os
         import pickle
         from sklearn.externals import joblib
         from sklearn.linear_model import LogisticRegression
         from azureml.core.model import Model
          
         def init():
         
            global original_model
            global scoring_model
             
            # retrieve the path to the model file using the model name
            # assume original model is named original_prediction_model
            original_model_path = Model.get_model_path('original_prediction_model')
            scoring_explainer_path = Model.get_model_path('my_scoring_explainer')

            original_model = joblib.load(original_model_path)
            scoring_explainer = joblib.load(scoring_explainer_path)

         def run(raw_data):
            # get predictions and explanations for each data point
            data = pd.read_json(raw_data)
            # make prediction
            predictions = original_model.predict(data)
            # retrieve model explanations
            local_importance_values = scoring_explainer.explain(data)
            # you can return any data type as long as it is JSON-serializable
            return {'predictions': predictions.tolist(), 'local_importance_values': local_importance_values}
         ```
   1. デプロイ構成を定義します。

         この構成は、モデルの要件によって異なります。 次の例では、1 つの CPU コアと 1 GB のメモリを使用する構成を定義します。

         ```python
         from azureml.core.webservice import AciWebservice

          aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                                    memory_gb=1,
                                                    tags={"data": "NAME_OF_THE_DATASET",
                                                          "method" : "local_explanation"},
                                                    description='Get local explanations for NAME_OF_THE_PROBLEM')
         ```

   1. 環境の依存関係を含むファイルを作成します。

         ```python
         from azureml.core.conda_dependencies import CondaDependencies

         # WARNING: to install this, g++ needs to be available on the Docker image and is not by default (look at the next cell)

         azureml_pip_packages = ['azureml-defaults', 'azureml-contrib-interpret', 'azureml-core', 'azureml-telemetry', 'azureml-interpret']
 

         # specify CondaDependencies obj
         myenv = CondaDependencies.create(conda_packages=['scikit-learn', 'pandas'],
                                          pip_packages=['sklearn-pandas'] + azureml_pip_packages,
                                          pin_sdk_version=False)


         with open("myenv.yml","w") as f:
            f.write(myenv.serialize_to_string())

         with open("myenv.yml","r") as f:
            print(f.read())
         ```

   1. g++ がインストールされたカスタム Dockerfile を作成します。

         ```python
         %%writefile dockerfile
         RUN apt-get update && apt-get install -y g++
         ```

   1. 作成されたイメージをデプロイします。
   
         このプロセスには約 5 分かかります。

         ```python
         from azureml.core.webservice import Webservice
         from azureml.core.image import ContainerImage

         # use the custom scoring, docker, and conda files we created above
         image_config = ContainerImage.image_configuration(execution_script="score.py",
                                                         docker_file="dockerfile",
                                                         runtime="python",
                                                         conda_file="myenv.yml")

         # use configs and models generated above
         service = Webservice.deploy_from_model(workspace=ws,
                                             name='model-scoring-service',
                                             deployment_config=aciconfig,
                                             models=[scoring_explainer_model, original_model],
                                             image_config=image_config)

         service.wait_for_deployment(show_output=True)
         ```

1. デプロイをテストします。

    ```python
    import requests

    # create data to test service with
    examples = x_list[:4]
    input_data = examples.to_json()

    headers = {'Content-Type':'application/json'}

    # send request to service
    resp = requests.post(service.scoring_uri, input_data, headers=headers)

    print("POST to url", service.scoring_uri)
    # can covert back to Python objects from json string if desired
    print("prediction:", resp.text)
    ```

1. クリーンアップします。

   デプロイされた Web サービスを削除するには、`service.delete()` を使用します。

## <a name="next-steps"></a>次のステップ

[モデルの解釈可能性の詳細について学習する](how-to-machine-learning-interpretability.md)

[Azure Machine Learning の解釈可能性サンプル ノートブックを確認する](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)

