---
title: 'Azure ExpressRoute: 監視、メトリック、およびアラート'
description: Azure 全体のすべてのメトリック、アラート、診断ログを一元的に管理できる Azure Monitor を使用した、Azure ExpressRoute の監視、メトリクス、およびアラートについて説明します。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 01/11/2020
ms.author: duau
ms.openlocfilehash: 7a5da35da35b2f447256bc742681ccd7a7d403da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99091568"
---
# <a name="expressroute-monitoring-metrics-and-alerts"></a>ExpressRoute の監視、メトリック、およびアラート

この記事では、Azure Monitor を使用した ExpressRoute の監視、メトリック、およびアラートについて説明します。 Azure Monitor を使用すると、Azure 全体のすべてのメトリック、アラート、診断ログを一元的に管理できます。
 
>[!NOTE]
>**クラシック メトリック** の使用はお勧めしません。
>

## <a name="expressroute-metrics"></a>ExpressRoute メトリック

**[メトリック]** を表示するには、 *[Azure Monitor]* ページに移動して *[メトリック]* をクリックします。 **[ExpressRoute]** メトリックを表示するには、リソースの種類を *[ExpressRoute 回線]* でフィルターを適用します。 **[Global Reach]** メトリックを表示するには、リソースの種類を *[ExpressRoute 回線]* でフィルターを適用し、Global Reach が有効になっている ExpressRoute 回線リソースを選択します。 **ExpressRoute Direct** メトリックを表示するには、 *[ExpressRoute ポート]* でリソースの種類をフィルターします。 

メトリックを選択すると、既定の集計が適用されます。 必要に応じて、さまざまなディメンションでメトリックを表示する分割を適用できます。

### <a name="available-metrics"></a>使用可能なメトリック

|**メトリック**|**カテゴリ**|**ディメンション**|**機能**|
| --- | --- | --- | --- |
|ARP の可用性|可用性|<ui><li>ピア (プライマリ/セカンダリの ExpressRoute ルーター)</ui></li><ui><li> ピアリングの種類 (プライベート/パブリック/Microsoft)</ui></li>|ExpressRoute|
|BGP の可用性|可用性|<ui><li> ピア (プライマリ/セカンダリの ExpressRoute ルーター)</ui></li><ui><li> ピアリングの種類</ui></li>|ExpressRoute|
|BitsInPerSecond (受信ビット数/秒)|トラフィック|<ui><li> ピアリングの種類 (ExpressRoute)</ui></li><ui><li>リンク (ExpressRoute Direct)</ui></li>|<li>ExpressRoute</li><li>ExpressRoute Direct</li><ui><li>ExpressRoute ゲートウェイの接続</ui></li>|
|BitsOutPerSecond (送信ビット数/秒)|トラフィック| <ui><li>ピアリングの種類 (ExpressRoute)</ui></li><ui><li> リンク (ExpressRoute Direct) |<ui><li>ExpressRoute<ui><li>ExpressRoute Direct</ui></li><ui><li>ExpressRoute ゲートウェイの接続</ui></li>|
|CPU 使用率|パフォーマンス| <ui><li>インスタンス</ui></li>|ExpressRoute 仮想ネットワーク ゲートウェイ|
|1 秒あたりのパケット数|パフォーマンス| <ui><li>インスタンス</ui></li>|ExpressRoute 仮想ネットワーク ゲートウェイ|
|ピアにアドバタイズされたルートの数 |可用性| <ui><li>インスタンス</ui></li>|ExpressRoute 仮想ネットワーク ゲートウェイ|
|ピアから学習したルートの数 |可用性| <ui><li>インスタンス</ui></li>|ExpressRoute 仮想ネットワーク ゲートウェイ|
|ルート変更の頻度 |可用性| <ui><li>インスタンス</ui></li>|ExpressRoute 仮想ネットワーク ゲートウェイ|
|仮想ネットワーク内の VM の数 |可用性| 該当なし |ExpressRoute 仮想ネットワーク ゲートウェイ|
|GlobalReachBitsInPerSecond|トラフィック|<ui><li>ピアリングされた回線 Skey (サービス キー)</ui></li>|Global Reach|
|GlobalReachBitsOutPerSecond|トラフィック|<ui><li>ピアリングされた回線 Skey (サービス キー)</ui></li>|Global Reach|
|AdminState|物理的な接続性|Link|ExpressRoute Direct|
|LineProtocol|物理的な接続性|Link|ExpressRoute Direct|
|RxLightLevel|物理的な接続性|<ui><li>リンク</ui></li><ui><li>レーン</ui></li>|ExpressRoute Direct|
|TxLightLevel|物理的な接続性|<ui><li>リンク</ui></li><ui><li>レーン</ui></li>|ExpressRoute Direct|
>[!NOTE]
>*GlobalGlobalReachBitsInPerSecond* と *GlobalGlobalReachBitsOutPerSecond* の使用は、少なくとも 1 つの Global Reach 接続が確立されている場合にのみ表示されます。
>

## <a name="circuits-metrics"></a>回線のメトリック

### <a name="bits-in-and-out---metrics-across-all-peerings"></a>ビットのインとアウト - すべてのピアリング全体でのメトリック

特定の ExpressRoute 回線上のすべてのピアリングを超えてメトリックを表示できます。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg" alt-text="回線のメトリック":::

### <a name="bits-in-and-out---metrics-per-peering"></a>ビットのインとアウト - ピアリングごとのメトリック

プライベート、パブリック、Microsoft ピアリングのメトリック (ビット/秒) を表示できます。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg" alt-text="ピアリングごとのメトリック":::

### <a name="bgp-availability---split-by-peer"></a>BGP の可用性 - ピアによる分割  

ピアリングとピア (プライマリとセカンダリの ExpressRoute ルーター) 全体で、BGP の可用性はほぼリアルタイムで表示できます。 このダッシュボードには、プライベート ピアリング用のプライマリ BGP セッションはアップ状態であり、プライベート ピアリング用の 2 番目の BGP セッションはダウンしていると表示されます。 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erBgpAvailabilityMetrics.jpg" alt-text="ピアごとの BGP の可用性":::

### <a name="arp-availability---split-by-peering"></a>ARP の可用性 - ピアリングによる分割  

ピアリングとピア (プライマリとセカンダリの ExpressRoute ルーター) 全体で、[ARP](./expressroute-troubleshooting-arp-resource-manager.md) の可用性はほぼリアルタイムで表示できます。 このダッシュボードには、両方のピアを超えてプライベート ピアリング ARP セッションはアップ状態ですが、ピアリング間の Microsoft ピアリングに対しては完全にダウンしていると表示されます。 既定の集計 (平均) は、両方のピアとの間に使用されていました。  

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erArpAvailabilityMetrics.jpg" alt-text="ピアごとの ARP の可用性":::

## <a name="expressroute-direct-metrics"></a>ExpressRoute Direct メトリック

### <a name="admin-state---split-by-link"></a>管理状態 - リンクによる分割

ExpressRoute Direct ポート ペアのリンクごとに管理状態を表示できます。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/adminstate-per-link.jpg" alt-text="ER Direct 管理状態":::

### <a name="bits-in-per-second---split-by-link"></a>1 秒あたりのビット イン - リンクで分割

ExpressRoute Direct ポート ペアの両方のリンクを対象に、1 秒あたりのビット インを表示できます。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/bits-in-per-second-per-link.jpg" alt-text="ER Direct の 1 秒あたりのビット イン":::

### <a name="bits-out-per-second---split-by-link"></a>1 秒あたりのビット アウト - リンクで分割

ExpressRoute Direct ポート ペアの両方のリンクを対象に、1 秒あたりのビット アウトも表示できます。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/bits-out-per-second-per-link.jpg" alt-text="ER Direct の 1 秒あたりのビット アウト":::

### <a name="line-protocol---split-by-link"></a>回線プロトコル - リンクによる分割

ExpressRoute Direct ポート ペアのリンクごとに回線プロトコルを表示できます。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/line-protocol-per-link.jpg" alt-text="ER Direct 回線プロトコル":::

### <a name="rx-light-level---split-by-link"></a>Rx ライト レベル - リンクによる分割

ポート別の Rx ライト レベル (ExpressRoute Direct ポートで **受信** するライト レベル) を表示できます。 正常な Rx ライト レベルは通常、-10 から 0 dBm までの範囲に収まります。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/rxlight-level-per-link.jpg" alt-text="ER Direct 回線 Rx ライト レベル":::

### <a name="tx-light-level---split-by-link"></a>Tx ライト レベル - リンクによる分割

ポート別の Tx ライト レベル (ExpressRoute Direct ポートで **送信** するライト レベル) を表示できます。 正常な Tx ライト レベルは一般に、-10 から 0 dBm までの範囲に収まります。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/txlight-level-per-link.jpg" alt-text="ER Direct 回線 Tx ライト レベル":::

## <a name="expressroute-virtual-network-gateway-metrics"></a>ExpressRoute 仮想ネットワーク ゲートウェイ メトリック

### <a name="cpu-utilization---split-instance"></a>CPU 使用率 - インスタンスの分割

ゲートウェイ インスタンスの CPU 使用率を表示できます。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/cpu-split.jpg" alt-text="CPU 分割":::

### <a name="packets-per-second---split-by-instance"></a>1 秒あたりのパケット数 - インスタンスで分割

ゲートウェイを通過する 1 秒あたりのパケット数を表示できます。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/pps-split.jpg" alt-text="1 秒あたりのパケット数 - 分割":::

### <a name="count-of-routes-advertised-to-peer---split-by-instance"></a>ピアにアドバタイズされたルートの数 - インスタンスで分割

ExpressRoute 回線にアドバタイズされたルートの数を表示できます。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/count-of-routes-advertised-to-peer.png" alt-text="ピアにアドバタイズされたルートの数":::

### <a name="count-of-routes-learned-from-peer---split-by-instance"></a>ピアから学習したルートの数 - インスタンスで分割

ExpressRoute 回線から受け取ったルートの数を表示できます。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/count-of-routes-learned-from-peer.png" alt-text="ピアから学習したルートの数":::

### <a name="frequency-of-routes-change---split-by-instance"></a>ルート変更の頻度 - インスタンスで分割

ゲートウェイでルートが変更された頻度を表示できます。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/frequency-of-routes-changed.png" alt-text="ルートが変更された頻度":::

### <a name="number-of-vms-in-the-virtual-network"></a>仮想ネットワーク内の VM の数

仮想ネットワーク内の仮想マシンの数を表示できます。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/number-of-virtual-machines-virtual-network.png" alt-text="仮想ネットワーク内の仮想マシンの数":::

## <a name="expressroute-gateway-connections-in-bitsseconds"></a>ExpressRoute ゲートウェイの接続 (ビット/秒)

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erconnections.jpg" alt-text="ゲートウェイの接続":::

## <a name="alerts-for-expressroute-gateway-connections"></a>ExpressRoute ゲートウェイの接続のアラート

1. アラートを構成するには、**Azure Monitor** に移動して、 **[アラート]** を選択します。

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/eralertshowto.jpg" alt-text="alerts":::
2. **[+ ターゲットの選択]** をクリックし、ExpressRoute ゲートウェイの接続リソースを選択します。

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alerthowto2.jpg" alt-text="target":::
3. アラートの詳細を定義します。

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alerthowto3.jpg" alt-text="アクション グループ":::
4. アクション グループを定義して追加します。

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/actiongroup.png" alt-text="アクション グループの追加":::

## <a name="alerts-based-on-each-peering"></a>各ピアリングに基づくアラート

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/basedpeering.jpg" alt-text="各ピアリング":::

## <a name="configure-alerts-for-activity-logs-on-circuits"></a>回線でアクティビティ ログのアラートを構成する

**[アラートの条件]** で、[シグナルの種類] に **[アクティビティ ログ]** を選択し、[シグナル] を選択することができます。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alertshowto6activitylog.jpg" alt-text="アクティビティ ログ":::

## <a name="additional-metrics-in-log-analytics"></a>Log Analytics の追加のメトリック

ExpressRoute メトリックは、ExpressRoute 回線に移動し、 *[ログ]* タブを選択する方法でも表示できます。クエリを実行するメトリックについては、出力に以下の列が含まれます。

|**列**|**Type**|**説明**|
| --- | --- | --- |
|TimeGrain|string|PT1M (メトリック値が 1 分ごとにプッシュされます)|
|Count|real|通常は 2 に等しい (各 MSEE により、1 分ごとにメトリック値が 1 つプッシュされます)|
|最小値|real|2 つの MSEE によってプッシュされる 2 つのメトリック値の最小値|
|最大値|real|2 つの MSEE によってプッシュされる 2 つのメトリック値の最大値|
|Average|real|(最小値 + 最大値)/2 に等しい|
|合計|real|両方の MSEE からの 2 つのメトリック値の合計 (クエリが実行されたメトリックの場合、メインの値に焦点が当たる)|
  
## <a name="next-steps"></a>次のステップ

ExpressRoute 接続を構成します。
  
* [回線の作成と変更](expressroute-howto-circuit-arm.md)
* [ピアリング構成の作成と変更](expressroute-howto-routing-arm.md)
* [ExpressRoute 回線への VNet のリンク](expressroute-howto-linkvnet-arm.md)
