---
title: 単純な Lucene クエリ構文を使用する
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search インデックスに対するフルテキスト検索、フィルター検索、および地理検索の簡単な構文を示すクエリの例。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 2abe19351c92bf9cea85c85dd55f47b5ee6d1625
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101694038"
---
# <a name="use-the-simple-search-syntax-in-azure-cognitive-search"></a>Azure Cognitive Search での "単純な" 検索構文を使用する

Azure Cognitive Search では、[単純なクエリ構文](query-simple-syntax.md)で既定のクエリ パーサーが呼び出されて、フルテキスト検索が実行されます。 このパーサーは高速で、フルテキスト検索、フィルター検索、ファセット検索、プレフィックス検索などの一般的なシナリオに対応します。 この記事では、例を使用して、[ドキュメントの検索 (REST API)](/rest/api/searchservice/search-documents) 要求での単純な構文の使用方法を示します。

> [!NOTE]
> 代替のクエリ構文は[完全な Lucene](query-lucene-syntax.md) であり、あいまい検索、ワイルドカード検索などのより複雑なクエリ構造をサポートしています。 詳細と例については、[完全な Lucene 構文の使用](search-query-lucene-examples.md)に関するページをご覧ください。

## <a name="hotels-sample-index"></a>ホテルのサンプル インデックス

以下のクエリは、この[クイックスタート](search-get-started-portal.md)の手順に従って作成できる hotels-sample-index に基づいています。

クエリの例は、REST API および POST 要求を使用して表されています。 [Postman](search-get-started-rest.md) または [Cognitive Search 拡張機能を備えた Visual Studio Code](search-get-started-vs-code.md) に貼り付けて実行できます。

要求ヘッダーには次の値が必要です。

| Key | 値 |
|-----|-------|
| Content-Type | application/json|
| api-key  | `<your-search-service-api-key>`、クエリまたは管理者キーのいずれか |

次の例のように、URI パラメーターには、検索サービスのエンドポイントと、インデックス名、docs コレクション、search コマンド、および API バージョンを含める必要があります。

```http
https://{{service-name}}.search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30
```

要求本文は有効な JSON の形式である必要があります。

```json
{
    "search": "*",
    "queryType": "simple",
    "select": "HotelId, HotelName, Category, Tags, Description",
    "count": true
}
```

+ `*` に設定された "search" は null または空の検索に相当する未指定のクエリです。 これは特に便利なわけではありませんが、最も簡単な検索方法であり、インデックス内の取得可能なすべてのフィールドに値がすべて入った状態で表示されます。

+ "simple" に設定された "queryType" は既定値であり、省略可能ですが、この記事中のクエリの例が単純な構文で表現されていることを強調するために含めています。

+ フィールドのコンマ区切りリストに設定した "select" は、検索結果のコンテキストで有用なフィールドのみが含まれるように検索結果を構成するために使用されます。

+ "count"は、検索条件に一致するドキュメントの数を返します。 空の検索文字列では、この数はインデックス内のすべてのドキュメントになります (hotels-sample-index の場合は 50)。

## <a name="example-1-full-text-search"></a>例 1: フルテキスト検索

フルテキスト検索では、ブール演算子の有無にかかわらず、スタンドアロンの用語または引用符で囲んだ語句をいくつでも使用できます。 

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "pool spa +airport",
    "searchMode": any,
    "queryType": "simple",
    "select": "HotelId, HotelName, Category, Description",
    "count": true
}
```

重要な用語または語句で構成したキーワード検索が、最もよく機能する傾向があります。 文字列フィールドでは、インデックス作成およびクエリ中にテキスト分析が行われ、"the"、"and"、"it" などの不必要な単語が削除されます。 クエリ文字列がインデックスでどのようにトークン化されるかを確認するには、[Analyze Text](/rest/api/searchservice/test-analyzer) 呼び出しで文字列をインデックスに渡します。

"searchMode" パラメーターは、精度とリコールを制御します。 リコールを増やしたい場合、既定の "any" 値を使用して、クエリ文字列の一部でも一致した場合に結果を返すようにします。 精度を優先し、文字列の全部が一致することを必須にする場合は、searchMode を "all" に変更します。 両方のやり方で上記のクエリを試して、searchMode によって結果がどのように変わるかを確認してください。

"pool spa +airport" クエリの応答は次の例のようになります (簡潔にするために省略しています)。

```json
"@odata.count": 6,
"value": [
    {
        "@search.score": 7.3617697,
        "HotelId": "21",
        "HotelName": "Nova Hotel & Spa",
        "Description": "1 Mile from the airport.  Free WiFi, Outdoor Pool, Complimentary Airport Shuttle, 6 miles from the beach & 10 miles from downtown.",
        "Category": "Resort and Spa",
        "Tags": [
            "pool",
            "continental breakfast",
            "free parking"
        ]
    },
    {
        "@search.score": 2.5560288,
        "HotelId": "25",
        "HotelName": "Scottish Inn",
        "Description": "Newly Redesigned Rooms & airport shuttle.  Minutes from the airport, enjoy lakeside amenities, a resort-style pool & stylish new guestrooms with Internet TVs.",
        "Category": "Luxury",
        "Tags": [
            "24-hour front desk service",
            "continental breakfast",
            "free wifi"
        ]
    },
    {
        "@search.score": 2.2988036,
        "HotelId": "35",
        "HotelName": "Suites At Bellevue Square",
        "Description": "Luxury at the mall.  Located across the street from the Light Rail to downtown.  Free shuttle to the mall and airport.",
        "Category": "Resort and Spa",
        "Tags": [
            "continental breakfast",
            "air conditioning",
            "24-hour front desk service"
        ]
    }
```

応答の検索スコアに注目してください。 これは一致の関連性スコアです。 既定では、検索サービスはこのスコアに基づいて上位 50 件の一致を返します。

検索がフルテキスト検索でないか、条件が指定されていないためランクがない場合は、"1.0" の均一のスコアが発生します。 たとえば、空の検索 (search=`*`) の場合、行は任意の順序で返されます。 実際の基準を含めると、検索スコアは意味のある値に変化します。

## <a name="example-2-look-up-by-id"></a>例 2:ID による参照

クエリで検索結果を返す場合は、次の論理的な手順として、ドキュメントから多くのフィールドを含む詳細ページを指定します。 この例では、[Lookup Document](/rest/api/searchservice/lookup-document) を使用してドキュメント ID を渡して 1 つのドキュメントを返す方法を示します。

```http
GET /indexes/hotels-sample-index/docs/41?api-version=2020-06-30
```

すべてのドキュメントは一意の識別子を持ちます。 ポータルを使用している場合は、 **[インデックス]** タブからインデックスを選択し、フィールド定義を見てどのフィールドがキーであるかを判断します。 [Get Index](/rest/api/searchservice/get-index) 呼び出しでは、REST を使用して、応答本文にインデックス定義を返します。

上記のクエリに対する応答は、そのキーが 41 であるドキュメントで構成されます。 インデックス定義で "retrievable" とマークされているすべてのフィールドが検索結果で返され、アプリに表示されます。

```json
{
    "HotelId": "41",
    "HotelName": "Ocean Air Motel",
    "Description": "Oceanfront hotel overlooking the beach features rooms with a private balcony and 2 indoor and outdoor pools. Various shops and art entertainment are on the boardwalk, just steps away.",
    "Description_fr": "L'hôtel front de mer surplombant la plage dispose de chambres avec balcon privé et 2 piscines intérieures et extérieures. Divers commerces et animations artistiques sont sur la promenade, à quelques pas.",
    "Category": "Budget",
    "Tags": [
        "pool",
        "air conditioning",
        "bar"
    ],
    "ParkingIncluded": true,
    "LastRenovationDate": "1951-05-10T00:00:00Z",
    "Rating": 3.5,
    "Location": {
        "type": "Point",
        "coordinates": [
            -157.846817,
            21.295841
        ],
        "crs": {
            "type": "name",
            "properties": {
                "name": "EPSG:4326"
            }
        }
    },
    "Address": {
        "StreetAddress": "1450 Ala Moana Blvd 2238 Ala Moana Ctr",
        "City": "Honolulu",
        "StateProvince": "HI",
        "PostalCode": "96814",
        "Country": "USA"
    },
```

## <a name="example-3-filter-on-text"></a>例 3: テキストのフィルター

[フィルター構文](search-query-odata-filter.md)は、単独で使用することも、"search" と一緒に使用することもできる OData 式です。 一緒に使用した場合、"filter" が最初にインデックス全体に適用され、次にフィルター処理の結果に対して検索が実行されます。 フィルターはクエリのパフォーマンス向上に役立つ手法です。フィルターを使うと、検索クエリで処理が必要なドキュメントの数が減ります。

フィルターは、インデックス定義で "filterable" とマークされている任意のフィールドに対して定義できます。 hotel-sample-index の場合、フィルター可能なフィールドには、Category、Tags、ParkingIncluded、Rating、ほとんどの Address フィールドが含まれます。

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "art tours",
    "queryType": "simple",
    "filter": "Category eq 'Resort and Spa'",
    "select": "HotelId,HotelName,Description,Category",
    "count": true
}
```

上記のクエリに対する応答は、"Report and Spa" に分類され、用語 "art" または "tour" を含むホテルのみがスコープになっています。 この場合、一致するのは 1 件だけです。

```json
{
    "@search.score": 2.8576312,
    "HotelId": "31",
    "HotelName": "Santa Fe Stay",
    "Description": "Nestled on six beautifully landscaped acres, located 2 blocks from the Plaza. Unwind at the spa and indulge in art tours on site.",
    "Category": "Resort and Spa"
}
```

## <a name="example-4-filter-functions"></a>例 4: フィルター関数

フィルター式に ["search.ismatch" および "search.ismatchscoring" 関数](search-query-odata-full-text-search-functions.md)を含めて、フィルターの中で検索クエリを構築することができます。 このフィルター式では、無料 Wi-Fi、無料駐車場などのアメニティを選択するために、*free* にワイルドカードを使用しています。

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
  {
    "search": "",
    "filter": "search.ismatch('free*', 'Tags', 'full', 'any')",
    "select": "HotelId, HotelName, Category, Description",
    "count": true
  }
```

上記のクエリに対する応答は、無料のアメニティを提供する 19 のホテルに一致します。 結果全体で検索スコアが均一の "1.0" であることに注目してください。 これは、検索式が null または空で、結果は逐語的フィルターの一致であり、フルテキスト検索は行われないためです。 関連性スコアは、フルテキスト検索でのみ返されます。 "search" なしでフィルターを使用している場合は、検索順位を制御できるよう、並べ替え可能なフィールドが十分にあることを確認してください。

```json
"@odata.count": 19,
"value": [
    {
        "@search.score": 1.0,
        "HotelId": "31",
        "HotelName": "Santa Fe Stay",
        "Tags": [
            "view",
            "restaurant",
            "free parking"
        ]
    },
    {
        "@search.score": 1.0,
        "HotelId": "27",
        "HotelName": "Super Deluxe Inn & Suites",
        "Tags": [
            "bar",
            "free wifi"
        ]
    },
    {
        "@search.score": 1.0,
        "HotelId": "39",
        "HotelName": "Whitefish Lodge & Suites",
        "Tags": [
            "continental breakfast",
            "free parking",
            "free wifi"
        ]
    },
    {
        "@search.score": 1.0,
        "HotelId": "11",
        "HotelName": "Regal Orb Resort & Spa",
        "Tags": [
            "free wifi",
            "restaurant",
            "24-hour front desk service"
        ]
    },
```

## <a name="example-5-range-filters"></a>例 5: 範囲フィルター

範囲フィルターは、任意のデータ型のフィルター式を通してサポートされます。 次の例は、数値と文字列の範囲を示しています。 範囲フィルターではデータ型が重要であり、数値フィールドにある数値データと文字列フィールドにある文字列データで最もうまく機能します。 数値文字列を比較できないため、文字列フィールドの数値データは範囲には適していません。

次のクエリは数値範囲です。 hotel-sample-index では、フィルター処理可能な数値フィールドは Rating のみです。

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "Rating ge 2 and Rating lt 4",
    "select": "HotelId, HotelName, Rating",
    "orderby": "Rating desc",
    "count": true
}
```

このクエリの応答は、次の例のようになります (簡潔にするため省略しています)。

```json
"@odata.count": 27,
"value": [
    {
        "@search.score": 1.0,
        "HotelId": "22",
        "HotelName": "Stone Lion Inn",
        "Rating": 3.9
    },
    {
        "@search.score": 1.0,
        "HotelId": "25",
        "HotelName": "Scottish Inn",
        "Rating": 3.8
    },
    {
        "@search.score": 1.0,
        "HotelId": "2",
        "HotelName": "Twin Dome Motel",
        "Rating": 3.6
    }
```

次のクエリは、文字列フィールド (Address/StateProvince) に対する範囲フィルターです。

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "Address/StateProvince ge 'A*' and Address/StateProvince lt 'D*'",
    "select": "HotelId, HotelName, Address/StateProvince",
    "count": true
}
```

このクエリの応答は、下記の例のようになります (簡潔にするため省略しています)。 この例では、インデックス定義で StateProvince の属性が "sortable" でないため、このフィールドによる並べ替えはできません。

```json
"@odata.count": 9,
"value": [
    {
        "@search.score": 1.0,
        "HotelId": "9",
        "HotelName": "Smile Hotel",
        "Address": {
            "StateProvince": "CA "
        }
    },
    {
        "@search.score": 1.0,
        "HotelId": "39",
        "HotelName": "Whitefish Lodge & Suites",
        "Address": {
            "StateProvince": "CO"
        }
    },
    {
        "@search.score": 1.0,
        "HotelId": "7",
        "HotelName": "Countryside Resort",
        "Address": {
            "StateProvince": "CA "
        }
    },
```

## <a name="example-6-geo-search"></a>例 6: 地理検索

hotels-sample インデックスには、緯度と経度の座標を持つ geo_location フィールドが含まれています。 この例では、[geo.distance 関数](search-query-odata-geo-spatial-functions.md#examples)を使用して、開始点を中心に、指定された距離 (キロメートル単位) に収まるドキュメントをフィルター処理します。 クエリの最後の値 (10) を調整して、クエリのサーフェス領域を拡大または縮小できます。

```http
POST /indexes/v/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "geo.distance(Location, geography'POINT(-122.335114 47.612839)') le 10",
    "select": "HotelId, HotelName, Address/City, Address/StateProvince",
    "count": true
}
```

このクエリに対する応答は、指定された座標から 10 km 以内の距離にあるすべてのホテルを返します。

```json
{
    "@odata.count": 3,
    "value": [
        {
            "@search.score": 1.0,
            "HotelId": "45",
            "HotelName": "Arcadia Resort & Restaurant",
            "Address": {
                "City": "Seattle",
                "StateProvince": "WA"
            }
        },
        {
            "@search.score": 1.0,
            "HotelId": "24",
            "HotelName": "Gacc Capital",
            "Address": {
                "City": "Seattle",
                "StateProvince": "WA"
            }
        },
        {
            "@search.score": 1.0,
            "HotelId": "16",
            "HotelName": "Double Sanctuary Resort",
            "Address": {
                "City": "Seattle",
                "StateProvince": "WA"
            }
        }
    ]
}
```

## <a name="example-7-booleans-with-searchmode"></a>例 7:searchMode によるブール値

単純構文では、AND、OR、NOT のクエリ ロジックをサポートするために、文字 (`+, -, |`) の形式のブール演算子をサポートしています。 ブール型の検索は想定どおりに動作しますが、いくつかの注目すべき例外があります。 

以前の例では、"searchMode" パラメーターについて、精度とリコールに影響するメカニズムとして紹介しました。"searchMode=any" はリコールを優先し (一部でも条件を満たすドキュメントは一致と見なされます)、"searchMode=all" は精度を優先します (すべての条件がドキュメント内で一致する必要があります)。 

ブール検索のコンテキストでは、複数の演算子を使用してクエリをスタックし、結果を狭めるのではなく広げようとしている場合に、既定の "searchMode=any" は混乱を招く可能性があります。 これは特に NOT に当てはまります。NOT では、特定の用語または語句を "含まない" すべてのドキュメントが結果に含まれます。

具体的な例を次に示します。 searchMode (any) を使用して次のクエリを実行すると、42 個のドキュメントが返されます。用語 "restaurant" を含むものに加えて、語句 "air conditioning" が存在しないすべてのドキュメントです。 

ブール演算子 (`-`) と語句 "air conditioning" の間にスペースがないことに注目してください。

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "restaurant -\"air conditioning\"",
    "searchMode": "any",
    "searchFields": "Tags",
    "select": "HotelId, HotelName, Tags",
    "count": true
}
```

"searchMode=all" に変更すると、条件に対する累積効果が適用され、用語 "restaurant" を含むドキュメントから、語句 "air conditioning" を含むものを除いて構成される、より小さな結果セット (7 件の一致) が返されます。

このクエリの応答は、次の例のようになりました (簡潔にするため省略しています)。

```json
"@odata.count": 7,
"value": [
    {
        "@search.score": 2.5460577,
        "HotelId": "11",
        "HotelName": "Regal Orb Resort & Spa",
        "Tags": [
            "free wifi",
            "restaurant",
            "24-hour front desk service"
        ]
    },
    {
        "@search.score": 2.166792,
        "HotelId": "10",
        "HotelName": "Countryside Hotel",
        "Tags": [
            "24-hour front desk service",
            "coffee in lobby",
            "restaurant"
        ]
    },
```

## <a name="example-8-paging-results"></a>例 8: 結果のページング

以前の例では、検索結果の構成に影響を与えるパラメーターについて説明しました。たとえば "select" は、結果に含まれるフィールド、並べ替え順序、および、すべての一致のカウントを含める方法を決定します。 今回の例は、検索結果の構成の続きです。特定のページに表示する複数の結果をバッチ化するためのページング パラメーターの形式について扱います。 

既定では、検索サービスは上位 50 件の一致を返します。 各ページの一致の数を制御するには、"top" を使用してバッチのサイズを定義した後、"skip" を使用して後続のバッチを取得します。

並べ替えた結果に対するページングの影響のほうが確認しやすいため、次の例では、Rating フィールドでフィルターと並べ替え順序を使用しています (Rating はフィルター処理と並べ替えの両方が可能です)。 通常の完全検索クエリでは、上位の一致は "@search.score" によって順位付けおよびページングされます。

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "Rating gt 4",
    "select": "HotelName, Rating",
    "orderby": "Rating desc",
    "top": "5",
    "count": true
}
```

このクエリでは 21 件の一致するドキュメントが見つかりますが、"top" を指定したため、応答では上位 5 件の一致のみが返され、評価は 4.9 が最高、"Lady of the Lake B & B" の 4.7 が最低になります。 

次の 5 件を取得するには、最初のバッチをスキップします。

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "Rating gt 4",
    "select": "HotelName, Rating",
    "orderby": "Rating desc",
    "top": "5",
    "skip": "5",
    "count": true
}
```

2 番目のバッチの応答では、最初の 5 件の一致をスキップして、"Pull'r Inn Motel" から始まる次の 5 件を返します。 それ以降のバッチを取得するには、"top" は 5 のままにして、(skip=5、skip=10、skip=15 というように) 新しい要求のたびに "skip" を 5 ずつ大きくします。

```json
"value": [
    {
        "@search.score": 1.0,
        "HotelName": "Pull'r Inn Motel",
        "Rating": 4.7
    },
    {
        "@search.score": 1.0,
        "HotelName": "Sublime Cliff Hotel",
        "Rating": 4.6
    },
    {
        "@search.score": 1.0,
        "HotelName": "Antiquity Hotel",
        "Rating": 4.5
    },
    {
        "@search.score": 1.0,
        "HotelName": "Nordick's Motel",
        "Rating": 4.5
    },
    {
        "@search.score": 1.0,
        "HotelName": "Winter Panorama Resort",
        "Rating": 4.5
    }
]
```

## <a name="next-steps"></a>次のステップ

基本的なクエリの構文がある程度理解できたら、次は、コードでクエリを指定してみましょう。 次のリンクは、Azure SDK を使用して検索クエリを設定する方法について説明しています。

+ [.NET SDK を使用したインデックスのクエリ実行](search-get-started-dotnet.md)
+ [Python SDK を使用したインデックスのクエリ実行](search-get-started-python.md)
+ [JavaScript SDK を使用したインデックスのクエリ実行](search-get-started-javascript.md)

追加の構文リファレンス、クエリ アーキテクチャ、およびサンプルについては、次のリンク先を参照してください。

+ [高度なクエリを作成するための Lucene 構文のクエリの例](search-query-lucene-examples.md)
+ [Azure Cognitive Search でのフルテキスト検索のしくみ](search-lucene-query-architecture.md)
+ [単純なクエリ構文](query-simple-syntax.md)
+ [Full Lucene クエリ構文](query-lucene-syntax.md)
+ [フィルターの構文](search-query-odata-filter.md)