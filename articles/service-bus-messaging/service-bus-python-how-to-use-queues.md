---
title: Azure Service Bus のキューを Python azure-servicebus パッケージ バージョン 7.0.0 で使用する
description: この記事では、Python を使用して、Azure Service Bus キューとの間でメッセージを送受信する方法について説明します。
author: spelluru
documentationcenter: python
ms.devlang: python
ms.topic: quickstart
ms.date: 11/18/2020
ms.author: spelluru
ms.custom: seo-python-october2019, devx-track-python
ms.openlocfilehash: 0553062032a58ec9eb9cf3c474ee7c8f19fc544d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98631557"
---
# <a name="send-messages-to-and-receive-messages-from-azure-service-bus-queues-python"></a>Azure Service Bus キューとの間でメッセージを送受信する (Python)
この記事では、Python を使用して、Azure Service Bus キューとの間でメッセージを送受信する方法について説明します。 

## <a name="prerequisites"></a>前提条件
- Azure サブスクリプション。 [Visual Studio または MSDN のサブスクライバー特典](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF)を有効にするか、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)にサインアップしてください。
- 使用するキューがない場合は、「[Azure portal を使用して Service Bus キューを作成する](service-bus-quickstart-portal.md)」の記事にある手順に従って、キューを作成します。 Service Bus 名前空間の **接続文字列** と、作成した **キュー** の名前をメモしておいてください。
- Python 2.7 以降および [Python Azure Service Bus](https://pypi.python.org/pypi/azure-servicebus) パッケージがインストールされていること。 詳しくは、[Python インストール ガイド](/azure/developer/python/azure-sdk-install)に関する記事をご覧ください。 

## <a name="send-messages-to-a-queue"></a>メッセージをキューに送信する

1. 次の import ステートメントを追加します。 

    ```python
    from azure.servicebus import ServiceBusClient, ServiceBusMessage
    ```
2. 次の定数を追加します。 

    ```python
    CONNECTION_STR = "<NAMESPACE CONNECTION STRING>"
    QUEUE_NAME = "<QUEUE NAME>"
    ```

    > [!IMPORTANT]
    > - `<NAMESPACE CONNECTION STRING>` を Service Bus 名前空間の接続文字列に置き換えます。
    > - `<QUEUE NAME>` をキューの名前に置き換えます。 
3. 単一のメッセージを送信するためのメソッドを追加します。

    ```python
    def send_single_message(sender):
        # create a Service Bus message
        message = ServiceBusMessage("Single Message")
        # send the message to the queue
        sender.send_messages(message)
        print("Sent a single message")
    ```

    sender は、作成したキューのクライアントとしての役割を担うオブジェクトです。 それを後で作成し、引数としてこの関数に送信することになります。 
4. 一連のメッセージを送信するためのメソッドを追加します。

    ```python
    def send_a_list_of_messages(sender):
        # create a list of messages
        messages = [ServiceBusMessage("Message in list") for _ in range(5)]
        # send the list of messages to the queue
        sender.send_messages(messages)
        print("Sent a list of 5 messages")
    ```
5. メッセージのバッチを送信するためのメソッドを追加します。

    ```python
    def send_batch_message(sender):
        # create a batch of messages
        batch_message = sender.create_message_batch()
        for _ in range(10):
            try:
                # add a message to the batch
                batch_message.add_message(ServiceBusMessage("Message inside a ServiceBusMessageBatch"))
            except ValueError:
                # ServiceBusMessageBatch object reaches max_size.
                # New ServiceBusMessageBatch object can be created here to send more data.
                break
        # send the batch of messages to the queue
        sender.send_messages(batch_message)
        print("Sent a batch of 10 messages")
    ```
6. Service Bus クライアントを作成し、メッセージを送信するためのキューの sender オブジェクトを作成します。

    ```python
    # create a Service Bus client using the connection string
    servicebus_client = ServiceBusClient.from_connection_string(conn_str=CONNECTION_STR, logging_enable=True)
    with servicebus_client:
        # get a Queue Sender object to send messages to the queue
        sender = servicebus_client.get_queue_sender(queue_name=QUEUE_NAME)
        with sender:
            # send one message        
            send_single_message(sender)
            # send a list of messages
            send_a_list_of_messages(sender)
            # send a batch of messages
            send_batch_message(sender)
    
    print("Done sending messages")
    print("-----------------------")
    ```
 
## <a name="receive-messages-from-a-queue"></a>キューからメッセージを受信する
次のコードを print ステートメントの後ろに追加します。 このコードは、新しいメッセージを受信しない状態が 5 (`max_wait_time`) 秒続くまで、新しいメッセージを受信し続けます。 

```python
with servicebus_client:
    # get the Queue Receiver object for the queue
    receiver = servicebus_client.get_queue_receiver(queue_name=QUEUE_NAME, max_wait_time=5)
    with receiver:
        for msg in receiver:
            print("Received: " + str(msg))
            # complete the message so that the message is removed from the queue
            receiver.complete_message(msg)
```

## <a name="full-code"></a>コード全体

```python
# import os
from azure.servicebus import ServiceBusClient, ServiceBusMessage

CONNECTION_STR = "<NAMESPACE CONNECTION STRING>"
QUEUE_NAME = "<QUEUE NAME>"

def send_single_message(sender):
    message = ServiceBusMessage("Single Message")
    sender.send_messages(message)
    print("Sent a single message")

def send_a_list_of_messages(sender):
    messages = [ServiceBusMessage("Message in list") for _ in range(5)]
    sender.send_messages(messages)
    print("Sent a list of 5 messages")

def send_batch_message(sender):
    batch_message = sender.create_message_batch()
    for _ in range(10):
        try:
            batch_message.add_message(ServiceBusMessage("Message inside a ServiceBusMessageBatch"))
        except ValueError:
            # ServiceBusMessageBatch object reaches max_size.
            # New ServiceBusMessageBatch object can be created here to send more data.
            break
    sender.send_messages(batch_message)
    print("Sent a batch of 10 messages")

servicebus_client = ServiceBusClient.from_connection_string(conn_str=CONNECTION_STR, logging_enable=True)

with servicebus_client:
    sender = servicebus_client.get_queue_sender(queue_name=QUEUE_NAME)
    with sender:
        send_single_message(sender)
        send_a_list_of_messages(sender)
        send_batch_message(sender)

print("Done sending messages")
print("-----------------------")

with servicebus_client:
    receiver = servicebus_client.get_queue_receiver(queue_name=QUEUE_NAME, max_wait_time=5)
    with receiver:
        for msg in receiver:
            print("Received: " + str(msg))
            receiver.complete_message(msg)
```

## <a name="run-the-app"></a>アプリを実行する
アプリケーションを実行すると、次の出力が表示されます。 

```console
Sent a single message
Sent a list of 5 messages
Sent a batch of 10 messages
Done sending messages
-----------------------
Received: Single Message
Received: Message in list
Received: Message in list
Received: Message in list
Received: Message in list
Received: Message in list
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
```

Azure portal で、使用する Service Bus 名前空間に移動します。 **[概要]** ページで、**受信** メッセージ数と **送信** メッセージ数が 16 になっていることを確認します。 これらのカウントが表示されない場合は、数分待ってから、ページを最新の情報に更新してください。 

:::image type="content" source="./media/service-bus-python-how-to-use-queues/overview-incoming-outgoing-messages.png" alt-text="受信メッセージ数と送信メッセージ数":::

この **[概要]** ページでキューを選択して、 **[Service Bus キュー]** ページに移動します。 **受信** メッセージ数と **送信** メッセージ数は、このページで確認することもできます。 加えて、キューの **現在のサイズ** や **アクティブなメッセージ数** など、他の情報も表示されます。 

:::image type="content" source="./media/service-bus-python-how-to-use-queues/queue-details.png" alt-text="キューの詳細":::


## <a name="next-steps"></a>次のステップ
次のドキュメントおよびサンプルを参照してください。 

- [Python 用の Azure Service Bus クライアント ライブラリ](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/servicebus/azure-servicebus)
- [サンプル](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/servicebus/azure-servicebus/samples)。 
    - **sync_samples** フォルダーには、Service Bus を同期的な方法で扱う方法を紹介したサンプルが格納されています。 このクイック スタートで使用したのは、こちらの方法です。 
    - **async_samples** フォルダーには、Service Bus を非同期的な方法で扱う方法を紹介したサンプルが格納されています。 
- [azure-servicebus のリファレンス ドキュメント](/python/api/azure-servicebus/azure.servicebus?preserve-view=true)