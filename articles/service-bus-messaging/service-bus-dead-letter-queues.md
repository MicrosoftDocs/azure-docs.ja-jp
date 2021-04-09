---
title: Service Bus の配信不能キュー | Microsoft Docs
description: Service Bus の配信不能キューについて説明します。 Service Bus キューおよびトピック サブスクリプションでは、配信不能キューと呼ばれるセカンダリ サブキューが提供されます。
ms.topic: article
ms.date: 06/23/2020
ms.custom: fasttrack-edit, devx-track-csharp
ms.openlocfilehash: ad62f946584071e7ce6fd55f48b5f7ee8db44a2f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98630100"
---
# <a name="overview-of-service-bus-dead-letter-queues"></a>Service Bus の配信不能キューの概要

Azure Service Bus キューおよびトピック サブスクリプションでは、*配信不能キュー* (DLQ) と呼ばれるセカンダリ サブキューが提供されます。 配信不能キューを明示的に作成する必要はなく、削除したり、メイン エンティティとは別に管理したりすることはできません。

この記事では、Service Bus の配信不能キューについて説明します。 説明の多くは、GitHub の[配信不能キューのサンプル](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/DeadletterQueue)に示されています。
 
## <a name="the-dead-letter-queue"></a>配信不能キュー

配信不能キューの目的は、受信者に配信できないメッセージ、または処理できなかったメッセージを保持することです。 したがって、メッセージを DLQ から取り出して、検査することができます。 アプリケーションは演算子を利用して、問題を修正してメッセージを再送信し、エラーが発生していたという事実をログに記録してから修正処置を行います。 

API とプロトコルの観点では、DLQ は他のキューとほとんど同じですが、メッセージを再送信できるのは親エンティティの配信不能操作でのみである点が異なります。 また、有効期間は監視されず、DLQ のメッセージを配信不能にすることはできません。 配信不能キューでは、ピーク ロック配信やトランザクション操作が完全にサポートされます。

DLQ は自動的にクリーンアップされません。 DLQ から明示的に取得し、配信不能メッセージに対して [Complete()](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) を呼び出すまで、メッセージは DLQ に残ります。

## <a name="dlq-message-count"></a>DLQ のメッセージ数
配信不能キュー内のメッセージの数をトピック レベルで取得することはできません。 これは、Service Bus が内部エラーをスローしない限り、メッセージはトピック レベルに存在しないためです。 送信者がトピックにメッセージを送信すると、メッセージはミリ秒以内にトピックのサブスクリプションに転送されるため、トピック レベルには存在しなくなります。 したがって、トピックのサブスクリプションに関連付けられている DLQ にメッセージが表示されます。 次の例では、**Service Bus Explorer** で、サブスクリプション "test1" の DLQ に現在 62 件のメッセージがあることが示されています。 

![DLQ のメッセージ数](./media/service-bus-dead-letter-queues/dead-letter-queue-message-count.png)

Azure CLI コマンド [`az servicebus topic subscription show`](/cli/azure/servicebus/topic/subscription#az-servicebus-topic-subscription-show) を使用して、DLQ のメッセージ数を取得することもできます。 

## <a name="moving-messages-to-the-dlq"></a>DLQ にメッセージを移動する

Service Bus には、メッセージがメッセージング エンジン自体から DLQ にプッシュされる原因となるアクティビティがいくつかあります。 アプリケーションは明示的にメッセージを DLQ に移動することもできます。 

ブローカーによってメッセージが移動され、ブローカーがメッセージに対して内部バージョンの [DeadLetter](/dotnet/api/microsoft.azure.servicebus.queueclient.deadletterasync) メソッドを呼び出すと、`DeadLetterReason` および `DeadLetterErrorDescription` という 2 つのプロパティが追加されます。

アプリケーションは `DeadLetterReason` プロパティに対して独自のコードを定義できますが、システムでは以下の値が設定されます。

| DeadLetterReason | DeadLetterErrorDescription |
| --- | --- |
|HeaderSizeExceeded |このストリームのサイズ クォータを超えています。 |
|TTLExpiredException |メッセージの有効期限が切れているため、配信不能です。 詳細については、「[TimeToLive の超過](#exceeding-timetolive)」セクションを参照してください。 |
|Session ID is null. (セッション ID は Null です。) |セッションが有効なエンティティではセッション ID が Null のメッセージは許可されません。 |
|MaxTransferHopCountExceeded | キュー間で転送するときに許容される最大ホップ数。 値は 4 に設定されています。 |
| MaxDeliveryCountExceededExceptionMessage | 最大数の配信試行後、メッセージを使用できませんでした。 詳細については、「[MaxDeliveryCount の超過](#exceeding-maxdeliverycount)」セクションを参照してください。 |

## <a name="exceeding-maxdeliverycount"></a>MaxDeliveryCount の超過

キューおよびサブスクリプションにはそれぞれ [QueueDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) および [SubscriptionDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.maxdeliverycount) プロパティがあり、既定値は 10 です。 メッセージがロック状態で配信されたが ([ReceiveMode.PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode))、明示的に破棄されたかロックが期限切れになった場合は、メッセージの [BrokeredMessage.DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) が増えます。 [DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) が [MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) を超えると、メッセージは DLQ に移動にされ、`MaxDeliveryCountExceeded` 理由コードが示されます。

この動作を無効にすることはできませんが、[MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) を大きい数に設定することはできます。

## <a name="exceeding-timetolive"></a>TimeToLive の超過

[QueueDescription.EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.queuedescription) または [SubscriptionDescription.EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription) プロパティが **true** に設定されている場合 (既定値は **false**)、期限が切れるメッセージはすべて DLQ に移動され、`TTLExpiredException` 理由コードが示されます。

メイン キューまたはサブスクリプションに対してプルを実行しているアクティブな受信者が少なくとも 1 つある場合は、期限切れメッセージは単に消去され、DLQ に移動されるだけです。また、[遅延メッセージ](./message-deferral.md)は有効期限が切れた後に消去されず、配信不能キューに移動されません。 これらの動作は仕様によるものです。

## <a name="errors-while-processing-subscription-rules"></a>サブスクリプション ルールの処理中のエラー

[SubscriptionDescription.EnableDeadLetteringOnFilterEvaluationExceptions](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription) プロパティがサブスクリプションに対して有効になっている場合、サブスクリプションの SQL フィルター ルールの実行時に発生したエラーはすべて問題のメッセージと共に DLQ にキャプチャされます。 このオプションは、すべてのメッセージ型がサブスクライバーを持つわけではない運用環境では使用しないでください。

## <a name="application-level-dead-lettering"></a>アプリケーション レベルの配信不能処理

システム指定の配信不能処理機能に加え、アプリケーションでは DLQ を使用して許容できないメッセージを明示的に拒否できます。 それらには、何らかのシステムの問題により適切に処理できないメッセージ、誤った形式のペイロードを保持するメッセージ、メッセージ レベルのセキュリティ スキームの使用時に認証に失敗したメッセージなどがあります。

## <a name="dead-lettering-in-forwardto-or-sendvia-scenarios"></a>ForwardTo または SendVia での配信不能シナリオ

メッセージは、次の条件に該当するときに転送不能キューに送信されます。

- メッセージが、[連結](service-bus-auto-forwarding.md)されている 5 つ以上のキューまたはトピックを通過する。
- 送信先キューまたはトピックが無効または削除されている。
- 送信先キューまたはトピックがエンティティの最大サイズを超えている。

これらの配信不能メッセージを取得するために、[FormatTransferDeadletterPath](/dotnet/api/microsoft.azure.servicebus.entitynamehelper.formattransferdeadletterpath) ユーティリティ メソッドを使用して受信者を作成することができます。

## <a name="example"></a>例

次のコード スニペットではメッセージの受信者を作成します。 メイン キューの受信ループで、コードは [Receive(TimeSpan.Zero)](/dotnet/api/microsoft.servicebus.messaging.messagereceiver) のメッセージを取得します。この場合、ブローカーはすぐに使用できるメッセージをすぐに返すか、結果なしで返すように求められます。 コードはメッセージを受信すると、すぐに破棄し、`DeliveryCount` が増えます。 システムが DLQ にメッセージを移動すると、メイン キューは空になり、ループが終了し、[ReceiveAsync](/dotnet/api/microsoft.servicebus.messaging.messagereceiver) は **null** を返します。

```csharp
var receiver = await receiverFactory.CreateMessageReceiverAsync(queueName, ReceiveMode.PeekLock);
while(true)
{
    var msg = await receiver.ReceiveAsync(TimeSpan.Zero);
    if (msg != null)
    {
        Console.WriteLine("Picked up message; DeliveryCount {0}", msg.DeliveryCount);
        await msg.AbandonAsync();
    }
    else
    {
        break;
    }
}
```

## <a name="path-to-the-dead-letter-queue"></a>配信不能キューへのパス
配信不能キューにアクセスするには、次の構文を使用します。

```
<queue path>/$deadletterqueue
<topic path>/Subscriptions/<subscription path>/$deadletterqueue
```

.NET SDK を使用している場合、配信不能キューへのパスを取得するには、SubscriptionClient.FormatDeadLetterPath() メソッドを使用します。 このメソッドは、 **/$DeadLetterQueue** を使用して、トピック名、サブスクリプション名、サフィックスを指定します。


## <a name="next-steps"></a>次のステップ

Service Bus キューの詳細については、次の記事を参照してください。

* [Service Bus キューの使用](service-bus-dotnet-get-started-with-queues.md)
* [Azure キューと Service Bus キューの比較](service-bus-azure-and-service-bus-queues-compared-contrasted.md)

