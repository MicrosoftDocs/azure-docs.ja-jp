---
title: Durable Functions のバインド - Azure
description: Azure Functions の Durable Functons 拡張機能のトリガーとバインドの使用方法。
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: fbd645ef9f5e687e71ce110fc84b8342e31defed
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70087533"
---
# <a name="bindings-for-durable-functions-azure-functions"></a>Durable Functions のバインド (Azure Functions)

[Durable Functions](durable-functions-overview.md) 拡張機能には、オーケストレーター関数とアクティビティ関数の実行を制御する 2 つの新しいトリガーのバインドが導入されています。 Durable Functions ランタイムのクライアントとして機能する出力バインドも導入されています。

## <a name="orchestration-triggers"></a>オーケストレーション トリガー

オーケストレーション トリガーを使用して、永続的なオーケストレーター関数を作成できます。 このトリガーは、新しいオーケストレーター関数インスタンスの開始と、タスクのために "待機している" 既存のオーケストレーター関数インスタンスの再開をサポートします。

Azure Functions 用の Visual Studio ツールを使用する場合、オーケストレーション トリガーは、[OrchestrationTriggerAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationTriggerAttribute.html) .NET 属性を使用して構成されます。

オーケストレーター関数を (JavaScript や C# スクリプトなどの) スクリプト言語で記述する場合、オーケストレーション トリガーは、*function.json* ファイルの `bindings` 配列の次の JSON オブジェクトによって定義されます。

```json
{
    "name": "<Name of input parameter in function signature>",
    "orchestration": "<Optional - name of the orchestration>",
    "type": "orchestrationTrigger",
    "direction": "in"
}
```

* `orchestration` はオーケストレーションの名前です。 これは、クライアントがこのオーケストレーター関数の新しいインスタンスを開始するときに使用する必要がある値です。 このプロパティは省略可能です。 指定されない場合は関数の名前が使用されます。

内部的には、このトリガーのバインドは、関数アプリの既定のストレージ アカウントで一連のキューをポーリングします。 これらのキューは拡張機能の内部実装の詳細であるため、バインド プロパティに明示的に構成されることはありません。

### <a name="trigger-behavior"></a>トリガーの動作

オーケストレーション トリガーについての注意事項を次に示します。

* **シングル スレッド** - 1 つのホスト インスタンスのすべてのオーケストレーター関数の実行で単一のディスパッチャー スレッドが使用されます。 このため、オーケストレーター関数が効率的であり、I/O の実行がないことを保証することが重要です。 さらに、このスレッドが、Durable Functions 固有のタスクの種類のために待機しているとき以外は、非同期操作を行わないことを保証することも重要です。
* **有害メッセージの処理** - オーケストレーション トリガーでは、有害メッセージはサポートされません。
* **メッセージの可視性** - オーケストレーション トリガー メッセージはキューから削除され、構成可能な期間にわたって非表示を保持します。 これらのメッセージの可視性は、関数アプリが正常に実行されている限り、自動的に更新されます。
* **戻り値** - 戻り値は JSON にシリアル化され、Azure Table ストレージのオーケストレーション履歴テーブルに保存されます。 これらの戻り値は、オーケストレーション クライアントのバインドによるクエリを実行できます。これについては、後で説明します。

> [!WARNING]
> オーケストレーター関数は、オーケストレーション トリガーのバインドを除いて、入力または出力バインドを使用しないでください。 これらのバインドはシングル スレッド ルールと I/O ルールに従っていない可能性があるため、これらのバインドを実行すると、Durable Task 拡張機能で問題が発生する可能性があります。

> [!WARNING]
> JavaScript オーケストレーター関数は、`async` で宣言してはなりません。

### <a name="trigger-usage-net"></a>トリガーの使用方法 (.NET)

オーケストレーション トリガーのバインドは、入力と出力の両方をサポートします。 入力と出力の処理に関する注意事項を次に示します。

* **入力** - .NET オーケストレーション関数は、パラメーター型として [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) のみをサポートします。 関数シグネチャ内で入力を直接逆シリアル化することはサポートされていません。 コードでは、[GetInput\<T>](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_GetInput__1)(.NET) または `getInput` (JavaScript) メソッドを使用してオーケストレーター関数の入力をフェッチする必要があります。 これらの入力は、JSON にシリアル化できる型にする必要があります。
* **出力** - オーケストレーション トリガーは、入力と同じように出力値をサポートします。 関数の戻り値は、出力値を割り当てるために使用され、JSON にシリアル化できる必要があります。 .NET 関数が `Task` または `void` を返した場合は、出力として `null` 値が保存されます。

### <a name="trigger-sample"></a>トリガー サンプル

最も単純な "Hello World" オーケストレーター関数の例を次に示します。

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static string Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (Functions 2.x のみ)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    return `Hello ${name}!`;
});
```

> [!NOTE]
> JavaScript の `context` オブジェクトは、DurableOrchestrationContext を表していませんが、[関数コンテキスト全体](../functions-reference-node.md#context-object)を表しています。 `context` オブジェクトの `df` プロパティによって、オーケストレーション メソッドにアクセスできます。

> [!NOTE]
> JavaScript オーケストレーターでは `return` を使用する必要があります。 `durable-functions` ライブラリは、`context.done` メソッドの呼び出しを管理します。

オーケストレーター関数の大半はアクティビティ関数を呼び出すため、"Hello World" の例でアクティビティ関数を呼び出す方法を示します。

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static async Task<string> Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    string result = await context.CallActivityAsync<string>("SayHello", name);
    return result;
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (Functions 2.x のみ)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    const result = yield context.df.callActivity("SayHello", name);
    return result;
});
```

## <a name="activity-triggers"></a>アクティビティ トリガー

アクティビティ トリガーを使用して、オーケストレーター関数によって呼び出される関数を作成できます。

Visual Studio を使用している場合、アクティビティ トリガーは [ActvityTriggerAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.ActivityTriggerAttribute.html) .NET 属性を使用して構成されます。

VS Code または Azure portal を使用して開発する場合、アクティビティ トリガーは *function.json* の `bindings` 配列で次の JSON オブジェクトによって定義されます。

```json
{
    "name": "<Name of input parameter in function signature>",
    "activity": "<Optional - name of the activity>",
    "type": "activityTrigger",
    "direction": "in"
}
```

* `activity` はアクティビティの名前です。 これは、オーケストレーター関数がこのアクティビティ関数を呼び出すために使用する値です。 このプロパティは省略可能です。 指定されない場合は関数の名前が使用されます。

内部的には、このトリガーのバインドは、関数アプリの既定のストレージ アカウントでキューをポーリングします。 このキューは拡張機能の内部実装の詳細であるため、バインド プロパティに明示的に構成されることはありません。

### <a name="trigger-behavior"></a>トリガーの動作

アクティビティ トリガーに関する注意事項を次に示します。

* **スレッド処理** - オーケストレーション トリガーとは異なり、アクティビティ トリガーにはスレッド処理と I/O に関する制限はありません。 それらは、標準的な関数と同様に扱うことができます。
* **有害メッセージの処理** - アクティビティ トリガーには、有害メッセージのサポートはありません。
* **メッセージの可視性** - アクティビティ トリガー メッセージはキューから削除され、構成可能な期間にわたって非表示を保持します。 これらのメッセージの可視性は、関数アプリが正常に実行されている限り、自動的に更新されます。
* **戻り値** - 戻り値は JSON にシリアル化され、Azure Table ストレージのオーケストレーション履歴テーブルに保存されます。

> [!WARNING]
> アクティビティ関数のストレージ バックエンドは実装の詳細であるため、ユーザー コードはこれらのストレージ エンティティと直接対話すべきではありません。

### <a name="trigger-usage-net"></a>トリガーの使用方法 (.NET)

アクティビティ トリガーのバインドは、オーケストレーション トリガーと同じように、入力と出力の両方をサポートします。 入力と出力の処理に関する注意事項を次に示します。

* **入力** - .NET アクティビティ関数は、パラメーター型として [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html) をネイティブに使用します。 別の方法として、JSON にシリアル化できるパラメーター型を使用してアクティビティ関数を宣言できます。 `DurableActivityContext` を使用する場合は、[GetInput\<T>](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html#Microsoft_Azure_WebJobs_DurableActivityContext_GetInput__1) を呼び出して、アクティビティ関数の入力をフェッチして逆シリアル化できます。
* **出力** - アクティビティ関数は、入力と同じように出力値をサポートします。 関数の戻り値は、出力値を割り当てるために使用され、JSON にシリアル化できる必要があります。 .NET 関数が `Task` または `void` を返した場合は、出力として `null` 値が保存されます。
* **メタデータ** - .NET アクティビティ関数を `string instanceId` パラメーターにバインドして、親オーケストレーションのインスタンス ID を取得できます。

### <a name="trigger-sample"></a>トリガー サンプル

単純な "Hello World" アクティビティ関数の例を次に示します。

#### <a name="c"></a>C#

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] DurableActivityContext helloContext)
{
    string name = helloContext.GetInput<string>();
    return $"Hello {name}!";
}
```

.NET `ActivityTriggerAttribute` バインドの既定のパラメーター型は `DurableActivityContext` です。 ただし、.NET アクティビティ トリガーは、JSON にシリアル化できる型 (プリミティブ型を含む) への直接的なバインドもサポートしているため、同じ関数を次のように単純化できます。

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] string name)
{
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (Functions 2.x のみ)

```javascript
module.exports = async function(context) {
    return `Hello ${context.bindings.name}!`;
};
```

JavaScript のバインドは、追加のパラメーターとして渡すこともできるため、同じ関数を次のように単純化できます。

```javascript
module.exports = async function(context, name) {
    return `Hello ${name}!`;
};
```

### <a name="passing-multiple-parameters"></a>複数のパラメーターを渡す

アクティビティ関数に複数のパラメーターを直接渡すことはできません。 この場合のレコメンデーションは、オブジェクトの配列を渡すか、.NET の [ValueTuples](https://docs.microsoft.com/dotnet/csharp/tuples) オブジェクトを使用することです。

次の例では、[C# 7](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-7#tuples) に追加された [ValueTuples](https://docs.microsoft.com/dotnet/csharp/tuples) の新機能を使用しています。

```csharp
[FunctionName("GetCourseRecommendations")]
public static async Task<dynamic> RunOrchestrator(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    string major = "ComputerScience";
    int universityYear = context.GetInput<int>();

    dynamic courseRecommendations = await context.CallActivityAsync<dynamic>("CourseRecommendations", (major, universityYear));
    return courseRecommendations;
}

[FunctionName("CourseRecommendations")]
public static async Task<dynamic> Mapper([ActivityTrigger] DurableActivityContext inputs)
{
    // parse input for student's major and year in university
    (string Major, int UniversityYear) studentInfo = inputs.GetInput<(string, int)>();

    // retrieve and return course recommendations by major and university year
    return new {
        major = studentInfo.Major,
        universityYear = studentInfo.UniversityYear,
        recommendedCourses = new []
        {
            "Introduction to .NET Programming",
            "Introduction to Linux",
            "Becoming an Entrepreneur"
        }
    };
}
```

## <a name="orchestration-client"></a>オーケストレーション クライアント

オーケストレーション クライアントのバインドを使用して、オーケストレーター関数と対話する関数を記述できます。 たとえば、次のようにオーケストレーション インスタンスを操作できます。

* インスタンスを開始する。
* インスタンスの状態をクエリする。
* インスタンスを終了する。
* インスタンスの実行中にイベントを送信する。
* インスタンスの履歴を消去します。

Visual Studio を使用する場合は、[OrchestrationClientAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationClientAttribute.html) .NET 属性を使用してオーケストレーション クライアントにバインドできます。

スクリプト言語 ( *.csx* ファイル、 *.js* ファイルなど) を使用して開発する場合、オーケストレーション トリガーは、*function.json* の `bindings` 配列で次の JSON オブジェクトによって定義されます。

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "orchestrationClient",
    "direction": "in"
}
```

* `taskHub` - 複数の関数アプリが同じストレージ アカウントを共有するが、相互に分離する必要があるシナリオで使用されます。 指定されていない場合は、`host.json` の既定値が使用されます。 この値は、ターゲットのオーケストレーター関数によって使用される値と一致している必要があります。
* `connectionName` - ストレージ アカウント接続文字列を含むアプリ設定の名前。 この接続文字列で表されるストレージ アカウントは、ターゲットのオーケストレーター関数によって使用されるものと同じにする必要があります。 指定されない場合は、関数アプリの既定のストレージ アカウント接続文字列が使用されます。

> [!NOTE]
> ほとんどの場合、これらのプロパティを省略し、既定の動作を使用することをお勧めします。

### <a name="client-usage"></a>クライアントの使用

.NET 関数では、通常は、`DurableOrchestrationClient` にバインドします。これにより、Durable Functions によってサポートされるすべてのクライアント API にフル アクセスできます。 JavaScript では、同じ API が、`getClient` から返される `DurableOrchestrationClient` オブジェクトによって公開されます。 クライアント オブジェクトの API には以下が含まれます。

* [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_)
* [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_)
* [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_)
* [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_)
* [PurgeInstanceHistoryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_PurgeInstanceHistoryAsync_) (現在 .NET のみ)

.NET 関数は `IAsyncCollector<T>` にバインドすることもできます。`T` は [StartOrchestrationArgs](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.StartOrchestrationArgs.html) または `JObject` です。

これらの操作の詳細については、API のドキュメントの [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) を参照してください。

> [!WARNING]
> JavaScript でローカルに開発する場合は、環境変数 `WEBSITE_HOSTNAME` を `localhost:<port>` に設定する必要があります。たとえば、 `DurableOrchestrationClient` のメソッドを使用するには、`localhost:7071` に設定します。 この要件の詳細については、[GitHub の問題](https://github.com/Azure/azure-functions-durable-js/issues/28)に関するページをご覧ください。

### <a name="client-sample-visual-studio-development"></a>クライアントのサンプル (Visual Studio での開発)

"HelloWorld" オーケストレーションを開始するキューによってトリガーされる関数の例を次に示します。

```csharp
[FunctionName("QueueStart")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [OrchestrationClient] DurableOrchestrationClient starter)
{
    // Orchestration input comes from the queue message content.
    return starter.StartNewAsync("HelloWorld", input);
}
```

### <a name="client-sample-not-visual-studio"></a>クライアントのサンプル (Visual Studio 以外)

開発用に Visual Studio を使用していない場合は、次の *function.json* ファイルを作成できます。 この例は、永続的なオーケストレーション クライアントのバインドを使用するキューによってトリガーされる関数の構成方法を示しています。

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "queueTrigger",
      "queueName": "durable-function-trigger",
      "direction": "in"
    },
    {
      "name": "starter",
      "type": "orchestrationClient",
      "direction": "in"
    }
  ]
}
```

新しいオーケストレーター関数のインスタンスを開始する言語固有のサンプルを次に示します。

#### <a name="c-sample"></a>C# のサンプル

次の例は、永続的なオーケストレーション クライアントのバインドを使用して、C# スクリプト関数から新しい関数インスタンスを開始する方法を示しています。

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

public static Task<string> Run(string input, DurableOrchestrationClient starter)
{
    return starter.StartNewAsync("HelloWorld", input);
}
```

#### <a name="javascript-sample"></a>JavaScript のサンプル

次の例は、永続的なオーケストレーション クライアントのバインドを使用して、JavaScript 関数から新しい関数インスタンスを開始する方法を示しています。

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    return instanceId = await client.startNew("HelloWorld", undefined, context.bindings.input);
};
```

インスタンスの開始の詳細については、[インスタンスの管理](durable-functions-instance-management.md)に関する記事を参照してください。

<a name="host-json"></a>

## <a name="hostjson-settings"></a>host.json 設定

[!INCLUDE [durabletask](../../../includes/functions-host-json-durabletask.md)]

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [チェックポイント処理と動作の再現の詳細](durable-functions-checkpointing-and-replay.md)