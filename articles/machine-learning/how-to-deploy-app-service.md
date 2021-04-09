---
title: Azure App Service に ML モデルをデプロイする (プレビュー)
titleSuffix: Azure Machine Learning
description: Azure Machine Learning を使用して Azure App Service を使用した Web アプリにトレーニング済み ML モデルをデプロイする方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 06/23/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, deploy, devx-track-azurecli
ms.openlocfilehash: 3b1b416f3fec9e40261a82c88260c041918c1424
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102522004"
---
# <a name="deploy-a-machine-learning-model-to-azure-app-service-preview"></a>Azure App Service に機械学習モデルをデプロイする (プレビュー)


Azure Machine Learning から Web アプリとして Azure App Service にモデルをデプロイする方法について説明します。

> [!IMPORTANT]
> Azure Machine Learning と Azure App Service の両方が一般公開されていますが、Machine Learning サービスから App Service にモデルをデプロイする機能はプレビュー段階です。

Azure Machine Learning を使用すると、トレーニング済みの機械学習モデルから Docker イメージを作成できます。 このイメージには、データを受信してモデルに送信し、応答を返す Web サービスが含まれています。 Azure App Service を使用してこのイメージを展開でき、さらに次の機能が用意されています。

* セキュリティ強化を目的とした[高度な認証](../app-service/configure-authentication-provider-aad.md)。 認証方法には、Azure Active Directory と多要素認証の両方が含まれます。
* 再デプロイ不要の[自動スケーリング](../azure-monitor/autoscale/autoscale-get-started.md?toc=%2fazure%2fapp-service%2ftoc.json)。
* クライアントとサービスの間のセキュリティで保護された通信のための [TLS サポート](../app-service/configure-ssl-certificate-in-code.md)。

Azure App Service によって提供される機能の詳細については、[App Service の概要](../app-service/overview.md)を参照してください。

> [!IMPORTANT]
> デプロイしたモデルで使用されるスコアリング データまたはスコアリングの結果を記録する能力が必要な場合は、代わりに Azure Kubernetes Service にデプロイする必要があります。 詳細については、[実稼働環境でのデータの収集](how-to-enable-data-collection.md)に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件

* Azure Machine Learning ワークスペース。 詳細については、「[ワークスペースの作成](how-to-manage-workspace.md)を参照してください。
* [Azure CLI](/cli/azure/install-azure-cli)。
* ワークスペースに登録されているトレーニング済みの機械学習モデル。 モデルがない場合は、[イメージ分類のチュートリアル: モデルのトレーニング](tutorial-train-models-with-aml.md)を使用して、トレーニングと登録を行います。

    > [!IMPORTANT]
    > この記事に含まれるスニペットは、次の変数が設定されていることを前提としています。
    >
    > * `ws` - Azure Machine Learning ワークスペース。
    > * `model` - デプロイされる登録済みのモデル。
    > * `inference_config` - モデルの推論構成。
    >
    > これらの変数の設定の詳細については、「[Azure Machine Learning を使用してモデルをデプロイする](how-to-deploy-and-where.md)」を参照してください。

## <a name="prepare-for-deployment"></a>展開を準備する

デプロイを行う前に、モデルを Web サービスとして実行するために必要なものを定義する必要があります。 次の一覧で、デプロイするために必要な主要項目について説明します。

* __エントリ スクリプト__。 このスクリプトは、要求を受け入れ、モデルを使用してその要求にスコアを付け、その結果を返します。

    > [!IMPORTANT]
    > エントリ スクリプトはモデルに固有のものです。受信要求データの形式、モデルで想定されるデータの形式、およびクライアントに返されるデータの形式を理解しておく必要があります。
    >
    > 要求データがモデルで使用できない形式になっている場合、スクリプトで受け入れ可能な形式に変換することができます。 また、応答をクライアントに返す前に変換することもできます。

    > [!IMPORTANT]
    > Azure Machine Learning SDK には、Web サービスでデータストアまたはデータ セットにアクセスする方法は用意されていません。 デプロイの外部に格納されているデータにアクセスするためにデプロイされたモデルが必要な場合 (Azure Storage アカウントの場合など)、関連する SDK を使用してカスタム コード ソリューションを開発する必要があります。 たとえば、[Azure Storage SDK for Python](https://github.com/Azure/azure-storage-python) です。
    >
    > シナリオに適したもう 1 つの方法として[バッチ予測](./tutorial-pipeline-batch-scoring-classification.md)があります。これにより、スコアリング時にデータストアにアクセスすることができます。

    エントリ スクリプトの詳細については、「[Azure Machine Learning を使用してモデルをデプロイする](how-to-deploy-and-where.md)」を参照してください。

* **依存関係**。エントリ スクリプトまたはモデルを実行するために必要なヘルパー スクリプトや Python/Conda パッケージなど。

これらのエンティティは、__推論構成__ にカプセル化されます。 推論構成では、エントリ スクリプトとその他の依存関係が参照されます。

> [!IMPORTANT]
> Azure App Service で使用するための推論構成を作成する際は、[環境](/python/api/azureml-core/azureml.core.environment(class))オブジェクトを使用する必要があります。 カスタム環境を定義する場合は、バージョン 1.0.45 以降の azureml-defaults を pip 依存関係として追加する必要があることに注意してください。 このパッケージには、Web サービスとしてモデルをホストするために必要な機能が含まれています。 次の例で、環境オブジェクトを作成し、推論構成でそれを使用する方法を示します。
>
> ```python
> from azureml.core.environment import Environment
> from azureml.core.conda_dependencies import CondaDependencies
> from azureml.core.model import InferenceConfig
>
> # Create an environment and add conda dependencies to it
> myenv = Environment(name="myenv")
> # Enable Docker based environment
> myenv.docker.enabled = True
> # Build conda dependencies
> myenv.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'],
>                                                            pip_packages=['azureml-defaults'])
> inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
> ```

環境の詳細については、[トレーニングとデプロイのための環境の作成と管理](how-to-use-environments.md)に関する記事を参照してください。

推論構成の詳細については、「[Azure Machine Learning を使用してモデルをデプロイする](how-to-deploy-and-where.md)」を参照してください。

> [!IMPORTANT]
> Azure App Service にデプロイするときに __デプロイ構成__ を作成する必要はありません。

## <a name="create-the-image"></a>イメージの作成

Azure App Service にデプロイされる Docker イメージを作成するには、[Model.package](/python/api/azureml-core/azureml.core.model.model) を使用します。 次のコード スニペットで、モデルと推論構成から新しいイメージを作成する方法を示します。

> [!NOTE]
> このコード スニペットは、`model` に登録済みのモデルが含まれており、`inference_config` に推論環境の構成が含まれていることを前提としています。 詳細については、「[Azure Machine Learning を使用してモデルをデプロイする](how-to-deploy-and-where.md)」を参照してください。

```python
from azureml.core import Model

package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
# Display the package location/ACR path
print(package.location)
```

`show_output=True` の場合、Docker ビルド プロセスの出力が表示されます。 プロセスが完了すると、ワークスペース用の Azure Container Registry 内にイメージが作成されます。 イメージがビルドされると、Azure Container Registry 内の場所が表示されます。 返される場所は、`<acrinstance>.azurecr.io/package@sha256:<imagename>` の形式です。 たとえば、「 `myml08024f78fd10.azurecr.io/package@sha256:20190827151241` 」のように入力します。

> [!IMPORTANT]
> イメージをデプロイするときに使用されるため、場所情報を保存します。

## <a name="deploy-image-as-a-web-app"></a>イメージを Web アプリとしてデプロイする

1. 次のコマンドを使用して、イメージを含む Azure Container Registry のログイン資格情報を取得します。 `<acrinstance>` を、以前 `package.location` から返された値に置き換えます。

    ```azurecli-interactive
    az acr credential show --name <myacr>
    ```

    このコマンドの出力は、次の JSON ドキュメントのようになります。

    ```json
    {
    "passwords": [
        {
        "name": "password",
        "value": "Iv0lRZQ9762LUJrFiffo3P4sWgk4q+nW"
        },
        {
        "name": "password2",
        "value": "=pKCxHatX96jeoYBWZLsPR6opszr==mg"
        }
    ],
    "username": "myml08024f78fd10"
    }
    ```

    __username__ の値と __passwords__ の 1 つを保存します。

1. サービスをデプロイするリソース グループまたは App Service プランがまだない場合は、次のコマンドで両方の作成方法を確認してください。

    ```azurecli-interactive
    az group create --name myresourcegroup --location "West Europe"
    az appservice plan create --name myplanname --resource-group myresourcegroup --sku B1 --is-linux
    ```

    この例では、__Basic__ 価格レベル (`--sku B1`) が使用されます。

    > [!IMPORTANT]
    > Azure Machine Learning によって作成されたイメージでは Linux が使用されるため、`--is-linux` パラメーターを使用する必要があります。

1. Web アプリを作成するには、次のコマンドを使用します。 `<app-name>` を使用する名前に置き換えます。 `<acrinstance>` と `<imagename>` を、前の手順で返された `package.location` の値に置き換えます。

    ```azurecli-interactive
    az webapp create --resource-group myresourcegroup --plan myplanname --name <app-name> --deployment-container-image-name <acrinstance>.azurecr.io/package@sha256:<imagename>
    ```

    このコマンドでは、次の JSON ドキュメントのような情報が返されます。

    ```json
    {
    "adminSiteName": null,
    "appServicePlanName": "myplanname",
    "geoRegion": "West Europe",
    "hostingEnvironmentProfile": null,
    "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myplanname",
    "kind": "linux",
    "location": "West Europe",
    "maximumNumberOfWorkers": 1,
    "name": "myplanname",
    < JSON data removed for brevity. >
    "targetWorkerSizeId": 0,
    "type": "Microsoft.Web/serverfarms",
    "workerTierName": null
    }
    ```

    > [!IMPORTANT]
    > この時点で、Web アプリが作成されています。 ただし、イメージを含む Azure Container Registry に資格情報が指定されていないため、Web アプリはアクティブになりません。 次の手順で、コンテナー レジストリの認証情報を指定します。

1. コンテナー レジストリにアクセスするために必要な資格情報を Web アプリに指定するには、次のコマンドを使用します。 `<app-name>` を使用する名前に置き換えます。 `<acrinstance>` と `<imagename>` を、前の手順で返された `package.location` の値に置き換えます。 `<username>` と `<password>` を、前の手順で取得した ACR ログイン情報に置き換えます。

    ```azurecli-interactive
    az webapp config container set --name <app-name> --resource-group myresourcegroup --docker-custom-image-name <acrinstance>.azurecr.io/package@sha256:<imagename> --docker-registry-server-url https://<acrinstance>.azurecr.io --docker-registry-server-user <username> --docker-registry-server-password <password>
    ```

    このコマンドでは、次の JSON ドキュメントのような情報が返されます。

    ```json
    [
    {
        "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
        "slotSetting": false,
        "value": "false"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_URL",
        "slotSetting": false,
        "value": "https://myml08024f78fd10.azurecr.io"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_USERNAME",
        "slotSetting": false,
        "value": "myml08024f78fd10"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
        "slotSetting": false,
        "value": null
    },
    {
        "name": "DOCKER_CUSTOM_IMAGE_NAME",
        "value": "DOCKER|myml08024f78fd10.azurecr.io/package@sha256:20190827195524"
    }
    ]
    ```

この時点で、Web アプリでイメージの読み込みが開始されます。

> [!IMPORTANT]
> イメージが読み込まれるまで数分かかる場合があります。 進行状況を監視するには、次のコマンドを使用します。
>
> ```azurecli-interactive
> az webapp log tail --name <app-name> --resource-group myresourcegroup
> ```
>
> イメージが読み込まれ、サイトがアクティブになると、ログに `Container <container name> for site <app-name> initialized successfully and is ready to serve requests` というメッセージが表示されます。

イメージがデプロイされたら、次のコマンドを使用してホスト名を見つけることができます。

```azurecli-interactive
az webapp show --name <app-name> --resource-group myresourcegroup
```

このコマンドでは、`<app-name>.azurewebsites.net` というホスト名のような情報が返されます。 この値は、サービスの __ベース URL__ の一部として使用します。

## <a name="use-the-web-app"></a>Web アプリを使用する

モデルに要求を渡す Web サービスは、`{baseurl}/score` にあります。 たとえば、「 `https://<app-name>.azurewebsites.net/score` 」のように入力します。 次の Python コードは、URL にデータを送信し、応答を表示する方法を示しています。

```python
import requests
import json

scoring_uri = "https://mywebapp.azurewebsites.net/score"

headers = {'Content-Type':'application/json'}

test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10],
    [10,9,8,7,6,5,4,3,2,1]
]})

response = requests.post(scoring_uri, data=test_sample, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

## <a name="next-steps"></a>次のステップ

* [App Service on Linux](/azure/app-service/containers/) のドキュメントで Web アプリを構成する方法を確認します。
* 「[Azure での自動スケールの使用](../azure-monitor/autoscale/autoscale-get-started.md?toc=%2fazure%2fapp-service%2ftoc.json)」でスケールの詳細を確認します。
* [Azure App Service で TLS/SSL 証明書を使用します](../app-service/configure-ssl-certificate-in-code.md)。
* [Azure Active Directory サインインを使用するように App Service アプリを構成します](../app-service/configure-authentication-provider-aad.md)。
* [Web サービスとしてデプロイされた ML モデルを使用する](how-to-consume-web-service.md)