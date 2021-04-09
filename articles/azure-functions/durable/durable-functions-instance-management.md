---
title: Durable Functions でのインスタンスの管理 - Azure
description: Azure Functions の Durable Functions 拡張機能でインスタンスを管理する方法を説明します。
author: cgillum
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 7329962d547fcb0635e3a9af3d80e562da59f7f2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103199787"
---
# <a name="manage-instances-in-durable-functions-in-azure"></a>Azure における Durable Functions でのインスタンスの管理

Azure Functions の [Durable Functions](durable-functions-overview.md) 拡張機能を使っている場合、または使い始めたい場合に、それを最大限有効に活用できるようにします。 その管理方法を詳しく学習することにより、Durable Functions のオーケストレーション インスタンスを最適化できます。 この記事では、各インスタンスの管理操作について詳しく説明します。

たとえば、インスタンスの開始や終了、およびすべてのインスタンスのクエリやフィルターを指定したインスタンスのクエリなどを実行できます。 さらに、インスタンスにイベントを送信、オーケストレーションの完了を待ってから、HTTP 管理 Webhook URL を取得することができます。 この記事では、インスタンスの巻き戻し、インスタンスの履歴の消去、タスク ハブの削除など、他の管理操作についても説明します。

Durable Functions では、これらの各管理操作の実装方法に関するオプションがあります。 この記事では、.NET (C#)、JavaScript、および Python について [Azure Functions Core Tools](../functions-run-local.md) を使用する例を提供します。

## <a name="start-instances"></a>インスタンスを開始する

オーケストレーションのインスタンスを開始できることが重要です。 これは一般に、別の関数のトリガーで Durable Functions のバインドが使用されているときに行われます。

[オーケストレーション クライアント バインディング](durable-functions-bindings.md#orchestration-client)上の `StartNewAsync` (.NET)、`startNew` (JavaScript)、または `start_new` (Python) メソッドにより、新しいインスタンスが開始されます。 内部的には、このメソッドは、メッセージをコントロール キューにエンキューし、これにより[オーケストレーション トリガーのバインド](durable-functions-bindings.md#orchestration-trigger)を使用する、指定された名前の関数がトリガーされます。

この非同期操作は、オーケストレーション プロセスが正常にスケジュールされたときに完了します。

新しいオーケストレーション インスタンスを開始するためのパラメーターは次のとおりです。

* **Name**:スケジュールするオーケストレーター関数の名前。
* **入力**:オーケストレーター関数に入力として渡す必要のある JSON でシリアル化できる任意のデータ。
* **InstanceId**: (省略可能) インスタンスの一意の ID。 このパラメーターを指定しない場合、メソッドではランダムな ID が使用されます。

> [!TIP]
> インスタンス ID にはランダムな識別子を使用します。 ランダムなインスタンス ID を使用すると、複数の VM にわたってオーケストレーター関数をスケーリングする場合に、負荷が均等に分散されるようになります。 ランダムではないインスタンス ID を使用するのに適しているのは、外部ソースから ID を取得する必要があるとき、または[シングルトン オーケストレーター](durable-functions-singletons.md) パターンを実装するときです。

次のコードは、新しいオーケストレーション インスタンスを開始する関数の例です。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("HelloWorldQueueTrigger")]
public static async Task Run(
    [QueueTrigger("start-queue")] string input,
    [DurableClient] IDurableOrchestrationClient starter,
    ILogger log)
{
    string instanceId = await starter.StartNewAsync("HelloWorld", input);
    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
}
```

> [!NOTE]
> 前記の C# コードは Durable Functions 2.x 用です。 Durable Functions 1.x では、`DurableClient` 属性の代わりに `OrchestrationClient` 属性を使用する必要があります。また、`IDurableOrchestrationClient` ではなく `DurableOrchestrationClient` パラメーター型を使用する必要があります。 バージョン間の相違点の詳細については、[Durable Functions のバージョン](durable-functions-versions.md)に関する記事を参照してください。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

<a name="javascript-function-json"></a>別途指定されていない限り、このページでは HTTP トリガーと次の function.json が使用されます。

**function.json**

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": ["post"]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "name": "starter",
      "type": "durableClient",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

> [!NOTE]
> この例では、Durable Functions 2.x をターゲットにしています。 バージョン 1.x の場合、`durableClient` ではなく `orchestrationClient` を使用します。

**index.js**

```javascript
const df = require("durable-functions");

module.exports = async function(context, input) {
    const client = df.getClient(context);

    const instanceId = await client.startNew("HelloWorld", undefined, input);
    context.log(`Started orchestration with ID = ${instanceId}.`);
};
```

# <a name="python"></a>[Python](#tab/python)

<a name="javascript-function-json"></a>別途指定されていない限り、このページでは HTTP トリガーと次の function.json が使用されます。

**function.json**

```json
{
  "scriptFile": "__init__.py",
  "bindings": [    
    {
      "name": "msg",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "messages",
      "connection": "AzureStorageQueuesConnectionString"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "name": "starter",
      "type": "durableClient",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

> [!NOTE]
> この例では、Durable Functions 2.x をターゲットにしています。 バージョン 1.x の場合、`durableClient` ではなく `orchestrationClient` を使用します。

**__init__.py**

```python
import logging
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)
    
    instance_id = await client.start_new('HelloWorld', None, None)
    logging.log(f"Started orchestration with ID = ${instance_id}.")

```

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

[Azure Functions Core Tools](../functions-run-local.md) の `durable start-new` コマンドを使用して、インスタンスを直接開始することもできます。 使用できるパラメーターは次のとおりです。

* **`function-name` (必須)** : 開始する関数の名前。
* **`input` (省略可能)** : 関数への入力 (インラインまたは JSON ファイル経由のどちらか)。 ファイルの場合は、`@` でファイルへのパスにプレフィックスを追加します (例: `@path/to/file.json`)。
* **`id` (省略可能)** : オーケストレーション インスタンスの ID。 このパラメーターを指定しないと、コマンドではランダムな GUID が使用されます。
* **`connection-string-setting` (省略可能)** : 使用するストレージ接続文字列を含むアプリケーション設定の名前。 既定では、AzureWebJobsStorage です。
* **`task-hub-name` (省略可能)** : 使用する Durable Functions タスク ハブの名前。 既定では、DurableFunctionsHub です。 [host.json](durable-functions-bindings.md#host-json) で durableTask:HubName を使用してこれを設定することもできます。

> [!NOTE]
> Core Tools のコマンドでは、関数アプリのルート ディレクトリから実行されることが想定されています。 `connection-string-setting` および `task-hub-name` パラメーターを明示的に指定した場合、任意のディレクトリからコマンドを実行できます。 関数アプリのホストが実行されていなくても、これらのコマンドを実行できますが、ホストが実行されていない場合、一部の効果を観察できないことがあります。 たとえば、`start-new` コマンドではターゲットのタスク ハブに開始メッセージがエンキューされますが、メッセージを処理できる関数アプリのホスト プロセスが実行されていない限り、オーケストレーションは実際に実行されません。

次のコマンドでは、HelloWorld という名前の関数が開始されて、`counter-data.json` ファイルの内容がその関数に渡されます。

```bash
func durable start-new --function-name HelloWorld --input @counter-data.json --task-hub-name TestTaskHub
```

## <a name="query-instances"></a>インスタンスのクエリを実行する

オーケストレーション管理作業の一環として、ほとんどの場合、オーケストレーション インスタンスの状態に関する情報を収集する必要があります (たとえば、正常に完了したか、失敗したか)。

[オーケストレーション クライアント バインディング](durable-functions-bindings.md#orchestration-client)上の `GetStatusAsync` (.NET)、`getStatus` (JavaScript)、または `get_status` (Python) メソッドにより、オーケストレーション インスタンスの状態がクエリされます。

これは、パラメーターとして `instanceId` (必須)、`showHistory` (省略可能)、`showHistoryOutput` (省略可能)、`showInput` (省略可能) を使用します。

* **`showHistory`** :`true` に設定すると、応答に実行履歴が含まれます。
* **`showHistoryOutput`** :`true` に設定すると、実行履歴にアクティビティ出力が含まれます。
* **`showInput`** :`false` にすると、応答に関数の入力が含まれます。 既定値は `true` です。

このメソッドは次のプロパティを持つオブジェクトを返します。

* **Name**:オーケストレーター関数の名前。
* **InstanceId**: オーケストレーションのインスタンス ID (`instanceId` 入力と同じにする必要があります)。
* **CreatedTime**: オーケストレーター関数が実行を開始した時刻。
* **LastUpdatedTime**: オーケストレーションが最後にチェックポイントされた時刻。
* **入力**:JSON 値としての関数の入力。 `showInput` が false の場合、このフィールドは設定されません。
* **CustomStatus**: JSON 形式でのカスタム オーケストレーションの状態。
* **出力**:JSON 値としての関数の出力 (関数が完了している場合)。 オーケストレーター関数が失敗した場合、このプロパティには、エラーの詳細が含まれます。 オーケストレーター関数が終了した場合、このプロパティには終了の理由が含まれます (存在する場合)。
* **RuntimeStatus**: 次のいずれかの値:
  * **Pending**: インスタンスはスケジュールされたが、まだ実行を開始していません。
  * **[実行中]** : インスタンスが実行を開始しました。
  * **Completed**: インスタンスが正常に完了しました。
  * **ContinuedAsNew**: インスタンスが新しい履歴で自身を再開しました。 これは一時的な状態です。
  * **[失敗]** : インスタンスがエラーで失敗しました。
  * **[中止]** : インスタンスが突然停止されました。
* **履歴**: オーケストレーションの実行履歴。 このフィールドにデータが設定されるのは、`showHistory` を `true` に設定した場合のみです。

> [!NOTE]
> すべてのスケジュールされたタスクが完了し、_かつ_ オーケストレーターが返されるまで、オーケストレーターは `Completed` とマークされません。 つまり、オーケストレーターが `Completed` とマークされるためには、`return` ステートメントに到達するだけでは不十分です。 これは、`WhenAny` が使用されている場合に特に関係があります。これらのオーケストレーターは、スケジュールされたすべてのタスクが実行される前に`return`することがよくあります。

このメソッドは、インスタンスが存在しない場合は `null` (.NET)、`undefined` (JavaScript)、または `None` (Python) を返します。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("GetStatus")]
public static async Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("check-status-queue")] string instanceId)
{
    DurableOrchestrationStatus status = await client.GetStatusAsync(instanceId);
    // do something based on the current status.
}
```

> [!NOTE]
> 前記の C# コードは Durable Functions 2.x 用です。 Durable Functions 1.x では、`DurableClient` 属性の代わりに `OrchestrationClient` 属性を使用する必要があります。また、`IDurableOrchestrationClient` ではなく `DurableOrchestrationClient` パラメーター型を使用する必要があります。 バージョン間の相違点の詳細については、[Durable Functions のバージョン](durable-functions-versions.md)に関する記事を参照してください。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const status = await client.getStatus(instanceId);
    // do something based on the current status.
    // example: if status.runtimeStatus === df.OrchestrationRuntimeStatus.Running: ...
}
```

function.json 構成については「[インスタンスを開始する](#javascript-function-json)」を参照してください。

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str, instance_id: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    status = await client.get_status(instance_id)
    # do something based on the current status
    # example: if (existing_instance.runtime_status is df.OrchestrationRuntimeStatus.Running) { ...
```

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

[Azure Functions Core Tools](../functions-run-local.md) の `durable get-runtime-status` コマンドを使用して、オーケストレーション インスタンスの状態を直接取得することもできます。 使用できるパラメーターは次のとおりです。

* **`id` (必須)** : オーケストレーション インスタンスの ID。
* **`show-input` (省略可能)** : `true` に設定すると、応答に関数の入力が含まれます。 既定値は `false` です。
* **`show-output` (省略可能)** : `true` に設定すると、応答に関数の出力が含まれます。 既定値は `false` です。
* **`connection-string-setting` (省略可能)** : 使用するストレージ接続文字列を含むアプリケーション設定の名前。 既定では、 `AzureWebJobsStorage`です。
* **`task-hub-name` (省略可能)** : 使用する Durable Functions タスク ハブの名前。 既定では、 `DurableFunctionsHub`です。 これは、[host.json](durable-functions-bindings.md#host-json) で durableTask:HubName を使用して設定することもできます。

次のコマンドでは、0ab8c55a66644d68a3a8b220b12d209c というオーケストレーション インスタンス ID を持つインスタンスの状態 (入力と出力を含む) が取得されます。 関数アプリのルート ディレクトリから `func` コマンドを実行することが想定されています。

```bash
func durable get-runtime-status --id 0ab8c55a66644d68a3a8b220b12d209c --show-input true --show-output true
```

`durable get-history` コマンドを使用して、オーケストレーション インスタンスの履歴を取得できます。 使用できるパラメーターは次のとおりです。

* **`id` (必須)** : オーケストレーション インスタンスの ID。
* **`connection-string-setting` (省略可能)** : 使用するストレージ接続文字列を含むアプリケーション設定の名前。 既定では、 `AzureWebJobsStorage`です。
* **`task-hub-name` (省略可能)** : 使用する Durable Functions タスク ハブの名前。 既定では、 `DurableFunctionsHub`です。 これは、host.json で durableTask:HubName を使用して設定することもできます。

```bash
func durable get-history --id 0ab8c55a66644d68a3a8b220b12d209c
```

## <a name="query-all-instances"></a>すべてのインスタンスのクエリを実行する

オーケストレーションで一度に 1 つのインスタンスのクエリを実行するのではなく、一度にすべてのインスタンスのクエリを実行する方が効率的な場合があります。

[ListInstancesAsync](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.idurableorchestrationclient.listinstancesasync#Microsoft_Azure_WebJobs_Extensions_DurableTask_IDurableOrchestrationClient_ListInstancesAsync_Microsoft_Azure_WebJobs_Extensions_DurableTask_OrchestrationStatusQueryCondition_System_Threading_CancellationToken_) (.NET)、[getStatusAll](/javascript/api/durable-functions/durableorchestrationclient#getstatusall--) (JavaScript)、または `get_status_all` (Python) メソッドを使用して、すべてのオーケストレーション インスタンスの状態のクエリを実行できます。 .NET では、それを取り消したい場合、`CancellationToken` オブジェクトを渡すことができます。 このメソッドは、クエリ パラメーターに一致するオーケストレーション インスタンスを表すオブジェクトの一覧を返します。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("GetAllStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient client,
    ILogger log)
{
    var noFilter = new OrchestrationStatusQueryCondition();
    OrchestrationStatusQueryResult result = await client.ListInstancesAsync(
        noFilter,
        CancellationToken.None);
    foreach (DurableOrchestrationStatus instance in result.DurableOrchestrationState)
    {
        log.LogInformation(JsonConvert.SerializeObject(instance));
    }
    
    // Note: ListInstancesAsync only returns the first page of results.
    // To request additional pages provide the result.ContinuationToken
    // to the OrchestrationStatusQueryCondition's ContinuationToken property.
}
```

> [!NOTE]
> 前記の C# コードは Durable Functions 2.x 用です。 Durable Functions 1.x では、`DurableClient` 属性の代わりに `OrchestrationClient` 属性を使用する必要があります。また、`IDurableOrchestrationClient` ではなく `DurableOrchestrationClient` パラメーター型を使用する必要があります。 バージョン間の相違点の詳細については、[Durable Functions のバージョン](durable-functions-versions.md)に関する記事を参照してください。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    const instances = await client.getStatusAll();
    instances.forEach((instance) => {
        context.log(JSON.stringify(instance));
    });
};
```

# <a name="python"></a>[Python](#tab/python)

```python
import logging
import json
import azure.functions as func
import azure.durable_functions as df


async def main(req: func.HttpRequest, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    instances = await client.get_status_all()

    for instance in instances:
        logging.log(json.dumps(instance))
```

function.json 構成については「[インスタンスを開始する](#javascript-function-json)」を参照してください。

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

[Azure Functions Core Tools](../functions-run-local.md) の `durable get-instances` コマンドを使用して、インスタンスのクエリを直接実行することもできます。 使用できるパラメーターは次のとおりです。

* **`top` (省略可能)** : このコマンドは、ページングをサポートします。 このパラメーターは、要求ごとに取得されるインスタンス数に対応します。 既定値は 10 です。
* **`continuation-token` (省略可能)** : 取得するインスタンスのページまたはセクションを示すトークン。 `get-instances` を実行するたびに、次の一連のインスタンスにトークンが返されます。
* **`connection-string-setting` (省略可能)** : 使用するストレージ接続文字列を含むアプリケーション設定の名前。 既定では、 `AzureWebJobsStorage`です。
* **`task-hub-name` (省略可能)** : 使用する Durable Functions タスク ハブの名前。 既定では、 `DurableFunctionsHub`です。 これは、[host.json](durable-functions-bindings.md#host-json) で durableTask:HubName を使用して設定することもできます。

```bash
func durable get-instances
```

## <a name="query-instances-with-filters"></a>フィルターを使用してインスタンスのクエリを実行する

標準のインスタンス クエリで提供できるすべての情報が本当は必要ないときはどうしますか。 たとえば、オーケストレーションの作成時刻やオーケストレーションの実行時の状態だけを調べている場合です。 フィルターを適用することで、クエリを絞り込むことができます。

一連の定義済みのフィルターに一致するオーケストレーション インスタンスの一覧を取得するには、[ListInstancesAsync](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.idurableorchestrationclient.listinstancesasync#Microsoft_Azure_WebJobs_Extensions_DurableTask_IDurableOrchestrationClient_ListInstancesAsync_Microsoft_Azure_WebJobs_Extensions_DurableTask_OrchestrationStatusQueryCondition_System_Threading_CancellationToken_) (.NET) または [getStatusBy](/javascript/api/durable-functions/durableorchestrationclient#getstatusby-date---undefined--date---undefined--orchestrationruntimestatus---) (JavaScript) メソッドを使用します。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("QueryStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient client,
    ILogger log)
{
    // Get the first 100 running or pending instances that were created between 7 and 1 day(s) ago
    var queryFilter = new OrchestrationStatusQueryCondition
    {
        RuntimeStatus = new[]
        {
            OrchestrationRuntimeStatus.Pending,
            OrchestrationRuntimeStatus.Running,
        },
        CreatedTimeFrom = DateTime.UtcNow.Subtract(TimeSpan.FromDays(7)),
        CreatedTimeTo = DateTime.UtcNow.Subtract(TimeSpan.FromDays(1)),
        PageSize = 100,
    };
    
    OrchestrationStatusQueryResult result = await client.ListInstancesAsync(
        queryFilter,
        CancellationToken.None);
    foreach (DurableOrchestrationStatus instance in result.DurableOrchestrationState)
    {
        log.LogInformation(JsonConvert.SerializeObject(instance));
    }
}
```

> [!NOTE]
> 前記の C# コードは Durable Functions 2.x 用です。 Durable Functions 1.x では、`DurableClient` 属性の代わりに `OrchestrationClient` 属性を使用する必要があります。また、`IDurableOrchestrationClient` ではなく `DurableOrchestrationClient` パラメーター型を使用する必要があります。 バージョン間の相違点の詳細については、[Durable Functions のバージョン](durable-functions-versions.md)に関する記事を参照してください。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    const runtimeStatus = [
        df.OrchestrationRuntimeStatus.Completed,
        df.OrchestrationRuntimeStatus.Running,
    ];
    const instances = await client.getStatusBy(
        new Date(2018, 3, 10, 10, 1, 0),
        new Date(2018, 3, 10, 10, 23, 59),
        runtimeStatus
    );
    instances.forEach((instance) => {
        context.log(JSON.stringify(instance));
    });
};
```

function.json 構成については「[インスタンスを開始する](#javascript-function-json)」を参照してください。

# <a name="python"></a>[Python](#tab/python)

```python
import logging
from datetime import datetime
import json
import azure.functions as func
import azure.durable_functions as df
from azure.durable_functions.models.OrchestrationRuntimeStatus import OrchestrationRuntimeStatus

async def main(req: func.HttpRequest, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    runtime_status = [OrchestrationRuntimeStatus.Completed, OrchestrationRuntimeStatus.Running]

    instances = await client.get_status_by(
        datetime(2018, 3, 10, 10, 1, 0),
        datetime(2018, 3, 10, 10, 23, 59),
        runtime_status
    )

    for instance in instances:
        logging.log(json.dumps(instance))
```

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Azure Functions Core Tools では、`durable get-instances` コマンドでフィルターを使用することもできます。 前述の `top`、`continuation-token`、`connection-string-setting`、`task-hub-name` パラメーターに加えて、3 つのフィルター パラメーター (`created-after`、`created-before`、`runtime-status`) を使用できます。

* **`created-after` (省略可能)** : この日付/時刻 (UTC) の後に作成されたインスタンスを取得します。 ISO 8601 形式の日時が受け入れられます。
* **`created-before` (省略可能)** : この日付/時刻 (UTC) の前に作成されたインスタンスを取得します。 ISO 8601 形式の日時が受け入れられます。
* **`runtime-status` (省略可能)** : 特定の状態 (たとえば、実行中または完了) のインスタンスを取得します。 複数の (スペースで区切られた) 状態を指定できます。
* **`top` (省略可能)** : 要求ごとに取得されるインスタンスの数。 既定値は 10 です。
* **`continuation-token` (省略可能)** : 取得するインスタンスのページまたはセクションを示すトークン。 `get-instances` を実行するたびに、次の一連のインスタンスにトークンが返されます。
* **`connection-string-setting` (省略可能)** : 使用するストレージ接続文字列を含むアプリケーション設定の名前。 既定では、 `AzureWebJobsStorage`です。
* **`task-hub-name` (省略可能)** : 使用する Durable Functions タスク ハブの名前。 既定では、 `DurableFunctionsHub`です。 これは、[host.json](durable-functions-bindings.md#host-json) で durableTask:HubName を使用して設定することもできます。

フィルター (`created-after`、`created-before`、`runtime-status`) を何も指定しないと、コマンドでは、実行状態や作成時刻に関係なく、単に `top` インスタンスが取得されます。

```bash
func durable get-instances --created-after 2018-03-10T13:57:31Z --created-before  2018-03-10T23:59Z --top 15
```

## <a name="terminate-instances"></a>インスタンスを終了する

実行に時間がかかっているオーケストレーション インスタンスがある場合、または単に何らかの理由で完了する前にインスタンスを停止する必要がある場合のため、インスタンスを終了するオプションがあります。

[オーケストレーション クライアント バインディング](durable-functions-bindings.md#orchestration-client)上の `TerminateAsync` (.NET)、`terminate` (JavaScript)、または `terminate` (Python) メソッドを使用して、インスタンスを終了できます。 パラメーターは `instanceId` と `reason` 文字列の 2 つでは、これらはログとインスタンスの状態に書き込まれます。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("TerminateInstance")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("terminate-queue")] string instanceId)
{
    string reason = "It was time to be done.";
    return client.TerminateAsync(instanceId, reason);
}
```

> [!NOTE]
> 前記の C# コードは Durable Functions 2.x 用です。 Durable Functions 1.x では、`DurableClient` 属性の代わりに `OrchestrationClient` 属性を使用する必要があります。また、`IDurableOrchestrationClient` ではなく `DurableOrchestrationClient` パラメーター型を使用する必要があります。 バージョン間の相違点の詳細については、[Durable Functions のバージョン](durable-functions-versions.md)に関する記事を参照してください。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "It was time to be done.";
    return client.terminate(instanceId, reason);
};
```

function.json 構成については「[インスタンスを開始する](#javascript-function-json)」を参照してください。

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str, instance_id: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    reason = "It was time to be done."
    return client.terminate(instance_id, reason)
```

---

終了したインスタンスは、最終的に `Terminated` 状態に切り替えられます。 ただし、この切り替えはすぐには行われません。 代わりに、終了操作は、そのインスタンスに対する他の操作と共に、タスクハブでキューに登録されます。 [インスタンス クエリ](#query-instances) API を使用して、終了したインスタンスが実際に`Terminated`状態になったことを確認できます。

> [!NOTE]
> インスタンスの終了は、現在、伝達されません。 アクティビティ関数およびサブオーケストレーションは、これらを呼び出したオーケストレーション インスタンスが終了しているかどうかに関係なく、完了するまで実行されます。

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

[Azure Functions Core Tools](../functions-run-local.md) の `durable terminate` コマンドを使用して、オーケストレーション インスタンスを直接終了することもできます。 使用できるパラメーターは次のとおりです。

* **`id` (必須)** : 終了するオーケストレーション インスタンスの ID。
* **`reason` (省略可能)** : 終了の理由。
* **`connection-string-setting` (省略可能)** : 使用するストレージ接続文字列を含むアプリケーション設定の名前。 既定では、 `AzureWebJobsStorage`です。
* **`task-hub-name` (省略可能)** : 使用する Durable Functions タスク ハブの名前。 既定では、 `DurableFunctionsHub`です。 これは、[host.json](durable-functions-bindings.md#host-json) で durableTask:HubName を使用して設定することもできます。

次のコマンドでは、ID が 0ab8c55a66644d68a3a8b220b12d209c のオーケストレーション インスタンスを終了します。

```bash
func durable terminate --id 0ab8c55a66644d68a3a8b220b12d209c --reason "It was time to be done."
```

## <a name="send-events-to-instances"></a>インスタンスにイベントを送信する

一部のシナリオでは、オーケストレーター関数が待機して外部イベントをリッスンできることが重要です。 これには、[監視関数](durable-functions-overview.md#monitoring)や、[人による操作](durable-functions-overview.md#human)を待機している関数が含まれます。

[オーケストレーション クライアントのバインド](durable-functions-bindings.md#orchestration-client)上の `RaiseEventAsync` (.NET) または `raiseEvent` (JavaScript) メソッドを使用して、実行中のインスタンスにイベント通知を送信します。 これらのイベントを処理できるインスタンスは、`WaitForExternalEvent` (.NET) への呼び出しを待っているインスタンス、または`waitForExternalEvent` (JavaScript) の呼び出しを一時停止しているインスタンスです。

`RaiseEventAsync` (.NET) および `raiseEvent` (JavaScript) へのパラメーターは次のとおりです。

* **InstanceId**: インスタンスの一意の ID。
* **EventName**: 送信するイベントの名前。
* **EventData**: インスタンスに送信する JSON でシリアル化できるペイロード。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("RaiseEvent")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("event-queue")] string instanceId)
{
    int[] eventData = new int[] { 1, 2, 3 };
    return client.RaiseEventAsync(instanceId, "MyEvent", eventData);
}
```

> [!NOTE]
> 前記の C# コードは Durable Functions 2.x 用です。 Durable Functions 1.x では、`DurableClient` 属性の代わりに `OrchestrationClient` 属性を使用する必要があります。また、`IDurableOrchestrationClient` ではなく `DurableOrchestrationClient` パラメーター型を使用する必要があります。 バージョン間の相違点の詳細については、[Durable Functions のバージョン](durable-functions-versions.md)に関する記事を参照してください。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const eventData = [ 1, 2, 3 ];
    return client.raiseEvent(instanceId, "MyEvent", eventData);
};
```

function.json 構成については「[インスタンスを開始する](#javascript-function-json)」を参照してください。

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str, instance_id: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    event_data = [1, 2 ,3]
    return client.raise_event(instance_id, 'MyEvent', event_data)
```

---

> [!NOTE]
> 指定したインスタンス ID のオーケストレーション インスタンスが存在しない場合、イベント メッセージは破棄されます。 インスタンスが存在していても、まだイベントを待機していない場合、受信して処理する準備が整うまでイベントはインスタンスの状態で格納されます。

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

[Azure Functions Core Tools](../functions-run-local.md) の `durable raise-event` コマンドを使用して、オーケストレーション インスタンスに対するイベントを直接発生させることもできます。 使用できるパラメーターは次のとおりです。

* **`id` (必須)** : オーケストレーション インスタンスの ID。
* **`event-name`** :発生させるイベントの名前。
* **`event-data` (省略可能)** : オーケストレーション インスタンスに送信するデータ。 これは JSON ファイルへのパスにするか、コマンド ラインでデータを直接指定することができます。
* **`connection-string-setting` (省略可能)** : 使用するストレージ接続文字列を含むアプリケーション設定の名前。 既定では、 `AzureWebJobsStorage`です。
* **`task-hub-name` (省略可能)** : 使用する Durable Functions タスク ハブの名前。 既定では、 `DurableFunctionsHub`です。 これは、[host.json](durable-functions-bindings.md#host-json) で durableTask:HubName を使用して設定することもできます。

```bash
func durable raise-event --id 0ab8c55a66644d68a3a8b220b12d209c --event-name MyEvent --event-data @eventdata.json
```

```bash
func durable raise-event --id 1234567 --event-name MyOtherEvent --event-data 3
```

## <a name="wait-for-orchestration-completion"></a>オーケストレーションの完了の待機

実行時間の長いオーケストレーションでは、オーケストレーションの結果を待機して取得することが必要な場合があります。 このような場合は、オーケストレーションに対してタイムアウト期間も定義できると便利です。 タイムアウトを過ぎた場合は、結果ではなく、オーケストレーションの状態が返されます。

`WaitForCompletionOrCreateCheckStatusResponseAsync` (.NET) または `waitForCompletionOrCreateCheckStatusResponse` (JavaScript) メソッドを使用すると、オーケストレーション インスタンスからの実際の出力を同期的に取得できます。 既定では、これらのメソッドでは `timeout` には 10 秒、`retryInterval` には 1 秒が既定値として使用されます。  

この API の使用方法を示す HTTP トリガー関数の例を次に示します。

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpSyncStart.cs)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpSyncStart/index.js)]

function.json 構成については「[インスタンスを開始する](#javascript-function-json)」を参照してください。

# <a name="python"></a>[Python](#tab/python)

```python
import logging
import azure.functions as func
import azure.durable_functions as df

timeout = "timeout"
retry_interval = "retryInterval"

async def main(req: func.HttpRequest, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    instance_id = await client.start_new(req.route_params['functionName'], None, req.get_body())
    logging.log(f"Started orchestration with ID = '${instance_id}'.")

    timeout_in_milliseconds = get_time_in_seconds(req, timeout)
    timeout_in_milliseconds = timeout_in_milliseconds if timeout_in_milliseconds != None else 30000
    retry_interval_in_milliseconds = get_time_in_seconds(req, retry_interval)
    retry_interval_in_milliseconds = retry_interval_in_milliseconds if retry_interval_in_milliseconds != None else 1000

    return client.wait_for_completion_or_create_check_status_response(
        req,
        instance_id,
        timeout_in_milliseconds,
        retry_interval_in_milliseconds
    )

def get_time_in_seconds(req: func.HttpRequest, query_parameter_name: str):
    query_value = req.params.get(query_parameter_name)
    return query_value if query_value != None else 1000
```

---

次の行で関数を呼び出します。 タイムアウトには 2 秒、再試行間隔には 0.5 秒を使用します。

```bash
    http POST http://localhost:7071/orchestrators/E1_HelloSequence/wait?timeout=2&retryInterval=0.5
```

オーケストレーション インスタンスからの応答を取得するために必要な時間に応じて 2 つのケースがあります。

* オーケストレーション インスタンスが、定義されたタイムアウト (このケースでは 2 秒) 内に完了すると、応答は、同期して提供される実際のオーケストレーション インスタンス出力です。

    ```http
        HTTP/1.1 200 OK
        Content-Type: application/json; charset=utf-8
        Date: Thu, 14 Dec 2018 06:14:29 GMT
        Transfer-Encoding: chunked

        [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ]
    ```

* オーケストレーション インスタンスが、定義されたタイムアウト内に完了できない場合、応答は「[HTTP API URL の検出](durable-functions-http-api.md)」で説明されている既定のものになります。

    ```http
        HTTP/1.1 202 Accepted
        Content-Type: application/json; charset=utf-8
        Date: Thu, 14 Dec 2018 06:13:51 GMT
        Location: http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}
        Retry-After: 10
        Transfer-Encoding: chunked

        {
            "id": "d3b72dddefce4e758d92f4d411567177",
            "sendEventPostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177/raiseEvent/{eventName}?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "statusQueryGetUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "terminatePostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177/terminate?reason={text}&taskHub={taskHub}&connection={connection}&code={systemKey}"
        }
    ```

> [!NOTE]
> Webhook URL の形式は、実行している Azure Functions ホストのバージョンによって異なる場合があります。 上記の例は、Azure Functions 2.0 ホスト用の形式です。

## <a name="retrieve-http-management-webhook-urls"></a>HTTP 管理 Webhook URL を取得する

外部システムを使用して、オーケストレーションに対するイベントを監視したり、発生させたりできます。 外部システムは、[HTTP API URL の検出](durable-functions-http-features.md#http-api-url-discovery)に関するページで説明されている既定の応答の一部である Webhook URL を介して、Durable Functions と通信できます。 Webhook URL は、[オーケストレーション クライアントのバインド](durable-functions-bindings.md#orchestration-client)を使用してプログラムからアクセスすることもできます。 `CreateHttpManagementPayload` (.NET) または `createHttpManagementPayload` (JavaScript) メソッドを使用して、これらの Webhook URL を含むシリアル化可能なオブジェクトを取得できます。

`CreateHttpManagementPayload` (.NET) と `createHttpManagementPayload` (JavaScript) メソッドには、1 つのパラメーターがあります。

* **instanceId**: インスタンスの一意の ID。

このメソッドは次の文字列プロパティを持つオブジェクトを返します。

* **Id**:オーケストレーションのインスタンス ID (`InstanceId` 入力と同じにする必要があります)。
* **StatusQueryGetUri**: オーケストレーション インスタンスの状態の URL。
* **SendEventPostUri**: オーケストレーション インスタンスの "イベント発生" URL。
* **TerminatePostUri**: オーケストレーション インスタンスの "終了" URL。
* **PurgeHistoryDeleteUri**:オーケストレーション インスタンスの "消去履歴" URL。

次の例に示すように、関数はこれらのオブジェクトのインスタンスを外部システムに送信して、対応するオーケストレーションでイベントを監視または発生させることができます。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("SendInstanceInfo")]
public static void SendInstanceInfo(
    [ActivityTrigger] IDurableActivityContext ctx,
    [DurableClient] IDurableOrchestrationClient client,
    [DocumentDB(
        databaseName: "MonitorDB",
        collectionName: "HttpManagementPayloads",
        ConnectionStringSetting = "CosmosDBConnection")]out dynamic document)
{
    HttpManagementPayload payload = client.CreateHttpManagementPayload(ctx.InstanceId);

    // send the payload to Cosmos DB
    document = new { Payload = payload, id = ctx.InstanceId };
}
```

> [!NOTE]
> 前記の C# コードは Durable Functions 2.x 用です。 Durable Functions 1.x では、`IDurableActivityContext` ではなく `DurableActivityContext` を使用する必要があり、`DurableClient` 属性の代わりに `OrchestrationClient` 属性を使用する必要があり、`IDurableOrchestrationClient` ではなく `DurableOrchestrationClient` パラメーター型を使用する必要があります。 バージョン間の相違点の詳細については、[Durable Functions のバージョン](durable-functions-versions.md)に関する記事を参照してください。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

modules.exports = async function(context, ctx) {
    const client = df.getClient(context);

    const payload = client.createHttpManagementPayload(ctx.instanceId);

    // send the payload to Cosmos DB
    context.bindings.document = JSON.stringify({
        id: ctx.instanceId,
        payload,
    });
};
```

function.json 構成については「[インスタンスを開始する](#javascript-function-json)」を参照してください。

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str, instance_id: str) -> func.cosmosdb.cdb.Document:
    client = df.DurableOrchestrationClient(starter)

    payload = client.create_check_status_response(req, instance_id).get_body().decode()

    return func.cosmosdb.CosmosDBConverter.encode({
        id: instance_id,
        payload: payload
    })
```
---

## <a name="rewind-instances-preview"></a>インスタンスを巻き戻す (プレビュー)

予期しない理由でオーケストレーション エラーが発生した場合は、その目的のために作成されている API を使用して、以前の正常な状態にインスタンスを "*巻き戻す*" ことができます。

> [!NOTE]
> この API は、適切なエラー処理や再試行ポリシーの代わりとなるものではありません。 予期しない理由でオーケストレーション インスタンスが失敗する場合にのみ使用するものです。 エラー処理と再試行ポリシーについて詳しくは、[エラー処理](durable-functions-error-handling.md)に関する記事をご覧ください。

[オーケストレーション クライアントのバインド](durable-functions-bindings.md#orchestration-client)上の `RewindAsync` (.NET) または `rewind` (JavaScript) メソッドを使用して、オーケストレーションを *実行中* の状態に戻します。 またこのメソッドは、オーケストレーション エラーの原因となったアクティビティやサブ オーケストレーションの実行失敗も再実行します。

たとえば、一連の[人による承認](durable-functions-overview.md#human)が含まれるワークフローがあるものとします。 承認が必要であることをユーザーに通知し、リアルタイムの応答を待機する一連のアクティビティ関数があるとします。 すべての承認アクティビティが応答を受信するかタイムアウトになった後、アプリケーションの構成ミス (無効なデータベース接続文字列など) により別のアクティビティが失敗します。 結果として、ワークフローの深い部分でオーケストレーションが失敗します。 `RewindAsync` (.NET) または `rewind` (JavaScript) API を使用すると、アプリケーション管理者は構成エラーを修正し、失敗したオーケストレーションを失敗の直前の状態に巻き戻すことができます。 人間の対話手順はいずれも再承認が不要で、オーケストレーションは正常に完了できるようになります。

> [!NOTE]
> "*巻き戻し*" 機能では、永続タイマーを使用したオーケストレーション インスタンスの巻き戻しはサポートされません。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("RewindInstance")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("rewind-queue")] string instanceId)
{
    string reason = "Orchestrator failed and needs to be revived.";
    return client.RewindAsync(instanceId, reason);
}
```

> [!NOTE]
> 前記の C# コードは Durable Functions 2.x 用です。 Durable Functions 1.x では、`DurableClient` 属性の代わりに `OrchestrationClient` 属性を使用する必要があります。また、`IDurableOrchestrationClient` ではなく `DurableOrchestrationClient` パラメーター型を使用する必要があります。 バージョン間の相違点の詳細については、[Durable Functions のバージョン](durable-functions-versions.md)に関する記事を参照してください。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "Orchestrator failed and needs to be revived.";
    return client.rewind(instanceId, reason);
};
```

function.json 構成については「[インスタンスを開始する](#javascript-function-json)」を参照してください。

# <a name="python"></a>[Python](#tab/python)

> [!NOTE]
> 現在、この機能は Python ではサポートされていません。

<!-- ```python
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str, instance_id: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    reason = "Orchestrator failed and needs to be revived."
    return client.rewind(instance_id, reason)
``` -->

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

[Azure Functions Core Tools](../functions-run-local.md) の `durable rewind` コマンドを使用して、オーケストレーション インスタンスを直巻き戻すこともできます。 使用できるパラメーターは次のとおりです。

* **`id` (必須)** : オーケストレーション インスタンスの ID。
* **`reason` (省略可能)** : オーケストレーション インスタンスを巻き戻す理由。
* **`connection-string-setting` (省略可能)** : 使用するストレージ接続文字列を含むアプリケーション設定の名前。 既定では、 `AzureWebJobsStorage`です。
* **`task-hub-name` (省略可能)** : 使用する Durable Functions タスク ハブの名前。 既定では、[host.json](durable-functions-bindings.md#host-json) ファイル内のタスク ハブ名が使用されます。

```bash
func durable rewind --id 0ab8c55a66644d68a3a8b220b12d209c --reason "Orchestrator failed and needs to be revived."
```

## <a name="purge-instance-history"></a>インスタンスの履歴を消去する

オーケストレーションに関連付けられているすべてのデータを削除するには、インスタンスの履歴を消去できます。 たとえば、完了したインスタンスに関連付けられている Azure テーブルの行や、大きいメッセージ BLOB を削除したいことがあります。 これを行うには、[オーケストレーション クライアントのバインド](durable-functions-bindings.md#orchestration-client)の `PurgeInstanceHistoryAsync` (.NET) または `purgeInstanceHistory` (JavaScript) メソッドを使用します。

このメソッドには 2 つのオーバーロードがあります。 最初のオーバーロードは、オーケストレーション インスタンスの ID によって履歴を消去します。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("purge-queue")] string instanceId)
{
    return client.PurgeInstanceHistoryAsync(instanceId);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);
    return client.purgeInstanceHistory(instanceId);
};
```

function.json 構成については「[インスタンスを開始する](#javascript-function-json)」を参照してください。

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str, instance_id: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    return client.purge_instance_history(instance_id)
```

---

次の例では、指定した時間間隔後に完了したすべてのオーケストレーション インスタンスの履歴を消去する、タイマーによってトリガーされる関数を示します。 この場合は、30 日以上前に完了したすべてのインスタンスのデータが削除されます。 これは、1 日 1 回、午前 12 時に実行するようにスケジュールされています。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [TimerTrigger("0 0 12 * * *")]TimerInfo myTimer)
{
    return client.PurgeInstanceHistoryAsync(
        DateTime.MinValue,
        DateTime.UtcNow.AddDays(-30),  
        new List<OrchestrationStatus>
        {
            OrchestrationStatus.Completed
        });
}
```

> [!NOTE]
> 前記の C# コードは Durable Functions 2.x 用です。 Durable Functions 1.x では、`DurableClient` 属性の代わりに `OrchestrationClient` 属性を使用する必要があります。また、`IDurableOrchestrationClient` ではなく `DurableOrchestrationClient` パラメーター型を使用する必要があります。 バージョン間の相違点の詳細については、[Durable Functions のバージョン](durable-functions-versions.md)に関する記事を参照してください。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

`purgeInstanceHistoryBy` メソッドを使用し、複数のインスタンスのインスタンス履歴を条件付きで消去することができます。

**function.json**

```json
{
  "bindings": [
    {
      "schedule": "0 0 12 * * *",
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in"
    },
    {
      "name": "starter",
      "type": "durableClient",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

> [!NOTE]
> この例では、Durable Functions 2.x をターゲットにしています。 バージョン 1.x の場合、`durableClient` ではなく `orchestrationClient` を使用します。

**index.js**

```javascript
const df = require("durable-functions");

module.exports = async function (context, myTimer) {
    const client = df.getClient(context);
    const createdTimeFrom = new Date(0);
    const createdTimeTo = new Date().setDate(today.getDate() - 30);
    const runtimeStatuses = [ df.OrchestrationRuntimeStatus.Completed ];
    return client.purgeInstanceHistoryBy(createdTimeFrom, createdTimeTo, runtimeStatuses);
};
```
# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df
from azure.durable_functions.models.DurableOrchestrationStatus import OrchestrationRuntimeStatus
from datetime import datetime, timedelta

async def main(req: func.HttpRequest, starter: str, instance_id: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)
    created_time_from = datetime.min
    created_time_to = datetime.today() + timedelta(days = -30)
    runtime_statuses = [OrchestrationRuntimeStatus.Completed]

    return client.purge_instance_history_by(created_time_from, created_time_to, runtime_statuses)
```
---

> [!NOTE]
> 履歴の消去の操作を成功させるには、ターゲット インスタンスの実行時の状態が **完了**、**終了**、または **失敗** になっている必要があります。

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

[Azure Functions Core Tools](../functions-run-local.md) の `durable purge-history` コマンドを使用して、オーケストレーション インスタンスの履歴を消去することもできます。 前のセクションの 2 つ目の C# の例と同様に、指定した時間間隔中に作成されたすべてのオーケストレーション インスタンスの履歴が消去されます。 さらに、実行状態によって消去されるインスタンスをフィルター処理できます。 コマンドにはいくつかのパラメーターがあります。

* **`created-after` (省略可能)** : この日付/時刻 (UTC) の後に作成されたインスタンスの履歴を消去します。 ISO 8601 形式の日時が受け入れられます。
* **`created-before` (省略可能)** : この日付/時刻 (UTC) の前に作成されたインスタンスの履歴を消去します。 ISO 8601 形式の日時が受け入れられます。
* **`runtime-status` (省略可能)** : 特定の状態 (たとえば、実行中または完了) のインスタンスの履歴を消去します。 複数の (スペースで区切られた) 状態を指定できます。
* **`connection-string-setting` (省略可能)** : 使用するストレージ接続文字列を含むアプリケーション設定の名前。 既定では、 `AzureWebJobsStorage`です。
* **`task-hub-name` (省略可能)** : 使用する Durable Functions タスク ハブの名前。 既定では、[host.json](durable-functions-bindings.md#host-json) ファイル内のタスク ハブ名が使用されます。

次のコマンドでは、2018 年 11 月 14 日午後 7 時 35 分 (UTC) より前に作成されたすべての失敗したインスタンスの履歴が削除されます。

```bash
func durable purge-history --created-before 2018-11-14T19:35:00.0000000Z --runtime-status failed
```

## <a name="delete-a-task-hub"></a>タスク ハブを削除する

[Azure Functions Core Tools の ](../functions-run-local.md) `durable delete-task-hub` コマンドを使用して、Azure storage のテーブル、キュー、BLOB などの、特定のタスクハブに関連付けられているすべてのストレージ成果物を削除できます。 コマンドには 2 つのパラメーターがあります。

* **`connection-string-setting` (省略可能)** : 使用するストレージ接続文字列を含むアプリケーション設定の名前。 既定では、 `AzureWebJobsStorage`です。
* **`task-hub-name` (省略可能)** : 使用する Durable Functions タスク ハブの名前。 既定では、[host.json](durable-functions-bindings.md#host-json) ファイル内のタスク ハブ名が使用されます。

次のコマンドでは、`UserTest` タスク ハブに関連付けられている Azure ストレージ データがすべて削除されます。

```bash
func durable delete-task-hub --task-hub-name UserTest
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [バージョン管理の方法](durable-functions-versioning.md)

> [!div class="nextstepaction"]
> [インスタンス管理用の組み込みの HTTP API リファレンス](durable-functions-http-api.md)
