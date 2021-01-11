---
title: TensorFlow モデルのトレーニングとデプロイ
titleSuffix: Azure Machine Learning
description: Azure Machine Learning を使用して、大規模な TensorFlow トレーニング スクリプトを実行する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: maxluk
author: maxluk
ms.date: 08/20/2019
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 1d7523f68f97250f71405f840a6a5e9fcf9fb2f9
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87320869"
---
# <a name="build-a-tensorflow-deep-learning-model-at-scale-with-azure-machine-learning"></a>Azure Machine Learning を使用して大規模な TensorFlow ディープ ラーニング モデルを構築する
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

この記事では、Azure Machine Learning の [TensorFlow エスティメーター](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) クラスを使用して、大規模な [TensorFlow](https://www.tensorflow.org/overview) トレーニング スクリプトを実行する方法について説明します。 この例では、ディープ ニューラル ネットワーク (DNN) を使用して手書きの数字を分類するための TensorFlow モデルをトレーニングして登録します。

TensorFlow モデルを一から開発する場合でも、[既存のモデル](how-to-deploy-existing-model.md)をクラウドに取り込む場合でも、Azure Machine Learning を使用してオープンソースのトレーニング ジョブをスケールアウトし、運用グレードのモデルの構築、デプロイ、バージョン管理、監視を行うことができます。

[ディープ ラーニングと機械学習の比較](concept-deep-learning-vs-machine-learning.md)の詳細を確認してください。

## <a name="prerequisites"></a>前提条件

このコードは、次の環境のいずれかで実行してください。

 - Azure Machine Learning コンピューティング インスタンス - ダウンロードやインストールは必要なし

     - 「[チュートリアル: 環境とワークスペースを設定する](tutorial-1st-experiment-sdk-setup.md)」を完了して、SDK とサンプル リポジトリが事前に読み込まれた専用のノートブック サーバーを作成します。
    - ノートブック サーバー上のディープ ラーニングの samples フォルダーで、**how-to-use-azureml > ml-frameworks > tensorflow > deployment > train-hyperparameter-tune-deploy-with-tensorflow** フォルダーの順に移動して、次の完了および展開済みノートブックを見つけます。 
 
 - 独自の Jupyter Notebook サーバー

    - [Azure Machine Learning SDK をインストールします](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)。
    - [ワークスペース構成ファイルを作成します](how-to-configure-environment.md#workspace)。
    - [サンプル スクリプト ファイル](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/tensorflow/deployment/train-hyperparameter-tune-deploy-with-tensorflow) `mnist-tf.py` および `utils.py` をダウンロードする
     
    このガイドの完成した [Jupyter Notebook バージョン](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/deployment/train-hyperparameter-tune-deploy-with-tensorflow/train-hyperparameter-tune-deploy-with-tensorflow.ipynb)は、GitHub サンプル ページにもあります。 このノートブックには、インテリジェントなハイパーパラメーター調整、モデル デプロイ、およびノートブックのウィジェットを示す展開済みセクションが含まれています。

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
from azureml.train.dnn import TensorFlow
```

### <a name="initialize-a-workspace"></a>ワークスペースを初期化する

[Azure Machine Learning ワークスペース](concept-workspace.md)は、サービス用の最上位のリソースです。 作成されるすべての成果物を操作できる一元的な場所が用意されています。 Python SDK では、[`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) オブジェクトを作成することでワークスペースの成果物にアクセスできます。

[前提条件のセクション](#prerequisites)で作成した `config.json` ファイルからワークスペース オブジェクトを作成します。

```Python
ws = Workspace.from_config()
```

### <a name="create-a-deep-learning-experiment"></a>ディープ ラーニングの実験を作成する

トレーニング スクリプトを保存するための実験とフォルダーを作成します。 この例では、"tf-mnist" という実験を作成します。

```Python
script_folder = './tf-mnist'
os.makedirs(script_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='tf-mnist')
```

### <a name="create-a-file-dataset"></a>ファイル データセットの作成

`FileDataset` オブジェクトでは、ご利用のワークスペース データストアまたはパブリック URL 内の 1 つまたは複数のファイルが参照されます。 ファイルは任意の形式にすることができ、クラスには、ファイルのダウンロードまたはご利用のコンピューティングへのファイルのマウントを行うための機能が用意されています。 `FileDataset` を作成することにより、データソースの場所への参照を作成します。 データセットに変換を適用した場合は、それらの変換もデータセットに格納されます。 データは既存の場所に残るので、追加のストレージ コストは発生しません。 詳細については、`Dataset` パッケージの[攻略](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets)ガイドを参照してください。

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

# list the files referenced by dataset
dataset.to_path()
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

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

コンピューティング先の詳細については、[コンピューティング先の概要](concept-compute-target.md)に関する記事を参照してください。

## <a name="create-a-tensorflow-estimator"></a>TensorFlow エスティメーターを作成する

[TensorFlow エスティメーター](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)には、コンピューティング先で TensorFlow トレーニング ジョブを起動する簡単な方法が用意されています。

TensorFlow エスティメーターは、ジェネリック [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) クラスを介して実装されています。これは、任意のフレームワークをサポートするために使用できます。 ジェネリック エスティメーターを使用したモデルのトレーニングの詳細については、[エスティメーターを使用した Azure Machine Learning によるモデルのトレーニング](how-to-train-ml-models.md)に関するページを参照してください。

トレーニング スクリプトを実行するために追加の PIP パッケージまたは Conda パッケージが必要な場合は、`pip_packages` および `conda_packages` 引数に名前を渡すことで、パッケージを結果の Docker イメージにインストールできます。


> [!WARNING]
> Azure Machine Learning では、ソース ディレクトリ全体をコピーすることで、トレーニング スクリプトが実行されます。 アップロードしたくない機密データがある場合は、[.ignore ファイル](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots)を使用するか、ソース ディレクトリに含めないようにします。 代わりに、[データストア](https://docs.microsoft.com/python/api/azureml-core/azureml.data?view=azure-ml-py)を使用してデータにアクセスしてください。


```python
script_params = {
    '--data-folder': dataset.as_named_input('mnist').as_mount(),
    '--batch-size': 50,
    '--first-layer-neurons': 300,
    '--second-layer-neurons': 100,
    '--learning-rate': 0.01
}

est = TensorFlow(source_directory=script_folder,
                 entry_script='tf_mnist.py',
                 script_params=script_params,
                 compute_target=compute_target,
                 use_gpu=True,
                 pip_packages=['azureml-dataprep[pandas,fuse]'])
```

> [!TIP]
> TensorFlow の Estimator クラスで新たに **Tensorflow 2.0** がサポートされます。 詳細については、[ブログ記事](https://azure.microsoft.com/blog/tensorflow-2-0-on-azure-fine-tuning-bert-for-question-tagging/)を参照してください。

Python 環境のカスタマイズの詳細については、[トレーニングとデプロイのための環境の作成と管理](how-to-use-environments.md)に関するページを参照してください。 

## <a name="submit-a-run"></a>実行を送信する

[実行オブジェクト](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py)には、ジョブの実行中および完了後の実行履歴へのインターフェイスが用意されています。

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

実行は、以下の段階を経て実施されます。

- **準備**:Docker イメージは TensorFlow エスティメーターに従って作成されます。 イメージはワークスペースのコンテナー レジストリにアップロードされ、後で実行するためにキャッシュされます。 ログは実行履歴にもストリーミングされ、進行状況を監視するために表示することができます。

- **拡大縮小**:Batch AI クラスターでの実行に現在使用可能な数より多くのノードが必要な場合、クラスターはスケールアップを試みます。

- **[実行中]** : script フォルダー内のすべてのスクリプトがコンピューティング先にアップロードされ、データ ストアがマウントまたはコピーされ、entry_script が実行されます。 stdout および ./logs フォルダーの出力は実行履歴にストリーミングされ、実行を監視するために使用できます。

- **後処理**:実行の ./outputs フォルダーが実行履歴にコピーされます。

## <a name="register-or-download-a-model"></a>モデルを登録またはダウンロードする

モデルのトレーニングが終わったら、それをワークスペースに登録できます。 モデルの登録を使用すると、モデルをワークスペースに格納し、バージョン管理して、[モデルの管理とデプロイ](concept-model-management-and-deployment.md)を簡単にすることができます。 パラメーター `model_framework`、`model_framework_version`、および `resource_configuration` を指定することによって、コードなしのモデル デプロイが使用可能になります。 これにより、登録済みのモデルからモデルを Web サービスとして直接デプロイできるようになり、`ResourceConfiguration` オブジェクトによって Web サービスのコンピューティング リソースが定義されます。

```Python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = run.register_model(model_name='tf-dnn-mnist', 
                           model_path='outputs/model',
                           model_framework=Model.Framework.TENSORFLOW,
                           model_framework_version='1.13.0',
                           resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5))
```

実行オブジェクトを使用してモデルのローカル コピーをダウンロードすることもできます。 トレーニング スクリプトの `mnist-tf.py` では、TensorFlow セーバー オブジェクトによってモデルがローカル フォルダー (コンピューティング先に対するローカル) に永続化されます。 実行オブジェクトを使用してコピーをダウンロードすることができます。

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

for f in run.get_file_names():
    if f.startswith('outputs/model'):
        output_file_path = os.path.join('./model', f.split('/')[-1])
        print('Downloading from {} to {} ...'.format(f, output_file_path))
        run.download_file(name=f, output_file_path=output_file_path)
```

## <a name="distributed-training"></a>分散トレーニング

[`TensorFlow`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) 推定器は、CPU と GPU のクラスターでの分散トレーニングもサポートしています。 分散 TensorFlow ジョブは簡単に実行でき、オーケストレーションの管理は Azure Machine Learning によって自動的に行われます。

Azure Machine Learning では、TensorFlow における分散トレーニングについて 2 つの方式をサポートしています。

- [MPI ベースの](https://www.open-mpi.org/)分散トレーニング ([Horovod](https://github.com/uber/horovod) フレームワークを使用)
- ネイティブの[分散 TensorFlow](https://www.tensorflow.org/deploy/distributed) (パラメーター サーバー方式を使用)

### <a name="horovod"></a>Horovod

[Horovod](https://github.com/uber/horovod) は、分散トレーニング用のオープンソース フレームワークとして Uber が開発したものです。 これを利用すると、分散 GPU TensorFlow ジョブを簡単に実行できます。

Horovod を使用するには、TensorFlow コンストラクターの `distributed_training` パラメーターに [`MpiConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) オブジェクトを指定します。 このパラメーターによって、トレーニング スクリプトで使用する Horovod ライブラリが確実にインストールされます。

```Python
from azureml.core.runconfig import MpiConfiguration
from azureml.train.dnn import TensorFlow

# Tensorflow constructor
estimator= TensorFlow(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=MpiConfiguration(),
                      framework_version='1.13',
                      use_gpu=True,
                      pip_packages=['azureml-dataprep[pandas,fuse]'])
```

### <a name="parameter-server"></a>パラメーター サーバー

[ネイティブの分散 TensorFlow](https://www.tensorflow.org/deploy/distributed) を実行することもできます｡その場合は､パラメーター サーバー モデルを使用します。 この方法では、パラメーター サーバーとワーカーからなるクラスター全体でトレーニングが行われます。 トレーニング中､ワーカーがグラディエントの計算する一方､パラメーター サーバーはグラディエントを集計します｡

パラメーター サーバー メソッドを使用するには、TensorFlow コンストラクターの `distributed_training` パラメーターに [`TensorflowConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration?view=azure-ml-py) オブジェクトを指定します。

```Python
from azureml.train.dnn import TensorFlow

distributed_training = TensorflowConfiguration()
distributed_training.worker_count = 2

# Tensorflow constructor
tf_est= TensorFlow(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=distributed_training,
                      use_gpu=True,
                      pip_packages=['azureml-dataprep[pandas,fuse]'])

# submit the TensorFlow job
run = exp.submit(tf_est)
```

#### <a name="define-cluster-specifications-in-tf_config"></a>'TF_CONFIG' でクラスター仕様を定義する

[`tf.train.ClusterSpec`](https://www.tensorflow.org/api_docs/python/tf/train/ClusterSpec) のためのクラスターのネットワーク アドレスとポートも必要になるため、Azure Machine Learning によって `TF_CONFIG` 環境変数が自動的に設定されます。

`TF_CONFIG` 環境変数は JSON 文字列です｡ 以下に､パラメーター サーバー対する変数例を次に示します。

```JSON
TF_CONFIG='{
    "cluster": {
        "ps": ["host0:2222", "host1:2222"],
        "worker": ["host2:2222", "host3:2222", "host4:2222"],
    },
    "task": {"type": "ps", "index": 0},
    "environment": "cloud"
}'
```

TensorFlow の高レベル [`tf.estimator`](https://www.tensorflow.org/api_docs/python/tf/estimator) API の場合は、TensorFlow が `TF_CONFIG` 変数を解析してクラスターの仕様を自動的にビルドします。

TensorFlow の低レベル コア API をトレーニングに使用する場合は、トレーニング コードの中で自分で `TF_CONFIG` 変数を解析して `tf.train.ClusterSpec` をビルドします。

```Python
import os, json
import tensorflow as tf

tf_config = os.environ.get('TF_CONFIG')
if not tf_config or tf_config == "":
    raise ValueError("TF_CONFIG not found.")
tf_config_json = json.loads(tf_config)
cluster_spec = tf.train.ClusterSpec(cluster)

```

## <a name="deploy-a-tensorflow-model"></a>TensorFlow モデルをデプロイする

今登録したモデルは、どのエスティメーターをトレーニングに使用したかには関係なく、Azure Machine Learning の他のすべての登録済みのモデルとまったく同じ方法でデプロイできます。 デプロイ方法にはモデルの登録に関するセクションが含まれていますが、登録済みのモデルが既にあるため、デプロイのために[コンピューティング先の作成](how-to-deploy-and-where.md#choose-a-compute-target)に直接スキップできます。

## <a name="preview-no-code-model-deployment"></a>(プレビュー) コードなしのモデル デプロイ

従来のデプロイ ルートの代わりに、コードなしのデプロイ機能 (プレビュー) を Tensorflow に使用することもできます。 `model_framework`、`model_framework_version`、および `resource_configuration` パラメーターを使用して前に示したようにモデルを登録することにより、単純に `deploy()` 静的関数を使用してモデルをデプロイできます。

```python
service = Model.deploy(ws, "tensorflow-web-service", [model])
```

完全な[方法](how-to-deploy-and-where.md)では、Azure Machine Learning を使用したデプロイがさらに詳細に説明されています。

## <a name="next-steps"></a>次のステップ

この記事では、TensorFlow モデルをトレーニングして登録し、デプロイのオプションについて学習しました。 Azure Machine Learning の詳細については、以下の他の記事をご覧ください。

* [トレーニング中に実行メトリクスを追跡する](how-to-track-experiments.md)
* [ハイパーパラメーターを調整する](how-to-tune-hyperparameters.md)
* [Azure での分散型ディープ ラーニング トレーニングの参照アーキテクチャ](/azure/architecture/reference-architectures/ai/training-deep-learning)
