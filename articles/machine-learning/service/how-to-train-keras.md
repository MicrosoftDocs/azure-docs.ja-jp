---
title: Keras を使用したディープ ラーニング ニューラル ネットワークのトレーニング
titleSuffix: Azure Machine Learning
description: Azure Machine Learning を使用して、TensorFlow 上で実行される Keras ディープ ニューラル ネットワーク分類モデルをトレーニングして登録する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.reviewer: peterlu
ms.date: 08/01/2019
ms.custom: seodec18
ms.openlocfilehash: 9015fa445c64bffa74509e84d90eb77508da6d9e
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2019
ms.locfileid: "71076449"
---
# <a name="train-and-register-a-keras-classification-model-with-azure-machine-learning"></a>Azure Machine Learning を使用して Keras 分類モデルをトレーニングして登録する

この記事では、Azure Machine Learning を使用して、TensorFlow 上に構築された Keras 分類モデルをトレーニングして登録する方法について説明します。 これは、一般的な [MNIST データセット](http://yann.lecun.com/exdb/mnist/)を使用して、[TensorFlow](https://www.tensorflow.org/overview) 上で実行される [Keras Python ライブラリ](https://keras.io)を使用して構築されたディープ ニューラル ネットワーク (DNN) を使用して手書きの数字を分類します。

Keras は、開発を簡素化するために他の一般的な DNN フレームワーク上で動作できる高レベルのニューラル ネットワーク API です。 Azure Machine Learning では、エラスティック クラウド コンピューティング リソースを使用して、トレーニング ジョブを迅速にスケールアウトできます。 また、トレーニング実行、バージョン モデル、デプロイ モデル、その他多くを追跡することもできます。

Keras モデルを最初から開発しているか、または既存のモデルをクラウドに導入しているかにかかわらず、Azure Machine Learning は運用環境対応のモデルを構築するのに役立ちます。

機械学習とディープ ラーニングの違いについて詳しくは、[概念に関する記事](concept-deep-learning-vs-machine-learning.md)をご覧ください。

## <a name="prerequisites"></a>前提条件

このコードは、次の環境のいずれかで実行してください。

 - Azure Machine Learning Notebook VM - ダウンロードやインストールは必要なし

     - 「[チュートリアル: 環境とワークスペースを設定する](tutorial-1st-experiment-sdk-setup.md)」を完了して、SDK とサンプル リポジトリが事前に読み込まれた専用のノートブック サーバーを作成します。
    - ノートブック サーバー上の samples フォルダーで、**how-to-use-azureml > training-with-deep-learning > train-hyperparameter-tune-deploy-with-keras** の各フォルダーに移動して、完成した展開済みノートブックを見つけます。

 - 独自の Jupyter Notebook サーバー

    - [Azure Machine Learning SDK をインストールします](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)。
    - [ワークスペース構成ファイルを作成します](how-to-configure-environment.md#workspace)。
    - [サンプル スクリプト ファイル](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras) `mnist-keras.py` および `utils.py` をダウンロードする

    このガイドの完成した [Jupyter Notebook バージョン](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.ipynb)は、GitHub サンプル ページにもあります。 このノートブックには、インテリジェントなハイパーパラメーター調整、モデル デプロイ、およびノートブックのウィジェットを示す展開済みセクションが含まれています。

## <a name="set-up-the-experiment"></a>実験を設定する

このセクションでは、必要な Python パッケージを読み込み、ワークスペースを初期化し、実験を作成し、トレーニング データとトレーニング スクリプトをアップロードすることで、トレーニング実験を設定します。

### <a name="import-packages"></a>パッケージをインポートする

まず、必要な Python ライブラリをインポートします。

```Python
import os
import azureml
from azureml.core import Experiment
from azureml.core import Workspace, Run
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>ワークスペースを初期化する

[Azure Machine Learning ワークスペース](concept-workspace.md)は、サービス用の最上位のリソースです。 作成されるすべての成果物を操作できる一元的な場所が用意されています。 Python SDK では、[`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) オブジェクトを作成することでワークスペースの成果物にアクセスできます。

[前提条件のセクション](#prerequisites)で作成した `config.json` ファイルからワークスペース オブジェクトを作成します。

```Python
ws = Workspace.from_config()
```

### <a name="create-an-experiment"></a>実験の作成

ご利用のワークスペースに "keras-mnist" という実験を作成します。

```Python
exp = Experiment(workspace=ws, name='keras-mnist')
```

### <a name="create-a-file-dataset"></a>ファイル データセットの作成

`FileDataset` オブジェクトでは、ご利用のワークスペース データストアまたはパブリック URL 内の 1 つまたは複数のファイルが参照されます。 ファイルは任意の形式にすることができ、クラスには、ファイルのダウンロードまたはご利用のコンピューティングへのファイルのマウントを行うための機能が用意されています。 `FileDataset` を作成することにより、データソースの場所への参照を作成します。 データセットに任意の変換を適用した場合は、それらはデータセットにも格納されます。 データは既存の場所に残るので、追加のストレージ コストは発生しません。 詳細については、`Dataset` パッケージの[攻略](https://docs.microsoft.com/azure/machine-learning/service/how-to-create-register-datasets)ガイドを参照してください。

```python
from azureml.core.dataset import Dataset

web_paths = [
            'http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz'
            ]
dataset = Dataset.File.from_files(path=web_paths)
```

他のユーザーと共有したり、さまざまな実験で再利用したり、トレーニング スクリプト内で名前で参照したりできるように、データセットをご利用のワークスペースに登録するには、`register()` メソッドを使います。

```python
dataset = dataset.register(workspace=ws,
                           name='mnist dataset',
                           description='training and test dataset',
                           create_new_version=True)
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

まず、`Dataset` クラスを使用してワークスペース データストアからデータを取得します。

```python
dataset = Dataset.get_by_name(ws, 'mnist dataset')

# list the files referenced by mnist dataset
dataset.to_path()
```

TensorFlow エスティメーターは、ジェネリック [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) クラスを介して実装されています。これは、任意のフレームワークをサポートするために使用できます。 さらに、DNN ハイパーパラメーター設定を含むディクショナリ `script_params` を作成します。 ジェネリック エスティメーターを使用したモデルのトレーニングの詳細については、[エスティメーターを使用した Azure Machine Learning によるモデルのトレーニング](how-to-train-ml-models.md)に関するページを参照してください。

```python
from azureml.train.dnn import TensorFlow

script_params = {
    '--data-folder': dataset.as_named_input('mnist').as_mount(),
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

DNN モデルのトレーニングが終わったら、それをワークスペースに登録できます。 モデルの登録を使用すると、モデルをワークスペースに格納し、バージョン管理して、[モデルの管理とデプロイ](concept-model-management-and-deployment.md)を簡単にすることができます。

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

この記事では、Azure Machine Learning で Keras モデルをトレーニングして登録しました。 モデルをデプロイする方法を学習するには、モデル デプロイの記事に進んでください。

> [!div class="nextstepaction"]
> [モデルをデプロイする方法と場所](how-to-deploy-and-where.md)
* [トレーニング中に実行メトリクスを追跡する](how-to-track-experiments.md)
* [ハイパーパラメーターを調整する](how-to-tune-hyperparameters.md)
* [トレーニング済みモデルをデプロイする](how-to-deploy-and-where.md)
* [Azure での分散型ディープ ラーニング トレーニングの参照アーキテクチャ](/azure/architecture/reference-architectures/ai/training-deep-learning)
