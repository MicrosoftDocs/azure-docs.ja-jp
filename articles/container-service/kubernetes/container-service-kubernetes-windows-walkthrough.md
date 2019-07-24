---
title: (非推奨) クイック スタート - Windows 用 Azure Kubernetes クラスター
description: Azure CLI を使用して Azure Container Service で Windows コンテナー用 Kubernetes クラスターを作成する方法を簡単に説明します。
services: container-service
author: dlepow
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/18/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017, mvc, devcenter
ms.openlocfilehash: d7ce702bb726fb89780d251f31023c9490112c36
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/02/2019
ms.locfileid: "55659390"
---
# <a name="deprecated-deploy-kubernetes-cluster-for-windows-containers"></a>(非推奨) Windows コンテナー用の Kubernetes クラスターをデプロイする

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

Azure CLI は、コマンドラインやスクリプトで Azure リソースを作成および管理するために使用します。 このガイドでは、Azure CLI を使用して、[Kubernetes](https://kubernetes.io/docs/home/) クラスターを [Azure Container Service](../container-service-intro.md) にデプロイする方法を詳しく説明します。 クラスターをデプロイしたら、Kubernetes `kubectl` コマンドライン ツールを使用してクラスターに接続し、最初の Windows コンテナーをデプロイします。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、このクイック スタートを実施するには、Azure CLI バージョン 2.0.4 以降を実行している必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。 

> [!NOTE]
> Azure Container Service における Kubernetes での Windows コンテナーのサポートはプレビュー段階です。 
>

## <a name="create-a-resource-group"></a>リソース グループの作成

[az group create](/cli/azure/group#az-group-create) コマンドでリソース グループを作成します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理グループです。 

次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-kubernetes-cluster"></a>Kubernetes クラスターを作成する
[az acs create](/cli/azure/acs#az-acs-create) コマンドを使用して Azure Container Service に Kubernetes クラスターを作成します。 

次の例では、1 つの Windows マスター ノードと 2 つの Linux エージェント ノードを含む、*myK8sCluster* という名前のクラスターを作成します。 この例では、Linux のマスターに接続するために必要な SSH キーを作成します。 この例では、Windows ノードの管理ユーザーの名前に *azureuser*、パスワードに *myPassword12* を使用します。 これらの値を、環境に適した内容に更新します。 



```azurecli-interactive 
az acs create --orchestrator-type=kubernetes \
    --resource-group myResourceGroup \
    --name=myK8sCluster \
    --agent-count=2 \
    --generate-ssh-keys \
    --windows --admin-username azureuser \
    --admin-password myPassword12
```

数分してコマンドが完了すると、デプロイに関する情報が表示されます。

## <a name="install-kubectl"></a>kubectl のインストール

クライアント コンピューターから Kubernetes クラスターに接続するには、Kubernetes コマンドライン クライアント ([`kubectl`](https://kubernetes.io/docs/user-guide/kubectl/)) を使用します。 

Azure CloudShell を使用している場合、`kubectl` は既にインストールされています。 ローカルにインストールする場合には、[az acs kubernetes install-cli](/cli/azure/acs/kubernetes) コマンドを使用します。

次の Azure CLI の例では `kubectl` がご使用のシステムにインストールされます。 Windows では、管理者として次のコマンドを実行します。

```azurecli-interactive 
az acs kubernetes install-cli
```


## <a name="connect-with-kubectl"></a>kubectl を使用して接続する

Kubernetes クラスターに接続するように `kubectl` を構成するには、[az acs kubernetes get-credentials](/cli/azure/acs/kubernetes) コマンドを実行します。 次の例では、Kubernetes クラスターのクラスター構成がダウンロードされます。

```azurecli-interactive 
az acs kubernetes get-credentials --resource-group=myResourceGroup --name=myK8sCluster
```

コンピューターからクラスターへの接続を確認するために、次のコマンドを実行してみます。

```azurecli-interactive
kubectl get nodes
```

`kubectl` によって、マスター ノードとエージェント ノードが一覧表示されます。

```azurecli-interactive
NAME                    STATUS                     AGE       VERSION
k8s-agent-98dc3136-0    Ready                      5m        v1.5.3
k8s-agent-98dc3136-1    Ready                      5m        v1.5.3
k8s-master-98dc3136-0   Ready,SchedulingDisabled   5m        v1.5.3

```

## <a name="deploy-a-windows-iis-container"></a>Windows IIS コンテナーをデプロイする

1 つ以上のコンテナーが含まれる Kubernetes の "*ポッド*" 内で Docker コンテナーを実行できます。 

この基本的な例では、JSON ファイルを使って Microsoft Internet Information Server (IIS) コンテナーを指定し、その後、`kubctl apply` を使ってポッドを作成します。 

`iis.json` という名前のローカル ファイルを作成し、次のテキストをコピーします。 このファイルによって、[Docker Hub](https://hub.docker.com/r/microsoft/iis/) からのパブリック コンテナー イメージを使用し、Windows Server 2016 Nano Server で IIS を実行するよう Kubernetes に伝えられます。 このコンテナーにはポート 80 が使用されていますが、初期状態ではクラスター ネットワーク内からしかアクセスできません。

 ```JSON
 {
  "apiVersion": "v1",
  "kind": "Pod",
  "metadata": {
    "name": "iis",
    "labels": {
      "name": "iis"
    }
  },
  "spec": {
    "containers": [
      {
        "name": "iis",
        "image": "microsoft/iis:nanoserver",
        "ports": [
          {
          "containerPort": 80
          }
        ]
      }
    ],
    "nodeSelector": {
     "beta.kubernetes.io/os": "windows"
     }
   }
 }
 ```

ポッドを開始するには次のように入力します。
  
```azurecli-interactive
kubectl apply -f iis.json
```  

デプロイを追跡するには次のように入力します。
  
```azurecli-interactive
kubectl get pods
```

ポッドのデプロイ中は、状態が `ContainerCreating` になります。 コンテナーの状態が `Running` になるまでに数分かかることがあります。

```azurecli-interactive
NAME     READY        STATUS        RESTARTS    AGE
iis      1/1          Running       0           32s
```

## <a name="view-the-iis-welcome-page"></a>IIS のようこそページの表示

パブリック IP アドレスでポッドを世界に公開するには、次のコマンドを入力します。

```azurecli-interactive
kubectl expose pods iis --port=80 --type=LoadBalancer
```

このコマンドにより、サービスと、サービスのためのパブリック IP アドレスを持つ Azure Load Balancer ルールが、Kubernetes によって作成されます。 

次のコマンドを実行して、サービスの状態を確認します。

```azurecli-interactive
kubectl get svc
```

初期状態では IP アドレスが `pending` として表示されます。 数分後に、`iis`ポッドの外部 IP アドレスが設定されます。
  
```azurecli-interactive
NAME         CLUSTER-IP     EXTERNAL-IP     PORT(S)        AGE       
kubernetes   10.0.0.1       <none>          443/TCP        21h       
iis          10.0.111.25    13.64.158.233   80/TCP         22m
```

任意の Web ブラウザーを使用して、外部 IP アドレスで IIS の既定のようこそページを確認します。

![IIS にブラウザーでアクセスしたところ](./media/container-service-kubernetes-windows-walkthrough/kubernetes-iis.png)  


## <a name="delete-cluster"></a>クラスターを削除する
クラスターが必要なくなったら、[az group delete](/cli/azure/group#az-group-delete) コマンドを使用して、リソース グループ、コンテナー サービス、およびすべての関連リソースを削除できます。

```azurecli-interactive 
az group delete --name myResourceGroup
```


## <a name="next-steps"></a>次の手順

このクイック スタートでは、`kubectl` で接続される Kubernetes クラスターをデプロイし、IIS コンテナーを含むポッドをデプロイしました。 Azure Container Service の詳細を学ぶには、Kubernetes のチュートリアルに進みます。

> [!div class="nextstepaction"]
> [ACS Kubernetes クラスターの管理](container-service-tutorial-kubernetes-prepare-app.md)
