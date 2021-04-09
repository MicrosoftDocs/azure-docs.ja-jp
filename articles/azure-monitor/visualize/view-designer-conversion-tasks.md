---
title: Azure Monitor ビュー デザイナーからブックへの変換の一般的なタスク
description: Azure Monitor のビューからブックへの移行時の一般的なタスク。
author: austonli
ms.author: aul
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: d479e64620519491ba4847349ea33820747fcea7
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102043475"
---
# <a name="view-designer-to-workbooks-conversion-common-tasks"></a>ビュー デザイナーからブックへの変換の一般的なタスク
[ビュー デザイナー](view-designer.md)は Azure Monitor の機能で、Log Analytics ワークスペース内のデータを、グラフ、リスト、タイムラインを使用して視覚化するのに役立つカスタム ビューを作成できます。 これらは段階的に廃止中であり、追加の機能を提供するブックに置き換えられています。 この記事では、ビューをブックに変換する場合の一般的なタスクの詳細について説明します。


## <a name="quickstart-with-preset-view-designer-templates"></a>プリセットのビュー デザイナー テンプレートを使用したクイックスタート

Log Analytics ワークスペースのブックには、ビュー デザイナーの一部のビューに対応するように作成されたテンプレートが既に用意されています。 **[View Designer Guides] (ビュー デザイナー ガイド)** カテゴリで **[View Designer Transition Guide] (ビュー デザイナー移行ガイド)** を選択して、利用できるオプションを確認するか、いずれかのプリセット テンプレートを選択します。

![サンプル テンプレート](media/view-designer-conversion-tasks/templates.png)

## <a name="enabling-time-range-filter"></a>時間範囲フィルターの有効化
ビュー デザイナーには既定の時間範囲フィルターが組み込まれていますが、ブックでは、この設定は既定で有効になっていません。 ブックを使用すると、ユーザーは自分のデータ ログにとってより適切な場合がある独自の時間範囲フィルターを作成できます。 下に、フィルターを生成する手順を示します。

**[パラメーターの追加]** オプションを選択します。 既定の **[スタイル]** は *[ピル]* に設定されています。

![パラメーターを追加する](media/view-designer-conversion-tasks/add-param.png)

 **[パラメーターの追加]** ボタンを選択します。

![パラメーターの追加](media/view-designer-conversion-tasks/add-parameter.png)

サイドバー メニューから **[パラメーター名]** テキストボックスに、「*TimeRange*」と入力します。 **[Parameter Type] (パラメーターの型)** を *[時間範囲の選択]* として設定します。 **[Required?] (必須?)** チェックボックスを選択します。

![パラメーター メニュー](media/view-designer-conversion-tasks/parameter-menu.png)

サイドバー メニューの左上隅でパラメーターを保存します。 ドロップダウンでは、既定で *未設定* のままにすることも、*24 時間* などの既定の **TimeRange** 値を選択することもできます。 **[編集完了]** を選択します。

パラメーター名の周りに中かっこ {} を追加することで、クエリ内でパラメーターを使用できます。 パラメーターの詳細については、[パラメーターに関する Workbooks のドキュメント](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Documentation/Parameters/Parameters.md)に記載されています。

## <a name="updating-queries-with-the-timerange-parameter"></a>TimeRange パラメーターを使用してクエリを更新する

### <a name="option-1-select-timerange-from-the-time-range-dropdown"></a>オプション 1: [時間の範囲] ドロップダウンから [TimeRange] を選択する

![時間のパラメーター](media/view-designer-conversion-tasks/time-parameter.png)

### <a name="option-2-update-your-log-queries"></a>オプション 2: ログ クエリを更新する

次の例に示すように、クエリ内に `| where TimeGenerated {TimeRange}` という行を追加します。

元の質問
```KQL
search * 
| summarize count() by Type
```

更新されたクエリ
```KQL
search * 
| where TimeGenerated {TimeRange} 
| summarize count() by Type
```

## <a name="including-a-list"></a>リストを含める
ビュー デザイナーのほとんどのビューにはリストが含まれています。この標準リストは、ブック内に再現できます。

![タイル リスト](media/view-designer-conversion-tasks/tile-list.png)

セル オプションから **[クエリの追加]** をクリックし、視覚化を追加します。

![パラメーターを追加する](media/view-designer-conversion-tasks/add-param.png)

ビュー デザイナーでは、元の例の構文と一致する既定のクエリが使用されます。 クエリを次の例のように更新されたフォームに変更することで、これを更新できます。

元の質問
```KQL
search * 
| summarize AggregatedValue = count() by Type
```

更新されたクエリ
```KQL
search * 
| summarize Count = count() by Type
```

これにより、次のようなリストが生成されます。

![リストの例](media/view-designer-conversion-tasks/list-example.png)

## <a name="enabling-sparklines"></a>スパークラインの有効化
グリッドの一般的な機能は、スパークラインを追加し、長期にわたるさまざまなデータ パターンをまとめることです。 ビュー デザイナーには、ブックと同様に、すべての一覧に対して **スパークラインを有効にする** 機能が用意されています。 ビュー デザイナーに適合するスパークラインをデータに含めるには、次の例のように、元のクエリを使用してデータを結合します。

元の質問
```KQL
search *
| summarize AggregatedValue = count() by Type) on Type
```

更新されたクエリ
```KQL
search * 
| summarize AggregatedValue = count() by Type
| join kind = inner (search * 
    | make-series Trend = count() default=0 on TimeGenerated from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by Type) on Type
| project Type, AggregatedValue, Trend
```

**[列の設定]** を選択します。
![列の設定](media/view-designer-conversion-tasks/column-settings.png)

**[列レンダラー]** ドロップダウンを *[Spark area] (スパーク領域)* に更新します。
![スパークライン](media/view-designer-conversion-tasks/sparkline.png)

設定を保存し、再度クエリを実行して、スパークラインを含むようにテーブルを更新します。

結果のグリッドは次のようになります。 ![スパークラインの例 ](media/view-designer-conversion-tasks/sparkline-example.png)

## <a name="advanced-cell-settings"></a>セルの詳細設定
ビュー デザイナーを反映させるため、ブックのセル サイズを変更する、ログにピンや外部リンクを追加するなどのタスクを実行できます。

**詳細設定** にアクセスするには、各セルの下部にある歯車アイコンを選択します。

![詳細設定](media/view-designer-conversion-tasks/advanced-settings.png)

これにより、さまざまなオプションがあるメニューが表示されます。

![[詳細設定] の設定](media/view-designer-conversion-tasks/advanced-settings-settings.png)

ピンや外部クエリへのリンクを追加するには、対応するチェックボックスをオンにします。 セルにタイトルを追加するには、**[グラフのタイトル]** セクションに、必要なタイトルを入力します。

既定では、どのブックのセルもページ幅全体に表示されるように設定されますが、これは、**[詳細設定]** メニューの **[スタイル]** タブでセルを縮小することによって調整できます

![[詳細設定] のスタイル](media/view-designer-conversion-tasks/advanced-settings-style.png)

 
## <a name="additional-parameters"></a>追加パラメーター
ブックに新しいパラメーターを作成するには、**[パラメーターの追加]** を選択します。 

サブスクリプションを選択するには、サイド メニューの **[パラメーター名]** フィールドに「*Subscription*」と入力し、**[Parameter type] (パラメーターの型)** ドロップダウンから *[Subscription Picker]* (サブスクリプションの選択) を選択します

![サブスクリプション メニュー](media/view-designer-conversion-tasks/subscription-filter.png)

リソースを選択するには、サイド メニューの **[パラメーター名]** フィールドに「*Resource*」と入力し、**[Parameter type] (パラメーターの型)** ドロップダウンから *[Resource Picker]* (リソースの選択) を選択します。

![リソース メニュー](media/view-designer-conversion-tasks/resource-filter.png)

これでドロップダウンが挿入され、さまざまなサブスクリプションとリソースにアクセスできるようになります。

![サブスクリプション リソースのドロップダウン](media/view-designer-conversion-tasks/subscription-resource.png)


## <a name="next-steps"></a>次のステップ
- [タイルの変換](view-designer-conversion-tiles.md)
