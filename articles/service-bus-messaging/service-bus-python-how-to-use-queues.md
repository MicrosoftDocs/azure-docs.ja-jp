---
title: Azure Service Bus キューの使用方法 (Python) | Microsoft Docs
description: Python から Azure Service Bus キューを使用する方法を説明します。
services: service-bus-messaging
documentationcenter: python
author: axisc
manager: timlt
editor: spelluru
ms.assetid: b95ee5cd-3b31-459c-a7f3-cf8bcf77858b
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 04/10/2019
ms.author: aschhab
ms.openlocfilehash: fa3aedf138564fedafe555adfbaf6c56efc1813e
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68360845"
---
# <a name="how-to-use-service-bus-queues-with-python"></a>Python で Service Bus キューを使用する方法

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

このチュートリアルでは、Python アプリケーションを作成して、Service Bus キューとの間でメッセージを送受信する方法を学習します。 

## <a name="prerequisites"></a>前提条件
1. Azure サブスクリプション。 このチュートリアルを完了するには、Azure アカウントが必要です。 [MSDN のサブスクライバー特典](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF)を有効にするか、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)にサインアップしてください。
2. 「[Azure portal を使用して Service Bus キューを作成する](service-bus-quickstart-portal.md)」の手順に従ってください。
    1. Service Bus **キュー**の**概要**をお読みください。 
    2. Service Bus **名前空間**を作成します。 
    3. **接続文字列**を取得します。 

        > [!NOTE]
        > このチュートリアルでは、Python を使用して Service Bus 名前空間で**キュー**を作成します。 
1. Python または [Python Azure Service Bus パッケージ][Python Azure Service Bus package]をインストールする方法については、「[Python インストール ガイド](../python-how-to-install.md)」をご覧ください。 Service Bus Python SDK のドキュメント全体については、[ここ](/python/api/overview/azure/servicebus?view=azure-python)をご覧ください。

## <a name="create-a-queue"></a>キューを作成する
**ServiceBusClient** オブジェクトを使用すると、キューを操作できます。 プログラムを使用して Service Bus にアクセスするすべての Python ファイルの先頭付近に次のコードを追加します。

```python
from azure.servicebus import ServiceBusClient
```

次のコードでは、**ServiceBusClient** オブジェクトを作成します。 `<CONNECTION STRING>` を servicebus 接続文字列で置換します。

```python
sb_client = ServiceBusClient.from_connection_string('<CONNECTION STRING>')
```

SAS キーの名前と値は、[Azure portal][Azure portal] 接続情報に含まれています。また、サーバー エクスプローラーで Service Bus 名前空間を選択すると、Visual Studio の **[プロパティ]** ウィンドウに表示されます (前のセクションに示されているとおり)。

```python
sb_client.create_queue("taskqueue")
```

`create_queue` メソッドは追加のオプションもサポートしています。これにより、メッセージの有効期間 (TTL) や最大キュー サイズなどの既定のキューの設定をオーバーライドできます。 次の例では、最大キュー サイズを 5 GB に設定し、TTL 値を 1 分に設定しています。

```python
sb_client.create_queue("taskqueue", max_size_in_megabytes=5120,
                       default_message_time_to_live=datetime.timedelta(minutes=1))
```

詳細については、[Azure Service Bus Python のドキュメント](/python/api/overview/azure/servicebus?view=azure-python)を参照してください。

## <a name="send-messages-to-a-queue"></a>メッセージをキューに送信する
メッセージを Service Bus キューに送信するには、アプリケーションで `ServiceBusClient` オブジェクトに対する `send` メソッドを呼び出します。

次の例では、`send_queue_message` を使用して、`taskqueue` という名前のキューにテスト メッセージを送信する方法を示しています。

```python
from azure.servicebus import QueueClient, Message

# Create the QueueClient
queue_client = QueueClient.from_connection_string(
    "<CONNECTION STRING>", "<QUEUE NAME>")

# Send a test message to the queue
msg = Message(b'Test Message')
queue_client.send(msg)
```

Service Bus キューでサポートされているメッセージの最大サイズは、[Standard レベル](service-bus-premium-messaging.md)では 256 KB、[Premium レベル](service-bus-premium-messaging.md)では 1 MB です。 標準とカスタムのアプリケーション プロパティが含まれるヘッダーの最大サイズは 64 KB です。 キューで保持されるメッセージ数には上限がありませんが、キュー 1 つあたりが保持できるメッセージの合計サイズには上限があります。 このキュー サイズは作成時に定義され、上限は 5 GB です。 クォータの詳細については、「[Service Bus のクォータ][Service Bus quotas]」を参照してください。

詳細については、[Azure Service Bus Python のドキュメント](/python/api/overview/azure/servicebus?view=azure-python)を参照してください。

## <a name="receive-messages-from-a-queue"></a>キューからメッセージを受信する
メッセージは、`ServiceBusService`オブジェクトの`get_receiver` メソッドを使用してキューから受信します。

```python
from azure.servicebus import QueueClient, Message

# Create the QueueClient
queue_client = QueueClient.from_connection_string(
    "<CONNECTION STRING>", "<QUEUE NAME>")

# Receive the message from the queue
with queue_client.get_receiver() as queue_receiver:
    messages = queue_receiver.fetch_next(timeout=3)
    for message in messages:
        print(message)
        message.complete()
```

詳細については、[Azure Service Bus Python のドキュメント](/python/api/overview/azure/servicebus?view=azure-python)を参照してください。


`peek_lock` パラメーターが **False** に設定されていると、メッセージが読み取られるときにキューから削除されます。 `peek_lock` パラメーターを **True** に設定することによって、キューからメッセージを削除せずに、メッセージを読み取って (ピークして) ロックすることができます。

受信操作の中で行われるメッセージの読み取りと削除の動作は、最もシンプルなモデルであり、障害発生時にアプリケーション側でメッセージを処理しないことを許容できるシナリオに最適です。 このことを理解するために、コンシューマーが受信要求を発行した後で、メッセージを処理する前にクラッシュしたというシナリオを考えてみましょう。 Service Bus はメッセージを読み取り済みとしてマークするため、アプリケーションが再起動してメッセージの読み取りを再開すると、クラッシュ前に読み取られていたメッセージは見落とされることになります。

`peek_lock` パラメーターが **True** に設定されている場合、受信処理が 2 段階の動作になり、メッセージが失われることが許容できないアプリケーションに対応することができます。 Service Bus は要求を受け取ると、次に読み取られるメッセージを検索して、他のコンシューマーが受信できないようロックしてから、アプリケーションにメッセージを返します。 アプリケーションがメッセージの処理を終えた後 (または後で処理するために確実に保存した後)、**Message** オブジェクトの **delete** メソッドを呼び出して受信処理の第 2 段階を完了します。 **delete** メソッドによって、メッセージが読み取り中としてマークされ、キューから削除されます。

```python
msg.delete()
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>アプリケーションのクラッシュと読み取り不能のメッセージを処理する方法
Service Bus には、アプリケーションにエラーが発生した場合や、メッセージの処理に問題がある場合に復旧を支援する機能が備わっています。 受信側のアプリケーションがなんらかの理由によってメッセージを処理できない場合には、**Message** オブジェクトの **unlock** メソッドを呼び出すことができます。 このメソッドが呼び出されると、Service Bus によってキュー内のメッセージのロックが解除され、メッセージが再度受信できる状態に変わります。メッセージを受信するアプリケーションは、以前と同じものでも、別のものでもかまいません。

キュー内でロックされているメッセージには、タイムアウトも設定されています。アプリケーションがクラッシュした場合など、ロックがタイムアウトになる前にアプリケーションがメッセージの処理に失敗した場合は、Service Bus によってメッセージのロックが自動的に解除され、再度受信できる状態に変わります。

メッセージが処理された後、**delete** メソッドが呼び出される前にアプリケーションがクラッシュした場合は、アプリケーションが再起動する際にメッセージが再配信されます。 一般的に、この動作は **1 回以上の処理** と呼ばれます。つまり、すべてのメッセージが 1 回以上処理されますが、特定の状況では、同じメッセージが再配信される可能性があります。 重複処理が許されないシナリオの場合、重複メッセージの配信を扱うロジックをアプリケーションに追加する必要があります。 通常、この問題はメッセージの **MessageId** プロパティを使用して対処します。このプロパティは配信が試行された後も同じ値を保持します。

> [!NOTE]
> Service Bus リソースは、[Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/) で管理できます。 Service Bus Explorer を使用すると、ユーザーは Service Bus 名前空間に接続し、簡単な方法でメッセージング エンティティを管理できます。 このツールには、インポート/エクスポート機能や、トピック、キュー、サブスクリプション、リレー サービス、通知ハブ、イベント ハブをテストする機能などの高度な機能が用意されています。 

## <a name="next-steps"></a>次の手順
これで、Service Bus キューの基本を学習できました。さらに詳細な情報が必要な場合は、次の記事をご覧ください。

* [キュー、トピック、サブスクリプション][Queues, topics, and subscriptions]

[Azure portal]: https://portal.azure.com
[Python Azure Service Bus package]: https://pypi.python.org/pypi/azure-servicebus  
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Service Bus quotas]: service-bus-quotas.md

