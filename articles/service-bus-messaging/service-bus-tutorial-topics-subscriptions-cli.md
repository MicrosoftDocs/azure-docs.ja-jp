---
title: チュートリアル - Azure CLI で公開/サブスクライブ チャネルとトピック フィルターを使用して小売在庫品を更新する | Microsoft Docs
description: このチュートリアルでは、トピックとサブスクリプションからメッセージを送受信する方法と、Azure CLI を使用してフィルター ルールを追加および使用する方法について説明します。
services: service-bus-messaging
author: spelluru
manager: timlt
ms.author: spelluru
ms.date: 09/22/2018
ms.topic: tutorial
ms.service: service-bus-messaging
ms.custom: mvc
ms.openlocfilehash: 60c4d1631717eea602e391387d57afe268efbace
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58075886"
---
# <a name="tutorial-update-inventory-using-cli-and-topicssubscriptions"></a>チュートリアル:CLI とトピック/サブスクリプションを使用して在庫を更新する

Microsoft Azure Service Bus は、アプリケーションとサービスの間で情報を送信するマルチテナント クラウド メッセージング サービスです。 非同期操作により、柔軟なブローカー メッセージング、構造化された先入れ先出し型 (FIFO) のメッセージング、および発行/購読機能が可能になります。 このチュートリアルでは、公開/サブスクライブ チャネルで Azure CLI と Java を使用して、小売在庫シナリオで Service Bus トピックとサブスクリプションを使用する方法について説明します。

このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
> * Azure CLI を使用して、Service Bus トピックとそのトピックへの 1 つ以上のサブスクリプションを作成する
> * Azure CLI を使用してトピック フィルターを追加する
> * 異なる内容の 2 つのメッセージを作成する
> * メッセージを送信し、所定のサブスクリプションに到着したことを確認する
> * サブスクリプションからメッセージを受信する

このシナリオの例は、複数の小売店の在庫品の更新です。 このシナリオでは、各店舗または一連の店舗が、在庫品を更新するためのメッセージを受け取ります。 このチュートリアルでは、サブスクリプションとフィルターを使用してこのシナリオを実装する方法を示します。 まず、3 つのサブスクリプションを持つトピックを作成し、いくつかのルールとフィルターを追加してから、トピックとサブスクリプションからメッセージを送受信します。

![topic](./media/service-bus-tutorial-topics-subscriptions-cli/about-service-bus-topic.png)

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウント][]を作成できます。

## <a name="prerequisites"></a>前提条件

Java で Service Bus アプリを開発するには、以下のものがインストールされている必要があります。

- [Java Development Kit](https://aka.ms/azure-jdks) (最新バージョン)。
- [Azure CLI](https://docs.microsoft.com/cli/azure)
- [Apache Maven](https://maven.apache.org) バージョン 3.0 以降。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、このチュートリアルでは、Azure CLI バージョン 2.0.4 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="service-bus-topics-and-subscriptions"></a>Service Bus トピックとサブスクリプション

[トピックへの各サブスクリプション](service-bus-messaging-overview.md#topics)は、各メッセージのコピーを受信できます。 トピックは完全にプロトコルであり、意味的には Service Bus キューと互換性があります。 Service Bus のトピックは、フィルターの条件を持つさまざまな選択ルールをサポートしています。メッセージのプロパティを設定または変更するオプションのアクションもあります。 ルールが一致するたびに、メッセージが生成されます。 ルール、フィルター、およびアクションの詳細については、こちらの[リンク](topic-filters.md)を参照してください。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

CLI がインストールされたら、コマンド プロンプトを開き、次のコマンドを実行して Azure にサインインします。 Cloud Shell を使用している場合は、これらの手順は必要ありません。

1. Azure CLI をローカルで使用している場合は、次のコマンドを実行して Azure にサインインします。 Cloud Shell でこれらのコマンドを実行している場合、このサインイン手順は不要です。

   ```azurecli-interactive
   az login
   ```

2. 現在のサブスクリプション コンテキストを、使用する Azure サブスクリプションに設定します。

   ```azurecli-interactive
   az account set --subscription Azure_subscription_name
   ```

## <a name="use-cli-to-provision-resources"></a>CLI を使用してリソースをプロビジョニングする

次のコマンドを発行して、Service Bus のリソースをプロビジョニングします。 すべてのプレースホルダーを適切な値に置き換えてください。

```azurecli-interactive
# Create a resource group
az group create --name myResourcegroup --location eastus

# Create a Service Bus messaging namespace with a unique name
namespaceName=myNameSpace$RANDOM
az servicebus namespace create \
   --resource-group myResourceGroup \
   --name $namespaceName \
   --location eastus

# Create a Service Bus topic
az servicebus topic create --resource-group myResourceGroup \
   --namespace-name $namespaceName \
   --name myTopic

# Create subscription 1 to the topic
az servicebus subscription create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --name S1

# Create filter 1 - use custom properties
az servicebus rule create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --subscription-name S1 --name MyFilter --filter-sql-expression "StoreId IN ('Store1','Store2','Store3')"

# Create filter 2 - use custom properties
az servicebus rule create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --subscription-name S1 --name MySecondFilter --filter-sql-expression "StoreId = 'Store4'"

# Create subscription 2
az servicebus subscription create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --name S2

# Create filter 3 - use message header properties via IN list and 
# combine with custom properties.
az servicebus rule create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --subscription-name S2 --name MyFilter --filter-sql-expression "sys.To IN ('Store5','Store6','Store7') OR StoreId = 'Store8'"

# Create subscription 3
az servicebus subscription create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --name S3

# Create filter 4 - Get everything except messages for subscription 1 and 2. 
# Also modify and add an action; in this case set the label to a specified value. 
# Assume those stores might not be part of your main store, so you only add 
# specific items to them. For that, you flag them specifically.
az servicebus rule create --resource-group DemoGroup --namespace-name DemoNamespaceSB --topic-name tutorialtest1
 --subscription-name S3 --name MyFilter --filter-sql-expression "sys.To NOT IN ('Store1','Store2','Store3','Store4','Sto
re5','Store6','Store7','Store8') OR StoreId NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Stor
e8')" --action-sql-expression "SET sys.Label = 'SalesEvent'"

# Get the connection string
connectionString=$(az servicebus namespace authorization-rule keys list \
   --resource-group myResourceGroup \
   --namespace-name  $namespaceName \
   --name RootManageSharedAccessKey \
   --query primaryConnectionString --output tsv)
```

最後のコマンドを実行した後、接続文字列および選択したキュー名をコピーし、メモ帳などの一時的な場所に貼り付けます。 これは、次のステップで必要になります。

## <a name="create-filter-rules-on-subscriptions"></a>サブスクリプションに対してフィルター ルールを作成する

名前空間とトピック/サブスクリプションがプロビジョニングされ、必要な資格情報を持っている場合、サブスクリプションに対してフィルター ルールを作成し、メッセージを送受信できる状態です。 [こちらの GitHub サンプル フォルダー](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/TopicFilters)でコードを調べることができます。

## <a name="send-and-receive-messages"></a>メッセージを送受信する

1. Cloud Shell が開いていて、Bash プロンプトが表示されていることを確認します。

2. 次のコマンドを発行して、[Service Bus の GitHub リポジトリ](https://github.com/Azure/azure-service-bus/)を複製します。

   ```shell
   git clone https://github.com/Azure/azure-service-bus.git
   ```

2. サンプル フォルダー `azure-service-bus/samples/Java/quickstarts-and-tutorials/quickstart-java/tutorial-topics-subscriptions-filters-java` に移動します。 Bash シェルでは、コマンドの大文字と小文字が区別されます。また、パスの区切りはスラッシュにする必要があります。

3. 次のコマンドを発行して、アプリケーションをビルドします。
   
   ```shell
   mvn clean package -DskipTests
   ```
4. プログラムを実行するには、次のコマンドを発行します。 プレースホルダーを、前の手順で取得した接続文字列とトピック名に置き換えます。

   ```shell
   java -jar .\target\tutorial-topics-subscriptions-filters-1.0.0-jar-with-dependencies.jar -c "myConnectionString" -t "myTopicName"
   ```

   トピックに送信され、次に個々のサブスクリプションから受信する 10 個のメッセージを観察します。

   ![プログラムの出力](./media/service-bus-tutorial-topics-subscriptions-cli/service-bus-tutorial-topics-subscriptions-cli.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

次のコマンドを実行して、リソース グループ、名前空間、およびすべての関連リソースを削除します。

```azurecli-interactive
az group delete --resource-group my-resourcegroup
```

## <a name="understand-the-sample-code"></a>サンプル コードを理解する

このセクションでは、サンプル コードの処理内容の詳細について説明します。

### <a name="get-connection-string-and-queue"></a>接続文字列とキューを取得する

コードはまず一連の変数を宣言し、プログラムの残りの処理を実行します。

```java
    public String ConnectionString = null;
    public String TopicName = null;
    static final String[] Subscriptions = {"S1","S2","S3"};
    static final String[] Store = {"Store1","Store2","Store3","Store4","Store5","Store6","Store7","Store8","Store9","Store10"};
    static final String SysField = "sys.To";
    static final String CustomField = "StoreId";
    int NrOfMessagesPerStore = 1; // Send at least 1.
```

接続文字列とトピック名は、コマンドライン パラメーターを介して追加され、`main()` に渡される唯一の値です。 実際のコードの実行は `run()` メソッドでトリガーされます。このコードで、トピックのメッセージを送信し、受信します。

```java
public static void main(String[] args) {
    TutorialTopicsSubscriptionsFilters app = new TutorialTopicsSubscriptionsFilters();
        try {
            app.runApp(args);
            app.run();
        } catch (Exception e) {
            System.out.printf("%s", e.toString());
        }
        System.exit(0);
    }
}

public void run() throws Exception {
    // Send sample messages.
    this.sendMessagesToTopic();

    // Receive messages from subscriptions.
    this.receiveAllMessages();
}
```

### <a name="create-topic-client-to-send-messages"></a>トピック クライアントを作成してメッセージを送信する

メッセージを送受信するために、`sendMessagesToTopic()` メソッドはトピック クライアント インスタンスを作成します。このインスタンスでは、接続文字列とトピック名を使用し、次にメッセージを送信する別のメソッドを呼び出します。

```java
public void sendMessagesToTopic() throws Exception, ServiceBusException {
    // Create client for the topic.
    TopicClient topicClient = new TopicClient(new ConnectionStringBuilder(ConnectionString, TopicName));

    // Create a message sender from the topic client.

    System.out.printf("\nSending orders to topic.\n");

    // Now we can start sending orders.
    CompletableFuture.allOf(
            SendOrders(topicClient,Store[0]),
            SendOrders(topicClient,Store[1]),
            SendOrders(topicClient,Store[2]),
            SendOrders(topicClient,Store[3]),
            SendOrders(topicClient,Store[4]),
            SendOrders(topicClient,Store[5]),
            SendOrders(topicClient,Store[6]),
            SendOrders(topicClient,Store[7]),
            SendOrders(topicClient,Store[8]),
            SendOrders(topicClient,Store[9])
    ).join();

    System.out.printf("\nAll messages sent.\n");
}

    public CompletableFuture<Void> SendOrders(TopicClient topicClient, String store) throws Exception {

        for(int i = 0;i<NrOfMessagesPerStore;i++) {
            Random r = new Random();
            final Item item = new Item(r.nextInt(5),r.nextInt(5),r.nextInt(5));
            IMessage message = new Message(GSON.toJson(item,Item.class).getBytes(UTF_8));
            // We always set the Sent to field
            message.setTo(store);
            final String StoreId = store;
            Double priceToString = item.getPrice();
            final String priceForPut = priceToString.toString();
            message.setProperties(new HashMap<String, String>() {{
                // Additionally we add a customer store field. In reality you would use sys.To or a customer property but not both.
                // This is just for demo purposes.
                put("StoreId", StoreId);
                // Adding more potential filter / rule and action able fields
                put("Price", priceForPut);
                put("Color", item.getColor());
                put("Category", item.getItemCategory());
            }});

            System.out.printf("Sent order to Store %s. Price=%f, Color=%s, Category=%s\n", StoreId, item.getPrice(), item.getColor(), item.getItemCategory());
            topicClient.sendAsync(message);
        }

        return new CompletableFuture().completedFuture(null);
    }
```

### <a name="receive-messages-from-the-individual-subscriptions"></a>個々のサブスクリプションからメッセージを受信する

`receiveAllMessages()` メソッドから `receiveAllMessageFromSubscription()` メソッドが呼び出されます。次に、呼び出しごとにサブスクリプション クライアントが作成され、個々のサブスクリプションからメッセージが受信されます。

```java
public void receiveAllMessages() throws Exception {
    System.out.printf("\nStart Receiving Messages.\n");

    CompletableFuture.allOf(
            receiveAllMessageFromSubscription(Subscriptions[0]),
            receiveAllMessageFromSubscription(Subscriptions[1]),
            receiveAllMessageFromSubscription(Subscriptions[2])
            ).join();
}

public CompletableFuture<Void> receiveAllMessageFromSubscription(String subscription) throws Exception {

    int receivedMessages = 0;

    // Create subscription client.
    IMessageReceiver subscriptionClient = ClientFactory.createMessageReceiverFromConnectionStringBuilder(new ConnectionStringBuilder(ConnectionString, TopicName+"/subscriptions/"+ subscription), ReceiveMode.PEEKLOCK);

    // Create a receiver from the subscription client and receive all messages.
    System.out.printf("\nReceiving messages from subscription %s.\n\n", subscription);

    while (true)
    {
        // This will make the connection wait for N seconds if new messages are available.
        // If no additional messages come we close the connection. This can also be used to realize long polling.
        // In case of long polling you would obviously set it more to e.g. 60 seconds.
        IMessage receivedMessage = subscriptionClient.receive(Duration.ofSeconds(1));
        if (receivedMessage != null)
        {
            if ( receivedMessage.getProperties() != null ) {
                System.out.printf("StoreId=%s\n", receivedMessage.getProperties().get("StoreId"));
                
                // Show the label modified by the rule action
                if(receivedMessage.getLabel() != null)
                    System.out.printf("Label=%s\n", receivedMessage.getLabel());
            }
            
            byte[] body = receivedMessage.getBody();
            Item theItem = GSON.fromJson(new String(body, UTF_8), Item.class);
            System.out.printf("Item data. Price=%f, Color=%s, Category=%s\n", theItem.getPrice(), theItem.getColor(), theItem.getItemCategory());
            
            subscriptionClient.complete(receivedMessage.getLockToken());
            receivedMessages++;
        }
        else
        {
            // No more messages to receive.
            subscriptionClient.close();
            break;
        }
    }
    System.out.printf("\nReceived %s messages from subscription %s.\n", receivedMessages, subscription);
    
    return new CompletableFuture().completedFuture(null);
}
```

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure CLI を使用してリソースをプロビジョニングした後、Service Bus のトピックとそのサブスクリプションからメッセージを送受信しました。 以下の方法について学習しました。

> [!div class="checklist"]
> * Azure Portal を使用して、Service Bus トピックとそのトピックへの 1 つ以上のサブスクリプションを作成する
> * .NET コードを使用してトピック フィルターを追加する
> * 異なる内容の 2 つのメッセージを作成する
> * メッセージを送信し、所定のサブスクリプションに到着したことを確認する
> * サブスクリプションからメッセージを受信する

メッセージの送受信の他の例については、[GitHub の Service Bus サンプル](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted)から始めてください。

Service Bus の公開/サブスクライブ機能の使用方法の詳細については、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [PowerShell とトピック/サブスクリプションを使用して在庫を更新する](service-bus-tutorial-topics-subscriptions-portal.md)

[無料アカウント]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[Install the Azure CLI]: /cli/azure/install-azure-cli
[az group create]: /cli/azure/group#az_group_create
