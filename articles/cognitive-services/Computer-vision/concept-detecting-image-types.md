---
title: 画像の種類の検出 - Computer Vision
titleSuffix: Azure Cognitive Services
description: Computer Vision API の画像の種類の検出機能に関連する概念。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 6d2ed00f3fc6f5b52a9a13a96f1e1659e30f02d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "96532603"
---
# <a name="detecting-image-types-with-computer-vision"></a>Computer Vision での画像の種類の検出

Computer Vision は、[画像分析](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) API を使用して、画像のコンテンツの種類を分析し、画像がクリップ アートか線画かを示すことができます。

## <a name="detecting-clip-art"></a>クリップ アートの検出

Computer Vision は、次の表に示すように、画像を分析して、画像がクリップ アートである可能性を 0 から 3 のスケールで評価します。

| 値 | 意味 |
|-------|---------|
| 0 | クリップ アートではない |
| 1 | あいまい |
| 2 | 通常のクリップ アート |
| 3 | 良好なクリップ アート |

### <a name="clip-art-detection-examples"></a>クリップ アート検出の例

次の JSON 応答では、サンプル画像がクリップ アートである可能性を評価するときに Computer Vision が返すものを示します。

![一切れのチーズのクリップ アート画像](./Images/cheese_clipart.png)

```json
{
    "imageType": {
        "clipArtType": 3,
        "lineDrawingType": 0
    },
    "requestId": "88c48d8c-80f3-449f-878f-6947f3b35a27",
    "metadata": {
        "height": 225,
        "width": 300,
        "format": "Jpeg"
    }
}
```

![青い家と前庭](./Images/house_yard.png)

```json
{
    "imageType": {
        "clipArtType": 0,
        "lineDrawingType": 0
    },
    "requestId": "a9c8490a-2740-4e04-923b-e8f4830d0e47",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

## <a name="detecting-line-drawings"></a>線画の検出

Computer Vision は画像を分析し、画像が線画かどうかを示すブール値を返します。

### <a name="line-drawing-detection-examples"></a>線画の検出例

次の JSON 応答では、サンプル画像が線画かどうかを示すときに Computer Vision が返すものを示します。

![ライオンの線画画像](./Images/lion_drawing.png)

```json
{
    "imageType": {
        "clipArtType": 2,
        "lineDrawingType": 1
    },
    "requestId": "6442dc22-476a-41c4-aa3d-9ceb15172f01",
    "metadata": {
        "height": 268,
        "width": 300,
        "format": "Jpeg"
    }
}
```

![白い花と緑の背景](./Images/flower.png)

```json
{
    "imageType": {
        "clipArtType": 0,
        "lineDrawingType": 0
    },
    "requestId": "98437d65-1b05-4ab7-b439-7098b5dfdcbf",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

## <a name="use-the-api"></a>API の使用

画像種類検出機能は [Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) API に含まれています。 ネイティブ SDK または REST を呼び出すことでこの API を呼び出すことができます。 `ImageType` を **visualFeatures** クエリ パラメーターに追加します。 その後、完全な JSON 応答が得られたら、`"imageType"` セクションのコンテンツを対象に文字列を解析します。

* [クイック スタート: Computer Vision REST API またはクライアント ライブラリ](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
