---
title: Azure Kubernetes Service (AKS) クラスターのスケーリング
description: Azure Kubernetes Service (AKS) クラスターでノードの数をスケーリングする方法について説明します。
services: container-service
ms.topic: article
ms.date: 09/16/2020
ms.openlocfilehash: fdb61bf090351894329c24eb1a3c73d627e622e8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102173768"
---
# <a name="scale-the-node-count-in-an-azure-kubernetes-service-aks-cluster"></a>Azure Kubernetes Service (AKS) クラスターでノードの数をスケーリングする

アプリケーションに必要なリソースが変化した場合は、AKS クラスターを手動でスケーリングし、異なる数のノードを実行できます。 スケール ダウンするときには、実行中のアプリケーションの中断を最小限に抑えるために、ノードは慎重に[切断およびドレインされます][kubernetes-drain]。 スケールアップすると、AKS では、ノードが Kubernetes クラスターによって `Ready` とマークされるまで待機してから、それらに基づいてポッドがスケジュールされます。

## <a name="scale-the-cluster-nodes"></a>クラスター ノードのスケーリング

最初に、[az aks show][az-aks-show] コマンドを使用してノード プールの *名前* を取得します。 次の例では、*myResourceGroup* リソース グループ内の *myAKSCluster* という名前のクラスターのノード プール名を取得します。

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query agentPoolProfiles
```

次の出力例は、*name* が *nodepool1* であることを示しています。

```output
[
  {
    "count": 1,
    "maxPods": 110,
    "name": "nodepool1",
    "osDiskSizeGb": 30,
    "osType": "Linux",
    "storageProfile": "ManagedDisks",
    "vmSize": "Standard_DS2_v2"
  }
]
```

[az aks scale][az-aks-scale] コマンドを使用してクラスター ノードをスケーリングします。 次の例では、*myAKSCluster* という名前のクラスターを単一のノードにスケーリングします。 *nodepool1* など、前のコマンドから返された独自の `--nodepool-name` を指定します。

```azurecli-interactive
az aks scale --resource-group myResourceGroup --name myAKSCluster --node-count 1 --nodepool-name <your node pool name>
```

次の出力例は、*agentPoolProfiles* セクションに示されているように、クラスターが正常に 1 つのノードにスケーリングされたことを示しています。

```json
{
  "aadProfile": null,
  "addonProfiles": null,
  "agentPoolProfiles": [
    {
      "count": 1,
      "maxPods": 110,
      "name": "nodepool1",
      "osDiskSizeGb": 30,
      "osType": "Linux",
      "storageProfile": "ManagedDisks",
      "vmSize": "Standard_DS2_v2",
      "vnetSubnetId": null
    }
  ],
  [...]
}
```


## <a name="scale-user-node-pools-to-0"></a>`User` ノード プールを 0 にスケーリングする

常に実行中のノードを必要とする `System` ノード プールとは異なり、`User` ノード プールでは 0 にスケーリングできます。 システムとユーザー ノード プールの違いについては、「[システムおよびユーザー ノード プール](use-system-pools.md)」を参照してください。

ユーザー プールを 0 にスケーリングするには、上記の `az aks scale` コマンドの代わりに [az aks nodepool scale][az-aks-nodepool-scale] を使用し、ノード数として 0 を設定します。


```azurecli-interactive
az aks nodepool scale --name <your node pool name> --cluster-name myAKSCluster --resource-group myResourceGroup  --node-count 0 
```

また、[クラスター オートスケーラー](cluster-autoscaler.md)の `--min-count` パラメーターを 0 に設定することによって、`User` ノード プールを 0 ノードに自動スケーリングすることもできます。

## <a name="next-steps"></a>次の手順

この記事では、AKS クラスターを手動でスケールしてノード数を増減しました。 [クラスターの自動スケーラー][cluster-autoscaler]を使用してクラスターを自動的にスケーリングすることもできます。

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[cluster-autoscaler]: cluster-autoscaler.md
[az-aks-nodepool-scale]: /cli/azure/aks/nodepool#az-aks-nodepool-scale