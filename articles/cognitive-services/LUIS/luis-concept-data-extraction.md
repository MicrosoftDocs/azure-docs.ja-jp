---
title: データの抽出 - LUIS
titleSuffix: Azure Cognitive Services
description: 意図とエンティティが含まれる発話テキストからデータを抽出します。 Language Understanding (LUIS) から抽出できるデータの種類について説明します。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: ff0a9838d1fcc9db3b6cc25b47c840e01056e6cd
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703144"
---
# <a name="extract-data-from-utterance-text-with-intents-and-entities"></a>意図とエンティティが含まれる発話テキストからデータを抽出する
LUIS を使用すると、ユーザーの自然言語での発話から情報を取得できます。 この情報は、アクションを実行するために、プログラム、アプリケーション、またはチャットボットで使用できるような方法で抽出されます。 以降のセクションで、JSON の例を使用して、意図とエンティティから返されるデータについて説明します。

抽出するのが最も困難なデータは機械学習データです。その理由は、テキストが完全一致ではないためです。 機械学習[エンティティ](luis-concept-entity-types.md)のデータ抽出は、期待どおりのデータを受け取っていると確信できるまでは、[作成サイクル](luis-concept-app-iteration.md)の一環とする必要があります。

## <a name="data-location-and-key-usage"></a>データの場所とキー使用法
LUIS では、公開されている[エンドポイント](luis-glossary.md#endpoint)からデータを提供しています。 **HTTPS 要求** (POST または GET) には、発話と、ステージング環境や運用環境など、オプションの構成がいくつか含まれます。

#### <a name="v2-prediction-endpoint-requesttabv2"></a>[V2 予測エンドポイントの要求](#tab/V2)

`https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&q=book 2 tickets to paris`

#### <a name="v3-prediction-endpoint-requesttabv3"></a>[V3 予測エンドポイントの要求](#tab/V3)

`https://westus.api.cognitive.microsoft.com/luis/v3.0-preview/apps/<appID>/slots/<slot-type>/predict?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&query=book 2 tickets to paris`

V3 予測エンドポイントの詳細については[こちら](luis-migration-api-v3.md)を参照してください。

* * * 

`appID` は、LUIS アプリの **[設定]** ページで確認できます。また、LUIS アプリの編集時に URL の一部 (`/apps/` の後) として確認できます。 `subscription-key` は、アプリの照会に使用するエンドポイント キーです。 LUIS を学習している間は無料のオーサリング/スターター キーを使用できますが、エンドポイント キーを、[想定される LUIS の使用法](luis-boundaries.md#key-limits)をサポートするキーに変更することが重要です。 `timezoneOffset` の単位は分です。

**HTTPS 応答**には、ステージングまたは運用エンドポイントの現在公開されているモデルに基づいて LUIS によって判別できるすべての意図およびエンティティの情報が含まれています。 エンドポイント URL は [LUIS](luis-reference-regions.md) Web サイトの **[Manage]\(管理\)** セクションの **[Keys and endpoints]\(キーとエンドポイント\)** ページにあります。

## <a name="data-from-intents"></a>意図からのデータ
プライマリ データは、最上位スコアの**意図の名前**です。 エンドポイントの応答は次のとおりです。

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 予測エンドポイントの応答](#tab/V2)

```JSON
{
  "query": "when do you open next?",
  "topScoringIntent": {
    "intent": "GetStoreInfo",
    "score": 0.984749258
  },
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 予測エンドポイントの応答](#tab/V3)

```JSON
{
  "query": "when do you open next?",
  "prediction": {
    "normalizedQuery": "when do you open next?",
    "topIntent": "GetStoreInfo",
    "intents": {
        "GetStoreInfo": {
            "score": 0.984749258
        }
    }
  },
  "entities": []
}
```

V3 予測エンドポイントの詳細については[こちら](luis-migration-api-v3.md)を参照してください。

* * * 

|データ オブジェクト|データ型|データの場所|値|
|--|--|--|--|
|Intent|string|topScoringIntent.intent|"GetStoreInfo"|

チャットボットまたは LUIS 呼び出し元アプリが複数の意図のスコアに基づいて決定を行う場合、すべての意図のスコアを返します。


#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 予測エンドポイントの応答](#tab/V2)

querystring パラメーター `verbose=true` を設定します。 エンドポイントの応答は次のとおりです。

```JSON
{
  "query": "when do you open next?",
  "topScoringIntent": {
    "intent": "GetStoreInfo",
    "score": 0.984749258
  },
  "intents": [
    {
      "intent": "GetStoreInfo",
      "score": 0.984749258
    },
    {
      "intent": "None",
      "score": 0.2040639
    }
  ],
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 予測エンドポイントの応答](#tab/V3)

querystring パラメーター `show-all-intents=true` を設定します。 エンドポイントの応答は次のとおりです。

```JSON
{
    "query": "when do you open next?",
    "prediction": {
        "normalizedQuery": "when do you open next?",
        "topIntent": "GetStoreInfo",
        "intents": {
            "GetStoreInfo": {
                "score": 0.984749258
            },
            "None": {
                 "score": 0.2040639
            }
        },
        "entities": {
        }
    }
}
```

V3 予測エンドポイントの詳細については[こちら](luis-migration-api-v3.md)を参照してください。

* * * 

意図は、スコアが最も高いものから最も低いものへと並べ替えられます。

|データ オブジェクト|データ型|データの場所|値|Score|
|--|--|--|--|:--|
|Intent|string|intents[0].intent|"GetStoreInfo"|0.984749258|
|Intent|string|intents[1].intent|"None"|0.0168218873|

事前構築済みのドメインを追加する場合、意図の名前は、`Utilties` や`Communication` などのドメインと、意図を表します。

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 予測エンドポイントの応答](#tab/V2)

```JSON
{
  "query": "Turn on the lights next monday at 9am",
  "topScoringIntent": {
    "intent": "Utilities.ShowNext",
    "score": 0.07842206
  },
  "intents": [
    {
      "intent": "Utilities.ShowNext",
      "score": 0.07842206
    },
    {
      "intent": "Communication.StartOver",
      "score": 0.0239675418
    },
    {
      "intent": "None",
      "score": 0.0168218873
    }],
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 予測エンドポイントの応答](#tab/V3)

```JSON
{
    "query": "Turn on the lights next monday at 9am",
    "prediction": {
        "normalizedQuery": "Turn on the lights next monday at 9am",
        "topIntent": "Utilities.ShowNext",
        "intents": {
            "Utilities.ShowNext": {
                "score": 0.07842206
            },
            "Communication.StartOver": {
                "score": 0.0239675418
            },
            "None": {
                "score": 0.00085447653
            }
        },
        "entities": []
    }
}
```

V3 予測エンドポイントの詳細については[こちら](luis-migration-api-v3.md)を参照してください。

* * * 

|Domain|データ オブジェクト|データ型|データの場所|値|
|--|--|--|--|--|
|Utilities|Intent|string|intents[0].intent|"<b>Utilities</b>.ShowNext"|
|Communication|Intent|string|intents[1].intent|<b>Communication</b>.StartOver"|
||Intent|string|intents[2].intent|"None"|


## <a name="data-from-entities"></a>エンティティからのデータ
ほとんどのチャットボットおよびアプリケーションでは、意図名以外の情報も必要です。 この省略可能な追加データは、発話で検出されたエンティティから取得されます。 エンティティの種類ごとに、一致に関するさまざまな情報が返されます。

発話内の 1 つの単語またはフレーズは、複数のエンティティと一致する可能性があります。 その場合、一致するエンティティごとにそのスコアが返されます。

すべてのエンティティが、エンドポイントから応答の**エンティティ**配列で返されます。

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 予測エンドポイントの応答](#tab/V2)

```JSON
"entities": [
  {
    "entity": "bob jones",
    "type": "Name",
    "startIndex": 0,
    "endIndex": 8,
    "score": 0.473899543
  },
  {
    "entity": "3",
    "type": "builtin.number",
    "startIndex": 16,
    "endIndex": 16,
    "resolution": {
      "value": "3"
    }
  }
]
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 予測エンドポイントの応答](#tab/V3)

```JSON
"entities": {
    "name":["bob jones"],
    "number": [3]
}
```
V3 予測エンドポイントの詳細については[こちら](luis-migration-api-v3.md)を参照してください。

* * * 

## <a name="tokenized-entity-returned"></a>返されるトークン化されたエンティティ
いくつかの[カルチャ](luis-language-support.md#tokenization)では、`entity` 値が[トークン化](luis-glossary.md#token)されたエンティティ オブジェクトが返されます。 エンティティ オブジェクト内で LUIS によって返される startIndex および endIndex は、トークン化された新しい値にマップされていませんが、代わりに生のエンティティをプログラムで抽出できるように、元のクエリにマップされています。 

たとえば、ドイツ語の場合、単語 `das Bauernbrot` は `das bauern brot` にトークン化されます。 トークン化された値 `das bauern brot` が返され、元の値は、元のクエリの startIndex および endIndex からプログラムで判断でき、`das Bauernbrot` が提供されます。

## <a name="simple-entity-data"></a>シンプル エンティティ データ

[シンプル エンティティ](reference-entity-simple.md)は、機械学習された値です。 これは、単語またはフレーズです。

## <a name="composite-entity-data"></a>複合エンティティ データ

[複合エンティティ](reference-entity-composite.md)は、事前構築済みエンティティ、シンプル、正規表現、リスト エンティティなどの他のエンティティで構成されます。 個別のエンティティが、エンティティ全体を形成します。 

## <a name="list-entity-data"></a>リスト エンティティ データ

[リスト エンティティ](reference-entity-list.md)は、固定かつ限定された関連単語セットとそのシノニムを表します。 LUIS では、リスト エンティティの追加の値は検出されません。 現在のリストに基づいて新しい単語の候補を表示するには、 **[Recommend] (推奨)** 機能を使用します。 同じ値を持つリスト エンティティが複数存在する場合は、エンドポイント クエリに各エンティティが返されます。 

## <a name="prebuilt-entity-data"></a>事前構築済みのエンティティ データ
[事前構築済み](luis-concept-entity-types.md)のエンティティは、オープン ソースの [Recognizers-Text](https://github.com/Microsoft/Recognizers-Text) プロジェクトを使用して、正規表現の一致に基づき検出されます。 事前構築済みのエンティティは、エンティティ配列で返され、`builtin::` というプレフィックスが付加された種類の名前が使用されます。 次のテキストは、返される事前構築済みのエンティティを含む発話の例です。

`Dec 5th send to +1 360-555-1212`

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 予測エンドポイントの応答](#tab/V2)

```JSON
"entities": [
    {
      "entity": "dec 5th",
      "type": "builtin.datetimeV2.date",
      "startIndex": 0,
      "endIndex": 6,
      "resolution": {
        "values": [
          {
            "timex": "XXXX-12-05",
            "type": "date",
            "value": "2017-12-05"
          },
          {
            "timex": "XXXX-12-05",
            "type": "date",
            "value": "2018-12-05"
          }
        ]
      }
    },
    {
      "entity": "1",
      "type": "builtin.number",
      "startIndex": 18,
      "endIndex": 18,
      "resolution": {
        "value": "1"
      }
    },
    {
      "entity": "360",
      "type": "builtin.number",
      "startIndex": 20,
      "endIndex": 22,
      "resolution": {
        "value": "360"
      }
    },
    {
      "entity": "555",
      "type": "builtin.number",
      "startIndex": 26,
      "endIndex": 28,
      "resolution": {
        "value": "555"
      }
    },
    {
      "entity": "1212",
      "type": "builtin.number",
      "startIndex": 32,
      "endIndex": 35,
      "resolution": {
        "value": "1212"
      }
    },
    {
      "entity": "5th",
      "type": "builtin.ordinal",
      "startIndex": 4,
      "endIndex": 6,
      "resolution": {
        "value": "5"
      }
    },
    {
      "entity": "1 360 - 555 - 1212",
      "type": "builtin.phonenumber",
      "startIndex": 18,
      "endIndex": 35,
      "resolution": {
        "value": "1 360 - 555 - 1212"
      }
    }
  ]
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 予測エンドポイントの応答](#tab/V3)

querystring パラメーター `verbose=true` なし。

```json
"entities": {
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "XXXX-12-05",
                    "value": "2018-12-05"
                },
                {
                    "timex": "XXXX-12-05",
                    "value": "2019-12-05"
                }
            ]
        }
    ],
    "ordinal": [
        {
            "offset": 5,
            "relativeTo": "start"
        }
    ],
    "ordinalV2": [
        {
            "offset": 5,
            "relativeTo": "start"
        }
    ],
    "number": [
        1360,
        555,
        1212
    ],
    "phonenumber": [
        "1 360-555-1212"
    ]
}
```

querystring パラメーター `verbose=true` あり。

```json

"entities": {
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "XXXX-12-05",
                    "value": "2018-12-05"
                },
                {
                    "timex": "XXXX-12-05",
                    "value": "2019-12-05"
                }
            ]
        }
    ],
    "ordinal": [
        {
            "offset": 5,
            "relativeTo": "start"
        }
    ],
    "ordinalV2": [
        {
            "offset": 5,
            "relativeTo": "start"
        }
    ],
    "number": [
        1360,
        555,
        1212
    ],
    "phonenumber": [
        "1 360-555-1212"
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.date",
                "text": "Dec 5th",
                "startIndex": 0,
                "length": 7,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "ordinal": [
            {
                "type": "builtin.ordinal",
                "text": "5th",
                "startIndex": 4,
                "length": 3,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "ordinalV2": [
            {
                "type": "builtin.ordinalV2",
                "text": "5th",
                "startIndex": 4,
                "length": 3,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "number": [
            {
                "type": "builtin.number",
                "text": "1 360",
                "startIndex": 17,
                "length": 5,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "builtin.number",
                "text": "555",
                "startIndex": 23,
                "length": 3,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "builtin.number",
                "text": "1212",
                "startIndex": 27,
                "length": 4,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "phonenumber": [
            {
                "type": "builtin.phonenumber",
                "text": "1 360-555-1212",
                "startIndex": 17,
                "length": 14,
                "score": 1.0,
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

V3 予測エンドポイントの詳細については[こちら](luis-migration-api-v3.md)を参照してください。

* * * 
## <a name="regular-expression-entity-data"></a>正規表現エンティティ データ

[正規表現エンティティ](reference-entity-regular-expression.md)は、指定した正規表現パターンに基づいてエンティティを抽出します。

## <a name="extracting-names"></a>名前の抽出
名前は、文字と単語のほぼすべての組み合わせが考えられるため、発話から名前を取得することは困難です。 抽出する名前の種類に応じて、複数のオプションがあります。 次の推奨事項はルールではなく、詳細なガイドラインです。

### <a name="add-prebuilt-personname-and-geographyv2-entities"></a>事前構築済みの PersonName エンティティと GeographyV2 エンティティを追加する

[PersonName](luis-reference-prebuilt-person.md) エンティティと [GeographyV2](luis-reference-prebuilt-geographyV2.md) エンティティは、いくつかの[言語カルチャ](luis-reference-prebuilt-entities.md)で利用できます。 

### <a name="names-of-people"></a>人の名前

人の名前は、言語およびカルチャに応じて、幾分ある種の形式を持つことがあります。 事前構築済みの **[personName](luis-reference-prebuilt-person.md)** エンティティ、または氏名の[ロール](luis-concept-roles.md)が含まれる **[簡易エンティティ](luis-concept-entity-types.md#simple-entity)** のどちらかを使用します。 

簡易エンティティを使用する場合は、必ず発話のさまざまな部分に姓と名を使用している例を提供してください。また、None 意図を含むあらゆる意図にわたるさまざまな長さの発話で、姓と名を使用している例を提供してください。 エンドポイントの発話を定期的に[確認](luis-how-to-review-endoint-utt.md)して、適切に予測されていないすべての名前にラベルを付けます。

### <a name="names-of-places"></a>場所の名前

場所の名前には、市区町村、郡、州、都道府県、国/地域などの既知の名前が設定されています。 位置情報を抽出するには、事前構築済みエンティティ **[geographyV2](luis-reference-prebuilt-geographyv2.md)** を使用します。

### <a name="new-and-emerging-names"></a>新しい名前

一部のアプリでは、製品や企業などの新しい名前を検索できる必要があります。 これらの種類の名前は、最も困難な種類のデータ抽出です。 **[簡易エンティティ](luis-concept-entity-types.md#simple-entity)** から始めて、[フレーズ リスト](luis-concept-feature.md)を追加します。 エンドポイントの発話を定期的に[確認](luis-how-to-review-endoint-utt.md)して、適切に予測されていないすべての名前にラベルを付けます。

## <a name="pattern-roles-data"></a>パターンの役割データ
役割は、エンティティのコンテキスト上の差異です。


#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 予測エンドポイントの応答](#tab/V2)

エンティティ名は `Location` で、2 つのロール、`Origin` と `Destination` があります。

```JSON
"entities": [
  {
    "entity": "bob jones",
    "type": "Employee",
    "startIndex": 5,
    "endIndex": 13,
    "score": 0.922820568,
    "role": ""
  },
  {
    "entity": "seattle",
    "type": "Location",
    "startIndex": 20,
    "endIndex": 26,
    "score": 0.948008537,
    "role": "Origin"
  },
  {
    "entity": "redmond",
    "type": "Location",
    "startIndex": 31,
    "endIndex": 37,
    "score": 0.7047979,
    "role": "Destination"
  }
]
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 予測エンドポイントの応答](#tab/V3)

V3 では、**ロール名**はオブジェクトのプライマリ名になります。 

エンティティ名は `Location` で、2 つのロール、`Origin` と `Destination` があります。

querystring パラメーター `verbose=true` なし。

```json
"entities": {
    "Employee": [
        "bob jones"
    ],
    "Origin": [
        "seattle"
    ],
    "Destination": [
        "redmond"
    ]
}
```

querystring パラメーター `verbose=true` あり。

```json
"entities": {
    "Employee": [
        "bob jones"
    ],
    "LocationOrigin": [
        "seattle"
    ],
    "LocationDestination": [
        "redmond"
    ],
    "$instance": {
        "Employee": [
            {
                "type": "Employee",
                "text": "bob jones",
                "startIndex": 5,
                "length": 9,
                "score": 0.982873261,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "Origin": [
            {
                "role": "Origin",
                "type": "Location",
                "text": "seattle",
                "startIndex": 20,
                "length": 7,
                "score": 0.9913306,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "Destination": [
            {
                "role": "Destination",
                "type": "Location",
                "text": "redmond",
                "startIndex": 31,
                "length": 7,
                "score": 0.898179531,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]

}
```

V3 予測エンドポイントの詳細については[こちら](luis-migration-api-v3.md)を参照してください。

* * *

## <a name="patternany-entity-data"></a>Pattern.any エンティティ データ

[Pattern.any](reference-entity-pattern-any.md) は、エンティティの開始位置と終了位置を示すためにパターンのテンプレート発話でのみ使用される、可変長プレースホルダーです。  

## <a name="sentiment-analysis"></a>センチメント分析
センチメント分析が構成されている場合、LUIS の json 応答には、センチメント分析が含まれます。 センチメント分析の詳細については、[Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/) のドキュメントを参照してください。

### <a name="sentiment-data"></a>センチメント データ
センチメント データは 1 と 0 の間のスコアで、1 に近いほどポジティブなセンチメントを示し、0 に近いほどネガティブな感情を示します。

カルチャが `en-us` の場合、応答は次のとおりです。

```JSON
"sentimentAnalysis": {
  "label": "positive",
  "score": 0.9163064
}
```

その他のすべてのカルチャについては、応答は次のとおりです。

```JSON
"sentimentAnalysis": {
  "score": 0.9163064
}
```


### <a name="key-phrase-extraction-entity-data"></a>キー フレーズ抽出エンティティ データ
キー フレーズ抽出エンティティでは、[Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/) によって提供される、発話内のキー フレーズを返します。


#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 予測エンドポイントの応答](#tab/V2)

```JSON
{
  "query": "Is there a map of places with beautiful views on a favorite trail?",
  "topScoringIntent": {
    "intent": "GetJobInformation",
    "score": 0.764368951
  },
  "intents": [
    ...
  ],
  "entities": [
    {
      "entity": "beautiful views",
      "type": "builtin.keyPhrase",
      "startIndex": 30,
      "endIndex": 44
    },
    {
      "entity": "map of places",
      "type": "builtin.keyPhrase",
      "startIndex": 11,
      "endIndex": 23
    },
    {
      "entity": "favorite trail",
      "type": "builtin.keyPhrase",
      "startIndex": 51,
      "endIndex": 64
    }
  ]
}
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 予測エンドポイントの応答](#tab/V3)

V3 予測エンドポイントの詳細については[こちら](luis-migration-api-v3.md)を参照してください。

querystring パラメーター `verbose=true` なし。

```json
"entities": {
    "keyPhrase": [
        "map of places",
        "beautiful views",
        "favorite trail"
    ]
}
```

querystring パラメーター `verbose=true` あり。

```json
"entities": {
    "keyPhrase": [
        "map of places",
        "beautiful views",
        "favorite trail"
    ],
    "$instance": {
        "keyPhrase": [
            {
                "type": "builtin.keyPhrase",
                "text": "map of places",
                "startIndex": 11,
                "length": 13,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "builtin.keyPhrase",
                "text": "beautiful views",
                "startIndex": 30,
                "length": 15,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "builtin.keyPhrase",
                "text": "favorite trail",
                "startIndex": 51,
                "length": 14,
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

V3 予測エンドポイントの詳細については[こちら](luis-migration-api-v3.md)を参照してください。

* * *


## <a name="data-matching-multiple-entities"></a>複数のエンティティに一致するデータ

LUIS では、発話内で検出されたすべてのエンティティを返します。 その結果、チャットボットでは、結果に基づいて決定を行う必要があります。 発話内には多数のエンティティが存在する可能性があります。

`book me 2 adult business tickets to paris tomorrow on air france`

LUIS エンドポイントでは、異なるエンティティ内に同じデータが見つかる場合があります。

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 予測エンドポイントの応答](#tab/V2)

```JSON
{
  "query": "book me 2 adult business tickets to paris tomorrow on air france",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 1.0
  },
  "intents": [
    {
      "intent": "BookFlight",
      "score": 1.0
    },
    {
      "intent": "Concierge",
      "score": 0.04216196
    },
    {
      "intent": "None",
      "score": 0.03610297
    }
  ],
  "entities": [
    {
      "entity": "air france",
      "type": "Airline",
      "startIndex": 54,
      "endIndex": 63,
      "score": 0.8291798
    },
    {
      "entity": "adult",
      "type": "Category",
      "startIndex": 10,
      "endIndex": 14,
      "resolution": {
        "values": [
          "adult"
        ]
      }
    },
    {
      "entity": "paris",
      "type": "Cities",
      "startIndex": 36,
      "endIndex": 40,
      "resolution": {
        "values": [
          "Paris"
        ]
      }
    },
    {
      "entity": "tomorrow",
      "type": "builtin.datetimeV2.date",
      "startIndex": 42,
      "endIndex": 49,
      "resolution": {
        "values": [
          {
            "timex": "2018-02-21",
            "type": "date",
            "value": "2018-02-21"
          }
        ]
      }
    },
    {
      "entity": "paris",
      "type": "Location::ToLocation",
      "startIndex": 36,
      "endIndex": 40,
      "score": 0.9730773
    },
    {
      "entity": "2",
      "type": "builtin.number",
      "startIndex": 8,
      "endIndex": 8,
      "resolution": {
        "value": "2"
      }
    },
    {
      "entity": "business",
      "type": "Seat",
      "startIndex": 16,
      "endIndex": 23,
      "resolution": {
        "values": [
          "business"
        ]
      }
    },
    {
      "entity": "2 adult business",
      "type": "TicketSeatOrder",
      "startIndex": 8,
      "endIndex": 23,
      "score": 0.8784727
    }
  ],
  "compositeEntities": [
    {
      "parentType": "TicketSeatOrder",
      "value": "2 adult business",
      "children": [
        {
          "type": "Category",
          "value": "adult"
        },
        {
          "type": "builtin.number",
          "value": "2"
        },
        {
          "type": "Seat",
          "value": "business"
        }
      ]
    }
  ]
}
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 予測エンドポイントの応答](#tab/V3)

querystring パラメーター `verbose=true` なし。

```json
"entities": {
    "TicketsOrder": [
        {
            "number": [
                2
            ],
            "PassengerCategory": [
                "adult"
            ],
            "TravelClass": [
                "business"
            ]
        }
    ],
    "Location::LocationTo": [
        "paris"
    ],
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "2019-09-28",
                    "value": "2019-09-28"
                }
            ]
        }
    ],
    "Airline": [
        "air france"
    ]
}
```

querystring パラメーター `verbose=true` あり。


```json
"entities": {
    "TicketsOrder": [
        {
            "number": [
                2
            ],
            "PassengerCategory": [
                "adult"
            ],
            "TravelClass": [
                "business"
            ],
            "$instance": {
                "number": [
                    {
                        "type": "builtin.number",
                        "text": "2",
                        "startIndex": 8,
                        "length": 1,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ],
                "PassengerCategory": [
                    {
                        "type": "PassengerCategory",
                        "text": "adult",
                        "startIndex": 10,
                        "length": 5,
                        "score": 0.9503733,
                        "modelTypeId": 3,
                        "modelType": "Hierarchical Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ],
                "TravelClass": [
                    {
                        "type": "TravelClass",
                        "text": "business",
                        "startIndex": 16,
                        "length": 8,
                        "score": 0.950095,
                        "modelTypeId": 3,
                        "modelType": "Hierarchical Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ]
            }
        }
    ],
    "Location::LocationTo": [
        "paris"
    ],
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "2019-09-28",
                    "value": "2019-09-28"
                }
            ]
        }
    ],
    "Airline": [
        "air france"
    ],
    "$instance": {
        "TicketsOrder": [
            {
                "type": "TicketsOrder",
                "text": "2 adult business",
                "startIndex": 8,
                "length": 16,
                "score": 0.942183256,
                "modelTypeId": 4,
                "modelType": "Composite Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "Location::LocationTo": [
            {
                "type": "Location::LocationTo",
                "text": "paris",
                "startIndex": 36,
                "length": 5,
                "score": 0.9905354,
                "modelTypeId": 3,
                "modelType": "Hierarchical Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.date",
                "text": "tomorrow",
                "startIndex": 42,
                "length": 8,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "Airline": [
            {
                "type": "Airline",
                "text": "air france",
                "startIndex": 54,
                "length": 10,
                "score": 0.9455415,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

V3 予測エンドポイントの詳細については[こちら](luis-migration-api-v3.md)を参照してください。

* * *

## <a name="data-matching-multiple-list-entities"></a>複数のリスト エンティティに一致するデータ

単語またはフレーズが複数のリスト エンティティに一致すると、エンドポイント クエリからそれぞれのリスト エンティティが返されます。

クエリ `when is the best time to go to red rock?` の場合、アプリの複数のリストに単語 `red` があると、LUIS ではすべてのエンティティを認識し、JSON エンドポイントの応答の一部としてエンティティの配列を返します。 

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 予測エンドポイントの応答](#tab/V2)

```JSON
{
  "query": "when is the best time to go to red rock?",
  "topScoringIntent": {
    "intent": "Calendar.Find",
    "score": 0.06701678
  },
  "entities": [
    {
      "entity": "red",
      "type": "Colors",
      "startIndex": 31,
      "endIndex": 33,
      "resolution": {
        "values": [
          "Red"
        ]
      }
    },
    {
      "entity": "red rock",
      "type": "Cities",
      "startIndex": 31,
      "endIndex": 38,
      "resolution": {
        "values": [
          "Destinations"
        ]
      }
    }
  ]
}
```



#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 予測エンドポイントの応答](#tab/V3)

クエリ文字列に `verbose=true` なし。

```JSON
{
    "query": "when is the best time to go to red rock",
    "prediction": {
        "normalizedQuery": "when is the best time to go to red rock",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.823669851
            }
        },
        "entities": {
            "Colors": [
                [
                    "red"
                ]
            ],
            "Cities": [
                [
                    "Destinations"
                ]
            ]
        }
    }
}
```


クエリ文字列に `verbose=true` あり。

```JSON
{
    "query": "when is the best time to go to red rock",
    "prediction": {
        "normalizedQuery": "when is the best time to go to red rock",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.823669851
            }
        },
        "entities": {
            "Colors": [
                [
                    "red"
                ]
            ],
            "Cities": [
                [
                    "Destinations"
                ]
            ],
            "$instance": {
                "Colors": [
                    {
                        "type": "Colors",
                        "text": "red",
                        "startIndex": 31,
                        "length": 3,
                        "modelTypeId": 5,
                        "modelType": "List Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ],
                "Cities": [
                    {
                        "type": "Cities",
                        "text": "red rock",
                        "startIndex": 31,
                        "length": 8,
                        "modelTypeId": 5,
                        "modelType": "List Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ]
            }
        }
    }
}
```

V3 予測エンドポイントの詳細については[こちら](luis-migration-api-v3.md)を参照してください。

* * *

## <a name="next-steps"></a>次の手順

[エンティティの追加](luis-how-to-add-entities.md)に関するページで、LUIS アプリにエンティティを追加する方法の詳細を確認します。
