---
title: Scikit-learn モデルをトレーニングして登録する
titleSuffix: Azure Machine Learning service
description: この記事では、Azure Machine Learning service を使用して Scikit-learn モデルをトレーニングおよび登録する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.date: 06/30/2019
ms.custom: seodec18
ms.openlocfilehash: c9e983f7981c1155964617694d2cce86aba741b7
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840019"
---
# <a name="train-and-register-scikit-learn-models-at-scale-with-azure-machine-learning-service"></a>Azure Machine Learning service を使用して Scikit-learn モデルを大規模にトレーニングおよび登録する

この記事では、Azure Machine Learning service を使用して Scikit-learn モデルをトレーニングおよび登録する方法について説明します。 人気のある [Iris データセット](https://archive.ics.uci.edu/ml/datasets/iris)を使って、カスタム [scikit-learn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) クラスでアヤメの花の画像を分類します。

Scikit-learn は、機械学習でよく使われるオープンソースのコンピューティング フレームワークです。 Azure Machine Learning service を利用すると、エラスティック クラウド コンピューティング リソースを使用して、オープン ソースのトレーニング ジョブを迅速にスケールアウトできます。 トレーニングの実行、モデルのバージョン管理、モデルのデプロイなどを追跡することもできます。

Scikit-learn モデルをゼロから開発する場合でも、既存のモデルをクラウドに導入する場合でも、Azure Machine Learning service は運用環境対応モデルの構築に役立ちます。

## <a name="prerequisites"></a>前提条件

次のいずれかの環境で、このコードを実行してください。
 - Azure Machine Learning Notebook VM - ダウンロードとインストールが不要

    - [クラウドベースのノートブックによるクイック スタート](quickstart-run-cloud-notebook.md)に従って、SDK とサンプル リポジトリが事前に読み込まれた専用の Notebook サーバーを作成します。
    - Notebook サーバー上の samples フォルダーで、**how-to-use-azureml > training > train-hyperparameter-tune-deploy-with-sklearn** の順に選択してこのディレクトリに移動し、完了済みで展開済みのノートブックを見つけます。

 - 独自の Jupyter Notebook サーバー

    - [Azure Machine Learning SDK for Python をインストールする](setup-create-workspace.md#sdk)
    - [ワークスペース構成ファイルを作成する](setup-create-workspace.md#write-a-configuration-file)
    - [サンプル スクリプト ファイル `train_iris.py` をダウンロードする](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/train-hyperparameter-tune-deploy-with-sklearn)
    - このガイドの完成した [Jupyter Notebook バージョン](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-sklearn.ipynb)は、GitHub サンプル ページにもあります。 ノートブックには、インテリジェントなハイパーパラメーターのチューニングをカバーし、プライマリ メトリックによる最適なモデルを取得する、拡張セクションが含まれています。

## <a name="set-up-the-experiment"></a>実験を設定する

このセクションでは、必要な Python パッケージを読み込み、ワークスペースを初期化し、実験を作成し、トレーニング データとトレーニング スクリプトをアップロードすることで、トレーニング実験を設定します。

### <a name="import-packages"></a>パッケージをインポートする

まず、必要な Python ライブラリをインポートします。

```Python
import os
import urllib
import shutil
import azureml

from azureml.core import Experiment
from azureml.core import Workspace, Run

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>ワークスペースを初期化する

[Azure Machine Learning service ワークスペース](concept-workspace.md)は、本サービスの最上位レベルのリソースです。 作成されるすべての成果物を操作できる一元的な場所が用意されています。 Python SDK では、[`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) オブジェクトを作成することでワークスペースの成果物にアクセスできます。

[前提条件のセクション](#prerequisites)で作成した `config.json` ファイルからワークスペース オブジェクトを作成します。

```Python
ws = Workspace.from_config()
```

### <a name="create-an-experiment"></a>実験の作成

トレーニング スクリプトを保存するための実験とフォルダーを作成します。 この例では、"sklearn-iris" という実験を作成します。

```Python
project_folder = './sklearn-iris'
os.makedirs(project_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='sklearn-iris')
```

### <a name="upload-dataset-and-scripts"></a>データセットとスクリプトをアップロードする

[データストア](how-to-access-data.md)は、データをコンピューティング先にマウントまたはコピーすることでデータを格納およびアクセスできる場所です。 各ワークスペースには既定のデータストアがあります。 トレーニング中に簡単にアクセスできるように、データとトレーニング スクリプトをデータストアにアップロードします。

1. データ用のディレクトリを作成します。

    ```Python
    os.makedirs('./data/iris', exist_ok=True)
    ```

1. Iris データセットを既定のデータストアにアップロードします。

    ```Python
    ds = ws.get_default_datastore()
    ds.upload(src_dir='./data/iris', target_path='iris', overwrite=True, show_progress=True)
    ```

1. Scikit-learn のトレーニング スクリプト `train_iris.py` をアップロードします。

    ```Python
    shutil.copy('./train_iris.py', project_folder)
    ```

## <a name="create-or-get-a-compute-target"></a>コンピューティング先を作成または取得する

Scikit-learn ジョブを実行するためのコンピューティング先を作成します。 Scikit-learn では、単一ノードの CPU コンピューティングのみがサポートされてます。

次のコードでは、リモート トレーニング コンピューティング リソース用に Azure Machine Learning のマネージド コンピューティング (AmlCompute) が作成されます。 AmlCompute の作成には約 5 分かかります。 その名前の AmlCompute がワークスペースに既にある場合、このコードの作成プロセスはスキップされます。

```Python
cluster_name = "cpu-cluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2', 
                                                           max_nodes=4)

    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

コンピューティング先の詳細については、[コンピューティング先の概要](concept-compute-target.md)に関する記事を参照してください。

## <a name="create-a-scikit-learn-estimator"></a>Scikit-learn の見積もりツールを作成する

[Scikit-learn 見積もりツール](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)では、コンピューティング先で Scikit-learn トレーニング ジョブを開始する簡単な方法が提供されます。 それは [`SKLearn`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) クラスによって実装されます。このクラスを使って、単一ノードの CPU トレーニングをサポートできます。

トレーニング スクリプトを実行するために追加の pip パッケージまたは conda パッケージが必要な場合は、`pip_packages` および `conda_packages` 引数に名前を渡すことで、パッケージを結果の docker イメージにインストールできます。

```Python
from azureml.train.sklearn import SKLearn

script_params = {
    '--kernel': 'linear',
    '--penalty': 1.0,
}

estimator = SKLearn(source_directory=project_folder, 
                    script_params=script_params,
                    compute_target=compute_target,
                    entry_script='train_iris.py'
                    pip_packages=['joblib']
                   )
```

## <a name="submit-a-run"></a>実行を送信する

[実行オブジェクト](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py)には、ジョブの実行中および完了後の実行履歴へのインターフェイスが用意されています。

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

実行は、以下の段階を経て実施されます。

- **準備**:docker イメージは TensorFlow エスティメーターに従って作成されます。 イメージはワークスペースのコンテナー レジストリにアップロードされ、後で実行するためにキャッシュされます。 ログは実行履歴にもストリーミングされ、進行状況を監視するために表示することができます。

- **拡大縮小**:Batch AI クラスターで実行の実施に現在使用できる数よりも多くのノードが必要な場合、スケールアップが試行されます。

- **Running**: script フォルダー内のすべてのスクリプトがコンピューティング先にアップロードされ、データ ストアがマウントまたはコピーされ、entry_script が実行されます。 stdout および ./logs フォルダーの出力は実行履歴にストリーミングされ、実行を監視するために使用できます。

- **後処理**:実行の ./outputs フォルダーが実行履歴にコピーされます。

## <a name="save-and-register-the-model"></a>モデルを保存して登録する

モデルのトレーニングが終わったら、それをワークスペースに保存して登録できます。 モデルの登録を使用すると、モデルをワークスペースに格納し、バージョン管理して、[モデルの管理とデプロイ](concept-model-management-and-deployment.md)を簡単にすることができます。

モデルを保存するには、トレーニング スクリプト train_iris.py に次のコードを追加します。 

``` Python
import joblib

joblib.dump(svm_model_linear, 'model.joblib')
```

次のコードでワークスペースにモデルを登録します。

```Python
model = run.register_model(model_name='sklearn-iris', model_path='model.joblib')
```

## <a name="next-steps"></a>次の手順

この記事では、Azure Machine Learning service で Scikit-learn モデルをトレーニングして登録しました。

* モデルをデプロイする方法を学習するには、[モデルのデプロイ](how-to-deploy-and-where.md)に関する記事に進んでください。

* [ハイパーパラメーターを調整する](how-to-tune-hyperparameters.md)

* [トレーニング中に実行メトリクスを追跡する](how-to-track-experiments.md)