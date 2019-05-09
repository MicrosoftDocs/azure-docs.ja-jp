---
title: 自動 ML リモート コンピューティング ターゲット
titleSuffix: Azure Machine Learning service
description: Data Science Virtual Machine (DSVM) リモート コンピューティング ターゲット上の自動機械学習と、Azure Machine Learning サービスを使用して、モデルを構築する方法について学習します
services: machine-learning
author: nacharya1
ms.author: nilesha
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 6f2d71abeacee531b21a8276f621367dd39a39d9
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "58891669"
---
# <a name="train-models-with-automated-machine-learning-in-the-cloud"></a>クラウドで自動機械学習を使用してモデルをトレーニングする

Azure Machine Learning では、管理しているさまざまな種類のコンピューティング リソースに対してモデルをトレーニングします。 ローカル コンピューターまたはクラウド内のコンピューターをコンピューティング ターゲットにすることができます。

コンピューティング ターゲットをさらに追加して、機械学習実験を簡単にスケール アップまたはスケール アウトできます。 コンピューティング ターゲットのオプションには、Ubuntu ベースのデータ サイエンス仮想マシン (DSVM) または Azure Machine Learning コンピューティングが含まれます。 DSVM とは、データ サイエンス専用に構築された Microsoft の Azure クラウド上にあるカスタマイズされた VM イメージです。 多くの一般的なデータ サイエンスや他のツールが事前にインストールされ、事前に構成されています。  

この記事では、DSVM 上で ML を使用してモデルを構築する方法について説明します。

## <a name="how-does-remote-differ-from-local"></a>リモートとローカルの違い

[自動機械学習を使用して分類モデルをトレーニングする方法](tutorial-auto-train-models.md)に関するチュートリアルでは、ローカル コンピューターで自動 ML を使用してモデルをトレーニングする方法について説明しています。  ローカルでトレーニングするときのワークフローは、リモート ターゲットの場合にも適用できます。 ただし、リモート コンピューティングの場合、自動 ML 実験の反復処理は非同期的に実行されます。 この機能では、特定の反復を取り消したり、実行の状態を確認したりできるほか、Jupyter ノートブックで他のセルに対する作業を続行することも可能です。 リモートからトレーニングするには、まず Azure DSVM などのリモート コンピューティング ターゲットを作成します。  次に、リモート ソースを構成して、そこにご自身のコードを送信します。

この記事では、リモートの DSVM で自動 ML 実験を実行するために必要な追加の手順について説明します。  チュートリアルのワークスペース オブジェクト `ws` は、このコード全体で使用されています。

```python
ws = Workspace.from_config()
```

## <a name="create-resource"></a>リソースを作成する

DSVM がまだ存在しない場合は、ワークスペースに DSVM を作成します (`ws`)。 以前に作成された DSVM がある場合、このコードは作成プロセスをスキップし、既存のリソースの詳細を `dsvm_compute` オブジェクトに読み込みます。  

**推定所要時間**: VM の作成時間は約 5 分です。

```python
from azureml.core.compute import DsvmCompute

dsvm_name = 'mydsvm' #Name your DSVM
try:
    dsvm_compute = DsvmCompute(ws, dsvm_name)
    print('found existing dsvm.')
except:
    print('creating new dsvm.')
    # Below is using a VM of SKU Standard_D2_v2 which is 2 core machine. You can check Azure virtual machines documentation for additional SKUs of VMs.
    dsvm_config = DsvmCompute.provisioning_configuration(vm_size = "Standard_D2_v2")
    dsvm_compute = DsvmCompute.create(ws, name = dsvm_name, provisioning_configuration = dsvm_config)
    dsvm_compute.wait_for_completion(show_output = True)
```

リモート コンピューティング ターゲットとして `dsvm_compute` オブジェクトを使用できるようになりました。

DSVM の名前には次の制限があります。
+ 64 文字未満にする必要があります。  
+ 次の文字は使用できません。`\` ~ ! @ # $ % ^ & * ( ) = + _ [ ] { } \\\\ | ; : \' \\" , < > / ?.`

>[!Warning]
>Marketplace の購入資格に関するメッセージで作成が失敗する場合:
>    1. [Azure portal](https://portal.azure.com) に移動します
>    1. DSVM の作成を開始します 
>    1. [Want to create programmatically]\(プログラムで作成する\) を選択してプログラムによる作成を有効にします
>    1. VM を実際に作成せずに終了します
>    1. 作成コードを再実行します

このコードでは、プロビジョニングされた DSVM に対してユーザー名またはパスワードが作成されません。 VM に直接接続する必要がある場合は、[Azure portal](https://portal.azure.com) に移動して資格情報を作成します。  

### <a name="attach-existing-linux-dsvm"></a>既存の Linux DSVM をアタッチする

既存の Linux DSVM をコンピューティング ターゲットとしてアタッチすることもできます。 この例では、既存 DSVM を利用しますが、新しいリソースは作成しません。

> [!NOTE]
>
> 次のコードでは、[RemoteCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.remote.remotecompute?view=azure-ml-py) ターゲット クラスを使用して、既存の VM をコンピューティング ターゲットとしてアタッチします。
> [DsvmCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.dsvmcompute?view=azure-ml-py) クラスは、この設計パターンを優先して今後のリリースで非推奨になります。

次のコードを実行して、既存の Linux DSVM からコンピューティング ターゲットを作成します。

```python
from azureml.core.compute import ComputeTarget, RemoteCompute 

attach_config = RemoteCompute.attach_configuration(username='<username>',
                                                   address='<ip_address_or_fqdn>',
                                                   ssh_port=22,
                                                   private_key_file='./.ssh/id_rsa')
compute_target = ComputeTarget.attach(workspace=ws,
                                      name='attached-vm',
                                      attach_configuration=attach_config)

compute_target.wait_for_completion(show_output=True)
```

リモート コンピューティング ターゲットとして `compute_target` オブジェクトを使用できるようになりました。

## <a name="access-data-using-getdata-file"></a>get_data ファイルを使用してデータにアクセスする

トレーニング データに対するリモート リソースのアクセスを用意します。 リモート コンピューティングで実行される自動機械学習実験の場合、`get_data()` 関数を使用してデータを取得する必要があります。  

アクセスを用意するには、次の手順を行う必要があります。
+ `get_data()` 関数を含む get_data.py ファイルを作成します 
+ 絶対パスとしてアクセス可能なディレクトリに、そのファイルを配置します 

BLOB ストレージまたはローカル ディスクからデータを読み取るコードを get_data.py ファイルにカプセル化することができます。 次のコード サンプルでは、sklearn パッケージのデータが使用されています。

>[!Warning]
>リモート コンピューティングを使用している場合は、`get_data()` を使用する必要があります。お使いのデータの変換はここで実行されます。 get_data() の一部としてデータ変換用の追加ライブラリをインストールする場合は、追加の手順に従う必要があります。 詳細については、[auto-ml-dataprep のサンプル ノートブック](https://aka.ms/aml-auto-ml-data-prep )を参照してください。


```python
# Create a project_folder if it doesn't exist
if not os.path.exists(project_folder):
    os.makedirs(project_folder)

#Write the get_data file.
%%writefile $project_folder/get_data.py

from sklearn import datasets
from scipy import sparse
import numpy as np

def get_data():
    
    digits = datasets.load_digits()
    X_digits = digits.data[10:,:]
    y_digits = digits.target[10:]

    return { "X" : X_digits, "y" : y_digits }
```

## <a name="configure-experiment"></a>実験を構成する

`AutoMLConfig` の設定を指定します  (全パラメーターの一覧と使用できる値については[こちら](how-to-configure-auto-train.md#configure-experiment)を参照してください)。

この設定で、`run_configuration` は `run_config` オブジェクトに設定されます。このオブジェクトには、DSVM の設定と構成が含まれています。  

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
                             compute_target = dsvm_compute,
                             data_script=project_folder + "/get_data.py",
                             **automl_settings,
                            )
```

### <a name="enable-model-explanations"></a>モデル説明を有効にする

`AutoMLConfig` コンストラクターに、省略可能な `model_explainability` パラメーターを設定します。 また、モデル説明機能を使用するために、`X_valid` パラメーターとして検証データフレーム オブジェクトを渡す必要があります。

```python
automl_config = AutoMLConfig(task='classification',
                             debug_log='automl_errors.log',
                             path=project_folder,
                             compute_target = dsvm_compute,
                             data_script=project_folder + "/get_data.py",
                             **automl_settings,
                             model_explainability=True,
                             X_valid = X_test
                            )
```

## <a name="submit-training-experiment"></a>トレーニング実験を送信する

次に、自動的にハイパー パラメーターのアルゴリズムを選択し、モデルをトレーニングするように構成を送信します

```python
from azureml.core.experiment import Experiment
experiment=Experiment(ws, 'automl_remote')
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

[トレーニングのチュートリアル](tutorial-auto-train-models.md#explore-the-results)と同じ Jupyter ウィジェットを使用して、結果のグラフと表を表示することができます。

```python
from azureml.widgets import RunDetails
RunDetails(remote_run).show()
```
ウィジェットの静的画像を次に示します。  ノートブックで表内の任意の行をクリックすると、その実行の実行プロパティと出力ログが表示されます。   また、グラフの上のドロップダウンを使用して、各繰り返しについて使用できる各メトリックのグラフを表示することもできます。

![ウィジェットの表](./media/how-to-auto-train-remote/table.png)
![ウィジェットのプロット](./media/how-to-auto-train-remote/plot.png)

ウィジェットに表示される URL をクリックすると、個々の実行の詳細を表示し、探索することができます。
 
### <a name="view-logs"></a>ログを表示する。

`/tmp/azureml_run/{iterationid}/azureml-logs` にある DSVM 上のログを見つけます。

## <a name="best-model-explanation"></a>最適なモデル説明

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

[how-to-use-azureml/automated-machine-learning/remote-execution/auto-ml-remote-execution.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/remote-execution/auto-ml-remote-execution.ipynb) ノートブックは、この記事の概念を示しています。 

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>次の手順

[自動トレーニングの設定を構成する方法](how-to-configure-auto-train.md)について説明します。
