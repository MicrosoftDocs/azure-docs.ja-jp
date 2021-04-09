---
title: Ruby で Azure Service Bus キューを使用する方法
description: このチュートリアルでは、Ruby アプリケーションを作成して、Service Bus キューとの間でメッセージを送受信する方法を学習します。
documentationcenter: ruby
ms.devlang: ruby
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: 16dda6fc4637f052514a0e78a0804bf4702ed20b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "85336650"
---
# <a name="quickstart-how-to-use-service-bus-queues-with-ruby"></a>クイックスタート: Ruby で Service Bus キューを使用する方法

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

このチュートリアルでは、Ruby アプリケーションを作成して、Service Bus キューとの間でメッセージを送受信する方法を学習します。 サンプルは Ruby で記述され、Azure gem を利用しています。

## <a name="prerequisites"></a>前提条件
1. Azure サブスクリプション。 このチュートリアルを完了するには、Azure アカウントが必要です。 [MSDN のサブスクライバー特典](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF)を有効にするか、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)にサインアップしてください。
2. 「[Azure portal を使用して Service Bus キューを作成する](service-bus-quickstart-portal.md)」の手順に従ってください。
    1. Service Bus **キュー** の **概要** をお読みください。 
    2. Service Bus **名前空間** を作成します。 
    3. **接続文字列** を取得します。 

        > [!NOTE]
        > このチュートリアルでは、Ruby を使用して Service Bus 名前空間で **キュー** を作成します。 

[!INCLUDE [service-bus-ruby-setup](../../includes/service-bus-ruby-setup.md)]

## <a name="how-to-create-a-queue"></a>キューの作成方法
**Azure::ServiceBusService** オブジェクトを使用して、キューを操作できます。 キューを作成するには、`create_queue()` メソッドを使用します。 次の例では、キューを作成するか、すべてのエラーを出力します。

```ruby
azure_service_bus_service = Azure::ServiceBus::ServiceBusService.new(sb_host, { signer: signer})
begin
  queue = azure_service_bus_service.create_queue("test-queue")
rescue
  puts $!
end
```

**Azure::ServiceBus::Queue** オブジェクトに追加のオプションを渡すこともできます。これにより、メッセージの有効期間やキューの最大サイズなどの既定のキューの設定をオーバーライドできます。 次の例は、キューの最大サイズを 5 GB に、有効期間を 1 分に設定する方法を示しています。

```ruby
queue = Azure::ServiceBus::Queue.new("test-queue")
queue.max_size_in_megabytes = 5120
queue.default_message_time_to_live = "PT1M"

queue = azure_service_bus_service.create_queue(queue)
```

## <a name="how-to-send-messages-to-a-queue"></a>メッセージをキューに送信する方法
メッセージを Service Bus キューに送信するには、アプリケーションで **Azure::ServiceBusService** オブジェクトの `send_queue_message()` メソッドを呼び出します。 Service Bus キューに送信された (およびキューから受信された) メッセージは **Azure::ServiceBus::BrokeredMessage** オブジェクトであり、このオブジェクトには、一連の標準的なプロパティ (`label`、`time_to_live` など)、アプリケーションに特有のカスタム プロパティの保持に使用するディクショナリ、および任意のアプリケーション データの本体が備わっています。 アプリケーションでは、メッセージとして文字列値を渡すことによってメッセージの本文を設定でき、必須の標準プロパティは既定値に設定されます。

次の例では、`send_queue_message()` を使用して、`test-queue` という名前のキューにテスト メッセージを送信する方法を示しています。

```ruby
message = Azure::ServiceBus::BrokeredMessage.new("test queue message")
message.correlation_id = "test-correlation-id"
azure_service_bus_service.send_queue_message("test-queue", message)
```

Service Bus キューでサポートされているメッセージの最大サイズは、[Standard レベル](service-bus-premium-messaging.md)では 256 KB、[Premium レベル](service-bus-premium-messaging.md)では 1 MB です。 標準とカスタムのアプリケーション プロパティが含まれるヘッダーの最大サイズは 64 KB です。 キューで保持されるメッセージ数には上限がありませんが、キュー 1 つあたりが保持できるメッセージの合計サイズには上限があります。 このキュー サイズは作成時に定義され、上限は 5 GB です。

## <a name="how-to-receive-messages-from-a-queue"></a>キューからメッセージを受信する方法
キューからメッセージを受信するには、**Azure::ServiceBusService** オブジェクトの `receive_queue_message()` メソッドを使用します。 既定では、メッセージは読み取られて (ピークされて) ロックされますが、キューからは削除されません。 ただし、`:peek_lock` オプションを **false** に設定すると、読み取ったメッセージをキューから削除できます。

既定の動作では、読み取りと削除が 2 段階の操作になるため、メッセージが失われることを許容できないアプリケーションにも対応することができます。 Service Bus は要求を受け取ると、次に読み取られるメッセージを検索して、他のコンシューマーが受信できないようロックしてから、アプリケーションにメッセージを返します。 アプリケーションがメッセージの処理を終えた後 (または後で処理するために確実に保存した後)、`delete_queue_message()` メソッドを呼び出し、削除するメッセージをパラメーターとして指定して、受信処理の第 2 段階を完了します。 `delete_queue_message()` メソッドによって、メッセージが読み取り中としてマークされ、キューから削除されます。

`:peek_lock` パラメーターを **false** に設定すると、メッセージの読み取りと削除は最もシンプルなモデルになります。これは、障害発生時にアプリケーション側でメッセージを処理しないことを許容できるシナリオに最適です。 このことを理解するために、コンシューマーが受信要求を発行した後で、メッセージを処理する前にクラッシュしたというシナリオを考えてみましょう。 Service Bus はメッセージを読み取り済みとしてマークしているため、アプリケーションが再起動してメッセージの読み取りを再開すると、クラッシュ前に読み取られていたメッセージは見落とされることになります。

次の例は `receive_queue_message()` を使用してメッセージを受信し、処理する方法を示しています。 この例では、まず `:peek_lock` を **false** に設定し、メッセージを受信して削除します。次に、別のメッセージを受信してから、`delete_queue_message()` を使用してメッセージを削除します。

```ruby
message = azure_service_bus_service.receive_queue_message("test-queue",
  { :peek_lock => false })
message = azure_service_bus_service.receive_queue_message("test-queue")
azure_service_bus_service.delete_queue_message(message)
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>アプリケーションのクラッシュと読み取り不能のメッセージを処理する方法
Service Bus には、アプリケーションにエラーが発生した場合や、メッセージの処理に問題がある場合に復旧を支援する機能が備わっています。 受信側のアプリケーションが何らかの理由によってメッセージを処理できない場合には、**Azure::ServiceBusService** オブジェクトの `unlock_queue_message()` メソッドを呼び出すことができます。 このメソッドが呼び出されると、Service Bus によってキュー内のメッセージのロックが解除され、メッセージが再度受信できる状態に変わります。メッセージを受信するアプリケーションは、以前と同じものでも、別のものでもかまいません。

キュー内でロックされているメッセージにはタイムアウトも設定されています。アプリケーションがクラッシュした場合など、ロックがタイムアウトになる前にアプリケーションがメッセージの処理に失敗した場合には、メッセージのロックが自動的に解除され、再度受信できる状態に変わります。

メッセージが処理された後、`delete_queue_message()` メソッドが呼び出される前にアプリケーションがクラッシュした場合は、アプリケーションが再起動する際にメッセージが再配信されます。 一般的に、このプロセスは "*1 回以上の処理*" と呼ばれます。つまり、すべてのメッセージが 1 回以上処理されますが、特定の状況では、同じメッセージが再配信される可能性があります。 重複処理が許されないシナリオの場合、重複メッセージの配信を扱うロジックをアプリケーションに追加する必要があります。 通常、この問題はメッセージの `message_id` プロパティを使用して対処します。このプロパティは配信が試行された後も同じ値を保持します。

> [!NOTE]
> Service Bus リソースは、[Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/) で管理できます。 Service Bus Explorer を使用すると、ユーザーは Service Bus 名前空間に接続し、簡単な方法でメッセージング エンティティを管理できます。 このツールには、インポート/エクスポート機能や、トピック、キュー、サブスクリプション、リレー サービス、通知ハブ、イベント ハブをテストする機能などの高度な機能が用意されています。 

## <a name="next-steps"></a>次のステップ
これで、Service Bus キューの基本を学習できました。さらに詳細な情報が必要な場合は、次のリンク先を参照してください。

* [キュー、トピック、サブスクリプション](service-bus-queues-topics-subscriptions.md)の概要。
* GitHub の [Azure SDK for Ruby](https://github.com/Azure/azure-sdk-for-ruby) リポジトリ。

この記事で説明されている Azure Service Bus キューと、[「Ruby から Queue ストレージを使用する方法」](../storage/queues/storage-ruby-how-to-use-queue-storage.md)の記事で説明されている Azure キューの比較については、[「Azure キューと Service Bus キューの比較」](service-bus-azure-and-service-bus-queues-compared-contrasted.md)を参照してください。

