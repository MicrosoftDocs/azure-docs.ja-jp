---
title: "Application Insights を使用したアプリケーションの状態と利用状況の監視"
description: "Application Insights の使用を開始します。 オンプレミスの、または Microsoft Azure アプリケーションの使用状況、可用性、パフォーマンスを分析します。"
services: application-insights
documentationcenter: 
author: alancameronwills
manager: douge
ms.assetid: 40650472-e860-4c1b-a589-9956245df307
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/25/2015
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: d84ab993b1d9489ca9d2edaa1cb9672d9bced899
ms.openlocfilehash: 7dd1289453fe146b446322570fcb1c0df74b4db2


---
# <a name="monitor-performance-in-web-applications"></a>Web アプリケーションのパフォーマンスを監視する


アプリケーションが正常に実行されていること、および障害がすぐに検出されることを確認します。 [Application Insights][start] はパフォーマンスの問題や例外を通知し、問題の根本原因の検出と診断を支援します。

Application Insights は、Java と ASP.NET の Web アプリケーションとサービス、WCF サービスの両方を監視できます。 それらは、オンプレミスで、仮想マシン上で、または Microsoft Azure Web サイトとしてホストできます。 

クライアント側で、Application Insights は、Web ページと、iOS、Android、Windows ストア アプリを含むさまざまなデバイスからテレメトリを取得できます。

## <a name="a-namesetupaset-up-performance-monitoring"></a><a name="setup"></a>パフォーマンス モニターの設定
プロジェクトに Application Insights を追加していない場合 (つまり、ApplicationInsights.config がない場合)、以下のいずれかの方法で開始します。

* [ASP.NET Web アプリ](app-insights-asp-net.md)
  * [例外の監視を追加する](app-insights-asp-net-exceptions.md)
  * [依存関係の監視を追加する](app-insights-monitor-performance-live-website-now.md)
* [J2EE Web アプリ](app-insights-java-get-started.md)
  * [依存関係の監視を追加する](app-insights-java-agent.md)

## <a name="a-nameviewaexploring-performance-metrics"></a><a name="view"></a>パフォーマンス メトリックの監視
[Azure ポータル](https://portal.azure.com)で、アプリケーション用に設定した Application Insights リソースを参照します。 概要ブレードに、基本的なパフォーマンス データが表示されます。

詳細を表示したり、より長い期間の結果を表示したりするには、グラフをクリックします。 たとえば、[要求] タイルをクリックして、次の時間範囲を選択します。

![クリックしてより多くのデータを表示し、時間範囲を選択する](./media/app-insights-web-monitor-performance/appinsights-48metrics.png)

グラフをクリックして表示するメトリックを選択するか、新しいグラフを追加してそのメトリックを選択します。

![グラフをクリックし、メトリックを選択する](./media/app-insights-web-monitor-performance/appinsights-61perfchoices.png)

> [!NOTE]
> **すべてのメトリックのチェック ボックスをオフにしてください** 。 メトリックはグループに分けられ、グループ内のあるメンバーが選択されると、そのグループのメンバーのみが表示されます。
> 
> 

## <a name="a-namemetricsawhat-does-it-all-mean-performance-tiles-and-reports"></a><a name="metrics"></a>どのような意味がありますか? パフォーマンス タイルとレポート
多様なパフォーマンス メトリックを取得できます。 まず、アプリケーション ブレードに既定で表示されるメトリックから始めます。

### <a name="requests"></a>要求
指定の期間に受け取った HTTP 要求の数です。 これを他のレポートにおける結果と比較して、負荷が変化するとアプリケーションの動作がどのように変わるかを確認します。

HTTP 要求には、ページ、データ、画像に関するすべての GET 要求または POST 要求が含まれます。

タイルをクリックして、特定の URL のカウントを取得します。

### <a name="average-response-time"></a>平均応答時間
アプリケーションに入力した Web 要求と返された応答の間の時間を測定します。

各ポイントは移動平均を示しています。 多くの要求がある場合、グラフ内の明確なピークや下落以外に、平均から逸脱している要求が存在することがあります。

異常なピークを探します。 通常、要求の数が多くなれば応答時間も長くなることが想定されます。 上昇の形が不均衡な場合、アプリケーションがリソース制限 (CPU、および CPU が使用するサービス機能など) に達した可能性があります。

タイルをクリックして、特定の URL の時間を取得します。

![](./media/app-insights-web-monitor-performance/appinsights-42reqs.png)

### <a name="slowest-requests"></a>最も遅い要求
![](./media/app-insights-web-monitor-performance/appinsights-44slowest.png)

パフォーマンス チューニングが必要となる可能性がある要求を示します。

### <a name="failed-requests"></a>失敗した要求
![](./media/app-insights-web-monitor-performance/appinsights-46failed.png)

受信できない例外がスローされた要求の数。

タイルをクリックして、特定の障害の詳細を表示したり、個別の要求を選択してその詳細を表示したりします。 

障害の代表的な例だけが、それぞれの検査用に保持されます。

### <a name="other-metrics"></a>その他のメトリック
表示できる他のメトリックを確認するには、グラフをクリックし、すべてのメトリックを選択解除してすべてのメトリック セットを表示します。 (i) をクリックし、各メトリック定義を表示します。

![すべてのメトリックを選択解除し、全セットを表示する](./media/app-insights-web-monitor-performance/appinsights-62allchoices.png)

いずれかのメトリックを選択すると、同じグラフ上に表示できない他のメトリックは無効になります。

## <a name="set-alerts"></a>アラートの設定
すべてのメトリックの異常な値を電子メールで通知するには、アラートを追加します。 アカウント管理者または特定の電子メール アドレスのいずれかに電子メールを送信することを選択できます。

![](./media/app-insights-web-monitor-performance/appinsights-413setMetricAlert.png)

その他のプロパティの前に、リソースを設定します。 パフォーマンスまたは使用状況のメトリックにアラートを設定する場合、Web テスト リソースは選択しないでください。

しきい値を入力するように求められたら、単位に注意してください。

*[アラートの追加] ボタンが表示されません。* これは、読み取り専用のアクセス許可しかないグループ アカウントなのでしょうか? アカウント管理者にご確認ください。

## <a name="a-namediagnosisadiagnosing-issues"></a><a name="diagnosis"></a>問題の診断
パフォーマンス上の問題を検出して診断するためのいくつかのヒントを以下に記します。

* [Web テスト][可用性]を設定して、Web サイトが停止した場合や、間違って応答したり、応答速度が低下したりした場合にアラートを送信するようにします。 
* 要求の数を他のメトリックと比較し、障害や応答速度の低下が負荷と関連しているかどうかを確認します。
* コードに[トレース ステートメントを挿入し、そのステートメントを検索する][diagnostic]ことにより、問題を特定しやすくします。

## <a name="a-namenextanext-steps"></a><a name="next"></a>次のステップ
[Web テスト][可用性] - 世界中から定期的に Web 要求をアプリケーションに送信します。

[診断トレースの収集と検索][diagnostic] - トレース呼び出しを挿入し、結果を詳しく調べて問題を特定します。

[使用状況の追跡][usage] - アプリケーションの使用状況を調べます。

[トラブルシューティング][qna] と Q & A

## <a name="video"></a>ビデオ
> [!VIDEO https://channel9.msdn.com/Series/ConnectOn-Demand/222/player]


<!--Link references-->

[可用性]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[greenbrown]: app-insights-asp-net.md
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md
[usage]: app-insights-web-track-usage.md





<!--HONumber=Nov16_HO3-->


