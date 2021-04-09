---
title: Python で ML モデルの公平性を評価する (プレビュー)
titleSuffix: Azure Machine Learning
description: Fairlearn と Azure Machine Learning Python SDK を使用して、機械学習モデルの公平性を評価し、軽減する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: mesameki
author: mesameki
ms.reviewer: luquinta
ms.date: 11/16/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, responsible-ml
ms.openlocfilehash: 322b036fee840db58ed610795155af6c9e1320cc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100366971"
---
# <a name="use-azure-machine-learning-with-the-fairlearn-open-source-package-to-assess-the-fairness-of-ml-models-preview"></a>Azure Machine Learning と Fairlearn オープンソース パッケージを使用して ML モデルの公平性を評価する (プレビュー)

この攻略ガイドでは、Azure Machine Learning と共に [Fairlearn](https://fairlearn.github.io/) オープンソース Python パッケージを使用して、次のタスクを実行する方法について学習します。

* モデルの予測の公平性を評価します。 機械学習における公平性について詳しくは、[機械学習における公平性に関する記事](concept-fairness-ml.md)をご覧ください。
* Azure Machine Learning Studio を使用して、公平性の評価に関する分析情報のアップロード、一覧表示、ダウンロードを行います。
* Azure Machine Learning Studio の公平性の評価ダッシュボードを参照して、モデルの公平性に関する分析情報を操作します。

>[!NOTE]
> 公平性の評価は純粋に技術的な行為ではありません。 **このパッケージは機械学習モデルの公平性を評価するのに役立ちますが、モデルの実行方法に関する構成と決定を行うことができるのは、お客様のみです。**  このパッケージは公平性を評価するための定量的なメトリックを特定するのに役立ちますが、機械学習モデルの開発者は、独自のモデルの公平性を評価するために、定性分析を実行する必要もあります。

## <a name="azure-machine-learning-fairness-sdk"></a>Azure Machine Learning の公平性 SDK 

Azure Machine Learning の公平性 SDK `azureml-contrib-fairness` では、オープンソースの Python パッケージ [Fairlearn](http://fairlearn.github.io) が Azure Machine Learning 内に統合されます。 Azure Machine Learning 内での Fairlearn の統合について詳しくは、これらの[サンプル ノートブック](https://github.com/Azure/MachineLearningNotebooks/tree/master/contrib/fairness)をご覧ください。 Fairlearn の詳細については、[サンプル ガイド](https://fairlearn.github.io/master/auto_examples/)と[サンプル ノートブック](https://github.com/fairlearn/fairlearn/tree/master/notebooks)を参照してください。 

次のコマンドを使用して、`azureml-contrib-fairness` および `fairlearn` パッケージをインストールします。
```bash
pip install azureml-contrib-fairness
pip install fairlearn==0.4.6
```
新しいバージョンの Fairlearn は、次のコード例でも動作します。



## <a name="upload-fairness-insights-for-a-single-model"></a>1 つのモデルの公平性に関する分析情報をアップロードする

次の例は公平性パッケージの使用方法を示しています。 モデルの公平性に関する分析情報を Azure Machine Learning にアップロードし、Azure Machine Learning スタジオで公平性の評価ダッシュボードを表示します。

1. Jupyter Notebook でサンプル モデルをトレーニングします。 

    データセットについては、よく知られている成人国勢調査のデータセットを使用します。これは OpenML からフェッチします。 個人が過去にローンを返済したかどうかを示すラベルに関するローン決定問題があるとします。 これまでに確認されていない個人がローンを返済するかどうかを予測するモデルをトレーニングします。 このようなモデルは、ローンの決定に使用することができます。

    ```python
    import copy
    import numpy as np
    import pandas as pd

    from sklearn.compose import ColumnTransformer
    from sklearn.datasets import fetch_openml
    from sklearn.impute import SimpleImputer
    from sklearn.linear_model import LogisticRegression
    from sklearn.model_selection import train_test_split
    from sklearn.preprocessing import StandardScaler, OneHotEncoder
    from sklearn.compose import make_column_selector as selector
    from sklearn.pipeline import Pipeline
    
    from fairlearn.widget import FairlearnDashboard

    # Load the census dataset
    data = fetch_openml(data_id=1590, as_frame=True)
    X_raw = data.data
    y = (data.target == ">50K") * 1
    
    # (Optional) Separate the "sex" and "race" sensitive features out and drop them from the main data prior to training your model
    X_raw = data.data
    y = (data.target == ">50K") * 1
    A = X_raw[["race", "sex"]]
    X = X_raw.drop(labels=['sex', 'race'],axis = 1)
    
    # Split the data in "train" and "test" sets
    (X_train, X_test, y_train, y_test, A_train, A_test) = train_test_split(
        X_raw, y, A, test_size=0.3, random_state=12345, stratify=y
    )

    # Ensure indices are aligned between X, y and A,
    # after all the slicing and splitting of DataFrames
    # and Series
    X_train = X_train.reset_index(drop=True)
    X_test = X_test.reset_index(drop=True)
    y_train = y_train.reset_index(drop=True)
    y_test = y_test.reset_index(drop=True)
    A_train = A_train.reset_index(drop=True)
    A_test = A_test.reset_index(drop=True)

    # Define a processing pipeline. This happens after the split to avoid data leakage
    numeric_transformer = Pipeline(
        steps=[
            ("impute", SimpleImputer()),
            ("scaler", StandardScaler()),
        ]
    )
    categorical_transformer = Pipeline(
        [
            ("impute", SimpleImputer(strategy="most_frequent")),
            ("ohe", OneHotEncoder(handle_unknown="ignore")),
        ]
    )
    preprocessor = ColumnTransformer(
        transformers=[
            ("num", numeric_transformer, selector(dtype_exclude="category")),
            ("cat", categorical_transformer, selector(dtype_include="category")),
        ]
    )

    # Put an estimator onto the end of the pipeline
    lr_predictor = Pipeline(
        steps=[
            ("preprocessor", copy.deepcopy(preprocessor)),
            (
                "classifier",
                LogisticRegression(solver="liblinear", fit_intercept=True),
            ),
        ]
    )

    # Train the model on the test data
    lr_predictor.fit(X_train, y_train)

    # (Optional) View this model in Fairlearn's fairness dashboard, and see the disparities which appear:
    from fairlearn.widget import FairlearnDashboard
    FairlearnDashboard(sensitive_features=A_test, 
                       sensitive_feature_names=['Race', 'Sex'],
                       y_true=y_test,
                       y_pred={"lr_model": lr_predictor.predict(X_test)})
    ```

2. Azure Machine Learning にログインして、モデルを登録します。
   
    公平性ダッシュボードは、登録されたモデルまたは登録されていないモデルと統合できます。 次の手順に従って Azure Machine Learning にモデルを登録します。
    ```python
    from azureml.core import Workspace, Experiment, Model
    import joblib
    import os
    
    ws = Workspace.from_config()
    ws.get_details()

    os.makedirs('models', exist_ok=True)
    
    # Function to register models into Azure Machine Learning
    def register_model(name, model):
        print("Registering ", name)
        model_path = "models/{0}.pkl".format(name)
        joblib.dump(value=model, filename=model_path)
        registered_model = Model.register(model_path=model_path,
                                        model_name=name,
                                        workspace=ws)
        print("Registered ", registered_model.id)
        return registered_model.id

    # Call the register_model function 
    lr_reg_id = register_model("fairness_logistic_regression", lr_predictor)
    ```

3. 公平性のメトリックを事前計算します。

    Fairlearn の `metrics` パッケージを使用して、ダッシュボード ディクショナリを作成します。 `_create_group_metric_set` メソッドには Dashboard コンストラクターと同様の引数があります。ただし、(名前を使用できるように) センシティブ特徴がディクショナリとして渡される点が異なります。 また、このメソッドを呼び出すときに、予測の種類 (この場合は二項分類) も指定する必要があります。

    ```python
    #  Create a dictionary of model(s) you want to assess for fairness 
    sf = { 'Race': A_test.race, 'Sex': A_test.sex}
    ys_pred = { lr_reg_id:lr_predictor.predict(X_test) }
    from fairlearn.metrics._group_metric_set import _create_group_metric_set

    dash_dict = _create_group_metric_set(y_true=y_test,
                                        predictions=ys_pred,
                                        sensitive_features=sf,
                                        prediction_type='binary_classification')
    ```
4. 事前計算済み公平性メトリックをアップロードします。
    
    次に、`azureml.contrib.fairness` パッケージをインポートしてアップロードを実行します。

    ```python
    from azureml.contrib.fairness import upload_dashboard_dictionary, download_dashboard_by_upload_id
    ```
    Experiment を作成し、Run を作成して、ダッシュボードをアップロードします。
    ```python
    exp = Experiment(ws, "Test_Fairness_Census_Demo")
    print(exp)

    run = exp.start_logging()

    # Upload the dashboard to Azure Machine Learning
    try:
        dashboard_title = "Fairness insights of Logistic Regression Classifier"
        # Set validate_model_ids parameter of upload_dashboard_dictionary to False if you have not registered your model(s)
        upload_id = upload_dashboard_dictionary(run,
                                                dash_dict,
                                                dashboard_name=dashboard_title)
        print("\nUploaded to id: {0}\n".format(upload_id))
        
        # To test the dashboard, you can download it back and ensure it contains the right information
        downloaded_dict = download_dashboard_by_upload_id(run, upload_id)
    finally:
        run.complete()
    ```
5. Azure Machine Learning Studio から公平性ダッシュボードを確認します

    前の手順 (生成された公平性に関する分析情報を Azure Machine Learning にアップロードする) を完了したら、[Azure Machine Learning Studio](https://ml.azure.com) で公平性ダッシュボードを表示できます。 このダッシュボードは Fairlearn で提供されているのと同じ視覚化ダッシュボードであり、センシティブ特徴のサブグループ (例: 男性と女性など) 間の格差を分析することができます。
    Azure Machine Learning Studio の視覚化ダッシュボードにアクセスするには、次のパスのいずれかに従います。

    * **[実験] ペイン (プレビュー)**
    1. 左側のウィンドウの **[実験]** を選択して、Azure Machine Learning で実行した実験の一覧を表示します。
    1. 特定の実験を選択して、その実験内のすべての実行を表示します。
    1. 実行を選択してから **[Fairness]\(公平性\)** タブを選択して、説明の視覚化ダッシュボードを表示します。
    1. **[公平性]** タブが表示されたら、右側のメニューで **公平性 ID** をクリックします。
    1. 公平性の評価ページで、重要な属性、パフォーマンス メトリック、および対象となる公平性メトリックを選択して、ダッシュボードを構成します。
    1. グラフの種類を別の種類に切り替えて、**割り当て** の害と **サービス品質** の害の両方を観察します。



    [![公平性ダッシュボードの割り当て](./media/how-to-machine-learning-fairness-aml/dashboard-1.png)](./media/how-to-machine-learning-fairness-aml/dashboard-1.png#lightbox)
    
    [![公平性ダッシュボードのサービス品質](./media/how-to-machine-learning-fairness-aml/dashboard-2.png)](./media/how-to-machine-learning-fairness-aml/dashboard-2.png#lightbox)
    * **[モデル] ペイン**
    1. 前の手順に従って元のモデルを登録した場合は、左側のペインの **[モデル]** を選択してそれを表示できます。
    1. モデルを選択してから **[Fairness]\(公平性\)** タブを選択して、説明の視覚化ダッシュボードを表示します。

    視覚化ダッシュボードとその内容の詳細については、Fairlearn の[ユーザー ガイド](https://fairlearn.github.io/master/user_guide/assessment.html#fairlearn-dashboard)を参照してください。

## <a name="upload-fairness-insights-for-multiple-models"></a>複数のモデルの公平性に関する分析情報をアップロードする

複数のモデルを比較し、公平性の評価がどのように異なるかを確認するには、複数のモデルを視覚化ダッシュボードに渡して、そのパフォーマンスと公平性のトレードオフを比較します。

1. モデルをトレーニングします。
    
    サポート ベクター マシンの推定器に基づいて 2 番目の分類子を作成し、Fairlearn の `metrics` パッケージを使用して公平性ダッシュボード ディクショナリをアップロードします。 以前にトレーニングされたモデルが引き続き使用できることを前提としています。


    ```python
    # Put an SVM predictor onto the preprocessing pipeline
    from sklearn import svm
    svm_predictor = Pipeline(
        steps=[
            ("preprocessor", copy.deepcopy(preprocessor)),
            (
                "classifier",
                svm.SVC(),
            ),
        ]
    )

    # Train your second classification model
    svm_predictor.fit(X_train, y_train)
    ```

2. モデルを登録します

    次に、両方のモデルを Azure Machine Learning 内に登録します。 便宜上、結果をディクショナリに格納します。これにより、登録されたモデルの `id` (`name:version` 形式の文字列) が予測因子自体にマップされます。

    ```python
    model_dict = {}

    lr_reg_id = register_model("fairness_logistic_regression", lr_predictor)
    model_dict[lr_reg_id] = lr_predictor

    svm_reg_id = register_model("fairness_svm", svm_predictor)
    model_dict[svm_reg_id] = svm_predictor
    ```

3. Fairlearn ダッシュボードをローカルに読み込みます

    公平性に関する分析情報を Azure Machine Learning にアップロードする前に、ローカルに呼び出した Fairlearn ダッシュボードでこれらの予測を調べることができます。 



    ```python
    #  Generate models' predictions and load the fairness dashboard locally 
    ys_pred = {}
    for n, p in model_dict.items():
        ys_pred[n] = p.predict(X_test)

    from fairlearn.widget import FairlearnDashboard

    FairlearnDashboard(sensitive_features=A_test, 
                    sensitive_feature_names=['Race', 'Sex'],
                    y_true=y_test.tolist(),
                    y_pred=ys_pred)
    ```

3. 公平性のメトリックを事前計算します。

    Fairlearn の `metrics` パッケージを使用して、ダッシュボード ディクショナリを作成します。

    ```python
    sf = { 'Race': A_test.race, 'Sex': A_test.sex }

    from fairlearn.metrics._group_metric_set import _create_group_metric_set

    dash_dict = _create_group_metric_set(y_true=Y_test,
                                        predictions=ys_pred,
                                        sensitive_features=sf,
                                        prediction_type='binary_classification')
    ```
4. 事前計算済み公平性メトリックをアップロードします。
    
    次に、`azureml.contrib.fairness` パッケージをインポートしてアップロードを実行します。

    ```python
    from azureml.contrib.fairness import upload_dashboard_dictionary, download_dashboard_by_upload_id
    ```
    Experiment を作成し、Run を作成して、ダッシュボードをアップロードします。
    ```python
    exp = Experiment(ws, "Compare_Two_Models_Fairness_Census_Demo")
    print(exp)

    run = exp.start_logging()

    # Upload the dashboard to Azure Machine Learning
    try:
        dashboard_title = "Fairness Assessment of Logistic Regression and SVM Classifiers"
        # Set validate_model_ids parameter of upload_dashboard_dictionary to False if you have not registered your model(s)
        upload_id = upload_dashboard_dictionary(run,
                                                dash_dict,
                                                dashboard_name=dashboard_title)
        print("\nUploaded to id: {0}\n".format(upload_id))
        
        # To test the dashboard, you can download it back and ensure it contains the right information
        downloaded_dict = download_dashboard_by_upload_id(run, upload_id)
    finally:
        run.complete()
    ```


    前のセクションと同様に、Azure Machine Learning Studio で前述のパスのいずれか ( **[実験]** または **[モデル]** 経由) に従って視覚化ダッシュボードにアクセスし、公平性とパフォーマンスの観点から 2 つのモデルを比較することができます。


## <a name="upload-unmitigated-and-mitigated-fairness-insights"></a>軽減されていない公平性と軽減された公平性に関する分析情報をアップロードする

Fairlearn の[軽減アルゴリズム](https://fairlearn.github.io/master/user_guide/mitigation.html)を使用して、生成された軽減済みモデルを元の軽減されていないモデルと比較し、比較したモデル間でのパフォーマンスと公平性のトレードオフをナビゲートできます。

[グリッド サーチ](https://fairlearn.github.io/master/user_guide/mitigation.html#grid-search)軽減アルゴリズムの使用方法が示された例 (異なる公平性とパフォーマンスのトレードオフを持つ軽減されたモデルのコレクションが作成されます) を確認するには、こちらの[サンプル ノートブック](https://github.com/Azure/MachineLearningNotebooks/blob/master/contrib/fairness/fairlearn-azureml-mitigation.ipynb)を参照してください。 

複数のモデルの公平性に関する分析情報を 1 回の Run でアップロードすると、公平性とパフォーマンスに関してモデルを比較できます。 モデルの比較グラフに表示されるいずれかのモデルをクリックすると、特定のモデルの公平性に関する詳細な分析情報を確認できます。


[![モデルの比較 Fairlearn ダッシュボード](./media/how-to-machine-learning-fairness-aml/multi-model-dashboard.png)](./media/how-to-machine-learning-fairness-aml/multi-model-dashboard.png#lightbox)
    

## <a name="next-steps"></a>次のステップ

[モデルの公平性に関する詳細情報](concept-fairness-ml.md)

[Azure Machine Learning の公平性サンプル ノートブックを確認する](https://github.com/Azure/MachineLearningNotebooks/tree/master/contrib/fairness)
