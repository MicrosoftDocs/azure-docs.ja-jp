---
title: "Python から Queue Storage を使用する方法 | Microsoft Docs"
description: "Python から Azure Queue サービスを使用して、キューの作成と削除のほか、メッセージの挿入、取得、および削除を行う方法を説明します。"
services: storage
documentationcenter: python
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: cc0d2da2-379a-4b58-a234-8852b4e3d99d
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 12/08/2016
ms.author: robinsh
translationtype: Human Translation
ms.sourcegitcommit: fefebeae665ccd14f15b0197241b30d33830fd09
ms.openlocfilehash: 8b09e5c7505554f1007cdf46ae74a100fb083432


---
# <a name="how-to-use-queue-storage-from-python"></a>Python から Queue ストレージを使用する方法
[!INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Overview
このガイドでは、Azure Queue ストレージ サービスを使用して一般的なシナリオを実行する方法について説明します。 サンプルは Python で作成され、 [Microsoft Azure Storage SDK for Python]を使用しています。 キュー メッセージの**挿入**、**ピーク**、**取得**、**削除**と、**キューの作成と削除**の各シナリオについて説明します。 キューの詳細については、「次のステップ」セクションを参照してください。

[!INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="how-to-create-a-queue"></a>方法: キューを作成する
**QueueService** オブジェクトを使用して、キューを操作できます。 次のコードでは、 **QueueService** オブジェクトを作成します。 プログラムを使用して Azure Storage にアクセスするすべての Python ファイルの先頭付近に、次のコードを追加します。

```python
from azure.storage.queue import QueueService
```

次のコードでは、ストレージ アカウント名とアカウント キーを使用して **QueueService** オブジェクトを作成します。 'myaccount' と 'mykey' は、実際のアカウント名とキーに置き換えてください。

```python
queue_service = QueueService(account_name='myaccount', account_key='mykey')

queue_service.create_queue('taskqueue')
```

## <a name="how-to-insert-a-message-into-a-queue"></a>方法: メッセージをキューに挿入する
キューにメッセージを挿入するには、**put\_message** メソッドを使い、新しいメッセージを作成してキューに追加します。

```python
queue_service.put_message('taskqueue', u'Hello World')
```

## <a name="how-to-peek-at-the-next-message"></a>方法: 次のメッセージをピークする
**peek\_messages** メソッドを呼び出すと、キューの先頭にあるメッセージをキューから削除せずにピークできます。 既定では、**peek\_messages** は 1 つのメッセージを対象としてピークします。

```python
messages = queue_service.peek_messages('taskqueue')
for message in messages:
    print(message.content)
```

## <a name="how-to-dequeue-messages"></a>方法: メッセージをデキューする
コードでは、2 つの手順でキューからメッセージを削除します。 **get\_messages** を呼び出すと、既定では、キュー内の次のメッセージを取得します。 **get\_messages** から返されたメッセージは、このキューからメッセージを読み取る他のコードから参照できなくなります。 既定では、このメッセージを参照できない状態は 30 秒間続きます。 また、キューからのメッセージの削除を完了するには、**delete\_message** を呼び出す必要があります。 2 段階の手順でメッセージを削除するこの方法では、ハードウェアまたはソフトウェアの問題が原因でコードによるメッセージの処理が失敗した場合に、コードの別のインスタンスで同じメッセージを取得し、もう一度処理することができます。 コードでは、メッセージが処理された直後に **delete\_message** を呼び出します。

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)
```

キューからのメッセージの取得をカスタマイズする方法は 2 つあります。
1 つ目の方法では、(最大 32 個の) メッセージのバッチを取得できます。 2 つ目の方法では、コードで各メッセージを完全に処理できるように、非表示タイムアウトの設定を長くまたは短くすることができます。 次のコード例では、**get\_messages** メソッドを使って、1 回の呼び出しで 16 個のメッセージを取得します。 その後、for ループを使用して、各メッセージを処理します。 また、各メッセージの非表示タイムアウトを 5 分に設定します。

```python
messages = queue_service.get_messages('taskqueue', num_messages=16, visibility_timeout=5*60)
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)        
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>方法: キューに配置されたメッセージの内容を変更する
キュー内のメッセージの内容をインプレースで変更できます。 メッセージが作業タスクを表している場合は、この機能を使用して、作業タスクの状態を更新できます。 次のコードでは、**update\_message** メソッドを使ってメッセージを更新します。 表示のタイムアウトは 0 に設定されています。これは、メッセージが即時に表示され、コンテンツが更新されることを示します。

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    queue_service.update_message('taskqueue', message.id, message.pop_receipt, 0, u'Hello World Again')
```

## <a name="how-to-get-the-queue-length"></a>方法: キューの長さを取得する
キュー内のメッセージの概数を取得できます。 **get\_queue\_metadata** メソッドを使って、キューのメタデータと、**approximate_message_count** を返すようにキュー サービスに要求します。 Queue サービスが要求に応答した後にメッセージが追加または削除される可能性があるため、取得される結果はおおよその数を示しているだけです。

```python
metadata = queue_service.get_queue_metadata('taskqueue')
count = metadata.approximate_message_count
```

## <a name="how-to-delete-a-queue"></a>方法: キューを削除する
キューおよびキューに含まれているすべてのメッセージを削除するには、**delete\_queue** メソッドを呼び出します。

```python
queue_service.delete_queue('taskqueue')
```

## <a name="next-steps"></a>次のステップ
これで、Queue Storage の基本を学習できました。さらに詳細な情報が必要な場合は、次のリンク先を参照してください。

* [Python デベロッパー センター](/develop/python/)
* [Azure Storage Services REST API (Azure Storage サービスの REST API)](http://msdn.microsoft.com/library/azure/dd179355)
* [Azure Storage チーム ブログ]
* [Microsoft Azure Storage SDK for Python]

[Azure Storage チーム ブログ]: http://blogs.msdn.com/b/windowsazurestorage/
[Microsoft Azure Storage SDK for Python]: https://github.com/Azure/azure-storage-python


<!--HONumber=Nov16_HO3-->


