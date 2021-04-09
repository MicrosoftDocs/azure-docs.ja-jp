---
title: 機械学習モデルをデプロイする方法
titleSuffix: Azure Machine Learning
description: 機械学習モデルをデプロイする方法と場所について説明します。 Azure Container Instances、Azure Kubernetes Service、Azure IoT Edge、および FPGA にデプロイします。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.reviewer: larryfr
ms.date: 01/13/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-python, deploy, devx-track-azurecli
adobe-target: true
ms.openlocfilehash: ed397e9f8db721a6baa641fc958af0dda570ce57
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103561942"
---
# <a name="deploy-machine-learning-models-to-azure"></a>機械学習モデルを Azure にデプロイする

機械学習またはディープ ラーニング モデルを Web サービスとして Azure クラウドにデプロイする方法について説明します。 また、Azure IoT Edge デバイスにデプロイすることもできます。

モデルをどこにデプロイするかに関係なく、ワークフローは同様です。

1. モデルを登録します (省略可能、下記参照)。
1. 推論構成を準備します ([コードなしのデプロイ](./how-to-deploy-no-code-deployment.md)を使用する場合を除く)。
1. エントリ スクリプトを準備します ([コードなしのデプロイ](./how-to-deploy-no-code-deployment.md)を使用する場合を除く)。
1. コンピューティング ターゲットを選択します。
1. モデルをコンピューティング ターゲットにデプロイします。
1. 結果の Web サービスをテストします。

機械学習デプロイ ワークフローに関連する概念の詳細については、[Azure Machine Learning でのモデルの管理、デプロイ、監視](concept-model-management-and-deployment.md)に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

- Azure Machine Learning ワークスペース。 詳細については、[Azure Machine Learning ワークスペースの作成](how-to-manage-workspace.md)に関するページをご覧ください。
- モデルです。 トレーニング済みのモデルがない場合、[こちらのチュートリアル](https://aka.ms/azml-deploy-cloud)で提供されているモデルと依存関係のファイルを使用できます。
- [Machine Learning service 用 Azure コマンド ライン インターフェイス (CLI) の拡張機能](reference-azure-machine-learning-cli.md)。

# <a name="python"></a>[Python](#tab/python)

- Azure Machine Learning ワークスペース。 詳細については、[Azure Machine Learning ワークスペースの作成](how-to-manage-workspace.md)に関するページをご覧ください。
- モデルです。 トレーニング済みのモデルがない場合、[こちらのチュートリアル](https://aka.ms/azml-deploy-cloud)で提供されているモデルと依存関係のファイルを使用できます。
- [Python 用 Azure Machine Learning ソフトウェア開発キット (SDK)](/python/api/overview/azure/ml/intro)。

---

## <a name="connect-to-your-workspace"></a>ワークスペースに接続する

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

[サブスクリプション コンテキストの設定](/cli/azure/manage-azure-subscriptions-azure-cli#change-the-active-subscription)に関する Azure CLI ドキュメントに記載されている手順に従います。

その後、次の操作を行います。

```azurecli-interactive
az ml workspace list --resource-group=<my resource group>
```

アクセス権のあるワークスペースを表示します。

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core import Workspace
ws = Workspace.from_config(path=".file-path/ws_config.json")
```

SDK を使用してワークスペースに接続する方法の詳細については、[Azure Machine Learning SDK for Python](/python/api/overview/azure/ml/intro#workspace) のドキュメントをご覧ください。


---


## <a name="register-your-model-optional"></a><a id="registermodel"></a> モデルを登録する (省略可能)

登録済みモデルは、モデルを構成する 1 つまたは複数のファイルの論理コンテナーです。 たとえば、複数のファイルに格納されているモデルがある場合は、ワークスペースに単一モデルとしてそれらを登録できます。 ファイルの登録後は、その登録済みモデルをダウンロードするかデプロイし、登録されたすべてのファイルを受信できます。

> [!TIP] 
> バージョンを追跡するためにモデルを登録することをお勧めしますが、必須ではありません。 モデルを登録しないで続行する場合は、[InferenceConfig](/python/api/azureml-core/azureml.core.model.inferenceconfig) または [InferenceConfig](./reference-azure-machine-learning-cli.md#inference-configuration-schema) でソース ディレクトリを指定し、確実にそのソース ディレクトリ内にモデルが存在するようにする必要があります。

> [!TIP]
> モデルを登録するときは、(トレーニングの実行から) クラウドの場所またはローカル ディレクトリのパスを指定します。 このパスは、登録プロセスの一部としてアップロードするファイルを見つけるためだけのものです。 エントリ スクリプトで使用されるパスと一致する必要はありません。 詳細については、「[エントリ スクリプトでモデル ファイルを検索する](./how-to-deploy-advanced-entry-script.md#load-registered-models)」を参照してください。

> [!IMPORTANT]
> Azure Machine Learning スタジオの [モデル] ページの [`Tags` でフィルター] オプションを使用するとき、お客様は `TagName : TagValue` を使用するのではなく、`TagName=TagValue` を (スペースなしで) 使用してください。

次の例では、モデルを登録する方法を示します。

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

### <a name="register-a-model-from-an-azure-ml-training-run"></a>Azure ML トレーニングの実行からモデルを登録する

```azurecli-interactive
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --run-id myrunid --tag area=mnist
```

[!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

`--asset-path` パラメーターは、クラウドでのモデルの場所を示します。 この例では、1 つのファイルへのパスが使用されます。 モデルの登録に複数のファイルを含めるには、`--asset-path` を、それらのファイルが含まれているフォルダーのパスに設定します。

### <a name="register-a-model-from-a-local-file"></a>ローカル ファイルからモデルを登録する

```azurecli-interactive
az ml model register -n onnx_mnist -p mnist/model.onnx
```

モデルの登録に複数のファイルを含めるには、`-p` を、それらのファイルが含まれているフォルダーのパスに設定します。

`az ml model register` に関する詳細については、[リファレンス ドキュメント](/cli/azure/ext/azure-cli-ml/ml/model)を参照してください。

# <a name="python"></a>[Python](#tab/python)

### <a name="register-a-model-from-an-azure-ml-training-run"></a>Azure ML トレーニングの実行からモデルを登録する

  SDK を使用してモデルをトレーニングする場合、モデルのトレーニング方法に応じて、[Run](/python/api/azureml-core/azureml.core.run.run) オブジェクトまたは [AutoMLRun](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun) オブジェクトのいずれかを受け取ることができます。 各オブジェクトは、実験の実行によって作成されたモデルを登録するために使用できます。

  + `azureml.core.Run` オブジェクトからモデルを登録する
 
    ```python
    model = run.register_model(model_name='sklearn_mnist',
                               tags={'area': 'mnist'},
                               model_path='outputs/sklearn_mnist_model.pkl')
    print(model.name, model.id, model.version, sep='\t')
    ```

    `model_path` パラメーターは、クラウドでのモデルの場所を示します。 この例では、1 つのファイルへのパスが使用されます。 モデルの登録に複数のファイルを含めるには、`model_path` を、それらのファイルが含まれているフォルダーのパスに設定します。 詳細については、[Run.register_model](/python/api/azureml-core/azureml.core.run.run#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none----kwargs-) のドキュメントを参照してください。

  + `azureml.train.automl.run.AutoMLRun` オブジェクトからモデルを登録する

    ```python
        description = 'My AutoML Model'
        model = run.register_model(description = description,
                                   tags={'area': 'mnist'})

        print(run.model_id)
    ```

    この例では、`metric` パラメーターと `iteration` パラメーターが指定されていません。これにより、最適なプライマリ メトリックを持つイテレーションが登録されます。 実行から返された `model_id` 値がモデル名の代わりに使用されます。

    詳細については、[AutoMLRun.register_model](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun#register-model-model-name-none--description-none--tags-none--iteration-none--metric-none-) のドキュメントを参照してください。

    登録済みモデルを `AutoMLRun` からデプロイするには、[Azure Machine Learning スタジオのワンクリック デプロイ ボタン](how-to-use-automated-ml-for-ml-models.md#deploy-your-model)でそれを行うことをお勧めします。 
### <a name="register-a-model-from-a-local-file"></a>ローカル ファイルからモデルを登録する

モデルのローカル パスを指定することで、モデルを登録できます。 フォルダーまたは 1 個のファイルのパスのいずれかを指定できます。 この方法を使用すると、Azure Machine Learning でトレーニングされてからダウンロードされたモデルを登録できます。 この方法を使用して、Azure Machine Learning の外部でトレーニングされたモデルを登録することもできます。

[!INCLUDE [trusted models](../../includes/machine-learning-service-trusted-model.md)]

+ **SDK と ONNX の使用**

    ```python
    import os
    import urllib.request
    from azureml.core.model import Model
    # Download model
    onnx_model_url = "https://www.cntk.ai/OnnxModels/mnist/opset_7/mnist.tar.gz"
    urllib.request.urlretrieve(onnx_model_url, filename="mnist.tar.gz")
    os.system('tar xvzf mnist.tar.gz')
    # Register model
    model = Model.register(workspace = ws,
                            model_path ="mnist/model.onnx",
                            model_name = "onnx_mnist",
                            tags = {"onnx": "demo"},
                            description = "MNIST image classification CNN from ONNX Model Zoo",)
    ```

  モデルの登録に複数のファイルを含めるには、`model_path` を、それらのファイルが含まれているフォルダーのパスに設定します。

詳細については、[Model クラス](/python/api/azureml-core/azureml.core.model.model)のドキュメントを参照してください。

Azure Machine Learning 以外でトレーニングされたモデルの使用の詳細については、[既存のモデルをデプロイする方法](how-to-deploy-existing-model.md)に関する記事を参照してください。

---

## <a name="define-an-entry-script"></a>エントリ スクリプトを定義する

[!INCLUDE [write entry script](../../includes/machine-learning-entry-script.md)]


## <a name="define-an-inference-configuration"></a>推論構成を定義する


推論構成では、モデルを含む Web サービスを設定する方法を示します。 これは後でモデルをデプロイするときに使用されます。

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

最小限の推論構成は、次のように記述することができます。

```json
{
    "entryScript": "score.py",
    "sourceDirectory": "./working_dir"
}
```

これにより、受信要求を処理するために、機械学習デプロイで `./working_dir` ディレクトリ内のファイル `score.py` が使用されることが指定されます。

推論構成に関する詳細な説明については、[こちらの記事を参照](./reference-azure-machine-learning-cli.md#inference-configuration-schema)してください。 

# <a name="python"></a>[Python](#tab/python)

その具体的な例を次に示します。

1. ワークスペースからの[キュレーションされた環境](resource-curated-environments.md)の読み込み
1. 環境の複製
1. 依存関係としての `scikit-learn` の指定
1. InferenceConfig の作成を目的とした環境の使用

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig


env = Environment.get(workspace, "AzureML-Minimal").clone(env_name)

for pip_package in ["scikit-learn"]:
    env.python.conda_dependencies.add_pip_package(pip_package)

inference_config = InferenceConfig(entry_script='path-to-score.py',
                                    environment=env)
```

環境の詳細については、[トレーニングとデプロイのための環境の作成と管理](how-to-use-environments.md)に関する記事を参照してください。

推論構成の詳細については、[InferenceConfig](/python/api/azureml-core/azureml.core.model.inferenceconfig) クラスのドキュメントを参照してください。

---

> [!TIP] 
> 推論構成を利用したカスタム Docker イメージの使用については、[カスタム Docker イメージを使用してモデルをデプロイする方法](how-to-deploy-custom-docker-image.md)に関するページを参照してください。

## <a name="choose-a-compute-target"></a>コンピューティング ターゲットを選択する

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

## <a name="define-a-deployment-configuration"></a>デプロイ構成を定義する

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

デプロイ構成で使用できるオプションは、選択するコンピューティング ターゲットによって異なります。

[!INCLUDE [aml-local-deploy-config](../../includes/machine-learning-service-local-deploy-config.md)]

詳細については、[こちらのリファレンス](./reference-azure-machine-learning-cli.md#deployment-configuration-schema)を参照してください。

# <a name="python"></a>[Python](#tab/python)

モデルをデプロイする前にデプロイ構成を定義する必要があります。 *デプロイ構成は、Web サービスがホストされるコンピューティング ターゲットに固有となります*。 たとえば、モデルをローカルでデプロイするときに、サービスが要求を受け入れるポートを指定する必要があります。 デプロイ構成は、エントリ スクリプトの一部ではありません。 これは、モデルとエントリ スクリプトをホストするコンピューティング先の特性を定義するために使用されます。

また、たとえば、ワークスペースに関連付けられている Azure Kubernetes Service (AKS) インスタンスがまだない場合などにも、コンピューティング リソースを作成する必要があります。

次の表には、コンピューティング ターゲット別にデプロイ構成を作成する例があります。

| コンピューティング ターゲット | デプロイ構成の例 |
| ----- | ----- |
| ローカル | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Azure Container Instances | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Azure Kubernetes Service | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

これらのローカル、Azure Container Instances、および AKS Web サービスのクラスは、`azureml.core.webservice` からインポートできます。

```python
from azureml.core.webservice import AciWebservice, AksWebservice, LocalWebservice
```

---

## <a name="deploy-your-machine-learning-model"></a>機械学習モデルをデプロイする

これでモデルをデプロイする準備が整いました。 

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

### <a name="using-a-registered-model"></a>登録済みのモデルの使用

Azure Machine Learning ワークスペースにモデルを登録した場合は、"mymodel:1" を、モデルの名前とそのバージョン番号に置き換えます。

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
```

### <a name="using-a-local-model"></a>ローカル モデルの使用

モデルを登録しない場合は、inferenceconfig.json に "sourceDirectory" パラメーターを渡して、モデルの提供元となるローカル ディレクトリを指定します。

```azurecli-interactive
az ml model deploy --ic inferenceconfig.json --dc deploymentconfig.json
```

# <a name="python"></a>[Python](#tab/python)

次の例では、ローカル デプロイを示しています。 構文は、前の手順で選択したコンピューティング ターゲットによって異なります。

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

詳細については、[LocalWebservice](/python/api/azureml-core/azureml.core.webservice.local.localwebservice)、[Model.deploy()](/python/api/azureml-core/azureml.core.model.model#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)、および [Webservice](/python/api/azureml-core/azureml.core.webservice.webservice) のドキュメントを参照してください。

---

### <a name="understanding-service-state"></a>サービスの状態について

モデルのデプロイ中に、完全にデプロイされるまでの間に、サービスの状態が変化することがあります。

次の表で、サービスの各状態について説明します。

| Web サービスの状態 | 説明 | 最終的な状態
| ----- | ----- | ----- |
| 移行中 | サービスはデプロイ処理中です。 | いいえ |
| 異常 | サービスはデプロイされましたが、現在アクセスできません。  | いいえ |
| スケジュール不可 | リソースが不足しているため、現時点ではサービスをデプロイできません。 | いいえ |
| 失敗 | エラーまたはクラッシュが発生したため、サービスをデプロイできませんでした。 | はい |
| Healthy | サービスは正常であり、エンドポイントを使用できます。 | はい |

> [!TIP]
> デプロイ時に、コンピューティング ターゲットの Docker イメージが構築され、Azure Container Registry (ACR) から読み込まれます。 既定では、Azure Machine Learning により、*basic* サービス レベルを使用する ACR が作成されます。 ワークスペースの ACR を standard レベルまたは premium レベルに変更すると、イメージを構築してコンピューティング ターゲットにデプロイするための時間を短縮することができます。 詳細については、「[Azure Container Registry サービス階層](../container-registry/container-registry-skus.md)」を参照してください。

> [!NOTE]
> Azure Kubernetes Service (AKS) にモデルをデプロイする場合は、そのクラスターで [Azure Monitor](../azure-monitor/containers/container-insights-enable-existing-clusters.md) を有効にすることをお勧めします。 これは、全体的なクラスターの正常性とリソースの使用状況を把握するのに役立ちます。 また、次のリソースも役立つ場合があります。
>
> * [AKS クラスターに影響する Resource Health イベントを確認する](../aks/aks-resource-health.md)
> * [Azure Kubernetes Service 診断](../aks/concepts-diagnostics.md)
>
> 異常または過負荷のクラスターにモデルをデプロイしようとすると、問題が発生することが予想されます。 AKS クラスターでの問題のトラブルシューティングに関するヘルプが必要な場合は、AKS サポートにお問い合わせください。

### <a name="batch-inference"></a><a id="azuremlcompute"></a> バッチ推論
Azure Machine Learning のコンピューティング先は、Azure Machine Learning によって作成され、管理されます。 これは Azure Machine Learning パイプラインからのバッチ予測に使用できます。

Azure Machine Learning コンピューティングを使用したバッチ推論のチュートリアルについては、[バッチ予測を実行する方法](tutorial-pipeline-batch-scoring-classification.md)に関するページを参照してください。

### <a name="iot-edge-inference"></a><a id="iotedge"></a> IoT Edge の推論
エッジにデプロイするためのサポートは現在、プレビューの段階にあります。 詳細については、[IoT Edge モジュールとしての Azure Machine Learning のデプロイ](../iot-edge/tutorial-deploy-machine-learning.md)に関する記事を参照してください。

## <a name="delete-resources"></a>リソースを削除する

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

デプロイされた Web サービスを削除するには、`az ml service <name of webservice>` を使用します。

ワークスペースから登録済みのモデルを削除するには、`az ml model delete <model id>` を使用します。

[Web サービスの削除](/cli/azure/ext/azure-cli-ml/ml/service#ext-azure-cli-ml-az-ml-service-delete)と[モデルの削除](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-delete)に関する詳細を確認してください。

# <a name="python"></a>[Python](#tab/python)

デプロイされた Web サービスを削除するには、`service.delete()` を使用します。
登録済みのモデルを削除するには、`model.delete()` を使用します。

詳細については、[WebService.delete()](/python/api/azureml-core/azureml.core.webservice%28class%29#delete--) と [Model.delete()](/python/api/azureml-core/azureml.core.model.model#delete--) のドキュメントを参照してください。

---

## <a name="next-steps"></a>次のステップ

* [失敗したデプロイのトラブルシューティング](how-to-troubleshoot-deployment.md)
* [Azure Kubernetes Service にデプロイする](how-to-deploy-azure-kubernetes-service.md)
* [Web サービスを使用するクライアント アプリケーションを作成する](how-to-consume-web-service.md)
* [Web サービスを更新する](how-to-deploy-update-web-service.md)
* [カスタム Docker イメージを使用してモデルをデプロイする方法](how-to-deploy-custom-docker-image.md)
* [Azure Machine Learning スタジオにおける自動化された ML 実行のためのワンクリック デプロイ](how-to-use-automated-ml-for-ml-models.md#deploy-your-model)
* [TLS を使用して Azure Machine Learning による Web サービスをセキュリティで保護する](how-to-secure-web-service.md)
* [Application Insights を使用して Azure Machine Learning のモデルを監視する](how-to-enable-app-insights.md)
* [実稼働環境でモデルのデータを収集する](how-to-enable-data-collection.md)
* [モデル デプロイのイベント アラートおよびトリガーを作成する](how-to-use-event-grid.md)