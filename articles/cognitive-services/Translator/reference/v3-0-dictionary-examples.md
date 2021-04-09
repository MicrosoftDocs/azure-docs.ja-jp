---
title: Translator Dictionary Examples メソッド
titleSuffix: Azure Cognitive Services
description: Translator Dictionary Examples メソッドからは、辞書内の用語がコンテキストで使用される方法を示す例が提供されます。
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 01/21/2020
ms.author: lajanuar
ms.openlocfilehash: e7f0e106c1ca154dcd54990395430b3e0f6c536f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "98895511"
---
# <a name="translator-30-dictionary-examples"></a>Translator 3.0:辞書の例

辞書内の用語がコンテキストで使用される方法を示す例を提供します。 この操作は、[辞書検索](./v3-0-dictionary-lookup.md)と一緒に使用されます。

## <a name="request-url"></a>要求 URL

`POST` 要求の送信先は次のとおりです。

```HTTP
https://api.cognitive.microsofttranslator.com/dictionary/examples?api-version=3.0
```

## <a name="request-parameters"></a>要求パラメーター

クエリ文字列に渡される要求パラメーターを次に示します。

| クエリ パラメーター | 説明 |
| --------- | ----------- |
| api-version <img width=200/> | "**必須のパラメーター**"。<br/>クライアントによって要求される API のバージョン。 値は `3.0` とする必要があります。 |
| from | "**必須のパラメーター**"。<br/>入力テキストの言語を指定します。 ソース言語は、`dictionary` スコープに含まれている[サポートされている言語](./v3-0-languages.md)のいずれかとする必要があります。 |
| to | "**必須のパラメーター**"。<br/>出力テキストの言語を指定します。 ターゲット言語は、`dictionary` スコープに含まれている[サポートされている言語](./v3-0-languages.md)のいずれかとする必要があります。  | 

要求ヘッダーには次のものがあります。

| ヘッダー  | 説明 |
| ------ | ----------- |
| 認証ヘッダー <img width=200/>  | "**必須の要求ヘッダー**" です。<br/><a href="/azure/cognitive-services/translator/reference/v3-0-reference#authentication">認証に使用できるオプション</a>に関するページをご覧ください。 |
| Content-Type | "**必須の要求ヘッダー**" です。<br/>ペイロードのコンテンツ タイプを指定します。 次のいずれかの値になります。`application/json` |
| Content-Length   | "**必須の要求ヘッダー**" です。<br/>要求本文の長さです。 |
| X-ClientTraceId   | **オプション**。<br/>要求を一意に識別する、クライアントで生成された GUID。 `ClientTraceId` という名前のクエリ パラメーターを使用してクエリ文字列内にトレース ID を含める場合、このヘッダーは省略できます。 |

## <a name="request-body"></a>要求本文

要求の本文は JSON 配列です。 各配列要素は、次のプロパティを持つ JSON オブジェクトです。

  * `Text`:検索する用語を指定する文字列。 これは、前の`normalizedText`辞書検索[要求の逆翻訳からの ](./v3-0-dictionary-lookup.md) フィールドの値とする必要があります。 これは、`normalizedSource` フィールドの値にもなります。

  * `Translation`:[辞書検索](./v3-0-dictionary-lookup.md)操作によって既に返されている翻訳済みテキストを指定する文字列。 これは、[辞書検索](./v3-0-dictionary-lookup.md)応答の `translations` リストにある `normalizedTarget` フィールドの値とする必要があります。 サービスからは、ソースとターゲットの特定の単語ペアの例が返されます。

例を示します。

```json
[
    {"Text":"fly", "Translation":"volar"}
]
```

次の制限事項が適用されます。

* 配列に含めることができる要素は、最大でも 10 個です。
* 配列要素のテキスト値は、スペースも含めて 100 文字を超えてはなりません。

## <a name="response-body"></a>応答本文

正常な応答は、入力配列内の文字列ごとに 1 つの結果が含まれる JSON 配列となります。 結果オブジェクトには次のプロパティが含まれています。

  * `normalizedSource`:ソース用語の正規化された形式を与える文字列。 通常、これは、要求本文内の一致するリスト インデックスの `Text` フィールドの値と同一である必要があります。
    
  * `normalizedTarget`:ターゲット用語の正規化形式を与える文字列。 通常、これは、要求本文内の一致するリスト インデックスの `Translation` フィールドの値と同一である必要があります。
  
  * `examples`:(ソース用語、ターゲット用語) ペアの例のリスト。 リストの各要素は、次のプロパティを持つオブジェクトです。

    * `sourcePrefix`:完全な例を形成するために `sourceTerm` の値の "_前_" に連結する文字列。 空白文字は、必要なときは既にそこに存在するので追加しないでください。 この値は空の文字列でもかまいません。

    * `sourceTerm`:実際の用語検索に等しい文字列。 完全な例を形成するために、この文字列は `sourcePrefix` および `sourceSuffix` を伴って追加されます。 その値は区別されており、ユーザー インターフェイス内で、太字にするなど、マークすることができます。

    * `sourceSuffix`:完全な例を形成するために `sourceTerm` の値の "_後_" に連結する文字列。 空白文字は、必要なときは既にそこに存在するので追加しないでください。 この値は空の文字列でもかまいません。

    * `targetPrefix`:ターゲットを除けば、`sourcePrefix` に類似する文字列です。

    * `targetTerm`:ターゲットを除けば、`sourceTerm` に類似する文字列です。

    * `targetSuffix`:ターゲットを除けば、`sourceSuffix` に類似する文字列です。

    > [!NOTE]
    > 辞書に例が存在しない場合、応答は 200 (OK) になりますが、`examples` リストは空のリストとなります。

## <a name="examples"></a>例

この例では、英語の用語 `fly` とそのスペイン語翻訳 `volar` で構成されるペアの例を検索する方法を示します。

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/examples?api-version=3.0&from=en&to=es" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly', 'Translation':'volar'}]"
```

応答本文 (わかりやすくするために短縮) は次のとおりです。

```
[
    {
        "normalizedSource":"fly",
        "normalizedTarget":"volar",
        "examples":[
            {
                "sourcePrefix":"They need machines to ",
                "sourceTerm":"fly",
                "sourceSuffix":".",
                "targetPrefix":"Necesitan máquinas para ",
                "targetTerm":"volar",
                "targetSuffix":"."
            },      
            {
                "sourcePrefix":"That should really ",
                "sourceTerm":"fly",
                "sourceSuffix":".",
                "targetPrefix":"Eso realmente debe ",
                "targetTerm":"volar",
                "targetSuffix":"."
            },
            //
            // ...list abbreviated for documentation clarity
            //
        ]
    }
]
```