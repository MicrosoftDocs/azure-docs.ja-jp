---
title: Bing News Search JavaScript クライアント ライブラリのクイックスタート
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/12/2020
ms.author: aahi
ms.custom: devx-track-javascript
ms.openlocfilehash: cc96233ea6e2d02f3c3a2036466e3934aa234f5b
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2020
ms.locfileid: "87407977"
---
このクイックスタートでは、Bing News Search の JavaScript 用のクライアント ライブラリを使用してニュースの検索を開始します。 Bing News Search の REST API はほとんどのプログラミング言語に対応していますが、このクライアント ライブラリを使用すると、サービスをお使いのアプリケーションに簡単に統合することができます。 このサンプルのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/newsSearch.js) にあります。

## <a name="prerequisites"></a>前提条件

* [Node.js](https://nodejs.org/en/)

Bing News Search のクライアント ライブラリを使用してコンソール アプリケーションを設定するには、次のようにします。
1. 開発環境で `npm install ms-rest-azure` を実行します。
2. 開発環境で `npm install azure-cognitiveservices-newssearch` を実行します。


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](~/includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>アプリケーションを作成して初期化する

1. `CognitiveServicesCredentials` のインスタンスを作成します。 サブスクリプション キーと検索用語の変数を作成します。

    ```javascript
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
    let search_term = 'Winter Olympics'
    ```

2. クライアントをインスタンス化します。
    
    ```javascript
    const NewsSearchAPIClient = require('azure-cognitiveservices-newssearch');
    let client = new NewsSearchAPIClient(credentials);
    ```

## <a name="send-a-search-query"></a>検索クエリを送信する

1. クライアントを使用して検索語 (この場合は "Winter Olympics") を検索します。
    
    ```javascript
    client.newsOperations.search(search_term).then((result) => {
        console.log(result.value);
    }).catch((err) => {
        throw err;
    });
    ```

このコードでは、テキストを解析せず、コンソールに `result.value` アイテムを出力します。 カテゴリ別の結果がある場合、その結果には次が含まれます。

- `_type: 'NewsArticle'`
- `_type: 'WebPage'`
- `_type: 'VideoObject'`
- `_type: 'ImageObject'`

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [単一ページの Web アプリの作成](../../tutorial-bing-news-search-single-page-app.md)
