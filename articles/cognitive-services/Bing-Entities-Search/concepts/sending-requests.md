---
title: Bing Entity Search API への検索要求の送信
titleSuffix: Azure cognitive Services
description: Bing Entity Search API は、Bing に検索クエリを送信して、エンティティと場所を含む検索結果を取得します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: aahi
ms.openlocfilehash: ad6d89fec9f2c94129e19c09ee3e1e76d5bb6e44
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96353274"
---
# <a name="sending-search-requests-to-the-bing-entity-search-api"></a>Bing Entity Search API への検索要求の送信

> [!WARNING]
> Bing Search API は、Cognitive Services から Bing Search Services に移行されます。 **2020 年 10 月 30 日** 以降、Bing Search の新しいインスタンスは、[こちら](/bing/search-apis/bing-web-search/create-bing-search-service-resource)に記載されているプロセスに従ってプロビジョニングする必要があります。
> Cognitive Services を使用してプロビジョニングされた Bing Search API は、次の 3 年間、または Enterprise Agreement の終わり (どちらか先に発生した方) までサポートされます。
> 移行手順については、[Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) に関するページを参照してください。

Bing Entity Search API は、Bing に検索クエリを送信して、エンティティと場所を含む検索結果を取得します。 場所の結果には、レストラン、ホテルやその他の地元企業が含まれます。 場所の場合、クエリではローカル ビジネスの名前を指定したり、一覧 (たとえば、近くのレストラン) を尋ねたりできます。 エンティティの結果には、人、場所、または物が含まれます。 このコンテキストでの場所とは、観光名所、州、国/地域などです。

## <a name="the-endpoint"></a>エンドポイント

エンティティおよび場所検索の結果を取得するには、次のエンドポイントに GET 要求を送信します。  

```
https://api.cognitive.microsoft.com/bing/v7.0/entities
```

要求では、HTTPS プロトコルを使用する必要があります。

すべての要求をサーバーから送信することをお勧めします。 クライアント アプリケーションの一部としてキーを配布すると、悪意のあるサード パーティがアクセスする可能性が高くなります。 また、サーバーから呼び出しを行うと、API の将来のバージョンでアップグレードする場所が 1 つで済みます。

## <a name="specifying-query-parameters-and-headers"></a>クエリ パラメーターとヘッダーの指定

要求では、ユーザーの検索語句が含まれている [q](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#query) クエリ パラメーターを指定する必要があります。 要求では [mkt](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#mkt) クエリ パラメーターも指定する必要があります。このパラメーターは、結果取得元の市場を示します。 省略可能なクエリ パラメーターの一覧については、「[Query Parameters (クエリ パラメーター)](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#query-parameters)」を参照してください。 すべてのクエリ パラメーターを URL エンコードします。  
  
要求では、[Ocp-Apim-Subscription-Key](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#subscriptionkey) ヘッダーを指定する必要があります。 省略可能ですが、次のヘッダーも指定することをお勧めします。  
  
-   [User-Agent](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#useragent)  
-   [X-MSEdge-ClientID](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#clientid)  
-   [X-MSEdge-ClientIP](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#clientip)  
-   [X-Search-Location](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#location)  

クライアント IP と場所のヘッダーは、場所に対応したコンテンツを返すために重要です。  

すべての要求ヘッダーと応答ヘッダーのリストについては、「[Headers](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#headers)」(ヘッダー) を参照してください。

## <a name="the-request"></a>要求

すべての推奨されるクエリ パラメーターとヘッダーを含むエンティティ要求を次に示します。 

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/entities?q=mount+rainier&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

いずれかの Bing API を初めて呼び出す場合は、クライアント ID ヘッダーを含めないでください。 クライアント ID を含めるのは、過去に Bing API を呼び出したことがあり、かつユーザーとデバイスの組み合わせに対応するクライアント ID が Bing から返されたことがある場合だけです。

## <a name="the-response"></a>応答

前述の要求への応答は次のようになります。 例では、Bing に固有の応答ヘッダーも示されています。 応答オブジェクトについては、「[SearchResponse](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#searchresponse)」をご覧ください。

[!INCLUDE [cognitive-services-bing-url-note](../../../../includes/cognitive-services-bing-url-note.md)]

```json
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "mount rainier"
    },
    "entities" : {
        "queryScenario" : "DominantEntity",
        "value" : [{
            "contractualRules" : [{
                "_type" : "ContractualRules\/LicenseAttribution",
                "targetPropertyName" : "description",
                "mustBeCloseToContent" : true,
                "license" : {
                    "name" : "CC-BY-SA",
                    "url" : "http:\/\/creativecommons.org\/licenses\/by-sa\/3.0\/"
                },
                "licenseNotice" : "Text under CC-BY-SA license"
            },
            {
                "_type" : "ContractualRules\/LinkAttribution",
                "targetPropertyName" : "description",
                "mustBeCloseToContent" : true,
                "text" : "en.wikipedia.org",
                "url" : "http:\/\/en.wikipedia.org\/wiki\/Mount_Rainier"
            },
            {
                "_type" : "ContractualRules\/MediaAttribution",
                "targetPropertyName" : "image",
                "mustBeCloseToContent" : true,
                "url" : "http:\/\/en.wikipedia.org\/wiki\/Mount_Rainier"
            }],
            "webSearchUrl" : "https:\/\/www.bing.com\/search?q=Mount%20Rainier...",
            "name" : "Mount Rainier",
            "image" : {
                "name" : "Mount Rainier",
                "thumbnailUrl" : "https:\/\/www.bing.com\/th?id=A21890c0e1f...",
                "provider" : [{
                    "_type" : "Organization",
                    "url" : "http:\/\/en.wikipedia.org\/wiki\/Mount_Rainier"
                }],
                "hostPageUrl" : "http:\/\/upload.wikimedia.org\/wikipedia...",
                "width" : 110,
                "height" : 110
            },
            "description" : "Mount Rainier, Mount Tacoma, or Mount Tahoma is the highest...",
            "entityPresentationInfo" : {
                "entityScenario" : "DominantEntity",
                "entityTypeHints" : ["Attraction"],
                "entityTypeDisplayHint" : "Mountain"
            },
            "bingId" : "9ae3e6ca-81ea-6fa1-ffa0-42e1d78906"
        }]
    }
}
```


## <a name="next-steps"></a>次のステップ

* [Bing Entity API でのエンティティの検索](search-for-entities.md)
* [Bing API の使用と表示の要件](../../bing-web-search/use-display-requirements.md)