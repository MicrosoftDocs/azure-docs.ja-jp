---
title: 完全な Lucene クエリ構文の使用
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search インデックスでのあいまい検索、近接検索、用語ブースト、正規表現検索、ワイルドカード検索用の Lucene クエリ構文を示すクエリの例。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 6213efb6ba14052c6f957a6d999f48f55f65186c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101693562"
---
# <a name="use-the-full-lucene-search-syntax-advanced-queries-in-azure-cognitive-search"></a>"完全な" Lucene 検索構文の使用 (Azure Cognitive Search での高度なクエリ)

Azure Cognitive Search のクエリを構築するときは、既定の[シンプルなクエリ パーサー](query-simple-syntax.md)をより強力な [Lucene クエリ パーサー](query-lucene-syntax.md)に置き換えることにより、特殊化された高度なクエリ式を作成することができます。

Lucene パーサーは、フィールド スコープ クエリ、あいまい検索、インフィックスおよびサフィックスのワイルドカード検索、近接検索、用語ブースト、正規表現検索など、より複雑なクエリ形式に対応しています。 機能が強力になるほど処理要件が増えるため、実行時間がやや長くなることを見込んでください。 この記事では、完全な構文に基づいてクエリ操作をデモンストレーションする例について詳しく説明します。

> [!Note]
> 語幹検索や見出し語認定を想定していると意外なことですが、完全な Lucene クエリ構文で有効になる特殊化されたクエリ構造の多くは[テキスト解析](search-lucene-query-architecture.md#stage-2-lexical-analysis)されません。 語彙の分析は、完全な用語でのみ実行されます (用語クエリまたは語句クエリ)。 不完全な語句 (プレフィックス クエリ、ワイルドカードのクエリ、正規表現のクエリ、あいまいクエリ) でのクエリの種類は、解析ステージをバイパスして、クエリ ツリーに直接追加されます。 部分的なクエリ用語に対して適用される変換は、大文字から小文字への変換だけです。 
>

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
    "queryType": "full",
    "select": "HotelId, HotelName, Category, Tags, Description",
    "count": true
}
```

+ `*` に設定された "search" は null または空の検索に相当する未指定のクエリです。 これは特に便利なわけではありませんが、最も簡単な検索方法であり、インデックス内の取得可能なすべてのフィールドに値がすべて入った状態で表示されます。

+ "queryType" を "full" に設定すると、完全な Lucene クエリ パーサーが呼び出され、この構文ではこれが必要です。

+ フィールドのコンマ区切りリストに設定した "select" は、検索結果のコンテキストで有用なフィールドのみが含まれるように検索結果を構成するために使用されます。

+ "count"は、検索条件に一致するドキュメントの数を返します。 空の検索文字列では、この数はインデックス内のすべてのドキュメントになります (hotels-sample-index の場合は 50)。

## <a name="example-1-fielded-search"></a>例 1: フィールド検索

フィールド検索では、特定のフィールドが、埋め込まれた個別の検索式のスコープになります。 この例では、用語 "hotel" を含むホテル名を検索しますが、"motel" は検索しません。 AND を使用して複数のフィールドを指定できます。 

このクエリ構文を使用する場合、クエリするフィールドが検索式自体に含まれるときは "searchFields" パラメーターを省略できます。 "searchFields" をフィールド検索に含める場合、`fieldName:searchExpression` は "searchFields" よりも常に優先されます。

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "HotelName:(hotel NOT motel) AND Category:'Resort and Spa'",
    "queryType": "full",
    "select": "HotelName, Category",
    "count": true
}
```

このクエリの応答は次の例のようになります。"Resort and Spa" でフィルター処理され、"hotel" または "motel" が名前に含まれるホテルが返されます。

```json
"@odata.count": 4,
"value": [
    {
        "@search.score": 4.481559,
        "HotelName": "Nova Hotel & Spa",
        "Category": "Resort and Spa"
    },
    {
        "@search.score": 2.4524608,
        "HotelName": "King's Palace Hotel",
        "Category": "Resort and Spa"
    },
    {
        "@search.score": 2.3970203,
        "HotelName": "Triple Landscape Hotel",
        "Category": "Resort and Spa"
    },
    {
        "@search.score": 2.2953436,
        "HotelName": "Peaceful Market Hotel & Spa",
        "Category": "Resort and Spa"
    }
]
```

検索式は、単一の用語、単一の語句、またはかっこで囲まれた複雑な式が可能であり、必要に応じてブール演算子も使用できます。 例として、次のようなものがあります。

+ `HotelName:(hotel NOT motel)`
+ `Address/StateProvince:("WA" OR "CA")`
+ `Tags:("free wifi" NOT "free parking") AND "coffee in lobby"`

語句を 1 つのエンティティとして評価するのであれば、複数の文字列を引用符で囲ってください。この例では、Address/StateProvince フィールドで 2 つの異なる場所を検索しています。 クライアントによっては、引用符のエスケープ (`\`) が必要になる場合があります。

`fieldName:searchExpression` に指定されたフィールドは検索可能フィールドである必要があります。 フィールド定義の属性が決まるしくみの詳細については、[Create Index (REST API)](/rest/api/searchservice/create-index) に関するページを参照してください。

## <a name="example-2-fuzzy-search"></a>例 2: あいまい検索

あいまい検索は、スペルミスの単語を含め、類似した用語に一致します。 あいまい検索を実行するには、1 つの言葉の終わりにチルダ記号 `~` を付けます。任意で編集距離を指定するパラメーターとして 0 ～ 2 の値を指定します。 たとえば、`blue~` または `blue~1` は blue、blues、glue を返します。

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "Tags:conserge~",
    "queryType": "full",
    "select": "HotelName, Category, Tags",
    "searchFields": "HotelName, Category, Tags",
    "count": true
}
```

このクエリの応答は、一致するドキュメント内の "concierge" に解決します (簡潔にするために省略しています)。

```json
"@odata.count": 12,
"value": [
    {
        "@search.score": 1.1832147,
        "HotelName": "Secret Point Motel",
        "Category": "Boutique",
        "Tags": [
            "pool",
            "air conditioning",
            "concierge"
        ]
    },
    {
        "@search.score": 1.1819803,
        "HotelName": "Twin Dome Motel",
        "Category": "Boutique",
        "Tags": [
            "pool",
            "free wifi",
            "concierge"
        ]
    },
    {
        "@search.score": 1.1773309,
        "HotelName": "Smile Hotel",
        "Category": "Suite",
        "Tags": [
            "view",
            "concierge",
            "laundry service"
        ]
    },
```

語句は直接サポートされていませんが、`search=Tags:landy~ AND sevic~` のように、複数部分から成る語句の各用語に対してあいまい一致を指定できます。  このクエリ式では、"laundry service" で 15 件の一致が見つかります。

> [!Note]
> あいまいクエリは[分析](search-lucene-query-architecture.md#stage-2-lexical-analysis)されません。 不完全な語句 (プレフィックス クエリ、ワイルドカードのクエリ、正規表現のクエリ、あいまいクエリ) でのクエリの種類は、解析ステージをバイパスして、クエリ ツリーに直接追加されます。 部分的なクエリ用語に対して適用される変換は、大文字から小文字への変換だけです。
>

## <a name="example-3-proximity-search"></a>例 3: 近接検索

近接検索では、ドキュメント内で近くにある語句を検索します。 言葉の終わりにチルダ記号 "~" を挿入し、近接境界となる語数を続けます。

このクエリでは、ドキュメント内で互いに 5 語以内にある "hotel" と "airport" という用語を検索します。 語句を保持するために引用符をエスケープ (`\"`) しています。

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "Description: \"hotel airport\"~5",
    "queryType": "full",
    "select": "HotelName, Description",
    "searchFields": "HotelName, Description",
    "count": true
}
```

このクエリの応答は、次の例のようになります。

```json
"@odata.count": 2,
"value": [
    {
        "@search.score": 0.6331726,
        "HotelName": "Trails End Motel",
        "Description": "Only 8 miles from Downtown.  On-site bar/restaurant, Free hot breakfast buffet, Free wireless internet, All non-smoking hotel. Only 15 miles from airport."
    },
    {
        "@search.score": 0.43032226,
        "HotelName": "Catfish Creek Fishing Cabins",
        "Description": "Brand new mattresses and pillows.  Free airport shuttle. Great hotel for your business needs. Comp WIFI, atrium lounge & restaurant, 1 mile from light rail."
    }
]
```

## <a name="example-4-term-boosting"></a>例 4: 用語ブースト

用語ブーストでは、指定用語を含む文書に含まない文書より高い順位が設定されます (ブーストされます)。 用語をブーストするには、キャレット記号 `^` とブースト係数 (数字) を、検索する用語の終わりに使用します。 ブースト係数の既定値は 1 で、正の値でなければなりませんが、1 未満 (例: 0.2) でも構いません。 用語ブーストはスコアリング プロファイルとは違います。スコアリング プロファイルは、特定の用語ではなく、特定のフィールドをブーストします。

この "ブースト前" クエリでは "beach access" を検索しており、一方または両方の用語に一致する 7 つのドキュメントがあることがわかります。

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "beach access",
    "queryType": "full",
    "select": "HotelName, Description, Tags",
    "searchFields": "HotelName, Description, Tags",
    "count": true
}
```

実際には、"access" に一致するドキュメントは 1 つだけですが、唯一の一致であるため、用語 "beach" がドキュメントに含まれていないにもかかわらず配置が高く (2 番目の位置に) なっています。

```json
"@odata.count": 7,
"value": [
    {
        "@search.score": 2.2723424,
        "HotelName": "Nova Hotel & Spa",
        "Description": "1 Mile from the airport.  Free WiFi, Outdoor Pool, Complimentary Airport Shuttle, 6 miles from the beach & 10 miles from downtown."
    },
    {
        "@search.score": 1.5507699,
        "HotelName": "Old Carrabelle Hotel",
        "Description": "Spacious rooms, glamorous suites and residences, rooftop pool, walking access to shopping, dining, entertainment and the city center."
    },
    {
        "@search.score": 1.5358944,
        "HotelName": "Whitefish Lodge & Suites",
        "Description": "Located on in the heart of the forest. Enjoy Warm Weather, Beach Club Services, Natural Hot Springs, Airport Shuttle."
    },
    {
        "@search.score": 1.3433652,
        "HotelName": "Ocean Air Motel",
        "Description": "Oceanfront hotel overlooking the beach features rooms with a private balcony and 2 indoor and outdoor pools. Various shops and art entertainment are on the boardwalk, just steps away."
    },
```

この "ブースト後" のクエリでは、検索を繰り返しますが、今回は、用語 "beach" に用語 "access" よりも高い優先順位を与えて結果をブーストします。 人が判読できるバージョンのクエリは `search=Description:beach^2 access` です。 クライアントによっては、`^2` は `%5E2` のように表現することが必要な場合があります。

用語 "beach" をブーストした後は、Old Carrabelle Hotel の一致は 6 番目にまで下がります。

<!-- Consider a scoring profile that boosts matches in a certain field, such as "genre" in a music app. Term boosting could be used to further boost certain search terms higher than others. For example, "rock^2 electronic" will boost documents that contain the search terms in the "genre" field higher than other searchable fields in the index. Furthermore, documents that contain the search term "rock" will be ranked higher than the other search term "electronic" as a result of the term boost value (2). -->

## <a name="example-5-regex"></a>例 5: 正規表現

正規表現検索では、スラッシュ "/" の間のコンテンツに基づいて一致が検索されます。[RegExp](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html) クラスに詳細があります。

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "HotelName:/(Mo|Ho)tel/",
    "queryType": "full",
    "select": "HotelName",
    "count": true
}
```

このクエリの応答は、次の例のようになります。

```json
    "@odata.count": 22,
    "value": [
        {
            "@search.score": 1.0,
            "HotelName": "Days Hotel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Triple Landscape Hotel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Smile Hotel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Pelham Hotel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Sublime Cliff Hotel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Twin Dome Motel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Nova Hotel & Spa"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Scarlet Harbor Hotel"
        },
```

> [!Note]
> 正規表現クエリは[分析](./search-lucene-query-architecture.md#stage-2-lexical-analysis)されません。 部分的なクエリ用語に対して適用される変換は、大文字から小文字への変換だけです。
>

## <a name="example-6-wildcard-search"></a>例 6: ワイルドカード検索

複数 (`*`) または単数 (`?`) の文字のワイルドカード検索で、一般に認識されている構文を使用できます。 Lucene Query Parser では、これらの文字を語句ではなく 1 つの用語に利用することにご注意ください。

このクエリでは、プレフィックス "sc" を含むホテル名を検索します。 検索の最初の文字に `*` や `?` の記号は使用できません。

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "HotelName:sc*",
    "queryType": "full",
    "select": "HotelName",
    "count": true
}
```

このクエリの応答は、次の例のようになります。

```json
    "@odata.count": 2,
    "value": [
        {
            "@search.score": 1.0,
            "HotelName": "Scarlet Harbor Hotel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Scottish Inn"
        }
    ]
```

> [!Note]
> ワイルドカード クエリは[分析](./search-lucene-query-architecture.md#stage-2-lexical-analysis)されません。 部分的なクエリ用語に対して適用される変換は、大文字から小文字への変換だけです。
>

## <a name="next-steps"></a>次のステップ

コードでクエリを指定してみてください。 次のリンクは、Azure SDK を使用して検索クエリを設定する方法について説明しています。

+ [.NET SDK を使用したインデックスのクエリ実行](search-get-started-dotnet.md)
+ [Python SDK を使用したインデックスのクエリ実行](search-get-started-python.md)
+ [JavaScript SDK を使用したインデックスのクエリ実行](search-get-started-javascript.md)

追加の構文リファレンス、クエリ アーキテクチャ、およびサンプルについては、次のリンク先を参照してください。

+ [高度なクエリを作成するための Lucene 構文のクエリの例](search-query-lucene-examples.md)
+ [Azure Cognitive Search でのフルテキスト検索のしくみ](search-lucene-query-architecture.md)
+ [単純なクエリ構文](query-simple-syntax.md)
+ [Full Lucene クエリ構文](query-lucene-syntax.md)
+ [フィルターの構文](search-query-odata-filter.md)