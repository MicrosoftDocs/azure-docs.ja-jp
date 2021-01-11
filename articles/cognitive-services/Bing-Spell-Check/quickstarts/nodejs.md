---
title: クイック スタート:REST API と Node.js を使用してスペルをチェックする - Bing Spell Check
titleSuffix: Azure Cognitive Services
description: このクイックスタートで、Bing Spell Check REST API を使用してスペルと文法をチェックしてみましょう。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 05/21/2020
ms.author: aahi
ms.custom: devx-track-javascript
ms.openlocfilehash: aaaa571928556a6972d3136ef4cacaa3bd4cb798
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2020
ms.locfileid: "87405009"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-nodejs"></a>クイック スタート:Bing Spell Check REST API と Node.js を使用してスペルをチェックする

このクイック スタートを使用して、Bing Spell Check の REST API を呼び出してみましょう。 このシンプルな JavaScript アプリケーションは、API に要求を送信して、一連の修正候補を返します。 

このアプリケーションは JavaScript で記述されていますが、この API はほとんどのプログラミング言語と互換性のある RESTful Web サービスです。 このアプリケーションのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingSpellCheckv7.js) で入手できます。

## <a name="prerequisites"></a>前提条件

* [Node.js 6](https://nodejs.org/en/download/) 以降。

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]


## <a name="create-and-initialize-a-project"></a>プロジェクトの作成と初期化

1. 普段使用している IDE またはエディターで新しい JavaScript ファイルを作成します。 厳格度を設定し、`https` を要求します。 次に、API エンドポイントのホスト、パス、サブスクリプション キーの変数を作成します。 次のコードのグローバル エンドポイントを使用するか、Azure portal に表示される、対象のリソースの[カスタム サブドメイン](../../../cognitive-services/cognitive-services-custom-subdomains.md) エンドポイントを使用することができます。

    ```javascript
    'use strict';
    let https = require ('https');

    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/spellcheck';
    let key = '<ENTER-KEY-HERE>';
    ```

2. 検索パラメーターとチェック対象のテキストの変数を作成します。 

   1. `=` 演算子を使用して、`mkt` パラメーターに市場コードを割り当てます。 市場コードは、要求の送信元となる国/地域のコードです。 

   1. `&` 演算子を使用して `mode` パラメーターを追加し、スペルチェック モードを割り当てます。 モードは `proof` (スペルまたは文法のほとんどのエラーが検出されます)、または `spell` (スペル ミスはほとんど検出されますが、文法エラーの検出数は相対的に少なくなります) のいずれかにすることができます。

    ```javascript
    let mkt = "en-US";
    let mode = "proof";
    let text = "Hollo, wrld!";
    let query_string = "?mkt=" + mkt + "&mode=" + mode;
    ```

## <a name="create-the-request-parameters"></a>要求のパラメーターを作成する

`POST` メソッドを含む新しいオブジェクトを作成して、要求のパラメーターを作成します。 実際のエンドポイントのパスとクエリ文字列とを付加したパスを追加してください。 次に、お使いのサブスクリプション キーを `Ocp-Apim-Subscription-Key` ヘッダーに追加します。

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

API からの JSON 応答を受け取って出力する `response_handler` という関数を作成します。 応答本文の変数を作成します。 `data` フラグを受け取る際に、`response.on()` を使用して応答を追加します。 `end` フラグを受け取ったら、JSON 本文をコンソールに出力します。

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

API の呼び出しには、`https.request()` を使用します。その際、実際の要求のパラメーターと応答ハンドラーを指定します。 目的のテキストを API に書き込んだら、要求を終了します。

```javascript
let req = https.request (request_params, response_handler);
req.write ("text=" + text);
req.end ();
```


## <a name="run-the-application"></a>アプリケーションの実行

1. プロジェクトをビルドして実行します。

1. コマンド ラインを使用している場合は、次のコマンドを使用して、アプリケーションをビルドおよび実行します。

   ```bash
   node <FILE_NAME>.js
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

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [単一ページの Web アプリの作成](../tutorials/spellcheck.md)

- [Bing Spell Check API とは](../overview.md)
- [Bing Spell Check API v7 リファレンス](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
