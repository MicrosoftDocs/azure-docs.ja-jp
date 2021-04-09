---
title: Azure NetApp Files と Azure Kubernetes Service を統合する
description: Azure NetApp Files と Azure Kubernetes Service を統合する方法を確認します
services: container-service
ms.topic: article
ms.date: 10/23/2020
ms.openlocfilehash: 1d5aa8232b5d0aaa68e6d7e3dcbb9a7d70d0e8f8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102182147"
---
# <a name="integrate-azure-netapp-files-with-azure-kubernetes-service"></a>Azure NetApp Files と Azure Kubernetes Service を統合する

[Azure NetApp Files][anf] は、エンタープライズクラスでハイパフォーマンスの Azure 上で実行される従量制課金ファイル ストレージ サービスです。 この記事では、Azure NetApp Files と Azure Kubernetes Service (AKS) を統合する方法を説明します。

## <a name="before-you-begin"></a>開始する前に
この記事は、AKS クラスターがすでに存在していることを前提としています。 AKS クラスターが必要な場合は、[Azure CLI を使用した場合][aks-quickstart-cli]または [Azure portal を使用した場合][aks-quickstart-portal]の AKS のクイックスタートを参照してください。

> [!IMPORTANT]
> また、AKS クラスターは、[Azure NetApp Files をサポートするリージョン][anf-regions]にある必要があります。

また、Azure CLI バージョン 2.0.59 以降がインストールされ、構成されている必要もあります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][install-azure-cli]に関するページを参照してください。

### <a name="limitations"></a>制限事項

Azure NetApp Files を使用した場合、次の制限が適用されます。

* Azure NetApp Files は、[選択された Azure リージョン][anf-regions]でのみ利用できます。
* Azure NetApp Files を使用するには、Azure NetApp Files サービスへのアクセス権が必要です。 アクセスを申請するには、[Azure NetApp Files 順番待ちリスト送信フォーム][anf-waitlist]を使用するか、 https://azure.microsoft.com/services/netapp/#getting-started にアクセスできます。 Azure NetApp Files サービスには、Azure NetApp Files チームから正式な確認メールが届くまでアクセスすることができません。
* AKS クラスターの初期展開後は、Azure NetApp Files の静的プロビジョニングのみがサポートされます。
* Azure NetApp Files で動的プロビジョニングを使用するには [NetApp Trident](https://netapp-trident.readthedocs.io/) バージョン 19.07 以降をインストールして構成します。

## <a name="configure-azure-netapp-files"></a>Azure NetApp Files の構成

> [!IMPORTANT]
> *Microsoft.NetApp* リソース プロバイダーを登録する前に、該当するサブスクリプションの [Azure NetApp Files 順番待ちリスト送信フォーム][anf-waitlist]を記入するか、 https://azure.microsoft.com/services/netapp/#getting-started にアクセスする必要があります。 Azure NetApp Files チームから正式な確認メールが届くまで、リソース プロバイダーを登録することはできません。

*Microsoft.NetApp* リソース プロバイダーを登録します。

```azurecli
az provider register --namespace Microsoft.NetApp --wait
```

> [!NOTE]
> この処理には、完了までに時間がかかる場合があります。

AKS で使用するための Azure NetApp アカウントを作成する場合は、**ノード** リソース グループ内にアカウントを作成する必要があります。 最初に、[az aks show][az-aks-show] コマンドを使用してリソース グループ名を取得し、`--query nodeResourceGroup` クエリ パラメーターを追加します｡ 次の例では、リソース グループ名 *myResourceGroup* にある *myAKSCluster* という名前の AKS クラスターのノード リソース グループを取得しています。

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
```

```output
MC_myResourceGroup_myAKSCluster_eastus
```

[az netappfiles account create][az-netappfiles-account-create] を使用して、その **ノード** リソース グループ (AKS クラスターと同じリージョン) に Azure NetApp Files アカウントを作成します。 次の例では、*MC_myResourceGroup_myAKSCluster_eastus* リソース グループと *eastus* リージョンに *myaccount1* という名前のアカウントを作成します。

```azurecli
az netappfiles account create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1
```

[az netappfiles pool create][az-netappfiles-pool-create] を使用して新しい容量プールを作成します。 次の例では、*Premium* サービス レベルの 4 TB の *mypool1* という名前の新しい容量プールを作成します。

```azurecli
az netappfiles pool create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1 \
    --pool-name mypool1 \
    --size 4 \
    --service-level Premium
```

[az network vnet subnet create][az-network-vnet-subnet-create] を使用して、[Azure NetApp Files に委任][anf-delegate-subnet]するサブネットを作成します。 *このサブネットは、AKS クラスターと同じ仮想ネットワーク内に存在する必要があります。*

```azurecli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
VNET_NAME=$(az network vnet list --resource-group $RESOURCE_GROUP --query [].name -o tsv)
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
SUBNET_NAME=MyNetAppSubnet
az network vnet subnet create \
    --resource-group $RESOURCE_GROUP \
    --vnet-name $VNET_NAME \
    --name $SUBNET_NAME \
    --delegations "Microsoft.NetApp/volumes" \
    --address-prefixes 10.0.0.0/28
```

[az netappfiles volume create][az-netappfiles-volume-create] を使用して、ボリュームを作成します。

```azurecli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
LOCATION=eastus
ANF_ACCOUNT_NAME=myaccount1
POOL_NAME=mypool1
SERVICE_LEVEL=Premium
VNET_NAME=$(az network vnet list --resource-group $RESOURCE_GROUP --query [].name -o tsv)
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
SUBNET_NAME=MyNetAppSubnet
SUBNET_ID=$(az network vnet subnet show --resource-group $RESOURCE_GROUP --vnet-name $VNET_NAME --name $SUBNET_NAME --query "id" -o tsv)
VOLUME_SIZE_GiB=100 # 100 GiB
UNIQUE_FILE_PATH="myfilepath2" # Please note that file path needs to be unique within all ANF Accounts

az netappfiles volume create \
    --resource-group $RESOURCE_GROUP \
    --location $LOCATION \
    --account-name $ANF_ACCOUNT_NAME \
    --pool-name $POOL_NAME \
    --name "myvol1" \
    --service-level $SERVICE_LEVEL \
    --vnet $VNET_ID \
    --subnet $SUBNET_ID \
    --usage-threshold $VOLUME_SIZE_GiB \
    --file-path $UNIQUE_FILE_PATH \
    --protocol-types "NFSv3"
```

## <a name="create-the-persistentvolume"></a>PersistentVolume の作成

[az netappfiles volume show][az-netappfiles-volume-show] を使用して、ボリュームの詳細を一覧表示します。

```azurecli
az netappfiles volume show --resource-group $RESOURCE_GROUP --account-name $ANF_ACCOUNT_NAME --pool-name $POOL_NAME --volume-name "myvol1"
```

```output
{
  ...
  "creationToken": "myfilepath2",
  ...
  "mountTargets": [
    {
      ...
      "ipAddress": "10.0.0.4",
      ...
    }
  ],
  ...
}
```

PersistentVolume を定義する `pv-nfs.yaml` を作成します。 `path` を前のコマンドからの *creationToken* に、`server` を *ipAddress* に置き換えます。 次に例を示します。

```yaml
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-nfs
spec:
  capacity:
    storage: 100Gi
  accessModes:
    - ReadWriteMany
  mountOptions:
    - vers=3
  nfs:
    server: 10.0.0.4
    path: /myfilepath2
```

*server* と *path* を前の手順で作成した NFS (ネットワーク ファイル システム) ボリュームの値に更新します。 [kubectl apply][kubectl-apply] コマンドを使用して PersistentVolume を作成します。

```console
kubectl apply -f pv-nfs.yaml
```

[kubectl describe][kubectl-describe] コマンドを使用して、PersistentVolume の "*状態*" が "*使用可能*" であることを確認します。

```console
kubectl describe pv pv-nfs
```

## <a name="create-the-persistentvolumeclaim"></a>PersistentVolumeClaim の作成

PersistentVolume を定義する `pvc-nfs.yaml` を作成します。 次に例を示します。

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-nfs
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: ""
  resources:
    requests:
      storage: 1Gi
```

[kubectl apply][kubectl-apply] コマンドを使用して PersistentVolumeClaim を作成します。

```console
kubectl apply -f pvc-nfs.yaml
```

[kubectl describe][kubectl-describe] コマンドを使用して、PersistentVolumeClaim の "*状態*" が "*使用可能*" であることを確認します。

```console
kubectl describe pvc pvc-nfs
```

## <a name="mount-with-a-pod"></a>ポッドを使ったマウント

PersistentVolumeClaim を使用するポッドを定義する `nginx-nfs.yaml` を作成します。 次に例を示します。

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx-nfs
spec:
  containers:
  - image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    name: nginx-nfs
    command:
    - "/bin/sh"
    - "-c"
    - while true; do echo $(date) >> /mnt/azure/outfile; sleep 1; done
    volumeMounts:
    - name: disk01
      mountPath: /mnt/azure
  volumes:
  - name: disk01
    persistentVolumeClaim:
      claimName: pvc-nfs
```

[kubectl apply][kubectl-apply] コマンドを使用してポッドを作成します。

```console
kubectl apply -f nginx-nfs.yaml
```

[kubectl describe][kubectl-describe] コマンドを使用して、ポッドが "*実行中*" であることを確認します。

```console
kubectl describe pod nginx-nfs
```

ボリュームがポッドにマウントされていることを [kubectl exec][kubectl-exec] を使用して確認してポッドに接続してから、`df -h` を使用してボリュームがマウントされてるかどうかを確認します。

```console
$ kubectl exec -it nginx-nfs -- sh
```

```output
/ # df -h
Filesystem             Size  Used Avail Use% Mounted on
...
10.0.0.4:/myfilepath2  100T  384K  100T   1% /mnt/azure
...
```

## <a name="next-steps"></a>次のステップ

Azure NetApp Files の詳細については、「[Azure NetApp Files とは][anf]」を参照してください。 NFS と AKS の使用について詳しくは、「[NFS (Network File System) Linux Server ボリュームを手動で作成し、Azure Kubernetes Service (AKS) と共に使用する][aks-nfs]」を参照してください。


[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-nfs]: azure-nfs-volume.md
[anf]: ../azure-netapp-files/azure-netapp-files-introduction.md
[anf-delegate-subnet]: ../azure-netapp-files/azure-netapp-files-delegate-subnet.md
[anf-quickstart]: ../azure-netapp-files/
[anf-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all
[anf-waitlist]: https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8cq17Xv9yVBtRCSlcD_gdVUNUpUWEpLNERIM1NOVzA5MzczQ0dQR1ZTSS4u
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-netappfiles-account-create]: /cli/azure/netappfiles/account#az-netappfiles-account-create
[az-netappfiles-pool-create]: /cli/azure/netappfiles/pool#az-netappfiles-pool-create
[az-netappfiles-volume-create]: /cli/azure/netappfiles/volume#az-netappfiles-volume-create
[az-netappfiles-volume-show]: /cli/azure/netappfiles/volume#az-netappfiles-volume-show
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[install-azure-cli]: /cli/azure/install-azure-cli
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-exec]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#exec
