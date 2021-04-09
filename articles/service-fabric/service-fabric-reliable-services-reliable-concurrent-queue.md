---
title: Azure Service Fabric の ReliableConcurrentQueue
description: ReliableConcurrentQueue は、並列エンキューと並列デキューが利用できる高スループットのキューです。
ms.topic: conceptual
ms.date: 5/1/2017
ms.openlocfilehash: d6852982621d3efd3f4a8597a2959fceb13abd12
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98784293"
---
# <a name="introduction-to-reliableconcurrentqueue-in-azure-service-fabric"></a>Azure Service Fabric の ReliableConcurrentQueue の概要
Reliable Concurrent Queue は、エンキュー操作とデキュー操作に関して高いコンカレンシーを備えた、非同期、トランザクショナル、レプリケートを特徴とするキューです。 [Reliable Queue](/dotnet/api/microsoft.servicefabric.data.collections.ireliablequeue-1#microsoft_servicefabric_data_collections_ireliablequeue_1) の特徴である厳密な FIFO の順序付けを緩和し、ベストエフォートの順序付けを利用できるようにすることで、高いスループットと短い待ち時間が得られるように設計されています。

## <a name="apis"></a>API

|Concurrent Queue                |Reliable Concurrent Queue                                         |
|--------------------------------|------------------------------------------------------------------|
| void Enqueue(T item)           | Task EnqueueAsync(ITransaction tx, T item)                       |
| bool TryDequeue(out T result)  | Task< ConditionalValue < T > > TryDequeueAsync(ITransaction tx)  |
| int Count()                    | long Count()                                                     |

## <a name="comparison-with-reliable-queue"></a>[Reliable Queue](/dotnet/api/microsoft.servicefabric.data.collections.ireliablequeue-1#microsoft_servicefabric_data_collections_ireliablequeue_1) との比較

Reliable Concurrent Queue は、[Reliable Queue](/dotnet/api/microsoft.servicefabric.data.collections.ireliablequeue-1#microsoft_servicefabric_data_collections_ireliablequeue_1) の代替手段として提供されています。 このキューは FIFO の厳密な順序付けが必要ないケースで使用してください。FIFO の順序を確実に守るためには、そのトレードオフとして、コンカレンシーは放棄しなければなりません。  [Reliable Queue](/dotnet/api/microsoft.servicefabric.data.collections.ireliablequeue-1#microsoft_servicefabric_data_collections_ireliablequeue_1) では、エンキューとデキューを許可するトランザクションを一度に 1 つまでとし、FIFO の順序付けを強制的に適用するためにロックが使用されます。 これに対し、Reliable Concurrent Queue では、順序付けの制約が緩和され、任意の数の同時トランザクションが交互に、そのエンキュー操作とデキュー操作を実行できます。 Reliable Concurrent Queue では、ベストエフォートでの順序付けは備わっていますが、2 つの値の相対的順序は保証されません。

エンキュー/デキューを実行する同時トランザクションが複数存在するときは必ず、[Reliable Queue](/dotnet/api/microsoft.servicefabric.data.collections.ireliablequeue-1#microsoft_servicefabric_data_collections_ireliablequeue_1) よりも Reliable Concurrent Queue の方がスループットは高く、待ち時間は短くなります。

ReliableConcurrentQueue の使用例として、[メッセージ キュー](https://en.wikipedia.org/wiki/Message_queue)のシナリオが挙げられます。 このシナリオでは、メッセージ プロデューサーが、何らかの要素を作成してキューに追加し、メッセージ コンシューマーがキューからメッセージを取り出して処理します。 同時トランザクションを使用して複数のプロデューサーと複数のコンシューマーが別々に動作し、そのキューを処理することが可能です。

## <a name="usage-guidelines"></a>使用方法のガイドライン
* キューに格納されている要素のリテンション期間は短いことが想定されています。 つまりキューに長い間、要素が留まらないことが前提となります。
* このキューでは、FIFO の厳密な順序が保証されません。
* このキューは、それ自身の書き込みを読み取りません。 トランザクション内で何らかの要素がエンキューされた場合、同じトランザクション内のデキュー側からは、その要素が見えません。
* デキューは互いに分離されているわけではありません。 トランザクション *txnA* で要素 *A* がデキューされた場合、*txnA* がコミットされなかったとしても、同時トランザクション *txnB* からは、要素 *A* が見えません。  *txnA* が中止された場合は、直ちに *txnB* から *A* が見えるようになります。
* *TryPeekAsync* の動作は、*TryDequeueAsync* の後でトランザクションを中止することで実装できます。 この動作の例については、「プログラミング パターン」セクションを参照してください。
* Count にトランザクション性はありません。 キュー内の要素数を大まかに把握する目的で使うことはできますが、それが表すのは特定の時点での情報であり、信頼することはできません。
* システムのパフォーマンスに影響が生じる可能性がある長時間実行されるトランザクションを回避するため、トランザクションがアクティブである間は、デキューされた項目に対して負荷の大きい処理を実行しないでください。

## <a name="code-snippets"></a>コード スニペット
いくつかのコード スニペットとその結果として得られる出力について見ていきましょう。 このセクションでは例外処理は行っていません。

### <a name="instantiation"></a>インスタンス化
信頼性の高い同時実行キューのインスタンスを作成することは、他のリライアブル コレクションに似ています。

```csharp
IReliableConcurrentQueue<int> queue = await this.StateManager.GetOrAddAsync<IReliableConcurrentQueue<int>>("myQueue");
```

### <a name="enqueueasync"></a>EnqueueAsync
以下に示したのは、EnqueueAsync を使ったいくつかのコード スニペットと予想される出力結果です。

- "*ケース 1: 単一のエンキュー タスク*"

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.EnqueueAsync(txn, 10, cancellationToken);
    await this.Queue.EnqueueAsync(txn, 20, cancellationToken);

    await txn.CommitAsync();
}
```

このタスクが正常に完了し、このキューに変更を加える同時トランザクションが存在しなかったと仮定しましょう。 この場合キューには、次のいずれかの順序で要素が格納されていると考えられます。

> 10、20
> 
> 20、10


- "*ケース 2: 並列エンキュー タスク*"

```
// Parallel Task 1
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.EnqueueAsync(txn, 10, cancellationToken);
    await this.Queue.EnqueueAsync(txn, 20, cancellationToken);

    await txn.CommitAsync();
}

// Parallel Task 2
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.EnqueueAsync(txn, 30, cancellationToken);
    await this.Queue.EnqueueAsync(txn, 40, cancellationToken);

    await txn.CommitAsync();
}
```

これらのタスクが並列実行されて正常に完了し、このキューに変更を加える同時トランザクションが存在しなかったと仮定しましょう。 キュー内の要素の順序を推測することはできません。 このコード スニペットでは、要素の順序が 24 とおり (4 の階乗) 存在し、 そのどれに該当するかは不定です。  キューは、元の (エンキュー時の) 順序で要素を保とうとしますが、同時に実行される操作やエラーが原因でやむをえず順序を変更する可能性があります。


### <a name="dequeueasync"></a>DequeueAsync
以下に示したのは、TryDequeueAsync を使ったいくつかのコード スニペットと予想される出力結果です。 キューには既に、次の要素が格納されているとします。
> 10、20、30、40、50、60

- "*ケース 1: 単一のデキュー タスク*"

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);

    await txn.CommitAsync();
}
```

このタスクが正常に完了し、このキューに変更を加える同時トランザクションが存在しなかったと仮定しましょう。 キュー内の要素の順序を推測することはできません。任意の 3 つの要素が任意の順序でデキューされる可能性があります。 キューは、元の (エンキュー時の) 順序で要素を保とうとしますが、同時に実行される操作やエラーが原因でやむをえず順序を変更する可能性があります。  

- "*ケース 2: 並列デキュー タスク*"

```
// Parallel Task 1
List<int> dequeue1;
using (var txn = this.StateManager.CreateTransaction())
{
    dequeue1.Add(await this.Queue.TryDequeueAsync(txn, cancellationToken)).val;
    dequeue1.Add(await this.Queue.TryDequeueAsync(txn, cancellationToken)).val;

    await txn.CommitAsync();
}

// Parallel Task 2
List<int> dequeue2;
using (var txn = this.StateManager.CreateTransaction())
{
    dequeue2.Add(await this.Queue.TryDequeueAsync(txn, cancellationToken)).val;
    dequeue2.Add(await this.Queue.TryDequeueAsync(txn, cancellationToken)).val;

    await txn.CommitAsync();
}
```

これらのタスクが並列実行されて正常に完了し、このキューに変更を加える同時トランザクションが存在しなかったと仮定しましょう。 キュー内の要素の順序を推測することはできません。そのため、*dequeue1* と *dequeue2* の各リストにはそれぞれ、任意の 2 つの要素が任意の順序で格納されます。

同じ要素が両方のリストに出現することは "*ありません*"。 したがって dequeue1 に *10*、*30* が格納されている場合、dequeue2 には *20*、*40* が格納されます。

- "*ケース 3: トランザクションの中止を伴うデキューの順序*"

デキューの途中でトランザクションを中止すると、取り出された要素がキューの先頭に戻されます。 キューの先頭に要素が戻される順序は保証されません。 以下のコードを見てください。

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);

    // Abort the transaction
    await txn.AbortAsync();
}
```
要素が次の順序でデキューされたと仮定します。
> 10、20

トランザクションを中止すると、次のいずれかの順序で要素がキューの先頭に戻されます。
> 10、20
> 
> 20、10

同じことは、トランザクションが正常に "*コミット*" されないすべてのケースに当てはまります。

## <a name="programming-patterns"></a>プログラミング パターン
このセクションでは、ReliableConcurrentQueue を使う際に役立つと考えられるプログラミング パターンをいくつか見ていきます。

### <a name="batch-dequeues"></a>バッチ デキュー
推奨されるプログラミング パターンは、コンシューマー タスクで、そのデキューを 1 つずつ実行するのではなくバッチ処理することです。 個々のバッチ (バッチ サイズ) の後に設ける待ち時間は、ユーザーがスロットルできます。 このプログラミング モデルを示したのが、以下のコード スニペットです。 この例では、トランザクションがコミットされた後に処理が実行されます。したがって、処理中にエラーが発生した場合、未処理の項目は、処理されないまま失われることに注意してください。  代わりに、トランザクションのスコープ内で処理を実行することもできますが、パフォーマンスに悪影響が生じる可能性があり、既に処理されている項目の処理が必要になります。

```
int batchSize = 5;
long delayMs = 100;

while(!cancellationToken.IsCancellationRequested)
{
    // Buffer for dequeued items
    List<int> processItems = new List<int>();

    using (var txn = this.StateManager.CreateTransaction())
    {
        ConditionalValue<int> ret;

        for(int i = 0; i < batchSize; ++i)
        {
            ret = await this.Queue.TryDequeueAsync(txn, cancellationToken);

            if (ret.HasValue)
            {
                // If an item was dequeued, add to the buffer for processing
                processItems.Add(ret.Value);
            }
            else
            {
                // else break the for loop
                break;
            }
        }

        await txn.CommitAsync();
    }

    // Process the dequeues
    for (int i = 0; i < processItems.Count; ++i)
    {
        Console.WriteLine("Value : " + processItems[i]);
    }

    int delayFactor = batchSize - processItems.Count;
    await Task.Delay(TimeSpan.FromMilliseconds(delayMs * delayFactor), cancellationToken);
}
```

### <a name="best-effort-notification-based-processing"></a>ベストエフォートの通知ベースの処理
もう 1 つの興味深いプログラミング パターンとして、Count API を使った方法を紹介します。 ここでは、キューに関して、ベストエフォートの通知ベースの処理を実装することができます。 エンキュー タスクまたはデキュー タスクは、キューの Count を使ってスロットルすることができます。  先ほどの例と同様、処理はトランザクションの外で実行されるため、処理中にエラーが発生した場合、未処理の要素は失われます。

```
int threshold = 5;
long delayMs = 1000;

while(!cancellationToken.IsCancellationRequested)
{
    while (this.Queue.Count < threshold)
    {
        cancellationToken.ThrowIfCancellationRequested();

        // If the queue does not have the threshold number of items, delay the task and check again
        await Task.Delay(TimeSpan.FromMilliseconds(delayMs), cancellationToken);
    }

    // If there are approximately threshold number of items, try and process the queue

    // Buffer for dequeued items
    List<int> processItems = new List<int>();

    using (var txn = this.StateManager.CreateTransaction())
    {
        ConditionalValue<int> ret;

        do
        {
            ret = await this.Queue.TryDequeueAsync(txn, cancellationToken);

            if (ret.HasValue)
            {
                // If an item was dequeued, add to the buffer for processing
                processItems.Add(ret.Value);
            }
        } while (processItems.Count < threshold && ret.HasValue);

        await txn.CommitAsync();
    }

    // Process the dequeues
    for (int i = 0; i < processItems.Count; ++i)
    {
        Console.WriteLine("Value : " + processItems[i]);
    }
}
```

### <a name="best-effort-drain"></a>ベストエフォートのドレイン
データ構造の同時性のために、キューのドレインは保証できません。  キューに実行中のユーザーの操作がない場合でも、TryDequeueAsync に対する特定の呼び出しから、既にエンキューされてコミットされた項目が返されない可能性があります。  "*最終的には*"、エンキューされた要素がデキュー側から見えるようになりますが、アウトオブバンドの通信メカニズムがなければ、個々のコンシューマーは、そのキューが定常状態に到達したことを把握できません。これは、すべてのプロデューサーが停止して、新しいエンキュー処理が発生することがないとしても同様です。 したがってドレインはベストエフォートの処理として、以下のように実装されます。

キューのドレインを試行する場合は、あらかじめ後続のプロデューサー タスクとコンシューマー タスクをすべて停止し、実行中のトランザクションをコミット (または中止) しておく必要があります。  キューに予期される項目の数がわかっている場合は、すべての項目がデキューされたことを知らせる通知を設定することができます。

```
int numItemsDequeued;
int batchSize = 5;

ConditionalValue ret;

do
{
    List<int> processItems = new List<int>();

    using (var txn = this.StateManager.CreateTransaction())
    {
        do
        {
            ret = await this.Queue.TryDequeueAsync(txn, cancellationToken);

            if(ret.HasValue)
            {
                // Buffer the dequeues
                processItems.Add(ret.Value);
            }
        } while (ret.HasValue && processItems.Count < batchSize);

        await txn.CommitAsync();
    }

    // Process the dequeues
    for (int i = 0; i < processItems.Count; ++i)
    {
        Console.WriteLine("Value : " + processItems[i]);
    }
} while (ret.HasValue);
```

### <a name="peek"></a>プレビュー
ReliableConcurrentQueue には、*TryPeekAsync* API がありません。 プレビューのセマンティクスは、*TryDequeueAsync* の後でトランザクションを中止することによって実現できます。 この例では、要素の値が *10* より大きい場合にのみデキューが処理されます。

```
using (var txn = this.StateManager.CreateTransaction())
{
    ConditionalValue ret = await this.Queue.TryDequeueAsync(txn, cancellationToken);
    bool valueProcessed = false;

    if (ret.HasValue)
    {
        if (ret.Value > 10)
        {
            // Process the item
            Console.WriteLine("Value : " + ret.Value);
            valueProcessed = true;
        }
    }

    if (valueProcessed)
    {
        await txn.CommitAsync();    
    }
    else
    {
        await txn.AbortAsync();
    }
}
```

## <a name="must-read"></a>関連記事
* [Reliable Services の概要](service-fabric-reliable-services-quick-start.md)
* [Reliable Collection での作業](service-fabric-work-with-reliable-collections.md)
* [Reliable Services の通知](service-fabric-reliable-services-notifications.md)
* [Reliable Services のバックアップと復元 (ディザスター リカバリー)](service-fabric-reliable-services-backup-restore.md)
* [Reliable State Manager の構成](service-fabric-reliable-services-configuration.md)
* [Service Fabric Web API サービスの概要](./service-fabric-reliable-services-communication-aspnetcore.md)
* [Reliable Services プログラミング モデルの詳細な使用方法](./service-fabric-reliable-services-lifecycle.md)
* [Reliable Collection の開発者向けリファレンス](/dotnet/api/microsoft.servicefabric.data.collections#microsoft_servicefabric_data_collections)
