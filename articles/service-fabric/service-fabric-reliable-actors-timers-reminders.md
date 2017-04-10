---
title: "Reliable Actors のタイマーとアラーム | Microsoft Docs"
description: "Service Fabric Reliable Actors のタイマーとアラームの概要"
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: amanbha
ms.assetid: 00c48716-569e-4a64-bd6c-25234c85ff4f
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/10/2017
ms.author: vturecek
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 56b2e13c3bf053175e357a627d45a91d9a9a4ba9


---
# <a name="actor-timers-and-reminders"></a>アクターのタイマーとアラーム
アクターは、タイマーまたはアラームを登録することで、自身の定期的な作業をスケジュールできます。 ここでは、タイマーとアラームの使用方法と、これらの違いについて説明します。

## <a name="actor-timers"></a>アクターのタイマー
アクターのタイマーは、アクターのランタイムが提供するターンごとの同時実行の保証をコールバック メソッドが考慮するように、.NET タイマーの単純なラッパーを提供します。

アクターは、`RegisterTimer` と `UnregisterTimer` のメソッドをその基本クラスに使用して、タイマーを登録/登録解除できます。 次の例はタイマー API の使用を示します。 API は、.NET タイマーによく似ています。 この例では、タイマーが期限に達すると、アクターのランタイムが `MoveObject` メソッドを呼び出します。 このメソッドは、ターンごとの同時実行を優先することを保証します。 つまり、このコールバックの実行が完了するまで、他のアクター メソッドやタイマーとアラームのコールバックは進行しません。

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

タイマーの次の期間は、コールバックが実行を完了した後に開始します。 これは、コールバックを実行している間はタイマーが停止し、コールバックが完了したときにタイマーが開始することを意味します。

コールバックが完了すると、アクターのランタイムは、アクターの状態マネージャーに加えられた変更を保存します。 状態の保存中にエラーが発生した場合、そのアクターのオブジェクトは非アクティブ化し、新しいインスタンスがアクティブ化されます。 

ガベージ コレクションの一部としてアクターが非アクティブ化されると、すべてのタイマーが停止します。 その後は、タイマーのコールバックは呼び出されません。 また、アクターのランタイムは、非アクティブ化の前に実行されていたタイマーについての情報は保持しません。 その後、再アクティブ化したときに必要なタイマーを登録するかどうかはアクターによって異なります。 詳細については、「 [アクターのガベージ コレクション](service-fabric-reliable-actors-lifecycle.md)」のセクションを参照してください。

## <a name="actor-reminders"></a>アクターのアラーム
アラームは、指定した時間にアクターに永続的なコールバックをトリガーするメカニズムです。 タイマーと似ていますが、アラームの場合は、アクターが明示的にアラームの登録を解除するか、アクターが明示的に削除されるまで、あらゆる状況下でトリガーされます。 具体的には、アラームはアクターの無効化およびフェールオーバーによりトリガーされます。 これは、アクターのランタイムがアクターのアラームに関する情報を永続化するためです。

アラームを登録するには、次の例に示すように、アクターが基本クラスで提供される `RegisterReminderAsync` メソッドを呼び出します。

```csharp
protected override async Task OnActivateAsync()
{
    string reminderName = "Pay cell phone bill";
    int amountInDollars = 100;

    IActorReminder reminderRegistration = await this.RegisterReminderAsync(
        reminderName,
        BitConverter.GetBytes(amountInDollars),
        TimeSpan.FromDays(3),
        TimeSpan.FromDays(1));
}
```

上記の例で、 `"Pay cell phone bill"` はアラーム名です。 これは、アクターがアラームを一意に識別するために使用する文字列です。 `BitConverter.GetBytes(amountInDollars)` は、そのアラームに関連付けられているコンテキストです。 これは、アラームのコールバックの引数、つまり `IRemindable.ReceiveReminderAsync`としてアクターに戻ります。

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

アラームがトリガーされると、Reliable Actors のランタイムがアクターの `ReceiveReminderAsync` メソッドを呼び出します。 アクターは複数のアラームを登録でき、 `ReceiveReminderAsync` メソッドは、そのいずれかのアラームがトリガーされると呼び出されます。 アクターは `ReceiveReminderAsync` メソッドに渡されるアラーム名を使用して、どのアラームがトリガーされたかを判別できます。

アクターのランタイムは、 `ReceiveReminderAsync` の呼び出しが完了するとアクターの状態を保存します。 状態の保存中にエラーが発生した場合、そのアクターのオブジェクトは非アクティブ化し、新しいインスタンスがアクティブ化されます。 

アラームの登録を解除するには、次の例に示すように、アクターが `UnregisterReminderAsync` メソッドを呼び出します。

```csharp
IActorReminder reminder = GetReminder("Pay cell phone bill");
Task reminderUnregistration = UnregisterReminderAsync(reminder);
```

上記の例のように、 `UnregisterReminderAsync` メソッドは、`IActorReminder` インターフェイスを受け入れます。 アクターの基本クラスは、アラーム名で渡すことで、`IActorReminder` インターフェイスを取得できる `GetReminder` メソッドをサポートします。 これにより、アクターが `RegisterReminder` メソッドの呼び出しから返された `IActorReminder` インターフェイスを永続化する必要がなくなるため便利です。

## <a name="next-steps"></a>次のステップ
* [アクター イベント](service-fabric-reliable-actors-events.md)
* [アクターの再入](service-fabric-reliable-actors-reentrancy.md)
* [アクターの診断とパフォーマンスの監視](service-fabric-reliable-actors-diagnostics.md)
* [Actor API リファレンス ドキュメント](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [コード サンプル](https://github.com/Azure/servicefabric-samples)




<!--HONumber=Nov16_HO3-->


