---
title: "クイックスタート - Linux 用 Azure Docker Swarm クラスター | Microsoft Docs"
description: "Azure CLI を使用して Azure Container Service で Linux コンテナー用 Docker Swarm クラスターを作成する方法を簡単に説明します。"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service, Docker, Swarm
keywords: 
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/14/2017
ms.author: nepeters
ms.custom: 
ms.openlocfilehash: 876135d62d548e155f4ebefd8bbd9d9cca8b87d6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-docker-swarm-cluster"></a>Docker Swarm クラスターのデプロイ

このクイック スタートでは、Azure CLI を使用して Docker Swarm クラスターをデプロイします。 次に、Web フロントエンドと Redis インスタンスで構成される複数コンテナー アプリケーションをデプロイして、このクラスターで実行します。 完了すると、このアプリケーションはインターネット経由でアクセス可能になります。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

このクイックスタートでは、Azure CLI バージョン 2.0.4 以降を実行している必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli)」を参照してください。

## <a name="create-a-resource-group"></a>リソース グループの作成

[az group create](/cli/azure/group#create) コマンドでリソース グループを作成します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理グループです。

次の例では、*myResourceGroup* という名前のリソース グループを場所 *westus* に作成します。

```azurecli-interactive
az group create --name myResourceGroup --location westus
```

出力:

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup",
  "location": "westcentralus",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-docker-swarm-cluster"></a>Docker Swarm クラスターの作成

[az acs create](/cli/azure/acs#create) コマンドを使用して Azure Container Service に Docker Swarm クラスターを作成します。 

次の例では、1 つの Linux マスター ノードと 3 つの Linux エージェント ノードを含む、*mySwarmCluster* という名前のクラスターを作成します。

```azurecli-interactive
az acs create --name mySwarmCluster --orchestrator-type Swarm --resource-group myResourceGroup --generate-ssh-keys
```

制限付き試用版を使用する場合など、Azure サブスクリプションによって Azure リソースへのアクセスが制限される場合もあります。 使用可能なコア数が限られているためにデプロイが失敗した場合は、`--agent-count 1` を [az acs create](/cli/azure/acs#create) コマンドに追加して、既定のエージェント数を減らします。 

数分してコマンドが完了すると、このクラスターに関する情報が json 形式で表示されます。

## <a name="connect-to-the-cluster"></a>クラスターへの接続

このクイック スタートでは、Docker Swarm マスターと Docker エージェント プールの両方の IP アドレスが必要です。 両方の IP アドレスを返す次のコマンドを実行します。


```bash
az network public-ip list --resource-group myResourceGroup --query "[*].{Name:name,IPAddress:ipAddress}" -o table
```

出力:

```bash
Name                                                                 IPAddress
-------------------------------------------------------------------  -------------
swarmm-agent-ip-myswarmcluster-myresourcegroup-d5b9d4agent-66066781  52.179.23.131
swarmm-master-ip-myswarmcluster-myresourcegroup-d5b9d4mgmt-66066781  52.141.37.199
```

Swarm マスターへの SSH トンネルを作成します。 `IPAddress` を Swarm マスターの IP アドレスに置き換えます。

```bash
ssh -p 2200 -fNL 2375:localhost:2375 azureuser@IPAddress
```

`DOCKER_HOST` 環境変数を設定します。 これにより、Docker Swarm に対して docker コマンドを実行する際に、ホストの名前を省略することができます。

```bash
export DOCKER_HOST=:2375
```

これで Docker Swarm 上で Docker サービスを実行する準備が整いました。


## <a name="run-the-application"></a>アプリケーションの実行

`docker-compose.yaml` という名前のファイルを作成し、次の内容をコピーします。

```yaml
version: '3'
services:
  azure-vote-back:
    image: redis
    container_name: azure-vote-back
    ports:
        - "6379:6379"

  azure-vote-front:
    image: microsoft/azure-vote-front:redis-v1
    container_name: azure-vote-front
    environment:
      REDIS: azure-vote-back
    ports:
        - "80:80"
```

次のコマンドを実行して Azure Vote サービスを作成します。

```bash
docker-compose up -d
```

出力:

```bash
Creating network "user_default" with the default driver
Pulling azure-vote-front (microsoft/azure-vote-front:redis-v1)...
swarm-agent-EE873B23000005: Pulling microsoft/azure-vote-front:redis-v1...
swarm-agent-EE873B23000004: Pulling microsoft/azure-vote-front:redis-v1... : downloaded
Pulling azure-vote-back (redis:latest)...
swarm-agent-EE873B23000004: Pulling redis:latest... : downloaded
Creating azure-vote-front ... 
Creating azure-vote-back ... 
Creating azure-vote-front
Creating azure-vote-back ...
```

## <a name="test-the-application"></a>アプリケーションをテストする

Azure Vote アプリケーションをテストするために、ブラウザーで Swarm エージェントプールの IP アドレスにアクセスします。

![Azure Vote にブラウザーでアクセスしたところ](media/container-service-docker-swarm-mode-walkthrough/azure-vote.png)

## <a name="delete-cluster"></a>クラスターを削除する
クラスターが必要なくなったら、[az group delete](/cli/azure/group#delete) コマンドを使用して、リソース グループ、コンテナー サービス、およびすべての関連リソースを削除できます。

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="get-the-code"></a>コードの入手

このクイック スタートでは、事前に作成したコンテナー イメージを使用して、Docker サービスを作成しました。 関連するアプリケーション コード、Dockerfile、および Compose ファイルは、GitHub で入手できます。

[https://github.com/Azure-Samples/azure-voting-app-redis](https://github.com/Azure-Samples/azure-voting-app-redis.git)

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、Docker Swarm クラスターをデプロイし、そこに複数コンテナー アプリケーションをデプロイしました。

Docker Swarm と Visual Studio Team Services の統合について確認するには、「Docker Swarm と VSTS を使用した CI/CD」に進んでください。

> [!div class="nextstepaction"]
> [Docker Swarm と VSTS を使用した CI/CD](./container-service-docker-swarm-setup-ci-cd.md)