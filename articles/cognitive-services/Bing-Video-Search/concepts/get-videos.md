---
title: Bing Video Search API への検索要求の送信
titleSuffix: Azure Cognitive Services
description: Bing Video Search API に検索クエリを送信する方法について説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: overview
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: f1d433dd7d919aa5d11f2f59b74a5e3f2dca0b59
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/25/2019
ms.locfileid: "68500296"
---
# <a name="search-for-videos-with-the-bing-video-search-api"></a>Bing Video Search API で動画を検索する

Bing Video Search API を使用すると、Bing のコグニティブ ニュース検索機能を簡単にアプリケーションに統合することができます。 この API は主に、関連する動画を Web から検索して返すものですが、同時に、インテリジェントで的を絞った動画検索を Web 上で行うための機能をいくつか備えています。

## <a name="getting-videos"></a>動画の取得

ユーザーの検索語句に関連した動画を Web から取得するには、次の GET 要求を送信します。

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

すべての要求は、サーバーから実行する必要があります。

いずれかの Bing API を初めて呼び出す場合は、クライアント ID ヘッダーを含めないでください。 クライアント ID を含めるのは、過去に Bing API を呼び出したことがあり、かつユーザーとデバイスの組み合わせに対応するクライアント ID が Bing から返されたことがある場合だけです。

特定のドメインから動画を取得するには、[site:](https://msdn.microsoft.com/library/ff795613.aspx) というクエリ演算子を使用します。

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us HTTP/1.1
```

応答として返されるデータには、そのクエリとの関連性が高いと Bing によって判断された一連の動画を格納する [Videos](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos) 応答が含まれています。 このリスト内の各 [Video](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video) オブジェクトには、動画の URL、再生時間、大きさ、エンコード形式などの属性が含まれています。 video オブジェクトには、動画のサムネイルの URL やサムネイルの大きさも含まれています。

```json
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545...",
    "totalEstimatedMatches" : 1000,
    "value" : [
        {
            "name" : "How to sail - What to Wear for Dinghy Sailing",
            "description" : "An informative video on what to wear when...",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7...",
            "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OVP.DYW...",
            "datePublished" : "2014-03-04T11:51:53",
            "publisher" : [
                {
                    "name" : "Fabrikam"
                }
            ],
            "creator" : 
            {
                "name" : "Marcus Appel"
            },
            "contentUrl" : "https:\/\/www.fabrikam.com\/watch?v=vzmPjZ--g",
            "hostPageUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D569...",
            "encodingFormat" : "h264",
            "hostPageDisplayUrl" : "https:\/\/www.fabrikam.com\/watch?v=vzmPjZ--g",
            "width" : 1280,
            "height" : 720,
            "duration" : "PT2M47S",
            "motionThumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?id=OM.Y62...",
            "embedHtml" : "<iframe width=\"1280\" height=\"720\" src=\"https:...><\/iframe>",
            "allowHttpsEmbed" : true,
            "viewCount" : 8743,
            "thumbnail" : 
            {
                "width" : 300,
                "height" : 168
            },
            "videoId" : "6DB795E11A6E3CBAAD636DB795E113CBAAD63",
            "allowMobileEmbed" : true,
            "isSuperfresh" : false
        },
        ...
    ],
    "queryExpansions" : [...],
    "nextOffsetAddCount" : 0,
    "pivotSuggestions" : [...]
}
```

## <a name="video-thumbnails"></a>動画のサムネイル

Bing Video Search API から返された動画のサムネイルは、すべて表示することも、サブセットを表示することもできます。 サブセットを表示する場合、残りの動画を表示するためのオプションをユーザーに提供します。 Bing API の[利用と表示の要件](../UseAndDisplayRequirements.md)上、動画は、応答で返された順に表示する必要があります。 サムネイルのサイズ変更については、[サムネイルのサイズ変更とクロップ](../../bing-web-search/resize-and-crop-thumbnails.md)に関するページを参照してください。 

ユーザーがサムネイルにマウス ポインターを重ねたときに、[motionThumbnailUrl](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video-motionthumbnailurl) を使用して、サムネイル バージョンの動画を再生することができます。 動画のサムネイルを表示するときは、必ずその帰属を示すようにしてください。

<!-- Removing until the images can be sanitized.
![Motion thumbnail of a video](../bing-web-search/media/cognitive-services-bing-web-api/bing-web-video-motion-thumbnail.PNG)
-->

動画の表示に関しては、サムネイルをクリックする際に、次の 3 つのオプションが存在します。

- ホスト Web サイト (YouTube など) で動画を再生する場合は、[hostPageUrl](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video-hostpageurl) を使用します。
- Bing の動画ブラウザーで動画を再生する場合は、[webSearchUrl](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video-websearchurl) を使用します。
- 独自に用意したエクスペリエンスに動画を埋め込む場合は、[embdedHtml](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video-embedhtml) を使用します。 

動画を再生するときは、それが発行者や作成者によるものであることがわかるようにします。

[videoId](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video-videoid) を使用して動画の分析情報を取得する方法については、「[Video Insights (ビデオの分析情報)](../video-insights.md)」を参照してください。

## <a name="filtering-videos"></a>動画のフィルタリング

Video Search API の既定の動作では、クエリとの関連性が高い動画がすべて返されます。 無料動画や長さが 5 分未満の動画のみを必要する場合は、次のフィルター クエリ パラメーターを使用します。

- [pricing](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#pricing)&mdash; 料金で動画をフィルタリング (例: 無料動画や有料動画など)
- [resolution](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#resolution)&mdash; 解像度で動画をフィルタリング (例: 720p 以上の解像度の動画など)
- [videoLength](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videolength)&mdash; 動画の長さで動画をフィルタリング (例: 長さが 5 分未満の動画など)
- [freshness](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#freshness)&mdash; 新しさで動画をフィルタリング (例: この 1 週間に Bing によって検出された動画など)

特定のドメインから動画を得るには、クエリ文字列に [site:](https://msdn.microsoft.com/library/ff795613.aspx) クエリ演算子を含めます。

> [!NOTE]
> クエリによっては、`site:` クエリ演算子を使用した場合、[safeSearch](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#safesearch) 設定にかかわらず、成人向けのコンテンツが応答に含まれることがあります。 `site:` の使用は、そのサイト上のコンテンツを承知していて、なおかつ成人向けのコンテンツが含まれていても問題がないシナリオに限定してください。

次の例は、ContosoSailing.com から、Bing が過去 1 か月に検出した 720p 以上の解像度を持つ無料動画を取得する方法を示しています。

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies+site:contososailing.com&pricing=free&freshness=month&resolution=720p&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

## <a name="expanding-the-query"></a>クエリの展開

Bing がクエリを展開して元の検索を絞り込むことができる場合、[Videos](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos) オブジェクトには `queryExpansions` フィールドが含まれます。 たとえば、クエリが *Cleaning Gutters* であれば、クエリは次のように展開されます。Gutter Cleaning **Tools**、Cleaning Gutters **From the Ground**、Gutter Cleaning **Machine**、および **Easy** Gutter Cleaning。

次の例は、*Cleaning Gutters* の展開されたクエリを示しています。

```json
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52FBC5...",
    "totalEstimatedMatches" : 1000,
    "value" : [...],
    "nextOffsetAddCount" : 4,
    "queryExpansions" : [
        {
            "text" : "Gutter Cleaning Tools",
            "displayText" : "Tools",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52FB....",
            "searchLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v5...",
            "thumbnail" : {
                "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?q=Gutter..."
            }
        },
        ...
    ]
    "pivotSuggestions" : [...],
}
```

`queryExpansions` フィールドには、[Query](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#query_obj) オブジェクトのリストが含まれています。 `text` フィールドには、展開されたクエリが格納され、`displayText` フィールドには、展開語句が格納されます。 ユーザーが本当に探しているのは、展開されたクエリ文字列であることも考えられます。そのような場合に備えて、text フィールドと thumbnail フィールドを使用して、展開されたクエリ文字列をユーザーに表示することができます。 サムネイルやテキストは、`webSearchUrl` URL または `searchLink` URL を使用して、クリック可能な状態にしましょう。 ユーザーを Bing の検索結果に誘導する場合は `webSearchUrl` を使用し、独自の結果ページを用意する場合は `searchLink` を使用します。

## <a name="pivoting-the-query"></a>クエリのピボット

元の検索クエリを Bing が分割できる場合、[Videos](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos) オブジェクトには `pivotSuggestions` フィールドが存在します。 たとえば、元のクエリが「*Cleaning Gutters*」である場合、そのクエリが Bing によって *Cleaning* と *Gutters* に分割される可能性があります。

次の例は、*Cleaning Gutters* に関するピボット候補を示しています。

```json
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52FBC...",
    "totalEstimatedMatches" : 1000,
    "value" : [...],
    "nextOffsetAddCount" : 0,
    "queryExpansions" : [...],
    "pivotSuggestions" : [
        {
            "pivot" : "cleaning",
            "suggestions" : [
                {
                    "text" : "Gutter Repair",
                    "displayText" : "Repair",
                    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52...",
                    "searchLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v5\/videos...",
                    "thumbnail" : {
                        "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?q=Gutter..."
                    }
                },
                ...
            ]
        },
        {
            "pivot" : "gutters",
            "suggestions" : [
                {
                    "text" : "Window Cleaning",
                    "displayText" : "Window",
                    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52FBC59...",
                    "searchLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v5...",
                    "thumbnail" : {
                        "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?q=Window..."
                    }
                },
                ...
            ]
        }
    ]
}
```

応答には、ピボットごとにおすすめクエリを含んだ [Query](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#query_obj) オブジェクトのリストが格納されます。 `text` フィールドには、おすすめクエリが格納され、`displayText` フィールには、元のクエリ内のピボットを置き換えた語句が格納されます たとえば、Window Cleaning などです。

ユーザーが本当に探しているのは、展開されたクエリ文字列であることも考えられます。そのような場合に備えて、`text` フィールドと `thumbnail` フィールドを使用して、展開されたクエリ文字列をユーザーに表示することができます。 サムネイルやテキストは、`webSearchUrl` URL または `searchLink` URL を使用して、クリック可能な状態にしましょう。 ユーザーを Bing の検索結果に誘導する場合は `webSearchUrl` を使用し、独自の結果ページを用意する場合は `searchLink` を使用します。

## <a name="throttling-requests"></a>スロットル リクエスト

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]
