---
title: Azure Monitor の分析情報を使用した Virtual WAN の監視
description: この記事では、Azure Monitor の分析情報を使用して Azure Virtual WAN を監視する方法について説明します。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 6ead00a0979d81ef11ac81fb13a1abe31317691d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100571321"
---
# <a name="azure-monitor-insights-for-virtual-wan"></a>Virtual WAN に関する Azure Monitor の分析情報

Azure Virtual WAN に関する [Azure Monitor の分析情報](../azure-monitor/insights/network-insights-overview.md)を使用すると、ユーザーやオペレーターは、自動検出されたトポロジ マップを通じて仮想 WAN の状態とステータスを確認できます。 リソースの状態とステータスがマップに重ねて表示され、仮想 WAN の全体的な正常性のスナップショット ビューが提供されます。 Virtual WAN ポータルのリソース構成ページにワンクリックでアクセスすることで、マップのリソースにナビゲーションすることができます。

Virtual WAN リソースレベルのメトリックは、事前にパッケージ化された Virtual WAN メトリック ブックを使用して収集および提示されます。 このブックには、仮想 WAN、ハブ、ゲートウェイ、接続レベルのメトリックが表示されます。 この記事では、Virtual WAN に関する Azure Monitor の分析情報を使用して、Virtual WAN のトポロジとメトリックのすべてを 1 か所で表示する手順について説明します。

> [!NOTE]
> **[インサイト]** メニュー オプションは、Virtual WAN ポータルの **[監視]** にあります。 Azure Monitor for Networks を使用して、Virtual WAN トポロジとメトリック ブックにアクセスすることができます。 詳細については、「[Azure Monitor for Networks](../azure-monitor/insights/network-insights-overview.md)」を参照してください。 
>

## <a name="before-you-begin"></a>開始する前に

この記事のステップを完了するには、1 つまたは複数のハブを持つ仮想 WAN が必要です。 仮想 WAN とハブを作成するには、次の記事のステップに従います。

* [仮想 WAN を作成する](virtual-wan-site-to-site-portal.md#openvwan)
* [ハブを作成する](virtual-wan-site-to-site-portal.md#hub)

## <a name="view-vwan-topology"></a><a name="topology"></a>VWAN のトポロジを表示する

**Azure portal** > **Virtual WAN** の順に移動します。 左側のウィンドウの **[モニター]** メニューで、 **[分析情報 (プレビュー)]** を選択します。 **[分析情報]** ビューが表示されます。 ここには、Virtual WAN の依存関係マップと、 **[メトリック]** の概要を示すミニ ブックが表示されます。

"**図 1:[モニター] > [分析情報] メニュー**

:::image type="content" source="./media/azure-monitor-insights/monitor-menu.png" alt-text="[分析情報 (プレビュー)] ビューを示すスクリーンショット。" lightbox="./media/azure-monitor-insights/monitor-menu.png":::

**[分析情報]** ビューで、自動検出された Virtual WAN リソースを表示できます。 このリソースには、ハブ、ゲートウェイ、ファイアウォール、接続とスポークの仮想ネットワーク、サードパーティの NVA、エンドツーエンドの Virtual WAN 内のブランチが含まれます。 例については、**図 2** を参照してください。

リソースの状態とステータスは、色分けされ、マップのリソース アイコンに重ねて表示されます。 ハブの容量やゲートウェイの使用率などの Virtual WAN メトリックの概要が、ミニ ブックのウィンドウの右側に表示されます。

"**図 2:[分析情報] ビュー**

:::image type="content" source="./media/azure-monitor-insights/insights-view.png" alt-text="[分析情報] ビューを示すスクリーンショット。" lightbox="./media/azure-monitor-insights/insights-view.png":::

## <a name="dependency-view"></a><a name="dependency"></a>[依存関係] ビュー

Virtual WAN の **[依存関係]** ビューによって、ハブ アンド スポーク アーキテクチャに幅広く編成されているすべての Virtual WAN リソースの相互接続ビューを視覚化できます。

"**図 3:VWAN の [依存関係] ビュー**

:::image type="content" source="./media/azure-monitor-insights/dependency-map.png" alt-text="[依存関係] ビューを示すスクリーンショット。" lightbox="./media/azure-monitor-insights/dependency-map.png":::

**[依存関係]** ビュー マップには、接続されたグラフとして次のリソースが表示されます。

* さまざまな Azure リージョンにまたがる Virtual WAN ハブ。
* ハブに直接接続されているスポークの仮想ネットワーク。
* それぞれの ExpressRoute、S2S および P2S 接続、仮想ネットワーク ゲートウェイを介して各ハブに接続されている、VPN および Azure ExpressRoute のブランチ サイトと P2S ユーザー。
* ハブ (セキュリティで保護されたハブ) にデプロイされた Azure Firewall (サードパーティのファイアウォール プロキシを含む)。
* スポークの仮想ネットワークにデプロイされているサードパーティの NVA (ネットワーク仮想アプライアンス)。

また、依存関係マップには、間接的に接続された仮想ネットワーク (Virtual WAN のスポークの仮想ネットワークとピアリングされた仮想ネットワーク) も表示されます。

依存関係マップを使用すると、各リソースの構成設定に簡単に移動できます。 たとえば、ハブ リソースをマウスでポイントして、ハブ領域やハブ プレフィックスなどの基本的なリソース構成を表示できます。 右クリックして、ハブリ ソースの Azure portal ページにアクセスします。

"**図 4:リソース固有の情報に移動する**

:::image type="content" source="./media/azure-monitor-insights/resource-information.png" alt-text="リソース固有の情報に移動する方法を示すスクリーンショット。":::

**[依存関係]** ビューの検索とフィルター バーを使用すると、グラフ全体を簡単に検索することができます。 さまざまなフィルターが、検索を特定のパスや状態に絞り込むのに役立ちます。

**図 5:検索とフィルター処理**

:::image type="content" source="./media/azure-monitor-insights/search-filter.png" alt-text="検索とフィルター バーを示すスクリーンショット。" lightbox="./media/azure-monitor-insights/search-filter.png":::

## <a name="detailed-metrics"></a><a name="detailed"></a>詳細メトリック

**[詳細なメトリックの表示]** を選択して、詳細な **[メトリック]** ページにアクセスできます。 **[メトリック]** ページは、個別のタブが事前に構成されたダッシュボードです。 これらのタブにより、仮想 WAN レベル、ハブ レベル、個々の接続レベルでの仮想 WAN のリソース容量、パフォーマンス、使用率に関する分析情報が提供されます。

**図 6:詳細メトリック ダッシュボード**

:::image type="content" source="./media/azure-monitor-insights/detailed-metrics.png" alt-text="詳細メトリック ダッシュボードを示すスクリーンショット。" lightbox="./media/azure-monitor-insights/detailed-metrics.png":::

## <a name="next-steps"></a>次のステップ

* 詳細については、「[Azure Monitor のメトリック](../azure-monitor/essentials/data-platform-metrics.md)」を参照してください。
* すべての Virtual WAN メトリックの詳細については、[Virtual WAN のログとメトリック](logs-metrics.md)に関する記事を参照してください。
