---
title: Azure Kubernetes Service (AKS) で内部ロード バランサーを作成する
description: サービスを Azure Kubernetes Service (AKS) を使用して公開する内部ロード バランサーを作成して使用する方法を示します。
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 03/04/2019
ms.author: mlearned
ms.openlocfilehash: 5842003d43d4268d0f663e8a57e40562a480e252
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67615154"
---
# <a name="use-an-internal-load-balancer-with-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) で内部ロード バランサーを使用する

Azure Kubernetes Service (AKS) でアプリケーションへのアクセスを制限するために、内部ロード バランサーを作成して使用できます。 内部ロード バランサーは、Kubernetes サービスを Kubernetes クラスターと同じ仮想ネットワークで実行されているアプリケーションからのみアクセス可能にします。 この記事では、Azure Kubernetes Service (AKS) を使用して内部ロード バランサーを使用する方法を示します。

> [!NOTE]
> Azure Load Balancer は、*Basic* と *Standard* の 2 つの SKU で使用できます。 サービス マニフェストを使用して AKS でロード バランサーを作成する場合、既定では *Basic* SKU が使用されます。 詳細については、[Azure ロード バランサー SKU の比較][azure-lb-comparison]に関するページを参照してください。

## <a name="before-you-begin"></a>開始する前に

この記事は、AKS クラスターがすでに存在していることを前提としています。 AKS クラスターが必要な場合は、AKS のクイックスタート ([Azure CLI を使用][aks-quickstart-cli]、or [using the Azure portal][aks-quickstart-portal]) を参照してください。

また、Azure CLI バージョン 2.0.59 以降がインストールされ、構成されている必要もあります。 バージョンを確認するには、 `az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「 [Azure CLI のインストール][install-azure-cli]」を参照してください。

既存のサブネットまたはリソース グループを使用する場合、AKS クラスターのサービス プリンシパルにはネットワーク リソースを管理するアクセス許可が必要です。 一般に、委任されたリソースのサービス プリンシパルには*ネットワーク共同作成者*ロールを割り当てます。 アクセス許可の詳細については、[他の Azure リソースへの AKS アクセスの委任][aks-sp]に関する記事を参照してください。

## <a name="create-an-internal-load-balancer"></a>内部ロード バランサーを作成します。

内部ロード バランサーを作成するには、次の例に示すように、サービスの種類 *LoadBalancer* と *azure-load-balancer-internal* の注釈を含む `internal-lb.yaml` という名前のサービス マニフェストを作成します。

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: internal-app
```

[kubectl apply][kubectl-apply] を使用して内部ロード バランサーをデプロイし、YAML マニフェストの名前を指定します。

```console
kubectl apply -f internal-lb.yaml
```

ノード リソース グループ内にAzure ロード バランサーが作成され、AKS クラスターと同じ仮想ネットワークに接続されます。

サービスの詳細を表示すると、内部ロード バランサーの IP アドレスが *EXTERNAL-IP* 列に示されます。 このコンテキストでは、"*外部*" はロード バランサーの外部インスタンスに対するものであり、パブリックな外部 IP アドレスに対するものではありません。 次の例に示すように、IP アドレスが *\<[保留中]\>* から実際の内部 IP アドレスに変わるには 1 ～ 2 分かかることがあります。

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.0.248.59   10.240.0.7    80:30555/TCP   2m
```

## <a name="specify-an-ip-address"></a>IP アドレスを指定する

内部ロード バランサーに特定の IP アドレスを使用する場合は、ロード バランサーの YAML マニフェストに *loadBalancerIP* プロパティを追加します。 指定した IP アドレスは AKS クラスターと同じサブネットに存在し、まだリソースに割り当てられていない必要があります。

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
spec:
  type: LoadBalancer
  loadBalancerIP: 10.240.0.25
  ports:
  - port: 80
  selector:
    app: internal-app
```

デプロイしてサービスの詳細を表示すると、*EXTERNAL-IP* 列の IP アドレスに、指定された IP アドレスが反映されます。

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.0.184.168   10.240.0.25   80:30225/TCP   4m
```

## <a name="use-private-networks"></a>プライベート ネットワークを使用する

AKS クラスターを作成するときに、高度なネットワーク設定を指定できます。 このアプローチでは、既存の Azure 仮想ネットワークとサブネットにクラスターをデプロイできます。 1 つのシナリオは、オンプレミス環境に接続されたプライベート ネットワークに AKS クラスターをデプロイし、内部的にのみアクセスできるサービスを実行することです。 詳細については、[Kubenet][use-kubenet] or [Azure CNI][advanced-networking] での仮想ネットワーク サブネットの構成を参照してください。

プライベート ネットワークを使用する AKS クラスターに内部ロード バランサーをデプロイする場合、前の手順を変更する必要はありません。 ロード バランサーは、AKS クラスターと同じリソース グループ内に作成されますが、次の例に示すように、プライベート仮想ネットワークとサブネットに接続されます。

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.1.15.188   10.0.0.35     80:31669/TCP   1m
```

> [!NOTE]
> AKS クラスターのサービス プリンシパルに対して、Azure 仮想ネットワーク リソースがデプロイされるリソース グループへの*ネットワーク共同作成者*ロールを付与しなければならない場合があります。 [az aks show][az-aks-show] を使用してサービス プリンシパルを表示します (例: `az aks show --resource-group myResourceGroup --name myAKSCluster --query "servicePrincipalProfile.clientId"`)。 ロールの割り当てを作成するには、[az role assignment create][az-role-assignment-create] コマンドを使用します。

## <a name="specify-a-different-subnet"></a>別のサブネットを指定する

ロード バランサーのサブネットを指定するには、サービスに *azure-load-balancer-internal-subnet* 注釈を追加します。 指定されたサブネットは、AKS クラスターと同じ仮想ネットワーク内に存在する必要があります。 デプロイされると、ロード バランサーの *EXTERNAL-IP* アドレスが、指定されたサブネットの一部になります。

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
    service.beta.kubernetes.io/azure-load-balancer-internal-subnet: "apps-subnet"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: internal-app
```

## <a name="delete-the-load-balancer"></a>ロード バランサーを削除する

内部ロード バランサーを使用しているすべてのサービスが削除されると、ロード バランサー自体も削除されます。

また、すべての Kubernetes リソース (`kubectl delete service internal-app` など) と同様に、サービスを直接削除することもできます。それにより、基礎となる Azure ロード バランサーも削除されます。

## <a name="next-steps"></a>次の手順

[Kubernetes サービスのドキュメント][kubernetes-services]で Kubernetes サービスについて学習する。

<!-- LINKS - External -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
[aks-engine]: https://github.com/Azure/aks-engine

<!-- LINKS - Internal -->
[advanced-networking]: configure-azure-cni.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[azure-lb-comparison]: ../load-balancer/load-balancer-overview.md#skus
[use-kubenet]: configure-kubenet.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-sp]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
