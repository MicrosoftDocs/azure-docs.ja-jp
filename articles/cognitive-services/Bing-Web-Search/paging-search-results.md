---
title: 検索結果をページングする方法 - Bing Search API
titleSuffix: Azure Cognitive Services
description: Bing Search API から検索結果をページングする方法について説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 26CA595B-0866-43E8-93A2-F2B5E09D1F3B
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: aahi
ms.openlocfilehash: 9fc05ab42c75bac1f8e192dd4fe20bb142881479
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2019
ms.locfileid: "72176903"
---
# <a name="how-to-page-through-results-from-the-bing-search-apis"></a>Bing Search API から結果をページングする方法

Bing Web、Custom、Image、News または Video Search API の呼び出しを送信すると、Bing からは、クエリに関連する可能性のある結果の合計数のサブセットが返されます。 利用可能な結果の推定総数を取得するには、回答オブジェクトの `totalEstimatedMatches` フィールドにアクセスします。 

例: 

```json
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43CA...",
        "totalEstimatedMatches" : 262000,
        "value" : [...]
    }
}  
```

## <a name="paging-through-search-results"></a>検索結果をページングする

利用可能な動画をページングするには、要求の送信時にクエリ パラメーター `count` と `offset` を使用します。  

> [!NOTE]
>
> * Bing Video、Image、News API によるページングは、一般的な動画 (`/video/search`)、ニュース (`/news/search`)、画像 (`/image/search`) の検索にのみ適用されます。 流行のトピックとカテゴリによるページングはサポートされていません。  
> * `TotalEstimatedMatches` フィールドは、現在のクエリに対する検索結果の合計数の見積もりです。 `count` パラメーターと `offset` パラメーターを設定すると、この見積もりが変わることがあります。

| パラメーター | 説明                                                                                                                                                                |
|-----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `count`   | 応答で返される結果の数を指定します。 `count` の既定値と、要求できる結果の最大数は API によって異なることに注意してください。 これらの値については、「[次の手順](#next-steps)」の下にあるリファレンス ドキュメントを参照してください。 |
| `offset`  | スキップする結果の数を指定します。 `offset` は 0 から始まり、(`totalEstimatedMatches` - `count`) 未満である必要があります。                                           |

たとえば、1 ページあたり 15 件の結果を表示する場合、`count` を 15 に、`offset` を 0 に設定し、結果の最初のページを取得します。 後続の API 呼び出しごとに、`offset` を 15 ずつ増やします。 次の例は、オフセット 45 から始まる 15 件の Web ページを要求します。

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&count=15&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

既定の `count` 値を使用する場合、API 呼び出しで指定する必要があるのは `offset` クエリ パラメーターだけです。  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

> [!NOTE]
> Bing Web Search API から返される結果には、Web ページ、画像、動画、ニュースが含まれます。 Bing Web Search API からの検索結果をページングするとき、[Web ページ](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webpage)のみがページングされ、画像やニュースなど、他の種類の応答はページングされません。 `WebPage` オブジェクトの検索結果には、他の種類の応答にも表示される結果が含まれることがあります。
>
> フィルター値を指定せずに `responseFilter` クエリ パラメーターを使用する場合、`count` パラメーターと `offset` パラメーターを使用しないでください。 

## <a name="next-steps"></a>次の手順

* [Bing Web Search API とは](bing-api-comparison.md)
* [Bing Web Search API v7 リファレンス](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)
* [Bing Custom Search API v7 リファレンス](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference)
* [Bing News Search API v7 リファレンス](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference)
* [Bing Video Search API v7 リファレンス](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference)
* [Bing Image Search API v7 リファレンス](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)