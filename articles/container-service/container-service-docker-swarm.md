---
title: "Docker API での Azure Swarm クラスターの管理 | Microsoft Docs"
description: "Azure Container Service の Docker Swarm クラスターにコンテナーをデプロイします"
services: container-service
documentationcenter: 
author: rgardler
manager: madhana
editor: 
tags: acs, azure-container-service
keywords: "Docker、コンテナー、マイクロ サービス、Mesos、Azure"
ms.assetid: af8f6fb2-13dc-429c-b82a-24a741168d42
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/13/2016
ms.author: rogardle
ms.translationtype: Human Translation
ms.sourcegitcommit: 0aa9b3ae14f586fc79e6ebee898e794d526c19bd
ms.openlocfilehash: bd20dc4eb3948d08e3c2dd2ad2bb42d18df67796
ms.contentlocale: ja-jp
ms.lasthandoff: 02/17/2017


---
# <a name="container-management-with-docker-swarm"></a>Docker Swarm でのコンテナーの管理
Docker Swarm は、コンテナーにまとめたワークロードをプールされた Docker ホストのセット全体にデプロイする環境を提供します。 Docker Swarm では、ネイティブの Docker API を使用します。 Docker Swarm 上のコンテナーを管理するワークフローは、1 つのコンテナー ホスト上の場合とほぼ同じです。 このドキュメントでは、Docker Swarm の Azure コンテナー サービス インスタンスで、コンテナーにまとめたワークロードをデプロイする簡単な例について説明します。 Docker Swarm の詳細なドキュメントについては、 [Docker.com の Docker Swarm](https://docs.docker.com/swarm/)を参照してください。

[!INCLUDE [container-service-swarm-mode-note](../../includes/container-service-swarm-mode-note.md)]

このドキュメントで行う演習の前提条件:

[Azure コンテナー サービスに Swarm クラスターを作成する](container-service-deployment.md)

[Azure コンテナー サービスの Swarm クラスターと接続する](container-service-connect.md)

## <a name="deploy-a-new-container"></a>新しいコンテナーをデプロイする
Docker Swarm で新しいコンテナーを作成するには、 `docker run` コマンドを使用します (上記の前提条件に従い、SSH トンネルをマスターに対して開いておいてください)。 この例では、 `yeasy/simple-web` イメージからコンテナーを作成します。

```bash
user@ubuntu:~$ docker run -d -p 80:80 yeasy/simple-web

4298d397b9ab6f37e2d1978ef3c8c1537c938e98a8bf096ff00def2eab04bf72
```

コンテナーを作成したら、 `docker ps` を使用してコンテナーに関する情報を返します。 ここで、コンテナーをホストする Swarm エージェントが一覧に表示されます。

```bash
user@ubuntu:~$ docker ps

CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                 NAMES
4298d397b9ab        yeasy/simple-web    "/bin/sh -c 'python i"   31 seconds ago      Up 9 seconds        10.0.0.5:80->80/tcp   swarm-agent-34A73819-1/happy_allen
```  

このコンテナーで実行されているアプリケーションには、Swarm エージェント ロード バランサーのパブリック DNS 名を使用してアクセスできます。 この情報は、Azure ポータルで見つけることができます。  

![Real visit results](../media/container-service-docker-swarm/real-visit.jpg)  

既定では、Load Balancer 用にポート 80、8080、443 が開放されています。 別のポートに接続する場合は、エージェント プール用に Azure Load Balancer でそのポートを開く必要があります。

## <a name="deploy-multiple-containers"></a>複数のコンテナーをデプロイする
"docker run" を複数回実行して複数のコンテナーを起動した場合、 `docker ps` コマンドを使用して、コンテナーが実行されているホストを確認できます。 次の例では、3 つのコンテナーが 3 つの Swarm エージェントに均等に配分されます。  

```bash
user@ubuntu:~$ docker ps

CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                 NAMES
11be062ff602        yeasy/simple-web    "/bin/sh -c 'python i"   11 seconds ago      Up 10 seconds       10.0.0.6:83->80/tcp   swarm-agent-34A73819-2/clever_banach
1ff421554c50        yeasy/simple-web    "/bin/sh -c 'python i"   49 seconds ago      Up 48 seconds       10.0.0.4:82->80/tcp   swarm-agent-34A73819-0/stupefied_ride
4298d397b9ab        yeasy/simple-web    "/bin/sh -c 'python i"   2 minutes ago       Up 2 minutes        10.0.0.5:80->80/tcp   swarm-agent-34A73819-1/happy_allen
```  

## <a name="deploy-containers-by-using-docker-compose"></a>Docker Compose を使用してコンテナーをデプロイする
Docker Compose を使用して、複数のコンテナーのデプロイと構成を自動化することができます。 自動化するには、Secure Shell (SSH) トンネルが作成され、DOCKER_HOST 変数が設定されている必要があります (上記の前提条件を参照)。

ローカル システムに docker-compose.yml を作成します。 作成には、この [サンプル](https://raw.githubusercontent.com/rgardler/AzureDevTestDeploy/master/docker-compose.yml)を使用します。

```bash
web:
  image: adtd/web:0.1
  ports:
    - "80:80"
  links:
    - rest:rest-demo-azure.marathon.mesos
rest:
  image: adtd/rest:0.1
  ports:
    - "8080:8080"

```

`docker-compose up -d` を実行してコンテナーのデプロイを開始します。

```bash
user@ubuntu:~/compose$ docker-compose up -d
Pulling rest (adtd/rest:0.1)...
swarm-agent-3B7093B8-0: Pulling adtd/rest:0.1... : downloaded
swarm-agent-3B7093B8-2: Pulling adtd/rest:0.1... : downloaded
swarm-agent-3B7093B8-3: Pulling adtd/rest:0.1... : downloaded
Creating compose_rest_1
Pulling web (adtd/web:0.1)...
swarm-agent-3B7093B8-3: Pulling adtd/web:0.1... : downloaded
swarm-agent-3B7093B8-0: Pulling adtd/web:0.1... : downloaded
swarm-agent-3B7093B8-2: Pulling adtd/web:0.1... : downloaded
Creating compose_web_1
```

最後に、実行されているコンテナーの一覧が返されます。 この一覧は、Docker Compose を使用してデプロイされたコンテナーを反映しています。

```bash
user@ubuntu:~/compose$ docker ps
CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS              PORTS                     NAMES
caf185d221b7        adtd/web:0.1        "apache2-foreground"   2 minutes ago       Up About a minute   10.0.0.4:80->80/tcp       swarm-agent-3B7093B8-0/compose_web_1
040efc0ea937        adtd/rest:0.1       "catalina.sh run"      3 minutes ago       Up 2 minutes        10.0.0.4:8080->8080/tcp   swarm-agent-3B7093B8-0/compose_rest_1
```

当然ながら、`docker-compose ps` を使用すると、`compose.yml` ファイルに定義されているコンテナーのみを確認することができます。

## <a name="next-steps"></a>次のステップ
[Docker Swarm の詳細](https://docs.docker.com/swarm/)


