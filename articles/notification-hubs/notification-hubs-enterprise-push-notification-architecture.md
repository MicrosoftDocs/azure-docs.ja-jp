---
title: Azure Notification Hubs - エンタープライズ環境のプッシュ アーキテクチャ
description: エンタープライズ環境での Azure Notification Hubs の使用について
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 903023e9-9347-442a-924b-663af85e05c6
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 018315b7ed468e24fb922337848d14703ffdcd4d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89003628"
---
# <a name="enterprise-push-architectural-guidance"></a>エンタープライズ環境のプッシュ アーキテクチャに関するガイダンス

今日の企業における重要事項は、外部のエンド ユーザーや社内の従業員向けのモバイル アプリケーションを作成することへと徐々に変化してきました。 そこには、メインフレームや一部の LOB アプリケーションなどの既存のバックエンド システムが存在しますが、これらをモバイル アプリケーション アーキテクチャに統合する必要があります。 このガイドでは、一般的なシナリオに対する可能性のあるソリューションを推奨しながら、この統合を実行するための最適な方法について説明します。

必要となるのは、バックエンド システムで特定のイベントが発生した場合に、ユーザーのモバイル アプリケーションにプッシュ通知が送信されるようにすることです。 たとえば、iPhone 上で銀行のバンキング アプリを使用している銀行の顧客が、口座から一定金額以上の引き落としがあったときに通知を受信したいと考えている場合や、Windows Phone 上で予算承認アプリを使用している財務部門の従業員が、承認の要求が受信されたときに通知を受信したいと考えているイントラネットのシナリオがあります。

銀行口座または承認処理は、ユーザーへのプッシュを開始する必要のある何らかのバックエンド システムで実行されている可能性があります。 このようなバックエンド システムは複数存在する可能性があり、これらのすべてで、イベントが通知をトリガーしたときにプッシュを行う同じ種類のロジックを構築する必要があります。 ここでの複雑さは、複数のバックエンド システムをまとめて、エンド ユーザーがさまざまな通知にサブスクライブし、かつ複数のモバイル アプリケーションが存在する可能性のある 1 つのプッシュ システムと統合することにあります。 たとえば、1 つのモバイル アプリケーションがこのような複数のバックエンド システムから通知を受信する可能性のあるイントラネット モバイル アプリがあります。 バックエンド システムはプッシュのセマンティクスやテクノロジを認識していないか、または認識する必要がないため、ここでの従来からの一般的なソリューションでは、目的のイベントがないかどうかバックエンド システムをポーリングし、クライアントにプッシュ メッセージを送信する役割を果たすコンポーネントが導入されていました。

より優れたソリューションとして、複雑さを軽減しながら、ソリューションをスケーラブルにする Azure Service Bus トピック/サブスクリプション モデルの使用があります。

次のセクションでは、このソリューションの全般的なアーキテクチャを紹介します (概論として、複数のモバイル アプリを前提としていますが、モバイル アプリが 1 つしかない場合にも同様に該当します)。

## <a name="architecture"></a>アーキテクチャ

![エンタープライズ アーキテクチャの図。イベント、サブスクリプション、プッシュ メッセージを通過する流れになっています。][1]

このアーキテクチャ図の重要な部分は、トピック/サブスクリプション プログラミング モデルを提供する Azure Service Bus です (詳細については、[Service Bus Pub/Sub programming (Service Bus のトピックとサブスクリプションの使用方法) (Service Bus のトピックとサブスクリプションの使用方法)]に関するページを参照)。 受信者 (この場合は、モバイル バックエンド。通常は、モバイル アプリへのプッシュを開始する [Azure Mobile Service]) は、バックエンド システムから直接にではなく、代わりに、[Azure Service Bus] によって提供される中間の抽象化レイヤーからメッセージを受信します。これにより、モバイル バックエンドは 1 つ以上のバックエンド システムからメッセージを受信できるようになります。 Service Bus トピックは、バックエンド システムごとに作成する必要があります (会計、人事、財務など)。これは基本的に、メッセージのプッシュ通知としての送信を開始する目的とする "トピック" です。 バックエンド システムは、これらのトピックにメッセージを送信します。 Service Bus のサブスクリプションを作成することで、モバイル バックエンドがこのような 1 つまたは複数のトピックをサブスクライブできます。 これにより、モバイル バックエンドは対応するバックエンド システムから通知を受信できます。 モバイル バックエンドは継続的にサブスクリプションのメッセージをリッスンし、メッセージを受信すると、Notification Hubs に通知として送信します。 その後、通知ハブは最終的に、そのメッセージをモバイル アプリに配信します。 主要なコンポーネントの一覧を次に示します。

1. バックエンド システム (LoB　またはレガシ システム)
   * Service Bus のトピックを作成
   * メッセージを送信
1. モバイル バックエンド
   * サービスのサブスクリプションを作成
   * (バックエンド システムから) メッセージを受信
   * (Azure Notification Hubs 経由で) クライアントに通知を送信
1. モバイル アプリケーション
   * 通知を受信して表示

### <a name="benefits"></a>メリット

1. 受信者 (Notification Hubs を経由したモバイル アプリケーションまたはサービス) と送信者 (バックエンド システム) を分離することで、最小限の変更によって追加のバックエンド システムを統合できます。
1. これはまた、複数のモバイル アプリのシナリオで、1 つ以上のバックエンド システムからイベントを受信できるようにします。  

## <a name="sample"></a>サンプル

### <a name="prerequisites"></a>前提条件

これらの概念や一般的な作成および構成手順に精通するために、次のチュートリアルを完了してください。

1. [Service Bus Pub/Sub programming (Service Bus のトピックとサブスクリプションの使用方法) (Service Bus のトピックとサブスクリプションの使用方法)] - このチュートリアルでは、Service Bus トピック/サブスクリプションの操作の詳細、トピック/サブスクリプションを格納する名前空間を作成する方法、そこからメッセージを送受信する方法について説明します。
2. [Notification Hubs の使用 - Windows ユニバーサル チュートリアル] - このチュートリアルでは、Windows ストア アプリを設定し、Notification Hubs を使用して登録してから通知を受信する方法について説明します。

### <a name="sample-code"></a>サンプル コード

完全なコード サンプルは「 [Notification Hubs のサンプル (英語)]」から入手できます。 このサンプルは、3 つのコンポーネントに分割されています。

1. **EnterprisePushBackendSystem**

    a. このプロジェクトは **WindowsAzure.ServiceBus** NuGet パッケージを使用し、「[Service Bus Pub/Sub programming (Service Bus のトピックとサブスクリプションの使用方法) (Service Bus のトピックとサブスクリプションの使用方法)]」に基づいています。

    b. このアプリケーションは、LoB システムをシミュレートする単純な C# コンソール アプリであり、メッセージのモバイル アプリへの配信を開始します。

    ```csharp
    static void Main(string[] args)
    {
        string connectionString =
            CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

        // Create the topic
        CreateTopic(connectionString);

        // Send message
        SendMessage(connectionString);
    }
    ```

    c. `CreateTopic` は、Service Bus トピックを作成するために使用されます。

    ```csharp
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
    ```

    d. `SendMessage` は、この Service Bus のトピックにメッセージを送信するために使用します。 このコードは単純に、このサンプルの目的として、一連のランダムなメッセージを定期的にトピックに送信します。 通常は、イベントの発生時にメッセージを送信するバックエンド システムが存在します。

    ```csharp
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
    ```
2. **ReceiveAndSendNotification**

    a. このプロジェクトは *WindowsAzure.ServiceBus* および **Microsoft.Web.WebJobs.Publish** NuGet パッケージを使用し、「[Service Bus Pub/Sub programming (Service Bus のトピックとサブスクリプションの使用方法) (Service Bus のトピックとサブスクリプションの使用方法)]」に基づいています。

    b. 次のコンソール アプリは、LoB/バックエンド システム メッセージをリッスンするために継続的に実行する必要があるため、[Azure WebJob] として実行されます。 このアプリケーションは、モバイル バックエンドの一部です。

    ```csharp
    static void Main(string[] args)
    {
        string connectionString =
                 CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

        // Create the subscription that receives messages
        CreateSubscription(connectionString);

        // Receive message
        ReceiveMessageAndSendNotification(connectionString);
    }
    ```

    c. `CreateSubscription` は、バックエンド システムがメッセージを送信するトピックの Service Bus サブスクリプションを作成するために使用されます。 ビジネス シナリオに応じて、このコンポーネントは対応するトピックへの 1 つ以上のサブスクリプションを作成します (たとえば、一部は人事システムから、また一部は財務システムからメッセージを受信するなど)。

    ```csharp
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
    ```

    d. `ReceiveMessageAndSendNotification` は、サブスクリプションを使用してトピックからメッセージを読み取り、読み取りが正常に実行された場合に、Azure Notification Hubs を使用してモバイル アプリケーションに送信する通知 (サンプル シナリオでは Windows ネイティブのトースト通知) を作成するために使用します。

    ```csharp
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
    ```

    e. このアプリを **WebJob** として発行するには、Visual Studio でソリューションを右クリックし、 **[WebJob として発行]** を選択します。

    ![右クリック オプションのスクリーンショット。[WebJob として発行] が赤で囲まれています。][2]

    f. 発行プロファイルを選択し、この WebJob をホストする新しい Azure Web サイトを作成し (このサイトがまだ存在しない場合)、Web サイトを作成したら **[発行]** を選択します。

    :::image type="complex" source="./media/notification-hubs-enterprise-push-architecture/PublishAsWebJob.png" alt-text="Azure でサイトを作成するためのワークフローのスクリーンショット。":::
    [Web の発行] ダイアログのスクリーンショット。[Microsoft Azure Web サイト] オプションが選択されています。緑の矢印が指し示す [Select Existing Website]\(既存の Web サイトを選択する\) ダイアログ ボックスでは [新規] オプションが赤で囲まれています。そこからさらに緑の矢印が指し示す [Create site on Microsoft Azure]\(Microsoft Azure でサイトを作成する\) ダイアログ ボックスでは [サイト名] と [作成] オプションが赤で囲まれています。
    :::image-end:::

    g. ジョブを [連続実行する] ように構成すると、[Azure Portal] にログインしたときに、以下のような画面が表示されます。

    ![Azure portal のスクリーンショット。エンタープライズ プッシュ バックエンド Web ジョブが表示されています。名前、スケジュール、ログの値が赤で囲まれています。][4]

3. **EnterprisePushMobileApp**

    a. このアプリケーションは、モバイル バックエンドの一部として実行されている WebJob からトースト通知を受信して表示する Windows ストア アプリケーションです。 このコードは、「[Notification Hubs の使用 - Windows ユニバーサル チュートリアル]」に基づいています。  

    b. アプリケーションでトースト通知の受信が有効になっていることを確認します。

    c. (`HubName` および `DefaultListenSharedAccessSignature` の値を置換してから) アプリの起動時に、以下の Notification Hubs の登録コードが確実に呼び出されるようにします。

    ```csharp
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
    ```

### <a name="running-the-sample"></a>サンプルの実行

1. WebJob が正常に実行され、継続的に実行するようにスケジュールされていることを確認します。
2. Windows ストア アプリを起動する **EnterprisePushMobileApp** を実行します。
3. LoB バックエンドをシミュレートし、メッセージの送信を開始する **EnterprisePushBackendSystem** コンソール アプリケーションを実行すると、次の図のようなトースト通知が表示されます。

    ![Enterprise Push Backend System アプリを実行するコンソールと、そのアプリで送信されるメッセージのスクリーンショット。][5]

4. これらのメッセージは最初、WebJob で Service Bus サブスクリプションによって監視されていた Service Bus トピックに送信されました。 メッセージを受信すると、通知が作成されてモバイル アプリに送信されます。 [Azure Portal] で WebJobs の [ログ] リンクをクリックすると、WebJobs ログで処理を確認することができます。

    ![[Continuous WebJob Details]\(継続的な WebJob の詳細\) ダイアログ ボックスのスクリーンショット。送信されるメッセージが赤で囲まれています。][6]

<!-- Images -->
[1]: ./media/notification-hubs-enterprise-push-architecture/architecture.png
[2]: ./media/notification-hubs-enterprise-push-architecture/WebJobsContextMenu.png
[3]: ./media/notification-hubs-enterprise-push-architecture/PublishAsWebJob.png
[4]: ./media/notification-hubs-enterprise-push-architecture/WebJob.png
[5]: ./media/notification-hubs-enterprise-push-architecture/Notifications.png
[6]: ./media/notification-hubs-enterprise-push-architecture/WebJobsLog.png

<!-- Links -->
[Notification Hubs のサンプル (英語)]: https://github.com/Azure/azure-notificationhubs-samples
[Azure Mobile Service]: https://azure.microsoft.com/documentation/services/mobile-services/
[Azure Service Bus]: ../service-bus-messaging/service-bus-messaging-overview.md
[Service Bus Pub/Sub programming (Service Bus のトピックとサブスクリプションの使用方法) (Service Bus のトピックとサブスクリプションの使用方法)]: ../service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions.md
[Azure WebJob]: ../app-service/webjobs-create.md
[Notification Hubs の使用 - Windows ユニバーサル チュートリアル]: ./notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Azure Portal]: https://portal.azure.com/
