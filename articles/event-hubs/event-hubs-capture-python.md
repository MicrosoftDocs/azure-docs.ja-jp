---
title: Python アプリからキャプチャしたデータを読み取る - Azure Event Hubs |Microsoft Docs
description: Azure Python SDK を用いて、Event Hubs Capture 機能の使用方法を示すサンプル。
services: event-hubs
documentationcenter: ''
author: shubhavijayasarathy
manager: timlt
editor: ''
ms.assetid: bdff820c-5b38-4054-a06a-d1de207f01f6
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 639bc4ff9c69bca3d5f8bca6967bfc3e8e6a13d4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "60822414"
---
# <a name="event-hubs-capture-walkthrough-python"></a>Event Hubs Capture チュートリアル:Python

Capture は Azure Event Hubs の機能です。 これを使用すると、任意の Azure BLOB ストレージ アカウントに、イベント ハブ内のストリーミング データを自動的に配布できます。 この機能により、リアルタイムのストリーミング データを容易にバッチ処理することができます。 この記事では、Event Hubs Capture を Python で使用する方法について説明します。 Event Hubs Capture の詳細については、[概要の記事](event-hubs-capture-overview.md)をご覧ください。

このサンプルでは、[Azure Python SDK](https://azure.microsoft.com/develop/python/) を使用して、Capture の機能を試してみます。 Sender.py プログラムは、シミュレートされた環境のテレメトリを JSON 形式で Event Hubs に送信します。 イベント ハブは、Capture 機能を使用して、このデータを数回に分けて Blob Storage に書き込むように構成されています。 capturereader.py アプリは、こうした BLOB を読み取り、デバイスごとに追加ファイルを作成します。 その後、アプリはデータを .csv ファイルに書き込みます。

## <a name="what-youll-accomplish"></a>作業内容

1. Azure Portal を使用して Azure BLOB ストレージ アカウントと BLOB コンテナーを作成します。
2. Azure Portal を使用して Event Hubs 名前空間を作成します。
3. Azure Portal を使用して Capture 機能が有効なイベント ハブを作成します。
4. Python スクリプトを使用してイベント ハブへデータを送信します。
5. キャプチャからファイルを読み取り、別の Python スクリプトを使用して処理します。

## <a name="prerequisites"></a>前提条件

- Python 2.7.x
- Azure サブスクリプション。 お持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。
- アクティブな [Event Hubs 名前空間とイベント ハブ](event-hubs-create.md)。 
- 次の手順に従って、イベント ハブの**キャプチャ**機能を有効にする:[Azure Portal を使用して Event Hubs Capture を有効にする](event-hubs-capture-enable-through-portal.md)

## <a name="create-an-azure-blob-storage-account"></a>Azure BLOB ストレージ アカウントを作成する
1. [Azure Portal][Azure portal] にサインインします。
2. ポータルの左側のウィンドウで **[新規]**  >  **[ストレージ]**  >  **[ストレージ アカウント]** の順に選択します。
3. **[ストレージ アカウントの作成]** ウィンドウで情報を選択し、 **[作成]** を選択します。
   
   ![[ストレージ アカウントの作成] ウィンドウ][1]
4. **[デプロイメントが成功しました]** メッセージが表示されたら、新しいストレージ アカウントの名前を選択し、 **[要点]** ウィンドウで **[BLOB]** を選択します。 **[Blob service]** ウィンドウが開いたら、上部にある **[+ コンテナー]** を選択します。 コンテナーの**キャプチャ**に名前を付け、 **[Blob service]** ウィンドウを閉じます。
5. 左側のウィンドウにある **[アクセス キー]** を選択し、ストレージ アカウントの名前と **[key1]** の値をコピーします。 これらの値をメモ帳などに一時的に保存します。

## <a name="create-a-python-script-to-send-events-to-your-event-hub"></a>イベントをイベント ハブに送信する Python スクリプトを作成する
1. [Visual Studio Code][Visual Studio Code] など、お使いの Python エディターを開きます。
2. **sender.py**という名前のスクリプトを作成します。 このスクリプトでは、200 のイベントをイベント ハブに送信します。 これらは、JSON で送信される単純な環境の測定値です。
3. 以下のコードを sender.py に貼り付けます:
   
   ```python
   import uuid
   import datetime
   import random
   import json
   from azure.servicebus.control_client import ServiceBusService
   
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
4. 名前空間の名前、キーの値、および Event Hubs の名前空間を作成したときに取得したイベント ハブの名前を使用するように、上記のコードを更新します。

## <a name="create-a-python-script-to-read-your-capture-files"></a>Capture ファイルを読み取る Python スクリプトを作成する

1. ウィンドウに情報を入力し、 **[作成]** を選択します。
2. **capturereader.py** という名前のスクリプトを作成します。 このスクリプトはキャプチャされたファイルを読み取り、それぞれのデバイスにのみデータを書き込むようにデバイスごとのファイルを作成します。
3. 以下のコードを capturereader.py に貼り付けます。
   
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
4. `startProcessing` の呼び出しで、ストレージ アカウントの名前とキーの適切な値を貼り付けます。

## <a name="run-the-scripts"></a>スクリプトの実行
1. Python をパス設定した状態でコマンド プロンプトを開き、これらのコマンドを実行してPython の前提条件となるパッケージをインストールします:
   
   ```
   pip install azure-storage
   pip install azure-servicebus
   pip install avro
   ```
   
   **azure-storage** または **azure** の以前のバージョンを使用する場合は、 **--upgrade** オプションの使用が必要になることがあります。
   
   また、次のコマンドの実行が必要になる場合もあります (ほとんどのシステムでは必要ありません):
   
   ```
   pip install cryptography
   ```
2. ディレクトリを sender.py と capturereader.py を保存した場所に変更し、このコマンドを実行します:
   
   ```
   start python sender.py
   ```
   
   このコマンドにより、新しい Python プロセスが起動して sender を実行します。
3. キャプチャが実行されるまで、数分間待ちます。 元のコマンド ウィンドウに、次のコマンドを入力します。
   
   ```
   python capturereader.py
   ```

   このキャプチャ プロセッサでは、ローカル ディレクトリが使用され、ストレージ アカウントとコンテナーからすべての BLOB がダウンロードされます。 空ではないものがすべて処理され、結果が .csv ファイルとしてローカル ディレクトリに書き込まれます。

## <a name="next-steps"></a>次の手順

Event Hubs の詳細については、次のリンクを使用してください。

* [Event Hubs Capture の概要][Overview of Event Hubs Capture]
* [Event Hubs を使用するサンプル アプリケーション](https://github.com/Azure/azure-event-hubs/tree/master/samples)
* [Event Hubs の概要][Event Hubs overview]

[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Capture]: event-hubs-capture-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]:../storage/common/storage-create-storage-account.md
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
