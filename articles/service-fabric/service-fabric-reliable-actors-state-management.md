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
ms.date: 02/10/2017
ms.author: vturecek
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 194935398809b1905ddc4100b5f09fce7f75a796


---
# <a name="reliable-actors-state-management"></a>Reliable Actors の状態管理
Reliable Actors は、ロジックと状態の両方をカプセル化できるシングル スレッド オブジェクトです。 アクターは Reliable Services 上で実行されるため、Reliable Services によって使用されるのと同じ永続化およびレプリケーション メカニズムを使用して、状態を確実に保持することができます。 このため、エラー後や、ガベージ コレクションに続く再アクティブ化の後や、リソース分散またはアップグレードのためにクラスター内のノード間を移動されたときでも、アクターは状態を失いません。

## <a name="state-persistence-and-replication"></a>状態の永続性とレプリケーション
各アクター インスタンスは一意の ID にマッピングされるため、すべての Reliable Actors は、 *ステートフル* であると見なされます。 つまり、同じアクター ID を繰り返し呼び出すと、同じアクター インスタンスにルーティングされます。 これは、クライアント呼び出しが常に同じサーバーにルーティングされるとは限らないステートレス システムとは異なります。 このため、アクター サービスは常にステートフルなサービスです。

ただし、アクターはステートフルと見なせるとしても、状態を確実に格納しているとは限りません。 アクターは、データ ストレージの要件に基づいて、次のような状態の永続性とレプリケーションのレベルを選択できます。

* **永続化状態:** 状態はディスクに永続化され、3 つ以上のレプリカにレプリケートされます。 これは、完全なクラスター停止があっても状態を永続化できる、最も持続性のある状態ストレージ オプションです。
* **揮発状態:** 状態は 3 つ以上のレプリカにレプリケートされ、メモリだけに保持されます。 これにより、ノード障害時、アクター障害時、およびアップグレードとリソース分散中の回復力が得られます。 ただし、状態はディスクに永続化されないため、すべてのレプリカが同時に失われると、状態も失われます。
* **非永続化状態:** 状態はレプリケートされず、ディスクにも書き込まれません。 単に状態を確実に保持する必要がないアクター用です。

永続性の各レベルは、単にサービスの*状態プロバイダー*と*レプリケーション*構成が異なるだけです。 状態がディスクに書き込まれるかどうかは、*状態プロバイダー*によって決まります。これは、状態を格納する Reliable Service のコンポーネントです。また、レプリケーションによって、いくつのレプリカにサービスがデプロイされるかが決まります。 Reliable Services と同様に、状態プロバイダーもレプリカ数も手動で簡単に設定できます。 アクター フレームワークは、属性を提供します。これは、アクターに使用された場合に、既定の状態プロバイダーとレプリカ数の自動生成設定を自動的に選択して、上記の 3 つの永続性設定のいずれかを実現します。

### <a name="persisted-state"></a>永続化状態
```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
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
この設定は、メモリ内のみの状態プロバイダーを使用し、レプリカ数を 3 に設定します。

### <a name="no-persisted-state"></a>非永続化状態
```csharp
[StatePersistence(StatePersistence.None)]
class MyActor : Actor, IMyActor
{
}
```
この設定は、メモリ内のみの状態プロバイダーを使用し、レプリカ数を 1 に設定します。

### <a name="defaults-and-generated-settings"></a>既定値と生成される設定
`StatePersistence` 属性を使用する場合、状態プロバイダーは実行時にアクター サービスが開始されるときに自動的に選択されます。 ただし、レプリカ数は、コンパイル時に Visual Studio アクター ビルド ツールによって設定されます。 ビルド ツールは自動的にアクター サービスの*既定のサービス*を ApplicationManifest.xml 内に生成します。 **最小レプリカ セット サイズ**と**ターゲット レプリカ セット サイズ**のために、パラメーターが作成されます。 もちろん、これらのパラメーターは手動で変更できます。ただし、`StatePersistence` 属性が変更されるたびに、パラメーターは選択された `StatePersistence` 属性の既定のレプリカ セット サイズ値に設定され、前の値は上書きされます。 つまり、`StatePersistence` 属性の値を変更すると、ServiceManifest.xml に設定した値**のみ**がビルド時にオーバーライドされます。 

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
各アクター インスタンスは、独自の状態マネージャーを持ちます。これは、辞書のようなデータ構造で、キーと値のペアを確実に保持します。 状態マネージャーは、状態プロバイダーのラッパーです。 どの永続性設定が使用されるかにかかわらず、データを格納するために使用できますが、ローリング アップグレードを通じて、データを保持しつつ、実行中のアクター サービスを揮発 (メモリ内のみ) 状態設定から永続化状態設定に変更できることは保証されません。 ただし、実行中のサービスのレプリカ数を変更することはできます。 

状態マネージャー キーは、文字列である必要があります。一方、値はジェネリックであり、カスタム型も含めて、任意の型にすることができます。 状態マネージャーに格納される値は、データ コントラクト シリアル化可能である必要があります。これらの値は、アクターの状態の永続性設定によっては、レプリケーション中にネットワーク経由で他のノードに送信されたり、ディスクに書き込まれたりする可能性があるためです。 

状態マネージャーは、Reliable Dictionary に含まれているような、状態を管理するための一般的な辞書メソッドを公開します。

### <a name="accessing-state"></a>状態へのアクセス
状態には、状態マネージャーからキーでアクセスできます。 状態マネージャーのメソッドは、すべて非同期です。アクターが永続化状態を持つ場合に、ディスク I/O が必要になる可能性があるためです。 最初のアクセス時に、状態オブジェクトがメモリにキャッシュされます。 その後のアクセス操作では、メモリのオブジェクトに直接アクセスし、同期的に戻ります。ディスク I/O や、非同期のコンテキスト切り替えのオーバーヘッドは生じません。 状態オブジェクトは、次の場合に、キャッシュから削除されます。

* アクター メソッドが、状態マネージャーからオブジェクトを取得した後に、未処理の例外をスローする。
* アクターが、非アクティブ化の後、またはエラーのために、再アクティブ化される。
* 状態プロバイダーが、状態をディスクにページングする場合。 この動作は、状態プロバイダーの実装に依存します。 `Persisted` 設定用の既定の状態プロバイダーは、この動作を行います。 

状態は、次のように標準の *Get* 操作を使用して取得できます。指定されたキーのエントリが存在しない場合は、`KeyNotFoundException` がスローされます。 

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

状態は、次のように *TryGet* メソッドを使用して取得することもできます。指定されたキーのエントリが存在しない場合も、例外はスローされません。

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

### <a name="saving-state"></a>状態の保存
状態マネージャーの取得メソッドは、ローカル メモリ内のオブジェクトへの参照を返します。 ローカル メモリ内のこのオブジェクトを変更しただけでは、変更は永続的に保存されません。 オブジェクトが状態マネージャーから取得され、変更された場合、それを永続的に保存するには、状態マネージャーに再挿入する必要があります。

状態を挿入するには、次のように、無条件の *Set* を使用します。これは、`dictionary["key"] = value` 構文と同等です。

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

状態を追加するには、次のように、*Add* メソッドを使用します。既に存在するキーを追加しようとすると、`InvalidOperationException` がスローされます。

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

状態の追加には、次のように、*TryAdd* メソッドを使用することもできます。既に存在するキーを追加しようとしても、例外はスローされません。

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

アクター メソッドの最後に、状態マネージャーは、挿入または更新操作によって追加または変更されたすべての値を自動的に保存します。 "保存" には、使用されている設定に応じて、ディスクへの永続化やレプリケーションも含まれます。 変更されていない値は、永続化もレプリケートもされません。 値が変更されなかった場合、保存操作では何も行われません。 保存が失敗した場合、変更された状態は破棄され、元の状態が再度読み込まれます。

状態は、次のように、アクター ベースの `SaveStateAsync` メソッドを呼び出すことで、手動で保存することもできます。

```csharp
async Task IMyActor.SetCountAsync(int count)
{
    await this.StateManager.AddOrUpdateStateAsync("count", count, (key, value) => count > value ? count : value);

    await this.SaveStateAsync();
}
```

### <a name="removing-state"></a>状態の削除
状態は、 *Remove* メソッドを呼び出すことによって、アクターの状態マネージャーから完全に削除することができます。 このメソッドは、存在しないキーを削除しようとすると、 `KeyNotFoundException` をスローします。

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

状態は、*TryRemove* メソッドを使用して完全に削除することもできます。この場合は、存在しないキーを削除しようとしても、例外はスローされません。

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

## <a name="next-steps"></a>次のステップ
* [アクター型のシリアル化](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)
* [アクターのポリモーフィズムとオブジェクト指向設計パターン](service-fabric-reliable-actors-polymorphism.md)
* [アクターの診断とパフォーマンスの監視](service-fabric-reliable-actors-diagnostics.md)
* [Actor API リファレンス ドキュメント](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [コード サンプル](https://github.com/Azure/servicefabric-samples)




<!--HONumber=Nov16_HO3-->


