---
title: Azure Kubernetes Service (AKS) でシステム ノード プールを使用する
description: Azure Kubernetes Service (AKS) でシステム ノード プールを作成および管理する方法について学習します
services: container-service
ms.topic: article
ms.date: 06/18/2020
ms.author: mlearned
ms.custom: fasttrack-edit, devx-track-azurecli
ms.openlocfilehash: 9c53cb53517c4696a1bb47c2cb72335979d58d3a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102178832"
---
# <a name="manage-system-node-pools-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) でシステム ノード プールを管理する

Azure Kubernetes Service (AKS) で同じ構成のノードは、*ノード プール* にグループ化できます。 ノード プールには、お使いのアプリケーションを実行する基になる VM が含まれます。 お使いの AKS クラスターには、システム ノード プールとユーザー ノードプールの 2 つの異なるノード プールのモードがあります。 システム ノード プールは、`CoreDNS` や `metrics-server` などの重要なシステム ポッドをホストするという主要な目的を果たします。 ユーザー ノード プールは、アプリケーション ポッドをホストするという主要な目的を果たします。 ただし、AKS クラスター内のプールを 1 つだけにする場合は、システム ノード プールでアプリケーション ポッドをスケジュールすることができます。 各 AKS クラスターには、少なくとも 1 つのノードを含むシステム ノード プールが、少なくとも 1 つ含まれている必要があります。

> [!Important]
> 運用環境内のお使いの AKS クラスターで 1 つのシステム ノード プールを実行する場合、そのノード プールには少なくとも 3 つのノードを使用することをお勧めします。

## <a name="before-you-begin"></a>開始する前に

* Azure CLI バージョン 2.3.1 以降がインストールされて構成されている必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][install-azure-cli]に関するページを参照してください。

## <a name="limitations"></a>制限事項

システム ノード プールをサポートする AKS クラスターを作成および管理する場合には、次の制限があります。

* 「[Azure Kubernetes Service (AKS) のクォータ、仮想マシンのサイズの制限、およびリージョンの可用性][quotas-skus-regions]」を参照してください。
* AKS クラスターは、VM の種類である仮想マシン スケール セット、および *Standard* SKU ロード バランサーを使用して構築する必要があります。
* ノード プールの名前は、小文字の英数字のみを含めることができ、小文字で始める必要があります。 Linux のノード プールの長さは、1 から 12 文字の範囲内である必要があります。 Windows のノード プールの長さは、1 から 6 文字の範囲内である必要があります。
* ノード プール モードを設定するには、2020-03-01 以上の API バージョンを使用する必要があります。 2020-03-01 より前の API バージョンで作成されたクラスターにはユーザー ノード プールのみが含まれますが、[プール モードの更新手順](#update-existing-cluster-system-and-user-node-pools)に従って、システム ノード プールを含むように移行できます。
* ノード プールのモードは必須プロパティであり、ARM テンプレートまたは API 呼び出しを直接行う場合は、明示的に設定する必要があります。

## <a name="system-and-user-node-pools"></a>システムおよびユーザー ノード プール

システム ノード プールの場合、AKS はラベル **kubernetes.azure.com/mode: system** をそのノードに自動的に割り当てます。 これにより、AKS では、このラベルを含むノード プールでのシステム ポッドのスケジューリングが優先されます。 このラベルは、システム ノード プールでのアプリケーション ポッドのスケジュールを妨げません。 ただし、アプリケーション ポッドから重要なシステム ポッドを分離することをお勧めします。これにより、間違ったまたは悪意のあるアプリケーション ポッドが誤ってシステム ポッドを強制終了することを防ぐことができます。 この動作は、専用のシステム ノード プールを作成することで適用することができます。 システム ノード プールでアプリケーション ポッドがスケジュールされないようにするには、`CriticalAddonsOnly=true:NoSchedule` テイントを使用します。

システム ノード プールには、次の制限があります。

* システム プールの osType は、Linux である必要があります。
* ユーザー ノード プールの osType には、Linux または Windows が可能です。
* システム プールには少なくとも 1 つのノードを含める必要があり、ユーザー ノード プールには 0 以上のノードを含めることができます。
* システム ノード プールには、少なくとも 2 つの vCPU と 4 GB のメモリのがある VM SKU が必要です。
* システム ノード プールでは、[ポッドの最小値と最大値の式][maximum-pods]に関する説明のとおり、少なくともポッドを 30 サポートしている必要があります。
* スポット ノード プールには、ユーザー ノード プールが必要です。
* システム ノード プールをさらに追加したり、システム ノード プールとなるノード プールを変更したりしても、システム ポッドは自動的には移動 *されません*。 ノード プールをユーザー ノード プールに変更しても、システム ポッドは同じノード プール上で実行し続けることができます。 システム ポッドを実行中の、以前はシステム ノード プールだったノード プールを削除またはスケールダウンした場合、それらのシステム ポッドは、推奨されるスケジュールで新しいシステム ノード プールに再デプロイされます。

ノード プールには、次の操作を実行できます。

* 専用のシステム ノード プールを作成する (`mode:system` のノード プールに対してシステム ポッドをスケジュールすることを優先)
* 別のシステム ノード プールが AKS クラスター内にあり、それが代わりをする場合に、システム ノード プールをユーザー ノード プールに変更する。
* ユーザー ノード プールをシステム ノード プールに変更する。
* ユーザー ノード プールを削除する。
* 別のシステム ノード プールが AKS クラスター内にあり、それが代わりをする場合に、システム ノード プールを削除できる。
* AKS クラスターには、少なくとも 1 つのシステム ノード プールが必要であり、複数のシステム ノード プールが存在する場合がある。
* 既存のノード プールで変更できない各種の設定を変更したい場合は、新しいノード プールを作成して既存のものと置き換えることができる。 たとえば、新しい maxPods 設定で新しいノード プールを追加し、古いノード プールを削除する。

## <a name="create-a-new-aks-cluster-with-a-system-node-pool"></a>システム ノード プールを含む新しい AKS クラスターを作成する

新しい AKS クラスターを作成すると、1 つのノードを含むシステム ノード プールが自動作成されます。 最初のノード プールのモード型は、既定で system になります。 `az aks nodepool add` を使用して作成した新しいノード プールは、モード パラメーターを明示的に指定しない限り、ユーザー ノード プールになります。

次の例では、*myResourceGroup* という名前のリソース グループを *米国東部* リージョンに作成します。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

AKS クラスターを作成するには、[az aks create][az-aks-create] コマンドを使用します。 次の例では、1 つのノードが含まれる 1 つの専用システム プールを持つ、*myAKSCluster* という名前のクラスターを作成します。 ご自分の運用環境のワークロードでは、少なくともノードが 3 つあるシステム ノード プールを使用していることを確認してください。 この操作が完了するまでに数分かかる場合があります。

```azurecli-interactive
# Create a new AKS cluster with a single system pool
az aks create -g myResourceGroup --name myAKSCluster --node-count 1 --generate-ssh-keys
```

## <a name="add-a-dedicated-system-node-pool-to-an-existing-aks-cluster"></a>既存の AKS クラスターに専用システム ノード プールを追加する

> [!Important]
> ノード プールを作成した後は、CLI を使用してノード テイントを変更することができません。

既存の AKS クラスターには、システム ノード プールを 1 つ以上追加できます。 ユーザー ノード プールでアプリケーション ポッドをスケジュールし、システム ノード プールを重要なシステム ポッド専用にすることをお勧めします。 これにより、悪意のあるアプリケーション ポッドが誤ってシステムポッドを強制終了することを防ぐことができます。 システム ノード プールの `CriticalAddonsOnly=true:NoSchedule` [テイント][aks-taints]でこの動作を適用します。 

次のコマンドでは、モード型が system の専用ノード プールを既定のノード数 3 で追加します。

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name systempool \
    --node-count 3 \
    --node-taints CriticalAddonsOnly=true:NoSchedule \
    --mode System
```
## <a name="show-details-for-your-node-pool"></a>お使いのノード プールの詳細を表示する

お使いのノード プールの詳細を確認するには、次のコマンドを使用します。  

```azurecli-interactive
az aks nodepool show -g myResourceGroup --cluster-name myAKSCluster -n systempool
```

システム ノード プールには **System** 型のモードが定義され、ユーザー ノード プールには **User** 型のモードが定義されています。 システム プールの場合は、テイントが `CriticalAddonsOnly=true:NoSchedule` に設定されていることを確認します。これにより、このノード プールでアプリケーション ポッドがスケジュールされることを防ぐことができます。

```output
{
  "agentPoolType": "VirtualMachineScaleSets",
  "availabilityZones": null,
  "count": 1,
  "enableAutoScaling": null,
  "enableNodePublicIp": false,
  "id": "/subscriptions/yourSubscriptionId/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/agentPools/systempool",
  "maxCount": null,
  "maxPods": 110,
  "minCount": null,
  "mode": "System",
  "name": "systempool",
  "nodeImageVersion": "AKSUbuntu-1604-2020.06.30",
  "nodeLabels": {},
  "nodeTaints": [
    "CriticalAddonsOnly=true:NoSchedule"
  ],
  "orchestratorVersion": "1.16.10",
  "osDiskSizeGb": 128,
  "osType": "Linux",
  "provisioningState": "Failed",
  "proximityPlacementGroupId": null,
  "resourceGroup": "myResourceGroup",
  "scaleSetEvictionPolicy": null,
  "scaleSetPriority": null,
  "spotMaxPrice": null,
  "tags": null,
  "type": "Microsoft.ContainerService/managedClusters/agentPools",
  "upgradeSettings": {
    "maxSurge": null
  },
  "vmSize": "Standard_DS2_v2",
  "vnetSubnetId": null
}
```

## <a name="update-existing-cluster-system-and-user-node-pools"></a>既存のクラスターのシステムおよびユーザー ノード プールを更新する

> [!NOTE]
> システム ノード プール モードを設定するには、2020-03-01 以降の API バージョンを使用する必要があります。 2020-03-01 より前の API バージョンで作成されたクラスターには、結果としてユーザー ノード プールのみが含まれます。 以前のクラスターでシステム ノード プールの機能とメリットを使用するには、次のコマンドを使用して、既存のノード プールのモードを最新の Azure CLI バージョンに更新します。

システムおよびユーザー ノード プールのいずれもモードを変更できます。 システム ノード プールをユーザー プールに変更できるのは、AKS クラスターに別のシステム ノード プールが既に存在する場合のみです。

このコマンドは、システム ノード プールをユーザー ノード プールに変更します。

```azurecli-interactive
az aks nodepool update -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode user
```

このコマンドは、ユーザー ノード プールをシステム ノード プールに変更します。

```azurecli-interactive
az aks nodepool update -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode system
```

## <a name="delete-a-system-node-pool"></a>システムノードプールを削除する

> [!Note]
> API バージョン 2020-03-02 より前で AKS クラスターでシステム ノード プールを使用する場合、新しいシステム ノード プールを追加してから、元の既定のノード プールを削除してください。

お使いの AKS クラスターに少なくとも 2 つのシステム ノード プールがないと、そのうちの 1 つを削除できません。

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster -n mynodepool
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

クラスターを削除するには、[az group delete][az-group-delete] コマンドを使用して AKS リソース グループを削除します。

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```



## <a name="next-steps"></a>次のステップ

この記事では、AKS クラスターでシステム ノード プールを作成および管理する方法を学習しました。 複数のノード プールの使用方法の詳細については、[複数のノード プールの使用][use-multiple-node-pools]に関するページを参照してください。

<!-- EXTERNAL LINKS -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubernetes-labels]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/
[kubernetes-label-syntax]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#syntax-and-character-set

<!-- INTERNAL LINKS -->
[aks-taints]: use-multiple-node-pools.md#setting-nodepool-taints
[aks-windows]: windows-container-cli.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-nodepool-add]: /cli/azure/aks/nodepool#az-aks-nodepool-add
[az-aks-nodepool-list]: /cli/azure/aks/nodepool#az-aks-nodepool-list
[az-aks-nodepool-update]: /cli/azure/aks/nodepool#az-aks-nodepool-update
[az-aks-nodepool-upgrade]: /cli/azure/aks/nodepool#az-aks-nodepool-upgrade
[az-aks-nodepool-scale]: /cli/azure/aks/nodepool#az-aks-nodepool-scale
[az-aks-nodepool-delete]: /cli/azure/aks/nodepool#az-aks-nodepool-delete
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[gpu-cluster]: gpu-cluster.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[quotas-skus-regions]: quotas-skus-regions.md
[supported-versions]: supported-kubernetes-versions.md
[tag-limitation]: ../azure-resource-manager/management/tag-resources.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[vm-sizes]: ../virtual-machines/sizes.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[maximum-pods]: configure-azure-cni.md#maximum-pods-per-node
