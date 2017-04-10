---
title: "Azure Service Bus で処理されるトランザクションの概要 | Microsoft Docs"
description: "Azure Service Bus のアトミック トランザクションと経由送信の概要"
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 64449247-1026-44ba-b15a-9610f9385ed8
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: clemensv;sethm
translationtype: Human Translation
ms.sourcegitcommit: c39abad6c5e2a9e2ae7add9ecda48783f61bc736
ms.openlocfilehash: 8d0f3818831a22550fb0eea9bcbc1f62b133003a
ms.lasthandoff: 02/03/2017


---
# <a name="overview-of-service-bus-transaction-processing"></a>Service Bus のトランザクション処理の概要
この記事では、Azure Service Bus のトランザクション機能について説明します。 説明の多くは、[Service Bus を使用したアトミック トランザクションのサンプル](https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/AtomicTransactions)に示されています。 この記事はトランザクション処理の概要と Service Bus の "*経由送信*" 機能に限定されていますが、アトミック トランザクションのサンプルの範囲はこれよりも広く複雑です。

## <a name="transactions-in-service-bus"></a>Service Bus でのトランザクション
[トランザクション](https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/AtomicTransactions#what-are-transactions)により、2 つ以上の操作が&1; つの*実行スコープ*にグループ化されます。 性質上、このようなトランザクションによって、操作の特定のグループに属する操作がすべて成功するか、すべて失敗するかのいずれかになる必要があります。 この点において、トランザクションは&1; つの単位として動作します。このことは、多くの場合 "*原子性* "と呼ばれます。 

Service Bus はトランザクション メッセージ ブローカーであり、そのメッセージ ストアに対するすべての内部操作のトランザクション整合性を確保します。 [配信不能キュー](service-bus-dead-letter-queues.md)へのメッセージの移動や、エンティティ間のメッセージの [自動転送](service-bus-auto-forwarding.md) などの、すべての Service Bus 内部のメッセージ転送は、トランザクショナルです。 そのため、Service Bus がメッセージを受け入れる場合、メッセージは既に格納されて、シーケンス番号のラベルが付けられています。 それ以降、Service Bus 内のメッセージの転送はエンティティ間で連動する操作となり、メッセージが失われる (ソースが成功し、ターゲットが失敗する) ことも、重複する (ソースが失敗し、ターゲットが成功する) こともありません。

Service Bus は、トランザクションのスコープ内の単一メッセージング エンティティ (キュー、トピック、サブスクリプション) に対するグループ化操作をサポートしています。 たとえば、トランザクション スコープ内から&1; つのキューにいくつかのメッセージを送信できます。また、トランザクションが正常に完了したときにのみ、メッセージがキューのログにコミットされます。

## <a name="operations-within-a-transaction-scope"></a>トランザクション スコープ内での操作
トランザクション スコープ内で実行できる操作は次のとおりです。

* **[QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient)、[MessageSender](/dotnet/api/microsoft.servicebus.messaging.messagesender)、[TopicClient](/dotnet/api/microsoft.servicebus.messaging.topicclient)**: Send、SendAsync、SendBatch、SendBatchAsync 
* **[BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)**: Complete、CompleteAsync、Abandon、AbandonAsync、Deadletter、DeadletterAsync、Defer、DeferAsync、RenewLock、RenewLockAsync 

一部の受信ループ内、または [OnMessage](/dotnet/api/microsoft.servicebus.messaging.messagereceiver#Microsoft_ServiceBus_Messaging_MessageReceiver_OnMessage_System_Action_Microsoft_ServiceBus_Messaging_BrokeredMessage__Microsoft_ServiceBus_Messaging_OnMessageOptions_) コールバックで、[ReceiveMode.PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode) モードを使用してアプリケーションがメッセージを取得した後にのみ、メッセージを処理するためのトランザクション スコープを開くことを前提としているため、受信操作は含まれません。

その後、メッセージの処理 (完了、破棄、配信不能、延期) がトランザクションのスコープ内で発生します。この処理の発生は、トランザクションの全体的な結果に依存します。

## <a name="transfers-and-send-via"></a>転送および "経由送信"
キューからプロセッサ、そこから別のキューへのデータのトランザクションの移行を有効にするために、Service Bus は "*転送*" をサポートしています。 転送操作では、送信者がまず "転送キュー" にメッセージを送信すると、転送キューが、自動転送機能で使用されているものと同じ堅牢な転送実装を使用して、すぐに目的の送信先キューにメッセージを移動します。 メッセージは、転送キューのコンシューマーに表示されるようになる方法で、転送キューのログにコミットされることはありません。

転送キュー自体が送信者の入力メッセージの送信元である場合は、このトランザクションの機能の能力が明らかになります。 つまり、Service Bus は、入力メッセージの完了 (または延期や配信不能) 操作を実行しながら、転送キューを "経由" してメッセージを送信先キューに転送できる、オールインワンのアトミック操作です。 

### <a name="see-it-in-code"></a>コードでの確認
このような転送を設定するには、転送キューを経由して送信先キューを対象とするメッセージの送信者を作成します。 同じキューからメッセージをプルする受信者も必要です。 次に例を示します。

```csharp
var sender = this.messagingFactory.CreateMessageSender(destinationQueue, myQueueName);
var receiver = this.messagingFactory.CreateMessageReceiver(myQueueName);
```

その後、単純なトランザクションで、次の例のように、これらの要素を使用します。

```csharp
var msg = receiver.Receive();

using (scope = new TransactionScope())
{
    // Do whatever work is required 

    var newmsg = ... // package the result 

    msg.Complete(); // mark the message as done
    sender.Send(newmsg); // forward the result

    scope.Complete(); // declare the transaction done
} 
```

## <a name="next-steps"></a>次のステップ

Service Bus キューの詳細については、次の記事を参照してください。

* [自動転送を使用した Service Bus エンティティのチェーン](service-bus-auto-forwarding.md)
* [自動転送のサンプル](https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/AutoForward)
* [Service Bus を使用したアトミック トランザクションのサンプル](https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/AtomicTransactions)
* [Azure キューと Service Bus キューの比較](service-bus-azure-and-service-bus-queues-compared-contrasted.md)
* [Service Bus キューの使用方法](service-bus-dotnet-get-started-with-queues.md)


