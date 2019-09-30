---
title: 持続エンティティ - Azure Functions
description: 持続エンティティとはどのようなもので、Azure Functions 用の Durable Functions 拡張機能でどのように使用するかについて説明します。
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: overview
ms.date: 08/31/2019
ms.author: azfuncdf
ms.openlocfilehash: 99e61cef55bd97704063e4d2da90909d0376c327
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/12/2019
ms.locfileid: "70961460"
---
# <a name="entity-functions-preview"></a>エンティティ関数 (プレビュー)

エンティティ関数では、"*持続エンティティ*" と呼ばれる小さい状態の読み取りと更新のための操作が定義されています。 オーケストレーター関数と同様、エンティティ関数は特殊なトリガー型である "*エンティティ トリガー*" を含む関数です。 オーケストレーター関数とは異なり、エンティティ関数には特定のコードの制約はありません。 また、エンティティ関数では、制御フローを介して状態を表す暗黙的ではなく、明示的に状態が管理されます。

> [!NOTE]
> エンティティ関数と関連する機能は Durable Functions 2.0 以降でのみ使用できます。 エンティティ関数は現在、パブリック プレビューの段階にあります。

## <a name="entity-identity"></a>エンティティ ID

エンティティ (エンティティ "*インスタンス*" と呼ばれることがあります) には、一意の識別子である "*エンティティ ID*" を使用してアクセスします。 エンティティ ID は単に、エンティティのインスタンスを一意に示す文字列のペアです。 構成は次のとおりです。

* **エンティティ名**: エンティティの種類を示す名前 (例: "Counter")。
* **エンティティ キー**: 同じ名前の他のすべてのエンティティの間でそのエンティティを一意に示す文字列 (例: GUID)。

たとえば、"*カウンター*" エンティティ関数は、オンライン ゲームでスコアを保持するために使用される可能性があります。 ゲームの各インスタンスは、`@Counter@Game1`、`@Counter@Game2` のような一意のエンティティ ID を持ちます。 特定のエンティティを対象とするすべての操作では、エンティティ ID をパラメーターとして指定する必要があります。

## <a name="programming-models"></a>プログラミング モデル

持続エンティティでは、2 つの異なるプログラミング モデルがサポートされています。 最初のモデルは動的な "機能" モデルで、1 つの関数によってエンティティが定義されます。 2 番目のモデルはオブジェクト指向モデルで、エンティティはクラスとメソッドによって定義されます。 以下のセクションでは、これらのモデルおよびエンティティと対話するためのプログラミング モデルについて説明します。

### <a name="defining-entities"></a>エンティティの定義

持続性のあるエンティティを作成するためのオプションのプログラミング モデルが 2 つあります。 次のコードは、標準関数として実装されているシンプルな *Counter* エンティティの例です。 この関数では 3 つの "*操作*" `add`、`reset`、`get` が定義されており、いずれでも整数の状態値 `currentValue` が操作されます。

```csharp
[FunctionName("Counter")]
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

このモデルは、単純なエンティティの実装や、操作の動的なセットを持つ実装に最適です。 ただし、静的だが実装がより複雑なエンティティに役立つクラスベースのプログラミング モデルも使用できます。 次の例は、クラスとメソッドを使用した `Counter` エンティティの同等の実装です。

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
> エンティティ クラスを使用するときは、`[FunctionName]` 属性を持つ関数エントリ ポイント メソッドを、`static` と宣言する "*必要があります*"。 非静的なエントリ ポイント メソッドでは、複数のオブジェクトが初期化されたり、他の未定義の動作が発生したりする可能性があります。

クラスベースのプログラミング モデルでは、`IDurableEntityContext` オブジェクトを `Entity.Current` 静的プロパティで使用できます。

クラスベースのモデルは、[Orleans](https://www.microsoft.com/research/project/orleans-virtual-actors/) によって実用化されているプログラミング モデルに似ています。 このモデルでは、エンティティ型は .NET クラスとして定義されます。 クラスの各メソッドは、外部クライアントによって呼び出すことができる操作です。 ただし、Orleans とは異なり、.NET インターフェイスは省略可能です。 前の *Counter* の例では、インターフェイスを使用しませんでしたが、他の関数または HTTP API 呼び出しを介して呼び出すことができます。

> [!NOTE]
> エンティティ トリガー関数は、Durable Functions 2.0 以降で使用できます。 現在、エンティティ トリガー関数は、.NET 関数アプリでのみ使用できます。

### <a name="accessing-entities-from-clients"></a>クライアントからエンティティへのアクセス

通常の関数 ("*クライアント関数*" とも呼ばれます) では、[エンティティ クライアント出力バインド](durable-functions-bindings.md#entity-client)を使用して、持続エンティティを呼び出したり、持続エンティティのクエリを実行したりできます。 次の例では、このバインドを使用してエンティティに "*シグナル通知する*"、キューによってトリガーされた関数を示します。

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

> [!NOTE]
> .NET 関数では、エンティティへの通知に対して、緩やかに型指定されたメソッドとタイプセーフなメソッドの両方がサポートされています。 詳しくは、[エンティティ クライアントのバインド](durable-functions-bindings.md#entity-client-usage)に関する参照ドキュメントをご覧ください。

"*シグナル通知*" とは、エンティティの API 呼び出しが一方向で非同期であることを意味します。 "*クライアント関数*" でエンティティによる操作の処理が終了したことを認識することも、エンティティ関数でクライアント関数に値を返すこともできません。 一方向のキューベースのメッセージングは、持続エンティティでパフォーマンスより持続性を優先するために意図的に選択された設計です。 この設計の選択は、持続エンティティと他の同様のテクノロジを比較したときの、トレードオフの 1 つです。 次のセクションで説明するように、現在は、オーケストレーションだけがエンティティからの戻り値を処理できます。

次の例に示すように、クライアント関数では、エンティティの状態のクエリを実行することもできます。

```csharp
[FunctionName("QueryCounter")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function)] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client)
{
    var entityId = new EntityId(nameof(Counter), "myCounter");
    JObject state = await client.ReadEntityStateAsync<JObject>(entityId);
    return req.CreateResponse(HttpStatusCode.OK, state);
}
```

エンティティ状態のクエリは永続性追跡ストアに送信され、エンティティの最後に "*保存された*" 状態が返されます。 返される状態は、エンティティのメモリ内の状態より古いものである可能性があります。 次のセクションで説明するように、エンティティのメモリ内の状態を読み取ることができるのはオーケストレーションだけです。

### <a name="accessing-entities-from-orchestrations"></a>オーケストレーションからエンティティへのアクセス

オーケストレーター関数では、"[オーケストレーション トリガー バインド](durable-functions-bindings.md#orchestration-trigger)" に対する API を使用して、エンティティにアクセスできます。 オーケストレーター関数では、一方向通信 (ファイア アンド フォーゲット、"*シグナル通知*" とも呼ばれます) または双方向通信 (要求と応答、"*呼び出し*" とも呼ばれます) のいずれかを選択できます。 次のコード例では、オーケストレーター関数での *Counter* エンティティの "*呼び出し*" と "*シグナル通知*" を示します。

```csharp
[FunctionName("CounterOrchestration")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var entityId = new EntityId(nameof(Counter), "myCounter");

    // Synchronous call to the entity which returns a value
    int currentValue = await context.CallEntityAsync<int>(entityId, "Get");
    if (currentValue < 10)
    {
        // Asynchronous call which updates the value
        await context.SignalEntityAsync<int>(entityId, "Add", 1);
    }
}
```

エンティティを呼び出して応答 (戻り値または例外) を取得できるのは、オーケストレーションだけです。 [クライアント バインド](durable-functions-bindings.md#entity-client)を使用するクライアント関数は、エンティティに対する "*シグナル通知*" だけが可能です。

> [!NOTE]
> オーケストレーター関数からのエンティティの呼び出しは、オーケストレーター関数からの[アクティビティ関数](durable-functions-types-features-overview.md#activity-functions)の呼び出しと似ています。 主な違いは、エンティティ関数は、アドレス ("*エンティティ ID*") を持つ持続オブジェクトであり、操作名の指定がサポートされていることです。 それに対し、アクティビティ関数はステートレスであり、操作の概念はありません。

### <a name="dependency-injection-in-entity-classes-net"></a>エンティティ クラスでの依存関係の挿入 (.NET)

エンティティ クラスでは、[Azure Functions の依存関係の挿入](../functions-dotnet-dependency-injection.md)がサポートされています。 次の例では、`IHttpClientFactory` サービスをクラスベースのエンティティに登録する方法を示します。

```csharp
[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddHttpClient();
        }
    }
}
```

次のスニペットでは、挿入されたサービスをエンティティ クラスに組み込む方法を示します。

```csharp
public class HttpEntity
{
    private readonly HttpClient client;

    public class HttpEntity(IHttpClientFactory factory)
    {
        this.client = factory.CreateClient();
    }

    public Task<int> GetAsync(string url)
    {
        using (var response = await this.client.GetAsync(url))
        {
            return (int)response.StatusCode;
        }
    }

    // The function entry point must be declared static
    [FunctionName(nameof(HttpEntity))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<HttpEntity>();
}
```

> [!NOTE]
> 通常の .NET Azure Functions でコンストラクターの挿入を使用する場合とは異なり、クラスベースのエンティティに対する関数のエントリ ポイント メソッドは、`static` と宣言する "*必要があります*"。 非静的な関数エントリ ポイントを宣言すると、通常の Azure Functions オブジェクト初期化子と永続エンティティ オブジェクト初期化子との間で競合が発生する可能性があります。

### <a name="bindings-in-entity-classes-net"></a>エンティティ クラスでのバインド (.NET)

通常の関数とは異なり、エンティティ クラスのメソッドでは、入力と出力のバインドに直接アクセスできません。 代わりに、エントリ ポイント関数の宣言でバインド データをキャプチャした後、`DispatchAsync<T>` メソッドに渡す必要があります。 `DispatchAsync<T>`に渡されたオブジェクトは、エンティティ クラスのコンストラクターに引数として自動的に渡されます。

次の例では、[BLOB 入力バインド](../functions-bindings-storage-blob.md#input)からの `CloudBlobContainer` 参照をクラス ベースのエンティティで使用できるようにする方法を示します。

```csharp
public class BlobBackedEntity
{
    private readonly CloudBlobContainer container;

    public BlobBackedEntity(CloudBlobContainer container)
    {
        this.container = container;
    }

    // ... entity methods can use this.container in their implementations ...
    
    [FunctionName(nameof(BlobBackedEntity))]
    public static Task Run(
        [EntityTrigger] IDurableEntityContext context,
        [Blob("my-container", FileAccess.Read)] CloudBlobContainer container)
    {
        // passing the binding object as a parameter makes it available to the
        // entity class constructor
        return context.DispatchAsync<BlobBackedEntity>(container);
    }
}
```

Azure Functions でのバインドについて詳しくは、[Azure Functions のトリガーとバインド](../functions-triggers-bindings.md)に関するドキュメントをご覧ください。

## <a name="entity-coordination"></a>エンティティの調整

複数のエンティティ間で操作を調整することが必要になる場合があります。 たとえば、銀行のアプリケーションでは、個々の銀行口座を表すエンティティがある場合があります。 口座間で送金を行う場合は、"_送金元_" 口座に十分な資金があること、および "_送金元_" 口座と "_送金先_" 口座の両方に対する更新がトランザクション的に一貫した方法で実行されされることを確保する必要があります。

### <a name="transfer-funds-example-in-c"></a>C# での送金の例

次のコード例では、オーケストレーター関数を使用して、2 つの "_口座_" エンティティ間で送金を行います。 エンティティの更新を調整するには、`LockAsync` メソッドを使用して、オーケストレーション内に "_クリティカル セクション_" を作成する必要があります。

> [!NOTE]
> わかりやすくするため、この例では、前に定義した `Counter` エンティティを再利用します。 ただし、実際のアプリケーションでは、より詳細な `BankAccount` エンティティを定義することをお勧めします。

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

前の例では、オーケストレーター関数を使用して、"_送金元_" エンティティから "_送金先_" エンティティに送金を行いました。 `LockAsync` メソッドにより、"_送金元_" と "_送金先_" の両方の口座エンティティがロックされました。 このロックにより、オーケストレーションのロジックが `using` ステートメントの最後で "_クリティカル セクション_" を終了するまで、他のクライアントによってどちらの口座の状態も照会または変更されないことが保証されました。 これにより、"_送金元_" 口座からの過剰な送金の可能性を防ぐことができました。

### <a name="critical-section-behavior"></a>クリティカル セクションの動作

`LockAsync` メソッドによって、オーケストレーション内に "_クリティカル セクション_" が作成されます。 これらの "_クリティカル セクション_" により、指定されているエンティティのセットに対して他のオーケストレーションにより重複する変更が行われることを防止できます。 内部的には、`LockAsync` API ではエンティティに "ロック" 操作が送信され、同じこれらの各エンティティから "ロック取得済み" 応答メッセージを受信するとその API は制御を返します。 "*ロック*" と "*ロック解除*" はどちらも、すべてのエンティティでサポートされている組み込み操作です。

ロック状態にあるエンティティに対して、他のクライアントからの操作は許可されません。 この動作により、一度に 1 つのオーケストレーション インスタンスだけがエンティティをロックできるようになります。 オーケストレーションによってロックされているエンティティに対して呼び出し元が操作を呼び出そうとすると、その操作は "*保留中操作キュー*" に配置されます。 保持しているオーケストレーションがロックを解放するまで、保留中の操作は処理されません。

> [!NOTE] 
> これは、C# の `lock` ステートメントなど、ほとんどのプログラミング言語で使用される同期プリミティブとは若干異なります。 たとえば、C# では、複数のスレッド間で同期が確実に行われるようにするには、すべてのスレッドで `lock` ステートメントを使用する必要があります。 一方、エンティティでは、すべての呼び出し元が明示的にエンティティを "_ロックする_" 必要はありません。 いずれかの呼び出し元によってエンティティがロックされた場合、そのエンティティに対する他のすべての操作はブロックされ、キューでそのロックの後に配置されます。

エンティティのロックは持続的であるため、実行プロセスがリサイクルされた場合でも永続化されます。 ロックは、エンティティの持続状態の一部として内部的に保持されます。

### <a name="critical-section-restrictions"></a>クリティカル セクションの制限

クリティカル セクションの使用方法には、いくつかの制限が課せられています。 これらの制限は、デッドロックや再入を防ぐために機能します。

* クリティカル セクションを入れ子にすることはできません。
* クリティカル セクションでサブオーケストレーションを作成することはできません。
* クリティカル セクションでは、自分がロックしているエンティティのみを呼び出すことができます。
* クリティカル セクションでは、複数の並列呼び出しを使用して、同じエンティティを呼び出すことはできません。
* クリティカル セクションでは、自分がロックしていないエンティティのみにシグナルを送信できます。

## <a name="comparison-with-virtual-actors"></a>仮想アクターとの比較

持続エンティティの多くの機能は、[アクター モデル](https://en.wikipedia.org/wiki/Actor_model)に基づいています。 アクターについて既によくわかっている場合は、この記事で説明されている多くの概念を理解できます。 具体的には、持続エンティティは多くの点で[仮想アクター](https://research.microsoft.com/projects/orleans/)と似ています。

* 持続エンティティは、"*エンティティ ID*" を使用してアドレス指定可能です。
* 持続エンティティの操作は、競合状態を避けるため、一度に 1 つずつ順番に実行されます。
* 持続エンティティは、呼び出されるか、シグナルされると、自動的に作成されます。
* 実行する操作がなくなると、持続エンティティはメモリからサイレントにアンロードされます。

ただし、いくつか重要な違いがあります。

* 持続エンティティは、"*待ち時間*" より "*持続性*" の方が優先されるので、待ち時間の要件が厳しいアプリケーションには適していない可能性があります。
* エンティティ間で送信されるメッセージは、信頼性の高い方法で順番に配信されます。
* 持続エンティティは、持続オーケストレーションと組み合わせて使用でき、分散ロック メカニズムがサポートされます。
* エンティティでの要求/応答パターンは、オーケストレーションに制限されます。 "*クライアント エンティティ間*" および "*エンティティ間*" の通信では、元のアクター モデルのように、一方向のメッセージ ("シグナル通知" とも呼ばれます) のみが許可されます。 この動作により、分散デッドロックが防止されます。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [タスク ハブについて確認する](durable-functions-task-hubs.md)