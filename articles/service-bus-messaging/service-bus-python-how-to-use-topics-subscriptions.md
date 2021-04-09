---
title: Azure Service Bus のトピックとサブスクリプションを Python azure-servicebus パッケージ バージョン 7.0.0 で使用する
description: この記事では、Python を使用してトピックにメッセージを送信したり、サブスクリプションからメッセージを受信したりする方法について説明します。
documentationcenter: python
author: spelluru
ms.devlang: python
ms.topic: quickstart
ms.date: 11/18/2020
ms.author: spelluru
ms.custom: devx-track-python
ms.openlocfilehash: 4eba3ea055e78888d482927fa6eed5c7d41fa0ba
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98630049"
---
# <a name="send-messages-to-an-azure-service-bus-topic-and-receive-messages-from-subscriptions-to-the-topic-python"></a>Azure Service Bus トピックへのメッセージ送信とトピックのサブスクリプションからのメッセージ受信 (Python)
この記事では、Python を使用して Service Bus トピックにメッセージを送信したり、そのトピックのサブスクリプションからメッセージを受信したりする方法について説明します。 

## <a name="prerequisites"></a>前提条件
- Azure サブスクリプション。 [Visual Studio または MSDN のサブスクライバー特典](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF)を有効にするか、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)にサインアップしてください。
- 「[Quickstart:Azure portal を使用して Service Bus トピックとそのサブスクリプションを作成する](service-bus-quickstart-topics-subscriptions-portal.md)」の手順に従ってください。 接続文字列、トピック名、サブスクリプション名を書き留めておきます。 このクイックスタートで使用するサブスクリプションは 1 つだけです。 
- Python 2.7 以上および [Azure Python SDK][Azure Python パッケージ] パッケージがインストールされていること。 詳しくは、[Python インストール ガイド](/azure/developer/python/azure-sdk-install)に関する記事をご覧ください。

## <a name="send-messages-to-a-topic"></a>メッセージをトピックに送信する

1. 次の import ステートメントを追加します。 

    ```python
    from azure.servicebus import ServiceBusClient, ServiceBusMessage
    ```
2. 次の定数を追加します。 

    ```python
    CONNECTION_STR = "<NAMESPACE CONNECTION STRING>"
    TOPIC_NAME = "<TOPIC NAME>"
    SUBSCRIPTION_NAME = "<SUBSCRIPTION NAME>"
    ```
    
    > [!IMPORTANT]
    > - `<NAMESPACE CONNECTION STRING>` を名前空間の接続文字列に置き換えます。
    > - `<TOPIC NAME>` を対象のトピックの名前に置き換えます。
    > - `<SUBSCRIPTION NAME>` をトピックのサブスクリプションの名前に置き換えます。 
3. 単一のメッセージを送信するためのメソッドを追加します。

    ```python
    def send_single_message(sender):
        # create a Service Bus message
        message = ServiceBusMessage("Single Message")
        # send the message to the topic
        sender.send_messages(message)
        print("Sent a single message")
    ```

    sender は、作成したトピックのクライアントとしての役割を担うオブジェクトです。 それを後で作成し、引数としてこの関数に送信することになります。 
4. 一連のメッセージを送信するためのメソッドを追加します。

    ```python
    def send_a_list_of_messages(sender):
        # create a list of messages
        messages = [ServiceBusMessage("Message in list") for _ in range(5)]
        # send the list of messages to the topic
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
        # send the batch of messages to the topic
        sender.send_messages(batch_message)
        print("Sent a batch of 10 messages")
    ```
6. Service Bus クライアントを作成し、メッセージを送信するためのトピックの sender オブジェクトを作成します。

    ```python
    # create a Service Bus client using the connection string
    servicebus_client = ServiceBusClient.from_connection_string(conn_str=CONNECTION_STR, logging_enable=True)
    with servicebus_client:
        # get a Topic Sender object to send messages to the topic
        sender = servicebus_client.get_topic_sender(topic_name=TOPIC_NAME)
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
 
## <a name="receive-messages-from-a-subscription"></a>サブスクリプションからメッセージを受信する
次のコードを print ステートメントの後ろに追加します。 このコードは、新しいメッセージを受信しない状態が 5 (`max_wait_time`) 秒続くまで、新しいメッセージを受信し続けます。 

```python
with servicebus_client:
    # get the Subscription Receiver object for the subscription    
    receiver = servicebus_client.get_subscription_receiver(topic_name=TOPIC_NAME, subscription_name=SUBSCRIPTION_NAME, max_wait_time=5)
    with receiver:
        for msg in receiver:
            print("Received: " + str(msg))
            # complete the message so that the message is removed from the subscription
            receiver.complete_message(msg)
```

## <a name="full-code"></a>コード全体

```python
from azure.servicebus import ServiceBusClient, ServiceBusMessage

CONNECTION_STR = "<NAMESPACE CONNECTION STRING>"
TOPIC_NAME = "<TOPIC NAME>"
SUBSCRIPTION_NAME = "<SUBSCRIPTION NAME>"

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
    sender = servicebus_client.get_topic_sender(topic_name=TOPIC_NAME)
    with sender:
        send_single_message(sender)
        send_a_list_of_messages(sender)
        send_batch_message(sender)

print("Done sending messages")
print("-----------------------")

with servicebus_client:
    receiver = servicebus_client.get_subscription_receiver(topic_name=TOPIC_NAME, subscription_name=SUBSCRIPTION_NAME, max_wait_time=5)
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

下のペインでトピックを選択し、トピックの **[Service Bus トピック]** ページを表示します。 このページの **[メッセージ]** グラフに 3 つの受信メッセージと 3 つの送信メッセージが確認できると思います。 

:::image type="content" source="./media/service-bus-python-how-to-use-topics-subscriptions/topic-page-portal.png" alt-text="受信メッセージと送信メッセージ":::

このページでいずれかのサブスクリプションを選択すると、その **[Service Bus Subscription]\(Service Bus サブスクリプション\)** ページが表示されます。 このページで、アクティブなメッセージ数や配信不能メッセージ数を確認できます。 この例では、すべてのメッセージが受信済みであるため、アクティブなメッセージ数はゼロです。 

:::image type="content" source="./media/service-bus-python-how-to-use-topics-subscriptions/active-message-count.png" alt-text="アクティブなメッセージ数":::

受信コードをコメント アウトした場合、アクティブなメッセージ数が "16" と表示されます。 

:::image type="content" source="./media/service-bus-python-how-to-use-topics-subscriptions/active-message-count-2.png" alt-text="アクティブなメッセージ数 - 受信しなかった場合":::

## <a name="next-steps"></a>次のステップ
次のドキュメントおよびサンプルを参照してください。 

- [Python 用の Azure Service Bus クライアント ライブラリ](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/servicebus/azure-servicebus)
- [サンプル](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/servicebus/azure-servicebus/samples)。 
    - **sync_samples** フォルダーには、Service Bus を同期的な方法で扱う方法を紹介したサンプルが格納されています。 このクイック スタートで使用したのは、こちらの方法です。 
    - **async_samples** フォルダーには、Service Bus を非同期的な方法で扱う方法を紹介したサンプルが格納されています。 
- [azure-servicebus のリファレンス ドキュメント](/python/api/azure-servicebus/azure.servicebus?preserve-view=true)