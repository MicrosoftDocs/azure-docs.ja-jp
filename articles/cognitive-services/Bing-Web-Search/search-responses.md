---
title: Bing Web Search API の応答の構造と答えの種類
titleSuffix: Azure Cognitive Services
description: Bing Web Search に検索要求を送信すると、応答本文で `SearchResponse` オブジェクトが返されます。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 3dda95312a0b9191ddc11de62959f308ee19fff4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "94380982"
---
# <a name="bing-web-search-api-response-structure-and-answer-types"></a>Bing Web Search API の応答の構造と答えの種類  

> [!WARNING]
> Bing Search API は、Cognitive Services から Bing Search Services に移行されます。 **2020 年 10 月 30 日** 以降、Bing Search の新しいインスタンスは、[こちら](/bing/search-apis/bing-web-search/create-bing-search-service-resource)に記載されているプロセスに従ってプロビジョニングする必要があります。
> Cognitive Services を使用してプロビジョニングされた Bing Search API は、次の 3 年間、または Enterprise Agreement の終わり (どちらか先に発生した方) までサポートされます。
> 移行手順については、[Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) に関する記事を参照してください。

Bing Web Search に検索要求を送信すると、応答本文で [`SearchResponse`](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#searchresponse) オブジェクトが返されます。 このオブジェクトには、クエリとの関連性が高いと Bing が判断した各回答に対応するフィールドが含まれています。 次の例は、Bing からすべての回答が返された場合の応答オブジェクトを示しています。

```json
{
    "_type": "SearchResponse",
    "queryContext": {...},
    "webPages": {...},
    "images": {...},
    "relatedSearches": {...},
    "videos": {...},
    "news": {...},
    "spellSuggestion": {...},
    "computation": {...},
    "timeZone": {...},
    "rankingResponse": {...}
}, ...
```

Bing Web Search から返されるのは、回答のサブセットであるのが一般的です。 たとえば、検索語が *sailing dinghies* であれば、応答には `webPages`、`images`、`rankingResponse` が含まれると考えられます。 [responseFilter](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#responsefilter) を使って Web ページを除外した場合を除けば、応答には必ず回答として `webpages` と `rankingResponse` が含まれます。

[!INCLUDE [cognitive-services-bing-url-note](../../../includes/cognitive-services-bing-url-note.md)]

## <a name="webpages-answer"></a>webPages 回答

[webPages](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webanswer) の回答には、クエリとの関連性が高いと Bing Web Search によって判断された Web ページへのリンクのリストが格納されます。 このリストに含まれる各 [Webpage](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webpage) には、ページの名前、URL、表示 URL、コンテンツの簡単な説明、Bing でそのコンテンツが見つかった日付が含まれます。

```json
{
    "id": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0",
    "name": "Dinghy sailing",
    "url": "https:\/\/www.bing.com\/cr?IG=3A43CA5...",
    "displayUrl": "https:\/\/en.contoso.com\/wiki\/Dinghy_sailing",
    "snippet": "Dinghy sailing is the activity of sailing small boats...",
    "dateLastCrawled": "2017-04-05T16:25:00"
}, ...
```

該当する Web ページにユーザーを誘導するハイパーリンクを作成するには、`name` と `url` を使用します。

<!-- Remove until this can be replaced with a sanitized version.
The following shows an example of how you might display the webpage in a search results page.

![Rendered webpage example](./media/cognitive-services-bing-web-api/bing-rendered-webpage-example.PNG)
-->

## <a name="images-answer"></a>イメージの応答

[images](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) 回答には、そのクエリとの関連性が高いと Bing によって判断された一連の画像が格納されます。 このリスト内の各 [image](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image) には、その URL、サイズ、大きさ、エンコード形式が含まれています。 image オブジェクトには、画像のサムネイルの URL やサムネイルの大きさも含まれています。

```json
{
    "name": "Rich Passage Sailing Dinghy",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=3A43CA5CA64...",
    "thumbnailUrl": "https:\/\/tse1.mm.bing.net\/th?id=OIP....",
    "datePublished": "2011-10-29T11:26:00",
    "contentUrl": "http:\/\/upload.contoso.com\/sailing\/...",
    "hostPageUrl": "http:\/\/www.bing.com\/cr?IG=3A43CA5CA6464....",
    "contentSize": "79239 B",
    "encodingFormat": "jpeg",
    "hostPageDisplayUrl": "http:\/\/en.contoso.com\/wiki\/File...",
    "width": 526,
    "height": 688,
    "thumbnail": {
        "width": 229,
        "height": 300
    },
    "insightsSourcesSummary": {
        "shoppingSourcesCount": 0,
        "recipeSourcesCount": 0
    }
}, ...
```

ユーザーのデバイスにもよりますが、通常はサムネイルのサブセットを表示したうえで、残りの画像の[ページングを行う](./paging-search-results.md)ためのオプションをユーザーに表示します。

<!-- Remove until this can be replaced with a sanitized version.
![List of thumbnail images](./media/cognitive-services-bing-web-api/bing-web-image-thumbnails.PNG)
-->

ユーザーがサムネイルにカーソルを合わせたタイミングでそのサムネイルを展開することもできます。 画像を展開する場合は、必ずその帰属を示すようにしてください。 たとえば、`hostPageDisplayUrl` からホストを展開して、それを画像の下に表示するようにします。 サムネイルのサイズ変更については、[サムネイルのサイズ変更とクロップ](./resize-and-crop-thumbnails.md)に関するページを参照してください。

<!-- Remove until this can be replaced with a sanitized version.
![Expanded view of thumbnail image](./media/cognitive-services-bing-web-api/bing-web-image-thumbnail-expansion.PNG)
-->

ユーザーがサムネイルをクリックした場合は、`webSearchUrl` を使って、Bing の画像検索結果ページ (画像のコラージュが表示されるページ) にユーザーを誘導します。

images 回答と image について詳しくは、[Image Search API](../bing-image-search/overview.md) に関するページをご覧ください。

## <a name="related-searches-answer"></a>relatedSearches 回答

[relatedSearches](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#searchresponse-relatedsearches) 回答には、他のユーザーによって実行された関連クエリの中で最もポピュラーなもののリストが格納されます。 リスト内の各 [Query](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#query_obj) には、クエリ文字列 (`text`) のほか、検索語句を強調表示するための文字を含んだクエリ文字列 (`displayText`)、そのクエリに対する Bing の検索結果ページへの URL (`webSearchUrl`) が含まれています。

```json
{
    "text": "dinghy racing teams",
    "displayText": "dinghy racing teams",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=96C4CF214A0..."
}, ...
```

関連クエリに対する Bing の検索結果ページにユーザーを誘導するハイパーリンクを作成するには、`displayText` クエリ文字列と `webSearchUrl` URL を使用します。 独自の Web Search API クエリの中で `text` クエリ文字列を使用し、独自にその結果を表示することもできます。

`displayText` で検索結果を強調表示するためのマーカーの使い方については、[検索結果の強調表示](../bing-web-search/hit-highlighting.md)に関するページを参照してください。

次に示したのは、Bing.com での関連するクエリの使用例です。

![Bing での関連検索の例](./media/cognitive-services-bing-web-api/bing-web-rendered-relatedsearches.GIF)

## <a name="videos-answer"></a>videos 回答

[videos](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos) 回答には、そのクエリとの関連性が高いと Bing によって判断された一連の動画が格納されます。 このリスト内の各 [video](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video) には、動画の URL、再生時間、大きさ、エンコード形式が含まれています。 video オブジェクトには、動画のサムネイルの URL やサムネイルの大きさも含まれています。

```json
{
    "name": "Sailing dinghy",
    "description": "Northwind Traders is a 12 foot gunter rigged...",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=1CAE739681D84...",
    "thumbnailUrl": "https:\/\/tse2.mm.bing.net\/th?id=OVP.wsKiL...",
    "datePublished": "2013-11-06T01:56:28",
    "publisher": [{
        "name": "Fabrikam"
    }],
    "contentUrl": "https:\/\/www.fabrikam.com\/watch?v=MrVBWZpJjX",
    "hostPageUrl": "https:\/\/www.bing.com\/cr?IG=1CAE739681D8400DB...",
    "encodingFormat": "mp4",
    "hostPageDisplayUrl": "https:\/\/www.fabrikam.com\/watch?v=MrBWZpJjXo",
    "width": 1280,
    "height": 720,
    "duration": "PT3M47S",
    "motionThumbnailUrl": "https:\/\/tse2.mm.bing.net\/th?id=OM.oa...",
    "embedHtml": "<iframe width=\"1280\" height=\"720\" src=\"http:\/\/www....><\/iframe>",
    "allowHttpsEmbed": true,
    "viewCount": 19089,
    "thumbnail": {
        "width": 300,
        "height": 168
    },
    "allowMobileEmbed": true,
    "isSuperfresh": false
}, ...
```

ユーザーのデバイスにもよりますが、通常は動画のサブセットを表示したうえで、残りの動画を表示するためのオプションを表示します。 動画のサムネイルは、動画の長さ、説明 (名前)、属性 (公開元) と一緒に表示することになるでしょう。

<!-- Remove until this can be replaced with a sanitized version.
![List of video thumbnails](./media/cognitive-services-bing-web-api/bing-web-video-thumbnails.PNG)
-->

ユーザーがサムネイルにマウス ポインターを重ねたときに、`motionThumbnailUrl` を使用して、サムネイル バージョンの動画を再生することができます。 動画のサムネイルを表示するときは、必ずその帰属を示すようにしてください。

<!-- Remove until this can be replaced with a sanitized version.
![Motion thumbnail of a video](./media/cognitive-services-bing-web-api/bing-web-video-motion-thumbnail.PNG)
-->

サムネイルがクリックされた場合の動画の再生方法としては、次の選択性があります。

- ホスト Web サイト (YouTube など) で動画を再生する場合は、`hostPageUrl` を使用します。
- Bing の動画ブラウザーで動画を再生する場合は、`webSearchUrl` を使用します。
- 独自に用意したエクスペリエンスに動画を埋め込む場合は、`embedHtml` を使用します。

videos 回答と video について詳しくは、[Video Search API](../bing-video-search/overview.md) に関するページをご覧ください。

## <a name="news-answer"></a>news 回答

[news](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#news) 回答には、そのクエリとの関連性が高いと Bing によって判断された一連のニュース記事が格納されます。 このリスト内の各 [newsarticle](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#newsarticle) には、その記事の名前や説明、さらに、ホストの Web サイト上の記事への URL が含まれています。 記事に画像が含まれている場合、このオブジェクトには、その画像のサムネイルが含まれます。

```json
{
    "name": "WC Sailing Qualifies for America Trophy with...",
    "url": "http:\/\/www.bing.com\/cr?IG=3445EEF15DAF4FFFBF7...",
    "image": {
        "contentUrl": "http:\/\/www.contoso.com\/sports\/sail...",
        "thumbnail": {
            "contentUrl": "https:\/\/www.bing.com\/th?id=ON.1...",
            "width": 400,
            "height": 272
        }
    },
    "description": "The WC sailing team qualified for a...",
    "provider": [{
        "_type": "Organization",
        "name": "contoso.com"
    }],
    "datePublished": "2017-04-16T21:56:00"
}, ...
```

ユーザーのデバイスにもよりますが、ニュース記事のサブセットを表示したうえで、残りの記事を表示するためのオプションを表示するのが一般的です。 ホストのサイト上にあるニュース記事にユーザーを誘導するハイパーリンクを作成するには、`name` と `url` を使用します。 記事に画像が含まれる場合に、その画像をクリックできるようにするには、`url` を使用します。 必ず `provider` を使用して記事の帰属を示すようにしてください。

<!-- Remove until this can be replaced with a sanitized version.
The following shows an example of how you might display articles in a search results page.

![List of news articles](./media/cognitive-services-bing-web-api/bing-web-news-list.PNG)
-->

news 回答と newsarticle について詳しくは、[News Search API](../bing-news-search/search-the-web.md) に関するページをご覧ください。

## <a name="computation-answer"></a>computation 回答

ユーザーが数式または単位変換クエリを入力した場合、応答には [Computation](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#computation) 回答が格納されます。 `computation` 回答には、正規化された式とその結果が格納されます。

単位変換クエリとは、ある単位を別の単位に変換するクエリをいいます。 たとえば、"*How many feet in 10 meters? (10 メートルは何フィート?)* " や "*How many tablespoons in a 1/4 cup? (1/4 カップは大さじ何杯?)* " といったクエリが考えられます。

次に示したのは、"*How many feet in 10 meters? (10 メートルは何フィート?)* " に対する `computation` 回答です。

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
    "expression": "10 meters",
    "value": "32.808399 feet"
}, ...
```

次に、数学的クエリとそれに対応する `computation` 回答の例を示します。

```
Query: (5+3)(10/2)+8
Encoded query: %285%2B3%29%2810%2F2%29%2B8
```

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
    "expression": "((5+3)*(10\/2))+8",
    "value": "48"
}
```

```
Query: sqrt(4^2+8^2)
Encoded query: sqrt%284^2%2B8^2%29
```

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
    "expression": "sqrt((4^2)+(8^2))",
    "value": "8.94427191"
}
```

```
Query: 30 6/8 - 18 8/16
Encoded query: 30%206%2F8%20-%2018%208%2F16
```

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#WolframAlpha",
    "expression": "30 6\/8-18 8\/16",
    "value": "12.25"
}
```

```
Query: 8^2+11^2-2*8*11*cos(37)
Encoded query: 8^2%2B11^2-2*8*11*cos%2837%29
```

```json
"computation": {
        "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
        "expression": "(8^2)+(11^2)-(2*8*11*cos(37))",
        "value": "44.4401502"
}
```

数式には、次の記号を含めることができます。

|Symbol|説明|
|------------|-----------------|
|+|加算|
|-|減算|
|/|除算|
|*|乗算|
|^|Power|
|!|階乗|
|.|Decimal|
|()|優先順位のグループ化|
|[]|機能|

数式には、次の定数を含めることができます。

|Symbol|説明|
|------------|-----------------|
|Pi|3.14159...|
|i|i|
|i|虚数|
|e|自然対数の底 (2.71828...)|
|GoldenRatio|黄金比 (1.61803...)|

数式には、次の関数を含めることができます。

|Symbol|説明|
|------------|-----------------|
|並べ替え|平方根|
|Sin[x]、Cos[x]、Tan[x]<br />Csc[x]、Sec[x]、Cot[x]|三角関数 (引数はラジアン単位)|
|ArcSin[x]、ArcCos[x]、ArcTan[x]<br />ArcCsc[x]、ArcSec[x]、ArcCot[x]|逆三角関数 (結果はラジアン単位)|
|Exp[x]、E^x|指数関数|
|Log[x]|自然対数|
|Sinh[x]、Cosh[x]、Tanh[x]<br />Csch[x]、Sech[x]、Coth[x]|双曲線関数|
|ArcSinh[x]、ArcCosh[x]、ArcTanh[x]<br />ArcCsch[x]、ArcSech[x]、ArcCoth[x]|逆双曲線関数|

変数を含む数式 (例: 4x+6=18、x は変数) はサポートされません。

## <a name="timezone-answer"></a>timeZone 回答

ユーザーが時刻または日付のクエリを入力した場合、応答には [TimeZone](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#timezone) 回答が格納されます。 この回答は、暗黙的なクエリと明示的なクエリをサポートします。 "*What time is it? (何時?)* " など暗黙的なクエリは、ユーザーの位置情報に基づくローカル時刻を返します。 "*What time is it in Seattle? (シアトルでは何時?)* " など明示的なクエリは、ワシントン州シアトルのローカル時刻を返します。

`timeZone` 回答からは、場所の名前、指定された場所における現在の UTC 日時、UTC オフセットが得られます。 その場所の境界が複数のタイム ゾーンにまたがっている場合、回答には、その境界内のすべてのタイム ゾーンにおける現在の UTC 日時が格納されます。 たとえばフロリダ州は 2 つのタイム ゾーンにまたがっているため、回答には、両方のタイム ゾーンのローカル日時が格納されます。  

クエリで州または国/地域の時刻を要求した場合、Bing は、その場所の地理的境界内に含まれる主要都市を判断し、それを `primaryCityTime` フィールドに格納して返します。 その境界が複数のタイム ゾーンにまたがっている場合、残りのタイム ゾーンは `otherCityTimes` フィールドで返されます。

次に示したのは、`timeZone` 回答を返すクエリの例です。

```
Query: What time is it?

"timeZone": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#TimeZone",
    "primaryCityTime": {
        "location": "Redmond, Washington, United States",
        "time": "2015-10-27T08:38:12.1189231Z",
        "utcOffset": "UTC-7"
    }
}

Query: What time is it in the Pacific time zone?

"timeZone": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#TimeZone",
    "primaryCityTime": {
        "location": "Pacific Time Zone",
        "time": "2015-10-23T12:33:19.0728146Z",
        "utcOffset": "UTC-7"
    }
}

Query: Time in Florida?

"timeZone": {
        "id": "https:\/\/www.bing.com\/api\/v7\/#TimeZone",
        "primaryCityTime": {
            "location": "Tallahassee, Florida, United States",
            "time": "2015-10-23T13:04:56.6774389Z",
            "utcOffset": "UTC-4"
        },
        "otherCityTimes": [{
            "location": "Pensacola",
            "time": "2015-10-23T12:04:56.6664294Z",
            "utcOffset": "UTC-5"
        }]
}

Query: What time is it in the U.S.

"timeZone": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#TimeZone",
    "primaryCityTime": {
        "location": "Washington, D.C., United States",
        "time": "2015-10-23T15:27:59.8892745Z",
        "utcOffset": "UTC-4"
    },
    "otherCityTimes": [{
        "location": "Honolulu",
        "time": "2015-10-23T09:27:59.8892745Z",
        "utcOffset": "UTC-10"
    },
    {
        "location": "Anchorage",
        "time": "2015-10-23T11:27:59.8892745Z",
        "utcOffset": "UTC-8"
    },
    {
        "location": "Phoenix",
        "time": "2015-10-23T12:27:59.8892745Z",
        "utcOffset": "UTC-7"
    },
    {
        "location": "Los Angeles",
        "time": "2015-10-23T12:27:59.8942788Z",
        "utcOffset": "UTC-7"
    },
    {
        "location": "Denver",
        "time": "2015-10-23T13:27:59.8812681Z",
        "utcOffset": "UTC-6"
    },
    {
        "location": "Chicago",
        "time": "2015-10-23T14:27:59.8892745Z",
        "utcOffset": "UTC-5"
    }]
}
```

## <a name="spellsuggestion-answer"></a>SpellSuggestion 回答

検索の対象がユーザーの意図と異なる可能性があると Bing が判断した場合、応答には [SpellSuggestions](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#spellsuggestions) オブジェクトが含まれます。 たとえばユーザーが「*carlos pen*」を検索した場合、Bing は (過去に他のユーザーが行った *carlos pen* の検索に基づき) ユーザーが本当に検索したいのは Carlos Pena であると判断します。 スペル応答の例を次に示します。

```json
"spellSuggestions": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#SpellSuggestions",
    "value": [{
        "text": "carlos pena",
        "displayText": "carlos pena"
    }]
}, ...
```

## <a name="response-headers"></a>応答ヘッダー

Bing Web Search API からの応答には、以下のヘッダーが含まれることがあります。

| ヘッダー | 説明 |
|-|-|
|`X-MSEdge-ClientID`|Bing がユーザーに割り当てた一意の ID|
|`BingAPIs-Market`|要求を満たすために使用された市場|
|`BingAPIs-TraceId`|この要求に対する Bing API サーバー上のログ エントリ (サポート用)|

クライアント ID を保持し、後続の要求で返すことが特に重要です。 これを行うときに、検索は検索結果の順位付けに過去のコンテキストを使用し、一貫性のあるユーザー エクスペリエンスも提供します。

ただし、JavaScript から Bing Web Search API を呼び出すときに、ブラウザーの組み込みのセキュリティ機能 (CORS) によっては、これらのヘッダーの値にアクセスできないことがあります。

ヘッダーにアクセスするために、CORS プロキシを介して Bing Web Search API 要求を行うことができます。 そのようなプロキシからの応答には、応答ヘッダーをフィルター処理して JavaScript で使用できるようにする `Access-Control-Expose-Headers` ヘッダーが含まれています。

CORS プロキシをインストールして[チュートリアル アプリ](tutorial-bing-web-search-single-page-app.md)が省略可能なクライアント ヘッダーにアクセスできるようにするのは簡単です。 まず、[Node.js をインストールします](https://nodejs.org/en/download/) (まだインストールしていない場合)。 その後、コマンド プロンプトで次のコマンドを入力します。

```console
npm install -g cors-proxy-server
```

次に、HTML ファイル内の Bing Web Search API エンドポイントを次のように変更します。
`http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search`

最後に、次のコマンドを使用して、CORS プロキシを開始します。

```console
cors-proxy-server
```

チュートリアル アプリを使用している間はコマンド ウィンドウを開いたままにしておいてください。ウィンドウを閉じるとプロキシが停止します。 検索結果の下の展開可能な HTTP ヘッダー セクションに、`X-MSEdge-ClientID` ヘッダー (など) が表示され、各要求で同じであることを確認できます。

## <a name="response-headers-in-production"></a>実稼働環境での応答ヘッダー

前の回答で説明されている CORS プロキシ アプローチは、開発、テスト、学習に適しています。

実稼働環境では、Bing Web Search API を使用する Web ページと同じドメインでサーバー側スクリプトをホストする必要があります。 このスクリプトでは、Web ページの JavaScript からの要求時に API 呼び出しを実行し、ヘッダーを含むすべての結果をクライアントに戻す必要があります。 2 つのリソース (ページとスクリプト) が配信元を共有するので、CORS は使用されず、Web ページ上の JavaScript から特殊なヘッダーにアクセスできます。

API キーはサーバー側スクリプトでのみ必要なので、このアプローチでは API キーもパブリックへの露出から保護されます。 スクリプトでは、別の方法を使用して、要求が承認されているかどうかを確認できます。

Bing による修正候補の例を次に示します。

![Bing による修正候補の例](./media/cognitive-services-bing-web-api/bing-web-spellingsuggestion.GIF)  

## <a name="next-steps"></a>次のステップ  

* [要求のスロットル](throttling-requests.md)に関するドキュメントをご覧ください。  

## <a name="see-also"></a>関連項目  

* [Bing Web Search API リファレンス](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)