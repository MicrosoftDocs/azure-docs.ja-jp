---
title: Durable Functions のシングルトン - Azure
description: Azure Functions の Durable Functons 拡張機能でシングルトンを使用する方法。
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: aca7aa30744c79cefd3c7704a8fde1df203b2c9d
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/10/2019
ms.locfileid: "54198985"
---
# <a name="singleton-orchestrators-in-durable-functions-azure-functions"></a>Durable Functions のシングルトン オーケストレーター (Azure Functions)

バックグラウンド ジョブでは、多くの場合、特定のオーケストレーターのインスタンスが 1 度に 1 つだけ実行されることを保証する必要があります。 これは、[Durable Functions](durable-functions-overview.md) でオーケストレーターを作成するときに、特定のインスタンス ID を割り当てることによって実現できます。

## <a name="singleton-example"></a>シングルトンの例

次の C# および JavaScript の例は、シングルトン バックグラウンド ジョブのオーケストレーションを作成する HTTP トリガー関数を示しています。 このコードは、指定されたインスタンス ID の インスタンスが 1 つだけ存在することを保証します。

### <a name="c"></a>C#

```cs
[FunctionName("HttpStartSingle")]
public static async Task<HttpResponseMessage> RunSingle(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}/{instanceId}")] HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient starter,
    string functionName,
    string instanceId,
    ILogger log)
{
    // Check if an instance with the specified ID already exists.
    var existingInstance = await starter.GetStatusAsync(instanceId);
    if (existingInstance == null)
    {
        // An instance with the specified ID doesn't exist, create one.
        dynamic eventData = await req.Content.ReadAsAsync<object>();
        await starter.StartNewAsync(functionName, instanceId, eventData);
        log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
        return starter.CreateCheckStatusResponse(req, instanceId);
    }
    else
    {
        // An instance with the specified ID exists, don't create one.
        return req.CreateErrorResponse(
            HttpStatusCode.Conflict,
            $"An instance with ID '{instanceId}' already exists.");
    }
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (Functions 2.x のみ)

```javascript
const df = require("durable-functions");

modules.exports = async function(context, req) {
    const client = df.getClient(context);

    const instanceId = req.params.instanceId;
    const functionName = req.params.functionsName;

    // Check if an instance with the specified ID already exists.
    const existingInstance = await client.getStatus(instanceId);
    if (!existingInstance) {
        // An instance with the specified ID doesn't exist, create one.
        const eventData = req.body;
        await client.startNew(functionName, instanceId, eventData);
        context.log(`Started orchestration with ID = '${instanceId}'.`);
        return client.createCheckStatusResponse(req, instanceId);
    } else {
        // An instance with the specified ID exists, don't create one.
        return {
            status: 409,
            body: `An instance with ID '${instanceId}' already exists.`,
        };
    }
};
```

既定では、インスタンス ID は、ランダムに生成される GUID です。 ただし、ここでは、インスタンス ID は URL からルート データに渡されます。 コードは、[GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_GetStatusAsync_) (C#) または `getStatus` (JavaScript) を呼び出して、指定した ID を持つインスタンスが既に実行されているかどうかを確認します。 実行されていなければ、インスタンスがその ID で作成されます。

> [!WARNING]
> JavaScript でローカルに開発する場合は、環境変数 `WEBSITE_HOSTNAME` を `localhost:<port>` に設定する必要があります。たとえば、 `DurableOrchestrationClient` のメソッドを使用するには、`localhost:7071` に設定します。 この要件の詳細については、[GitHub の問題](https://github.com/Azure/azure-functions-durable-js/issues/28)に関するページを参照してください。

> [!NOTE]
> このサンプルには潜在的な競合状態があります。 **HttpStartSingle** の 2 つのインスタンスが同時に実行された場合、両方の関数呼び出しで成功が報告されますが、実際に開始するのは 1 つのオーケストレーション インスタンスだけです。 これは、要件によっては望ましくない副作用になる可能性があります。 このため、このトリガー関数を同時に 2 つの要求で実行できないようにすることが重要です。

オーケストレーター関数の 実装の詳細は、実際は問題ではありません。 開始して完了する通常のオーケストレーター関数でも、永久に実行される関数 (つまり、[永続的オーケストレーション](durable-functions-eternal-orchestrations.md)) でも可能です。 重要な点は、インスタンスが 1 度に 1 つだけ実行されていることです。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [サブオーケストレーションを呼び出す方法を知る](durable-functions-sub-orchestrations.md)
