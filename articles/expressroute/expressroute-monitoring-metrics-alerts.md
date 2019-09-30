---
title: 監視、メトリック、およびアラート - Azure ExpressRoute | Microsoft Docs
description: このページでは、ExpressRoute の監視について説明しています。
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 92ec03e20fb6e681a0afd14048449ad004ebca0c
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/23/2019
ms.locfileid: "69991462"
---
# <a name="expressroute-monitoring-metrics-and-alerts"></a>ExpressRoute の監視、メトリック、およびアラート

この記事では、Azure Monitor を使用した ExpressRoute の監視、メトリック、およびアラートについて説明します。 Azure Monitor を使用すると、Azure 全体のすべてのメトリック、アラート、診断ログを一元的に管理できます。
 
>[!NOTE]
>**クラシック メトリック**の使用はお勧めしません。
>

## <a name="circuit-metrics"></a>回線のメトリック

**[メトリック]** に移動するには、監視する回線の ExpressRoute ページをクリックします。 **[監視]** の下に **[メトリック]** が表示されます。 以下に一覧表示されているメトリックから選択します。 既定の集計が適用されます。 必要に応じて、さまざまなディメンションでメトリックを表示する分割を適用できます。

### <a name="metrics-available"></a>使用可能なメトリック: 
* **可用性** 
    * ARP の可用性
      * 使用可能なディメンション:
        * ピア (プライマリ/セカンダリの ExpressRoute ルーター)
        * ピアリングの種類 (プライベート/パブリック/Microsoft)
    * BGP の可用性
      * 使用可能なディメンション:
        * ピア (プライマリ/セカンダリの ExpressRoute ルーター)
        * ピアリングの種類 (プライベート/パブリック/Microsoft)
* **トラフィック**
    * BitsInPerSecond (受信ビット数/秒)
      * 使用可能なディメンション:
        * ピアリングの種類 (プライベート/パブリック/Microsoft)
    * BitsOutPerSecond (送信ビット数/秒)
      * 使用可能なディメンション:
        * ピアリングの種類 (プライベート/パブリック/Microsoft)
    * GlobalReachBitsInPerSecond
      * 使用可能なディメンション:
        * ピアリングされた回線 Skey (サービス キー)
    * GlobalReachBitsOutPerSecond
      * 使用可能なディメンション:
        * ピアリングされた回線 Skey (サービス キー)

>[!NOTE]
>*GlobalGlobalReachBitsInPerSecond* と *GlobalGlobalReachBitsOutPerSecond* の使用は、少なくとも 1 つの Global Reach 接続が確立されている場合にのみ表示されます。
>

## <a name="bits-in-and-out---metrics-across-all-peerings"></a>ビットのインとアウト - すべてのピアリング全体でのメトリック

特定の ExpressRoute 回線上のすべてのピアリングを超えてメトリックを表示できます。

![回線のメトリック](./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg)

## <a name="bits-in-and-out---metrics-per-peering"></a>ビットのインとアウト - ピアリングごとのメトリック

プライベート、パブリック、Microsoft ピアリングのメトリック (ビット/秒) を表示できます。

![ピアリングごとのメトリック](./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg) 

## <a name="bgp-availability---split-by-peer"></a>BGP の可用性 - ピアによる分割  

ピアリングとピア (プライマリとセカンダリの ExpressRoute ルーター) 全体で、BGP の可用性はほぼリアルタイムで表示できます。 このダッシュボードには、プライベート ピアリング用のプライマリ BGP セッションはアップ状態であり、プライベート ピアリング用の 2 番目の BGP セッションはダウンしていると表示されます。 

![ピアごとの BGP の可用性](./media/expressroute-monitoring-metrics-alerts/erBgpAvailabilityMetrics.jpg) 

## <a name="arp-availability---split-by-peering"></a>ARP の可用性 - ピアリングによる分割  

ピアリングとピア (プライマリとセカンダリの ExpressRoute ルーター) 全体で、[ARP](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-arp-resource-manager) の可用性はほぼリアルタイムで表示できます。 このダッシュボードには、両方のピアを超えてプライベート ピアリング ARP セッションはアップ状態ですが、ピアリング間の Microsoft ピアリングに対しては完全にダウンしていると表示されます。 既定の集計 (平均) は、両方のピアとの間に使用されていました。  

![ピアごとの ARP の可用性](./media/expressroute-monitoring-metrics-alerts/erArpAvailabilityMetrics.jpg) 

## <a name="expressroute-gateway-connections-in-bitsseconds"></a>ExpressRoute ゲートウェイの接続 (ビット/秒)

![ゲートウェイの接続](./media/expressroute-monitoring-metrics-alerts/erconnections.jpg ) 

## <a name="alerts-for-expressroute-gateway-connections"></a>ExpressRoute ゲートウェイの接続のアラート

1. アラートを構成するには、**Azure Monitor** に移動し、 **[アラート]** をクリックします。

   ![alerts](./media/expressroute-monitoring-metrics-alerts/eralertshowto.jpg)

2. **[+ ターゲットの選択]** をクリックし、ExpressRoute ゲートウェイの接続リソースを選択します。

   ![target]( ./media/expressroute-monitoring-metrics-alerts/alerthowto2.jpg)
3. アラートの詳細を定義します。

   ![アクション グループ](./media/expressroute-monitoring-metrics-alerts/alerthowto3.jpg)

4. アクション グループを定義して追加します。

   ![アクション グループの追加](./media/expressroute-monitoring-metrics-alerts/actiongroup.png)

## <a name="alerts-based-on-each-peering"></a>各ピアリングに基づくアラート

 ![what](./media/expressroute-monitoring-metrics-alerts/basedpeering.jpg)

## <a name="configure-alerts-for-activity-logs-on-circuits"></a>回線でアクティビティ ログのアラートを構成する

**[アラートの条件]** で、[シグナルの種類] に **[アクティビティ ログ]** を選択し、[シグナル] を選択することができます。

  ![another](./media/expressroute-monitoring-metrics-alerts/alertshowto6activitylog.jpg)
  
## <a name="next-steps"></a>次の手順

ExpressRoute 接続を構成します。
  
  * [回線の作成と変更](expressroute-howto-circuit-arm.md)
  * [ピアリング構成の作成と変更](expressroute-howto-routing-arm.md)
  * [ExpressRoute 回線への VNet のリンク](expressroute-howto-linkvnet-arm.md)
