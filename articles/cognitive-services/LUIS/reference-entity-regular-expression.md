---
title: 正規表現エンティティ型 - LUIS
description: 生の発話テキストには正規表現をお勧めします。 大文字小文字とカルチャのバリアントは無視されます。  正規表現の照合は、スペルチェックによる変更後に、トークン レベルではなく文字レベルで適用されます。
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 04/14/2020
ms.openlocfilehash: 18e44ec43e1169aa054e6e5b4591ccd8611a7f4d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "95025227"
---
# <a name="regular-expression-entity"></a>正規表現エンティティ

正規表現エンティティは、指定された正規表現パターンに基づいてエンティティを抽出します。

生の発話テキストには正規表現をお勧めします。 大文字小文字とカルチャのバリアントは無視されます。  正規表現の照合は、スペルチェックによる変更後に、トークン レベルではなく文字レベルで適用されます。 角かっこが多用されているなど、正規表現が複雑すぎる場合は、このモデルに式を追加できません。 [.NET Regex](/dotnet/standard/base-types/regular-expressions) ライブラリの全体ではなく一部が使用されます。

**エンティティは、次のような場合に最適です。**

* データが一貫して書式設定され、バリエーションにも一貫性がある。
* 正規表現では、2 レベルを超える入れ子は必要ありません。

![正規表現エンティティ](./media/luis-concept-entities/regex-entity.png)

## <a name="usage-considerations"></a>使用に関する考慮事項

正規表現は予想を超えて一致する場合があります。 `one` や `two` のような、数字を表す単語の一致がその例です。 次に示すのは、他の数字と共に数字 `one` に一致する正規表現の例です。

```javascript
(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*
```

この正規表現は、`phone` のように、これらの数字で終わる単語にも一致します。 このような問題を修正するには、正規表現の一致が必ず、単語の境界を考慮に入れるようにします。 この例で単語の境界を使用するための正規表現は、次の正規表現で使用されます。

```javascript
\b(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*\b
```

### <a name="example-json"></a>JSON の例

正規表現エンティティの定義として `kb[0-9]{6}` を使用したときの、次のクエリに対して返される正規表現エンティティを含む発話の例は、以下の JSON 応答のようになります。

`When was kb123456 published?`:

#### <a name="v2-prediction-endpoint-response"></a>[V2 予測エンドポイントの応答](#tab/V2)

```JSON
"entities": [
  {
    "entity": "kb123456",
    "type": "KB number",
    "startIndex": 9,
    "endIndex": 16
  }
]
```


#### <a name="v3-prediction-endpoint-response"></a>[V3 予測エンドポイントの応答](#tab/V3)


これは、クエリ文字列で `verbose=false` が設定されている場合の JSON です。

```json
"entities": {
    "KB number": [
        "kb123456"
    ]
}
```

これは、クエリ文字列で `verbose=true` が設定されている場合の JSON です。

```json
"entities": {
    "KB number": [
        "kb123456"
    ],
    "$instance": {
        "KB number": [
            {
                "type": "KB number",
                "text": "kb123456",
                "startIndex": 9,
                "length": 8,
                "modelTypeId": 8,
                "modelType": "Regex Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

* * *

## <a name="next-steps"></a>次のステップ

エンティティの詳細を確認します。

* [概念](luis-concept-entity-types.md)
* [作成方法](luis-how-to-add-entities.md)