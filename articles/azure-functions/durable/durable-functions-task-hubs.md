---
title: Durable Functions におけるタスク ハブ - Azure
description: Azure Functions の Durable Functions 拡張機能におけるタスク ハブについて説明します。 タスク ハブを構成する方法について説明します。
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2017
ms.author: azfuncdf
ms.openlocfilehash: 596eedab39ff926fcdc880c82c49ac464b7ff23b
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/06/2019
ms.locfileid: "55753472"
---
# <a name="task-hubs-in-durable-functions-azure-functions"></a>Durable Functions におけるタスク ハブ (Azure Functions)

[Durable Functions](durable-functions-overview.md) の*タスク ハブ*は、オーケストレーションに使用される Azure Storage リソースの論理コンテナーです。 オーケストレーター関数とアクティビティ関数は、同じタスク ハブに属しているときに限り、情報をやり取りすることができます。

複数の関数アプリがストレージ アカウントを共有している場合、各関数アプリには個別のタスク ハブ名を構成する*必要があります*。 ストレージ アカウントには、複数のタスク ハブを含めることができます。 次の図は、共有ストレージ アカウントと専用ストレージ アカウントの各関数アプリにタスク ハブが 1 つあることを示しています。

![共有ストレージ アカウントと専用ストレージ アカウントの図](./media/durable-functions-task-hubs/task-hubs-storage.png)

## <a name="azure-storage-resources"></a>Azure Storage のリソース

タスク ハブは次のストレージ リソースから構成されます。

* 1 つまたは複数のコントロールキュー。
* 1 つの作業項目キュー。
* 1 つの履歴テーブル。
* 1 つのインスタンス テーブル。
* Lease Blob を少なくとも 1 つ含んだ 1 つのストレージ コンテナー。

これらすべてのリソースは、オーケストレーター関数またはアクティビティ関数の実行時 (またはスケジュール時) に、既定の Azure ストレージ アカウントに自動的に作成されます。 これらのリソースがどのように使用されるかについては、[パフォーマンスとスケーリング](durable-functions-perf-and-scale.md)に関する記事で説明しています。

## <a name="task-hub-names"></a>タスク ハブ名

次の例に示すように、タスク ハブは *host.json* ファイルに宣言されている名前で識別されます。

### <a name="hostjson-functions-1x"></a>host.json (Functions 1.x)

```json
{
  "durableTask": {
    "hubName": "MyTaskHub"
  }
}
```

### <a name="hostjson-functions-2x"></a>host.json (Functions 2.x)

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": "MyTaskHub"
    }
  }
}
```

次の *host.json* ファイルの例に示すように、タスク ハブはアプリの設定を使用して構成することもできます。

### <a name="hostjson-functions-1x"></a>host.json (Functions 1.x)

```json
{
  "durableTask": {
    "hubName": "%MyTaskHub%"
  }
}
```

### <a name="hostjson-functions-2x"></a>host.json (Functions 2.x)

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": "%MyTaskHub%"
    }
  }
}
```

タスク ハブ名は、`MyTaskHub` アプリ設定の値に設定されます。 次の `local.settings.json` は、`MyTaskHub` 設定を `samplehubname` として定義する方法を示しています。

```json
{
  "IsEncrypted": false,
  "Values": {
    "MyTaskHub" : "samplehubname"
  }
}
```

以下は、アプリ設定として構成されているタスク ハブを操作するために [OrchestrationClientBinding](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationClientAttribute.html) を使用する関数を記述する方法の、プリコンパイル済み C# の場合の例を示しています。

```csharp
[FunctionName("HttpStart")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}")] HttpRequestMessage req,
    [OrchestrationClient(TaskHub = "%MyTaskHub%")] DurableOrchestrationClientBase starter,
    string functionName,
    ILogger log)
{
    // Function input comes from the request content.
    dynamic eventData = await req.Content.ReadAsAsync<object>();
    string instanceId = await starter.StartNewAsync(functionName, eventData);

    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");

    return starter.CreateCheckStatusResponse(req, instanceId);
}
```

また、以下は JavaScript に必要な構成を示しています。 `function.json` ファイルのタスク ハブ プロパティは、アプリ設定を通じて設定されます。

```json
{
    "name": "input",
    "taskHub": "%MyTaskHub%",
    "type": "orchestrationClient",
    "direction": "in"
}
```

タスク ハブの名前は、先頭文字をアルファベットとする必要があります。また、使用できるのはアルファベットと数値だけです。 指定されていない場合、既定の名前は **DurableFunctionsHub** です。

> [!NOTE]
> この名前は共有ストレージ アカウント内に複数のタスク ハブがある場合に、それぞれのタスク ハブを区別するものです。 共有ストレージ アカウントを共有する関数アプリが複数ある場合、*host.json* ファイルでタスク ハブごとに異なる名前を明示的に構成する必要があります。 このようにしないと、複数の関数アプリで他の関数アプリとのメッセージに関する競合が発生し、それが原因で未定義の動作が発生する可能性があります。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [バージョン管理の方法](durable-functions-versioning.md)
