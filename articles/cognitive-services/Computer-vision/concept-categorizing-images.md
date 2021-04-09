---
title: 画像の分類 - Computer Vision
titleSuffix: Azure Cognitive Services
description: Computer Vision API の画像分類機能に関連する概念について説明します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 9721ffa807c9adbeb50839113bc64fd23d8eb13f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "96533725"
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

| Image | カテゴリ |
|-------|----------|
| ![家族としてポーズをとっている 4 人の人物](./Images/family_photo.png) | people_group |
| ![草原の座っている子犬](./Images/cute_dog.png) | animal_dog |
| ![日没時に山頂の岩の上に立っている人物](./Images/mountain_vista.png) | outdoor_mountain |
| ![テーブル上の丸パンの山](./Images/bread.png) | food_bread |

## <a name="use-the-api"></a>API の使用

分類機能は [Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) API に含まれています。 ネイティブ SDK または REST を呼び出すことでこの API を呼び出すことができます。 `Categories` を **visualFeatures** クエリ パラメーターに追加します。 その後、完全な JSON 応答が得られたら、`"categories"` セクションのコンテンツを対象に文字列を解析します。

* [クイック スタート: Computer Vision REST API またはクライアント ライブラリ](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)

## <a name="next-steps"></a>次のステップ

[画像のタグ付け](concept-tagging-images.md)および[画像の説明](concept-describing-images.md)に関連する概念を学習します。
