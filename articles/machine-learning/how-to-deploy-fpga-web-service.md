---
title: ML モデルを FPGA にデプロイする
titleSuffix: Azure Machine Learning
description: フィールド プログラマブル ゲート アレイについて説明します。 Azure Machine Learning を使用して FPGA に Web サービスをデプロイし、待機時間が非常に短い推論を行うことができます。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: jordane
author: jpe316
ms.date: 09/24/2020
ms.topic: conceptual
ms.custom: how-to, contperf-fy21q2, devx-track-python, deploy
ms.openlocfilehash: e6a58a6555602af2494683037721a1f83e7ea33c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102519318"
---
# <a name="deploy-ml-models-to-field-programmable-gate-arrays-fpgas-with-azure-machine-learning"></a>Azure Machine Learning を使用して ML モデルをフィールド プログラマブル ゲート アレイ (FPGA) にデプロイする 

この記事では、FPGA と、[Azure Machine Learning](overview-what-is-azure-ml.md) の[Hardware Accelerated Models Python パッケージ](/python/api/azureml-accel-models/azureml.accel)を使用して ML モデルを Azure FPGA にデプロイする方法について説明します。

## <a name="what-are-fpgas"></a>FPGA とは
FPGA には、プログラミング可能なロジック ブロックの配列と、再構成可能な相互接続の階層が含まれています。 相互接続を使用して、製造後にさまざまな方法でこれらのブロックを構成できます。 他のチップと比較して、FPGA はプログラミング可能であることとパフォーマンスを兼ね備えています。 

FPGA によって、リアルタイムの推論 (つまりモデルのスコアリング) 要求に対して短い待機時間を達成できるようになります。 非同期の要求 (バッチ処理) は必要ありません。 バッチ処理では、処理の必要なデータが多くなるため待機時間は長くなる場合があります。 ニューラル処理ユニットの実装にバッチ処理は必要ないので、待機時間は CPU および GPU プロセッサと比較して何倍も短くなる場合があります。

さまざまな種類の機械学習モデルに向けて FPGA を再構成できます。 この柔軟性により、最適な数値精度と使用されているメモリ モデルに基づいて、容易にアプリケーションを高速化できるようになっています。 FPGA は再構成可能なため、急速に変化する AI アルゴリズムの要件に遅れることなく対応できます。

![Azure Machine Learning における FPGA の比較図](./media/how-to-deploy-fpga-web-service/azure-machine-learning-fpga-comparison.png)

|プロセッサ| 省略形 |説明|
|---|:-------:|------|
|特定用途向け集積回路|ASIC|Google の Tensor Processor Unit (TPU) などのカスタム回路は、最高の効率性を提供します。 ニーズに変化に応じて再構成することはできません。|
|フィールド プログラマブル ゲート アレイ|FPGA|FPGA (Azure で利用できるものなど) は、ASIC に近いパフォーマンスを提供します。 将来新しいロジックを実装するための柔軟性と再構成可能性も備えています。|
|グラフィックス処理装置|GPU|AI 計算用に人気のある選択肢です。 GPU が並列処理機能を提供し、CPU より画像のレンダリングを高速化します。|
|中央処理装置|CPU|汎用プロセッサであり、そのパフォーマンスはグラフィックスや動画の処理に最適ではありません。|

## <a name="fpga-support-in-azure"></a>Azure での FPGA のサポート

Microsoft Azure は、世界最大規模の、FPGA 分野でのクラウド関連投資です。 Microsoft は、ディープ ニューラル ネットワーク (DNN) 評価、Bing 検索ランキング、およびソフトウェア定義ネットワーク (SDN) アクセラレーション用に FPGA を使用して、待機時間を短縮しながら、他のタスク用に CPU を解放します。

Azure の FPGA は、リアルタイムの AI 計算を高速化するためにデータ サイエンティストや開発者が使用する、Intel の FPGA デバイスをベースにしています。 この FPGA 対応のアーキテクチャでは、パフォーマンス、柔軟性、スケールが提供され、Azure で使用できます。

Azure FPGA は Azure Machine Learning と統合されています。 Azure では、事前にトレーニングされた DNN を FPGA 間で並列化して、サービスをスケールアウトできます。 DNN は、転移学習用のディープ特徴抽出器として事前トレーニングすることも、または更新された重みで微調整することもできます。

|Azure のシナリオと構成|サポートされている DNN モデル|地域のサポート|
|--------------------------|--------------------|----------------|
|+ 画像の分類と認識のシナリオ<br/>+ TensorFlow のデプロイ (TensorFlow 1.x が必要)<br/>+ Intel FPGA ハードウェア|- ResNet 50<br/>- ResNet 152<br/>- DenseNet-121<br/>- VGG-16<br/>- SSD-VGG|- 米国東部<br/>- 東南アジア<br/>- 西ヨーロッパ<br/>- 米国西部 2|

待ち時間とスループットを最適化するには、FPGA モデルにデータを送信するクライアントが上記のいずれかのリージョン (モデルをデプロイするリージョン) にある必要があります。

**Azure VM の PBS ファミリ** には、Intel Arria 10 FPGA が含まれています。 Azure のクォータの割り当てを確認すると、"Standard PBS Family vCPUs" と表示されます。 PB6 VM には、6 つの vCPUs と 1 つの FPGA が搭載されています。 PB6 VM は、FPGA へのモデル デプロイ中に Azure Machine Learning によって自動的にプロビジョニングされます。 Azure ML でのみ使用され、任意のビットストリームは実行できません。 たとえば、暗号化、エンコードなどを行うビットストリームで FPGA をフラッシュすることはできません。

## <a name="deploy-models-on-fpgas"></a>モデルの FPGA でのデプロイ

[Azure Machine Learning Hardware Accelerated Models](/python/api/azureml-accel-models/azureml.accel) を使用して、モデルを FPGA 上の Web サービスとしてデプロイできます。 FPGA を使用すると、単一のバッチ サイズでも、待機時間が極端に短い推論を実行できます。 

この例では、TensorFlow グラフを作成して入力画像を前処理し、それを FPGA 上で ResNet 50 を使用する特徴抽出器にした後、ImageNet データ セットでトレーニング済みの分類子を使って機能を実行します。 次に、モデルが AKS クラスターにデプロイされます。

### <a name="prerequisites"></a>前提条件

- Azure サブスクリプション。 お持ちでない場合は、[従量課金制](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go)アカウントを作成してください (無料の Azure アカウントは、FPGA クォータの対象になりません)。

- [ワークスペースの作成](how-to-manage-workspace.md)に関するページで説明されているように、Azure Machine Learning ワークスペースと Azure Machine Learning SDK for Python がインストールされています。
 
- Hardware Accelerated Models パッケージ: `pip install --upgrade azureml-accel-models[cpu]`    
    
- [Azure CLI](/cli/azure/install-azure-cli)

- FPGA クォータ。 [クォータの要求](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2nac9-PZhBDnNSV2ITz0LNUN0U5S0hXRkNITk85QURTWk9ZUUFUWkkyTC4u)を送信するか、次の CLI コマンドを実行してクォータを確認します。 

    ```azurecli-interactive
    az vm list-usage --location "eastus" -o table --query "[?localName=='Standard PBS Family vCPUs']"
    ```

   返される __CurrentValue__ に少なくとも 6 つの vCPU があることを確認します。  

### <a name="define-the-tensorflow-model"></a>TensorFlow モデルを定義する

まず [Azure Machine Learning SDK for Python](/python/api/overview/azure/ml/intro) を使用してサービス定義を作成します。 サービス定義は、TensorFlow に基づいてグラフ (入力、特徴抽出器、分類子) のパイプラインを記述しているファイルです。 デプロイ コマンドは、定義とグラフを ZIP ファイルに圧縮し、その ZIP を Azure Blob Storage にアップロードします。 DNN は、FPGA 上で実行するように既にデプロイされています。

1. Azure Machine Learning ワークスペースを読み込む

   ```python
   import os
   import tensorflow as tf
   
   from azureml.core import Workspace
  
   ws = Workspace.from_config()
   print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep='\n')
   ```

1. イメージの前処理を行います。 Web サービスへの入力は JPEG 画像です。  最初の手順では、JPEG 画像をデコードして前処理します。  JPEG 画像は文字列として扱われ、その結果は ResNet 50 モデルへの入力となるテンソルです。

   ```python
   # Input images as a two-dimensional tensor containing an arbitrary number of images represented a strings
   import azureml.accel.models.utils as utils
   tf.reset_default_graph()
   
   in_images = tf.placeholder(tf.string)
   image_tensors = utils.preprocess_array(in_images)
   print(image_tensors.shape)
   ```

1. 特徴抽出器を読み込みます。 モデルを初期化して、特徴抽出器として使用される ResNet50 の量子化されたバージョンの TensorFlow チェックポイントをダウンロードします。  他のディープ ニューラル ネットワークをインポートするには、コード スニペットの "QuantizedResnet50" を置き換えます。

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

1. 分類子を追加します。 この分類子は ImageNet データ セットでトレーニングされています。

   ```python
   classifier_output = model_graph.get_default_classifier(feature_tensor)
   print(classifier_output)
   ```

1. モデルを保存します。 プリプロセッサ、ResNet 50 特徴抽出器、および分類子を読み込んだ後は、グラフと関連する変数をモデルとして保存します。

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

1. **モデル変換と推論の要求に使用するため**、入出力のテンソルを保存します。 

   ```python
   input_tensors = in_images.name
   output_tensors = classifier_output.name

   print(input_tensors)
   print(output_tensors)
   ```

   既定の分類子を使用した場合、次のモデルが推論のために分類子の出力テンソルと共に表示され、使用できます。

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

### <a name="convert-the-model-to-the-open-neural-network-exchange-format-onnx"></a>モデルを Open Neural Network Exchange 形式 (ONNX) に変換する

FPGA にデプロイする前に、モデルを [ONNX](https://onnx.ai/) 形式に変換します。

1. SDK と Azure Blob Storage 内の ZIP ファイルを使用して、モデルを[登録](concept-model-management-and-deployment.md)します。 モデルに関するタグやその他のメタデータを追加すると、トレーニング済みのモデルを追跡するのに役立ちます。

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

1. TensorFlow グラフを ONNX 形式に変換します。  Web サービスを使用するときにクライアントから使用できるように、入出力テンソルの名前を指定する必要があります。

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

### <a name="containerize-and-deploy-the-model"></a>モデルのコンテナー化とデプロイ

次に、変換されたモデルとすべての依存関係から Docker イメージを作成します。  この Docker イメージをデプロイし、インスタンス化できるようになります。  サポートされているデプロイ先には、クラウド内の Azure Kubernetes Service (AKS) や [Azure Data Box Edge](../databox-online/azure-stack-edge-overview.md) のようなエッジ デバイスなどがあります。  登録した Docker イメージにタグと説明を追加することもできます。

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

#### <a name="deploy-to-an-azure-kubernetes-service-cluster"></a>Azure Kubernetes Service クラスターにデプロイする

1. 大規模な運用 Web サービスとしてモデルをデプロイするには、AKS を使用します。 Azure Machine Learning SDK、CLI、または [Azure Machine Learning Studio](https://ml.azure.com) を使用して、新規に作成できます。

    ```python
    from azureml.core.compute import AksCompute, ComputeTarget
    
    # Specify the Standard_PB6s Azure VM and location. Values for location may be "eastus", "southeastasia", "westeurope", or "westus2". If no value is specified, the default is "eastus".
    prov_config = AksCompute.provisioning_configuration(vm_size = "Standard_PB6s",
                                                        agent_count = 1,
                                                        location = "eastus")
    
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

1. AKS クラスターにコンテナーをデプロイします。

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

#### <a name="deploy-to-a-local-edge-server"></a>ローカル エッジ サーバーにデプロイする

すべての [Azure Data Box Edge デバイス](../databox-online/azure-stack-edge-overview.md
)には、モデルを実行するための FPGA が含まれています。  FPGA 上で一度に実行できるモデルは 1 つだけです。  別のモデルを実行するには、単に新しいコンテナーをデプロイします。 手順とサンプル コードは、[この Azure サンプル](https://github.com/Azure-Samples/aml-hardware-accelerated-models)に含まれています。

### <a name="consume-the-deployed-model"></a>配置したモデルを使用する

最後に、サンプル クライアントを使用して Docker イメージを呼び出し、モデルから予測を取得します。  次のサンプル クライアント コードが用意されています。
- [Python](https://github.com/Azure/aml-real-time-ai/blob/master/pythonlib/amlrealtimeai/client.py)
- [C#](https://github.com/Azure/aml-real-time-ai/blob/master/sample-clients/csharp)

Docker イメージは、gRPC と TensorFlow Serving の "予測" API をサポートしています。

TensorFlow Serving のサンプル クライアントをダウンロードすることもできます。

```python
# Using the grpc client in Azure ML Accelerated Models SDK package
from azureml.accel import PredictionClient

address = aks_service.scoring_uri
ssl_enabled = address.startswith("https")
address = address[address.find('/')+2:].strip('/')
port = 443 if ssl_enabled else 80

# Initialize Azure ML Accelerated Models client
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

### <a name="clean-up-resources"></a>リソースをクリーンアップする

不要なコストを回避するには、Web サービス、イメージ、モデルのリソースを **この順序で** クリーンアップします。

```python
aks_service.delete()
aks_target.delete()
image.delete()
registered_model.delete()
converted_model.delete()
```

## <a name="next-steps"></a>次の手順

+ [Web サービスをセキュリティで保護する](how-to-secure-web-service.md)方法のドキュメントを確認します。

+ FPGA と [Azure Machine Learning の価格とコスト](https://azure.microsoft.com/pricing/details/machine-learning/)の詳細について説明します。

+ [Hyperscale hardware:ML at scale on top of Azure + FPGA (ハイパースケール ハードウェア: Azure + FPGA 基盤の大規模 ML):Build 2018 (ビデオ)](https://channel9.msdn.com/events/Build/2018/BRK3202)

+ [Microsoft の FPGA ベースの構成可能なクラウド (ビデオ)](https://channel9.msdn.com/Events/Build/2017/B8063)

+ [リアルタイム AI 用の Project Brainwave](https://www.microsoft.com/research/project/project-brainwave/)

+ [自動化された光学的検査システム](https://blogs.microsoft.com/ai/build-2018-project-brainwave/)
