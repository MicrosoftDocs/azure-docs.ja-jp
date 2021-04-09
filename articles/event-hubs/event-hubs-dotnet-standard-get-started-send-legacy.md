---
title: .NET を使用して Azure Event Hubs との間でイベントを送受信する (以前)
description: この記事では、以前の Microsoft.Azure.EventHubs パッケージを使用して、Azure Event Hubs との間でイベントを送受信する .NET Core アプリを作成する方法について説明します。
ms.topic: quickstart
ms.date: 06/23/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 7092c3a4c32fe2ad46b71c5a796ac811e4253dbb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100653093"
---
# <a name="send-events-to-or-receive-events-from-azure-event-hubs-using-net-core-microsoftazureeventhubs"></a>.NET Core を使用して Azure Event Hubs との間でイベントを送受信する(Microsoft.Azure.EventHubs)
このクイックスタートでは、**Microsoft.Azure.EventHubs** .NET Core ライブラリを使用して、イベント ハブとの間でイベントを送受信する方法について説明します。

> [!WARNING]
> このクイックスタートでは、以前の **Microsoft.Azure.EventHubs** パッケージを使用します。 最新の **Azure.Messaging.EventHubs** ライブラリを使用するクイックスタートについては、[Azure.Messaging.EventHubs ライブラリを使用したイベントの送受信](event-hubs-dotnet-standard-getstarted-send.md)に関するページを参照してください。 古いライブラリから新しいライブラリを使用するようにアプリケーションを移行するには、[Microsoft.Azure.EventHubs から Azure.Messaging.EventHubs への移行のガイド](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md)を参照してください。

## <a name="prerequisites"></a>前提条件
Azure Event Hubs を初めて使用する場合は、このクイックスタートを行う前に[イベント ハブの概要](event-hubs-about.md)を参照してください。 

このクイック スタートを完了するには、次の前提条件を用意しておく必要があります。

- **Microsoft Azure サブスクリプション**。 Azure Event Hubs を含む Azure サービスを使用するには、サブスクリプションが必要です。  既存の Microsoft Azure アカウントをお持ちでない場合は、[アカウントを作成する](https://azure.microsoft.com)際に、[無料試用版](https://azure.microsoft.com/free/)にサインアップするか、MSDN サブスクライバー特典を利用できます。
- [Microsoft Visual Studio 2019](https://www.visualstudio.com)。
- [.NET Core Visual Studio 2015 または 2017 ツール](https://www.microsoft.com/net/core)。 
- **Event Hubs 名前空間とイベント ハブを作成する**。 最初の手順では、[Azure Portal](https://portal.azure.com) を使用して Event Hubs 型の名前空間を作成し、アプリケーションがイベント ハブと通信するために必要な管理資格情報を取得します。 名前空間とイベント ハブを作成するには、[こちらの記事](event-hubs-create.md)の手順に従います。 その後、次の記事の手順に従って、**イベント ハブ名前空間用の接続文字列** を取得します。[接続文字列を取得する](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)。 この接続文字列は、このクイックスタートの後の手順で必要になります。

## <a name="send-events"></a>送信イベント 
このセクションでは、イベント ハブにイベントを送信する .NET Core コンソール アプリケーションの作成方法を説明します。 

> [!NOTE]
> このクイック スタートをサンプルとして [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleSender) からダウンロードし、`EventHubConnectionString` と `EventHubName` の文字列を対象のイベント ハブの値に置き換え、実行します。 または、このクイックスタートの手順に従って独自のものを作成します。


### <a name="create-a-console-application"></a>コンソール アプリケーションの作成

Visual Studio を起動します。 **[ファイル]** メニューの **[新規作成]** をクリックし、 **[プロジェクト]** をクリックします。 .NET Core コンソール アプリケーションを作成します。

![新しいプロジェクト](./media/event-hubs-dotnet-standard-getstarted-send/netcoresnd.png)

### <a name="add-the-event-hubs-nuget-package"></a>Event Hubs NuGet パッケージの追加

[`Microsoft.Azure.EventHubs`](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) .NET Core ライブラリの NuGet パッケージを、次の手順でプロジェクトに追加します。 

1. 新しく作成したプロジェクトを右クリックし、**[NuGet パッケージの管理]** を選択します。
2. **[参照]** タブをクリックして、"Microsoft.Azure.EventHubs" を検索し、**Microsoft.Azure.EventHubs** パッケージを選択します。 **[インストール]** をクリックし、インストールが完了したら、このダイアログ ボックスを閉じます。

### <a name="write-code-to-send-messages-to-the-event-hub"></a>イベント ハブにメッセージを送信するコードの記述

1. Program.cs ファイルの先頭に次の `using` ステートメントを追加します。

    ```csharp
    using Microsoft.Azure.EventHubs;
    using System.Text;
    using System.Threading.Tasks;
    ```

2. Event Hubs 接続文字列とエンティティ パス (個別のイベント ハブ名) の `Program` クラスに定数を追加します。 中かっこ内のプレースホルダーを、イベント ハブの作成時に取得した適切な値に置き換えます。 `{Event Hubs connection string}` がイベント ハブの文字列ではなく名前空間レベルの接続文字列であることを確認します。 

    ```csharp
    private static EventHubClient eventHubClient;
    private const string EventHubConnectionString = "{Event Hubs connection string}";
    private const string EventHubName = "{Event Hub path/name}";
    ```

3. 次のように、`Program` クラスに `MainAsync` という名前の新しいメソッドを追加します。

    ```csharp
    private static async Task MainAsync(string[] args)
    {
        // Creates an EventHubsConnectionStringBuilder object from the connection string, and sets the EntityPath.
        // Typically, the connection string should have the entity path in it, but this simple scenario
        // uses the connection string from the namespace.
        var connectionStringBuilder = new EventHubsConnectionStringBuilder(EventHubConnectionString)
        {
            EntityPath = EventHubName
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
    // Uses the event hub client to send 100 messages to the event hub.
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
            private const string EventHubConnectionString = "{Event Hubs connection string}";
            private const string EventHubName = "{Event Hub path/name}";

            public static void Main(string[] args)
            {
                MainAsync(args).GetAwaiter().GetResult();
            }

            private static async Task MainAsync(string[] args)
            {
                // Creates an EventHubsConnectionStringBuilder object from the connection string, and sets the EntityPath.
                // Typically, the connection string should have the entity path in it, but for the sake of this simple scenario
                // we are using the connection string from the namespace.
                var connectionStringBuilder = new EventHubsConnectionStringBuilder(EventHubConnectionString)
                {
                    EntityPath = EventHubName
                };

                eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());

                await SendMessagesToEventHub(100);

                await eventHubClient.CloseAsync();

                Console.WriteLine("Press ENTER to exit.");
                Console.ReadLine();
            }

            // Uses the event hub client to send 100 messages to the event hub.
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

## <a name="receive-events"></a>受信イベント
このセクションでは、[イベント プロセッサ ホスト](event-hubs-event-processor-host.md)を使用して Event Hub からメッセージを受信する .NET Core コンソール アプリケーションの記述方法を説明します。 [イベント プロセッサ ホスト](event-hubs-event-processor-host.md)は、永続的なチェックポイントの管理によってイベント ハブのイベントの受信を簡素化し、並列してそれらのイベント ハブから受信する .NET クラスです。 イベント プロセッサ ホストを使用すると、さまざまなノードでホストされている場合でも、複数の受信側間でイベントを分割できます。 この例では、受信側が単一の場合にイベント プロセッサ ホストを使用する方法を示します。
> [!NOTE]
> このクイック スタートをサンプルとして [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver) からダウンロードし、`EventHubConnectionString`、`EventHubName`、`StorageAccountName`、`StorageAccountKey`、`StorageContainerName` の各文字列を対象のイベント ハブの値に置き換え、実行します。 または、このチュートリアルの手順に従って独自のものを作成します。

[!INCLUDE [event-hubs-create-storage](../../includes/event-hubs-create-storage.md)]

### <a name="create-a-console-application"></a>コンソール アプリケーションの作成

Visual Studio を起動します。 **[ファイル]** メニューの **[新規作成]** をクリックし、 **[プロジェクト]** をクリックします。 .NET Core コンソール アプリケーションを作成します。

![受信用の新しいプロジェクト](./media/event-hubs-dotnet-standard-getstarted-receive-eph/netcorercv.png)

### <a name="add-the-event-hubs-nuget-package"></a>Event Hubs NuGet パッケージの追加

次の手順に従って、.NET Standard ライブラリ NuGet パッケージの [**Microsoft.Azure.EventHubs**](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) と [**Microsoft.Azure.EventHubs.Processor**](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/) をプロジェクトに追加します。 

1. 新しく作成したプロジェクトを右クリックし、**[NuGet パッケージの管理]** を選択します。
2. **[参照]** タブをクリックし、**Microsoft.Azure.EventHubs** を探して、**Microsoft.Azure.EventHubs** パッケージを選びます。 **[インストール]** をクリックし、インストールが完了したら、このダイアログ ボックスを閉じます。
3. 手順 1 と 2 を繰り返し、**Microsoft.Azure.EventHubs.Processor** パッケージをインストールします。

### <a name="implement-the-ieventprocessor-interface"></a>IEventProcessor インターフェイスの実装

1. ソリューション エクスプローラーで、プロジェクトを右クリックし、**[追加]**、**[クラス]** の順にクリックします。 新しいクラスに **SimpleEventProcessor** の名前を付けます。

2. SimpleEventProcessor.cs ファイルを開き、ファイルの先頭に `using` ステートメントを追加します。

    ```csharp
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
    using System.Threading.Tasks;
    ```

3. `IEventProcessor` インターフェイスを実装します。 `SimpleEventProcessor` クラスの内容全体を次のコードに置き換えます。

    ```csharp
    public class SimpleEventProcessor : IEventProcessor
    {
        public Task CloseAsync(PartitionContext context, CloseReason reason)
        {
            Console.WriteLine($"Processor Shutting Down. Partition '{context.PartitionId}', Reason: '{reason}'.");
            return Task.CompletedTask;
        }

        public Task OpenAsync(PartitionContext context)
        {
            Console.WriteLine($"SimpleEventProcessor initialized. Partition: '{context.PartitionId}'");
            return Task.CompletedTask;
        }

        public Task ProcessErrorAsync(PartitionContext context, Exception error)
        {
            Console.WriteLine($"Error on Partition: {context.PartitionId}, Error: {error.Message}");
            return Task.CompletedTask;
        }

        public Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
        {
            foreach (var eventData in messages)
            {
                var data = Encoding.UTF8.GetString(eventData.Body.Array, eventData.Body.Offset, eventData.Body.Count);
                Console.WriteLine($"Message received. Partition: '{context.PartitionId}', Data: '{data}'");
            }

            return context.CheckpointAsync();
        }
    }
    ```

### <a name="update-the-main-method-to-use-simpleeventprocessor"></a>SimpleEventProcessor を使用する Main メソッドを更新する

1. Program.cs ファイルの先頭に次の `using` ステートメントを追加します。

    ```csharp
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
    using System.Threading.Tasks;
    ```

2. イベント ハブ接続文字列、イベント ハブ名、ストレージ アカウント コンテナー名、ストレージ アカウント名、およびストレージ アカウント キーの `Program` クラスに定数を追加します。 プレースホルダーを対応する値に置き換えて、次のコードを追加します。

    ```csharp
    private const string EventHubConnectionString = "{Event Hubs connection string}";
    private const string EventHubName = "{Event Hub path/name}";
    private const string StorageContainerName = "{Storage account container name}";
    private const string StorageAccountName = "{Storage account name}";
    private const string StorageAccountKey = "{Storage account key}";

    private static readonly string StorageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", StorageAccountName, StorageAccountKey);
    ```   

3. 次のように、`Program` クラスに `MainAsync` という名前の新しいメソッドを追加します。

    ```csharp
    private static async Task MainAsync(string[] args)
    {
        Console.WriteLine("Registering EventProcessor...");

        var eventProcessorHost = new EventProcessorHost(
            EventHubName,
            PartitionReceiver.DefaultConsumerGroupName,
            EventHubConnectionString,
            StorageConnectionString,
            StorageContainerName);

        // Registers the Event Processor Host and starts receiving messages
        await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();

        Console.WriteLine("Receiving. Press ENTER to stop worker.");
        Console.ReadLine();

        // Disposes of the Event Processor Host
        await eventProcessorHost.UnregisterEventProcessorAsync();
    }
    ```

3. `Main` メソッドに次のコード行を追加します。

    ```csharp
    MainAsync(args).GetAwaiter().GetResult();
    ```

    Program.cs ファイルは次のようになります。

    ```csharp
    namespace SampleEphReceiver
    {

        public class Program
        {
            private const string EventHubConnectionString = "{Event Hubs connection string}";
            private const string EventHubName = "{Event Hub path/name}";
            private const string StorageContainerName = "{Storage account container name}";
            private const string StorageAccountName = "{Storage account name}";
            private const string StorageAccountKey = "{Storage account key}";

            private static readonly string StorageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", StorageAccountName, StorageAccountKey);

            public static void Main(string[] args)
            {
                MainAsync(args).GetAwaiter().GetResult();
            }

            private static async Task MainAsync(string[] args)
            {
                Console.WriteLine("Registering EventProcessor...");

                var eventProcessorHost = new EventProcessorHost(
                    EventHubName,
                    PartitionReceiver.DefaultConsumerGroupName,
                    EventHubConnectionString,
                    StorageConnectionString,
                    StorageContainerName);

                // Registers the Event Processor Host and starts receiving messages
                await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();

                Console.WriteLine("Receiving. Press ENTER to stop worker.");
                Console.ReadLine();

                // Disposes of the Event Processor Host
                await eventProcessorHost.UnregisterEventProcessorAsync();
            }
        }
    }
    ```

4. プログラムを実行し、エラーがないことを確認します。


## <a name="next-steps"></a>次のステップ
次の記事を参照してください。

- [Azure ロールベースのアクセス制御 (Azure RBAC) のサンプル](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac)。 
    
    これらのサンプルでは、古い **Microsoft.Azure.EventHubs** ライブラリが使用されていますが、最新の **Azure.Messaging.EventHubs** ライブラリを使用するように簡単に更新できます。 古いライブラリから新しいライブラリを使用するようにサンプルを移行する方法については、[Microsoft.Azure.EventHubs から Azure.Messaging.EventHubs への移行のガイド](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md)に関する記事を参照してください。
- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Azure Event Hubs の機能と用語](event-hubs-features.md)
- [Event Hubs の FAQ](event-hubs-faq.md)


