---
title: OData 地理空間関数リファレンス - Azure Search
description: 'Azure Search クエリでの OData 地理空間関数: geo.distance および geo.intersects。'
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: brjohnstmsft
ms.author: brjohnst
ms.manager: cgronlun
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: 0ce63ab1143c784eb3e10f47c20ef2b5034d63a7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67081880"
---
# <a name="odata-geo-spatial-functions-in-azure-search---geodistance-and-geointersects"></a>Azure Search クエリでの OData 地理空間関数 - `geo.distance` および `geo.intersects`

Azure Search では、[OData フィルター式](query-odata-filter-orderby-syntax.md)で `geo.distance` および `geo.intersects` 関数を介した地理空間クエリがサポートされています。 `geo.distance` 関数では、フィルターの一部として渡される 2 つのポイント (1 つはフィールドまたは範囲変数、もう 1 つは定数) の間の距離がキロメートル単位で返されます。 `geo.intersects` 関数からは、指定されたポイントが指定された多角形の内部にある場合、`true` が返されます。ポイントはフィールドまたは範囲変数として、多角形は定数として指定されて、フィルターの一部として渡されます。

また、[ **$orderby** パラメーター](search-query-odata-orderby.md)内で `geo.distance` 関数を使用することで、指定されたポイントからの距離によって検索結果を並べ替えることもできます。 **$orderby** の `geo.distance` の構文は **$filter** の場合と同じになります。 **$orderby** で `geo.distance` を使用するとき、それが適用されるフィールドは `Edm.GeographyPoint` 型にする必要があり、また**並べ替え可能**である必要があります。

## <a name="syntax"></a>構文

次の EBNF ([拡張バッカス・ナウア記法](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) では、`geo.distance` および `geo.intersects` 関数の文法と、それらが操作する地理空間値が定義されます。

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
geo_distance_call ::=
    'geo.distance(' variable ',' geo_point ')'
    | 'geo.distance(' geo_point ',' variable ')'

geo_point ::= "geography'POINT(" lon_lat ")'"

lon_lat ::= float_literal ' ' float_literal

geo_intersects_call ::=
    'geo.intersects(' variable ',' geo_polygon ')'

/* You need at least four points to form a polygon, where the first and
last points are the same. */
geo_polygon ::=
    "geography'POLYGON((" lon_lat ',' lon_lat ',' lon_lat ',' lon_lat_list "))'"

lon_lat_list ::= lon_lat(',' lon_lat)*
```

対話型の構文ダイアグラムも利用できます。

> [!div class="nextstepaction"]
> [Azure Search の OData 構文ダイアグラム](https://azuresearch.github.io/odata-syntax-diagram/#geo_distance_call)

> [!NOTE]
> 完全な EBNF については、「[Azure Search の OData 式構文リファレンス](search-query-odata-syntax-reference.md)」をご覧ください。

### <a name="geodistance"></a>geo.distance

`geo.distance` 関数は、`Edm.GeographyPoint` 型の 2 つのパラメーターを取り、両者の間の距離である `Edm.Double` 値をキロメートル単位で返します。 これは、距離を通常はメートルで返す、OData 地理空間操作をサポートする他のサービスとは異なります。

`geo.distance` に対するパラメーターの一方は geography ポイント定数とし、もう一方はフィールド パス (または `Collection(Edm.GeographyPoint)` 型のフィールドを反復処理するフィルターの場合は範囲変数) とする必要があります。 これらのパラメーターの順序は関係ありません。

geography ポイント定数の形式は `geography'POINT(<longitude> <latitude>)'` となります。ここで、longitude と latitude は数値定数です。

> [!NOTE]
> フィルターで `geo.distance` を使用する場合は、`lt`、`le`、`gt`、`ge` を使用して、関数で返される距離と定数を比較する必要があります。 演算子の `eq` と `ne` は、距離を比較するときは使用できません。 たとえば、`geo.distance` の正しい使用は次のとおりです: `$filter=geo.distance(location, geography'POINT(-122.131577 47.678581)') le 5`。

### <a name="geointersects"></a>geo.intersects

`geo.intersects` 関数は、`Edm.GeographyPoint` 型の変数と、定数 `Edm.GeographyPolygon` を取り、ポイントが多角形の境界内にある場合は `Edm.Boolean` -- `true` を返し、それ以外の場合は `false` を返します。

多角形は、境界リングを定義するポイント シーケンスとして格納される 2 次元の表面です (下の[例](#examples)を参照してください)。 多角形は閉じられている必要があります。つまり、最初と最後のポイント セットを同じにする必要があります。 [多角形のポイントは反時計回りにする必要があります](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1)。

### <a name="geo-spatial-queries-and-polygons-spanning-the-180th-meridian"></a>180 度経線に広がる地理空間のクエリと多角形

180 度経線 (日付変更線の近く) を含むクエリを作成する多くの地理空間クエリ ライブラリは、使用禁止か、多角形を 2 つに分割して経線のそれぞれの側に 1 つずつ配置するなどの回避策が必要とします。

Azure Search では、クエリの形状が長方形で、(`geo.intersects(location, geography'POLYGON((179 65, 179 66, -179 66, -179 65, 179 65))'` のように) 経度と緯度に沿ってグリッド レイアウトに合わせて座標の位置が調整される場合、180 度経線を含む地理空間クエリは予想どおり動作します。 それ以外の場合、長方形ではない形状や整列されていない形状については、多角形を分割する手法を検討してください。  

### <a name="geo-spatial-functions-and-null"></a>地理空間関数と `null`

Azure Search での他のコレクション以外のすべてのフィールドのように、`Edm.GeographyPoint` 型のフィールドには `null` 値を含めることができます。 `null` であるフィールドに対する `geo.intersects` が Azure Search によって評価されると、結果は常に `false` になります。 この場合の `geo.distance` の動作はコンテキストによって異なります。

- フィルターでは、`null` フィールドの `geo.distance` は結果として `null` になります。 つまり、`null` を null 以外の任意の値と比較すると `false` に評価されるため、ドキュメントは一致しません。
- **$orderby** を使用して結果を並べ替える場合、`null` フィールドの `geo.distance` は結果として最大限の距離になります。 このようなフィールドを持つドキュメントは、`asc` の並べ替え方向が使用された場合 (既定値)、他のいずれのものよりも下位に並べ替えられ、方向が `desc` の場合は、他のいずれのものよりも上位に並べ替えられます。

## <a name="examples"></a>例

### <a name="filter-examples"></a>フィルターの例

指定された参照ポイントの 10 キロメートル内にあるホテルをすべて探します (場所は `Edm.GeographyPoint` 型のフィールドです)。

    geo.distance(location, geography'POINT(-122.131577 47.678581)') le 10

多角形として表現された所与のビューポート内にあるホテルをすべて探します (場所は `Edm.GeographyPoint` 型のフィールドです)。 多角形が閉じられていること (最初のポイントと最後のポイントのセットを同じにする必要があります)、[ポイントが時計回りで一覧表示されている](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1)ことに注意してください。

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')

### <a name="order-by-examples"></a>order by の例

`rating` 別にホテルを降順で並べ替え、その後、特定の座標からの距離別に昇順で並べ替えます。

    rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

`search.score` と `rating` に基づいて降順でホテルを並べ替え、その後、所与の座標からの距離別に昇順で並べ替え、2 つのホテルで評価が同じ場合、距離的に近い方が先に表示されるようにします。

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>次の手順  

- [Azure Search のフィルター](search-filters.md)
- [Azure Search の OData 式言語の概要](query-odata-filter-orderby-syntax.md)
- [Azure Search の OData 式構文リファレンス](search-query-odata-syntax-reference.md)
- [ドキュメントの検索 &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
