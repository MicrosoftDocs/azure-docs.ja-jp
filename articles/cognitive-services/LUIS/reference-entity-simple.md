---
title: シンプル エンティティ型 - LUIS
titleSuffix: Azure Cognitive Services
description: シンプル エンティティでは、機械学習のコンテキストからの単一の概念が記述されています。 シンプル エンティティを使用して結果を改善するときは、フレーズ リストを追加します。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: 1b5754be3c9941101a53f332841ace93caf9acdd
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/20/2020
ms.locfileid: "83684558"
---
# <a name="simple-entity"></a>シンプル エンティティ

シンプル エンティティとは、1 つの概念について説明し、機械学習コンテキストから学習した汎用的エンティティです。 シンプル エンティティは、一般に会社名、製品名、その他のカテゴリの名前などの名前なので、使用する名前のシグナルの強化にシンプル エンティティを使用する場合は、[フレーズ リスト](luis-concept-feature.md)を追加します。

**エンティティは、次のような場合に最適です。**

* データが一貫して書式設定されていないが、同じことを示している。

![シンプル エンティティ](./media/luis-concept-entities/simple-entity.png)

## <a name="example-json"></a>JSON の例

`Bob Jones wants 3 meatball pho`

前の発話では、`Bob Jones` は、シンプルな `Customer` エンティティとしてラベルが付けられています。

エンドポイントから返されるデータには、エンティティ名、発話から検出されたテキスト、検出されたテキストの場所、およびスコアが含まれます。

#### <a name="v2-prediction-endpoint-response"></a>[V2 予測エンドポイントの応答](#tab/V2)

```JSON
"entities": [
  {
  "entity": "bob jones",
  "type": "Customer",
  "startIndex": 0,
  "endIndex": 8,
  "score": 0.473899543
  }
]
```

#### <a name="v3-prediction-endpoint-response"></a>[V3 予測エンドポイントの応答](#tab/V3)

これは、クエリ文字列で `verbose=false` が設定されている場合の JSON です。

```json
"entities": {
    "Customer": [
        "Bob Jones"
    ]
}```

This is the JSON if `verbose=true` is set in the query string:

```json
"entities": {
    "Customer": [
        "Bob Jones"
    ],
    "$instance": {
        "Customer": [
            {
                "type": "Customer",
                "text": "Bob Jones",
                "startIndex": 0,
                "length": 9,
                "score": 0.9339134,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

* * *

|データ オブジェクト|エンティティ名|値|
|--|--|--|
|シンプル エンティティ|`Customer`|`bob jones`|

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [パターンの構文について学習する](reference-pattern-syntax.md)