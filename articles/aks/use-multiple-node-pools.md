---
title: Azure Kubernetes Service (AKS) で複数のノード プールを使用する
description: Azure Kubernetes Service (AKS) のクラスターで複数のノード プールを作成および管理する方法について学習します
services: container-service
ms.topic: article
ms.date: 04/08/2020
ms.openlocfilehash: 3e029695e9dce79473ada0bae3e7f0bbfd30db89
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102218487"
---
# <a name="create-and-manage-multiple-node-pools-for-a-cluster-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) のクラスターで複数のノード プールを作成および管理する

Azure Kubernetes Service (AKS) で同じ構成のノードは、*ノード プール* にグループ化できます。 これらのノード プールには、お使いのアプリケーションを実行する基になる VM が含まれています。 最初のノード数とそのサイズ (SKU) は、"[システム ノード プール][use-system-pool]" が作成される AKS クラスターの作成時に定義します。 コンピューティングまたは記憶域の要件が異なるアプリケーションをサポートするには、追加の "*ユーザー ノード プール*" を作成します。 システム ノード プールは、CoreDNS や tunnelfront などの重要なシステム ポッドをホストするという主要な目的を果たします。 ユーザー ノード プールは、アプリケーション ポッドをホストするという主要な目的を果たします。 ただし、AKS クラスター内のプールを 1 つだけにする場合は、システム ノード プールでアプリケーション ポッドをスケジュールすることができます。 ユーザー ノード プールは、お使いのアプリケーションに固有のポッドを配置する場所です。 たとえば、これらの追加のユーザー ノード プールを使用すると、コンピューティング集約型のアプリケーションに GPU を提供したり、高パフォーマンスな SSD ストレージにアクセスを提供したりできます。

> [!NOTE]
> この機能を使用すると、複数のノード プールを作成および管理する方法をより細かく制御できます。 そのため、作成/更新/削除には個別のコマンドが必要です。 以前は、`az aks create` または `az aks update` を介したクラスター操作は、managedCluster API を使用するものであり、コントロール プレーンと単一ノード プールを変更する唯一の方法でした。 この機能は、agentPool API を介した、エージェント プールに対する個別の操作セットを公開するものであり、個々のノード プールに対して操作を実行するには `az aks nodepool` コマンド セットを使用する必要があります。

この記事では、AKS クラスターで複数のノード プールを作成および管理する方法について説明します。

## <a name="before-you-begin"></a>開始する前に

Azure CLI バージョン 2.2.0 以降がインストールされて構成されている必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][install-azure-cli]に関するページを参照してください。

## <a name="limitations"></a>制限事項

複数のノード プールをサポートする AKS クラスターを作成および管理する場合には、次の制限があります。

* 「[Azure Kubernetes Service (AKS) のクォータ、仮想マシンのサイズの制限、およびリージョンの可用性][quotas-skus-regions]」を参照してください。
* 別のシステム ノード プールが AKS クラスター内にあり、それが代わりをする場合に、システム ノード プールを削除できる。
* システム プールには少なくとも 1 つのノードを含める必要があり、ユーザー ノード プールには 0 以上のノードを含めることができます。
* AKS クラスターが複数のノード プールを使用するためには、Standard SKU のロード バランサーを使用する必要があります。Basic SKU のロード バランサーでは、この機能がサポートされません。
* AKS クラスターでは、ノードに仮想マシン スケール セットを使用する必要があります。
* ノード プールの名前は、小文字の英数字のみを含めることができ、小文字で始める必要があります。 Linux ノード プールの場合、長さは 1 から 12 文字である必要があります。Windows ノード プールの場合、長さは 1 から 6 文字である必要があります。
* すべてのノード プールは、同じ仮想ネットワーク内に存在する必要があります
* クラスターの作成時に複数のノード プールを作成する場合は、ノード プールで使用されるすべての Kubernetes のバージョンが、コントロール プレーンに設定されたバージョンと一致している必要があります。 これは、ノード プールごとの操作を使用してクラスターがプロビジョニングされた後で更新できます。

## <a name="create-an-aks-cluster"></a>AKS クラスターを作成する

> [!Important]
> 運用環境内のお使いの AKS クラスターで 1 つのシステム ノード プールを実行する場合、そのノード プールには少なくとも 3 つのノードを使用することをお勧めします。

まず、1 つのノード プールで AKS クラスターを作成開始します。 次の例では、[az group create][az-group-create] コマンドを使用して、*myResourceGroup* という名前のリソース グループを *eastus* リージョンに作成しています。 次いで、*myAKSCluster* という名前の AKS クラスターを [az aks create][az-aks-create] コマンドを使用して作成しています。

> [!NOTE]
> 複数のノード プールを使用する場合、*Basic* ロード バランサー SKU は **サポートされません**。 既定では、AKS クラスターが、Azure CLI および Azure portal から *Standard* ロード バランサー SKU で作成されます。

```azurecli-interactive
# Create a resource group in East US
az group create --name myResourceGroup --location eastus

# Create a basic single-node AKS cluster
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 2 \
    --generate-ssh-keys \
    --load-balancer-sku standard
```

クラスターの作成には数分かかります。

> [!NOTE]
> 重要なシステム サービスが既定のノード プールで実行されるため、クラスターを確実に動作させるには、このノード プールで少なくとも 2 つのノードを実行する必要があります。

クラスターの準備ができたら、[az aks get-credentials][az-aks-get-credentials] コマンドを使用して、`kubectl` で使用するクラスターの資格情報を取得します。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="add-a-node-pool"></a>ノード プールの追加

前の手順で作成したクラスターには、ノード プールが 1 つあります。 [az aks nodepool add][az-aks-nodepool-add] コマンドを使用して、2 番目のノード プールを追加しましょう。 次の例では、*3* つのノードを実行する *mynodepool* という名前のノード プールを作成します。

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3
```

> [!NOTE]
> ノード プールの名前は、小文字で始める必要があり、英数字のみを含めることができます。 Linux ノード プールの場合、長さは 1 から 12 文字である必要があります。Windows ノード プールの場合、長さは 1 から 6 文字である必要があります。

お使いのノード プールの状態を確認するには、[az aks node pool list][az-aks-nodepool-list] コマンドを使用し、次のようにお使いのリソース グループとクラスター名を指定します。

```azurecli-interactive
az aks nodepool list --resource-group myResourceGroup --cluster-name myAKSCluster
```

次の出力例では、*mynodepool* がノード プール内に 3 つのノードと共に正常に作成されたことを示しています。 前の手順で AKS クラスターを作成したとき、既定の *nodepool1* がノード数 *2* で作成されました。

```output
[
  {
    ...
    "count": 3,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

> [!TIP]
> ノード プールを追加するときに *VmSize* を指定しなかった場合、既定のサイズは、Windows ノード プールの場合は *Standard_D2s_v3*、Linux ノード プールの場合は *Standard_DS2_v2* になります。 *OrchestratorVersion* が指定されていない場合、コントロール プレーンと同じバージョンが既定値になります。

### <a name="add-a-node-pool-with-a-unique-subnet-preview"></a>一意なサブネットを持つノード プールを追加する (プレビュー)

ワークロードでは、クラスターのノードを論理的に分離するために個別のプールに分割することが必要になる場合があります。 この分離は、クラスター内の各ノード プール専用の個別のサブネットによってサポートできます。 これにより、ノード プール間で分割するための連続しない仮想ネットワーク アドレス空間などの要件に対応できます。

#### <a name="limitations"></a>制限事項

* ノード プールに割り当てられるサブネットはすべて、同じ仮想ネットワークに属している必要があります。
* DNS 解決や kubectl logs/exec/port-forward プロキシのトンネリングなど、重要な機能を提供するために、システム ポッドはクラスター内のすべてのノードとポッドにアクセスできる必要があります。
* クラスター作成後、VNet を拡張する場合、元の cidr の外でサブネットを追加する前に、クラスターを更新する必要があります (マネージド クラスター操作があれば、それを実行しますが、ノード プール操作は数に入りません)。 元々は許可していましたが、エージェント プールを追加すると AKS でエラーが出るようになっています。 クラスターを調整する方法がわからない場合、サポート チケットを提出してください。 
* Calico ネットワーク ポリシーはサポートされていません。 
* Azure ネットワーク ポリシーはサポートされていません。
* Kube-proxy からは隣接する cidr が 1 つ求められ、3 つの最適化にそれが使用されます。 詳細については、この [K.E.P](https://github.com/kubernetes/enhancements/blob/master/keps/sig-network/20191104-iptables-no-cluster-cidr.md ) と [こちら](https://kubernetes.io/docs/reference/command-line-tools-reference/kube-proxy/)の --cluster-cidr を参照してください。 azure cni では、最初のノード プールのサブネットが kube-proxy に与えられます。 

専用サブネットを持つノード プールを作成するには、ノード プールを作成する際に、サブネットのリソース ID を追加パラメーターとして渡します。

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3 \
    --vnet-subnet-id <YOUR_SUBNET_RESOURCE_ID>
```

## <a name="upgrade-a-node-pool"></a>ノード プールのアップグレード

> [!NOTE]
> クラスターまたはノード プールでは、アップグレードやスケーリングの操作を同時に実行することはできず、実行を試みた場合には、エラーが返されます。 代わりに、ターゲット リソースに対する次の要求を行う前に、各操作の種類をその同じリソースで完了する必要があります。 詳細については、[トラブルシューティング ガイド](./troubleshooting.md#im-receiving-errors-when-trying-to-upgrade-or-scale-that-state-my-cluster-is-being-upgraded-or-has-failed-upgrade)を参照してください。

このセクションのコマンドからは、1 つの特定のノード プールをアップグレードする方法が説明されます。 コントロール プレーンとノード プールの Kubernetes バージョンをアップグレードするとき、両者の関係については、[下のセクション](#upgrade-a-cluster-control-plane-with-multiple-node-pools)で説明します。

> [!NOTE]
> ノード プールの OS イメージのバージョンは、クラスターの Kubernetes バージョンに関連付けられています。 OS イメージは、クラスターのアップグレードの後でのみアップグレードされます。

この例には 2 つのノード プールがあるため、[az aks nodepool upgrade][az-aks-nodepool-upgrade] を使用してノード プールをアップグレードする必要があります。 使用可能なアップグレードを確認するには、[az aks get-upgrades][az-aks-get-upgrades] を使用します。

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster
```

*mynodepool* をアップグレードしましょう。 次の例のように、[az aks node pool upgrade][az-aks-nodepool-upgrade] コマンドを使用してノード プールをアップグレードします。

```azurecli-interactive
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --kubernetes-version KUBERNETES_VERSION \
    --no-wait
```

[az aks node pool list][az-aks-nodepool-list] コマンドを使用し、再度お使いのノード プールの状態を一覧表示します。 次の例では、*mynodepool* が *KUBERNETES_VERSION* への *Upgrading* 状態であることを示しています。

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 3,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "KUBERNETES_VERSION",
    ...
    "provisioningState": "Upgrading",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

ノードを指定したバージョンにアップグレードするには数分かかります。

AKS クラスター内のノード プールは、すべて同じ Kubernetes のバージョンにアップグレードするのがベスト プラクティスです。 `az aks upgrade` の既定の動作では、この配置を実現するために、すべてのノード プールがコントロール プレーンと共にアップグレードされます。 個々のノード プールをアップグレードできる機能によりローリング アップグレードの実行が可能になり、アプリケーションのアップタイムを維持するノード プール間のポッドを前述の制約内でスケジュールできます。

## <a name="upgrade-a-cluster-control-plane-with-multiple-node-pools"></a>複数のノード プールを使用するでクラスターのコントロール プレーンをアップグレードする

> [!NOTE]
> Kubernetes は、標準の[セマンティック バージョニング](https://semver.org/)のバージョン管理スキームを使用します。 バージョン番号は *x.y.z* として表されます。ここで、*x* はメジャー バージョン、*y* はマイナー バージョン、*z* は修正プログラムのバージョンです。 たとえば、バージョン *1.12.6* の場合は、1 がメジャー バージョン、12 がマイナー バージョン、6 が修正プログラムのバージョンです。 コントロール プレーンの Kubernetes バージョンと初期ノード プールは、クラスター作成時に設定されます。 その他のすべてのノード プールには、クラスターに追加されるときに Kubernetes バージョンが設定されます。 Kubernetes バージョンは、ノード プール間、およびノード プールとコントロール プレーン間では異なる場合があります。

AKS クラスターには、Kubernetes バージョンが関連付けられている 2 つのクラスター リソース オブジェクトがあります。

1. クラスターのコントロール プレーンの Kubernetes バージョン。
2. Kubernetes バージョンのノード プール。

コントロール プレーンは、1 つまたは複数のノード プールにマップされます。 アップグレード操作の動作は、どの Azure CLI コマンドを使用するかによって異なります。

AKS コントロール プレーンをアップグレードするには、`az aks upgrade` を使用する必要があります。 このコマンドにより、コントロール プレーンのバージョンとクラスター内のすべてのノード プールがアップグレードされます。

`--control-plane-only` フラグを使用して `az aks upgrade` コマンドを実行すると、クラスターのコントロール プレーンのみがアップグレードされます。 クラスター内の関連付けられているノード プールは一切変更されません。

個々のノード プールをアップグレードするには、`az aks nodepool upgrade` を使用する必要があります。 このコマンドでは、指定した Kubernetes バージョンのターゲット ノード プールのみがアップグレードされます

### <a name="validation-rules-for-upgrades"></a>アップグレードの検証規則

クラスターのコントロール プレーンおよびノード プールに対して有効な Kubernetes のアップグレードは、次の規則のセットによって検証されます。

* ノード プールをアップグレードするための有効なバージョンの規則:
   * ノード プールのバージョンは、コントロール プレーンと同じ "*メジャー*" バージョンである必要があります。
   * ノード プールの "*マイナー*" バージョンは、コントロール プレーンのバージョンの 2 つ以内の "*マイナー*" バージョンでなければなりません。
   * ノード プールのバージョンを、コントロールの `major.minor.patch` バージョンよりも大きくすることはできません。

* アップグレード操作を送信するための規則:
   * コントロール プレーンでも、ノード プールでも、Kubernetes バージョンをダウングレードすることはできません。
   * ノード プールの Kubernetes バージョンが指定されていない場合、動作は使用中のクライアントによって異なります。 Resource Manager テンプレートの宣言は、ノード プールに対して定義されている既存のバージョンが使用される場合、そのバージョンにフォールバックします。何も設定されていない場合、フォールバックのためにコントロール プレーン バージョンが使用されます。
   * 特定の時点でコントロール プレーンまたはノード プールのアップグレードまたはスケーリングのどちらかを行うことはできますが、単一のコントロール プレーンまたはノード プール リソースに対して複数の操作を同時に送信することはできません。

## <a name="scale-a-node-pool-manually"></a>ノード プールの手動でのスケーリング

お使いのアプリケーションのワークロードに対する需要の変化に伴い、ノード プール内のノード数をスケーリングしなければならなくなる場合があります。 ノード数は増やすことも減らすことも可能です。

<!--If you scale down, nodes are carefully [cordoned and drained][kubernetes-drain] to minimize disruption to running applications.-->

ノード プール内のノード数をスケーリングするには、[az aks node pool scale][az-aks-nodepool-scale] コマンドを使用します。 次の例では、*mynodepool* 内のノード数を *5* にスケーリングしています。

```azurecli-interactive
az aks nodepool scale \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 5 \
    --no-wait
```

[az aks node pool list][az-aks-nodepool-list] コマンドを使用し、再度お使いのノード プールの状態を一覧表示します。 次の例では、*mynodepool* が新しいノード数の *5* で *Scaling* の状態であることを示しています。

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 5,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Scaling",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

スケーリングの操作の完了には、数分かかります。

## <a name="scale-a-specific-node-pool-automatically-by-enabling-the-cluster-autoscaler"></a>クラスター オートスケーラーを有効にすることで特定のノード プールを自動的にスケーリングする

AKS には、[クラスター オートスケーラー](cluster-autoscaler.md)と呼ばれる機能を使用してノード プールを自動的にスケーリングするための独立した機能が用意されています。 この機能は、ノード プールごとに一意の最小および最大スケール カウントを使用して、ノード プールごとに有効にすることができます。 [ノード プールごとのクラスター オートスケーラーを使用](cluster-autoscaler.md#use-the-cluster-autoscaler-with-multiple-node-pools-enabled)する方法を参照してください。

## <a name="delete-a-node-pool"></a>ノード プールの削除

プールが不要になったら、それを削除して、基になっている VM を削除します。 ノード プールを削除するには、[az aks node pool delete][az-aks-nodepool-delete] コマンドを使用し、ノード プール名を指定します。 次の例では、前の手順で作成した *mynoodepool* を削除します。

> [!CAUTION]
> ノード プールの削除で失われたデータを復元するオプションはありません。 その他のノード プールでポッドをスケジューリングできない場合、それらのアプリケーションは利用できません。 使用中のアプリケーションにデータ バックアップがない場合や、お使いのクラスター内の他のノード プールで実行する機能がない場合は、ノード プールは削除しないでください。

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name mynodepool --no-wait
```

次の [az aks node pool list][az-aks-nodepool-list] コマンドでの出力例では、*mynodepool* が *Deleting* の状態であることを示しています。

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 5,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Deleting",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

ノードおよびノード プールの削除には数分かかります。

## <a name="specify-a-vm-size-for-a-node-pool"></a>ノード プールの VM サイズの指定

前のノード プールの作成例では、クラスターに作成したノードに、既定の VM サイズを使用しました。 より一般的なシナリオでは、さまざまなサイズおよび性能の VM のノード プールが作成されます。 たとえば、大きな CPU またはメモリのノードが含まれるノード プール、または GPU をサポートするノード プールが作成されます。 次の手順では、[taints と tolerations を使用](#setting-nodepool-taints)して、Kubernetes スケジューラにこれらのノードで実行されるポッドに対するアクセスを制限する方法を通知します。

次の例では、*Standard_NC6* の VM サイズを使用する GPU ベースのノード プールを作成します。 これらの VM は NVIDIA Tesla K80 カードを搭載しています。 使用可能な VM サイズの詳細については、「[Azure での Linux VM のサイズ][vm-sizes]」を参照してください。

再度 [az aks node pool add][az-aks-nodepool-add] コマンドを使用して、ノード プールを作成します。 今回は、次のように *gpunodepool* を名前として指定し、*Standard_NC6* サイズを `--node-vm-size` パラメーターを使用して指定します。

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name gpunodepool \
    --node-count 1 \
    --node-vm-size Standard_NC6 \
    --no-wait
```

[az aks node pool list][az-aks-nodepool-list] コマンドの次の出力例では、*gpunodepool* によって指定した *VmSize* を使用してノードが *Creating* されていることを示しています。

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 1,
    ...
    "name": "gpunodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "vmSize": "Standard_NC6",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

*gpunodepool* が正常に作成されるには、数分かかります。

## <a name="specify-a-taint-label-or-tag-for-a-node-pool"></a>テイント、ラベル、またはタグをノード プールに指定する

### <a name="setting-nodepool-taints"></a>ノード プールのテイントの設定

ノード プールを作成するときに、テイント、ラベル、タグをそのノード プールに追加できます。 テイント、ラベル、タグを追加すると、そのノード プール内のすべてのノードもそのテイント、ラベル、タグを取得します。

テイントが指定されたノード プールを作成するには、[az aks nodepool add][az-aks-nodepool-add] を使用します。 名前 *taintnp* を指定し、`--node-taints` パラメーターを使用してテイントに *sku=gpu:NoSchedule* を指定します。

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name taintnp \
    --node-count 1 \
    --node-taints sku=gpu:NoSchedule \
    --no-wait
```

> [!NOTE]
> テイントは、ノード プールを作成するときにノード プールに対してのみ設定できます。

[az aks nodepool list][az-aks-nodepool-list] コマンドからの次の出力例では、*taintnp* によって、指定した *nodeTaints* でノードが "*作成されている*" ことが示されています。

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 1,
    ...
    "name": "taintnp",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "nodeTaints":  [
      "sku=gpu:NoSchedule"
    ],
    ...
  },
 ...
]
```

テイントの情報は、ノードのスケジューリング規則を処理するために Kubernetes に表示されます。 Kubernetes スケジューラでは、テイントと容認を使用して、ノードで実行できるワークロードを制限できます。

* **テイント** は、ノードに適用されて、特定のポッドのみをそのノードでスケジュールできることを示します。
* **容認** は、ポッドに適用されて、ポッドがノードのテイントを "*許容する*" ことを許可します。

Kubernetes での高度なスケジューラ機能の詳細については、「[Azure Kubernetes Service (AKS) での高度なスケジューラ機能に関するベスト プラクティス][taints-tolerations]」を参照してください。

前の手順では、ノード プールの作成時に *sku=gpu:NoSchedule* テイントを適用しました。 次の YAML マニフェストの基本例では、Kubernetes スケジューラがそのノードプール内のノードで NGINX ポッドを実行できるように toleration を使用しています。

`nginx-toleration.yaml` という名前のファイルを作成し、次の例の YAML にコピーします。

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - image: mcr.microsoft.com/oss/nginx/nginx:1.15.9-alpine
    name: mypod
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 1
        memory: 2G
  tolerations:
  - key: "sku"
    operator: "Equal"
    value: "gpu"
    effect: "NoSchedule"
```

`kubectl apply -f nginx-toleration.yaml` コマンドを使用してポッドをスケジュールします。

```console
kubectl apply -f nginx-toleration.yaml
```

ポッドのスケジュールおよび NGINX イメージのプルには、数秒かかります。 [kubectl describe pod][kubectl-describe] コマンドを使用して、ポッドの状態を参照します。 次の縮約された出力例では、*sku = gpu:NoSchedule* toleration を適用しています。 イベント セクションで、スケジューラによってポッドが *aks-taintnp-28993262-vmss000000* ノードに割り当てられています。

```console
kubectl describe pod mypod
```

```output
[...]
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
                 sku=gpu:NoSchedule
Events:
  Type    Reason     Age    From                Message
  ----    ------     ----   ----                -------
  Normal  Scheduled  4m48s  default-scheduler   Successfully assigned default/mypod to aks-taintnp-28993262-vmss000000
  Normal  Pulling    4m47s  kubelet             pulling image "mcr.microsoft.com/oss/nginx/nginx:1.15.9-alpine"
  Normal  Pulled     4m43s  kubelet             Successfully pulled image "mcr.microsoft.com/oss/nginx/nginx:1.15.9-alpine"
  Normal  Created    4m40s  kubelet             Created container
  Normal  Started    4m40s  kubelet             Started container
```

*taintnp* のノードでスケジュールできるのは、この toleration が適用されているポッドのみです。 その他のポッドは、*nodepool1* ノード プールにスケジュールされます。 ノード プールを追加作成した場合、追加の taints と tolerations を使用して、それらのノード リソースにどのようなポッドをスケジュールするか制限できます。

### <a name="setting-nodepool-labels"></a>ノード プールのラベルの設定

ノード プールを作成するときに、ラベルをノード プールに追加することもできます。 ノード プールに設定されたラベルは、ノード プールの各ノードに追加されます。 ノードのスケジューリング規則を処理するため、これらの[ラベルは Kubernetes に表示][kubernetes-labels]されます。

ラベルが追加されたノード プールを作成するには、[az aks nodepool add][az-aks-nodepool-add] を使用します。 名前 *labelnp* を指定し、`--labels` パラメーターを使用して、*dept=IT* と *costcenter=9999* をラベルに指定します。

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name labelnp \
    --node-count 1 \
    --labels dept=IT costcenter=9999 \
    --no-wait
```

> [!NOTE]
> ラベルは、ノード プールを作成するときにノード プールに対してのみ設定できます。 また、ラベルはキーと値のペアであり、[有効な構文][kubernetes-label-syntax]で指定されている必要があります。

[az aks nodepool list][az-aks-nodepool-list] コマンドからの次の出力例では、*labelnp* によって、指定した *nodeLabels* でノードが "*作成されている*" ことが示されています。

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 1,
    ...
    "name": "labelnp",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "nodeLabels":  {
      "dept": "IT",
      "costcenter": "9999"
    },
    ...
  },
 ...
]
```

### <a name="setting-nodepool-azure-tags"></a>ノード プールの Azure タグの設定

AKS クラスター内のノード プールに Azure タグを適用できます。 ノード プールに適用されるタグは、ノード プール内の各ノードに適用され、アップグレードによって保持されます。 また、スケールアウト操作中にノード プールに追加される新しいノードにもタグが適用されます。 タグを追加すると、ポリシーの追跡やコスト見積もりなどのタスクに役立ちます。

Azure タグには、キーを検索してタグを取得する場合などに操作の大文字と小文字を区別しないキーがあります。 この場合、指定されたキーを持つタグは、大文字/小文字の違いに関係なく更新または取得されます。 タグの値は大文字と小文字が区別されます。

AKS では、複数のタグが同じキーで設定され、大文字/小文字が異なる場合、使用されるタグはアルファベット順で最初のものになります。 たとえば、`{"Key1": "val1", "kEy1": "val2", "key1": "val3"}` の場合は `Key1` と `val1` が設定されます。

[az aks nodepool add][az-aks-nodepool-add] を使用してノード プールを作成します。 名前 *tagnodepool* を指定し、`--tag` パラメーターを使用して、*dept = IT* と *costcenter=9999* をタグに指定します。

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name tagnodepool \
    --node-count 1 \
    --tags dept=IT costcenter=9999 \
    --no-wait
```

> [!NOTE]
> `--tags` パラメーターは、[az aks nodepool update][az-aks-nodepool-update] コマンドを使用する場合やクラスターの作成時にも使用できます。 クラスターの作成時に、`--tags` パラメーターを指定すると、そのクラスターで作成される最初のノード プールにタグが適用されます。 すべてのタグ名は、「[タグを使用した Azure リソースの整理][tag-limitation]」の制限に従う必要があります。 `--tags` パラメーターを使用してノード プールを更新すると、既存のタグ値が更新され、新しいタグが追加されます。 たとえば、ノード プールにタグの *dept=IT* と *costcenter=9999* があった場合は、タグの *team=dev* と *costcenter=111* で更新すると、ノードプールには、タグの *dept=IT*、*costcenter=111*、*team=dev* が含まれます。

[az aks nodepool list][az-aks-nodepool-list] コマンドの次の出力例は、*tagnodepool* によって、指定した "*タグ*" を使用してノードが *Creating* されていることを示しています。

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 1,
    ...
    "name": "tagnodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "tags": {
      "dept": "IT",
      "costcenter": "9999"
    },
    ...
  },
 ...
]
```

## <a name="manage-node-pools-using-a-resource-manager-template"></a>Resource Manager テンプレートを使用したノード プールの管理

作成する Azure Resource Manager テンプレートと管理対象リソースを使用する場合、通常、テンプレート内の設定を更新し、再デプロイしてリソースを更新できます。 AKS 内のノード プールでは、AKS クラスターが作成されると、初期ノード プール プロファイルは更新できません。 この動作は、既存の Resource Manager テンプレートの更新、ノード プールへの変更、再デプロイが行えないことを意味します。 代わりに、既存の AKS クラスターのノード プールのみを更新する別の Resource Manager テンプレートを作成する必要があります。

`aks-agentpools.json` などのテンプレートを作成し、次のマニフェスト例を貼り付けます。 このテンプレートの例は、次の設定を構成します。

* 3 つのノードを実行するように、*myagentpool* という *Linux* ノード プールを更新します。
* Kubernetes バージョン *1.15.7* を実行するように、ノード プール内のノードを設定します。
* *Standard_DS2_v2* とノード サイズを定義します。

必要に応じてこれらの値を編集し、ノード プールを更新、追加、または削除します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "clusterName": {
            "type": "string",
            "metadata": {
                "description": "The name of your existing AKS cluster."
            }
        },
        "location": {
            "type": "string",
            "metadata": {
                "description": "The location of your existing AKS cluster."
            }
        },
        "agentPoolName": {
            "type": "string",
            "defaultValue": "myagentpool",
            "metadata": {
                "description": "The name of the agent pool to create or update."
            }
        },
        "vnetSubnetId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The Vnet subnet resource ID for your existing AKS cluster."
            }
        }
    },
    "variables": {
        "apiVersion": {
            "aks": "2020-01-01"
        },
        "agentPoolProfiles": {
            "maxPods": 30,
            "osDiskSizeGB": 0,
            "agentCount": 3,
            "agentVmSize": "Standard_DS2_v2",
            "osType": "Linux",
            "vnetSubnetId": "[parameters('vnetSubnetId')]"
        }
    },
    "resources": [
        {
            "apiVersion": "2020-01-01",
            "type": "Microsoft.ContainerService/managedClusters/agentPools",
            "name": "[concat(parameters('clusterName'),'/', parameters('agentPoolName'))]",
            "location": "[parameters('location')]",
            "properties": {
                "maxPods": "[variables('agentPoolProfiles').maxPods]",
                "osDiskSizeGB": "[variables('agentPoolProfiles').osDiskSizeGB]",
                "count": "[variables('agentPoolProfiles').agentCount]",
                "vmSize": "[variables('agentPoolProfiles').agentVmSize]",
                "osType": "[variables('agentPoolProfiles').osType]",
                "storageProfile": "ManagedDisks",
                "type": "VirtualMachineScaleSets",
                "vnetSubnetID": "[variables('agentPoolProfiles').vnetSubnetId]",
                "orchestratorVersion": "1.15.7"
            }
        }
    ]
}
```

次の例に示すように、[az deployment group create][az-deployment-group-create] コマンドを使用してこのテンプレートをデプロイします。 既存の AKS クラスターの名前と場所を求められます。

```azurecli-interactive
az deployment group create \
    --resource-group myResourceGroup \
    --template-file aks-agentpools.json
```

> [!TIP]
> 次の例に示すように、テンプレートに *tag* プロパティを追加することで、ノード プールにタグを追加できます。
> 
> ```json
> ...
> "resources": [
> {
>   ...
>   "properties": {
>     ...
>     "tags": {
>       "name1": "val1"
>     },
>     ...
>   }
> }
> ...
> ```

Resource Manager テンプレートで定義するノード プール設定および操作に応じて、AKS クラスターの更新には数分かかる場合があります。

## <a name="assign-a-public-ip-per-node-for-your-node-pools-preview"></a>ノード プールのノードごとにパブリック IP を割り当てる (プレビュー)

> [!WARNING]
> ノードごとにパブリック IP を割り当てる機能を使用するには、CLI プレビュー版拡張機能 0.4.43 以上をインストールする必要があります。

AKS ノードは、通信用に独自のパブリック IP アドレスを必要としません。 ただし、シナリオでは、ノード プール内のノードが専用のパブリック IP アドレスを受け取ることが必要な場合があります。 一般的なシナリオとしては、ゲームのワークロードがあります。この場合、ホップを最小限に抑えるために、コンソールをクラウド仮想マシンに直接接続する必要があります。 このシナリオは、プレビュー機能であるノード パブリック IP (プレビュー) を登録することにより、AKS で実現することができます。

最新の aks-preview 拡張機能をインストールして更新するには、次の Azure CLI コマンドを使用します。

```azurecli
az extension add --name aks-preview
az extension update --name aks-preview
az extension list
```

ノード パブリック IP 機能を登録するには、次の Azure CLI コマンドを使用します。

```azurecli-interactive
az feature register --name NodePublicIPPreview --namespace Microsoft.ContainerService
```
機能の登録には数分かかる場合があります。  状態を確認するには、次のコマンドを使用します。

```azurecli-interactive
 az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/NodePublicIPPreview')].{Name:name,State:properties.state}"
```

登録が正常に完了したら、新しいリソース グループを作成します。

```azurecli-interactive
az group create --name myResourceGroup2 --location eastus
```

新しい AKS クラスターを作成し、ノードのパブリック IP を接続します。 ノード プール内の各ノードは、一意のパブリック IP を受け取ります。 これを確認するには、仮想マシン スケール セットのインスタンスを参照します。

```azurecli-interactive
az aks create -g MyResourceGroup2 -n MyManagedCluster -l eastus  --enable-node-public-ip
```

既存の AKS クラスターの場合は、新しいノード プールを追加し、ノードのパブリック IP を接続することもできます。

```azurecli-interactive
az aks nodepool add -g MyResourceGroup2 --cluster-name MyManagedCluster -n nodepool2 --enable-node-public-ip
```

> [!Important]
> プレビュー期間中、Azure Instance Metadata Service は、現在 Standard レベルの VM SKU のパブリック IP アドレスの取得をサポートしていません。 この制限のため、kubectl コマンドを使用して、ノードに割り当てられているパブリック IP アドレスを表示することはできません。 ただし、IP は割り当てられ、意図したとおりに機能します。 ノードのパブリック IP は、仮想マシン スケール セット内のインスタンスに接続されます。

ノードのパブリック IP は、さまざまな方法で見つけることができます。

* Azure CLI コマンド [az vmss list-instance-public-ips][az-list-ips] を使用
* [PowerShell または Bash コマンド][vmss-commands]を使用。 
* 仮想マシン スケール セット内のインスタンスを表示して、Azure portal 内のパブリック IP を表示することも可能です。

> [!Important]
> [ノード リソース グループ][node-resource-group]には、ノードとそのパブリック IP が含まれています。 ノードのパブリック IP を検索するコマンドを実行するときは、ノード リソース グループを使用します。

```azurecli
az vmss list-instance-public-ips -g MC_MyResourceGroup2_MyManagedCluster_eastus -n YourVirtualMachineScaleSetName
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

この記事では、GPU ベースのノードを含む AKS クラスターを作成しました。 不要なコストを減らすには、*gpunodepool*、または AKS クラスター全体を削除してください。

GPU ベースのノード プールを削除するには、次の例のように [az aks nodepool delete][az-aks-nodepool-delete] コマンドを使用します。

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name gpunodepool
```

クラスター自体を削除するには、[az group delete][az-group-delete] コマンドを使用して AKS リソース グループを削除します。

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

また、ノード プールのパブリック IP のシナリオで作成した追加のクラスターを削除することもできます。

```azurecli-interactive
az group delete --name myResourceGroup2 --yes --no-wait
```

## <a name="next-steps"></a>次のステップ

[システム ノード プール][use-system-pool]の詳細情報

この記事では、AKS クラスターで複数のノード プールを作成および管理する方法を学習しました。 すべてのノード プールのポッドを制御する方法の詳細については、「[Azure Kubernetes Service (AKS) での高度なスケジューラ機能に関するベスト プラクティス][operator-best-practices-advanced-scheduler]」を参照してください。

Windows Server コンテナー ノード プールを作成して使用するには、[AKS での Windows Server コンテナーの作成][aks-windows]に関する記事を参照してください。

[近接配置グループ][reduce-latency-ppg]を使用して、AKS アプリケーションの待機時間を短縮します。

<!-- EXTERNAL LINKS -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubernetes-labels]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/
[kubernetes-label-syntax]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#syntax-and-character-set

<!-- INTERNAL LINKS -->
[aks-windows]: windows-container-cli.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-nodepool-add]: /cli/azure/aks/nodepool#az-aks-nodepool-add
[az-aks-nodepool-list]: /cli/azure/aks/nodepool#az-aks-nodepool-list
[az-aks-nodepool-update]: /cli/azure/aks/nodepool#az-aks-nodepool-update
[az-aks-nodepool-upgrade]: /cli/azure/aks/nodepool#az-aks-nodepool-upgrade
[az-aks-nodepool-scale]: /cli/azure/aks/nodepool#az-aks-nodepool-scale
[az-aks-nodepool-delete]: /cli/azure/aks/nodepool#az-aks-nodepool-delete
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[az-deployment-group-create]: /cli/azure/deployment/group#az_deployment_group_create
[gpu-cluster]: gpu-cluster.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[quotas-skus-regions]: quotas-skus-regions.md
[supported-versions]: supported-kubernetes-versions.md
[tag-limitation]: ../azure-resource-manager/management/tag-resources.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[vm-sizes]: ../virtual-machines/sizes.md
[use-system-pool]: use-system-pools.md
[ip-limitations]: ../virtual-network/virtual-network-ip-addresses-overview-arm#standard
[node-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[vmss-commands]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md#public-ipv4-per-virtual-machine
[az-list-ips]: /cli/azure/vmss.md#az-vmss-list-instance-public-ips
[reduce-latency-ppg]: reduce-latency-ppg.md
