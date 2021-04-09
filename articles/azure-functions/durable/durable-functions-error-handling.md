---
title: Durable Functions のエラー処理 - Azure
description: Azure Functions の Durable Functions 拡張機能で発生したエラーを処理する方法について説明します。
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: azfuncdf
ms.openlocfilehash: 023f9dfcc421935c3f7515e847108925d5e5521e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97673649"
---
# <a name="handling-errors-in-durable-functions-azure-functions"></a>Durable Functions のエラー処理 (Azure Functions)

Durable Function のオーケストレーションはコードで実装され、プログラミング言語の組み込みエラー処理機能を使用できます。 エラー処理と補正をオーケストレーションに追加するために学習する必要がある新しい概念は、実際にはありません。 ただし、注意する必要があるいくつかの動作があります。

## <a name="errors-in-activity-functions"></a>アクティビティ関数のエラー

アクティビティ関数でスローされた例外はオーケストレーター関数に戻され、`FunctionFailedException` としてスローされます。 ニーズに合ったエラー処理と補正コードをオーケストレーター関数内に記述できます。

たとえば、ある口座の資金を別の口座に送金する次のオーケストレーター関数を検討します。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("TransferFunds")]
public static async Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var transferDetails = context.GetInput<TransferOperation>();

    await context.CallActivityAsync("DebitAccount",
        new
        {
            Account = transferDetails.SourceAccount,
            Amount = transferDetails.Amount
        });

    try
    {
        await context.CallActivityAsync("CreditAccount",
            new
            {
                Account = transferDetails.DestinationAccount,
                Amount = transferDetails.Amount
            });
    }
    catch (Exception)
    {
        // Refund the source account.
        // Another try/catch could be used here based on the needs of the application.
        await context.CallActivityAsync("CreditAccount",
            new
            {
                Account = transferDetails.SourceAccount,
                Amount = transferDetails.Amount
            });
    }
}
```

> [!NOTE]
> 前述の C# の例は Durable Functions 2.x 用です。 Durable Functions 1.x の場合、`IDurableOrchestrationContext` の代わりに `DurableOrchestrationContext` を使用する必要があります。 バージョン間の相違点の詳細については、[Durable Functions のバージョン](durable-functions-versions.md)に関する記事を参照してください。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const transferDetails = context.df.getInput();

    yield context.df.callActivity("DebitAccount",
        {
            account = transferDetails.sourceAccount,
            amount = transferDetails.amount,
        }
    );

    try {
        yield context.df.callActivity("CreditAccount",
            {
                account = transferDetails.destinationAccount,
                amount = transferDetails.amount,
            }
        );
    }
    catch (error) {
        // Refund the source account.
        // Another try/catch could be used here based on the needs of the application.
        yield context.df.callActivity("CreditAccount",
            {
                account = transferDetails.sourceAccount,
                amount = transferDetails.amount,
            }
        );
    }
});
```
# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    transfer_details = context.get_input()

    yield context.call_activity('DebitAccount', {
         'account': transfer_details['sourceAccount'],
         'amount' : transfer_details['amount']
    })

    try:
        yield context.call_activity('CreditAccount', {
                'account': transfer_details['destinationAccount'],
                'amount': transfer_details['amount'],
            })
    except:
        yield context.call_activity('CreditAccount', {
            'account': transfer_details['sourceAccount'],
            'amount': transfer_details['amount']
        })

main = df.Orchestrator.create(orchestrator_function)
```

---

最初の **CreditAccount** 関数の呼び出しが失敗した場合、オーケストレーター関数は、資金を送金元口座に戻すことで、これを補正します。

## <a name="automatic-retry-on-failure"></a>エラー発生時の自動再試行

アクティビティ関数またはサブオーケストレーション関数を呼び出すときに、自動再試行ポリシーを指定できます。 次の例では、関数の呼び出しを最大 3 回試行し、次の再試行まで 5 秒間待機します。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("TimerOrchestratorWithRetry")]
public static async Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var retryOptions = new RetryOptions(
        firstRetryInterval: TimeSpan.FromSeconds(5),
        maxNumberOfAttempts: 3);

    await context.CallActivityWithRetryAsync("FlakyFunction", retryOptions, null);

    // ...
}
```

> [!NOTE]
> 前述の C# の例は Durable Functions 2.x 用です。 Durable Functions 1.x の場合、`IDurableOrchestrationContext` の代わりに `DurableOrchestrationContext` を使用する必要があります。 バージョン間の相違点の詳細については、[Durable Functions のバージョン](durable-functions-versions.md)に関する記事を参照してください。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const firstRetryIntervalInMilliseconds = 5000;
    const maxNumberOfAttempts = 3;

    const retryOptions = 
        new df.RetryOptions(firstRetryIntervalInMilliseconds, maxNumberOfAttempts);

    yield context.df.callActivityWithRetry("FlakyFunction", retryOptions);

    // ...
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    first_retry_interval_in_milliseconds = 5000
    max_number_of_attempts = 3

    retry_options = df.RetryOptions(first_retry_interval_in_milliseconds, max_number_of_attempts)

    yield context.call_activity_with_retry('FlakyFunction', retry_options)

main = df.Orchestrator.create(orchestrator_function)
```

---

前の例のアクティビティ関数呼び出しでは、自動再試行ポリシーを構成するためのパラメーターを使用します。 自動再試行ポリシーをカスタマイズするために、次のようないくつかのオプションがあります。

* **最大試行回数**: 再試行の最大数。
* **1 回目の再試行の間隔**: 1 回目の再試行の前に待つ時間。
* **バックオフ係数**: バックオフの増加率を決定するために使用される係数。 既定値は 1 です。
* **最大再試行間隔**: 再試行の間に待つ最長時間。
* **再試行タイムアウト**: 再試行の実行に費やす最長時間。 既定の動作は、無限の再試行です。
* **ハンドル**: 関数を再試行する必要があるかどうかを決定するために、ユーザー定義のコールバックを指定できます。 

> [!NOTE]
> ユーザー定義のコールバックは、JavaScript の Durable Functions (`context.df.RetryOptions`) では現在サポートされていません。


## <a name="function-timeouts"></a>関数のタイムアウト

オーケストレーター関数内の関数呼び出しは、完了に時間がかかりすぎる場合は破棄できます。 現時点でこれを適切に行うには、次の例のように、`context.CreateTimer` (.NET)、`context.df.createTimer` (JavaScript)、または `context.create_timer` (Python) を `Task.WhenAny` (.NET)、`context.df.Task.any` (JavaScript)、または `context.task_any` (Python) と組み合わせて使用して[持続的タイマー](durable-functions-timers.md)を作成します。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("TimerOrchestrator")]
public static async Task<bool> Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    TimeSpan timeout = TimeSpan.FromSeconds(30);
    DateTime deadline = context.CurrentUtcDateTime.Add(timeout);

    using (var cts = new CancellationTokenSource())
    {
        Task activityTask = context.CallActivityAsync("FlakyFunction");
        Task timeoutTask = context.CreateTimer(deadline, cts.Token);

        Task winner = await Task.WhenAny(activityTask, timeoutTask);
        if (winner == activityTask)
        {
            // success case
            cts.Cancel();
            return true;
        }
        else
        {
            // timeout case
            return false;
        }
    }
}
```

> [!NOTE]
> 前述の C# の例は Durable Functions 2.x 用です。 Durable Functions 1.x の場合、`IDurableOrchestrationContext` の代わりに `DurableOrchestrationContext` を使用する必要があります。 バージョン間の相違点の詳細については、[Durable Functions のバージョン](durable-functions-versions.md)に関する記事を参照してください。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const deadline = moment.utc(context.df.currentUtcDateTime).add(30, "s");

    const activityTask = context.df.callActivity("FlakyFunction");
    const timeoutTask = context.df.createTimer(deadline.toDate());

    const winner = yield context.df.Task.any([activityTask, timeoutTask]);
    if (winner === activityTask) {
        // success case
        timeoutTask.cancel();
        return true;
    } else {
        // timeout case
        return false;
    }
});
```
# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df
from datetime import datetime, timedelta

def orchestrator_function(context: df.DurableOrchestrationContext):
    deadline = context.current_utc_datetime + timedelta(seconds = 30)
    
    activity_task = context.call_activity('FlakyFunction')
    timeout_task = context.create_timer(deadline)

    winner = yield context.task_any(activity_task, timeout_task)
    if winner == activity_task:
        timeout_task.cancel()
        return True
    else:
        return False

main = df.Orchestrator.create(orchestrator_function)
```

---

> [!NOTE]
> このメカニズムは、実際には、進行中のアクティビティ関数の実行を終了するのではなく、 オーケストレーター関数が、単に結果を無視して、次に進めるようにするだけです。 詳細については、[タイマー](durable-functions-timers.md#usage-for-timeout)に関するドキュメントをご覧ください。

## <a name="unhandled-exceptions"></a>ハンドルされない例外

オーケストレーター関数がハンドルされない例外で失敗した場合、例外の詳細がログに記録され、インスタンスは `Failed` 状態で完了します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [永続的オーケストレーションについて学習する](durable-functions-eternal-orchestrations.md)

> [!div class="nextstepaction"]
> [問題を診断する方法を知る](durable-functions-diagnostics.md)
