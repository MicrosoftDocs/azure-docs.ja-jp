---
title: Reliable Actors のタイマーとアラーム
description: Service Fabric Reliable Actors のタイマーとアラームについて、それぞれをいつ使用するかに関するガイダンスを含む概要です。
ms.topic: conceptual
ms.date: 11/02/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: f77eb29c9146fe66d5d2b6073c33e30fbab649c2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791796"
---
# <a name="actor-timers-and-reminders"></a>アクターのタイマーとアラーム
アクターは、タイマーまたはアラームを登録することで、自身の定期的な作業をスケジュールできます。 ここでは、タイマーとアラームの使用方法と、これらの違いについて説明します。

## <a name="actor-timers"></a>アクターのタイマー
アクターのタイマーは、アクターのランタイムが提供するターンごとのコンカレンシーの保証を、コールバック メソッドが考慮するように、.NET タイマーまたは Java タイマーの単純なラッパーを提供します。

アクターは、その基本クラスに `RegisterTimer`(C#) または `registerTimer`(Java) および `UnregisterTimer`(C#) または `unregisterTimer`(Java) のメソッドを使用して、タイマーを登録/登録解除できます。 次の例はタイマー API の使用を示します。 API は、.NET タイマーまたは Java タイマーによく似ています。 この例では、タイマーが期限に達すると、アクターのランタイムが `MoveObject`(C#) または `moveObject`(Java) メソッドを呼び出します。 このメソッドは、ターンごとのコンカレンシーを優先することを保証します。 つまり、このコールバックの実行が完了するまで、他のアクター メソッドやタイマーとアラームのコールバックは進行しません。

```csharp
class VisualObjectActor : Actor, IVisualObject
{
    private IActorTimer _updateTimer;

    public VisualObjectActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    protected override Task OnActivateAsync()
    {
        ...

        _updateTimer = RegisterTimer(
            MoveObject,                     // Callback method
            null,                           // Parameter to pass to the callback method
            TimeSpan.FromMilliseconds(15),  // Amount of time to delay before the callback is invoked
            TimeSpan.FromMilliseconds(15)); // Time interval between invocations of the callback method

        return base.OnActivateAsync();
    }

    protected override Task OnDeactivateAsync()
    {
        if (_updateTimer != null)
        {
            UnregisterTimer(_updateTimer);
        }

        return base.OnDeactivateAsync();
    }

    private Task MoveObject(object state)
    {
        ...
        return Task.FromResult(true);
    }
}
```
```Java
public class VisualObjectActorImpl extends FabricActor implements VisualObjectActor
{
    private ActorTimer updateTimer;

    public VisualObjectActorImpl(FabricActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    @Override
    protected CompletableFuture onActivateAsync()
    {
        ...

        return this.stateManager()
                .getOrAddStateAsync(
                        stateName,
                        VisualObject.createRandom(
                                this.getId().toString(),
                                new Random(this.getId().toString().hashCode())))
                .thenApply((r) -> {
                    this.registerTimer(
                            (o) -> this.moveObject(o),                        // Callback method
                            "moveObject",
                            null,                                             // Parameter to pass to the callback method
                            Duration.ofMillis(10),                            // Amount of time to delay before the callback is invoked
                            Duration.ofMillis(timerIntervalInMilliSeconds));  // Time interval between invocations of the callback method
                    return null;
                });
    }

    @Override
    protected CompletableFuture onDeactivateAsync()
    {
        if (updateTimer != null)
        {
            unregisterTimer(updateTimer);
        }

        return super.onDeactivateAsync();
    }

    private CompletableFuture moveObject(Object state)
    {
        ...
        return this.stateManager().getStateAsync(this.stateName).thenCompose(v -> {
            VisualObject v1 = (VisualObject)v;
            v1.move();
            return (CompletableFuture<?>)this.stateManager().setStateAsync(stateName, v1).
                    thenApply(r -> {
                      ...
                      return null;});
        });
    }
}
```

タイマーの次の期間は、コールバックが実行を完了した後に開始します。 これは、コールバックを実行している間はタイマーが停止し、コールバックが完了したときにタイマーが開始することを意味します。

コールバックが完了すると、アクターのランタイムは、アクターの状態マネージャーに加えられた変更を保存します。 状態の保存中にエラーが発生した場合、そのアクターのオブジェクトは非アクティブ化し、新しいインスタンスがアクティブ化されます。

[リマインダー](#actor-reminders)とは異なり、タイマーを更新することはできません。 `RegisterTimer` が再度呼び出されると、新しいタイマーが登録されます。

ガベージ コレクションの一部としてアクターが非アクティブ化されると、すべてのタイマーが停止します。 その後は、タイマーのコールバックは呼び出されません。 また、アクターのランタイムは、非アクティブ化の前に実行されていたタイマーについての情報は保持しません。 その後、再アクティブ化したときに必要なタイマーを登録するかどうかはアクターによって異なります。 詳細については、「 [アクターのガベージ コレクション](service-fabric-reliable-actors-lifecycle.md)」のセクションを参照してください。

## <a name="actor-reminders"></a>アクターのアラーム
アラームは、指定した時間にアクターに永続的なコールバックをトリガーするメカニズムです。 タイマーと似ていますが、アラームの場合は、アクターが明示的にアラームの登録を解除するか、アクターが明示的に削除されるまで、あらゆる状況下でトリガーされます。 具体的には、アラームはアクターの無効化およびフェールオーバーによりトリガーされます。 これは、アクターのランタイムが、アクター状態プロバイダーを使用してアクターのアラームに関する情報を永続化するためです。 また、タイマーとは異なり、既存のアラームを更新するには、同じ *reminderName* を使用して登録メソッド (`RegisterReminderAsync`) を再度呼び出します。

> [!NOTE]
> アラートの信頼性は、アクター状態プロバイダーが提供する状態の信頼性の保証に関連付けられています。 つまり、状態の永続性が "*なし*" に設定されているアクターの場合、フェールオーバー後にアラートが起動しません。

アラームを登録するには、次の例に示すように、アクターが基本クラスで提供される [`RegisterReminderAsync`](/dotnet/api/microsoft.servicefabric.actors.runtime.actorbase.registerreminderasync#remarks) メソッドを呼び出します。

```csharp
protected override async Task OnActivateAsync()
{
    string reminderName = "Pay cell phone bill";
    int amountInDollars = 100;

    IActorReminder reminderRegistration = await this.RegisterReminderAsync(
        reminderName,
        BitConverter.GetBytes(amountInDollars),
        TimeSpan.FromDays(3),    //The amount of time to delay before firing the reminder
        TimeSpan.FromDays(1));    //The time interval between firing of reminders
}
```

```Java
@Override
protected CompletableFuture onActivateAsync()
{
    String reminderName = "Pay cell phone bill";
    int amountInDollars = 100;

    ActorReminder reminderRegistration = this.registerReminderAsync(
            reminderName,
            state,
            dueTime,    //The amount of time to delay before firing the reminder
            period);    //The time interval between firing of reminders
}
```

上記の例で、 `"Pay cell phone bill"` はアラーム名です。 これは、アクターがアラームを一意に識別するために使用する文字列です。 `BitConverter.GetBytes(amountInDollars)`(C#) は、そのアラームに関連付けられているコンテキストです。 これは、アラームのコールバックの引数、つまり `IRemindable.ReceiveReminderAsync`(C#) または `Remindable.receiveReminderAsync`(Java) としてアクターに戻ります。

アラームを使用するアクターは、次の例に示すように `IRemindable` インターフェイスを実装する必要があります。

```csharp
public class ToDoListActor : Actor, IToDoListActor, IRemindable
{
    public ToDoListActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task ReceiveReminderAsync(string reminderName, byte[] context, TimeSpan dueTime, TimeSpan period)
    {
        if (reminderName.Equals("Pay cell phone bill"))
        {
            int amountToPay = BitConverter.ToInt32(context, 0);
            System.Console.WriteLine("Please pay your cell phone bill of ${0}!", amountToPay);
        }
        return Task.FromResult(true);
    }
}
```
```Java
public class ToDoListActorImpl extends FabricActor implements ToDoListActor, Remindable
{
    public ToDoListActor(FabricActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture receiveReminderAsync(String reminderName, byte[] context, Duration dueTime, Duration period)
    {
        if (reminderName.equals("Pay cell phone bill"))
        {
            int amountToPay = ByteBuffer.wrap(context).getInt();
            System.out.println("Please pay your cell phone bill of " + amountToPay);
        }
        return CompletableFuture.completedFuture(true);
    }

```

アラームがトリガーされると、Reliable Actors のランタイムがアクターの `ReceiveReminderAsync`(C#) メソッドまたは `receiveReminderAsync`(Java) メソッドを呼び出します。 アクターは複数のアラームを登録でき、そのいずれかのアラームがトリガーされると、`ReceiveReminderAsync`(C#) メソッドまたは `receiveReminderAsync`(Java) メソッドが呼び出されます。 アクターは `ReceiveReminderAsync`(C#) メソッドまたは `receiveReminderAsync`(Java) メソッドに渡されるアラーム名を使用して、どのアラームがトリガーされたかを判別します。

アクターのランタイムは、`ReceiveReminderAsync`(C#) または `receiveReminderAsync`(Java) の呼び出しが完了すると、アクターの状態を保存します。 状態の保存中にエラーが発生した場合、そのアクターのオブジェクトは非アクティブ化し、新しいインスタンスがアクティブ化されます。

アラームの登録を解除するには、次の例に示すように、アクターが `UnregisterReminderAsync`(C#) メソッドまたは `unregisterReminderAsync`(Java) メソッドを呼び出します。

```csharp
IActorReminder reminder = GetReminder("Pay cell phone bill");
Task reminderUnregistration = UnregisterReminderAsync(reminder);
```
```Java
ActorReminder reminder = getReminder("Pay cell phone bill");
CompletableFuture reminderUnregistration = unregisterReminderAsync(reminder);
```

上記の例のように、`UnregisterReminderAsync`(C#) メソッドまたは `unregisterReminderAsync`(Java) メソッドは、`IActorReminder`(C#) インターフェースまたは `ActorReminder`(Java) インターフェイスを受け入れます。 アクターの基本クラスは、アラーム名で渡すことで、`IActorReminder`(C#) インターフェイスまたは `ActorReminder`(Java) インターフェイスを取得できる `GetReminder`(C#) メソッドまたは `getReminder`(Java) メソッドをサポートします。 これにより、アクターが `RegisterReminder`(C#) メソッドまたは `registerReminder`(Java) メソッドの呼び出しから返された `IActorReminder`(C#) インターフェイスまたは `ActorReminder`(Java) インターフェイスを永続化する必要がなくなるため便利です。

## <a name="next-steps"></a>次の手順
Reliable Actor のイベントと再入について学習します。
* [アクター イベント](service-fabric-reliable-actors-events.md)
* [アクターの再入](service-fabric-reliable-actors-reentrancy.md)
