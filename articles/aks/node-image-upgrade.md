---
title: Azure Kubernetes Service (AKS) ノード イメージのアップグレード
description: AKS クラスター ノードとノード プールのイメージをアップグレードする方法について説明します。
ms.service: container-service
ms.topic: conceptual
ms.date: 11/25/2020
ms.author: jpalma
ms.openlocfilehash: 83d7d48922806334e2b49494fe0ef1d15e1a7a6a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96531481"
---
# <a name="azure-kubernetes-service-aks-node-image-upgrade"></a>Azure Kubernetes Service (AKS) ノード イメージのアップグレード

AKS では、ノード上のイメージのアップグレードがサポートされているため、最新の OS とランタイムの更新プログラムを使用して最新の状態にすることができます。 AKS は、最新の更新プログラムを使用して 1 週間に 1 つの新しいイメージを提供するため、Linux または Windows の修正プログラムを含む最新の機能については、ノードのイメージを定期的にアップグレードすることをお勧めします。 この記事では、AKS クラスター ノード イメージをアップグレードする方法と、Kubernetes のバージョンをアップグレードせずにノード プール イメージを更新する方法について説明します。

AKS によって提供される最新のイメージの詳細については、[AKS のリリース ノート](https://github.com/Azure/AKS/releases)をご覧ください。

お使いのクラスターの Kubernetes バージョンのアップグレードの詳細については、「[AKS クラスターのアップグレード][upgrade-cluster]」をご覧ください。

> [!NOTE]
> AKS クラスターでは、ノードに仮想マシン スケール セットを使用する必要があります。

## <a name="check-if-your-node-pool-is-on-the-latest-node-image"></a>ノード プールが最新のノード イメージにあるかどうかを確認する

次のコマンドを使用して、ノード プールで使用可能な最新のノード イメージ バージョンを確認できます。 

```azurecli
az aks nodepool get-upgrades \
    --nodepool-name mynodepool \
    --cluster-name myAKSCluster \
    --resource-group myResourceGroup
```

出力には、次の例のように `latestNodeImageVersion` が表示されます。

```output
{
  "id": "/subscriptions/XXXX-XXX-XXX-XXX-XXXXX/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/agentPools/nodepool1/upgradeProfiles/default",
  "kubernetesVersion": "1.17.11",
  "latestNodeImageVersion": "AKSUbuntu-1604-2020.10.28",
  "name": "default",
  "osType": "Linux",
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.ContainerService/managedClusters/agentPools/upgradeProfiles",
  "upgrades": null
}
```

`nodepool1` の場合、使用可能な最新のノード イメージは `AKSUbuntu-1604-2020.10.28` です。 次のように実行することによって、ノード プールで使用中の現在のノード イメージ バージョンと最新バージョンを比較できるようになりました。

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --query nodeImageVersion
```

出力例を次に示します。

```output
"AKSUbuntu-1604-2020.10.08"
```

したがって、この例では、現在のイメージ バージョン `AKSUbuntu-1604-2020.10.08` から最新バージョンの `AKSUbuntu-1604-2020.10.28` にアップグレードすることができます。 

## <a name="upgrade-all-nodes-in-all-node-pools"></a>すべてのノード プールのすべてのノードをアップグレードする

ノード イメージのアップグレードは、`az aks upgrade` で実行されます。 ノード イメージをアップグレードするには、次のコマンドを使用します。

```azurecli
az aks upgrade \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-image-only
```

アップグレード中に、次の `kubectl` コマンドを使用してノード イメージの状態を確認し、ラベルを取得し、現在のノード イメージ情報をフィルターで除外します。

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

アップグレードが完了したら、`az aks show` を使用して、更新されたノード プールの詳細を取得します。 現在のノード イメージが `nodeImageVersion` プロパティに表示されます。

```azurecli
az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster
```

## <a name="upgrade-a-specific-node-pool"></a>特定のノード プールのアップグレード

ノード プールのイメージをアップグレードすることは、クラスター上のイメージをアップグレードすることと似ています。

Kubernetes クラスターをアップグレードせずにノード プールの OS イメージを更新するには、次の例の `--node-image-only` オプションを使用します。

```azurecli
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-image-only
```

アップグレード中に、次の `kubectl` コマンドを使用してノード イメージの状態を確認し、ラベルを取得し、現在のノード イメージ情報をフィルターで除外します。

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

アップグレードが完了したら、`az aks nodepool show` を使用して、更新されたノード プールの詳細を取得します。 現在のノード イメージが `nodeImageVersion` プロパティに表示されます。

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool
```

## <a name="upgrade-node-images-with-node-surge"></a>ノード サージを使用したノード イメージのアップグレード

ノード イメージのアップグレード プロセスを高速化するために、カスタマイズ可能なノード サージ値を使用して、ノード イメージをアップグレードすることができます。 既定では、AKS は 1 つの追加ノードを使用してアップグレードを構成します。

アップグレードの速度を上げる場合は、`--max-surge` の値を使用して、アップグレードがより速く完了するように、アップグレードに使用するノードの数を構成します。 さまざまな `--max-surge` 設定のトレードオフの詳細については、「[ノード サージ アップグレードのカスタマイズ][max-surge]」を参照してください。

次のコマンドは、ノード イメージのアップグレードを実行するための最大サージ値を設定します。

```azurecli
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --max-surge 33% \
    --node-image-only \
    --no-wait
```

アップグレード中に、次の `kubectl` コマンドを使用してノード イメージの状態を確認し、ラベルを取得し、現在のノード イメージ情報をフィルターで除外します。

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

`az aks nodepool show` を使用して、更新されたノード プールの詳細を取得します。 現在のノード イメージが `nodeImageVersion` プロパティに表示されます。

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool
```

## <a name="next-steps"></a>次のステップ

- 最新のノード イメージの詳細については、[AKS のリリース ノート](https://github.com/Azure/AKS/releases)をご覧ください。
- 「[AKS クラスターのアップグレード][upgrade-cluster]」によって Kubernetes バージョンをアップグレードする方法を確認します。
- [GitHub Actions でクラスターとノード プールのアップグレードを自動的に適用します][github-schedule]
- 複数のノード プールの詳細と、[複数のノード プールの作成と管理][use-multiple-node-pools]によってノード プールをアップグレードする方法を確認します。

<!-- LINKS - internal -->
[upgrade-cluster]: upgrade-cluster.md
[github-schedule]: node-upgrade-github-actions.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[max-surge]: upgrade-cluster.md#customize-node-surge-upgrade
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
