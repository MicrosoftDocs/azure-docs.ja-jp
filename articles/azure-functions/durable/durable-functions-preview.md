---
title: Durable Functions のプレビュー機能 - Azure Functions
description: Durable Functions のプレビュー機能について説明します。
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: article
ms.date: 07/08/2019
ms.author: azfuncdf
ms.openlocfilehash: 94784ee9a72d11e168828f1543cbe62219ac4a74
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70097952"
---
# <a name="durable-functions-20-preview-azure-functions"></a>Durable Functions 2.0 プレビュー (Azure Functions)

*Durable Functions* は、サーバーレス環境でステートフル関数を記述できる、[Azure Functions](../functions-overview.md) と [Azure WebJobs](../../app-service/web-sites-create-web-jobs.md) の拡張機能です。 この拡張機能は状態、チェックポイント、再起動を管理します。 Durable Functions にまだ慣れていない場合は、[概要ドキュメント](durable-functions-overview.md)をご覧ください。

Durable Functions 1.x は Azure Functions の GA (一般提供) 機能ですが、現在パブリック プレビュー段階のサブ機能もいくつか含まれています。 この記事では、新しくリリースされたプレビュー機能について説明し、そのしくみと使い始める方法を詳しく見ていきます。

> [!NOTE]
> これらのプレビュー機能は Durable Functions 2.0 リリースの一部であり、現在それはいくつかの破壊的変更を含む**プレビュー品質リリース**です。 Azure Functions Durable 拡張機能パッケージ ビルドは、nuget.org 上に **2.0.0-betaX** という形式のバージョンで存在します。 これらのビルドは運用環境のワークロードを対象としておらず、今後のリリースで破壊的変更が追加される可能性があります。

## <a name="breaking-changes"></a>重大な変更

Durable Functions 2.0 では、破壊的変更がいくつか導入されています。 既存のアプリケーションは、コードを変更しないと Durable Functions 2.0 と互換性がないものと思われます。 このセクションでは、変更の一部を示します。

### <a name="hostjson-schema"></a>Host.json スキーマ

次のスニペットでは、host.json の新しいスキーマを示します。 注意すべき主な変更点は、新しいサブセクションです。

* ストレージ固有の構成用の `"storageProvider"` (および `"azureStorage"` サブセクション)
* 追跡とログ記録の構成用の `"tracking"`
* イベント グリッド通知構成用の `"notifications"` (および `"eventGrid"` サブセクション)

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "azureStorage": {
          "connectionStringName": <string>,
          "controlQueueBatchSize": <int?>,
          "partitionCount": <int?>,
          "controlQueueVisibilityTimeout": <hh:mm:ss?>,
          "workItemQueueVisibilityTimeout": <hh:mm:ss?>,
          "trackingStoreConnectionStringName": <string?>,
          "trackingStoreNamePrefix": <string?>,
          "maxQueuePollingInterval": <hh:mm:ss?>
        }
      },
      "tracking": {
        "traceInputsAndOutputs": <bool?>,
        "traceReplayEvents": <bool?>,
      },
      "notifications": {
        "eventGrid": {
          "topicEndpoint": <string?>,
          "keySettingName": <string?>,
          "publishRetryCount": <string?>,
          "publishRetryInterval": <hh:mm:ss?>,
          "publishRetryHttpStatus": <int[]?>,
          "publishEventTypes": <string[]?>,
        }
      },
      "maxConcurrentActivityFunctions": <int?>,
      "maxConcurrentOrchestratorFunctions": <int?>,
      "extendedSessionsEnabled": <bool?>,
      "extendedSessionIdleTimeoutInSeconds": <int?>,
      "customLifeCycleNotificationHelperType": <string?>
  }
}
```

Durable Functions 2.0 は安定化が続けられており、host.json の `durableTask` セクションはさらに変更されます。 これらの変更について詳しくは、[こちらの GitHub の問題](https://github.com/Azure/azure-functions-durable-extension/issues/641)をご覧ください。

### <a name="public-interface-changes"></a>パブリック インターフェイスの変更

Durable Functions によってサポートされるさまざまな "コンテキスト" オブジェクトには、単体テストでの使用が意図される抽象基本クラスが含まれました。 Durable Functions 2.0 の一部として、これらの抽象基本クラスがインターフェイスに置き換えられました。 具象型を直接使用する関数のコードには影響しません。

次の表では、主な変更を示します。

| 古い型 | 新しい型 |
|----------|----------|
| DurableOrchestrationClientBase | IDurableOrchestrationClient |
| DurableOrchestrationContextBase | IDurableOrchestrationContext |
| DurableActivityContextBase | IDurableActivityContext |

抽象基本クラスに仮想メソッドが含まれていた場合、これらの仮想メソッドは `DurableContextExtensions` で定義される拡張メソッドに置き換えられています。

## <a name="entity-functions"></a>エンティティ関数

エンティティ関数では、"*持続エンティティ*" と呼ばれる小さい状態の読み取りと更新のための操作が定義されています。 オーケストレーター関数と同様、エンティティ関数は特殊なトリガー型である "*エンティティ トリガー*" を含む関数です。 オーケストレーター関数とは異なり、エンティティ関数には特定のコードの制約はありません。 また、エンティティ関数では、制御フローを介して状態を表す暗黙的ではなく、明示的に状態が管理されます。

### <a name="net-programing-models"></a>.NET プログラミング モデル

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

このモデルは、単純なエンティティの実装や、操作の動的なセットを持つ実装に最適です。 ただし、静的だが実装がより複雑なエンティティに役立つクラスベースのプログラミング モデルもあります。 次の例は、.NET クラスおよびメソッドを使用した `Counter` エンティティの同等の実装です。

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

クラスベースのモデルは、[Orleans](https://www.microsoft.com/research/project/orleans-virtual-actors/) によって実用化されているプログラミング モデルに似ています。 このモデルでは、エンティティ型は .NET クラスとして定義されます。 クラスの各メソッドは、外部クライアントによって呼び出すことができる操作です。 ただし、Orleans とは異なり、.NET インターフェイスは省略可能です。 前の *Counter* の例では、インターフェイスを使用しませんでしたが、他の関数または HTTP API 呼び出しを介して呼び出すことができます。

エンティティの "*インスタンス*" には、一意の識別子である "*エンティティ ID*" を使用してアクセスします。 エンティティ ID は単に、エンティティのインスタンスを一意に示す文字列のペアです。 構成は次のとおりです。

* **エンティティ名**: エンティティの種類を示す名前 (例: "Counter")。
* **エンティティ キー**: 同じ名前の他のすべてのエンティティの間でそのエンティティを一意に示す文字列 (例: GUID)。

たとえば、"*カウンター*" エンティティ関数は、オンライン ゲームでスコアを保持するために使用される可能性があります。 ゲームの各インスタンスは、`@Counter@Game1`、`@Counter@Game2` のような一意のエンティティ ID を持ちます。

### <a name="comparison-with-virtual-actors"></a>仮想アクターとの比較

持続エンティティの設計には、[アクター モデル](https://en.wikipedia.org/wiki/Actor_model)が大きく影響しています。 アクターについて既に理解している場合、持続エンティティの背後にある概念はよくわかるはずです。 具体的には、持続エンティティは多くの点で[仮想アクター](https://research.microsoft.com/projects/orleans/)と似ています。

* 持続エンティティは、"*エンティティ ID*" を使用してアドレス指定可能です。
* 持続エンティティの操作は、競合状態を避けるため、一度に 1 つずつ順番に実行されます。
* 持続エンティティは、呼び出されるか、シグナルされると、自動的に作成されます。
* 実行する操作がなくなると、持続エンティティはメモリからサイレントにアンロードされます。

ただし、いくつか重要な違いがあります。

* 持続エンティティは、"*待ち時間*" より "*持続性*" の方が優先されるので、待ち時間の要件が厳しいアプリケーションには適していない可能性があります。
* エンティティ間で送信されるメッセージは、信頼性の高い方法で順番に配信されます。
* 持続エンティティは、持続性のあるオーケストレーションと組み合わせて使用でき、この記事の後半で説明する分散ロックとして使用できます。
* エンティティでの要求/応答パターンは、オーケストレーションに制限されます。 エンティティ間通信では、元のアクター モデルのように、一方向のメッセージ (別名 "シグナリング") のみが許可されます。 この動作により、分散デッドロックが防止されます。

### <a name="durable-entity-net-apis"></a>持続エンティティの .NET API

エンティティのサポートには、いくつかの API が含まれます。 たとえば、上で示したように、エンティティ関数を定義するための新しい API があり、エンティティで操作が呼び出されるときの動作が指定されています。 また、クライアントとオーケストレーションに関する既存の API が、エンティティと相互作用するための新しい機能で更新されています。

#### <a name="implementing-entity-operations"></a>エンティティの操作の実装

エンティティでの操作の実行では、コンテキスト オブジェクト (.NET では`IDurableEntityContext`) で次のメンバーを呼び出すことができます。

* **OperationName**: 操作の名前を取得します。
* **GetInput\<TInput>** : 操作に対する入力を取得します。
* **GetState\<TState>** : エンティティの現在の状態を取得します。
* **SetState**: エンティティの状態を更新します。
* **SignalEntity**: エンティティに一方向のメッセージを送信します。
* **Self**: エンティティの ID を取得します。
* **Return**: 操作を呼び出したクライアントまたはオーケストレーションに値を返します。
* **IsNewlyConstructed**: 操作の前にエンティティが存在しなかった場合、`true` を返します。
* **DestructOnExit**: 操作が完了したら、エンティティを削除します。

操作はオーケストレーションほど制限が厳しくありません。

* 操作では、同期または非同期の API を使用して、外部の I/O を呼び出すことができます (非同期のもののみを使用することをお勧めします)。
* 操作は決定論的ではない場合があります。 たとえば、`DateTime.UtcNow`、`Guid.NewGuid()`、または `new Random()` を呼び出しても安全です。

#### <a name="accessing-entities-from-clients"></a>クライアントからエンティティへのアクセス

持続エンティティは、`orchestrationClient` バインド (.NET では `IDurableOrchestrationClient`) を使用して通常の関数から呼び出すことができます。 以下のメソッドがサポートされています。

* **ReadEntityStateAsync\<T>** : エンティティの状態を読み取ります。
* **SignalEntityAsync**: エンティティに一方向のメッセージを送信し、エンキューされるまで待機します。
* **Signalentityasync\<T >** : `SignalEntityAsync` と同じですが、型 `T` の生成されたプロキシ オブジェクトを使用します。

前の `SignalEntityAsync` 呼び出しでは、エンティティ操作の名前を `string` として指定し、操作のペイロードを `object` として指定する必要があります。 次のサンプル コードは、このパターンの例です。

```csharp
EntityId id = // ...
object amount = 5;
context.SignalEntityAsync(id, "Add", amount);
```

また、タイプ セーフ アクセス用にプロキシ オブジェクトを生成することもできます。 タイプ セーフなプロキシを生成するには、エンティティ型によってインターフェイスが実装される必要があります。 たとえば、前述の `Counter` エンティティによって、次のように定義された `ICounter` インターフェイスが実装されたとします。

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

その後、クライアント コードで `SignalEntityAsync<T>` を使用し、`ICounter` インターフェイスを型パラメーターとして指定することで、タイプ セーフなプロキシを生成できます。 次のコード サンプルは、タイプ セーフなプロキシのこの使用方法を示しています。

```csharp
[FunctionName("UserDeleteAvailable")]
public static async Task AddValueClient(
    [QueueTrigger("my-queue")] string message,
    [OrchestrationClient] IDurableOrchestrationClient client)
{
    int amount = int.Parse(message);
    var target = new EntityId(nameof(Counter), "MyCounter");
    await client.SignalEntityAsync<ICounter>(target, proxy => proxy.Add(amount));
}
```

前の例では、`proxy` パラメーターは動的に生成された `ICounter` のインスタンスであり、`Add` への呼び出しを、`SignalEntityAsync` への同等の (型指定されていない) 呼び出しに内部的に変換します。

`SignalEntityAsync<T>` の型パラメーターには、次の制限があります。

* 型パラメーターはインターフェイス型であることが必要です。
* インターフェイスで定義できるのはメソッドのみです。 プロパティはサポートされていません。
* メソッドごとに 1 つまたは 0 個のパラメーターを定義する必要があります。
* それぞれのメソッドは、`void`、`Task`、または `Task<T>` のいずれかを返す必要があります。ただし `T` は、JSON にシリアル化できる型です。
* インターフェイスは、インターフェイスのアセンブリ内の型 1 つだけで実装される必要があります。

ほとんどの場合、これらの要件を満たしていないインターフェイスでは、ランタイム例外が発生します。

> [!NOTE]
> `IDurableOrchestrationClient` の `ReadEntityStateAsync` メソッドと `SignalEntityAsync` メソッドでは、整合性よりもパフォーマンスが優先されることに注意することが重要です。 `ReadEntityStateAsync` では古い値を返すことができ、`SignalEntityAsync` は操作が完了する前に戻ることができます。

#### <a name="accessing-entities-from-orchestrations"></a>オーケストレーションからエンティティへのアクセス

オーケストレーションでは、`IDurableOrchestrationContext` オブジェクトを使用してエンティティにアクセスできます。 一方向の通信の (ファイア アンド フォーゲット) または双方向の通信 (要求と応答) を選択できます。 それぞれのメソッドは次のとおりです。

* **SignalEntity**: エンティティに一方向のメッセージを送信します。
* **CallEntityAsync**: エンティティにメッセージを送信し、操作が完了したことを示す応答を待ちます。
* **CallEntityAsync\<T>** : エンティティにメッセージを送信し、T 型の結果を含む応答を待ちます。

双方向の通信を使用すると、操作の実行中にスローされた例外も、呼び出し元のオーケストレーションに返送されて、再スローされます。 これに対し、ファイア アンド フォーゲットを使用すると、例外は反映されません。

タイプ セーフなアクセスの場合、オーケストレーション関数ではインターフェイスに基づいてプロキシを生成できます。 この目的には、`CreateEntityProxy` 拡張メソッドを使用できます。

```csharp
public interface IAsyncCounter
{
    Task AddAsync(int amount);
    Task ResetAsync();
    Task<int> GetAsync();
}

[FunctionName("CounterOrchestration")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    // ...
    IAsyncCounter proxy = context.CreateEntityProxy<IAsyncCounter>("MyCounter");
    await proxy.AddAsync(5);
    int newValue = await proxy.GetAsync();
    // ...
}
```

前の例では、`IAsyncCounter` インターフェイスを実装する "counter" エンティティが存在することが想定されていました。 その後、オーケストレーションでは、`IAsyncCounter` 型定義を使用して、エンティティと同期的に対話するためのプロキシ型を生成することができました。

### <a name="locking-entities-from-orchestrations"></a>オーケストレーションからのエンティティのロック

オーケストレーションでは、エンティティをロックできます。 この機能では、"*クリティカル セクション*" を使用することによって望ましくない競合を防ぐ簡単な方法が提供されます。

コンテキスト オブジェクトでは次のメソッドが提供されます。

* **LockAsync**: 1 つまたは複数のエンティティでロックを取得します。
* **IsLocked**: 現在クリティカル セクションにいる場合は true を返し、それ以外の場合は false を返します。

オーケストレーションが終了すると、クリティカル セクションが終了し、すべてのロックが解放されます。 .NET では、`LockAsync` から破棄されるとクリティカル セクションを終了する `IDisposable` が返され、それを `using` 句と組み合わせて使用して、クリティカル セクションの構文表現を取得できます。

たとえば、2 人のプレーヤーが使用可能かどうかをテストした後、両方をゲームに割り当る必要があるオーケストレーションについて考えます。 このタスクは、次のようにクリティカル セクションを使用して実装できます。

```csharp
[FunctionName("Orchestrator")]
public static async Task RunOrchestrator(
    [OrchestrationTrigger] IDurableOrchestrationContext ctx)
{
    EntityId player1 = /* ... */;
    EntityId player2 = /* ... */;

    using (await ctx.LockAsync(player1, player2))
    {
        bool available1 = await ctx.CallEntityAsync<bool>(player1, "is-available");
        bool available2 = await ctx.CallEntityAsync<bool>(player2, "is-available");

        if (available1 && available2)
        {
            Guid gameId = ctx.NewGuid();

            await ctx.CallEntityAsync(player1, "assign-game", gameId);
            await ctx.CallEntityAsync(player2, "assign-game", gameId);
        }
    }
}
```

クリティカル セクション内では両方のプレーヤー エンティティがロックされ、これはクリティカル セクション内から呼び出されるもの以外の操作を実行していないことを意味します。 この動作により、異なるゲームに割り当てられているプレーヤーや、サインオフなど、競合する操作が防止されます。

クリティカル セクションの使用方法には、いくつかの制限が課せられています。 これらの制限は、デッドロックや再入を防ぐために機能します。

* クリティカル セクションを入れ子にすることはできません。
* クリティカル セクションでサブオーケストレーションを作成することはできません。
* クリティカル セクションでは、自分がロックしているエンティティのみを呼び出すことができます。
* クリティカル セクションでは、複数の並列呼び出しを使用して、同じエンティティを呼び出すことはできません。
* クリティカル セクションでは、自分がロックしていないエンティティのみにシグナルを送信できます。

## <a name="alternate-storage-providers"></a>代替ストレージ プロバイダー

現在、Durable Task Framework では、[Azure Storage](https://github.com/Azure/durabletask/tree/master/src/DurableTask.AzureStorage)、[Azure Service Bus](https://github.com/Azure/durabletask/tree/master/src/DurableTask.ServiceBus)、[インメモリ エミュレーター](https://github.com/Azure/durabletask/tree/master/src/DurableTask.Emulator)、実験的な [Redis](https://github.com/Azure/durabletask/tree/redis/src/DurableTask.Redis) プロバイダーなど、複数のストレージ プロバイダーがサポートされています。 ただし、これまで、Azure Functions の Durable Task 拡張機能では Azure Storage プロバイダーのみがサポートされていました。 Durable Functions 2.0 以降では、Redis プロバイダーから始めて、代替ストレージ プロバイダーのサポートが追加されています。

> [!NOTE]
> Durable Functions 2.0 では、.NET Standard 2.0 と互換性のあるプロバイダーのみがサポートされます。 これを書いている時点で、Azure Service Bus プロバイダーは .NET Standard 2.0 をサポートしていないため、代替ストレージ プロバイダーとして使用できません。

### <a name="emulator"></a>エミュレーター

[DurableTask.Emulator](https://www.nuget.org/packages/Microsoft.Azure.DurableTask.Emulator/) プロバイダーは、ローカル テスト シナリオに適している、ローカル メモリの非持続ストレージ プロバイダーです。 次の最小限の **host.json** スキーマを使用して構成できます。

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "emulator": { }
      }
    }
  }
}
```

### <a name="redis-experimental"></a>Redis (実験段階)

[DurableTask.Redis](https://www.nuget.org/packages/Microsoft.Azure.DurableTask.Redis/) プロバイダーでは、すべてのオーケストレーション状態が構成されている Redis クラスターに保持されます。

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "redis": {
          "connectionStringName": <string>,
        }
      }
    }
  }
}
```

`connectionStringName` では、アプリ設定または環境変数の名前を参照する必要があります。 そのアプリ設定または環境変数には、Redis 接続文字列の値が "*サーバー:ポート*" の形式で含まれる必要があります。 たとえば、ローカル Redis クラスターに接続する場合は `localhost:6379` です。

> [!NOTE]
> 現在、Redis プロバイダーは実験段階であり、1 つのノードで実行されている関数アプリのみをサポートします。 Redis プロバイダーが一般公開されることは保証されず、将来のリリースでは削除される可能性があります。
