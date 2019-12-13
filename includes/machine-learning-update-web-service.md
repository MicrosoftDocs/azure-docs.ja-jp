---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 11/06/2019
ms.author: larryfr
ms.openlocfilehash: 04e8f1f212eb4fa7cf4d8e19132107f2ddb53729
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926979"
---
Web サービスを更新するには、`update` メソッドを使用します。 推論の構成で指定できる新しいモデル、新しいエントリ スクリプト、または新しい依存関係を使用するように Web サービスを更新することができます。 詳細については、[Webservice.update](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice.webservice?view=azure-ml-py#update--args-) のドキュメントを参照してください。

> [!IMPORTANT]
> 新しいバージョンのモデルを作成するときは、それを使用したい各サービスを手動で更新する必要があります。

**SDK を使用する**

次のコードは、SDK を使用して Web サービスのモデル、環境、エントリ スクリプトを更新する方法を示しています。

```python
from azureml.core import Environment
from azureml.core.webservice import Webservice
from azureml.core.model import Model, InferenceConfig

# Register new model.
new_model = Model.register(model_path="outputs/sklearn_mnist_model.pkl",
                           model_name="sklearn_mnist",
                           tags={"key": "0.1"},
                           description="test",
                           workspace=ws)

# Use version 3 of the environment.
deploy_env = Environment.get(workspace=ws,name="myenv",version="3")
inference_config = InferenceConfig(entry_script="score.py",
                                   environment=deploy_env)

service_name = 'myservice'
# Retrieve existing service.
service = Webservice(name=service_name, workspace=ws)



# Update to new model(s).
service.update(models=[new_model], inference_config=inference_config)
print(service.state)
print(service.get_logs())
```

**CLI を使用する**

ML CLI を使用して Web サービスを更新することもできます。 次の例では、新しいモデルを登録し、この新しいモデルを使用するように Web サービスを更新する方法を示します。

```azurecli
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --output-metadata-file modelinfo.json
az ml service update -n myservice --model-metadata-file modelinfo.json
```

> [!TIP]
> この例では、JSON ドキュメントを使用して、登録コマンドから更新コマンドにモデル情報を渡します。
>
> 新しいエントリ スクリプトまたは環境を使用するようにサービスを更新するには、[推論構成ファイル](/azure/machine-learning/service/reference-azure-machine-learning-cli#inference-configuration-schema)を作成し、それを `ic` パラメーターで指定します。

詳細については、[az ml service update](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/service?view=azure-cli-latest#ext-azure-cli-ml-az-ml-service-update) のドキュメントを参照してください。