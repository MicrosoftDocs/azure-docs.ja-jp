---
title: ロード バランサーで静的 IP を使用する
titleSuffix: Azure Kubernetes Service
description: Azure Kubernetes Service (AKS) ロード バランサーで静的 IP アドレスを使用する方法を説明します｡
services: container-service
ms.topic: article
ms.date: 11/14/2020
ms.openlocfilehash: 102df48ca22fb996e0f4d9c402b8ce8f0fa80f2c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102509474"
---
# <a name="use-a-static-public-ip-address-and-dns-label-with-the-azure-kubernetes-service-aks-load-balancer"></a>Azure Kubernetes Service (AKS) ロード バランサーで静的パブリック IP アドレスと DNS ラベルを使用する

既定では、AKS クラスターによって作成されたロード バランサーのリソースに割り当てられているパブリック IP アドレスは､そのリソースの有効期間の間のみ有効です。 Kubernetes サービスを削除すると、関連付けられているロード バランサーと IP アドレスも削除されます。 デプロイし直された Kubernetes サービスに対して特定の IP アドレスを割り当てるか､あるいは IP アドレスを保持する場合は、静的パブリック IP アドレスを作成して､使用することができます。

この記事では、静的パブリック IP アドレスを作成して、Kubernetes サービスに割り当てる方法を示します。

## <a name="before-you-begin"></a>開始する前に

この記事は、AKS クラスターがすでに存在していることを前提としています。 AKS クラスターが必要な場合は、[Azure CLI を使用した場合][aks-quickstart-cli]または [Azure portal を使用した場合][aks-quickstart-portal]の AKS のクイックスタートを参照してください。

また、Azure CLI バージョン 2.0.59 以降がインストールされ、構成されている必要もあります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][install-azure-cli]に関するページを参照してください。

この記事では、*Standard* SKU IP を *Standard* SKU ロード バランサーと共に使用する方法について説明します。 詳しくは、「[Azure における IP アドレスの種類と割り当て方法][ip-sku]」をご覧ください。

## <a name="create-a-static-ip-address"></a>静的 IP アドレスを作成する

[az network public-ip create][az-network-public-ip-create] コマンドを使用して、静的パブリック IP アドレスを作成します。 以下では、*myResourceGroup* リソース グループに *myAKSPublicIP* という名前の静的 IP リソースを作成します。

```azurecli-interactive
az network public-ip create \
    --resource-group myResourceGroup \
    --name myAKSPublicIP \
    --sku Standard \
    --allocation-method static
```

> [!NOTE]
> AKS クラスターで *Basic* SKU ロード バランサーを使用している場合は、パブリック IP を定義するときに *sku* パラメーターに *Basic* を使用します。 *Basic* SKU IP のみが *Basic* SKU ロード バランサーで動作し、*Standard* SKU IP のみが *Standard* SKU ロード バランサーで動作します。 

次の出力例 (一部) に見られるように IP アドレスが表示されます。

```json
{
  "publicIp": {
    ...
    "ipAddress": "40.121.183.52",
    ...
  }
}
```

このパブリック IP アドレスは､後で [az network public ip list][az-network-public-ip-list] コマンドを使用して取得することができます。 次の例に示すように、ノードのリソース グループ名と作成したパブリック IP を指定して、*ipAddress* に対するクエリを指定します。

```azurecli-interactive
$ az network public-ip show --resource-group myResourceGroup --name myAKSPublicIP --query ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-using-the-static-ip-address"></a>静的 IP アドレスを使用してサービスを作成する

サービスを作成する前に、AKS クラスターで使用されるクラスター ID に、該当する他のリソース グループへの委任されたアクセス許可が含まれていることを確認してください。 次に例を示します。

```azurecli-interactive
az role assignment create \
    --assignee <Client ID> \
    --role "Network Contributor" \
    --scope /subscriptions/<subscription id>/resourceGroups/<resource group name>
```

> [!IMPORTANT]
> 送信 IP をカスタマイズした場合は、クラスター ID に、送信パブリック IP とこの受信パブリック IP の両方に対するアクセス許可があることを確認します。

静的パブリック IP アドレスを使用して *LoadBalancer* サービスを作成するには､YAML マニフェストに `loadBalancerIP` プロパティと静的パブリック IP の値を追加します｡ `load-balancer-service.yaml` という名前のファイルを作成し、そこに以下の YAML をコピーします。 以前の手順で作成した独自のパブリック IP アドレスを指定します。 次の例では、*myResourceGroup* という名前のリソース グループに注釈も設定されます。 次の独自のリソース グループ名を指定します。

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

`kubectl apply` コマンドを使用して、サービスとデプロイを作成します。

```console
kubectl apply -f load-balancer-service.yaml
```

## <a name="apply-a-dns-label-to-the-service"></a>サービスに DNS ラベルを適用する

サービスで動的または静的パブリック IP アドレスを使用している場合は、サービス注釈 `service.beta.kubernetes.io/azure-dns-label-name` を使用して、公開 DNS ラベルを設定できます。 これにより、Azure のパブリック DNS サーバーとトップ レベル ドメインを使用して、サービスの完全修飾ドメイン名が発行されます。 注釈の値は Azure の場所内で一意である必要があるため、十分に修飾されたラベルを使用することをお勧めします。   

その後、Azure は`<location>.cloudapp.azure.com` （場所は選択した地域）などの規定のサブネットを、指定した名前に自動的に追加して、完全修飾 DNS 名を作成します。 次に例を示します。

```yaml
apiVersion: v1
kind: Service
metadata:
  annotations:
    service.beta.kubernetes.io/azure-dns-label-name: myserviceuniquelabel
  name: azure-load-balancer
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-load-balancer
```

> [!NOTE] 
> 独自のドメインでサービスを発行するには、[Azure DNS][azure-dns-zone] と [外部 DNS][external-dns] プロジェクトをご参照ください。

## <a name="troubleshoot"></a>トラブルシューティング

Kubernetes のサービス マニフェストの *loadBalancerIP* プロパティに定義されている静的 IP アドレスが存在しないか､ノード リソース グループ内に作成されておらず、追加の委任も構成されていない場合、ロード バランサーのサービスの作成は失敗します。 トラブルシューティングを行うには、[kubectl describe][kubectl-describe] コマンドを使用してサービス作成イベントを確認します。 次の例に示すように、YAML マニフェストで指定されているサービス名を指定します。

```console
kubectl describe service azure-load-balancer
```

Kubernetes サービス リソースに関する情報が表示されます。 次の出力例の最後にある *イベント* は、*ユーザー指定の IP アドレスが見つからなかった* ことを示しています。 こうしたシナリオでは、ノードのリソース グループに静的パブリック IP アドレスを作成したこと、または Kubernetes のサービス マニフェストに指定されている IP アドレスが正しいことを確認します。

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

## <a name="next-steps"></a>次のステップ

アプリケーションへのネットワーク トラフィックに対する制御を強化することを目的として、[イングレス コント ローラーを作成][aks-ingress-basic]することもできます。 また[静的パブリック IP アドレスを使用してイングレス コント ローラーを作成する][aks-static-ingress]こともできます。

<!-- LINKS - External -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[azure-dns-zone]: https://azure.microsoft.com/services/dns/
[external-dns]: https://github.com/kubernetes-sigs/external-dns

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
[ip-sku]: ../virtual-network/public-ip-addresses.md#sku
