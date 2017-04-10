---
title: "Azure Service Bus トピックの使用 (.NET) | Microsoft Docs"
description: "Azure における Service Bus のトピックとサブスクリプションを .NET で使用する方法について学習します。 コード サンプルは .NET アプリケーション向けに作成されています。"
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 31d0bc29-6524-4b1b-9c7f-aa15d5a9d3b4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: get-started-article
ms.date: 03/23/2017
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: bec18e91ef8798a791d4b1fe93bd529593197e01
ms.lasthandoff: 03/24/2017


---
# <a name="how-to-use-service-bus-topics-and-subscriptions"></a>Service Bus のトピックとサブスクリプションの使用方法
[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

この記事では、Service Bus のトピックとサブスクリプションの使用方法について説明します。 サンプルは C# で記述され、.NET API を利用しています。 ここでは、トピックとサブスクリプションの作成、サブスクリプション フィルターの作成、トピックへのメッセージの送信、サブスクリプションからのメッセージの受信、トピックとサブスクリプションの削除などのシナリオについて説明します。 トピックとサブスクリプションの詳細については、「[次のステップ](#next-steps)」セクションを参照してください。

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

[!INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

## <a name="configure-the-application-to-use-service-bus"></a>Service Bus を使用するためのアプリケーションの構成
Service Bus を使用するアプリケーションを作成するときには、Service Bus アセンブリに対する参照を追加して、対応する名前空間を含める必要があります。 これを行う最も簡単な方法は、適切な [NuGet](https://www.nuget.org) パッケージをダウンロードすることです。

## <a name="get-the-service-bus-nuget-package"></a>Service Bus NuGet パッケージの取得
[Service Bus NuGet パッケージ](https://www.nuget.org/packages/WindowsAzure.ServiceBus)は、必要な Service Bus 依存関係をすべて備えたアプリケーションを構成する最も簡単な方法です。 Service Bus NuGet パッケージをプロジェクトにインストールするには、次のステップを行います。

1. [ソリューション エクスプローラー] で、**[参照]** を右クリックし、**[NuGet パッケージの管理]** をクリックします。
2. **[参照]** をクリックし、"Azure Service Bus" を検索して、**Microsoft Azure Service Bus** の項目を選択します。 **[インストール]** をクリックし、インストールが完了したら、このダイアログ ボックスを閉じます。
   
   ![][7]

これで、Service Bus に対応するコードを作成する準備ができました。

## <a name="create-a-service-bus-connection-string"></a>Service Bus の接続文字列の作成
Service Bus では、接続文字列を使用してエンドポイントと資格情報を格納します。 接続文字列は、ハードコーディングするのではなく、構成ファイルの中で指定します。

* Azure サービスを使用するときには、Azure サービス構成システム (.csdef ファイルと .cscfg ファイル) を使用して接続文字列を格納することをお勧めします。
* Azure Websites または Azure Virtual Machines を使用する場合には、.NET 構成システム (Web.config ファイルなど) を使用して接続文字列を格納することをお勧めします。

いずれの場合も、この記事で後ほど説明する `CloudConfigurationManager.GetSetting` メソッドを使用して接続文字列を取得できます。

### <a name="configure-your-connection-string"></a>接続文字列の構成
サービス構成メカニズムを使用すると、アプリケーションを再デプロイしなくても [Azure Portal][Azure portal] から構成設定を動的に変更できます。 たとえば、次の例に示すように、サービス定義 (**.csdef**) ファイルに `Setting` ラベルを追加します。

```xml
<ServiceDefinition name="Azure1">
...
    <WebRole name="MyRole" vmsize="Small">
        <ConfigurationSettings>
            <Setting name="Microsoft.ServiceBus.ConnectionString" />
        </ConfigurationSettings>
    </WebRole>
...
</ServiceDefinition>
```

次に、サービス構成 (.cscfg) ファイルで値を指定します。

```xml
<ServiceConfiguration serviceName="Azure1">
...
    <Role name="MyRole">
        <ConfigurationSettings>
            <Setting name="Microsoft.ServiceBus.ConnectionString"
                     value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
        </ConfigurationSettings>
    </Role>
...
</ServiceConfiguration>
```

前に説明したように、ポータルから取得した Shared Access Signature (SAS) のキー名とキー値を使用します。

### <a name="configure-your-connection-string-when-using-azure-websites-or-azure-virtual-machines"></a>Azure Websites サイトまたは Azure Virtual Machines を使用する場合の接続文字列の構成
Websites または Virtual Machines を使用する場合には、.NET 構成システム (Web.config など) を使用することをお勧めします。 `<appSettings>` 要素を使用して接続文字列を格納します。

```xml
<configuration>
    <appSettings>
        <add key="Microsoft.ServiceBus.ConnectionString"
             value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
    </appSettings>
</configuration>
```

前に説明したように、[Azure Portal][Azure portal] から取得した SAS 名とキー値を使用します。

## <a name="create-a-topic"></a>トピックを作成する
[NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) クラスを使用して Service Bus のトピックとサブスクリプションに対する管理操作を実行できます。 このクラスにはトピックの作成、列挙、削除のためのメソッドが用意されています。

次の例では、Azure の `CloudConfigurationManager` クラスと接続文字列を使用して `NamespaceManager` オブジェクトを作成します。この接続文字列は、Service Bus 名前空間のベース アドレスと、それを管理するためのアクセス許可を備えた適切な SAS 資格情報で構成されます。 この接続文字列は、次のようになっています。

```xml
Endpoint=sb://<yourNamespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<yourKey>
```

前のセクションの構成設定であれば、次の例を使用します。

```csharp
// Create the topic if it does not exist already.
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.TopicExists("TestTopic"))
{
    namespaceManager.CreateTopic("TestTopic");
}
```

[CreateTopic](/dotnet/api/microsoft.servicebus.namespacemanager) メソッドにはオーバーロードがあり、トピックのプロパティを設定できるようになっています。たとえば、トピックに送信されるメッセージに対して既定の有効期間 (TTL) 値が適用されるように設定することができます。 このような設定は、[TopicDescription](/dotnet/api/microsoft.servicebus.messaging.topicdescription) クラスを使用して適用します。 次の例では、名前が **TestTopic**、最大サイズが 5 GB、既定のメッセージ TTL が 1 分であるトピックを作成する方法を示します。

```csharp
// Configure Topic Settings.
TopicDescription td = new TopicDescription("TestTopic");
td.MaxSizeInMegabytes = 5120;
td.DefaultMessageTimeToLive = new TimeSpan(0, 1, 0);

// Create a new Topic with custom settings.
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.TopicExists("TestTopic"))
{
    namespaceManager.CreateTopic(td);
}
```

> [!NOTE]
> [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) オブジェクトで [TopicExists](/dotnet/api/microsoft.servicebus.namespacemanager#Microsoft_ServiceBus_NamespaceManager_TopicExists_System_String_) メソッドを使用することで、指定した名前のトピックが名前空間に既に存在するかどうかを確認できます。
> 
> 

## <a name="create-a-subscription"></a>サブスクリプションの作成
トピック サブスクリプションは、[NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) クラスを使用して作成することもできます。 サブスクリプションを指定し、サブスクリプションの仮想キューに渡すメッセージを制限するフィルターを設定できます。

> [!IMPORTANT]
> サブスクリプションでメッセージを受信するためには、トピックにメッセージを送信する前に、そのサブスクリプションを作成しておく必要があります。 トピックに対するサブスクリプションが存在しない場合、それらのメッセージはトピックで破棄されます。
> 
> 

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>既定の (MatchAll) フィルターを適用したサブスクリプションの作成
**MatchAll** フィルターは、新しいサブスクリプションの作成時にフィルターが指定されていない場合に使用される既定のフィルターです。 **MatchAll** フィルターを使用すると、トピックに発行されたすべてのメッセージがサブスクリプションの仮想キューに置かれます。 次の例では、"AllMessages" という名前のサブスクリプションを作成し、既定の **MatchAll** フィルターを使用します。

```csharp
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.SubscriptionExists("TestTopic", "AllMessages"))
{
    namespaceManager.CreateSubscription("TestTopic", "AllMessages");
}
```

### <a name="create-subscriptions-with-filters"></a>フィルターを適用したサブスクリプションの作成
トピックに送信されたメッセージのうち、特定のトピック サブスクリプション内に表示されるメッセージを指定できるフィルターを設定することもできます。

サブスクリプションでサポートされるフィルターのうち、最も柔軟性の高いものが、SQL92 のサブセットを実装する [SqlFilter][SqlFilter] クラスです。 SQL フィルターは、トピックに発行されるメッセージのプロパティに対して適用されます。 SQL フィルターで使用できる式の詳細については、[SqlFilter.SqlExpression][SqlFilter.SqlExpression] 構文の説明を参照してください。

次の例では、**HighMessages** という名前のサブスクリプションを作成し、[SqlFilter][SqlFilter] オブジェクトを適用します。このフィルターでは、カスタム プロパティ **MessageNumber** が 3 を超えるメッセージのみが選択されます。

```csharp
// Create a "HighMessages" filtered subscription.
SqlFilter highMessagesFilter =
   new SqlFilter("MessageId > 3");

namespaceManager.CreateSubscription("TestTopic",
   "HighMessages",
   highMessagesFilter);
```

同様に、次の例では、**LowMessages** という名前のサブスクリプションを作成し、[SqlFilter][SqlFilter] を適用します。このフィルターでは、**MessageNumber** プロパティが 3 以下のメッセージのみが選択されます。

```csharp
// Create a "LowMessages" filtered subscription.
SqlFilter lowMessagesFilter =
   new SqlFilter("MessageId <= 3");

namespaceManager.CreateSubscription("TestTopic",
   "LowMessages",
   lowMessagesFilter);
```

これでメッセージが `TestTopic` に送信されると、そのメッセージは **AllMessages** トピック サブスクリプションをサブスクライブした受信者に必ず配信され、さらにメッセージの内容に応じて、**HighMessages** と **LowMessages** トピック サブスクリプションをサブスクライブしている受信者に対して選択的に配信されます。

## <a name="send-messages-to-a-topic"></a>メッセージをトピックに送信する
アプリケーションで Service Bus トピックにメッセージを送信するには、接続文字列を使用して [TopicClient](/dotnet/api/microsoft.servicebus.messaging.topicclient) オブジェクトを作成します。

次のコードでは、前の例で [CreateFromConnectionString](/dotnet/api/microsoft.servicebus.messaging.topicclient#Microsoft_ServiceBus_Messaging_TopicClient_CreateFromConnectionString_System_String_System_String_) API を使用して作成した **TestTopic** トピックのために [TopicClient](/dotnet/api/microsoft.servicebus.messaging.topicclient) オブジェクトを作成する方法を示しています。

```csharp
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

TopicClient Client =
    TopicClient.CreateFromConnectionString(connectionString, "TestTopic");

Client.Send(new BrokeredMessage());
```

Service Bus トピックに送信されたメッセージは、[BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) クラスのインスタンスです。 **BrokeredMessage** オブジェクトには、([Label](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Label)、[TimeToLive](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_TimeToLive) などの) 標準的なプロパティ、アプリケーションに特有のカスタム プロパティの保持に使用するディクショナリ、任意のアプリケーション データの本体が備わっています。 アプリケーションでは、**BrokeredMessage** オブジェクトのコンストラクターにシリアル化可能なオブジェクトを渡すことによってメッセージの本文を設定できます。その後で、適切な **DataContractSerializer** を使用してオブジェクトをシリアル化します。 この方法に代わって、**System.IO.Stream** オブジェクトを使用できます。

以下の例では、上のコード例で取得した **TestTopic** [TopicClient](/dotnet/api/microsoft.servicebus.messaging.topicclient) オブジェクトにテスト メッセージを 5 件送信する方法を示しています。 各メッセージの [MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId) プロパティ値がループの反復回数に応じて変化することに注目してください (これによってメッセージを受信するサブスクリプションが決定されます)。

```csharp
for (int i=0; i<5; i++)
{
  // Create message, passing a string message for the body.
  BrokeredMessage message = new BrokeredMessage("Test message " + i);

  // Set additional custom app-specific property.
  message.Properties["MessageId"] = i;

  // Send message to the topic.
  Client.Send(message);
}
```

Service Bus トピックでサポートされているメッセージの最大サイズは、[Standard レベル](service-bus-premium-messaging.md)では 256 KB、[Premium レベル](service-bus-premium-messaging.md)では 1 MB です。 標準とカスタムのアプリケーション プロパティが含まれるヘッダーの最大サイズは 64 KB です。 1 つのトピックで保持されるメッセージ数に上限はありませんが、1 つのトピックで保持できるメッセージの合計サイズには上限があります。 このトピックのサイズはトピックの作成時に定義します。上限は 5 GB です。 パーティション分割が有効な場合、上限が高くなります。 詳細については、「 [パーティション分割されたメッセージング エンティティ](service-bus-partitioning.md)」を参照してください。

## <a name="how-to-receive-messages-from-a-subscription"></a>サブスクリプションからメッセージを受信する方法
サブスクリプションからメッセージを受信する推奨の方法は、[SubscriptionClient](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient) オブジェクトの使用です。 **SubscriptionClient** オブジェクトには、2 つの動作モードがあります。[*ReceiveAndDelete* と *PeekLock*](/dotnet/api/microsoft.servicebus.messaging.receivemode) です。 **PeekLock** が既定値です。

**ReceiveAndDelete** モードを使用する場合、受信は 1 回ずつの動作になります。つまり、Service Bus は、サブスクリプション内のメッセージに対する読み取り要求を受け取ると、メッセージを読み取り中としてマークし、アプリケーションに返します。 **ReceiveAndDelete** モードは最もシンプルなモデルであり、障害発生時にアプリケーション側でメッセージを処理しないことを許容できるシナリオに最適です。 このことを理解するために、コンシューマーが受信要求を発行した後で、メッセージを処理する前にクラッシュしたというシナリオを考えてみましょう。 Service Bus はメッセージを読み取り済みとしてマークしているため、アプリケーションが再起動してメッセージの読み取りを再開すると、クラッシュ前に読み取られていたメッセージは見落とされることになります。

**PeekLock** モード (既定のモード) では、受信処理が 2 段階の動作になり、メッセージが失われることが許容できないアプリケーションに対応することができます。 Service Bus は要求を受け取ると、次に読み取られるメッセージを検索して、他のコンシューマーが受信できないようロックしてから、アプリケーションにメッセージを返します。 アプリケーションがメッセージの処理を終えた後 (または後で処理するために確実に保存した後)、受信したメッセージに対して [Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Complete) を呼び出して受信処理の第 2 段階を完了します。 Service Bus が **Complete** の呼び出しを確認すると、メッセージが読み取り中としてマークされ、サブスクリプションから削除されます。

次の例では、既定の **PeekLock** モードを使用したメッセージの受信および処理の方法を示しています。 別の [ReceiveMode](/dotnet/api/microsoft.servicebus.messaging.receivemode) 値を指定する場合には、[CreateFromConnectionString](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient#Microsoft_ServiceBus_Messaging_SubscriptionClient_CreateFromConnectionString_System_String_System_String_System_String_Microsoft_ServiceBus_Messaging_ReceiveMode_) に別のオーバーロードを使用できます。 この例では、[OnMessage](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient#Microsoft_ServiceBus_Messaging_SubscriptionClient_OnMessage_System_Action_Microsoft_ServiceBus_Messaging_BrokeredMessage__Microsoft_ServiceBus_Messaging_OnMessageOptions_) コールバックを使用して **HighMessages** サブスクリプションにメッセージが到着するたびに処理しています。

```csharp
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

SubscriptionClient Client =
    SubscriptionClient.CreateFromConnectionString
            (connectionString, "TestTopic", "HighMessages");

// Configure the callback options.
OnMessageOptions options = new OnMessageOptions();
options.AutoComplete = false;
options.AutoRenewTimeout = TimeSpan.FromMinutes(1);

Client.OnMessage((message) =>
{
    try
    {
        // Process message from subscription.
        Console.WriteLine("\n**High Messages**");
        Console.WriteLine("Body: " + message.GetBody<string>());
        Console.WriteLine("MessageID: " + message.MessageId);
        Console.WriteLine("Message Number: " +
            message.Properties["MessageNumber"]);

        // Remove message from subscription.
        message.Complete();
    }
    catch (Exception)
    {
        // Indicates a problem, unlock message in subscription.
        message.Abandon();
    }
}, options);
```

この例では、[OnMessageOptions](/dotnet/api/microsoft.servicebus.messaging.onmessageoptions) オブジェクトを使用して、[OnMessage](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient#Microsoft_ServiceBus_Messaging_SubscriptionClient_OnMessage_System_Action_Microsoft_ServiceBus_Messaging_BrokeredMessage__Microsoft_ServiceBus_Messaging_OnMessageOptions_) コールバックを構成します。 [AutoComplete](/dotnet/api/microsoft.servicebus.messaging.onmessageoptions#Microsoft_ServiceBus_Messaging_OnMessageOptions_AutoComplete) は **false** に設定され、受信したメッセージで [Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Complete) を呼び出すタイミングを手動で制御できるようになります。 [AutoRenewTimeout](/dotnet/api/microsoft.servicebus.messaging.onmessageoptions#Microsoft_ServiceBus_Messaging_OnMessageOptions_AutoRenewTimeout) は 1 分に設定され、クライアントは自動更新機能が終了するまで最大で 1 分間待機し、新しい呼び出しを行ってメッセージを確認します。 このプロパティ値により、メッセージを取得しない課金対象呼び出しをクライアントが行う回数が減ります。

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>アプリケーションのクラッシュと読み取り不能のメッセージを処理する方法
Service Bus には、アプリケーションにエラーが発生した場合や、メッセージの処理に問題がある場合に復旧を支援する機能が備わっています。 受信側のアプリケーションがなんらかの理由によってメッセージを処理できない場合には、受信したメッセージで ([Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Complete) メソッドの代わりに) [Abandon](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Abandon_System_Collections_Generic_IDictionary_System_String_System_Object__) メソッドを呼び出すことができます。 このメソッドが呼び出されると、Service Bus によりサブスクリプション内のメッセージのロックが解除され、メッセージが再度受信できる状態に変わります。このメッセージは、同じコンシューマー側アプリケーションまたは他のコンシューマー側アプリケーションで受信されます。

サブスクリプション内でロックされているメッセージにはタイムアウトも設定されています。アプリケーションがクラッシュした場合など、ロックがタイムアウトになる前にアプリケーションがメッセージの処理に失敗した場合には、Service Bus によってメッセージのロックが自動的に解除され、再度受信できる状態に変わります。

メッセージが処理された後、[Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Complete) 要求が発行される前にアプリケーションがクラッシュした場合は、アプリケーションが再起動する際にメッセージが再配信されます。 一般的に、この動作は "*1 回以上の処理*" と呼ばれます。つまり、すべてのメッセージが 1 回以上処理されますが、特定の状況では、同じメッセージが再配信される可能性があります。 重複処理が許されないシナリオの場合、重複メッセージの配信を扱うロジックをアプリケーションに追加する必要があります。 通常、この問題はメッセージの [MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId) プロパティを使用して対処します。このプロパティは配信が試行された後も同じ値を保持します。

## <a name="delete-topics-and-subscriptions"></a>トピックとサブスクリプションを削除する
次の例では、**TestTopic** という名前のトピックを **HowToSample** サービス名前空間から削除する方法を示しています。

```csharp
// Delete Topic.
namespaceManager.DeleteTopic("TestTopic");
```

トピックを削除すると、そのトピックに登録されたサブスクリプションもすべて削除されます。 サブスクリプションは、個別に削除することもできます。 次のコードでは、**HighMessages** という名前のサブスクリプションを **TestTopic** トピックから削除する方法を示しています。

```csharp
namespaceManager.DeleteSubscription("TestTopic", "HighMessages");
```

## <a name="next-steps"></a>次のステップ
これで、Service Bus のトピックとサブスクリプションの基本を学習できました。さらに詳細な情報が必要な場合は、次のリンク先を参照してください。

* [キュー、トピック、サブスクリプション][Queues, topics, and subscriptions]
* [トピック フィルターのサンプル][Topic filters sample]
* [SqlFilter][SqlFilter] の API のリファレンス
* Service Bus キューとの間でメッセージを送受信する実用アプリケーションの作成: [Service Bus ブローカー メッセージング .NET チュートリアル][Service Bus brokered messaging .NET tutorial]
* Service Bus のサンプル: [Azure のサンプル][Azure samples]からダウンロードするか、[概要](service-bus-samples.md)に関するページを参照してください。

[Azure portal]: https://portal.azure.com

[7]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/getting-started-multi-tier-13.png

[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Topic filters sample]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/TopicFilters
[SqlFilter]: /dotnet/api/microsoft.servicebus.messaging.sqlfilter
[SqlFilter.SqlExpression]: /dotnet/api/microsoft.servicebus.messaging.sqlfilter#Microsoft_ServiceBus_Messaging_SqlFilter_SqlExpression
[Service Bus brokered messaging .NET tutorial]: service-bus-brokered-tutorial-dotnet.md
[Azure samples]: https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=2

