---
title: Java を使用して Azure Event Hubs との間でイベントを送受信する (レガシ)
description: この記事では、以前の azure-eventhubs パッケージを使用して、Azure Event Hubs との間でイベントを送受信する Java アプリケーションを作成する方法について説明します。
ms.topic: quickstart
ms.date: 06/23/2020
ms.custom: devx-track-java
ms.openlocfilehash: e86ca76f3eb661e1407a02b58e60b62b391f5702
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97607985"
---
# <a name="use-java-to-send-events-to-or-receive-events-from-azure-event-hubs-azure-eventhubs"></a>Java を使用して Azure Event Hubs との間でイベントを送受信する (azure-eventhubs)

このクイックスタートでは、**azure-eventhubs** Java パッケージを使用して、イベント ハブとの間でイベントを送受信する方法について説明します。

> [!WARNING]
> このクイックスタートでは、以前の **azure-eventhubs** パッケージと **azure-eventhubs-eph** パッケージを使用します。 最新の **azure-messaging-eventhubs** パッケージを使用するクイックスタートについては、[azure-messaging-eventhubs を使用したイベントの送受信](event-hubs-java-get-started-send.md)に関するページを参照してください。 古いパッケージではなく新しいパッケージを使用するようにアプリケーションを移行するには、[azure-eventhubs から azure-messaging-eventhubs への移行ガイド](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/migration-guide.md)に関するページを参照してください。 

## <a name="prerequisites"></a>前提条件

Azure Event Hubs を初めて使用する場合は、このクイックスタートを行う前に[イベント ハブの概要](event-hubs-about.md)を参照してください。 

このクイック スタートを完了するには、次の前提条件を用意しておく必要があります。

- **Microsoft Azure サブスクリプション**。 Azure Event Hubs を含む Azure サービスを使用するには、サブスクリプションが必要です。  既存の Microsoft Azure アカウントをお持ちでない場合は、[アカウントを作成する](https://azure.microsoft.com)際に、[無料試用版](https://azure.microsoft.com/free/)にサインアップするか、MSDN サブスクライバー特典を利用できます。
- Java 開発環境。 このクイックスタートでは [Eclipse](https://www.eclipse.org/) を使用します。
- **Event Hubs 名前空間とイベント ハブを作成する**。 最初の手順では、[Azure Portal](https://portal.azure.com) を使用して Event Hubs 型の名前空間を作成し、アプリケーションがイベント ハブと通信するために必要な管理資格情報を取得します。 名前空間とイベント ハブを作成するには、[こちらの記事](event-hubs-create.md)の手順に従います。 その後、次の記事の手順に従って、イベント ハブ用のアクセス キーの値を取得します。[接続文字列を取得する](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)。 このクイックスタートの後半で記述するコードで、このアクセス キーを使用します。 既定のキー名は次のとおりです:**RootManageSharedAccessKey**。

## <a name="send-events"></a>送信イベント 
このセクションでは、イベント ハブにイベントを送信する Java アプリケーションの作成方法を説明します。 

> [!NOTE]
> このクイック スタートをサンプルとして [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/SimpleSend) からダウンロードし、`EventHubConnectionString` と `EventHubName` の文字列を対象のイベント ハブの値に置き換え、実行します。 または、このクイックスタートの手順に従って独自のものを作成します。

### <a name="add-reference-to-azure-event-hubs-library"></a>Azure Event Hubs ライブラリへの参照を追加する

[Maven Central Repository](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22) の Maven プロジェクトで Event Hub 用の Java クライアント ライブラリを使用できます。 Maven プロジェクト ファイル内で次の依存関係宣言を使用して、このライブラリを参照できます。

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>2.2.0</version>
</dependency>
```

ビルド環境の種類に応じて、[Maven Central Repository](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22) から最新リリースの JAR ファイルを明示的に取得できます。  

単純なイベント パブリッシャーの場合は、Event Hubs クライアント クラス用の *com.microsoft.azure.eventhubs* パッケージと、Azure Service Bus メッセージング クライアントと共有される一般的な例外などのユーティリティ クラス用の *com.microsoft.azure.servicebus* パッケージをインポートします。 

### <a name="write-code-to-send-messages-to-the-event-hub"></a>イベント ハブにメッセージを送信するコードの記述

次のサンプルでは、最初に、好みの Java 開発環境でコンソール/シェル アプリケーション用の新しい Maven プロジェクトを作成します。 `SimpleSend` という名前のクラスを追加し、このクラスに次のコードを追加します。

```java
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.microsoft.azure.eventhubs.ConnectionStringBuilder;
import com.microsoft.azure.eventhubs.EventData;
import com.microsoft.azure.eventhubs.EventHubClient;
import com.microsoft.azure.eventhubs.EventHubException;

import java.io.IOException;
import java.nio.charset.Charset;
import java.time.Instant;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Executors;
import java.util.concurrent.ScheduledExecutorService;

public class SimpleSend {

    public static void main(String[] args)
            throws EventHubException, ExecutionException, InterruptedException, IOException {

    }
 }
```

### <a name="construct-connection-string"></a>接続文字列を作成する

ConnectionStringBuilder クラスを使用して、Event Hubs クライアント インスタンスに渡す接続文字列の値を作成します。 プレースホルダーは、名前空間とイベント ハブの作成時に取得した値に置き換えます。

```java
        final ConnectionStringBuilder connStr = new ConnectionStringBuilder()
                .setNamespaceName("<EVENTHUB NAMESPACE") 
                .setEventHubName("EVENT HUB")
                .setSasKeyName("RootManageSharedAccessKey")
                .setSasKey("SHARED ACCESS KEY");
```

### <a name="write-code-to-send-events"></a>イベントを送信するコードを記述する

文字列を UTF-8 バイト エンコードに変換して単数のイベントを作成します。 その後、接続文字列から新しい Event Hubs クライアント インスタンスを作成し、メッセージを送信します。   

```java 
        final Gson gson = new GsonBuilder().create();

        // The Executor handles all asynchronous tasks and this is passed to the EventHubClient instance.
        // This enables the user to segregate their thread pool based on the work load.
        // This pool can then be shared across multiple EventHubClient instances.
        // The following sample uses a single thread executor, as there is only one EventHubClient instance,
        // handling different flavors of ingestion to Event Hubs here.
        final ScheduledExecutorService executorService = Executors.newScheduledThreadPool(4);

        // Each EventHubClient instance spins up a new TCP/TLS connection, which is expensive.
        // It is always a best practice to reuse these instances. The following sample shows this.
        final EventHubClient ehClient = EventHubClient.createSync(connStr.toString(), executorService);

        try {
            for (int i = 0; i < 10; i++) {

                String payload = "Message " + Integer.toString(i);
                byte[] payloadBytes = gson.toJson(payload).getBytes(Charset.defaultCharset());
                EventData sendEvent = EventData.create(payloadBytes);

                // Send - not tied to any partition
                // Event Hubs service will round-robin the events across all Event Hubs partitions.
                // This is the recommended & most reliable way to send to Event Hubs.
                ehClient.sendSync(sendEvent);
            }

            System.out.println(Instant.now() + ": Send Complete...");
            System.out.println("Press Enter to stop.");
            System.in.read();
        } finally {
            ehClient.closeSync();
            executorService.shutdown();
        }

``` 

プログラムをビルドして実行し、エラーがないことを確認します。

お疲れさまでした。 メッセージをイベント ハブに送信しました。

### <a name="appendix-how-messages-are-routed-to-eventhub-partitions"></a>付録: EventHub パーティションへのメッセージのルーティング動作

コンシューマーがメッセージを取得するためには、あらかじめパブリッシャーがそのメッセージをパーティションに発行する必要があります。 com.microsoft.azure.eventhubs.EventHubClient オブジェクトの sendSync() メソッドを使ってイベント ハブに同期的にメッセージを発行するとき、パーティション キーが指定されているかどうかに応じて、そのメッセージは特定のパーティションに送信されるか、または利用可能なすべてのパーティションにラウンド ロビン方式で分散されます。

パーティション キーを表す文字列が指定されている場合、そのキーをハッシュすることによって、イベントの送信先となるパーティションが決定されます。

パーティション キーが設定されていない場合は、利用可能なすべてのパーティションに対してラウンド ロビン式にメッセージが分散されます

```java
// Serialize the event into bytes
byte[] payloadBytes = gson.toJson(messagePayload).getBytes(Charset.defaultCharset());

// Use the bytes to construct an {@link EventData} object
EventData sendEvent = EventData.create(payloadBytes);

// Transmits the event to event hub without a partition key
// If a partition key is not set, then we will round-robin to all topic partitions
eventHubClient.sendSync(sendEvent);

//  the partitionKey will be hash'ed to determine the partitionId to send the eventData to.
eventHubClient.sendSync(sendEvent, partitionKey);

// close the client at the end of your program
eventHubClient.closeSync();

```

## <a name="receive-events"></a>受信イベント
このチュートリアルのコードは [GitHub の EventProcessorSample コード](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/EventProcessorSample)に基づくものであり、完全に動作するアプリケーションを表示する場合に確認することができます。

### <a name="receive-messages-with-eventprocessorhost-in-java"></a>Java の EventProcessorHost を使用したメッセージの受信

**EventProcessorHost** は、永続的なチェックポイントと、Event Hubs からの並列受信を管理することで、Event Hubs のイベントの受信を簡素化する Java クラスです。 EventProcessorHost を使用すると、異なる複数のノードでホストされている場合でも、複数の受信側の間でイベントを分割することができます。 この例では、受信側が単一の場合に EventProcessorHost を使用する方法を示します。

### <a name="create-a-storage-account"></a>ストレージ アカウントの作成

EventProcessorHost を使用するには、[Azure Storage アカウント][Azure Storage アカウント]が必要です。

1. [Azure portal](https://portal.azure.com) にサインインし、画面左側の **[Create a resource]\(リソースの作成\)** を選択します。
2. **[ストレージ]** を選択し、**[ストレージ アカウント]** を選択します。 **[ストレージ アカウントの作成]** ウィンドウで、ストレージ アカウントの名前を入力します。 残りのフィールドを完了し、目的の地域を選択し、**[作成]** を選択します。

    ![Azure portal でストレージ アカウントを作成する](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-azure-storage-account.png)

3. 新しく作成したストレージ アカウントを選択し、**[アクセス キー]** を選択します。

    ![Azure portal でアクセス キーを取得する](./media/event-hubs-dotnet-framework-getstarted-receive-eph/select-azure-storage-access-keys.png)

    key1 の値を一時的な場所にコピーします。 このチュートリアルの後の方で、それを使用します。

### <a name="create-a-java-project-using-the-eventprocessor-host"></a>EventProcessor ホストを使用した Java プロジェクトの作成

Event Hubs の Java クライアント ライブラリは、 [Maven セントラル リポジトリ](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22)の Maven プロジェクトで利用でき、Maven プロジェクト ファイル内の以下の依存関係宣言を使用して参照できます。 

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>2.2.0</version>
</dependency>
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs-eph</artifactId>
    <version>2.4.0</version>
</dependency>
```

ビルド環境の種類に応じて、[Maven Central Repository](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22) から最新リリースの JAR ファイルを明示的に取得できます。

1. 次のサンプルでは、最初に、好みの Java 開発環境でコンソール/シェル アプリケーション用の新しい Maven プロジェクトを作成します。 このクラスは `ErrorNotificationHandler`と呼ばれます。     

    ```java
    import java.util.function.Consumer;
    import com.microsoft.azure.eventprocessorhost.ExceptionReceivedEventArgs;

    public class ErrorNotificationHandler implements Consumer<ExceptionReceivedEventArgs>
    {
        @Override
        public void accept(ExceptionReceivedEventArgs t)
        {
            System.out.println("SAMPLE: Host " + t.getHostname() + " received general error notification during " + t.getAction() + ": " + t.getException().toString());
        }
    }
    ```
2. 次のコードで `EventProcessorSample`という新しいクラスを作成します。 プレースホルダーを、イベント ハブとストレージ アカウントの作成時に使用した値に置き換えます。

   ```java
   package com.microsoft.azure.eventhubs.samples.eventprocessorsample;

   import com.microsoft.azure.eventhubs.ConnectionStringBuilder;
   import com.microsoft.azure.eventhubs.EventData;
   import com.microsoft.azure.eventprocessorhost.CloseReason;
   import com.microsoft.azure.eventprocessorhost.EventProcessorHost;
   import com.microsoft.azure.eventprocessorhost.EventProcessorOptions;
   import com.microsoft.azure.eventprocessorhost.ExceptionReceivedEventArgs;
   import com.microsoft.azure.eventprocessorhost.IEventProcessor;
   import com.microsoft.azure.eventprocessorhost.PartitionContext;

   import java.util.concurrent.ExecutionException;
   import java.util.function.Consumer;

   public class EventProcessorSample
   {
       public static void main(String args[]) throws InterruptedException, ExecutionException
       {
           String consumerGroupName = "$Default";
           String namespaceName = "----NamespaceName----";
           String eventHubName = "----EventHubName----";
           String sasKeyName = "----SharedAccessSignatureKeyName----";
           String sasKey = "----SharedAccessSignatureKey----";
           String storageConnectionString = "----AzureStorageConnectionString----";
           String storageContainerName = "----StorageContainerName----";
           String hostNamePrefix = "----HostNamePrefix----";

           ConnectionStringBuilder eventHubConnectionString = new ConnectionStringBuilder()
                .setNamespaceName(namespaceName)
                .setEventHubName(eventHubName)
                .setSasKeyName(sasKeyName)
                .setSasKey(sasKey);

           EventProcessorHost host = new EventProcessorHost(
                EventProcessorHost.createHostName(hostNamePrefix),
                eventHubName,
                consumerGroupName,
                eventHubConnectionString.toString(),
                storageConnectionString,
                storageContainerName);

           System.out.println("Registering host named " + host.getHostName());
           EventProcessorOptions options = new EventProcessorOptions();
           options.setExceptionNotification(new ErrorNotificationHandler());

           host.registerEventProcessor(EventProcessor.class, options)
           .whenComplete((unused, e) ->
           {
               if (e != null)
               {
                   System.out.println("Failure while registering: " + e.toString());
                   if (e.getCause() != null)
                   {
                       System.out.println("Inner exception: " + e.getCause().toString());
                   }
               }
           })
           .thenAccept((unused) ->
           {
               System.out.println("Press enter to stop.");
                 try 
               {
                   System.in.read();
               }
               catch (Exception e)
               {
                   System.out.println("Keyboard read failed: " + e.toString());
               }
           })
           .thenCompose((unused) ->
            {
                return host.unregisterEventProcessor();
           })
           .exceptionally((e) ->
           {
               System.out.println("Failure while unregistering: " + e.toString());
               if (e.getCause() != null)
               {
                   System.out.println("Inner exception: " + e.getCause().toString());
               }
               return null;
           })
           .get(); // Wait for everything to finish before exiting main!

           System.out.println("End of sample");
       }
   }
   ```
3. 次のコードを使用して、`EventProcessor` という名前のクラスをもう 1 つ作成します。

    ```java
    public static class EventProcessor implements IEventProcessor
    {
        private int checkpointBatchingCount = 0;

        // OnOpen is called when a new event processor instance is created by the host. 
        @Override
        public void onOpen(PartitionContext context) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " is opening");
        }

        // OnClose is called when an event processor instance is being shut down. 
        @Override
        public void onClose(PartitionContext context, CloseReason reason) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " is closing for reason " + reason.toString());
        }

        // onError is called when an error occurs in EventProcessorHost code that is tied to this partition, such as a receiver failure.
        @Override
        public void onError(PartitionContext context, Throwable error)
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " onError: " + error.toString());
        }

        // onEvents is called when events are received on this partition of the Event Hub. 
        @Override
        public void onEvents(PartitionContext context, Iterable<EventData> events) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " got event batch");
            int eventCount = 0;
            for (EventData data : events)
            {
                try
                {
                    System.out.println("SAMPLE (" + context.getPartitionId() + "," + data.getSystemProperties().getOffset() + "," +
                            data.getSystemProperties().getSequenceNumber() + "): " + new String(data.getBytes(), "UTF8"));
                    eventCount++;

                    // Checkpointing persists the current position in the event stream for this partition and means that the next
                    // time any host opens an event processor on this event hub+consumer group+partition combination, it will start
                    // receiving at the event after this one. 
                    this.checkpointBatchingCount++;
                    if ((checkpointBatchingCount % 5) == 0)
                    {
                        System.out.println("SAMPLE: Partition " + context.getPartitionId() + " checkpointing at " +
                               data.getSystemProperties().getOffset() + "," + data.getSystemProperties().getSequenceNumber());
                        // Checkpoints are created asynchronously. It is important to wait for the result of checkpointing
                        // before exiting onEvents or before creating the next checkpoint, to detect errors and to ensure proper ordering.
                        context.checkpoint(data).get();
                    }
                }
                catch (Exception e)
                {
                    System.out.println("Processing failed for an event: " + e.toString());
                }
            }
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " batch size was " + eventCount + " for host " + context.getOwner());
        }
    }
    ```

このチュートリアルでは、EventProcessorHost の単一のインスタンスを使用します。 スループットを向上させるには、EventProcessorHost の複数のインスタンスを (なるべく別のコンピューターで) 実行することをお勧めします。  これにより、冗長性も提供されます。 このような場合、受信したイベントの負荷を分散するために、さまざまなインスタンスが自動的に連携します。 複数の受信側でぞれぞれ *すべて* のイベントを処理する場合、 **ConsumerGroup** 概念を使用する必要があります。 さまざまなコンピューターからイベントを受信する場合、デプロイしたコンピューター (またはロール) に基づいて EventProcessorHost インスタンスの名前を指定するのに便利です。

### <a name="publishing-messages-to-eventhub"></a>メッセージを EventHub に発行する

コンシューマーがメッセージを取得するためには、あらかじめパブリッシャーがそのメッセージをパーティションに発行する必要があります。 com.microsoft.azure.eventhubs.EventHubClient オブジェクトの sendSync() メソッドを使ってイベント ハブに同期的にメッセージを発行するとき、パーティション キーが指定されているかどうかに応じて、そのメッセージは特定のパーティションに送信されるか、または利用可能なすべてのパーティションにラウンド ロビン方式で分散されることに注目してください。

パーティション キーを表す文字列が指定されている場合、そのキーをハッシュすることによって、イベントの送信先となるパーティションが決定されます。

パーティション キーが設定されていない場合は、利用可能なすべてのパーティションに対してラウンド ロビン式にメッセージが分散されます

```java
// Serialize the event into bytes
byte[] payloadBytes = gson.toJson(messagePayload).getBytes(Charset.defaultCharset());

// Use the bytes to construct an {@link EventData} object
EventData sendEvent = EventData.create(payloadBytes);

// Transmits the event to event hub without a partition key
// If a partition key is not set, then we will round-robin to all topic partitions
eventHubClient.sendSync(sendEvent);

//  the partitionKey will be hash'ed to determine the partitionId to send the eventData to.
eventHubClient.sendSync(sendEvent, partitionKey);

```

### <a name="implementing-a-custom-checkpointmanager-for-eventprocessorhost-eph"></a>EventProcessorHost (EPH) 用のカスタム CheckpointManager を実装する

この API には、既定のチェックポイント マネージャーでは実際のユース ケースに対応できない場合を想定し、カスタム チェックポイント マネージャーを実装するメカニズムが用意されています。

既定のチェックポイント マネージャーには、Blob Storage が使用されます。しかし、EPH で使用されているチェックポイント マネージャーを独自の実装でオーバーライドすれば、その独自実装の基盤として任意のストアを使用することができます。

インターフェイス com.microsoft.azure.eventprocessorhost.ICheckpointManager を実装するクラスを作成します

チェックポイント マネージャー (com.microsoft.azure.eventprocessorhost.ICheckpointManager) のカスタム実装を使用します。

既定のチェックポイントのメカニズムを実装内でオーバーライドし、独自のデータ ストア (SQL Server、CosmosDB、Azure Cache for Redis など) に基づいて独自のチェックポイントを実装できます。 チェックポイント マネージャーの実装の基盤として使用するストアには、コンシューマー グループのイベントを処理するすべての EPH インスタンスからアクセスできるストアを使用することをお勧めします。

環境内で利用できるどのデータストアでも使用できます。

com.microsoft.azure.eventprocessorhost.EventProcessorHost クラスには、EventProcessorHost のチェックポイント マネージャーをオーバーライドする際に使用できる 2 つのコンストラクターがあります。

## <a name="next-steps"></a>次のステップ
次の記事を参照してください。 

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Azure Event Hubs の機能と用語](event-hubs-features.md)
- [Event Hubs の FAQ](event-hubs-faq.md)
