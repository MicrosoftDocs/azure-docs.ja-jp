---
title: 使用可能な web ページをページングする - Bing Custom Search
titleSuffix: Azure Cognitive Services
description: Bing Custom Search が返すことができるすべての Web ページをページングする方法を示します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: maheshb
ms.openlocfilehash: 13b4cef624c636b8935897338badf3349f27c7f5
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405045"
---
# <a name="paging-webpages"></a>Web ページのページング 

Custom Search API を呼び出すと、結果のリストが Bing から返されます。 このリストは、クエリに関連する可能性がある結果の総数のサブセットです。 利用可能な結果の推定総数を取得するには、回答オブジェクトの [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#totalestimatedmatches) フィールドにアクセスします。  
  
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
  
利用可能な Web ページをページングするには、[count](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#count) および [offset](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#offset) クエリ パラメーターを使用します。  
  
`count` パラメーターでは、応答で返される結果の数を指定します。 応答に要求できる結果の最大数は 50 です。 既定値は 10 です。 配信される実際の数は、要求した数よりも少ない可能性があります。

`offset` パラメーターでは、スキップする結果の数を指定します。 `offset` は 0 から始まり、(`totalEstimatedMatches` - `count`) 未満である必要があります。  
  
1 ページあたり 15 件の Web ページを表示する場合は、`count` を 15 に、`offset` を 0 に設定して、結果の最初のページを取得します。 後続のページごとに、`offset` を 15 ずつ増やします (たとえば、15、30)。  
  
オフセット 45 から始まる 15 件の Web ページを要求する例を次に示します。  
  
```  
GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?q=sailing+dinghies&count=15&offset=45&mkt=en-us&customConfig=123456 HTTP/1.1  
Ocp-Apim-Subscription-Key: <subscription ID>
Host: api.cognitive.microsoft.com  
```  

既定の `count` 値が実装で動作する場合、指定する必要があるのは `offset` クエリ パラメーターだけです。  
  
```  
GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?q=sailing+dinghies&offset=45&mkt=en-us&customConfig=123456 HTTP/1.1  
Ocp-Apim-Subscription-Key: <subscription ID>  
Host: api.cognitive.microsoft.com  
```  

> [!NOTE]
> `TotalEstimatedAnswers` フィールドは、現在のクエリで取得できる検索結果の合計数の見積もりです。  `count` パラメーターと `offset` パラメーターを設定すると、`TotalEstimatedAnswers` の数が変わる場合があります。 

