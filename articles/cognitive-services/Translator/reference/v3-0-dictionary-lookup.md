---
title: Translator の Dictionary Lookup メソッド
titleSuffix: Azure Cognitive Services
description: Dictionary Lookup メソッドからは、単語や少数の慣用句に対して代替の翻訳が提供されます。
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 01/21/2020
ms.author: lajanuar
ms.openlocfilehash: 88a76a16de43853a001f5db895d6ad418940de0f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "98895494"
---
# <a name="translator-30-dictionary-lookup"></a>Translator 3.0:辞書検索

単語や少数の慣用句に対し代替の翻訳を提供します。 各翻訳には品詞と、逆翻訳のリストが含まれます。 逆翻訳により、ユーザーはコンテキスト内の翻訳を理解することができます。 [辞書の例](./v3-0-dictionary-examples.md)操作では、さらにドリル ダウンすることで、各翻訳ペアの使用例を参照することができます。

## <a name="request-url"></a>要求 URL

`POST` 要求の送信先は次のとおりです。

```HTTP
https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0
```

## <a name="request-parameters"></a>要求パラメーター

クエリ文字列に渡される要求パラメーターを次に示します。

| クエリ パラメーター  | 説明 |
| ------ | ----------- |
| api-version <img width=200/>   | "**必須のパラメーター**"。<br/>クライアントによって要求される API のバージョン。 値は `3.0` とする必要があります |
| from | "**必須のパラメーター**"。<br/>入力テキストの言語を指定します。 ソース言語は、`dictionary` スコープに含まれている[サポートされている言語](./v3-0-languages.md)のいずれかとする必要があります。 |
| to   | "**必須のパラメーター**"。<br/>出力テキストの言語を指定します。 ターゲット言語は、`dictionary` スコープに含まれている[サポートされている言語](v3-0-languages.md)のいずれかとする必要があります。 |


要求ヘッダーには次のものがあります。

| ヘッダー  | 説明 |
| ------ | ----------- |
| 認証ヘッダー <img width=200/>  | "**必須の要求ヘッダー**" です。<br/><a href="/azure/cognitive-services/translator/reference/v3-0-reference#authentication">認証に使用できるオプション</a>に関するページをご覧ください。 |
| Content-Type | "**必須の要求ヘッダー**" です。<br/>ペイロードのコンテンツ タイプを指定します。 次のいずれかの値になります。`application/json` |
| Content-Length   | "**必須の要求ヘッダー**" です。<br/>要求本文の長さです。 |
| X-ClientTraceId   | **オプション**。<br/>要求を一意に識別する、クライアントで生成された GUID。 `ClientTraceId` という名前のクエリ パラメーターを使用してクエリ文字列内にトレース ID を含める場合、このヘッダーは省略できます。 |

## <a name="request-body"></a>要求本文

要求の本文は JSON 配列です。 各配列要素は、`Text` という名前の文字列プロパティ (検索する用語を表す) を持つ JSON オブジェクトです。

```json
[
    {"Text":"fly"}
]
```

次の制限事項が適用されます。

* 配列に含めることができる要素は、最大でも 10 個です。
* 配列要素のテキスト値は、スペースも含めて 100 文字を超えてはなりません。

## <a name="response-body"></a>応答本文

正常な応答は、入力配列内の文字列ごとに 1 つの結果が含まれる JSON 配列となります。 結果オブジェクトには次のプロパティが含まれています。

  * `normalizedSource`:ソース用語の正規化された形式を与える文字列。 たとえば、要求が "JOHN" の場合、正規化された形式は "john" になります。 このフィールドの内容は[検索例](./v3-0-dictionary-examples.md)の入力となります。
    
  * `displaySource`:エンド ユーザー表示に最適な形式でソース用語を与える文字列。 たとえば、入力が "JOHN" である場合、表示形式は名前の通常の綴り方を反映し、"John" となります。 

  * `translations`:ソース用語の翻訳のリストです。 リストの各要素は、次のプロパティを持つオブジェクトです。

    * `normalizedTarget`:ターゲット言語でのこの用語の正規化された形式を与える文字列。 この値を[検索例](./v3-0-dictionary-examples.md)の入力として使用する必要があります。

    * `displayTarget`:用語をターゲット言語の、エンド ユーザー表示に最適な形式で提供する文字列。 通常、これは `normalizedTarget` とは大文字/小文字の設定の点で異なるだけとなります。 たとえば、"Juan" のような固有名詞の場合は、`normalizedTarget = "juan"` と `displayTarget = "Juan"` が使用されます。

    * `posTag`:この用語を品詞タグに関連付ける文字列です。

        | タグ名 | 説明  |
        |----------|--------------|
        | ADJ      | 形容詞   |
        | ADV      | 副詞      |
        | CONJ     | 接続詞 |
        | DET      | 限定詞  |
        | MODAL    | 動詞        |
        | NOUN     | 名詞        |
        | PREP     | 前置詞 |
        | PRON     | 代名詞     |
        | VERB     | 動詞        |
        | OTHER    | その他        |

        実装上の注意点として、これらのタグは、英語側で品詞タグ付けによって決定されており、ソース/ターゲット ペアごとに最も頻度の高いタグが使用されます。 そのため、ユーザーがスペイン語の単語を頻繁に英語の別の品詞に翻訳する場合、タグは問題になる可能性があります (スペイン語の単語に対して)。

    * `confidence`:値は 0.0 から 1.0 の範囲となります。この値は翻訳ペアの "信頼度" (もっと正確に言うならば、"トレーニング データでの確率") を表します。 1 個のソース単語の信頼度スコアの合計は、1.0 になる場合とならない場合があります。 

    * `prefixWord`:翻訳のプレフィックスとして表示する単語を指定する文字列。 現時点では、これは性を区別する限定詞を持つ言語において、名詞の姓を区別する限定詞です。 たとえば、スペイン語の単語 "mosca" の接頭詞は "la" です。これは "mosca" がスペイン語の女性名詞だからです。 これは翻訳にのみ依存し、ソースには依存しません。 接頭詞がない場合は、空の文字列となります。
    
    * `backTranslations`:ターゲットの "逆翻訳" のリストです。 たとえば、ターゲットが翻訳されるソースの単語です。 リストには要求されたソース単語が必ず含められます (たとえば、検索されるソース単語が "fly" である場合、"fly" は必ず `backTranslations` リストに含められます)。 ただし、それが先頭に来るという保証はなく、多くの場合先頭にはなりません。 `backTranslations` リストの各要素は、次のプロパティで記述されるオブジェクトです。

        * `normalizedText`:ターゲットの逆翻訳であるソース用語の正規化された形式を与える文字列。 この値を[検索例](./v3-0-dictionary-examples.md)の入力として使用する必要があります。        

        * `displayText`:ターゲットの逆翻訳であるソース用語を、エンド ユーザー表示に最適な形式で与える文字列。

        * `numExamples`:この翻訳ペアで利用できる例の数を表す整数。 実際の例は、[検索例](./v3-0-dictionary-examples.md)の個別の呼び出しによって取得する必要があります。 この数は主に UX での表示を容易にすることを目的としています。 たとえば、例の数が 0 より大きい場合はユーザー インターフェイスによって逆翻訳へのハイパーリンクが追加され、例が存在しない場合は、逆翻訳がプレーン テキストとして表示されます。 [検索例](./v3-0-dictionary-examples.md)の呼び出しによって返される例の実際の数は `numExamples` 未満となる場合があります。これは、追加のフィルター処理を適用して "悪い" 例を即時に削除することができるからです。
        
        * `frequencyCount`:データ内でのこの翻訳ペアの頻度を表す整数。 このフィールドの主な目的は、最も頻繁に使用されている用語が最初に表示されるように逆翻訳を並べ替える手段をユーザー インターフェイスに提供することにあります。

    > [!NOTE]
    > 検索した用語が辞書に存在しない場合、応答は 200 (OK) になりますが、`translations` リストは空のリストとなります。

## <a name="examples"></a>例

この例では、英語の用語 `fly` についてスペイン語での代替翻訳を検索する方法を示します。

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0&from=en&to=es" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly'}]"
```

応答本文 (わかりやすくするために短縮) は次のとおりです。

```
[
    {
        "normalizedSource":"fly",
        "displaySource":"fly",
        "translations":[
            {
                "normalizedTarget":"volar",
                "displayTarget":"volar",
                "posTag":"VERB",
                "confidence":0.4081,
                "prefixWord":"",
                "backTranslations":[
                    {"normalizedText":"fly","displayText":"fly","numExamples":15,"frequencyCount":4637},
                    {"normalizedText":"flying","displayText":"flying","numExamples":15,"frequencyCount":1365},
                    {"normalizedText":"blow","displayText":"blow","numExamples":15,"frequencyCount":503},
                    {"normalizedText":"flight","displayText":"flight","numExamples":15,"frequencyCount":135}
                ]
            },
            {
                "normalizedTarget":"mosca",
                "displayTarget":"mosca",
                "posTag":"NOUN",
                "confidence":0.2668,
                "prefixWord":"",
                "backTranslations":[
                    {"normalizedText":"fly","displayText":"fly","numExamples":15,"frequencyCount":1697},
                    {"normalizedText":"flyweight","displayText":"flyweight","numExamples":0,"frequencyCount":48},
                    {"normalizedText":"flies","displayText":"flies","numExamples":9,"frequencyCount":34}
                ]
            },
            //
            // ...list abbreviated for documentation clarity
            //
        ]
    }
]
```

この例では、検索される用語が有効な辞書ペアに存在しない場合、どうなるかを示します。

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0&from=en&to=es" -H "X-ClientTraceId: 875030C7-5380-40B8-8A03-63DACCF69C11" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly123456'}]"
```

辞書内で用語が見つからないため、応答本文には空の `translations` リストが含まれます。

```
[
    {
        "normalizedSource":"fly123456",
        "displaySource":"fly123456",
        "translations":[]
    }
]
```