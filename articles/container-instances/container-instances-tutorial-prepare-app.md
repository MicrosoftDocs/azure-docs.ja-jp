---
title: チュートリアル - デプロイ用のコンテナー イメージを準備する
description: Azure Container Instances チュートリアル第 1 部 (全 3 部) - Azure Container Instances にデプロイするアプリをコンテナー イメージとして準備します
ms.topic: tutorial
ms.date: 03/21/2018
ms.custom: seodec18, mvc, devx-track-js
ms.openlocfilehash: 1eca34fb5da50b15f15322a3b534556bcd3bda09
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91303116"
---
# <a name="tutorial-create-a-container-image-for-deployment-to-azure-container-instances"></a>チュートリアル:Azure Container Instances へのデプロイに使用するコンテナー イメージを作成する

Azure Container Instances では、仮想マシンをプロビジョニングしたり、上位レベルのサービスを採用したりせずに、Azure のインフラストラクチャ上に Docker コンテナーをデプロイすることができます。 このチュートリアルでは、小さな Node.js Web アプリケーションを、Azure Container Instances を使用して実行できるコンテナー イメージにパッケージ化します。

シリーズの第 1 部となるこの記事では、次の内容を学習します。

> [!div class="checklist"]
> * GitHub からアプリケーション ソース コードを複製する
> * アプリケーション ソースからコンテナー イメージを作成する
> * ローカル Docker 環境でイメージをテストする

チュートリアルのパート2 およびパート 3 では、Azure Container Registry にイメージをアップロードした後、Azure Container Instances にデプロイします。

## <a name="before-you-begin"></a>開始する前に

[!INCLUDE [container-instances-tutorial-prerequisites](../../includes/container-instances-tutorial-prerequisites.md)]

## <a name="get-application-code"></a>アプリケーションのコードを入手する

このチュートリアルのサンプル アプリケーションは、[Node.js][nodejs] で構築した単純な Web アプリです。 このアプリケーションは、次のスクリーンショットに示したような静的 HTML ページを返します。

![ブラウザーに表示されたチュートリアル アプリ][aci-tutorial-app]

次のように、Git を使ってサンプル アプリケーションのリポジトリを複製します。

```bash
git clone https://github.com/Azure-Samples/aci-helloworld.git
```

GitHub から直接 [ZIP アーカイブ][aci-helloworld-zip]をダウンロードすることもできます。

## <a name="build-the-container-image"></a>コンテナー イメージを構築する

サンプル アプリケーションに含まれる Dockerfile は、コンテナーの構築方法を示しています。 その方法は、[Alpine Linux][alpine-linux] に基づく[公式 Node.js イメージ][docker-hub-nodeimage]から始まります。これは、コンテナーで使用するのに適した小規模なディストリビューションです。 次に、アプリケーション ファイルをコンテナーにコピーし、ノード パッケージ マネージャーを使用して依存関係をインストールして、最後にアプリケーションを起動します。

```Dockerfile
FROM node:8.9.3-alpine
RUN mkdir -p /usr/src/app
COPY ./app/ /usr/src/app/
WORKDIR /usr/src/app
RUN npm install
CMD node /usr/src/app/index.js
```

[docker build][docker-build] コマンドを使用してコンテナー イメージを作成し、これを *aci-tutorial-app* としてタグ付けします。

```bash
docker build ./aci-helloworld -t aci-tutorial-app
```

[docker build][docker-build] コマンドの出力は次のようになります (わかりやすくするために切り詰められています)。

```console
$ docker build ./aci-helloworld -t aci-tutorial-app
Sending build context to Docker daemon  119.3kB
Step 1/6 : FROM node:8.9.3-alpine
8.9.3-alpine: Pulling from library/node
88286f41530e: Pull complete
84f3a4bf8410: Pull complete
d0d9b2214720: Pull complete
Digest: sha256:c73277ccc763752b42bb2400d1aaecb4e3d32e3a9dbedd0e49885c71bea07354
Status: Downloaded newer image for node:8.9.3-alpine
 ---> 90f5ee24bee2
...
Step 6/6 : CMD node /usr/src/app/index.js
 ---> Running in f4a1ea099eec
 ---> 6edad76d09e9
Removing intermediate container f4a1ea099eec
Successfully built 6edad76d09e9
Successfully tagged aci-tutorial-app:latest
```

[docker images][docker-images] コマンドを使用して、構築されたイメージを確認します。

```bash
docker images
```

新しく構築したイメージがリストに表示されます。

```console
$ docker images
REPOSITORY          TAG       IMAGE ID        CREATED           SIZE
aci-tutorial-app    latest    5c745774dfa9    39 seconds ago    68.1 MB
```

## <a name="run-the-container-locally"></a>コンテナーをローカルで実行する

Azure Container Instances にコンテナーをデプロイする前に、[docker run][docker-run] を使ってローカルでコンテナーを実行し、コンテナーが動作することを確認します。 `-d` スイッチを使用すると、コンテナーをバックグラウンドで実行できます。一方、`-p` スイッチでは、コンピューター上の任意のポートをコンテナーのポート 80 にマップすることができます。

```bash
docker run -d -p 8080:80 aci-tutorial-app
```

`docker run` コマンドが成功した場合、コマンドからの出力に、実行中のコンテナーの ID が表示されます。

```console
$ docker run -d -p 8080:80 aci-tutorial-app
a2e3e4435db58ab0c664ce521854c2e1a1bda88c9cf2fcff46aedf48df86cccf
```

今度は、ブラウザーで `http://localhost:8080` に移動し、コンテナーが実行されていることを確認します。 次のような Web ページが表示されます。

![ブラウザーでのアプリのローカル実行][aci-tutorial-app-local]

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure Container Instances にデプロイできるコンテナー イメージを作成し、ローカルで実行されていることを確認しました。 ここで行った作業は次のとおりです。

> [!div class="checklist"]
> * GitHub からのアプリケーション ソースの複製
> * アプリケーション ソースからのコンテナー イメージの作成
> * コンテナーのローカルでのテスト

シリーズの次のチュートリアルに進んで、Azure Container Registry にコンテナー イメージを格納する方法について学習します。

> [!div class="nextstepaction"]
> [Azure Container Registry へのイメージのプッシュ](container-instances-tutorial-prepare-acr.md)

<!--- IMAGES --->
[aci-tutorial-app]:./media/container-instances-quickstart/aci-app-browser.png
[aci-tutorial-app-local]: ./media/container-instances-tutorial-prepare-app/aci-app-browser-local.png

<!-- LINKS - External -->
[aci-helloworld-zip]: https://github.com/Azure-Samples/aci-helloworld/archive/master.zip
[alpine-linux]: https://alpinelinux.org/
[docker-build]: https://docs.docker.com/engine/reference/commandline/build/
[docker-get-started]: https://docs.docker.com/get-started/
[docker-hub-nodeimage]: https://store.docker.com/images/node
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/
[nodejs]: https://nodejs.org

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
