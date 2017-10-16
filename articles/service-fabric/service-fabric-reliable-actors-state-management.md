---
title: "Reliable Actors の状態管理 | Microsoft Docs"
description: "Reliable Actors の状態が、高可用性のためにどのように管理、永続化、およびレプリケートされるかについて説明します。"
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: 37cf466a-5293-44c0-a4e0-037e5d292214
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2017
ms.author: vturecek
ms.openlocfilehash: aca8cf2b94e8b746a5cac6af021c7221a29b7345
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="reliable-actors-state-management"></a>Reliable Actors の状態管理
Reliable Actors は、ロジックと状態の両方をカプセル化できるシングル スレッド オブジェクトです。 アクターは Reliable Services 上で実行されるため、Reliable Services と同じ永続化およびレプリケーション メカニズムを使用して、状態を確実に保持することができます。 このため、エラー後やガベージ コレクションに続く再アクティベーション時、リソース分散またはアップグレードのためにクラスター内のノード間を移動されたときでも、アクターは状態を失いません。

## <a name="state-persistence-and-replication"></a>状態の永続性とレプリケーション
各アクター インスタンスは一意の ID にマッピングされるため、すべての Reliable Actors は、 *ステートフル* であると見なされます。 つまり、同じアクター ID を繰り返し呼び出すと、同じアクター インスタンスにルーティングされます。 一方ステートレス システムでは、クライアント呼び出しが常に同じサーバーにルーティングされるとは限りません。 このため、アクター サービスは常にステートフルなサービスです。

アクターをステートフルと見なすことができても、状態を確実に格納しているとは限りません。 アクターは、データ ストレージの要件に基づいて、次のような状態の永続性とレプリケーションのレベルを選択できます。

* **永続化状態:** 状態はディスクに永続化され、3 つ以上のレプリカにレプリケートされます。 これは、完全なクラスター停止があっても状態を永続化できる、最も持続性のある状態ストレージ オプションです。
* **揮発状態:** 状態は 3 つ以上のレプリカにレプリケートされ、メモリだけに保持されます。 これにより、ノード障害時、アクター障害時、およびアップグレードとリソース分散中の回復力が得られます。 ただし、状態はディスクに保持されません。 従って、すべてのレプリカが同時に失われると状態も失われます。
* **非永続化状態:** 状態はレプリケートされず、ディスクにも書き込まれません。 単に状態を確実に保持する必要がないアクター レベルです。

永続性の各レベルは、単にサービスの*状態プロバイダー*と*レプリケーション*構成が異なるだけです。 状態がディスクに書き込まれるかどうかは、状態プロバイダーによって決まります。これは、状態を格納する Reliable Service のコンポーネントです。 またレプリケーションは、サービスがデプロイされるレプリカ数によって決まります。 Reliable Services と同様に、状態プロバイダーもレプリカ数も手動で簡単に設定できます。 アクター フレームワークは、属性を提供します。これはアクターに使用された場合、既定の状態プロバイダーとレプリカ数の自動生成設定を自動的に選択して、上記の 3 つの永続性設定のいずれかを実現します。 StatePersistence 属性は、派生クラスによって継承されません。各 Actor 型は、独自の StatePersistence レベルを提供する必要があります。

### <a name="persisted-state"></a>永続化状態
```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl  extends FabricActor implements MyActor
{
}
```  
この設定は、ディスクにデータを格納する状態プロバイダーを使用し、自動的にサービス レプリカ数を 3 に設定します。

### <a name="volatile-state"></a>揮発状態
```csharp
[StatePersistence(StatePersistence.Volatile)]
class MyActor : Actor, IMyActor
{
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Volatile)
class MyActorImpl extends FabricActor implements MyActor
{
}
```
この設定は、メモリ内のみの状態プロバイダーを使用し、レプリカ数を 3 に設定します。

### <a name="no-persisted-state"></a>非永続化状態
```csharp
[StatePersistence(StatePersistence.None)]
class MyActor : Actor, IMyActor
{
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.None)
class MyActorImpl extends FabricActor implements MyActor
{
}
```
この設定は、メモリ内のみの状態プロバイダーを使用し、レプリカ数を 1 に設定します。

### <a name="defaults-and-generated-settings"></a>既定値と生成される設定
`StatePersistence`属性を使用する場合、状態プロバイダーはアクター サービスが開始される際に、ランタイムで自動的に選択されます。 ただし、レプリカ数は、コンパイル時に Visual Studio アクター ビルド ツールによって設定されます。 ビルド ツールは自動的にアクター サービスの*既定のサービス*を ApplicationManifest.xml 内に生成します。 **最小レプリカ セット サイズ**と**ターゲット レプリカ セット サイズ**のために、パラメーターが作成されます。

これらのパラメーターは手動で変更できます。 ただし`StatePersistence`属性が変更されるたびに、選択された`StatePersistence`属性の既定のレプリカ セット サイズ値にパラメーターは設定され、前の値はオーバーライドされます。 つまり、`StatePersistence`属性の値を変更すると、ServiceManifest.xml に設定した値がビルド時*のみ*にオーバーライドされます。

```xml
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application12Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Parameters>
      <Parameter Name="MyActorService_PartitionCount" DefaultValue="10" />
      <Parameter Name="MyActorService_MinReplicaSetSize" DefaultValue="3" />
      <Parameter Name="MyActorService_TargetReplicaSetSize" DefaultValue="3" />
   </Parameters>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyActorPkg" ServiceManifestVersion="1.0.0" />
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="MyActorService" GeneratedIdRef="77d965dc-85fb-488c-bd06-c6c1fe29d593|Persisted">
         <StatefulService ServiceTypeName="MyActorServiceType" TargetReplicaSetSize="[MyActorService_TargetReplicaSetSize]" MinReplicaSetSize="[MyActorService_MinReplicaSetSize]">
            <UniformInt64Partition PartitionCount="[MyActorService_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
         </StatefulService>
      </Service>
   </DefaultServices>
</ApplicationManifest>
```

## <a name="state-manager"></a>状態マネージャー
各アクター インスタンスは、独自の状態マネージャーを持ちます。これは、辞書のようなデータ構造で、キーと値のペアを確実に保持します。 状態マネージャーは、状態プロバイダーのラッパーです。 永続化設定の使用に関係なくデータの格納に使用できます。 データ保持中のローリング アップグレードを通じて、実行中のアクター サービスを揮発 (メモリ内のみ) 状態設定から永続化状態設定に変更できることは保証されません。 ただし、実行中のサービスのレプリカ数を変更することはできます。

状態マネージャー キーは、文字列でなければなりません。 値はジェネリックでカスタム型を含む任意のタイプです。 状態マネージャーに格納される値は、データ コントラクト シリアル化可能である必要があります。アクター状態の永続性設定によっては、これらの値がレプリケーション中にネットワーク経由で他のノードに送信されたり、ディスクに書き込まれたりする可能性があるためです。

状態マネージャーは、Reliable Dictionary に含まれているものと同様、状態管理のための一般的な辞書メソッドを公開します。

### <a name="accessing-state"></a>状態へのアクセス
状態には、状態マネージャーからキーでアクセスできます。 状態マネージャーのメソッドは、すべて非同期です。アクターが永続化状態を持つ場合に、ディスク I/O が必要になる可能性があるためです。 最初のアクセス時に、状態オブジェクトがメモリにキャッシュされます。 その後のアクセス操作では、メモリから直接オブジェクトにアクセスし同期的に戻ります。ディスク I/O や非同期のコンテキスト切り替えのオーバーヘッドは生じません。 状態オブジェクトは、次の場合に、キャッシュから削除されます。

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

### <a name="saving-state"></a>状態の保存
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

### <a name="removing-state"></a>状態の削除
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
