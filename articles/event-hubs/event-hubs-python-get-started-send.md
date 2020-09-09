---
title: Python を使用して Azure Event Hubs との間でイベントを送受信する (最新)
description: この記事では、最新の azure-eventhub バージョン 5 パッケージを使用して、Azure Event Hubs との間でイベントを送受信する Python アプリケーションを作成する方法について説明します。
ms.topic: quickstart
ms.date: 02/11/2020
ms.openlocfilehash: b6a30ba0cef8c460a2a3035b3ab40fd8173d7b2e
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88933904"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-by-using-python-azure-eventhub-version-5"></a>Python を使用して Event Hubs との間でイベントを送受信する (azure-eventhub バージョン 5)
このクイックスタートでは、**azure-eventhub バージョン 5** Python パッケージを使用して、イベント ハブとの間でイベントを送受信する方法について説明します。

## <a name="prerequisites"></a>前提条件
Azure Event Hubs を初めて使用する場合は、このクイックスタートを行う前に [Event Hubs の概要](event-hubs-about.md)を参照してください。 

このクイック スタートを完了するには、次の前提条件を用意しておく必要があります。

- **Microsoft Azure サブスクリプション**。 Azure Event Hubs を含む Azure サービスを使用するには、サブスクリプションが必要です。  既存の Microsoft Azure アカウントをお持ちでない場合は、[アカウントを作成する](https://azure.microsoft.com)際に、[無料試用版](https://azure.microsoft.com/free/)にサインアップするか、MSDN サブスクライバー特典を利用できます。
- Python 2.7 または 3.5 以降 (PIP がインストールおよび更新されている)。
- Event Hubs 用の Python パッケージ。 

    パッケージをインストールするには、コマンド プロンプトで Python をパス設定して、次のコマンドを実行します。

    ```cmd
    pip install azure-eventhub
    ```

    Azure Blob Storage をチェックポイント ストアとして使用してイベントを受信するには、次のパッケージをインストールします。

    ```cmd
    pip install azure-eventhub-checkpointstoreblob-aio
    ```
- **Event Hubs 名前空間とイベント ハブを作成する**。 最初の手順では、[Azure Portal](https://portal.azure.com) を使用して Event Hubs 型の名前空間を作成し、アプリケーションがイベント ハブと通信するために必要な管理資格情報を取得します。 名前空間とイベント ハブを作成するには、[こちらの記事](event-hubs-create.md)の手順に従います。 その後、次の記事の手順に従って、**Event Hubs 名前空間用の接続文字列**を取得します: [接続文字列を取得する](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)。 この接続文字列は、このクイックスタートの後の手順で必要になります。

## <a name="send-events"></a>送信イベント
このセクションでは、前に作成したイベント ハブにイベントを送信する Python スクリプトを作成します。

1. [Visual Studio Code](https://code.visualstudio.com/)など、お使いの Python エディターを開きます。
2. *send.py* という名前のスクリプトを作成します。 このスクリプトは、前に作成したイベント ハブにイベントのバッチを送信します。
3. 次のコードを *send.py* に貼り付けます。

    ```python
    import asyncio
    from azure.eventhub.aio import EventHubProducerClient
    from azure.eventhub import EventData

    async def run():
        # Create a producer client to send messages to the event hub.
        # Specify a connection string to your event hubs namespace and
            # the event hub name.
        producer = EventHubProducerClient.from_connection_string(conn_str="EVENT HUBS NAMESPACE - CONNECTION STRING", eventhub_name="EVENT HUB NAME")
        async with producer:
            # Create a batch.
            event_data_batch = await producer.create_batch()

            # Add events to the batch.
            event_data_batch.add(EventData('First event '))
            event_data_batch.add(EventData('Second event'))
            event_data_batch.add(EventData('Third event'))

            # Send the batch of events to the event hub.
            await producer.send_batch(event_data_batch)

    loop = asyncio.get_event_loop()
    loop.run_until_complete(run())

    ```

    > [!NOTE]
    > 情報提供を目的としたコメントを含む完全なソース コードについては、[GitHub の send_async.py ページ](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/samples/async_samples/send_async.py)にアクセスしてください。
    

## <a name="receive-events"></a>受信イベント
このクイックスタートでは、Azure Blob Storage をチェックポイント ストアとして使用します。 チェックポイント ストアは、チェックポイント (最後の読み取り位置) を保持するために使用されます。  

> [!NOTE]
> Azure Stack Hub 上で実行する場合、そのプラットフォームでサポートされる Storage Blob SDK のバージョンは、Azure で一般的に利用できるバージョンと異なります。 たとえば、[Azure Stack Hub バージョン 2002 上で](/azure-stack/user/event-hubs-overview)実行している場合、Storage Service で利用可能な最も高いバージョンは 2017-11-09 です。 この場合は、このセクションの手順に従うことに加え、Storage Service API バージョン 2017-11-09 を対象にするコードを追加する必要があります。 特定の Storage API バージョンを対象にする方法の例については、GitHub の[同期版](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob/samples/receive_events_using_checkpoint_store_storage_api_version.py)のサンプルと[非同期版](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/receive_events_using_checkpoint_store_storage_api_version_async.py)のサンプルを参照してください。 Azure Stack Hub でサポートされる Azure Storage サービスのバージョンについて詳しくは、「[Azure Stack Hub のストレージ: 違いと考慮事項](/azure-stack/user/azure-stack-acs-differences)」をご覧ください。


### <a name="create-an-azure-storage-account-and-a-blob-container"></a>Azure Storage アカウントと BLOB コンテナーを作成する
次の手順に従って、Azure Storage アカウントと BLOB コンテナーを作成します。

1. [Azure ストレージ アカウントの作成](../storage/common/storage-account-create.md?tabs=azure-portal)
2. [BLOB コンテナーを作成する](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [ストレージ アカウントへの接続文字列を取得する](../storage/common/storage-configure-connection-string.md)

接続文字列とコンテナー名を記録しておいてください。後から受信コードで使用します。


### <a name="create-a-python-script-to-receive-events"></a>イベントを受信する Python スクリプトの作成

このセクションでは、イベント ハブからイベントを受信する Python スクリプトを作成します。

1. [Visual Studio Code](https://code.visualstudio.com/)など、お使いの Python エディターを開きます。
2. *recv.py* という名前のスクリプトを作成します。
3. 次のコードを *recv.py* に貼り付けます。

    ```python
    import asyncio
    from azure.eventhub.aio import EventHubConsumerClient
    from azure.eventhub.extensions.checkpointstoreblobaio import BlobCheckpointStore


    async def on_event(partition_context, event):
        # Print the event data.
        print("Received the event: \"{}\" from the partition with ID: \"{}\"".format(event.body_as_str(encoding='UTF-8'), partition_context.partition_id))

        # Update the checkpoint so that the program doesn't read the events
        # that it has already read when you run it next time.
        await partition_context.update_checkpoint(event)

    async def main():
        # Create an Azure blob checkpoint store to store the checkpoints.
        checkpoint_store = BlobCheckpointStore.from_connection_string("AZURE STORAGE CONNECTION STRING", "BLOB CONTAINER NAME")

        # Create a consumer client for the event hub.
        client = EventHubConsumerClient.from_connection_string("EVENT HUBS NAMESPACE CONNECTION STRING", consumer_group="$Default", eventhub_name="EVENT HUB NAME", checkpoint_store=checkpoint_store)
        async with client:
            # Call the receive method. Read from the beginning of the partition (starting_position: "-1")
            await client.receive(on_event=on_event,  starting_position="-1")

    if __name__ == '__main__':
        loop = asyncio.get_event_loop()
        # Run the main method.
        loop.run_until_complete(main())    
    ```

    > [!NOTE]
    > 情報提供を目的とした補足コメントを含む完全なソース コードについては、[GitHub の recv_with_checkpoint_store_async.py ページ](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/samples/async_samples/recv_with_checkpoint_store_async.py)にアクセスしてください。


### <a name="run-the-receiver-app"></a>受信側アプリを実行する

スクリプトを実行するには、Python をパス設定した状態でコマンド プロンプトを開き、次のコマンドを実行します。

```bash
python recv.py
```

### <a name="run-the-sender-app"></a>送信側アプリを実行する

スクリプトを実行するには、Python をパス設定した状態でコマンド プロンプトを開き、次のコマンドを実行します。

```bash
python send.py
```

イベント ハブに送信されたメッセージが受信側ウィンドウに表示されます。


## <a name="next-steps"></a>次のステップ
このクイックスタートでは、イベントを非同期的に送受信しました。 イベントを同期的に送受信する方法については、[GitHub の sync_samples ページ](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples/sync_samples)にあるサンプルを参照してください。

GitHub にあるすべての (同期と非同期の両方の) サンプルについては、[Python 用 Azure Event Hubs クライアント ライブラリ サンプル](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples) ページにアクセスしてください。
