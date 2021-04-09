---
title: マップに多角形レイヤーを追加する | Microsoft Azure Maps
description: マップに多角形または円を追加する方法について説明します。 Azure Maps Web SDK を使用して幾何学図形をカスタマイズし、それらの更新や維持を容易にする方法について説明します。
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 96553e75d4b982cfe67d03961d4356a3844f253c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92890999"
---
# <a name="add-a-polygon-layer-to-the-map"></a>マップに多角形レイヤーを追加する

この記事では、多角形レイヤーを使用してマップ上に `Polygon` および `MultiPolygon` フィーチャー ジオメトリの領域をレンダリングする方法を示します。 Azure Maps の Web SDK では、[拡張 GeoJSON スキーマ](extend-geojson.md#circle)で定義されている円のジオメトリの作成もサポートしています。 これらの円は、マップ上にレンダリングされるときに多角形に変換されます。 [atlas.Shape](/javascript/api/azure-maps-control/atlas.shape) クラスでラップされている場合は、すべてのフィーチャー ジオメトリを簡単に更新できます。

## <a name="use-a-polygon-layer"></a>多角形レイヤーを使用する 

多角形レイヤーがデータ ソースに接続されていると、マップに読み込まれるときに、`Polygon` および `MultiPolygon` フィーチャーを含む領域がレンダリングされます。 多角形を作成し、データ ソースに追加し、[PolygonLayer](/javascript/api/azure-maps-control/atlas.layer.polygonlayer) クラスを使用して多角形レイヤーでレンダリングします。

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a rectangular polygon.
dataSource.add(new atlas.data.Feature(
    new atlas.data.Polygon([[
        [-73.98235, 40.76799],
        [-73.95785, 40.80044],
        [-73.94928, 40.7968],
        [-73.97317, 40.76437],
        [-73.98235, 40.76799]
    ]])
));

//Create and add a polygon layer to render the polygon to the map, below the label layer.
map.layers.add(new atlas.layer.PolygonLayer(dataSource, null,{
    fillColor: 'red',
    fillOpacity: 0.7
}), 'labels');
```

上記のコードの完全な実行サンプルを以下に示します。

<br/>

<iframe height='500' scrolling='no' title='マップに多角形を追加する ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>Add a polygon to a map</a>」Pen を表示します。
</iframe>

## <a name="use-a-polygon-and-line-layer-together"></a>多角形と線レイヤーを同時に使用する

線レイヤーを使用して、多角形のアウトラインをレンダリングします。 次のコード サンプルでは、前の例のように多角形がレンダリングされますが、今度は線レイヤーが追加されます。 この線レイヤーは、データ ソースに接続される 2 番目のレイヤーです。  

<br/>

<iframe height='500' scrolling='no' title='多角形と多角形を追加する線レイヤー' src='//codepen.io/azuremaps/embed/aRyEPy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/aRyEPy/'>Polygon and line layer to add polygon</a>」Pen を表示します。
</iframe>

## <a name="fill-a-polygon-with-a-pattern"></a>多角形をパターンで塗りつぶす

多角形を色で塗りつぶすだけでなく、イメージのパターンを使用して多角形を塗りつぶすこともできます。 イメージのパターンをマップ イメージのスプライト リソースに読み込んでから、このイメージを多角形レイヤーの `fillPattern` プロパティで参照します。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="多角形塗りつぶしパターン" src="//codepen.io/azuremaps/embed/JzQpYX/?height=500&theme-id=0&default-tab=js,result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/JzQpYX/'>Polygon fill pattern</a>」Pen を表示します。
</iframe>


> [!TIP]
> Azure Maps Web SDK には、塗りつぶしパターンとして使用できるカスタマイズ可能なイメージ テンプレートがいくつか用意されています。 詳細については、[画像テンプレートの使用方法](how-to-use-image-templates-web-sdk.md)のドキュメントを参照してください。

## <a name="customize-a-polygon-layer"></a>ポリゴン レイヤーをカスタマイズする

Polygon レイヤーにはごくわずかのスタイル オプションしかありません。 次のツールでそれらをお試しください。

<br/>

<iframe height='700' scrolling='no' title='LXvxpg' src='//codepen.io/azuremaps/embed/LXvxpg/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/LXvxpg/'>LXvxpg</a>」Pen を表示します。
</iframe>

<a id="addACircle"></a>

## <a name="add-a-circle-to-the-map"></a>マップに円を追加する

Azure Maps は、[ここ](extend-geojson.md#circle)に示されている円の定義を提供する、GeoJSON スキーマの拡張バージョンを使用しています。 `Point` フィーチャーを作成すると、マップ上に円がレンダリングされます。 この `Point` には、値が `"Circle"` の `subType` プロパティと、メートル単位で半径を表す数値が指定された `radius` プロパティがあります。 

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

Azure Maps Web SDK では、このような `Point` フィーチャーが `Polygon` フィーチャーに変換されます。 次に、これらのフィーチャーは、次のコード サンプルで示すように、多角形レイヤーと線レイヤーを使用してマップ上にレンダリングされます。

<br/>

<iframe height='500' scrolling='no' title='マップに円を追加する' src='//codepen.io/azuremaps/embed/PRmzJX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/PRmzJX/'>Add a circle to a map</a>」Pen を表示します。
</iframe>

## <a name="make-a-geometry-easy-to-update"></a>ジオメトリを簡単に更新する

`Shape` クラスは [Geometry](/javascript/api/azure-maps-control/atlas.data.geometry) または [Feature](/javascript/api/azure-maps-control/atlas.data.feature) をラップしているため、これらのフィーチャーの更新や管理が簡単です。 図形変数をインスタンス化するには、図形コンストラクターにジオメトリまたはプロパティのセットを渡します。

```javascript
//Creating a shape by passing in a geometry and a object containing properties.
var shape1 = new atlas.Shape(new atlas.data.Point[0,0], { myProperty: 1 });

//Creating a shape using a feature.
var shape2 = new atlas.Shape(new atlas.data.Feature(new atlas.data.Point[0,0], { myProperty: 1 });
```

次のコード サンプルでは、円の GeoJSON オブジェクトを図形クラスでラップする方法が示されています。 図形で半径の値が変化すると、マップ上で自動的に円がレンダリングされます。

<br/>

<iframe height='500' scrolling='no' title='シェイプのプロパティを更新する' src='//codepen.io/azuremaps/embed/ZqMeQY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/ZqMeQY/'>Update shape properties</a>」Pen を表示します。
</iframe>

## <a name="next-steps"></a>次のステップ

この記事で使われているクラスとメソッドの詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [Polygon](/javascript/api/azure-maps-control/atlas.data.polygon)

> [!div class="nextstepaction"]
> [PolygonLayer](/javascript/api/azure-maps-control/atlas.layer.polygonlayer)

> [!div class="nextstepaction"]
> [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions)

マップに追加するコード例の詳細については、次の記事を参照してください。

> [!div class="nextstepaction"]
> [データ ソースを作成する](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [ポップアップを追加する](map-add-popup.md)

> [!div class="nextstepaction"]
> [データドリブンのスタイルの式を使用する](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [画像テンプレートの使用方法](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [線レイヤーを追加する](map-add-line-layer.md)

その他のリソース:

> [!div class="nextstepaction"]
> [Azure Maps GeoJSON 仕様拡張機能](extend-geojson.md#circle)