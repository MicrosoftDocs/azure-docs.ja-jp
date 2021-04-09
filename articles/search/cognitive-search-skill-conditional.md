---
title: 条件付きコグニティブ スキル
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search の条件付きスキルを使用すると、スキルセット定義でフィルター処理、既定値の作成、値のマージができます。
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: f47ca56fa1b40422edeb0d4e11c24be6f60e49e5
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2021
ms.locfileid: "101666355"
---
# <a name="conditional-cognitive-skill"></a>条件付きコグニティブ スキル

**条件付きの** スキルを使用すると、出力に割り当てるデータを決定するブール演算を必要とする Azure Cognitive Search のシナリオが可能になります。 これらのシナリオには、条件に基づくフィルター処理、既定値の割り当て、データのマージなどがあります。

次の擬似コードは、条件付きスキルで実現できる内容を示しています。

```
if (condition) 
    { output = whenTrue } 
else 
    { output = whenFalse } 
```

> [!NOTE]
> このスキルは Azure Cognitive Services API にバインドされていないため、使用しても課金は発生しません。 ただし、1 日あたりのエンリッチメントの数を少数に制限する "無料" リソース オプションをオーバーライドするには、引き続き [Cognitive Services リソースをアタッチ](cognitive-search-attach-cognitive-services.md)する必要があります。

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ConditionalSkill


## <a name="evaluated-fields"></a>評価済みフィールド

このスキルは、入力が評価済みフィールドなので特別です。

次の各項目は、式の有効な値です。

-   注釈パス (式内のパスは、"$(" と ")" で区切る必要があります)
 <br/>
    例 :
    ```
        "= $(/document)"
        "= $(/document/content)"
    ```

-  リテラル (文字列、数値、true、false、null) <br/>
    例 :
    ```
       "= 'this is a string'"   // string (note the single quotation marks)
       "= 34"                   // number
       "= true"                 // Boolean
       "= null"                 // null value
    ```

-  比較演算子 (==、!=、>=、>、<=、<) を使用する式 <br/>
    例 :
    ```
        "= $(/document/language) == 'en'"
        "= $(/document/sentiment) >= 0.5"
    ```

-   ブール演算子 (&&、||、!、^) を使用する式 <br/>
    例 :
    ```
        "= $(/document/language) == 'en' && $(/document/sentiment) > 0.5"
        "= !true"
    ```

-   数値演算子 (+、-、\*、/、%) を使用する式 <br/>
    例 : 
    ```
        "= $(/document/sentiment) + 0.5"         // addition
        "= $(/document/totalValue) * 1.10"       // multiplication
        "= $(/document/lengthInMeters) / 0.3049" // division
    ```

条件付きスキルでは評価がサポートされているため、小規模の変換シナリオで使用できます。 例として、[スキル定義 4](#transformation-example) を参照してください。

## <a name="skill-inputs"></a>スキルの入力
入力では大文字と小文字が区別されます。

| 入力   | 説明 |
|-------------|-------------|
| condition   | この入力は、評価する条件を表す[評価済みフィールド](#evaluated-fields)です。 この条件はブール値 (*true* または *false*) に評価されます。   <br/>  例 : <br/> "= true" <br/> "= $(/document/language) =='fr'" <br/> "= $(/document/pages/\*/language) == $(/document/expectedLanguage)" <br/> |
| whenTrue    | この入力は、条件が *true* に評価された場合に返される値を表す [評価済みフィールド](#evaluated-fields)です。 定数文字列は単一引用符 (' と ') に囲まれて返されます。 <br/>サンプル値: <br/> "= 'contract'"<br/>"= $(/document/contractType)" <br/> "= $(/document/entities/\*)" <br/> |
| whenFalse   | この入力は、条件が *false* に評価された場合に返される値を表す [評価済みフィールド](#evaluated-fields)です。 <br/>サンプル値: <br/> "= 'contract'"<br/>"= $(/document/contractType)" <br/> "= $(/document/entities/\*)" <br/>

## <a name="skill-outputs"></a>スキルの出力
単に "ourput" という 1 つの出力があります。 条件が false の場合は値 *whenFalse* が返され、条件が true の場合は値 *whenTrue* が返されます。

## <a name="examples"></a>例

### <a name="sample-skill-definition-1-filter-documents-to-return-only-french-documents"></a>サンプル スキル定義 1: フランス語のドキュメントのみを返すように、ドキュメントをフィルター処理する

次の出力は、ドキュメントの言語がフランス語の場合、文の配列 ("/document/frenchSentences") を返します。 言語がフランス語ではない場合、その値は *null* に設定されます。

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == 'fr'" },
        { "name": "whenTrue", "source": "/document/sentences" },
        { "name": "whenFalse", "source": "= null" }
    ],
    "outputs": [ { "name": "output", "targetName": "frenchSentences" } ]
}
```
"/document/frenchSentences" が別のスキルの "*コンテキスト*" として使用される場合、そのスキルは "/document/frenchSentences" が *null* に設定されていない場合にのみ実行されます。


### <a name="sample-skill-definition-2-set-a-default-value-for-a-value-that-doesnt-exist"></a>サンプル スキル定義 2: 存在しない値の既定値を設定する

次の出力では注釈 ("/document/languageWithDefault") が作成され、ドキュメントの言語に設定されるか、言語が設定されていない場合は "es" に設定されます。

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == null" },
        { "name": "whenTrue", "source": "= 'es'" },
        { "name": "whenFalse", "source": "= $(/document/language)" }
    ],
    "outputs": [ { "name": "output", "targetName": "languageWithDefault" } ]
}
```

### <a name="sample-skill-definition-3-merge-values-from-two-fields-into-one"></a>サンプル スキル定義 3: 2 つのフィールドの値を 1 つにマージする

この例では、一部の文には *frenchSentiment* プロパティがあります。 *frenchSentiment* プロパティが null の場合は常に *englishSentiment* 値を使用します。 *sentiment* ("/document/sentences/*/sentiment") というメンバーに出力を割り当てます。

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document/sentences/*",
    "inputs": [
        { "name": "condition", "source": "= $(/document/sentences/*/frenchSentiment) == null" },
        { "name": "whenTrue", "source": "/document/sentences/*/englishSentiment" },
        { "name": "whenFalse", "source": "/document/sentences/*/frenchSentiment" }
    ],
    "outputs": [ { "name": "output", "targetName": "sentiment" } ]
}
```

## <a name="transformation-example"></a>変換の例
### <a name="sample-skill-definition-4-data-transformation-on-a-single-field"></a>サンプル スキル定義 4: 単一フィールドでのデータ変換

この例では、0 から 1 の間の *sentiment* を受け取ります。 これを -1 から 1 の間に変換します。 条件付きのスキルを使用して、この小さな変換を実行することができます。

この例では、条件が常に *true* であるため、このスキルの条件付きの側面は使用しません。

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document/sentences/*",
    "inputs": [
        { "name": "condition", "source": "= true" },
        { "name": "whenTrue", "source": "= $(/document/sentences/*/sentiment) * 2 - 1" },
        { "name": "whenFalse", "source": "= 0" }
    ],
    "outputs": [ { "name": "output", "targetName": "normalizedSentiment" } ]
}
```

## <a name="special-considerations"></a>特別な考慮事項
一部のパラメーターは評価されるため、記載されたパターンに慎重に従う必要があります。 式は等号 "=" で始める必要があります。 パスは "$(" と ")" で区切る必要があります。 文字列は必ず単一引用符で囲んでください。 これは、エバリュエーターで文字列と実際のパスや演算子を区別するのに役立ちます。 また、演算子の両側には必ず空白を入れてください (たとえば、パス内の "*" は乗算という意味ではありません)。


## <a name="next-steps"></a>次のステップ

+ [組み込みのスキル](cognitive-search-predefined-skills.md)
+ [スキルセットの定義方法](cognitive-search-defining-skillset.md)
