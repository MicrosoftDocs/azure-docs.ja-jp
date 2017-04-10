---
title: "Azure Application Insights における Live Metrics Stream | Microsoft Docs"
description: "Web アプリのパフォーマンスをリアルタイムで監視し、リリースまたはその他の変更の影響を観察します。"
services: application-insights
documentationcenter: 
author: alancameronwills
manager: carmonm
ms.assetid: 1f471176-38f3-40b3-bc6d-3f47d0cbaaa2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: fc6acf7b24bb017e4a93df1c15ed493a4442d6b5
ms.openlocfilehash: 917261e32845b53f2f2f3d12f60ca5fa87d441c7
ms.lasthandoff: 02/27/2017


---

# <a name="live-metrics-stream-instant-metrics-for-close-monitoring"></a>ライブ メトリックス ストリーム: 詳細な監視のインスタント メトリック
Live Metrics Stream には、まさにこの瞬間の [Application Insights](app-insights-overview.md) のメトリックが、ほぼリアルタイム (1 秒の待機時間) で表示されます。 この即時監視は、新しいビルドをリリースしており、すべてが正常に動作していることを確認する必要があるとき、またはリアルタイムでインシデントを調査しているときに非常に便利です。

[メトリックス エクスプローラー](app-insights-metrics-explorer.md)とは異なり、Live Metrics Stream にはメトリックの固定セットが表示されます。 データは、グラフが表示されている場合にのみ保持され、その後破棄されます。

Live Metrics Stream のデータは無料です。請求には加算されません。

![Live Metrics Stream のビデオ](./media/app-insights-live-stream/youtube.png)[Live Metrics Stream のビデオ](https://www.youtube.com/watch?v=zqfHf1Oi5PY)

![概要ブレードで、[Live Stream (ライブ ストリーム)] をクリックします](./media/app-insights-live-stream/live-stream.png)



## <a name="live-failures"></a>ライブ エラー

エラーまたは例外が記録されると、ライブ ストリームでそれらのサンプルが収集されます。 **[一時停止]** をクリックして特定のサンプルを保持し、イベントを選択すると、その詳細が表示されます。

![サンプリングされたライブ エラー](./media/app-insights-live-stream/live-stream-failures.png)


ライブ メトリックス ストリームは、[Application Insights SDK for web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) の最新バージョンで使用できます。

## <a name="filter-by-server-instance"></a>サーバー インスタンスによるフィルター処理

特定のサーバー ロール インスタンスを監視する場合は、サーバーでフィルター処理できます。

![サンプリングされたライブ エラー](./media/app-insights-live-stream/live-stream-filter.png)


## <a name="troubleshooting"></a>トラブルシューティング

データが表示されない場合 Live Metrics Stream は、他の Application Insights テレメトリとは異なるポートを使います。 [これらのポート](app-insights-ip-addresses.md)がファイアウォールで開いていることを確認してください。



## <a name="next-steps"></a>次のステップ
* [Application Insights による使用状況の監視](app-insights-overview-usage.md)
* [診断検索の使用](app-insights-diagnostic-search.md)


