---
title: (非推奨) Azure DC/OS クラスターの負荷分散コンテナー
description: Azure Container Service DC/OS クラスターの複数のコンテナーに負荷を分散します。
services: container-service
author: rgardler
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 06/02/2017
ms.author: rogardle
ms.custom: mvc
ms.openlocfilehash: 1e4c978a8767154fb6a1f9a822cb0dd8d1b8796e
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/16/2019
ms.locfileid: "54331029"
---
# <a name="deprecated-load-balance-containers-in-an-azure-container-service-dcos-cluster"></a>(非推奨) Azure Container Service DC/OS クラスター内のコンテナーで負荷分散する

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

この記事では、Marathon-LB を使用して、DC/OS によって管理された Azure Container Service で内部ロード バランサーを作成する方法を見ていきます。 この構成により、アプリケーションを水平方向にスケーリングすることができます。 また、ロード バランサーをパブリック クラスターに配置し、アプリケーション コンテナーをプライベート クラスターに配置することによって、パブリックおよびプライベート エージェント クラスターを活用することもできます。 このチュートリアルでは、次のことを行いました。

> [!div class="checklist"]
> * Marathon Load Balancer を構成する
> * ロード バランサーを使用してアプリケーションをデプロイする
> * Azure Load Balancer を構成する

このチュートリアルの手順を実行するには、ACS DC/OS クラスターが必要です。 必要に応じて、[このサンプル スクリプト](./../kubernetes/scripts/container-service-cli-deploy-dcos.md)で作成できます。

このチュートリアルには、Azure CLI バージョン 2.0.4 以降が必要です。 バージョンを確認するには、`az --version` を実行します。 アップグレードする必要がある場合は、「[Azure CLI のインストール]( /cli/azure/install-azure-cli)」を参照してください。 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="load-balancing-overview"></a>負荷分散の概要

Azure Container Service の DC/OS クラスターには、次に示す 2 つの負荷分散レイヤーがあります。 

**Azure Load Balancer** は、パブリック エントリ ポイント (エンド ユーザーがアクセスするエントリ ポイント) を提供します。 Azure LB は Azure Container Service によって自動的に提供され、既定ではポート 80、443、8080 を開放するように構成されています。

**Marathon Load Balancer (Marathon-LB)** は、受信要求を処理するコンテナー インスタンスに、それらの要求をルーティングします。 Web サービスを提供しているコンテナーをスケーリングすると、それに応じて Marathon-LB が動的に変化します。 このロード バランサーは、Container Service の既定で提供される機能ではありませんが、ごく簡単な方法でインストールできます。

## <a name="configure-marathon-load-balancer"></a>Marathon Load Balancer を構成する

Marathon Load Balancer は、デプロイされたコンテナーに基づいて動的に自身を再構成します。 また、コンテナーまたはエージェントの喪失に対する回復力もあります。この問題が起きた場合、Apache Mesos は他の場所でコンテナーを再起動し、Marathon-LB はそれに対応します。

パブリック エージェントのクラスターに Marathon Load Balancer をインストールする、次のコマンドを実行します。

```azurecli-interactive
dcos package install marathon-lb
```

## <a name="deploy-load-balanced-application"></a>負荷分散アプリケーションをデプロイする

marathon-lb パッケージができたので、負荷分散するアプリケーション コンテナーをデプロイできます。 

最初に、パブリックに公開されているエージェントの FQDN を取得します。

```azurecli-interactive
az acs list --resource-group myResourceGroup --query "[0].agentPoolProfiles[0].fqdn" --output tsv
```

次に、*hello-web.json*という名前のファイルを作成し、次の内容をコピーします。 `HAPROXY_0_VHOST` ラベルは DC OS/エージェントの FQDN で更新する必要があります。 

```json
{
  "id": "web",
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "yeasy/simple-web",
      "network": "BRIDGE",
      "portMappings": [
        { "hostPort": 0, "containerPort": 80, "servicePort": 10000 }
      ],
      "forcePullImage":true
    }
  },
  "instances": 3,
  "cpus": 0.1,
  "mem": 65,
  "healthChecks": [{
      "protocol": "HTTP",
      "path": "/",
      "portIndex": 0,
      "timeoutSeconds": 10,
      "gracePeriodSeconds": 10,
      "intervalSeconds": 2,
      "maxConsecutiveFailures": 10
  }],
  "labels":{
    "HAPROXY_GROUP":"external",
    "HAPROXY_0_VHOST":"YOUR FQDN",
    "HAPROXY_0_MODE":"http"
  }
}
```

DC/OS の CLI を使用してアプリケーションを実行します。 既定では、Marathon はプライベート クラスターにアプリケーションをデプロイします。 つまり、上記のデプロイにはロード バランサーからしかアクセスできないことを意味します。これは通常望ましい動作です。

```azurecli-interactive
dcos marathon app add hello-web.json
```

アプリケーションをデプロイした後は、エージェント クラスターの FQDN を参照して、負荷分散アプリケーションを表示します。

![負荷分散アプリケーションの画像](./media/container-service-load-balancing/lb-app.png)

## <a name="configure-azure-load-balancer"></a>Azure Load Balancer を構成する

既定では、Azure Load Balancer はポート 80、8080、443 を公開します。 上の例のように、これらの 3 つのポートのいずれかを使用している場合は、何もする必要がありません。 つまり、エージェント ロード バランサーの FQDN をヒットできるようになり、更新するたびにラウンド ロビン方式で 3 つの Web サーバーのいずれかがヒットします。 

別のポートを使用する場合は、使用したポート用のラウンド ロビン ルールとプローブをロード バランサーに追加する必要があります。 この操作は、[Azure CLI](../../azure-resource-manager/xplat-cli-azure-resource-manager.md) で `azure network lb rule create` コマンドと `azure network lb probe create` コマンドを使用して行うことができます。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Marathon と Azure のロード バランサーを使用した、次の操作を含む ACS の負荷分散について学習しました。

> [!div class="checklist"]
> * Marathon Load Balancer を構成する
> * ロード バランサーを使用してアプリケーションをデプロイする
> * Azure Load Balancer を構成する

次のチュートリアルでは、Azure での Azure ストレージと DC/OS の統合について学習します。

> [!div class="nextstepaction"]
> [DC/OS クラスターで Azure ファイル共有をマウントする](container-service-dcos-fileshare.md)