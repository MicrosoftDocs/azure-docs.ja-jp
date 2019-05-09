---
title: デプロイ トラブルシューティング ガイド
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning service を使用する AKS と ACI に関する一般的な Docker のデプロイ エラーの回避、解決、トラブルシューティング方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: chris-lauren
ms.author: clauren
ms.reviewer: jmartens
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 815be7400e0a0560ace7e07b317aeb25c2feacd5
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2019
ms.locfileid: "57450976"
---
# <a name="troubleshooting-azure-machine-learning-service-aks-and-aci-deployments"></a>Azure Machine Learning service の AKS および ACI デプロイのトラブルシューティング

この記事では、Azure Machine Learning service を使用する Azure Container Instances (ACI) と Azure Kubernetes Service (AKS) に関する一般的な Docker のデプロイ エラーの回避または解決する方法について説明します。

Azure Machine Learning サービスでモデルをデプロイするとき、さまざまなタスクが実行されます。 これはイベントの複雑な一連のイベントであり、問題が発生することがあります。 デプロイ タスクの内容:

1. ワークスペース モデル レジストリにモデルを登録します。

2. Docker イメージをビルドします。これには次のタスクが含まれます。
    1. 登録済みのモデルをレジストリからダウンロードします。 
    2. 環境 yaml ファイルに指定した依存関係に基づき、Python 環境を利用し、Dockerfile を作成します。
    3. Dockerfile で指定するモデル ファイルとスコアリング スクリプトを追加します。
    4. Dockerfile を使用して新しい Docker イメージをビルドします。
    5. ワークスペースに関連付けられている Azure Container Registry に Docker イメージを登録します。

3. Docker イメージを Azure Container Instance (ACI) サービスまたは Azure Kubernetes Service (AKS) にデプロイします。

4. ACI または AKS で新しいコンテナーを起動します。 

このプロセスの詳細は[モデル管理](concept-model-management-and-deployment.md)の概要にあります。

## <a name="before-you-begin"></a>開始する前に

問題に直面したら、最初に行うべきことは、(前述の) デプロイ タスクを個々の手順に分割し、問題を隔離することです。 

この作業は `Webservice.deploy` API または `Webservice.deploy_from_model` API を利用している場合に役立ちます。これらの関数では、前述の手順が 1 つのアクションにグループ化されるためです。 一般的にこれらの API は便利ですが、以下の API 呼び出しで置き換えることで、トラブルシューティング時に手順を分割することが役立ちます。

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
                                                      execution_script="score.py",
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
Docker イメージをビルドできない場合、`image.wait_for_creation()` 呼び出しが失敗し、エラー メッセージが表示されます。そのメッセージでヒントが見つかることがあります。 エラーに関する詳細は、イメージ ビルド ログにもあります。 下のサンプル コードでイメージ ビルド ログ URI の確認方法がわかります。

```python
# if you already have the image object handy
print(image.image_build_log_uri)

# if you only know the name of the image (note there might be multiple images with the same name but different version number)
print(ws.images['myimg'].image_build_log_uri)

# list logs for all images in the workspace
for name, img in ws.images.items():
    print (img.name, img.version, img.image_build_log_uri)
```
イメージ ログ URI は、Azure Blob Storage に保存されているログ ファイルを指す SAS URL です。 URI をコピーし、ブラウザー ウィンドウに貼り付けます。ログ ファイルはダウンロードして参照できます。

### <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Azure Key Vault アクセス ポリシーと Azure Resource Manager テンプレート

Azure Key Vault に対するアクセス ポリシーの問題が原因でイメージ ビルドも失敗する可能性があります。 これは、Azure Resource Manager テンプレートを使用してワークスペースおよび関連付けられたリソース (Azure Key Vault など) を複数回作成するときに、発生する可能性があります。 たとえば、継続的インテグレーションおよびデプロイ パイプラインの一部として同じパラメーターを指定して、テンプレートを複数回使用する場合が挙げられます。

テンプレートによるリソース作成操作のほとんどはべき等操作ですが、テンプレートを使用するたびに Key Vault によってアクセス ポリシーはクリアされます。 これにより、それを使用している既存のワークスペース用の Key Vault へのアクセスは中断されます。 結果として、新しいイメージを作成しようとすると、エラーが発生します。 表示される可能性のあるエラーの例を次に示します。

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
* Key Vault のアクセス ポリシーを調べ、この結果に基づいてテンプレートの `accessPolicies` プロパティを設定します。
* Key Vault リソースが既に存在しているかどうかを確認します。 存在している場合は、テンプレートを使ってそれを再作成しないでください。 たとえば、既に存在する場合は、Key Vault リソースの作成を無効にするためのパラメーターを追加します。

## <a name="service-launch-fails"></a>サービスを起動できない
イメージが正常にビルドされると、デプロイ構成に基づき、ACI または AKS でコンテナーの起動が試行されます。 ACI デプロイを先に試すことをお勧めします。よりシンプルな単一コンテナー デプロイであることがその理由です。 この方法で AKS 固有の問題を除外できます。

コンテナーの起動プロセスの一部として、スコアリング スクリプトの `init()` 関数が呼び出されます。 `init()` 関数でキャッチされない例外がある場合、エラー メッセージに **CrashLoopBackOff** エラーが表示されることがあります。 以下は問題解決に役立つヒントです。

### <a name="inspect-the-docker-log"></a>Docker ログを確認する
サービス オブジェクトから詳細な Docker エンジン ログ メッセージを出力できます。

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```

### <a name="debug-the-docker-image-locally"></a>Docker イメージをローカルでデバッグする
Docker ログでは問題に関する情報が十分に提供されないことがあります。 さらに一歩進め、ビルドされた Docker イメージを取得し、ローカル コンテナーを起動し、ライブ コンテナー内で直接、対話的にデバッグできます。 ローカル コンテナーを起動するには、Docker エンジンをローカルで実行しておく必要があります。[azure-cli](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) もインストールしておくと非常に簡単になります。

まず、イメージの場所を見つける必要があります。

```python
# print image location
print(image.image_location)
```

イメージの場所の形式は `<acr-name>.azurecr.io/<image-name>:<version-number>` です。たとえば、`myworkpaceacr.azurecr.io/myimage:3` のようになります。 

次にコマンドライン ウィンドウに移動します。 azure-cli をインストールしている場合、次のコマンドを入力し、イメージが保存されているワークスペースに関連付けられている ACR (Azure Container Registry) にサインインできます。 

```sh
# log on to Azure first if you haven't done so before
$ az login

# make sure you set the right subscription in case you have access to multiple subscriptions
$ az account set -s <subscription_name_or_id>

# now let's log in to the workspace ACR
# note the acr-name is the domain name WITHOUT the ".azurecr.io" postfix
# e.g.: az acr login -n myworkpaceacr
$ az acr login -n <acr-name>
```
azure-cli をインストールしていない場合、`docker login` コマンドを使用して ACR にログインできます。 ただし、先に Azure portal から ACR のユーザー名とパスワードを取得する必要があります。

ACR にログインしたら、Docker イメージを取得し、コンテナーをローカルで起動できます。その後、`docker run` コマンドを使用し、bash セッションを起動してデバッグできます。

```sh
# note the image_id is <acr-name>.azurecr.io/<image-name>:<version-number>
# for example: myworkpaceacr.azurecr.io/myimage:3
$ docker run -it <image_id> /bin/bash
```

実行中のコンテナーで bash セッションを開始すると、`/var/azureml-app` フォルダーでスコアリング スクリプトを見つけることができます。 その後、Python セッションを開始し、スコアリング スクリプトをデバッグできます。 

```sh
# enter the directory where scoring scripts live
cd /var/azureml-app

# find what Python packages are installed in the python environment
pip freeze

# sanity-check on score.py
# you might want to edit the score.py to trigger init().
# as most of the errors happen in init() when you are trying to load the model.
python score.py
```
スクリプトの編集にテキスト エディターが必要な場合、vim、nano、Emacs、普段ご利用のその他のエディターをインストールできます。

```sh
# update package index
apt-get update

# install a text editor of your choice
apt-get install vim
apt-get install nano
apt-get install emacs

# launch emacs (for example) to edit score.py
emacs score.py

# exit the container bash shell
exit
```

Web サービスをローカルで起動し、それに HTTP トラフィックを送信することもできます。 Docker コンテナーの Flask サーバーはポート 5001 で実行されます。 ホスト コンピューターで利用できるその他のポートにマッピングできます。
```sh
# you can find the scoring API at: http://localhost:8000/score
$ docker run -p 8000:5001 <image_id>
```

## <a name="function-fails-getmodelpath"></a>get_model_path() 関数が失敗する
多くの場合、スコアリング スクリプトの `init()` 関数では、コンテナー内のモデル ファイルまたはモデル ファイルのフォルダーを見つける目的で `Model.get_model_path()` 関数が呼び出されます。 モデル ファイルまたはフォルダーが見つからないとき、多くの場合、これが失敗の原因です。 このエラーをデバッグする最も簡単な方法は、Container シェルで以下の Python コードを実行することです。

```python
import logging
logging.basicConfig(level=logging.DEBUG)
from azureml.core.model import Model
print(Model.get_model_path(model_name='my-best-model'))
```

スコアリング スクリプトによるモデル ファイルまたはフォルダーの検索が予想されるコンテナー内のローカル パス (`/var/azureml-app` の相対パス) が出力されます。 その後、ファイルまたはフォルダーが予想される場所にあるかどうかを確認できます。

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
**メモ**:`run(input_data)` 呼び出しからエラー メッセージを返すことは、デバッグ目的のみで行ってください。 セキュリティ上の理由から、運用環境でこれを行うことはお勧めできません。

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

`autoscale_target_utilization`、`autoscale_max_replicas`、`autoscale_min_replicas` の設定方法の詳細については、[AksWebservice](https://docs.microsoft.com/en-us/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py) モジュール リファレンスを参照してください。


## <a name="next-steps"></a>次の手順

デプロイの詳細については、以下を参照してください。 
* [デプロイする方法とその場所](how-to-deploy-and-where.md)

* [チュートリアル:モデルのトレーニングとデプロイ](tutorial-train-models-with-aml.md)
