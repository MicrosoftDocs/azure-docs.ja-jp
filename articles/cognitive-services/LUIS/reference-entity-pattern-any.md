---
title: Pattern.any エンティティ型 - LUIS
titleSuffix: Azure Cognitive Services
description: Pattern.any は、エンティティの開始位置と終了位置を示すためにパターンのテンプレート発話でのみ使用される、可変長プレースホルダーです。
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.openlocfilehash: ec23be3709cebc534c059a21c52452abff683b18
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "91542208"
---
# <a name="patternany-entity"></a>Pattern.any エンティティ

Pattern.any は、エンティティの開始位置と終了位置を示すためにパターンのテンプレート発話でのみ使用される、可変長プレースホルダーです。  

Pattern.any エンティティは、意図のユーザー例内ではなく、[パターン](luis-how-to-model-intent-pattern.md) テンプレート例内でマークする必要があります。

**エンティティは、次のような場合に最適です。**

* エンティティの末尾を発話の残りのテキストと混同する可能性がある。

## <a name="usage"></a>使用法

タイトルに基づいて書籍を検索するクライアント アプリケーションでは、pattern.any によって完全なタイトルが抽出されます。 この書籍検索に対して pattern.any を使用するテンプレート発話は `Was {BookTitle} written by an American this year[?]` です。

次の表では、各行に 2 つのバージョンの発話があります。 一番上の発話は、LUIS が最初に発話を認識する方法です。 書籍タイトルの開始位置と終了位置が明確ではありません。 一番下の発話は、Pattern.any エンティティを使用してエンティティの開始と終了をマークします。

|太字はエンティティを含む発話|
|--|
|`Was The Man Who Mistook His Wife for a Hat and Other Clinical Tales written by an American this year?`<br><br>「**The Man Who Mistook His Wife for a Hat and Other Clinical Tales (自分の妻を帽子と間違える男やその他の臨床例)** 」は、アメリカ人によって今年執筆されましたか?|
|`Was Half Asleep in Frog Pajamas written by an American this year?`<br><br>「**Half Asleep in Frog Pajamas (カエルのパジャマで夢うつつ)** 」は、アメリカ人によって今年執筆されましたか?|
|`Was The Particular Sadness of Lemon Cake: A Novel written by an American this year?`<br><br>「**The Particular Sadness of Lemon Cake (レモンケーキの独特なさびしさ)** 」は、アメリカ人によって今年執筆された小説ですか?|
|`Was There's A Wocket In My Pocket! written by an American this year?`<br><br>「**There's A Wocket In My Pocket! (ポケットにウォケットが!)** 」 は、アメリカ人によって今年執筆されましたか?|
||



## <a name="example-json"></a>JSON の例

次のクエリがあるとします。

`where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`

Pattern.any として抽出する埋め込みフォーム名を使用します。

`Understand your responsibilities as a member of the community`

#### <a name="v2-prediction-endpoint-response"></a>[V2 予測エンドポイントの応答](#tab/V2)

```JSON
"entities": [
  {
    "entity": "understand your responsibilities as a member of the community",
    "type": "FormName",
    "startIndex": 18,
    "endIndex": 78,
    "role": ""
  }
```


#### <a name="v3-prediction-endpoint-response"></a>[V3 予測エンドポイントの応答](#tab/V3)

これは、クエリ文字列で `verbose=false` が設定されている場合の JSON です。

```json
"entities": {
    "FormName": [
        "Understand your responsibilities as a member of the community"
    ]
}
```

これは、クエリ文字列で `verbose=true` が設定されている場合の JSON です。

```json
"entities": {
    "FormName": [
        "Understand your responsibilities as a member of the community"
    ],
    "$instance": {
        "FormName": [
            {
                "type": "FormName",
                "text": "Understand your responsibilities as a member of the community",
                "startIndex": 18,
                "length": 61,
                "modelTypeId": 7,
                "modelType": "Pattern.Any Entity Extractor",
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

この [チュートリアル](luis-tutorial-pattern.md)では、発話が正しい形式であって、データの末尾が発話の残りの単語と混同しやすい可能性がある場合に、**Pattern.any** エンティティを使用して発話からデータを抽出します。
