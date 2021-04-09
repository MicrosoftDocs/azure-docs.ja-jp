---
title: JavaScript で azure/service-bus キューを使用する方法
description: 最新バージョンの @azure/service-bus パッケージを使用して、Service Bus キューとの間でメッセージを送受信する JavaScript プログラムの作成方法について説明します。
author: spelluru
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 11/09/2020
ms.author: spelluru
ms.custom: devx-track-js
ms.openlocfilehash: 3c499dcb5233cbf5cd4048c641d1b38e289cc35f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101739714"
---
# <a name="send-messages-to-and-receive-messages-from-azure-service-bus-queues-javascript"></a>Azure Service Bus キューとの間でメッセージを送受信する (JavaScript)
このチュートリアルでは、JavaScript プログラムの [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) パッケージを使用して、Service Bus キューとの間でメッセージを送受信する方法について説明します。

## <a name="prerequisites"></a>前提条件
- Azure サブスクリプション。 このチュートリアルを完了するには、Azure アカウントが必要です。 [MSDN のサブスクライバー特典](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF)を有効にするか、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)にサインアップしてください。
- 使用するキューがない場合は、「[Azure portal を使用して Service Bus キューを作成する](service-bus-quickstart-portal.md)」の記事にある手順に従って、キューを作成します。 Service Bus 名前空間の **接続文字列** と、作成した **キュー** の名前をメモしておいてください。

> [!NOTE]
> - このチュートリアルでは、コピーして [Nodejs](https://nodejs.org/) を使用して実行できるサンプルを扱います。 Node.js アプリケーションを作成する手順については、[Node.js アプリケーションの作成と Azure Web サイトへのデプロイ](../app-service/quickstart-nodejs.md)、または[Windows PowerShell を使用する Node.js クラウド サービス](../cloud-services/cloud-services-nodejs-develop-deploy-app.md)に関するページをご覧ください。

### <a name="use-node-package-manager-npm-to-install-the-package"></a>ノード パッケージ マネージャー (NPM) を使用してパッケージをインストールする
Service Bus の npm パッケージをインストールするには、パスに `npm` を設定したコマンド プロンプトを開き、サンプルを格納するフォルダーにディレクトリを変更してから、このコマンドを実行します。

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-queue"></a>メッセージをキューに送信する
次のサンプル コードは、キューにメッセージを送信する方法を示しています。

1. [Visual Studio Code](https://code.visualstudio.com/) など、お好みのエディターを開きます。
2. `send.js` というファイルを作成し、そこに以下のコードを貼り付けます。 このコードによって、キューにメッセージが送信されます。 メッセージには、ラベル (Scientist) と本文 (Einstein) が含まれています。

    ```javascript
    const { ServiceBusClient } = require("@azure/service-bus");
    
    // connection string to your Service Bus namespace
    const connectionString = "<CONNECTION STRING TO SERVICE BUS NAMESPACE>"

    // name of the queue
    const queueName = "<QUEUE NAME>"
    
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
     
        // createSender() can also be used to create a sender for a topic.
        const sender = sbClient.createSender(queueName);
     
        try {
            // Tries to send all messages in a single batch.
            // Will fail if the messages cannot fit in a batch.
            // await sender.sendMessages(messages);
     
            // create a batch object
            let batch = await sender.createMessageBatch(); 
            for (let i = 0; i < messages.length; i++) {
                // for each message in the array            
    
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
    
            // Send the last created batch of messages to the queue
            await sender.sendMessages(batch);
     
            console.log(`Sent a batch of messages to the queue: ${queueName}`);
                    
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
3. `<CONNECTION STRING TO SERVICE BUS NAMESPACE>` を Service Bus 名前空間の接続文字列に置き換えます。
1. `<QUEUE NAME>` をキューの名前に置き換えます。 
1. その後、コマンド プロンプトで次のコマンドを実行して、このファイルを実行します。

    ```console
    node send.js 
    ```
1. 次の出力が表示されます。

    ```console
    Sent a batch of messages to the queue: myqueue
    ```

## <a name="receive-messages-from-a-queue"></a>キューからメッセージを受信する

1. [Visual Studio Code](https://code.visualstudio.com/) など、お好みのエディターを開きます
2. `receive.js` というファイルを作成し、そこに次のコードを貼り付けます。

    ```javascript
    const { delay, ServiceBusClient, ServiceBusMessage } = require("@azure/service-bus");

    // connection string to your Service Bus namespace
    const connectionString = "<CONNECTION STRING TO SERVICE BUS NAMESPACE>"

    // name of the queue
    const queueName = "<QUEUE NAME>"

     async function main() {
        // create a Service Bus client using the connection string to the Service Bus namespace
        const sbClient = new ServiceBusClient(connectionString);
     
        // createReceiver() can also be used to create a receiver for a subscription.
        const receiver = sbClient.createReceiver(queueName);
    
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
3. `<CONNECTION STRING TO SERVICE BUS NAMESPACE>` を Service Bus 名前空間の接続文字列に置き換えます。
1. `<QUEUE NAME>` をキューの名前に置き換えます。 
1. その後、コマンド プロンプトで次のコマンドを実行して、このファイルを実行します。

    ```console
    node receive.js
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

Azure portal の Service Bus 名前空間の **[概要]** ページで、**受信** メッセージ数と **送信** メッセージ数を確認できます。 最新の値を表示するには、1 分程度待ってから、ページを最新の情報に更新しなければならないことがあります。 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png" alt-text="受信メッセージ数と送信メッセージ数":::

この **[概要]** ページでキューを選択して、 **[Service Bus キュー]** ページに移動します。 このページにも、**受信** メッセージ数と **送信** メッセージ数が表示されます。 加えて、キューの **現在のサイズ**、**最大サイズ**、**アクティブなメッセージ数** など、他の情報も表示されます。 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/queue-details.png" alt-text="キューの詳細":::
## <a name="next-steps"></a>次のステップ
次のドキュメントおよびサンプルを参照してください。 

- [JavaScript 用の Azure Service Bus クライアント ライブラリ](https://www.npmjs.com/package/@azure/service-bus)
- [JavaScript のサンプル](/samples/azure/azure-sdk-for-js/service-bus-javascript/)
- [TypeScript のサンプル](/samples/azure/azure-sdk-for-js/service-bus-typescript/)
- [API リファレンス ドキュメント](/javascript/api/overview/azure/service-bus)