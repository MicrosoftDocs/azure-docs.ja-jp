---
title: Service Bus トピックの使用方法 (Ruby) | Microsoft Docs
description: Azure での Service Bus のトピックとサブスクリプションの使用方法について説明します。 コード サンプルは Ruby アプリケーション向けに作成されています。
services: service-bus-messaging
documentationcenter: ruby
author: axisc
manager: timlt
editor: ''
ms.assetid: 3ef2295e-7c5f-4c54-a13b-a69c8045d4b6
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 04/15/2019
ms.author: aschhab
ms.openlocfilehash: b2a05a4695ee80873a2d7464c0a1cf4d46ed30f5
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543643"
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-ruby"></a>Ruby で Service Bus のトピックとサブスクリプションを使用する方法
 
[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

この記事では、Ruby アプリケーションから Service Bus のトピックとサブスクリプションを使用する方法について説明します。 紹介するシナリオは次のとおりです。

- キュー、トピック、およびサブスクリプションを作成する 
- サブスクリプション フィルターを作成する 
- メッセージをトピックに送信する 
- サブスクリプションからメッセージを受信する
- トピックとサブスクリプションを削除する


## <a name="prerequisites"></a>前提条件
1. Azure サブスクリプション。 このチュートリアルを完了するには、Azure アカウントが必要です。 [Visual Studio または MSDN のサブスクライバー特典](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF)を有効にするか、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)にサインアップしてください。
2. 「[Quickstart:Use the Azure portal to create a Service Bus topic and subscriptions to the topic](service-bus-quickstart-topics-subscriptions-portal.md)」(クイック スタート: Azure portal を使用して Service Bus トピックとその中に含まれるサブスクリプションを作成する) の手順に従って、Service Bus の**名前空間**を作成し、**接続文字列**を取得します。 

    > [!NOTE]
    > このクイック スタートでは、**Ruby** を使用して **トピック**とその中に含まれる**サブスクリプション**を作成します。 

[!INCLUDE [service-bus-ruby-setup](../../includes/service-bus-ruby-setup.md)]

## <a name="create-a-topic"></a>トピックを作成する
**Azure::ServiceBusService** オブジェクトを使用すると、トピックを操作できます。 次のコードでは、**Azure::ServiceBusService** オブジェクトを作成します。 トピックを作成するには、`create_topic()` メソッドを使用します。 次の例ではトピックを作成しており､エラーがあった場合は出力します｡

```ruby
azure_service_bus_service = Azure::ServiceBus::ServiceBusService.new(sb_host, { signer: signer})
begin
  topic = azure_service_bus_service.create_topic("test-topic")
rescue
  puts $!
end
```

**Azure::ServiceBus::Topic** オブジェクトに追加のオプションを渡すこともできます。これにより、メッセージの有効期間やキューの最大サイズなどの既定のトピックの設定をオーバーライドできます。 次の例は、キューの最大サイズを 5 GB に、有効期間を 1 分に設定する方法を示しています。

```ruby
topic = Azure::ServiceBus::Topic.new("test-topic")
topic.max_size_in_megabytes = 5120
topic.default_message_time_to_live = "PT1M"

topic = azure_service_bus_service.create_topic(topic)
```

## <a name="create-subscriptions"></a>サブスクリプションを作成する
トピック サブスクリプションも、**Azure::ServiceBusService** オブジェクトで作成します。 サブスクリプションを指定し、サブスクリプションの仮想キューに配信するメッセージを制限するフィルターを設定できます。

既定では、サブスクリプションは永続的です。 サブスクリプションは、サブスクリプションが削除されるか、サブスクリプションが関連付けられているトピックが削除されるまで保持されます。 アプリケーションにサブスクリプションを作成するロジックが含まれている場合は、最初に getSubscription メソッドを使用して、サブスクリプションが既に存在しているかどうかを確認する必要があります。

[AutoDeleteOnIdle プロパティ](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.autodeleteonidle)を設定することで、サブスクリプションを自動的に削除できます。

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>既定の (MatchAll) フィルターを適用したサブスクリプションの作成
新しいサブスクリプションの作成時にフィルターが指定されていない場合は､**MatchAll** フィルター (既定) が使用されます｡ **MatchAll** フィルターを使用すると、トピックに発行されたすべてのメッセージがサブスクリプションの仮想キューに置かれます。 次の例では、"all-messages" という名前のサブスクリプションを作成し、既定の **MatchAll** フィルターを使用します。

```ruby
subscription = azure_service_bus_service.create_subscription("test-topic", "all-messages")
```

### <a name="create-subscriptions-with-filters"></a>フィルターを適用したサブスクリプションの作成
トピックに送信されたメッセージのうち、特定のサブスクリプション内に表示されるメッセージを指定するフィルターを定義することもできます。

サブスクリプションでサポートされるフィルターのうち、最も柔軟性の高いものが、SQL92 のサブセットを実装する **Azure::ServiceBus::SqlFilter** です。 SQL フィルターは、トピックに発行されるメッセージのプロパティに対して適用されます。 SQL フィルターで使用できる式の詳細については、[SqlFilter](service-bus-messaging-sql-filter.md) 構文の説明をご覧ください。

**Azure::ServiceBusService** オブジェクトの `create_rule()` メソッドを使用して、サブスクリプションにフィルターを追加できます。 このメソッドでは、新しいフィルターを既存のサブスクリプションに追加できます。

既定のフィルターはすべての新しいサブスクリプションに自動的に適用されるため、最初に既定のフィルターを削除する必要があります。削除しないと､ **MatchAll** によって､指定された他のすべてのフィルターがオーバーライドされます。 既定のルールを削除するには、**Azure::ServiceBusService** オブジェクトの `delete_rule()` メソッドを使用します。

次の例では、"high-messages" という名前のサブスクリプションを作成し、**Azure::ServiceBus::SqlFilter** を適用します。このフィルターでは、カスタム プロパティ `message_number` が 3 を超えるメッセージのみが選択されます。

```ruby
subscription = azure_service_bus_service.create_subscription("test-topic", "high-messages")
azure_service_bus_service.delete_rule("test-topic", "high-messages", "$Default")

rule = Azure::ServiceBus::Rule.new("high-messages-rule")
rule.topic = "test-topic"
rule.subscription = "high-messages"
rule.filter = Azure::ServiceBus::SqlFilter.new({
  :sql_expression => "message_number > 3" })
rule = azure_service_bus_service.create_rule(rule)
```

同様に、次の例では `low-messages` という名前のサブスクリプションを作成し、**Azure::ServiceBus::SqlFilter** を適用します。このフィルターでは、`message_number` プロパティが 3 以下のメッセージのみが選択されます。

```ruby
subscription = azure_service_bus_service.create_subscription("test-topic", "low-messages")
azure_service_bus_service.delete_rule("test-topic", "low-messages", "$Default")

rule = Azure::ServiceBus::Rule.new("low-messages-rule")
rule.topic = "test-topic"
rule.subscription = "low-messages"
rule.filter = Azure::ServiceBus::SqlFilter.new({
  :sql_expression => "message_number <= 3" })
rule = azure_service_bus_service.create_rule(rule)
```

メッセージが `test-topic` に送信されると、そのメッセージは `all-messages` トピック サブスクリプションにサブスクライブしている受信者に必ず配信され、さらにメッセージのコンテンツに応じて、`high-messages` と `low-messages` トピック サブスクリプションにサブスクライブしている受信者に対して選択的に配信されます。

## <a name="send-messages-to-a-topic"></a>メッセージをトピックに送信する
メッセージを Service Bus トピックに送信するには、アプリケーションで **Azure::ServiceBusService** オブジェクトの `send_topic_message()` メソッドを使用する必要があります。 Service Bus トピックに送信されたメッセージは、**Azure::ServiceBus::BrokeredMessage** オブジェクトのインスタンスです。 **Azure::ServiceBus::BrokeredMessage** オブジェクトには、(`label` や `time_to_live` などの) 標準的なプロパティ、アプリケーションに特有のカスタム プロパティの保持に使用するディクショナリ、および文字列データの本体が備わっています。 アプリケーションは、`send_topic_message()` メソッドに文字列値を渡すことによってメッセージの本文を設定できます。必須の標準プロパティには既定値が設定されます。

次の例では、`test-topic` トピックに 5 件のテスト メッセージを送信する方法を示しています。 各メッセージの `message_number` カスタム プロパティの値が、ループの反復回数に応じて変化することに注意してください (これによってメッセージを受信するサブスクリプションが決定されます)。

```ruby
5.times do |i|
  message = Azure::ServiceBus::BrokeredMessage.new("test message " + i,
    { :message_number => i })
  azure_service_bus_service.send_topic_message("test-topic", message)
end
```

Service Bus トピックでサポートされているメッセージの最大サイズは、[Standard レベル](service-bus-premium-messaging.md)では 256 KB、[Premium レベル](service-bus-premium-messaging.md)では 1 MB です。 標準とカスタムのアプリケーション プロパティが含まれるヘッダーの最大サイズは 64 KB です。 1 つのトピックで保持されるメッセージ数に上限はありませんが、1 つのトピックで保持できるメッセージの合計サイズには上限があります。 このトピックのサイズはトピックの作成時に定義します。上限は 5 GB です。

## <a name="receive-messages-from-a-subscription"></a>サブスクリプションからメッセージを受信する
サブスクリプションからメッセージを受信するには、**Azure::ServiceBusService** オブジェクトの `receive_subscription_message()` メソッドを使用します。 既定では、メッセージは読み取られて (ピークされて) ロックされますが、サブスクリプションからは削除されません。 `peek_lock` オプションを **false** に設定すると、サブスクリプションからメッセージを読み取って削除できます。

既定の動作では、読み取りと削除が 2 段階の操作になるため、メッセージが失われることを許容できないアプリケーションにも対応することができます。 Service Bus は要求を受け取ると、次に読み取られるメッセージを検索して、他のコンシューマーが受信できないようロックしてから、アプリケーションにメッセージを返します。 アプリケーションがメッセージの処理を終えた後 (または後で処理するために確実に保存した後)、`delete_subscription_message()` メソッドを呼び出し、削除するメッセージをパラメーターとして指定して、受信処理の第 2 段階を完了します。 `delete_subscription_message()` メソッドによって、メッセージが読み取り中としてマークされ、サブスクリプションから削除されます。

`:peek_lock` パラメーターを **false** に設定すると、メッセージの読み取りと削除は最もシンプルなモデルになります。これは、障害発生時にアプリケーション側でメッセージを処理しないことを許容できるシナリオに最適です。 たとえば、コンシューマーが受信要求を発行した後で、メッセージを処理する前にクラッシュするシナリオを考えてみましょう。 Service Bus はメッセージを読み取り済みとしてマークしているため、アプリケーションが再起動してメッセージの読み取りを再開すると、クラッシュ前に読み取られていたメッセージは見落とされます。

次の例では、`receive_subscription_message()` を使用したメッセージの受信および処理の方法を示しています。 この例では、まず `:peek_lock` を **false** に設定し、`low-messages` サブスクリプションからのメッセージを受信して削除します。次に、`high-messages` からの別のメッセージを受信してから、`delete_subscription_message()` を使用してメッセージを削除します。

```ruby
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "low-messages", { :peek_lock => false })
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "high-messages")
azure_service_bus_service.delete_subscription_message(message)
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>アプリケーションのクラッシュと読み取り不能のメッセージを処理する方法
Service Bus には、アプリケーションにエラーが発生した場合や、メッセージの処理に問題がある場合に復旧を支援する機能が備わっています。 受信側のアプリケーションが何らかの理由によってメッセージを処理できない場合には、**Azure::ServiceBusService** オブジェクトの `unlock_subscription_message()` メソッドを呼び出すことができます。 これにより、Service Bus によりサブスクリプション内のメッセージのロックが解除され、メッセージが再度受信できる状態に変わります。このメッセージは、同じコンシューマー側アプリケーションまたは別のコンシューマー側アプリケーションで受信されます。

サブスクリプション内でロックされているメッセージにはタイムアウトも設定されています。ロックがタイムアウトになる前にアプリケーションがメッセージの処理に失敗した場合は (アプリケーションがクラッシュした場合など)、Service Bus によりメッセージのロックが自動的に解除され、再度受信できる状態に変わります。

メッセージが処理された後、`delete_subscription_message()` メソッドが呼び出される前にアプリケーションがクラッシュした場合は、アプリケーションが再起動する際にメッセージが再配信されます。 このプロセスは､しばしば "*1 回以上の処理*" と呼ばれます。つまり、すべてのメッセージが 1 回以上処理されますが、状況によっては、同じメッセージが再配信される可能性があります。 重複処理が許されないシナリオの場合、重複メッセージの配信を扱うロジックをアプリケーションに追加する必要があります。 通常、このロジックはメッセージの `message_id` プロパティを使用して実現され､配信の試行のたびにメッセージが変わることはありません｡

## <a name="delete-topics-and-subscriptions"></a>トピックとサブスクリプションを削除する
トピックとサブスクリプションは、[AutoDeleteOnIdle プロパティ](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.autodeleteonidle)が設定されている場合を除き、永続的です。 これらは [Azure portal][Azure portal] を通じて、またはプログラムで削除できます。 次の例では、`test-topic` という名前のトピックを削除する方法を示しています。

```ruby
azure_service_bus_service.delete_topic("test-topic")
```

トピックを削除すると、そのトピックに登録されたサブスクリプションもすべて削除されます。 サブスクリプションは、個別に削除することもできます。 次のコードでは、`high-messages` という名前のサブスクリプションを `test-topic` トピックから削除する方法を示しています。

```ruby
azure_service_bus_service.delete_subscription("test-topic", "high-messages")
```

> [!NOTE]
> Service Bus リソースは、[Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/) で管理できます。 Service Bus Explorer を使用すると、ユーザーは Service Bus 名前空間に接続し、簡単な方法でメッセージング エンティティを管理できます。 このツールには、インポート/エクスポート機能や、トピック、キュー、サブスクリプション、リレー サービス、通知ハブ、イベント ハブをテストする機能などの高度な機能が用意されています。 

## <a name="next-steps"></a>次の手順
これで、サービス バス トピックの基本を学習できました。さらに詳細な情報が必要な場合は、次のリンク先をご覧ください。

* [Service Bus のキュー、トピック、サブスクリプション](service-bus-queues-topics-subscriptions.md)。
* [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter) の API のリファレンス。
* GitHub の [Azure SDK for Ruby](https://github.com/Azure/azure-sdk-for-ruby) リポジトリ。

[Azure portal]: https://portal.azure.com
