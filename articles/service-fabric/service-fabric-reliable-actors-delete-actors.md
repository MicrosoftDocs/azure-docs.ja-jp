---
title: Azure Service Fabric アクターの削除 | Microsoft Docs
description: Service Fabric Reliable Actors とその状態を手動で削除する方法を説明します。
services: service-fabric
documentationcenter: .net
author: amanbha
manager: chackdan
editor: vturecek
ms.assetid: b91384cc-804c-49d6-a6cb-f3f3d7d65a8e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/19/2018
ms.author: amanbha
ms.openlocfilehash: e297a6f42774f29e2eca4a410b695d5bbb636300
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58666972"
---
# <a name="delete-reliable-actors-and-their-state"></a>Reliable Actors とその状態を削除する
非アクティブ化されたアクターのガベージ コレクションではアクター オブジェクトのみがクリーンアップされ、アクターの状態マネージャーに格納されているデータは削除されません。 アクターが再アクティブ化されると、そのデータは状態マネージャーを介して再び利用可能になります。 状態マネージャーにデータを格納したアクターが非アクティブ化され、その後も再アクティブ化されなかった場合は、そのデータをクリーンアップする必要がある場合があります。

[アクター サービス](service-fabric-reliable-actors-platform.md) は、リモートの呼び出し元からアクターを削除する機能を提供します。

```csharp
ActorId actorToDelete = new ActorId(id);

IActorService myActorServiceProxy = ActorServiceProxy.Create(
    new Uri("fabric:/MyApp/MyService"), actorToDelete);

await myActorServiceProxy.DeleteActorAsync(actorToDelete, cancellationToken)
```
```Java
ActorId actorToDelete = new ActorId(id);

ActorService myActorServiceProxy = ActorServiceProxy.create(
    new Uri("fabric:/MyApp/MyService"), actorToDelete);

myActorServiceProxy.deleteActorAsync(actorToDelete);
```

アクターを削除すると、アクターが現在アクティブかどうかに応じて、次のような結果になります。

* **アクティブ アクター**
  * アクターはアクティブ アクターの一覧から削除され、非アクティブ化されます。
  * 状態は完全に削除されます。
* **非アクティブなアクター**
  * 状態は完全に削除されます。

アクターが自身のアクター メソッドのいずれかから自身に対する削除を呼び出すことはできません。これは、アクター呼び出しのコンテキスト内で実行中のアクターを削除することはできないためです。このとき、ランタイムはアクターの呼び出しに関するロックを取得して、シングル スレッド アクセスを適用します。

Reliable Actors の詳細については、以下を参照してください。
* [アクターのタイマーとアラーム](service-fabric-reliable-actors-timers-reminders.md)
* [アクター イベント](service-fabric-reliable-actors-events.md)
* [アクターの再入](service-fabric-reliable-actors-reentrancy.md)
* [アクターの診断とパフォーマンスの監視](service-fabric-reliable-actors-diagnostics.md)
* [Actor API リファレンス ドキュメント](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [C# コード サンプル](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java コード サンプル](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-lifecycle/garbage-collection.png
