---
title: 利用可能な動画をページングする方法 - Bing Video Search
titleSuffix: Azure Cognitive Services
description: Bing Video Search API によって返される動画をページングする方法について説明します。
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: scottwhi
ms.openlocfilehash: c3570d8772734595c6707ca8103006867a8eb47a
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/25/2019
ms.locfileid: "68500721"
---
# <a name="paging-through-video-search-results"></a>動画検索結果のページング

Bing Video Search API は、クエリに対して見つかったすべての検索結果のサブセットを返します。 後続の API 呼び出しで検索結果をページングすることで、結果を取得し、アプリケーションで表示できます。

> [!NOTE]
> ページングは、動画検索 (/videos/search) にのみ適用され、動画の分析情報 (/videos/details) や急上昇中の動画 (/videos/trending) には適用されません。

## <a name="total-estimated-matches"></a>推定一致数の合計

見つかった検索結果の推定数を取得するには、JSON 応答の [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos-totalestimatedmatches) フィールドを使用します。   
  
```json  
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D56...",
    "totalEstimatedMatches" : 1000,
    "value" : [...]
}  
```  
  
## <a name="paging-through-videos"></a>動画のページング

利用可能な動画をページングするには、要求の送信時にクエリ パラメーターとして [count](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#count) と [offset](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#offset) を使用します。  
  

|パラメーター  |説明  |
|---------|---------|
|`count`     | 応答で返される結果の数を指定します。 応答に要求できる結果の最大数は 100 です。 既定値は 10 です。 配信される実際の数は、要求した数よりも少ない可能性があります。        |
|`offset`     | スキップする結果の数を指定します。 `offset` は 0 から始まり、(`totalEstimatedMatches` - `count`) 未満である必要があります。          |

たとえば、1 ページあたり 20 件の記事を表示する場合は、`count` を 20 に、`offset` を 0 に設定して、結果の最初のページを取得します。 後続のページごとに、`offset` を 20 ずつ増やします (たとえば、20、40)。  
  
次の例は、オフセット 40 から始まる 20 件の動画を要求します。  
  
```cURL  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&count=20&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

[count](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#count) に既定値を使用する場合、次の例のように、`offset` クエリ パラメーターのみを指定する必要があります。  
  
```cURL  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

一度に 35 個の動画をページングする場合は、最初の要求で `offset` クエリ パラメーターを 0 に設定し、後続の要求ごとに `offset` を 35 ずつ増やします。 ただし、一部の動画結果が次の応答と前の応答で重複する場合があります。 たとえば、応答内の最初の 2 つの動画が、前の応答の最後の 2 つの動画と同じである場合があります。

重複する結果を排除するには、`Videos` オブジェクトの [nextOffset](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos-nextoffset) フィールドを使用します。

たとえば、一度に 30 個の動画をページングする場合は、最初の要求で `count` を 30 に、`offset` を 0 に設定できます。 次の要求で、`offset` クエリ パラメーターを `nextOffset` 値に設定します。

> [!NOTE]
> `TotalEstimatedAnswers` フィールドは、現在のクエリで取得できる検索結果の合計数の見積もりです。  `count` パラメーターと `offset` パラメーターを設定すると、`TotalEstimatedAnswers` の数が変わる場合があります。 
  
## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [ビデオの分析情報の取得](video-insights.md)
