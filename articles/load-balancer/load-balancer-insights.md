---
title: Azure Load Balancer に関する分析情報
description: ロード バランサーの分析情報を使用して、迅速な障害発生箇所の特定と情報に基づく設計上の決定を実現します
services: load-balancer
documentationcenter: na
author: erichrt
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/27/2020
ms.author: errobin
ms.openlocfilehash: 190a9e431dedfb0f0fe6077e903174f253f5c773
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100589099"
---
# <a name="using-insights-to-monitor-and-configure-your-azure-load-balancer"></a>Azure Load Balancer を監視および構成するための分析情報の使用

ネットワーク用の Azure Monitor を使用すると、機能の依存関係の視覚化と、Load Balancer に対する事前構成済みのメトリック ダッシュボードが提供されます。 これらのビジュアルは、情報に基づいて設計に関する決定を行い、任意の障害を速やかに特定、診断、解決するのに役立ちます。

>[!NOTE] 
>この機能はプレビュー段階であり、機能依存関係ビューと事前構成済みダッシュボードは、このエクスペリエンスを向上させるために変更される可能性があることに注意してください

>[!IMPORTANT]
>構成済みメトリック ダッシュボードで Load Balancer 名前空間のメトリックを表示するには、Standard Load Balancer が必要です。 VM、仮想マシン スケール セット、接続モニターの名前空間のメトリックを表示することもできますが、運用ワークロードの場合は、Load Balancer メトリックの堅牢なセットを利用するため、[Standard にアップグレードする](./upgrade-basic-standard.md)ことをお勧めします。

## <a name="functional-dependency-view"></a>機能依存関係ビュー

機能依存関係ビューを使用すると、最も複雑なロード バランサーのセットアップでも把握できます。 最新の Load Balancer の構成についての視覚的なフィードバックにより、構成を念頭に置いて更新を行うことができます。

このビューには、Azure の Load Balancer リソースの [負荷分散] ブレードでアクセスします。

:::image type="content" source="./media/load-balancer-insights/load-balancer-functional-dependency-visual.png" alt-text="機能依存関係ビューの図。ロード バランサーのフロントエンドが、構成されたルールによってバックエンド プールのメンバーに接続されていることがわかります。Standard Load Balancer の場合、負荷分散ルールからバックエンド プールのインスタンスへの線は、正常性プローブの状態に基づいて色分けされます。" border="true":::

Standard Load Balancer の場合、バックエンド プールのリソースは正常性プローブの状態で色分けされ、トラフィックを処理するためのバックエンド プールの現在の可用性が示されます。 上記のトポロジと共に、正常性状態の時間グラフが表示され、アプリケーションの正常性のスナップショット ビューが提供されます。

## <a name="metrics-dashboard"></a>メトリック ダッシュボード

Load Balancer の [分析情報] ブレードからは、より詳細なメトリックを選択して、Load Balancer の特定の側面に関連するメトリック ビジュアルが含まれる事前構成済みの [Azure Monitor ブック](../azure-monitor/visualize/workbooks-overview.md)を表示できます。 このダッシュボードには、Load Balancer の状態と関連ドキュメントへのリンクがページの上部に表示されます。

最初に [概要] タブが表示されます。使用可能なタブ間を移動できます。各タブには、お使いの Load Balancer の特定の側面に関連するビジュアルが含まれます。 ダッシュボードの各タブの下部には、それぞれの明示的なガイダンスがあります。

現在使用できるダッシュボード タブは次のとおりです。
* 概要
* フロントエンドおよびバックエンドの可用性
* データ スループット
* フローの分布
* 接続モニター
* メトリック定義 

### <a name="overview-tab"></a>[概要] タブ
[概要] タブの検索可能なグリッドには、お使いの Load Balancer にアタッチされている各フロントエンド IP に対する、全体的なデータ パスの可用性と正常性プローブの状態が表示されます。 これらのメトリックにより、フロントエンド IP が応答可能かどうか、およびバックエンド プール内のコンピューティング インスタンスが着信接続に対して個別に応答可能かどうかが示されます。

また、このページで各フロントエンド IP に対する全体的なデータ スループットを確認し、期待されるトラフィック レベルが生成および受信されているかどうかを把握することもできます。 ページの下部にあるガイダンスを見ると、不規則な値が表示された場合の適切なタブがわかります。

### <a name="frontend-and-backend-availability-tab"></a>フロントエンドとバックエンドの可用性に関するタブ
フロントエンドとバックエンドの可用性のタブには、いくつかの便利なビューにデータ パスのスループットと正常性プローブの状態のメトリックが表示されます。 最初のグラフには集計値が表示されるため、問題があるかどうかを判断できます。 グラフの残りの部分には、これらのメトリックがさまざまなディメンション別に分割されて表示され、受信可用性の問題の原因を特定してトラブルシューティングすることができます。

ページの下部には、これらのグラフを表示するためのワークフローが、さまざまな症状の一般的な原因と共に表示されます。 

### <a name="data-throughput-tab"></a>データ スループットに関するタブ
データ スループットに関するタブを使用すると、受信および送信スループットを確認して、トラフィック パターンが想定どおりかどうかを確認できます。 受信データと送信データのスループットがフロントエンド IP とフロントエンド ポートに分けて表示されるため、実行しているサービスが個別に実行されているかどうかを識別できます。

### <a name="flow-distribution"></a>フローの分布
フローの分布に関するタブは、バックエンド インスタンスによって受信および生成されているフローの数を視覚化および管理するのに役立ちます。 受信トラフィックと送信トラフィックのフロー作成速度とフロー数、および各 VM と仮想マシン スケール セット インスタンスが受信しているネットワーク トラフィックが表示されます。 

これらのビューを使用すると、お使いの Load Balancer の構成またはトラフィック パターンによってトラフィックがアンバランスになっているかどうかについてのフィードバックを得ることができます。 たとえば、セッション アフィニティが構成されていて、1 つのクライアントが過剰な数の要求を行っている場合などです。 また、マシンのサイズに対する [VM ごとのフロー制限](../virtual-network/virtual-machine-network-throughput.md#flow-limits-and-active-connections-recommendations)に近づいているかどうかも確認できます。

### <a name="connection-monitors"></a>接続モニター
接続モニターに関するタブには、構成されているすべての[接続モニター](../network-watcher/connection-monitor.md)のグローバル マップでのラウンドトリップ待機時間が表示されます。 これらのビジュアルにより、厳密な待機時間が必要なサービスに役立つ情報が提供されます。 要件を満たすには、リージョンのデプロイを追加するか、[クロスリージョン負荷分散](./cross-region-overview.md)モデルに移行することが必要になる場合があります

### <a name="metric-definitions"></a>メトリック定義
メトリック定義に関するタブには、[多次元メトリックに関する記事](./load-balancer-standard-diagnostics.md#multi-dimensional-metrics)で示されているすべての情報が含まれます。

## <a name="next-steps"></a>次のステップ
* ダッシュボードを確認し、改善できるものがある場合は、次のリンクを使用してフィードバックを提供します
* [メトリックのドキュメントを見て、各メトリックの計算方法を理解していることを確認します](./load-balancer-standard-diagnostics.md#multi-dimensional-metrics)
* [Load Balancer に対する接続モニターを作成します](../network-watcher/connection-monitor.md)
* [独自のブックを作成](../azure-monitor/visualize/workbooks-overview.md)します。詳細なメトリック ダッシュボードで編集ボタンをクリックして、インスピレーションを得ることができます
