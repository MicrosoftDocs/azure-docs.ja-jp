---
title: 自動 ML リモート コンピューティング ターゲット
titleSuffix: Azure Machine Learning
description: Azure Machine Learning リモート コンピューティング先上の自動機械学習と、Azure Machine Learning を使用して、モデルを構築する方法について学習します
services: machine-learning
author: cartacioS
ms.author: sacartac
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.custom: how-to, automl
ms.date: 03/09/2020
ms.openlocfilehash: 402af581e0ca6de9936b78106840a4fe273069d8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102503455"
---
# <a name="train-models-with-automated-machine-learning-in-the-cloud"></a>クラウドで自動機械学習を使用してモデルをトレーニングする



Azure Machine Learning では、管理しているさまざまな種類のコンピューティング リソースに対してモデルをトレーニングします。 ローカル コンピューターまたはクラウド内のリソースをコンピューティング ターゲットにすることができます。

Azure Machine Learning Compute (AmlCompute) などのコンピューティング ターゲットを追加することで、機械学習実験を簡単にスケールアップまたはスケールアウトすることができます。 AmlCompute は、シングルノードまたはマルチノードのコンピューティングを簡単に作成できる、マネージド コンピューティング インフラストラクチャです。

この記事では、自動 ML と AmlCompute を使用してモデルを構築する方法について説明します。

## <a name="how-does-remote-differ-from-local"></a>リモートとローカルの違い

リモート コンピューティング ターゲットを使用する場合は、より多くの機能を使用できます。  詳細については、[ローカルとリモートのコンピューティング ターゲット](concept-automated-ml.md#local-remote)に関するページを参照してください。

[自動機械学習を使用して分類モデルをトレーニングする方法](tutorial-auto-train-models.md)に関するチュートリアルでは、ローカル コンピューターで自動 ML を使用してモデルをトレーニングする方法について説明しています。 ローカルでトレーニングするときのワークフローは、リモート ターゲットの場合にも適用できます。 リモートからトレーニングするには、まず AmlCompute などのリモート コンピューティング ターゲットを作成します。 次に、リモート ソースを構成して、そこにご自身のコードを送信します。

この記事では、リモートの AmlCompute ターゲットで自動 ML 実験を実行するために必要な追加の手順について説明します。 チュートリアルのワークスペース オブジェクト `ws` は、このコード全体で使用されています。

```python
ws = Workspace.from_config()
```

## <a name="create-resource"></a>リソースを作成する

まだ存在しない場合、[`AmlCompute`](/python/api/azureml-core/azureml.core.compute.amlcompute%28class%29) ターゲットをワークスペース (`ws`) に作成します。

**推定所要時間**: AmlCompute ターゲットの作成には約 5 分かかります。

```python
from azureml.core.compute import AmlCompute
from azureml.core.compute import ComputeTarget
import os

# choose a name for your cluster
compute_name = os.environ.get("AML_COMPUTE_CLUSTER_NAME", "cpu-cluster")
compute_min_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MIN_NODES", 0)
compute_max_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MAX_NODES", 4)

# This example uses CPU VM. For using GPU VM, set SKU to STANDARD_NC6
vm_size = os.environ.get("AML_COMPUTE_CLUSTER_SKU", "STANDARD_D2_V2")


if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('found compute target. just use it. ' + compute_name)
else:
    print('creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size = vm_size,
                                                                min_nodes = compute_min_nodes, 
                                                                max_nodes = compute_max_nodes)

    # create the cluster
    compute_target = ComputeTarget.create(ws, compute_name, provisioning_config)
    
    # can poll for a minimum number of nodes and for a specific timeout. 
    # if no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current AmlCompute status, use get_status()
    print(compute_target.get_status().serialize())
```

リモート コンピューティング ターゲットとして `compute_target` オブジェクトを使用できるようになりました。

クラスター名には次の制限があります。
+ 64 文字未満にする必要があります。
+ 次の文字は使用できません。`\` ~ ! @ # $ % ^ & * ( ) = + _ [ ] { } \\\\ | ; : \' \\" , < > / ?.`

## <a name="access-data-using-tabulardataset-function"></a>TabularDataset 関数を使用してデータにアクセスする

training_data ([`TabularDataset`](/python/api/azureml-core/azureml.data.tabulardataset) として) とラベルを定義しました。これらは、[`AutoMLConfig`](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) で 自動 ML に渡されます。 `TabularDataset` メソッド `from_delimited_files` は既定で `infer_column_types` を true に設定します。これにより、自動的に列の型が推測されます。 

列の型を手動で設定する場合は、各列の型を手動で設定するように `set_column_types` 引数を設定できます。 次のコード サンプルでは、sklearn パッケージのデータが使用されています。

```python
from sklearn import datasets
from azureml.core.dataset import Dataset
from scipy import sparse
import numpy as np
import pandas as pd
import os

# Create a project_folder if it doesn't exist
if not os.path.isdir('data'):
    os.mkdir('data')
    
if not os.path.exists('project_folder'):
    os.makedirs('project_folder')

X = pd.DataFrame(data_train.data[100:,:])
y = pd.DataFrame(data_train.target[100:])

# merge X and y
label = "digit"
X[label] = y

training_data = X

training_data.to_csv('data/digits.csv')
ds = ws.get_default_datastore()
ds.upload(src_dir='./data', target_path='digitsdata', overwrite=True, show_progress=True)

training_data = Dataset.Tabular.from_delimited_files(path=ds.path('digitsdata/digits.csv'))
```

## <a name="configure-experiment"></a>実験を構成する
`AutoMLConfig` の設定を指定します  (全パラメーターの一覧と使用できる値については[こちら](how-to-configure-auto-train.md#configure-experiment)を参照してください)。

```python
from azureml.train.automl import AutoMLConfig
import time
import logging

automl_settings = {
    "name": "AutoML_Demo_Experiment_{0}".format(time.time()),
    "experiment_timeout_minutes" : 20,
    "enable_early_stopping" : True,
    "iteration_timeout_minutes": 10,
    "n_cross_validations": 5,
    "primary_metric": 'AUC_weighted',
    "max_concurrent_iterations": 10,
}

automl_config = AutoMLConfig(task='classification',
                             debug_log='automl_errors.log',
                             path=project_folder,
                             compute_target=compute_target,
                             training_data=training_data,
                             label_column_name=label,
                             **automl_settings,
                             )
```

## <a name="submit-training-experiment"></a>トレーニング実験を送信する

次に、自動的にハイパー パラメーターのアルゴリズムを選択し、モデルをトレーニングするように構成を送信します

```python
from azureml.core.experiment import Experiment
experiment = Experiment(ws, 'Tutorial-automl-remote')
remote_run = experiment.submit(automl_config, show_output=True)
```

次の例のような出力が表示されます。

```output
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
```

## <a name="explore-results"></a>結果を探索する

[トレーニングのチュートリアル](tutorial-auto-train-models.md#explore-the-results)で紹介したものと同じ [Jupyter ウィジェット](/python/api/azureml-widgets/azureml.widgets)を使用して、結果のグラフと表を表示することができます。

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

ご利用のワークスペースで同じ情報にアクセスすることができます。  これらの結果の詳細については、[自動機械学習の結果の評価](how-to-understand-automated-ml.md)に関する記事を参照してください。

## <a name="example"></a>例

次の[ノートブック](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression/auto-ml-regression.ipynb)は、この記事の概念を示しています。

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>次のステップ

* [自動トレーニングの設定を構成する方法](how-to-configure-auto-train.md)について説明します。
* 自動 ML 実験でモデルの解釈機能を有効にする[方法](how-to-machine-learning-interpretability-automl.md)を確認します。