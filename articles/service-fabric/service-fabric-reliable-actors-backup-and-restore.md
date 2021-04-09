---
title: Azure Service Fabric アクターのバックアップと復元
description: ご利用の Azure Service Fabric アクターでバックアップと復元を実装する方法を説明します。
ms.topic: conceptual
ms.date: 10/29/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: 05dfb7d09e68373f52831a9e5233316df95ee552
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96571336"
---
# <a name="implement-reliable-actors-backup-and-restore"></a>Reliable Actors のバックアップと復元を実装する

> [!NOTE]
> Reliable Stateful サービスと Reliable Actors のデータのバックアップを構成するには、[定期的なバックアップと復元](service-fabric-backuprestoreservice-quickstart-azurecluster.md)を使用することをお勧めします。 
> 

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

Reliable Actors について詳しくは、以下の記事をご覧ください。
* [アクターの状態管理](service-fabric-reliable-actors-state-management.md)
* [アクターのライフサイクルとガベージ コレクション](service-fabric-reliable-actors-lifecycle.md)
* [アクターの API リファレンス ドキュメント](/previous-versions/azure/dn971626(v=azure.100))
* [.NET サンプル コード](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java サンプル コード](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png
