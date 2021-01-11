---
title: クイック スタート:Python で Azure Service Bus キューを使用する
description: この記事では、Python を使用して、Azure Service Bus キューを作成し、このキューとの間でメッセージを送受信する方法について説明します。
author: spelluru
documentationcenter: python
ms.devlang: python
ms.topic: quickstart
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: seo-python-october2019, devx-track-python
ms.openlocfilehash: 55ed71c6947c70ac797656f2f18cf71dd8aaae9d
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87852482"
---
# <a name="quickstart-use-azure-service-bus-queues-with-python"></a>クイック スタート:Python で Azure Service Bus キューを使用する

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

この記事では、Python を使用して、Azure Service Bus キューを作成し、このキューとの間でメッセージを送受信する方法について説明します。 

Python Azure Service Bus ライブラリの詳細については、「[Python 用 Service Bus ライブラリ](/python/api/overview/azure/servicebus?view=azure-python)」を参照してください。

## <a name="prerequisites"></a>前提条件
- Azure サブスクリプション。 [Visual Studio または MSDN のサブスクライバー特典](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF)を有効にするか、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)にサインアップしてください。
- 「[クイック スタート:Azure portal を使用して Service Bus トピックとそのサブスクリプションを作成する](service-bus-quickstart-topics-subscriptions-portal.md)」の手順に従って作成された Service Bus 名前空間。 この記事の後半で使用するために、 **[共有アクセス ポリシー]** 画面からプライマリ接続文字列をコピーします。 
- [Azure Service Bus][Python Azure Service Bus package] パッケージがインストールされた Python 3.4 x 以上。 詳しくは、[Python インストール ガイド](/azure/developer/python/azure-sdk-install)に関する記事をご覧ください。 

## <a name="create-a-queue"></a>キューを作成する

**ServiceBusClient** オブジェクトを使用して、キューを操作できます。 プログラムを使用して Service Bus にアクセスするには、Python ファイルの先頭付近に、次の行を追加します。

```python
from azure.servicebus import ServiceBusClient
```

次のコードを追加して、**ServiceBusClient** オブジェクトを作成します。 `<connectionstring>` を Service Bus のプライマリ接続文字列値に置き換えます。 この値は、[Azure portal][Azure portal] 内の Service Bus 名前空間の **[共有アクセス ポリシー]** にあります。

```python
sb_client = ServiceBusClient.from_connection_string('<connectionstring>')
```

次のコードでは、**ServiceBusClient** の `create_queue` メソッドを使用して、`taskqueue` というキューを既定の設定で作成します。

```python
sb_client.create_queue("taskqueue")
```

オプションを使用すると、メッセージの Time to Live (TTL) や最大トピック サイズなどの既定のキュー設定をオーバーライドできます。 次のコードは、最大キュー サイズが 5 GB、TTL 値が 1 分の `taskqueue` というキューを作成します。

```python
sb_client.create_queue("taskqueue", max_size_in_megabytes=5120,
                       default_message_time_to_live=datetime.timedelta(minutes=1))
```

## <a name="send-messages-to-a-queue"></a>メッセージをキューに送信する

メッセージを Service Bus キューに送信するには、アプリケーションで **ServiceBusClient** オブジェクトの `send` メソッドを呼び出します。 次のコード例では、キュー クライアントを作成し、`taskqueue` キューにテスト メッセージを送信します。 `<connectionstring>` を Service Bus のプライマリ接続文字列値に置き換えます。 

```python
from azure.servicebus import QueueClient, Message

# Create the QueueClient
queue_client = QueueClient.from_connection_string("<connectionstring>", "taskqueue")

# Send a test message to the queue
msg = Message(b'Test Message')
queue_client.send(msg)
```

### <a name="message-size-limits-and-quotas"></a>メッセージ サイズの制限とクォータ

Service Bus キューでサポートされているメッセージの最大サイズは、[Standard レベル](service-bus-premium-messaging.md)では 256 KB、[Premium レベル](service-bus-premium-messaging.md)では 1 MB です。 標準とカスタムのアプリケーション プロパティが含まれるヘッダーの最大サイズは 64 KB です。 1 つのキューで保持できるメッセージ数には上限がありませんが、1 つのキューで保持されるメッセージの合計サイズには上限があります。 キューのサイズは作成時に定義できます。上限は 5 GB です。 

クォータの詳細については、「[Service Bus のクォータ][Service Bus quotas]」を参照してください。

## <a name="receive-messages-from-a-queue"></a>キューからメッセージを受信する

キュー クライアントでは、**ServiceBusClient** オブジェクトの `get_receiver` メソッドを使用して、キューからメッセージを受信します。 次のコード例では、キュー クライアントを作成し、`taskqueue` キューからメッセージを受信します。 `<connectionstring>` を Service Bus のプライマリ接続文字列値に置き換えます。 

```python
from azure.servicebus import QueueClient, Message

# Create the QueueClient
queue_client = QueueClient.from_connection_string("<connectionstring>", "taskqueue")

# Receive the message from the queue
with queue_client.get_receiver() as queue_receiver:
    messages = queue_receiver.fetch_next(timeout=3)
    for message in messages:
        print(message)
        message.complete()
```

### <a name="use-the-peek_lock-parameter"></a>peek_lock パラメーターを使用する

`get_receiver` のオプションの `peek_lock` パラメーターでは、メッセージが読み取られたときに Service Bus によってそれらのメッセージがキューから削除されるかどうかを決定します。 メッセージを受信するための既定のモードは *PeekLock* です。つまり、`peek_lock` が **True** に設定されており、メッセージをキューから削除せずに読み取って (ピーク)、ロックします。 その後、各メッセージを明示的に完了して、キューから削除する必要があります。

読み取り時にキューからメッセージを削除するには、`get_receiver` の `peek_lock` パラメーターを **False** に設定します。 受信操作の一部としてメッセージを削除するのが最もシンプルなモデルですが、障害が発生した場合にアプリケーションがメッセージの欠落を許容できる場合にのみ動作します。 この動作を理解するために、コンシューマーが受信要求を発行した後で、それを処理する前にクラッシュしたというシナリオを考えてみましょう。 メッセージが受信中に削除された場合、アプリケーションが再起動してメッセージの読み取りを再開したときに、クラッシュ前に受信したメッセージは見落とされます。

アプリケーションがメッセージの紛失を許容できない場合、受信は 2 段階の操作です。 PeekLock では、次に読み取られるメッセージを検索し、他のコンシューマーが受信できないようロックしてから、アプリケーションにメッセージを返します。 メッセージの処理または格納後、アプリケーションは、**Message** オブジェクトの `complete` メソッドを呼び出して受信処理の第 2 段階を完了します。  `complete` メソッドによって、メッセージが読み取り中としてマークされ、キューから削除されます。

## <a name="handle-application-crashes-and-unreadable-messages"></a>アプリケーションのクラッシュと読み取り不能のメッセージを処理する

Service Bus には、アプリケーションにエラーが発生した場合や、メッセージの処理に問題がある場合に復旧を支援する機能が備わっています。 受信側のアプリケーションがなんらかの理由によってメッセージを処理できない場合には、**Message** オブジェクトの `unlock` メソッドを呼び出すことができます。 Service Bus によりキュー内のメッセージのロックが解除され、メッセージが同じまたは別のコンシューマー側アプリケーションによって再度受信できる状態に変わります。

キュー内でロックされているメッセージにもタイムアウトがあります。 アプリケーションがクラッシュした場合など、ロックがタイムアウトになる前にアプリケーションがメッセージの処理に失敗した場合には、Service Bus によってメッセージのロックが自動的に解除され、再度受信できる状態になります。

メッセージが処理された後、`complete` メソッドが呼び出される前にアプリケーションがクラッシュした場合は、アプリケーションが再起動する際にメッセージが再配信されます。 一般に、この動作は "*1 回以上の処理*" と呼ばれます。 各メッセージが 1 回以上処理されますが、特定の状況では、同じメッセージが再配信される可能性があります。 重複処理を許容できないシナリオでは、配信の試行をまたがって一定であるメッセージの **MessageId** プロパティを使用して、重複するメッセージ配信を処理できます。 

> [!TIP]
> Service Bus リソースは、[Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/) で管理できます。 Service Bus Explorer を使用すると、Service Bus 名前空間に接続し、メッセージング エンティティを簡単に管理できます。 このツールには、インポート/エクスポート機能や、トピック、キュー、サブスクリプション、リレー サービス、通知ハブ、イベント ハブをテストする機能などの高度な機能が用意されています。

## <a name="next-steps"></a>次のステップ

これで、Service Bus キューの基本を学習できました。詳しくは、[キュー、トピック、サブスクリプション][Queues, topics, and subscriptions]に関する記事をご覧ください。

[Azure portal]: https://portal.azure.com
[Python Azure Service Bus package]: https://pypi.python.org/pypi/azure-servicebus  
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Service Bus quotas]: service-bus-quotas.md
