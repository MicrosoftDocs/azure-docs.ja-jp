---
title: ドメイン固有のコンテンツ - Computer Vision
titleSuffix: Azure Cognitive Services
description: 画像に関する詳細な情報を返すための画像分類ドメインを指定する方法について説明します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 5cd872d66088e165bfc8356ab6d96a0a6135a0e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "94538310"
---
# <a name="detect-domain-specific-content"></a>ドメイン固有のコンテンツの検出

タグ付けと大まかなカテゴリ化に加えて、Computer Vision では、特殊なデータでトレーニング済みのモデルを使用した詳細なドメイン固有の解析もサポートされます。

ドメイン固有のモデルには、それ自体で使用する方法 (スコープ解析) と、カテゴリ化機能の拡張として使用する方法があります。

### <a name="scoped-analysis"></a>スコープされた分析

選択したドメイン固有のモデルのみを使用してイメージを分析するには、[Models/\<model\>/Analyze](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) API を使用します。

特定のイメージについて、**models/celebrities/analyze** API で返される JSON 応答の例を次に示します。

![Satya Nadella が笑顔で立っています](./images/satya.jpeg)

```json
{
  "result": {
    "celebrities": [{
      "faceRectangle": {
        "top": 391,
        "left": 318,
        "width": 184,
        "height": 184
      },
      "name": "Satya Nadella",
      "confidence": 0.99999856948852539
    }]
  },
  "requestId": "8217262a-1a90-4498-a242-68376a4b956b",
  "metadata": {
    "width": 800,
    "height": 1200,
    "format": "Jpeg"
  }
}
```

### <a name="enhanced-categorization-analysis"></a>拡張カテゴリ化解析

ドメイン固有のモデルを使用して、一般的なイメージ解析を補足することもできます。 これは、[Analyze](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) API 呼び出しの *details* パラメーターにドメイン固有のモデルを指定して、[大まかなカテゴリ化](concept-categorizing-images.md)の一部として行います。

この場合、最初に 86 のカテゴリ分類の分類子が呼び出されます。 検出されたカテゴリのいずれかに、一致するドメイン固有のモデルがあった場合、イメージはそのモデルもパススルーし、結果が追加されます。

次の JSON 応答は、ドメイン固有の解析を `detail` ノードとしてより広範なカテゴリ化解析に含める方法を示しています。

```json
"categories":[
  {
    "name":"abstract_",
    "score":0.00390625
  },
  {
    "name":"people_",
    "score":0.83984375,
    "detail":{
      "celebrities":[
        {
          "name":"Satya Nadella",
          "faceRectangle":{
            "left":597,
            "top":162,
            "width":248,
            "height":248
          },
          "confidence":0.999028444
        }
      ],
      "landmarks":[
        {
          "name":"Forbidden City",
          "confidence":0.9978346
        }
      ]
    }
  }
]
```

## <a name="list-the-domain-specific-models"></a>ドメイン固有のモデルの一覧

現在、Computer Vision では、次のドメイン固有モデルがサポートされています。

| Name | 説明 |
|------|-------------|
| celebrities | 著名人の認識、`people_` カテゴリに分類された画像についてサポート |
| landmarks | ランドマークの認識、`outdoor_` または `building_` カテゴリに分類された画像についてサポート |

[Models](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f20e) API を呼び出すと、各モデルを適用できるカテゴリと共に、この情報が返されます。

```json
{
  "models":[
    {
      "name":"celebrities",
      "categories":[
        "people_",
        "人_",
        "pessoas_",
        "gente_"
      ]
    },
    {
      "name":"landmarks",
      "categories":[
        "outdoor_",
        "户外_",
        "屋外_",
        "aoarlivre_",
        "alairelibre_",
        "building_",
        "建筑_",
        "建物_",
        "edifício_"
      ]
    }
  ]
}
```

## <a name="next-steps"></a>次のステップ

[画像の分類](concept-categorizing-images.md)に関する概念を学習する。
