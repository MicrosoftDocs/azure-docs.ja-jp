---
title: Azure Stack Edge Pro デバイスでの Kubernetes ストレージ管理について | Microsoft Docs
description: Azure Stack Edge Pro デバイスで Kubernetes ストレージ管理がどのように行われるかについて説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: d848d663121474085935c68e62b8aa38f195ba8d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102442181"
---
# <a name="kubernetes-storage-management-on-your-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU デバイスでの Kubernetes ストレージ管理

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure Stack Edge Pro デバイスでは、コンピューティング ロールを構成するときに Kubernetes クラスターが作成されます。 Kubernetes クラスターが作成されると、コンテナー化されたアプリケーションを Kubernetes クラスターのポッドにデプロイできます。 Kubernetes クラスター内のポッドにストレージを提供するには、別の方法があります。 

この記事では、Kubernetes クラスターでストレージをプロビジョニングする方法を全体的に、具体的にはお使いの Azure Stack Edge Pro デバイスに即して説明します。 

## <a name="storage-requirements-for-kubernetes-pods"></a>Kubernetes ポッドのストレージ要件

Kubernetes ポッドはステートレスですが、実行するアプリケーションは通常ステートフルです。 ポッドは有効期間が短く、再起動、フェールオーバー、または Kubernetes ノード間を移動する可能性があるため、ポッドに関連付けられているストレージについて次の要件を満たす必要があります。 

ストレージの要件:

- ポッドの外側にあること。
- ポッドのライフサイクルとは独立していること。
- すべての Kubernetes ノードからアクセスできること。

Kubernetes のストレージの管理方法を理解するには、次の 2 つの API リソースを理解する必要があります。 

- **PersistentVolume (PV)** : これは、Kubernetes クラスター内のストレージの一部です。 Kubernetes ストレージは `PersistentVolume` として静的にプロビジョニングできます。 また、`StorageClass` として動的にプロビジョニングすることもできます。

- **PersistentVolumeClaim (PVC)** : これは、ユーザーによるストレージの要求です。 PVC により PV リソースが消費されます。 PVC では、特定のサイズとアクセス モードを要求できます。 

    ユーザーはさまざまな問題に応じてさまざまなプロパティを持つ `PersistentVolumes` を必要とするため、クラスター管理者は、サイズやアクセス モードだけでなく、さまざまな点で異なる多様な `PersistentVolumes` を提供できる必要があります。 こうしたニーズには、`StorageClass` リソースが必要になります。

ストレージのプロビジョニングは、静的または動的に行うことができます。 各プロビジョニングの種類については、以降のセクションで説明します。

## <a name="static-provisioning"></a>静的プロビジョニング

Kubernetes クラスター管理者は、ストレージを静的にプロビジョニングできます。 これを行うには、SMB/NFS ファイルシステムに基づくストレージ バックエンドを使用することも、オンプレミス環境でネットワークを介してローカルに接続する iSCSI ディスクを使用することも、またはクラウドで Azure Files または Azure ディスクを使用することもできます。 この種類のストレージは既定ではプロビジョニングされないため、クラスター管理者がこのプロビジョニングを計画して管理する必要があります。 
 
次の図は、静的にプロビジョニングされたストレージが Kubernetes でどのように使用されるかを示しています。 

![PersistentVolumes を使用した静的プロビジョニング](./media/azure-stack-edge-gpu-kubernetes-storage/static-provisioning-persistent-volumes-1.png)

次の手順で行われます。 

1. **ストレージをプロビジョニングする**: クラスター管理者がストレージをプロビジョニングします。 この例では、クラスター管理者が 1 つまたは複数の SMB 共有を作成します。これらの共有により、対応する Kubernetes クラスターに永続的ボリューム オブジェクトが自動的に作成されます。 

1. **ストレージを要求する**: ストレージを要求する PVC の展開を送信します。 このストレージの要求は、PersistentVolumeClaim (PVC) です。 PV のサイズとアクセス モードが PVC のものと一致する場合、PVC は PV にバインドされます。 PVC と PV は一対一でマップされます。

1. **PVC をコンテナーにマウントする**: PVC が PV にバインドされると、コンテナー内のパスにこの PVC をマウントできます。 コンテナー内のアプリケーション ロジックによってこのパスから読み取りまたはこのパスに書き込みが行われると、データが SMB ストレージに書き込まれます。
 

## <a name="dynamic-provisioning"></a>動的プロビジョニング

次の図は、静的にプロビジョニングされたストレージが Kubernetes でどのように使用されるかを示しています。 

![StorageClasses 使用した動的プロビジョニング](./media/azure-stack-edge-gpu-kubernetes-storage/dynamic-provisioning-storage-classes-1.png)

次の手順で行われます。 

1. **ストレージ クラスを定義する**: クラスター管理者は、Kubernetes クラスターの動作環境に応じてストレージ クラスを定義します。 クラスター管理者は、プロビジョナーも展開します。これは、Kubernetes クラスターに展開された別のポッドまたはアプリケーションです。 プロビジョナーには、共有を動的にプロビジョニングするための詳細がすべて含まれています。  

1. **ストレージを要求する**: ストレージを要求するアプリケーションを送信します。 このストレージ クラス参照を使用して PVC が作成されると、プロビジョナーが呼び出されます。 

1. **ストレージを動的にプロビジョニングする**: プロビジョナーによって、ローカル ディスク ストレージに関連付けられている共有が動的に作成されます。 共有が作成されると、この共有に対応する PV オブジェクトも自動的に作成されます。

1. **PVC をコンテナーにマウントする**: PVC が PV にバインドされたら、静的プロビジョニングと同じ方法でコンテナーのパスに PVC をマウントし、共有に対して読み取りまたは書き込みを行うことができます。


## <a name="storage-provisioning-on-azure-stack-edge-pro"></a>Azure Stack Edge Pro でのストレージのプロビジョニング

Azure Stack Edge Pro デバイスでは、デバイスのストレージ機能を使用して、静的にプロビジョニングされた `PersistentVolumes` が作成されます。 共有をプロビジョニングし、 **[Edge コンピューティングで共有を使用する]** オプションを有効にすると、このアクションによって Kubernetes クラスター内に PV リソースが自動的に作成されます。

![静的プロビジョニングのための Azure portal でのローカル共有の作成](./media/azure-stack-edge-gpu-kubernetes-storage/static-provisioning-azure-portal-1.png)

クラウドの階層化を使用するには、[Edge コンピューティングで共有を使用する] オプションを有効にした状態で、Edge クラウド共有を作成します。 この共有に対しても、PV が自動的に作成されます。 Edge 共有に書き込むアプリケーション データはすべて、クラウドに階層化されます。 

![静的プロビジョニングのための Azure portal でのクラウド共有の作成](./media/azure-stack-edge-gpu-kubernetes-storage/static-provisioning-azure-portal-2.png)

SMB 共有と NFS 共有の両方を作成して、Azure Stack Edge Pro デバイスで PV を静的にプロビジョニングすることができます。 PV がプロビジョニングされたら、このストレージを要求する PVC を送信します。 ストレージを要求し、プロビジョニングした共有を使用する、PVC の展開 `yaml` の例を次に示します。


```yml
kind: PersistentVolumeClaim 
apiVersion: v1 
metadata: 
  name: pvc-smb-flexvol 
spec: 
  accessModes: 
  - ReadWriteMany 
  resources: 
    requests: 
      storage: 10Gi 
  volumeName: <nfs-or-smb-share-name-here> 
  storageClassName: ""
```

詳細については、[kubectl を使用した Azure Stack Edge Pro での静的プロビジョニングによるステートフル アプリケーションのデプロイ](azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes.md)に関する記事をご覧ください。

同じ静的にプロビジョニングされたストレージにアクセスするには、IoT のストレージ バインドに対応するボリューム マウント オプションは次のとおりです。 `/home/input` は、コンテナー内でボリュームにアクセスできるパスです。

```
{
"HostConfig": {
"Mounts": [
{
"Target": "/home/input",
"Source": "<nfs-or-smb-share-name-here>",
"Type": "volume"
},
{
"Target": "/home/output",
"Source": "<nfs-or-smb-share-name-here>",
"Type": "volume"
}]
}
}
```

Azure Stack Edge Pro には、Kubernetes ノードに接続されているデータ ディスク ストレージを使用する `ase-node-local` と呼ばれる組み込みの `StorageClass` もあります。 この `StorageClass` では動的プロビジョニングがサポートされています。 ポッド アプリケーションで `StorageClass` 参照を作成し、PV が自動的に作成されるようにすることができます。 詳細については、[Kubernetes ダッシュボード](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md)で `ase-node-local StorageClass` を照会してください。

![Kubernetes ダッシュボードの組み込みストレージ クラス](./media/azure-stack-edge-gpu-kubernetes-storage/dynamic-provisioning-builtin-storage-class-1.png)

詳細については、[kubectl を使用した Azure Stack Edge Pro での動的プロビジョニングによるステートフル アプリケーションのデプロイ](azure-stack-edge-gpu-deploy-stateful-application-dynamic-provision-kubernetes.md)に関する記事をご覧ください。

## <a name="choose-storage-type"></a>ストレージの種類の選択

デプロイするワークロードによっては、ストレージの種類を選択することが必要になる場合があります。 

- 展開する多くのノードによってボリュームが読み取り/書き込みとしてマウントされている `PersistentVolumes` に対して `ReadWriteMany` アクセス モードが必要な場合は、SMB/NFS 共有に対して静的プロビジョニングを使用します。

- デプロイするアプリケーションに POSIX の準拠要件 (例: MongoDB、PostgreSQL、MySQL、Prometheus などのアプリケーション) がある場合は、組み込みの StorageClass を使用します。 アクセス モードが `ReadWriteOnce` になっているか、ボリュームが 1 つのノードによって読み取り/書き込み可能としてマウントされています。 


アクセス モードの詳細については、[Kubernetes ボリュームのアクセス モード](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes)のセクションを参照してください。


## <a name="next-steps"></a>次のステップ

`PersistentVolume` を静的にプロビジョニングする方法については、次を参照してください。

- [kubectl を使用して Azure Stack Edge Pro で静的プロビジョニングによってステートフル アプリケーションをデプロイする](azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes.md)。

`StorageClass` を動的にプロビジョニングする方法については、次を参照してください。

- [kubectl を使用して Azure Stack Edge Pro で動的プロビジョニングによってステートフル アプリケーションをデプロイする](azure-stack-edge-gpu-deploy-stateful-application-dynamic-provision-kubernetes.md)。
