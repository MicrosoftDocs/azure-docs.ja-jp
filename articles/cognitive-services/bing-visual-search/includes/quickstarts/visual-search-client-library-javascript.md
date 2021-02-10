---
title: Bing Visual Search JavaScript クライアント ライブラリのクイックスタート
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/26/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: bd4f806013d92dee98bc16c67739748823804bbb
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2021
ms.locfileid: "98948621"
---
このクイックスタートを使用すると、JavaScript クライアント ライブラリを使用して、Bing Visual Search サービスから画像に関する分析情報を取得することができます。 Bing Visual Search の REST API はほとんどのプログラミング言語に対応していますが、このクライアント ライブラリを使用すると、サービスをお使いのアプリケーションに簡単に統合することができます。 このサンプルのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/visualSearch.js) にあります。 

[リファレンスのドキュメント](/javascript/api/@azure/cognitiveservices-visualsearch/) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-visualsearch) | [パッケージ (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-visualsearch) | [サンプル](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/)

## <a name="prerequisites"></a>前提条件

* 最新バージョンの [Node.js](https://nodejs.org/en/download/)。
* [Bing Visual Search SDK for JavaScript](https://www.npmjs.com/package/@azure/cognitiveservices-visualsearch)
     *  インストールするには、`npm install @azure/cognitiveservices-visualsearch` を実行します
* クライアントを認証するための `CognitiveServicesCredentials` クラス (`@azure/ms-rest-azure-js` パッケージに含まれています)。
     * インストールするには、`npm install @azure/ms-rest-azure-js` を実行します

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](~/includes/cognitive-services-bing-visual-search-signup-requirements.md)]

<a name="client"></a>

## <a name="create-and-initialize-the-application"></a>アプリケーションを作成して初期化する

1. 好みの IDE またはエディターで新しい JavaScript ファイルを作成し、次の要件を追加します。 次に、サブスクリプション キー、カスタム構成 ID、およびアップロードする画像へのファイル パス用の変数を作成します。 

    ```javascript
    const os = require("os");
    const async = require('async');
    const fs = require('fs');
    const Search = require('@azure/cognitiveservices-visualsearch');
    const CognitiveServicesCredentials = require('@azure/ms-rest-azure-js').CognitiveServicesCredentials;
    
    let keyVar = 'YOUR-VISUAL-SEARCH-ACCESS-KEY';
    let credentials = new CognitiveServicesCredentials(keyVar);
    let filePath = "../Data/image.jpg";
    ```

2. クライアントをインスタンス化します。

    ```javascript
    let visualSearchClient = new Search.VisualSearchClient(credentials);
    ```

## <a name="search-for-images"></a>画像を検索する

1. `fs.createReadStream()` を使用して画像ファイルを読み取り、検索要求と結果のための変数を作成します。 次に、クライアントを使用して画像を検索します。

    ```javascript
    let fileStream = fs.createReadStream(filePath);
    let visualSearchRequest = JSON.stringify({});
    let visualSearchResults;
    try {
        visualSearchResults = await visualSearchClient.images.visualSearch({
            image: fileStream,
            knowledgeRequest: visualSearchRequest
        });
        console.log("Search visual search request with binary of image");
    } catch (err) {
        console.log("Encountered exception. " + err.message);
    }
    ```

2. 上記のクエリの結果を解析します。

    ```javascript
    // Visual Search results
    if (visualSearchResults.image.imageInsightsToken) {
        console.log(`Uploaded image insights token: ${visualSearchResults.image.imageInsightsToken}`);
    }
    else {
        console.log("Couldn't find image insights token!");
    }
    
    // List of tags
    if (visualSearchResults.tags.length > 0) {
        let firstTagResult = visualSearchResults.tags[0];
        console.log(`Visual search tag count: ${visualSearchResults.tags.length}`);
    
        // List of actions in first tag
        if (firstTagResult.actions.length > 0) {
            let firstActionResult = firstTagResult.actions[0];
            console.log(`First tag action count: ${firstTagResult.actions.length}`);
            console.log(`First tag action type: ${firstActionResult.actionType}`);
        }
        else {
            console.log("Couldn't find tag actions!");
        }
    
    }
    else {
        console.log("Couldn't find image tags!");
    }
    
    ```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [シングルページ Web アプリの作成](../../tutorial-bing-visual-search-single-page-app.md)
