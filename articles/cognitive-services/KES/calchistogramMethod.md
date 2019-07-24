---
title: CalcHistogram メソッド - Knowledge Exploration Service API
titlesuffix: Azure Cognitive Services
description: Knowledge Exploration Service (KES) API で CalcHistogram メソッドを使用する方法について説明します。
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: aaa5b3a85c08f11d821557257de451b8ffc8a3fc
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2019
ms.locfileid: "55860458"
---
# <a name="calchistogram-method"></a>calchistogram メソッド
*calchistogram* メソッドは、構造化クエリ式と一致するオブジェクトを計算し、属性値の分布を計算します。

## <a name="request"></a>Request
`http://<host>/calchistogram?expr=<expr>[&options]` 

Name|値|説明
----|-----|-----------
expr | テキスト文字列 | ヒストグラムの計算の対象となるインデックス エンティティを指定する構造化クエリ式。
attributes | テキスト文字列 (既定値 ="") | 応答に含める属性のコンマ区切りリスト。
count   | 数値 (既定値 =10) | 返す結果の数。
offset  | 数値 (既定値 =0) | 返す最初の結果のインデックス。

## <a name="response-json"></a>応答 (JSON)
JSONPath | 説明
----|----
$.expr | 要求の *expr* パラメーターです。
$.num_entities | 一致するエンティティの総数。
$.histograms |  ヒストグラムの配列。要求される属性ごとに 1 つ。
$.histograms[\*].attribute | ヒストグラムが計算された属性の名前。
$.histograms[\*].distinct_values | この属性のエンティティに一致する個別の値の数。
$.histograms[\*].total_count | この属性のエンティティに一致する値インスタンスの総数。
$.histograms[\*].histogram | この属性のヒストグラム データ。
$.histograms[\*].histogram[\*].value | 属性値。
$.histograms[\*].histogram[\*].logprob  | この属性値を持つ一致エンティティの自然対数確率の合計。
$.histograms[\*].histogram[\*].count    | この属性値を持つ一致エンティティの数。
$.aborted | 要求がタイムアウトした場合は True です。

### <a name="example"></a>例
学術発表の次の例では、2013 年以降の特定の著者による年とキーワードごとの発表数のヒストグラムが計算されます。

`http://<host>/calchistogram?expr=And(Composite(Author.Name=='jaime teevan'),Year>=2013)&attributes=Year,Keyword&count=4`

応答では、クエリ式と一致する論文が 37 件あることが示されます。  *Year* 属性の場合、2013 年以降の年ごとに 1 つずつ合計 3 つの異なる値があります。  3 つの異なる値に対する合計論文数は 37 です。  各 *Year* について、ヒストグラムは、値、自然対数確率の合計、一致するエンティティの数を示しています。     

*Keyword* のヒストグラムには、34 個の異なるキーワードがあることが示されます。 論文は複数のキーワードに関連付けられている可能性があるので、合計数 (53) は一致するエンティティの数よりも大きいことがあります。  34 の異なる値がありますが、"count=4" パラメーターにより、応答には上位 4 つのみが含まれます。

```json
{
  "expr": "And(Composite(Author.Name=='jaime teevan'),Y>=2013)",
  "num_entities": 37,
  "histograms": [
    {
      "attribute": "Y",
      "distinct_values": 3,
      "total_count": 37,
      "histogram": [
        {
          "value": 2014,
          "logprob": -6.894,
          "count": 15
        },
        {
          "value": 2013,
          "logprob": -6.927,
          "count": 12
        },
        {
          "value": 2015,
          "logprob": -7.082,
          "count": 10
        }
      ]
    },
    {
      "attribute": "Keyword",
      "distinct_values": 34,
      "total_count": 53,
      "histogram": [
        {
          "value": "crowdsourcing",
          "logprob": -7.142,
          "count": 9
        },
        {
          "value": "information retrieval",
          "logprob": -7.389,
          "count": 4
        },
        {
          "value": "personalization",
          "logprob": -7.623,
          "count": 3
        },
        {
          "value": "mobile search",
          "logprob": -7.674,
          "count": 2
        }
      ]
    }
  ]
}
``` 
