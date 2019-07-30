---
title: Python を使用してメッセージを送受信する - Azure Event Hubs | Microsoft Docs
description: Python を使用して、Event Hubs にイベントを送信する方法、Event Hubs からイベントを受信する方法、Event Hubs を通してイベント ストリーミングをキャプチャする方法について説明します。
keywords: ''
documentationcenter: ''
services: event-hubs
author: ShubhaVijayasarathy
manager: ''
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/26/2018
ms.author: shvija
ms.openlocfilehash: f86dc92b4df45119930970acfe9e173f32f894fb
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68356055"
---
# <a name="how-to-use-azure-event-hubs-from-a-python-application"></a>Python アプリケーションから Azure Event Hubs を使用する方法
Azure Event Hubs はビッグ データ ストリーミング プラットフォームであり、毎秒数百万のイベントを受け取って処理できるイベント インジェスト サービスです。 Event Hubs では、分散されたソフトウェアやデバイスから生成されるイベント、データ、またはテレメトリを処理および格納できます。 イベント ハブに送信されたデータは、任意のリアルタイム分析プロバイダーやバッチ処理/ストレージ アダプターを使用して、変換および保存できます。 詳しくは、[Event Hubs の概要](event-hubs-what-is-event-hubs.md)に関する記事をご覧ください。 

この記事では、**Python** で記述されたアプリケーションから次のタスクを実行する方法を示す記事へのリンクが提供されています。

- [イベント ハブにイベントを送信する](#send-events-to-event-hubs)
- [イベント ハブからイベントを受信する](#receive-events-from-event-hubs)
- Azure Storage からキャプチャされたイベント データを読み取る 

## <a name="prerequisites"></a>前提条件
- 次のいずれかのクイック スタートに従って、イベント ハブを作成します。[Azure portal](event-hubs-create.md)、[Azure CLI](event-hubs-quickstart-cli.md)、[Azure PowerShell](event-hubs-quickstart-powershell.md)、[Azure Resource Manager テンプレート](event-hubs-resource-manager-namespace-event-hub.md)。 
- Python 3.4 以降をコンピューターにインストールします。

## <a name="install-python-package"></a>Python パッケージのインストール

Event Hubs 向けの Python パッケージをインストールするには、Python をパス設定した状態でコマンド プロンプトを開き、次のコマンドを実行します。 

```bash
pip install azure-eventhub
```

## <a name="send-events-to-event-hubs"></a>イベントを Event Hubs に送信する
次のコードでは、Python アプリケーションからイベント ハブにイベントを送信する方法を示します。 

1. イベント ハブの URL、キーの名前、キーの値を保持する変数を作成します。 

    ```python
    # Import classes from Event Hubs python package
    from azure.eventhub import EventHubClient, Receiver, Offset
    
    # Address can be in either of these formats:
    # "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<mynamespace>.servicebus.windows.net/myeventhub"
    # "amqps://<mynamespace>.servicebus.windows.net/myeventhub"
    # For example:
    ADDRESS = "amqps://<MyEventHubNamspaceName>.servicebus.windows.net/<MyEventHubName>"
    
    # SAS policy and key are not required if they are encoded in the URL
    USER = "<Name of the access key. Default name: RootManageSharedAccessKey>"
    KEY = "<The access key>"
    ```

2. Event Hubs クライアントを作成し、センダーを追加し、クライアントを実行し、センダーを使用してイベントを送信した後、終了したらクライアントを停止します。 

    ```python
    # Create an Event Hubs client
    client = EventHubClient(ADDRESS, debug=False, username=USER, password=KEY)
    
    # Add a sender to the client
    sender = client.add_sender(partition="0")
    
    # Run the Event Hub client
    client.run()
    
    # Send event to the event hub
    sender.send(EventData("<MyEventData>"))
    
    # Stop the Event Hubs client
    client.stop()
    
    ```

Python で記述されたアプリケーションからイベント ハブにイベントを送信する方法についての完全なチュートリアルは、[こちらの記事](event-hubs-python-get-started-send.md)をご覧ください。

## <a name="receive-events-from-event-hubs"></a>イベント ハブからイベントを受信する
次のコードでは、Python アプリケーションでイベント ハブからのイベントを受信する方法を示します。 

```python

# Create an Event Hubs client
client = EventHubClient(ADDRESS, debug=False, username=USER, password=KEY)

# Add a receiver to the client
receiver = client.add_receiver(
    CONSUMER_GROUP, PARTITION, prefetch=5000, offset=OFFSET)

# Run the Event Hubs client
client.run()

# Receive event data from the event hub
for event_data in receiver.receive(timeout=100):
    last_offset = event_data.offset
    last_sn = event_data.sequence_number
    print("Received: {}, {}".format(last_offset, last_sn))

# Stop the Event Hubs client
client.stop()
```

Python で記述されたアプリケーションでイベント ハブからのイベントを受信する方法についての完全なチュートリアルは、[こちらの記事](event-hubs-python-get-started-receive.md)をご覧ください

## <a name="read-capture-event-data-from-azure-storage"></a>Azure Storage からキャプチャ イベント データを読み取る
次のコードでは、**Azure BLOB ストレージ**に格納されているキャプチャされたイベント データを Python アプリケーションから読み取る方法を示します。イベント ハブの**キャプチャ**機能を有効にするには、「[Azure portal を使用して Event Hubs Capture を有効にする](event-hubs-capture-enable-through-portal.md)」の手順に従います。 その後、コードをテストする前に、イベントを何かイベント ハブに送信します。 

```python
import os
import string
import json
import avro.schema
from avro.datafile import DataFileReader, DataFileWriter
from avro.io import DatumReader, DatumWriter
from azure.storage.blob import BlockBlobService

def processBlob(filename):
    reader = DataFileReader(open(filename, 'rb'), DatumReader())
    dict = {}
    for reading in reader:
        parsed_json = json.loads(reading["Body"])
        if not 'id' in parsed_json:
            return
        if not dict.has_key(parsed_json['id']):
            list = []
            dict[parsed_json['id']] = list
        else:
            list = dict[parsed_json['id']]
            list.append(parsed_json)
    reader.close()
    for device in dict.keys():
        deviceFile = open(device + '.csv', "a")
        for r in dict[device]:
            deviceFile.write(", ".join([str(r[x]) for x in r.keys()])+'\n')

def startProcessing(accountName, key, container):
    print 'Processor started using path: ' + os.getcwd()
    block_blob_service = BlockBlobService(account_name=accountName, account_key=key)
    generator = block_blob_service.list_blobs(container)
    for blob in generator:
        #content_length == 508 is an empty file, so only process content_length > 508 (skip empty files)
        if blob.properties.content_length > 508:
            print('Downloaded a non empty blob: ' + blob.name)
            cleanName = string.replace(blob.name, '/', '_')
            block_blob_service.get_blob_to_path(container, blob.name, cleanName)
            processBlob(cleanName)
            os.remove(cleanName)
        block_blob_service.delete_blob(container, blob.name)
startProcessing('YOUR STORAGE ACCOUNT NAME', 'YOUR KEY', 'capture')
```

Python で記述されたアプリケーションから、Azure BLOB ストレージ内のキャプチャされた Event Hubs データを読み取る方法についての完全なチュートリアルは、[こちらの記事](event-hubs-capture-python.md)をご覧ください

## <a name="github-samples"></a>GitHub のサンプル
他の Python サンプルは、[azure-event-hubs-python Git リポジトリ](https://github.com/Azure/azure-event-hubs-python/)にあります。

## <a name="next-steps"></a>次の手順
「[Event Hubs の機能の概要](event-hubs-features.md)」から始まる概念セクションの記事を読みます。
