---
title: CalcHistogram メソッド - Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: CalcHistogram メソッドを使用し、論文エンティティのセットの属性値の分布を計算します。
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: a228c5b90e47c9c24c5da70484a1a28f9a3054b1
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58100481"
---
# <a name="calchistogram-method"></a>CalcHistogram メソッド

**calchistogram** REST API を使用して、論文エンティティのセットの属性値の分布を計算します。          


**REST エンドポイント:**
```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/calchistogram?
``` 
<br>

## <a name="request-parameters"></a>要求パラメーター

Name  |値 | 必須  |説明
-----------|----------|--------|----------
**expr**    |テキスト文字列 | はい  |ヒストグラムの計算の対象となるエンティティを指定するクエリ式。
**model** |テキスト文字列 | いいえ  |クエリを実行するモデルの名前を選択します。  現時点では、既定値は *latest* です。
**attributes** | テキスト文字列 | いいえ <br>既定値: | 応答に含まれている属性値を指定するコンマ区切りリスト。 属性名の大文字と小文字は区別されます。
**count** |Number | いいえ <br>既定値は10 |返す結果の数。
**offset**  |Number | いいえ <br>既定値は0 |返す最初の結果のインデックス。
**timeout**  |Number | いいえ <br>既定値は1,000 |タイムアウト (ミリ秒)。 タイムアウトが経過する前に見つかった解釈だけが返されます。

## <a name="response-json"></a>応答 (JSON)

Name | 説明
--------|---------
**expr**  |要求からの expr パラメーター。
**num_entities** | 一致するエンティティの総数。
**histograms** |  ヒストグラムの配列。要求で指定された属性ごとに 1 つです。
**histograms[x].attribute** | ヒストグラムが計算された属性の名前。
**histograms[x].distinct_values** | この属性の一致エンティティ内の個別の値の数。
**histograms[x].total_count** | この属性の一致エンティティ内の値インスタンスの総数。
**histograms[x].histogram** | この属性のヒストグラム データ。
**histograms[x].histogram[y].value** |  属性の値。
**histograms[x].histogram[y].logprob**  |この属性値を持つ一致エンティティの自然対数確率の合計。
**histograms[x].histogram[y].count**  |この属性値を持つ一致エンティティの数。
**aborted** | 要求がタイムアウトした場合は True です。


#### <a name="example"></a>例:
```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/calchistogram?expr=And(Composite(AA.AuN=='jaime teevan'),Y>2012)&attributes=Y,F.FN&count=4
```
<br>この例では、特定の著者について 2010 年以降の年ごとの刊行物数のヒストグラムを生成するために、まず、クエリ文字列 *papers by jaime teevan after 2012* を指定した **interpret** API を使用してクエリ式を生成できます。

```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/interpret?query=papers by jaime teevan after 2012
```
<br>interpret API から返される最初の解釈の式は、*And(Composite(AA.AuN=='jaime teevan'),Y>2012)* です。
<br>この式の値が **calchistogram** API に渡されます。 *attributes=Y,F.FN* パラメーターは、次の例のように論文数の分布を年および研究分野別に確認する必要があることを示します。
```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/calchistogram?expr=And(Composite(AA.AuN=='jaime teevan'),Y>2012)&attributes=Y,F.FN&count=4
```
<br>この要求に対する応答では、まずクエリ式に一致する 37 の論文があることが示されます。  *Year* 属性については、クエリで指定された 2012 より後の各年 (つまり 2013、2014、2015) に 1 つずつ、合計 3 つの異なる値があります。  3 つの個別の値に対する合計論文数は 37 です。  各 "*年*" について、ヒストグラムは、値、自然対数確率の合計、一致エンティティの数を示しています。     

"*研究分野*" のヒストグラムは、34 の異なる研究分野があることを示しています。 論文は複数の研究分野に関連付けられている可能性があるので、合計数 (53) は一致エンティティの数よりも大きくなることがあります。  34 の異なる値がありますが、*count=4* パラメーターが指定されているので、応答には上位 4 つのみが含まれます。

```JSON
{
  "expr": "And(Composite(AA.AuN=='jaime teevan'),Y>2012)",
  "num_entities": 37,
  "histograms": [
    {
      "attribute": "Y",
      "distinct_values": 3,
      "total_count": 37,
      "histogram": [
        {
          "value": 2014,
          "logprob": -15.753,
          "count": 15
        },
        {
          "value": 2013,
          "logprob": -15.805,
          "count": 12
        },
        {
          "value": 2015,
          "logprob": -16.035,
          "count": 10
        }
      ]
    },
    {
      "attribute": "F.FN",
      "distinct_values": 34,
      "total_count": 53,
      "histogram": [
        {
          "value": "crowdsourcing",
          "logprob": -15.258,
          "count": 9
        },
        {
          "value": "information retrieval",
          "logprob": -16.002,
          "count": 4
        },
        {
          "value": "personalization",
          "logprob": -16.226,
          "count": 3
        },
        {
          "value": "mobile search",
          "logprob": -17.228,
          "count": 2
        }
      ]
    }
  ]
}
```
