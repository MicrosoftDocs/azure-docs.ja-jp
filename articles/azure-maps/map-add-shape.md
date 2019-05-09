---
title: Azure Maps を使ってシェイプを追加する | Microsoft Docs
description: Javascript マップにシェイプを追加する方法
author: jingjing-z
ms.author: jinzh
ms.date: 10/30/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: f61c7a939902ee5d02b2e9ba896c7555968f9d0d
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2019
ms.locfileid: "59547987"
---
# <a name="add-a-shape-to-a-map"></a>マップにシェイプを追加する

この記事では、線レイヤーと多角形レイヤーを使用してマップ上にジオメトリをレンダリングする方法を示します。 Azure Maps の Web SDK では、[拡張 GeoJSON スキーマ](extend-geojson.md#circle)で定義されている円のジオメトリの作成もサポートしています。 [Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) クラスでラップされている場合は、すべてのフィーチャー ジオメトリも簡単に更新することができます。

<a id="addALine"></a>

## <a name="add-lines-to-the-map"></a>マップに線を追加する

`LineString` および `MultiLineString` フィーチャーは、マップ上のパスとアウトラインを表すために使用します。

### <a name="add-a-line"></a>線を追加する

<iframe height='500' scrolling='no' title='マップに線を追加する' src='//codepen.io/azuremaps/embed/qomaKv/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/qomaKv/'>Add a line to a map</a>」Pen を表示します。
</iframe>

上記のコードの最初のコード ブロックでは、マップ オブジェクトが作成されます。 作成方法については、[マップの作成](./map-create.md)に関する記事を参照してください。

2 番目のコード ブロックでは、[DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) クラスを使用して、データ ソース オブジェクトが作成されます。 [LineString](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.linestring?view=azure-iot-typescript-latest) オブジェクトが作成され、データ ソースに追加されます。

[LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) によって、[DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) にラップされた線オブジェクトがレンダリングされます。 最後のコード ブロックでは、線レイヤーが作成され、マップに追加されます。 線レイヤーのプロパティについては、[LinestringLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest) に関するページを参照してください。 データ ソースと線レイヤーが作成され、[イベント ハンドラー](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)関数内でマップに追加されるため、マップが完全に読み込まれた後に、線が表示されます。

### <a name="add-symbols-along-a-line"></a>線に沿ってシンボルを追加する

このサンプルでは、マップ上の線に沿って矢印アイコンを追加する方法を示します。 シンボル レイヤーを使用する場合は、"placement" オプションを "line" に設定します。これにより、シンボルが線に沿ってレンダリングされ、アイコンが回転します (0 度 = 右)。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="線に沿って矢印を表示する" src="//codepen.io/azuremaps/embed/drBJwX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/drBJwX/'>Show arrow along line</a>」Pen を表示します。
</iframe>

### <a name="line-stroke-gradient"></a> ストロークのグラデーションを線に追加する

線に 1 つのストローク色を適用できるだけでなく、線を色のグラデーションで塗りつぶして 1 つの線分から次の線分への遷移を示すこともできます。 たとえば、線のグラデーションを使用して、時間と距離の変化やオブジェクトを接続した線のさまざまな温度を表すことができます。 線にこのフィーチャーを適用するためには、データ ソースで `lineMetrics` オプションが true に設定されている必要があります。それにより、色のグラデーションを線の `strokeColor` オプションに渡すことができます。 ストロークのグラデーション式は、計算済みのメトリックを式に公開する `['line-progress']` データ式を参照する必要があります。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="ストロークのグラデーションのある線" src="//codepen.io/azuremaps/embed/wZwWJZ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/wZwWJZ/'>Line with Stroke Gradient</a>」Pen を表示します。
</iframe>

### <a name="customize-a-line-layer"></a>線レイヤーをカスタマイズする

線レイヤーにはいくつかのスタイル オプションがあります。 次のツールでそれらを試してください。

<br/>

<iframe height='700' scrolling='no' title='線レイヤーのオプション' src='//codepen.io/azuremaps/embed/GwLrgb/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/GwLrgb/'>Line Layer Options</a>」Pen を表示します。
</iframe>

<a id="addAPolygon"></a>

## <a name="add-a-polygon-to-the-map"></a>マップに多角形を追加する

`Polygon` および `MultiPolygon` フィーチャーは、多くの場合、マップ上の領域を表すために使用されます。 

### <a name="use-a-polygon-layer"></a>多角形レイヤーを使用する 

多角形レイヤーは、多角形の領域をレンダリングします。 

<iframe height='500' scrolling='no' title='マップに多角形を追加する ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>Add a polygon to a map</a>」Pen を表示します。
</iframe>

上記のコードの最初のコード ブロックでは、マップ オブジェクトが作成されます。 作成方法については、[マップの作成](./map-create.md)に関する記事を参照してください。

2 番目のコード ブロックでは、[DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) クラスを使用して、データ ソース オブジェクトが作成されます。 [Polygon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest) は座標の配列から作成され、データ ソースに追加されます。 

[PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) によって、[DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) にラップされたデータがマップにレンダリングされます。 最後のコード ブロックでは、多角形レイヤーが作成され、マップに追加されます。 多角形レイヤーのプロパティについては、[PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest) に関する記事をご覧ください。 データ ソースと多角形レイヤーが作成され、[イベント ハンドラー](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)内でマップに追加されるため、マップが完全に読み込まれた後に、多角形が表示されます。

### <a name="use-a-polygon-and-line-layer-together"></a>多角形と線レイヤーを同時に使用する

線レイヤーを使用して、多角形のアウトラインをレンダリングすることができます。 

<iframe height='500' scrolling='no' title='多角形と多角形を追加する線レイヤー' src='//codepen.io/azuremaps/embed/aRyEPy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/aRyEPy/'>Polygon and line layer to add polygon</a>」Pen を表示します。
</iframe>

上記のコードの最初のコード ブロックでは、マップ オブジェクトが作成されます。 作成方法については、[マップの作成](./map-create.md)に関する記事を参照してください。

2 番目のコード ブロックでは、[DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) クラスを使用して、データ ソース オブジェクトが作成されます。 [Polygon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest) は座標の配列から作成され、データ ソースに追加されます。 

[PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) によって、[DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) にラップされたデータがマップにレンダリングされます。 多角形レイヤーのプロパティについては、[PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest) に関する記事をご覧ください。 [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) は、複数の線の配列です。 線レイヤーのプロパティについては、[LinestringLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest) に関するページを参照してください。 3 番目のコード ブロックでは、多角形と線レイヤーが作成されます。

最後のコード ブロックでは、マップに多角形と線レイヤーが追加されます。 データ ソースとレイヤーが作成され、[イベント ハンドラー](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)内でマップに追加されるため、マップが完全に読み込まれた後に、多角形が表示されます。

> [!TIP]
> 線レイヤーの既定では、データ ソース内の多角形と線の座標がレンダリングされます。 LineString フィーチャーのみがレンダリングされるようにレイヤーを制限するには、レイヤーの `filter` プロパティを `['==', ['geometry-type'], 'LineString']` に設定します。または、MultiLineString フィーチャーも含める場合は、`['any', ['==', ['geometry-type'], 'LineString'], ['==', ['geometry-type'], 'MultiLineString']]` に設定します。

### <a name="fill-a-polygon-with-a-pattern"></a>多角形をパターンで塗りつぶす

多角形を色で塗りつぶすだけでなく、イメージのパターンを使用することもできます。 イメージのパターンをマップ イメージのスプライト リソースに読み込んでから、このイメージを多角形レイヤーの `fillPattern` プロパティで参照します。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="多角形塗りつぶしパターン" src="//codepen.io/azuremaps/embed/JzQpYX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/JzQpYX/'>Polygon fill pattern</a>」Pen を表示します。
</iframe>

### <a name="customize-a-polygon-layer"></a>ポリゴン レイヤーをカスタマイズする

Polygon レイヤーにはごくわずかのスタイル オプションしかありません。 次のツールでそれらを試してください。

<br/>

<iframe height='700' scrolling='no' title='LXvxpg' src='//codepen.io/azuremaps/embed/LXvxpg/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/LXvxpg/'>LXvxpg</a>」Pen を表示します。
</iframe>

<a id="addACircle"></a>

## <a name="add-a-circle-to-the-map"></a>マップに円を追加する

Azure Maps は、[ここ](extend-geojson.md#circle)に示されている円の定義を提供する、GeoJSON スキーマの拡張バージョンを使用してします。 値 `"Circle"` の `subType` プロパティとメートル単位の半径を表す数値を含む `radius` プロパティを含む `Point` フィーチャーを作成することで、円をマップ上にレンダリングすることができます。 例: 

```javascript
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.126986, 47.639754]
    },
    "properties": {
        "subType": "Circle",
        "radius": 100
    }
}  
```

Azure Maps の Web SDK は、これらの `Pooint` フィーチャーを内部的に `Polygon` に変換し、次に示すように多角形レイヤーと線レイヤーを使用してマップ上にレンダリングすることができます。

<iframe height='500' scrolling='no' title='マップに円を追加する' src='//codepen.io/azuremaps/embed/PRmzJX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/PRmzJX/'>Add a circle to a map</a>」Pen を表示します。
</iframe>

上記のコードの最初のコード ブロックでは、マップ オブジェクトが作成されます。 作成方法については、[マップの作成](./map-create.md)に関する記事を参照してください。

2 番目のコード ブロックでは、[DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) クラスを使用して、データ ソース オブジェクトが作成されます。 円の [Feature](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) は [Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) であり、`subType` プロパティは `"Circle"`、`radius` プロパティ値はメートルで設定されています。 `subType` が `"Circle"` の Point フィーチャーがデータ ソースに追加されると、マップ内で円の多角形に変換されます。

[PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) によって、[DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) にラップされたデータがマップにレンダリングされます。 最後のコード ブロックでは、多角形レイヤーが作成され、マップに追加されます。 多角形レイヤーのプロパティについては、[PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest) に関する記事をご覧ください。 データ ソースと多角形レイヤーが作成され、[イベント ハンドラー](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)内でマップに追加されるため、マップが完全に読み込まれた後に、円が表示されます。

## <a name="make-a-geometry-easy-to-update"></a>ジオメトリを簡単に更新する

`Shape` クラスは [Geometry](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.geometry?view=azure-iot-typescript-latest) または [Feature](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) をラップしているため、更新や管理が簡単です。
`new Shape(data: Feature<data.Geometry, any>)` はシェイプ オブジェクトを作成し、指定したフィーチャーで初期化します。

<br/>

<iframe height='500' scrolling='no' title='シェイプのプロパティを更新する' src='//codepen.io/azuremaps/embed/ZqMeQY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/ZqMeQY/'>Update shape properties</a>」Pen を表示します。
</iframe>

上記のコードの最初のブロックでは、マップ オブジェクトが作成されます。 作成方法については、[マップの作成](./map-create.md)に関する記事を参照してください。

ポイントは、[Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) クラスの [Feature](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) です。 2 番目のコード ブロックでは、HTML スライダー要素の半径の値が初期化されて、ポイント オブジェクトが作成され、[Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) クラス オブジェクトにラップされます。

3 番目のコード ブロックでは、HTML 範囲スライダー要素から値が取得され、shape クラスの [addProperty](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) メソッドを使用して、半径値が変更されます。

4 番目のコード ブロックでは、[DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) クラスを使用して、データ ソース オブジェクトが作成されます。 ポイントがデータ ソースに追加されます。

[PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) によって、[DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) にラップされたデータがマップにレンダリングされます。 3 番目のコード ブロックでは、多角形レイヤーが作成されます。 多角形レイヤーのプロパティについては、[PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest) に関する記事をご覧ください。 データ ソース、クリック イベント ハンドラー、多角形レイヤーが作成され、マップの [イベント ハンドラー](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)内に追加されるため、マップが完全に読み込まれた後に、ポイントが表示されます。

## <a name="next-steps"></a>次の手順

マップに追加するコード例の詳細については、次の記事を参照してください。

> [!div class="nextstepaction"]
> [データドリブンのスタイルの式を使用する](data-driven-style-expressions-web-sdk.md)
