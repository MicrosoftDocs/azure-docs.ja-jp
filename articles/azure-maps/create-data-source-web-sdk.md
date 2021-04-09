---
title: Microsoft Azure Maps でマップのデータ ソースを作成する
description: マップのデータ ソースを作成する方法について説明します。 Azure Maps Web SDK で使用されるデータ ソースについて説明します。GeoJSON ソースとベクター タイル。
author: rbrundritt
ms.author: richbrun
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 9964c99ddfb59811fc67df634b41cede5847ede0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97678844"
---
# <a name="create-a-data-source"></a>データ ソースを作成する

Azure Maps Web SDK では、データがデータ ソースに格納されます。 データ ソースを使用すると、クエリとレンダリングのデータ操作が最適化されます。 現在、次の 2 種類のデータ ソースがあります。

* **GeoJSON ソース**: 場所の生データを GeoJSON 形式でローカルに管理します。 小規模から中規模のデータ セット (数十万点を超える図形) に適しています。
* **ベクター タイル ソース**: マップのタイル システムに基づいて、現在のマップ ビューのベクター タイルとして書式設定されたデータを読み込みます。 大規模から大規模なデータセット (数百万または数十億点の図形) に最適です。

## <a name="geojson-data-source"></a>GeoJSON データ ソース

GeoJSON ベースのデータ ソースは、`DataSource` クラスを使用してローカルでデータを読み込んで格納します。 GeoJSON データは、[atlas.data](/javascript/api/azure-maps-control/atlas.data) 名前空間に、手動で作成するかヘルパー クラスを使用して作成できます。 `DataSource` クラスには、ローカルまたはリモートの GeoJSON ファイルをインポートするための関数が用意されています。 リモートの GeoJSON ファイルは、CORS が有効なエンドポイント上でホストされている必要があります。 `DataSource` クラスには、ポイント データをクラスター化するための機能があります。 データは、`DataSource` クラスを使用して、簡単に追加、削除、および更新できます。 次のコードは、Azure Maps で GeoJSON データを作成する方法を示しています。

```javascript
//Create raw GeoJSON object.
var rawGeoJson = {
     "type": "Feature",
     "geometry": {
         "type": "Point",
         "coordinates": [-100, 45]
     },
     "properties": {
         "custom-property": "value"
     }
};

//Create GeoJSON using helper classes (less error prone and less typing).
var geoJsonClass = new atlas.data.Feature(new atlas.data.Point([-100, 45]), {
    "custom-property": "value"
}); 
```

作成されたデータ ソースは、`map.sources` プロパティ ([SourceManager](/javascript/api/azure-maps-control/atlas.sourcemanager)) を使用してマップに追加できます。 次のコードは、`DataSource` を作成してマップに追加する方法を示しています。

```javascript
//Create a data source and add it to the map.
var source = new atlas.source.DataSource();
map.sources.add(source);
```

次のコードは、GeoJSON データを `DataSource` に追加するさまざまな方法を示しています。

```javascript
//GeoJsonData in the following code can be a single or array of GeoJSON features or geometries, a GeoJSON feature colleciton, or a single or array of atlas.Shape objects.

//Add geoJSON object to data source. 
source.add(geoJsonData);

//Load geoJSON data from URL. URL should be on a CORs enabled endpoint.
source.importDataFromUrl(geoJsonUrl);

//Overwrite all data in data source.
source.setShapes(geoJsonData);
```

> [!TIP]
> `DataSource`内のすべてのデータを上書きするとします。 `clear` 関数を呼び出してから `add` 関数を呼び出すと、マップで再レンダリングが 2 回行われ、それによって少しの遅延が発生する場合があります。 代わりに、データ ソース内のすべてのデータを削除して置換する `setShapes` 関数を使用します。これにより、マップの再レンダリングが 1 回だけトリガーされます。

## <a name="vector-tile-source"></a>ベクター タイル ソース

ベクター タイル ソースには、ベクター タイル レイヤーにアクセスする方法が記述されています。 ベクター タイル ソースをインスタンス化するには、[VectorTileSource](/javascript/api/azure-maps-control/atlas.source.vectortilesource) クラスを使用します。 ベクター タイル レイヤーはタイル レイヤーに似ていますが、同じではありません。 タイル レイヤーはラスター イメージです。 ベクター タイル レイヤーは、**PBF** 形式の圧縮ファイルです。 この圧縮ファイルには、ベクター マップ データと 1 つ以上のレイヤーが含まれています。 このファイルは、各レイヤーのスタイルに基づいて、クライアントでレンダリングおよびスタイル設定できます。 ベクター タイルのデータには、ポイント、線、および多角形の形式で地理的特徴が含まれています。 ベクター タイル レイヤーの使用には、ラスター タイル レイヤーよりも優れている点がいくつかあります。

* ベクター タイルのファイル サイズは、通常、同等のラスター タイルよりはるかに小さくなります。 そのため、使用される帯域幅が少なくなります。 つまり、待機時間の短縮、より高速なマップ、ユーザー エクスペリエンスの向上を意味します。
* ベクター タイルはクライアント上でレンダリングされるため、表示されているデバイスの解像度に適応できます。 結果として、レンダリングされるマップはより明確に定義され、鮮明なラベルが表示されます。
* ベクター マップ内のデータのスタイルを変更しても、クライアントに新しいスタイルを適用できるので、データを再度ダウンロードする必要はありません。 これに対し、ラスター タイル レイヤーのスタイルを変更するには、通常、タイルをサーバーから読み込み、新しいスタイルを適用する必要があります。
* データはベクター形式で配信されるので、データを準備するために必要なサーバー側の処理が少なくなります。 つまり、新しいデータをより速く使用できるようになります。

Azure Maps は、オープン スタンダードである [Mapbox Vector Tile 仕様](https://github.com/mapbox/vector-tile-spec)に準拠しています。 Azure Maps では、プラットフォームの一部として次のベクター タイル サービスが提供されます。

- Road tiles [ドキュメント](/rest/api/maps/renderv2/getmaptilepreview) | [データ形式の詳細](https://developer.tomtom.com/maps-api/maps-api-documentation-vector/tile)
- Traffic incidents [ドキュメント](/rest/api/maps/traffic/gettrafficincidenttile) | [データ形式の詳細](https://developer.tomtom.com/traffic-api/traffic-api-documentation-traffic-incidents/vector-incident-tiles)
- Traffic flow [ドキュメント](/rest/api/maps/traffic/gettrafficflowtile) | [データ形式の詳細](https://developer.tomtom.com/traffic-api/traffic-api-documentation-traffic-flow/vector-flow-tiles)
- Azure Maps Creator (プレビュー) は、[Get Tile Render V2](/rest/api/maps/renderv2/getmaptilepreview) を使用して、カスタム ベクター タイルを作成およびアクセスすることもできます

> [!TIP]
> Web SDK を使用して Azure Maps の Render Service からベクターまたはラスター イメージのタイルを使用する場合、`atlas.microsoft.com` をプレースホルダー `{azMapsDomain}` に置き換えることができます。 このプレースホルダーは、マップによって使用されるのと同じドメインに置き換えられ、同じ認証の詳細も自動的に追加されます。 これにより、Azure Active Directory 認証を使用するときに、Render Service での認証が大幅に簡単になります。

マップにベクター タイル ソースからのデータを表示するには、ソースをいずれかのデータ レンダリング レイヤーに接続します。 ベクター ソースを使用するすべてのレイヤーでオプションの `sourceLayer` 値を指定する必要があります。 次のコードでは、Azure Maps のトラフィック フロー ベクター タイル サービスをベクター タイル ソースとして読み込み、その後、線レイヤーを使用してマップに表示します。 このベクター タイル ソースには、ソース レイヤーに "Traffic flow" という 1 つのデータ セットがあります。 このデータ セット内の行データには、`traffic_level` というプロパティがあります。これは、色を選択して線のサイズを拡大縮小するために、このコードで使用されます。

```javascript
//Create a vector tile source and add it to the map.
var source = new atlas.source.VectorTileSource(null, {
    tiles: ['https://{azMapsDomain}/traffic/flow/tile/pbf?api-version=1.0&style=relative&zoom={z}&x={x}&y={y}'],
    maxZoom: 22
});
map.sources.add(source);

//Create a layer for traffic flow lines.
var flowLayer = new atlas.layer.LineLayer(source, null, {
    //The name of the data layer within the data source to pass into this rendering layer.
    sourceLayer: 'Traffic flow',

    //Color the roads based on the traffic_level property. 
    strokeColor: [
        'interpolate',
        ['linear'],
        ['get', 'traffic_level'],
        0, 'red',
        0.33, 'orange',
        0.66, 'green'
    ],

    //Scale the width of roads based on the traffic_level property. 
    strokeWidth: [
        'interpolate',
        ['linear'],
        ['get', 'traffic_level'],
        0, 6,
        1, 1
    ]
});

//Add the traffic flow layer below the labels to make the map clearer.
map.layers.add(flowLayer, 'labels');
```

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Vector tile line layer" src="https://codepen.io/azuremaps/embed/wvMXJYJ?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による Pen の "<a href='https://codepen.io/azuremaps/pen/wvMXJYJ'>Vector tile line layer</a>" を表示します。
</iframe>

<br/>

## <a name="connecting-a-data-source-to-a-layer"></a>レイヤーへのデータ ソースの接続

データは、レンダリング レイヤーを使用してマップ上にレンダリングされます。 1 つのデータ ソースは、1 つ以上のレンダリング レイヤーから参照できます。 次のレンダリング レイヤーでは、データ ソースが必要です。

* [バブル レイヤー](map-add-bubble-layer.md) - ポイント データをマップ上で拡大縮小された円としてレンダリングします。
* [シンボル レイヤー](map-add-pin.md) - ポイント データをアイコンやテキストとしてレンダリングします。
* [ヒート マップ レイヤー](map-add-heat-map-layer.md) - ポイント データを密度ヒート マップとしてレンダリングします。
* [線レイヤー](map-add-shape.md) - 線をレンダリングしたり、多角形のアウトラインをレンダリングしたりします。 
* [多角形レイヤー](map-add-shape.md) - 純色または画像パターンで多角形の領域を塗りつぶします。

次のコードは、データ ソースを作成し、マップに追加してバブル レイヤーに接続する方法を示しています。 次に、GeoJSON ポイント データをリモートの場所からデータ ソースにインポートします。 

```javascript
//Create a data source and add it to the map.
var source = new atlas.source.DataSource();
map.sources.add(source);

//Create a layer that defines how to render points in the data source and add it to the map.
map.layers.add(new atlas.layer.BubbleLayer(source));

//Load the earthquake data.
source.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/significant_month.geojson');
```

これらのデータ ソースに接続せず、レンダリングするためのデータを直接読み込む追加のレンダリング レイヤーがあります。 

* [画像レイヤー](map-add-image-layer.md) - マップの上に 1 つの画像をオーバーレイし、指定した座標のセットにその角をバインドします。
* [タイル レイヤー](map-add-tile-layer.md) - マップの上にラスター タイル レイヤーを重ねて表示します。

## <a name="one-data-source-with-multiple-layers"></a>複数のレイヤーがある 1 つのデータ ソース

複数のレイヤーを 1 つのデータ ソースに接続できます。 このオプションが役に立つさまざまなシナリオがあります。 たとえば、ユーザーが多角形を描画するシナリオを考えてみます。 ユーザーがポイントをマップに追加するときに、多角形領域をレンダリングして塗りつぶす必要があります。 多角形の輪郭を描画するスタイル指定された線を追加すると、ユーザーが描画するときに多角形のエッジが見やすくなります。 多角形内の個々の位置を簡単に編集するために、各位置の上にピンやマーカーなどのハンドルを追加することもできます。

![1 つのデータ ソースのデータをレンダリングする複数のレイヤーが表示されるマップ](media/create-data-source-web-sdk/multiple-layers-one-datasource.png)

ほとんどのマッピング プラットフォームで、多角形オブジェクト、線オブジェクト、および多角形内の各位置に対するピンが必要になります。 多角形を変更するときは、線とピンを手動で更新する必要がありますが、これはすぐに複雑になる可能性があります。

Azure Maps で必要なものは、次のコードで示すように、データ ソース内の 1 つの多角形だけです。

```javascript
//Create a data source and add it to the map.
var source = new atlas.source.DataSource();
map.sources.add(source);

//Create a polygon and add it to the data source.
source.add(new atlas.data.Polygon([[[/* Coordinates for polygon */]]]));

//Create a polygon layer to render the filled in area of the polygon.
var polygonLayer = new atlas.layer.PolygonLayer(source, 'myPolygonLayer', {
     fillColor: 'rgba(255,165,0,0.2)'
});

//Create a line layer for greater control of rendering the outline of the polygon.
var lineLayer = new atlas.layer.LineLayer(source, 'myLineLayer', {
     color: 'orange',
     width: 2
});

//Create a bubble layer to render the vertices of the polygon as scaled circles.
var bubbleLayer = new atlas.layer.BubbleLayer(source, 'myBubbleLayer', {
     color: 'orange',
     radius: 5,
     strokeColor: 'white',
     strokeWidth: 2
});

//Add all layers to the map.
map.layers.add([polygonLayer, lineLayer, bubbleLayer]);
```

> [!TIP]
> `map.layers.add` 関数を使用してマップにレイヤーを追加するときに、既存のレイヤーの ID またはインスタンスを 2 番目のパラメーターとして渡すことができます。 これにより、マップは、追加される新しいレイヤーを既存のレイヤーの下に挿入するように指示されます。 このメソッドは、レイヤー ID を渡すだけでなく、次の値もサポートします。
>
> * `"labels"` - マップ ラベル レイヤーの下に新しいレイヤーを挿入します。
> * `"transit"` - マップの道路および輸送のレイヤーの下に新しいレイヤーを挿入します。

## <a name="next-steps"></a>次のステップ

この記事で使われているクラスとメソッドの詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [DataSource](/javascript/api/azure-maps-control/atlas.source.datasource)

> [!div class="nextstepaction"]
> [DataSourceOptions](/javascript/api/azure-maps-control/atlas.datasourceoptions)

> [!div class="nextstepaction"]
> [VectorTileSource](/javascript/api/azure-maps-control/atlas.source.vectortilesource)

> [!div class="nextstepaction"]
> [VectorTileSourceOptions](/javascript/api/azure-maps-control/atlas.vectortilesourceoptions)

マップに追加できる他のコード サンプルについては、次の記事をご覧ください。

> [!div class="nextstepaction"]
> [ポップアップを追加する](map-add-popup.md)

> [!div class="nextstepaction"]
> [データドリブンのスタイルの式を使用する](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [シンボル レイヤーを追加する](map-add-pin.md)

> [!div class="nextstepaction"]
> [バブル レイヤーを追加する](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [線レイヤーを追加する](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [多角形レイヤーを追加する](map-add-shape.md)

> [!div class="nextstepaction"]
> [ヒート マップを追加する](map-add-heat-map-layer.md)

> [!div class="nextstepaction"]
> [コード サンプル](/samples/browse/?products=azure-maps)