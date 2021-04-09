---
title: Azure Service Bus Premium で Java Message Service 2.0 API を使用する
description: Azure Service Bus で Java Message Service (JMS) を使用する方法
ms.topic: article
ms.date: 07/17/2020
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 894821444f74248b73578595df943cb3a0025360
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101698401"
---
# <a name="use-java-message-service-20-api-with-azure-service-bus-premium"></a>Azure Service Bus Premium で Java Message Service 2.0 API を使用する

この記事では、広く使われている **Java Message Service (JMS) 2.0** API を使用し、Advanced Message Queueing Protocol (AMQP 1.0) プロトコルで Azure Service Bus を操作する方法について説明します。

> [!NOTE]
> Java Message Service (JMS) 2.0 API のサポートは **Azure Service Bus Premium レベル** に限られます。
>

## <a name="pre-requisites"></a>前提条件

### <a name="get-started-with-service-bus"></a>Service Bus の概要

このガイドでは、Service Bus 名前空間が既にあることを前提とします。 まだない場合は、[Azure portal](https://portal.azure.com) を使用して[名前空間とキューを作成する](service-bus-create-namespace-portal.md)ことができます。 

Service Bus 名前空間とキューの作成方法の詳細については、[Azure portal を使用して Service Bus キューを作成する](service-bus-quickstart-portal.md)方法に関するページを参照してください。

### <a name="set-up-a-java-development-environment"></a>Java 開発環境を設定する

Java アプリケーションを開発するには、次に示す適切な開発環境を設定する必要があります。 
   * JDK (Java Development Kit) または JRE (Java Runtime Environment) のいずれかがインストールされている。
   * JDK または JRE が、ビルド パスと適切なシステム変数に追加されている。
   * JDK または JRE を利用するために、Java IDE がインストールされている。 たとえば、Eclipse や IntelliJ。

Java on Azure 用の開発環境を準備する方法について詳しくは、[こちらのガイド](/azure/developer/java/fundamentals/)を参照してください。

## <a name="what-jms-features-are-supported"></a>どのような JMS 機能がサポートされていますか?

[!INCLUDE [service-bus-jms-features-list](../../includes/service-bus-jms-feature-list.md)]

## <a name="downloading-the-java-message-service-jms-client-library"></a>Java Message Service (JMS) クライアント ライブラリのダウンロード

Azure Service Bus Premium レベルに用意されているすべての機能を利用するには、プロジェクトのビルド パスに以下のライブラリを追加する必要があります。

[Azure-servicebus-jms](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms)

> [!NOTE]
> ビルド パスに [Azure-servicebus-jms](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms) を追加するには、[Maven](https://maven.apache.org/) や [Gradle](https://gradle.org/) など、プロジェクトに適した依存関係管理ツールをご利用ください。
>

## <a name="coding-java-applications"></a>Java アプリケーションのコーディング

依存関係がインポートされると、Java アプリケーションを JMS プロバイダーに依存しない方法で記述できます。

### <a name="connecting-to-azure-service-bus-using-jms"></a>JMS を使用した Azure Service Bus への接続

JMS クライアントを使用して Azure Service Bus に接続するには、**接続文字列** が必要です。これは、[Azure portal](https://portal.azure.com) の "共有アクセス ポリシー" の **[プライマリ接続文字列]** から入手できます。

1. `ServiceBusJmsConnectionFactorySettings` をインスタンス化します

    ```java
    ServiceBusJmsConnectionFactorySettings connFactorySettings = new ServiceBusJmsConnectionFactorySettings();
    connFactorySettings.setConnectionIdleTimeoutMS(20000);
    ```
2. 適切な `ServiceBusConnectionString` を使用して `ServiceBusJmsConnectionFactory` をインスタンス化します。

    ```java
    String ServiceBusConnectionString = "<SERVICE_BUS_CONNECTION_STRING_WITH_MANAGE_PERMISSIONS>";
    ConnectionFactory factory = new ServiceBusJmsConnectionFactory(ServiceBusConnectionString, connFactorySettings);
    ```

3. `ConnectionFactory` を使用して `Connection` を作成した後に `Session` を作成するか 

    ```java
    Connection connection = factory.createConnection();
    Session session = connection.createSession();
    ```
    または `JMSContext` を作成します (JMS 2.0 クライアントの場合)

    ```java
    JMSContext jmsContext = factory.createContext();
    ```

    >[!IMPORTANT]
    > 名前は同じですが、JMS の "セッション" と Service Bus の "セッション" は相互にまったく無関係です。
    >
    > JMS 1.1 の場合、セッションとは、MessageProducer、MessageConsumer、およびメッセージ自体の作成を可能にする、API の重要な構成要素のことです。 詳細については、[JMS API プログラミング モデル](https://docs.oracle.com/javaee/6/tutorial/doc/bnceh.html)を確認してください
    >
    > Service Bus の場合、[セッション](message-sessions.md)とは、キューおよびサブスクリプションでの FIFO 処理を可能にする、サービスおよびクライアント側の構成要素のことです。
    >

### <a name="write-the-jms-application"></a>JMS アプリケーションの記述

`Session` または `JMSContext` がインスタンス化されると、アプリケーションで使い慣れた JMS API を使用して管理操作とデータ操作の両方を実行できます。

サポートされる API については、[サポートされる JMS 機能](how-to-use-java-message-service-20.md#what-jms-features-are-supported)の一覧を参照してください。

以下に、JMS の基本的な使い方を示したサンプル コード スニペットをいくつか紹介します。

#### <a name="sending-messages-to-a-queue-and-topic"></a>キューとトピックにメッセージを送信する

```java
// Create the queue and topic
Queue queue = jmsContext.createQueue("basicQueue");
Topic topic = jmsContext.createTopic("basicTopic");
// Create the message
Message msg = jmsContext.createMessage();

// Create the JMS message producer
JMSProducer producer = jmsContext.createProducer();

// send the message to the queue
producer.send(queue, msg);
// send the message to the topic
producer.send(topic, msg);
```

#### <a name="receiving-messages-from-a-queue"></a>キューからメッセージを受信する

```java
// Create the queue
Queue queue = jmsContext.createQueue("basicQueue");

// Create the message consumer
JMSConsumer consumer = jmsContext.createConsumer(queue);

// Receive the message
Message msg = (Message) consumer.receive();
```

#### <a name="receiving-messages-from-a-shared-durable-subscription-on-a-topic"></a>トピックの共有の永続的サブスクリプションからメッセージを受信する

```java
// Create the topic
Topic topic = jmsContext.createTopic("basicTopic");

// Create a shared durable subscriber on the topic
JMSConsumer sharedDurableConsumer = jmsContext.createSharedDurableConsumer(topic, "sharedDurableConsumer");

// Receive the message
Message msg = (Message) sharedDurableConsumer.receive();
```

## <a name="summary"></a>まとめ

このガイドでは、AMQP 1.0 経由で Java Message Service (JMS) を使用する Java クライアント アプリケーションと Azure Service Bus がどのように対話するかについて説明しました。

Service Bus AMQP 1.0 のサポートは、.NET、C、Python、PHP など、その他の言語からも使用できます。 Service Bus で AMQP 1.0 のサポートを使用すると、これらのさまざまな言語を使って作成されたコンポーネントで高い信頼性と十分な忠実度のメッセージ交換が実現されます。

## <a name="next-steps"></a>次のステップ

Azure Service Bus と Java Message Service (JMS) エンティティの詳細については、以下のリンクを参照してください。 
* [Service Bus - キュー、トピック、サブスクリプション](service-bus-queues-topics-subscriptions.md)
* [Service Bus - Java メッセージ サービス エンティティ](service-bus-queues-topics-subscriptions.md#java-message-service-jms-20-entities)
* [Azure Service Bus での AMQP 1.0 サポート](service-bus-amqp-overview.md)
* [Service Bus AMQP 1.0: 開発者ガイド](service-bus-amqp-dotnet.md)
* [Service Bus キューの使用](service-bus-dotnet-get-started-with-queues.md)
* [Java Message Service API (外部の Oracle ドキュメント)](https://docs.oracle.com/javaee/7/api/javax/jms/package-summary.html)
* [ActiveMQ から Service Bus に移行する方法](migrate-jms-activemq-to-servicebus.md)