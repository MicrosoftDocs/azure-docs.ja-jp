---
title: モデルをデプロイする方法と場所
titleSuffix: Azure Machine Learning
description: Azure Container Instances、Azure Kubernetes Service、Azure IoT Edge、フィールド プログラマブル ゲート アレイなど、Azure Machine Learning モデルをデプロイする方法と場所について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 09/13/2019
ms.custom: seoapril2019
ms.openlocfilehash: bb86d551d83668a3558cf63827a64a481cf87e02
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926926"
---
# <a name="deploy-models-with-azure-machine-learning"></a>Azure Machine Learning を使用してモデルをデプロイする
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Web サービスとして Azure クラウドに、または Azure IoT Edge デバイスに機械学習モデルをデプロイする方法を説明します。

モデルを[どこにデプロイするか](#target)に関係なく、ワークフローは同様です。

1. モデルを登録します。
1. デプロイの準備をします。 (アセット、用途、コンピューティング ターゲットを指定する)。
1. コンピューティング ターゲットにモデルをデプロイします。
1. デプロイしたモデル (Web サービスとも呼ばれる) をテストします。

デプロイ ワークフローに関連する概念の詳細については、[Azure Machine Learning でのモデルの管理、デプロイ、監視](concept-model-management-and-deployment.md)に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件

- Azure Machine Learning ワークスペース。 詳細については、[Azure Machine Learning ワークスペースの作成](how-to-manage-workspace.md)に関するページをご覧ください。

- モデル。 トレーニング済みのモデルがない場合、[こちらのチュートリアル](https://aka.ms/azml-deploy-cloud)で提供されているモデルと依存関係のファイルを使用できます。

- [Machine Learning service 向けの Azure CLI 拡張機能](reference-azure-machine-learning-cli.md)、[Azure Machine Learning SDK for Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)、または [Azure Machine Learning Visual Studio Code 拡張機能](how-to-vscode-tools.md)。

## <a name="connect-to-your-workspace"></a>ワークスペースに接続する

次のコードでは、ローカル開発環境にキャッシュされた情報を使用して Azure Machine Learning ワークスペースに接続する方法を示します。

+ **SDK を使用する**

   ```python
   from azureml.core import Workspace
   ws = Workspace.from_config(path=".file-path/ws_config.json")
   ```

  SDK を使用してワークスペースに接続する方法の詳細については、[Azure Machine Learning SDK for Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace) のドキュメントをご覧ください。

+ **CLI を使用する**

   CLI を使用するときは、`-w` パラメーターまたは `--workspace-name` パラメーターを使用して、コマンドのワークスペースを指定します。

+ **VS コードを使用する**

   VS コードを使用する場合は、グラフィカル インターフェイスを使用してワークスペースを選択します。 詳しくは、VS Code 拡張機能のドキュメントの「[モデルを展開して管理する](how-to-vscode-tools.md#deploy-and-manage-models)」をご覧ください。

## <a id="registermodel"></a> モデルを登録する

登録済みモデルは、モデルを構成する 1 つまたは複数のファイルの論理コンテナーです。 たとえば、複数のファイルに格納されているモデルがある場合は、ワークスペースに単一モデルとしてそれらを登録できます。 ファイルの登録後は、その登録済みモデルをダウンロードするかデプロイし、登録されたすべてのファイルを受信できます。

> [!TIP]
> モデルを登録するときは、(トレーニングの実行から) クラウドの場所またはローカル ディレクトリのパスを指定します。 このパスは、登録プロセスの一部としてアップロードするファイルを見つけるためだけのものです。 エントリ スクリプトで使用されるパスと一致する必要はありません。 詳細については、「[エントリ スクリプトでモデル ファイルを検索する](#locate-model-files-in-your-entry-script)」を参照してください。

機械学習モデルをご使用の Azure Machine Learning ワークスペースに登録します。 これらのモデルは、Azure Machine Learning から取得することも、どこか他の場所から取得することもできます。 次の例では、モデルを登録する方法を示します。

### <a name="register-a-model-from-an-experiment-run"></a>実験の実行からモデルを登録する

このセクションのコード スニペットでは、トレーニング実行からモデルを登録する方法が示されています。

> [!IMPORTANT]
> これらのスニペットを使用するには、以前にトレーニング実行を行ったことがあり、`Run` オブジェクト (SDK の例) または実行 ID の値 (CLI の例) にアクセスできることが必要です。 トレーニング モデルの詳細については、[モデルのトレーニング用のコンピューティング先の設定](how-to-set-up-training-targets.md)に関する記事をご覧ください。

+ **SDK を使用する**

  SDK を使用してモデルをトレーニングする場合、モデルのトレーニング方法に応じて、[Run](https://review.docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py&branch=master) オブジェクトまたは [AutoMLRun](https://review.docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.run.automlrun?view=azure-ml-py&branch=master) オブジェクトのいずれかを受け取ることができます。 各オブジェクトは、実験の実行によって作成されたモデルを登録するために使用できます。

  + `azureml.core.Run` オブジェクトからモデルを登録する
 
    ```python
    model = run.register_model(model_name='sklearn_mnist', model_path='outputs/sklearn_mnist_model.pkl')
    print(model.name, model.id, model.version, sep='\t')
    ```

    `model_path` パラメーターは、クラウドでのモデルの場所を示します。 この例では、1 つのファイルへのパスが使用されます。 モデルの登録に複数のファイルを含めるには、`model_path` を、それらのファイルが含まれているフォルダーのパスに設定します。 詳細については、[Run.register_model](https://review.docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py&branch=master#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none----kwargs-) のドキュメントを参照してください。

  + `azureml.train.automl.run.AutoMLRun` オブジェクトからモデルを登録する

    ```python
        description = 'My AutoML Model'
        model = run.register_model(description = description)

        print(run.model_id)
    ```

    この例では、`metric` パラメーターと `iteration` パラメーターが指定されていません。これにより、最適なプライマリ メトリックを持つイテレーションが登録されます。 実行から返された `model_id` 値がモデル名の代わりに使用されます。

    詳細については、[AutoMLRun.register_model](https://review.docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.run.automlrun?view=azure-ml-py&branch=master#register-model-description-none--tags-none--iteration-none--metric-none-) のドキュメントを参照してください。

+ **CLI を使用する**

  ```azurecli-interactive
  az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --run-id myrunid
  ```

  [!INCLUDE [install extension](../../../includes/machine-learning-service-install-extension.md)]

  `--asset-path` パラメーターは、クラウドでのモデルの場所を示します。 この例では、1 つのファイルへのパスが使用されます。 モデルの登録に複数のファイルを含めるには、`--asset-path` を、それらのファイルが含まれているフォルダーのパスに設定します。

+ **VS コードを使用する**

  [VS コード](how-to-vscode-tools.md#deploy-and-manage-models)拡張機能を使用して、任意のモデル ファイルまたはフォルダーを使用してモデルを登録します。

### <a name="register-a-model-from-a-local-file"></a>ローカル ファイルからモデルを登録する

モデルのローカル パスを指定することで、モデルを登録できます。 フォルダーまたは 1 個のファイルのパスのいずれかを指定できます。 この方法を使用すると、Azure Machine Learning でトレーニングされてからダウンロードされたモデルを登録できます。 この方法を使用して、Azure Machine Learning の外部でトレーニングされたモデルを登録することもできます。

[!INCLUDE [trusted models](../../../includes/machine-learning-service-trusted-model.md)]

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

+ **CLI を使用する**

  ```azurecli-interactive
  az ml model register -n onnx_mnist -p mnist/model.onnx
  ```

  モデルの登録に複数のファイルを含めるには、`-p` を、それらのファイルが含まれているフォルダーのパスに設定します。

**推定所要時間**: 約 10 秒。

詳細については、[Model クラス](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py)のドキュメントを参照してください。

Azure Machine Learning 以外でトレーニングされたモデルの使用の詳細については、[既存のモデルをデプロイする方法](how-to-deploy-existing-model.md)に関する記事を参照してください。

<a name="target"></a>

## <a name="choose-a-compute-target"></a>コンピューティング ターゲットを選択する

次のコンピューティング ターゲット、またはコンピューティング リソースを使用して、Web サービスのデプロイをホストできます。

[!INCLUDE [aml-compute-target-deploy](../../../includes/aml-compute-target-deploy.md)]

## <a name="prepare-to-deploy"></a>デプロイを準備する

モデルをデプロイするには、次のものが必要です。

* **エントリ スクリプト**。 このスクリプトは、要求を受け入れ、モデルを使用してその要求にスコアを付け、その結果を返します。

    > [!IMPORTANT]
    > * エントリ スクリプトは、モデルに固有のものです。 このスクリプトでは、受信要求データの形式、モデルで想定されるデータの形式、およびクライアントに返されるデータの形式が認識されている必要があります。
    >
    >   要求データがモデルで使用できない形式である場合、スクリプトで受け入れ可能な形式に変換できます。 また、応答をクライアントに返す前に変換することもできます。
    >
    > * Azure Machine Learning SDK には、データストアまたはデータセットにアクセスするための Web サービスまたは IoT Edge のデプロイの手段は用意されていません。 デプロイしたモデルが、デプロイの外部に格納されているデータ (Azure Storage アカウントのデータなど) にアクセスする必要がある場合、関連する SDK を使用してカスタム コード ソリューションを開発する必要があります。 たとえば、[Azure Storage SDK for Python](https://github.com/Azure/azure-storage-python) です。
    >
    >   シナリオに適したもう 1 つの方法として[バッチ予測](how-to-run-batch-predictions.md)があります。これにより、スコアリング時にデータストアにアクセスできます。

* **依存関係**。エントリ スクリプトまたはモデルを実行するために必要なヘルパー スクリプトや Python/Conda パッケージなど。

* デプロイされたモデルをホストするコンピューティング先の**デプロイ構成**。 この構成には、モデルの実行に必要なメモリと CPU の要件などが記述されます。

これらの項目は、*推論構成*と*デプロイ構成*にカプセル化されます。 推論構成では、エントリ スクリプトとその他の依存関係が参照されます。 これらの構成は、SDK を使用してデプロイを実行するときにプログラムによって定義します。 これらは、CLI を使用するときに JSON ファイルで定義します。

### <a id="script"></a> 1.エントリ スクリプトと依存関係を定義する

エントリ スクリプトは、デプロイされた Web サービスに送信されたデータを受け取り、それをモデルに渡します。 次に、モデルから返された応答を受け取り、それをクライアントに返します。 *このスクリプトはこのモデルに固有のものです*。 そのため、モデルが受け入れ、返すデータを認識する必要があります。

このスクリプトにはモデルの読み込みと実行の 2 つの関数が含まれています。

* `init()`:通常は、この関数によってモデルがグローバル オブジェクトに読み込まれます。 この関数は、Web サービスの Docker コンテナーの起動時に 1 回だけ実行されます。

* `run(input_data)`:この関数では、モデルを使用して、入力データに基づいて値が予測されます。 実行の入力と出力は、通常、JSON を使用してシリアル化およびシリアル化解除が実行されます。 また、未加工のバイナリ データも使用できます。 モデルに送信する前、またはクライアントに返す前のデータを変換できます。

#### <a name="locate-model-files-in-your-entry-script"></a>エントリ スクリプトでモデル ファイルを検索する

エントリ スクリプトでモデルを検索するには、次の 2 つの方法があります。
* `AZUREML_MODEL_DIR`:モデルの場所へのパスを含む環境変数。
* `Model.get_model_path`:登録されているモデル名を使用してモデル ファイルへのパスを返す API。

##### <a name="azureml_model_dir"></a>AZUREML_MODEL_DIR

AZUREML_MODEL_DIR は、サービスのデプロイ中に作成される環境変数です。 この環境変数を使用して、デプロイされたモデルの場所を見つけることができます。

次の表では、デプロイされるモデルの数に応じて、AZUREML_MODEL_DIR の値について説明します。

| デプロイ | 環境変数の値 |
| ----- | ----- |
| 単一モデル | モデルを含むフォルダーへのパス。 |
| 複数のモデル | すべてのモデルを含むフォルダーへのパス。 モデルは、このフォルダー (`$MODEL_NAME/$VERSION`) で名前とバージョンによって検索されます |

モデル内のファイルへのパスを取得するには、環境変数を探しているファイル名と組み合わせます。
モデル ファイルのファイル名は、登録時とデプロイ時に保持されます。 

**単一モデルの例**
```python
model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_regression_model.pkl')
```

**複数のモデルの例**
```python
model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_model/1/sklearn_regression_model.pkl')
```

##### <a name="get_model_path"></a>get_model_path

モデルを登録するときに、レジストリ内のモデルを管理するために使用されるモデル名を指定します。 この名前は、ローカル ファイル システム上のモデル ファイルのパスを取得する [Model.get_model_path()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) メソッドで使用します。 フォルダーまたはファイルのコレクションを登録した場合、この API では、これらのファイルを含むディレクトリのパスが返されます。

モデルを登録する場合は名前を指定します。 この名前は、モデルがローカルにデプロイされるか、またはサービスのデプロイ時に配置されるときの場所に対応します。

> [!IMPORTANT]
> 自動機械学習を使用してモデルをトレーニングした場合、モデル名として `model_id` 値が使用されます。 自動機械学習でトレーニングされたモデルを登録してデプロイする例については、GitHub の「[Azure/MachineLearningNotebooks](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features)」を参照してください。

下の例では (`sklearn_mnist` という名前で登録された) `sklearn_mnist_model.pkl` という名前の単一のファイルのパスが返されます。

```python
model_path = Model.get_model_path('sklearn_mnist')
```

<a id="schema"></a>

#### <a name="optional-automatic-schema-generation"></a>(省略可能) スキーマの自動生成

Web サービスのスキーマを自動生成するには、定義された型オブジェクトのいずれかのコンストラクターに入力や出力のサンプルを指定します。 型とサンプルはスキーマを自動作成するために使用されます。 その後、Azure Machine Learning によって、デプロイ中に、Web サービスの [OpenAPI](https://swagger.io/docs/specification/about/) (Swagger) 仕様が作成されます。

現在サポートされている型は次のとおりです。

* `pandas`
* `numpy`
* `pyspark`
* 標準的な Python オブジェクト

スキーマ生成を使用するには、Conda 環境ファイルに `inference-schema` パッケージを追加します。 このパッケージの詳細については、[https://github.com/Azure/InferenceSchema](https://github.com/Azure/InferenceSchema) を参照してください。

##### <a name="example-dependencies-file"></a>依存関係ファイルの例

次の YAML は、推論のための Conda 依存関係ファイルの例です。

```YAML
name: project_environment
dependencies:
  - python=3.6.2
  - scikit-learn=0.20.0
  - pip:
    - azureml-defaults
    - inference-schema[numpy-support]
```

> [!IMPORTANT]
> 依存関係が Conda と pip (PyPi から) の両方で利用できる場合、Conda パッケージには通常、インストールの信頼性を高めるビルド済みのバイナリが付属しているため、Conda バージョンを使用することをお勧めします。
>
> 詳細については、「[Conda と Pip について](https://www.anaconda.com/understanding-conda-and-pip/)」を参照してください。
>
> 依存関係が Conda を介して使用可能かどうかを確認するには、`conda search <package-name>` コマンドを使用するか、[https://anaconda.org/anaconda/repo](https://anaconda.org/anaconda/repo) および [https://anaconda.org/conda-forge/repo](https://anaconda.org/conda-forge/repo) でパッケージ インデックスを使用します。

自動スキーマ生成を使用する場合、エントリ スクリプトで `inference-schema` パッケージをインポートする必要があります。

変数 `input_sample` と `output_sample` で入力と出力のサンプル形式を定義します。これは Web サービスの要求と応答の形式を表します。 これらのサンプルを、`run()` 関数の入力と出力の関数デコレーターで使用します。 下の scikit-learn の例では、スキーマ生成が使用されています。

##### <a name="example-entry-script"></a>エントリ スクリプトの例

次の例では、JSON データを受け取り、返す方法が示されています。

```python
#Example: scikit-learn and Swagger
import json
import numpy as np
import os
from sklearn.externals import joblib
from sklearn.linear_model import Ridge

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType


def init():
    global model
    # AZUREML_MODEL_DIR is an environment variable created during deployment. Join this path with the filename of the model file.
    # It holds the path to the directory that contains the deployed model (./azureml-models/$MODEL_NAME/$VERSION).
    # If there are multiple models, this value is the path to the directory containing all deployed models (./azureml-models).
    # Alternatively: model_path = Model.get_model_path('sklearn_mnist')
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    # Deserialize the model file back into a sklearn model
    model = joblib.load(model_path)


input_sample = np.array([[10, 9, 8, 7, 6, 5, 4, 3, 2, 1]])
output_sample = np.array([3726.995])


@input_schema('data', NumpyParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        result = model.predict(data)
        # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

次の例では、DataFrame を使用して、`<key: value>` ディクショナリとして入力データを定義する方法を示しています。 このメソッドは、Power BI からデプロイされた Web サービスを使用するためにサポートされています。 ([詳細については、Power BI から Web サービスを使用する方法に関するページを参照してください](https://docs.microsoft.com/power-bi/service-machine-learning-integration)。)

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
    # Replace filename if needed.
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'model_file.pkl')
    # Deserialize the model file back into a sklearn model.
    model = joblib.load(model_path)


input_sample = pd.DataFrame(data=[{
    # This is a decimal type sample. Use the data type that reflects this column in your data.
    "input_name_1": 5.1,
    # This is a string type sample. Use the data type that reflects this column in your data.
    "input_name_2": "value2",
    # This is an integer type sample. Use the data type that reflects this column in your data.
    "input_name_3": 3
}])

# This is an integer type sample. Use the data type that reflects the expected result.
output_sample = np.array([0])


@input_schema('data', PandasParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        result = model.predict(data)
        # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

他の例については、次のスクリプトをご覧ください。

* [PyTorch](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch)
* [TensorFlow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/tensorflow)
* [Keras](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras)
* [ONNX](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/)

<a id="binary"></a>

#### <a name="binary-data"></a>バイナリ データ

モデルがバイナリ データ (画像など) を受け入れる場合は、デプロイで使用される `score.py` ファイルを生の HTTP 要求を受け入れるように変更する必要があります。 生データを受け入れるには、エントリ スクリプトで `AMLRequest` クラスを使用して、`@rawhttp` デコレーターを `run()` 関数に追加します。

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
        # For this example, just return the URL for GETs.
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        # For a real-world solution, you would load the data from reqBody
        # and send it to the model. Then return the response.

        # For demonstration purposes, this example just returns the posted data as the response.
        return AMLResponse(reqBody, 200)
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> `AMLRequest` クラスは `azureml.contrib` 名前空間にあります。 この名前空間内のエンティティは、このサービスが改善されるに従って頻繁に変更されます。 この名前空間内のものはすべて、Microsoft によって完全にはサポートされていないプレビューとして見なす必要があります。
>
> これをローカルの開発環境でテストする必要がある場合は、次のコマンドを使用して、コンポーネントをインストールできます。
>
> ```shell
> pip install azureml-contrib-services
> ```

<a id="cors"></a>

#### <a name="cross-origin-resource-sharing-cors"></a>クロスオリジン リソース共有 (CORS)

クロスオリジン リソース共有を使用すると、Web ページ上のリソースを他のドメインから要求することができます。 CORS は、クライアント要求で送信され、サービス応答で返される HTTP ヘッダーを使用して機能します。 CORS と有効なヘッダーについて詳しくは、Wikipedia の[クロスオリジン リソース共有](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing)に関する説明を参照してください。

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
        # For this example, just return the URL for GETs.
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        # For a real-world solution, you would load the data from reqBody
        # and send it to the model. Then return the response.

        # For demonstration purposes, this example
        # adds a header and returns the request body.
        resp = AMLResponse(reqBody, 200)
        resp.headers['Access-Control-Allow-Origin'] = "http://www.example.com"
        return resp
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> `AMLResponse` クラスは `azureml.contrib` 名前空間にあります。 この名前空間内のエンティティは、このサービスが改善されるに従って頻繁に変更されます。 この名前空間内のものはすべて、Microsoft によって完全にはサポートされていないプレビューとして見なす必要があります。
>
> これをローカルの開発環境でテストする必要がある場合は、次のコマンドを使用して、コンポーネントをインストールできます。
>
> ```shell
> pip install azureml-contrib-services
> ```

### <a name="2-define-your-inferenceconfig"></a>2.InferenceConfig を定義する

推論構成では、予測するモデルを構成する方法が説明されます。 この構成は、エントリ スクリプトの一部ではありません。 これは、エントリ スクリプトを参照し、デプロイに必要なすべてのリソースを検索するために使用されます。 これは後でモデルをデプロイするときに使用されます。

推論構成は Azure Machine Learning 環境を使用して、デプロイに必要なソフトウェアの依存関係を定義できます。 環境では、トレーニングとデプロイに必要なソフトウェアの依存関係を作成、管理、再利用することができます。 次の例で、ワークスペースから環境を読み込み、推論構成でそれを使用する方法を示します。

```python
from azureml.core import Environment
from azureml.core.model import InferenceConfig

deploy_env = Environment.get(workspace=ws,name="myenv",version="1")
inference_config = InferenceConfig(entry_script="x/y/score.py",
                                   environment=deploy_env)
```

環境の詳細については、[トレーニングとデプロイのための環境の作成と管理](how-to-use-environments.md)に関する記事を参照してください。

環境を使用せずに、依存関係を直接指定することもできます。 次の例では、Conda ファイルからソフトウェアの依存関係を読み込む推論構成を作成する方法を示します。

```python
from azureml.core.model import InferenceConfig

inference_config = InferenceConfig(runtime="python",
                                   entry_script="x/y/score.py",
                                   conda_file="env/myenv.yml")
```

詳細については、[InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) クラスのドキュメントを参照してください。

推論構成を利用したカスタム Docker イメージの使用については、[カスタム Docker イメージを使用してモデルをデプロイする方法](how-to-deploy-custom-docker-image.md)に関するページを参照してください。

### <a name="cli-example-of-inferenceconfig"></a>InferenceConfig の CLI の例

[!INCLUDE [inference config](../../../includes/machine-learning-service-inference-config.md)]

次のコマンドでは、CLI を使用してモデルをデプロイする方法を示します。

```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json
```

この例では、構成で次の設定が指定されています。

* このモデルには Python が必要であるということ。
* [エントリ スクリプト](#script)。デプロイされたサービスに送信される Web 要求の処理に必要です。
* 推論を行うために必要な Python パッケージを記述する Conda ファイル。

推論構成を利用したカスタム Docker イメージの使用については、[カスタム Docker イメージを使用してモデルをデプロイする方法](how-to-deploy-custom-docker-image.md)に関するページを参照してください。

### <a name="3-define-your-deployment-configuration"></a>3.デプロイ構成を定義する

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

#### <a name="profiling"></a>プロファイリング

モデルをサービスとしてデプロイする前に、最適な CPU とメモリの要件を判断するためにプロファイリングを実行できます。 SDK または CLI を使用して、ご自分のモデルをプロファイリングできます。 次の例に、SDK を使用してモデルをプロファイリングする方法を示します。

> [!IMPORTANT]
> プロファイリングを使用する場合、指定した推論構成で Azure Machine Learning 環境を参照することはできません。 代わりに、`InferenceConfig` オブジェクトの `conda_file` パラメーターを使用して、ソフトウェアの依存関係を定義します。

```python
import json
test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10]
]})

profile = Model.profile(ws, "profilemymodel", [model], inference_config, test_data)
profile.wait_for_profiling(True)
profiling_results = profile.get_results()
print(profiling_results)
```

このコードでは、次の出力のような結果が表示されます。

```python
{'cpu': 1.0, 'memoryInGB': 0.5}
```

モデルのプロファイル結果は、`Run` オブジェクトとして出力されます。

CLI からプロファイリングを使用する方法については、「[az ml モデル プロファイル](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-profile)」を参照してください。

詳細については、次のドキュメントをご覧ください。

* [ModelProfile](https://docs.microsoft.com/python/api/azureml-core/azureml.core.profile.modelprofile?view=azure-ml-py)
* [profile()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#profile-workspace--profile-name--models--inference-config--input-data-)
* [推論構成ファイル スキーマ](reference-azure-machine-learning-cli.md#inference-configuration-schema)

## <a name="deploy-to-target"></a>ターゲットにデプロイする

デプロイでは、推論構成のデプロイ構成を使用して、モデルをデプロイします。 デプロイ プロセスは、コンピューティング ターゲットに関係なく類似しています。 AKS へのデプロイは、AKS クラスターへの参照を指定する必要があるため、少し異なります。

### <a name="securing-deployments-with-ssl"></a>SSL を使用したデプロイのセキュリティ保護

Web サービスのデプロイをセキュリティで保護する方法の詳細については、[SSL を使用した Web サービスのセキュリティ保護](how-to-secure-web-service.md#enable)に関する記事を参照してください。

### <a id="local"></a> ローカル デプロイ

モデルをローカルにデプロイするには、お使いのローカル コンピューターに Docker がインストールされている必要があります。

#### <a name="using-the-sdk"></a>SDK を使用する
zzs
```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

詳細については、[LocalWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py)、[Model.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)、および [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py) のドキュメントを参照してください。

#### <a name="using-the-cli"></a>CLI の使用

CLI を使用してモデルをデプロイするには、次のコマンドを使用します。 登録されているモデルの名前とバージョンに `mymodel:1` を置き換えます。

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
```

[!INCLUDE [aml-local-deploy-config](../../../includes/machine-learning-service-local-deploy-config.md)]

詳細については、[az ml model deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) のドキュメントを参照してください。

### <a id="notebookvm"></a> ノートブック VM Web サービス (開発/テスト)

[Azure Machine Learning ノートブック VM にモデルをデプロイする方法](how-to-deploy-local-container-notebook-vm.md)に関するページを参照してください。

### <a id="aci"></a> Azure Container Instances (開発/テスト)

[Azure Container Instances へのデプロイ](how-to-deploy-azure-container-instance.md)に関する記事を参照してください。

### <a id="aks"></a>Azure Kubernetes Service (開発/テストおよび運用)

[Azure Kubernetes Service へのデプロイ](how-to-deploy-azure-kubernetes-service.md)に関する記事を参照してください。

## <a name="consume-web-services"></a>Web サービスを使用する

デプロイされた Web サービスはすべて、REST API を提供します。そのため、さまざまなプログラミング言語でクライアント アプリケーションを作成できます。
サービスに対してキー認証を有効にしている場合、要求ヘッダーでトークンとしてサービス キーを指定する必要があります。
サービスに対してトークン認証を有効にしている場合、要求ヘッダーでベアラー トークンとして Azure Machine Learning JWT トークンを指定する必要があります。

> [!TIP]
> サービスをデプロイした後、スキーマ JSON ドキュメントを取得できます。 ローカル Web サービスの Swagger ファイルへの URI を取得するには、デプロイされた Web サービスの [swagger_uri プロパティ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri)を使用します (例: `service.swagger_uri`)。

### <a name="request-response-consumption"></a>要求 - 応答の使用量

Python でサービスを呼び出す方法の例を以下に示します。
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

デプロイで自動スキーマ生成を使用した場合は、[swagger_uri プロパティ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri)を使用して、サービスに対する OpenAPI 仕様のアドレスを取得できます。 (例: `print(service.swagger_uri)`)。仕様を取得するには、GET 要求を使用するか、ブラウザーで URI を開きます。

次の JSON ドキュメントは、デプロイに対して生成されるスキーマ (OpenAPI 仕様) の例です。

```json
{
    "swagger": "2.0",
    "info": {
        "title": "myservice",
        "description": "API specification for Azure Machine Learning myservice",
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

詳細については、[OpenAPI の仕様](https://swagger.io/specification/)をご覧ください。

仕様からクライアント ライブラリを作成できるユーティリティについては、[swagger-codegen](https://github.com/swagger-api/swagger-codegen) を参照してください。

### <a id="azuremlcompute"></a> バッチ推論
Azure Machine Learning のコンピューティング先は、Azure Machine Learning によって作成され、管理されます。 これは Azure Machine Learning パイプラインからのバッチ予測に使用できます。

Azure Machine Learning コンピューティングを使用したバッチ推論のチュートリアルについては、[バッチ予測を実行する方法](tutorial-pipeline-batch-scoring-classification.md)に関するページを参照してください。

### <a id="iotedge"></a> IoT Edge の推論
エッジにデプロイするためのサポートは現在、プレビューの段階にあります。 詳細については、[IoT Edge モジュールとしての Azure Machine Learning のデプロイ](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning)に関する記事を参照してください。


## <a id="update"></a> Web サービスを更新する

[!INCLUDE [aml-update-web-service](../../../includes/machine-learning-update-web-service.md)]

## <a name="continuously-deploy-models"></a>モデルを継続的にデプロイする

[Azure DevOps](https://azure.microsoft.com/services/devops/) 用の Machine Learning 拡張機能を使用して、モデルを継続的にデプロイできます。 Azure DevOps 用の Machine Learning 拡張機能を使用して、新しい機械学習モデルが Azure Machine Learning ワークスペースに登録されたときにデプロイ パイプラインをトリガーできます。

1. [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops) にサインアップします。そうすると、任意のプラットフォームや任意のクラウドへのご自分のアプリケーションの継続的インテグレーションとデリバリーを実現できます。 (Azure Pipelines は [Machine Learning パイプライン](concept-ml-pipelines.md#compare)とは異なることに注意してください)。

1. [Azure DevOps プロジェクトを作成します。](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

1. [Azure Pipelines 用の Machine Learning 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml&targetId=6756afbe-7032-4a36-9cb6-2771710cadc2&utm_source=vstsproduct&utm_medium=ExtHubManageList)をインストールします。

1. ご自分のすべての成果物にアクセスするために、サービス接続を使用して、ご自分の Azure Machine Learning ワークスペースへのサービス プリンシパル接続を設定します。 プロジェクト設定に移動し、 **[サービス接続]** を選択して、 **[Azure Resource Manager]** を選択します。

    [![Azure Resource Manager の選択](media/how-to-deploy-and-where/view-service-connection.png)](media/how-to-deploy-and-where/view-service-connection-expanded.png)

1. **[スコープ レベル]** の一覧で、 **[AzureMLWorkspace]** を選択し、残りの値を入力します。

    ![AzureMLWorkspace の選択](media/how-to-deploy-and-where/resource-manager-connection.png)

1. Azure Pipelines を使用してご自分の機械学習モデルを継続的にデプロイするために、パイプラインの下で **[リリース]** を選択します。 新しい成果物を追加し、 **[AzureML Model]** 成果物と前の手順で作成したサービス接続を選択します。 デプロイをトリガーするモデルとバージョンを選択します。

    [![AzureML モデルの選択](media/how-to-deploy-and-where/enable-modeltrigger-artifact.png)](media/how-to-deploy-and-where/enable-modeltrigger-artifact-expanded.png)

1. ご自分のモデル成果物に対してモデル トリガーを有効にします。 トリガーを有効にすると、そのモデルの指定したバージョン (最新バージョン) がワークスペースに登録されるたびに、Azure DevOps リリース パイプラインがトリガーされます。

    [![モデル トリガーを有効にする](media/how-to-deploy-and-where/set-modeltrigger.png)](media/how-to-deploy-and-where/set-modeltrigger-expanded.png)

他のサンプル プロジェクトと例については、GitHub の次のサンプル リポジトリを参照してください。

* [Microsoft/MLOps](https://github.com/Microsoft/MLOps)
* [Microsoft/MLOpsPython](https://github.com/microsoft/MLOpsPython)

## <a name="download-a-model"></a>モデルをダウンロード
モデルをダウンロードし、独自の実行環境でそれを使用する場合、次の SDK / CLI コマンドでそれを行うことができます。

SDK:
```python
model_path = Model(ws,'mymodel').download()
```

CLI:
```azurecli-interactive
az ml model download --model-id mymodel:1 --target-dir model_folder
```

## <a name="preview-no-code-model-deployment"></a>(プレビュー) コードなしのモデル デプロイ

コードなしのモデル デプロイは現在プレビュー段階で、次の機械学習フレームワークをサポートしています。

### <a name="tensorflow-savedmodel-format"></a>Tensorflow SavedModel 形式
コードなしのモデル デプロイを使用するには、TensorFlow モデルが **SavedModel 形式**で登録されている必要があります。

SavedModel の作成方法については、[このリンク](https://www.tensorflow.org/guide/saved_model)を参照してください。

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

### <a name="onnx-models"></a>ONNX モデル

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

### <a name="scikit-learn-models"></a>Scikit-learn モデル

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

注:事前構築済みの sklearn 推論コンテナーには次の依存関係が含まれています。

```yaml
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
```

## <a name="package-models"></a>モデルのパッケージ化

場合によっては、モデルをデプロイせずに Docker イメージを作成することが必要になることがあります (たとえば、[Azure App Service にデプロイする](how-to-deploy-app-service.md)予定がある場合)。 または、イメージをダウンロードして、ローカルの Docker インストールで実行することが必要な場合もあります。 さらに、イメージのビルドに使用するファイルをダウンロードし、それらを検査および変更して、手動でビルドすることが必要な場合もあります。

モデルのパッケージ化により、これらを行うことができます。 モデルを Web サービスとしてホストするために必要なすべてのアセットをパッケージ化し、完全にビルドされた Docker イメージ、またはイメージのビルドに必要なファイルのいずれかをダウンロードできます。 モデルのパッケージ化を使用するには、次の 2 つの方法があります。

**パッケージ化されたモデルをダウンロードする:** モデルと、それを Web サービスとしてホストするために必要なその他のファイルを含む Docker イメージをダウンロードします。

**Dockerfile を生成する:** Docker イメージをビルドするために必要な Dockerfile、モデル、エントリ スクリプト、およびその他のアセットをダウンロードします。 その後、イメージをローカルでビルドする前に、それらのファイルを検査するか、変更を加えることができます。

どちらのパッケージも、ローカルの Docker イメージを取得するために使用できます。

> [!TIP]
> パッケージの作成は、モデルのデプロイに似ています。 登録済みのモデルと推論構成を使用します。

> [!IMPORTANT]
> 完全にビルドされたイメージをダウンロードするか、ローカルでイメージをビルドするには、開発環境に [Docker](https://www.docker.com) をインストールしておく必要があります。

### <a name="download-a-packaged-model"></a>パッケージ化されたモデルをダウンロードする

次の例では、ワークスペースの Azure Container Registry に登録されているイメージをビルドします。

```python
package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
```

パッケージを作成した後、`package.pull()` を使用して、ローカルの Docker 環境にイメージをプルできます。 このコマンドの出力には、イメージの名前が表示されます。 例: 

`Status: Downloaded newer image for myworkspacef78fd10.azurecr.io/package:20190822181338` 

モデルをダウンロードした後、`docker images` コマンドを使用してローカル イメージを一覧表示します。

```text
REPOSITORY                               TAG                 IMAGE ID            CREATED             SIZE
myworkspacef78fd10.azurecr.io/package    20190822181338      7ff48015d5bd        4 minutes ago       1.43GB
```

このイメージに基づいてローカル コンテナーを開始するには、次のコマンドを使用して、シェルまたはコマンド ラインから名前付きコンテナーを開始します。 `<imageid>` 値を、`docker images` コマンドから返されたイメージ ID に置き換えます。

```bash
docker run -p 6789:5001 --name mycontainer <imageid>
```

このコマンドは、`myimage` という名前のイメージの最新バージョンを起動します。 これはローカル ポート 6789 を、Web サービスがリッスンしているコンテナー内のポート (5001) にマップします。 また、コンテナーに `mycontainer` という名前を割り当てます。これにより、コンテナーの停止が簡単になります。 コンテナーが開始したら、`http://localhost:6789/score` に要求を送信できます。

### <a name="generate-a-dockerfile-and-dependencies"></a>Dockerfile および依存関係を生成する

次の例では、イメージをローカルでビルドするために必要な Dockerfile、モデル、およびその他のアセットをダウンロードする方法を示します。 `generate_dockerfile=True` パラメーターは、完全にビルドされたイメージではなく、それらのファイルが必要であることを示します。

```python
package = Model.package(ws, [model], inference_config, generate_dockerfile=True)
package.wait_for_creation(show_output=True)
# Download the package.
package.save("./imagefiles")
# Get the Azure container registry that the model/Dockerfile uses.
acr=package.get_container_registry()
print("Address:", acr.address)
print("Username:", acr.username)
print("Password:", acr.password)
```

このコードは、イメージをビルドするために必要なファイルを `imagefiles` ディレクトリにダウンロードします。 保存ファイルに含まれている Dockerfile は、Azure Container Registry に格納されている基本イメージを参照します。 ローカルの Docker インストールでイメージをビルドする場合、アドレス、ユーザー名、およびパスワードを使用して、このレジストリに対して認証を行う必要があります。 ローカルの Docker インストールを使用してイメージをビルドするには、次の手順に従います。

1. シェルまたはコマンド ライン セッションから、次のコマンドを使用して Azure Container Registry で Docker を認証します。 `<address>`、`<username>`、および `<password>` を、`package.get_container_registry()` によって取得した値に置き換えます。

    ```bash
    docker login <address> -u <username> -p <password>
    ```

2. イメージをビルドするには、次のコマンドを使用します。 `<imagefiles>` を、`package.save()` でファイルを保存したディレクトリへのパスに置き換えます。

    ```bash
    docker build --tag myimage <imagefiles>
    ```

    このコマンドは、イメージ名を `myimage` に設定します。

イメージがビルドされたことを確認するには、`docker images` コマンドを使用します。 `myimage` イメージが一覧に表示されます。

```text
REPOSITORY      TAG                 IMAGE ID            CREATED             SIZE
<none>          <none>              2d5ee0bf3b3b        49 seconds ago      1.43GB
myimage         latest              739f22498d64        3 minutes ago       1.43GB
```

このイメージに基づいて新しいコンテナーを開始するには、次のコマンドを使用します。

```bash
docker run -p 6789:5001 --name mycontainer myimage:latest
```

このコマンドは、`myimage` という名前のイメージの最新バージョンを起動します。 これはローカル ポート 6789 を、Web サービスがリッスンしているコンテナー内のポート (5001) にマップします。 また、コンテナーに `mycontainer` という名前を割り当てます。これにより、コンテナーの停止が簡単になります。 コンテナーが開始したら、`http://localhost:6789/score` に要求を送信できます。

### <a name="example-client-to-test-the-local-container"></a>ローカル コンテナーをテストするクライアントの例

次のコードは、コンテナーで使用できる Python クライアントの例です。

```python
import requests
import json

# URL for the web service.
scoring_uri = 'http://localhost:6789/score'

# Two sets of data to score, so we get two results back.
data = {"data":
        [
            [ 1,2,3,4,5,6,7,8,9,10 ],
            [ 10,9,8,7,6,5,4,3,2,1 ]
        ]
        }
# Convert to JSON string.
input_data = json.dumps(data)

# Set the content type.
headers = {'Content-Type': 'application/json'}

# Make the request and display the response.
resp = requests.post(scoring_uri, input_data, headers=headers)
print(resp.text)
```

他のプログラミング言語でのクライアントの例については、[Web サービスとしてデプロイされたモデルの使用](how-to-consume-web-service.md)に関する記事を参照してください。

### <a name="stop-the-docker-container"></a>Docker コンテナーを停止する

コンテナーを停止するには、別のシェルまたはコマンド ラインから次のコマンドを使用します。

```bash
docker kill mycontainer
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

デプロイされた Web サービスを削除するには、`service.delete()` を使用します。
登録済みのモデルを削除するには、`model.delete()` を使用します。

詳細については、[WebService.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--) と [Model.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--) のドキュメントを参照してください。

## <a name="next-steps"></a>次の手順

* [カスタム Docker イメージを使用してモデルをデプロイする方法](how-to-deploy-custom-docker-image.md)
* [デプロイ トラブルシューティング](how-to-troubleshoot-deployment.md)
* [SSL を使用して Azure Machine Learning Web サービスをセキュリティで保護する](how-to-secure-web-service.md)
* [Web サービスとしてデプロイされた Azure Machine Learning モデルを使用する](how-to-consume-web-service.md)
* [Application Insights を使用して Azure Machine Learning のモデルを監視する](how-to-enable-app-insights.md)
* [実稼働環境でモデルのデータを収集する](how-to-enable-data-collection.md)

