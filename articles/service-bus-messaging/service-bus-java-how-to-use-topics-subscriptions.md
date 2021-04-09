---
title: Java で Azure Service Bus のトピックとサブスクリプションを使用する (azure-messaging-servicebus)
description: このクイックスタートでは、Azure Service Bus トピックにメッセージを送信してそのトピックのサブスクリプションからメッセージを受信する Java コードを、azure-messaging-servicebus パッケージを使用して作成します。
ms.devlang: Java
ms.topic: quickstart
ms.date: 02/13/2021
ms.openlocfilehash: c5b930fb2c87a09a1f4801365936c62a7cf79f1d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100516177"
---
# <a name="send-messages-to-an-azure-service-bus-topic-and-receive-messages-from-subscriptions-to-the-topic-java"></a>Azure Service Bus トピックへのメッセージ送信とトピックのサブスクリプションからのメッセージ受信 (Java)
このクイックスタートでは、Azure Service Bus トピックにメッセージを送信してそのトピックのサブスクリプションからメッセージを受信する Java コードを、azure-messaging-servicebus パッケージを使用して作成します。

> [!IMPORTANT]
> このクイックスタートでは、新しい azure-messaging-servicebus パッケージを使用します。 古い azure-servicebus パッケージを使用したクイックスタートについては、[azure-servicebus を使用したメッセージの送受信](service-bus-java-how-to-use-topics-subscriptions-legacy.md)に関する記事をご覧ください。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション。 このチュートリアルを完了するには、Azure アカウントが必要です。 [Visual Studio または MSDN のサブスクライバー特典](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF)を有効にするか、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)にサインアップしてください。
- 「[Quickstart:Azure portal を使用して Service Bus トピックとそのサブスクリプションを作成する](service-bus-quickstart-topics-subscriptions-portal.md)」の手順に従ってください。 接続文字列、トピック名、サブスクリプション名を書き留めておきます。 このクイックスタートで使用するサブスクリプションは 1 つだけです。 
- [Azure SDK for Java][Azure SDK for Java] をインストールします。 Eclipse を使用している場合は、Azure SDK for Java が含まれている [Azure Toolkit for Eclipse][Azure Toolkit for Eclipse] をインストールできます。 これで **Microsoft Azure Libraries for Java** をプロジェクトに追加できます。 IntelliJ を使用している場合は、[Azure Toolkit for IntelliJ のインストール](/azure/developer/java/toolkit-for-intellij/installation)に関するページを参照してください。 


## <a name="send-messages-to-a-topic"></a>メッセージをトピックに送信する
このセクションでは、Java コンソール プロジェクトを作成し、既に作成してあるトピックにメッセージを送信するコードを追加します。 

### <a name="create-a-java-console-project"></a>Java コンソール プロジェクトを作成する
Eclipse または任意のツールを使用して Java プロジェクトを作成します。 

### <a name="configure-your-application-to-use-service-bus"></a>Service Bus を使用するようにアプリケーションを構成する
Azure Core ライブラリおよび Azure Service Bus ライブラリへの参照を追加します。 

Eclipse を使用して Java コンソール アプリケーションを作成した場合は、Java プロジェクトを Maven に変換してください。 **[Package Explorer]\(パッケージ エクスプローラー\)** ウィンドウでプロジェクトを右クリックし、 **[Configure]\(構成\)**  ->  **[Convert to Maven project]\(Maven プロジェクトに変換\)** を選択します。 その後、これら 2 つのライブラリへの依存関係を追加します。その例を次に示します。

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>org.myorg.sbusquickstarts</groupId>
    <artifactId>sbustopicqs</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <build>
        <sourceDirectory>src</sourceDirectory>
        <plugins>
            <plugin>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.8.1</version>
                <configuration>
                    <release>15</release>
                </configuration>
            </plugin>
        </plugins>
    </build>
    <dependencies>
        <dependency>
            <groupId>com.azure</groupId>
            <artifactId>azure-core</artifactId>
            <version>1.13.0</version>
        </dependency>
        <dependency>
            <groupId>com.azure</groupId>
            <artifactId>azure-messaging-servicebus</artifactId>
            <version>7.0.2</version>
        </dependency>
    </dependencies>
</project>
```

### <a name="add-code-to-send-messages-to-the-topic"></a>トピックにメッセージを送信するためのコードを追加する
1. 次の `import` ステートメントを Java ファイルの冒頭に追加します。 

    ```java
    import com.azure.messaging.servicebus.*;
    
    import java.util.concurrent.CountDownLatch;
    import java.util.concurrent.TimeUnit;
    import java.util.Arrays;
    import java.util.List;
    ```    
5. このクラスに、接続文字列とトピック名を保持する変数を次のように定義します。 

    ```java
    static String connectionString = "<NAMESPACE CONNECTION STRING>";
    static String topicName = "<TOPIC NAME>";    
    static String subName = "<SUBSCRIPTION NAME>";
    ```

    `<NAMESPACE CONNECTION STRING>` を Service Bus 名前空間の接続文字列に置き換えます。 さらに、`<TOPIC NAME>` を対象のトピックの名前に置き換えます。
3. 1 つのメッセージをトピックに送信するためのメソッドを、`sendMessage` という名前でこのクラスに追加します。 

    ```java
    static void sendMessage()
    {
        // create a Service Bus Sender client for the queue 
        ServiceBusSenderClient senderClient = new ServiceBusClientBuilder()
                .connectionString(connectionString)
                .sender()
                .topicName(topicName)
                .buildClient();
        
        // send one message to the topic
        senderClient.sendMessage(new ServiceBusMessage("Hello, World!"));
        System.out.println("Sent a single message to the topic: " + topicName);        
    }
    ```
1. 一連のメッセージを作成するためのメソッドを、`createMessages` という名前でこのクラスに追加します。 通常、これらのメッセージはアプリケーションのさまざまな部分から届きます。 ここでは、次のように一連のサンプル メッセージを作成します。

    ```java
    static List<ServiceBusMessage> createMessages()
    {
        // create a list of messages and return it to the caller
        ServiceBusMessage[] messages = {
                new ServiceBusMessage("First message"),
                new ServiceBusMessage("Second message"),
                new ServiceBusMessage("Third message")
        };
        return Arrays.asList(messages);
    }
    ```
1. 作成したトピックにメッセージを送信するためのメソッドを、`sendMessageBatch` という名前で追加します。 このメソッドは、トピックの `ServiceBusSenderClient` を作成し、`createMessages` メソッドを呼び出して一連のメッセージを取得した後、1 つまたは複数のバッチを用意して、そのバッチをトピックに送信します。 

    ```java
    static void sendMessageBatch()
    {
        // create a Service Bus Sender client for the topic 
        ServiceBusSenderClient senderClient = new ServiceBusClientBuilder()
                .connectionString(connectionString)
                .sender()
                .topicName(topicName)
                .buildClient();

        // Creates an ServiceBusMessageBatch where the ServiceBus.
        ServiceBusMessageBatch messageBatch = senderClient.createMessageBatch();        
        
        // create a list of messages
        List<ServiceBusMessage> listOfMessages = createMessages();
        
        // We try to add as many messages as a batch can fit based on the maximum size and send to Service Bus when
        // the batch can hold no more messages. Create a new batch for next set of messages and repeat until all
        // messages are sent.        
        for (ServiceBusMessage message : listOfMessages) {
            if (messageBatch.tryAddMessage(message)) {
                continue;
            }

            // The batch is full, so we create a new batch and send the batch.
            senderClient.sendMessages(messageBatch);
            System.out.println("Sent a batch of messages to the topic: " + topicName);
            
            // create a new batch
            messageBatch = senderClient.createMessageBatch();

            // Add that message that we couldn't before.
            if (!messageBatch.tryAddMessage(message)) {
                System.err.printf("Message is too large for an empty batch. Skipping. Max size: %s.", messageBatch.getMaxSizeInBytes());
            }
        }

        if (messageBatch.getCount() > 0) {
            senderClient.sendMessages(messageBatch);
            System.out.println("Sent a batch of messages to the topic: " + topicName);
        }

        //close the client
        senderClient.close();
    }
    ```

## <a name="receive-messages-from-a-subscription"></a>サブスクリプションからメッセージを受信する
このセクションでは、トピックのサブスクリプションからメッセージを取得するコードを追加します。 

1. サブスクリプションからメッセージを受信するメソッドを、`receiveMessages` という名前で追加します。 このメソッドは、メッセージを処理するためのハンドラーとエラーを処理するためのハンドラーを指定してサブスクリプションの `ServiceBusProcessorClient` を作成します。 次に、プロセッサを起動して数秒間待機し、受信したメッセージを出力した後、プロセッサを停止して終了します。

    > [!IMPORTANT]
    > コードに出現する `ServiceBusTopicTest::processMessage` の `ServiceBusTopicTest` の部分は、実際のクラスの名前に置き換えてください。 

    ```java
    // handles received messages
    static void receiveMessages() throws InterruptedException
    {
        CountDownLatch countdownLatch = new CountDownLatch(1);

        // Create an instance of the processor through the ServiceBusClientBuilder
        ServiceBusProcessorClient processorClient = new ServiceBusClientBuilder()
            .connectionString(connectionString)
            .processor()
            .topicName(topicName)
            .subscriptionName(subName)
            .processMessage(ServiceBusTopicTest::processMessage)
            .processError(context -> processError(context, countdownLatch))
            .buildProcessorClient();

        System.out.println("Starting the processor");
        processorClient.start();

        TimeUnit.SECONDS.sleep(10);
        System.out.println("Stopping and closing the processor");
        processorClient.close();        
    }  
    ```
2. Service Bus サブスクリプションから受信したメッセージを処理するための `processMessage` メソッドを追加します。 

    ```java
    private static void processMessage(ServiceBusReceivedMessageContext context) {
        ServiceBusReceivedMessage message = context.getMessage();
        System.out.printf("Processing message. Session: %s, Sequence #: %s. Contents: %s%n", message.getMessageId(),
            message.getSequenceNumber(), message.getBody());
    }    
    ```
3. エラー メッセージを処理するための `processError` メソッドを追加します。

    ```java
    private static void processError(ServiceBusErrorContext context, CountDownLatch countdownLatch) {
        System.out.printf("Error when receiving messages from namespace: '%s'. Entity: '%s'%n",
            context.getFullyQualifiedNamespace(), context.getEntityPath());

        if (!(context.getException() instanceof ServiceBusException)) {
            System.out.printf("Non-ServiceBusException occurred: %s%n", context.getException());
            return;
        }

        ServiceBusException exception = (ServiceBusException) context.getException();
        ServiceBusFailureReason reason = exception.getReason();

        if (reason == ServiceBusFailureReason.MESSAGING_ENTITY_DISABLED
            || reason == ServiceBusFailureReason.MESSAGING_ENTITY_NOT_FOUND
            || reason == ServiceBusFailureReason.UNAUTHORIZED) {
            System.out.printf("An unrecoverable error occurred. Stopping processing with reason %s: %s%n",
                reason, exception.getMessage());

            countdownLatch.countDown();
        } else if (reason == ServiceBusFailureReason.MESSAGE_LOCK_LOST) {
            System.out.printf("Message lock lost for message: %s%n", context.getException());
        } else if (reason == ServiceBusFailureReason.SERVICE_BUSY) {
            try {
                // Choosing an arbitrary amount of time to wait until trying again.
                TimeUnit.SECONDS.sleep(1);
            } catch (InterruptedException e) {
                System.err.println("Unable to sleep for period of time");
            }
        } else {
            System.out.printf("Error source %s, reason %s, message: %s%n", context.getErrorSource(),
                reason, context.getException());
        }
    }  
    ```
1. `sendMessage`、`sendMessageBatch`、`receiveMessages` の各メソッドを呼び出し、`InterruptedException` をスローするように `main` メソッドを更新します。     

    ```java
    public static void main(String[] args) throws InterruptedException {        
        sendMessage();
        sendMessageBatch();
        receiveMessages();
    }   
    ```

## <a name="run-the-app"></a>アプリを実行する
プログラムを実行すると、次の出力に似た出力が表示されます。

```console
Sent a single message to the topic: mytopic
Sent a batch of messages to the topic: mytopic
Starting the processor
Processing message. Session: e0102f5fbaf646988a2f4b65f7d32385, Sequence #: 1. Contents: Hello, World!
Processing message. Session: 3e991e232ca248f2bc332caa8034bed9, Sequence #: 2. Contents: First message
Processing message. Session: 56d3a9ea7df446f8a2944ee72cca4ea0, Sequence #: 3. Contents: Second message
Processing message. Session: 7bd3bd3e966a40ebbc9b29b082da14bb, Sequence #: 4. Contents: Third message
```

Azure portal の Service Bus 名前空間の **[概要]** ページで、**受信** メッセージ数と **送信** メッセージ数を確認できます。 最新の値を表示するには、1 分程度待ってから、ページを最新の情報に更新しなければならないことがあります。 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png" alt-text="受信メッセージ数と送信メッセージ数" lightbox="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png":::

下部中央のペインの **[トピック]** タブに切り替えてトピックを選択すると、そのトピックの **[Service Bus トピック]** ページが表示されます。 このページの **[メッセージ]** グラフに 4 つの受信メッセージと 4 つの送信メッセージが確認できると思います。 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/topic-page-portal.png" alt-text="受信メッセージと送信メッセージ" lightbox="./media/service-bus-java-how-to-use-topics-subscriptions/topic-page-portal.png":::

`main` メソッドの `receiveMessages` 呼び出しをコメント アウトして再度アプリを実行した場合、 **[Service Bus トピック]** ページには、8 つの受信メッセージ (うち 4 つは新規) が表示されますが、送信メッセージは 4 つと表示されます。 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/updated-topic-page.png" alt-text="更新後のトピック ページ" lightbox="./media/service-bus-java-how-to-use-topics-subscriptions/updated-topic-page.png":::

このページでいずれかのサブスクリプションを選択すると、その **[Service Bus Subscription]\(Service Bus サブスクリプション\)** ページが表示されます。 このページで、アクティブなメッセージ数や配信不能メッセージ数を確認できます。 この例では、まだ受信者が受け取っていないアクティブなメッセージが 4 つ存在します。 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/active-message-count.png" alt-text="アクティブなメッセージ数" lightbox="./media/service-bus-java-how-to-use-topics-subscriptions/active-message-count.png":::

## <a name="next-steps"></a>次のステップ
次のドキュメントおよびサンプルを参照してください。

- [Java 用の Azure Service Bus クライアント ライブラリ - Readme](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/servicebus/azure-messaging-servicebus/README.md)
- [GitHub のサンプル](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Java API リファレンス](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-messaging-servicebus/7.0.0/index.html)


[Azure SDK for Java]: /java/api/overview/azure/
[Azure Toolkit for Eclipse]: /azure/developer/java/toolkit-for-eclipse/installation
[Service Bus queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter]: /dotnet/api/microsoft.azure.servicebus.sqlfilter
[SqlFilter.SqlExpression]: /dotnet/api/microsoft.azure.servicebus.sqlfilter.sqlexpression
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage