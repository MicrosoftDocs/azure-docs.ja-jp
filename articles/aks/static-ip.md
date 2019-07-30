---
title: Azure Kubernetes Service (AKS) ロード バランサーで静的 IP アドレスを使用する
description: Azure Kubernetes Service (AKS) ロード バランサーで静的 IP アドレスを使用する方法を説明します｡
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 03/04/2019
ms.author: mlearned
ms.openlocfilehash: 9e32715766734bcbb150d70aeed2dc5b06a4bcbb
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67614464"
---
# <a name="use-a-static-public-ip-address-with-the-azure-kubernetes-service-aks-load-balancer"></a>Azure Kubernetes Service (AKS) ロード バランサーで静的 IP アドレスを使用する

既定では、AKS クラスターによって作成されたロード バランサーのリソースに割り当てられているパブリック IP アドレスは､そのリソースの有効期間の間のみ有効です。 Kubernetes サービスを削除すると、関連付けられているロード バランサーと IP アドレスも削除されます。 デプロイし直された Kubernetes サービスに対して特定の IP アドレスを割り当てるか､あるいは IP アドレスを保持する場合は、静的パブリック IP アドレスを作成して､使用することができます。

この記事では、静的パブリック IP アドレスを作成して、Kubernetes サービスに割り当てる方法を示します。

## <a name="before-you-begin"></a>開始する前に

この記事は、AKS クラスターがすでに存在していることを前提としています。 AKS クラスターが必要な場合は、AKS のクイックスタートの、[Azure CLI を使用][aks-quickstart-cli]に関するページと、Azure portal に関するページor [using the Azure portal][aks-quickstart-portal]を参照してください。

また、Azure CLI バージョン 2.0.59 以降がインストールされ、構成されている必要もあります。 バージョンを確認するには、 `az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「 [Azure CLI のインストール][install-azure-cli]」を参照してください。

現在は *Basic IP の SKU* のみがサポートされています。 *Standard IP* リソース SKU をサポートするように取り組んでいます。 詳しくは、「[Azure における IP アドレスの種類と割り当て方法][ip-sku]」をご覧ください。

## <a name="create-a-static-ip-address"></a>静的 IP アドレスを作成する

AKS で使用する静的パブリック IP アドレスを作成する場合、その IP アドレスのリソースは**ノード** リソース グループ内に作成する必要があります。 これらのリソースを分離する場合は、後述のセクション (「[ノード リソース グループの外で静的 IP アドレスを使用する](#use-a-static-ip-address-outside-of-the-node-resource-group)」) をご覧ください。

まず、[az aks show][az-aks-show] コマンドを使用してノード リソース グループ名を取得し、`--query nodeResourceGroup` クエリ パラメーターを追加します。 次の例では、リソース グループ名 *myResourceGroup* にある AKS クラスター名のノード リソース グループ *myAKSCluster* を取得しています｡

```azurecli-interactive
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

次に、[az network public-ip create][az-network-public-ip-create] コマンドを使用して、静的パブリック IP アドレスを作成します。 上記コマンドで取得したノードのリソース グループ名を指定して､その IP アドレス リソースに対して､*myAKSPublicIP* などの名前を指定します｡

```azurecli-interactive
az network public-ip create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --name myAKSPublicIP \
    --allocation-method static
```

次の出力例 (一部) に見られるように IP アドレスが表示されます。

```json
{
  "publicIp": {
    "dnsSettings": null,
    "etag": "W/\"6b6fb15c-5281-4f64-b332-8f68f46e1358\"",
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/Microsoft.Network/publicIPAddresses/myAKSPublicIP",
    "idleTimeoutInMinutes": 4,
    "ipAddress": "40.121.183.52",
    [...]
  }
}
```

このパブリック IP アドレスは､後で [az network public ip list][az-network-public-ip-list] コマンドを使用して取得することができます。 次の例に示すように、ノードのリソース グループ名と作成したパブリック IP を指定して、*ipAddress* に対するクエリを指定します。

```azurecli-interactive
$ az network public-ip show --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP --query ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-using-the-static-ip-address"></a>静的 IP アドレスを使用してサービスを作成する

静的パブリック IP アドレスを使用してサービスに作成するには､YAML マニフェストに `loadBalancerIP` プロパティと静的パブリック IP の値を追加します｡ `load-balancer-service.yaml` という名前のファイルを作成し、そこに以下の YAML をコピーします。 以前の手順で作成した独自のパブリック IP アドレスを指定します。

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-load-balancer
spec:
  loadBalancerIP: 40.121.183.52
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-load-balancer
```

`kubectl apply` コマンドを使用して、サービスとデプロイを作成します。

```console
kubectl apply -f load-balancer-service.yaml
```

## <a name="use-a-static-ip-address-outside-of-the-node-resource-group"></a>ノード リソース グループの外で静的 IP アドレスを使用する

Kubernetes 1.10 以降に対しては、ノード リソース グループの外で作成された静的 IP アドレスを使用することができます。 次の例に示すように、AKS クラスターで使用されるサービス プリンシパルには、該当する他のリソース グループへの委任されたアクセス許可が含まれている必要があります。

```azurecli-interactive
az role assignment create\
    --assignee <SP Client ID> \
    --role "Network Contributor" \
    --scope /subscriptions/<subscription id>/resourceGroups/<resource group name>
```

ノード リソース グループの外で IP アドレスを使用するには、Service 定義に注釈を追加します。 次の例では、*myResourceGroup* という名前のリソース グループに注釈が設定されています。 次の独自のリソース グループ名を指定します。

```yaml
apiVersion: v1
kind: Service
metadata:
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-resource-group: myResourceGroup
  name: azure-load-balancer
spec:
  loadBalancerIP: 40.121.183.52
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-load-balancer
```

## <a name="troubleshoot"></a>トラブルシューティング

Kubernetes のサービス マニフェストの *loadBalancerIP* プロパティに定義されている静的 IP アドレスが存在しないか､ノード リソース グループ内に作成されておらず、追加の委任も構成されていない場合、ロード バランサーのサービスの作成は失敗します。 トラブルシューティングを行うには、[kubectl describe][kubectl-describe] コマンドを使用してサービス作成イベントを確認します。 次の例に示すように、YAML マニフェストで指定されているサービス名を指定します。

```console
kubectl describe service azure-load-balancer
```

Kubernetes サービス リソースに関する情報が表示されます。 次の出力例の最後にある *イベント*は、*ユーザー指定の IP アドレスが見つからなかった*ことを示しています。 こうしたシナリオでは、ノードのリソース グループに静的パブリック IP アドレスを作成したこと、または Kubernetes のサービス マニフェストに指定されている IP アドレスが正しいことを確認します。

```
Name:                     azure-load-balancer
Namespace:                default
Labels:                   <none>
Annotations:              <none>
Selector:                 app=azure-load-balancer
Type:                     LoadBalancer
IP:                       10.0.18.125
IP:                       40.121.183.52
Port:                     <unset>  80/TCP
TargetPort:               80/TCP
NodePort:                 <unset>  32582/TCP
Endpoints:                <none>
Session Affinity:         None
External Traffic Policy:  Cluster
Events:
  Type     Reason                      Age               From                Message
  ----     ------                      ----              ----                -------
  Normal   CreatingLoadBalancer        7s (x2 over 22s)  service-controller  Creating load balancer
  Warning  CreatingLoadBalancerFailed  6s (x2 over 12s)  service-controller  Error creating load balancer (will retry): Failed to create load balancer for service default/azure-load-balancer: user supplied IP Address 40.121.183.52 was not found
```

## <a name="next-steps"></a>次の手順

アプリケーションへのネットワーク トラフィックに対する制御を強化することを目的として、[イングレス コント ローラーを作成][aks-ingress-basic]することもできます. You can also [create an ingress controller with a static public IP address][aks-static-ingress]。

<!-- LINKS - External -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

<!-- LINKS - Internal -->
[aks-faq-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[az-aks-show]: /cli/azure/aks#az-aks-show
[aks-ingress-basic]: ingress-basic.md
[aks-static-ingress]: ingress-static-ip.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[ip-sku]: ../virtual-network/virtual-network-ip-addresses-overview-arm.md#sku
