---
title: "Azure の Kubernetes クラスターのクイック スタート | Microsoft Docs"
description: "Azure Container Service で Kubernetes クラスターをデプロイして使ってみます"
services: container-service
documentationcenter: 
author: anhowe
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 8da267e8-2aeb-4c24-9a7a-65bdca3a82d6
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/01/2017
ms.author: anhowe
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: e4f47341554e2de514c8be2f5c85983d09bbb760
ms.lasthandoff: 04/03/2017

---

# <a name="get-started-with-a-kubernetes-cluster-in-container-service"></a>Container Service で Kubernetes クラスターを使用する


この記事では、Azure CLI 2.0 コマンドを使用して Kubernetes クラスターを作成する方法について説明します。 その後、`kubectl` コマンド ライン ツールを使用して、クラスター内のコンテナーの操作を開始します。

次の図は、1 つのマスターと 2 つのエージェントを含むコンテナー サービス クラスターのアーキテクチャを示します。 マスターは Kubernetes REST API を提供します。 エージェント ノードは、Azure 可用性セットにグループ化され、コンテナーを実行します。 すべての VM は同一のプライベート仮想ネットワーク内にあり、完全な相互アクセスが可能です。

![Azure 上の Kubernetes クラスターの図](media/container-service-kubernetes-walkthrough/kubernetes.png)

## <a name="prerequisites"></a>前提条件
このチュートリアルは、[Azure CLI v.2.0](/cli/azure/install-az-cli2) のインストールとセットアップが完了していることを前提としています。 また、`~/.ssh/id_rsa.pub` に SSH RSA 公開キーが存在している必要もあります。 このキーがない場合は、[OS X と Linux](../virtual-machines/linux/mac-create-ssh-keys.md) または [Windows](../virtual-machines/linux/ssh-from-windows.md) 用の手順を参照してください。






## <a name="create-your-kubernetes-cluster"></a>Kubernetes クラスターの作成

Azure CLI 2.0 を使用してクラスターを作成する簡単なシェル コマンドを次に示します。 詳細については、[Azure CLI 2.0 を使用した Azure Container Service クラスターの作成](container-service-create-acs-cluster-cli.md)に関するページをご覧ください。

### <a name="create-a-resource-group"></a>リソース グループの作成
クラスターを作成するには、まず特定の場所にリソース グループを作成する必要があります。 次のようなコマンドを実行します。

```console
RESOURCE_GROUP=my-resource-group
LOCATION=westus
az group create --name=$RESOURCE_GROUP --location=$LOCATION
```

### <a name="create-a-cluster"></a>クラスターの作成
リソース グループが用意できたら、そのグループ内にクラスターを作成できます。

```console
DNS_PREFIX=some-unique-value
CLUSTER_NAME=any-acs-cluster-name
az acs create --orchestrator-type=kubernetes --resource-group $RESOURCE_GROUP --name=$CLUSTER_NAME --dns-prefix=$DNS_PREFIX
```

> [!NOTE]
> デプロイ中に、CLI によって `~/.ssh/id_rsa.pub` が Linux VM がアップロードされます。
>

このコマンドが完了すると、動作している Kubernetes クラスターが作成されます。

### <a name="connect-to-the-cluster"></a>クラスターへの接続

次の Azure CLI コマンドは、Kubernetes コマンド ライン クライアント、`kubectl` を使用して、クライアント コンピューターから Kubernetes クラスターに接続します。 詳細については、「[Azure Container Service クラスターに接続する](container-service-connect.md)」を参照してください。

`kubectl` をまだインストールしていない場合は、次のコマンドでインストールできます。

```console
az acs kubernetes install-cli
```

`kubectl` がインストールされたら、次のコマンドを実行して、マスターの Kubernetes クラスター構成を ~/.kube/config ファイルにダウンロードします。

```console
az acs kubernetes get-credentials --resource-group=$RESOURCE_GROUP --name=$CLUSTER_NAME
```

この時点で、自分のコンピューターからクラスターにアクセスできます。 次を実行してみてください。
```console
kubectl get nodes
```

クラスター内にコンピューターの一覧が表示されることを確認します。

## <a name="create-your-first-kubernetes-service"></a>最初の Kubernetes サービスの作成

このチュートリアルを完了すると、次の方法がわかります。
 * Docker アプリケーションをデプロイして公開する
 * `kubectl exec` を使用してコンテナーでコマンドを実行する 
 * Kubernetes ダッシュボードにアクセスする

### <a name="start-a-simple-container"></a>単純なコンテナーを起動する
次のコマンドを使用して、単純なコンテナー (この場合、Nginx Web サーバー) を実行できます。

```console
kubectl run nginx --image nginx
```

このコマンドにより、いずれかのノードにあるポッドで Nginx Docker コンテナーが開始されます。

実行中のコンテナーを表示するには、次を実行します。

```console
kubectl get pods
```

### <a name="expose-the-service-to-the-world"></a>サービスを公開する
サービスを公開するには、タイプが `LoadBalancer` の Kubernetes `Service` を作成します。

```console
kubectl expose deployments nginx --port=80 --type=LoadBalancer
```

これにより、パブリック IP アドレスを持つ Azure Load Balancer ルールが、Kubernetes によって作成されます。 変更がロード バランサーに反映されるまでに数分かかります。 詳細については、「[Azure Container Service の Kubernetes クラスターのコンテナーで負荷を分散する](container-service-kubernetes-load-balancing.md)」を参照してください。

次のコマンドを実行して、サービスが `pending` から変更され、外部 IP アドレスを表示されることを確認します。

```console
watch 'kubectl get svc'
```

  ![保留中から外部 IP アドレスへの切り替え確認の画像](media/container-service-kubernetes-walkthrough/kubernetes-nginx3.png)

外部 IP アドレスを確認したら、ブラウザーでそれを参照できます。

  ![Nginx の参照の画像](media/container-service-kubernetes-walkthrough/kubernetes-nginx4.png)  


### <a name="browse-the-kubernetes-ui"></a>Kubernetes UI を参照する
Kubernetes Web インターフェイスを表示するには、次のコマンドを使用できます。

```console
kubectl proxy
```
このコマンドは、localhost 上で簡単な認証済みのプロキシを実行します。これを使用して、[http://localhost:8001/ui](http://localhost:8001/ui) で実行されている Kubernetes Web UI を表示することができます。 詳細については、「[Azure Container Service で Kubernetes Web UI を使用する](container-service-kubernetes-ui.md)」を参照してください。

![Kubernetes ダッシュボードの画像](media/container-service-kubernetes-walkthrough/kubernetes-dashboard.png)

### <a name="remote-sessions-inside-your-containers"></a>コンテナー内のリモート セッション
Kubernetes では、クラスターで実行されているリモートの Docker コンテナー内でコマンドを実行することができます。

```console
# Get the name of your nginx pods
kubectl get pods
```

ポッドの名前を使用すると、ポッドに対してリモート コマンドを実行できます。  次に例を示します。

```console
kubectl exec <pod name> date
```

`-it` フラグを使用して、完全な対話型セッションを取得することもできます。

```console
kubectl exec <pod name> -it bash
```

![コンテナー内のリモート セッション](media/container-service-kubernetes-walkthrough/kubernetes-remote.png)



## <a name="next-steps"></a>次のステップ

Kubernetes クラスターで他の操作を行うには、次のリソースを参照してください。

* [Kubernetes ブートキャンプ](https://katacoda.com/embed/kubernetes-bootcamp/1/) - コンテナー化されたアプリケーションをデプロイ、スケール、更新、デバッグする方法について説明されています。
* [Kubernetes ユーザー ガイド](http://kubernetes.io/docs/user-guide/) - 既存の Kubernetes クラスターでのプログラム実行に関する情報が記載されています。
* [Kubernetes の例](https://github.com/kubernetes/kubernetes/tree/master/examples) - Kubernetes を使って実際のアプリケーションを実行する方法の例が記載されています。

