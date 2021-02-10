---
title: Azure Kubernetes Service (AKS) でホストベースの暗号化を有効にする
description: Azure Kubernetes Service (AKS) クラスターでホストベースの暗号化を構成する方法について説明します
services: container-service
ms.topic: article
ms.date: 01/27/2021
ms.openlocfilehash: ac28c698a766f1f3febaff582038906f658d58dd
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2021
ms.locfileid: "99071852"
---
# <a name="host-based-encryption-on-azure-kubernetes-service-aks-preview"></a>Azure Kubernetes Service (AKS) でのホストベースの暗号化 (プレビュー)

ホストベースの暗号化では、AKS エージェント ノードの VM の VM ホストに保存されたデータは、保存時に暗号化され、暗号化された状態でストレージ サービスに送られます。 つまり、一時ディスクは、プラットフォーム マネージド キーを使用して保存時に暗号化されます。 OS ディスクとデータ ディスクのキャッシュは、ディスクに設定された暗号化のタイプに応じて、プラットフォーム マネージド キーまたはカスタマー マネージド キーのどちらかを使用して保存時に暗号化されます。 既定では、AKS を使用する場合、OS ディスクとデータ ディスクはプラットフォーム マネージド キーを使用して保存時に暗号化されます。つまり、これらのディスクのキャッシュも既定で、プラットフォーム マネージド キーを使用して保存時に暗号化されます。  「[Azure Kubernetes Service (AKS) の Azure ディスクに独自のキー (BYOK) を使用する](azure-disk-customer-managed-keys.md)」の説明に従って、独自のマネージド キーを指定できます。 以後、これらのディスクのキャッシュも、この手順で指定するキーを使用して暗号化されるようになります。


## <a name="before-you-begin"></a>開始する前に

この機能は、クラスターの作成時またはノード プールの作成時にのみ設定できます。

> [!NOTE]
> ホストベースの暗号化は、Azure マネージド ディスクのサーバー側暗号化がサポートされている [Azure リージョン][supported-regions]で使用できますが、特定の[サポートされている VM サイズ][supported-sizes]しか使用できません。

### <a name="prerequisites"></a>前提条件

- `aks-preview` CLI 拡張機能 v0.4.73 以降のバージョンがインストールされていることを確認します。

### <a name="install-aks-preview-cli-extension"></a>aks-preview CLI 拡張機能をインストールする

ホストベースの暗号化に対応した AKS クラスターを作成するには、最新の *aks-preview* CLI 拡張機能が必要です。 [az extension add][az-extension-add] コマンドを使用して *aks-preview* Azure CLI 拡張機能をインストールするか、[az extension update][az-extension-update] コマンドを使用して使用可能な更新プログラムがあるかどうかを確認します。

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="limitations"></a>制限事項

- 新しいノード プールまたは新しいクラスターでのみ有効にできます。
- Azure マネージド ディスクのサーバー側暗号化がサポートされている [Azure リージョン][supported-regions]で、特定の[サポートされている VM サイズ][supported-sizes]のみを使用して有効にできます。
- Virtual Machine Scale Sets (VMSS) の *VM セット タイプ* に基づいた AKS クラスターとノード プールが必要です。

## <a name="use-host-based-encryption-on-new-clusters-preview"></a>新しいクラスターでホストベースの暗号化を使用する (プレビュー)

クラスターの作成時に、ホストベースの暗号化を使用するようにクラスター エージェント ノードを構成します。 

```azurecli-interactive
az aks create --name myAKSCluster --resource-group myResourceGroup -s Standard_DS2_v2 -l westus2 --enable-encryption-at-host
```

ホストベースの暗号化を使用せずにクラスターを作成する場合は、`--enable-encryption-at-host` パラメーターを省略できます。

## <a name="use-host-based-encryption-on-existing-clusters-preview"></a>既存のクラスターでホストベースの暗号化を使用する (プレビュー)

新しいノード プールをクラスターに追加することによって、既存のクラスターでホストベースの暗号化を有効にできます。 `--enable-encryption-at-host` パラメーターを使用して、ホストベースの暗号化を使用するように新しいノード プールを構成します。

```azurecli
az aks nodepool add --name hostencrypt --cluster-name myAKSCluster --resource-group myResourceGroup -s Standard_DS2_v2 -l westus2 --enable-encryption-at-host
```

ホストベースの暗号化機能を使用せずに新しいノード プールを作成する場合は、`--enable-encryption-at-host` パラメーターを省略できます。

## <a name="next-steps"></a>次のステップ

[AKS クラスターのセキュリティのベスト プラクティス][best-practices-security]を確認します。[ホストベースの暗号化](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)の詳細を確認します。


<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[best-practices-security]: ./operator-best-practices-cluster-security.md
[supported-regions]: ../virtual-machines/disk-encryption.md#supported-regions
[supported-sizes]: ../virtual-machines/disk-encryption.md#supported-vm-sizes
[azure-cli-install]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
