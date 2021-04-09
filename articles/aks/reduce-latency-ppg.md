---
title: 近接配置グループを使用して Azure Kubernetes Service (AKS) クラスターの待機時間を短縮する
description: 近接配置グループを使用して、AKS クラスターのワークロードの待機時間を短縮する方法を説明します。
services: container-service
manager: gwallace
ms.topic: article
ms.date: 10/19/2020
ms.openlocfilehash: c0c1bf83bf5a816debe61cab0ceab856bfbd062f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102181178"
---
# <a name="reduce-latency-with-proximity-placement-groups"></a>近接配置グループを使用して待機時間を短縮する

> [!Note]
> AKS 上で近接配置グループを使用すると、コロケーションはエージェント ノードにのみ適用されます。 ノード間、および対応するホストされたポッド間の待機時間が短縮されます。 コロケーションは、クラスターのコントロール プレーンの配置には影響しません。

Azure にアプリケーションをデプロイするときに、複数のリージョンまたは可用性ゾーンに仮想マシン (VM) インスタンスを分散すると、ネットワーク待機時間が発生し、アプリケーションの全体的なパフォーマンスに影響を及ぼす可能性があります。 近接配置グループは、Azure コンピューティング リソースが互いに物理的に近くに配置されるようにするために使用される論理的なグループ化です。 ゲーム、エンジニアリング シミュレーション、高頻度取引 (HFT) などの一部のアプリケーションでは、短い待機時間と、短時間で完了するタスクが必要です。 これらのようなハイ パフォーマンス コンピューティング (HPC) シナリオでは、クラスターのノード プールに[近接配置グループ](../virtual-machines/co-location.md#proximity-placement-groups) (PPG) を使用することを検討します。

## <a name="before-you-begin"></a>開始する前に

この記事では、Azure CLI バージョン 2.14 以降を実行している必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][azure-cli-install]に関するページを参照してください。

### <a name="limitations"></a>制限事項

* 近接配置グループは、最大で 1 つの可用性ゾーンにマップできます。
* ノード プールでは、Virtual Machine Scale Sets を使用して近接配置グループを関連付ける必要があります。
* ノード プールでは、ノード プールの作成時にのみ近接配置グループを関連付けることができます。

## <a name="node-pools-and-proximity-placement-groups"></a>ノード プールと近接配置グループ

近接配置グループでデプロイする最初のリソースは、特定のデータ センターに接続します。 同じ近接配置グループでデプロイされた他のリソースは、同じデータ センター内に併置されます。 近接配置グループを使用するリソースをすべて停止 (割り当て解除) または削除すると、接続は解除されます。

* 多くのノード プールを、1 つの近接配置グループに関連付けることができます。
* 1 つのノード プールは、1 つの近接配置グループとだけ関連付けることができます。

### <a name="configure-proximity-placement-groups-with-availability-zones"></a>可用性ゾーンを使用した近接配置グループの構成

> [!NOTE]
> 近接配置グループではノード プールで最大 1 つの可用性ゾーンを使用する必要がありますが、[ベースラインの Azure VM SLA 99.9%](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_9/) は、1 つのゾーン内の VM に対しても引き続き有効です。

近接配置グループはノード プールの概念であり、個々のノード プールに関連付けられています。 PPG リソースを使用しても、AKS コントロール プレーンの可用性には影響しません。 これは、ゾーンを使用してクラスターを設計する方法に影響を与える可能性があります。 クラスターが複数のゾーンに分散されるようにするには、次の設計をお勧めします。

* 3 つのゾーンを使用し、近接配置グループを関連付けずに、最初のシステム プールでクラスターをプロビジョニングします。 こうすることで、システム ポッドは、複数のゾーンに分散される専用ノード プールに配置されるようになります。
* 各プールに関連付けられている一意のゾーンと近接配置グループを使用して、追加のユーザー ノード プールを追加します。 たとえば、ゾーン 1 の nodepool1 と PPG1、ゾーン 2 の nodepool2 とPPG2、ゾーン 3 の nodepool3 と PPG3 です。 こうすることで、クラスター レベルで、ノードは複数のゾーンに分散され、個々のノード プールは専用の PPG リソースを持つ指定されたゾーン内に併置されます。

## <a name="create-a-new-aks-cluster-with-a-proximity-placement-group"></a>近接配置グループを使用して新しい AKS クラスターを作成する

次の例では、[az group create][az-group-create] コマンドを使用して、*myResourceGroup* という名前のリソース グループを *centralus* リージョンに作成しています。 次いで、*myAKSCluster* という名前の AKS クラスターを [az aks create][az-aks-create] コマンドを使用して作成しています。

高速ネットワークを使用すると、仮想マシンのネットワーク パフォーマンスが大幅に向上します。 近接配置グループと高速ネットワークを併用するのが理想的です。 既定では、AKS により[サポートされている仮想マシン インスタンス](../virtual-network/create-vm-accelerated-networking-cli.md?toc=/azure/virtual-machines/linux/toc.json#limitations-and-constraints)で高速ネットワークが使用されます。これには、2 つ以上の vCPU を持つほとんどの Azure 仮想マシンが含まれます。

最初のシステム ノード プールに関連付けられた近接配置グループを使用して、新しい AKS クラスターを作成します。

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```
次のコマンドを実行し、返された ID を格納します。

```azurecli-interactive
# Create proximity placement group
az ppg create -n myPPG -g myResourceGroup -l centralus -t standard
```

このコマンドでは、後の CLI コマンドに必要な *id* 値を含む出力が生成されます。

```output
{
  "availabilitySets": null,
  "colocationStatus": null,
  "id": "/subscriptions/yourSubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.Compute/proximityPlacementGroups/myPPG",
  "location": "centralus",
  "name": "myPPG",
  "proximityPlacementGroupType": "Standard",
  "resourceGroup": "myResourceGroup",
  "tags": {},
  "type": "Microsoft.Compute/proximityPlacementGroups",
  "virtualMachineScaleSets": null,
  "virtualMachines": null
}
```

次のコマンドの *myPPGResourceID* の値には、近接配置グループのリソース ID を使用します。

```azurecli-interactive
# Create an AKS cluster that uses a proximity placement group for the initial system node pool only. The PPG has no effect on the cluster control plane.
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --ppg myPPGResourceID
```

## <a name="add-a-proximity-placement-group-to-an-existing-cluster"></a>既存のクラスターに近接配置グループを追加する

新しいノード プールを作成することにより、既存のクラスターに近接配置グループを追加できます。 その後、必要に応じて、既存のワークロードを新しいノード プールに移行してから、元のノード プールを削除することができます。

前に作成したのと同じ近接配置グループを使用します。これにより、AKS クラスター内の両方のノード プールのエージェント ノードが、物理的に同じデータ センターに配置されます。

前に作成した近接配置グループのリソース ID を使用し、[`az aks nodepool add`][az-aks-nodepool-add] コマンドで新しいノード プールを追加します。

```azurecli-interactive
# Add a new node pool that uses a proximity placement group, use a --node-count = 1 for testing
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 1 \
    --ppg myPPGResourceID
```

## <a name="clean-up"></a>クリーンアップ

クラスターを削除するには、[`az group delete`][az-group-delete] コマンドを使用して AKS リソース グループを削除します。

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>次のステップ

* [近接配置グループ][proximity-placement-groups]についてさらに学習します。

<!-- LINKS - Internal -->
[azure-ad-rbac]: azure-ad-rbac.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
[az-aks-show]: /cli/azure/aks#az-aks-show
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[proximity-placement-groups]: ../virtual-machines/co-location.md#proximity-placement-groups
[az-aks-create]: /cli/azure/aks#az-aks-create
[system-pool]: ./use-system-pools.md
[az-aks-nodepool-add]: /cli/azure/aks/nodepool#az-aks-nodepool-add
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete