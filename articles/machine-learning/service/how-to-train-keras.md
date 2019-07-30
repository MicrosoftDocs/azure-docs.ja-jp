---
title: TensorFlow 上で実行される Keras モデルをトレーニングして登録する
titleSuffix: Azure Machine Learning service
description: この記事では、Azure Machine Learning service を使用して、TensorFlow 上で実行される Keras モデルをトレーニングして登録する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.reviewer: peterlu
ms.date: 06/07/2019
ms.custom: seodec18
ms.openlocfilehash: 9d405b454d755e0c848e9422c8d4cf6e7c505b68
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840047"
---
# <a name="train-and-register-keras-models-at-scale-with-azure-machine-learning-service"></a>Azure Machine Learning service を使用して Keras モデルを大規模にトレーニングして登録する

この記事では、Azure Machine Learning service を使用して、TensorFlow 上に構築された Keras モデルをトレーニングして登録する方法について説明します。 これは、一般的な [MNIST データセット](http://yann.lecun.com/exdb/mnist/)を使用して、[TensorFlow](https://www.tensorflow.org/overview) 上で実行される [Keras Python ライブラリ](https://keras.io)を使用して構築されたディープ ニューラル ネットワーク (DNN) を使用して手書きの数字を分類します。

Keras は、開発を簡素化するために他の一般的な DNN フレームワーク上で動作できる高レベルのニューラル ネットワーク API です。 Azure Machine Learning service では、エラスティック クラウド コンピューティング リソースを使用して、トレーニング ジョブを迅速にスケールアウトできます。 また、トレーニング実行、バージョン モデル、デプロイ モデル、その他多くを追跡することもできます。

Keras モデルを最初から開発しているか、または既存のモデルをクラウドに導入しているかにかかわらず、Azure Machine Learning service は運用環境対応のモデルを構築するのに役立ちます。

## <a name="prerequisites"></a>前提条件

このコードは、次の環境のいずれかで実行してください。

 - Azure Machine Learning Notebook VM - ダウンロードやインストールは必要なし

     - [クラウド ベースのノートブックのクイック スタート](quickstart-run-cloud-notebook.md)を完了して、SDK とサンプル リポジトリが事前に読み込まれた専用のノートブック サーバーを作成します。
    - ノートブック サーバー上の samples フォルダーで、**how-to-use-azureml > training-with-deep-learning > train-hyperparameter-tune-deploy-with-keras** の各フォルダーに移動して、完成した展開済みノートブックを見つけます。 
 
 - 独自の Jupyter Notebook サーバー

     - [Azure Machine Learning SDK for Python をインストールする](setup-create-workspace.md#sdk)
    - [ワークスペース構成ファイルを作成する](setup-create-workspace.md#write-a-configuration-file)
    - [サンプル スクリプト ファイル](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras) `mnist-keras.py` および `utils.py` をダウンロードする
     
    このガイドの完成した [Jupyter Notebook バージョン](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.ipynb)は、GitHub サンプル ページにもあります。 このノートブックには、インテリジェントなハイパーパラメーター調整、モデル デプロイ、およびノートブックのウィジェットを示す展開済みセクションが含まれています。

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

トレーニング スクリプトを保存するための実験とフォルダーを作成します。 この例では、"keras-mnist" という名前の実験を作成します。

```Python
script_folder = './keras-mnist'
os.makedirs(script_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='keras-mnist')
```

### <a name="upload-dataset-and-scripts"></a>データセットとスクリプトをアップロードする

[データストア](how-to-access-data.md)は、データをコンピューティング先にマウントまたはコピーすることでデータを格納およびアクセスできる場所です。 各ワークスペースには既定のデータストアがあります。 トレーニング中に簡単にアクセスできるように、データとトレーニング スクリプトをデータストアにアップロードします。

1. MNIST データセットをローカルにダウンロードします。

    ```Python
    os.makedirs('./data/mnist', exist_ok=True)

    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz', filename = './data/mnist/train-images.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz', filename = './data/mnist/train-labels.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz', filename = './data/mnist/test-images.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz', filename = './data/mnist/test-labels.gz')
    ```

1. MNIST データセットを既定のデータストアにアップロードします。

    ```Python
    ds = ws.get_default_datastore()
    ds.upload(src_dir='./data/mnist', target_path='mnist', overwrite=True, show_progress=True)
    ```

1. Keras トレーニング スクリプト `keras_mnist.py` とヘルパー ファイル `utils.py` をアップロードします。

    ```Python
    shutil.copy('./keras_mnist.py', script_folder)
    shutil.copy('./utils.py', script_folder)
    ```

## <a name="create-a-compute-target"></a>コンピューティング ターゲットを作成する

TensorFlow ジョブを実行するためのコンピューティング先を作成します。 この例では、GPU 対応の Azure Machine Learning コンピューティング クラスターを作成します。

```Python
cluster_name = "gpucluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6', 
                                                           max_nodes=4)

    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

コンピューティング先の詳細については、[コンピューティング先の概要](concept-compute-target.md)に関する記事を参照してください。

## <a name="create-a-tensorflow-estimator-and-import-keras"></a>TensorFlow エスティメーターを作成して Keras をインポートする

[TensorFlow エスティメーター](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)は、コンピューティング先で TensorFlow トレーニング ジョブを起動するための単純な方法を提供します。 Keras は TensorFlow 上で実行されるため、TensorFlow エスティメーターを使用し、`pip_packages` 引数を使用して Keras ライブラリをインポートできます。

TensorFlow エスティメーターは、ジェネリック [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) クラスを介して実装されています。これは、任意のフレームワークをサポートするために使用できます。 ジェネリック エスティメーターを使用したモデルのトレーニングの詳細については、[エスティメーターを使用した Azure Machine Learning によるモデルのトレーニング](how-to-train-ml-models.md)に関するページを参照してください。

```Python
script_params = {
    '--data-folder': ds.path('mnist').as_mount(),
    '--batch-size': 50,
    '--first-layer-neurons': 300,
    '--second-layer-neurons': 100,
    '--learning-rate': 0.001
}

est = TensorFlow(source_directory=script_folder,
                 entry_script='keras_mnist.py',
                 script_params=script_params,
                 compute_target=compute_target,
                 pip_packages=['keras', 'matplotlib'],
                 use_gpu=True)
```

## <a name="submit-a-run"></a>実行を送信する

[実行オブジェクト](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py)には、ジョブの実行中および完了後の実行履歴へのインターフェイスが用意されています。

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

実行は、以下の段階を経て実施されます。

- **準備**:docker イメージは TensorFlow エスティメーターに従って作成されます。 イメージはワークスペースのコンテナー レジストリにアップロードされ、後で実行するためにキャッシュされます。 ログは実行履歴にもストリーミングされ、進行状況を監視するために表示することができます。

- **拡大縮小**:Batch AI クラスターでの実行に現在使用可能な数より多くのノードが必要な場合、クラスターはスケールアップを試みます。

- **Running**: script フォルダー内のすべてのスクリプトがコンピューティング先にアップロードされ、データ ストアがマウントまたはコピーされ、entry_script が実行されます。 stdout および ./logs フォルダーの出力は実行履歴にストリーミングされ、実行を監視するために使用できます。

- **後処理**:実行の ./outputs フォルダーが実行履歴にコピーされます。

## <a name="register-the-model"></a>モデルを登録する

モデルのトレーニングが終わったら、それをワークスペースに登録できます。 モデルの登録を使用すると、モデルをワークスペースに格納し、バージョン管理して、[モデルの管理とデプロイ](concept-model-management-and-deployment.md)を簡単にすることができます。

```Python
model = run.register_model(model_name='keras-dnn-mnist', model_path='outputs/model')
```

モデルのローカル コピーをダウンロードすることもできます。 これは、追加のモデル検証作業をローカルで実行するのに役立つ場合があります。 トレーニング スクリプト `mnist-keras.py` では、TensorFlow セーバー オブジェクトにより、モデルがローカル フォルダー (コンピューティング先に対してローカル) に永続化されます。 実行オブジェクトを使用して、データストアからコピーをダウンロードできます。

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

for f in run.get_file_names():
    if f.startswith('outputs/model'):
        output_file_path = os.path.join('./model', f.split('/')[-1])
        print('Downloading from {} to {} ...'.format(f, output_file_path))
        run.download_file(name=f, output_file_path=output_file_path)
```

## <a name="next-steps"></a>次の手順

この記事では、Azure Machine Learning service で Keras モデルをトレーニングして登録しました。 モデルをデプロイする方法を学習するには、モデル デプロイの記事に進んでください。

> [!div class="nextstepaction"]
> [モデルをデプロイする方法と場所](how-to-deploy-and-where.md)
