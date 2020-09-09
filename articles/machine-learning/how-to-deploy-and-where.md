---
title: モデルをデプロイする方法と場所
titleSuffix: Azure Machine Learning
description: Azure Container Instances、Azure Kubernetes Service、Azure IoT Edge、フィールド プログラマブル ゲート アレイなど、Azure Machine Learning モデルをデプロイする方法と場所について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.reviewer: larryfr
ms.date: 07/08/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: d7502414f6476cafcc85bbefd28a4ec463f62099
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2020
ms.locfileid: "88751683"
---
# <a name="deploy-models-with-azure-machine-learning"></a>Azure Machine Learning を使用してモデルをデプロイする
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Web サービスとして Azure クラウドに、または Azure IoT Edge デバイスに機械学習モデルをデプロイする方法を説明します。

モデルをどこにデプロイするかに関係なく、ワークフローは同様です。

1. モデルを登録します。
1. 推論構成ファイルを準備します
1. エントリ スクリプトを準備します ([コードなしのデプロイ](how-to-deploy-no-code-deployment.md)を使用する場合を除く)
1. コンピューティング ターゲットにモデルをデプロイします。
1. デプロイしたモデル (Web サービスとも呼ばれる) をテストします。

デプロイ ワークフローに関連する概念の詳細については、[Azure Machine Learning でのモデルの管理、デプロイ、監視](concept-model-management-and-deployment.md)に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

- Azure Machine Learning ワークスペース。 詳細については、[Azure Machine Learning ワークスペースの作成](how-to-manage-workspace.md)に関するページをご覧ください。
- モデルです。 トレーニング済みのモデルがない場合、[こちらのチュートリアル](https://aka.ms/azml-deploy-cloud)で提供されているモデルと依存関係のファイルを使用できます。
- [Machine Learning service 用 Azure コマンド ライン インターフェイス (CLI) の拡張機能](reference-azure-machine-learning-cli.md)。

# <a name="python"></a>[Python](#tab/python)

- Azure Machine Learning ワークスペース。 詳細については、[Azure Machine Learning ワークスペースの作成](how-to-manage-workspace.md)に関するページをご覧ください。
- モデルです。 トレーニング済みのモデルがない場合、[こちらのチュートリアル](https://aka.ms/azml-deploy-cloud)で提供されているモデルと依存関係のファイルを使用できます。
- [Python 用 Azure Machine Learning ソフトウェア開発キット (SDK)](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)。

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

SDK を使用してワークスペースに接続する方法の詳細については、[Azure Machine Learning SDK for Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace) のドキュメントをご覧ください。


---


## <a name="register-your-model"></a><a id="registermodel"></a> モデルを登録する

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

登録済みモデルは、モデルを構成する 1 つまたは複数のファイルの論理コンテナーです。 たとえば、複数のファイルに格納されているモデルがある場合は、ワークスペースに単一モデルとしてそれらを登録できます。 ファイルの登録後は、その登録済みモデルをダウンロードするかデプロイし、登録されたすべてのファイルを受信できます。

> [!TIP]
> モデルを登録するときは、(トレーニングの実行から) クラウドの場所またはローカル ディレクトリのパスを指定します。 このパスは、登録プロセスの一部としてアップロードするファイルを見つけるためだけのものです。 エントリ スクリプトで使用されるパスと一致する必要はありません。 詳細については、「[エントリ スクリプトでモデル ファイルを検索する](how-to-deploy-advanced-entry-script.md#load-registered-models)」を参照してください。

機械学習モデルをご使用の Azure Machine Learning ワークスペースに登録します。 これらのモデルは、Azure Machine Learning から取得することも、どこか他の場所から取得することもできます。 モデルを登録するときに、必要に応じてモデルに関するメタデータを提供できます。 その後、モデル登録に適用する `tags` および `properties` 辞書を使用して、モデルをフィルター処理できます。

次の例では、モデルを登録する方法を示します。

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


登録済みモデルは、モデルを構成する 1 つまたは複数のファイルの論理コンテナーです。 たとえば、複数のファイルに格納されているモデルがある場合は、ワークスペースに単一モデルとしてそれらを登録できます。 ファイルの登録後は、その登録済みモデルをダウンロードするかデプロイし、登録されたすべてのファイルを受信できます。

> [!TIP]
> モデルを登録するときは、(トレーニングの実行から) クラウドの場所またはローカル ディレクトリのパスを指定します。 このパスは、登録プロセスの一部としてアップロードするファイルを見つけるためだけのものです。 エントリ スクリプトで使用されるパスと一致する必要はありません。 詳細については、「[エントリ スクリプトでモデル ファイルを検索する](how-to-deploy-advanced-entry-script.md#load-registered-models)」を参照してください。

機械学習モデルをご使用の Azure Machine Learning ワークスペースに登録します。 これらのモデルは、Azure Machine Learning から取得することも、どこか他の場所から取得することもできます。 モデルを登録するときに、必要に応じてモデルに関するメタデータを提供できます。 その後、モデル登録に適用する `tags` および `properties` 辞書を使用して、モデルをフィルター処理できます。

次の例では、モデルを登録する方法を示します。

### <a name="register-a-model-from-an-azure-ml-training-run"></a>Azure ML トレーニングの実行からモデルを登録する

  SDK を使用してモデルをトレーニングする場合、モデルのトレーニング方法に応じて、[Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py) オブジェクトまたは [AutoMLRun](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun) オブジェクトのいずれかを受け取ることができます。 各オブジェクトは、実験の実行によって作成されたモデルを登録するために使用できます。

  + `azureml.core.Run` オブジェクトからモデルを登録する
 
    ```python
    model = run.register_model(model_name='sklearn_mnist',
                               tags={'area': 'mnist'},
                               model_path='outputs/sklearn_mnist_model.pkl')
    print(model.name, model.id, model.version, sep='\t')
    ```

    `model_path` パラメーターは、クラウドでのモデルの場所を示します。 この例では、1 つのファイルへのパスが使用されます。 モデルの登録に複数のファイルを含めるには、`model_path` を、それらのファイルが含まれているフォルダーのパスに設定します。 詳細については、[Run.register_model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none----kwargs-) のドキュメントを参照してください。

  + `azureml.train.automl.run.AutoMLRun` オブジェクトからモデルを登録する

    ```python
        description = 'My AutoML Model'
        model = run.register_model(description = description,
                                   tags={'area': 'mnist'})

        print(run.model_id)
    ```

    この例では、`metric` パラメーターと `iteration` パラメーターが指定されていません。これにより、最適なプライマリ メトリックを持つイテレーションが登録されます。 実行から返された `model_id` 値がモデル名の代わりに使用されます。

    詳細については、[AutoMLRun.register_model](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun#register-model-model-name-none--description-none--tags-none--iteration-none--metric-none-) のドキュメントを参照してください。


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

詳細については、[Model クラス](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py)のドキュメントを参照してください。

Azure Machine Learning 以外でトレーニングされたモデルの使用の詳細については、[既存のモデルをデプロイする方法](how-to-deploy-existing-model.md)に関する記事を参照してください。

---

## <a name="define-an-entry-script"></a>エントリ スクリプトを定義する

[!INCLUDE [write entry script](../../includes/machine-learning-entry-script.md)]


## <a name="define-an-inference-configuration"></a>推論構成を定義する

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

[!INCLUDE [inference config](../../includes/machine-learning-service-inference-config.md)]

次のコマンドでは、CLI を使用してモデルをデプロイする方法を示します。

```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json
```

この例では、構成で次の設定が指定されています。

* このモデルには Python が必要であるということ
* [エントリ スクリプト](#define-an-entry-script)。デプロイされたサービスに送信される Web 要求の処理に必要です。
* 推論を行うために必要な Python パッケージを記述する Conda ファイル

推論構成を利用したカスタム Docker イメージの使用については、[カスタム Docker イメージを使用してモデルをデプロイする方法](how-to-deploy-custom-docker-image.md)に関するページを参照してください。

# <a name="python"></a>[Python](#tab/python)

推論構成では、モデルを含む Web サービスを設定する方法を示します。 これは後でモデルをデプロイするときに使用されます。

推論構成は Azure Machine Learning 環境を使用して、デプロイに必要なソフトウェアの依存関係を定義します。 環境では、トレーニングとデプロイに必要なソフトウェアの依存関係を作成、管理、再利用することができます。 カスタム依存関係ファイルから環境を作成するか、またはキュレートされた Azure Machine Learning 環境のいずれかを使用できます。 次の YAML は、推論のための Conda 依存関係ファイルの例です。 バージョン 1.0.45 以上では、pip の依存関係として、azureml-defaults を指定する必要があることに注意してください。これは、モデルを Web サービスとしてホストするために必要な機能が含まれているためです。 また、自動スキーマ生成を使用する場合、エントリ スクリプトでは `inference-schema` パッケージをインポートする必要もあります。

```YAML

name: project_environment
dependencies:
- python=3.6.2
- scikit-learn=0.22.1
- pip:
 # You must list azureml-defaults as a pip dependency
 - azureml-defaults>=1.0.45
 - inference-schema[numpy-support]
```

> [!IMPORTANT]
> 依存関係が Conda と pip (PyPi から) の両方で利用できる場合、Conda パッケージには通常、インストールの信頼性を高めるビルド済みのバイナリが付属しているため、Conda バージョンを使用することをお勧めします。
>
> 詳細については、「[Conda と Pip について](https://www.anaconda.com/understanding-conda-and-pip/)」を参照してください。
>
> 依存関係が Conda を介して使用可能かどうかを確認するには、`conda search <package-name>` コマンドを使用するか、[https://anaconda.org/anaconda/repo](https://anaconda.org/anaconda/repo) および [https://anaconda.org/conda-forge/repo](https://anaconda.org/conda-forge/repo) でパッケージ インデックスを使用します。

依存関係ファイルを使用して環境オブジェクトを作成して、後で使用するためにワークスペースに保存することができます。

```python
from azureml.core.environment import Environment
myenv = Environment.from_conda_specification(name = 'myenv',
                                                file_path = 'path-to-conda-specification-file'
myenv.register(workspace=ws)
```

Azure Machine Learning を使用した Python 環境の使用とカスタマイズの詳細については、「[Azure Machine Learning でソフトウェア環境を作成して使用する](how-to-use-environments.md)」を参照してください。

推論構成を利用したカスタム Docker イメージの使用については、[カスタム Docker イメージを使用してモデルをデプロイする方法](how-to-deploy-custom-docker-image.md)に関するページを参照してください。


次の例で、ワークスペースから環境を読み込み、推論構成でそれを使用する方法を示します。

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig


myenv = Environment.get(workspace=ws, name='myenv', version='1')
inference_config = InferenceConfig(entry_script='path-to-score.py',
                                    environment=myenv)
```

環境の詳細については、[トレーニングとデプロイのための環境の作成と管理](how-to-use-environments.md)に関する記事を参照してください。

推論構成の詳細については、[InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) クラスのドキュメントを参照してください。

---

## <a name="choose-a-compute-target"></a>コンピューティング ターゲットを選択する

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

## <a name="define-a-deployment-configuration"></a>デプロイ構成を定義する

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

デプロイ構成で使用できるオプションは、選択するコンピューティング ターゲットによって異なります。

[!INCLUDE [aml-local-deploy-config](../../includes/machine-learning-service-local-deploy-config.md)]

詳細については、[az ml model deploy](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-deploy) のドキュメントを参照してください。

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



## <a name="deploy-your-model"></a>モデルをデプロイする

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

詳細については、[LocalWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py)、[Model.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)、および [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py) のドキュメントを参照してください。

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


### <a name="batch-inference"></a><a id="azuremlcompute"></a> バッチ推論
Azure Machine Learning のコンピューティング先は、Azure Machine Learning によって作成され、管理されます。 これは Azure Machine Learning パイプラインからのバッチ予測に使用できます。

Azure Machine Learning コンピューティングを使用したバッチ推論のチュートリアルについては、[バッチ予測を実行する方法](tutorial-pipeline-batch-scoring-classification.md)に関するページを参照してください。

### <a name="iot-edge-inference"></a><a id="iotedge"></a> IoT Edge の推論
エッジにデプロイするためのサポートは現在、プレビューの段階にあります。 詳細については、[IoT Edge モジュールとしての Azure Machine Learning のデプロイ](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning)に関する記事を参照してください。

## <a name="delete-resources"></a>リソースを削除する

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

デプロイされた Web サービスを削除するには、`az ml service <name of webservice>` を使用します。

ワークスペースから登録済みのモデルを削除するには、`az ml model delete <model id>` を使用します。

[Web サービスの削除](/cli/azure/ext/azure-cli-ml/ml/service#ext-azure-cli-ml-az-ml-service-delete)と[モデルの削除](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-delete)に関する詳細を確認してください。

# <a name="python"></a>[Python](#tab/python)

デプロイされた Web サービスを削除するには、`service.delete()` を使用します。
登録済みのモデルを削除するには、`model.delete()` を使用します。

詳細については、[WebService.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--) と [Model.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--) のドキュメントを参照してください。

---


## <a name="next-steps"></a>次のステップ

* [失敗したデプロイのトラブルシューティング](how-to-troubleshoot-deployment.md)
* [Azure Kubernetes Service にデプロイする](how-to-deploy-azure-kubernetes-service.md)
* [Web サービスを使用するクライアント アプリケーションの作成](how-to-consume-web-service.md)
* [Web サービスを更新する](how-to-deploy-update-web-service.md)
* [カスタム Docker イメージを使用してモデルをデプロイする方法](how-to-deploy-custom-docker-image.md)
* [TLS を使用して Azure Machine Learning による Web サービスをセキュリティで保護する](how-to-secure-web-service.md)
* [Application Insights を使用して Azure Machine Learning のモデルを監視する](how-to-enable-app-insights.md)
* [実稼働環境でモデルのデータを収集する](how-to-enable-data-collection.md)
* [モデル デプロイのイベント アラートおよびトリガーを作成する](how-to-use-event-grid.md)

