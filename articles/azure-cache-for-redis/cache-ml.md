---
title: Azure Cache for Redis を使用して Azure Functions に機械学習モデルをデプロイする
description: この記事では、Azure Cache for Redis インスタンスを使用して、Azure Machine Learning からのモデルを Azure Functions に関数アプリとしてデプロイします。 Azure Cache for Redis は非常にパフォーマンスが高くスケーラブルです。Azure Machine Learning モデルと組み合わせると、アプリケーションの待機時間が短くなり、スループットも向上します。
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 09/30/2020
ms.openlocfilehash: ec8943bc73cac2020350dd4916f040f031cd842b
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102499698"
---
# <a name="deploy-a-machine-learning-model-to-azure-functions-with-azure-cache-for-redis"></a>Azure Cache for Redis を使用して Azure Functions に機械学習モデルをデプロイする 

この記事では、Azure Cache for Redis インスタンスを使用して、Azure Machine Learning からのモデルを Azure Functions に関数アプリとしてデプロイします。  

Azure Cache for Redis は非常にパフォーマンスが高くスケーラブルです。Azure Machine Learning モデルと組み合わせると、アプリケーションの待機時間が短くなり、スループットも向上します。 キャッシュが特に役立つシナリオがいくつかあります。データを推論するとき、および実際のモデル推論結果に対して使用されます。 いずれのシナリオでも、メタ データまたは結果はメモリ内に格納されるため、パフォーマンスが向上します。 

> [!NOTE]
> Azure Machine Learning と Azure Functions の両方が一般公開されていますが、Machine Learning サービスから Functions 用にモデルをパッケージする機能はプレビュー段階です。  
>

## <a name="prerequisites"></a>前提条件
* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/)。
* Azure Machine Learning ワークスペース。 詳細については、「[ワークスペースの作成](../machine-learning/how-to-manage-workspace.md)を参照してください。
* [Azure CLI](/cli/azure/install-azure-cli)。
* ワークスペースに登録されているトレーニング済みの機械学習モデル。 モデルがない場合は、[イメージ分類のチュートリアル: モデルのトレーニング](../machine-learning/tutorial-train-models-with-aml.md)を使用して、トレーニングと登録を行います。

> [!IMPORTANT]
> この記事に含まれるスニペットは、次の変数が設定されていることを前提としています。
>
> * `ws` - Azure Machine Learning ワークスペース。
> * `model` - デプロイされる登録済みのモデル。
> * `inference_config` - モデルの推論構成。
>
> これらの変数の設定の詳細については、「[Azure Machine Learning を使用してモデルをデプロイする](../machine-learning/how-to-deploy-and-where.md)」を参照してください。

## <a name="create-an-azure-cache-for-redis-instance"></a>Azure Cache for Redis インスタンスを作成する 
Basic、Standard、または Premium のいずれのキャッシュ インスタンスでも Azure Functions に機械学習モデルをデプロイすることができます。 キャッシュ インスタンスを作成するには、これらの手順に従います。  

1. Azure portal ホームページに戻るか、サイドバー メニューを開いて、 **[リソースの作成]** を選択します。 
   
1. **[新規]** ページで、 **[データベース]** を選択し、 **[Azure Cache for Redis]** を選択します。

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="[Azure Cache for Redis] を選択します。":::
   
1. **[新規 Redis Cache]** ページで、新しいキャッシュの設定を構成します。
   
   | 設定      | 推奨値  | 説明 |
   | ------------ |  ------- | -------------------------------------------------- |
   | **DNS 名** | グローバルに一意の名前を入力します。 | キャッシュ名は 1 から 63 文字の文字列で、数字、英字、ハイフンのみを使用する必要があります。 名前の先頭と末尾には数字または文字を使用する必要があり、連続するハイフンを含めることはできません。 キャッシュ インスタンスの "*ホスト名*" は、 *\<DNS name>.redis.cache.windows.net* になります。 | 
   | **サブスクリプション** | ドロップダウンで、ご自身のサブスクリプションを選択します。 | この新しい Azure Cache for Redis インスタンスが作成されるサブスクリプション。 | 
   | **リソース グループ** | ドロップ ダウンでリソース グループを選択するか、 **[新規作成]** を選択し、新しいリソース グループの名前を入力します。 | その中にキャッシュやその他のリソースを作成するリソース グループの名前。 すべてのアプリ リソースを 1 つのリソース グループに配置することで、それらをまとめて簡単に管理または削除できます。 | 
   | **場所** | ドロップ ダウンで場所を選択します。 | キャッシュを使用する他のサービスの近くの[リージョン](https://azure.microsoft.com/regions/)を選択します。 |
   | **価格レベル** | ドロップ ダウンで[価格レベル](https://azure.microsoft.com/pricing/details/cache/)を選択します。 |  価格レベルによって、キャッシュに使用できるのサイズ、パフォーマンス、および機能が決まります。 詳細については、[Azure Cache for Redis の概要](cache-overview.md)に関するページを参照してください。 |

1. **[ネットワーク]** タブを選択するか、ページの下部にある **[ネットワーク]** ボタンをクリックします。

1. **[ネットワーク]** タブで、接続方法を選択します。

1. **[次へ: 詳細]** タブを選択するか、ページの下部にある **[次へ: 詳細]** ボタンをクリックします。

1. Basic または Standard のキャッシュ インスタンスの **[詳細]** タブで、非 TLS ポートを有効にする場合は有効トグルをオンにします。

1. Premium キャッシュ インスタンスの **[詳細]** タブで、非 TLS ポート、クラスタリング、データ永続化の設定を構成します。

1. **[次へ: タグ]** タブを選択するか、ページの下部にある **[次へ: タグ]** ボタンをクリックします。

1. 必要に応じて、 **[タグ]** タブで、リソースを分類する場合は名前と値を入力します。 

1. **[Review + create]\(レビュー + 作成\)** を選択します。 [確認および作成] タブが表示され、Azure によって構成が検証されます。

1. 緑色の検証に成功のメッセージが表示された後、 **[作成]** を選択します。

キャッシュが作成されるまで、しばらく時間がかかります。 Azure Cache for Redis の **[概要]** ページで進行状況を監視できます。 **[状態]** に "**実行中**" と表示されている場合は、キャッシュを使用する準備ができています。 

## <a name="prepare-for-deployment"></a>展開を準備する

デプロイを行う前に、モデルを Web サービスとして実行するために必要なものを定義する必要があります。 次の一覧で、デプロイするために必要となる中心的な項目について説明します。

* __エントリ スクリプト__。 このスクリプトは、要求を受け入れ、モデルを使用してその要求にスコアを付け、その結果を返します。

    > [!IMPORTANT]
    > エントリ スクリプトはモデルに固有のものです。受信要求データの形式、モデルで想定されるデータの形式、およびクライアントに返されるデータの形式を理解しておく必要があります。
    >
    > 要求データがモデルで使用できない形式になっている場合、スクリプトで受け入れ可能な形式に変換することができます。 また、応答をクライアントに返す前に変換することもできます。
    >
    > 既定では、関数のパッケージ化の際、入力はテキストとして扱われます。 入力の生バイトの使用に関心がある場合 (たとえば、BLOB トリガーの場合)、[生データを受け入れる AMLRequest](../machine-learning/how-to-deploy-advanced-entry-script.md#binary-data) を使用する必要があります。

run 関数の場合は、それを Redis エンドポイントに確実に接続します。

```python
import json
import numpy as np
import os
import redis
from sklearn.externals import joblib

def init():
    global model
    global azrediscache
    azrediscache = redis.StrictRedis(host='<host_url>', port=6380, password="<access_key>", ssl=True)
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    model = joblib.load(model_path)

@input_schema('data', NumpyParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        input = azrediscache.get(data)
        result = model.predict(input)
        data = np.array(json.loads(data))
        result = model.predict(data)
        # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

エントリ スクリプトの詳細については、[スコアリング コードの定義](../machine-learning/how-to-deploy-and-where.md?tabs=python#define-an-entry-script)に関する記事を参照してください。

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
>                                                            pip_packages=['azureml-defaults', 'redis'])
> inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
> ```

環境の詳細については、[トレーニングとデプロイのための環境の作成と管理](../machine-learning/how-to-use-environments.md)に関する記事を参照してください。

推論構成の詳細については、「[Azure Machine Learning を使用してモデルをデプロイする](../machine-learning/how-to-deploy-and-where.md?tabs=python#define-an-inference-configuration)」を参照してください。

> [!IMPORTANT]
> Functions にデプロイするときに __デプロイ構成__ を作成する必要はありません。

## <a name="install-the-sdk-preview-package-for-functions-support"></a>Functions のサポート用に SDK プレビュー パッケージをインストールする

Azure Functions 用のパッケージをビルドするには、SDK プレビュー パッケージをインストールする必要があります。

```bash
pip install azureml-contrib-functions
```

## <a name="create-the-image"></a>イメージの作成

Azure Functions にデプロイする Docker イメージを作成するには、[azureml.contrib.functions.package](/python/api/azureml-contrib-functions/azureml.contrib.functions) または使用するトリガーに固有のパッケージ関数を使用します。 次のコード スニペットで、モデルおよび推論構成から、HTTP トリガーを使用する新しいパッケージを作成する方法を示します。

> [!NOTE]
> このコード スニペットは、`model` に登録済みのモデルが含まれており、`inference_config` に推論環境の構成が含まれていることを前提としています。 詳細については、「[Azure Machine Learning を使用してモデルをデプロイする](../machine-learning/how-to-deploy-and-where.md)」を参照してください。

```python
from azureml.contrib.functions import package
from azureml.contrib.functions import HTTP_TRIGGER
model_package = package(ws, [model], inference_config, functions_enabled=True, trigger=HTTP_TRIGGER)
model_package.wait_for_creation(show_output=True)
# Display the package location/ACR path
print(model_package.location)
```

`show_output=True` の場合、Docker ビルド プロセスの出力が表示されます。 プロセスが完了すると、ワークスペース用の Azure Container Registry 内にイメージが作成されます。 イメージがビルドされると、Azure Container Registry 内の場所が表示されます。 返される場所は、`<acrinstance>.azurecr.io/package@sha256:<imagename>` の形式です。

> [!NOTE]
> 現在、Functions のパッケージ化では、HTTP トリガー、BLOB トリガー、および Service Bus トリガーがサポートされています。 トリガーの詳細については、[Azure Functions のバインド](../azure-functions/functions-bindings-storage-blob-trigger.md#blob-name-patterns)に関する記事をご覧ください。

> [!IMPORTANT]
> イメージをデプロイするときに使用されるため、場所情報を保存します。

## <a name="deploy-image-as-a-web-app"></a>イメージを Web アプリとしてデプロイする

1. 次のコマンドを使用して、イメージを含む Azure Container Registry のログイン資格情報を取得します。 `<myacr>` を、以前 `package.location` から返された値に置き換えます。 

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
    > この時点で、関数アプリが作成されています。 しかし、イメージを含む Azure Container Registry に資格情報または HTTP トリガーの接続文字列が指定されていないため、関数アプリはアクティブになりません。 次の手順で、コンテナー レジストリの認証情報と接続文字列を指定します。 

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
> イメージが読み込まれるまで数分かかる場合があります。 Azure portal を使用して進行状況を監視できます。

## <a name="test-azure-functions-http-trigger"></a>Azure Functions の HTTP トリガーをテストする 

ここでは、Azure Functions の HTTP トリガーを実行してテストします。

1. Azure portal で関数アプリにアクセスします。
1. Developer で、 **[コードとテスト]** を選択します。 
1. 右側にある **[入力]** タブを選択します。 
1. **[実行]** ボタンをクリックして、Azure Functions の HTTP トリガーをテストします。 

これで、Azure Cache for Redis インスタンスを使用して、Azure Machine Learning からのモデルを関数アプリとして正常にデプロイできました。 Azure Cache for Redis の詳細については、以下のセクションのリンクを参照してください。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

次のチュートリアルに進む場合は、このクイック スタートで作成したリソースを維持して、再利用することができます。

そうではなく、クイック スタートの使用を終える場合は、課金を避けるために、このクイック スタートで作成した Azure リソースを削除することができます。 

> [!IMPORTANT]
> リソース グループを削除すると、元に戻すことができません。 リソース グループを削除すると、そのリソース グループ内のすべてのリソースは完全に削除されます。 間違ったリソース グループやリソースをうっかり削除しないようにしてください。 このサンプルのホストとなるリソースを、保持するリソースが含まれている既存のリソース グループ内に作成した場合は、リソース グループを削除するのではなく、個々のブレードから各リソースを個別に削除することができます。

### <a name="to-delete-a-resource-group"></a>リソース グループを削除するには

1. [Azure portal](https://portal.azure.com) にサインインし、 **[リソース グループ]** を選択します。

2. **[名前でフィルター]** ボックスにリソース グループの名前を入力します。 リソース グループの結果一覧で、**[...]** を選択し、**[リソース グループの削除]** を選択します。

リソース グループの削除の確認を求めるメッセージが表示されます。 確認のためにリソース グループの名前を入力し、**[削除]** を選択します。

しばらくすると、リソース グループとそのリソースのすべてが削除されます。

## <a name="next-steps"></a>次のステップ 

* [Azure Cache for Redis](./cache-overview.md) について詳細を学習する
* [Functions](../azure-functions/functions-create-function-linux-custom-image.md) のドキュメントで、関数アプリを構成する方法を学習する。
* [API リファレンス](/python/api/azureml-contrib-functions/azureml.contrib.functions) 
* [Azure Cache for Redis を使用する Python アプリ](./cache-python-get-started.md)を作成する
