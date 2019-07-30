---
title: Kubernetes on Azure のチュートリアル - アプリケーションの準備
description: この Azure Kubernetes Service (AKS) チュートリアルでは、Docker Compose を使用して複数コンテナー アプリを準備およびビルドする方法を説明します。その後、AKS にデプロイすることができます。
services: container-service
author: mlearned
ms.service: container-service
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: mlearned
ms.custom: mvc
ms.openlocfilehash: 2b4ba646a037b2f5561dabf97dee14454c07c573
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67614220"
---
# <a name="tutorial-prepare-an-application-for-azure-kubernetes-service-aks"></a>チュートリアル:Azure Kubernetes Service (AKS) 用のアプリケーションの準備

7 つのパートのうちの 1 番目であるこのチュートリアルでは、複数コンテナー アプリケーションを Kubernetes で使用する準備をします。 Docker Compose などの既存の開発ツールは、アプリケーションをローカルでビルドしてテストするために使用されます。 学習内容は次のとおりです。

> [!div class="checklist"]
> * GitHub からサンプル アプリケーション ソースを複製する
> * サンプル アプリケーション ソースからコンテナー イメージを作成する
> * ローカル Docker 環境で複数コンテナー アプリケーションをテストする

完了後、次のアプリケーションがローカル開発環境で実行されます。

![Azure 上の Kubernetes クラスターの図](./media/container-service-tutorial-kubernetes-prepare-app/azure-vote.png)

追加のチュートリアルでは、このコンテナー イメージが Azure Container Registry にアップロードされ、AKS クラスターにデプロイされます。

## <a name="before-you-begin"></a>開始する前に

このチュートリアルの前提として、コンテナー、コンテナー イメージ、`docker` コマンドなど、Docker のコア概念を基本的に理解している必要があります。 [Docker の入門][docker-get-started]に関するドキュメントでコンテナーの基礎についての入門情報を参照してください。

このチュートリアルを完了するには、Linux コンテナーを実行するローカルの Docker 開発環境が必要です。 Docker では、[Mac][docker-for-mac], [Windows][docker-for-windows] または [Linux][docker-for-linux] システムで Docker を構成できるパッケージが提供されています。

Azure Cloud Shell には、これらのチュートリアルのすべてのステップを完了するために必要な Docker コンポーネントが含まれているわけではありません。 そのため、完全な Docker 開発環境の使用をお勧めします。

## <a name="get-application-code"></a>アプリケーションのコードを入手する

このチュートリアルで使うサンプル アプリケーションは、基本的な投票アプリです。 アプリケーションは、フロントエンド Web コンポーネントとバックエンド Redis インスタンスで構成されています。 Web コンポーネントは、カスタム コンテナー イメージにパッケージ化されています。 Redis インスタンスでは、Docker Hub から変更されていないイメージを使用します。

サンプル アプリケーションを開発環境に複製するには、[git][] を使用します。

```console
git clone https://github.com/Azure-Samples/azure-voting-app-redis.git
```

複製されたディレクトリに移動します。

```console
cd azure-voting-app-redis
```

ディレクトリ内には、アプリケーションのソース コード、事前作成された Docker Compose ファイル、および Kubernetes マニフェスト ファイルがあります。 これらのファイルは、チュートリアル セット全体で使用されます。

## <a name="create-container-images"></a>コンテナー イメージを作成する

[Docker Compose][docker-compose] は、コンテナー イメージのビルドと複数コンテナー アプリケーションのデプロイとを自動化するために使用することができます。

コンテナー イメージの作成、Redis イメージのダウンロード、およびアプリケーションの起動を行うために、`docker-compose.yaml` サンプル ファイルを実行します。

```console
docker-compose up -d
```

完了したら、[docker images][docker-images] コマンドを使って、作成されたイメージを確認します。 3 つのイメージがダウンロードまたは作成されました。 *azure-vote-front* イメージにはフロントエンド アプリケーションが含まれており、ベースとして `nginx-flask` イメージが使用されます。 `redis` イメージは、Redis インスタンスを起動するために使用されます。

```
$ docker images

REPOSITORY                   TAG        IMAGE ID            CREATED             SIZE
azure-vote-front             latest     9cc914e25834        40 seconds ago      694MB
redis                        latest     a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask      788ca94b2313        9 months ago        694MB
```

[docker ps][docker-ps] コマンドを実行して、実行中のコンテナーを確認します。

```
$ docker ps

CONTAINER ID        IMAGE             COMMAND                  CREATED             STATUS              PORTS                           NAMES
82411933e8f9        azure-vote-front  "/usr/bin/supervisord"   57 seconds ago      Up 30 seconds       443/tcp, 0.0.0.0:8080->80/tcp   azure-vote-front
b68fed4b66b6        redis             "docker-entrypoint..."   57 seconds ago      Up 30 seconds       0.0.0.0:6379->6379/tcp          azure-vote-back
```

## <a name="test-application-locally"></a>ローカルでアプリケーションをテストする

実行中のアプリケーションを表示するには、ローカルの Web ブラウザーで「 `http://localhost:8080`」と入力します。 次の例で示すように、サンプル アプリケーションが読み込まれます。

![Azure 上の Kubernetes クラスターの図](./media/container-service-tutorial-kubernetes-prepare-app/azure-vote.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

アプリケーションの機能を検証したので、実行中のコンテナーを停止して削除できます。 コンテナー イメージを削除しないでください。次のチュートリアルで、*azure-vote-front* イメージは Azure Container Registry インスタンスにアップロードされます。

[docker-compose down][docker-compose-down] コマンドを使用して、コンテナー インスタンスとリソースを停止して削除します。

```console
docker-compose down
```

ローカル アプリケーションが削除されると、Azure Vote アプリケーション *azure-front-front* を含む Docker イメージが作成され、次のチュートリアルで使用できます。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、アプリケーションをテストし、アプリケーション用のコンテナー イメージを作成しました。 以下の方法について学習しました。

> [!div class="checklist"]
> * GitHub からサンプル アプリケーション ソースを複製する
> * サンプル アプリケーション ソースからコンテナー イメージを作成する
> * ローカル Docker 環境で複数コンテナー アプリケーションをテストする

次のチュートリアルでは、Azure Container Registry にコンテナー イメージを格納する方法について学習します。

> [!div class="nextstepaction"]
> [Azure Container Registry にイメージをプッシュする][aks-tutorial-prepare-acr]

<!-- LINKS - external -->
[docker-compose]: https://docs.docker.com/compose/
[docker-for-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-for-mac]: https://docs.docker.com/docker-for-mac/
[docker-for-windows]: https://docs.docker.com/docker-for-windows/
[docker-get-started]: https://docs.docker.com/get-started/
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-ps]: https://docs.docker.com/engine/reference/commandline/ps/
[docker-compose-down]: https://docs.docker.com/compose/reference/down
[git]: https://git-scm.com/downloads

<!-- LINKS - internal -->
[aks-tutorial-prepare-acr]: ./tutorial-kubernetes-prepare-acr.md
