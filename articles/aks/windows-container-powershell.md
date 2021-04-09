---
title: PowerShell を使用して AKS クラスター上に Windows Server コンテナーを作成する
description: 迅速に Kubernetes クラスターを作成し、PowerShell を使用して Azure Kubernetes Service (AKS) で Windows Server コンテナーにアプリケーションをデプロイする方法について説明します。
services: container-service
ms.topic: article
ms.date: 03/12/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b877ecbdca06ff73d152e1b491e993798a99f98a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103233516"
---
# <a name="create-a-windows-server-container-on-an-azure-kubernetes-service-aks-cluster-using-powershell"></a>PowerShell を使用して Azure Kubernetes Service (AKS) クラスター上に Windows Server コンテナーを作成する

Azure Kubernetes Service (AKS) は、クラスターをすばやくデプロイおよび管理することができる、マネージド Kubernetes サービスです。 この記事では、PowerShell を使用して AKS クラスターをデプロイします。 また、Windows Server コンテナー内の `ASP.NET` サンプル アプリケーションをクラスターにデプロイします。

![ASP.NET サンプル アプリケーションを参照している画像](media/windows-container-powershell/asp-net-sample-app.png)

この記事では、Kubernetes の基本的な概念を理解していることを前提としています。 詳細については、「[Azure Kubernetes Services (AKS) における Kubernetes の中心概念][kubernetes-concepts]」を参照してください。

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

ローカルで PowerShell を使用する場合は、Az PowerShell モジュールをインストールしたうえで、[Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) コマンドレットを使用して自分の Azure アカウントに接続する必要があります。 Az PowerShell モジュールのインストールの詳細については、「[Azure PowerShell のインストール][install-azure-powershell]」を参照してください。 また、Az.Aks PowerShell モジュールもインストールする必要があります。 

```azurepowershell-interactive
Install-Module Az.Aks
```

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

複数の Azure サブスクリプションをお持ちの場合は、リソースが課金の対象となる適切なサブスクリプションを選択してください。 [Set-AzContext](/powershell/module/az.accounts/set-azcontext) コマンドレットを使用して、特定のサブスクリプション ID を選択します。

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="limitations"></a>制限事項

複数のノード プールをサポートする AKS クラスターを作成および管理する場合には、次の制限があります。

* 最初のノード プールは削除できません。

次の追加の制限事項は Windows Server ノード プールに該当します。

* AKS クラスターでは、最大で 10 のノード プールを作成できます。
* AKS クラスターでは、最大で 100 のノードを各ノード プールで作成できます。
* Windows Server ノード プール名は 6 文字に制限されています。

## <a name="create-a-resource-group"></a>リソース グループを作成する

[Azure リソース グループ](../azure-resource-manager/management/overview.md)は、Azure リソースが展開され管理される論理グループです。 リソース グループを作成する際は、場所を指定するよう求められます。 この場所は、リソース グループのメタデータが格納される場所です。リソースの作成時に別のリージョンを指定しない場合は、Azure でリソースが実行される場所でもあります。 [New-AzResourceGroup][new-azresourcegroup] コマンドレットを使用してリソース グループを作成します。

次の例では、**myResourceGroup** という名前のリソース グループを **eastus** に作成します。

> [!NOTE]
> この記事では、このチュートリアルのコマンドに対して PowerShell 構文を使用します。 Azure Cloud Shell を使用する場合、[Cloud Shell] ウィンドウの左上のドロップダウンが **[PowerShell]** に設定されていることを確認してください。

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

## <a name="create-an-aks-cluster"></a>AKS クラスターを作成する

`ssh-keygen` コマンドライン ユーティリティを使用して、SSH キー ペアを生成します。 詳細については、「[簡単な手順: Azure 内に Linux VM 用の SSH 公開/秘密キーのペアを作成して使用する](../virtual-machines/linux/mac-create-ssh-keys.md)」を参照してください。

Windows Server コンテナー用のノード プールをサポートする AKS クラスターを実行するには、[Azure CNI][azure-cni-about] の (高度な) ネットワーク プラグインを使用するネットワーク ポリシーを、ご利用のクラスターで使用する必要があります。 必要なサブネット範囲とネットワークに関する考慮事項を計画するのに役立つ詳細情報については、[Azure CNI ネットワークの構成][use-advanced-networking]に関するページを参照してください。 **myAKSCluster** という名前の AKS クラスターを作成するには、下の [New-AzAks][new-azaks] コマンドレットを使用します。 次の例では、必要なネットワーク リソースが存在しない場合、それらが作成されます。

> [!NOTE]
> クラスターが確実に動作するようにするには、既定のノード プールで少なくとも 2 つのノードを実行する必要があります。

```azurepowershell-interactive
$Username = Read-Host -Prompt 'Please create a username for the administrator credentials on your Windows Server containers: '
$Password = Read-Host -Prompt 'Please create a password for the administrator credentials on your Windows Server containers: ' -AsSecureString
New-AzAksCluster -ResourceGroupName myResourceGroup -Name myAKSCluster -NodeCount 2 -NetworkPlugin azure -NodeVmSetType VirtualMachineScaleSets -WindowsProfileAdminUserName $Username -WindowsProfileAdminUserPassword $Password
```

> [!Note]
> このリージョンでバージョンがサポートされていないために AKS クラスターを作成できない場合は、`Get-AzAksVersion -Location eastus` コマンドを使用して、このリージョンでサポートされているバージョンの一覧を確認することができます。

数分後、コマンドが完了し、クラスターに関する情報が返されます。 場合によっては、クラスターのプロビジョニングに数分以上かかることがあります。 このような場合は、最大 10 分と考えてください。

## <a name="add-a-windows-server-node-pool"></a>Windows Server ノード プールを追加する

既定では、Linux コンテナーを実行できるノード プールを使用して、AKS クラスターが作成されます。 Windows Server コンテナーを Linux ノード プールと共に実行できるノード プールをさらに追加するには、`New-AzAksNodePool` コマンドレットを使用します。

```azurepowershell-interactive
New-AzAksNodePool -ResourceGroupName myResourceGroup -ClusterName myAKSCluster -VmSetType VirtualMachineScaleSets -OsType Windows -Name npwin
```

上記のコマンドでは、**npwin** という名前の新しいノード プールが作成され、それが **myAKSCluster** に追加されます。 Windows Server コンテナーを実行するノード プールを作成する場合、**VmSize** の既定値は **Standard_D2s_v3** となります。 **VmSize** パラメーターを設定することを選択した場合は、[制限された VM サイズ][restricted-vm-sizes]の一覧を確認してください。 推奨される最小サイズは、**Standard_D2s_v3** です。 前記のコマンドではまた、`New-AzAks` の実行時に作成された既定の VNET 内の既定のサブネットが使用されます。

## <a name="connect-to-the-cluster"></a>クラスターに接続する

Kubernetes クラスターを管理するには、Kubernetes のコマンドライン クライアントである [kubectl][kubectl] を使用します。 Azure Cloud Shell を使用している場合、`kubectl` は既にインストールされています。 `kubectl` をローカルにインストールするには、`Install-AzAksKubectl` コマンドレットを使用します。

```azurepowershell-interactive
Install-AzAksKubectl
```

Kubernetes クラスターに接続するように `kubectl` を構成するには、[Import-AzAksCredential][import-azakscredential] コマンドレットを使用します。 このコマンドは、資格情報をダウンロードし、それを使用するように Kubernetes CLI を構成します。

```azurepowershell-interactive
Import-AzAksCredential -ResourceGroupName myResourceGroup -Name myAKSCluster
```

クラスターへの接続を確認するには、クラスター ノードの一覧を返す [kubectl get][kubectl-get] コマンドを使用します。

```azurepowershell-interactive
kubectl get nodes
```

次の出力例は、クラスター内のすべてのノードを示しています。 すべてのノードの状態が **[準備完了]** であることを確認します。

```plaintext
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.16.7
aksnpwin987654                      Ready    agent   108s   v1.16.7
```

## <a name="run-the-application"></a>アプリケーションの実行

Kubernetes のマニフェスト ファイルでは、どのコンテナー イメージを実行するかなど、クラスターの望ましい状態を定義します。 この記事では、Windows Server コンテナー内で ASP.NET サンプル アプリケーションを実行するために必要なすべてのオブジェクトを作成するのにマニフェストを使用します。 このマニフェストには、ASP.NET サンプル アプリケーションの [Kubernetes デプロイ][kubernetes-deployment]と、インターネットからアプリケーションにアクセスするための外部 [Kubernetes サービス][kubernetes-service]が含まれています。

ASP.NET サンプル アプリケーションは、[.NET Framework のサンプル][dotnet-samples]の一部として提供され、Windows Server コンテナー内で実行されます。 AKS では、Windows Server コンテナーは **Windows Server 2019** 以降のイメージをベースにしている必要があります。 Kubernetes マニフェスト ファイルではまた、[ノード セレクター][node-selector]を定義する必要があり、この定義では、Windows Server コンテナーを実行できるノード上でご利用の ASP.NET サンプル アプリケーションのポッドを実行するように AKS クラスターに指示します。

`sample.yaml` という名前のファイルを作成し、以下の YAML 定義をコピーします。 Azure Cloud Shell を使用する場合は、仮想システムまたは物理システムで作業するときと同じように、`vi` または `nano` を使用してこのファイルを作成できます。

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sample
  labels:
    app: sample
spec:
  replicas: 1
  template:
    metadata:
      name: sample
      labels:
        app: sample
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": windows
      containers:
      - name: sample
        image: mcr.microsoft.com/dotnet/framework/samples:aspnetapp
        resources:
          limits:
            cpu: 1
            memory: 800M
          requests:
            cpu: .1
            memory: 300M
        ports:
          - containerPort: 80
  selector:
    matchLabels:
      app: sample
---
apiVersion: v1
kind: Service
metadata:
  name: sample
spec:
  type: LoadBalancer
  ports:
  - protocol: TCP
    port: 80
  selector:
    app: sample
```

[kubectl apply][kubectl-apply] コマンドを使用してアプリケーションをデプロイし、ご利用の YAML マニフェストの名前を指定します。

```azurepowershell-interactive
kubectl apply -f sample.yaml
```

次の出力例は、正常に作成されたデプロイおよびサービスを示しています。

```plaintext
deployment.apps/sample created
service/sample created
```

## <a name="test-the-application"></a>アプリケーションをテストする

アプリケーションが実行されると、Kubernetes サービスによってアプリケーション フロントエンドがインターネットに公開されます。
このプロセスが完了するまでに数分かかることがあります。 場合によっては、サービスのプロビジョニングに数分以上かかることがあります。 このような場合は、最大 10 分と考えてください。

進行状況を監視するには、[kubectl get service][kubectl-get] コマンドを `--watch` 引数と一緒に使用します。

```azurepowershell-interactive
kubectl get service sample --watch
```

最初に、"**サンプル**" サービスの **EXTERNAL-IP** が "**保留中**" として表示されます。

```plaintext
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
sample             LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

**EXTERNAL-IP** アドレスが "**保留中**" から実際のパブリック IP アドレスに変わったら、`CTRL-C` を使用して `kubectl` ウォッチ プロセスを停止します。 次の出力例は、サービスに割り当てられている有効なパブリック IP アドレスを示しています。

```plaintext
sample  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

サンプル アプリが動作していることを確認するには、Web ブラウザーを開いてサービスの外部 IP アドレスにアクセスします。

![ASP.NET サンプル アプリケーションを参照している画像](media/windows-container-powershell/asp-net-sample-app.png)

> [!Note]
> ページを読み込もうとしたときに接続タイムアウトが発生する場合は、次のコマンド `kubectl get pods --watch` を使用してサンプル アプリの準備ができていることを確認することをお勧めします。 外部 IP アドレスが使用可能になるまでは、Windows コンテナーが起動しない場合があります。

## <a name="delete-cluster"></a>クラスターを削除する

クラスターが必要なくなったら、[Remove-AzResourceGroup][remove-azresourcegroup] コマンドレットを使用して、リソース グループ、コンテナー サービス、およびすべての関連リソースを削除してください。

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

> [!NOTE]
> クラスターを削除したとき、AKS クラスターで使用される Azure Active Directory サービス プリンシパルは削除されません。 サービス プリンシパルを削除する手順については、[AKS のサービス プリンシパルに関する考慮事項と削除][sp-delete]に関するページを参照してください。 マネージド ID を使用した場合、ID はプラットフォームによって管理されるので、削除する必要はありません。

## <a name="next-steps"></a>次のステップ

この記事では、Kubernetes クラスターをデプロイし、そこに、Windows Server コンテナー内の `ASP.NET` サンプル アプリケーションをデプロイしました。 作成したクラスターの [Kubernetes Web ダッシュボードにアクセス][kubernetes-dashboard]します。

AKS の詳細を参照し、デプロイの例の完全なコードを確認するには、Kubernetes クラスター チュートリアルに進んでください。

> [!div class="nextstepaction"]
> [AKS チュートリアル][aks-tutorial]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[dotnet-samples]: https://hub.docker.com/_/microsoft-dotnet-framework-samples/
[node-selector]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[install-azure-powershell]: /powershell/azure/install-az-ps
[new-azresourcegroup]: /powershell/module/az.resources/new-azresourcegroup
[azure-cni-about]: concepts-network.md#azure-cni-advanced-networking
[use-advanced-networking]: configure-azure-cni.md
[new-azaks]: /powershell/module/az.aks/new-azaks
[restricted-vm-sizes]: quotas-skus-regions.md#restricted-vm-sizes
[import-azakscredential]: /powershell/module/az.aks/import-azakscredential
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[remove-azresourcegroup]: /powershell/module/az.resources/remove-azresourcegroup
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[kubernetes-dashboard]: kubernetes-dashboard.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
