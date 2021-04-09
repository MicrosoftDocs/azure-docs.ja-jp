---
title: モデルのパッケージ化
titleSuffix: Azure Machine Learning
description: モデルをパッケージ化します。 モデルは、後でダウンロードできる docker イメージとしてパッケージ化することも、Dockerfile を作成し、それを使用してイメージをビルドすることもできます。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 07/31/2020
ms.topic: conceptual
ms.reviewer: larryfr
ms.custom: deploy
ms.openlocfilehash: 299eb316b534ddc9d5eee934cc15eae841276038
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "93312627"
---
# <a name="how-to-package-a-registered-model-with-docker"></a>Docker を使用して登録済みモデルをパッケージ化する方法

この記事では、登録済みの Azure Machine Learning モデルを Docker を使用してパッケージ化する方法について説明します。

## <a name="prerequisites"></a>前提条件

この記事は、Machine Learning ワークスペースでのモデルのトレーニングと登録が済んでいることを前提としています。 scikit-learn モデルのトレーニングと登録方法については、[こちらのチュートリアル](how-to-train-scikit-learn.md)を参照してください。


## <a name="package-models"></a>モデルのパッケージ化

場合によっては、モデルをデプロイせずに Docker イメージを作成することが必要になることがあります (たとえば、[Azure App Service にデプロイする](how-to-deploy-app-service.md)予定がある場合)。 または、イメージをダウンロードして、ローカルの Docker インストールで実行することが必要な場合もあります。 さらに、イメージのビルドに使用するファイルをダウンロードし、それらを検査および変更して、手動でビルドすることが必要な場合もあります。

モデルのパッケージ化により、これらを行うことができます。 モデルを Web サービスとしてホストするために必要なすべてのアセットをパッケージ化し、完全にビルドされた Docker イメージ、またはイメージのビルドに必要なファイルのいずれかをダウンロードできます。 モデルのパッケージ化を使用するには、次の 2 つの方法があります。

**パッケージ化されたモデルをダウンロードする:** モデルと、それを Web サービスとしてホストするために必要なその他のファイルを含む Docker イメージをダウンロードします。

**Dockerfile を生成する:** Docker イメージをビルドするために必要な Dockerfile、モデル、エントリ スクリプト、およびその他のアセットをダウンロードします。 その後、イメージをローカルでビルドする前に、それらのファイルを検査するか、変更を加えることができます。

どちらのパッケージも、ローカルの Docker イメージを取得するために使用できます。

> [!TIP]
> パッケージの作成は、モデルのデプロイに似ています。 登録済みのモデルと推論構成を使用します。

> [!IMPORTANT]
> 完全にビルドされたイメージをダウンロードするか、ローカルでイメージをビルドするには、開発環境に [Docker](https://www.docker.com) をインストールしておく必要があります。

### <a name="download-a-packaged-model"></a>パッケージ化されたモデルをダウンロードする

次の例では、ワークスペースの Azure Container Registry に登録されているイメージをビルドします。

```python
package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
```

パッケージを作成した後、`package.pull()` を使用して、ローカルの Docker 環境にイメージをプルできます。 このコマンドの出力には、イメージの名前が表示されます。 次に例を示します。 

`Status: Downloaded newer image for myworkspacef78fd10.azurecr.io/package:20190822181338`. 

モデルをダウンロードした後、`docker images` コマンドを使用してローカル イメージを一覧表示します。

```text
REPOSITORY                               TAG                 IMAGE ID            CREATED             SIZE
myworkspacef78fd10.azurecr.io/package    20190822181338      7ff48015d5bd        4 minutes ago       1.43 GB
```

このイメージに基づいてローカル コンテナーを開始するには、次のコマンドを使用して、シェルまたはコマンド ラインから名前付きコンテナーを開始します。 `<imageid>` 値を、`docker images` コマンドから返されたイメージ ID に置き換えます。

```bash
docker run -p 6789:5001 --name mycontainer <imageid>
```

このコマンドは、`myimage` という名前のイメージの最新バージョンを起動します。 これはローカル ポート 6789 を、Web サービスがリッスンしているコンテナー内のポート (5001) にマップします。 また、コンテナーに `mycontainer` という名前を割り当てます。これにより、コンテナーの停止が簡単になります。 コンテナーが開始したら、`http://localhost:6789/score` に要求を送信できます。

### <a name="generate-a-dockerfile-and-dependencies"></a>Dockerfile および依存関係を生成する

次の例では、イメージをローカルでビルドするために必要な Dockerfile、モデル、およびその他のアセットをダウンロードする方法を示します。 `generate_dockerfile=True` パラメーターは、完全にビルドされたイメージではなく、それらのファイルが必要であることを示します。

```python
package = Model.package(ws, [model], inference_config, generate_dockerfile=True)
package.wait_for_creation(show_output=True)
# Download the package.
package.save("./imagefiles")
# Get the Azure container registry that the model/Dockerfile uses.
acr=package.get_container_registry()
print("Address:", acr.address)
print("Username:", acr.username)
print("Password:", acr.password)
```

このコードは、イメージをビルドするために必要なファイルを `imagefiles` ディレクトリにダウンロードします。 保存ファイルに含まれている Dockerfile は、Azure Container Registry に格納されている基本イメージを参照します。 ローカルの Docker インストールでイメージをビルドする場合、アドレス、ユーザー名、およびパスワードを使用して、このレジストリに対して認証を行う必要があります。 ローカルの Docker インストールを使用してイメージをビルドするには、次の手順に従います。

1. シェルまたはコマンド ライン セッションから、次のコマンドを使用して Azure Container Registry で Docker を認証します。 `<address>`、`<username>`、および `<password>` を、`package.get_container_registry()` によって取得した値に置き換えます。

    ```bash
    docker login <address> -u <username> -p <password>
    ```

2. イメージをビルドするには、次のコマンドを使用します。 `<imagefiles>` を、`package.save()` でファイルを保存したディレクトリへのパスに置き換えます。

    ```bash
    docker build --tag myimage <imagefiles>
    ```

    このコマンドは、イメージ名を `myimage` に設定します。

イメージがビルドされたことを確認するには、`docker images` コマンドを使用します。 `myimage` イメージが一覧に表示されます。

```text
REPOSITORY      TAG                 IMAGE ID            CREATED             SIZE
<none>          <none>              2d5ee0bf3b3b        49 seconds ago      1.43 GB
myimage         latest              739f22498d64        3 minutes ago       1.43 GB
```

このイメージに基づいて新しいコンテナーを開始するには、次のコマンドを使用します。

```bash
docker run -p 6789:5001 --name mycontainer myimage:latest
```

このコマンドは、`myimage` という名前のイメージの最新バージョンを起動します。 これはローカル ポート 6789 を、Web サービスがリッスンしているコンテナー内のポート (5001) にマップします。 また、コンテナーに `mycontainer` という名前を割り当てます。これにより、コンテナーの停止が簡単になります。 コンテナーが開始したら、`http://localhost:6789/score` に要求を送信できます。

### <a name="example-client-to-test-the-local-container"></a>ローカル コンテナーをテストするクライアントの例

次のコードは、コンテナーで使用できる Python クライアントの例です。

```python
import requests
import json

# URL for the web service.
scoring_uri = 'http://localhost:6789/score'

# Two sets of data to score, so we get two results back.
data = {"data":
        [
            [ 1,2,3,4,5,6,7,8,9,10 ],
            [ 10,9,8,7,6,5,4,3,2,1 ]
        ]
        }
# Convert to JSON string.
input_data = json.dumps(data)

# Set the content type.
headers = {'Content-Type': 'application/json'}

# Make the request and display the response.
resp = requests.post(scoring_uri, input_data, headers=headers)
print(resp.text)
```

他のプログラミング言語でのクライアントの例については、[Web サービスとしてデプロイされたモデルの使用](how-to-consume-web-service.md)に関する記事を参照してください。

### <a name="stop-the-docker-container"></a>Docker コンテナーを停止する

コンテナーを停止するには、別のシェルまたはコマンド ラインから次のコマンドを使用します。

```bash
docker kill mycontainer
```

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
