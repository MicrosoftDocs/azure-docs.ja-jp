---
title: "Azure Service Bus キューを使用するアプリケーションを作成する | Microsoft Docs"
description: "Azure Service Bus を使用する単純なキュー ベースのアプリケーションを作成する方法。"
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 754d91b3-1426-405e-84b4-fd36d65b114a
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/15/2017
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: d987aa22379ede44da1b791f034d713a49ad486a
ms.openlocfilehash: 40414edebcd76fc93136cbc14d7a6436fc7f6da5
ms.lasthandoff: 02/16/2017


---
# <a name="create-applications-that-use-service-bus-queues"></a>Service Bus キューを使用するアプリケーションを作成する
このトピックでは、Service Bus キューについて説明し、Service Bus を使用する簡単なキュー ベースのアプリケーションを作成する方法を示します。

個々 の販売時点管理 (POS) 端末から売上データを在庫管理システムにルーティングし、在庫管理システムではそのデータを使用して在庫を補充する時期を決定する、小売業界のシナリオについて検討します。 このソリューションでは、次の図に示すように、端末と在庫管理システムの間の通信に Service Bus メッセージングを使用します。

![Service Bus キューの画像 1](./media/service-bus-create-queues/IC657161.gif)

各 POS 端末は、**DataCollectionQueue** にメッセージを送信することによって、売上データを報告します。 これらのメッセージは、在庫管理システムによって取り出されるまでこのキューに残っています。 POS 端末は処理を続行するために在庫管理システムからの応答を待機する必要がないため、このパターンは*非同期メッセージング*と呼ばれることがよくあります。

## <a name="why-queuing"></a>キューを使用する理由
このアプリケーションを設定するために必要なコードを見る前に、POS 端末が在庫管理システムと直接 (同期的に) 対話するのではなく、キューをこのシナリオで使用することの利点について検討します。

### <a name="temporal-decoupling"></a>一時的な結合の解除
非同期メッセージング パターンでは、プロデューサーとコンシューマーが同時にオンラインになっている必要はありません。 このメッセージング インフラストラクチャは、コンシューマーがメッセージを受信する準備ができるまで、メッセージを確実に格納します。 つまり、分散型アプリケーションのコンポーネントをメンテナンスやコンポーネント クラッシュの場合でもシステム全体に影響を与えずに自動的に切断できます。 さらに、コンシューマー アプリケーションがオンラインになっている必要がある時間は、1 日のうち一定の時間だけで済みます。 たとえば、この小売業のシナリオでは、在庫管理システムは営業時間の終了後にオンラインになるだけで済むかもしれません。

### <a name="load-leveling"></a>負荷平準化
多くのアプリケーションでは、システム負荷が時間の経過とともに変化しますが、各作業単位に必要な処理時間は 通常一定に保たれます。 メッセージ プロデューサーとメッセージ コンシューマーの間をキューで仲介することは、コンシューマー側アプリケーション (worker) はピーク時ではなく平均時の負荷に対応できるようにプロビジョニングすればいいということを意味します。 キューの深さは、受信する負荷の変化に合わせて増減します。 このため、アプリケーション負荷への対応に必要なインフラストラクチャに関して直接費用を節約できます。

![Service Bus キューの画像 2](./media/service-bus-create-queues/IC657162.gif)

### <a name="load-balancing"></a>負荷分散
負荷の増大に合わせて、worker キューからの読み取りのために worker プロセスを追加できます。 各メッセージは、ワーカー プロセスの中の&1; つのプロセスによって処理されます。 さらに、このプルベースの負荷分散では、各 worker コンピューターがそれぞれ独自の最大レートでメッセージをプルするため、それらのコンピューターの処理能力が異なる場合であっても使用を最適化できます。 このパターンは、競合コンシューマー パターンと 呼ばれることもあります。

![Service Bus キューの画像 3](./media/service-bus-create-queues/IC657163.gif)

### <a name="loose-coupling"></a>疎結合
メッセージ キューを使用してメッセージ プロデューサーとメッセージ コンシューマーの間を仲介すると、必然的にコンポーネント間の結び付きは緩くなります。 プロデューサーとコンシューマーは相互に認識しないため、プロデューサーに影響することなく、コンシューマーをアップグレードできます。 さらに、既存のエンドポイントに影響を与えずメッセージング トポロジを拡張できます。 これについては、パブリッシュ/サブスクライブのところでさらに詳しく説明します。

## <a name="show-me-the-code"></a>コード
以下では、Service Bus を使用してこのアプリケーションを作成する方法を示します。

### <a name="sign-up-for-an-azure-account"></a>Azure アカウントにサインアップする
Service Bus の使用を開始するには、Azure アカウントが必要です。 アカウントがまだない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A85619ABF)で無料アカウントにサインアップできます。

### <a name="create-a-namespace"></a>名前空間の作成
サブスクリプションを作成した後は、[サービス名前空間を作成](service-bus-create-namespace-portal.md)できます。 各名前空間は、一連の Service Bus エンティティに対するスコープ コンテナーの役割を果たします。 新しい名前空間に、すべての Service Bus アカウントについて一意の名前を指定します。 

### <a name="install-the-nuget-package"></a>NuGet パッケージのインストール
Service Bus の名前空間を使用するには、アプリケーションは Service Bus アセンブリ (具体的には Microsoft.ServiceBus.dll) を参照する必要があります。 このアセンブリは Microsoft Azure SDK に含まれ、[Azure SDK のダウンロード ページ](https://azure.microsoft.com/downloads/)からダウンロードできます。 ただし、Service Bus API を取得し、Service Bus 依存関係をすべて備えたアプリケーションを構成する最も簡単な方法は、[Service Bus NuGet パッケージ](https://www.nuget.org/packages/WindowsAzure.ServiceBus)です。

### <a name="create-the-queue"></a>キューを作成する
Service Bus メッセージング エンティティ (キューおよびトピックのパブリッシュ/サブスクライブ) の管理は、[NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) クラスを使用して行われます。 Service Bus は、[Shared Access Signature (SAS)](service-bus-sas.md) ベースのセキュリティ モデルを使用します。 [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) クラスは、いくつかの既知のトークン プロバイダーを返すファクトリ メソッドが組み込まれたセキュリティ トークン プロバイダーを表します。 SAS の資格情報を保持するには、[CreateSharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider#Microsoft_ServiceBus_TokenProvider_CreateSharedAccessSignatureTokenProvider_System_String_) メソッドを使用します。 その後、Service Bus 名前空間のベース アドレスとトークン プロバイダーを使用して、[NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) インスタンスが作成されます。

[NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) クラスには、メッセージング エンティティの作成、列挙、削除を実行するためのメソッドが用意されています。 ここでは、[NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) インスタンスを作成し、それを使用して **DataCollectionQueue** キューを作成するコードを示します。

```csharp
Uri uri = ServiceBusEnvironment.CreateServiceUri("sb", 
                "test-blog", string.Empty);
string name = "RootManageSharedAccessKey";
string key = "abcdefghijklmopqrstuvwxyz";

TokenProvider tokenProvider = 
    TokenProvider.CreateSharedAccessSignatureTokenProvider(name, key);
NamespaceManager namespaceManager = 
    new NamespaceManager(uri, tokenProvider);
namespaceManager.CreateQueue("DataCollectionQueue");
```

キューのプロパティを調整できる [CreateQueue](/dotnet/api/microsoft.servicebus.namespacemanager#Microsoft_ServiceBus_NamespaceManager_CreateQueue_System_String_) メソッドのオーバーロードがあることに注意してください。 たとえば、キューに送信されるメッセージの既定の有効期限 (TTL) の値を設定できます。

### <a name="send-messages-to-the-queue"></a>キューへのメッセージの送信
Service Bus のエンティティに対して実行時の操作を行う場合 (メッセージの送受信など)、アプリケーションではまず [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) オブジェクトを作成する必要があります。 [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) クラスと同じように、[MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) インスタンスもサービスの名前空間のベース アドレスとトークン プロバイダーから作成されます。

```csharp
 BrokeredMessage bm = new BrokeredMessage(salesData);
 bm.Label = "SalesReport";
 bm.Properties["StoreName"] = "Redmond";
 bm.Properties["MachineID"] = "POS_1";
```

Service Bus キューに送信されたメッセージ (および Service Bus キューから受信したメッセージ) は、[BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) クラスのインスタンスになります。 このクラスは、標準的なプロパティのセット ([Label](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Label)、[TimeToLive](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_TimeToLive) などの)、アプリケーションのプロパティの保持に使用するディクショナリ、任意のアプリケーション データの本体で構成されます。 アプリケーションでは、任意のシリアル化可能なオブジェクトを渡すことによって本体を設定できます (次の例では、POS 端末からの売上データを表す **SalesData** オブジェクトを渡しています)。オブジェクトをシリアル化するには、[DataContractSerializer](https://msdn.microsoft.com/library/system.runtime.serialization.datacontractserializer.aspx) を使用します。 この方法に代わって、[Stream](https://msdn.microsoft.com/library/system.io.stream.aspx) オブジェクトを提供することもできます。

特定のキュー (この例では **DataCollectionQueue**) にメッセージを送信する最も簡単な方法は、[CreateMessageSender](/dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_CreateMessageSender_System_String_) を使用して [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) インスタンスから [MessageSender](/dotnet/api/microsoft.servicebus.messaging.messagesender) オブジェクトを直接作成することです。

```csharp
MessageSender sender = factory.CreateMessageSender("DataCollectionQueue");
sender.Send(bm);
```

### <a name="receiving-messages-from-the-queue"></a>キューからのメッセージの受信
キューからメッセージを受け取るには、[MessageReceiver](/dotnet/api/microsoft.servicebus.messaging.messagereceiver) オブジェクトを使用できます。このオブジェクトは、[CreateMessageReceiver](/dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_CreateMessageReceiver_System_String_) を使用して [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) から直接作成します。 メッセージ レシーバーは、**ReceiveAndDelete** と **PeekLock** という&2; つの異なるモードで動作できます。 [ReceiveMode](/dotnet/api/microsoft.servicebus.messaging.receivemode) は、[CreateMessageReceiver](/dotnet/api/microsoft.servicebus.messaging.messagingfactory?redirectedfrom=MSDN#Microsoft_ServiceBus_Messaging_MessagingFactory_CreateMessageReceiver_System_String_Microsoft_ServiceBus_Messaging_ReceiveMode_) の呼び出しへのパラメーターとして、メッセージ レシーバーの作成時に設定されます。

**ReceiveAndDelete** モードでは、受信は単発の操作です。つまり、Service Bus は要求を受信すると、メッセージを読み取り中としてマークしてアプリケーションに返します。 **ReceiveAndDelete** モードは最もシンプルなモデルであり、障害が発生した場合にアプリケーション側でメッセージを処理しないことを許容できるシナリオに最適です。 このことを理解するために、コンシューマーが受信要求を発行した後で、メッセージを処理する前にクラッシュしたというシナリオを考えてみましょう。 Service Bus がメッセージを読み取り中としてマークするため、アプリケーションは、再起動してメッセージの読み取りを再開すると、クラッシュ前に読み取られていたメッセージを見落とすことになります。

**PeekLock** モードでは、メッセージの受信処理が&2; 段階の動作になり、メッセージが失われることが許容できないアプリケーションに対応することができます。 Service Bus は要求を受け取ると、次に読み取られるメッセージを検索して、他のコンシューマーが受信できないようロックしてから、アプリケーションにメッセージを返します。 アプリケーションがメッセージの処理を終えた後 (または後で処理するために確実に保存した後)、受信したメッセージに対して [Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Complete) を呼び出して受信処理の第&2; 段階を完了します。 Service Bus は、[Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Complete) の呼び出しを確認すると、メッセージを読み取り済みとしてマークします。

結果としては他に&2; つの可能性があります。 第&1; に、アプリケーションがなんらかの理由によってメッセージを処理できない場合には、受信したメッセージに対して ([Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Complete) の代わりに) [Abandon](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Abandon) を呼び出すことができます。 このメソッドが呼び出されると、Service Bus によってメッセージのロックが解除され、同じコンシューマーまたは競合する別のコンシューマーが再度そのメッセージを受信できるようになります。 第&2; に、ロックに関連付けられたタイムアウトがあります。アプリケーションがクラッシュした場合など、ロックがタイムアウトになる前にアプリケーションがメッセージを処理できない場合は、Service Bus によってメッセージのロックが解除され、再度受信できるようになります (基本的に既定で [Abandon](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Abandon) 操作を実行します)。

メッセージが処理された後、[Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Complete) 要求が発行される前にアプリケーションがクラッシュした場合は、アプリケーションが再起動する際にメッセージが再配信されます。 一般に、この動作は "*1 回以上*" の処理と呼ばれます。 つまり、すべてのメッセージが&1; 回以上処理されますが、特定の状況では、同じメッセージが再配信される可能性があります。 重複した処理を許されないシナリオの場合は、アプリケーションで重複を検出する追加ロジックが必要です。 これは、メッセージの [MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId) プロパティを使用して実現できます。 このプロパティの値は、配信の試行を通じて変化しません。 これは "*厳密に&1; 回*" の処理と呼ばれます。

次に示すコードは、**PeekLock** モードを使用してメッセージを受信および処理します。このモードは、[ReceiveMode](/dotnet/api/microsoft.servicebus.messaging.receivemode) の値が明示的に提供されない場合の既定値です。

```csharp
MessageReceiver receiver = factory.CreateMessageReceiver("DataCollectionQueue");
BrokeredMessage receivedMessage = receiver.Receive();
try
{
    ProcessMessage(receivedMessage);
    receivedMessage.Complete();
}
catch (Exception e)
{
    receivedMessage.Abandon();
}
```

### <a name="use-the-queue-client"></a>キュー クライアントの使用
このセクションのこれまでの例では、[MessageSender](/dotnet/api/microsoft.servicebus.messaging.messagesender) および [MessageReceiver](/dotnet/api/microsoft.servicebus.messaging.messagereceiver) オブジェクトを [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) から直接作成し、キューとの間でメッセージを送受信しました。 他の方法として、[QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient) オブジェクトを使用するものがあります。このオブジェクトは、送信と受信両方の操作に加えて、セッションなどのさらに高度な機能をサポートします。

```csharp
QueueClient queueClient = factory.CreateQueueClient("DataCollectionQueue");
queueClient.Send(bm);

BrokeredMessage message = queueClient.Receive();

try
{
    ProcessMessage(message);
    message.Complete();
}
catch (Exception e)
{
    message.Abandon();
} 
```

## <a name="next-steps"></a>次のステップ
このトピックではキューの基本を説明しました。次に、「[Service Bus のトピックとサブスクリプションを使用するアプリケーションを作成する](service-bus-create-topics-subscriptions.md)」で Service Bus のトピックとサブスクリプションのパブリッシュ/サブスクライブ機能の使用を学習してください。


