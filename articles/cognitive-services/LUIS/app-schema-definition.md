---
title: アプリ スキーマ定義
description: LUIS アプリは `.json` または `.lu` で表され、すべての意図、エンティティ、発話の例、機能、および設定を含んでいます。
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 08/22/2020
ms.openlocfilehash: db4fd52dad82542f20e58ebb3b8526c5be7f2f88
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "91327325"
---
# <a name="app-schema-definition"></a>アプリ スキーマ定義

LUIS アプリは `.json` または `.lu` で表され、すべての意図、エンティティ、発話の例、機能、および設定を含んでいます。

## <a name="format"></a>Format

アプリをインポートまたはエクスポートするときは、`.json` または `.lu` を選択します。

|Format|Information|
|--|--|
|`.json`| 標準プログラミング形式|
|`.lu`|Bot Framework の [Bot Builder ツール](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown/docs/lu-file-format.md)でサポートされています。|

## <a name="version-7x"></a>バージョン 7.x

* バージョン 7.x への移行の場合、エンティティは入れ子になった機械学習エンティティとして表されます。
* 次のオーサリング API で `enableNestedChildren` プロパティを使用して、入れ子になった機械学習エンティティを作成するためのサポート:
    * [Add label](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c08)
    * [Add batch label](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c09)
    * [Review labels](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c0a)
    * [Suggest endpoint queries for entities](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2e)
    * [Suggest endpoint queries for intents](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2d)

```json
{
  "luis_schema_version": "7.0.0",
  "intents": [
    {
      "name": "None",
      "features": []
    }
  ],
  "entities": [],
  "hierarchicals": [],
  "composites": [],
  "closedLists": [],
  "prebuiltEntities": [],
  "utterances": [],
  "versionId": "0.1",
  "name": "example-app",
  "desc": "",
  "culture": "en-us",
  "tokenizerVersion": "1.0.0",
  "patternAnyEntities": [],
  "regex_entities": [],
  "phraselists": [
  ],
  "regex_features": [],
  "patterns": [],
  "settings": []
}
```

| 要素                  | コメント                              |
|--------------------------|--------------------------------------|
| "hierarchicals": [],     | 非推奨。[機械学習エンティティ](luis-concept-entity-types.md)を使用してください。   |
| "composites": [],        | 非推奨。[機械学習エンティティ](luis-concept-entity-types.md)を使用してください。 [複合エンティティ](reference-entity-composite.md)のリファレンス。 |
| "closedLists": [],       | [リスト エンティティ](reference-entity-list.md)のリファレンス。主にエンティティの特徴として使用されます。    |
| "versionId":"0.1",      | LUIS アプリのバージョン。|
| "name": "example-app",   | LUIS アプリの名前。 |
| "desc": "",              | LUIS アプリの説明 (省略可能)。  |
| "culture": "en-us",      | アプリの[言語](luis-language-support.md)。事前構築済みのエンティティ、機械学習、トークナイザーなど、基になる機能に影響を及ぼします。  |
| "tokenizerVersion":"1.0.0", | [Tokenizer](luis-language-support.md#tokenization)  |
| "patternAnyEntities": [],   | [Pattern.any エンティティ](reference-entity-pattern-any.md)    |
| "regex_entities": [],    |  [正規表現エンティティ](reference-entity-regular-expression.md)   |
| "phraselists": [],       |  [語句一覧 (特徴)](luis-concept-feature.md#create-a-phrase-list-for-a-concept)   |
| "regex_features": [],    |  非推奨。[機械学習エンティティ](luis-concept-entity-types.md)を使用してください。 |
| "patterns": [],          |  [パターン構文](reference-pattern-syntax.md)を使用して[予測精度を改善するパターン](luis-concept-patterns.md)   |
| "settings": []           | [アプリ設定](luis-reference-application-settings.md)|

## <a name="version-6x"></a>バージョン 6.x

* バージョン 6.x への移行の場合、新しい[機械学習エンティティ](reference-entity-machine-learned-entity.md)を使用して、エンティティを表します。

```json
{
  "luis_schema_version": "6.0.0",
  "intents": [
    {
      "name": "None",
      "features": []
    }
  ],
  "entities": [],
  "hierarchicals": [],
  "composites": [],
  "closedLists": [],
  "prebuiltEntities": [],
  "utterances": [],
  "versionId": "0.1",
  "name": "example-app",
  "desc": "",
  "culture": "en-us",
  "tokenizerVersion": "1.0.0",
  "patternAnyEntities": [],
  "regex_entities": [],
  "phraselists": [],
  "regex_features": [],
  "patterns": [],
  "settings": []
}
```

## <a name="version-4x"></a>バージョン 4.x

```json
{
  "luis_schema_version": "4.0.0",
  "versionId": "0.1",
  "name": "example-app",
  "desc": "",
  "culture": "en-us",
  "tokenizerVersion": "1.0.0",
  "intents": [
    {
      "name": "None"
    }
  ],
  "entities": [],
  "composites": [],
  "closedLists": [],
  "patternAnyEntities": [],
  "regex_entities": [],
  "prebuiltEntities": [],
  "model_features": [],
  "regex_features": [],
  "patterns": [],
  "utterances": [],
  "settings": []
}
```

## <a name="next-steps"></a>次のステップ

* [V3 オーサリング API](luis-migration-authoring-entities.md) に移行する