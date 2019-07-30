---
title: GPU を使用した推論のためのモデルをデプロイする
titleSuffix: Azure Machine Learning service
description: この記事では、Azure Machine Learning service を使用して、GPU 対応の Tensorflow ディープ ラーニング モデルを Web サービスとしてデプロイし、推論要求をスコア付けする方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: csteegz
ms.reviewer: larryfr
ms.date: 06/01/2019
ms.openlocfilehash: 8086d059913cc61bff0bca31681368bea6d76777
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543798"
---
# <a name="deploy-a-deep-learning-model-for-inference-with-gpu"></a>GPU を使用した推論のためのディープ ラーニング モデルをデプロイする

この記事では、Azure Machine Learning service を使用して GPU 対応の Tensorflow ディープ ラーニング モデルを Web サービスとしてデプロイする方法について説明します。

モデルを Azure Kubernetes Service (AKS) クラスターにデプロイして、GPU 対応の推論を行います。 推論、つまりモデルのスコア付けは、デプロイされたモデルが予測に使用されるフェーズです。 CPU の代わりに GPU を使用すると、高度に並列化可能な計算でパフォーマンスが向上します。

このサンプルでは TensorFlow モデルを使用しますが、スコアリング ファイルと環境ファイルにわずかな変更を加えることで、GPU をサポートするすべての機械学習フレームワークに次の手順を適用できます。 

この記事では、次の手順を実行します。

* GPU 対応 AKS クラスターを作成する
* Tensorflow GPU モデルをデプロイする
* デプロイされたモデルにサンプル クエリを発行する

## <a name="prerequisites"></a>前提条件

* Azure Machine Learning service ワークスペース。
* Python ディストリビューション。
* TensorFlow で保存された登録済みモデル。
    * モデルを登録する方法については、[モデルのデプロイ](../service/how-to-deploy-and-where.md#registermodel)に関するページを参照してください。

この一連のハウツー シリーズのパート 1 の [TensorFlow モデルのトレーニング方法](how-to-train-tensorflow.md)に関する記事を完了することで、必要な前提条件を満たすことができます。

## <a name="provision-an-aks-cluster-with-gpus"></a>GPU を備えた AKS クラスターをプロビジョニングする

Azure には、さまざまな GPU オプションが多数あります。 そのいずれも、推論に使用できます。 機能とコストの完全な内訳については、[N シリーズ VM の一覧](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#n-series)を参照してください。

AKS と Azure Machine Learning service の使用の詳細については、[デプロイの方法と場所](../service/how-to-deploy-and-where.md#deploy-aks)に関する記事を参照してください。

```Python
# Choose a name for your cluster
aks_name = "aks-gpu"

# Check to see if the cluster already exists
try:
    compute_target = ComputeTarget(workspace=ws, name=aks_name)
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
> Azure では、AKS クラスターがプロビジョニングがされている限り、課金が行われます。 使い終わったら、必ず自分の AKS クラスターを削除してください。

## <a name="write-the-entry-script"></a>エントリ スクリプトを記述する

次のコードを `score.py` として自分の作業ディレクトリに保存します。 画像が自分のサービスに送信されると、このファイルによって画像にスコアが付けられます。 TensorFlow で保存されたモデルが読み込まれ、各 POST 要求で入力画像が TensorFlow セッションに渡されて、結果のスコアが返されます。 他の推論フレームワークでは、異なるスコアリング ファイルが必要になります。

```python
import json
import numpy as np
import os
import tensorflow as tf

from azureml.core.model import Model

def init():
    global X, output, sess
    tf.reset_default_graph()
    model_root = Model.get_model_path('tf-dnn-mnist')
    saver = tf.train.import_meta_graph(os.path.join(model_root, 'mnist-tf.model.meta'))
    X = tf.get_default_graph().get_tensor_by_name("network/X:0")
    output = tf.get_default_graph().get_tensor_by_name("network/output/MatMul:0")
    
    sess = tf.Session()
    saver.restore(sess, os.path.join(model_root, 'mnist-tf.model'))

def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    out = output.eval(session=sess, feed_dict={X: data})
    y_hat = np.argmax(out, axis=1)
    return y_hat.tolist()

```
## <a name="define-the-conda-environment"></a>Conda 環境を定義する

`myenv.yml` という名前の Conda 環境ファイルを作成し、自分のサービスの依存関係を指定します。 パフォーマンスを高速化するには、`tensorflow-gpu` の使用を指定することが重要です。

```yaml
name: project_environment
dependencies:
  # The python interpreter version.
  # Currently Azure ML only supports 3.5.2 and later.
- python=3.6.2

- pip:
  - azureml-defaults==1.0.43.*
- numpy
- tensorflow-gpu=1.12
channels:
- conda-forge
```

## <a name="define-the-gpu-inferenceconfig-class"></a>GPU InferenceConfig クラスを定義する

GPU を有効にし、Docker イメージを使用して CUDA がインストールされるようにする `InferenceConfig` オブジェクトを作成します。

```python
from azureml.core.model import Model
from azureml.core.model import InferenceConfig

aks_service_name ='aks-dnn-mnist'
gpu_aks_config = AksWebservice.deploy_configuration(autoscale_enabled = False, 
                                                    num_replicas = 3, 
                                                    cpu_cores=2, 
                                                    memory_gb=4)
model = Model(ws,"tf-dnn-mnist")

inference_config = InferenceConfig(runtime= "python", 
                                   entry_script="score.py",
                                   conda_file="myenv.yml", 
                                   enable_gpu=True)
```

詳細については、次を参照してください。

- [InferenceConfig クラス](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py)
- [AksServiceDeploymentConfiguration クラス](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py)

## <a name="deploy-the-model"></a>モデルをデプロイする

モデルを自分の AKS クラスターにデプロイし、それによってサービスが作成されるのを待ちます。

```python
aks_service = Model.deploy(ws,
                           models=[model],
                           inference_config=inference_config, 
                           deployment_config=gpu_aks_config,
                           deployment_target=aks_target,
                           name=aks_service_name)

aks_service.wait_for_deployment(show_output = True)
print(aks_service.state)
```

> [!NOTE]
> Azure Machine Learning service では、GPU の有効化が想定される `InferenceConfig` オブジェクトがあるモデルは、GPU がないクラスターにデプロイされません。

詳細については、「[Model class (Model クラス)](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py)」を参照してください。

## <a name="issue-a-sample-query-to-your-model"></a>モデルにサンプル クエリを発行する

デプロイされたモデルにテスト クエリを送信します。 モデルに jpeg 画像を送信すると、画像がスコア付けされます。 次のコード サンプルでは、外部のユーティリティ関数を使用して画像を読み込みます。 関連するコードは [GitHub 上の pir TensorFlow サンプル](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow/utils.py)にあります。 

```python
# Used to test your webservice
from utils import load_data 

# Load test data from model training
X_test = load_data('./data/mnist/test-images.gz', False) / 255.0
y_test = load_data('./data/mnist/test-labels.gz', True).reshape(-1)

# send a random row from the test set to score
random_index = np.random.randint(0, len(X_test)-1)
input_data = "{\"data\": [" + str(list(X_test[random_index])) + "]}"

api_key = aks_service.get_keys()[0]
headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}
resp = requests.post(aks_service.scoring_uri, input_data, headers=headers)

print("POST to url", aks_service.scoring_uri)
#print("input data:", input_data)
print("label:", y_test[random_index])
print("prediction:", resp.text)
```

> [!IMPORTANT]
> 待機時間を最小化し、スループットを最適化するために、ご利用のクライアントは、必ずエンドポイントと同じ Azure リージョンに存在するようにします。 この例では、API は米国東部 Azure リージョンに作成されています。

## <a name="clean-up-the-resources"></a>リソースのクリーンアップ

この例を完了したら、リソースを削除します。

> [!IMPORTANT]
> Azure では、AKS クラスターがデプロイされている時間に基づいて課金が行われます。 これが完了した後は必ずクリーンアップしてください。

```python
aks_service.delete()
aks_target.delete()
```

## <a name="next-steps"></a>次の手順

* [FPGA でモデルをデプロイする](../service/how-to-deploy-fpga-web-service.md)
* [ONNX を使用してモデルをデプロイする](../service/concept-onnx.md#deploy-onnx-models-in-azure)
* [TensorFlow DNN モデルをトレーニングする](../service/how-to-train-tensorflow.md)
