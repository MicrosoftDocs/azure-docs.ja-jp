---
title: プレビュー - Azure Kubernetes Service (AKS) クラスター上に Windows Server コンテナーを作成する
description: 迅速に Kubernetes クラスターを作成し、Azure CLI を使用して Azure Kubernetes Service (AKS) で Windows Server コンテナーにアプリケーションをデプロイする方法について説明します。
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 06/17/2019
ms.author: mlearned
ms.openlocfilehash: 305901007180cfb197cf5c0dfb338800449560a1
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2019
ms.locfileid: "68382029"
---
# <a name="preview---create-a-windows-server-container-on-an-azure-kubernetes-service-aks-cluster-using-the-azure-cli"></a>プレビュー - Azure CLI を使用して Azure Kubernetes Service (AKS) クラスター上に Windows Server コンテナーを作成する

Azure Kubernetes Service (AKS) は、クラスターをすばやくデプロイおよび管理することができる、マネージド Kubernetes サービスです。 この記事では、Azure CLI を使用して AKS クラスターをデプロイします。 また、Windows Server コンテナー内の ASP.NET サンプル アプリケーションをクラスターにデプロイします。

現在、この機能はプレビュー段階にあります。

![ASP.NET サンプル アプリケーションを参照している画像](media/windows-container/asp-net-sample-app.png)

この記事では、Kubernetes の基本的な概念を理解していることを前提としています。 詳細については、「[Azure Kubernetes Services (AKS) における Kubernetes の中心概念][kubernetes-concepts]」を参照してください。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、この記事では、Azure CLI バージョン 2.0.61 以降を実行していることが要件となります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][azure-cli-install]に関するページを参照してください。

## <a name="before-you-begin"></a>開始する前に

Windows Server コンテナーを実行できるクラスターを作成したら、さらにノード プールを追加する必要があります。 ノード プールをさらに追加する手順については後述しますが、まず、いくつかのプレビュー機能を有効にする必要があります。

> [!IMPORTANT]
> AKS のプレビュー機能は、セルフサービス、オプトインです。 これらは、コミュニティからフィードバックやバグを収集するために提供されています。 これらの機能はプレビュー段階であり、運用環境での使用を意図していません。 パブリック プレビュー段階の機能は、"ベスト エフォート" のサポートに該当します。 AKS テクニカル サポート チームによるサポートは、太平洋タイム ゾーン (PST) での営業時間内のみで利用できます。 詳細については、次のサポートに関する記事を参照してください。
>
> * [AKS のサポート ポリシー][aks-support-policies]
> * [Azure サポートに関する FAQ][aks-faq]

### <a name="install-aks-preview-cli-extension"></a>aks-preview CLI 拡張機能をインストールする

Windows Server コンテナーを使用するには、*aks-preview* CLI 拡張機能のバージョン 0.4.1 以降が必要です。 [az extension add][az-extension-add] コマンドを使用して *aks-preview* Azure CLI 拡張機能をインストールしてから、az extension update コマンドを使用して使用可能な更新プログラムを確認します。

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-windows-preview-feature"></a>Windows のプレビュー機能を登録する

複数のノード プールを使用して Windows Server コンテナーを実行できる AKS クラスターを作成するには、まず、ご利用のサブスクリプションで *WindowsPreview* 機能フラグを有効にします。 *WindowsPreview*機能ではまた、複数ノード プール クラスターと仮想マシン スケール セットを使用して、Kubernetes ノードのデプロイおよび構成が管理されます。 次の例に示すように [az feature register][az-feature-register] コマンドを使用して、*WindowsPreview* 機能フラグを登録します。

```azurecli-interactive
az feature register --name WindowsPreview --namespace Microsoft.ContainerService
```

> [!NOTE]
> *WindowsPreview* 機能フラグが正常に登録された後で作成するすべての AKS クラスターでは、このプレビュー クラスター エクスペリエンスが使用されます。 完全にサポートされた通常のクラスターを引き続き作成するには、運用サブスクリプションでプレビュー機能を有効にしないでください。 プレビュー機能のテストには、別のテストまたは開発用 Azure サブスクリプションを使用します。

登録が完了するまでに数分かかります。 [az feature list][az-feature-list] コマンドを使用して、登録状態を確認します。

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/WindowsPreview')].{Name:name,State:properties.state}"
```

登録状態が `Registered` の場合、Ctrl + C キーを押して状態の監視を停止します。  次に、[az provider register][az-provider-register] コマンドを使用して、*Microsoft.ContainerService* リソース プロバイダーの登録を更新します。

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="limitations"></a>制限事項

複数のノード プールをサポートする AKS クラスターを作成および管理する際は、次の制限が適用されます。

* *WindowsPreview* が正常に登録された後に作成するクラスターには複数のノード プールを利用できます。 また、ご利用のサブスクリプションに対して *MultiAgentpoolPreview* 機能と *VMSSPreview* 機能を登録した場合も、複数ノード プールを使用できます。 これらの機能が正常に登録される前に作成された既存の AKS クラスターでは、ノード プールを追加することも管理することもできません。
* 最初のノード プールを削除することはできません。

この機能がプレビュー段階にある間は、次の追加の制限事項が適用されます。

* AKS クラスターは、最大で 8 つノード プールを持つことができます。
* AKS クラスターでは、この 8 つのノード プール全体で最大 400 のノードを保持することができます。
* Windows Server ノード プール名は 6 文字に制限されています。

## <a name="create-a-resource-group"></a>リソース グループの作成

Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理グループです。 リソース グループを作成する際は、場所を指定するよう求められます。 この場所は、リソース グループのメタデータが格納される場所です。リソースの作成時に別のリージョンを指定しない場合は、Azure でリソースが実行される場所でもあります。 [az group create][az-group-create] コマンドを使用して、リソース グループを作成します。

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

## <a name="create-an-aks-cluster"></a>AKS クラスターの作成

Windows Server コンテナー用のノード プールをサポートする AKS クラスターを実行するには、[Azure CNI][azure-cni-about] を使用するネットワーク ポリシーを、ご利用のクラスターで使用する必要があります (advanced) network plugin. For more detailed information to help plan out the required subnet ranges and network considerations, see [configure Azure CNI networking][use-advanced-networking]。 *myAKSCluster* という名前の AKS クラスターを作成するには、[az aks create][az-aks-create] コマンドを使用します。 このコマンドでは、必要なネットワーク リソースが存在しない場合、それらが作成されます。 クラスターは 1 つのノードを使用して構成されます。
  * クラスター上に作成された Windows Server コンテナーの管理者資格情報が、*windows-admin-password* パラメーターと *windows-admin-username* パラメーターによって設定されます。
  * 独自の安全な *PASSWORD_WIN* を指定します (この記事のコマンドは BASH シェルに入力するということに注意してください)。

パスワード検証エラーが発生した場合は、別のリージョンでリソース グループを作成してください。

```azurecli-interactive
PASSWORD_WIN="P@ssw0rd1234"

az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --enable-addons monitoring \
    --kubernetes-version 1.14.1 \
    --generate-ssh-keys \
    --windows-admin-password $PASSWORD_WIN \
    --windows-admin-username azureuser \
    --enable-vmss \
    --network-plugin azure
```

> [!Note]
> その後、新しいリソース グループを使用してクラスターを作成してください。
> 数分後、コマンドが完了し、クラスターに関する情報が JSON 形式で返されます。

Windows Server ノード プールを追加する

## <a name="add-a-windows-server-node-pool"></a>既定では、Linux コンテナーを実行できるノード プールを使用して、AKS クラスターが作成されます。

Windows Server コンテナーを実行できるノード プールをさらに追加するには、`az aks nodepool add` コマンドを使用します。 上記のコマンドでは、*npwin* という名前の新しいノード プールが作成され、それが *myAKSCluster* に追加されます。

```azurecli
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --os-type Windows \
    --name npwin \
    --node-count 1 \
    --kubernetes-version 1.14.1
```

Windows Server コンテナーを実行するノード プールを作成する場合、*node-vm-size* の既定値は *Standard_D2s_v3* となります。 *node-vm-size* パラメーターを設定することを選択した場合は、[制限された VM サイズ][restricted-vm-sizes]の一覧を確認してください。 推奨される最小サイズは、*Standard_D2s_v3* です。 上記のコマンドではまた、`az aks create` の実行時に作成された既定の VNET 内の既定のサブネットが使用されます。 クラスターへの接続

## <a name="connect-to-the-cluster"></a>Kubernetes クラスターを管理するには、Kubernetes のコマンドライン クライアントである [kubectl][kubectl] を使用します。

Azure Cloud Shell を使用している場合、`kubectl` は既にインストールされています。 `kubectl` をローカルにインストールするには、[az aks install-cli][az-aks-install-cli] コマンドを使用します。 Kubernetes クラスターに接続するように `kubectl` を構成するには、[az aks get-credentials][az-aks-get-credentials] コマンドを使用します。

```azurecli
az aks install-cli
```

このコマンドは、資格情報をダウンロードし、それを使用するように Kubernetes CLI を構成します。 クラスターへの接続を確認するには、[kubectl get][kubectl-get] コマンドを使用して、クラスター ノードの一覧を返します。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

次の出力例は、前の手順で作成した単一ノードを示しています。

```azurecli-interactive
kubectl get nodes
```

ノードの状態が "*準備完了*" であることを確認します。 アプリケーションの実行

```
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.14.1
aksnpwin987654                      Ready    agent   108s   v1.14.1
```

## <a name="run-the-application"></a>Kubernetes のマニフェスト ファイルでは、どのコンテナー イメージを実行するかなど、クラスターの望ましい状態を定義します。

この記事では、Windows Server コンテナー内で ASP.NET サンプル アプリケーションを実行するために必要なすべてのオブジェクトを作成するのにマニフェストを使用します。 このマニフェストには、ASP.NET サンプル アプリケーションの [Kubernetes デプロイ][kubernetes-deployment]と、インターネットからアプリケーションにアクセスするための外部 Kubernetes サービスが含まれています。 ASP.NET サンプル アプリケーションは、[.NET Framework のサンプル][dotnet-samples]の一部として提供され、Windows Server コンテナー内で実行されます。

AKS では、Windows Server コンテナーは *Windows Server 2019* 以降のイメージをベースにしている必要があります。 Kubernetes マニフェスト ファイルではまた、[ノード セレクター][node-selector]を定義する必要があり、この定義では、Windows Server コンテナーを実行できるノード上でご利用の ASP.NET サンプル アプリケーションのポッドを実行するように AKS クラスターに指示します。 `sample.yaml` という名前のファイルを作成し、以下の YAML 定義をコピーします。

Azure Cloud Shell を使用する場合は、仮想システムまたは物理システムで作業するときと同じように、`vi` または `nano` を使用してこのファイルを作成できます。 [kubectl apply][kubectl-apply] コマンドを使用してアプリケーションをデプロイし、YAML マニフェストの名前を指定します。

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

次の出力例は、正常に作成されたデプロイおよびサービスを示しています。

```azurecli-interactive
kubectl apply -f sample.yaml
```

アプリケーションをテストする

```
deployment.apps/sample created
service/sample created
```

## <a name="test-the-application"></a>アプリケーションが実行されると、Kubernetes サービスによってアプリケーション フロント エンドがインターネットに公開されます。

このプロセスが完了するまでに数分かかることがあります。 進行状況を監視するには、[kubectl get service][kubectl-get] コマンドを `--watch` 引数と一緒に使用します。

最初に、"*サンプル*" サービスの *EXTERNAL-IP* が "*保留中*" として表示されます。

```azurecli-interactive
kubectl get service sample --watch
```

*EXTERNAL-IP* アドレスが "*保留中*" から実際のパブリック IP アドレスに変わったら、`CTRL-C` を使用して `kubectl` ウォッチ プロセスを停止します。

```
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
sample             LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

次の出力例は、サービスに割り当てられている有効なパブリック IP アドレスを示しています。 サンプル アプリが動作していることを確認するには、Web ブラウザーを開いてサービスの外部 IP アドレスにアクセスします。

```
sample  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

ASP.NET サンプル アプリケーションを参照している画像

![クラスターを削除する](media/windows-container/asp-net-sample-app.png)

## <a name="delete-cluster"></a>クラスターが必要なくなったら、[az group delete][az-group-delete] コマンドを使って、リソース グループ、コンテナー サービス、およびすべての関連リソースを削除してください。

クラスターを削除したとき、AKS クラスターで使用される Azure Active Directory サービス プリンシパルは削除されません。

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> サービス プリンシパルを削除する手順については、[AKS のサービス プリンシパルに関する考慮事項と削除][sp-delete]に関するページを参照してください。 次の手順

## <a name="next-steps"></a>この記事では、Kubernetes クラスターをデプロイし、そこに、Windows Server コンテナー内の ASP.NET サンプル アプリケーションをデプロイしました。

先ほど作成したクラスターの [Kubernetes Web ダッシュボードにアクセス][kubernetes-dashboard]します。 AKS の詳細を参照し、デプロイの例の完全なコードを確認するには、Kubernetes クラスター チュートリアルに進んでください。

[AKS チュートリアル][aks-tutorial]

> [!div class="nextstepaction"]
> <bpt id="p1">[</bpt>AKS tutorial<ept id="p1">][aks-tutorial]</ept>

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[node-selector]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[dotnet-samples]: https://hub.docker.com/_/microsoft-dotnet-framework-samples/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[aks-monitor]: https://aka.ms/coingfonboarding
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-browse]: /cli/azure/aks?view=azure-cli-latest#az-aks-browse
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
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
[use-advanced-networking]: configure-advanced-networking.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
