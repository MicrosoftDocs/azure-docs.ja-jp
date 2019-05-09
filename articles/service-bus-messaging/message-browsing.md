---
title: Azure Service Bus のメッセージの読み取り | Microsoft Docs
description: Service Bus メッセージの読み取りとピーク
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 425cf262b80e83a4d06074a567a2921eee12f9c2
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/24/2019
ms.locfileid: "54855943"
---
# <a name="message-browsing"></a>メッセージの読み取り

Service Bus クライアントは、メッセージを読み取るか、ピークすることにより、通常は診断とデバッグの目的で、キューまたはサブスクリプションにあるすべてのメッセージを列挙できます。

ピーク操作では、`Receive()` または `OnMessage()` ループで即時に取得できるメッセージだけでなく、キューまたはサブスクリプション メッセージ ログにあるすべてのメッセージが返されます。 各メッセージの `State` プロパティによって、メッセージがアクティブか (受信可能)、[遅延](message-deferral.md)、または[スケジュール済み](message-sequencing.md)かが分かります。

消費され、有効期限の切れたメッセージは、非同期 "ガベージ コレクション" を実行することよって消去されますが、この操作はメッセージの有効期限が切れると同時に実行されるとは限らないため、`Peek` は実際には既に有効期限が切れ、キューまたはサブスクリプションで受信操作が次回呼び出されたときに削除または配信不能キューに入れられるメッセージを返す可能性もあります。

このことに留意することは、特に、キューから遅延メッセージを回復しようとする場合に重要になります。 [ExpiresAtUtc](/dotnet/api/microsoft.azure.servicebus.message.expiresatutc#Microsoft_Azure_ServiceBus_Message_ExpiresAtUtc) インスタントが渡したメッセージは、ピークによって返された場合でも、他の方法では通常の取得ができなくなります。 ピークはログの現在の状態を反映する診断ツールであるため、これらのメッセージの返しは意図的に実行されます。

ピークでは、ロックされていて、現在他の受信者が処理しているが、まだ完了していないメッセージも返されます。 ただし、ピークは接続していない状態のスナップショットを返すため、メッセージのロック状態はピーク メッセージでは観察できません。アプリケーションがそれを読み取ろうとすると、[LockedUntilUtc](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.lockeduntilutc) と [LockToken](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.locktoken#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_LockToken) プロパティで [InvalidOperationException](/dotnet/api/system.invalidoperationexception) 例外になります。

## <a name="peek-apis"></a>Peek API

[Peek/PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_PeekAsync) および [PeekBatch/PeekBatchAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatchasync#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatchAsync_System_Int64_System_Int32_) メソッドはすべての .NET および Java クライアント ライブラリおよびすべての受信オブジェクトのメソッド、**MessageReceiver**、**MessageSession**、**QueueClient**、および **SubscriptionClient** に存在します。 ピークは、すべてのキュー、サブスクリプション、およびそれらに対応する配信不能キューに動作します。

繰り返し呼び出されると、Peek メソッドは、最小から最大のシーケンス番号の順序で、キューまたはサブスクリプションのログ内に存在するすべてのメッセージを列挙します。 これは、メッセージがエンキューされる順序であり、メッセージが最終的に取得される順序ではありません。

[PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatch_System_Int32_) は、複数のメッセージを取得し、列挙型として返します。 使用可能なメッセージがない場合は、列挙型オブジェクトは、null ではなく、空になります。

[SequenceNumber](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.sequencenumber#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_SequenceNumber) を使用してメソッドのオーバー ロードをシードを設定して開始し、パラメーターなしでメソッドのオーバーロードを呼び出してさらに列挙することもできます。 **PeekBatch** 関数も同様に機能しますが、一度にすべてのメッセージ セットを取得します。

## <a name="next-steps"></a>次の手順

Service Bus メッセージングの詳細については、次のトピックをご覧ください。

* [Service Bus のキュー、トピック、サブスクリプション](service-bus-queues-topics-subscriptions.md)
* [Service Bus キューの使用](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus のトピックとサブスクリプションの使用方法](service-bus-dotnet-how-to-use-topics-subscriptions.md)
