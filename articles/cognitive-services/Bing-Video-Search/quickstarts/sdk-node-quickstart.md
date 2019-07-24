---
title: クイック スタート:Bing Video Search SDK for Node.js を使用して動画を検索する
titleSuffix: Azure Cognitive Services
description: このクイック スタートでは、Bing Video Search SDK for Node.js を使用して動画の検索要求を送信します。
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 01/31/2019
ms.author: rosh
ms.openlocfilehash: ffd03a900ec304a930dfc64e7860e28e0374da55
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2019
ms.locfileid: "55863757"
---
# <a name="quickstart-perform-a-video-search-with-the-bing-video-search-sdk-for-nodejs"></a>クイック スタート:Bing Video Search SDK for Node.js で動画の検索を実行する

このクイック スタートを使用して、Bing Video Search SDK for Node.js を使ったニュース検索を始めましょう。 Bing Video Search には、ほとんどのプログラミング言語に対応した REST API がありますが、SDK を使用すれば、アプリケーションに対して簡単にサービスを統合することができます。 このサンプルのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/videoSearch.js) にあります。 そちらの方が注釈や機能が詳しく記載されています。

## <a name="prerequisites"></a>前提条件

- [Node.js](https://www.nodejs.org/)

Bing Video Search SDK を使用してコンソール アプリケーションを設定するには:
* 開発環境で `npm install ms-rest-azure` を実行します。
* 開発環境で `npm install azure-cognitiveservices-videosearch` を実行します。

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>アプリケーションを作成して初期化する

1. 任意の IDE またはエディターで新しい JavaScript ファイルを作成し、`CognitiveServicesCredentials` モジュールと Bing Video Search SDK の `require()` ステートメントを追加します。 サブスクリプション キーの変数を作成します。 
    
    ```javascript
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    const VideoSearchAPIClient = require('azure-cognitiveservices-videosearch');
    ```

2. 実際のキーを指定して `CognitiveServicesCredentials` のインスタンスを作成します。 さらに、それを使用して動画検索クライアントのインスタンスを作成します。

    ```javascript
    let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
    let client = new VideoSearchAPIClient(credentials);
    ```

## <a name="send-the-search-request"></a>検索要求を送信する

1. `client.videosOperations.search()` を使用して、Bing Video Search API に検索要求を送信します。 検索結果が返されたら、`.then()` を使用して結果をログします。
    
    ```javascript
    client.videosOperations.search('Interstellar Trailer').then((result) => {
        console.log(result.value);
    }).catch((err) => {
        throw err;
    });
    ```

<!-- Remove until the response can be replace with a sanitized version.
The code prints `result.value` items to the console without parsing any text. The results will be:
- _type: 'VideoObjectElementType'

![Video results](media/video-search-sdk-node-results.png)
-->

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [シングル ページ Web アプリを作成する](../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>関連項目 

* [Bing Video Search API とは](../overview.md)
* [Cognitive Services の .NET 向け SDK のサンプル](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)