---
title: クイック スタート:Node.js を使用して Bing Custom Search エンドポイントを呼び出す | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: このクイック スタートでは、Node.js を使用して、Bing Custom Search インスタンスに検索結果を要求します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 05/15/2019
ms.author: aahi
ms.openlocfilehash: 3703658d6cab78592c50a23deaf9da2a426aebec
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405174"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-nodejs"></a>クイック スタート:Node.js を使用して Bing Custom Search エンドポイントを呼び出す

このクイック スタートでは、Bing Custom Search インスタンスに検索結果を要求します。 このアプリケーションは JavaScript で記述されていますが、Bing Custom Search API はほとんどのプログラミング言語と互換性のある RESTful Web サービスです。 このサンプルのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingCustomSearchv7.js) で入手できます。

## <a name="prerequisites"></a>前提条件

- Bing Custom Search インスタンス。 「[クイック スタート:初めての Bing Custom Search インスタンスを作成する](quick-start.md)」で詳細を確認する。

- [Node.JS](https://www.nodejs.org/)

- [JavaScript Request ライブラリ](https://github.com/request/request)

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>アプリケーションを作成して初期化する

1. 任意の IDE またはエディターで新しい JavaScript ファイルを作成し、requests ライブラリの `require()` ステートメントを追加します。 サブスクリプション キー、カスタム構成 ID、検索語句に使用する変数を作成します。 

    ```javascript
    var request = require("request");
    
    var subscriptionKey = 'YOUR-SUBSCRIPTION-KEY';
    var customConfigId = 'YOUR-CUSTOM-CONFIG-ID';
    var searchTerm = 'microsoft';
    ```

## <a name="send-and-receive-a-search-request"></a>検索要求の送信と受信 

1. 要求に含めて送信する情報を格納する変数を作成します。 検索語句を `q=` クエリ パラメーターに、また検索インスタンスのカスタム構成 ID を `customconfig=` に追加して要求 URL を作成します。 パラメーターの区切りには、`&` 文字を使用します。 

    ```javascript
    var info = {
        url: 'https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?' + 
            'q=' + searchTerm + "&" +
            'customconfig=' + customConfigId,
        headers: {
            'Ocp-Apim-Subscription-Key' : subscriptionKey
        }
    }
    ```

1. JavaScript Request ライブラリを使用して、検索要求を Bing Custom Search インスタンスに送信し、その結果についての情報 (名前、URL、Web ページが最後にクロールされた日付など) を出力します。

    ```javascript
    request(info, function(error, response, body){
            var searchResponse = JSON.parse(body);
            for(var i = 0; i < searchResponse.webPages.value.length; ++i){
                var webPage = searchResponse.webPages.value[i];
                console.log('name: ' + webPage.name);
                console.log('url: ' + webPage.url);
                console.log('displayUrl: ' + webPage.displayUrl);
                console.log('snippet: ' + webPage.snippet);
                console.log('dateLastCrawled: ' + webPage.dateLastCrawled);
                console.log();
            }
    ```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Custom Search Web アプリの作成](./tutorials/custom-search-web-page.md)
