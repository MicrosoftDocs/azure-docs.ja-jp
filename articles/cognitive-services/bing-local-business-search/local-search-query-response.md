---
title: Bing Local Business Search API クエリと応答の送信と使用
titleSuffix: Azure Cognitive Services
description: この記事を使用して、Bing Local Business Search API で検索クエリを送信および使用する方法について学習します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: quickstart
ms.date: 06/26/2018
ms.author: rosh
ms.openlocfilehash: 5a26324f22ed46d0c06ecb0eb7ecaf74f589051e
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423445"
---
# <a name="sending-and-using-bing-local-business-search-api-queries-and-responses"></a>Bing Local Business Search API クエリと応答の送信と使用

Bing Local Business Search API から、検索クエリをそのエンドポイントに送信し、必須の `Ocp-Apim-Subscription-Key` ヘッダーを含めることで、ローカルの結果を得ることができます。 使用可能な[ヘッダー](local-search-reference.md#headers)と[パラメーター](local-search-reference.md#query-parameters)と共に、検索対象の領域の[地理的境界](specify-geographic-search.md)と、返される場所の[カテゴリ](local-search-query-response.md)を指定して、検索をカスタマイズすることができます。

## <a name="creating-a-request"></a>要求の作成

Bing Local Business Search API に要求を送信するには、検索語句を `q=` パラメーターに追加してから、それを API エンドポイントに追加して、`Ocp-Apim-Subscription-Key` ヘッダーを含めます。 例:

`https://api.cognitive.microsoft.com/bing/localbusinesses/v7.0/search?q=restaurant+in+Bellevue`

完全な要求 URL の構文を以下に示します。 要求の送信について詳しくは、Bing Local Business Search API の[クイック スタート](quickstarts/local-quickstart.md)、および[ヘッダー](local-search-reference.md#headers)と[パラメーター](local-search-reference.md#query-parameters)のリファレンス コンテンツを参照してください。 

ローカル検索カテゴリについては、「[Search categories for the Bing Local Business Search API](local-categories.md)」 (Bing Local Business Search API の検索カテゴリ) を参照してください。

```
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search[?q][&localCategories][&cc][&mkt][&safesearch][&setlang][&count][&first][&localCircularView][&localMapView]
```

## <a name="using-responses"></a>応答の使用

Bing Local Business Search API からの JSON 応答には、`SearchResponse` オブジェクトが含まれます。 API では、`places` フィールドに関連する検索結果が返されます。 結果が見つからない場合、`places` フィールドは応答に含まれません。

[!INCLUDE [cognitive-services-bing-url-note](../../../includes/cognitive-services-bing-url-note.md)]

```
{
   "_type": "SearchResponse",
   "queryContext": {
      "originalQuery": "restaurant in Bellevue"
   },
   "places": {
      "totalEstimatedMatches": 10,
. . . 
```

### <a name="search-result-attributes"></a>検索結果の属性

API によって返される JSON の結果には、次の属性が含まれます。

* _type
* address
* entityPresentationInfo
* geo
* id
* 名前
* routeablePoint
* telephone
* url

ヘッダー、パラメーター、市場コード、応答オブジェクト、エラーなどの一般的な情報については、[Bing Local Search API v7](local-search-reference.md) のリファレンスをご覧ください。

> [!NOTE]
> お客様およびお客様の代理を務める第三者のいずれも、Local Search API から取得したデータを Microsoft 以外のサービスまたは機能のテスト、開発、トレーニング、頒布、または提供を目的として利用、保持、保存、キャッシュ、共有、または配布することは認められません。 


## <a name="example-json-response"></a>JSON の応答例

次の JSON 応答には、クエリ `?q=restaurant+in+Bellevue` で指定された検索結果が含まれています。

```json
Vary: Accept-Encoding
BingAPIs-TraceId: 5376FFEB65294E24BB9F91AD70545826
BingAPIs-SessionId: 06ED7CEC80F746AA892EDAAC97CB0CB4
X-MSEdge-ClientID: 112C391E72C0624204153594738C63DE
X-MSAPI-UserState: aeab
BingAPIs-Market: en-US
X-Search-ResponseInfo: InternalResponseTime=659,MSDatacenter=CO4
X-MSEdge-Ref: Ref A: 5376FFEB65294E24BB9F91AD70545826 Ref B: BY3EDGE0306 Ref C: 2018-10-16T16:26:15Z
apim-request-id: fe54f585-7c54-4bf5-8b92-b9bede2b710a
Strict-Transport-Security: max-age=31536000; includeSubDomains; preload
x-content-type-options: nosniff
Cache-Control: max-age=0, private
Date: Tue, 16 Oct 2018 16:26:15 GMT
P3P: CP="NON UNI COM NAV STA LOC CURa DEVa PSAa PSDa OUR IND"
Content-Length: 978
Content-Type: application/json; charset=utf-8
Expires: Tue, 16 Oct 2018 16:25:15 GMT

{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "restaurant Bellevue"
  },
  "places": {
    "totalEstimatedMatches": 50,
    "value": [{
      "_type": "LocalBusiness",
      "id": "https:\/\/cognitivegblppe.azure-api.net\/api\/v7\/#Places.0",
      "name": "Facing East Taiwanese Restaurant",
      "url": "http:\/\/litadesign.wix.com\/facingeastrestaurant",
      "entityPresentationInfo": {
        "entityScenario": "ListItem",
        "entityTypeHints": ["Place", "LocalBusiness", "Restaurant"]
      },
      "geo": {
        "latitude": 47.6199188232422,
        "longitude": -122.202796936035
      },
      "routablePoint": {
        "latitude": 47.6199188232422,
        "longitude": -122.201713562012
      },
      "address": {
        "streetAddress": "1075 Bellevue Way NE Ste B2",
        "addressLocality": "Bellevue",
        "addressRegion": "WA",
        "postalCode": "98004",
        "addressCountry": "US",
        "neighborhood": "Bellevue",
        "text": "1075 Bellevue Way NE Ste B2, Bellevue, WA 98004"
      },
      "telephone": "(425) 688-2986"
    }],
    "searchAction": {
      "location": [{
        "name": "Bellevue, Washington"
      }],
      "query": "restaurant"
    }
  }
}
 
```

## <a name="throttling-requests"></a>スロットル リクエスト

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]


## <a name="next-steps"></a>次の手順
- [Local Business Search のクイック スタート](quickstarts/local-quickstart.md)
- [Local Business Search (Java) のクイック スタート](quickstarts/local-search-java-quickstart.md)
- [Local Business Search (Node.js) のクイック スタート](quickstarts/local-search-node-quickstart.md)
- [Local Business Search (Python) のクイック スタート](quickstarts/local-search-python-quickstart.md)
