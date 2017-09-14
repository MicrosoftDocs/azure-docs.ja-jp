---
title: "Helm を使用して Azure Kubernetes にコンテナーをデプロイする | Microsoft Docs"
description: "Helm パッケージ化ツールを使用して、Azure Container Service の Kubernetes クラスターにコンテナーをデプロイする"
services: container-service
documentationcenter: 
author: sauryadas
manager: madhana
editor: 
tags: acs, azure-container-service
keywords: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/10/2017
ms.author: saudas
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: bfd49ea68c597b109a2c6823b7a8115608fa26c3
ms.openlocfilehash: 703b516c8fd443d2bb751530a8f62496b020cb5d
ms.contentlocale: ja-jp
ms.lasthandoff: 07/25/2017

---
# <a name="use-helm-to-deploy-containers-on-a-kubernetes-cluster"></a>Helm を使用して、Kubernetes クラスターにコンテナーをデプロイする 

[Helm](https://github.com/kubernetes/helm/) は、Kubernetes アプリケーションのライフサイクルをインストールおよび管理するのに役立つオープン ソースのパッケージ化ツールです。 Apt-get や Yum などの Linux パッケージ マネージャーと同様に、Helm は、構成済みの Kubernetes リソースのパッケージである Kubernetes チャートの管理に使用されます。 この記事では、Azure Container Service にデプロイされる Kubernetes クラスター上の Helm と連携する方法について説明します。

Helm には、次の 2 つのコンポーネントがあります。 
* **Helm CLI** は、ローカルまたはクラウドのコンピューターで実行するクライアントです。  

* **Tiller** は、Kubernetes クラスター上で実行され、Kubernetes アプリケーションのライフサイクルを管理するサーバーです。 
 
## <a name="prerequisites"></a>前提条件

* [Azure Container Service で Kubernetes クラスター](container-service-kubernetes-walkthrough.md)を作成する

* [ローカル コンピューターで `kubectl`](../container-service-connect.md) をインストールおよび構成する

* [ローカル コンピューターで Helm](https://github.com/kubernetes/helm/blob/master/docs/install.md) をインストールする

## <a name="helm-basics"></a>Helm の基礎 

Tiller をインストールしてアプリケーションをデプロイする Kubernetes クラスターについての情報を表示するには、次のコマンドを入力します。

```bash
kubectl cluster-info 
```
![kubectl cluster-info](./media/container-service-kubernetes-helm/clusterinfo.png)
 
Helm をインストール後、次のコマンドを入力して、Kubernetes クラスターに Tiller をインストールします。

```bash
helm init --upgrade
```
インストールが正常に完了すると、次のような出力が表示されます。

![Tiller インストール](./media/container-service-kubernetes-helm/tiller-install.png)
 
 
 
 
レポジトリで使用可能なすべての Helm チャートを表示するには、次のコマンドを入力します。

```bash 
helm search 
```

次のような出力結果が表示されます。

![Helm 検索](./media/container-service-kubernetes-helm/helm-search.png)
 
チャートを更新して最新バージョンを取得するには、次のように入力します。

```bash 
helm repo update 
```
## <a name="deploy-an-nginx-ingress-controller-chart"></a>Nginx イングレス コントローラー チャートをデプロイする 
 
Nginx イングレス コントローラー チャートをデプロイするには、次の 1 つのコマンドを入力します。

```bash
helm install stable/nginx-ingress 
```
![イングレス コント ローラーをデプロイする](./media/container-service-kubernetes-helm/nginx-ingress.png)

`kubectl get svc` を入力して、クラスターで実行されているすべてのサービスを表示すると、イングレス コントローラーに IP アドレスが割り当てられていることがわかります (割り当て操作の実行中に、`<pending>` が表示されます。 完了するまでに数分かかります)。 

IP アドレスが割り当てられたら、外部 IP アドレスの値に移動して、Nginx がバックエンドで実行されていることを確認します。 
 
![イングレス IP アドレス](./media/container-service-kubernetes-helm/ingress-ip-address.png)


クラスターにインストールされているチャートの一覧を表示するには、次のように入力します。

```bash
helm list 
```

コマンドを `helm ls` に短縮できます。
 
 
 
 
## <a name="deploy-a-mariadb-chart-and-client"></a>MariaDB チャートとクライアントをデプロイする

ここで、MariaDB チャートと MariaDB クライアントをデプロイしてデータベースに接続します。

MariaDB チャートをデプロイするには、次のコマンドを入力します。

```bash
helm install --name v1 stable/mariadb
```

ここで `--name` は、リリースに使用されるタグです。

> [!TIP]
> デプロイメントが失敗した場合は、`helm repo update` を実行してもう一度お試しください。
>
 
 
クラスターにデプロイされているすべてのチャートを表示するには、次のように入力します。

```bash 
helm list
```
 
クラスターで実行されているすべてのデプロイメントを表示するには、次のように入力します。

```bash
kubectl get deployments 
``` 
 
 
最後に、ポッドを実行してクライアントにアクセスするには、次のように入力します。

```bash
kubectl run v1-mariadb-client --rm --tty -i --image bitnami/mariadb --command -- bash  
``` 
 
 
クライアントに接続するには、次のコマンドを入力して、デプロイメントの名前を `v1-mariadb` に置き換えます。

```bash
sudo mysql –h v1-mariadb
```
 
 
ここで、標準の SQL コマンドを使用して、データベース、テーブルなどを作成することができます。たとえば、`Create DATABASE testdb1;` は空のデータベースを作成します。 
 
 
 
## <a name="next-steps"></a>次のステップ

* Kubernetes チャートの管理に関する詳細については、「[Helm ドキュメント](https://github.com/kubernetes/helm/blob/master/docs/index.md)」を参照してください。 


