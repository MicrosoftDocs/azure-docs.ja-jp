---
title: Face REST API クイックスタート
description: cURL で Face REST API を使用して、顔の検出、類似検索 (画像による顔検索)、顔の識別 (顔認識検索)、顔データの移行を行います。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: include
ms.date: 12/06/2020
ms.author: pafarley
ms.openlocfilehash: 5523d89548ba08dfc5a43f5c21b5a12a5e5955c3
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102510883"
---
Face REST API を使用して顔認識を開始します。 Face サービスは、画像内の人間の顔を検出および認識するための高度なアルゴリズムへのアクセスを提供します。

Face REST API を使用して、次のことを行います。

* [画像内の顔を検出する](#detect-faces-in-an-image)
* [似た顔を探す](#find-similar-faces)

> [!NOTE]
> このクイックスタートでは、cURL コマンドを使用して REST API を呼び出します。 また、プログラミング言語を使用して REST API を呼び出すこともできます。 GitHub のサンプルを参照して、[C#](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/Face/rest)、[Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/Face/rest)、[Java](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/java/Face/rest)、[JavaScript](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/javascript/Face/rest)、[Go](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/go/Face/rest) の例をご確認ください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services/)
* Azure サブスクリプションを入手したら、Azure portal で <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title="Face リソースを作成"  target="_blank">Face リソースを作成</a>し、キーとエンドポイントを取得します。 デプロイされたら、 **[リソースに移動]** をクリックします。
    * 対象のアプリケーションを Face API に接続するには、作成したリソースのキーとエンドポイントが必要です。 このクイックスタートで後に示すコードに、自分のキーとエンドポイントを貼り付けます。
    * Free 価格レベル (`F0`) を使用してサービスを試用し、後から運用環境用の有料レベルにアップグレードすることができます。
* [PowerShell バージョン 6.0 以降](https://docs.microsoft.com/powershell/scripting/install/installing-powershell-core-on-windows)、または同様のコマンド ライン アプリケーション。


## <a name="detect-faces-in-an-image"></a>画像内の顔を検出する

Face API を呼び出して、顔の属性データを画像から取得するには、次のようなコマンドを使用します。 まず、テキスト エディターにコードをコピーしてください。実行する前に、コマンドの一部に変更を加える必要があります。

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="detection_model_2":::

次の変更を行います。
1. `Ocp-Apim-Subscription-Key` を、有効な Face サブスクリプション キーに割り当てます。
1. このクエリ URL の最初の部分を、ご自分のサブスクリプション キーに対応するエンドポイントに合わせて変更します。
   [!INCLUDE [subdomains-note](../../../../../includes/cognitive-services-custom-subdomains-note.md)]
1. 必要に応じて、別の画像を指すように要求の本文の URL を変更します。

変更を加えたら、コマンド プロンプトを開いて新しいコマンドを入力します。 

### <a name="examine-the-results"></a>結果を確認する

コンソール ウィンドウに、顔の情報が JSON データとして表示されます。 次に例を示します。

```json
[
  {
    "faceId": "49d55c17-e018-4a42-ba7b-8cbbdfae7c6f",
    "faceRectangle": {
      "top": 131,
      "left": 177,
      "width": 162,
      "height": 162
    }
  }
]  
```

### <a name="get-face-attributes"></a>顔の属性の取得
 
顔の属性を抽出するには、Detect API をもう一度呼び出します。ただし、`detectionModel` を `detection_01` に設定します。 `returnFaceAttributes` クエリ パラメーターも追加します。 このコマンドは、次のようになります。 先ほどと同様、Face サブスクリプション キーとエンドポイントを挿入します。

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="detection_model_1":::

### <a name="examine-the-results"></a>結果を確認する

これで、返される顔の情報に顔の属性が追加されます。 次に例を示します。

```json
[
  {
    "faceId": "49d55c17-e018-4a42-ba7b-8cbbdfae7c6f",
    "faceRectangle": {
      "top": 131,
      "left": 177,
      "width": 162,
      "height": 162
    },
    "faceAttributes": {
      "smile": 0,
      "headPose": {
        "pitch": 0,
        "roll": 0.1,
        "yaw": -32.9
      },
      "gender": "female",
      "age": 22.9,
      "facialHair": {
        "moustache": 0,
        "beard": 0,
        "sideburns": 0
      },
      "glasses": "NoGlasses",
      "emotion": {
        "anger": 0,
        "contempt": 0,
        "disgust": 0,
        "fear": 0,
        "happiness": 0,
        "neutral": 0.986,
        "sadness": 0.009,
        "surprise": 0.005
      },
      "blur": {
        "blurLevel": "low",
        "value": 0.06
      },
      "exposure": {
        "exposureLevel": "goodExposure",
        "value": 0.67
      },
      "noise": {
        "noiseLevel": "low",
        "value": 0
      },
      "makeup": {
        "eyeMakeup": true,
        "lipMakeup": true
      },
      "accessories": [],
      "occlusion": {
        "foreheadOccluded": false,
        "eyeOccluded": false,
        "mouthOccluded": false
      },
      "hair": {
        "bald": 0,
        "invisible": false,
        "hairColor": [
          {
            "color": "brown",
            "confidence": 1
          },
          {
            "color": "black",
            "confidence": 0.87
          },
          {
            "color": "other",
            "confidence": 0.51
          },
          {
            "color": "blond",
            "confidence": 0.08
          },
          {
            "color": "red",
            "confidence": 0.08
          },
          {
            "color": "gray",
            "confidence": 0.02
          }
        ]
      }
    }
  }
]
```

## <a name="find-similar-faces"></a>似た顔の検索

この操作では、検出された顔 (ソース) を 1 つ受け取って、他の顔のセット (ターゲット) から一致するものを見つけます (画像による顔検索)。 一致するものが見つかると、一致した顔の ID がコンソールに出力されます。

### <a name="detect-faces-for-comparison"></a>比較の対象となる顔を検出する

最初に画像内の顔を検出してから、それらを比較する必要があります。 「[顔を検出する](#detect-faces-in-an-image)」セクションで行ったように、このコマンドを実行します。 この検出方法は、比較操作のために最適化されています。 前のセクションほど詳細には顔の属性を抽出せず、また、別の検出モデルを使用しています。

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="detect_for_similar":::

JSON 応答内の `"faceId"` 値を検索し、一時的な場所に保存します。 その後、上記のコマンドを他の画像 URL に対してもう一度呼び出し、それらの顔 ID も保存します。 これらの ID は、似た顔の検索先となる顔のターゲット グループとして使用します。

:::code source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="similar_group":::

最後に、照合に使用する 1 つのソースの顔を検出し、その ID を保存します。 この ID は他の ID とは別に保管してください。

:::code source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="similar_matcher":::

### <a name="find-matches"></a>一致するものを探す

テキスト エディターに次のコマンドをコピーします。

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="similar":::

その後、次の変更を行います。
1. `Ocp-Apim-Subscription-Key` を、有効な Face サブスクリプション キーに割り当てます。
1. このクエリ URL の最初の部分を、ご自分のサブスクリプション キーに対応するエンドポイントに合わせて変更します。

`body` 値には次の JSON コンテンツを使用します。

:::code language="JSON" source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="similar_body":::

1. `"faceId"` のソースの顔 ID を使用します。
1. その他の顔 ID を `"faceIds"` 配列に単語として貼り付けます。

### <a name="examine-the-results"></a>結果を確認する

顔のクエリに一致する顔の ID の一覧を示す JSON 応答を受け取ります。

```json
[
    {
        "persistedFaceId" : "015839fb-fbd9-4f79-ace9-7675fc2f1dd9",
        "confidence" : 0.82
    },
    ...
] 
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Cognitive Services サブスクリプションをクリーンアップして削除したい場合は、リソースまたはリソース グループを削除することができます。 リソース グループを削除すると、それに関連付けられている他のリソースも削除されます。

* [ポータル](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Face REST API を使用して基本的な顔認識タスクを行う方法について学習しました。 次は、リファレンス ドキュメントを参照して、ライブラリの詳細について学習してください。

> [!div class="nextstepaction"]
> [Face API リファレンス](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)

* [Face サービスとは](../../overview.md)