---
title: Azure Service Bus - メッセージの参照
description: Service Bus メッセージを参照してピークすると、Azure Service Bus クライアントは、キューまたはサブスクリプションのすべてのメッセージを列挙できます。
ms.topic: article
ms.date: 11/11/2020
ms.openlocfilehash: c52c9c967d4eada1a931e188ed4d25f7691cfb91
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "94553643"
---
# <a name="message-browsing"></a>メッセージの読み取り

Service Bus クライアントは、メッセージを読み取るか、ピークすることにより、診断とデバッグの目的で、キューまたはサブスクリプションのすべてのメッセージを列挙できます。

キューに対するピーク操作では、`Receive()` または `OnMessage()` ループでの即時取得に使用できるメッセージだけでなく、キュー内のすべてのメッセージが返されます。 各メッセージの `State` プロパティによって、メッセージがアクティブか (受信可能)、[遅延](message-deferral.md)、または[スケジュール済み](message-sequencing.md)かが分かります。 サブスクリプションに対するピーク操作では、サブスクリプション メッセージ ログ内のスケジュール済みメッセージ以外のすべてのメッセージが返されます。 

消費され、有効期限の切れたメッセージは、非同期 "ガベージ コレクション" を実行することよって消去されます。 この手順は、メッセージの有効期限が切れた後にただちに実行されるとは限りません。 そのため、`Peek` は有効期限が既に切れているメッセージを返す可能性があります。 これらのメッセージは、次回キューまたはサブスクリプションで受信操作が呼び出されたときに削除されるか、または配信不能となります。 キューから遅延メッセージを回復しようとする場合、この動作に留意してください。 期限切れのメッセージは、ピークによって返された場合でも、他の方法では通常の取得ができなくなります。 ピークはログの現在の状態を反映する診断ツールであるため、これらのメッセージが返されるのは設計によるものです。

ピークでは、他の受信者が現在処理しているロックされたメッセージも返されます。 ただし、ピークでは切断されたスナップショットが返されるため、メッセージのロック状態はピーク メッセージでは確認できません。 アプリケーションがこれらを読み取ろうとすると、[LockedUntilUtc](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.lockeduntilutc) と [LockToken](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.locktoken#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_LockToken) プロパティにより [InvalidOperationException](/dotnet/api/system.invalidoperationexception) がスローされます。

## <a name="peek-apis"></a>Peek API

[Peek/PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_PeekAsync) と [PeekBatch/PeekBatchAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatchasync#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatchAsync_System_Int64_System_Int32_) メソッドは、.NET および Java クライアント ライブラリと、受信オブジェクトに存在します。**MessageReceiver**、**MessageSession**。 ピークは、キュー、サブスクリプション、およびそれらの各配信不能キューで動作します。

繰り返し呼び出されると、**Peek** は、キューまたはサブスクリプション ログ内のすべてのメッセージを、使用可能なシーケンス番号が最も低いものから最も高いものの順に列挙します。 これは、メッセージがエンキューされた順序であり、メッセージが最終的に取得される可能性のある順序ではありません。

[PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatch_System_Int32_) は、複数のメッセージを取得し、列挙型として返します。 使用可能なメッセージがない場合は、列挙型オブジェクトは、null ではなく、空になります。

[SequenceNumber](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.sequencenumber#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_SequenceNumber) を設定してメソッドのオーバーロードを使用し、パラメーターのないメソッドのオーバーロードを呼び出して、さらに列挙することもできます。 **PeekBatch** 関数も同様に機能しますが、一度にすべてのメッセージ セットを取得します。

## <a name="next-steps"></a>次のステップ

Service Bus メッセージングの詳細については、次のトピックをご覧ください。

* [Service Bus のキュー、トピック、サブスクリプション](service-bus-queues-topics-subscriptions.md)
* [Service Bus キューの使用](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus のトピックとサブスクリプションの使用方法](service-bus-dotnet-how-to-use-topics-subscriptions.md)
