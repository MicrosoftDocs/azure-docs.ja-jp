---
title: コードなしのデプロイ (プレビュー)
titleSuffix: Azure Machine Learning
description: コードなしのデプロイでは、エントリ スクリプトを手動で作成することなく、モデルを web サービスとしてデプロイすることができます。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 07/31/2020
ms.topic: conceptual
ms.custom: deploy
ms.reviewer: larryfr
ms.openlocfilehash: a17126695aa5138d1df7fd17cfaa2f5f75ad1004
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "93324914"
---
# <a name="preview-no-code-model-deployment"></a>(プレビュー) コードなしのモデル デプロイ

コードなしのモデル デプロイは現在プレビュー段階で、次の機械学習フレームワークをサポートしています。

## <a name="tensorflow-savedmodel-format"></a>TensorFlow SavedModel 形式
コードなしのモデル デプロイを使用するには、TensorFlow モデルが **SavedModel 形式** で登録されている必要があります。

SavedModel の作成方法については、[このリンク](https://www.tensorflow.org/guide/saved_model)を参照してください。

[TensorFlow Serving DockerHub](https://registry.hub.docker.com/r/tensorflow/serving/tags) で "Tags" の下に一覧表示されている TensorFlow バージョンはすべてサポートされています。

```python
from azureml.core import Model

model = Model.register(workspace=ws,
                       model_name='flowers',                        # Name of the registered model in your workspace.
                       model_path='./flowers_model',                # Local Tensorflow SavedModel folder to upload and register as a model.
                       model_framework=Model.Framework.TENSORFLOW,  # Framework used to create the model.
                       model_framework_version='1.14.0',            # Version of Tensorflow used to create the model.
                       description='Flowers model')

service_name = 'tensorflow-flower-service'
service = Model.deploy(ws, service_name, [model])
```

## <a name="onnx-models"></a>ONNX モデル

ONNX モデルの登録とデプロイは、任意の ONNX 推論グラフでサポートされています。 前処理および後処理のステップは、現在サポートされていません。

MNIST ONNX モデルを登録してデプロイする方法の例を次に示します。

```python
from azureml.core import Model

model = Model.register(workspace=ws,
                       model_name='mnist-sample',                  # Name of the registered model in your workspace.
                       model_path='mnist-model.onnx',              # Local ONNX model to upload and register as a model.
                       model_framework=Model.Framework.ONNX ,      # Framework used to create the model.
                       model_framework_version='1.3',              # Version of ONNX used to create the model.
                       description='Onnx MNIST model')

service_name = 'onnx-mnist-service'
service = Model.deploy(ws, service_name, [model])
```

モデルにスコアを付けるには、「[Web サービスとしてデプロイされた Azure Machine Learning モデルを使用する](./how-to-consume-web-service.md)」を参照してください。 多くの ONNX プロジェクトでは、protobuf ファイルを使用してトレーニング データと検証データがコンパクトに格納されます。これにより、サービスで想定されているデータ形式がわかりにくい場合があります。 モデル開発者は、開発者向けに次のドキュメントを作成する必要があります。

* 入力形式 (JSON またはバイナリ)
* 入力データの形式と型 (たとえば、形式 [100,100,3] の浮動小数点数の配列)
* ドメイン情報 (イメージ、色空間、コンポーネントの順序、値が正規化されているかどうかなど)

Pytorch を使用している場合は、[モデルを PyTorch から ONNX にエクスポートする](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb)と、変換および制限に関する詳細が得られます。 

## <a name="scikit-learn-models"></a>Scikit-learn モデル

コードなしのモデル デプロイは、全種類の組み込み Scikit-learn モデルでサポートされています。

追加コードなしの sklearn モデルの登録とデプロイの例を次に示します。

```python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = Model.register(workspace=ws,
                       model_name='my-sklearn-model',                # Name of the registered model in your workspace.
                       model_path='./sklearn_regression_model.pkl',  # Local file to upload and register as a model.
                       model_framework=Model.Framework.SCIKITLEARN,  # Framework used to create the model.
                       model_framework_version='0.19.1',             # Version of scikit-learn used to create the model.
                       resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5),
                       description='Ridge regression model to predict diabetes progression.',
                       tags={'area': 'diabetes', 'type': 'regression'})
                       
service_name = 'my-sklearn-service'
service = Model.deploy(ws, service_name, [model])
```

> [!NOTE]
> predict_proba をサポートするモデルでは、既定でそのメソッドが使用されます。 これをオーバーライドして predict を使用するには、次のように POST の本文を変更します。

```python
import json


input_payload = json.dumps({
    'data': [
        [ 0.03807591,  0.05068012,  0.06169621, 0.02187235, -0.0442235,
         -0.03482076, -0.04340085, -0.00259226, 0.01990842, -0.01764613]
    ],
    'method': 'predict'  # If you have a classification model, the default behavior is to run 'predict_proba'.
})

output = service.run(input_payload)

print(output)
```

> [!NOTE]
> 事前に構築された scikit-learn 推論コンテナーには、次の依存関係が含まれています。

```yaml
    - dill
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
    - joblib
    - pandas
    - scipy
    - sklearn_pandas
```
## <a name="next-steps"></a>次のステップ

* [失敗したデプロイのトラブルシューティング](how-to-troubleshoot-deployment.md)
* [Azure Kubernetes Service にデプロイする](how-to-deploy-azure-kubernetes-service.md)
* [Web サービスを使用するクライアント アプリケーションを作成する](how-to-consume-web-service.md)
* [Web サービスを更新する](how-to-deploy-update-web-service.md)
* [カスタム Docker イメージを使用してモデルをデプロイする方法](how-to-deploy-custom-docker-image.md)
* [TLS を使用して Azure Machine Learning による Web サービスをセキュリティで保護する](how-to-secure-web-service.md)
* [Application Insights を使用して Azure Machine Learning のモデルを監視する](how-to-enable-app-insights.md)
* [実稼働環境でモデルのデータを収集する](how-to-enable-data-collection.md)
* [モデル デプロイのイベント アラートおよびトリガーを作成する](how-to-use-event-grid.md)