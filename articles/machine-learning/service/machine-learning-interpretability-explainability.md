---
title: モデルの解釈可能性
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning Interpretability SDK を使用して、モデルで予測が行われる理由を説明する方法について説明します。 トレーニング中と推論中に、モデルで予測が行われる方法を理解するために使用できます。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: larryfr
ms.date: 04/09/2019
ms.openlocfilehash: fbcafb61ecd69f58bb3c14d1b15f36f1b21f2833
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/11/2019
ms.locfileid: "59494442"
---
# <a name="azure-machine-learning-interpretability-sdk"></a>Azure Machine Learning Interpretability SDK

この記事では、モデルで Azure Machine Learning Interpretability SDK を使用することによって予測が作成される理由を説明する方法について学習します。 モデルを説明できることは、以下の理由により重要です。

* 顧客や利害関係者は、**モデルで行われた予測を信頼できるかどうか**を知りたい。
* データ サイエンティストは、**モデルのクエリを実行して分析情報を見つける方法**を理解したい。 また、**モデルを改善する方法**について情報に基づいて決定するためのツールも必要である。
* 会社は、**さまざまな入力分布でのモデルの動作**および**特定の入力の分析中のモデルの動作方法**を理解する必要がある。

機械学習の解釈可能性は、機械学習開発サイクルの 2 つのフェーズである**トレーニング**時と**推論**時において重要です。

* **トレーニング**中: モデル設計者と評価者は、利害関係者の信頼を築くためにモデルの出力を説明するための解釈可能性ツールが必要です。 モデルをデバッグして、動作が目標に一致しているかどうかを決定できるように、モデルへの分析情報も必要です。 最後に、確実にモデルがバイアスされていないようにする必要があります。
* **推論**中: モデルを使用する人に対して、予測を説明できる必要があります。 たとえば、モデルが住宅ローンを拒否した理由や、投資ポートフォリオに高いリスクを予測した理由などです。

Azure Machine Learning Interpretability SDK には、Microsoft と実績のあるサードパーティ ライブラリ (SHAP や LIME など) によって開発されたテクノロジが組み込まれています。 SDK では、統合されたライブラリ全体にわたって一般的な API が提供されており、Azure Machine Learning services と統合します。 この SDK を使用すると、使いやすくてスケーラブルな最先端のテクノロジを利用して、**すべてのデータについてグローバルに**、または**特定のデータ ポイントについてローカルに**、使用機械学習モデルを説明できます。

## <a name="how-does-it-work"></a>それはどのように機能しますか?

Azure Machine Learning Interpretability を適用し、モデルのグローバルな動作または特定の予測を理解することができます。 前者はグローバル説明と呼ばれ、後者はローカル説明と呼ばれます。

Azure Machine Learning Interpretability のメソッドは、メソッドがモデルに依存しないか、またはモデルに固有かに基づいても分類できます。 一部のメソッドは、特定の種類のモデルを対象にします。 たとえば、SHAP の tree explainer は、ツリー ベースのモデルにのみ適用されます。 mimic explainer や SHAP の kernel explainer などの一部のメソッドでは、モデルはブラック ボックスとして処理されます。 Azure Machine Learning Interpretability SDK では、データ セット、モデルの種類、およびユース ケースに基づいて、これらのさまざまなアプローチが使用されます。

Azure Machine Learning Interpretability では、モデルがその予測を行った方法についての情報のセットが返されます。 情報には次の項目が含まれます。

* グローバル/ローカルな特徴の相対的重要度
* グローバル/ローカルな特徴と予測の関係

## <a name="architecture"></a>アーキテクチャ

Azure Machine Learning Interpretability SDK は、2 つの Python パッケージで構成されています。

* [azureml.explain.model](https://docs.microsoft.com/python/api/azureml-explain-model/?view=azure-ml-py) -メインのパッケージであり、Microsoft によってサポートされる機能が含まれています。
* `azureml.contrib.explain.model` - 試すことができるプレビュー機能と実験的機能。

    > [!IMPORTANT]
    > contrib 内のものは完全にはサポートされていません。 実験的な機能が成熟すると、メインのパッケージに徐々に移動されます。

### <a name="explainers"></a>Explainer

Azure Machine Learning Interpretability SDK には、2 セットの Explainer が導入されています。Direct Explainer と Meta Explainer です。

__Direct Explainer__ は統合されたライブラリからのものです。 SDK はすべての Explainer をラップし、共通の API と出力形式が公開されるようにします。 以下は、SDK で使用可能な Direct Explainer の一覧です。

> [!TIP]
> これらの Explainer を直接使用する方がよい場合は、共通の API と出力形式を使用する代わりに直接呼び出すことができます。

* **Tree Explainer**: SHAP の Tree Explainer は、ツリーおよびツリーのアンサンブルに固有の多項式時間高速 SHAP 値推定アルゴリズムに注目します
* **Deep Explainer**: SHAP による説明に基づくと、Deep Explainer は "SHAP NIPS の論文で説明されている DeepLIFT との接続上に構築されているディープ ラーニング モデルでの SHAP 値に対する高速近似アルゴリズムです。 TensorFlow モデルおよび TensorFlow バックエンドを使用する Keras モデルがサポートされています (PyTorch の暫定サポートもあります)"。
* **Kernel Explainer**: SHAP の Kernel Explainer は、特別に重み付けされたローカル線形回帰を使用して任意のモデルの SHAP 値を推定します。
* **Mimic Explainer**: Mimic Explainer は、グローバル サロゲート モデルのアイデアに基づいています。 グローバル サロゲート モデルは、ブラック ボックス モデルの予測をできる限り正確に近似するためにトレーニングされた本質的に解釈可能なモデルです。 データ サイエンティストは、サロゲート モデルを解釈してブラック ボックス モデルについての結論を導き出すことができます。
* **LIME Explainer**: LIME に基づく LIME Explainer は、最新の LIME (Local interpretable model-agnostic explanations) アルゴリズムを使用して、ローカル サロゲート モデルを作成します。 グローバル サロゲート モデルとは異なり、LIME はローカル サロゲート モデルをトレーニングして個別の予測を説明することに着目します。
* **HAN Text Explainer**: HAN Text Explainer は、特定のブラック ボックス テキスト モデルに対するテキスト データからモデルの説明を取得するために Hierarchical Attention Network を使用します。 特定の教師モデルの予測された出力で HAN サロゲート モデルをトレーニングします。 テキスト コーパス全体でグローバルにトレーニングした後、説明の正確性を向上させるため、特定のドキュメントに対する微調整ステップを追加しました。 HAN は、文と単語のアテンションに対する 2 つのアテンション レイヤーで双方向 RNN を使用します。 DNN が教師モデルでトレーニングされて、特定のドキュメントで微調整された後、アテンション レイヤーから単語の重要性を抽出できます。 テキスト データについては HAN は LIME や SHAP より正確ですが、トレーニング時間に関していっそうコストがかかります。 ただし、GloVe 単語埋め込みを使用してネットワークを初期化するオプションをユーザーに提供することで、トレーニング時間を改善しましたが、まだ時間がかかります。 HAN をリモート Azure GPU VM で実行することで、トレーニング時間を大幅に改善できます。 HAN の実装については、「Hierarchical Attention Networks for Document Classification (ドキュメントの分類に対する階層型アテンション ネットワーク)」 (Yang 他、2016) ([https://www.cs.cmu.edu/~diyiy/docs/naacl16.pdf](https://www.cs.cmu.edu/~diyiy/docs/naacl16.pdf)) で説明されています。

__Meta Explainer__ は、自動的に適切な Direct Explainer を選択し、特定のモデルとデータ セットに基づいて最適な説明情報を生成します。 Meta Explainer は、統合または開発したすべてのライブラリ (SHAP、LIME、Mimic など) を利用します。 以下は、SDK で使用可能な Meta Explainer です。

* **Tabular Explainer**: 表形式のデータセットで使用されます。
* **Text Explainer**: テキストのデータセットで使用されます。

Direct Explainer のメタ選択に加えて、Meta Explainer では、基になるライブラリ上に追加機能が開発され、Direct Explainer より速度とスケーラビリティが向上します。

現在、`TabularExplainer` では次のロジックを使用して Direct Explainer が呼び出されています。

1. ツリー ベースのモデルの場合、`TreeExplainer` を適用します
2. DNN モデルの場合、`DeepExplainer` を適用します
3. ブラック ボックス モデルとして扱い、次のものを適用します `KernelExplainer`

`TabularExplainer` に組み込まれたインテリジェンスは、より多くのライブラリが SDK に統合され、各 Explainer の長所と短所について学習することで、ますます高度なものになります。

`TabularExplainer` ではまた、Direct Explainer より大きな機能とパフォーマンスの強化も行われています。

* **初期化データセットの要約作成**。 説明の速度が最も重要な場合、初期化データセットを要約し、代表的なサンプルの小さなセットを生成します。これにより、グローバルとローカル両方の説明が高速化されます。
* **評価データ セットのサンプリング**。 ユーザーが評価サンプルの大きなセットを渡しても、実際にはそれらのすべてを評価する必要がない場合は、サンプリング パラメーターを true に設定し、グローバルな説明を高速化できます。

次の図では、Direct Explainer と Meta Explainer の 2 つのセットの間の関係を示します。

[![M機械学習解釈可能性のアーキテクチャ](./media/machine-learning-interpretability-explainability/interpretability-architecture.png)](./media/machine-learning-interpretability-explainability/interpretability-architecture.png#lightbox)

### <a name="models-supported"></a>サポートされているモデル

Python の `numpy.array`、`pandas.DataFrame`、`iml.datatypes.DenseData`、または `scipy.sparse.csr_matrix` 形式のデータセットでトレーニングされたすべてのモデルが、Machine Learning Interpretability SDK によってサポートされます。

説明関数では、モデルとパイプラインの両方が入力として受け付けられます。 モデルを提供する場合、モデルでは Scikit 規則に準拠する予測関数 `predict` または `predict_proba` が実装されている必要があります。 パイプライン (パイプライン スクリプトの名前) が提供された場合、説明関数では、パイプライン スクリプトの実行から予測が返るものと想定します。

### <a name="local-and-remote-compute-target"></a>ローカルとリモートのコンピューティング先

Machine Learning Interpretability SDK は、ローカルおよびリモート両方のコンピューティング先で動作するように設計されています。 ローカルで実行した場合、SDK 関数はどの Azure サービスにも接続されません。 Azure Machine Learning コンピューティング上で説明をリモートで実行し、説明情報を Azure Machine Learning 実行履歴サービスに記録できます。 この情報が記録されると、ユーザーによる分析のため、説明からのレポートや視覚化を、Azure Machine Learning ワークスペース ポータルですぐに使用できます。

## <a name="train-and-explain-locally"></a>ローカル環境でトレーニングと説明を行う

1. ローカル環境の Jupyter ノートブックでモデルをトレーニングします。 

    ``` python
    # load breast cancer dataset, a well-known small dataset that comes with scikit-learn
    from sklearn.datasets import load_breast_cancer
    from sklearn import svm
    from sklearn.model_selection import train_test_split
    breast_cancer_data = load_breast_cancer()
    classes = breast_cancer_data.target_names.tolist()
    # Split data into train and test
    from sklearn.model_selection import train_test_split
    x_train, x_test, y_train, y_test = train_test_split(breast_cancer_data.data, breast_cancer_data.target, test_size=0.2, random_state=0)
    clf = svm.SVC(gamma=0.001, C=100., probability=True)
    model = clf.fit(x_train, y_train)
    ```

2. Explainer を呼び出します。Explainer オブジェクトを開始するには、モデル、トレーニング データ、目的の機能 (省略可能)、出力クラス名 (分類の場合) を Explainer に渡す必要があります。 ローカルで [TabularExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.tabularexplainer?view=azure-ml-py)、[MimicExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.mimic.mimicexplainer?view=azure-ml-py)、`LimeExplainer` を使用して Explainer オブジェクトをインスタンス化する方法を次に示します。 `TabularExplainer` は下にある 3 つの Explainer (`TreeExplainer`、`DeepExplainer`、`KernelExplainer`) のいずれかを呼び出し、ユース ケース用に最適な 1 つを自動的に選択します。 ただし、基になっている 3 つの Explainer のそれぞれを直接呼び出すことができます。

    ```python
    from azureml.explain.model.tabular_explainer import TabularExplainer
    explainer = TabularExplainer(model, x_train, features=breast_cancer_data.feature_names, classes=classes)
    ```
    or
    ```python
    from azureml.explain.model.mimic.mimic_explainer import MimicExplainer
    from azureml.explain.model.mimic.models.lightgbm_model import LGBMExplainableModel
    explainer = MimicExplainer(model, x_train, LGBMExplainableModel, features=breast_cancer_data.feature_names, classes=classes)
    ```

3. グローバルな特徴の重要度の値を取得します。

    ```python
    # You can use the training data or the test data here. 
    global_explanation = explainer.explain_global(x_train)
    # Sorted feature importance values and feature names
    sorted_global_importance_values = global_explanation.get_ranked_global_values()
    sorted_global_importance_names = global_explanation.get_ranked_global_names()
    dict(zip(sorted_global_importance_names, sorted_global_importance_values))
    ```

4. ローカルな特徴の重要度の値: 次の関数呼び出しを使用して、個々のインスタンスまたはインスタンスのグループを説明します。

    ```python
    # explain the first data point in the test set
    local_explanation = explainer.explain_local(x_test[0,:])
    
    # sorted feature importance values and feature names
    sorted_local_importance_names = local_explanation.get_ranked_local_names()
    sorted_local_importance_values = local_explanation.get_ranked_local_values()
    ```
    or
    ```python
    # explain the first five data points in the test set
    local_explanation = explainer.explain_local(x_test[0:4,:])
    
    # sorted feature importance values and feature names
    sorted_local_importance_names = local_explanation.get_ranked_local_names()
    sorted_local_importance_values = local_explanation.get_ranked_local_values()
    ```

## <a name="train-and-explain-remotely"></a>リモート環境でトレーニングと説明を行う

Azure Machine Learning service でサポートされているさまざまなコンピューティング先でトレーニングできますが、このセクションの例では AMLCompute を使用してこれを行う方法を示します。

1. ローカルの Jupyter ノートブック (run_explainer.py など) でトレーニング スクリプトを作成します。

    ``` python  
    run = Run.get_context()
    client = ExplanationClient.from_run(run)
    
    # Train your model here

    # explain predictions on your local machine    
    explainer = TabularExplainer(model, x_train, features=breast_cancer_data.feature_names, classes=classes)
    # explain overall model predictions (global explanation)
    global_explanation = explainer.explain_global(x_test)
    # explain local data points (individual instances)
    local_explanation = explainer.explain_local(x_test[0,:])
    # upload global and local explanation objects to Run History
    upload_model_explanation(run, local_explanation, top_k=2, comment='local explanation: top 2 features')
    # Uploading global model explanation data for storage or visualization in webUX
    # The explanation can then be downloaded on any compute
    # Multiple explanations can be uploaded
    client.upload_model_explanation(global_explanation, comment='global explanation: all features')
    # Or you can only upload the explanation object with the top k feature info
    #client.upload_model_explanation(global_explanation, top_k=2, comment='global explanation: Only top 2 features')
    ```

2. コンピューティング先として Azure Machine Learning コンピューティングを設定し、トレーニングの実行を送信する方法について学習するには、「[モデル トレーニング用のコンピューティング ターゲットを設定する](how-to-set-up-training-targets.md#amlcompute)」の手順に従ってください。

3. ローカル環境の Jupyter Notebook で説明をダウンロードします。 
    > [!IMPORTANT]
    > contrib 内のものは完全にはサポートされていません。 実験的な機能が成熟すると、メインのパッケージに徐々に移動されます。

    ``` python
    from azureml.contrib.explain.model.explanation.explanation_client import ExplanationClient
    # Get model explanation data
    client = ExplanationClient.from_run(run)
    explanation = client.download_model_explanation()
    local_importance_values = explanation.local_importance_values
    expected_values = explanation.expected_values
    # Or you can use the saved run.id to retrive the feature importance values
    client = ExplanationClient.from_run_id(ws, experiment_name, run.id)
    explanation = client.download_model_explanation()
    local_importance_values = explanation.local_importance_values
    expected_values = explanation.expected_values
    # Get the top k (e.g., 4) most important features with their importance values
    explanation = client.download_model_explanation(top_k=4)
    global_importance_values = explanation.get_ranked_global_values()
    global_importance_names = explanation.get_ranked_global_names()
    print('global importance values: {}'.format(global_importance_values))
    print('global importance names: {}'.format(global_importance_names))
    ```

## <a name="next-steps"></a>次の手順

上記の手順が示されている Jupyter ノートブックのコレクションを見るには、「[Azure Machine Learning Interpretability sample notebooks (Azure Machine Learning Interpretability サンプル ノートブック)](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)」をご覧ください。