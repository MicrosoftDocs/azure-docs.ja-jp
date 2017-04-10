---
title: ".NET Standard を使用して Azure Event Hubs にイベントを送信する | Microsoft Docs"
description: ".NET Standard で Event Hubs へのイベント送信を開始する"
services: event-hubs
documentationcenter: na
author: jtaubensee
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2017
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: 6e0ad6b5bec11c5197dd7bded64168a1b8cc2fdd
ms.openlocfilehash: 420a79a3f98500fa5e9054c3a59d9ac20ecb6cbf
ms.lasthandoff: 03/28/2017

---

# <a name="get-started-sending-messages-to-azure-event-hubs-in-net-standard"></a>.NET Standard で Azure Event Hubs へのメッセージ送信を開始する

> [!NOTE]
> このサンプルは [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/SampleSender) で入手できます。

このチュートリアルでは、Event Hub に一連のメッセージを送信する .NET Core コンソール アプリケーションの記述方法を示します。 [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/SampleSender) ソリューションをそのまま実行するには、`EhConnectionString` および `EhEntityPath` 文字列を Event Hub の値に置き換えるか、 このチュートリアルの手順に従って独自のものを作成します。

## <a name="prerequisites"></a>前提条件

* [Microsoft Visual Studio 2015 または 2017](http://www.visualstudio.com)。 このチュートリアルの例では Visual Studio 2015 を使用しますが、Visual Studio 2017 もサポートされています。
* [.NET Core Visual Studio 2015 または 2017 ツール](http://www.microsoft.com/net/core)。
* Azure サブスクリプション。
* Event Hubs 名前空間。

Event Hub にメッセージを送信するために、Visual Studio を使って C# コンソール アプリケーションを記述します。

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Event Hubs 名前空間とイベント ハブを作成する

最初のステップでは、[Azure Portal](https://portal.azure.com) を使用して Event Hubs 型の名前空間を作成し、アプリケーションがイベント ハブと通信するために必要な管理資格情報を取得します。 名前空間とイベント ハブを作成するには、[この記事](event-hubs-create.md)の手順を踏み、次のステップに進みます。

## <a name="create-a-console-application"></a>コンソール アプリケーションの作成

Visual Studio を起動します。 **[ファイル]** メニューの **[新規作成]** をクリックし、**[プロジェクト]** をクリックします。 .NET Core コンソール アプリケーションを作成します。

![新しいプロジェクト][1]

## <a name="add-the-event-hubs-nuget-package"></a>Event Hubs NuGet パッケージの追加

[`Microsoft.Azure.EventHubs`](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) NuGet パッケージをプロジェクトに追加します。

## <a name="write-some-code-to-send-messages-to-the-event-hub"></a>Event Hub にメッセージを送信するコードの記述

1. Program.cs ファイルの先頭に次の `using` ステートメントを追加します。

    ```csharp
    using Microsoft.Azure.EventHubs;
    using System.Text;
    using System.Threading.Tasks;
    ```

2. Event Hubs 接続文字列とエンティティ パス (個別の Event Hub 名) の `Program` クラスに定数を追加します。 中かっこ内のプレースホルダーを、Event Hub の作成時に取得した適切な値に置き換えます。

    ```csharp
    private static EventHubClient eventHubClient;
    private const string EhConnectionString = "{Event Hubs connection string}";
    private const string EhEntityPath = "{Event Hub path/name}";
    ```

3. 次のように、`Program` クラスに `MainAsync` という名前の新しいメソッドを追加します。

    ```csharp
    private static async Task MainAsync(string[] args)
    {
        // Creates an EventHubsConnectionStringBuilder object from the connection string, and sets the EntityPath.
        // Typically, the connection string should have the entity path in it, but for the sake of this simple scenario
        // we are using the connection string from the namespace.
        var connectionStringBuilder = new EventHubsConnectionStringBuilder(EhConnectionString)
        {
            EntityPath = EhEntityPath
        };

        eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());

        await SendMessagesToEventHub(100);

        await eventHubClient.CloseAsync();

        Console.WriteLine("Press ENTER to exit.");
        Console.ReadLine();
    }
    ```

4. 次のように、`Program` クラスに `SendMessagesToEventHub` という名前の新しいメソッドを追加します。

    ```csharp
    // Creates an Event Hub client and sends 100 messages to the event hub.
    private static async Task SendMessagesToEventHub(int numMessagesToSend)
    {
        for (var i = 0; i < numMessagesToSend; i++)
        {
            try
            {
                var message = $"Message {i}";
                Console.WriteLine($"Sending message: {message}");
                await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
            }
            catch (Exception exception)
            {
                Console.WriteLine($"{DateTime.Now} > Exception: {exception.Message}");
            }

            await Task.Delay(10);
        }

        Console.WriteLine($"{numMessagesToSend} messages sent.");
    }
    ```

5. `Program` クラスの `Main` メソッドに次のコードを追加します。

    ```csharp
    MainAsync(args).GetAwaiter().GetResult();
    ```

   Program.cs は次のようになります。

    ```csharp
    namespace SampleSender
    {
        using System;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.Azure.EventHubs;

        public class Program
        {
            private static EventHubClient eventHubClient;
            private const string EhConnectionString = "{Event Hubs connection string}";
            private const string EhEntityPath = "{Event Hub path/name}";

            public static void Main(string[] args)
            {
                MainAsync(args).GetAwaiter().GetResult();
            }

            private static async Task MainAsync(string[] args)
            {
                // Creates an EventHubsConnectionStringBuilder object from the connection string, and sets the EntityPath.
                // Typically, the connection string should have the entity path in it, but for the sake of this simple scenario
                // we are using the connection string from the namespace.
                var connectionStringBuilder = new EventHubsConnectionStringBuilder(EhConnectionString)
                {
                    EntityPath = EhEntityPath
                };

                eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());

                await SendMessagesToEventHub(100);

                await eventHubClient.CloseAsync();

                Console.WriteLine("Press ENTER to exit.");
                Console.ReadLine();
            }

            // Creates an Event Hub client and sends 100 messages to the event hub.
            private static async Task SendMessagesToEventHub(int numMessagesToSend)
            {
                for (var i = 0; i < numMessagesToSend; i++)
                {
                    try
                    {
                        var message = $"Message {i}";
                        Console.WriteLine($"Sending message: {message}");
                        await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
                    }
                    catch (Exception exception)
                    {
                        Console.WriteLine($"{DateTime.Now} > Exception: {exception.Message}");
                    }

                    await Task.Delay(10);
                }

                Console.WriteLine($"{numMessagesToSend} messages sent.");
            }
        }
    }
    ```

6. プログラムを実行し、エラーがないことを確認します。

お疲れさまでした。 メッセージを Event Hub に送信しました。

## <a name="next-steps"></a>次のステップ
Event Hubs の詳細については、次のリンク先を参照してください:

* [Event Hubs からイベントを受信する](event-hubs-dotnet-standard-getstarted-receive-eph.md)
* [Event Hubs の概要](event-hubs-what-is-event-hubs.md)
* [Event Hub を作成する](event-hubs-create.md)
* [Event Hubs の FAQ](event-hubs-faq.md)

[1]: ./media/event-hubs-dotnet-standard-getstarted-send/netcore.png

