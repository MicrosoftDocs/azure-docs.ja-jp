---
title: 事前作成済み ordinal V2 エンティティ - LUIS
titleSuffix: Azure Cognitive Services
description: この記事には、Language Understanding (LUIS) の事前作成済み ordinal V2 エンティティの情報が含まれています。
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.openlocfilehash: 3fd80389dfa54cb8386c13e0e05a1e71ac4bd956
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "91541936"
---
# <a name="ordinal-v2-prebuilt-entity-for-a-luis-app"></a>LUIS アプリの事前作成済み ordinal V2 エンティティ
ordinal V2 (序数) では、[ordinal](luis-reference-prebuilt-ordinal.md) を拡張して、`next`、`last`、`previous` などの相対参照を提供します。 事前作成済み ordinal エンティティを使用しても、これらは抽出されません。

## <a name="resolution-for-prebuilt-ordinal-v2-entity"></a>事前作成済み ordinal V2 エンティティの解決

次のエンティティ オブジェクトがクエリに対して返されます。

`what is the second to last choice in the list`

#### <a name="v3-response"></a>[V3 の応答](#tab/V3)

次の JSON は、`verbose` パラメーターが `false` に設定されている場合です。

```json
"entities": {
    "ordinalV2": [
        {
            "offset": -1,
            "relativeTo": "end"
        }
    ]
}
```

#### <a name="v3-verbose-response"></a>[V3 の詳細な応答](#tab/V3-verbose)

次の JSON は、`verbose` パラメーターが `true` に設定されている場合です。

```json
"entities": {
    "ordinalV2": [
        {
            "offset": -1,
            "relativeTo": "end"
        }
    ],
    "$instance": {
        "ordinalV2": [
            {
                "type": "builtin.ordinalV2.relative",
                "text": "the second to last",
                "startIndex": 8,
                "length": 18,
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

次の例は、**builtin.ordinalV2** エンティティの解決を示しています。

```json
"entities": [
    {
        "entity": "the second to last",
        "type": "builtin.ordinalV2.relative",
        "startIndex": 8,
        "endIndex": 25,
        "resolution": {
            "offset": "-1",
            "relativeTo": "end"
        }
    }
]
```
* * *

## <a name="next-steps"></a>次のステップ

[V3 予測エンドポイント](luis-migration-api-v3.md)の詳細について学習します。

[percentage](luis-reference-prebuilt-percentage.md)、[phone number](luis-reference-prebuilt-phonenumber.md)、および [temperature](luis-reference-prebuilt-temperature.md) エンティティについて学習します。
