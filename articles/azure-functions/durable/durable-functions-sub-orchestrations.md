---
title: Durable Functions のサブオーケストレーション - Azure
description: Azure Functions の拡張機能である Durable Functions のオーケストレーションからオーケストレーションを呼び出す方法。
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 5625bc2ddfa4b6f527ca16f19f33d257a1834d4b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "85340820"
---
# <a name="sub-orchestrations-in-durable-functions-azure-functions"></a>Durable Functions (Azure Functions) でのサブオーケストレーション

オーケストレーター関数はアクティビティ関数を呼び出すだけでなく、別のオーケストレーター関数を呼び出すことができます。 たとえば、小規模なオーケストレーター関数のライブラリから大規模なオーケストレーションを作成できます。 またはオーケストレーター関数の複数のインスタンスを並列で実行できます。

オーケストレーター関数からは、.NET の `CallSubOrchestratorAsync` または `CallSubOrchestratorWithRetryAsync` メソッド、または JavaScript の `callSubOrchestrator` または `callSubOrchestratorWithRetry` メソッドを使用して、別のオーケストレーター関数を呼び出すことができます。 [エラー処理と補正](durable-functions-error-handling.md#automatic-retry-on-failure)の記事で自動再試行の詳細について説明しています。

呼び出し元から見ると、サブオーケストレーター関数はアクティビティ関数と同じように動作します。 それらは値を返したり、例外をスローしたり、親のオーケストレーター関数によって待機させたりすることができます。 

> [!NOTE]
> サブオーケストレーションは現在、.NET と JavaScript でサポートされています。

## <a name="example"></a>例

次の例では、プロビジョニングが必要な複数のデバイスがある IoT ("モノのインターネット") シナリオについて説明しています。 次の関数は、各デバイスに対して実行する必要があるプロビジョニング ワークフローを表します。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
public static async Task DeviceProvisioningOrchestration(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

---

このオーケストレーター関数は、そのまま一回限りのデバイス プロビジョニングに使用するか、または大規模なオーケストレーションに組み込むことができます。 後者の場合、親のオーケストレーター関数は `CallSubOrchestratorAsync` (.NET) または `callSubOrchestrator` (JavaScript) API を使用して `DeviceProvisioningOrchestration` のインスタンスをスケジュールできます。

複数のオーケストレーター関数を並列で実行する方法を次に示します。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("ProvisionNewDevices")]
public static async Task ProvisionNewDevices(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
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

> [!NOTE]
> 前述の C# の例は Durable Functions 2.x 用です。 Durable Functions 1.x の場合、`IDurableOrchestrationContext` の代わりに `DurableOrchestrationContext` を使用する必要があります。 バージョン間の相違点の詳細については、[Durable Functions のバージョン](durable-functions-versions.md)に関する記事を参照してください。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const deviceIds = yield context.df.callActivity("GetNewDeviceIds");

    // Run multiple device provisioning flows in parallel
    const provisioningTasks = [];
    var id = 0;
    for (const deviceId of deviceIds) {
        const child_id = context.df.instanceId+`:${id}`;
        const provisionTask = context.df.callSubOrchestrator("DeviceProvisioningOrchestration", deviceId, child_id);
        provisioningTasks.push(provisionTask);
        id++;
    }

    yield context.df.Task.all(provisioningTasks);

    // ...
});
```

---

> [!NOTE]
> 親オーケストレーションと同じ関数アプリでサブオーケストレーションを定義する必要があります。 別の関数アプリでオーケストレーションを呼び出して待機する必要がある場合は、HTTP API の組み込みサポートと HTTP 202 ポーリング コンシューマー パターンの使用を検討してください。 詳細については、「[HTTP 機能](durable-functions-http-features.md)」トピックを参照してください。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [カスタム オーケストレーションの状態を設定する方法を学習します](durable-functions-custom-orchestration-status.md)
