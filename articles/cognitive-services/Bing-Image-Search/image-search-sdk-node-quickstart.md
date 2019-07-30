---
title: クイック スタート:イメージを検索する - Bing Image Search SDK for Node.js
titleSuffix: Azure Cognitive Services
description: このクイック スタートを利用し、Bing Image Search SDK を使用して最初の画像検索を行います。この SDK は、API のラッパーであり、同じ機能を含んでいます。 このシンプルな Node.js アプリケーションは、イメージ検索クエリを送信し、JSON 応答を解析して、返された最初のイメージの URL を表示します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: quickstart
ms.date: 03/04/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 24f6de759f233b82cb19a21401d3886a80bd746f
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423894"
---
# <a name="quickstart-search-for-images-with-the-bing-image-search-sdk-for-nodejs"></a>クイック スタート:Bing Image Search SDK for Node.js を使用してイメージを検索する

このクイック スタートを利用し、Bing Image Search SDK を使用して最初の画像検索を行います。この SDK は、API のラッパーであり、同じ機能を含んでいます。 このシンプルな JavaScript アプリケーションは、イメージ検索クエリを送信し、JSON 応答を解析して、返された最初のイメージの URL を表示します。

このサンプルのソース コードは、追加のエラー処理と注釈を含め、[GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/imageSearch.js) で入手できます。

## <a name="prerequisites"></a>前提条件
**[検索]** で [Cognitive Services のアクセス キー](https://azure.microsoft.com/try/cognitive-services/)を取得します。  「[Cognitive Services の価格 - Bing Search API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)」もご覧ください。  「[Cognitive Services の価格 - Bing Search API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)」もご覧ください。

* [Node.js 用の Cognitive Services Image Search SDK](https://www.npmjs.com/package/azure-cognitiveservices-imagesearch)
    * `npm install azure-cognitiveservices-imagesearch` を使用してインストールする
* [Node.js Azure Rest](https://www.npmjs.com/package/ms-rest-azure) モジュール
    * `npm install ms-rest-azure` を使用してインストールする

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>アプリケーションを作成して初期化する

1. 好みの IDE またはエディターで新しい JavaScript ファイルを作成し、厳格度、https、およびその他の要件を設定します。

    ```javascript
    'use strict';
    const ImageSearchAPIClient = require('azure-cognitiveservices-imagesearch');
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    ```

2. プロジェクトの main メソッドでは、有効なサブスクリプション キー、Bing で返す必要のあるイメージの結果、および検索語句の変数を作成します。 その後、キーを使用してイメージ検索クライアントをインスタンス化します。

    ```javascript
    //replace this value with your valid subscription key.
    let serviceKey = "ENTER YOUR KEY HERE";

    //the search term for the request
    let searchTerm = "canadian rockies";

    //instantiate the image search client 
    let credentials = new CognitiveServicesCredentials(serviceKey);
    let imageSearchApiClient = new ImageSearchAPIClient(credentials);

    ```

## <a name="create-an-asynchronous-helper-function"></a>非同期ヘルパー関数を作成する

1. クライアントを非同期に呼び出す関数を作成し、Bing Image Search サービスから応答を返します。  
    ```javascript
    //a helper function to perform an async call to the Bing Image Search API
    const sendQuery = async () => {
        return await imageSearchApiClient.imagesOperations.search(searchTerm);
    };
    ```
   ## <a name="send-a-query-and-handle-the-response"></a>クエリを送信して応答を処理する

1. ヘルパー関数を呼び出して `promise` を処理し、応答で返されたイメージの結果を解析します。

    応答に検索結果が含まれている場合は、最初の結果を格納して、返された URL の合計数と共にサムネイルの URL などの詳細を出力します。  
    ```javascript
    sendQuery().then(imageResults => {
        if (imageResults == null) {
        console.log("No image results were found.");
        }
        else {
            console.log(`Total number of images returned: ${imageResults.value.length}`);
            let firstImageResult = imageResults.value[0];
            //display the details for the first image result. After running the application,
            //you can copy the resulting URLs from the console into your browser to view the image.
            console.log(`Total number of images found: ${imageResults.value.length}`);
            console.log(`Copy these URLs to view the first image returned:`);
            console.log(`First image thumbnail url: ${firstImageResult.thumbnailUrl}`);
            console.log(`First image content url: ${firstImageResult.contentUrl}`);
        }
      })
      .catch(err => console.error(err))
    ```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Bing Image Search の単一ページ アプリのチュートリアル](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app)

## <a name="see-also"></a>関連項目

* [Bing Image Search とは](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [オンラインの対話型デモを試す](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [無料の Cognitive Services アクセス キーを取得する](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)
* [Azure Cognitive Services SDK の Node.js サンプル](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)
* [Azure Cognitive Services のドキュメント](https://docs.microsoft.com/azure/cognitive-services)
* [Bing Image Search API リファレンス](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
