---
title: "Service Fabric の Reliable Actors | Microsoft Docs"
description: "Reliable Actors と Reliable Services の階層的な関係と、Reliable Actors による Service Fabric プラットフォームの機能の使用方法について説明します。"
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: amanbha
ms.assetid: 45839a7f-0536-46f1-ae2b-8ba3556407fb
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/10/2017
ms.author: vturecek
translationtype: Human Translation
ms.sourcegitcommit: 6e0ad6b5bec11c5197dd7bded64168a1b8cc2fdd
ms.openlocfilehash: ba07b1bf1b49ebb24a7d5cfbaad71f5f17c49192
ms.lasthandoff: 03/28/2017


---
# <a name="how-reliable-actors-use-the-service-fabric-platform"></a>高信頼アクターの Service Fabric プラットフォームの使用方法
この記事では、Reliable Actors による Service Fabric プラットフォームの使用方法について説明します。 Reliable Actors は、 *アクター サービス*と呼ばれるステートフル リライアブル サービスの実装にホストされるフレームワークで実行されます。 アクター サービスには、アクターのライフサイクルとメッセージ ディスパッチを管理するうえで必要なコンポーネントがすべて含まれています。

* アクター ランタイムはライフサイクルとガベージ コレクションを管理し、シングルスレッドのアクセスを強制します。
* アクター サービス リモート処理リスナーはアクターに対するリモート アクセス呼び出しを受け取り、ディスパッチャに送信して、適切なアクター インスタンスにルーティングします。
* アクター状態プロバイダーは各種状態プロバイダー (Reliable Collections 状態プロバイダーなど) をラップし、アクターの状態管理のためのアダプターを提供します。

これらのコンポーネントで Reliable Actors フレームワークが構成されます。 

## <a name="service-layering"></a>サービスのレイヤー
アクター サービス自体は Reliable Service であるため、Reliable Services の[アプリケーション モデル](service-fabric-application-model.md)、ライフサイクル、[パッケージ化](service-fabric-package-apps.md)、[デプロイメント](service-fabric-deploy-remove-applications.md)、アップグレード、スケーリングの各概念は、すべてアクター サービスにも同様に適用されます。 

![Actor Service layering][1]

上記の図は、Service Fabric アプリケーション フレームワークとユーザー コードの関係を示しています。 青色の要素は Reliable Services アプリケーション フレームワークを表しています。また、オレンジ色の要素は Reliable Actors フレームワークを表し、緑色の要素はユーザー コードを表しています。 

Reliable Services では、サービスは `StatefulService` クラスを継承します。これは、`StatefulServiceBase` (ステートレス サービスの場合は `StatelessService`) から派生したものです。 Reliable Actors では、アクターが実行されるアクター パターンを実装する `StatefulServiceBase` クラスの別の実装であるアクター サービスを使用します。 アクター サービス自体は `StatefulServiceBase` の実装にすぎないため、`StatefulService` を継承したときと同様に、`ActorService` から派生した独自のサービスを記述し、サービスレベルの機能を実装できます。次にその例を示します。

* サービスのバックアップと復元。
* すべてのアクターで共有される機能 (サーキット ブレーカーなど)。
* 個別のアクターだけでなく、アクター サービス自体でのリモート処理プロシージャの呼び出し。 

### <a name="using-the-actor-service"></a>アクター サービスの使用
アクター インスタンスは、それが実行されるアクター サービスにアクセスできます。 アクター インスタンスは、アクター サービスを通じて、パーティション ID、サービス名、アプリケーション名、その他 Service Fabric プラットフォームに固有の情報を含むサービス コンテキストをプログラムで取得できます。

```csharp
Task MyActorMethod()
{
    Guid partitionId = this.ActorService.Context.PartitionId;
    string serviceTypeName = this.ActorService.Context.ServiceTypeName;
    Uri serviceInstanceName = this.ActorService.Context.ServiceName;
    string applicationInstanceName = this.ActorService.Context.CodePackageActivationContext.ApplicationName;
}
```

アクター サービスは、すべての Reliable Services と同様に、Service Fabric ランタイムのサービスの種類に登録する必要があります。 アクター サービスでアクター インスタンスを実行するには、アクターの種類もアクター サービスに登録する必要があります。 `ActorRuntime` 登録メソッドは、アクターに対してこの処理を実行します。 最も単純なケースでは、単にアクターの種類を登録するだけでかまいません。そうすれば、既定の設定のアクター サービスが暗黙的に使用されます。

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>().GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```  

また、登録メソッドによって提供されるラムダを使ってアクター サービスを自分で構築することもできます。 この場合は、アクター サービスを構成し、アクター インスタンスを明示的に構築できます。そのため、コンストラクターを通じてアクターに依存関係を挿入できます。

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>(
            (context, actorType) => new ActorService(context, actorType, () => new MyActor()))
            .GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```

### <a name="actor-service-methods"></a>アクター サービスのメソッド
アクター サービスは `IActorService` を実装し、それが `IService` を実装します。 これは、Reliable Services リモート処理によって使用されるインターフェイスであり、サービス メソッドでのリモート プロシージャ呼び出しを可能にします。 これには、サービスのリモート処理によってリモートで呼び出すことのできるサービスレベルのメソッドが含まれます。

#### <a name="enumerating-actors"></a>アクターの列挙
アクター サービスにより、クライアントは、サービスによってホストされるアクターのメタデータを列挙できます。 アクター サービスはパーティション分割されたステートフル サービスであるため、列挙はパーティションごとに実行されます。 各パーティションには多数のアクターが含まれる可能性があるため、列挙はページングされた結果のセットとして返されます。 ページはすべてのページが読み取られるまでループされます。 次の例は、アクター サービスの 1 つのパーティションに含まれるすべてのアクティブ アクターのリストを作成する方法を示しています。

```csharp
IActorService actorServiceProxy = ActorServiceProxy.Create(
    new Uri("fabric:/MyApp/MyService"), partitionKey);

ContinuationToken continuationToken = null;
List<ActorInformation> activeActors = new List<ActorInformation>();

do
{
    PagedResult<ActorInformation> page = await actorServiceProxy.GetActorsAsync(continuationToken, cancellationToken);

    activeActors.AddRange(page.Items.Where(x => x.IsActive));

    continuationToken = page.ContinuationToken;
}
while (continuationToken != null);
```

#### <a name="deleting-actors"></a>アクターの削除
アクター サービスは、アクターを削除する機能も提供します。

```csharp
ActorId actorToDelete = new ActorId(id);

IActorService myActorServiceProxy = ActorServiceProxy.Create(
    new Uri("fabric:/MyApp/MyService"), actorToDelete);

await myActorServiceProxy.DeleteActorAsync(actorToDelete, cancellationToken)
```

アクターとその状態の削除の詳細については、 [アクターのライフサイクルに関するドキュメント](service-fabric-reliable-actors-lifecycle.md)を参照してください。

### <a name="custom-actor-service"></a>カスタム アクター サービス
アクター登録ラムダを使えば、`ActorService` から派生する独自のカスタム アクター サービス (独自のサービスレベルの機能を実装できる) を登録することもできます。 そのためには、`ActorService` を継承するサービス クラスを記述します。 カスタム アクター サービスは、`ActorService` からすべてのアクター ランタイム機能を継承します。カスタム アクター サービスを使えば、独自のサービス メソッドを実装できます。

```csharp
class MyActorService : ActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, Func<ActorBase> newActor)
        : base(context, typeInfo, newActor)
    { }
}
```

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>(
            (context, actorType) => new MyActorService(context, actorType, () => new MyActor()))
            .GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```


#### <a name="implementing-actor-back-up-and-restore"></a>アクターのバックアップと復元の実装
 次の例のカスタム アクター サービスは、既に `ActorService`に存在するリモート処理リスナーを活用して、アクター データをバックアップするメソッドを公開しています。

```csharp
public interface IMyActorService : IService
{
    Task BackupActorsAsync();
}

class MyActorService : ActorService, IMyActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, Func<ActorBase> newActor)
        : base(context, typeInfo, newActor)
    { }

    public Task BackupActorsAsync()
    {
        return this.BackupAsync(new BackupDescription(PerformBackupAsync));
    }

    private async Task<bool> PerformBackupAsync(BackupInfo backupInfo, CancellationToken cancellationToken)
    {
        try
        {
           // store the contents of backupInfo.Directory
           return true;
        }
        finally
        {
           Directory.Delete(backupInfo.Directory, recursive: true);
        }
    }
}
```

この例では、`IMyActorService` は `IService` を実装し、`MyActorService` によって実装されるリモート処理コントラクトです。 このリモート処理コントラクトを追加すれば、`IMyActorService` のメソッドは、`ActorServiceProxy` を使用してリモート処理プロキシを作成することにより、クライアントから利用できるようになります。

```csharp
IMyActorService myActorServiceProxy = ActorServiceProxy.Create<IMyActorService>(
    new Uri("fabric:/MyApp/MyService"), ActorId.CreateRandom());

await myActorServiceProxy.BackupActorsAsync();
```


## <a name="application-model"></a>アプリケーション モデル
アクター サービスは Reliable Services であるため、アプリケーション モデルは同じです。 ただし、アクター フレームワーク ビルド ツールによって、アプリケーション モデル ファイルの多くが自動的に生成されます。

### <a name="service-manifest"></a>サービス マニフェスト
アクター サービスの ServiceManifest.xml の内容は、アクター フレームワーク ビルド ツールによって自動的に生成されます。 次のトピックがあります。

* アクター サービスの種類。 種類の名前は、アクター プロジェクトの名前に基づいて生成されます。 アクターの永続化属性に応じて、HasPersistedState フラグも設定されます。
* コード パッケージ。
* 構成パッケージ。
* リソースとエンドポイント

### <a name="application-manifest"></a>アプリケーション マニフェスト
アクター フレームワーク ビルド ツールによって、アクター サービスの既定のサービス定義が自動的に作成されます。 また、ビルド ツールによって既定のサービス プロパティが設定されます。

* レプリカ セットの数は、アクターの永続化属性によって決まります。 アクターの永続化属性が変更されるたびに、それに応じて既定のサービス定義のレプリカ セットの数がリセットされます。
* パーティション構成と範囲は Uniform Int64 とフル Int64 キー範囲に設定されます。

## <a name="service-fabric-partition-concepts-for-actors"></a>アクターの Service Fabric のパーティションの概念
アクター サービスはパーティション分割されたステートフル サービスです。 アクター サービスの各パーティションには、アクターのセットが含まれています。 サービス パーティションは、Service Fabric の複数のノードに自動的に分散されます。 そのため、アクター インスタンスが分散されることになります。

![Actor partitioning and distribution][5]

Reliable Services は、さまざまなパーティション構成とパーティション キー範囲で作成できます。 アクター サービスは、Int64 パーティション構成とフル Int64 キー範囲でアクターをパーティションにマップします。 

### <a name="actor-id"></a>アクター ID
サービスで作成される各アクターには、 `ActorId` クラスで表される一意の ID が関連付けられます。 `ActorId` は、ランダム ID を生成することにより、サービス パーティション間での均一なアクターの分散に使用できるあいまいな ID 値です。

```csharp
ActorProxy.Create<IMyActor>(ActorId.CreateRandom());
```

`ActorId` はいずれも Int64 にハッシュされます。アクター サービスで Int64 パーティション構成とフル Int64 キー範囲を使用する必要があるのはこのためです。 ただし、`ActorID` には、GUID、文字列、Int64 などのカスタム ID 値を使用できます。 

```csharp
ActorProxy.Create<IMyActor>(new ActorId(Guid.NewGuid()));
ActorProxy.Create<IMyActor>(new ActorId("myActorId"));
ActorProxy.Create<IMyActor>(new ActorId(1234));
```

GUID と文字列を使用した場合、値は Int64 にハッシュされます。 ただし、Int64 を `ActorId`に明示的に指定した場合は、Int64 はハッシュされることなくパーティションに直接マップされます。 これを使用して、アクターを配置するパーティションを制御できます。

## <a name="next-steps"></a>次のステップ
* [アクターの状態管理](service-fabric-reliable-actors-state-management.md)
* [アクターのライフサイクルとガベージ コレクション](service-fabric-reliable-actors-lifecycle.md)
* [アクターの API リファレンス ドキュメント](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [コード サンプル](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png

