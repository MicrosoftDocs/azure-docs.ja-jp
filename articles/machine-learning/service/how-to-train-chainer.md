---
title: Chainer モデルのトレーニングと登録
titleSuffix: Azure Machine Learning service
description: この記事では、Azure Machine Learning service を使用して Chainer モデルをトレーニングし、登録する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.reviewer: sdgilley
ms.date: 06/15/2019
ms.openlocfilehash: 8ecefccbdf5f02652e935858b6ae8fb4cdfde640
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840035"
---
# <a name="train-and-register-chainer-models-at-scale-with-azure-machine-learning-service"></a>Azure Machine Learning service を使用して Chainer モデルを大規模にトレーニングし、登録する

この記事では、Azure Machine Learning service を使用して Chainer モデルをトレーニングし、登録する方法について説明します。 人気の [MNIST データセット](http://yann.lecun.com/exdb/mnist/)を使用し、[numpy](https://www.numpy.org/) を基盤に [Chainer Python ライブラリ](https://Chainer.org)を使用して構築されたディープ ニューラル ネットワーク (DNN) を使用して手書きの数字を分類します。

Chainer は、他の人気 DNN フレームワークを基盤に実行することで開発を簡素化できるハイレベル ニューラル ネットワーク API です。 Azure Machine Learning service を利用すると、エラスティック クラウド コンピューティング リソースを使用し、トレーニング ジョブを迅速にスケールアウトできます。 トレーニングの実行、モデルのバージョン管理、モデルのデプロイなどを追跡することもできます。

Chainer モデルをゼロから開発する場合でも、既存のモデルをクラウドに導入する場合でも、Azure Machine Learning service は運用環境対応モデルの構築に役立ちます。

Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning service](https://aka.ms/AMLFree) を今日からお試しいただけます。

## <a name="prerequisites"></a>前提条件

次のいずれかの環境で、このコードを実行してください。

- Azure Machine Learning Notebook VM - ダウンロードとインストールが不要

    - [クラウドベースのノートブックによるクイック スタート](quickstart-run-cloud-notebook.md)に従って、SDK とサンプル リポジトリが事前に読み込まれた専用の Notebook サーバーを作成します。
    - Notebook サーバー上の samples フォルダー内で、**how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer** フォルダーから完了済みのノートブックとファイルを見つけます。  このノートブックには、インテリジェント ハイパーパラメーター チューニング、モデル デプロイ、ノートブック ウィジェットを取り上げた拡張セクションがあります。

- 独自の Jupyter Notebook サーバー

    - [Azure Machine Learning SDK for Python をインストールする](setup-create-workspace.md#sdk)
    - [ワークスペース構成ファイルを作成する](setup-create-workspace.md#write-a-configuration-file)
    - サンプル スクリプト ファイル [chainer_mnist.py](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer/chainer_mnist.py) をダウンロードする
     - GitHub サンプル ページにも、完全な [Jupyter Notebook バージョン](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb)があります。 このノートブックには、インテリジェント ハイパーパラメーター チューニング、モデル デプロイ、ノートブック ウィジェットを取り上げた拡張セクションがあります。

## <a name="set-up-the-experiment"></a>実験を設定する

このセクションでは、必要な Python パッケージを読み込み、ワークスペースを初期化し、実験を作成し、トレーニング データとトレーニング スクリプトをアップロードすることで、トレーニング実験を設定します。

### <a name="import-packages"></a>パッケージをインポートする

まず、azureml.core Python ライブラリをインポートし、バージョン番号を表示します。

```
# Check core SDK version number
import azureml.core

print("SDK version:", azureml.core.VERSION)
```

### <a name="initialize-a-workspace"></a>ワークスペースを初期化する

[Azure Machine Learning service ワークスペース](concept-workspace.md)は、本サービスの最上位レベルのリソースです。 作成されるすべての成果物を操作できる一元的な場所が用意されています。 Python SDK では、[`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) オブジェクトを作成することでワークスペースの成果物にアクセスできます。

[前提条件のセクション](#prerequisites)で作成した `config.json` ファイルからワークスペース オブジェクトを作成します。

```Python
ws = Workspace.from_config()
```

### <a name="create-a-project-directory"></a>プロジェクト ディレクトリを作成する
リモート リソースでアクセスを必要とする、ローカル コンピューターからの必要なコードがすべて含まれるディレクトリを作成します。 これには、トレーニング スクリプトと、トレーニング スクリプトが依存する追加ファイルが含まれます。

```
import os

project_folder = './chainer-mnist'
os.makedirs(project_folder, exist_ok=True)
```

### <a name="prepare-training-script"></a>トレーニング スクリプトを準備する

このチュートリアルでは、トレーニング スクリプトの **chainer_mnist.py** は既に用意されています。 実際には、コードを変更しなくても、あらゆるカスタム トレーニング スクリプトをそのまま Azure ML で実行できるはずです。

Azure ML の追跡およびメトリック機能を利用するには、トレーニング スクリプト内に少量の Azure ML コードを追加する必要があります。  トレーニング スクリプト **chainer_mnist.py** には、Azure ML 実行に一部のメトリックをログ記録する方法があります。 それを行うには、スクリプト内で Azure ML `Run` オブジェクトにアクセスします。

トレーニング スクリプト **chainer_mnist.py** をプロジェクト ディレクトリにコピーします。

```
import shutil

shutil.copy('chainer_mnist.py', project_folder)
```

### <a name="create-an-experiment"></a>実験の作成

トレーニング スクリプトを保存するための実験とフォルダーを作成します。 この例では、"chainer-mnist" という実験を作成します。

```
from azureml.core import Experiment

experiment_name = 'chainer-mnist'
experiment = Experiment(ws, name=experiment_name)
```


## <a name="create-or-get-a-compute-target"></a>コンピューティング先を作成または取得する

モデルをトレーニングするには[コンピューティング先](concept-compute-target.md)が必要です。 このチュートリアルでは、リモート トレーニング コンピューティング リソースに Azure ML マネージド コンピューティング (AmlCompute) を使用します。

**AmlCompute の作成には約 5 分かかります**。 その名前の AmlCompute がワークスペースに既にある場合、このコードの作成プロセスはスキップされます。  

```Python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# choose a name for your cluster
cluster_name = "gpu-cluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target.')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6', 
                                                           max_nodes=4)

    # create the cluster
    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True)

# use get_status() to get a detailed status for the current cluster. 
print(compute_target.get_status().serialize())
```

コンピューティング先の詳細については、[コンピューティング先の概要](concept-compute-target.md)に関する記事を参照してください。

## <a name="create-a-chainer-estimator"></a>Chainer エスティメーターを作成する

[Chainer エスティメーター](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py)には、コンピューティング先で Chainer トレーニング ジョブを起動する簡単な方法が用意されています。

Chainer エスティメーターは、ジェネリック [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) クラスを介して実装されています。これは、任意のフレームワークをサポートするために使用できます。 ジェネリック エスティメーターを使用したモデルのトレーニングの詳細については、[エスティメーターを使用した Azure Machine Learning によるモデルのトレーニング](how-to-train-ml-models.md)に関するページを参照してください。

```Python
from azureml.train.dnn import Chainer

script_params = {
    '--epochs': 10,
    '--batchsize': 128,
    '--output_dir': './outputs'
}

estimator = Chainer(source_directory=project_folder, 
                    script_params=script_params,
                    compute_target=compute_target,
                    pip_packages=['numpy', 'pytest'],
                    entry_script='chainer_mnist.py',
                    use_gpu=True)
```

## <a name="submit-a-run"></a>実行を送信する

[実行オブジェクト](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py)には、ジョブの実行中および完了後の実行履歴へのインターフェイスが用意されています。

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

実行は、以下の段階を経て実施されます。

- **準備**:docker イメージは Chainer エスティメーターに従って作成されます。 イメージはワークスペースのコンテナー レジストリにアップロードされ、後で実行するためにキャッシュされます。 ログは実行履歴にもストリーミングされ、進行状況を監視するために表示することができます。

- **拡大縮小**:Batch AI クラスターで実行の実施に現在使用できる数よりも多くのノードが必要な場合、スケールアップが試行されます。

- **Running**: script フォルダー内のすべてのスクリプトがコンピューティング先にアップロードされ、データ ストアがマウントまたはコピーされ、entry_script が実行されます。 stdout および ./logs フォルダーの出力は実行履歴にストリーミングされ、実行を監視するために使用できます。

- **後処理**:実行の ./outputs フォルダーが実行履歴にコピーされます。

## <a name="save-and-register-the-model"></a>モデルを保存して登録する

モデルのトレーニングが終わったら、それをワークスペースに保存して登録できます。 モデルの登録を使用すると、モデルをワークスペースに格納し、バージョン管理して、[モデルの管理とデプロイ](concept-model-management-and-deployment.md)を簡単にすることができます。

モデルを保存するには、トレーニング スクリプト **chainer_mnist.py** に次のコードを追加します。 

``` Python
    serializers.save_npz(os.path.join(args.output_dir, 'model.npz'), model)
```

次のコードでワークスペースにモデルを登録します。

```Python
model = run.register_model(model_name='chainer-dnn-mnist', model_path='outputs/model.npz')
```



## <a name="next-steps"></a>次の手順

この記事では、Azure Machine Learning service で Chainer モデルをトレーニングしました。 

* モデルをデプロイする方法を学習するには、[モデル デプロイ](how-to-deploy-and-where.md)の記事に進んでください。

* [ハイパーパラメーターを調整する](how-to-tune-hyperparameters.md)

* [トレーニング中に実行メトリクスを追跡する](how-to-track-experiments.md)
