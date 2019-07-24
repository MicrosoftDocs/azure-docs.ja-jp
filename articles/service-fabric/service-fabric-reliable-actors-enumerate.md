---
title: Azure Service Fabric でのアクターの列挙 | Microsoft Docs
description: Reliable Actors とそのメタデータを列挙する方法を説明します。
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: amanbha
ms.assetid: 45839a7f-0536-46f1-ae2b-8ba3556407fb
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/19/2018
ms.author: vturecek
ms.openlocfilehash: 04e2c32b18e6897d6443fea68587aba9ae294be5
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58664495"
---
# <a name="enumerate-service-fabric-reliable-actors"></a>Service Fabric Reliable Actors を列挙する
Reliable Actors サービスでは、クライアントは、サービスがホストしているアクターに関するメタデータを列挙できます。 アクター サービスはパーティション分割されたステートフル サービスであるため、列挙はパーティションごとに実行されます。 各パーティションには多数のアクターが含まれる可能性があるため、列挙はページングされた結果のセットとして返されます。 ページはすべてのページが読み取られるまでループされます。 次の例は、アクター サービスの 1 つのパーティションに含まれるすべてのアクティブ アクターのリストを作成する方法を示しています。

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



## <a name="next-steps"></a>次の手順
* [アクターの状態管理](service-fabric-reliable-actors-state-management.md)
* [アクターのライフサイクルとガベージ コレクション](service-fabric-reliable-actors-lifecycle.md)
* [アクターの API リファレンス ドキュメント](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [.NET サンプル コード](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java サンプル コード](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png
