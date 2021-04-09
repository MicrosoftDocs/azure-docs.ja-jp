---
title: Azure Application Insights とは何か | Microsoft Docs
description: ライブ Web アプリケーションのアプリケーション パフォーマンス管理と使用状況を追跡します。  問題の検出、トリアージ、診断を行い、ユーザーがアプリをどのように使用しているかを理解します。
ms.topic: overview
ms.date: 06/03/2019
ms.custom: mvc
ms.openlocfilehash: ac6b6f2b47df0bce9ae164c83df54adfdf00b184
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100575709"
---
# <a name="what-is-application-insights"></a>Application Insights とは何か?
Application Insights は [Azure Monitor](../overview.md) の機能であり、開発者や DevOps プロフェッショナル向けの拡張可能なアプリケーション パフォーマンス管理 (APM) サービスです。 このサービスを使用して、実行中のアプリケーションを監視することができます。 パフォーマンスの異常を自動的に検出し、組み込まれている強力な分析ツールを使用して、問題を診断し、ユーザーがアプリを使用して実行している操作を把握できます。  Application Insights は、パフォーマンスやユーザビリティを継続的に向上させるうえで役立つように設計されています。 オンプレミス、ハイブリッド、または任意のパブリック クラウドでホストされている .NET、Node.js、Java、Python などのさまざまなプラットフォーム上のアプリで機能します。 DevOps プロセスと統合され、さまざまなツールへの接続ポイントを備えています。 Visual Studio App Center と統合することで、モバイル アプリからテレメトリを監視および分析できます。

## <a name="how-does-application-insights-work"></a>Application Insights のしくみ
アプリケーションに小規模なインストルメンテーション パッケージ (SDK) をインストールするか、[サポートされている](./platforms.md)場合は Application Insights エージェントを使用して Application Insights を有効にします。 インストルメンテーションによってアプリが監視され、インストルメンテーション キーと呼ばれる一意の GUID を使用して、テレメトリ データが Azure Application Insights リソースに転送されます。

Web サービス アプリケーションだけでなく、バックグラウンド コンポーネントや Web ページ内の JavaScript 自体もインストルメント化することができます。 アプリケーションおよびそのコンポーネントの実行場所は問いません。Azure でホストされている必要はありません。

![アプリ内の Application Insights のインストルメンテーションが Application Insights のリソースにテレメトリを送信します。](./media/app-insights-overview/diagram.png)

さらに、パフォーマンス カウンター、Azure Diagnostics、Docker のログなどのホスト環境からテレメトリをプルすることができます。 定期的に Web サービスに人工的な要求を送信する Web テストを設定することもできます。

これらのテレメトリ ストリームはすべて Azure Monitor に統合されています。 Azure portal では、強力な分析ツールと検索ツールを生データに適用できます。

### <a name="whats-the-overhead"></a>オーバーヘッドとは
アプリのパフォーマンスへの影響はわずかです。 追跡呼び出しはブロッキングを行わず、バッチ処理された後、別のスレッドで送信されます。

## <a name="what-does-application-insights-monitor"></a>Application Insights の監視対象

Application Insights は、開発チーム用のツールであり、アプリのパフォーマンスや使用状況を把握できるように支援します。 以下を監視します。

* **要求レート、応答時間、およびエラー率**: 最も人気のあるページがどの時間帯にどの場所のユーザーからアクセスされているかを調べます。 最もパフォーマンスの高いページを確認します。 要求が多いときに、応答時間と失敗率が高くなる場合は、おそらくリソースに問題があります。 
* **依存率、応答時間、およびエラー率**: 外部サービスによって応答が遅くなっているかどうかを調べます。
* **例外**: 集計された統計を分析します。または特定のインスタンスを選択し、スタック トレースと関連する要求を調べます。 サーバーとブラウザーの両方の例外が報告されます。
* **ページ ビューと読み込みのパフォーマンス**: ユーザーのブラウザーから報告されます。
* Web ページからの **AJAX 呼び出し**: レート、応答時間、およびエラー率。
* **ユーザー数とセッション数**。
* Windows または Linux サーバー コンピューターの CPU、メモリ、ネットワーク使用率などの **パフォーマンス カウンター**。 
* Docker または Azure の **ホスト診断**。 
* アプリの **診断トレース ログ**: これにより、トレース イベントを要求に関連付けることができます。
* 販売された品目や勝利したゲームなどのビジネス イベントを追跡するためにクライアントまたはサーバーのコード内に書き込んだ **カスタム イベントとメトリック**。

## <a name="where-do-i-see-my-telemetry"></a>テレメトリの確認場所

データを探索する方法は多数あります。 次の記事を参照してください。

|  |  |
| --- | --- |
| [**スマート検出と手動のアラート**](./proactive-diagnostics.md)<br/>アプリのテレメトリの通常パターンに合わせて自動アラートを設定し、通常とは異なるパターンがある場合にアラートをトリガーします。 カスタムまたは標準のメトリックスの特定レベルで[アラートを設定](../alerts/alerts-log.md)することもできます。 |![アラートのサンプル](./media/app-insights-overview/alerts-tn.png) |
| [**アプリケーション マップ**](./app-map.md)<br/>アプリのコンポーネントを、主要なメトリックとアラートと共に表示します。 |![アプリケーション マップ](./media/app-insights-overview/appmap-tn.png)  |
| [**プロファイラー**](./profiler.md)<br/>サンプリングされた要求の実行プロファイルを検査します。 |![サンプリングされた要求の実行プロファイルを示すキャプチャ画面。](./media/app-insights-overview/profiler.png) |
| [**利用状況分析**](./usage-overview.md)<br/>ユーザーのセグメント化とリテンションを分析します。|![リテンション期間ツール](./media/app-insights-overview/retention.png) |
| [**インスタンスのデータの診断検索**](./diagnostic-search.md)<br/>要求、例外、依存関係の呼び出し、ログ トレースおよびページ ビューなどのイベントを検索およびフィルター処理します。  |![テレメトリの検索](./media/app-insights-overview/search-tn.png) |
| [**集計データのメトリックス エクスプ ローラー**](../essentials/metrics-charts.md)<br/>要求、失敗、および例外の比率、応答時間、ページの読み込み時間などの集計データを調査、フィルター処理、およびセグメント分割します。 |![メトリック](./media/app-insights-overview/metrics-tn.png) |
| [**ダッシュボード**](./overview-dashboard.md)<br/>複数のリソースからのデータをマッシュアップし、他のユーザーと共有します。 複数コンポーネントのアプリケーションと、チーム ルームでの継続的な表示に最適です。 |![ダッシュボードのサンプル](./media/app-insights-overview/dashboard-tn.png) |
| [**ライブ メトリック ストリーム**](./live-stream.md)<br/>新しいビルドをデプロイする場合、このほぼリアルタイムのパフォーマンス インジケーターを監視し、すべてが期待どおりに動作することを確認します。 |![ライブ メトリックのサンプル](./media/app-insights-overview/live-metrics-tn.png) |
| [**分析**](../logs/log-query-overview.md)<br/>この強力なクエリ言語を使用して、アプリのパフォーマンスと使用状況に関する難しい質問に回答します。 |![分析のサンプル](./media/app-insights-overview/analytics-tn.png) |
| [**Visual Studio**](./visual-studio.md)<br/>パフォーマンス データをコードで確認します。 スタック トレースからコードに移動します。|![Visual Studio の [例外の詳細] とスタック トレースからコードに移動する例のスクリーンショット。](./media/app-insights-overview/visual-studio-tn.png) |
| [**スナップショット デバッガー**](./snapshot-debugger.md)<br/>ライブの操作からサンプリングされたスナップショットをパラメーター値でデバッグします。|![Visual studio](./media/app-insights-overview/snapshot.png) |
| [**Power BI**](./export-power-bi.md)<br/>使用状況のメトリックをその他のビジネス インテリジェンスと統合します。| ![Power BI](./media/app-insights-overview/power-bi.png)|
| [**REST API**](https://dev.applicationinsights.io/)<br/>メトリックと生データのクエリを実行するコードを記述します。| ![REST API](./media/app-insights-overview/rest-tn.png) |
| [**連続エクスポート**](./export-telemetry.md)<br/>生データが届いたらすぐにストレージに一括エクスポートします。 |![エクスポート](./media/app-insights-overview/export-tn.png) |

## <a name="how-do-i-use-application-insights"></a>Application Insights の使用方法

### <a name="monitor"></a>モニター
Application Insights を Web アプリにインストールし、[可用性 Web テスト](./monitor-web-app-availability.md)を設定します。さらに、次のことを行います。

* チーム ルーム用の既定の[アプリケーション ダッシュボード](./overview-dashboard.md)をチェックし、負荷、応答性のほか、依存関係、ページの読み込み、および AJAX 呼び出しのパフォーマンスを監視します。
* 最も時間がかかり、最も失敗した要求を検出します。
* 低下しているものについてすぐにわかるように、新しいリリースをデプロイしたときに [Live Stream](./live-stream.md) を監視します。

### <a name="detect-diagnose"></a>検出、診断
アラートを受け取ったときや問題を検出したときに、次のことを行います。

* 影響を受けるユーザーの数を評価します。
* 失敗を、例外、依存関係呼び出し、トレースと相互に関連付けます。
* プロファイラー、スナップショット、スタック ダンプ、およびトレース ログを調べます。

### <a name="build-measure-learn"></a>ビルド、評価、学習
デプロイした各新機能の[有効性を評価](./usage-overview.md)します。

* 顧客が新しい UX やビジネス機能をどのように使用しているかを評価することを計画します。
* カスタム テレメトリをコードに書き込みます。
* テレメトリによる確かな証拠に基づいて次の開発サイクルを決定します。

## <a name="get-started"></a>はじめに
Application Insights は、Microsoft Azure でホストされる多数のサービスの 1 つであり、テレメトリは分析と表示のために Microsoft Azure に送信されます。 このため、他の操作を行う前に、 [Microsoft Azure](https://azure.com)のサブスクリプションが必要になります。 サインアップは無料で、Application Insights の基本的な[価格プラン](https://azure.microsoft.com/pricing/details/application-insights/)を選択できます。大幅に増えた使用量に対応するようアプリケーションを拡大しない限り、料金は発生しません。 組織に既にサブスクリプションがある場合は、それにユーザーの Microsoft アカウントを追加することができます。

作業の開始方法はいくつかあります。 目的に適した方法から始めてください。 後で、他のものを追加することもできます。

* **実行時: サーバー上の Web アプリをインストルメント化します。** 既にデプロイ済みのアプリケーションに最適です。 コードを更新しなくて済みます。
  * [**Azure Web Apps でホストされる ASP.NET または ASP.NET Core アプリケーション**](./azure-web-apps.md)
  * [**Azure VM または Azure 仮想マシン スケール セット上の IIS でホストされる ASP.NET アプリケーション**](./azure-vm-vmss-apps.md)
  * [**IIS のオンプレミス VM でホストされる ASP.NET アプリケーション**](./monitor-performance-live-website-now.md)
* **開発時: コードに Application Insights を追加します。** テレメトリの収集をカスタマイズし、追加のテレメトリを送信できます。
  * [ASP.NET アプリケーション](./asp-net.md)
  * [ASP.NET Core アプリケーション](./asp-net-core.md)
  * [.NET コンソール アプリケーション](./console.md)
  * [Java](./java-get-started.md)
  * [Node.js](./nodejs.md)
  * [Python](./opencensus-python.md)
  * [その他のプラットフォーム](./platforms.md)
* ページ ビュー、AJAX、その他のクライアント側テレメトリ用に **[Web ページをインストルメント化](./javascript.md)** します。
* Visual Studio App Center と統合することで、 **[モバイル アプリの使用状況を分析](../app/mobile-center-quickstart.md)** します。
* **[可用性テスト](./monitor-web-app-availability.md)** - Microsoft のサーバーから定期的に Web サイトに ping を実行します。

## <a name="next-steps"></a>次のステップ
実行時に開始する:

* [Azure VM と Azure 仮想マシン スケール セットの IIS でホストされたアプリ](./azure-vm-vmss-apps.md)
* [IIS サーバー](./monitor-performance-live-website-now.md)
* [Azure Web Apps](./azure-web-apps.md)

開発時に開始する:

* [ASP.NET](./asp-net.md)
* [ASP.NET Core](./asp-net-core.md)
* [Java](./java-get-started.md)
* [Node.js](./nodejs.md)
* [Python](./opencensus-python.md)
* [JavaScript](./javascript.md)


## <a name="support-and-feedback"></a>サポートとフィードバック
* 質問および問題点:
  * [トラブルシューティング][qna]
  * [Microsoft Q&A 質問ページ](/answers/topics/azure-monitor.html)
  * [StackOverflow](https://stackoverflow.com/questions/tagged/ms-application-insights)
* ご提案:
  * [UserVoice](https://feedback.azure.com/forums/357324-application-insights/filters/top)
* ブログ:
  * [Application Insights のブログ](https://azure.microsoft.com/blog/tag/application-insights)

<!--Link references-->

[android]: ../app/mobile-center-quickstart.md
[azure]: ../../insights-perf-analytics.md
[client]: ./javascript.md
[desktop]: ./windows-desktop.md
[greenbrown]: ./asp-net.md
[ios]: ../app/mobile-center-quickstart.md
[java]: ./java-get-started.md
[knowUsers]: app-insights-web-track-usage.md
[platforms]: ./platforms.md
[portal]: https://portal.azure.com/
[qna]: ../faq.md
[redfield]: ./monitor-performance-live-website-now.md

