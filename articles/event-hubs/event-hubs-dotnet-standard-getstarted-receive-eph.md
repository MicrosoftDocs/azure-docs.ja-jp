---
title: ".NET Standard を使用して Azure Event Hubs からイベントを受信する | Microsoft Docs"
description: ".NET standard で EventProcessorHost を使用したメッセージ受信を開始する"
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
ms.openlocfilehash: c86a1feee02bbf8580a40119ac140528217e435d
ms.lasthandoff: 03/28/2017

---

# <a name="get-started-receiving-messages-with-the-event-processor-host-in-net-standard"></a>.NET Standard で EventProcessorHost を使用したメッセージ受信を開始する

> [!NOTE]
> このサンプルは [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/SampleEphReceiver) で入手できます。

このチュートリアルでは、**EventProcessorHost** を使用して Event Hub からメッセージを受信する .NET Core コンソール アプリケーションの記述方法を説明します。 [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/SampleEphReceiver) ソリューションをそのまま実行するには、文字列を Event Hub とストレージ アカウントの値に置き換えます。 または、このチュートリアルの手順に従って独自のものを作成します。

## <a name="prerequisites"></a>前提条件

* [Microsoft Visual Studio 2015 または 2017](http://www.visualstudio.com)。 このチュートリアルの例では Visual Studio 2015 を使用しますが、Visual Studio 2017 もサポートされています。
* [.NET Core Visual Studio 2015 または 2017 ツール](http://www.microsoft.com/net/core)。
* Azure サブスクリプション。
* Azure Event Hubs 名前空間。
* Azure ストレージ アカウント。

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Event Hubs 名前空間とイベント ハブを作成する  

最初のステップでは、[Azure Portal](https://portal.azure.com) を使用して Event Hubs 型の名前空間を作成し、アプリケーションがイベント ハブと通信するために必要な管理資格情報を取得します。 名前空間とイベント ハブを作成するには、[この記事](event-hubs-create.md)の手順を踏み、次のステップに進みます。  

## <a name="create-an-azure-storage-account"></a>Azure のストレージ アカウントの作成  

1. [Azure Portal](https://portal.azure.com) にサインインします。  
2. ポータルの左側のナビゲーション ウィンドウで **[新規]** をクリックし、**[ストレージ]**、**[ストレージ アカウント]** の順にクリックします。  
3. ストレージ アカウントのブレードで、フィールドを入力し、**[作成]** をクリックします。

    ![[ストレージ アカウントの作成]][1]

4. **[デプロイメントが成功しました]** メッセージが表示されたら、新しいストレージ アカウント名をクリックし、 **[要点]** ブレードで **[BLOB]** をクリックします。 **[Blob service]** ブレードが開いたら、上部にある **[+ Container (+ コンテナー)]** をクリックします。 コンテナーに名前を付け、**[Blob service]** ブレードを閉じます。  
5. 左のブレードにある **[アクセス キー]** をクリックして、ストレージ コンテナーとストレージ アカウントの名前、および **[key1]** の値をコピーします。 これらの値をメモ帳などに一時的に保存します。  

## <a name="create-a-console-application"></a>コンソール アプリケーションの作成

Visual Studio を起動します。 **[ファイル]** メニューの **[新規作成]** をクリックし、**[プロジェクト]** をクリックします。 .NET Core コンソール アプリケーションを作成します。

![新しいプロジェクト][2]

## <a name="add-the-event-hubs-nuget-package"></a>Event Hubs NuGet パッケージの追加

次の NuGet パッケージをプロジェクトに追加します。
* [`Microsoft.Azure.EventHubs`](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/)
* [`Microsoft.Azure.EventHubs.Processor`](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/)

## <a name="implement-the-ieventprocessor-interface"></a>IEventProcessor インターフェイスの実装

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

## <a name="write-a-main-console-method-that-uses-the-simpleeventprocessor-class-to-receive-messages"></a>SimpleEventProcessor クラスを使用するメイン コンソール メソッドを記述してメッセージを受信

1. Program.cs ファイルの先頭に次の `using` ステートメントを追加します。

    ```csharp
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
    using System.Threading.Tasks;
    ```

2. Event Hubs 接続文字列、Event Hub 名、ストレージ アカウント コンテナー名、ストレージ アカウント名、およびストレージ アカウント キーの `Program` クラスに定数を追加します。 プレースホルダーを対応する値に置き換えて、次のコードを追加します。

    ```csharp
    private const string EhConnectionString = "{Event Hubs connection string}";
    private const string EhEntityPath = "{Event Hub path/name}";
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
            EhEntityPath,
            PartitionReceiver.DefaultConsumerGroupName,
            EhConnectionString,
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
            private const string EhConnectionString = "{Event Hubs connection string}";
            private const string EhEntityPath = "{Event Hub path/name}";
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
                    EhEntityPath,
                    PartitionReceiver.DefaultConsumerGroupName,
                    EhConnectionString,
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

お疲れさまでした。 イベント プロセッサ ホストを使用して、イベント ハブからメッセージを受信しました。

## <a name="next-steps"></a>次のステップ
Event Hubs の詳細については、次のリンク先を参照してください:

* [Event Hubs の概要](event-hubs-what-is-event-hubs.md)
* [Event Hub を作成する](event-hubs-create.md)
* [Event Hubs の FAQ](event-hubs-faq.md)

[1]: ./media/event-hubs-dotnet-standard-getstarted-receive-eph/event-hubs-python1.png
[2]: ./media/event-hubs-dotnet-standard-getstarted-receive-eph/netcore.png

