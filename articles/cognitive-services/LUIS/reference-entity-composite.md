---
title: 複合エンティティ型 - LUIS
titleSuffix: Azure Cognitive Services
description: 複合エンティティは、事前構築済みエンティティ、シンプル、正規表現、リスト エンティティなどの他のエンティティで構成されます。 個別のエンティティが、エンティティ全体を形成します。
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 04/14/2020
ms.openlocfilehash: 3d936555a4d9a9c80718a24ba892c762f5a8354a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "97608394"
---
# <a name="composite-entity"></a>複合エンティティ

複合エンティティは、事前構築済みエンティティ、シンプル、正規表現、リスト エンティティなどの他のエンティティで構成されます。 個別のエンティティが、エンティティ全体を形成します。

> [!CAUTION]
> このエンティティは **非推奨** です。 [機械学習エンティティ](reference-entity-machine-learned-entity.md)に移行してください。

**このエンティティは、次のようなデータに最適です。**

* 相互に関連している。
* 発話のコンテキストで相互に関連する。
* さまざまなエンティティ型を使用する。
* クライアント アプリケーションによって情報の単位としてグループ化され、処理される必要がある。
* 機械学習を必要とするさまざまなユーザー発話がある。

![複合エンティティ](./media/luis-concept-entities/composite-entity.png)

## <a name="example-json"></a>JSON の例

次の発話で事前構築済みの `number` と `Location::ToLocation` の複合エンティティを考えてみましょう。

`book 2 tickets to cairo`

number の `2` と ToLocation `cairo` の間には、どのエンティティにも属さない単語があります。 [LUIS](luis-reference-regions.md) Web サイトの、ラベルの付いた発話で使用される緑色の下線が複合エンティティを示します。

![複合エンティティ](./media/luis-concept-data-extraction/composite-entity.png)

#### <a name="v2-prediction-endpoint-response"></a>[V2 予測エンドポイントの応答](#tab/V2)

複合エンティティは、`compositeEntities` 配列で返され、その複合内のすべてのエンティティも、`entities` 配列で返されます。

```JSON
  "entities": [
    {
      "entity": "2 tickets to cairo",
      "type": "ticketinfo",
      "startIndex": 5,
      "endIndex": 22,
      "score": 0.9214487
    },
    {
      "entity": "cairo",
      "type": "builtin.geographyV2.city",
      "startIndex": 18,
      "endIndex": 22
    },
    {
      "entity": "2",
      "type": "builtin.number",
      "startIndex": 5,
      "endIndex": 5,
      "resolution": {
        "subtype": "integer",
        "value": "2"
      }
    }
  ],
  "compositeEntities": [
    {
      "parentType": "ticketinfo",
      "value": "2 tickets to cairo",
      "children": [
        {
          "type": "builtin.number",
          "value": "2"
        },
        {
          "type": "builtin.geographyV2.city",
          "value": "cairo"
        }
      ]
    }
  ]
```

#### <a name="v3-prediction-endpoint-response"></a>[V3 予測エンドポイントの応答](#tab/V3)

これは、クエリ文字列で `verbose=false` が設定されている場合の JSON です。

```json
"entities": {
    "ticketinfo": [
        {
            "number": [
                2
            ],
            "geographyV2": [
                "cairo"
            ]
        }
    ]
}
```

これは、クエリ文字列で `verbose=true` が設定されている場合の JSON です。

```json
"entities": {
    "ticketinfo": [
        {
            "number": [
                2
            ],
            "geographyV2": [
                "cairo"
            ],
            "$instance": {
                "number": [
                    {
                        "type": "builtin.number",
                        "text": "2",
                        "startIndex": 5,
                        "length": 1,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ],
                "geographyV2": [
                    {
                        "type": "builtin.geographyV2.city",
                        "text": "cairo",
                        "startIndex": 18,
                        "length": 5,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ]
            }
        }
    ],
    "$instance": {
        "ticketinfo": [
            {
                "type": "ticketinfo",
                "text": "2 tickets to cairo",
                "startIndex": 5,
                "length": 18,
                "score": 0.9214487,
                "modelTypeId": 4,
                "modelType": "Composite Entity Extractor",
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
|事前構築済みのエンティティ - number|"builtin.number"|"2"|
|事前構築済みエンティティ - GeographyV2|"Location::ToLocation"|"cairo"|

## <a name="next-steps"></a>次のステップ

エンティティの詳細を確認します。

* [概念](luis-concept-entity-types.md)
* [作成方法](luis-how-to-add-entities.md)
