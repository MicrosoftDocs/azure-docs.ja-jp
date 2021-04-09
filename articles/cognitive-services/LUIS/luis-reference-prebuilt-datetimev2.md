---
title: datetimeV2 作成済みエンティティ - LUIS
titleSuffix: Azure Cognitive Services
description: この記事には、Language Understanding (LUIS) での datetimeV2 作成済みエンティティについての情報が含まれます。
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 04/13/2020
ms.openlocfilehash: 83522de9c00056a3808b002b3103f45c72553399
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "96013073"
---
# <a name="datetimev2-prebuilt-entity-for-a-luis-app"></a>LUIS アプリの datetimeV2 作成済みエンティティ

**datetimeV2** 作成済みエンティティは、日付と時刻の値を抽出します。 これらの値は、クライアント プログラムで使用できるように標準化された形式に解決されます。 発話に完全ではない日付または時刻が含まれる場合、LUIS は "_過去と未来両方の値_" をエンドポイントの応答に含めます。 このエンティティは既にトレーニングされているので、datetimeV2 を含む発話の例をアプリケーション意図に追加する必要はありません。

## <a name="types-of-datetimev2"></a>datetimeV2 のタイプ
DatetimeV2 は、[Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-DateTime.yaml) GitHub リポジトリから管理されます。

## <a name="example-json"></a>JSON の例

次に示す発話とその部分的な JSON 応答を次に示します。

`8am on may 2nd 2019`

#### <a name="v3-response"></a>[V3 の応答](#tab/1-1)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "datetime",
            "values": [
                {
                    "timex": "2019-05-02T08",
                    "resolution": [
                        {
                            "value": "2019-05-02 08:00:00"
                        }
                    ]
                }
            ]
        }
    ]
}
```

#### <a name="v3-verbose-response"></a>[V3 の詳細な応答](#tab/1-2)

```json

"entities": {
    "datetimeV2": [
        {
            "type": "datetime",
            "values": [
                {
                    "timex": "2019-05-02T08",
                    "resolution": [
                        {
                            "value": "2019-05-02 08:00:00"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.datetime",
                "text": "8am on may 2nd 2019",
                "startIndex": 0,
                "length": 19,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

#### <a name="v2-response"></a>[V2 の応答](#tab/1-3)

```json
"entities": [
  {
    "entity": "8am on may 2nd 2019",
    "type": "builtin.datetimeV2.datetime",
    "startIndex": 0,
    "endIndex": 18,
    "resolution": {
      "values": [
        {
          "timex": "2019-05-02T08",
          "type": "datetime",
          "value": "2019-05-02 08:00:00"
        }
      ]
    }
  }
]
 ```

|プロパティ名 |プロパティの型と説明|
|---|---|
|Entity|**string** - 日付、時刻、日付範囲、時刻範囲の種類を含む発話から抽出されたテキスト。|
|type|**string** - [datetimeV2 のサブタイプ](#subtypes-of-datetimev2)の 1 つ
|startIndex|**int** - エンティティが開始する発話内の位置のインデックス。|
|endIndex|**int** - エンティティが終了する発話内の位置のインデックス。|
|resolution|1、2、または 4 つの[解決の値](#values-of-resolution)を持つ `values` 配列。|
|end|時刻範囲または日付範囲の終わりの値。`value` と同じ形式です。 `type` が `daterange`、`timerange`、または `datetimerange` の場合にのみ使用されます。|

* * *

## <a name="subtypes-of-datetimev2"></a>datetimeV2 のサブタイプ

**datetimeV2** 作成済みエンティティには、以下のサブタイプがあります。後の表ではそれぞれの例を示します。
* `date`
* `time`
* `daterange`
* `timerange`
* `datetimerange`


## <a name="values-of-resolution"></a>解決の値
* 発話内の日付または時刻が完全に指定され、あいまいではない場合、配列の要素は 1 つです。
* datetimeV2 の値があいまいな場合、配列の要素は 2 つです。 あいまいさとしては、特定の年、時、または時刻範囲の欠落などがあります。 例については、「[あいまいな日付](#ambiguous-dates)」をご覧ください。 時刻が午前か 午後かあいまいな場合は、両方の値が含まれます。
* 発話に曖昧な要素が 2 つある場合、配列の要素は 4 つです。 次のような要素が含まれる場合、このあいまいさになります。
  * 年に関してあいまいな日付または日付範囲
  * 午前か午後かに関してあいまいな時刻または 時刻範囲。 たとえば、4 月 3 日3 時 0 分などです。

`values` 配列の各要素には、次のフィールドが含まれることがあります。

|プロパティ名|プロパティの説明|
|--|--|
|timex|[ISO 8601 標準](https://en.wikipedia.org/wiki/ISO_8601)に従う TIMEX 形式および TimeML 言語を使用した注釈のための TIMEX3 属性で表された時刻、日付、日付範囲。|
|mod|値の使用方法を説明する際に使用される用語 (例: `before`、`after`)。|
|type|サブタイプ。`datetime`、`date`、`time`、`daterange`、`timerange`、`datetimerange`、`duration`、`set` のいずれかの項目を指定できます。|
|value|**省略可。** yyyy-MM-dd  (date)、HH:mm:ss (time)、yyyy-MM-dd HH:mm:ss (datetime) 形式の datetime オブジェクト。 `type` が `duration` の場合、値は秒数 (期間) です <br/> `type` が `datetime`、`date`、`time`、または duration の場合にのみ使用されます。|

## <a name="valid-date-values"></a>有効な日付の値

**datetimeV2** は、次の範囲の日付をサポートします。

| Min | Max |
|----------|-------------|
| 1900 年 1 月 1 日   | 2099 年 12 月 31 日 |

## <a name="ambiguous-dates"></a>あいまいな日付

日付が過去または未来のどちらでもありうる場合、LUIS は両方の値を提供します。 たとえば、月と日だけで、年が含まれない発話などです。

たとえば、次の発話があるとします。

`May 2nd`

* 今日の日付が 2017 年 5 月 3 日の場合、LUIS は値として "2017-05-02" と "2018-05-02" の両方を提供します。
* 今日の日付が 2017 年 5 月 1 日のときは、LUIS は値として "2016-05-02" と "2017-05-02" の両方を提供します。

次の例では、エンティティ "5 月 2 日" の解決を示します。 この解決では、今日の日付が 2017 年 5 月 2 日から 2018 年 5 月 1 日の間の日付であるものと仮定します。
`timex` フィールド内の `X` が付いたフィールドは、発話で明示的に指定されていない日付の部分です。

## <a name="date-resolution-example"></a>日付の解決の例


次に示す発話とその部分的な JSON 応答を次に示します。

`May 2nd`

#### <a name="v3-response"></a>[V3 の応答](#tab/2-1)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "XXXX-05-02",
                    "resolution": [
                        {
                            "value": "2019-05-02"
                        },
                        {
                            "value": "2020-05-02"
                        }
                    ]
                }
            ]
        }
    ]
}
```

#### <a name="v3-verbose-response"></a>[V3 の詳細な応答](#tab/2-2)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "XXXX-05-02",
                    "resolution": [
                        {
                            "value": "2019-05-02"
                        },
                        {
                            "value": "2020-05-02"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.date",
                "text": "May 2nd",
                "startIndex": 0,
                "length": 7,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

#### <a name="v2-response"></a>[V2 の応答](#tab/2-3)

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
            "value": "2019-05-02"
          },
          {
            "timex": "XXXX-05-02",
            "type": "date",
            "value": "2020-05-02"
          }
        ]
      }
    }
  ]
```
* * *

## <a name="date-range-resolution-examples-for-numeric-date"></a>数字の日付に対する日付範囲の解決の例

`datetimeV2` エンティティは、日付と時刻の範囲を抽出します。 `start` および `end` フィールドは、範囲の開始と終了を指定します。 発話が `May 2nd to May 5th` の場合、LUIS は、現在の年と次の年の両方に対して **daterange** 値を提供します。 `timex` フィールドで、`XXXX` の値は年のあいまいさを示します。 `P3D` は、時刻の期間が 3 日の長さであることを示します。

次に示す発話とその部分的な JSON 応答を次に示します。

`May 2nd to May 5th`

#### <a name="v3-response"></a>[V3 の応答](#tab/3-1)

```json

"entities": {
    "datetimeV2": [
        {
            "type": "daterange",
            "values": [
                {
                    "timex": "(XXXX-05-02,XXXX-05-05,P3D)",
                    "resolution": [
                        {
                            "start": "2019-05-02",
                            "end": "2019-05-05"
                        },
                        {
                            "start": "2020-05-02",
                            "end": "2020-05-05"
                        }
                    ]
                }
            ]
        }
    ]
}
```


#### <a name="v3-verbose-response"></a>[V3 の詳細な応答](#tab/3-2)

```json

"entities": {
    "datetimeV2": [
        {
            "type": "daterange",
            "values": [
                {
                    "timex": "(XXXX-05-02,XXXX-05-05,P3D)",
                    "resolution": [
                        {
                            "start": "2019-05-02",
                            "end": "2019-05-05"
                        },
                        {
                            "start": "2020-05-02",
                            "end": "2020-05-05"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.daterange",
                "text": "May 2nd to May 5th",
                "startIndex": 0,
                "length": 18,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

#### <a name="v2-response"></a>[V2 の応答](#tab/3-3)

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
            "start": "2019-05-02",
            "end": "2019-05-05"
          }
        ]
      }
    }
  ]
```
* * *

## <a name="date-range-resolution-examples-for-day-of-week"></a>曜日に対する日付範囲の解決の例

次の例は、LUIS が **datetimeV2** を使用して発話 `Tuesday to Thursday` を解決する方法を示しています。 この例では、現在の日付は 6 月 19 日です。 LUIS は、現在の日付より前と後の両方の日付範囲に対する **daterange** 値を含めます。

次に示す発話とその部分的な JSON 応答を次に示します。

`Tuesday to Thursday`

#### <a name="v3-response"></a>[V3 の応答](#tab/4-1)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "daterange",
            "values": [
                {
                    "timex": "(XXXX-WXX-2,XXXX-WXX-4,P2D)",
                    "resolution": [
                        {
                            "start": "2019-10-08",
                            "end": "2019-10-10"
                        },
                        {
                            "start": "2019-10-15",
                            "end": "2019-10-17"
                        }
                    ]
                }
            ]
        }
    ]
}
```

#### <a name="v3-verbose-response"></a>[V3 の詳細な応答](#tab/4-2)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "daterange",
            "values": [
                {
                    "timex": "(XXXX-WXX-2,XXXX-WXX-4,P2D)",
                    "resolution": [
                        {
                            "start": "2019-10-08",
                            "end": "2019-10-10"
                        },
                        {
                            "start": "2019-10-15",
                            "end": "2019-10-17"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.daterange",
                "text": "Tuesday to Thursday",
                "startIndex": 0,
                "length": 19,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

#### <a name="v2-response"></a>[V2 の応答](#tab/4-3)

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
            "start": "2019-04-30",
            "end": "2019-05-02"
          }
        ]
      }
    }
  ]
```
* * *

## <a name="ambiguous-time"></a>あいまいな時刻
時刻または時刻範囲があいまいな場合、値の配列には 2 つの時刻要素が含まれます。 あいまいな時刻がある場合は、値には午前の時刻と 午後の時刻の 両方の値が含まれます。

## <a name="time-range-resolution-example"></a>時刻範囲の解決の例

DatetimeV2 JSON 応答は API V3 で変更されました。 次の例では、時刻の範囲を含む発話の解決に LUIS が **datetimeV2** を使用する方法を示します。

API V2 からの変更点:
* `datetimeV2.timex.type` プロパティは、親レベル `datetimev2.type` で返されるため、返されなくなりました。
* `datetimeV2.value` プロパティは `datetimeV2.timex` に名前が変更されました。

次に示す発話とその部分的な JSON 応答を次に示します。

`from 6pm to 7pm`

#### <a name="v3-response"></a>[V3 の応答](#tab/5-1)

次の JSON は、`verbose` パラメーターが `false` に設定されている場合です。

```JSON

"entities": {
    "datetimeV2": [
        {
            "type": "timerange",
            "values": [
                {
                    "timex": "(T18,T19,PT1H)",
                    "resolution": [
                        {
                            "start": "18:00:00",
                            "end": "19:00:00"
                        }
                    ]
                }
            ]
        }
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 の詳細な応答](#tab/5-2)

次の JSON は、`verbose` パラメーターが `true` に設定されている場合です。

```json

"entities": {
    "datetimeV2": [
        {
            "type": "timerange",
            "values": [
                {
                    "timex": "(T18,T19,PT1H)",
                    "resolution": [
                        {
                            "start": "18:00:00",
                            "end": "19:00:00"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.timerange",
                "text": "from 6pm to 7pm",
                "startIndex": 0,
                "length": 15,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```
#### <a name="v2-response"></a>[V2 の応答](#tab/5-3)

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

* * *

## <a name="time-resolution-example"></a>時刻の解決の例

次に示す発話とその部分的な JSON 応答を次に示します。

`8am`

#### <a name="v3-response"></a>[V3 の応答](#tab/6-1)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "time",
            "values": [
                {
                    "timex": "T08",
                    "resolution": [
                        {
                            "value": "08:00:00"
                        }
                    ]
                }
            ]
        }
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 の詳細な応答](#tab/6-2)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "time",
            "values": [
                {
                    "timex": "T08",
                    "resolution": [
                        {
                            "value": "08:00:00"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.time",
                "text": "8am",
                "startIndex": 0,
                "length": 3,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```
#### <a name="v2-response"></a>[V2 の応答](#tab/6-3)

```json
"entities": [
  {
    "entity": "8am",
    "type": "builtin.datetimeV2.time",
    "startIndex": 0,
    "endIndex": 2,
    "resolution": {
      "values": [
        {
          "timex": "T08",
          "type": "time",
          "value": "08:00:00"
        }
      ]
    }
  }
]
```

* * *

## <a name="deprecated-prebuilt-datetime"></a>非推奨の作成済み datetime

`datetime` 事前構築済みエンティティは非推奨であり、**datetimeV2** に置き換えられています。

LUIS アプリで `datetime` を `datetimeV2` に置き換えるには、次の手順のようにします。

1. LUIS Web インターフェイスの **[Entities]\(エンティティ\)** ウィンドウを開きます。
2. **datetime** 作成済みエンティティを削除します。
3. **[Add prebuilt entity]\(作成済みエンティティの追加\)** をクリックします。
4. **datetimeV2** を選択し、 **[保存]** をクリックします。

## <a name="next-steps"></a>次のステップ

[V3 予測エンドポイント](luis-migration-api-v3.md)の詳細について学習します。

[dimension](luis-reference-prebuilt-dimension.md)、[email](luis-reference-prebuilt-email.md)、[number](luis-reference-prebuilt-number.md) について学習します。

