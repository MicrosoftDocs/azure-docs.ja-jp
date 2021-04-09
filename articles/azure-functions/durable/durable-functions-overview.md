---
title: Durable Functions の概要 - Azure
description: Azure Functions の Durable Functions 拡張機能の概要です。
author: cgillum
ms.topic: overview
ms.date: 12/23/2020
ms.author: cgillum
ms.reviewer: azfuncdf
ms.openlocfilehash: f6199cb20cd56538823f7f7d0967a9cfe59f7099
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102636659"
---
# <a name="what-are-durable-functions"></a>Durable Functions とは

*Durable Functions* は、サーバーレス コンピューティング環境でステートフル関数を記述できる [Azure Functions](../functions-overview.md) の拡張機能です。 この拡張機能では、Azure Functions プログラミング モデルを使用して、[*オーケストレーター関数*](durable-functions-orchestrations.md)を記述することでステートフル ワークフローを定義でき、[*エンティティ関数*](durable-functions-entities.md)を記述することでステートフル エンティティを定義できます。 拡張機能によって状態、チェックポイント、再起動がバックグラウンドで管理されるため、ユーザーはビジネス ロジックに専念できます。

## <a name="supported-languages"></a><a name="language-support"></a>サポートされている言語

Durable Functions では、現在次の言語をサポートしています。

* **C#**: [プリコンパイル済みクラス ライブラリ](../functions-dotnet-class-library.md)と [C# スクリプト](../functions-reference-csharp.md)の両方。
* **JavaScript**: Azure Functions ランタイムのバージョン 2.x でのみサポートされています。 Durable Functions 拡張機能のバージョン 1.7.0 以降が必要です。 
* **Python**: Durable Functions 拡張機能のバージョン 2.3.1 以降が必要です。
* **F#**: プリコンパイル済みクラス ライブラリと F# スクリプト。 F# スクリプトは、Azure Functions ランタイムのバージョン 1.x でのみサポートされています。
* **PowerShell**: Durable Functions のサポートは、現在パブリック プレビューの段階です。 Azure Functions ランタイムのバージョン 3.x と PowerShell 7 でのみサポートされています。 Durable Functions 拡張機能のバージョン 2.2.2 以降が必要です。 現在サポートされているパターンは次のとおりです。[関数チェーン](#chaining)、[ファンアウトおよびファンイン](#fan-in-out)、[非同期 HTTP API](#async-http)。

最新の機能と更新プログラムにアクセスするには、最新バージョンの Durable Functions 拡張機能と言語固有の Durable Functions ライブラリを使用することをお勧めします。 [Durable Functions のバージョン](durable-functions-versions.md)の詳細情報を参照してください。

Durable Functions では、すべての [Azure Functions 言語](../supported-languages.md)をサポートすることを目標としています。 追加言語をサポートするための最新の作業状況については、[Durable Functions の問題の一覧](https://github.com/Azure/azure-functions-durable-extension/issues)を参照してください。

Azure Functions と同様に、[Visual Studio 2019](durable-functions-create-first-csharp.md)、[Visual Studio Code](quickstart-js-vscode.md)、および [Azure portal](durable-functions-create-portal.md) を使用して Durable Functions を開発するのに役立つテンプレートがあります。

## <a name="application-patterns"></a>アプリケーション パターン

Durable Functions の主なユース ケースは、サーバーレス アプリケーションにおける複雑でステートフルな調整の要件をシンプルにすることです。 以下のセクションでは、Durable Functions を使用することでメリットがある、典型的なアプリケーションのパターンを示します。

* [関数チェーン](#chaining)
* [ファンアウト/ファンイン](#fan-in-out)
* [非同期 HTTP API シリーズ](#async-http)
* [Monitoring](#monitoring)
* [人による操作](#human)
* [アグリゲーター (ステートフル エンティティ)](#aggregator)

### <a name="pattern-1-function-chaining"></a>パターン #1: 関数チェーン

関数チェーン パターンでは、一連の関数が特定の順序で実行されます。 このパターンでは、ある関数の出力が、別の関数の入力に適用されます。

![関数チェーン パターンの図](./media/durable-functions-concepts/function-chaining.png)

次の例に示すように、Durable Functions を使用して、関数チェーン パターンを簡潔に実装できます。

この例では、`F1`、`F2`、`F3`、および `F4` という値が、同じ関数アプリ内の他の関数の名前です。 通常の命令型のコーディング構造を使用して、制御フローを実装できます。 コードは、上から下に実行されます。 コードに条件文やループなどの既存言語の制御フロー セマンティクスを含めることができます。 `try`/`catch`/`finally` ブロックに、エラー処理ロジックを含めることができます。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("Chaining")]
public static async Task<object> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
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

`context` パラメーターを使用して、他の関数を名前で呼び出し、パラメーターを渡して、関数の出力を返すことができます。 コードが `await` を呼び出すたびに Durable Functions フレームワークは、現在の関数インスタンスの進行状況にチェックポイントを設定します。 プロセスまたは仮想マシンが実行途中でリサイクルされる場合、関数インスタンスは直前の `await` 呼び出しから再開されます。 詳細については、次のセクション (パターン #2: ファンアウト/ファンイン) を参照してください。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    try {
        const x = yield context.df.callActivity("F1");
        const y = yield context.df.callActivity("F2", x);
        const z = yield context.df.callActivity("F3", y);
        return    yield context.df.callActivity("F4", z);
    } catch (error) {
        // Error handling or compensation goes here.
    }
});
```

`context.df` オブジェクトを使用して、他の関数を名前で呼び出し、パラメーターを渡して、関数の出力を返すことができます。 コードが `yield` を呼び出すたびに Durable Functions フレームワークは、現在の関数インスタンスの進行状況にチェックポイントを設定します。 プロセスまたは仮想マシンが実行途中でリサイクルされる場合、関数インスタンスは直前の `yield` 呼び出しから再開されます。 詳細については、次のセクション (パターン #2: ファンアウト/ファンイン) を参照してください。

> [!NOTE]
> JavaScript の `context` オブジェクトは、[関数コンテキスト](../functions-reference-node.md#context-object)全体を表しています。 Durable Functions コンテキストには、メイン コンテキストの `df` プロパティを使用してアクセスします。

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df


def orchestrator_function(context: df.DurableOrchestrationContext):
    x = yield context.call_activity("F1", None)
    y = yield context.call_activity("F2", x)
    z = yield context.call_activity("F3", y)
    result = yield context.call_activity("F4", z)
    return result


main = df.Orchestrator.create(orchestrator_function)
```

`context` オブジェクトを使用して、他の関数を名前で呼び出し、パラメーターを渡して、関数の出力を返すことができます。 コードが `yield` を呼び出すたびに Durable Functions フレームワークは、現在の関数インスタンスの進行状況にチェックポイントを設定します。 プロセスまたは仮想マシンが実行途中でリサイクルされる場合、関数インスタンスは直前の `yield` 呼び出しから再開されます。 詳細については、次のセクション (パターン #2: ファンアウト/ファンイン) を参照してください。

> [!NOTE]
> Python の `context` オブジェクトは、オーケストレーション コンテキストを表します。 メインの Azure Functions コンテキストには、オーケストレーション コンテキストの `function_context` プロパティを使用してアクセスします。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```PowerShell
param($Context)

$X = Invoke-ActivityFunction -FunctionName 'F1'
$Y = Invoke-ActivityFunction -FunctionName 'F2' -Input $X
$Z = Invoke-ActivityFunction -FunctionName 'F3' -Input $Y
Invoke-ActivityFunction -FunctionName 'F4' -Input $Z
```

`Invoke-ActivityFunction` コマンドを使用して他の関数を名前で呼び出し、パラメーターを渡して、関数の出力を返すことができます。 コードで `NoWait` スイッチを用いずに `Invoke-ActivityFunction` を呼び出すたびに、Durable Functions フレームワークによって、現在の関数インスタンスの進行状況に対するチェックポイントが設定されます。 プロセスまたは仮想マシンが実行途中でリサイクルされる場合、関数インスタンスは直前の `Invoke-ActivityFunction` 呼び出しから再開されます。 詳細については、次のセクション (パターン #2: ファンアウト/ファンイン) を参照してください。

---

### <a name="pattern-2-fan-outfan-in"></a>パターン #2: ファンアウト/ファンイン

ファンアウト/ファンイン パターンでは、複数の関数を並列で実行し、すべての関数が完了するまで待機します。 複数の関数から返される結果に基づいて集計作業が行われることは、よくあることです。

![ファンアウト/ファンイン パターンの図](./media/durable-functions-concepts/fan-out-fan-in.png)

通常の関数では、複数のメッセージを 1 つのキューに送信することでファンアウトできます。 ファンインして戻ることはずっと難しくなります。 通常の関数でファンインするには、キューによってトリガーされた関数の終了を追跡した後、関数の出力を格納するコードを記述する必要があります。

Durable Functions 拡張機能では、比較的単純なコードでこのパターンを処理します。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("FanOutFanIn")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
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

ファンアウト作業は、`F2` 関数の複数のインスタンスに分散されます。 動的タスク リストを使用して、この作業が追跡されます。 `Task.WhenAll` が呼び出され、すべての呼び出された関数が終了するまで待機します。 その後、`F2` 関数の出力が動的タスク リストから集計され、`F3` 関数に渡されます。

`Task.WhenAll` の `await` 呼び出しの際に設定される自動チェックポイントによって、実行途中でクラッシュや再起動が発生した場合でも、既に完了したすべてのタスクをやり直す必要がなくなります。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

ファンアウト作業は、`F2` 関数の複数のインスタンスに分散されます。 動的タスク リストを使用して、この作業が追跡されます。 `context.df.Task.all` API が呼び出され、すべての呼び出された関数が終了するまで待機します。 その後、`F2` 関数の出力が動的タスク リストから集計され、`F3` 関数に渡されます。

`context.df.Task.all` の `yield` 呼び出しの際に設定される自動チェックポイントによって、実行途中でクラッシュや再起動が発生した場合でも、既に完了したすべてのタスクをやり直す必要がなくなります。

# <a name="python"></a>[Python](#tab/python)

```python
import azure.durable_functions as df


def orchestrator_function(context: df.DurableOrchestrationContext):
    # Get a list of N work items to process in parallel.
    work_batch = yield context.call_activity("F1", None)

    parallel_tasks = [ context.call_activity("F2", b) for b in work_batch ]
    
    outputs = yield context.task_all(parallel_tasks)

    # Aggregate all N outputs and send the result to F3.
    total = sum(outputs)
    yield context.call_activity("F3", total)


main = df.Orchestrator.create(orchestrator_function)
```

ファンアウト作業は、`F2` 関数の複数のインスタンスに分散されます。 動的タスク リストを使用して、この作業が追跡されます。 `context.task_all` API が呼び出され、すべての呼び出された関数が終了するまで待機します。 その後、`F2` 関数の出力が動的タスク リストから集計され、`F3` 関数に渡されます。

`context.task_all` の `yield` 呼び出しの際に設定される自動チェックポイントによって、実行途中でクラッシュや再起動が発生した場合でも、既に完了したすべてのタスクをやり直す必要がなくなります。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```PowerShell
param($Context)

# Get a list of work items to process in parallel.
$WorkBatch = Invoke-ActivityFunction -FunctionName 'F1'

$ParallelTasks =
    foreach ($WorkItem in $WorkBatch) {
        Invoke-ActivityFunction -FunctionName 'F2' -Input $WorkItem -NoWait
    }

$Outputs = Wait-ActivityFunction -Task $ParallelTasks

# Aggregate all outputs and send the result to F3.
$Total = ($Outputs | Measure-Object -Sum).Sum
Invoke-ActivityFunction -FunctionName 'F3' -Input $Total
```

ファンアウト作業は、`F2` 関数の複数のインスタンスに分散されます。 `F2` 関数の呼び出しで `NoWait` スイッチが使用されていることに注意してください。このスイッチを使用すると、オーケストレーターはアクティビティを完了しなくても `F2` を呼び出すことができます。 動的タスク リストを使用して、この作業が追跡されます。 `Wait-ActivityFunction` コマンドが呼び出され、呼び出されたすべての関数が終了するまで待機します。 その後、`F2` 関数の出力が動的タスク リストから集計され、`F3` 関数に渡されます。

`Wait-ActivityFunction` 呼び出しの際に設定される自動チェックポイントによって、実行途中でのクラッシュや再起動が発生した場合でも、既に完了したタスクをやり直す必要がなくなります。

---

> [!NOTE]
> まれな状況ですが、アクティビティ関数の完了後、完了がオーケストレーション履歴に保存される前の時間帯にクラッシュが発生する可能性があります。 この場合、アクティビティ関数は、プロセス復旧後に最初から再実行されます。

### <a name="pattern-3-async-http-apis"></a>パターン #3: 非同期 HTTP API

非同期 HTTP API パターンでは、外部クライアントとの間の実行時間の長い操作の状態を調整するという問題に対処します。 このパターンを実装する一般的な方法は、HTTP エンドポイントによって実行時間の長いアクションをトリガーすることです。 その後、ポーリングによって操作が完了したことを認識できる状態エンドポイントにクライアントをリダイレクトします。

![HTTP API パターンの図](./media/durable-functions-concepts/async-http-api.png)

Durable Functions には、このパターン向けの **組み込みサポート** が用意されており、長時間の関数の実行を操作するために記述する必要があるコードを簡略化できます。または、そのようなコードの記述が不要になります。 たとえば、Durable Functions のクイック スタート サンプル ([C#](durable-functions-create-first-csharp.md) と [JavaScript](quickstart-js-vscode.md)) に、新しいオーケストレーター関数のインスタンスを開始するために使用できる、シンプルな REST コマンドが示されています。 インスタンスが開始されると、この拡張機能によって、オーケストレーター関数の状態をクエリする Webhook HTTP API が公開されます。 

次の例は、オーケストレーターを開始し、その状態をクエリする REST コマンドを示しています。 わかりやすくするため、この例ではプロトコルの細部をいくらか省略しています。

```
> curl -X POST https://myfunc.azurewebsites.net/api/orchestrators/DoWork -H "Content-Length: 0" -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/instances/b79baf67f717453ca9e86c5da21e03ec

{"id":"b79baf67f717453ca9e86c5da21e03ec", ...}

> curl https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/instances/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/instances/b79baf67f717453ca9e86c5da21e03ec

{"runtimeStatus":"Running","lastUpdatedTime":"2019-03-16T21:20:47Z", ...}

> curl https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/instances/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 200 OK
Content-Length: 175
Content-Type: application/json

{"runtimeStatus":"Completed","lastUpdatedTime":"2019-03-16T21:20:57Z", ...}
```

状態は Durable Functions ランタイムによって自動的に管理されるため、自分で状態追跡メカニズムを実装する必要はありません。

Durable Functions 拡張機能には、長時間実行されるオーケストレーションを管理する組み込みの HTTP API が公開されています。 または、独自の関数トリガー (HTTP、キュー、Azure Event Hubs など) と[オーケストレーション クライアント バインド](durable-functions-bindings.md#orchestration-client)を使用して、このパターンを自分で実装できます。 たとえば、キュー メッセージを使用して終了をトリガーできます。 または、生成されたキーを認証で使用する組み込みの HTTP API の代わりに、Azure Active Directory の認証ポリシーによって保護される HTTP トリガーを使用できます。

詳細については、[HTTP 機能](durable-functions-http-features.md)の記事を参照してください。その記事では、Durable Functions 拡張機能を使用して、非同期の長時間プロセスを HTTP 経由で公開する方法について説明しています。

### <a name="pattern-4-monitor"></a><a name="monitoring"></a>パターン #4: モニター

監視パターンは、ワークフロー内の柔軟な繰り返しプロセスを指します。 一例は、特定の条件が満たされるまでポーリングすることです。 通常の[タイマー トリガー](../functions-bindings-timer.md)を使用して、定期的なクリーンアップ ジョブなどの基本的なシナリオに対処できますが、その間隔は静的であり、インスタンスの有効期間の管理は複雑になります。 Durable Functions を使用して、柔軟な繰り返し間隔の作成、タスクの有効期間の管理、単一のオーケストレーションからの複数の監視プロセスの作成を実行できます。

監視パターンの一例は、前述の非同期 HTTP API シナリオを逆にすることです。 外部クライアント用のエンドポイントを公開して実行時間の長い操作を監視する代わりに、長時間実行される監視で外部エンドポイントを使用して、状態が変化するまで待機します。

![監視パターンの図](./media/durable-functions-concepts/monitor.png)

数行のコードで、Durable Functions を使用して任意のエンドポイントを観察する複数のモニターを作成できます。 条件が満たされたときにこれらのモニターによって実行を終了するか、別の関数から持続的オーケストレーション クライアントを使用してモニターを終了できます。 特定の条件に基づいてモニターの `wait` 間隔を変更できます (指数関数的バックオフなど)。 

次のコードでは、基本的なモニターが実装されます。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("MonitorJobStatus")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const jobId = context.df.getInput();
    const pollingInterval = getPollingInterval();
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

# <a name="python"></a>[Python](#tab/python)

```python
import azure.durable_functions as df
import json
from datetime import timedelta 


def orchestrator_function(context: df.DurableOrchestrationContext):
    job = json.loads(context.get_input())
    job_id = job["jobId"]
    polling_interval = job["pollingInterval"]
    expiry_time = job["expiryTime"]

    while context.current_utc_datetime < expiry_time:
        job_status = yield context.call_activity("GetJobStatus", job_id)
        if job_status == "Completed":
            # Perform an action when a condition is met.
            yield context.call_activity("SendAlert", job_id)
            break

        # Orchestration sleeps until this time.
        next_check = context.current_utc_datetime + timedelta(seconds=polling_interval)
        yield context.create_timer(next_check)

    # Perform more work here, or let the orchestration end.


main = df.Orchestrator.create(orchestrator_function)
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

現在、モニターは PowerShell ではサポートされていません。

---

要求が受信されると、そのジョブ ID 用の新しいオーケストレーション インスタンスが作成されます。 インスタンスは、条件が満たされてループが終了するまで、状態をポーリングします。 ポーリング間隔は、永続タイマーによって制御されます。 その後、さらに作業を実行するか、オーケストレーションを終了できます。 `nextCheck` が `expiryTime` を超えると、モニターが終了します。

### <a name="pattern-5-human-interaction"></a>パターン #5: 人による操作

多くの自動化されたプロセスには、何らかの人による操作が含まれます。 自動化されたプロセスに人による操作が含まれる場合に問題になるのが、人は必ずしもクラウド サービスのように可用性と応答性が高くないということです。 自動化されたプロセスでは、タイムアウトと補正ロジックを使用して、この操作を許容する必要があります。

承認プロセスは、人による操作を含むビジネス プロセスの一例です。 特定の金額を超えた経費報告書は、上司の承認が必要な場合があります。 上司が (休暇中などで) その経費報告書を 72 時間以内に承認しない場合は、他の誰か (おそらくは上司の上司) から承認を得るためにエスカレーション プロセスを開始します。

![人による操作が含まれるパターンの図](./media/durable-functions-concepts/approval.png)

オーケストレーター関数を使用して、この例のパターンを実装できます。 オーケストレーターが[永続タイマー](durable-functions-timers.md)を使用して承認を要求します。 オーケストレーターは、タイムアウトが発生した場合はエスカレーションを行います。 オーケストレーターは、[外部イベント](durable-functions-external-events.md) (人による操作によって生成される通知など) が発生するまで待機します。

次の例では、人による操作パターンを示すための承認プロセスを作成しています。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("ApprovalWorkflow")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
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

永続タイマーを作成するために、`context.CreateTimer` が呼び出されます。 通知は `context.WaitForExternalEvent` が受け取ります。 その後、エスカレーションする (タイムアウトが先に発生した場合) か承認を処理する (タイムアウト前に承認を得た場合) かを決定するために、`Task.WhenAny` が呼び出されます。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

永続タイマーを作成するために、`context.df.createTimer` が呼び出されます。 通知は `context.df.waitForExternalEvent` が受け取ります。 その後、エスカレーションする (タイムアウトが先に発生した場合) か承認を処理する (タイムアウト前に承認を得た場合) かを決定するために、`context.df.Task.any` が呼び出されます。

# <a name="python"></a>[Python](#tab/python)

```python
import azure.durable_functions as df
import json
from datetime import timedelta 


def orchestrator_function(context: df.DurableOrchestrationContext):
    yield context.call_activity("RequestApproval", None)

    due_time = context.current_utc_datetime + timedelta(hours=72)
    durable_timeout_task = context.create_timer(due_time)
    approval_event_task = context.wait_for_external_event("ApprovalEvent")

    winning_task = yield context.task_any([approval_event_task, durable_timeout_task])

    if approval_event_task == winning_task:
        durable_timeout_task.cancel()
        yield context.call_activity("ProcessApproval", approval_event_task.result)
    else:
        yield context.call_activity("Escalate", None)


main = df.Orchestrator.create(orchestrator_function)
```

永続タイマーを作成するために、`context.create_timer` が呼び出されます。 通知は `context.wait_for_external_event` が受け取ります。 その後、エスカレーションする (タイムアウトが先に発生した場合) か承認を処理する (タイムアウト前に承認を得た場合) かを決定するために、`context.task_any` が呼び出されます。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

現在、ユーザー操作は PowerShell ではサポートされていません。

---

外部クライアントでは、[組み込みの HTTP API](durable-functions-http-api.md#raise-event) を使用して、待機中のオーケストレーター関数にイベント通知を配信できます。

```bash
curl -d "true" http://localhost:7071/runtime/webhooks/durabletask/instances/{instanceId}/raiseEvent/ApprovalEvent -H "Content-Type: application/json"
```

同じ関数アプリ内の別の関数から持続的オーケストレーション クライアントを使用してイベントを発生させることもできます。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("RaiseEventToOrchestration")]
public static async Task Run(
    [HttpTrigger] string instanceId,
    [DurableClient] IDurableOrchestrationClient client)
{
    bool isApproved = true;
    await client.RaiseEventAsync(instanceId, "ApprovalEvent", isApproved);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const isApproved = true;
    await client.raiseEvent(instanceId, "ApprovalEvent", isApproved);
};
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.durable_functions as df


async def main(client: str):
    durable_client = df.DurableOrchestrationClient(client)
    is_approved = True
    await durable_client.raise_event(instance_id, "ApprovalEvent", is_approved)
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

現在、ユーザー操作は PowerShell ではサポートされていません。

---

### <a name="pattern-6-aggregator-stateful-entities"></a><a name="aggregator"></a>パターン #6:アグリゲーター (ステートフル エンティティ)

6 番目のパターンは、ある期間のイベント データを 1 つのアドレス可能な *エンティティ* に集計することに関連しています。 このパターンでは、集計されるデータは、複数のソースから取得されるか、バッチで配信されるか、または長期間にわたって分散される可能性があります。 アグリゲーターがイベント データの到着時にイベント データに対してアクションを行ったり、外部クライアントが集計されたデータをクエリする必要が生じたりする場合があります。

![アグリゲーターの図](./media/durable-functions-concepts/aggregator.png)

このパターンを通常のステートレス関数で実装しようとする際に注意が必要な点は、同時実行制御が大きな課題となることです。 複数のスレッドが同時に同じデータを変更することに注意する必要があるだけでなく、アグリゲーターが一度に 1 つの VM 上でのみ実行されるようにすることも注意する必要があります。

[持続エンティティ](durable-functions-entities.md)を使用すると、このパターンを単一の関数として簡単に実装できます。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    int currentValue = ctx.GetState<int>();
    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            int amount = ctx.GetInput<int>();
            ctx.SetState(currentValue + amount);
            break;
        case "reset":
            ctx.SetState(0);
            break;
        case "get":
            ctx.Return(currentValue);
            break;
    }
}
```

持続エンティティは、.NET のクラスとしてモデル化することもできます。 このモデルは、操作の一覧が固定されていて、サイズが大きくなった場合に役立ちます。 次の例は、.NET クラスおよびメソッドを使用した `Counter` エンティティの同等の実装です。

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.entity(function(context) {
    const currentValue = context.df.getState(() => 0);
    switch (context.df.operationName) {
        case "add":
            const amount = context.df.getInput();
            context.df.setState(currentValue + amount);
            break;
        case "reset":
            context.df.setState(0);
            break;
        case "get":
            context.df.return(currentValue);
            break;
    }
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import logging
import json

import azure.functions as func
import azure.durable_functions as df


def entity_function(context: df.DurableOrchestrationContext):

    current_value = context.get_state(lambda: 0)
    operation = context.operation_name
    if operation == "add":
        amount = context.get_input()
        current_value += amount
        context.set_result(current_value)
    elif operation == "reset":
        current_value = 0
    elif operation == "get":
        context.set_result(current_value)
    
    context.set_state(current_value)

main = df.Entity.create(entity_function)
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

現在、持続エンティティは PowerShell ではサポートされていません。

---

クライアントは、[エンティティ クライアント バインディング](durable-functions-bindings.md#entity-client)を使用して、エンティティ関数の *操作* をエンキューすることができます ("シグナル通知" とも呼ばれる)。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static async Task Run(
    [EventHubTrigger("device-sensor-events")] EventData eventData,
    [DurableClient] IDurableOrchestrationClient entityClient)
{
    var metricType = (string)eventData.Properties["metric"];
    var delta = BitConverter.ToInt32(eventData.Body, eventData.Body.Offset);

    // The "Counter/{metricType}" entity is created on-demand.
    var entityId = new EntityId("Counter", metricType);
    await entityClient.SignalEntityAsync(entityId, "add", delta);
}
```

> [!NOTE]
> 動的に生成されたプロキシは、.NET においてタイプセーフな方法でシグナル通知エンティティに対して使用することもできます。 シグナル通知に加えて、クライアントは、オーケストレーション クライアント バインディングで[タイプ セーフのメソッド](durable-functions-bindings.md#entity-client-usage)を使用して、エンティティ関数の状態をクエリすることもできます。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    await context.df.signalEntity(entityId, "add", 1);
};
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df


async def main(req: func.HttpRequest, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)
    entity_id = df.EntityId("Counter", "myCounter")
    instance_id = await client.signal_entity(entity_id, "add", 1)
    return func.HttpResponse("Entity signaled")
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

現在、持続エンティティは PowerShell ではサポートされていません。

---

エンティティ関数は、[Durable Functions 2.0](durable-functions-versions.md) 以降の C#、JavaScript、Python で使用できます。

## <a name="the-technology"></a>テクノロジ

Durable Functions 拡張機能の背後には、コードでワークフローを構築するために使用される GitHub のオープン ソース ライブラリである [Durable Task Framework](https://github.com/Azure/durabletask) があり、この拡張機能はその上に構築されています。 Azure Functions が Azure WebJobs のサーバーレスな進化形であるのと同じように、Durable Functions は Durable Task Framework のサーバーレスな進化形です。 Microsoft や他の組織は、ミッション クリティカルなプロセスを自動化するために、Durable Task Framework を広範囲にわたって使用しています。 サーバーレスな Azure Functions 環境には自然に適合します。

## <a name="code-constraints"></a>コードの制約

信頼性の高い長時間の実行の保証を提供するために、オーケストレーター関数には、従う必要がある一連のコーディング規則があります。 詳細については、「[オーケストレーター関数コードの制約](durable-functions-code-constraints.md)」の記事を参照してください。

## <a name="billing"></a>課金

Durable Functions は Azure Functions と同じように課金されます。 詳細については、[Azure Functions の価格](https://azure.microsoft.com/pricing/details/functions/)に関するページを参照してください。 Azure Functions の[従量課金プラン](../consumption-plan.md)でオーケストレーター関数を実行する場合は、注意する必要がある課金動作がいくつかあります。 これらの動作の詳細については、「[Durable Functions の課金](durable-functions-billing.md)」の記事を参照してください。

## <a name="jump-right-in"></a>すぐに始める

次の言語固有のクイック スタート チュートリアルのいずれかを完了することで、10 分足らずで Durable Functions を使い始めることができます。

* [C# と Visual Studio 2019 を使用する場合](durable-functions-create-first-csharp.md)
* [Visual Studio Code と JavaScript を使用する場合](quickstart-js-vscode.md)
* [Visual Studio Code と Python を使用する場合](quickstart-python-vscode.md)
* [Visual Studio Code と PowerShell を使用する場合](quickstart-powershell-vscode.md)

これらのクイックスタートでは、"hello world" という持続的関数をローカルで作成してテストします。 その後、関数コードを Azure に発行します。 作成した関数は、他の関数の呼び出しを調整し、連結します。

## <a name="learn-more"></a>詳細情報

次のビデオでは、Durable Functions の利点を取り上げています。

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Durable-Functions-in-Azure-Functions/player] 

Durable Functions と基になるテクノロジの詳細については、次のビデオをご覧ください (.NET に重点を置いていますが、考え方はサポートされている他の言語にも当てはまります)。

> [!VIDEO https://channel9.msdn.com/Events/dotnetConf/2018/S204/player]

Durable Functions は [Azure Functions](../functions-overview.md)の高度な拡張機能であるため、すべてのアプリケーションに適しているわけではありません。 他の Azure オーケストレーション テクノロジとの比較については、「[Azure Functions と Azure Logic Apps の比較](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-azure-functions-and-azure-logic-apps)」を参照してください。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Durable Functions の関数の種類と機能](durable-functions-types-features-overview.md)
