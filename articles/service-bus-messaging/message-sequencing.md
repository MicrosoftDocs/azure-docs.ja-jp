---
title: Azure Service Bus のメッセージのシーケンス処理とタイムスタンプ | Microsoft Docs
description: この記事では、Azure Service Bus のメッセージのシーケンス処理と (タイムスタンプによる) 順序付けを維持する方法について説明します。
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: fdb18802e576ad114fd3f783d5efd7bb826a5f94
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "85341179"
---
# <a name="message-sequencing-and-timestamps"></a>メッセージのシーケンス処理とタイムスタンプ

シーケンス処理とタイムスタンプは、受信または参照するメッセージの [SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber) プロパティおよび [EnqueuedTimeUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc) プロパティを通じて Service Bus のすべてのエンティティとサーフェスで常に利用できる 2 つの機能です。

メッセージの絶対的な順序が重要なケースや、コンシューマーが信頼できるメッセージの一意識別子を必要とするケースでは、ブローカーは、キューまたはトピックを基準とした、ギャップのない増分するシーケンス番号をメッセージに付けます。 パーティション分割されたエンティティの場合、シーケンス番号はパーティションを基準として発行されます。

**SequenceNumber** の値は、メッセージがブローカーおよび関数によって受け入れおよび格納されるときに内部識別子として割り当てられる 64 ビットの整数です。 パーティション分割されたエンティティの場合、最上位の 16 ビットはパーティション識別子を表します。 48/64 ビット範囲が使い果たされると、シーケンス番号は 0 にロールオーバーされます。

シーケンス番号はクライアントではなく中立的な中央機関によって割り当てられるため、一意識別子として信頼できます。 また、この番号は真の到着順序を表しており、順序の基準としてはタイムスタンプよりも正確です。なぜなら、タイムスタンプは極端に高いメッセージ送信率での十分な分解能がなく、ブローカーの所有権がノード間で移動する状況では (最小限ではありますが) 時間のずれが生じる可能性があるからです。

絶対的な到着順序は、たとえば限定数の商品を先着順で提供するビジネス シナリオ (コンサート チケットの販売など) で最後の商品を提供する際に重要となります。

タイムスタンプ機能は中立的で信頼できる機関として機能し、**EnqueuedTimeUtc** プロパティで示される、メッセージが到着した UTC 時刻を正確にキャプチャします。 この値は、ビジネス シナリオが期限に依存する場合 (たとえば、作業項目が特定の日付の午前 0 時より前に送信されたが、キューのバックログでかなり遅れて処理される場合など) に役立ちます。

## <a name="scheduled-messages"></a>スケジュール設定されたメッセージ

メッセージを、遅延処理されるようにキューまたはトピックに送信できます。たとえば、特定の時刻にシステムによって処理可能になるようにジョブをスケジュール設定できます。 この機能により、分散された時間ベースのスケジューラが実現します。

スケジュール設定されたメッセージは、定義されたエンキュー時刻まではキューで具体化されません。 その時刻より前であれば、スケジュール設定されたメッセージをキャンセルできます。 キャンセルすると、メッセージが削除されます。

メッセージをスケジュール設定するには、通常の送信パス経由でメッセージを送信するときに [ScheduledEnqueueTimeUtc](/dotnet/api/microsoft.azure.servicebus.message.scheduledenqueuetimeutc) プロパティを設定するか、[ScheduleMessageAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync#Microsoft_Azure_ServiceBus_QueueClient_ScheduleMessageAsync_Microsoft_Azure_ServiceBus_Message_System_DateTimeOffset_) API で明示的に設定します。 後者の場合、スケジュール設定されたメッセージの **SequenceNumber** がただちに返されます。必要な場合はこれを後で使用して、スケジュール設定されたメッセージをキャンセルできます。 スケジュール設定されたメッセージとそのシーケンス番号を、[メッセージのブラウズ](message-browsing.md)を使用して調べることもできます。

スケジュール設定されたメッセージの **SequenceNumber** は、メッセージがこの状態にある間のみ有効です。 アクティブ状態に移行すると、メッセージは即時にエンキューされた場合のようにキューに追加され、新しい **SequenceNumber** を割り当てられます。

この機能は個別のメッセージにアンカーされており、メッセージは 1 回しかエンキューできないため、Service Bus はメッセージの定期的なスケジュールをサポートしていません。

## <a name="next-steps"></a>次のステップ

Service Bus メッセージングの詳細については、次のトピックをご覧ください。

* [Service Bus のキュー、トピック、サブスクリプション](service-bus-queues-topics-subscriptions.md)
* [Service Bus キューの使用](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus のトピックとサブスクリプションの使用方法](service-bus-dotnet-how-to-use-topics-subscriptions.md)