---
title: Azure CLI を使用して仮想ノードを作成する
titleSuffix: Azure Kubernetes Service
description: Azure CLI を使用して、仮想ノードを使用する Azure Kubernetes Service (AKS) クラスターを作成してポッドを実行する方法を説明します。
services: container-service
ms.topic: conceptual
ms.date: 03/16/2021
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 1c673cae41fcbd3d54aa9b4062dd030ace9f0767
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104577803"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes-using-the-azure-cli"></a>Azure CLI を使って仮想ノードを使用する Azure Kubernetes Service (AKS) クラスターを作成して構成する

この記事では、Azure CLI を使用し、仮想ネットワーク リソースと AKS クラスターを作成して構成し、その後仮想ノードを有効にする方法を示します。


## <a name="before-you-begin"></a>開始する前に

仮想ノードを使用すると、Azure Container Instances (ACI) および AKS クラスターで実行されているポッド間でのネットワーク通信が可能になります。 この通信を可能にするために、仮想ネットワーク サブネットが作成され、委任されたアクセス許可が割り当てられます。 仮想ノードは、"*高度*" ネットワーク (Azure CNI) を使用して作成された AKS クラスターに対してのみ機能します。 既定では、AKS クラスターは "*基本*" ネットワーク (kubenet) を使用して作成されます。 この記事では、仮想ネットワークとサブネットを作成した後、高度ネットワークを使用した AKS クラスターをデプロイする方法について説明します。

> [!IMPORTANT]
> AKS で仮想ノードを使用する前に、[AKS 仮想ノードの制限事項][virtual-nodes-aks]と [ACI の仮想ネットワークの制限事項][virtual-nodes-networking-aci]の両方をご確認ください。 これらの制限は、AKS クラスターと仮想ノードの両方の場所、ネットワーク構成、およびその他の構成の詳細に影響します。

以前に ACI を使用していない場合は、ご使用のサブスクリプションでサービス プロバイダーを登録します。 ACI プロバイダー登録の状態は、次の例で示すように [az provider list][az-provider-list] コマンドを使用して確認できます。

```azurecli-interactive
az provider list --query "[?contains(namespace,'Microsoft.ContainerInstance')]" -o table
```

*Microsoft.ContainerInstance* プロバイダーは、次の出力の例で示すように *Registered* としてレポートします。

```output
Namespace                    RegistrationState    RegistrationPolicy
---------------------------  -------------------  --------------------
Microsoft.ContainerInstance  Registered           RegistrationRequired
```

プロバイダーが *NotRegistered* として示される場合は、次の例で示すように [az provider register][az-provider-register] を使用してプロバイダーを登録します。

```azurecli-interactive
az provider register --namespace Microsoft.ContainerInstance
```

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell を起動する

Azure Cloud Shell は無料のインタラクティブ シェルです。この記事の手順は、Azure Cloud Shell を使って実行することができます。 一般的な Azure ツールが事前にインストールされており、アカウントで使用できるように構成されています。

Cloud Shell を開くには、コード ブロックの右上隅にある **[使ってみる]** を選択します。 [https://shell.azure.com/bash](https://shell.azure.com/bash) に移動して、別のブラウザー タブで Cloud Shell を起動することもできます。 **[コピー]** を選択してコードのブロックをコピーし、Cloud Shell に貼り付けてから、Enter キーを押して実行します。

CLI をローカルにインストールして使用する場合、この記事では、Azure CLI バージョン 2.0.49 以降が必要です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="create-a-resource-group"></a>リソース グループを作成する

Azure リソース グループは、Azure リソースが展開され管理される論理グループです。 [az group create][az-group-create] コマンドを使用して、リソース グループを作成します。 次の例では、*myResourceGroup* という名前のリソース グループを場所 *westus* に作成します。

```azurecli-interactive
az group create --name myResourceGroup --location westus
```

## <a name="create-a-virtual-network"></a>仮想ネットワークの作成

[az network vnet create][az-network-vnet-create] コマンドを使用して、仮想ネットワークを作成します。 次の例では、 *10.0.0.0/8* というアドレス プレフィックスを持つ *myVnet* という仮想ネットワークと、*myAKSSubnet* というサブネットを作成します。 このサブネットのアドレス プレフィックスは、既定で *10.240.0.0/16* に設定されます。

```azurecli-interactive
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefixes 10.0.0.0/8 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 10.240.0.0/16
```

次に、[az network vnet subnet create][az-network-vnet-subnet-create] コマンドを使用して、仮想ノード用の追加サブネットを作成します。 次の例では、*10.241.0.0/16* というアドレス プレフィックスを持つ *myVirtualNodeSubnet* という名前のサブネットを作成します。

```azurecli-interactive
az network vnet subnet create \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name myVirtualNodeSubnet \
    --address-prefixes 10.241.0.0/16
```

## <a name="create-a-service-principal-or-use-a-managed-identity"></a>サービス プリンシパルの作成またはマネージド ID の使用

AKS クラスターが他の Azure リソースとやりとりできるようにするために、クラスター ID が使用されます。 このクラスター ID は、Azure CLI または Azure portal で自動的に作成するか、または事前に作成しておいて、アクセス許可を追加で割り当てることができます。 既定では、このクラスター ID はマネージド ID です。 詳細については、[マネージド ID の使用](use-managed-identity.md)に関するページを参照してください。 また、サービス プリンシパルをクラスター ID として使用することもできます。 次の手順は、サービス プリンシパルを手動で作成してクラスターに割り当てる方法を示しています。

[az ad sp create-for-rbac][az-ad-sp-create-for-rbac] コマンドを使用して、サービス プリンシパルを作成します。 `--skip-assignment` パラメーターで、余分なアクセス許可の割り当てを制限します。

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

出力は次の例のようになります。

```output
{
  "appId": "bef76eb3-d743-4a97-9534-03e9388811fc",
  "displayName": "azure-cli-2018-11-21-18-42-00",
  "name": "http://azure-cli-2018-11-21-18-42-00",
  "password": "1d257915-8714-4ce7-a7fb-0e5a5411df7f",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

*appId* と *password* を書き留めておいてください。 これらの値は、以降の手順で使用します。

## <a name="assign-permissions-to-the-virtual-network"></a>仮想ネットワークにアクセス許可を割り当てる

クラスターに仮想ネットワークの使用と管理を許可するには、AKS のサービス プリンシパルに、ネットワーク リソースを使用するための適切な権限を付与する必要があります。

まず、[az network vnet show][az-network-vnet-show] を使用して、仮想ネットワークのリソース ID を取得します。

```azurecli-interactive
az network vnet show --resource-group myResourceGroup --name myVnet --query id -o tsv
```

仮想ネットワークを使用するための適切なアクセス権を AKS クラスターに与えるには、[az role assignment create][az-role-assignment-create] コマンドを使用してロールの割り当てを作成します。 `<appId`> と `<vnetId>` は、前の 2 つの手順で書き留めた値に置き換えてください。

```azurecli-interactive
az role assignment create --assignee <appId> --scope <vnetId> --role Contributor
```

## <a name="create-an-aks-cluster"></a>AKS クラスターを作成する

前の手順で作成した AKS サブネット内に、AKS クラスターをデプロイします。 [az network vnet subnet show][az-network-vnet-subnet-show] を使用して、このサブネットの ID を取得します。

```azurecli-interactive
az network vnet subnet show --resource-group myResourceGroup --vnet-name myVnet --name myAKSSubnet --query id -o tsv
```

AKS クラスターを作成するには、[az aks create][az-aks-create] コマンドを使用します。 次の例では、*myAKSCluster* という名前のクラスターを 1 つのノードで作成します。 `<subnetId>` を前の手順で取得した ID に置き換え、`<appId>` と `<password>` を前のセクションで収集した値に置き換えます。

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --network-plugin azure \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id <subnetId> \
    --service-principal <appId> \
    --client-secret <password>
```

数分後、コマンドが完了すると、クラスターに関する JSON 形式の情報が返されます。

## <a name="enable-virtual-nodes-addon"></a>仮想ノード アドオンを有効にする

次に、仮想ノードを有効にするために、[az aks enable-addons][az-aks-enable-addons] コマンドを使用します。 次の例では、前の手順で作成した *myVirtualNodeSubnet* というサブネットを使用しています。

```azurecli-interactive
az aks enable-addons \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --addons virtual-node \
    --subnet-name myVirtualNodeSubnet
```

## <a name="connect-to-the-cluster"></a>クラスターに接続する

Kubernetes クラスターに接続するように `kubectl` を構成するには、[az aks get-credentials][az-aks-get-credentials] コマンドを使用します。 この手順では、資格情報をダウンロードし、Kubernetes CLI を構成してそれらの資格情報を使用します。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

クラスターへの接続を確認するには、クラスター ノードの一覧を返す [kubectl get][kubectl-get] コマンドを使用します。

```console
kubectl get nodes
```

次の出力例は、単一の VM ノードが作成されてから、Linux 用の仮想ノード、*virtual-node-aci-linux* が作成されることを示しています。

```output
NAME                          STATUS    ROLES     AGE       VERSION
virtual-node-aci-linux        Ready     agent     28m       v1.11.2
aks-agentpool-14693408-0      Ready     agent     32m       v1.11.2
```

## <a name="deploy-a-sample-app"></a>サンプル アプリのデプロイ

`virtual-node.yaml` という名前のファイルを作成し、そこに以下の YAML をコピーします。 ノード上のコンテナーをスケジュールするため、[nodeSelector][node-selector] と [toleration][toleration] が定義されます。

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aci-helloworld
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aci-helloworld
  template:
    metadata:
      labels:
        app: aci-helloworld
    spec:
      containers:
      - name: aci-helloworld
        image: mcr.microsoft.com/azuredocs/aci-helloworld
        ports:
        - containerPort: 80
      nodeSelector:
        kubernetes.io/role: agent
        beta.kubernetes.io/os: linux
        type: virtual-kubelet
      tolerations:
      - key: virtual-kubelet.io/provider
        operator: Exists
      - key: azure.com/aci
        effect: NoSchedule
```

[kubectl apply][kubectl-apply] コマンドを使用してアプリケーションを実行します。

```console
kubectl apply -f virtual-node.yaml
```

`-o wide` 引数を指定して [kubectl get pods][kubectl-get] コマンドを使用し、ポッドの一覧とスケジュールされたノードを出力します。 `aci-helloworld` ポッドは `virtual-node-aci-linux` ノードでスケジュールされています。

```console
kubectl get pods -o wide
```

```output
NAME                            READY     STATUS    RESTARTS   AGE       IP           NODE
aci-helloworld-9b55975f-bnmfl   1/1       Running   0          4m        10.241.0.4   virtual-node-aci-linux
```

ポッドには、仮想ノードで使用するために委任された Azure 仮想ネットワーク サブネットからの内部 IP アドレスが割り当てられます。

> [!NOTE]
> Azure Container Registry に格納されているイメージを使用する場合、[Kubernetes シークレットを構成して使用します][acr-aks-secrets]。 仮想ノードの現在の制限は、Azure AD サービス プリンシパル統合認証を使用できないことです。 シークレットを使用しない場合、仮想ノードでスケジュールされたポッドの開始に失敗し、エラー `HTTP response status code 400 error code "InaccessibleImage"` が報告されます。

## <a name="test-the-virtual-node-pod"></a>仮想ノード ポッドのテスト

仮想ノードで実行されているポッドをテストするには、Web クライアントでデモ アプリケーションを参照します。 ポッドには内部 IP アドレスが割り当てられているため、AKS クラスターの別のポッドからこの接続をすばやくテストできます。 テスト ポッドを作成し、それにターミナル セッションをアタッチします。

```console
kubectl run -it --rm testvk --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
```

`apt-get` を使用して、`curl` をポッドにインストールします。

```console
apt-get update && apt-get install -y curl
```

次に、`curl` ( *http://10.241.0.4* など) を使用して、ポッドのアドレスにアクセスします。 前の `kubectl get pods` コマンドで示された独自の内部 IP アドレスを入力します。

```console
curl -L http://10.241.0.4
```

次の簡約された出力例に示されているように、デモ アプリケーションが表示されます。

```output
<html>
<head>
  <title>Welcome to Azure Container Instances!</title>
</head>
[...]
```

`exit` を使用してテスト ポッドへのターミナル セッションを閉じます。 セッションが終了すると、ポッドが削除されます。

## <a name="remove-virtual-nodes"></a>仮想ノードを削除する

仮想ノードを使用する必要がなくなった場合は、[az aks disable-addons][az aks disable-addons] コマンドを使用して、それらを無効にすることができます。 

必要に応じて、[https://shell.azure.com](https://shell.azure.com) にアクセスし、お使いのブラウザーで Azure Cloud Shell を開きます。

最初に、仮想ノードで実行されている `aci-helloworld` ポッドを削除します。

```console
kubectl delete -f virtual-node.yaml
```

次のコマンド例では、Linux の仮想ノードを無効にしています。

```azurecli-interactive
az aks disable-addons --resource-group myResourceGroup --name myAKSCluster --addons virtual-node
```

次に、仮想ネットワークのリソースとリソース グループを削除します。

```azurecli-interactive
# Change the name of your resource group, cluster and network resources as needed
RES_GROUP=myResourceGroup
AKS_CLUSTER=myAKScluster
AKS_VNET=myVnet
AKS_SUBNET=myVirtualNodeSubnet

# Get AKS node resource group
NODE_RES_GROUP=$(az aks show --resource-group $RES_GROUP --name $AKS_CLUSTER --query nodeResourceGroup --output tsv)

# Get network profile ID
NETWORK_PROFILE_ID=$(az network profile list --resource-group $NODE_RES_GROUP --query [0].id --output tsv)

# Delete the network profile
az network profile delete --id $NETWORK_PROFILE_ID -y

# Delete the subnet delegation to Azure Container Instances
az network vnet subnet update --resource-group $RES_GROUP --vnet-name $AKS_VNET --name $AKS_SUBNET --remove delegations 0
```

## <a name="next-steps"></a>次のステップ

この記事では、ポッドを仮想ノードでスケジュールし、プライベートの内部 IP アドレスを割り当てました。 代わりに、サービスのデプロイを作成して、ロード バランサーまたはイングレス コントローラーを使用してトラフィックを自分のポッドにルーティングすることもできます。 詳細は、[AKS での基本的なイングレス コント ローラーの作成][aks-basic-ingress]を参照してください。

多くの場合、仮想ノードは AKS のスケーリング ソリューションの 1 つのコンポーネントです。 スケーリング ソリューションの詳細については、次の記事を参照してください。

- [Kubernetes のポッドの水平オートスケーラーを使用する][aks-hpa]
- [Kubernetes クラスター オートスケーラーを使用する][aks-cluster-autoscaler]
- [仮想ノード用の自動スケーリング サンプルをチェックアウトする][virtual-node-autoscale]
- [Virtual Kubelet のオープン ソース ライブラリの詳細を確認する][virtual-kubelet-repo]

<!-- LINKS - external -->
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[aks-github]: https://github.com/azure/aks/issues
[virtual-node-autoscale]: https://github.com/Azure-Samples/virtual-node-autoscale
[virtual-kubelet-repo]: https://github.com/virtual-kubelet/virtual-kubelet
[acr-aks-secrets]: https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/

<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-create]: /cli/azure/group#az-group-create
[az-network-vnet-create]: /cli/azure/network/vnet#az-network-vnet-create
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-network-vnet-show]: /cli/azure/network/vnet#az-network-vnet-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-show
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-enable-addons]: /cli/azure/aks#az-aks-enable-addons
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az aks disable-addons]: /cli/azure/aks#az-aks-disable-addons
[aks-hpa]: tutorial-kubernetes-scale.md
[aks-cluster-autoscaler]: ./cluster-autoscaler.md
[aks-basic-ingress]: ingress-basic.md
[az-provider-list]: /cli/azure/provider#az-provider-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[virtual-nodes-aks]: virtual-nodes.md
[virtual-nodes-networking-aci]: ../container-instances/container-instances-virtual-network-concepts.md