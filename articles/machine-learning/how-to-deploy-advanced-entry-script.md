---
title: 高度なシナリオのためのエントリ スクリプトを作成する
titleSuffix: Azure Machine Learning entry script authoring
author: gvashishtha
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 07/31/2020
ms.author: gopalv
ms.openlocfilehash: 0499cd6885454604e89ce4cadc313b2f68c45156
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2020
ms.locfileid: "87544506"
---
# <a name="advanced-entry-script-authoring"></a>高度なエントリ スクリプトの作成

この記事では、特殊なユース ケースのエントリ スクリプトを記述する方法について説明します。

## <a name="prerequisites"></a>前提条件

この記事では、Azure Machine Learning を使用してデプロイする、トレーニングされた機械学習モデルが既にあることを前提としています。 モデル デプロイの詳細については、[こちらのチュートリアル](how-to-deploy-and-where.md)を参照してください。

## <a name="automatically-generate-a-swagger-schema"></a>Swagger スキーマを自動生成する

Web サービスのスキーマを自動生成するには、定義された型オブジェクトのいずれかのコンストラクターに入力や出力のサンプルを指定します。 型とサンプルはスキーマを自動作成するために使用されます。 その後、Azure Machine Learning によって、デプロイ中に、Web サービスの [OpenAPI](https://swagger.io/docs/specification/about/) (Swagger) 仕様が作成されます。

現在サポートされている型は次のとおりです。

* `pandas`
* `numpy`
* `pyspark`
* 標準的な Python オブジェクト

スキーマ生成を使用するには、オープンソースの `inference-schema` パッケージを依存関係ファイルに含めます。 このパッケージの詳細については、[https://github.com/Azure/InferenceSchema](https://github.com/Azure/InferenceSchema) を参照してください。 変数 `input_sample` と `output_sample` で入力と出力のサンプル形式を定義します。これは Web サービスの要求と応答の形式を表します。 これらのサンプルを、`run()` 関数の入力と出力の関数デコレーターで使用します。 下の scikit-learn の例では、スキーマ生成が使用されています。


## <a name="power-bi-compatible-endpoint"></a>Power BI 互換エンドポイント 

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

# To indicate that we support a variable length of data input,
# set enforce_shape=False
@input_schema('data', PandasParameterType(input_sample, enforce_shape=False))
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


## <a name="binary-ie-image-data"></a><a id="binary-data"></a> バイナリ (例: 画像) データ

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

`AMLRequest` クラスからは、score.py の未加工の投稿データにのみアクセスできます。クライアント側コンポーネントはありません。 クライアントからは、通常どおりにデータを投稿します。 たとえば、次の Python コードを使用すると、イメージ ファイルを読み取り、データを投稿することができます。

```python
import requests
# Load image data
data = open('example.jpg', 'rb').read()
# Post raw data to scoring URI
res = requests.post(url='<scoring-uri>', data=data, headers={'Content-Type': 'application/octet-stream'})
```

<a id="cors"></a>

## <a name="cross-origin-resource-sharing-cors"></a>クロスオリジン リソース共有 (CORS)

クロスオリジン リソース共有を使用すると、Web ページ上のリソースを他のドメインから要求することができます。 CORS は、クライアント要求で送信され、サービス応答で返される HTTP ヘッダーを使用して機能します。 CORS と有効なヘッダーについて詳しくは、Wikipedia の[クロスオリジン リソース共有](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing)に関する説明を参照してください。

CORS をサポートするようにモデル デプロイを構成するには、エントリ スクリプトで `AMLResponse` クラスを使用します。 このクラスを使用すると、応答オブジェクトにヘッダーを設定できます。

次の例では、エントリ スクリプトから応答に対して `Access-Control-Allow-Origin` ヘッダーを設定しています。

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


> [!WARNING]
> Azure Machine Learning では、スコア付けサービスを実行しているコンテナーに POST 要求と GET 要求のみがルーティングされます。 これにより、ブラウザーではプレフライト CORS 要求に対して OPTIONS 要求が使用されるため、エラーが発生する可能性があります。
> 


## <a name="load-registered-models"></a>登録済みモデルの読み込み

エントリ スクリプトでモデルを検索するには、次の 2 つの方法があります。
* `AZUREML_MODEL_DIR`:モデルの場所へのパスを含む環境変数。
* `Model.get_model_path`:登録されているモデル名を使用してモデル ファイルへのパスを返す API。

#### <a name="azureml_model_dir"></a>AZUREML_MODEL_DIR

AZUREML_MODEL_DIR は、サービスのデプロイ中に作成される環境変数です。 この環境変数を使用して、デプロイされたモデルの場所を見つけることができます。

次の表では、デプロイされるモデルの数に応じて、AZUREML_MODEL_DIR の値について説明します。

| デプロイ | 環境変数の値 |
| ----- | ----- |
| 単一モデル | モデルを含むフォルダーへのパス。 |
| 複数のモデル | すべてのモデルを含むフォルダーへのパス。 モデルは、このフォルダー (`$MODEL_NAME/$VERSION`) で名前とバージョンによって検索されます |

モデルの登録とデプロイ中、モデルは AZUREML_MODEL_DIR パスに配置され、元のファイル名が保持されます。

エントリ スクリプト内のモデル ファイルへのパスを取得するには、環境変数と探しているファイル名を組み合わせます。

**単一モデルの例**
```python
# Example when the model is a file
model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_regression_model.pkl')

# Example when the model is a folder containing a file
file_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'my_model_folder', 'sklearn_regression_model.pkl')
```

**複数のモデルの例**

このシナリオでは、2 つのモデルがワークスペースに登録されます。

* `my_first_model`:1 つのファイル (`my_first_model.pkl`) が含まれており、バージョンは 1 つ (`1`) のみです。
* `my_second_model`:1 つのファイル (`my_second_model.pkl`) が含まれており、2 つのバージョン `1` と `2` があります。

サービスがデプロイされた場合、両方のモデルがデプロイ操作で提供されます。

```python
first_model = Model(ws, name="my_first_model", version=1)
second_model = Model(ws, name="my_second_model", version=2)
service = Model.deploy(ws, "myservice", [first_model, second_model], inference_config, deployment_config)
```

サービスをホストする Docker イメージでは、`AZUREML_MODEL_DIR` 環境変数に、モデルが配置されているディレクトリが格納されます。
このディレクトリで、各モデルは `MODEL_NAME/VERSION` のディレクトリ パスに配置されます。 ここで、`MODEL_NAME` は登録されているモデルの名前で、`VERSION` はモデルのバージョンです。 登録されたモデルを構成するファイルは、これらのディレクトリに格納されます。

この例で、パスは `$AZUREML_MODEL_DIR/my_first_model/1/my_first_model.pkl` と `$AZUREML_MODEL_DIR/my_second_model/2/my_second_model.pkl` です。


```python
# Example when the model is a file, and the deployment contains multiple models
first_model_name = 'my_first_model'
first_model_version = '1'
first_model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), first_model_name, first_model_version, 'my_first_model.pkl')
second_model_name = 'my_second_model'
second_model_version = '2'
second_model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), second_model_name, second_model_version, 'my_second_model.pkl')
```

### <a name="get_model_path"></a>get_model_path

モデルを登録するときに、レジストリ内のモデルを管理するために使用されるモデル名を指定します。 この名前は、ローカル ファイル システム上のモデル ファイルのパスを取得する [Model.get_model_path()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) メソッドで使用します。 フォルダーまたはファイルのコレクションを登録した場合、この API では、これらのファイルを含むディレクトリのパスが返されます。

モデルを登録する場合は名前を指定します。 この名前は、モデルがローカルにデプロイされるか、またはサービスのデプロイ時に配置されるときの場所に対応します。

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
