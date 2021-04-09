---
title: クイック スタート:Azure CLI を使用して AKS クラスターをデプロイする
description: Kubernetes クラスターの作成、アプリケーションのデプロイ、および Azure Kubernetes Service (AKS) でのパフォーマンスの監視を、Azure CLI を使用して迅速に行う方法について説明します。
services: container-service
ms.topic: quickstart
ms.date: 02/26/2021
ms.custom:
- H1Hack27Feb2017
- mvc
- devcenter
- seo-javascript-september2019
- seo-javascript-october2019
- seo-python-october2019
- devx-track-azurecli
- contperf-fy21q1
ms.openlocfilehash: b3d6c7695f74c048cb03e3f4e7ae822005c81c06
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103492882"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-cluster-using-the-azure-cli"></a>クイック スタート:Azure CLI を使用して Azure Kubernetes Service クラスターをデプロイする

Azure Kubernetes Service (AKS) は、クラスターをすばやくデプロイおよび管理することができる、マネージド Kubernetes サービスです。 このクイックスタートでは次の作業を行います。
* Azure CLI を使用して AKS クラスターをデプロイします。 
* このクラスターで、Web フロント エンドと Redis インスタンスが含まれている複数コンテナー アプリケーションを実行します。 
* アプリケーションを実行するクラスターとポッドの正常性を監視します。

  ![Azure Kubernetes Service にデプロイされた投票アプリ](./media/container-service-kubernetes-walkthrough/voting-app-deployed-in-azure-kubernetes-service.png)

このクイックスタートは、Kubernetes の基本的な概念を理解していることを前提としています。 詳細については、「[Azure Kubernetes Services (AKS) における Kubernetes の中心概念][kubernetes-concepts]」を参照してください。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Windows Server ノード プールの作成の詳細については、[Windows Server コンテナーをサポートする AKS クラスターの作成][windows-container-cli]に関するページを参照してください。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- この記事では、Azure CLI のバージョン 2.0.64 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

> [!NOTE]
> このクイックスタートのコマンドを、Azure Cloud Shell ではなくローカルで実行する場合は、管理者としてコマンドを実行してください。

## <a name="create-a-resource-group"></a>リソース グループを作成する

[Azure リソース グループ](../azure-resource-manager/management/overview.md)は、Azure リソースが展開され管理される論理グループです。 リソース グループを作成する際は、場所の指定を求めるプロンプトが表示されます。 この場所は次のとおりです。 
* リソース グループ メタデータのストレージの場所。
* リソースの作成時に別のリージョンを指定しない場合に、Azure でリソースが実行される場所です。 

次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。

[az group create][az-group-create] コマンドを使用して、リソース グループを作成します。


```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

リソース グループが正常に作成された場合の出力:

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup",
  "location": "eastus",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="enable-cluster-monitoring"></a>クラスターの監視を有効にする

1. *Microsoft.OperationsManagement* と *Microsoft.OperationalInsights* がサブスクリプションで登録されていることを確認してください。 登録状態を確認するには:

    ```azurecli
    az provider show -n Microsoft.OperationsManagement -o table
    az provider show -n Microsoft.OperationalInsights -o table
    ```
 
    登録されていない場合は、次を使用して、*Microsoft.OperationsManagement* と *Microsoft.OperationalInsights* を登録します。
 
    ```azurecli
    az provider register --namespace Microsoft.OperationsManagement
    az provider register --namespace Microsoft.OperationalInsights
    ```

2. [Azure Monitor for containers][azure-monitor-containers] を、 *--enable-addons monitoring* パラメーターを使用して有効にします。 

## <a name="create-aks-cluster"></a>AKS クラスターの作成

[az aks create][az-aks-create] コマンドを使用して、AKS クラスターを作成します。 次の例では、*myAKSCluster* という名前のクラスターを 1 つのノードで作成します。 

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster --node-count 1 --enable-addons monitoring --generate-ssh-keys
```

数分後、コマンドが完了し、クラスターに関する情報が JSON 形式で返されます。

> [!NOTE]
> AKS クラスターを作成すると、AKS リソースを保存するための 2 つ目のリソース グループが自動的に作成されます。 詳細については、「[AKS と一緒にリソース グループが 2 つ作成されるのはなぜでしょうか?](./faq.md#why-are-two-resource-groups-created-with-aks)」を参照してください。

## <a name="connect-to-the-cluster"></a>クラスターに接続する

Kubernetes クラスターを管理するには、Kubernetes のコマンドライン クライアントである [kubectl][kubectl] を使います。 Azure Cloud Shell を使用している場合、`kubectl` は既にインストールされています。 

1. [az aks install-cli][az-aks-install-cli] コマンドを使用して、`kubectl` をローカルにインストールします。

    ```azurecli
    az aks install-cli
    ```

2. [az aks get-credentials][az-aks-get-credentials] コマンドを使用して、Kubernetes クラスターに接続するように `kubectl` を構成します。 次のコマンドで、以下を行います。  
      * 資格情報をダウンロードし、それを使用するように Kubernetes CLI を構成します。
      * `~/.kube/config` ([Kubernetes 構成ファイル][kubeconfig-file]の既定の場所) を使用します。 Kubernetes 構成ファイルに対して別の場所を指定するには、 *--file* を使用します。


    ```azurecli-interactive
    az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
    ```

3. [kubectl get][kubectl-get] コマンドを使用して、ご利用のクラスターへの接続を確認します。 このコマンドでは、クラスター ノードの一覧が返されます。

    ```azurecli-interactive
    kubectl get nodes
    ```

    出力は、前の手順で作成した単一ノードを示しています。 ノードの状態が "*準備完了*" であることを確認します。

    ```output
    NAME                       STATUS   ROLES   AGE     VERSION
    aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.12.8
    ```

## <a name="run-the-application"></a>アプリケーションの実行

[Kubernetes のマニフェスト ファイル][kubernetes-deployment]では、どのコンテナー イメージを実行するかなど、クラスターの望ましい状態を定義します。 

このクイックスタートでは、マニフェストを使用して、[Azure Vote アプリケーション][azure-vote-app]を実行するために必要なすべてのオブジェクトを作成します。 このマニフェストには、次の 2 つの [Kubernetes デプロイ][kubernetes-deployment]が含まれています。
* サンプルの Azure Vote Python アプリケーション。
* Redis インスタンス。 

次の 2 つの [Kubernetes サービス][kubernetes-service]も作成されます。
* Redis インスタンスの内部サービス。
* インターネットから Azure Vote アプリケーションにアクセスするための外部サービス。

1. `azure-vote.yaml` という名前でファイルを作成します。
    * Azure Cloud Shell を使用する場合は、仮想システムまたは物理システムで作業するときと同じように、`code`、`vi`、`nano` のいずれかを使用してこのファイルを作成できます。
1. 次の YAML 定義をコピーします。

    ```yaml
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: azure-vote-back
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: azure-vote-back
      template:
        metadata:
          labels:
            app: azure-vote-back
        spec:
          nodeSelector:
            "beta.kubernetes.io/os": linux
          containers:
          - name: azure-vote-back
            image: mcr.microsoft.com/oss/bitnami/redis:6.0.8
            env:
            - name: ALLOW_EMPTY_PASSWORD
              value: "yes"
            resources:
              requests:
                cpu: 100m
                memory: 128Mi
              limits:
                cpu: 250m
                memory: 256Mi
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
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: azure-vote-front
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: azure-vote-front
      template:
        metadata:
          labels:
            app: azure-vote-front
        spec:
          nodeSelector:
            "beta.kubernetes.io/os": linux
          containers:
          - name: azure-vote-front
            image: mcr.microsoft.com/azuredocs/azure-vote-front:v1
            resources:
              requests:
                cpu: 100m
                memory: 128Mi
              limits:
                cpu: 250m
                memory: 256Mi
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

1. [kubectl apply][kubectl-apply] コマンドを使用してアプリケーションをデプロイし、ご利用の YAML マニフェストの名前を指定します。

    ```console
    kubectl apply -f azure-vote.yaml
    ```

    出力は、正常に作成されたデプロイとサービスを示しています。

    ```output
    deployment "azure-vote-back" created
    service "azure-vote-back" created
    deployment "azure-vote-front" created
    service "azure-vote-front" created
    ```

## <a name="test-the-application"></a>アプリケーションをテストする

アプリケーションが実行されると、Kubernetes サービスによってアプリケーション フロント エンドがインターネットに公開されます。 このプロセスが完了するまでに数分かかることがあります。

[kubectl get service][kubectl-get] コマンドと `--watch` 引数を使用して、進行状況を監視します。

```azurecli-interactive
kubectl get service azure-vote-front --watch
```

`azure-vote-front` サービスの **EXTERNAL-IP** の出力は、最初は *pending* として表示されます。

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

**EXTERNAL-IP** アドレスが *保留中* から実際のパブリック IP アドレスに変わったら、`CTRL-C` を使用して `kubectl` ウォッチ プロセスを停止します。 次の出力例は、サービスに割り当てられている有効なパブリック IP アドレスを示しています。

```output
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Azure Vote アプリが動作していることを確認するには、Web ブラウザーを開いてサービスの外部 IP アドレスにアクセスします。

![Azure Kubernetes Service にデプロイされた投票アプリ](./media/container-service-kubernetes-walkthrough/voting-app-deployed-in-azure-kubernetes-service.png)

[Azure Monitor for containers][azure-monitor-containers] によってキャプチャされたクラスター ノードとポッドの正常性メトリックを Azure portal で確認します。 

## <a name="delete-the-cluster"></a>クラスターを削除する

Azure の課金を回避するために、不要なリソースをクリーンアップしてください。 [az group delete][az-group-delete] コマンドを使用して、リソース グループ、コンテナー サービス、およびすべての関連リソースを削除します。

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> クラスターを削除したとき、AKS クラスターで使用される Azure Active Directory サービス プリンシパルは削除されません。 サービス プリンシパルを削除する手順については、[AKS のサービス プリンシパルに関する考慮事項と削除][sp-delete]に関するページを参照してください。
> 
> マネージド ID を使用した場合、ID はプラットフォームによって管理されるので、削除する必要はありません。

## <a name="get-the-code"></a>コードの入手

このクイック スタートでは、Kubernetes のデプロイを作成するために、既存のコンテナー イメージを使用しました。 関連するアプリケーション コード、Dockerfile、Kubernetes マニフェスト ファイルは、[GitHub で入手できます。][azure-vote-app]

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、Kubernetes クラスターをデプロイし、そこに複数コンテナー アプリケーションをデプロイしました。 AKS クラスターの [Kubernetes Web ダッシュボードにアクセス][kubernetes-dashboard]します。

AKS の詳細を参照し、デプロイの例の完全なコードを確認するには、Kubernetes クラスター チュートリアルに進んでください。

> [!div class="nextstepaction"]
> [AKS チュートリアル][aks-tutorial]

<!-- LINKS - external -->
[azure-vote-app]: https://github.com/Azure-Samples/azure-voting-app-redis.git
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubeconfig-file]: https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[aks-monitor]: ../azure-monitor/containers/container-insights-onboard.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-browse]: /cli/azure/aks#az-aks-browse
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks#az-aks-install-cli
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli-install]: /cli/azure/install-azure-cli
[azure-monitor-containers]: ../azure-monitor/containers/container-insights-overview.md
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[azure-portal]: https://portal.azure.com
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[kubernetes-dashboard]: kubernetes-dashboard.md
[windows-container-cli]: windows-container-cli.md