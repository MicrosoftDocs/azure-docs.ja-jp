---
title: ネットワークの分析情報を使用した Azure ExpressRoute の分析情報
description: ネットワークの分析情報を使用した Azure ExpressRoute の分析情報について説明します。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 03/23/2021
ms.author: duau
ms.openlocfilehash: 7033ea6a1ba6d85f9aa15e14bb9577b2439c59a8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105050341"
---
# <a name="azure-expressroute-insights-using-network-insights"></a>ネットワークの分析情報を使用した Azure ExpressRoute の分析情報

この記事では、ExpressRoute のメトリックと構成をすべて 1 か所で表示するためにネットワークの分析情報がどのように役立つかについて説明します。 ネットワークの分析情報を使用すると、追加のセットアップを完了しなくても、重要な ExpressRoute 情報を含むトポロジ マップと正常性ダッシュボードを表示できます。

:::image type="content" source="./media/expressroute-network-insights/monitor-landing-page.png" alt-text="ExpressRoute 監視のランディング ページのスクリーンショット。" lightbox="./media/expressroute-network-insights/monitor-landing-page-expanded.png":::

## <a name="visualize-functional-dependencies"></a>機能依存関係の視覚化

このソリューションを表示するには、 *[Azure Monitor]* ページに移動し、 *[ネットワーク]* を選択して、 *[ExpressRoute 回線]* カードを選択します。 次に、表示する回線のトポロジ ボタンを選択します。

機能依存関係ビューでは、ExpressRoute のセットアップが明確に示され、さまざまな ExpressRoute コンポーネント (ピアリング、接続、ゲートウェイ) 間の関係が描写されます。

:::image type="content" source="./media/expressroute-network-insights/topology-view.png" alt-text="ネットワーク分析情報のトポロジ ビューのスクリーンショット。" lightbox="./media/expressroute-network-insights/topology-view-expanded.png":::

トポロジ マップ内のコンポーネントにマウス ポインターを合わせると、構成情報が表示されます。 たとえば、ExpressRoute ピアリング コンポーネントにマウス ポインターを合わせると、回線帯域幅や Global Reach の有効化などの詳細が表示されます。

:::image type="content" source="./media/expressroute-network-insights/topology-hovered.png" alt-text="トポロジ ビューのリソースにマウス ポインターを置いたスクリーンショット。" lightbox="./media/expressroute-network-insights/topology-hovered-expanded.png":::

## <a name="view-a-detailed-and-pre-loaded-metrics-dashboard"></a>詳細および事前に読み込まれたメトリックのダッシュボードを表示する

機能依存関係ビューを使用して ExpressRoute セットアップのトポロジを確認したら、 **[詳細なメトリックの表示]** を選択して詳細メトリックのビューに移動し、回線のパフォーマンスを把握します。 このビューには、リンクされたリソースの一覧と、重要な ExpressRoute メトリックの詳細なダッシュボードが用意されています。

**[リンクされたリソース]** セクションには、接続されている ExpressRoute ゲートウェイと構成されているピアリングが一覧表示されます。これを選択すると、対応するリソース ページに移動できます。

:::image type="content" source="./media/expressroute-network-insights/linked-resources.png" alt-text="監視ページのリンクされたリソースのスクリーンショット。":::


**[ExpressRoute Metrics]\(ExpressRoute のメトリック\)** セクションには、 **[可用性]** 、 **[スループット]** 、 **[Packet Drops]\(パケットの破棄\)** 、 **[Gateway Metrics]\(ゲートウェイ メトリック\)** のカテゴリにまたがる重要な回線のメトリックのグラフが含まれています。

### <a name="availability"></a>可用性

[*可用性*] タブでは、ARP と BGP の可用性を追跡し、回線全体と個々の接続 (プライマリとセカンダリ) の両方のデータがプロットされます。 

:::image type="content" source="./media/expressroute-network-insights/arp-bgp-availability.png" alt-text="可用性メトリック グラフのスクリーンショット。" lightbox="./media/expressroute-network-insights/arp-bgp-availability-expanded.png":::

### <a name="throughput"></a>スループット

同様に、[*スループット*] タブには、イングレスとエグレス トラフィックの合計スループットがビット/秒でプロットされます。 また、個々の接続と構成されたピアリングそれぞれの種類のスループットを表示することもできます。

:::image type="content" source="./media/expressroute-network-insights/throughput.png" alt-text="スループット メトリック グラフのスクリーンショット。" lightbox="./media/expressroute-network-insights/throughput-expanded.png":::

### <a name="packet-drops"></a>パケットの破棄

*[Packet Drops]\(パケットの破棄\)* タブには、回線経由のイングレスとエグレス トラフィックで破棄されたビット/秒がプロットされます。 このタブを使用すると、回線の帯域幅が定常的に必要になる場合や超過する場合に発生する可能性のあるパフォーマンスの問題を簡単に監視できます。

:::image type="content" source="./media/expressroute-network-insights/dropped-packets.png" alt-text="破棄されたパケットのグラフのスクリーンショット。" lightbox="./media/expressroute-network-insights/dropped-packets-expanded.png":::

### <a name="gateway-metrics"></a>ゲートウェイ メトリック

最後に、[Gateway Metrics]\(ゲートウェイ メトリック\) タブには、選択した ExpressRoute ゲートウェイ (リンクされたリソース セクション) の主要なメトリックのグラフが表示されます。 特定の仮想ネットワークへの接続を監視する必要がある場合は、このタブを使用します。

:::image type="content" source="./media/expressroute-network-insights/gateway-metrics.png" alt-text="ゲートウェイのスループットと CPU メトリックのスクリーンショット。" lightbox="./media/expressroute-network-insights/gateway-metrics-expanded.png":::

## <a name="next-steps"></a>次のステップ

ExpressRoute 接続を構成します。
  
* [Azure ExpressRoute](expressroute-introduction.md)、[ネットワーク分析情報](../azure-monitor/insights/network-insights-overview.md)、[Network Watcher](../network-watcher/network-watcher-monitoring-overview.md) の詳細を参照する
* [回線の作成と変更](expressroute-howto-circuit-arm.md)
* [ピアリング構成の作成と変更](expressroute-howto-routing-arm.md)
* [ExpressRoute 回線への VNet のリンク](expressroute-howto-linkvnet-arm.md)
* [メトリックをカスタマイズ](expressroute-monitoring-metrics-alerts.md)して[接続モニター](../network-watcher/connection-monitor-overview.md)を作成する
