---
title: "Analytics - Azure Application Insights の強力な検索ツール | Microsoft Docs"
description: "Application Insights の強力な診断検索ツールである Analytics の概要。 "
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.assetid: 0a2f6011-5bcf-47b7-8450-40f284274b24
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: bwren
ms.translationtype: HT
ms.sourcegitcommit: c30998a77071242d985737e55a7dc2c0bf70b947
ms.openlocfilehash: a2dc351bd0346f5ca46f1eaafeff678c3339c8c9
ms.contentlocale: ja-jp
ms.lasthandoff: 08/02/2017

---
# <a name="analytics-in-application-insights"></a>Application Insights の Analytics
[Analytics](app-insights-analytics.md) は、[Application Insights](app-insights-overview.md) の強力な検索機能です。 ここでは、Log Analytics のクエリ言語について説明します。 

* **[紹介ビデオを見る](https://applicationanalytics-media.azureedge.net/home_page_video.mp4)**。
* **[シミュレーション データで Analytics を試す](https://analytics.applicationinsights.io/demo)** (ご使用のアプリからまだ Application Insights にデータが送信されていない場合)。
* **[SQL ユーザーのチート シート](https://aka.ms/sql-analytics)**では、最も一般的な言語の対応付けを確認できます。
* **[言語リファレンス](app-insights-analytics-reference.md)** Log Analytics クエリ言語の強力な機能すべての使用方法について説明します。


## <a name="queries-in-analytics"></a>Analytics でのクエリ
一般的なクエリでは、"*ソース*" テーブルの後に、一連の "*演算子*" を `|` で区切って記載します。 

例として、ハイデラバードの市民が何時に Web アプリを使用しているか調べてみましょう。 この調査の際に、HTTP 要求に対してどのような結果コードが返されるか確認しましょう。 

```AIQL
requests
| where timestamp > ago(30d)
| summarize ClientCount = dcount(client_IP) by bin(timestamp, 1h), resultCode
| extend LocalTime = timestamp - 4h
| order by LocalTime desc
| render barchart
```

異なるクライアント IP アドレスをカウントし、それらを過去 7 日間にわたり 1 日の時刻ごとにまとめます。 

> [!NOTE]
> 前の 24 時間以外の結果を得るには、クエリに 'timestamp' を明示的に含めるか、または時間範囲のドロップダウン メニューを使用します。
>

結果を棒グラフで表示し、異なる応答コードの結果を積算してましょう。

![棒グラフ、X 軸および Y 軸を選択してからセグメント化します](./media/app-insights-analytics/020.png)

ハイデラバードでは、アプリは昼食時と就寝時に最も使用されているようです (これら 500 のコードを調べる必要があります)。

次のような、強力な統計演算もあります。

![統計クエリの結果](./media/app-insights-analytics/025.png)

言語には、以下のような多くの魅力的な機能があります。


* [フィルター](https://docs.loganalytics.io/queryLanguage/query_language_whereoperator.html)。カスタム プロパティとメトリックを含む、すべてのフィールドで未加工のアプリ テレメトリをフィルター処理します。
* [結合](https://docs.loganalytics.io/queryLanguage/query_language_joinoperator.html)。複数のテーブルを結合します。要求をページ ビュー、依存関係の呼び出し、例外およびログ トレースに関連付けます。
* 強力な統計 [集計](https://docs.loganalytics.io/learn/tutorials/aggregations.html)。
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



## <a name="query-examples"></a>クエリのサンプル

これらのチュートリアルを試して、Analytics のパワーを実感してください。

 *  「[要求期間のスパイクとステップ ジャンプの自動診断](https://analytics.applicationinsights.io/demo#/discover/query/results/chart?title=Automatic%20diagnostics%20of%20sudden%20spikes%20or%20step%20jumps%20in%20requests%20duration&shared=true)」
 *  「[時系列分析でのパフォーマンス低下の分析](https://analytics.applicationinsights.io/demo#/discover/query/main?title=Analyzing%20performance%20degradations%20with%20time%20series%20analysis&shared=true)」
 *  「[Autocluster と diffpatterns によるアプリケーション エラーの分析](https://analytics.applicationinsights.io/demo#/discover/query/main?title=Analyzing%20application%20failures%20with%20autocluster%20and%20diffpatterns&shared=true)」
 *  「[時系列分析での高度な図形検出](https://analytics.applicationinsights.io/demo#/discover/query/main?title=Advanced%20shape%20detection%20with%20time%20series%20analysis&shared=true)」
 *  「[スライディング ウィンドウ操作を使用したアプリケーションの使用状況 (ローリング MAU/DAU など) の分析](https://analytics.applicationinsights.io/demo#/discover/query/main?title=Using%20sliding%20window%20calculations%20to%20analyze%20usage%20metrics:%20rolling%20MAU~2FDAU%20and%20cohorts&shared=true)」
 *  「[デバッグ ログの分析に基づくサービスの中断の検出](https://analytics.applicationinsights.io/demo#/discover/query/main?title=Detection%20of%20service%20disruptions%20based%20on%20regression%20analysis%20of%20trace%20logs&shared=true)」と、一致するブログ投稿 ([ここ](https://maximshklar.wordpress.com/2017/02/16/finding-trends-in-traces-with-smart-data-analytics))。
 *  「[単純なデバッグ ログを使用したアプリケーションのパフォーマンスのプロファイリング](https://analytics.applicationinsights.io/demo#/discover/query/main?title=Profiling%20applications'%20performance%20with%20simple%20debug%20logs&shared=true)」と、一致するブログ投稿 ([ここ](https://yossiattasblog.wordpress.com/2017/03/13/first-blog-post/))。
 *  「[単純なデバッグ ログを使用したコードのフロー内の各ステップの実行時間測定](https://analytics.applicationinsights.io/demo#/discover/query/main?title=Measuring%20the%20duration%20of%20each%20step%20in%20your%20code%20flow%20using%20simple%20debug%20logs&shared=true)」と、一致するブログ投稿 ([ここ](https://yossiattasblog.wordpress.com/2017/03/14/measuring-the-duration-of-each-step-in-your-code-flow-using-simple-debug-logs/))。
 *  「[単純なデバッグ ログを使用した同時実行の分析](https://analytics.applicationinsights.io/demo#/discover/query/results/chart?title=Analyzing%20concurrency%20with%20simple%20debug%20logs&shared=true)」と、一致するブログ投稿 ([ここ](https://yossiattasblog.wordpress.com/2017/03/23/analyzing-concurrency-using-simple-debug-logs/))。



## <a name="next-steps"></a>次のステップ
* まず、 [言語ツアー](app-insights-analytics-tour.md)を参照することをお勧めします。 
* [Analytics の使用](app-insights-analytics-using.md)の詳細。 
* [言語のリファレンス](app-insights-analytics-reference.md)。 

