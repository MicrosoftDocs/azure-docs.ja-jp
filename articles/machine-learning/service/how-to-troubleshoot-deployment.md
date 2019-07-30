---
title: デプロイ トラブルシューティング ガイド
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning service を使用する Azure Kubernetes Service と Azure Container Instances での一般的な Docker デプロイ エラーの回避、解決、またはトラブルシューティング方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: chris-lauren
ms.author: clauren
ms.reviewer: jmartens
ms.date: 07/09/2019
ms.custom: seodec18
ms.openlocfilehash: 24716a9b9fa5174d899cf0678b83b2da0c59957c
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68358675"
---
# <a name="troubleshooting-azure-machine-learning-service-azure-kubernetes-service-and-azure-container-instances-deployment"></a>Azure Machine Learning service の Azure Kubernetes Service および Azure Container Instances デプロイのトラブルシューティング

Azure Machine Learning service を使用する Azure Container Instances (ACI) と Azure Kubernetes Service (AKS) での一般的な Docker デプロイ エラーの回避方法または解決方法について説明します。

Azure Machine Learning サービスでモデルをデプロイするとき、さまざまなタスクが実行されます。 デプロイ タスクの内容:

1. ワークスペース モデル レジストリにモデルを登録します。

2. Docker イメージをビルドします。これには次のタスクが含まれます。
    1. 登録済みのモデルをレジストリからダウンロードします。 
    2. 環境 yaml ファイルに指定した依存関係に基づき、Python 環境を利用し、Dockerfile を作成します。
    3. Dockerfile で指定するモデル ファイルとスコアリング スクリプトを追加します。
    4. Dockerfile を使用して新しい Docker イメージをビルドします。
    5. ワークスペースに関連付けられている Azure Container Registry に Docker イメージを登録します。

    > [!IMPORTANT]
    > コードによっては、入力なしでイメージの作成が自動的に行われます。

3. Docker イメージを Azure Container Instance (ACI) サービスまたは Azure Kubernetes Service (AKS) にデプロイします。

4. ACI または AKS で新しいコンテナーを起動します。 

このプロセスの詳細は[モデル管理](concept-model-management-and-deployment.md)の概要にあります。

## <a name="before-you-begin"></a>開始する前に

問題に直面したら、最初に行うべきことは、(前述の) デプロイ タスクを個々の手順に分割し、問題を隔離することです。

デプロイを複数のタスクに分割すると、[Webservice.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#deploy-workspace--name--model-paths--image-config--deployment-config-none--deployment-target-none-) API または [Webservice.deploy_from_model()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#deploy-from-model-workspace--name--models--image-config--deployment-config-none--deployment-target-none-) API を使用している場合に役立ちます。どちらの関数も、前述の手順を単一のアクションとして実行するためです。 一般的にこれらの API は便利ですが、以下の API 呼び出しで置き換えることで、トラブルシューティング時に手順を分割することが役立ちます。

1. モデルを登録します。 サンプル コードは次のようになります。

    ```python
    # register a model out of a run record
    model = best_run.register_model(model_name='my_best_model', model_path='outputs/my_model.pkl')

    # or, you can register a file or a folder of files as a model
    model = Model.register(model_path='my_model.pkl', model_name='my_best_model', workspace=ws)
    ```

2. イメージをビルドします。 サンプル コードは次のようになります。

    ```python
    # configure the image
    image_config = ContainerImage.image_configuration(runtime="python",
                                                      entry_script="score.py",
                                                      conda_file="myenv.yml")

    # create the image
    image = Image.create(name='myimg', models=[model], image_config=image_config, workspace=ws)

    # wait for image creation to finish
    image.wait_for_creation(show_output=True)
    ```

3. サービスとしてイメージをデプロイします。 サンプル コードは次のようになります。

    ```python
    # configure an ACI-based deployment
    aci_config = AciWebservice.deploy_configuration(cpu_cores=1, memory_gb=1)

    aci_service = Webservice.deploy_from_image(deployment_config=aci_config, 
                                               image=image, 
                                               name='mysvc', 
                                               workspace=ws)
    aci_service.wait_for_deployment(show_output=True)    
    ```

デプロイ プロセスを個別タスクに分割できたところで、最も一般的なエラーをいくつか確認してみましょう。

## <a name="image-building-fails"></a>イメージをビルドできない

Docker イメージをビルドできない場合、[image.wait_for_creation()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.image(class)?view=azure-ml-py#wait-for-creation-show-output-false-) または [service.wait_for_deployment()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#wait-for-deployment-show-output-false-) 呼び出しは失敗し、手掛かりを示すいくつかのエラー メッセージが表示されます。 エラーに関する詳細は、イメージ ビルド ログにもあります。 下のサンプル コードでイメージ ビルド ログ URI の確認方法がわかります。

```python
# if you already have the image object handy
print(image.image_build_log_uri)

# if you only know the name of the image (note there might be multiple images with the same name but different version number)
print(ws.images['myimg'].image_build_log_uri)

# list logs for all images in the workspace
for name, img in ws.images.items():
    print(img.name, img.version, img.image_build_log_uri)
```

イメージ ログ URI は、Azure Blob Storage に保存されているログ ファイルを指す SAS URL です。 URI をコピーし、ブラウザー ウィンドウに貼り付けます。ログ ファイルはダウンロードして参照できます。

### <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Azure Key Vault アクセス ポリシーと Azure Resource Manager テンプレート

Azure Key Vault に対するアクセス ポリシーの問題が原因でイメージ ビルドも失敗する可能性があります。 この状況は、Azure Resource Manager テンプレートを使用してワークスペースおよび関連付けられたリソース (Azure Key Vault など) を複数回作成するときに、発生する可能性があります。 たとえば、継続的インテグレーションおよびデプロイ パイプラインの一部として同じパラメーターを指定して、テンプレートを複数回使用する場合が挙げられます。

テンプレートによるリソース作成操作のほとんどはべき等操作ですが、テンプレートを使用するたびに Key Vault によってアクセス ポリシーはクリアされます。 アクセス ポリシーをクリアすると、それを使用している既存のワークスペース用の Key Vault へのアクセスは中断されます。 この状況で新しいイメージを作成しようとすると、エラーが発生します。 表示される可能性のあるエラーの例を次に示します。

__ポータル__:
```text
Create image "myimage": An internal server error occurred. Please try again. If the problem persists, contact support.
```

__SDK__:
```python
image = ContainerImage.create(name = "myimage", models = [model], image_config = image_config, workspace = ws)
Creating image
Traceback (most recent call last):
  File "C:\Python37\lib\site-packages\azureml\core\image\image.py", line 341, in create
    resp.raise_for_status()
  File "C:\Python37\lib\site-packages\requests\models.py", line 940, in raise_for_status
    raise HTTPError(http_error_msg, response=self)
requests.exceptions.HTTPError: 500 Server Error: Internal Server Error for url: https://eastus.modelmanagement.azureml.net/api/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>/images?api-version=2018-11-19

Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "C:\Python37\lib\site-packages\azureml\core\image\image.py", line 346, in create
    'Content: {}'.format(resp.status_code, resp.headers, resp.content))
azureml.exceptions._azureml_exception.WebserviceException: Received bad response from Model Management Service:
Response Code: 500
Headers: {'Date': 'Tue, 26 Feb 2019 17:47:53 GMT', 'Content-Type': 'application/json', 'Transfer-Encoding': 'chunked', 'Connection': 'keep-alive', 'api-supported-versions': '2018-03-01-preview, 2018-11-19', 'x-ms-client-request-id': '3cdcf791f1214b9cbac93076ebfb5167', 'x-ms-client-session-id': '', 'Strict-Transport-Security': 'max-age=15724800; includeSubDomains; preload'}
Content: b'{"code":"InternalServerError","statusCode":500,"message":"An internal server error occurred. Please try again. If the problem persists, contact support"}'
```

__CLI__:
```text
ERROR: {'Azure-cli-ml Version': None, 'Error': WebserviceException('Received bad response from Model Management Service:\nResponse Code: 500\nHeaders: {\'Date\': \'Tue, 26 Feb 2019 17:34:05
GMT\', \'Content-Type\': \'application/json\', \'Transfer-Encoding\': \'chunked\', \'Connection\': \'keep-alive\', \'api-supported-versions\': \'2018-03-01-preview, 2018-11-19\', \'x-ms-client-request-id\':
\'bc89430916164412abe3d82acb1d1109\', \'x-ms-client-session-id\': \'\', \'Strict-Transport-Security\': \'max-age=15724800; includeSubDomains; preload\'}\nContent:
b\'{"code":"InternalServerError","statusCode":500,"message":"An internal server error occurred. Please try again. If the problem persists, contact support"}\'',)}
```

この問題を回避するには、次のいずれかのアプローチをお勧めします。

* パラメーターが同じである場合は、テンプレートを複数回デプロイしないでください。 または、テンプレートを使用してリソースを作成する前に、既存の同じものを削除してください。
* Key Vault のアクセス ポリシーを調べ、これらのポリシーを使用してテンプレートの `accessPolicies` プロパティを設定します。
* Key Vault リソースが既に存在しているかどうかを確認します。 存在している場合は、テンプレートを使ってそれを再作成しないでください。 たとえば、既に存在する場合は、Key Vault リソースの作成を無効にするためのパラメーターを追加します。

## <a name="debug-locally"></a>ローカル デバッグ

モデルを ACI または AKS にデプロイする際に問題が発生した場合は、ローカル Web サービスとしてデプロイしてみてください。 ローカル Web サービスを使用すると、問題のトラブルシューティングが簡単になります。 モデルを含む Docker イメージがダウンロードされ、ローカル システムで起動されます。

> [!IMPORTANT]
> ローカル Web サービスのデプロイでは、ローカル システムで動作する Docker インストールが必要です。 ローカル Web サービスをデプロイする前に、Docker を実行しておく必要があります。 Docker のインストールと使用については、[https://www.docker.com/](https://www.docker.com/) を参照してください。

> [!WARNING]
> ローカル Web サービスのデプロイは、運用シナリオではサポートされていません。

ローカルにデプロイするには、`LocalWebservice.deploy_configuration()` を使用してデプロイ構成を作成するようにコードを変更します。 次に、`Model.deploy()` を使用して、サービスをデプロイします。 次の例では、モデル (`model` 変数に含まれる) をローカル Web サービスとしてデプロイします。

```python
from azureml.core.model import InferenceConfig, Model
from azureml.core.webservice import LocalWebservice

# Create inference configuration. This creates a docker image that contains the model.
inference_config = InferenceConfig(runtime="python",
                                   entry_script="score.py",
                                   conda_file="myenv.yml")

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

### <a name="update-the-service"></a>サービスの更新

ローカル テスト中に、ログ記録を追加したり、発見した問題の解決を試みるために、`score.py` ファイルを更新する必要がある場合があります。 変更を `score.py` ファイルに再度読み込むには、`reload()` を使用します。 たとえば、次のコードは、サービスのスクリプトを再度読み込み、サービスにデータを送信します。 データは、更新された `score.py` ファイルを使用してスコア付けされます。

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

### <a id="dockerlog"></a> Docker ログの確認

サービス オブジェクトから詳細な Docker エンジン ログ メッセージを出力できます。 ACI、AKS、およびローカル デプロイのログを表示できます。 次の例は、ログを出力する方法を示しています。

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```

## <a name="service-launch-fails"></a>サービスを起動できない

イメージが正常にビルドされると、デプロイ構成を使用して、コンテナーの起動が試行されます。 コンテナーの起動プロセスの一部として、スコアリング スクリプトの `init()` 関数が呼び出されます。 `init()` 関数でキャッチされない例外がある場合、エラー メッセージに **CrashLoopBackOff** エラーが表示されることがあります。

「[Docker ログの確認](#dockerlog)」セクションの情報を使用して、ログを確認します。

## <a name="function-fails-getmodelpath"></a>get_model_path() 関数が失敗する

多くの場合、スコアリング スクリプトの `init()` 関数では、コンテナー内のモデル ファイルまたはモデル ファイルのフォルダーを見つける目的で [Model.get_model_path()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) 関数が呼び出されます。 モデル ファイルまたはフォルダーが見つからない場合、この関数は失敗します。 このエラーをデバッグする最も簡単な方法は、Container シェルで以下の Python コードを実行することです。

```python
from azureml.core.model import Model
import logging
logging.basicConfig(level=logging.DEBUG)
print(Model.get_model_path(model_name='my-best-model'))
```

この例では、スコアリング スクリプトによってモデル ファイルまたはフォルダーの存在が予期されるコンテナー内のローカル パス (`/var/azureml-app` の相対パス) が出力されます。 その後、ファイルまたはフォルダーが予想される場所にあるかどうかを確認できます。

ログ レベルを DEBUG に設定すると、追加情報が記録される場合があり、エラーの特定に利用できる可能性があります。

## <a name="function-fails-runinputdata"></a>run(input_data) 関数が失敗する

サービスが正常にデプロイされたが、スコアリング エンドポイントにデータを投稿するとクラッシュする場合、代わりに詳細なエラー メッセージが返されるように `run(input_data)` 関数にエラーをキャッチするステートメントを追加できます。 例:

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

**メモ**:`run(input_data)` 呼び出しからエラー メッセージを返すことは、デバッグ目的のみで行ってください。 セキュリティ上の理由から、運用環境ではこの方法でエラー メッセージを返さないでください。

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


## <a name="advanced-debugging"></a>高度なデバッグ

場合によっては、モデル デプロイに含まれる Python コードを対話的にデバッグする必要が生じることがあります。 たとえば、エントリ スクリプトが失敗し、追加のログ記録によっても理由を特定できない場合がこれにあたります。 Visual Studio Code と Python Tools for Visual Studio (PTVSD) を使用することによって、Docker コンテナー内で実行されるコードにアタッチできます。

> [!IMPORTANT]
> このデバッグ方法は、`Model.deploy()` と `LocalWebservice.deploy_configuration` を使用してローカルでモデルをデプロイしている場合は機能しません。 代わりに、[ContainerImage](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.containerimage?view=azure-ml-py) クラスを使用してイメージを作成する必要があります。 
>
> ローカル Web サービスのデプロイでは、ローカル システムで動作する Docker インストールが必要です。 ローカル Web サービスをデプロイする前に、Docker を実行しておく必要があります。 Docker のインストールと使用については、[https://www.docker.com/](https://www.docker.com/) を参照してください。

### <a name="configure-development-environment"></a>開発環境の設定

1. ローカルの VS Code 開発環境に Python Tools for Visual Studio (PTVSD) をインストールするには、次のコマンドを使用します。

    ```
    python -m pip install --upgrade ptvsd
    ```

    VS Code で PTVSD を使用する方法の詳細については、「[Remote Debugging (リモート デバッグ)](https://code.visualstudio.com/docs/python/debugging#_remote-debugging)」を参照してください。

1. Docker イメージと通信するように VS Code を構成するには、次のような新しいデバッグ構成を作成します。

    1. VS Code から __[デバッグ]__ メニューを選択し、 __[構成を開く]__ を選択します。 __launch.json__ という名前のファイルが開きます。

    1. __launch.json__ ファイルで、`"configurations": [` を含む行を見つけ、その後に次のテキストを挿入します。

        ```json
        {
            "name": "Azure Machine Learning service: Docker Debug",
            "type": "python",
            "request": "attach",
            "port": 5678,
            "host": "localhost",
            "pathMappings": [
                {
                    "localRoot": "${workspaceFolder}",
                    "remoteRoot": "/var/azureml-app"
                }
            ]
        }
        ```

        > [!IMPORTANT]
        > 構成セクションに既に他のエントリがある場合は、挿入したコードの後にコンマ (,) を追加します。

        このセクションは、ポート 5678 を使用して Docker コンテナーにアタッチされます。

    1. __launch.json__ ファイルを保存します。

### <a name="create-an-image-that-includes-ptvsd"></a>PTVSD を含むイメージの作成

1. PTVSD が含まれるように、デプロイの Conda 環境を変更します。 次の例は、`pip_packages` パラメーターを使用して PTVSD を追加する方法を示しています。

    ```python
    from azureml.core.conda_dependencies import CondaDependencies 
    
    # Usually a good idea to choose specific version numbers
    # so training is made on same packages as scoring
    myenv = CondaDependencies.create(conda_packages=['numpy==1.15.4',            
                                'scikit-learn==0.19.1', 'pandas==0.23.4'],
                                 pip_packages = ['azureml-defaults==1.0.17', 'ptvsd'])
    
    with open("myenv.yml","w") as f:
        f.write(myenv.serialize_to_string())
    ```

1. サービスの開始時に PTVSD を起動し、接続を待機するには、`score.py` ファイルの先頭に次を追加します。

    ```python
    import ptvsd
    # Allows other computers to attach to ptvsd on this IP address and port.
    ptvsd.enable_attach(address=('0.0.0.0', 5678), redirect_output = True)
    # Wait 30 seconds for a debugger to attach. If none attaches, the script continues as normal.
    ptvsd.wait_for_attach(timeout = 30)
    print("Debugger attached...")
    ```

1. デバッグ中に、イメージを再作成せずに、イメージ内のファイルに変更を加える必要が生じる場合があります。 Docker イメージにテキスト エディター (vim) をインストールするには、`Dockerfile.steps` という名前の新しいテキスト ファイルを作成し、ファイルの内容として次を使用します。

    ```text
    RUN apt-get update && apt-get -y install vim
    ```

    テキスト エディターを使用すると、新しいイメージを作成せずに Docker イメージ内のファイルを変更し、その変更をテストできます。

1. `Dockerfile.steps` ファイルを使用するイメージを作成するには、イメージの作成時に `docker_file` パラメーターを使用します。 次の例に、この方法を示します。

    > [!NOTE]
    > この例は、`ws` が Azure Machine Learning ワークスペースをポイントし、`model` がデプロイされているモデルであることを前提としています。 `myenv.yml` ファイルには、手順 1. で作成した Conda の依存関係が含まれています。

    ```python
    from azureml.core.image import Image, ContainerImage
    image_config = ContainerImage.image_configuration(runtime= "python",
                                 execution_script="score.py",
                                 conda_file="myenv.yml",
                                 docker_file="Dockerfile.steps")

    image = Image.create(name = "myimage",
                     models = [model],
                     image_config = image_config, 
                     workspace = ws)
    # Print the location of the image in the repository
    print(image.image_location)
    ```

イメージが作成されたら、レジストリにイメージの場所が表示されます。 場所は次のテキストのようになります。

```text
myregistry.azurecr.io/myimage:1
```

このテキストの例では、レジストリ名は `myregistry`、イメージの名前は `myimage` となります。 イメージのバージョンは `1` です。

### <a name="download-the-image"></a>イメージのダウンロード

1. コマンド プロンプト、ターミナル、またはその他のシェルを開き、次の [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) コマンドを使用して、Azure Machine Learning ワークスペースを含む Azure サブスクリプションに対して認証を行います。

    ```azurecli
    az login
    ```

1. イメージを含む Azure Container Registry (ACR) に対して認証を行うには、次のコマンドを使用します。 `myregistry` を、イメージを登録したときに返されたもので置き換えます。

    ```azurecli
    az acr login --name myregistry
    ```

1. ローカルの Docker にイメージをダウンロードするには、次のコマンドを使用します。 `myimagepath` を、イメージを登録したときに返された場所で置き換えます。

    ```bash
    docker pull myimagepath
    ```

    イメージ パスは `myregistry.azurecr.io/myimage:1` のようになります。 ここで、`myregistry` がお使いのレジストリ、`myimage` がイメージ、`1` がイメージのバージョンとなります。

    > [!TIP]
    > 前の手順の認証は、持続的なものではありません。 認証コマンドとプル コマンドの間の時間が長すぎると、認証エラーが発生します。 この場合は、再認証を行ってください。

    ダウンロードの完了にかかる時間は、インターネット接続の速度によって異なります。 処理中は、ダウンロードの進行状態が表示されます。 ダウンロードが完了したら、`docker images` コマンドを使用してイメージがダウンロードされたことを確認できます。

1. イメージの操作を容易にするには、次のコマンドを使用してタグを追加します。 `myimagepath` を、手順 2. の場所の値で置き換えます。

    ```bash
    docker tag myimagepath debug:1
    ```

    残りの手順では、完全なイメージ パス値の代わりに、`debug:1` でローカル イメージを参照できます。

### <a name="debug-the-service"></a>サービスのデバッグ

> [!TIP]
> `score.py` ファイルで PTVSD 接続のタイムアウトを設定している場合、タイムアウトの有効期限が切れる前に、VS Code をデバッグ セッションに接続する必要があります。 このセクションの手順を使用する前に、VS Code を起動して `score.py` のローカル コピーを開き、ブレークポイントを設定して準備を整えておく必要があります。
>
> デバッグとブレークポイントの設定の詳細については、「[Debugging (デバッグ)](https://code.visualstudio.com/Docs/editor/debugging)」を参照してください。

1. イメージを使って Docker コンテナーを開始するには、次のコマンドを使用します。

    ```bash
    docker run --rm --name debug -p 8000:5001 -p 5678:5678 debug:1
    ```

1. VS Code をコンテナー内の PTVSD に接続するには、VS Code を開き、F5 キーを使用するか __[デバッグ]__ を選択します。 メッセージが表示されたら、 __[Azure Machine Learning service: Docker Debug]__ 構成を選択します。 サイド バーのデバッグ アイコンを選択して、[デバッグ] ドロップダウン メニューから __[Azure Machine Learning service: Docker Debug]__ エントリを選択し、緑色の矢印を使用してデバッガーをアタッチすることもできます。

    ![デバッグ アイコン、デバッグの開始ボタン、および構成セレクター](media/how-to-troubleshoot-deployment/start-debugging.png)

この時点で、VS Code は Docker コンテナー内の PTVSD に接続し、前に設定したブレークポイントで停止します。 これで、実行時のようにコードをステップ実行したり、変数を表示したりできます。

VS Code を使用した Python のデバッグの詳細については、「[Python コードのデバッグ](https://docs.microsoft.com/visualstudio/python/debugging-python-in-visual-studio?view=vs-2019)」を参照してください。

<a id="editfiles"></a>
### <a name="modify-the-container-files"></a>コンテナー ファイルの変更

イメージ内のファイルに変更を加えるには、実行中のコンテナーにアタッチし、Bash シェルを実行します。 そこから、vim を利用してファイルを編集できます。

1. 実行中のコンテナーに接続し、コンテナー内で Bash シェルを起動するには、次のコマンドを使用します。

    ```bash
    docker exec -it debug /bin/bash
    ```

1. サービスで使用されるファイルを検索するには、コンテナーで Bash シェルから次のコマンドを使用します。

    ```bash
    cd /var/azureml-app
    ```

    ここから、vim を利用して `score.py` ファイルを編集できます。 vim の使用方法の詳細については、「[Using the Vim editor (Vim エディターの使用)](https://www.tldp.org/LDP/intro-linux/html/sect_06_02.html)」を参照してください。

1. コンテナーへの変更は、通常は保持されません。 加えた変更を保存するには、上記の手順で起動したシェルを終了する前に、(つまり、別のシェルで) 次のコマンドを使用します。

    ```bash
    docker commit debug debug:2
    ```

    このコマンドによって、編集内容が含まれる `debug:2` という名前の新しいイメージが作成されます。

    > [!TIP]
    > 変更を有効にするには、現在のコンテナーを停止してから新しいバージョンの使用を開始する必要があります。

1. コンテナー内のファイルに加えた変更が、VS Code で使用するローカル ファイルと確実に同期されるようにします。 そうしないと、デバッガーで期待通りのエクスペリエンスが得られません。

### <a name="stop-the-container"></a>コンテナーの停止

コンテナーを停止するには、次のコマンドを使用します。

```bash
docker stop debug
```

## <a name="next-steps"></a>次の手順

デプロイの詳細については、以下を参照してください。

* [デプロイする方法とその場所](how-to-deploy-and-where.md)
* [チュートリアル:モデルのトレーニングとデプロイ](tutorial-train-models-with-aml.md)
