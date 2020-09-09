---
title: Docker デプロイのトラブルシューティング
titleSuffix: Azure Machine Learning
description: Azure Machine Learning を使用する Azure Kubernetes Service と Azure Container Instances での一般的な Docker デプロイ エラーの回避、解決、またはトラブルシューティング方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: clauren42
ms.author: clauren
ms.reviewer: jmartens
ms.date: 08/06/2020
ms.topic: conceptual
ms.custom: troubleshooting, contperfq4, devx-track-python
ms.openlocfilehash: 3f8a3c705878e212e6a26670e20b5a81a3f2a6ba
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87904379"
---
# <a name="troubleshoot-docker-deployment-of-models-with-azure-kubernetes-service-and-azure-container-instances"></a>Azure Kubernetes Service と Azure Container Instances を使用したモデルの Docker デプロイのトラブルシューティング 

Azure Machine Learning を使用する Azure Container Instances (ACI) と Azure Kubernetes Service (AKS) での一般的な Docker デプロイ エラーをトラブルシューティング、解決、または回避する方法について説明します。

## <a name="prerequisites"></a>前提条件

* **Azure サブスクリプション**。 お持ちでない場合は、[無料版または有料版の Azure Machine Learning](https://aka.ms/AMLFree) をお試しください。
* [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)。
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。
* [Azure Machine Learning 用 CLI 拡張機能](reference-azure-machine-learning-cli.md)。
* ローカルでデバッグするには、ローカル システム上に機能する Docker のインストールが必要です。

    Docker のインストールを確認するには、ターミナルまたはコマンド プロンプトからコマンド `docker run hello-world` を使用します。 Docker のインストール、または Docker のエラーのトラブルシューティングについては、[Docker のドキュメント](https://docs.docker.com/)を参照してください。

## <a name="steps-for-docker-deployment-of-machine-learning-models"></a>機械学習モデルの Docker デプロイの手順

Azure Machine Learning にモデルをデプロイすると、システムによって多数のタスクが実行されます。

モデル デプロイでは、[環境](how-to-use-environments.md)オブジェクトを入力パラメーターとして使用して、[Model.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) API を経由する方法が推奨されます。 この場合、サービスによってデプロイ段階で基本的な Docker イメージが作成され、必要なモデルがすべて 1 回の呼び出しでマウントされます。 基本的なデプロイ タスクは次のとおりです。

1. ワークスペース モデル レジストリにモデルを登録します。

2. 推論構成を定義する:
    1. 環境 yaml ファイルで指定した依存関係に基づいて、[環境](how-to-use-environments.md)オブジェクトを作成するか、調達された環境のいずれかを使用します。
    2. 環境とスコアリング スクリプトに基づいて、推論構成 (InferenceConfig オブジェクト) を作成します。

3. モデルを Azure コンテナー インスタンス (ACI) サービスまたは Azure Kubernetes Service (AKS) にデプロイします。

このプロセスの詳細は[モデル管理](concept-model-management-and-deployment.md)の概要にあります。

## <a name="before-you-begin"></a>開始する前に

問題に直面したら、最初に行うべきことは、(前述の) デプロイ タスクを個々の手順に分割し、問題を隔離することです。

[環境](how-to-use-environments.md)オブジェクトを入力パラメーターとして指定し、[Model.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) API 経由で新しいまたは推奨されるデプロイ方法を使用していると仮定した場合、コードは次の 3 つの主な手順に分けることができます。

1. モデルを登録します。 サンプル コードをいくつか以下に示します。

    ```python
    from azureml.core.model import Model


    # register a model out of a run record
    model = best_run.register_model(model_name='my_best_model', model_path='outputs/my_model.pkl')

    # or, you can register a file or a folder of files as a model
    model = Model.register(model_path='my_model.pkl', model_name='my_best_model', workspace=ws)
    ```

2. デプロイの推論構成を定義する:

    ```python
    from azureml.core.model import InferenceConfig
    from azureml.core.environment import Environment


    # create inference configuration based on the requirements defined in the YAML
    myenv = Environment.from_conda_specification(name="myenv", file_path="myenv.yml")
    inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
    ```

3. 前の手順で作成された推論構成を使用して、モデルをデプロイします。

    ```python
    from azureml.core.webservice import AciWebservice


    # deploy the model
    aci_config = AciWebservice.deploy_configuration(cpu_cores=1, memory_gb=1)
    aci_service = Model.deploy(workspace=ws,
                           name='my-service',
                           models=[model],
                           inference_config=inference_config,
                           deployment_config=aci_config)
    aci_service.wait_for_deployment(show_output=True)
    ```

デプロイ プロセスを個別タスクに分割できたところで、最も一般的なエラーをいくつか確認してみましょう。

## <a name="debug-locally"></a>ローカル デバッグ

モデルを ACI または AKS にデプロイする際に問題が発生した場合は、ローカル Web サービスとしてデプロイしてみてください。 ローカル Web サービスを使用すると、問題のトラブルシューティングが簡単になります。 モデルを含む Docker イメージがダウンロードされ、ローカル システムで起動されます。

[MachineLearningNotebooks](https://github.com/Azure/MachineLearningNotebooks) リポジトリにあるサンプル[ローカル展開ノートブック](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/deploy-to-local/register-model-deploy-local.ipynb)から、実行可能な例を探索します。

> [!WARNING]
> ローカル Web サービスのデプロイは、運用シナリオではサポートされていません。

ローカルにデプロイするには、`LocalWebservice.deploy_configuration()` を使用してデプロイ構成を作成するようにコードを変更します。 次に、`Model.deploy()` を使用して、サービスをデプロイします。 次の例では、モデル (モデル変数に含まれる) をローカル Web サービスとしてデプロイします。

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig, Model
from azureml.core.webservice import LocalWebservice


# Create inference configuration based on the environment definition and the entry script
myenv = Environment.from_conda_specification(name="env", file_path="myenv.yml")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
# Create a local deployment, using port 8890 for the web service endpoint
deployment_config = LocalWebservice.deploy_configuration(port=8890)
# Deploy the service
service = Model.deploy(
    ws, "mymodel", [model], inference_config, deployment_config)
# Wait for the deployment to complete
service.wait_for_deployment(True)
# Display the port that the web service is available on
print(service.port)
```

独自の conda 仕様 YAML を定義する場合、pip の依存関係として 1.0.45 以降のバージョンの azureml-defaults を列挙する必要があります。 このパッケージには、Web サービスとしてモデルをホストするために必要な機能が含まれています。

この時点で、通常どおりにサービスを操作できます。 たとえば、次のコードは、サービスにデータを送信する方法を示しています。

```python
import json

test_sample = json.dumps({'data': [
    [1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
    [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
]})

test_sample = bytes(test_sample, encoding='utf8')

prediction = service.run(input_data=test_sample)
print(prediction)
```

Python 環境のカスタマイズの詳細については、[トレーニングとデプロイのための環境の作成と管理](how-to-use-environments.md)に関するページを参照してください。 

### <a name="update-the-service"></a>サービスの更新

ローカル テスト中に、ログ記録を追加したり、発見した問題の解決を試みるために、`score.py` ファイルを更新する必要がある場合があります。 変更を `score.py` ファイルに再度読み込むには、`reload()` を使用します。 たとえば、次のコードは、サービスのスクリプトを再度読み込み、サービスにデータを送信します。 データは、更新された `score.py` ファイルを使用してスコア付けされます。

> [!IMPORTANT]
> `reload` メソッドは、ローカル デプロイでのみ使用できます。 デプロイを別のコンピューティング先に更新する方法については、[Web サービスを更新する方法](how-to-deploy-update-web-service.md)に関する記事を参照してください。

```python
service.reload()
print(service.run(input_data=test_sample))
```

> [!NOTE]
> スクリプトは、サービスによって使用される `InferenceConfig` オブジェクトによって指定された場所から再度読み込まれます。

モデル、Conda の依存関係、またはデプロイ構成を変更するには、[update()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#update--args-) を使用します。 次の例では、サービスで使用されるモデルを更新します。

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>サービスの削除

サービスを削除するには、[delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#delete--) を使用します。

### <a name="inspect-the-docker-log"></a><a id="dockerlog"></a> Docker ログの確認

サービス オブジェクトから詳細な Docker エンジン ログ メッセージを出力できます。 ACI、AKS、およびローカル デプロイのログを表示できます。 次の例は、ログを出力する方法を示しています。

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```
ログに `Booting worker with pid: <pid>` の行が何度も表示されている場合、これはワーカーを起動するのに十分なメモリがないことを意味します。
このエラーを解決するには、`deployment_config` にある `memory_gb` の値を増やします
 
## <a name="container-cannot-be-scheduled"></a>コンテナーをスケジュールできない

Azure Kubernetes Service コンピューティング ターゲットにサービスをデプロイするときに、Azure Machine Learning では、要求された量のリソースを使用してサービスをスケジュールすることを試みます。 5 分後、利用可能な適切な量のリソースがある利用可能なノードがクラスターにない場合、"`Couldn't Schedule because the kubernetes cluster didn't have available resources after trying for 00:05:00`" というメッセージが表示されてデプロイは失敗します。 このエラーに対処するには、ノードを追加するか、ノードの SKU を変更するか、またはサービスのリソース要件を変更します。 

通常、このエラー メッセージでは、追加する必要があるリソースが示されます。たとえば、"`0/3 nodes are available: 3 Insufficient nvidia.com/gpu`" というエラー メッセージが表示された場合、これは、サービスに GPU が必要であり、クラスターの 3 つのノードには利用可能な GPU がないことを意味します。 これに対処するには、ノードを追加するか (GPU SKU を使用している場合)、GPU 対応の SKU に切り替えるか (SKU が GPU 対応でない場合)、GPU を必要としないように環境を変更します。  

## <a name="service-launch-fails"></a>サービスを起動できない

イメージが正常にビルドされると、デプロイ構成を使用して、コンテナーの起動が試行されます。 コンテナーの起動プロセスの一部として、スコアリング スクリプトの `init()` 関数が呼び出されます。 `init()` 関数でキャッチされない例外がある場合、エラー メッセージに **CrashLoopBackOff** エラーが表示されることがあります。

「[Docker ログの確認](#dockerlog)」セクションの情報を使用して、ログを確認します。

## <a name="function-fails-get_model_path"></a>get_model_path() 関数が失敗する

多くの場合、スコアリング スクリプトの `init()` 関数では、コンテナー内のモデル ファイルまたはモデル ファイルのフォルダーを見つける目的で [Model.get_model_path()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) 関数が呼び出されます。 モデル ファイルまたはフォルダーが見つからない場合、この関数は失敗します。 このエラーをデバッグする最も簡単な方法は、Container シェルで以下の Python コードを実行することです。

```python
from azureml.core.model import Model
import logging
logging.basicConfig(level=logging.DEBUG)
print(Model.get_model_path(model_name='my-best-model'))
```

この例では、スコアリング スクリプトによってモデル ファイルまたはフォルダーの存在が予期されるコンテナー内のローカル パス (`/var/azureml-app` の相対パス) が出力されます。 その後、ファイルまたはフォルダーが予想される場所にあるかどうかを確認できます。

ログ レベルを DEBUG に設定すると、追加情報が記録される場合があり、エラーの特定に利用できる可能性があります。

## <a name="function-fails-runinput_data"></a>run(input_data) 関数が失敗する

サービスが正常にデプロイされたが、スコアリング エンドポイントにデータを投稿するとクラッシュする場合、代わりに詳細なエラー メッセージが返されるように `run(input_data)` 関数にエラーをキャッチするステートメントを追加できます。 次に例を示します。

```python
def run(input_data):
    try:
        data = json.loads(input_data)['data']
        data = np.array(data)
        result = model.predict(data)
        return json.dumps({"result": result.tolist()})
    except Exception as e:
        result = str(e)
        # return error message back to the client
        return json.dumps({"error": result})
```

**注**:`run(input_data)` 呼び出しからエラー メッセージを返すことは、デバッグ目的のみで行ってください。 セキュリティ上の理由から、運用環境ではこの方法でエラー メッセージを返さないでください。

## <a name="http-status-code-502"></a>HTTP 状態コード 502

502 状態コードは、サービスが例外をスローしたか、score.py ファイルの `run()` メソッドでクラッシュしたことを示します。 この記事の情報を使用して、ファイルをデバッグします。

## <a name="http-status-code-503"></a>HTTP 状態コード 503

Azure Kubernetes Service のデプロイでは、自動スケールがサポートされているため、レプリカを加えて、追加の負荷に対応することができます。 しかし、自動スケールは、**段階的な**負荷の変化に対処するように設計されています。 1 秒あたりに受信する要求の量が急増した場合、クライアントは HTTP 状態コード 503 を受信する可能性があります。

状態コード 503 を防ぐには、次の 2 つのことが役立ちます。

* 自動スケールによって新しいレプリカが作成される使用率レベルを変更します。
    
    既定では、自動スケールの目標使用率は 70% に設定されています。これは、1 秒あたりに受信する要求の量 (RPS) が最大 30% 増加した場合まで対処できることを意味します。 使用率の目標を調整するには、`autoscale_target_utilization` をより小さい値に設定します。

    > [!IMPORTANT]
    > この変更によって、レプリカの作成時間は*短縮*されません。 その代わり、より低い使用率しきい値で作成されます。 サービスの使用率が 70% になるまで待機するのでなく値を 30% に変更すると、使用率が 30% になった段階でレプリカが作成されます。
    
    現在の最大数のレプリカが Web サービスによって既に使用されていて、状態コード 503 が引き続き発生する場合は、`autoscale_max_replicas` の値を大きくして、レプリカの最大個数を増やします。

* レプリカの最小個数を変更します。 レプリカの最小個数を増やすと、着信トラフィックの急増に対処するためのプールが大きくなります。

    レプリカの最小個数を増やすには、`autoscale_min_replicas` をより大きな値に設定します。 必要なレプリカ個数は次のコードを使用して計算できるので、値をご利用のプロジェクトに固有の値に置換します。

    ```python
    from math import ceil
    # target requests per second
    targetRps = 20
    # time to process the request (in seconds)
    reqTime = 10
    # Maximum requests per container
    maxReqPerContainer = 1
    # target_utilization. 70% in this example
    targetUtilization = .7

    concurrentRequests = targetRps * reqTime / targetUtilization

    # Number of container replicas
    replicas = ceil(concurrentRequests / maxReqPerContainer)
    ```

    > [!NOTE]
    > 受信する要求の量が、新しい最小レプリカ数で対処できるレベルを超えて急増した場合、再び 503 が発生する可能性があります。 たとえば、ご利用のサービスへのトラフィックが増えた場合、レプリカの最小個数を増やすことが必要な場合があります。

`autoscale_target_utilization`、`autoscale_max_replicas`、`autoscale_min_replicas` の設定方法の詳細については、[AksWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py) モジュール リファレンスを参照してください。

## <a name="http-status-code-504"></a>HTTP 状態コード 504

504 状態コードは、要求がタイムアウトしたことを示します。既定のタイムアウトは 1 分です。

タイムアウト値を増やすか、score.py を変更して不要な呼び出しを削除することで、サービスの高速化を試みることができます。 これらのアクションで問題が解決しない場合は、この記事の情報を使用して score.py ファイルをデバッグします。 コードが応答なし状態または無限ループになっている可能性があります。

## <a name="advanced-debugging"></a>高度なデバッグ

場合によっては、モデル デプロイに含まれる Python コードを対話的にデバッグする必要が生じることがあります。 たとえば、エントリ スクリプトが失敗し、追加のログ記録によっても理由を特定できない場合がこれにあたります。 Visual Studio Code と debugpy を使用すると、Docker コンテナー内で実行されているコードにアタッチできます。 詳細については、[VS Code での対話型デバッグのガイド](how-to-debug-visual-studio-code.md#debug-and-troubleshoot-deployments)を参照してください。

## <a name="next-steps"></a>次のステップ

デプロイの詳細については、以下を参照してください。

* [デプロイする方法とその場所](how-to-deploy-and-where.md)
* [チュートリアル:モデルのトレーニングとデプロイ](tutorial-train-models-with-aml.md)
