---
title: 監視、メトリック、およびアラート - Azure ExpressRoute | Microsoft Docs
description: このページでは、ExpressRoute の監視について説明しています。
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: d78c110f3317f4dd9f16cbe243aeca437e9890a1
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58108799"
---
# <a name="expressroute-monitoring-metrics-and-alerts"></a>ExpressRoute の監視、メトリック、およびアラート

この記事では、Azure Monitor を使用した ExpressRoute の監視、メトリック、およびアラートについて説明します。 Azure Monitor を使用すると、Azure 全体のすべてのメトリック、アラート、診断ログを一元的に管理できます。
 
>[!NOTE]
>**クラシック メトリック**の使用はお勧めしません。
>

## <a name="circuit-metrics"></a>回線のメトリック

**[メトリック]** に移動するには、監視する回線の ExpressRoute ページをクリックします。 **[監視]** の下に **[メトリック]** が表示されます。 BitsInPerSecond または BitsOutPerSecond と、Aggregation を選択します。 必要に応じて、ピアリングの種類ごとにメトリックを表示する分割を適用できます。

![回線のメトリック](./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg)

## <a name="metrics-per-peering"></a>ピアリングごとのメトリック

プライベート、パブリック、Microsoft ピアリングのメトリック (ビット/秒) を表示できます。

![ピアリングごとのメトリック](./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg) 

## <a name="expressroute-gateway-connections-in-bitsseconds"></a>ExpressRoute ゲートウェイの接続 (ビット/秒)

![ゲートウェイの接続](./media/expressroute-monitoring-metrics-alerts/erconnections.jpg ) 

## <a name="alerts-for-expressroute-gateway-connections"></a>ExpressRoute ゲートウェイの接続のアラート

1. アラートを構成するには、**Azure Monitor** に移動し、**[アラート]** をクリックします。

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
