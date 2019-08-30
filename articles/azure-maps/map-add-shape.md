---
title: Azure Maps に多角形レイヤーを追加する | Microsoft Docs
description: Azure Maps Web SDK に多角形レイヤーを追加する方法。
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: ca6c0f5e6fde5a31655ed17f4a016bf44216643f
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976135"
---
# <a name="add-a-polygon-layer-to-the-map"></a>マップに多角形レイヤーを追加する

この記事では、多角形レイヤーを使用してマップ上に `Polygon` および `MultiPolygon` フィーチャー ジオメトリの領域をレンダリングする方法を示します。 Azure Maps の Web SDK では、[拡張 GeoJSON スキーマ](extend-geojson.md#circle)で定義されている円のジオメトリの作成もサポートしています。 これらの円は、マップ上にレンダリングされるときに多角形に変換されます。 [atlas.Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) クラスでラップされている場合は、すべてのフィーチャー ジオメトリも簡単に更新することができます。

## <a name="use-a-polygon-layer"></a>多角形レイヤーを使用する 

多角形レイヤーがデータ ソースに接続されていると、マップに読み込まれるときに、`Polygon` および `MultiPolygon` フィーチャーの領域がレンダリングされます。 次のコードでは、多角形を作成し、データ ソースに追加し、[PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) クラスを使用して多角形レイヤーでレンダリングする方法が示されています。

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

//Create and add a polygon layer to render the polygon to the map.
map.layers.add(new atlas.layer.PolygonLayer(dataSource, null,{
    fillColor: 'red',
    opacaty: 0.5
}));
```

上記の機能の完全な実行コード サンプルを以下に示します。

<br/>

<iframe height='500' scrolling='no' title='マップに多角形を追加する ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>Add a polygon to a map</a>」Pen を表示します。
</iframe>

## <a name="use-a-polygon-and-line-layer-together"></a>多角形と線レイヤーを同時に使用する

線レイヤーを使用して、多角形のアウトラインをレンダリングすることができます。 次のコード サンプルでは、前の例のようにポリゴンがレンダリングされますが、今度はデータ ソースに接続された 2 番目のレイヤーとして線レイヤーが追加されます。  

<iframe height='500' scrolling='no' title='多角形と多角形を追加する線レイヤー' src='//codepen.io/azuremaps/embed/aRyEPy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/aRyEPy/'>Polygon and line layer to add polygon</a>」Pen を表示します。
</iframe>

## <a name="fill-a-polygon-with-a-pattern"></a>多角形をパターンで塗りつぶす

多角形を色で塗りつぶすだけでなく、イメージのパターンを使用することもできます。 イメージのパターンをマップ イメージのスプライト リソースに読み込んでから、このイメージを多角形レイヤーの `fillPattern` プロパティで参照します。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="多角形塗りつぶしパターン" src="//codepen.io/azuremaps/embed/JzQpYX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/JzQpYX/'>Polygon fill pattern</a>」Pen を表示します。
</iframe>


> [!TIP]
> Azure Maps Web SDK には、塗りつぶしパターンとして使用できるカスタマイズ可能なイメージ テンプレートがいくつか用意されています。 詳細については、「[イメージ テンプレートの使用方法](how-to-use-image-templates-web-sdk.md)」のドキュメントを参照してください。

## <a name="customize-a-polygon-layer"></a>ポリゴン レイヤーをカスタマイズする

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

Azure Maps Web SDK では、これらの `Point` フィーチャーが内部的に `Polygon` に変換され、次のコード サンプルで示すように多角形レイヤーと線レイヤーを使用してマップ上にレンダリングすることができます。

<br/>

<iframe height='500' scrolling='no' title='マップに円を追加する' src='//codepen.io/azuremaps/embed/PRmzJX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/PRmzJX/'>Add a circle to a map</a>」Pen を表示します。
</iframe>

## <a name="make-a-geometry-easy-to-update"></a>ジオメトリを簡単に更新する

`Shape` クラスは [Geometry](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.geometry?view=azure-iot-typescript-latest) または [Feature](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) をラップしているため、更新や管理が簡単です。 ジオメトリと一連のプロパティを渡すか、または次のコードで示すようにフィーチャーを渡すことによって、図形を作成することができます。

```javascript
//Creating a shape by passing in a geometry and a object containing properties.
var shape1 = new atlas.Shape(new atlas.data.Point[0,0], { myProperty: 1 });

//Creating a shape using a feature.
var shape2 = new atlas.Shape(new atlas.data.Feature(new atlas.data.Point[0,0], { myProperty: 1 });
```

次のコード サンプルでは、円の GeoJSON オブジェクトを図形クラスでラップし、スライダーを使用してその半径プロパティを簡単に更新する方法が示されています。 図形で半径の値が変化すると、円のレンダリングがマップ上で自動的に更新されます。

<br/>

<iframe height='500' scrolling='no' title='シェイプのプロパティを更新する' src='//codepen.io/azuremaps/embed/ZqMeQY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/ZqMeQY/'>Update shape properties</a>」Pen を表示します。
</iframe>

## <a name="next-steps"></a>次の手順

この記事で使われているクラスとメソッドの詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [Polygon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)

マップに追加するコード例の詳細については、次の記事を参照してください。

> [!div class="nextstepaction"]
> [データ ソースを作成する](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [ポップアップを追加する](map-add-popup.md)

> [!div class="nextstepaction"]
> [データドリブンのスタイルの式を使用する](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [イメージ テンプレートの使用方法](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [線レイヤーを追加する](map-add-line-layer.md)

その他のリソース:

> [!div class="nextstepaction"]
> [Azure Maps GeoJSON 仕様拡張機能](extend-geojson.md#circle)