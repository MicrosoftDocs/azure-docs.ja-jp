---
title: モデルの FPGA でのデプロイ
titleSuffix: Azure Machine Learning service
description: 待機時間が極端に短い推論のために、FPGA 上で実行されるモデルを含む Web サービスを Azure Machine Learning service でデプロイする方法を説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: tedway
author: tedway
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: 6cb9de60fe63c936da7340e6ec540a37163216f5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67074970"
---
# <a name="deploy-a-model-as-a-web-service-on-an-fpga-with-azure-machine-learning-service"></a>Azure Machine Learning service でモデルを Web サービスとして FPGA 上に配置する

Azure Machine Learning Hardware Accelerated Models を使用して、モデルを [FPGA (field programmable gate arrays)](concept-accelerate-with-fpgas.md) 上の Web サービスとしてデプロイできます。 FPGA を使用すると、単一のバッチ サイズでも、待機時間が極端に短い推論を実行できます。 推論、つまりモデル スコアリングとは、通常は運用環境のデータに基づいて、デプロイしたモデルを使用して予測を行うフェーズです。

現在使用できるのは、以下のモデルです。
  - ResNet 50
  - ResNet 152
  - DenseNet-121
  - VGG-16
  - SSD-VGG

FPGA は、次の Azure リージョンで使用できます。
  - 米国東部
  - 東南アジア
  - 西ヨーロッパ
  - 米国西部 2

> [!IMPORTANT]
> 待ち時間とスループットを最適化するには、FPGA モデルにデータを送信するクライアントが上記のいずれかのリージョン (モデルをデプロイするリージョン) にある必要があります。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション。  お持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning service](https://aka.ms/AMLFree) を今日からお試しいただけます。

- FPGA クォータ。  クォータがあるかどうかを確認するには、Azure CLI を使用します。
    ```shell
    az vm list-usage --location "eastus" -o table
    ```

    その他の場所は ``southeastasia``、``westeurope``、および ``westus2`` です。

    "Name" 列の下で "Standard PBS Family vCPUs" を探し、"CurrentValue" の下に少なくとも 6 つの vCPU があることを確認します。

    クォータがない場合は、要求フォームを[こちら](https://aka.ms/accelerateAI)から送信してください。

- Azure Machine Learning サービス ワークスペースと、Azure Machine Learning SDK for Python がインストール済み。 これらの前提条件を満たす方法については、[開発環境を構成する方法](how-to-configure-environment.md)に関するドキュメントを参照してください。
 
- Hardware Accelerated Models 用の Python SDK:

    ```shell
    pip install --upgrade azureml-accel-models
    ```

## <a name="sample-notebooks"></a>ノートブックのサンプル

次のような例の[ノートブックのサンプル](https://aka.ms/aml-accel-models-notebooks)が参考用に用意されています。

## <a name="create-and-containerize-your-model"></a>モデルを作成してコンテナー化する

このドキュメントでは、TensorFlow グラフを作成して入力画像を前処理し、それを FPGA 上で ResNet 50 を使用する特徴抽出器にした後、ImageNet データ セットでトレーニング済みの分類子を使って機能を実行する方法について説明します。

手順に従って次の操作を行います。

* TensorFlow モデルを定義する
* モデルを変換する
* モデルをデプロイする
* 配置したモデルを使用する
* 展開したサービスを削除する

### <a name="load-azure-ml-workspace"></a>Azure ML ワークスペースを読み込む

Azure ML ワークスペースを読み込みます。

```python
import os
import tensorflow as tf
 
from azureml.core import Workspace

ws = Workspace.from_config()
print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep = '\n')
```

### <a name="preprocess-image"></a>イメージの前処理

Web サービスへの入力は JPEG 画像です。  最初の手順では、JPEG 画像をデコードして前処理します。  JPEG 画像は文字列として扱われ、その結果は ResNet 50 モデルへの入力となるテンソルです。

```python
# Input images as a two-dimensional tensor containing an arbitrary number of images represented a strings
import azureml.accel.models.utils as utils
tf.reset_default_graph()

in_images = tf.placeholder(tf.string)
image_tensors = utils.preprocess_array(in_images)
print(image_tensors.shape)
```

### <a name="load-featurizer"></a>特徴抽出器を読み込む

モデルを初期化して、特徴抽出器として使用される ResNet50 の量子化されたバージョンの TensorFlow チェックポイントをダウンロードします。  以下のコード スニペットの "QuantizedResnet50" は、以下に示す他のディープ ニューラル ネットワークをインポートすることで置き換えることができます。

- QuantizedResnet152
- QuantizedVgg16
- Densenet121

```python
from azureml.accel.models import QuantizedResnet50
save_path = os.path.expanduser('~/models')
model_graph = QuantizedResnet50(save_path, is_frozen = True)
feature_tensor = model_graph.import_graph_def(image_tensors)
print(model_graph.version)
print(feature_tensor.name)
print(feature_tensor.shape)
```

### <a name="add-classifier"></a>分類子を追加する

この分類子は ImageNet データ セットでトレーニングされています。  転移学習およびカスタマイズされた重みのトレーニングの例は、[サンプル ノートブック](https://aka.ms/aml-notebooks)のセットに含まれています。

```python
classifier_output = model_graph.get_default_classifier(feature_tensor)
print(classifier_output)
```

### <a name="save-the-model"></a>モデルを保存する

プリプロセッサ、ResNet 50 特徴抽出器、および分類子を読み込んだ後は、グラフと関連する変数をモデルとして保存します。

```python
model_name = "resnet50"
model_save_path = os.path.join(save_path, model_name)
print("Saving model in {}".format(model_save_path))

with tf.Session() as sess:
    model_graph.restore_weights(sess)
    tf.saved_model.simple_save(sess, model_save_path,
                                   inputs={'images': in_images},
                                   outputs={'output_alias': classifier_output})
```

### <a name="save-input-and-output-tensors"></a>入出力のテンソルを保存する
モデルの変換と推論には、前処理中に作成された入出力のテンソルと、分類器のステップが必要になります。

```python
input_tensors = in_images.name
output_tensors = classifier_output.name

print(input_tensors)
print(output_tensors)
```

> [!IMPORTANT]
> 入出力のテンソルは、モデルの変換と要求の推論で必要になるため、保存します。

使用可能なモデルとそれに対応する既定の分類器の出力テンソルは以下のとおりです。これは、既定の分類器を使用した場合に推論で使用します。

+ Resnet50、QuantizedResnet50
  ```
  output_tensors = "classifier_1/resnet_v1_50/predictions/Softmax:0"
  ```
+ Resnet152、QuantizedResnet152
  ```
  output_tensors = "classifier/resnet_v1_152/predictions/Softmax:0"
  ```
+ Densenet121、QuantizedDensenet121
  ```
  output_tensors = "classifier/densenet121/predictions/Softmax:0"
  ```
+ Vgg16、QuantizedVgg16
  ```
  output_tensors = "classifier/vgg_16/fc8/squeezed:0"
  ```
+ SsdVgg、QuantizedSsdVgg
  ```
  output_tensors = ['ssd_300_vgg/block4_box/Reshape_1:0', 'ssd_300_vgg/block7_box/Reshape_1:0', 'ssd_300_vgg/block8_box/Reshape_1:0', 'ssd_300_vgg/block9_box/Reshape_1:0', 'ssd_300_vgg/block10_box/Reshape_1:0', 'ssd_300_vgg/block11_box/Reshape_1:0', 'ssd_300_vgg/block4_box/Reshape:0', 'ssd_300_vgg/block7_box/Reshape:0', 'ssd_300_vgg/block8_box/Reshape:0', 'ssd_300_vgg/block9_box/Reshape:0', 'ssd_300_vgg/block10_box/Reshape:0', 'ssd_300_vgg/block11_box/Reshape:0']
  ```

### <a name="register-model"></a>モデルの登録

作成したモデルを[登録](./concept-model-management-and-deployment.md)します。  モデルに関するタグやその他のメタデータを追加すると、トレーニング済みのモデルを追跡するのに役立ちます。

```python
from azureml.core.model import Model

registered_model = Model.register(workspace = ws,
                                  model_path = model_save_path,
                                  model_name = model_name)

print("Successfully registered: ", registered_model.name, registered_model.description, registered_model.version, sep = '\t')
```

既に登録済みのモデルがありそれを読み込む場合は、そのモデルを取得することができます。

```python
from azureml.core.model import Model
model_name = "resnet50"
# By default, the latest version is retrieved. You can specify the version, i.e. version=1
registered_model = Model(ws, name="resnet50")
print(registered_model.name, registered_model.description, registered_model.version, sep = '\t')
```

### <a name="convert-model"></a>モデルを変換する

TensorFlow グラフは、Open Neural Network Exchange 形式 ([ONNX](https://onnx.ai/)) に変換します。  入力テンソルと出力テンソルの名前を入力する必要があります。これらの名前は、Web サービスの使用時にクライアントによって使用されます。

```python
from azureml.accel import AccelOnnxConverter

convert_request = AccelOnnxConverter.convert_tf_model(ws, registered_model, input_tensors, output_tensors)

# If it fails, you can run wait_for_completion again with show_output=True.
convert_request.wait_for_completion(show_output = False)

# If the above call succeeded, get the converted model
converted_model = convert_request.result
print("\nSuccessfully converted: ", converted_model.name, converted_model.url, converted_model.version, 
      converted_model.id, converted_model.created_time, '\n')
```

### <a name="create-docker-image"></a>Docker イメージを作成する

変換されたモデルとすべての依存関係を Docker イメージに追加します。  この Docker イメージをデプロイし、インスタンス化できるようになります。  サポートされているデプロイ先には、クラウド内の AKS や [Azure Data Box Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview) のようなエッジ デバイスなどがあります。  登録した Docker イメージにタグと説明を追加することもできます。

```python
from azureml.core.image import Image
from azureml.accel import AccelContainerImage

image_config = AccelContainerImage.image_configuration()
# Image name must be lowercase
image_name = "{}-image".format(model_name)

image = Image.create(name = image_name,
                     models = [converted_model],
                     image_config = image_config, 
                     workspace = ws)
image.wait_for_creation(show_output = False)
```

タグ別にイメージを一覧表示し、デバッグ用に詳細ログを取得します。

```python
for i in Image.list(workspace = ws):
    print('{}(v.{} [{}]) stored at {} with build log {}'.format(i.name, i.version, i.creation_state, i.image_location, i.image_build_log_uri))
```

## <a name="model-deployment"></a>モデル デプロイ

### <a name="deploy-to-the-cloud"></a>クラウドにデプロイする

お客様のモデルを高スケールの運用 Web サービスとしてデプロイするには、Azure Kubernetes Service (AKS) を使用します。 Azure Machine Learning SDK、CLI、または Azure portal を使用して、新規に作成できます。

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Specify the Standard_PB6s Azure VM
prov_config = AksCompute.provisioning_configuration(vm_size = "Standard_PB6s",
                                                    agent_count = 1)

aks_name = 'my-aks-cluster'
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws, 
                                  name = aks_name, 
                                  provisioning_configuration = prov_config)
```

AKS のデプロイは、約 15 分かかります。  デプロイが成功したことを確認します。

```python
aks_target.wait_for_completion(show_output = True)
print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```

AKS クラスターにコンテナーをデプロイします。
```python
from azureml.core.webservice import Webservice, AksWebservice

# For this deployment, set the web service configuration without enabling auto-scaling or authentication for testing
aks_config = AksWebservice.deploy_configuration(autoscale_enabled=False,
                                                num_replicas=1,
                                                auth_enabled = False)

aks_service_name ='my-aks-service'

aks_service = Webservice.deploy_from_image(workspace = ws,
                                           name = aks_service_name,
                                           image = image,
                                           deployment_config = aks_config,
                                           deployment_target = aks_target)
aks_service.wait_for_deployment(show_output = True)
```

#### <a name="test-the-cloud-service"></a>クラウド サービスをテストする
Docker イメージは、gRPC と TensorFlow Serving の "予測" API をサポートしています。  サンプル クライアントを使用して Docker イメージを呼び出し、モデルから予測を取得します。  次のサンプル クライアント コードが用意されています。
- [Python](https://github.com/Azure/aml-real-time-ai/blob/master/pythonlib/amlrealtimeai/client.py)
- [C#](https://github.com/Azure/aml-real-time-ai/blob/master/sample-clients/csharp)

TensorFlow Serving を使用する場合は、[サンプル クライアントをダウンロード](https://www.tensorflow.org/serving/setup)できます。

```python
# Using the grpc client in Azure ML Accelerated Models SDK package
from azureml.accel import PredictionClient

address = aks_service.scoring_uri
ssl_enabled = address.startswith("https")
address = address[address.find('/')+2:].strip('/')
port = 443 if ssl_enabled else 80

# Initialize AzureML Accelerated Models client
client = PredictionClient(address=address,
                          port=port,
                          use_ssl=ssl_enabled,
                          service_name=aks_service.name)
```

この分類器は [ImageNet](http://www.image-net.org/) データ セット上でトレーニングされているため、人間が判読できるラベルにクラスをマップします。

```python
import requests
classes_entries = requests.get("https://raw.githubusercontent.com/Lasagne/Recipes/master/examples/resnet50/imagenet_classes.txt").text.splitlines()

# Score image with input and output tensor names
results = client.score_file(path="./snowleopardgaze.jpg", 
                             input_name=input_tensors, 
                             outputs=output_tensors)

# map results [class_id] => [confidence]
results = enumerate(results)
# sort results by confidence
sorted_results = sorted(results, key=lambda x: x[1], reverse=True)
# print top 5 results
for top in sorted_results[:5]:
    print(classes_entries[top[0]], 'confidence:', top[1])
```

### <a name="clean-up-the-service"></a>サービスをクリーンアップする
Web サービス、イメージ、およびモデルを削除します (依存関係があるため、この順序で実行する必要があります)。

```python
aks_service.delete()
aks_target.delete()
image.delete()
registered_model.delete()
converted_model.delete()
```

## <a name="deploy-to-a-local-edge-server"></a>ローカル エッジ サーバーにデプロイする

すべての [Azure Data Box Edge デバイス](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview
)には、モデルを実行するための FPGA が含まれています。  FPGA 上で一度に実行できるモデルは 1 つだけです。  別のモデルを実行するには、単に新しいコンテナーをデプロイします。 手順とサンプル コードは、[この Azure サンプル](https://github.com/Azure-Samples/aml-hardware-accelerated-models)に含まれています。

## <a name="secure-fpga-web-services"></a>FPGA の Web サービスをセキュリティで保護する

FPGA の Web サービスのセキュリティ保護については、[Web サービスのセキュリティ保護](how-to-secure-web-service.md)に関するドキュメントを参照してください。

## <a name="pbs-family-vms"></a>PBS ファミリの VM

Azure VM の PBS ファミリには、Intel Arria 10 FPGA が含まれています。  Azure のクォータの割り当てを確認すると、"Standard PBS Family vCPUs" と表示されます。  PB6 VM には 6 つの vCPU と 1 つの FPGA が含まれており、FPGA へのモデルのデプロイの一環として Azure ML によって自動的にプロビジョニングされます。  Azure ML でのみ使用され、任意のビットストリームは実行できません。  たとえば、暗号化、エンコードなどを行うビットストリームで FPGA をフラッシュすることはできません。 
