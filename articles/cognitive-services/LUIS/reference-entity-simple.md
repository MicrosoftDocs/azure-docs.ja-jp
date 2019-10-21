---
title: シンプル エンティティ型 - LUIS
titleSuffix: Azure Cognitive Services
description: シンプル エンティティとは、1 つの概念について説明し、機械学習コンテキストから学習した汎用的エンティティです。 シンプル エンティティは、一般に会社名、製品名、その他のカテゴリの名前などの名前なので、使用する名前のシグナルの強化にシンプル エンティティを使用する場合は、フレーズ リストを追加します。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: 2eb3ff847f9bfc162adfb281d2ac1fad6f8c5093
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695128"
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

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 予測エンドポイントの応答](#tab/V2)

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

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 予測エンドポイントの応答](#tab/V3)

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

## <a name="next-steps"></a>次の手順

この[チュートリアル](luis-quickstart-primary-and-secondary-data.md)では、**Simple エンティティ**を使用して発話から雇用ジョブ名の機械学習データを抽出します。 抽出精度を向上させるために、Simple エンティティに固有の用語の[フレーズ リスト](luis-concept-feature.md)を追加します。