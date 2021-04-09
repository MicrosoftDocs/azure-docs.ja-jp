---
title: Azure Log Analytics のネットワーク パフォーマンス モニター ソリューション | Microsoft Docs
description: Network Performance Monitor の ExpressRoute モニター機能を使用して、ブランチ オフィスと Azure の間のエンド ツー エンド接続とパフォーマンスを Azure ExpressRoute 経由で監視します。
ms.topic: conceptual
author: abshamsft
ms.author: absha
ms.date: 11/27/2018
ms.openlocfilehash: 0b005e46e9f9a740fee153e063bebf108c22b303
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101725698"
---
# <a name="expressroute-monitor"></a>ExpressRoute モニター

> [!IMPORTANT]
> 2021 年 7 月 1 日以降、既存のワークスペースに新しいテストを追加したり、Network Performance Monitor で新しいワークスペースを有効にしたりできなくなります。 2021 年 7 月 1 日より前に作成されたテストは使い続けることができます。 現在のワークロードに対するサービスの中断を最小限に抑えるには、2024 年 2 月 29 日より前に、[Network Performance Monitor から Azure Network Watcher の新しい接続モニターにテストを移行](../../network-watcher/migrate-to-connection-monitor-from-network-performance-monitor.md)します。

[Network Performance Monitor](network-performance-monitor.md) の Azure ExpressRoute モニター機能を使用して、ブランチ オフィスと Azure の間のエンド ツー エンド接続とパフォーマンスを Azure ExpressRoute 経由で監視できます。 主な利点は次のとおりです。 

- サブスクリプションに関連付けられている ExpressRoute 回線の自動検出。
- ExpressRoute の帯域幅使用率、回線での損失と待ち時間、ピアリングと Azure Virtual Network のレベルの追跡。
- ExpressRoute 回線のネットワーク トポロジの検出。

![ExpressRoute モニター](media/network-performance-monitor-expressroute/expressroute-intro.png)

## <a name="configuration"></a>構成 
Network Performance Monitor の構成を開くには、[Network Performance Monitor ソリューション](network-performance-monitor.md)を開き、 **[構成]** を選びます。

### <a name="configure-network-security-group-rules"></a>ネットワーク セキュリティ グループ規則を構成する 
Network Performance Monitor による監視に使われる Azure 内のサーバーについては、Network Performance Monitor の代理トランザクションに使われるポートの TCP トラフィックを許可するようにネットワーク セキュリティ グループ (NSG) 規則を構成します。 既定のポートは 8084 です。 この構成により、Azure VM にインストールされている Log Analytics エージェントは、オンプレミスの監視エージェントと通信できます。 

NSG の詳細については、 [ネットワーク セキュリティ グループ](../../virtual-network/manage-network-security-group.md)に関するページを参照してください。 

>[!NOTE]
> この手順を続行する前に、オンプレミスのサーバー エージェントと Azure サーバー エージェントをインストールし、EnableRules.ps1 PowerShell スクリプトを実行します。 

 
### <a name="discover-expressroute-peering-connections"></a>ExpressRoute ピアリング接続の検出 
 
1. **[ExpressRoute ピアリング]** ビューを選びます。
2. **[今すぐ検出する]** を選び、この Azure Log Analytics ワークスペースにリンクされている Azure サブスクリプション内の仮想ネットワークに接続されているすべての ExpressRoute プライベート ピアリングを検出します。

    >[!NOTE]
    > このソリューションでは、現時点では ExpressRoute プライベート ピアリングしか検出されません。 

    >[!NOTE]
    > 検出されるのは、この Log Analytics ワークスペースにリンクされているサブスクリプションに関連付けられている仮想ネットワークに接続されているプライベート ピアリングのみです。 ExpressRoute が、このワークスペースにリンクされているサブスクリプションの外部の仮想ネットワークに接続されている場合は、これらのサブスクリプションに Log Analytics ワークスペースを作成します。 その後、Network Performance Monitor を使ってこれらのピアリングを監視します。 

    ![ExpressRoute Monitor の構成](media/network-performance-monitor-expressroute/expressroute-configure.png)
 
   検出が完了した後、検出されたプライベート ピアリング接続がテーブルに一覧表示されます。 これらのピアリングの監視は、最初は無効状態になっています。 

### <a name="enable-monitoring-of-the-expressroute-peering-connections"></a>ExpressRoute ピアリング接続の監視を有効にする 

1. 監視するプライベート ピアリング接続を選びます。
2. 右側のウィンドウで、**[このピアリングを監視する]** チェック ボックスをオンにします。 
3. この接続の正常性イベントを作成する場合は、**[このピアリングの正常性監視を有効にする]** をオンにします。 
4. 監視条件を選択します。 しきい値を入力して、正常性イベントの生成に関するカスタムしきい値を設定できます。 ピアリング接続に対して選択したしきい値を条件の値が上回ると、正常性イベントが生成されます。 
5. **[エージェントの追加]** を選び、このピアリング接続を監視するために使う監視エージェントを選びます。 接続の両端にエージェントを追加したことを確認します。 このピアリングに接続されている仮想ネットワークには少なくとも 1 つのエージェントが必要です。 また、少なくとも 1 つのオンプレミスのエージェントがこのピアリングに接続されている必要もあります。 
6. **[保存]** を選んで構成を保存します。 

   ![ExpressRoute の監視の構成](media/network-performance-monitor-expressroute/expressroute-configure-discovery.png)


規則を有効にして値とエージェントを選んだ後、値が反映されて **[ExpressRoute の監視]** タイルが表示されるまで、30 ～ 60 分間待ちます。 監視タイルが表示されると、ExpressRoute 回線と接続リソースは Network Performance Monitor によって監視されています。 

>[!NOTE]
> この機能は、新しいクエリ言語にアップグレードされたワークスペースで確実に機能します。

## <a name="walkthrough"></a>チュートリアル 

Network Performance Monitor ダッシュ ボードには、ExpressRoute 回線とピアリング接続の正常性の概要が表示されます。 

![ネットワーク パフォーマンス モニターのダッシュボード](media/network-performance-monitor-expressroute/npm-dashboard-expressroute.png) 

### <a name="circuits-list"></a>回線の一覧 

監視対象の全 ExpressRoute 回線の一覧を表示するには、[ExpressRoute 回線] タイルを選びます。 いずれかの回線を選択すると、その正常性状態のほか、パケット損失、帯域幅使用率、待ち時間の各トレンド グラフを表示できます。 これらのグラフは対話操作が可能です。 グラフのプロット対象となる時間枠を自分で選んでカスタマイズすることができます。 データ ポイントを拡大し、細かく表示するには、グラフ上の領域でマウスをドラッグします。 

![ExpressRoute 回線の一覧](media/network-performance-monitor-expressroute/expressroute-circuits.png) 

### <a name="trends-of-loss-latency-and-throughput"></a>パケット損失、待ち時間、スループットのトレンド 

帯域幅使用状況、待ち時間、損失の各グラフは対話操作が可能です。 これらのグラフの任意のセクションをマウス操作で拡大することができます。 帯域幅、待機時間、および損失データを他の間隔で表示することもできます。 左上の **[アクション]** ボタンで  **[日付/時刻]** を選びます。 

![ExpressRoute の待機時間](media/network-performance-monitor-expressroute/expressroute-latency.png) 

### <a name="peerings-list"></a>ピアリング一覧 

プライベート ピアリング上の仮想ネットワークに対するすべての接続の一覧を表示するには、ダッシュボードの **[プライベート ピアリング]** タイルを選びます。 ここでいずれかの仮想ネットワークの接続を選択すると、その正常性状態のほか、パケット損失、帯域幅使用率、待ち時間の各トレンド グラフを表示できます。 

![ExpressRoute ピアリング](media/network-performance-monitor-expressroute/expressroute-peerings.png) 

### <a name="circuit-topology"></a>回線トポロジ 

回線トポロジを表示するには、**[トポロジ]** タイルを選びます。 この操作により、選択した回線またはピアリングのトポロジ ビューが表示されます。 トポロジ ダイアグラムには、ネットワーク上の各セグメントの待ち時間が表示され、各レイヤー 3 ホップがダイアグラムのノードで表現されます。 いずれかのホップを選ぶと、そのホップについてのさらに詳しい情報が表示されます。 視認性を高めて表示範囲をオンプレミスのホップにまで広げるには、**[フィルター]** の下のスライダー バーを動かします。 スライダー バーを左右に動かすと、トポロジ グラフに表示されるホップ数が増減します。 各セグメントの待ち時間が視覚的に確認できるので、ネットワーク上のセグメントの中で、待ち時間の長いセグメントを短時間で切り分けることができます。

![ExpressRoute のトポロジ](media/network-performance-monitor-expressroute/expressroute-topology.png)

### <a name="detailed-topology-view-of-a-circuit"></a>回線の詳細なトポロジ ビュー 

このビューには、仮想ネットワーク接続が表示されます。 

![ExpressRoute の仮想ネットワーク接続](media/network-performance-monitor-expressroute/expressroute-vnet.png)
 
## <a name="diagnostics"></a>診断 

Network Performance Monitor は、いくつかの回線接続の問題を診断するのに役立ちます。 目にする可能性がある問題のいくつかを以下に示します。

通知コードを確認し、**LogAnalytics** を使用してアラートを設定できます。 **[NPM Diagnostics]\(NPM 診断\)** ページには、トリガーされるすべての診断メッセージの説明が表示されます。

| 通知コード (ログ) | 説明 |
| --- | --- |
| 5501 | ExpressRoute 回線のセカンダリ接続をスキャンできません。 |
| 5502 | ExpressRoute 回線のプライマリ接続をスキャンできません。 |
| 5503 | ワークスペースに関連付けられたサブスクリプションに回線が見つかりませんでした。 | 
| 5508 | トラフィックがパスの回線を通っているかどうか確認できません。 |
| 5510 | トラフィックは目的の回線を通っていません。 | 
| 5511 | トラフィックは目的の仮想ネットワークを通っていません。 | 

**回線がダウンしている。** Network Performance Monitor は、オンプレミスのリソースと Azure 仮想ネットワーク間の接続が失われるとすぐに通知します。 この通知により、ユーザーからエスカレーションを受ける前に予防的なアクションを実行し、ダウンタイムを短縮できます。

![ExpressRoute 回線がダウンしている](media/network-performance-monitor-expressroute/expressroute-circuit-down.png)
 

**トラフィックが目的の回線経由で流れていない。** Network Performance Monitor は、意図した ExpressRoute 回線をトラフィックが流れないときは常に通知します。 この問題は、回路がダウンし、トラフィックがバックアップ ルート経由で流れている場合に発生することがあります。 ルーティングの問題がある場合にも発生する可能性があります。 この情報は、ルーティング ポリシーの構成の問題を予防的に管理し、最適かつ最も安全なルートが使用されるようにするために役立ちます。 

 

**トラフィックがプライマリ回線経由で流れていない。** Network Performance Monitor は、トラフィックがセカンダリの ExpressRoute 回線経由で流れている場合に通知します。 この場合は接続性の問題は発生しませんが、プライマリ回線の問題の予防的なトラブルシューティングを行うと、より入念に準備を整えることができます。 

 
![ExpressRoute トラフィック フロー](media/network-performance-monitor-expressroute/expressroute-traffic-flow.png)


**ピーク時の使用率によるパフォーマンス低下。** 帯域幅使用率の傾向を待ち時間の傾向と関連付けて、Azure ワークロードのパフォーマンス低下が帯域幅使用率のピークによるものかどうかを識別します。 その後、それに応じてアクションを実行することができます。

![ExpressRoute の帯域幅の使用率](media/network-performance-monitor-expressroute/expressroute-peak-utilization.png)

 

## <a name="next-steps"></a>次のステップ
詳細なネットワーク パフォーマンスのデータ レコードを表示するために、[ログを検索](../logs/log-query-overview.md)します。