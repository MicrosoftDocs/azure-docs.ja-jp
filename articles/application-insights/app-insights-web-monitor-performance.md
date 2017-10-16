---
title: "Application Insights を使用したアプリケーションの状態と利用状況の監視"
description: "Application Insights の使用を開始します。 オンプレミスの、または Microsoft Azure アプリケーションの使用状況、可用性、パフォーマンスを分析します。"
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.assetid: 40650472-e860-4c1b-a589-9956245df307
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 09/20/2017
ms.author: bwren
ms.openlocfilehash: 9efe10fa35c6a7c84e0d448bbe53127d16d20870
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-performance-in-web-applications"></a>Web アプリケーションのパフォーマンスを監視する


アプリケーションが正常に実行されていること、および障害がすぐに検出されることを確認します。 [Application Insights][start] はパフォーマンスの問題や例外を通知し、問題の根本原因の検出と診断を支援します。

Application Insights は、Java と ASP.NET の Web アプリケーションとサービス、WCF サービスの両方を監視できます。 それらは、オンプレミスで、仮想マシン上で、または Microsoft Azure Websites としてホストできます。 

クライアント側で、Application Insights は、Web ページと、iOS、Android、Windows ストア アプリを含むさまざまなデバイスからテレメトリを取得できます。

>[!Note]
> Web アプリケーションでパフォーマンスが低下しているページを見つける新しいエクスペリエンスを作成しました。 アクセスできない場合は、[プレビュー ブレード](app-insights-previews.md)でプレビュー オプションを構成することで有効にしてください。 この新しいエクスペリエンスについて詳しくは、「[インタラクティブなパフォーマンス調査によりパフォーマンスのボトルネックを探して修正する](#Find-and-fix-performance-bottlenecks-with-an-interactive-Performance-investigation)」をご覧ください。

## <a name="setup"></a>パフォーマンス モニターの設定
プロジェクトに Application Insights を追加していない場合 (つまり、ApplicationInsights.config がない場合)、以下のいずれかの方法で開始します。

* [ASP.NET Web アプリ](app-insights-asp-net.md)
  * [例外の監視を追加する](app-insights-asp-net-exceptions.md)
  * [依存関係の監視を追加する](app-insights-monitor-performance-live-website-now.md)
* [J2EE Web アプリ](app-insights-java-get-started.md)
  * [依存関係の監視を追加する](app-insights-java-agent.md)

## <a name="view"></a>パフォーマンス メトリックの監視
[Azure ポータル](https://portal.azure.com)で、アプリケーション用に設定した Application Insights リソースを参照します。 概要ブレードに、基本的なパフォーマンス データが表示されます。

詳細を表示したり、より長い期間の結果を表示したりするには、グラフをクリックします。 たとえば、[要求] タイルをクリックして、次の時間範囲を選択します。

![クリックしてより多くのデータを表示し、時間範囲を選択する](./media/app-insights-web-monitor-performance/appinsights-48metrics.png)

グラフをクリックして表示するメトリックを選択するか、新しいグラフを追加してそのメトリックを選択します。

![グラフをクリックし、メトリックを選択する](./media/app-insights-web-monitor-performance/appinsights-61perfchoices.png)

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

## <a name="diagnosis"></a>問題の診断
パフォーマンス上の問題を検出して診断するためのいくつかのヒントを以下に記します。

* [Web テスト][availability]を設定して、Web サイトが停止した場合や、間違って応答したり、応答速度が低下したりした場合にアラートを送信するようにします。 
* 要求の数を他のメトリックと比較し、障害や応答速度の低下が負荷と関連しているかどうかを確認します。
* コードに[トレース ステートメント][diagnostic]を挿入し、そのステートメントを検索することにより、問題を特定しやすくします。
* 稼働中の Web アプリを [Live Metrics Stream][livestream] で監視します。
* .Net アプリケーションの状態を[スナップショット デバッガー][snapshot]でキャプチャします。

>[!Note]
> 現在、Application Insights のパフォーマンス調査をインタラクティブな全画面エクスペリエンスに移行するための作業が行われています。 以降のドキュメントでは、最初に新しいエクスペリエンスについて説明します。さらに、従来のエクスペリエンスについてもアクセスが必要になる場合に備えて説明します。従来のエクスペリエンスは、移行中のどの段階でも使用できます。

## <a name="find-and-fix-performance-bottlenecks-with-an-interactive-full-screen-performance-investigation"></a>インタラクティブな全画面のパフォーマンス調査によりパフォーマンスのボトルネックを探して修正する

Application Insights の新しいインタラクティブなパフォーマンス調査機能を使用して、Web アプリの操作のパフォーマンス低下を調べることができます。 特定の低速な操作をすばやく選択し、[Profiler](app-insights-profiler.md) を使用して、低速な操作の根本原因となっているコードを特定することができます。 選択した操作に対して表示される新しい期間分布を使用すると、そのエクスペリエンスが顧客にとってどれだけ好ましくないかがひとめでわかります。 実際、それぞれの低速な操作について、影響を受けたユーザーの操作の数を確認できます。 次の例では、[GET Customers/Details]\(顧客/詳細の取得\) 操作のエクスペリエンスを詳しく見ています。 期間分布を見ると、3 つのスパイクがあることがわかります。 最も左のスパイクは 400 ミリ秒付近にあり、非常に反応の良いエクスペリエンスを表しています。 中央のスパイクは 1.2 秒付近にあり、平凡なエクスペリエンスを表しています。 最後に、3.6 秒の地点に 99 パーセンタイルのエクスペリエンスを表すもう 1 つの小さなスパイクがあります。これは、顧客が不満を招いて去る原因になっている可能性があります。 このエクスペリエンスは、同じ操作の高速なエクスペリエンスよりも 10 倍低速です。 

![[GET Customers/Details]\(顧客/詳細の取得\) の 3 つの期間スパイク](./media/app-insights-web-monitor-performance/PerformanceTriageViewZoomedDistribution.png)

この操作のユーザー エクスペリエンスをより深く理解するために、より長い時間範囲を選択することができます。 また、操作が特に低速な特定の時間枠で時間を絞り込むこともできます。 次の例では、既定の 24 時間の時間範囲から 7 日間の時間範囲に切り替えた後、12 日 (火) から 13 日 (水) の 9:47 ～ 12:47 の時間枠にズームインしています。 右側の期間分布とサンプルおよびプロファイラー トレースの数が両方とも更新されていることに注意してください。

![7 日間の範囲と時間枠における [GET Customers/Details]\(顧客/詳細の取得\) の 3 つの期間スパイク](./media/app-insights-web-monitor-performance/PerformanceTriageView7DaysZoomedTrend.png)

次に、低速なエクスペリエンスを絞り込むために、95 番目から 99 番目のパーセンタイルまでの期間にズームインします。 これらは、ユーザーの操作のうち特に低速な 4% を表します。

![7 日間の範囲と時間枠における [GET Customers/Details]\(顧客/詳細の取得\) の 3 つの期間スパイク](./media/app-insights-web-monitor-performance/PerformanceTriageView7DaysZoomedTrendZoomed95th99th.png)

ここで、[サンプル] ボタンをクリックして代表的なサンプルを見たり、[Profiler traces]\(Profiler トレース\) ボタンをクリックして代表的なプロファイラー トレースを見たりできます。 この例には、対象の時間枠と範囲期間で、[GET Customers/Details]\(顧客/詳細の取得\) 操作について収集された 4 つのトレースがあります。

ときには、コードではなくコードで呼び出される依存関係に問題があることがあります。 このような低速の依存関係を調査するには、パフォーマンス トリアージ ビューの [依存関係] タブに切り替えます。 パフォーマンス ビューでは既定でトレンド平均が示されますが、ここで本当に見たいのは 95 パーセンタイル (または成熟したサービスを監視している場合は 99 パーセンタイル) であることに注意してください。 次の例では、PUT fabrikamaccount という名前の低速な Azure BLOB 依存関係に焦点を当てています。 良好なエクスペリエンスは 40 ミリ秒付近で発生しています。一方、それよりも 3 倍遅い同じ依存関係への低速な呼び出しが 120 ミリ秒付近で発生しています。 これらの呼び出しが重なって対応する操作が顕著に遅くなるまでに多くはかかりません。 [操作] タブで実行できるのと同様に、代表的なサンプルとプロファイラー トレースを掘り下げることができます。

![7 日間の範囲と時間枠における [GET Customers/Details]\(顧客/詳細の取得\) の 3 つの期間スパイク](./media/app-insights-web-monitor-performance/SlowDependencies95thTrend.png)

インタラクティブな全画面のパフォーマンス調査に新しく追加されたもう 1 つの非常に強力な機能は、洞察との統合です。 Application Insights は、洞察の応答性回帰を検出して明らかにすることができ、フォーカス対象のサンプル セット内の共通の特性を識別するのに役立ちます。 利用可能なすべての洞察を調べる最も良い方法は、30 日間の時間範囲に切り替えた後、[Overall]\(全体\) を選択して、過去 1 か月間のすべての操作についての分析を表示することです。

![7 日間の範囲と時間枠における [GET Customers/Details]\(顧客/詳細の取得\) の 3 つの期間スパイク](./media/app-insights-web-monitor-performance/Performance30DayOveralllnsights.png)

Web アプリ ユーザーに対する貧弱なエクスペリエンスを招く原因を見つけるのは、干し草の山から針を見つけ出すような困難な作業ですが、Application Insights の新しいパフォーマンス トリアージ ビューは誇張なしにその役に立ちます。

## <a name="deprecated-find-and-fix-performance-bottlenecks-with-a-narrow-bladed-legacy-performance-investigation"></a>使用中止: 細いブレードの従来のパフォーマンス調査機能でパフォーマンスのボトルネックを見つけて修正する

従来の Application Insights のブレード形式のパフォーマンス調査機能を使用して、Web アプリの全体的なパフォーマンスを低下させている領域を特定できます。 速度が遅い特定のページを見つけ、[Profiler](app-insights-profiler.md) を使用してその問題の根本原因をコードまで追跡できます。 

### <a name="create-a-list-of-slow-performing-pages"></a>パフォーマンスが低下しているページの一覧を作成する 

パフォーマンスの問題を見つけるための最初の手順は、応答が遅いページの一覧を取得することです。 以下のスクリーンショットは、[パフォーマンス] ブレードを使用して問題が発生している可能性があるページの一覧を取得し、さらに調査する方法を示します。 このページから、アプリの応答時間が午後 6 時頃に低下しており、午後 10 時頃に再度低下していることがすぐにわかります。 また、顧客/詳細の取得操作の一部で、応答時間の中央値が 507.05 ミリ秒の操作が長時間実行されています。 

![Application Insights のインタラクティブなパフォーマンス](./media/app-insights-web-monitor-performance/performance1.png)

### <a name="drill-down-on-specific-pages"></a>特定のページにドリル ダウンする

アプリのパフォーマンスについてスナップショットを作成したら、パフォーマンスが低下している具体的な操作に関する詳細を取得できます。 一覧の操作をクリックすると、以下に示すように詳細が表示されます。 グラフから、パフォーマンスが依存関係に基づいていたかどうかを確認できます。 また、応答時間の変動を経験したユーザーの数も確認できます。 

![Application Insights の [操作] ブレード](./media/app-insights-web-monitor-performance/performance5.png)

### <a name="drill-down-on-a-specific-time-period"></a>特定の期間にドリル ダウンする

調査する時点を特定したら、さらにドリル ダウンしてパフォーマンスの低下を招いた可能性がある具体的な操作を確認できます。 具体的な時点をクリックすると、以下に示すようにページの詳細を確認できます。 以下の例では、指定した期間における操作の一覧が、サーバー応答コードと操作時間とともに表示されます。 また、TFS 作業項目を開くための URL があり、必要に応じて開発チームにこの情報を送信できます。

![Application Insights のタイム スライス](./media/app-insights-web-monitor-performance/performance2.png)

### <a name="drill-down-on-a-specific-operation"></a>特定の操作にドリル ダウンする

調査する時点を特定したら、さらにドリル ダウンしてパフォーマンスの低下を招いた可能性がある具体的な操作を確認できます。 一覧から操作をクリックして、以下に示すように操作の詳細を確認します。 この例では、操作が失敗し、Application Insights にアプリケーションがスローした例外の詳細が表示されます。 ここでも、このブレードから TFS 作業項目を簡単に作成できます。

![Application Insights の [操作] ブレード](./media/app-insights-web-monitor-performance/performance3.png)


## <a name="next"></a>次のステップ
[Web テスト][availability] - 世界中から定期的に Web 要求をアプリケーションに送信します。

[診断トレースの収集と検索][diagnostic] - トレース呼び出しを挿入し、結果を詳しく調べて問題を特定します。

[使用状況の追跡][usage] - アプリケーションの使用状況を調べます。

[トラブルシューティング][qna] と Q & A



<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[greenbrown]: app-insights-asp-net.md
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md
[usage]: app-insights-web-track-usage.md
[livestream]: app-insights-live-stream.md
[snapshot]: app-insights-snapshot-debugger.md



