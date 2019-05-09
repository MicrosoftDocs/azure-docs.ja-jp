---
title: Evaluate メソッド - Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Evaluate メソッドは、クエリ式に基づいてアカデミック エンティティのセットを返すために使用します。
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: d2e628fb7fc502ef9ba81d20680d66f24fd7d138
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58004926"
---
# <a name="evaluate-method"></a>Evaluate メソッド

**evaluate** REST API は、クエリ式に基づいてアカデミック エンティティのセットを返すために使用します。
<br>

**REST エンドポイント:**  
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/evaluate? 
```   
<br>

## <a name="request-parameters"></a>要求パラメーター  

Name     | 値 | 必須  | 説明
-----------|-----------|---------|--------
**expr**       | テキスト文字列 | はい | エンティティが返す必要のあるエンティティを指定するクエリ式。
**model**      | テキスト文字列 | いいえ   | クエリを実行するモデルの名前。  現時点では、既定値は *latest* です。        
**attributes** | テキスト文字列 | いいえ <br>既定値:Id | 応答に含まれている属性値を指定するコンマ区切りリスト。 属性名の大文字と小文字は区別されます。
**count**        | Number | いいえ <br>既定値は10 | 返す結果の数。
**offset**     | Number |   いいえ <br>既定値は0    | 返す最初の結果のインデックス。
**orderby** |   テキスト文字列 | いいえ <br>既定値: 確率の降順 | エンティティの並べ替えに使用される属性の名前。 必要に応じて、昇順/降順を指定できます。 形式: *name:asc* または *name:desc*。
  
 <br>

## <a name="response-json"></a>応答 (JSON)

Name | 説明
-------|-----   
**expr** |  要求からの *expr* パラメーター。
**entities** |  クエリ式に一致する 0 個以上のエンティティの配列。 各エンティティには、自然対数確率値と、要求されたその他の属性の値が含まれています。
**aborted** | 要求がタイムアウトした場合は True です。

<br>

#### <a name="example"></a>例:
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/evaluate?expr=
Composite(AA.AuN=='jaime teevan')&count=2&attributes=Ti,Y,CC,AA.AuN,AA.AuId
```
<br>通常、式は **interpret** メソッドへの応答から取得されます。  しかし、クエリ式を自分で作成することもできます (「[Query Expression Syntax](QueryExpressionSyntax.md)」(クエリ式の構文) をご覧ください)。  
  
*count* および *offset* パラメーターを使用すると、1 つの巨大な (そして低速な可能性もある) 応答が生成される単一の要求を送信せずに多数の結果を取得できます。  この例の要求では、**interpret** API 応答からの最初の解釈に対する式を *expr* 値として使用しました。 *count=2* パラメーターは、2 つのエンティティ結果が要求されていることを指定します。 また、*attributes=Ti,Y,CC,AA.AuN,AA.AuId* パラメーターは、各結果のタイトル、年、引用数、著者名、著者 ID が要求されていることを示します。  属性の一覧については、「[エンティティの属性](EntityAttributes.md)」をご覧ください。
  
```JSON
{
  "expr": "Composite(AA.AuN=='jaime teevan')",
  "entities": 
  [
    {
      "logprob": -15.08,
      "Ti": "personalizing search via automated analysis of interests and activities",
      "Y": 2005,
      "CC": 372,
      "AA": [
        {
          "AuN": "jaime teevan",
          "AuId": 1968481722
        },
        {
          "AuN": "susan t dumais",
          "AuId": 676500258
        },
        {
          "AuN": "eric horvitz",
          "AuId": 1470530979
        }
      ]
    },
    {
      "logprob": -15.389,
      "Ti": "the perfect search engine is not enough a study of orienteering behavior in directed search",
      "Y": 2004,
      "CC": 237,
      "AA": [
        {
          "AuN": "jaime teevan",
          "AuId": 1982462162
        },
        {
          "AuN": "christine alvarado",
          "AuId": 2163512453
        },
        {
          "AuN": "mark s ackerman",
          "AuId": 2055132526
        },
        {
          "AuN": "david r karger",
          "AuId": 2012534293
        }
      ]
    }
  ]
}
 ```
 
