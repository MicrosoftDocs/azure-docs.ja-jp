---
title: Reliable Services の通知
description: Reliable State Manager と Reliable Dictionary に関して Service Fabric Reliable Services の通知の概念をまとめたドキュメント
author: mcoskun
ms.topic: conceptual
ms.date: 6/29/2017
ms.author: mcoskun
ms.custom: devx-track-csharp
ms.openlocfilehash: f5b48cc6cca2e143c48ed7bdfc99de936be2a227
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98784581"
---
# <a name="reliable-services-notifications"></a>Reliable Services の通知
通知により、クライアントは対象となるオブジェクトの変更を追跡できます。 通知は 2 種類のオブジェクトでサポートされます: *Reliable State Manager* および *Reliable Dictionary*。

通知を使用する一般的な理由は次のとおりです。

* セカンダリ インデックスなどのマテリアライズドビューや、レプリカの状態の集計フィルター ビューを構築するため。 たとえば、Reliable Dictionary のすべてのキーの並べ替えられたインデックスなどがあります。
* 監視データ (過去 1 時間に追加されたユーザーの数など) を送信するため。

通知は操作の適用の一環として発生します。 そのため、通知はできるだけ速やかに処理する必要があり、同期イベントには負荷の高い操作を含めないようにする必要があります。

## <a name="reliable-state-manager-notifications"></a>Reliable State Manager の通知
Reliable State Manager は、次のイベントの通知を提供します。

* トランザクション
  * Commit
* 状態マネージャー
  * [再構築]
  * Reliable State の追加
  * Reliable State の削除

Reliable State Manager は、現在処理中のトランザクションを追跡します。 通知を発生させるトランザクションの状態の変化は、トランザクションのコミットだけです。

Reliable State Manager は、Reliable Dictionary や Reliable Queue などの Reliable State のコレクションを保持します。 このコレクションが変更されると (Reliable State が追加または削除されたときや、コレクション全体が再構築されたとき)、Reliable State Manager は通知を発生させます。
Reliable State Manager のコレクションは、次の 3 つの状況で再構築されます。

* 回復: レプリカは、起動時にディスクから以前の状態を回復します。 回復の最後に、回復された Reliable State のセットを含む **NotifyStateManagerChangedEventArgs** を使用して、イベントを発生させます。
* 完全コピー: レプリカが構成セットに参加できるようにするには、レプリカを構築しておく必要があります。 場合によっては、プライマリ レプリカの Reliable State Manager の状態の完全コピーを、アイドル状態のセカンダリ レプリカに適用する必要があります。 セカンダリ レプリカの Reliable State Manager は、プライマリ レプリカから取得した Reliable State のセットを含む **NotifyStateManagerChangedEventArgs** を使用して、イベントを発生させます。
* 復元: ディザスター リカバリー シナリオでは、**RestoreAsync** を介して、レプリカの状態をバックアップから復元できます。 そうした場合、プライマリ レプリカの Reliable State Manager は、バックアップから復元した Reliable State のセットを含む **NotifyStateManagerChangedEventArgs** を使用して、イベントを発生させます。

トランザクション通知または State Manager 通知を登録するには、Reliable State Manager で **TransactionChanged** イベントまたは **StateManagerChanged** イベントに登録する必要があります。 これらのイベント ハンドラーに登録する一般的な場所は、ステートフル サービスのコンストラクターです。 コンストラクターで登録すると、 **IReliableStateManager** の有効期間中の変更によって発生した通知を見落とすことがなくなります。

```csharp
public MyService(StatefulServiceContext context)
    : base(MyService.EndpointName, context, CreateReliableStateManager(context))
{
    this.StateManager.TransactionChanged += this.OnTransactionChangedHandler;
    this.StateManager.StateManagerChanged += this.OnStateManagerChangedHandler;
}
```

**TransactionChanged** イベント ハンドラーでは、**NotifyTransactionChangedEventArgs** を使用してイベントの詳細を提供します。 これには、変更の種類を示す Action プロパティ (例: **NotifyTransactionChangedAction.Commit**) と、 変更されたトランザクションへの参照を提供する Transaction プロパティが含まれます。

> [!NOTE]
> 現時点では、 **TransactionChanged** イベントは、トランザクションがコミットされた場合にのみ発生します。 その場合、Action が **NotifyTransactionChangedAction.Commit** になります。 ただし、今後、他の種類のトランザクションの状態変更によってイベントが発生する可能性があります。 そのため、Action を確認し、Action が予想していたものである場合にのみ、そのイベントを処理することをお勧めします。
> 
> 

**TransactionChanged** イベント ハンドラーの例を次に示します。

```csharp
private void OnTransactionChangedHandler(object sender, NotifyTransactionChangedEventArgs e)
{
    if (e.Action == NotifyTransactionChangedAction.Commit)
    {
        this.lastCommitLsn = e.Transaction.CommitSequenceNumber;
        this.lastTransactionId = e.Transaction.TransactionId;

        this.lastCommittedTransactionList.Add(e.Transaction.TransactionId);
    }
}
```

**StateManagerChanged** イベント ハンドラーでは、**NotifyStateManagerChangedEventArgs** を使用してイベントの詳細を提供します。
**NotifyStateManagerChangedEventArgs** には、2 つのサブクラスがあります: **NotifyStateManagerRebuildEventArgs** および **NotifyStateManagerSingleEntityChangedEventArgs**。
**NotifyStateManagerChangedEventArgs** の Action プロパティを使用して、**NotifyStateManagerChangedEventArgs** を適切なサブクラスにキャストします。

* **NotifyStateManagerChangedAction.Rebuild**: **NotifyStateManagerRebuildEventArgs**
* **NotifyStateManagerChangedAction.Add** および **NotifyStateManagerChangedAction.Remove**: **NotifyStateManagerSingleEntityChangedEventArgs**

**StateManagerChanged** 通知ハンドラーの例を次に示します。

```csharp
public void OnStateManagerChangedHandler(object sender, NotifyStateManagerChangedEventArgs e)
{
    if (e.Action == NotifyStateManagerChangedAction.Rebuild)
    {
        this.ProcessStateManagerRebuildNotification(e);

        return;
    }

    this.ProcessStateManagerSingleEntityNotification(e);
}
```

## <a name="reliable-dictionary-notifications"></a>Reliable Dictionary の通知
Reliable Dictionary は、次のイベントの通知を提供します。

* 再構築:**ReliableDictionary** が回復またはコピーされたローカルの状態またはバックアップから状態を回復したときに呼び出されます。
* Clear: **ClearAsync** メソッドを使用して、**ReliableDictionary** の状態がクリアされたときに呼び出されます。
* 次の項目を追加します。**ReliableDictionary** に項目が追加されたときに呼び出されます。
* Update: **IReliableDictionary** の項目が更新されたときに呼び出されます。
* Remove: **IReliableDictionary** の項目が削除されたときに呼び出されます。

Reliable Dictionary の通知を取得するには、**IReliableDictionary** で **DictionaryChanged** イベント ハンドラーに登録する必要があります。 これらのイベント ハンドラーに登録する一般的な場所は、 **ReliableStateManager.StateManagerChanged** の追加通知内です。
**IReliableDictionary** を **IReliableStateManager** に追加するときに登録すると、通知を見落とすことがなくなります。

```csharp
private void ProcessStateManagerSingleEntityNotification(NotifyStateManagerChangedEventArgs e)
{
    var operation = e as NotifyStateManagerSingleEntityChangedEventArgs;

    if (operation.Action == NotifyStateManagerChangedAction.Add)
    {
        if (operation.ReliableState is IReliableDictionary<TKey, TValue>)
        {
            var dictionary = (IReliableDictionary<TKey, TValue>)operation.ReliableState;
            dictionary.RebuildNotificationAsyncCallback = this.OnDictionaryRebuildNotificationHandlerAsync;
            dictionary.DictionaryChanged += this.OnDictionaryChangedHandler;
        }
    }
}
```

> [!NOTE]
> **ProcessStateManagerSingleEntityNotification** は、前の **OnStateManagerChangedHandler** の例で呼び出されたサンプル メソッドです。
> 
> 

前のコードでは、**IReliableNotificationAsyncCallback** インターフェイスと **DictionaryChanged** を設定しています。 **NotifyDictionaryRebuildEventArgs** には、非同期で列挙する必要がある **IAsyncEnumerable** インターフェイスが含まれているので、再構築通知は **OnDictionaryChangedHandler** ではなく、**RebuildNotificationAsyncCallback** によって発生します。

```csharp
public async Task OnDictionaryRebuildNotificationHandlerAsync(
    IReliableDictionary<TKey, TValue> origin,
    NotifyDictionaryRebuildEventArgs<TKey, TValue> rebuildNotification)
{
    this.secondaryIndex.Clear();

    var enumerator = e.State.GetAsyncEnumerator();
    while (await enumerator.MoveNextAsync(CancellationToken.None))
    {
        this.secondaryIndex.Add(enumerator.Current.Key, enumerator.Current.Value);
    }
}
```

> [!NOTE]
> 前のコードでは、再構築通知の処理の一環として、まず、保持されている集計された状態がクリアされます。 Reliable Collection は新しい状態で再構築されるので、以前の通知はすべて無関係になります。
> 
> 

**DictionaryChanged** イベント ハンドラーでは、**NotifyDictionaryChangedEventArgs** を使用してイベントの詳細を提供します。
**NotifyDictionaryChangedEventArgs** には 5 つのサブクラスがあります。 **NotifyDictionaryChangedEventArgs** の Action プロパティを使用して、**NotifyDictionaryChangedEventArgs** を適切なサブクラスにキャストします。

* **NotifyDictionaryChangedAction.Rebuild**: **NotifyDictionaryRebuildEventArgs**
* **NotifyDictionaryChangedAction.Clear**: **NotifyDictionaryClearEventArgs**
* **NotifyDictionaryChangedAction.Add**:**NotifyDictionaryItemAddedEventArgs**
* **NotifyDictionaryChangedAction.Update**: **NotifyDictionaryItemUpdatedEventArgs**
* **NotifyDictionaryChangedAction.Remove**: **NotifyDictionaryItemRemovedEventArgs**

```csharp
public void OnDictionaryChangedHandler(object sender, NotifyDictionaryChangedEventArgs<TKey, TValue> e)
{
    switch (e.Action)
    {
        case NotifyDictionaryChangedAction.Clear:
            var clearEvent = e as NotifyDictionaryClearEventArgs<TKey, TValue>;
            this.ProcessClearNotification(clearEvent);
            return;

        case NotifyDictionaryChangedAction.Add:
            var addEvent = e as NotifyDictionaryItemAddedEventArgs<TKey, TValue>;
            this.ProcessAddNotification(addEvent);
            return;

        case NotifyDictionaryChangedAction.Update:
            var updateEvent = e as NotifyDictionaryItemUpdatedEventArgs<TKey, TValue>;
            this.ProcessUpdateNotification(updateEvent);
            return;

        case NotifyDictionaryChangedAction.Remove:
            var deleteEvent = e as NotifyDictionaryItemRemovedEventArgs<TKey, TValue>;
            this.ProcessRemoveNotification(deleteEvent);
            return;

        default:
            break;
    }
}
```

## <a name="recommendations"></a>Recommendations
* *します* 。
* *しないでください* 。
* *します* 。 今後、新しい Action の種類が追加される可能性があります。

次の点に注意してください。

* 通知は操作の実行の一環として発生します。 たとえば、復元通知は復元操作の最後の手順として発生します。 通知イベントが処理されるまで、復元は終了しません。
* 通知は操作の適用の一環として発生するため、クライアントにはローカルでコミットされた操作の通知だけが表示されます。 操作はローカルでコミットされる (ログに記録される) ことだけが保証されるので、後で元に戻される場合もあれば、戻されない場合もあります。
* やり直しパスでは、適用された操作ごとに 1 つの通知が発生します。 つまり、トランザクション T1 に Create(X)、Delete(X)、Create(X) が含まれている場合、X の作成の通知、削除の通知、再度作成の通知をこの順序で受け取ることになります。
* トランザクションに複数の操作が含まれている場合、操作はプライマリ レプリカでユーザーから受信した順序で適用されます。
* 誤った進行の処理の一環として、一部の操作が元に戻されることがあります。 レプリカの状態を安定したポイントにロールバックするこのような元に戻す操作では、通知が発生します。 元に戻す通知の重要な違いの 1 つは、重複するキーを持つイベントが集約されることです。 たとえば、トランザクション T1 を元に戻すと、ユーザーには Delete(X) の通知だけが表示されます。

## <a name="next-steps"></a>次のステップ
* [Reliable Collection](service-fabric-work-with-reliable-collections.md)
* [Reliable Service の概要](service-fabric-reliable-services-quick-start.md)
* [Reliable Service のバックアップと復元 (障害復旧)](service-fabric-reliable-services-backup-restore.md)
* [Reliable Collection の開発者向けリファレンス](/dotnet/api/microsoft.servicefabric.data.collections#microsoft_servicefabric_data_collections)
