---
title: Durable Functions におけるタスク ハブ - Azure
description: Azure Functions の Durable Functions 拡張機能におけるタスク ハブについて説明します。 タスク ハブを構成する方法について説明します。
author: cgillum
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: azfuncdf
ms.openlocfilehash: 26234039c77601bc1d29beeebd3fcb8461d6d6c9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96009519"
---
# <a name="task-hubs-in-durable-functions-azure-functions"></a>Durable Functions におけるタスク ハブ (Azure Functions)

[Durable Functions](durable-functions-overview.md) の *タスク ハブ* は、オーケストレーションに使用される Azure Storage リソースの論理コンテナーです。 オーケストレーター関数とアクティビティ関数は、同じタスク ハブに属しているときに限り、情報をやり取りすることができます。

複数の関数アプリがストレージ アカウントを共有している場合、各関数アプリには個別のタスク ハブ名を構成する *必要があります*。 ストレージ アカウントには、複数のタスク ハブを含めることができます。 次の図は、共有ストレージ アカウントと専用ストレージ アカウントの各関数アプリにタスク ハブが 1 つあることを示しています。

![共有ストレージ アカウントと専用ストレージ アカウントの図](./media/durable-functions-task-hubs/task-hubs-storage.png)

## <a name="azure-storage-resources"></a>Azure Storage のリソース

タスク ハブは次のストレージ リソースから構成されます。

* 1 つまたは複数のコントロールキュー。
* 1 つの作業項目キュー。
* 1 つの履歴テーブル。
* 1 つのインスタンス テーブル。
* Lease Blob を少なくとも 1 つ含んだ 1 つのストレージ コンテナー。
* サイズの大きいメッセージ ペイロードを含むストレージ コンテナー (該当する場合)。

これらすべてのリソースは、オーケストレーター、エンティティ、またはアクティビティ関数の実行時 (またはスケジュール時) に、既定の Azure Storage アカウントに自動的に作成されます。 これらのリソースがどのように使用されるかについては、[パフォーマンスとスケーリング](durable-functions-perf-and-scale.md)に関する記事で説明しています。

## <a name="task-hub-names"></a>タスク ハブ名

タスク ハブは、これらの規則に準拠した名前で識別されます。

* 英数字のみを含む
* 文字で始まる
* 最小長が 3 文字で、最大長が 45 文字である

次の例に示すように、タスク ハブ名は *host.json* ファイルで宣言されます。

### <a name="hostjson-functions-20"></a>host.json (Functions 2.0)

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

### <a name="hostjson-functions-1x"></a>host.json (Functions 1.x)

```json
{
  "durableTask": {
    "hubName": "MyTaskHub"
  }
}
```

次の `host.json` ファイルの例に示すように、タスク ハブはアプリの設定を使用して構成することもできます。

### <a name="hostjson-functions-10"></a>host.json (Functions 1.0)

```json
{
  "durableTask": {
    "hubName": "%MyTaskHub%"
  }
}
```

### <a name="hostjson-functions-20"></a>host.json (Functions 2.0)

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

次のコードでは、アプリ設定として構成されているタスク ハブを操作するために[オーケストレーション クライアント バインド](durable-functions-bindings.md#orchestration-client)を使用する関数の記述方法を示しています。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("HttpStart")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}")] HttpRequestMessage req,
    [DurableClient(TaskHub = "%MyTaskHub%")] IDurableOrchestrationClient starter,
    string functionName,
    ILogger log)
{
    // Function input comes from the request content.
    object eventData = await req.Content.ReadAsAsync<object>();
    string instanceId = await starter.StartNewAsync(functionName, eventData);

    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");

    return starter.CreateCheckStatusResponse(req, instanceId);
}
```

> [!NOTE]
> 前記の C# の例は Durable Functions 2.x 用です。 Durable Functions 1.x の場合、`IDurableOrchestrationContext` の代わりに `DurableOrchestrationContext` を使用する必要があります。 バージョン間の相違点の詳細については、[Durable Functions のバージョン](durable-functions-versions.md)に関する記事を参照してください。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

`function.json` ファイルのタスク ハブ プロパティは、アプリ設定を通じて設定されます。

```json
{
    "name": "input",
    "taskHub": "%MyTaskHub%",
    "type": "orchestrationClient",
    "direction": "in"
}
```

# <a name="python"></a>[Python](#tab/python)

`function.json` ファイルのタスク ハブ プロパティは、アプリ設定を通じて設定されます。

```json
{
    "name": "input",
    "taskHub": "%MyTaskHub%",
    "type": "orchestrationClient",
    "direction": "in"
}
```

---

タスク ハブの名前は、先頭文字をアルファベットとする必要があります。また、使用できるのはアルファベットと数値だけです。 指定しない場合、次の表に示すように、既定のタスク ハブ名が使用されます。

| Durable の拡張機能のバージョン | 既定のタスク ハブ名 |
| - | - |
| 2.x | Azure にデプロイする場合、タスク ハブ名は _関数アプリ_ の名前から派生します。 Azure の外部で実行する場合、既定のタスク ハブ名は `TestHubName` です。 |
| 1.x | すべての環境で、既定のタスク ハブ名は `DurableFunctionsHub` です。 |

拡張機能のバージョン間の相違点の詳細については、[Durable Functions のバージョン](durable-functions-versions.md)に関する記事を参照してください。

> [!NOTE]
> この名前は共有ストレージ アカウント内に複数のタスク ハブがある場合に、それぞれのタスク ハブを区別するものです。 共有ストレージ アカウントを共有する関数アプリが複数ある場合、*host.json* ファイルでタスク ハブごとに異なる名前を明示的に構成する必要があります。 このようにしないと、複数の関数アプリがメッセージをめぐって互いに競合し、その結果、オーケストレーションが `Pending` または `Running` 状態で予期せず "スタック" するなど、未定義の動作が発生する可能性があります。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [オーケストレーションのバージョン管理を処理する方法の詳細](durable-functions-versioning.md)
