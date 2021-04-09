---
title: ランタイムのアプリの拡張 - LUIS
description: 既に公開されている予測エンドポイントを拡張して新しい情報を渡す方法について説明します。
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: 1d94e9f59062e4d730b8f3b71022442e81e6eeda
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "98953023"
---
# <a name="extend-app-at-prediction-runtime"></a>予測ランタイムにアプリを拡張する

アプリのスキーマ (モデルと機能) がトレーニングされ、予測エンドポイントに公開されます。 この公開済みのモデルは、予測ランタイムで使用されます。 予測を強化するために、ユーザーの発話と共に新しい情報を予測ランタイムに渡すことができます。

次の 2 つの予測ランタイム スキーマの変更があります。
* [外部エンティティ](#external-entities)
* [動的リスト](#dynamic-lists)

<a name="external-entities-passed-in-at-prediction-time"></a>

## <a name="external-entities"></a>外部エンティティ

外部エンティティを使用すると、LUIS アプリが実行時にエンティティを特定してラベル付けを行い、それを既存のエンティティの特徴として使用できるようになります。 これにより、予測エンドポイントにクエリを送信する前に、個別のカスタム エンティティ エクストラクターを使用することができます。 これはクエリ予測エンドポイントで行われるため、モデルを再トレーニングして発行する必要がありません。

クライアント アプリケーションでは、エンティティの一致を管理し、一致したエンティティの発話内の場所を特定し、その情報を要求で送信することで、アプリケーション独自のエンティティ エクストラクターを提供しています。

外部エンティティは、任意のエンティティの種類を拡張するためのメカニズムであり、同時に、他のモデルへのシグナルとしても使用されます。

これは、クエリ予測の実行時にのみ使用可能なデータを持つエンティティの場合に便利です。 この種類のデータの例としては、常に変化するデータや、ユーザーに固有のデータなどがあります。 ユーザーの連絡先リストから得た外部情報を使用して、LUIS の連絡先エンティティを拡張することができます。

外部エンティティは、V3 オーサリング API の一部です。 このバージョンへの[移行](luis-migration-api-v3.md)の詳細について説明します。

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

会話のこのターンの発話は、`Hazem` への参照として `him` を使用します。 会話のチャット ボットは、POST 本文で、`Hazem` を最初の発話から抽出されたエンティティの値 `him` にマップすることができます。

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

`preferExternalEntities` オプションのプロパティは、同じ名前の予測されたエンティティと重複する外部エンティティをユーザーが送信する場合に、渡されたエンティティまたはモデル内に存在するエンティティを LUIS が選択することを指定します。

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

`preferExternalEntities` が `false` に設定されている場合、LUIS は外部エンティティが送信されなかったかのように応答を返します。

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

`preferExternalEntities` が `true` に設定されている場合、LUIS は次を含む応答を返します。

```JSON
"datetimeV2": [
    {
        "date": "2019-06-21"
    }
]
```



#### <a name="resolution"></a>解像度

"_省略可能な_" `resolution` プロパティが予測応答で返されることで、この外部エンティティに関連付けられているメタデータを渡し、それを応答で再び受け取ることができます。

この主な目的は、事前構築済みエンティティを拡張することですが、そのエンティティの種類に限定されません。

`resolution` プロパティには、数値、文字列、オブジェクト、配列のいずれも指定することができます。

* "Dallas"
* {"text": "value"}
* 12345
* ["a", "b", "c"]

<a name="dynamic-lists-passed-in-at-prediction-time"></a>

## <a name="dynamic-lists"></a>動的リスト

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
            "listEntity*":"ProductList",
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

## <a name="next-steps"></a>次のステップ

* [予測スコア](luis-concept-prediction-score.md)
* [オーサリング API V3 の変更](luis-migration-api-v3.md)
