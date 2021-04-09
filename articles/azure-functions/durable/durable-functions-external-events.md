---
title: Durable Functions での外部イベントの処理 - Azure
description: Azure Functions の Durable Functions 拡張機能で外部イベントを処理する方法について説明します。
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: azfuncdf
ms.openlocfilehash: c08306edcea02a9207ab5a15eb62b7fffc2ecb44
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99576331"
---
# <a name="handling-external-events-in-durable-functions-azure-functions"></a>Durable Functions での外部イベントの処理 (Azure Functions)

オーケストレーター関数には、外部イベントを待ち受ける機能があります。 [Durable Functions](durable-functions-overview.md) のこの機能は、多くの場合、人による操作または他の外部トリガーを処理するときに便利です。

> [!NOTE]
> 外部イベントは、一方向の非同期操作です。 それらは、イベントを送信するクライアントが、オーケストレーター関数からの同期応答を必要とする状況には適していません。

## <a name="wait-for-events"></a>イベントを待つ

[オーケストレーション トリガー バインド](durable-functions-bindings.md#orchestration-trigger)の [WaitForExternalEvent](/dotnet/api/microsoft.azure.webjobs.durableorchestrationcontextbase.waitforexternalevent?view=azure-dotnet-legacy) (.NET)、`waitForExternalEvent` (JavaScript)、および `wait_for_external_event` (Python) メソッドを使うと、オーケストレーター関数では外部イベントを非同期に待機してリッスンできるようになります。 リスニング オーケストレーター関数は、受信するイベントの "*名前*" と "*データのシェイプ*" を宣言します。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("BudgetApproval")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool approved = await context.WaitForExternalEvent<bool>("Approval");
    if (approved)
    {
        // approval granted - do the approved action
    }
    else
    {
        // approval denied - send a notification
    }
}
```

> [!NOTE]
> 前記の C# コードは Durable Functions 2.x 用です。 Durable Functions 1.x の場合、`IDurableOrchestrationContext` の代わりに `DurableOrchestrationContext` を使用する必要があります。 バージョン間の相違点の詳細については、[Durable Functions のバージョン](durable-functions-versions.md)に関する記事を参照してください。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const approved = yield context.df.waitForExternalEvent("Approval");
    if (approved) {
        // approval granted - do the approved action
    } else {
        // approval denied - send a notification
    }
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    approved = context.wait_for_external_event('Approval')
    if approved:
        # approval granted - do the approved action
    else:
        # approval denied - send a notification

main = df.Orchestrator.create(orchestrator_function)
```

---

前の例では、特定の 1 つのイベントをリッスンし、受信したときにアクションを実行します。

次の例に示すように、複数のイベントを同時にリッスンできます。この例では、発生する可能性がある 3 つのイベント通知のいずれかを待っています。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("Select")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var event1 = context.WaitForExternalEvent<float>("Event1");
    var event2 = context.WaitForExternalEvent<bool>("Event2");
    var event3 = context.WaitForExternalEvent<int>("Event3");

    var winner = await Task.WhenAny(event1, event2, event3);
    if (winner == event1)
    {
        // ...
    }
    else if (winner == event2)
    {
        // ...
    }
    else if (winner == event3)
    {
        // ...
    }
}
```

> [!NOTE]
> 前記の C# コードは Durable Functions 2.x 用です。 Durable Functions 1.x の場合、`IDurableOrchestrationContext` の代わりに `DurableOrchestrationContext` を使用する必要があります。 バージョン間の相違点の詳細については、[Durable Functions のバージョン](durable-functions-versions.md)に関する記事を参照してください。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const event1 = context.df.waitForExternalEvent("Event1");
    const event2 = context.df.waitForExternalEvent("Event2");
    const event3 = context.df.waitForExternalEvent("Event3");

    const winner = yield context.df.Task.any([event1, event2, event3]);
    if (winner === event1) {
        // ...
    } else if (winner === event2) {
        // ...
    } else if (winner === event3) {
        // ...
    }
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    event1 = context.wait_for_external_event('Event1')
    event2 = context.wait_for_external_event('Event2')
    event3 = context.wait_for_external_event('Event3')

    winner = context.task_any([event1, event2, event3])
    if winner == event1:
        # ...
    elif winner == event2:
        # ...
    elif winner == event3:
        # ...

main = df.Orchestrator.create(orchestrator_function)
```

---

前の例では、複数のイベントの "*いずれか*" をリッスンします。 "*すべて*" のイベントを待つこともできます。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("NewBuildingPermit")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string applicationId = context.GetInput<string>();

    var gate1 = context.WaitForExternalEvent("CityPlanningApproval");
    var gate2 = context.WaitForExternalEvent("FireDeptApproval");
    var gate3 = context.WaitForExternalEvent("BuildingDeptApproval");

    // all three departments must grant approval before a permit can be issued
    await Task.WhenAll(gate1, gate2, gate3);

    await context.CallActivityAsync("IssueBuildingPermit", applicationId);
}
```

> [!NOTE]
> 前のコードは Durable Functions 2.x 用です。 Durable Functions 1.x の場合、`IDurableOrchestrationContext` の代わりに `DurableOrchestrationContext` を使用する必要があります。 バージョン間の相違点の詳細については、[Durable Functions のバージョン](durable-functions-versions.md)に関する記事を参照してください。

.NET では、イベント ペイロードを想定された型 `T` に変換できない場合、例外がスローされます。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const applicationId = context.df.getInput();

    const gate1 = context.df.waitForExternalEvent("CityPlanningApproval");
    const gate2 = context.df.waitForExternalEvent("FireDeptApproval");
    const gate3 = context.df.waitForExternalEvent("BuildingDeptApproval");

    // all three departments must grant approval before a permit can be issued
    yield context.df.Task.all([gate1, gate2, gate3]);

    yield context.df.callActivity("IssueBuildingPermit", applicationId);
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    application_id = context.get_input()
    
    gate1 = context.wait_for_external_event('CityPlanningApproval')
    gate2 = context.wait_for_external_event('FireDeptApproval')
    gate3 = context.wait_for_external_event('BuildingDeptApproval')

    yield context.task_all([gate1, gate2, gate3])
    yield context.call_activity('IssueBuildingPermit', application_id)

main = df.Orchestrator.create(orchestrator_function)
```

---

`WaitForExternalEvent` では、入力を無期限に待機します。  関数アプリは、待機中に安全にアンロードすることができます。 これは、このオーケストレーション インスタンスのイベントが到着すると、自動的に起動され、すぐにそのイベントを処理します。

> [!NOTE]
> 関数アプリで従量課金プランを使用する場合、オーケストレーター関数が `WaitForExternalEvent` (.NET)、`waitForExternalEvent` (JavaScript)、 または `wait_for_external_event` (Python) からのタスクを待っている間は、待ち時間がどんなに長くなっても課金されません。

## <a name="send-events"></a>送信イベント

外部のイベントは、[RaiseEventAsync](/dotnet/api/microsoft.azure.webjobs.durableorchestrationclientbase.raiseeventasync?view=azure-dotnet-legacy) (.NET) メソッドまたは `raiseEventAsync` (JavaScript) メソッドを使用してオーケストレーションに送信できます。 これらのメソッドは、[オーケストレーション クライアント](durable-functions-bindings.md#orchestration-client) バインディングによって公開されています。 組み込みの [raise event HTTP API](durable-functions-http-api.md#raise-event) を使用して、外部のイベントをオーケストレーションに送信することもできます。

生成されたイベントには、*instance ID*、*eventName*、*eventData* パラメーターとして含まれます。 オーケストレーター関数は、`WaitForExternalEvent` (.NET) API または `waitForExternalEvent` (JavaScript) API を使用してそれらのイベントを処理します。 イベントが処理されるためには、送信側と受信側の両方で、*eventName* が一致している必要があります。 また、イベント データは、JSON でシリアル化できる必要があります。

内部的には、"raise event" のメカニズムが、待機オーケストレーター関数によって取得されるメッセージをエンキューします。 指定した "*イベント名*" でインスタンスが待機していない場合、イベント メッセージがインメモリ キューに追加されます。 オーケストレーション インスタンスが後でその "*イベント名*" のリッスンを開始した場合、キューにイベント メッセージがあるかどうかを確認します。

> [!NOTE]
> 指定した "*インスタンス ID*" のオーケストレーション インスタンスが存在しない場合、イベント メッセージは破棄されます。

次の例は、キューによってトリガーされる関数で、"承認" イベントをオーケストレーター関数インスタンスに送信します。 オーケストレーション インスタンス ID は、キュー メッセージの本文から取得されます。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("ApprovalQueueProcessor")]
public static async Task Run(
    [QueueTrigger("approval-queue")] string instanceId,
    [DurableClient] IDurableOrchestrationClient client)
{
    await client.RaiseEventAsync(instanceId, "Approval", true);
}
```

> [!NOTE]
> 前記の C# コードは Durable Functions 2.x 用です。 Durable Functions 1.x では、`DurableClient` 属性の代わりに `OrchestrationClient` 属性を使用する必要があります。また、`IDurableOrchestrationClient` ではなく `DurableOrchestrationClient` パラメーター型を使用する必要があります。 バージョン間の相違点の詳細については、[Durable Functions のバージョン](durable-functions-versions.md)に関する記事を参照してください。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);
    await client.raiseEvent(instanceId, "Approval", true);
};
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

async def main(instance_id:str, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)
    await client.raise_event(instance_id, 'Approval', True)
```

---

内部的には、`RaiseEventAsync` (.NET)、`raiseEvent` (JavaScript)、または `raise_event` (Python) は、待機オーケストレーター関数によって取得されるメッセージをエンキューします。 指定した "*イベント名*" でインスタンスが待機していない場合、イベント メッセージがインメモリ キューに追加されます。 オーケストレーション インスタンスが後でその "*イベント名*" のリッスンを開始した場合、キューにイベント メッセージがあるかどうかを確認します。

> [!NOTE]
> 指定した "*インスタンス ID*" のオーケストレーション インスタンスが存在しない場合、イベント メッセージは破棄されます。

### <a name="http"></a>HTTP

次に示したのは、オーケストレーション インスタンスに "Approval" イベントを発生させる HTTP 要求の例です。 

```http
POST /runtime/webhooks/durabletask/instances/MyInstanceId/raiseEvent/Approval&code=XXX
Content-Type: application/json

"true"
```

このケースでは、インスタンス ID が *MyInstanceId* としてハードコーディングされています。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [エラー処理の実装方法を学習する](durable-functions-error-handling.md)

> [!div class="nextstepaction"]
> [人による操作を待つサンプルを実行する](durable-functions-phone-verification.md)