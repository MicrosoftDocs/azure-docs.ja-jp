---
title: .NET を使用して Azure Event Hubs との間でイベントを送受信する (最新バージョン)
description: この記事では、最新の Azure.Messaging.EventHubs パッケージを使用して、Azure Event Hubs との間でイベントを送受信する .NET Core アプリケーションを作成する方法について説明します。
ms.topic: quickstart
ms.date: 09/25/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 36551a52bc095a37aa5176ab945dd5d27c640954
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97504612"
---
# <a name="send-events-to-and-receive-events-from-azure-event-hubs---net-azuremessagingeventhubs"></a>Azure Event Hubs との間でイベントを送受信する - .NET (Azure.Messaging.EventHubs) 
このクイックスタートでは、**Azure.Messaging.EventHubs** .NET ライブラリを使用して、イベント ハブとの間でイベントを送受信する方法について説明します。 

> [!IMPORTANT]
> このクイックスタートでは、新しい **Azure.Messaging.EventHubs** ライブラリを使用します。 以前の **Microsoft.Azure.EventHubs** ライブラリを使用するクイックスタートについては、[Microsoft.Azure.EventHubs ライブラリを使用したイベントの送受信](event-hubs-dotnet-standard-get-started-send-legacy.md)に関するページを参照してください。 



## <a name="prerequisites"></a>前提条件
Azure Event Hubs を初めて使用する場合は、このクイックスタートを行う前に [Event Hubs の概要](event-hubs-about.md)を参照してください。 

このクイック スタートを完了するには、次の前提条件を用意しておく必要があります。

- **Microsoft Azure サブスクリプション**。 Azure Event Hubs を含む Azure サービスを使用するには、サブスクリプションが必要です。  既存の Microsoft Azure アカウントをお持ちでない場合は、[アカウントを作成する](https://azure.microsoft.com)際に、[無料試用版](https://azure.microsoft.com/free/)にサインアップするか、MSDN サブスクライバー特典を利用できます。
- **Microsoft Visual Studio 2019**。 Azure Event Hubs クライアント ライブラリでは、C# 8.0 で導入された新機能を利用しています。  以前のバージョンの C# 言語でライブラリを使用することもできますが、新しい構文は使用できません。 完全な構文を使用するには、[.NET Core SDK](https://dotnet.microsoft.com/download) 3.0 以降および[言語バージョン](/dotnet/csharp/language-reference/configure-language-version#override-a-default) を `latest` に設定して、コンパイルすることをお勧めします。 Visual Studio を使用している場合、Visual Studio 2019 より前のバージョンには、C# 8.0 プロジェクトをビルドするために必要なツールとの互換性がありません。 無料の Community エディションを含む Visual Studio 2019 は、[こちら](https://visualstudio.microsoft.com/vs/)からダウンロードできます。
- **Event Hubs 名前空間とイベント ハブを作成する**。 最初の手順では、[Azure Portal](https://portal.azure.com) を使用して Event Hubs 型の名前空間を作成し、アプリケーションがイベント ハブと通信するために必要な管理資格情報を取得します。 名前空間とイベント ハブを作成するには、[こちらの記事](event-hubs-create.md)の手順に従います。 その後、次の記事の手順に従って、**Event Hubs 名前空間用の接続文字列** を取得します: [接続文字列を取得する](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)。 この接続文字列は、このクイックスタートの後の手順で必要になります。

## <a name="send-events"></a>送信イベント 
このセクションでは、イベント ハブにイベントを送信する .NET Core コンソール アプリケーションの作成方法を説明します。 

### <a name="create-a-console-application"></a>コンソール アプリケーションの作成

1. Visual Studio 2019 を起動します。 
1. **[新しいプロジェクトの作成]** を選択します。 
1. **[新しいプロジェクトの作成]** ダイアログ ボックスで、次の手順に従います。このダイアログ ボックスが表示されない場合は、メニューで **[ファイル]** 、 **[新規]** 、 **[プロジェクト]** の順に選択します。 
    1. プログラミング言語として **[C#]** を選択します。
    1. アプリケーションの種類として **[コンソール]** を選択します。 
    1. 結果の一覧から **[コンソール アプリ (.NET Core)]** を選択します。 
    1. 次に、 **[次へ]** を選択します。 

        ![[新しいプロジェクト] ダイアログ ボックス](./media/getstarted-dotnet-standard-send-v2/new-send-project.png)    
1. プロジェクト名として「**EventHubsSender**」、ソリューション名として「**EventHubsQuickStart**」と入力し、 **[OK]** を選択してプロジェクトを作成します。 

    ![[C#] > [コンソール] アプリ](./media/getstarted-dotnet-standard-send-v2/project-solution-names.png)

### <a name="add-the-event-hubs-nuget-package"></a>Event Hubs NuGet パッケージの追加

1. メニューから **[ツール]**  >  **[NuGet パッケージ マネージャー]**  >  **[パッケージ マネージャー コンソール]** の順に選択します。 
1. 次のコマンドを実行して、**Azure.Messaging.EventHubs** NuGet パッケージをインストールします。

    ```cmd
    Install-Package Azure.Messaging.EventHubs
    ```


### <a name="write-code-to-send-messages-to-the-event-hub"></a>イベント ハブにメッセージを送信するコードの記述

1. **Program.cs** ファイルの先頭に次の `using` ステートメントを追加します。

    ```csharp
    using System;
    using System.Text;
    using System.Threading.Tasks;
    using Azure.Messaging.EventHubs;
    using Azure.Messaging.EventHubs.Producer;
    ```

2. Event Hubs 接続文字列およびイベント ハブ名を表す定数を、`Program` クラスに追加します。 山かっこ内のプレースホルダーを、イベント ハブの作成時に取得した適切な値で置き換えます。 `{Event Hubs namespace connection string}` がイベント ハブの文字列ではなく名前空間レベルの接続文字列であることを確認します。 

    ```csharp
    private const string connectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";
    private const string eventHubName = "<EVENT HUB NAME>";
    ```

3. `Main` メソッドを次の `async Main` メソッドで置き換えます。 詳細については、コードのコメントを参照してください。 

    ```csharp
        static async Task Main()
        {
            // Create a producer client that you can use to send events to an event hub
            await using (var producerClient = new EventHubProducerClient(connectionString, eventHubName))
            {
                // Create a batch of events 
                using EventDataBatch eventBatch = await producerClient.CreateBatchAsync();

                // Add events to the batch. An event is a represented by a collection of bytes and metadata. 
                eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("First event")));
                eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("Second event")));
                eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("Third event")));

                // Use the producer client to send the batch of events to the event hub
                await producerClient.SendAsync(eventBatch);
                Console.WriteLine("A batch of 3 events has been published.");
            }
        }
    ```
5. プロジェクトをビルドし、エラーがないことを確認します。
6. プログラムを実行し、確認メッセージが表示されるまで待ちます。 
7. Azure portal で、イベント ハブがメッセージを受信したことを確認できます。 **[メトリック]** セクションで **[メッセージ]** ビューに切り替えます。 ページを最新の情報に更新して、グラフを更新します。 メッセージが受信されたことが示されるまでに数秒かかることがあります。 

    [![イベント ハブがメッセージを受信したことを確認する](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)

    > [!NOTE]
    > より多くの情報を含むコメント付きの完全なソース コードについては、[GitHub 上のこちらのファイル](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/samples/Sample04_PublishingEvents.md)を参照してください

## <a name="receive-events"></a>受信イベント
このセクションでは、イベント プロセッサを使用してイベント ハブからメッセージを受信する .NET Core コンソール アプリケーションを作成する方法について説明します。 イベント プロセッサは、永続的なチェックポイントとそれらのイベント ハブからの並列受信を管理することによって、イベント ハブからのイベントの受信を簡素化します。 イベント プロセッサは、特定のイベントハブとコンシューマー グループに関連付けられています。 イベント ハブ内の複数のパーティションからイベントを受け取り、指定されたコードを使用して処理できるようにハンドラー デリゲートに渡します。 


> [!WARNING]
> このコードを Azure Stack Hub で実行すると、特定の Storage API バージョンを対象としている場合を除き、実行時エラーが発生します。 これは、Event Hubs SDK では、Azure で利用できる最新の Azure Storage API が使用されますが、Azure Stack Hub プラットフォームではこれを利用できない可能性があるためです。 Azure Stack Hub でサポートされる Storage Blob SDK のバージョンは、Azure で一般的に利用できるバージョンと異なる場合があります。 チェックポイント ストアとして Azure Blob Storage を使用している場合は、[Azure Stack Hub ビルドでサポートされている Azure Storage API バージョン](/azure-stack/user/azure-stack-acs-differences?#api-version)を確認し、コード内でそのバージョンを対象にします。 
>
> たとえば、Azure Stack Hub バージョン 2005 上で実行している場合、Storage サービスで利用できる最も高いバージョンは 2019-02-02 となります。 既定では、Event Hubs SDK クライアント ライブラリでは、Azure で利用できる最も高いバージョン (SDK のリリース時点では 2019-07-07) が使用されます。 この場合は、このセクションの手順に従うことに加え、Storage サービス API バージョン 2019-02-02 を対象とするコードを追加する必要もあります。 特定の Storage API バージョンを対象にする方法の例については、[GitHub のサンプル](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/)を参照してください。 
 

### <a name="create-an-azure-storage-and-a-blob-container"></a>Azure Storage と BLOB コンテナーを作成する
このクイックスタートでは、チェックポイント ストアとして Azure Storage を使用します。 Azure ストレージ アカウントを作成するには、次の手順に従います。 

1. [Azure ストレージ アカウントの作成](../storage/common/storage-account-create.md?tabs=azure-portal)
2. [BLOB コンテナーを作成する](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [ストレージ アカウントへの接続文字列を取得する](../storage/common/storage-configure-connection-string.md)

    接続文字列とコンテナー名を書き留めておきます。 これらは、受信コードで使用します。 


### <a name="create-a-project-for-the-receiver"></a>受信側のプロジェクトを作成する

1. ソリューション エクスプローラー ウィンドウで、 **[EventHubQuickStart]** ソリューションを右クリックし、 **[追加]** をポイントして、 **[新しいプロジェクト]** を選択します。 
1. **[コンソール アプリ (.NET Core)]** を選択し、 **[次へ]** を選択します。 
1. **[プロジェクト名]** に「**EventHubsReceiver**」と入力し、 **[作成]** を選択します。 

### <a name="add-the-event-hubs-nuget-package"></a>Event Hubs NuGet パッケージの追加

1. メニューから **[ツール]**  >  **[NuGet パッケージ マネージャー]**  >  **[パッケージ マネージャー コンソール]** の順に選択します。 
1. 次のコマンドを実行して、**Azure.Messaging.EventHubs** NuGet パッケージをインストールします。

    ```cmd
    Install-Package Azure.Messaging.EventHubs
    ```
1. 次のコマンドを実行して、**Azure.Messaging.EventHubs.Processor** NuGet パッケージをインストールします。

    ```cmd
    Install-Package Azure.Messaging.EventHubs.Processor
    ```    

### <a name="update-the-main-method"></a>Main メソッドを更新する 

1. **Program.cs** ファイルの先頭に次の `using` ステートメントを追加します。

    ```csharp
    using System;
    using System.Text;
    using System.Threading.Tasks;
    using Azure.Storage.Blobs;
    using Azure.Messaging.EventHubs;
    using Azure.Messaging.EventHubs.Consumer;
    using Azure.Messaging.EventHubs.Processor;
    ```
1. Event Hubs 接続文字列およびイベント ハブ名を表す定数を、`Program` クラスに追加します。 山かっこ内のプレースホルダーを、イベント ハブの作成時に取得した適切な値で置き換えます。 山かっこ内のプレースホルダーを、イベント ハブとストレージ アカウントの作成時に取得した適切な値 (アクセス キーとプライマリ接続文字列) で置き換えます。 `{Event Hubs namespace connection string}` がイベント ハブの文字列ではなく名前空間レベルの接続文字列であることを確認します。

    ```csharp
        private const string ehubNamespaceConnectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";
        private const string eventHubName = "<EVENT HUB NAME>";
        private const string blobStorageConnectionString = "<AZURE STORAGE CONNECTION STRING>";
        private const string blobContainerName = "<BLOB CONTAINER NAME>";
    ```
3. `Main` メソッドを次の `async Main` メソッドで置き換えます。 詳細については、コードのコメントを参照してください。 

    ```csharp
        static async Task Main()
        {
            // Read from the default consumer group: $Default
            string consumerGroup = EventHubConsumerClient.DefaultConsumerGroupName;

            // Create a blob container client that the event processor will use 
            BlobContainerClient storageClient = new BlobContainerClient(blobStorageConnectionString, blobContainerName);

            // Create an event processor client to process events in the event hub
            EventProcessorClient processor = new EventProcessorClient(storageClient, consumerGroup, ehubNamespaceConnectionString, eventHubName);

            // Register handlers for processing events and handling errors
            processor.ProcessEventAsync += ProcessEventHandler;
            processor.ProcessErrorAsync += ProcessErrorHandler;

            // Start the processing
            await processor.StartProcessingAsync();

            // Wait for 10 seconds for the events to be processed
            await Task.Delay(TimeSpan.FromSeconds(10));

            // Stop the processing
            await processor.StopProcessingAsync();
        }    
    ```
1. ここで、次のイベントおよびエラー ハンドラー メソッドをクラスに追加します。 

    ```csharp
        static async Task ProcessEventHandler(ProcessEventArgs eventArgs)
        {
            // Write the body of the event to the console window
            Console.WriteLine("\tReceived event: {0}", Encoding.UTF8.GetString(eventArgs.Data.Body.ToArray()));

            // Update checkpoint in the blob storage so that the app receives only new events the next time it's run
            await eventArgs.UpdateCheckpointAsync(eventArgs.CancellationToken);
        }

        static Task ProcessErrorHandler(ProcessErrorEventArgs eventArgs)
        {
            // Write details about the error to the console window
            Console.WriteLine($"\tPartition '{ eventArgs.PartitionId}': an unhandled exception was encountered. This was not expected to happen.");
            Console.WriteLine(eventArgs.Exception.Message);
            return Task.CompletedTask;
        }    
    ```
1. プロジェクトをビルドし、エラーがないことを確認します。

    > [!NOTE]
    > より多くの情報を含むコメント付きの完全なソース コードについては、[GitHub 上のこちらのファイル](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/Sample01_HelloWorld.md)を参照してください。
6. 受信側アプリを実行します。 
1. イベントが受信されたことを示すメッセージが表示されます。 

    ![受信されたイベント](./media/getstarted-dotnet-standard-send-v2/event-received.png)

    これらのイベントは、前に送信側プログラムを実行してイベント ハブに送信した 3 つのイベントです。 


## <a name="next-steps"></a>次のステップ
GitHub で次のサンプルを確認します。 

- [GitHub にある Event Hubs のサンプル](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs/samples)
- [GitHub にあるイベント プロセッサのサンプル](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples)
- [Azure ロールベースのアクセス制御 (Azure RBAC) のサンプル](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)
