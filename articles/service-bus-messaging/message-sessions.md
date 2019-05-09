---
title: Azure Service Bus のメッセージ セッション | Microsoft Docs
description: セッションで Azure Service Bus メッセージのシーケンスを処理します。
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
ms.openlocfilehash: c767406ceec703b5c14680ec96fdf703c2316044
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59500143"
---
# <a name="message-sessions-first-in-first-out-fifo"></a>メッセージ セッション: 先入れ先出し (FIFO) 

Microsoft Azure Service Bus セッションでは、関連メッセージのバインドなしシーケンスの結合および順序指定処理が可能です。 Service Bus の FIFO 処理を保証するには、セッションを使用します。 Service Bus では、メッセージ間の関係の性質に関する規範はなく、またメッセージのシーケンスの開始または終了位置を既定するモデルは特に定義されていません。

> [!NOTE]
> Service Bus の Basic レベルはセッションをサポートしていません。 Standard レベルと Premium レベルはセッションをサポートしています。 詳細については、「[Service Bus の価格](https://azure.microsoft.com/pricing/details/service-bus/)」をご覧ください。

送信プロセスは、トピックまたはキューにメッセージを送信するときに、アプリケーションで定義されたセッションに固有の ID に、[SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId) プロパティを設定することで、セッションを作成できます AMQP 1.0 プロトコル レベルでは、この値は *group-id* プロパティに相当します。

セッション対応のキューまたはサブスクリプションでは、セッションの [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId) を持つメッセージが 1 つ以上ある場合にセッションが生成されます。 1 度生成されたセッションの有効期限または表示期間や API は定義されていません。 理論上は、メッセージを今日のセッションで受信して、次のメッセージを 1 年後に受信したとしても、**SessionId** が一致する限り、Service Bus の観点からすると同じセッションになります。

通常、アプリケーションでは関連するメッセージの開始および終了は明確に認識されます。 Service Bus では、特定の規則は設定されていません。

ファイルの転送シーケンスを説明する例として、最初のメッセージの **start** に **Label** プロパティを設定し、中間メッセージに **content**、最後のメッセージに **end** を設定します。 コンテンツ メッセージの相対位置は、現在のメッセージの *SequenceNumber* と **start** メッセージの *SequenceNumber* との差分として計算できます。

Service Bus のセッション機能では、C# や Java API の [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) の形式で特定の受信操作を行えます。 この機能は、Azure Resource Manager からキューやサブスクリプションの [requiresSession](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) プロパティを設定するか、ポータルでフラグを設定することによって有効化できます。 これは、関連する API 操作を使用する前に必要です。

フラグは、ポータルの次のチェック ボックスを使用して設定します。

![][2]

セッションに対応する API は、キューまたはサブスクリプション クライアントに存在します。 セッションとメッセージをいつ受信するかを制御する命令モデルがあり、受信ループを管理する複雑性を低減できる プロセス *OnMessage* に類似したハンドラーベースのモデルがあります。

## <a name="session-features"></a>セッションの機能

セッションは、順序指定の送信を維持し保証しながら、インターリーブされたメッセージ ストリームの同時逆多重化を提供します。

![][1]

[MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) の受信プロセスは、セッションを受け入れたクライアントによって作成されます。 クライアントは [QueueClient.AcceptMessageSession](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesession#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSession) (C# の場合は [QueueClient.AcceptMessageSessionAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesessionasync#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSessionAsync)) を呼び出します。 リアクティブなコールバック モデルでは、セッション ハンドラーが登録されます。

[MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) オブジェクトが受け入れられクライアントに保持されている間、そのクライアントはそのセッションの [SessionId](/dotnet/api/microsoft.servicebus.messaging.messagesession.sessionid#Microsoft_ServiceBus_Messaging_MessageSession_SessionId) が付いたキューまたはサブスクリプションに存在するすべてのメッセージと、その **SessionId** が付けられ、セッションが保持されている間も着信し続けるメッセージに対する排他的ロックを保持します。

ロックが解除されるのは、**Close** または **CloseAsync** が呼び出されたとき、またはロックの有効期限が切れ、アプリケーションが終了操作を実行できないときです。 セッション ロックは、ファイルの排他的ロックと同様に処理する必要があり、アプリケーションは、セッションが不要になったらただちにセッションを閉じ、それ以上のメッセージを期待しない必要があります。

複数の同時受信プロセスがキューからプルする場合、特定のセッションに属するメッセージは、そのセッションに対するロックを現在保持している特定の受信プロセスに送信されます。 この操作により、1 つのキューまたはサブスクリプションに存在するインターリーブされたメッセージ ストリームは別の受信プロセスに対しクリーンに逆多重化され、ロック管理は Service Bus 内のサーバー側で行われるため、それらの受信プロセスはクライアント マシン上でアクティブな状態を維持できます。

上の図には、3 つの同時セッション受信プロセスが示されています。 `SessionId` = 4 の 1 つのセッションには有効な所有クライアントがなく、メッセージはこの特定のセッションから配信されません。 セッションは、サブ キューなどのさまざまな方法で機能します。

セッション受信プロセスによって保持されるセッション ロックは、*peek-lock* 解決モデルによって使用されるメッセージ ロックの傘です。 受信プロセスは 2 つのメッセージを同時に送信することはでませんが、メッセージは順序付けて処理する必要があります。 新しいメッセージは、以前のメッセージが完了または配信不能に移動されたときにのみ取得できます。 メッセージを破棄すると、同じメッセージが、次の受信操作で再利用されます。

## <a name="message-session-state"></a>メッセージ セッションの状態

ワークフローが大規模で高可用性のクラウド システムで処理される場合、特定のセッションに関連付けられている、ワークフロー ハンドラーは、想定外の障害から回復でき、作業が開始された異なるプロセスやマシンにある未完了の作業を再開できる能力がある必要があります。

セッション状態機能は、そのセッションに対して記録された処理の状態になるようにすぐに利用可能なセッションが、新しいプロセッサによって取得されるときに、ブローカー内のメッセージ セッションのアプリケーションで定義された注釈を使用できます。

Service Bus の観点からは、メッセージ セッションの状態は、Service Bus Standard の場合は 256 KB の、Service Bus Premium の場合は 1 MB のサイズのデータを保持できる不透明なバイナリ オブジェクトです。 セッションに対する処理状態は、セッション状態に保持されるか、セッション状態は記憶域の場所または情報が保管されているデータベース レコードを指すことができます。

セッション状態を管理するための [SetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) および [GetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate#Microsoft_ServiceBus_Messaging_MessageSession_GetState) API は、C# と Java API のいずれも [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) にあります。 過去にセッション状態セットがないセッションは、**GetState** への参照として **null** を返します。 以前に設定したセッション状態をクリアするには [SetState(null)](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) を使用します。

セッション状態は、セッション内のすべてのメッセージが使用済みになっても、クリア (**null** を返す) されない限り同じままであることに注意してください。

キューまたはサブスクリプションに存在するすべてのセッションは、Java API の **SessionBrowser** メソッドを使用して、.NET クライアントでは [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) および [SubscriptionClient](/dotnet/api/microsoft.azure.servicebus.subscriptionclient) の [GetMessageSessions](/dotnet/api/microsoft.servicebus.messaging.queueclient.getmessagesessions#Microsoft_ServiceBus_Messaging_QueueClient_GetMessageSessions) を使って列挙できます。

キューに保持されたセッション状態またはサブスクリプション数は、そのエンティティのストレージ クォータがいっぱいになるまで計算されます。 このため、セッションで、アプリケーションが終了したら、外部管理コストを回避するために、アプリケーションで保持された状態をクリーンアップすることをを推奨します。

## <a name="next-steps"></a>次の手順

- .NET 標準ライブラリを使用して Service Bus キューからのセッション ベースのメッセージを送受信する[詳細な例](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/BasicSendReceiveUsingQueueClient)。
- .NET Framework クライアントを使用してセッション対応メッセージを処理する[例](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/Sessions)。 

Service Bus メッセージングの詳細については、次のトピックをご覧ください。

* [Service Bus のキュー、トピック、サブスクリプション](service-bus-queues-topics-subscriptions.md)
* [Service Bus キューの使用](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus のトピックとサブスクリプションの使用方法](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/message-sessions/sessions.png
[2]: ./media/message-sessions/queue-sessions.png