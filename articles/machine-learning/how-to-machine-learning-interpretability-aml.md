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
ms.custom: how-to, devx-track-python, responsible-ml
ms.openlocfilehash: fda1bc2ef0a112a8a32ba7c4caebf29028c8cdd7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "98222753"
---
# <a name="use-the-interpretability-package-to-explain-ml-models--predictions-in-python-preview"></a>解釈可能性パッケージを使用して、Python ML モデルと予測について説明する (プレビュー)



この攻略ガイドでは、Azure Machine Learning Python SDK の解釈可能性パッケージを使用して次のタスクを実行する方法について学習します。


* 個人用コンピューターでローカルに実行されるモデルの動作全体または個々の予測について説明します。

* エンジニアリング済みの特徴量のための解釈可能性テクニックを有効にします。

* Azure でのモデル全体と個々の予測の動作について説明します。

* 視覚化ダッシュボードを使用して、モデルの説明と対話します。

* モデルと共にスコアリング Explainer をデプロイして、推論中の説明を観察します。



サポートされている解釈可能性テクニックと機械学習モデルの詳細については、「[Azure Machine Learning でのモデルの解釈可能性](how-to-machine-learning-interpretability.md)」と「[サンプル ノートブック](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)」を参照してください。

## <a name="generate-feature-importance-value-on-your-personal-machine"></a>個人用コンピューターで特徴量の重要度の値を生成する 
次の例は、Azure サービスに接続することなく、個人用コンピューターで解釈可能性パッケージを使用する方法を示しています。

1. `azureml-interpret` パッケージをインストールします。
    ```bash
    pip install azureml-interpret
    ```

2. ローカルの Jupyter Notebook でサンプル モデルをトレーニングします。

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

1. `azureml-interpret` パッケージをインストールします。
    ```bash
    pip install azureml-interpret
    ```
1. ローカルの Jupyter Notebook でトレーニング スクリプトを作成します。 たとえば、「 `train_explain.py` 」のように入力します。

    ```python
    from azureml.interpret import ExplanationClient
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

1. Azure Machine Learning コンピューティングをコンピューティング先として設定し、トレーニング実行を送信します。 手順については、「[Azure Machine Learning コンピューティング クラスターの作成と管理](how-to-create-attach-compute-cluster.md)」を参照してください。 [ノートブックの例](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model/azure-integration/remote-explanation)も役立つ可能性があります。

1. ローカル環境の Jupyter Notebook で説明をダウンロードします。

    ```python
    from azureml.interpret import ExplanationClient
    
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

ローカルの Jupyter Notebook に説明をダウンロードしたら、視覚化ダッシュボードを使用してモデルを理解および解釈できます。 Jupyter Notebook に視覚化ダッシュボード ウィジェットを読み込むには、次のコードを使用します。

```python
from interpret_community.widget import ExplanationDashboard

ExplanationDashboard(global_explanation, model, datasetX=x_test)
```

視覚化では、エンジニアリングされたものと未加工の特徴の両方に関する説明をサポートします。 未加工の説明は元のデータセットの特徴に基づいており、エンジニアリングされた説明は特徴エンジニアリングが適用されたデータセットの特徴に基づいています。

元のデータセットに関してモデルを解釈しようとする場合、各特徴量の重要度は元のデータセットの列に対応するため、未加工の説明を使用することをお勧めします。 エンジニアリングされた説明が役立つ可能性がある 1 つのシナリオは、カテゴリ特徴から個々のカテゴリの影響を調べる場合です。 ワンホット エンコーディングがカテゴリ特徴に適用される場合、結果として得られるエンジニアリングされた説明には、カテゴリごとに異なる重要度の値が、ワンホット エンジニアリングされた特徴ごとに 1 つずつ含まれます。 これは、データセットのどの部分がモデルに最も有益な情報を提供するかを絞り込む場合に役立ちます。

> [!NOTE]
> エンジニアリング説明と未加工の説明は、順番に計算されます。 まず、モデルと特徴量化パイプラインに基づいて、エンジニアリングされた説明が作成されます。 次に、同じ未加工の特徴から得られた、エンジニアリングされた特徴量の重要度を集計することにより、そのエンジニアリングされた説明に基づいて未加工の説明が作成されます。

### <a name="create-edit-and-view-dataset-cohorts"></a>データセットのコーホートの作成、編集、表示

上部のリボンには、モデルとデータに関する総合的な統計が表示されます。 データをデータセットのコーホート (サブグループ) に細分化して、これらの定義されたサブグループ全体でモデルのパフォーマンスと説明を調査または比較できます。 これらのサブグループ間でデータセットの統計と説明を比較することにより、あるグループと別のグループで発生する可能性のあるエラーの理由を把握できます。

[![データセットのコーホートの作成、編集、表示](./media/how-to-machine-learning-interpretability-aml/dataset-cohorts.gif)](./media/how-to-machine-learning-interpretability-aml/dataset-cohorts.gif#lightbox)

### <a name="understand-entire-model-behavior-global-explanation"></a>モデルの動作全体を理解する (グローバル説明) 

説明ダッシュボードの最初の 3 つのタブでは、トレーニングされたモデルの全体的な分析と共にその予測および説明が提供されます。

#### <a name="model-performance"></a>モデル パフォーマンス
得られた予測値とご使用のモデルのパフォーマンス メトリックの値の分布を調べて、モデルのパフォーマンスを評価します。 データセットのさまざまなコーホートまたはサブグループにおけるパフォーマンスの比較分析を確認して、モデルを詳しく調べることができます。 さまざまなディメンションを横断するには、y 値と x 値に沿ったフィルターを選択します。 正確性、精度、リコール、擬陽性率 (FPR)、偽陰性率 (FNR) などのメトリックを表示できます。

[![説明の視覚化での [モデル パフォーマンス] タブ](./media/how-to-machine-learning-interpretability-aml/model-performance.gif)](./media/how-to-machine-learning-interpretability-aml/model-performance.gif#lightbox)

#### <a name="dataset-explorer"></a>データセット エクスプローラー
X、Y、色の各軸に沿ってさまざまなフィルターを選択し、さまざまな次元に沿ってデータをスライスすることにより、データセットの統計を調べます。 上記のデータセットのコーホートを作成し、予測結果、データセットの特徴、エラー グループなどのフィルターを使用してデータセットの統計を分析します。 グラフの種類を変更するには、グラフの右上隅にある歯車のアイコンを使用します。

[![説明の視覚化での [データセット エクスプローラー] タブ](./media/how-to-machine-learning-interpretability-aml/dataset-explorer.gif)](./media/how-to-machine-learning-interpretability-aml/dataset-explorer.gif#lightbox)

#### <a name="aggregate-feature-importance"></a>特徴量の重要度の集計
モデル全体の予測に影響を与える上位 k 個の重要な特徴を調べます (グローバル説明とも呼ばれます)。 特徴量の重要度の値を降順で表示するには、スライダーを使用します。 最大 3 つのコーホートを選択すると、それらの特徴量の重要度の値が並べて表示されます。 グラフ内の特徴バーのいずれかをクリックすると、選択した特徴量の値が次の依存関係プロットのモデル予測にどのように影響するかを確認できます。

[![説明の視覚化での [特徴量の重要度の集計] タブ](./media/how-to-machine-learning-interpretability-aml/aggregate-feature-importance.gif)](./media/how-to-machine-learning-interpretability-aml/aggregate-feature-importance.gif#lightbox)

### <a name="understand-individual-predictions-local-explanation"></a>個々の予測を理解する (ローカル説明) 

説明タブの 4 番目のタブでは、個々のデータポイントとそれらの個々の特徴量の重要度を詳しく調べることができます。 メインの散布図で個々のデータ ポイントのいずれかをクリックするか、右側のパネル ウィザードで特定のデータ ポイントを選択することにより、任意のデータ ポイントの個々の特徴量の重要度プロットを読み込むことができます。

|プロット|説明|
|----|-----------|
|個々の特徴量の重要度|個々の予測の上位 k 個の重要な特徴量を示します。 特定のデータ ポイントでの基になるモデルのローカルな動作を示すのに役立ちます。|
|What-if 分析|選択した実際のデータ ポイントの特徴量の値を変更し、新しい特徴量の値を使用して仮想データ ポイントを生成することにより、変更の結果として生じる予測値の変化を観察できます。|
|個別条件付き期待値 (ICE)|特徴値の最小値から最大値への変更を許可します。 特徴が変化したときにデータ ポイントの予測がどのように変化するかを示すのに役立ちます。|

[![説明ダッシュボードの [個々の特徴量の重要度と What-If] タブ](./media/how-to-machine-learning-interpretability-aml/individual-tab.gif)](./media/how-to-machine-learning-interpretability-aml/individual-tab.gif#lightbox)

> [!NOTE]
> これらは多くの近似値に基づく説明であり、予測の "原因" ではありません。 因果推論の厳密な数学的堅牢性がなければ、ユーザーが What-If ツールの特徴量の摂動に基づいて実際の決定を行うことはお勧めしません。 このツールは、主としてモデルの理解とデバッグに使用します。

### <a name="visualization-in-azure-machine-learning-studio"></a>Azure Machine Learning Studio での視覚化

[リモートの解釈可能性](how-to-machine-learning-interpretability-aml.md#generate-feature-importance-values-via-remote-runs)の手順 (生成された説明の Azure Machine Learning 実行履歴へのアップロード) を完了すると、[Azure Machine Learning Studio](https://ml.azure.com) に視覚化ダッシュボードが表示されます。 このダッシュボードは、上で説明した視覚化ダッシュボードのより単純なバージョンです。 Azure Machine Learning スタジオには、リアルタイムの計算を実行できるアクティブなコンピューティングがないため、What-If データ ポイントの生成と ICE プロットは無効になります。

データ セット、グローバルおよびローカルの説明が使用可能な場合、すべてのタブにデータが入力されます。 グローバル説明のみを使用できる場合、[個々の特徴量の重要度] タブは無効になります。

Azure Machine Learning Studio の視覚化ダッシュボードにアクセスするには、次のパスのいずれかに従います。

* **[実験]** ウィンドウ (プレビュー)
  1. 左側のウィンドウの **[実験]** を選択して、Azure Machine Learning で実行した実験の一覧を表示します。
  1. 特定の実験を選択して、その実験内のすべての実行を表示します。
  1. 実行を選択してから **[説明]** タブを選択して、説明の視覚化ダッシュボードを表示します。

   [![実験の AzureML スタジオ内の視覚化ダッシュボードと [特徴量の重要度の集計]](./media/how-to-machine-learning-interpretability-aml/model-explanation-dashboard-aml-studio.png)](./media/how-to-machine-learning-interpretability-aml/model-explanation-dashboard-aml-studio.png#lightbox)

* **[モデル]** ウィンドウ
  1. 「[Azure Machine Learning を使用してモデルをデプロイする](./how-to-deploy-and-where.md)」の手順に従って元のモデルを登録している場合は、左側のウィンドウの **[モデル]** を選択してそれを表示できます。
  1. モデルを選択してから **[説明]** タブを選択して、説明の視覚化ダッシュボードを表示します。

## <a name="interpretability-at-inference-time"></a>推論時の解釈可能性

元のモデルと共に Explainer をデプロイし、推論時にそれを使用して、新しいデータ ポイントに対する個々の特徴量の重要度の値 (ローカル説明) を指定できます。 また、推論時の解釈可能性のパフォーマンスを向上させるために、軽量のスコアリング Explainer も用意されています。これは、現在、Azure Machine Learning SDK でのみサポートされています。 軽量のスコアリング Explainer をデプロイするプロセスはモデルのデプロイと同様で、次の手順が含まれます。

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

   1. 必要に応じて、「[Azure Machine Learning を使用してモデルをデプロイする](./how-to-deploy-and-where.md)」の手順に従って元の予測モデルを登録します。

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

         azureml_pip_packages = ['azureml-defaults', 'azureml-core', 'azureml-telemetry', 'azureml-interpret']
 

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

## <a name="troubleshooting"></a>トラブルシューティング

* **スパース データがサポートされない**: モデルの説明ダッシュボードでは、特徴の数が多いと機能が中断するか、速度が大幅に低下するため、現在、スパース データ形式はサポートされていません。 さらに、データ セットが大きい場合や特徴の数が多い場合、一般的なメモリの問題が発生します。 

* **モデルの説明で予測モデルがサポートされない**: 解釈可能性、最適なモデルの説明は、最適なモデルとして TCNForecaster、AutoArima、Prophet、ExponentialSmoothing、Average、Naive、Seasonal Average、Seasonal Naive のアルゴリズムを推奨する AutoML 予測実験では利用できません。 AutoML 予測には、説明をサポートする回帰モデルが用意されています。 ただし、説明ダッシュボードでは、データ パイプラインが複雑なため、[個々の特徴量の重要度] タブは予測に対してサポートされていません。

* **データ インデックスのローカル説明**: 説明ダッシュボードでは、データをランダムにダウンサンプリングするため、そのデータ セットのデータ ポイントが 5,000 個を超える場合、ローカルの重要度値を元の検証データ セットの行識別子に関連付けることをサポートしていません。 ただし、ダッシュボードには、[個別の特徴量の重要度] タブでダッシュボードに渡された各データ ポイントについて未加工のデータ セットの特徴量値が表示されます。ユーザーは、未加工のデータ セットの特徴量値を照合することで、ローカルの重要度を元のデータ セットにマッピングできます。 検証データ セットのサイズが 5,000 サンプル未満の場合、AzureML スタジオの `index` 機能は検証データ セットのインデックスに対応します。

* **スタジオで What-if または ICE のプロットがサポートされない**: アップロードされた説明には、摂動された特徴量の予測と確率を再計算するためのアクティブな計算が必要であるため、Azure Machine Learning スタジオの [説明] タブでは、What-If および Individual Conditional Expectation (ICE) プロットはサポートされていません。 現在、これは、SDK を使用してウィジェットとして実行すると、Jupyter ノートブックでサポートされます。


## <a name="next-steps"></a>次のステップ

[モデルの解釈可能性の詳細について学習する](how-to-machine-learning-interpretability.md)

[Azure Machine Learning の解釈可能性サンプル ノートブックを確認する](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)
