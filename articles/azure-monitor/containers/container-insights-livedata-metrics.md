---
title: Container insights を使用してメトリックをリアルタイムで表示する | Microsoft Docs
description: この記事では、Container insights で kubectl を使用せずにメトリックをリアルタイムで表示する方法について説明します。
ms.topic: conceptual
ms.date: 10/15/2019
ms.custom: references_regions
ms.openlocfilehash: 2e3a8a417a934374c2c0b256bb65a471d98fdebe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101731852"
---
# <a name="how-to-view-metrics-in-real-time"></a>メトリックをリアルタイムで表示する方法

Container insights のライブ データ (プレビュー) 機能を使用すると、クラスター内のノードとポッドの状態に関するメトリックをリアルタイムで視覚化することができます。 `kubectl top nodes`、`kubectl get pods –all-namespaces`、`kubectl get nodes` コマンドへの直接アクセスがエミュレートされ、この分析情報に含まれるパフォーマンス グラフのデータの呼び出し、解析、視覚化が行われます。

この記事では、この機能の使用方法について詳しく説明します。

>[!NOTE]
>[プライベート クラスター](https://azure.microsoft.com/updates/aks-private-cluster/)として有効にされた AKS クラスターは、この機能でサポートされていません。 この機能では、ブラウザーからプロキシ サーバーを介した Kubernetes API への直接アクセスを利用します。 このプロキシから Kubernetes API をブロックするようにネットワーク セキュリティを有効にすると、このトラフィックはブロックされます。

ライブ データ (プレビュー) 機能の設定またはトラブルシューティングについては、[セットアップ ガイド](container-insights-livedata-setup.md)を参照してください。

## <a name="how-it-works"></a>機能

Live Data (プレビュー) 機能は、Kubernetes API に直接アクセスします。認証モデルの詳細については、[こちら](https://kubernetes.io/docs/concepts/overview/kubernetes-api/)を参照してください。

この機能は、メトリック エンドポイント (`/api/v1/nodes`、`/apis/metrics.k8s.io/v1beta1/nodes`、`/api/v1/pods` など) に対してポーリング操作を実行します。これは、既定では 5 秒ごとに実行されます。 このデータはブラウザーにキャッシュされ、Container insights で **[ライブに移動 (プレビュー)]** を選択すると **[クラスター]** タブに表示される 4 つのパフォーマンス グラフにグラフ化されます。 後続の各ポーリングは、5 分間のローリング視覚化ウィンドウにグラフ化されます。

![クラスター ビューでの [ライブに移動] オプション](./media/container-insights-livedata-metrics/cluster-view-go-live-example-01.png)

ポーリング間隔は、 **[間隔の設定]** ドロップダウンから構成できます。1 秒、5 秒、15 秒、および 30 秒ごとの新しいデータのポーリングを設定できます。

![[ライブに移動] ドロップダウン ポーリング間隔](./media/container-insights-livedata-metrics/cluster-view-polling-interval-dropdown.png)

>[!IMPORTANT]
>短時間で問題のトラブルシューティングを行う場合は、ポーリング間隔を 1 秒に設定することをお勧めします。 これらの要求は、クラスターの Kubernetes API の可用性とスロットリングに影響を与える可能性があります。 その後、より長いポーリング間隔へと再構成します。

>[!IMPORTANT]
>この機能の操作中、データが永続的に保存されることはありません。 このセッション中にキャプチャされたすべての情報は、ブラウザーを閉じるか、またはこの機能から移動すると、すぐに削除されます。 データは、5 分間のウィンドウ内に表示するために残されています。5 分を経過したメトリックはすべて完全に削除されます。

これらのグラフは、ライブ モードで表示した最後の Azure ダッシュボードにピン留めすることはできません。

## <a name="metrics-captured"></a>キャプチャされるメトリック

### <a name="node-cpu-utilization---node-memory-utilization-"></a>ノードの CPU 使用率 (%) / ノードのメモリ使用率 (%)

これら 2 つのパフォーマンス グラフは、`kubectl top nodes` を呼び出し、**CPU%** および **MEMORY%** 列の結果をそれぞれのグラフにキャプチャすることと同等として位置づけられます。

![Kubectl top nodes の例の結果](./media/container-insights-livedata-metrics/kubectl-top-nodes-example.png)

![ノードの CPU 使用率グラフ](./media/container-insights-livedata-metrics/cluster-view-node-cpu-util.png)

![ノードのメモリ使用率グラフ](./media/container-insights-livedata-metrics/cluster-view-node-memory-util.png)

パーセンタイル計算は、大規模なクラスターでクラスター内の外れ値ノードを識別するために役立ちます。 たとえば、スケールダウンのために、ノードの使用率が低いかどうかを把握できます。 **Min** 集計を利用すると、クラスター内の使用率が低いノードを確認できます。 さらに詳しく調査するには、 **[ノード]** タブを選択し、CPU またはメモリの使用率でグリッドを並べ替えます。

また、どのノードが制限値に近づいているのか、スケールアウトが必要であるかどうかを把握するのにも役立ちます。 **Max** および **P95** 集計の両方を使用すると、クラスター内にリソース使用率が高いノードがあるかどうかを確認するのに役立ちます。 さらに詳しく調査するには、 **[ノード]** タブにもう一度切り替えます。

### <a name="node-count"></a>ノード数

このパフォーマンス グラフは、`kubectl get nodes` を呼び出し、状態の種類でグループ化されたグラフに **STATUS** 列をマッピングすることと同等として位置づけられます。

![Kubectl get nodes の例の結果](./media/container-insights-livedata-metrics/kubectl-get-nodes-example.png)

![ノード数のグラフ](./media/container-insights-livedata-metrics/cluster-view-node-count-01.png)

ノードは、**準備完了** または **準備不完了** 状態のいずれかで報告されます。 これらはカウントされ (合計数が作成されます)、これらの 2 つの集計の結果がグラフ化されます。
たとえば、ノードが失敗の状態にあるかどうかを把握できます。 **準備不完了** 集計を使用すると、クラスター内で現在 **準備不完了** 状態のノードの数をすぐに確認することができます。

### <a name="active-pod-count"></a>アクティブなポッド数

このパフォーマンス グラフは、`kubectl get pods –all-namespaces` を呼び出し、状態の種類でグループ化されたグラフに **STATUS** 列をマッピングすることと同等として位置づけられます。

![Kubectl get pods の例の結果](./media/container-insights-livedata-metrics/kubectl-get-pods-example.png)

![ノードのポッド数のグラフ](./media/container-insights-livedata-metrics/cluster-view-node-pod-count.png)

>[!NOTE]
>`kubectl` によって解釈される状態の名前は、グラフ内で正確に一致しない場合があります。

## <a name="next-steps"></a>次のステップ

アラートの作成、視覚化の作成、またはクラスターの詳細な分析を行うために、定義済みのクエリや例を確認するには、[ログ クエリの例](container-insights-log-search.md#search-logs-to-analyze-data)を参照してください。
