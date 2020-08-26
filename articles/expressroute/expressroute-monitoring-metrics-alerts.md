---
title: 'Azure ExpressRoute: 監視、メトリック、およびアラート'
description: Azure 全体のすべてのメトリック、アラート、診断ログを一元的に管理できる Azure Monitor を使用した、Azure ExpressRoute の監視、メトリクス、およびアラートについて説明します。
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: how-to
ms.date: 08/22/2019
ms.author: cherylmc
ms.openlocfilehash: 49e5acb7fc0cfe947d846f2943fb5071d6554ea5
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2020
ms.locfileid: "88192472"
---
# <a name="expressroute-monitoring-metrics-and-alerts"></a>ExpressRoute の監視、メトリック、およびアラート

この記事では、Azure Monitor を使用した ExpressRoute の監視、メトリック、およびアラートについて説明します。 Azure Monitor を使用すると、Azure 全体のすべてのメトリック、アラート、診断ログを一元的に管理できます。
 
>[!NOTE]
>**クラシック メトリック**の使用はお勧めしません。
>

## <a name="expressroute-metrics"></a>ExpressRoute メトリック

**[メトリック]** を表示するには、 *[Azure Monitor]* ページに移動して *[メトリック]* をクリックします。 **[ExpressRoute]** メトリックを表示するには、リソースの種類を *[ExpressRoute 回線]* でフィルターを適用します。 **[Global Reach]** メトリックを表示するには、リソースの種類を *[ExpressRoute 回線]* でフィルターを適用し、Global Reach が有効になっている ExpressRoute 回線リソースを選択します。 **ExpressRoute Direct** メトリックを表示するには、 *[ExpressRoute ポート]* でリソースの種類をフィルターします。 

メトリックを選択すると、既定の集計が適用されます。 必要に応じて、さまざまなディメンションでメトリックを表示する分割を適用できます。

### <a name="available-metrics"></a>使用可能なメトリック
|**メトリック**|**カテゴリ**|**ディメンション**|**機能**|
| --- | --- | --- | --- |
|ARP の可用性|可用性|<ui><li>ピア (プライマリ/セカンダリの ExpressRoute ルーター)</ui></li><ui><li> ピアリングの種類 (プライベート/パブリック/Microsoft)</ui></li>|ExpressRoute|
|BGP の可用性|可用性|<ui><li> ピア (プライマリ/セカンダリの ExpressRoute ルーター)</ui></li><ui><li> ピアリングの種類</ui></li>|ExpressRoute|
|BitsInPerSecond (受信ビット数/秒)|トラフィック|<ui><li> ピアリングの種類 (ExpressRoute)</ui></li><ui><li>リンク (ExpressRoute Direct)</ui></li>| <li> ExpressRoute</li><li>ExpressRoute Direct|
|BitsOutPerSecond (送信ビット数/秒)|トラフィック| <ui><li>ピアリングの種類 (ExpressRoute)</ui></li><ui><li> リンク (ExpressRoute Direct) | <ui><li>ExpressRoute<ui><li>ExpressRoute Direct</ui></li> |
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

![回線のメトリック](./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg)

### <a name="bits-in-and-out---metrics-per-peering"></a>ビットのインとアウト - ピアリングごとのメトリック

プライベート、パブリック、Microsoft ピアリングのメトリック (ビット/秒) を表示できます。

![ピアリングごとのメトリック](./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg) 

### <a name="bgp-availability---split-by-peer"></a>BGP の可用性 - ピアによる分割  

ピアリングとピア (プライマリとセカンダリの ExpressRoute ルーター) 全体で、BGP の可用性はほぼリアルタイムで表示できます。 このダッシュボードには、プライベート ピアリング用のプライマリ BGP セッションはアップ状態であり、プライベート ピアリング用の 2 番目の BGP セッションはダウンしていると表示されます。 

![ピアごとの BGP の可用性](./media/expressroute-monitoring-metrics-alerts/erBgpAvailabilityMetrics.jpg) 

### <a name="arp-availability---split-by-peering"></a>ARP の可用性 - ピアリングによる分割  

ピアリングとピア (プライマリとセカンダリの ExpressRoute ルーター) 全体で、[ARP](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-arp-resource-manager) の可用性はほぼリアルタイムで表示できます。 このダッシュボードには、両方のピアを超えてプライベート ピアリング ARP セッションはアップ状態ですが、ピアリング間の Microsoft ピアリングに対しては完全にダウンしていると表示されます。 既定の集計 (平均) は、両方のピアとの間に使用されていました。  

![ピアごとの ARP の可用性](./media/expressroute-monitoring-metrics-alerts/erArpAvailabilityMetrics.jpg) 

## <a name="expressroute-direct-metrics"></a>ExpressRoute Direct メトリック

### <a name="admin-state---split-by-link"></a>管理状態 - リンクによる分割
ExpressRoute Direct ポート ペアのリンクごとに管理状態を表示できます。

![er direct 管理状態](./media/expressroute-monitoring-metrics-alerts/adminstate-per-link.jpg)

### <a name="bits-in-per-second---split-by-link"></a>1 秒あたりのビット イン - リンクで分割
ExpressRoute Direct ポート ペアの両方のリンクを対象に、1 秒あたりのビット インを表示できます。 

![er direct の 1 秒あたりのビット イン](./media/expressroute-monitoring-metrics-alerts/bits-in-per-second-per-link.jpg)

### <a name="bits-out-per-second---split-by-link"></a>1 秒あたりのビット アウト - リンクで分割
ExpressRoute Direct ポート ペアの両方のリンクを対象に、1 秒あたりのビット アウトも表示できます。 

![er direct の 1 秒あたりのビット アウト](./media/expressroute-monitoring-metrics-alerts/bits-out-per-second-per-link.jpg)

### <a name="line-protocol---split-by-link"></a>回線プロトコル - リンクによる分割
ExpressRoute Direct ポート ペアのリンクごとに回線プロトコルを表示できます。

![er direct 回線プロトコル](./media/expressroute-monitoring-metrics-alerts/line-protocol-per-link.jpg)

### <a name="rx-light-level---split-by-link"></a>Rx ライト レベル - リンクによる分割
ポート別の Rx ライト レベル (ExpressRoute Direct ポートで**受信**するライト レベル) を表示できます。 正常な Rx ライト レベルは通常、-10 から 0 dBm までの範囲に収まります。

![er direct 回線 Rx ライト レベル](./media/expressroute-monitoring-metrics-alerts/rxlight-level-per-link.jpg)

### <a name="tx-light-level---split-by-link"></a>Tx ライト レベル - リンクによる分割
ポート別の Tx ライト レベル (ExpressRoute Direct ポートで**送信**するライト レベル) を表示できます。 正常な Tx ライト レベルは一般に、-10 から 0 dBm までの範囲に収まります。

![er direct 回線 Rx ライト レベル](./media/expressroute-monitoring-metrics-alerts/txlight-level-per-link.jpg)

## <a name="expressroute-gateway-connections-in-bitsseconds"></a>ExpressRoute ゲートウェイの接続 (ビット/秒)

![ゲートウェイの接続](./media/expressroute-monitoring-metrics-alerts/erconnections.jpg )

## <a name="alerts-for-expressroute-gateway-connections"></a>ExpressRoute ゲートウェイの接続のアラート

1. アラートを構成するには、**Azure Monitor** に移動し、 **[アラート]** をクリックします。

   ![alerts](./media/expressroute-monitoring-metrics-alerts/eralertshowto.jpg)

2. **[+ ターゲットの選択]** をクリックし、ExpressRoute ゲートウェイの接続リソースを選択します。

   ![ターゲット (target)]( ./media/expressroute-monitoring-metrics-alerts/alerthowto2.jpg)
3. アラートの詳細を定義します。

   ![アクション グループ](./media/expressroute-monitoring-metrics-alerts/alerthowto3.jpg)

4. アクション グループを定義して追加します。

   ![アクション グループの追加](./media/expressroute-monitoring-metrics-alerts/actiongroup.png)

## <a name="alerts-based-on-each-peering"></a>各ピアリングに基づくアラート

 ![what](./media/expressroute-monitoring-metrics-alerts/basedpeering.jpg)

## <a name="configure-alerts-for-activity-logs-on-circuits"></a>回線でアクティビティ ログのアラートを構成する

**[アラートの条件]** で、[シグナルの種類] に **[アクティビティ ログ]** を選択し、[シグナル] を選択することができます。

  ![another](./media/expressroute-monitoring-metrics-alerts/alertshowto6activitylog.jpg)
  
## <a name="next-steps"></a>次のステップ

ExpressRoute 接続を構成します。
  
  * [回線の作成と変更](expressroute-howto-circuit-arm.md)
  * [ピアリング構成の作成と変更](expressroute-howto-routing-arm.md)
  * [ExpressRoute 回線への VNet のリンク](expressroute-howto-linkvnet-arm.md)
