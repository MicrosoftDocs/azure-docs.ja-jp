---
title: マップに線レイヤーを追加する | Microsoft Azure Maps
description: マップに線を追加する方法について説明します。 Azure Maps Web SDK を使用し、マップに行レイヤーを追加したり、シンボルやカラー グラデーションで行をカスタマイズしたりする例について説明します。
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: fedf1bc37661100404106859d3aef52b3eddf06d
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102048139"
---
# <a name="add-a-line-layer-to-the-map"></a>マップに線レイヤーを追加する

線レイヤーを使用すると、マップ上にパスまたはルートとして `LineString` および `MultiLineString` フィーチャーをレンダリングできます。 また、線レイヤーを使用して、`Polygon` および `MultiPolygon` フィーチャーのアウトラインをレンダリングすることもできます。 データ ソースは、レンダリングするデータを提供するために線レイヤーに接続されます。 

> [!TIP]
> 線レイヤーの既定では、データ ソース内の多角形と線の座標がレンダリングされます。 LineString フィーチャーのみがレンダリングされるようにレイヤーを制限するには、レイヤーの `filter` プロパティを `['==', ['geometry-type'], 'LineString']` に設定します。または、MultiLineString フィーチャーも含める場合は、`['any', ['==', ['geometry-type'], 'LineString'], ['==', ['geometry-type'], 'MultiLineString']]` に設定します。

次のコードは、線の作成方法を示しています。 データ ソースに線を追加した後、[LineLayer](/javascript/api/azure-maps-control/atlas.layer.linelayer) クラスを使用して、線レイヤーで描画します。

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a line and add it to the data source.
dataSource.add(new atlas.data.LineString([[-73.972340, 40.743270], [-74.004420, 40.756800]]));
  
//Create a line layer to render the line to the map.
map.layers.add(new atlas.layer.LineLayer(dataSource, null, {
    strokeColor: 'blue',
    strokeWidth: 5
}));
```

上記の機能の完全な実行コード サンプルを以下に示します。

<br/>

<iframe height='500' scrolling='no' title='マップに線を追加する' src='//codepen.io/azuremaps/embed/qomaKv/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/qomaKv/'>Add a line to a map</a>」Pen を表示します。
</iframe>

線レイヤーは、「[LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions)」と「[データドリブンのスタイルの式を使用する](data-driven-style-expressions-web-sdk.md)」を使用してスタイルを指定できます。

## <a name="add-symbols-along-a-line"></a>線に沿ってシンボルを追加する

このサンプルでは、マップ上の線に沿って矢印アイコンを追加する方法を示します。 シンボル レイヤーを使用する場合は、"placement" オプションを "line" に設定します。 このオプションを選択すると、線に沿って記号がレンダリングされ、アイコンが回転します (0 度 = 右)。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="線に沿って矢印を表示する" src="//codepen.io/azuremaps/embed/drBJwX/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" loading="lazy" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/drBJwX/'>Show arrow along line</a>」Pen を表示します。
</iframe>

> [!TIP]
> Azure Maps Web SDK には、シンボル レイヤーで使用できるカスタマイズ可能な画像テンプレートがいくつか用意されています。 詳細については、[画像テンプレートの使用方法](how-to-use-image-templates-web-sdk.md)のドキュメントを参照してください。

<a name="line-stroke-gradient"></a>

## <a name="add-a-stroke-gradient-to-a-line"></a>ストロークのグラデーションを線に追加する

線に 1 つのストロークの色を適用できます。 さらに、線を色のグラデーションで塗りつぶして、1 つの線分から次の線分への遷移を示すこともできます。 たとえば、線のグラデーションを使用して、時間と距離の変化やオブジェクトを接続した線のさまざまな温度を表すことができます。 線にこのフィーチャーを適用するためには、データ ソースで `lineMetrics` オプションが `true` に設定されている必要があります。それにより、色のグラデーション式を線の `strokeColor` オプションに渡すことができます。 ストロークのグラデーション式は、計算済みのメトリックを式に公開する `['line-progress']` データ式を参照する必要があります。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="ストロークのグラデーションのある線" src="//codepen.io/azuremaps/embed/wZwWJZ/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" loading="lazy" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/wZwWJZ/'>Line with Stroke Gradient</a>」Pen を表示します。
</iframe>

## <a name="customize-a-line-layer"></a>線レイヤーをカスタマイズする

線レイヤーには、さまざまなスタイル オプションがあります。 次のツールでそれらをお試しください。

<br/>

<iframe height='700' scrolling='no' title='線レイヤーのオプション' src='//codepen.io/azuremaps/embed/GwLrgb/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/GwLrgb/'>Line Layer Options</a>」Pen を表示します。
</iframe>

## <a name="next-steps"></a>次のステップ

この記事で使われているクラスとメソッドの詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [LineLayer](/javascript/api/azure-maps-control/atlas.layer.linelayer) 

> [!div class="nextstepaction"]
> [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions)

マップに追加できる他のコード サンプルについては、次の記事をご覧ください。

> [!div class="nextstepaction"]
> [データ ソースを作成する](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [ポップアップを追加する](map-add-popup.md)

> [!div class="nextstepaction"]
> [データドリブンのスタイルの式を使用する](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [画像テンプレートの使用方法](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [多角形レイヤーを追加する](map-add-shape.md)