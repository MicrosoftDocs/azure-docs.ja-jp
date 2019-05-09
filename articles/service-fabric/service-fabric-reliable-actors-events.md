---
title: アクター ベースの Azure Service Fabric アクターでのイベント | Microsoft Docs
description: Service Fabric Reliable Actors のイベントの概要
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: aa01b0f7-8f88-403a-bfe1-5aba00312c24
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/06/2017
ms.author: amanbha
ms.openlocfilehash: 9075fc8391e8afa21e3963c1eff6a630c586d647
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58659390"
---
# <a name="actor-events"></a>アクター イベント
アクター イベントは、アクターからクライアントにベスト エフォート通知を送信する方法を提供します。 アクター イベントは、アクターとクライアントの通信用に設計されており、アクター間の通信には使用できません。

次のコード スニペットは、アプリケーションでアクター イベントを使用する方法を示しています。

アクターによって発行されたイベントを表すインターフェイスを定義します。 このインターフェイスは、 `IActorEvents` インターフェイスから派生する必要があります。 メソッドの引数は、 [データ コントラクト シリアル化可能](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)である必要があります。 イベント通知が一方向かつベスト エフォートであるため、メソッドは void を返す必要があります。

```csharp
public interface IGameEvents : IActorEvents
{
    void GameScoreUpdated(Guid gameId, string currentScore);
}
```
```Java
public interface GameEvents implements ActorEvents
{
    void gameScoreUpdated(UUID gameId, String currentScore);
}
```
アクター インターフェイスで、アクターによって発行されたイベントを宣言します。

```csharp
public interface IGameActor : IActor, IActorEventPublisher<IGameEvents>
{
    Task UpdateGameStatus(GameStatus status);

    Task<string> GetGameScore();
}
```
```Java
public interface GameActor extends Actor, ActorEventPublisherE<GameEvents>
{
    CompletableFuture<?> updateGameStatus(GameStatus status);

    CompletableFuture<String> getGameScore();
}
```
クライアント側で、イベント ハンドラーを実装します。

```csharp
class GameEventsHandler : IGameEvents
{
    public void GameScoreUpdated(Guid gameId, string currentScore)
    {
        Console.WriteLine(@"Updates: Game: {0}, Score: {1}", gameId, currentScore);
    }
}
```

```Java
class GameEventsHandler implements GameEvents {
    public void gameScoreUpdated(UUID gameId, String currentScore)
    {
        System.out.println("Updates: Game: "+gameId+" ,Score: "+currentScore);
    }
}
```

クライアント側で、イベントを発行するアクターに対してプロキシを作成し、そのイベントをサブスクライブします。

```csharp
var proxy = ActorProxy.Create<IGameActor>(
                    new ActorId(Guid.Parse(arg)), ApplicationName);

await proxy.SubscribeAsync<IGameEvents>(new GameEventsHandler());
```

```Java
GameActor actorProxy = ActorProxyBase.create<GameActor>(GameActor.class, new ActorId(UUID.fromString(args)));

return ActorProxyEventUtility.subscribeAsync(actorProxy, new GameEventsHandler());
```

フェールオーバーが発生した場合、アクターは別のプロセスまたはノードにフェールオーバーする可能性があります。 アクター プロキシは、アクティブなサブスクリプションを管理し、自動的に再サブスクライブします。 `ActorProxyEventExtensions.SubscribeAsync<TEvent>` API を介して、再サブスクリプションの間隔を制御できます。 サブスクリプションを解除するには、 `ActorProxyEventExtensions.UnsubscribeAsync<TEvent>` API を使用します。

アクターで、イベントが発生したときに発行します。 イベントをサブスクライブしているユーザーがいる場合、アクター ランタイムはこれらのユーザーに通知を送信します。

```csharp
var ev = GetEvent<IGameEvents>();
ev.GameScoreUpdated(Id.GetGuidId(), score);
```
```Java
GameEvents event = getEvent<GameEvents>(GameEvents.class);
event.gameScoreUpdated(Id.getUUIDId(), score);
```


## <a name="next-steps"></a>次の手順
* [アクターの再入](service-fabric-reliable-actors-reentrancy.md)
* [アクターの診断とパフォーマンスの監視](service-fabric-reliable-actors-diagnostics.md)
* [Actor API リファレンス ドキュメント](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [C# コード サンプル](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [C# .NET Core コード サンプル](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started)
* [Java コード サンプル](https://github.com/Azure-Samples/service-fabric-java-getting-started)
