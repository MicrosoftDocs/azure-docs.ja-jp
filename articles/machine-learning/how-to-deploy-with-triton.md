---
title: Triton を使用した高パフォーマンス モデルのサービス (プレビュー)
titleSuffix: Azure Machine Learning
description: Azure Machine Learning で NVIDIA Triton 推論サーバーを使用してモデルを展開する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 02/16/2020
ms.topic: conceptual
ms.reviewer: larryfr
ms.custom: deploy
ms.openlocfilehash: 2966b685e1904102467bf16994ea781556544047
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102519199"
---
# <a name="high-performance-serving-with-triton-inference-server-preview"></a>Triton 推論サーバーを使用した高パフォーマンスのサービス (プレビュー) 

[NVIDIA Triton 推論サーバー](https://aka.ms/nvidia-triton-docs)を使用して、モデルの推論に使用する Web サービスのパフォーマンスを向上させる方法について説明します。

推論用のモデルを展開する方法の 1 つは、Web サービスとして展開することです。 たとえば、Azure Kubernetes Service または Azure Container Instances への展開です。 既定では、Azure Machine Learning はシングルスレッドの "*汎用*" Web フレームワークを使用して Web サービスを展開します。

Triton は、"*推論用に最適化された*" フレームワークです。 GPU の使用率が高く、コスト効率に優れた推論を実現します。 サーバー側では、受信した要求をバッチ処理し、推論のためにこれらのバッチを送信します。 バッチ処理は GPU リソースのより適切な利用を可能にする、Triton のパフォーマンスにおける重要な部分です。

> [!IMPORTANT]
> Azure Machine Learning からの展開に対する Triton の使用は、現在 __プレビュー__ 段階です。 プレビューの機能は、カスタマー サポートの対象になっていない場合があります。 詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

> [!TIP]
> このドキュメントに記載されているコード スニペットは説明を目的としたものであり、完全なソリューションとなっていない場合があります。 動作するコード例については、[Azure Machine Learning での Triton のエンドツーエンドのサンプル](https://aka.ms/triton-aml-sample)を参照してください。

## <a name="prerequisites"></a>前提条件

* **Azure サブスクリプション**。 お持ちでない場合は、[無料版または有料版の Azure Machine Learning](https://aka.ms/AMLFree) をお試しください。
* Azure Machine Learning で[モデルを展開する方法と場所](how-to-deploy-and-where.md)について理解していること。
* [Azure Machine Learning SDK for Python](/python/api/overview/azure/ml/) **または** [Azure CLI](/cli/azure/) と [機械学習拡張機能](reference-azure-machine-learning-cli.md)。
* ローカル テスト用の Docker の動作するインストール。 Docker のインストールと検証の詳細については、[オリエンテーションとセットアップ](https://docs.docker.com/get-started/)に関する Docker ドキュメントを参照してください。

## <a name="architectural-overview"></a>アーキテクチャの概要

独自のモデルに Triton を使用する前に、Triton がどのように Azure Machine Learning と連携して動作し、既定の展開と比較してどう違うかを理解することが重要です。

**Triton を使用しない既定の展開**

* 受信した要求を同時に処理するために、複数の [Gunicorn](https://gunicorn.org/) ワーカーが開始されています。
* これらのワーカーでは、前処理、モデルの呼び出し、および後処理が行われます。 
* クライアントでは、__Azure ML スコアリング URI__ が使用されます。 たとえば、「 `https://myservice.azureml.net/score` 」のように入力します。

:::image type="content" source="./media/how-to-deploy-with-triton/normal-deploy.png" alt-text="標準の、Triton を使用しない展開アーキテクチャの図":::

**Triton を使用した直接デプロイ**

* 要求は、Triton サーバーに直接送られます。
* Triton によって、GPU 使用率を最大化するために要求がバッチ処理されます。
* クライアントは、__Triton URI__ を使用して要求を行います。 たとえば、「 `https://myservice.azureml.net/v2/models/${MODEL_NAME}/versions/${MODEL_VERSION}/infer` 」のように入力します。

:::image type="content" source="./media/how-to-deploy-with-triton/triton-deploy.png" alt-text="Python ミドルウェアを使用せずに、Triton のみを使用した Inferenceconfig のデプロイ":::

**Triton を使用した推論構成の展開**

* 受信した要求を同時に処理するために、複数の [Gunicorn](https://gunicorn.org/) ワーカーが開始されています。
* 要求は **Triton サーバー** に転送されます。 
* Triton によって、GPU 使用率を最大化するために要求がバッチ処理されます。
* クライアントは、__Azure ML スコアリング URI__ を使用して要求を行います。 たとえば、「 `https://myservice.azureml.net/score` 」のように入力します。

:::image type="content" source="./media/how-to-deploy-with-triton/inference-config-deploy.png" alt-text="Triton と Python ミドルウェアを使用したデプロイ":::

モデルの展開に Triton を使用するワークフローは次のとおりです。

1. Triton を使用してモデルを直接提供します。
1. Triton によって展開されたモデルに、要求を送信できることを確認します。
1. (オプション) サーバー側の前および後処理用に Python ミドルウェアのレイヤーを作成します。

## <a name="deploying-triton-without-python-pre--and-post-processing"></a>Python の前および後処理を使用せずに Triton をデプロイする

まず、下の手順に従って、Triton 推論サーバーがモデルに使用できることを確認します。

### <a name="optional-define-a-model-config-file"></a>(省略可能) モデル構成ファイルの定義

モデル構成ファイルでは、想定される入力の数と、それらの入力の次元を Triton に対して指定します。 構成ファイルの作成の詳細については、[モデルの構成](https://aka.ms/nvidia-triton-docs)に関する NVIDIA ドキュメントを参照してください。

> [!TIP]
> Triton 推論サーバーの起動時に `--strict-model-config=false` オプションを使用します。これは、ONNX または TensorFlow モデルに `config.pbtxt` ファイルを指定する必要がないことを意味します。
> 
> このオプションの詳細については、[生成されたモデルの構成](https://aka.ms/nvidia-triton-docs)に関する NVIDIA ドキュメントを参照してください。

### <a name="use-the-correct-directory-structure"></a>正しいディレクトリ構造を使用する

Azure Machine Learning にモデルを登録する場合、個々のファイルまたはディレクトリ構造を登録できます。 Triton を使用するには、`triton` という名前のディレクトリを含むディレクトリ構造に対してモデルの登録を行う必要があります。 このディレクトリの一般的な構造は次のとおりです。

```bash
models
    - triton
        - model_1
            - model_version
                - model_file
                - config_file
        - model_2
            ...
```

> [!IMPORTANT]
> このディレクトリ構造は Triton モデル リポジトリであり、モデルを Triton で動作させるために必要です。 詳細については、[Triton モデル リポジトリ](https://aka.ms/nvidia-triton-docs)に関する NVIDIA ドキュメントを参照してください。

### <a name="register-your-triton-model"></a>Triton モデルを登録する

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli-interactive
az ml model register -n my_triton_model -p models --model-framework=Multi
```

`az ml model register` に関する詳細については、[リファレンス ドキュメント](/cli/azure/ext/azure-cli-ml/ml/model)を参照してください。

# <a name="python"></a>[Python](#tab/python)


```python

from azureml.core.model import Model

model_path = "models"

model = Model.register(
    model_path=model_path,
    model_name="bidaf-9-tutorial",
    tags={"area": "Natural language processing", "type": "Question-answering"},
    description="Question answering from ONNX model zoo",
    workspace=ws,
    model_framework=Model.Framework.MULTI,  # This line tells us you are registering a Triton model
)

```
詳細については、[Model クラス](/python/api/azureml-core/azureml.core.model.model)のドキュメントを参照してください。

---

### <a name="deploy-your-model"></a>モデルをデプロイする

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

Azure Machine Learning によって作成された "aks-gpu" という GPU 対応の Azure Kubernetes Service クラスターがある場合は、次のコマンドを使用してモデルをデプロイできます。

```azurecli
az ml model deploy -n triton-webservice -m triton_model:1 --dc deploymentconfig.json --compute-target aks-gpu
```

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core.webservice import AksWebservice
from azureml.core.model import InferenceConfig
from random import randint

service_name = "triton-webservice"

config = AksWebservice.deploy_configuration(
    compute_target_name="aks-gpu",
    gpu_cores=1,
    cpu_cores=1,
    memory_gb=4,
    auth_enabled=True,
)

service = Model.deploy(
    workspace=ws,
    name=service_name,
    models=[model],
    deployment_config=config,
    overwrite=True,
)
```
---

[モデルのデプロイの詳細については、こちらのドキュメントを](how-to-deploy-and-where.md)参照してください。

### <a name="call-into-your-deployed-model"></a>デプロイしたモデルを呼び出す

最初に、スコアリング URI とベアラー トークンを取得します。

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)


```azurecli
az ml service show --name=triton-webservice
```
# <a name="python"></a>[Python](#tab/python)

```python
import requests

print(service.scoring_uri)
print(service.get_keys())

```

---

サービスが実行されていることを確認するために、次を実行します。 

```{bash}
!curl -v $scoring_uri/v2/health/ready -H 'Authorization: Bearer '"$service_key"''
```

このコマンドでは、次のような情報が返されます。 `200 OK` に注目してください。この状態は、Web サーバーが実行中であることを示します。

```{bash}
*   Trying 127.0.0.1:8000...
* Connected to localhost (127.0.0.1) port 8000 (#0)
> GET /v2/health/ready HTTP/1.1
> Host: localhost:8000
> User-Agent: curl/7.71.1
> Accept: */*
>
* Mark bundle as not supporting multiuse
< HTTP/1.1 200 OK
HTTP/1.1 200 OK
```

正常性チェックを実行したら、推論のために Triton にデータを送信するクライアントを作成できます。 クライアントの作成の詳細については、[クライアントの例](https://aka.ms/nvidia-client-examples)に関する NVIDIA ドキュメントを参照してください。 [Triton GitHub に Python のサンプル](https://aka.ms/nvidia-triton-docs)もあります。

この時点で、デプロイされた webservice に Python の前および後処理を追加ない場合、作業は完了です。 前および後処理用のこのロジックを追加する場合は、そのまま読み進めてください。

## <a name="optional-re-deploy-with-a-python-entry-script-for-pre--and-post-processing"></a>(オプション) 前および後処理用の Python エントリ スクリプトを使用して再デプロイする

Triton がモデルに使用できることを確認した後、"_エントリ スクリプト_" を定義することで、前処理と後処理のコードを追加できます。 このファイルの名前は `score.py` です。 エントリ スクリプトの詳細については、「[エントリ スクリプトを定義する](how-to-deploy-and-where.md#define-an-entry-script)」を参照してください。

主なステップの 2 つは、`init()` メソッドで Triton HTTP クライアントを初期化し、`run()` 関数でそのクライアントを呼び出すことです。

### <a name="initialize-the-triton-client"></a>Triton クライアントを初期化する

次の例のようなコードを `score.py` ファイルに含めます。 Azure Machine Learning の Triton は、localhost のポート 8000 でアドレス指定されることを想定しています。 この場合の localhost は、この展開の Docker イメージ内にあり、ローカル マシン上のポートではありません。

> [!TIP]
> `tritonhttpclient` pip パッケージは、キュレーションされた `AzureML-Triton` 環境に含まれているので、pip 依存関係として指定する必要はありません。

```python
import tritonhttpclient

def init():
    global triton_client
    triton_client = tritonhttpclient.InferenceServerClient(url="localhost:8000")
```

### <a name="modify-your-scoring-script-to-call-into-triton"></a>Triton を呼び出すようにスコアリング スクリプトを変更する

次の例は、モデルのメタデータを動的に要求する方法を示しています。

> [!TIP]
> Triton クライアントの `.get_model_metadata` メソッドを使用して、Triton で読み込まれたモデルのメタデータを動的に要求できます。 使用例については、[サンプル ノートブック](https://aka.ms/triton-aml-sample)を参照してください。

```python
input = tritonhttpclient.InferInput(input_name, data.shape, datatype)
input.set_data_from_numpy(data, binary_data=binary_data)

output = tritonhttpclient.InferRequestedOutput(
         output_name, binary_data=binary_data, class_count=class_count)

# Run inference
res = triton_client.infer(model_name,
                          [input]
                          request_id='0',
                          outputs=[output])

```

<a id="redeploy"></a>

### <a name="redeploy-with-an-inference-configuration"></a>推論構成を使用した再展開

推論の構成では、エントリ スクリプトと、Python SDK または Azure CLI を使用した Azure Machine Learning 展開プロセスを使用できます。

> [!IMPORTANT]
> `AzureML-Triton` [がキュレーションされた環境](./resource-curated-environments.md)を指定する必要があります。
>
> Python コード例では、`AzureML-Triton` を `My-Triton` という名前の別の環境に複製します。 Azure CLI コードでもこの環境が使用されます。 環境の複製の詳細については、[Environment.Clone()](/python/api/azureml-core/azureml.core.environment.environment#clone-new-name-) のリファレンスを参照してください。

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

> [!TIP]
> 推論構成の作成の詳細については、「[推論構成スキーマ](./reference-azure-machine-learning-cli.md#inference-configuration-schema)」を参照してください。

```azurecli
az ml model deploy -n triton-densenet-onnx \
-m densenet_onnx:1 \
--ic inference-config.json \
-e My-Triton --dc deploymentconfig.json \
--overwrite --compute-target=aks-gpu
```

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core.webservice import LocalWebservice
from azureml.core import Environment
from azureml.core.model import InferenceConfig


local_service_name = "triton-bidaf-onnx"
env = Environment.get(ws, "AzureML-Triton").clone("My-Triton")

for pip_package in ["nltk"]:
    env.python.conda_dependencies.add_pip_package(pip_package)

inference_config = InferenceConfig(
    entry_script="score_bidaf.py",  # This entry script is where we dispatch a call to the Triton server
    source_directory=os.path.join("..", "scripts"),
    environment=env
)

local_config = LocalWebservice.deploy_configuration(
    port=6789
)

local_service = Model.deploy(
    workspace=ws,
    name=local_service_name,
    models=[model],
    inference_config=inference_config,
    deployment_config=local_config,
    overwrite=True)

local_service.wait_for_deployment(show_output = True)
print(local_service.state)
# Print the URI you can use to call the local deployment
print(local_service.scoring_uri)
```

---

展開が完了すると、スコアリング URI が表示されます。 今回のローカル展開では、`http://localhost:6789/score` です。 クラウドにデプロイする場合は、[az ml service show](/cli/azure/ext/azure-cli-ml/ml/service#ext_azure_cli_ml_az_ml_service_show) CLI コマンドを使用してスコアリング URI を取得できます。

スコアリング URI に推論要求を送信するクライアントを作成する方法については、[Web サービスとして展開されたモデルの使用](how-to-consume-web-service.md)に関するページを参照してください。

### <a name="setting-the-number-of-workers"></a>worker の数の設定

デプロイ内の worker の数を設定するには、環境変数 `WORKER_COUNT` を設定します。 `env` と呼ばれる [Environment](/python/api/azureml-core/azureml.core.environment.environment) オブジェクトがあると仮定した場合、以下を実行できます。

```{py}
env.environment_variables["WORKER_COUNT"] = "1"
```

これにより、指定した数の worker をスピン アップするよう Azure ML が指示されます。


## <a name="clean-up-resources"></a>リソースをクリーンアップする

Azure Machine Learning ワークスペースの使用を継続する予定で、デプロイされたサービスを削除する場合は、次のオプションのいずれかを使用します。


# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli
az ml service delete -n triton-densenet-onnx
```
# <a name="python"></a>[Python](#tab/python)

```python
local_service.delete()
```


---

## <a name="next-steps"></a>次のステップ

* [Azure Machine Learning での Triton のエンドツーエンドのサンプルを参照する](https://aka.ms/aml-triton-sample)
* [Triton クライアントの例](https://aka.ms/nvidia-client-examples)を確認する
* [Triton 推論サーバーのドキュメント](https://aka.ms/nvidia-triton-docs)を読む
* [失敗したデプロイのトラブルシューティング](how-to-troubleshoot-deployment.md)
* [Azure Kubernetes Service にデプロイする](how-to-deploy-azure-kubernetes-service.md)
* [Web サービスを更新する](how-to-deploy-update-web-service.md)
* [実稼働環境でモデルのデータを収集する](how-to-enable-data-collection.md)