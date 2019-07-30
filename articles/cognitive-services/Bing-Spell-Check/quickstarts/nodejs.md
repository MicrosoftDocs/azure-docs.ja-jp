---
title: クイック スタート:Bing Spell Check REST API と Node.js を使用してスペルをチェックする
titleSuffix: Azure Cognitive Services
description: Bing Spell Check REST API を使用してスペルと文法をチェックしてみましょう。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 04/02/2019
ms.author: aahill
ms.openlocfilehash: c45e9e7743aca2d091b62caed6a7c5a724bacebf
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423505"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-nodejs"></a>クイック スタート:Bing Spell Check REST API と Node.js を使用してスペルをチェックする

このクイック スタートを使用して、Bing Spell Check の REST API を呼び出してみましょう。 このシンプルな Node アプリケーションは、API に要求を送信して、認識されなかった単語とそれに続く修正候補のリストを返します。 このアプリケーションは Node.js で記述されていますが、API はほとんどのプログラミング言語と互換性のある RESTful Web サービスです。 このアプリケーションのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingSpellCheckv7.js) で入手できます。

## <a name="prerequisites"></a>前提条件

* [Node.js 6](https://nodejs.org/en/download/) 以降。

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]


## <a name="create-and-initialize-a-project"></a>プロジェクトの作成と初期化

1. 普段使用している IDE またはエディターで新しい JavaScript ファイルを作成します。 厳格度を設定し、`https` を要求します。 次に、API エンドポイントのホスト、パス、サブスクリプション キーの変数を作成します。

    ```javascript
    'use strict';
    let https = require ('https');

    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/spellcheck';
    let key = '<ENTER-KEY-HERE>';
    ```

2. 検索パラメーターとチェック対象のテキストの変数を作成します。 `mkt=` の後に市場コードを追加します。 市場コードは、要求の送信元となる国です。 さらに、スペルチェック モードを `&mode=` の後に追加します。 モードは `proof` (ほとんどのスペル/文法の誤りが検出されます) または `spell` (スペルの誤りはほとんど検出されますが、文法の誤りの検出数は相対的に少なくなります) のどちらかです。

    ```javascript
    let mkt = "en-US";
    let mode = "proof";
    let text = "Hollo, wrld!";
    let query_string = "?mkt=" + mkt + "&mode=" + mode;
    ```

## <a name="create-the-request-parameters"></a>要求のパラメーターを作成する

`POST` メソッドを含む新しいオブジェクトを作成して、要求のパラメーターを作成します。 実際のエンドポイントのパスとクエリ文字列とを付加したパスを追加してください。 お使いのサブスクリプション キーを `Ocp-Apim-Subscription-Key` ヘッダーに追加します。

```javascript
let request_params = {
   method : 'POST',
   hostname : host,
   path : path + query_string,
   headers : {
   'Content-Type' : 'application/x-www-form-urlencoded',
   'Content-Length' : text.length + 5,
      'Ocp-Apim-Subscription-Key' : key,
   }
};
```

## <a name="create-a-response-handler"></a>応答ハンドラーの作成

API からの JSON 応答を受け取って出力する `response_handler` という関数を作成します。 応答本文の変数を作成します。 `data` フラグを受け取ったら、`response.on()` を使用して応答を追加します。 `end` フラグを受け取ったら、JSON 本文をコンソールに出力します。

```javascript
let response_handler = function (response) {
    let body = '';
    response.on ('data', function (d) {
        body += d;
    });
    response.on ('end', function () {
        let body_ = JSON.parse (body);
        console.log (body_);
    });
    response.on ('error', function (e) {
        console.log ('Error: ' + e.message);
    });
};
```

## <a name="send-the-request"></a>要求を送信する

API の呼び出しには、`https.request()` を使用します。その際、実際の要求のパラメーターと応答ハンドラーを指定します。 目的のテキストを API に書き込んだ後、要求を終了します。

```javascript
let req = https.request (request_params, response_handler);
req.write ("text=" + text);
req.end ();
```

## <a name="example-json-response"></a>JSON の応答例

成功した応答は、次の例に示すように JSON で返されます。

```json
{
   "_type": "SpellCheck",
   "flaggedTokens": [
      {
         "offset": 0,
         "token": "Hollo",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "Hello",
               "score": 0.9115257530801
            },
            {
               "suggestion": "Hollow",
               "score": 0.858039839213461
            },
            {
               "suggestion": "Hallo",
               "score": 0.597385084464481
            }
         ]
      },
      {
         "offset": 7,
         "token": "wrld",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "world",
               "score": 0.9115257530801
            }
         ]
      }
   ]
}
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [シングル ページ Web アプリを作成する](../tutorials/spellcheck.md)

- [Bing Spell Check API とは](../overview.md)
- [Bing Spell Check API v7 リファレンス](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
