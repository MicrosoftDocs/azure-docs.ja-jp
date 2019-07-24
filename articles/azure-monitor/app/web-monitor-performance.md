---
title: Application Insights を使用したアプリケーションの状態と利用状況の監視
description: Application Insights の使用を開始します。 オンプレミスの、または Microsoft Azure アプリケーションの使用状況、可用性、パフォーマンスを分析します。
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 40650472-e860-4c1b-a589-9956245df307
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/10/2018
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: d7b8037f50fc4877fe233925f3e922648169f73b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57901170"
---
# <a name="monitor-performance-in-web-applications"></a>Web アプリケーションのパフォーマンスを監視する


アプリケーションが正常に実行されていること、および障害がすぐに検出されることを確認します。 [Application Insights][start] はパフォーマンスの問題や例外を通知し、問題の根本原因の検出と診断を支援します。

Application Insights は、Java と ASP.NET の Web アプリケーションとサービス、WCF サービスの両方を監視できます。 それらは、オンプレミスで、仮想マシン上で、または Microsoft Azure Websites としてホストできます。 

クライアント側で、Application Insights は、Web ページと、iOS、Android、Windows ストア アプリを含むさまざまなデバイスからテレメトリを取得できます。

## <a name="setup"></a>パフォーマンス モニターの設定
プロジェクトに Application Insights を追加していない場合 (つまり、ApplicationInsights.config がない場合)、以下のいずれかの方法で開始します。

* [ASP.NET Web アプリ](../../azure-monitor/app/asp-net.md)
  * [例外の監視を追加する](../../azure-monitor/app/asp-net-exceptions.md)
  * [依存関係の監視を追加する](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Java EE Web アプリ](../../azure-monitor/app/java-get-started.md)
  * [依存関係の監視を追加する](../../azure-monitor/app/java-agent.md)

## <a name="view"></a>パフォーマンス メトリックの監視
[Azure ポータル](https://portal.azure.com)で、アプリケーション用に設定した Application Insights リソースを参照します。 概要ブレードに、基本的なパフォーマンス データが表示されます。

詳細を表示したり、より長い期間の結果を表示したりするには、グラフをクリックします。 たとえば、[要求] タイルをクリックして、次の時間範囲を選択します。

![クリックしてより多くのデータを表示し、時間範囲を選択する](./media/web-monitor-performance/appinsights-48metrics.png)

グラフをクリックして表示するメトリックを選択するか、新しいグラフを追加してそのメトリックを選択します。

![グラフをクリックし、メトリックを選択する](./media/web-monitor-performance/appinsights-61perfchoices.png)

> [!NOTE]
> **すべてのメトリックのチェック ボックスをオフにしてください** 。 メトリックはグループに分けられ、グループ内のあるメンバーが選択されると、そのグループのメンバーのみが表示されます。

## <a name="metrics"></a>どのような意味がありますか? パフォーマンス タイルとレポート
パフォーマンスに関する各種メトリックを取得できます。 まず、アプリケーション ブレードに既定で表示されるメトリックから始めます。

### <a name="requests"></a>要求
指定の期間に受け取った HTTP 要求の数です。 これを他のレポートにおける結果と比較して、負荷が変化するとアプリケーションの動作がどのように変わるかを確認します。

HTTP 要求には、ページ、データ、画像に関するすべての GET 要求または POST 要求が含まれます。

タイルをクリックして、特定の URL のカウントを取得します。

### <a name="average-response-time"></a>平均応答時間
アプリケーションに入力した Web 要求と返された応答の間の時間を測定します。

各ポイントは移動平均を示しています。 多くの要求がある場合、グラフ内の明確なピークや下落以外に、平均から逸脱している要求が存在することがあります。

異常なピークを探します。 通常、要求の数が多くなれば応答時間も長くなることが想定されます。 上昇の形が不均衡な場合、アプリケーションがリソース制限 (CPU、および CPU が使用するサービス機能など) に達した可能性があります。

タイルをクリックして、特定の URL の時間を取得します。

![](./media/web-monitor-performance/appinsights-42reqs.png)

### <a name="slowest-requests"></a>最も遅い要求
![](./media/web-monitor-performance/appinsights-44slowest.png)

パフォーマンス チューニングが必要となる可能性がある要求を示します。

### <a name="failed-requests"></a>失敗した要求
![](./media/web-monitor-performance/appinsights-46failed.png)

受信できない例外がスローされた要求の数。

タイルをクリックして、特定の障害の詳細を表示したり、個別の要求を選択してその詳細を表示したりします。 

障害の代表的な例だけが、それぞれの検査用に保持されます。

### <a name="other-metrics"></a>その他のメトリック
表示できる他のメトリックを確認するには、グラフをクリックし、すべてのメトリックを選択解除してすべてのメトリック セットを表示します。 (i) をクリックし、各メトリック定義を表示します。

![すべてのメトリックを選択解除し、全セットを表示する](./media/web-monitor-performance/appinsights-62allchoices.png)

いずれかのメトリックを選択すると、同じグラフ上に表示できない他のメトリックは無効になります。

## <a name="set-alerts"></a>アラートの設定
すべてのメトリックの異常な値を電子メールで通知するには、アラートを追加します。 アカウント管理者または特定の電子メール アドレスのいずれかに電子メールを送信することを選択できます。

![](./media/web-monitor-performance/appinsights-413setMetricAlert.png)

その他のプロパティの前に、リソースを設定します。 パフォーマンスまたは使用状況のメトリックにアラートを設定する場合、Web テスト リソースは選択しないでください。

しきい値を入力するように求められたら、単位に注意してください。

*[アラートの追加] ボタンが表示されません。* これは、読み取り専用のアクセス許可しかないグループ アカウントなのでしょうか? アカウント管理者にご確認ください。

## <a name="diagnosis"></a>問題の診断
パフォーマンス上の問題を検出して診断するためのいくつかのヒントを以下に記します。

* [Web テスト][availability]を設定して、Web サイトが停止した場合や、間違って応答したり、応答速度が低下したりした場合にアラートを送信するようにします。 
* 要求の数を他のメトリックと比較し、障害や応答速度の低下が負荷と関連しているかどうかを確認します。
* コードに[トレース ステートメント][diagnostic]を挿入し、そのステートメントを検索することにより、問題を特定しやすくします。
* 稼働中の Web アプリを [Live Metrics Stream][livestream] で監視します。
* ご自分の .NET アプリケーションの状態を[スナップショット デバッガー][snapshot]を使用してキャプチャします。

## <a name="find-and-fix-performance-bottlenecks-with-performance-investigation-experience"></a>過去のパフォーマンス調査を利用し、パフォーマンスのボトルネックを探して修正する

過去のパフォーマンス調査を利用し、Web アプリの操作のパフォーマンス低下を調べることができます。 特定の低速な操作をすばやく選択し、[Profiler](../../azure-monitor/app/profiler.md) を使用して、低速な操作の根本原因となっているコードを特定することができます。 選択した操作に対して表示される新しい期間分布を使用すると、そのエクスペリエンスが顧客にとってどれだけ好ましくないかがひとめでわかります。 低速な操作ごとに、その影響を受けたユーザーの操作の数を確認できます。 次の例では、[GET Customers/Details]\(顧客/詳細の取得\) 操作のエクスペリエンスを詳しく見ています。 期間分布を見ると、3 つのスパイクがあることがわかります。 最も左のスパイクは 400 ミリ秒付近にあり、非常に反応がよいことがわかります。 中央のスパイクは 1.2 秒付近にあり、中程度であることがわかります。 最後になりますが、3.6 秒の地点に 99 パーセンタイルを表す小さなスパイクがもう 1 つあります。これは、顧客が不満を感じて去ってしまう原因になっている可能性があります。 このエクスペリエンスは、同じ操作の高速なエクスペリエンスよりも 10 倍低速です。 

![[GET Customers/Details]\(顧客/詳細の取得\) の 3 つの期間スパイク](./media/web-monitor-performance/PerformanceTriageViewZoomedDistribution.png)

この操作のユーザー エクスペリエンスをより深く理解するために、より長い時間範囲を選択することができます。 また、操作が低速な特定の時間枠で時間を絞り込むこともできます。 次の例では、既定の 24 時間の時間範囲から 7 日間の時間範囲に切り替えた後、12 日 (火) から 13 日 (水) の 9:47 ～ 12:47 の時間枠にズームインしています。 右側では期間分布と、サンプルとプロファイラー トレースの数が両方とも更新されています。

![7 日間の範囲と時間枠における [GET Customers/Details]\(顧客/詳細の取得\) の 3 つの期間スパイク](./media/web-monitor-performance/PerformanceTriageView7DaysZoomedTrend.png)

次に、低速なエクスペリエンスを絞り込むために、95 番目から 99 番目のパーセンタイルまでの期間にズームインします。 これらは、ユーザーの操作のうち低速な 4% を表します。

![7 日間の範囲と時間枠における [GET Customers/Details]\(顧客/詳細の取得\) の 3 つの期間スパイク](./media/web-monitor-performance/PerformanceTriageView7DaysZoomedTrendZoomed95th99th.png)

ここで、[サンプル] ボタンをクリックして代表的なサンプルを見たり、[Profiler traces]\(Profiler トレース\) ボタンをクリックして代表的なプロファイラー トレースを見たりできます。 この例には、対象の時間枠と範囲期間で、[GET Customers/Details]\(顧客/詳細の取得\) 操作について収集された 4 つのトレースがあります。

ときには、コードではなくコードで呼び出される依存関係に問題があることがあります。 このような低速の依存関係を調査するには、パフォーマンス トリアージ ビューの [依存関係] タブに切り替えます。 パフォーマンス ビューでは既定でトレンド平均が示されますが、ここで本当に見たいのは 95 パーセンタイル (または成熟したサービスを監視している場合は 99 パーセンタイル) です。 次の例では、PUT fabrikamaccount という名前の低速な Azure BLOB 依存関係に焦点を当てています。 良好なエクスペリエンスは 40 ミリ秒付近で多く発生しています。一方、それよりも 3 倍遅い同じ依存関係への低速な呼び出しが 120 ミリ秒付近で多く発生しています。 これらの呼び出しが重なって対応する操作が顕著に遅くなるまでに多くはかかりません。 [操作] タブで実行できるのと同様に、代表的なサンプルとプロファイラー トレースを掘り下げることができます。

![7 日間の範囲と時間枠における [GET Customers/Details]\(顧客/詳細の取得\) の 3 つの期間スパイク](./media/web-monitor-performance/SlowDependencies95thTrend.png)

パフォーマンス調査によって、重点としたサンプル セットと共に関連情報が表示されます。 利用可能なすべての洞察を調べる最も良い方法は、30 日間の時間範囲に切り替えた後、[Overall]\(全体\) を選択して、過去 1 か月間のすべての操作についての分析を表示することです。

![7 日間の範囲と時間枠における [GET Customers/Details]\(顧客/詳細の取得\) の 3 つの期間スパイク](./media/web-monitor-performance/Performance30DayOveralllnsights.png)


## <a name="next"></a>次のステップ
[Web テスト][availability] - 世界中から定期的に Web 要求をアプリケーションに送信します。

[診断トレースの収集と検索][diagnostic] - トレース呼び出しを挿入し、結果を詳しく調べて問題を特定します。

[使用状況の追跡][usage] - アプリケーションの使用状況を調べます。

[トラブルシューティング][qna] と Q & A



<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[greenbrown]: ../../azure-monitor/app/asp-net.md
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[redfield]: ../../azure-monitor/app/monitor-performance-live-website-now.md
[start]: ../../azure-monitor/app/app-insights-overview.md
[usage]: usage-overview.md
[livestream]: ../../azure-monitor/app/live-stream.md
[snapshot]: ../../azure-monitor/app/snapshot-debugger.md



