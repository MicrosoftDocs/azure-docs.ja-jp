---
title: モデルの解釈可能性
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning SDK を使用して、モデルから得られる予測の理由を説明する方法について説明します。 トレーニング中と推論中に、モデルで予測が行われる方法を理解するために使用できます。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: larryfr
ms.date: 06/21/2019
ms.openlocfilehash: 1e742c278b9356c7501964541802e0c96dc74b09
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68358649"
---
# <a name="model-interpretability-with-azure-machine-learning-service"></a>Azure Machine Learning service を使用したモデルの解釈可能性

この記事では、Azure Machine Learning Python SDK のさまざまな解釈可能性パッケージを使用してモデルが行った予測の理由を説明する方法について説明します。

SDK のクラスとメソッドを使用して、次のことを得ることができます。
+ 未加工の特徴とエンジニアリングされた両方の特徴の重要度の値
+ トレーニングおよび推論中の実際の大規模データセットに関する解釈可能性。
+ トレーニング時にデータと説明のパターンの検出を支援するための対話型の視覚化

開発サイクルのトレーニング フェーズでは、モデル設計者と評価者が、モデルの解釈可能性出力を使用して仮説を検証し、利害関係者との信頼を構築できます。  また、モデルへの洞察をデバッグに使用したり、モデルの動作が目標に一致しているか検証したり、偏りがないか検査したりできます。

機械学習では、**特徴**は、ターゲット データ ポイントの予測に使用されるデータ フィールドです。 たとえば、信用リスクを予測するために、年齢、アカウント サイズ、およびアカウントの有効期間のデータ フィールドが使用されるとします。 この場合、年齢、アカウント サイズ、およびアカウントの有効期間が**特徴**です。 特徴の重要度から、各データ フィールドがモデルの予測にどのような影響を与えたかがわかります。 たとえば、年齢は予測で頻繁に使用されるが、アカウントのサイズと有効期間は予測精度に大きな影響を与えない場合があります。 このプロセスにより、データ サイエンティストは結果の予測を説明でき、利害関係者はモデルで最も重要なデータ ポイントを認識できます。

これらのツールを使用すると、使いやすくてスケーラブルな最先端のテクノロジを利用して、**すべてのデータについてグローバルに**、または**特定のデータ ポイントについてローカルに**、機械学習モデルを説明できます。

解釈可能性クラスは、複数の SDK パッケージを介して利用可能になります。 [Azure Machine Learning 用の SDK パッケージをインストールする](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)方法を確認してください。

* [`azureml.explain.model`](https://docs.microsoft.com/python/api/azureml-explain-model/?view=azure-ml-py)。Microsoft でサポートされている機能を含むメイン パッケージ。

* `azureml.contrib.explain.model`。試すことができるプレビュー機能と実験的機能。

* `azureml.train.automl.automlexplainer`。自動化された機械学習モデルを解釈するためのパッケージ。

> [!IMPORTANT]
> `contrib` 名前空間のコンテンツは完全にはサポートされていません。 実験的な機能が成熟すると、メインの名前空間に徐々に移動されます。

## <a name="how-to-interpret-your-model"></a>モデルを解釈する方法

解釈可能性クラスとメソッドを適用して、モデルのグローバルな動作または特定の予測を理解することができます。 前者はグローバル説明と呼ばれ、後者はローカル説明と呼ばれます。

メソッドは、メソッドがモデルに依存しないか、またはモデルに固有かに基づいても分類できます。 一部のメソッドは、特定の種類のモデルを対象にします。 たとえば、SHAP の tree explainer は、ツリー ベースのモデルにのみ適用されます。 mimic explainer や SHAP の kernel explainer などの一部のメソッドでは、モデルはブラック ボックスとして処理されます。 `explain` パッケージでは、データ セット、モデルの種類、およびユース ケースに基づいて、これらのさまざまなアプローチが使用されます。

出力は、特定のモデルが予測を行うときの方法に関する情報セットであり、例えば次のものがあります。
* グローバル/ローカルな特徴の相対的重要度

* グローバル/ローカルな特徴と予測の関係

### <a name="explainers"></a>Explainer

SDK には次の 2 つの Explainer セットがあります。Direct Explainer と Meta Explainer です。

__Direct Explainer__ は統合されたライブラリからのものです。 SDK はすべての Explainer をラップし、共通の API と出力形式が公開されるようにします。 これらの Explainer を直接使用する方がよい場合は、共通の API と出力形式を使用する代わりに直接呼び出すことができます。 以下は、SDK で使用可能な Direct Explainer の一覧です。

* **SHAP Tree Explainer**:SHAP の Tree Explainer は、ツリーおよびツリーのアンサンブルに固有の多項式時間高速 SHAP 値推定アルゴリズムに注目します
* **SHAP Deep Explainer**:SHAP による説明に基づくと、Deep Explainer は "SHAP NIPS の論文で説明されている DeepLIFT との接続上に構築されているディープ ラーニング モデルでの SHAP 値に対する高速近似アルゴリズムです。 TensorFlow モデルおよび TensorFlow バックエンドを使用する Keras モデルがサポートされています (PyTorch の暫定サポートもあります)"。
* **SHAP Kernel Explainer**:SHAP の Kernel Explainer は、特別に重み付けされたローカル線形回帰を使用して任意のモデルの SHAP 値を推定します。
* **Mimic Explainer**: Mimic Explainer は、グローバル サロゲート モデルのアイデアに基づいています。 グローバル サロゲート モデルは、ブラック ボックス モデルの予測をできる限り正確に近似するためにトレーニングされた本質的に解釈可能なモデルです。 データ サイエンティストは、サロゲート モデルを解釈してブラック ボックス モデルについての結論を導き出すことができます。 サロゲート モデルとして、次のいずれかの解釈可能なモデルを使用できます。LightGBM (LinearExplainableModel)、線形回帰 (LinearExplainableModel)、確率勾配降下説明可能モデル (SGDExplainableModel)、およびデシジョン ツリー (DecisionTreeExplainableModel)。


* **Permutation Feature Importance Explainer**:Permutation Feature Importance は、[Breiman のランダム フォレスト論文](https://www.stat.berkeley.edu/%7Ebreiman/randomforest2001.pdf) (セクション 10 を参照してください) から着想を得た、分類および回帰モデルの説明に使用される手法です。 概要を説明すると、データセット全体に対して一度に 1 つの特徴のデータをランダムにシャッフルし、対象のパフォーマンス メトリックがどのくらい低下するかを計算するしくみです。 変化が大きいほど、その特徴の重要度は高くなります。

* **LIME Explainer** (`contrib`):LIME に基づく LIME Explainer は、最新の LIME (Local interpretable model-agnostic explanations) アルゴリズムを使用して、ローカル サロゲート モデルを作成します。 グローバル サロゲート モデルとは異なり、LIME はローカル サロゲート モデルをトレーニングして個別の予測を説明することに着目します。
* **HAN Text Explainer** (`contrib`):HAN Text Explainer は、特定のブラック ボックス テキスト モデルに対するテキスト データからモデルの説明を取得するために Hierarchical Attention Network を使用します。 特定の教師モデルの予測された出力で HAN サロゲート モデルをトレーニングします。 テキスト コーパス全体でグローバルにトレーニングした後、説明の正確性を向上させるため、特定のドキュメントに対する微調整ステップを追加しました。 HAN は、文と単語のアテンションに対する 2 つのアテンション レイヤーで双方向 RNN を使用します。 DNN が教師モデルでトレーニングされて、特定のドキュメントで微調整された後、アテンション レイヤーから単語の重要性を抽出できます。 テキスト データについては HAN は LIME や SHAP より正確ですが、トレーニング時間に関していっそうコストがかかります。 ただし、GloVe 単語埋め込みを使用してネットワークを初期化するオプションをユーザーに提供することで、トレーニング時間を改善しましたが、まだ時間がかかります。 HAN をリモート Azure GPU VM で実行することで、トレーニング時間を大幅に改善できます。 HAN の実装については、「Hierarchical Attention Networks for Document Classification (ドキュメントの分類に対する階層型アテンション ネットワーク)」 (Yang 他、2016) ([https://www.cs.cmu.edu/~diyiy/docs/naacl16.pdf](https://www.cs.cmu.edu/~diyiy/docs/naacl16.pdf)) で説明されています。


__Meta Explainer__ は、自動的に適切な Direct Explainer を選択し、特定のモデルとデータ セットに基づいて最適な説明情報を生成します。 Meta Explainer は、統合または開発したすべてのライブラリ (SHAP、LIME、Mimic など) を利用します。 以下は、SDK で使用可能な Meta Explainer です。

* **Tabular Explainer**: 表形式のデータセットで使用されます。
* **Text Explainer**: テキストのデータセットで使用されます。
* **Image Explainer**:画像データセットと共に使用されます。

Direct Explainer のメタ選択に加えて、Meta Explainer では、基になるライブラリ上に追加機能が開発され、Direct Explainer より速度とスケーラビリティが向上します。

現在、`TabularExplainer` では次のロジックを使用して Direct SHAP Explainer が呼び出されています。

1. ツリー ベースのモデルの場合は、SHAP `TreeExplainer` を適用し、それ以外の場合、
2. DNN モデルの場合は、SHAP `DeepExplainer` を適用し、それ以外の場合は、
3. ブラックボックス モデルとして扱い、SHAP `KernelExplainer` を適用します

`TabularExplainer` に組み込まれたインテリジェンスは、より多くのライブラリが SDK に統合され、各 Explainer の長所と短所について学習することで、ますます高度なものになります。

`TabularExplainer` ではまた、Direct Explainer より大きな機能とパフォーマンスの強化も行われています。

* **初期化データセットの要約作成**。 説明の速度が最も重要な場合、初期化データセットを要約し、代表的なサンプルの小さなセットを生成します。これにより、グローバルとローカル両方の説明が高速化されます。
* **評価データ セットのサンプリング**。 ユーザーが評価サンプルの大きなセットを渡しても、実際にはそれらのすべてを評価する必要がない場合は、サンプリング パラメーターを true に設定し、グローバルな説明を高速化できます。

次の図は、Direct Explainer と Meta Explainer の現在の構造を示しています。

[![機械学習解釈可能性のアーキテクチャ](./media/machine-learning-interpretability-explainability/interpretability-architecture.png)](./media/machine-learning-interpretability-explainability/interpretability-architecture.png#lightbox)


### <a name="models-supported"></a>サポートされているモデル

Python の `numpy.array`、`pandas.DataFrame`、`iml.datatypes.DenseData`、または `scipy.sparse.csr_matrix` 形式のデータセットでトレーニングされたすべてのモデルが、SDK の解釈可能性 `explain` パッケージによってサポートされます。

説明関数では、モデルとパイプラインの両方が入力として受け付けられます。 モデルを提供する場合、モデルでは Scikit 規則に準拠する予測関数 `predict` または `predict_proba` が実装されている必要があります。 パイプライン (パイプライン スクリプトの名前) が提供された場合、説明関数では、パイプライン スクリプトの実行から予測が返るものと想定します。 PyTorch、TensorFlow、Keras のディープ ラーニング フレームワークでトレーニングされたモデルをサポートしています。

### <a name="local-and-remote-compute-target"></a>ローカルとリモートのコンピューティング先

`explain` パッケージは、ローカルおよびリモート両方のコンピューティング先で動作するように設計されています。 ローカルで実行した場合、SDK 関数はどの Azure サービスにも接続されません。 Azure Machine Learning コンピューティング上で説明をリモートで実行し、説明情報を Azure Machine Learning 実行履歴サービスに記録できます。 この情報が記録されると、ユーザーによる分析のため、説明からのレポートや視覚化を、Azure Machine Learning ワークスペース ポータルですぐに使用できます。

## <a name="interpretability-in-training"></a>トレーニングでの解釈可能性

### <a name="train-and-explain-locally"></a>ローカル環境でトレーニングと説明を行う

1. ローカル環境の Jupyter ノートブックでモデルをトレーニングします。

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

2. Explainer を呼び出します。Explainer オブジェクトを初期化するには、モデルとトレーニング データの一部を Explainer のコンストラクターに渡す必要があります。 また、説明や視覚化をよりわかりやすくするために使用される機能名と出力クラス名 (分類を行う場合) をオプションで渡すこともできます。 ローカルで [TabularExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.tabularexplainer?view=azure-ml-py)、[MimicExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.mimic.mimicexplainer?view=azure-ml-py)、[PFIExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.permutation.permutation_importance.pfiexplainer?view=azure-ml-py) を使用して Explainer オブジェクトをインスタンス化する方法を次に示します。 `TabularExplainer` は下にある 3 つの SHAP Explainer (`TreeExplainer`、`DeepExplainer`、`KernelExplainer`) のいずれかを呼び出し、ユース ケース用に最適な 1 つを自動的に選択します。 ただし、基になっている 3 つの Explainer のそれぞれを直接呼び出すことができます。

    ```python
    from azureml.explain.model.tabular_explainer import TabularExplainer
    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, 
                                 x_train, 
                                 features=breast_cancer_data.feature_names, 
                                 classes=classes)
    ```

    or

    ```python
    from azureml.explain.model.mimic.mimic_explainer import MimicExplainer
    
    # you can use one of the following four interpretable models as a global surrogate to the black box model
    from azureml.explain.model.mimic.models.lightgbm_model import LGBMExplainableModel
    from azureml.explain.model.mimic.models.linear_model import LinearExplainableModel
    from azureml.explain.model.mimic.models.linear_model import SGDExplainableModel
    from azureml.explain.model.mimic.models.tree_model import DecisionTreeExplainableModel

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
    from azureml.explain.model.permutation.permutation_importance import PFIExplainer 
    
    # "features" and "classes" fields are optional
    explainer = PFIExplainer(model, 
                             features=breast_cancer_data.feature_names, 
                             classes=classes)
    ```

3. グローバルな特徴の重要度の値を取得します。

    ```python
    # you can use the training data or the test data here
    global_explanation = explainer.explain_global(x_train)
    
    # if you used the PFIExplainer in the previous step, use the next line of code instead
    # global_explanation = explainer.explain_global(x_train, true_labels=y_test)

    # sorted feature importance values and feature names
    sorted_global_importance_values = global_explanation.get_ranked_global_values()
    sorted_global_importance_names = global_explanation.get_ranked_global_names()
    dict(zip(sorted_global_importance_names, sorted_global_importance_values))

    # alternatively, you can print out a dictionary that holds the top K feature names and values
    global_explanation.get_feature_importance_dict()
    ```

4. ローカルな特徴の重要度の値を取得する: 次の関数呼び出しを使用して、個々のインスタンスまたはインスタンスのグループを説明します。 PFIExplainer はローカルな説明をサポートしていないことに注意してください。

    ```python
    # explain the first data point in the test set
    local_explanation = explainer.explain_local(x_test[0])

    # sorted feature importance values and feature names
    sorted_local_importance_names = local_explanation.get_ranked_local_names()
    sorted_local_importance_values = local_explanation.get_ranked_local_values()
    ```

    or

    ```python
    # explain the first five data points in the test set
    local_explanation = explainer.explain_local(x_test[0:4])

    # sorted feature importance values and feature names
    sorted_local_importance_names = local_explanation.get_ranked_local_names()
    sorted_local_importance_values = local_explanation.get_ranked_local_values()
    ```

### <a name="train-and-explain-remotely"></a>リモート環境でトレーニングと説明を行う

Azure Machine Learning service でサポートされているさまざまなコンピューティング先でトレーニングできますが、このセクションの例では Azure Machine Learning コンピューティング先を使用してこれを行う方法を示します。

1. ローカルの Jupyter ノートブック (run_explainer.py など) でトレーニング スクリプトを作成します。

    ```python
    from azureml.contrib.explain.model.explanation.explanation_client import ExplanationClient
    from azureml.core.run import Run

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

2. コンピューティング先として Azure Machine Learning コンピューティングを設定し、トレーニングの実行を送信する方法について学習するには、「[モデル トレーニング用のコンピューティング ターゲットを設定する](how-to-set-up-training-targets.md#amlcompute)」の手順に従ってください。

3. ローカル環境の Jupyter Notebook で説明をダウンロードします。

    ```python
    from azureml.contrib.explain.model.explanation.explanation_client import ExplanationClient
    
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

モデルを理解して解釈するには、視覚化のダッシュボードを使用します。

### <a name="global-visualizations"></a>グローバルな視覚化

次のプロットは、トレーニング済みモデルの全体像を、その予測および説明とともに示しています。

|プロット|説明|
|----|-----------|
|データ探索| データセットの概要と予測値。|
|グローバルな重要度|グローバルに上位 K 個 (構成可能な K) の重要な特徴を示します。 このグラフは、基になるモデルのグローバルな動作を理解するのに役立ちます。|
|説明の探索|ある特徴が、モデルの予測値 (または予測値の確率) の変更にいかに関与しているかを示します。 |
|まとめ| すべてのデータ ポイントにわたって、符号付きローカル特徴の重要度の値を使用して、各特徴が予測値に与える影響の分布を表示します。|

[![視覚化ダッシュ ボード グローバル](./media/machine-learning-interpretability-explainability/global-charts.png)](./media/machine-learning-interpretability-explainability/global-charts.png#lightbox)

### <a name="local-visualizations"></a>ローカルな視覚化

任意の時点で前のプロットの個々のデータ ポイントをクリックして、特定のデータ ポイントに対するローカル特徴の重要度プロットを読み込むことができます。

|プロット|説明|
|----|-----------|
|ローカルな重要度|グローバルに上位 K 個 (構成可能な K) の重要な特徴を示します。 このグラフは、特定のデータ ポイントについての基になるモデルのローカルな動作を理解するのに役立ちます。|
|補正の探索|選択したデータ ポイントの特徴値を変更し、それらの変更が予測値にどのように影響するかを観察できます。|
|個別条件付き期待値 (ICE)| 特徴値を最小値から最大値に変更して、特徴が変化したときにデータ ポイントの予測がどのように変化するかを確認できます。|

[![視覚化ダッシュボードのローカルな特徴の重要度](./media/machine-learning-interpretability-explainability/local-charts.png)](./media/machine-learning-interpretability-explainability/local-charts.png#lightbox)


[![視覚化ダッシュボードの特徴の補正](./media/machine-learning-interpretability-explainability/perturbation.gif)](./media/machine-learning-interpretability-explainability/perturbation.gif#lightbox)


[![視覚化ダッシュボードの ICE プロット](./media/machine-learning-interpretability-explainability/ice-plot.png)](./media/machine-learning-interpretability-explainability/ice-plot.png#lightbox)

Jupyter カーネルを起動する前に、視覚化ダッシュボードのウィジェット拡張機能を有効にする必要があります。

* Jupyter Notebook

    ```shell
    jupyter nbextension install --py --sys-prefix azureml.contrib.explain.model.visualize
    jupyter nbextension enable --py --sys-prefix azureml.contrib.explain.model.visualize
    ```



* Jupyter のラボ

    ```shell
    jupyter labextension install @jupyter-widgets/jupyterlab-manager
    jupyter labextension install microsoft-mli-widget
    ```
視覚化のダッシュボードを読み込むには、次のコードを使用します。

```python
from azureml.contrib.explain.model.visualize import ExplanationDashboard

ExplanationDashboard(global_explanation, model, x_test)
```

## <a name="raw-feature-transformations"></a>未加工の特徴の変換

必要に応じて、特徴変換パイプラインを Explainer に渡して、(エンジニアリングされた特徴ではなく) 変換前の未加工の特徴における説明を受け取ることができます。 これを省略した場合、Explainer はエンジニアリングされた特徴における説明を提供します。

サポートされている変換の形式は、[sklearn-pandas](https://github.com/scikit-learn-contrib/sklearn-pandas) に記載されているものと同じです。 一般に、どのような変換も、単一の列で動作し、したがって明らかに 1 対多である限りサポートされます。 

生の特徴は、`sklearn.compose.ColumnTransformer` または適合するトランスフォーマー タプルの一覧を使って説明できます。 以下のセルは `sklearn.compose.ColumnTransformer` を使用しています。 

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

## <a name="interpretability-at-inferencing-time"></a>推論時の解釈可能性

Explainer は元のモデルと共にデプロイでき、ローカルの説明情報を提供するためにスコアリング時に使用できます。 推論時の解釈可能性をより効率的にするために、より軽量のスコアリング Explainer も用意されています。 軽量のスコアリング Explainer をデプロイするプロセスはモデルのデプロイと同様で、次の手順が含まれます。




1. 説明オブジェクトを作成します (たとえば TabularExplainer を使用します)。

   ```python
   from azureml.contrib.explain.model.tabular_explainer import TabularExplainer

   explainer = TabularExplainer(model, 
                                initialization_examples=x_train, 
                                features=dataset_feature_names, 
                                classes=dataset_classes, 
                                transformations=transformations)
   ```

1. 説明オブジェクトを使用してスコアリング Explainer を作成します。

   ```python
   from azureml.contrib.explain.model.scoring.scoring_explainer import KernelScoringExplainer, save

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

1. [省略可能] スコアリング Explainer をクラウドから取得し、説明をテストします

   ```python
   from azureml.contrib.explain.model.scoring.scoring_explainer import load

   # retrieve the scoring explainer model from cloud"
   scoring_explainer_model = Model(ws, 'my_scoring_explainer')
   scoring_explainer_model_path = scoring_explainer_model.download(target_dir=os.getcwd(), exist_ok=True)

   # load scoring explainer from disk
   scoring_explainer = load(scoring_explainer_model_path)

   # test scoring explainer locally
   preds = scoring_explainer.explain(x_test)
   print(preds)
   ```

1. イメージをコンピューティング先にデプロイします。

   1. スコアリング ファイルを作成します (この手順の前に、「[Azure Machine Learning service を使用してモデルをデプロイする](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where)」の手順を実行して元の予測モデルを登録します)

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

   1. デプロイ構成を定義します (この構成は、モデルの要件によって異なります。 次の例では、1 つの CPU コアと 1 GB のメモリが使用される構成を定義しています)

        ```python
        from azureml.core.webservice import AciWebservice

        aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                                       memory_gb=1,
                                                       tags={"data": "NAME_OF_THE_DATASET",
                                                             "method" : "local_explanation"},
                                                       description='Get local explanations for NAME_OF_THE_PROBLEM')
        ```

   1. 環境の依存関係を持つファイルを作成します。

        ```python
        from azureml.core.conda_dependencies import CondaDependencies

        # WARNING: to install this, g++ needs to be available on the Docker image and is not by default (look at the next cell)

        azureml_pip_packages = ['azureml-defaults', 'azureml-contrib-explain-model', 'azureml-core', 'azureml-telemetry', 'azureml-explain-model']
 

        # specify CondaDependencies obj
        myenv = CondaDependencies.create(conda_packages=['scikit-learn', 'pandas'],
                                         pip_packages=['sklearn-pandas'] + azureml_pip_packages,
                                         pin_sdk_version=False)


        with open("myenv.yml","w") as f:
            f.write(myenv.serialize_to_string())

        with open("myenv.yml","r") as f:
            print(f.read())
        ```

   1. g++ がインストールされているカスタム Dockerfile を作成します

        ```python
        %%writefile dockerfile
        RUN apt-get update && apt-get install -y g++
        ```

   1. 作成済みのイメージをデプロイします (推定所要時間:5 分)

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

1. 展開をテスト

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

1. クリーンアップ:デプロイされた Web サービスを削除するには、`service.delete()` を使用します。




## <a name="next-steps"></a>次の手順

上記の手順が示されている Jupyter ノートブックのコレクションを見るには、「[Azure Machine Learning Interpretability sample notebooks (Azure Machine Learning Interpretability サンプル ノートブック)](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)」をご覧ください。
