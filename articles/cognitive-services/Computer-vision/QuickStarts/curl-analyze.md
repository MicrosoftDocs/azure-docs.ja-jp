---
title: クイック スタート:リモート画像の分析 - REST、cURL
titleSuffix: Azure Cognitive Services
description: このクイック スタートでは、cURL で Computer Vision API を使用してリモート画像を分析します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 2be6625fa2c21c3ece767f270746171c3928a774
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67604452"
---
# <a name="quickstart-analyze-a-remote-image-using-the-computer-vision-rest-api-and-curl"></a>クイック スタート:Computer Vision の REST API と cURL を使用してリモート画像を分析する

このクイック スタートでは、Computer Vision の REST API を使用して、リモートに格納されている画像を分析し、視覚的特徴を抽出します。 [画像分析](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa)メソッドを使うと、画像の内容に基づいて視覚的特徴を抽出できます。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) を作成してください。

## <a name="prerequisites"></a>前提条件

- [cURL](https://curl.haxx.se/windows) が必要です。
- Computer Vision のサブスクリプション キーが必要です。 無料試用版のキーは「[Cognitive Services を試す](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision)」から取得できます。 または、[Cognitive Services アカウントの作成](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)に関するページの手順に従って、Computer Vision をサブスクライブし、キーを取得します。

## <a name="create-and-run-the-sample-command"></a>サンプル コマンドの作成と実行

このサンプルを作成して実行するには、次の手順を実行します。

1. テキスト エディターに次のコマンドをコピーします。
1. 必要に応じて、コマンドに次の変更を加えます。
    1. `<subscriptionKey>` 値を、サブスクリプション キーに置き換えます。
    1. 必要に応じて、サブスクリプション キーを取得した Azure リージョンの[画像分析](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa)メソッドのエンドポイントで要求の URL (`https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/analyze`) を置き換えます。
    1. 必要に応じて、サポートされる別の言語を使用するように、要求 URL の言語のパラメーター (`language=en`) を変更します。
    1. 必要に応じて、要求本文の画像 URL (`http://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg\`) を、分析する別の画像の URL に変更します。
1. コマンド プロンプト ウィンドウを開きます。
1. テキスト エディターからコマンド プロンプト ウィンドウにコマンドを貼り付けて、コマンドを実行します。

```bash
curl -H "Ocp-Apim-Subscription-Key: <subscriptionKey>" -H "Content-Type: application/json" "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/analyze?visualFeatures=Categories,Description&details=Landmarks&language=en" -d "{\"url\":\"http://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg\"}"
```

## <a name="examine-the-response"></a>結果の確認

成功応答が JSON で返されます。 サンプル アプリケーションによって成功応答が解析され、次の例のようにコマンド プロンプト ウィンドウに表示されます。

```json
{
  "categories": [
    {
      "name": "outdoor_water",
      "score": 0.9921875,
      "detail": {
        "landmarks": []
      }
    }
  ],
  "description": {
    "tags": [
      "nature",
      "water",
      "waterfall",
      "outdoor",
      "rock",
      "mountain",
      "rocky",
      "grass",
      "hill",
      "covered",
      "hillside",
      "standing",
      "side",
      "group",
      "walking",
      "white",
      "man",
      "large",
      "snow",
      "grazing",
      "forest",
      "slope",
      "herd",
      "river",
      "giraffe",
      "field"
    ],
    "captions": [
      {
        "text": "a large waterfall over a rocky cliff",
        "confidence": 0.916458423253597
      }
    ]
  },
  "requestId": "b6e33879-abb2-43a0-a96e-02cb5ae0b795",
  "metadata": {
    "height": 959,
    "width": 1280,
    "format": "Jpeg"
  }
}
```

## <a name="next-steps"></a>次の手順

画像の分析、著名人やランドマークの検出、サムネイルの作成、印刷されたテキストや手書きテキストの抽出に使用される Computer Vision API の詳細を確認します。 Computer Vision API を簡単に試す場合は、[Open API テスト コンソール](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console)をお試しください。

> [!div class="nextstepaction"]
> [Computer Vision API の詳細を確認する](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
