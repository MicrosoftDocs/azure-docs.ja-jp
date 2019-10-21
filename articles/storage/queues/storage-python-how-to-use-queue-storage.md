---
title: Python から Azure Queue storage を使用する方法 - Azure Storage
description: Python から Azure Queue サービスを使用して、キューの作成と削除のほか、メッセージの挿入、取得、および削除を行う方法を説明します。
author: mhopkins-msft
ms.service: storage
ms.author: mhopkins
ms.date: 09/17/2019
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: cbrooks
ms.openlocfilehash: 18333d3da0bb444ea236a4fbda4d6b72d7647053
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2019
ms.locfileid: "71059058"
---
# <a name="how-to-use-azure-queue-storage-from-python"></a>Python から Azure Queue storage を使用する方法

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>概要

このガイドでは、Azure Queue ストレージ サービスを使用して一般的なシナリオを実行する方法について説明します。 サンプルは Python で作成され、 [Microsoft Azure Storage SDK for Python]を使用しています。 キュー メッセージの挿入、ピーク、取得、削除と、キューの作成と削除の各シナリオについて説明します。 キューの詳細については、「[次のステップ](#next-steps)」のセクションを参照してください。

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Azure Storage SDK for Python をダウンロードしてインストールする

[Azure Storage SDK for Python](https://github.com/azure/azure-storage-python) には、Python バージョン 2.7、3.3、またはそれ以降が必要です。
 
### <a name="install-via-pypi"></a>PyPi でインストールする

Python Package Index (PyPI) でインストールするには、次のように入力します。

```bash
pip install azure-storage-queue
```

> [!NOTE]
> Azure Storage SDK for Python 0.36 以前のバージョンからアップグレードする場合は、`pip uninstall azure-storage` を使用して以前の SDK をアンインストールしてから、最新のパッケージをインストールします。

別のインストール方法については、[Azure Storage SDK for Python](https://github.com/Azure/azure-storage-python/) をご覧ください。

## <a name="view-the-sample-application"></a>サンプル アプリケーションを表示する

Python を Azure キューと共に使用する方法を示すサンプル アプリケーションを表示して実行するには、「[Azure Storage:Getting Started with Azure Queues in Python (Azure Storage: Python での Azure キューの概要)](https://github.com/Azure-Samples/storage-queue-python-getting-started)」をご覧ください。 

サンプル アプリケーションを実行するには、`azure-storage-queue` パッケージと `azure-storage-common` パッケージの両方がインストールされていることを確認します。

## <a name="create-a-queue"></a>キューを作成する

[QueueService](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice) オブジェクトを使用して、キューを操作できます。 次のコードでは、`QueueService` オブジェクトを作成します。 プログラムを使用して Azure Storage にアクセスするすべての Python ファイルの先頭付近に、次のコードを追加します。

```python
from azure.storage.queue import QueueService
```

次のコードは、ストレージ アカウントの名前とアカウント キーを使用して、`QueueService` オブジェクトを作成します。 *myaccount* と *mykey* は、実際のアカウント名とキーに置き換えてください。

```python
queue_service = QueueService(account_name='myaccount', account_key='mykey')

queue_service.create_queue('taskqueue')
```

## <a name="insert-a-message-into-a-queue"></a>メッセージをキューに挿入する

キューにメッセージを挿入するには、[put_message](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#put-message-queue-name--content--visibility-timeout-none--time-to-live-none--timeout-none-) メソッドを使用し、新しいメッセージを作成してキューに追加します。

```python
queue_service.put_message('taskqueue', u'Hello World')
```

Azure のキュー メッセージはテキストとして格納されます。 バイナリ データを格納する場合は、キューにメッセージを配置する前に、Base64 エンコードおよびデコード関数をキュー サービス オブジェクトに設定します。

```python
# setup queue Base64 encoding and decoding functions
queue_service.encode_function = QueueMessageFormat.binary_base64encode
queue_service.decode_function = QueueMessageFormat.binary_base64decode
```

## <a name="peek-at-the-next-message"></a>次のメッセージをピークする

[peek_messages](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#peek-messages-queue-name--num-messages-none--timeout-none-) メソッドを呼び出すと、キューの先頭にあるメッセージをキューから削除せずにピークできます。 `peek_messages` の既定では、1 つのメッセージを対象としてピークします。

```python
messages = queue_service.peek_messages('taskqueue')
for message in messages:
    print(message.content)
```

## <a name="dequeue-messages"></a>メッセージをデキューする

コードでは、2 つの手順でキューからメッセージを削除します。 [get_messages](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#get-messages-queue-name--num-messages-none--visibility-timeout-none--timeout-none-) を呼び出すと、既定では、キュー内に次のメッセージが取得されます。 `get_messages` から返されたメッセージは、このキューからメッセージを読み取る他のコードから参照できなくなります。 既定では、このメッセージを参照できない状態は 30 秒間続きます。 また、キューからのメッセージの削除を完了するには、[delete_message](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#delete-message-queue-name--message-id--pop-receipt--timeout-none-) を呼び出す必要があります。 2 段階の手順でメッセージを削除するこの方法では、ハードウェアまたはソフトウェアの問題が原因でコードによるメッセージの処理が失敗した場合に、コードの別のインスタンスで同じメッセージを取得し、もう一度処理することができます。 ご自分のコードで、メッセージが処理された直後に `delete_message` を呼び出します。

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)
```

キューからのメッセージの取得をカスタマイズする方法は 2 つあります。 1 つ目の方法では、(最大 32 個の) メッセージのバッチを取得できます。 2 つ目の方法では、コードで各メッセージを完全に処理できるように、非表示タイムアウトの設定を長くまたは短くすることができます。 次のコード例では、`get_messages` メソッドを使用して、1 回の呼び出しで 16 個のメッセージを取得します。 その後、for ループを使用して、各メッセージを処理します。 また、各メッセージの非表示タイムアウトを 5 分に設定します。

```python
messages = queue_service.get_messages(
    'taskqueue', num_messages=16, visibility_timeout=5*60)
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)
```

## <a name="change-the-contents-of-a-queued-message"></a>キューに配置されたメッセージの内容を変更する

キュー内のメッセージの内容をインプレースで変更できます。 メッセージが作業タスクを表している場合は、この機能を使用して、作業タスクの状態を更新できます。 次のコードでは、[update_message](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#update-message-queue-name--message-id--pop-receipt--visibility-timeout--content-none--timeout-none-) メソッドを使用してメッセージを更新します。 表示のタイムアウトは 0 に設定されています。これは、メッセージが即時に表示され、コンテンツが更新されることを示します。

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    queue_service.update_message(
        'taskqueue', message.id, message.pop_receipt, 0, u'Hello World Again')
```

## <a name="get-the-queue-length"></a>キューの長さを取得する

キュー内のメッセージの概数を取得できます。 [get_queue_metadata](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#get-queue-metadata-queue-name--timeout-none-) メソッドを使って、キューについてのメタデータと `approximate_message_count` を返すように Queue サービスに要求します。 Queue サービスが要求に応答した後にメッセージが追加または削除される可能性があるため、取得される結果はおおよその数を示しているだけです。

```python
metadata = queue_service.get_queue_metadata('taskqueue')
count = metadata.approximate_message_count
```

## <a name="delete-a-queue"></a>キューを削除する

キューおよびキューに含まれているすべてのメッセージを削除するには、[delete_queue](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#delete-queue-queue-name--fail-not-exist-false--timeout-none-) メソッドを呼び出します。

```python
queue_service.delete_queue('taskqueue')
```

## <a name="next-steps"></a>次の手順

これで、Queue Storage の基本を学習できました。さらに詳細な情報が必要な場合は、次のリンク先を参照してください。

* [Azure キューの Python API リファレンス](/python/api/azure-storage-queue)
* [Python デベロッパー センター](https://azure.microsoft.com/develop/python/)
* [Azure Storage Services REST API (Azure Storage サービスの REST API)](https://msdn.microsoft.com/library/azure/dd179355)

[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/
[Microsoft Azure Storage SDK for Python]: https://github.com/Azure/azure-storage-python
