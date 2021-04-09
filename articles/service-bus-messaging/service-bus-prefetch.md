---
title: Azure Service Bus プリフェッチ メッセージ | Microsoft Docs
description: Azure Service Bus メッセージをプリフェッチすることでパフォーマンスを向上します。 メッセージは、アプリケーションが要求する前に、ローカルですぐに取得できるようになっています。
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 05e23b0590f0c04171efda8fb561b4c2664ed096
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "85341054"
---
# <a name="prefetch-azure-service-bus-messages"></a>Azure Service Bus メッセージのプリフェッチ

公式の Service Bus クライアントで *プリフェッチ* を有効にしていると、受信側は、アプリケーションが元々要求した数を超えて [PrefetchCount](/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount#Microsoft_Azure_ServiceBus_QueueClient_PrefetchCount) 限界に達するまでメッセージを自動的に取得します。

このため、[Receive](/dotnet/api/microsoft.servicebus.messaging.queueclient.receive) または [ReceiveAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receiveasync) を 1 回呼び出すと、使用の準備ができると同時にメッセージが返され、すぐに使用できます。 クライアントは、プリフェッチ バッファーが満杯になるまで、バック グラウンドでメッセージを取得し続けます。

## <a name="enable-prefetch"></a>プリフェッチの有効化

.NET では、**MessageReceiver**、**QueueClient** または **SubscriptionClient** の [PrefetchCount](/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount#Microsoft_Azure_ServiceBus_QueueClient_PrefetchCount) のプロパティを 0 より大きい数値に設定することでプリフェッチ機能を有効化します。 値を 0 に設定すると、プリフェッチが解除されます。

この設定は、[QueuesGettingStarted](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/QueuesGettingStarted) または [ReceiveLoop](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/ReceiveLoop) サンプルの設定の受信側に簡単に追加することができ、これらのコンテキストでの効果を確認できます。

メッセージがプリフェッチ バッファーにある間は、その後の **Receive**/**ReceiveAsync** 呼び出しはバッファーから満たされ、スペースが空くとバックグラウンドでバッファに補充されます。 配信するメッセージがない場合は、受信操作によってバッファーが空になり、想定どおり、ブロックを待機します。

プリフェッチも、[OnMessage](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage) および [OnMessageAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessageasync) API で同様に機能します。

## <a name="if-it-is-faster-why-is-prefetch-not-the-default-option"></a>プリフェッチの方が速度が速いのに既定のオプションではない理由

プリフェッチは、アプリケーションが要求する前にローカルで取得できるようメッセージを用意することによって、メッセージ フローを高速化します。 このスループットの向上は、以下のメリットとデメリットを比較したうえで、アプリケーションの作成者がその使用を明示的に決定した結果として得られるものです。

[ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) 受信モードでは、プリフェッチ バッファーに取得されたすべてのメッセージはキューで使用できなくなり、**Receive**/**ReceiveAsync** または **OnMessage**/**OnMessageAsync** API を通して アプリケーションで受信されるまで、メモリ内のプリフェッチ バッファーにのみ存在します。 アプリケーションがメッセージを受信する前に終了すると、それらのメッセージは失われ、復元できなくなります。

[PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock) 受信モードでは、メッセージはロック状態のプリフェッチ バッファーにフェッチされ、ロックの有効期限のためのタイムアウト クロックが設定されます。 プリフェッチ バッファーのサイズが大きく、処理に時間がかかるため、メッセージがプリフェッチ バッファー内にある間、あるいはアプリケーションがメッセージを処理している間にメッセージのロックの有効期限が切れると、アプリケーションが処理について混乱するイベントが発生する可能性があります。

アプリケーションは、期限が切れた、またはロックの有効期限がもうすぐ切れるメッセージを取得する場合があります。 この場合、アプリケーションはメッセージを処理する可能性がありますが、ロックの有効期限のために完了できません。 アプリケーションは [LockedUntilUtc](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.lockeduntilutc) プロパティ (ブローカーとローカル コンピューターのクロックのずれによって規定されます) をチェックします。 メッセージのロックの有効期限が切れている場合は、アプリケーションはメッセージを無視する必要があり、メッセージに対する、またはメッセージによる API 呼び出しは行われません。 メッセージの期限が切れていないが有効期限が近づいている場合は、[message.RenewLock()](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_RenewLockAsync_System_String_) を呼び出すことにより、既定のロック期間 1 回分までロックを更新および拡張できます。

プリフェッチ バッファーでロックの有効期限が自動的に切れると、メッセージは破棄済みとして扱われ、もう一度キューから取得できるようになります。 それにより、プリフェッチ バッファーにフェッチされ、末尾に配置される場合があります。 メッセージの有効期限中、プリフェッチ バッファーが通常通り処理されない場合は、メッセージのプリフェッチが繰り返されますが、使用可能な (ロックが有効) 状態で効果的に配信されることはなく、最終的には、最大配信回数を超えて配信不能キューに入れられます。

高い信頼性でメッセージを処理する必要がある場合は、処理に工数と時間がかかるため、プリフェッチ機能をまったく使用しないか、慎重に使用することをお勧めします。

高いスループットを必要としており、メッセージ処理が一般に安価である場合は、プリフェッチによって大きなスループットの利点がもたらされます。

最大プリフェッチ数とキューまたはサブスクリプションに構成されているロック期間は、ロックのタイムアウトが、プリフェッチ バッファーの最大サイズに 1 メッセージ分を足した累計予想メッセージ処理時間以上になるようにバランスを調整する必要があります。 同時に、ロック タイムアウトは、メッセージが誤って破棄された場合に、再配信される前にロックの有効期限が切れないよう、その最大 [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) を超えない長さである必要があります。

## <a name="next-steps"></a>次のステップ

Service Bus メッセージングの詳細については、次のトピックをご覧ください。

* [Service Bus のキュー、トピック、サブスクリプション](service-bus-queues-topics-subscriptions.md)
* [Service Bus キューの使用](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus のトピックとサブスクリプションの使用方法](service-bus-dotnet-how-to-use-topics-subscriptions.md)
