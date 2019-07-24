---
title: Azure Service Bus のトピックとサブスクリプションの概要 | Microsoft Docs
description: Service Bus メッセージングのトピックとサブスクリプションを使った C# .NET Core コンソール アプリケーションを作成します。
services: service-bus-messaging
documentationcenter: .net
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: conceptual
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 04/15/2019
ms.author: aschhab
ms.openlocfilehash: 892d485fb5cdaa08107870e9ab5b2b7ad9bcba5b
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2019
ms.locfileid: "59608896"
---
# <a name="get-started-with-service-bus-topics"></a>Service Bus トピックの概要

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

このチュートリアルに含まれる手順は次のとおりです。

1. トピックに一連のメッセージを送信するための .NET Core コンソール アプリケーションを作成する。
2. それらのメッセージをサブスクリプションから受信する .NET Core コンソール アプリケーションを作成する。

## <a name="prerequisites"></a>前提条件

1. Azure サブスクリプション。 このチュートリアルを完了するには、Azure アカウントが必要です。 [Visual Studio または MSDN のサブスクライバー特典](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF)を有効にするか、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)にサインアップしてください。
2. 従うべき手順を「[クイック スタート:Azure portal を使用して Service Bus トピックとそのサブスクリプションを作成する](service-bus-quickstart-topics-subscriptions-portal.md)」で確認し、次のタスクを実行します:
    1. Service Bus **名前空間**を作成します。
    2. **接続文字列**を取得します。
    3. 名前空間の**トピック**を作成します。
    4. 名前空間のトピックへの**サブスクリプションを 1 つ**作成します。
3. [Visual Studio 2017 Update 3 (バージョン 15.3, 26730.01)](https://www.visualstudio.com/vs) 以降。
4. [NET Core SDK](https://www.microsoft.com/net/download/windows) バージョン 2.0 以降。
 
## <a name="send-messages-to-the-topic"></a>トピックにメッセージを送信する

トピックにメッセージを送信するために、Visual Studio を使用して C# コンソール アプリケーションを作成します。

### <a name="create-a-console-application"></a>コンソール アプリケーションの作成

Visual Studio を起動し、新しい**コンソール アプリ (.NET Core)** プロジェクトを作成します。

### <a name="add-the-service-bus-nuget-package"></a>Service Bus NuGet パッケージの追加

1. 新しく作成したプロジェクトを右クリックし、**[NuGet パッケージの管理]** を選択します。
2. **[参照]** タブをクリックし、**[Microsoft Azure Service Bus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus/)** を検索して、**[Microsoft.Azure.ServiceBus]** 項目を選択します。 **[インストール]** をクリックし、インストールが完了したら、このダイアログ ボックスを閉じます。
   
    ![Select a NuGet package][nuget-pkg]

### <a name="write-code-to-send-messages-to-the-topic"></a>トピックにメッセージを送信するコードの作成

1. Program.cs 内の名前空間定義とクラス宣言の前に、次の `using` ステートメントを追加します。
   
    ```csharp
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;
    ```

2. `Program` クラス内で次の変数を宣言します。 名前空間の作成時に取得した接続文字列を `ServiceBusConnectionString` 変数に設定し、トピックの作成時に使用した名前を `TopicName` に設定します。
   
    ```csharp
    const string ServiceBusConnectionString = "<your_connection_string>";
    const string TopicName = "<your_topic_name>";
    static ITopicClient topicClient;
    ``` 

3. `Main()` の既定の内容を次のコード行に置き換えます。

    ```csharp
    MainAsync().GetAwaiter().GetResult();
    ```
   
4. `Main()` のすぐ後に、メッセージ送信メソッドを呼び出す次の非同期 `MainAsync()` メソッドを追加します。

    ```csharp
    static async Task MainAsync()
    {
        const int numberOfMessages = 10;
        topicClient = new TopicClient(ServiceBusConnectionString, TopicName);

        Console.WriteLine("======================================================");
        Console.WriteLine("Press ENTER key to exit after sending all the messages.");
        Console.WriteLine("======================================================");

        // Send messages.
        await SendMessagesAsync(numberOfMessages);

        Console.ReadKey();

        await topicClient.CloseAsync();
    }
    ```

5. `MainAsync()` メソッドのすぐ後に、次の `SendMessagesAsync()` メソッドを追加します。`numberOfMessagesToSend` で指定された数 (現時点では 10 に設定) のメッセージを送信する処理が、このメソッドによって実行されます。

    ```csharp
    static async Task SendMessagesAsync(int numberOfMessagesToSend)
    {
        try
        {
            for (var i = 0; i < numberOfMessagesToSend; i++)
            {
                // Create a new message to send to the topic.
                string messageBody = $"Message {i}";
                var message = new Message(Encoding.UTF8.GetBytes(messageBody));

                // Write the body of the message to the console.
                Console.WriteLine($"Sending message: {messageBody}");

                // Send the message to the topic.
                await topicClient.SendAsync(message);
            }
        }
        catch (Exception exception)
        {
            Console.WriteLine($"{DateTime.Now} :: Exception: {exception.Message}");
        }
    }
    ```
  
6. 送信側の Program.cs ファイルは次のようになります。
   
    ```csharp
    namespace CoreSenderApp
    {
        using System;
        using System.Text;
        using System.Threading;
        using System.Threading.Tasks;
        using Microsoft.Azure.ServiceBus;

        class Program
        {
            const string ServiceBusConnectionString = "<your_connection_string>";
            const string TopicName = "<your_topic_name>";
            static ITopicClient topicClient;

            static void Main(string[] args)
            {
                MainAsync().GetAwaiter().GetResult();
            }

            static async Task MainAsync()
            {
                const int numberOfMessages = 10;
                topicClient = new TopicClient(ServiceBusConnectionString, TopicName);

                Console.WriteLine("======================================================");
                Console.WriteLine("Press ENTER key to exit after sending all the messages.");
                Console.WriteLine("======================================================");

                // Send messages.
                await SendMessagesAsync(numberOfMessages);

                Console.ReadKey();

                await topicClient.CloseAsync();
            }

            static async Task SendMessagesAsync(int numberOfMessagesToSend)
            {
                try
                {
                    for (var i = 0; i < numberOfMessagesToSend; i++)
                    {
                        // Create a new message to send to the topic
                        string messageBody = $"Message {i}";
                        var message = new Message(Encoding.UTF8.GetBytes(messageBody));

                        // Write the body of the message to the console
                        Console.WriteLine($"Sending message: {messageBody}");

                        // Send the message to the topic
                        await topicClient.SendAsync(message);
                    }
                }
                catch (Exception exception)
                {
                    Console.WriteLine($"{DateTime.Now} :: Exception: {exception.Message}");
                }
            }
        }
    }
    ```

3. プログラムを実行し、Azure Portal を確認します。名前空間の **[概要]** ウィンドウでトピックの名前をクリックしてください。 トピックの **[要点]** 画面が表示されます。 ウィンドウの下部付近に表示されているサブスクリプション一覧で、サブスクリプションの **[メッセージ数]** の値が **10** になっていることがわかります。 この値は、メッセージを取得しないまま送信側アプリケーションを実行するたびに 10 ずつ増えます (メッセージの取得については次のセクションで説明します)。 アプリからトピックにメッセージを追加するたびに、トピックの最新のサイズによって、**[要点]** ウィンドウの **[現在]** の値も増えている点に注目してください。
   
      ![メッセージ サイズ][topic-message]

## <a name="receive-messages-from-the-subscription"></a>サブスクリプションからメッセージを受信する

送信したメッセージを受信するには、前の手順で説明した送信側アプリケーションと同じように、.NET Core コンソール アプリケーションをもう 1 つ作成し、**Microsoft.Azure.ServiceBus** NuGet パッケージをインストールします。

### <a name="write-code-to-receive-messages-from-the-subscription"></a>サブスクリプションからメッセージを受信するコードの作成

1. Program.cs 内の名前空間定義とクラス宣言の前に、次の `using` ステートメントを追加します。
   
    ```csharp
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;
    ```

2. `Program` クラス内で次の変数を宣言します。 名前空間の作成時に取得した接続文字列を `ServiceBusConnectionString` 変数に設定し、トピックの作成時に使用した名前を `TopicName` に設定します。さらに、そのトピックのサブスクリプションを作成するときに使用した名前を `SubscriptionName` に設定します。
   
    ```csharp
    const string ServiceBusConnectionString = "<your_connection_string>";
    const string TopicName = "<your_topic_name>";
    const string SubscriptionName = "<your_subscription_name>";
    static ISubscriptionClient subscriptionClient;
    ```

3. `Main()` の既定の内容を次のコード行に置き換えます。

    ```csharp
    MainAsync().GetAwaiter().GetResult();
    ```

4. `Main()` のすぐ後に、`RegisterOnMessageHandlerAndReceiveMessages()` メソッドを呼び出す次の非同期 `MainAsync()` メソッドを追加します。

    ```csharp
    static async Task MainAsync()
    {
        subscriptionClient = new SubscriptionClient(ServiceBusConnectionString, TopicName, SubscriptionName);

        Console.WriteLine("======================================================");
        Console.WriteLine("Press ENTER key to exit after receiving all the messages.");
        Console.WriteLine("======================================================");

        // Register subscription message handler and receive messages in a loop
        RegisterOnMessageHandlerAndReceiveMessages();

        Console.ReadKey();

        await subscriptionClient.CloseAsync();
    }
    ```

5. `MainAsync()` メソッドのすぐ後に、次のメソッドを追加します。これは、メッセージ ハンドラーを登録して送信側アプリケーションから送られたメッセージを受信するメソッドです。

    ```csharp
    static void RegisterOnMessageHandlerAndReceiveMessages()
    {
        // Configure the message handler options in terms of exception handling, number of concurrent messages to deliver, etc.
        var messageHandlerOptions = new MessageHandlerOptions(ExceptionReceivedHandler)
        {
            // Maximum number of concurrent calls to the callback ProcessMessagesAsync(), set to 1 for simplicity.
            // Set it according to how many messages the application wants to process in parallel.
            MaxConcurrentCalls = 1,

            // Indicates whether the message pump should automatically complete the messages after returning from user callback.
            // False below indicates the complete operation is handled by the user callback as in ProcessMessagesAsync().
            AutoComplete = false
        };

        // Register the function that processes messages.
        subscriptionClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
    }
    ```    

6. 先ほどのメソッドのすぐ後に、受信したメッセージを処理する次の `ProcessMessagesAsync()` メソッドを追加します。
 
    ```csharp
    static async Task ProcessMessagesAsync(Message message, CancellationToken token)
    {
        // Process the message.
        Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

        // Complete the message so that it is not received again.
        // This can be done only if the subscriptionClient is created in ReceiveMode.PeekLock mode (which is the default).
        await subscriptionClient.CompleteAsync(message.SystemProperties.LockToken);

        // Note: Use the cancellationToken passed as necessary to determine if the subscriptionClient has already been closed.
        // If subscriptionClient has already been closed, you can choose to not call CompleteAsync() or AbandonAsync() etc.
        // to avoid unnecessary exceptions.
    }
    ```

7. 最後に、次のメソッドを追加します。例外が発生した場合は、すべてこのメソッドによって処理されます。
 
    ```csharp
    // Use this handler to examine the exceptions received on the message pump.
    static Task ExceptionReceivedHandler(ExceptionReceivedEventArgs exceptionReceivedEventArgs)
    {
        Console.WriteLine($"Message handler encountered an exception {exceptionReceivedEventArgs.Exception}.");
        var context = exceptionReceivedEventArgs.ExceptionReceivedContext;
        Console.WriteLine("Exception context for troubleshooting:");
        Console.WriteLine($"- Endpoint: {context.Endpoint}");
        Console.WriteLine($"- Entity Path: {context.EntityPath}");
        Console.WriteLine($"- Executing Action: {context.Action}");
        return Task.CompletedTask;
    }    
    ```    

8. 受信側の Program.cs ファイルは次のようになります。
   
    ```csharp
    namespace CoreReceiverApp
    {
        using System;
        using System.Text;
        using System.Threading;
        using System.Threading.Tasks;
        using Microsoft.Azure.ServiceBus;

        class Program
        {
            const string ServiceBusConnectionString = "<your_connection_string>";
            const string TopicName = "<your_topic_name>";
            const string SubscriptionName = "<your_subscription_name>";
            static ISubscriptionClient subscriptionClient;

            static void Main(string[] args)
            {
                MainAsync().GetAwaiter().GetResult();
            }

            static async Task MainAsync()
            {
                subscriptionClient = new SubscriptionClient(ServiceBusConnectionString, TopicName, SubscriptionName);

                Console.WriteLine("======================================================");
                Console.WriteLine("Press ENTER key to exit after receiving all the messages.");
                Console.WriteLine("======================================================");

                // Register subscription message handler and receive messages in a loop.
                RegisterOnMessageHandlerAndReceiveMessages();

                Console.ReadKey();

                await subscriptionClient.CloseAsync();
            }

            static void RegisterOnMessageHandlerAndReceiveMessages()
            {
                // Configure the message handler options in terms of exception handling, number of concurrent messages to deliver, etc.
                var messageHandlerOptions = new MessageHandlerOptions(ExceptionReceivedHandler)
                {
                    // Maximum number of concurrent calls to the callback ProcessMessagesAsync(), set to 1 for simplicity.
                    // Set it according to how many messages the application wants to process in parallel.
                    MaxConcurrentCalls = 1,

                    // Indicates whether MessagePump should automatically complete the messages after returning from User Callback.
                    // False below indicates the Complete will be handled by the User Callback as in `ProcessMessagesAsync` below.
                    AutoComplete = false
                };

                // Register the function that processes messages.
                subscriptionClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
            }

            static async Task ProcessMessagesAsync(Message message, CancellationToken token)
            {
                // Process the message.
                Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

                // Complete the message so that it is not received again.
                // This can be done only if the subscriptionClient is created in ReceiveMode.PeekLock mode (which is the default).
                await subscriptionClient.CompleteAsync(message.SystemProperties.LockToken);

                // Note: Use the cancellationToken passed as necessary to determine if the subscriptionClient has already been closed.
                // If subscriptionClient has already been closed, you can choose to not call CompleteAsync() or AbandonAsync() etc.
                // to avoid unnecessary exceptions.
            }

            static Task ExceptionReceivedHandler(ExceptionReceivedEventArgs exceptionReceivedEventArgs)
            {
                Console.WriteLine($"Message handler encountered an exception {exceptionReceivedEventArgs.Exception}.");
                var context = exceptionReceivedEventArgs.ExceptionReceivedContext;
                Console.WriteLine("Exception context for troubleshooting:");
                Console.WriteLine($"- Endpoint: {context.Endpoint}");
                Console.WriteLine($"- Entity Path: {context.EntityPath}");
                Console.WriteLine($"- Executing Action: {context.Action}");
                return Task.CompletedTask;
            }
        }
    }
    ```
9. プログラムを実行し、もう一度ポータルを確認します。 **[メッセージ数]** と **[現在]** の値が **0** になっていることがわかります。
   
    ![トピックの長さ][topic-message-receive]

お疲れさまでした。 以上、トピックとサブスクリプションを作成し、10 個のメッセージを送信して、それらのメッセージを受信するまでの流れを、.NET Standard ライブラリを使って説明しました。

## <a name="next-steps"></a>次の手順

Service Bus メッセージングのさらに高度な機能を紹介する、Service Bus の[サンプルが含まれる GitHub リポジトリ](https://github.com/Azure/azure-service-bus/tree/master/samples)を参照してください。

<!--Image references-->

[nuget-pkg]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/nuget-package.png
[topic-message]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/topic-message.png
[topic-message-receive]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/topic-message-receive.png
[createtopic1]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic1.png
[createtopic2]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic2.png
[createtopic3]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic3.png
[createtopic4]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic4.png
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples
[azure-portal]: https://portal.azure.com
