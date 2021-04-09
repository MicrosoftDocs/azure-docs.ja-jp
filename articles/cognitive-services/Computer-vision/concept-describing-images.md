---
title: 画像の説明 - Computer Vision
titleSuffix: Azure Cognitive Services
description: Computer Vision API の画像説明機能に関連する概念。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 4fd328dad3544697ba1f4896f7383be857b097a5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "96530665"
---
# <a name="describe-images-with-human-readable-language"></a>人間が判読できる言語で画像を説明する

Computer Vision では、イメージを分析し、人間が判読できる言語で、その内容を説明する文を生成できます。 実際には、アルゴリズムにより、さまざまな視覚化機能に基づいて複数の説明が返されます。各説明には、信頼度スコアが付与されます。 最終的には、各説明を信頼度の高い順に並べたリストが出力されます。

## <a name="image-description-example"></a>画像の説明の例

次の JSON 応答は、視覚的特徴に基づいてサンプル画像を説明するときに、Computer Vision から返される内容を示します。

![マンハッタンのビル群の白黒写真](./Images/bw_buildings.png)

```json
{
    "description": {
        "tags": ["outdoor", "building", "photo", "city", "white", "black", "large", "sitting", "old", "water", "skyscraper", "many", "boat", "river", "group", "street", "people", "field", "tall", "bird", "standing"],
        "captions": [
            {
                "text": "a black and white photo of a city",
                "confidence": 0.95301952483304808
            },
            {
                "text": "a black and white photo of a large city",
                "confidence": 0.94085190563213816
            },
            {
                "text": "a large white building in a city",
                "confidence": 0.93108362931954824
            }
        ]
    },
    "requestId": "b20bfc83-fb25-4b8d-a3f8-b2a1f084b159",
    "metadata": {
        "height": 300,
        "width": 239,
        "format": "Jpeg"
    }
}
```

## <a name="use-the-api"></a>API の使用

画像説明機能は [Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) API に含まれています。 ネイティブ SDK または REST を呼び出すことでこの API を呼び出すことができます。 `Description` を **visualFeatures** クエリ パラメーターに追加します。 その後、完全な JSON 応答が得られたら、`"description"` セクションのコンテンツを対象に文字列を解析します。

* [クイック スタート: Computer Vision REST API またはクライアント ライブラリ](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)

## <a name="next-steps"></a>次のステップ

[画像へのタグ付け](concept-tagging-images.md)および[画像の分類](concept-categorizing-images.md)に関連する概念を学習します。
