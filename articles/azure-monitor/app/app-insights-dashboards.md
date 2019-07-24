---
title: Azure Application Insights のダッシュボードおよびナビゲーション | Microsoft Docs
description: 重要な APM グラフとクエリのビューを作成します。
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 39b0701b-2fec-4683-842a-8a19424f67bd
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/14/2017
ms.author: mbullwin
ms.openlocfilehash: afbf2bc32aa737eb5f6dde41035b206d6e260252
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2019
ms.locfileid: "55813766"
---
# <a name="navigation-and-dashboards-in-the-application-insights-portal"></a>Application Insights ポータルのナビゲーションおよびダッシュボード
[プロジェクトに Application Insights を設定](../../azure-monitor/app/app-insights-overview.md)すると、[Azure Portal](https://portal.azure.com) のプロジェクトの Application Insights リソースに、アプリのパフォーマンスと使用状況に関するテレメトリ データが表示されます。

## <a name="find-your-telemetry"></a>テレメトリの検索
[Azure Portal](https://portal.azure.com) にサインインし、アプリ用に作成した Application Insights リソースに移動します。

![[参照] をクリックし、[Application Insights]、アプリの順に選択します。](./media/app-insights-dashboards/00-start.png)

アプリの [概要] ブレード (ページ) には、アプリの主要な診断メトリックの概要が表示されます。また、[概要] ブレードはポータルの他の機能のゲートウェイでもあります。

![テレメトリを表示する主な方法](./media/app-insights-dashboards/010-oview.png)

グラフとグリッドをカスタマイズし、ダッシュボードにピン留めできます。 これにより、さまざまなアプリの重要なテレメトリをまとめて、中央のダッシュボードに表示することができます。

## <a name="dashboards"></a>ダッシュボード
[Microsoft Azure Portal](https://portal.azure.com) にサインインすると、まず、ダッシュボードが表示されます。 ここでは、 [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) のテレメトリなどを含む、すべての Azure リソースから最も重要なグラフをまとめて表示することができます。

![カスタマイズされたダッシュボード](./media/app-insights-dashboards/31.png)

1. Application Insights でご利用のアプリなどの**特定のリソースに移動**する: 左側のバーを使用します。
2. **現在のダッシュボードに戻る**か、または最近アクセスした他のビューに切り替えます。左上にあるドロップダウン メニューを使用します。
3. **ダッシュボードを切り替える**: ダッシュボードのタイトルにあるドロップダウン メニューを使用します。
4. **ダッシュボードを作成、編集、共有する**: ダッシュボード ツール バーを使用します。
5. **ダッシュ ボードを編集する**: タイルにマウス ポインターを合わせてから、上部のバーを使ってタイルを移動、カスタマイズ、削除します。

## <a name="add-to-a-dashboard"></a>ダッシュボードへの追加
特に重要なブレードやグラフのセットがあれば、そのコピーをダッシュボードにピン留めしておきましょう。 ダッシュボードを開いたときにすぐに見ることができます。

![グラフをピン留めするには、マウスでポイントし、ヘッダーの [...] をクリックします。](./media/app-insights-dashboards/33.png)

1. ダッシュボードにグラフをピン留めします。 グラフのコピーがダッシュボードに表示されます。
2. ブレード全体をダッシュボードにピン留めします。クリックして進むことのできるタイルとしてブレード全体がダッシュボードに表示されます。
3. 現在のダッシュボードに戻るには、左上隅をクリックします。 その後ドロップダウン メニューを使用して現在のビューに戻ることができます。

グラフはタイルにグループ化されます。1 つのタイルに複数のグラフを含めることができます。 タイル全体をダッシュボードにピン留めします。

グラフは、以下に示すグラフの時間範囲に応じた頻度で自動的に更新されます。

* 1 時間までの時間の範囲: 5 分ごとに更新
* 1 から 24 時間の時間の範囲: 15 分ごとに更新
* 24 時間を超える時間の範囲: (時間の範囲)/60。

### <a name="pin-any-query-in-analytics"></a>Analytics の任意のクエリをピン留めする
共有ダッシュボードに [Analytics のグラフをピン留め](../../azure-monitor/log-query/get-started-portal.md)することもできます。 これにより、任意のクエリのグラフを標準的なメトリックの横に追加できます。 

結果は 1 時間ごとに自動的に再計算されます。 すぐに再計算するには、グラフ上の更新アイコンをクリックします。 (ブラウザーを更新しても再計算は実行されません)。

## <a name="adjust-a-tile-on-the-dashboard"></a>ダッシュボード上のタイルの調整
タイルをダッシュボード上に追加すると、タイルを調整することができます。

![グラフを編集するには、そのグラフ上にポインターを置きます。](./media/app-insights-dashboards/36.png)

1. タイルにグラフを追加します。
2. メトリック、グループ化ディメンションおよびグラフのスタイル (テーブル、グラフ) を設定します。
3. ダイアグラムをドラッグしてズームインします。元に戻すボタンをクリックして期間をリセットします。タイル上のグラフのフィルター プロパティを設定します。
4. タイルのタイトルを設定します。

メトリックス エクスプ ローラーのブレードからピン留めされたタイルには、概要ブレードからピン留めされたタイルよりも多くの編集オプションがあります。

ピン留めした元のタイルは、編集の影響を受けません。

## <a name="switch-between-dashboards"></a>ダッシュボード間の切り替え
複数のダッシュボードを保存して、切り替えることができます。 ピン留めしたグラフやブレードは、そのとき開いていたダッシュボードに追加されます。

![ダッシュボード間を切り替えるには、[ダッシュボード] をクリックし、保存されているダッシュボードを選択します。 新しいダッシュボードを作成して保存するには、[新規] をクリックします。 並べ替えるには、[編集] をクリックします。](./media/app-insights-dashboards/32.png)

たとえば、チーム ルームを全画面で表示するダッシュボードを作成し、全般的な開発用に別のダッシュボードを作成することができます。

このダッシュボードでは、ブレードがタイルとして表示されています。タイルをクリックするとブレードに移動します。 グラフは、元の場所のグラフを複製したものです。

![タイルを表すブレードを開くには、そのタイルをクリックします](./media/app-insights-dashboards/35.png)

## <a name="share-dashboards"></a>ダッシュボードの共有
ダッシュボードを作成すると、他のユーザーとそのダッシュボードを共有できます。

![ダッシュボードのヘッダーで [共有] をクリックします](./media/app-insights-dashboards/41.png)

詳細については、 [ロールとアクセス制御](../../azure-monitor/app/resources-roles-access-control.md)に関するページをご覧ください。

## <a name="create-dashboards-programmatically"></a>プログラムによるダッシュボードの作成
[Azure Resource Manager](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards-create-programmatically) と簡易 JSON エディターを使用して、ダッシュボードの作成を自動化できます。

## <a name="app-navigation"></a>アプリのナビゲーション
概要ブレードはアプリの詳細情報へのゲートウェイです。

* **グラフまたはタイル** - タイルまたはグラフをクリックすると、その表示内容の詳細が表示されます。

### <a name="overview-blade-buttons"></a>概要ブレードのボタン
![概要ブレードの上部のナビゲーション バー](./media/app-insights-dashboards/app-overview-top-nav.png)

* [**メトリックス エクスプローラー**](../../azure-monitor/app/metrics-explorer.md) - パフォーマンスと使用状況の独自のグラフを作成します。
* [**検索**](../../azure-monitor/app/diagnostic-search.md) - 要求、例外、ログ トレースなどのイベントの特定のインスタンスを調べることができます。
* [**分析**](../../azure-monitor/app/analytics.md) - テレメトリに対して強力なクエリを実行できます。
* **時間範囲** - ブレードのすべてのグラフで表示される範囲を調整します。
* **削除** - このアプリの Application Insights リソースを削除します。 この場合、アプリ コードから Application Insights パッケージを削除するか、別の Application Insights リソースにテレメトリを送信するようアプリの[インストルメンテーション キー](../../azure-monitor/app/create-new-resource.md #copy-the-instrumentation-key)を編集する必要があります。

### <a name="essentials-tab"></a>[要点] タブ
* [インストルメンテーション キー](../../azure-monitor/app/create-new-resource.md #copy-the-instrumentation-key) - このアプリ リソースを識別します。

### <a name="app-navigation-bar"></a>アプリのナビゲーション バー
![左側のナビゲーション バー](./media/app-insights-dashboards/app-left-nav-bar.png)

* **概要** - アプリの概要ブレードに戻ります。
* **アクティビティ ログ** - アラートと Azure 管理イベントです。
* [**アクセス制御**](../../azure-monitor/app/resources-roles-access-control.md) - チーム メンバーと他のユーザーにアクセスを提供します。
* [**タグ**](../../azure-resource-manager/resource-group-using-tags.md) - タグを使用して、自分のアプリと他のアプリをグループ化します。

調査

* [**アプリケーション マップ**](app-map.md) - 依存関係情報から取得される、アプリケーションのコンポーネントを示すアクティブ マップ。
* [**スマート検出**](../../azure-monitor/app/proactive-diagnostics.md) - 最近のパフォーマンス アラートを確認します。
* [**Live Stream**](../../azure-monitor/app/live-stream.md) - ほぼ即時のメトリックの固定セットが表示されます。これは、新しいビルドのデプロイ時やデバッグ時に役立ちます。
* [**可用性/Web テスト**](../../azure-monitor/app/monitor-web-app-availability.md) - Web アプリに世界中から通常の要求を送信します。*
* [**失敗、パフォーマンス**](../../azure-monitor/app/web-monitor-performance.md) - 例外、失敗率、アプリへの要求とアプリから[依存関係](../../azure-monitor/app/asp-net-dependencies.md)への要求の応答時間です。
* [**パフォーマンス**](../../azure-monitor/app/web-monitor-performance.md) - 応答時間および依存関係の応答時間です。
* [サーバー](../../azure-monitor/app/web-monitor-performance.md) - パフォーマンス カウンターです。 [Status Monitor をインストール](../../azure-monitor/app/monitor-performance-live-website-now.md)している場合に使用できます。
* **ブラウザー** - ページ ビューと AJAX パフォーマンスです。 [Web ページをインストルメント化](../../azure-monitor/app/javascript.md)している場合に使用できます。
* **使用状況** - ページ ビュー、ユーザー、およびセッションの数です。 [Web ページをインストルメント化](../../azure-monitor/app/javascript.md)している場合に使用できます。

構成

* **作業の開始** - インライン チュートリアルです。
* **プロパティ** - インストルメンテーション キー、サブスクリプション、およびリソース ID です。
* [アラート](../../azure-monitor/app/alerts.md) - メトリック アラートの構成です。
* [連続エクスポート](../../azure-monitor/app/export-telemetry.md) - Azure ストレージへのテレメトリのエクスポートを構成します。
* [パフォーマンス テスト](../../azure-monitor/app/monitor-web-app-availability.md#performance-tests) - Web サイトでの代理負荷を設定します。
* [クォータと価格](../../azure-monitor/app/pricing.md)および[インジェスト サンプリング](../../azure-monitor/app/sampling.md)。
* **API アクセス** - [リリース注釈](annotations.md)を作成します。データ アクセス API で使用されます。
* [**作業項目**](../../azure-monitor/app/diagnostic-search.md#create-work-item) - テレメトリを確認しながらバグを作成できるように、作業項目追跡システムに接続します。

設定

* [**ロック**](../../azure-resource-manager/resource-group-lock-resources.md) - Azure リソースをロックします。
* [**Automation スクリプト**](../../azure-monitor/app/powershell.md) - 新しいリソースを作成する際にテンプレートとして使用できるように、Azure リソースの定義をエクスポートします。


## <a name="video"></a>ビデオ

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>次の手順

|  |  |
| --- | --- |
| [メトリックス エクスプローラー](../../azure-monitor/app/metrics-explorer.md)<br/>メトリックのフィルター処理と分割 |![検索例](./media/app-insights-dashboards/64.png) |
| [診断検索](../../azure-monitor/app/diagnostic-search.md)<br/>イベントや関連イベントを検索して確認し、バグを作成 |![検索例](./media/app-insights-dashboards/61.png) |
| [Analytics](../../azure-monitor/app/analytics.md)<br/>強力なクエリ言語 |![検索例](./media/app-insights-dashboards/63.png) |
