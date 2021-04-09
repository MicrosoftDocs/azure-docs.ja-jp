---
title: ordinal 作成済みエンティティ - LUIS
titleSuffix: Azure Cognitive Services
description: この記事には、Language Understanding (LUIS) での ordinal 作成済みエンティティについての情報が含まれます。
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/14/2019
ms.openlocfilehash: 1fa86c8960ea9f32163ebd7991260a19ef7a5d79
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "91535459"
---
# <a name="ordinal-prebuilt-entity-for-a-luis-app"></a>LUIS アプリの ordinal 作成済みエンティティ
序数は、セット内のオブジェクトの数値表現 `first`、`second`、`third` です。 このエンティティは既にトレーニングされているので、序数メールを含む発話の例をアプリケーション意図に追加する必要はありません。 ordinal エンティティは、[多くのカルチャ](luis-reference-prebuilt-entities.md)でサポートされます。

## <a name="types-of-ordinal"></a>ordinal の種類
ordinal は [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L45) GitHub リポジトリから管理されます。

## <a name="resolution-for-prebuilt-ordinal-entity"></a>作成済み ordinal エンティティの解決

次のエンティティ オブジェクトがクエリに対して返されます。

`Order the second option`

#### <a name="v3-response"></a>[V3 の応答](#tab/V3)

次の JSON は、`verbose` パラメーターが `false` に設定されている場合です。

```json
"entities": {
    "ordinal": [
        2
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 の詳細な応答](#tab/V3-verbose)
次の JSON は、`verbose` パラメーターが `true` に設定されている場合です。

```json
"entities": {
    "ordinal": [
        2
    ],
    "$instance": {
        "ordinal": [
            {
                "type": "builtin.ordinal",
                "text": "second",
                "startIndex": 10,
                "length": 6,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

#### <a name="v2-response"></a>[V2 の応答](#tab/V2)

次の例では、**builtin.ordinal** エンティティの解決を示します。

```json
"entities": [
  {
    "entity": "second",
    "type": "builtin.ordinal",
    "startIndex": 10,
    "endIndex": 15,
    "resolution": {
      "value": "2"
    }
  }
]
```
* * *

## <a name="next-steps"></a>次のステップ

[V3 予測エンドポイント](luis-migration-api-v3.md)の詳細について学習します。

[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)、[phone number](luis-reference-prebuilt-phonenumber.md)、[temperature](luis-reference-prebuilt-temperature.md) エンティティについて学習します。
