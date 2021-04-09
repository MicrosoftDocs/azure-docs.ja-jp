---
title: パーティション分割された Azure Service Bus のキューおよびトピックの作成 | Microsoft Docs
description: 複数のメッセージ ブローカーを使用して Service Bus のキューとトピックをパーティション分割する方法について説明します。
ms.topic: article
ms.date: 06/23/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 9c500a69f853b11437a0dcaa48213fe3a84da53b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97796637"
---
# <a name="partitioned-queues-and-topics"></a>パーティション分割されたキューとトピック

Azure Service Bus では、メッセージを処理する複数のメッセージ ブローカーとメッセージを格納する複数のメッセージング ストアを採用しています。 従来のキューまたはトピックは、単一のメッセージ ブローカーで処理されて 1 つのメッセージング ストアに格納されます。 Service Bus "*パーティション*" では、キューとトピック、つまり "*メッセージング エンティティ*" を複数のメッセージ ブローカーとメッセージング ストアにパーティション分割することもできます。 パーティション分割は、パーティション分割されたエンティティの全体のスループットが、単一のメッセージ ブローカーまたはメッセージング ストアのパフォーマンスによって制限されなくなることを意味します。 また、1 つのメッセージング ストアが一時的に停止しても、パーティション分割されたキューまたはトピックは使用することができます。 パーティション分割されたキューとトピックには、トランザクションやセッションのサポートなど、あらゆる高度な Service Bus 機能を含めることができます。

> [!NOTE]
> パーティション分割は、Basic または Standard SKU のすべてのキューおよびトピックに対するエンティティの作成で使用できます。 Premium メッセージング SKU では使用できませんが、Premium 名前空間のパーティション分割された既存のエンティティは継続して正常に動作します。
 
既存のキューまたはトピックでパーティション分割のオプションを変更することはできません。このオプションを設定できるのはエンティティの作成時のみです。

## <a name="how-it-works"></a>しくみ

パーティション分割されたキューまたはトピックはそれぞれ、複数のパーティションで構成されます。 パーティションはそれぞれ別々のメッセージング ストアに格納され、別々のメッセージ ブローカーで処理されます。 パーティション分割されたキューまたはトピックにメッセージが送信されると、Service Bus はそのメッセージをいずれかのパーティションに割り当てます。 対象のフラグメントは、Service Bus によってランダムに選択されるか、送信側で指定できるパーティション キーによって選択されます。

クライアントがパーティション分割されたキューからのメッセージまたはサブスクリプションからパーティション分割されたトピックへのメッセージを受信する場合、Service Bus はすべてのパーティションを照会してメッセージを探し、いずれかのメッセージング ストアから取得された最初のメッセージを受信側に返します。 Service Bus は、追加の受信要求を受信すると、他のメッセージをキャッシュして返します。 受信側クライアントはパーティション分割を認識しません。パーティション分割されたキューやトピックの動作 (読み込み、完了、遅延、配信不能、プリフェッチなど) は、クライアント側には通常のエンティティの動作と同じに見えます。

パーティション分割されていないエンティティに対するピーク操作では、常に最も古いメッセージが返されますが、パーティション分割されたエンティティでは返されません。 代わりに、メッセージ ブローカーが最初に応答したパーティションのうち、最も古いメッセージが返されます。 返されたメッセージが、すべてのパーティションで最も古いメッセージであることは保証されません。 

パーティション分割されたキューやトピックとのメッセージの送受信に追加費用は発生しません。

> [!NOTE]
> ピーク操作によって、最も古いメッセージがシーケンス番号に基づいてパーティションから返されます。 パーティション分割されたエンティティの場合、シーケンス番号はパーティションを基準として発行されます。 詳細については、「[メッセージのシーケンス処理とタイムスタンプ](../service-bus-messaging/message-sequencing.md)」を参照してください。

## <a name="enable-partitioning"></a>パーティション分割の有効化

Azure Service Bus でパーティション分割されたキューとトピックを使用するには、Azure SDK Version 2.2 以降を使用するか、HTTP 要求で `api-version=2013-10` 以降を指定します。

### <a name="standard"></a>Standard

Standard メッセージング レベルでは、Service Bus キューおよびトピックは、1 GB、2 GB、3 GB、4 GB、5 GB で作成できます (既定値は 1 GB)。 パーティション分割が有効な場合、Service Bus は指定されたサイズごとにエンティティの 16 個のコピー (16 個のパーティション) を作成します。 そのため、サイズが 5 GB のキューを作成すると、パーティションが 16 個であるため、最大キュー サイズは 5 \* 16 = 80 GB になります。 パーティション分割したキューまたはトピックの最大サイズは、[Azure portal][Azure portal] のそのエンティティの **[概要]** ブレードでエントリを調べることによって確認できます。

### <a name="premium"></a>Premium

Premium レベルの名前空間では、エンティティのパーティション分割はサポートされていません。 ただし、Service Bus のキューとトピックは、1 GB、2 GB、3 GB、4 GB、5 GB、10 GB、20 GB、40 GB、80 GB で引き続き作成できます (既定値は 1 GB)。 キューまたはトピックのサイズは、[Azure portal][Azure portal] のそのエンティティの **[概要]** ブレードでエントリを調べることによって確認できます。

### <a name="create-a-partitioned-entity"></a>パーティション分割されたエンティティを作成する

パーティション分割されたキューまたはトピックを作成する方法は複数あります。 ご使用のアプリケーションからキューまたはトピックを作成する際に、[QueueDescription.EnablePartitioning][QueueDescription.EnablePartitioning] プロパティまたは [TopicDescription.EnablePartitioning][TopicDescription.EnablePartitioning] プロパティをそれぞれ **true** に設定することで、キューまたはトピックのパーティション分割を有効にできます。 これらのプロパティは、キューまたはトピックが作成された時点で設定される必要があり、従来の [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) ライブラリのみで使用できます。 既に説明したように、既存のキューまたはトピックでこれらのプロパティを変更することはできません。 次に例を示します。

```csharp
// Create partitioned topic
NamespaceManager ns = NamespaceManager.CreateFromConnectionString(myConnectionString);
TopicDescription td = new TopicDescription(TopicName);
td.EnablePartitioning = true;
ns.CreateTopic(td);
```

また、[Azure portal][Azure portal] で、パーティション分割されたキューまたはトピックを作成することもできます。 ポータルでキューまたはトピックを作成する場合は、キューまたはトピックの **[作成]** ダイアログ ボックスにある **[パーティション分割の有効化]** オプションが既定でオンになっています。 このオプションを無効にできるのは Standard レベルのエンティティのみです。Premium レベルでは、パーティション分割はサポートされておらず、チェック ボックスは無効です。 

## <a name="use-of-partition-keys"></a>パーティション キーの使用

パーティション分割されたキューまたはトピックにメッセージがエンキューされると、Service Bus はパーティション キーが存在するかどうかを調べます。 パーティション キーが見つかった場合は、そのキーに基づいてパーティションを選択します。 パーティション キーが見つからない場合は、内部アルゴリズムに基づいてパーティションを選択します。

### <a name="using-a-partition-key"></a>パーティション キーを使用する場合

セッションやトランザクションなどの一部のシナリオでは、メッセージを特定のパーティションに格納する必要があります。 このようなシナリオでは必ず、パーティション キーを使用する必要があります。 同じパーティション キーを使用するメッセージはすべて、同じパーティションに割り当てられます。 そのパーティションが一時的に使用できなくなると、Service Bus はエラーを返します。

以下に示すように、シナリオに応じて、さまざまなメッセージ プロパティがパーティション キーとして使用されます。

**SessionId**: メッセージに [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) プロパティが設定されている場合、Service Bus はパーティション キーとして **SessionID** を使用します。 このように、同じセッションに属するメッセージはすべて、同じメッセージ ブローカーによって処理されます。 セッションでは、Service Bus はメッセージの順序と、セッション状態の整合性を保証することができます。

**PartitionKey**:メッセージに [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) プロパティは設定されているが、[SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) プロパティは設定されていない場合、Service Bus は [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) プロパティ値をパーティション キーとして使用します。 メッセージで [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) プロパティと [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) プロパティが両方とも設定されている場合、この 2 つのプロパティの値は同じである必要があります。 [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) プロパティが [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) プロパティとは異なる値に設定されている場合、Service Bus は無効操作例外を返します。 セッションを認識しないトランザクション メッセージを送信側が送信する場合は、[PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) プロパティを使用する必要があります。 パーティション キーを使用することにより、トランザクション内で送信されるすべてのメッセージを同じメッセージング ブローカーで処理することができます。

**MessageId**: キューまたはトピックで [RequiresDuplicateDetection](/dotnet/api/microsoft.azure.management.servicebus.models.sbqueue.requiresduplicatedetection) プロパティが **true** に設定され、[SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) プロパティまたは [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) プロパティが設定されていない場合は、[MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid) プロパティ値がパーティション キーとしての役割を果たします。 (送信元のアプリケーションでメッセージ ID が割り当てられていない場合は、Microsoft .NET および AMQP のライブラリで自動的に割り当てられます)。この場合は、同じメッセージのすべてのコピーが同じメッセージ ブローカーによって処理されます。 この ID により、Service Bus は重複したメッセージの検出と削除ができるようになります。 [RequiresDuplicateDetection](/dotnet/api/microsoft.azure.management.servicebus.models.sbqueue.requiresduplicatedetection) プロパティが **true** に設定されていない場合、Service Bus は [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid) プロパティをパーティション キーと見なしません。

### <a name="not-using-a-partition-key"></a>パーティション キーを使用しない場合

パーティション キーが存在しない場合、Service Bus は、ラウンドロビン方式で、パーティション分割されたキューまたはトピックのすべてのパーティションにメッセージを配信します。 選択されたパーティションが使用できない場合、Service Bus はメッセージを別のパーティションに割り当てます。 このように、メッセージング ストアが一時的に使用できなくても送信操作は成功します。 ただし、パーティション キーで提供される保証された順序にはなりません。

可用性 (パーティション キーなし) と一貫性 (パーティション キーを使用) の間のトレードオフの詳細については、[こちらの記事](../event-hubs/event-hubs-availability-and-consistency.md)を参照してください。 この情報は、パーティション分割された Service Bus エンティティにも同様に適用されます。

Service Bus が別のパーティションにメッセージをエンキューする時間を確保するには、メッセージの送信元のクライアントによって指定される [OperationTimeout](/dotnet/api/microsoft.azure.servicebus.queueclient.operationtimeout) 値が 15 秒を超える必要があります。 [OperationTimeout](/dotnet/api/microsoft.azure.servicebus.queueclient.operationtimeout) プロパティを既定値の 60 秒に設定することをお勧めします。

パーティション キーは、メッセージを特定のパーティションに "ピン留め" します。 このパーティションを保持しているメッセージング ストアを使用できない場合、Service Bus はエラーを返します。 パーティション キーが存在しない場合、Service Bus は異なるパーティションを選択できるので、操作は成功します。 したがって、パーティション キーが必要でない場合は、パーティション キーを指定しないことをお勧めします。

## <a name="advanced-topics-use-transactions-with-partitioned-entities"></a>高度なトピック: パーティション分割されたエンティティでのトランザクションの使用

トランザクションの一部として送信されるメッセージでは、パーティション キーを指定する必要があります。 キーは次のプロパティのいずれかです: [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid)、[PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey)、または [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid)。 同じトランザクションの一部として送信されるすべてのメッセージで、同じパーティション キーを指定する必要があります。 トランザクション内でパーティション キーなしのメッセージを送信しようとすると、Service Bus は無効操作例外を返します。 同じトランザクション内で異なるパーティション キーを持つ複数のメッセージを送信しようとすると、Service Bus は無効操作例外を返します。 次に例を示します。

```csharp
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    Message msg = new Message("This is a message");
    msg.PartitionKey = "myPartitionKey";
    await messageSender.SendAsync(msg); 
    await ts.CompleteAsync();
}
committableTransaction.Commit();
```

パーティション キーとなるプロパティが設定されている場合、Service Bus はメッセージを特定のパーティションに "ピン留め" します。 この動作は、トランザクションが使用されているかどうかにかかわらず起こります。 必要でない限り、パーティション キーを指定しないことをお勧めします。

## <a name="using-sessions-with-partitioned-entities"></a>パーティション分割されたエンティティでのセッションの使用

トランザクション メッセージを、セッションを認識するトピックまたはキューに送信するには、メッセージに [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) プロパティが設定されている必要があります。 [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) プロパティも指定されている場合は、このプロパティの値が [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) プロパティの値と同じである必要があります。 これらが異なる場合、Service Bus は無効操作例外を返します。

通常の (パーティション分割されていない) キューまたはトピックと異なり、単一のトランザクションを使用して複数のメッセージを別々のセッションに送信することはできません。 これを試みると、Service Bus は無効操作例外を返します。 次に例を示します。

```csharp
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    Message msg = new Message("This is a message");
    msg.SessionId = "mySession";
    await messageSender.SendAsync(msg); 
    await ts.CompleteAsync();
}
committableTransaction.Commit();
```

## <a name="automatic-message-forwarding-with-partitioned-entities"></a>パーティション分割されたエンティティでのメッセージの自動転送

Service Bus では、パーティション分割されたエンティティを送信元または送信先とするメッセージの自動転送、およびパーティション分割されたエンティティ間でのメッセージの自動転送をサポートしています。 メッセージの自動転送を有効にするには、ソース キューまたはサブスクリプションで [QueueDescription.ForwardTo][QueueDescription.ForwardTo] プロパティを設定する必要があります。 メッセージでパーティション キー ([SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid)、[PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey)、[MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid)) が指定されている場合、そのパーティション キーが出力先エンティティで使用されます。

## <a name="considerations-and-guidelines"></a>考慮事項とガイドライン
* **高い整合性機能**: パーティション キーの明示的制御、重複データ検出、セッションといった機能が使用されるエンティティの場合、メッセージング操作は常に特定のパーティションにルーティングされます。 いずれかのパーティションにトラフィックが集中した場合や、基になるストアに異常が生じた場合、それらの操作は失敗し、可用性が低下します。 それでも全体として堅牢性は、パーティション分割されていないエンティティと比べれば、はるかに高くなります。問題が発生するのはトラフィックの一部だけです。すべてのトラフィックで問題が発生するわけではありません。 詳細については、「[Event Hubs における可用性と一貫性](../event-hubs/event-hubs-availability-and-consistency.md)」を参照してください。
* **管理**: 作成、更新、削除といった操作は、エンティティのすべてのパーティションに対して実行する必要があります。 異常のあるパーティションが 1 つでもあると、それらの操作は失敗します。 Get 操作に関して言えば、メッセージ数などの情報は、全パーティションから集計する必要があります。 いずれかのパーティションに異常があった場合、そのエンティティの可用性ステータスは "制限あり" として報告されます。
* **低量メッセージのシナリオ**: このようなシナリオの場合、特に HTTP プロトコルの使用時には、すべてのメッセージを取得するために、複数の受信操作を実行する必要が生じることがあります。 受信要求の場合、フロント エンドは、すべてのパーティションを受信し、返されたすべての応答をキャッシュします。 以降、同じ接続上で行われる受信要求でこのキャッシュを利用できるため、受信で発生する遅延は小さくなります。 ただし複数の接続が存在する場合や、HTTP を使用している場合は、要求ごとに新しい接続が確立されます。 そのため、要求元と同じノードに応答が届く保証はありません。 既存のメッセージがすべてロックされ、別のフロント エンドでキャッシュされている場合は、受信操作から **null** が返されます。 最終的にはメッセージの有効期限が切れ、再度受信できる状態になります。 HTTP キープアライブの使用をお勧めします。 低量のシナリオでパーティション分割を使用する場合、受信操作に予想以上の時間がかかることがあります。 そのため、このようなシナリオではパーティション分割を使用しないことをお勧めします。 パーティション分割された既存のエンティティをすべて削除し、パフォーマンスを向上させるためにパーティション分割を無効にして再作成してください。
* **メッセージの参照/ピーク**: 従来の [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) ライブラリでのみ使用可能です。 [PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) は、必ずしも [MessageCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.messagecount) プロパティに指定された数のメッセージを返すとは限りません。 一般に、この動作には 2 つの理由があります。 1 つ目は、メッセージのコレクションの総サイズが、最大サイズである 256 KB を超えていることです。 2 つ目は、キューまたはトピックの [EnablePartitioning プロパティ](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning)が **true** に設定されている場合に、要求されたメッセージ数を満たすだけのメッセージがパーティションにない可能性があります。 通常、アプリケーションは、決まった数のメッセージを受信する必要がある場合、そのメッセージ数に達するまで、または読み取るメッセージがなくなるまで、[PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) を繰り返し呼び出す必要があります。 コード サンプルを含む詳細については、[QueueClient.PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) または [SubscriptionClient.PeekBatch](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient.peekbatch) に関する API ドキュメントをご覧ください。

## <a name="latest-added-features"></a>最近追加された機能

* パーティション分割エンティティで、ルールの追加と削除がサポートされました。 通常の (パーティション分割されていない) エンティティとは異なり、トランザクションでは、これらの操作がサポートされません。 
* パーティション分割されたエンティティとの間で行われるメッセージの送受信で AMQP がサポートされます。
* AMQP は、次の操作でサポートされるようになりました: [Batch Send](/dotnet/api/microsoft.servicebus.messaging.queueclient.sendbatch)、[Batch Receive](/dotnet/api/microsoft.servicebus.messaging.queueclient.receivebatch)、[Receive by Sequence Number](/dotnet/api/microsoft.servicebus.messaging.queueclient.receive)、[Peek](/dotnet/api/microsoft.servicebus.messaging.queueclient.peek)、[Renew Lock](/dotnet/api/microsoft.servicebus.messaging.queueclient.renewmessagelock)、[Schedule Message](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync)、[Cancel Scheduled Message](/dotnet/api/microsoft.azure.servicebus.queueclient.cancelscheduledmessageasync)、[Add Rule](/dotnet/api/microsoft.azure.servicebus.ruledescription)、[Remove Rule](/dotnet/api/microsoft.azure.servicebus.ruledescription)、[Session Renew Lock](/dotnet/api/microsoft.servicebus.messaging.messagesession.renewlock)、[Set Session State](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate)、[Get Session State](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate)、[Enumerate Sessions](/dotnet/api/microsoft.servicebus.messaging.queueclient.getmessagesessions)。

## <a name="partitioned-entities-limitations"></a>パーティション分割されたエンティティの制限事項

現在、Service Bus は、パーティション分割されたキューまたはトピックに以下の制限を適用します。

* Premium メッセージング レベルでは、パーティション分割されたキューとトピックをサポートしていません。 セッションは、SessionId を使用することで Premier レベルでサポートされます。 
* パーティション分割されたキューまたはトピックは、単一のトランザクションの別々のセッションに属するメッセージの送信はサポートしていません。
* Service Bus は、現在、名前空間あたり最大 100 のパーティション分割されたキューまたはトピックをサポートします。 パーティション分割された各キューまたはトピックは、名前空間あたり 10,000 エンティティのクォータに対してカウントされます (Premium レベルには適用されません)。

## <a name="next-steps"></a>次のステップ

[AMQP 1.0 プロトコル ガイド](service-bus-amqp-protocol-guide.md)で、AMQP 1.0 メッセージング仕様の核となる概念を確認します。

[Azure portal]: https://portal.azure.com
[QueueDescription.EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning
[TopicDescription.EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.topicdescription.enablepartitioning
[QueueDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.forwardto
[AMQP 1.0 support for Service Bus partitioned queues and topics]: ./service-bus-amqp-protocol-guide.md
