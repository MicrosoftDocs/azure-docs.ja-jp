---
title: Durable Functions のモニター - Azure
description: Azure Functions の Durable Functions 拡張機能を使って状態モニターを実装する方法を説明します。
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 9be062ec42f054832225c17a65b06e47dbcbe990
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2019
ms.locfileid: "59607281"
---
# <a name="monitor-scenario-in-durable-functions---weather-watcher-sample"></a>Durable Functions のモニター シナリオ - 天気ウォッチャーのサンプル

モニター パターンは、ワークフローの柔軟な "*再帰*" プロセスを参照します。たとえば、特定の条件が満たされるまでポーリングするなどです。 この記事では、[Durable Functions](durable-functions-overview.md) を使って監視を実装するサンプルを説明します。

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>シナリオの概要

このサンプルでは、ある場所の現在の気象条件を監視し、空が晴れたときに SMS でユーザーに通知します。 定期的にタイマーでトリガーされる関数を使って、天気を確認し、アラートを送信できます。 ただし、このアプローチの 1 つの問題は**有効期間管理**です。 送信する必要があるアラートが 1 つだけの場合は、晴天が検出された後でモニターを無効にする必要があります。 特にメリットがあるのは、監視パターンがそれ自体の実行を終了できることです。

* モニターは、スケジュールではなく一定間隔で実行されます。タイマーは "*実行*" を毎時トリガーし、モニターはアクション間で 1 時間 "*待機*" します。 モニターのアクションは、指定されない限り重複しません。これは実行時間の長いタスクで重要です。
* モニターの間隔は動的にすることができます。待機時間は、いくつかの条件に基づいて変化することがあります。
* モニターは、ある条件が満たされたときに終了することも、別のプロセスによって終了することもできます。
* モニターは、パラメーターを受け取ることができます。 サンプルでは、要求された場所と電話番号に同じ気象監視プロセスを適用する方法を示します。
* モニターはスケーラブルです。 各モニターはオーケストレーション インスタンスであるため、新しい関数を作成したり、コードをさらに定義したりしなくても、複数のモニターを作成できます。
* モニターは、より大規模なワークフローと簡単に統合できます。 モニターは、より複雑なオーケストレーション関数の 1 つのセクション、つまり[サブ オーケストレーション](durable-functions-sub-orchestrations.md)にすることができます。

## <a name="configuring-twilio-integration"></a>Twilio 統合の構成

[!INCLUDE [functions-twilio-integration](../../../includes/functions-twilio-integration.md)]

## <a name="configuring-weather-underground-integration"></a>Weather Underground 統合の構成

このサンプルでは、Weather Underground API を使って、ある場所の現在の気象条件をチェックします。

まず必要なのは、Weather Underground アカウントです。 [https://www.wunderground.com/signup](https://www.wunderground.com/signup) で無料で作成できます。 アカウントを作成したら、API キーを取得する必要があります。 これは、[https://www.wunderground.com/weather/api](https://www.wunderground.com/weather/api) にアクセスし、キー設定を選択することで行います。 Stratus Developer プランは無料で、このサンプルを実行するのに十分です。

API キーを入手したら、次の**アプリ設定**を関数アプリに追加します。

| アプリ設定の名前 | 値の説明 |
| - | - |
| **WeatherUndergroundApiKey**  | Weather Underground API キー。 |

## <a name="the-functions"></a>関数

この記事では、サンプル アプリで使用されている次の関数について説明します。

* `E3_Monitor`:`E3_GetIsClear` を定期的に呼び出すオーケストレーター関数。 `E3_GetIsClear` が true を返した場合に `E3_SendGoodWeatherAlert` を呼び出します。
* `E3_GetIsClear`:ある場所の現在の気象条件を確認するアクティビティ関数です。
* `E3_SendGoodWeatherAlert`:Twilio 経由で SMS メッセージを送信するアクティビティ関数です。

以下のセクションでは、C# スクリプトと JavaScript で使用される構成とコードについて説明します。 Visual Studio 開発用のコードは、この記事の最後に記載されています。

## <a name="the-weather-monitoring-orchestration-visual-studio-code-and-azure-portal-sample-code"></a>気象監視オーケストレーション (Visual Studio Code と Azure Portal のサンプル コード)

**E3_Monitor** 関数は、オーケストレーター関数用の標準的な *function.json* を使います。

[!code-json[Main](~/samples-durable-functions/samples/csx/E3_Monitor/function.json)]

関数を実装するコードを次に示します。

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E3_Monitor/run.csx)]

### <a name="javascript-functions-2x-only"></a>JavaScript (Functions 2.x のみ)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_Monitor/index.js)]

このオーケストレーター関数は、次のアクションを行います。

1. 監視する "*場所*" と SMS 通知の送信先の "*電話番号*" から構成される **MonitorRequest** を取得します。
2. 監視の有効期限を決定します。 サンプルでは、簡略化のためにハード コーディングされた値を使います。
3. **E3_GetIsClear** を呼び出し、要求された場所が晴れているかどうかを判断します。
4. 天気が晴れの場合は、**E3_SendGoodWeatherAlert** を呼び出して、要求された電話番号に SMS 通知を送信します。
5. 持続的タイマーを作成して、次のポーリング間隔でオーケストレーションを再開します。 サンプルでは、簡略化のためにハード コーディングされた値を使います。
6. [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) (C#) または `currentUtcDateTime` (JavaScript) がモニターの有効期限を経過するか SMS アラートが送信されるまで、実行を続けます。

複数の **MonitorRequests** を送信して、複数のオーケストレーター インスタンスを同時に実行できます。 監視する場所と SMS アラートを送信する電話番号を指定することができます。

## <a name="strongly-typed-data-transfer-net-only"></a>厳密に型指定されたデータ転送 (.NET のみ)

オーケストレーターにはデータの複数の部分が必要であるため、C# および C# スクリプトにおける厳密に型指定されたデータ転送には[共有 POCO オブジェクト](../functions-reference-csharp.md#reusing-csx-code)が使われます。[!code-csharp[Main](~/samples-durable-functions/samples/csx/shared/MonitorRequest.csx)]

[!code-csharp[Main](~/samples-durable-functions/samples/csx/shared/Location.csx)]

JavaScript サンプルでは通常の JSON オブジェクトをパラメーターとして使います。

## <a name="helper-activity-functions"></a>ヘルパー アクティビティ関数

他のサンプルと同様に、ヘルパー アクティビティ関数は、`activityTrigger` トリガー バインドを使う標準的な関数です。 **E3_GetIsClear** 関数は、Weather Underground API を使って現在の気象条件を取得し、晴れているかどうかを判断します。 *function.json* の定義は次のようになります。

[!code-json[Main](~/samples-durable-functions/samples/csx/E3_GetIsClear/function.json)]

その実装を次に示します。 データ転送に使われる POCO と同様に、API 呼び出しを処理し、応答 JSON を解析するロジックは、C# で共有クラスに抽象化されます。 これは [Visual Studio サンプル コード](#run-the-sample)の一部です。

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E3_GetIsClear/run.csx)]

### <a name="javascript-functions-2x-only"></a>JavaScript (Functions 2.x のみ)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_GetIsClear/index.js)]

**E3_SendGoodWeatherAlert** 関数は、Twilio バインディングを使って、散歩に適した時間であることをエンド ユーザーに通知する SMS メッセージを送信します。 その *function.json* は単純です。

[!code-json[Main](~/samples-durable-functions/samples/csx/E3_SendGoodWeatherAlert/function.json)]

SMS メッセージを送信するコードを次に示します。

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E3_SendGoodWeatherAlert/run.csx)]

### <a name="javascript-functions-2x-only"></a>JavaScript (Functions 2.x のみ)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_SendGoodWeatherAlert/index.js)]

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
Location: https://{host}/admin/extensions/DurableTaskExtension/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={SystemKey}
RetryAfter: 10

{"id": "f6893f25acf64df2ab53a35c09d52635", "statusQueryGetUri": "https://{host}/admin/extensions/DurableTaskExtension/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "sendEventPostUri": "https://{host}/admin/extensions/DurableTaskExtension/instances/f6893f25acf64df2ab53a35c09d52635/raiseEvent/{eventName}?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "terminatePostUri": "https://{host}/admin/extensions/DurableTaskExtension/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason={text}&taskHub=SampleHubVS&connection=Storage&code={systemKey}"}
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

オーケストレーションは、タイムアウトになるか晴天が検出されると[終了](durable-functions-instance-management.md)します。 別の関数内で `TerminateAsync` (.NET) または `terminate` (JavaScript) を使うか、上の 202 応答で参照されている **terminatePostUri** HTTP POST webhook を呼び出して `{text}` を終了の理由に置き換えることもできます。

```
POST https://{host}/admin/extensions/DurableTaskExtension/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason=Because&taskHub=SampleHubVS&connection=Storage&code={systemKey}
```

## <a name="visual-studio-sample-code"></a>Visual Studio のサンプル コード

Visual Studio プロジェクトの単一の C# ファイルとしてのオーケストレーションを次に示します。

> [!NOTE]
> 下のサンプル コードを実行するには、`Microsoft.Azure.WebJobs.Extensions.Twilio` Nuget パッケージをインストールする必要があります。

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/Monitor.cs)]

## <a name="next-steps"></a>次の手順

このサンプルでは、Durable Functions で[持続的タイマー](durable-functions-timers.md)と条件付きロジックを使って外部ソースの状態を監視する方法を示しました。 次のサンプルでは、外部イベントと[持続的タイマー](durable-functions-timers.md)を使用してユーザーの操作を処理する方法について説明します。

> [!div class="nextstepaction"]
> [人との対話が含まれるサンプルを実行する](durable-functions-phone-verification.md)
