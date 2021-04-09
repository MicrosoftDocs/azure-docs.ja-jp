---
title: V3 API での予測エンドポイントの変更
description: クエリ予測エンドポイント V3 API が変更されています。 このガイドでは、バージョン 3 のエンドポイント API に移行する方法について説明します。
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 06/30/2020
ms.openlocfilehash: 59dfa439f6428f2db972a8f848887e1a74bc2622
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "98624305"
---
# <a name="prediction-endpoint-changes-for-v3"></a>V3 の予測エンドポイントの変更

クエリ予測エンドポイント V3 API が変更されています。 このガイドでは、バージョン 3 のエンドポイント API に移行する方法について説明します。

**一般公開状態** - この V3 API には、V2 API から大幅に変更された JSON の要求と応答が含まれています。

V3 API には、次の機能が用意されています。

* [外部エンティティ](schema-change-prediction-runtime.md#external-entities-passed-in-at-prediction-time)
* [動的リスト](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time)
* [事前構築済みエンティティの JSON の変更](#prebuilt-entity-changes)

上記の新機能をサポートするために、予測エンドポイントの[要求](#request-changes)と[応答](#response-changes)が大幅に変更されました。これには、次のものが含まれます。

* [応答オブジェクトの変更](#top-level-json-changes)
* [エンティティ名ではなくエンティティ ロール名の参照](#entity-role-name-instead-of-entity-name)
* [発話内のエンティティをマークするプロパティ](#marking-placement-of-entities-in-utterances)

V3 の[リファレンス ドキュメント](https://aka.ms/luis-api-v3)をご利用いただけます。

## <a name="v3-changes-from-preview-to-ga"></a>プレビューから GA への V3 の変更

V3 では、GA への移行の一環として次の変更が行われました。

* 次の作成済みのエンティティには、異なる JSON 応答があります。
    * [OrdinalV1](luis-reference-prebuilt-ordinal.md)
    * [GeographyV2](luis-reference-prebuilt-geographyv2.md)
    * [DatetimeV2](luis-reference-prebuilt-datetimev2.md)
    * `units` から `unit` への測定可能な単位のキー名

* 要求本文の JSON の変更:
    * `preferExternalEntities` から `preferExternalEntities` へ
    * 外部エンティティ用の省略可能な `score` パラメーター

* 応答本文の JSON の変更:
    * `normalizedQuery` の削除

## <a name="suggested-adoption-strategy"></a>提案される採用戦略

Bot Framework、Bing Spell Check V7 を使用する場合、または LUIS アプリの作成のみを移行する場合は、V2 エンドポイントを引き続き使用します。

影響を受けるクライアント アプリケーションまたは統合 (Bot Framework、Bing Spell Check V7) がなく、LUIS アプリの作成と予測エンドポイントの同時移行を問題なく実行できる場合は、V3 予測エンドポイントの使用を開始します。 V2 予測エンドポイントは引き続き使用可能であり、優れたフォールバック戦略です。


## <a name="not-supported"></a>サポートされていません

### <a name="bing-spell-check"></a>Bing Spell Check

この API は V3 予測エンドポイントではサポートされていません。スペル修正には引き続き V2 API 予測エンドポイントを使用してください。 V3 API を使用していてスペル修正が必要な場合は、LUIS API にテキストを送信する前に、クライアント アプリケーションで [Bing Spell Check](../bing-spell-check/overview.md) API を呼び出し、テキストを正しいスペルに変更します。

## <a name="bot-framework-and-azure-bot-service-client-applications"></a>Bot Framework と Azure Bot Service クライアント アプリケーション

Bot Framework の Version 4.7 がリリースされるまで、引き続き V2 API 予測エンドポイントを使用してください。


## <a name="endpoint-url-changes"></a>エンドポイント URL の変更

### <a name="changes-by-slot-name-and-version-name"></a>スロット名とバージョン名による変更

[V3 エンドポイントの HTTP 呼び出しの形式](developer-reference-resource.md#rest-endpoints)が変更されました。

バージョンによってクエリを実行する場合は、まず `"directVersionPublish":true` を使用して [API 経由で発行](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3b)する必要があります。 スロット名の代わりにバージョン ID を参照して、エンドポイントのクエリを実行します。

|`SLOT-NAME` の有効な値|
|--|
|`production`|
|`staging`|

## <a name="request-changes"></a>変更の要求

### <a name="query-string-changes"></a>クエリ文字列の変更

[!INCLUDE [V3 query params](./includes/v3-prediction-query-params.md)]

### <a name="v3-post-body"></a>V3 の POST 本文

```JSON
{
    "query":"your utterance here",
    "options":{
        "datetimeReference": "2019-05-05T12:00:00",
        "preferExternalEntities": true
    },
    "externalEntities":[],
    "dynamicLists":[]
}
```

|プロパティ|Type|Version|Default|目的|
|--|--|--|--|--|
|`dynamicLists`|array|V3 のみ|不要。|[動的リスト](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time)を使用すると、既に LUIS アプリに存在し、トレーニングおよび発行済みの既存のリスト エンティティを拡張することができます。|
|`externalEntities`|array|V3 のみ|不要。|[外部エンティティ](schema-change-prediction-runtime.md#external-entities-passed-in-at-prediction-time)を使用すると、LUIS アプリが実行時にエンティティを特定してラベル付けを行い、それを既存のエンティティの特徴として使用できるようになります。 |
|`options.datetimeReference`|string|V3 のみ|既定値なし|[datetimeV2 オフセット](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity)を決定するために使用されます。 datetimeReference の形式は [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) です。|
|`options.preferExternalEntities`|boolean|V3 のみ|false|予測にユーザーの[外部エンティティ (既存のエンティティと同じ名前)](schema-change-prediction-runtime.md#override-existing-model-predictions) を使用するか、それともモデル内の既存のエンティティを使用するかを指定します。 |
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
* 予測されたデータへのプログラムによるアクセスが容易になります。 V2 で配列全体を列挙していたのとは異なり、意図とエンティティの両方の値に **名前指定** によってアクセスできます。 エンティティ ロールの予測では、ロール名が返されます (アプリ全体で一意であるため)。
* データ型は (特定できる場合は) 維持されます。 数値が文字列として返されることがなくなります。
* 最も優先度の高い予測情報と追加のメタデータが区別され、`$instance` オブジェクトに返されます。

### <a name="entity-response-changes"></a>エンティティの応答の変更

#### <a name="marking-placement-of-entities-in-utterances"></a>発話内のエンティティの位置をマークする

**V2 では**、発話内のエンティティは `startIndex` と `endIndex` でマークされます。

**V3 では**、エンティティは `startIndex` と `entityLength` でマークされます。

#### <a name="access-instance-for-entity-metadata"></a>エンティティ メタデータのために `$instance` にアクセスする

エンティティ メタデータが必要な場合は、クエリ文字列で `verbose=true` フラグを使用する必要があります。メタデータは応答の `$instance` オブジェクトに含まれます。 例については、以降のセクションの JSON 応答で示されています。

#### <a name="each-predicted-entity-is-represented-as-an-array"></a>予測された各エンティティは配列として表される

各エンティティは発話内で複数回予測される可能性があるため、`prediction.entities.<entity-name>` オブジェクトには配列が含まれます。

<a name="prebuilt-entities-with-new-json"></a>

#### <a name="prebuilt-entity-changes"></a>事前構築済みのエンティティの変更

V3 の応答オブジェクトには、事前構築済みエンティティの変更も含まれています。 詳細については、[特定の事前構築済みのエンティティ](luis-reference-prebuilt-entities.md)に関する記事を確認してください。

#### <a name="list-entity-prediction-changes"></a>リスト エンティティ予測の変更

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

#### <a name="entity-role-name-instead-of-entity-name"></a>エンティティ名ではなくエンティティ ロール名

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

<a name="external-entities-passed-in-at-prediction-time"></a>
<a name="override-existing-model-predictions"></a>

## <a name="extend-the-app-at-prediction-time"></a>予測時にアプリを拡張する

予測ランタイムにアプリを拡張する方法についての[概念](schema-change-prediction-runtime.md)について説明します。


## <a name="next-steps"></a>次のステップ

V3 API のドキュメントを使用して、LUIS [エンドポイント](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0/operations/5cb0a9459a1fe8fa44c28dd8) API に対する既存の REST 呼び出しを更新します。
