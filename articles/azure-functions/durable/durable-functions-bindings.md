---
title: Durable Functions のバインド - Azure
description: Azure Functions の Durable Functons 拡張機能のトリガーとバインドの使用方法。
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: azfuncdf
ms.openlocfilehash: f297c89d2c3ba5692a44fab631c0d46c75f48692
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2019
ms.locfileid: "71033585"
---
# <a name="bindings-for-durable-functions-azure-functions"></a>Durable Functions のバインド (Azure Functions)

[Durable Functions](durable-functions-overview.md) 拡張機能には、オーケストレーター関数とアクティビティ関数の実行を制御する 2 つの新しいトリガーのバインドが導入されています。 Durable Functions ランタイムのクライアントとして機能する出力バインドも導入されています。

## <a name="orchestration-trigger"></a>オーケストレーション トリガー

オーケストレーション トリガーを使用して、[永続的なオーケストレーター関数](durable-functions-types-features-overview.md#orchestrator-functions)を作成できます。 このトリガーは、新しいオーケストレーター関数インスタンスの開始と、タスクのために "待機している" 既存のオーケストレーター関数インスタンスの再開をサポートします。

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
> オーケストレーター関数は、オーケストレーション トリガーのバインドを除いて、入力または出力バインドを使用しないでください。 これらのバインドはシングル スレッド ルールと I/O ルールに従っていない可能性があるため、これらのバインドを実行すると、Durable Task 拡張機能で問題が発生する可能性があります。 他のバインドを使用する場合は、Orchestrator 関数から呼び出されたアクティビティ関数にそれらを追加します。

> [!WARNING]
> JavaScript オーケストレーター関数は、`async` で宣言してはなりません。

### <a name="trigger-usage-net"></a>トリガーの使用方法 (.NET)

オーケストレーション トリガーのバインドは、入力と出力の両方をサポートします。 入力と出力の処理に関する注意事項を次に示します。

* **入力** - .NET オーケストレーション関数は、パラメーター型として [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) のみをサポートします。 関数シグネチャ内で入力を直接逆シリアル化することはサポートされていません。 コードでは、[GetInput\<T>](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_GetInput__1)(.NET) または `getInput` (JavaScript) メソッドを使用してオーケストレーター関数の入力をフェッチする必要があります。 これらの入力は、JSON にシリアル化できる型にする必要があります。
* **出力** - オーケストレーション トリガーは、入力と同じように出力値をサポートします。 関数の戻り値は、出力値を割り当てるために使用され、JSON にシリアル化できる必要があります。 .NET 関数が `Task` または `void` を返した場合は、出力として `null` 値が保存されます。

### <a name="trigger-sample"></a>トリガー サンプル

最もシンプルな "Hello World" オーケストレーター関数のコードの例を次に示します。

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
> JavaScript の `context` オブジェクトは、DurableOrchestrationContext ではなく、[関数コンテキスト全体](../functions-reference-node.md#context-object)を表しています。 `context` オブジェクトの `df` プロパティによって、オーケストレーション メソッドにアクセスできます。

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

## <a name="activity-trigger"></a>アクティビティ トリガー

アクティビティ トリガーを使用すると、オーケストレーター関数によって呼び出される関数 ([アクティビティ関数](durable-functions-types-features-overview.md#activity-functions)と呼ばれる) を作成できます。

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

* `activity` はアクティビティの名前です。 この値は、オーケストレーター関数がこのアクティビティ関数を呼び出すために使用する名前です。 このプロパティは省略可能です。 指定されない場合は関数の名前が使用されます。

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

シンプルな "Hello World" アクティビティ関数のコードの例を次に示します。

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


### <a name="using-input-and-output-bindings"></a>入出力バインドを使用する

アクティビティ トリガーのバインドに加えて、通常の入出力バインドを使用できます。 たとえば、アクティビティ バインドへの入力を取得し、EventHub 出力バインドを使用して EventHub にメッセージを送信できます。

```json
{
  "bindings": [
    {
      "name": "message",
      "type": "activityTrigger",
      "direction": "in"
    },
    {
      "type": "eventHub",
      "name": "outputEventHubMessage",
      "connection": "EventhubConnectionSetting",
      "eventHubName": "eh_messages",
      "direction": "out"
  }
  ]
}
```

```javascript
module.exports = async function (context) {
    context.bindings.outputEventHubMessage = context.bindings.message;
};
```

## <a name="orchestration-client"></a>オーケストレーション クライアント

オーケストレーション クライアントのバインドを使用すると、オーケストレーター関数とやりとりする関数を記述できます。 これらの関数は、[クライアント関数](durable-functions-types-features-overview.md#client-functions)と呼ばれることもあります。 たとえば、次のようにオーケストレーション インスタンスを操作できます。

* インスタンスを開始する。
* インスタンスの状態をクエリする。
* インスタンスを終了する。
* インスタンスの実行中にイベントを送信する。
* インスタンスの履歴を消去します。

Visual Studio を使用する場合は、Durable Functions 1.0 用の [OrchestrationClientAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationClientAttribute.html) .NET 属性を使用してオーケストレーション クライアントにバインドできます。 Durable Functions 2.0 プレビュー以降では、`DurableClientAttribute` .NET 属性を使用してオーケストレーション クライアントにバインドできます。

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

.NET 関数では、通常は、`DurableOrchestrationClient` にバインドします。これにより、Durable Functions によってサポートされるすべてのクライアント API にフル アクセスできます。 Durable Functions 2.0 以降では、代わりに `IDurableOrchestrationClient` インターフェイスにバインドします。 JavaScript では、同じ API が、`getClient` から返されるオブジェクトによって公開されます。 クライアント オブジェクトの API には以下が含まれます。

* [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_)
* [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_)
* [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_)
* [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_)
* [PurgeInstanceHistoryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_PurgeInstanceHistoryAsync_)
* [CreateCheckStatusResponse](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateCheckStatusResponse_)
* [CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_)

.NET 関数は `IAsyncCollector<T>` にバインドすることもできます。`T` は [StartOrchestrationArgs](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.StartOrchestrationArgs.html) または `JObject` です。

これらの操作の詳細については、[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) の API ドキュメントを参照してください。

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

## <a name="entity-trigger"></a>エンティティ トリガー

エンティティ トリガーを使用すると、[エンティティ関数](durable-functions-entities.md)を作成できます。 このトリガーでは、特定のエンティティ インスタンスのイベントの処理がサポートされています。

Azure Functions 用の Visual Studio ツールを使用する場合、エンティティ トリガーは、`EntityTriggerAttribute` .NET 属性を使用して構成されます。

> [!NOTE]
> エンティティ トリガーは、Durable Functions 2.0 以降で使用できます。 エンティティ トリガーは、JavaScript ではまだ使用できません。

内部的には、このトリガーのバインドは、関数アプリの既定のストレージ アカウントで一連のキューをポーリングします。 これらのキューは拡張機能の内部実装の詳細であるため、バインド プロパティに明示的に構成されることはありません。

### <a name="trigger-behavior"></a>トリガーの動作

エンティティ トリガーに関する注意事項を次に示します。

* **シングルスレッド**: 特定のエンティティに対する操作を処理する場合は、単一のディスパッチャー スレッドが使用されます。 複数のメッセージが同時に 1 つのエンティティに送信される場合、操作は一度に 1 つずつ処理されます。
* **有害メッセージの処理** - エンティティ トリガーには、有害メッセージのサポートはありません。
* **メッセージの可視性** - エンティティ トリガー メッセージはキューから削除され、構成可能な期間にわたって非表示を保持します。 これらのメッセージの可視性は、関数アプリが正常に実行されている限り、自動的に更新されます。
* **戻り値** - エンティティ関数では戻り値はサポートされていません。 特定の API を使用することで、状態を保存したり、オーケストレーションに値を渡したりすることができます。

実行中にエンティティに対して行われた状態の変更はいずれも、実行の完了後も自動的に保持されます。

### <a name="trigger-usage-net"></a>トリガーの使用方法 (.NET)

すべてのエンティティ関数は、`IDurableEntityContext` のパラメーター型を備え、それには次のメンバーが含まれています。

* **EntityName**: 現在実行中のエンティティの名前を取得します。
* **EntityKey**: 現在実行中のエンティティのキーを取得します。
* **EntityId**: 現在実行中のエンティティの ID を取得します。
* **OperationName**: 現在の操作の名前を取得します。
* **IsNewlyConstructed**: 操作の前にエンティティが存在しなかった場合、`true` を返します。
* **GetState\<TState>()** : エンティティの現在の状態を取得します。 `TState` パラメーターは、プリミティブ型または JSON にシリアル化できる型にする必要があります。
* **SetState(object)** : エンティティの状態を更新します。 `object` パラメーターは、プリミティブ オブジェクトまたは JSON にシリアル化できるオブジェクトにする必要があります。
* **GetInput\<TInput>()** : 現在操作に対する入力を取得します。 `TInput` 型のパラメーターは、プリミティブ型または JSON にシリアル化できる型を表す必要があります。
* **Return(object)** : 操作を呼び出したオーケストレーションに値を返します。 `object` パラメーターは、プリミティブ オブジェクトまたは JSON にシリアル化できるオブジェクトにする必要があります。
* **DestructOnExit**: 現在の操作が完了したら、エンティティを削除します。
* **SignalEntity(EntityId, string, object)** : エンティティに一方向のメッセージを送信します。 `object` パラメーターは、プリミティブ オブジェクトまたは JSON にシリアル化できるオブジェクトにする必要があります。

クラスベースのエンティティ プログラミング モードを使用する場合は、`Entity.Current` スレッド静的プロパティを使用して `IDurableEntityContext` オブジェクトを参照できます。

### <a name="trigger-sample---entity-function"></a>トリガーのサンプル - エンティティ関数

次のコードは、標準関数として実装されているシンプルな *Counter* エンティティの例です。 この関数では 3 つの "*操作*" `add`、`reset`、`get` が定義されており、いずれでも整数の状態値 `currentValue` が操作されます。

```csharp
[FunctionName(nameof(Counter))]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    int currentValue = ctx.GetState<int>();

    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            int amount = ctx.GetInput<int>();
            currentValue += operand;
            break;
        case "reset":
            currentValue = 0;
            break;
        case "get":
            ctx.Return(currentValue);
            break;
    }

    ctx.SetState(currentValue);
}
```

### <a name="trigger-sample---entity-class"></a>トリガーのサンプル - エンティティ クラス

次の例は、.NET クラスおよびメソッドを使用した前の `Counter` エンティティの実装と同等です。

```csharp
public class Counter
{
    [JsonProperty("value")]
    public int CurrentValue { get; set; }

    public void Add(int amount) => this.CurrentValue += amount;
    
    public void Reset() => this.CurrentValue = 0;
    
    public int Get() => this.CurrentValue;

    [FunctionName(nameof(Counter))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<Counter>();
}
```

> [!NOTE]
> エンティティ クラスを使用するときは、`[FunctionName]` 属性を持つ関数エントリ ポイント メソッドを、`static` と宣言する "*必要があります*"。 非静的なエントリ ポイント メソッドを使用すると、複数のオブジェクトが初期化されたり、他の未定義の動作が発生したりする可能性があります。

エンティティ クラスには、バインディングおよび .NET 依存関係の挿入を対話処理するための特別なメカニズムがあります。 詳細については、[持続エンティティ](durable-functions-entities.md)に関する記事を参照してください。

## <a name="entity-client"></a>エンティティ クライアント

エンティティ クライアント バインディングを使用すると、[エンティティ関数](#entity-trigger)を非同期的にトリガーできます。 これらの関数は、[クライアント関数](durable-functions-types-features-overview.md#client-functions)と呼ばれることもあります。

Visual Studio を使用する場合は、`DurableClientAttribute` .NET 属性を使用してエンティティ クライアントにバインドできます。

> [!NOTE]
> また、`[DurableClientAttribute]` を使用して、[オーケストレーション クライアント](#orchestration-client)にバインドすることもできます。

スクリプト言語 ( *.csx* ファイル、 *.js* ファイルなど) を使用して開発する場合、エンティティ トリガーは、*function.json* の `bindings` 配列で次の JSON オブジェクトによって定義されます。

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "durableClient",
    "direction": "out"
}
```

* `taskHub` - 複数の関数アプリが同じストレージ アカウントを共有するが、相互に分離する必要があるシナリオで使用されます。 指定されていない場合は、`host.json` の既定値が使用されます。 この値は、ターゲットのエンティティ関数によって使用される値と一致している必要があります。
* `connectionName` - ストレージ アカウント接続文字列を含むアプリ設定の名前。 この接続文字列で表されるストレージ アカウントは、ターゲットのエンティティ関数によって使用されるものと同じにする必要があります。 指定されない場合は、関数アプリの既定のストレージ アカウント接続文字列が使用されます。

> [!NOTE]
> ほとんどの場合、オプションのプロパティを省略し、既定の動作を使用することをお勧めします。

### <a name="entity-client-usage"></a>エンティティ クライアントの使用

.NET 関数では、通常は、`IDurableEntityClient` にバインドします。これにより、持続エンティティによってサポートされるすべてのクライアント API にフル アクセスできます。 また、`IDurableClient` インターフェイスにバインドすることもできます。これにより、エンティティとオーケストレーションの両方のクライアント API にアクセスできるようになります。 クライアント オブジェクトの API には以下が含まれます。

* **ReadEntityStateAsync\<T>** : エンティティの状態を読み取ります。
* **SignalEntityAsync**: エンティティに一方向のメッセージを送信し、エンキューされるまで待機します。
* **SignalEntityAsync\<TEntityInterface>** : `SignalEntityAsync` と同じですが、型 `TEntityInterface` の生成されたプロキシ オブジェクトを使用します。
* **CreateEntityProxy\<TEntityInterface>** : 型 `TEntityInterface` の動的プロキシを動的に生成し、エンティティへのタイプセーフな呼び出しを行います。

> [!NOTE]
> 前の "信号" 操作がすべて非同期であることを理解しておくことが重要です。 エンティティ関数を呼び出して、クライアントから戻り値を取得することはできません。 同様に、エンティティによって操作の実行が開始される前に、`SignalEntityAsync` が返される場合があります。 オーケストレーター関数のみがエンティティ関数を同期的に呼び出して、戻り値を処理することができます。

`SignalEntityAsync` API では、`EntityId` としてエンティティの一意の識別子を指定する必要があります。 また、これらの API では、必要に応じて、エンティティ操作の名前を `string` として、操作のペイロードを JSON にシリアル化できる `object` として受け取ることもできます。 ターゲット エンティティが存在しない場合は、指定されたエンティティ ID を使用してそれが自動的に作成されます。

### <a name="client-sample-untyped"></a>クライアントのサンプル (型指定なし)

"Counter" エンティティを呼び出す、キューによってトリガーされる関数の例を次に示します。

```csharp
[FunctionName("AddFromQueue")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [DurableClient] IDurableEntityClient client)
{
    // Entity operation input comes from the queue message content.
    var entityId = new EntityId(nameof(Counter), "myCounter");
    int amount = int.Parse(input);
    return client.SignalEntityAsync(entityId, "Add", amount);
}
```

### <a name="client-sample-typed"></a>クライアントのサンプル (型指定あり)

エンティティ操作へのタイプ セーフ アクセス用にプロキシ オブジェクトを生成することができます。 タイプ セーフなプロキシを生成するには、エンティティ型によってインターフェイスが実装される必要があります。 たとえば、前述の `Counter` エンティティによって、次のように定義された `ICounter` インターフェイスが実装されたとします。

```csharp
public interface ICounter
{
    void Add(int amount);
    void Reset();
    int Get();
}

public class Counter : ICounter
{
    // ...
}
```

その後、クライアント コードで `SignalEntityAsync<TEntityInterface>` を使用し、`ICounter` インターフェイスを型パラメーターとして指定することで、タイプ セーフなプロキシを生成できます。 次のコード サンプルは、タイプ セーフなプロキシのこの使用方法を示しています。

```csharp
[FunctionName("UserDeleteAvailable")]
public static async Task AddValueClient(
    [QueueTrigger("my-queue")] string message,
    [DurableClient] IDurableEntityClient client)
{
    var target = new EntityId(nameof(Counter), "myCounter");
    int amount = int.Parse(message);
    await client.SignalEntityAsync<ICounter>(target, proxy => proxy.Add(amount));
}
```

前の例では、`proxy` パラメーターは動的に生成された `ICounter` のインスタンスであり、`Add` への呼び出しを、`SignalEntityAsync` への同等の (型指定されていない) 呼び出しに内部的に変換します。

エンティティ インターフェイスの定義には、いくつかの規則があります。

* `SignalEntityAsync<TEntityInterface>` 内の型パラメーター `TEntityInterface` は、インターフェイスとする必要があります。
* エンティティ インターフェイスでは、メソッドのみを定義する必要があります。
* エンティティ インターフェイス メソッドで、複数のパラメーターを定義することはできません。
* エンティティ インターフェイス メソッドは、`void`、`Task`、または `Task<T>` を返す必要があります。ここで、`T` は戻り値です。
* エンティティ インターフェイスには、同じアセンブリ (つまり、エンティティ クラス) 内に、具体的な実装クラスが 1 つだけ含まれている必要があります。

これらの規則のいずれかに違反すると、実行時に `InvalidOperationException` がスローされます。 例外メッセージに、どの規則が破られたかが示されます。

> [!NOTE]
> `SignalEntityAsync` API は一方向の操作を表します。 エンティティ インターフェイスから `Task<T>` が返された場合、`T` パラメーターの値は常に null または `default` になります。

<a name="host-json"></a>

## <a name="hostjson-settings"></a>host.json 設定

[!INCLUDE [durabletask](../../../includes/functions-host-json-durabletask.md)]

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [インスタンス管理用の組み込みの HTTP API リファレンス](durable-functions-http-api.md)