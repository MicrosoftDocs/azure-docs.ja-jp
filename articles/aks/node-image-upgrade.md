---
title: Azure Kubernetes Service (AKS) ノード イメージのアップグレード
description: AKS クラスター ノードとノード プールのイメージをアップグレードする方法について説明します。
author: laurenhughes
ms.author: lahugh
ms.service: container-service
ms.topic: conceptual
ms.date: 08/17/2020
ms.openlocfilehash: 744e62f8a2207cff400a96069fc6ea82866f6e2d
ms.sourcegitcommit: 420c30c760caf5742ba2e71f18cfd7649d1ead8a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2020
ms.locfileid: "89055687"
---
# <a name="azure-kubernetes-service-aks-node-image-upgrade"></a>Azure Kubernetes Service (AKS) ノード イメージのアップグレード

AKS では、ノード上のイメージのアップグレードがサポートされているため、最新の OS とランタイムの更新プログラムを使用して最新の状態にすることができます。 AKS は、最新の更新プログラムを使用して 1 週間に 1 つの新しいイメージを提供するため、Linux または Windows の修正プログラムを含む最新の機能については、ノードのイメージを定期的にアップグレードすることをお勧めします。 この記事では、AKS クラスター ノード イメージをアップグレードする方法と、Kubernetes のバージョンをアップグレードせずにノード プール イメージを更新する方法について説明します。

AKS によって提供される最新のイメージについて知りたい場合、詳細については [AKS のリリース ノート](https://github.com/Azure/AKS/releases)をご覧ください。

お使いのクラスターの Kubernetes バージョンのアップグレードの詳細については、「[AKS クラスターのアップグレード][upgrade-cluster]」をご覧ください。

## <a name="limitations"></a>制限事項

* AKS クラスターでは、ノードに仮想マシン スケール セットを使用する必要があります。

## <a name="install-the-aks-cli-extension"></a>AKS CLI 拡張機能をインストールする

次のコア CLI バージョンがリリースされる前にノード イメージ アップグレードを使用するには、*aks-preview* CLI 拡張機能が必要です。 [az extension add][az-extension-add] コマンドを使用した後、[az extension update][az-extension-update] コマンドを使用して、使用可能な更新プログラムがあるかどうかを確認します。

```azurecli
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

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

Kubernetes クラスターのアップグレードを実行せずにノード プールの OS イメージを更新するには、次の例の `--node-image-only` オプションを使用します。

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
- [Azure Kubernetes Service (AKS) の Linux ノードにセキュリティとカーネルの更新を適用します][security-update]
- 複数のノード プールの詳細と、[複数のノード プールの作成と管理][use-multiple-node-pools]によってノード プールをアップグレードする方法を確認します。

<!-- LINKS - internal -->
[upgrade-cluster]: upgrade-cluster.md
[security-update]: node-updates-kured.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[max-surge]: upgrade-cluster.md#customize-node-surge-upgrade-preview
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
