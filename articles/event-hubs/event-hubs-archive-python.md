---
title: "Azure Event Hubs Archive チュートリアル | Microsoft Docs"
description: "Azure Python SDK を使用して、Event Hubs Archive 機能を使用する方法を試すサンプル。"
services: event-hubs
documentationcenter: 
author: djrosanova
manager: timlt
editor: 
ms.assetid: bdff820c-5b38-4054-a06a-d1de207f01f6
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/12/2017
ms.author: darosa;sethm
translationtype: Human Translation
ms.sourcegitcommit: 25dd25d8f8f0388ed7ef11bb26344ad7199fde2e
ms.openlocfilehash: 3f0487fba592426c835d81a46a752697ecf34d8b


---
# <a name="event-hubs-archive-walkthrough-python"></a>Event Hubs Archive チュートリアル: Python
Event Hubs Archive は Event Hubs の新機能で、任意の Azure Blob Storage アカウントに、Event Hub 内のストリーム データを自動的に配布できます。 これにより、ストリーミング データをリアルタイムで容易にバッチ処理することができます。 この記事では、Python で Event Hubs Archive を使用する方法について説明します。 Event Hubs Archive の詳細については、「 [概要の記事](event-hubs-archive-overview.md)」を参照してください。

このサンプルでは、[Azure Python SDK](https://azure.microsoft.com/develop/python/) を使用して、Archive の機能を試してみます。 Sender.py プログラムは、シミュレートされた環境のテレメトリを JSON 形式で Event Hubs に送信します。 Event Hub を構成して Archive 機能を使用することにより、このデータをバッチ内の BLOB ストレージに書き込みます。 Archivereader.py アプリはこれらの BLOB を読み込み、デバイスごとに追加ファイルを作成し、データを .csv ファイルに書き込みます。

作業内容

1. Azure Portal を使用して Azure Blob Storage アカウントと BLOB コンテナーを作成します。
2. Azure Portal を使用して Event Hub 名前空間を作成します。
3. Azure Portal を使用して Archive 機能が有効な Event Hub を作成します。
4. Python スクリプトを使用して Event Hub へデータを送信します。
5. アーカイブからファイルを読み取り、別の Python スクリプトで処理します。

前提条件

- Python 2.7.x
- Azure サブスクリプション
- アクティブな [Event Hubs 名前空間と Event Hub。](event-hubs-create.md)

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="create-an-azure-storage-account"></a>Azure Storage アカウントの作成
1. [Azure Portal][Azure portal] にログオンします。
2. ポータルの左側のナビゲーション ウィンドウで **[新規]** をクリックし、**[ストレージ]**、**[ストレージ アカウント]** の順にクリックします。
3. ストレージ アカウントのブレードで、フィールドを入力し、**[作成]** をクリックします。
   
   ![][1]
4. **[デプロイメントが成功しました]** メッセージが表示されたら、新しいストレージ アカウント名をクリックし、**[要点]** ブレードで **[BLOB]** をクリックします。 **[Blob service]** ブレードが開いたら、上部にある **[+ Container (+ コンテナー)]** をクリックします。 コンテナーの **[アーカイブ]** に名前を付け、**[Blob service]** ブレードを閉じます。
5. 左のブレードにある **[アクセス キー]** をクリックして、ストレージ アカウントの名前と **[key1]** の値をコピーします。 これらの値をメモ帳などに一時的に保存します。

## <a name="create-a-python-script-to-send-events-to-your-event-hub"></a>イベントを Event Hub に送信する Python スクリプトを作成する
1. [Visual Studio Code][Visual Studio Code] など、お使いの Python エディターを開きます。
2. **sender.py**という名前のスクリプトを作成します。 このスクリプトでは、200 のイベントを Event Hub に送信します。 これらは、JSON で送信される単純な環境の測定値です。
3. 以下のコードを sender.py に貼り付けます:
   
   ```python
   import uuid
   import datetime
   import random
   import json
   from azure.servicebus import ServiceBusService
   
   sbs = ServiceBusService(service_namespace='INSERT YOUR NAMESPACE NAME', shared_access_key_name='RootManageSharedAccessKey', shared_access_key_value='INSERT YOUR KEY')
   devices = []
   for x in range(0, 10):
       devices.append(str(uuid.uuid4()))
   
   for y in range(0,20):
       for dev in devices:
           reading = {'id': dev, 'timestamp': str(datetime.datetime.utcnow()), 'uv': random.random(), 'temperature': random.randint(70, 100), 'humidity': random.randint(70, 100)}
           s = json.dumps(reading)
           sbs.send_event('INSERT YOUR EVENT HUB NAME', s)
       print y
   ```
4. 名前空間の名前、キーの値、および Event Hubs の名前空間を作成したときに取得した Event Hub の名前を使用するように、上記のコードを更新します。

## <a name="create-a-python-script-to-read-your-archive-files"></a>アーカイブ ファイルを読み取る Python スクリプトを作成する
1. ブレードに入力し、 **[作成]**をクリックします。
2. **archivereader.py**と言う名前のスクリプトを作成します。 このスクリプトはアーカイブ ファイルを読み取り、それぞれのデバイスにのみデータを書き込むようにデバイスごとのファイルを作成します。
3. 以下のコードを archivereader.py に貼り付けます:
   
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
           if blob.properties.content_length != 0:
               print('Downloaded a non empty blob: ' + blob.name)
               cleanName = string.replace(blob.name, '/', '_')
               block_blob_service.get_blob_to_path(container, blob.name, cleanName)
               processBlob(cleanName)
               os.remove(cleanName)
           block_blob_service.delete_blob(container, blob.name)
   startProcessing('YOUR STORAGE ACCOUNT NAME', 'YOUR KEY', 'archive')
   ```
4. 呼び出しでのストレージ アカウント名とキーには必ず適切な値を `startProcessing`に貼り付けます。

## <a name="run-the-scripts"></a>スクリプトの実行
1. Python をパス設定した状態でコマンド プロンプトを開き、これらのコマンドを実行してPython の前提条件となるパッケージをインストールします:
   
   ```
   pip install azure-storage
   pip install azure-servicebus
   pip install avro
   ```
   
   azure ストレージまたは azure の以前のバージョンを使用する場合は、 **--アップグレード** オプションを使用する場合があります。
   
   また、次のことを実行する場合もあります (ほとんどのシステムでは必要ありません):
   
   ```
   pip install cryptography
   ```
2. ディレクトリを、sender.py と archivereader.py を保存した場所に変更し、このコマンドを実行します:
   
   ```
   start python sender.py
   ```
   
   これにより、新しい Python プロセスが起動して sender を実行します。
3. アーカイブが実行されるまで、数分間待ちます。 元のコマンド ウィンドウに、次のコマンドを入力します。
   
    ```
    python archivereader.py
    ```

    このアーカイブ プロセッサでは、ローカルのディレクトリを使用して、ストレージ アカウントとコンテナーからすべての BLOB をダウンロードします。 空ではないものがすべて処理され、結果が .csv ファイルとしてローカル ディレクトリに書き込まれます。

## <a name="next-steps"></a>次のステップ
Event Hubs の詳細については、次のリンク先を参照してください:

* [Event Hubs Archive の概要][Overview of Event Hubs Archive]
* [Event Hubs を使用する完全なサンプル アプリケーション][sample application that uses Event Hubs]
* [Event Hubs でのイベント処理のスケールアウト][Scale out Event Processing with Event Hubs] サンプル
* [Event Hubs の概要][Event Hubs overview]

[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Archive]: event-hubs-archive-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]: ../storage/storage-create-storage-account.md
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: event-hubs-overview.md
[sample application that uses Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Scale out Event Processing with Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3



<!--HONumber=Feb17_HO1-->


