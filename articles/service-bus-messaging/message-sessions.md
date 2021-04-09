---
title: Azure Service Bus のメッセージ セッション | Microsoft Docs
description: この記事では、セッションを使用して、関連メッセージのバインドなしシーケンスの結合および順序指定処理を有効にする方法を説明します。
ms.topic: article
ms.date: 01/20/2021
ms.openlocfilehash: 6d316571d69d2e1e73ddca4ccca53c116ee8fa5f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98680755"
---
# <a name="message-sessions"></a>メッセージ セッション
Microsoft Azure Service Bus セッションでは、関連メッセージのバインドなしシーケンスの結合および順序指定処理が可能です。 セッションは、**先入れ先出し (FIFO)** および **要求 - 応答** のパターンで使用できます。 この記事では、Service Bus の使用時に、セッションを使用してこれらのパターンを実装する方法について説明します。 

> [!NOTE]
> Service Bus の Basic レベルはセッションをサポートしていません。 Standard レベルと Premium レベルはセッションをサポートしています。 これらのレベルの違いについては、「[Service Bus の価格](https://azure.microsoft.com/pricing/details/service-bus/)」を参照してください。

## <a name="first-in-first-out-fifo-pattern"></a>先入れ先出し (FIFO) パターン
Service Bus で FIFO 処理を保証するには、セッションを使用します。 Service Bus では、メッセージ間の関係の性質に関する規範はなく、またメッセージのシーケンスの開始または終了位置を決定する特定のモデルは定義されていません。

送信プロセスは、トピックまたはキューにメッセージを送信するときに、アプリケーションで定義されたセッションに固有の ID に、[SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId) プロパティを設定することで、セッションを作成できます AMQP 1.0 プロトコル レベルでは、この値は *group-id* プロパティに相当します。

セッション対応のキューまたはサブスクリプションでは、セッションの [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId) を持つメッセージが 1 つ以上ある場合にセッションが生成されます。 1 度生成されたセッションの有効期限や存続期間、および API は定義されていません。 理論上は、メッセージを今日のセッションで受信して、次のメッセージを 1 年後に受信したとしても、**SessionId** が一致する限り、Service Bus の観点からすると同じセッションになります。

通常、アプリケーションでは関連するメッセージの開始および終了は明確に認識されます。 Service Bus では、特定の規則は設定されていません。

ファイルの転送シーケンスを説明する例として、最初のメッセージの **start** に **Label** プロパティを設定し、中間メッセージに **content**、最後のメッセージに **end** を設定します。 コンテンツ メッセージの相対位置は、現在のメッセージの *SequenceNumber* と **start** メッセージの *SequenceNumber* との差分として計算できます。

Service Bus のセッション機能では、C# や Java API の [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) の形式で特定の受信操作を行えます。 この機能は、Azure Resource Manager からキューやサブスクリプションの [requiresSession](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) プロパティを設定するか、ポータルでフラグを設定することによって有効化できます。 これは、関連する API 操作を使用する前に必要です。

フラグは、ポータルの次のチェック ボックスを使用して設定します。

![[Enable sessions]\(セッションを有効にする\) オプションが選択されて赤枠で囲まれている [キューの作成] ダイアログ ボックスのスクリーンショット。][2]

> [!NOTE]
> キューまたはサブスクリプションでセッションが有効になっている場合、クライアント アプリケーションでは通常のメッセージを送受信 ***できなくなります***。 すべてのメッセージは、(セッション ID を設定することで) セッションの一部として送信し、セッションを受信することで受信する必要があります。

セッションに対応する API は、キューまたはサブスクリプション クライアントに存在します。 セッションとメッセージを受信するタイミングを制御する命令モデルと、受信ループを管理する複雑さを解消する *OnMessage* に類似したハンドラーベースのモデルがあります。

### <a name="session-features"></a>セッションの機能

セッションは、順序指定の送信を維持し保証しながら、インターリーブされたメッセージ ストリームの同時逆多重化を提供します。

![セッション機能で順序指定の送信が維持される方法を示す図。][1]

[MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) の受信プロセスは、セッションを受け入れたクライアントによって作成されます。 クライアントは [QueueClient.AcceptMessageSession](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesession#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSession) (C# の場合は [QueueClient.AcceptMessageSessionAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesessionasync#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSessionAsync)) を呼び出します。 リアクティブなコールバック モデルでは、セッション ハンドラーが登録されます。

[MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) オブジェクトが受け入れられてクライアントに保持されている間、クライアントは、そのセッションの [SessionId](/dotnet/api/microsoft.servicebus.messaging.messagesession.sessionid#Microsoft_ServiceBus_Messaging_MessageSession_SessionId) を持つ、キューまたはサブスクリプションに存在するすべてのメッセージと、その **SessionId** を持つ、セッションが保持されている間に引き続き到着するメッセージに対する排他的ロックを保持します。

ロックが解除されるのは、**Close** または **CloseAsync** が呼び出されたとき、またはアプリケーションが終了操作を実行できない場合にロックの有効期限が切れたときです。 セッション ロックは、ファイルの排他的ロックと同様に処理する必要があります。つまり、アプリケーションは、セッションが不要になったり、それ以上のメッセージが想定されなくなったりしたらただちにセッションを閉じる必要があります。

複数の同時受信プロセスがキューからプルする場合、特定のセッションに属するメッセージは、そのセッションに対するロックを現在保持している特定の受信プロセスに送信されます。 この操作により、1 つのキューまたはサブスクリプション内のインターリーブされたメッセージ ストリームはさまざまな受信プロセスに対してクリーンに逆多重化されます。また、ロック管理が Service Bus 内のサーバー側で行われるため、それらの受信プロセスはさまざまなクライアント マシン上に存続できます。

上の図には、3 つの同時セッション受信プロセスが示されています。 `SessionId` = 4 の 1 つのセッションには有効な所有クライアントがなく、メッセージはこの特定のセッションから配信されません。 セッションは、サブ キューなどのさまざまな方法で機能します。

セッション受信プロセスによって保持されるセッション ロックは、*peek-lock* 解決モデルによって使用されるメッセージ ロックの傘です。 セッションに対してロックを保持できるのは、1 つの受信プロセスだけです。 受信プロセスは多数のインフライト メッセージを受け取る可能性がありますが、メッセージは順番に受信されます。 メッセージを破棄すると、同じメッセージが、次の受信操作で再利用されます。

### <a name="message-session-state"></a>メッセージ セッションの状態

ワークフローが大規模で高可用性のクラウド システムで処理される場合、特定のセッションに関連付けられている、ワークフロー ハンドラーは、想定外の障害から回復でき、異なるプロセスやマシンで部分的に完了した作業をその作業が開始した場所から再開できる必要があります。

セッション状態機能は、そのセッションに対して記録された処理の状態になるようにすぐに利用可能なセッションが、新しいプロセッサによって取得されるときに、ブローカー内のメッセージ セッションのアプリケーションで定義された注釈を使用できます。

Service Bus の観点からは、メッセージ セッションの状態は、Service Bus Standard の場合は 256 KB の、Service Bus Premium の場合は 1 MB のサイズのデータを保持できる不透明なバイナリ オブジェクトです。 セッションに対する処理状態は、セッション状態に保持されるか、セッション状態は記憶域の場所または情報が保管されているデータベース レコードを指すことができます。

セッション状態を管理するための [SetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) および [GetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate#Microsoft_ServiceBus_Messaging_MessageSession_GetState) API は、C# と Java API のいずれも [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) にあります。 過去にセッション状態セットがないセッションは、**GetState** への参照として **null** を返します。 以前に設定したセッション状態をクリアするには [SetState(null)](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) を使用します。

セッション状態は、セッション内のすべてのメッセージが処理されても、クリア (**null** を返す) されない限りそのままです。

キューまたはサブスクリプションに存在するすべてのセッションは、Java API の **SessionBrowser** メソッドを使用して、.NET Framework クライアントでは [QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient) および [SubscriptionClient](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient) の [GetMessageSessions](/dotnet/api/microsoft.servicebus.messaging.queueclient.getmessagesessions#Microsoft_ServiceBus_Messaging_QueueClient_GetMessageSessions) を使って列挙できます。

キューに保持されたセッション状態またはサブスクリプション数は、そのエンティティのストレージ クォータがいっぱいになるまで計算されます。 このため、セッションで、アプリケーションが終了したら、外部管理コストを回避するために、アプリケーションで保持された状態をクリーンアップすることをを推奨します。

### <a name="impact-of-delivery-count"></a>配信数の影響

メッセージごとの配信数の定義は、セッションのコンテキストと、セッションがない場合とで若干異なります。 配信数が増える場合についてまとめた表を次に示します。

| シナリオ | メッセージの配信数が増える |
|----------|---------------------------------------------|
| セッションは受け入れられますが、(タイムアウトによって) セッション ロックの有効期限が切れています | はい |
| セッションは受け入れられ、セッション内のメッセージは (ロックされている場合でも) 完了しておらず、セッションは閉じられています | いいえ |
| セッションは受け入れられ、メッセージが完了した後、セッションは明示的に閉じられています | 該当なし (これは標準のフローです。 この場合、メッセージはセッションから削除されます) |

## <a name="request-response-pattern"></a>要求 - 応答パターン
[要求 - 応答パターン](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RequestReply.html)は、適切に確立された統合パターンであり、送信側アプリケーションから要求を送信でき、受信側が送信側アプリケーションに応答を正しく送信する手段を提供します。 このパターンには、通常、アプリケーションからの応答の送信先となる、有効期間が短いキューまたはトピックが必要です。 このシナリオでは、セッションは、同等のセマンティクスを持つ単純な代替ソリューションを提供します。 

送信側アプリケーションを一意に識別するように特定のヘッダー パラメーターを設定することにより、複数のアプリケーションが 1 つの要求キューに要求を送信できます。 受信側アプリケーションは、キューに入ってくる要求を処理し、セッション対応のキューで応答を送信して、送信側が要求メッセージで送信した一意識別子にセッション ID を設定します。 要求を送信したアプリケーションは、特定のセッション ID でメッセージを受信し、応答を正しく処理することができます。

> [!NOTE]
> 最初の要求を送信したアプリケーションでは、セッション ID を認識し、`SessionClient.AcceptMessageSession(SessionID)` を使用して、応答を予期しているセッションをロックする必要があります。 アプリケーションのインスタンスをセッション ID として一意に識別する、GUID を使用することをお勧めします。特定の受信側が応答をロックして処理できるようにするには、キューにセッション ハンドラーも `AcceptMessageSession(timeout)` も存在していない必要があります。

## <a name="next-steps"></a>次のステップ

- セッション対応メッセージを処理するために .NET フレームワークを使用する例としては、[Microsoft.Azure.ServiceBus samples](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/Sessions) または [Microsoft.ServiceBus.Messaging samples](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/Sessions) を参照してください。 

Service Bus メッセージングの詳細については、次のトピックをご覧ください。

* [Service Bus のキュー、トピック、サブスクリプション](service-bus-queues-topics-subscriptions.md)
* [Service Bus キューの使用](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus のトピックとサブスクリプションの使用方法](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/message-sessions/sessions.png
[2]: ./media/message-sessions/queue-sessions.png
