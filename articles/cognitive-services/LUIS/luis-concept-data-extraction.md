---
title: データの抽出
titleSuffix: Language Understanding - Azure Cognitive Services
description: 意図とエンティティが含まれる発話テキストからデータを抽出します。 Language Understanding (LUIS) から抽出できるデータの種類について説明します。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: diberry
ms.openlocfilehash: 35f1521884de3a4a0971b6e1c00f92a9094a8550
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/12/2019
ms.locfileid: "59526291"
---
# <a name="extract-data-from-utterance-text-with-intents-and-entities"></a>意図とエンティティが含まれる発話テキストからデータを抽出する
LUIS を使用すると、ユーザーの自然言語での発話から情報を取得できます。 この情報は、アクションを実行するために、プログラム、アプリケーション、またはチャットボットで使用できるような方法で抽出されます。 以降のセクションで、JSON の例を使用して、意図とエンティティから返されるデータについて説明します。

抽出するのが最も困難なデータは機械学習データです。その理由は、テキストが完全一致ではないためです。 機械学習[エンティティ](luis-concept-entity-types.md)のデータ抽出は、期待どおりのデータを受け取っていると確信できるまでは、[作成サイクル](luis-concept-app-iteration.md)の一環とする必要があります。

## <a name="data-location-and-key-usage"></a>データの場所とキー使用法
LUIS では、公開されている[エンドポイント](luis-glossary.md#endpoint)からデータを提供しています。 **HTTPS 要求** (POST または GET) には、発話と、ステージング環境や運用環境など、オプションの構成がいくつか含まれます。

`https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&q=book 2 tickets to paris`

`appID` は、LUIS アプリの **[設定]** ページで確認できます。また、LUIS アプリの編集時に URL の一部 (`/apps/` の後) として確認できます。 `subscription-key` は、アプリの照会に使用するエンドポイント キーです。 LUIS を学習している間は無料のオーサリング/スターター キーを使用できますが、エンドポイント キーを、[想定される LUIS の使用法](luis-boundaries.md#key-limits)をサポートするキーに変更することが重要です。 `timezoneOffset` の単位は分です。

**HTTPS 応答**には、ステージングまたは運用エンドポイントの現在公開されているモデルに基づいて LUIS によって判別できるすべての意図およびエンティティの情報が含まれています。 エンドポイント URL は [LUIS](luis-reference-regions.md) Web サイトの **[Manage]\(管理\)** セクションの **[Keys and endpoints]\(キーとエンドポイント\)** ページにあります。

## <a name="data-from-intents"></a>意図からのデータ
プライマリ データは、最上位スコアの**意図の名前**です。 `MyStore` [クイック スタート](luis-quickstart-intents-only.md)を使用すると、エンドポイントの応答は次のとおりです。

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

|データ オブジェクト|データ型|データの場所|値|
|--|--|--|--|
|意図|String|topScoringIntent.intent|"GetStoreInfo"|

チャットボットまたは LUIS 呼び出し元アプリが複数の意図のスコアに基づいて決定を行う場合、querystring パラメーター `verbose=true` を設定して、すべての意図のスコアを返します。 エンドポイントの応答は次のとおりです。

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

意図は、スコアが最も高いものから最も低いものへと並べ替えられます。

|データ オブジェクト|データ型|データの場所|値|Score|
|--|--|--|--|:--|
|意図|String|intents[0].intent|"GetStoreInfo"|0.984749258|
|意図|String|intents[1].intent|"None"|0.0168218873|

事前構築済みのドメインを追加する場合、意図の名前は、`Utilties` や`Communication` などのドメインと、意図を表します。

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

|Domain|データ オブジェクト|データ型|データの場所|値|
|--|--|--|--|--|
|Utilities|意図|String|intents[0].intent|"<b>Utilities</b>.ShowNext"|
|Communication|意図|String|intents[1].intent|<b>Communication</b>.StartOver"|
||意図|String|intents[2].intent|"None"|


## <a name="data-from-entities"></a>エンティティからのデータ
ほとんどのチャットボットおよびアプリケーションでは、意図名以外の情報も必要です。 この省略可能な追加データは、発話で検出されたエンティティから取得されます。 エンティティの種類ごとに、一致に関するさまざまな情報が返されます。

発話内の 1 つの単語またはフレーズは、複数のエンティティと一致する可能性があります。 その場合、一致するエンティティごとにそのスコアが返されます。

すべてのエンティティが、エンドポイントから応答の**エンティティ**配列で返されます。

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

## <a name="tokenized-entity-returned"></a>返されるトークン化されたエンティティ
いくつかの[カルチャ](luis-language-support.md#tokenization)では、`entity` 値が[トークン化](luis-glossary.md#token)されたエンティティ オブジェクトが返されます。 エンティティ オブジェクト内で LUIS によって返される startIndex および endIndex は、トークン化された新しい値にマップされていませんが、代わりに生のエンティティをプログラムで抽出できるように、元のクエリにマップされています。 

たとえば、ドイツ語の場合、単語 `das Bauernbrot` は `das bauern brot` にトークン化されます。 トークン化された値 `das bauern brot` が返され、元の値は、元のクエリの startIndex および endIndex からプログラムで判断でき、`das Bauernbrot` が提供されます。

## <a name="simple-entity-data"></a>シンプル エンティティ データ

[シンプル エンティティ](luis-concept-entity-types.md)は、機械学習された値です。 これは、単語またはフレーズです。

`Bob Jones wants 3 meatball pho`

前の発話では、`Bob Jones` は、シンプルな `Customer` エンティティとしてラベルが付けられています。

エンドポイントから返されるデータには、エンティティ名、発話から検出されたテキスト、検出されたテキストの場所、およびスコアが含まれます。

```JSON
"entities": [
  {
  "entity": "bob jones",
  "type": "Customer",
  "startIndex": 0,
  "endIndex": 8,
  "score": 0.473899543
  }
]
```

|データ オブジェクト|エンティティ名|値|
|--|--|--|
|シンプル エンティティ|`Customer`|`bob jones`|

## <a name="hierarchical-entity-data"></a>階層構造エンティティ データ

**階層エンティティは最終的に非推奨になります。エンティティのサブタイプを決定するには、階層エンティティではなく、[エンティティ ロール](luis-concept-roles.md)を使用します。**

[階層構造](luis-concept-entity-types.md)エンティティは、機械学習され、単語またはフレーズを含めることができます。 子は、コンテキストによって識別されます。 テキストの完全一致を使用して親子関係を見つけようとしている場合は、[リスト](#list-entity-data) エンティティを使用してください。

`book 2 tickets to paris`

前の発話で、`paris` には、`Location` 階層構造エンティティの子 `Location::ToLocation` というラベルが付けられます。

エンドポイントから返されるデータには、エンティティ名と子の名前、発話から検出されたテキスト、検出されたテキストの場所、およびスコアが含まれます。

```JSON
"entities": [
  {
    "entity": "paris",
    "type": "Location::ToLocation",
    "startIndex": 18,
    "endIndex": 22,
    "score": 0.6866132
  }
]
```

|データ オブジェクト|親|子|値|
|--|--|--|--|
|階層構造エンティティ|Location|ToLocation|"paris"|

## <a name="composite-entity-data"></a>複合エンティティ データ
[複合](luis-concept-entity-types.md)エンティティは、機械学習され、単語またはフレーズを含めることができます。 たとえば、次の発話で、事前構築済みの `number` と `Location::ToLocation` の複合エンティティを考えてみましょう。

`book 2 tickets to paris`

number の `2` と ToLocation `paris` の間には、どのエンティティにも属さない単語があります。 [LUIS](luis-reference-regions.md) Web サイトの、ラベルの付いた発話で使用される緑色の下線が複合エンティティを示します。

![複合エンティティ](./media/luis-concept-data-extraction/composite-entity.png)

複合エンティティは、`compositeEntities` 配列で返され、その複合内のすべてのエンティティも、`entities` 配列で返されます。

```JSON
  "entities": [
    {
      "entity": "paris",
      "type": "Location::ToLocation",
      "startIndex": 18,
      "endIndex": 22,
      "score": 0.956998169
    },
    {
      "entity": "2",
      "type": "builtin.number",
      "startIndex": 5,
      "endIndex": 5,
      "resolution": {
        "value": "2"
      }
    },
    {
      "entity": "2 tickets to paris",
      "type": "Order",
      "startIndex": 5,
      "endIndex": 22,
      "score": 0.7714499
    }
  ],
  "compositeEntities": [
    {
      "parentType": "Order",
      "value": "2 tickets to paris",
      "children": [
        {
          "type": "builtin.number",
          "value": "2"
        },
        {
          "type": "Location::ToLocation",
          "value": "paris"
        }
      ]
    }
  ]
```    

|データ オブジェクト|エンティティ名|値|
|--|--|--|
|事前構築済みのエンティティ - number|"builtin.number"|"2"|
|階層構造エンティティ - Location|"Location::ToLocation"|"paris"|

## <a name="list-entity-data"></a>リスト エンティティ データ

[リスト](luis-concept-entity-types.md) エンティティでは、機械学習は行われません。 完全なテキスト一致です。 リストは、リスト内の項目と、その項目のシノニムを表します。 LUIS では、応答内のエンティティとして、任意のリスト内の項目に対してあらゆる一致がマークされます。 シノニムは、複数のリストに存在する可能性があります。

アプリに `Cities` いう名前のリストがあり、空港がある都市 (Sea-tac)、空港コード (SEA)、郵便番号 (98101)、および電話の市外局番 (206) など、都市名のバリエーションに対応しています。

|リスト項目|項目のシノニム|
|---|---|
|シアトル|sea-tac、sea、98101、206、+1 |
|パリ|cdg、roissy、ory、75001, 1, +33|

`book 2 tickets to paris`

前の発話では、単語 `paris` は、`Cities` リスト エンティティの一部として、パリ項目にマップされます。 このリスト エンティティは、項目のシノニムだけでなく、項目の正規化された名前と一致します。

```JSON
"entities": [
  {
    "entity": "paris",
    "type": "Cities",
    "startIndex": 18,
    "endIndex": 22,
    "resolution": {
      "values": [
        "Paris"
      ]
    }
  }
]
```

別の発話の例では、パリのシノニムを使用します。

`book 2 tickets to roissy`

```JSON
"entities": [
  {
    "entity": "roissy",
    "type": "Cities",
    "startIndex": 18,
    "endIndex": 23,
    "resolution": {
      "values": [
        "Paris"
      ]
    }
  }
]
```

## <a name="prebuilt-entity-data"></a>事前構築済みのエンティティ データ
[事前構築済み](luis-concept-entity-types.md)のエンティティは、オープン ソースの [Recognizers-Text](https://github.com/Microsoft/Recognizers-Text) プロジェクトを使用して、正規表現の一致に基づき検出されます。 事前構築済みのエンティティは、エンティティ配列で返され、`builtin::` というプレフィックスが付加された種類の名前が使用されます。 次のテキストは、返される事前構築済みのエンティティを含む発話の例です。

`Dec 5th send to +1 360-555-1212`

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

## <a name="regular-expression-entity-data"></a>正規表現エンティティ データ
[正規表現](luis-concept-entity-types.md)エンティティは、エンティティの作成時に指定する式を使用して、正規表現の一致に基づき検出されます。 正規表現エンティティの定義として `kb[0-9]{6}` を使用した場合、次の JSON 応答が、クエリ `When was kb123456 published?` に対して返される正規表現エンティティを含む発話の例です。

```JSON
{
  "query": "when was kb123456 published?",
  "topScoringIntent": {
    "intent": "FindKBArticle",
    "score": 0.933641255
  },
  "intents": [
    {
      "intent": "FindKBArticle",
      "score": 0.933641255
    },
    {
      "intent": "None",
      "score": 0.04397359
    }
  ],
  "entities": [
    {
      "entity": "kb123456",
      "type": "KB number",
      "startIndex": 9,
      "endIndex": 16
    }
  ]
}
```

## <a name="extracting-names"></a>名前の抽出
名前は、文字と単語のほぼすべての組み合わせが考えられるため、発話から名前を取得することは困難です。 抽出する名前の種類に応じて、複数のオプションがあります。 次の推奨事項はルールではなく、詳細なガイドラインです。

### <a name="add-prebuilt-personname-and-geographyv2-entities"></a>事前構築済みの PersonName エンティティと GeographyV2 エンティティを追加する

[PersonName](luis-reference-prebuilt-person.md) エンティティと [GeographyV2](luis-reference-prebuilt-geographyV2.md) エンティティは、いくつかの[言語カルチャ](luis-reference-prebuilt-entities.md)で利用できます。 

### <a name="names-of-people"></a>人の名前

人の名前は、言語およびカルチャに応じて、幾分ある種の形式を持つことがあります。 事前構築済みの **[personName](luis-reference-prebuilt-person.md)** エンティティ、または氏名の[ロール](luis-concept-roles.md)が含まれる**[簡易エンティティ](luis-concept-entity-types.md#simple-entity)** のどちらかを使用します。 

簡易エンティティを使用する場合は、必ず発話のさまざまな部分に姓と名を使用している例を提供してください。また、None 意図を含むあらゆる意図にわたるさまざまな長さの発話で、姓と名を使用している例を提供してください。 エンドポイントの発話を定期的に[確認](luis-how-to-review-endoint-utt.md)して、適切に予測されていないすべての名前にラベルを付けます。

### <a name="names-of-places"></a>場所の名前

場所の名前は、設定されており、市区町村、郡、州、都道府県、国などがあります。 位置情報を抽出するには、事前構築済みエンティティ **[geographyV2](luis-reference-prebuilt-geographyv2.md)** を使用します。

### <a name="new-and-emerging-names"></a>新しい名前

一部のアプリでは、製品や企業などの新しい名前を検索できる必要があります。 これらの種類の名前は、最も困難な種類のデータ抽出です。 **[簡易エンティティ](luis-concept-entity-types.md#simple-entity)** から始めて、[フレーズ リスト](luis-concept-feature.md)を追加します。 エンドポイントの発話を定期的に[確認](luis-how-to-review-endoint-utt.md)して、適切に予測されていないすべての名前にラベルを付けます。

## <a name="pattern-roles-data"></a>パターンの役割データ
役割は、エンティティのコンテキスト上の差異です。

```JSON
{
  "query": "move bob jones from seattle to redmond",
  "topScoringIntent": {
    "intent": "MoveAssetsOrPeople",
    "score": 0.9999998
  },
  "intents": [
    {
      "intent": "MoveAssetsOrPeople",
      "score": 0.9999998
    },
    {
      "intent": "None",
      "score": 1.02040713E-06
    },
    {
      "intent": "GetEmployeeBenefits",
      "score": 6.12244548E-07
    },
    {
      "intent": "GetEmployeeOrgChart",
      "score": 6.12244548E-07
    },
    {
      "intent": "FindForm",
      "score": 1.1E-09
    }
  ],
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
}
```

## <a name="patternany-entity-data"></a>Pattern.any エンティティ データ
Pattern.any エンティティは、[パターン](luis-concept-patterns.md)のテンプレート発話で使用される可変長のエンティティです。

```JSON
{
  "query": "where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?",
  "topScoringIntent": {
    "intent": "FindForm",
    "score": 0.999999464
  },
  "intents": [
    {
      "intent": "FindForm",
      "score": 0.999999464
    },
    {
      "intent": "GetEmployeeBenefits",
      "score": 4.883697E-06
    },
    {
      "intent": "None",
      "score": 1.02040713E-06
    },
    {
      "intent": "GetEmployeeOrgChart",
      "score": 9.278342E-07
    },
    {
      "intent": "MoveAssetsOrPeople",
      "score": 9.278342E-07
    }
  ],
  "entities": [
    {
      "entity": "understand your responsibilities as a member of the community",
      "type": "FormName",
      "startIndex": 18,
      "endIndex": 78,
      "role": ""
    }
  ]
}
```


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

## <a name="data-matching-multiple-entities"></a>複数のエンティティに一致するデータ

LUIS では、発話内で検出されたすべてのエンティティを返します。 その結果、チャットボットでは、結果に基づいて決定を行う必要があります。 発話内には多数のエンティティが存在する可能性があります。

`book me 2 adult business tickets to paris tomorrow on air france`

LUIS エンドポイントでは、異なるエンティティ内に同じデータが見つかる場合があります。

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

## <a name="data-matching-multiple-list-entities"></a>複数のリスト エンティティに一致するデータ

単語またはフレーズが複数のリスト エンティティに一致すると、エンドポイント クエリからそれぞれのリスト エンティティが返されます。

クエリ `when is the best time to go to red rock?` の場合、アプリの複数のリストに単語 `red` があると、LUIS ではすべてのエンティティを認識し、JSON エンドポイントの応答の一部としてエンティティの配列を返します。 

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

## <a name="next-steps"></a>次の手順

[エンティティの追加](luis-how-to-add-entities.md)に関するページで、LUIS アプリにエンティティを追加する方法の詳細を確認します。
