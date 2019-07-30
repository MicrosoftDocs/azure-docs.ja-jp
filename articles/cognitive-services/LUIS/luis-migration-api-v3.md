---
title: V2 から V3 への API の移行
titleSuffix: Azure Cognitive Services
description: バージョン 3 のエンドポイント API が変更されました。 このガイドでは、バージョン 3 のエンドポイント API に移行する方法について説明します。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 06/24/2019
ms.author: diberry
ms.openlocfilehash: 4c08c95a05d4f22e2338a7264409aec0f64a4755
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442522"
---
# <a name="preview-migrate-to-api-version-3x-for-luis-apps"></a>更新:LUIS アプリの API バージョン 3.x への移行

クエリ予測エンドポイント API が変更されました。 このガイドでは、バージョン 3 のエンドポイント API に移行する方法について説明します。 

この V3 API では次の新機能が提供されます。これには、JSON 要求/応答の大幅な変更が含まれています。 

* [外部エンティティ](#external-entities-passed-in-at-prediction-time)
* [動的リスト](#dynamic-lists-passed-in-at-prediction-time)
* [事前構築済みエンティティの JSON の変更](#prebuilt-entities-with-new-json)

<!--
* [Multi-intent detection of utterance](#detect-multiple-intents-within-single-utterance)
-->

上記の新機能をサポートするために、クエリ予測エンドポイントの[要求](#request-changes)と[応答](#response-changes)が大幅に変更されました。これには、次のものが含まれます。

* [応答オブジェクトの変更](#top-level-json-changes)
* [エンティティ名ではなくエンティティ ロール名の参照](#entity-role-name-instead-of-entity-name)
* [発話内のエンティティをマークするプロパティ](#marking-placement-of-entities-in-utterances)

LUIS の次の機能は V3 API では**サポートされていません**。

* Bing Spell Check V7

V3 の[リファレンス ドキュメント](https://aka.ms/luis-api-v3)をご利用いただけます。

## <a name="endpoint-url-changes-by-slot-name"></a>スロット名によるエンドポイント URL の変更

V3 エンドポイントの HTTP 呼び出しの形式が変更されました。

|メソッド|URL|
|--|--|
|GET|https://<b>{REGION}</b>.api.cognitive.microsoft.com/luis/<b>v3.0-preview</b>/apps/<b>{APP-ID}</b>/slots/<b>{SLOT-NAME}</b>/predict?query=<b>{QUERY}</b>|
|POST|https://<b>{REGION}</b>.api.cognitive.microsoft.com/luis/<b>v3.0-preview</b>/apps/<b>{APP-ID}</b>/slots/<b>{SLOT-NAME}</b>/predict|
|||

## <a name="endpoint-url-changes-by-version-id"></a>バージョン ID によるエンドポイント URL の変更

バージョンによってクエリを実行する場合、まず `"directVersionPublish":true` を使用して [API 経由で発行](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3b)する必要があります。 スロット名の代わりにバージョン ID を参照して、エンドポイントのクエリを実行します。


|メソッド|URL|
|--|--|
|GET|https://<b>{REGION}</b>.api.cognitive.microsoft.com/luis/<b>v3.0-preview</b>/apps/<b>{APP-ID}</b>/versions/<b>{VERSION-ID}</b>/predict?query=<b>{QUERY}</b>|
|POST|https://<b>{REGION}</b>.api.cognitive.microsoft.com/luis/<b>v3.0-preview</b>/apps/<b>{APP-ID}</b>/versions/<b>{VERSION-ID}</b>/predict|
|||

## <a name="prebuilt-entities-with-new-json"></a>事前構築済みエンティティの新しい JSON

V3 の応答オブジェクトの変更には、[事前構築済みエンティティ](luis-reference-prebuilt-entities.md)も含まれています。 

## <a name="request-changes"></a>変更の要求 

### <a name="query-string-parameters"></a>クエリ文字列パラメーター

V3 API には異なるクエリ文字列パラメーターがあります。

|パラメーター名|Type|バージョン|既定値|目的|
|--|--|--|--|--|
|`log`|ブール値|V2 および V3|false|ログ ファイルにクエリを格納します。| 
|`query`|string|V3 のみ|既定値なし - GET 要求では必須|**V2 では**、予測される発話は `q` パラメーター内にあります。 <br><br>**V3 では**、この機能は `query` パラメーターで渡されます。|
|`show-all-intents`|Boolean|V3 のみ|false|すべての意図と対応するスコアを **prediction.intents** オブジェクトに返します。 意図は、親の `intents` オブジェクト内のオブジェクトとして返されます。 これにより、配列 `prediction.intents.give` 内で意図を探す必要はなく、プログラムによるアクセスが可能になります。 V2 では、これらは配列で返されていました。 |
|`verbose`|ブール値|V2 および V3|false|**V2 では**、true に設定した場合、予測されたすべての意図が返されていました。 予測されたすべての意図が必要な場合は、V3 の `show-all-intents` パラメーターを使用します。<br><br>**V3 では**、このパラメーターではエンティティ予測のエンティティ メタデータの詳細のみが提供されます。  |



<!--
|`multiple-segments`|boolean|V3 only|Break utterance into segments and predict each segment for intents and entities.|
-->


### <a name="the-query-prediction-json-body-for-the-post-request"></a>`POST` 要求のクエリ予測 JSON 本文

```JSON
{
    "query":"your utterance here",
    "options":{
        "datetimeReference": "2019-05-05T12:00:00",
        "overridePredictions": true
    },
    "externalEntities":[],
    "dynamicLists":[]
}
```

|プロパティ|Type|バージョン|既定値|目的|
|--|--|--|--|--|
|`dynamicLists`|array|V3 のみ|不要。|[動的リスト](#dynamic-lists-passed-in-at-prediction-time)を使用すると、既に LUIS アプリに存在し、トレーニングおよび発行済みの既存のリスト エンティティを拡張することができます。|
|`externalEntities`|array|V3 のみ|不要。|[外部エンティティ](#external-entities-passed-in-at-prediction-time)を使用すると、LUIS アプリが実行時にエンティティを特定してラベル付けを行い、それを既存のエンティティの特徴として使用できるようになります。 |
|`options.datetimeReference`|string|V3 のみ|既定値なし|[datetimeV2 オフセット](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity)を決定するために使用されます。|
|`options.overridePredictions`|ブール値|V3 のみ|false|予測にユーザーの[外部エンティティ (既存のエンティティと同じ名前)](#override-existing-model-predictions) を使用するか、それともモデル内の既存のエンティティを使用するかを指定します。 |
|`query`|string|V3 のみ|必須。|**V2 では**、予測される発話は `q` パラメーター内にあります。 <br><br>**V3 では**、この機能は `query` パラメーターで渡されます。|



## <a name="response-changes"></a>応答の変更

クエリ応答の JSON が変更されて、最も頻繁に使用されるデータへのプログラムによる大規模アクセスが可能になりました。 

### <a name="top-level-json-changes"></a>最上位レベルの JSON の変更

V2 の最上位の JSON プロパティは次のようになります。`verbose` が true に設定されている場合、すべての意図とそのスコアが `intents` プロパティに返されます。

```JSON
{
    "query":"this is your utterance you want predicted",
    "topScoringIntent":{},
    "intents":[],
    "entities":[],
    "compositeEntities":[]
}
```

V3 の最上位の JSON プロパティは次のようになります。

```JSON
{
    "query": "this is your utterance you want predicted",
    "prediction":{
        "normalizedQuery": "this is your utterance you want predicted - after normalization",
        "topIntent": "intent-name-1",
        "intents": {}, 
        "entities":{}
    }
}
```

`intents` オブジェクトは順不同のリストです。 `intents` の最初の子が `topIntent` になるとは限りません。 代わりに、`topIntent` の値を使ってスコアを探します。

```nodejs
const topIntentName = response.prediction.topIntent;
const score = intents[topIntentName];
```

応答の JSON スキーマの変更により、次のことが可能になります。

* 元の発話 `query` と返される予測 `prediction` との違いが明確になります。
* 予測されたデータへのプログラムによるアクセスが容易になります。 V2 で配列全体を列挙していたのとは異なり、意図とエンティティの両方の値に**名前指定**によってアクセスできます。 エンティティ ロールの予測では、ロール名が返されます (アプリ全体で一意であるため)。
* データ型は (特定できる場合は) 維持されます。 数値が文字列として返されることがなくなります。
* 最も優先度の高い予測情報と追加のメタデータが区別され、`$instance` オブジェクトに返されます。 

### <a name="access-instance-for-entity-metadata"></a>エンティティ メタデータのために `$instance` にアクセスする

エンティティ メタデータが必要な場合は、クエリ文字列で `verbose=true` フラグを使用する必要があります。メタデータは応答の `$instance` オブジェクトに含まれます。 例については、以降のセクションの JSON 応答で示されています。

### <a name="each-predicted-entity-is-represented-as-an-array"></a>予測された各エンティティは配列として表される

各エンティティは発話内で複数回予測される可能性があるため、`prediction.entities.<entity-name>` オブジェクトには配列が含まれます。 

### <a name="list-entity-prediction-changes"></a>リスト エンティティ予測の変更

リスト エンティティ予測の JSON は、配列の配列になるよう変更されました。

```JSON
"entities":{
    "my_list_entity":[
        ["canonical-form-1","canonical-form-2"],
        ["canonical-form-2"]
    ]
}
```
内部配列の 1 つ 1 つが、発話内のテキストに対応しています。 同じテキストがリスト エンティティの複数のサブリストに現れる可能性があるため、内部オブジェクトは配列になります。 

`entities` オブジェクトと `$instance` オブジェクト間をマッピングするとき、リスト エンティティ予測のためにオブジェクトの順序が保持されます。

```nodejs
const item = 0; // order preserved, use same enumeration for both
const predictedCanonicalForm = entities.my_list_entity[item];
const associatedMetadata = entities.$instance.my_list_entity[item];
```

### <a name="entity-role-name-instead-of-entity-name"></a>エンティティ名ではなくエンティティ ロール名 

V2 では、エンティティ名を一意の識別子として、予測されたすべてのエンティティが `entities` 配列で返されていました。 V3 では、エンティティでロールが使用されていて、エンティティ ロールを予測する場合、ロール名がプライマリ識別子になります。 これが可能なのは、エンティティ ロール名が、他のモデル (意図、エンティティ) の名前を含め、アプリ全体で一意でなければならないためです。

次の例では、テキスト `Yellow Bird Lane` を含む発話を検討します。 このテキストは、カスタムの `Location` エンティティの `Destination` ロールとして予測されます。

|発話テキスト|エンティティ名|ロール名|
|--|--|--|
|`Yellow Bird Lane`|`Location`|`Destination`|

V2 では、エンティティは "_エンティティ名_" によって識別され、ロールはオブジェクトのプロパティとして指定されます。

```JSON
"entities":[
    {
        "entity": "Yellow Bird Lane",
        "type": "Location",
        "startIndex": 13,
        "endIndex": 20,
        "score": 0.786378264,
        "role": "Destination"
    }
]
```

V3 では、ロールを予測する場合、エンティティは "_エンティティ ロール_" によって参照されます。

```JSON
"entities":{
    "Destination":[
        "Yellow Bird Lane"
    ]
}
```

V3 では、エンティティ メタデータを返す `verbose` フラグで同じ結果が得られます。

```JSON
"entities":{
    "Destination":[
        "Yellow Bird Lane"
    ],
    "$instance":{
        "Destination": [
            {
                "role": "Destination",
                "type": "Location",
                "text": "Yellow Bird Lane",
                "startIndex": 25,
                "length":16,
                "score": 0.9837309,
                "modelTypeId": 1,
                "modelType": "Entity Extractor"
            }
        ]
    }
}
```

## <a name="external-entities-passed-in-at-prediction-time"></a>予測時に渡される外部エンティティ

外部エンティティを使用すると、LUIS アプリが実行時にエンティティを特定してラベル付けを行い、それを既存のエンティティの特徴として使用できるようになります。 これにより、予測エンドポイントにクエリを送信する前に、個別のカスタム エンティティ エクストラクターを使用することができます。 これはクエリ予測エンドポイントで行われるため、モデルを再トレーニングして発行する必要がありません。

クライアント アプリケーションでは、エンティティの一致を管理し、一致したエンティティの発話内の場所を特定し、その情報を要求で送信することで、アプリケーション独自のエンティティ エクストラクターを提供しています。 

外部エンティティは、任意のエンティティの種類を拡張するためのメカニズムであり、同時に、ロールや複合などの他のモデルへのシグナルとしても使用されます。

これは、クエリ予測の実行時にのみ使用可能なデータを持つエンティティの場合に便利です。 この種類のデータの例としては、常に変化するデータや、ユーザーに固有のデータなどがあります。 ユーザーの連絡先リストから得た外部情報を使用して、LUIS の連絡先エンティティを拡張することができます。 

### <a name="entity-already-exists-in-app"></a>アプリに既に存在するエンティティ

エンドポイント要求 POST 本文で渡される外部エンティティの `entityName` の値は、要求時にはトレーニングおよび発行済みのアプリに既に存在していなければなりません。 エンティティの種類にかかわらず、すべての種類がサポートされています。

### <a name="first-turn-in-conversation"></a>会話の最初の発話

チャット ボットの会話の最初の発話で、ユーザーが次のような不完全な情報を入力した場合を考えてみましょう。

`Send Hazem a new message`

チャット ボットから LUIS への要求では、`Hazem` に関する情報を POST 本文で渡すことができるため、そのままユーザーの連絡先の 1 つとして照合されます。

```json
    "externalEntities": [
        {
            "entityName":"contacts",
            "startIndex": 5,
            "entityLength": 5,
            "resolution": {
                "employeeID": "05013",
                "preferredContactType": "TeamsChat"
            }
        }
    ]
```

予測の応答には、この外部エンティティと、予測されたその他すべてのエンティティが含まれます (要求で定義されているため)。  

### <a name="second-turn-in-conversation"></a>会話の 2 番目の発話

チャット ボットに対するユーザーの次の発話では、さらにあいまいな言葉を使います。

`Send him a calendar reminder for the party.`

前の発話では、`him` を `Hazem` への参照として使用しています。 会話のチャット ボットは、POST 本文で、`Hazem` を最初の発話から抽出されたエンティティの値 `him` にマップすることができます。

```json
    "externalEntities": [
        {
            "entityName":"contacts",
            "startIndex": 5,
            "entityLength": 3,
            "resolution": {
                "employeeID": "05013",
                "preferredContactType": "TeamsChat"
            }
        }
    ]
```

予測の応答には、この外部エンティティと、予測されたその他すべてのエンティティが含まれます (要求で定義されているため)。  

### <a name="override-existing-model-predictions"></a>既存のモデルの予測をオーバーライドする

`overridePredictions` オプションのプロパティは、同じ名前の予測されたエンティティと重複する外部エンティティをユーザーが送信する場合に、渡されたエンティティまたはモデル内に存在するエンティティを LUIS が選択することを指定します。 

たとえば、クエリ `today I'm free` を考えます。 LUIS は `today` を datetimeV2 として検出し、応答は次のとおりです。

```JSON
"datetimeV2": [
    {
        "type": "date",
        "values": [
            {
                "timex": "2019-06-21",
                "value": "2019-06-21"
            }
        ]
    }
]
```

ユーザーが外部エンティティを送信する場合:

```JSON
{
    "entityName": "datetimeV2",
    "startIndex": 0,
    "entityLength": 5,
    "resolution": {
        "date": "2019-06-21"
    }
}
```

`overridePredictions` が `false` に設定されている場合、LUIS は外部エンティティが送信されなかったかのように応答を返します。 

```JSON
"datetimeV2": [
    {
        "type": "date",
        "values": [
            {
                "timex": "2019-06-21",
                "value": "2019-06-21"
            }
        ]
    }
]
```

`overridePredictions` が `true` に設定されている場合、LUIS は次を含む応答を返します。

```JSON
"datetimeV2": [
    {
        "date": "2019-06-21"
    }
]
```



#### <a name="resolution"></a>解決策

"_省略可能な_" `resolution` プロパティが予測応答で返されることで、この外部エンティティに関連付けられているメタデータを渡し、それを応答で再び受け取ることができます。 

この主な目的は、事前構築済みエンティティを拡張することですが、そのエンティティの種類に限定されません。 

`resolution` プロパティには、数値、文字列、オブジェクト、配列のいずれも指定することができます。

* "Dallas"
* {"text": "value"}
* 12345 
* ["a", "b", "c"]



## <a name="dynamic-lists-passed-in-at-prediction-time"></a>予測時に渡される動的リスト

動的リストを使用すると、既に LUIS アプリに存在し、トレーニングおよび発行済みの既存のリスト エンティティを拡張することができます。 

リスト エンティティの値を定期的に変更する必要がある場合は、この機能を使用します。 この機能を使用することで、既にトレーニングおよび発行済みのリスト エンティティを次のように拡張することができます。

* クエリ予測エンドポイント要求時。
* 単一の要求に対して。

リスト エンティティは、LUIS アプリで空にすることができますが、必ず存在している必要があります。 LUIS アプリのリスト エンティティは変更されませんが、エンドポイントでの予測機能が拡張され、約 1,000 項目を含む最大 2 つのリストを含めることができるようになります。

### <a name="dynamic-list-json-request-body"></a>動的リストの JSON 要求本文

次の JSON 本文を送信して類義語を含む新しいサブリストをリストに追加し、`POST` クエリ予測要求を使用してテキスト `LUIS` のリスト エンティティを予測します。

```JSON
{
    "query": "Send Hazem a message to add an item to the meeting agenda about LUIS.",
    "options":{
        "timezoneOffset": "-8:00"
    },
    "dynamicLists": [
        {
            "listEntityName":"ProductList",
            "requestLists":[
                {
                    "name": "Azure Cognitive Services",
                    "canonicalForm": "Azure-Cognitive-Services",
                    "synonyms":[
                        "language understanding",
                        "luis",
                        "qna maker"
                    ]
                }
            ]
        }
    ]
}
```

予測の応答には、このリスト エンティティと、予測されたその他すべてのエンティティが含まれます (要求で定義されているため)。 

## <a name="timezoneoffset-renamed-to-datetimereference"></a>TimezoneOffset から datetimeReference への名称変更

**V2 では**、`timezoneOffset` [パラメーター](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity)は、要求が GET と POST のどちらの要求として送信されるかにかかわらず、予測の要求でクエリ文字列パラメーターとして送信されます。 

**V3 では**、POST 本文のパラメーター `datetimeReference` で同じ機能が提供されます。 

## <a name="marking-placement-of-entities-in-utterances"></a>発話内のエンティティの位置をマークする

**V2 では**、発話内のエンティティは `startIndex` と `endIndex` でマークされます。 

**V3 では**、エンティティは `startIndex` と `entityLength` でマークされます。

## <a name="deprecation"></a>非推奨 

V2 API は、V3 プレビューの後、少なくとも 9 か月間は非推奨になることはありません。 

## <a name="next-steps"></a>次の手順

V3 API のドキュメントを使用して、LUIS [エンドポイント](https://aka.ms/luis-api-v3) API に対する既存の REST 呼び出しを更新します。 