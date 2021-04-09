---
title: トピックとサブスクリプションにプレビューの JavaScript azure/service-bus を使用する
description: 最新のプレビュー バージョンの @azure/service-bus パッケージを使用して Service Bus トピックにメッセージを送信したり、トピックのサブスクリプションからメッセージを受信したりする JavaScript プログラムの作成方法について説明します。
author: spelluru
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 11/09/2020
ms.author: spelluru
ms.custom: devx-track-js
ms.openlocfilehash: a1afe4207ce3833f3bcb55bc7bc2e8e27f393f63
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98179998"
---
# <a name="quickstart-service-bus-topics-and-subscriptions-with-nodejs-and-the-preview-azureservice-bus-package"></a>クイックスタート: Service Bus のトピックとサブスクリプションを Node.js およびプレビューの azure/service-bus パッケージで使用する
このチュートリアルでは、JavaScript プログラムの [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) パッケージを使用して、Service Bus トピックにメッセージを送信したり、そのトピックの Service Bus サブスクリプションからメッセージを受信したりする方法について説明します。

## <a name="prerequisites"></a>前提条件
- Azure サブスクリプション。 このチュートリアルを完了するには、Azure アカウントが必要です。 [MSDN のサブスクライバー特典](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF)を有効にするか、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)にサインアップしてください。
- 「[Quickstart:Azure portal を使用して Service Bus トピックとそのサブスクリプションを作成する](service-bus-quickstart-topics-subscriptions-portal.md)」の手順に従ってください。 接続文字列、トピック名、サブスクリプション名を書き留めておきます。 このクイックスタートで使用するサブスクリプションは 1 つだけです。 

> [!NOTE]
> - このチュートリアルでは、コピーして [Nodejs](https://nodejs.org/) を使用して実行できるサンプルを扱います。 Node.js アプリケーションを作成する手順については、[Node.js アプリケーションの作成と Azure Web サイトへのデプロイ](../app-service/quickstart-nodejs.md)に関するページ、または [Windows PowerShell を使用する Node.js クラウド サービス](../cloud-services/cloud-services-nodejs-develop-deploy-app.md)に関するページを参照してください。

### <a name="use-node-package-manager-npm-to-install-the-package"></a>ノード パッケージ マネージャー (NPM) を使用してパッケージをインストールする
Service Bus の npm パッケージをインストールするには、パスに `npm` を設定したコマンド プロンプトを開き、サンプルを格納するフォルダーにディレクトリを変更してから、このコマンドを実行します。

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-topic"></a>メッセージをトピックに送信する
次のサンプル コードは、メッセージのバッチを Service Bus トピックに送信する方法を示しています。 詳細については、コードのコメントを参照してください。 

1. [Visual Studio Code](https://code.visualstudio.com/) など、お好みのエディターを開きます
2. `sendtotopic.js` というファイルを作成し、そこに以下のコードを貼り付けます。 このコードによって、トピックにメッセージが送信されます。

    ```javascript
    const { ServiceBusClient } = require("@azure/service-bus");
    
    const connectionString = "<SERVICE BUS NAMESPACE CONNECTION STRING>"
    const topicName = "<TOPIC NAME>";
    
    const messages = [
        { body: "Albert Einstein" },
        { body: "Werner Heisenberg" },
        { body: "Marie Curie" },
        { body: "Steven Hawking" },
        { body: "Isaac Newton" },
        { body: "Niels Bohr" },
        { body: "Michael Faraday" },
        { body: "Galileo Galilei" },
        { body: "Johannes Kepler" },
        { body: "Nikolaus Kopernikus" }
     ];
    
     async function main() {
        // create a Service Bus client using the connection string to the Service Bus namespace
        const sbClient = new ServiceBusClient(connectionString);
     
        // createSender() can also be used to create a sender for a queue.
        const sender = sbClient.createSender(topicName);
     
        try {
            // Tries to send all messages in a single batch.
            // Will fail if the messages cannot fit in a batch.
            // await sender.sendMessages(messages);
     
            // create a batch object
            let batch = await sender.createMessageBatch(); 
            for (let i = 0; i < messages.length; i++) {
                // for each message in the arry         
    
                // try to add the message to the batch
                if (!batch.tryAddMessage(messages[i])) {            
                    // if it fails to add the message to the current batch
                    // send the current batch as it is full
                    await sender.sendMessages(batch);
    
                    // then, create a new batch 
                    batch = await sender.createMessageBatch();
     
                    // now, add the message failed to be added to the previous batch to this batch
                    if (!batch.tryAddMessage(messages[i])) {
                        // if it still can't be added to the batch, the message is probably too big to fit in a batch
                        throw new Error("Message too big to fit in a batch");
                    }
                }
            }
    
            // Send the last created batch of messages to the topic
            await sender.sendMessages(batch);
     
            console.log(`Sent a batch of messages to the topic: ${topicName}`);
                    
            // Close the sender
            await sender.close();
        } finally {
            await sbClient.close();
        }
    }
    
    // call the main function
    main().catch((err) => {
        console.log("Error occurred: ", err);
        process.exit(1);
     });    
    ```
3. `<SERVICE BUS NAMESPACE CONNECTION STRING>` を Service Bus 名前空間の接続文字列に置き換えます。
1. `<TOPIC NAME>` を対象のトピックの名前に置き換えます。 
1. その後、コマンド プロンプトで次のコマンドを実行して、このファイルを実行します。

    ```console
    node sendtotopic.js 
    ```
1. 次の出力が表示されます。

    ```console
    Sent a batch of messages to the topic: mytopic
    ```

## <a name="receive-messages-from-a-subscription"></a>サブスクリプションからメッセージを受信する
1. [Visual Studio Code](https://code.visualstudio.com/) など、お好みのエディターを開きます
2. **receivefromsubscription.js** というファイルを作成し、そこに次のコードを貼り付けます。 詳細については、コードのコメントを参照してください。 

    ```javascript
    const { delay, ServiceBusClient, ServiceBusMessage } = require("@azure/service-bus");
    
    const connectionString = "<SERVICE BUS NAMESPACE CONNECTION STRING>"
    const topicName = "<TOPIC NAME>";
    const subscriptionName = "<SUBSCRIPTION NAME>";
    
     async function main() {
        // create a Service Bus client using the connection string to the Service Bus namespace
        const sbClient = new ServiceBusClient(connectionString);
     
        // createReceiver() can also be used to create a receiver for a queue.
        const receiver = sbClient.createReceiver(topicName, subscriptionName);
    
        // function to handle messages
        const myMessageHandler = async (messageReceived) => {
            console.log(`Received message: ${messageReceived.body}`);
        };
    
        // function to handle any errors
        const myErrorHandler = async (error) => {
            console.log(error);
        };
    
        // subscribe and specify the message and error handlers
        receiver.subscribe({
            processMessage: myMessageHandler,
            processError: myErrorHandler
        });
    
        // Waiting long enough before closing the sender to send messages
        await delay(5000);
    
        await receiver.close(); 
        await sbClient.close();
    }
    
    // call the main function
    main().catch((err) => {
        console.log("Error occurred: ", err);
        process.exit(1);
     });    
    ```
3. `<SERVICE BUS NAMESPACE CONNECTION STRING>` を名前空間の接続文字列に置き換えます。 
1. `<TOPIC NAME>` を対象のトピックの名前に置き換えます。 
1. `<SUBSCRIPTION NAME>` をトピックのサブスクリプションの名前に置き換えます。 
1. その後、コマンド プロンプトで次のコマンドを実行して、このファイルを実行します。

    ```console
    node receivefromsubscription.js
    ```
1. 次の出力が表示されます。

    ```console
    Received message: Albert Einstein
    Received message: Werner Heisenberg
    Received message: Marie Curie
    Received message: Steven Hawking
    Received message: Isaac Newton
    Received message: Niels Bohr
    Received message: Michael Faraday
    Received message: Galileo Galilei
    Received message: Johannes Kepler
    Received message: Nikolaus Kopernikus
    ```

Azure portal で Service Bus 名前空間に移動し、下のペインでトピックを選択して、トピックの **[Service Bus トピック]** ページを表示します。 このページの **[メッセージ]** グラフに 3 つの受信メッセージと 3 つの送信メッセージが確認できると思います。 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/topic-page-portal.png" alt-text="受信メッセージと送信メッセージ":::

次回、 **[Service Bus トピック]** ページでアプリの送信のみを実行した場合、6 つの受信メッセージ (うち 3 つは新規) が表示されますが、送信メッセージは 3 つと表示されます。 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/updated-topic-page.png" alt-text="更新後のトピック ページ":::

このページでいずれかのサブスクリプションを選択すると、その **[Service Bus Subscription]\(Service Bus サブスクリプション\)** ページが表示されます。 このページで、アクティブなメッセージ数や配信不能メッセージ数を確認できます。 この例では、まだ受信者が受け取っていないアクティブなメッセージが 3 つ存在します。 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/active-message-count.png" alt-text="アクティブなメッセージ数":::

## <a name="next-steps"></a>次のステップ
次のドキュメントおよびサンプルを参照してください。 

- [Python 用の Azure Service Bus クライアント ライブラリ](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/servicebus/service-bus/README.md)
- [サンプル](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples)。 JavaScript のサンプルは **javascript** フォルダーに、TypeScript のサンプルは **typescript** フォルダーに格納されています。 
- [azure-servicebus のリファレンス ドキュメント](/javascript/api/overview/azure/service-bus)
