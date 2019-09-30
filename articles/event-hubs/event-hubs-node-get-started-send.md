---
title: Node.js を使用してイベントを送受信する - Azure Event Hubs | Microsoft Docs
description: この記事では、Azure Event Hubs からイベントを送信する Node.js アプリケーションを作成するためのチュートリアルを提供します。
services: event-hubs
author: spelluru
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.custom: seodec18
ms.date: 04/15/2019
ms.author: spelluru
ms.openlocfilehash: 3bb222d3197ef37d56767300d71cc350d25a37bd
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2019
ms.locfileid: "70984480"
---
# <a name="send-events-to-or-receive-events-from-azure-event-hubs-using-nodejs"></a>Node.js を使用して Azure Event Hubs との間でイベントを送受信する | Microsoft Docs

Azure Event Hubs はビッグ データ ストリーミング プラットフォームであり、毎秒数百万のイベントを受け取って処理できるイベント インジェスト サービスです。 Event Hubs では、分散されたソフトウェアやデバイスから生成されるイベント、データ、またはテレメトリを処理および格納できます。 イベント ハブに送信されたデータは、任意のリアルタイム分析プロバイダーやバッチ処理/ストレージ アダプターを使用して、変換および保存できます。 Event Hubs の詳しい概要については、[Event Hubs の概要](event-hubs-about.md)と [Event Hubs の機能](event-hubs-features.md)に関するページをご覧ください。

このチュートリアルでは、Node.js アプリケーションを作成し、イベント ハブとの間でイベントを送受信する方法について説明します。

> [!NOTE]
> このクイック スタートをサンプルとして [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client) からダウンロードし、`EventHubConnectionString` と `EventHubName` の文字列を対象のイベント ハブの値に置き換え、実行します。 または、このチュートリアルの手順に従って独自のものを作成します。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次の前提条件を用意しておく必要があります。

- アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) を作成してください。
- Node.js バージョン 8.x 以降。 [https://nodejs.org](https://nodejs.org) から最新の LTS バージョンをダウンロードします。
- Visual Studio Code (推奨) または他の任意の IDE
- **Event Hubs 名前空間とイベント ハブを作成する**。 最初の手順では、[Azure Portal](https://portal.azure.com) を使用して Event Hubs 型の名前空間を作成し、アプリケーションがイベント ハブと通信するために必要な管理資格情報を取得します。 名前空間とイベント ハブを作成するには、[こちらの記事](event-hubs-create.md)の手順を実行した後、このチュートリアルに示されている手順を続けます。 その後、次の記事の手順に従って、イベント ハブ名前空間用の接続文字列を取得します。[接続文字列を取得する](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)。 このチュートリアルの中で、その接続文字列が後で必要になります。


### <a name="install-npm-package"></a>npm パッケージをインストールする
[Event Hubs 用の npm パッケージ](https://www.npmjs.com/package/@azure/event-hubs)をインストールするには、パスに `npm` を設定してコマンド プロンプトを開き、サンプルを格納するフォルダーにディレクトリを変更してから、このコマンドを実行します。

```shell
npm install @azure/event-hubs
```

[イベント プロセッサ ホスト用の npm パッケージ](https://www.npmjs.com/package/@azure/event-processor-host)をインストールするには、代わりに以下のコマンドを実行します。

```shell
npm install @azure/event-processor-host
```

## <a name="send-events"></a>送信イベント

このセクションでは、イベント ハブにイベントを送信する Node.js アプリケーションの作成方法を説明します。 

1. [Visual Studio Code](https://code.visualstudio.com) など、お好みのエディターを開きます。 
2. `send.js` というファイルを作成し、そこに以下のコードを貼り付けます。 次の記事の手順に従って、イベント ハブ名前空間用の接続文字列を取得します。[接続文字列を取得する](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)。 

    ```javascript
    const { EventHubClient } = require("@azure/event-hubs");

    // Connection string - primary key of the Event Hubs namespace. 
    // For example: Endpoint=sb://myeventhubns.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    const connectionString = "Endpoint=sb://<EVENT HUBS NAMESPACE NAME>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<SHARED ACCESS KEY>";

    // Name of the event hub. For example: myeventhub
    const eventHubsName = "<EVENT HUB NAME>";

    async function main() {
      const client = EventHubClient.createFromConnectionString(connectionString, eventHubsName);

      for (let i = 0; i < 100; i++) {
        const eventData = {body: `Event ${i}`};
        console.log(`Sending message: ${eventData.body}`);
        await client.send(eventData);
      }

      await client.close();
    }

    main().catch(err => {
      console.log("Error occurred: ", err);
    });
    ```
3. 上記のコード内に、お使いの Event Hub の接続文字列と名前を入力します。
4. その後、コマンド プロンプトで `node send.js` コマンドを実行して、このファイルを実行します。 これにより、お使いの Event Hub に 100 個のイベントが送信されます。

お疲れさまでした。 これで、イベントをイベント ハブに送信できました。


## <a name="receive-events"></a>受信イベント

このセクションでは、イベント ハブにある既定のコンシューマー グループの 1 つのパーティションからイベントを受信する Node.js アプリケーションを作成する方法を示します。 

1. [Visual Studio Code](https://code.visualstudio.com) など、お好みのエディターを開きます。 
2. `receive.js` というファイルを作成し、そこに以下のコードを貼り付けます。
    ```javascript
    const { EventHubClient, delay } = require("@azure/event-hubs");

    // Connection string - primary key of the Event Hubs namespace. 
    // For example: Endpoint=sb://myeventhubns.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    const connectionString = "Endpoint=sb://<EVENT HUBS NAMESPACE NAME>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<SHARED ACCESS KEY>";

    // Name of the event hub. For example: myeventhub
    const eventHubsName = "<EVENT HUB NAME>";

    async function main() {
      const client = EventHubClient.createFromConnectionString(connectionString, eventHubsName);
      const allPartitionIds = await client.getPartitionIds();
      const firstPartitionId = allPartitionIds[0];

      const receiveHandler = client.receive(firstPartitionId, eventData => {
        console.log(`Received message: ${eventData.body} from partition ${firstPartitionId}`);
      }, error => {
        console.log('Error when receiving message: ', error)
      });

      // Sleep for a while before stopping the receive operation.
      await delay(15000);
      await receiveHandler.stop();

      await client.close();
    }

    main().catch(err => {
      console.log("Error occurred: ", err);
    });
    ```
3. 上記のコードに、お使いの Event Hub の接続文字列と名前を入力します。
4. その後、コマンド プロンプトで `node receive.js` コマンドを実行して、このファイルを実行します。 これにより、お使いの Event Hub での既定のコンシューマー グループのパーティションの 1 つから、イベントを受信します。

お疲れさまでした。 これで、イベント ハブからイベントを受信できました。

## <a name="receive-events-using-event-processor-host"></a>イベント プロセッサ ホストを使用してイベントを受信する

このセクションでは、Node.js アプリケーション内で Azure [EventProcessorHost](event-hubs-event-processor-host.md) を使用して、イベント ハブからイベントを受信する方法を示します。 EventProcessorHost (EPH) を使用すると、イベント ハブの顧客グループのすべてのパーティションで受信者を作成することで、イベント ハブからイベントを効率的に受信できます。 これにより、Azure Storage Blob 内で定期的に受信したメッセージのメタデータがチェックポイントに設定されます。 この手法によって、後で前回終了したところからメッセージを継続して受信しやすくなります。

1. [Visual Studio Code](https://code.visualstudio.com) など、お好みのエディターを開きます。 
2. `receiveAll.js` というファイルを作成し、そこに以下のコードを貼り付けます。
    ```javascript
    const { EventProcessorHost, delay } = require("@azure/event-processor-host");

    // Connection string - primary key of the Event Hubs namespace. 
    // For example: Endpoint=sb://myeventhubns.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    const eventHubConnectionString = "Endpoint=sb://<EVENT HUBS NAMESPACE NAME>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<SHARED ACCESS KEY>";

    // Name of the event hub. For example: myeventhub
    const eventHubName = "<EVENT HUB NAME>";

    // Azure Storage connection string
    const storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=<STORAGE ACCOUNT NAME>;AccountKey=<STORAGE ACCOUNT KEY>;EndpointSuffix=core.windows.net";

    async function main() {
      const eph = EventProcessorHost.createFromConnectionString(
        "my-eph",
        storageConnectionString,
        "my-storage-container-name",
        eventHubConnectionString,
        {
          eventHubPath: eventHubName,
          onEphError: (error) => {
            console.log("[%s] Error: %O", error);
          }
        }
      );


      eph.start((context, eventData) => {
        console.log(`Received message: ${eventData.body} from partition ${context.partitionId}`);
      }, error => {
        console.log('Error when receiving message: ', error)
      });

      // Sleep for a while before stopping the receive operation.
      await delay(15000);
      await eph.stop();
    }

    main().catch(err => {
      console.log("Error occurred: ", err);
    });

    ```
3. 上記のコード内に、お使いの Event Hub の接続文字列と名前に加えて、Azure BLOB ストレージ用の接続文字列を入力します。
4. その後、コマンド プロンプトで `node receiveAll.js` コマンドを実行して、このファイルを実行します。

お疲れさまでした。 これで、イベント プロセッサ ホストを使用して、イベント ハブからイベントを受信できました。 これにより、お使いの Event Hub の既定のコンシューマー グループのすべてのパーティションから、イベントを受信します。

## <a name="next-steps"></a>次の手順
次の記事を参照してください。

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Azure Event Hubs の機能と用語](event-hubs-features.md)
- [Event Hubs の FAQ](event-hubs-faq.md)
- GitHub 上で [Event Hubs](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples) および[イベント プロセッサ ホスト](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-processor-host/samples)用の他の Node.js サンプルを確認してください。
