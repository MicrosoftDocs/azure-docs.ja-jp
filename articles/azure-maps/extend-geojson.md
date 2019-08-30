---
title: Azure Maps の拡張された GeoJSON ジオメトリ | Microsoft Docs
description: Azure Maps で GeoJSON ジオメトリを拡張する方法について説明します。
author: sataneja
ms.author: sataneja
ms.date: 05/17/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 52325248d21a5d5112c9a7f9497c3e03fdf102a4
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881995"
---
# <a name="extended-geojson-geometries"></a>拡張された GeoJSON ジオメトリ

Azure Maps は、地理機能内で、または地理機能を併用して検索に役立つ API を提供します。
これらの API は、地理的な特徴 (州の境界、高速道路など) を表すための [GeoJSON 仕様][1]を標準化します。  

[GeoJSON 仕様][1]では、次のジオメトリのみがサポートされています。

* GeometryCollection
* LineString
* MultiLineString
* MultiPoint
* MultiPolygon
* Point
* Polygon

Azure Maps API ([Search Inside Geometry](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry) など) の中には、[GeoJSON 仕様][1]に含まれない "Circle" などのジオメトリが使用できるものもあります。

この記事では、特定のジオメトリを表すために、Azure Maps が [GeoJSON 仕様][1]をどのように拡張するかについて詳細に説明します。

## <a name="circle"></a>Circle

`Circle` ジオメトリは、[GeoJSON 仕様][1]ではサポートされていません。円を表すには、`GeoJSON Point Feature` オブジェクトが使用されます。

`GeoJSON Feature` オブジェクトを使用して表現される `Circle` ジオメトリは、以下を含む __必要があります__ 。

- Center

    円の中心は `GeoJSON Point` オブジェクトを使用して表します。

- Radius

    円の `radius` は `GeoJSON Feature` のプロパティを使用して表します。 半径値は、_メートル_単位で、`double` 型である必要があります。

- SubType

    円ジオメトリには、`subType` プロパティも含める必要があります。 このプロパティは、`GeoJSON Feature` のプロパティに含まれ、その値は _Circle_ である必要があります。

#### <a name="example"></a>例

円を表す方法は次のとおりです。中心は (緯度:47.639754、経度: -122.126986) であり、半径は 100 m で `GeoJSON Feature` オブジェクトを使用します。

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

`GeoJSON Polygon Feature` オブジェクトを使用して表現される `Rectangle` ジオメトリは、以下を含む __必要があります__ 。

- Corners

    四角形の角は、`GeoJSON Polygon` オブジェクトの座標を使用して表されます。 5 つの座標 (角ごとに 1 つと、多角形リングを閉じるための、1 番目の座標と同じ 5 番目の座標) が必要です。 これらの座標は、開発者が望むように整列および回転されると想定されます。

- SubType

    四角形ジオメトリには、`subType` プロパティも含める必要があります。 このプロパティは、`GeoJSON Feature` のプロパティに含まれ、その値は _Rectangle_ である必要があります。

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
## <a name="next-steps"></a>次の手順

Azure Maps の GeoJSON データの詳細について学習します。

> [!div class="nextstepaction"]
> [ジオフェンスの GeoJSON 形式](geofence-geojson.md)

Azure Maps と位置情報インテリジェンス アプリケーションに関連する一般的な技術用語の用語集を確認します。

> [!div class="nextstepaction"]
> [Azure Maps の用語集](glossary.md)

[1]: https://tools.ietf.org/html/rfc7946
