---
title: "REST API を使用した Azure Search インデックスの照会 | Microsoft Docs"
description: "Azure Search の検索クエリを作成し、検索パラメーターを使用して検索結果のフィルター処理と並べ替えを行います。"
services: search
documentationcenter: 
manager: jhubbard
author: ashmaka
ms.assetid: 8b3ca890-2f5f-44b6-a140-6cb676fc2c9c
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 01/12/2017
ms.author: ashmaka
translationtype: Human Translation
ms.sourcegitcommit: 7d45759915f38ba4337b745eb2b28dcbc72dbbe0
ms.openlocfilehash: 449110cfda1a08b73b5e21cbf495e59f32d80339

---

# <a name="query-your-azure-search-index-using-the-rest-api"></a>REST API を使用した Azure Search インデックスの照会
> [!div class="op_single_selector"]
>
> * [概要](search-query-overview.md)
> * [ポータル](search-explorer.md)
> * [.NET](search-query-dotnet.md)
> * [REST ()](search-query-rest-api.md)
>
>

この記事では、[Azure Search REST API](https://docs.microsoft.com/rest/api/searchservice/) を使用してインデックスを照会する方法について説明します。

このチュートリアルを開始する前に、既に [Azure Search インデックスを作成](search-what-is-an-index.md)し、[インデックスにデータを読み込んで](search-what-is-data-import.md)います。

## <a name="identify-your-azure-search-services-query-api-key"></a>Azure Search サービスのクエリ API キーの識別
Azure Search REST API に対するすべての検索操作で鍵となるコンポーネントは、プロビジョニングしたサービスに対して生成された *API キー* です。 有効なキーがあれば、要求を送信するアプリケーションとそれを処理するサービスの間で、要求ごとに信頼を確立できます。

1. サービスの API キーを探すには、[Azure Portal](https://portal.azure.com/) にサインインします。
2. Azure Search サービスのブレードに移動します。
3. "キー" アイコンをクリックします。

サービスで*管理者キー*と*クエリ キー*を使用できるようになります。

* プライマリおよびセカンダリ *管理者キー* は、サービスの管理のほか、インデックス、インデクサー、データ ソースの作成と削除など、すべての操作に対する完全な権限を付与するものです。 キーは 2 つあるため、プライマリ キーを再生成することにした場合もセカンダリ キーを使い続けることができます (その逆も可能です)。
* *クエリ キー* はインデックスとドキュメントに対する読み取り専用アクセスを付与するものであり、通常は、検索要求を発行するクライアント アプリケーションに配布されます。

インデックスを照会する目的では、いずれかのクエリ キーを使用できます。 クエリには管理者キーを使うこともできますが、アプリケーション コードではクエリ キーを使うようにしてください。この方が、[最少権限の原則](https://en.wikipedia.org/wiki/Principle_of_least_privilege)に適っています。

## <a name="formulate-your-query"></a>クエリの作成
[REST API を使用してインデックスを検索する](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)方法は 2 とおりあります。 その 1 つは、要求本文の JSON オブジェクトにクエリ パラメーターが定義された HTTP POST 要求を発行する方法です。 もう 1 つは、要求 URL にクエリ パラメーターが定義された HTTP GET 要求を発行する方法です。 POST の方が GET よりもクエリ パラメーターのサイズの[制限が緩やか](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)です。 そのため、GET の方が便利である特殊な状況を除いて、POST を使用することをお勧めします。

POST でも GET でも、*サービス名*、*インデックス名*、適切な *API バージョン* (このドキュメントが書かれた時点で最新の API バージョンは `2016-09-01`) を要求 URL に指定する必要があります。 GET の場合は、URL の末尾の*クエリ文字列*でクエリ パラメーターを指定します。 この URL の形式については、以下を参照してください。

    https://[service name].search.windows.net/indexes/[index name]/docs?[query string]&api-version=2016-09-01

POST の形式も同じですが、クエリ文字列のパラメーターで api-version だけを指定します。

#### <a name="example-queries"></a>クエリの例
"hotels" という名前のインデックスに対するクエリの例をいくつか紹介します。 これらのクエリは、GET と POST の両方の形式で示します。

次のクエリは、インデックス全体で "budget" という単語を探し、`hotelName` フィールドのみを返します。

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=budget&$select=hotelName&api-version=2016-09-01

POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2016-09-01
{
    "search": "budget",
    "select": "hotelName"
}
```

次のクエリは、フィルターをインデックスに適用して 1 泊 150 ドル未満のホテルを探し、`hotelId` と `description` を返します。

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&$filter=baseRate lt 150&$select=hotelId,description&api-version=2016-09-01

POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2016-09-01
{
    "search": "*",
    "filter": "baseRate lt 150",
    "select": "hotelId,description"
}
```

次のクエリは、インデックス全体を検索し、特定のフィールド (`lastRenovationDate`) の降順で並べ替えます。そして上位 2 件の結果を取得し、`hotelName` と `lastRenovationDate` のみを表示します。

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&$top=2&$orderby=lastRenovationDate desc&$select=hotelName,lastRenovationDate&api-version=2016-09-01

POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2016-09-01
{
    "search": "*",
    "orderby": "lastRenovationDate desc",
    "select": "hotelName,lastRenovationDate",
    "top": 2
}
```

## <a name="submit-your-http-request"></a>HTTP 要求の送信
これで、HTTP 要求 の URL (GET の場合) または本文 (POST の場合) の一部としてクエリを作成したので、要求ヘッダーを定義し、クエリを送信できます。

#### <a name="request-and-request-headers"></a>要求と要求ヘッダー
GET の場合は 2 つ、POST の場合は 3 つ、要求ヘッダーを定義する必要があります。

1. `api-key` ヘッダーは、上記のステップ I で特定したクエリ キーに設定する必要があります。 `api-key` ヘッダーとして管理者キーを使うこともできますが、クエリ キーを使うことをお勧めします。クエリ キーなら、インデックスとドキュメントへの読み取り専用アクセスを排他的に付与できるためです。
2. `Accept` ヘッダーを `application/json` に設定する必要があります。
3. POST の場合のみ、`Content-Type` ヘッダーも `application/json` に設定する必要があります。

Azure Search REST API を使用して "hotels" インデックスを検索するための HTTP GET 要求を以下に示します。ここでは、"motel" という単語を検索するシンプルなクエリを使っています。

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=motel&api-version=2016-09-01
Accept: application/json
api-key: [query key]
```

これも同じクエリの例ですが、今回は HTTP POST を使っています。

```
POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2016-09-01
Content-Type: application/json
Accept: application/json
api-key: [query key]

{
    "search": "motel"
}
```

クエリ要求が成功すると状態コード `200 OK` が返され、検索結果は応答本文で JSON として返されます。 上記のクエリの結果がどのような形になるかを以下に示します。ここでは、"hotels" インデックスに「[REST API を使用した Azure Search へのデータのインポート](search-import-data-rest-api.md)」のサンプル データが読み込まれているものとします (この JSON はわかりやすく整形されています)。

```JSON
{
    "value": [
        {
            "@search.score": 0.59600675,
            "hotelId": "2",
            "baseRate": 79.99,
            "description": "Cheapest hotel in town",
            "description_fr": "Hôtel le moins cher en ville",
            "hotelName": "Roach Motel",
            "category": "Budget",
            "tags":["motel", "budget"],
            "parkingIncluded": true,
            "smokingAllowed": true,
            "lastRenovationDate": "1982-04-28T00:00:00Z",
            "rating": 1,
            "location": {
                "type": "Point",
                "coordinates": [-122.131577, 49.678581],
                "crs": {
                    "type":"name",
                    "properties": {
                        "name": "EPSG:4326"
                    }
                }
            }
        }
    ]
}
```

詳細については、「 [Search Documents (Azure Search Service REST API) (ドキュメントの検索 (Azure Search Service REST API))](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)」の「Response (応答)」セクションを参照してください。 エラーが発生した場合に返される可能性のあるその他の HTTP 状態コードの詳細については、「 [HTTP status codes (Azure Search) (HTTP 状態コード (Azure Search))](https://docs.microsoft.com/rest/api/searchservice/HTTP-status-codes)」を参照してください。



<!--HONumber=Feb17_HO3-->


