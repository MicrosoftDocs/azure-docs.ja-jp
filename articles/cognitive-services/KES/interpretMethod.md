---
title: Interpret メソッド - Knowledge Exploration Service API
titlesuffix: Azure Cognitive Services
description: Knowledge Exploration Service (KES) API で Interpret メソッドを使用する方法について説明します。
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 88776e2f4167c950d60c0405dcf950b5173fb989
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2019
ms.locfileid: "55870930"
---
# <a name="interpret-method"></a>interpret メソッド

*interpret* メソッドは、自然言語のクエリ文字列を受け取り、文法とインデックス データに基づいたユーザーの意図の解釈を、書式を設定して返します。  対話形式の検索エクスペリエンスを提供するために、*complete* パラメーターを 1 に設定してオートコンプリートの候補を有効にしたユーザーが文字を入力するたびに、このメソッドを呼び出すことができます。

## <a name="request"></a>Request

`http://<host>/interpret?query=<query>[&<options>]`

Name|値| 説明
----|----|----
query    | テキスト文字列 | ユーザーが入力したクエリ。  complete が 1 に設定されている場合、クエリは、クエリのオートコンプリートの候補を生成するためのプレフィックスとして解釈されます。        
complete | 0 (既定) または 1 | 1 は、文法およびインデックス データに基づいてオートコンプリートの候補が生成されることを意味します。         
count    | 数値 (既定値 =10) | 返される解釈の最大数。         
offset   | 数値 (既定値 =0) | 返される最初の解釈のインデックス。  たとえば、*count=2&offset=0* では、解釈 0 および 1 が返されます。 *count=2&offset=2* では、解釈 2 および 3 が返されます。       
timeout  | 数値 (既定値 =1000) | タイムアウト (ミリ秒)。 タイムアウトが経過する前に見つかった解釈だけが返されます。

*count* パラメーターと *offset* パラメーターを使用すると、複数の要求によって大量の結果を増分的に取得できます。

## <a name="response-json"></a>応答 (JSON)

JSONPath     | 説明
---------|---------
$.query |要求の *query* パラメーター。
$.interpretations   |入力クエリを文法と照合する 0 個以上の方法の配列。
$.interpretations[\*].logprob   |解釈の相対的な対数確率 (<= 0)。  値が高いほど確率が高くなります。
$.interpretations[\*].parse |クエリの各部分がどのように解釈されたかを示す XML 文字列。
$.interpretations[\*].rules |解釈中に呼び出された文法で定義されている 1 つ以上のルールの配列。
$.interpretations[\*].rules[\*].name    |ルールの名前。
$.interpretations[\*].rules[\*].output  |ルールのセマンティック出力。
$.interpretations[\*].rules[\*].output.type |セマンティック出力のデータ型。
$.interpretations[\*].rules[\*].output.value|セマンティック出力の値。  
$.aborted | 要求がタイムアウトした場合は True です。

### <a name="parse-xml"></a>解析 XML

解析 XML は、文法のルールとインデックスの属性に対してどのように一致するかに関する情報の注釈を (完成した) クエリに付けます。  学術的刊行物ドメインの例を次に示します。

```xml
<rule name="#GetPapers">
  papers by 
  <attr name="academic#Author.Name" canonical="heungyeung shum">harry shum</attr>
  <rule name="#GetPaperYear">
    written in
    <attr name="academic#Year">2000</attr>
  </rule>
</rule>
```

`<rule>` 要素は、`name` 属性で指定されたルールに一致するクエリ内の範囲を区切ります。  解析に文法のルール参照が含まれている場合は、入れ子になっている可能性があります。

`<attr>` 要素は、`name` 属性で指定されたインデックス属性に一致するクエリ内の範囲を区切ります。  一致に入力クエリのシノニムを含む場合、`canonical` 属性には、インデックスからのシノニムと一致する正規値が含まれます。

## <a name="example"></a>例

学術的刊行物の例では、次の要求は、プレフィックス クエリ "papers by jaime" に対してオートコンプリートの候補を最大 2 つ返します。

`http://<host>/interpret?query=papers by jaime&complete=1&count=2`

応答には、部分的なクエリ "papers by jaime" を補完する可能性が高い上位 2 つ ("count=2") の解釈 ("papers by jaime teevan" と "papers by jaime green") が含まれています。  要求で "complete=1" が指定されたので、サービスは、著者 "jaime" との完全一致のみを考慮する代わりにクエリ入力候補を生成しました。 正規値 "j l green" は、解析で指定されているようにシノニム "jamie green" を介して照合されます。


```json
{
  "query": "papers by jaime",
  "interpretations": [
    {
      "logprob": -5.615,
      "parse": "<rule name=\"#GetPapers\">papers by <attr name=\"academic#Author.Name\">jaime teevan</attr></rule>",
      "rules": [
        {
          "name": "#GetPapers",
          "output": {
            "type": "query",
            "value": "Composite(Author.Name=='jaime teevan')"
          }
        }
      ]
    },
    {
      "logprob": -5.849,
      "parse": "<rule name=\"#GetPapers\">papers by <attr name=\"academic#Author.Name\" canonical=\"j l green\">jaime green</attr></rule>",
      "rules": [
        {
          "name": "#GetPapers",
          "output": {
            "type": "query",
            "value": "Composite(Author.Name=='j l green')"
          }
        }
      ]
    }
  ]
}
```  

この例のように、セマンティック出力の種類が "query" である場合、一致するオブジェクトを取得するには、*expr* パラメーターを介して *output.value* を [*evaluate*](evaluateMethod.md) API に渡します。

`http://<host>/evaluate?expr=Composite(AA.AuN=='jaime teevan')`
  
