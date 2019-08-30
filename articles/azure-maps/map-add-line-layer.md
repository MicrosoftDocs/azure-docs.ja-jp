---
title: Azure Maps に線レイヤーを追加する | Microsoft Docs
description: Azure Maps Web SDK に線レイヤーを追加する方法。
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: f07e36d82c9044a212cda8173df9fe0a9544393a
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976564"
---
# <a name="add-a-line-layer-to-the-map"></a>マップに線レイヤーを追加する

線レイヤーを使用すると、マップ上にパスまたはルートとして `LineString` および `MultiLineString` フィーチャーをレンダリングできます。 また、線レイヤーを使用して、`Polygon` および `MultiPolygon` フィーチャーのアウトラインをレンダリングすることもできます。 データ ソースは、表示するデータを提供するために線レイヤーに接続されています。 

> [!TIP]
> 線レイヤーの既定では、データ ソース内の多角形と線の座標がレンダリングされます。 LineString フィーチャーのみがレンダリングされるようにレイヤーを制限するには、レイヤーの `filter` プロパティを `['==', ['geometry-type'], 'LineString']` に設定します。または、MultiLineString フィーチャーも含める場合は、`['any', ['==', ['geometry-type'], 'LineString'], ['==', ['geometry-type'], 'MultiLineString']]` に設定します。

次のコードは、線を作成し、それをデータ ソースに追加し、[LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) クラスを使用して線レイヤーでレンダリングする方法を示しています。

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

<iframe height='500' scrolling='no' title='マップに線を追加する' src='//codepen.io/azuremaps/embed/qomaKv/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/qomaKv/'>Add a line to a map</a>」Pen を表示します。
</iframe>

線レイヤーは、「[LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)」と「[データドリブンのスタイルの式を使用する](data-driven-style-expressions-web-sdk.md)」を使用してスタイルを指定できます。

## <a name="add-symbols-along-a-line"></a>線に沿ってシンボルを追加する

このサンプルでは、マップ上の線に沿って矢印アイコンを追加する方法を示します。 シンボル レイヤーを使用する場合は、"placement" オプションを "line" に設定します。これにより、シンボルが線に沿ってレンダリングされ、アイコンが回転します (0 度 = 右)。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="線に沿って矢印を表示する" src="//codepen.io/azuremaps/embed/drBJwX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/drBJwX/'>Show arrow along line</a>」Pen を表示します。
</iframe>

> [!TIP]
> Azure Maps Web SDK には、シンボル レイヤーで使用できるカスタマイズ可能な画像テンプレートがいくつか用意されています。 詳細については、[画像テンプレートの使用方法](how-to-use-image-templates-web-sdk.md)のドキュメントを参照してください。

<a name="line-stroke-gradient"></a>

## <a name="add-a-stroke-gradient-to-a-line"></a>ストロークのグラデーションを線に追加する

線に 1 つのストローク色を適用できるだけでなく、線を色のグラデーションで塗りつぶして 1 つの線分から次の線分への遷移を示すこともできます。 たとえば、線のグラデーションを使用して、時間と距離の変化やオブジェクトを接続した線のさまざまな温度を表すことができます。 線にこのフィーチャーを適用するためには、データ ソースで `lineMetrics` オプションが true に設定されている必要があります。それにより、色のグラデーションを線の `strokeColor` オプションに渡すことができます。 ストロークのグラデーション式は、計算済みのメトリックを式に公開する `['line-progress']` データ式を参照する必要があります。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="ストロークのグラデーションのある線" src="//codepen.io/azuremaps/embed/wZwWJZ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/wZwWJZ/'>Line with Stroke Gradient</a>」Pen を表示します。
</iframe>

## <a name="customize-a-line-layer"></a>線レイヤーをカスタマイズする

線レイヤーにはいくつかのスタイル オプションがあります。 次のツールでそれらを試してください。

<br/>

<iframe height='700' scrolling='no' title='線レイヤーのオプション' src='//codepen.io/azuremaps/embed/GwLrgb/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/GwLrgb/'>Line Layer Options</a>」Pen を表示します。
</iframe>

## <a name="next-steps"></a>次の手順

この記事で使われているクラスとメソッドの詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) 

> [!div class="nextstepaction"]
> [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)

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