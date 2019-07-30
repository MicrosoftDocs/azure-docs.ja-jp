---
title: Bing News Search API でニュースを検索する
titleSuffix: Azure Cognitive Services
description: 通常のニュース、トレンドのトピック、ヘッドラインの検索クエリを送信する方法について説明します。
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: overview
ms.date: 06/19/2019
ms.author: scottwhi
ms.openlocfilehash: b70cf50bb33ca7962116586a347c508b15abcd0c
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423882"
---
# <a name="search-for-news-with-the-bing-news-search-api"></a>Bing News Search API でニュースを検索する

Bing Image Search API を使用すると、Bing のコグニティブ ニュース検索機能を簡単にアプリケーションに統合することができます。

Bing News Search API は主に、関連するニュース記事を検索して返すものですが、同時に、インテリジェントで的を絞ったニュース検索を Web 上で行うための機能をいくつか備えています。

## <a name="suggest-and-use-search-terms"></a>検索語句の提案と使用

ユーザーが検索語句を入力するための検索ボックスを用意する場合は、[Bing Autosuggest API](../../bing-autosuggest/get-suggested-search-terms.md) を使用することでエクスペリエンスが向上します。 この API は、検索語句をユーザーが入力している最中に、その一部分に基づいてクエリ文字列の候補を返します。

ユーザーが検索語句を入力した後、それを URL エンコードしたうえで、[q](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#query) クエリ パラメーターを設定します。 たとえば「*sailing dinghies*」と入力された場合、`q` を `sailing+dinghies` または `sailing%20dinghies` に設定します。

## <a name="get-general-news"></a>通常のニュースを取得する

ユーザーの検索語句に関連した通常のニュース記事を Web から取得するには、次の GET 要求を送信します。

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

いずれかの Bing API を初めて呼び出す場合は、クライアント ID ヘッダーを含めないでください。 クライアント ID を含めるのは、過去に Bing API を呼び出したことがあり、かつユーザーとデバイスの組み合わせに対応するクライアント ID が Bing から返されたことがある場合だけです。

特定のドメインからニュースを取得するには、[site:](https://msdn.microsoft.com/library/ff795613.aspx) というクエリ演算子を使用します。

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us HTTP/1.1
```

前のクエリに対する応答を次の JSON サンプルに示します。 Bing Search APIs の[利用と表示の要件](../useanddisplayrequirements.md)上、それぞれのニュース記事は、応答で返された順に表示する必要があります。 記事にクラスター化されている記事が含まれる場合、関連する記事が存在することを示し、要求された時点でそれらを表示する必要があります。

```json
{
    "_type" : "News",
    "readLink" : "https:\/\/api.cognitive.microsoft.com\/bing\/v5\/news\/search?q=sailing+dinghies",
    "totalEstimatedMatches" : 88400,
    "value" : [{
        "name" : "Sailing Vies for Four Trophies",
        "url" : "http:\/\/www.bing.com\/cr?IG=CCE2F06CA750455891FE99A72...",
        "image" : {
            "thumbnail" : {
                "contentUrl" : "https:\/\/www.bing.com\/th?id=ON.9C23AA5...",
                "width" : 650,
                "height" : 341
            }
        },
        "description" : "College Rankings, presented by Zim...",
        "provider" : [{
            "_type" : "Organization",
            "name" : "contoso.com"
        }],
        "datePublished" : "2017-04-14T15:28:00"
    },

    ...

    {
        "name" : "Fabrikam Sailing Club to host Mirror Dinghy...",
        "url" : "http:\/\/www.bing.com\/cr?IG=CCE2F06CA750455891F...",
        "image" : {
            "thumbnail" : {
                "contentUrl" : "https:\/\/www.bing.com\/th?id=ON.36...",
                "width" : 448,
                "height" : 300
            }
        },
        "description" : "The sailing club that trained Olympian Ben...",
        "provider" : [{
            "_type" : "Organization",
            "name" : "Contoso"
        }],
        "datePublished" : "2017-04-04T11:02:00",
        "category" : "Sports"
    }]
}
```

[news](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v5-reference#news) 回答には、そのクエリとの関連性が高いと Bing によって判断された一連のニュース記事が格納されます。 表示できる記事の数の見積もりは、`totalEstimatedMatches` フィールドに格納されます。 記事のページングの詳細については、「[ニュースのページング](../paging-news.md)」を参照してください。

このリスト内の各 [newsarticle](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v5-reference#newsarticle) には、その記事の名前や説明、さらに、ホストの Web サイト上の記事への URL が含まれています。 記事に画像が含まれている場合、このオブジェクトには、その画像のサムネイルが含まれます。 ホストのサイト上にあるニュース記事にユーザーを誘導するハイパーリンクを作成するには、`name` と `url` を使用します。 記事に画像が含まれる場合は、`url` を使用して画像もクリックできるようにします。 必ず `provider` を使用して記事の帰属を示すようにしてください。

Bing がニュース記事のカテゴリを決定できる場合、記事に `category` フィールドが含まれます。

## <a name="get-todays-top-news"></a>今日のトップ ニュースを取得する

今日のトップ ニュース記事を入手するには、`q` パラメーターを設定せずに、以前と同じ通常のニュース要求を送信します。

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

トップ ニュースを取得する際の応答は、通常のニュースを取得する場合とほとんど同じです。 ただし、結果の数は決まっているので、`news` 応答に `totalEstimatedMatches` フィールドは含まれません。 トップ ニュース記事の数は、ニュース サイクルによって異なる場合があります。 必ず `provider` フィールドを使用して、記事の帰属を示すようにしてください。

## <a name="get-news-by-category"></a>カテゴリ別のニュースを取得する

トップ スポーツ、エンターテイメント記事など、カテゴリ別にニュース記事を取得するには、Bing に次の GET 要求を送信します。

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news?category=sports&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

[category](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#category) クエリ パラメーターを使用して、取得する記事のカテゴリを指定します。 指定可能なニュース カテゴリの一覧は、「[News Categories by Market (マーケット別ニュース カテゴリ)](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#news-categories-by-market)」を参照してください。

カテゴリ別にニュースを取得する際の応答は、通常のニュースの取得とほとんど同じです。 ただし、記事はすべて指定したカテゴリのものです。

## <a name="get-headline-news"></a>ヘッドライン ニュースを取得する

ヘッドライン ニュース記事を要求してすべてのニュース カテゴリから記事を取得するには、Bing に次の GET 要求を送信します。

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news?mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

[category](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#category) クエリ パラメーターは含めないでください。

ヘッドライン ニュースを取得する際の応答は、今日のトップ ニュースを取得する場合と同じです。 記事がヘッドライン記事である場合、`headline` フィールドが **true** に設定されます。

既定では、応答には最大 12 個のヘッドライン記事が含まれます。 返されるヘッドライン記事の数を変更するには、[headlineCount](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#headlinecount) クエリ パラメーターを指定します。 応答には、ニュースのカテゴリ 1 つにつき最大 4 つの非ヘッドライン記事も含まれます。

応答では、クラスターは 1 つの記事としてカウントされます。 クラスターには複数の記事が含まれる可能性があるため、12 を超えるヘッドライン記事や、1 つのカテゴリにつき 4 つを超える非ヘッドライン記事が応答に含まれる場合があります。

## <a name="get-trending-news"></a>トレンドのニュースを取得する

ソーシャル ネットワークでトレンドになっているニュース トピックを取得するには、Bing に次の GET 要求を送信します。

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news/trendingtopics?mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
X-MSAPI-UserState: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

> [!NOTE]
> トレンドのトピックは、en-US および zh-CN マーケットでのみ使用できます。

前述の要求への応答は次の JSON のようになります。 各トレンドのニュース記事には、関連する画像、ニュース速報フラグ、および記事の Bing 検索結果の URL が含まれています。 `webSearchUrl` フィールドの URL を使用して、ユーザーを Bing 検索結果ページに誘導します。 または、クエリ テキストを使用して [Web Search API](../../bing-web-search/search-the-web.md) を呼び出し、自分で結果を表示します。

```json
{
    "_type" : "TrendingTopics",
    "value" : [{
        "name" : "Canada pot measure",
        "image" : {
            "url" : "https:\/\/www.bing.com\/th?id=OPN.RTNews_hHD...",
            "provider" : [{
                "_type" : "Organization",
                "name" : "Contoso Images"
            }]
        },
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=070292D8CEDD...",
        "isBreakingNews" : false,
        "query" : {
            "text" : "Canada marijuana"
        }
    },
    {
        "name" : "Down on Vegas move",
        "image" : {
            "url" : "https:\/\/www.bing.com\/th?id=OPN.RTNews_Bfbmg8h...",
            "provider" : [{
                "_type" : "Organization",
                "name" : "Contoso"
            }]
        },
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=070292D8CEDD...",
        "isBreakingNews" : false,
        "query" : {
            "text" : "Marcus Appel Las Vegas"
        }
    },

    ...

    ]
}
```

## <a name="getting-related-news"></a>関連ニュースの取得

ニュース記事に関連する他の記事がある場合、ニュース記事に [clusteredArticles](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#newsarticle-clusteredarticles) フィールドが含まれる場合があります。 次に、クラスター化された記事を含む記事を示します。

```json
    {
        "name" : "Playoffs 2017: Betting lines, point spreads...",
        "url" : "http:\/\/www.bing.com\/cr?IG=4B7056CEC271408997D115...",
        "image" : {
            "thumbnail" : {
                "contentUrl" : "https:\/\/www.bing.com\/th?id=ON.D7B1...",
                "width" : 700,
                "height" : 393
            }
        },
        "description" : "April 14, 2017 3:37pm EDT April 14, 2017 3:34pm...",
        "provider" : [{
            "_type" : "Organization",
            "name" : "Contoso"
        }],
        "datePublished" : "2017-04-14T19:43:00",
        "category" : "Sports",
        "clusteredArticles" : [{
            "name" : "Playoffs 2017: Betting odds, favorites to win...",
            "url" : "http:\/\/www.bing.com\/cr?IG=4B7056CEC271408997D1159E...",
            "description" : "April 14, 2017 3:30pm EDT April 14, 2017 3:27pm...",
            "provider" : [{
                "_type" : "Organization",
                "name" : "Contoso"
            }],
            "datePublished" : "2017-04-14T19:37:00",
            "category" : "Sports"
        }]
    },
```

## <a name="throttling-requests"></a>スロットル リクエスト

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Bing News Search の結果をページングする方法](../paging-news.md)
