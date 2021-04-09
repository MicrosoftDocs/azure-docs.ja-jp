---
title: Azure Kubernetes Service (AKS) で Azure Files 用の Container Storage Interface (CSI) ドライバーを使用する
description: Azure Kubernetes Service (AKS) クラスターで Azure Files 用の Container Storage Interface (CSI) ドライバーを使用する方法について説明します。
services: container-service
ms.topic: article
ms.date: 08/27/2020
author: palma21
ms.openlocfilehash: 93f7f7a3c59beca362145ac16f7cf727df773f81
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102174063"
---
# <a name="use-azure-files-container-storage-interface-csi-drivers-in-azure-kubernetes-service-aks-preview"></a>Azure Kubernetes Service (AKS) で Azure Files の Container Storage Interface (CSI) ドライバーを使用する (プレビュー)

Azure Files の Container Storage Interface (CSI) ドライバーは、Azure Files 共有のライフサイクルを管理するために Azure Kubernetes Service (AKS) によって使用される [CSI 仕様](https://github.com/container-storage-interface/spec/blob/master/spec.md)準拠のドライバーです。

CSI は、Kubernetes のコンテナー化されたワークロードに任意のブロックおよびファイル ストレージ システムを公開する標準です。 CSI を採用および使用すると、Kubernetes のコア コードに触れたり、そのリリース サイクルを待つことなく、AKS が Kubernetes で新しいストレージ システムを公開したり、既存のストレージ システムを改良したりするプラグインを記述、デプロイ、反復処理できるようになります。

CSI ドライバーがサポートされる AKS クラスターを作成するには、「[AKS の Azure ディスクおよび Azure Files で CSI ドライバーを有効にする](csi-storage-drivers.md)」を参照してください。

>[!NOTE]
> *ツリー内ドライバー* とは、プラグインの新しい CSI ドライバーに対し、コア Kubernetes コードの一部である現在のストレージ ドライバーを指します。

## <a name="use-a-persistent-volume-with-azure-files"></a>Azure Files を使用した永続ボリュームを使用する

[永続ボリューム (PV)](concepts-storage.md#persistent-volumes)とは、Kubernetes ポッドで使用するためにプロビジョニングされているストレージの一部です。 PV は 1 つまたは複数のポッドで使用でき、動的または静的にプロビジョニングできます。 複数のポッドが同じストレージ ボリュームに同時アクセスする必要がある場合は、Azure Files を使用し、[サーバー メッセージ ブロック (SMB) プロトコル][smb-overview]を使用して接続します。 この記事では、AKS クラスターの複数のポッドで使用するために、Azure Files 共有を動的に作成する方法を示します。 静的プロビジョニングの場合は、[Azure Files 共有を含むボリュームを手動で作成して使用する](azure-files-volume.md)に関する記事を参照してください。

Kubernetes ボリュームの詳細については、[AKS でのアプリケーションのストレージ オプション][concepts-storage]に関するページを参照してください。

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="dynamically-create-azure-files-pvs-by-using-the-built-in-storage-classes"></a>組み込みのストレージ クラスを使用して Azure Files の PV を動的に作成する

ストレージ クラスを使用して、Azure Files 共有を作成する方法を定義します。 ストレージ アカウントは、ストレージ クラスと共に使用して Azure Files 共有を保持するために、[ノード リソース グループ][node-resource-group]内に自動的に作成されます。 *skuName* には、次のいずれかの [Azure Storage の冗長性 SKU][storage-skus] を選択します。

* **Standard_LRS**:標準のローカル冗長ストレージ
* **Standard_GRS**:標準の geo 冗長ストレージ
* **Standard_ZRS**:標準のゾーン冗長ストレージ
* **Standard_RAGRS**:標準の読み取りアクセス geo 冗長ストレージ
* **Premium_LRS**:Premium ローカル冗長ストレージ

> [!NOTE]
> Azure Files は Azure Premium Storage をサポートしています。 Premium ファイル共有の最小サイズは 100 GB です。

AKS でストレージ CSI ドライバーを使用する場合は、Azure Files の CSI ストレージ ドライバーを使用する 2 つの組み込み `StorageClasses` が追加されています。 追加の CSI ストレージ クラスは、ツリー内の既定のストレージ クラスと共にクラスターで作成されます。

- `azurefile-csi`:Azure Standard Storage を使用して Azure Files 共有を作成します。
- `azurefile-csi-premium`:Azure Premium Storage を使用して Azure Files 共有を作成します。

両方のストレージ クラスの再利用ポリシーによって、それぞれの PV が削除されたときに、基になる Azure Files 共有が削除されます。 ストレージ クラスによってファイル共有を拡張可能にする構成も行われるため、必要なのは、永続ボリューム要求 (PVC) を新しいサイズで編集することだけです。

これらのストレージ クラスを使用するには、[PVC](concepts-storage.md#persistent-volume-claims) とそれらを参照して使用するポッドを作成します。 PVC を使用して、ストレージ クラスに基づいてストレージを自動的にプロビジョニングします。 PVC は、事前に作成されたいずれかのストレージ クラスまたはユーザー定義のストレージ クラスを使用して、目的の SKU とサイズの Azure Files 共有を作成できます。 ポッド定義を作成するとき、必要なストレージを要求するために PVC が指定されます。

[kubectl apply][kubectl-apply] コマンドを使用して、[現在の日付を `outfile` に出力する PVC とポッドの例](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/statefulset.yaml)を作成します。

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/pvc-azurefile-csi.yaml
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/nginx-pod-azurefile.yaml

persistentvolumeclaim/pvc-azurefile created
pod/nginx-azurefile created
```

ポッドが実行中の状態になったら、次のコマンドを実行して、出力に `outfile` が含まれていることを確認することによって、ファイル共有が正しくマウントされていることを検証できます。

```console
$ kubectl exec nginx-azurefile -- ls -l /mnt/azurefile

total 29
-rwxrwxrwx 1 root root 29348 Aug 31 21:59 outfile
```

## <a name="create-a-custom-storage-class"></a>カスタム ストレージ クラスを作成する

既定のストレージ クラスは最も一般的なシナリオに適合しますが、すべてに適合するわけではありません。 場合によっては、独自のストレージ クラスを独自のパラメーターを使用してカスタマイズすることもできます。 たとえば、次のマニフェストを使用して、ファイル共有の `mountOptions` を構成します。

Kubernetes でマウントされたファイル共有の場合、*fileMode* と *dirMode* の既定値は *0777* です。 ストレージ クラス オブジェクトでは、さまざまなマウント オプションを指定できます。

`azure-file-sc.yaml` という名前のファイルを作成し、次のマニフェストの例を貼り付けます。

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: my-azurefile
provisioner: file.csi.azure.com
reclaimPolicy: Delete
volumeBindingMode: Immediate
allowVolumeExpansion: true
mountOptions:
  - dir_mode=0640
  - file_mode=0640
  - uid=0
  - gid=0
  - mfsymlinks
  - cache=strict # https://linux.die.net/man/8/mount.cifs
  - nosharesock
parameters:
  skuName: Standard_LRS
```

[kubectl apply][kubectl-apply] コマンドを使用してストレージ クラスを作成します。

```console
kubectl apply -f azure-file-sc.yaml

storageclass.storage.k8s.io/my-azurefile created
```

Azure Files の CSI ドライバーでは、[永続ボリュームおよび基になるファイル共有のスナップショット](https://kubernetes-csi.github.io/docs/snapshot-restore-feature.html)の作成がサポートされています。

[kubectl apply][kubectl-apply] コマンドを使用して、[ボリューム スナップショット クラス](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/snapshot/volumesnapshotclass-azurefile.yaml)を作成します。

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/snapshot/volumesnapshotclass-azurefile.yaml

volumesnapshotclass.snapshot.storage.k8s.io/csi-azurefile-vsc created
```

[このチュートリアルの始めに動的に作成した](#dynamically-create-azure-files-pvs-by-using-the-built-in-storage-classes) PVC (`pvc-azurefile`) から、[ボリューム スナップショット](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/snapshot/volumesnapshot-azurefile.yaml) を作成します。


```bash
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/snapshot/volumesnapshot-azurefile.yaml


volumesnapshot.snapshot.storage.k8s.io/azurefile-volume-snapshot created
```

スナップショットが正しく作成されたことを確認します。

```bash
$ kubectl describe volumesnapshot azurefile-volume-snapshot

Name:         azurefile-volume-snapshot
Namespace:    default
Labels:       <none>
Annotations:  API Version:  snapshot.storage.k8s.io/v1beta1
Kind:         VolumeSnapshot
Metadata:
  Creation Timestamp:  2020-08-27T22:37:41Z
  Finalizers:
    snapshot.storage.kubernetes.io/volumesnapshot-as-source-protection
    snapshot.storage.kubernetes.io/volumesnapshot-bound-protection
  Generation:        1
  Resource Version:  955091
  Self Link:         /apis/snapshot.storage.k8s.io/v1beta1/namespaces/default/volumesnapshots/azurefile-volume-snapshot
  UID:               c359a38f-35c1-4fb1-9da9-2c06d35ca0f4
Spec:
  Source:
    Persistent Volume Claim Name:  pvc-azurefile
  Volume Snapshot Class Name:      csi-azurefile-vsc
Status:
  Bound Volume Snapshot Content Name:  snapcontent-c359a38f-35c1-4fb1-9da9-2c06d35ca0f4
  Ready To Use:                        false
Events:                                <none>
```

## <a name="resize-a-persistent-volume"></a>永続ボリュームのサイズを変更する

PVC で大きいボリュームを要求できます。 PVC オブジェクトを編集し、より大きいサイズを指定します。 この変更により、PV の基になるボリュームの拡張がトリガーされます。

> [!NOTE]
> 要求を満たすために新しい PV が作成されることはありません。 代わりに、既存のボリュームのサイズが変更されます。

AKS では、組み込みの `azurefile-csi` ストレージ クラスは既に拡張をサポートしているので、[このストレージ クラスで前に作成した PVC](#dynamically-create-azure-files-pvs-by-using-the-built-in-storage-classes) を使用します。 この PVC は 100 Gi のファイル共有を要求しました。 これを確認するには、次のコマンドを実行します。

```console 
$ kubectl exec -it nginx-azurefile -- df -h /mnt/azurefile

Filesystem                                                                                Size  Used Avail Use% Mounted on
//f149b5a219bd34caeb07de9.file.core.windows.net/pvc-5e5d9980-da38-492b-8581-17e3cad01770  100G  128K  100G   1% /mnt/azurefile
```

`spec.resources.requests.storage` フィールドを増やして、PVC を拡張します。

```console
$ kubectl patch pvc pvc-azurefile --type merge --patch '{"spec": {"resources": {"requests": {"storage": "200Gi"}}}}'

persistentvolumeclaim/pvc-azurefile patched
```

PVC とポッド内のファイル システムの両方に新しいサイズが表示されることを確認します。

```console
$ kubectl get pvc pvc-azurefile
NAME            STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS    AGE
pvc-azurefile   Bound    pvc-5e5d9980-da38-492b-8581-17e3cad01770   200Gi      RWX            azurefile-csi   64m

$ kubectl exec -it nginx-azurefile -- df -h /mnt/azurefile
Filesystem                                                                                Size  Used Avail Use% Mounted on
//f149b5a219bd34caeb07de9.file.core.windows.net/pvc-5e5d9980-da38-492b-8581-17e3cad01770  200G  128K  200G   1% /mnt/azurefile
```


## <a name="nfs-file-shares"></a>NFS ファイル共有
[Azure Files では、NFS v4.1 プロトコルがサポートされるようになりました](../storage/files/storage-files-how-to-create-nfs-shares.md)。 Azure Files での NFS 4.1 のサポートによって、サービスとしてのフル マネージド NFS ファイル システムがお客様に提供されます。これは、可用性が高く耐久性に優れた、分散型で回復力のあるストレージ プラットフォーム上に構築されています。

 このオプションは、インプレース データ更新を使用するランダム アクセス ワークロードに合わせて最適化されており、完全な POSIX ファイル システムのサポートを提供します。 このセクションでは、AKS クラスター上で NFS 共有を Azure File CSI ドライバーと共に使用する方法について説明します。

プレビュー段階での[制限事項](../storage/files/storage-files-compare-protocols.md#limitations)と[利用可能なリージョン](../storage/files/storage-files-compare-protocols.md#regional-availability)を必ず確認してください。

### <a name="register-the-allownfsfileshares-preview-feature"></a>`AllowNfsFileShares` プレビュー機能を登録する

NFS 4.1 を活用するファイル共有を作成するには、お使いのサブスクリプションで `AllowNfsFileShares` 機能フラグを有効にする必要があります。

`AllowNfsFileShares` 機能フラグは、次の例のとおり、[az feature register][az-feature-register] コマンドを使用して登録します。

```azurecli-interactive
az feature register --namespace "Microsoft.Storage" --name "AllowNfsFileShares"
```

状態が *[登録済み]* と表示されるまでに数分かかります。 登録の状態は、[az feature list][az-feature-list] コマンドで確認できます。

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.Storage/AllowNfsFileShares')].{Name:name,State:properties.state}"
```

準備ができたら、[az provider register][az-provider-register] コマンドを使用して、*Microsoft.Storage* リソース プロバイダーの登録を更新します。

```azurecli-interactive
az provider register --namespace Microsoft.Storage
```

### <a name="create-a-storage-account-for-the-nfs-file-share"></a>NFS ファイル共有のストレージ アカウントを作成する

NFS 共有をサポートするために、次の構成で [`Premium_LRS` Azure ストレージ アカウントを作成](../storage/files/storage-how-to-create-file-share.md)します。
- アカウントの種類: FileStorage
- 安全な転送が必須 (HTTPS トラフィックのみを有効にする): false
- [ファイアウォールと仮想ネットワーク] でお使いのエージェント ノードの仮想ネットワークを選択します。そのため、MC_ リソース グループにストレージ アカウントを作成することが適している場合があります。

### <a name="create-nfs-file-share-storage-class"></a>NFS ファイル共有のストレージ クラスを作成する

以下のマニフェストのそれぞれのプレースホルダーを編集して、`nfs-sc.yaml` ファイルを保存します。

```yml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: azurefile-csi-nfs
provisioner: file.csi.azure.com
parameters:
  resourceGroup: EXISTING_RESOURCE_GROUP_NAME  # optional, required only when storage account is not in the same resource group as your agent nodes
  storageAccount: EXISTING_STORAGE_ACCOUNT_NAME
  protocol: nfs
```

ファイルを編集して保存した後、[kubectl apply][kubectl-apply] コマンドを使用してストレージ クラスを作成します。

```console
$ kubectl apply -f nfs-sc.yaml

storageclass.storage.k8s.io/azurefile-csi created
```

### <a name="create-a-deployment-with-an-nfs-backed-file-share"></a>NFS でサポートされるファイル共有を使用してデプロイを作成する
[kubectl apply][kubectl-apply] コマンドを使用して次のコマンドをデプロイすることにより、タイムスタンプをファイル `data.txt` に保存する[ステートフル セット](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/statefulset.yaml)の例をデプロイできます。

 ```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/statefulset.yaml

statefulset.apps/statefulset-azurefile created
```

次のコマンドを実行して、ボリュームの内容を検証します。

```console
$ kubectl exec -it statefulset-azurefile-0 -- df -h

Filesystem      Size  Used Avail Use% Mounted on
...
/dev/sda1                                                                                 29G   11G   19G  37% /etc/hosts
accountname.file.core.windows.net:/accountname/pvc-fa72ec43-ae64-42e4-a8a2-556606f5da38  100G     0  100G   0% /mnt/azurefile
...
```

>[!NOTE]
> NFS ファイル共有は Premium アカウントにあるため、ファイル共有の最小サイズは 100 GB であることに注意してください。 ストレージ サイズが小さい PVC を作成すると、「ファイル共有を作成できませんでした...サイズ (5)...」というエラーが発生する場合があります。


## <a name="windows-containers"></a>Windows コンテナー

Azure Files の CSI ドライバーは、Windows のノードとコンテナーもサポートしています。 Windows コンテナーを使用する場合は、[Windows コンテナーのチュートリアル](windows-container-cli.md)に従って、Windows ノード プールを追加してください。

Windows ノード プールを追加したら、`azurefile-csi` などの組み込みのストレージ クラスを使用するか、カスタムのストレージ クラスを作成します。 [kubectl apply][kubectl-apply] コマンドを使用して次のコマンドをデプロイすることにより、タイムスタンプをファイル `data.txt` に保存する [Windows ベースのステートフル セット](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/windows/statefulset.yaml)の例をデプロイできます。

 ```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/windows/statefulset.yaml

statefulset.apps/busybox-azurefile created
```

次のコマンドを実行して、ボリュームの内容を検証します。

```console
$ kubectl exec -it busybox-azurefile-0 -- cat c:\\mnt\\azurefile\\data.txt # on Linux/MacOS Bash
$ kubectl exec -it busybox-azurefile-0 -- cat c:\mnt\azurefile\data.txt # on Windows Powershell/CMD

2020-08-27 22:11:01Z
2020-08-27 22:11:02Z
2020-08-27 22:11:04Z
(...)
```

## <a name="next-steps"></a>次の手順

- Azure ディスクで CSI ドライバーを使用する方法を学習するには、[Azure ディスクでの CSI ドライバーの使用](azure-disk-csi.md)に関するページを参照してください。
- ストレージのベスト プラクティスの詳細については、「[Azure Kubernetes Service のストレージとバックアップに関するベスト プラクティス][operator-best-practices-storage]」を参照してください。


<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/
[smb-overview]: /windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview


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
[node-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[storage-skus]: ../storage/common/storage-redundancy.md