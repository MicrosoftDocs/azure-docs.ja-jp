---
title: "Azure Application Insights とは何か  | Microsoft Docs"
description: "ライブ Web アプリケーションのアプリケーション パフォーマンス管理と使用状況を追跡します。  問題の検出、トリアージ、診断を行い、ユーザーがアプリをどのように使用しているかを理解します。"
services: application-insights
documentationcenter: 
author: alancameronwills
manager: carmonm
ms.assetid: 379721d1-0f82-445a-b416-45b94cb969ec
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: fd35f1774ffda3d3751a6fa4b6e17f2132274916
ms.openlocfilehash: 3b029b67e52b212d604e3d645fe7a451b5a60b3e
ms.lasthandoff: 03/16/2017


---
# <a name="what-is-application-insights"></a>Application Insights とは何か?
Application Insights は、複数のプラットフォームで使用できる Web 開発者向けの拡張可能なアプリケーション パフォーマンス管理 (APM) サービスです。 このサービスを使用して、実行中の Web アプリケーションを監視することができます。 パフォーマンスに異常があると、自動的に検出されます。 組み込まれている強力な分析ツールを使えば、問題を診断し、ユーザーがアプリを使用して実行している操作を把握できます。  Application Insights は、パフォーマンスやユーザビリティを継続的に向上させるうえで役立つように設計されています。 オンプレミスまたはクラウドでホストされている .NET、Node.js、J2EE などのさまざまなプラットフォーム上のアプリで機能します。 devOps プロセスと統合され、さまざまなツールへの接続ポイントを備えています。

![ユーザー アクティビティの統計をグラフ化したり、特定のイベントをドリルダウンしたりすることができます。](./media/app-insights-overview/00-sample.png)

[概要を説明するアニメーションを参照してください](https://www.youtube.com/watch?v=fX2NtGrh-Y0)。

## <a name="how-does-application-insights-work"></a>Application Insights のしくみ
小さなインストルメンテーション パッケージをアプリケーションにインストールし、Application Insights リソースを Microsoft Azure ポータルに設定します。 このインストルメンテーションがアプリを監視し、テレメトリ データをポータルに送信します  (アプリケーションの実行場所は問いません。Azure でホストされている必要はありません)。

Web サービス アプリケーションだけでなく、バックグラウンド コンポーネントや Web ページ内の JavaScript 自体もインストルメント化することができます。 

![アプリ内の Application Insights のインストルメンテーションが Application Insights のリソースにテレメトリを送信します。](./media/app-insights-overview/01-scheme.png)


さらに、パフォーマンス カウンター、Azure 診断、Docker のログなどのホスト環境からテレメトリをプルすることができます。 定期的に Web サービスに人工的な要求を送信する Web テストを設定することもできます。

すべてのテレメトリ ストリームは Azure Portal に統合され、このポータルで、生データに対して強力な分析および検索ツールを適用することができます。


### <a name="whats-the-overhead"></a>オーバーヘッドとは
アプリのパフォーマンスへの影響はごくわずかです。 追跡呼び出しはブロッキングを行わず、バッチ処理された後、別のスレッドで送信されます。

## <a name="what-does-application-insights-monitor"></a>Application Insights の監視対象

Application Insights は、開発チーム用のツールであり、アプリのパフォーマンスや使用状況を把握できるように支援します。 以下を監視します。

* **要求レート、応答時間、およびエラー率**: 最も人気のあるページがどの時間帯にどの場所のユーザーからアクセスされているかを調べます。 最もパフォーマンスの高いページを確認します。 要求が多いときに、応答時間と失敗率が高くなる場合は、おそらくリソースに問題があります。 
* **依存率、応答時間、およびエラー率**: 外部サービスによって応答が遅くなっているかどうかを調べます。
* **例外**: 集計された統計を分析します。または特定のインスタンスを選択し、スタック トレースと関連する要求を調べます。 サーバーとブラウザーの両方の例外が報告されます。
* **ページ ビューと読み込みのパフォーマンス**: ユーザーのブラウザーから報告されます。
* Web ページからの **AJAX 呼び出し**: レート、応答時間、およびエラー率。
* **ユーザー数とセッション数**。
* Windows または Linux サーバー コンピューターの CPU、メモリ、ネットワーク使用率などの**パフォーマンス カウンター**。 
* Docker または Azure の**ホスト診断**。 
* アプリの**診断トレース ログ**: これにより、トレース イベントを要求に関連付けることができます。
* 販売された品目や勝利したゲームなどのビジネス イベントを追跡するためにクライアントまたはサーバーのコード内に書き込んだ**カスタム イベントとメトリック**。

## <a name="where-do-i-see-my-telemetry"></a>テレメトリの確認場所

データを探索する方法は多数あります。 次の記事を参照してください。

|  |  |
| --- | --- |
| [**アプリケーション マップ**](app-insights-app-map.md)<br/>アプリのコンポーネントを、主要なメトリックとアラートと共に表示します。 |![アプリケーション マップ](./media/app-insights-overview/appmap-tn.png)  |
| [**インスタンスのデータの診断検索**](app-insights-diagnostic-search.md)<br/>要求、例外、依存関係の呼び出し、ログ トレースおよびページ ビューなどのイベントを検索およびフィルター処理します。  |![テレメトリの検索](./media/app-insights-overview/search-tn.png) |
| [**集計データのメトリックス エクスプ ローラー**](app-insights-metrics-explorer.md)<br/>要求、失敗、および例外の比率、応答時間、ページの読み込み時間などの集計データを調査、フィルター処理、およびセグメント分割します。 |![メトリック](./media/app-insights-overview/metrics-tn.png) |
| [**ダッシュボード**](app-insights-dashboards.md#dashboards)<br/>複数のリソースからのデータをマッシュアップし、他のユーザーと共有します。 複数コンポーネントのアプリケーションと、チーム ルームでの継続的な表示に最適です。 |![ダッシュボードのサンプル](./media/app-insights-overview/dashboard-tn.png) |
| [**ライブ メトリック ストリーム**](app-insights-live-stream.md)<br/>新しいビルドをデプロイする場合、このほぼリアルタイムのパフォーマンス インジケーターを監視し、すべてが期待どおりに動作することを確認します。 |![ライブ メトリックのサンプル](./media/app-insights-overview/live-metrics-tn.png) |
| [**分析**](app-insights-analytics.md)<br/>この強力なクエリ言語を使用して、アプリのパフォーマンスと使用状況に関する難しい質問に回答します。 |![分析のサンプル](./media/app-insights-overview/analytics-tn.png) |
| [**自動および手動のアラート**](app-insights-alerts.md)<br/>アプリのテレメトリの通常パターンに対して自動アラートを適応し、通常とは異なるパターンがある場合にアラートをトリガーします。 カスタムまたは標準のメトリックスの特定レベルでアラートを設定することもできます。 |![アラートのサンプル](./media/app-insights-overview/alerts-tn.png) |
| [**Visual Studio**](app-insights-visual-studio.md)<br/>パフォーマンス データをコードで確認します。 スタック トレースからコードに移動します。|![Visual studio](./media/app-insights-overview/visual-studio-tn.png) |
| [**Power BI**](app-insights-export-power-bi.md)<br/>使用状況のメトリックをその他のビジネス インテリジェンスと統合します。| ![Power BI](./media/app-insights-overview/power-bi.png)|
| [**REST API**](https://dev.applicationinsights.io/)<br/>メトリックと生データのクエリを実行するコードを記述します。| ![REST API](./media/app-insights-overview/rest-tn.png) |
| [**連続エクスポート**](app-insights-export-telemetry.md)<br/>生データが届いたらすぐにストレージに一括エクスポートします。 |![エクスポート](./media/app-insights-overview/export-tn.png) |

## <a name="how-do-i-use-application-insights"></a>Application Insights の使用方法

### <a name="monitor"></a>監視
Application Insights を Web アプリにインストールし、[可用性 Web テスト](app-insights-monitor-web-app-availability.md)を設定します。さらに、次のことを行います。

* チーム ルーム用の[ダッシュボード](app-insights-dashboards.md)を設定し、負荷、応答性のほか、依存関係、ページの読み込み、および AJAX 呼び出しのパフォーマンスを監視します。
* 最も時間がかかり、最も失敗した要求を検出します。
* 低下しているものについてすぐにわかるように、新しいリリースをデプロイしたときに [Live Stream](app-insights-live-stream.md) を監視します。

### <a name="detect-diagnose"></a>検出、診断
アラートを受け取ったときや問題を検出したときに、次のことを行います。

* 影響を受けるユーザーの数を評価します。
* 例外、依存関係呼び出し、トレースと失敗を相互に関連付けます。
* スタック ダンプとトレース ログを調べます。

### <a name="build-measure-learn"></a>ビルド、評価、学習
デプロイした各新機能の有効性を評価します。

* 顧客が新しい UX やビジネス機能をどのように使用しているかを評価することを計画します。
* カスタム テレメトリをコードに書き込みます。
* テレメトリによる確かな証拠に基づいて次の開発サイクルを決定します。

## <a name="get-started"></a>作業開始
Application Insights は、Microsoft Azure でホストされる多数のサービスの 1 つであり、テレメトリは分析と表示のために Microsoft Azure に送信されます。 このため、他の操作を行う前に、 [Microsoft Azure](http://azure.com)のサブスクリプションが必要になります。 サインアップは無料で、Application Insights の基本的な[価格プラン](https://azure.microsoft.com/pricing/details/application-insights/)を選択できます。大幅に増えた使用量に対応するようアプリケーションを拡大しない限り、料金は発生しません。 組織に既にサブスクリプションがある場合は、それにユーザーの Microsoft アカウントを追加することができます。

作業の開始方法はいくつかあります。 目的に適した方法から始めてください。 後で、他のものを追加することもできます。

* **実行時: サーバー上の Web アプリをインストルメント化します。** コードを更新しなくて済みます。 サーバーへの管理者アクセスが必要になります。
  * [**オンプレミスまたは VM 上の IIS**](app-insights-monitor-performance-live-website-now.md)
  * [**Azure Web アプリまたは VM**](app-insights-monitor-performance-live-website-now.md)
  * [**J2EE**](app-insights-java-live.md)
* **開発時: コードに Application Insights を追加します。** カスタム テレメトリを記述したり、バックエンド アプリおよびデスクトップ アプリをインストルメント化したりすることができます。
  * [Visual Studio](app-insights-asp-net.md) 2013 Update 2 以降。
  * [Eclipse](app-insights-java-eclipse.md) または[その他のツール](app-insights-java-get-started.md)の Java
  * [Node.JS](app-insights-nodejs.md)
  * [その他のプラットフォーム](app-insights-platforms.md)
* ページ ビュー、AJAX、その他のクライアント側テレメトリ用に **[Web ページをインストルメント化](app-insights-javascript.md)**します。
* **[可用性テスト](app-insights-monitor-web-app-availability.md)** - Microsoft のサーバーから定期的に Web サイトに ping を実行します。


## <a name="next-steps"></a>次のステップ
実行時に開始する:

* [IIS サーバー](app-insights-monitor-performance-live-website-now.md)
* [J2EE サーバー](app-insights-java-live.md)

開発時に開始する:

* [ASP.NET](app-insights-asp-net.md)
* [Java](app-insights-java-get-started.md)
* [Node.JS](app-insights-nodejs.md)

## <a name="support-and-feedback"></a>サポートとフィードバック
* 質問および問題点:
  * [Troubleshooting][qna]
  * [MSDN フォーラム](https://social.msdn.microsoft.com/Forums/vstudio/home?forum=ApplicationInsights)
  * [StackOverflow](http://stackoverflow.com/questions/tagged/ms-application-insights)
  * [開発者のサポートを得る](app-insights-get-dev-support.md)
* ご提案:
  * [UserVoice](https://visualstudio.uservoice.com/forums/357324)
* ブログ:
  * [Application Insights のブログ](https://azure.microsoft.com/blog/tag/application-insights)

## <a name="videos"></a>ビデオ

[![Animated introduction](./media/app-insights-overview/video-front-1.png)](https://www.youtube.com/watch?v=fX2NtGrh-Y0)

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player] 

<!--Link references-->

[android]: https://github.com/Microsoft/ApplicationInsights-Android
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[desktop]: app-insights-windows-desktop.md
[detect]: app-insights-detect-triage-diagnose.md
[greenbrown]: app-insights-asp-net.md
[ios]: https://github.com/Microsoft/ApplicationInsights-iOS
[java]: app-insights-java-get-started.md
[knowUsers]: app-insights-overview-usage.md
[platforms]: app-insights-platforms.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md

