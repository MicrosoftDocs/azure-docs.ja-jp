---
title: Azure Service Bus メッセージの有効期限の設定 | Microsoft Docs
description: Azure Service Bus メッセージの Time to Live と有効期限の設定
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
ms.openlocfilehash: 1ea645ee53f91a62bd49fb1da0d44e2962708b88
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/24/2019
ms.locfileid: "54856963"
---
# <a name="message-expiration-time-to-live"></a>メッセージの有効期限 (Time to Live)

メッセージが受信者に伝えるメッセージ、コマンド、または問い合わせの中のペイロードには、ほとんどの場合、何らかの形式のアプリケーション レベルの有効期限が適用されます。 有効期限が切れると、コンテンツの配信は停止され、要求された操作は実行されなくなります。

キューおよびトピックがアプリケーションの部分的実行、またはアプリケーションの一部の実行の文脈で使用されることが多い開発およびテスト環境では、次のテストを何もない状態で開始できるよう、保留中のテスト メッセージをガベージ コレクションで自動的に回収する必要もあります。

各メッセージの有効期限は、相対的な期間を指定する、[TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) システム プロパティを設定することで制御できます。 メッセージがエンティティにエンキューされると、有効期限は、絶対瞬間になります。 その時点で、[ExpiresAtUtc](/dotnet/api/microsoft.azure.servicebus.message.expiresatutc) プロパティに [(**EnqueuedTimeUtc**](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc#Microsoft_ServiceBus_Messaging_BrokeredMessage_EnqueuedTimeUtc) + [**TimeToLive**)](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) の値が設定されます。 ブローカー メッセージの有効期限 (TTL) 設定は、クライアントがアクティブにリッスンしていない場合は適用されません。

**ExpiresAtUtc** 瞬間を過ぎると、メッセージは取得できなくなります。 有効期限設定は、現在配信がロックされているメッセージには影響しません。これらのメッセージは、引き続き正常に処理されます。 ロックの有効期限が切れた、またはメッセージが破棄されると、有効期限切れは直ちに有効になります。

メッセージがロックされている状態では、有効期限が切れたメッセージをアプリケーションが所有している可能性があります。 アプリケーションがメッセージの処理を進めるか、メッセージを破棄するかは、実装によって異なります。

## <a name="entity-level-expiration"></a>エンティティ レベルの有効期限

キューまたはトピックに送信されたすべてのメッセージには、[defaultMessageTimeToLive](/azure/templates/microsoft.servicebus/namespaces/queues) プロパティでエンティティ レベルに設定された既定の有効期限が適用され、これは、作成中にポータルで設定し後で調整することもできます。 既定の有効期限は、[TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) が明示的に設定されていないエンティティに送信されるすべてのメッセージに使用されます。 既定の有効期限は、**TimeToLive** 値の上限としても機能します。 **TimeToLive** 有効期限が既定値より長いメッセージは、エンキューされる前に **defaultMessageTimeToLive** の値に自動的に調整されます。

有効期限が切れたメッセージは、[EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enabledeadletteringonmessageexpiration#Microsoft_ServiceBus_Messaging_QueueDescription_EnableDeadLetteringOnMessageExpiration) プロパティを設定するか、またはポータルでそれぞれのボックスにチェックを付けることで、[配信不能キュー](service-bus-dead-letter-queues.md)に移動することもできます。 オプションを無効のままにすると、期限切れのメッセージは削除されます。 配信不能キューに移動された期限切れのメッセージは、ユーザー プロパティ セクションにブローカーが保存する [DeadletterReason](service-bus-dead-letter-queues.md#moving-messages-to-the-dlq) プロパティを評価することで、他の配信不能キューと区別できます。この場合、値は [TTLExpiredException](service-bus-dead-letter-queues.md#moving-messages-to-the-dlq) になります。

上記のように、ロック中のメッセージは有効期限が切れず、フラグがエンティティに設定されている場合は、ロックが破棄された時点で、または有効期限が切れるとメッセージは配信不能キューに移動されます。 ただし、メッセージが正常に到着した場合は移動されません。この場合、有効期限が切れたとされたにもかかわらず、アプリケーションによって正常に処理されたと推測できます。

[TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) と期限切れ時に配信不能キューに自動的 (トランザクションとして) に移動される機能を組み合わせることで、ハンドラーまたはハンドラーのグループに渡された期限付きのジョブを、期限が来たときに確実に取得し処理できます。

たとえば、規模が制限されたバックエンドでジョブを確実に実行する必要がある Web サイトがあるとします。ここで、トラフィックの急増が随時発生する、またバックエンドの可用性を隔離する必要があるとします。 通常の場合、送信されたユーザー データのためのサーバー側のハンドラーが、情報をキューにプッシュし、その後、処理の完了を確認する返信を返信キューで受け取ります。 トラフィックのスパイクがあり、バックエンド ハンドラーが期限までにバックログ アイテムを処理できない場合、期限切れのジョブは配信不能キューに返されます。 対話ユーザーには、要求した操作が通常より少し長くかかることを通知し、異なるキューから要求を処理パスに送り、最終的には処理結果をユーザーにメールで送信できます。 


## <a name="temporary-entities"></a>一時エンティティ

Service Bus のキュー、トピック、およびサブスクリプションは、指定した期間使用されていない場合は自動的に削除される、一時エンティティとして作成できます。
 
自動クリーンアップは、エンティティが動的に作成され、テストまたはデバッグの実行が中断されたことにより、使用後にクリーンアップされない、開発およびテストのシナリオで役立ちます。 また、アプリケーションが、応答キューなどの動的エンティティを作成し、Web サーバー プロセス、または比較的寿命の短い他のオブジェクトに応答を戻し、オブジェクト インスタンスが消去されたときに確実にこれらのエンティティをクリーン アップすることが困難な場合でも有用です。

この機能は、[autoDeleteOnIdle](/azure/templates/microsoft.servicebus/namespaces/queues) プロパティを使用して有効にできます。 このプロパティは、このプロパティはエンティティがアイドル状態 (未使用) であり続けると自動的に削除される期間を設定します。 このプロパティの最小値は 5 です。
 
**autoDeleteOnIdle** プロパティは、Azure Resource Manager を操作して、または .NET Framework クライアント [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) API 経由で設定する必要があります。 これはポータルでは設定できません。

## <a name="idleness"></a>アイドル

エンティティ (キュー、トピック、およびサブスクリプション) のアイドルとみなされるものを次に示します。

- キュー
    - 送信なし  
    - 受信なし  
    - キューに対する更新なし  
    - スケジュール設定されたメッセージなし  
    - 閲覧/ピークなし 
- トピック  
    - 送信なし  
    - トピックに対する更新なし  
    - スケジュール設定されたメッセージなし 
- サブスクリプション
    - 受信なし  
    - サブスクリプションに対する更新なし  
    - サブスクリプションに新しいルールは追加されない  
    - 閲覧/ピークなし  
 


## <a name="next-steps"></a>次の手順

Service Bus メッセージングの詳細については、次のトピックをご覧ください。

* [Service Bus のキュー、トピック、サブスクリプション](service-bus-queues-topics-subscriptions.md)
* [Service Bus キューの使用](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus のトピックとサブスクリプションの使用方法](service-bus-dotnet-how-to-use-topics-subscriptions.md)