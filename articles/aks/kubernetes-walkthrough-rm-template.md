---
title: 'クイック スタート: Azure Kubernetes Service (AKS) クラスターの作成'
description: Azure Resource Manager テンプレートを使用して Kubernetes クラスターを迅速に作成し、Azure Kubernetes Service (AKS) にアプリケーションをデプロイする方法を学ぶ
services: container-service
ms.topic: quickstart
ms.date: 03/15/2021
ms.custom: mvc,subject-armqs, devx-track-azurecli
ms.openlocfilehash: e88c56f050f2f6d1183eef23a844f5eaf1f671c2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103492950"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-using-an-arm-template"></a>クイック スタート:ARM テンプレートを使用して Azure Kubernetes Service (AKS) クラスターをデプロイする

Azure Kubernetes Service (AKS) は、クラスターをすばやくデプロイおよび管理することができる、マネージド Kubernetes サービスです。 このクイックスタートでは次の作業を行います。
* Azure Resource Manager テンプレートを使用して AKS クラスターをデプロイします。 
* このクラスターで、Web フロントエンドと Redis インスタンスが含まれている複数コンテナー アプリケーションを実行します。 

![Azure Vote にブラウザーでアクセスしたところ](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

このクイックスタートは、Kubernetes の基本的な概念を理解していることを前提としています。 詳細については、「[Azure Kubernetes Services (AKS) における Kubernetes の中心概念][kubernetes-concepts]」を参照してください。

環境が前提条件を満たしていて、ARM テンプレートの使用に慣れている場合は、 **[Azure へのデプロイ]** ボタンを選択します。 Azure portal でテンプレートが開きます。

[![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-aks%2Fazuredeploy.json)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- この記事では、Azure CLI のバージョン 2.0.61 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

- Resource Manager テンプレートを使用して AKS クラスターを作成するには、SSH 公開キーを指定します。 このリソースが必要な場合は、後のセクションを参照してください。それ以外の場合は、「[テンプレートを確認する](#review-the-template)」セクションに進んでください。

### <a name="create-an-ssh-key-pair"></a>SSH キー ペアの作成

AKS ノードにアクセスするには、`ssh-keygen` コマンドを使用して生成する SSH キーの組 (公開および秘密) を使用して接続します。 既定では、これらのファイルは *~/.ssh* ディレクトリに作成されます。 `ssh-keygen` コマンドを実行すると、指定した場所に同じ名前の SSH キーの組が既に存在する場合は上書きされます。

1. [https://shell.azure.com](https://shell.azure.com) にアクセスし、お使いのブラウザーで Cloud Shell を開きます。

1. `ssh-keygen` コマンドを実行します。 次の例では、RSA 暗号化と 2048 ビット長を使用して SSH キーの組が作成されます。

    ```console
    ssh-keygen -t rsa -b 2048
    ```

SSH キーの作成の詳細については、[Azure での認証用の SSH キーの作成と管理][ssh-keys]に関するページを参照してください。

## <a name="review-the-template"></a>テンプレートを確認する

このクイック スタートで使用されるテンプレートは [Azure クイック スタート テンプレート](https://azure.microsoft.com/resources/templates/101-aks/)からのものです。

:::code language="json" source="~/quickstart-templates/101-aks/azuredeploy.json":::

他の AKS サンプルについては、[AKS クイック スタートのテンプレート][aks-quickstart-templates]に関するサイトを参照してください。

## <a name="deploy-the-template"></a>テンプレートのデプロイ

1. 次のボタンを選択し、Azure にサインインして、テンプレートを開きます。

    [![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-aks%2Fazuredeploy.json)

2. 次の値を選択または入力します。

    このクイック スタートでは、 *[OS ディスク サイズ GB]* 、 *[エージェント数]* 、 *[エージェント VM のサイズ]* 、 *[OS の種類]* 、および *[Kubernetes バージョン]* を既定値のままにしておいてください。 以下のテンプレート パラメーターに、独自の値を指定します。

    * **サブスクリプション**:Azure サブスクリプションを選択します。
    * **[リソース グループ]** : **[新規作成]** を選択します。 リソース グループの一意の名前 (*myResourceGroup* など) を入力し、 **[OK]** を選択します。
    * **[場所]** :場所 (**米国東部** など) を選択します。
    * **[クラスター名]** : AKS クラスターの一意の名前 (*myAKSCluster* など) を入力します。
    * **[DNS プレフィックス]** : クラスターの一意の DNS プレフィックス (*myakscluster* など) を入力します。
    * **[Linux Admin Username]\(Linux 管理者ユーザー名\)** : SSH を使用して接続するためのユーザー名 (*azureuser* など) を入力します。
    * **[SSH RSA Public Key]\(SSH RSA 公開キー\)** : SSH キー ペアの "*公開*" 部分 (既定では、 *~/.ssh/id_rsa.pub* の内容) をコピーして貼り付けます。

    ![ポータルで Azure Kubernetes Service クラスターを作成するための Resource Manager テンプレート](./media/kubernetes-walkthrough-rm-template/create-aks-cluster-using-template-portal.png)

3. **[確認および作成]** を選択します。

AKS クラスターの作成には数分かかります。 クラスターが正常にデプロイされるのを待ってから、次の手順に進みます。

## <a name="validate-the-deployment"></a>デプロイの検証

### <a name="connect-to-the-cluster"></a>クラスターに接続する

Kubernetes クラスターを管理するには、Kubernetes のコマンドライン クライアントである [kubectl][kubectl] を使います。 Azure Cloud Shell を使用している場合、`kubectl` は既にインストールされています。 

1. [az aks install-cli][az-aks-install-cli] コマンドを使用して、`kubectl` をローカルにインストールします。

    ```azurecli
    az aks install-cli
    ```

2. [az aks get-credentials][az-aks-get-credentials] コマンドを使用して、Kubernetes クラスターに接続するように `kubectl` を構成します。 このコマンドは、資格情報をダウンロードし、それを使用するように Kubernetes CLI を構成します。

    ```azurecli-interactive
    az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
    ```

3. [kubectl get][kubectl-get] コマンドを使用して、ご利用のクラスターへの接続を確認します。 このコマンドを実行すると、クラスター ノードの一覧が返されます。

    ```console
    kubectl get nodes
    ```

    出力は、前の手順で作成したノードを示しています。 すべてのノードの状態が "*準備完了*" であることを確認します。

    ```output
    NAME                       STATUS   ROLES   AGE     VERSION
    aks-agentpool-41324942-0   Ready    agent   6m44s   v1.12.6    
    aks-agentpool-41324942-1   Ready    agent   6m46s   v1.12.6
    aks-agentpool-41324942-2   Ready    agent   6m45s   v1.12.6
    ```

### <a name="run-the-application"></a>アプリケーションの実行

[Kubernetes のマニフェスト ファイル][kubernetes-deployment]では、どのコンテナー イメージを実行するかなど、クラスターの望ましい状態を定義します。 

このクイックスタートでは、マニフェストを使用して、[Azure Vote アプリケーション][azure-vote-app]を実行するために必要なすべてのオブジェクトを作成します。 このマニフェストには、次の 2 つの [Kubernetes デプロイ][kubernetes-deployment]が含まれています。
* サンプルの Azure Vote Python アプリケーション。
* Redis インスタンス。 

次の 2 つの [Kubernetes サービス][kubernetes-service]も作成されます。
* Redis インスタンスの内部サービス。
* インターネットから Azure Vote アプリケーションにアクセスするための外部サービス。

1. `azure-vote.yaml` という名前でファイルを作成します。
    * Azure Cloud Shell を使用する場合は、仮想または物理システムで作業するときと同じように、`vi` または `nano` を使用してこのファイルを作成できます。
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

### <a name="test-the-application"></a>アプリケーションをテストする

アプリケーションが実行されると、Kubernetes サービスによってアプリケーション フロント エンドがインターネットに公開されます。 このプロセスが完了するまでに数分かかることがあります。

[kubectl get service][kubectl-get] コマンドと `--watch` 引数を使用して、進行状況を監視します。

```console
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

![Azure Vote にブラウザーでアクセスしたところ](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

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
[azure-dev-spaces]: ../dev-spaces/index.yml
[aks-quickstart-templates]: https://azure.microsoft.com/resources/templates/?term=Azure+Kubernetes+Service

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
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[azure-portal]: https://portal.azure.com
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[kubernetes-dashboard]: kubernetes-dashboard.md
[ssh-keys]: ../virtual-machines/linux/create-ssh-keys-detailed.md
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
