---
title: 既存のモデルを使用してデプロイする
titleSuffix: Azure Machine Learning
description: Azure Machine Learning を利用し、Azure の外部でトレーニングした ML モデルを Azure クラウドに持ち込む方法について説明します。 その後、Web サービスまたは IoT モジュールとしてモデルをデプロイします。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 07/17/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, deploy
ms.openlocfilehash: 8076e3f6b2198abe46ae49cd8e0fd2b02f000231
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102519352"
---
# <a name="deploy-your-existing-model-with-azure-machine-learning"></a>Azure Machine Learning を使用して既存のモデルをデプロイする


この記事では、Azure Machine Learning の外部でトレーニングした機械学習モデルを登録してデプロイする方法について説明します。 Web サービスとしてデプロイしたり、IoT Edge デバイスにデプロイしたりできます。  デプロイ後、Azure Machine Learning でモデルを監視したり、データ ドリフトを検出したりできます。 

この記事にある概念と用語の詳細については、[機械学習モデルの管理、デプロイ、および監視](concept-model-management-and-deployment.md)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

* [Azure Machine Learning ワークスペース](how-to-manage-workspace.md)
  + Python の例では、`ws` 変数がご利用の Azure Machine Learning のワークスペースに設定されていることを前提としています。 ワークスペースに接続する方法の詳細については、[Azure Machine Learning SDK for Python のドキュメント](/python/api/overview/azure/ml/#workspace)を参照してください。
  
  + CLI の例では、`myworkspace` と `myresourcegroup` のプレースホルダーが使用されますが、これはお使いのワークスペースの名前とそのワークスペースが含まれるリソース グループに置換する必要があります。

* [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/install)。  

* [Azure CLI](/cli/azure/install-azure-cli) と [Machine Learning CLI 拡張機能](reference-azure-machine-learning-cli.md)。

* トレーニング済みのモデル。 モデルは、開発環境上の 1 つ以上のファイルに保存する必要があります。 <br><br>トレーニングされたモデルの登録例を示すため、この記事のサンプル コードでは、[Paolo Ripamonti の Twitter 感情分析プロジェクト](https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis)からのモデルが使用されます。

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

詳細については、[Model.register()](/python/api/azureml-core/azureml.core.model%28class%29#register-workspace--model-path--model-name--tags-none--properties-none--description-none--datasets-none--model-framework-none--model-framework-version-none--child-paths-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none-) のリファレンスを参照してください。

```azurecli
az ml model register -p ./models -n sentiment -w myworkspace -g myresourcegroup
```

> [!TIP]
> また、登録されているモデルに、`tags` および `properties` ディクショナリ オブジェクトを設定することもできます。 これらの値は、後で特定のモデルを識別するために使用できます。 たとえば、使用されているフレームワーク、トレーニング パラメーターなどです。

詳細については、[az ml model register](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-register) のリファレンスを参照してください。


一般的なモデル登録の詳細については、「[モデルを管理、デプロイ、および監視する](concept-model-management-and-deployment.md)」を参照してください。

## <a name="define-inference-configuration"></a>推論構成を定義する

推論構成には、デプロイされたモデルの実行に使用される環境を定義します。 推論構成からは、デプロイ時にモデルの実行に使用される次のエンティティが参照されます。

* `score.py` という名前のエントリ スクリプトでは、デプロイされたサービスの開始時にモデルが読み込まれます。 このスクリプトは、データを受信し、それをモデルに渡してから応答を返す処理も担当します。
* Azure Machine Learning [環境](how-to-use-environments.md)。 環境には、モデルとエントリ スクリプトの実行に必要なソフトウェアの依存関係を定義します。

次の例で、SDK を使用して環境を作成し、推論構成でそれを使用する方法を示します。

```python
from azureml.core.model import InferenceConfig
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

# Create the environment
myenv = Environment(name="myenv")
conda_dep = CondaDependencies()

# Define the packages needed by the model and scripts
conda_dep.add_conda_package("tensorflow")
conda_dep.add_conda_package("numpy")
conda_dep.add_conda_package("scikit-learn")
# You must list azureml-defaults as a pip dependency
conda_dep.add_pip_package("azureml-defaults")
conda_dep.add_pip_package("keras")
conda_dep.add_pip_package("gensim")

# Adds dependencies to PythonSection of myenv
myenv.python.conda_dependencies=conda_dep

inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
```

詳細については、次の記事を参照してください。

+ [環境の使用方法](how-to-use-environments.md)。
+ [InferenceConfig](/python/api/azureml-core/azureml.core.model.inferenceconfig) のリファレンス。


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
    - gensim
```

推論構成の詳細については、「[Azure Machine Learning を使用してモデルをデプロイする](how-to-deploy-and-where.md)」を参照してください。

### <a name="entry-script-scorepy"></a>エントリ スクリプト (score.py)

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

[Webservice](/python/api/azureml-core/azureml.core.webservice) パッケージには、デプロイに使用されるクラスが含まれています。 使用するクラスによって、モデルのデプロイ場所が決まります。 たとえば、Azure Kubernetes Service に Web サービスとしてデプロイするには、[AksWebService.deploy_configuration()](/python/api/azureml-core/azureml.core.webservice.akswebservice#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none--compute-target-name-none-) を使用してデプロイ構成を作成します。

次の Python コードでは、ローカル デプロイ用のデプロイ構成を定義します。 この構成では、モデルを Web サービスとしてローカル コンピューターに展開します。

> [!IMPORTANT]
> ローカル デプロイでは、ローカル コンピューターに [Docker](https://www.docker.com/) が正常にインストールされている必要があります。

```python
from azureml.core.webservice import LocalWebservice

deployment_config = LocalWebservice.deploy_configuration()
```

詳細については、[LocalWebservice.deploy_configuration()](/python/api/azureml-core/azureml.core.webservice.localwebservice#deploy-configuration-port-none-) のリファレンスを参照してください。

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

詳細については、[Model.deploy()](/python/api/azureml-core/azureml.core.model.model#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) のリファレンスを参照してください。

CLI からモデルをデプロイするには、次のコマンドを使用します。 このコマンドによって、`inferenceConfig.json` および `deploymentConfig.json` ファイルに格納されている推論およびデプロイ構成が使用され、登録モデルのバージョン 1 (`sentiment:1`) がデプロイされます。

```azurecli
az ml model deploy -n myservice -m sentiment:1 --ic inferenceConfig.json --dc deploymentConfig.json
```

詳細については、[az ml model deploy](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-deploy) のリファレンスを参照してください。

デプロイの詳細については、「[モデルをデプロイする方法と場所](how-to-deploy-and-where.md)」を参照してください。

## <a name="request-response-consumption"></a>要求 - 応答の使用量

デプロイ後は、スコアリング URI が表示されます。 この URI は、クライアントからサービスに要求を送信するときに使用できます。 次の例は、サービスにデータを送信して応答を表示する簡単な Python クライアントです。

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
* [デプロイ済みモデルのクライアントを作成する方法](how-to-consume-web-service.md)