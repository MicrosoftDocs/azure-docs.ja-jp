---
title: Azure Time Series Insights プレビュー エクスプローラーでデータを視覚化する | Microsoft Docs
description: この記事では、Azure Time Series Insights プレビュー エクスプローラー Web アプリで使用できる機能とオプションについて説明します。
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/03/2018
ms.custom: seodec18
ms.openlocfilehash: ed8a829bd73a53dc94bf8b08648b3d6684f718f6
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58109765"
---
# <a name="visualize-data-in-the-explorer-preview"></a>エクスプローラー プレビューでデータを視覚化する

この記事では、Azure Time Series Insights プレビュー [エクスプローラー Web アプリ](https://insights.timeseries.azure.com/preview/samples)で使用できる機能とオプションについて説明します。

## <a name="prerequisites"></a>前提条件

Azure Time Series Insights プレビュー エクスプローラーを使用するには、次のことが済んでいる必要があります。

* Time Series Insights 環境をセットアップします。 詳細については、[チュートリアル: Azure Time Series Insights プレビュー](./time-series-insights-update-create-environment.md)に関する記事をご覧ください。
* 作成した Time Series Insights 環境へのデータ アクセスをアカウントに対して提供します。 自分自身と同じように他のユーザーにもアクセスを提供できます。
* 環境にデータをプッシュするために Time Series Insights 環境にイベント ソースを追加します。

## <a name="learn-about-the-azure-time-series-insights-preview-explorer"></a>Azure Time Series Insights プレビュー エクスプローラーの概要

  ![explorer-one][1]

Azure Time Series Insights プレビュー エクスプローラーは次の要素で構成されます。

* **ナビゲーション バー**:分析ページとモデル ページを切り替えることができます。
* **階層ツリー**:グラフ化する特定のデータ要素を選択できます。
* **タイム シリーズ ウェル**:現在選択されているデータ要素が表示されます。
* **グラフ パネル**:現在作業しているグラフが表示されます。
* **タイムライン**:作業する時間範囲を変更できます。
* **アプリ バー**:現在のテナントなどのユーザー管理オプションが含まれており、テーマと言語の設定を変更することができます。

## <a name="time-series-insights-preview-environment-panel"></a>Time Series Insights プレビュー環境パネル

環境パネルには、アクセスできるすべての Time Series Insights 環境が表示されます。 一覧には、従量課金制環境プレビューと S1/S2 環境 (GA) が含まれます。 使用する Time Series Insights 環境をクリックするだけです。

  ![explorer-two][2]

## <a name="time-series-insights-preview-navigation-menu"></a>Time Series Insights プレビューのナビゲーション メニュー

  ![explorer-three][3]

ナビゲーション メニューでは、Time Series Insights のアプリを切り替えることができます。

* **分析**:モデル化されたタイム シリーズ データまたはモデル化されていないタイム シリーズ データで、グラフを作成したり、豊富な分析を実行したりできます。

* **モデル**:Time Series Insights プレビューの新しい種類、階層、インスタンスを Time Series Insights のモデルにプッシュすることができます。

## <a name="time-series-insights-preview-model-authoring"></a>Time Series Insights プレビューの作成

このアプリでは、タイム シリーズ モデルの作成、読み取り、更新、および削除 (CRUD) 操作を行うことができます。  

* **タイム シリーズ モデルの種類**:Time Series Insights の種類では、計算を行うための変数または数式を定義できます。 それらは、特定の Time Series Insights インスタンスに関連付けられます。 種類は、1 つまたは複数の変数を持つことができます。
* **タイム シリーズ モデルの階層**:階層は、データの体系的な編成です。 階層は、Time Series Insights データ内のさまざまなエンティティ間のリレーションシップを示しています。
* **タイム シリーズ モデルのインスタンス**:インスタンスはタイム シリーズ自体です。 ほとんどの場合は、環境内での資産の一意識別子である DeviceID または AssetID を持っています。

タイム シリーズ モデルの詳細については、「[Times Series Models](./time-series-insights-update-tsm.md)」(タイム シリーズ モデル) をご覧ください。

## <a name="time-series-insights-preview-model-search-panel"></a>Time Series Insights プレビューのモデル検索パネル

モデル検索パネルを使用すると、タイム シリーズ モデル階層を簡単に検索してその内部を移動し、グラフに表示する特定のタイム シリーズ インスタンスを見つけることができます。 選択したインスタンスは、現在のグラフとデータ ウェルの両方に追加されます。

  ![explorer-four][4]

## <a name="time-series-insights-preview-well"></a>Time Series Insights プレビューのウェル

ウェルには、選択したタイム シリーズ インスタンスに関連付けられているインスタンス フィールドとその他のメタデータが表示されます。 右側にあるチェック ボックスを使用して、現在のグラフでの特定のインスタンスの表示/非表示を切り替えることができます。 また、要素の右側にある赤い x コントロールをクリックして、現在のデータ ウェルから特定のデータ要素を削除することもできます。

  ![explorer-five][5]

また、テレメトリ パネルをポップアウトして、データ ウェル内の要素の縦方向の表示をいっそう見やすくすることもできます。

  ![explorer-six][6]

> [!NOTE]
> 次のメッセージが表示される場合、インスタンスには選択した時間範囲のデータがありません。 問題を解決するには、時間範囲を広げるか、またはインスタンスがデータをプッシュしていることを確認します。
>
> ![explorer-seven][7]

## <a name="time-series-insights-preview-chart"></a>Time Series Insights プレビューのグラフ

グラフでは、タイム シリーズのインスタンスを線として表示できます。 Web コントロールをクリックすることで、環境パネル、データ モデル、時間範囲コントロール パネルを折りたたみ、グラフを大きくすることができます。

  ![explorer-eight][8]

1. **選択されている日付範囲**:視覚化に使用できるデータ要素を制御します。

1. **内部日付範囲スライダー ツール**:2 つの終端コントロールをドラッグして、目的の時間範囲を調整します。

1. **時間範囲折りたたみコントロール**:時間範囲パネル エディターを折りたたんだり展開したりします。

1. **Y 軸形式コントロール**:Y 軸の使用可能な表示オプションを順番に切り替えます。

    * `Default`:各線に個別に Y 軸が作成されます。
    * `Stacked`:複数の線が同じ Y 軸に積み重ねられます。Y 軸のデータは選択されている線に基づいて変化します。
    * `Shared`:すべての Y 軸データが一緒に表示されます。

1. **現在のデータ要素**:現在選択されているデータ要素とそれに関連する詳細です。

現在のグラフ上でデータ ポイントを左クリックし、選択した領域を選択した終端にドラッグすることで、特定のデータ スライスを詳細に表示できます。 次の図のように、グレーの選択領域を右クリックしてズームをクリックします。

  ![explorer-nine][9]

ズーム操作を実行すると、選択したデータセットが表示されます。 Time Series Insights データの 3 つの Y 軸表現を順番に切り替えるには、Y 軸形式コントロールをクリックします。

  ![explorer-ten][10]

次に示すのは、共有 Y 軸の例です。

  ![explorer-eleven][11]

## <a name="time-series-insights-preview-time-editor-panel"></a>Time Series Insights プレビューの時間エディター パネル

Time Series Insights プレビューを使用するときは、最初に時間範囲を選択します。 選択した時間範囲によって、Time Series Insights プレビュー ウィジェットで操作できるデータセットを制御します。 Time Series Insights プレビューでは、次の Web コントロールを使用して、作業する時間範囲を選択できます。

  ![explorer-twelve][12]

1. **内部日付範囲スライダー ツール**:2 つの終端コントロールをドラッグして、目的の時間範囲を調整します。 この内部日付範囲は、外部日付範囲スライダー コントロールによって制限されます。

1. **日付範囲の拡大縮小ボタン**:いずれかのボタンを選択して、目的の期間になるように時間範囲を広げたり狭めたりします。

1. **時間範囲折りたたみコントロール**:この Web コントロールを使用すると、内部日付範囲スライダー ツールを除くすべてのコントロールを非表示にできます。

1. **外部日付範囲スライダー コントロール**:終端コントロールを使用して、内部日付範囲コントロールで使用できる外部日付範囲を選択します。

1. **クイック タイム日付範囲ドロップダウン**:過去 30 分、過去 12 時間、カスタム範囲など、事前設定された時間範囲の選択肢をすばやく切り替えることができます。 この値を変更すると、間隔サイズ スライダー ツールで説明されている使用可能な間隔範囲も変化します。

1. **間隔サイズ スライダー ツール**:同じ時間範囲で間隔を拡大縮小できます。 この操作により、大きい時間スライスの間での動きをより細かく制御できます。 最小ミリ秒のスライスまで滑らかにトレンドを表示して、いっそう細かく高い解像度でデータの断片を見ることができます。 スライダーの既定の開始点は、解像度、クエリ速度、および粒度のバランスをとって、選択したデータが最適に表示されるよう設定されます。

1. **日付範囲の開始と終了 Web コントロール**:この Web コントロールを使用すると、クリックして簡単に目的の日付と時刻の範囲を選択できます。 コントロールを使用して、異なるタイム ゾーンに切り替えることもできます。 変更を行った後、現在のワークスペースに適用するには、**[保存]** を選択します。

   ![explorer-thirteen][13]

## <a name="time-series-insights-preview-navigation-panel"></a>Time Series Insights プレビューのナビゲーション パネル

Time Series Insights プレビューのナビゲーション パネルでは、次の機能が提供されます。

  ![explorer-fourteen][14]

### <a name="current-session-share-link-control"></a>現在のセッションの共有リンク コントロール

  ![explorer-fifteen][15]

リンク Web コントロール (強調表示されている部分) を選択すると、現在の Azure Time Series Insights 作業セッションを保存または共有するための URL が生成されます。次のものが含まれます。

* 現在選択されている時間範囲
* 現在選択されている間隔サイズ
* 現在選択されている日付ウェル

### <a name="tenant-section"></a>テナント セクション

  ![explorer-sixteen][16]

* 現在の Time Series Insights ログイン アカウントの情報が表示されます。
* Time Series Insights の使用可能なテーマを切り替えることができます。

### <a name="theme-selection"></a>テーマの選択

Azure Time Series Insights プレビューでは、2 つのテーマがサポートされています。

* **ライト テーマ**:このドキュメントで示されている既定のテーマです。
* **ダーク テーマ**:次に示すようにエクスプローラーをレンダリングします。

  ![explorer-seventeen][17]

ここでは、サポートされている言語を変更することもできます。

## <a name="s1s2-environment-controls"></a>S1/S2 環境コントロール

### <a name="time-series-insights-preview-terms-panel"></a>Time Series Insights プレビュー条件パネル

このセクションは、更新された UI でのエクスプローラーの使用を試みる既存の S1/S2 環境のみに適用されます。 GA 製品とプレビューを組み合わせて使用できます。 既存の UI から更新されたエクスプローラーにいくつかの機能が追加されていますが、既存の Time Series Insights エクスプローラーで S1/S2 環境向けの完全な UI エクスペリエンスを利用できます。  

階層の代わりに、Time Series Insights 条件パネルが表示され、環境内でのクエリを定義します。 述語に基づいてデータをフィルター処理できます。

  ![explorer-eighteen][18]

Time Series Insights プレビュー条件編集パネルには、次のパラメーターが表示されます。

**Where**:Where 句と、次の表で示すオペランドのセットを使用して、イベントをすばやくフィルター処理できます。 オペランドを選択して検索を実施した場合、述語はその検索に基づいて自動的に更新されます。 サポートされるオペランドの型には以下が含まれます。

| Operation | サポートされている型   | メモ |
| --- | --- | --- |
| `<`、`>`、`<=`, `>=` | Double、DateTime、TimeSpan | |
| `=`、`!=`、`<>` | String、Bool、Double、DateTime、TimeSpan、NULL |
| `IN` | String、Bool、Double、DateTime、TimeSpan、NULL | すべてのオペランドは同じ型か NULL 定数である必要があります。 |
| `HAS` | String | 右側で使用できるのは定数文字列リテラルのみです。 空の文字列と NULL は使用できません。 |

サポートされているクエリ操作とデータ型の詳細については、「[Time Series Expression (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx)」(タイム シリーズ式 (TSX)) をご覧ください。

### <a name="examples-of-where-clauses"></a>Where 句の例

  ![explorer-nineteen][19]

**メジャー**:このドロップダウン リストには、現在のグラフの要素として使用できるすべての数値列 (**Double**) が表示されます。

**分割基準**:このドロップダウン リストには、データのグループ化に使用できるモデル内のすべてカテゴリ列 (String) が表示されます。 同じ X 軸上に表示する期間を最大 5 つまで追加できます。 目的のパラメーターを入力し、**[追加]** を選択して新しい条件を追加します。

  ![explorer-twenty][20]

次の図で示す表示アイコンを選択して、グラフ パネルでの要素の表示/非表示を切り替えることができます。 赤い **[x]** をクリックすると、クエリを完全に削除できます。

  ![explorer-twenty-one][21]

## <a name="next-steps"></a>次の手順

次の記事を参照してください。
* [Azure Time Series Insights プレビューのストレージとイングレス](./time-series-insights-update-storage-ingress.md)
* [データ モデリング](./time-series-insights-update-tsm.md)
* [診断とトラブルシューティング](./time-series-insights-update-how-to-troubleshoot.md)

<!-- Images -->
[1]: media/v2-update-explorer/explorer-one.png
[2]: media/v2-update-explorer/explorer-two.png
[3]: media/v2-update-explorer/explorer-three.png
[4]: media/v2-update-explorer/explorer-four.png
[5]: media/v2-update-explorer/explorer-five.png
[6]: media/v2-update-explorer/explorer-six.png
[7]: media/v2-update-explorer/explorer-seven.png
[8]: media/v2-update-explorer/explorer-eight.png
[9]: media/v2-update-explorer/explorer-nine.png
[10]: media/v2-update-explorer/explorer-ten.png
[11]: media/v2-update-explorer/explorer-eleven.png
[12]: media/v2-update-explorer/explorer-twelve.png
[13]: media/v2-update-explorer/explorer-thirteen.png
[14]: media/v2-update-explorer/explorer-fourteen.png
[15]: media/v2-update-explorer/explorer-fifteen.png
[16]: media/v2-update-explorer/explorer-sixteen.png
[17]: media/v2-update-explorer/explorer-seventeen.png
[18]: media/v2-update-explorer/explorer-eighteen.png
[19]: media/v2-update-explorer/explorer-nineteen.png
[20]: media/v2-update-explorer/explorer-twenty.png
[21]: media/v2-update-explorer/explorer-twenty-one.png
