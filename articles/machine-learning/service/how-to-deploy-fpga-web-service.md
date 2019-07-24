---
title: モデルの FPGA でのデプロイ
titleSuffix: Azure Machine Learning service
description: 待機時間が極端に短い推論のために、FPGA 上で実行されるモデルを含む Web サービスを Azure Machine Learning service でデプロイする方法を説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: tedway
author: tedway
ms.date: 1/29/2019
ms.custom: seodec18
ms.openlocfilehash: 7aa0e11ed47219829830369d17b300270d3fbffb
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2019
ms.locfileid: "58259416"
---
# <a name="deploy-a-model-as-a-web-service-on-an-fpga-with-azure-machine-learning-service"></a>Azure Machine Learning service でモデルを Web サービスとして FPGA 上に配置する

モデルを [FPGA (field programmable gate arrays)](concept-accelerate-with-fpgas.md) 上の Web サービスとしてデプロイできます。  FPGA を使用すると、単一のバッチ サイズでも、待機時間が極端に短い推論を実行できます。  現在使用できるのは、以下のモデルです。
  - ResNet 50
  - ResNet 152
  - DenseNet-121
  - VGG-16   

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning service](https://aka.ms/AMLFree) を今日からお試しいただけます。

- Azure Machine Learning サービス ワークスペースと、Azure Machine Learning SDK for Python がインストール済み。 これらの前提条件を満たす方法については、[開発環境を構成する方法](how-to-configure-environment.md)に関するドキュメントを参照してください。
 
  - ご利用のワークステーションが、*米国東部 2* リージョン内にある必要があります。

  - contrib extras をインストールします。

    ```shell
    pip install --upgrade azureml-sdk[contrib]
    ```

  - 現在、tensorflow バージョン 1.10 以下だけがサポートされているため、他のすべてのインストールが完了した後で、それをインストールしてください。

    ```shell
    pip install "tensorflow==1.10"
    ```

## <a name="create-and-deploy-your-model"></a>モデルを作成してデプロイする
パイプラインを作成して入力イメージを前処理し、それを FPGA 上で ResNet 50 を使用する機能にした後、ImageNet データ セットでトレーニング済みの分類器を使って機能を実行します。

手順に従って次の操作を行います。

* モデル パイプラインを定義する
* モデルをデプロイする
* 配置したモデルを使用する
* 展開したサービスを削除する

> [!IMPORTANT]
> 待機時間とスループットを最適化するために、ご利用のクライアントは、エンドポイントと同じ Azure リージョンに存在する必要があります。  現時点では、この API は、Azure の米国東部リージョンに作成されています。



### <a name="preprocess-image"></a>イメージの前処理
パイプラインの最初のステージは、イメージを処理するためのものです。

```python
import os
import tensorflow as tf

# Input images as a two-dimensional tensor containing an arbitrary number of images represented a strings
import azureml.contrib.brainwave.models.utils as utils
in_images = tf.placeholder(tf.string)
image_tensors = utils.preprocess_array(in_images)
print(image_tensors.shape)
```

### <a name="add-featurizer"></a>Featurizer を追加する
モデルを初期化して、特徴抽出器として使用される ResNet50 の量子化されたバージョンの TensorFlow チェックポイントをダウンロードします。

```python
from azureml.contrib.brainwave.models import QuantizedResnet50
model_path = os.path.expanduser('~/models')
model = QuantizedResnet50(model_path, is_frozen = True)
feature_tensor = model.import_graph_def(image_tensors)
print(model.version)
print(feature_tensor.name)
print(feature_tensor.shape)
```

### <a name="add-classifier"></a>分類子を追加する
この分類子は ImageNet データ セットでトレーニングされています。

```python
classifier_output = model.get_default_classifier(feature_tensor)
```

### <a name="create-service-definition"></a>サービス定義を作成する
これで、イメージの前工程、特徴抽出器、およびサービスで実行する分類器を定義できたので、サービス定義を作成することができます。 サービス定義は、FPGA サービスにデプロイされているモデルから生成されたファイルのセットです。 サービス定義はパイプラインで構成されています。 パイプラインは順番に実行される一連のステージです。  TensorFlow ステージ、Keras ステージ、および BrainWave ステージがサポートされています。  ステージは、各ステージにおける出力が後続ステージへの入力となって、サービス上で順番に実行されます。

TensorFlow ステージを作成するには、グラフ (この場合は、既定のグラフが使用されます) およびこのステージへの入力テンソルと出力テンソルを含むセッションを指定します。  この情報は、サービス上で実行できるように、グラフを保存するために使用されます。

```python
from azureml.contrib.brainwave.pipeline import ModelDefinition, TensorflowStage, BrainWaveStage

save_path = os.path.expanduser('~/models/save')
model_def_path = os.path.join(save_path, 'model_def.zip')

model_def = ModelDefinition()
with tf.Session() as sess:
    model_def.pipeline.append(TensorflowStage(sess, in_images, image_tensors))
    model_def.pipeline.append(BrainWaveStage(sess, model))
    model_def.pipeline.append(TensorflowStage(sess, feature_tensor, classifier_output))
    model_def.save(model_def_path)
    print(model_def_path)
```

### <a name="deploy-model"></a>モデルをデプロイする
サービス定義からサービスを作成します。  ご利用のワークステーションが、米国東部 2 の場所にある必要があります。

```python
from azureml.core import Workspace

ws = Workspace.from_config()
print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep = '\n')

from azureml.core.model import Model
model_name = "resnet-50-rtai"
registered_model = Model.register(ws, model_def_path, model_name)

from azureml.core.webservice import Webservice
from azureml.exceptions import WebserviceException
from azureml.contrib.brainwave import BrainwaveWebservice, BrainwaveImage
service_name = "imagenet-infer"
service = None
try:
    service = Webservice(ws, service_name)
except WebserviceException:
    image_config = BrainwaveImage.image_configuration()
    deployment_config = BrainwaveWebservice.deploy_configuration()
    service = Webservice.deploy_from_model(ws, service_name, [registered_model], image_config, deployment_config)
    service.wait_for_deployment(True)
```

### <a name="test-the-service"></a>サービスをテストする
イメージを API に送信して応答をテストするには、出力クラス ID から ImageNet クラス名にマッピングを追加します。

```python
import requests
classes_entries = requests.get("https://raw.githubusercontent.com/Lasagne/Recipes/master/examples/resnet50/imagenet_classes.txt").text.splitlines()
```

ご利用のサービスを呼び出し、以下のファイル名 "your-image.jpg" をご使用のマシンからのイメージに置き換えます。 

```python
with open('your-image.jpg') as f:
    results = service.run(f)
# map results [class_id] => [confidence]
results = enumerate(results)
# sort results by confidence
sorted_results = sorted(results, key=lambda x: x[1], reverse=True)
# print top 5 results
for top in sorted_results[:5]:
    print(classes_entries[top[0]], 'confidence:', top[1])
``` 

### <a name="clean-up-service"></a>サービスをクリーンアップする
サービスを削除します。

```python
service.delete()
    
registered_model.delete()
```

## <a name="secure-fpga-web-services"></a>FPGA の Web サービスをセキュリティで保護する

FPGA の Web サービスのセキュリティ保護については、[Web サービスのセキュリティ保護](how-to-secure-web-service.md)に関するドキュメントを参照してください。


## <a name="next-steps"></a>次の手順

[Web サービスとしてデプロイされる ML モデルを使用する](how-to-consume-web-service.md)方法を学習します。
