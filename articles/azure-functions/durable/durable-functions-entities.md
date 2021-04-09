---
title: 持続エンティティ - Azure Functions
description: 持続エンティティとはどのようなもので、Azure Functions 用の Durable Functions 拡張機能でどのように使用するかについて説明します。
author: cgillum
ms.topic: overview
ms.date: 12/17/2019
ms.author: azfuncdf
ms.openlocfilehash: c898444659c2ce071163e9ab774a4534f8c51a9c
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102632052"
---
# <a name="entity-functions"></a>エンティティ関数

エンティティ関数では、"*持続エンティティ*" と呼ばれる小さい状態の読み取りと更新のための操作が定義されています。 オーケストレーター関数と同様、エンティティ関数は特殊なトリガー型である *エンティティ トリガー* を含む関数です。 オーケストレーター関数とは異なり、エンティティ関数では、制御フローを介して状態を暗黙的に表すのではなく、エンティティの状態を明示的に管理します。
エンティティは、それぞれが適度なサイズの状態を備えた多数のエンティティ全体に作業を分散することにより、アプリケーションをスケールアウトする手段を提供します。

> [!NOTE]
> エンティティ関数と関連する機能は [Durable Functions 2.0](durable-functions-versions.md#migrate-from-1x-to-2x) 以降でのみ使用できます。 現在、.NET、JavaScript、Python でサポートされています。

## <a name="general-concepts"></a>一般的な概念

エンティティは、メッセージを介して通信する小さなサービスと同じように動作します。 各エンティティは、一意の ID と内部状態 (存在する場合) を備えています。 サービスやオブジェクトと同様に、エンティティは、要求されたときに操作を実行します。 操作を実行すると、エンティティの内部状態が更新されることがあります。 また、外部サービスを呼び出して応答を待つこともあります。 エンティティは、リライアブル キューを介して暗黙的に送信されるメッセージを使用して、他のエンティティ、オーケストレーション、およびクライアントと通信します。 

競合を防ぐために、1 つのエンティティに対するすべての操作は、直列に (つまり順番に) 実行されることが保証されます。

> [!NOTE]
> 呼び出されたエンティティにより、ペイロードが完了に処理された後、後で入力が到着したらアクティブになるように新しい実行がスケジュールされます。 その結果、エンティティの実行ログで、各エンティティの呼び出しの後に追加の実行が示されることがあります。これは想定されることです。

### <a name="entity-id"></a>エンティティ ID
エンティティには、一意の識別子である "*エンティティ ID*" を介してアクセスします。 エンティティ ID は単に、エンティティのインスタンスを一意に示す文字列のペアです。 構成は次のとおりです。

* **エンティティ名**: エンティティの種類を示す名前です。 たとえば、"Counter" などです。 この名前は、エンティティを実装するエンティティ関数の名前と一致している必要があります。 大文字と小文字は区別されません。
* **エンティティ キー**: 同じ名前の他のすべてのエンティティの間でそのエンティティを一意に示す文字列です。 たとえば、GUID などです。

たとえば、`Counter` エンティティ関数は、オンライン ゲームでスコアを保持するために使用される可能性があります。 ゲームの各インスタンスは、`@Counter@Game1` や `@Counter@Game2` などの一意のエンティティ ID を持ちます。 特定のエンティティを対象とするすべての操作では、エンティティ ID をパラメーターとして指定する必要があります。

### <a name="entity-operations"></a>エンティティの操作 ###

エンティティで操作を呼び出すには、次を指定します。

* ターゲット エンティティの **エンティティ ID**。
* **操作名**: 実行する操作を指定する文字列です。 たとえば、`Counter` エンティティでは、`add`、`get`、または `reset` 操作をサポートする場合があります。
* **操作の入力**: 操作のオプションの入力パラメーターです。 たとえば、add 操作では入力として整数値を受け取ることができます。
* **スケジュール時刻**: 操作の配信時刻を指定するためのオプション パラメーターです。 たとえば、将来、数日間にわたって操作を実行するよう確実にスケジュールすることができます。

操作では、結果値またはエラー結果 (JavaScript エラーや .NET 例外など) を返すことができます。 この結果またはエラーは、操作を呼び出したオーケストレーションによって確認できます。

エンティティ操作では、エンティティの状態の作成、読み取り、更新、および削除を行うこともできます。 エンティティの状態は、常にストレージ内に持続的に保持されます。

## <a name="define-entities"></a>エンティティの定義

現在、エンティティを定義するための 2 つの異なる API は次のとおりです。

**関数ベースの構文**: エンティティは関数として表され、操作はアプリケーションによって明示的にディスパッチされます。 この構文は、単純な状態のエンティティ、操作が少ないエンティティ、または動的な操作のセット (アプリケーション フレームワーク内のような) を備えたエンティティに適しています。 この構文は、コンパイル時に型エラーがキャッチされないため、保守が煩雑になる可能性があります。

**クラスベースの構文 (.NET のみ)** : エンティティおよび操作はクラスおよびメソッドによって表されます。 この構文では、より読みやすいコードが生成され、タイプ セーフな方法で操作を呼び出すことができます。 クラスベースの構文は、関数ベースの構文の上にあるシン レイヤーであるため、同じアプリケーションで両方のバリアントを同じ意味で使用できます。

# <a name="c"></a>[C#](#tab/csharp)

### <a name="example-function-based-syntax---c"></a>例:関数ベースの構文 - C#

次のコードは、持続的関数として実装されているシンプルな `Counter` エンティティの例です。 この関数では 3 つの操作 `add`、`reset`、`get` が定義されていて、いずれも整数の状態に対して動作します。

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
            ctx.Return(ctx.GetState<int>());
            break;
    }
}
```

関数ベースの構文と使用方法の詳細については、「[関数ベースの構文](durable-functions-dotnet-entities.md#function-based-syntax)」を参照してください。

### <a name="example-class-based-syntax---c"></a>例:クラス ベースの構文 - C#

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

### <a name="example-javascript-entity"></a>例:JavaScript のエンティティ

`durable-functions` npm パッケージのバージョン **1.3.0** 以降では、JavaScript で持続エンティティが利用できます。 以下のコードは、JavaScript で記述されている持続的関数として実装された `Counter` エンティティです。

**Counter (function.json)**
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

**Counter (index.js)**
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
# <a name="python"></a>[Python](#tab/python)

### <a name="example-python-entity"></a>例: Python エンティティ

次のコードは、Python で記述されている持続的関数として実装された `Counter` エンティティです。

**Counter (function.json)**
```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "context",
      "type": "entityTrigger",
      "direction": "in"
    }
  ]
}
```

**Counter/__init__.py**
```Python
import azure.functions as func
import azure.durable_functions as df


def entity_function(context: df.DurableEntityContext):
    current_value = context.get_state(lambda: 0)
    operation = context.operation_name
    if operation == "add":
        amount = context.get_input()
        current_value += amount
    elif operation == "reset":
        current_value = 0
    elif operation == "get":
        context.set_result(current_value)
    context.set_state(current_value)


main = df.Entity.create(entity_function)
```
---

## <a name="access-entities"></a>エンティティへのアクセス

エンティティには、一方向または双方向の通信を使用してアクセスできます。 次の用語は、2 つの通信の形式を区別します。 

* エンティティの **呼び出し** では、双方向 (ラウンドトリップ) 通信が使われます。 操作メッセージをエンティティに送信し、応答メッセージを待機してから続行します。 応答メッセージでは、結果値またはエラー結果 (JavaScript エラーや .NET 例外など) を提供できます。 この結果またはエラーは、呼び出し元によって確認されます。
* エンティティの **シグナル通知** では、一方向 (ファイア アンド フォーゲット) 通信が使われます。 操作メッセージを送信しますが、応答を待ちません。 最終的にメッセージが配信されることは保証されますが、送信側は、それがいつかを認識せず、結果値やエラーを確認することもできません。

エンティティには、クライアント関数内、オーケストレーター関数内、またはエンティティ関数内からアクセスできます。 すべての形式の通信がすべてのコンテキストでサポートされるわけではありません。

* クライアント内からは、エンティティにシグナル通知を出すことができ、エンティティの状態を読み取ることができます。
* オーケストレーション内からは、エンティティにシグナル通知を出すことができ、エンティティを呼び出すことができます。
* エンティティ内からは、エンティティにシグナル通知を出すことができます。

次の例では、エンティティにアクセスするこれらのさまざまな方法を示しています。

### <a name="example-client-signals-an-entity"></a>例:クライアントがエンティティにシグナル通知を出す

通常の Azure 関数 (クライアント関数とも呼ばれます) からエンティティにアクセスするには、[エンティティ クライアントのバインド](durable-functions-bindings.md#entity-client)を使用します。 次の例では、このバインドを使用してエンティティにシグナル通知する、キューによってトリガーされた関数を示します。

# <a name="c"></a>[C#](#tab/csharp)

> [!NOTE]
> わかりやすくするために、次の例では、エンティティにアクセスするための緩やかに型指定された構文を示しています。 一般に、より多くの型チェックが提供されるので、[インターフェイスを介してエンティティにアクセスする](durable-functions-dotnet-entities.md#accessing-entities-through-interfaces)ことをお勧めします。

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    await client.signalEntity(entityId, "add", 1);
};
```

# <a name="python"></a>[Python](#tab/python)

```Python
from azure.durable_functions import DurableOrchestrationClient
import azure.functions as func


async def main(req: func.HttpRequest, starter: str, message):
    client = DurableOrchestrationClient(starter)
    entityId = df.EntityId("Counter", "myCounter")
    await client.signal_entity(entityId, "add", 1)
```

---

"*シグナル通知*" とは、エンティティの API 呼び出しが一方向で非同期であることを意味します。 クライアント関数は、エンティティが操作をいつ処理したかを認識できません。 また、クライアント関数は、結果の値または例外を確認することはできません。 

### <a name="example-client-reads-an-entity-state"></a>例:クライアントがエンティティの状態を読み取る

次の例に示すように、クライアント関数では、エンティティの状態についてクエリを実行することもできます。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("QueryCounter")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function)] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client)
{
    var entityId = new EntityId(nameof(Counter), "myCounter");
    EntityStateResponse<JObject> stateResponse = await client.ReadEntityStateAsync<JObject>(entityId);
    return req.CreateResponse(HttpStatusCode.OK, stateResponse.EntityState);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    const stateResponse = await client.readEntityState(entityId);
    return stateResponse.entityState;
};
```

# <a name="python"></a>[Python](#tab/python)

> [!NOTE]
> 現在、Python でクライアントからエンティティの状態を読み取ることはできません。 代わりに、オーケストレーターの `callEntity` を使用してください。

---

エンティティ状態のクエリは永続性追跡ストアに送信され、エンティティの最後に保存された状態が返されます。 この状態は、常に "コミット済み" 状態です。つまり、操作の実行中に想定される一時的な中間状態ではありません。 ただし、この状態は、エンティティのメモリ内の状態より古い可能性があります。 次のセクションで説明するように、エンティティのメモリ内の状態を読み取ることができるのはオーケストレーションだけです。

### <a name="example-orchestration-signals-and-calls-an-entity"></a>例:オーケストレーションがエンティティにシグナル通知を出し、エンティティを呼び出す

オーケストレーター関数では、[オーケストレーション トリガー バインド](durable-functions-bindings.md#orchestration-trigger)に対する API を使用して、エンティティにアクセスできます。 次のコード例では、オーケストレーター関数による `Counter` エンティティの呼び出しとシグナル通知を示します。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("CounterOrchestration")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var entityId = new EntityId(nameof(Counter), "myCounter");

    // Two-way call to the entity which returns a value - awaits the response
    int currentValue = await context.CallEntityAsync<int>(entityId, "Get");
    if (currentValue < 10)
    {
        // One-way signal to the entity which updates the value - does not await a response
        context.SignalEntity(entityId, "Add", 1);
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    const entityId = new df.EntityId("Counter", "myCounter");

    // Two-way call to the entity which returns a value - awaits the response
    currentValue = yield context.df.callEntity(entityId, "get");
});
```

> [!NOTE]
> JavaScript では現在、オーケストレーターからのエンティティのシグナル通知はサポートされていません。 代わりに `callEntity` を使用してください

# <a name="python"></a>[Python](#tab/python)

```Python
import azure.functions as func
import azure.durable_functions as df


def orchestrator_function(context: df.DurableOrchestrationContext):
    entityId = df.EntityId("Counter", "myCounter")
    current_value = yield context.call_entity(entityId, "get")
    if current_value < 10:
        context.signal_entity(entityId, "add", 1)
    return state
```

---

エンティティを呼び出して応答 (戻り値または例外) を取得できるのは、オーケストレーションだけです。 [クライアント バインド](durable-functions-bindings.md#entity-client)を使用するクライアント関数は、エンティティに対するシグナル通知だけが可能です。

> [!NOTE]
> オーケストレーター関数からのエンティティの呼び出しは、オーケストレーター関数からの[アクティビティ関数](durable-functions-types-features-overview.md#activity-functions)の呼び出しと似ています。 主な違いは、エンティティ関数は、アドレス (エンティティ ID) を持つ持続オブジェクトであることです。 エンティティ関数は、操作名の指定がサポートされています。 それに対し、アクティビティ関数はステートレスであり、操作の概念はありません。

### <a name="example-entity-signals-an-entity"></a>例:エンティティがエンティティにシグナル通知を出す

エンティティ関数は、操作の実行中に他のエンティティ (またはそれ自体) にシグナルを送信することができます。
たとえば、前の `Counter` エンティティの例を変更して、カウンターが値 100 に達したときに "マイルストーン到達" シグナルを一部の監視エンティティに送信することができます。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
   case "add":
        var currentValue = ctx.GetState<int>();
        var amount = ctx.GetInput<int>();
        if (currentValue < 100 && currentValue + amount >= 100)
        {
            ctx.SignalEntity(new EntityId("MonitorEntity", ""), "milestone-reached", ctx.EntityKey);
        }

        ctx.SetState(currentValue + amount);
        break;
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
    case "add":
        const amount = context.df.getInput();
        if (currentValue < 100 && currentValue + amount >= 100) {
            const entityId = new df.EntityId("MonitorEntity", "");
            context.df.signalEntity(entityId, "milestone-reached", context.df.instanceId);
        }
        context.df.setState(currentValue + amount);
        break;
```

# <a name="python"></a>[Python](#tab/python)

> [!NOTE]
> Python では、エンティティ間の通知はまだサポートされていません。 エンティティの通知には、代わりにオーケストレーターを使用してください。

---

## <a name="entity-coordination-currently-net-only"></a><a name="entity-coordination"></a>エンティティの調整 (現在 .NET のみ)

複数のエンティティ間で操作を調整することが必要になる場合があります。 たとえば、銀行のアプリケーションでは、個々の銀行口座を表すエンティティがある場合があります。 口座間で送金を行う場合は、送金元口座に十分な資金があることを確認する必要があります。 さらに、送金元口座と送金先口座の両方に対する更新がトランザクション的に一貫した方法で実行されることを確認する必要もあります。

### <a name="example-transfer-funds-c"></a>例:送金 (C#)

次のコード例では、オーケストレーター関数を使用して、2 つの口座エンティティ間で送金を行います。 エンティティの更新を調整するには、`LockAsync` メソッドを使用して、オーケストレーション内に _クリティカル セクション_ を作成する必要があります。

> [!NOTE]
> わかりやすくするため、この例では、前に定義した `Counter` エンティティを再利用します。 実際のアプリケーションでは、より詳細な `BankAccount` エンティティを定義することをお勧めします。

```csharp
// This is a method called by an orchestrator function
public static async Task<bool> TransferFundsAsync(
    string sourceId,
    string destinationId,
    int transferAmount,
    IDurableOrchestrationContext context)
{
    var sourceEntity = new EntityId(nameof(Counter), sourceId);
    var destinationEntity = new EntityId(nameof(Counter), destinationId);

    // Create a critical section to avoid race conditions.
    // No operations can be performed on either the source or
    // destination accounts until the locks are released.
    using (await context.LockAsync(sourceEntity, destinationEntity))
    {
        ICounter sourceProxy = 
            context.CreateEntityProxy<ICounter>(sourceEntity);
        ICounter destinationProxy =
            context.CreateEntityProxy<ICounter>(destinationEntity);

        int sourceBalance = await sourceProxy.Get();

        if (sourceBalance >= transferAmount)
        {
            await sourceProxy.Add(-transferAmount);
            await destinationProxy.Add(transferAmount);

            // the transfer succeeded
            return true;
        }
        else
        {
            // the transfer failed due to insufficient funds
            return false;
        }
    }
}
```

.NET の `LockAsync` では、破棄されるとクリティカル セクションが終了される `IDisposable` が返されます。 この `IDisposable` の結果を `using` ブロックと共に使用して、クリティカル セクションの構文表現を取得できます。

前の例では、オーケストレーター関数を使用して、送金元エンティティから送金先エンティティに送金を行いました。 `LockAsync` メソッドにより、送金元と送金先の両方の口座エンティティがロックされました。 このロックにより、オーケストレーションのロジックが `using` ステートメントの最後でクリティカル セクションを終了するまで、他のクライアントによってどちらの口座の状態も照会または変更されないことが保証されました。 この動作により、送金元口座からの過剰な送金の可能性を防ぐことができます。

> [!NOTE] 
> オーケストレーションが (正常に、またはエラーで) 終了すると、進行中のすべてのクリティカル セクションが暗黙的に終了し、すべてのロックが解除されます。

### <a name="critical-section-behavior"></a>クリティカル セクションの動作

`LockAsync` メソッドによって、オーケストレーション内にクリティカル セクションが作成されます。 これらのクリティカル セクションにより、指定されているエンティティのセットに対して他のオーケストレーションにより重複する変更が行われることを防止できます。 内部的には、`LockAsync` API ではエンティティに "ロック" 操作が送信され、同じこれらの各エンティティから "ロック取得済み" 応答メッセージを受信するとその API は制御を返します。 ロックとロック解除はどちらも、すべてのエンティティでサポートされている組み込み操作です。

ロック状態にあるエンティティに対して、他のクライアントからの操作は許可されません。 この動作により、一度に 1 つのオーケストレーション インスタンスだけがエンティティをロックできるようになります。 オーケストレーションによってロックされているエンティティに対して呼び出し元が操作を呼び出そうとすると、その操作は保留中操作キューに配置されます。 保持しているオーケストレーションがロックを解放するまで、保留中の操作は処理されません。

> [!NOTE] 
> この動作は、C# の `lock` ステートメントなど、ほとんどのプログラミング言語で使用される同期プリミティブとは若干異なります。 たとえば、C# では、複数のスレッド間で同期が確実に行われるようにするには、すべてのスレッドで `lock` ステートメントを使用する必要があります。 一方、エンティティでは、すべての呼び出し元が明示的にエンティティをロックする必要はありません。 いずれかの呼び出し元によってエンティティがロックされた場合、そのエンティティに対する他のすべての操作はブロックされ、キューでそのロックの後に配置されます。

エンティティのロックは持続的であるため、実行プロセスがリサイクルされた場合でも永続化されます。 ロックは、エンティティの持続状態の一部として内部的に保持されます。

トランザクションとは異なり、クリティカル セクションでは、エラーが発生した場合に変更が自動的にロールバックされません。 代わりに、たとえばエラーまたは例外をキャッチすることにより、エラー処理 (ロールバックや再試行など) を明示的にコーディングする必要があります。 この設計の選択は意図的なものです。 一般に、オーケストレーションのすべての効果を自動的にロールバックすることは困難であるか不可能です。これは、オーケストレーションによってアクティビティが実行され、ロールバックできない外部サービスへの呼び出しが行われることがあるためです。 また、ロールバックの試み自体が失敗し、さらにエラー処理が必要になる場合があります。

### <a name="critical-section-rules"></a>クリティカル セクションのルール

ほとんどのプログラミング言語の低レベルのロック プリミティブとは異なり、クリティカル セクションは、*デッドロックが発生しないことが保証されます*。 デッドロックを防ぐために、次の制限が適用されます。 

* クリティカル セクションを入れ子にすることはできません。
* クリティカル セクションでサブオーケストレーションを作成することはできません。
* クリティカル セクションでは、自分がロックしているエンティティのみを呼び出すことができます。
* クリティカル セクションでは、複数の並列呼び出しを使用して、同じエンティティを呼び出すことはできません。
* クリティカル セクションでは、自分がロックしていないエンティティのみにシグナルを送信できます。

これらのルールに違反すると、どのルールに違反していたかを説明するメッセージが含まれる実行時エラー (.NET の `LockingRulesViolationException` など) が発生します。

## <a name="comparison-with-virtual-actors"></a>仮想アクターとの比較

持続エンティティの多くの機能は、[アクター モデル](https://en.wikipedia.org/wiki/Actor_model)に基づいています。 アクターについて既によくわかっている場合は、この記事で説明されている多くの概念を理解できます。 持続エンティティは、[Orleans プロジェクト](http://dotnet.github.io/orleans/)によって普及した[仮想アクター](https://research.microsoft.com/projects/orleans/)またはグレインに特に似ています。 次に例を示します。

* 持続エンティティは、エンティティ ID を使用してアドレス指定可能です。
* 持続エンティティの操作は、競合状態を避けるため、一度に 1 つずつ順番に実行されます。
* 持続エンティティは、呼び出されるか、シグナル通知を受け取ると、暗黙的に作成されます。
* 実行する操作がなくなると、持続エンティティはメモリからサイレントにアンロードされます。

いくつか注目すべき重要な違いがあります。

* 持続エンティティは、待ち時間より持続性の方が優先されるので、待ち時間の要件が厳しいアプリケーションには適していない可能性があります。
* 持続エンティティには、メッセージの組み込みのタイムアウトがありません。 Orleans では、構成可能な時間後にすべてのメッセージがタイムアウトします。 既定値は 30 秒です。
* エンティティ間で送信されるメッセージは、信頼性の高い方法で順番に配信されます。 Orleans では、ストリームを介して送信されるコンテンツの信頼性の高い配信または順次配信がサポートされますが、グレイン間のすべてのメッセージについては保証されません。
* エンティティでの要求 - 応答パターンは、オーケストレーションに制限されます。 エンティティ内からは、元のアクター モデルのように、そして Orleans のグレインとは異なり、一方向のメッセージ (シグナル通知とも呼ばれます) のみが許可されます。 
* 持続エンティティではデッドロックは発生しません。 Orleans では、デッドロックが発生する可能性があり、メッセージがタイムアウトするまで解決されません。
* 持続エンティティは、持続オーケストレーションと組み合わせて使用でき、分散ロック メカニズムがサポートされます。 

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [.NET の持続エンティティに関する開発者ガイドを読む](durable-functions-dotnet-entities.md)

> [!div class="nextstepaction"]
> [タスク ハブについて確認する](durable-functions-task-hubs.md)
