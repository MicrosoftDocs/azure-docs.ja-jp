---
title: 既存のモデルを使用してデプロイする
titleSuffix: Azure Machine Learning
description: サービスの外部でトレーニングされたモデルと共に Azure Machine Learning を使用する方法について説明します。 Azure Machine Learning の外部で作成されたモデルを登録してから、それらを Web サービスまたは Azure IoT Edge モジュールとしてデプロイできます。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 06/19/2019
ms.openlocfilehash: a864ec8c9bbdf90f04c98c8d9656c863fb32b653
ms.sourcegitcommit: a7a9d7f366adab2cfca13c8d9cbcf5b40d57e63a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/20/2019
ms.locfileid: "71162472"
---
# <a name="use-an-existing-model-with-azure-machine-learning"></a>Azure Machine Learning で既存のモデルを使用する

Azure Machine Learning で既存の機械学習モデルを使用する方法について説明します。

Azure Machine Learning の外部でトレーニングされた機械学習モデルがある場合でも、そのサービスを使用してモデルを Web サービスまたは IoT Edge デバイスとしてデプロイできます。 

> [!TIP]
> この記事では、既存モデルの登録とデプロイに関する基本情報を提供します。 デプロイされると、Azure Machine Learning によってご利用のモデルを監視できます。 また、デプロイに送信された入力データを保存し、データ ドリフト分析やモデルの新しいバージョンのトレーニングに使用できます。
>
> ここで使用されている概念と用語の詳細については、[機械学習モデルの管理、デプロイ、および監視](concept-model-management-and-deployment.md)に関するページを参照してください。
>
> デプロイ プロセスの一般的な情報については、「[Azure Machine Learning を使用してモデルをデプロイする](how-to-deploy-and-where.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

* Azure Machine Learning ワークスペース。 詳細については、「[ワークスペースの作成](how-to-manage-workspace.md)」を参照してください。

    > [!TIP]
    > この記事の Python の例では、`ws` 変数がご利用の Azure Machine Learning のワークスペースに設定されていることを前提としています。
    >
    > CLI の例では、`myworkspace` と `myresourcegroup` のプレースホルダーを使用しています。 これらは、ワークスペースと、それを含むリソース グループの名前に置き換えてください。

* [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)。  

* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) と [Machine Learning CLI 拡張機能](reference-azure-machine-learning-cli.md)。

* トレーニング済みのモデル。 モデルは、開発環境上の 1 つ以上のファイルに保存する必要があります。

    > [!NOTE]
    > Azure Machine Learning の外部でトレーニングされたモデルの登録例を示すために、この記事のサンプル コード スニペットでは、Paolo Ripamonti の Twitter センチメント分析プロジェクトで作成されたモデル [https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis](https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis) を使用します。

## <a name="register-the-models"></a>モデルを登録する

モデルを登録すると、ワークスペース内のモデルに関するメタデータを保存、バージョン管理、および追跡できます。 次の Python および CLI の例では、`models` ディレクトリに `model.h5`、`model.w2v`、`encoder.pkl`、および `tokenizer.pkl` ファイルが含まれています。 この例では、`models` ディレクトリに含まれているファイルを `sentiment` という名前の新しいモデル登録としてアップロードします。

```python
from azureml.core.model import Model
# Tip: When model_path is set to a directory, you can use the child_paths parameter to include
#      only some of the files from the directory
model = Model.register(model_path = "./models",
                       model_name = "sentiment",
                       description = "Sentiment analysis model trained outside Azure Machine Learning",
                       workspace = ws)
```

詳細については、[Model.register()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model(class)?view=azure-ml-py#register-workspace--model-path--model-name--tags-none--properties-none--description-none--datasets-none--model-framework-none--model-framework-version-none--child-paths-none-) のリファレンスを参照してください。

```azurecli
az ml model register -p ./models -n sentiment -w myworkspace -g myresourcegroup
```

詳細については、[az ml model register](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-register) のリファレンスを参照してください。


一般的なモデル登録の詳細については、「[モデルを管理、デプロイ、および監視する](concept-model-management-and-deployment.md)」を参照してください。


## <a name="define-inference-configuration"></a>推論構成を定義する

推論構成には、デプロイされたモデルの実行に使用される環境を定義します。 推論構成からは、デプロイ時にモデルの実行に使用される次のエンティティが参照されます。

* エントリ スクリプト。 このファイル (`score.py` という名前) では、デプロイされたサービスの開始時にモデルが読み込まれます。 また、データを受信し、それをモデルに渡してから応答を返す処理も担当します。
* Azure Machine Learning [環境](how-to-use-environments.md)。 環境には、モデルとエントリ スクリプトの実行に必要なソフトウェアの依存関係を定義します。

次の例で、SDK を使用して環境を作成し、推論構成でそれを使用する方法を示します。

```python
from azureml.core.model import InferenceConfig
from azureml.core import Environment
from azureml.core.environment import CondaDependencies

# Create the environment
myenv = Environment(name="myenv")
conda_dep = CondaDependencies()

# Define the packages needed by the model and scripts
conda_dep.add_conda_package("tensorflow")
conda_dep.add_conda_package("numpy")
conda_dep.add_conda_package("scikit-learn")
conda_dep.add_pip_package("keras")

# Adds dependencies to PythonSection of myenv
myenv.python.conda_dependencies=conda_dep

inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
```

詳細については、次の記事を参照してください。

+ [環境の使用方法](how-to-use-environments.md)。
+ [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) のリファレンス。


この CLI では YAML ファイルから推論構成が読み込まれます。

```yaml
{
   "entryScript": "score.py",
   "runtime": "python",
   "condaFile": "myenv.yml"
}
```

CLI では、conda 環境は、推論構成から参照される `myenv.yml` ファイルに定義されます。 次の YAML は、このファイルの内容です。

```yaml
name: inference_environment
dependencies:
- python=3.6.2
- tensorflow
- numpy
- scikit-learn
- pip:
    - azureml-defaults
    - keras
```

推論構成の詳細については、「[Azure Machine Learning を使用してモデルをデプロイする](how-to-deploy-and-where.md)」を参照してください。

### <a name="entry-script"></a>エントリ スクリプト。

エントリ スクリプトには、`init()` と `run(data)` ていう 2 つの必要な関数しかありません。 これらの関数は、起動時にサービスを初期化し、クライアントから渡された要求データを使用してモデルを実行するために使用されます。 スクリプトの残りの部分では、モデルの読み込みと実行が処理されます。

> [!IMPORTANT]
> すべてのモデルで機能する汎用のエントリ スクリプトはありません。 常に使用されるモデルに固有のものです。 モデルを読み込む方法、モデルが期待するデータ形式、およびモデルを使用してデータをスコア付けする方法が認識されている必要があります。

次の Python コードはエントリ スクリプトの例です (`score.py`)。

```python
import os
import pickle
import json
import time
from keras.models import load_model
from keras.preprocessing.sequence import pad_sequences
from gensim.models.word2vec import Word2Vec

# SENTIMENT
POSITIVE = "POSITIVE"
NEGATIVE = "NEGATIVE"
NEUTRAL = "NEUTRAL"
SENTIMENT_THRESHOLDS = (0.4, 0.7)
SEQUENCE_LENGTH = 300

# Called when the deployed service starts
def init():
    global model
    global tokenizer
    global encoder
    global w2v_model

    # Get the path where the deployed model can be found.
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), './models')
    # load models
    model = load_model(model_path + '/model.h5')
    w2v_model = Word2Vec.load(model_path + '/model.w2v')

    with open(model_path + '/tokenizer.pkl','rb') as handle:
        tokenizer = pickle.load(handle)

    with open(model_path + '/encoder.pkl','rb') as handle:
        encoder = pickle.load(handle)

# Handle requests to the service
def run(data):
    try:
        # Pick out the text property of the JSON request.
        # This expects a request in the form of {"text": "some text to score for sentiment"}
        data = json.loads(data)
        prediction = predict(data['text'])
        #Return prediction
        return prediction
    except Exception as e:
        error = str(e)
        return error

# Determine sentiment from score
def decode_sentiment(score, include_neutral=True):
    if include_neutral:
        label = NEUTRAL
        if score <= SENTIMENT_THRESHOLDS[0]:
            label = NEGATIVE
        elif score >= SENTIMENT_THRESHOLDS[1]:
            label = POSITIVE
        return label
    else:
        return NEGATIVE if score < 0.5 else POSITIVE

# Predict sentiment using the model
def predict(text, include_neutral=True):
    start_at = time.time()
    # Tokenize text
    x_test = pad_sequences(tokenizer.texts_to_sequences([text]), maxlen=SEQUENCE_LENGTH)
    # Predict
    score = model.predict([x_test])[0]
    # Decode sentiment
    label = decode_sentiment(score, include_neutral=include_neutral)

    return {"label": label, "score": float(score),
       "elapsed_time": time.time()-start_at}  
```

エントリ スクリプトの詳細については、「[Azure Machine Learning を使用してモデルをデプロイする](how-to-deploy-and-where.md)」を参照してください。

## <a name="define-deployment"></a>デプロイを定義する

[Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice?view=azure-ml-py) パッケージには、デプロイに使用されるクラスが含まれています。 使用するクラスによって、モデルのデプロイ場所が決まります。 たとえば、Azure Kubernetes Service に Web サービスとしてデプロイするには、[AksWebService.deploy_configuration()](/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none-) を使用してデプロイ構成を作成します。

次の Python コードでは、ローカル デプロイ用のデプロイ構成を定義します。 この構成では、モデルを Web サービスとしてローカル コンピューターに展開します。

> [!IMPORTANT]
> ローカル デプロイでは、ローカル コンピューターに [Docker](https://www.docker.com/) が正常にインストールされている必要があります。

```python
from azureml.core.webservice import LocalWebservice

deployment_config = LocalWebservice.deploy_configuration()
```

詳細については、[LocalWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.localwebservice?view=azure-ml-py#deploy-configuration-port-none-) のリファレンスを参照してください。

この CLI では YAML ファイルからデプロイ構成が読み込まれます。

```YAML
{
    "computeType": "LOCAL"
}
```

Azure クラウドの Azure Kubernetes Service など、別のコンピューティング先へのデプロイは、デプロイ構成の変更と同じくらい簡単です。 詳細については、「[モデルをデプロイする方法と場所](how-to-deploy-and-where.md)」を参照してください。

## <a name="deploy-the-model"></a>モデルをデプロイする

次の例では、`sentiment` という名前の登録モデルに関する情報を読み込み、それを `sentiment` という名前のサービスとしてデプロイします。 デプロイ時に、推論構成およびデプロイ構成を使用してサービス環境を作成し、構成します。

```python
from azureml.core.model import Model

model = Model(ws, name='sentiment')
service = Model.deploy(ws, 'myservice', [model], inference_config, deployment_config)

service.wait_for_deployment(True)
print(service.state)
print("scoring URI: " + service.scoring_uri)
```

詳細については、[Model.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config--deployment-config-none--deployment-target-none-) のリファレンスを参照してください。

CLI からモデルをデプロイするには、次のコマンドを使用します。 このコマンドによって、`inferenceConfig.json` および `deploymentConfig.json` ファイルに格納されている推論およびデプロイ構成が使用され、登録モデルのバージョン 1 (`sentiment:1`) がデプロイされます。

```azurecli
az ml model deploy -n myservice -m sentiment:1 --ic inferenceConfig.json --dc deploymentConfig.json
```

詳細については、[az ml model deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) のリファレンスを参照してください。

デプロイの詳細については、「[モデルをデプロイする方法と場所](how-to-deploy-and-where.md)」を参照してください。

## <a name="request-response-consumption"></a>要求 - 応答の使用量

デプロイ後は、スコアリング URI が表示されます。 この URI は、クライアントからサービスに要求を送信するときに使用できます。 次の例は、サービスにデータを送信して応答を表示する基本的な Python クライアントです。

```python
import requests
import json

scoring_uri = 'scoring uri for your service'
headers = {'Content-Type':'application/json'}

test_data = json.dumps({'text': 'Today is a great day!'})

response = requests.post(scoring_uri, data=test_data, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

デプロイされたサービスを消費する方法については、[クライアントの作成](how-to-consume-web-service.md)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

* [Application Insights を使用して Azure Machine Learning のモデルを監視する](how-to-enable-app-insights.md)
* [実稼働環境でモデルのデータを収集する](how-to-enable-data-collection.md)
* [モデルをデプロイする方法と場所](how-to-deploy-and-where.md)
* [デプロイ済みモデルのクライアントを作成する方法](how-to-consume-web-service.md)
