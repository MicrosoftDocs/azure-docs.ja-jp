---
title: Python アプリから Azure Event Hubs でキャプチャされたデータを読み取る (最新)
description: この記事では、イベント ハブに送信されたデータをキャプチャし、キャプチャされたイベント データを Azure Storage アカウントから読み取る Python コードを記述する方法について説明します。
ms.topic: quickstart
ms.date: 01/04/2021
ms.openlocfilehash: acc2ce04add5fd837e9edc789e9616a9f04fb4b9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97883199"
---
# <a name="capture-event-hubs-data-in-azure-storage-and-read-it-by-using-python-azure-eventhub"></a>Event Hubs データを Azure Storage にキャプチャし、Python (azure-eventhub) を使用してそれを読み取る

イベント ハブに送信されたデータが Azure ストレージ アカウントあるいは Azure Data Lake Storage Gen 1 または Gen 2 にキャプチャされるようにイベント ハブを構成できます。 この記事では、イベントをイベント ハブに送信し、キャプチャされたデータを **Azure Blob Storage** から読み取る Python コードを記述する方法を示します。 この機能の詳細については、[Event Hubs Capture 機能の概要](event-hubs-capture-overview.md)に関するページを参照してください。

このクイックスタートでは、[Azure Python SDK](https://azure.microsoft.com/develop/python/) を使用して、Capture の機能を試してみます。 *sender.py* アプリは、シミュレートされた環境のテレメトリを JSON 形式でイベント ハブに送信します。 イベント ハブは、Capture 機能を使用して、このデータを数回に分けて Blob Storage に書き込むように構成されています。 *capturereader.py* アプリは、こうした BLOB を読み取り、デバイスごとに追加ファイルを作成します。 その後、アプリはデータを CSV ファイルに書き込みます。

このクイック スタートでは次の作業を行います。 

> [!div class="checklist"]
> * Azure portal で Azure Blob Storage アカウントとコンテナーを作成します。
> * Azure portal を使用して Event Hubs 名前空間を作成します。
> * Capture 機能が有効になっているイベント ハブを作成し、それをストレージ アカウントに接続します。
> * Python スクリプトを使用して、利用するイベント ハブへデータを送信します。
> * 別の Python スクリプトを使用して、Event Hubs Capture からファイルを読み取り、処理します。

## <a name="prerequisites"></a>前提条件

- PIP と次のパッケージがインストールされた Python。 この記事のコードは、これらのバージョンに対してテスト済みです。 
    - Python 3.7
    - azure-eventhub 5.2.0
    - azure-storage-blob 12.6.0
    - avro-python3 1.10.1
- Azure サブスクリプション。 お持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。  
- アクティブな Event Hubs 名前空間とイベント ハブ。
[Event Hubs 名前空間を作成し、その名前空間内にイベント ハブを作成します](event-hubs-create.md)。 Event Hubs 名前空間の名前、イベント ハブの名前、および名前空間のプライマリ アクセス キーを記録しておいてください。 アクセス キーの取得については、「[Event Hubs の接続文字列の取得](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)」を参照してください。 既定のキー名は *RootManageSharedAccessKey* です。 このクイックスタートでは、プライマリ キーのみ必要となります。 接続文字列は必要ありません。  
- Azure Storage アカウント、ストレージ アカウントの BLOB コンテナー、ストレージ アカウントへの接続文字列。 これらの項目がない場合は、次の手順を実行します。  
    1. [Azure Storage アカウントを作成する](../storage/common/storage-account-create.md?tabs=azure-portal)  
    1. [ストレージ アカウントに BLOB コンテナーを作成する](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)  
    1. [ストレージ アカウントへの接続文字列を取得する](../storage/common/storage-configure-connection-string.md)

    接続文字列とコンテナー名を記録しておいてください。後でこのクイックスタートの中で使用します。  
- イベント ハブの Capture 機能の有効化。 [Azure portal を使用して Event Hubs Capture を有効にする方法](event-hubs-capture-enable-through-portal.md)に関するページの手順に従ってください。 前の手順で作成したストレージ アカウントと BLOB コンテナーを選択します。 この機能はまた、イベント ハブの作成時に有効にすることもできます。  

## <a name="create-a-python-script-to-send-events-to-your-event-hub"></a>イベントをイベント ハブに送信する Python スクリプトを作成する
このセクションでは、イベント ハブに 200 のイベント (10 デバイス * 20 イベント) を送信する Python スクリプトを作成します。 これらのイベントは、JSON 形式で送信されたサンプルの環境測定値です。 

1. [Visual Studio Code][Visual Studio Code]など、お使いの Python エディターを開きます。
2. *sender.py* という名前のスクリプトを作成します。 
3. 次のコードを "*sender.py*" に貼り付けます。 
   
    ```python
    import time
    import os
    import uuid
    import datetime
    import random
    import json
    
    from azure.eventhub import EventHubProducerClient, EventData
    
    # This script simulates the production of events for 10 devices.
    devices = []
    for x in range(0, 10):
        devices.append(str(uuid.uuid4()))
    
    # Create a producer client to produce and publish events to the event hub.
    producer = EventHubProducerClient.from_connection_string(conn_str="EVENT HUBS NAMESAPCE CONNECTION STRING", eventhub_name="EVENT HUB NAME")
    
    for y in range(0,20):    # For each device, produce 20 events. 
        event_data_batch = producer.create_batch() # Create a batch. You will add events to the batch later. 
        for dev in devices:
            # Create a dummy reading.
            reading = {'id': dev, 'timestamp': str(datetime.datetime.utcnow()), 'uv': random.random(), 'temperature': random.randint(70, 100), 'humidity': random.randint(70, 100)}
            s = json.dumps(reading) # Convert the reading into a JSON string.
            event_data_batch.add(EventData(s)) # Add event data to the batch.
        producer.send_batch(event_data_batch) # Send the batch of events to the event hub.
    
    # Close the producer.    
    producer.close()
    ```
4. スクリプト内の次の値を置き換えます。  
    * `EVENT HUBS NAMESPACE CONNECTION STRING` を Event Hubs 名前空間への接続文字列に置き換えます。  
    * `EVENT HUB NAME` をイベント ハブの名前に置き換えます。  
5. イベントをイベント ハブに送信するスクリプトを実行します。  
6. Azure portal で、イベント ハブがメッセージを受信したことを確認できます。 **[メトリック]** セクションで **[メッセージ]** ビューに切り替えます。 ページを最新の情報に更新して、グラフを更新します。 メッセージが受信されたとページに表示されるまでに数秒かかることがあります。 

    [![イベント ハブがメッセージを受信したことを確認する](./media/get-started-capture-python-v2/messages-portal.png)](./media/get-started-capture-python-v2/messages-portal.png#lightbox)

## <a name="create-a-python-script-to-read-your-capture-files"></a>Capture ファイルを読み取る Python スクリプトを作成する
この例では、キャプチャされたデータは Azure Blob Storage に格納されます。 このセクションのスクリプトは、Azure Storage アカウントからキャプチャ データ ファイルを読み取り、ユーザーが簡単に開いて確認できる CSV ファイルを生成します。 アプリケーションの現在の作業ディレクトリ内に 10 個のファイルが表示されます。 これらのファイルには、10 個のデバイスの環境測定値が含まれています。 

1. Python エディターで、*capturereader.py* という名前のスクリプトを作成します。 このスクリプトはキャプチャされたファイルを読み取り、それぞれのデバイスにのみデータを書き込むようにデバイスごとのファイルを作成します。
2. 次のコードを "*capturereader.py*" に貼り付けます。 
   
    ```python
    import os
    import string
    import json
    import uuid
    import avro.schema
    
    from azure.storage.blob import ContainerClient, BlobClient
    from avro.datafile import DataFileReader, DataFileWriter
    from avro.io import DatumReader, DatumWriter
    
    
    def processBlob2(filename):
        reader = DataFileReader(open(filename, 'rb'), DatumReader())
        dict = {}
        for reading in reader:
            parsed_json = json.loads(reading["Body"])
            if not 'id' in parsed_json:
                return
            if not parsed_json['id'] in dict:
                list = []
                dict[parsed_json['id']] = list
            else:
                list = dict[parsed_json['id']]
                list.append(parsed_json)
        reader.close()
        for device in dict.keys():
            filename = os.getcwd() + '\\' + str(device) + '.csv'
            deviceFile = open(filename, "a")
            for r in dict[device]:
                deviceFile.write(", ".join([str(r[x]) for x in r.keys()])+'\n')
    
    def startProcessing():
        print('Processor started using path: ' + os.getcwd())
        # Create a blob container client.
        container = ContainerClient.from_connection_string("AZURE STORAGE CONNECTION STRING", container_name="BLOB CONTAINER NAME")
        blob_list = container.list_blobs() # List all the blobs in the container.
        for blob in blob_list:
            # Content_length == 508 is an empty file, so process only content_length > 508 (skip empty files).        
            if blob.size > 508:
                print('Downloaded a non empty blob: ' + blob.name)
                # Create a blob client for the blob.
                blob_client = ContainerClient.get_blob_client(container, blob=blob.name)
                # Construct a file name based on the blob name.
                cleanName = str.replace(blob.name, '/', '_')
                cleanName = os.getcwd() + '\\' + cleanName 
                with open(cleanName, "wb+") as my_file: # Open the file to write. Create it if it doesn't exist. 
                    my_file.write(blob_client.download_blob().readall()) # Write blob contents into the file.
                processBlob2(cleanName) # Convert the file into a CSV file.
                os.remove(cleanName) # Remove the original downloaded file.
                # Delete the blob from the container after it's read.
                container.delete_blob(blob.name)
    
    startProcessing()    
    ```
3. `AZURE STORAGE CONNECTION STRING` を Azure Storage アカウントへの接続文字列に置き換えます。 このクイックスタートで作成したコンテナーの名前は *capture* です。 コンテナーに別の名前を使用した場合は、*capture* をストレージ アカウント内のコンテナーの名前に置き換えます。 

## <a name="run-the-scripts"></a>スクリプトの実行
1. Python をパス設定した状態でコマンド プロンプトを開き、これらのコマンドを実行してPython の前提条件となるパッケージをインストールします:
   
   ```
   pip install azure-storage-blob
   pip install azure-eventhub
   pip install avro-python3
   ```

    > [!NOTE]
    > この記事のコードは、これらのバージョンに対してテスト済みです。 
    > - Python 3.7
    > - azure-eventhub 5.2.0
    > - azure-storage-blob 12.6.0
    > - avro-python3 1.10.1
2. *sender.py* と *capturereader.py* の保存先ディレクトリに移動して、次のコマンドを実行します。
   
   ```
   python sender.py
   ```
   
   このコマンドにより、新しい Python プロセスが起動して sender を実行します。
3. capture が実行されるまで数分待って、元のコマンド ウィンドウに、次のコマンドを入力します。
   
   ```
   python capturereader.py
   ```

   このキャプチャ プロセッサでは、ローカル ディレクトリが使用され、ストレージ アカウントとコンテナーからすべての BLOB がダウンロードされます。 空ではないものがすべて処理され、結果が CSV ファイルとしてローカル ディレクトリに書き込まれます。

## <a name="next-steps"></a>次のステップ
[GitHub の Python サンプル](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples)をご覧ください。 


[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Capture]: event-hubs-capture-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]:../storage/common/storage-create-storage-account.md
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: ./event-hubs-about.md
