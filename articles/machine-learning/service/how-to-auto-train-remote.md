---
title: 自動 ML リモート コンピューティング ターゲット
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning リモート コンピューティング ターゲット上の自動機械学習と、Azure Machine Learning service を使用して、モデルを構築する方法について学習します
services: machine-learning
author: nacharya1
ms.author: nilesha
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 7/12/2019
ms.openlocfilehash: 3c3205b64803ac4ee67997ef546ffd64c89f23b4
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624826"
---
# <a name="train-models-with-automated-machine-learning-in-the-cloud"></a>クラウドで自動機械学習を使用してモデルをトレーニングする

Azure Machine Learning では、管理しているさまざまな種類のコンピューティング リソースに対してモデルをトレーニングします。 ローカル コンピューターまたはクラウド内のリソースをコンピューティング ターゲットにすることができます。

Azure Machine Learning Compute (AmlCompute) などのコンピューティング ターゲットを追加することで、機械学習実験を簡単にスケールアップまたはスケールアウトすることができます。 AmlCompute は、シングルノードまたはマルチノードのコンピューティングを簡単に作成できる、マネージド コンピューティング インフラストラクチャです。

この記事では、自動 ML と AmlCompute を使用してモデルを構築する方法について説明します。

## <a name="how-does-remote-differ-from-local"></a>リモートとローカルの違い

[自動機械学習を使用して分類モデルをトレーニングする方法](tutorial-auto-train-models.md)に関するチュートリアルでは、ローカル コンピューターで自動 ML を使用してモデルをトレーニングする方法について説明しています。 ローカルでトレーニングするときのワークフローは、リモート ターゲットの場合にも適用できます。 ただし、リモート コンピューティングの場合、自動 ML 実験の反復処理は非同期的に実行されます。 この機能では、特定の反復を取り消したり、実行の状態を確認したりできるほか、Jupyter ノートブックで他のセルに対する作業を続行することも可能です。 リモートからトレーニングするには、まず AmlCompute などのリモート コンピューティング ターゲットを作成します。 次に、リモート ソースを構成して、そこにご自身のコードを送信します。

この記事では、リモートの AmlCompute ターゲットで自動 ML 実験を実行するために必要な追加の手順について説明します。 チュートリアルのワークスペース オブジェクト `ws` は、このコード全体で使用されています。

```python
ws = Workspace.from_config()
```

## <a name="create-resource"></a>リソースを作成する

まだ存在しない場合は、ワークスペース (`ws`) に AmlCompute ターゲットを作成します。

**推定所要時間**: AmlCompute ターゲットの作成には約 5 分かかります。

```python
from azureml.core.compute import AmlCompute
from azureml.core.compute import ComputeTarget

amlcompute_cluster_name = "automlcl"  # Name your cluster
provisioning_config = AmlCompute.provisioning_configuration(vm_size="STANDARD_D2_V2",
                                                            # for GPU, use "STANDARD_NC6"
                                                            # vm_priority = 'lowpriority', # optional
                                                            max_nodes=6)
compute_target = ComputeTarget.create(
    ws, amlcompute_cluster_name, provisioning_config)

# Can poll for a minimum number of nodes and for a specific timeout.
# If no min_node_count is provided, it will use the scale settings for the cluster.
compute_target.wait_for_completion(
    show_output=True, min_node_count=None, timeout_in_minutes=20)
```

リモート コンピューティング ターゲットとして `compute_target` オブジェクトを使用できるようになりました。

クラスター名には次の制限があります。
+ 64 文字未満にする必要があります。
+ 次の文字は使用できません。`\` ~ ! @ # $ % ^ & * ( ) = + _ [ ] { } \\\\ | ; : \' \\" , < > / ?.`

## <a name="access-data-using-tabulardataset-function"></a>TabularDataset 関数を使用してデータにアクセスする

X と y は `TabularDataset` として定義されています。これらは AutoMLConfig 内の自動化された ML に渡されます。 `from_delimited_files` により既定で `infer_column_types` は true に設定されます。これにより、列の型が自動的に推測されます。 

列の型を手動で設定する場合は、各列の型を手動で設定するように `set_column_types` 引数を設定できます。 次のコード サンプルでは、sklearn パッケージのデータが使用されています。

```python
# Create a project_folder if it doesn't exist
if not os.path.isdir('data'):
    os.mkdir('data')
    
if not os.path.exists(project_folder):
    os.makedirs(project_folder)

from sklearn import datasets
from azureml.core.dataset import Dataset
from scipy import sparse
import numpy as np
import pandas as pd

data_train = datasets.load_digits()

pd.DataFrame(data_train.data[100:,:]).to_csv("data/X_train.csv", index=False)
pd.DataFrame(data_train.target[100:]).to_csv("data/y_train.csv", index=False)

ds = ws.get_default_datastore()
ds.upload(src_dir='./data', target_path='digitsdata', overwrite=True, show_progress=True)

X = Dataset.Tabular.from_delimited_files(path=ds.path('digitsdata/X_train.csv'))
y = Dataset.Tabular.from_delimited_files(path=ds.path('digitsdata/y_train.csv'))

```

## <a name="create-run-configuration"></a>実行構成の作成

get_data.py スクリプトで依存関係を使用できるようにするには、定義された `CondaDependencies` で `RunConfiguration` オブジェクトを定義します。 `AutoMLConfig` で `run_configuration` パラメーターにこのオブジェクトを使用します。

```python
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies

run_config = RunConfiguration(framework="python")
run_config.target = compute_target
run_config.environment.docker.enabled = True
run_config.environment.docker.base_image = azureml.core.runconfig.DEFAULT_CPU_IMAGE

dependencies = CondaDependencies.create(
    pip_packages=["scikit-learn", "scipy", "numpy"])
run_config.environment.python.conda_dependencies = dependencies
```

この設計パターンのその他の例については、この[サンプル ノートブック](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/remote-amlcompute/auto-ml-remote-amlcompute.ipynb)を参照してください。

## <a name="configure-experiment"></a>実験を構成する
`AutoMLConfig` の設定を指定します  (全パラメーターの一覧と使用できる値については[こちら](how-to-configure-auto-train.md#configure-experiment)を参照してください)。

```python
from azureml.train.automl import AutoMLConfig
import time
import logging

automl_settings = {
    "name": "AutoML_Demo_Experiment_{0}".format(time.time()),
    "iteration_timeout_minutes": 10,
    "iterations": 20,
    "n_cross_validations": 5,
    "primary_metric": 'AUC_weighted',
    "preprocess": False,
    "max_concurrent_iterations": 10,
    "verbosity": logging.INFO
}

automl_config = AutoMLConfig(task='classification',
                             debug_log='automl_errors.log',
                             path=project_folder,
                             compute_target=compute_target,
                             run_configuration=run_config,
                             X = X,
                             y = y,
                             **automl_settings,
                             )
```

### <a name="enable-model-explanations"></a>モデル説明を有効にする

`AutoMLConfig` コンストラクターに、省略可能な `model_explainability` パラメーターを設定します。 また、モデル説明機能を使用するために、`X_valid` パラメーターとして検証データフレーム オブジェクトを渡す必要があります。

```python
automl_config = AutoMLConfig(task='classification',
                             debug_log='automl_errors.log',
                             path=project_folder,
                             compute_target=compute_target,
                             run_configuration=run_config,
                             X = X,
                             y = y,
                             **automl_settings,
                             model_explainability=True,
                             X_valid=X_test
                             )
```

## <a name="submit-training-experiment"></a>トレーニング実験を送信する

次に、自動的にハイパー パラメーターのアルゴリズムを選択し、モデルをトレーニングするように構成を送信します

```python
from azureml.core.experiment import Experiment
experiment = Experiment(ws, 'automl_remote')
remote_run = experiment.submit(automl_config, show_output=True)
```

次の例のような出力が表示されます。

    Running on remote compute: mydsvmParent Run ID: AutoML_015ffe76-c331-406d-9bfd-0fd42d8ab7f6
    ***********************************************************************************************
    ITERATION: The iteration being evaluated.
    PIPELINE:  A summary description of the pipeline being evaluated.
    DURATION: Time taken for the current iteration.
    METRIC: The result of computing score on the fitted pipeline.
    BEST: The best observed score thus far.
    ***********************************************************************************************

     ITERATION     PIPELINE                               DURATION                METRIC      BEST
             2      Standardize SGD classifier            0:02:36                  0.954     0.954
             7      Normalizer DT                         0:02:22                  0.161     0.954
             0      Scale MaxAbs 1 extra trees            0:02:45                  0.936     0.954
             4      Robust Scaler SGD classifier          0:02:24                  0.867     0.954
             1      Normalizer kNN                        0:02:44                  0.984     0.984
             9      Normalizer extra trees                0:03:15                  0.834     0.984
             5      Robust Scaler DT                      0:02:18                  0.736     0.984
             8      Standardize kNN                       0:02:05                  0.981     0.984
             6      Standardize SVM                       0:02:18                  0.984     0.984
            10      Scale MaxAbs 1 DT                     0:02:18                  0.077     0.984
            11      Standardize SGD classifier            0:02:24                  0.863     0.984
             3      Standardize gradient boosting         0:03:03                  0.971     0.984
            12      Robust Scaler logistic regression     0:02:32                  0.955     0.984
            14      Scale MaxAbs 1 SVM                    0:02:15                  0.989     0.989
            13      Scale MaxAbs 1 gradient boosting      0:02:15                  0.971     0.989
            15      Robust Scaler kNN                     0:02:28                  0.904     0.989
            17      Standardize kNN                       0:02:22                  0.974     0.989
            16      Scale 0/1 gradient boosting           0:02:18                  0.968     0.989
            18      Scale 0/1 extra trees                 0:02:18                  0.828     0.989
            19      Robust Scaler kNN                     0:02:32                  0.983     0.989


## <a name="explore-results"></a>結果を探索する

[トレーニングのチュートリアル](tutorial-auto-train-models.md#explore-the-results)で紹介したものと同じ [Jupyter ウィジェット](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py)を使用して、結果のグラフと表を表示することができます。

```python
from azureml.widgets import RunDetails
RunDetails(remote_run).show()
```

ウィジェットの静的画像を次に示します。  ノートブックで表内の任意の行をクリックすると、その実行の実行プロパティと出力ログが表示されます。   また、グラフの上のドロップダウンを使用して、各繰り返しについて使用できる各メトリックのグラフを表示することもできます。

![ウィジェットの表](./media/how-to-auto-train-remote/table.png)
![ウィジェットのプロット](./media/how-to-auto-train-remote/plot.png)

ウィジェットに表示される URL をクリックすると、個々の実行の詳細を表示し、探索することができます。  

Jupyter Notebook を使用していない場合は、実行自体の URL を表示できます。

```
remote_run.get_portal_url()
```

ご利用のワークスペースで同じ情報にアクセスすることができます。  これらの結果の詳細については、「[自動機械学習の結果について](how-to-understand-automated-ml.md)」を参照してください。

### <a name="view-logs"></a>ログを表示する。

`/tmp/azureml_run/{iterationid}/azureml-logs` にある DSVM 上のログを見つけます。

## <a name="explain"></a> 最適なモデル説明

モデル説明データを取得すると、モデルに関する詳細情報を表示して、バックエンドで実行されている機能に対して透明性を高めることができます。 この例では、最適なモデルに対してのみモデル説明を実行します。 パイプライン内のすべてのモデルに対して実行する場合は、実行時間が大幅に長くなります。 モデル説明情報には次が含まれます。

* shap_values:shap lib によって生成される説明情報。
* expected_values:X_train データのセットに適用されるモデルの予期される値。
* overall_summary:降順で並べ替えられるモデル レベル機能の重要度の値。
* overall_imp:overall_summary と同じ順序で並べ替えられる機能名。
* per_class_summary:降順で並べ替えられるクラス レベル機能の重要度の値。 分類の場合にのみ使用できます。
* per_class_imp:per_class_summary と同じ順序で並べ替えられる機能名。 分類の場合にのみ使用できます。

次のコードを使用すると、イテレーションから最適なパイプラインを選択できます。 `get_output` メソッドは、最適な実行と、最後の fit の呼び出しで適合したモデルを返します。

```python
best_run, fitted_model = remote_run.get_output()
```

`retrieve_model_explanation` 関数をインポートし、最適なモデル上で実行します。

```python
from azureml.train.automl.automlexplainer import retrieve_model_explanation

shap_values, expected_values, overall_summary, overall_imp, per_class_summary, per_class_imp = \
    retrieve_model_explanation(best_run)
```

確認する `best_run` 説明変数の結果を印刷します。

```python
print(overall_summary)
print(overall_imp)
print(per_class_summary)
print(per_class_imp)
```

`best_run` 説明概要変数を印刷すると、次の出力が得られます。

![モデル説明コンソール出力](./media/how-to-auto-train-remote/expl-print.png)

また、ウィジェット UI、およびワークスペース内の Azure portal 上の Web UI を介して、機能の重要度を視覚化することもできます。

![モデル説明 UI](./media/how-to-auto-train-remote/model-exp.png)

## <a name="example"></a>例

[how-to-use-azureml/automated-machine-learning/remote-amlcompute/auto-ml-remote-amlcompute.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/remote-amlcompute/auto-ml-remote-amlcompute.ipynb) ノートブックは、この記事の概念を示しています。

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>次の手順

[自動トレーニングの設定を構成する方法](how-to-configure-auto-train.md)について説明します。
