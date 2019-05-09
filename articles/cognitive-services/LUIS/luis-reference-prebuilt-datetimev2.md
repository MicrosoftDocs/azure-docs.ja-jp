---
title: datetimeV2 作成済みエンティティ
titleSuffix: Azure
description: この記事には、Language Understanding (LUIS) での datetimeV2 作成済みエンティティについての情報が含まれます。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 02/28/2019
ms.author: diberry
ms.openlocfilehash: 6b4c3f7445d18ab1548fd63b1f4d12c5901cf949
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2019
ms.locfileid: "57339523"
---
# <a name="datetimev2-prebuilt-entity-for-a-luis-app"></a>LUIS アプリの datetimeV2 作成済みエンティティ

**datetimeV2** 作成済みエンティティは、日付と時刻の値を抽出します。 これらの値は、クライアント プログラムで使用できるように標準化された形式に解決されます。 発話に完全ではない日付または時刻が含まれる場合、LUIS は "_過去と未来両方の値_" をエンドポイントの応答に含めます。 このエンティティは既にトレーニングされているので、datetimeV2 を含む発話の例をアプリケーション意図に追加する必要はありません。 

## <a name="types-of-datetimev2"></a>datetimeV2 のタイプ
datetimeV2 は [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-DateTime.yaml) GitHub リポジトリから管理されます。

## <a name="example-json"></a>JSON の例 
次の例の JSON 応答には、`datetimeV2` エンティティとサブタイプ `datetime` が含まれます。 他のタイプの datetimeV2 エンティティの例については、「[datetimeV2 のサブタイプ](#subtypes-of-datetimev2)</a>」をご覧ください。

```json
"entities": [
  {
    "entity": "8am on may 2nd 2017",
    "type": "builtin.datetimeV2.datetime",
    "startIndex": 0,
    "endIndex": 18,
    "resolution": {
      "values": [
        {
          "timex": "2017-05-02T08",
          "type": "datetime",
          "value": "2017-05-02 08:00:00"
        }
      ]
    }
  }
]
  ```

## <a name="json-property-descriptions"></a>JSON のプロパティの説明

|プロパティ名 |プロパティの型と説明|
|---|---|
|エンティティ|**string** - 日付、時刻、日付範囲、時刻範囲の種類を含む発話から抽出されたテキスト。|
|type|**string** - [datetimeV2 のサブタイプ](#subtypes-of-datetimev2)の 1 つ
|startIndex|**int** - エンティティが開始する発話内の位置のインデックス。|
|endIndex|**int** - エンティティが終了する発話内の位置のインデックス。|
|resolution|1、2、または 4 つの[解決の値](#values-of-resolution)を持つ `values` 配列。|
|end|時刻範囲または日付範囲の終わりの値。`value` と同じ形式です。 `type` が `daterange`、`timerange`、または `datetimerange` の場合にのみ使用されます。|

## <a name="subtypes-of-datetimev2"></a>datetimeV2 のサブタイプ

**datetimeV2** 作成済みエンティティには、以下のサブタイプがあります。後の表ではそれぞれの例を示します。
* `date`
* `time`
* `daterange`
* `timerange`
* `datetimerange`
* `duration`
* `set`

## <a name="values-of-resolution"></a>解決の値
* 発話内の日付または時刻が完全に指定され、あいまいではない場合、配列の要素は 1 つです。
* datetimeV2 の値があいまいな場合、配列の要素は 2 つです。 あいまいさとしては、特定の年、時、または時刻範囲の欠落などがあります。 例については、「[あいまいな日付](#ambiguous-dates)」をご覧ください。 時刻が午前か 午後かあいまいな場合は、両方の値が含まれます。
* 発話に曖昧な要素が 2 つある場合、配列の要素は 4 つです。 次のような要素が含まれる場合、このあいまいさになります。
  * 年に関してあいまいな日付または日付範囲
  * 午前か午後かに関してあいまいな時刻または 時刻範囲。 たとえば、4 月 3 日3 時 0 分などです。

`values` 配列の各要素には、次のフィールドが含まれることがあります。 

|プロパティ名|プロパティの説明|
|--|--|
|timex|[ISO 8601 標準](https://en.wikipedia.org/wiki/ISO_8601)に従う TIMEX 形式および TimeML 言語を使用した注釈のための TIMEX3 属性で表された時刻、日付、日付範囲。 この注釈については、[TIMEX のガイドライン](http://www.timeml.org/tempeval2/tempeval2-trial/guidelines/timex3guidelines-072009.pdf)のページをご覧ください。|
|type|サブタイプ。次のいずれかです: datetime、date、time、daterange、timerange、datetimerange、duration、set。|
|value|**省略可能。** yyyy:MM:dd (date)、HH:mm:ss (time)、yyyy:MM:dd HH:mm:ss (datetime) の形式の datetime オブジェクト。 `type` が `duration` の場合、値は秒数 (期間) です <br/> `type` が `datetime`、`date`、`time`、または duration の場合にのみ使用されます。|

## <a name="valid-date-values"></a>有効な日付の値

**datetimeV2** は、次の範囲の日付をサポートします。

| Min | max |
|----------|-------------|
| 1900 年 1 月 1 日   | 2099 年 12 月 31 日 |

## <a name="ambiguous-dates"></a>あいまいな日付

日付が過去または未来のどちらでもありうる場合、LUIS は両方の値を提供します。 たとえば、月と日だけで、年が含まれない発話などです。  

たとえば、"5 月 2 日" という発話について考えます。
* 今日の日付が 2017 年 5 月 3 日の場合、LUIS は値として "2017-05-02" と "2018-05-02" の両方を提供します。 
* 今日の日付が 2017 年 5 月 1 日のときは、LUIS は値として "2016-05-02" と "2017-05-02" の両方を提供します。

次の例では、エンティティ "5 月 2 日" の解決を示します。 この解決では、今日の日付が 2017 年 5 月 2 日から 2018 年 5 月 1 日の間の日付であるものと仮定します。
`timex` フィールド内の `X` が付いたフィールドは、発話で明示的に指定されていない日付の部分です。

```json
  "entities": [
    {
      "entity": "may 2nd",
      "type": "builtin.datetimeV2.date",
      "startIndex": 0,
      "endIndex": 6,
      "resolution": {
        "values": [
          {
            "timex": "XXXX-05-02",
            "type": "date",
            "value": "2017-05-02"
          },
          {
            "timex": "XXXX-05-02",
            "type": "date",
            "value": "2018-05-02"
          }
        ]
      }
    }
  ]
```

## <a name="date-range-resolution-examples-for-numeric-date"></a>数字の日付に対する日付範囲の解決の例

`datetimeV2` エンティティは、日付と時刻の範囲を抽出します。 `start` および `end` フィールドは、範囲の開始と終了を指定します。 発話が "5 月 2 日から 5 月 5 日" の場合、LUIS は今年と翌年の両方に対して **daterange** 値を提供します。 `timex` フィールドで、`XXXX` の値は年のあいまいさを示します。 `P3D` は、時刻の期間が 3 日の長さであることを示します。

```json
"entities": [
    {
      "entity": "may 2nd to may 5th",
      "type": "builtin.datetimeV2.daterange",
      "startIndex": 0,
      "endIndex": 17,
      "resolution": {
        "values": [
          {
            "timex": "(XXXX-05-02,XXXX-05-05,P3D)",
            "type": "daterange",
            "start": "2017-05-02",
            "end": "2017-05-05"
          },
          {
            "timex": "(XXXX-05-02,XXXX-05-05,P3D)",
            "type": "daterange",
            "start": "2018-05-02",
            "end": "2018-05-05"
          }
        ]
      }
    }
  ]
```

## <a name="date-range-resolution-examples-for-day-of-week"></a>曜日に対する日付範囲の解決の例

次の例では、"火曜日から木曜日" という発話の解決に LUIS が **datetimeV2** を使用する方法を示します。 この例では、現在の日付は 6 月 19 日です。 LUIS は、現在の日付より前と後の両方の日付範囲に対する **daterange** 値を含めます。

```json
  "entities": [
    {
      "entity": "tuesday to thursday",
      "type": "builtin.datetimeV2.daterange",
      "startIndex": 0,
      "endIndex": 19,
      "resolution": {
        "values": [
          {
            "timex": "(XXXX-WXX-2,XXXX-WXX-4,P2D)",
            "type": "daterange",
            "start": "2017-06-13",
            "end": "2017-06-15"
          },
          {
            "timex": "(XXXX-WXX-2,XXXX-WXX-4,P2D)",
            "type": "daterange",
            "start": "2017-06-20",
            "end": "2017-06-22"
          }
        ]
      }
    }
  ]
```
## <a name="ambiguous-time"></a>あいまいな時刻
時刻または時刻範囲があいまいな場合、値の配列には 2 つの時刻要素が含まれます。 あいまいな時刻がある場合は、値には午前の時刻と 午後の時刻の 両方の値が含まれます。

## <a name="time-range-resolution-example"></a>時刻範囲の解決の例

次の例では、時刻の範囲を含む発話の解決に LUIS が **datetimeV2** を使用する方法を示します。

```json
  "entities": [
    {
      "entity": "6pm to 7pm",
      "type": "builtin.datetimeV2.timerange",
      "startIndex": 0,
      "endIndex": 9,
      "resolution": {
        "values": [
          {
            "timex": "(T18,T19,PT1H)",
            "type": "timerange",
            "start": "18:00:00",
            "end": "19:00:00"
          }
        ]
      }
    }
  ]
```

## <a name="deprecated-prebuilt-datetime"></a>非推奨の作成済み datetime

`datetime` 事前構築済みエンティティは非推奨であり、**datetimeV2** に置き換えられています。 

LUIS アプリで `datetime` を `datetimeV2` に置き換えるには、次の手順のようにします。

1. LUIS Web インターフェイスの **[Entities]\(エンティティ\)** ウィンドウを開きます。 
2. **datetime** 作成済みエンティティを削除します。
3. **[Add prebuilt entity]\(作成済みエンティティの追加\)** をクリックします。
4. **datetimeV2** を選択し、**[保存]** をクリックします。

## <a name="next-steps"></a>次の手順

[dimension](luis-reference-prebuilt-dimension.md)、[email](luis-reference-prebuilt-email.md)、[number](luis-reference-prebuilt-number.md) について学習します。 

