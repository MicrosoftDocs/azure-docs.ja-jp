---
title: 視覚化
description: Azure Synapse ノートブックを使用してデータを視覚化する
author: midesa
ms.author: midesa
ms.reviewer: euang
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 09/13/2020
ms.openlocfilehash: f11693b34048b11c02668e086561b9a6521a5213
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98121527"
---
# <a name="visualize-data"></a>データの視覚化
Azure Synapse は、データ ウェアハウスやビッグ データ分析システム全体にわたって分析情報を取得する時間を早める統合分析サービスです。 データの視覚化は、ご利用のデータに関する分析情報を取得するうえで重要なコンポーネントです。 大規模および小規模のデータを、人間が理解しやすくするのに役立ちます。 また、データのグループ内のパターン、傾向、および外れ値を容易に検出できるようになります。 

Azure Synapse Analytics で Apache Spark を使用する場合は、ご利用のデータを視覚化するのに役立つさまざまな組み込みオプションが用意されています。たとえば、Synapse ノートブック グラフのオプション、人気の高いオープンソース ライブラリへのアクセス、Synapse SQL および Power BI との統合などです。

## <a name="notebook-chart-options"></a>ノートブック グラフのオプション
Azure Synapse notebook を使用する場合は、グラフのオプションを使用して、表形式の結果ビューをカスタマイズされたグラフに変えることができます。 なお、データの視覚化はコードを記述しなくても行うことができます。 

### <a name="displaydf-function"></a>display (df) 関数
```display``` 関数を使用すると、SQL クエリと Apache Spark データフレームおよび RDD を豊富なデータ視覚化に変換することができます。この ```display``` 関数は、PySpark、Scala、Java、および .NET で作成されたデータフレームまたは RDD に対して使用できます。

グラフのオプションにアクセスするには、次のようにします。
1. 既定では、 ```%%sql``` マジック コマンドの出力が、レンダリングされたテーブル ビューに表示されます。 また、Spark DataFrame または Resilient Distributed Dataset (RDD) 関数で ```display(df)``` を呼び出して、レンダリングされたテーブル ビューを生成することもできます。 
   
2. レンダリングされたテーブル ビューを用意できたら、グラフ ビューに切り替えます。
   ![built-in-charts](./media/apache-spark-development-using-notebooks/synapse-built-in-charts.png#lightbox)

3. これで、次の値を指定して、視覚化をカスタマイズできるようになりました。
   | 構成 | 説明 |
   |--|--| 
   | [グラフの種類] | ```display``` 関数では、横棒グラフ、散布図、折れ線グラフなど、さまざまな種類のグラフがサポートされています。 |
   | Key | X 軸の値の範囲を指定します|
   | 値 | Y 軸の値の範囲を指定します |
   | 系列グループ | 集計のグループを決定するために使用されます。 | 
   | 集計 | 視覚化でデータを集計する方法| 
   
   
    > [!NOTE]
    > 既定では、```display(df)``` 関数は、グラフを表示するためにデータの最初の 1000 行のみを取得します。 **[Aggregation over all results]\(すべての結果の集計\)** をオンにして **[適用]** ボタンをクリックすると、データセット全体からグラフが生成されます。 グラフの設定が変更されると、Spark ジョブがトリガーされます。 計算が完了してグラフがレンダリングされるまでに数分かかる場合があることに注意してください。
   
4. 完了すると、最終的な視覚化を表示して操作できるようになります。

### <a name="displaydf-statistic-details"></a>display(df) 統計の詳細
<code>display(df, summary = true)</code> を使用すると、各列の列名、列の種類、一意の値、欠損値など、特定の Apache Spark DataFrame の統計の概要を確認できます。 また、特定の列を選択して、その最小値、最大値、平均値、および標準偏差を表示することもできます。
    ![built-in-charts-summary](./media/apache-spark-development-using-notebooks/synapse-built-in-charts-summary.png#lightbox)
   
### <a name="displayhtmldf-option"></a>displayHTML(df) オプション
Azure Synapse Analytics ノートブックでは、```displayHTML``` 関数を使用した HTML グラフィックスがサポートされています。

次の図は、[D3.js](https://d3js.org/) を使用して視覚化を作成する例です。

   ![d3-js-example](./media/apache-spark-data-viz/d3-boxplot.png#lightbox)

上の視覚化を作成するには、次のコードを実行します。

```python
displayHTML("""<!DOCTYPE html>
<meta charset="utf-8">

<!-- Load d3.js -->
<script src="https://d3js.org/d3.v4.js"></script>

<!-- Create a div where the graph will take place -->
<div id="my_dataviz"></div>
<script>

// set the dimensions and margins of the graph
var margin = {top: 10, right: 30, bottom: 30, left: 40},
  width = 400 - margin.left - margin.right,
  height = 400 - margin.top - margin.bottom;

// append the svg object to the body of the page
var svg = d3.select("#my_dataviz")
.append("svg")
  .attr("width", width + margin.left + margin.right)
  .attr("height", height + margin.top + margin.bottom)
.append("g")
  .attr("transform",
        "translate(" + margin.left + "," + margin.top + ")");

// Create Data
var data = [12,19,11,13,12,22,13,4,15,16,18,19,20,12,11,9]

// Compute summary statistics used for the box:
var data_sorted = data.sort(d3.ascending)
var q1 = d3.quantile(data_sorted, .25)
var median = d3.quantile(data_sorted, .5)
var q3 = d3.quantile(data_sorted, .75)
var interQuantileRange = q3 - q1
var min = q1 - 1.5 * interQuantileRange
var max = q1 + 1.5 * interQuantileRange

// Show the Y scale
var y = d3.scaleLinear()
  .domain([0,24])
  .range([height, 0]);
svg.call(d3.axisLeft(y))

// a few features for the box
var center = 200
var width = 100

// Show the main vertical line
svg
.append("line")
  .attr("x1", center)
  .attr("x2", center)
  .attr("y1", y(min) )
  .attr("y2", y(max) )
  .attr("stroke", "black")

// Show the box
svg
.append("rect")
  .attr("x", center - width/2)
  .attr("y", y(q3) )
  .attr("height", (y(q1)-y(q3)) )
  .attr("width", width )
  .attr("stroke", "black")
  .style("fill", "#69b3a2")

// show median, min and max horizontal lines
svg
.selectAll("toto")
.data([min, median, max])
.enter()
.append("line")
  .attr("x1", center-width/2)
  .attr("x2", center+width/2)
  .attr("y1", function(d){ return(y(d))} )
  .attr("y2", function(d){ return(y(d))} )
  .attr("stroke", "black")
</script>

"""
)

```

## <a name="popular-libraries"></a>人気の高いライブラリ
データの視覚化に関して言えば、Python には、さまざまな機能を多数備えた複数のグラフ ライブラリが用意されています。 既定では、Azure Synapse Analytics のすべての Apache Spark プールに、厳選された人気の高いオープンソース ライブラリのセットが含まれています。 また、Azure Synapse Analytics ライブラリ管理機能を使用して、ライブラリおよびバージョンをさらに追加したり、管理したりすることもできます。 

### <a name="bokeh"></a>Bokeh
```displayHTML(df)``` を使用して、HTML または対話型のライブラリ (**ボケ** など) をレンダリングすることができます。 

次の図は、**ボケ** を使用して、マップ上にグリフをプロットする例です。

   ![bokeh-example](./media/apache-spark-development-using-notebooks/synapse-bokeh-image.png#lightbox)

上の図を描画するには、次のサンプル コードを実行します。

```python
from bokeh.plotting import figure, output_file
from bokeh.tile_providers import get_provider, Vendors
from bokeh.embed import file_html
from bokeh.resources import CDN
from bokeh.models import ColumnDataSource

tile_provider = get_provider(Vendors.CARTODBPOSITRON)

# range bounds supplied in web mercator coordinates
p = figure(x_range=(-9000000,-8000000), y_range=(4000000,5000000),
           x_axis_type="mercator", y_axis_type="mercator")
p.add_tile(tile_provider)

# plot datapoints on the map
source = ColumnDataSource(
    data=dict(x=[ -8800000, -8500000 , -8800000],
              y=[4200000, 4500000, 4900000])
)

p.circle(x="x", y="y", size=15, fill_color="blue", fill_alpha=0.8, source=source)

# create an html document that embeds the Bokeh plot
html = file_html(p, CDN, "my plot1")

# display this html
displayHTML(html)
```

### <a name="matplotlib"></a>Matplotlib
各ライブラリの組み込みのレンダリング関数を使用すれば、Matplotlib のような標準のプロット ライブラリをレンダリングすることができます。

次の図は、**Matplotlib** を使用して横棒グラフを作成する例を示しています。
   ![折れ線グラフの例。](./media/apache-spark-data-viz/matplotlib-example.png#lightbox)

上の図を描画するには、次のサンプル コードを実行します。

```python
# Bar chart

import matplotlib.pyplot as plt

x1 = [1, 3, 4, 5, 6, 7, 9]
y1 = [4, 7, 2, 4, 7, 8, 3]

x2 = [2, 4, 6, 8, 10]
y2 = [5, 6, 2, 6, 2]

plt.bar(x1, y1, label="Blue Bar", color='b')
plt.bar(x2, y2, label="Green Bar", color='g')
plt.plot()

plt.xlabel("bar number")
plt.ylabel("bar height")
plt.title("Bar Chart Example")
plt.legend()
plt.show()
```

### <a name="additional-libraries"></a>その他のライブラリ 
これらのライブラリ以外に、Azure Synapse Analytics ランタイムには、データの視覚化によく使用される、次のライブラリのセットも含まれています。
- [Matplotlib](https://matplotlib.org/)
- [Bokeh](https://bokeh.org/)
- [Seaborn](https://seaborn.pydata.org/) 

利用可能なライブラリとバージョンの最新情報については、Azure Synapse Analytics ランタイムの[ドキュメント](./spark/../apache-spark-version-support.md)を参照してください。

## <a name="connect-to-power-bi-using-apache-spark--sql-on-demand"></a>Apache Spark および SQL オンデマンドを使用して Power BI に接続する
Azure Synapse Analytics と Power BI は密に統合されるため、データ エンジニアは、分析ソリューションを構築することができます。

Azure Synapse Analytics では、さまざまなワークスペース計算エンジンが、Spark プールとサーバーレス SQL プールの間でデータベースとテーブルを共有できます。 [共有メタデータ モデル](../metadata/overview.md)を使用すると、SQL オンデマンドにより Apache Spark テーブルに対してクエリを実行することができます。 完了したら、SQL オンデマンド エンドポイントを Power BI に接続することで、同期された Spark テーブルに対するクエリを容易に実行できるようになります。


## <a name="next-steps"></a>次のステップ
- Spark SQL DW コネクタを設定する方法の詳細については、次を参照してください。[Synapse SQL コネクタ](./spark/../synapse-spark-sql-pool-import-export.md)
- 既定のライブラリを確認します: [Azure Synapse Analytics ランタイム](../spark/apache-spark-version-support.md)