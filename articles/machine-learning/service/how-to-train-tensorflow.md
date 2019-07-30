---
title: TensorFlow モデルのトレーニングと登録
titleSuffix: Azure Machine Learning service
description: この記事では、Azure Machine Learning service を使用して TensorFlow モデルをトレーニングおよび登録する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.date: 06/10/2019
ms.custom: seodec18
ms.openlocfilehash: 67263df319063cdf21dadea257dcab05ba0d5f7b
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839994"
---
# <a name="train-and-register-tensorflow-models-at-scale-with-azure-machine-learning-service"></a>Azure Machine Learning service を使用して TensorFlow モデルを大規模にトレーニングおよび登録する

この記事では、Azure Machine Learning service を使用して TensorFlow モデルをトレーニングおよび登録する方法について説明します。 よく使用される [MNIST データセット](http://yann.lecun.com/exdb/mnist/)を使用して、[TensorFlow Python ライブラリ](https://www.tensorflow.org/overview)を使用して構築されたディープ ニューラル ネットワークを使用して、手書きの数字を分類します。

TensorFlow は、ディープ ニューラル ネットワーク (DNN) の作成に一般的に使用されている、オープン ソースのコンピューティング フレームワークです。 Azure Machine Learning service を利用すると、エラスティック クラウド コンピューティング リソースを使用して、オープン ソースのトレーニング ジョブを迅速にスケールアウトできます。 トレーニングの実行、モデルのバージョン管理、モデルのデプロイなどを追跡することもできます。

TensorFlow モデルをゼロから開発する場合でも、[既存のモデル](how-to-deploy-existing-model.md)をクラウドに導入する場合でも、Azure Machine Learning service は運用環境対応モデルの構築に役立ちます。

## <a name="prerequisites"></a>前提条件

次のいずれかの環境で、このコードを実行してください。

 - Azure Machine Learning Notebook VM - ダウンロードとインストールが不要

     - [クラウドベースのノートブックによるクイックスタート](quickstart-run-cloud-notebook.md)を完了して、SDK とサンプル リポジトリが事前に読み込まれている専用のノートブック サーバーを作成します。
    - Notebook サーバー上の samples フォルダーで、**how-to-use-azureml > training-with-deep-learning > train-hyperparameter-tune-deploy-with-tensorflow** の順に移動して、次の完了および展開済みノートブックを見つけます。 
 
 - 独自の Jupyter Notebook サーバー

     - [Azure Machine Learning SDK for Python をインストールする](setup-create-workspace.md#sdk)
    - [ワークスペース構成ファイルを作成する](setup-create-workspace.md#write-a-configuration-file)
    - [サンプル スクリプト ファイル](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow) `mnist-tf.py` および `utils.py` をダウンロードする
     
    このガイドの完成した [Jupyter Notebook バージョン](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow/train-hyperparameter-tune-deploy-with-tensorflow.ipynb)は、GitHub サンプル ページにもあります。 このノートブックには、高度なハイパーパラメーターの調整、モデル デプロイ、およびノートブック ウィジェットについて説明するセクションがあります。

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

トレーニング スクリプトを保存するための実験とフォルダーを作成します。 この例では、"tf-mnist" という実験を作成します。

```Python
script_folder = './tf-mnist'
os.makedirs(script_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='tf-mnist')
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

1. TensorFlow トレーニング スクリプトの `tf_mnist.py` とヘルパー ファイルの `utils.py` をアップロードします。

    ```Python
    shutil.copy('./tf_mnist.py', script_folder)
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

## <a name="create-a-tensorflow-estimator"></a>TensorFlow エスティメーターを作成する

[TensorFlow エスティメーター](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)には、コンピューティング先で TensorFlow トレーニング ジョブを起動する簡単な方法が用意されています。

TensorFlow エスティメーターは、ジェネリック [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) クラスを介して実装されています。これは、任意のフレームワークをサポートするために使用できます。 ジェネリック エスティメーターを使用したモデルのトレーニングの詳細については、[エスティメーターを使用した Azure Machine Learning によるモデルのトレーニング](how-to-train-ml-models.md)に関するページを参照してください。

トレーニング スクリプトを実行するために追加の pip パッケージまたは conda パッケージが必要な場合は、`pip_packages` および `conda_packages` 引数に名前を渡すことで、パッケージを結果の docker イメージにインストールできます。

```Python
script_params = {
    '--data-folder': ws.get_default_datastore().as_mount(),
    '--batch-size': 50,
    '--first-layer-neurons': 300,
    '--second-layer-neurons': 100,
    '--learning-rate': 0.01
}

est = TensorFlow(source_directory=script_folder,
                 entry_script='tf_mnist.py',
                 script_params=script_params,
                 compute_target=compute_target,
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

- **拡大縮小**:Batch AI クラスターで実行の実施に現在使用できる数よりも多くのノードが必要な場合、スケールアップが試行されます。

- **Running**: script フォルダー内のすべてのスクリプトがコンピューティング先にアップロードされ、データ ストアがマウントまたはコピーされ、entry_script が実行されます。 stdout および ./logs フォルダーの出力は実行履歴にストリーミングされ、実行を監視するために使用できます。

- **後処理**:実行の ./outputs フォルダーが実行履歴にコピーされます。

## <a name="register-or-download-a-model"></a>モデルを登録またはダウンロードする

モデルのトレーニングが終わったら、それをワークスペースに登録できます。 モデルの登録を使用すると、モデルをワークスペースに格納し、バージョン管理して、[モデルの管理とデプロイ](concept-model-management-and-deployment.md)を簡単にすることができます。

```Python
model = run.register_model(model_name='tf-dnn-mnist', model_path='outputs/model')
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

[`TensorFlow`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) 推定器は、CPU と GPU のクラスターでの分散トレーニングもサポートしています。 分散 TensorFlow ジョブは簡単に実行できます。また、オーケストレーションの管理は Azure Machine Learning service で自動的に行われます。

Azure Machine Learning service では、TensorFlow での分散トレーニング方法として次の 2 つがサポートされます。

- [MPI ベースの](https://www.open-mpi.org/)分散トレーニング ([Horovod](https://github.com/uber/horovod) フレームワークを使用)
- ネイティブの[分散 TensorFlow](https://www.tensorflow.org/deploy/distributed) (パラメーター サーバー方式を使用)

### <a name="horovod"></a>Horovod

[Horovod](https://github.com/uber/horovod) は、分散トレーニング用のオープンソース フレームワークとして Uber が開発したものです。 これを利用すると、分散 GPU TensorFlow ジョブを簡単に実行できます。

Horovod を使用するには、TensorFlow コンストラクターの `distributed_training` パラメーターに [`MpiConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) オブジェクトを指定します。 このパラメーターによって、トレーニング スクリプトで使用する Horovod ライブラリが確実にインストールされます。

```Python
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
                      use_gpu=True)
```

### <a name="parameter-server"></a>パラメーター サーバー

[ネイティブの分散 TensorFlow](https://www.tensorflow.org/deploy/distributed) を実行することもできます｡その場合は､パラメーター サーバー モデルを使用します。 この方法では、パラメーター サーバーとワーカーからなるクラスター全体でトレーニングが行われます。 トレーニング中､ワーカーがグラディエントの計算する一方､パラメーター サーバーはグラディエントを集計します｡

パラメーター サーバー メソッドを使用するには、TensorFlow コンストラクターの `distributed_training` パラメーターに [`TensorflowConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration?view=azure-ml-py) オブジェクトを指定します。

```Python
from azureml.train.dnn import TensorFlow

distributed_training = TensorflowConfiguration()
distributed_training.worker_count = 2

# Tensorflow constructor
estimator= TensorFlow(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=distributed_training,
                      use_gpu=True)

# submit the TensorFlow job
run = exp.submit(tf_est)
```

#### <a name="define-cluster-specifications-in-tfconfig"></a>'TF_CONFIG' でクラスター仕様を定義する

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

## <a name="next-steps"></a>次の手順

この記事では、TensorFlow モデルのトレーニングと登録を行いました。 モデルを GPU 対応クラスターにデプロイする方法については、GPU モデル デプロイの記事に進んでください。

[GPU を使用した推論のためにデプロイする方法](how-to-deploy-inferencing-gpus.md)
[Tensorboard を使用して監視する方法](how-to-monitor-tensorboard.md)
