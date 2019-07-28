---
title: イメージへの分類 - Computer Vision
titleSuffix: Azure Cognitive Services
description: Computer Vision API の画像分類機能に関連する概念について説明します。
services: cognitive-services
author: patrickfarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 257da397e11843ee96e93f7b3e9bc5ada29822cf
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "60203324"
---
# <a name="categorize-images-by-subject-matter"></a>主題によるイメージの分類

タグや説明に加え、Computer Vision では、画像で検出された分類ベースのカテゴリが返されます。 タグとは異なり、カテゴリは親/子の遺伝階層で整理され、その数も少なくなります (数千ものタグと違って 86)。 すべてのカテゴリ名は英語です。 分類は単独で実行されるか、より新しいタグ モデルと共に実行できます。

## <a name="the-86-category-concept"></a>86 のカテゴリ概念

Computer Vision では次の図の 86 カテゴリの一覧を使用して、大まかに、または具体的に画像を分類できます。 テキスト形式の完全な分類については、[カテゴリの分類](category-taxonomy.md)に関するページを参照してください。

![カテゴリの分類に含まれるすべてのカテゴリのグループ化されたリスト](./Images/analyze_categories-v2.png)

## <a name="image-categorization-examples"></a>イメージの分類の例

次の JSON 応答は、視覚的特徴に基づいてサンプル イメージを分類する際に、Computer Vision から返される内容を示します。

![共同住宅の屋上にいる女性](./Images/woman_roof.png)

```json
{
    "categories": [
        {
            "name": "people_",
            "score": 0.81640625
        }
    ],
    "requestId": "bae7f76a-1cc7-4479-8d29-48a694974705",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

次の表は、各イメージについて Computer Vision から返される一般的なイメージ セットとカテゴリを示しています。

| Image | Category |
|-------|----------|
| ![家族としてポーズをとっている 4 人の人物](./Images/family_photo.png) | people_group |
| ![草原の座っている子犬](./Images/cute_dog.png) | animal_dog |
| ![日没時に山頂の岩の上に立っている人物](./Images/mountain_vista.png) | outdoor_mountain |
| ![テーブル上の丸パンの山](./Images/bread.png) | food_bread |

## <a name="next-steps"></a>次の手順

[イメージのタグ付け](concept-tagging-images.md)および[イメージの説明](concept-describing-images.md)に関する概念を確認します。
