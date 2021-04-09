---
title: Azure Cosmos DB の地理空間データと GeoJSON 位置データ
description: Azure Cosmos DB と SQL API を使用して空間オブジェクトを作成する方法について説明します。
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/25/2021
ms.author: tisande
ms.custom: devx-track-js
ms.openlocfilehash: b20c72ae3ed8a8fffa02fc3a2c86f9f73ba2663b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101692132"
---
# <a name="geospatial-and-geojson-location-data-in-azure-cosmos-db"></a>Azure Cosmos DB の地理空間データと GeoJSON 位置データ
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

この記事では、Azure Cosmos DB の地理空間機能を紹介します。 地理空間インデックスに関するドキュメントを読むと、次の質問に答えられるようになります。

* 空間データを Azure Cosmos DB に保存する方法
* Azure Cosmos DB 内の空間データを SQL や LINQ で照会する方法
* Azure Cosmos DB の空間インデックスを有効または無効にする方法

## <a name="spatial-data-use-cases"></a>空間データのユース ケース

地理空間データには、主に近接検索クエリが含まれます (例: "現在地付近に存在するコーヒー ショップをすべて検索")。 一般的なユース ケースは次のとおりです。

* 特定の場所でのマーケティング イニシアチブを促進する位置情報分析。
* 小売や医療など、複数の業界向けの場所ベースのパーソナル化。
* 輸送の最適化のための物流強化。
* 特に保険会社や金融企業向けのリスク分析。
* アラートと通知に関する状況認識。

## <a name="introduction-to-spatial-data"></a>空間データの概要

空間データは、物体の空間における位置と形状を表現します。 ほとんどのアプリケーションにおける空間データは、地球上の物体、および地理空間データに対応します。 空間データを使用することで、人物の位置や、関心のある場所、都市や湖の境界を表現することができます。

Azure Cosmos DB の SQL API では、**geometry** データ型と **geography** データ型の 2 つの空間データ型がサポートされています。

- **geometry** 型では、ユークリッド (平面) 座標系でデータを表します。
- **geography** 型は、球体地球座標系のデータを表します。

## <a name="supported-data-types"></a>サポートされるデータ型

Azure Cosmos DB は、[GeoJSON 仕様](https://tools.ietf.org/html/rfc7946)を使用して表現された地理空間ポイント データのインデックスとクエリをサポートしています。 GeoJSON データの構造は常に有効な JSON オブジェクトであるため、特殊なツールやライブラリがなくても、Azure Cosmos DB を使って保存とクエリを実行できます。

Azure Cosmos DB では、次の空間データ型がサポートされます。

- ポイント
- LineString
- 多角形
- MultiPolygon

### <a name="points"></a>ポイント

**Point** は、空間における一点の位置を表します。 スーパーやキオスクの所在地、自動車、都市などの位置は、地理空間データにおけるポイントによって正確に表されます。  GeoJSON (と Azure Cosmos DB) では、ポイントがその座標ペアまたは経度/緯度によって表されます。

次に示したのは、ポイントを表す JSON の例です。

**Azure Cosmos DB のポイント**

```json
{
    "type":"Point",
    "coordinates":[ 31.9, -4.8 ]
}
```

空間データ型は、Azure Cosmos DB ドキュメントに埋め込むことができます。次の例では、位置情報データを含んだユーザー プロファイルにデータを埋め込んでいます。

**Azure Cosmos DB に保存された、位置データを含むプロファイルの使用**

```json
{
    "id":"cosmosdb-profile",
    "screen_name":"@CosmosDB",
    "city":"Redmond",
    "topics":[ "global", "distributed" ],
    "location":{
        "type":"Point",
        "coordinates":[ 31.9, -4.8 ]
    }
}
```

### <a name="points-in-a-geometry-coordinate-system"></a>geometry 座標系のポイント

**geometry** データ型の場合、GeoJSON の仕様では、まず水平軸が指定され、次に垂直軸が指定されます。

### <a name="points-in-a-geography-coordinate-system"></a>geography 座標系のポイント

**geography** データ型の場合、GeoJSON 仕様では、最初に経度、次に緯度と指定されています。 他の地図作成アプリケーションと同様、経度と緯度は角度であり、度 (°) の単位で表されます。 経度値は、グリニッジ子午線を基準とする -180°～ 180.0°の範囲で測定され、緯度値は、赤道を基準とする -90.0°～ 90.0°の範囲で測定されます。

Azure Cosmos DB では座標が WGS-84 測地系で解釈されます。 座標参照系の詳細については、以下を参照してください。

### <a name="linestrings"></a>LineStrings

**LineString** は、空間における一連の複数のポイントとそれらをつなぐ線分を表します。 地理空間データでは、幹線道路や川を表すために LineString がよく使用されます。

**GeoJSON の LineStrings**

```json
    "type":"LineString",
    "coordinates":[ [
        [ 31.8, -5 ],
        [ 31.8, -4.7 ]
    ] ]
```

### <a name="polygons"></a>多角形

接続されたポイントが LineString を形成し、それによって囲まれた境界が **Polygon** となります。 ポリゴンは通常、湖や行政区 (市区町村、都道府県など) など、自然な形状を表す目的で使用されます。 以下は Azure Cosmos DB における Polygon の例です。

**GeoJSON における Polygon**

```json
{
    "type":"Polygon",
    "coordinates":[ [
        [ 31.8, -5 ],
        [ 32, -5 ],
        [ 32, -4.7 ],
        [ 31.8, -4.7 ],
        [ 31.8, -5 ]
    ] ]
}
```

> [!NOTE]
> GeoJSON 仕様では、最後に指定された座標ペアが、最初に指定された座標ペアとちょうど重なり、閉じた形状になっていることが、有効な Polygon の条件となります。
>
> Polygon 内のポイントは、反時計回りに指定する必要があります。 時計回りに指定された Polygon は、その中の領域を逆にしたものを表します。
>
>

### <a name="multipolygons"></a>MultiPolygon

**MultiPolygon** は、0 個以上の多角形の配列です。 **MultiPolygon** は辺を重ねたり、共通の領域を持つことはできません。 1 つ以上の点で接することはできます。

**GeoJSON の MultiPolygon**

```json
{
    "type":"MultiPolygon",
    "coordinates":[[[
        [52.0, 12.0],
        [53.0, 12.0],
        [53.0, 13.0],
        [52.0, 13.0],
        [52.0, 12.0]
        ]],
        [[
        [50.0, 0.0],
        [51.0, 0.0],
        [51.0, 5.0],
        [50.0, 5.0],
        [50.0, 0.0]
        ]]]
}
```

## <a name="coordinate-reference-systems"></a>座標参照系

地球の形状は不規則であるため、地理の地理空間データの座標は多く座標参照系 (CRS) で表され、それぞれ独自の基準系と測定単位が存在します。 たとえば、"National Grid of Britain" は、イギリスでは精度の高い座標系ですが、イギリス外では精度が下がります。

今日使われている最も一般的な CRS は、世界測地系の [WGS-84](https://earth-info.nga.mil/GandG/update/index.php) です。 WGS-84 は、GPS 装置や、多くの地図サービス (Google マップ、Bing マップの API など) で使用されています。 Azure Cosmos DB でサポートされるのは、WGS-84 CRS を使用した地理の地理空間データのインデックスとクエリだけです。

## <a name="creating-documents-with-spatial-data"></a>空間データを使用したドキュメントの作成
GeoJSON の値を含んだドキュメントを作成すると、対応するコンテナーのインデックス作成ポリシーに従い、空間インデックスを使用して自動的にインデックスが作成されます。 Python や Node.js など動的に型付けされる言語で Azure Cosmos DB SDK を使用している場合は、有効な GeoJSON を作成する必要があります。

**地理空間データを含んだドキュメントの作成 (Node.js)**

```javascript
var userProfileDocument = {
    "id":"cosmosdb",
    "location":{
        "type":"Point",
        "coordinates":[ -122.12, 47.66 ]
    }
};

client.createDocument(`dbs/${databaseName}/colls/${collectionName}`, userProfileDocument, (err, created) => {
    // additional code within the callback
});
```

SQL API の場合は、`Microsoft.Azure.Cosmos.Spatial` 名前空間内の `Point`、`LineString`、`Polygon`、および `MultiPolygon` クラスを使って、アプリケーションのオブジェクト内に位置情報を埋め込むことができます。 これらのクラスを使用して簡単に、空間データを GeoJSON にシリアル化したり、逆シリアル化したりすることができます。

**地理空間データを含んだドキュメントの作成 (.NET)**

```csharp
using Microsoft.Azure.Cosmos.Spatial;

public class UserProfile
{
    [JsonProperty("id")]
    public string id { get; set; }

    [JsonProperty("location")]
    public Point Location { get; set; }

    // More properties
}

await container.CreateItemAsync( new UserProfile
    {
        id = "cosmosdb",
        Location = new Point (-122.12, 47.66)
    });
```

緯度情報と経度情報がなくても、物理的な住所や所在地名 (都市、国や地域など) があれば、Bing マップ REST サービスなどのジオコーディング サービスを使って実際の座標を検索することができます。 Bing マップのジオコーディングの詳細については、 [こちら](/bingmaps/rest-services/)を参照してください。

## <a name="next-steps"></a>次のステップ

Azure Cosmos DB の地理空間機能の基本的な使い方を学んだら、次の段階に進みましょう。

* [Azure Cosmos DB のクエリ](sql-query-getting-started.md)の詳細を確認する
* [Azure Cosmos DB を使用した空間データのクエリ](sql-query-geospatial-query.md)の詳細を確認する
* [Azure Cosmos DB で空間データのインデックスを作成する](sql-query-geospatial-index.md)方法の詳細を確認する