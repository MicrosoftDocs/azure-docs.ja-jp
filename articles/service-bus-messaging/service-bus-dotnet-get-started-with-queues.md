---
title: Azure Service Bus キューの概要 (Azure.Messaging.ServiceBus)
description: このチュートリアルでは、.NET Core C# アプリケーションを作成して、Service Bus キューとの間でメッセージを送受信します。
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.date: 11/13/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: ec3f53e6f69614028c013efa5f0e6852cbc3f8ae
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98631642"
---
# <a name="send-messages-to-and-receive-messages-from-azure-service-bus-queues-net"></a>Azure Service Bus キューとの間でメッセージを送受信する (.NET)
このチュートリアルでは、**Azure.Messaging.ServiceBus** パッケージを使用して .NET Core コンソール アプリケーションを作成し、Service Bus キューとの間でメッセージを送受信します。 

> [!Important]
> このクイックスタートでは、新しい Azure.Messaging.ServiceBus パッケージを使用します。 以前の Microsoft.Azure.ServiceBus パッケージを使用したクイックスタートについては、[Microsoft.Azure.ServiceBus パッケージを使用したイベントの送受信](service-bus-dotnet-get-started-with-queues-legacy.md)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

- [Visual Studio 2019](https://www.visualstudio.com/vs)
- Azure サブスクリプション。 このチュートリアルを完了するには、Azure アカウントが必要です。 [MSDN のサブスクライバー特典](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF)を有効にするか、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)にサインアップしてください。
- 使用するキューがない場合は、「[Azure portal を使用して Service Bus キューを作成する](service-bus-quickstart-portal.md)」の記事にある手順に従って、キューを作成します。 Service Bus 名前空間の **接続文字列** と、作成した **キュー** の名前をメモしておいてください。

## <a name="send-messages-to-a-queue"></a>メッセージをキューに送信する
このクイック スタートでは、キューにメッセージを送信する C# .NET Core コンソール アプリケーションを作成します。

### <a name="create-a-console-application"></a>コンソール アプリケーションの作成
Visual Studio を起動し、C# 用の新しい **コンソール アプリ (.NET Core)** プロジェクトを作成します。 

### <a name="add-the-service-bus-nuget-package"></a>Service Bus NuGet パッケージの追加

1. 新しく作成したプロジェクトを右クリックし、**[NuGet パッケージの管理]** を選択します。
1. **[参照]** を選択します。 **[Azure.Messaging.ServiceBus](https://www.nuget.org/packages/Azure.Messaging.ServiceBus/)** を検索して選択します。
1. **[インストール]** を選択し、インストールが完了したら、NuGet パッケージ マネージャーを閉じます。

### <a name="add-code-to-send-messages-to-the-queue"></a>キューにメッセージを送信するコードを追加する

1. *Program.cs* 内の名前空間定義の先頭 (クラス宣言の前) に、次の `using` ステートメントを追加します。

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Threading.Tasks;
    
    using Azure.Messaging.ServiceBus;
    ```

1. `Program` クラス内で次の変数を宣言します。

    ```csharp
        static string connectionString = "<NAMESPACE CONNECTION STRING>";
        static string queueName = "<QUEUE NAME>";
    ```

    名前空間の接続文字列を `connectionString` 変数として入力します。 キュー名を入力します。

1. `Main()` メソッドのすぐ後に、メッセージを送信する作業を行う次の `SendMessagesAsync()` メソッドを追加します。

    ```csharp
        static async Task SendMessageAsync()
        {
            // create a Service Bus client 
            await using (ServiceBusClient client = new ServiceBusClient(connectionString))
            {
                // create a sender for the queue 
                ServiceBusSender sender = client.CreateSender(queueName);

                // create a message that we can send
                ServiceBusMessage message = new ServiceBusMessage("Hello world!");

                // send the message
                await sender.SendMessageAsync(message);
                Console.WriteLine($"Sent a single message to the queue: {queueName}");
            }
        }
    ```
1. メッセージのキュー (.NET キュー) を作成するためのメソッドを `CreateMessages` という名前で `Program` クラスに追加します。 通常、これらのメッセージはアプリケーションのさまざまな部分から届きます。 ここでは、サンプル メッセージのキューを作成します。

    ```csharp
        static Queue<ServiceBusMessage> CreateMessages()
        {
            // create a queue containing the messages and return it to the caller
            Queue<ServiceBusMessage> messages = new Queue<ServiceBusMessage>();
            messages.Enqueue(new ServiceBusMessage("First message in the batch"));
            messages.Enqueue(new ServiceBusMessage("Second message in the batch"));
            messages.Enqueue(new ServiceBusMessage("Third message in the batch"));
            return messages;
        }
    ```
1. `SendMessageBatchAsync` という名前のメソッドを `Program` クラスに追加し、次のコードを追加します。 このメソッドは、メッセージのキューを受け取り、Service Bus キューに送信するバッチを準備します。 

    ```csharp
        static async Task SendMessageBatchAsync()
        {
            // create a Service Bus client 
            await using (ServiceBusClient client = new ServiceBusClient(connectionString))
            {
                // create a sender for the queue 
                ServiceBusSender sender = client.CreateSender(queueName);

                // get the messages to be sent to the Service Bus queue
                Queue<ServiceBusMessage> messages = CreateMessages();

                // total number of messages to be sent to the Service Bus queue
                int messageCount = messages.Count;

                // while all messages are not sent to the Service Bus queue
                while (messages.Count > 0)
                {
                    // start a new batch 
                    using ServiceBusMessageBatch messageBatch = await sender.CreateMessageBatchAsync();

                    // add the first message to the batch
                    if (messageBatch.TryAddMessage(messages.Peek()))
                    {
                        // dequeue the message from the .NET queue once the message is added to the batch
                        messages.Dequeue();
                    }
                    else
                    {
                        // if the first message can't fit, then it is too large for the batch
                        throw new Exception($"Message {messageCount - messages.Count} is too large and cannot be sent.");
                    }

                    // add as many messages as possible to the current batch
                    while (messages.Count > 0 && messageBatch.TryAddMessage(messages.Peek()))
                    {
                        // dequeue the message from the .NET queue as it has been added to the batch
                        messages.Dequeue();
                    }
        
                    // now, send the batch
                    await sender.SendMessagesAsync(messageBatch);
        
                    // if there are any remaining messages in the .NET queue, the while loop repeats 
                }

                Console.WriteLine($"Sent a batch of {messageCount} messages to the topic: {queueName}");
            }
        }
    ```
1. `Main()` メソッドを次の **async** `Main` メソッドに置き換えます。 これは両方の Send メソッドを呼び出して、単一のメッセージおよびメッセージのバッチをキューに送信するものです。 

    ```csharp
        static async Task Main()
        {
            // send a message to the queue
            await SendMessageAsync();

            // send a batch of messages to the queue
            await SendMessageBatchAsync();
        }
    ```
5. アプリケーションを実行します。 次のメッセージが表示されます。 

    ```console
    Sent a single message to the queue: myqueue
    Sent a batch of messages to the queue: myqueue
    ```       
1. Azure portal で次の手順を実行します。
    1. 自分の Service Bus 名前空間に移動します。 
    1. **[概要]** ページ中央下のペインでキューを選択します。 
    1. **[要点]** セクションの値に注目します。

    :::image type="content" source="./media/service-bus-dotnet-get-started-with-queues/sent-messages-essentials.png" alt-text="数とサイズが示された受信メッセージ" lightbox="./media/service-bus-dotnet-get-started-with-queues/sent-messages-essentials.png":::

    次の値に注目してください。
    - キューの **[アクティブなメッセージ数]** の値は、現在 **4** です。 この値は、メッセージを取得しないまま送信側アプリを実行するたびに 4 ずつ増えます。
    - アプリからキューにメッセージを追加するたびに、キューの最新のサイズによって、**[Essentials]** の **[現在]** の値が増えます。
    - 下部の **[Metrics]\(メトリック\)** セクションの **[メッセージ]** グラフを見ると、このキューには 4 つの受信メッセージがあることがわかります。 

## <a name="receive-messages-from-a-queue"></a>キューからメッセージを受信する
このセクションでは、キューからメッセージを取得するコードを追加します。

1. メッセージや発生したエラーを処理する次のメソッドを `Program` クラスに追加します。 

    ```csharp
        // handle received messages
        static async Task MessageHandler(ProcessMessageEventArgs args)
        {
            string body = args.Message.Body.ToString();
            Console.WriteLine($"Received: {body}");

            // complete the message. messages is deleted from the queue. 
            await args.CompleteMessageAsync(args.Message);
        }

        // handle any errors when receiving messages
        static Task ErrorHandler(ProcessErrorEventArgs args)
        {
            Console.WriteLine(args.Exception.ToString());
            return Task.CompletedTask;
        }
    ```
1. `Program` クラスに `ReceiveMessagesAsync` という名前のメソッドを追加し、メッセージを受信するための次のコードを追加します。 

    ```csharp
        static async Task ReceiveMessagesAsync()
        {
            await using (ServiceBusClient client = new ServiceBusClient(connectionString))
            {
                // create a processor that we can use to process the messages
                ServiceBusProcessor processor = client.CreateProcessor(queueName, new ServiceBusProcessorOptions());

                // add handler to process messages
                processor.ProcessMessageAsync += MessageHandler;

                // add handler to process any errors
                processor.ProcessErrorAsync += ErrorHandler;

                // start processing 
                await processor.StartProcessingAsync();

                Console.WriteLine("Wait for a minute and then press any key to end the processing");
                Console.ReadKey();

                // stop processing 
                Console.WriteLine("\nStopping the receiver...");
                await processor.StopProcessingAsync();
                Console.WriteLine("Stopped receiving messages");
            }
        }
    ```
1. `Main` メソッドから `ReceiveMessagesAsync` メソッドへの呼び出しを追加します。 単にメッセージの受信をテストしたい場合は、`SendMessagesAsync` メソッドをコメントアウトしてください。 そうしなかった場合は、キューに送信された 4 つのメッセージが重複して表示されます。 

    ```csharp
        static async Task Main()
        {
            // send a message to the queue
            await SendMessageAsync();

            // send a batch of messages to the queue
            await SendMessageBatchAsync();

            // receive message from the queue
            await ReceiveMessagesAsync();
        }
    ```

## <a name="run-the-app"></a>アプリを実行する
アプリケーションを実行します。 しばらく待ってから、任意のキーを押してメッセージの受信を停止します。 次の出力が表示されます (押したのは Space キー)。 

```console
Sent a single message to the queue: myqueue
Sent a batch of messages to the queue: myqueue
Wait for a minute and then press any key to end the processing
Received: Hello world!
Received: First message in the batch
Received: Second message in the batch
Received: Third message in the batch
Received: Hello world!
Received: First message in the batch
Received: Second message in the batch
Received: Third message in the batch

Stopping the receiver...
Stopped receiving messages
```

もう一度ポータルを確認します。 

- **[アクティブなメッセージ数]** と **[現在]** の値は **0** になりました。
- 下部の **[Metrics]\(メトリック\)** セクションの **[メッセージ]** グラフを見ると、このキューには 8 つの受信メッセージと 8 つの送信メッセージがあることがわかります。 

    :::image type="content" source="./media/service-bus-dotnet-get-started-with-queues/queue-messages-size-final.png" alt-text="受信後のアクティブなメッセージとサイズ" lightbox="./media/service-bus-dotnet-get-started-with-queues/queue-messages-size-final.png":::

## <a name="next-steps"></a>次のステップ
次のドキュメントおよびサンプルを参照してください。

- [.NET 用の Azure Service Bus クライアント ライブラリ - Readme](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus)
- [GitHub のサンプル](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus/samples)
- [.NET API リファレンス](/dotnet/api/azure.messaging.servicebus?preserve-view=true)