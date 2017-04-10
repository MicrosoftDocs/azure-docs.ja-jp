---
title: "パーティション分割された Azure Service Bus のキューおよびトピックの作成 | Microsoft Docs"
description: "複数のメッセージ ブローカーを使用して Service Bus のキューとトピックをパーティション分割する方法について説明します。"
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: a0c7d5a2-4876-42cb-8344-a1fc988746e7
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/28/2017
ms.author: sethm;hillaryc
translationtype: Human Translation
ms.sourcegitcommit: b4802009a8512cb4dcb49602545c7a31969e0a25
ms.openlocfilehash: 1952adece7e874ade97c2a8480455e1236bb74f1
ms.lasthandoff: 03/29/2017


---
# <a name="partitioned-queues-and-topics"></a>パーティション分割されたキューとトピック
Azure Service Bus では、メッセージを処理する複数のメッセージ ブローカーとメッセージを格納する複数のメッセージング ストアを採用しています。 従来のキューまたはトピックは、単一のメッセージ ブローカーで処理されて 1 つのメッセージング ストアに格納されます。 Service Bus *パーティション*では、キューまたはトピックを複数のメッセージ ブローカーとメッセージング ストアにパーティション分割することもできます。 そのため、パーティション分割されたキューまたはトピックの全体のスループットは、単一のメッセージ ブローカーまたはメッセージング ストアのパフォーマンスによって制限されなくなりました。 また、1 つのメッセージング ストアが一時的に停止しても、パーティション分割されたキューまたはトピックは使用することができます。 パーティション分割されたキューとトピックには、トランザクションやセッションのサポートなど、あらゆる高度な Service Bus 機能を含めることができます。

Service Bus 内部の詳細については、「[Service Bus アーキテクチャ][Service Bus architecture]」の記事をご覧ください。

## <a name="how-it-works"></a>動作のしくみ
パーティション分割されたキューまたはトピックはそれぞれ、複数のフラグメントで構成されます。 フラグメントはそれぞれ別々のメッセージング ストアに格納され、別々のメッセージ ブローカーで処理されます。 パーティション分割されたキューまたはトピックにメッセージが送信されると、Service Bus はそのメッセージをいずれかのフラグメントに割り当てます。 対象のフラグメントは、Service Bus によってランダムに選択されるか、送信側で指定できるパーティション キーによって選択されます。

クライアントがパーティション分割されたキューからのメッセージまたはサブスクリプションからパーティション分割されたトピックへのメッセージを受信する場合、Service Bus はすべてのフラグメントを照会してメッセージを探し、いずれかのメッセージング ストアから取得された最初のメッセージを受信側に返します。 Service Bus は、追加の受信要求を受信すると、他のメッセージをキャッシュして返します。 受信側クライアントはパーティション分割を認識しません。パーティション分割されたキューやトピックの動作 (読み込み、完了、遅延、配信不能、プリフェッチなど) は、クライアント側には通常のエンティティの動作と同じに見えます。

パーティション分割されたキューやトピックとのメッセージの送受信に追加費用は発生しません。

## <a name="enable-partitioning"></a>パーティション分割の有効化
Azure Service Bus でパーティション分割されたキューとトピックを使用するには、Azure SDK Version 2.2 以降を使用するか、HTTP 要求で `api-version=2013-10` を指定します。

Service Bus のキューとトピックは、1 GB、2 GB、3 GB、4 GB、5 GB で作成できます (既定値は 1 GB)。 パーティション分割が有効な場合、Service Bus は指定された GB 数に対して 1 GB ごとに 16 個のパーティションを作成します。 そのため、サイズが 5 GB のキューを作成すると、パーティションが 16 個であるため、最大キュー サイズは 5 \* 16 = 80 GB になります。 パーティション分割したキューまたはトピックの最大サイズは、[Azure Portal][Azure portal] の各エントリで確認できます。

パーティション分割されたキューまたはトピックを作成する方法は複数あります。 ご使用のアプリケーションからキューまたはトピックを作成する際に、[QueueDescription.EnablePartitioning][QueueDescription.EnablePartitioning] プロパティまたは [TopicDescription.EnablePartitioning][TopicDescription.EnablePartitioning] プロパティをそれぞれ **true** に設定することで、キューまたはトピックのパーティション分割を有効にできます。 これらのプロパティは、キューまたはトピックの作成時に設定する必要があります。 既存のキューまたはトピックでこれらのプロパティを変更することはできません。 次に例を示します。

```csharp
// Create partitioned topic
NamespaceManager ns = NamespaceManager.CreateFromConnectionString(myConnectionString);
TopicDescription td = new TopicDescription(TopicName);
td.EnablePartitioning = true;
ns.CreateTopic(td);
```

また、Visual Studio または [Azure Portal][Azure portal] で、パーティション分割されたキューまたはトピックを作成することもできます。 ポータルでキューまたはトピックを作成する場合は、キューまたはトピックの **[設定]** ウィンドウの **[全般設定]** ブレードにある **[パーティション分割の有効化]** オプションを **true** に設定します。 Visual Studio で作成する場合は、**[新しいキュー]** または **[新しいトピック]** ダイアログ ボックスの ** [パーティション分割の有効化] ** チェック ボックスをオンにします。

## <a name="use-of-partition-keys"></a>パーティション キーの使用
パーティション分割されたキューまたはトピックにメッセージがエンキューされると、Service Bus はパーティション キーが存在するかどうかを調べます。 パーティション キーが見つかった場合は、そのキーに基づいてフラグメントを選択します。 パーティション キーが見つからない場合は、内部アルゴリズムに基づいてフラグメントを選択します。

### <a name="using-a-partition-key"></a>パーティション キーを使用する場合
セッションやトランザクションなどの一部のシナリオでは、メッセージを特定のフラグメントに格納する必要があります。 このようなシナリオでは必ず、パーティション キーを使用する必要があります。 同じパーティション キーを使用するメッセージはすべて、同じフラグメントに割り当てられます。 そのフラグメントが一時的に使用できなくなると、Service Bus はエラーを返します。

以下に示すように、シナリオに応じて、さまざまなメッセージ プロパティがパーティション キーとして使用されます。

**SessionId**: メッセージで [BrokeredMessage.SessionId][BrokeredMessage.SessionId] プロパティが設定されている場合、Service Bus はこのプロパティをパーティション キーとして使用します。 このように、同じセッションに属するメッセージはすべて、同じメッセージ ブローカーによって処理されます。 この方法により、Service Bus はメッセージの順序と、セッション状態の整合性を保証することができます。

**PartitionKey**: メッセージで [BrokeredMessage.PartitionKey][BrokeredMessage.PartitionKey] プロパティは設定されているが、[BrokeredMessage.SessionId][BrokeredMessage.SessionId] プロパティは設定されていない場合、Service Bus は [PartitionKey][PartitionKey] プロパティをパーティション キーとして使用します。 メッセージで [SessionId][SessionId] プロパティと [PartitionKey][PartitionKey] プロパティが両方とも設定されている場合、この 2 つのプロパティの値は同じである必要があります。 [PartitionKey][PartitionKey] プロパティが [SessionId][SessionId] プロパティとは異なる値に設定されている場合、Service Bus は無効操作例外を返します。 セッションを認識しないトランザクション メッセージを送信側が送信する場合は、[PartitionKey][PartitionKey] プロパティを使用する必要があります。 パーティション キーを使用することにより、トランザクション内で送信されるすべてのメッセージを同じメッセージング ブローカーで処理することができます。

**MessageId**: キューまたはトピックで [QueueDescription.RequiresDuplicateDetection][QueueDescription.RequiresDuplicateDetection] プロパティが **true** に設定され、[BrokeredMessage.SessionId][BrokeredMessage.SessionId] プロパティまたは [BrokeredMessage.PartitionKey][BrokeredMessage.PartitionKey] プロパティが設定されていない場合は、[BrokeredMessage.MessageId][BrokeredMessage.MessageId] プロパティがパーティション キーとしての役割を果たします。 (送信元のアプリケーションでメッセージ ID が割り当てられていない場合は、Microsoft .NET および AMQP のライブラリによって自動的にメッセージ ID が割り当てられます)。この場合は、同じメッセージのすべてのコピーが同じメッセージ ブローカーによって処理されます。 これにより、Service Bus は重複したメッセージの検出と削除ができるようになります。 [QueueDescription.RequiresDuplicateDetection][QueueDescription.RequiresDuplicateDetection] プロパティが **true** に設定されていない場合、Service Bus は [MessageId][MessageId] プロパティをパーティション キーと見なしません。

### <a name="not-using-a-partition-key"></a>パーティション キーを使用しない場合
パーティション キーが存在しない場合、Service Bus は、ラウンドロビン方式で、パーティション分割されたキューまたはトピックのすべてのフラグメントにメッセージを配信します。 選択されたフラグメントが使用できない場合、Service Bus はメッセージを別のフラグメントに割り当てます。 このように、メッセージング ストアが一時的に使用できなくても送信操作は成功します。

Service Bus が別のフラグメントにメッセージをエンキューする時間を確保するには、メッセージの送信元のクライアントによって指定される [MessagingFactorySettings.OperationTimeout][MessagingFactorySettings.OperationTimeout] 値が 15 秒を超える必要があります。 [OperationTimeout][OperationTimeout] プロパティを既定値の 60 秒に設定することをお勧めします。

パーティション キーは、メッセージを特定のフラグメントに "ピン留め" します。 このフラグメントを保持しているメッセージング ストアを使用できない場合、Service Bus はエラーを返します。 パーティション キーが存在しない場合、Service Bus は異なるフラグメントを選択できるので、操作は成功します。 したがって、パーティション キーが必要でない場合は、パーティション キーを指定しないことをお勧めします。

## <a name="advanced-topics-use-transactions-with-partitioned-entities"></a>高度なトピック: パーティション分割されたエンティティでのトランザクションの使用
トランザクションの一部として送信されるメッセージでは、パーティション キーを指定する必要があります。 指定できるプロパティは、[BrokeredMessage.SessionId][BrokeredMessage.SessionId]、[BrokeredMessage.PartitionKey][BrokeredMessage.PartitionKey]、[BrokeredMessage.MessageId][BrokeredMessage.MessageId] のいずれかです。 同じトランザクションの一部として送信されるすべてのメッセージで、同じパーティション キーを指定する必要があります。 トランザクション内でパーティション キーなしのメッセージを送信しようとすると、Service Bus は無効操作例外を返します。 同じトランザクション内で異なるパーティション キーを持つ複数のメッセージを送信しようとすると、Service Bus は無効操作例外を返します。 For example:

```csharp
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    BrokeredMessage msg = new BrokeredMessage("This is a message");
    msg.PartitionKey = "myPartitionKey";
    messageSender.Send(msg); 
    ts.Complete();
}
committableTransaction.Commit();
```

パーティション キーとなるプロパティが設定されている場合、Service Bus はメッセージを特定のフラグメントに "ピン留め" します。 この動作は、トランザクションが使用されているかどうかにかかわらず起こります。 必要でない限り、パーティション キーを指定しないことをお勧めします。

## <a name="using-sessions-with-partitioned-entities"></a>パーティション分割されたエンティティでのセッションの使用
トランザクション メッセージを、セッションを認識するトピックまたはキューに送信するには、メッセージで [BrokeredMessage.SessionId][BrokeredMessage.SessionId] プロパティが設定されている必要があります。 [BrokeredMessage.PartitionKey][BrokeredMessage.PartitionKey] プロパティも指定されている場合は、このプロパティの値が [SessionId][SessionId] プロパティの値と同じである必要があります。 これらが異なる場合、Service Bus は無効操作例外を返します。

通常の (パーティション分割されていない) キューまたはトピックと異なり、単一のトランザクションを使用して複数のメッセージを別々のセッションに送信することはできません。 これを試みると、Service Bus は無効操作例外を返します。 For example:

```csharp
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    BrokeredMessage msg = new BrokeredMessage("This is a message");
    msg.SessionId = "mySession";
    messageSender.Send(msg); 
    ts.Complete();
}
committableTransaction.Commit();
```

## <a name="automatic-message-forwarding-with-partitioned-entities"></a>パーティション分割されたエンティティでのメッセージの自動転送
Service Bus では、パーティション分割されたエンティティを送信元または送信先とするメッセージの自動転送、およびパーティション分割されたエンティティ間でのメッセージの自動転送をサポートしています。 メッセージの自動転送を有効にするには、ソース キューまたはサブスクリプションで [QueueDescription.ForwardTo][QueueDescription.ForwardTo] プロパティを設定する必要があります。 メッセージでパーティション キー ([SessionId][SessionId]、[PartitionKey][PartitionKey]、[MessageId][MessageId]) が指定されている場合、そのパーティション キーが出力先エンティティで使用されます。

## <a name="considerations-and-guidelines"></a>考慮事項とガイドライン
* **高い整合性機能**: パーティション キーの明示的制御、重複データ検出、セッションといった機能が使用されるエンティティの場合、メッセージング操作は常に特定のフラグメントにルーティングされます。 いずれかのフラグメントにトラフィックが集中した場合や、基になるストアに異常が生じた場合、それらの操作は失敗し、可用性が低下します。 それでも全体として堅牢性は、パーティション分割されていないエンティティと比べれば、はるかに高くなります。問題が発生するのはトラフィックの一部だけです。すべてのトラフィックで問題が発生するわけではありません。
* **管理**: 作成、更新、削除といった操作は、エンティティのすべてのフラグメントに対して実行する必要があります。 異常のあるフラグメントが 1 つでもあると、それらの操作は失敗します。 Get 操作に関して言えば、メッセージ数などの情報は、全フラグメントから集計する必要があります。 いずれかのフラグメントに異常があった場合、そのエンティティの可用性ステータスは "制限あり" として報告されます。
* **メッセージ ボリュームの削減に伴うシナリオ**: 特に HTTP プロトコルを使用している場合は、すべてのメッセージを取得するために、複数の受信操作を実行しなければならないことがあります。 受信要求の場合、フロント エンドは、すべてのフラグメントを受信し、返されたすべての応答をキャッシュします。 以降、同じ接続上で行われる受信要求でこのキャッシュを利用できるため、受信で発生する遅延は小さくなります。 ただし複数の接続が存在する場合や、HTTP を使用している場合は、要求ごとに新しい接続が確立されます。 そのため、要求元と同じノードに応答が届く保証はありません。 既存のメッセージがすべてロックされ、別のフロント エンドでキャッシュされている場合は、受信操作から **null**が返されます。 最終的にはメッセージの有効期限が切れ、再度受信できる状態になります。 HTTP キープアライブの使用をお勧めします。
* **メッセージの参照/ピーク**: [PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatch_System_Int32_) は、必ずしも [MessageCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription#Microsoft_ServiceBus_Messaging_QueueDescription_MessageCount) プロパティに指定された数のメッセージを返すとは限りません。 一般に、これには 2 つの理由があります。 1 つ目は、メッセージのコレクションの総サイズが、最大サイズである 256 KB を超えていることです。 2 つ目は、キューまたはトピックの [EnablePartitioning プロパティ](/dotnet/api/microsoft.servicebus.messaging.queuedescription#Microsoft_ServiceBus_Messaging_QueueDescription_EnablePartitioning)が **true** に設定されている場合に、要求されたメッセージ数を満たすだけのメッセージがパーティションにない可能性があります。 通常、アプリケーションは、決まった数のメッセージを受信する必要がある場合、そのメッセージ数に達するまで、または読み取るメッセージがなくなるまで、[PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatch_System_Int32_) を繰り返し呼び出す必要があります。 コード サンプルを含む詳細については、[QueueClient.PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatch_System_Int32_) または [SubscriptionClient.PeekBatch](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient#Microsoft_ServiceBus_Messaging_SubscriptionClient_PeekBatch_System_Int32_) に関する記事をご覧ください。

## <a name="latest-added-features"></a>最近追加された機能
* パーティション分割エンティティで、ルールの追加と削除がサポートされました。 通常の (パーティション分割されていない) エンティティとは異なり、トランザクションでは、これらの操作がサポートされません。 
* パーティション分割されたエンティティとの間で行われるメッセージの送受信で AMQP がサポートされます。
* 今後は、[Batch Send](/dotnet/api/microsoft.servicebus.messaging.queueclient#Microsoft_ServiceBus_Messaging_QueueClient_SendBatch_System_Collections_Generic_IEnumerable_Microsoft_ServiceBus_Messaging_BrokeredMessage__)、[Batch Receive](/dotnet/api/microsoft.servicebus.messaging.queueclient#Microsoft_ServiceBus_Messaging_QueueClient_ReceiveBatch_System_Int32_)、[Receive by Sequence Number](/dotnet/api/microsoft.servicebus.messaging.queueclient#Microsoft_ServiceBus_Messaging_QueueClient_Receive_System_Int64_)、[Peek](/dotnet/api/microsoft.servicebus.messaging.queueclient#Microsoft_ServiceBus_Messaging_QueueClient_Peek)、[Renew Lock](/dotnet/api/microsoft.servicebus.messaging.queueclient#Microsoft_ServiceBus_Messaging_QueueClient_RenewMessageLock_System_Guid_)、[Schedule Message](/dotnet/api/microsoft.servicebus.messaging.queueclient#Microsoft_ServiceBus_Messaging_QueueClient_ScheduleMessageAsync_Microsoft_ServiceBus_Messaging_BrokeredMessage_System_DateTimeOffset_)、[Cancel Scheduled Message](/dotnet/api/microsoft.servicebus.messaging.queueclient#Microsoft_ServiceBus_Messaging_QueueClient_CancelScheduledMessageAsync_System_Int64_)、[Add Rule](/dotnet/api/microsoft.servicebus.messaging.ruledescription)、[Remove Rule](/dotnet/api/microsoft.servicebus.messaging.ruledescription)、[Session Renew Lock](/dotnet/api/microsoft.servicebus.messaging.messagesession#Microsoft_ServiceBus_Messaging_MessageSession_RenewLock)、[Set Session State](/dotnet/api/microsoft.servicebus.messaging.messagesession#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_)、[Get Session State](/dotnet/api/microsoft.servicebus.messaging.messagesession#Microsoft_ServiceBus_Messaging_MessageSession_GetState)、[Enumerate Sessions](/dotnet/api/microsoft.servicebus.messaging.queueclient#Microsoft_ServiceBus_Messaging_QueueClient_GetMessageSessionsAsync) の各操作で AMQP がサポートされます。

## <a name="partitioned-entities-limitations"></a>パーティション分割されたエンティティの制限事項
現在、Service Bus は、パーティション分割されたキューまたはトピックに以下の制限を適用します。

* パーティション分割されたキューまたはトピックは、単一のトランザクションの別々のセッションに属するメッセージの送信はサポートしていません。
* Service Bus は、現在、名前空間あたり最大 100 のパーティション分割されたキューまたはトピックをサポートします。 パーティション分割された各キューまたはトピックは、名前空間あたり 10,000 エンティティのクォータに対してカウントされます (Premium レベルには適用されません)。

## <a name="next-steps"></a>次のステップ
メッセージング エンティティのパーティション分割について詳しくは、「[パーティション分割された Service Bus のキューとトピックにおける AMQP 1.0 のサポート][AMQP 1.0 support for Service Bus partitioned queues and topics]」の説明をご覧ください。 

[Service Bus architecture]: service-bus-architecture.md
[Azure portal]: https://portal.azure.com
[QueueDescription.EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.queuedescription#Microsoft_ServiceBus_Messaging_QueueDescription_EnablePartitioning
[TopicDescription.EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.topicdescription#Microsoft_ServiceBus_Messaging_TopicDescription_EnablePartitioning
[BrokeredMessage.SessionId]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_SessionId
[BrokeredMessage.PartitionKey]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_PartitionKey
[SessionId]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_SessionId
[PartitionKey]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_PartitionKey
[QueueDescription.RequiresDuplicateDetection]: /dotnet/api/microsoft.servicebus.messaging.queuedescription#Microsoft_ServiceBus_Messaging_QueueDescription_RequiresDuplicateDetection
[BrokeredMessage.MessageId]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId
[MessageId]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId
[MessagingFactorySettings.OperationTimeout]: /dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings#Microsoft_ServiceBus_Messaging_MessagingFactorySettings_OperationTimeout
[OperationTimeout]: /dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings#Microsoft_ServiceBus_Messaging_MessagingFactorySettings_OperationTimeout
[QueueDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.queuedescription#Microsoft_ServiceBus_Messaging_QueueDescription_ForwardTo
[AMQP 1.0 support for Service Bus partitioned queues and topics]: service-bus-partitioned-queues-and-topics-amqp-overview.md

