---
title: Azure Kubernetes Service (AKS) クラスターで Virtual Kubelet を実行する
description: Azure Kubernetes Service (AKS) で Virtual Kubelet を使用して、Azure Container Instances 上で Linux および Windows コンテナーを実行する方法について説明します。
services: container-service
author: mlearned
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: mlearned
ms.openlocfilehash: f18992be353d2d6cc739412d98ccd97d5e78d4c7
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67613856"
---
# <a name="use-virtual-kubelet-with-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) での Virtual Kubelet の使用

Azure Container Instances (ACI) には Azure 内でコンテナーを実行するためのホスト環境が用意されています。 ACI を使用する場合、Azure がユーザーに代わって管理を担当するので、基礎となるコンピューティング インフラストラクチャを管理する必要はありません。 ACI でコンテナーを実行すると、実行中の各コンテナーについて秒単位で課金されます。

Azure Container Instances に Virtual Kubelet プロバイダーを使用する場合、標準の Kubernetes ノードであるかのように、コンテナー インスタンス上で Linux と Windows 両方のコンテナーをスケジュールすることができます。 この構成では、Kubernetes の機能と、コンテナー インスタンスの管理値とコスト上の利点の両方を活用できます。

> [!NOTE]
> AKS では、ACI でコンテナーをスケジュールするための "*仮想ノード*" と呼ばれる組み込みサポートを利用できるようになりました。 これらの仮想ノードでは、現在、Linux コンテナー インスタンスがサポートされています。 Windows コンテナー インスタンスをスケジュールする必要がある場合は、引き続き Virtual Kubelet を使用できます。 それ以外の場合は、この記事に記載されている手動の Virtual Kubelet の手順ではなく、仮想ノードを使用する必要があります。 仮想ノードを使い始めるには、[Azure CLI][virtual-nodes-cli] または or [Azure portal][virtual-nodes-portal] を使用します。
>
> Virtual Kubelet は実験的なオープン ソース プロジェクトなので、その点を考慮して使用する必要があります。 Virtual Kubelet に関する投稿、問題の提起、詳細情報については、[Virtual Kubelet GitHub プロジェクト][vk-github]に関するページを参照してください。

## <a name="before-you-begin"></a>開始する前に

このドキュメントでは、AKS クラスターがあることを前提としています。 AKS クラスターが必要な場合は、[Azure Kubernetes Service (AKS) のクイック スタート][aks-quick-start]に関するページを参照してください。

また、Azure CLI バージョン **2.0.65** 以降がインストールされている必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

Virtual Kubelet をインストールするには、AKS クラスターに [Helm][aks-helm] をインストールして構成します。 必要に応じて、Tiller が [Kubernetes RBAC で使用するために構成されている](#for-rbac-enabled-clusters)ことを確認します。

### <a name="register-container-instances-feature-provider"></a>Container Instances 機能プロバイダーを登録する

以前に Azure コンテナー インスタンス (ACI) サービスを使用していない場合は、ご使用のサブスクリプションでサービス プロバイダーを登録します。 ACI プロバイダーの状態は、次の例で示すように [az provider list][az-provider-list] コマンドを使用して確認できます。

```azurecli-interactive
az provider list --query "[?contains(namespace,'Microsoft.ContainerInstance')]" -o table
```

*Microsoft.ContainerInstance* プロバイダーは、次の出力の例で示すように *Registered* としてレポートします。

```console
Namespace                    RegistrationState
---------------------------  -------------------
Microsoft.ContainerInstance  Registered
```

プロバイダーが *NotRegistered* として示される場合は、次の例で示すように [az provider register][az-provider-register] を使用してプロバイダーを登録します。

```azurecli-interactive
az provider register --namespace Microsoft.ContainerInstance
```

### <a name="for-rbac-enabled-clusters"></a>RBAC 対応クラスターの場合

AKS クラスターが RBAC に対応している場合、Tiller で使用するためのサービス アカウントとロール バインディングを作成する必要があります。 詳細については、[Helm でのロール ベースのアクセス制御に関する説明][helm-rbac]を参照してください。 サービス アカウントとロールのバインドを作成するには、*rbac-virtual-kubelet.yaml* という名前のファイルを作成して次の定義を貼り付けます。

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: tiller
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: tiller
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: tiller
    namespace: kube-system
```

次の例に示すように、[kubectl apply][kubectl-apply] でサービス アカウントとバインドを適用し、*rbac-virtual-kubelet.yaml* ファイルを指定します。

```console
$ kubectl apply -f rbac-virtual-kubelet.yaml

clusterrolebinding.rbac.authorization.k8s.io/tiller created
```

Tiller サービス アカウントを使用するように Helm を構成する:

```console
helm init --service-account tiller
```

続けて、AKS クラスターに Virtual Kubelet をインストールすることができます。

## <a name="installation"></a>インストール

Virtual Kubelet をインストールするには、[az aks install-connector][aks-install-connector] コマンドを使用します。 次の例では、Linux と Windows 両方のコネクタを展開します。

```azurecli-interactive
az aks install-connector \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --connector-name virtual-kubelet \
    --os-type Both
```

これらの引数は、[az aks install-connector][aks-install-connector] コマンドに使用できます。

| 引数: | 説明 | 必須 |
|---|---|:---:|
| `--connector-name` | ACI コネクタの名前。| はい |
| `--name` `-n` | マネージド クラスターの名前。 | はい |
| `--resource-group` `-g` | リソース グループの名前。 | はい |
| `--os-type` | コンテナー インスタンスのオペレーティング システムのタイプ。 使用できる値は以下の通りです。両方、Linux、Windows。 既定値はLinux。 | いいえ |
| `--aci-resource-group` | ACI コンテナー グループを作成するリソース グループ。 | いいえ |
| `--location` `-l` | ACI コンテナー グループを作成する場所。 | いいえ |
| `--service-principal` | Azure API に対する認証に使用されるサービス プリンシパル。 | いいえ |
| `--client-secret` | サービス プリンシパルに関連付けられているシークレット。 | いいえ |
| `--chart-url` | ACI Connector をインストールする Helm チャートの URL。 | いいえ |
| `--image-tag` | Virtual Kubelet コンテナー イメージのイメージ タグ。 | いいえ |

## <a name="validate-virtual-kubelet"></a>Virtual Kubelet を検証する

Virtual Kubelet がインストールされていることを検証するには、[kubectl get nodes][kubectl-get] コマンドを使用して Kubernetes ノードの一覧を返します。

```console
$ kubectl get nodes

NAME                                             STATUS   ROLES   AGE   VERSION
aks-nodepool1-56577038-0                         Ready    agent   11m   v1.12.8
virtual-kubelet-virtual-kubelet-linux-eastus     Ready    agent   39s   v1.13.1-vk-v0.9.0-1-g7b92d1ee-dev
virtual-kubelet-virtual-kubelet-windows-eastus   Ready    agent   37s   v1.13.1-vk-v0.9.0-1-g7b92d1ee-dev
```

## <a name="run-linux-container"></a>Linux コンテナーを実行する

`virtual-kubelet-linux.yaml` という名前のファイルを作成し、そこに以下の YAML をコピーします。 ノード上のコンテナーをスケジュールするために [nodeSelector][node-selector] と and [toleration][toleration] が使用されている点に気を付けてください。

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
        beta.kubernetes.io/os: linux
        kubernetes.io/role: agent
        type: virtual-kubelet
      tolerations:
      - key: virtual-kubelet.io/provider
        operator: Equal
        value: azure
        effect: NoSchedule
```

[kubectl create][kubectl-create] コマンドを使用してアプリケーションを実行します。

```console
kubectl create -f virtual-kubelet-linux.yaml
```

`-o wide` 引数を指定して [kubectl get pods][kubectl-get] コマンドを使用し、スケジュールされたノードがあるポッドの一覧を出力します。 `aci-helloworld` ポッドは `virtual-kubelet-virtual-kubelet-linux` ノードでスケジュールされています。

```console
$ kubectl get pods -o wide

NAME                              READY   STATUS    RESTARTS   AGE     IP               NODE
aci-helloworld-7b9ffbf946-rx87g   1/1     Running   0          22s     52.224.147.210   virtual-kubelet-virtual-kubelet-linux-eastus
```

## <a name="run-windows-container"></a>Windows コンテナーを実行する

`virtual-kubelet-windows.yaml` という名前のファイルを作成し、そこに以下の YAML をコピーします。 ノード上のコンテナーをスケジュールするために [nodeSelector][node-selector] と and [toleration][toleration] が使用されている点に気を付けてください。

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nanoserver-iis
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nanoserver-iis
  template:
    metadata:
      labels:
        app: nanoserver-iis
    spec:
      containers:
      - name: nanoserver-iis
        image: microsoft/iis:nanoserver
        ports:
        - containerPort: 80
      nodeSelector:
        beta.kubernetes.io/os: windows
        kubernetes.io/role: agent
        type: virtual-kubelet
      tolerations:
      - key: virtual-kubelet.io/provider
        operator: Equal
        value: azure
        effect: NoSchedule
```

[kubectl create][kubectl-create] コマンドを使用してアプリケーションを実行します。

```console
kubectl create -f virtual-kubelet-windows.yaml
```

`-o wide` 引数を指定して [kubectl get pods][kubectl-get] コマンドを使用し、スケジュールされたノードがあるポッドの一覧を出力します。 `nanoserver-iis` ポッドは `virtual-kubelet-virtual-kubelet-windows` ノードでスケジュールされています。

```console
$ kubectl get pods -o wide

NAME                              READY   STATUS    RESTARTS   AGE     IP               NODE
nanoserver-iis-5d999b87d7-6h8s9   1/1     Running   0          47s     52.224.143.39    virtual-kubelet-virtual-kubelet-windows-eastus
```

## <a name="remove-virtual-kubelet"></a>Virtual Kubelet を削除する

Virtual Kubelet を削除するには、[az aks remove-connector][aks-remove-connector] コマンドを使用します。 引数の値を、コネクタ、AKS クラスター、および AKS クラスター リソース グループの名前に置き換えます。

```azurecli-interactive
az aks remove-connector \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --connector-name virtual-kubelet \
    --os-type Both
```

> [!NOTE]
> 両方の OS コネクタを削除する際にエラーが発生した場合、または Windows または Linux OS コネクタのみを削除する場合は、手動で OS の種類を指定できます。 前の `az aks remove-connector` コマンドに `--os-type` パラメーターを追加し、`Windows` または `Linux` を指定します。

## <a name="next-steps"></a>次の手順

Virtual Kubelet で考えられる問題については、[既知の問題と回避策][vk-troubleshooting]. To report problems with the Virtual Kubelet, [open a GitHub issue][vk-issues]に関する記事を参照してください。

[Virtual Kubelet GitHub プロジェクト][vk-github]のページで Virtual Kubelet の詳細について参照してください。

<!-- LINKS - internal -->
[aks-quick-start]: ./kubernetes-walkthrough.md
[aks-remove-connector]: /cli/azure/aks#az-aks-remove-connector
[az-container-list]: /cli/azure/aks#az-aks-list
[aks-install-connector]: /cli/azure/aks#az-aks-install-connector
[virtual-nodes-cli]: virtual-nodes-cli.md
[virtual-nodes-portal]: virtual-nodes-portal.md
[aks-helm]: kubernetes-helm.md
[az-provider-list]: /cli/azure/provider#az-provider-list
[az-provider-register]: /cli/azure/provider#az-provider-register

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[vk-github]: https://github.com/virtual-kubelet/virtual-kubelet
[helm-rbac]: https://docs.helm.sh/using_helm/#role-based-access-control
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[vk-troubleshooting]: https://github.com/virtual-kubelet/virtual-kubelet#known-quirks-and-workarounds
[vk-issues]: https://github.com/virtual-kubelet/virtual-kubelet/issues
