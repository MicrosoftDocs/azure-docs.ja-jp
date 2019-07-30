---
title: 概念 - Azure Kubernetes サービス (AKS) におけるストレージ
description: Azure Kubernetes Service (AKS) のストレージについて、ボリューム、永続ボリューム、ストレージ クラス、要求などを説明します。
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: mlearned
ms.openlocfilehash: eb9141d363bdb09b5773f80dfc5a1c4b9b92728f
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67615807"
---
# <a name="storage-options-for-applications-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) でのアプリケーションのストレージ オプション

Azure Kubernetes Service (AKS) で実行されるアプリケーションで、データの格納や取得が必要になることがあります。 一部のアプリケーションのワークロードでは、このデータ格納のために、ノード上のローカルの高速ストレージを使用することができます (このノードはポッドが削除されると不要になります)。 その他のアプリケーションのワークロードでは、Azure プラットフォーム内の通常のデータ ボリューム上に永続的なストレージが必要になることがあります。 複数のポッドが同じデータ ボリュームを共有するか、またはポッドが別のノード上に再スケジュールされると、データ ボリュームに再接続する必要があります。 さらに、機密データまたはアプリケーション構成情報をポッドに挿入する必要がある場合もあります。

![Azure Kubernetes Service (AKS) クラスターでのアプリケーションのストレージ オプション](media/concepts-storage/aks-storage-options.png)

この記事では、AKS 内でアプリケーションにストレージを提供する主要な概念について説明します。

- [Volumes](#volumes)
- [永続ボリューム](#persistent-volumes)
- [ストレージ クラス](#storage-classes)
- [永続ボリューム要求](#persistent-volume-claims)

## <a name="volumes"></a>ボリューム

多くの場合、アプリケーションではデータの格納と取得を行う必要があります。 通常、Kubernetes では、破棄できる短期的なリソースとして個々のポッドが扱われるため、アプリケーションで必要に応じてデータを使用したり保存したりするためのさまざまなアプローチが提供されています。 "*ボリューム*" とは、複数のポッドにまたがり、アプリケーション ライフサイクルを通じて、データを格納、取得および保存する手段です。

データを格納して取得するための従来のボリュームは、Azure Storage を基盤とする Kubernetes リソースとして作成されます。 これらのデータ ボリュームを手動で作成してポッドに直接割り当てることも、Kubernetes で自動的に作成することもできます。 これらのデータ ボリュームには、Azure ディスクまたは Azure Files を使用できます。

- "*Azure ディスク*" は、Kubernetes *DataDisk* リソースを作成するために使用できます。 ディスクは、高パフォーマンス SSD に支えられた Azure Premium ストレージ、または標準 HHD に支えられた Azure Standard ストレージを使用できます。 ほとんどの運用ワークロードと開発ワークロードでは Premium ストレージを使用してください。 Azure ディスクは *ReadWriteOnce* としてマウントされるため、1 つのノードでしか使用できません。 ストレージ ボリュームに複数のノードで同時にアクセスするためには、Azure Files を使用してください。
- *Azure Files* は、Azure Storage アカウントによって支えられる SMB 3.0 共有をポッドにマウントするために使用できます。 Files では、複数のノードとポッドにまたがってデータを共有できます。 現在、Files で使用できるのは、標準 HDD に支えられている Azure Standard ストレージのみです。

Kubernetes におけるボリュームは、情報を格納して取得するだけの従来のディスクとは異なります。 Kubernetes のボリュームは、コンテナーで使用するために、ポッドにデータを挿入する手段としても使用できます。 Kubernetes における、その他の一般的なボリュームの種類を次に示します。

- *emptyDir* - 通常、このボリュームはポッド用の一時的な領域として使用されます。 ポッド内のすべてのコンテナーが、このボリューム上のデータにアクセスできます。 この種類のボリュームに書き込まれたデータはポッドの有効期間しか保持されません。ポッドが削除されると、ボリュームは削除されます。 一般的にこのボリュームは基礎となるローカル ノードのディスク ストレージを使用しますが、ノードのメモリ内のみに存在することもできます。
- *secret* - このボリュームは、パスワードなどの機密データをポッドに挿入するために使用されます。 まず Kubernetes API を使用してシークレットを作成します。 ポッドまたはデプロイを定義するとき、特定のシークレットを要求できます。 シークレットは、シークレットを必要とするポッドがスケジュールされているノードのみに提供されます。シークレットは *tmpfs* に格納され、ディスクには書き込まれません。 シークレットを必要とする最後のポッドがノードから削除されると、シークレットはノードの tmpfs から削除されます。 シークレットは、指定した名前空間内に格納され、同じ名前空間内のポッドによってのみアクセスできます。
- *configMap* - この種類のボリュームは、アプリケーション構成情報など、キーと値のペアのプロパティをポッドに挿入するために使用されます。 コンテナー イメージ内にアプリケーション構成情報を定義する代わりに、Kubernetes リソースとして定義すると、簡単に更新でき、デプロイ時にポッドの新しいインスタンスに適用できます。 シークレットの使用と同じく、最初に Kubernetes API を使用して ConfigMap を作成します。 その後、ポッドまたはデプロイを定義するときに、この ConfigMap を要求できます。 ConfigMap は、指定した名前空間内に格納され、同じ名前空間内のポッドによってのみアクセスできます。

## <a name="persistent-volumes"></a>永続ボリューム

ポッド ライフサイクルの一部として定義および作成されるボリュームは、そのポッドが削除されるまでしか存在しません。 メンテナンス イベントでポッドが別のホストに再スケジュールされた場合でも、多くの場合、ポッドはストレージがそのまま存在することを予期しています (特に StatefulSets)。 "*永続ボリューム*" (PV) は、Kubernetes API によって作成および管理されるストレージ リソースであり、個々のポッドの有効期間が終了しても存在できます。

PersistentVolume を提供するために Azure ディスクまたは Azure Files が使用されます。 前の「ボリューム」のセクションで説明したように、多くの場合、ディスクまたは Files の選択はデータへの同時アクセスの必要性またはパフォーマンス レベルによって決まります。

![Azure Kubernetes Services (AKS) クラスターでの永続ボリューム](media/concepts-storage/persistent-volumes.png)

PersistentVolume は、クラスター管理者が "*静的に*" 作成することも、Kubernetes API サーバーが "*動的に*" 作成することもできます。 ポッドがスケジュールされ、そのポッドが現在使用可能でないストレージを要求する場合、Kubernetes は基礎となる Azure ディスクまたは Files ストレージを作成して、ポッドに接続することができます。 動的なプロビジョニングでは *StorageClass* を使用して、どの種類の Azure Storage を作成する必要があるかを特定します。

## <a name="storage-classes"></a>ストレージ クラス

Premium や Standard など異なる階層を定義するために、*StorageClass* を作成できます。 StorageClass によって *reclaimPolicy* も定義されます。 この reclaimPolicy が、ポッドが削除されて永続ボリュームが不要になる可能性がある場合の、基礎となる Azure Storage リソースの動作を制御します。 基礎となるストレージ リソースは削除することも、将来のポッドで使用するために保持しておくこともできます。

AKS では、次の 2 つの初期 StorageClasses が作成されます。

- *default* - Azure Standard ストレージを使用してマネージド ディスクを作成します。 解放ポリシーは、基礎となる Azure ディスクを使用したポッドが削除されるときに、ディスクが削除されるように指定します。
- *managed-premium* - Azure Premium ストレージを使用してマネージド ディスクを作成します。 ここでも、解放ポリシーは、基礎となる Azure ディスクを使用したポッドが削除されるときに、ディスクが削除されるように指定します。

永続ボリュームで StorageClass が指定されない場合は、既定の StorageClass が使用されます。 永続ボリュームを要求するときは、必要なストレージが使用されることに注意してください。 `kubectl` を使用して、その他のニーズのために StorageClass を作成できます。 次の例は、Premium マネージド ディスクを使用し、ポッドの削除時に基礎となる Azure ディスクを "*保持する*" ように指定します。

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: managed-premium-retain
provisioner: kubernetes.io/azure-disk
reclaimPolicy: Retain
parameters:
  storageaccounttype: Premium_LRS
  kind: Managed
```

## <a name="persistent-volume-claims"></a>永続ボリューム要求

PersistentVolumeClaim は、特定の StorageClass のディスクまたはファイル ストレージ、アクセス モード、サイズを要求します。 Kubernetes API サーバーは、定義された StorageClass に基づいて要求を満たす既存のリソースがない場合、基礎となるストレージ リソースを動的にプロビジョニングできます。 ボリュームがポッドに接続されると、ポッドの定義にボリューム マウントが含まれます。

![Azure Kubernetes Services (AKS) クラスターでの永続ボリューム要求](media/concepts-storage/persistent-volume-claims.png)

要求したポッドに、使用可能なストレージ リソースがポッドに割り当てられると、PersistentVolume が PersistentVolumeClaim に "*バインド*" されます。 永続ボリュームと要求は 1 対 1 のマッピングです。

次の例の YAML マニフェストでは、*managed-premium* StorageClass を使用し、サイズが *5Gi* のディスクを要求する永続ボリューム要求が示されます。

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azure-managed-disk
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: managed-premium
  resources:
    requests:
      storage: 5Gi
```

ポッド定義を作成するとき、必要なストレージを要求するために永続ボリューム要求が指定されます。 また、アプリケーションがデータを読み書きするための *volumeMount* も指定します。 次の例の YAML マニフェストでは、前の永続ボリューム要求を使用してボリュームを */mnt/azure* にマウントする方法が示されます。

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx
spec:
  containers:
    - name: myfrontend
      image: nginx
      volumeMounts:
      - mountPath: "/mnt/azure"
        name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: azure-managed-disk
```

## <a name="next-steps"></a>次の手順

関連するベスト プラクティスについては、[AKS のストレージとバックアップに関するベスト プラクティス][operator-best-practices-storage]に関する記事を参照してください。

Azure ディスクまたは Azure Files を使用する動的または静的ボリュームの作成方法については、次の操作方法の記事をご覧ください。

- [Azure ディスクを使用した静的ボリュームの作成][aks-static-disks]
- [Azure Files を使用した静的ボリュームの作成][aks-static-files]
- [Azure ディスクを使用した動的ボリュームの作成][aks-dynamic-disks]
- [Azure Files を使用した動的ボリュームの作成][aks-dynamic-files]

Kubernetes と AKS の中心概念の追加情報については、次の記事を参照してください。

- [Kubernetes/AKS クラスターとワークロード][aks-concepts-clusters-workloads]
- [Kubernetes/AKS の ID][aks-concepts-identity]
- [Kubernetes/AKS のセキュリティ][aks-concepts-security]
- [Kubernetes/AKS の仮想ネットワーク][aks-concepts-network]
- [Kubernetes/AKS のスケール][aks-concepts-scale]

<!-- EXTERNAL LINKS -->

<!-- INTERNAL LINKS -->
[aks-static-disks]: azure-disk-volume.md
[aks-static-files]: azure-files-volume.md
[aks-dynamic-disks]: azure-disks-dynamic-pv.md
[aks-dynamic-files]: azure-files-dynamic-pv.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-network]: concepts-network.md
[operator-best-practices-storage]: operator-best-practices-storage.md
