---
title: クイック スタート:サムネイルの生成 - REST、Node.js
titleSuffix: Azure Cognitive Services
description: このクイック スタートでは、Computer Vision API と Node.js を使って、画像からサムネイルを生成します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 03/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: c10be50adbfae559873da87fffe4cdb10bc350ef
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57875840"
---
# <a name="quickstart-generate-a-thumbnail-using-the-rest-api-and-nodejs-in-computer-vision"></a>クイック スタート: Computer Vision の REST API と Node.js を使用したサムネイルの生成

このクイック スタートでは、Computer Vision の REST API を使って、画像からサムネイルを生成します。 [サムネイル取得](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb)メソッドを使うと、画像のサムネイルを生成することができます。 その際に指定する高さと幅は、入力画像の縦横比と異なっていてもかまいません。 Computer Vision では、スマート トリミングを使ってインテリジェントに関心領域を識別し、その領域に基づいてトリミングの座標を生成します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) を作成してください。

## <a name="prerequisites"></a>前提条件

- [Node.js](https://nodejs.org) 4.x 以降がインストールされている必要があります。
- [npm](https://www.npmjs.com/) がインストールされている必要があります。
- Computer Vision のサブスクリプション キーが必要です。 「[サブスクリプション キーを取得する](../Vision-API-How-to-Topics/HowToSubscribe.md)」をご覧ください。

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
1. 必要に応じて、コードに次の変更を加えます。
    1. `subscriptionKey` 値を、サブスクリプション キーに置き換えます。
    1. 必要に応じて、サブスクリプション キーを取得した Azure リージョンの[サムネイル取得](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb)メソッドのエンドポイント URL で `uriBase` 値を置き換えます。
    1. 必要に応じて、分析したい別の画像の URL で `imageUrl` 値を置き換えます。
1. `.js` 拡張子のファイルとして、コードを保存します。 たとえば、「 `get-thumbnail.js` 」のように入力します。
1. コマンド プロンプト ウィンドウを開きます。
1. プロンプトで、`node` コマンドを使用してファイルを実行します。 たとえば、「 `node get-thumbnail.js` 」のように入力します。

```javascript
'use strict';

const request = require('request');

// Replace <Subscription Key> with your valid subscription key.
const subscriptionKey = '<Subscription Key>';

// You must use the same location in your REST call as you used to get your
// subscription keys. For example, if you got your subscription keys from
// westus, replace "westcentralus" in the URL below with "westus".
const uriBase =
    'https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/generateThumbnail';

const imageUrl =
    'https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg';

// Request parameters.
const params = {
    'width': '100',
    'height': '100',
    'smartCropping': 'true'
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
});
```

## <a name="examine-the-response"></a>結果の確認

成功応答は、サムネイル画像データを表すバイナリ データとして返されます。 要求が失敗した場合、応答がコンソール ウィンドウに表示されます。 失敗した要求の応答には、原因の特定につながるエラー コードとメッセージが格納されています。

## <a name="next-steps"></a>次の手順

画像の分析、著名人やランドマークの検出、サムネイルの作成、印刷されたテキストや手書きテキストの抽出に使用される Computer Vision API の詳細を確認します。 Computer Vision API を簡単に試す場合は、[Open API テスト コンソール](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console)をお試しください。

> [!div class="nextstepaction"]
> [Computer Vision API の詳細を確認する](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
