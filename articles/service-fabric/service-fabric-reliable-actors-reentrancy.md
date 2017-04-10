---
title: "アクター ベースの Azure マイクロサービスでの再入 | Microsoft Docs"
description: "Service Fabric Reliable Actors の再入の概要"
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: amanbha
ms.assetid: be23464a-0eea-4eca-ae5a-2e1b650d365e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/10/2017
ms.author: vturecek
translationtype: Human Translation
ms.sourcegitcommit: 7033955fa9c18b2fa1a28d488ad5268d598de287
ms.openlocfilehash: 7f6ac80472d3e4fab390b9aa69105e19dca7b947


---
# <a name="reliable-actors-reentrancy"></a>Reliable Actors の再入
Reliable Actors ランタイムは、論理呼び出しコンテキスト ベースの再入を既定で許可します。 これにより、アクターは、同じ呼び出しコンテキストのチェーンにある場合は再入可能になります。 たとえば、アクター A がアクター C にメッセージを送信するアクター B にメッセージを送信するとします。メッセージ処理の一環として、アクター C がアクター A を呼び出した場合、メッセージは再入可能であるため、再入が許可されます。 処理が完了するまで、アクター A では別の呼び出しコンテキストの一部である他のメッセージがブロックされます。

`ActorReentrancyMode` 列挙型で定義されているアクターの再入に使用できるオプションには、次の&2; つがあります。

* `LogicalCallContext` (既定の動作)
* `Disallowed` - 再入を無効にします

```csharp
public enum ActorReentrancyMode
{
    LogicalCallContext = 1,
    Disallowed = 2
}
```

再入は、登録時に `ActorService`の設定で構成できます。 この設定は、アクター サービスで作成されたすべてのアクター インスタンスに適用されます。

次の例では、再入モードを `ActorReentrancyMode.Disallowed`に設定したアクター サービスを示しています。 この場合、アクターが別のアクターに再入メッセージを送信すると、 `FabricException` 型の例外がスローされます。

```csharp
static class Program
{
    static void Main()
    {
        try
        {
            ActorRuntime.RegisterActorAsync<Actor1>(
                (context, actorType) => new ActorService(
                    context, 
                    actorType, () => new Actor1(), 
                    settings: new ActorServiceSettings()
                    {
                        ActorConcurrencySettings = new ActorConcurrencySettings()
                        {
                            ReentrancyMode = ActorReentrancyMode.Disallowed
                        }
                    }))
                .GetAwaiter().GetResult();

            Thread.Sleep(Timeout.Infinite);
        }
        catch (Exception e)
        {
            ActorEventSource.Current.ActorHostInitializationFailed(e.ToString());
            throw;
        }
    }
}
```

## <a name="next-steps"></a>次のステップ
* [アクターの診断とパフォーマンスの監視](service-fabric-reliable-actors-diagnostics.md)
* [Actor API リファレンス ドキュメント](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [コード サンプル](https://github.com/Azure/servicefabric-samples)




<!--HONumber=Jan17_HO4-->


