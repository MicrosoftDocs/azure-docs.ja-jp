---
title: プレビュー - Azure Kubernetes Service (AKS) で Standard SKU ロード バランサーを使用する
description: Standard SKU でロード バランサーを使用して、Azure Kubernetes Service (AKS) でサービスを公開する方法について説明します。
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 06/25/2019
ms.author: zarhoads
ms.openlocfilehash: a9cf3db3a15fab5a2f067a146950e02923a20379
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476802"
---
# <a name="preview---use-a-standard-sku-load-balancer-in-azure-kubernetes-service-aks"></a>プレビュー - Azure Kubernetes Service (AKS) で Standard SKU ロード バランサーを使用する

Azure Kubernetes Service (AKS) でアプリケーションへのアクセスを提供するために、Azure Load Balancer を作成して使用できます。 AKS で実行されるロード バランサーは、内部または外部ロード バランサーとして使用できます。 内部ロード バランサーは、AKS クラスターと同じ仮想ネットワークで実行されているアプリケーションに対してのみ Kubernetes サービスへのアクセスを可能にします。 外部ロード バランサーは、1 つ以上のイングレス用のパブリック IP を受け取り、そのパブリック IP を使用して外部から Kubernetes サービスにアクセスできるようにします。

Azure Load Balancer は、*Basic* と *Standard* の 2 つの SKU で使用できます。 サービス マニフェストを使用して AKS でロード バランサーを作成する場合、既定では *Basic* SKU が使用されます。 *Standard* SKU ロード バランサーを使用すると、より大きいバックエンド プール サイズ、Availability Zones など、追加のフィーチャーや機能が提供されます。 使用するロード バランサーを選択する前に、*Standard* と *Basic* ロード バランサーの違いを理解しておくことが重要です。 AKS クラスターを作成した後は、そのクラスターのロード バランサー SKU は変更できません。 *Basic* と *Standard* SKU の詳細については、「[Azure Load Balancer の SKU の比較][azure-lb-comparison]」をご覧ください。

この記事では、Azure Kubernetes Service (AKS) で *Standard* SKU を使用する Azure Load Balancer を作成し、使用する方法について説明します。

この記事は、Kubernetes および Azure Load Balancer の基本的な概念を理解していることを前提としています。 詳細については、「[Azure Kubernetes Services (AKS) における Kubernetes の中心概念][kubernetes-concepts]」and [What is Azure Load Balancer?][azure-lb]をご覧ください。

現在、この機能はプレビュー段階にあります。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、この記事では、Azure CLI バージョン 2.0.59 以降を実行していることが要件となります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][install-azure-cli]に関するページを参照してください。

## <a name="before-you-begin"></a>開始する前に

既存のサブネットまたはリソース グループを使用する場合、AKS クラスターのサービス プリンシパルにはネットワーク リソースを管理するアクセス許可が必要です。 一般に、委任されたリソースのサービス プリンシパルには*ネットワーク共同作成者*ロールを割り当てます。 アクセス許可の詳細については、[他の Azure リソースへの AKS アクセスの委任][aks-sp]に関する記事を参照してください。

ロード バランサーの SKU を既定の *Basic* ではなく *Standard* に設定する AKS クラスターを作成する必要があります。 AKS クラスターの作成については後の手順で説明します。最初に、プレビュー機能をいくつか有効にする必要があります。

> [!IMPORTANT]
> AKS のプレビュー機能は、セルフサービスのオプトインです。 これらは、コミュニティからフィードバックやバグを収集するために提供されています。 これらの機能はプレビュー段階であり、運用環境での使用を意図していません。 パブリック プレビュー段階の機能は、"ベスト エフォート" のサポートに該当します。 AKS テクニカル サポート チームによるサポートは、太平洋タイム ゾーン (PST) での営業時間内のみで利用できます。 詳細については、次のサポートに関する記事を参照してください。
>
> * [AKS のサポート ポリシー][aks-support-policies]
> * [Azure サポートに関する FAQ][aks-faq]

### <a name="install-aks-preview-cli-extension"></a>aks-preview CLI 拡張機能をインストールする

Azure Load Balancer Standard SKU を使用するには、*aks-preview* CLI 拡張機能のバージョン 0.4.1 以降が必要です。 [az extension add][az-extension-add] コマンドを使用して *aks-preview* Azure CLI 拡張機能をインストールしてから、az extension update コマンドを使用して使用可能な更新プログラムを確認します。

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-aksazurestandardloadbalancer-preview-feature"></a>AKSAzureStandardLoadBalancer プレビュー機能を登録する

*Standard* SKU を使用するロード バランサーを使用できる AKS クラスターを作成するには、サブスクリプションで *AKSAzureStandardLoadBalancer* 機能フラグを有効にする必要があります。 *AKSAzureStandardLoadBalancer* 機能は、仮想マシン スケール セットを使用してクラスターを作成するときに *VMSSPreview* も使用します。 この機能は、クラスターを構成するときに、最新のサービス拡張機能のセットを提供します。 必須ではありませんが、*VMSSPreview* 機能フラグも有効にすることをお勧めします。

> [!CAUTION]
> サブスクリプションで機能を登録する場合、現時点ではその機能を登録解除することはできません。 一部のプレビュー機能を有効にすると、すべての AKS クラスターに対して既定値が使用され、サブスクリプション内に作成される場合があります。 運用サブスクリプションではプレビュー機能を有効にしないでください。 プレビュー機能をテストし、フィードバックを集めるには、別のサブスクリプションを使用してください。

次の例に示されているように、[az feature register][az-feature-register] コマンドを使用して *VMSSPreview* および *AKSAzureStandardLoadBalancer* 機能フラグを登録します。

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "VMSSPreview"
az feature register --namespace "Microsoft.ContainerService" --name "AKSAzureStandardLoadBalancer"
```

> [!NOTE]
> *VMSSPreview* または *AKSAzureStandardLoadBalancer* 機能フラグを正常に登録した後に作成されたすべての AKS クラスターでは、このプレビュー クラスター エクスペリエンスが使用されます。 完全にサポートされた通常のクラスターを引き続き作成するには、運用サブスクリプションでプレビュー機能を有効にしないでください。 プレビュー機能のテストには、別のテストまたは開発用 Azure サブスクリプションを使用します。

状態が *[登録済み]* と表示されるまでに数分かかります。 登録状態を確認するには、[az feature list][az-feature-list] コマンドを使用します。

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/VMSSPreview')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSAzureStandardLoadBalancer')].{Name:name,State:properties.state}"
```

準備ができたら、[az provider register][az-provider-register] コマンドを使用して、*Microsoft.ContainerService* リソース プロバイダーの登録を更新します。

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="limitations"></a>制限事項

*Standard* SKU を使用するロード バランサーをサポートする AKS クラスターを作成し、管理する場合、次の制限が適用されます。

* ロード バランサーに対して *Standard* SKU を使用する場合、パブリック アドレスを許可する必要があり、IP 作成を禁止する Azure Policy は作成しないようにする必要があります。 AKS クラスターは、AKS クラスター用に作成されたのと同じリソース グループ (通常、名前の先頭が *MC_* ) に *Standard* SKU パブリック IP を自動的に作成します。 AKS により、パブリック IP が *Standard* SKU ロード バランサーに割り当てられます。 AKS クラスターからのエグレス トラフィックを許可するために、このパブリック IP が必要です。 また、このパブリック IP は、コントロール プレーンとエージェント ノードの間の接続を維持するため、および前のバージョンの AKS との互換性を維持するためにも必要です。
* ロード バランサーに対して *Standard* SKU を使用する場合、Kubernetes バージョン 1.13.5 以降を使用する必要があります。

この機能がプレビュー段階にある間は、追加で次の制限もあります。

* AKS でロード バランサーに対して *Standard* SKU を使用する場合、ロード バランサーのエグレスに独自のパブリック IP アドレスを設定することはできません。 AKS がロード バランサーに割り当てる IP アドレスを使用する必要があります。

## <a name="create-a-resource-group"></a>リソース グループの作成

Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理グループです。 リソース グループを作成する際は、場所を指定するよう求められます。 この場所は、リソース グループのメタデータが格納される場所です。リソースの作成時に別のリージョンを指定しない場合は、Azure でリソースが実行される場所でもあります。 [az group create][az-group-create] コマンドを使用して、リソース グループを作成します。

次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。

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

## <a name="create-aks-cluster"></a>AKS クラスターの作成
*Standard* SKU を使用するロード バランサーをサポートする AKS クラスターを実行するには、クラスターで *load-balancer-sku* パラメーターを *standard* に設定する必要があります。 このパラメーターにより、クラスターが作成されるときに、*Standard* SKU を使用するロード バランサーが作成されます。 クラスターで *LoadBalancer* サービスを実行すると、*Standard* SK ロード バランサーの構成がサービスの構成で更新されます。 *myAKSCluster* という名前の AKS クラスターを作成するには、[az aks create][az-aks-create] コマンドを使用します。

> [!NOTE]
> *load-balancer-sku* プロパティは、クラスターの作成時にのみ使用できます。 AKS クラスターが作成された後にロード バランサー SKU を変更することはできません。 また、1 つのクラスターで使用できるロード バランサー SKU の種類は 1 つのみです。

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-vmss \
    --node-count 1 \
    --kubernetes-version 1.14.0 \
    --load-balancer-sku standard \
    --generate-ssh-keys
```

数分後、コマンドが完了し、クラスターに関する情報が JSON 形式で返されます。

## <a name="connect-to-the-cluster"></a>クラスターへの接続

Kubernetes クラスターを管理するには、Kubernetes のコマンドライン クライアントである [kubectl][kubectl] を使用します。 Azure Cloud Shell を使用している場合、`kubectl` は既にインストールされています。 `kubectl` をローカルにインストールするには、[az aks install-cli][az-aks-install-cli] コマンドを使用します。

```azurecli
az aks install-cli
```

Kubernetes クラスターに接続するように `kubectl` を構成するには、[az aks get-credentials][az-aks-get-credentials] コマンドを使用します。 このコマンドは、資格情報をダウンロードし、それを使用するように Kubernetes CLI を構成します。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

クラスターへの接続を確認するには、[kubectl get][kubectl-get] コマンドを使用して、クラスター ノードの一覧を返します。

```azurecli-interactive
kubectl get nodes
```

次の出力例は、前の手順で作成した単一ノードを示しています。 ノードの状態が "*準備完了*" であることを確認します。

```
NAME                       STATUS   ROLES   AGE     VERSION
aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.14.0
```

## <a name="verify-your-cluster-uses-the-standard-sku"></a>クラスターで *Standard* SKU が使用されているか確認する

[az aks show][az-aks-show] を使用して、クラスターの構成を表示します。

```console
$ az aks show --resource-group myResourceGroup --name myAKSCluster

{
  "aadProfile": null,
  "addonProfiles": null,
   ...
   "networkProfile": {
    "dnsServiceIp": "10.0.0.10",
    "dockerBridgeCidr": "172.17.0.1/16",
    "loadBalancerSku": "standard",
    ...
```

*loadBalancerSku* プロパティが *standard* と表示されていることを確認します。

## <a name="use-the-load-balancer"></a>ロード バランサーを使用する

クラスターでロード バランサーを使用するには、サービス タイプ *LoadBalancer* を使用してサービス マニフェストを作成します。 ロード バランサーが動作していることを表示するには、クラスターで実行するサンプル アプリケーションで別のマニフェストを作成します。 このサンプル アプリケーションは、ロード バランサーを介して公開され、ブラウザーで表示できます。

次の例に示されているように、`sample.yaml` という名前のマニフェストを作成します。

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
```

上記のマニフェストにより、*azure-vote-front* および *azure-vote-back* という 2 つのデプロイが構成されます。 ロード バランサーを使用して公開されるように *azure-vote-front* デプロイを構成するために、次の例に示されているように、`standard-lb.yaml` という名前のマニフェストを作成します。

```yaml
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

サービス *azure-vote-front* は、*LoadBalancer* タイプを使用して、*azure-vote-front* デプロイに接続するように AKS クラスターのロード バランサーを構成します。

[kubectl apply][kubectl-apply] を使用してサンプル アプリケーションとロード バランサーをデプロイし、YAML マニフェストの名前を指定します。

```console
kubectl apply -f sample.yaml
kubectl apply -f standard-lb.yaml
```

これで、サンプル アプリケーションを公開するように *Standard* SKU ロード バランサーが構成されました。 [kubectl get][kubectl-get] を使用して *azure-vote-front* のサービス詳細を表示し、ロード バランサーのパブリック IP を確認します。 ロード バランサーのパブリック IP アドレスは、*EXTERNAL-IP* 列に表示されます。 次の例に示されているように、IP アドレスが *\<[保留中]\>* から実際の外部 IP アドレスに変わるには 1、2 分かかることがあります。

```
$ kubectl get service azure-vote-front

NAME                TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)        AGE
azure-vote-front    LoadBalancer   10.0.227.198   52.179.23.131   80:31201/TCP   16s
```

ブラウザーでパブリック IP に移動し、サンプル アプリケーションが表示されることを確認します。 上記の例では、パブリック IP は `52.179.23.131` です。

![Azure Vote にブラウザーでアクセスしたところ](media/container-service-kubernetes-walkthrough/azure-vote.png)

> [!NOTE]
> また、ロード バランサーを内部にして、パブリック IP を公開しないように構成することもできます。 ロード バランサーを内部として構成するには、*LoadBalancer* サービスにアノテーションとして `service.beta.kubernetes.io/azure-load-balancer-internal: "true"` を追加します。 サンプルの yaml マニフェストと内部ロード バランサーの詳細について、[こちら][internal-lb-yaml]で確認できます。

## <a name="clean-up-the-standard-sku-load-balancer-configuration"></a>Standard SKU ロード バランサーの構成をクリーンアップする

サンプル アプリケーションとロード バランサーの構成を削除するには、[kubectl delete][kubectl-delete] を使用します。

```console
kubectl delete -f sample.yaml
kubectl delete -f standard-lb.yaml
```

## <a name="next-steps"></a>次の手順

[Kubernetes サービスのドキュメント][kubernetes-services]で Kubernetes サービスについて学習する。

<!-- LINKS - External -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
[aks-engine]: https://github.com/Azure/aks-engine

<!-- LINKS - Internal -->
[advanced-networking]: configure-azure-cni.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-sp]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-create]: /cli/azure/group#az-group-create
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[azure-lb]: ../load-balancer/load-balancer-overview.md
[azure-lb-comparison]: ../load-balancer/load-balancer-overview.md#skus
[install-azure-cli]: /cli/azure/install-azure-cli
[internal-lb-yaml]: internal-lb.md#create-an-internal-load-balancer
[kubernetes-concepts]: concepts-clusters-workloads.md
[use-kubenet]: configure-kubenet.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
