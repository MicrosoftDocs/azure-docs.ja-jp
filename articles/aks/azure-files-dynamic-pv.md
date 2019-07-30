---
title: Azure Kubernetes Service (AKS) で複数のポッドのファイル ボリュームを動的に作成する
description: Azure Kubernetes Service (AKS) で複数の同時実行ポッドで使用するための Azure Files を含む永続ボリュームを動的に作成する方法について説明します
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 07/08/2019
ms.author: mlearned
ms.openlocfilehash: 580363973afd918351931edfb187a1a8d38d6985
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/25/2019
ms.locfileid: "67665969"
---
# <a name="dynamically-create-and-use-a-persistent-volume-with-azure-files-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) で Azure Files を含む永続ボリュームを動的に作成して使用する

永続ボリュームとは、Kubernetes ポッドで使用するためにプロビジョニングされているストレージの一部です。 永続ボリュームは 1 つまたは複数のポッドで使用でき、動的または静的にプロビジョニングできます。 複数のポッドが同じストレージ ボリュームに同時アクセスする必要がある場合は、Azure Files を使用し、[サーバー メッセージ ブロック (SMB) プロトコル][smb-overview]を使用して接続します。 この記事では、Azure Kubernetes Service (AKS) クラスターの複数のポッドで使用するために、Azure Files 共有を動的に作成する方法を示します。

Kubernetes ボリュームの詳細については、[AKS でのアプリケーションのストレージ オプション][concepts-storage]に関するページを参照してください。

## <a name="before-you-begin"></a>開始する前に

この記事は、AKS クラスターがすでに存在していることを前提としています。 AKS クラスターが必要な場合は、[Azure CLI を使用した場合][aks-quickstart-cli]または [Azure portal を使用した場合][aks-quickstart-portal]の AKS のクイックスタートを参照してください。

また、Azure CLI バージョン 2.0.59 以降がインストールされ、構成されている必要もあります。 バージョンを確認するには、 `az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「 [Azure CLI のインストール][install-azure-cli]」を参照してください。

## <a name="create-a-storage-class"></a>ストレージ クラスの作成

ストレージ クラスを使用して、Azure ファイル共有を作成する方法を定義します。 ストレージ アカウントは、ストレージ クラスと共に使用して Azure ファイル共有を保持するために、[ノード リソース グループ][node-resource-group]内に自動的に作成されます。 *skuName* には、次の [Azure Storage の冗長性][storage-skus]から選択します。

* *Standard_LRS* - 標準のローカル冗長ストレージ (LRS)
* *Standard_GRS* - 標準の geo 冗長ストレージ (GRS)
* *Standard_RAGRS* - 標準の読み取りアクセス geo 冗長ストレージ (RA-GRS)

> [!NOTE]
> Azure Files では、Kubernetes 1.13 以降が実行される AKS クラスターでの Premium Storage がサポートされています。

Azure Files 用の Kubernetes ストレージ クラスの詳細については、[Kubernetes ストレージ クラス][kubernetes-storage-classes]に関するページを参照してください。

`azure-file-sc.yaml` という名前のファイルを作成し、次の例のマニフェストにコピーします。 *mountOptions* の詳細については、「[マウント オプション][mount-options]」セクションを参照してください。

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
parameters:
  skuName: Standard_LRS
```

[kubectl apply][kubectl-apply] コマンドを使用してストレージ クラスを作成します。

```console
kubectl apply -f azure-file-sc.yaml
```

## <a name="create-a-cluster-role-and-binding"></a>クラスターのロールとバインディングの作成

AKS クラスターでは、実行できるアクションを制限するために、Kubernetes のロールベース アクセス制御 (RBAC) が使用されます。 付与するアクセス許可を*ロール*によって定義し、それらを*バインド*によって目的のユーザーに適用します。 これらの割り当ては、特定の名前空間に適用することも、クラスター全体に適用することもできます。 詳細については、[RBAC 承認の使用][kubernetes-rbac]に関するページを参照してください。

Azure プラットフォームで必要なストレージ リソースを作成できるようにするには、*ClusterRole* と *ClusterRoleBinding* を作成します。 `azure-pvc-roles.yaml` という名前のファイルを作成し、そこに以下の YAML をコピーします。

```yaml
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: system:azure-cloud-provider
rules:
- apiGroups: ['']
  resources: ['secrets']
  verbs:     ['get','create']
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: system:azure-cloud-provider
roleRef:
  kind: ClusterRole
  apiGroup: rbac.authorization.k8s.io
  name: system:azure-cloud-provider
subjects:
- kind: ServiceAccount
  name: persistent-volume-binder
  namespace: kube-system
```

[kubectl apply][kubectl-apply] コマンドを使用してアクセス許可を割り当てます。

```console
kubectl apply -f azure-pvc-roles.yaml
```

## <a name="create-a-persistent-volume-claim"></a>永続ボリューム要求の作成

永続ボリューム要求 (PVC) は、ストレージ クラス オブジェクトを使用して、Azure ファイル共有を動的にプロビジョニングします。 次の YAML を使うと、サイズが *5GB* で *ReadWriteMany* アクセスの永続ボリューム要求を作成できます。 アクセス モードの詳細については、[Kubernetes 永続ボリューム][access-modes]に関するドキュメントを参照してください。

`azure-file-pvc.yaml` という名前のファイルを作成し、そこに以下の YAML をコピーします。 *storageClassName* が最後の手順で作成したストレージ クラスと一致していることを確認します。

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azurefile
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: azurefile
  resources:
    requests:
      storage: 5Gi
```

[kubectl apply][kubectl-apply] コマンドを使用して永続ボリューム要求を作成します。

```console
kubectl apply -f azure-file-pvc.yaml
```

完了すると、ファイル共有が作成されます。 接続情報と資格情報を含む Kubernetes シークレットも作成されます。 [kubectl get][kubectl-get] コマンドを使用すると、PVC の状態を表示できます。

```console
$ kubectl get pvc azurefile

NAME        STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
azurefile   Bound     pvc-8436e62e-a0d9-11e5-8521-5a8664dc0477   5Gi        RWX            azurefile      5m
```

## <a name="use-the-persistent-volume"></a>永続ボリュームの使用

次の YAML は、永続ボリューム要求 *azurefile* を使って、 */mnt/azure* パスに Azure ファイル共有をマウントするポッドを作成します。 Windows Server コンテナー (AKS では現在プレビュー段階) の場合、 *'D:'* などの Windows パス規則を使用して、*mountPath* を指定します。

`azure-pvc-files.yaml` という名前のファイルを作成し、そこに以下の YAML をコピーします。 *claimName* が最後の手順で作成したPVC と一致していることを確認します。

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
  - name: mypod
    image: nginx:1.15.5
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
    - mountPath: "/mnt/azure"
      name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: azurefile
```

[kubectl apply][kubectl-apply] コマンドを使用してポッドを作成します。

```console
kubectl apply -f azure-pvc-files.yaml
```

これで Azure Files 共有が */mnt/azure* ディレクトリにマウントされ、ポッドが稼働状態となりました。 この構成は、`kubectl describe pod mypod` 経由でポッドを調べるときに表示できます。 次に示したのは、その出力例の抜粋です。コンテナーにマウントされたボリュームが表示されています。

```
Containers:
  mypod:
    Container ID:   docker://053bc9c0df72232d755aa040bfba8b533fa696b123876108dec400e364d2523e
    Image:          nginx:1.15.5
    Image ID:       docker-pullable://nginx@sha256:d85914d547a6c92faa39ce7058bd7529baacab7e0cd4255442b04577c4d1f424
    State:          Running
      Started:      Fri, 01 Mar 2019 23:56:16 +0000
    Ready:          True
    Mounts:
      /mnt/azure from volume (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-8rv4z (ro)
[...]
Volumes:
  volume:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  azurefile
    ReadOnly:   false
[...]
```

## <a name="mount-options"></a>マウント オプション

*fileMode* と *dirMode* の既定値は、次の表に示すように Kubernetes のバージョンによって異なります。

| version | value |
| ---- | ---- |
| v1.6.x、v1.7.x | 0777 |
| v1.8.0 - v1.8.5 | 0700 |
| v1.8.6 以上 | 0755 |
| v1.9.0 | 0700 |
| v1.9.1 以上 | 0755 |

バージョン 1.8.5 以降のクラスターを使い、ストレージ クラスに永続ボリュームを動的に作成している場合は、ストレージ クラスのオブジェクトに対してマウント オプションを指定できます。 次の例では、*0777* が設定されます。

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
parameters:
  skuName: Standard_LRS
```

バージョン 1.8.0 - 1.8.4 のクラスターを使用している場合は、*runAsUser* の値を *0* に設定してセキュリティ コンテキストを指定できます。 ポッドのセキュリティ コンテキストについて詳しくは、[セキュリティ コンテキストの構成][kubernetes-security-context]に関するページを参照してください。

## <a name="next-steps"></a>次の手順

関連するベスト プラクティスについては、[AKS のストレージとバックアップに関するベスト プラクティス][operator-best-practices-storage]に関する記事を参照してください。

Azure Files を使用した Kubernetes 永続ボリュームについて、さらに詳しい情報を確認します。

> [!div class="nextstepaction"]
> [Azure Files 用 Kubernetes プラグイン][kubernetes-files]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-security-context]: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/#azure-file
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[pv-static]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#static
[smb-overview]: /windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-group-list]: /cli/azure/group#az-group-list
[az-resource-show]: /cli/azure/aks#az-aks-show
[az-storage-account-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-key-list]: /cli/azure/storage/account/keys#az-storage-account-keys-list
[az-storage-share-create]: /cli/azure/storage/share#az-storage-share-create
[mount-options]: #mount-options
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[storage-skus]: ../storage/common/storage-redundancy.md
[kubernetes-rbac]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
[node-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks