---
title: Node.js と azure/service-bus での Azure Service Bus キューの使用方法 | Microsoft Docs
description: Node.js アプリから Azure の Service Bus キューを使用する方法を学習します。
services: service-bus-messaging
documentationcenter: nodejs
author: axisc
manager: timlt
editor: spelluru
ms.assetid: a87a00f9-9aba-4c49-a0df-f900a8b67b3f
ms.service: service-bus-messaging
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 04/10/2019
ms.author: aschhab
ms.openlocfilehash: 7aacefde9c037fcce64d9256e35082eb04e0a2f3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "65988349"
---
# <a name="how-to-use-service-bus-queues-with-nodejs-and-the-azureservice-bus-package"></a>Node.js および azure/service-bus パッケージで Service Bus キューを 使用する方法
> [!div class="op_multi_selector" title1="プログラミング言語" title2="Node.js パッケージ"]
> - [(Node.js | azure-sb)](service-bus-nodejs-how-to-use-queues.md)
> - [(Node.js | @azure/service-bus)](service-bus-nodejs-how-to-use-queues-new-package.md)

このチュートリアルでは、新しい [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) パッケージを使用して Service Bus キューとの間でメッセージを送受信する Node.js プログラムの記述方法を学習します。 このパッケージではより高速な [AMQP 1.0 プロトコル](service-bus-amqp-overview.md)が使用されます。一方、以前の [azure-sb](https://www.npmjs.com/package/azure-sb) パッケージでは [Service Bus REST ランタイム API](/rest/api/servicebus/service-bus-runtime-rest) が使用されていました。 サンプルは JavaScript で記述されています。

## <a name="prerequisites"></a>前提条件
- Azure サブスクリプション。 このチュートリアルを完了するには、Azure アカウントが必要です。 [MSDN のサブスクライバー特典](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF)を有効にするか、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)にサインアップしてください。
- 使用するキューがない場合は、「[Azure portal を使用して Service Bus キューを作成する](service-bus-quickstart-portal.md)」の記事にある手順に従って、キューを作成します。 Service Bus インスタンスの接続文字列と、作成したキューの名前をメモしておいてください。 サンプルでこれらの値を使用します。

> [!NOTE]
> - このチュートリアルでは、コピーして [Nodejs](https://nodejs.org/) を使用して実行できるサンプルを扱います。 Node.js アプリケーションを作成する手順については、[Node.js アプリケーションの作成と Azure Web サイトへのデプロイ](../app-service/app-service-web-get-started-nodejs.md)、または[Windows PowerShell を使用する Node.js クラウド サービス](../cloud-services/cloud-services-nodejs-develop-deploy-app.md)に関するページをご覧ください。
> - 新しい [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) パッケージでは、キューの作成がまだサポートされていません。 ブログラムでそれらを作成する場合は、[@azure/arm-servicebus](https://www.npmjs.com/package/@azure/arm-servicebus) パッケージを使用してください。

### <a name="use-node-package-manager-npm-to-install-the-package"></a>ノード パッケージ マネージャー (NPM) を使用してパッケージをインストールする
Service Bus の npm パッケージをインストールするには、パスに `npm` を設定したコマンド プロンプトを開き、サンプルを格納するフォルダーにディレクトリを変更してから、このコマンドを実行します。

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-queue"></a>メッセージをキューに送信する
Service Bus キューとのやりとりは、[ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) クラスをインスタンス化し、それを使用して [QueueClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient) クラスをインスタンス化することから始まります。 キュー クライアントを取得したら、送信側を作成し、その上で [send](https://docs.microsoft.com/javascript/api/%40azure/service-bus/sender#send-sendablemessageinfo-) または [sendBatch](https://docs.microsoft.com/javascript/api/@azure/service-bus/sender#sendbatch-sendablemessageinfo---) メソッドを使用してメッセージを送信できます。

1. [Visual Studio Code](https://code.visualstudio.com/) など、お好みのエディターを開きます
2. `send.js` というファイルを作成し、そこに以下のコードを貼り付けます。 このコードでは、キューに 10 件のメッセージが送信されます。

    ```javascript
    const { ServiceBusClient } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const queueName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const queueClient = sbClient.createQueueClient(queueName);
      const sender = queueClient.createSender();
      
      try {
        for (let i = 0; i < 10; i++) {
          const message= {
            body: `Hello world! ${i}`,
            label: `test`,
            userProperties: {
                myCustomPropertyName: `my custom property value ${i}`
           }
          };
          console.log(`Sending message: ${message.body}`);
          await sender.send(message);
        }
        
        await queueClient.close();
      } finally {
        await sbClient.close();
      }
    }
    
    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. 上記のコードで、接続文字列と、キューの名前を入力します。
4. その後、コマンド プロンプトで `node send.js` コマンドを実行して、このファイルを実行します。

お疲れさまでした。 これで、Service Bus キューにメッセージが送信されました。

メッセージには、送信時に設定できる、`label` や `messageId` のような標準プロパティがいくつかあります。 カスタム プロパティを設定する場合は、`userProperties` を使用します。これは、カスタム データのキーと値のペアを保持できる json オブジェクトです。

Service Bus キューでサポートされているメッセージの最大サイズは、[Standard レベル](service-bus-premium-messaging.md)では 256 KB、[Premium レベル](service-bus-premium-messaging.md)では 1 MB です。 1 つのキューで保持されるメッセージ数には上限がありませんが、1 つのキューで保持できるメッセージの合計サイズには上限があります。 このキュー サイズは作成時に定義され、上限は 5 GB です。 クォータの詳細については、「[Service Bus のクォータ](service-bus-quotas.md)」を参照してください。

## <a name="receive-messages-from-a-queue"></a>キューからメッセージを受信する
Service Bus キューとのやりとりは、[ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) クラスをインスタンス化し、それを使用して [QueueClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient) クラスをインスタンス化することから始まります。 キュー クライアントを取得したら、受信側を作成し、[receiveMessages](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#receivemessages-number--undefined---number-) または [registerMessageHandler](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#registermessagehandler-onmessage--onerror--messagehandleroptions-) メソッドを使用して、メッセージを受信できます。

1. [Visual Studio Code](https://code.visualstudio.com/) など、お好みのエディターを開きます
2. `recieve.js` というファイルを作成し、そこに以下のコードを貼り付けます。 このコードでは、キューから 10 件のメッセージの受信を試みます。 受信する実際の数は、キュー内のメッセージの数とネットワーク待機時間によって異なります。

    ```javascript
    const { ServiceBusClient, ReceiveMode } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const queueName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const queueClient = sbClient.createQueueClient(queueName);
      const receiver = queueClient.createReceiver(ReceiveMode.ReceiveAndDelete);
      try {
        const messages = await receiver.receiveMessages(10)
        console.log("Received messages:");
        console.log(messages.map(message => message.body));
         
        await queueClient.close();
      } finally {
        await sbClient.close();
      }
    }
    
    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. 上記のコードで、接続文字列と、キューの名前を入力します。
4. その後、コマンド プロンプトで `node receiveMessages.js` コマンドを実行して、このファイルを実行します。

お疲れさまでした。 これで、Service Bus キューからメッセージが受信されました。

[CreateReceiver](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient#createreceiver-receivemode-) メソッドでは `ReceiveMode` を取り込みます。これは、[ReceiveAndDelete](message-transfers-locks-settlement.md#settling-receive-operations) および [PeekLock](message-transfers-locks-settlement.md#settling-receive-operations) という値を持つ列挙型です。 メッセージで `complete()`、`abandon()`、`defer()`、または `deadletter()` メソッドのいずれかを使って `PeekLock` モードを使用する場合は、必ず、[メッセージを解決](message-transfers-locks-settlement.md#settling-receive-operations)してください。

> [!NOTE]
> Service Bus リソースは、[Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/) で管理できます。 Service Bus Explorer を使用すると、ユーザーは Service Bus 名前空間に接続し、簡単な方法でメッセージング エンティティを管理できます。 このツールには、インポート/エクスポート機能や、トピック、キュー、サブスクリプション、リレー サービス、通知ハブ、イベント ハブをテストする機能などの高度な機能が用意されています。 

## <a name="next-steps"></a>次の手順
詳細については、次のリソースを参照してください。
- [キュー、トピック、サブスクリプション](service-bus-queues-topics-subscriptions.md)
- [GitHub で Service Bus の Nodejsサンプル](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples/javascript)を確認する
- [Node.js デベロッパー センター](https://azure.microsoft.com/develop/nodejs/)

