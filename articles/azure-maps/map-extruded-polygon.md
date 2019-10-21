---
title: Azure Maps に浮き出し多角形レイヤーを追加する | Microsoft Docs
description: Azure Maps Web SDK に浮き出し多角形レイヤーを追加する方法。
author: walsehgal
ms.author: v-musehg
ms.date: 10/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: fdb5e2b78d9e5817c5a5d139cdf0b34744ed011f
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2019
ms.locfileid: "72170865"
---
# <a name="add-an-extrusion-polygon-layer-to-the-map"></a>浮き出し多角形レイヤーをマップに追加する

この記事では、多角形浮き出しレイヤーを使用し、フィーチャー ジオメトリ `Polygon` および `MultiPolygon` の領域がマップ上で浮き出るようにレンダリングする方法を紹介します。 Azure Maps の Web SDK では、[拡張 GeoJSON スキーマ](extend-geojson.md#circle)で定義されている円のジオメトリの作成もサポートしています。 これらの円は、マップ上にレンダリングされるときに多角形に変換されます。 [atlas.Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) クラスでラップされている場合は、すべてのフィーチャー ジオメトリも簡単に更新することができます。


## <a name="use-a-polygon-extrusion-layer"></a>多角形浮き出しレイヤーを使用する

[多角形浮き出しレイヤー](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer?view=azure-maps-typescript-latest)がデータ ソースに接続されており、マップ上に読み込まれると、フィーチャー `Polygon` および `MultiPolygon` の領域が浮き出してレンダリングされます。 多角形浮き出しレイヤーの `height` プロパティと `base` プロパティにより、地面からの基本距離と浮き出し形状の高さが**メートル**で定義されます。 次のコードでは、多角形を作成してそれをデータ ソースに追加し、PolygonExtrusionLayer クラスを利用してレンダリングする方法を確認できます。

> [!Note]
> 多角形浮き出しレイヤーに定義されている `base` 値は、`height` 以下にする必要があります。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="浮き出し多角形" src="https://codepen.io/azuremaps/embed/wvvBpvE?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/wvvBpvE'>Extruded polygon</a>」Pen を参照してください。</iframe>


## <a name="add-data-driven-multipolygons"></a>データ ドリブン マルチポリゴンを追加する

多角形浮き出しレイヤーを利用して階級区分図をレンダリングできます。その際、その `height` プロパティと `fillColor` プロパティをフィーチャー ジオメトリ `Polygon` および `MultiPolygon` の統計変数の測定値に比例するように設定します。 次のコード サンプルからは、州別の人口密度測定値を基準とする、米国の浮き出し階級区分図を確認できます。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="浮き出し階級区分図" src="https://codepen.io/azuremaps/embed/eYYYNox?height=265&theme-id=0&default-tab=result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/eYYYNox'>Extruded choropleth map</a>」Pen を参照してください。
</iframe>

## <a name="add-a-circle-to-the-map"></a>マップに円を追加する

Azure Maps は、[ここ](https://docs.microsoft.com/azure/azure-maps/extend-geojson#circle)に示されている円の定義を提供する、GeoJSON スキーマの拡張バージョンを使用してします。 `Circle` の `subType` プロパティと、**メートル**で半径を表わす数値付き `Radius` プロパティでフィーチャー `point` を作成することで、浮き出した円をマップ上でレンダリングできます。 例:

```Javascript
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

Azure Maps Web SDK では、このようなフィーチャー `Point` が内部的に `Polygon` に変換され、次のコード サンプルで示すように、多角形浮き出しレイヤーを使用してマップ上にレンダリングできます。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="ドローン領空ポリゴン" src="https://codepen.io/azuremaps/embed/zYYYrxo?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/zYYYrxo'>Drone airspace polygon</a>」Pen を参照してください。
</iframe>


## <a name="next-steps"></a>次の手順

この記事で使われているクラスとメソッドの詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [Polygon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [多角形浮き出しレイヤー](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer?view=azure-maps-typescript-latest)

その他のリソース:

> [!div class="nextstepaction"]
> [Azure Maps GeoJSON 仕様拡張機能](extend-geojson.md#circle)
