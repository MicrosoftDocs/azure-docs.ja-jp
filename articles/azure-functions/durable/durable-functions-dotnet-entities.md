---
title: .NET での持続エンティティに関する開発者ガイド - Azure Functions
description: Azure Functions の Durable Functions 拡張機能を使用して .NET で持続エンティティを操作する方法。
author: sebastianburckhardt
ms.topic: conceptual
ms.date: 10/06/2019
ms.author: azfuncdf
ms.openlocfilehash: 88d2a23104b67dae8fd480406eb9171e9f3d5652
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92740018"
---
# <a name="developers-guide-to-durable-entities-in-net"></a>.NET での持続エンティティに関する開発者ガイド

この記事では、例と一般的な助言を含め、.NET で持続エンティティを開発するために使用できるインターフェイスについて詳しく説明します。 

エンティティ関数は、アプリケーションの状態を粒度の細かいエンティティのコレクションとして整理するための便利な方法をサーバーレス アプリケーションの開発者に提供します。 基になる概念の詳細については、[持続エンティティの概念](durable-functions-entities.md)に関する記事を参照してください。

現在、Microsoft では、エンティティを定義するための 2 つの API を提供しています。

- **クラスベースの構文** は、エンティティと操作をクラスおよびメソッドとして表します。 この構文を使用すると、簡単に読み取り可能なコードが生成され、インターフェイスによって型チェックされた方法で操作を呼び出すことができます。 

- **関数ベースの構文** は、エンティティを関数として表す下位レベルのインターフェイスです。 これにより、エンティティ操作のディスパッチ方法と、エンティティの状態の管理方法を細かく制御できます。  

この記事では、主に、ほとんどのアプリケーションに適していると予想されるクラスベースの構文について重点的に説明します。 ただし、[関数ベースの構文](#function-based-syntax)は、エンティティの状態と操作に対して独自の抽象的概念を定義または管理する必要があるアプリケーションに適している場合があります。 また、クラスベースの構文で現在サポートされていない総称性を必要とするライブラリの実装にも適している可能性があります。 

> [!NOTE]
> クラスベースの構文は、関数ベースの構文の上にあるレイヤーにすぎないため、同じアプリケーションで両方のバリアントを同じ意味で使用できます。 
 
## <a name="defining-entity-classes"></a>エンティティ クラスの定義

次の例は、整数型の 1 つの値を格納し、`Add`、`Reset`、`Get`、および `Delete` の 4 つの操作を提供する `Counter` エンティティの実装です。

```csharp
[JsonObject(MemberSerialization.OptIn)]
public class Counter
{
    [JsonProperty("value")]
    public int Value { get; set; }

    public void Add(int amount) 
    {
        this.Value += amount;
    }

    public Task Reset() 
    {
        this.Value = 0;
        return Task.CompletedTask;
    }

    public Task<int> Get() 
    {
        return Task.FromResult(this.Value);
    }

    public void Delete() 
    {
        Entity.Current.DeleteState();
    }

    [FunctionName(nameof(Counter))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<Counter>();
}
```

`Run` 関数には、クラスベースの構文を使用するために必要な定型句が含まれています。 それは、*静的な* Azure Function である必要があります。 エンティティによって処理される操作メッセージごとに 1 回実行されます。 `DispatchAsync<T>` が呼び出され、エンティティがまだメモリに存在しない場合は、`T` 型のオブジェクトが構築され、ストレージで見つかった最後に保持されていた JSON (存在する場合) を使ってフィールドが設定されます。 次に、名前が一致するメソッドが呼び出されます。

> [!NOTE]
> クラスベースのエンティティの状態は、エンティティが操作を処理する前に **暗黙的に作成** され、`Entity.Current.DeleteState()` を呼び出すことにより、操作で **明示的に削除** できます。

### <a name="class-requirements"></a>クラスの要件
 
エンティティ クラスは、特別なスーパークラス、インターフェイス、または属性を必要としない POCO (単純な従来の CLR オブジェクト) です。 ただし

- クラスは構築可能である必要があります (「[エンティティの構築](#entity-construction)」を参照してください)。
- クラスは JSON のシリアル化可能である必要があります (「[エンティティのシリアル化](#entity-serialization)」を参照してください)。

また、操作として呼び出されることを意図されているメソッドは、追加の要件を満たしている必要があります。

- 1 つの操作には最大で 1 つの引数が必要であり、オーバーロードやジェネリック型引数を持つことはできません。
- インターフェイスを使用してオーケストレーションから呼び出されるように指定された操作は、`Task` または `Task<T>` を返す必要があります。
- 引数と戻り値は、シリアル化可能な値またはオブジェクトである必要があります。

### <a name="what-can-operations-do"></a>操作で実行できること

すべてのエンティティ操作は、エンティティの状態の読み取りと更新が可能であり、状態の変更は自動的にストレージに保存されます。 さらに、操作では、すべての Azure Functions に共通する一般的な制限内で外部 I/O またはその他の計算を実行できます。

操作では、`Entity.Current` コンテキストによって提供される機能にもアクセスできます。

* `EntityName`: 現在実行しているエンティティの名前。
* `EntityKey`: 現在実行しているエンティティのキー。
* `EntityId`: 現在実行しているエンティティの ID (名前とキーを含む)。
* `SignalEntity`: 一方向のメッセージをエンティティに送信します。
* `CreateNewOrchestration`: 新しいオーケストレーションを開始します。
* `DeleteState`: このエンティティの状態を削除します。

たとえば、カウンターが 100 に達したときにオーケストレーションを開始し、エンティティ ID を入力引数として渡すように、カウンター エンティティを変更することができます。

```csharp
    public void Add(int amount) 
    {
        if (this.Value < 100 && this.Value + amount >= 100)
        {
            Entity.Current.StartNewOrchestration("MilestoneReached", Entity.Current.EntityId);
        }
        this.Value += amount;      
    }
```

## <a name="accessing-entities-directly"></a>エンティティへの直接アクセス

クラスベースのエンティティには、エンティティの明示的な文字列名とその操作を使用して直接アクセスできます。 次に例をいくつか示します。基になる概念 (シグナルや呼び出しなど) の詳細については、「[エンティティへのアクセス](durable-functions-entities.md#access-entities)」の説明を参照してください。 

> [!NOTE]
> 可能な場合は、より多くの型チェックが提供されるので、[インターフェイスを介してエンティティにアクセスする](#accessing-entities-through-interfaces)ことをお勧めします。

### <a name="example-client-signals-entity"></a>例: クライアントがエンティティにシグナル通知を出す

次の Azure HTTP 関数は、REST 規則を使用して DELETE 操作を実装します。 URL パスでキーが渡されるカウンター エンティティに DELETE シグナル通知を送信します。

```csharp
[FunctionName("DeleteCounter")]
public static async Task<HttpResponseMessage> DeleteCounter(
    [HttpTrigger(AuthorizationLevel.Function, "delete", Route = "Counter/{entityKey}")] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client,
    string entityKey)
{
    var entityId = new EntityId("Counter", entityKey);
    await client.SignalEntityAsync(entityId, "Delete");    
    return req.CreateResponse(HttpStatusCode.Accepted);
}
```

### <a name="example-client-reads-entity-state"></a>例: クライアントがエンティティの状態を読み取る

次の Azure HTTP 関数は、REST 規則を使用して GET 操作を実装します。 URL パスでキーが渡されるカウンター エンティティの現在の状態を読み取ります。

```csharp
[FunctionName("GetCounter")]
public static async Task<HttpResponseMessage> GetCounter(
    [HttpTrigger(AuthorizationLevel.Function, "get", Route = "Counter/{entityKey}")] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client,
    string entityKey)
{
    var entityId = new EntityId("Counter", entityKey);
    var state = await client.ReadEntityStateAsync<Counter>(entityId); 
    return req.CreateResponse(state);
}
```

> [!NOTE]
> `ReadEntityStateAsync` によって返されるオブジェクトは、単なるローカル コピー、つまり以前の特定の時点からのエンティティ状態のスナップショットです。 特に、それは古い可能性があり、このオブジェクトを変更しても、実際のエンティティに影響はありません。 

### <a name="example-orchestration-first-signals-then-calls-entity"></a>例: オーケストレーションが最初にシグナル通知を出し、次にエンティティを呼び出す

次のオーケストレーションは、カウンター エンティティに対して増分するようにシグナル通知を出し、次に同じエンティティを呼び出してその最新の値を読み取ります。

```csharp
[FunctionName("IncrementThenGet")]
public static async Task<int> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var entityId = new EntityId("Counter", "myCounter");

    // One-way signal to the entity - does not await a response
    context.SignalEntity(entityId, "Add", 1);

    // Two-way call to the entity which returns a value - awaits the response
    int currentValue = await context.CallEntityAsync<int>(entityId, "Get");

    return currentValue;
}
```

## <a name="accessing-entities-through-interfaces"></a>インターフェイスを使用したエンティティへのアクセス

インターフェイスを使用して、生成されたプロキシ オブジェクトを介してエンティティにアクセスできます。 この方法では、操作の名前と引数の型が、実装されているものと確実に一致します。 可能な場合は常に、エンティティにアクセスするためにインターフェイスを使用することをお勧めします。

たとえば、カウンターの例を次のように変更できます。

```csharp
public interface ICounter
{
    void Add(int amount);
    Task Reset();
    Task<int> Get();
    void Delete();
}

public class Counter : ICounter
{
    ...
}
```

エンティティ クラスとエンティティ インターフェイスは、[Orleans](https://www.microsoft.com/research/project/orleans-virtual-actors/) によって普及されたグレインとグレイン インターフェイスに似ています。 持続エンティティと Orleans の類似点と相違点の詳細については、「[仮想アクターとの比較](durable-functions-entities.md#comparison-with-virtual-actors)」を参照してください。

インターフェイスは、型チェックを提供するだけでなく、アプリケーション内の問題をより適切に分離するためにも役立ちます。 たとえば、エンティティは複数のインターフェイスを実装する可能性があるため、1 つのエンティティで複数のロールを処理できます。 また、インターフェイスは複数のエンティティによって実装される可能性があるため、一般的な通信パターンを再利用可能なライブラリとして実装できます。

### <a name="example-client-signals-entity-through-interface"></a>例: クライアントがインターフェイスを介してエンティティにシグナルを出す

クライアント コードでは、`SignalEntityAsync<TEntityInterface>` を使用して、`TEntityInterface` を実装するエンティティにシグナル通知を送信できます。 次に例を示します。

```csharp
[FunctionName("DeleteCounter")]
public static async Task<HttpResponseMessage> DeleteCounter(
    [HttpTrigger(AuthorizationLevel.Function, "delete", Route = "Counter/{entityKey}")] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client,
    string entityKey)
{
    var entityId = new EntityId("Counter", entityKey);
    await client.SignalEntityAsync<ICounter>(entityId, proxy => proxy.Delete());    
    return req.CreateResponse(HttpStatusCode.Accepted);
}
```

この例で、`proxy` パラメーターは `ICounter` の動的に生成されたインスタンスで、`Delete` への呼び出しを内部でシグナル通知に変換します。

> [!NOTE]
> `SignalEntityAsync` API は、一方向の操作にのみ使用できます。 操作によって `Task<T>` が返された場合でも、`T` パラメーターの値は、実際の結果ではなく、常に null または `default` になります。
たとえば、値が返されないため、`Get` 操作にシグナル通知を出すことは意味がありません。 代わりに、クライアントは `ReadStateAsync` を使用してカウンターの状態に直接アクセスするか、`Get` 操作を呼び出すオーケストレーター関数を開始できます。 

### <a name="example-orchestration-first-signals-then-calls-entity-through-proxy"></a>例: オーケストレーションは最初にシグナル通知を出し、次にプロキシ経由でエンティティを呼び出す

オーケストレーション内からエンティティを呼び出したり、シグナル通知を出したりするには、インターフェイス型と共に `CreateEntityProxy` を使用して、エンティティのプロキシを生成することができます。 そして、このプロキシを使用して、操作に対する呼び出しやシグナル通知を実行できます。

```csharp
[FunctionName("IncrementThenGet")]
public static async Task<int> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var entityId = new EntityId("Counter", "myCounter");
    var proxy = context.CreateEntityProxy<ICounter>(entityId);

    // One-way signal to the entity - does not await a response
    proxy.Add(1);

    // Two-way call to the entity which returns a value - awaits the response
    int currentValue = await proxy.Get();

    return currentValue;
}
```

暗黙的に、`void` を返すすべての操作にはシグナル通知が出され、`Task` または `Task<T>` を返すすべての操作は呼び出されます。 この既定の動作は変更でき、`SignalEntity<IInterfaceType>` メソッドを明示的に使用することで、タスクが返される場合でも操作にシグナル通知を出すことができます。

### <a name="shorter-option-for-specifying-the-target"></a>ターゲットを指定するための短いオプション

インターフェイスを使用してエンティティを呼び出したり、シグナル通知を出したりする場合、最初の引数はターゲット エンティティを指定する必要があります。 ターゲットを指定するには、エンティティ ID を指定するか、エンティティを実装するクラスが 1 つだけの場合はエンティティ キーのみを指定します。

```csharp
context.SignalEntity<ICounter>(new EntityId(nameof(Counter), "myCounter"), ...);
context.SignalEntity<ICounter>("myCounter", ...);
```

エンティティ キーのみが指定され、実行時に一意の実装が見つからない場合は、`InvalidOperationException` がスローされます。 

### <a name="restrictions-on-entity-interfaces"></a>エンティティ インターフェイスに関する制限事項

通常どおり、すべてのパラメーターと戻り値の型は、JSON でシリアル化できる必要があります。 そうでない場合、シリアル化の例外が実行時にスローされます。

さらに、いくつかの追加の規則を適用します。
* エンティティ インターフェイスでは、メソッドのみを定義する必要があります。
* エンティティ インターフェイスにジェネリック パラメーターを含めることはできません。
* エンティティ インターフェイス メソッドは、複数のパラメーターを持つことはできません。
* エンティティ インターフェイス メソッドは、`void`、`Task`、または `Task<T>` を返す必要があります。 

これらのいずれかの規則に違反した場合、`SignalEntity` または `CreateProxy` の型引数としてインターフェイスが使用されると、実行時に `InvalidOperationException` がスローされます。 例外メッセージで、どの規則に違反したかが説明されます。

> [!NOTE]
> `void` を返すインターフェイス メソッドには、シグナル通知を送信できるだけで (一方向)、呼び出しを行うことはできません (双方向)。 `Task` または `Task<T>` を返すインターフェイス メソッドには、呼び出しを行うこともシグナル通知を出すこともできます。 呼び出された場合は、操作の結果を返すか、操作によってスローされた例外を再スローします。 ただし、シグナル通知が出されると、操作からの実際の結果や例外を返すのではなく、既定値だけを返します。

## <a name="entity-serialization"></a>エンティティのシリアル化

エンティティの状態は永続的に持続されるため、エンティティ クラスはシリアル化可能である必要があります。 Durable Functions ランタイムは、この目的で、シリアル化と逆シリアル化のプロセスを制御する多数のポリシーと属性がサポートされている [Json.NET](https://www.newtonsoft.com/json) ライブラリを使用します。 最も一般的に使用されている C# データ型 (配列型やコレクション型を含む) は既にシリアル化可能であり、持続エンティティの状態を定義するために簡単に使用できます。

たとえば、Json.NET は、次のクラスを簡単にシリアル化および逆シリアル化できます。

```csharp
[JsonObject(MemberSerialization = MemberSerialization.OptIn)]
public class User
{
    [JsonProperty("name")]
    public string Name { get; set; }

    [JsonProperty("yearOfBirth")]
    public int YearOfBirth { get; set; }

    [JsonProperty("timestamp")]
    public DateTime Timestamp { get; set; }

    [JsonProperty("contacts")]
    public Dictionary<Guid, Contact> Contacts { get; set; } = new Dictionary<Guid, Contact>();

    [JsonObject(MemberSerialization = MemberSerialization.OptOut)]
    public struct Contact
    {
        public string Name;
        public string Number;
    }

    ...
}
```

### <a name="serialization-attributes"></a>シリアル化属性

上記の例では、基になるシリアル化をより見やすくするために、いくつかの属性を含めることを選択しました。
- クラスがシリアル化可能でなければならないことを思い出させ、JSON プロパティとして明示的にマークされたメンバーのみを永続化するために、クラスに `[JsonObject(MemberSerialization.OptIn)]` の注釈を付けています。
-  フィールドが永続化されたエンティティの状態の一部であることを思い出させ、JSON 表現で使用するプロパティ名を指定するために、永続化されるフィールドに `[JsonProperty("name")]` の注釈を付けています。

ただし、これらの属性は必須ではありません。Json.NET で機能する限り、他の規則や属性が許可されます。 たとえば、`[DataContract]` 属性を使用することもできれば、属性をまったく使用しないこともできます。

```csharp
[DataContract]
public class Counter
{
    [DataMember]
    public int Value { get; set; }
    ...
}

public class Counter
{
    public int Value;
    ...
}
```

既定では、クラスの名前は JSON 表現の一部として格納 *されません*。つまり、`TypeNameHandling.None` を既定の設定として使用します。 この既定の動作は、`JsonObject` 属性または `JsonProperty` 属性を使用してオーバーライドできます。

### <a name="making-changes-to-class-definitions"></a>クラス定義に対する変更

格納された JSON オブジェクトが新しいクラス定義と一致しなくなる可能性があるため、アプリケーションの実行後にクラス定義を変更する場合は注意が必要です。 しかし、`JsonConvert.PopulateObject` によって使用される逆シリアル化プロセスを理解している限り、多くの場合、データ形式の変更を正しく処理することができます。

たとえば、変更とその影響の例を次に示します。

1. 格納されている JSON に存在しない新しいプロパティが追加された場合、その既定値が想定されます。
1. 格納されている JSON に存在するプロパティが削除されると、以前のコンテンツは失われます。
1. プロパティの名前が変更された場合、その効果は、古いものを削除して新しいものを追加したのと同じです。
1. プロパティの型が変更されて、格納された JSON から逆シリアル化できなくなると、例外がスローされます。
1. プロパティの型が変更されても、格納された JSON から逆シリアル化できる場合は、そのようにします。

Json.NET の動作をカスタマイズするために使用できるオプションは多数あります。 たとえば、格納されている JSON に、クラスに存在しないフィールドが含まれている場合に例外を強制するには、属性 `JsonObject(MissingMemberHandling = MissingMemberHandling.Error)` を指定します。 また、任意の形式で格納された JSON を読み取ることができる、逆シリアル化のためのカスタム コードを記述することもできます。

## <a name="entity-construction"></a>エンティティの構築

場合によっては、エンティティ オブジェクトの構築方法をより細かく制御する必要があります。 ここでは、エンティティ オブジェクトを構築するときの既定の動作を変更するためのいくつかのオプションについて説明します。 

### <a name="custom-initialization-on-first-access"></a>初回アクセス時のカスタム初期化

時々、アクセスされたことがないエンティティや削除されているエンティティに操作をディスパッチする前に、特別な初期化を実行することが必要な場合があります。 この動作を指定するために、`DispatchAsync` の前に条件を追加できます。

```csharp
[FunctionName(nameof(Counter))]
public static Task Run([EntityTrigger] IDurableEntityContext ctx)
{
    if (!ctx.HasState)
    {
        ctx.SetState(...);
    }
    return ctx.DispatchAsync<Counter>();
}
```

### <a name="bindings-in-entity-classes"></a>エンティティ クラスでのバインド

通常の関数とは異なり、エンティティ クラスのメソッドでは、入力と出力のバインドに直接アクセスできません。 代わりに、エントリ ポイント関数の宣言でバインド データをキャプチャした後、`DispatchAsync<T>` メソッドに渡す必要があります。 `DispatchAsync<T>`に渡されたオブジェクトは、エンティティ クラスのコンストラクターに引数として自動的に渡されます。

次の例では、[BLOB 入力バインド](../functions-bindings-storage-blob-input.md)からの `CloudBlobContainer` 参照をクラス ベースのエンティティで使用できるようにする方法を示します。

```csharp
public class BlobBackedEntity
{
    [JsonIgnore]
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

### <a name="dependency-injection-in-entity-classes"></a>エンティティ クラスでの依存関係の挿入

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
    [JsonIgnore]
    private readonly HttpClient client;

    public HttpEntity(IHttpClientFactory factory)
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

    [FunctionName(nameof(HttpEntity))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<HttpEntity>();
}
```

> [!NOTE]
> シリアル化に関する問題を回避するには、挿入された値を格納するためのフィールドをシリアル化から除外するようにしてください。

> [!NOTE]
> 通常の .NET Azure Functions でコンストラクターの挿入を使用する場合とは異なり、クラスベースのエンティティに対する関数のエントリ ポイント メソッドは、`static` と宣言する "*必要があります*"。 非静的な関数エントリ ポイントを宣言すると、通常の Azure Functions オブジェクト初期化子と永続エンティティ オブジェクト初期化子との間で競合が発生する可能性があります。

## <a name="function-based-syntax"></a>関数ベースの構文

これまで、ほとんどのアプリケーションに適していると予想されるクラスベースの構文について重点的に説明してきました。 ただし、関数ベースの構文は、エンティティの状態と操作に対して独自の抽象的概念を定義または管理する必要があるアプリケーションに適している場合があります。 また、クラスベースの構文で現在サポートされていない総称性を必要とするライブラリを実装するときにも適している可能性があります。 

関数ベースの構文では、エンティティ関数は、操作のディスパッチを明示的に処理し、エンティティの状態を明示的に管理します。 たとえば、次のコードは、関数ベースの構文を使用して実装された *Counter* エンティティを示しています。  

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
        case "delete":
            ctx.DeleteState();
            break;
    }
}
```

### <a name="the-entity-context-object"></a>エンティティ コンテキスト オブジェクト

エンティティ固有の機能には、`IDurableEntityContext` 型のコンテキスト オブジェクトを介してアクセスできます。 このコンテキスト オブジェクトは、エンティティ関数のパラメーターとして、および非同期ローカル プロパティ `Entity.Current` を介して使用できます。

次のメンバーは、現在の操作に関する情報を提供し、戻り値の指定を許可します。 

* `EntityName`: 現在実行しているエンティティの名前。
* `EntityKey`: 現在実行しているエンティティのキー。
* `EntityId`: 現在実行しているエンティティの ID (名前とキーを含む)。
* `OperationName`: 現在の操作の名前。
* `GetInput<TInput>()`: 現在の操作の入力を取得します。
* `Return(arg)`: 操作を呼び出したオーケストレーションに値を返します。

次のメンバーは、エンティティの状態 (作成、読み取り、更新、削除) を管理します。 

* `HasState`: エンティティが存在するかどうか、つまり、何らかの状態があるかどうか。 
* `GetState<TState>()`: エンティティの現在の状態を取得します。 まだ存在しない場合は、作成されます。
* `SetState(arg)`: エンティティの状態を作成または更新します。
* `DeleteState()`: エンティティの状態を削除します (存在する場合)。 

`GetState` によって返された状態がオブジェクトの場合は、アプリケーション コードによって直接変更できます。 最後に `SetState` を再度呼び出す必要はありません (ただし、害もありません)。 `GetState<TState>` が複数回呼び出される場合は、同じ型を使用する必要があります。

最後に、次のメンバーを使用して他のエンティティにシグナル通知を出したり、新しいオーケストレーションを開始したりします。

* `SignalEntity(EntityId, operation, input)`: 一方向のメッセージをエンティティに送信します。
* `CreateNewOrchestration(orchestratorFunctionName, input)`: 新しいオーケストレーションを開始します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [エンティティの概念について学ぶ](durable-functions-entities.md)
