---
title: データの変更 - LUIS
description: Language Understanding (LUIS) での予測前にデータを変更する方法について説明します。
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/06/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 621a41f743b751a8c24bf6f6ad8497fb5c79775c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "95026012"
---
# <a name="alter-utterance-data-before-or-during-prediction"></a>予測前または予測中に発話データに変更を加える
LUIS では、予測前または予測中に発話を操作する方法が用意されています。 これらには、[スペルの修正](luis-tutorial-bing-spellcheck.md)や、事前構築済み [datetimeV2](luis-reference-prebuilt-datetimev2.md) でのタイム ゾーンの問題の修正が含まれます。

## <a name="correct-spelling-errors-in-utterance"></a>発話内のスペル ミスの修正


### <a name="v3-runtime"></a>V3 ランタイム

発話を LUIS に送信する前に、スペルを修正するためにテキストを前処理します。 正しい予測が得られるように、正しいスペルで発話の例を使用してください。

LUIS に送信する前に、[Bing Spell Check](../bing-spell-check/overview.md) を使用してテキストを修正してください。

### <a name="prior-to-v3-runtime"></a>V3 ランタイムより前

LUIS では、[Bing Spell Check API V7](../Bing-Spell-Check/overview.md) を使用して、発話内のスペル ミスを修正します。 LUIS には、このサービスに関連付けられているキーが必要です。 キーを作成し、[エンドポイント](https://go.microsoft.com/fwlink/?linkid=2092356)にクエリ文字列パラメーターとして追加します。

スペル修正が機能するためには、エンドポイントに 2 つのパラメーターが必要です。

|Param|値|
|--|--|
|`spellCheck`|boolean|
|`bing-spell-check-subscription-key`|[Bing Spell Check API V7](https://azure.microsoft.com/services/cognitive-services/spell-check/) エンドポイント キー|

[Bing Spell Check API V7](https://azure.microsoft.com/services/cognitive-services/spell-check/) でエラーが検出されると、元の発話と修正された発話が、予測と共にエンドポイントから返されます。

#### <a name="v2-prediction-endpoint-response"></a>[V2 予測エンドポイントの応答](#tab/V2)

```JSON
{
  "query": "Book a flite to London?",
  "alteredQuery": "Book a flight to London?",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.780123
  },
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-response"></a>[V3 予測エンドポイントの応答](#tab/V3)

```JSON
{
    "query": "Book a flite to London?",
    "prediction": {
        "normalizedQuery": "book a flight to london?",
        "topIntent": "BookFlight",
        "intents": {
            "BookFlight": {
                "score": 0.780123
            }
        },
        "entities": {},
    }
}
```

* * *

### <a name="list-of-allowed-words"></a>使用可能な単語の一覧
LUIS で使用される Bing スペル チェック API は、スペル チェックの変更中に無視する単語のリストをサポートしていません。 単語または頭字語のリストを許可する必要がある場合は、クライアント アプリケーションで発話を処理してから、意図予測のために発話を LUIS に送信します。

## <a name="change-time-zone-of-prebuilt-datetimev2-entity"></a>事前構築済み datetimeV2 エンティティのタイム ゾーンの変更
LUIS アプリで事前構築済み [datetimeV2](luis-reference-prebuilt-datetimev2.md) エンティティを使用している場合、予測の応答で datetime 値が返されることがあります。 要求のタイム ゾーンを使用して、返すべき正しい datetime が決定されます。 要求元がボットであったり、LUIS の前段階の集約化された他のアプリケーションであったりする場合は、LUIS で使用するタイム ゾーンを修正します。

### <a name="v3-prediction-api-to-alter-timezone"></a>タイムゾーンを変更する V3 予測 API

V3 では、`datetimeReference` によってタイムゾーン オフセットが決定されます。 [V3 予測](luis-migration-api-v3.md#v3-post-body)の詳細についてご確認ください。

### <a name="v2-prediction-api-to-alter-timezone"></a>タイムゾーンを変更する V2 予測 API
タイムゾーンは、API バージョンに基づく `timezoneOffset` パラメーターを使用して、ユーザーのタイムゾーンをエンドポイントに追加することで修正されます。 時刻を変更するには、パラメーターの値は正または負の数値 (分単位) である必要があります。

#### <a name="v2-prediction-daylight-savings-example"></a>V2 予測の夏時間の例
返される事前構築済み datetimeV2 を夏時間用に調整する必要がある場合は、[エンドポイント](https://go.microsoft.com/fwlink/?linkid=2092356) クエリで、正負の値 (分単位) を含むクエリ文字列パラメーターを使用する必要があります。

60 分を追加する場合は、次の手順を実行します。

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q=Turn the lights on?timezoneOffset=60&verbose={boolean}&spellCheck={boolean}&staging={boolean}&bing-spell-check-subscription-key={string}&log={boolean}`

60 分を削除する場合は、次の手順を実行します。

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q=Turn the lights on?timezoneOffset=-60&verbose={boolean}&spellCheck={boolean}&staging={boolean}&bing-spell-check-subscription-key={string}&log={boolean}`

#### <a name="v2-prediction-c-code-determines-correct-value-of-parameter"></a>V2 予測の C# コードによってパラメーターの正しい値を決定する

次の C# コードでは、[TimeZoneInfo](/dotnet/api/system.timezoneinfo) クラスの [FindSystemTimeZoneById](/dotnet/api/system.timezoneinfo.findsystemtimezonebyid#examples) メソッドを使用して、システム時刻に基づいて正しいオフセット値を決定します。

```csharp
// Get CST zone id
TimeZoneInfo targetZone = TimeZoneInfo.FindSystemTimeZoneById("Central Standard Time");

// Get local machine's value of Now
DateTime utcDatetime = DateTime.UtcNow;

// Get Central Standard Time value of Now
DateTime cstDatetime = TimeZoneInfo.ConvertTimeFromUtc(utcDatetime, targetZone);

// Find timezoneOffset/datetimeReference
int offset = (int)((cstDatetime - utcDatetime).TotalMinutes);
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [このチュートリアルを使用してスペル ミスを修正します。](luis-tutorial-bing-spellcheck.md)