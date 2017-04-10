---
title: "Azure Application Insights のダッシュボードおよびナビゲーション | Microsoft Docs"
description: "重要な APM グラフとクエリのビューを作成します。"
services: application-insights
documentationcenter: 
author: alancameronwills
manager: carmonm
ms.assetid: 39b0701b-2fec-4683-842a-8a19424f67bd
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 03/14/2017
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: fd35f1774ffda3d3751a6fa4b6e17f2132274916
ms.openlocfilehash: 097a8aeecc783199b25d95dbc470dcabefa37ced
ms.lasthandoff: 03/16/2017


---
# <a name="navigation-and-dashboards-in-the-application-insights-portal"></a>Application Insights ポータルのナビゲーションおよびダッシュボード
[プロジェクトに Application Insights を設定](app-insights-overview.md)すると、[Azure Portal](https://portal.azure.com) のプロジェクトの Application Insights リソースに、アプリのパフォーマンスと使用状況に関するテレメトリ データが表示されます。

## <a name="find-your-telemetry"></a>テレメトリの検索
[Azure Portal](https://portal.azure.com) にサインインし、アプリ用に作成した Application Insights リソースに移動します。

![[参照] をクリックし、[Application Insights]、アプリの順に選択します。](./media/app-insights-dashboards/00-start.png)

アプリの [概要] ブレード (ページ) には、アプリの主要な診断メトリックの概要が表示されます。また、[概要] ブレードはポータルの他の機能のゲートウェイでもあります。

![テレメトリを表示する主な方法](./media/app-insights-dashboards/010-oview.png)

グラフとグリッドをカスタマイズし、ダッシュボードにピン留めできます。 これにより、さまざまなアプリの重要なテレメトリをまとめて、中央のダッシュボードに表示することができます。

## <a name="dashboards"></a>ダッシュボード
[Microsoft Azure ポータル](https://portal.azure.com) にサインインすると、まず、ダッシュボードが表示されます。 ここでは、 [Azure Application Insights](app-insights-overview.md) のテレメトリなどを含む、すべての Azure リソースから最も重要なグラフをまとめて表示することができます。

![カスタマイズされたダッシュボード](./media/app-insights-dashboards/31.png)

1. Application Insights でアプリなどの**特定のリソースに移動**する: 左側のバーを使用します。
2. **現在のダッシュボードに戻る**か、最近アクセスした他のビューに切り替える: 左上のドロップダウン メニューを使用します。
3. **ダッシュボードを切り替える**: ダッシュボードのタイトルにあるドロップダウン メニューを使用します。
4. **ダッシュボードを作成、編集、共有する**: ダッシュボード ツール バーを使用します。
5. **ダッシュボードを編集する**: タイルにマウス ポインターを合わせ、上部のバーを使用してタイルを移動、カスタマイズ、削除します。

## <a name="add-to-a-dashboard"></a>ダッシュボードへの追加
特に重要なブレードやグラフのセットがあれば、そのコピーをダッシュボードにピン留めしておきましょう。 ダッシュボードを開いたときにすぐに見ることができます。

![グラフをピン留めするには、マウスでポイントし、ヘッダーの [...] をクリックします。](./media/app-insights-dashboards/33.png)

1. ダッシュボードにグラフをピン留めします。 グラフのコピーがダッシュボードに表示されます。
2. ブレード全体をダッシュボードにピン留めします。クリックして進むことのできるタイルとしてブレード全体がダッシュボードに表示されます。
3. 現在のダッシュボードに戻るには、左上隅をクリックします。 その後ドロップダウン メニューを使用して現在のビューに戻ることができます。

グラフはタイルにグループ化されます。1 つのタイルに複数のグラフを含めることができます。 タイル全体をダッシュボードにピン留めします。

### <a name="pin-any-query-in-analytics"></a>Analytics の任意のクエリをピン留めする
[共有](#share-dashboards-with-your-team)ダッシュボードに [Analytics のグラフをピン留め](app-insights-analytics-using.md#pin-to-dashboard)することもできます。 これにより、任意のクエリのグラフを標準的なメトリックの横に追加できます。 (この機能は課金対象です。)

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

詳細については、 [ロールとアクセス制御](app-insights-resources-roles-access-control.md)に関するページをご覧ください。

## <a name="app-navigation"></a>アプリのナビゲーション
概要ブレードはアプリの詳細情報へのゲートウェイです。

* **グラフまたはタイル** - タイルまたはグラフをクリックすると、その表示内容の詳細が表示されます。

### <a name="overview-blade-buttons"></a>概要ブレードのボタン
![概要ブレードの上部のナビゲーション バー](./media/app-insights-dashboards/app-overview-top-nav.png)

* [**メトリックス エクスプローラー**](app-insights-metrics-explorer.md) - パフォーマンスと使用状況の独自のグラフを作成します。
* [**検索**](app-insights-diagnostic-search.md) - 要求、例外、ログ トレースなどのイベントの特定のインスタンスを調べることができます。
* [**分析**](app-insights-analytics.md) - テレメトリに対して強力なクエリを実行できます。
* **時間範囲** - ブレードのすべてのグラフで表示される範囲を調整します。
* **削除** - このアプリの Application Insights リソースを削除します。 この場合、アプリ コードから Application Insights パッケージを削除するか、別の Application Insights リソースにテレメトリを送信するようアプリの[インストルメンテーション キー](app-insights-create-new-resource.md#copy-the-instrumentation-key)を編集する必要があります。

### <a name="essentials-tab"></a>[要点] タブ
* [インストルメンテーション キー](app-insights-create-new-resource.md#copy-the-instrumentation-key) - このアプリ リソースを識別します。
* 価格 - 機能を使用可能にし、ボリュームの上限を設定します。

### <a name="app-navigation-bar"></a>アプリのナビゲーション バー
![左側のナビゲーション バー](./media/app-insights-dashboards/app-left-nav-bar.png)

* **概要** - アプリの概要ブレードに戻ります。
* **アクティビティ ログ** - アラートと Azure 管理イベントです。
* [**アクセス制御**](app-insights-resources-roles-access-control.md) - チーム メンバーと他のユーザーにアクセスを提供します。
* [**タグ**](../azure-resource-manager/resource-group-using-tags.md) - タグを使用して、自分のアプリと他のアプリをグループ化します。

調査

* [**アプリケーション マップ**](app-insights-app-map.md) - 依存関係情報から取得される、アプリケーションのコンポーネントを示すアクティブ マップ。
* [**スマート検出**](app-insights-proactive-diagnostics.md) - 最近のパフォーマンス アラートを確認します。
* [**Live Stream**](app-insights-live-stream.md) - ほぼ即時のメトリックの固定セットが表示されます。これは、新しいビルドのデプロイ時やデバッグ時に役立ちます。
* [**可用性/Web テスト**](app-insights-monitor-web-app-availability.md) - Web アプリに世界中から通常の要求を送信します。*
* [**失敗、パフォーマンス**](app-insights-web-monitor-performance.md) - 例外、失敗率、アプリへの要求とアプリから[依存関係](app-insights-asp-net-dependencies.md)への要求の応答時間です。
* [**パフォーマンス**](app-insights-web-monitor-performance.md) - 応答時間および依存関係の応答時間です。
* [サーバー](app-insights-web-monitor-performance.md) - パフォーマンス カウンターです。 [Status Monitor をインストール](app-insights-monitor-performance-live-website-now.md)している場合に使用できます。
* **ブラウザー** - ページ ビューと AJAX パフォーマンスです。 [Web ページをインストルメント化](app-insights-javascript.md)している場合に使用できます。
* **使用状況** - ページ ビュー、ユーザー、およびセッションの数です。 [Web ページをインストルメント化](app-insights-javascript.md)している場合に使用できます。

構成

* **作業の開始** - インライン チュートリアルです。
* **プロパティ** - インストルメンテーション キー、サブスクリプション、およびリソース ID です。
* [アラート](app-insights-alerts.md) - メトリック アラートの構成です。
* [連続エクスポート](app-insights-export-telemetry.md) - Azure ストレージへのテレメトリのエクスポートを構成します。
* [パフォーマンス テスト](app-insights-monitor-web-app-availability.md#performance-tests) - Web サイトでの代理負荷を設定します。
* [クォータと価格](app-insights-pricing.md)および[インジェスト サンプリング](app-insights-sampling.md)。
* **API アクセス** - [リリース注釈](app-insights-annotations.md)を作成します。データ アクセス API で使用されます。
* [**作業項目**](app-insights-diagnostic-search.md#create-work-item) - テレメトリを確認しながらバグを作成できるように、作業項目追跡システムに接続します。

設定

* [**ロック**](../azure-resource-manager/resource-group-lock-resources.md) - Azure リソースをロックします。
* [**Automation スクリプト**](app-insights-powershell.md) - 新しいリソースを作成する際にテンプレートとして使用できるように、Azure リソースの定義をエクスポートします。

サポート

* **サポート要求** - 有料のサブスクリプションが必要です。 [支援を得る方法](app-insights-get-dev-support.md)に関する記事もご覧ください。

## <a name="video"></a>ビデオ

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>次のステップ

|  |  |
| --- | --- |
| [メトリックス エクスプローラー](app-insights-metrics-explorer.md)<br/>メトリックのフィルター処理と分割 |![検索例](./media/app-insights-dashboards/64.png) |
| [診断検索](app-insights-diagnostic-search.md)<br/>イベントや関連イベントを検索して確認し、バグを作成 |![検索例](./media/app-insights-dashboards/61.png) |
| [Analytics](app-insights-analytics.md)<br/>強力なクエリ言語 |![検索例](./media/app-insights-dashboards/63.png) |

