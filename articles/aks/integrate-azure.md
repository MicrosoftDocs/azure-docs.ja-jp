---
title: Open Service Broker for Azure (OSBA) を使用して Azure で管理されたサービスと統合する
description: Open Service Broker for Azure (OSBA) を使用して Azure で管理されたサービスと統合する
services: container-service
author: zr-msft
manager: jeconnoc
ms.service: container-service
ms.topic: overview
ms.date: 12/05/2017
ms.author: zarhoads
ms.openlocfilehash: 7a887905bcb4b09c1b4ae179116b3f08c75caabd
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2019
ms.locfileid: "58758279"
---
# <a name="integrate-with-azure-managed-services-using-open-service-broker-for-azure-osba"></a>Open Service Broker for Azure (OSBA) を使用して Azure で管理されたサービスと統合する

[Kubernetes サービス カタログ][kubernetes-service-catalog]と共に Open Service Broker for Azure (OSBA) を使うと、開発者は Azure で管理されたサービスを Kubernetes で利用できます。 このガイドでは、Kubernetes サービス カタログ、Open Service Broker for Azure (OSBA)、および Kubernetes を使ってAzure で管理されたサービスを使うアプリケーションのデプロイに注目します。

## <a name="prerequisites"></a>前提条件
* Azure サブスクリプション

* Azure CLI: [ローカルにインストールする][azure-cli-install]ことも、[Azure Cloud Shell][azure-cloud-shell] で使うこともできます。

* Helm CLI 2.7 以降: [ローカルにインストールする][helm-cli-install]ことも、[Azure Cloud Shell][azure-cloud-shell] で使うこともできます。

* Azure サブスクリプションの共同作成者ロールでサービス プリンシパルを作成するためのアクセス許可。

* 既存の Azure Kubernetes Service (AKS) クラスター。 AKS クラスターが必要な場合は、「[AKS クラスターの作成][create-aks-cluster]」クイックスタートに従ってください。

## <a name="install-service-catalog"></a>サービス カタログをインストールする

最初に、Helm チャートを使って Kubernetes クラスターにサービス カタログをインストールします。 クラスターの Tiller (Helm サーバー) のインストールを、次のコマンドでアップグレードします。

```azurecli-interactive
helm init --upgrade
```

次に、サービス カタログのチャートを Helm リポジトリに追加します。

```azurecli-interactive
helm repo add svc-cat https://svc-catalog-charts.storage.googleapis.com
```

最後に、Helm チャートを使用してサービス カタログをインストールします。 ご利用のクラスターが RBAC に対応している場合は、このコマンドを使用します。

```azurecli-interactive
helm install svc-cat/catalog --name catalog --namespace catalog --set apiserver.storage.etcd.persistence.enabled=true --set apiserver.healthcheck.enabled=false --set controllerManager.healthcheck.enabled=false --set apiserver.verbosity=2 --set controllerManager.verbosity=2
```

ご利用のクラスターが RBAC に対応していない場合は、このコマンドを使用します。

```azurecli-interactive
helm install svc-cat/catalog --name catalog --namespace catalog --set rbacEnable=false --set apiserver.storage.etcd.persistence.enabled=true --set apiserver.healthcheck.enabled=false --set controllerManager.healthcheck.enabled=false --set apiserver.verbosity=2 --set controllerManager.verbosity=2
```

Helm チャートを実行した後、次のコマンドの出力に `servicecatalog` が表示されることを確認します。

```azurecli-interactive
kubectl get apiservice
```

たとえば、次のような出力が表示される必要があります (切り捨ててあります)。

```
NAME                                 AGE
v1.                                  10m
v1.authentication.k8s.io             10m
...
v1beta1.servicecatalog.k8s.io        34s
v1beta1.storage.k8s.io               10
```

## <a name="install-open-service-broker-for-azure"></a>Open Service Broker for Azure をインストールする

次のステップでは、[Open Service Broker for Azure][open-service-broker-azure] をインストールします。これには、Azure で管理されたサービスのカタログが含まれます。 使用可能な Azure サービスの例は、Azure Database for PostgreSQL、Azure Database for MySQL、Azure SQL Database などです。

最初に、Open Service Broker for Azure の Helm リポジトリを追加します。

```azurecli-interactive
helm repo add azure https://kubernetescharts.blob.core.windows.net/azure
```

次の Azure CLI コマンドを使って、[サービス プリンシパル][create-service-principal]を作成します。

```azurecli-interactive
az ad sp create-for-rbac
```

出力は次のようになります。 次のステップで使うので、`appId`、`password`、`tenant` の値を書き留めておきます。

```JSON
{
  "appId": "7248f250-0000-0000-0000-dbdeb8400d85",
  "displayName": "azure-cli-2017-10-15-02-20-15",
  "name": "http://azure-cli-2017-10-15-02-20-15",
  "password": "77851d2c-0000-0000-0000-cb3ebc97975a",
  "tenant": "72f988bf-0000-0000-0000-2d7cd011db47"
}
```

次の環境変数に上記の値を設定します。

```azurecli-interactive
AZURE_CLIENT_ID=<appId>
AZURE_CLIENT_SECRET=<password>
AZURE_TENANT_ID=<tenant>
```

Azure サブスクリプション ID を取得します。

```azurecli-interactive
az account show --query id --output tsv
```

再び、次の環境変数に上記の値を設定します。

```azurecli-interactive
AZURE_SUBSCRIPTION_ID=[your Azure subscription ID from above]
```

環境変数の設定が済んだので、次のコマンドを実行し、Helm チャートを使って Open Service Broker for Azure をインストールします。

```azurecli-interactive
helm install azure/open-service-broker-azure --name osba --namespace osba \
    --set azure.subscriptionId=$AZURE_SUBSCRIPTION_ID \
    --set azure.tenantId=$AZURE_TENANT_ID \
    --set azure.clientId=$AZURE_CLIENT_ID \
    --set azure.clientSecret=$AZURE_CLIENT_SECRET
```

OSBA のデプロイが完了したら、[サービス カタログ CLI][service-catalog-cli] をインストールします。これは、サービス ブローカー、サービス クラス、サービス プランなどのクエリを行うための使いやすいコマンド ライン インターフェイスです。

次のコマンドを実行して、サービス カタログ CLI のバイナリをインストールします。

```azurecli-interactive
curl -sLO https://servicecatalogcli.blob.core.windows.net/cli/latest/$(uname -s)/$(uname -m)/svcat
chmod +x ./svcat
```

ここで、インストールされているサービス ブローカーの一覧を表示します。

```azurecli-interactive
./svcat get brokers
```

次のような出力が表示されます。

```
  NAME                               URL                                STATUS
+------+--------------------------------------------------------------+--------+
  osba   http://osba-open-service-broker-azure.osba.svc.cluster.local   Ready
```

次に、利用可能なサービス クラスの一覧を表示します。 表示されるサービス クラスは使用可能な Azure で管理されたサービスであり、Open Service Broker for Azure を使ってプロビジョニングできます。

```azurecli-interactive
./svcat get classes
```

最後に、すべての利用可能なサービス プランの一覧を表示します。 サービス プランは、Azure で管理されたサービスのサービス レベルです。 たとえば、Azure Database for MySQL の場合、プランの範囲は、50 データベース トランザクション ユニット (DTU) の Basic レベルである `basic50` から、800 DTU の Standard レベルである `standard800` までです。

```azurecli-interactive
./svcat get plans
```

## <a name="install-wordpress-from-helm-chart-using-azure-database-for-mysql"></a>Azure Database for MySQL を使って Helm チャートから WordPress をインストールする

このステップでは、Helm を使って WordPress の更新された Helm チャートをインストールします。 このチャートは、WordPress が使うことのできる外部 Azure Database for MySQL インスタンスをプロビジョニングします。 このプロセスには数分かかることがあります。

```azurecli-interactive
helm install azure/wordpress --name wordpress --namespace wordpress --set resources.requests.cpu=0 --set replicaCount=1
```

インストールで適切なリソースがプロビジョニングされたことを確認するには、インストールされているサービス インスタンスとバインディングの一覧を表示します。

```azurecli-interactive
./svcat get instances -n wordpress
./svcat get bindings -n wordpress
```

インストールされているシークレットの一覧を表示します。

```azurecli-interactive
kubectl get secrets -n wordpress -o yaml
```

## <a name="next-steps"></a>次の手順

この記事では、Azure Kubernetes Service (AKS) クラスターにサービス カタログをデプロイしました。 Open Service Broker for Azure を使って、Azure で管理されたサービス (この例では Azure Database for MySQL) を使う WordPress のインストールをデプロイしました。

他の更新された OSBA ベースの Helm チャートにアクセスするには、[Azure/helm-charts][helm-charts] リポジトリを参照してください。 OSBA で動作する独自のチャートの作成に関心がある場合は、「[Creating a New Chart][helm-create-new-chart]」(新しいチャートの作成) をご覧ください。

<!-- LINKS - external -->
[helm-charts]: https://github.com/Azure/helm-charts
[helm-cli-install]: https://docs.helm.sh/helm/#helm-install
[helm-create-new-chart]: https://github.com/Azure/helm-charts#creating-a-new-chart
[kubernetes-service-catalog]: https://github.com/kubernetes-incubator/service-catalog
[open-service-broker-azure]: https://github.com/Azure/open-service-broker-azure
[service-catalog-cli]: https://github.com/Azure/service-catalog-cli

<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[azure-cloud-shell]: ../cloud-shell/overview.md
[create-aks-cluster]: ./kubernetes-walkthrough.md
[create-service-principal]: ./kubernetes-service-principal.md
