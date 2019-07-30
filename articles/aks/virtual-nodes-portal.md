---
title: Azure Kubernetes Service (AKS) でポータルを使用して仮想ノードを作成する
description: Azure portal を使用して、仮想ノードを使用する Azure Kubernetes Service (AKS) クラスターを作成してポッドを実行する方法を説明します。
services: container-service
author: mlearned
ms.topic: conceptual
ms.service: container-service
ms.date: 05/06/2019
ms.author: mlearned
ms.openlocfilehash: 8752d888e24e7135d488be6d1b377070a30fe4eb
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67613842"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes-in-the-azure-portal"></a>Azure portal で仮想ノードを使用する Azure Kubernetes Service (AKS) クラスターを作成して構成する

Azure Kubernetes Service (AKS) クラスターでワークロードをすばやくデプロイするには、仮想ノードを使用します。 仮想ノードを使用すると、ポッドを短時間でプロビジョニングできるため、ポッドの実行時間に対して秒単位の支払いだけで済みます。 スケーリング シナリオでは、Kubernetes クラスターのオートスケーラーが VM コンピューティング ノードをデプロイして追加のポッドを実行するのを待つ必要はありません。 仮想ノードは、Linux のポッドとノードでのみサポートされます。

この記事では、仮想ノードを有効にして、仮想ネットワーク リソースと AKS クラスターを作成して構成する方法を示します。

## <a name="before-you-begin"></a>開始する前に

仮想ノードを使用すると、ACI および AKS クラスターで実行されているポッド間でのネットワーク通信が可能になります。 この通信を可能にするために、仮想ネットワーク サブネットが作成され、委任されたアクセス許可が割り当てられます。 仮想ノードは、"*高度*" ネットワークを使用して作成された AKS クラスターに対してのみ機能します。 既定では、AKS クラスターは "*基本*" ネットワークを使用して作成されます。 この記事では、仮想ネットワークとサブネットを作成した後、高度ネットワークを使用した AKS クラスターをデプロイする方法について説明します。

以前に ACI を使用していない場合は、ご使用のサブスクリプションでサービス プロバイダーを登録します。 ACI プロバイダーの状態は、次の例で示すように [az provider list][az-provider-list] コマンドを使用して確認できます。

```azurecli-interactive
az provider list --query "[?contains(namespace,'Microsoft.ContainerInstance')]" -o table
```

*Microsoft.ContainerInstance* プロバイダーは、次の出力の例で示すように *Registered* としてレポートします。

```
Namespace                    RegistrationState
---------------------------  -------------------
Microsoft.ContainerInstance  Registered
```

プロバイダーが *NotRegistered* として示される場合は、次の例で示すように [az provider register][az-provider-register] を使用してプロバイダーを登録します。

```azurecli-interactive
az provider register --namespace Microsoft.ContainerInstance
```

## <a name="regional-availability"></a>リージョン別の提供状況

仮想ノードのデプロイでは、次のリージョンがサポートされています。

* オーストラリア東部 (australiaeast)
* 米国中部 (centralus)
* 米国東部 (eastus)
* 米国東部 2 (eastus2)
* 東日本 (japaneast)
* 北ヨーロッパ (northeurope)
* 東南アジア (southeastasia)
* 米国中西部 (westcentralus)
* 西ヨーロッパ (westeurope)
* 米国西部 (westus)
* 米国西部 2 (westus2)

## <a name="known-limitations"></a>既知の制限事項
仮想ノードの機能は、ACI の機能セットに大きく依存します。 次のシナリオは、仮想ノードではまだサポートされていません

* サービス プリンシパルを使用した ACR イメージのプル。 [対処法](https://github.com/virtual-kubelet/virtual-kubelet/blob/master/providers/azure/README.md#Private-registry)は、[Kubernetes シークレット](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/#create-a-secret-by-providing-credentials-on-the-command-line)を使用することです
* [仮想ネットワークの制限事項](../container-instances/container-instances-vnet.md) (VNet ピアリング、Kubernetes ネットワーク ポリシー、およびネットワーク セキュリティ グループを使用したインターネットへの送信トラフィックなど)。
* Init コンテナー
* [ホストのエイリアス](https://kubernetes.io/docs/concepts/services-networking/add-entries-to-pod-etc-hosts-with-host-aliases/)
* ACI の exec の[引数](../container-instances/container-instances-exec.md#restrictions)
* [Daemonsets](concepts-clusters-workloads.md#statefulsets-and-daemonsets) は仮想ノードへポッドをデプロイしない
* [Windows Server ノード (現在は AKS でプレビュー段階)](windows-container-cli.md) を仮想ノードと併用することはサポートされていません。 仮想ノードを使用して、Windows Server コンテナーをスケジュールすることができ、AKS クラスター内の Windows Server ノードは必要ありません。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal ( https://portal.azure.com ) にサインインします。

## <a name="create-an-aks-cluster"></a>AKS クラスターの作成

Azure portal の左上隅で、 **[リソースの作成]**  >  **[Kubernetes Service]** を選択します。

**[基本]** ページで、次のオプションを構成します。

- *プロジェクトの詳細*:サブスクリプションを選択し、Azure リソース グループ (たとえば、*myResourceGroup*) を選択または作成します。 **Kubernetes クラスター名** (たとえば、*myAKSCluster*) を入力します。
- *クラスターの詳細*:AKS クラスターのリージョン、Kubernetes バージョン、および DNS 名プレフィックスを選択します。
- *プライマリ ノード プール*:AKS ノードの VM サイズを選択します。 AKS クラスターがデプロイされた後に、VM サイズを変更することは**できません**。
     - クラスターにデプロイするノードの数を選択します。 この記事では、 **[ノード数]** を *1* に設定します。 ノード数は、クラスターをデプロイした後に調整**できます**。

**[次へ: **スケール]** をクリックします。

**[スケール]** ページの **[仮想ノード]** で *[有効]* を選択します。

![AKS クラスターを作成し、仮想ノードを有効にする](media/virtual-nodes-portal/enable-virtual-nodes.png)

既定では、Azure Active Directory サービス プリンシパルが作成されます。 このサービス プリンシパルは、クラスター通信と他の Azure サービスとの統合に使用されます。

このクラスターは高度なネットワークに対しても構成されます。 仮想ノードは、独自の Azure 仮想ネットワーク サブネットを使用するように構成されます。 このサブネットには、Azure リソースと AKS クラスター間を接続するための委任されたアクセス許可があります。 委任されたサブネットがまだない場合、Azure portal によって仮想ノードで使用するための Azure 仮想ネットワークとサブネットが作成および構成されます。

**[Review + create]\(レビュー + 作成\)** を選択します。 検証が完了したら、 **[作成]** を選択します。

AKS クラスターを作成して、使用準備が完了するのには数分かかります。

## <a name="connect-to-the-cluster"></a>クラスターへの接続

Azure Cloud Shell は無料のインタラクティブ シェルです。この記事の手順は、Azure Cloud Shell を使って実行することができます。 一般的な Azure ツールが事前にインストールされており、アカウントで使用できるように構成されています。 Kubernetes クラスターを管理するには、Kubernetes のコマンドライン クライアントである [kubectl][kubectl] を使用します。 `kubectl` クライアントは Azure Cloud Shell に事前にインストールされています。

Cloud Shell を開くには、コード ブロックの右上隅にある **[使ってみる]** を選択します。 [https://shell.azure.com/bash](https://shell.azure.com/bash) に移動して、別のブラウザー タブで Cloud Shell を起動することもできます。 **[コピー]** を選択してコードのブロックをコピーし、Cloud Shell に貼り付けてから、Enter キーを押して実行します。

Kubernetes クラスターに接続するように `kubectl` を構成するには、[az aks get-credentials][az-aks-get-credentials] コマンドを実行します。 次の例では、*myResourceGroup* という名前のリソース グループの *myAKSCluster* という名前のクラスターの資格情報を取得します。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

クラスターへの接続を確認するには、[kubectl get][kubectl-get] コマンドを使用して、クラスター ノードの一覧を返します。

```azurecli-interactive
kubectl get nodes
```

次の出力例は、単一の VM ノードが作成されてから、Linux 用の仮想ノード、*virtual-node-aci-linux*が作成されることを示しています。

```
$ kubectl get nodes

NAME                           STATUS    ROLES     AGE       VERSION
virtual-node-aci-linux         Ready     agent     28m       v1.11.2
aks-agentpool-14693408-0       Ready     agent     32m       v1.11.2
```

## <a name="deploy-a-sample-app"></a>サンプル アプリのデプロイ

Azure Cloud Shell で、`virtual-node.yaml` という名前のファイルを作成し、そこに以下の YAML をコピーします。 ノード上のコンテナーをスケジュールするため、[nodeSelector][node-selector] and [toleration][toleration] が定義されます。 これらの設定により、仮想ノードでポッドがスケジュール設定され、機能が正常に有効化されていることを確認できます。

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
        image: microsoft/aci-helloworld
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

```azurecli-interactive
kubectl apply -f virtual-node.yaml
```

`-o wide` 引数を指定して [kubectl get pods][kubectl-get] コマンドを使用し、ポッドの一覧とスケジュールされたノードを出力します。 `virtual-node-helloworld` ポッドは `virtual-node-linux` ノードでスケジュールされています。

```
$ kubectl get pods -o wide

NAME                                     READY     STATUS    RESTARTS   AGE       IP           NODE
virtual-node-helloworld-9b55975f-bnmfl   1/1       Running   0          4m        10.241.0.4   virtual-node-aci-linux
```

ポッドには、仮想ノードで使用するために委任された Azure 仮想ネットワーク サブネットからの内部 IP アドレスが割り当てられます。

> [!NOTE]
> Azure Container Registry に格納されているイメージを使用する場合、[Kubernetes シークレットを構成して使用します][acr-aks-secrets]。 仮想ノードの現在の制限は、Azure AD サービス プリンシパル統合認証を使用できないことです。 シークレットを使用しない場合、仮想ノードでスケジュールされたポッドの開始に失敗し、エラー `HTTP response status code 400 error code "InaccessibleImage"` が報告されます。

## <a name="test-the-virtual-node-pod"></a>仮想ノード ポッドのテスト

仮想ノードで実行されているポッドをテストするには、Web クライアントでデモ アプリケーションを参照します。 ポッドには内部 IP アドレスが割り当てられているため、AKS クラスターの別のポッドからこの接続をすばやくテストできます。 テスト ポッドを作成し、それにターミナル セッションをアタッチします。

```azurecli-interactive
kubectl run -it --rm virtual-node-test --image=debian
```

`apt-get` を使用して、`curl` をポッドにインストールします。

```azurecli-interactive
apt-get update && apt-get install -y curl
```

次に、`curl` ( *http://10.241.0.4* など) を使用して、ポッドのアドレスにアクセスします。 前の `kubectl get pods` コマンドで示された独自の内部 IP アドレスを入力します。

```azurecli-interactive
curl -L http://10.241.0.4
```

次の簡約された出力例に示されているように、デモ アプリケーションが表示されます。

```
$ curl -L 10.241.0.4

<html>
<head>
  <title>Welcome to Azure Container Instances!</title>
</head>
[...]
```

`exit` を使用してテスト ポッドへのターミナル セッションを閉じます。 セッションが終了すると、ポッドが削除されます。

## <a name="next-steps"></a>次の手順

この記事では、ポッドを仮想ノードでスケジュールし、プライベートの内部 IP アドレスを割り当てました。 代わりに、サービスのデプロイを作成して、ロード バランサーまたはイングレス コントローラーを使用してトラフィックを自分のポッドにルーティングすることもできます。 詳細は、[AKS での基本的なイングレス コント ローラーの作成][aks-basic-ingress]を参照してください。

仮想ノードは、AKS のスケーリング ソリューションの 1 つのコンポーネントです。 スケーリング ソリューションの詳細については、次の記事を参照してください。

- [Kubernetes のポッドの水平オートスケーラーを使用する][aks-hpa]
- [Kubernetes クラスター オートスケーラーを使用する][aks-cluster-autoscaler]
- [仮想ノード用の自動スケーリング サンプルをチェックアウトする][virtual-node-autoscale]
- [Virtual Kubelet のオープン ソース ライブラリの詳細を確認する][virtual-kubelet-repo]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[aks-github]: https://github.com/azure/aks/issues]
[virtual-node-autoscale]: https://github.com/Azure-Samples/virtual-node-autoscale
[virtual-kubelet-repo]: https://github.com/virtual-kubelet/virtual-kubelet

<!-- LINKS - internal -->
[aks-network]: ./networking-overview.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[aks-hpa]: tutorial-kubernetes-scale.md
[aks-cluster-autoscaler]: cluster-autoscaler.md
[aks-basic-ingress]: ingress-basic.md
[acr-aks-secrets]: ../container-registry/container-registry-auth-aks.md#access-with-kubernetes-secret
[az-provider-list]: /cli/azure/provider#az-provider-list