---
title: Azure Service Bus - メッセージ遅延
description: この記事では、Azure Service Bus メッセージの配信を遅延する方法について説明します。 メッセージは、キューまたはサブスクリプションに留まり、確保されます。
ms.topic: article
ms.date: 06/23/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: e3a940f8aa9e72d9b09e9c0a3305521c6f17dfb0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98622047"
---
# <a name="message-deferral"></a>メッセージ遅延

キューまたはサブスクリプションのクライアントが処理すべきメッセージを受信したものの、アプリケーション内の特別な状況が原因ですぐに処理を行えない場合、メッセージを取得するタイミングを "遅延" させることができます。 メッセージは、キューまたはサブスクリプションに留まり、確保されます。

遅延は、ワークフロー処理のシナリオを対象に作成された機能です。 ワークフロー フレームワークでは、一定の操作を特定の順序で処理することが必要な場合があります。また、他のメッセージで通知された、優先度の高い所定の作業が完了するまで、受信したいくつかのメッセージの処理を延期することが必要な場合があります。

簡単でわかりやすい例としては、一致する注文書が店舗からフルフィルメント システムに入力される前に、外部の支払いプロバイダーからの支払い通知がシステムに表示される注文処理シーケンスが挙げられます。 このケースでは、関連する注文が届くまで、支払い通知の処理がフルフィルメント システムによって遅延される場合があります。 異なる送信元からのメッセージによってワークフローが進行する集合シナリオでは、リアルタイムの実行順序は正しいのに、結果を反映するメッセージが到着する順序は正しくない場合があります。

つまり遅延は、処理の延期が必要なメッセージをメッセージ ストアに確実に保持しながら、到着した順序から処理できる順序に並べ直すのに役立ちます。

> [!NOTE]
> 遅延メッセージは、[有効期限が切れた後](./service-bus-dead-letter-queues.md#exceeding-timetolive)に、配信不能キューに自動的に移動されません。 この動作は仕様によるものです。

## <a name="message-deferral-apis"></a>メッセージ遅延 API

API は、.NET Framework クライアントの [BrokeredMessage.Defer](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.defer#Microsoft_ServiceBus_Messaging_BrokeredMessage_Defer) または [BrokeredMessage.DeferAsync](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.deferasync#Microsoft_ServiceBus_Messaging_BrokeredMessage_DeferAsync)、.NET Standard クライアントの [MessageReceiver.DeferAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deferasync)、Java クライアントの [IMessageReceiver.defer](/java/api/com.microsoft.azure.servicebus.imessagereceiver.defer) または [IMessageReceiver.deferAsync](/java/api/com.microsoft.azure.servicebus.imessagereceiver.deferasync) です。 

遅延メッセージは、すべての他のアクティブなメッセージと共にメイン キューに留まります (サブキューに含まれる配達不能メッセージとは異なります)。ただし、これらの遅延メッセージは、通常の Receive/ReceiveAsync 関数を使用して受信することができなくなります。 アプリケーションが遅延メッセージを追跡できなくなった場合、[メッセージ参照](message-browsing.md)を使用して検出できます。

遅延メッセージを取得するには、所有者がそれを遅延させる際に [SequenceNumber](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.sequencenumber#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_SequenceNumber) を記憶しておく必要があります。 遅延メッセージのシーケンス番号がわかっている受信者は、後ほど `Receive(sequenceNumber)` で明示的にメッセージを受信できます。

メッセージを処理する特定のリソースが一時的に使用できないためにそのメッセージを処理できないものの、メッセージの処理をすぐに中断できない場合があります。このような場合、数分間そのメッセージを確保する方法は、数分以内に投稿される [スケジュール済みメッセージ](message-sequencing.md)の **SequenceNumber** を記憶しておき、スケジュールされたメッセージが到着したときに遅延メッセージを取得し直すことです。 メッセージ ハンドラーですべての操作についてデータベースが使用されており、そのデータベースが一時的に使用できない場合は、遅延を使用しないようにしてください。その代わり、データベースが再び使用できるようになるまで、メッセージの受信を完全に中断してください。


## <a name="next-steps"></a>次のステップ

Service Bus メッセージングの詳細については、次のトピックをご覧ください。

* [Service Bus のキュー、トピック、サブスクリプション](service-bus-queues-topics-subscriptions.md)
* [Service Bus キューの使用](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus のトピックとサブスクリプションの使用方法](service-bus-dotnet-how-to-use-topics-subscriptions.md)
