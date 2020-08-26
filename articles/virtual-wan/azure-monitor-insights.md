---
title: Azure Monitor の分析情報を使用した Azure Virtual WAN の監視
description: Azure Monitor の分析情報を使用した Virtual WAN の監視について説明します
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 07/22/2020
ms.author: cherylmc
ms.openlocfilehash: e3316b4a2255652972a0b9deef813f894f993589
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87836073"
---
# <a name="azure-monitor-insights-for-virtual-wan-preview"></a>Virtual WAN に関する Azure Monitor の分析情報 (プレビュー)

Virtual WAN に関する [Azure Monitor の分析情報](../azure-monitor/insights/network-insights-overview.md)を使用すると、ユーザーやオペレーターは、自動検出されたトポロジ マップを通じて Virtual WAN の状態とステータスを確認できます。 リソースの状態とステータスがマップに重ねて表示され、Virtual WAN の全体的な正常性のスナップショット ビューが提供されます。 Virtual WAN ポータルのリソース構成ページにワンクリックでアクセスすることで、マップのリソース ナビゲーションが有効になります。

Virtual WAN、ハブ、ゲートウェイ、接続レベルのメトリックが示される Virtual WAN の事前パッケージ済みメトリック ブックによって、Virtual WAN のリソース レベルのメトリックが収集されて表示されます。 この記事では、Virtual WAN に関する Azure Monitor の分析情報を使用して、Virtual WAN のトポロジとメトリックのすべてを 1 か所で表示する手順について説明します。

> [!NOTE]
> Virtual WAN ポータルの [分析情報] メニュー オプションは、ロールアウトの処理が行われています。Virtual WAN の [分析情報] メニューがロールアウトされている間、Virtual WAN のトポロジとメトリック ブックには、Azure Monitor for Networks を使用して直接アクセスできます。 詳細については、[Azure Monitor の分析情報](../azure-monitor/insights/network-insights-overview.md)に関する記事を参照してください。 
>

## <a name="before-you-begin"></a>開始する前に

この記事の手順では、1 つ以上のハブがある仮想 WAN が既にデプロイされていること想定します。 新しい仮想 WAN と新しいハブを作成するには、次の記事の手順に従います。

* [仮想 WAN を作成する](virtual-wan-site-to-site-portal.md#openvwan)
* [ハブを作成する](virtual-wan-site-to-site-portal.md#hub)

## <a name="view-vwan-topology"></a><a name="topology"></a>VWAN のトポロジを表示する

**[Azure portal] -> [仮想 WAN]** の左側にある **[モニター]** メニューで、 **[分析情報 (プレビュー)]** を選択します。 これにより、 **[分析情報] ビュー**が表示されます。ここには、Virtual WAN 依存関係マップと大まかなメトリック ミニブックが表示されます。

"**図 1:Monitor - 分析情報メニュー**

:::image type="content" source="./media/azure-monitor-insights/monitor-menu.png" alt-text="図" lightbox="./media/azure-monitor-insights/monitor-menu.png":::

**[分析情報]** ビューでは、**図 2** のように、エンド ツー エンドの Virtual WAN 内のハブ、ゲートウェイ、ファイアウォール、接続とスポーク VNet、サードパーティ製の NVA、ブランチなど、自動検出された Virtual WAN リソースを表示できます。

**リソースの状態**と**ステータス**は、色分けされ、マップのリソース アイコンに重ねて表示されます。 ハブの容量やゲートウェイの使用率など、Virtual WAN の大まかなメトリックが、ミニブックを介して右側に表示されます。

"**図 2:[分析情報] ビュー**

:::image type="content" source="./media/azure-monitor-insights/insights-view.png" alt-text="図" lightbox="./media/azure-monitor-insights/insights-view.png":::

## <a name="dependency-view"></a><a name="dependency"></a>[依存関係] ビュー

Virtual WAN の **[依存関係]** ビューでは、ハブ アンド スポーク アーキテクチャに幅広く編成されているすべての Virtual WAN リソースの相互接続ビューを視覚化できます。

"**図 3:VWAN の [依存関係] ビュー**

:::image type="content" source="./media/azure-monitor-insights/dependency-map.png" alt-text="依存関係マップ" lightbox="./media/azure-monitor-insights/dependency-map.png":::

[依存関係] ビュー マップには、接続されたグラフとして次のリソースが表示されます。

* さまざまな Azure リージョンにまたがる Virtual WAN ハブ。
* ハブに直接接続されているスポーク VNet。
* それぞれの ExpressRoute、S2S および P2S 接続、仮想ネットワーク ゲートウェイを介して各ハブに接続されている、VPN および ExpressRoute のブランチ サイトと P2S ユーザー。
* ハブ (セキュリティで保護されたハブ) にデプロイされた Azure Firewall (サードパーティのファイアウォール プロキシを含む)。
* スポーク VNet にデプロイされているサードパーティの NVA (ネットワーク仮想アプライアンス)。

また、依存関係マップには、間接的に接続された VNet (Virtual WAN スポーク VNet とピアリングされた VNet) も表示されます。

依存関係マップを使用すると、各リソースの構成設定に簡単に移動できます。 たとえば、ハブ リソースをマウスでポイントして、ハブ領域やハブ プレフィックスなどの基本的なリソース構成を表示できます。 右クリックして、ハブリ ソースの Azure portal ページにアクセスします。

"**図 4:リソース固有の情報に移動する**

:::image type="content" source="./media/azure-monitor-insights/resource-information.png" alt-text="リソース情報":::

[依存関係] ビューの検索とフィルター バーを使用すると、グラフ全体を簡単に検索することができます。 様々なフィルターを使用すると、検索を特定のパスや状態に絞り込むのに役立ちます。

**図 5:検索とフィルター処理**

:::image type="content" source="./media/azure-monitor-insights/search-filter.png" alt-text="検索とフィルター バー" lightbox="./media/azure-monitor-insights/search-filter.png":::

## <a name="detailed-metrics"></a><a name="detailed"></a>詳細メトリック

**[詳細なメトリックの表示]** を選択して、詳細な **[メトリック]** ページにアクセスできます。 [メトリック] ページは、Virtual WAN レベル、ハブ レベル、個々の接続での Virtual WAN のリソース容量、パフォーマンス、使用率に関する有益な分析情報が提供される個別のタブが事前に構成されたダッシュボードです。

**図 6:詳細メトリック ダッシュボード**

:::image type="content" source="./media/azure-monitor-insights/detailed-metrics.png" alt-text="詳細メトリック" lightbox="./media/azure-monitor-insights/detailed-metrics.png":::

## <a name="next-steps"></a>次のステップ

* Azure Monitor のメトリックの詳細については、「[Azure Monitor のメトリック](../azure-monitor/platform/data-platform-metrics.md)」を参照してください。
* すべての Virtual WAN メトリックの詳細については、[Virtual WAN のログとメトリック](logs-metrics.md)に関する記事を参照してください。
