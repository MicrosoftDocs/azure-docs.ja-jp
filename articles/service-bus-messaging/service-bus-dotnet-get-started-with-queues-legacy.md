---
title: Azure Service Bus キューの使用 | Microsoft Docs
description: このチュートリアルでは、.NET Core コンソール アプリケーションを作成して、Service Bus キューとの間でメッセージを送受信します。
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.date: 09/01/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: e8e70884838d56003694e2da09668527ce5b6c7b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100652991"
---
# <a name="get-started-with-service-bus-queues"></a>Service Bus キューの使用
このチュートリアルでは、.NET Core コンソール アプリケーションを作成して、Service Bus キューとの間でメッセージを送受信します。

> [!WARNING]
> このクイックスタートでは、以前の Microsoft.Azure.ServiceBus パッケージを使用します。 最新の Azure.Messaging.ServiceBus パッケージを使用するクイックスタートについては、[Azure.Messaging.ServiceBus パッケージを使用したイベントの送受信](service-bus-dotnet-get-started-with-queues.md)に関するページを参照してください。 

## <a name="prerequisites"></a>前提条件

- [Visual Studio 2019](https://www.visualstudio.com/vs)。
- [NET Core SDK](https://www.microsoft.com/net/download/windows) バージョン 2.0 以降。
- Azure サブスクリプション。 このチュートリアルを完了するには、Azure アカウントが必要です。 [MSDN のサブスクライバー特典](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF)を有効にするか、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)にサインアップしてください。
- 使用するキューがない場合は、「[Azure portal を使用して Service Bus キューを作成する](service-bus-quickstart-portal.md)」の記事にある手順に従って、キューを作成します。

  - Service Bus キューの概要を読みます。
  - Service Bus 名前空間を作成します。
  - 接続文字列を取得します。
  - Service Bus キューを作成する。

## <a name="send-messages"></a>メッセージを送信する

キューにメッセージを送信するために、Visual Studio を使用して C# コンソール アプリケーションを作成します。

### <a name="create-a-console-application"></a>コンソール アプリケーションの作成

Visual Studio を起動し、C# 用の新しい **コンソール アプリ (.NET Core)** プロジェクトを作成します。 この例では、アプリの名前を *CoreSenderApp* に します。

### <a name="add-the-service-bus-nuget-package"></a>Service Bus NuGet パッケージの追加

1. 新しく作成したプロジェクトを右クリックし、**[NuGet パッケージの管理]** を選択します。
1. **[参照]** を選択します。 **[[Microsoft.Azure.ServiceBus]](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus/)** を検索して選択します。
1. **[インストール]** を選択し、インストールが完了したら、NuGet パッケージ マネージャーを閉じます。

    ![Select a NuGet package][nuget-pkg]

### <a name="write-code-to-send-messages-to-the-queue"></a>キューにメッセージを送信するコードの作成

1. *Program.cs* 内の名前空間定義の先頭 (クラス宣言の前) に、次の `using` ステートメントを追加します。

    ```csharp
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;
    ```

1. `Program` クラス内で次の変数を宣言します。

    ```csharp
    const string ServiceBusConnectionString = "<your_connection_string>";
    const string QueueName = "<your_queue_name>";
    static IQueueClient queueClient;
    ```

    名前空間の接続文字列を `ServiceBusConnectionString` 変数として入力します。 キュー名を入力します。

1. `Main()` メソッドを次の **async** `Main` メソッドに置き換えます。 これは次の手順で追加する `SendMessagesAsync()` メソッドを呼び出して、キューにメッセージを送信します。 

    ```csharp
    public static async Task Main(string[] args)
    {    
        const int numberOfMessages = 10;
        queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

        Console.WriteLine("======================================================");
        Console.WriteLine("Press ENTER key to exit after sending all the messages.");
        Console.WriteLine("======================================================");

        // Send messages.
        await SendMessagesAsync(numberOfMessages);

        Console.ReadKey();

        await queueClient.CloseAsync();
    }
    ```
1. `MainAsync()` メソッドのすぐ後に、次の `SendMessagesAsync()` メソッドを追加します。`numberOfMessagesToSend` で指定された数 (現時点では 10 に設定) のメッセージを送信する処理が、このメソッドによって実行されます。

    ```csharp
    static async Task SendMessagesAsync(int numberOfMessagesToSend)
    {
        try
        {
            for (var i = 0; i < numberOfMessagesToSend; i++)
            {
                // Create a new message to send to the queue.
                string messageBody = $"Message {i}";
                var message = new Message(Encoding.UTF8.GetBytes(messageBody));

                // Write the body of the message to the console.
                Console.WriteLine($"Sending message: {messageBody}");

                // Send the message to the queue.
                await queueClient.SendAsync(message);
            }
        }
        catch (Exception exception)
        {
            Console.WriteLine($"{DateTime.Now} :: Exception: {exception.Message}");
        }
    }
    ```

*Program.cs* ファイルは次のようになります。

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
        // Connection String for the namespace can be obtained from the Azure portal under the 
        // 'Shared Access policies' section.
        const string ServiceBusConnectionString = "<your_connection_string>";
        const string QueueName = "<your_queue_name>";
        static IQueueClient queueClient;

        public static async Task Main(string[] args)
        {    
            const int numberOfMessages = 10;
            queueClient = new QueueClient(ServiceBusConnectionString, QueueName);
    
            Console.WriteLine("======================================================");
            Console.WriteLine("Press ENTER key to exit after sending all the messages.");
            Console.WriteLine("======================================================");
    
            // Send messages.
            await SendMessagesAsync(numberOfMessages);
    
            Console.ReadKey();
    
            await queueClient.CloseAsync();
        }

        static async Task SendMessagesAsync(int numberOfMessagesToSend)
        {
            try
            {
                for (var i = 0; i < numberOfMessagesToSend; i++)
                {
                    // Create a new message to send to the queue
                    string messageBody = $"Message {i}";
                    var message = new Message(Encoding.UTF8.GetBytes(messageBody));

                    // Write the body of the message to the console
                    Console.WriteLine($"Sending message: {messageBody}");

                    // Send the message to the queue
                    await queueClient.SendAsync(message);
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

プログラムを実行し、Azure portal を確認します。

名前空間の **[概要]** ウィンドウでキューの名前を選択して、キューの **Essentials** を表示します。

![数とサイズが示された受信メッセージ][queue-message]

キューの **[アクティブなメッセージ数]** の値は、現在 **10** です。 この値は、メッセージを取得しないまま送信側アプリを実行するたびに 10 ずつ増えます。

アプリからキューにメッセージを追加するたびに、キューの最新のサイズによって、**[Essentials]** の **[現在]** の値が増えます。

次のセクションでは、これらのメッセージの取得方法について説明します。

## <a name="receive-messages"></a>メッセージを受信する

送信したメッセージを受信するには、別の **コンソール アプリ (.NET Core)** アプリケーションを作成します。 送信側アプリケーションの場合と同様に、**Microsoft.Azure.ServiceBus** NuGet パッケージをインストールします。

### <a name="write-code-to-receive-messages-from-the-queue"></a>キューからメッセージを受信するコードの作成

1. *Program.cs* 内の名前空間定義の先頭 (クラス宣言の前) に、次の `using` ステートメントを追加します。

    ```csharp
    using System;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;
    ```

1. `Program` クラス内で次の変数を宣言します。

    ```csharp
    const string ServiceBusConnectionString = "<your_connection_string>";
    const string QueueName = "<your_queue_name>";
    static IQueueClient queueClient;
    ```

    名前空間の接続文字列を `ServiceBusConnectionString` 変数として入力します。 キュー名を入力します。

1. `Main()` メソッドを次のコードで置き換えます。

    ```csharp
    static void Main(string[] args)
    {
        MainAsync().GetAwaiter().GetResult();
    }

    static async Task MainAsync()
    {
        queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

        Console.WriteLine("======================================================");
        Console.WriteLine("Press ENTER key to exit after receiving all the messages.");
        Console.WriteLine("======================================================");

        // Register QueueClient's MessageHandler and receive messages in a loop
        RegisterOnMessageHandlerAndReceiveMessages();

        Console.ReadKey();

        await queueClient.CloseAsync();
    }
    ```

1. `MainAsync()` メソッドのすぐ後に、次のメソッドを追加します。これは、メッセージ ハンドラーを登録し、送信側アプリケーションによって送信されたメッセージを受信します。

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
        queueClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
    }
    ```

1. 先ほどのメソッドのすぐ後に、受信したメッセージを処理する次の `ProcessMessagesAsync()` メソッドを追加します。

    ```csharp
    static async Task ProcessMessagesAsync(Message message, CancellationToken token)
    {
        // Process the message.
        Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

        // Complete the message so that it is not received again.
        // This can be done only if the queue Client is created in ReceiveMode.PeekLock mode (which is the default).
        await queueClient.CompleteAsync(message.SystemProperties.LockToken);

        // Note: Use the cancellationToken passed as necessary to determine if the queueClient has already been closed.
        // If queueClient has already been closed, you can choose to not call CompleteAsync() or AbandonAsync() etc.
        // to avoid unnecessary exceptions.
    }
    ```

1. 最後に、次のメソッドを追加します。例外が発生した場合は、すべてこのメソッドによって処理されます。

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

*Program.cs* ファイルは次のようになります。

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
        // Connection String for the namespace can be obtained from the Azure portal under the 
        // 'Shared Access policies' section.
        const string ServiceBusConnectionString = "<your_connection_string>";
        const string QueueName = "<your_queue_name>";
        static IQueueClient queueClient;

        static void Main(string[] args)
        {
            MainAsync().GetAwaiter().GetResult();
        }

        static async Task MainAsync()
        {
            queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

            Console.WriteLine("======================================================");
            Console.WriteLine("Press ENTER key to exit after receiving all the messages.");
            Console.WriteLine("======================================================");

            // Register QueueClient's MessageHandler and receive messages in a loop
            RegisterOnMessageHandlerAndReceiveMessages();
 
            Console.ReadKey();

            await queueClient.CloseAsync();
        }

        static void RegisterOnMessageHandlerAndReceiveMessages()
        {
            // Configure the MessageHandler Options in terms of exception handling, number of concurrent messages to deliver etc.
            var messageHandlerOptions = new MessageHandlerOptions(ExceptionReceivedHandler)
            {
                // Maximum number of Concurrent calls to the callback `ProcessMessagesAsync`, set to 1 for simplicity.
                // Set it according to how many messages the application wants to process in parallel.
                MaxConcurrentCalls = 1,

                // Indicates whether MessagePump should automatically complete the messages after returning from User Callback.
                // False below indicates the Complete will be handled by the User Callback as in `ProcessMessagesAsync` below.
                AutoComplete = false
            };

            // Register the function that will process messages
            queueClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
        }

        static async Task ProcessMessagesAsync(Message message, CancellationToken token)
        {
            // Process the message
            Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

            // Complete the message so that it is not received again.
            // This can be done only if the queueClient is created in ReceiveMode.PeekLock mode (which is default).
            await queueClient.CompleteAsync(message.SystemProperties.LockToken);

            // Note: Use the cancellationToken passed as necessary to determine if the queueClient has already been closed.
            // If queueClient has already been Closed, you may chose to not call CompleteAsync() or AbandonAsync() etc. calls 
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

プログラムを実行し、もう一度ポータルを確認します。 **[アクティブなメッセージ数]** と **[現在]** の値は **0** になりました。

![メッセージが受信された後のキュー][queue-message-receive]

おめでとうございます。 ここではキューを作成して、そのキューに一連のメッセージを送信し、同じキューからそれらのメッセージを受信しました。

> [!NOTE]
> Service Bus リソースは、[Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/) で管理できます。 Service Bus Explorer を使用すると、ユーザーは簡単に Service Bus 名前空間に接続し、メッセージング エンティティを管理できます。 このツールには、インポート/エクスポート機能や、トピック、キュー、サブスクリプション、リレー サービス、通知ハブ、イベント ハブをテストする機能などの高度な機能が用意されています。

## <a name="next-steps"></a>次のステップ

Service Bus メッセージングのさらに高度な機能を紹介する[サンプルが含まれる GitHub リポジトリ](https://github.com/Azure/azure-service-bus/tree/master/samples)を参照してください。

<!--Image references-->

[nuget-pkg]: ./media/service-bus-dotnet-get-started-with-queues-legacy/nuget-package.png
[queue-message]: ./media/service-bus-dotnet-get-started-with-queues-legacy/messages-sent-to-essentials.png
[queue-message-receive]: ./media/service-bus-dotnet-get-started-with-queues-legacy/queue-message-receive-in-essentials.png

