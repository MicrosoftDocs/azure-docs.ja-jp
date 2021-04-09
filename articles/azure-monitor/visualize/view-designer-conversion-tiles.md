---
title: Azure Monitor ビュー デザイナーからブック タイルへの変換
description: Azure Monitor のビューからの移行時に、タイルをブックに変換する方法について詳しく説明します。
author: austonli
ms.author: aul
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 9b8d1840c0b1cea4c03312d38ed892fde94faa4c
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102043339"
---
# <a name="azure-monitor-view-designer-tile-conversions"></a>Azure Monitor ビュー デザイナーでのタイル変換
[ビュー デザイナー](view-designer.md)は Azure Monitor の機能で、Log Analytics ワークスペース内のデータを、グラフ、リスト、タイムラインを使用して視覚化するのに役立つカスタム ビューを作成できます。 これらは段階的に廃止中であり、追加の機能を提供するブックに置き換えられています。 この記事では、さまざまなタイルをブックに変換することの詳細を説明します。

## <a name="donut--list-tile"></a>[Donut & list] (ドーナツとリスト) タイル

![ドーナツ リスト](media/view-designer-conversion-tiles/donut-list.png)

ブック内にドーナツとリストのタイルを再作成するには、2 つの異なる視覚化が必要です。 ドーナツ部分には 2 つのオプションがあります。
どちらの場合も、最初に、**[クエリの追加]** を選択し、ビュー デザイナーの元のクエリをセル内に貼り付けます。

**オプション 1**: **[視覚化]** ドロップダウンから **[円グラフ]** を選択します。 ![円グラフの視覚化メニュー](media/view-designer-conversion-tiles/pie-chart.png)

**オプション 2**: **[視覚化]** ドロップダウンから **[クエリごとに設定]** を選択し、`| render piechart` をクエリに追加します。

 ![[視覚化] メニュー](media/view-designer-conversion-tiles/set-by-query.png)

**例**

元の質問
```KQL
search * 
| summarize AggregatedValue = count() by Type 
| order by AggregatedValue desc
```

更新されたクエリ
```KQL
search * 
| summarize AggregatedValue = count() by Type 
| order by AggregatedValue desc 
| render piechart
```

リストの作成とスパークラインの有効化については、[一般的なタスク](view-designer-conversion-tasks.md)に関する記事を参照してください。

次に示すのは、ドーナツとリストのタイルがブックでどのように再解釈されるかの例です。

![ドーナツ リストのブック](media/view-designer-conversion-tiles/donut-workbooks.png)

## <a name="line-chart--list-tile"></a>[Line chart & list] (折れ線グラフとリスト) タイル
![折れ線グラフ リスト](media/view-designer-conversion-tiles/line-list.png) 

折れ線グラフの部分を再作成するには、次のようにクエリを更新します。

元の質問
```KQL
search * 
| summarize AggregatedValue = count() by Type
```

更新されたクエリ
```KQL
search * 
| make-series Count = count() default=0 on TimeGenerated from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by Type
```

折れ線グラフを視覚化する場合、2 つのオプションがあります

**オプション 1:** **[視覚化]** ドロップダウンから **[折れ線グラフ]** を選択します。
 
 ![[折れ線グラフ] メニュー](media/view-designer-conversion-tiles/line-visualization.png)

**オプション 2**: **[視覚化]** ドロップダウンから **[クエリごとに設定]** を選択し、`| render linechart` をクエリに追加します。

 ![[視覚化] メニュー](media/view-designer-conversion-tiles/set-by-query.png)

**例**

```KQL
search * 
| make-series Count = count() default=0 on TimeGenerated from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by Type 
| render linechart_
```

リストの作成とスパークラインの有効化については、[一般的なタスク](view-designer-conversion-tasks.md)に関する記事を参照してください。

次に示すのは、折れ線グラフとリストのタイルがブックでどのように再解釈されるかの例です。

![折れ線グラフ リストのブック](media/view-designer-conversion-tiles/line-workbooks.png)

## <a name="number--list-tile"></a>[Number & list] (数値とリスト) タイル

 ![タイル リスト](media/view-designer-conversion-tiles/tile-list-example.png)

番号のタイルのために、次のようにクエリを更新します。

元の質問
```KQL
search * 
| summarize AggregatedValue = count() by Computer | count
```

更新されたクエリ
```KQL
search *
| summarize AggregatedValue = count() by Computer 
| summarize Count = count()
```

[視覚化] ドロップダウンを **[タイル]** に変更し、**[タイルの設定]** を選択します。
 ![タイルの視覚化](media/view-designer-conversion-tiles/tile-visualization.png)

**[タイトル]** セクションは空白のままにして、**[左]** を選択します。 **[列を使用:]** の値を **[カウント]** に変更し、**[列レンダラー]** を **[Big Number] (多数)** に変更します。

![タイルの設定](media/view-designer-conversion-tiles/tile-settings.png)

 
リストの作成とスパークラインの有効化については、[一般的なタスク](view-designer-conversion-tasks.md)に関する記事を参照してください。

次に示すのは、数値とリストのタイルがブックでどのように再解釈されるかの例です。

![数値リストのブック](media/view-designer-conversion-tiles/number-workbooks.png)

## <a name="timeline--list"></a>タイムラインとリスト

 ![タイムライン リスト](media/view-designer-conversion-tiles/time-list.png)

タイムラインのために、次のようにクエリを更新します。

元の質問
```KQL
search * 
| sort by TimeGenerated desc
```

更新されたクエリ
```KQL
search * 
| summarize Count = count() by Computer, bin(TimeGenerated,{TimeRange:grain})
```

クエリを横棒グラフとして視覚化する場合、2 つのオプションがあります。

**オプション 1**: **[視覚化]** ドロップダウンから **[横棒グラフ]** を選択します。 ![横棒グラフの視覚化](media/view-designer-conversion-tiles/bar-visualization.png)
 
**オプション 2**: **[視覚化]** ドロップダウンから **[クエリごとに設定]** を選択し、`| render barchart` をクエリに追加します。

 ![[視覚化] メニュー](media/view-designer-conversion-tiles/set-by-query.png)

 
リストの作成とスパークラインの有効化については、[一般的なタスク](view-designer-conversion-tasks.md)に関する記事を参照してください。

次に示すのは、タイムラインとリストのタイルがブックでどのように再解釈されるかの例です。

![タイムライン リストのブック](media/view-designer-conversion-tiles/time-workbooks.png)

## <a name="next-steps"></a>次のステップ

- [ビュー デザイナーからブックへの移行の概要](view-designer-conversion-overview.md)
