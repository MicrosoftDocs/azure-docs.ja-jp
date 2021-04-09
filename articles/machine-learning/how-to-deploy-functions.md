---
title: Azure Functions アプリに ML モデルをデプロイする (プレビュー)
titleSuffix: Azure Machine Learning
description: Azure Machine Learning を使用して、モデルを Web サービスとしてパッケージ化し、Azure Functions アプリにデプロイする方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: vaidyas
author: vaidya-s
ms.reviewer: larryfr
ms.date: 03/06/2020
ms.topic: conceptual
ms.custom: how-to, racking-python, devx-track-azurecli
ms.openlocfilehash: abb38ebbdacdf4f153148fbe121e54ede88f066a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102519267"
---
# <a name="deploy-a-machine-learning-model-to-azure-functions-preview"></a>Azure Functions に機械学習モデルをデプロイする (プレビュー)


Azure Machine Learning から関数アプリとして Azure Functions にモデルをデプロイする方法について説明します。

> [!IMPORTANT]
> Azure Machine Learning と Azure Functions の両方が一般公開されていますが、Machine Learning サービスから Functions 用にモデルをパッケージする機能はプレビュー段階です。

Azure Machine Learning を使用すると、トレーニング済みの機械学習モデルから Docker イメージを作成できます。 Azure Machine Learning のプレビュー機能を使用して、これらの機械学習モデルを関数アプリに組み込み、[Azure Functions にデプロイ](../azure-functions/functions-deployment-technologies.md#docker-container)できます。

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

デプロイを行う前に、モデルを Web サービスとして実行するために必要なものを定義する必要があります。 次の一覧で、デプロイするために必要となる中心的な項目について説明します。

* __エントリ スクリプト__。 このスクリプトは、要求を受け入れ、モデルを使用してその要求にスコアを付け、その結果を返します。

    > [!IMPORTANT]
    > エントリ スクリプトはモデルに固有のものです。受信要求データの形式、モデルで想定されるデータの形式、およびクライアントに返されるデータの形式を理解しておく必要があります。
    >
    > 要求データがモデルで使用できない形式になっている場合、スクリプトで受け入れ可能な形式に変換することができます。 また、応答をクライアントに返す前に変換することもできます。
    >
    > 既定では、関数のパッケージ化の際、入力はテキストとして扱われます。 入力の生バイトの使用に関心がある場合 (たとえば、BLOB トリガーの場合)、[生データを受け入れる AMLRequest](./how-to-deploy-advanced-entry-script.md#binary-data) を使用する必要があります。

エントリ スクリプトの詳細については、[スコアリング コードの定義](./how-to-deploy-and-where.md#define-an-entry-script)に関する記事を参照してください。

* **依存関係**。エントリ スクリプトまたはモデルを実行するために必要なヘルパー スクリプトや Python/Conda パッケージなど。

これらのエンティティは、__推論構成__ にカプセル化されます。 推論構成では、エントリ スクリプトとその他の依存関係が参照されます。

> [!IMPORTANT]
> Azure Functions で使用するための推論構成を作成する際は、[環境](/python/api/azureml-core/azureml.core.environment%28class%29)オブジェクトを使用する必要があります。 カスタム環境を定義する場合は、バージョン 1.0.45 以降の azureml-defaults を pip 依存関係として追加する必要があることに注意してください。 このパッケージには、Web サービスとしてモデルをホストするために必要な機能が含まれています。 次の例で、環境オブジェクトを作成し、推論構成でそれを使用する方法を示します。
>
> ```python
> from azureml.core.environment import Environment
> from azureml.core.conda_dependencies import CondaDependencies
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
> Functions にデプロイするときに __デプロイ構成__ を作成する必要はありません。

## <a name="install-the-sdk-preview-package-for-functions-support"></a>関数のサポート用に SDK プレビュー パッケージをインストールする

Azure Functions 用のパッケージをビルドするには、SDK プレビュー パッケージをインストールする必要があります。

```bash
pip install azureml-contrib-functions
```

## <a name="create-the-image"></a>イメージの作成

Azure Functions にデプロイする Docker イメージを作成するには、[azureml.contrib.functions.package](/python/api/azureml-contrib-functions/azureml.contrib.functions) または使用するトリガーに固有のパッケージ関数を使用します。 次のコード スニペットで、モデルと推論構成から、BLOB トリガーを使用する新しいパッケージを作成する方法を示します。

> [!NOTE]
> このコード スニペットは、`model` に登録済みのモデルが含まれており、`inference_config` に推論環境の構成が含まれていることを前提としています。 詳細については、「[Azure Machine Learning を使用してモデルをデプロイする](how-to-deploy-and-where.md)」を参照してください。

```python
from azureml.contrib.functions import package
from azureml.contrib.functions import BLOB_TRIGGER
blob = package(ws, [model], inference_config, functions_enabled=True, trigger=BLOB_TRIGGER, input_path="input/{blobname}.json", output_path="output/{blobname}_out.json")
blob.wait_for_creation(show_output=True)
# Display the package location/ACR path
print(blob.location)
```

`show_output=True` の場合、Docker ビルド プロセスの出力が表示されます。 プロセスが完了すると、ワークスペース用の Azure Container Registry 内にイメージが作成されます。 イメージがビルドされると、Azure Container Registry 内の場所が表示されます。 返される場所は、`<acrinstance>.azurecr.io/package@sha256:<imagename>` の形式です。

> [!NOTE]
> 現在、関数のパッケージ化では、HTTP トリガー、BLOB トリガー、および Service Bus トリガーがサポートされています。 トリガーの詳細については、[Azure Functions のバインド](../azure-functions/functions-bindings-storage-blob-trigger.md#blob-name-patterns)に関する記事をご覧ください。

> [!IMPORTANT]
> イメージをデプロイするときに使用されるため、場所情報を保存します。

## <a name="deploy-image-as-a-web-app"></a>イメージを Web アプリとしてデプロイする

1. 次のコマンドを使用して、イメージを含む Azure Container Registry のログイン資格情報を取得します。 `<myacr>` を、以前 `blob.location` から返された値に置き換えます。 

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

    この例では、_Linux Basic_ 価格レベル (`--sku B1`) が使用されます。

    > [!IMPORTANT]
    > Azure Machine Learning によって作成されたイメージでは Linux が使用されるため、`--is-linux` パラメーターを使用する必要があります。

1. Web ジョブ ストレージに使用するストレージ アカウントを作成し、その接続文字列を取得します。 `<webjobStorage>` を使用する名前に置き換えます。

    ```azurecli-interactive
    az storage account create --name <webjobStorage> --location westeurope --resource-group myresourcegroup --sku Standard_LRS
    ```
    ```azurecli-interactive
    az storage account show-connection-string --resource-group myresourcegroup --name <webJobStorage> --query connectionString --output tsv
    ```

1. 関数アプリを作成するには、次のコマンドを使用します。 `<app-name>` を使用する名前に置き換えます。 `<acrinstance>` と `<imagename>` を、前の手順で返された `package.location` の値に置き換えます。 `<webjobStorage>` は、前の手順で作成したストレージ アカウントの名前で置き換えます。

    ```azurecli-interactive
    az functionapp create --resource-group myresourcegroup --plan myplanname --name <app-name> --deployment-container-image-name <acrinstance>.azurecr.io/package:<imagename> --storage-account <webjobStorage>
    ```

    > [!IMPORTANT]
    > この時点で、関数アプリが作成されています。 しかし、イメージを含む Azure コンテナー レジストリに資格情報または BLOB トリガーの接続文字列が指定されていないため、関数アプリはアクティブになりません。 次の手順で、コンテナー レジストリの認証情報と接続文字列を指定します。 

1. BLOB トリガー ストレージに使用するストレージ アカウントを作成し、その接続文字列を取得します。 `<triggerStorage>` を使用する名前に置き換えます。

    ```azurecli-interactive
    az storage account create --name <triggerStorage> --location westeurope --resource-group myresourcegroup --sku Standard_LRS
    ```
    ```azurecli-interactive
    az storage account show-connection-string --resource-group myresourcegroup --name <triggerStorage> --query connectionString --output tsv
    ```
    関数アプリに提供するため、この接続文字列を記録しておきます。 これは後で `<triggerConnectionString>` に使用します。

1. ストレージ アカウントに入力用と出力用のコンテナーを作成します。 `<triggerConnectionString>` は、先ほど返された接続文字列で置き換えます。

    ```azurecli-interactive
    az storage container create -n input --connection-string <triggerConnectionString>
    ```
    ```azurecli-interactive
    az storage container create -n output --connection-string <triggerConnectionString>
    ```

1. トリガー接続文字列を関数アプリに関連付けるには、次のコマンドを使用します。 `<app-name>` は、関数アプリの名前で置き換えます。 `<triggerConnectionString>` は、先ほど返された接続文字列で置き換えます。

    ```azurecli-interactive
    az functionapp config appsettings set --name <app-name> --resource-group myresourcegroup --settings "TriggerConnectionString=<triggerConnectionString>"
    ```
1. 次のコマンドを使用して、作成したコンテナーに関連付けられているタグを取得する必要があります。 `<username>` は、先ほどコンテナー レジストリから返されたユーザー名で置き換えます。

    ```azurecli-interactive
    az acr repository show-tags --repository package --name <username> --output tsv
    ```
    返された値を保存します。これは次の手順で `imagetag` として使用します。

1. コンテナー レジストリにアクセスするために必要な資格情報を関数アプリに指定するには、次のコマンドを使用します。 `<app-name>` は、関数アプリの名前で置き換えます。 `<acrinstance>` と `<imagetag>` を、前の手順の AZ CLI 呼び出しの値に置き換えます。 `<username>` と `<password>` を、前の手順で取得した ACR ログイン情報に置き換えます。

    ```azurecli-interactive
    az functionapp config container set --name <app-name> --resource-group myresourcegroup --docker-custom-image-name <acrinstance>.azurecr.io/package:<imagetag> --docker-registry-server-url https://<acrinstance>.azurecr.io --docker-registry-server-user <username> --docker-registry-server-password <password>
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
        "value": "DOCKER|myml08024f78fd10.azurecr.io/package:20190827195524"
    }
    ]
    ```

この時点で、関数アプリでイメージの読み込みが開始されます。

> [!IMPORTANT]
> イメージが読み込まれるまで数分かかる場合があります。 Azure Portal を使って進行状況を監視できます。

## <a name="test-the-deployment"></a>展開をテスト

イメージが読み込まれ、アプリを使用できるようになったら、次の手順を使用してアプリをトリガーします。

1. score.py ファイルで想定されるデータを含む、テキスト ファイルを作成します。 次の例では、10 個の数値配列が想定される score.py を使用することにします。

    ```json
    {"data": [[1, 2, 3, 4, 5, 6, 7, 8, 9, 10], [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]]}
    ```

    > [!IMPORTANT]
    > データの形式は、お使いの score.py とモデルで想定される内容によって異なります。

2. 次のコマンドを使用して、以前に作成したトリガー ストレージ BLOB の入力コンテナーにこのファイルをアップロードします。 `<file>` をこのデータを含むファイル名に置き換えます。 `<triggerConnectionString>` は、先ほど返された接続文字列で置き換えます。 この例では、`input` は以前に作成した入力コンテナーの名前です。 別の名前を使用した場合は、この値を置き換えます。

    ```azurecli-interactive
    az storage blob upload --container-name input --file <file> --name <file> --connection-string <triggerConnectionString>
    ```

    このコマンドの出力は、次の JSON のようになります。

    ```json
    {
    "etag": "\"0x8D7C21528E08844\"",
    "lastModified": "2020-03-06T21:27:23+00:00"
    }
    ```

3. 関数で生成された出力を表示するには、次のコマンドを使用して、生成された出力ファイルをリストします。 `<triggerConnectionString>` は、先ほど返された接続文字列で置き換えます。 この例では、`output` は、以前に作成した出力コンテナーの名前です。 別の名前を使用した場合は、この値を置き換えます。

    ```azurecli-interactive
    az storage blob list --container-name output --connection-string <triggerConnectionString> --query '[].name' --output tsv
    ```

    このコマンドの出力は、`sample_input_out.json` のようになります。

4. ファイルをダウンロードしてコンテンツを検査するには、次のコマンドを使用します。 `<file>` は、前のコマンドで返されたファイル名で置き換えます。 `<triggerConnectionString>` は、先ほど返された接続文字列で置き換えます。 

    ```azurecli-interactive
    az storage blob download --container-name output --file <file> --name <file> --connection-string <triggerConnectionString>
    ```

    コマンドが完了したら、そのファイルを開きます。 それには、モデルによって返されたデータが含まれています。

Blob トリガーの使用に関する詳細については、[Azure Blob storage によってトリガーされる関数の作成](../azure-functions/functions-create-storage-blob-triggered-function.md) に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

* [Functions](../azure-functions/functions-create-function-linux-custom-image.md) のドキュメントで、関数アプリを構成する方法を学習する。
* [Azure Blob Storage のバインド](../azure-functions/functions-bindings-storage-blob.md)に関する記事で、Blob Storage のトリガーの詳細について学習する。
* [Azure App Service にモデルをデプロイする](how-to-deploy-app-service.md)。
* [Web サービスとしてデプロイされた ML モデルを使用する](how-to-consume-web-service.md)
* [API リファレンス](/python/api/azureml-contrib-functions/azureml.contrib.functions)
