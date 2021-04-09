---
title: Azure Files 共有を手動で作成する
titleSuffix: Azure Kubernetes Service
description: Azure Kubernetes Service (AKS) 上で複数の同時実行ポッドで使用するための Azure Files を含むボリュームを手動で作成する方法について説明します
services: container-service
ms.topic: article
ms.date: 03/01/2019
ms.openlocfilehash: 4e009c5de2e24c1b0bd94fb4c11b0c52a3bc378d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102609075"
---
# <a name="manually-create-and-use-a-volume-with-azure-files-share-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) 上で Azure ファイル共有を含むボリュームを手動で作成して使用する

コンテナーベースのアプリケーションは、データへのアクセスとデータの永続化の手段として、外部データ ボリュームが必要になることが少なくありません。 複数のポッドが同じストレージ ボリュームに同時アクセスする必要がある場合は、Azure Files を使用し、[サーバー メッセージ ブロック (SMB) プロトコル][smb-overview]を使用して接続します。 この記事では、Azure ファイル共有を手動で作成し、AKS のポッドにアタッチする方法を示します。

Kubernetes ボリュームの詳細については、[AKS でのアプリケーションのストレージ オプション][concepts-storage]に関するページを参照してください。

## <a name="before-you-begin"></a>開始する前に

この記事は、AKS クラスターがすでに存在していることを前提としています。 AKS クラスターが必要な場合は、[Azure CLI を使用した場合][aks-quickstart-cli]または [Azure portal を使用した場合][aks-quickstart-portal]の AKS のクイックスタートを参照してください。

また、Azure CLI バージョン 2.0.59 以降がインストールされ、構成されている必要もあります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][install-azure-cli]に関するページを参照してください。

## <a name="create-an-azure-file-share"></a>Azure ファイル共有を作成する

Azure Files を Kubernetes ボリュームとして使用するには、あらかじめ Azure Storage アカウントとファイル共有を作成しておく必要があります。 以下のコマンドでは、*myAKSShare* という名前のリソース グループ、ストレージ アカウント、*aksshare* という名前のファイル共有を作成します。

```azurecli-interactive
# Change these four parameters as needed for your own environment
AKS_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
AKS_PERS_RESOURCE_GROUP=myAKSShare
AKS_PERS_LOCATION=eastus
AKS_PERS_SHARE_NAME=aksshare

# Create a resource group
az group create --name $AKS_PERS_RESOURCE_GROUP --location $AKS_PERS_LOCATION

# Create a storage account
az storage account create -n $AKS_PERS_STORAGE_ACCOUNT_NAME -g $AKS_PERS_RESOURCE_GROUP -l $AKS_PERS_LOCATION --sku Standard_LRS

# Export the connection string as an environment variable, this is used when creating the Azure file share
export AZURE_STORAGE_CONNECTION_STRING=$(az storage account show-connection-string -n $AKS_PERS_STORAGE_ACCOUNT_NAME -g $AKS_PERS_RESOURCE_GROUP -o tsv)

# Create the file share
az storage share create -n $AKS_PERS_SHARE_NAME --connection-string $AZURE_STORAGE_CONNECTION_STRING

# Get storage account key
STORAGE_KEY=$(az storage account keys list --resource-group $AKS_PERS_RESOURCE_GROUP --account-name $AKS_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" -o tsv)

# Echo storage account name and key
echo Storage account name: $AKS_PERS_STORAGE_ACCOUNT_NAME
echo Storage account key: $STORAGE_KEY
```

ストレージ アカウント名と、スクリプトの出力の末尾に表示されるキーをメモしておきます。 これらの値は、次の手順のいずれかで Kubernetes ボリュームを作成するときに必要になります。

## <a name="create-a-kubernetes-secret"></a>Kubernetes シークレットを作成する

Kubernetes には、前の手順で作成されたファイル共有にアクセスするための資格情報が必要です。 これらの資格情報は [Kubernetes シークレット][kubernetes-secret]に格納され、Kubernetes ポッドを作成するときにそのシークレットが参照されます。

`kubectl create secret` コマンドを使用して、シークレットを作成します。 次の例では、*azure-secret* という名前の共有を作成し、前の手順の *azurestorageaccountname* と *azurestorageaccountkey* を設定します。 既存の Azure ストレージ アカウントを使用するには、アカウント名とキーを指定します。

```console
kubectl create secret generic azure-secret --from-literal=azurestorageaccountname=$AKS_PERS_STORAGE_ACCOUNT_NAME --from-literal=azurestorageaccountkey=$STORAGE_KEY
```

## <a name="mount-file-share-as-an-inline-volume"></a>ファイル共有をインライン ボリュームとしてマウントする
> 注: 1.18.15、1.19.7、1.20.2、1.21.0 以降、インライン `azureFile` ボリューム内のシークレット名前空間は、`default` 名前空間としてのみ設定できます。異なるシークレット名前空間を指定するには、代わりに次の永続ボリュームの例を使用してください。

Azure ファイル共有をポッドにマウントするには、コンテナーの指定でボリュームを構成します。次の内容で、`azure-files-pod.yaml` という名前の新しいファイルを作成します。 ファイル共有の名前またはシークレット名を変更した場合は、*shareName* と *secretName* を更新します。 必要な場合は、`mountPath` を更新します。これはファイル共有がポッドにマウントされているパスです。 Windows Server コンテナーの場合、 *'D:'* などの Windows パス規則を使用して *mountPath* を指定します。

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
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
    azureFile:
      secretName: azure-secret
      shareName: aksshare
      readOnly: false
```

`kubectl` コマンドを使用して、ポッドを作成します。

```console
kubectl apply -f azure-files-pod.yaml
```

これで Azure ファイル共有が */mnt/azure* にマウントされ、ポッドが稼働状態となりました。 `kubectl describe pod mypod` を使用すると、共有が正常にマウントされていることを確認できます。 次に示したのは、その出力例の抜粋です。コンテナーにマウントされたボリュームが表示されています。

```
Containers:
  mypod:
    Container ID:   docker://86d244cfc7c4822401e88f55fd75217d213aa9c3c6a3df169e76e8e25ed28166
    Image:          mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    Image ID:       docker-pullable://nginx@sha256:9ad0746d8f2ea6df3a17ba89eca40b48c47066dfab55a75e08e2b70fc80d929e
    State:          Running
      Started:      Sat, 02 Mar 2019 00:05:47 +0000
    Ready:          True
    Mounts:
      /mnt/azure from azure (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-z5sd7 (ro)
[...]
Volumes:
  azure:
    Type:        AzureFile (an Azure File Service mount on the host and bind mount to the pod)
    SecretName:  azure-secret
    ShareName:   aksshare
    ReadOnly:    false
  default-token-z5sd7:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-z5sd7
[...]
```

## <a name="mount-file-share-as-an-persistent-volume"></a>ファイル共有を永続ボリュームとしてマウントする
 - マウント オプション

Kubernetes バージョン 1.15 以降の場合、*fileMode* と *dirMode* の既定値は *0777* です。 次の例では、*PersistentVolume* オブジェクトに対して *0755* を設定します。

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: azurefile
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteMany
  azureFile:
    secretName: azure-secret
    secretNamespace: default
    shareName: aksshare
    readOnly: false
  mountOptions:
  - dir_mode=0755
  - file_mode=0755
  - uid=1000
  - gid=1000
  - mfsymlinks
  - nobrl
```

マウント オプションを更新するには、*PersistentVolume* を使用して *azurefile-mount-options-pv.yaml* ファイルを作成します。 次に例を示します。

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: azurefile
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteMany
  azureFile:
    secretName: azure-secret
    shareName: aksshare
    readOnly: false
  mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
  - mfsymlinks
  - nobrl
```

*PersistentVolume* を使用する *PersistentVolumeClaim* を使って *azurefile-mount-options-pvc.yaml* ファイルを作成します。 次に例を示します。

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azurefile
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: ""
  resources:
    requests:
      storage: 5Gi
```

`kubectl` コマンドを使用して、*PersistentVolume* と *PersistentVolumeClaim* を作成します。

```console
kubectl apply -f azurefile-mount-options-pv.yaml
kubectl apply -f azurefile-mount-options-pvc.yaml
```

*PersistentVolumeClaim* が作成され、*PersistentVolume* にバインドされていることを確認します。

```console
$ kubectl get pvc azurefile

NAME        STATUS   VOLUME      CAPACITY   ACCESS MODES   STORAGECLASS   AGE
azurefile   Bound    azurefile   5Gi        RWX            azurefile      5s
```

*PersistentVolumeClaim* を参照してポッドを更新するようにコンテナーの仕様を更新します。 次に例を示します。

```yaml
...
  volumes:
  - name: azure
    persistentVolumeClaim:
      claimName: azurefile
```

## <a name="next-steps"></a>次のステップ

関連するベスト プラクティスについては、[AKS のストレージとバックアップに関するベスト プラクティス][operator-best-practices-storage]に関する記事を参照してください。

AKS クラスターと Azure Files の操作について詳しくは、[Azure Files 対応の Kubernetes プラグイン][kubernetes-files]に関するページを参照してください。

ストレージ クラスのパラメーターについては、「[静的プロビジョニング (独自のファイル共有を使用)](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/docs/driver-parameters.md#static-provisionbring-your-own-file-share)」を参照してください。

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#create
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[smb-overview]: /windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview
[kubernetes-security-context]: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-storage-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-key-list]: /cli/azure/storage/account/keys#az-storage-account-keys-list
[az-storage-share-create]: /cli/azure/storage/share#az-storage-share-create
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
