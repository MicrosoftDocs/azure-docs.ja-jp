---
title: "Azure DC/OS クラスターの負荷分散コンテナー | Microsoft Docs"
description: "Azure Container Service DC/OS クラスターの複数のコンテナーに負荷を分散します。"
services: container-service
documentationcenter: 
author: rgardler
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "コンテナー, マクロサービス, DC/OS, Azure"
ms.assetid: f0ab5645-2636-42de-b23b-4c3a7e3aa8bb
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/11/2016
ms.author: rogardle
translationtype: Human Translation
ms.sourcegitcommit: 0aa9b3ae14f586fc79e6ebee898e794d526c19bd
ms.openlocfilehash: 27ad7100f6203db3ba3dcc88ffdc191b9b9d45cb


---
# <a name="load-balance-containers-in-an-azure-container-service-dcos-cluster"></a>Azure Container Service DC/OS クラスターの負荷分散コンテナー
この記事では、Marathon-LB を使用して、DC/OS によって管理された Azure Container Service で内部ロード バランサーを作成する方法を見ていきます。 こうすることで、アプリケーションを水平方向にスケーリングすることができます。 また、ロード バランサーをパブリック クラスターに配置し、アプリケーション コンテナーをプライベート クラスターに配置することによって、パブリックおよびプライベート エージェント クラスターを活用することもできます。

## <a name="prerequisites"></a>前提条件
オーケストレーターの種類が DC/OS の [Azure Container Service のインスタンスをデプロイ](container-service-deployment.md)し、[クライアントがクラスターに接続できるようにします](container-service-connect.md)。 

## <a name="load-balancing"></a>負荷分散
作成するコンテナー サービス クラスターには、次に示す&2; つの負荷分散レイヤーがあります。 

1. Azure Load Balancer は、パブリック エントリ ポイント (エンド ユーザーがアクセスするエントリ ポイント) を提供します。 これは Azure コンテナー サービスによって自動的に提供され、既定ではポート 80、443、8080 を開放するように構成されています。
2. Marathon Load Balancer (Marathon-LB) は、受信要求を処理するコンテナー インスタンスに、それらの要求をルーティングします。 Web サービスを提供しているコンテナーをスケーリングすると、それに応じて Marathon-LB が動的に変化します。 このロード バランサーは、コンテナー サービスの既定で提供される機能ではありませんが、ごく簡単な方法でインストールできます。

## <a name="marathon-load-balancer"></a>Marathon Load Balancer
Marathon Load Balancer は、デプロイされたコンテナーに基づいて動的に自身を再構成します。 また、コンテナーまたはエージェントの喪失に対する回復力もあります。この問題が起きた場合、Apache Mesos は単に他の場所でコンテナーを再起動し、Marathon-LB はそれに対応します。

Marathon Load Balancer をインストールするには、DC/OS Web UI またはコマンド ラインを使用します。

### <a name="install-marathon-lb-using-dcos-web-ui"></a>DC/OS Web UI を使用して Marathon-LB をインストールする
1. [Universe (ユニバース)] をクリックします。
2. "Marathon-LB" を検索します。
3. [Install (インストール)] をクリックします。

![Installing marathon-lb via the DC/OS Web Interface](./media/dcos/marathon-lb-install.png)

### <a name="install-marathon-lb-using-the-dcos-cli"></a>DC/OS CLI を使用して Marathon-LB をインストールする
DC/OS CLI をインストールし、クラスターに接続できることを確認したら、クライアント コンピューターから次のコマンドを実行します。

```bash
dcos package install marathon-lb
```

このコマンドは、ロード バランサーをパブリック エージェント クラスターに自動的にインストールします。

## <a name="deploy-a-load-balanced-web-application"></a>負荷分散 Web アプリケーションをデプロイする
marathon-lb パッケージができたので、負荷分散するアプリケーション コンテナーをデプロイできます。 この例では、次の構成を使用して、単純な Web サーバーをデプロイします。

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

* `HAProxy_0_VHOST` の値に、エージェントのロード バランサーの FQDN を設定します。 これは、 `<acsName>agents.<region>.cloudapp.azure.com`という形式になります。 たとえば、`West US` リージョンに `myacs` という名前の Container Service クラスターを作成した場合、FQDN は `myacsagents.westus.cloudapp.azure.com` になります。 この FQDN は、 [Azure Portal](https://portal.azure.com)で Container Service 用に作成したリソース グループのリソースを表示している状態で、名前に "agent" が含まれるロード バランサーを検索する方法でも確認できます。
* servicePort を 10,000 以上のポートに設定します。 この設定により、このコンテナーで実行されているサービスを特定できます。つまり、marathon-lb は、これを使用して、分散先となるサービスを特定します。
* `HAPROXY_GROUP` ラベルを "external" に設定します。
* `hostPort` を 0 に設定します。 この設定は、使用可能なポートを Marathon が任意に割り当てることを意味します。
* `instances` を、作成するインスタンスの数に設定します。 これらの値は、設定後にいつでもスケールアップまたはスケールダウンできます。

Marathon は既定でプライベート クラスターにデプロイされることに注意する必要があります。つまり、上のデプロイは、ロード バランサー経由でのみアクセスできます。通常は、これが望ましい動作です。

### <a name="deploy-using-the-dcos-web-ui"></a>DC/OS Web UI を使用してデプロイする
1. [SSH トンネル](container-service-connect.md)を設定した後、http://localhost/marathon の Marathon ページにアクセスして、[`Create Appliction`] をクリックします。
2. `New Application` ダイアログ ボックスで、右上にある [`JSON Mode`] をクリックします。
3. 上記の JSON をエディターに貼り付けます。
4. [`Create Appliction`] をクリックします。

### <a name="deploy-using-the-dcos-cli"></a>DC/OS CLI を使用してデプロイする
DC/OS CLI でこのアプリケーションをデプロイするには、上記の JSON をコピーして `hello-web.json` というファイルに貼り付け、次のコマンドを実行します。

```bash
dcos marathon app add hello-web.json
```

## <a name="azure-load-balancer"></a>Azure Load Balancer
既定では、Azure Load Balancer はポート 80、8080、443 を公開します。 上の例のように、これらの&3; つのポートのいずれかを使用している場合は、何もする必要がありません。 つまり、エージェント ロード バランサーの FQDN をヒットできるようになり、更新するたびにラウンド ロビン方式で&3; つの Web サーバーのいずれかがヒットします。 ただし、別のポートを使用する場合は、使用したポート用のラウンド ロビン ルールとプローブをロード バランサーに追加する必要があります。 この操作は、[Azure CLI](../xplat-cli-azure-resource-manager.md) で `azure network lb rule create` コマンドと `azure network lb probe create` コマンドを使用して行うことができます。 また、この操作は Azure ポータルからも実行できます。

## <a name="additional-scenarios"></a>その他のシナリオ
公開するサービスに応じて使用するドメインが異なるシナリオもあります。 次に例を示します。

mydomain1.com -> Azure LB:80 -> marathon-lb:10001 -> mycontainer1:33292  
mydomain2.com -> Azure LB:80 -> marathon-lb:10002 -> mycontainer2:22321

これを実現するには、[仮想ホスト](https://mesosphere.com/blog/2015/12/04/dcos-marathon-lb/)に関するページを参照してください。ドメインを特定の marathon-lb パスに関連付ける方法を説明しています。

また、さまざまなポートを公開し、marathon-lb の背後で適切なサービスにそれらのポートを再マップすることもできます。次に例を示します。

Azure lb:80 -> marathon-lb:10001 -> mycontainer:233423  
Azure lb:8080 -> marathon-lb:1002 -> mycontainer2:33432

## <a name="next-steps"></a>次のステップ
DC/OS に関するドキュメントで [Marathon-LB](https://dcos.io/docs/1.7/usage/service-discovery/marathon-lb/)の詳細について参照してください。




<!--HONumber=Jan17_HO4-->


