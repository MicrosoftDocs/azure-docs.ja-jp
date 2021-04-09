---
title: Cognitive Search で使用するモデルをデプロイする
titleSuffix: Azure Machine Learning
description: Azure Machine Learning を使用して、Cognitive Search で使用するモデルをデプロイする方法について説明します。 モデルは、検索エクスペリエンスを強化するためのカスタム スキルとして使用されます。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: cgronlun
author: cjgronlund
ms.reviewer: larryfr
ms.date: 03/11/2021
ms.custom: deploy
ms.openlocfilehash: 22c8880cbcde1f1a55fa66beee0323e0348e1164
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103149615"
---
# <a name="deploy-a-model-for-use-with-cognitive-search"></a>Cognitive Search で使用するモデルをデプロイする


この記事では、[Azure Cognitive Search](../search/search-what-is-azure-search.md) で使用するモデルを、Azure Machine Learning を使用してデプロイする方法について説明します。

Cognitive Search では、異種コンテンツを人やアプリケーションが照会できるようにするために、コンテンツ処理が実行されます。 このプロセスは、Azure Machine Learning からデプロイされたモデルを使用して拡張することができます。

Azure Machine Learning は、トレーニング済みのモデルを Web サービスとしてデプロイすることができます。 その Web サービスは、Cognitive Search の "_スキル_" に埋め込まれて、処理パイプラインに追加されます。

> [!IMPORTANT]
> この記事の情報は、モデルのデプロイに限定されます。 そのモデルを Cognitive Search で使用するためのデプロイ構成のサポートについて取り上げています。
>
> デプロイされたモデルを使用するための Cognitive Search の構成方法については、「[Azure Machine Learning を使用してカスタム スキルを作成およびデプロイする](../search/cognitive-search-tutorial-aml-custom-skill.md)」のチュートリアルを参照してください。
>
> チュートリアルの基になっているサンプルについては、[https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill) を参照してください。

Azure Cognitive Search で使用するモデルをデプロイするときは、次の要件を満たす必要があります。

* 推論に使用するモデルは、Azure Kubernetes Service を使用してホストする。
* Azure Kubernetes Service のトランスポート層セキュリティ (TLS) を有効にする。 Cognitive Search とデプロイされたモデルとの間の HTTPS 通信には、TLS を使用してセキュリティが確保されます。
* エントリ スクリプトは、`inference_schema` パッケージを使用してサービスの OpenAPI (Swagger) スキーマを生成する。
* また、そのエントリ スクリプトは、JSON データを入力として受け取り、出力として JSON を生成する。


## <a name="prerequisites"></a>前提条件

* Azure Machine Learning ワークスペース。 詳細については、[Azure Machine Learning ワークスペースの作成](how-to-manage-workspace.md)に関するページをご覧ください。

* Azure Machine Learning SDK がインストールされた Python 開発環境。 詳細については、[Azure Machine Learning Web サービス](/python/api/overview/azure/ml/install)に関するページを参照してください。  

* 登録済みのモデル。 モデルがない場合は、[https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill) にあるサンプル ノートブックを使用してください。

* [モデルをデプロイする方法と場所](how-to-deploy-and-where.md)についての一般的な理解。

## <a name="connect-to-your-workspace"></a>ワークスペースに接続する

Azure Machine Learning ワークスペースでは、Azure Machine Learning を使用するときに作成する、すべての成果物を操作するための一元的な場所が提供されます。 ワークスペースには、スクリプトのログ、メトリック、出力、スナップショットなど、すべてのトレーニング実行の履歴が保持されます。

既存のワークスペースに接続するには、次のコードを使用します。

> [!IMPORTANT]
> このコード スニペットでは、ワークスペースの構成が現在のディレクトリまたはその親に保存されていることを想定しています。 詳細については、[Azure Machine Learning ワークスペースの作成と管理](how-to-manage-workspace.md)に関するページを参照してください。 構成をファイルに保存する方法について詳しくは、「[ワークスペース構成ファイルを作成する](how-to-configure-environment.md#workspace)」を参照してください。

```python
from azureml.core import Workspace

try:
    # Load the workspace configuration from local cached inffo
    ws = Workspace.from_config()
    print(ws.name, ws.location, ws.resource_group, ws.location, sep='\t')
    print('Library configuration succeeded')
except:
    print('Workspace not found')
```

## <a name="create-a-kubernetes-cluster"></a>Kubernetes クラスターを作成する

**推定所要時間**: 約 20 分です。

Kubernetes クラスターは、コンテナー化されたアプリケーションの実行に使用される一連の仮想マシン インスタンス ("ノード" と呼ばれる) です。

Azure Machine Learning から Azure Kubernetes Service にモデルをデプロイすると、モデルとそれを Web サービスとしてホストするために必要なすべての資産とが 1 つの Docker コンテナーにパッケージ化されます。 その後、このコンテナーがクラスターにデプロイされます。

次のコードは、ワークスペース用に新しい Azure Kubernetes Service (AKS) クラスターを作成する方法を示しています。

> [!TIP]
> 既存の Azure Kubernetes Service クラスターを Azure Machine Learning のワークスペースにアタッチすることもできます。 詳細については、「[Azure Kubernetes Service にモデルをデプロイする方法](how-to-deploy-azure-kubernetes-service.md)」に関するページを参照してください。

> [!IMPORTANT]
> このコードは、`enable_ssl()` メソッドを使用して、クラスターのトランスポート層セキュリティ (TLS) を有効にしている点に注目してください。 デプロイしたモデルを Cognitive Services から使用する予定がある場合、これは必須です。

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Create or attach to an AKS inferencing cluster

# Create the provisioning configuration with defaults
prov_config = AksCompute.provisioning_configuration()

# Enable TLS (sometimes called SSL) communications
# Leaf domain label generates a name using the formula
#  "<leaf-domain-label>######.<azure-region>.cloudapp.azure.com"
#  where "######" is a random series of characters
prov_config.enable_ssl(leaf_domain_label = "contoso")

cluster_name = 'amlskills'
# Try to use an existing compute target by that name.
# If one doesn't exist, create one.
try:
    
    aks_target = ComputeTarget(ws, cluster_name)
    print("Attaching to existing cluster")
except Exception as e:
    print("Creating new cluster")
    aks_target = ComputeTarget.create(workspace = ws, 
                                  name = cluster_name, 
                                  provisioning_configuration = prov_config)
    # Wait for the create process to complete
    aks_target.wait_for_completion(show_output = True)
```

> [!IMPORTANT]
> Azure では、AKS クラスターが存在する限り、課金が行われます。 使い終わったら、必ず自分の AKS クラスターを削除してください。

AKS と Azure Machine Learning の使用の詳細については、[Azure Kubernetes Service にデプロイする方法](how-to-deploy-azure-kubernetes-service.md)に関するページを参照してください。

## <a name="write-the-entry-script"></a>エントリ スクリプトを記述する

エントリ スクリプトは、Web サービスに送信されたデータを受け取り、それをモデルに渡して、スコア付けの結果を返します。 次のスクリプトでは、起動時にモデルを読み込み、モデルを使用してデータをスコア付けします。 このファイルは、`score.py` とも呼ばれます。

> [!TIP]
> エントリ スクリプトは、モデルに固有のものです。 たとえば、スクリプトは、モデルで使用するフレームワークやデータ形式などを認識している必要があります。

> [!IMPORTANT]
> デプロイしたモデルを Azure Cognitive Services から使用する予定がある場合は、`inference_schema` パッケージを使用して、デプロイのスキーマ生成を有効にする必要があります。 このパッケージにはデコレーターが用意されており、モデルを使用して推論を実行する Web サービスの入力データ形式と出力データ形式は、このデコレーターを使用して定義することができます。

```python
from azureml.core.model import Model
from nlp_architect.models.absa.inference.inference import SentimentInference
from spacy.cli.download import download as spacy_download
import traceback
import json
# Inference schema for schema discovery
from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType
from inference_schema.parameter_types.standard_py_parameter_type import StandardPythonParameterType

def init():
    """
    Set up the ABSA model for Inference  
    """
    global SentInference
    spacy_download('en')
    aspect_lex = Model.get_model_path('hotel_aspect_lex')
    opinion_lex = Model.get_model_path('hotel_opinion_lex') 
    SentInference = SentimentInference(aspect_lex, opinion_lex)

# Use inference schema decorators and sample input/output to
# build the OpenAPI (Swagger) schema for the deployment
standard_sample_input = {'text': 'a sample input record containing some text' }
standard_sample_output = {"sentiment": {"sentence": "This place makes false booking prices, when you get there, they say they do not have the reservation for that day.", 
                                        "terms": [{"text": "hotels", "type": "AS", "polarity": "POS", "score": 1.0, "start": 300, "len": 6}, 
                                                  {"text": "nice", "type": "OP", "polarity": "POS", "score": 1.0, "start": 295, "len": 4}]}}
@input_schema('raw_data', StandardPythonParameterType(standard_sample_input))
@output_schema(StandardPythonParameterType(standard_sample_output))    
def run(raw_data):
    try:
        # Get the value of the 'text' field from the JSON input and perform inference
        input_txt = raw_data["text"]
        doc = SentInference.run(doc=input_txt)
        if doc is None:
            return None
        sentences = doc._sentences
        result = {"sentence": doc._doc_text}
        terms = []
        for sentence in sentences:
            for event in sentence._events:
                for x in event:
                    term = {"text": x._text, "type":x._type.value, "polarity": x._polarity.value, "score": x._score,"start": x._start,"len": x._len }
                    terms.append(term)
        result["terms"] = terms
        print("Success!")
        # Return the results to the client as a JSON document
        return {"sentiment": result}
    except Exception as e:
        result = str(e)
        # return error message back to the client
        print("Failure!")
        print(traceback.format_exc())
        return json.dumps({"error": result, "tb": traceback.format_exc()})
```

エントリ スクリプトの詳細については、[デプロイする方法と場所](how-to-deploy-and-where.md)に関するページをご覧ください。

## <a name="define-the-software-environment"></a>ソフトウェア環境を定義する

この環境クラスは、サービスの Python 依存関係を定義する目的で使用されます。 ここには、モデルとエントリ スクリプトの両方に必要な依存関係が含まれます。 この例では、通常の pypi インデックスと GitHub リポジトリからパッケージをインストールします。 

```python
from azureml.core.conda_dependencies import CondaDependencies 
from azureml.core import Environment

conda = None
pip = ["azureml-defaults", "azureml-monitoring", 
       "git+https://github.com/NervanaSystems/nlp-architect.git@absa", 'nlp-architect', 'inference-schema',
       "spacy==2.0.18"]

conda_deps = CondaDependencies.create(conda_packages=None, pip_packages=pip)

myenv = Environment(name='myenv')
myenv.python.conda_dependencies = conda_deps
```

環境の詳細については、[トレーニングとデプロイのための環境の作成と管理](how-to-use-environments.md)に関する記事を参照してください。

## <a name="define-the-deployment-configuration"></a>デプロイ構成を定義する

デプロイ構成では、Web サービスの実行に使用する Azure Kubernetes Service ホスト環境を定義します。

> [!TIP]
> デプロイに必要なメモリ、CPU、GPU がよくわからない場合は、プロファイリングを使用してそれらを調べることができます。 詳細については、[モデルをデプロイする方法と場所](how-to-deploy-and-where.md)に関するページを参照してください。

```python
from azureml.core.model import Model
from azureml.core.webservice import Webservice
from azureml.core.image import ContainerImage
from azureml.core.webservice import AksWebservice, Webservice

# If deploying to a cluster configured for dev/test, ensure that it was created with enough
# cores and memory to handle this deployment configuration. Note that memory is also used by
# things such as dependencies and AML components.

aks_config = AksWebservice.deploy_configuration(autoscale_enabled=True, 
                                                       autoscale_min_replicas=1, 
                                                       autoscale_max_replicas=3, 
                                                       autoscale_refresh_seconds=10, 
                                                       autoscale_target_utilization=70,
                                                       auth_enabled=True, 
                                                       cpu_cores=1, memory_gb=2, 
                                                       scoring_timeout_ms=5000, 
                                                       replica_max_concurrent_requests=2, 
                                                       max_request_wait_time=5000)
```

詳細については、リファレンス ドキュメントで [AksService.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.akswebservice#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none--compute-target-name-none-) に関するページをご覧ください。

## <a name="define-the-inference-configuration"></a>推論構成を定義する

推論構成は、エントリ スクリプトと環境オブジェクトをポイントします。

```python
from azureml.core.model import InferenceConfig
inf_config = InferenceConfig(entry_script='score.py', environment=myenv)
```

詳細については、リファレンス ドキュメントで [InferenceConfig](/python/api/azureml-core/azureml.core.model.inferenceconfig) に関するページをご覧ください。

## <a name="deploy-the-model"></a>モデルをデプロイする

モデルを自分の AKS クラスターにデプロイし、それによってサービスが作成されるのを待ちます。 この例では、登録済みの 2 つのモデルがレジストリから読み込まれて AKS にデプロイされます。 デプロイ後、そこに含まれる `score.py` ファイルが、それらのモデルを読み込んで推論を実行します。

```python
from azureml.core.webservice import AksWebservice, Webservice

c_aspect_lex = Model(ws, 'hotel_aspect_lex')
c_opinion_lex = Model(ws, 'hotel_opinion_lex') 
service_name = "hotel-absa-v2"

aks_service = Model.deploy(workspace=ws,
                           name=service_name,
                           models=[c_aspect_lex, c_opinion_lex],
                           inference_config=inf_config,
                           deployment_config=aks_config,
                           deployment_target=aks_target,
                           overwrite=True)

aks_service.wait_for_deployment(show_output = True)
print(aks_service.state)
```

詳細については、リファレンス ドキュメントで [Model](/python/api/azureml-core/azureml.core.model.model) に関するページをご覧ください。

## <a name="issue-a-sample-query-to-your-service"></a>サービスにサンプル クエリを発行する

次の例では、前のコード セクションで `aks_service` 変数に格納されたデプロイ情報を使用しています。 この変数を使用して、サービスとの通信に必要な認証トークンとスコアリングの URL を取得します。

```python
import requests
import json

primary, secondary = aks_service.get_keys()

# Test data
input_data = '{"raw_data": {"text": "This is a nice place for a relaxing evening out with friends. The owners seem pretty nice, too. I have been there a few times including last night. Recommend."}}'

# Since authentication was enabled for the deployment, set the authorization header.
headers = {'Content-Type':'application/json',  'Authorization':('Bearer '+ primary)} 

# Send the request and display the results
resp = requests.post(aks_service.scoring_uri, input_data, headers=headers)
print(resp.text)
```

サービスから返される結果は、次のような JSON になっています。

```json
{"sentiment": {"sentence": "This is a nice place for a relaxing evening out with friends. The owners seem pretty nice, too. I have been there a few times including last night. Recommend.", "terms": [{"text": "place", "type": "AS", "polarity": "POS", "score": 1.0, "start": 15, "len": 5}, {"text": "nice", "type": "OP", "polarity": "POS", "score": 1.0, "start": 10, "len": 4}]}}
```

## <a name="connect-to-cognitive-search"></a>Cognitive Search に接続する

このモデルを Cognitive Search から使用する方法については、「[Azure Machine Learning を使用してカスタム スキルを作成およびデプロイする](../search/cognitive-search-tutorial-aml-custom-skill.md)」のチュートリアルを参照してください。

## <a name="clean-up-the-resources"></a>リソースのクリーンアップ

この例専用に AKS クラスターを作成した場合は、Cognitive Search でテストした後にリソースを削除してください。

> [!IMPORTANT]
> Azure では、AKS クラスターがデプロイされている時間に基づいて課金が行われます。 これが完了した後は必ずクリーンアップしてください。

```python
aks_service.delete()
aks_target.delete()
```

## <a name="next-steps"></a>次のステップ

* [Azure Machine Learning を使用してカスタム スキルを作成およびデプロイする](../search/cognitive-search-tutorial-aml-custom-skill.md)