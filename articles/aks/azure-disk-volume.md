---
title: Azure Kubernetes Service (AKS) のポッド用の静的ボリュームを作成する
description: Azure Kubernetes Service (AKS) のポッドで使用するための Azure ディスクを含むボリュームを手動で作成する方法について説明します
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 03/01/2019
ms.author: mlearned
ms.openlocfilehash: 9017c8cf721fbb9c493dc18da769b9d6e83ddf05
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/25/2019
ms.locfileid: "67616131"
---
# <a name="manually-create-and-use-a-volume-with-azure-disks-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) で Azure ディスクを含むボリュームの手動での作成および使用

コンテナーベースのアプリケーションは、データへのアクセスとデータの永続化の手段として、外部データ ボリュームが必要になることが少なくありません。 単一のポッドでストレージにアクセスする必要がある場合は、Azure ディスクを使用してアプリケーションが使用できるネイティブ ボリュームを表すことができます。 この記事では、手動で Azure ディスクを作成し、AKS のポッドにアタッチする方法を示します。

> [!NOTE]
> Azure ディスクは、一度に 1 つのポッドにのみマウントできます。 複数のポッド間で永続的なボリュームを共有する必要がある場合は、[Azure Files][azure-files-volume] を使用してください。

Kubernetes ボリュームの詳細については、[AKS でのアプリケーションのストレージ オプション][concepts-storage]に関するページを参照してください。

## <a name="before-you-begin"></a>開始する前に

この記事は、AKS クラスターがすでに存在していることを前提としています。 AKS クラスターが必要な場合は、[Azure CLI を使用した場合][aks-quickstart-cli]または [Azure portal を使用した場合][aks-quickstart-portal]の AKS のクイックスタートを参照してください。

また、Azure CLI バージョン 2.0.59 以降がインストールされ、構成されている必要もあります。 バージョンを確認するには、 `az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「 [Azure CLI のインストール][install-azure-cli]」を参照してください。

## <a name="create-an-azure-disk"></a>Azure ディスクを作成する

AKS で使用するための Azure ディスクを作成する場合は、**ノード** リソース グループ内にディスク リソースを作成することができます。 この方法により、AKS クラスターがディスク リソースにアクセスおよび管理できるようになります。 代わりに独立したリソース グループにディスクを作成する場合は、クラスターの Azure Kubernetes Service (AKS) サービス プリンシパルに、ディスクのリソース グループに対する `Contributor` ロールを付与する必要があります。

この記事では、ノード リソース グループ内にディスクを作成します。 最初に、[az aks show][az-aks-show] コマンドを使用してリソース グループ名を取得し、`--query nodeResourceGroup` クエリ パラメーターを追加します。 次の例では、リソース グループ名 *myResourceGroup* にある AKS クラスター名のノード リソース グループ *myAKSCluster* を取得しています｡

```azurecli-interactive
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

ここで、[az disk create][az-disk-create] コマンドを使用してディスクを作成します。 上記コマンドで取得したノードのリソース グループ名を指定して､そのディスク リソースに対して､*myAKSDisk* などの名前を指定します｡ 次の例では、*20* GiB のディスクを作成し、作成後にそのディスクの ID を出力します。 Windows Server コンテナー (現在 AKS でプレビュー段階) で使用されるディスクを作成する必要がある場合は、ディスクを適切にフォーマットするために `--os-type windows` パラメーターを追加します。

```azurecli-interactive
az disk create \
  --resource-group MC_myResourceGroup_myAKSCluster_eastus \
  --name myAKSDisk \
  --size-gb 20 \
  --query id --output tsv
```

> [!NOTE]
> Azure ディスクは、特定サイズの SKU 単位で課金されます。 これらの SKU の範囲は、S4 または P4 ディスクの 32 GiB から、S80 または P80 ディスクの 32 TiB までです (プレビュー中)。 Premium 管理ディスクのスループットと IOPS パフォーマンスは、SKU と AKS クラスターのノードのインスタンスのサイズに依存します。 [Managed Disks の価格とパフォーマンス ][managed-disk-pricing-performance]に関するページを参照してください。

次の出力例に示すように、コマンドが正常に完了すると、ディスク リソース ID が表示されます。 このディスク ID は、次の手順でディスクをマウントするために使用します。

```console
/subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

## <a name="mount-disk-as-volume"></a>ディスクをボリュームとしてマウントする

Azure ディスクをポッドにマウントするには、コンテナーの指定でボリュームを構成します。次の内容で、`azure-disk-pod.yaml` という名前の新しいファイルを作成します。 前の手順で作成したディスクの名前で `diskName` を更新し、ディスク作成コマンドの出力に示されたディスク ID で `diskURI` を更新します。 必要な場合は、`mountPath` を更新します。これは Azure ディスクがポッドにマウントされているパスです。 Windows Server コンテナー (AKS では現在プレビュー段階) の場合、 *'D:'* などの Windows パス規則を使用して、*mountPath* を指定します。

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - image: nginx:1.15.5
    name: mypod
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
      - name: azure
        mountPath: /mnt/azure
  volumes:
      - name: azure
        azureDisk:
          kind: Managed
          diskName: myAKSDisk
          diskURI: /subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

`kubectl` コマンドを使用して、ポッドを作成します。

```console
kubectl apply -f azure-disk-pod.yaml
```

これで Azure ディスクが `/mnt/azure` にマウントされ、ポッドが稼働状態となりました。 `kubectl describe pod mypod` を使用すると、ディスクが正常にマウントされていることを確認できます。 次に示したのは、その出力例の抜粋です。コンテナーにマウントされたボリュームが表示されています。

```
[...]
Volumes:
  azure:
    Type:         AzureDisk (an Azure Data Disk mount on the host and bind mount to the pod)
    DiskName:     myAKSDisk
    DiskURI:      /subscriptions/<subscriptionID/resourceGroups/MC_myResourceGroupAKS_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
    Kind:         Managed
    FSType:       ext4
    CachingMode:  ReadWrite
    ReadOnly:     false
  default-token-z5sd7:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-z5sd7
    Optional:    false
[...]
Events:
  Type    Reason                 Age   From                               Message
  ----    ------                 ----  ----                               -------
  Normal  Scheduled              1m    default-scheduler                  Successfully assigned mypod to aks-nodepool1-79590246-0
  Normal  SuccessfulMountVolume  1m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "default-token-z5sd7"
  Normal  SuccessfulMountVolume  41s   kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "azure"
[...]
```

## <a name="next-steps"></a>次の手順

関連するベスト プラクティスについては、[AKS のストレージとバックアップに関するベスト プラクティス][operator-best-practices-storage]に関するページを参照してください。

AKS クラスターと Azure ディスクの操作の詳細については、[Azure ディスク対応の Kubernetes プラグイン][kubernetes-disks]に関するページを参照してください。

<!-- LINKS - external -->
[kubernetes-disks]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_disk/README.md
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

<!-- LINKS - internal -->
[az-disk-list]: /cli/azure/disk#az-disk-list
[az-disk-create]: /cli/azure/disk#az-disk-create
[az-group-list]: /cli/azure/group#az-group-list
[az-resource-show]: /cli/azure/resource#az-resource-show
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[install-azure-cli]: /cli/azure/install-azure-cli
[azure-files-volume]: azure-files-volume.md
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
