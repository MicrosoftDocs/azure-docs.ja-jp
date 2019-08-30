---
title: 顔検出 - Computer Vision
titleSuffix: Azure Cognitive Services
description: Computer Vision API の顔検出機能に関連する概念について説明します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 3675acf59c6889dd00c8f26089f509bbcd3c724e
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2019
ms.locfileid: "68945246"
---
# <a name="face-detection-with-computer-vision"></a>Computer Vision での顔の検出

Computer Vision は、画像内の人間の顔を検出し、検出された各顔の年齢、性別、四角形を生成できます。 

> [!NOTE]
> この機能は、Azure [Face](/azure/cognitive-services/face/) サービスでも利用できます。 ここでは、顔識別、姿勢検出など、より詳細な顔分析について説明しています。 

## <a name="face-detection-examples"></a>顔検出の例

次の例では、人の顔を 1 つ含む画像に対して Computer Vision によって返される JSON 応答を示します。

![Vision Analyze 屋上にいる女性の顔](./Images/woman_roof_face.png)

```json
{
    "faces": [
        {
            "age": 23,
            "gender": "Female",
            "faceRectangle": {
                "top": 45,
                "left": 194,
                "width": 44,
                "height": 44
            }
        }
    ],
    "requestId": "8439ba87-de65-441b-a0f1-c85913157ecd",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Png"
    }
}
```

2 番目の例では、人の顔複数含む画像に対して返される JSON 応答を示します。

![Vision Analyze 家族写真の顔](./Images/family_photo_face.png)

```json
{
    "faces": [
        {
            "age": 11,
            "gender": "Male",
            "faceRectangle": {
                "top": 62,
                "left": 22,
                "width": 45,
                "height": 45
            }
        },
        {
            "age": 11,
            "gender": "Female",
            "faceRectangle": {
                "top": 127,
                "left": 240,
                "width": 42,
                "height": 42
            }
        },
        {
            "age": 37,
            "gender": "Female",
            "faceRectangle": {
                "top": 55,
                "left": 200,
                "width": 41,
                "height": 41
            }
        },
        {
            "age": 41,
            "gender": "Male",
            "faceRectangle": {
                "top": 45,
                "left": 103,
                "width": 39,
                "height": 39
            }
        }
    ],
    "requestId": "3a383cbe-1a05-4104-9ce7-1b5cf352b239",
    "metadata": {
        "height": 230,
        "width": 300,
        "format": "Png"
    }
}
```

## <a name="next-steps"></a>次の手順

顔検出機能を使用する方法については、[画像分析](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa)のリファレンス ドキュメントを参照してください。
