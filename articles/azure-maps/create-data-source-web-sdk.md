---
title: Azure Maps でデータ ソースを作成する | Microsoft Docs
description: データ ソースを作成し、Azure Maps Web SDK で使用する方法。
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 36c06182d0807ce3d255477a865023ae7b74e2cb
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2019
ms.locfileid: "69874927"
---
# <a name="create-a-data-source"></a>データ ソースを作成する

Azure Maps Web SDK では、クエリとレンダリングに合わせてデータを最適化するデータ ソースにデータを格納します。 現在、次の 2 種類のデータ ソースがあります。

**GeoJSON データ ソース**

GeoJSON ベースのデータ ソースは、`DataSource` クラスを使用してローカルでデータを読み込んで格納できます。 GeoJSON データは、[atlas.data](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data) 名前空間に、手動で作成するかヘルパー クラスを使用して作成できます。 `DataSource` クラスには、ローカルまたはリモートの GeoJSON ファイルをインポートするための関数が用意されています。 リモートの GeoJSON ファイルは、CORS が有効なエンドポイント上でホストされている必要があります。 `DataSource` クラスには、ポイント データをクラスター化するための機能があります。 データは、`DataSource` クラスを使用して、簡単に追加、削除、および更新できます。


> [!TIP]
> `DataSource` 内のすべてのデータを上書きするときに、`clear` 関数を呼び出してから `add` 関数を呼び出すと、マップで再レンダリングが 2 回試行され、それによって少しの遅延が発生する場合があります。 代わりに、データ ソース内のすべてのデータを削除して置換する `setShapes` 関数を使用します。これにより、マップの再レンダリングが 1 回だけトリガーされます。

**ベクター タイル ソース**

ベクター タイル ソースには、ベクター タイル レイヤーにアクセスする方法を記述します。これは、[VectorTileSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.vectortilesource) クラスを使用して作成できます。 Azure Maps は、オープン スタンダードである [Mapbox Vector Tile 仕様](https://github.com/mapbox/vector-tile-spec)に沿っています。 ベクター タイル レイヤーはタイル レイヤーに似ていますが、各タイルはラスター画像ではなく、圧縮ファイル (PBF 形式) です。これには、ベクター マップ データと、各レイヤーのスタイルに基づいてクライアント上でレンダリングおよびスタイル設定できる 1 つ以上のレイヤーが含まれます。 ベクター タイルのデータには、ポイント、線、および多角形の形式で地理的特徴が含まれています。 ベクター タイル レイヤーには、ラスター タイル レイヤーよりも優れている点がいくつかあります。

 - ベクター タイルのファイル サイズは、通常、同等のラスター タイルよりはるかに小さくなります。 そのため、使用される帯域幅が少なくなります。つまり、待機時間が短くなり、マップが高速になります。 これにより、ユーザー エクスペリエンスが向上します。
 - ベクター タイルはクライアント上でレンダリングされるため、表示されているデバイスの解像度に適応することができます。 これにより、レンダリングされるマップはより明確に定義され、鮮明なラベルで表示されるようになります。 
 - ベクター マップ内のデータのスタイルを変更しても、クライアントに新しいスタイルを適用できるので、データを再度ダウンロードする必要はありません。 これに対し、ラスター タイル レイヤーのスタイルを変更するには、通常、新しいスタイルが適用されたタイルをサーバーから読み込む必要があります。
 - データはベクター形式で配信されるので、データを準備するために必要なサーバー側の処理が少なくなります。つまり、新しいデータをより速く使用できるようになります。

ベクター ソースを使用するすべてのレイヤーで `sourceLayer` 値を指定する必要があります。 

作成されたデータ ソースは、`map.sources` プロパティ ([SourceManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.sourcemanager)) を使用してマップに追加できます。 次のコードは、`DataSource` を作成してマップに追加する方法を示しています。

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);
```

## <a name="connecting-a-data-source-to-a-layer"></a>レイヤーへのデータ ソースの接続

データは、レンダリング レイヤーを使用してマップ上にレンダリングされます。 1 つのデータ ソースは、1 つ以上のレンダリング レイヤーから参照できます。 次のレンダリング レイヤーでは、データ ソースを強化する必要があります。

- [バブル レイヤー](map-add-bubble-layer.md) - ポイント データをマップ上で拡大縮小された円としてレンダリングします。
- [シンボル レイヤー](map-add-pin.md) - ポイント データをアイコンやテキストとしてレンダリングします。
- [ヒート マップ レイヤー](map-add-heat-map-layer.md) - ポイント データを密度ヒート マップとしてレンダリングします。
- [線レイヤー](map-add-shape.md) - 多角形のアウトラインをレンダリングするために使用できます。 
- [多角形レイヤー](map-add-shape.md) - 純色または画像パターンで多角形の領域を塗りつぶします。

次のコードは、データ ソースを作成し、マップに追加してバブル レイヤーに接続してから、GeoJSON ポイント データをリモートの場所からそこにインポートする方法を示しています。 

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a layer that defines how to render points in the data source and add it to the map.
map.layers.add(new atlas.layer.BubbleLayer(datasource));

//Load the earthquake data.
datasource.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/significant_month.geojson');
```

これらのデータ ソースに接続せず、直接レンダリングするデータを読み込む追加のレンダリング レイヤーがあります。 

- [画像レイヤー](map-add-image-layer.md) - マップの上に 1 つの画像をオーバーレイし、指定した座標のセットにその角をバインドします。
- [タイル レイヤー](map-add-tile-layer.md) - マップの上にラスター タイル レイヤーを重ねて表示します。

## <a name="one-data-source-with-multiple-layers"></a>複数のレイヤーがある 1 つのデータ ソース

複数のレイヤーを 1 つのデータ ソースに接続できます。 これは奇妙に思えるかもしれませんが、さまざまなシナリオでこの機能が役に立ちます。 たとえば、多角形描画エクスペリエンスを作成するシナリオについて考えます。 ユーザーが多角形を描画できるようにするには、ユーザーがマップに点を付かしているときに、塗りつぶし多角形リージョンをレンダリングする必要があります。 多角形の輪郭を描画するスタイル指定された線を追加すると、描画されている多角形のエッジが見やすくなります。 最後に、多角形内の各位置に何らかの種類のハンドル (ピンやマーカーなど) を追加すると、個別の各位置を編集しやすくなります。 このシナリオを示す画像を次に示します。

![1 つのデータ ソースのデータをレンダリングする複数のレイヤーが表示されるマップ](media/create-data-source-web-sdk/multiple-layers-one-datasource.png)

ほとんどのマッピング プラットフォームでこのシナリオを実現するには、多角形オブジェクト、線オブジェクト、および多角形内の各位置に対するピンを作成する必要があります。 多角形を変更するときは、線とピンを手動で更新する必要がありますが、これはすぐに複雑になる可能性があります。

Azure Maps で必要なものは、次のコードで示すように、データ ソース内の 1 つの多角形だけです。

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a polygon and add it to the data source.
dataSource.add(new atlas.data.Polygon([[[/* Coordinates for polygon */]]]));

//Create a polygon layer to render the filled in area of the polygon.
var polygonLayer = new atlas.layer.PolygonLayer(dataSource, 'myPolygonLayer', {
     fillColor: 'rgba(255,165,0,0.2)'
});

//Create a line layer for greater control of rendering the outline of the polygon.
var lineLayer = new atlas.layer.LineLayer(dataSource, 'myLineLayer', {
     color: 'orange',
     width: 2
});

//Create a bubble layer to render the vertices of the polygon as scaled circles.
var bubbleLayer = new atlas.layer.BubbleLayer(dataSource, 'myBubbleLayer', {
     color: 'orange',
     radius: 5,
     outlineColor: 'white',
     outlineWidth: 2
});

//Add all layers to the map.
map.layers.add([polygonLayer, lineLayer, bubbleLayer]);
```

## <a name="next-steps"></a>次の手順

この記事で使われているクラスとメソッドの詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [DataSourceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.datasourceoptions?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [VectorTileSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.vectortilesource?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [VectorTileSourceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.vectortilesourceoptions?view=azure-maps-typescript-latest)

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
> [コード サンプル](https://docs.microsoft.com/samples/browse/?products=azure-maps)