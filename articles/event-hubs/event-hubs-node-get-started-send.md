---
title: JavaScript を使用して Azure Event Hubs との間でイベントを送受信する (最新)
description: この記事では、最新の azure/event-hubs パッケージを使用して、Azure Event Hubs との間でイベントを送受信する JavaScript アプリケーションを作成する方法について説明します。
ms.topic: quickstart
ms.date: 06/23/2020
ms.custom: devx-track-js
ms.openlocfilehash: c418c13346fb1ec8ba16965fa1020c676ddf3ac6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104602555"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-by-using-javascript--azureevent-hubs"></a>JavaScript (azure/event-hubs) を使用して Event Hubs との間でイベントを送受信する
このクイックスタートでは、**azure/event-hubs** JavaScript パッケージを使用して、イベント ハブとの間でイベントを送受信する方法について説明します。 


## <a name="prerequisites"></a>前提条件
Azure Event Hubs を初めて使用する場合は、このクイックスタートを行う前に[イベント ハブの概要](event-hubs-about.md)を参照してください。 

このクイック スタートを完了するには、次の前提条件を用意しておく必要があります。

- **Microsoft Azure サブスクリプション**。 Azure Event Hubs を含む Azure サービスを使用するには、サブスクリプションが必要です。  既存の Microsoft Azure アカウントをお持ちでない場合は、[アカウントを作成する](https://azure.microsoft.com)際に、[無料試用版](https://azure.microsoft.com/free/)にサインアップするか、MSDN サブスクライバー特典を利用できます。
- Node.js バージョン 8.x 以降。 最新の[長期サポート (LTS) バージョン](https://nodejs.org)をダウンロードしてください。  
- Visual Studio Code (推奨) または他の任意の統合開発環境 (IDE)。  
- アクティブな Event Hubs 名前空間とイベント ハブ。 これらを作成するには、次の手順を実行します。 

   1. [Azure portal](https://portal.azure.com) で *Event Hubs* 型の名前空間を作成し、アプリケーションがイベント ハブと通信するために必要な管理資格情報を取得します。 
   1. 名前空間とイベント ハブを作成するには、「[クイック スタート: Azure portal を使用したイベント ハブの作成](event-hubs-create.md)」の手順に従います。
   1. 続けてこのクイックスタートの手順に従います。 
   1. イベント ハブの名前空間の接続文字列を取得するために、[接続文字列の取得](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)に関するセクションの手順に従います。 接続文字列は記録しておいてください。後でこのクイックスタートの中で使用します。
- **Event Hubs 名前空間とイベント ハブを作成する**。 最初の手順では、[Azure Portal](https://portal.azure.com) を使用して Event Hubs 型の名前空間を作成し、アプリケーションがイベント ハブと通信するために必要な管理資格情報を取得します。 名前空間とイベント ハブを作成するには、[こちらの記事](event-hubs-create.md)の手順に従います。 その後、次の記事の手順に従って、**Event Hubs 名前空間用の接続文字列** を取得します: [接続文字列を取得する](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)。 この接続文字列は、このクイックスタートの後の手順で必要になります。

### <a name="install-the-npm-package"></a>npm パッケージのインストール
[Event Hubs 用の Node Package Manager (npm) パッケージ](https://www.npmjs.com/package/@azure/event-hubs)をインストールするには、パスに *npm* を設定してコマンド プロンプトを開き、サンプルを格納するフォルダーにディレクトリを変更してから、このコマンドを実行します。

```shell
npm install @azure/event-hubs
```

受信側では、追加で 2 つのパッケージをインストールする必要があります。 このクイックスタートでは、既に読み取ったイベントをプログラムが読み取らないよう、Azure Blob Storage を使用してチェックポイントを永続化します。 BLOB 内で定期的に受信したメッセージのメタデータ チェックポイントが実行されます。 この手法によって、後で前回終了したところからメッセージを継続して受信しやすくなります。

次のコマンドを実行します。

```shell
npm install @azure/storage-blob
```

```shell
npm install @azure/eventhubs-checkpointstore-blob
```

## <a name="send-events"></a>送信イベント

このセクションでは、イベント ハブにイベントを送信する JavaScript アプリケーションを作成します。

1. [Visual Studio Code](https://code.visualstudio.com) など、お好みのエディターを開きます。
1. *send.js* というファイルを作成し、そこに次のコードを貼り付けます。

    ```javascript
    const { EventHubProducerClient } = require("@azure/event-hubs");

    const connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";
    const eventHubName = "EVENT HUB NAME";

    async function main() {

      // Create a producer client to send messages to the event hub.
      const producer = new EventHubProducerClient(connectionString, eventHubName);

      // Prepare a batch of three events.
      const batch = await producer.createBatch();
      batch.tryAdd({ body: "First event" });
      batch.tryAdd({ body: "Second event" });
      batch.tryAdd({ body: "Third event" });    

      // Send the batch to the event hub.
      await producer.sendBatch(batch);

      // Close the producer client.
      await producer.close();

      console.log("A batch of three events have been sent to the event hub");
    }

    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
1. このコード内の次の値は、実際の値に置き換えてください。
    * `EVENT HUBS NAMESPACE CONNECTION STRING` 
    * `EVENT HUB NAME`
1. `node send.js` を実行して、このファイルを実行します。 このコマンドにより、3 つのイベントのバッチがイベント ハブに送信されます
1. Azure portal で、イベント ハブがメッセージを受信したことを確認します。 **[メトリック]** セクションで **[メッセージ]** ビューに切り替えます。 ページを最新の情報に更新して、グラフを更新します。 メッセージが受信されたことが示されるまでに数秒かかることがあります。

    [![イベント ハブがメッセージを受信したことを確認する](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)

    > [!NOTE]
    > 情報提供を目的とした補足コメントを含む完全なソース コードについては、[GitHub の sendEvents.js ページ](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/samples/javascript/sendEvents.js)にアクセスしてください。

お疲れさまでした。 これで、イベントをイベント ハブに送信できました。


## <a name="receive-events"></a>受信イベント
このセクションでは、JavaScript アプリケーション内で Azure Blob Storage チェックポイント ストアを使用して、イベント ハブからイベントを受信します。 これにより、Azure Storage Blob 内で定期的に受信したメッセージのメタデータ チェックポイントが実行されます。 この手法によって、後で前回終了したところからメッセージを継続して受信しやすくなります。

> [!WARNING]
> このコードを Azure Stack Hub で実行すると、特定の Storage API バージョンを対象としている場合を除き、実行時エラーが発生します。 これは、Event Hubs SDK では、Azure で利用できる最新の Azure Storage API が使用されますが、Azure Stack Hub プラットフォームではこれを利用できない可能性があるためです。 Azure Stack Hub でサポートされる Storage Blob SDK のバージョンは、Azure で一般的に利用できるバージョンと異なる場合があります。 チェックポイント ストアとして Azure Blob Storage を使用している場合は、[Azure Stack Hub ビルドでサポートされている Azure Storage API バージョン](/azure-stack/user/azure-stack-acs-differences?#api-version)を確認し、コード内でそのバージョンを対象にします。 
>
> たとえば、Azure Stack Hub バージョン 2005 上で実行している場合、Storage サービスで利用できる最も高いバージョンは 2019-02-02 となります。 既定では、Event Hubs SDK クライアント ライブラリでは、Azure で利用できる最も高いバージョン (SDK のリリース時点では 2019-07-07) が使用されます。 この場合は、このセクションの手順に従うことに加え、Storage サービス API バージョン 2019-02-02 を対象とするコードを追加する必要もあります。 特定の Storage API バージョンを対象にする方法の例については、GitHub にある [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/javascript/receiveEventsWithApiSpecificStorage.js) サンプルと [TypeScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/typescript/src/receiveEventsWithApiSpecificStorage.ts) サンプルを参照してください。 


### <a name="create-an-azure-storage-account-and-a-blob-container"></a>Azure Storage アカウントと BLOB コンテナーを作成する
Azure Storage アカウントを作成して、そこに BLOB コンテナーを作成するには、次の作業を行います。

1. [Azure Storage アカウントを作成する](../storage/common/storage-account-create.md?tabs=azure-portal)  
2. [ストレージ アカウントに BLOB コンテナーを作成する](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)  
3. [ストレージ アカウントへの接続文字列を取得する](../storage/common/storage-configure-connection-string.md)

接続文字列とコンテナー名を記録しておいてください。後から受信コードで使用します。

### <a name="write-code-to-receive-events"></a>イベントを受信するコードを記述する

1. [Visual Studio Code](https://code.visualstudio.com) など、お好みのエディターを開きます。
1. *receive.js* というファイルを作成し、そこに次のコードを貼り付けます。

    ```javascript
    const { EventHubConsumerClient } = require("@azure/event-hubs");
    const { ContainerClient } = require("@azure/storage-blob");    
    const { BlobCheckpointStore } = require("@azure/eventhubs-checkpointstore-blob");

    const connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";    
    const eventHubName = "EVENT HUB NAME";
    const consumerGroup = "$Default"; // name of the default consumer group
    const storageConnectionString = "AZURE STORAGE CONNECTION STRING";
    const containerName = "BLOB CONTAINER NAME";

    async function main() {
      // Create a blob container client and a blob checkpoint store using the client.
      const containerClient = new ContainerClient(storageConnectionString, containerName);
      const checkpointStore = new BlobCheckpointStore(containerClient);

      // Create a consumer client for the event hub by specifying the checkpoint store.
      const consumerClient = new EventHubConsumerClient(consumerGroup, connectionString, eventHubName, checkpointStore);

      // Subscribe to the events, and specify handlers for processing the events and errors.
      const subscription = consumerClient.subscribe({
          processEvents: async (events, context) => {
            if (events.length === 0) {
              console.log(`No events received within wait time. Waiting for next interval`);
              return;
            }
          
            for (const event of events) {
              console.log(`Received event: '${event.body}' from partition: '${context.partitionId}' and consumer group: '${context.consumerGroup}'`);
            }
            // Update the checkpoint.
            await context.updateCheckpoint(events[events.length - 1]);
          },

          processError: async (err, context) => {
            console.log(`Error : ${err}`);
          }
        }
      );

      // After 30 seconds, stop processing.
      await new Promise((resolve) => {
        setTimeout(async () => {
          await subscription.close();
          await consumerClient.close();
          resolve();
        }, 30000);
      });
    }

    main().catch((err) => {
      console.log("Error occurred: ", err);
    });    
    ```
1. このコード内の次の値は、実際の値に置き換えてください。
    - `EVENT HUBS NAMESPACE CONNECTION STRING`
    - `EVENT HUB NAME`
    - `AZURE STORAGE CONNECTION STRING`
    - `BLOB CONTAINER NAME`
1. コマンド プロンプトから `node receive.js` を実行して、このファイルを実行します。 受信したイベントに関するメッセージがウィンドウに表示されます。

    > [!NOTE]
    > 情報提供を目的とした補足コメントを含む完全なソース コードについては、[GitHub の receiveEventsUsingCheckpointStore.js ページ](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/javascript/receiveEventsUsingCheckpointStore.js)にアクセスしてください。

お疲れさまでした。 これで、イベント ハブからイベントを受信できました。 受信側プログラムは、イベント ハブの既定のコンシューマー グループのすべてのパーティションからイベントを受信します。

## <a name="next-steps"></a>次のステップ
GitHub で次のサンプルを確認します。

- [JavaScript のサンプル](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/javascript)
- [TypeScript のサンプル](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/typescript)
