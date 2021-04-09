---
title: Azure Kubernetes Service (AKS) でのクラスター オートスケーラーの使用
description: Azure Kubernetes Service (AKS) クラスターでのアプリケーションの需要を満たすように、クラスター オートスケーラーを使用してクラスターを自動的にスケーリングする方法について説明します。
services: container-service
ms.topic: article
ms.date: 07/18/2019
ms.openlocfilehash: 9caf56545efc6aefae525e28614d39705c00c21e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101742570"
---
# <a name="automatically-scale-a-cluster-to-meet-application-demands-on-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) でのアプリケーションの需要を満たすようにクラスターを自動的にスケーリング

Azure Kubernetes Service (AKS) のアプリケーションの需要に対応するには、ワークロードを実行するノードの数を調整する必要が生じる場合があります。 クラスター オートスケーラー コンポーネントは、リソース制約のためにスケジュールできないクラスター内のポッドを監視できます。 問題が検出されると、アプリケーションの需要を満たすためにノード プール内のノードの数が増やされます。 また、実行ポッドの不足について定期的にノードがチェックされ、必要に応じてノードの数が減らされます。 AKS クラスター内のノードの数を自動的に増減するこの機能を使用すると、効率的で、コスト効率の高いクラスターを実行できます。

この記事では、AKS クラスターでクラスター オートスケーラーを有効にして管理する方法について説明します。

## <a name="before-you-begin"></a>開始する前に

この記事では、Azure CLI バージョン 2.0.76 以降を実行している必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][azure-cli-install]に関するページを参照してください。

## <a name="about-the-cluster-autoscaler"></a>クラスター オートスケーラーについて

平日と夜間、または週末など、アプリケーションの変則的な需要に対応するために、多くの場合、クラスターには自動的にスケーリングする方法が必要になります。 AKS クラスターは、次の 2 つの方法のいずれかでスケーリングできます。

* **クラスター オートスケーラー** は、リソース制約のためにノードでスケジュールできないポッドを監視します。 その後、クラスターによってノードの数が自動的に増やされます。
* **ポッドの水平オートスケーラー** は、Kubernetes クラスターのメトリック サーバーを使用して、ポッドのリソースの需要をモニターします。 アプリケーションで必要なリソースが増えると、その需要を満たすためにポッドの数が自動的に増やされます。

![クラスター オートスケーラーとポッドの水平オートスケーラーは、必要なアプリケーション需要に対応するために、多くの場合、連携して機能します。](media/autoscaler/cluster-autoscaler.png)

また、ポッドの水平オートスケーラーとクラスター オートスケーラーでは、必要に応じてポッドとノードの数を減らすこともできます。 クラスター オートスケーラーは、一定期間未使用の容量があった場合、ノードの数を減らします。 クラスター オートスケーラーによって削除されるノード上のポッドは、クラスター内の他の場所で安全にスケジュールされます。 クラスター オートスケーラーは、以下の状況のように、ポッドが移動できない場合はスケールダウンできないことがあります。

* ポッドが直接作成されており、デプロイやレプリカ セットなどのコントローラー オブジェクトによってサポートされていない。
* Pod Disruption Budget (PDB) の制限が非常に厳しく、ポッドの数が特定のしきい値を下回ることが許可されていない。
* ポッドが、別のノードでスケジュールされた場合に適用できないノード セレクターまたはアンチ アフィニティーを使用している。

クラスター オートスケーラーでスケールダウンを行えない場合がある状況について詳しくは、「[What types of pods can prevent the cluster autoscaler from removing a node?][autoscaler-scaledown]」 (クラスター オートスケーラーがノードから削除されるのを防止できるのは、どの種類のポッドですか?) を参照してください。

クラスター オートスケーラーは、スケール イベント間の時間間隔やリソースしきい値などに対して開始パラメーターを使用します。 クラスター オートスケーラーが使用するパラメーターの詳細については、「[オートスケーラー プロファイルの使用](#using-the-autoscaler-profile)」を参照してください。

クラスター オートスケーラーとポッドの水平オートスケーラーは連携して機能し、多くの場合、両方のオートスケーラーが一緒に 1 つのクラスターにデプロイされます。 この 2 つを組み合わせて使用する場合、ポッドの水平オートスケーラーの主な役割は、アプリケーションの需要を満たすために必要な数のポッドを実行することになります。 クラスター オートスケーラーの主な役割は、スケジュールされたポッドをサポートするために必要な数のノードを実行することになります。

> [!NOTE]
> クラスター オートスケーラーを使用する場合、手動スケーリングは無効になります。 必要なノード数は、クラスター オートスケーラーによって決定されるようにします。 クラスターを手動でスケーリングする場合は、[クラスター オートスケーラーを無効化](#disable-the-cluster-autoscaler)します。

## <a name="create-an-aks-cluster-and-enable-the-cluster-autoscaler"></a>AKS クラスターの作成とクラスター オートスケーラーの有効化

AKS クラスターを作成する必要がある場合は、[az aks create][az-aks-create] コマンドを使用します。 クラスターのノード プール上でクラスター オートスケーラーを有効にして構成するには、 `--enable-cluster-autoscaler` パラメーターを使用し、ノードの `--min-count` と `--max-count` を指定します。

> [!IMPORTANT]
> クラスター オートスケーラーは、Kubernetes のコンポーネントです。 AKS クラスターは、ノードに仮想マシン スケール セットを使用しますが、Azure portal で、または Azure CLI を使用して、スケール セットの自動スケーリングの設定を手動で有効にしたり編集したりしないでください。 必要なスケール設定の管理は、Kubernetes クラスター オートスケーラーが行います。 詳細については、[ノード リソース グループ内の AKS リソースを変更可能かどうか][aks-faq-node-resource-group]に関するセクションを参照してください。

次の例では、仮想マシンのスケール セットによって戻された 1 つのノード プールを使用して、AKS クラスターを作成します。 また、クラスターのノードプール上でクラスター オートスケーラーを有効にし、最小値を *1* ノード、最大値を *3* ノードに設定します。

```azurecli-interactive
# First create a resource group
az group create --name myResourceGroup --location eastus

# Now create the AKS cluster and enable the cluster autoscaler
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 1 \
  --vm-set-type VirtualMachineScaleSets \
  --load-balancer-sku standard \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

このクラスターを作成して、クラスター オートスケーラーの設定を構成するには数分かかります。

## <a name="update-an-existing-aks-cluster-to-enable-the-cluster-autoscaler"></a>既存の AKS クラスターを更新してクラスター オートスケーラーを有効にする

既存のクラスターのノード プール上でクラスター オートスケーラーを有効にして構成するには、[az aks update][az-aks-update] コマンドを使用します。 `--enable-cluster-autoscaler` パラメーターを使用し、ノードの `--min-count` と `--max-count` を指定します。

> [!IMPORTANT]
> クラスター オートスケーラーは、Kubernetes のコンポーネントです。 AKS クラスターは、ノードに仮想マシン スケール セットを使用しますが、Azure portal で、または Azure CLI を使用して、スケール セットの自動スケーリングの設定を手動で有効にしたり編集したりしないでください。 必要なスケール設定の管理は、Kubernetes クラスター オートスケーラーが行います。 詳細については、[ノード リソース グループ内の AKS リソースを変更可能かどうか][aks-faq-node-resource-group]に関するセクションを参照してください。

次の例では、既存の AKS クラスターを更新して、クラスターのノードプール上でクラスター オートスケーラーを有効にし、最小値を *1* ノード、最大値を *3* ノードに設定しています。

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

このクラスターを更新してクラスター オートスケーラーの設定を構成するには、数分かかります。

## <a name="change-the-cluster-autoscaler-settings"></a>クラスター オートスケーラーの設定の変更

> [!IMPORTANT]
> AKS クラスターに複数のノード プールがある場合は、[複数のエージェント プールを使用した自動スケーリングのセクション](#use-the-cluster-autoscaler-with-multiple-node-pools-enabled)に進んでください。 複数のエージェント プールがあるクラスターの場合は、`az aks` ではなく `az aks nodepool` コマンド セットを使用して、ノード プール固有のプロパティを変更する必要があります。

AKS クラスターを作成するか既存のノード プールを更新する前述のステップで、クラスター オートスケーラーの最小ノード数は *1* に設定され、最大ノード数は *3* に設定されました。 アプリケーション需要の変化に応じて、クラスター オートスケーラーのノード数の調整が必要になる場合があります。

ノード数を変更するには、[az aks update][az-aks-update] コマンドを使用します。

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

上の例では、*myAKSCluster* の単一のノード プールにあるクラスター オートスケーラーを更新して、最小値を *1* ノード、最大値を *5* ノードにします。

> [!NOTE]
> クラスター オートスケーラーでは、各ノード プールに設定された最小数と最大数に基づいてスケーリングが決定されますが、最小数または最大数が更新された後にそれが適用されません。 たとえば、現在のノード数が 3 のときに最小値を 5 に設定しても、プールはすぐに 5 にはスケールアップされません。 ノード プールの最小値が現在のノード数よりも大きい値になると、2 つの新しいノードを必要とするほど十分な数の、オートスケーラー イベントをトリガーするスケジュールできないポッドが存在するときに、この新しい最小または最大の設定が適用されます。 スケール イベントの後、新しい数の制限が適用されます。

アプリケーションとサービスのパフォーマンスをモニターして、必要なパフォーマンスに一致するようにクラスター オートスケーラーのノード数を調整してください。

## <a name="using-the-autoscaler-profile"></a>オートスケーラー プロファイルの使用

クラスター全体のオートスケーラー プロファイルで既定値を変更することで、クラスター オートスケーラーの詳細をよりきめ細かに構成することもできます。 たとえばスケールダウン イベントは、ノードの使用率が低い状態で 10 分経過した後に発生します。 15 分ごとに実行されたワークロードがある場合は、15 分または 20 分経過後に使用率が低いノードをスケールダウンするようにオートスケーラー プロファイルを変更できます。 クラスター オートスケーラーを有効にすると、異なる設定を指定しない限り既定のプロファイルが使用されます。 クラスター オートスケーラー プロファイルでは、以下の設定を更新できます。

| 設定                          | 説明                                                                              | 既定値 |
|----------------------------------|------------------------------------------------------------------------------------------|---------------|
| scan-interval                    | スケールアップまたはスケールダウンに関してクラスターが再評価される頻度                                    | 10 秒    |
| scale-down-delay-after-add       | スケールアップ後に、スケールダウンの評価が再開されるまでの時間                               | 10 分    |
| scale-down-delay-after-delete    | ノードの削除後に、スケールダウンの評価が再開されるまでの時間                          | scan-interval |
| scale-down-delay-after-failure   | スケールダウンの失敗後に、スケールダウンの評価が再開されるまでの時間                     | 3 分     |
| scale-down-unneeded-time         | ノードが不要になってからスケールダウンの対象になるまでの時間                  | 10 分    |
| scale-down-unready-time          | 準備ができていないノードが不要になってからスケールダウンの対象になるまでの時間         | 20 分    |
| scale-down-utilization-threshold | 要求されたリソースの合計を容量で割った値として定義される、ノード利用レベル。これを下回るノードはスケールダウンの対象と見なすことができます。 | 0.5 |
| max-graceful-termination-sec     | ノードのスケールダウンを試みるときに、クラスター オートスケーラーがポッドの終了を待機する最大秒数 | 600 秒   |
| balance-similar-node-groups      | 類似のノード プールを検出し、その間でノード数のバランスを取ります                 | false         |
| expander                         | スケールアップで使用するノード プール [expander](https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-expanders) の種類 指定できる値: `most-pods`、`random`、`least-waste`、`priority` | random | 
| skip-nodes-with-local-storage    | true の場合、EmptyDir や HostPath などのローカル ストレージを備えたポッドがあるノードは、クラスター オートスケーラーによって削除されなくなります | true |
| skip-nodes-with-system-pods      | true の場合、ポッドのあるノードは、クラスター オートスケーラーによって kube-system から削除されなくなります (DaemonSet またはミラー ポッドを除く) | true | 
| max-empty-bulk-delete            | 同時に削除できる空ノードの最大数                       | 10 ノード      |
| new-pod-scale-up-delay           | バーストまたはバッチ スケールのように、Kubernetes スケジューラによってすべてのポッドがスケジュールされる前に CA を動作させたくないシナリオの場合、一定の期間に達する前のスケジュールされていないポッドを無視するように CA に指示できます。                                                                                                                | 0 秒    |
| max-total-unready-percentage     | クラスター内の準備が完了していないノードの最大割合。 この割合を超えると、CA の動作は停止されます | 45% |
| max-node-provision-time          | オートスケーラーがノードがプロビジョニングされるのを待機する最大時間                           | 約 15 分    |   
| ok-total-unready-count           | max-total-unready-percentage に関係なく、準備が完了していないノードの許可されている数            | 3 ノード       |

> [!IMPORTANT]
> クラスター オートスケーラー プロファイルは、クラスター オートスケーラーを使用するすべてのノード プールに影響を及ぼします。 ノード プールごとにオートスケーラー プロファイルを設定することはできません。
>
> クラスター オートスケーラー プロファイルには、バージョン *2.11.1* 以上の Azure CLI が必要です。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][azure-cli-install]に関するページを参照してください。

### <a name="set-the-cluster-autoscaler-profile-on-an-existing-aks-cluster"></a>既存の AKS クラスターに対してクラスター オートスケーラー プロファイルを設定する

*cluster-autoscaler-profile* パラメーターを指定して [az aks update][az-aks-update-preview] コマンドを使用し、クラスターに対してクラスター オートスケーラー プロファイルを設定します。 次の例では、プロファイルでのスキャン間隔の設定を 30 秒として構成しています。

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --cluster-autoscaler-profile scan-interval=30s
```

クラスター内のノード プールに対してクラスター オートスケーラーを有効にすると、それらのクラスターでもクラスター オートスケーラー プロファイルが使用されます。 次に例を示します。

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name mynodepool \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

> [!IMPORTANT]
> クラスター オートスケーラー プロファイルを設定すると、クラスター オートスケーラーが有効にされた既存のノード プールでは、すぐにプロファイルの使用が開始されます。

### <a name="set-the-cluster-autoscaler-profile-when-creating-an-aks-cluster"></a>AKS クラスター作成時にクラスター オートスケーラー プロファイルを設定する

クラスターを作成するときに *cluster-autoscaler-profile* パラメーターを使用することもできます。 次に例を示します。

```azurecli-interactive
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 1 \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3 \
  --cluster-autoscaler-profile scan-interval=30s
```

上のコマンドでは、AKS クラスターを作成し、クラスター全体のオートスケーラー プロファイルのスキャン間隔を 30 秒と定義しています。 また、このコマンドでは、初期ノード プールに対してクラスター オートスケーラーを有効にし、最小ノード数を 1 に、最大ノード数を 3 に設定しています。

### <a name="reset-cluster-autoscaler-profile-to-default-values"></a>クラスター オートスケーラー プロファイルを既定値にリセットする

クラスターに対するクラスター オートスケーラー プロファイルをリセットするには、[az aks update][az-aks-update-preview] コマンドを使用します。

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --cluster-autoscaler-profile ""
```

## <a name="disable-the-cluster-autoscaler"></a>クラスター オートスケーラーの無効化

クラスター オートスケーラーを今後使用しない場合は、[az aks update][az-aks-update-preview] コマンドで `--disable-cluster-autoscaler` パラメーターを指定することで無効にできます。 クラスター オートスケーラーが無効になってもノードは削除されません。

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --disable-cluster-autoscaler
```

クラスター オートスケーラーを無効にした後、[az aks scale][az-aks-scale] コマンドを使用して、クラスターを手動でスケーリングできます。 ポッドの水平オートスケーラーを使用している場合、その機能はクラスター オートスケーラーを無効にしても引き続き実行されますが、ノード リソースがすべて使用中になると、ポッドをスケジュールできなくなる可能性があります。

## <a name="re-enable-a-disabled-cluster-autoscaler"></a>無効なクラスター オートスケーラーを再度有効にする

既存のクラスター上でクラスター オートスケーラーを再度有効にする場合は、[az aks update][az-aks-update-preview] コマンドで `--enable-cluster-autoscaler`、`--min-count`、および `--max-count` のパラメーターを指定することで再度有効にすることができます。

## <a name="retrieve-cluster-autoscaler-logs-and-status"></a>クラスター オートスケーラーのログと状態を取得する

オートスケーラーのイベントを診断し、デバッグする目的で、オートスケーラー アドオンからログと状態を取得できます。

AKS では、ユーザーに代わってクラスター オートスケーラーが管理され、マネージド コントロール プレーンで実行されます。 コントロール プレーン ノードを有効にすると、CA からのログと操作を確認できます。

クラスター オートスケーラーから Log Analytics にプッシュされるようにログを構成するには、次の手順に従います。

1. Log Analytics にクラスター オートスケーラーのログをプッシュするようにリソース ログのルールを設定します。 [手順の詳細はこちらにあります][aks-view-master-logs]。[ログ] のオプションを選択するときは、確実に `cluster-autoscaler` のボックスにチェックマークを入れます。
1. Azure portal から、クラスターの [ログ] セクションを選択します。
1. Log Analytics に次のサンプル クエリを入力します。

```
AzureDiagnostics
| where Category == "cluster-autoscaler"
```

取得するログがあれば、次の例のようなログが表示されるはずです。

![Log Analytics のログ](media/autoscaler/autoscaler-logs.png)

クラスター オートスケーラーにより、`cluster-autoscaler-status` という名前の `configmap` に正常性状態も書き込まれます。 これらのログを取得するには、次の `kubectl` コマンドを実行します。 クラスター オートスケーラーで構成されたノード プールごとに、正常性状態が報告されます。

```
kubectl get configmap -n kube-system cluster-autoscaler-status -o yaml
```

オートスケーラーからログに記録される内容については、[Kubernetes/オートスケーラー GitHub プロジェクト][kubernetes-faq]の FAQ をお読みください。

## <a name="use-the-cluster-autoscaler-with-multiple-node-pools-enabled"></a>複数のノード プールを有効にしてクラスター オートスケーラーを使用する

クラスター オートスケーラーは、[複数のノード プール][aks-multiple-node-pools]を有効にして使用できます。 そのドキュメントに従って、既存のクラスターで複数のノード プールを有効にし、ノード プールを追加する方法を確認してください。 両方の機能を一緒に使用すると、クラスター内の個々のノード プールでクラスター オートスケーラーを有効にし、それぞれに一意の自動スケーリング ルールを渡すことができます。

次のコマンドは、このドキュメントで前述した [初期手順](#create-an-aks-cluster-and-enable-the-cluster-autoscaler)に従っており、既存のノード プールの最大数を *3* から *5* に更新することを前提としています。 既存のノード プールの設定を更新するには、[az aks nodepool update][az-aks-nodepool-update] コマンドを使用します。

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name nodepool1 \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

クラスター オートスケーラーは、[az aks nodepool update][az-aks-nodepool-update] で `--disable-cluster-autoscaler` パラメーターを渡すことで無効にすることができます。

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name nodepool1 \
  --disable-cluster-autoscaler
```

既存のクラスター上でクラスター オートスケーラーを再度有効にする場合は、[az aks nodepool update][az-aks-nodepool-update] コマンドで `--enable-cluster-autoscaler`、 `--min-count`、および `--max-count` パラメーターを指定することで有効にできます。

> [!NOTE]
> 複数のゾーンにまたがるノード プールでクラスター オートスケーラーを使用して、ボリューム トポロジ スケジュール設定など、ゾーンに関連するスケジュール機能を活用する予定の場合は、ゾーンごとに 1つのノード プールを用意し、オートスケーラー プロファイルを使用して `--balance-similar-node-groups` を有効にすることをお勧めします。 これにより、オートスケーラーが正常にスケールアップし、ノード プールのサイズが均衡を保つようになります。

## <a name="next-steps"></a>次のステップ

この記事では、AKS ノードの数を自動的にスケーリングする方法について説明します。 また、ポッドの水平オートスケーラーを使用して、アプリケーションを実行するポッドの数を自動的に調整することもできます。 ポッドの水平オートスケーラーの使用手順については、「[AKS でのアプリケーションのスケーリング][aks-scale-apps]」を参照してください。

<!-- LINKS - internal -->
[aks-faq]: faq.md
[aks-faq-node-resource-group]: faq.md#can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group
[aks-multiple-node-pools]: use-multiple-node-pools.md
[aks-scale-apps]: tutorial-kubernetes-scale.md
[aks-support-policies]: support-policies.md
[aks-upgrade]: upgrade-cluster.md
[aks-view-master-logs]: ./view-control-plane-logs.md#enable-resource-logs
[autoscaler-profile-properties]: #using-the-autoscaler-profile
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-update]: /cli/azure/aks#az-aks-update
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register

<!-- LINKS - external -->
[az-aks-update-preview]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview
[az-aks-nodepool-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview#enable-cluster-auto-scaler-for-a-node-pool
[autoscaler-scaledown]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-types-of-pods-can-prevent-ca-from-removing-a-node
[autoscaler-parameters]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca
[kubernetes-faq]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#ca-doesnt-work-but-it-used-to-work-yesterday-why