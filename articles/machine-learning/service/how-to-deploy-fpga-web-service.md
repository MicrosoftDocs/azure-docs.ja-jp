---
title: FPGA とは - デプロイ方法
titleSuffix: Azure Machine Learning service
description: 待機時間が極端に短い推論のために、FPGA 上で実行されるモデルを含む Web サービスを Azure Machine Learning service でデプロイする方法を説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: tedway
author: tedway
ms.date: 07/25/2019
ms.custom: seodec18
ms.openlocfilehash: ff4259c438fec448ba510e4c248de6f4acc184ab
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2019
ms.locfileid: "68990503"
---
# <a name="what-are-field-programmable-gate-arrays-fpga-and-how-to-deploy"></a>フィールド プログラマブル ゲート アレイ (FPGA) の説明とデプロイ方法

この記事では、フィールド プログラマブル ゲート アレイ (FPGA) についてその概要を説明し、Azure Machine Learning service を使用してモデルを Azure FPGA にデプロイする方法を示します。 

FPGA には、プログラミング可能なロジック ブロックの配列と、再構成可能な相互接続の階層が含まれています。 相互接続を使用して、製造後にさまざまな方法でこれらのブロックを構成できます。 他のチップと比較して、FPGA はプログラミング可能であることとパフォーマンスを兼ね備えています。

## <a name="fpgas-vs-cpu-gpu-and-asic"></a>FPGA とCPU、GPU、ASIC の違い

次の図と表に、FPGA と他のプロセッサの比較を示します。

![Azure Machine Learning service における FPGA の比較図](./media/concept-accelerate-with-fpgas/azure-machine-learning-fpga-comparison.png)

|プロセッサ||説明|
|---|:-------:|------|
|特定用途向け集積回路|ASIC|Google の TensorFlow Processor Unit (TPU) などのカスタム回路は、最高の効率性を提供します。 ニーズに変化に応じて再構成することはできません。|
|フィールド プログラマブル ゲート アレイ|FPGA|FPGA (Azure で利用できるものなど) は、ASIC に近いパフォーマンスを提供します。 将来新しいロジックを実装するための柔軟性と再構成可能性も備えています。|
|グラフィックス処理装置|GPU|AI 計算用に人気のある選択肢です。 GPU が並列処理機能を提供し、CPU より画像のレンダリングを高速化します。|
|中央処理装置|CPU|汎用プロセッサであり、そのパフォーマンスはグラフィックスや動画の処理に最適ではありません。|

Azure の FPGA は、リアルタイムの AI 計算を高速化するためにデータ サイエンティストや開発者が使用する、Intel の FPGA デバイスをベースにしています。 この FPGA 対応のアーキテクチャでは、パフォーマンス、柔軟性、スケールが提供され、Azure で使用できます。

FPGA によって、リアルタイムの推論 (つまりモデルのスコアリング) 要求に対して短い待機時間を達成できるようになります。 非同期の要求 (バッチ処理) は必要ありません。 バッチ処理では、処理の必要なデータが多くなるため待機時間は長くなる場合があります。 ニューラル処理ユニットの実装にバッチ処理は必要ないので、待機時間は CPU および GPU プロセッサと比較して何倍も短くなる場合があります。

### <a name="reconfigurable-power"></a>再構成可能な能力
さまざまな種類の機械学習モデルに向けて FPGA を再構成できます。 この柔軟性により、最適な数値精度と使用されているメモリ モデルに基づいて、容易にアプリケーションを高速化できるようになっています。 FPGA は再構成可能なため、急速に変化する AI アルゴリズムの要件に遅れることなく対応できます。

## <a name="whats-supported-on-azure"></a>Azure でサポートされるもの
Microsoft Azure は、世界最大規模の、FPGA 分野でのクラウド関連投資です。 この FPGA 対応のハードウェア アーキテクチャを使用すると、トレーニング済みのニューラル ネットワークは迅速かつ低遅延で実行します。 Azure は、事前トレーニング済みのディープ ニューラル ネットワーク (DNN) を FPGA 間で並列化し、サービスをスケールアウトすることができます。 DNN は、転移学習用のディープ特徴抽出器として事前トレーニングすることも、または更新された重みで微調整することもできます。

Azure の FPGA のサポート:

+ 画像の分類と認識のシナリオ
+ TensorFlow のデプロイ
+ Intel FPGA ハードウェア 

現在使用できるのは、以下の DNN モデルです。
  - ResNet 50
  - ResNet 152
  - DenseNet-121
  - VGG-16
  - SSD-VGG

FPGA は、次の Azure リージョンで使用できます。
  - East US
  - 東南アジア
  - 西ヨーロッパ
  - 米国西部 2

> [!IMPORTANT]
> 待ち時間とスループットを最適化するには、FPGA モデルにデータを送信するクライアントが上記のいずれかのリージョン (モデルをデプロイするリージョン) にある必要があります。

**Azure VM の PBS ファミリ**には、Intel Arria 10 FPGA が含まれています。 Azure のクォータの割り当てを確認すると、"Standard PBS Family vCPUs" と表示されます。 PB6 VM には 6 つの vCPU と 1 つの FPGA が含まれており、FPGA へのモデルのデプロイの一環として Azure ML によって自動的にプロビジョニングされます。 Azure ML でのみ使用され、任意のビットストリームは実行できません。 たとえば、暗号化、エンコードなどを行うビットストリームで FPGA をフラッシュすることはできません。

### <a name="scenarios-and-applications"></a>シナリオとアプリケーション

Azure FPGA は Azure Machine Learning と統合されています。 Microsoft は、DNN 評価、Bing 検索ランキング、およびソフトウェア定義ネットワーク (SDN) アクセラレーション用に FPGA を使用して、待機時間を短縮しながら、他のタスク用に CPU を解放します。

次のシナリオでは FPGA を使用します。
+ [自動化された光学的検査システム](https://blogs.microsoft.com/ai/build-2018-project-brainwave/)

+ [土地被覆マッピング](https://blogs.technet.microsoft.com/machinelearning/2018/05/29/how-to-use-fpgas-for-deep-learning-inference-to-perform-land-cover-mapping-on-terabytes-of-aerial-images/)



## <a name="example-deploy-models-on-fpgas"></a>例:モデルの FPGA でのデプロイ 

Azure Machine Learning Hardware Accelerated Models を使用して、モデルを FPGA 上の Web サービスとしてデプロイできます。 FPGA を使用すると、単一のバッチ サイズでも、待機時間が極端に短い推論を実行できます。 推論、つまりモデル スコアリングとは、通常は運用環境のデータに基づいて、デプロイしたモデルを使用して予測を行うフェーズです。


### <a name="prerequisites"></a>前提条件

- Azure サブスクリプション。  お持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning service](https://aka.ms/AMLFree) を今日からお試しいただけます。

- FPGA クォータ。 クォータがあるかどうかを確認するには、Azure CLI を使用します。

    ```shell
    az vm list-usage --location "eastus" -o table --query "[?localName=='Standard PBS Family vCPUs']"
    ```

    > [!TIP]
    > その他の候補の場所は、``southeastasia``、``westeurope``、および ``westus2`` です。

    コマンドにより、次のようなテキストが返されます。

    ```text
    CurrentValue    Limit    LocalName
    --------------  -------  -------------------------
    0               6        Standard PBS Family vCPUs
    ```

    __CurrentValue__ の下に少なくとも 6 つの vCPU があることを確認します。

    クォータがない場合は、[https://aka.ms/accelerateAI](https://aka.ms/accelerateAI) で要求を送信してください。

- Azure Machine Learning サービス ワークスペースと、Azure Machine Learning SDK for Python がインストール済み。 詳細については、「[ワークスペースの作成](how-to-manage-workspace.md)」を参照してください。
 
- Hardware Accelerated Models 用の Python SDK:

    ```shell
    pip install --upgrade azureml-accel-models
    ```


## <a name="1-create-and-containerize-models"></a>1.モデルを作成してコンテナー化する

このドキュメントでは、TensorFlow グラフを作成して入力画像を前処理し、それを FPGA 上で ResNet 50 を使用する特徴抽出器にした後、ImageNet データ セットでトレーニング済みの分類子を使って機能を実行する方法について説明します。

手順に従って次の操作を行います。

* TensorFlow モデルを定義する
* モデルを変換する
* モデルをデプロイする
* 配置したモデルを使用する
* 展開したサービスを削除する

[Azure Machine Learning SDK for Python](https://aka.ms/aml-sdk) を使用してサービス定義を作成します。 サービス定義は、TensorFlow に基づいてグラフ (入力、特徴抽出器、分類子) のパイプラインを記述しているファイルです。 デプロイ コマンドは、定義とグラフを ZIP ファイルに自動的に圧縮し、その ZIP を Azure Blob Storage にアップロードします。 DNN は、FPGA 上で実行するように既にデプロイされています。

### <a name="load-azure-ml-workspace"></a>Azure ML ワークスペースを読み込む

Azure ML ワークスペースを読み込みます。

```python
import os
import tensorflow as tf

from azureml.core import Workspace

ws = Workspace.from_config()
print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep='\n')
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
model_graph = QuantizedResnet50(save_path, is_frozen=True)
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
  ```python
  output_tensors = "classifier_1/resnet_v1_50/predictions/Softmax:0"
  ```
+ Resnet152、QuantizedResnet152
  ```python
  output_tensors = "classifier/resnet_v1_152/predictions/Softmax:0"
  ```
+ Densenet121、QuantizedDensenet121
  ```python
  output_tensors = "classifier/densenet121/predictions/Softmax:0"
  ```
+ Vgg16、QuantizedVgg16
  ```python
  output_tensors = "classifier/vgg_16/fc8/squeezed:0"
  ```
+ SsdVgg、QuantizedSsdVgg
  ```python
  output_tensors = ['ssd_300_vgg/block4_box/Reshape_1:0', 'ssd_300_vgg/block7_box/Reshape_1:0', 'ssd_300_vgg/block8_box/Reshape_1:0', 'ssd_300_vgg/block9_box/Reshape_1:0', 'ssd_300_vgg/block10_box/Reshape_1:0', 'ssd_300_vgg/block11_box/Reshape_1:0', 'ssd_300_vgg/block4_box/Reshape:0', 'ssd_300_vgg/block7_box/Reshape:0', 'ssd_300_vgg/block8_box/Reshape:0', 'ssd_300_vgg/block9_box/Reshape:0', 'ssd_300_vgg/block10_box/Reshape:0', 'ssd_300_vgg/block11_box/Reshape:0']
  ```

### <a name="register-model"></a>モデルの登録

SDK と Azure Blob Storage 内の ZIP ファイルを使用して、モデルを[登録](./concept-model-management-and-deployment.md)します。 モデルに関するタグやその他のメタデータを追加すると、トレーニング済みのモデルを追跡するのに役立ちます。

```python
from azureml.core.model import Model

registered_model = Model.register(workspace=ws,
                                  model_path=model_save_path,
                                  model_name=model_name)

print("Successfully registered: ", registered_model.name,
      registered_model.description, registered_model.version, sep='\t')
```

既に登録済みのモデルがありそれを読み込む場合は、そのモデルを取得することができます。

```python
from azureml.core.model import Model
model_name = "resnet50"
# By default, the latest version is retrieved. You can specify the version, i.e. version=1
registered_model = Model(ws, name="resnet50")
print(registered_model.name, registered_model.description,
      registered_model.version, sep='\t')
```

### <a name="convert-model"></a>モデルを変換する

TensorFlow グラフは、Open Neural Network Exchange 形式 ([ONNX](https://onnx.ai/)) に変換します。  入力テンソルと出力テンソルの名前を入力する必要があります。これらの名前は、Web サービスの使用時にクライアントによって使用されます。

```python
from azureml.accel import AccelOnnxConverter

convert_request = AccelOnnxConverter.convert_tf_model(
    ws, registered_model, input_tensors, output_tensors)

# If it fails, you can run wait_for_completion again with show_output=True.
convert_request.wait_for_completion(show_output=False)

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

image = Image.create(name=image_name,
                     models=[converted_model],
                     image_config=image_config,
                     workspace=ws)
image.wait_for_creation(show_output=False)
```

タグ別にイメージを一覧表示し、デバッグ用に詳細ログを取得します。

```python
for i in Image.list(workspace=ws):
    print('{}(v.{} [{}]) stored at {} with build log {}'.format(
        i.name, i.version, i.creation_state, i.image_location, i.image_build_log_uri))
```

## <a name="2-deploy-to-cloud-or-edge"></a>2.クラウドまたはエッジにデプロイする

### <a name="deploy-to-the-cloud"></a>クラウドにデプロイする

お客様のモデルを高スケールの運用 Web サービスとしてデプロイするには、Azure Kubernetes Service (AKS) を使用します。 Azure Machine Learning SDK、CLI、または Azure portal を使用して、新規に作成できます。

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Specify the Standard_PB6s Azure VM
prov_config = AksCompute.provisioning_configuration(vm_size="Standard_PB6s",
                                                    agent_count=1)

aks_name = 'my-aks-cluster'
# Create the cluster
aks_target = ComputeTarget.create(workspace=ws,
                                  name=aks_name,
                                  provisioning_configuration=prov_config)
```

AKS のデプロイは、約 15 分かかります。  デプロイが成功したことを確認します。

```python
aks_target.wait_for_completion(show_output=True)
print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```

AKS クラスターにコンテナーをデプロイします。
```python
from azureml.core.webservice import Webservice, AksWebservice

# For this deployment, set the web service configuration without enabling auto-scaling or authentication for testing
aks_config = AksWebservice.deploy_configuration(autoscale_enabled=False,
                                                num_replicas=1,
                                                auth_enabled=False)

aks_service_name = 'my-aks-service'

aks_service = Webservice.deploy_from_image(workspace=ws,
                                           name=aks_service_name,
                                           image=image,
                                           deployment_config=aks_config,
                                           deployment_target=aks_target)
aks_service.wait_for_deployment(show_output=True)
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
classes_entries = requests.get(
    "https://raw.githubusercontent.com/Lasagne/Recipes/master/examples/resnet50/imagenet_classes.txt").text.splitlines()

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

### <a name="deploy-to-a-local-edge-server"></a>ローカル エッジ サーバーにデプロイする

すべての [Azure Data Box Edge デバイス](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview
)には、モデルを実行するための FPGA が含まれています。  FPGA 上で一度に実行できるモデルは 1 つだけです。  別のモデルを実行するには、単に新しいコンテナーをデプロイします。 手順とサンプル コードは、[この Azure サンプル](https://github.com/Azure-Samples/aml-hardware-accelerated-models)に含まれています。

## <a name="secure-fpga-web-services"></a>FPGA の Web サービスをセキュリティで保護する

FPGA の Web サービスをセキュリティで保護するには、[Web サービスのセキュリティ保護](how-to-secure-web-service.md)に関するドキュメントを参照してください。

## <a name="next-steps"></a>次の手順

次のノートブック、ビデオ、およびブログをご覧ください。

+ さまざまな[サンプル ノートブック](https://aka.ms/aml-accel-models-notebooks)。

+ [Hyperscale hardware:ML at scale on top of Azure + FPGA (ハイパースケール ハードウェア: Azure + FPGA 基盤の大規模 ML):Build 2018 (ビデオ)](https://channel9.msdn.com/events/Build/2018/BRK3202)

+ [Microsoft の FPGA ベースの構成可能なクラウドについて (ビデオ)](https://channel9.msdn.com/Events/Build/2017/B8063)

+ [リアルタイム AI のための Project Brainwave: プロジェクト ホーム ページ](https://www.microsoft.com/research/project/project-brainwave/)
