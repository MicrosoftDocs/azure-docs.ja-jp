---
title: クイック スタート:PowerShell を使用して AKS クラスターをデプロイする
description: Kubernetes クラスターの作成、アプリケーションのデプロイ、および Azure Kubernetes Service (AKS) でのパフォーマンスの監視を、PowerShell を使用して迅速に行う方法について説明します。
services: container-service
ms.topic: quickstart
ms.date: 08/18/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4dde1dcd131a497b60a314513df44cc0443d28ed
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88589998"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-cluster-using-powershell"></a>クイック スタート:PowerShell を使用して Azure Kubernetes Service クラスターをデプロイする

このクイックスタートでは、PowerShell を使用して Azure Kubernetes Service (AKS) クラスターをデプロイします。 AKS は、クラスターをすばやくデプロイおよび管理することができる、マネージド Kubernetes サービスです。 このクラスターで、Web フロントエンドと Redis インスタンスが含まれている複数コンテナー アプリケーションが実行されます。 その後、アプリケーションを実行するクラスターとポッドの正常性を監視する方法を示します。

Windows Server ノード プールの作成の詳細については、[Windows Server コンテナーをサポートする AKS クラスターの作成][windows-container-powershell]に関するページを参照してください。

![Azure Kubernetes Service にデプロイされた投票アプリ](./media/kubernetes-walkthrough-powershell/voting-app-deployed-in-azure-kubernetes-service.png)

このクイックスタートは、Kubernetes の基本的な概念を理解していることを前提としています。 詳細については、「[Azure Kubernetes Services (AKS) における Kubernetes の中心概念][kubernetes-concepts]」を参照してください。

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

ローカルで PowerShell を使用する場合は、Az PowerShell モジュールをインストールしたうえで、[Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) コマンドレットを使用して自分の Azure アカウントに接続する必要があります。 Az PowerShell モジュールのインストールの詳細については、「[Azure PowerShell のインストール][install-azure-powershell]」を参照してください。

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

複数の Azure サブスクリプションをお持ちの場合は、リソースが課金の対象となる適切なサブスクリプションを選択してください。 [Set-AzContext](/powershell/module/az.accounts/set-azcontext) コマンドレットを使用して、特定のサブスクリプション ID を選択します。

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>リソース グループを作成する

[Azure リソース グループ](../azure-resource-manager/management/overview.md)は、Azure リソースが展開され管理される論理グループです。 リソース グループを作成する際は、場所を指定するよう求められます。 この場所は、リソース グループのメタデータが格納される場所です。リソースの作成時に別のリージョンを指定しない場合は、Azure でリソースが実行される場所でもあります。 [New-AzResourceGroup][new-azresourcegroup] コマンドレットを使用してリソース グループを作成します。

次の例では、**myResourceGroup** という名前のリソース グループを**米国東部**リージョンに作成します。

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location eastus
```

次の出力例では、正常に作成されたリソース グループが示されています。

```plaintext
ResourceGroupName : myResourceGroup
Location          : eastus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup
```

## <a name="create-aks-cluster"></a>AKS クラスターの作成

`ssh-keygen` コマンドライン ユーティリティを使用して、SSH キー ペアを生成します。 詳細については、「[簡単な手順: Azure 内に Linux VM 用の SSH 公開/秘密キーのペアを作成して使用する](../virtual-machines/linux/mac-create-ssh-keys.md)」を参照してください。

AKS クラスターを作成するには、[New-AzAks][new-azaks] コマンドレットを使用します。 次の例では、**myAKSCluster** という名前のクラスターを 1 つのノードで作成します。 Azure Monitor for Containers も既定で有効になっています。 この操作は、完了するまでに数分かかります。

> [!NOTE]
> AKS クラスターを作成すると、AKS リソースを保存するための 2 つ目のリソース グループが自動的に作成されます。 詳細については、「[AKS と一緒にリソース グループが 2 つ作成されるのはなぜでしょうか?](./faq.md#why-are-two-resource-groups-created-with-aks)」を参照してください。

```azurepowershell-interactive
New-AzAks -ResourceGroupName myResourceGroup -Name myAKSCluster -NodeCount 1
```

数分後、コマンドが完了し、クラスターに関する情報が返されます。

## <a name="connect-to-the-cluster"></a>クラスターに接続する

Kubernetes クラスターを管理するには、Kubernetes のコマンドライン クライアントである [kubectl][kubectl] を使用します。 Azure Cloud Shell を使用している場合、`kubectl` は既にインストールされています。 `kubectl` をローカルにインストールするには、`Install-AzAksKubectl` コマンドレットを使用します。

```azurepowershell
Install-AzAksKubectl
```

Kubernetes クラスターに接続するように `kubectl` を構成するには、[Import-AzAksCredential][import-azakscredential] コマンドレットを使用します。 次の例では、資格情報をダウンロードし、それを使用するように Kubernetes CLI を構成します。

```azurepowershell-interactive
Import-AzAksCredential -ResourceGroupName myResourceGroup -Name myAKSCluster
```

クラスターへの接続を確認するには、クラスター ノードの一覧を返す [kubectl get][kubectl-get] コマンドを使用します。

```azurepowershell-interactive
.\kubectl get nodes
```

次の出力例は、前の手順で作成した単一ノードを示しています。 ノードの状態が "**準備完了**" であることを確認します。

```plaintext
NAME                       STATUS   ROLES   AGE     VERSION
aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.15.10
```

## <a name="run-the-application"></a>アプリケーションの実行

Kubernetes のマニフェスト ファイルでは、どのコンテナー イメージを実行するかなど、クラスターの望ましい状態を定義します。 このクイック スタートでは、マニフェストを使用して、Azure Vote アプリケーションを実行するために必要なすべてのオブジェクトを作成します。 このマニフェストには、 [Kubernetes デプロイ][kubernetes-deployment] が 2 つ含まれます。サンプル Azure Vote Python アプリケーション用と Redis インスタンス用です。 さらに、Kubernetes サービスが 2 つ作成されます。Redis インスタンスに使用される内部サービスと、Azure Vote アプリケーションにインターネットからアクセスするための外部サービスです。

`azure-vote.yaml` という名前のファイルを作成し、以下の YAML 定義をコピーします。 Azure Cloud Shell を使用する場合は、仮想システムまたは物理システムで作業するときと同じように、`vi` または `nano` を使用してこのファイルを作成できます。

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
        image: redis
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
        image: microsoft/azure-vote-front:v1
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

[kubectl apply][kubectl-apply] コマンドを使用してアプリケーションをデプロイし、ご利用の YAML マニフェストの名前を指定します。

```azurepowershell-interactive
.\kubectl apply -f azure-vote.yaml
```

次の出力例は、正常に作成されたデプロイおよびサービスを示しています。

```plaintext
deployment.apps/azure-vote-back created
service/azure-vote-back created
deployment.apps/azure-vote-front created
service/azure-vote-front created
```

## <a name="test-the-application"></a>アプリケーションをテストする

アプリケーションが実行されると、Kubernetes サービスによってアプリケーション フロントエンドがインターネットに公開されます。
このプロセスが完了するまでに数分かかることがあります。

進行状況を監視するには、[kubectl get service][kubectl-get] コマンドを `--watch` 引数と一緒に使用します。

```azurepowershell-interactive
.\kubectl get service azure-vote-front --watch
```

最初に、**azure-vote-front** サービスの **EXTERNAL-IP** が "**保留中**" として表示されます。

```plaintext
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

**EXTERNAL-IP** アドレスが "**保留中**" から実際のパブリック IP アドレスに変わったら、`CTRL-C` を使用して `kubectl` ウォッチ プロセスを停止します。 次の出力例は、サービスに割り当てられている有効なパブリック IP アドレスを示しています。

```plaintext
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Azure Vote アプリが動作していることを確認するには、Web ブラウザーを開いてサービスの外部 IP アドレスにアクセスします。

![Azure Kubernetes Service にデプロイされた投票アプリ](./media/kubernetes-walkthrough-powershell/voting-app-deployed-in-azure-kubernetes-service.png)

AKS クラスターが作成されたとき、クラスター ノードとポッドの両方の正常性メトリックを取得するために、[コンテナーに対する Azure Monitor](../azure-monitor/insights/container-insights-overview.md) が有効になりました。 これらの正常性メトリックは、Azure portal で利用できます。

## <a name="delete-the-cluster"></a>クラスターを削除する

Azure の課金を回避するには、不要なリソースをクリーンアップする必要があります。 クラスターが必要なくなったら、[Remove-AzResourceGroup][remove-azresourcegroup] コマンドレットを使用して、リソース グループ、コンテナー サービス、およびすべての関連リソースを削除してください。

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

> [!NOTE]
> クラスターを削除したとき、AKS クラスターで使用される Azure Active Directory サービス プリンシパルは削除されません。 サービス プリンシパルを削除する手順については、[AKS のサービス プリンシパルに関する考慮事項と削除][sp-delete]に関するページを参照してください。 マネージド ID を使用した場合、ID はプラットフォームによって管理されるので、削除する必要はありません。

## <a name="get-the-code"></a>コードの入手

このクイック スタートでは、Kubernetes のデプロイを作成するために、事前に作成したコンテナー イメージを使用しました。 関連するアプリケーション コード、Dockerfile、および Kubernetes マニフェスト ファイルは、GitHub で入手できます。

[https://github.com/Azure-Samples/azure-voting-app-redis][azure-vote-app]

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、Kubernetes クラスターをデプロイし、そこに複数コンテナー アプリケーションをデプロイしました。 また、AKS クラスターの [Kubernetes Web ダッシュボードにアクセスする][kubernetes-dashboard]こともできます。

AKS の詳細を参照し、デプロイの例の完全なコードを確認するには、Kubernetes クラスター チュートリアルに進んでください。

> [!div class="nextstepaction"]
> [AKS チュートリアル][aks-tutorial]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[azure-dev-spaces]: ../dev-spaces/index.yml
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[azure-vote-app]: https://github.com/Azure-Samples/azure-voting-app-redis.git

<!-- LINKS - internal -->
[windows-container-powershell]: windows-container-powershell.md
[kubernetes-concepts]: concepts-clusters-workloads.md
[install-azure-powershell]: /powershell/azure/install-az-ps
[new-azresourcegroup]: /powershell/module/az.resources/new-azresourcegroup
[new-azaks]: /powershell/module/az.aks/new-azaks
[import-azakscredential]: /powershell/module/az.aks/import-azakscredential
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[remove-azresourcegroup]: /powershell/module/az.resources/remove-azresourcegroup
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[kubernetes-dashboard]: kubernetes-dashboard.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
