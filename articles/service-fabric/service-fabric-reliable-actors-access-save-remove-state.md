---
title: Azure Service Fabric の状態の管理
description: Azure Service Fabric Reliable Actors の状態のアクセス、保存、削除と、アプリケーションを設計するときの考慮事項について説明します。
ms.topic: conceptual
ms.date: 03/19/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: 1e796232719342883efc2aa5dd377d586f3039ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96571319"
---
# <a name="access-save-and-remove-reliable-actors-state"></a>Reliable Actors の状態のアクセス、保存、および削除
[Reliable Actors](service-fabric-reliable-actors-introduction.md) は、ロジックと状態の両方をカプセル化し、状態を確実に維持できるシングル スレッド オブジェクトです。 各アクター インスタンスは、独自の[状態マネージャー](service-fabric-reliable-actors-state-management.md)を持ちます。これは、辞書のようなデータ構造で、キーと値のペアを確実に格納します。 状態マネージャーは、状態プロバイダーのラッパーです。 [永続化設定](service-fabric-reliable-actors-state-management.md#state-persistence-and-replication)の使用に関係なく、データの格納に使用できます。

状態マネージャー キーは、文字列でなければなりません。 値はジェネリックでカスタム型を含む任意のタイプです。 状態マネージャーに格納される値は、データ コントラクト シリアル化可能である必要があります。アクター状態の永続性設定によっては、これらの値がレプリケーション中にネットワーク経由で他のノードに送信されたり、ディスクに書き込まれたりする可能性があるためです。

状態マネージャーは、Reliable Dictionary に含まれているものと同様、状態管理のための一般的な辞書メソッドを公開します。

詳細については、[アクター状態の管理に関するベスト プラクティス](service-fabric-reliable-actors-state-management.md#best-practices)についてのページを参照してください。

## <a name="access-state"></a>状態にアクセスする
状態には、状態マネージャーからキーでアクセスします。 状態マネージャーのメソッドは、すべて非同期です。アクターが永続化状態を持つ場合に、ディスク I/O が必要になる可能性があるためです。 最初のアクセス時に、状態オブジェクトがメモリにキャッシュされます。 その後のアクセス操作では、メモリから直接オブジェクトにアクセスし同期的に戻ります。ディスク I/O や非同期のコンテキスト切り替えのオーバーヘッドは生じません。 状態オブジェクトは、次の場合に、キャッシュから削除されます。

* アクター メソッドは、状態マネージャーからオブジェクトを取得した後に、未処理の例外をスローします。
* アクターは、非アクティブ化またはエラーの後に再アクティブ化されます。
* 状態プロバイダーは、状態をディスクにページングします。 この動作は、状態プロバイダーの実装に依存します。 `Persisted` 設定用の既定の状態プロバイダーは、この動作を行います。

キーのエントリが存在しない場合は、`KeyNotFoundException`(C#) または `NoSuchElementException`(Java) をスローする標準の *Get* 操作を使用して状態を取得できます。

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<int> GetCountAsync()
    {
        return this.StateManager.GetStateAsync<int>("MyState");
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture<Integer> getCountAsync()
    {
        return this.stateManager().getStateAsync("MyState");
    }
}
```

キーのエントリが存在しない場合、スローしない *TryGet* メソッドを使用して状態を取得することもできます。

```csharp
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public async Task<int> GetCountAsync()
    {
        ConditionalValue<int> result = await this.StateManager.TryGetStateAsync<int>("MyState");
        if (result.HasValue)
        {
            return result.Value;
        }

        return 0;
    }
}
```
```Java
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture<Integer> getCountAsync()
    {
        return this.stateManager().<Integer>tryGetStateAsync("MyState").thenApply(result -> {
            if (result.hasValue()) {
                return result.getValue();
            } else {
                return 0;
            });
    }
}
```

## <a name="save-state"></a>状態を保存する
状態マネージャーの取得メソッドは、ローカル メモリ内のオブジェクトへの参照を返します。 ローカル メモリ内のこのオブジェクトを変更しただけでは、変更は永続的に保存されません。 オブジェクトが状態マネージャーから取得され変更された場合、それを永続的に保存するには、状態マネージャーに再挿入する必要があります。

状態を挿入するには無条件の *Set* を使用します。これは、`dictionary["key"] = value` 構文と同等です。

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task SetCountAsync(int value)
    {
        return this.StateManager.SetStateAsync<int>("MyState", value);
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture setCountAsync(int value)
    {
        return this.stateManager().setStateAsync("MyState", value);
    }
}
```

*Add* メソッド使用して状態を追加することができます。 このメソッドは、すでに存在するキーを追加しようとしたときに `InvalidOperationException`(c#) または `IllegalStateException`(Java) をスローします。

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task AddCountAsync(int value)
    {
        return this.StateManager.AddStateAsync<int>("MyState", value);
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture addCountAsync(int value)
    {
        return this.stateManager().addOrUpdateStateAsync("MyState", value, (key, old_value) -> old_value + value);
    }
}
```

*TryAdd* メソッド使用して状態を追加することもできます。 このメソッドは、すでに存在するキーを追加しようとしたときでもスローしません。

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public async Task AddCountAsync(int value)
    {
        bool result = await this.StateManager.TryAddStateAsync<int>("MyState", value);

        if (result)
        {
            // Added successfully!
        }
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture addCountAsync(int value)
    {
        return this.stateManager().tryAddStateAsync("MyState", value).thenApply((result)->{
            if(result)
            {
                // Added successfully!
            }
        });
    }
}
```

状態マネージャーはアクター メソッドの最後に、挿入または更新操作によって追加または変更されたすべての値を自動的に保存します。 "保存" には、使用されている設定に応じて、ディスクへの永続化やレプリケーションも含まれます。 変更されていない値は、永続化もレプリケートもされません。 値が変更されなかった場合、保存操作では何も行われません。 保存が失敗した場合、変更された状態は破棄され、元の状態が再度読み込まれます。

状態はアクター ベースの `SaveStateAsync` メソッドを呼び出すことで、手動で保存することもできます。

```csharp
async Task IMyActor.SetCountAsync(int count)
{
    await this.StateManager.AddOrUpdateStateAsync("count", count, (key, value) => count > value ? count : value);

    await this.SaveStateAsync();
}
```
```Java
interface MyActor {
    CompletableFuture setCountAsync(int count)
    {
        this.stateManager().addOrUpdateStateAsync("count", count, (key, value) -> count > value ? count : value).thenApply();

        this.stateManager().saveStateAsync().thenApply();
    }
}
```

## <a name="remove-state"></a>状態を削除する
状態は *Remove* メソッドを呼び出すことによって、アクターの状態マネージャーから完全に削除することができます。 このメソッドは、存在しないキーを削除しようとしたときに `KeyNotFoundException`(c#) または `NoSuchElementException`(Java) をスローします。

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task RemoveCountAsync()
    {
        return this.StateManager.RemoveStateAsync("MyState");
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture removeCountAsync()
    {
        return this.stateManager().removeStateAsync("MyState");
    }
}
```

*TryRemove* メソッドを使用して、状態を完全に削除することもできます。 このメソッドは、存在しないキーを削除しようとしたときでもスローしません。

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public async Task RemoveCountAsync()
    {
        bool result = await this.StateManager.TryRemoveStateAsync("MyState");

        if (result)
        {
            // State removed!
        }
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture removeCountAsync()
    {
        return this.stateManager().tryRemoveStateAsync("MyState").thenApply((result)->{
            if(result)
            {
                // State removed!
            }
        });
    }
}
```

## <a name="next-steps"></a>次のステップ

Reliable Actors に格納される状態は、ディスクに書き込まれて、高可用性のためにレプリケートされる前に、シリアル化される必要があります。 [Actor 型のシリアル化](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)についてさらに学習してください。

次に、[アクターの診断とパフォーマンスの監視](service-fabric-reliable-actors-diagnostics.md)についてさらに学習してください。
