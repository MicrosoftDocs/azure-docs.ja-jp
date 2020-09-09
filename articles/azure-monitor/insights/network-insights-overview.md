---
title: Azure Monitor for Networks (プレビュー)
description: デプロイされたすべてのネットワーク リソースの正常性とメトリックを構成なしで包括的に把握できる、Azure Monitor for Network の簡単な概要。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/07/2019
ms.openlocfilehash: ccc5578944dc6eea9a62360045272896d78e2fac
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2020
ms.locfileid: "87128677"
---
# <a name="azure-monitor-for-networks-preview"></a>Azure Monitor for Networks (プレビュー)
Azure Monitor for Network では、デプロイされたすべてのネットワーク リソースの正常性とメトリックを構成なしで包括的に把握できます。 高度な検索機能を使用すると、リソースの依存関係を特定するのに役立ちます。たとえば、ホストされている Web サイト名を検索するだけで、Web サイトをホストしているリソースを特定できるというシナリオが可能になります。

Azure Monitor for Networks の **[概要]** ページでは、ネットワーク リソースのインベントリと、リソースの正常性やアラートを簡単に視覚化できます。 4 つの主要な機能領域があります。

- 検索とフィルター処理
- リソース正常性とメトリック
- 警告 
- [依存関係] ビュー

![[概要] ページ](media/network-insights-overview/overview.png)

## <a name="search-and-filtering"></a>検索とフィルター処理
リソース正常性とアラートのビューは、 **[サブスクリプション]** 、 **[リソース グループ]** 、 **[リソースの種類]** などのフィルターを使用してカスタマイズできます。 検索ボックスでは、リソース プロパティを使用して検索することができます。

検索ボックスは、リソースや関連するリソースを検索するために使用できます。 たとえば、パブリック IP がアプリケーション ゲートウェイに関連付けられているとします。 このパブリック IP の DNS 名を検索すると、パブリック IP と、関連付けられているアプリケーション ゲートウェイが特定されます。

![検索](media/network-insights-overview/search.png)


## <a name="resource-health-and-metric"></a>リソース正常性とメトリック
各タイルには、リソースの種類と、選択されたすべてのサブスクリプション全体でデプロイされているインスタンスの数、およびリソース正常性の状態が表示されます。 次の例では、デプロイされている 105 個の ER および VPN 接続があり、103 個は正常で、2 個は利用不可です。

![リソース ヘルス](media/network-insights-overview/resource-health.png)

利用不可の 2 つの ER および VPN 接続をクリックすると、メトリック ビューが起動します。 

![メトリック ビュー](media/network-insights-overview/metric-view.png)

グリッド ビューの各要素をクリックできます。 [正常性] アイコンをクリックすると、その接続のリソース正常性にリダイレクトされます。 [アラート] をクリックすると、その接続のアラートとメトリックのそれぞれのページにリダイレクトされます。 

## <a name="alerts"></a>警告
右側の **[アラート]** グリッドには、すべてのサブスクリプションで選択されたリソースに対して生成されたすべてのアラートのビューが表示されます。 アラート数をクリックすると、アラートの詳細に関するページに移動します。

## <a name="dependency-view"></a>[依存関係] ビュー
**[依存関係]** ビューは、リソースがどのように構成されているかを視覚化するのに役立ちます。 現在、[依存関係] ビューは Application Gateway、Virtual WAN、Load Balancer に対してサポートされるようになっています。 たとえば、Application Gateway の場合、[依存関係] ビューにアクセスするには、[メトリック] グリッド ビューで Application Gateway のリソース名をクリックします。 これは、Virtual WAN および Load Balancer にも適用されます。 

![[Application Gateway] ビュー](media/network-insights-overview/application-gateway.png)

Application Gateway の **[依存関係]** ビューは、フロントエンド IP がリスナー、ルール、バックエンド プールにどのように接続されているかを、簡略化されたビューで示します。 接続しているエッジは色分けされ、バックエンド プールの正常性に基づいて追加の詳細が表示されます。 このビューでは、Application Gateway メトリックと、VMSS と VM インスタンスなどのすべての関連するバックエンド プールのメトリックの詳しいビューも表示されます。

![[依存関係] ビュー](media/network-insights-overview/dependency-view.png)

依存関係グラフでは、構成設定に簡単に移動できます。 他の機能にアクセスするには、バックエンド プールを右クリックします。 たとえば、バックエンド プールが VM である場合、VM Insights と Network Watcher 接続のトラブルシューティングに直接アクセスして、接続の問題を特定できます。

![[依存関係] ビューのメニュー](media/network-insights-overview/dependency-view-menu.png)

[依存関係] ビューの検索バーとフィルター バーを使用すると、グラフ全体を簡単に検索することができます。 たとえば、次の例で *AppGWTestRule* を検索すると、グラフィック表示が絞り込まれて、*AppGWTestRule* に接続されているすべてのノードが表示されます。 

![検索例](media/network-insights-overview/search-example.png)

様々なフィルターを使用すると、特定のパスや状態に絞り込むのに役立ちます。 たとえば、 **[正常性状態]** ドロップダウンから *[異常]* だけを選択すると、状態が *[異常]* であるすべてのエッジを表示することができます。

**[Detailed Metric View]\(詳細メトリック ビュー\)** をクリックすると、アプリケーション ゲートウェイ、すべてのバックエンド プール リソース、フロントエンド IP に関する詳しいメトリックを含む事前構成されたブックを起動できます。 

## <a name="next-steps"></a>次のステップ 

- ネットワーク監視の詳細については、「[Azure Network Watcher とは](../../network-watcher/network-watcher-monitoring-overview.md)」をご覧ください。
