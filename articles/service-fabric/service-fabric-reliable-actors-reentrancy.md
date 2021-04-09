---
title: Azure Service Fabric アクターでの再入
description: 呼び出しコンテキストに基づき中断を論理的に回避する方法である、Service Fabric Reliable Actors の再入の概要です。
ms.topic: conceptual
ms.date: 11/02/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 90a8e9146d18d628fbf124307f1b79b912c9cef9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98789382"
---
# <a name="reliable-actors-reentrancy"></a>Reliable Actors の再入
Reliable Actors ランタイムは、論理呼び出しコンテキスト ベースの再入を既定で許可します。 これにより、アクターは、同じ呼び出しコンテキストのチェーンにある場合は再入可能になります。 たとえば、アクター A がアクター C にメッセージを送信するアクター B にメッセージを送信するとします。メッセージ処理の一環として、アクター C がアクター A を呼び出した場合、メッセージは再入可能であるため、再入が許可されます。 処理が完了するまで、アクター A では別の呼び出しコンテキストの一部である他のメッセージがブロックされます。

`ActorReentrancyMode` 列挙型で定義されているアクターの再入に使用できるオプションには、次の 2 つがあります。

* `LogicalCallContext` (既定の動作)
* `Disallowed` - 再入を無効にします

```csharp
public enum ActorReentrancyMode
{
    LogicalCallContext = 1,
    Disallowed = 2
}
```
```Java
public enum ActorReentrancyMode
{
    LogicalCallContext(1),
    Disallowed(2)
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
```Java
static class Program
{
    static void Main()
    {
        try
        {
            ActorConcurrencySettings actorConcurrencySettings = new ActorConcurrencySettings();
            actorConcurrencySettings.setReentrancyMode(ActorReentrancyMode.Disallowed);

            ActorServiceSettings actorServiceSettings = new ActorServiceSettings();
            actorServiceSettings.setActorConcurrencySettings(actorConcurrencySettings);

            ActorRuntime.registerActorAsync(
                Actor1.getClass(),
                (context, actorType) -> new FabricActorService(
                    context,
                    actorType, () -> new Actor1(),
                    null,
                    stateProvider,
                    actorServiceSettings, timeout);

            Thread.sleep(Long.MAX_VALUE);
        }
        catch (Exception e)
        {
            throw e;
        }
    }
}
```


## <a name="next-steps"></a>次のステップ
* [Actor API リファレンス ドキュメント](/dotnet/api/microsoft.servicefabric.actors)で再入の詳細を確認する
