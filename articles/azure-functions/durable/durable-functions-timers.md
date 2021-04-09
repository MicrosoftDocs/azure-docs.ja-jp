---
title: Durable Functions のタイマー - Azure
description: Azure Functions の Durable Functions 拡張機能で持続的タイマーを実装する方法について説明します。
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: azfuncdf
ms.openlocfilehash: bb91f205a9b83b0b4b410644ef6c0fcbbf60876a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91876449"
---
# <a name="timers-in-durable-functions-azure-functions"></a>Durable Functions のタイマー (Azure Functions)

[Durable Functions](durable-functions-overview.md) には、遅延を実装したり、非同期アクションでタイムアウトを設定したりできるように、オーケストレーター関数で使用する "*持続的タイマー*" が用意されています。 持続的タイマーは、`Thread.Sleep` および `Task.Delay` (C#)、または `setTimeout()` および `setInterval()` (JavaScript)、または `time.sleep()` (Python) の代わりに、オーケストレーター関数で使用されます。

[オーケストレーション トリガー バインド](durable-functions-bindings.md#orchestration-trigger)の `CreateTimer` (.NET) メソッドまたは `createTimer` (JavaScript) メソッドを呼び出すことによって、持続的タイマーを作成します。 メソッドは、指定した日時に完了するタスクを返します。

## <a name="timer-limitations"></a>タイマーの制限事項

午後 4 時 30 分に期限切れになるタイマーを作成すると、基になる Durable Task Framework は、午後 4 時 30 分にのみ表示されるメッセージをエンキューします。 Azure Functions 従量課金プランで実行されている場合、新しく表示されるタイマー メッセージにより、適切な VM で関数アプリが確実にアクティブになります。

> [!NOTE]
> * Durable 拡張機能の[バージョン 2.3.0](https://github.com/Azure/azure-functions-durable-extension/releases/tag/v2.3.0) 以降、持続的タイマーは無制限です。 それ以前のバージョンの拡張機能では、持続的タイマーは 7 日間に制限されています。 以前のバージョンを使用していて、7 日より長い遅延が必要な場合は、`while` ループ内でタイマー API を使用して、この遅延をシミュレートできます。
> * 持続的タイマーの起動時間を計算するときは、.NET では `DateTime.UtcNow` ではなく `CurrentUtcDateTime` を、JavaScript では `Date.now` または `Date.UTC` ではなく `currentUtcDateTime` を常に使用します。 詳細については、「[オーケストレーター関数コードの制約](durable-functions-code-constraints.md)」の記事を参照してください。

## <a name="usage-for-delay"></a>遅延の使用

次の例は、実行を遅らせる持続的タイマーを使用する方法を示しています。 この例では、10 日間、請求の通知を毎日発行します。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("BillingIssuer")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    for (int i = 0; i < 10; i++)
    {
        DateTime deadline = context.CurrentUtcDateTime.Add(TimeSpan.FromDays(1));
        await context.CreateTimer(deadline, CancellationToken.None);
        await context.CallActivityAsync("SendBillingEvent");
    }
}
```

> [!NOTE]
> 前記の C# の例は Durable Functions 2.x をターゲットにしています。 Durable Functions 1.x の場合、`IDurableOrchestrationContext` の代わりに `DurableOrchestrationContext` を使用する必要があります。 バージョン間の相違点の詳細については、[Durable Functions のバージョン](durable-functions-versions.md)に関する記事を参照してください。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```js
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    for (let i = 0; i < 10; i++) {
        const deadline = moment.utc(context.df.currentUtcDateTime).add(1, 'd');
        yield context.df.createTimer(deadline.toDate());
        yield context.df.callActivity("SendBillingEvent");
    }
});
```
# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df
from datetime import datetime, timedelta

def orchestrator_function(context: df.DurableOrchestrationContext):
    for i in range(0, 9):
        deadline = context.current_utc_datetime + timedelta(days=1)
        yield context.create_timer(deadline)
        yield context.call_activity("SendBillingEvent")

main = df.Orchestrator.create(orchestrator_function)
```
---

> [!WARNING]
> オーケストレーター関数の無限ループが発生しなようにしてください。 無限ループ シナリオを安全かつ効率的に実装する方法については、[永続的オーケストレーション](durable-functions-eternal-orchestrations.md)に関するページをご覧ください。

## <a name="usage-for-timeout"></a>タイムアウトの使用

この例は、持続的タイマーを使用して、タイムアウトを実装する方法を示しています。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("TryGetQuote")]
public static async Task<bool> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    TimeSpan timeout = TimeSpan.FromSeconds(30);
    DateTime deadline = context.CurrentUtcDateTime.Add(timeout);

    using (var cts = new CancellationTokenSource())
    {
        Task activityTask = context.CallActivityAsync("GetQuote");
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
> 前記の C# の例は Durable Functions 2.x をターゲットにしています。 Durable Functions 1.x の場合、`IDurableOrchestrationContext` の代わりに `DurableOrchestrationContext` を使用する必要があります。 バージョン間の相違点の詳細については、[Durable Functions のバージョン](durable-functions-versions.md)に関する記事を参照してください。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```js
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const deadline = moment.utc(context.df.currentUtcDateTime).add(30, "s");

    const activityTask = context.df.callActivity("GetQuote");
    const timeoutTask = context.df.createTimer(deadline.toDate());

    const winner = yield context.df.Task.any([activityTask, timeoutTask]);
    if (winner === activityTask) {
        // success case
        timeoutTask.cancel();
        return true;
    }
    else
    {
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
    deadline = context.current_utc_datetime + timedelta(seconds=30)
    activity_task = context.call_activity("GetQuote")
    timeout_task = context.create_timer(deadline)

    winner = yield context.task_any([activity_task, timeout_task])
    if winner == activity_task:
        timeout_task.cancel()
        return True
    elif winner == timeout_task:
        return False

main = df.Orchestrator.create(orchestrator_function)
```

---

> [!WARNING]
> コードでタイマーの完了を待たない場合、`CancellationTokenSource` を使用するか (.NET)、返された `TimerTask` で `cancel()` を呼び出し (JavaScript)、持続的タイマーをキャンセルします。 Durable Task Framework では、未処理のタスクすべてが完了するかキャンセルされるまで、オーケストレーションの状態は "完了" に変更されません。

このキャンセル メカニズムは、進行中のアクティビティ関数またはサブオーケストレーションの実行は終了させません。 オーケストレーター関数が、単に結果を無視して、次に進めるようにするだけです。 関数アプリが従量課金プランを使用している場合は、アクティビティ関数を破棄しても、その関数によって消費される時間とメモリはすべて課金対象となります。 既定では、従量課金プランで実行されている関数のタイムアウトは、5 分に設定されています。 この制限を超えると、Azure Functions ホストは、すべての実行を停止し、ランナウェイ課金状況を回避するために、リサイクルされます。 [関数のタイムアウトは構成可能](../functions-host-json.md#functiontimeout)です。

オーケストレーター関数でタイムアウトを実装する方法の詳細な例については、[人による操作とタイムアウト - 電話検証](durable-functions-phone-verification.md)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [外部イベントを発生させて処理する方法を確認する](durable-functions-external-events.md)
