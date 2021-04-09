---
title: Azure Kubernetes Service (AKS) で Azure ディスク用の Container Storage Interface (CSI) ドライバーを使用する
description: Azure Kubernetes Service (AKS) クラスターで Azure ディスク用の Container Storage Interface (CSI) ドライバーを使用する方法について説明します。
services: container-service
ms.topic: article
ms.date: 08/27/2020
author: palma21
ms.openlocfilehash: 2b4079b6d4eb39b65a7a60cd4d149c7748ab39ce
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102178883"
---
# <a name="use-the-azure-disk-container-storage-interface-csi-drivers-in-azure-kubernetes-service-aks-preview"></a>Azure Kubernetes Service (AKS) で Azure ディスクの Container Storage Interface (CSI) ドライバーを使用する (プレビュー)
Azure ディスクの Container Storage Interface (CSI) ドライバーは、Azure ディスクのライフサイクルを管理するために Azure Kubernetes Service (AKS) によって使用される [CSI 仕様](https://github.com/container-storage-interface/spec/blob/master/spec.md)準拠のドライバーです。

CSI は、Kubernetes のコンテナー化されたワークロードに任意のブロックおよびファイル ストレージ システムを公開する標準です。 CSI を採用および使用すると、Kubernetes のコア コードに触れたり、そのリリース サイクルを待つことなく、AKS が Kubernetes で新しいストレージ システムを公開したり、既存のストレージ システムを改良したりするプラグインを記述、デプロイ、反復処理できるようになります。

CSI ドライバーがサポートされる AKS クラスターを作成するには、[AKS で Azure ディスクおよび Azure Files 用の CSI ドライバーを有効にする方法](csi-storage-drivers.md)に関する記事を参照してください。

>[!NOTE]
> "*ツリー内ドライバー*" とは、プラグインの新しい CSI ドライバーに対し、コア Kubernetes コードの一部である現在のストレージ ドライバーを指します。

## <a name="use-csi-persistent-volumes-with-azure-disks"></a>Azure ディスクを含む CSI 永続ボリュームを使用する

[永続ボリューム](concepts-storage.md#persistent-volumes) (PV) とは、Kubernetes ポッドで使用するためにプロビジョニングされているストレージの一部です。 PV は 1 つまたは複数のポッドで使用でき、動的または静的にプロビジョニングできます。 この記事では、AKS クラスター内の単一のポッドによって使用するために Azure ディスクの PV を動的に作成する方法を説明します。 静的プロビジョニングの場合は、[Azure ディスクを含むボリュームを手動で作成して使用する方法](azure-disk-volume.md)に関する記事を参照してください。

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

Kubernetes ボリュームの詳細については、[AKS でのアプリケーションのストレージ オプション][concepts-storage]に関するページを参照してください。

## <a name="dynamically-create-azure-disk-pvs-by-using-the-built-in-storage-classes"></a>組み込みのストレージ クラスを使用して Azure ディスクの PV を動的に作成する

ストレージ クラスは、保存の単位を永続ボリュームを使用して動的に作成する方法を定義します。 Kubernetes ストレージ クラスの詳細については、[Kubernetes ストレージ クラス][kubernetes-storage-classes]に関する記事を参照してください。 AKS でストレージ CSI ドライバーを使用する場合は、Azure ディスクの CSI ストレージ ドライバーを使用する 2 つの組み込み `StorageClasses` が追加されています。 追加の CSI ストレージ クラスは、ツリー内の既定のストレージ クラスと共にクラスターで作成されます。

- `managed-csi`:Azure Standard SSD ローカル冗長ストレージ (LRS) を使用して、マネージド ディスクを作成します。
- `managed-csi-premium`:Azure Premium LRS を使用してマネージド ディスクを作成します。

両方のストレージ クラスの再利用ポリシーによって、それぞれの PV が削除されると、基になる Azure ディスクが削除されます。 ストレージ クラスでは、PV は拡張可能なようにも構成されます。 ユーザーは、新しいサイズで永続ボリューム要求 (PVC) を編集することだけが必要です。

これらのストレージ クラスを利用するには、[PVC](concepts-storage.md#persistent-volume-claims) とそれらを参照して使用するポッドを作成します。 PVC を使用して、ストレージ クラスに基づいてストレージを自動的にプロビジョニングします。 PVC は、事前に作成されたいずれかのストレージ クラスまたはユーザー定義のストレージ クラスを使用して、目的の SKU とサイズの Azure マネージド ディスクを作成できます。 ポッド定義を作成するとき、必要なストレージを要求するために PVC が指定されます。

[kubectl apply][kubectl-apply] コマンドを使用して、ポッドとそれぞれの PVC の例を作成します。

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/pvc-azuredisk-csi.yaml
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/nginx-pod-azuredisk.yaml

persistentvolumeclaim/pvc-azuredisk created
pod/nginx-azuredisk created
```

ポッドが実行中状態になったら、`test.txt` という名前の新しいファイルを作成します。

```bash
$ kubectl exec nginx-azuredisk -- touch /mnt/azuredisk/test.txt
```

次のコマンドを実行して、出力に `test.txt` ファイルが表示されることを確認することによって、ディスクが正しくマウントされていることを検証できます。

```console
$ kubectl exec nginx-azuredisk -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

## <a name="create-a-custom-storage-class"></a>カスタム ストレージ クラスを作成する

既定のストレージ クラスは最も一般的なシナリオに適合しますが、すべてに適合するわけではありません。 場合によっては、独自のストレージ クラスを独自のパラメーターを使用してカスタマイズすることもできます。 たとえば、`volumeBindingMode` クラスの変更が必要になるシナリオがあります。

既定のストレージ クラスでは、PVC が作成された直後に発生することを保証する `volumeBindingMode: Immediate` クラスが使用されます。 ノード プールがトポロジに制約されている場合 (たとえば、可用性ゾーンを使用している場合)、PV は、ポッドのスケジューリング要件 (この場合は特定のゾーン) を認識しないでバインドまたはプロビジョニングされます。

このシナリオに対処するには、`volumeBindingMode: WaitForFirstConsumer` を使用できます。これにより、PV のバインドとプロビジョニングは、PVC を使用するポッドが作成されるまで遅延されます。 このようにして、PV は、ポッドのスケジューリング制約によって指定された可用性ゾーン (または他のトポロジ) に準拠し、その中にプロビジョニングされます。

`sc-azuredisk-csi-waitforfirstconsumer.yaml` というファイルを作成し、次のマニフェストを貼り付けます。
ストレージ クラスは `managed-csi` ストレージ クラスと同じですが、`volumeBindingMode` クラスは異なります。

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azuredisk-csi-waitforfirstconsumer
provisioner: disk.csi.azure.com
parameters:
  skuname: StandardSSD_LRS 
allowVolumeExpansion: true
reclaimPolicy: Delete
volumeBindingMode: WaitForFirstConsumer
```

[kubectl apply][kubectl-apply] コマンドを使用してストレージ クラスを作成し、`sc-azuredisk-csi-waitforfirstconsumer.yaml` ファイルを指定します。

```console
$ kubectl apply -f sc-azuredisk-csi-waitforfirstconsumer.yaml

storageclass.storage.k8s.io/azuredisk-csi-waitforfirstconsumer created
```

## <a name="volume-snapshots"></a>ボリューム スナップショット

Azure ディスクの CSI ドライバーでは、[永続ボリュームのスナップショット](https://kubernetes-csi.github.io/docs/snapshot-restore-feature.html)の作成がサポートされています。 この機能の一部として、ドライバーでは、`incremental` パラメーターに設定されている値 (既定では true) に応じて、"*完全*" または "[*増分*" スナップショット](../virtual-machines/disks-incremental-snapshots.md)のいずれかを実行できます。

すべてのパラメーターの詳細については、[ボリューム スナップショット クラスのパラメーター](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/docs/driver-parameters.md#volumesnapshotclass)に関するページを参照してください。

### <a name="create-a-volume-snapshot"></a>ボリューム スナップショットを作成する

この機能の例として、[kubectl apply][kubectl-apply] コマンドを使用して[ボリューム スナップショット クラス](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/storageclass-azuredisk-snapshot.yaml)を作成します。

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/storageclass-azuredisk-snapshot.yaml

volumesnapshotclass.snapshot.storage.k8s.io/csi-azuredisk-vsc created
```

次に、[このチュートリアルの始めに動的に作成した](#dynamically-create-azure-disk-pvs-by-using-the-built-in-storage-classes) PVC (`pvc-azuredisk`) から、[ボリューム スナップショット](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/azuredisk-volume-snapshot.yaml)を作成しましょう。


```bash
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/azuredisk-volume-snapshot.yaml

volumesnapshot.snapshot.storage.k8s.io/azuredisk-volume-snapshot created
```

スナップショットが正しく作成されたことを確認します。

```bash
$ kubectl describe volumesnapshot azuredisk-volume-snapshot

Name:         azuredisk-volume-snapshot
Namespace:    default
Labels:       <none>
Annotations:  API Version:  snapshot.storage.k8s.io/v1beta1
Kind:         VolumeSnapshot
Metadata:
  Creation Timestamp:  2020-08-27T05:27:58Z
  Finalizers:
    snapshot.storage.kubernetes.io/volumesnapshot-as-source-protection
    snapshot.storage.kubernetes.io/volumesnapshot-bound-protection
  Generation:        1
  Resource Version:  714582
  Self Link:         /apis/snapshot.storage.k8s.io/v1beta1/namespaces/default/volumesnapshots/azuredisk-volume-snapshot
  UID:               dd953ab5-6c24-42d4-ad4a-f33180e0ef87
Spec:
  Source:
    Persistent Volume Claim Name:  pvc-azuredisk
  Volume Snapshot Class Name:      csi-azuredisk-vsc
Status:
  Bound Volume Snapshot Content Name:  snapcontent-dd953ab5-6c24-42d4-ad4a-f33180e0ef87
  Creation Time:                       2020-08-31T05:27:59Z
  Ready To Use:                        true
  Restore Size:                        10Gi
Events:                                <none>
```

### <a name="create-a-new-pvc-based-on-a-volume-snapshot"></a>ボリューム スナップショットを基にして新しい PVC を作成する

ボリューム スナップショットを基にして新しい PVC を作成することができます。 前のステップで作成したスナップショットを使用して、[新しい PVC](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/pvc-azuredisk-snapshot-restored.yaml) と、それを使用する[新しいポッド](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/nginx-pod-restored-snapshot.yaml)を作成します。

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/pvc-azuredisk-snapshot-restored.yaml

$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/nginx-pod-restored-snapshot.yaml

persistentvolumeclaim/pvc-azuredisk-snapshot-restored created
pod/nginx-restored created
```

最後に、内容を調べて、前に作成したものと同じ PVC であることを確認します。

```console
$ kubectl exec nginx-restored -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

予想どおり、前に作成した `test.txt` ファイルをまだ見ることができます。

## <a name="clone-volumes"></a>ボリュームを複製する

複製されたボリュームは、既存の Kubernetes ボリュームの複製として定義されます。 Kubernetes でのボリュームの複製の詳細については、[ボリュームの複製](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#volume-cloning)に関する概念ドキュメントを参照してください。

Azure ディスク用の CSI ドライバーでは、ボリュームの複製がサポートされています。 デモンストレーションのため、[前に作成した](#dynamically-create-azure-disk-pvs-by-using-the-built-in-storage-classes) `azuredisk-pvc` の[複製されたボリューム](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml)と、[それを使用するために新しいポッド](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml)を作成します。


```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/cloning/pvc-azuredisk-cloning.yaml

$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml

persistentvolumeclaim/pvc-azuredisk-cloning created
pod/nginx-restored-cloning created
```

次のコマンドを実行し、作成したファイル `test.txt` がまだ表示されていることを確認することで、複製されたボリュームの内容を確認できます。

```console
$ kubectl exec nginx-restored-cloning -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

## <a name="resize-a-persistent-volume"></a>永続ボリュームのサイズを変更する

代わりに、PVC で大きいボリュームを要求できます。 PVC オブジェクトを編集し、より大きいサイズを指定します。 この変更により、PV の基になるボリュームの拡張がトリガーされます。

> [!NOTE]
> 要求を満たすために新しい PV が作成されることはありません。 代わりに、既存のボリュームのサイズが変更されます。

AKS では、組み込みの `managed-csi` ストレージ クラスは既に拡張に対応しているので、[このストレージ クラスで前に作成した PVC](#dynamically-create-azure-disk-pvs-by-using-the-built-in-storage-classes) を使用します。 PVC では、10 Gi の永続ボリュームが要求されました。 これを確認するには、次のコマンドを実行します。

```console 
$ kubectl exec -it nginx-azuredisk -- df -h /mnt/azuredisk

Filesystem      Size  Used Avail Use% Mounted on
/dev/sdc        9.8G   42M  9.8G   1% /mnt/azuredisk
```

> [!IMPORTANT]
> 現在、Azure ディスク CSI ドライバーでは、ポッドが関連付けられていない (および、ボリュームが特定のノードにマウントされていない) PVC のみがサポートされます。

そのため、先ほど作成したポッドを削除します。

```console
$ kubectl delete -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/nginx-pod-azuredisk.yaml

pod "nginx-azuredisk" deleted
```

`spec.resources.requests.storage` フィールドを大きくして、PVC を拡張します。

```console
$ kubectl patch pvc pvc-azuredisk --type merge --patch '{"spec": {"resources": {"requests": {"storage": "15Gi"}}}}'

persistentvolumeclaim/pvc-azuredisk patched
```

ボリュームが大きくなっていることを確認します。

```console
$ kubectl get pv

NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                                     STORAGECLASS   REASON   AGE
pvc-391ea1a6-0191-4022-b915-c8dc4216174a   15Gi       RWO            Delete           Bound    default/pvc-azuredisk                     managed-csi             2d2h
(...)
```

> [!NOTE]
> ポッドが再び関連付けられるまで、PVC に新しいサイズは反映されません。

新しいポッドを作成します。

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/nginx-pod-azuredisk.yaml

pod/nginx-azuredisk created
```

最後に、PVC のサイズとポッドの内容を確認します。
```console
$ kubectl get pvc pvc-azuredisk
NAME            STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
pvc-azuredisk   Bound    pvc-391ea1a6-0191-4022-b915-c8dc4216174a   15Gi       RWO            managed-csi    2d2h

$ kubectl exec -it nginx-azuredisk -- df -h /mnt/azuredisk
Filesystem      Size  Used Avail Use% Mounted on
/dev/sdc         15G   46M   15G   1% /mnt/azuredisk
```

## <a name="shared-disk"></a>共有ディスク

[Azure 共有ディスク](../virtual-machines/disks-shared.md)は、Azure ディスクをエージェント ノードに同時に接続できるようにする Azure マネージド ディスク機能です。 マネージド ディスクを複数のエージェント ノードに接続すると、たとえば、新規にデプロイしたり、既存のクラスター化されたアプリケーションを Azure に移行したりできます。

> [!IMPORTANT] 
> 現在のところ、生のブロック デバイス (`volumeMode: Block`) のみ、Azure ディスク CSI ドライバーでサポートされています。 アプリケーションでは、生のブロック デバイスとして露出する共有ディスクでの書き込み、読み取り、ロック、キャッシュ、マウント、フェンスの調整と制御を管理する必要があります。

共有ディスク ストレージ クラスと PVC が含まれる次のコマンドをコピーし、`shared-disk.yaml` という名前のファイルを作成してみましょう。

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: managed-csi-shared
provisioner: disk.csi.azure.com
parameters:
  skuname: Premium_LRS  # Currently shared disk is only available with premium SSD
  maxShares: "2"
  cachingMode: None  # ReadOnly cache is not available for premium SSD with maxShares>1
reclaimPolicy: Delete
---
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: pvc-azuredisk-shared
spec:
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 256Gi  # minimum size of shared disk is 256GB (P15)
  volumeMode: Block
  storageClassName: managed-csi-shared
```

[kubectl apply][kubectl-apply] コマンドを使用してストレージ クラスを作成し、`shared-disk.yaml` ファイルを指定します。

```console
$ kubectl apply -f shared-disk.yaml

storageclass.storage.k8s.io/managed-csi-shared created
persistentvolumeclaim/pvc-azuredisk-shared created
``` 

それでは、次のコマンドをコピーし、`deployment-shared.yml` という名前のファイルを作成しましょう。

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: nginx
  name: deployment-azuredisk
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
      name: deployment-azuredisk
    spec:
      containers:
        - name: deployment-azuredisk
          image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
          volumeDevices:
            - name: azuredisk
              devicePath: /dev/sdx
      volumes:
        - name: azuredisk
          persistentVolumeClaim:
            claimName: pvc-azuredisk-shared
```

[kubectl apply][kubectl-apply] コマンドを使用してデプロイを作成し、`deployment-shared.yml` ファイルを指定します。

```console
$ kubectl apply -f deployment-shared.yml

deployment/deployment-azuredisk created
```

最後に、ポッド内のブロック デバイスを確認しましょう。

```console
# kubectl exec -it deployment-sharedisk-7454978bc6-xh7jp sh
/ # dd if=/dev/zero of=/dev/sdx bs=1024k count=100
100+0 records in
100+0 records out/s
```

## <a name="windows-containers"></a>Windows コンテナー

Azure ディスクの CSI ドライバーでは、Windows のノードとコンテナーもサポートされています。 Windows コンテナーを使用する場合は、[Windows コンテナーのチュートリアル](windows-container-cli.md)に従って、Windows ノード プールを追加してください。

Windows ノード プールを追加した後は、`managed-csi` などの組み込みストレージ クラスを使用できます。 [kubectl apply][kubectl-apply] コマンドを使用して次のコマンドをデプロイすることにより、タイムスタンプをファイル `data.txt` に保存する [Windows ベースのステートフル セット](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/windows/statefulset.yaml)の例をデプロイできます。

 ```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/windows/statefulset.yaml

statefulset.apps/busybox-azuredisk created
```

次のコマンドを実行して、ボリュームの内容を検証できます。

```console
$ kubectl exec -it busybox-azuredisk-0 -- cat c:\\mnt\\azuredisk\\data.txt # on Linux/MacOS Bash
$ kubectl exec -it busybox-azuredisk-0 -- cat c:\mnt\azuredisk\data.txt # on Windows Powershell/CMD

2020-08-27 08:13:41Z
2020-08-27 08:13:42Z
2020-08-27 08:13:44Z
(...)
```

## <a name="next-steps"></a>次の手順

- Azure Files で CSI ドライバーを使用する方法を学習するには、[Azure Files での CSI ドライバーの使用](azure-files-csi.md)に関するページを参照してください。
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