---
title: Azure Kubernetes Service (AKS) で複数のノード プールを使用する
description: Azure Kubernetes Service (AKS) のクラスターで複数のノード プールを作成および管理する方法について学習します
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 08/9/2019
ms.author: mlearned
ms.openlocfilehash: f507619a1c8e80623a756b91f3fd6187283212f0
ms.sourcegitcommit: d614a9fc1cc044ff8ba898297aad638858504efa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74996733"
---
# <a name="create-and-manage-multiple-node-pools-for-a-cluster-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) のクラスターで複数のノード プールを作成および管理する

Azure Kubernetes Service (AKS) で同じ構成のノードは、*ノード プール*にグループ化できます。 これらのノード プールには、お使いのアプリケーションを実行する基になる VM が含まれています。 最初のノード数とそのサイズ (SKU) は、*既定のノード プール*が作成される AKS クラスターの作成時に定義します。 コンピューティングまたは記憶域の要件が異なるアプリケーションをサポートするには、ノード プールを追加作成します。 たとえば、これらの追加のノード プールを使用すると、コンピューティング集約型のアプリケーションに GPU を提供したり、高パフォーマンスな SSD ストレージにアクセスを提供したりできます。

> [!NOTE]
> この機能を使用すると、複数のノード プールを作成および管理する方法をより細かく制御できます。 そのため、作成/更新/削除には個別のコマンドが必要です。 以前は、`az aks create` または `az aks update` を介したクラスター操作は、managedCluster API を使用するものであり、コントロール プレーンと単一ノード プールを変更する唯一の方法でした。 この機能は、agentPool API を介した、エージェント プールに対する個別の操作セットを公開するものであり、個々のノード プールに対して操作を実行するには `az aks nodepool` コマンド セットを使用する必要があります。

この記事では、AKS クラスターで複数のノード プールを作成および管理する方法について説明します。

## <a name="before-you-begin"></a>開始する前に

Azure CLI バージョン 2.0.76 以降がインストールされて構成されている必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][install-azure-cli]に関するページを参照してください。

## <a name="limitations"></a>制限事項

複数のノード プールをサポートする AKS クラスターを作成および管理する場合には、次の制限があります。

* 既定の (最初の) ノード プールは削除できません。
* HTTP アプリケーションのルーティング アドオンは使用できません。
* AKS クラスターが複数のノード プールを使用するためには、Standard SKU のロード バランサーを使用する必要があります。Basic SKU のロード バランサーでは、この機能がサポートされません。
* AKS クラスターでは、ノードに仮想マシン スケール セットを使用する必要があります。
* ほとんどの操作と同様に、既存の Resource Manager テンプレートを使用してノード プールを追加したり、削除したりすることはできません。 代わりに、[別の Resource Manager テンプレートを使用](#manage-node-pools-using-a-resource-manager-template)して、AKS クラスター内のノード プールに変更を加えます。
* ノード プールの名前は、小文字の英数字のみを含めることができ、小文字で始める必要があります。 Linux ノード プールの場合、長さは 1 から 12 文字である必要があります。Windows ノード プールの場合、長さは 1 から 6 文字である必要があります。
* AKS クラスターは、最大で 8 つノード プールを持つことができます。
* AKS クラスターには、これらの 8 つのノード プールにまたがる最大 800 個のノードを含めることができます。
* すべてのノード プールは、同じサブネット内に存在する必要があります。

## <a name="create-an-aks-cluster"></a>AKS クラスターの作成

まず、1 つのノード プールで AKS クラスターを作成開始します。 次の例では、[az group create][az-group-create] コマンドを使用して、*myResourceGroup* という名前のリソース グループを *eastus* リージョンに作成しています。 次いで、*myAKSCluster* という名前の AKS クラスターを [az aks create][az-aks-create] コマンドを使用して作成しています。 次の手順では、*1.13.10* の *--kubernetes-version* を使用してノード プールを更新する方法を示しています。 [Kubernetes のサポートされている任意のバージョン][supported-versions]を指定できます。

> [!NOTE]
> 複数のノード プールを使用する場合、*Basic* ロード バランサー SKU は**サポートされません**。 既定では、AKS クラスターが、Azure CLI および Azure portal から *Standard* ロード バランサー SKU で作成されます。

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
    --kubernetes-version 1.13.10 \
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
    --node-count 3 \
    --kubernetes-version 1.12.7
```

> [!NOTE]
> ノード プールの名前は、小文字で始める必要があり、英数字のみを含めることができます。 Linux ノード プールの場合、長さは 1 から 12 文字である必要があります。Windows ノード プールの場合、長さは 1 から 6 文字である必要があります。

お使いのノード プールの状態を確認するには、[az aks node pool list][az-aks-nodepool-list] コマンドを使用し、次のようにお使いのリソース グループとクラスター名を指定します。

```azurecli-interactive
az aks nodepool list --resource-group myResourceGroup --cluster-name myAKSCluster
```

次の出力例では、*mynodepool* がノード プール内に 3 つのノードと共に正常に作成されたことを示しています。 前の手順で AKS クラスターを作成したとき、既定の *nodepool1* がノード数 *2* で作成されました。

```console
$ az aks nodepool list --resource-group myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 3,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.12.7",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.13.10",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

> [!TIP]
> ノード プールを追加したとき、*OrchestratorVersion* または *VmSize* を指定していない場合、それらのノードは AKS クラスターの既定を使用して作成されます。 この例のそれは、Kubernetes バージョン *1.13.10* とノード サイズ *Standard_DS2_v2* です。

## <a name="upgrade-a-node-pool"></a>ノード プールのアップグレード
 
> [!NOTE]
> クラスターまたはノード プールでは、アップグレードやスケーリングの操作を同時に実行することはできず、実行を試みた場合には、エラーが返されます。 代わりに、ターゲット リソースに対する次の要求を行う前に、各操作の種類をその同じリソースで完了する必要があります。 詳細については、[トラブルシューティング ガイド](https://aka.ms/aks-pending-upgrade)を参照してください。

最初の手順で AKS クラスターを初回作成したとき、`--kubernetes-version` として *1.13.10* を指定しました。 これにより、コントロール プレーンと既定ノード プールの両方の Kubernetes バージョンが設定されます。 このセクションのコマンドからは、1 つの特定のノード プールをアップグレードする方法が説明されます。

コントロール プレーンとノード プールの Kubernetes バージョンをアップグレードするとき、両者の関係については、[下のセクション](#upgrade-a-cluster-control-plane-with-multiple-node-pools)で説明します。

> [!NOTE]
> ノード プールの OS イメージのバージョンは、クラスターの Kubernetes バージョンに関連付けられています。 OS イメージは、クラスターのアップグレードの後でのみアップグレードされます。

この例には 2 つのノード プールがあるため、[az aks nodepool upgrade][az-aks-nodepool-upgrade] を使用してノード プールをアップグレードする必要があります。 *mynodepool* を Kubernetes *1.13.10* にアップグレードしましょう。 次の例のように、[az aks node pool upgrade][az-aks-nodepool-upgrade] コマンドを使用してノード プールをアップグレードします。

```azurecli-interactive
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --kubernetes-version 1.13.10 \
    --no-wait
```

[az aks node pool list][az-aks-nodepool-list] コマンドを使用し、再度お使いのノード プールの状態を一覧表示します。 次の例では、*mynodepool* が *1.13.10* への *Upgrading* 状態であることを示しています。

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 3,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.13.10",
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
    "orchestratorVersion": "1.13.10",
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

AKS コントロール プレーンをアップグレードするには、`az aks upgrade` を使用する必要があります。 これにより、コントロール プレーンのバージョンとクラスター内のすべてのノード プールがアップグレードされます。 

`--control-plane-only` フラグを使用して `az aks upgrade` コマンドを実行すると、クラスターのコントロール プレーンのみがアップグレードされます。 クラスター内の関連付けられているノード プールは一切変更されません。

個々のノード プールをアップグレードするには、`az aks nodepool upgrade` を使用する必要があります。 この場合は、指定された Kubernetes バージョンのターゲット ノード プールのみがアップグレードされます

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

```console
$ az aks nodepool list -g myResourceGroupPools --cluster-name myAKSCluster

[
  {
    ...
    "count": 5,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.13.10",
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
    "orchestratorVersion": "1.13.10",
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

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 5,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.13.10",
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
    "orchestratorVersion": "1.13.10",
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

前のノード プールの作成例では、クラスターに作成したノードに、既定の VM サイズを使用しました。 より一般的なシナリオでは、さまざまなサイズおよび性能の VM のノード プールが作成されます。 たとえば、大きな CPU またはメモリのノードが含まれるノード プール、または GPU をサポートするノード プールが作成されます。 次の手順では、[taints と tolerations を使用](#schedule-pods-using-taints-and-tolerations)して、Kubernetes スケジューラにこれらのノードで実行されるポッドに対するアクセスを制限する方法を通知します。

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

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 1,
    ...
    "name": "gpunodepool",
    "orchestratorVersion": "1.13.10",
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
    "orchestratorVersion": "1.13.10",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

*gpunodepool* が正常に作成されるには、数分かかります。

## <a name="schedule-pods-using-taints-and-tolerations"></a>taints と tolerations を使用したポッドのスケジュール

現在、最初に作成した既定のノード プールと、GPU ベースのノード プールの 2 つのノード プールがお使いのクラスターにあります。 [kubectl get nodes][kubectl-get] コマンドを使用すると、お使いのクラスターのノードを参照できます。 次の出力例では、ノードが示されています。

```console
$ kubectl get nodes

NAME                                 STATUS   ROLES   AGE     VERSION
aks-gpunodepool-28993262-vmss000000  Ready    agent   4m22s   v1.13.10
aks-nodepool1-28993262-vmss000000    Ready    agent   115m    v1.13.10
```

Kubernetes スケジューラでは、テイントと容認を使用して、ノードで実行できるワークロードを制限できます。

* **テイント**は、ノードに適用されて、特定のポッドのみをそのノードでスケジュールできることを示します。
* **容認**は、ポッドに適用されて、ポッドがノードのテイントを "*許容する*" ことを許可します。

Kubernetes での高度なスケジューラ機能の詳細については、「[Azure Kubernetes Service (AKS) での高度なスケジューラ機能に関するベスト プラクティス][taints-tolerations]」を参照してください。

この例では、[kubectl taint node][kubectl-taint] コマンドを使用してお使いの GPU ベースのノードに taint を適用しています。 前の `kubectl get nodes` コマンドからお使いの GPU ベースのノードの名前を指定します。 taint が *key:value*、次いでスケジューリング オプションとして適用されます。 次の例では *sku=gpu* の組み合わせを使用してポッドを定義しています。されない場合、*NoSchedule* 機能を持つことになります。

```console
kubectl taint node aks-gpunodepool-28993262-vmss000000 sku=gpu:NoSchedule
```

次の YAML マニフェストの基本例では、Kubernetes スケジューラが GPU ベースのノードで NGINX ポッドを実行できるように toleration を使用しています。 MNIST データセットに対して Tensorflow ジョブを実行する、より適切な、しかし時間がかかる例については、「[Azure Kubernetes Service (AKS) でコンピューティングを集中的に使用するワークロードに GPU を使用する][gpu-cluster]」を参照してください。

`gpu-toleration.yaml` という名前のファイルを作成し、次の例の YAML にコピーします。

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - image: nginx:1.15.9
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

`kubectl apply -f gpu-toleration.yaml` コマンドを使用してポッドをスケジュールします。

```console
kubectl apply -f gpu-toleration.yaml
```

ポッドのスケジュールおよび NGINX イメージのプルには、数秒かかります。 [kubectl describe pod][kubectl-describe] コマンドを使用して、ポッドの状態を参照します。 次の縮約された出力例では、*sku = gpu:NoSchedule* toleration を適用しています。 次のように、イベント セクションでは、スケジューラがポッドを *aks-gpunodepool-28993262-vmss000000* GPU ベース ノードに割り当てています。

```console
$ kubectl describe pod mypod

[...]
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
                 sku=gpu:NoSchedule
Events:
  Type    Reason     Age    From                                          Message
  ----    ------     ----   ----                                          -------
  Normal  Scheduled  4m48s  default-scheduler                             Successfully assigned default/mypod to aks-gpunodepool-28993262-vmss000000
  Normal  Pulling    4m47s  kubelet, aks-gpunodepool-28993262-vmss000000  pulling image "nginx:1.15.9"
  Normal  Pulled     4m43s  kubelet, aks-gpunodepool-28993262-vmss000000  Successfully pulled image "nginx:1.15.9"
  Normal  Created    4m40s  kubelet, aks-gpunodepool-28993262-vmss000000  Created container
  Normal  Started    4m40s  kubelet, aks-gpunodepool-28993262-vmss000000  Started container
```

この taint が適用されたポッドのみが、*gpunodepool* のノードにスケジュールできます。 その他のポッドは、*nodepool1* ノード プールにスケジュールされます。 ノード プールを追加作成した場合、追加の taints と tolerations を使用して、それらのノード リソースにどのようなポッドをスケジュールするか制限できます。

## <a name="manage-node-pools-using-a-resource-manager-template"></a>Resource Manager テンプレートを使用したノード プールの管理

作成する Azure Resource Manager テンプレートと管理対象リソースを使用する場合、通常、テンプレート内の設定を更新し、再デプロイしてリソースを更新できます。 AKS 内のノード プールでは、AKS クラスターが作成されると、初期ノード プール プロファイルは更新できません。 この動作は、既存の Resource Manager テンプレートの更新、ノード プールへの変更、再デプロイが行えないことを意味します。 代わりに、既存の AKS クラスターのノード プールのみを更新する別の Resource Manager テンプレートを作成する必要があります。

`aks-agentpools.json` などのテンプレートを作成し、次のマニフェスト例を貼り付けます。 このテンプレートの例は、次の設定を構成します。

* 3 つのノードを実行するように、*myagentpool* という *Linux* ノード プールを更新します。
* Kubernetes バージョン *1.13.10* を実行するように、ノード プール内のノードを設定します。
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
            "aks": "2019-04-01"
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
            "apiVersion": "2019-04-01",
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
                "orchestratorVersion": "1.13.10"
            }
        }
    ]
}
```

次の例で示すように、[az group deployment create][az-group-deployment-create] コマンドを使用してこのテンプレートをデプロイします。 既存の AKS クラスターの名前と場所を求められます。

```azurecli-interactive
az group deployment create \
    --resource-group myResourceGroup \
    --template-file aks-agentpools.json
```

Resource Manager テンプレートで定義するノード プール設定および操作に応じて、AKS クラスターの更新には数分かかる場合があります。

## <a name="assign-a-public-ip-per-node-in-a-node-pool"></a>ノード プール内のノードごとにパブリック IP を割り当てる

> [!WARNING]
> ノードごとにパブリック IP を割り当てるとき、プレビュー中、その IP は *AKS の Standard Load Balancer* で使用できません。ロード バランサーの規則と VM プロビジョニングが競合する可能性があるためです。 プレビューの間、ノードごとにパブリック IP を割り当てる必要がある場合は、*Basic Load Balancer SKU* を使用する必要があります。

AKS ノードは、通信用に独自のパブリック IP アドレスを必要としません。 ただし、一部のシナリオでは、ノード プール内のノードが独自のパブリック IP アドレスを備えることが必要な場合があります。 たとえば、ゲームで、ホップを最小限にするためにクラウド仮想マシンにコンソールが直接接続する必要がある場合です。 これは、別のプレビュー機能であるノード パブリック IP (プレビュー) に登録することで実現できます。

```azurecli-interactive
az feature register --name NodePublicIPPreview --namespace Microsoft.ContainerService
```

登録が正常に完了したら、[上記](#manage-node-pools-using-a-resource-manager-template)と同じ手順に従って Azure Resource Manager テンプレートをデプロイし、ブール値プロパティ `enableNodePublicIP` を agentPoolProfiles に追加します。 指定しない場合は、既定で `false` として設定されるため、これを `true` に設定します。 これは作成時限定のプロパティであり、2019-06-01 の最小 API バージョンが必要です。 これは、Linux と Windows のどちらのノード プールにも適用できます。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

この記事では、GPU ベースのノードを含む AKS クラスターを作成しました。 不要なコストを減らすには、*gpunodepool*、または AKS クラスター全体を削除してください。

GPU ベースのノード プールを削除するには、次の例のように [az aks nodepool delete][az-aks-nodepool-delete] コマンドを使用します。

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name gpunodepool
```

クラスター自体を削除するには、[az group delete][az-group-delete] コマンドを使用して AKS リソース グループを削除します。

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>次の手順

この記事では、AKS クラスターで複数のノード プールを作成および管理する方法を学習しました。 すべてのノード プールのポッドを制御する方法の詳細については、「[Azure Kubernetes Service (AKS) での高度なスケジューラ機能に関するベスト プラクティス][operator-best-practices-advanced-scheduler]」を参照してください。

Windows Server コンテナー ノード プールを作成して使用するには、[AKS での Windows Server コンテナーの作成][aks-windows]に関する記事を参照してください。

<!-- EXTERNAL LINKS -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

<!-- INTERNAL LINKS -->
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-nodepool-add]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-add
[az-aks-nodepool-list]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-list
[az-aks-nodepool-upgrade]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-upgrade
[az-aks-nodepool-scale]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-scale
[az-aks-nodepool-delete]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-delete
[vm-sizes]: ../virtual-machines/linux/sizes.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[gpu-cluster]: gpu-cluster.md
[az-group-delete]: /cli/azure/group#az-group-delete
[install-azure-cli]: /cli/azure/install-azure-cli
[supported-versions]: supported-kubernetes-versions.md
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-windows]: windows-container-cli.md
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
