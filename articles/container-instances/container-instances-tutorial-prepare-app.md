---
title: "Azure Container Instances チュートリアル - アプリの準備 | Azure Docs"
description: "Azure Container Instances にデプロイするアプリを準備します"
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/01/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: ca4cd00b3e9e58fd1137b896e7aac96549bf6d05
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="create-container-for-deployment-to-azure-container-instances"></a>デプロイするコンテナーを Azure Container Instances に作成する

Azure Container Instances では、仮想マシンをプロビジョニングしたり、上位レベルのサービスを採用したりせずに、Azure のインフラストラクチャ上に Docker コンテナーをデプロイすることができます。 このチュートリアルでは、Node.js で単純な Web アプリケーションを構築し、Azure Container Instances を使用して実行できるコンテナーでそのアプリケーションをパッケージ化します。 以下の内容を説明します。

> [!div class="checklist"]
> * GitHub からアプリケーション ソースを複製する  
> * アプリケーション ソースからコンテナー イメージを作成する
> * ローカル Docker 環境でイメージをテストする

後続のチュートリアルでは、Azure Container Registry にイメージをアップロードし、Azure Container Instances にデプロイします。

## <a name="before-you-begin"></a>開始する前に

このチュートリアルの前提として、コンテナー、コンテナー イメージ、基本 Docker コマンドなど、Docker のコア概念を基本的に理解している必要があります。 必要な場合は、[Docker の入門]( https://docs.docker.com/get-started/)に関するドキュメントでコンテナーの基礎を参照してください。 

このチュートリアルを完了するには、Docker 開発環境が必要です。 Docker では、[Mac](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/)、または [Linux](https://docs.docker.com/engine/installation/#supported-platforms) システムで Docker を簡単に構成できるパッケージが提供されています。

Azure Cloud Shell には、このチュートリアルの各ステップを完了するのに必要な Docker コンポーネントがすべて含まれているわけではありません。 そのため、完全な Docker 開発環境の使用をお勧めします。

## <a name="get-application-code"></a>アプリケーションのコードを入手する

このチュートリアルのサンプルには、[Node.js](http://nodejs.org) で構築した単純な Web アプリケーションが含まれます。 このアプリは、次のような静的な HTML ページとして機能します。

![ブラウザーに表示されたチュートリアル アプリ][aci-tutorial-app]

GitHub を使用してサンプルをダウンロードします。

```bash
git clone https://github.com/Azure-Samples/aci-helloworld.git
```

## <a name="build-the-container-image"></a>コンテナー イメージを構築する

サンプル リポジトリで提供される Dockerfile は、コンテナーの構築方法を示しています。 その方法は、[Alpine Linux](https://alpinelinux.org/) に基づく[公式 Node.js イメージ][dockerhub-nodeimage]から始まります。これは、コンテナーで使用するのに適した小規模なディストリビューションです。 次に、アプリケーション ファイルをコンテナーにコピーし、ノード パッケージ マネージャーを使用して依存関係をインストールして、最後にアプリケーションを起動します。

```
FROM node:8.2.0-alpine
RUN mkdir -p /usr/src/app
COPY ./app/* /usr/src/app/
WORKDIR /usr/src/app
RUN npm install
CMD node /usr/src/app/index.js
```

`docker build` コマンドを使用してコンテナー イメージを作成し、これを *aci-tutorial-app* としてタグ付けします。

```bash
docker build ./aci-helloworld -t aci-tutorial-app
```

`docker images` を使用して、構築されたイメージを参照します。

```bash
docker images
```

出力:

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
aci-tutorial-app             latest              5c745774dfa9        39 seconds ago       68.1 MB
```

## <a name="run-the-container-locally"></a>コンテナーをローカルで実行する

Azure Container Instances にコンテナーをデプロイする前に、ローカルでコンテナーを実行して、コンテナーが動作することを確認します。 `-d` スイッチを使用すると、コンテナーをバックグラウンドで実行できます。一方、`-p` スイッチでは、コンピューター上の任意のポートをコンテナーのポート 80 にマップすることができます。

```bash
docker run -d -p 8080:80 aci-tutorial-app
```

ブラウザーで http://localhost:8080 を開き、コンテナーが実行されていることを確認します。

![ブラウザーでのアプリのローカル実行][aci-tutorial-app-local]

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure Container Instances にデプロイできるコンテナー イメージを作成しました。 次の手順を完了しました。

> [!div class="checklist"]
> * GitHub からアプリケーション ソースを複製する  
> * アプリケーション ソースからコンテナー イメージを作成する
> * コンテナーをローカルでテストする

次のチュートリアルでは、Azure Container Registry にコンテナー イメージを格納する方法について学習します。

> [!div class="nextstepaction"]
> [Azure Container Registry にイメージをプッシュする](./container-instances-tutorial-prepare-acr.md)

<!-- LINKS -->
[dockerhub-nodeimage]: https://hub.docker.com/r/library/node/tags/8.2.0-alpine/

<!--- IMAGES --->
[aci-tutorial-app]:./media/container-instances-quickstart/aci-app-browser.png
[aci-tutorial-app-local]: ./media/container-instances-tutorial-prepare-app/aci-app-browser-local.png