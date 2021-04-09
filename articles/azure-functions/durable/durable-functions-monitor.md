---
title: Durable Functions のモニター - Azure
description: Azure Functions の Durable Functions 拡張機能を使って状態モニターを実装する方法を説明します。
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 8ef32ecfb6f69b71d29578d3b8314f568fd9386a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102431076"
---
# <a name="monitor-scenario-in-durable-functions---weather-watcher-sample"></a>Durable Functions のモニター シナリオ - 天気ウォッチャーのサンプル

モニター パターンは、ワークフローの柔軟な "*繰り返し*" プロセスを参照します。たとえば、特定の条件が満たされるまでポーリングします。 この記事では、[Durable Functions](durable-functions-overview.md) を使って監視を実装するサンプルを説明します。

## <a name="prerequisites"></a>前提条件

# <a name="c"></a>[C#](#tab/csharp)

* [クイックスタートの記事を完了していること](durable-functions-create-first-csharp.md)
* [GitHub からサンプル プロジェクトを複製またはダウンロードしていること](https://github.com/Azure/azure-functions-durable-extension/tree/main/samples/precompiled)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

* [クイックスタートの記事を完了していること](quickstart-js-vscode.md)
* [GitHub からサンプル プロジェクトを複製またはダウンロードしていること](https://github.com/Azure/azure-functions-durable-extension/tree/main/samples/javascript)

---

## <a name="scenario-overview"></a>シナリオの概要

このサンプルでは、ある場所の現在の気象条件を監視し、空が晴れたときに SMS でユーザーに通知します。 定期的にタイマーでトリガーされる関数を使って、天気を確認し、アラートを送信できます。 ただし、このアプローチの 1 つの問題は **有効期間管理** です。 送信する必要があるアラートが 1 つだけの場合は、晴天が検出された後でモニターを無効にする必要があります。 特にメリットがあるのは、監視パターンがそれ自体の実行を終了できることです。

* モニターは、スケジュールではなく一定間隔で実行されます。タイマーは "*実行*" を毎時トリガーし、モニターはアクション間で 1 時間 "*待機*" します。 モニターのアクションは、指定されない限り重複しません。これは実行時間の長いタスクで重要です。
* モニターの間隔は動的にすることができます。待機時間は、いくつかの条件に基づいて変化することがあります。
* モニターは、ある条件が満たされたときに終了することも、別のプロセスによって終了することもできます。
* モニターは、パラメーターを受け取ることができます。 サンプルでは、要求された場所と電話番号に同じ気象監視プロセスを適用する方法を示します。
* モニターはスケーラブルです。 各モニターはオーケストレーション インスタンスであるため、新しい関数を作成したり、コードをさらに定義したりしなくても、複数のモニターを作成できます。
* モニターは、より大規模なワークフローと簡単に統合できます。 モニターは、より複雑なオーケストレーション関数の 1 つのセクション、つまり[サブ オーケストレーション](durable-functions-sub-orchestrations.md)にすることができます。

## <a name="configuration"></a>構成

### <a name="configuring-twilio-integration"></a>Twilio 統合の構成

[!INCLUDE [functions-twilio-integration](../../../includes/functions-twilio-integration.md)]

### <a name="configuring-weather-underground-integration"></a>Weather Underground 統合の構成

このサンプルでは、Weather Underground API を使って、ある場所の現在の気象条件をチェックします。

まず必要なのは、Weather Underground アカウントです。 [https://www.wunderground.com/signup](https://www.wunderground.com/signup) で無料で作成できます。 アカウントを作成したら、API キーを取得する必要があります。 これは、[https://www.wunderground.com/weather/api](https://www.wunderground.com/weather/api/?MR=1) にアクセスし、キー設定を選択することで行います。 Stratus Developer プランは無料で、このサンプルを実行するのに十分です。

API キーを入手したら、次の **アプリ設定** を関数アプリに追加します。

| アプリ設定の名前 | 値の説明 |
| - | - |
| **WeatherUndergroundApiKey**  | Weather Underground API キー。 |

## <a name="the-functions"></a>関数

この記事では、サンプル アプリで使用されている次の関数について説明します。

* `E3_Monitor`:`E3_GetIsClear` を定期的に呼び出す[オーケストレーター関数](durable-functions-bindings.md#orchestration-trigger)です。 `E3_GetIsClear` が true を返した場合に `E3_SendGoodWeatherAlert` を呼び出します。
* `E3_GetIsClear`:ある場所の現在の気象条件を確認する[アクティビティ関数](durable-functions-bindings.md#activity-trigger)です。
* `E3_SendGoodWeatherAlert`:Twilio 経由で SMS メッセージを送信するアクティビティ関数です。

### <a name="e3_monitor-orchestrator-function"></a>E3_Monitor オーケストレーター関数

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/Monitor.cs?range=41-78,97-115)]

オーケストレーターには、監視する場所と、その場所が明確になった場合にメッセージを送信する電話番号が必要です。 このデータは、厳密に型指定された `MonitorRequest` オブジェクトとしてオーケストレーターに渡されます。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

**E3_Monitor** 関数は、オーケストレーター関数用の標準的な *function.json* を使います。

[!code-json[Main](~/samples-durable-functions/samples/javascript/E3_Monitor/function.json)]

関数を実装するコードを次に示します。

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_Monitor/index.js)]

---

このオーケストレーター関数は、次のアクションを行います。

1. 監視する "*場所*" と SMS 通知の送信先の "*電話番号*" から構成される **MonitorRequest** を取得します。
2. 監視の有効期限を決定します。 サンプルでは、簡略化のためにハード コーディングされた値を使います。
3. **E3_GetIsClear** を呼び出し、要求された場所が晴れているかどうかを判断します。
4. 天気が晴れの場合は、**E3_SendGoodWeatherAlert** を呼び出して、要求された電話番号に SMS 通知を送信します。
5. 持続的タイマーを作成して、次のポーリング間隔でオーケストレーションを再開します。 サンプルでは、簡略化のためにハード コーディングされた値を使います。
6. 現在の UTC 時間がモニターの有効期限を経過するか SMS アラートが送信されるまで、実行を続けます。

オーケストレーター関数を複数回呼び出すことによって、複数のオーケストレーター インスタンスを同時に実行できます。 監視する場所と SMS アラートを送信する電話番号を指定することができます。 最後に、オーケストレーター関数は、タイマーの待機中は実行されて "*いない*" ため、その間は課金されることはありません。
### <a name="e3_getisclear-activity-function"></a>E3_GetIsClear アクティビティ関数

他のサンプルと同様に、ヘルパー アクティビティ関数は、`activityTrigger` トリガー バインドを使う標準的な関数です。 **E3_GetIsClear** 関数は、Weather Underground API を使って現在の気象条件を取得し、晴れているかどうかを判断します。

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/Monitor.cs?range=80-85)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

*function.json* の定義は次のようになります。

[!code-json[Main](~/samples-durable-functions/samples/javascript/E3_GetIsClear/function.json)]

その実装を次に示します。

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_GetIsClear/index.js)]

---

### <a name="e3_sendgoodweatheralert-activity-function"></a>E3_SendGoodWeatherAlert アクティビティ関数

**E3_SendGoodWeatherAlert** 関数は、Twilio バインディングを使って、散歩に適した時間であることをエンド ユーザーに通知する SMS メッセージを送信します。

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/Monitor.cs?range=87-96,140-205)]

> [!NOTE]
> サンプル コードを実行するには、`Microsoft.Azure.WebJobs.Extensions.Twilio` NuGet パッケージをインストールする必要があります。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

その *function.json* は単純です。

[!code-json[Main](~/samples-durable-functions/samples/javascript/E3_SendGoodWeatherAlert/function.json)]

SMS メッセージを送信するコードを次に示します。

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_SendGoodWeatherAlert/index.js)]

---

## <a name="run-the-sample"></a>サンプルを実行する

サンプルに含まれる HTTP によってトリガーされる関数を使って、次の HTTP POST 要求を送信することによりオーケストレーションを開始できます。

```
POST https://{host}/orchestrators/E3_Monitor
Content-Length: 77
Content-Type: application/json

{ "location": { "city": "Redmond", "state": "WA" }, "phone": "+1425XXXXXXX" }
```

```
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={SystemKey}
RetryAfter: 10

{"id": "f6893f25acf64df2ab53a35c09d52635", "statusQueryGetUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "sendEventPostUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/raiseEvent/{eventName}?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "terminatePostUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason={text}&taskHub=SampleHubVS&connection=Storage&code={systemKey}"}
```

**E3_Monitor** インスタンスが起動し、要求された場所の現在の気象条件のクエリを実行します。 天気が晴れの場合はアラートを送信するアクティビティ関数を呼び出し、そうでない場合はタイマーを設定します。 タイマーの期限が切れると、オーケストレーションが再開されます。

オーケストレーションのアクティビティは、Azure Functions ポータルで関数ログを調べることで確認できます。

```
2018-03-01T01:14:41.649 Function started (Id=2d5fcadf-275b-4226-a174-f9f943c90cd1)
2018-03-01T01:14:42.741 Started orchestration with ID = '1608200bb2ce4b7face5fc3b8e674f2e'.
2018-03-01T01:14:42.780 Function completed (Success, Id=2d5fcadf-275b-4226-a174-f9f943c90cd1, Duration=1111ms)
2018-03-01T01:14:52.765 Function started (Id=b1b7eb4a-96d3-4f11-a0ff-893e08dd4cfb)
2018-03-01T01:14:52.890 Received monitor request. Location: Redmond, WA. Phone: +1425XXXXXXX.
2018-03-01T01:14:52.895 Instantiating monitor for Redmond, WA. Expires: 3/1/2018 7:14:52 AM.
2018-03-01T01:14:52.909 Checking current weather conditions for Redmond, WA at 3/1/2018 1:14:52 AM.
2018-03-01T01:14:52.954 Function completed (Success, Id=b1b7eb4a-96d3-4f11-a0ff-893e08dd4cfb, Duration=189ms)
2018-03-01T01:14:53.226 Function started (Id=80a4cb26-c4be-46ba-85c8-ea0c6d07d859)
2018-03-01T01:14:53.808 Function completed (Success, Id=80a4cb26-c4be-46ba-85c8-ea0c6d07d859, Duration=582ms)
2018-03-01T01:14:53.967 Function started (Id=561d0c78-ee6e-46cb-b6db-39ef639c9a2c)
2018-03-01T01:14:53.996 Next check for Redmond, WA at 3/1/2018 1:44:53 AM.
2018-03-01T01:14:54.030 Function completed (Success, Id=561d0c78-ee6e-46cb-b6db-39ef639c9a2c, Duration=62ms)
```

オーケストレーションは、タイムアウトになるか晴天が検出されると完了します。 別の関数内で `terminate` API を使用することも、上の 202 応答で参照されている **terminatePostUri** HTTP POST Webhook を呼び出すこともできます。 Webhook を使用するには、`{text}` を早期終了の理由に置き換えます。 HTTP POST URL は概ね次のようになります。

```
POST https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason=Because&taskHub=SampleHubVS&connection=Storage&code={systemKey}
```

## <a name="next-steps"></a>次のステップ

このサンプルでは、Durable Functions で[持続的タイマー](durable-functions-timers.md)と条件付きロジックを使って外部ソースの状態を監視する方法を示しました。 次のサンプルでは、外部イベントと[持続的タイマー](durable-functions-timers.md)を使用してユーザーの操作を処理する方法について説明します。

> [!div class="nextstepaction"]
> [人との対話が含まれるサンプルを実行する](durable-functions-phone-verification.md)