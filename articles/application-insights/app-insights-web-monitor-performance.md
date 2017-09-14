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
ms.date: 11/25/2015
ms.author: bwren
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 69ead621c179bf49f17ed3274be4b625fc587556
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

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

## <a name="find-and-fix-performance-bottlenecks-with-an-interactive-performance-investigation"></a>インタラクティブなパフォーマンス調査によりパフォーマンスのボトルネックを探して修正する

新しい Application Insights のインタラクティブなパフォーマンス調査機能を使用して、Web アプリの全体的なパフォーマンスを低下させている領域を特定できます。 速度を低下させている具体的なページを簡単に特定し、[プロファイル ツール](app-insights-profiler.md)を使用してそれらのページ間に相関関係があるかどうかを確認できます。

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




