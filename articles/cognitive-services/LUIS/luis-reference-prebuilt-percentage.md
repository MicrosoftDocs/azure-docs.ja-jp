---
title: percentage 作成済みエンティティ - LUIS
titleSuffix: Azure Cognitive Services
description: この記事には、Language Understanding (LUIS) での percentage 作成済みエンティティについての情報が含まれます。
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.openlocfilehash: bb64a32e2bdd3976fba3ce63433b13eb4891afc7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "91541698"
---
# <a name="percentage-prebuilt-entity-for-a-luis-app"></a>LUIS アプリの percentage 作成済みエンティティ
パーセンテージの値は、分数 `3 1/2` またはパーセンテージ `2%` として出現する可能性があります。 このエンティティは既にトレーニングされているので、パーセンテージを含む発話の例をアプリケーション意図に追加する必要はありません。 percentage エンティティは、[多くのカルチャ](luis-reference-prebuilt-entities.md)でサポートされています。

## <a name="types-of-percentage"></a>percentage のタイプ
percentage は [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L114) GitHub リポジトリから管理されます。

## <a name="resolution-for-prebuilt-percentage-entity"></a>作成済み percentage エンティティの解決

次のエンティティ オブジェクトがクエリに対して返されます。

`set a trigger when my stock goes up 2%`

#### <a name="v3-response"></a>[V3 の応答](#tab/V3)

次の JSON は、`verbose` パラメーターが `false` に設定されている場合です。

```json
"entities": {
    "percentage": [
        2
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 の詳細な応答](#tab/V3-verbose)
次の JSON は、`verbose` パラメーターが `true` に設定されている場合です。

```json
"entities": {
    "percentage": [
        2
    ],
    "$instance": {
        "percentage": [
            {
                "type": "builtin.percentage",
                "text": "2%",
                "startIndex": 36,
                "length": 2,
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

次の例では、**builtin.percentage** エンティティの解決を示します。

```json
"entities": [
    {
        "entity": "2%",
        "type": "builtin.percentage",
        "startIndex": 36,
        "endIndex": 37,
        "resolution": {
        "value": "2%"
        }
    }
]
```
* * *

## <a name="next-steps"></a>次のステップ

[V3 予測エンドポイント](luis-migration-api-v3.md)の詳細について学習します。

[ordinal](luis-reference-prebuilt-ordinal.md)、[number](luis-reference-prebuilt-number.md)、[temperature](luis-reference-prebuilt-temperature.md) エンティティについて学習します。
