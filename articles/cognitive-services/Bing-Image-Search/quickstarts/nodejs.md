---
title: クイック スタート:REST API と Node.js を使用してイメージを検索する - Bing Image Search
titleSuffix: Azure Cognitive Services
description: このクイック スタートを使用して、JavaScript を使って Bing Image Search REST API にイメージ検索要求を送信し、JSON 応答を受信します。
services: cognitive-services
documentationcenter: ''
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: quickstart
ms.date: 12/06/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 2aaed57c7e1d817cd892f45c441ab59d4ffba3d3
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930774"
---
# <a name="quickstart-search-for-images-using-the-bing-image-search-rest-api-and-nodejs"></a>クイック スタート:Bing Image Search REST API と Node.js を使用してイメージを検索する

このクイック スタートでは、Bing Image Search API に検索要求を送信する基本的な方法について説明します。 この JavaScript アプリケーションは、検索クエリを API に送信し、その結果から最初の画像の URL を表示します。 このアプリケーションは JavaScript で記述されていますが、API はほとんどのプログラミング言語と互換性のある RESTful Web サービスです。

このサンプルのソース コードは、追加のエラー処理と注釈を含め、[GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingImageSearchv7Quickstart.js) で入手できます。

## <a name="prerequisites"></a>前提条件

* 最新バージョンの [Node.js](https://nodejs.org/en/download/)。

* [JavaScript Request ライブラリ](https://github.com/request/request)  

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

「[Cognitive Services の価格 - Bing Search API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)」もご覧ください。

## <a name="create-and-initialize-the-application"></a>アプリケーションを作成して初期化する

1. 好みの IDE またはエディターで新しい JavaScript ファイルを作成し、厳格度と https の要件を設定します。

    ```javascript
    'use strict';
    let https = require('https');
    ```

2. API エンドポイント、画像 API 検索パス、サブスクリプション キー、および検索用語の変数を作成します。 `host` には、以下のグローバル エンドポイントを指定するか、Azure portal に表示される、リソースの[カスタム サブドメイン](../../../cognitive-services/cognitive-services-custom-subdomains.md) エンドポイントを指定できます。

    ```javascript
    let subscriptionKey = 'enter key here';
    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/images/search';
    let term = 'tropical ocean';
    ```

## <a name="construct-the-search-request-and-query"></a>検索要求とクエリを構築します。

1. 最後の手順の変数を使用して、API 要求の検索 URL の書式を設定します。 検索語句は、URL エンコードしたうえで API に送信する必要があります。

    ```javascript
    let request_params = {
        method : 'GET',
        hostname : host,
        path : path + '?q=' + encodeURIComponent(search),
        headers : {
        'Ocp-Apim-Subscription-Key' : subscriptionKey,
        }
    };
    ```

2. 要求ライブラリを使用してクエリを API に送信します。 `response_handler` は次のセクションで定義します。
    ```javascript
    let req = https.request(request_params, response_handler);
    req.end();
    ```

## <a name="handle-and-parse-the-response"></a>応答の処理と解析

1. HTTP 呼び出し `response` をパラメーターとして受け取る `response_handler` という名前の関数を定義します。 この関数内で次の手順を実行します。

    1. JSON 応答の本文を含む変数を定義します。  
        ```javascript
        let response_handler = function (response) {
            let body = '';
        };
        ```

    2. **data** フラグが呼び出されたときに応答の本文を格納する
        ```javascript
        response.on('data', function (d) {
            body += d;
        });
        ```

    3. **end** フラグが通知されたら、JSON 応答から最初の結果を取得します。 返された画像の総数と共に、最初の画像の URL を出力します。

        ```javascript
        response.on('end', function () {
            let firstImageResult = imageResults.value[0];
            console.log(`Image result count: ${imageResults.value.length}`);
            console.log(`First image thumbnail url: ${firstImageResult.thumbnailUrl}`);
            console.log(`First image web search url: ${firstImageResult.webSearchUrl}`);
         });
        ```

## <a name="example-json-response"></a>JSON の応答例

Bing Image Search API からの応答は、JSON として返されます。 このサンプル応答は、1 つの結果だけを表示するように切り詰められています。

```json
{
"_type":"Images",
"instrumentation":{
    "_type":"ResponseInstrumentation"
},
"readLink":"images\/search?q=tropical ocean",
"webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=tropical ocean&FORM=OIIARP",
"totalEstimatedMatches":842,
"nextOffset":47,
"value":[
    {
        "webSearchUrl":"https:\/\/www.bing.com\/images\/search?view=detailv2&FORM=OIIRPO&q=tropical+ocean&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&simid=608027248313960152",
        "name":"My Life in the Ocean | The greatest WordPress.com site in ...",
        "thumbnailUrl":"https:\/\/tse3.mm.bing.net\/th?id=OIP.fmwSKKmKpmZtJiBDps1kLAHaEo&pid=Api",
        "datePublished":"2017-11-03T08:51:00.0000000Z",
        "contentUrl":"https:\/\/mylifeintheocean.files.wordpress.com\/2012\/11\/tropical-ocean-wallpaper-1920x12003.jpg",
        "hostPageUrl":"https:\/\/mylifeintheocean.wordpress.com\/",
        "contentSize":"897388 B",
        "encodingFormat":"jpeg",
        "hostPageDisplayUrl":"https:\/\/mylifeintheocean.wordpress.com",
        "width":1920,
        "height":1200,
        "thumbnail":{
        "width":474,
        "height":296
        },
        "imageInsightsToken":"ccid_fmwSKKmK*mid_8607ACDACB243BDEA7E1EF78127DA931E680E3A5*simid_608027248313960152*thid_OIP.fmwSKKmKpmZtJiBDps1kLAHaEo",
        "insightsMetadata":{
        "recipeSourcesCount":0,
        "bestRepresentativeQuery":{
            "text":"Tropical Beaches Desktop Wallpaper",
            "displayText":"Tropical Beaches Desktop Wallpaper",
            "webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=Tropical+Beaches+Desktop+Wallpaper&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&FORM=IDBQDM"
        },
        "pagesIncludingCount":115,
        "availableSizesCount":44
        },
        "imageId":"8607ACDACB243BDEA7E1EF78127DA931E680E3A5",
        "accentColor":"0050B2"
    }]
}
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [単一ページのアプリを作成する](../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>関連項目

* [Bing Image Search とは](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [オンラインの対話型デモを試す](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/) 
* Bing Search API シリーズの[価格の詳細](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/) 
* [無料の Cognitive Services アクセス キーを取得する](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)  
* [Azure Cognitive Services のドキュメント](https://docs.microsoft.com/azure/cognitive-services)
* [Bing Image Search API リファレンス](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
