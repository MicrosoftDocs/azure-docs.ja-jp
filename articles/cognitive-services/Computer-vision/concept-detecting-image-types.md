---
title: 画像の種類の検出 - Computer Vision
titleSuffix: Azure Cognitive Services
description: Computer Vision API の画像の種類の検出機能に関連する概念。
services: cognitive-services
author: patrickfarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 37cdac16a51a30bdaf1ba0266bab7fdd1f2990f0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "60368325"
---
# <a name="detecting-image-types-with-computer-vision"></a>Computer Vision での画像の種類の検出

Computer Vision は、[画像分析](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API を使用して、画像のコンテンツの種類を分析し、画像がクリップ アートか線画かを示すことができます。

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

## <a name="next-steps"></a>次の手順

画像の種類を検出する方法については、[画像分析](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa)のリファレンス ドキュメントを参照してください。
