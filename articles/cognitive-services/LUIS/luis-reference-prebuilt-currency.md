---
title: currency 作成済みエンティティ - LUIS
titleSuffix: Azure Cognitive Services
description: この記事には、Language Understanding (LUIS) での currency 作成済みエンティティについての情報が含まれます。
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/14/2019
ms.openlocfilehash: c89de0ba74d04c510f3d5ba537f3a6dcc4819169
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "91534338"
---
# <a name="currency-prebuilt-entity-for-a-luis-app"></a>LUIS アプリの currency 作成済みエンティティ
作成済みの currency エンティティは、LUIS アプリのカルチャに関係なく、多くの貨幣単位や国/地域の通貨を検出します。 このエンティティは既にトレーニングされているので、通貨を含む発話の例をアプリケーション意図に追加する必要はありません。 currency エンティティは、[多くのカルチャ](luis-reference-prebuilt-entities.md)でサポートされています。

## <a name="types-of-currency"></a>currency の種類
currency は [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L26) GitHub リポジトリから管理されます

## <a name="resolution-for-currency-entity"></a>currency エンティティの解決

#### <a name="v3-response"></a>[V3 の応答](#tab/V3)

次の JSON は、`verbose` パラメーターが `false` に設定されている場合です。

```json
"entities": {
    "money": [
        {
            "number": 10.99,
            "units": "Dollar"
        }
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 の詳細な応答](#tab/V3-verbose)
次の JSON は、`verbose` パラメーターが `true` に設定されている場合です。

```json
"entities": {
    "money": [
        {
            "number": 10.99,
            "unit": "Dollar"
        }
    ],
    "$instance": {
        "money": [
            {
                "type": "builtin.currency",
                "text": "$10.99",
                "startIndex": 23,
                "length": 6,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor"
            }
        ]
    }
}
```

#### <a name="v2-response"></a>[V2 の応答](#tab/V2)

次の例では、**builtin.currency** エンティティの解決を示します。

```json
"entities": [
    {
        "entity": "$10.99",
        "type": "builtin.currency",
        "startIndex": 23,
        "endIndex": 28,
        "resolution": {
        "unit": "Dollar",
        "value": "10.99"
        }
    }
]
```
* * *

## <a name="next-steps"></a>次のステップ

[V3 予測エンドポイント](luis-migration-api-v3.md)の詳細について学習します。

[datetimeV2](luis-reference-prebuilt-datetimev2.md)、[dimension](luis-reference-prebuilt-dimension.md)、[email](luis-reference-prebuilt-email.md) エンティティについて学習します。
