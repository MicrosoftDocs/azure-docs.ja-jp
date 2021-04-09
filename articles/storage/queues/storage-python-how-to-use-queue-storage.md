---
title: Python から Azure Queue Storage を使用する方法
description: Python から Azure Queue Storage を使用して、キューの作成と削除のほか、メッセージの挿入、取得、および削除を行う方法を説明します。
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: dineshm
ms.date: 02/16/2021
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.custom: seo-javascript-october2019, devx-track-python
ms.openlocfilehash: 8c30d05cf49162155f84961e1fd8a32361444ba5
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653263"
---
# <a name="how-to-use-azure-queue-storage-from-python"></a>Python から Azure Queue Storage を使用する方法

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

## <a name="overview"></a>概要

この記事では、Azure Queue Storage サービスを使用した一般的なシナリオについて説明します。 紹介するシナリオには、キュー メッセージの挿入、ピーク、取得、削除が含まれます。 キューを作成および削除するためのコードについても説明します。

この記事のサンプルは Python で作成され、[Python パッケージ用 Azure Queue Storage クライアント ライブラリ](https://github.com/azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-queue)を使用しています。 キューの詳細については、「[次のステップ](#next-steps)」のセクションを参照してください。

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Azure Storage SDK for Python をダウンロードしてインストールする

[Azure Storage SDK for Python](https://github.com/azure/azure-storage-python) には、Python v2.7、v3.3、またはそれ以降が必要です。

### <a name="install-via-pypi"></a>PyPI でインストールする

Python Package Index (PyPI) でインストールするには、次のように入力します。

# <a name="python-v12"></a>[Python v12](#tab/python)

```console
pip install azure-storage-queue
```

# <a name="python-v2"></a>[Python v2](#tab/python2)

```console
pip install azure-storage-queue==2.1.0
```

---

> [!NOTE]
> Azure Storage SDK for Python v0.36 以前からアップグレードする場合は、`pip uninstall azure-storage` を使用して以前の SDK をアンインストールしてから、最新のパッケージをインストールします。

別のインストール方法については、[Azure SDK for Python](https://github.com/Azure/Azure-SDK-for-Python)をご覧ください。

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="configure-your-application-to-access-queue-storage"></a>Queue ストレージにアクセスするようにアプリケーションを構成する

# <a name="python-v12"></a>[Python v12](#tab/python)

[`QueueClient`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient) オブジェクトを使用して、キューを操作できます。 プログラムを使用して Azure キューにアクセスするすべての Python ファイルの先頭付近に次のコードを追加します。

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_ImportStatements":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

[`QueueService`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true) オブジェクトを使用して、キューを操作できます。 次のコードでは、`QueueService` オブジェクトを作成します。 プログラムを使用して Azure Storage にアクセスするすべての Python ファイルの先頭付近に次のコードを追加します。

```python
from azure.storage.queue import (
        QueueService,
        QueueMessageFormat
)

import os, uuid
```

---

`os` パッケージは、環境変数を取得するためのサポートを提供します。 `uuid` パッケージは、キュー名の一意の識別子を生成するためのサポートを提供します。

## <a name="create-a-queue"></a>キューを作成する

接続文字列は、前の手順で設定した `AZURE_STORAGE_CONNECTION_STRING` 環境変数から取得されます。

# <a name="python-v12"></a>[Python v12](#tab/python)

次のコードは、ストレージ接続文字列を使用して `QueueClient` オブジェクトを作成します。

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_CreateQueue":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

次のコードは、ストレージ接続文字列を使用して `QueueService` オブジェクトを作成します。

```python
# Retrieve the connection string from an environment
# variable named AZURE_STORAGE_CONNECTION_STRING
connect_str = os.getenv("AZURE_STORAGE_CONNECTION_STRING")

# Create a unique name for the queue
queue_name = "queue-" + str(uuid.uuid4())

# Create a QueueService object which will
# be used to create and manipulate the queue
print("Creating queue: " + queue_name)
queue_service = QueueService(connection_string=connect_str)

# Create the queue
queue_service.create_queue(queue_name)
```

---

Azure のキュー メッセージはテキストとして格納されます。 バイナリ データを格納する場合は、キューにメッセージを配置する前に、Base64 エンコードおよびデコード関数を設定します。

# <a name="python-v12"></a>[Python v12](#tab/python)

クライアント オブジェクトの作成時に Base64 エンコードおよびデコード関数を構成します。

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_EncodeMessage":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Queue Storage オブジェクトで Base64 エンコードおよびデコード関数を構成します。

```python
# Setup Base64 encoding and decoding functions
queue_service.encode_function = QueueMessageFormat.binary_base64encode
queue_service.decode_function = QueueMessageFormat.binary_base64decode
```

---

## <a name="insert-a-message-into-a-queue"></a>メッセージをキューに挿入する

# <a name="python-v12"></a>[Python v12](#tab/python)

メッセージをキューに挿入するには、[`send_message`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#send-message-content----kwargs-) メソッドを使用します。

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_AddMessage":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

キューにメッセージを挿入するには、[`put_message`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true#put-message-queue-name--content--visibility-timeout-none--time-to-live-none--timeout-none-) メソッドを使い、新しいメッセージを作成してキューに追加します。

```python
message = u"Hello, World"
print("Adding message: " + message)
queue_service.put_message(queue_name, message)
```

---

## <a name="peek-at-messages"></a>メッセージをピークする

# <a name="python-v12"></a>[Python v12](#tab/python)

[`peek_messages`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#peek-messages-max-messages-none----kwargs-) メソッドを呼び出せば、メッセージをキューから削除せずにピークできます。 既定では、このメソッドは 1 つのメッセージを対象としてピークします。

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_PeekMessage":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

[`peek_messages`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true#peek-messages-queue-name--num-messages-none--timeout-none-) メソッドを呼び出せば、メッセージをキューから削除せずにピークできます。 既定では、このメソッドは 1 つのメッセージを対象としてピークします。

```python
messages = queue_service.peek_messages(queue_name)

for peeked_message in messages:
    print("Peeked message: " + peeked_message.content)
```

---

## <a name="change-the-contents-of-a-queued-message"></a>キューに配置されたメッセージの内容を変更する

キュー内のメッセージの内容をインプレースで変更できます。 メッセージがタスクを表している場合は、この機能を使用して、タスクの状態を更新できます。

# <a name="python-v12"></a>[Python v12](#tab/python)

次のコードでは、[`update_message`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#update-message-message--pop-receipt-none--content-none----kwargs-) メソッドを使用してメッセージを更新します。 表示のタイムアウトは 0 に設定されています。これは、メッセージが即時に表示され、コンテンツが更新されることを示します。

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_ChangeMessage":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

次のコードでは、[`update_message`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true#update-message-queue-name--message-id--pop-receipt--visibility-timeout--content-none--timeout-none-) メソッドを使用してメッセージを更新します。 表示のタイムアウトは 0 に設定されています。これは、メッセージが即時に表示され、コンテンツが更新されることを示します。

```python
messages = queue_service.get_messages(queue_name)

for message in messages:
    queue_service.update_message(
        queue_name, message.id, message.pop_receipt, 0, u"Hello, World Again")
```

---

## <a name="get-the-queue-length"></a>キューの長さを取得する

キュー内のメッセージの概数を取得できます。

# <a name="python-v12"></a>[Python v12](#tab/python)

[get_queue_properties](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#get-queue-properties---kwargs-) メソッドからは、`approximate_message_count` を含む、キューのプロパティが返されます。

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_GetQueueLength":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

[`get_queue_metadata`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true#get-queue-metadata-queue-name--timeout-none-) メソッドからは、`approximate_message_count` を含む、キューのプロパティが返されます。

```python
metadata = queue_service.get_queue_metadata(queue_name)
count = metadata.approximate_message_count
print("Message count: " + str(count))
```

---

サービスが要求に応答した後にメッセージが追加または削除される可能性があるため、取得される結果はおおよその数を示しているだけです。

## <a name="dequeue-messages"></a>メッセージをデキューする

キューからのメッセージの削除は、2 段階の手順で行います。 コードでのメッセージの処理が失敗した場合でも、この 2 段階のプロセスにより、同じメッセージを取得して再試行できます。 メッセージが正常に処理された後に `delete_message` を呼び出します。

# <a name="python-v12"></a>[Python v12](#tab/python)

[receive_messages](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#receive-messages---kwargs-) を呼び出すと、既定では、キュー内に次のメッセージが取得されます。 `receive_messages` から返されたメッセージは、このキューからメッセージを読み取る他のコードから参照できなくなります。 既定では、このメッセージを参照できない状態は 30 秒間続きます。 また、キューからのメッセージの削除を完了するには、[delete_message](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#delete-message-message--pop-receipt-none----kwargs-) を呼び出す必要があります。

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_DequeueMessages":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

[get_messages](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true#get-messages-queue-name--num-messages-none--visibility-timeout-none--timeout-none-) を呼び出すと、既定では、キュー内に次のメッセージが取得されます。 `get_messages` から返されたメッセージは、このキューからメッセージを読み取る他のコードから参照できなくなります。 既定では、このメッセージを参照できない状態は 30 秒間続きます。 また、キューからのメッセージの削除を完了するには、[delete_message](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true#delete-message-queue-name--message-id--pop-receipt--timeout-none-) を呼び出す必要があります。

```python
messages = queue_service.get_messages(queue_name)

for message in messages:
    print("Deleting message: " + message.content)
    queue_service.delete_message(queue_name, message.id, message.pop_receipt)
```

---

キューからのメッセージの取得をカスタマイズする方法は 2 つあります。 1 つ目の方法では、(最大 32 個の) メッセージのバッチを取得できます。 2 つ目の方法では、コードで各メッセージを完全に処理できるように、非表示タイムアウトの設定を長くまたは短くすることができます。

# <a name="python-v12"></a>[Python v12](#tab/python)

次のコード例では、[`receive_messages`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#receive-messages---kwargs-) メソッドを使用して、メッセージを一括して取得します。 次に、入れ子になった `for` ループを使用して、各バッチ内の各メッセージを処理します。 また、各メッセージの非表示タイムアウトを 5 分に設定します。

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_DequeueByPage":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

次のコード例では、[`get_messages`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true#get-messages-queue-name--num-messages-none--visibility-timeout-none--timeout-none-) メソッドを使用して、1 回の呼び出しで 16 個のメッセージを取得します。 その後、`for` ループを使用して、各メッセージを処理します。 また、各メッセージの非表示タイムアウトを 5 分に設定します。

```python
messages = queue_service.get_messages(queue_name, num_messages=16, visibility_timeout=5*60)

for message in messages:
    print("Deleting message: " + message.content)
    queue_service.delete_message(queue_name, message.id, message.pop_receipt)
```

---

## <a name="delete-a-queue"></a>キューを削除する

# <a name="python-v12"></a>[Python v12](#tab/python)

キューおよびキューに含まれているすべてのメッセージを削除するには、[`delete_queue`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#delete-queue---kwargs-) メソッドを呼び出します。

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_DeleteQueue":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

キューおよびキューに含まれているすべてのメッセージを削除するには、[`delete_queue`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true#delete-queue-queue-name--fail-not-exist-false--timeout-none-) メソッドを呼び出します。

```python
print("Deleting queue: " + queue_name)
queue_service.delete_queue(queue_name)
```

---

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="next-steps"></a>次のステップ

これで、Queue Storage の基本を学習できました。さらに詳細な情報が必要な場合は、次のリンク先を参照してください。

- [Azure Queue Storage の Python API リファレンス](/python/api/azure-storage-queue)
- [Python デベロッパー センター](https://azure.microsoft.com/develop/python/)
- [Azure Storage REST API リファレンス](/rest/api/storageservices/)
