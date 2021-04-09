---
title: Bing Video Search API を使用して、急上昇中の動画を Web で検索する
titleSuffix: Azure Cognitive Services
description: Bing Video Search API を使用して、急上昇中の動画を Web で検索する方法を示します。
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: scottwhi
ms.openlocfilehash: cfeebcda201df592f6c396dcc780208a36d1e989
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96353801"
---
# <a name="get-trending-videos-with-the-bing-video-search-api"></a>Bing Video Search API を使用して急上昇中の動画を取得する 

> [!WARNING]
> Bing Search API は、Cognitive Services から Bing Search Services に移行されます。 **2020 年 10 月 30 日** 以降、Bing Search の新しいインスタンスは、[こちら](/bing/search-apis/bing-web-search/create-bing-search-service-resource)に記載されているプロセスに従ってプロビジョニングする必要があります。
> Cognitive Services を使用してプロビジョニングされた Bing Search API は、次の 3 年間、または Enterprise Agreement の終わり (どちらか先に発生した方) までサポートされます。
> 移行手順については、[Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) に関する記事を参照してください。

Bing Video Search API を使用すると、さまざまなカテゴリで現在急上昇中の動画を Web 全体から検索することができます。 

## <a name="get-request"></a>GET 要求

今日の急上昇中の動画を Bing Video Search API から取得するには、次の GET 要求を送信します。  
  
```cURL
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/trending?mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

## <a name="market-support"></a>市場のサポート

次の市場は、急上昇中の動画をサポートしています。  
 
-   en-AU (英語、オーストラリア)  
-   en-CA (英語、カナダ)  
-   en-GB (英語、英国)  
-   en-ID (英語、インドネシア)  
-   en-IE (英語、アイルランド)  
-   en-IN (英語、インド)  
-   en-NZ (英語、ニュージーランド)  
-   en-PH (英語、フィリピン)  
-   en-SG (英語、シンガポール)  
-   en-US (英語、米国)  
-   en-WW (英語、世界全域の集約コード)  
-   en-ZA (英語、南アフリカ)  
-   zh-CN (中国語、中国)

## <a name="example-json-response"></a>JSON の応答例  

次の例は、カテゴリとサブカテゴリごとに一覧表示された急上昇中の動画を含む API 応答を示しています。 この応答にはバナー ビデオも含まれています。バナー ビデオは、最も人気のある急上昇中の動画であり、1 つ以上のカテゴリから選択される場合があります。  

```json
{  
    "_type" : "TrendingVideos",  
    "bannerTiles" : [
        {  
            "query" : {  
                "text" : "Hello - Smith",  
                "displayText" : "Hello - Smith",  
                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3E8F5..."
            },  
            "image" : {  
                "description" : "Image from: contosowallpapers.com",  
                "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=RsA%2fdPlTmx4zS...",  
                "headLine" : "\"Hello\" is a song by...",  
                "contentUrl" : "http:\/\/www.contosowallpapers.com\/wp-content..."  
            }  
        },  
        . . .  
    ],  
    "categories" : [
        {  
            "title" : "Music videos",  
            "subcategories" : [
                {  
                    "tiles" : [
                        {  
                            "query" : {  
                                "text" : "Song Title - Artist Name",  
                                "displayText" : "Song Title - Artist Name",  
                                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3E8F5..."
                            },  
                            "image" : {  
                                "description" : "Image from: contoso.com",  
                                "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?id=...",  
                                "contentUrl" : "http:\/\/images6.contoso.com\/image..."  
                            }  
                        },  
                        . . .  
                    ],
                    "title" : "Top "  
                },
                {
                    "tiles" : [...],
                    "title" : "Trending"
                },
                . . .
            ],  
        },
        {
            "title" : "Viral videos",
            "subcategories" : [
                {
                    "tiles" : [...],
                    "title" : "Trending"
                },
                . . .
            ],  
        },
        . . .  
    ]  
}  
  
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [ビデオの分析情報の取得](video-insights.md)