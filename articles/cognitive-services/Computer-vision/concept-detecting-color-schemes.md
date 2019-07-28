---
title: 配色の検出 - Computer Vision
titleSuffix: Azure Cognitive Services
description: Computer Vision API を使用したイメージの配色の検出に関連する概念。
services: cognitive-services
author: patrickfarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: ff7af2204f9e05a1ba4ef800c63c3ad462242350
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "60368589"
---
# <a name="detect-color-schemes-in-images"></a>イメージ内の配色を検出する

Computer Vision では、イメージ内のカラーが分析され、前景のドミナント カラー、背景のドミナント カラー、およびイメージ全体の一連のドミナント カラーの 3 種類の属性が利用できます。 返されるカラーは、黒、青、茶色、灰色、緑、オレンジ、ピンク、紫、赤、青緑、白、黄色のセットに属しています。 

Computer Vision では、ドミナント カラーと彩度の組み合わせに基づき、イメージ内で最も鮮やかなカラーを表すアクセント カラーも抽出されます。 アクセント カラーは、16 進数の HTML カラー コードとして返されます。 

Computer Vision では、イメージが白黒であるかどうかを示すブール値も返されます。

## <a name="color-scheme-detection-examples"></a>配色の検出の例

次の例は、イメージの例の配色を検出するときに Computer Vision によって返される JSON 応答を示しています。 ここでは、イメージの例は白黒ではありませんが、前景と背景のドミナント カラーは黒で、イメージ全体のドミナント カラーは白黒です。

![山の夕日と人のシルエット](./Images/mountain_vista.png)

```json
{
    "color": {
        "dominantColorForeground": "Black",
        "dominantColorBackground": "Black",
        "dominantColors": ["Black", "White"],
        "accentColor": "BB6D10",
        "isBwImg": false
    },
    "requestId": "0dc394bf-db50-4871-bdcc-13707d9405ea",
    "metadata": {
        "height": 202,
        "width": 300,
        "format": "Jpeg"
    }
}
```

### <a name="dominant-color-examples"></a>ドミナント カラーの例

次の表は、各イメージ例について返された前景のカラー、背景のカラー、およびイメージのカラーを示しています。

| Image | ドミナント カラー |
|-------|-----------------|
|![白い花と緑の背景](./Images/flower.png)| 前景:黒<br/>背景:白<br/>色:黒、白、緑|
![駅を通過中の電車](./Images/train_station.png) | 前景:黒<br/>背景:黒<br/>色:黒 |

### <a name="accent-color-examples"></a>アクセント カラーの例

 次の表は、各イメージ例について返されたアクセント カラーを 16 進数の HTML カラー値で示したものです。

| Image | アクセント カラー |
|-------|--------------|
|![日没時に山頂の岩の上に立っている人物](./Images/mountain_vista.png) | #BB6D10 |
|![白い花と緑の背景](./Images/flower.png) | #C6A205 |
|![駅を通過中の電車](./Images/train_station.png) | #474A84 |

### <a name="black--white-detection-examples"></a>白黒の検出の例

次の表は、イメージ例における Computer Vision の白黒の評価を示しています。

| Image | 白黒かどうか |
|-------|----------------|
|![マンハッタンのビル群の白黒写真](./Images/bw_buildings.png) | true |
|![青い家と前庭](./Images/house_yard.png) | false |

## <a name="next-steps"></a>次の手順

[イメージの種類の検出](concept-detecting-image-types.md)に関する概念を理解します。
