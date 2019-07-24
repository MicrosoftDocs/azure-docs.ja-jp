---
title: クイック スタート:Bing Visual Search REST API と Node.js を使用して画像に関する分析情報を取得する
titleSuffix: Azure Cognitive Services
description: Bing Visual Search API に画像をアップロードし、画像に関する分析情報を取得する方法について説明します。
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 4/02/2019
ms.author: scottwhi
ms.openlocfilehash: 9414bac220d928618b403aa2f7df7748772e0e9a
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/05/2019
ms.locfileid: "59047570"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-nodejs"></a>クイック スタート:Bing Visual Search REST API と Node.js を使用して画像に関する分析情報を取得する

このクイック スタートを使用すると、Bing Visual Search API への最初の呼び出しを行い、検索結果を表示することができます。 このシンプルな JavaScript アプリケーションは、API に画像をアップロードし、それについて返された情報を表示するというものです。 このアプリケーションは JavaScript で記述されていますが、API はほとんどのプログラミング言語と互換性のある RESTful Web サービスです。

ローカルの画像をアップロードする際には、フォーム データに `Content-Disposition` ヘッダーが含まれている必要があります。 その `name` パラメーターは "image" に設定する必要があります。`filename` パラメーターは任意の文字列に設定できます。 フォームの内容には、画像のバイナリ データが含まれます。 アップロードできるイメージの最大サイズは、1 MB です。

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

## <a name="prerequisites"></a>前提条件

* [Node.js](https://nodejs.org/en/download/)
* JavaScript 用の Request モジュール このモジュールは、`npm install request` コマンドを使用してインストールできます。
* form-data モジュール。 このモジュールは、`npm install form-data` コマンドを使用してインストールできます。 

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="initialize-the-application"></a>アプリケーションを初期化する

1. 好みの IDE またはエディターで JavaScript ファイルを作成し、次の要件を設定します。

    ```javascript
    var request = require('request');
    var FormData = require('form-data');
    var fs = require('fs');
    ```

2. API エンドポイント、サブスクリプション キー、および画像へのパスを格納する変数を作成します。

    ```javascript
    var baseUri = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch';
    var subscriptionKey = 'your-api-key';
    var imagePath = "path-to-your-image";
    ```

3. API からの応答を出力するための、`requestCallback()` という関数を作成します。

    ```javascript
    function requestCallback(err, res, body) {
        console.log(JSON.stringify(JSON.parse(body), null, '  '))
    }
    ```

## <a name="construct-and-send-the-search-request"></a>検索要求を構築して送信する

1. `FormData()` を使用して新しい **FormData** オブジェクトを作成し、`fs.createReadStream()` を使用して、そのオブジェクトに画像のパスを追加します。
    
    ```javascript
    var form = new FormData();
    form.append("image", fs.createReadStream(imagePath));
    ```

2. 要求ライブラリを使用して画像をアップロードし、`requestCallback()` を呼び出して応答を出力します。 要求ヘッダーには必ずサブスクリプション キーを追加してください。

    ```javascript
    form.getLength(function(err, length){
      if (err) {
        return requestCallback(err);
      }
      var r = request.post(baseUri, requestCallback);
      r._form = form; 
      r.setHeader('Ocp-Apim-Subscription-Key', subscriptionKey);
    });
    ```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Visual Search のシングルページ Web アプリを作成する](../tutorial-bing-visual-search-single-page-app.md)
