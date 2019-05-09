---
title: Bing Image Search API で注目の画像を取得する
titleSuffix: Azure Cognitive Services
description: Bing Image Search API で Web から今日の注目の画像を検索します。
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: EAB92D35-5C0B-4A0A-8F49-02DF7FAD44B4
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: scottwhi
ms.custom: seodec2018
ms.openlocfilehash: 024e007a34f71256d5cd148720412a0c57bb74ed
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2019
ms.locfileid: "57342226"
---
# <a name="get-trending-images-from-the-web"></a>Web から注目の画像を取得する

今日の注目の画像を取得するには、次の GET 要求を送信します。  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/trending?mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Trending Images API では、現在、次の市場のみがサポートされています。  

- en-US (英語、米国)  
- en-CA (英語、カナダ)  
- en-AU (英語、オーストラリア)  
- zh-CN (中国語、中国)

応答には、画像をカテゴリ別に一覧表示する [TrendingImages](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#trendingimages) オブジェクトが含まれています。 カテゴリの `title` を使用して、ユーザー エクスペリエンスの画像をグループ化します。 カテゴリは、毎日変わる可能性があります。  

```json
{
    "_type" : "TrendingImages",  
    "categories" : [{  
        "title" : "Popular people searches",  
        "tiles" : [{  
            "query" : {  
                "text" : "Smith",  
                "displayText" : "Mr. Smith",  
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?q=smith&FORM=..."
            },  
            "image" : {  
                "id" : "C3C60AE779A054D5CD80D3CACF0F3",  
                "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?id=OIP.M2532...",  
                "contentUrl" : "http:\/\/www.contoso.com.au\/assets\/Uploads\/smith-SH01.jpg",  
                "thumbnail" : {  
                    "width" : 288,  
                    "height" : 300  
                }  
            }  
        },  
        . . .  
        ]  
    },  
    . . .  
    {  
        "title" : "Popular Halloween searches",  
        "tiles" : [{  
            "query" : {  
                "text" : "Halloween costumes for adults",  
                "displayText" : "Halloween costumes for adults",  
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?q=Halloween+costumes..."
            },  
            "image" : {  
                "id" : "0F3395F2983003F89DCEE711B55D7FA53E4",  
                "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OIP.Me429c...",  
                "contentUrl" : "http:\/\/images.domain.com\/products\/8179\/1-1\/adult-squirrel...",  
                "thumbnail" : {  
                    "width" : 336,  
                    "height" : 480  
                }  
            }  
        }]  
    }]  
}  
```  

各タイルには、画像と、関連画像を取得するオプションが含まれています。 関連画像を取得するには、クエリ `text` を使用して [Image Search API](./search-the-web.md) を呼び出し、関連画像を自分で表示します。 または、`webSearchUrl` 内の URL を使用して、関連画像を含む Bing の画像検索結果ページに移動することもできます。

Image Search API を呼び出して関連画像を取得する場合は、[id](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#id) クエリ パラメーターを `id` フィールド内の ID に設定します。 ID を指定すると、応答に画像 (応答内の最初の画像) とその関連画像が含まれます。 また、[q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference) クエリ パラメーターを `query` オブジェクトの `text` フィールドのテキストに設定します。

次の例は、画像 ID を使用して、前の Trending Images API の応答の Mr. Smith の関連画像を取得する方法を示します。

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=Smith&id=77FDE4A1C6529A23C7CF0EC073FAA64843E828F2&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  
