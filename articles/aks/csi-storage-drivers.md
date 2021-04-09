---
title: Azure Kubernetes Service (AKS) で Container Storage Interface (CSI) ドライバーを有効にする
description: Azure Kubernetes Service (AKS) クラスターで Azure ディスクと Azure Files 用の Container Storage Interface (CSI) ドライバーを有効にする方法について説明します。
services: container-service
ms.topic: article
ms.date: 08/27/2020
author: palma21
ms.openlocfilehash: fa40ab22f0c1ebf47bb490a50f782a848d1441e1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102182113"
---
# <a name="enable-container-storage-interface-csi-drivers-for-azure-disks-and-azure-files-on-azure-kubernetes-service-aks-preview"></a>Azure Kubernetes Service (AKS) で Azure ディスクと Azure Files 用の Container Storage Interface (CSI) ドライバーを有効にする (プレビュー)

Container Storage Interface (CSI) は、Kubernetes のコンテナー化されたワークロードに任意のブロックおよびファイル ストレージ システムを公開する標準です。 CSI を採用および使用すると、Kubernetes のコア コードを触ったり、そのリリース サイクルを待つことなく、Azure Kubernetes Service (AKS) が Kubernetes で新しい、あるいは既存のストレージ システムを公開するプラグインを記述、デプロイ、反復処理できるようになります。

AKS での CSI ストレージ ドライバーのサポートにより、次をネイティブに使用できるようになります。
- Kubernetes の *DataDisk* リソースの作成に使用する [*Azure ディスク*](azure-disk-csi.md)。 ディスクには、高パフォーマンス SSD を使用する Azure Premium Storage、または標準 HDD または Standard SSD を使用する Azure Standard Storage を使用できます。 ほとんどの運用ワークロードと開発ワークロードでは Premium Storage を使用します。 Azure ディスクは *ReadWriteOnce* としてマウントされるため、1 つのポッドでしか使用できません。 複数のポッドから同時にアクセスできるストレージ ボリュームについては、Azure Files を使用します。
- Azure Storage アカウントで使用する SMB 3.0 共有をポッドにマウントするために使用する [*Azure Files*](azure-files-csi.md)。 Azure Files を使用すると、複数のノードとポッド間でデータを共有できます。 Azure Files には、標準 HDD を使用する Azure Standard Storage または高パフォーマンス SSD を使用する Azure Premium Storage を使用できます。

> [!IMPORTANT]
> Kubernetes バージョン 1.21 以降では、既定で CSI ドライバーのみが使用されます。 これらのドライバーは、Kubernetes でのストレージ サポートの未来です。
>
> "*ツリー内ドライバー*" とは、プラグインの新しい CSI ドライバーに対し、コア Kubernetes コードの一部である現在のストレージ ドライバーを指します。

## <a name="limitations"></a>制限事項

- この機能は、クラスターの作成時にのみ設定できます。
- CSI ドライバーをサポートする最小の Kubernetes マイナー バージョンは、v1.17 です。
- プレビュー期間中の既定のストレージ クラスは、[同じくツリー内ストレージ クラス](concepts-storage.md#storage-classes)です。 この機能が一般公開されると、既定のストレージ クラスは `managed-csi` ストレージ クラスになり、ツリー内ストレージ クラスは削除されます。
- 最初のプレビュー段階では、Azure CLI のみがサポートされます。

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="register-the-enableazurediskfilecsidriver-preview-feature"></a>`EnableAzureDiskFileCSIDriver` プレビュー機能を登録する

Azure ディスクおよび Azure Files に CSI ドライバーを使用する AKS クラスターを作成するには、お使いのサブスクリプションで `EnableAzureDiskFileCSIDriver` 機能フラグを有効にする必要があります。

`EnableAzureDiskFileCSIDriver` 機能フラグは、次の例のとおり、[az feature register][az-feature-register] コマンドを使用して登録します。

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "EnableAzureDiskFileCSIDriver"
```

状態が *[登録済み]* と表示されるまでに数分かかります。 登録の状態は、[az feature list][az-feature-list] コマンドで確認できます。

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableAzureDiskFileCSIDriver')].{Name:name,State:properties.state}"
```

準備ができたら、[az provider register][az-provider-register] コマンドを使用して、*Microsoft.ContainerService* リソース プロバイダーの登録を更新します。

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="install-aks-preview-cli-extension"></a>aks-preview CLI 拡張機能をインストールする

CSI ストレージ ドライバーを使用する AKS クラスターまたはノード プールを作成するには、最新の *aks-preview* Azure CLI 拡張機能が必要です。 *aks-preview* Azure CLI 拡張機能は、[az extension add][az-extension-add] コマンドを使用してインストールします。 または、[az extension update][az-extension-update] コマンドを使用すると、使用可能な更新プログラムをインストールできます。

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
``` 


## <a name="create-a-new-cluster-that-can-use-csi-storage-drivers"></a>CSI ストレージ ドライバーを使用する新しいクラスターを作成する

次の CLI コマンドを使用すると、Azure ディスクと Azure Files 用に CSI ストレージ ドライバーを使用する新しいクラスターを作成できます。 `--aks-custom-headers` フラグを使用して `EnableAzureDiskFileCSIDriver` 機能を設定します。

Azure リソース グループを作成します。

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location canadacentral
```

CSI ストレージ ドライバーをサポートする AKS クラスターを作成します。

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g MyResourceGroup -n MyManagedCluster --network-plugin azure -k 1.17.9 --aks-custom-headers EnableAzureDiskFileCSIDriver=true
```

CSI ストレージ ドライバーではなく、クラスター ツリー内ストレージ ドライバーを作成する場合は、カスタム `--aks-custom-headers` パラメーターを省略します。


このノードにアタッチできる Azure ディスク ベース ボリュームの数を確認するには、次を実行します。

```console
$ kubectl get nodes
aks-nodepool1-25371499-vmss000000
aks-nodepool1-25371499-vmss000001
aks-nodepool1-25371499-vmss000002

$ echo $(kubectl get CSINode <NODE NAME> -o jsonpath="{.spec.drivers[1].allocatable.count}")
8
```

## <a name="next-steps"></a>次の手順

- Azure ディスクで CSI ドライブを使用する場合は、[Azure ディスクでの CSI ドライバーの使用](azure-disk-csi.md)に関するページを参照してください。
- Azure Files で CSI ドライブを使用する場合は、[Azure Files での CSI ドライバーの使用](azure-files-csi.md)に関するページを参照してください。
- ストレージのベスト プラクティスの詳細については、「[Azure Kubernetes Service のストレージとバックアップに関するベスト プラクティス][operator-best-practices-storage]」を参照してください。

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

<!-- LINKS - internal -->
[azure-disk-volume]: azure-disk-volume.md
[azure-files-pvc]: azure-files-dynamic-pv.md
[premium-storage]: ../virtual-machines/disks-types.md
[az-disk-list]: /cli/azure/disk#az-disk-list
[az-snapshot-create]: /cli/azure/snapshot#az-snapshot-create
[az-disk-create]: /cli/azure/disk#az-disk-create
[az-disk-show]: /cli/azure/disk#az-disk-show
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
[storage-class-concepts]: concepts-storage.md#storage-classes
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register