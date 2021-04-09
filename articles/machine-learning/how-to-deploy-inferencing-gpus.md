---
title: GPU を使用した推論のためのモデルをデプロイする
titleSuffix: Azure Machine Learning
description: この記事では、Azure Machine Learning を使用して、GPU 対応の Tensorflow ディープ ラーニング モデルを Web サービスとしてデプロイし、推論要求をスコア付けする方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: vaidyas
author: csteegz
ms.reviewer: larryfr
ms.date: 06/17/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, deploy
ms.openlocfilehash: 6797c32ded5c12bbac3fafa1eabd1e6f74d28e07
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102519250"
---
# <a name="deploy-a-deep-learning-model-for-inference-with-gpu"></a>GPU を使用した推論のためのディープ ラーニング モデルをデプロイする


この記事では、Azure Machine Learning を使用して GPU 対応のモデルを Web サービスとしてデプロイする方法について説明します。 この記事の情報は、Azure Kubernetes Service (AKS) へのモデルのデプロイに基づいています。 AKS クラスターは、モデルが推論に使用する GPU リソースを提供します。

推論、つまりモデルによるスコア付けは、デプロイしたモデルを使用して予測を行うフェーズです。 CPU の代わりに GPU を使用すると、高度に並列化可能な計算によってパフォーマンスが向上します。

> [!IMPORTANT]
> Web サービスのデプロイでは、GPU による推論がサポートされるのは、Azure Kubernetes Service のみです。 __機械学習パイプライン__ を使用した推論では、GPU は Azure Machine Learning コンピューティングでのみサポートされます。 ML パイプラインの使用方法の詳細については、「[チュートリアル:バッチ スコアリング用の Azure Machine Learning パイプラインを作成する](tutorial-pipeline-batch-scoring-classification.md)」に従います。 

> [!TIP]
> この記事のコード スニペットでは TensorFlow モデルが使用されていますが、ここに記載されている情報は GPU をサポートする任意の機械学習フレームワークに適用できます。

> [!NOTE]
> この記事の情報は、[Azure Kubernetes Service にデプロイする方法](how-to-deploy-azure-kubernetes-service.md)に関する記事の情報に基づいています。 これは、AKS へのデプロイの概要を説明したものですが、本記事では、GPU 固有のデプロイについて説明します。

## <a name="prerequisites"></a>前提条件

* Azure Machine Learning ワークスペース。 詳細については、[Azure Machine Learning ワークスペースの作成](how-to-manage-workspace.md)に関するページをご覧ください。

* Azure Machine Learning SDK がインストールされた Python 開発環境。 詳細については、[Azure Machine Learning Web サービス](/python/api/overview/azure/ml/install)に関するページを参照してください。  

* GPU を使用する登録済みのモデル。

    * モデルを登録する方法については、[モデルのデプロイ](how-to-deploy-and-where.md#registermodel)に関するページを参照してください。

    * このドキュメントの作成に使用された TensorFlow モデルを作成して登録するには、[TensorFlow モデルのトレーニング方法](how-to-train-tensorflow.md)に関するページをご覧ください。

* [モデルをデプロイする方法と場所](how-to-deploy-and-where.md)についての一般的な理解。

## <a name="connect-to-your-workspace"></a>ワークスペースに接続する

既存のワークスペースに接続するには、次のコードを使用します。

> [!IMPORTANT]
> このコード スニペットでは、ワークスペースの構成が現在のディレクトリまたはその親に保存されていることを想定しています。 ワークスペースの作成方法について詳しくは、[Azure Machine Learning ワークスペースを作成し、管理する](how-to-manage-workspace.md)方法に関するページを参照してください。   構成をファイルに保存する方法について詳しくは、「[ワークスペース構成ファイルを作成する](how-to-configure-environment.md#workspace)」を参照してください。

```python
from azureml.core import Workspace

# Connect to the workspace
ws = Workspace.from_config()
```

## <a name="create-a-kubernetes-cluster-with-gpus"></a>GPU を備えた Kubernetes クラスターを作成する

Azure Kubernetes Service には、さまざまな GPU オプションが用意されています。 そのいずれも、モデルによる推論に使用できます。 機能とコストの完全な内訳については、[N シリーズ VM の一覧](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#n-series)を参照してください。

次のコードは、ワークスペース用に新しい AKS クラスターを作成する方法を示しています。

```python
from azureml.core.compute import ComputeTarget, AksCompute
from azureml.exceptions import ComputeTargetException

# Choose a name for your cluster
aks_name = "aks-gpu"

# Check to see if the cluster already exists
try:
    aks_target = ComputeTarget(workspace=ws, name=aks_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    # Provision AKS cluster with GPU machine
    prov_config = AksCompute.provisioning_configuration(vm_size="Standard_NC6")

    # Create the cluster
    aks_target = ComputeTarget.create(
        workspace=ws, name=aks_name, provisioning_configuration=prov_config
    )

    aks_target.wait_for_completion(show_output=True)
```

> [!IMPORTANT]
> Azure では、AKS クラスターが存在する限り、課金が行われます。 使い終わったら、必ず自分の AKS クラスターを削除してください。

AKS と Azure Machine Learning の使用の詳細については、[Azure Kubernetes Service にデプロイする方法](how-to-deploy-azure-kubernetes-service.md)に関するページを参照してください。

## <a name="write-the-entry-script"></a>エントリ スクリプトを記述する

エントリ スクリプトは、Web サービスに送信されたデータを受け取り、それをモデルに渡して、スコア付けの結果を返します。 次のスクリプトでは、起動時に Tensorflow モデルを読み込み、モデルを使用してデータをスコア付けします。

> [!TIP]
> エントリ スクリプトは、モデルに固有のものです。 たとえば、スクリプトは、モデルで使用するフレームワークやデータ形式などを認識している必要があります。

```python
import json
import numpy as np
import os
import tensorflow as tf

from azureml.core.model import Model


def init():
    global X, output, sess
    tf.reset_default_graph()
    model_root = os.getenv('AZUREML_MODEL_DIR')
    # the name of the folder in which to look for tensorflow model files
    tf_model_folder = 'model'
    saver = tf.train.import_meta_graph(
        os.path.join(model_root, tf_model_folder, 'mnist-tf.model.meta'))
    X = tf.get_default_graph().get_tensor_by_name("network/X:0")
    output = tf.get_default_graph().get_tensor_by_name("network/output/MatMul:0")

    sess = tf.Session()
    saver.restore(sess, os.path.join(model_root, tf_model_folder, 'mnist-tf.model'))


def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    out = output.eval(session=sess, feed_dict={X: data})
    y_hat = np.argmax(out, axis=1)
    return y_hat.tolist()
```

このファイルの名前は `score.py` です。 エントリ スクリプトの詳細については、[デプロイする方法と場所](how-to-deploy-and-where.md)に関するページをご覧ください。

## <a name="define-the-conda-environment"></a>Conda 環境を定義する

Conda 環境ファイルによって、サービスの依存関係が指定されます。 ここには、モデルとエントリ スクリプトの両方に必要な依存関係が含まれます。 バージョン 1.0.45 以上では、pip の依存関係として、azureml-defaults を指定する必要があります。これは、モデルを Web サービスとしてホストするために必要な機能が含まれているためです。 次の YAML では、Tensorflow モデルの環境が定義されます。 ここに指定されている `tensorflow-gpu` は、このデプロイで GPU を使用することを示しています。

```yaml
name: project_environment
dependencies:
  # The python interpreter version.
  # Currently Azure ML only supports 3.5.2 and later.
- python=3.6.2

- pip:
  # You must list azureml-defaults as a pip dependency
  - azureml-defaults>=1.0.45
  - numpy
  - tensorflow-gpu=1.12
channels:
- conda-forge
```

この例では、ファイルは `myenv.yml` として保存されます。

## <a name="define-the-deployment-configuration"></a>デプロイ構成を定義する

> [!IMPORTANT]
> AKS では、ポッドで GPU を共有することはできません。また、クラスター内の GPU と同じ数の GPU 対応の Web サービスのレプリカのみを使用できます。

デプロイ構成では、Web サービスの実行に使用する Azure Kubernetes Service 環境を定義します。

```python
from azureml.core.webservice import AksWebservice

gpu_aks_config = AksWebservice.deploy_configuration(autoscale_enabled=False,
                                                    num_replicas=3,
                                                    cpu_cores=2,
                                                    memory_gb=4)
```

詳細については、リファレンス ドキュメントで [AksService.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.akswebservice#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none--compute-target-name-none-) に関するページをご覧ください。

## <a name="define-the-inference-configuration"></a>推論構成を定義する

推論構成は、エントリ スクリプトと、GPU をサポートする Docker イメージを使用する環境オブジェクトを指し示します。 バージョン 1.0.45 以上では、pip の依存関係として、環境定義に使用される YAML ファイルで azureml-defaults を指定する必要があります。これは、モデルを Web サービスとしてホストするために必要な機能が含まれているためです。

```python
from azureml.core.model import InferenceConfig
from azureml.core.environment import Environment, DEFAULT_GPU_IMAGE

myenv = Environment.from_conda_specification(name="myenv", file_path="myenv.yml")
myenv.docker.base_image = DEFAULT_GPU_IMAGE
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
```

環境の詳細については、[トレーニングとデプロイのための環境の作成と管理](how-to-use-environments.md)に関する記事を参照してください。
詳細については、リファレンス ドキュメントで [InferenceConfig](/python/api/azureml-core/azureml.core.model.inferenceconfig) に関するページをご覧ください。

## <a name="deploy-the-model"></a>モデルをデプロイする

モデルを自分の AKS クラスターにデプロイし、それによってサービスが作成されるのを待ちます。

```python
from azureml.core.model import Model

# Name of the web service that is deployed
aks_service_name = 'aks-dnn-mnist'
# Get the registerd model
model = Model(ws, "tf-dnn-mnist")
# Deploy the model
aks_service = Model.deploy(ws,
                           models=[model],
                           inference_config=inference_config,
                           deployment_config=gpu_aks_config,
                           deployment_target=aks_target,
                           name=aks_service_name)

aks_service.wait_for_deployment(show_output=True)
print(aks_service.state)
```

詳細については、リファレンス ドキュメントで [Model](/python/api/azureml-core/azureml.core.model.model) に関するページをご覧ください。

## <a name="issue-a-sample-query-to-your-service"></a>サービスにサンプル クエリを発行する

デプロイされたモデルにテスト クエリを送信します。 モデルに jpeg 画像を送信すると、画像がスコア付けされます。 次のコード サンプルでは、テスト データをダウンロードし、ランダムにテスト イメージを選択してサービスに送信します。

```python
# Used to test your webservice
import os
import urllib
import gzip
import numpy as np
import struct
import requests

# load compressed MNIST gz files and return numpy arrays
def load_data(filename, label=False):
    with gzip.open(filename) as gz:
        struct.unpack('I', gz.read(4))
        n_items = struct.unpack('>I', gz.read(4))
        if not label:
            n_rows = struct.unpack('>I', gz.read(4))[0]
            n_cols = struct.unpack('>I', gz.read(4))[0]
            res = np.frombuffer(gz.read(n_items[0] * n_rows * n_cols), dtype=np.uint8)
            res = res.reshape(n_items[0], n_rows * n_cols)
        else:
            res = np.frombuffer(gz.read(n_items[0]), dtype=np.uint8)
            res = res.reshape(n_items[0], 1)
    return res

# one-hot encode a 1-D array
def one_hot_encode(array, num_of_classes):
    return np.eye(num_of_classes)[array.reshape(-1)]

# Download test data
os.makedirs('./data/mnist', exist_ok=True)
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz', filename='./data/mnist/test-images.gz')
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz', filename='./data/mnist/test-labels.gz')

# Load test data from model training
X_test = load_data('./data/mnist/test-images.gz', False) / 255.0
y_test = load_data('./data/mnist/test-labels.gz', True).reshape(-1)

# send a random row from the test set to score
random_index = np.random.randint(0, len(X_test)-1)
input_data = "{\"data\": [" + str(list(X_test[random_index])) + "]}"

api_key = aks_service.get_keys()[0]
headers = {'Content-Type': 'application/json',
           'Authorization': ('Bearer ' + api_key)}
resp = requests.post(aks_service.scoring_uri, input_data, headers=headers)

print("POST to url", aks_service.scoring_uri)
print("label:", y_test[random_index])
print("prediction:", resp.text)
```

クライアント アプリケーションの作成方法の詳細については、[デプロイされた Web サービスを使用するクライアントの作成](how-to-consume-web-service.md)に関するページをご覧ください。

## <a name="clean-up-the-resources"></a>リソースのクリーンアップ

この例専用に AKS クラスターを作成した場合は、完了後にリソースを削除します。

> [!IMPORTANT]
> Azure では、AKS クラスターがデプロイされている時間に基づいて課金が行われます。 これが完了した後は必ずクリーンアップしてください。

```python
aks_service.delete()
aks_target.delete()
```

## <a name="next-steps"></a>次のステップ

* [FPGA でモデルをデプロイする](how-to-deploy-fpga-web-service.md)
* [ONNX を使用してモデルをデプロイする](concept-onnx.md#deploy-onnx-models-in-azure)
* [TensorFlow DNN モデルをトレーニングする](how-to-train-tensorflow.md)