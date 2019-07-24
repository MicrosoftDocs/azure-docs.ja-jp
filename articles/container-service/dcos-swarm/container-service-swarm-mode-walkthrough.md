---
title: (非推奨) クイック スタート - Linux 用 Azure Docker CE クラスター
description: Azure CLI を使用して Azure Container Service で Linux コンテナー用 Docker CE クラスターを作成する方法を簡単に説明します。
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/16/2018
ms.author: iainfou
ms.custom: ''
ms.openlocfilehash: a7a7455ce9167a9c480d317d50fdce49e2ef06a9
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2018
ms.locfileid: "52994293"
---
# <a name="deprecated-deploy-docker-ce-cluster"></a>(非推奨) Docker CE クラスターのデプロイ

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

このクイック スタートでは、Azure CLI を使用して Docker CE クラスターをデプロイします。 次に、Web フロントエンドと Redis インスタンスで構成される複数コンテナー アプリケーションをデプロイして、このクラスターで実行します。 完了すると、このアプリケーションはインターネット経由でアクセス可能になります。

Azure Container Service での Docker CE は現在プレビュー段階です。**運用環境のワークロードには使用しないでください**。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

CLI をローカルにインストールして使用する場合、このクイック スタートを実施するには、Azure CLI バージョン 2.0.4 以降を実行している必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="create-a-resource-group"></a>リソース グループの作成

[az group create](/cli/azure/group#az-group-create) コマンドでリソース グループを作成します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理グループです。

次の例では、*myResourceGroup* という名前のリソース グループを場所 *westus2* に作成します。

```azurecli-interactive
az group create --name myResourceGroup --location westus2
```

出力:

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup",
  "location": "westus2",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-docker-swarm-cluster"></a>Docker Swarm クラスターの作成

[az acs create](/cli/azure/acs#az-acs-create) コマンドを使用して Azure Container Service に Docker CE クラスターを作成します。 Docker CE の地域の可用性については、「[ACS regions for Docker CE](https://github.com/Azure/ACS/blob/master/announcements/2017-08-04_additional_regions.md)」(Docker CE の ACS 地域) を参照してください。

次の例では、1 つの Linux マスター ノードと 3 つの Linux エージェント ノードを含む、*mySwarmCluster* という名前のクラスターを作成します。

```azurecli-interactive
az acs create --name mySwarmCluster --orchestrator-type dockerce --resource-group myResourceGroup --generate-ssh-keys
```

制限付き試用版を使用する場合など、Azure サブスクリプションによって Azure リソースへのアクセスが制限される場合もあります。 使用可能なコア数が限られているためにデプロイが失敗した場合は、`--agent-count 1` を [az acs create](/cli/azure/acs#az-acs-create) コマンドに追加して、既定のエージェント数を減らします。 

数分してコマンドが完了すると、このクラスターに関する情報が JSON 形式で表示されます。

## <a name="connect-to-the-cluster"></a>クラスターへの接続

このクイック スタートでは、Docker Swarm マスターと Docker エージェント プールの両方の FQDN が必要です。 マスターとエージェントの両方の FQDN を返す次のコマンドを実行します。


```bash
az acs list --resource-group myResourceGroup --query '[*].{Master:masterProfile.fqdn,Agent:agentPoolProfiles[0].fqdn}' -o table
```

出力:

```bash
Master                                                               Agent
-------------------------------------------------------------------  --------------------------------------------------------------------
myswarmcluster-myresourcegroup-d5b9d4mgmt.ukwest.cloudapp.azure.com  myswarmcluster-myresourcegroup-d5b9d4agent.ukwest.cloudapp.azure.com
```

Swarm マスターへの SSH トンネルを作成します。 `MasterFQDN` を Swarm マスターの FQDN アドレスに置き換えます。

```bash
ssh -p 2200 -fNL localhost:2374:/var/run/docker.sock azureuser@MasterFQDN
```

`DOCKER_HOST` 環境変数を設定します。 これにより、Docker Swarm に対して docker コマンドを実行する際に、ホストの名前を省略することができます。

```bash
export DOCKER_HOST=localhost:2374
```

これで Docker Swarm 上で Docker サービスを実行する準備が整いました。


## <a name="run-the-application"></a>アプリケーションの実行

`azure-vote.yaml` という名前のファイルを作成し、次の内容をコピーします。


```yaml
version: '3'
services:
  azure-vote-back:
    image: redis
    ports:
        - "6379:6379"

  azure-vote-front:
    image: microsoft/azure-vote-front:v1
    environment:
      REDIS: azure-vote-back
    ports:
        - "80:80"
```

[docker stack deploy](https://docs.docker.com/engine/reference/commandline/stack_deploy/) コマンドを実行して Azure Vote サービスを作成します。

```bash
docker stack deploy azure-vote --compose-file azure-vote.yaml
```

出力:

```bash
Creating network azure-vote_default
Creating service azure-vote_azure-vote-back
Creating service azure-vote_azure-vote-front
```

アプリケーションのデプロイの状態を取得するには、[docker stack ps](https://docs.docker.com/engine/reference/commandline/stack_ps/) コマンドを使用します。

```bash
docker stack ps azure-vote
```

各サービスの `CURRENT STATE` が `Running` になれば、アプリケーションは準備完了です。

```bash
ID                  NAME                            IMAGE                                 NODE                               DESIRED STATE       CURRENT STATE                ERROR               PORTS
tnklkv3ogu3i        azure-vote_azure-vote-front.1   microsoft/azure-vote-front:v1   swarmm-agentpool0-66066781000004   Running             Running 5 seconds ago                            
lg99i4hy68r9        azure-vote_azure-vote-back.1    redis:latest                          swarmm-agentpool0-66066781000002   Running             Running about a minute ago
```

## <a name="test-the-application"></a>アプリケーションをテストする

Azure Vote アプリケーションをテストするために、ブラウザーで Swarm エージェントプールの FQDN にアクセスします。

![Azure Vote にブラウザーでアクセスしたところ](media/container-service-docker-swarm-mode-walkthrough/azure-vote.png)

## <a name="delete-cluster"></a>クラスターを削除する
クラスターが必要なくなったら、[az group delete](/cli/azure/group#az-group-delete) コマンドを使用して、リソース グループ、コンテナー サービス、およびすべての関連リソースを削除できます。

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="get-the-code"></a>コードの入手

このクイック スタートでは、事前に作成したコンテナー イメージを使用して、Docker サービスを作成しました。 関連するアプリケーション コード、Dockerfile、および Compose ファイルは、GitHub で入手できます。

[https://github.com/Azure-Samples/azure-voting-app-redis](https://github.com/Azure-Samples/azure-voting-app-redis.git)

## <a name="next-steps"></a>次の手順

このクイック スタートでは、Docker Swarm クラスターをデプロイし、そこに複数コンテナー アプリケーションをデプロイしました。

Docker Swarm と Azure DevOps の統合について確認するには、Docker Swarm と Azure DevOps を使用した CI/CD に進んでください。

> [!div class="nextstepaction"]
> [Docker Swarm と Azure DevOps を使用した CI/CD](./container-service-docker-swarm-setup-ci-cd.md)
