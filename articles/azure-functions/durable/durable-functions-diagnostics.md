---
title: Durable Functions における診断 - Azure
description: Azure Functions の Durable Functions 拡張機能に関する問題を診断する方法について説明します。
author: cgillum
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: azfuncdf
ms.openlocfilehash: 62cc5e1762a2a54b26cbebae5aa7cfbf64204ba5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100584627"
---
# <a name="diagnostics-in-durable-functions-in-azure"></a>Azure での Durable Functions における診断

[Durable Functions](durable-functions-overview.md) に関する問題の診断には、いくつかの方法があります。 その中には、通常の関数の場合と同じ方法もあれば、Durable Functions に固有の方法もあります。

## <a name="application-insights"></a>Application Insights

Azure Functions の診断と監視には、[Application Insights](../../azure-monitor/app/app-insights-overview.md) を使用する方法が推奨されています。 Durable Functions にもそれが当てはまります。 Application Insights を関数アプリで活用する方法の概要については、「[Azure Functions を監視する](../functions-monitoring.md)」を参照してください。

また、Azure Functions Durable 拡張機能では "*追跡イベント*" が生成されます。このイベントを使って、オーケストレーションの実行をエンドツーエンドでトレースできます。 これらの追跡イベントは、Azure portal で [Application Insights 分析](../../azure-monitor/logs/log-query-overview.md)ツールを使って検出および照会できます。

### <a name="tracking-data"></a>データの追跡

追跡イベントは、オーケストレーション インスタンスの各ライフサイクル イベントによって Application Insights の **traces** コレクションに出力されます。 このイベントに格納されている **customDimensions** ペイロードには、いくつかのフィールドがあります。  フィールド名は、いずれも `prop__` で始まります。

* **hubName**: オーケストレーションが実行されているタスク ハブの名前。
* **appName**: Function App の名前。 このフィールドは、複数の関数アプリで同じ Application Insights インスタンスを共有しているときなどに使用できます。
* **slotName**: 現在の関数アプリが実行されている [デプロイ スロット](../functions-deployment-slots.md)。 このフィールドは、デプロイ スロットを使用してご自分のオーケストレーションのバージョン管理を行うときに便利です。
* **functionName**: オーケストレーターまたはアクティビティ関数の名前。
* **functionType**: 関数の種類 (**オーケストレーター** や **アクティビティ** など)。
* **instanceId**: オーケストレーション インスタンスの一意の ID。
* **state**: インスタンスのライフサイクルの実行状態。 有効な値は、次のとおりです。
  * **Scheduled**: 関数は実行をスケジュールされたが、まだ実行を開始していません。
  * **Started**: 関数は実行を開始したが、まだ待機中でも完了済みでもありません。
  * **Awaited**: オーケストレーターが何らかの作業をスケジュールし、その完了を待っています。
  * **Listening**: オーケストレーターが外部のイベント通知をリッスンしています。
  * **Completed**: 関数が正常に完了しました。
  * **[失敗]** : 関数がエラーで失敗しました。
* **reason**: 追跡イベントに関連付けられている追加のデータ。 たとえば外部イベントの通知をインスタンスが待機している場合、待機しているイベントの名前がこのフィールドによって表されます。 関数が失敗した場合、このフィールドにはエラーの詳細が格納されます。
* **isReplay**: 追跡イベントが再生された実行に対するものであるかどうかを示すブール値。
* **extensionVersion**: Durable Task 拡張機能のバージョン。 このバージョン情報は、拡張機能にバグの可能性があることを報告するときに特に重要です。 長時間実行されるインスタンスでは、実行中に更新が生じた場合、複数のバージョンが報告されます。
* **sequenceNumber**: イベントの実行シーケンス番号。 タイムスタンプと組み合わせると、イベントを実行時刻順に並べ替えるのに役立ちます。 "*インスタンスの実行中にホストが再起動した場合、この番号はゼロにリセットされるので、常に、最初にタイムスタンプで並べ替えて、次に sequenceNumber で並べ替えることが重要です。* "

Application Insights に出力される追跡データの詳細レベルは、`host.json` ファイルの `logger` (Functions 1.x) または `logging` (Functions 2.0) セクションで構成できます。

#### <a name="functions-10"></a>Functions 1.0

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

#### <a name="functions-20"></a>Functions 2.0

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

#### <a name="functions-10"></a>Functions 1.0

```json
{
    "durableTask": {
        "logReplayEvents": true
    }
}
```

#### <a name="functions-20"></a>Functions 2.0

```json
{
    "extensions": {
        "durableTask": {
            "logReplayEvents": true
        }
    }
}
```

> [!NOTE]
> 既定では、データの出力頻度が高くなりすぎないよう、Azure Functions ランタイムによって Application Insights テレメトリがサンプリングされます。 そのため、短時間に多数のライフサイクル イベントが発生すると追跡情報が失われることがあります。 この動作を構成する方法については、[Azure Functions の監視に関する記事](../configure-monitoring.md#configure-sampling)で説明しています。

### <a name="single-instance-query"></a>シングル インスタンス クエリ

次のクエリでは、[Hello シーケンス](durable-functions-sequence.md)関数オーケストレーションの単一インスタンスに関する履歴追跡データが表示されます。 これは [Kusto クエリ言語](/azure/data-explorer/kusto/query/)を使用して記述されています。 "*論理*" 実行パスだけを表示するために、再生実行は除外されています。 次のクエリで示すように、イベントは `timestamp` と `sequenceNumber` で並べ替えることができます。

```kusto
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

![Application Insights シングル インスタンスの並べ替えクエリ](./media/durable-functions-diagnostics/app-insights-single-instance-ordered-query.png)

### <a name="instance-summary-query"></a>インスタンス サマリー クエリ

次のクエリでは、指定した時間範囲内で実行されたすべてのオーケストレーション インスタンスの状態が表示されます。

```kusto
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

![Application Insights シングル インスタンス クエリ](./media/durable-functions-diagnostics/app-insights-single-summary-query.png)

## <a name="durable-task-framework-logging"></a>Durable Task Framework のログ

Durable 拡張機能のログは、オーケストレーション ロジックの動作を理解するのに役立ちます。 ただし、これらのログには、フレームワークレベルのパフォーマンスと信頼性の問題をデバッグするのに十分な情報が必ず含まれているわけではありません。 Durable 拡張機能の **v 2.3.0** 以降では、基になる Durable Task Framework (DTFx) によって出力されたログもコレクションに使用できます。

DTFx によって出力されるログを見るときは、DTFx エンジンが、コア ディスパッチ エンジン (`DurableTask.Core`) と、サポートされている多くのストレージ プロバイダーの 1 つ (Durable Functions は既定で `DurableTask.AzureStorage` を使用します) の 2 つのコンポーネントで構成されていることを理解しておくことが重要です。

* **DurableTask.Core**: オーケストレーションの実行と詳細なスケジュール設定に関する情報が含まれています。
* **DurableTask.AzureStorage**: 内部オーケストレーションの状態を格納およびフェッチするために使用される内部キュー、BLOB、ストレージ テーブルなど、Azure Storage 成果物の操作に関連する情報が含まれます。

これらのログを有効にするには、関数アプリの **host.json** ファイルの `logging/logLevel` セクションを更新します。 次の例は、`DurableTask.Core` と `DurableTask.AzureStorage` の両方からの警告とエラーのログを有効にする方法を示しています。

```json
{
  "version": "2.0",
  "logging": {
    "logLevel": {
      "DurableTask.AzureStorage": "Warning",
      "DurableTask.Core": "Warning"
    }
  }
}
```

Application Insights を有効にしている場合、これらのログは `trace` コレクションに自動的に追加されます。 Kusto クエリを使用して他の `trace` ログを検索するのと同じ方法でこれらを検索できます。

> [!NOTE]
> 実稼働アプリケーションでは、`"Warning"` フィルターを使用して、`DurableTask.Core` ログと `DurableTask.AzureStorage` ログを有効にすることをお勧めします。 `"Information"` などの詳細度の高いフィルターは、パフォーマンスの問題をデバッグする場合に非常に便利です。 ただし、これらのログ イベントは大量であるため、Application Insights のデータ ストレージ コストが大幅に増加する可能性があります。

次の Kusto クエリは DTFx ログのクエリを実行する方法を示しています。 クエリの最も重要な部分は `where customerDimensions.Category startswith "DurableTask"` です。これは、結果を `DurableTask.Core` および `DurableTask.AzureStorage` カテゴリのログにフィルター処理するためです。

```kusto
traces
| where customDimensions.Category startswith "DurableTask"
| project
    timestamp,
    severityLevel,
    Category = customDimensions.Category,
    EventId = customDimensions.EventId,
    message,
    customDimensions
| order by timestamp asc 
```
結果は、Durable Task Framework ログ プロバイダーによって書き込まれたログのセットになります。

![Application Insights DTFx クエリ結果](./media/durable-functions-diagnostics/app-insights-dtfx.png)

使用できるログ イベントの詳細については、[GitHub の Durable Task Framework 構造化ログに関するドキュメント](https://github.com/Azure/durabletask/tree/master/src/DurableTask.Core/Logging#durabletaskcore-logging)を参照してください。

## <a name="app-logging"></a>アプリのログ

オーケストレーター関数から直接ログを書き込む際には、常にオーケストレーターの再生動作を考慮することが大切です。 たとえば次のオーケストレーター関数について考えてみます。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("FunctionChain")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context,
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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

# <a name="python"></a>[Python](#tab/python)
```python
import logging
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    logging.info("Calling F1.")
    yield context.call_activity("F1")
    logging.info("Calling F2.")
    yield context.call_activity("F2")
    logging.info("Calling F3.")
    yield context.call_activity("F3")
    return None

main = df.Orchestrator.create(orchestrator_function)
```

---

最終的なログ データは、次の出力例のようになります。

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

再生以外の実行だけをログに記録する場合は、"is replaying" フラグが `false` である場合にのみログを記録する条件式を記述できます。 先ほどの例に再生の条件判定を加えます。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("FunctionChain")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context,
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

Durable Functions 2.0 以降、.NET のオーケストレーター関数にも、再生中にログ ステートメントを自動的にフィルターで除外する `ILogger` を作成するオプションがあります。 この自動フィルター処理は、[IDurableOrchestrationContext.CreateReplaySafeLogger(ILogger)](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.durablecontextextensions.createreplaysafelogger) API を使用して行われます。

```csharp
[FunctionName("FunctionChain")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context,
    ILogger log)
{
    log = context.CreateReplaySafeLogger(log);
    log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

> [!NOTE]
> 前述の C# の例は Durable Functions 2.x 用です。 Durable Functions 1.x の場合、`IDurableOrchestrationContext` の代わりに `DurableOrchestrationContext` を使用する必要があります。 バージョン間の相違点の詳細については、[Durable Functions のバージョン](durable-functions-versions.md)に関する記事を参照してください。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

# <a name="python"></a>[Python](#tab/python)

```python
import logging
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    if not context.is_replaying:
        logging.info("Calling F1.")
    yield context.call_activity("F1")
    if not context.is_replaying:
        logging.info("Calling F2.")
    yield context.call_activity("F2")
    if not context.is_replaying:
        logging.info("Calling F3.")
    yield context.call_activity("F3")
    return None

main = df.Orchestrator.create(orchestrator_function)
```

---

前述の変更により、ログ出力は次のようになります。

```txt
Calling F1.
Calling F2.
Calling F3.
Done!
```

## <a name="custom-status"></a>カスタム状態

カスタムオーケストレーションの状態を使用すると、オーケストレーター関数のカスタム状態値を設定できます。 このカスタム ステータスは、[HTTP 状態クエリ API](durable-functions-http-api.md#get-instance-status) または言語固有の API 呼び出しを介して外部クライアントから参照できます。 オーケストレーションのカスタム状態により、オーケストレーター関数のより充実した監視が可能になります。 たとえば、オーケストレーター関数コードで "set custom status" API を呼び出して、実行時間の長い操作の進行状況を更新できます。 クライアント (Web ページや他の外部システムなど) は、HTTP status query API に定期的に照会して豊富な進行状況情報を取得できます。 オーケストレーター関数でカスタム ステータス値を設定するためのサンプル コードを次に示します。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("SetStatusTest")]
public static async Task SetStatusTest([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    // ...do work...

    // update the status of the orchestration with some arbitrary data
    var customStatus = new { completionPercentage = 90.0, status = "Updating database records" };
    context.SetCustomStatus(customStatus);

    // ...do more work...
}
```

> [!NOTE]
> 前記の C# の例は Durable Functions 2.x 用です。 Durable Functions 1.x の場合、`IDurableOrchestrationContext` の代わりに `DurableOrchestrationContext` を使用する必要があります。 バージョン間の相違点の詳細については、[Durable Functions のバージョン](durable-functions-versions.md)に関する記事を参照してください。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

# <a name="python"></a>[Python](#tab/python)

```python
import logging
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    # ...do work...

    # update the status of the orchestration with some arbitrary data
    custom_status = {'completionPercentage': 90.0, 'status': 'Updating database records'}
    context.set_custom_status(custom_status)
    # ...do more work...

    return None

main = df.Orchestrator.create(orchestrator_function)
```

---

オーケストレーションの実行中に、外部クライアントはこのカスタム状態を取り込むことができます:

```http
GET /runtime/webhooks/durabletask/instances/instance123?code=XYZ

```

クライアントは次の応答を取得します:

```json
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

* **再生**: オーケストレーター関数は、新しい入力を受信すると、定期的に [再生](durable-functions-orchestrations.md#reliability)されます。 この動作は、オーケストレーター関数の実行が *論理上* は 1 回でも、複数回、同じブレークポイントに到達する可能性があることを意味します。特に、関数コードの最初の方に設定されている場合、その可能性が強くなります。
* **待機**: オーケストレーター関数内で `await` に到達した場合は常に、Durable Task Framework ディスパッチャーに制御が戻されます。 特定の `await` に到達したのが初めてである場合、関連するタスクが再開されることは *ありません*。 タスクが再開されない以上、await をステップ *オーバー* (Visual Studio では F10) することは不可能です。 ステップ オーバーが機能するのは、タスクが再生されているときだけです。
* **メッセージングのタイムアウト**: Durable Functions は、オーケストレーター関数、アクティビティ関数、およびエンティティ関数の実行を開始するために、内部的にキュー メッセージを使用します。 マルチ VM 環境では、長時間デバッグにブレークインすると、別の VM によってメッセージが取り出され、二重実行となる可能性があります。 この動作は、キューによってトリガーされる通常の関数でも起こりますが、あえてこのコンテキストで指摘したのは、キューが実装の要となる動作であるためです。
* **停止と開始**: Durable Functions のメッセージは、デバッグ セッション間で保持されます。 持続的関数の実行中にデバッグを停止し、ローカル ホスト プロセスを終了すると、その関数は今後のデバッグ セッションで自動的に再実行される可能性があります。 この動作は、予期していない場合、混乱を招くことがあります。 この動作を回避する 1 つの方法は、デバッグ セッション間に[内部ストレージ キュー](durable-functions-perf-and-scale.md#internal-queue-triggers)のすべてのメッセージを消去することです。

> [!TIP]
> オーケストレーター関数にブレークポイントを設定するときに、再生以外の実行でのみ停止させる必要がある場合は、"is replaying" 値が `false` の場合にのみ停止させる条件付きブレークポイントを設定できます。

## <a name="storage"></a>ストレージ

既定では、Durable Functions の状態が Azure Storage に格納されます。 この動作は、[Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) などのツールを使用してご自分のオーケストレーションの状態を調査することができることを意味します。

![Azure Storage Explorer のスクリーンショット](./media/durable-functions-diagnostics/storage-explorer.png)

オーケストレーションの状態を正確に把握できるので、これはデバッグで役に立ちます。 また、キュー内のメッセージを調べることで、保留中になっている (場合によってはスタックしている) 処理を把握することも可能です。

> [!WARNING]
> テーブル ストレージ内の実行履歴を確認できるのは便利ですが、このテーブルに依存することは避けてください。 Durable Functions 拡張機能の刷新に伴って変更される可能性があります。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure Functions での監視の詳細を学習する](../functions-monitoring.md)
