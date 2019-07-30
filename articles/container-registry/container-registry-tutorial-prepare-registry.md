---
title: チュートリアル - Azure に geo レプリケートされている Docker レジストリを作成する
description: Azure Container Registry を作成し、geo レプリケーションを構成して、Docker イメージを準備し、それをレジストリにデプロイします。 3 部構成のシリーズのパート 1。
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: tutorial
ms.date: 04/30/2017
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: 87746bd39e624699612bf5221258ad757cd462b3
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/26/2019
ms.locfileid: "68309585"
---
# <a name="tutorial-prepare-a-geo-replicated-azure-container-registry"></a>チュートリアル:geo レプリケーション Azure Container Registry の準備

Azure Container Registry は、デプロイの近くにネットワークを確保できる Azure にデプロイされたプライベート Docker レジストリです。 この一連の 3 つのチュートリアルの記事では、geo レプリケーションを使用して、Linux コンテナーで実行する ASP.NET Core Web アプリケーションを、2 つの [Web Apps for Containers](../app-service/containers/index.yml) インスタンスにデプロイする方法について説明します。 Azure が最も近い geo レプリケーション レポジトリからの各 Web アプリ インスタンスにイメージをデプロイする方法を確認します。

このチュートリアルの 3 部構成のシリーズのパート 1 では、次の作業を行います。

> [!div class="checklist"]
> * geo レプリケーション Azure Container Registry を作成する
> * GitHub からアプリケーション ソース コードを複製する
> * アプリケーション ソースから Docker コンテナー イメージを構築する
> * レジストリにコンテナー イメージをプッシュする

以降のチュートリアルでは、コンテナーをプライベート レジストリから 2 つの Azure リージョンで実行している Web アプリにデプロイします。 次に、アプリケーションのコードを更新し、レジストリへの単一の `docker push` で、両方の Web アプリ インスタンスを更新します。

## <a name="before-you-begin"></a>開始する前に

このチュートリアルには、Azure CLI (バージョン 2.0.31 以降) のローカル インストールが必要です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。

前提として、コンテナー、コンテナー イメージ、基本 Docker CLI コマンドなど、Docker のコア概念を基本的に理解している必要があります。 [Docker の入門]( https://docs.docker.com/get-started/)に関するドキュメントでコンテナーの基礎についての入門情報を参照してください。

このチュートリアルを完了するには、Docker のローカル インストールが必要です。 Docker では、[macOS](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/)、および [Linux](https://docs.docker.com/engine/installation/#supported-platforms) システム向けのインストールの指示を提供しています。

Azure Cloud Shell には、このチュートリアルの各ステップを完了するのに必要な Docker コンポーネントがすべて含まれているわけではありません。 そのため、Azure CLI および Docker 開発環境のローカル インストールをお勧めします。

## <a name="create-a-container-registry"></a>コンテナー レジストリの作成

[Azure Portal](https://portal.azure.com) にサインインします。

**[リソースの作成]**  >  **[コンテナー]**  >  **[Azure Container Registry]** の順に選択します。

![Azure Portal でコンテナー レジストリを作成する][tut-portal-01]

次の設定で、新しいレジストリを構成します。

* **レジストリ名**:Azure 内でグローバルに一意で、5 - 50 文字の英数字を含むレジストリ名を作成します
* **リソース グループ**:**新規作成** > `myResourceGroup`
* **場所**: `West US`
* **管理者ユーザー**: `Enable` (イメージをプルするために、Web App for Containers に必要)
* **SKU**: `Premium` (geo レプリケーションに必要)

**[作成]** を選択して ACR インスタンスをデプロイします。

![Azure Portal でコンテナー レジストリを作成する][tut-portal-02]

このチュートリアルの残りの部分では、選択したコンテナー **レジストリ名**のプレースホルダーとして `<acrName>` を使用します。

> [!TIP]
> Azure Container Registry は通常、複数のコンテナー ホスト間で使用される有効期間が長いリソースであるため、独自のリソース グループにレジストリを作成することをお勧めします。 geo レプリケーション レジストリと webhook を構成すると、これらの追加リソースが同じリソース グループに配置されます。
>

## <a name="configure-geo-replication"></a>geo レプリケーションの構成

Premium レジストリを使用できるようになったので、geo レプリケーションを構成できます。 そうすると、次のチュートリアルで 2 つのリージョンで実行するように構成する Web アプリが、最も近いレジストリからそのコンテナー イメージをプルできるようになります。

Azure Portal で新しいコンテナー レジストリに移動し、 **[SERVICES]** (サービス) の下の **[レプリケーション]** を選択します。

![Azure Portal のコンテナー レジストリ UI の [レプリケーション]][tut-portal-03]

マップに、geo レプリケーションで使用できる Azure リージョンを表す緑色の六角形が表示されます。

 ![Azure Portal のリージョン マップ][tut-map-01]

その緑色の六角形を選択して、米国東部リージョンにレジストリをレプリケートし、 **[レプリケーションの作成]** で **[作成]** を選択します。

 ![Azure Portal の [レプリケーションの作成] UI][tut-portal-04]

レプリケーションが完了すると、ポータルで、両方のリージョンに対して*準備完了*が示されます。 **[更新]** ボタンを使用して、レプリケーションの状態を更新します。レプリカが作成され、同期されるまで、1 分ほどかかる可能性があります。

![Azure Portal の [レプリケーションの状態] UI][tut-portal-05]

## <a name="container-registry-login"></a>Container Registry のログイン

geo レプリケーションを構成したので、コンテナー イメージを構築し、レジストリにプッシュします。 イメージをプッシュする前に、まず ACR インスタンスにログインする必要があります。

[az acr login](https://docs.microsoft.com/cli/azure/acr#az-acr-login) コマンドを使用して、レジストリの資格情報を認証し、キャッシュします。 `<acrName>` を、前で作成したレジストリの名前に置き換えます。

```azurecli
az acr login --name <acrName>
```

このコマンドは、完了すると、`Login Succeeded` を返します。

## <a name="get-application-code"></a>アプリケーションのコードを入手する

このチュートリアルのサンプルには、[ASP.NET Core][aspnet-core] で構築した小さな Web アプリケーションが含まれます。 アプリケーションは、Azure Container Registry によってイメージがデプロイされたリージョンを表示する HTML ページを提供します。

![ブラウザーに表示されたチュートリアル アプリ][tut-app-01]

Git を使用して、サンプルをローカル ディレクトリにダウンロードし、`cd` でそのディレクトリに変更します。

```bash
git clone https://github.com/Azure-Samples/acr-helloworld.git
cd acr-helloworld
```

`git` をインストールしていない場合、GitHub から直接 [ZIP アーカイブをダウンロード][acr-helloworld-zip]できます。

## <a name="update-dockerfile"></a>Dockerfile の更新

サンプルに含まれる Dockerfile は、コンテナーの構築方法を示しています。 公式の [aspnetcore][dockerhub-aspnetcore] イメージから開始し、アプリケーション ファイルをコンテナーにコピーし、依存関係をインストールして、公式の [aspnetcore-build][dockerhub-aspnetcore-build]イメージを使用して出力をコンパイルし、最後に、最適化された aspnetcore イメージを構築します。

[Dockerfile][dockerfile] は複製されたソース内の `./AcrHelloworld/Dockerfile` にあります。

```Dockerfile
FROM microsoft/aspnetcore:2.0 AS base
# Update <acrName> with the name of your registry
# Example: uniqueregistryname.azurecr.io
ENV DOCKER_REGISTRY <acrName>.azurecr.io
WORKDIR /app
EXPOSE 80

FROM microsoft/aspnetcore-build:2.0 AS build
WORKDIR /src
COPY *.sln ./
COPY AcrHelloworld/AcrHelloworld.csproj AcrHelloworld/
RUN dotnet restore
COPY . .
WORKDIR /src/AcrHelloworld
RUN dotnet build -c Release -o /app

FROM build AS publish
RUN dotnet publish -c Release -o /app

FROM base AS production
WORKDIR /app
COPY --from=publish /app .
ENTRYPOINT ["dotnet", "AcrHelloworld.dll"]
```

*acr-helloworld* イメージ内のアプリケーションは、DNS で、レジストリのログイン サーバーに関する情報をクエリすることによって、コンテナーのデプロイ元のリージョンを判断しようとします。 Dockerfile 内の `DOCKER_REGISTRY` 環境変数に、レジストリのログイン サーバーの完全修飾ドメイン名 (FQDN) を指定する必要があります。

まず、`az acr show` コマンドでレジストリのログイン サーバーを取得します。 `<acrName>` を、前の手順で作成したレジストリの名前に置き換えます。

```azurecli
az acr show --name <acrName> --query "{acrLoginServer:loginServer}" --output table
```

出力:

```bash
AcrLoginServer
-----------------------------
uniqueregistryname.azurecr.io
```

次に、`ENV DOCKER_REGISTRY` 行をレジストリのログイン サーバーの FQDN で更新します。 この例は、サンプルのレジストリ名 *uniqueregistryname* を反映しています。

```Dockerfile
ENV DOCKER_REGISTRY uniqueregistryname.azurecr.io
```

## <a name="build-container-image"></a>コンテナー イメージの構築

レジストリ ログイン サーバーの FQDN で Dockerfile を更新したので、`docker build` を使用して、コンテナー イメージを作成できます。 次のコマンドを実行してイメージを作成し、プライベート レジストリの URL でそれにタグ付けして、再度 `<acrName>` をレジストリの名前に置き換えます。

```bash
docker build . -f ./AcrHelloworld/Dockerfile -t <acrName>.azurecr.io/acr-helloworld:v1
```

Docker イメージが構築されると、複数行の出力が表示されます (ここでは切り詰めて表示しています)。

```bash
Sending build context to Docker daemon  523.8kB
Step 1/18 : FROM microsoft/aspnetcore:2.0 AS base
2.0: Pulling from microsoft/aspnetcore
3e17c6eae66c: Pulling fs layer

[...]

Step 18/18 : ENTRYPOINT dotnet AcrHelloworld.dll
 ---> Running in 6906d98c47a1
 ---> c9ca1763cfb1
Removing intermediate container 6906d98c47a1
Successfully built c9ca1763cfb1
Successfully tagged uniqueregistryname.azurecr.io/acr-helloworld:v1
```

`docker images` を使用して、ビルドされ、タグが付けられたイメージを表示します。

```console
$ docker images
REPOSITORY                                      TAG    IMAGE ID        CREATED               SIZE
uniqueregistryname.azurecr.io/acr-helloworld    v1     01ac48d5c8cf    About a minute ago    284MB
[...]
```

## <a name="push-image-to-azure-container-registry"></a>Azure Container Registry へのイメージのプッシュ

次に、`docker push` コマンドを使用して、*acr-helloworld* イメージをレジストリにプッシュします。 `<acrName>` をレジストリの名前に置き換えます。

```bash
docker push <acrName>.azurecr.io/acr-helloworld:v1
```

geo レプリケーション用にレジストリを構成したため、この単一の `docker push` コマンドで、イメージが*米国西部*と*米国東部*リージョンの両方に自動的にレプリケートされます。

```console
$ docker push uniqueregistryname.azurecr.io/acr-helloworld:v1
The push refers to a repository [uniqueregistryname.azurecr.io/acr-helloworld]
cd54739c444b: Pushed
d6803756744a: Pushed
b7b1f3a15779: Pushed
a89567dff12d: Pushed
59c7b561ff56: Pushed
9a2f9413d9e4: Pushed
a75caa09eb1f: Pushed
v1: digest: sha256:0799014f91384bda5b87591170b1242bcd719f07a03d1f9a1ddbae72b3543970 size: 1792
```

## <a name="next-steps"></a>次の手順

このチュートリアルでは、プライベートの geo レプリケーション コンテナー レジストリを作成し、コンテナー イメージを構築して、そのイメージをレジストリにプッシュしました。

次のチュートリアルに進み、geo レプリケーションを使用して、イメージをローカルで使用するために、コンテナーを複数の Web Apps for Containers インスタンスにデプロイします。

> [!div class="nextstepaction"]
> [Azure Container Registry からの Web アプリのデプロイ](container-registry-tutorial-deploy-app.md)

<!-- IMAGES -->
[tut-portal-01]: ./media/container-registry-tutorial-prepare-registry/tut-portal-01.png
[tut-portal-02]: ./media/container-registry-tutorial-prepare-registry/tut-portal-02.png
[tut-portal-03]: ./media/container-registry-tutorial-prepare-registry/tut-portal-03.png
[tut-portal-04]: ./media/container-registry-tutorial-prepare-registry/tut-portal-04.png
[tut-portal-05]: ./media/container-registry-tutorial-prepare-registry/tut-portal-05.png
[tut-app-01]: ./media/container-registry-tutorial-prepare-registry/tut-app-01.png
[tut-map-01]: ./media/container-registry-tutorial-prepare-registry/tut-map-01.png

<!-- LINKS - External -->
[acr-helloworld-zip]: https://github.com/Azure-Samples/acr-helloworld/archive/master.zip
[aspnet-core]: https://dot.net
[dockerhub-aspnetcore]: https://hub.docker.com/r/microsoft/aspnetcore/
[dockerhub-aspnetcore-build]: https://store.docker.com/community/images/microsoft/aspnetcore-build
[dockerfile]: https://github.com/Azure-Samples/acr-helloworld/blob/master/AcrHelloworld/Dockerfile