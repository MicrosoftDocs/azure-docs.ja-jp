---
title: Azure Maps にヒート マップ レイヤーを追加する | Microsoft Docs
description: Azure Maps Web SDK にヒート マップ レイヤーを追加する方法。
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: e83b3c5f7f7cb6fa729a628f01f4103d44c19df8
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976196"
---
# <a name="add-a-heat-map-layer"></a>ヒート マップ レイヤーを追加する

ヒート マップは、点密度マップとも呼ばれ、一定の範囲の色を使用して、データの密度を表すために使用されるデータ視覚化の一種です。 多くの場合、マップ上のデータの "ホットスポット" を示すために使用され、大規模なポイント データ セットをレンダリングする場合に適しています。  たとえば、マップ ビュー内の何万もの点をシンボルとしてレンダリングすると、マップ領域の大部分が覆われ、重なり合うシンボルが増えるため、データから多くの分析情報を得ることが困難になります。 ただし、これと同じデータ セットをヒート マップとして視覚化すると、ポイント データが最も密集している場所と他の領域に対する相対密度を簡単に把握できます。 ヒート マップは多くのシナリオで使用されます。 次に例をいくつか示します。

- 気温データが一般的にヒート マップとしてレンダリングされるのは、2 つのデータ ポイント間の気温の概算を示す場合です。
- ノイズ センサーのデータをヒート マップとしてレンダリングすることで、センサーがある場所のノイズの異常がわかるだけでなく、離れた場所の散逸を分析することもできます。 個々の場所のノイズ レベルは高くなくても、複数のセンサーのノイズ感知領域が重なる場合は、その重なる領域でノイズ レベルがより高くなる可能性があり、結果としてヒート マップに可視化されます。
- 各データ ポイントの強度が速度に基づいている重み付けハイト マップとして速度を含む GPS トレースを視覚化する方法は、車両がスピードを出した場所を確認するために適しています。

> [!TIP]
> ヒート マップ レイヤーでは、既定ではデータ ソース内のすべてのジオメトリの座標がレンダリングされます。 ポイント ジオメトリ フィーチャーのみがレンダリングされるようにレイヤーを制限するには、レイヤーの `filter` プロパティを `['==', ['geometry-type'], 'Point']` に設定します。または、MultiPoint フィーチャーも含める場合は、`['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` に設定します。

## <a name="add-a-heat-map-layer"></a>ヒート マップ レイヤーを追加する

ポイントのデータ ソースをヒート マップとしてレンダリングするには、データ ソースを `HeatMapLayer` クラスのインスタンスに渡し、それを次のようにマップに追加します。

次のコードでは、各ヒート ポイントの半径はすべてのズーム レベルで 10 ピクセルです。 ヒート マップ レイヤーをマップに追加するときに、このサンプルではラベル レイヤーの下にそれを挿入し、ラベルがヒート マップの上に明確に表示されるようにしてユーザー エクスペリエンスを向上します。 このサンプルのデータは、[USGS 地震ハザード プログラム](https://earthquake.usgs.gov/)がソースであり、過去 30 日間に発生した重大な地震を表しています。

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Load a data set of points, in this case earthquake data from the USGS.
datasource.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson');

//Create a heatmap and add it to the map.
map.layers.add(new atlas.layer.HeatMapLayer(datasource, null, {
  radius: 10,
  opacity: 0.8
}), 'labels');
```

上記の機能の完全な実行コード サンプルを以下に示します。

<br/>

<iframe height='500' scrolling='no' title='単純なヒート マップ レイヤー' src='//codepen.io/azuremaps/embed/gQqdQB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/gQqdQB/'>Simple Heat Map Layer</a>」Pen を表示します。
</iframe>

## <a name="customizing-the-heat-map-layer"></a>ヒート マップ レイヤーのカスタマイズ

前の例では、半径と不透明度のオプションを設定してヒート マップをカスタマイズしました。 ヒート マップ レイヤーには、カスタマイズのためのオプションがいくつか用意されています。

* `radius`:各データ ポイントをレンダリングするときのピクセルの半径を定義します。 この半径は、固定数または式として設定できます。 式を使用すると、ズーム レベルに基づいて半径をスケーリングすることができます。これにより、マップ上の一貫した空間領域 (たとえば、5 マイルの半径) を表すことができます。
* `color`:ヒート マップの配色方法を指定します。 色のグラデーションはヒート マップによく使用され、`interpolate` 式で作成できます。 ヒート マップの色分けに `step` 式を使用すると、密度は、等高線またはレーダー スタイル マップのような範囲に視覚的に分割されます。 このようなカラー パレットでは、最小濃度値から最大濃度値の色を定義します。 ヒート マップの色の値は、`heatmap-density` 値に対する式として指定されます。 補間式のインデックス 0 の色、または段階式の既定の色では、データのない領域の色が定義されます。これは背景色を定義するために使用できます。 多くの場合、この値は透明または半透明の黒に設定することが好まれます。 色の式の例を次に示します。

| 補間色の式 | 段階的な色の式 | 
|--------------------------------|--------------------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'interpolate',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['linear'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['heatmap-density'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;0, 'transparent',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, 'purple',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.5, '#fb00fb',<br/>&nbsp;&nbsp;&nbsp;&nbsp;1, '#00c3ff'<br/>\] | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'step',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['heatmap-density'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;'transparent',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, 'navy',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.25, 'green',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.50, 'yellow',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.75, 'red'<br/>\] | 

- `opacity`:ヒート マップ レイヤーの不透明度または透明度を指定します。
- `intensity`:各データ ポイントの重みに乗数を適用すると、ヒート マップの全体的な強度を高め、データ ポイントの重みのわずかな違いを視覚化しやすくなります。
- `weight`:既定では、すべてのデータ ポイントの重みは 1 なので、すべてのデータ ポイントの重みは等しくなります。 weight オプションは乗数として機能し、数値または式として設定できます。 重みを 2 などの数値に設定すると、各データ ポイントがマップに 2 回配置された場合に相当するので、密度が 2 倍になります。 weight オプションを数値に設定すると、intensity オプションを使用する場合と同様にヒート マップがレンダリングされます。 ただし、式を使用する場合は、各データ ポイントの重みは、各データ ポイントのプロパティに基づいて決まります。 たとえば、地震データの場合、各データ ポイントは地震を表します。 各地震データ ポイントの重要なメトリックは、マグニチュードの値です。 地震は常に発生していますが、大部分はマグニチュードが低く、感じることすらありません。 式にマグニチュード値を使用して各データ ポイントに重みを割り当てると、より大きな地震をヒート マップ内でよりわかりやすく表すことができます。
- 最小/最大ズーム、可視、フィルターという基本のレイヤー オプションに加え、データ ソースを更新する場合の `source` オプションや、データ ソースがベクター タイル ソースの場合の `source-layer` オプションもあります。

ヒート マップ レイヤーのさまざまなオプションをテストするためのツールを次に紹介します。

<br/>

<iframe height='700' scrolling='no' title='ヒート マップ レイヤーのオプション' src='//codepen.io/azuremaps/embed/WYPaXr/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/WYPaXr/'>Heat Map Layer Options</a>」Pen を表示します。
</iframe>

## <a name="consistent-zoomable-heat-map"></a>一貫性のあるズーム可能なヒート マップ

既定では、ヒート マップ レイヤーにレンダリングされるデータ ポイントの半径には、すべてのズーム レベルの固定ピクセル半径が含まれます。 マップがズームされると、データがまとめて集計され、ヒート マップ レイヤーの外観が変化します。 `zoom` 式を使用すると、各データ ポイントによってマップの同じ物理領域がカバーされるように、各ズーム レベルの半径をスケーリングできます。 これにより、ヒート マップ レイヤーの外観は、より静的で一貫性の高いものになります。 マップの各ズーム レベルは、垂直方向および水平方向のピクセル数がすぐ下のズーム レベルの 2 倍になっています。 ズーム レベルごとに半径が 2 倍になるようにスケーリングすると、すべてのズーム レベルで外観に一貫性のあるヒート マップが作成されます。 この操作は、次のサンプルに示すように、基数 2 の `exponential interpolation` 式で `zoom` 式を使用して実現できます。 マップをズームして、ヒート マップがズーム レベルに応じてどのように変化するかを確認してください。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="一貫性のあるズーム可能なヒート マップ" src="//codepen.io/azuremaps/embed/OGyMZr/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a> 上で、Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) によるペンの<a href='https://codepen.io/azuremaps/pen/OGyMZr/'>一貫性のあるズーム可能なヒート マップ</a>をご覧ください。
</iframe>

> [!TIP]
> データ ソースに対するクラスタリングを有効にすると、相互に近いポイントはクラスター化されたポイントとしてグループ化されます。 各クラスターのポイント数は、ヒート マップの重み式として使用できます。また、レンダリングする必要があるポイント数を大幅に減らすことができます。 クラスターのポイント数は、次のようにポイント フィーチャーの `point_count` プロパティに格納されています。 
> ```JavaScript
> var layer = new atlas.layer.HeatMapLayer(datasource, null, {
>    weight: ['get', 'point_count']
> });
> ```
> クラスタリングの半径がわずか数ピクセルの場合、レンダリングによる視覚的な違いはほとんどありません。 半径が大きいほど各クラスター内でグループ化されるポイントが増えるので、ヒート マップのパフォーマンスは向上しますが、違いがより顕著になります。

## <a name="next-steps"></a>次の手順

この記事で使われているクラスとメソッドの詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [HeatMapLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HeatMapLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)

マップに追加するコード例の詳細については、次の記事を参照してください。

> [!div class="nextstepaction"]
> [データ ソースを作成する](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [データドリブンのスタイルの式を使用する](data-driven-style-expressions-web-sdk.md)