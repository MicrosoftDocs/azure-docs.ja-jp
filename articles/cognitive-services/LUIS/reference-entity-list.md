---
title: リスト エンティティ型 - LUIS
description: リスト エンティティは、固定かつ限定された関連単語セットとそのシノニムを表します。 LUIS では、リスト エンティティの追加の値は検出されません。 現在のリストに基づいて新しい単語の候補を表示するには、[Recommend] (推奨) 機能を使用します。
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 04/14/2020
ms.openlocfilehash: 410b33b5c6078d096fa4b2acaa7b49bc14c95e31
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "97608274"
---
# <a name="list-entity"></a>リスト エンティティ

リスト エンティティは、固定かつ限定された関連単語セットとそのシノニムを表します。 LUIS では、リスト エンティティの追加の値は検出されません。 現在のリストに基づいて新しい単語の候補を表示するには、 **[Recommend] (推奨)** 機能を使用します。 同じ値を持つリスト エンティティが複数存在する場合は、エンドポイント クエリに各エンティティが返されます。

リスト エンティティでは、機械学習は行われません。 完全なテキスト一致です。 LUIS では、応答内のエンティティとして、任意のリスト内の項目に対してあらゆる一致がマークされます。

**エンティティは、次のようなテキスト データに最適です。**

* 既知のセットである。
* あまり変化しない。 リストを頻繁に変更する必要がある、またはリストを自己拡張させたい場合、フレーズ リストで強化したシンプル エンティティの方が良い選択肢です。
* セットがこのエンティティ型の最大 LUIS [境界](luis-limits.md)を超えていない。
* 発話内のテキストがシノニムまたは正規名に大文字と小文字が区別されず一致している。 LUIS では、この一致以外にリストは使用されません。 あいまい一致、語幹抽出、複数形、その他のバリエーションは、リスト エンティティでは解決されません。 バリエーションを管理するには、オプションのテキスト構文で[パターン](reference-pattern-syntax.md#syntax-to-mark-optional-text-in-a-template-utterance)を使用することを検討します。

![リスト エンティティ](./media/luis-concept-entities/list-entity.png)

## <a name="example-json-to-import-into-list-entity"></a>リスト エンティティにインポートする .json の例

  次の JSON 形式を使用して、既存のリスト エンティティに値をインポートできます。

  ```JSON
  [
      {
          "canonicalForm": "Blue",
          "list": [
              "navy",
              "royal",
              "baby"
          ]
      },
      {
          "canonicalForm": "Green",
          "list": [
              "kelly",
              "forest",
              "avacado"
          ]
      }
  ]
  ```

## <a name="example-json-response"></a>JSON の応答例

アプリに `Cities` いう名前のリストがあり、空港がある都市 (Sea-tac)、空港コード (SEA)、郵便番号 (98101)、および電話の市外局番 (206) など、都市名のバリエーションに対応しています。

|リスト項目|項目のシノニム|
|---|---|
|`Seattle`|`sea-tac`, `sea`, `98101`, `206`, `+1` |
|`Paris`|`cdg`, `roissy`, `ory`, `75001`, `1`, `+33`|

`book 2 tickets to paris`

前の発話では、単語 `paris` は、`Cities` リスト エンティティの一部として、パリ項目にマップされます。 このリスト エンティティは、項目のシノニムだけでなく、項目の正規化された名前と一致します。

#### <a name="v2-prediction-endpoint-response"></a>[V2 予測エンドポイントの応答](#tab/V2)

```JSON
  "entities": [
    {
      "entity": "paris",
      "type": "Cities",
      "startIndex": 18,
      "endIndex": 22,
      "resolution": {
        "values": [
          "Paris"
        ]
      }
    }
  ]
```

#### <a name="v3-prediction-endpoint-response"></a>[V3 予測エンドポイントの応答](#tab/V3)

これは、クエリ文字列で `verbose=false` が設定されている場合の JSON です。

```json
"entities": {
    "Cities": [
        [
            "Paris"
        ]
    ]
}
```

これは、クエリ文字列で `verbose=true` が設定されている場合の JSON です。

```json
"entities": {
    "Cities": [
        [
            "Paris"
        ]
    ],
    "$instance": {
        "Cities": [
            {
                "type": "Cities",
                "text": "paris",
                "startIndex": 18,
                "length": 5,
                "modelTypeId": 5,
                "modelType": "List Entity Extractor",
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
|リスト エンティティ|`Cities`|`paris`|

## <a name="next-steps"></a>次のステップ

エンティティの詳細を確認します。

* [概念](luis-concept-entity-types.md)
* [作成方法](luis-how-to-add-entities.md)
