---
title: Durable Functions のバインド - Azure
description: Azure Functions の Durable Functons 拡張機能のトリガーとバインドの使用方法。
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: azfuncdf
ms.openlocfilehash: fe64945aeb71011e769ba4016ae36cb557f41f33
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102424259"
---
# <a name="bindings-for-durable-functions-azure-functions"></a>Durable Functions のバインド (Azure Functions)

[Durable Functions](durable-functions-overview.md) 拡張機能には、オーケストレーター関数とアクティビティ関数の実行を制御する 2 つの新しいトリガーのバインドが導入されています。 Durable Functions ランタイムのクライアントとして機能する出力バインドも導入されています。

## <a name="orchestration-trigger"></a>オーケストレーション トリガー

オーケストレーション トリガーを使用して、[永続的なオーケストレーター関数](durable-functions-types-features-overview.md#orchestrator-functions)を作成できます。 このトリガーは、新しいオーケストレーター関数インスタンスの開始と、タスクのために "待機している" 既存のオーケストレーター関数インスタンスの再開をサポートします。

Azure Functions 用の Visual Studio ツールを使用する場合、オーケストレーション トリガーは、[OrchestrationTriggerAttribute](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.orchestrationtriggerattribute) .NET 属性を使用して構成されます。

オーケストレーター関数を (JavaScript や C# スクリプトなどの) スクリプト言語で記述する場合、オーケストレーション トリガーは、*function.json* ファイルの `bindings` 配列の次の JSON オブジェクトによって定義されます。

```json
{
    "name": "<Name of input parameter in function signature>",
    "orchestration": "<Optional - name of the orchestration>",
    "type": "orchestrationTrigger",
    "direction": "in"
}
```

* `orchestration` は、クライアントがこのオーケストレーター関数の新しいインスタンスを開始するときに使用する必要がある、オーケストレーションの名前です。 このプロパティは省略可能です。 指定されない場合は関数の名前が使用されます。

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

* **入力** - .NET オーケストレーション関数は、パラメーター型として `DurableOrchestrationContext` のみをサポートします。 関数シグネチャ内で入力を直接逆シリアル化することはサポートされていません。 コードでは、`GetInput<T>` (.NET) または `getInput` (JavaScript) メソッドを使用してオーケストレーター関数の入力をフェッチする必要があります。 これらの入力は、JSON にシリアル化できる型にする必要があります。
* **出力** - オーケストレーション トリガーは、入力と同じように出力値をサポートします。 関数の戻り値は、出力値を割り当てるために使用され、JSON にシリアル化できる必要があります。 .NET 関数が `Task` または `void` を返した場合は、出力として `null` 値が保存されます。

### <a name="trigger-sample"></a>トリガー サンプル

最もシンプルな "Hello World" オーケストレーター関数のコードの例を次に示します。

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static string Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    return $"Hello {name}!";
}
```
> [!NOTE]
> 前のコードは Durable Functions 2.x 用です。 Durable Functions 1.x の場合、`IDurableOrchestrationContext` の代わりに `DurableOrchestrationContext` を使用する必要があります。 バージョン間の相違点の詳細については、[Durable Functions のバージョン](durable-functions-versions.md)に関する記事を参照してください。

#### <a name="javascript-functions-20-only"></a>JavaScript (Functions 2.0 のみ)

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
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    string result = await context.CallActivityAsync<string>("SayHello", name);
    return result;
}
```

> [!NOTE]
> 前のコードは Durable Functions 2.x 用です。 Durable Functions 1.x の場合、`IDurableOrchestrationContext` の代わりに `DurableOrchestrationContext` を使用する必要があります。 バージョン間の相違点の詳細については、[Durable Functions のバージョン](durable-functions-versions.md)に関する記事を参照してください。

#### <a name="javascript-functions-20-only"></a>JavaScript (Functions 2.0 のみ)

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

Visual Studio を使用する場合、アクティビティ トリガーは `ActivityTriggerAttribute` .NET 属性を使用して構成されます。

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

* **入力** - .NET アクティビティ関数は、パラメーター型として `DurableActivityContext` をネイティブに使用します。 別の方法として、JSON にシリアル化できるパラメーター型を使用してアクティビティ関数を宣言できます。 `DurableActivityContext` を使用する場合は、`GetInput<T>` を呼び出して、アクティビティ関数の入力をフェッチして逆シリアル化できます。
* **出力** - アクティビティ関数は、入力と同じように出力値をサポートします。 関数の戻り値は、出力値を割り当てるために使用され、JSON にシリアル化できる必要があります。 .NET 関数が `Task` または `void` を返した場合は、出力として `null` 値が保存されます。
* **メタデータ** - .NET アクティビティ関数を `string instanceId` パラメーターにバインドして、親オーケストレーションのインスタンス ID を取得できます。

### <a name="trigger-sample"></a>トリガー サンプル

シンプルな "Hello World" アクティビティ関数のコードの例を次に示します。

#### <a name="c"></a>C#

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] IDurableActivityContext helloContext)
{
    string name = helloContext.GetInput<string>();
    return $"Hello {name}!";
}
```

> [!NOTE]
> 前のコードは Durable Functions 2.x 用です。 Durable Functions 1.x の場合、`IDurableActivityContext` の代わりに `DurableActivityContext` を使用する必要があります。 バージョン間の相違点の詳細については、[Durable Functions のバージョン](durable-functions-versions.md)に関する記事を参照してください。

.NET `ActivityTriggerAttribute` バインドの既定のパラメーター型は `IDurableActivityContext` です。 ただし、.NET アクティビティ トリガーは、JSON にシリアル化できる型 (プリミティブ型を含む) への直接的なバインドもサポートしているため、同じ関数を次のように単純化できます。

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] string name)
{
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-20-only"></a>JavaScript (Functions 2.0 のみ)

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

Visual Studio を使用する場合は、Durable Functions 1.0 用の `OrchestrationClientAttribute` .NET 属性を使用してオーケストレーション クライアントにバインドできます。 Durable Functions 2.0 以降では、`DurableClientAttribute` .NET 属性を使用してオーケストレーション クライアントにバインドできます。

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

.NET 関数では、通常は、`IDurableOrchestrationClient` にバインドします。これにより、Durable Functions によってサポートされるすべてのオーケストレーション クライアント API にフル アクセスできます。 古い Durable Functions 2.x リリースでは、代わりに `DurableOrchestrationClient` クラスにバインドします。 JavaScript では、同じ API が、`getClient` から返されるオブジェクトによって公開されます。 クライアント オブジェクトの API には以下が含まれます。

* `StartNewAsync`
* `GetStatusAsync`
* `TerminateAsync`
* `RaiseEventAsync`
* `PurgeInstanceHistoryAsync`
* `CreateCheckStatusResponse`
* `CreateHttpManagementPayload`

.NET 関数は `IAsyncCollector<T>` にバインドすることもできます。`T` は `StartOrchestrationArgs` または `JObject` です。

これらの操作の詳細については、`IDurableOrchestrationClient` API のドキュメントを参照してください。

### <a name="client-sample-visual-studio-development"></a>クライアントのサンプル (Visual Studio での開発)

"HelloWorld" オーケストレーションを開始するキューによってトリガーされる関数の例を次に示します。

```csharp
[FunctionName("QueueStart")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [DurableClient] IDurableOrchestrationClient starter)
{
    // Orchestration input comes from the queue message content.
    return starter.StartNewAsync("HelloWorld", input);
}
```

> [!NOTE]
> 前記の C# コードは Durable Functions 2.x 用です。 Durable Functions 1.x では、`DurableClient` 属性の代わりに `OrchestrationClient` 属性を使用する必要があります。また、`IDurableOrchestrationClient` ではなく `DurableOrchestrationClient` パラメーター型を使用する必要があります。 バージョン間の相違点の詳細については、[Durable Functions のバージョン](durable-functions-versions.md)に関する記事を参照してください。

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
      "type": "durableClient",
      "direction": "in"
    }
  ]
}
```

> [!NOTE]
> 前記の JSON は Durable Functions 2.x 用です。 Durable Functions 1.x の場合、トリガー型として `durableClient` の代わりに `orchestrationClient` を使用する必要があります。 バージョン間の相違点の詳細については、[Durable Functions のバージョン](durable-functions-versions.md)に関する記事を参照してください。

新しいオーケストレーター関数のインスタンスを開始する言語固有のサンプルを次に示します。

#### <a name="c-script-sample"></a>C# スクリプトのサンプル

次の例は、永続的なオーケストレーション クライアントのバインドを使用して、キューによってトリガーされる C# 関数から新しい関数インスタンスを開始する方法を示しています。

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

using Microsoft.Azure.WebJobs.Extensions.DurableTask;

public static Task Run(string input, IDurableOrchestrationClient starter)
{
    return starter.StartNewAsync("HelloWorld", input);
}
```

> [!NOTE]
> 前のコードは Durable Functions 2.x 用です。 Durable Functions 1.x の場合、`IDurableOrchestrationClient` の代わりに `DurableOrchestrationClient` パラメーター型を使用する必要があります。 バージョン間の相違点の詳細については、[Durable Functions のバージョン](durable-functions-versions.md)に関する記事を参照してください。

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
> エンティティ トリガーは、Durable Functions 2.x 以降で使用できます。

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

* **EntityName**: 現在実行中のエンティティの名前です。
* **EntityKey**: 現在実行中のエンティティのキーです。
* **EntityId**: 現在実行中のエンティティの ID です。
* **OperationName**: 現在の操作の名前です。
* **HasState**: エンティティが存在するかどうか、つまり、何らかの状態にあるかどうか。 
* **GetState\<TState>()** : エンティティの現在の状態を取得します。 まだ存在しない場合は、作成されて、`default<TState>` に初期化されます。 `TState` パラメーターは、プリミティブ型または JSON にシリアル化できる型にする必要があります。 
* **GetState\<TState>(initfunction)** : エンティティの現在の状態を取得します。 まだ存在しない場合は、指定された `initfunction` パラメーターを呼び出して作成されます。 `TState` パラメーターは、プリミティブ型または JSON にシリアル化できる型にする必要があります。 
* **SetState(arg)** : エンティティの状態を作成または更新します。 `arg` パラメーターは、JSON にシリアル化できるオブジェクトまたはプリミティブにする必要があります。
* **DeleteState()** : エンティティの状態を削除します。 
* **GetInput\<TInput>()** : 現在の操作に対する入力を取得します。 `TInput` 型パラメーターは、プリミティブ型または JSON にシリアル化できる型にする必要があります。
* **Return(arg)** : 操作を呼び出したオーケストレーションに値を返します。 `arg` パラメーターは、プリミティブ オブジェクトまたは JSON にシリアル化できるオブジェクトにする必要があります。
* **SignalEntity(EntityId, scheduledTimeUtc, operation, input)** : エンティティに一方向のメッセージを送信します。 `operation` パラメーターは NULL 以外の文字列にする必要があります。オプションの `scheduledTimeUtc` は、操作を呼び出す UTC 日時にする必要があります。`input` パラメーターはプリミティブまたは JSON にシリアル化できるオブジェクトにする必要があります。
* **CreateNewOrchestration(orchestratorFunctionName, input)** : 新しいオーケストレーションを開始します。 `input` パラメーターは、プリミティブ オブジェクトまたは JSON にシリアル化できるオブジェクトにする必要があります。

エンティティ関数に渡される `IDurableEntityContext` オブジェクトには、`Entity.Current` 非同期ローカル プロパティを使用してアクセスできます。 この方法は、クラスベースのプログラミング モデルを使用する場合に便利です。

### <a name="trigger-sample-c-function-based-syntax"></a>トリガーのサンプル (C# 関数ベースの構文)

次のコードは、持続的関数として実装されているシンプルな *Counter* エンティティの例です。 この関数では 3 つの操作 `add`、`reset`、`get` が定義されていて、いずれも整数の状態に対して動作します。

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            ctx.SetState(ctx.GetState<int>() + ctx.GetInput<int>());
            break;
        case "reset":
            ctx.SetState(0);
            break;
        case "get":
            ctx.Return(ctx.GetState<int>()));
            break;
    }
}
```

関数ベースの構文と使用方法の詳細については、「[Function ベースの構文](durable-functions-dotnet-entities.md#function-based-syntax)」を参照してください。

### <a name="trigger-sample-c-class-based-syntax"></a>トリガーのサンプル (C# クラスベースの構文)

次の例は、クラスとメソッドを使用した `Counter` エンティティの同等の実装です。

```csharp
[JsonObject(MemberSerialization.OptIn)]
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

このエンティティの状態は `Counter` 型のオブジェクトになっています。これには、カウンターの現在の値を格納するフィールドが含まれます。 このオブジェクトは、ストレージに保持するために、[Json.NET](https://www.newtonsoft.com/json) ライブラリによってシリアル化および逆シリアル化されます。 

クラスベースの構文とその使用方法の詳細については、「[エンティティ クラスの定義](durable-functions-dotnet-entities.md#defining-entity-classes)」を参照してください。

> [!NOTE]
> エンティティ クラスを使用するときは、`[FunctionName]` 属性を持つ関数エントリ ポイント メソッドを、`static` と宣言する "*必要があります*"。 非静的なエントリ ポイント メソッドを使用すると、複数のオブジェクトが初期化されたり、他の未定義の動作が発生したりする可能性があります。

エンティティ クラスには、バインディングおよび .NET 依存関係の挿入を対話処理するための特別なメカニズムがあります。 詳細については、「[エンティティの構築](durable-functions-dotnet-entities.md#entity-construction)」をご覧ください。

### <a name="trigger-sample-javascript"></a>トリガーのサンプル (JavaScript)

次のコードは、JavaScript で記述した持続的関数として実装されているシンプルな *Counter* エンティティの例です。 この関数では 3 つの操作 `add`、`reset`、`get` が定義されていて、いずれも整数の状態に対して動作します。

**function.json**
```json
{
  "bindings": [
    {
      "name": "context",
      "type": "entityTrigger",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

**index.js**
```javascript
const df = require("durable-functions");

module.exports = df.entity(function(context) {
    const currentValue = context.df.getState(() => 0);
    switch (context.df.operationName) {
        case "add":
            const amount = context.df.getInput();
            context.df.setState(currentValue + amount);
            break;
        case "reset":
            context.df.setState(0);
            break;
        case "get":
            context.df.return(currentValue);
            break;
    }
});
```

> [!NOTE]
> `durable-functions` npm パッケージのバージョン **1.3.0** 以降では、JavaScript で持続エンティティが利用できます。

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
    "direction": "in"
}
```

* `taskHub` - 複数の関数アプリが同じストレージ アカウントを共有するが、相互に分離する必要があるシナリオで使用されます。 指定されていない場合は、`host.json` の既定値が使用されます。 この値は、ターゲットのエンティティ関数によって使用される値と一致している必要があります。
* `connectionName` - ストレージ アカウント接続文字列を含むアプリ設定の名前。 この接続文字列で表されるストレージ アカウントは、ターゲットのエンティティ関数によって使用されるものと同じにする必要があります。 指定されない場合は、関数アプリの既定のストレージ アカウント接続文字列が使用されます。

> [!NOTE]
> ほとんどの場合、オプションのプロパティを省略し、既定の動作を使用することをお勧めします。

### <a name="entity-client-usage"></a>エンティティ クライアントの使用

.NET 関数では、通常は、`IDurableEntityClient` にバインドします。これにより、持続エンティティによってサポートされるすべてのクライアント API にフル アクセスできます。 また、`IDurableOrchestrationClient` インターフェイスにバインドすることもできます。これにより、エンティティとオーケストレーションの両方のクライアント API にアクセスできるようになります。 クライアント オブジェクトの API には以下が含まれます。

* **ReadEntityStateAsync\<T>** : エンティティの状態を読み取ります。 ターゲット エンティティが存在するかどうかを示す応答が返されます。存在する場合は、その状態も返されます。
* **SignalEntityAsync**: エンティティに一方向のメッセージを送信し、エンキューされるまで待機します。
* **ListEntitiesAsync**: 複数のエンティティの状態をクエリします。 エンティティは、*name* と *last operation time* でクエリできます。

シグナルを送信する前にターゲット エンティティを作成する必要はありません。エンティティの状態は、シグナルを処理するエンティティ関数内で作成できます。

> [!NOTE]
> クライアントから送信された "シグナル" は、単にエンキューされ、後で非同期的に処理されることを理解しておくことが重要です。 特に、`SignalEntityAsync` は、通常、エンティティが操作を開始する前に戻ります。戻り値を取得したり、例外を監視したりすることはできません。 より強力な保証が必要な場合 (ワークフローの場合など)、*オーケストレーター関数* を使用する必要があります。これにより、エンティティ操作の完了を待機し、戻り値を処理して例外を監視することができます。

### <a name="example-client-signals-entity-directly---c"></a>例: クライアントがエンティティにシグナル通知を直接出す - C#

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

### <a name="example-client-signals-entity-via-interface---c"></a>例: クライアントがインターフェイスを介してエンティティにシグナル通知を出す - C#

可能な場合は、より多くの型チェックが提供されるので、[インターフェイスを介してエンティティにアクセスする](durable-functions-dotnet-entities.md#accessing-entities-through-interfaces)ことをお勧めします。 たとえば、前述の `Counter` エンティティによって、次のように定義された `ICounter` インターフェイスが実装されたとします。

```csharp
public interface ICounter
{
    void Add(int amount);
    void Reset();
    Task<int> Get();
}

public class Counter : ICounter
{
    // ...
}
```

クライアント コードでは、`SignalEntityAsync<ICounter>` を使用してタイプセーフなプロキシを生成できます。

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

`proxy` パラメーターは動的に生成された `ICounter` のインスタンスであり、`Add` への呼び出しを、`SignalEntityAsync` への同等の (型指定されていない) 呼び出しに内部的に変換します。

> [!NOTE]
> `SignalEntityAsync` API は一方向の操作を表します。 エンティティ インターフェイスから `Task<T>` が返された場合、`T` パラメーターの値は常に null または `default` になります。

特に、値が返されないため、`Get` 操作にシグナル通知を出すことは意味がありません。 代わりに、クライアントは `ReadStateAsync` を使用してカウンターの状態に直接アクセスするか、`Get` 操作を呼び出すオーケストレーター関数を開始できます。

### <a name="example-client-signals-entity---javascript"></a>例: クライアントがエンティティにシグナル通知を出す - JavaScript

次に示すのは、キューによってトリガーされ "Counter" エンティティにシグナル通知を出す JavaScript 関数の例です。

**function.json**
```json
{
    "bindings": [
      {
        "name": "input",
        "type": "queueTrigger",
        "queueName": "durable-entity-trigger",
        "direction": "in",
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

**index.js**
```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    await context.df.signalEntity(entityId, "add", 1);
};
```

> [!NOTE]
> `durable-functions` npm パッケージのバージョン **1.3.0** 以降では、JavaScript で持続エンティティが利用できます。

<a name="host-json"></a>
## <a name="hostjson-settings"></a>host.json 設定

[!INCLUDE [durabletask](../../../includes/functions-host-json-durabletask.md)]

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [インスタンス管理用の組み込みの HTTP API リファレンス](durable-functions-http-api.md)
