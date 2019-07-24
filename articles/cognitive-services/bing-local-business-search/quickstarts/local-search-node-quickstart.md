---
title: クイック スタート - Node.js を使用して Bing Local Business Search API にクエリを送信する | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: Node での Bing Local Business Search API の使用を開始します。
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.topic: article
ms.date: 11/01/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: d649926a60d115c66a2763cef6b02b4aedbb962c
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2019
ms.locfileid: "57761312"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-using-nodejs"></a>クイック スタート: Node.js を使用して Bing Local Business Search API にクエリを送信する

このクイック スタートを利用して、Azure Cognitive Service である Bing Local Business Search API への要求の送信を開始します。 このシンプルなアプリケーションは Node.js で記述されていますが、API は、HTTP 要求の発行と JSON の解析が可能な任意のプログラミング言語と互換性がある RESTful Web サービスです。

このサンプル アプリケーションでは、検索クエリ `hotel in Bellevue` に対する API からのローカルな応答データを取得します。

## <a name="prerequisites"></a>前提条件

* 最新バージョンの [Node.js](https://nodejs.org/en/download/)。

* [JavaScript Request ライブラリ](https://github.com/request/request)

Bing API を使用して [Cognitive Services API アカウント](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)を取得している必要があります。 このクイック スタートには[無料試用版](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)で十分です。 無料試用版で提供されるアクセス キーを使用します。  「[Cognitive Services の価格 - Bing Search API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)」もご覧ください。

## <a name="code-scenario"></a>シナリオのコードを書く

以下のコードで要求を定義して送信します。 これは、次の手順で実装されます。

1. エンドポイントをホストとパスで指定する変数を宣言します。
2. クエリを指定し、クエリ パラメーターを追加します。
3. 応答のハンドラー関数を作成します。
4. 要求を作成し、Ocp-Apim-Subscription-Key ヘッダーを追加する Search 関数を定義します。
5. 検索機能を実行します。

このデモの完全なコードを次に示します。

```
'use strict';

let https = require('https');

// Replace the subscriptionKey string value with your valid subscription key.
let subscriptionKey = 'your-access-key';

let host = 'api.cognitive.microsoft.com/bing';
let path = '/v7.0/localbusinesses/search';

let mkt = 'en-US';
let q = 'hotel in Bellevue';

let params = '?q=' + encodeURI(q) + "&mkt=" + mkt;

let response_handler = function (response) {
    let body = '';
    response.on('data', function (d) {
        body += d;
    });
    response.on('end', function () {
        let body_ = JSON.parse(body);
        let body__ = JSON.stringify(body_, null, '  ');
        console.log(body__);
    });
    response.on('error', function (e) {
        console.log('Error: ' + e.message);
    });
};

let Search = function () {
    let request_params = {
        method: 'GET',
        hostname: host,
        path: path + params,
        headers: {
            'Ocp-Apim-Subscription-Key': subscriptionKey,
        }
    };

    let req = https.request(request_params, response_handler);
    req.end();
}

Search();

```

## <a name="next-steps"></a>次の手順

* [Local Business Search のクイック スタート](local-quickstart.md)
* [Local Business Search (Java) のクイック スタート](local-search-java-quickstart.md)
* [Local Business Search (Python) のクイック スタート](local-search-python-quickstart.md)
