---
title: ステートフル サービスの単体テストの開発
description: ステートフル サービス向け Azure Service Fabric での単体テスト、および開発中に留意すべき特別な考慮事項について説明します。
ms.topic: conceptual
ms.date: 09/04/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: 287c0544daa3c44d91fd336b502c496b9b4bb266
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89011397"
---
# <a name="create-unit-tests-for-stateful-services"></a>ステートフル サービスの単体テストの作成
Service Fabric ステートフル サービスの単体テストにより、従来のアプリケーションまたはドメイン固有の単体テストでは必ずしも検出されない一般的な誤りが明らかになります。 ステートフル サービスの単体テストを作成するときに、留意すべき特別な考慮事項があります。

1. 各レプリカによってアプリケーション コードが実行されますが、それぞれ異なるコンテキストで実行されます。 サービスで 3 つのレプリカを使用する場合、サービス コードは異なるコンテキスト/役割の下で 3 つのノード上で並列実行されます。
2. ステートフル サービス内に保存される状態は、すべてのレプリカ間で一貫している必要があります。 この一貫性は、状態マネージャーとリライアブル コレクションによってすぐに実現されます。 ただし、メモリ内の状態はアプリケーション コードで管理する必要があります。
3. 各レプリカは、クラスター上で実行中のある時点で役割が変わります。 プライマリをホストしているノードが使用できなくなった場合や過負荷になった場合は、セカンダリ レプリカがプライマリになります。 これは Service Fabric の自然な動作であるため、サービスは最終的に異なる役割で実行されるように計画する必要があります。

この記事は、「[Unit testing stateful services in Service Fabric (Service Fabric のステートフル サービスの単体テスト)](service-fabric-concepts-unit-testing.md)」を読了していることを前提としています。

## <a name="the-servicefabricmocks-library"></a>ServiceFabric.Mocks ライブラリ
[ServiceFabric.Mocks](https://www.nuget.org/packages/ServiceFabric.Mocks/) のバージョン 3.3.0 以降では、レプリカのオーケストレーションと状態管理の両方をモックするための API が提供されています。 これは各例で使用されます。

[Nuget](https://www.nuget.org/packages/ServiceFabric.Mocks/)
[GitHub](https://github.com/loekd/ServiceFabric.Mocks)

*ServiceFabric.Mocks は、Microsoft が所有または管理しているわけではありません。ただし、現在、ステートフル サービスの単体テストのための Microsoft 推奨ライブラリとなっています。*

## <a name="set-up-the-mock-orchestration-and-state"></a>モック オーケストレーションおよび状態を設定する
テストの準備 (Arrange) 部分の一環として、モック レプリカ セットと状態マネージャーが作成されます。 レプリカ セットは、各レプリカのテスト対象となるサービスのインスタンスの作成を制御します。 また、`OnChangeRole` や `RunAsync` などのライフサイクル イベントの実行も制御します。 モック状態マネージャーにより、状態マネージャーに対して実行される操作が、実際の状態マネージャーの場合と同様に実行され、維持されるようになります。

1. テスト対象のサービスをインスタンス化するサービス ファクトリ デリゲートを作成します。 これは、Service Fabric サービスまたは Service Fabric アクターの `Program.cs` で通常見られるサービス ファクトリ コールバックと類似しているか同じである必要があります。 これは次のシグネチャに従う必要があります。
   ```csharp
   MyStatefulService CreateMyStatefulService(StatefulServiceContext context, IReliableStateManagerReplica2 stateManager)
   ```
2. `MockReliableStateManager` クラスのインスタンスを作成します。 これにより、状態マネージャーとのすべての対話がモックされます。
3. `MockStatefulServiceReplicaSet<TStatefulService>` のインスタンスを作成します。`TStatefulService` は、テスト対象のサービスの種類です。 これには、手順 1. で作成したデリゲートと、手順 2. でインスタンス化された状態マネージャーが必要です。
4. レプリカ セットにレプリカを追加します。 役割 (Primary、ActiveSecondary、IdleSecondary など) とレプリカの ID を指定します。
   > レプリカ ID は必ず保持してください。 これらは、単体テストの実行 (Act) 部分とアサート (Assert) 部分で使用される可能性があります。

```csharp
//service factory to instruct how to create the service instance
var serviceFactory = (StatefulServiceContext context, IReliableStateManagerReplica2 stateManager) => new MyStatefulService(context, stateManager);
//instantiate a new mock state manager
var stateManager = new MockReliableStateManager();
//instantiate a new replica set with the service factory and state manager
var replicaSet = new MockStatefulServiceReplicaSet<MyStatefulService>(CreateStatefulService, stateManager);
//add a new Primary replica with id 1
await replicaSet.AddReplicaAsync(ReplicaRole.Primary, 1);
//add a new ActiveSecondary replica with id 2
await replicaSet.AddReplicaAsync(ReplicaRole.ActiveSecondary, 2);
//add a second ActiveSecondary replica with id 3
await replicaSet.AddReplicaAsync(ReplicaRole.ActiveSecondary, 3);
```

## <a name="execute-service-requests"></a>サービス要求を実行する
サービス要求は、コンビニエンス プロパティと参照を使用して特定のレプリカで実行できます。
```csharp
const string stateName = "test";
var payload = new Payload(StatePayload);

//execute a request on the primary replica using
await replicaSet.Primary.ServiceInstance.InsertAsync(stateName, payload);

//execute a request against replica with id 2
await replicaSet[2].ServiceInstance.InsertAsync(stateName, payload);

//execute a request against one of the active secondary replicas
await replicaSet.FirstActiveSecondary.InsertAsync(stateName, payload);
```

## <a name="execute-a-service-move"></a>サービス移行を実行する
モック レプリカ セットでは、さまざまな種類のサービス移行をトリガーするコンビニエンス メソッドをいくつか公開しています。
```csharp
//promote the first active secondary to primary
replicaSet.PromoteNewReplicaToPrimaryAsync();
//promote the secondary with replica id 4 to primary
replicaSet.PromoteNewReplicaToPrimaryAsync(4);

//promote the first idle secondary to an active secondary
PromoteIdleSecondaryToActiveSecondaryAsync();
//promote idle secondary with replica id 4 to active secondary
PromoteIdleSecondaryToActiveSecondaryAsync(4);

//add a new replica with randomly assigned replica id and promote it to primary
PromoteNewReplicaToPrimaryAsync()
//add a new replica with replica id 4 and promote it to primary
PromoteNewReplicaToPrimaryAsync(4)
```

## <a name="putting-it-all-together"></a>まとめ
次のテストでは、3 ノード レプリカ セットを設定し、役割が変わった後にセカンダリからデータを使用できるかどうかを検証します。 このテストによって検出される可能性のある一般的な問題は、`InsertAsync` の実行時に追加されたデータが、`CommitAsync` を実行せずにメモリ内またはリライアブル コレクションに保存されているケースです。 どちらの場合も、セカンダリはプライマリと同期されていません。 これは、サービスの移行後の一貫性のない応答の原因となります。

```csharp
[TestMethod]
public async Task TestServiceState_InMemoryState_PromoteActiveSecondary()
{
    var stateManager = new MockReliableStateManager();
    var replicaSet = new MockStatefulServiceReplicaSet<MyStatefulService>(CreateStatefulService, stateManager);
    await replicaSet.AddReplicaAsync(ReplicaRole.Primary, 1);
    await replicaSet.AddReplicaAsync(ReplicaRole.ActiveSecondary, 2);
    await replicaSet.AddReplicaAsync(ReplicaRole.ActiveSecondary, 3);

    const string stateName = "test";
    var payload = new Payload(StatePayload);

    //insert data
    await replicaSet.Primary.ServiceInstance.InsertAsync(stateName, payload);
    //promote one of the secondaries to primary
    await replicaSet.PromoteActiveSecondaryToPrimaryAsync(2);
    //get data
    var payloads = (await replicaSet.Primary.ServiceInstance.GetPayloadsAsync()).ToList();

    //data should match what was inserted against the primary
    Assert.IsTrue(payloads.Count == 1);
    Assert.IsTrue(payloads[0].Content == payload.Content);

    //verify the data was saved against the reliable dictionary
    var dictionary = await StateManager.GetOrAddAsync<IReliableDictionary<string, Payload>>(MyStatefulService.StateManagerDictionaryKey);
    using(var tx = StateManager.CreateTransaction())
    {
        var payload = await dictionary.TryGetValue(stateName);
        Assert.IsTrue(payload.HasValue);
        Assert.IsTrue(payload.Value.Content == payload.Content);
    }
}
```

## <a name="next-steps"></a>次のステップ
[サービス間の通信](service-fabric-testability-scenarios-service-communication.md)をテストし、[制御された混乱を使用して障害をシミュレート](service-fabric-controlled-chaos.md)する方法を確認します。
