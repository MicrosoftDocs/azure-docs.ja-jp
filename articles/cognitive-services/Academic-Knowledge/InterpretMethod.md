---
title: Interpret メソッド - Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Interpret メソッドを使用して、Microsoft Cognitive Services の Academic Graph データと Academic Grammar に基づくユーザー クエリ文字列の解釈を書式設定して返します。
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: b679f1da0ada3e61fca79cdb985a43dc445877ce
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57975493"
---
# <a name="interpret-method"></a>Interpret メソッド

**interpret** REST API は、エンド ユーザーのクエリ文字列 (つまり、アプリケーションのユーザーが入力したクエリ) を受け取り、Academic Graph データと Academic Grammar に基づくユーザーの意図の解釈を書式設定して返します。

対話型エクスペリエンスを提供するには、ユーザーが文字を入力するたびに、このメソッドを繰り返し呼び出します。 その場合、**complete** パラメーターを 1 に設定して、オートコンプリートの候補を有効にする必要があります。 アプリケーションにオートコンプリートが不要な場合は、**complete** パラメーターを 0 にする必要があります。

**REST エンドポイント:**

    https://westus.api.cognitive.microsoft.com/academic/v1.0/interpret?

## <a name="request-parameters"></a>要求パラメーター

Name     | 値 | 必須  | 説明
---------|---------|---------|---------
**query**    | テキスト文字列 | はい | ユーザーが入力したクエリ。  complete が 1 に設定されている場合、クエリは、クエリのオートコンプリートの候補を生成するためのプレフィックスとして解釈されます。        
**model**    | テキスト文字列 | いいえ   | クエリを実行するモデルの名前。  現時点では、既定値は *latest* です。        
**complete** | 0 または 1 | いいえ <br>既定値: 0  | 1 は、文法およびグラフ データに基づいてオートコンプリートの候補が生成されることを意味します。         
**count**    | Number | いいえ <br>既定値: 10 | 返される解釈の最大数。         
**offset**   | Number | いいえ <br>既定値: 0  | 返される最初の解釈のインデックス。 たとえば、*count=2&offset=0* では、解釈 0 および 1 が返されます。 *count=2&offset=2* では、解釈 2 および 3 が返されます。       
**timeout**  | Number | いいえ <br>既定値: 1000 | タイムアウト (ミリ秒)。 タイムアウトが経過する前に見つかった解釈だけが返されます。

<br>
  
## <a name="response-json"></a>応答 (JSON)

Name     | 説明
---------|---------
**query** |要求からの *query* パラメーター。
**interpretations** |ユーザー入力を文法と照合する 0 以上の異なる方法の配列。
**interpretations[x].logprob**  |解釈の相対的な自然対数確率。 値が大きいほど確率が高くなります。
**interpretations[x].parse**  |クエリの各部分がどのように解釈されたかを示す XML 文字列。
**interpretations[x].rules**  |解釈中に呼び出された文法で定義されている 1 つ以上のルールの配列。 Academic Knowledge API の場合、必ず 1 つのルールがあります。
**interpretations[x].rules[y].name**  |ルールの名前。
**interpretations[x].rules[y].output**  |ルールの出力。
**interpretations[x].rules[y].output.type** |ルールの出力のデータ型。  Academic Knowledge API の場合、これは常に "query" です。
**interpretations[x].rules[y].output.value**  |ルールの出力。 Academic Knowledge API の場合、これは evaluate および calchistogram メソッドに渡すことができるクエリ式文字列です。
**aborted** | 要求がタイムアウトした場合は True です。

<br>

#### <a name="example"></a>例:
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/interpret?query=papers by jaime&complete=1&count=2
 ```
<br>以下の応答には、部分的なユーザー入力 *papers by jaime* を補完する可能性が高い上位 2 つの解釈 (*papers by jaime teevan* および *papers by jaime green*) が含まれています (パラメーター *count=2* が指定されているため)。  要求で *complete=1* が指定されたので、サービスは、著者 *jaime* との完全一致のみを考慮する代わりにクエリ入力候補を生成しました。 基準の値 *j l green* は、解析で指定されているようにシノニム *jamie green* を介して照合されます。


```JSON
{
  "query": "papers by jaime",
  "interpretations": [
    {
      "logprob": -12.728,
      "parse": "<rule name=\"#GetPapers\">papers by <attr name=\"academic#AA.AuN\">jaime teevan</attr></rule>",
      "rules": [
        {
          "name": "#GetPapers",
          "output": {
            "type": "query",
            "value": "Composite(AA.AuN=='jaime teevan')"
          }
        }
      ]
    },
    {
      "logprob": -12.774,
      "parse": "<rule name=\"#GetPapers\">papers by <attr name=\"academic#AA.AuN\" canonical=\"j l green\">jaime green</attr></rule>",
      "rules": [
        {
          "name": "#GetPapers",
          "output": {
            "type": "query",
            "value": "Composite(AA.AuN=='j l green')"
          }
        }
      ]
    }
  ]
}
```  
<br>解釈のエンティティの結果を取得するには、**interpret** API から *output.value* を使用し、*expr* パラメーターを介してそれを **evaluate** API に渡します。 この例では、最初の解釈のクエリは次のとおりです。 
```
evaluate?expr=Composite(AA.AuN=='jaime teevan')
```
 
