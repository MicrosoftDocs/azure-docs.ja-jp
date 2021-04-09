---
title: Azure Service Bus JMS 2.0 開発者ガイド
description: Java Message Service (JMS) 2.0 API を使用して Azure Service Bus と通信する方法
ms.topic: article
ms.date: 01/17/2021
ms.openlocfilehash: 492da46a5ce4f5955a72972d91a8c56d54c385bb
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101726956"
---
# <a name="azure-service-bus-jms-20-developer-guide"></a>Azure Service Bus JMS 2.0 開発者ガイド

このガイドでは、Java Message Service (JMS) 2.0 API を使用して Azure Service Bus との通信を成功させるために役立つ詳細情報を紹介します。

Java 開発者として Azure Service Bus を初めて使用する場合、以下の記事をお読みください。

| 作業の開始 | 概念 |
|----------------|-------|
| <ul> <li> [Azure Service Bus とは](service-bus-messaging-overview.md) </li> <li> [キュー、トピック、サブスクリプション](service-bus-queues-topics-subscriptions.md) </li> </ul> | <ul> <li> [Azure Service Bus - Premium レベル](service-bus-premium-messaging.md) </li> </ul> |

## <a name="java-message-service-jms-programming-model"></a>Java Message Service (JMS) プログラミング モデル

次の図は、Java Message Service API プログラミング モデルの概要を示したものです。 

> [!NOTE]
>
>**Azure Service Bus Premium レベル** では、JMS 1.1 および JMS 2.0 がサポートされています。
> <br> <br>
> **Azure Service Bus - Standard** レベルでは、限られた JMS 1.1 機能がサポートされています。 詳細については、こちらの[ドキュメント](service-bus-java-how-to-use-jms-api-amqp.md)を参照してください。
>

# <a name="jms-20-programming-model"></a>[JMS 2.0 プログラミング モデル](#tab/JMS-20)

:::image type="content" source="./media/jms-developer-guide/java-message-service-20-programming-model.png "alt-text="JMS 2.0 プログラミング モデルを示した図。" border="false":::

# <a name="jms-11-programming-model"></a>[JMS 1.1 プログラミング モデル](#tab/JMS-11)

:::image type="content" source="./media/jms-developer-guide/java-message-service-11-programming-model.png "alt-text="JMS 1.1 プログラミング モデルを示した図。" border="false":::

---

## <a name="jms---building-blocks"></a>JMS - 構成要素

JMS アプリケーションと通信するには、次の構成要素を使用できます。

> [!NOTE]
> 以下のガイドは、「[Oracle Java EE 6 Tutorial for Java Message Service (JMS) (Java Message Service (JMS) 用の Oracle JAVA EE 6 チュートリアル)](https://docs.oracle.com/javaee/6/tutorial/doc/bnceh.html)」を出典としたものです
>
> Java Message Service (JMS) についての理解を深めるために、このチュートリアルを参照することをお勧めします。
>

### <a name="connection-factory"></a>接続ファクトリ
接続ファクトリ オブジェクトは、JMS プロバイダーに接続するためにクライアントによって使用されます。 接続ファクトリは、管理者によって定義された一連の接続構成パラメーターをカプセル化するものです。

各接続ファクトリは、`ConnectionFactory`、`QueueConnectionFactory`、または `TopicConnectionFactory` インターフェイスのインスタンスです。

これらのインターフェイスは、Azure Service Bus との接続を簡単にするために、`ServiceBusJmsConnectionFactory`、`ServiceBusJmsQueueConnectionFactory`、および `ServiceBusJmsTopicConnectionFactory` をそれぞれ使用して実装されます。 接続ファクトリは、次のパラメーターを使用してインスタンス化できます。 
   * 接続文字列 - Azure Service Bus Premium レベルの名前空間に対する接続文字列。
   * 次の設定を含んだ、ServiceBusJmsConnectionFactorySettings プロパティ バッグ
      * connectionIdleTimeoutMS - アイドル状態の接続のタイムアウト値 (ミリ秒単位)。
      * traceFrames - デバッグ用に AMQP トレース フレームを収集するための、ブール型のフラグ。
      * *その他の構成パラメーター*

ファクトリは次のようなコードで作成できます。 接続文字列は必須パラメーターですが、追加のプロパティは省略可能です。

```java
ConnectionFactory factory = new ServiceBusJmsConnectionFactory(SERVICE_BUS_CONNECTION_STRING, null);
```

> [!IMPORTANT]
> JMS 2.0 API を利用する Java アプリケーションは、接続文字列のみを使用して Azure Service Bus に接続する必要があります。 現在、JMS クライアントの認証は、接続文字列を使用してのみサポートされています。
>
> Azure Active Directory (AAD) による認証は、現在サポートされていません。
>

### <a name="jms-destination"></a>JMS の送信先

送信先とは、生成したメッセージのターゲットと、使用するメッセージ ソースを指定するために、クライアントによって使用されるオブジェクトです。

送信先は、Azure Service Bus 内のエンティティにマップされます - キュー (ポイント間の場合) とトピック (パブリッシュ/サブスクライブの場合)。

### <a name="connections"></a>接続

接続は、JMS プロバイダーとの仮想接続をカプセル化するものです。 Azure Service Bus では、これは AMQP を経由した、アプリケーションと Azure Service Bus との間のステートフル接続として表わされます。

接続は、次に示すように接続ファクトリから作成されます。

```java
Connection connection = factory.createConnection();
```

### <a name="sessions"></a>セッション

セッションは、メッセージを生成および使用するためのシングルスレッド コンテキストです。 メッセージ、メッセージ プロデューサー、およびメッセージ コンシューマーを作成するために使用できますが、送信と受信をアトミックな作業単位へとグループ化できるようにするための、トランザクション コンテキストとしても使用できます。

セッションは、次に示すように接続オブジェクトから作成できます。

```java
Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
```

#### <a name="session-modes"></a>セッション モード

セッションは、以下のいずれかのモードで作成できます。

| セッション モード | 動作 |
| ----------------- | -------- |
|**Session.AUTO_ACKNOWLEDGE** | 受信の呼び出しからセッションが正常に返されたとき、またはメッセージを処理するためにセッションが呼び出したメッセージ リスナーが正常に返されたときに、クライアントのメッセージ受信がセッションによって自動的に確認されます。|
|**Session.CLIENT_ACKNOWLEDGE** |メッセージの確認メソッドを呼び出すことにより、使用されたメッセージがクライアントによって確認されます。|
|**Session.DUPS_OK_ACKNOWLEDGE**|この受信確認モードでは、メッセージの遅延配信を行うよう求める指示がセッションに送られます。| 
|**Session.SESSION_TRANSACTED**|この値は、セッションでローカル トランザクションを使用することを指定するために、接続オブジェクトのメソッド createSession(int sessionMode) に、引数として渡すことができます。| 

セッション モードが指定されていない場合、既定では **Session.AUTO_ACKNOWLEDGE** が選択されます。

### <a name="jmscontext"></a>JMSContext

> [!NOTE]
> JMSContext は、JMS 2.0 仕様の一部として定義されています。
>

JMSContext は、接続とセッション オブジェクトによって提供される機能を組み合わせたものです。 これは、接続ファクトリ オブジェクトから作成できます。

```java
JMSContext context = connectionFactory.createContext();
```

#### <a name="jmscontext-modes"></a>JMSContext のモード

JMSContext は、**Session** オブジェクトと同様、「[セッション モード](#session-modes)」で説明されているのと同じ受信確認モードで作成できます。

```java
JMSContext context = connectionFactory.createContext(JMSContext.AUTO_ACKNOWLEDGE);
```

モードが指定されていない場合、既定では **JMSContext.AUTO_ACKNOWLEDGE** が選択されます。

### <a name="jms-message-producers"></a>JMS メッセージ プロデューサー

メッセージ プロデューサーは、JMSContext または Session を使用して作成され、送信先へのメッセージ送信に使用されるオブジェクトです。

次のように、スタンドアロン オブジェクトとして作成できます。 

```java
JMSProducer producer = context.createProducer();
```

または、メッセージの送信が必要な場合に、実行時に作成することができます。

```java
context.createProducer().send(destination, message);
```

### <a name="jms-message-consumers"></a>JMS メッセージ コンシューマー

メッセージ コンシューマーは、JMSContext または Session によって作成され、送信先に送信されたメッセージを受信するために使用されるオブジェクトです。 次のようなコードで作成できます。

```java
JMSConsumer consumer = context.createConsumer(dest);
```

#### <a name="synchronous-receives-via-receive-method"></a>receive() メソッドを使用した同期受信

メッセージ コンシューマーでは、`receive()` メソッドを使用して、送信先からメッセージを同期的に受信することができます。

引数/タイムアウトが指定されていない場合や、タイムアウトが "0"' の場合は、メッセージが到着するか、または接続が切断される (どちらか早い方) まで、コンシューマーは無期限にブロック状態になります。

```java
Message m = consumer.receive();
Message m = consumer.receive(0);
```

0 以外の正の引数を指定すると、コンシューマーはそのタイマーが期限切れになるまでブロック状態になります。

```java
Message m = consumer.receive(1000); // time out after one second.
```

#### <a name="asynchronous-receives-with-jms-message-listeners"></a>JMS メッセージ リスナーを使用した非同期受信

メッセージ リスナーは、送信先でメッセージを非同期的に処理するために使用されるオブジェクトです。 特定のビジネス ロジックが存在する必要がある `onMessage` メソッドを含んだ、`MessageListener` インターフェイスを実装しています。

メッセージ リスナー オブジェクトは、`setMessageListener` メソッドを使用して、特定のメッセージ コンシューマーに対してインスタンス化し、登録する必要があります。

```java
Listener myListener = new Listener();
consumer.setMessageListener(myListener);
```

### <a name="consuming-from-topics"></a>トピックからの使用

[JMS メッセージ コンシューマー](#jms-message-consumers)は、キューまたはトピックである[送信先](#jms-destination)に対して作成されます。

キューのコンシューマーは、クライアント アプリケーションと Azure Service Bus の間のセッション (および接続) のコンテキスト内に存在する、クライアント側のオブジェクトです。

ただし、トピックのコンシューマーには、次の 2 つの部分があります。 
   * Session (または JMSContext) のコンテキスト内に存在する **クライアント側オブジェクト** と、
   * Azure Service Bus のエンティティである **サブスクリプション**。

サブスクリプションは、[こちら](java-message-service-20-entities.md#java-message-service-jms-subscriptions)に記載されています。以下のいずれかを指定できます。 
   * 共有の永続的サブスクリプション
   * 共有の非永続的サブスクリプション
   * 非共有の永続的サブスクリプション
   * 非共有の非永続的サブスクリプション

### <a name="jms-queue-browsers"></a>JMS キュー ブラウザー

JMS API では、アプリケーションでキュー内のメッセージを参照し、各メッセージのヘッダー値を表示できるようにする、`QueueBrowser` オブジェクトが提供されています。

キュー ブラウザーは、次のように JMSContext を使用して作成できます。

```java
QueueBrowser browser = context.createBrowser(queue);
```

> [!NOTE]
> JMS API では、トピックを参照するための API は提供されていません。
>
> これは、トピック自体にメッセージが格納されていないためです。 メッセージは、トピックに送信されるとすぐに、適切なサブスクリプションへと転送されます。
>

### <a name="jms-message-selectors"></a>JMS メッセージ セレクター

メッセージ セレクターは、受信したメッセージをフィルター処理するために、受信側アプリケーションによって使用されます。 メッセージ セレクターを使用した場合、受信側アプリケーションはメッセージのフィルター処理を自身で実行するのではなく、JMS プロバイダー (この場合は Azure Service Bus) に処理をオフロードします。

セレクターは、以下のいずれかのコンシューマーを作成するときに使用できます。 
   * 共有の永続的サブスクリプション
   * 非共有の永続的サブスクリプション
   * 共有の非永続的サブスクリプション
   * 非共有の非永続的サブスクリプション
   * キュー ブラウザー


## <a name="summary"></a>まとめ

この開発者ガイドでは、Java Message Service (JMS) を使用した Java クライアント アプリケーションが Azure Service Bus にどのように接続するかについて説明しました。

## <a name="next-steps"></a>次のステップ

Azure Service Bus と Java Message Service (JMS) エンティティの詳細については、以下のリンクを参照してください。 
* [Service Bus - キュー、トピック、サブスクリプション](service-bus-queues-topics-subscriptions.md)
* [Service Bus - Java メッセージ サービス エンティティ](service-bus-queues-topics-subscriptions.md#java-message-service-jms-20-entities)
* [Azure Service Bus での AMQP 1.0 サポート](service-bus-amqp-overview.md)
* [Service Bus AMQP 1.0: 開発者ガイド](service-bus-amqp-dotnet.md)
* [Service Bus キューの使用](service-bus-dotnet-get-started-with-queues.md)
* [Java Message Service API (外部の Oracle ドキュメント)](https://docs.oracle.com/javaee/7/api/javax/jms/package-summary.html)
* [ActiveMQ から Service Bus に移行する方法](migrate-jms-activemq-to-servicebus.md)
