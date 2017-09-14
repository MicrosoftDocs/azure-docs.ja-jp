---
title: "クイックスタート - Linux 用 Azure Kubernetes クラスター | Microsoft Docs"
description: "Azure CLI を使用して Azure Container Service で Linux コンテナー用 Kubernetes クラスターを作成する方法を簡単に説明します。"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 8da267e8-2aeb-4c24-9a7a-65bdca3a82d6
ms.service: container-service
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/21/2017
ms.author: nepeters
ms.custom: H1Hack27Feb2017, mvc
ms.translationtype: HT
ms.sourcegitcommit: 646886ad82d47162a62835e343fcaa7dadfaa311
ms.openlocfilehash: 5a2131659903e79b28f4d1b795d25a31d8d4ce8d
ms.contentlocale: ja-jp
ms.lasthandoff: 08/25/2017

---

# <a name="deploy-kubernetes-cluster-for-linux-containers"></a>Linux コンテナー用の Kubernetes クラスターをデプロイする

このクイック スタートでは、Azure CLI を使用して Kubernetes クラスターをデプロイします。 次に、Web フロントエンドと Redis インスタンスで構成される複数コンテナー アプリケーションをデプロイして、このクラスターで実行します。 完了すると、このアプリケーションはインターネット経由でアクセス可能になります。 

このドキュメントで使用されているサンプル アプリケーションは、Python で作成されています。 ここで詳しく説明している概念と手順は、あらゆるコンテナー イメージを Kubernetes クラスターにデプロイする目的で使用できます。 このプロジェクトに関連してあらかじめ作成された Kubernetes マニフェスト ファイルとコード、Dockerfile は、[GitHub](https://github.com/Azure-Samples/azure-voting-app-redis.git) で入手できます。

![Azure Vote にブラウザーでアクセスしたところ](media/container-service-kubernetes-walkthrough/azure-vote.png)

このクイック スタートは、Kubernetes の概念についての基礎知識があることを前提としています。Kubernetes の詳細については、[Kubernetes のドキュメント]( https://kubernetes.io/docs/home/)を参照してください。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、このクイック スタートを実施するには、Azure CLI バージョン 2.0.4 以降を実行している必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli)」を参照してください。 

## <a name="create-a-resource-group"></a>リソース グループの作成

[az group create](/cli/azure/group#create) コマンドでリソース グループを作成します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理グループです。 

次の例では、*myResourceGroup* という名前のリソース グループを *westeurope* の場所に作成します。

```azurecli-interactive 
az group create --name myResourceGroup --location westeurope
```

出力:

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup",
  "location": "westeurope",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-kubernetes-cluster"></a>Kubernetes クラスターを作成する

[az acs create](/cli/azure/acs#create) コマンドを使用して Azure Container Service に Kubernetes クラスターを作成します。 次の例では、1 つの Linux マスター ノードと 3 つの Linux エージェント ノードを含む、*myK8sCluster* という名前のクラスターを作成します。

```azurecli-interactive 
az acs create --orchestrator-type kubernetes --resource-group myResourceGroup --name myK8sCluster --generate-ssh-keys 
```

数分してコマンドが完了すると、このクラスターに関する情報が json 形式で表示されます。 

## <a name="connect-to-the-cluster"></a>クラスターへの接続

Kubernetes クラスターを管理するには、Kubernetes のコマンドライン クライアントである [kubectl](https://kubernetes.io/docs/user-guide/kubectl/) を使用します。 

Azure CloudShell を使用している場合、kubectl は既にインストールされています。 ローカルにインストールする場合には、[az acs kubernetes install-cli](/cli/azure/acs/kubernetes#install-cli) コマンドを使用します。

Kubernetes クラスターに接続するように kubectl を構成するには、[az acs kubernetes get-credentials](/cli/azure/acs/kubernetes#get-credentials) コマンドを実行します。 この手順では、資格情報をダウンロードし、Kubernetes CLI を構成してそれらの資格情報を使用します。

```azurecli-interactive 
az acs kubernetes get-credentials --resource-group=myResourceGroup --name=myK8sCluster
```

クラスターへの接続を確認するには、[kubectl get](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get) コマンドを使用して、クラスター ノードの一覧を返します。

```azurecli-interactive
kubectl get nodes
```

出力:

```bash
NAME                    STATUS                     AGE       VERSION
k8s-agent-14ad53a1-0    Ready                      10m       v1.6.6
k8s-agent-14ad53a1-1    Ready                      10m       v1.6.6
k8s-agent-14ad53a1-2    Ready                      10m       v1.6.6
k8s-master-14ad53a1-0   Ready,SchedulingDisabled   10m       v1.6.6
```

## <a name="run-the-application"></a>アプリケーションの実行

Kubernetes のマニフェスト ファイルでは、どのようなコンテナー イメージを実行するかというようなことも含め、クラスターの望ましい状態を定義します。 この例では、マニフェストを使用して、Azure Vote アプリケーションを実行するために必要なすべてのオブジェクトを作成します。 

`azure-vote.yml` という名前のファイルを作成し、そこに以下の YAML をコピーします。 Azure Cloud Shell で作業している場合、仮想システムまたは物理システムで作業するときと同じように vi または Nano を使用してこのファイルを作成できます。

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: azure-vote-back
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: azure-vote-back
    spec:
      containers:
      - name: azure-vote-back
        image: redis
        ports:
        - containerPort: 6379
          name: redis
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-back
spec:
  ports:
  - port: 6379
  selector:
    app: azure-vote-back
---
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: azure-vote-front
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: azure-vote-front
    spec:
      containers:
      - name: azure-vote-front
        image: microsoft/azure-vote-front:redis-v1
        ports:
        - containerPort: 80
        env:
        - name: REDIS
          value: "azure-vote-back"
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

[kubectl create](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#create) コマンドを使用してアプリケーションを実行します。

```azurecli-interactive
kubectl create -f azure-vote.yml
```

出力:

```bash
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-the-application"></a>アプリケーションをテストする

アプリケーションが実行されると、アプリケーション フロントエンドをインターネットに公開する [Kubernetes サービス](https://kubernetes.io/docs/concepts/services-networking/service/)が作成されます。 このプロセスが完了するまでに数分かかることがあります。 

進行状況を監視するには、[kubectl get service](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get) コマンドを `--watch` 引数と一緒に使用します。

```azurecli-interactive
kubectl get service azure-vote-front --watch
```

最初に、*azure-vote-front* サービスの **EXTERNAL-IP** が "*保留中*" として表示されます。 EXTERNAL-IP アドレスが "*保留中*" から "*IP アドレス*" に変わると、`CTRL-C` を使用してkubectl ウォッチ プロセスを停止します。 
  
```bash
azure-vote-front   10.0.34.242   <pending>     80:30676/TCP   7s
azure-vote-front   10.0.34.242   52.179.23.131   80:30676/TCP   2m
```

これで、外部 IP アドレスを参照して Azure Vote アプリを表示できるようになりました。

![Azure Vote にブラウザーでアクセスしたところ](media/container-service-kubernetes-walkthrough/azure-vote.png)  

## <a name="delete-cluster"></a>クラスターを削除する
クラスターが必要なくなったら、[az group delete](/cli/azure/group#delete) コマンドを使用して、リソース グループ、コンテナー サービス、およびすべての関連リソースを削除できます。

```azurecli-interactive 
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="get-the-code"></a>コードの入手

このクイック スタートでは、事前に作成したコンテナー イメージを使用して、Kubernetes のデプロイを作成しました。 関連するアプリケーション コード、Dockerfile、および Kubernetes マニフェスト ファイルは、GitHub で入手できます。

[https://github.com/Azure-Samples/azure-voting-app-redis](https://github.com/Azure-Samples/azure-voting-app-redis.git)

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、Kubernetes クラスターをデプロイし、そこに複数コンテナー アプリケーションをデプロイしました。 

Azure Container Service の詳細を参照し、デプロイの例の完全なコードを確認するには、Kubernetes クラスター チュートリアルに進んでください。

> [!div class="nextstepaction"]
> [ACS Kubernetes クラスターの管理](./container-service-tutorial-kubernetes-prepare-app.md)

