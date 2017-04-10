---
title: "Analytics - Azure Application Insights の強力な検索ツール | Microsoft Docs"
description: "Application Insights の強力な診断検索ツールである Analytics の概要。 "
services: application-insights
documentationcenter: 
author: alancameronwills
manager: carmonm
ms.assetid: 0a2f6011-5bcf-47b7-8450-40f284274b24
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: fd35f1774ffda3d3751a6fa4b6e17f2132274916
ms.openlocfilehash: 4fbd80f7e9775fe3c12a54302be4a162d2102e2f
ms.lasthandoff: 03/16/2017


---
# <a name="analytics-in-application-insights"></a>Application Insights の Analytics
[Analytics](app-insights-analytics.md) は、[Application Insights](app-insights-overview.md) の強力な検索機能です。 ここでは、Analytics のクエリ言語について説明します。 

* **[紹介ビデオを見る](https://applicationanalytics-media.azureedge.net/home_page_video.mp4)**。
* **[シミュレーション データで Analytics を試す](https://analytics.applicationinsights.io/demo)** (ご使用のアプリからまだ Application Insights にデータが送信されていない場合)。
* **[SQL ユーザーのチート シート](https://aka.ms/sql-analytics)**では、最も一般的な言語の対応付けを確認できます。
* **[言語リファレンス](app-insights-analytics-reference.md)** Analytics クエリ言語の強力な機能すべての使用方法について説明します。

## <a name="queries-in-analytics"></a>Analytics でのクエリ
一般的なクエリでは、"*ソース*" テーブルの後に、一連の "*演算子*" を `|` で区切って記載します。 

例として、ハイデラバードの市民が何時に Web アプリを使用しているか調べてみましょう。 この調査の際に、HTTP 要求に対してどのような結果コードが返されるか確認しましょう。 

```AIQL

    requests      // Table of events that log HTTP requests.
    | where timestamp > ago(7d) and client_City == "Hyderabad"
    | summarize clients = dcount(client_IP) 
      by tod_UTC=bin(timestamp % 1d, 1h), resultCode
    | extend local_hour = (tod_UTC + 5h + 30min) % 24h + datetime("2001-01-01") 
```

異なるクライアント IP アドレスをカウントし、それらを過去 7 日間にわたり 1 日の時刻ごとにまとめます。 

結果を棒グラフで表示し、異なる応答コードの結果を積算してましょう。

![棒グラフ、X 軸および Y 軸を選択してからセグメント化します](./media/app-insights-analytics/020.png)

ハイデラバードでは、アプリは昼食時と就寝時に最も使用されているようです (これら 500 のコードを調べる必要があります)。

次のような、強力な統計演算もあります。

![](./media/app-insights-analytics/025.png)

言語には、以下のような多くの魅力的な機能があります。

* [フィルター](app-insights-analytics-reference.md#where-operator)。カスタム プロパティとメトリックを含む、すべてのフィールドで未加工のアプリ テレメトリをフィルター処理します。
* [結合](app-insights-analytics-reference.md#join-operator)。複数のテーブルを結合します。要求をページ ビュー、依存関係の呼び出し、例外およびログ トレースに関連付けます。
* 強力な統計 [集計](app-insights-analytics-reference.md#aggregations)。
* SQL と同じように強力ですが、複雑なクエリがより容易になります。つまり、ステートメントを入れ子にするのではなく、基本演算間でデータをパイプします。
* 迅速かつ強力な視覚化。
* [グラフを Azure ダッシュボードにピン留め](app-insights-analytics-using.md#pin-to-dashboard)。
* [クエリを Power BI にエクスポート](app-insights-analytics-using.md#export-to-power-bi)。
* Powershell などからのプログラムによるクエリの実行に使用できる [REST API](https://dev.applicationinsights.io/) があります。


## <a name="connect-to-your-application-insights-data"></a>Application Insights のデータに接続する
以下のように、Application Insights でアプリの [概要ブレード](app-insights-dashboards.md) から Analytics を開きます。 

![portal.azure.com で Application Insights リソースを開き、[Analytics] をクリックします。](./media/app-insights-analytics/001.png)


## <a name="video"></a>ビデオ

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/123/player] 

[!INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]


## <a name="next-steps"></a>次のステップ
* まず、 [言語ツアー](app-insights-analytics-tour.md)を参照することをお勧めします。



