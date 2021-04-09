---
title: Azure CLI を使用して AKS クラスター上に Windows Server コンテナーを作成する
description: 迅速に Kubernetes クラスターを作成し、Azure CLI を使用して Azure Kubernetes Service (AKS) で Windows Server コンテナーにアプリケーションをデプロイする方法について説明します。
services: container-service
ms.topic: article
ms.date: 07/16/2020
ms.openlocfilehash: 13b4fbd21bb348d1ef79a3ca68128869115745cc
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2021
ms.locfileid: "103200902"
---
# <a name="create-a-windows-server-container-on-an-azure-kubernetes-service-aks-cluster-using-the-azure-cli"></a>Azure CLI を使用して Azure Kubernetes Service (AKS) クラスター上に Windows Server コンテナーを作成する

Azure Kubernetes Service (AKS) は、クラスターをすばやくデプロイおよび管理することができる、マネージド Kubernetes サービスです。 この記事では、Azure CLI を使用して AKS クラスターをデプロイします。 また、Windows Server コンテナー内の ASP.NET サンプル アプリケーションをクラスターにデプロイします。

![ASP.NET サンプル アプリケーションを参照している画像](media/windows-container/asp-net-sample-app.png)

この記事では、Kubernetes の基本的な概念を理解していることを前提としています。 詳細については、「[Azure Kubernetes Services (AKS) における Kubernetes の中心概念][kubernetes-concepts]」を参照してください。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

### <a name="limitations"></a>制限事項

複数のノード プールをサポートする AKS クラスターを作成および管理する場合には、次の制限があります。

* 最初のノード プールは削除できません。

次の追加の制限事項は Windows Server ノード プールに該当します。

* AKS クラスターでは、最大で 10 のノード プールを作成できます。
* AKS クラスターでは、最大で 100 のノードを各ノード プールで作成できます。
* Windows Server ノード プール名は 6 文字に制限されています。

## <a name="create-a-resource-group"></a>リソース グループを作成する

Azure リソース グループは、Azure リソースが展開され管理される論理グループです。 リソース グループを作成する際は、場所を指定するよう求められます。 この場所は、リソース グループのメタデータが格納される場所です。リソースの作成時に別のリージョンを指定しない場合は、Azure でリソースが実行される場所でもあります。 [az group create][az-group-create] コマンドを使用して、リソース グループを作成します。

次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。

> [!NOTE]
> この記事では、このチュートリアルのコマンドに対して Bash 構文を使用します。
> Azure Cloud Shell を使用する場合、[Cloud Shell] ウィンドウの左上のドロップダウンが **[Bash]** に設定されていることを確認してください。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

次の出力例では、正常に作成されたリソース グループが示されています。

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup",
  "location": "eastus",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

## <a name="create-an-aks-cluster"></a>AKS クラスターを作成する

Windows Server コンテナー用のノード プールをサポートする AKS クラスターを実行するには、[Azure CNI][azure-cni-about] の (高度な) ネットワーク プラグインを使用するネットワーク ポリシーを、ご利用のクラスターで使用する必要があります。 必要なサブネット範囲とネットワークに関する考慮事項を計画するのに役立つ詳細情報については、[Azure CNI ネットワークの構成][use-advanced-networking]に関するページを参照してください。 *myAKSCluster* という名前の AKS クラスターを作成するには、[az aks create][az-aks-create] コマンドを使用します。 このコマンドでは、必要なネットワーク リソースが存在しない場合、それらが作成されます。

* クラスターは 2 つのノードで構成されています。
* クラスター上に作成されるすべての Windows Server コンテナーの管理者資格情報は、`--windows-admin-password` パラメーターと `--windows-admin-username` パラメーターによって設定されます。前者は [Windows Server のパスワード要件][windows-server-password]を満たしている必要があります。 *windows-admin-password* パラメーターを指定しない場合は、値を指定するように求められます。
* ノード プールは `VirtualMachineScaleSets` を使用します。

> [!NOTE]
> クラスターが確実に動作するようにするには、既定のノード プールで少なくとも 2 つのノードを実行する必要があります。

クラスターの Windows Server コンテナーの管理者資格情報として使用するユーザー名を作成します。 次のコマンドを実行すると、ユーザー名の入力が求められ、後のコマンドで使用できるように WINDOWS_USERNAME と設定されます (この記事に示すコマンドは BASH シェルで入力されます)。

```azurecli-interactive
echo "Please enter the username to use as administrator credentials for Windows Server containers on your cluster: " && read WINDOWS_USERNAME
```

`--windows-admin-username` パラメーターを指定して、クラスターを作成します。 次のコマンド例では、前のコマンドで設定した *WINDOWS_USERNAME* の値を使用してクラスターを作成します。 または、*WINDOWS_USERNAME* を使用する代わりに、パラメーターに直接別のユーザー名を指定することもできます。 次のコマンドを実行すると、クラスターの Windows Server コンテナー向けに、管理者資格情報のパスワードを作成するように求めるメッセージも表示されます。 または、*windows-admin-password* パラメーターを使用して、独自の値を指定することもできます。

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 2 \
    --enable-addons monitoring \
    --generate-ssh-keys \
    --windows-admin-username $WINDOWS_USERNAME \
    --vm-set-type VirtualMachineScaleSets \
    --network-plugin azure
```

> [!NOTE]
> パスワード検証エラーが表示された場合は、設定したパスワードが [Windows Server のパスワード要件][windows-server-password]を満たしていることを確認します。 パスワードが要件を満たしている場合は、別のリージョンでリソース グループの作成を試みます。 その後、新しいリソース グループを使用してクラスターを作成してください。

数分後、コマンドが完了し、クラスターに関する情報が JSON 形式で返されます。 場合によっては、クラスターのプロビジョニングに数分以上かかることがあります。 このような場合は、最大 10 分と考えてください。

## <a name="add-a-windows-server-node-pool"></a>Windows Server ノード プールを追加する

既定では、Linux コンテナーを実行できるノード プールを使用して、AKS クラスターが作成されます。 Windows Server コンテナーを Linux ノード プールと共に実行できるノード プールをさらに追加するには、`az aks nodepool add` コマンドを使用します。

```azurecli
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --os-type Windows \
    --name npwin \
    --node-count 1
```

上記のコマンドでは、*npwin* という名前の新しいノード プールが作成され、それが *myAKSCluster* に追加されます。 Windows Server コンテナーを実行するノード プールを作成する場合、*node-vm-size* の既定値は *Standard_D2s_v3* となります。 *node-vm-size* パラメーターを設定することを選択した場合は、[制限された VM サイズ][restricted-vm-sizes]の一覧を確認してください。 推奨される最小サイズは、*Standard_D2s_v3* です。 上記のコマンドではまた、`az aks create` の実行時に作成された既定の VNET 内の既定のサブネットが使用されます。

## <a name="connect-to-the-cluster"></a>クラスターに接続する

Kubernetes クラスターを管理するには、Kubernetes のコマンドライン クライアントである [kubectl][kubectl] を使用します。 Azure Cloud Shell を使用している場合、`kubectl` は既にインストールされています。 `kubectl` をローカルにインストールするには、[az aks install-cli][az-aks-install-cli] コマンドを使用します。

```azurecli
az aks install-cli
```

Kubernetes クラスターに接続するように `kubectl` を構成するには、[az aks get-credentials][az-aks-get-credentials] コマンドを使用します。 このコマンドは、資格情報をダウンロードし、それを使用するように Kubernetes CLI を構成します。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

クラスターへの接続を確認するには、クラスター ノードの一覧を返す [kubectl get][kubectl-get] コマンドを使用します。

```console
kubectl get nodes
```

次の出力例は、クラスター内のすべてのノードを示しています。 すべてのノードの状態が *[準備完了]* であることを確認します。

```output
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.16.9
aksnpwin987654                      Ready    agent   108s   v1.16.9
```

## <a name="run-the-application"></a>アプリケーションの実行

Kubernetes のマニフェスト ファイルでは、どのコンテナー イメージを実行するかなど、クラスターの望ましい状態を定義します。 この記事では、Windows Server コンテナー内で ASP.NET サンプル アプリケーションを実行するために必要なすべてのオブジェクトを作成するのにマニフェストを使用します。 このマニフェストには、ASP.NET サンプル アプリケーションの [Kubernetes デプロイ][kubernetes-deployment]と、インターネットからアプリケーションにアクセスするための外部 [Kubernetes サービス][kubernetes-service]が含まれています。

ASP.NET サンプル アプリケーションは、[.NET Framework のサンプル][dotnet-samples]の一部として提供され、Windows Server コンテナー内で実行されます。 AKS では、Windows Server コンテナーは *Windows Server 2019* 以降のイメージをベースにしている必要があります。 Kubernetes マニフェスト ファイルではまた、[ノード セレクター][node-selector]を定義する必要があり、この定義では、Windows Server コンテナーを実行できるノード上でご利用の ASP.NET サンプル アプリケーションのポッドを実行するように AKS クラスターに指示します。

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

```console
kubectl apply -f sample.yaml
```

次の出力例は、正常に作成されたデプロイおよびサービスを示しています。

```output
deployment.apps/sample created
service/sample created
```

## <a name="test-the-application"></a>アプリケーションをテストする

アプリケーションが実行されると、Kubernetes サービスによってアプリケーション フロント エンドがインターネットに公開されます。 このプロセスが完了するまでに数分かかることがあります。 場合によっては、サービスのプロビジョニングに数分以上かかることがあります。 このような場合は、最大 10 分と考えてください。

進行状況を監視するには、[kubectl get service][kubectl-get] コマンドを `--watch` 引数と一緒に使用します。

```console
kubectl get service sample --watch
```

最初に、"*サンプル*" サービスの *EXTERNAL-IP* が "*保留中*" として表示されます。

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
sample             LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

*EXTERNAL-IP* アドレスが "*保留中*" から実際のパブリック IP アドレスに変わったら、`CTRL-C` を使用して `kubectl` ウォッチ プロセスを停止します。 次の出力例は、サービスに割り当てられている有効なパブリック IP アドレスを示しています。

```output
sample  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

サンプル アプリが動作していることを確認するには、Web ブラウザーを開いてサービスの外部 IP アドレスにアクセスします。

![ASP.NET サンプル アプリケーションを参照している画像](media/windows-container/asp-net-sample-app.png)

> [!Note]
> ページを読み込もうとしたときに接続タイムアウトが発生する場合は、次のコマンド [kubectl get pods --watch] を使用してサンプル アプリの準備ができていることを確認することをお勧めします。 外部 IP アドレスが使用可能になるまでは、Windows コンテナーが起動しない場合があります。

## <a name="delete-cluster"></a>クラスターを削除する

クラスターが必要なくなったら、[az group delete][az-group-delete] コマンドを使って、リソース グループ、コンテナー サービス、およびすべての関連リソースを削除してください。

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> クラスターを削除したとき、AKS クラスターで使用される Azure Active Directory サービス プリンシパルは削除されません。 サービス プリンシパルを削除する手順については、[AKS のサービス プリンシパルに関する考慮事項と削除][sp-delete]に関するページを参照してください。 マネージド ID を使用した場合、ID はプラットフォームによって管理されるので、削除する必要はありません。

## <a name="next-steps"></a>次のステップ

この記事では、Kubernetes クラスターをデプロイし、そこに、Windows Server コンテナー内の ASP.NET サンプル アプリケーションをデプロイしました。 先ほど作成したクラスターの [Kubernetes Web ダッシュボードにアクセス][kubernetes-dashboard]します。

AKS の詳細を参照し、デプロイの例の完全なコードを確認するには、Kubernetes クラスター チュートリアルに進んでください。

> [!div class="nextstepaction"]
> [AKS チュートリアル][aks-tutorial]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[node-selector]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[dotnet-samples]: https://hub.docker.com/_/microsoft-dotnet-framework-samples/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[aks-monitor]: ../azure-monitor/containers/container-insights-onboard.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-browse]: /cli/azure/aks#az-aks-browse
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks#az-aks-install-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[az-provider-register]: /cli/azure/provider#az-provider-register
[azure-cli-install]: /cli/azure/install-azure-cli
[azure-cni-about]: concepts-network.md#azure-cni-advanced-networking
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[azure-portal]: https://portal.azure.com
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[kubernetes-dashboard]: kubernetes-dashboard.md
[restricted-vm-sizes]: quotas-skus-regions.md#restricted-vm-sizes
[use-advanced-networking]: configure-azure-cni.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[windows-server-password]: /windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements#reference