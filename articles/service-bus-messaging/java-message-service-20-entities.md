---
title: Azure Service Bus メッセージング - Java Message Service エンティティ
description: この記事では、Java Message Service API でアクセスできる Azure Service Bus メッセージング エンティティの概要について取り上げます。
ms.topic: article
ms.date: 07/20/2020
ms.openlocfilehash: ee4e0124dced16b86d5292c647e129aa87645f22
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2021
ms.locfileid: "100652583"
---
# <a name="java-message-service-jms-20-entities"></a>Java Message Service (JMS) 2.0 のエンティティ

Azure Service Bus Premium に接続して [Azure Service Bus JMS ライブラリ](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms)を利用するクライアント アプリケーションは、下のエンティティを使用できます。

## <a name="queues"></a>キュー

JMS のキューは意味的に、従来の [Service Bus キュー](service-bus-queues-topics-subscriptions.md#queues)に相当します。

キューを作成するには、`JMSContext` クラス内で下のメソッドを使用します。

```java
Queue createQueue(String queueName)
```

## <a name="topics"></a>トピック

JMS のトピックは意味的に、従来の [Service Bus トピック](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions)に相当します。

トピックを作成するには、`JMSContext` クラス内で下のメソッドを使用します。

```java
Topic createTopic(String topicName)
```

## <a name="temporary-queues"></a>一時キュー

クライアント アプリケーションが、アプリケーションの有効期間の間存在する一時的なエンティティを必要とする場合は、一時キューを使用できます。 これらのエンティティは、[要求-応答](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RequestReply.html)パターンで使用されます。

一時キューを作成するには、`JMSContext` クラス内で下のメソッドを使用します。

```java
TemporaryQueue createTemporaryQueue()
```

## <a name="temporary-topics"></a>一時トピック

一時キューと同様に、一時トピックは、アプリケーションの有効期間の間存在する一時的なエンティティを通してパブリッシュ/サブスクライブを可能にするために存在します。

一時トピックを作成するには、`JMSContext` クラス内で下のメソッドを使用します。

```java
TemporaryTopic createTemporaryTopic()
```

## <a name="java-message-service-jms-subscriptions"></a>Java Message Service (JMS) のサブスクリプション

これは [サブスクリプション](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions)と意味的に似ています (つまり、トピックに関して存在してパブリッシュとサブスクライブのセマンティクスを実現します) が、Java Message Service の仕様で、特定のサブスクリプションに対する属性として、**共有**、**非共有**、**永続的、**非永続的** という概念が導入されています。

> [!NOTE]
> 下のサブスクリプションは、Azure Service Bus の Premium サービス レベルで、[Azure Service Bus JMS ライブラリ](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms)を使用して Azure Service Bus に接続するクライアント アプリケーションのために使用できます。
>
> Azure portal を使用して作成できるのは、永続的サブスクリプションのみです。
>

### <a name="shared-durable-subscriptions"></a>共有の永続的サブスクリプション

共有の永続的サブスクリプションは、アプリケーションがサブスクリプションからのデータを常にアクティブに使用しているかどうかにかかわらず、あるトピックに関してパブリッシュされたすべてのメッセージを受信し、アプリケーションで処理する場合に使用されます。

Service Bus から受信するよう認証されたすべてのアプリケーションで、共有の永続的サブスクリプションから受信できます。

共有の永続的サブスクリプションを作成するには、`JMSContext` クラスに対して次のメソッドを使用します。

```java
JMSConsumer createSharedDurableConsumer(Topic topic, String name)

JMSConsumer createSharedDurableConsumer(Topic topic, String name, String messageSelector)
```

`JMSContext` クラスに対して `unsubscribe` メソッドを使用して削除されていない限り、共有の永続的サブスクリプションは存在し続けます。

```java
void unsubscribe(String name)
```

### <a name="unshared-durable-subscriptions"></a>非共有の永続的サブスクリプション

共有の永続的サブスクリプションと同様に、非共有の永続的サブスクリプションは、アプリケーションがサブスクリプションからのデータをアクティブに使用しているかどうかにかかわらず、あるトピックに関してパブリッシュされたすべてのメッセージを受信し、アプリケーションで処理する場合に使用されます。

ただし、これは非共有のサブスクリプションであるため、サブスクリプションを作成したアプリケーションのみがそのサブスクリプションから受信できます。

非共有の永続的サブスクリプションを作成するには、`JMSContext` クラスから以下のメソッドを使用します。 

```java
JMSConsumer createDurableConsumer(Topic topic, String name)

JMSConsumer createDurableConsumer(Topic topic, String name, String messageSelector, boolean noLocal)
```

> [!NOTE]
> `noLocal` 機能は現在サポートされておらず、無視されます。
>

`JMSContext` クラスに対して `unsubscribe` メソッドを使用して削除されていない限り、非共有の永続的サブスクリプションは存在し続けます。

```java
void unsubscribe(String name)
```

### <a name="shared-non-durable-subscriptions"></a>共有の非永続的サブスクリプション

複数のクライアント アプリケーションが 1 つのサブスクリプションからメッセージを受信して処理する必要がある場合、サブスクリプションからのデータを積極的に使用/受信するようになるまでだけ、共有の非永続的サブスクリプションが使用されます。

サブスクリプションは永続的でないため、保持されません。 それに対するアクティブなコンシューマーが存在しない場合、メッセージはこのサブスクリプションによって受信されません。

共有の非永続的サブスクリプションを作成するには、下のメソッドに示したように、`JMSContext` クラスから `JmsConsumer` を作成します。

```java
JMSConsumer createSharedConsumer(Topic topic, String sharedSubscriptionName)

JMSConsumer createSharedConsumer(Topic topic, String sharedSubscriptionName, String messageSelector)
```

共有の非永続的サブスクリプションは、そこからデータを受信するアクティブなコンシューマーが存在するようになるまで存在し続けます。

### <a name="unshared-non-durable-subscriptions"></a>非共有の非永続的サブスクリプション

クライアント アプリケーションがサブスクリプションからメッセージを受信して処理する必要がある場合、サブスクリプションからのデータを積極的に使用するようになるまでだけ、非共有の非永続的サブスクリプションが使用されます。 このサブスクリプションに存在できるコンシューマーは 1 つだけです。つまり、そのサブスクリプションを作成したクライアントです。

サブスクリプションは永続的でないため、保持されません。 それに対するアクティブなコンシューマーが存在しない場合、メッセージはこのサブスクリプションによって受信されません。

非共有の非永続的サブスクリプションを作成するには、下のメソッドに示したように、`JMSContext` クラスから `JMSConsumer` を作成します。

```java
JMSConsumer createConsumer(Destination destination)

JMSConsumer createConsumer(Destination destination, String messageSelector)

JMSConsumer createConsumer(Destination destination, String messageSelector, boolean noLocal)
```

> [!NOTE]
> `noLocal` 機能は現在サポートされておらず、無視されます。
>

非共有の非永続的サブスクリプションは、そこからデータを受信するアクティブなコンシューマーが存在するようになるまで存在し続けます。

### <a name="message-selectors"></a>メッセージ セレクター

通常の Service Bus サブスクリプションには **フィルターとアクション** があるのと同様に、JMS サブスクリプションには **メッセージ セレクター** があります。

メッセージ セレクターは、それぞれの JMS サブスクリプションに対して設定することができ、メッセージのヘッダー プロパティに関するフィルター条件として存在します。 メッセージ セレクターの式に一致するヘッダー プロパティを持つメッセージだけが配信されます。 値が null または空の文字列の場合、その JMS サブスクリプション/コンシューマーにはメッセージ セレクターがないことを示しています。

## <a name="additional-concepts-for-java-message-service-jms-20-subscriptions"></a>Java Message Service (JMS) 2.0 サブスクリプションのその他の概念

### <a name="client-scoping"></a>クライアントのスコープ

Java Message Service (JMS) 2.0 API で指定されているように、サブスクリプションは、(適切な `clientId` によって識別された) "*特定のクライアント アプリケーションにスコープ設定*" されている場合とそうでない場合があります。

サブスクリプションにスコープを設定すると、そこには、同じクライアント ID を持つクライアント アプリケーションから **しかアクセスできなくなります**。 

特定のクライアント ID (clientId1 など) にスコープ設定されているサブスクリプションに、別のクライアント ID (たとえば、clientId2) を持つアプリケーションからアクセスしようとすると、別のクライアント ID (clientId2) にスコープ設定された別のサブスクリプションが作成されます。

> [!NOTE]
> クライアント ID は null 値または空にすることができますが、JMS クライアント アプリケーションで設定されているクライアント ID と一致している必要があります。 Azure Service Bus の観点からは、null 値のクライアント ID と空のクライアント ID の動作は同じです。
>
> クライアント ID が null 値または空に設定されている場合、それにアクセスできるのは、クライアント ID がやはり null 値または空に設定されているクライアント アプリケーションだけです。
>

### <a name="shareability"></a>共有性

**共有** サブスクリプションでは、複数のクライアントまたはコンシューマー (つまり、JMSConsumer オブジェクト) がそこからメッセージを受信できます。

>[!NOTE]
> 特定のクライアント ID にスコープ設定されている共有サブスクリプションも複数のクライアントまたはコンシューマー (つまり、JMSConsumer オブジェクト) からアクセスできますが、各クライアント アプリケーションのクライアント ID は同じでなければなりません。
>
 

**非共有** サブスクリプションでは、1 つのクライアントまたはコンシューマー (つまり、JMSConsumer オブジェクト) だけがそこからメッセージを受信できます。 非共有サブスクリプションで `JMSConsumer` が作成され、一方でそこに既にメッセージをリッスンしているアクティブな `JMSConsumer` がある場合は、`JMSException` がスローされます。


### <a name="durability"></a>Durability

**永続的** サブスクリプションは永続化されていて、アプリケーション (`JMSConsumer`) がそこからメッセージを使用しているかどうかに関係なく、トピックからのメッセージの収集を続けます。

**非永続的** サブスクリプションは永続化されておらず、アプリケーション (`JMSConsumer`) がそこからメッセージを使用している限り、トピックからメッセージを収集します。 

## <a name="representation-of-client-scoped-subscriptions"></a>クライアント スコープ サブスクリプションの表現

クライアント スコープ (JMS) サブスクリプションが既存の[サブスクリプション](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions)と共存する必要がある場合、クライアント スコープ (JMS) サブスクリプションの表現方法は下の形式に従います。

   * **\<SUBSCRIPTION-NAME\>** $ **\<CLIENT-ID\>** $**D** (永続的サブスクリプションの場合)
   * **\<SUBSCRIPTION-NAME\>** $ **\<CLIENT-ID\>** $**ND** (非永続的サブスクリプションの場合)

ここで、 **$** は区切り記号です。

## <a name="next-steps"></a>次のステップ

Service Bus のメッセージングの詳細と使用例については、次の詳細トピックをご覧ください。

* [Service Bus メッセージングの概要](service-bus-messaging-overview.md)
* [Azure Service Bus Premium で Java Message Service 2.0 API を使用する](how-to-use-java-message-service-20.md)



