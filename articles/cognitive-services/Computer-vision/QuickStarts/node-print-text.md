---
title: クイック スタート:印刷されたテキストの抽出 - REST、Node.js
titleSuffix: Azure Cognitive Services
description: このクイック スタートでは、Node.js と Computer Vision API を使って、画像内の印刷されたテキストを抽出します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 08/05/2020
ms.author: pafarley
ms.custom: seodec18, devx-track-javascript
ms.openlocfilehash: 4f0a895795c9a50e3ec81632b2d57329dbe4777d
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87834985"
---
# <a name="quickstart-extract-printed-text-ocr-using-the-computer-vision-rest-api-and-nodejs"></a>クイック スタート:Computer Vision の REST API と Node.js を使用して印刷されたテキスト (OCR) を抽出する

> [!NOTE]
> 英語のテキストを抽出する場合は、新しい[読み取り操作](https://docs.microsoft.com/azure/cognitive-services/computer-vision/concept-recognizing-text)の使用を検討してください。

このクイックスタートでは、Computer Vision の REST API を使用して、光学式文字認識 (OCR) で印刷されたテキストを抽出します。 [OCR](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) メソッドを使って画像内の印刷されたテキストを検出し、認識した文字をマシンで扱うことができる文字ストリームに抽出します。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services/)
* [Node.js](https://nodejs.org) 4.x 以降 
* [npm](https://www.npmjs.com/) 
* Azure サブスクリプションを入手したら、Azure portal で <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="Computer Vision リソースを作成"  target="_blank">Computer Vision リソースを作成<span class="docon docon-navigate-external x-hidden-focus"></span></a>し、キーとエンドポイントを取得します。 デプロイされたら、 **[リソースに移動]** をクリックします。
    * 対象のアプリケーションを Computer Vision サービスに接続するには、作成したリソースのキーとエンドポイントが必要です。 このクイックスタートで後に示すコードに、自分のキーとエンドポイントを貼り付けます。
    * Free 価格レベル (`F0`) を使用してサービスを試用し、後から運用環境用の有料レベルにアップグレードすることができます。
* キーとエンドポイント URL 用に、それぞれ `COMPUTER_VISION_SUBSCRIPTION_KEY` と `COMPUTER_VISION_ENDPOINT` という名前の[環境変数を作成](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)します。

## <a name="create-and-run-the-sample"></a>サンプルの作成と実行

このサンプルを作成して実行するには、次の手順を実行します。

1. npm [`request`](https://www.npmjs.com/package/request) パッケージをインストールします。
   1. 管理者としてコマンド プロンプト ウィンドウを開きます。
   1. 次のコマンドを実行します。

      ```console
      npm install request
      ```

   1. パッケージが正常にインストールされたら、コマンド プロンプト ウィンドウを閉じます。

1. テキスト エディターに次のコードをコピーします。
1. 必要に応じて、印刷されたテキストを抽出したい別の画像の URL で `imageUrl` 値を置き換えます。
1. `.js` 拡張子のファイルとして、コードを保存します。 たとえば、「 `get-printed-text.js` 」のように入力します。
1. コマンド プロンプト ウィンドウを開きます。
1. プロンプトで、`node` コマンドを使用してファイルを実行します。 たとえば、「 `node get-printed-text.js` 」のように入力します。

```javascript
'use strict';

const request = require('request');

let subscriptionKey = process.env['COMPUTER_VISION_SUBSCRIPTION_KEY'];
let endpoint = process.env['COMPUTER_VISION_ENDPOINT']
if (!subscriptionKey) { throw new Error('Set your environment variables for your subscription key and endpoint.'); }

var uriBase = endpoint + 'vision/v3.0/ocr';

const imageUrl = 'https://upload.wikimedia.org/wikipedia/commons/thumb/a/af/' +
    'Atomist_quote_from_Democritus.png/338px-Atomist_quote_from_Democritus.png';

// Request parameters.
const params = {
    'language': 'unk',
    'detectOrientation': 'true',
};

const options = {
    uri: uriBase,
    qs: params,
    body: '{"url": ' + '"' + imageUrl + '"}',
    headers: {
        'Content-Type': 'application/json',
        'Ocp-Apim-Subscription-Key' : subscriptionKey
    }
};

request.post(options, (error, response, body) => {
  if (error) {
    console.log('Error: ', error);
    return;
  }
  let jsonResponse = JSON.stringify(JSON.parse(body), null, '  ');
  console.log('JSON Response\n');
  console.log(jsonResponse);
});
```

## <a name="examine-the-response"></a>結果の確認

成功応答が JSON で返されます。 サンプルによって成功応答が解析され、次の例のようにコマンド プロンプト ウィンドウに表示されます。

```json
{
  "language": "en",
  "orientation": "Up",
  "textAngle": 0,
  "regions": [
    {
      "boundingBox": "21,16,304,451",
      "lines": [
        {
          "boundingBox": "28,16,288,41",
          "words": [
            {
              "boundingBox": "28,16,288,41",
              "text": "NOTHING"
            }
          ]
        },
        {
          "boundingBox": "27,66,283,52",
          "words": [
            {
              "boundingBox": "27,66,283,52",
              "text": "EXISTS"
            }
          ]
        },
        {
          "boundingBox": "27,128,292,49",
          "words": [
            {
              "boundingBox": "27,128,292,49",
              "text": "EXCEPT"
            }
          ]
        },
        {
          "boundingBox": "24,188,292,54",
          "words": [
            {
              "boundingBox": "24,188,292,54",
              "text": "ATOMS"
            }
          ]
        },
        {
          "boundingBox": "22,253,297,32",
          "words": [
            {
              "boundingBox": "22,253,105,32",
              "text": "AND"
            },
            {
              "boundingBox": "144,253,175,32",
              "text": "EMPTY"
            }
          ]
        },
        {
          "boundingBox": "21,298,304,60",
          "words": [
            {
              "boundingBox": "21,298,304,60",
              "text": "SPACE."
            }
          ]
        },
        {
          "boundingBox": "26,387,294,37",
          "words": [
            {
              "boundingBox": "26,387,210,37",
              "text": "Everything"
            },
            {
              "boundingBox": "249,389,71,27",
              "text": "else"
            }
          ]
        },
        {
          "boundingBox": "127,431,198,36",
          "words": [
            {
              "boundingBox": "127,431,31,29",
              "text": "is"
            },
            {
              "boundingBox": "172,431,153,36",
              "text": "opinion."
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

不要になったら、ファイルを削除して、npm `request` パッケージをアンインストールします。 パッケージをアンインストールするには、次の手順を実行します。

1. 管理者としてコマンド プロンプト ウィンドウを開きます。
2. 次のコマンドを実行します。

   ```console
   npm uninstall request
   ```

3. パッケージが正常にアンインストールされたら、コマンド プロンプト ウィンドウを閉じます。

## <a name="next-steps"></a>次のステップ

次は、画像の分析、著名人やランドマークの検出、サムネイルの作成、印刷されたテキストや手書きテキストの抽出に使用される Computer Vision API の詳細を確認します。

> [!div class="nextstepaction"]
> [Computer Vision API の詳細を確認する](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
