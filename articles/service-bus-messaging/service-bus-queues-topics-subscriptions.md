---
title: "Azure Service Bus のメッセージング キュー、トピック、サブスクリプションの概要 | Microsoft Docs"
description: "Service Bus メッセージング エンティティの概要です。"
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: a306ced4-74e9-47c6-990a-d9c47efa31d5
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/23/2017
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: 0c727a47d6b947484f9a5cd678fef14d6fe2b4ab
ms.lasthandoff: 03/24/2017


---
# <a name="service-bus-queues-topics-and-subscriptions"></a>Service Bus のキュー、トピック、サブスクリプション
Microsoft Azure Service Bus は、信頼性の高いメッセージ キュー機能や永続的なパブリッシュ/サブスクライブ メッセージング機能など、クラウドベースのメッセージ指向ミドルウェアの一連のテクノロジをサポートしています。 このような "仲介型" メッセージング機能は、分離されたメッセージング機能と考えることができます。これは、Service Bus メッセージング ファブリックを使用するパブリッシュ/サブスクライブ、一時的な切り離し、負荷分散のシナリオをサポートします。 分離型通信には、クライアントとサーバーを必要に応じて接続し、非同期に操作を実行できるなど多数の利点があります。

Service Bus のメッセージング機能の中核を形成するメッセージング エンティティは、キュー、トピックおよびサブスクリプション、ルール/アクション、ルール/アクションです。

## <a name="queues"></a>キュー
キューでは、コンシューマーが競合している場合のメッセージ配信に*先入れ先出し法 (FIFO)* を使用します。 つまり、通常、メッセージは、キューに追加された順番で受信され、処理されると予想されます。このとき、メッセージを受信して処理できるメッセージ コンシューマーは、メッセージ 1 件につき 1 つだけです。 キューを使用する主な利点は、アプリケーション コンポーネントの "一時的な切り離し" を達成することです。 言い換えると、メッセージはキューに永続的に格納されるため、プロデューサー (送信者) とコンシューマー (受信者) が同時にメッセージを送受信する必要はありません。 さらに、プロデューサーは、メッセージの処理と送信を続ける場合、メッセージ コンシューマーからの応答を待つ必要がありません。

関連する利点として "負荷平準化" があります。これにより、プロデューサーとコンシューマーは異なるレートでメッセージを送受信できます。 多くのアプリケーションでは、システム負荷が時間の経過とともに変化しますが、各作業単位に必要な処理時間は通常一定に保たれます。 仲介のメッセージ プロデューサーとコンシューマーでキューを使用する場合、コンシューマー側アプリケーションに必要なのは、ピーク時の負荷ではなく平均的な負荷を処理できるようにプロビジョニングしておくことだけです。 キューの深さは、受信の負荷の変化に応じて増減します。 このため、アプリケーション負荷への対応に必要なインフラストラクチャに関して直接費用を節約できます。 負荷の増大に合わせて、キューからの読み取りのためにワーカー プロセスを追加できます。 各メッセージは、ワーカー プロセスの中の 1 つのプロセスによって処理されます。 さらに、このプルベースの負荷分散では、各ワーカー コンピューターがそれぞれ独自の最大レートでメッセージをプルするため、それらのコンピューターの処理能力が異なる場合であっても使用を最適化できます。 このパターンは、"競合コンシューマー" パターンと呼ばれることもあります。

キューを使用してメッセージ プロデューサーとメッセージ コンシューマーの間を仲介すると、必然的にコンポーネント間の結び付きは緩くなります。 プロデューサーとコンシューマーは相互に認識しないため、プロデューサーに影響することなく、コンシューマーをアップグレードできます。

キューの作成は、複数の手順から成るプロセスです。 Service Bus メッセージング エンティティ (キューとトピックの両方) の管理操作は、[Microsoft.ServiceBus.NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager#microsoft_servicebus_namespacemanager) クラスで実行されます。このクラスは、Service Bus の名前空間とユーザー資格情報のベース アドレスを指定して作成します。 [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager#microsoft_servicebus_namespacemanager) クラスには、メッセージング エンティティの作成、列挙、削除を実行するためのメソッドが用意されています。 SAS の名前とキーから [Microsoft.ServiceBus.TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider#microsoft_servicebus_tokenprovider) オブジェクトを作成し、サービスの名前空間管理オブジェクトを作成した後、[Microsoft.ServiceBus.NamespaceManager.CreateQueue](/dotnet/api/microsoft.servicebus.namespacemanager#Microsoft_ServiceBus_NamespaceManager_CreateQueue_System_String_) メソッドを使用してキューを作成できます。 次に例を示します。

```csharp
// Create management credentials
TokenProvider credentials = TokenProvider.CreateSharedAccessSignatureTokenProvider(sasKeyName,sasKeyValue);
// Create namespace client
NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", ServiceNamespace, string.Empty), credentials);
```

その後、Service Bus の URI を引数として使用して、キュー オブジェクトとメッセージング ファクトリを作成できます。 次に例を示します。

```csharp
QueueDescription myQueue;
myQueue = namespaceClient.CreateQueue("TestQueue");
MessagingFactory factory = MessagingFactory.Create(ServiceBusEnvironment.CreateServiceUri("sb", ServiceNamespace, string.Empty), credentials); 
QueueClient myQueueClient = factory.CreateQueueClient("TestQueue");
```

その後は、キューにメッセージを送信できます。 たとえば、`MessageList` という名前の仲介型メッセージの一覧がある場合、コードは次のようになります。

```csharp
for (int count = 0; count < 6; count++)
{
    var issue = MessageList[count];
    issue.Label = issue.Properties["IssueTitle"].ToString();
    myQueueClient.Send(issue);
}
```

その後、次のように、キューからメッセージを受信します。

```csharp
while ((message = myQueueClient.Receive(new TimeSpan(hours: 0, minutes: 0, seconds: 5))) != null)
    {
        Console.WriteLine(string.Format("Message received: {0}, {1}, {2}", message.SequenceNumber, message.Label, message.MessageId));
        message.Complete();

        Console.WriteLine("Processing message (sleeping...)");
        Thread.Sleep(1000);
    }
```

[ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) モードでは、受信は単発の操作です。つまり、Service Bus は要求を受信すると、メッセージを読み取り中としてマークしてアプリケーションに返します。 **ReceiveAndDelete** モードは最もシンプルなモデルであり、障害発生時にアプリケーション側でメッセージを処理しないことを許容できるシナリオに最適です。 このことを理解するために、コンシューマーが受信要求を発行した後で、メッセージを処理する前にクラッシュしたというシナリオを考えてみましょう。 Service Bus がメッセージを読み取り中としてマークするため、アプリケーションは、再起動してメッセージの読み取りを再開すると、クラッシュ前に読み取られていたメッセージを見落とすことになります。

[PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode) モードでは、メッセージの受信処理は 2 段階になります。これにより、メッセージが失われることを許容できないアプリケーションに対応することができます。 Service Bus は要求を受け取ると、次に読み取られるメッセージを検索して、他のコンシューマーが受信できないようロックしてから、アプリケーションにそのメッセージを返します。 アプリケーションがメッセージの処理を終えた後 (または後で処理するために確実に保存した後)、受信したメッセージに対して [Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Complete) を呼び出して受信処理の第 2 段階を完了します。 Service Bus は、**Complete** の呼び出しを確認すると、メッセージを読み取り済みとしてマークします。

アプリケーションがなんらかの理由によってメッセージを処理できない場合には、受信したメッセージに対して ([Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Complete) の代わりに) [Abandon](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Abandon) メソッドを呼び出すことができます。 このメソッドが呼び出されると、Service Bus によってメッセージのロックが解除され、同じコンシューマーまたは競合する別のコンシューマーが再度そのメッセージを受信できるようになります。 第二に、ロックに関連付けられたタイムアウトがあります。アプリケーションがクラッシュした場合など、ロックがタイムアウトになる前にアプリケーションがメッセージの処理に失敗した場合は、Service Bus によってメッセージのロックが解除され、再度受信できるようになります (基本的に既定で [Abandon](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Abandon) 操作を実行します)。

メッセージが処理された後、**Complete** 要求が発行される前にアプリケーションがクラッシュした場合は、アプリケーションが再起動する際にメッセージが再配信されます。 一般的に、この動作は "*1 回以上*" の処理と呼ばれます。つまり、各メッセージは 1 回以上処理されますが、 特定の状況では、同じメッセージが再配信される可能性があります。 重複した処理を許容できないシナリオでは、メッセージの **MessageId** プロパティに基づいて実現可能な重複を検出するための追加のロジックがアプリケーションで必要になります。このプロパティはメッセージが再配信されても変わりません。 これは "*厳密に 1 回*" の処理と呼ばれます。

## <a name="topics-and-subscriptions"></a>トピックとサブスクリプション
各メッセージが 1 つのコンシューマーによって処理されるキューとは異なり、"*トピック*" と "*サブスクリプション*" では、"*パブリッシュ/サブスクライブ*" パターンを使用した 1 対多形式の通信が行われます。 パブリッシュされた各メッセージは、これは非常に多くの受信者を対象とする場合に便利であり、トピックに登録している各サブスクリプションで使用できます。 メッセージは、トピックに送信された後、サブスクリプションごとに設定できるフィルター規則に基づいて、関連付けられている 1 つ以上のサブスクリプションに配信されます。 サブスクリプションでは、追加のフィルターを使用して、受信するメッセージを限定できます。 メッセージは、キューに送信される場合と同じようにトピックに送信されますが、トピックからメッセージを直接受信することはありません。 代わりに、メッセージはサブスクリプションから受信します。 トピック サブスクリプションは、トピックに送信されたメッセージのコピーを受け取る仮想キューのようなものです。 メッセージは、キューから受信する場合と同じ方法でサブスクリプションから受信します。

比較として、キューのメッセージ送信機能はそのままトピックに相当し、メッセージ受信機能はサブスクリプションに相当します。 特に、これは、サブスクリプションでは、競合コンシューマー、一時的な切り離し、負荷平滑化、負荷分散など、キューに関してこのセクションで既に説明したのと同じパターンがサポートされることを意味します。

トピックの作成は、前のセクションの例で示したキューの作成と似ています。 サービス URI を作成してから、[NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) クラスを使用して名前空間クライアントを作成します。 その後、[CreateTopic](/dotnet/api/microsoft.servicebus.namespacemanager#Microsoft_ServiceBus_NamespaceManager_CreateTopic_System_String_) メソッドを使用してトピックを作成します。 次に例を示します。

```csharp
TopicDescription dataCollectionTopic = namespaceClient.CreateTopic("DataCollectionTopic");
```

次に、必要に応じてサブスクリプションを追加します。

```csharp
SubscriptionDescription myAgentSubscription = namespaceClient.CreateSubscription(myTopic.Path, "Inventory");
SubscriptionDescription myAuditSubscription = namespaceClient.CreateSubscription(myTopic.Path, "Dashboard");
```

その後、トピック クライアントを作成できます。 次に例を示します。

```csharp
MessagingFactory factory = MessagingFactory.Create(serviceUri, tokenProvider);
TopicClient myTopicClient = factory.CreateTopicClient(myTopic.Path)
```

前のセクションで示したように、メッセージの送信者を使用すると、トピックとの間でメッセージを送受信できます。 次に例を示します。

```csharp
foreach (BrokeredMessage message in messageList)
{
    myTopicClient.Send(message);
    Console.WriteLine(
    string.Format("Message sent: Id = {0}, Body = {1}", message.MessageId, message.GetBody<string>()));
}
```

キューの場合に似ていますが、[QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient) オブジェクトの代わりに [SubscriptionClient](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient) オブジェクトを使用して、サブスクリプションからメッセージを受信します。 サブスクリプション クライアントを作成し、トピックの名前とサブスクリプションの名前、さらに必要に応じて受信モードをパラメーターとして渡します。 たとえば、**Inventory** サブスクリプションでは、次のようになります。

```csharp
// Create the subscription client
MessagingFactory factory = MessagingFactory.Create(serviceUri, tokenProvider); 

SubscriptionClient agentSubscriptionClient = factory.CreateSubscriptionClient("IssueTrackingTopic", "Inventory", ReceiveMode.PeekLock);
SubscriptionClient auditSubscriptionClient = factory.CreateSubscriptionClient("IssueTrackingTopic", "Dashboard", ReceiveMode.ReceiveAndDelete); 

while ((message = agentSubscriptionClient.Receive(TimeSpan.FromSeconds(5))) != null)
{
    Console.WriteLine("\nReceiving message from Inventory...");
    Console.WriteLine(string.Format("Message received: Id = {0}, Body = {1}", message.MessageId, message.GetBody<string>()));
    message.Complete();
}          

// Create a receiver using ReceiveAndDelete mode
while ((message = auditSubscriptionClient.Receive(TimeSpan.FromSeconds(5))) != null)
{
    Console.WriteLine("\nReceiving message from Dashboard...");
    Console.WriteLine(string.Format("Message received: Id = {0}, Body = {1}", message.MessageId, message.GetBody<string>()));
}
```

### <a name="rules-and-actions"></a>ルールとアクション
多くのシナリオでは、特性のあるメッセージは、異なる方法で処理する必要があります。 これを実現するために、目的のプロパティを持つメッセージを検索し、該当するプロパティに特定の変更を行うようにサブスクリプションを構成できます。 Service Bus のサブスクリプションには、トピックに送信されたすべてのメッセージが表示されますが、仮想サブスクリプション キューにコピーできるのは、これらのメッセージの一部のみです。 これを行うには、サブスクリプション フィルターを使用します。 このような変更は、"*フィルター アクション*" と呼ばれます。 サブスクリプションを作成する場合、メッセージのシステム プロパティ (**Label** など) とカスタム アプリケーション プロパティ (**StoreName** など) の両方で機能するフィルター式を指定できます。この場合、SQL フィルター式は省略可能です。SQL フィルター式を指定しない場合は、サブスクリプションで定義されている任意のフィルター アクションが、そのサブスクリプションのすべてのメッセージに対して実行されます。

前の例を使用して、**Store1** からの受信メッセージだけを取り出すようにフィルターするには、Dashboard サブスクリプションを次のように作成します。

```csharp
namespaceManager.CreateSubscription("IssueTrackingTopic", "Dashboard", new SqlFilter("StoreName = 'Store1'"));
```

このサブスクリプション フィルターを適用すると、`StoreName` プロパティが `Store1` に設定されているメッセージだけが、`Dashboard` サブスクリプションの仮想キューにコピーされます。

使用可能なフィルター値の詳細については、[SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter) クラスと [SqlRuleAction](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction) クラスのドキュメントを参照してください。 また、[「ブローカー メッセージング: 高度なフィルター」](http://code.msdn.microsoft.com/Brokered-Messaging-6b0d2749)と [トピック フィルター](https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/TopicFilters) に関するページのサンプルも参照してください。

## <a name="next-steps"></a>次のステップ
Service Bus のメッセージングの使用の詳細と例については、次の高度なトピックをご覧ください。

* [Service Bus メッセージングの概要](service-bus-messaging-overview.md)
* [Service Bus ブローカー メッセージングに関する .NET チュートリアル](service-bus-brokered-tutorial-dotnet.md)
* [Service Bus ブローカー メッセージングの REST チュートリアル](service-bus-brokered-tutorial-rest.md)
* [トピック フィルターのサンプル](https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/TopicFilters)
* [ブローカー メッセージング: 高度なフィルターのサンプル](http://code.msdn.microsoft.com/Brokered-Messaging-6b0d2749)


