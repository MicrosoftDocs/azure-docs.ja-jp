---
title: Reliable Actors のアクター型のシリアル化に関する留意事項 | Microsoft Docs
description: Service Fabric Reliable Actors の状態とインターフェイスを定義する場合に使用できるシリアル化可能なクラスを定義するための基本的な要件について説明します。
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 6e50e4dc-969a-4a1c-b36c-b292d964c7e3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: c8eeeb0ade6ca002adf3211cbf49127be9b76edb
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58667517"
---
# <a name="notes-on-service-fabric-reliable-actors-type-serialization"></a>Service Fabric Reliable Actors 型のシリアル化に関する留意事項
すべてのメソッドの引数、アクター インターフェイスの各メソッドによって返されるタスクの結果の型、アクターの状態マネージャーに保存されるオブジェクトは、[データ コントラクト シリアル化可能](/dotnet/framework/wcf/feature-details/types-supported-by-the-data-contract-serializer)である必要があります。 これは、[アクター イベント インターフェイス](service-fabric-reliable-actors-events.md)で定義されているメソッドの引数にも当てはまります。 (アクター イベント インターフェイス メソッドは常に void を返します)。

## <a name="custom-data-types"></a>カスタム データ型
この例では、次のアクター インターフェイスで、`VoicemailBox` というカスタム データ型を返すメソッドを定義しています。

```csharp
public interface IVoiceMailBoxActor : IActor
{
    Task<VoicemailBox> GetMailBoxAsync();
}
```

```Java
public interface VoiceMailBoxActor extends Actor
{
    CompletableFuture<VoicemailBox> getMailBoxAsync();
}
```

このインターフェイスは、状態マネージャーを使用して `VoicemailBox` オブジェクトを保存するアクターによって実装されます。

```csharp
[StatePersistence(StatePersistence.Persisted)]
public class VoiceMailBoxActor : Actor, IVoicemailBoxActor
{
    public VoiceMailBoxActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<VoicemailBox> GetMailboxAsync()
    {
        return this.StateManager.GetStateAsync<VoicemailBox>("Mailbox");
    }
}

```

```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
public class VoiceMailBoxActorImpl extends FabricActor implements VoicemailBoxActor
{
    public VoiceMailBoxActorImpl(ActorService actorService, ActorId actorId)
    {
         super(actorService, actorId);
    }

    public CompletableFuture<VoicemailBox> getMailBoxAsync()
    {
         return this.stateManager().getStateAsync("Mailbox");
    }
}

```

この例では、 `VoicemailBox` オブジェクトは次の状況でシリアル化されます。

* オブジェクトがアクター インスタンスと呼び出し元の間で送信されるとき。
* オブジェクトが状態マネージャーに保存されるとき (状態マネージャーでオブジェクトがディスクに保存され、他のノードにレプリケートされます)。

Reliable Actors フレームワークでは、DataContract シリアル化を使用します。 そのため、カスタム データ オブジェクトとそのメンバーに、それぞれ **DataContract** 属性と **DataMember** 属性を注釈として付ける必要があります。

```csharp
[DataContract]
public class Voicemail
{
    [DataMember]
    public Guid Id { get; set; }

    [DataMember]
    public string Message { get; set; }

    [DataMember]
    public DateTime ReceivedAt { get; set; }
}
```
```Java
public class Voicemail implements Serializable
{
    private static final long serialVersionUID = 42L;

    private UUID id;                    //getUUID() and setUUID()

    private String message;             //getMessage() and setMessage()

    private GregorianCalendar receivedAt; //getReceivedAt() and setReceivedAt()
}
```


```csharp
[DataContract]
public class VoicemailBox
{
    public VoicemailBox()
    {
        this.MessageList = new List<Voicemail>();
    }

    [DataMember]
    public List<Voicemail> MessageList { get; set; }

    [DataMember]
    public string Greeting { get; set; }
}
```
```Java
public class VoicemailBox implements Serializable
{
    static final long serialVersionUID = 42L;
    
    public VoicemailBox()
    {
        this.messageList = new ArrayList<Voicemail>();
    }

    private List<Voicemail> messageList;   //getMessageList() and setMessageList()

    private String greeting;               //getGreeting() and setGreeting()
}
```


## <a name="next-steps"></a>次の手順
* [アクターのライフサイクルとガベージ コレクション](service-fabric-reliable-actors-lifecycle.md)
* [アクターのタイマーとアラーム](service-fabric-reliable-actors-timers-reminders.md)
* [アクター イベント](service-fabric-reliable-actors-events.md)
* [アクターの再入](service-fabric-reliable-actors-reentrancy.md)
* [アクターのポリモーフィズムとオブジェクト指向設計パターン](service-fabric-reliable-actors-polymorphism.md)
* [アクターの診断とパフォーマンスの監視](service-fabric-reliable-actors-diagnostics.md)
