---
title: ディープ ラーニング Keras モデルをトレーニングする
titleSuffix: Azure Machine Learning
description: Azure Machine Learning を使用して、TensorFlow 上で実行される Keras ディープ ニューラル ネットワーク分類モデルをトレーニングして登録する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: minxia
author: mx-iao
ms.reviewer: peterlu
ms.date: 09/28/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 2b4af9dec2bf397ad2766c68d547eeac85a9a9a3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102518366"
---
# <a name="train-keras-models-at-scale-with-azure-machine-learning"></a>Azure Machine Learning を使用して Keras モデルを大規模にトレーニングする

この記事では、Azure Machine Learning を使用して Keras トレーニング スクリプトを実行する方法について説明します。

この記事に記載されたサンプル コードは、Azure Machine Learning を使用して、TensorFlow バックエンドで構築された Keras 分類モデルをトレーニングして登録する方法を示しています。 これは、一般的な [MNIST データセット](http://yann.lecun.com/exdb/mnist/)を使用して、[TensorFlow](https://www.tensorflow.org/overview) 上で実行される [Keras Python ライブラリ](https://keras.io)を使用して構築されたディープ ニューラル ネットワーク (DNN) を使用して手書きの数字を分類します。

Keras は、開発を簡素化するために他の一般的な DNN フレームワーク上で動作できる高レベルのニューラル ネットワーク API です。 Azure Machine Learning では、エラスティック クラウド コンピューティング リソースを使用して、トレーニング ジョブを迅速にスケールアウトできます。 また、トレーニング実行、バージョン モデル、デプロイ モデル、その他多くを追跡することもできます。

Keras モデルを最初から開発しているか、または既存のモデルをクラウドに導入しているかにかかわらず、Azure Machine Learning は運用環境対応のモデルを構築するのに役立ちます。

> [!NOTE]
> スタンドアロンの Keras パッケージではなく、TensorFlow に組み込まれた Keras API の **tf.keras** を使用している場合は、代わりに [TensorFlow モデルのトレーニング](how-to-train-tensorflow.md)に関する記事をご覧ください。

## <a name="prerequisites"></a>前提条件

このコードは、次の環境のいずれかで実行してください。

- Azure Machine Learning コンピューティング インスタンス - ダウンロードやインストールは必要なし

     - 「[チュートリアル: 環境とワークスペースを設定する](tutorial-1st-experiment-sdk-setup.md)」を完了して、SDK とサンプル リポジトリが事前に読み込まれた専用のノートブック サーバーを作成します。
    - ノートブック サーバー上の samples フォルダーで、**how-to-use-azureml > ml-frameworks > keras > train-hyperparameter-tune-deploy-with-keras** の各フォルダーに移動して、完成した展開済みノートブックを見つけます。

 - 独自の Jupyter Notebook サーバー

    - [Azure Machine Learning SDK (1.15.0 以上) をインストールします](/python/api/overview/azure/ml/install)。
    - [ワークスペース構成ファイルを作成します](how-to-configure-environment.md#workspace)。
    - [サンプル スクリプト ファイル](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/keras/train-hyperparameter-tune-deploy-with-keras) `keras_mnist.py` および `utils.py` をダウンロードする

    このガイドの完成した [Jupyter Notebook バージョン](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/keras/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.ipynb)は、GitHub サンプル ページにもあります。 このノートブックには、インテリジェントなハイパーパラメーター調整、モデル デプロイ、およびノートブックのウィジェットを示す展開済みセクションが含まれています。

## <a name="set-up-the-experiment"></a>実験を設定する

このセクションでは、必要な Python パッケージの読み込み、ワークスペースの初期化、入力トレーニング データ用の FileDataset の作成、コンピューティング先の作成、およびトレーニング環境の定義を行うことで、トレーニング実験を設定します。

### <a name="import-packages"></a>パッケージをインポートする

まず、必要な Python ライブラリをインポートします。

```Python
import os
import azureml
from azureml.core import Experiment
from azureml.core import Environment
from azureml.core import Workspace, Run
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>ワークスペースを初期化する

[Azure Machine Learning ワークスペース](concept-workspace.md)は、サービス用の最上位のリソースです。 作成されるすべての成果物を操作できる一元的な場所が用意されています。 Python SDK では、[`workspace`](/python/api/azureml-core/azureml.core.workspace.workspace) オブジェクトを作成することでワークスペースの成果物にアクセスできます。

[前提条件のセクション](#prerequisites)で作成した `config.json` ファイルからワークスペース オブジェクトを作成します。

```Python
ws = Workspace.from_config()
```

### <a name="create-a-file-dataset"></a>ファイル データセットの作成

`FileDataset` オブジェクトでは、ご利用のワークスペース データストアまたはパブリック URL 内の 1 つまたは複数のファイルが参照されます。 ファイルは任意の形式にすることができ、クラスには、ファイルのダウンロードまたはご利用のコンピューティングへのファイルのマウントを行うための機能が用意されています。 `FileDataset` を作成することにより、データソースの場所への参照を作成します。 データセットに変換を適用した場合は、それらの変換もデータセットに格納されます。 データは既存の場所に残るので、追加のストレージ コストは発生しません。 詳細については、`Dataset` パッケージの[攻略](./how-to-create-register-datasets.md)ガイドを参照してください。

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

`register()` メソッドを使用してデータセットをお使いのワークスペースに登録すると、それらを他のユーザーと共有したり、さまざまな実験で再利用したり、トレーニング スクリプト内で名前で参照したりできるようになります。

```python
dataset = dataset.register(workspace=ws,
                           name='mnist-dataset',
                           description='training and test dataset',
                           create_new_version=True)
```

### <a name="create-a-compute-target"></a>コンピューティング ターゲットを作成する

トレーニング ジョブを実行するためのコンピューティング先を作成します。 この例では、GPU 対応の Azure Machine Learning コンピューティング クラスターを作成します。

```Python
cluster_name = "gpu-cluster"

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

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

コンピューティング先の詳細については、[コンピューティング先の概要](concept-compute-target.md)に関する記事を参照してください。

### <a name="define-your-environment"></a>環境を定義する

トレーニング スクリプトの依存関係をカプセル化する Azure ML [環境](concept-environments.md)を定義します。

最初に、YAML ファイルで Conda の依存関係を定義します (この例では、ファイルの名前は `conda_dependencies.yml` です)。

```yaml
channels:
- conda-forge
dependencies:
- python=3.6.2
- pip:
  - azureml-defaults
  - tensorflow-gpu==2.0.0
  - keras<=2.3.1
  - matplotlib
```

この conda 環境仕様から Azure ML 環境を作成します。 環境は、実行時に Docker コンテナーにパッケージ化されます。

ベース イメージが指定されていない場合、Azure ML では既定により CPU イメージ `azureml.core.environment.DEFAULT_CPU_IMAGE` がベース イメージとして使用されます。 この例では GPU クラスター上でトレーニングを実行するため、必要な GPU ドライバーと依存関係が含まれる GPU ベース イメージを指定する必要があります。 Azure ML では、Microsoft Container Registry (MCR) に公開された、お客様が使用できる 1 組のベース イメージを保持しています。詳細については、GitHub リポジトリ「[Azure/AzureML-Containers](https://github.com/Azure/AzureML-Containers)」を参照してください。

```python
keras_env = Environment.from_conda_specification(name='keras-env', file_path='conda_dependencies.yml')

# Specify a GPU base image
keras_env.docker.enabled = True
keras_env.docker.base_image = 'mcr.microsoft.com/azureml/openmpi3.1.2-cuda10.0-cudnn7-ubuntu18.04'
```

環境の作成と使用の詳細について詳しくは、「[Azure Machine Learning でソフトウェア環境を作成して使用する](how-to-use-environments.md)」を参照してください。

## <a name="configure-and-submit-your-training-run"></a>トレーニングの実行を構成して送信する

### <a name="create-a-scriptrunconfig"></a>ScriptRunConfig を作成する
まず、`Dataset` クラスを使用してワークスペース データストアからデータを取得します。

```python
dataset = Dataset.get_by_name(ws, 'mnist-dataset')

# list the files referenced by mnist-dataset
dataset.to_path()
```

ScriptRunConfig オブジェクトを作成して、トレーニング スクリプト、使用する環境、実行対象のコンピューティング先など、トレーニング ジョブの構成の詳細を指定します。

トレーニング スクリプトへの引数は、`arguments` パラメーターで指定されている場合、すべてコマンド ラインで渡されます。 FileDataset の DatasetConsumptionConfig は、引数としてトレーニング スクリプトに渡されます (`--data-folder` 引数に対応)。 Azure ML では、この DatasetConsumptionConfig がバッキング データストアのマウント ポイントに解決され、その後、トレーニング スクリプトからアクセスできるようになります。

```python
from azureml.core import ScriptRunConfig

args = ['--data-folder', dataset.as_mount(),
        '--batch-size', 50,
        '--first-layer-neurons', 300,
        '--second-layer-neurons', 100,
        '--learning-rate', 0.001]

src = ScriptRunConfig(source_directory=script_folder,
                      script='keras_mnist.py',
                      arguments=args,
                      compute_target=compute_target,
                      environment=keras_env)
```

ScriptRunConfig を使用したジョブの構成の詳細については、[トレーニングの実行の構成と送信](how-to-set-up-training-targets.md)に関する記事をご覧ください。

> [!WARNING]
> 以前に TensorFlow Estimator を使用して Keras トレーニング ジョブを構成していた場合は、1.19.0 SDK のリリース以降では、Estimator が非推奨になっていることに注意してください。 Azure ML SDK 1.15.0 以降では、ディープ ラーニング フレームワークを使用するものを含めて、ScriptRunConfig がトレーニング ジョブを構成する場合に推奨される方法です。 移行に関する一般的な質問については、[Estimator から ScriptRunConfig への移行](how-to-migrate-from-estimators-to-scriptrunconfig.md)に関するガイドを参照してください。

### <a name="submit-your-run"></a>実行を送信する

[実行オブジェクト](/python/api/azureml-core/azureml.core.run%28class%29)には、ジョブの実行中および完了後の実行履歴へのインターフェイスが用意されています。

```Python
run = Experiment(workspace=ws, name='Tutorial-Keras-Minst').submit(src)
run.wait_for_completion(show_output=True)
```

### <a name="what-happens-during-run-execution"></a>実行実施中の動作
実行は、以下の段階を経て実施されます。

- **準備**:Docker イメージは、定義されている環境に従って作成されます。 イメージはワークスペースのコンテナー レジストリにアップロードされ、後で実行するためにキャッシュされます。 ログは実行履歴にもストリーミングされ、進行状況を監視するために表示することができます。 代わりに、キュレーションされた環境が指定されている場合は、そのキュレーションされた環境を補足するキャッシュ済みのイメージが使用されます。

- **拡大縮小**:Batch AI クラスターでの実行に現在使用可能な数より多くのノードが必要な場合、クラスターはスケールアップを試みます。

- **[実行中]** : スクリプト フォルダー内のすべてのスクリプトがコンピューティング先にアップロードされ、データ ストアがマウントまたはコピーされて、`script` が実行されます。 stdout からの出力と **./logs** フォルダーが実行履歴にストリーミングされるので、実行の監視のために使用できます。

- **後処理**:実行の **./outputs** フォルダーが実行履歴にコピーされます。

## <a name="register-the-model"></a>モデルを登録する

モデルのトレーニングが終わったら、それをワークスペースに登録できます。 モデルの登録を使用すると、モデルをワークスペースに格納し、バージョン管理して、[モデルの管理とデプロイ](concept-model-management-and-deployment.md)を簡単にすることができます。

```Python
model = run.register_model(model_name='keras-mnist', model_path='outputs/model')
```

> [!TIP]
> デプロイ方法にはモデルの登録に関するセクションが含まれていますが、登録済みのモデルが既にあるため、デプロイのために[コンピューティング先の作成](how-to-deploy-and-where.md#choose-a-compute-target)に直接スキップできます。

モデルのローカル コピーをダウンロードすることもできます。 これは、追加のモデル検証作業をローカルで実行するのに役立つ場合があります。 トレーニング スクリプト `keras_mnist.py` では、TensorFlow セーバー オブジェクトにより、モデルがローカル フォルダー (コンピューティング先に対してローカル) に永続化されます。 実行オブジェクトを使用して、実行履歴からコピーをダウンロードできます。

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

* [モデルをデプロイする方法と場所](how-to-deploy-and-where.md)
* [トレーニング中に実行メトリクスを追跡する](how-to-track-experiments.md)
* [ハイパーパラメーターを調整する](how-to-tune-hyperparameters.md)
* [トレーニング済みモデルをデプロイする](how-to-deploy-and-where.md)
* [Azure での分散型ディープ ラーニング トレーニングの参照アーキテクチャ](/azure/architecture/reference-architectures/ai/training-deep-learning)
