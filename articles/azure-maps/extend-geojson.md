---
title: 拡張された GeoJSON ジオメトリ | Microsoft Azure Maps
description: Azure Maps で、追加の幾何学図形を含めるために GeoJSON 仕様がどのように拡張されているかを説明します。 マップで使用する円と四角形を設定する例を確認します。
author: sataneja
ms.author: sataneja
ms.date: 05/17/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 87a5dec2582796c6703bfb37b76e26e53a1192c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92895785"
---
# <a name="extended-geojson-geometries"></a>拡張された GeoJSON ジオメトリ

Azure Maps は、地理機能内で、または地理機能を併用して検索に役立つ API のリストを提供します。 これらの API は、地理的機能を表すための標準 [GeoJSON 仕様][1] に準拠しています。  

[GeoJSON 仕様][1]では、次のジオメトリのみがサポートされています。

* GeometryCollection
* LineString
* MultiLineString
* MultiPoint
* MultiPolygon
* ポイント
* 多角形

一部の Azure Maps API は、[GeoJSON spec][1]の一部ではないジオメトリを受け入れます。たとえば、[Geometry API 内の検索](/rest/api/maps/search/postsearchinsidegeometry)では、円と多角形が受け入れられます。

この記事では、特定のジオメトリを表すために、Azure Maps が [GeoJSON 仕様][1]をどのように拡張するかについて詳細に説明します。

## <a name="circle"></a>Circle

`Circle` ジオメトリは、[GeoJSON 仕様][1]ではサポートされていません。円を表すには、`GeoJSON Point Feature` オブジェクトが使用されます。

`GeoJSON Feature`オブジェクトを使用して表される`Circle`ジオメトリには、次の座標とプロパティが含まれている __必要があります__。

- Center

    円の中心は `GeoJSON Point` オブジェクトを使用して表します。

- Radius

    円の `radius` は `GeoJSON Feature` のプロパティを使用して表します。 半径値は、_メートル_ 単位で、`double` 型である必要があります。

- SubType

    円ジオメトリには、`subType` プロパティも含める必要があります。 このプロパティは、`GeoJSON Feature` のプロパティに含まれ、その値は _Circle_ である必要があります。

#### <a name="example"></a>例

`GeoJSON Feature` オブジェクトを使用して円を表す方法は次のとおりです。: 円の中心を緯度:47.639754 と経度 :-122.126986 に設定し、100メートルに等しい半径を割り当てます。

```json            
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

## <a name="rectangle"></a>Rectangle

`Rectangle` ジオメトリは、[GeoJSON 仕様][1]ではサポートされていません。四角形を表すには、`GeoJSON Polygon Feature` オブジェクトが使用されます。 四角形の拡張機能は、主に Web SDK の描画ツール モジュールによって使用されます。

`GeoJSON Polygon Feature`オブジェクトを使用して表される`Rectangle`ジオメトリには、次の座標とプロパティが含まれている __必要があります__。

- Corners

    四角形の角は、`GeoJSON Polygon` オブジェクトの座標を使用して表されます。 コーナーごとに1つずつ、5つの座標を指定する必要があります。 とは、最初の座標と同じ5番目の座標です。多角形リングを閉じます。 これらの座標が調整され、開発者が必要に応じて回転できることが前提となります。

- SubType

    四角形ジオメトリには、`subType` プロパティも含める必要があります。 このプロパティは`GeoJSON Feature`のプロパティの一部である必要があり、その値は _四角形_ である必要があります。

### <a name="example"></a>例

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Polygon",
        "coordinates": [[[5,25],[14,25],[14,29],[5,29],[5,25]]]
    },
    "properties": {
        "subType": "Rectangle"
    }
}

```
## <a name="next-steps"></a>次のステップ

Azure Maps の GeoJSON データの詳細について学習します。

> [!div class="nextstepaction"]
> [ジオフェンスの GeoJSON 形式](geofence-geojson.md)

Azure Maps と位置情報インテリジェンス アプリケーションに関連する一般的な技術用語の用語集を確認します。

> [!div class="nextstepaction"]
> [Azure Maps の用語集](glossary.md)

[1]: https://tools.ietf.org/html/rfc7946