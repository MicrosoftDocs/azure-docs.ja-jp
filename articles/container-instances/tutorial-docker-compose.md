---
title: チュートリアル - Docker Compose を使用して複数コンテナー グループをデプロイする
description: Docker Compose を使用して、複数コンテナーのアプリケーションをビルドして実行し、アプリケーションを Azure Container Instances にデプロイする
ms.topic: tutorial
ms.date: 10/28/2020
ms.custom: ''
ms.openlocfilehash: a71ff438feaef555a85c33d818c287c64621d40d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92913842"
---
# <a name="tutorial-deploy-a-multi-container-group-using-docker-compose"></a>チュートリアル:Docker Compose を使用して複数コンテナー グループをデプロイする 

このチュートリアルでは、[Docker Compose](https://docs.docker.com/compose/) を使用して複数コンテナー アプリケーションをローカルで定義して実行した後、Azure Container Instances に[コンテナー グループ](container-instances-container-groups.md)としてデプロイします。 

Docker を使用してクラウドネイティブ アプリを開発していて、ローカルでの開発からクラウドへのデプロイにシームレスに切り替えたい場合は、Azure Container Instances でコンテナーをオンデマンドで実行します。 この機能は、[Docker と Azure 間の統合](https://docs.docker.com/engine/context/aci-integration/)によって実現されます。 ネイティブ Docker コマンドを使用すると、Azure で [1 つのコンテナー インスタンス](quickstart-docker-cli.md)または複数コンテナー グループを実行できます。

> [!IMPORTANT]
> Azure Container Instances の一部の機能はサポートされていません。 [Docker ACI Integration](https://github.com/docker/aci-integration-beta) GitHub リポジトリでイシューを作成して、Docker と Azure の統合に関するフィードバックを提供してください。

> [!TIP]
> コンテナー、イメージ、コンテキストを開発、実行、管理するための統合エクスペリエンスとして、[Visual Studio Code 用の Docker 拡張機能](https://aka.ms/VSCodeDocker)を使用できます。

この記事では、次の内容について説明します。

> [!div class="checklist"]
> * Azure コンテナー レジストリを作成する
> * GitHub からアプリケーション ソース コードを複製する
> * Docker Compose を使用してイメージをビルドし、複数コンテナー アプリケーションをローカルで実行する
> * アプリケーション イメージをコンテナー レジストリにプッシュする
> * Docker 用の Azure コンテキストを作成する
> * Azure Container Instances にアプリケーションをデプロイする

## <a name="prerequisites"></a>前提条件

* **Azure CLI** - ローカル コンピューターに Azure CLI がインストールされている必要があります。 バージョン 2.10.1 以降をお勧めします。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

* **Docker Desktop** - [Windows](https://desktop.docker.com/win/edge/Docker%20Desktop%20Installer.exe) または [macOS](https://desktop.docker.com/mac/edge/Docker.dmg) で利用可能な、Docker Desktop バージョン 2.3.0.5 以降を使用する必要があります。 または、[Docker ACI Integration CLI for Linux](https://docs.docker.com/engine/context/aci-integration/#install-the-docker-aci-integration-cli-on-linux) をインストールします。

[!INCLUDE [container-instances-create-registry](../../includes/container-instances-create-registry.md)]

## <a name="get-application-code"></a>アプリケーションのコードを入手する

このチュートリアルで使うサンプル アプリケーションは、基本的な投票アプリです。 アプリケーションは、フロントエンド Web コンポーネントとバックエンド Redis インスタンスで構成されています。 Web コンポーネントは、カスタム コンテナー イメージにパッケージ化されています。 Redis インスタンスでは、Docker Hub から変更されていないイメージを使用します。

サンプル アプリケーションを開発環境に複製するには、[git](https://git-scm.com/downloads) を使用します。

```console
git clone https://github.com/Azure-Samples/azure-voting-app-redis.git
```

複製されたディレクトリに移動します。

```console
cd azure-voting-app-redis
```

ディレクトリ内にはアプリケーションのソース コードと、事前作成された Docker Compose ファイルである docker-compose.yaml があります。

## <a name="modify-docker-compose-file"></a>Docker Compose ファイルの変更

テキスト エディターで docker-compose.yaml を開きます。 このファイルでは、`azure-vote-back` および `azure-vote-front` サービスが構成されます。

```yml
version: '3'
services:
  azure-vote-back:
    image: mcr.microsoft.com/oss/bitnami/redis:6.0.8
    container_name: azure-vote-back
    environment:
      ALLOW_EMPTY_PASSWORD: "yes"
    ports:
        - "6379:6379"

  azure-vote-front:
    build: ./azure-vote
    image: mcr.microsoft.com/azuredocs/azure-vote-front:v1
    container_name: azure-vote-front
    environment:
      REDIS: azure-vote-back
    ports:
        - "8080:80"
```

`azure-vote-front` 構成で、次の 2 つの変更を行います。

1. `azure-vote-front` サービスの `image` プロパティを更新します。 イメージ名の前に、Azure コンテナー レジストリのログイン サーバー名である \<acrName\>.azurecr.io を付加します。 たとえば、レジストリに *myregistry* という名前が付けられている場合、ログイン サーバー名は *myregistry.azurecr.io* (すべて小文字) になり、イメージ プロパティは `myregistry.azurecr.io/azure-vote-front` となります。
1. `ports` マッピングを `80:80` に変更します。 ファイルを保存します。

更新されたファイルは、次のようになります。

```yml
version: '3'
services:
  azure-vote-back:
    image: mcr.microsoft.com/oss/bitnami/redis:6.0.8
    container_name: azure-vote-back
    environment:
      ALLOW_EMPTY_PASSWORD: "yes"
    ports:
        - "6379:6379"

  azure-vote-front:
    build: ./azure-vote
    image: myregistry.azurecr.io/azure-vote-front
    container_name: azure-vote-front
    environment:
      REDIS: azure-vote-back
    ports:
        - "80:80"
```

これらの置換を行うことによって、次の手順で作成する `azure-vote-front` イメージに Azure コンテナー レジストリ用のタグが付けられ、イメージをプルして Azure Container Instances で実行できるようになります。

> [!TIP]
> このシナリオでは、Azure コンテナー レジストリを使用する必要はありません。 たとえば、アプリケーション イメージをホストするために、Docker Hub でプライベート リポジトリを選択できます。 別のレジストリを選択した場合は、イメージのプロパティを適切に更新します。

## <a name="run-multi-container-application-locally"></a>複数コンテナー アプリケーションをローカルで実行する

[docker-compose up](https://docs.docker.com/compose/reference/up/) を実行すると、サンプル `docker-compose.yaml` ファイルを使用してコンテナー イメージをビルドし、Redis イメージをダウンロードして、アプリケーションを起動します。

```console
docker-compose up --build -d
```

完了したら、[docker images](https://docs.docker.com/engine/reference/commandline/images/) コマンドを使って、作成されたイメージを確認します。 3 つのイメージがダウンロードまたは作成されていることを確認してください。 `azure-vote-front` イメージには、`uwsgi-nginx-flask` イメージをベースとして使用するフロントエンド アプリケーションが含まれています。 `redis` イメージは、Redis インスタンスを起動するために使用されます。

```
$ docker images

REPOSITORY                                TAG        IMAGE ID            CREATED             SIZE
myregistry.azurecr.io/azure-vote-front    latest     9cc914e25834        40 seconds ago      944MB
mcr.microsoft.com/oss/bitnami/redis       6.0.8      3a54a920bb6c        4 weeks ago          103MB
tiangolo/uwsgi-nginx-flask                python3.6  788ca94b2313        9 months ago        9444MB
```

[docker ps](https://docs.docker.com/engine/reference/commandline/ps/) コマンドを実行して、実行中のコンテナーを確認します。

```
$ docker ps

CONTAINER ID        IMAGE                                      COMMAND                  CREATED             STATUS              PORTS                           NAMES
82411933e8f9        myregistry.azurecr.io/azure-vote-front     "/entrypoint.sh /sta…"   57 seconds ago      Up 30 seconds       443/tcp, 0.0.0.0:80->80/tcp   azure-vote-front
b62b47a7d313        mcr.microsoft.com/oss/bitnami/redis:6.0.8  "/opt/bitnami/script…"   57 seconds ago      Up 30 seconds       0.0.0.0:6379->6379/tcp          azure-vote-back
```

実行中のアプリケーションを表示するには、ローカルの Web ブラウザーで「 `http://localhost:80`」と入力します。 次の例で示すように、サンプル アプリケーションが読み込まれます。

:::image type="content" source="media/tutorial-docker-compose/azure-vote.png" alt-text="投票アプリの画像":::

ローカル アプリケーションの試行が終了したら、[docker-compose down](https://docs.docker.com/compose/reference/down/) を実行してアプリケーションを停止し、コンテナーを削除します。

```console
docker-compose down
```

## <a name="push-image-to-container-registry"></a>コンテナー レジストリにイメージをプッシュする

アプリケーションを Azure Container Instances にデプロイするには、`azure-vote-front` イメージをコンテナー レジストリにプッシュする必要があります。 [docker-compose push](https://docs.docker.com/compose/reference/push) を実行してイメージをプッシュします。

```console
docker-compose push
```

レジストリにプッシュされるまでに数分かかる場合があります。

イメージがレジストリに格納されたことを確認するには、[az acr repository show](/cli/azure/acr/repository#az-acr-repository-show) コマンドを実行します。

```azurecli
az acr repository show --name <acrName> --repository azure-vote-front
```

[!INCLUDE [container-instances-create-docker-context](../../includes/container-instances-create-docker-context.md)]

## <a name="deploy-application-to-azure-container-instances"></a>アプリケーションを Azure Container instances にデプロイする

次に、ACI コンテキストに変更します。 以降の Docker コマンドは、このコンテキストで実行されます。

```console
docker context use myacicontext
```

`docker compose up` を実行して、Azure Container Instances でアプリケーションを起動します。 `azure-vote-front` イメージがコンテナー レジストリからプルされ、コンテナー グループが Azure Container Instances に作成されます。

```console
docker compose up
```

> [!NOTE]
> ACI コンテキストで現在使用できる Docker Compose コマンドは `docker compose up` と `docker compose down` です。 これらのコマンドには、`docker` と `compose` の間にハイフンはありません。

しばらくすると、コンテナー グループがデプロイされます。 サンプル出力:

```
[+] Running 3/3
 ⠿ Group azurevotingappredis  Created                          3.6s
 ⠿ azure-vote-back            Done                             10.6s
 ⠿ azure-vote-front           Done                             10.6s
```

`docker ps` を実行して、実行中のコンテナーとコンテナー グループに割り当てられている IP アドレスを確認します。

```console
docker ps
```

サンプル出力:

```
CONTAINER ID                           IMAGE                                         COMMAND             STATUS              PORTS
azurevotingappredis_azure-vote-back    mcr.microsoft.com/oss/bitnami/redis:6.0.8                         Running             52.179.23.131:6379->6379/tcp
azurevotingappredis_azure-vote-front   myregistry.azurecr.io/azure-vote-front                            Running             52.179.23.131:80->80/tcp
```

クラウドで実行中のアプリケーションを表示するには、表示されている IP アドレスをローカル Web ブラウザーに入力します。 この例では、「`52.179.23.131`」と入力します。 次の例で示すように、サンプル アプリケーションが読み込まれます。

:::image type="content" source="media/tutorial-docker-compose/azure-vote-aci.png" alt-text="ACI での投票アプリの画像":::

フロントエンド コンテナーのログを表示するには、[docker logs](https://docs.docker.com/engine/reference/commandline/logs) コマンドを実行します。 次に例を示します。

```console
docker logs azurevotingappredis_azure-vote-front
```

また、Azure portal またはその他の Azure ツールを使用して、デプロイしたコンテナー グループのプロパティと状態を確認することもできます。

アプリケーションの試行が完了したら、`docker compose down` を使用してアプリケーションとコンテナーを停止します。

```console
docker compose down
```

このコマンドを実行すると、Azure Container Instances のコンテナー グループが削除されます。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Docker Compose を使用して、複数コンテナー アプリケーションのローカルでの実行から Azure Container Instances での実行に切り替えました。 以下の方法を学習しました。

> [!div class="checklist"]
> * Azure コンテナー レジストリを作成する
> * GitHub からアプリケーション ソース コードを複製する
> * Docker Compose を使用してイメージをビルドし、複数コンテナー アプリケーションをローカルで実行する
> * アプリケーション イメージをコンテナー レジストリにプッシュする
> * Docker 用の Azure コンテキストを作成する
> * Azure Container Instances にアプリケーションをデプロイする

また、コンテナー、イメージ、コンテキストを開発、実行、管理するための統合エクスペリエンスとして、[Visual Studio Code 用の Docker 拡張機能](https://aka.ms/VSCodeDocker)を使用することもできます。

さらに多くの Azure Container Instances の機能を利用する場合は、Azure ツールを使用して複数コンテナー グループを指定します。 たとえば、Azure CLI を [YAML ファイル](container-instances-multi-container-yaml.md)と共に使用して、または [Azure Resource Manager テンプレート](container-instances-multi-container-group.md)を使用してコンテナー グループをデプロイする方法のチュートリアルを参照してください。 