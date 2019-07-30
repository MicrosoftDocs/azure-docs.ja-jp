---
title: Bing News Search の結果をページングする方法
titleSuffix: Azure Cognitive Services
description: Bing News Search API が返すニュース記事をページングする方法について学習します。
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: scottwhi
ms.openlocfilehash: e5f8dce1a03e44758eea737ad2da419fa67c36a2
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423747"
---
# <a name="how-to-page-through-news-search-results"></a>ニュース検索結果をページングする方法

News Search API を呼び出すと、クエリに関連する結果のリストが Bing から返されます。 利用可能な結果の推定総数を取得するには、回答オブジェクトの [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#news-totalmatches) フィールドにアクセスします。  
  
次の例は、ニュース回答に含まれる `totalEstimatedMatches` フィールドを示しています。  

```json
{  
    "_type" : "News",  
    "readLink" : "https:\/\/api.cognitive.microsoft.com\/bing\/v7\/news\/search?q=sailing+dinghies",  
    "totalEstimatedMatches" : 88400,  
    "value" : [...]  
}  
```  
  
利用可能な記事をページングするには、[count](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#count) および [offset](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#offset) クエリ パラメーターを使用します。  
 

|パラメーター  |説明  |
|---------|---------|
|`count`     | 応答で返される結果の数を指定します。 応答に要求できる結果の最大数は 100 です。 既定値は 10 です。 配信される実際の数は、要求した数よりも少ない可能性があります。        |
|`offset`     | スキップする結果の数を指定します。 `offset` は 0 から始まり、(`totalEstimatedMatches` - `count`) 未満である必要があります。          |

たとえば、1 ページあたり 20 件の記事を表示する場合は、`count` を 20 に、`offset` を 0 に設定して、結果の最初のページを取得します。 後続のページごとに、`offset` を 20 ずつ増やします (たとえば、20、40)。  
  
次の例は、オフセット 40 から始まる 20 件のニュース記事を要求します。  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies&count=20&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  
  
既定値 `count` が実装に対して動作する場合は、次の例で示すように、`offset` クエリ パラメーターのみを指定します。  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

> [!NOTE]
> ページングはニュース検索 (/news/search) のみに適用され、トレンドのトピック (/news/trendingtopics) またはニュース (/news) のカテゴリには適用されません。

> [!NOTE]
> `TotalEstimatedAnswers` フィールドは、現在のクエリで取得できる検索結果の合計数の見積もりです。  `count` パラメーターと `offset` パラメーターを設定すると、`TotalEstimatedAnswers` の数が変わる場合があります。 
