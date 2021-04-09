---
title: age 作成済みエンティティ - LUIS
titleSuffix: Azure Cognitive Services
description: この記事には、Language Understanding (LUIS) での age 作成済みエンティティについての情報が含まれます。
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/04/2019
ms.openlocfilehash: 4e1a6b1b6fb99a2786de06e89960e8480a5e1338
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "91532926"
---
# <a name="age-prebuilt-entity-for-a-luis-app"></a>LUIS アプリの age 作成済みエンティティ
作成済みの age エンティティは、数値と、年数、月数、週数、日数の両方で、年齢の値をキャプチャします。 このエンティティは既にトレーニングされているので、年齢を含む発話の例をアプリケーション意図に追加する必要はありません。 age エンティティは、[多くのカルチャ](luis-reference-prebuilt-entities.md)でサポートされます。

## <a name="types-of-age"></a>age のタイプ
age は [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L3) GitHub リポジトリから管理されます。

## <a name="resolution-for-prebuilt-age-entity"></a>作成済み age エンティティの解決



#### <a name="v3-response"></a>[V3 の応答](#tab/V3)

次の JSON は、`verbose` パラメーターが `false` に設定されている場合です。

```json
"entities": {
    "age": [
        {
            "number": 90,
            "unit": "Day"
        }
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 の詳細な応答](#tab/V3-verbose)
次の JSON は、`verbose` パラメーターが `true` に設定されている場合です。

```json
"entities": {
    "age": [
        {
            "number": 90,
            "unit": "Day"
        }
    ],
    "$instance": {
        "age": [
            {
                "type": "builtin.age",
                "text": "90 day old",
                "startIndex": 2,
                "length": 10,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor"
            }
        ]
    }
}
```
#### <a name="v2-response"></a>[V2 の応答](#tab/V2)

次の例では、**builtin.age** エンティティの解決を示します。

```json
  "entities": [
    {
      "entity": "90 day old",
      "type": "builtin.age",
      "startIndex": 2,
      "endIndex": 11,
      "resolution": {
        "unit": "Day",
        "value": "90"
      }
    }
```
* * *

## <a name="next-steps"></a>次のステップ

[V3 予測エンドポイント](luis-migration-api-v3.md)の詳細について学習します。

[currency](luis-reference-prebuilt-currency.md)、[datetimeV2](luis-reference-prebuilt-datetimev2.md)、[dimension](luis-reference-prebuilt-dimension.md) エンティティについて学習します。
