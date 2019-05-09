---
title: 検索結果をページングする方法 - Bing Web Search API
titleSuffix: Azure Cognitive Services
description: Bing Web Search API から検索結果をページングする方法について説明します。
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 26CA595B-0866-43E8-93A2-F2B5E09D1F3B
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 08/20/2018
ms.author: aahi
ms.openlocfilehash: 68b99e7f0763f46c705a1ff2ba3a0e90216429c2
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2019
ms.locfileid: "55878597"
---
# <a name="how-to-page-through-results-from-the-bing-web-search-api"></a>Bing Web Search API から結果をページングする方法

Web Search API を呼び出すと、結果のリストが Bing から返されます。 このリストは、クエリに関連する可能性がある結果の総数のサブセットです。 利用可能な結果の推定総数を取得するには、回答オブジェクトの [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference) フィールドにアクセスします。  

次の例は、Web 回答に含まれる `totalEstimatedMatches` フィールドを示しています。  

```
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43CA...",
        "totalEstimatedMatches" : 262000,
        "value" : [...]
    }
}  
```

利用可能な Web ページをページングするには、[count](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#count) および [offset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#offset) クエリ パラメーターを使用します。  

`count` パラメーターでは、応答で返される結果の数を指定します。 応答に要求できる結果の最大数は 50 です。 既定値は 10 です。 配信される実際の数は、要求した数よりも少ない可能性があります。

`offset` パラメーターでは、スキップする結果の数を指定します。 `offset` は 0 から始まり、(`totalEstimatedMatches` - `count`) 未満である必要があります。  

1 ページあたり 15 件の Web ページを表示する場合は、`count` を 15 に、`offset` を 0 に設定して、結果の最初のページを取得します。 後続のページごとに、`offset` を 15 ずつ増やします (たとえば、15、30)。  

次の例は、オフセット 45 から始まる 15 件の Web ページを要求します。  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&count=15&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

既定の `count` 値が実装で動作する場合、指定する必要があるのは `offset` クエリ パラメーターだけです。  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

Web Search API は、Web ページを含み、画像、動画、ニュースを含む可能性のある結果を返します。 検索結果をページングする場合、[WebAnswer](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#webanswer) 回答をページングし、画像やニュースなどの他の回答はページングしません。 たとえば、`count` を 50 に設定した場合、50 件の Web ページ結果が返されますが、応答には、他の回答の結果も含まれる場合があります。 たとえば、応答には、15 個の画像と 4 つのニュース記事が含まれる場合があります。 結果に最初のページのニュースが含まれ、2 ページのニュースが含まれない場合、またはその逆の場合もあります。   

`responseFilter` クエリ パラメーターを指定し、フィルターの一覧に Web ページを含めない場合は、`count` および `offset` パラメーターを使用しないでください。 

> [!NOTE]
> `TotalEstimatedAnswers` フィールドは、現在のクエリで取得できる検索結果の合計数の見積もりです。  `count` パラメーターと `offset` パラメーターを設定すると、`TotalEstimatedAnswers` の数が変わる場合があります。 
