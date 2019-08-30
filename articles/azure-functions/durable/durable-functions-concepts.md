---
title: Azure Functions の Durable Functions のパターンと技術概念
description: Azure Functions の Durable Functions 拡張機能を使用したクラウドでのステートフル コードの実行によってメリットを得る方法について説明します。
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: azfuncdf
ms.openlocfilehash: 828bcaa8c93454ba845c30c03c76144310891123
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70098253"
---
# <a name="durable-functions-patterns-and-technical-concepts-azure-functions"></a>Durable Functions のパターンと技術概念 (Azure Functions)

Durable Functions は [Azure Functions](../functions-overview.md) と [Azure WebJobs](../../app-service/web-sites-create-web-jobs.md) の拡張機能です。 Durable Functions を使用して、サーバーレス環境でステートフル関数を記述できます。 この拡張機能は状態、チェックポイント、再起動を管理します。 

この記事では、Azure Functions 用の Durable Functions 拡張機能の動作と一般的な実装パターンについての詳細な情報を示します。 この情報は、開発上の課題を解決するために Durable Functions を使用する方法を決定するのに役立ちます。

> [!NOTE]
> Durable Functions は Azure Functions の高度な拡張機能であり、すべてのアプリケーションに適しているわけではありません。 この記事は、開発者が [Azure Functions](../functions-overview.md) のコンセプトを十分に理解しており、サーバーレス アプリケーションの開発に関わる課題があることを前提としています。

## <a name="patterns"></a>パターン

このセクションでは、Durable Functions が有用である、いくつかの典型的なアプリケーションのパターンについて説明します。

### <a name="chaining"></a>パターン #1: 関数チェーン

関数チェーン パターンでは、一連の関数が特定の順序で実行されます。 このパターンでは、ある関数の出力が、別の関数の入力に適用されます。

![関数チェーン パターンの図](./media/durable-functions-concepts/function-chaining.png)

次の例に示すように、Durable Functions を使用して、関数チェーン パターンを簡潔に実装できます。

#### <a name="c-script"></a>C# スクリプト

```csharp
public static async Task<object> Run(DurableOrchestrationContext context)
{
    try
    {
        var x = await context.CallActivityAsync<object>("F1");
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

> [!NOTE]
> C# でのプリコンパイル済みの永続関数の記述と C# スクリプトでのプリコンパイル済みの永続関数の記述との間には、この例には示されていないわずかな違いがあります。 C# のプリコンパイル済みの関数では、対応する属性で永続パラメーターを修飾する必要があります。 一例として、`DurableOrchestrationContext` パラメーターの `[OrchestrationTrigger]` 属性があります。 C# のプリコンパイル済みの関数では、パラメーターが適切に修飾されていない場合、実行時に関数に変数を挿入できないことでエラーが発生します。 例については、[GitHub の azure-functions-durable-extension のサンプル](https://github.com/Azure/azure-functions-durable-extension/blob/master/samples)を参照してください。

#### <a name="javascript-functions-2x-only"></a>JavaScript (Functions 2.x のみ)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const x = yield context.df.callActivity("F1");
    const y = yield context.df.callActivity("F2", x);
    const z = yield context.df.callActivity("F3", y);
    return yield context.df.callActivity("F4", z);
});
```

この例では、`F1`、`F2`、`F3`、および `F4` という値が、関数アプリ内の他の関数の名前です。 通常の命令型のコーディング構造を使用して、制御フローを実装できます。 コードは、上から下に実行されます。 コードに条件文やループなどの既存言語の制御フロー セマンティクスを含めることができます。 `try`/`catch`/`finally` ブロックに、エラー処理ロジックを含めることができます。

`context` パラメーターの [DurableOrchestrationContext] (\(.NET\)) と `context.df` オブジェクト (JavaScript) を使用して、名前によって他の関数を呼び出し、パラメーターを渡し、関数の出力を返すことができます。 コードで `await` (C#) または `yield` (JavaScript) が呼び出されるたびに Durable Functions フレームワークによって、現在の関数インスタンスの進行状況に対するチェックポイントが設定されます。 プロセスまたは VM が実行途中でリサイクルされる場合、関数インスタンスは直前の `await` または `yield` 呼び出しから再開されます。 詳細については、次のセクション (パターン #2: ファンアウト/ファンイン) を参照してください。

> [!NOTE]
> JavaScript の `context` オブジェクトは、[DurableOrchestrationContext] パラメーターだけではなく、[関数コンテキスト](../functions-reference-node.md#context-object)全体を表しています。

### <a name="fan-in-out"></a>パターン #2: ファンアウト/ファンイン

ファンアウト/ファンイン パターンでは、複数の関数を並列で実行し、すべての関数が完了するまで待機します。 複数の関数から返される結果に基づいて集計作業が行われることは、よくあることです。

![ファンアウト/ファンイン パターンの図](./media/durable-functions-concepts/fan-out-fan-in.png)

通常の関数では、複数のメッセージを 1 つのキューに送信することでファンアウトできます。 ファンインして戻ることはずっと難しくなります。 通常の関数でファンインするには、キューによってトリガーされた関数の終了を追跡した後、関数の出力を格納するコードを記述する必要があります。 

Durable Functions 拡張機能では、比較的単純なコードでこのパターンを処理します。

#### <a name="c-script"></a>C# スクリプト

```csharp
public static async Task Run(DurableOrchestrationContext context)
{
    var parallelTasks = new List<Task<int>>();

    // Get a list of N work items to process in parallel.
    object[] workBatch = await context.CallActivityAsync<object[]>("F1");
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

### <a name="async-http"></a>パターン #3: 非同期 HTTP API

非同期 HTTP API パターンでは、外部クライアントとの間の実行時間の長い操作の状態を調整するという問題に対処します。 このパターンを実装する一般的な方法は、HTTP 呼び出しによって実行時間の長いアクションをトリガーすることです。 その後、ポーリングによって操作が完了したことを認識できる状態エンドポイントにクライアントをリダイレクトします。

![HTTP API パターンの図](./media/durable-functions-concepts/async-http-api.png)

Durable Functions には、実行時間の長い関数とやり取りするためのコードをシンプルにする組み込みの API が用意されています。 Durable Functions のクイック スタート サンプル ([C#](durable-functions-create-first-csharp.md) と [JavaScript](quickstart-js-vscode.md)) に、新しいオーケストレーター関数のインスタンスを開始するために使用できる、シンプルな REST コマンドが示されています。 インスタンスが開始されると、この拡張機能によって、オーケストレーター関数の状態をクエリする Webhook HTTP API が公開されます。 

次の例は、オーケストレーターを開始し、その状態をクエリする REST コマンドを示しています。 わかりやすくするため、この例では細部をいくらか省略しています。

```
> curl -X POST https://myfunc.azurewebsites.net/orchestrators/DoWork -H "Content-Length: 0" -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec

{"id":"b79baf67f717453ca9e86c5da21e03ec", ...}

> curl https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec

{"runtimeStatus":"Running","lastUpdatedTime":"2017-03-16T21:20:47Z", ...}

> curl https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 200 OK
Content-Length: 175
Content-Type: application/json

{"runtimeStatus":"Completed","lastUpdatedTime":"2017-03-16T21:20:57Z", ...}
```

状態は Durable Functions ランタイムによって管理されるため、自分で状態追跡メカニズムを実装する必要はありません。

Durable Functions 拡張機能には、長時間実行されるオーケストレーションを管理する Webhook が組み込まれています。 独自の関数トリガー (HTTP、キュー、Azure Event Hubs など) と `orchestrationClient` バインドを使用して、このパターンを自分で実装できます。 たとえば、キュー メッセージを使用して終了をトリガーできます。 または、生成されたキーを認証で使用する組み込みの Webhook の代わりに、Azure Active Directory の認証ポリシーによって保護される HTTP トリガーを使用できます。

HTTP API パターンを使用する方法の例をいくつか次に示します。

#### <a name="c"></a>C#

```csharp
// An HTTP-triggered function starts a new orchestrator function instance.
public static async Task<HttpResponseMessage> Run(
    HttpRequestMessage req,
    DurableOrchestrationClient starter,
    string functionName,
    ILogger log)
{
    // The function name comes from the request URL.
    // The function input comes from the request content.
    dynamic eventData = await req.Content.ReadAsAsync<object>();
    string instanceId = await starter.StartNewAsync(functionName, eventData);

    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");

    return starter.CreateCheckStatusResponse(req, instanceId);
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (Functions 2.x のみ)

```javascript
// An HTTP-triggered function starts a new orchestrator function instance.
const df = require("durable-functions");

module.exports = async function (context, req) {
    const client = df.getClient(context);

    // The function name comes from the request URL.
    // The function input comes from the request content.
    const eventData = req.body;
    const instanceId = await client.startNew(req.params.functionName, undefined, eventData);

    context.log(`Started orchestration with ID = '${instanceId}'.`);

    return client.createCheckStatusResponse(req, instanceId);
};
```

.NET における [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) `starter`パラメーターは、Durable Functions 拡張機能の一部である `orchestrationClient` 出力バインドからの値です。 JavaScript では、`df.getClient(context)` を呼び出すことによって、このオブジェクトが返されます。 これらのオブジェクトには、新しいまたは既存のオーケストレーター関数インスタンスの開始、インスタンスへのイベントの送信、インスタンスの停止、およびインタンスに対するクエリの送信を行うためのメソッドがあります。

上記の例では、HTTP によってトリガーされる関数が、受信 URL から `functionName` 値を受け取り、その値を [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) に渡します。 次に [CreateCheckStatusResponse](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateCheckStatusResponse_System_Net_Http_HttpRequestMessage_System_String_) バインド API が、`Location` ヘッダーとインスタンスの追加情報を含む応答を返します。 この情報を後で使用して、開始されたインスタンスの状態を調べたり、インスタンスを終了したりできます。

### <a name="monitoring"></a>パターン #4: 監視

監視パターンは、ワークフロー内の柔軟な繰り返しプロセスを指します。 一例は、特定の条件が満たされるまでポーリングすることです。 通常の[タイマー トリガー](../functions-bindings-timer.md)を使用して、定期的なクリーンアップ ジョブなどの基本的なシナリオに対処できますが、その間隔は静的であり、インスタンスの有効期間の管理は複雑になります。 Durable Functions を使用して、柔軟な繰り返し間隔の作成、タスクの有効期間の管理、単一のオーケストレーションからの複数の監視プロセスの作成を実行できます。

監視パターンの一例は、前述の非同期 HTTP API シナリオを逆にすることです。 外部クライアント用のエンドポイントを公開して実行時間の長い操作を監視する代わりに、長時間実行される監視で外部エンドポイントを使用して、状態が変化するまで待機します。

![監視パターンの図](./media/durable-functions-concepts/monitor.png)

数行のコードで、Durable Functions を使用して任意のエンドポイントを観察する複数のモニターを作成できます。 条件が満たされたときにこれらのモニターによって実行を終了するか、[DurableOrchestrationClient](durable-functions-instance-management.md) によってモニターを終了できます。 特定の条件に基づいてモニターの `wait` 間隔を変更できます (指数関数的バックオフなど)。 

次のコードでは、基本的なモニターが実装されます。

#### <a name="c-script"></a>C# スクリプト

```csharp
public static async Task Run(DurableOrchestrationContext context)
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

多くの自動化されたプロセスには、何らかの人による操作が含まれます。 自動化されたプロセスに人による操作が含まれる場合に問題になるのが、人は必ずしもクラウド サービスのように可用性と応答性が高くないということです。 自動化されたプロセスでは、タイムアウトと補正ロジックを使用して、これを許容する必要があります。

承認プロセスは、人による操作を含むビジネス プロセスの一例です。 特定の金額を超えた経費報告書は、上司の承認が必要な場合があります。 上司が (休暇中などで) その経費報告書を 72 時間以内に承認しない場合は、他の誰か (おそらくは上司の上司) から承認を得るためにエスカレーション プロセスを開始します。

![人による操作が含まれるパターンの図](./media/durable-functions-concepts/approval.png)

オーケストレーター関数を使用して、この例のパターンを実装できます。 オーケストレーターが[永続タイマー](durable-functions-timers.md)を使用して承認を要求します。 オーケストレーターは、タイムアウトが発生した場合はエスカレーションを行います。 オーケストレーターは、[外部イベント](durable-functions-external-events.md) (人による操作によって生成される通知など) が発生するまで待機します。

次の例では、人による操作パターンを示すための承認プロセスを作成しています。

#### <a name="c-script"></a>C# スクリプト

```csharp
public static async Task Run(DurableOrchestrationContext context)
{
    await context.CallActivityAsync("RequestApproval");
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
            await context.CallActivityAsync("Escalate");
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
public static async Task Run(string instanceId, DurableOrchestrationClient client)
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
            currentValue += operand;
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

持続エンティティは、.NET クラスとしてモデル化することもできます。 これは、操作のリストが大きくなり、そのほとんどが静的である場合に便利です。 次の例は、.NET クラスおよびメソッドを使用した `Counter` エンティティの同等の実装です。

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

クライアントは、`orchestrationClient` バインディングを使用して、エンティティ関数の*操作* をエンキューすることができます (「シグナル通知」とも呼ばれる)。

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

動的に生成されたプロキシは、タイプセーフな方法でシグナル通知エンティティにも使用できます。 シグナル通知に加えて、クライアントは、`orchestrationClient` バインディングのメソッドを使用して、エンティティ関数の状態をクエリすることもできます。

> [!NOTE]
> エンティティ関数は、現在 [Durable Functions 2.0 プレビュー](durable-functions-preview.md)でのみ使用できます。

## <a name="the-technology"></a>テクノロジ

Durable Functions 拡張機能の背後には、永続的なタスクのオーケストレーションを構築するために使用される GitHub のオープン ソース ライブラリである [Durable Task Framework](https://github.com/Azure/durabletask) があり、この拡張機能はその上に構築されています。 Azure Functions が Azure WebJobs のサーバーレスな進化形であるのと同じように、Durable Functions は Durable Task Framework のサーバーレスな進化形です。 Microsoft や他の組織は、ミッション クリティカルなプロセスを自動化するために、Durable Task Framework を広範囲にわたって使用しています。 サーバーレスな Azure Functions 環境には自然に適合します。

### <a name="event-sourcing-checkpointing-and-replay"></a>イベント ソーシング、チェックポイント設定、リプレイ

オーケストレーター関数は、[イベント ソーシング](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing)設計パターンを使用して、この関数の実行状態を確実に管理します。 Durable Functions 拡張機能では、オーケストレーションの現在の状態を直接格納する代わりに追加専用ストアを使用して、関数オーケストレーションが実行するすべてのアクションが記録されます。 追加専用ストアには、実行時のすべての状態の "ダンプ" に比べて、多くのメリットがあります。 メリットには、パフォーマンス、スケーラビリティ、および応答性の向上が含まれます。 トランザクション データの最終的な一貫性、完全な監査証跡、および監査履歴も取得できます。 監査証跡では、信頼性の高い補正アクションがサポートされます。

Durable Functions では、イベント ソーシングが透過的に使用されます。 バックグラウンドで、オーケストレーター関数内の `await` (C#) または `yield` (JavaScript) 演算子によって、オーケストレーター スレッドのコントロールが Durable Task Framework ディスパッチャーに戻されます。 次に、このディスパッチャーは、オーケストレーター関数がスケジュールした新しいアクション (1 つ以上の子関数を呼び出す、永続タイマーをスケジュールする、など) をストレージにコミットします。 この透過的なコミット アクションが、オーケストレーション インスタンスの実行履歴に追加されます。 この履歴はストレージ テーブルに格納されます。 次に、このコミット アクションはキューにメッセージを追加して実際の作業をスケジュールします。 この時点では、メモリからオーケストレーター関数をアンロードできます。 

オーケストレーター関数に対する課金は、Azure Functions の従量課金プランを使用している場合はここで停止します。 処理すべき作業がさらにある場合は、関数が再起動され、関数の状態が再構築されます。

オーケストレーション関数にさらに処理すべき作業 (応答メッセージの受信や永続タイマーの終了など) が与えられると、オーケストレーターが起動し、関数全体が始めから再実行され、ローカルの状態が再構築されます。 

このリプレイ中にコードが関数を呼び出そうとする (または他の非同期作業を行おうとする) と、Durable Task Framework によって現在のオーケストレーションの実行履歴が確認されます。 [アクティビティ関数](durable-functions-types-features-overview.md#activity-functions)が既に実行され、いくつかの結果が生成されていることがわかった場合は、関数の結果をリプレイし、オーケストレーター コードの実行を続行します。 リプレイは、関数コードが完了するまで、または新しい非同期作業がスケジュールされるまで続行されます。

### <a name="orchestrator-code-constraints"></a>オーケストレーター コードの制約

オーケストレーター コードのリプレイ動作によって、オーケストレーター関数に記述できるコードの種類に制約が生まれます。 たとえば、オーケストレーター コードは複数回リプレイされるため、確定的である必要があり、毎回同じ結果を出す必要があります。 制約の完全な一覧については、[オーケストレーター コードの制約](durable-functions-checkpointing-and-replay.md#orchestrator-code-constraints)に関する記事を参照してください。

## <a name="monitoring-and-diagnostics"></a>監視と診断

Durable Functions 拡張機能では、関数アプリが Azure Application Insights インストルメンテーション キーを使用して設定される場合、構造化された追跡データを [Application Insights](../functions-monitoring.md) に自動的に出力します。 この追跡データを使用して、オーケストレーションのアクションと進行状況を監視できます。

[Application Insights Analytics](../../application-insights/app-insights-analytics.md) を使用したときに、Application Insights ポータル内で Durable Functions の追跡イベントがどのように表示されるかの例を次に示します。

![Application Insights クエリ結果](./media/durable-functions-concepts/app-insights-1.png)

各ログ エントリの `customDimensions` フィールドで、有用な構造化データを見つけることができます。 完全に展開されたエントリの例を次に示します。

![Application Insights クエリの customDimensions フィールド](./media/durable-functions-concepts/app-insights-2.png)

Durable Task Framework ディスパッチャーのリプレイ動作のおかげで、リプレイされたアクションの豊富なログ エントリを確認できます。 冗長なログ エントリは、コア エンジンのリプレイ動作を理解するのに役立ちます。 [診断](durable-functions-diagnostics.md)に関する記事に、リプレイ ログをフィルターで除外するサンプル クエリが示されています。それによって "リアルタイム" ログだけを確認できます。

## <a name="storage-and-scalability"></a>ストレージとスケーラビリティ

Durable Functions 拡張機能では、Azure Storage のキュー、テーブル、および BLOB を使用して実行履歴の状態を保持し、関数の実行をトリガーします。 関数アプリの既定のストレージ アカウントを使用するか、別のストレージ アカウントを構成できます。 ストレージのスループット制限に基づいて、別のアカウントを使用することもできます。 記述するオーケストレーター コードは、これらのストレージ アカウント内のエンティティとやりとりをすることはありません。 Durable Task Framework では、エンティティは実装の詳細として直接管理されます。

オーケストレーター関数はアクティビティ関数をスケジュールし、内部キュー メッセージを介して応答を受け取ります。 関数アプリが Azure Functions の従量課金プランで実行されている場合、これらのキューは [Azure Functions Scale Controller](../functions-scale.md#how-the-consumption-and-premium-plans-work) によって監視されます。 必要に応じて、新しいコンピューティング インスタンスが追加されます。 複数の VM にスケールアウトされた場合、オーケストレーター関数は 1 つの VM 上で実行され、オーケストレーター関数によって呼び出されるアクティビティ関数は別の VM で実行されることがあります。 Durable Functions のスケーリング動作の詳細については、[パフォーマンスとスケール](durable-functions-perf-and-scale.md)に関する記事をご覧ください。

オーケストレーター アカウントの実行履歴は、テーブル ストレージに格納されます。 インスタンスが特定の VM 上に復元される場合、オーケストレーターは、テーブルから実行履歴をフェッチして、そのローカル状態を再構築できるようにします。 テーブル ストレージの履歴を使用できることの便利な面は、[Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) などのツールを使用してオーケストレーションの履歴を確認できることです。

Storage BLOB は、主にオーケストレーション インスタンスの複数の VM 間のスケールアウトを調整するためのリース メカニズムとして使用されます。 Storage BLOB は、テーブルまたはキューに直接保存することができない大きなメッセージのデータを保持するために使用されます。

![Azure Storage Explorer のスクリーンショット](./media/durable-functions-concepts/storage-explorer.png)

> [!NOTE]
> テーブル ストレージで実行履歴を確認できるのは簡単で便利ですが、このテーブルには依存関係を作成しないでください。 このテーブルは、Durable Functions 拡張機能の発展に伴って変更される可能性があります。

## <a name="known-issues"></a>既知の問題

すべての既知の問題は、[GitHub の Issue](https://github.com/Azure/azure-functions-durable-extension/issues) 一覧で追跡されています。 問題が発生したときに、その問題が GitHub で見つからない場合は、新しい Issue を開いてください。 問題の詳細な説明を含めてください。

## <a name="next-steps"></a>次の手順

Durable Functions の詳細については、[Durable Functions の関数の種類と機能](durable-functions-types-features-overview.md)に関する記事を参照してください。 

作業を開始するには:

> [!div class="nextstepaction"]
> [最初の Durable Function を作成する](durable-functions-create-first-csharp.md)

[DurableOrchestrationContext]: https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html
