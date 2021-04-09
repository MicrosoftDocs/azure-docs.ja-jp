---
title: Azure Kubernetes Service (AKS) クラスターにスポット ノード プールを追加する
description: Azure Kubernetes Service (AKS) クラスターにスポット ノード プールを追加する方法について説明します。
services: container-service
ms.service: container-service
ms.topic: article
ms.date: 10/19/2020
ms.openlocfilehash: 7f838b2a78f1c6993aa247f2944d4f2a9b1e9556
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102181127"
---
# <a name="add-a-spot-node-pool-to-an-azure-kubernetes-service-aks-cluster"></a>Azure Kubernetes Service (AKS) クラスターにスポット ノード プールを追加する

スポット ノード プールは、[スポット仮想マシン スケール セット][vmss-spot]によってサポートされるノード プールです。 AKS クラスターのノードにスポット VM を使用すると、Azure の未使用の容量を利用できるため、コストが大幅に削減されます。 使用可能な未使用の容量は、ノード サイズ、リージョン、時刻などの多くの要因によって異なります。

スポット ノード プールをデプロイすると、使用可能な容量が存在する場合、Azure ではスポット ノードが割り当てられます。 ただし、スポット ノードのための SLA は存在しません。 スポット ノード プールをサポートするスポット スケール セットは 1 つの障害ドメインにデプロイされ、高可用性の保証は提供されません。 Azure に元の容量が必要になったらいつでも、Azure インフラストラクチャはスポット ノードを排除します。

スポット ノードは、中断、早期終了、または排除を処理できるワークロードに最適です。 たとえば、バッチ処理ジョブ、開発およびテスト環境、大規模なコンピューティング ワークロードなどのワークロードは、スポット ノード プールでスケジュールする良い候補になる可能性があります。

この記事では、既存の Azure Kubernetes Service (AKS) クラスターにセカンダリ スポット ノード プールを追加します。

この記事は、Kubernetes および Azure Load Balancer の基本的な概念を理解していることを前提としています。 詳細については、「[Azure Kubernetes Services (AKS) における Kubernetes の中心概念][kubernetes-concepts]」を参照してください。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="before-you-begin"></a>開始する前に

スポット ノード プールを使用するクラスターを作成する場合は、そのクラスターも、ノード プールと *Standard* SKU ロード バランサーに仮想マシン スケール セットを使用する必要があります。 また、スポット ノード プールを使用するクラスターを作成した後、さらにノード プールを追加することも必要です。 ノード プールの追加についてはこの後の手順で説明されます。

この記事では、Azure CLI バージョン 2.14 以降を実行している必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][azure-cli-install]に関するページを参照してください。

### <a name="limitations"></a>制限事項

スポット ノード プールを含む AKS クラスターを作成および管理する場合は、次の制限が適用されます。

* スポット ノード プールをクラスターの既定のノード プールにすることはできません。 スポット ノード プールはセカンダリ プールとしてのみ使用できます。
* スポット ノード プールは切断とドレインを保証できないため、スポット ノード プールをアップグレードすることはできません。 Kubernetes バージョンのアップグレードなどの操作を行うには、既存のスポット ノード プールを新しいノード プールに置き換える必要があります。 スポット ノード プールを置き換えるには、別のバージョンの Kubernetes で新しいスポット ノード プールを作成し、その状態が *[準備完了]* になるまで待ってから古いノード プールを削除します。
* コントロール プレーンとノード プールを一度にアップグレードすることはできません。 個別にアップグレードするか、またはスポット ノード プールを削除して、コントロール プレーンと残りのノード プールを一度にアップグレードする必要があります。
* スポット ノード プールは、仮想マシン スケール セットを使用する必要があります。
* 作成後に ScaleSetPriority または SpotMaxPrice を変更することはできません。
* SpotMaxPrice を設定するときは、この値を -1 か、または小数点以下最大 5 桁の正の値にする必要があります。
* スポット ノード プールにはラベル *kubernetes.azure.com/scalesetpriority:spot*、taint *kubernetes.azure.com/scalesetpriority=spot:NoSchedule* が割り当てられ、システム ポッドにはアンチアフィニティが割り当てられます。
* スポット ノード プールでワークロードをスケジュールするには、[対応する toleration][spot-toleration] を追加する必要があります。

## <a name="add-a-spot-node-pool-to-an-aks-cluster"></a>AKS クラスターにスポット ノード プールを追加する

複数のノード プールが有効になっている既存のクラスターにスポット ノード プールを追加する必要があります。 複数のノード プールを含む AKS クラスターの作成の詳細については、[ここ][use-multiple-node-pools]を参照してください。

[az aks nodepool add][az-aks-nodepool-add] を使用してノード プールを作成します。
```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name spotnodepool \
    --priority Spot \
    --eviction-policy Delete \
    --spot-max-price -1 \
    --enable-cluster-autoscaler \
    --min-count 1 \
    --max-count 3 \
    --no-wait
```

既定では、複数のノード プールを含む AKS クラスターを作成する場合は、その AKS クラスター内に *Regular* の *priority* を持つノード プールを作成します。 上のコマンドは、*Spot* の *priority* を持つ既存の AKS クラスターに補助ノード プールを追加します。 *Spot* の *priority* により、ノード プールがスポット ノード プールになります。 上の例では、*eviction-policy* パラメーターは、既定値である *Delete* に設定されています。 [eviction policy][eviction-policy] を *Delete* に設定すると、ノード プールの基になるスケール セット内のノードは、排除されたときに削除されます。 また、eviction policy を *Deallocate* に設定することもできます。 eviction policy を *Deallocate* に設定すると、基になるスケール セット内のノードは、排除時に停止済み/割り当て解除済み状態に設定されます。 停止済み/割り当て解除済み状態のノードはコンピューティング クォータには含まれず、クラスターのスケーリングやアップグレードで問題を発生させる場合があります。 *priority* および *eviction-policy* 値は、ノード プールの作成中にのみ設定できます。 これらの値を後で更新することはできません。

このコマンドではまた、スポット ノード プールで使用することが推奨される[クラスター オートスケーラー][cluster-autoscaler]も有効になります。 クラスターで実行されているワークロードに基づいて、クラスター オートスケーラーは、ノード プール内のノードの数をスケールアップおよびスケールダウンします。 スポット ノード プールでは、追加のノードが引き続き必要な場合、クラスター オートスケーラーは排除の後にノードの数をスケールアップします。 ノード プールに含めることができるノードの最大数を変更する場合は、クラスター オートスケーラーに関連付けられている `maxCount` 値も調整する必要があります。 クラスター オートスケーラーを使用しない場合は、排除により、スポット プールが最終的に 0 まで減少し、追加のスポット ノードを受信するための手動操作が必要になります。

> [!Important]
> スポット ノード プールでは、バッチ処理ジョブやテスト環境などの、中断を処理できるワークロードのみをスケジュールしてください。 スポット ノード プールでノードの排除を処理できるワークロードのみが確実にスケジュールされるようにするには、スポット ノード プールで [taint と toleration][taints-tolerations] を設定することをお勧めします。 たとえば、上のコマンドは既定で *kubernetes.azure.com/scalesetpriority=spot:NoSchedule* の taint を追加するため、このノードでは対応する toleration を持つポッドのみがスケジュールされます。

## <a name="verify-the-spot-node-pool"></a>スポット ノード プールを確認する

ノード プールがスポット ノード プールとして追加されたを確認するには:

```azurecli
az aks nodepool show --resource-group myResourceGroup --cluster-name myAKSCluster --name spotnodepool
```

*scaleSetPriority* が *Spot* であることを確認します。

スポット ノードで実行するポッドをスケジュールするには、そのスポット ノードに適用される taint に対応する toleration を追加します。 次の例は、前の手順で使用された *kubernetes.azure.com/scalesetpriority=spot:NoSchedule* taint に対応する toleration を定義する yaml ファイルの一部を示しています。

```yaml
spec:
  containers:
  - name: spot-example
  tolerations:
  - key: "kubernetes.azure.com/scalesetpriority"
    operator: "Equal"
    value: "spot"
    effect: "NoSchedule"
   ...
```

この toleration を持つポッドがデプロイされたら、Kubernetes は、taint が適用されているノードでそのポッドを正常にスケジュールできます。

## <a name="max-price-for-a-spot-pool"></a>スポット プールの最大価格
リージョンと SKU に基づいて、[スポット インスタンスの価格は変動します][pricing-spot]。 詳細については、[Linux][pricing-linux] および [Windows][pricing-windows] での価格を参照してください。

可変する価格に対して、最大 5 桁の小数点以下を使用して、最大価格を米ドル (USD) で設定することができます。 たとえば、*0.98765* という値は、1 時間あたり 0.98765 米国ドルの最大価格になります。 最大価格を *-1* に設定すると、インスタンスは価格に基づいて排除されません。 インスタンスの価格は、使用可能な容量とクォータがある限り、現在のスポットの価格または標準インスタンスの価格のいずれか低い方になります。

## <a name="next-steps"></a>次のステップ

この記事では、AKS クラスターにスポット ノード プールを追加する方法について説明しました。 すべてのノード プールのポッドを制御する方法の詳細については、「[Azure Kubernetes Service (AKS) での高度なスケジューラ機能に関するベスト プラクティス][operator-best-practices-advanced-scheduler]」を参照してください。

<!-- LINKS - External -->
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/

<!-- LINKS - Internal -->
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-nodepool-add]: /cli/azure/aks/nodepool#az-aks-nodepool-add
[cluster-autoscaler]: cluster-autoscaler.md
[eviction-policy]: ../virtual-machine-scale-sets/use-spot.md#eviction-policy
[kubernetes-concepts]: concepts-clusters-workloads.md
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[pricing-linux]: https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/
[pricing-spot]: ../virtual-machine-scale-sets/use-spot.md#pricing
[pricing-windows]: https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/windows/
[spot-toleration]: #verify-the-spot-node-pool
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[use-multiple-node-pools]: use-multiple-node-pools.md
[vmss-spot]: ../virtual-machine-scale-sets/use-spot.md