---
title: モデルをデプロイする方法と場所
titleSuffix: Azure Machine Learning service
description: お客様の Azure Machine Learning service モデルをデプロイする方法と場所について説明します (Azure Container Instances、Azure Kubernetes Service、Azure IoT Edge、フィールド プログラマブル ゲート アレイ)。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 08/06/2019
ms.custom: seoapril2019
ms.openlocfilehash: acb3717f0e71ca1e67f1ddec79a259935f6cc539
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2019
ms.locfileid: "69897680"
---
# <a name="deploy-models-with-the-azure-machine-learning-service"></a>Azure Machine Learning service を使用してモデルをデプロイする

Web サービスとして Azure クラウドに、または IoT Edge デバイスに機械学習モデルをデプロイする方法を説明します。

モデルを[どこにデプロイするか](#target)に関係なく、ワークフローは同様です。

1. モデルを登録します。
1. デプロイの準備をします (アセット、使用、コンピューティング ターゲットを指定する)。
1. コンピューティング ターゲットにモデルをデプロイします。
1. デプロイしたモデル (Web サービスとも呼ばれる) をテストします。

デプロイ ワークフローに関連する概念の詳細については、「[Azure Machine Learning service でモデルを管理、デプロイ、および監視する](concept-model-management-and-deployment.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

- Azure Machine Learning ワークスペース。 詳細については、[Azure Machine Learning service ワークスペースの作成](how-to-manage-workspace.md) に関する記事を参照してください。

- モデル。 トレーニング済みのモデルがない場合、[こちらのチュートリアル](https://aka.ms/azml-deploy-cloud)で与えられたモデルと依存関係のファイルを使用できます。

- [Machine Learning サービス向けの Azure CLI 拡張機能](reference-azure-machine-learning-cli.md)、[Azure Machine Learning Python SDK](https://aka.ms/aml-sdk)、または [Azure Machine Learning Visual Studio Code 拡張機能](how-to-vscode-tools.md)。

## <a name="connect-to-your-workspace"></a>ワークスペースに接続する

次のコードでは、ローカル開発環境にキャッシュされた情報を使用して Azure Machine Learning service ワークスペースに接続する方法を示します。

**SDK を使用する**

```python
from azureml.core import Workspace
ws = Workspace.from_config(path=".file-path/ws_config.json")
```

SDK を使用してワークスペースに接続する方法について詳しくは、[Azure Machine Learning SDK for Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace) に関する記事をご覧ください。

**CLI を使用する**

CLI を使用するときは、`-w` パラメーターまたは `--workspace-name` パラメーターを使用して、コマンドのワークスペースを指定します。

**VS コードを使用する**

VS Code を使用するときは、グラフィカル インターフェイスを使用してワークスペースを選択します。 詳しくは、VS Code 拡張機能のドキュメントの「[モデルを展開して管理する](how-to-vscode-tools.md#deploy-and-manage-models)」をご覧ください。

## <a id="registermodel"></a> モデルを登録する

登録済みモデルは、モデルを構成する 1 つまたは複数のファイルの論理コンテナーです。 たとえば、複数のファイルに格納されているモデルがある場合は、ワークスペースに単一モデルとしてそれらを登録できます。 登録後は、その登録済みモデルをダウンロードするかデプロイし、登録されたすべてのファイルを受信できます。

> [!TIP]
> モデルを登録するときは、(トレーニングの実行から) クラウドの場所またはローカル ディレクトリのパスを指定します。 このパスは、登録プロセスの一部としてアップロードするファイルを見つけるためだけのものです。エントリ スクリプトで使用されるパスと一致する必要はありません。 詳しくは、「[Get_model_path とは何か](#what-is-get_model_path)」をご覧ください。

機械学習モデルをご使用の Azure Machine Learning ワークスペースに登録します。 これらのモデルは、Azure Machine Learning から取得することも、どこか他の場所から取得することもできます。 次の例では、モデルを登録する方法を示します。

### <a name="register-a-model-from-an-experiment-run"></a>実験の実行からモデルを登録する

このセクションのコード スニペットでは、トレーニング実行からモデルを登録する方法が示されています。

> [!IMPORTANT]
> これらのスニペットでは、以前にトレーニング実行を行ったことがあり、`run` オブジェクト (SDK の例) または実行 ID の値 (CLI の例) にアクセスできるものと想定されています。 トレーニング モデルについて詳しくは、[モデルのトレーニング用のコンピューティング先の作成と使用](how-to-set-up-training-targets.md)に関する記事をご覧ください。

+ **SDK を使用する**

  ```python
  model = run.register_model(model_name='sklearn_mnist', model_path='outputs/sklearn_mnist_model.pkl')
  print(model.name, model.id, model.version, sep='\t')
  ```

  `model_path` では、クラウドでのモデルの場所が示されています。 この例では、1 つのファイルへのパスが使用されます。 モデルの登録に複数のファイルを含めるには、`model_path` を、それらのファイルが含まれているディレクトリに設定します。

+ **CLI を使用する**

  ```azurecli-interactive
  az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --run-id myrunid
  ```

  [!INCLUDE [install extension](../../../includes/machine-learning-service-install-extension.md)]

  `--asset-path` では、クラウドでのモデルの場所が示されています。 この例では、1 つのファイルへのパスが使用されます。 モデルの登録に複数のファイルを含めるには、`--asset-path` を、それらのファイルが含まれているディレクトリに設定します。

+ **VS コードを使用する**

  [VS コード](how-to-vscode-tools.md#deploy-and-manage-models)拡張機能により、任意のモデル ファイルまたはフォルダーを使用してモデルを登録します。

### <a name="register-a-model-from-a-local-file"></a>ローカル ファイルからモデルを登録する

モデルへの**ローカル パス**を指定することで、モデルを登録できます。 フォルダーまたは 1 個のファイルを提供できます。 この方法を使用すると、Azure Machine Learning service でトレーニングされてからダウンロードされたモデルと、Azure Machine Learning の外部でトレーニングされたモデルの両方を登録できます。

[!INCLUDE [trusted models](../../../includes/machine-learning-service-trusted-model.md)]

+ **Python SDK による ONNX の例:**

    ```python
    import os
    import urllib.request
    from azureml.core import Model
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

  モデルの登録に複数のファイルを含めるには、`model_path` を、それらのファイルが含まれているディレクトリに設定します。

+ **CLI を使用する**

  ```azurecli-interactive
  az ml model register -n onnx_mnist -p mnist/model.onnx
  ```

  モデルの登録に複数のファイルを含めるには、`-p` を、それらのファイルが含まれているディレクトリに設定します。

**推定所要時間**: 約 10 秒。

詳細については、[Model クラス](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py)のリファレンス ドキュメントを参照してください。

Azure Machine Learning service 以外でトレーニングされたモデルの使用の詳細については、[既存のモデルをデプロイする方法](how-to-deploy-existing-model.md)に関する記事を参照してください。

<a name="target"></a>

## <a name="choose-a-compute-target"></a>コンピューティング ターゲットを選択する

次のコンピューティング ターゲット、またはコンピューティング リソースを使用して、Web サービスのデプロイをホストすることができます。 

[!INCLUDE [aml-compute-target-deploy](../../../includes/aml-compute-target-deploy.md)]

## <a name="prepare-to-deploy"></a>デプロイを準備する

モデルをデプロイするには、次のものが必要です。

* __エントリ スクリプト__。 このスクリプトは、要求を受け入れ、モデルを使用してその要求にスコアを付け、その結果を返します。

    > [!IMPORTANT]
    > エントリ スクリプトは、モデルに固有のもので、受信要求データの形式、モデルで想定されるデータの形式、およびクライアントに返されるデータの形式を理解する必要があります。
    >
    > 要求データがモデルで使用できない形式になっている場合、スクリプトはそれを受け入れ可能な形式に変換できます。 また、応答をクライアントに返す前に変換することもできます。

    > [!IMPORTANT]
    > Azure Machine Learning SDK には、データストアまたはデータセットにアクセスするための Web サービスまたは IoT Edge のデプロイの手段は用意されていません。 デプロイの外部に格納されているデータにアクセスするためにデプロイされたモデルが必要な場合 (Azure Storage アカウントの場合など)、関連する SDK を使用してカスタム コード ソリューションを開発する必要があります。 たとえば、[Azure Storage SDK for Python](https://github.com/Azure/azure-storage-python) です。
    >
    > シナリオに適したもう 1 つの方法として[バッチ予測](how-to-run-batch-predictions.md)があります。これにより、スコアリング時にデータストアにアクセスすることができます。

* エントリ スクリプトやモデルの実行に必要なヘルパー スクリプトや Python/Conda パッケージなどの**依存関係**

* デプロイされたモデルをホストするコンピューティング先の__デプロイ構成__。 この構成には、モデルの実行に必要なメモリと CPU の要件などが記述されます。

これらのエンティティは、__推論の構成__、および__デプロイ構成__にカプセル化されます。 推論の構成では、エントリ スクリプトとその他の依存関係が参照されます。 これらの構成は、SDK を使用する場合はプログラムで定義され、CLI を使用してデプロイを実行する場合は JSON ファイルとして定義されます。

### <a id="script"></a> 1.エントリ スクリプトと依存関係を定義する

エントリ スクリプトは、デプロイされた Web サービスに送信されたデータを受け取り、それをモデルに渡します。 次に、モデルから返された応答を受け取り、それをクライアントに返します。 **このスクリプトはこのモデルに固有のものです**。そのため、モデルが受け入れ、返すデータを理解する必要があります。

このスクリプトにはモデルの読み込みと実行の 2 つの関数が含まれています。

* `init()`:通常は、この関数によってモデルがグローバル オブジェクトに読み込まれます。 この関数は、Web サービスの Docker コンテナーの起動時に 1 回だけ実行されます。

* `run(input_data)`:この関数では、モデルを使用して、入力データに基づいて値が予測されます。 実行に対する入力と出力は、通常、JSON を使用してシリアル化およびシリアル化解除が実行されます。 また、未加工のバイナリ データも使用できます。 モデルに送信する前、またはクライアントに返す前のデータを変換することができます。

#### <a name="what-is-get_model_path"></a>Get_model_path とは何か

モデルを登録するときに、レジストリ内のモデルを管理するために使用されるモデル名を指定します。 この名前は、ローカル ファイル システム上のモデル ファイルのパスを取得する [Model.get_model_path()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) で使用します。 フォルダーまたはファイルのコレクションを登録した場合、この API では、これらのファイルを含むディレクトリのパスが返されます。

モデルを登録する場合、ローカルに、またはサービスのデプロイ時に、モデルの配置場所に対応する名前を指定します。

下の例では (`sklearn_mnist` という名前で登録された) `sklearn_mnist_model.pkl` という名前の単一のファイルのパスが返されます。

```python
model_path = Model.get_model_path('sklearn_mnist')
```

<a id="schema"></a>

#### <a name="optional-automatic-schema-generation"></a>(省略可能) スキーマの自動生成

Web サービスのスキーマを自動生成するには、定義された型オブジェクトのいずれかのコンストラクターに入力や出力のサンプルを指定します。型とサンプルはスキーマを自動生成するために使用されます。 その後、Azure Machine Learning サービスによって、デプロイ中に、Web サービスの [OpenAPI](https://swagger.io/docs/specification/about/) (Swagger) 仕様が作成されます。

現在サポートされている型は次のとおりです。

* `pandas`
* `numpy`
* `pyspark`
* 標準的な Python オブジェクト

スキーマ生成を使用するには、Conda 環境ファイルに `inference-schema` パッケージを追加します。

##### <a name="example-dependencies-file"></a>依存関係ファイルの例

次の YAML は、推論のための Conda 依存関係ファイルの例です。

```YAML
name: project_environment
dependencies:
  - python=3.6.2
  - pip:
    - azureml-defaults
    - scikit-learn==0.20.0
    - inference-schema[numpy-support]
```

自動スキーマ生成を使用する場合、エントリ スクリプトで `inference-schema` パッケージをインポートする**必要があります**。

変数 `input_sample` と `output_sample` で入力と出力のサンプル形式を定義します。これは Web サービスの要求と応答の形式を表します。 これらのサンプルを、`run()` 関数の入力と出力の関数デコレーターで使用します。 下の scikit-learn の例では、スキーマ生成が使用されています。

##### <a name="example-entry-script"></a>エントリ スクリプトの例

次の例では、JSON データを受け取り、返す方法が示されています。

```python
#example: scikit-learn and Swagger
import json
import numpy as np
from sklearn.externals import joblib
from sklearn.linear_model import Ridge
from azureml.core.model import Model

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType


def init():
    global model
    # note here "sklearn_regression_model.pkl" is the name of the model registered under
    # this is a different behavior than before when the code is run locally, even though the code is the same.
    model_path = Model.get_model_path('sklearn_regression_model.pkl')
    # deserialize the model file back into a sklearn model
    model = joblib.load(model_path)


input_sample = np.array([[10, 9, 8, 7, 6, 5, 4, 3, 2, 1]])
output_sample = np.array([3726.995])


@input_schema('data', NumpyParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        result = model.predict(data)
        # you can return any datatype as long as it is JSON-serializable
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

次の例では、Dataframe を使用して、`<key: value>` ディクショナリとして入力データを定義する方法を示しています。 この方法は、Power BI からデプロイされた Web サービスを使用する場合にサポートされています ([Power BI から Web サービスを使用する方法について](https://docs.microsoft.com/power-bi/service-machine-learning-integration))。

```python
import json
import pickle
import numpy as np
import pandas as pd
import azureml.train.automl
from sklearn.externals import joblib
from azureml.core.model import Model

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType
from inference_schema.parameter_types.pandas_parameter_type import PandasParameterType


def init():
    global model
    # replace model_name with your actual model name, if needed
    model_path = Model.get_model_path('model_name')
    # deserialize the model file back into a sklearn model
    model = joblib.load(model_path)


input_sample = pd.DataFrame(data=[{
    # This is a decimal type sample. Use the data type that reflects this column in your data
    "input_name_1": 5.1,
    # This is a string type sample. Use the data type that reflects this column in your data
    "input_name_2": "value2",
    # This is a integer type sample. Use the data type that reflects this column in your data
    "input_name_3": 3
}])

# This is a integer type sample. Use the data type that reflects the expected result
output_sample = np.array([0])


@input_schema('data', PandasParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        result = model.predict(data)
        # you can return any datatype as long as it is JSON-serializable
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

他にもスクリプトの例が必要であれば、次のサンプルをご覧ください。

* Pytorch: [https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch)
* TensorFlow: [https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow)
* Keras: [https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras)
* ONNX: [https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/)

<a id="binary"></a>

#### <a name="binary-data"></a>バイナリ データ

モデルがバイナリ データ (画像など) を受け付ける場合は、デプロイで使用される `score.py` ファイルを生の HTTP 要求を受け付けるように変更する必要があります。 生データを受け入れるには、エントリ スクリプトで `AMLRequest` クラスを使用して、`@rawhttp` デコレーターを `run()` 関数に追加します。

バイナリ データを受け付ける `score.py` の例を次に示します。

```python
from azureml.contrib.services.aml_request import AMLRequest, rawhttp
from azureml.contrib.services.aml_response import AMLResponse


def init():
    print("This is init()")


@rawhttp
def run(request):
    print("This is run()")
    print("Request: [{0}]".format(request))
    if request.method == 'GET':
        # For this example, just return the URL for GETs
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        # For a real world solution, you would load the data from reqBody
        # and send to the model. Then return the response.

        # For demonstration purposes, this example just returns the posted data as the response.
        return AMLResponse(reqBody, 200)
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> `AMLRequest` クラスは `azureml.contrib` 名前空間にあります。 この名前空間内のものは、このサービスが改善されるに従って頻繁に変更されます。 そのため、この名前空間内のものはすべて Microsoft によって完全にサポートされているものではなく、プレビューとして見なされる必要があります。
>
> これをローカルの開発環境でテストする必要がある場合は、次のコマンドを使用して、コンポーネントをインストールできます。
>
> ```shell
> pip install azureml-contrib-services
> ```

<a id="cors"></a>

#### <a name="cross-origin-resource-sharing-cors"></a>クロスオリジン リソース共有 (CORS)

クロスオリジン リソース共有を使用すると、Web ページ上のリソースを他のドメインから要求することができます。 CORS は、クライアント要求で送信され、サービス応答で返される HTTP ヘッダーに基づいて機能します。 CORS と有効なヘッダーについて詳しくは、Wikipedia の[クロスオリジン リソース共有](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing)に関する説明を参照してください。

CORS をサポートするようにモデル デプロイを構成するには、エントリ スクリプトで `AMLResponse` クラスを使用します。 このクラスを使用すると、応答オブジェクトにヘッダーを設定できます。

次の例では、エントリ スクリプトから応答に対して `Access-Control-Allow-Origin` ヘッダーを設定しています。

```python
from azureml.contrib.services.aml_response import AMLResponse

def init():
    print("This is init()")

def run(request):
    print("This is run()")
    print("Request: [{0}]".format(request))
    if request.method == 'GET':
        # For this example, just return the URL for GETs
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        # For a real world solution, you would load the data from reqBody
        # and send to the model. Then return the response.

        # For demonstration purposes, this example
        # adds a header and returns the request body
        resp = AMLResponse(reqBody, 200)
        resp.headers['Access-Control-Allow-Origin'] = "http://www.example.com"
        return resp
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> `AMLResponse` クラスは `azureml.contrib` 名前空間にあります。 この名前空間内のものは、このサービスが改善されるに従って頻繁に変更されます。 そのため、この名前空間内のものはすべて Microsoft によって完全にサポートされているものではなく、プレビューとして見なされる必要があります。
>
> これをローカルの開発環境でテストする必要がある場合は、次のコマンドを使用して、コンポーネントをインストールできます。
>
> ```shell
> pip install azureml-contrib-services
> ```

### <a name="2-define-your-inferenceconfig"></a>2.InferenceConfig を定義する

推論構成では、予測するモデルを構成する方法が説明されます。 この構成は、エントリ スクリプトの一部ではありません。これは、エントリ スクリプトを参照し、デプロイに必要なすべてのリソースを検索するために使用されます。 これは、後でモデルを実際にデプロイするときに使用します。

次の例では、推論構成を作成する方法が示されています。 この構成では、ランタイム、エントリ スクリプト、および (必要に応じて) Conda 環境ファイルを指定します。

```python
from azureml.core.model import InferenceConfig

inference_config = InferenceConfig(runtime="python",
                                   entry_script="x/y/score.py",
                                   conda_file="env/myenv.yml")
```

詳細については、[InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) クラスのリファレンスを参照してください。

推論構成を利用したカスタム Docker イメージの使用については、[カスタム Docker イメージを使用してモデルをデプロイする方法](how-to-deploy-custom-docker-image.md)に関するページを参照してください。

### <a name="cli-example-of-inferenceconfig"></a>InferenceConfig の CLI の例

[!INCLUDE [inference config](../../../includes/machine-learning-service-inference-config.md)]

次のコマンドでは、CLI を使用してモデルをデプロイする方法を示します。

```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json
```

この例では、構成に次の項目が含まれています。

* このモデルには Python が必要であるということ
* [エントリ スクリプト](#script)。デプロイされたサービスに送信される Web 要求の処理に必要です。
* 推論を行うために必要な Python パッケージを記述する conda ファイル

推論構成を利用したカスタム Docker イメージの使用については、[カスタム Docker イメージを使用してモデルをデプロイする方法](how-to-deploy-custom-docker-image.md)に関するページを参照してください。

### <a name="3-define-your-deployment-configuration"></a>手順 3.デプロイ構成を定義する

デプロイの前にデプロイ構成を定義する必要があります。 __デプロイ構成は、Web サービスがホストされるコンピューティング ターゲットに固有となります__。 たとえば、ローカルでデプロイするとき、サービスにより要求が受け取られるポートを指定する必要があります。 デプロイ構成は、エントリ スクリプトの一部ではありません。 これは、モデルとエントリ スクリプトをホストするコンピューティング先の特性を定義するために使用されます。

また、コンピューティング リソースを作成する必要があります。 たとえば、Azure Kubernetes Service を自分のワークスペースにまだ関連付けていない場合です。

次の表には、コンピューティング ターゲット別にデプロイ構成を作成する例があります。

| コンピューティング ターゲット | デプロイ構成の例 |
| ----- | ----- |
| ローカル | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Azure Container Instances | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Azure Kubernetes Service | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

これらのローカル、ACI、および AKS Web サービスの各クラスは、`azureml.core.webservice` からインポートできます。

```python
from azureml.core.webservice import AciWebservice, AksWebservice, LocalWebservice
```

> [!TIP]
> モデルをサービスとしてデプロイする前に、最適な CPU とメモリの要件を判断するためにプロファイリングを実行できます。 SDK または CLI を使用して、ご自分のモデルをプロファイリングできます。 詳細については、[profile()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#profile-workspace--profile-name--models--inference-config--input-data-) および [az ml model profile](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-profile) のリファレンスを参照してください。
>
> モデルのプロファイル結果は、`Run` オブジェクトとして出力されます。 詳細については、[ModelProfile](https://docs.microsoft.com/python/api/azureml-core/azureml.core.profile.modelprofile?view=azure-ml-py) クラスのリファレンスを参照してください。

## <a name="deploy-to-target"></a>ターゲットにデプロイする

デプロイでは、推論構成のデプロイ構成を使用して、モデルをデプロイします。 デプロイ プロセスは、コンピューティング ターゲットに関係なく類似しています。 AKS へのデプロイは、AKS クラスターへの参照を指定する必要があるため、少し異なります。

### <a id="local"></a> ローカル デプロイ

ローカルにデプロイするには、お使いのローカル コンピューターに Docker がインストールされている必要があります。

#### <a name="using-the-sdk"></a>SDK を使用する

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

詳細については、[LocalWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py)、[Model.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config--deployment-config-none--deployment-target-none-)、および [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py) のリファレンス ドキュメントを参照してください。

#### <a name="using-the-cli"></a>CLI の使用

CLI を使用してデプロイするには、次のコマンドを使用します。 登録されているモデルの名前とバージョンに `mymodel:1` を置き換えます。

```azurecli-interactive
az ml model deploy -m mymodel:1 -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [aml-local-deploy-config](../../../includes/machine-learning-service-local-deploy-config.md)]

詳細については、[az ml model deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) のリファレンスを参照してください。

### <a id="notebookvm"></a> NotebookVM Web サービス (DEVTEST)

「[ノートブック VM にモデルをデプロイする](how-to-deploy-local-container-notebook-vm.md)」をご覧ください。

### <a id="aci"></a> Azure Container Instances (DEVTEST)

[Azure Container Instances へのデプロイ](how-to-deploy-azure-container-instance.md)に関する記事を参照してください。

### <a id="aks"></a>Azure Kubernetes Service (DEVTEST & PRODUCTION)

[Azure Kubernetes Service へのデプロイ](how-to-deploy-azure-kubernetes-service.md)に関する記事を参照してください。

## <a name="consume-web-services"></a>Web サービスを使用する

デプロイされた Web サービスはすべて、REST API を提供します。そのため、さまざまなプログラミング言語でクライアント アプリケーションを作成できます。 サービスに対してキー認証を有効にしている場合、要求ヘッダーでトークンとしてサービス キーを指定する必要があります。
サービスに対してトークン認証を有効にしている場合、要求ヘッダーでベアラー トークンとして Azure Machine Learning JWT トークンを指定する必要があります。

> [!TIP]
> サービスをデプロイした後、スキーマ JSON ドキュメントを取得できます。 ローカル Web サービスの Swagger ファイルへの URI を取得するには、デプロイされた Web サービスの [swagger_uri プロパティ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri)を使用します (例: `service.swagger_uri`)。

### <a name="request-response-consumption"></a>要求 - 応答の使用量

Python でサービスを呼び出す方法の例:
```python
import requests
import json

headers = {'Content-Type': 'application/json'}

if service.auth_enabled:
    headers['Authorization'] = 'Bearer '+service.get_keys()[0]
elif service.token_auth_enabled:
    headers['Authorization'] = 'Bearer '+service.get_token()[0]

print(headers)

test_sample = json.dumps({'data': [
    [1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
    [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
]})

response = requests.post(
    service.scoring_uri, data=test_sample, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

詳細については、[Web サービスを使用するクライアント アプリケーションの作成](how-to-consume-web-service.md)に関するページを参照してください。

### <a name="web-service-schema-openapi-specification"></a>Web サービスのスキーマ (OpenAPI 仕様)

デプロイで自動スキーマ生成を使用した場合は、[swagger_uri プロパティ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri)を使用して、サービスに対する OpenAPI 仕様のアドレスを取得できます。 たとえば、「 `print(service.swagger_uri)` 」のように入力します。 仕様を取得するには、GET 要求を使用します (または、ブラウザーで URI を開きます)。

次の JSON ドキュメントは、デプロイに対して生成されるスキーマ (OpenAPI 仕様) の例です。

```json
{
    "swagger": "2.0",
    "info": {
        "title": "myservice",
        "description": "API specification for the Azure Machine Learning service myservice",
        "version": "1.0"
    },
    "schemes": [
        "https"
    ],
    "consumes": [
        "application/json"
    ],
    "produces": [
        "application/json"
    ],
    "securityDefinitions": {
        "Bearer": {
            "type": "apiKey",
            "name": "Authorization",
            "in": "header",
            "description": "For example: Bearer abc123"
        }
    },
    "paths": {
        "/": {
            "get": {
                "operationId": "ServiceHealthCheck",
                "description": "Simple health check endpoint to ensure the service is up at any given point.",
                "responses": {
                    "200": {
                        "description": "If service is up and running, this response will be returned with the content 'Healthy'",
                        "schema": {
                            "type": "string"
                        },
                        "examples": {
                            "application/json": "Healthy"
                        }
                    },
                    "default": {
                        "description": "The service failed to execute due to an error.",
                        "schema": {
                            "$ref": "#/definitions/ErrorResponse"
                        }
                    }
                }
            }
        },
        "/score": {
            "post": {
                "operationId": "RunMLService",
                "description": "Run web service's model and get the prediction output",
                "security": [
                    {
                        "Bearer": []
                    }
                ],
                "parameters": [
                    {
                        "name": "serviceInputPayload",
                        "in": "body",
                        "description": "The input payload for executing the real-time machine learning service.",
                        "schema": {
                            "$ref": "#/definitions/ServiceInput"
                        }
                    }
                ],
                "responses": {
                    "200": {
                        "description": "The service processed the input correctly and provided a result prediction, if applicable.",
                        "schema": {
                            "$ref": "#/definitions/ServiceOutput"
                        }
                    },
                    "default": {
                        "description": "The service failed to execute due to an error.",
                        "schema": {
                            "$ref": "#/definitions/ErrorResponse"
                        }
                    }
                }
            }
        }
    },
    "definitions": {
        "ServiceInput": {
            "type": "object",
            "properties": {
                "data": {
                    "type": "array",
                    "items": {
                        "type": "array",
                        "items": {
                            "type": "integer",
                            "format": "int64"
                        }
                    }
                }
            },
            "example": {
                "data": [
                    [ 10, 9, 8, 7, 6, 5, 4, 3, 2, 1 ]
                ]
            }
        },
        "ServiceOutput": {
            "type": "array",
            "items": {
                "type": "number",
                "format": "double"
            },
            "example": [
                3726.995
            ]
        },
        "ErrorResponse": {
            "type": "object",
            "properties": {
                "status_code": {
                    "type": "integer",
                    "format": "int32"
                },
                "message": {
                    "type": "string"
                }
            }
        }
    }
}
```

仕様の詳細については、「[Open API 仕様](https://swagger.io/specification/)」を参照してください。

仕様からクライアント ライブラリを作成できるユーティリティについては、[swagger-codegen](https://github.com/swagger-api/swagger-codegen) を参照してください。

### <a id="azuremlcompute"></a> バッチ推論
Azure Machine Learning のコンピューティング ターゲットは、Azure Machine Learning service によって作成され、管理されます。 これは Azure Machine Learning パイプラインからのバッチ予測に使用できます。

Azure Machine Learning コンピューティングを使用したバッチ推論のチュートリアルについては、[バッチ予測を実行する方法](how-to-run-batch-predictions.md)に関する記事を参照してください。

### <a id="iotedge"></a> IoT Edge の推論
エッジにデプロイするためのサポートは現在、プレビューの段階にあります。 詳細については、[IoT Edge モジュールとして Azure Machine Learning をデプロイする](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning)方法に関する記事を参照してください。


## <a id="update"></a> Web サービスを更新する

[!INCLUDE [aml-update-web-service](../../../includes/machine-learning-update-web-service.md)]

## <a name="continuous-model-deployment"></a>継続的なモデル デプロイ 

[Azure DevOps](https://azure.microsoft.com/services/devops/) 用の Machine Learning 拡張機能を使用して、モデルを継続的にデプロイできます。 Azure DevOps 用の Machine Learning 拡張機能を使用すると、新しい機械学習モデルが Azure Machine Learning service ワークスペースに登録されたときにデプロイ パイプラインをトリガーできます。 

1. [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops) にサインアップします。そうすると、任意のプラットフォームや任意のクラウドへのご自分のアプリケーションの継続的インテグレーションとデリバリーを実現できます。 Azure Pipelines は [ML パイプライン](concept-ml-pipelines.md#compare)とは異なります。 

1. [Azure DevOps プロジェクトを作成します。](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

1. [Azure Pipelines 用の Machine Learning 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml&targetId=6756afbe-7032-4a36-9cb6-2771710cadc2&utm_source=vstsproduct&utm_medium=ExtHubManageList)をインストールします。 

1. ご自分のすべての成果物にアクセスするために、__サービス接続__を使用して、ご自分の Azure Machine Learning service ワークスペースへのサービス プリンシパル接続を設定します。 プロジェクト設定に移動し、[サービス接続] をクリックして、[Azure Resource Manager] を選択します。

    [![view-service-connection](media/how-to-deploy-and-where/view-service-connection.png)](media/how-to-deploy-and-where/view-service-connection-expanded.png) 

1. __スコープ レベル__として AzureMLWorkspace を定義し、以降のパラメーターを入力します。

    ![view-azure-resource-manager](media/how-to-deploy-and-where/resource-manager-connection.png)

1. 次に、Azure Pipelines を使用してご自分の機械学習モデルを継続的にデプロイするために、パイプラインの下で __[リリース]__ を選択します。 新しい成果物を追加し、AzureML Model 成果物と前の手順で作成したサービス接続を選択します。 デプロイをトリガーするモデルとバージョンを選択します。 

    [![select-AzureMLmodel-artifact](media/how-to-deploy-and-where/enable-modeltrigger-artifact.png)](media/how-to-deploy-and-where/enable-modeltrigger-artifact-expanded.png)

1. ご自分のモデル成果物に対してモデル トリガーを有効にします。 トリガーをオンにすると、そのモデルの指定したバージョン (つまり 最新バージョン) がご自分のワークスペースに登録されるたびに、Azure DevOps のリリース パイプラインがトリガーされます。 

    [![enable-model-trigger](media/how-to-deploy-and-where/set-modeltrigger.png)](media/how-to-deploy-and-where/set-modeltrigger-expanded.png)

他のサンプル プロジェクトと例については、次のサンプル リポジトリを参照してください。

* [https://github.com/Microsoft/MLOps](https://github.com/Microsoft/MLOps)
* [https://github.com/Microsoft/MLOpsPython](https://github.com/microsoft/MLOpsPython)

## <a name="clean-up-resources"></a>リソースのクリーンアップ
デプロイされた Web サービスを削除するには、`service.delete()` を使用します。
登録済みのモデルを削除するには、`model.delete()` を使用します。

詳細については、[WebService.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--) と [Model.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--) のリファレンス ドキュメントを参照してください。

## <a name="next-steps"></a>次の手順
* [カスタム Docker イメージを使用してモデルをデプロイする方法](how-to-deploy-custom-docker-image.md)
* [デプロイ トラブルシューティング](how-to-troubleshoot-deployment.md)
* [SSL を使用して Azure Machine Learning Web サービスをセキュリティで保護する](how-to-secure-web-service.md)
* [Web サービスとしてデプロイされた ML モデルを使用する](how-to-consume-web-service.md)
* [Application Insights を使用して Azure Machine Learning のモデルを監視する](how-to-enable-app-insights.md)
* [実稼働環境でモデルのデータを収集する](how-to-enable-data-collection.md)

