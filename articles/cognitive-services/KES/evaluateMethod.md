---
title: Evaluate メソッド - Knowledge Exploration Service API
titlesuffix: Azure Cognitive Services
description: Knowledge Exploration Service (KES) API で Evaluate メソッドを使用する方法について説明します。
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: dcfa9bb7931cf3b682bacf722b67acd6d4a370c0
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2019
ms.locfileid: "55860730"
---
# <a name="evaluate-method"></a>evaluate メソッド

*evaluate* メソッドは、構造化されたクエリ式の出力をインデックス データに基づいて評価して返します。

通常、式は interpret メソッドへの応答から取得されます。  しかし、クエリ式を自分で作成することもできます ([構造化クエリ式](Expressions.md)に関するページをご覧ください)。  

## <a name="request"></a>Request 

`http://<host>/evaluate?expr=<expr>&attributes=<attrs>[&<options>]`   

Name|値|説明
----|----|----
expr       | テキスト文字列 | インデックス エンティティのサブセットを選択する構造化クエリ式。
attributes | テキスト文字列 | 応答に含める属性のコンマ区切りリスト。
count      | 数値 (既定値 =10) | 返される結果の最大数。
offset     | 数値 (既定値 =0) | 返す最初の結果のインデックス。
orderby |   テキスト文字列 | 結果のソートに使用される属性の名前。"*attrname*[:(asc&#124;desc)]" のように、必要に応じて、後に並べ替え順序を指定します (既定値 =asc)。  指定しない場合、結果は自然対数確率の降順で返されます。
timeout  | 数値 (既定値 =1000) | タイムアウト (ミリ秒)。 タイムアウトが経過する前に計算された結果だけが返されます。

*count* パラメーターと *offset* パラメーターを使用すると、複数の要求によって大量の結果を増分的に取得できます。
  
## <a name="response-json"></a>応答 (JSON)
JSONPath|説明
----|----
$.expr | 要求の *expr* パラメーターです。
$.entities | 構造化クエリ式と一致する 0 以上のオブジェクト エンティティの配列。 
$.aborted | 要求がタイムアウトした場合は True です。

各エンティティには、*logprob* 値と要求された属性の値が含まれます。

## <a name="example"></a>例
学術発表の例で、次の要求は構造化クエリ式 (*interpret* 要求の出力から潜在的に得られる) を渡し、上位 2 つの一致エンティティについて少数の属性を取得します。

`http://<host>/evaluate?expr=Composite(Author.Name=='jaime teevan')&attributes=Title,Y,Author.Name,Author.Id&count=2`

応答には、一致率上位 2 つのエンティティが含まれます ("count=2")。  エンティティごとに、タイトル、年、著者名、著者 ID の属性が返されます。  複合属性値の構造がデータ ファイルでの指定とどのように一致するかに注意してください。 

```json
{
  "expr": "Composite(Author.Name=='jaime teevan')",
  "entities": 
  [
    {
      "logprob": -6.645,
      "Ti": "personalizing search via automated analysis of interests and activities",
      "Y": 2005,
      "Author": [
        {
          "Name": "jaime teevan",
          "Id": 1968481722
        },
        {
          "Name": "susan t dumais",
          "Id": 676500258
        },
        {
          "Name": "eric horvitz",
          "Id": 1470530979
        }
      ]
    },
    {
      "logprob": -6.764,
      "Ti": "the perfect search engine is not enough a study of orienteering behavior in directed search",
      "Y": 2004,
      "Author": [
        {
          "Name": "jaime teevan",
          "Id": 1982462162
        },
        {
          "Name": "christine alvarado",
          "Id": 2163512453
        },
        {
          "Name": "mark s ackerman",
          "Id": 2055132526
        },
        {
          "Name": "david r karger",
          "Id": 2012534293
        }
      ]
    }
  ]
}
```
