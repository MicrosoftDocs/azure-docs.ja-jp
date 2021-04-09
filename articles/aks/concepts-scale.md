---
title: 概念 - Azure Kubernetes Service (AKS) でのアプリケーションのスケーリング
description: ポッドの水平オートスケーラー、クラスター オートスケーラー、Azure Container Instances コネクタなど、Azure Kubernetes Service (AKS) でのスケーリングについて説明します。
services: container-service
ms.topic: conceptual
ms.date: 02/28/2019
ms.openlocfilehash: b72ed7cefc6a16eb484e1337dbd64e5f069a2201
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94686040"
---
# <a name="scaling-options-for-applications-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) でのアプリケーションのスケーリング オプション

Azure Kubernetes Service (AKS) でアプリケーションを実行すると、コンピューティング リソースの量の増減が必要になることがあります。 必要のあるアプリケーション インスタンスの数が変わると、基になる Kubernetes の数も変更する必要が生じます。 また、多数の追加アプリケーション インスタンスを迅速にプロビジョニングすることが必要になる場合もあります。

この記事では、AKS 内でのアプリケーションのスケーリングに役立つ主要な概念について説明します。

- [手動によるスケーリング](#manually-scale-pods-or-nodes)
- [ポッドの水平オートスケーラー (HPA)](#horizontal-pod-autoscaler)
- [クラスター オートスケーラー](#cluster-autoscaler)
- [AKS との Azure Container Instance (ACI) の統合](#burst-to-azure-container-instances)

## <a name="manually-scale-pods-or-nodes"></a>ポッドまたはノードを手動でスケーリングする

レプリカ (ポッド) とノードを手動でスケーリングし、使用可能なリソースと状態の変化に対するアプリケーションの対応をテストできます。 手動によるリソースのスケーリングでは、ノード数などの固定コストを維持するために、使用する一定量のリソースを定義することもできます。 手動でスケーリングするには、レプリカまたはノードの数を定義します。 その後、そのレプリカまたはノードの数に基づき、Kubernetes API によって追加ポッドの作成またはノードのドレインのスケジュールが設定されます。

ノードをスケールダウンすると、Kubernetes API によって、クラスターで使用されるコンピューティングの種類に関連付けられている Azure コンピューティング API が呼び出されます。 たとえば、VM Scale Sets 上に構築されたクラスターの場合は、VM Scale Sets API によって、削除するノードを選択するロジックが決められます。 スケールダウン時に削除対象のノードがどのように選択されるかの詳細については、[VMSS の FAQ](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#if-i-reduce-my-scale-set-capacity-from-20-to-15-which-vms-are-removed)を参照してください。

ポッドとノードの手動によるスケーリングを開始するには、「[チュートリアル: Azure Kubernetes Service (AKS) でのアプリケーションのスケーリング][aks-scale]」を参照してください。

## <a name="horizontal-pod-autoscaler"></a>ポッドの水平オートスケーラー

Kubernetes は、ポッドの水平オートスケーラー (HPA) を使用して、リソース需要を監視し、レプリカの数を自動的にスケーリングします。 既定では、ポッドの水平オートスケーラーは、レプリカ数での必要な変更について、30 秒ごとにメトリックの API をチェックします。 変更が必要な場合、それに応じてレプリカの数が増減されます。 ポッドの水平オートスケーラーは、Kubernetes 1.8 以降用のメトリック サーバーをデプロイした AKS クラスターで動作します。

![Kubernetes のポッドの水平オートスケーリング](media/concepts-scale/horizontal-pod-autoscaling.png)

所定のデプロイ用にポッドの水平オートスケーラーを構成する場合、実行できるレプリカの最小値と最大数を定義します。 また、CPU 使用率など、監視するメトリックで、スケーリングの決定の基になるメトリックも定義します。

AKS でポッドの水平オートスケーラーを開始するには、「[ポッドを自動スケールする][aks-hpa]」を参照してください。

### <a name="cooldown-of-scaling-events"></a>スケーリング イベントのクールダウン

ポッドの水平オートスケーラーは、30 秒ごとにメトリックの API をチェックするので、前のスケーリング イベントが正しく完了していないうちに、別のチェックが行われる可能性があります。 この動作のため、前のスケーリング イベントでアプリケーションのワークロードとリソースの需要を受け取ってそれに応じて調整できるようになる前に、ポッドの水平オートスケーラーによってレプリカの数が変更される可能性があります。

競合イベントを最小限に抑えるために、遅延値が設定されます。 この値は、ポッドの水平オートスケーラーがスケーリング イベント後に別のスケーリング イベントをトリガーできるまで待機する必要のある時間を定義します。 この動作により、新しいレプリカ数が有効になり、メトリックの API で配分されたワークロードを反映できるようになります。 [Kubernetes 1.12 時点でスケールアップ イベントの遅延はありません](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/#support-for-cooldown-delay)が、スケールダウン イベントの遅延は既定で 5 分に設定されています。

現時点では、これらのクールダウン値を既定値から調整することはできません。

## <a name="cluster-autoscaler"></a>クラスター オートスケーラー

ポッドの需要の変化に対応するために、Kubernetes には、ノード プール内で要求されるコンピューティング リソースに基づいてノードの数を調整するクラスター オートスケーラーがあります。 既定では、クラスター オートスケーラーは、必要なノード数の変更についてメトリック API サーバーを 10 秒ごとに確認します。 クラスター オートスケーラーが変更が必要だと判断した場合、それに応じて AKS クラスター内のノードの数が増減されます。 クラスターオートスケーラーは、Kubernetes 1.10.x 以降を実行する Kubernetes RBAC 対応 AKS クラスターで動作します。

![Kubernetes クラスター オートスケーラー](media/concepts-scale/cluster-autoscaler.png)

クラスター オートスケーラーは通常、ポッドの水平オートスケーラーと一緒に使用されます。 組み合わせた場合、ポッドの水平オートスケーラーはアプリケーションの需要に基づいてポッド数を増減し、クラスター オートスケーラーはそれに応じて、これらの追加ポッドの実行に必要になるノードの数を調整します。

AKS でクラスター オートスケーラーを開始するには、「[Azure Kubernetes Service のクラスター オートスケーラー (AKS) - プレビュー][aks-cluster-autoscaler]」を参照してください。

### <a name="scale-out-events"></a>スケールアウト イベント

ノードに、要求されたポッドを実行するために十分なコンピューティング リソースがない場合、そのポッドではスケジューリング プロセスを進めることができません。 ノード プール内で追加のコンピューティング リソースが利用できない限り、ポッドを開始できません。

ノード プールのリソース制約のためにスケジュール設定できないポッドが、クラスター オートスケーラーで確認された場合、追加のコンピューティング リソースを提供するため、ノード プール内のノードの数が増やされます。 これらの追加ノードが正常にデプロイされ、ノード プール内で使用できるようになると、ポッドはこれらのノードで実行するようにスケジュール設定されます。

アプリケーションが迅速にスケーリングする必要がある場合、クラスター オートスケーラーによってデプロイされた追加ノードがスケジュール設定されたポッドを受け入れるまで、一部のポッドはスケジュール設定の待機状態のままのことがあります。 高いバースト需要のあるアプリケーションの場合、仮想ノードと Azure Container Instances でスケーリングできます。

### <a name="scale-in-events"></a>スケールイン イベント

クラスター オートスケーラーでは、最近新しいスケジュール要求を受け取っていないノードのポッド スケジューリング状態も監視されます。 このシナリオでは、ノード プールに必要以上のコンピューティング リソースがあり、ノードの数を削減できることが示されます。

既定で10分間、不要であるかどうかのしきい値を超えたノードは、削除対象としてスケジュールされます。 このような状況が発生した場合、ポッドは、ノード プール内の他のノードで実行するようにスケジュール設定され、クラスター オートスケーラーは、ノードの数を減らします。

クラスター オートスケーラーがノードの数を減らしたときに、ポッドは別のノード上にスケジュール設定されるので、アプリケーションに何からの中断が発生することがあります。 中断を最小限に抑えるには、単一のポッド インスタンスを使用するアプリケーションを使用しないでください。

## <a name="burst-to-azure-container-instances"></a>Azure Container Instances へのバースト

AKS クラスターを迅速にスケーリングするために、Azure Container Instances (ACI) と統合できます。 Kubernetes には、レプリカおよびノード数をスケーリングするコンポーネントが組み込まれています。 ただし、アプリケーションが迅速なスケーリングを必要としている場合、ポッドの水平オートスケーラーは、ノード プール内の既存のコンピューティング リソースが提供できる数より多くのポッドをスケジュール設定できます。 構成されている場合、このシナリオは、クラスター オートスケーラーをトリガーして、ノード プール内の追加ノードをデプロイしますが、これらのノードが正常にプロビジョニングし、Kubernetes スケジューラがそれらの上でポッドを実行できるようになるまで数分かかることがあります。

![ACI への Kubernetes バースト スケーリング](media/concepts-scale/burst-scaling.png)

ACI では、追加のインフラストラクチャのオーバーヘッドなしに、コンテナー インスタンスを迅速にデプロイできます。 AKS で接続する場合、ACI は、AKS クラスターのセキュリティ保護された論理拡張機能になります。 [仮想ノード][virtual-nodes-cli] コンポーネントは [Virtual Kubelet][virtual-kubelet] に基づいており、仮想 Kubernetes ノードとして ACI を提示する AKS クラスターにインストールされます。 Kubernetes は続いて、直接 AKS クラスター内にある VM ノード上のポッドとしてではなく、仮想ノードを通じた ACI インスタンスとして実行するポッドをスケジュール設定できます。

アプリケーションは、仮想ノードを使用するために変更は不要です。 クラスター オートスケーラーが AKS クラスター内に新しいノードをデプロイするときに、デプロイは AKS と ACI にわたって遅延なくスケーリングできます。

仮想ノードは、AKS クラスターと同じ仮想ネットワーク内の追加サブネットにデプロイされます。 この仮想ネットワーク構成は、ACI と AKS 間のトラフィックをセキュリティで保護できます。 AKS クラスターと同様に、ACI インスタンスは、他のユーザーから分離されたセキュリティ保護された論理的なコンピューティング リソースです。

## <a name="next-steps"></a>次のステップ

スケーリング アプリケーションを開始するには、最初に[クイックスタートに従って、Azure CLI で AKS クラスターを作成][aks-quickstart]します。 次に、AKS クラスターでスケーリング アプリケーションを手動でも自動でも開始できます。

- [ポッド][aks-manually-scale-pods]または[ノード][aks-manually-scale-nodes]を手動でスケーリングする
- [ポッドの水平オートスケーラー][aks-hpa]を使用する
- [クラスター オートスケーラーを使用する][aks-cluster-autoscaler]

Kubernetes と AKS の中心概念の詳細については、次の記事を参照してください。

- [Kubernetes/AKS クラスターとワークロード][aks-concepts-clusters-workloads]
- [Kubernetes/AKS のアクセスと ID][aks-concepts-identity]
- [Kubernetes/AKS のセキュリティ][aks-concepts-security]
- [Kubernetes/AKS の仮想ネットワーク][aks-concepts-network]
- [Kubernetes/AKS のストレージ][aks-concepts-storage]

<!-- LINKS - external -->
[virtual-kubelet]: https://virtual-kubelet.io/

<!-- LINKS - internal -->
[aks-quickstart]: kubernetes-walkthrough.md
[aks-hpa]: tutorial-kubernetes-scale.md#autoscale-pods
[aks-scale]: tutorial-kubernetes-scale.md
[aks-manually-scale-pods]: tutorial-kubernetes-scale.md#manually-scale-pods
[aks-manually-scale-nodes]: tutorial-kubernetes-scale.md#manually-scale-aks-nodes
[aks-cluster-autoscaler]: ./cluster-autoscaler.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-network]: concepts-network.md
[virtual-nodes-cli]: virtual-nodes-cli.md
