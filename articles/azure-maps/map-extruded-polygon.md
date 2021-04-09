---
title: マップに多角形浮き出しレイヤーを追加する | Microsoft Azure Maps
description: Microsoft Azure Maps Web SDK に浮き出し多角形レイヤーを追加する方法。
author: anastasia-ms
ms.author: v-stharr
ms.date: 10/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 19675a92101ed1a13b07bc1a4039701cd029a020
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102044087"
---
# <a name="add-a-polygon-extrusion-layer-to-the-map"></a>マップに多角形浮き出しレイヤーを追加する

この記事では、多角形浮き出しレイヤーを使用して、フィーチャー ジオメトリ `Polygon` および `MultiPolygon` の領域が浮き出るようにレンダリングする方法を紹介します。 Azure Maps の Web SDK では、[拡張 GeoJSON スキーマ](extend-geojson.md#circle)で定義されている円のジオメトリのレンダリングをサポートしています。 これらの円は、マップ上にレンダリングされるときに多角形に変換できます。 [atlas.Shape](/javascript/api/azure-maps-control/atlas.shape) クラスでラップされている場合は、すべてのフィーチャー ジオメトリを簡単に更新することができます。

## <a name="use-a-polygon-extrusion-layer"></a>多角形浮き出しレイヤーを使用する

[多角形浮き出しレイヤー](/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer)をデータ ソースに接続します。 その後、マップに読み込まれます。 多角形浮き出しレイヤーでは、`Polygon` および `MultiPolygon` のフィーチャーの領域が浮き出るようにレンダリングされます。 多角形浮き出しレイヤーの `height` と `base` のプロパティにより、地面からの基本距離と浮き出し形状の高さが **メートル** で定義されます。 次のコードでは、多角形を作成してそれをデータ ソースに追加し、PolygonExtrusionLayer クラスを利用してレンダリングする方法を確認できます。

> [!Note]
> 多角形浮き出しレイヤーに定義されている `base` 値は、`height` 以下にする必要があります。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="浮き出し多角形" src="https://codepen.io/azuremaps/embed/wvvBpvE?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/wvvBpvE'>Extruded polygon</a>」Pen を参照してください。</iframe>

## <a name="add-data-driven-polygons"></a>データ ドリブン ポリゴンを追加する

階級区分図は、多角形浮き出しレイヤーを使用してレンダリングできます。 浮き出しレイヤーの `height` および `fillColor` プロパティを、`Polygon` および `MultiPolygon` フィーチャー ジオメトリの統計変数の測定値に設定します。 次のコード サンプルは、州別の人口密度測定値を基準とする、米国の浮き出し階級区分図を示します。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="浮き出し階級区分図" src="https://codepen.io/azuremaps/embed/eYYYNox?height=265&theme-id=0&default-tab=result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/eYYYNox'>Extruded choropleth map</a>」Pen を参照してください。
</iframe>

## <a name="add-a-circle-to-the-map"></a>マップに円を追加する

Azure Maps は、[ここ](./extend-geojson.md#circle)に示されている円の定義を提供する、GeoJSON スキーマの拡張バージョンを使用してします。 `Circle` の `subType` プロパティと、**メートル** で半径を表わす数値付き `Radius` プロパティでフィーチャー `point` を作成することで、浮き出した円をマップ上でレンダリングできます。 次に例を示します。

```javascript
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-105.203135, 39.664087]
    },
    "properties": {
        "subType": "Circle",
        "radius": 1000
    }
} 
```

Azure Maps Web SDK では、このような `Point` フィーチャーが内部的に `Polygon` フィーチャーに変換されます。 このような `Point` フィーチャーは、次のコード サンプルで示すように、多角形浮き出しレイヤーを使用してマップ上にレンダリングできます。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="ドローン領空ポリゴン" src="https://codepen.io/azuremaps/embed/zYYYrxo?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/zYYYrxo'>Drone airspace polygon</a>」Pen を参照してください。
</iframe>

## <a name="customize-a-polygon-extrusion-layer"></a>多角形浮き出しレイヤーをカスタマイズする

多角形浮き出しレイヤーには、いくつかのスタイル設定オプションがあります。 次のツールでそれらをお試しください。

<br/>

<iframe height='700' scrolling='no' title='PoogBRJ' src='//codepen.io/azuremaps/embed/PoogBRJ/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/PoogBRJ/'>PoogBRJ</a>」Pen を表示します。
</iframe>

## <a name="next-steps"></a>次のステップ

この記事で使われているクラスとメソッドの詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [Polygon](/javascript/api/azure-maps-control/atlas.data.polygon)

> [!div class="nextstepaction"]
> [多角形浮き出しレイヤー](/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer)

その他のリソース:

> [!div class="nextstepaction"]
> [Azure Maps GeoJSON 仕様拡張機能](extend-geojson.md#circle)