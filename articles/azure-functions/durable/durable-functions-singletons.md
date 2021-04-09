---
title: Durable Functions のシングルトン - Azure
description: Azure Functions の Durable Functons 拡張機能でシングルトンを使用する方法。
author: cgillum
ms.topic: conceptual
ms.date: 09/10/2020
ms.author: azfuncdf
ms.openlocfilehash: 50ed473d61dff19f41f77a79513c0ddab521e56f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91325744"
---
# <a name="singleton-orchestrators-in-durable-functions-azure-functions"></a>Durable Functions のシングルトン オーケストレーター (Azure Functions)

バックグラウンド ジョブでは、多くの場合、特定のオーケストレーターのインスタンスが一度に 1 つだけ実行されることを保証する必要があります。 特定のインスタンス ID を作成時にオーケストレーターに割り当てて、[Durable Functions](durable-functions-overview.md) でこのようなシングルトン動作を確実に行うことができます。

## <a name="singleton-example"></a>シングルトンの例

次の例は、シングルトン バックグラウンド ジョブのオーケストレーションを作成する HTTP トリガー関数を示しています。 このコードは、指定されたインスタンス ID の インスタンスが 1 つだけ存在することを保証します。

# <a name="c"></a>[C#](#tab/csharp)

```cs
[FunctionName("HttpStartSingle")]
public static async Task<HttpResponseMessage> RunSingle(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}/{instanceId}")] HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient starter,
    string functionName,
    string instanceId,
    ILogger log)
{
    // Check if an instance with the specified ID already exists or an existing one stopped running(completed/failed/terminated).
    var existingInstance = await starter.GetStatusAsync(instanceId);
    if (existingInstance == null 
    || existingInstance.RuntimeStatus == OrchestrationRuntimeStatus.Completed 
    || existingInstance.RuntimeStatus == OrchestrationRuntimeStatus.Failed 
    || existingInstance.RuntimeStatus == OrchestrationRuntimeStatus.Terminated)
    {
        // An instance with the specified ID doesn't exist or an existing one stopped running, create one.
        dynamic eventData = await req.Content.ReadAsAsync<object>();
        await starter.StartNewAsync(functionName, instanceId, eventData);
        log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
        return starter.CreateCheckStatusResponse(req, instanceId);
    }
    else
    {
        // An instance with the specified ID exists or an existing one still running, don't create one.
        return new HttpResponseMessage(HttpStatusCode.Conflict)
        {
            Content = new StringContent($"An instance with ID '{instanceId}' already exists."),
        };
    }
}
```

> [!NOTE]
> 前記の C# コードは Durable Functions 2.x 用です。 Durable Functions 1.x では、`DurableClient` 属性の代わりに `OrchestrationClient` 属性を使用する必要があります。また、`IDurableOrchestrationClient` ではなく `DurableOrchestrationClient` パラメーター型を使用する必要があります。 バージョン間の相違点の詳細については、[Durable Functions のバージョン](durable-functions-versions.md)に関する記事を参照してください。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

**function.json**

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "route": "orchestrators/{functionName}/{instanceId}",
      "methods": ["post"]
    },
    {
      "name": "starter",
      "type": "orchestrationClient",
      "direction": "in"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ]
}
```

**index.js**

```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    const instanceId = req.params.instanceId;
    const functionName = req.params.functionName;

    // Check if an instance with the specified ID already exists or an existing one stopped running(completed/failed/terminated).
    const existingInstance = await client.getStatus(instanceId);
    if (!existingInstance 
        || existingInstance.runtimeStatus == "Completed" 
        || existingInstance.runtimeStatus == "Failed" 
        || existingInstance.runtimeStatus == "Terminated") {
        // An instance with the specified ID doesn't exist or an existing one stopped running, create one.
        const eventData = req.body;
        await client.startNew(functionName, instanceId, eventData);
        context.log(`Started orchestration with ID = '${instanceId}'.`);
        return client.createCheckStatusResponse(req, instanceId);
    } else {
        // An instance with the specified ID exists or an existing one still running, don't create one.
        return {
            status: 409,
            body: `An instance with ID '${instanceId}' already exists.`,
        };
    }
};
```

# <a name="python"></a>[Python](#tab/python)

**function.json**

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "route": "orchestrators/{functionName}/{instanceId}",
      "methods": ["post"]
    },
    {
      "name": "starter",
      "type": "orchestrationClient",
      "direction": "in"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ]
}
```

**__init__.py**

```python
import logging
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)
    instance_id = req.route_params['instanceId']
    function_name = req.route_params['functionName']

    existing_instance = await client.get_status(instance_id)

    if existing_instance != None or existing_instance.runtime_status in ["Completed", "Failed", "Terminated"]:
        event_data = req.get_body()
        instance_id = await client.start_new(function_name, instance_id, event_data)
        logging.info(f"Started orchestration with ID = '{instance_id}'.")
        return client.create_check_status_response(req, instance_id)
    else:
        return {
            'status': 409,
            'body': f"An instance with ID '${instance_id}' already exists"
        }

```

---

既定では、インスタンス ID は、ランダムに生成される GUID です。 ただし、前記の例では、URL からルート データにインスタンス ID が渡されています。 このコードは、`GetStatusAsync` (C#)、`getStatus` (JavaScript)、または `get_status` (Python) を呼び出して、指定した ID を持つインスタンスが既に実行されているかどうかを確認します。 そのようなインスタンスが実行されていない場合は、その ID で新しいインスタンスが作成されます。

> [!NOTE]
> このサンプルには潜在的な競合状態があります。 **HttpStartSingle** の 2 つのインスタンスが同時に実行された場合、両方の関数呼び出しで成功が報告されますが、実際に開始するのは 1 つのオーケストレーション インスタンスだけです。 これは、要件によっては望ましくない副作用になる可能性があります。 このため、このトリガー関数を同時に 2 つの要求で実行できないようにすることが重要です。

オーケストレーター関数の実装の詳細は、実際は問題ではありません。 開始して完了する通常のオーケストレーター関数でも、永久に実行される関数 (つまり、[永続的オーケストレーション](durable-functions-eternal-orchestrations.md)) でも可能です。 重要な点は、インスタンスが 1 度に 1 つだけ実行されていることです。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [オーケストレーションのネイティブ HTTP 機能について学習する](durable-functions-http-features.md)
