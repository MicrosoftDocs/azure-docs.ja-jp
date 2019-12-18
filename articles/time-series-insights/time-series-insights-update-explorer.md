---
title: プレビュー エクスプローラーでデータを視覚化する - Azure Time Series Insights | Microsoft Docs
description: Azure Time Series Insights プレビュー エクスプローラーで使用できる機能とオプションについて説明します。
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 11/13/2019
ms.custom: seodec18
ms.openlocfilehash: 17ba808ebfabb68765cf35bbf0799d117bc6383b
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2019
ms.locfileid: "74133435"
---
# <a name="azure-time-series-insights-preview-explorer"></a>Azure Time Series Insights プレビュー エクスプローラー

この記事では、Azure Time Series Insights プレビュー [デモ Web アプリケーション](https://insights.timeseries.azure.com/preview/demo)内で使用できるさまざまな機能とオプションについて説明します。

## <a name="prerequisites"></a>前提条件

Azure Time Series Insights プレビュー エクスプローラーを使用するには、次のことが済んでいる必要があります。

* Time Series Insights 環境をプロビジョニングしておきます。 インスタンスのプロビジョニングの詳細については、[Azure Time Series Insights プレビュー](./time-series-insights-update-create-environment.md)のチュートリアルをお読みください。
* 作成した Time Series Insights 環境への[データ アクセスをアカウントに対して提供](./time-series-insights-data-access.md)します。 自分自身と同じように他のユーザーにもアクセスを提供できます。
* 環境にデータをプッシュするために Time Series Insights 環境にイベント ソースを追加します。
  * [イベント ハブに接続する方法](./time-series-insights-how-to-add-an-event-source-eventhub.md)を参照してください 
  * [IoT ハブに接続する方法](./time-series-insights-how-to-add-an-event-source-iothub.md)を参照してください

## <a name="explore-the-time-series-insights-preview-explorer"></a>Time Series Insights プレビュー エクスプローラーを試す

Azure Time Series Insights プレビュー エクスプローラーは次の 7 つの要素で構成されます。

[![Time Series Insights プレビュー エクスプローラーの概要](media/v2-update-explorer/preview-explorer-overview.png)](media/v2-update-explorer/preview-explorer-overview.png#lightbox)

1. [環境パネル](#1-environment-panel):すべての Azure Time Series Insights 環境を表示します。
1. [ナビゲーション バー](#2-navigation-bar):**分析**ページと**モデル** ページを切り替えることができます。
1. [階層ツリーと検索パネル](#3-hierarchy-tree-and-search-panel):グラフ化する特定のデータ要素を選択したり、検索したりできます。
1. [タイム シリーズ ウェル](#4-time-series-well):現在選択されているデータ要素がすべて表示されます。
1. [グラフ パネル](#5-chart-panel):現在作業しているグラフが表示されます。
1. [タイムライン](#6-time-editor-panel):作業する時間範囲を変更できます。
1. [アプリ バー](#7-app-bar):現在のテナントなどのユーザー管理オプションが含まれており、テーマと言語の設定を変更することができます。


## <a name="1-environment-panel"></a>1.環境パネル

環境パネルには、アクセスできるすべての Time Series Insights 環境が表示されます。 一覧には、従量課金制 (プレビュー) 環境と S1/S2 環境 (一般提供) が含まれます。 使用する Time Series Insights 環境をクリックするだけですぐにそこに移動します。

1. 表示された環境の横にあるドロップダウン矢印を選択します。

   [![環境パネル](media/v2-update-explorer/environment-panel.png)](media/v2-update-explorer/environment-panel.png#lightbox)

1. 次に、目的の環境を選択します。

## <a name="2-navigation-bar"></a>2.ナビゲーション バー

  [![ナビゲーション バー](media/v2-update-explorer/navigation-bar.png)](media/v2-update-explorer/navigation-bar.png#lightbox)

ナビゲーション バーを使用して、次の 2 つのビューを選択します。

* **分析**:モデル化されたタイム シリーズ データまたはモデル化されていないタイム シリーズ データで、グラフを作成したり、豊富な分析を実行したりするために使用します。
* **モデル**:Time Series Insights プレビューの新しい種類、階層、インスタンスを Time Series Insights のモデルにプッシュするために使用します。

### <a name="model-authoring"></a>モデルの作成

Azure Time Series Insights プレビューは、ご利用のタイム シリーズ モデルに対する完全な CRUD (作成、読み取り、更新、削除) 操作をサポートします。

[![モデル検索パネル](media/v2-update-explorer/model-search-panel.png)](media/v2-update-explorer/model-search-panel.png#lightbox)

* **タイム シリーズ モデルの種類**:Time Series Insights の種類を使用して、計算を行うための変数または数式を定義できます。 それらは、特定の Time Series Insights インスタンスに関連付けられます。 1 つの型は、1 つまたは複数の変数を持つことができます。
* **タイム シリーズ モデルの階層**:階層は、データの体系的な編成です。 階層は、Time Series Insights データ内のさまざまなエンティティ間のリレーションシップを示しています。
* **タイム シリーズ モデルのインスタンス**:インスタンスは、タイム シリーズ自体を表します。 ほとんどの場合は、環境内での資産の一意識別子である **DeviceID** または **AssetID** を持っています。

タイム シリーズ モデルの詳細については、「[Times Series Models](./time-series-insights-update-tsm.md)」(タイム シリーズ モデル) をご覧ください。

## <a name="3-hierarchy-tree-and-search-panel"></a>3.階層ツリーと検索パネル

階層ツリーと検索パネルを利用すると、[タイム シリーズ モデル](./time-series-insights-update-tsm.md)階層を簡単に検索してその内部を移動し、グラフに表示する特定のタイム シリーズ インスタンスを見つけることができます。 選択したインスタンスは、現在のグラフだけでなく、データ ウェルにも追加されます。 

[![階層ツリーと検索パネル](media/v2-update-explorer/hierarchy-search.png)](media/v2-update-explorer/hierarchy-search.png#lightbox)

検索結果ウィンドウでは、階層ビューやリスト ビューで結果を表示し、表示するインスタンスを見つけやすくすることもできます。
 
## <a name="4-time-series-well"></a>4.タイム シリーズ ウェル

ウェルには、選択した Time Series Insights インスタンスに関連付けられているインスタンス フィールドとその他のメタデータが表示されます。 右側にあるチェック ボックスを選択して、現在のグラフでの特定のインスタンスの表示/非表示を切り替えることができます。 

  [![プレビューのウェル](media/v2-update-explorer/preview-well.png)](media/v2-update-explorer/preview-well.png#lightbox)

要素の左にある赤い **[削除]** (ごみ箱) コントロールを選択することで、現在のデータ ウェルから特定のデータ要素を削除できます。 ウェルでは、グラフにおける各要素の表示方法も制御できます。 最小/最大値に陰影を付けたり、データ ポイントを追加したり、時系列の要素を動かしたり、段階的にインスタンスを視覚化したりできます。 

また、探索コントロールでは、タイム シフトや散布図を簡単に作成できます。  

  [![ウェル レイアウト オプション](media/v2-update-explorer/well-layout-options.png)](media/v2-update-explorer/well-layout-options.png#lightbox)

> [!NOTE]
> 次のメッセージが表示される場合、インスタンスには選択した時間範囲のデータがありません。 問題を解決するには、時間範囲を広げるか、またはインスタンスがデータをプッシュしていることを確認します。
>
> ![データ通知なし](media/v2-update-explorer/no-data-warning.png)

## <a name="5-chart-panel"></a>5.グラフ パネル

グラフでは、タイム シリーズのインスタンスを線として表示できます。 Web コントロールをクリックすることで、環境パネル、データ モデル、時間範囲コントロール パネルを折りたたみ、グラフを大きくすることができます。 

  [![プレビューのグラフの概要](media/v2-update-explorer/chart-overview.png)](media/v2-update-explorer/chart-overview.png#lightbox)

1. **グラフの種類**:視覚化に使用できるデータ要素を制御します。

1. **間隔のサイズ**:間隔のサイズ スライダー ツールでは、同じ期間で時間間隔を拡大/縮小できます。 これにより、緩やかな傾向を示す長期間からミリ秒単位の短期間に至るまで、より細かく動作を制御できます。粒度の細かい、高解像度のデータ片を表示することが可能になります。 スライダーの既定の開始点は、解像度、クエリ速度、および粒度のバランスをとって、選択したデータが最適に表示されるよう設定されます。

1. **ズームとパン**:このコントロールをクリックすると、グラフがズームまたはパンされます。

1. **Y 軸コントロール**:Y 軸の使用可能な表示オプションを順番に切り替えます。

    * `Stacked`:各線に個別に Y 軸が作成されます。
    * `Overlap`:これを使用し、同じ Y 軸上で複数の線を重ねます。Y 軸データは、選択されている線に基づいて変化します。
    * `Shared`:すべての Y 軸データが一緒に表示されます。

1. **マーカー要素**:現在選択されているデータ要素とそれに関連する詳細です。

現在のグラフ上にあるデータ ポイントを**左クリック**したまま、選択領域を任意のエンドポイントまでドラッグすることで特定のデータ スライスの詳細を表示できます。 下の画像のように、薄青い選択領域を**右クリック**し、 **[ズーム]** をクリックします。 選択期間にある利用統計情報イベントを表示したり、ダウンロードしたりすることもできます。

  [![プレビューのグラフのズーム](media/v2-update-explorer/preview-chart-zoom.png)](media/v2-update-explorer/preview-chart-zoom.png#lightbox)

**ズーム**操作を実行すると、選択したデータ セットが表示されます。 フォーマット コントロールを選択すると、Time Series Insights データの 3 とおりの Y 軸表現が順番に表示されます。

  [![プレビューのグラフの Y 軸](media/v2-update-explorer/standard-chart.png)](media/v2-update-explorer/standard-chart.png#lightbox)

下は**重複グラフ**の例です。

  [![重複グラフのオプション](media/v2-update-explorer/overlapping-chart.png)](media/v2-update-explorer/overlapping-chart.png#lightbox)

**[その他の操作]** ボタンをクリックすると、展開して **[CSV としてダウンロードする]** オプションと **[Export to Power BI]\(Power BI にエクスポートする\)** オプションが表示されます。

  [![[その他のアクション] オプション](media/v2-update-explorer/more-actions-icon.png)](media/v2-update-explorer/more-actions-icon.png#lightbox)

Time Series Insights ネイティブ Power BI コネクタの詳細は[こちら](concepts-power-bi.md)でご確認いただけます。

## <a name="6-time-editor-panel"></a>6.期間編集パネル

Time Series Insights を使用するときは、最初に時間範囲を選択します。 選択した時間範囲によって、Time Series Insights アップデート ウィジェットで操作できるデータセットを制御します。

  [![日時選択パネル](media/v2-update-explorer/timeline-element.png)](media/v2-update-explorer/timeline-element.png#lightbox)

> [!TIP]
> タイムラインの一部が琥珀 (オレンジ) 色で強調表示され、ウォーム ストアで利用できるデータの範囲を示します。

Time Series Insights アップデートで利用できる次の Web コントロールでは、作業期間を選択できます。 

  [![探索ウェル コントロール](media/v2-update-explorer/exploration-well-control.png)](media/v2-update-explorer/exploration-well-control.png#lightbox)

1. **内部日付範囲スライダー コントロール**:2 つの終端コントロールをドラッグして、目的の時間範囲を調整します。 この内部日付範囲は、外部日付範囲スライダー コントロールによって制限されます。

1. **日付範囲の拡大縮小ボタン**:いずれかのボタンを選択して、目的の期間になるように時間範囲を広げたり狭めたりします。

1. **時間範囲折りたたみコントロール**:この Web コントロールを使用すると、内部日付範囲スライダー ツールを除くすべてのコントロールを非表示にできます。

1. **外部日付範囲スライダー コントロール**:終端コントロールを使用して、内部日付範囲コントロールで使用できる外部日付範囲を選択します。

1. **時間範囲スライダー コントロール**:過去 **30 分**、**過去 12 時間**、**カスタム範囲**など、事前設定された時間範囲の選択肢をすばやく切り替えるために使用します。 この値を変更すると、間隔サイズ スライダー ツールで説明されている使用可能な間隔範囲も変化します。

   [![日付範囲の開始と終了のパネル](media/v2-update-explorer/to-and-from-element.png)](media/v2-update-explorer/to-and-from-element.png#lightbox)

## <a name="7-app-bar"></a>7.アプリ バー

Time Series Insights プレビューのナビゲーション パネルは、Time Series Insights アプリの一番上に表示されます。 次の機能が用意されています。

### <a name="current-session-share-link-control"></a>現在のセッションの共有リンク コントロール

  [![共有アイコン](media/v2-update-explorer/share-icon.png)](media/v2-update-explorer/share-icon.png#lightbox)

URL リンクをチームと共有するには、新しい **[Share]\(共有\)** アイコンを選択します。

  [![インスタンスの URL を共有する](media/v2-update-explorer/share-your-view.png)](media/v2-update-explorer/share-your-view.png#lightbox)

### <a name="tenant-section"></a>テナント セクション

  [![テナントの選択](media/v2-update-explorer/tenant-selection.png)](media/v2-update-explorer/tenant-selection.png#lightbox)

* 現在の Time Series Insights サインイン アカウントの情報が表示されます。
* Time Series Insights の使用可能なテーマを切り替えるために使用します。
* プレビューの[デモ Web アプリ](https://insights.timeseries.azure.com/preview/demo)を表示するために使用します。

### <a name="theme-selection"></a>テーマの選択

新しいテーマを選択するには、右上隅にあるプロフィール アイコンを選択します。 次に、 **[Change Theme]\(テーマの変更\)** を選択します。

  [![テーマの選択](media/v2-update-explorer/theme-selection.png)](media/v2-update-explorer/theme-selection.png#lightbox)

> [!TIP]
> プロフィール アイコンを選択して言語を選択することもできます。

Azure Time Series Insights プレビューでは、2 つのテーマがサポートされています。

* **ライト テーマ**:このドキュメントで示されている既定のテーマです。
* **ダーク テーマ**:次に示すようにエクスプローラーをレンダリングします。

  [![選択されたダーク テーマ](media/v2-update-explorer/dark-theme-selected.png)](media/v2-update-explorer/dark-theme-selected.png#lightbox)

## <a name="s1s2-environment-controls"></a>S1/S2 環境コントロール

### <a name="preview-terms-panel"></a>プレビューの期間パネル

このセクションは、更新された UI でのエクスプローラーの使用を試みる既存の S1/S2 環境のみに適用されます。 一般提供製品とプレビューを組み合わせて使用できます。 既存の UI から更新されたエクスプローラーにいくつかの機能が追加されていますが、既存の Time Series Insights エクスプローラーで S1/S2 環境向けの完全な UI エクスペリエンスを利用できます。 

階層の代わりに、Time Series Insights 条件パネルが表示され、環境内でのクエリを定義します。 述語に基づいてデータをフィルター処理するために使用します。

  [![Where クエリ パネル](media/v2-update-explorer/s1-s2-preview-query.png)](media/v2-update-explorer/s1-s2-preview-query.png#lightbox)

Time Series Insights プレビュー条件編集パネルには、次のパラメーターが表示されます。

**Where**:次の表で示すオペランドのセットを使用して、イベントをすばやくフィルター処理するには、Where 句を使用します。 オペランドを選択して検索を実施した場合、述語はその検索に基づいて自動的に更新されます。 サポートされるオペランドの型には以下が含まれます。

| Operation | サポートされている型   | メモ |
| --- | --- | --- |
| `<`、`>`、`<=`, `>=` | Double、DateTime、TimeSpan | |
| `=`、`!=`、`<>` | String、Bool、Double、DateTime、TimeSpan、NULL |
| `IN` | String、Bool、Double、DateTime、TimeSpan、NULL | すべてのオペランドは同じ型か NULL 定数である必要があります。 |
| `HAS` | string | 右側で使用できるのは定数文字列リテラルのみです。 空の文字列や NULL は許可されません。 |

サポートされているクエリ操作とデータ型の詳細については、[タイム シリーズ式 (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax) に関する記事を参照してください。

### <a name="examples-of-where-clauses"></a>Where 句の例

  [![Where 句の例](media/v2-update-explorer/example-queries.png)](media/v2-update-explorer/example-queries.png#lightbox)

**[メジャー]** :現在のグラフの要素として使用できるすべての数値列 (**Double**) が表示されるドロップダウン リスト。

**分割基準**:このドロップダウン リストには、データのグループ化に使用できるモデル内のすべてカテゴリ列 (String) が表示されます。 最大 5 つの期間を追加して同じ X 軸に表示できます。 目的のパラメーターを入力し、 **[追加]** を選択して新しい条件を追加します。

  [![クエリ/フィルター ビュー 1](media/v2-update-explorer/s1-s2-preview-filtered-view.png)](media/v2-update-explorer/s1-s2-preview-filtered-view.png#lightbox)

次の図で示す表示アイコンを選択して、グラフ パネルでの要素の表示/非表示を切り替えることができます。 クエリを完全に削除するには、赤い **X** を選択します。

  [![クエリ/フィルター オプションの取り消し](media/v2-update-explorer/s1-s2-preview-filtered-view-cancel.png)](media/v2-update-explorer/s1-s2-preview-filtered-view-cancel.png#lightbox)

## <a name="next-steps"></a>次の手順

- Azure Time Series Insights プレビューの[ストレージとイングレス](./time-series-insights-update-storage-ingress.md)の詳細を確認する。

- Time Series Insights プレビューでの[データ モデリング](./time-series-insights-update-tsm.md)に関するドキュメントを読む。

- Time Series Insights インスタンスの[診断とトラブルシューティングの方法](./time-series-insights-update-how-to-troubleshoot.md)を確認する。
