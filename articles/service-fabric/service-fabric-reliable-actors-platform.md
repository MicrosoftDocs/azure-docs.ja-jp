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
ms.date: 09/20/2017
ms.author: vturecek
ms.openlocfilehash: 43b3f758fe7017c0ec949ba6e28b76438cf1bc13
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="how-reliable-actors-use-the-service-fabric-platform"></a>高信頼アクターの Service Fabric プラットフォームの使用方法
この記事では、Reliable Actors による Azure Service Fabric プラットフォームの使用方法について説明します。 Reliable Actors は、*アクター サービス*と呼ばれるステートフル リライアブル サービスの実装にホストされるフレームワークで実行されます。 アクター サービスには、アクターのライフサイクルとメッセージ ディスパッチを管理するうえで必要なコンポーネントがすべて含まれています。

* アクター ランタイムはライフサイクルとガベージ コレクションを管理し、シングルスレッドのアクセスを強制します。
* アクター サービス リモート処理リスナーはアクターに対するリモート アクセス呼び出しを受け取り、ディスパッチャに送信して、適切なアクター インスタンスにルーティングします。
* アクター状態プロバイダーは各種状態プロバイダー (Reliable Collections 状態プロバイダーなど) をラップし、アクターの状態管理のためのアダプターを提供します。

これらのコンポーネントで Reliable Actors フレームワークが構成されます。

## <a name="service-layering"></a>サービスのレイヤー
アクター サービス自体は Reliable Service であるため、Reliable Services の[アプリケーション モデル](service-fabric-application-model.md)、ライフサイクル、[パッケージ化](service-fabric-package-apps.md)、[デプロイメント](service-fabric-deploy-remove-applications.md)、アップグレード、スケーリングの各概念は、すべてアクター サービスにも同様に適用されます。

![アクター サービスのレイヤー][1]

上記の図は、Service Fabric アプリケーション フレームワークとユーザー コードの関係を示しています。 青色の要素は Reliable Services アプリケーション フレームワークを表しています。また、オレンジ色の要素は Reliable Actors フレームワークを表し、緑色の要素はユーザー コードを表しています。

Reliable Services では、サービスは `StatefulService` クラスを継承します。 このクラス自体は、`StatefulServiceBase` (ステートレス サービスの場合は `StatelessService`) から派生します。 Reliable Actors では、アクター サービスを使用します。 アクター サービスは、アクターが実行されるアクター パターンを実装する `StatefulServiceBase` クラスの別の実装です。 アクター サービス自体は `StatefulServiceBase` の実装にすぎないため、`StatefulService` を継承したときと同様に、`ActorService` から派生した独自のサービスを記述し、サービスレベルの機能を実装できます。次にその例を示します。

* サービスのバックアップと復元。
* すべてのアクターで共有される機能 (サーキット ブレーカーなど)。
* アクター サービス自体と個別アクターでのリモート プロシージャ コール。

> [!NOTE]
> ステートフル サービスは、Java/Linux では現在サポートされていません。

### <a name="using-the-actor-service"></a>アクター サービスの使用
アクター インスタンスは、それが実行されるアクター サービスにアクセスできます。 アクター インスタンスは、アクター サービスを使用して、サービス コンテキストをプログラムによって取得できます。 サービス コンテキストには、パーティション ID、サービス名、アプリケーション名、他の Service Fabric プラットフォーム固有の情報が含まれます。

```csharp
Task MyActorMethod()
{
    Guid partitionId = this.ActorService.Context.PartitionId;
    string serviceTypeName = this.ActorService.Context.ServiceTypeName;
    Uri serviceInstanceName = this.ActorService.Context.ServiceName;
    string applicationInstanceName = this.ActorService.Context.CodePackageActivationContext.ApplicationName;
}
```
```Java
CompletableFuture<?> MyActorMethod()
{
    UUID partitionId = this.getActorService().getServiceContext().getPartitionId();
    String serviceTypeName = this.getActorService().getServiceContext().getServiceTypeName();
    URI serviceInstanceName = this.getActorService().getServiceContext().getServiceName();
    String applicationInstanceName = this.getActorService().getServiceContext().getCodePackageActivationContext().getApplicationName();
}
```


アクター サービスは、すべての Reliable Services と同様に、Service Fabric ランタイムのサービスの種類に登録する必要があります。 アクター サービスでアクター インスタンスを実行するためには、アクター型もアクター サービスに登録する必要があります。 `ActorRuntime` 登録メソッドは、アクターに対してこの処理を実行します。 最も単純なケースでは、単にアクターの種類を登録するだけでかまいません。そうすれば、既定の設定のアクター サービスが暗黙的に使用されます。

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

また、登録メソッドによって提供されるラムダを使ってアクター サービスを自分で構築することもできます。 これでアクター サービスを構成し、アクター インスタンスを明示的に構築して、コンストラクターを通じてアクターに依存関係を挿入できます。

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
```Java
static class Program
{
    private static void Main()
    {
      ActorRuntime.registerActorAsync(
              MyActor.class,
              (context, actorTypeInfo) -> new FabricActorService(context, actorTypeInfo),
              timeout);

        Thread.sleep(Long.MAX_VALUE);
    }
}
```

### <a name="actor-service-methods"></a>アクター サービスのメソッド
アクター サービスは `IActorService` (C#) または `ActorService` (Java) を実装し、それが `IService` (C#) または `Service` (Java) を実装します。 これは、Reliable Services リモート処理によって使用されるインターフェイスであり、サービス メソッドでのリモート プロシージャ呼び出しを可能にします。 これには、サービスのリモート処理によってリモートで呼び出すことのできるサービスレベルのメソッドが含まれます。

#### <a name="enumerating-actors"></a>アクターの列挙
アクター サービスにより、クライアントは、サービスがホストしているアクターのメタデータを列挙できます。 アクター サービスはパーティション分割されたステートフル サービスであるため、列挙はパーティションごとに実行されます。 各パーティションには多数のアクターが含まれる可能性があるため、列挙はページングされた結果のセットとして返されます。 ページはすべてのページが読み取られるまでループされます。 次の例は、アクター サービスの 1 つのパーティションに含まれるすべてのアクティブ アクターのリストを作成する方法を示しています。

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

```Java
ActorService actorServiceProxy = ActorServiceProxy.create(
    new URI("fabric:/MyApp/MyService"), partitionKey);

ContinuationToken continuationToken = null;
List<ActorInformation> activeActors = new ArrayList<ActorInformation>();

do
{
    PagedResult<ActorInformation> page = actorServiceProxy.getActorsAsync(continuationToken);

    while(ActorInformation x: page.getItems())
    {
         if(x.isActive()){
              activeActors.add(x);
         }
    }

    continuationToken = page.getContinuationToken();
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
```Java
ActorId actorToDelete = new ActorId(id);

ActorService myActorServiceProxy = ActorServiceProxy.create(
    new URI("fabric:/MyApp/MyService"), actorToDelete);

myActorServiceProxy.deleteActorAsync(actorToDelete);
```

アクターとその状態の削除の詳細については、[アクターのライフサイクルに関するドキュメント](service-fabric-reliable-actors-lifecycle.md)を参照してください。

### <a name="custom-actor-service"></a>カスタム アクター サービス
アクター登録ラムダを使用することで、`ActorService` (C#) および `FabricActorService` (Java) から派生する独自のカスタム アクター サービスを登録できます。 このカスタム アクター サービスでは、`ActorService` (C#) または `FabricActorService` (Java) を継承するサービス クラスを記述することで、独自のサービス レベル機能を実装できます。 カスタム アクター サービスは、`ActorService` (C#) または `FabricActorService` (Java) からすべてのアクター ランタイム機能を継承します。カスタム アクター サービスを使えば、独自のサービス メソッドを実装できます。

```csharp
class MyActorService : ActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, Func<ActorBase> newActor)
        : base(context, typeInfo, newActor)
    { }
}
```
```Java
class MyActorService extends FabricActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, BiFunction<FabricActorService, ActorId, ActorBase> newActor)
    {
         super(context, typeInfo, newActor);
    }
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
```Java
public class Program
{
    public static void main(String[] args)
    {
        ActorRuntime.registerActorAsync(
                MyActor.class,
                (context, actorTypeInfo) -> new FabricActorService(context, actorTypeInfo),
                timeout);
        Thread.sleep(Long.MAX_VALUE);
    }
}
```

#### <a name="implementing-actor-backup-and-restore"></a>アクターのバックアップと復元の実装
 次の例のカスタム アクター サービスは、既に `ActorService` に存在するリモート処理リスナーを活用して、アクター データをバックアップするメソッドを公開しています。

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
```Java
public interface MyActorService extends Service
{
    CompletableFuture<?> backupActorsAsync();
}

class MyActorServiceImpl extends ActorService implements MyActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, Func<FabricActorService, ActorId, ActorBase> newActor)
    {
       super(context, typeInfo, newActor);
    }

    public CompletableFuture backupActorsAsync()
    {
        return this.backupAsync(new BackupDescription((backupInfo, cancellationToken) -> performBackupAsync(backupInfo, cancellationToken)));
    }

    private CompletableFuture<Boolean> performBackupAsync(BackupInfo backupInfo, CancellationToken cancellationToken)
    {
        try
        {
           // store the contents of backupInfo.Directory
           return true;
        }
        finally
        {
           deleteDirectory(backupInfo.Directory)
        }
    }

    void deleteDirectory(File file) {
        File[] contents = file.listFiles();
        if (contents != null) {
            for (File f : contents) {
               deleteDirectory(f);
             }
        }
        file.delete();
    }
}
```


この例では、`IMyActorService` は `IService` (C#) および `Service` (Java) を実装し、`MyActorService` によって実装されるリモート処理コントラクトです。 このリモート処理コントラクトを追加すれば、`IMyActorService` のメソッドは、`ActorServiceProxy` を介してリモート処理プロキシを作成することにより、クライアントから利用できるようになります。

```csharp
IMyActorService myActorServiceProxy = ActorServiceProxy.Create<IMyActorService>(
    new Uri("fabric:/MyApp/MyService"), ActorId.CreateRandom());

await myActorServiceProxy.BackupActorsAsync();
```
```Java
MyActorService myActorServiceProxy = ActorServiceProxy.create(MyActorService.class,
    new URI("fabric:/MyApp/MyService"), actorId);

myActorServiceProxy.backupActorsAsync();
```

## <a name="application-model"></a>アプリケーション モデル
アクター サービスは Reliable Services であるため、アプリケーション モデルは同じです。 ただし、アクター フレームワーク ビルド ツールによって、一部のアプリケーション モデル ファイルが自動的に生成されます。

### <a name="service-manifest"></a>サービス マニフェスト
アクター フレームワーク ビルド ツールは、アクター サービスの ServiceManifest.xml ファイルの内容を自動的に生成します。 このファイルの内容は次のとおりです。

* アクター サービスの種類。 種類の名前は、アクターのプロジェクト名に基づいて生成されます。 アクターの永続化属性に応じて、HasPersistedState フラグも設定されます。
* コード パッケージ。
* 構成パッケージ。
* リソースとエンドポイント。

### <a name="application-manifest"></a>アプリケーション マニフェスト
アクター フレームワーク ビルド ツールによって、アクター サービスの既定のサービス定義が自動的に作成されます。 ビルド ツールによって既定のサービス プロパティが設定されます。

* レプリカ セットの数は、アクターの永続化属性によって決まります。 アクターの永続化属性が変更されるたびに、それに応じて既定のサービス定義のレプリカ セットの数がリセットされます。
* パーティション構成と範囲は Uniform Int64 とフル Int64 キー範囲に設定されます。

## <a name="service-fabric-partition-concepts-for-actors"></a>アクターの Service Fabric のパーティションの概念
アクター サービスはパーティション分割されたステートフル サービスです。 アクター サービスの各パーティションには、アクターのセットが含まれています。 サービス パーティションは、Service Fabric の複数のノードに自動的に分散されます。 その結果、アクター インスタンスが分散されます。

![Actor partitioning and distribution][5]

Reliable Services は、さまざまなパーティション構成とパーティション キー範囲で作成できます。 アクター サービスは、Int64 パーティション構成とフル Int64 キー範囲でアクターをパーティションにマップします。

### <a name="actor-id"></a>アクター ID
サービスで作成される各アクターには、 `ActorId` クラスで表される一意の ID が関連付けられます。 `ActorId` は、ランダム ID を生成することにより、サービス パーティション間での均一なアクターの分散に使用できるあいまいな ID 値です。

```csharp
ActorProxy.Create<IMyActor>(ActorId.CreateRandom());
```
```Java
ActorProxyBase.create<MyActor>(MyActor.class, ActorId.newId());
```


`ActorId` はいずれも Int64 にハッシュされます。 アクター サービスで Int64 パーティション構成とフル Int64 キー範囲を使用する必要があるのはこのためです。 ただし、`ActorID` には、GUID/UUID、文字列、Int64 などのカスタム ID 値を使用できます。

```csharp
ActorProxy.Create<IMyActor>(new ActorId(Guid.NewGuid()));
ActorProxy.Create<IMyActor>(new ActorId("myActorId"));
ActorProxy.Create<IMyActor>(new ActorId(1234));
```
```Java
ActorProxyBase.create(MyActor.class, new ActorId(UUID.randomUUID()));
ActorProxyBase.create(MyActor.class, new ActorId("myActorId"));
ActorProxyBase.create(MyActor.class, new ActorId(1234));
```

GUID/UUID と文字列を使用している場合、値は Int64 にハッシュされます。 ただし、Int64 を `ActorId`に明示的に指定している場合、Int64 はハッシュされることなくパーティションに直接マップされます。 この手法を使用して、どのパーティションにアクターが配置されるかを制御できます。

## <a name="actor-using-remoting-v2-stack"></a>リモート処理 V2 スタックを使用したアクター
2.8 Nuget パッケージによって、ユーザーは、カスタムのシリアル化などの機能を提供する高性能なリモート処理 V2 スタックを使用できるようになりました。 リモート処理 V2 には、既存のリモート処理スタック (これからは V1 リモート処理スタックと呼びます) との下位互換性がありません。

リモート処理 V2 スタックを使用するには、次の変更が必要です。
 1. アクター インターフェイスで次の Assembly 属性を追加します。
   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListener = RemotingListener.V2Listener,RemotingClient = RemotingClient.V2Client)]
   ```

 2. ActorService とアクター クライアント プロジェクトをビルドしてアップグレードし、V2 スタックの使用を開始します。

### <a name="actor-service-upgrade-to-remoting-v2-stack-without-impacting-service-availability"></a>サービスの可用性に影響を与えずにアクター サービスをリモート処理 V2 スタックにアップグレードする
この変更は、2 段階に分かれたアップグレードです。 次に示す順序で手順に従います。

1.  アクター インターフェイスで次の Assembly 属性を追加します。 この属性は、ActorService 用の 2 つのリスナーである、V1 (既存) および V2 リスナーを開始します。 この変更で ActorService をアップグレードします。

  ```csharp
  [assembly:FabricTransportActorRemotingProvider(RemotingListener = RemotingListener.CompatListener,RemotingClient = RemotingClient.V2Client)]
  ```

2. 上記アップグレードの完了後、ActorClients をアップグレードします。
この手順により、アクター プロキシは確実にリモート処理 V2 スタックを使用します。

3. この手順は省略可能です。 上記の属性を変更して V1 リスナーを削除します。

    ```csharp
    [assembly:FabricTransportActorRemotingProvider(RemotingListener = RemotingListener.V2Listener,RemotingClient = RemotingClient.V2Client)]
    ```

## <a name="next-steps"></a>次のステップ
* [アクターの状態管理](service-fabric-reliable-actors-state-management.md)
* [アクターのライフサイクルとガベージ コレクション](service-fabric-reliable-actors-lifecycle.md)
* [アクターの API リファレンス ドキュメント](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [.NET サンプル コード](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java サンプル コード](http://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png
