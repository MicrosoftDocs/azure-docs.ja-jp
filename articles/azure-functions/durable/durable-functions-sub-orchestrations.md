---
title: Durable Functions のサブオーケストレーション - Azure
description: Azure Functions の拡張機能である Durable Functions のオーケストレーションからオーケストレーションを呼び出す方法。
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 868efad58e14fd817729f0aa9ac785bc0f960867
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70087027"
---
# <a name="sub-orchestrations-in-durable-functions-azure-functions"></a>Durable Functions (Azure Functions) でのサブオーケストレーション

オーケストレーター関数はアクティビティ関数を呼び出すだけでなく、別のオーケストレーター関数を呼び出すことができます。 たとえば、オーケストレーター関数のライブラリから大規模なオーケストレーションを作成できます。 またはオーケストレーター関数の複数のインスタンスを並列で実行できます。

オーケストレーター関数は、.NET で [CallSubOrchestratorAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallSubOrchestratorAsync_) メソッドまたは [CallSubOrchestratorWithRetryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallSubOrchestratorWithRetryAsync_) メソッド、または JavaScript で `callSubOrchestrator` または `callSubOrchestratorWithRetry` メソッドを呼び出して別のオーケストレーター関数を呼び出すことができます。 [エラー処理と補正](durable-functions-error-handling.md#automatic-retry-on-failure)の記事で自動再試行の詳細について説明しています。

呼び出し元から見ると、サブオーケストレーター関数はアクティビティ関数と同じように動作します。 それらは値を返したり、例外をスローしたり、親のオーケストレーター関数によって待機させたりすることができます。

## <a name="example"></a>例

次の例では、プロビジョニングが必要な複数のデバイスがある IoT ("モノのインターネット") シナリオについて説明しています。 それぞれのデバイスに対して必要となる特定のオーケストレーションがあり、それは次のようになる場合があります。

### <a name="c"></a>C#

```csharp
public static async Task DeviceProvisioningOrchestration(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    string deviceId = context.GetInput<string>();

    // Step 1: Create an installation package in blob storage and return a SAS URL.
    Uri sasUrl = await context.CallActivityAsync<Uri>("CreateInstallationPackage", deviceId);

    // Step 2: Notify the device that the installation package is ready.
    await context.CallActivityAsync("SendPackageUrlToDevice", Tuple.Create(deviceId, sasUrl));

    // Step 3: Wait for the device to acknowledge that it has downloaded the new package.
    await context.WaitForExternalEvent<bool>("DownloadCompletedAck");

    // Step 4: ...
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (Functions 2.x のみ)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const deviceId = context.df.getInput();

    // Step 1: Create an installation package in blob storage and return a SAS URL.
    const sasUrl = yield context.df.callActivity("CreateInstallationPackage", deviceId);

    // Step 2: Notify the device that the installation package is ready.
    yield context.df.callActivity("SendPackageUrlToDevice", { id: deviceId, url: sasUrl });

    // Step 3: Wait for the device to acknowledge that it has downloaded the new package.
    yield context.df.waitForExternalEvent("DownloadCompletedAck");

    // Step 4: ...
});
```

このオーケストレーター関数は、そのまま一回限りのデバイス プロビジョニングに使用するか、または大規模なオーケストレーションに組み込むことができます。 後者の場合、親のオーケストレーター関数は `CallSubOrchestratorAsync` (C#) または `callSubOrchestrator` (JavaScript) API を使用して `DeviceProvisioningOrchestration` のインスタンスをスケジュールできます。

複数のオーケストレーター関数を並列で実行する方法を次に示します。

### <a name="c"></a>C#

```csharp
[FunctionName("ProvisionNewDevices")]
public static async Task ProvisionNewDevices(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    string[] deviceIds = await context.CallActivityAsync<string[]>("GetNewDeviceIds");

    // Run multiple device provisioning flows in parallel
    var provisioningTasks = new List<Task>();
    foreach (string deviceId in deviceIds)
    {
        Task provisionTask = context.CallSubOrchestratorAsync("DeviceProvisioningOrchestration", deviceId);
        provisioningTasks.Add(provisionTask);
    }

    await Task.WhenAll(provisioningTasks);

    // ...
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (Functions 2.x のみ)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const deviceIds = yield context.df.callActivity("GetNewDeviceIds");

    // Run multiple device provisioning flows in parallel
    const provisioningTasks = [];
    for (const deviceId of deviceIds) {
        const provisionTask = context.df.callSubOrchestrator("DeviceProvisioningOrchestration", deviceId);
        provisioningTasks.push(provisionTask);
    }

    yield context.df.Task.all(provisioningTasks);

    // ...
});
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [タスク ハブとその構成方法について学習する](durable-functions-task-hubs.md)
