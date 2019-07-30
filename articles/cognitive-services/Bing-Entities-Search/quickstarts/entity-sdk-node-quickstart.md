---
title: クイック スタート:Bing Entity Search SDK for Node.js を使用して検索要求を送信する
titleSuffix: Azure Cognitive Services
description: Bing Entity Search SDK for Node.js を使用してエンティティを検索する場合は、このクイック スタートを使用します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 07/24/2019
ms.author: aahi
ms.openlocfilehash: 40c9062dba5eb3bbed6ee90bfdb0a74c1d6c11d5
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479032"
---
# <a name="quickstart-send-a-search-request-with-the-bing-entity-search-sdk-for-nodejs"></a>クイック スタート:Bing Entity Search SDK for Node.js を使用して検索要求を送信する

このクイック スタートを使用すると、Bing Entity Search SDK for Node.js でエンティティの検索を始めることができます。 Bing Entity Search には、ほとんどのプログラミング言語に対応した REST API がありますが、SDK を使用すれば、アプリケーションに対して簡単にサービスを統合することができます。 このサンプルのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/entitySearch.js) にあります。

## <a name="prerequisites"></a>前提条件

* 最新バージョンの [Node.js](https://nodejs.org/en/download/)。

* [Bing Entity Search SDK for Node.js](https://www.npmjs.com/package/azure-cognitiveservices-entitysearch)

Bing Entity Search SDK をインストールするには:

1. 開発環境で `npm install ms-rest-azure` を実行します。
2. 開発環境で `npm install azure-cognitiveservices-entitysearch` を実行します。

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>アプリケーションを作成して初期化する

1. 好みの IDE またはエディターで新しい JavaScript ファイルを作成し、次の要件を追加します。 
    
    ```javascript
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    const EntitySearchAPIClient = require('azure-cognitiveservices-entitysearch');
    ```

2. サブスクリプション キーを使用して、`CognitiveServicesCredentials` のインスタンスを作成します。 その後、それで検索クライアントのインスタンスを作成します。

    ```javascript
    let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
    let entitySearchApiClient = new EntitySearchAPIClient(credentials);
    ```

## <a name="send-a-request-and-receive-a-response"></a>要求を送信して応答を受信する

1. `entitiesOperations.search()` を使用してエンティティの検索要求を送信します。 応答を受け取った後、`queryContext`、返された結果の数、最初の結果の説明を表示します。
      
    ```javascript
    entitySearchApiClient.entitiesOperations.search('seahawks').then((result) => {
        console.log(result.queryContext);
        console.log(result.entities.value);
        console.log(result.entities.value[0].description);
    }).catch((err) => {
        throw err;
    });
    ```

<!-- Removing until we can replace with a sanitized version.
![Entity results](media/entity-search-sdk-node-quickstart-results.png)
-->

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [単一ページの Web アプリの作成](../tutorial-bing-entities-search-single-page-app.md)

* [Bing Entity Search API とは](../overview.md )