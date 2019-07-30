---
title: Azure Service Bus のトピックとサブスクリプションを Node.js で使用する方法 | Microsoft Docs
description: Node.js アプリから Azure の Service Bus トピックとサブスクリプションを使用する方法を学習します。
services: service-bus-messaging
documentationcenter: nodejs
author: axisc
manager: timlt
editor: spelluru
ms.assetid: b9f5db85-7b6c-4cc7-bd2c-bd3087c99875
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 04/15/2019
ms.author: aschhab
ms.openlocfilehash: 7686014adb989494e6df277de4137b76c3125696
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "65992132"
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-nodejs-and-the-azureservice-bus-package"></a>Service Bus のトピックとサブスクリプションを Node.js および azure/service-bus パッケージで使用する方法
> [!div class="op_multi_selector" title1="プログラミング言語" title2="Node.js パッケージ"]
> - [(Node.js | azure-sb)](service-bus-nodejs-how-to-use-topics-subscriptions.md)
> - [(Node.js | @azure/service-bus)](service-bus-nodejs-how-to-use-topics-subscriptions-new-package.md)

このチュートリアルでは、新しい [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) パッケージを使用して Service Bus トピックにメッセージを送り、Service Bus サブスクリプションからメッセージを受け取る Node.js プログラムの記述方法を学習します。 このパッケージではより高速な [AMQP 1.0 プロトコル](service-bus-amqp-overview.md)が使用されます。一方、以前の [azure-sb](https://www.npmjs.com/package/azure-sb) パッケージでは [Service Bus REST ランタイム API](/rest/api/servicebus/service-bus-runtime-rest) が使用されていました。 サンプルは JavaScript で記述されています。

## <a name="prerequisites"></a>前提条件
- Azure サブスクリプション。 このチュートリアルを完了するには、Azure アカウントが必要です。 [MSDN のサブスクライバー特典](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF)を有効にするか、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)にサインアップしてください。
- 操作するトピックとサブスクリプションがない場合は、[Azure portal を使用する Service Bus トピックおよびサブスクリプションの作成](service-bus-quickstart-topics-subscriptions-portal.md)に関する記事にある手順に従って、それらを作成します。 Service Bus インスタンスの接続文字列と、作成したトピックおよびサブスクリプションの名前をメモしておいてください。 サンプルでこれらの値を使用します。

> [!NOTE]
> - このチュートリアルでは、[Nodejs](https://nodejs.org/) を使用して、コピーおよび実行できるサンプルを操作します。 Node.js アプリケーションを作成する手順については、[Node.js アプリケーションの作成と Azure Web サイトへのデプロイ](../app-service/app-service-web-get-started-nodejs.md)に関するページ、または [Windows PowerShell を使用する Node.js クラウド サービス](../cloud-services/cloud-services-nodejs-develop-deploy-app.md)に関するページを参照してください。
> - 新しい [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) パッケージでは、トピックとサブスクリプションの作成がまだサポートされません。 ブログラムでそれらを作成する場合は、[@azure/arm-servicebus](https://www.npmjs.com/package/@azure/arm-servicebus) パッケージを使用してください。

### <a name="use-node-package-manager-npm-to-install-the-package"></a>ノード パッケージ マネージャー (NPM) を使用してパッケージをインストールする
Service Bus の npm パッケージをインストールするには、パスに `npm` を設定したコマンド プロンプトを開き、サンプルを格納するフォルダーにディレクトリを変更してから、このコマンドを実行します。

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-topic"></a>メッセージをトピックに送信する
Service Bus トピックとのやりとりは、[ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) クラスをインスタンス化し、それを使用して [TopicClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/topicclient) クラスをインスタンス化することから始まります。 トピック クライアントを取得したら、送信側を作成し、その上で [send](https://docs.microsoft.com/javascript/api/%40azure/service-bus/sender#send-sendablemessageinfo-) または [sendBatch](https://docs.microsoft.com/javascript/api/@azure/service-bus/sender#sendbatch-sendablemessageinfo---) メソッドを使用してメッセージを送信できます。

1. [Visual Studio Code](https://code.visualstudio.com/) など、好みのエディターを開きます
2. `send.js` というファイルを作成し、そこに以下のコードを貼り付けます。 このコードでは、トピックに 10 件のメッセージが送信されます。

    ```javascript
    const { ServiceBusClient } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const topicName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const topicClient = sbClient.createTopicClient(topicName);
      const sender = topicClient.createSender();
      
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

            await topicClient.close();
          } finally {
            await sbClient.close();
          }
    }
    
    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. 上記のコードで、接続文字列と、トピックの名前を入力します。
4. その後、コマンド プロンプトで `node send.js` コマンドを実行して、このファイルを実行します。 

お疲れさまでした。 これで、Service Bus キューにメッセージが送信されました。

メッセージには、送信時に設定できる、`label` や `messageId` のような標準プロパティがいくつかあります。 カスタム プロパティを設定する場合は、`userProperties` を使用します。これは、カスタム データのキーと値のペアを保持できる json オブジェクトです。

Service Bus トピックでサポートされているメッセージの最大サイズは、[Standard レベル](service-bus-premium-messaging.md)では 256 KB、[Premium レベル](service-bus-premium-messaging.md)では 1 MB です。 1 つのトピックで保持されるメッセージ数に上限はありませんが、1 つのトピックで保持できるメッセージの合計サイズには上限があります。 このトピックのサイズはトピックの作成時に定義します。上限は 5 GB です。 クォータの詳細については、「[Service Bus のクォータ](service-bus-quotas.md)」を参照してください。

## <a name="receive-messages-from-a-subscription"></a>サブスクリプションからメッセージを受信する
Service Bus サブスクリプションとのやりとりは、[ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) クラスをインスタンス化し、それを使用して [SubscriptionClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/subscriptionclient) クラスをインスタンス化することから始まります。 サブスクリプション クライアントを取得したら、受信側を作成し、[receiveMessages](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#receivemessages-number--undefined---number-) または [registerMessageHandler](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#registermessagehandler-onmessage--onerror--messagehandleroptions-) メソッドを使用して、メッセージを受信できます。

1. [Visual Studio Code](https://code.visualstudio.com/) など、好みのエディターを開きます
2. `recieve.js` というファイルを作成し、そこに以下のコードを貼り付けます。 このコードでは、サブスクリプションから 10 件のメッセージを受信してみます。 受信する実際の数は、サブスクリプションのメッセージの数とネットワーク待機時間によって異なります。

    ```javascript
    const { ServiceBusClient, ReceiveMode } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const topicName = ""; 
    const subscriptionName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const subscriptionClient = sbClient.createSubscriptionClient(topicName, subscriptionName);
      const receiver = subscriptionClient.createReceiver(ReceiveMode.ReceiveAndDelete);
      
      try {
        const messages = await receiver.receiveMessages(10);
        console.log("Received messages:");
        console.log(messages.map(message => message.body));
        
        await subscriptionClient.close();
      } finally {
        await sbClient.close();
      }
    }
    
    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. 上記のコードで、接続文字列と、トピックおよびサブスクリプションの名前を入力します。
4. その後、コマンド プロンプトで `node receiveMessages.js` コマンドを実行して、このファイルを実行します。

お疲れさまでした。 これで、Service Bus サブスクリプションからメッセージが受信されました。

[createReceiver](https://docs.microsoft.com/javascript/api/%40azure/service-bus/subscriptionclient#createreceiver-receivemode-) メソッドでは `ReceiveMode` を取り込みます。これは、[ReceiveAndDelete](message-transfers-locks-settlement.md#settling-receive-operations) および [PeekLock](message-transfers-locks-settlement.md#settling-receive-operations) という値を持つ列挙型です。 メッセージで `complete()`、`abandon()`、`defer()`、または `deadletter()` メソッドのいずれかを使って `PeekLock` モードを使用する場合は、必ず、[メッセージを解決](message-transfers-locks-settlement.md#settling-receive-operations)してください。

## <a name="subscription-filters-and-actions"></a>サブスクリプションのフィルターとアクション
Service Bus では、[サブスクリプションのフィルターとアクション](topic-filters.md)がサポートされます。これにより、サブスクリプションへの受信メッセージをフィルター処理し、それらのプロパティを編集できます。

`SubscriptionClient` のインスタンスを取得したら、以下のメソッドを使用して、フィルターとアクションを制御するためのサブスクリプションに関する規則を取得、追加および削除できます。

- getRules
- addRule
- removeRule

すべてのサブスクリプションには、true フィルターを使用してすべての受信メッセージを許可するための、既定の規則があります。 新しい規則を追加する場合は、必ず、既定のフィルターを削除して、新しい規則でフィルターが機能するようにしてください。 サブスクリプションに規則がない場合、メッセージは受信されません。

> [!NOTE]
> Service Bus リソースは、[Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/) で管理できます。 Service Bus Explorer を使用すると、ユーザーは Service Bus 名前空間に接続し、簡単な方法でメッセージング エンティティを管理できます。 このツールには、インポート/エクスポート機能や、トピック、キュー、サブスクリプション、リレー サービス、通知ハブ、イベント ハブをテストする機能などの高度な機能が用意されています。 

## <a name="next-steps"></a>次の手順
詳細については、次のリソースを参照してください。

- [キュー、トピック、サブスクリプション](service-bus-queues-topics-subscriptions.md)
- [GitHub で Service Bus の Nodejsサンプル](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples/javascript)を確認する
- [Node.js デベロッパー センター](https://azure.microsoft.com/develop/nodejs/)


