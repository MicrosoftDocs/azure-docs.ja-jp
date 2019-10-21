---
title: Durable Functions の概要 - Azure
description: Azure Functions の Durable Functions 拡張機能の概要です。
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: overview
ms.date: 08/07/2019
ms.author: cgillum
ms.reviewer: azfuncdf
ms.openlocfilehash: a917a823d47d6a072cf5a3ee5d636b432913df9a
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/26/2019
ms.locfileid: "71299438"
---
# <a name="what-are-durable-functions"></a>Durable Functions とは

*Durable Functions* は、サーバーレス コンピューティング環境でステートフル関数を記述できる [Azure Functions](../functions-overview.md) の拡張機能です。 この拡張機能では、Azure Functions プログラミング モデルを使用して、[*オーケストレーター関数*](durable-functions-orchestrations.md)を記述することでステートフル ワークフローを定義でき、[*エンティティ関数*](durable-functions-entities.md)を記述することでステートフル エンティティを定義できます。 拡張機能によって状態、チェックポイント、再起動がバックグラウンドで管理されるため、ユーザーはビジネス ロジックに専念できます。

## <a name="language-support"></a>サポートされている言語

Durable Functions では、現在次の言語をサポートしています。

* **C#** : [プリコンパイル済みクラス ライブラリ](../functions-dotnet-class-library.md)と [C# スクリプト](../functions-reference-csharp.md)の両方。
* **F#** : プリコンパイル済みクラス ライブラリと F# スクリプト。 F# スクリプトは、Azure Functions ランタイムのバージョン 1.x でのみサポートされています。
* **JavaScript**: Azure Functions ランタイムのバージョン 2.x でのみサポートされています。 Durable Functions 拡張機能のバージョン 1.7.0 以降が必要です。 

Durable Functions では、すべての [Azure Functions 言語](../supported-languages.md)をサポートすることを目標としています。 追加言語をサポートするための最新の作業状況については、[Durable Functions の問題の一覧](https://github.com/Azure/azure-functions-durable-extension/issues)を参照してください。

Azure Functions と同様に、[Visual Studio 2019](durable-functions-create-first-csharp.md)、[Visual Studio Code](quickstart-js-vscode.md)、および [Azure portal](durable-functions-create-portal.md) を使用して Durable Functions を開発するのに役立つテンプレートがあります。

## <a name="application-patterns"></a>アプリケーション パターン

Durable Functions の主なユース ケースは、サーバーレス アプリケーションにおける複雑でステートフルな調整の要件をシンプルにすることです。 以下のセクションでは、Durable Functions を使用することでメリットがある、典型的なアプリケーションのパターンを示します。

* [関数チェーン](#chaining)
* [ファンアウト/ファンイン](#fan-in-out)
* [非同期 HTTP API シリーズ](#async-http)
* [監視](#monitoring)
* [人による操作](#human)
* [アグリゲーター](#aggregator)

### <a name="chaining"></a>パターン #1: 関数チェーン

関数チェーン パターンでは、一連の関数が特定の順序で実行されます。 このパターンでは、ある関数の出力が、別の関数の入力に適用されます。

![関数チェーン パターンの図](./media/durable-functions-concepts/function-chaining.png)

次の例に示すように、Durable Functions を使用して、関数チェーン パターンを簡潔に実装できます。

#### <a name="c"></a>C#

```csharp
[FunctionName("Chaining")]
public static async Task<object> Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    try
    {
        var x = await context.CallActivityAsync<object>("F1", null);
        var y = await context.CallActivityAsync<object>("F2", x);
        var z = await context.CallActivityAsync<object>("F3", y);
        return  await context.CallActivityAsync<object>("F4", z);
    }
    catch (Exception)
    {
        // Error handling or compensation goes here.
    }
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (Functions 2.x のみ)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const x = yield context.df.callActivity("F1");
    const y = yield context.df.callActivity("F2", x);
    const z = yield context.df.callActivity("F3", y);
    return    yield context.df.callActivity("F4", z);
});
```

この例では、`F1`、`F2`、`F3`、および `F4` という値が、関数アプリ内の他の関数の名前です。 通常の命令型のコーディング構造を使用して、制御フローを実装できます。 コードは、上から下に実行されます。 コードに条件文やループなどの既存言語の制御フロー セマンティクスを含めることができます。 `try`/`catch`/`finally` ブロックに、エラー処理ロジックを含めることができます。

`context` パラメーターの [DurableOrchestrationContext] \(.NET\) と `context.df` オブジェクト (JavaScript) を使用して、名前によって他の関数を呼び出し、パラメーターを渡し、関数の出力を返すことができます。 コードで `await` (C#) または `yield` (JavaScript) が呼び出されるたびに Durable Functions フレームワークによって、現在の関数インスタンスの進行状況に対するチェックポイントが設定されます。 プロセスまたは VM が実行途中でリサイクルされる場合、関数インスタンスは直前の `await` または `yield` 呼び出しから再開されます。 詳細については、次のセクション (パターン #2: ファンアウト/ファンイン) を参照してください。

> [!NOTE]
> JavaScript の `context` オブジェクトは、[DurableOrchestrationContext] パラメーターだけではなく、[関数コンテキスト](../functions-reference-node.md#context-object)全体を表しています。

### <a name="fan-in-out"></a>パターン #2: ファンアウト/ファンイン

ファンアウト/ファンイン パターンでは、複数の関数を並列で実行し、すべての関数が完了するまで待機します。 複数の関数から返される結果に基づいて集計作業が行われることは、よくあることです。

![ファンアウト/ファンイン パターンの図](./media/durable-functions-concepts/fan-out-fan-in.png)

通常の関数では、複数のメッセージを 1 つのキューに送信することでファンアウトできます。 ファンインして戻ることはずっと難しくなります。 通常の関数でファンインするには、キューによってトリガーされた関数の終了を追跡した後、関数の出力を格納するコードを記述する必要があります。

Durable Functions 拡張機能では、比較的単純なコードでこのパターンを処理します。

#### <a name="c"></a>C#

```csharp
[FunctionName("FanOutFanIn")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    var parallelTasks = new List<Task<int>>();

    // Get a list of N work items to process in parallel.
    object[] workBatch = await context.CallActivityAsync<object[]>("F1", null);
    for (int i = 0; i < workBatch.Length; i++)
    {
        Task<int> task = context.CallActivityAsync<int>("F2", workBatch[i]);
        parallelTasks.Add(task);
    }

    await Task.WhenAll(parallelTasks);

    // Aggregate all N outputs and send the result to F3.
    int sum = parallelTasks.Sum(t => t.Result);
    await context.CallActivityAsync("F3", sum);
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (Functions 2.x のみ)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const parallelTasks = [];

    // Get a list of N work items to process in parallel.
    const workBatch = yield context.df.callActivity("F1");
    for (let i = 0; i < workBatch.length; i++) {
        parallelTasks.push(context.df.callActivity("F2", workBatch[i]));
    }

    yield context.df.Task.all(parallelTasks);

    // Aggregate all N outputs and send the result to F3.
    const sum = parallelTasks.reduce((prev, curr) => prev + curr, 0);
    yield context.df.callActivity("F3", sum);
});
```

ファンアウト作業は、`F2` 関数の複数のインスタンスに分散されます。 動的タスク リストを使用して、この作業が追跡されます。 .NET `Task.WhenAll` API または JavaScript `context.df.Task.all` API が呼び出され、呼び出された関数がすべて終了するまで待機します。 その後、`F2` 関数の出力が動的タスク リストから集計され、`F3` 関数に渡されます。

`Task.WhenAll` または `context.df.Task.all` の `await` 呼び出しまたは `yield` 呼び出しの際に設定される自動チェックポイントによって、実行途中でクラッシュや再起動が発生した場合でも、既に完了したすべてのタスクをやり直す必要がなくなります。

> [!NOTE]
> まれな状況ですが、アクティビティ関数の完了後、完了がオーケストレーション履歴に保存される前の時間帯にクラッシュが発生する可能性があります。 この場合、アクティビティ関数は、プロセス復旧後に最初から再実行されます。

### <a name="async-http"></a>パターン #3: 非同期 HTTP API

非同期 HTTP API パターンでは、外部クライアントとの間の実行時間の長い操作の状態を調整するという問題に対処します。 このパターンを実装する一般的な方法は、HTTP エンドポイントによって実行時間の長いアクションをトリガーすることです。 その後、ポーリングによって操作が完了したことを認識できる状態エンドポイントにクライアントをリダイレクトします。

![HTTP API パターンの図](./media/durable-functions-concepts/async-http-api.png)

Durable Functions には、このパターン向けの**組み込みサポート**が用意されており、長時間の関数の実行を操作するために記述する必要があるコードを簡略化できます。または、そのようなコードの記述が不要になります。 たとえば、Durable Functions のクイック スタート サンプル ([C#](durable-functions-create-first-csharp.md) と [JavaScript](quickstart-js-vscode.md)) に、新しいオーケストレーター関数のインスタンスを開始するために使用できる、シンプルな REST コマンドが示されています。 インスタンスが開始されると、この拡張機能によって、オーケストレーター関数の状態をクエリする Webhook HTTP API が公開されます。 

次の例は、オーケストレーターを開始し、その状態をクエリする REST コマンドを示しています。 わかりやすくするため、この例ではプロトコルの細部をいくらか省略しています。

```
> curl -X POST https://myfunc.azurewebsites.net/orchestrators/DoWork -H "Content-Length: 0" -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/b79baf67f717453ca9e86c5da21e03ec

{"id":"b79baf67f717453ca9e86c5da21e03ec", ...}

> curl https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/b79baf67f717453ca9e86c5da21e03ec

{"runtimeStatus":"Running","lastUpdatedTime":"2019-03-16T21:20:47Z", ...}

> curl https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 200 OK
Content-Length: 175
Content-Type: application/json

{"runtimeStatus":"Completed","lastUpdatedTime":"2019-03-16T21:20:57Z", ...}
```

状態は Durable Functions ランタイムによって自動的に管理されるため、自分で状態追跡メカニズムを実装する必要はありません。

Durable Functions 拡張機能には、長時間実行されるオーケストレーションを管理する組み込みの HTTP API が公開されています。 または、独自の関数トリガー (HTTP、キュー、Azure Event Hubs など) と[オーケストレーション クライアント バインド](durable-functions-bindings.md#orchestration-client)を使用して、このパターンを自分で実装できます。 たとえば、キュー メッセージを使用して終了をトリガーできます。 または、生成されたキーを認証で使用する組み込みの HTTP API の代わりに、Azure Active Directory の認証ポリシーによって保護される HTTP トリガーを使用できます。

詳細については、[HTTP 機能](durable-functions-http-features.md)の記事を参照してください。その記事では、Durable Functions 拡張機能を使用して、非同期の長時間プロセスを HTTP 経由で公開する方法について説明しています。

### <a name="monitoring"></a>パターン #4: 監視

監視パターンは、ワークフロー内の柔軟な繰り返しプロセスを指します。 一例は、特定の条件が満たされるまでポーリングすることです。 通常の[タイマー トリガー](../functions-bindings-timer.md)を使用して、定期的なクリーンアップ ジョブなどの基本的なシナリオに対処できますが、その間隔は静的であり、インスタンスの有効期間の管理は複雑になります。 Durable Functions を使用して、柔軟な繰り返し間隔の作成、タスクの有効期間の管理、単一のオーケストレーションからの複数の監視プロセスの作成を実行できます。

監視パターンの一例は、前述の非同期 HTTP API シナリオを逆にすることです。 外部クライアント用のエンドポイントを公開して実行時間の長い操作を監視する代わりに、長時間実行される監視で外部エンドポイントを使用して、状態が変化するまで待機します。

![監視パターンの図](./media/durable-functions-concepts/monitor.png)

数行のコードで、Durable Functions を使用して任意のエンドポイントを観察する複数のモニターを作成できます。 条件が満たされたときにこれらのモニターによって実行を終了するか、[DurableOrchestrationClient](durable-functions-instance-management.md) によってモニターを終了できます。 特定の条件に基づいてモニターの `wait` 間隔を変更できます (指数関数的バックオフなど)。 

次のコードでは、基本的なモニターが実装されます。

#### <a name="c"></a>C#

```csharp
[FunctionName("MonitorJobStatus")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    int jobId = context.GetInput<int>();
    int pollingInterval = GetPollingInterval();
    DateTime expiryTime = GetExpiryTime();

    while (context.CurrentUtcDateTime < expiryTime)
    {
        var jobStatus = await context.CallActivityAsync<string>("GetJobStatus", jobId);
        if (jobStatus == "Completed")
        {
            // Perform an action when a condition is met.
            await context.CallActivityAsync("SendAlert", machineId);
            break;
        }

        // Orchestration sleeps until this time.
        var nextCheck = context.CurrentUtcDateTime.AddSeconds(pollingInterval);
        await context.CreateTimer(nextCheck, CancellationToken.None);
    }

    // Perform more work here, or let the orchestration end.
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (Functions 2.x のみ)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const jobId = context.df.getInput();
    const pollingInternal = getPollingInterval();
    const expiryTime = getExpiryTime();

    while (moment.utc(context.df.currentUtcDateTime).isBefore(expiryTime)) {
        const jobStatus = yield context.df.callActivity("GetJobStatus", jobId);
        if (jobStatus === "Completed") {
            // Perform an action when a condition is met.
            yield context.df.callActivity("SendAlert", machineId);
            break;
        }

        // Orchestration sleeps until this time.
        const nextCheck = moment.utc(context.df.currentUtcDateTime).add(pollingInterval, 's');
        yield context.df.createTimer(nextCheck.toDate());
    }

    // Perform more work here, or let the orchestration end.
});
```

要求が受信されると、そのジョブ ID 用の新しいオーケストレーション インスタンスが作成されます。 インスタンスは、条件が満たされてループが終了するまで、状態をポーリングします。 ポーリング間隔は、永続タイマーによって制御されます。 その後、さらに作業を実行するか、オーケストレーションを終了できます。 `context.CurrentUtcDateTime` (.NET) または `context.df.currentUtcDateTime` (JavaScript) が `expiryTime` を超えると、モニターは終了します。

### <a name="human"></a>パターン #5: 人による操作

多くの自動化されたプロセスには、何らかの人による操作が含まれます。 自動化されたプロセスに人による操作が含まれる場合に問題になるのが、人は必ずしもクラウド サービスのように可用性と応答性が高くないということです。 自動化されたプロセスでは、タイムアウトと補正ロジックを使用して、この操作を許容する必要があります。

承認プロセスは、人による操作を含むビジネス プロセスの一例です。 特定の金額を超えた経費報告書は、上司の承認が必要な場合があります。 上司が (休暇中などで) その経費報告書を 72 時間以内に承認しない場合は、他の誰か (おそらくは上司の上司) から承認を得るためにエスカレーション プロセスを開始します。

![人による操作が含まれるパターンの図](./media/durable-functions-concepts/approval.png)

オーケストレーター関数を使用して、この例のパターンを実装できます。 オーケストレーターが[永続タイマー](durable-functions-timers.md)を使用して承認を要求します。 オーケストレーターは、タイムアウトが発生した場合はエスカレーションを行います。 オーケストレーターは、[外部イベント](durable-functions-external-events.md) (人による操作によって生成される通知など) が発生するまで待機します。

次の例では、人による操作パターンを示すための承認プロセスを作成しています。

#### <a name="c"></a>C#

```csharp
[FunctionName("ApprovalWorkflow")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    await context.CallActivityAsync("RequestApproval", null);
    using (var timeoutCts = new CancellationTokenSource())
    {
        DateTime dueTime = context.CurrentUtcDateTime.AddHours(72);
        Task durableTimeout = context.CreateTimer(dueTime, timeoutCts.Token);

        Task<bool> approvalEvent = context.WaitForExternalEvent<bool>("ApprovalEvent");
        if (approvalEvent == await Task.WhenAny(approvalEvent, durableTimeout))
        {
            timeoutCts.Cancel();
            await context.CallActivityAsync("ProcessApproval", approvalEvent.Result);
        }
        else
        {
            await context.CallActivityAsync("Escalate", null);
        }
    }
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (Functions 2.x のみ)

```javascript
const df = require("durable-functions");
const moment = require('moment');

module.exports = df.orchestrator(function*(context) {
    yield context.df.callActivity("RequestApproval");

    const dueTime = moment.utc(context.df.currentUtcDateTime).add(72, 'h');
    const durableTimeout = context.df.createTimer(dueTime.toDate());

    const approvalEvent = context.df.waitForExternalEvent("ApprovalEvent");
    if (approvalEvent === yield context.df.Task.any([approvalEvent, durableTimeout])) {
        durableTimeout.cancel();
        yield context.df.callActivity("ProcessApproval", approvalEvent.result);
    } else {
        yield context.df.callActivity("Escalate");
    }
});
```

永続タイマーを作成するために、`context.CreateTimer` (.NET) または `context.df.createTimer` (JavaScript) が呼び出されます。 通知は `context.WaitForExternalEvent` (.NET) または `context.df.waitForExternalEvent` (JavaScript) が受け取ります。 その後、エスカレーションする (タイムアウトが先に発生した場合) か承認を処理する (タイムアウト前に承認を得た場合) かを決定するために、`Task.WhenAny` (.NET) または `context.df.Task.any` (JavaScript) が呼び出されます。

外部クライアントは、[組み込みの HTTP API](durable-functions-http-api.md#raise-event) を使用するか、別の関数からの [DurableOrchestrationClient.RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_System_String_System_String_System_Object_) API を使用して、待機中のオーケストレーター関数にイベント通知を配信できます。

```csharp
[FunctionName("RaiseEventToOrchestration")]
public static async Task Run(
    [HttpTrigger] string instanceId,
    [OrchestrationClient] DurableOrchestrationClient client)
{
    bool isApproved = true;
    await client.RaiseEventAsync(instanceId, "ApprovalEvent", isApproved);
}
```

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const isApproved = true;
    await client.raiseEvent(instanceId, "ApprovalEvent", isApproved);
};
```

```bash
curl -d "true" http://localhost:7071/runtime/webhooks/durabletask/instances/{instanceId}/raiseEvent/ApprovalEvent -H "Content-Type: application/json"
```

### <a name="aggregator"></a>パターン #6:アグリゲーター (プレビュー)

6 番目のパターンは、ある期間のイベント データを 1 つのアドレス可能な*エンティティ* に集計することに関連しています。 このパターンでは、集計されるデータは、複数のソースから取得されるか、バッチで配信されるか、または長期間にわたって分散される可能性があります。 アグリゲーターがイベント データの到着時にイベント データに対してアクションを行ったり、外部クライアントが集計されたデータをクエリする必要が生じたりする場合があります。

![アグリゲーターの図](./media/durable-functions-concepts/aggregator.png)

このパターンを通常のステートレス関数で実装しようとする際に注意が必要な点は、同時実行制御が大きな課題となることです。 複数のスレッドが同時に同じデータを変更することに注意する必要があるだけでなく、アグリゲーターが一度に 1 つの VM 上でのみ実行されるようにすることも注意する必要があります。

[Durable Entity 関数](durable-functions-preview.md#entity-functions)を使用すれば、このパターンを 1 つの関数として簡単に実装できます。

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    int currentValue = ctx.GetState<int>();

    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            int amount = ctx.GetInput<int>();
            currentValue += amount;
            break;
        case "reset":
            currentValue = 0;
            break;
        case "get":
            ctx.Return(currentValue);
            break;
    }

    ctx.SetState(currentValue);
}
```

持続エンティティは、.NET クラスとしてモデル化することもできます。 このモデルは、操作の一覧が固定されていて、サイズが大きくなった場合に役立ちます。 次の例は、.NET クラスおよびメソッドを使用した `Counter` エンティティの同等の実装です。

```csharp
public class Counter
{
    [JsonProperty("value")]
    public int CurrentValue { get; set; }

    public void Add(int amount) => this.CurrentValue += amount;

    public void Reset() => this.CurrentValue = 0;

    public int Get() => this.CurrentValue;

    [FunctionName(nameof(Counter))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<Counter>();
}
```

クライアントは、[エンティティ クライアント バインディング](durable-functions-bindings.md#entity-client)を使用して、エンティティ関数の*操作*をエンキューすることができます ("シグナル通知" とも呼ばれる)。

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static async Task Run(
    [EventHubTrigger("device-sensor-events")] EventData eventData,
    [OrchestrationClient] IDurableOrchestrationClient entityClient)
{
    var metricType = (string)eventData.Properties["metric"];
    var delta = BitConverter.ToInt32(eventData.Body, eventData.Body.Offset);

    // The "Counter/{metricType}" entity is created on-demand.
    var entityId = new EntityId("Counter", metricType);
    await entityClient.SignalEntityAsync(entityId, "add", delta);
}
```

動的に生成されたプロキシは、タイプセーフな方法でシグナル通知エンティティにも使用できます。 シグナル通知に加えて、クライアントは、オーケストレーション クライアント バインディングで[タイプ セーフのメソッド](durable-functions-bindings.md#entity-client-usage)を使用して、エンティティ関数の状態をクエリすることもできます。

> [!NOTE]
> 現在、エンティティ関数は [Durable Functions 2.0 プレビュー](durable-functions-preview.md)の一部として .NET でのみ使用できます。

## <a name="the-technology"></a>テクノロジ

Durable Functions 拡張機能の背後には、コードでワークフローを構築するために使用される GitHub のオープン ソース ライブラリである [Durable Task Framework](https://github.com/Azure/durabletask) があり、この拡張機能はその上に構築されています。 Azure Functions が Azure WebJobs のサーバーレスな進化形であるのと同じように、Durable Functions は Durable Task Framework のサーバーレスな進化形です。 Microsoft や他の組織は、ミッション クリティカルなプロセスを自動化するために、Durable Task Framework を広範囲にわたって使用しています。 サーバーレスな Azure Functions 環境には自然に適合します。

## <a name="code-constraints"></a>コードの制約

信頼性の高い長時間の実行の保証を提供するために、オーケストレーター関数には、従う必要がある一連のコーディング規則があります。 詳細については、「[オーケストレーター関数コードの制約](durable-functions-code-constraints.md)」の記事を参照してください。

## <a name="billing"></a>課金

Durable Functions は Azure Functions と同じように課金されます。 詳細については、[Azure Functions の価格](https://azure.microsoft.com/pricing/details/functions/)に関するページを参照してください。 Azure Functions の[従量課金プラン](../functions-scale.md#consumption-plan)でオーケストレーター関数を実行する場合は、注意する必要がある課金動作がいくつかあります。 これらの動作の詳細については、「[Durable Functions の課金](durable-functions-billing.md)」の記事を参照してください。

## <a name="jump-right-in"></a>すぐに始める

次の言語固有のクイック スタート チュートリアルのいずれかを完了することで、10 分足らずで Durable Functions を使い始めることができます。

* [C# と Visual Studio 2019 を使用する場合](durable-functions-create-first-csharp.md)
* [Visual Studio Code と JavaScript を使用する場合](quickstart-js-vscode.md)

どちらのクイック スタートでも、"hello world" という永続関数をローカルで作成してテストします。 その後、関数コードを Azure に発行します。 作成した関数は、他の関数の呼び出しを調整し、連結します。

## <a name="learn-more"></a>詳細情報

次のビデオでは、Durable Functions の利点を取り上げています。

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Durable-Functions-in-Azure-Functions/player] 

Durable Functions と基になるテクノロジの詳細については、次のビデオをご覧ください (.NET に重点を置いていますが、考え方はサポートされている他の言語にも当てはまります)。

> [!VIDEO https://channel9.msdn.com/Events/dotnetConf/2018/S204/player]

Durable Functions は [Azure Functions](../functions-overview.md)の高度な拡張機能であるため、すべてのアプリケーションに適しているわけではありません。 他の Azure オーケストレーション テクノロジとの比較については、「[Azure Functions と Azure Logic Apps の比較](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-azure-functions-and-azure-logic-apps)」を参照してください。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Durable Functions の関数の種類と機能](durable-functions-types-features-overview.md)
