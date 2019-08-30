---
title: Durable Functions における診断 - Azure
description: Azure Functions の Durable Functions 拡張機能に関する問題を診断する方法について説明します。
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 2cc60ee2c73aa6858f68d6b13a895a0188bb5735
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70098130"
---
# <a name="diagnostics-in-durable-functions-in-azure"></a>Azure での Durable Functions における診断

[Durable Functions](durable-functions-overview.md) に関する問題の診断には、いくつかの方法があります。 その中には、通常の関数の場合と同じ方法もあれば、Durable Functions に固有の方法もあります。

## <a name="application-insights"></a>Application Insights

Azure Functions の診断と監視には、[Application Insights](../../azure-monitor/app/app-insights-overview.md) を使用する方法が推奨されています。 Durable Functions にもそれが当てはまります。 Application Insights を関数アプリで活用する方法の概要については、「[Azure Functions を監視する](../functions-monitoring.md)」を参照してください。

また、Azure Functions Durable 拡張機能では "*追跡イベント*" が生成されます。このイベントを使って、オーケストレーションの実行をエンドツーエンドでトレースできます。 これらは、Azure Portal で [Application Insights 分析](../../azure-monitor/app/analytics.md)ツールを使って検出および照会できます。

### <a name="tracking-data"></a>データの追跡

追跡イベントは、オーケストレーション インスタンスの各ライフサイクル イベントによって Application Insights の **traces** コレクションに出力されます。 このイベントに格納されている **customDimensions** ペイロードには、いくつかのフィールドがあります。  フィールド名は、いずれも `prop__` で始まります。

* **hubName**: オーケストレーションが実行されているタスク ハブの名前。
* **appName**: Function App の名前。 これは、複数の関数アプリで同じ Application Insights インスタンスを共有しているときなどに使用できます。
* **slotName**: 現在の関数アプリが実行されている[デプロイ スロット](https://blogs.msdn.microsoft.com/appserviceteam/2017/06/13/deployment-slots-preview-for-azure-functions/)。 デプロイ スロットを利用してオーケストレーションのバージョン管理を行う際に使用できます。
* **functionName**: オーケストレーターまたはアクティビティ関数の名前。
* **functionType**: 関数の種類 (**オーケストレーター**や**アクティビティ**など)。
* **instanceId**: オーケストレーション インスタンスの一意の ID。
* **state**: インスタンスのライフサイクルの実行状態。 有効な値は、次のとおりです。
  * **Scheduled**: 関数は実行をスケジュールされたが、まだ実行を開始していません。
  * **Started**: 関数は実行を開始したが、まだ待機中でも完了済みでもありません。
  * **Awaited**: オーケストレーターが何らかの作業をスケジュールし、その完了を待っています。
  * **Listening**: オーケストレーターが外部のイベント通知をリッスンしています。
  * **Completed**: 関数が正常に完了しました。
  * **Failed**: 関数がエラーで失敗しました。
* **reason**: 追跡イベントに関連付けられている追加のデータ。 たとえば外部イベントの通知をインスタンスが待機している場合、待機しているイベントの名前がこのフィールドによって表されます。 関数が失敗した場合、エラーの詳細がここに格納されます。
* **isReplay**: 追跡イベントが再生された実行に対するものであるかどうかを示すブール値。
* **extensionVersion**: Durable Task 拡張機能のバージョン。 拡張機能のバグの可能性を報告するときにこのデータが特に重要となります。 長時間実行されるインスタンスでは、実行中に更新が生じた場合、複数のバージョンが報告されます。
* **sequenceNumber**: イベントの実行シーケンス番号。 タイムスタンプと組み合わせると、イベントを実行時刻順に並べ替えるのに役立ちます。 "*インスタンスの実行中にホストが再起動した場合、この番号はゼロにリセットされるので、常に、最初にタイムスタンプで並べ替えて、次に sequenceNumber で並べ替えることが重要です。* "

Application Insights に出力される追跡データの詳細レベルは、`host.json` ファイルの `logger` (Functions 1.x) または `logging` (Functions 2.x) セクションで構成できます。

#### <a name="functions-1x"></a>Functions 1.x

```json
{
    "logger": {
        "categoryFilter": {
            "categoryLevels": {
                "Host.Triggers.DurableTask": "Information"
            }
        }
    }
}
```

#### <a name="functions-2x"></a>Functions 2.x

```json
{
    "logging": {
        "logLevel": {
          "Host.Triggers.DurableTask": "Information",
        },
    }
}
```

既定では、すべての再生以外の追跡イベントが出力されます。 データのボリュームは、`Host.Triggers.DurableTask` を `"Warning"` または `"Error"` に設定し、追跡イベントの出力を例外的な状況に限定することで減らせます。

詳細なオーケストレーション再生イベントの生成を有効にするには、次のように `durableTask` の下で `host.json` ファイルの `LogReplayEvents` を `true` に設定します。

#### <a name="functions-1x"></a>Functions 1.x

```json
{
    "durableTask": {
        "logReplayEvents": true
    }
}
```

#### <a name="functions-2x"></a>Functions 2.x

```javascript
{
    "extensions": {
        "durableTask": {
            "logReplayEvents": true
        }
    }
}
```

> [!NOTE]
> 既定では、データの出力頻度が高くなりすぎないよう、Azure Functions ランタイムによって Application Insights テレメトリがサンプリングされます。 そのため、短時間に多数のライフサイクル イベントが発生すると追跡情報が失われることがあります。 この動作を構成する方法については、[Azure Functions の監視に関する記事](../functions-monitoring.md#configure-sampling)で説明しています。

### <a name="single-instance-query"></a>シングル インスタンス クエリ

次のクエリでは、[Hello シーケンス](durable-functions-sequence.md)関数オーケストレーションの単一インスタンスに関する履歴追跡データが表示されます。 これは、[Application Insights クエリ言語 (AIQL)](https://aka.ms/LogAnalyticsLanguageReference) で記述されています。 "*論理*" 実行パスだけを表示するために、再生実行は除外されています。 次のクエリで示すように、イベントは `timestamp` と `sequenceNumber` で並べ替えることができます。

```AIQL
let targetInstanceId = "ddd1aaa685034059b545eb004b15d4eb";
let start = datetime(2018-03-25T09:20:00);
traces
| where timestamp > start and timestamp < start + 30m
| where customDimensions.Category == "Host.Triggers.DurableTask"
| extend functionName = customDimensions["prop__functionName"]
| extend instanceId = customDimensions["prop__instanceId"]
| extend state = customDimensions["prop__state"]
| extend isReplay = tobool(tolower(customDimensions["prop__isReplay"]))
| extend sequenceNumber = tolong(customDimensions["prop__sequenceNumber"])
| where isReplay != true
| where instanceId == targetInstanceId
| sort by timestamp asc, sequenceNumber asc
| project timestamp, functionName, state, instanceId, sequenceNumber, appName = cloud_RoleName
```

結果では、実行時刻の昇順に並べ替えられたアクティビティ関数など、オーケストレーションの実行パスを示す追跡イベントが一覧表示されます。

![Application Insights クエリ](./media/durable-functions-diagnostics/app-insights-single-instance-ordered-query.png)

### <a name="instance-summary-query"></a>インスタンス サマリー クエリ

次のクエリでは、指定した時間範囲内で実行されたすべてのオーケストレーション インスタンスの状態が表示されます。

```AIQL
let start = datetime(2017-09-30T04:30:00);
traces
| where timestamp > start and timestamp < start + 1h
| where customDimensions.Category == "Host.Triggers.DurableTask"
| extend functionName = tostring(customDimensions["prop__functionName"])
| extend instanceId = tostring(customDimensions["prop__instanceId"])
| extend state = tostring(customDimensions["prop__state"])
| extend isReplay = tobool(tolower(customDimensions["prop__isReplay"]))
| extend output = tostring(customDimensions["prop__output"])
| where isReplay != true
| summarize arg_max(timestamp, *) by instanceId
| project timestamp, instanceId, functionName, state, output, appName = cloud_RoleName
| order by timestamp asc
```

結果では、インスタンス ID とその現在の実行状態が一覧表示されます。

![Application Insights クエリ](./media/durable-functions-diagnostics/app-insights-single-summary-query.png)

## <a name="logging"></a>ログの記録

オーケストレーター関数から直接ログを書き込む際には、常にオーケストレーターの再生動作を考慮することが大切です。 たとえば次のオーケストレーター関数について考えてみます。

### <a name="c"></a>C#

```cs
public static async Task Run(
    DurableOrchestrationContext context,
    ILogger log)
{
    log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (Functions 2.x のみ)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    context.log("Calling F1.");
    yield context.df.callActivity("F1");
    context.log("Calling F2.");
    yield context.df.callActivity("F2");
    context.log("Calling F3.");
    yield context.df.callActivity("F3");
    context.log("Done!");
});
```

最終的なログ データは、次のようになります。

```txt
Calling F1.
Calling F1.
Calling F2.
Calling F1.
Calling F2.
Calling F3.
Calling F1.
Calling F2.
Calling F3.
Done!
```

> [!NOTE]
> このログを見ると F1、F2、F3 が呼び出されていることになっていますが、"*実際には*"、これらの関数は最初に到達したときにしか呼び出されません。 それ以降、再生中に生じた呼び出しはスキップされ、オーケストレーター ロジックには出力結果が再生されます。

再生以外の実行だけをログに記録する場合は、`IsReplaying` が `false` である場合にのみログを記録する条件式を記述してください。 先ほどの例に再生の条件判定を加えます。

#### <a name="c"></a>C#

```cs
public static async Task Run(
    DurableOrchestrationContext context,
    ILogger log)
{
    if (!context.IsReplaying) log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    if (!context.IsReplaying) log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    if (!context.IsReplaying) log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (Functions 2.x のみ)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    if (!context.df.isReplaying) context.log("Calling F1.");
    yield context.df.callActivity("F1");
    if (!context.df.isReplaying) context.log("Calling F2.");
    yield context.df.callActivity("F2");
    if (!context.df.isReplaying) context.log("Calling F3.");
    yield context.df.callActivity("F3");
    context.log("Done!");
});
```

このように変更した場合、ログ出力は次のようになります。

```txt
Calling F1.
Calling F2.
Calling F3.
Done!
```

## <a name="custom-status"></a>カスタム状態

オーケストレーションのカスタム状態を使用すると、オーケストレーター関数のカスタム状態値を設定できます。 この状態は、HTTP status query API または `DurableOrchestrationClient.GetStatusAsync` API によって提供されます。 オーケストレーションのカスタム状態により、オーケストレーター関数のより充実した監視が可能になります。 たとえば、オーケストレーター関数コードに `DurableOrchestrationContext.SetCustomStatus` 呼び出しを含めて、実行時間の長い操作の進行状況を更新できます。 クライアント (Web ページや他の外部システムなど) は、HTTP status query API に定期的に照会して豊富な進行状況情報を取得できます。 `DurableOrchestrationContext.SetCustomStatus` を使用したサンプルを次に示します。

### <a name="c"></a>C#

```csharp
public static async Task SetStatusTest([OrchestrationTrigger] DurableOrchestrationContext context)
{
    // ...do work...

    // update the status of the orchestration with some arbitrary data
    var customStatus = new { completionPercentage = 90.0, status = "Updating database records" };
    context.SetCustomStatus(customStatus);

    // ...do more work...
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (Functions 2.x のみ)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    // ...do work...

    // update the status of the orchestration with some arbitrary data
    const customStatus = { completionPercentage: 90.0, status: "Updating database records", };
    context.df.setCustomStatus(customStatus);

    // ...do more work...
});
```

オーケストレーションの実行中に、外部クライアントはこのカスタム状態を取り込むことができます:

```http
GET /admin/extensions/DurableTaskExtension/instances/instance123

```

クライアントは次の応答を取得します:

```http
{
  "runtimeStatus": "Running",
  "input": null,
  "customStatus": { "completionPercentage": 90.0, "status": "Updating database records" },
  "output": null,
  "createdTime": "2017-10-06T18:30:24Z",
  "lastUpdatedTime": "2017-10-06T19:40:30Z"
}
```

> [!WARNING]
> カスタム状態ペイロードは、Azure Table Storage の列に収まる必要があるため、16 KB の UTF-16 JSON テキストに制限されています。 これより大きなペイロードが必要な場合は、外部ストレージを使用できます。

## <a name="debugging"></a>デバッグ

Azure Functions ではデバッグ関数コードが直接サポートされており、それと同じ機能が Durable Functions でも利用できます。Azure 内で実行するか、ローカルで実行するかは関係ありません。 ただしデバッグの際は、いくつかの動作に注意してください。

* **再生**: オーケストレーター関数は、新しい入力が受信されると定期的に再生されます。 つまり、オーケストレーター関数の実行が "*論理上*" は 1 回でも、複数回、同じブレークポイントに到達する可能性があります。特に、関数コードの最初の方にブレークポイントが設定されていると、その傾向が強くなります。
* **待機**: `await` に到達した場合は常に、Durable Task Framework ディスパッチャーに制御が戻されます。 特定の `await` に到達したのが初めてである場合、関連するタスクは "*決して*" 再開されません。 タスクが再開されない以上、await を "*ステップ オーバー*" (Visual Studio では F10) することは実質的に不可能です。 ステップ オーバーが機能するのは、タスクが再生されているときだけです。
* **メッセージングのタイムアウト**: Durable Functions は、オーケストレーター関数とアクティビティ関数の両方の実行を開始するために内部的にキュー メッセージを使用します。 マルチ VM 環境では、長時間デバッグにブレークインすると、別の VM によってメッセージが取り出され、二重実行となる可能性があります。 この動作は、キューによってトリガーされる通常の関数でも起こりますが、あえてこのコンテキストで指摘したのは、キューが実装の要となる動作であるためです。

> [!TIP]
> ブレークポイントを設定するとき、再生以外の実行でのみ停止させる必要がある場合は、`IsReplaying` が `false` の場合にのみ停止させる条件付きブレークポイントを設定できます。

## <a name="storage"></a>Storage

既定では、Durable Functions の状態が Azure Storage に格納されます。 つまり、オーケストレーションの状態は、[Microsoft Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer) などのツールを使用して調査することができます。

![Azure Storage Explorer のスクリーンショット](./media/durable-functions-diagnostics/storage-explorer.png)

オーケストレーションの状態を正確に把握できるので、これはデバッグで役に立ちます。 また、キュー内のメッセージを調べることで、保留中になっている (場合によってはスタックしている) 処理を把握することも可能です。

> [!WARNING]
> テーブル ストレージ内の実行履歴を確認できるのは便利ですが、このテーブルに依存することは避けてください。 Durable Functions 拡張機能の刷新に伴って変更される可能性があります。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [持続的タイマーの使用方法](durable-functions-timers.md)
