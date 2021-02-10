---
title: Azure Container Registry と Azure Kubernetes Service を統合する
description: Azure Kubernetes Service (AKS) と Azure Container Registry (ACR) を統合する方法について説明します。
services: container-service
manager: gwallace
ms.topic: article
ms.date: 01/08/2021
ms.openlocfilehash: 0d61cccb6b70091194d407eda056060d1fa3623c
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2021
ms.locfileid: "99053892"
---
# <a name="authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>Azure Kubernetes Service から Azure Container Registry の認証を受ける

Azure Kubernetes Service (AKS) で Azure Container Registry (ACR) を使用する場合は、認証メカニズムを確立する必要があります。 この操作は、必要なアクセス許可を ACR に付与することで、CLI とポータルのエクスペリエンスの一部として実装されます。 この記事では、これら 2 つの Azure サービス間の認証を構成する例を示します。 

Azure CLI を使用して、いくつかの単純なコマンドで AKS から ACR への統合を設定できます。 この統合により、AKS クラスターに関連付けられているサービス プリンシパルに AcrPull ロールが割り当てられます。

> [!NOTE]
> この記事では、AKS と ACR の間の自動認証について説明します。 プライベート外部レジストリからイメージをプルする必要がある場合は、[イメージのプル シークレット][Image Pull Secret]を使用します。

## <a name="before-you-begin"></a>開始する前に

これらの例には以下のものが必要です。

* **Azure サブスクリプション** 上の **所有者** または **Azure アカウント管理者** ロール。
* Azure CLI バージョン 2.7.0 以降

**所有者** または **Azure アカウント管理者** の役割を必要としないようにするには、サービス プリンシパルを手動で構成するか、既存のサービス プリンシパルを使用して AKS から ACR を認証します。 詳細については、[サービス プリンシパルによる ACR 認証](../container-registry/container-registry-auth-service-principal.md)または[プル シークレットを使用した Kubernetes からの認証](../container-registry/container-registry-auth-kubernetes.md)に関するページをご参照ください。

## <a name="create-a-new-aks-cluster-with-acr-integration"></a>ACR 統合を使用して新しい AKS クラスターを作成する

AKS クラスターの初期作成中に AKS と ACR の統合を設定できます。  AKS クラスターが ACR と対話できるようにするために、Azure Active Directory の **サービス プリンシパル** が使用されます。 次の CLI コマンドを使用すると、サブスクリプション内の既存の ACR を承認し、サービス プリンシパル用の適切な **ACRPull** ロールを構成することができます。 下のパラメーターの有効な値を指定してください。

```azurecli
# set this to the name of your Azure Container Registry.  It must be globally unique
MYACR=myContainerRegistry

# Run the following line to create an Azure Container Registry if you do not already have one
az acr create -n $MYACR -g myContainerRegistryResourceGroup --sku basic

# Create an AKS cluster with ACR integration
az aks create -n myAKSCluster -g myResourceGroup --generate-ssh-keys --attach-acr $MYACR
```

または、ACR リソース ID を使用して ACR の名前を指定することもできます。その形式は次のようになります。

`/subscriptions/\<subscription-id\>/resourceGroups/\<resource-group-name\>/providers/Microsoft.ContainerRegistry/registries/\<name\>`

> [!NOTE]
> AKS クラスターから別のサブスクリプションにある ACR を使用している場合は、AKS クラスターから接続または接続解除するときに ACR リソース ID を使用します。

```azurecli
az aks create -n myAKSCluster -g myResourceGroup --generate-ssh-keys --attach-acr /subscriptions/<subscription-id>/resourceGroups/myContainerRegistryResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry
```

この手順は、完了するまでに数分かかることがあります。

## <a name="configure-acr-integration-for-existing-aks-clusters"></a>既存の AKS クラスターに対する ACR 統合を構成する

下の **acr-name** または **acr-resource-id** に有効な値を指定することによって、既存の ACR と既存の AKS クラスターを統合します。

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acr-name>
```

または、

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acr-resource-id>
```

次を使用して、ACR と AKS クラスター間の統合を削除することもできます。

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acr-name>
```

or

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acr-resource-id>
```

## <a name="working-with-acr--aks"></a>ACR および AKS の操作

### <a name="import-an-image-into-your-acr"></a>イメージを ACR にインポートする

次を実行して、Docker Hub から ACR にイメージをインポートします。


```azurecli
az acr import  -n <acr-name> --source docker.io/library/nginx:latest --image nginx:v1
```

### <a name="deploy-the-sample-image-from-acr-to-aks"></a>ACR から AKS にサンプル イメージをデプロイする

適切な AKS 資格情報を持っていることを確認します

```azurecli
az aks get-credentials -g myResourceGroup -n myAKSCluster
```

次を含む **acr-nginx.yaml** という名前のファイルを作成します。 レジストリのリソース名の代わりに **acr-name** を使用します。 例: *myContainerRegistry*。

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx0-deployment
  labels:
    app: nginx0-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx0
  template:
    metadata:
      labels:
        app: nginx0
    spec:
      containers:
      - name: nginx
        image: <acr-name>.azurecr.io/nginx:v1
        ports:
        - containerPort: 80
```

次に、AKS クラスターでこのデプロイを実行します。

```console
kubectl apply -f acr-nginx.yaml
```

次を実行して、デプロイを監視できます。

```console
kubectl get pods
```

2 つのポッドが実行されているはずです。

```output
NAME                                 READY   STATUS    RESTARTS   AGE
nginx0-deployment-669dfc4d4b-x74kr   1/1     Running   0          20s
nginx0-deployment-669dfc4d4b-xdpd6   1/1     Running   0          20s
```

### <a name="troubleshooting"></a>トラブルシューティング
* [az aks check-acr](/cli/azure/aks#az_aks_check_acr) コマンドを実行して、レジストリに AKS クラスターからアクセスできることを確認します。
* ACR 診断の詳細については、[こちら](../container-registry/container-registry-diagnostics-audit-logs.md)を参照してください。
* ACR の正常性の詳細については、[こちら](../container-registry/container-registry-check-health.md)を参照してください。

<!-- LINKS - external -->
[AKS AKS CLI]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[Image Pull secret]: https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/