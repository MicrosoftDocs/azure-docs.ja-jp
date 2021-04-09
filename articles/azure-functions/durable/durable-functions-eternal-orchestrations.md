---
title: Durable Functions での永続的オーケストレーション - Azure
description: Azure Functions の Durable Functions 拡張機能を使用して永続的オーケストレーションを実装する方法について説明します。
author: cgillum
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: azfuncdf
ms.openlocfilehash: 82108ae0b2cabaab6dfa47c8bb5e893a44df38af
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102182062"
---
# <a name="eternal-orchestrations-in-durable-functions-azure-functions"></a>Durable Functions での永続的オーケストレーション (Azure Functions)

"*永続的オーケストレーション*" は、終了しないオーケストレーター関数です。 これは、アグリゲーター、無限ループを必要とする任意のシナリオに対して、[Durable Functions](durable-functions-overview.md) を使用する必要があるときに便利です。

## <a name="orchestration-history"></a>オーケストレーションの履歴

[オーケストレーションの履歴](durable-functions-orchestrations.md#orchestration-history)に関するページで説明したように、Durable Task Framework では、各関数オーケストレーションの履歴が追跡されます。 この履歴は、オーケストレーター関数が新しい作業をスケジュールする限り、拡大し続けます。 オーケストレーター関数が無限ループに入り、作業を継続的にスケジュールすると、この履歴は巨大になり、パフォーマンスが大幅に低下する可能性があります。 "*永続的オーケストレーション*" の概念は、無限ループを必要とするアプリケーションのこうした問題を軽減することを目的としています。

## <a name="resetting-and-restarting"></a>リセットと再開

オーケストレーター関数は、無限ループを使用せずに、[オーケストレーション トリガーのバインド](durable-functions-bindings.md#orchestration-trigger)の `ContinueAsNew` (.NET)、`continueAsNew` (JavaScript)、または `continue_as_new` (Python) メソッドを使用してその状態をリセットします。 このメソッドでは、JSON でシリアル化できる 1 つのパラメーターが使用され、このパラメーターが、次のオーケストレーター関数生成に対する新しい入力になります。

`ContinueAsNew` が呼び出されると、インスタンスは、終了前に、自身に対してメッセージをエンキューします。 メッセージは、新しい入力値でインスタンスを再開します。 同じインスタンス ID が保持されますが、オーケストレーター関数の履歴は効果的に切り詰められます。

> [!NOTE]
> Durable Task Framework では同じインスタンス ID が維持されますが、内部的には、`ContinueAsNew` によってリセットされるオーケストレーター関数に対して、新しい "*実行 ID*" が作成されます。 一般的に、この実行 ID は外部に公開されませんが、オーケストレーション実行のデバッグのタイミングを確認するときに役に立つ場合があります。

## <a name="periodic-work-example"></a>定期的な作業の例

永続的オーケストレーションのユース ケースの 1 つが、定期的な作業を無期限に実行する必要があるコードです。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("Periodic_Cleanup_Loop")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    await context.CallActivityAsync("DoCleanup", null);

    // sleep for one hour between cleanups
    DateTime nextCleanup = context.CurrentUtcDateTime.AddHours(1);
    await context.CreateTimer(nextCleanup, CancellationToken.None);

    context.ContinueAsNew(null);
}
```

> [!NOTE]
> 前記の C# の例は Durable Functions 2.x 用です。 Durable Functions 1.x の場合、`IDurableOrchestrationContext` の代わりに `DurableOrchestrationContext` を使用する必要があります。 バージョン間の相違点の詳細については、[Durable Functions のバージョン](durable-functions-versions.md)に関する記事を参照してください。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    yield context.df.callActivity("DoCleanup");

    // sleep for one hour between cleanups
    const nextCleanup = moment.utc(context.df.currentUtcDateTime).add(1, "h");
    yield context.df.createTimer(nextCleanup.toDate());

    yield context.df.continueAsNew(undefined);
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df
from datetime import datetime, timedelta

def orchestrator_function(context: df.DurableOrchestrationContext):
    yield context.call_activity("DoCleanup")

    # sleep for one hour between cleanups
    next_cleanup = context.current_utc_datetime + timedelta(hours = 1)
    yield context.create_timer(next_cleanup)

    context.continue_as_new(None)

main = df.Orchestrator.create(orchestrator_function)
```

---

この例と、タイマーによってトリガーされる関数の違いは、クリーンアップ トリガーのタイミングです。この例のタイミングはスケジュールに基づいていません。 たとえば、1 時間ごとに関数を実行する CRON スケジュールでは、1:00、2:00、3:00 といったタイミングで関数が実行され、重複の問題が発生する可能性があります。 この例では、クリーンアップ所要時間が 30 分の場合は、1:00、2:30、4:00 にスケジュールされるため、重複することはありません。

## <a name="starting-an-eternal-orchestration"></a>永続的オーケストレーションの開始

永続的オーケストレーションを開始するには、他のオーケストレーション関数と同様に、`StartNewAsync` (.NET)、`startNew` (JavaScript)、または `start_new` (Python) メソッドを使用します。  

> [!NOTE]
> 単一の永続的オーケストレーションを確実に実行するには、オーケストレーションの開始時に同じインスタンス `id` を維持することが重要です。 詳しくは、[インスタンス管理](durable-functions-instance-management.md)に関する記事をご覧ください。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("Trigger_Eternal_Orchestration")]
public static async Task<HttpResponseMessage> OrchestrationTrigger(
    [HttpTrigger(AuthorizationLevel.Function, "post", Route = null)] HttpRequestMessage request,
    [DurableClient] IDurableOrchestrationClient client)
{
    string instanceId = "StaticId";

    await client.StartNewAsync("Periodic_Cleanup_Loop", instanceId); 
    return client.CreateCheckStatusResponse(request, instanceId);
}
```

> [!NOTE]
> 前のコードは Durable Functions 2.x 用です。 Durable Functions 1.x では、`DurableClient` 属性の代わりに `OrchestrationClient` 属性を使用する必要があります。また、`IDurableOrchestrationClient` ではなく `DurableOrchestrationClient` パラメーター型を使用する必要があります。 バージョン間の相違点の詳細については、[Durable Functions のバージョン](durable-functions-versions.md)に関する記事を参照してください。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context, req) {
    const client = df.getClient(context);
    const instanceId = "StaticId";
    
    // null is used as the input, since there is no input in "Periodic_Cleanup_Loop".
    await client.startNew("Periodic_Cleanup_Loop", instanceId, null);

    context.log(`Started orchestration with ID = '${instanceId}'.`);
    return client.createCheckStatusResponse(context.bindingData.req, instanceId);
};
```
# <a name="python"></a>[Python](#tab/python)

```python
async def main(req: func.HttpRequest, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)
    instance_id = 'StaticId'

    await client.start_new('Periodic_Cleanup_Loop', instance_id, None)

    logging.info(f"Started orchestration with ID = '{instance_id}'.")
    return client.create_check_status_response(req, instance_id)

```

---

## <a name="exit-from-an-eternal-orchestration"></a>永続的オーケストレーションの終了

オーケストレーター関数は、最終的に完了する必要がある場合は、`ContinueAsNew` を "*呼び出さない*" でください。呼び出さなければ、関数は終了します。

オーケストレーター関数が無限ループにあり、停止する必要がある場合は、[オーケストレーション クライアントのバインド](durable-functions-bindings.md#orchestration-client)の `TerminateAsync` (.NET)、`terminate` (JavaScript)、または `terminate` (Python) メソッドを使用してそれを停止します。 詳しくは、[インスタンス管理](durable-functions-instance-management.md)に関する記事をご覧ください。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [単一のオーケストレーションを実装する方法を確認する](durable-functions-singletons.md)
