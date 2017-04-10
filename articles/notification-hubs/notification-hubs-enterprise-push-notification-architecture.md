---
title: "Azure Notification Hubs - エンタープライズ環境のプッシュ アーキテクチャ"
description: "エンタープライズ環境での Azure Notification Hubs の使用に関するガイダンス"
services: notification-hubs
documentationcenter: 
author: ysxu
manager: erikre
editor: 
ms.assetid: 903023e9-9347-442a-924b-663af85e05c6
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 06/29/2016
ms.author: yuaxu
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: ae7c1c9644ecfe7fe4ad6e332cc0683a3b5df22f


---
# <a name="enterprise-push-architectural-guidance"></a>エンタープライズ環境のプッシュ アーキテクチャに関するガイダンス
今日の企業における重要事項は、外部のエンド ユーザーや社内の従業員向けのモバイル アプリケーションを作成することへと徐々に変化してきました。 社内にはメインフレームや LoB アプリケーションといった既存のバックエンド システムがありますが、これらをモバイル アプリケーション アーキテクチャへと統合する必要が出てきています。 このガイドでは、一般的なシナリオ向けのソリューションを推奨しながら、この統合を行うために最適な方法について説明します。

必要となるのは、バックエンド システムで特定のイベントが発生した場合に、ユーザーのモバイル アプリケーションにプッシュ通知が送信されるようにすることです。 例: たとえば、iPhone で銀行取引アプリを利用している銀行の顧客が、口座から一定金額以上の引き落としがあった際に通知を受信したいと考えている場合、または、イントラネットの環境において、Windows Phone で予算承認のアプリを利用している財務部門の従業員が、承認要求を受け取った際に通知を受信したいと考えている場合などがあります。

こうした場合、銀行口座や承認処理はバックエンド システムで実行されている可能性が高く、バックエンド システムからユーザーにプッシュを開始する必要があります。 このようなバックエンド システムが複数ある場合は、そのすべてにおいて、イベントによって通知がトリガーされたときにプッシュを実装する同じ種類のロジックを構築する必要があります。 ここで複雑なのは、エンド ユーザーがさまざまな通知をサブスクライブしている場合に、複数のバックエンド システムを単一のプッシュ システムと統合する必要があることです。さらに、たとえばイントラネット モバイル アプリの場合などは、複数のモバイル アプリケーションがあり、その中の 1 つのモバイル アプリケーションがこのような複数のバックエンド システムから通知を受信する可能性があります。 バックエンド システムでは、プッシュのセマンティクスやテクノロジに対応していない、あるいは対応する必要がないため、従来の一般的なソリューションでは、バックエンド システムに対して特定のイベントをポーリングし、クライアントにプッシュ メッセージを送信するコンポーネントを導入していました。
ここでは、Azure Service Bus を使用した、より優れたソリューションであるトピック/サブスクリプション モデルについて説明します。これにより、複雑さを軽減し、スケーラブルなソリューションを実現することができます。

次のセクションでは、このソリューションの全般的なアーキテクチャを紹介します (概論として、複数のモバイル アプリを前提としていますが、モバイル アプリが 1 つしかない場合にも同様に該当します)。

## <a name="architecture"></a>アーキテクチャ
![][1]

このアーキテクチャの図で重要な要素は、トピック/サブスクリプションのプログラミング モデルを提供する Azure Service Bus です。(詳細については、「[Service Bus Pub/Sub programming (Service Bus のトピックとサブスクリプションの使用方法)]」をご覧ください)。 受信者 (この例ではモバイル バックエンド (通常は Mobile Apps へのプッシュを開始する [Azure Mobile Services])) は、バックエンド システムから直接メッセージを受信せず、代わりに [Azure Service Bus] で提供される中間抽象化レイヤーを介して、1 つまたは複数のバックエンド システムからメッセージを受信します。 Service Bus のトピックは、各バックエンド システム (会計、人事、財務など) に作成する必要があります。Service Bus のトピックは基本的に、プッシュ通知としてメッセージの送信を開始する特定の "トピック" です。 バックエンド システムは、これらのトピックにメッセージを送信します。 Service Bus のサブスクリプションを作成することで、モバイル バックエンドがこのような 1 つまたは複数のトピックをサブスクライブできます。 これにより、モバイル バックエンドは対応するバックエンド システムから通知を受信することができます。 モバイル バックエンドは継続的にサブスクリプションのメッセージをリッスンし、メッセージを受信すると、Notification Hubs に通知として送信します。 その後、Notification Hubs は最終的にモバイル アプリにメッセージを配信します。 主要なコンポーネントをまとめると、以下のようになります。

1. バックエンド システム (LoB　またはレガシ システム)
   * Service Bus のトピックを作成
   * メッセージを送信
2. モバイル バックエンド
   * サービスのサブスクリプションを作成
   * (バックエンド システムから) メッセージを受信
   * (Azure Notification Hubs 経由で) クライアントに通知を送信
3. モバイル アプリケーション
   * 通知を受信して表示

### <a name="benefits"></a>メリット:
1. 受信者 (Notification Hubs を経由したモバイル アプリケーションまたはサービス) と送信者 (バックエンド システム) を分離することで、最小限の変更によって追加のバックエンド システムを統合できます。
2. また、複数のモバイル アプリ 1 つまたは複数のバックエンド システムからイベントを受信するシナリオを実現できます。  

## <a name="sample"></a>サンプル:
### <a name="prerequisites"></a>前提条件
概念と一般的な作成および構成手順を理解するために、以下のチュートリアルを完了する必要があります。

1. [Service Bus Pub/Sub programming (Service Bus のトピックとサブスクリプションの使用方法)] - Service Bus のトピックとサブスクリプションの使用方法、トピックとサブスクリプションを格納する名前空間の作成方法、トピックとサブスクリプションへのメッセージの送信/受信方法の詳細について説明しています。
2. [Notification Hubs の使用 - Windows Universal チュートリアル] : Windows ストア アプリを設定し、Notification Hubs を使用して通知を登録してから受信する方法について説明しています。

### <a name="sample-code"></a>コード サンプル
完全なコード サンプルは「 [Notification Hubs のサンプル (英語)]」から入手できます。 このサンプルは、3 つのコンポーネントに分割されています。

1. **EnterprisePushBackendSystem**
   
    a. このプロジェクトは、*WindowsAzure.ServiceBus* Nuget パッケージを使用しており、「[Service Bus Pub/Sub programming (Service Bus のトピックとサブスクリプションの使用方法)]」の内容に基づいています。
   
    b. この単純な C# コンソール アプリでは、モバイル アプリへのメッセージの配信を開始する LoB システムをシミュレートします。
   
        static void Main(string[] args)
        {
            string connectionString =
                CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");
   
            // Create the topic where we will send notifications
            CreateTopic(connectionString);
   
            // Send message
            SendMessage(connectionString);
        }
   
    c. `CreateTopic` は、メッセージを送信する Service Bus のトピックを作成するために使用します。
   
        public static void CreateTopic(string connectionString)
        {
            // Create the topic if it does not exist already
   
            var namespaceManager =
                NamespaceManager.CreateFromConnectionString(connectionString);
   
            if (!namespaceManager.TopicExists(sampleTopic))
            {
                namespaceManager.CreateTopic(sampleTopic);
            }
        }
   
    d. `SendMessage` は、この Service Bus のトピックにメッセージを送信するために使用します。 ここでは、サンプルで使用するために、一連のランダムなメッセージを定期的にトピックに送信します。 通常は、イベントが発生した場合にバックエンド システムがメッセージを送信します。
   
        public static void SendMessage(string connectionString)
        {
            TopicClient client =
                TopicClient.CreateFromConnectionString(connectionString, sampleTopic);
   
            // Sends random messages every 10 seconds to the topic
            string[] messages =
            {
                "Employee Id '{0}' has joined.",
                "Employee Id '{0}' has left.",
                "Employee Id '{0}' has switched to a different team."
            };
   
            while (true)
            {
                Random rnd = new Random();
                string employeeId = rnd.Next(10000, 99999).ToString();
                string notification = String.Format(messages[rnd.Next(0,messages.Length)], employeeId);
   
                // Send Notification
                BrokeredMessage message = new BrokeredMessage(notification);
                client.Send(message);
   
                Console.WriteLine("{0} Message sent - '{1}'", DateTime.Now, notification);
   
                System.Threading.Thread.Sleep(new TimeSpan(0, 0, 10));
            }
        }
2. **ReceiveAndSendNotification**
   
    a. このプロジェクトは、*WindowsAzure.ServiceBus* および *Microsoft.Web.WebJobs.Publish* Nuget パッケージを使用しており、「[Service Bus Pub/Sub programming (Service Bus のトピックとサブスクリプションの使用方法)]」の内容に基づいています。
   
    b. この C# コンソール アプリは、継続的に実行して LoB またはバックエンド システムからのメッセージをリッスンする必要があるため、[Azure WebJobs] として実行します。 これは、モバイル バックエンドの一部になります。
   
        static void Main(string[] args)
        {
            string connectionString =
                     CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");
   
            // Create the subscription which will receive messages
            CreateSubscription(connectionString);
   
            // Receive message
            ReceiveMessageAndSendNotification(connectionString);
        }
   
    c. `CreateSubscription` は、バックエンド システムがメッセージを送信するトピックの Service Bus サブスクリプションを作成するために使用します。 ビジネス シナリオによっては、このコンポーネントは対応するトピックへの 1 つまたは複数のサブスクリプション (一部は人事部門のシステムからメッセージを受信し、一部は財務部門のシステムからメッセージを受信するなど)　を作成します。
   
        static void CreateSubscription(string connectionString)
        {
            // Create the subscription if it does not exist already
            var namespaceManager =
                NamespaceManager.CreateFromConnectionString(connectionString);
   
            if (!namespaceManager.SubscriptionExists(sampleTopic, sampleSubscription))
            {
                namespaceManager.CreateSubscription(sampleTopic, sampleSubscription);
            }
        }
   
    d. ReceiveMessageAndSendNotification は、サブスクリプションを使用してトピックからメッセージを読み取り、読み取りが正常に実行された場合に、Azure Notification Hubs を使用してモバイル アプリケーションに送信する通知 (サンプル シナリオでは Windows ネイティブのトースト通知) を作成するために使用します。
   
        static void ReceiveMessageAndSendNotification(string connectionString)
        {
            // Initialize the Notification Hub
            string hubConnectionString = CloudConfigurationManager.GetSetting
                    ("Microsoft.NotificationHub.ConnectionString");
            hub = NotificationHubClient.CreateClientFromConnectionString
                    (hubConnectionString, "enterprisepushservicehub");
   
            SubscriptionClient Client =
                SubscriptionClient.CreateFromConnectionString
                        (connectionString, sampleTopic, sampleSubscription);
   
            Client.Receive();
   
            // Continuously process messages received from the subscription
            while (true)
            {
                BrokeredMessage message = Client.Receive();
                var toastMessage = @"<toast><visual><binding template=""ToastText01""><text id=""1"">{messagepayload}</text></binding></visual></toast>";
   
                if (message != null)
                {
                    try
                    {
                        Console.WriteLine(message.MessageId);
                        Console.WriteLine(message.SequenceNumber);
                        string messageBody = message.GetBody<string>();
                        Console.WriteLine("Body: " + messageBody + "\n");
   
                        toastMessage = toastMessage.Replace("{messagepayload}", messageBody);
                        SendNotificationAsync(toastMessage);
   
                        // Remove message from subscription
                        message.Complete();
                    }
                    catch (Exception)
                    {
                        // Indicate a problem, unlock message in subscription
                        message.Abandon();
                    }
                }
            }
        }
        static async void SendNotificationAsync(string message)
        {
            await hub.SendWindowsNativeNotificationAsync(message);
        }
   
    e. これを **WebJobs** として発行するには、Visual Studio でソリューションを右クリックして **[WebJob として発行]** を選択します。
   
    ![][2]
   
    f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 発行プロファイルを選択し、この WebJobs をホストする Azure Websites が既に存在していない場合には新規作成してから **[発行]** を選択します。
   
    ![][3]
   
    g. ジョブを [連続実行する] ように構成すると、[Azure クラシック ポータル]にログインしたときに、以下のような画面が表示されます。
   
    ![][4]
3. **EnterprisePushMobileApp**
   
    a. この Windows ストア アプリケーションでは、モバイル　バックエンドの一部として実行されている WebJobs からトースト通知を受信して表示します。 これは、「[Notification Hubs の使用 - Windows Universal チュートリアル]」の内容に基づいています。  
   
    b. アプリケーションでトースト通知の受信が有効になっていることを確認します。
   
    c. (*HubName* および *DefaultListenSharedAccessSignature* を置換してから) アプリの起動時に、以下の Notification Hubs の登録コードが呼び出されることを確認します。
   
        private async void InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
   
            var hub = new NotificationHub("[HubName]", "[DefaultListenSharedAccessSignature]");
            var result = await hub.RegisterNativeAsync(channel.Uri);
   
            // Displays the registration ID so you know it was successful
            if (result.RegistrationId != null)
            {
                var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }

### <a name="running-sample"></a>サンプルの実行:
1. WebJobs が正常に実行されていて、「連続実行」するように設定されていることを確認します。
2. **EnterprisePushMobileApp** を実行して、Windows ストア アプリを開始します。
3. LoB バックエンドをシミュレートする **EnterprisePushBackendSystem** コンソール アプリケーションを実行して、メッセージの送信を開始すると、以下のようなトースト通知が表示されます。
   
    ![][5]
4. メッセージは、最初に WebJobs の Service Bus のサブスクリプションによって監視されていた Service Bus のトピックに送信されます。 メッセージを受信すると、通知が作成されてモバイル アプリに送信されます。 [Azure クラシック ポータル] で WebJobs の [ログ] リンクをクリックすると、WebJobs ログで処理を確認することができます。
   
    ![][6]

<!-- Images -->
[1]: ./media/notification-hubs-enterprise-push-architecture/architecture.png
[2]: ./media/notification-hubs-enterprise-push-architecture/WebJobsContextMenu.png
[3]: ./media/notification-hubs-enterprise-push-architecture/PublishAsWebJob.png
[4]: ./media/notification-hubs-enterprise-push-architecture/WebJob.png
[5]: ./media/notification-hubs-enterprise-push-architecture/Notifications.png
[6]: ./media/notification-hubs-enterprise-push-architecture/WebJobsLog.png

<!-- Links -->
[Notification Hubs のサンプル (英語)]: https://github.com/Azure/azure-notificationhubs-samples
[Azure Mobile Services]: http://azure.microsoft.com/documentation/services/mobile-services/
[Azure Service Bus]: http://azure.microsoft.com/documentation/articles/fundamentals-service-bus-hybrid-solutions/
[Service Bus Pub/Sub programming (Service Bus のトピックとサブスクリプションの使用方法)]: http://azure.microsoft.com/documentation/articles/service-bus-dotnet-how-to-use-topics-subscriptions/
[Azure WebJobs]: http://azure.microsoft.com/documentation/articles/web-sites-create-web-jobs/
[Notification Hubs の使用 - Windows Universal チュートリアル]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[Azure クラシック ポータル]: https://manage.windowsazure.com/



<!--HONumber=Nov16_HO3-->


