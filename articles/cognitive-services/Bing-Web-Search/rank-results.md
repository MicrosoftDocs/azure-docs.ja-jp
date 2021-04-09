---
title: ランキングを使用して検索結果を表示する方法 - Bing Web Search API
titleSuffix: Azure Cognitive Services
description: Bing Web Search API からランキングを使用して検索結果を表示する方法について説明します。
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: BBF87972-B6C3-4910-BB52-DE90893F6C71
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 03/17/2019
ms.author: scottwhi
ms.openlocfilehash: 20501d0993cc4566a79d6e916d801911606bea35
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "94380451"
---
# <a name="how-to-use-ranking-to-display-bing-web-search-api-results"></a>ランキングを使用して Bing Web Search API の結果を表示する方法  

> [!WARNING]
> Bing Search API は、Cognitive Services から Bing Search Services に移行されます。 **2020 年 10 月 30 日** 以降、Bing Search の新しいインスタンスは、[こちら](/bing/search-apis/bing-web-search/create-bing-search-service-resource)に記載されているプロセスに従ってプロビジョニングする必要があります。
> Cognitive Services を使用してプロビジョニングされた Bing Search API は、次の 3 年間、または Enterprise Agreement の終わり (どちらか先に発生した方) までサポートされます。
> 移行手順については、[Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) に関する記事を参照してください。

検索の各応答には、[RankingResponse](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#rankingresponse) 回答が含まれます。これには、検索結果の表示方法が指定されています。 ランキング応答は、従来の検索結果ページに対してメインライン コンテンツとサイドバー コンテンツによって結果をグループ化します。 結果を従来のメインラインおよびサイドバー形式で表示しない場合は、メインラインのコンテンツの視認性をサイドバーのコンテンツよりも高くする必要があります。  

各グループ (メインラインまたはサイドバー) 内で、[Items](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#rankinggroup-items) 配列は、コンテンツの表示順序を指定します。 各項目では、回答内の結果を次の 2 つの方法で識別できます。  

-   `answerType` および `resultIndex` — `answerType` フィールドは、回答 (Web ページ、ニュースなど) を識別し、`resultIndex` は、回答内の結果 (ニュース記事など) を識別します。 インデックスは 0 から始まります。  

-   `value` — `value` フィールドには、回答または回答内の結果の ID に一致する ID が含まれます。 回答または結果のいずれかに ID が含まれています。両方には含まれていません。  

ID を使用するのは簡単です。必要があるのは、ランキング ID を回答またはその結果の ID と一致させることだけです。 回答オブジェクトに `id` フィールドが含まれている場合は、すべての回答の結果を一緒に表示します。 たとえば、`News` オブジェクトに `id` フィールドが含まれている場合は、すべてのニュース記事を一緒に表示します。 `News` オブジェクトに `id` フィールドが含まれていない場合は、各ニュース記事に `id` フィールドが含まれており、ランキング応答によって、ニュース記事と他の回答の結果がミックスされます。  

`answerType` と `resultIndex` を使用するのはもう少し複雑です。 `answerType` を使用して、表示する結果が含まれる回答を識別します。 次に、`resultIndex` を使用してその回答の結果にインデックスを付けて、表示する結果を取得できるようにします  (`answerType` 値は、[SearchResponse](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#searchresponse) オブジェクト内のフィールドの名前です)。すべての回答の結果を一緒に表示する場合、ランキング応答の項目には、`resultIndex` フィールドは含まれません。  

## <a name="ranking-response-example"></a>ランキング応答の例

次に示すのは、[RankingResponse](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#rankingresponse) の例です。 Web 回答には `id` フィールドが含まれていないため、ランキングに基づいてすべての Web ページが個別に表示されます (各 Web ページには `id` フィールドが含まれています)。 画像、ビデオ、および関連検索の回答には `id` フィールドが含まれているため、これらの回答のそれぞれの結果がランキングに基づいて一緒に表示されます。

```json
{  
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF214...",
        "totalEstimatedMatches" : 835000,
        "value" : [
            {
                "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0",
                "name" : "Motor Sports - Live at the race track ...",
                "url" : "http:\/\/www.bing.com\/cr?IG=96C4CF214A0A43...",
                "displayUrl" : "www.contoso.com\/usa\/eventsandracing\/motorsport",
                "snippet" : "Here you will find detailed information about racing...",
                "deepLinks" : [{
                    "name" : "Customer Racing",
                    "url" : "http:\/\/www.bing.com\/cr?IG=96C4CF214A0A43...",
                    "snippet" : "Customer racing news; General news..."
            },
            . . .  
        ]  
    }],  
    "images" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images",
        "readLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images...",
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF214A...",
        "isFamilyFriendly" : true,
        "value" : [
            {
                "name" : "2016 Supercar Wallpapers",
                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4...",
                "thumbnailUrl" : "https:\/\/tse1.mm.bing.net\/th?id=OIP...",
                "datePublished" : "2017-03-25T11:14:00",
                "contentUrl" : "http:\/\/www.contoso.com\/wall...",
                "hostPageUrl" : "http:\/\/www.bing.com\/cr?IG=96C4CF214...",
                "contentSize" : "373283 B",
                "encodingFormat" : "jpeg",
                "hostPageDisplayUrl" : "http:\/\/www.contoso.com\/lmp-...",
                "width" : 1920,
                "height" : 1080,
                "thumbnail" : {
                    "width" : 300,
                    "height" : 168
                },
                "insightsSourcesSummary" : {
                    "shoppingSourcesCount" : 0,
                    "recipeSourcesCount" : 0
                }
            },
            . . .  
        ]  
    },  
    "relatedSearches" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#RelatedSearches",
        "value" : [
            {
                "text" : "vintage racing teams",
                "displayText" : "vintage racing teams",
                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF2..."
            },
            . . .  
        ]  
    },  
    "videos" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Videos",
        "readLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/videos...",
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF214A...",
        "isFamilyFriendly" : true,
        "value" : [
            {
                "name" : "Why We Race",
                "description" : "A new era begins in motorsports this weekend...",
                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF2...",
                "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OVP.Vo1...",
                "datePublished" : "2014-01-25T16:31:48",
                "publisher" : [
                    {
                        "name" : "Fabrikam"
                    }
                ],
                "contentUrl" : "https:\/\/www.fabrikam.com\/watch?v=oL...",
                "hostPageUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF214...",
                "encodingFormat" : "mp4",
                "hostPageDisplayUrl" : "https:\/\/www.fabrikam.com\/watch?v=oLAZgD...",
                "width" : 480,
                "height" : 360,
                "duration" : "PT2M42S",
                "motionThumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OM...",
                "embedHtml" : "<iframe width=\"1280\" height=\"720\" src=\"http:\/\/www.you...<\/iframe>",
                "allowHttpsEmbed" : true,
                "viewCount" : 47325,
                "thumbnail" : {
                    "width" : 300,
                    "height" : 168
                },
                "allowMobileEmbed" : true,
                "isSuperfresh" : false
            },
            . . .  
        ]  
    },  
    "rankingResponse" : {
        "mainline" : {
            "items" : [{
                "answerType" : "WebPages",
                "resultIndex" : 0,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0"
                }
            },
            {
                "answerType" : "Images",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 1,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.1"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 2,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.2"
                }
            },
            {
                "answerType" : "Videos",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Videos"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 3,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.3"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 4,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.4"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 5,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.5"
                }
            }]
        },
        "sidebar" : {
            "items" : [{
                "answerType" : "RelatedSearches",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#RelatedSearches"
                }
            }]
        }
    }
}  
```  

メインラインでは、このランキング応答に基づいて、次の検索結果が表示されます。  

-   最初の Web ページの結果
-   すべての画像  
-   2 番目と 3 番目の Web ページの結果  
-   すべてのビデオ  
-   4 番目、5 番目、および 6 番目の Web ページの結果  

サイドバーには、次の検索結果が表示されます。  

-   すべての関連検索  


## <a name="next-steps"></a>次のステップ

ランク付けされない結果を昇格する方法については、「[Promoting answers that are not ranked (ランク付けされない回答を昇格する)](./filter-answers.md#promoting-answers-that-are-not-ranked)」を参照してください。

応答内のランク付けされた回答の数を制限する方法については、「[Limiting the number of answers in the response (応答の回答数を制限する)](./filter-answers.md#limiting-the-number-of-answers-in-the-response)」を参照してください。

ランキングを使用して結果を表示する C# の例については、[C# のランキングのチュートリアル](./csharp-ranking-tutorial.md)を参照してください。