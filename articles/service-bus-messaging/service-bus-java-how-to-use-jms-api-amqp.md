---
title: "Java Service Bus API で AMQP 1.0 を使用する方法 | Microsoft Docs"
description: "Java Message Service (JMS) API を Azure Service Bus と Advanced Message Queuing Protodol (AMQP) 1.0 と共に使用する方法。"
services: service-bus-messaging
documentationcenter: java
author: sethmanheim
manager: timlt
editor: 
ms.assetid: be766f42-6fd1-410c-b275-8c400c811519
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 01/11/2017
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: f0b0c3bc9daf1e44dfebecedf628b09c97394f94
ms.openlocfilehash: eb22b8e8c2d2b4a619e50b94321d4f819764bdaa
ms.lasthandoff: 01/13/2017


---
# <a name="how-to-use-the-java-message-service-jms-api-with-service-bus-and-amqp-10"></a>Service Bus と AMQP 1.0 で Java Message Service (JMS) API を使用する方法に関するページ
Advanced Message Queuing Protocol (AMQP) 1.0 は、堅牢なクロスプラットフォーム メッセージング アプリケーションを作成するために使用できる、効率的で信頼性の高い回線レベルのメッセージング プロトコルです。

Service Bus での AMQP 1.0 のサポートにより、仲介型メッセージング機能 (キューおよびトピック発行/サブスクライブ) をさまざまなプラットフォームから効率的なバイナリ プロトコルを使って利用できます。 さらに、さまざまな言語、フレームワーク、およびオペレーティング システムを使って作成されたコンポーネントで構成されたアプリケーションを作成できます。

この記事では、一般的な Java Message Service (JMS) API 規格を使用して Java アプリケーションから Service Bus のメッセージング機能 (キューおよびトピック発行/サブスクライブ) を使用する方法について説明します。 Service Bus .NET API を使用して同じ作業を実行する方法が説明されている[関連記事](service-bus-dotnet-advanced-message-queuing.md)があります。 これら 2 種類のガイドを使用して、AMQP 1.0 を使用したクロスプラットフォームのメッセージングについて学習できます。

## <a name="get-started-with-service-bus"></a>Service Bus の概要
このガイドは、**queue1** という名前のキューが含まれている Service Bus 名前空間が既にあることを前提としています。 まだない場合は、[Azure ポータル](https://portal.azure.com)を使用して[名前空間とキュー](service-bus-create-namespace-portal.md)を作成できます。 Service Bus 名前空間とキューの作成方法の詳細については、「[Service Bus キューの使用](service-bus-dotnet-get-started-with-queues.md)」を参照してください。

> [!NOTE]
> パーティション分割されたキューおよびトピックも AMQP をサポートします。 詳細については、「[パーティション分割されたメッセージング エンティティ](service-bus-partitioning.md)」と「[パーティション分割された Service Bus のキューおよびトピックでの AMQP 1.0 のサポート](service-bus-partitioned-queues-and-topics-amqp-overview.md)」を参照してください。
> 
> 

## <a name="downloading-the-amqp-10-jms-client-library"></a>AMQP 1.0 JMS クライアント ライブラリのダウンロード
Apache Qpid JMS AMQP 1.0 クライアント ライブラリの最新バージョンをダウンロードする場所については、[https://qpid.apache.org/download.html](https://qpid.apache.org/download.html) を参照してください。

Service Bus を使用する JMS アプリケーションをビルドおよび実行するときは、次の 4 つの JAR ファイルを Apache Qpid JMS AMQP 1.0 ディストリビューション アーカイブから Java CLASSPATH に追加する必要があります。

* geronimo-jms\_1.1\_spec-1.0.jar
* qpid-amqp-1-0-client-[version].jar
* qpid-amqp-1-0-client-jms-[version].jar
* qpid-amqp-1-0-common-[version].jar

## <a name="coding-java-applications"></a>Java アプリケーションのコーディング
### <a name="java-naming-and-directory-interface-jndi"></a>Java Naming and Directory Interface (JNDI)
JMS では、Java Naming and Directory Interface (JNDI) を使用して論理名と物理名が関連付けられます。 2 種類の JMS オブジェクト、ConnectionFactory および Destination が JNDI を使用して解決されます。 JNDI で使用されるプロバイダー モデルでは、さまざまなディレクトリ サービスに接続して、名前解決のタスクを処理できます。 Apache Qpid JMS AMQP 1.0 ライブラリには、次の形式のプロパティ ファイルを使用して構成されるシンプルなプロパティ ファイル ベースの JNDI Provider が付属しています。

```
# servicebus.properties - sample JNDI configuration

# Register a ConnectionFactory in JNDI using the form:
# connectionfactory.[jndi_name] = [ConnectionURL]
connectionfactory.SBCF = amqps://[SASPolicyName]:[SASPolicyKey]@[namespace].servicebus.windows.net

# Register some queues in JNDI using the form
# queue.[jndi_name] = [physical_name]
# topic.[jndi_name] = [physical_name]
queue.QUEUE = queue1
```

#### <a name="configure-the-connectionfactory"></a>ConnectionFactory の構成
Qpid プロパティ ファイル JNDI プロバイダーで **ConnectionFactory** の定義に使用するエントリは、次のような形式になります。

```
connectionfactory.[jndi_name] = [ConnectionURL]
```

ここで、**[jndi_name]** と **[ConnectionURL]** には次の意味があります。

* **[jndi_name]**: ConnectionFactory の論理名。 Java アプリケーションで JNDI IntialContext.lookup() メソッドを使用して解決される名前です。
* **[ConnectionURL]**: AMQP ブローカーに必要な情報を JMS ライブラリに渡すための URL。

**ConnectionURL** の形式は次のようになります。

```
amqps://[SASPolicyName]:[SASPolicyKey]@[namespace].servicebus.windows.net
```
**[namespace]**、**[SASPolicyName]**、**[SASPolicyKey]** の意味は次のとおりです。

* **[namespace]**: Service Bus 名前空間。
* **[SASPolicyName]**: キューの Shared Access Signature のポリシー名。
* **[SASPolicyKey]**: キューの Shared Access Signature のポリシー キー。

> [!NOTE]
> パスワードは手動で URL エンコードする必要があります。 便利な URL エンコード ユーティリティは、[http://www.w3schools.com/tags/ref_urlencode.asp](http://www.w3schools.com/tags/ref_urlencode.asp) で入手できます。
> 
> 

#### <a name="configure-destinations"></a>送信先の構成
Qpid プロパティ ファイル JNDI プロバイダーで送信先の定義に使用するエントリは、次のような形式になります。

```
queue.[jndi_name] = [physical_name]
```

または

```
topic.[jndi_name] = [physical_name]
```

ここで、**[jndi\_name]** と **[physical\_name]** には次の意味があります。

* **[jndi_name]**: 送信先の論理名。 Java アプリケーションで JNDI IntialContext.lookup() メソッドを使用して解決される名前です。
* **[physical_name]**: アプリケーションでのメッセージの送受信に使用する Service Bus エンティティの名前。

> [!NOTE]
> Service Bus トピック サブスクリプションから受信した場合は、JNDI で指定された物理名がトピックの名前になります。 サブスクリプション名は、JMS アプリケーション コードで永続的なサブスクリプションが作成されるときに指定されます。 「[Service Bus AMQP 1.0 開発者ガイド](service-bus-amqp-dotnet.md)」では、JMS からのサービス バス トピック サブスクリプションの使用の詳細について説明しています。
> 
> 

### <a name="write-the-jms-application"></a>JMS アプリケーションの記述
JMS と Service Bus の使用時に必要になる特殊な API やオプションはありません。 ただし、この後で取り上げているようないくつかの制限があります。 いずれの JMS アプリケーションでも、まず、**ConnectionFactory** と送信先の名前解決が可能になるように JNDI 環境を構成する必要があります。

#### <a name="configure-the-jndi-initialcontext"></a>JNDI InitialContext の構成
JNDI 環境を構成するには、構成情報のハッシュ テーブルを javax.naming.InitialContext クラスのコンストラクターに渡します。 ハッシュ テーブル内の&2; つの必須の要素は Initial Context Factory と Provider URL です。 次のコードでは、Qpid Properties File JNDI Provider と、**servicebus.properties** という名前のプロパティ ファイルを使用して、JNDI 環境を構成する方法を示しています。

```java
Hashtable<String, String> env = new Hashtable<String, String>(); 
env.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory"); 
env.put(Context.PROVIDER_URL, "servicebus.properties"); 
InitialContext context = new InitialContext(env);
``` 

### <a name="a-simple-jms-application-using-a-service-bus-queue"></a>Service Bus キューを使用するシンプルな JMS アプリケーション
次のサンプル プログラムでは、JNDI 論理名が QUEUE の Service Bus キューに JMS TextMessages を送信し、折り返しそれらのメッセージを受信します。

```java
// SimpleSenderReceiver.java

import javax.jms.*;
import javax.naming.Context;
import javax.naming.InitialContext;
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.util.Hashtable;
import java.util.Random;

public class SimpleSenderReceiver implements MessageListener {
    private static boolean runReceiver = true;
    private Connection connection;
    private Session sendSession;
    private Session receiveSession;
    private MessageProducer sender;
    private MessageConsumer receiver;
    private static Random randomGenerator = new Random();

    public SimpleSenderReceiver() throws Exception {
        // Configure JNDI environment
        Hashtable<String, String> env = new Hashtable<String, String>();
        env.put(Context.INITIAL_CONTEXT_FACTORY, 
                   "org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory");
        env.put(Context.PROVIDER_URL, "servicebus.properties");
        Context context = new InitialContext(env);

        // Look up ConnectionFactory and Queue
        ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
        Destination queue = (Destination) context.lookup("QUEUE");

        // Create Connection
        connection = cf.createConnection();

        // Create sender-side Session and MessageProducer
        sendSession = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);
        sender = sendSession.createProducer(queue);

        if (runReceiver) {
            // Create receiver-side Session, MessageConsumer,and MessageListener
            receiveSession = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
            receiver = receiveSession.createConsumer(queue);
            receiver.setMessageListener(this);
            connection.start();
        }
    }

    public static void main(String[] args) {
        try {

            if ((args.length > 0) && args[0].equalsIgnoreCase("sendonly")) {
                runReceiver = false;
            }

            SimpleSenderReceiver simpleSenderReceiver = new SimpleSenderReceiver();
            System.out.println("Press [enter] to send a message. Type 'exit' + [enter] to quit.");
            BufferedReader commandLine = new java.io.BufferedReader(new InputStreamReader(System.in));

            while (true) {
                String s = commandLine.readLine();
                if (s.equalsIgnoreCase("exit")) {
                    simpleSenderReceiver.close();
                    System.exit(0);
                } else {
                    simpleSenderReceiver.sendMessage();
                }
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    private void sendMessage() throws JMSException {
        TextMessage message = sendSession.createTextMessage();
        message.setText("Test AMQP message from JMS");
        long randomMessageID = randomGenerator.nextLong() >>>1;
        message.setJMSMessageID("ID:" + randomMessageID);
        sender.send(message);
        System.out.println("Sent message with JMSMessageID = " + message.getJMSMessageID());
    }

    public void close() throws JMSException {
        connection.close();
    }

    public void onMessage(Message message) {
        try {
            System.out.println("Received message with JMSMessageID = " + message.getJMSMessageID());
            message.acknowledge();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}    
```

### <a name="run-the-application"></a>アプリケーションの実行
このアプリケーションを実行すると、次の形式の出力が生成されます。

```
> java SimpleSenderReceiver
Press [enter] to send a message. Type 'exit' + [enter] to quit.

Sent message with JMSMessageID = ID:2867600614942270318
Received message with JMSMessageID = ID:2867600614942270318

Sent message with JMSMessageID = ID:7578408152750301483
Received message with JMSMessageID = ID:7578408152750301483

Sent message with JMSMessageID = ID:956102171969368961
Received message with JMSMessageID = ID:956102171969368961
exit
```

## <a name="cross-platform-messaging-between-jms-and-net"></a>JMS と .NET の間のクロスプラットフォーム メッセージング
このガイドでは、JMS を使用して Service Bus との間でメッセージを送信および受信する方法について説明しました。 しかし、AMQP 1.0 の主な利点の 1 つは、さまざまな言語で書かれたコンポーネントからアプリケーションを作成して、高い信頼性と完全な忠実度でメッセージ交換を行えることにあります。

ここで示したサンプル JMS アプリケーションと、「[.NET サービス バス API で AMQP 1.0 を使用する方法](service-bus-dotnet-advanced-message-queuing.md)」に示されている類似の .NET アプリケーションを使用すると、.NET と Java の間でメッセージ交換を行うことができます。 

サービス バスと AMQP 1.0 を使ったクロスプラットフォーム メッセージングの詳細については、「[Service Bus AMQP 1.0 開発者ガイド](service-bus-amqp-dotnet.md)」を参照してください。

### <a name="jms-to-net"></a>JMS から .NET
JMS から .NET のメッセージングを試してみるには、次の手順を実行します。

* .NET サンプル アプリケーションを、コマンド ライン引数を使わずに起動します。
* Java サンプル アプリケーションを、"sendonly" コマンド ライン引数を使って起動します。 このモードでは、アプリケーションはキューからメッセージを受信せずに送信のみを行います。
* Java アプリケーションのコンソールで **Enter** キーを何度か押します。メッセージが送信されます。
* それらのメッセージが .NET アプリケーションによって受信されます。

#### <a name="output-from-jms-application"></a>JMS アプリケーションの出力
```
> java SimpleSenderReceiver sendonly
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Sent message with JMSMessageID = ID:4364096528752411591
Sent message with JMSMessageID = ID:459252991689389983
Sent message with JMSMessageID = ID:1565011046230456854
exit
```

#### <a name="output-from-net-application"></a>.NET アプリケーションの出力
```
> SimpleSenderReceiver.exe    
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Received message with MessageID = 4364096528752411591
Received message with MessageID = 459252991689389983
Received message with MessageID = 1565011046230456854
exit
```

### <a name="net-to-jms"></a>.NET から JMS
.NET から JMS のメッセージングを試してみるには、次の手順を実行します。

* .NET サンプル アプリケーションを、"sendonly" コマンド ライン引数を使って起動します。 このモードでは、アプリケーションはキューからメッセージを受信せずに送信のみを行います。
* Java サンプル アプリケーションを、コマンド ライン引数を使わずに起動します。
* .NET アプリケーションのコンソールで **Enter** キーを何度か押します。メッセージが送信されます。
* それらのメッセージが Java アプリケーションによって受信されます。

#### <a name="output-from-net-application"></a>.NET アプリケーションの出力
```
> SimpleSenderReceiver.exe sendonly
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Sent message with MessageID = d64e681a310a48a1ae0ce7b017bf1cf3    
Sent message with MessageID = 98a39664995b4f74b32e2a0ecccc46bb
Sent message with MessageID = acbca67f03c346de9b7893026f97ddeb
exit
```

#### <a name="output-from-jms-application"></a>JMS アプリケーションの出力
```
> java SimpleSenderReceiver    
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Received message with JMSMessageID = ID:d64e681a310a48a1ae0ce7b017bf1cf3
Received message with JMSMessageID = ID:98a39664995b4f74b32e2a0ecccc46bb
Received message with JMSMessageID = ID:acbca67f03c346de9b7893026f97ddeb
exit
```

## <a name="unsupported-features-and-restrictions"></a>サポートされていない機能および制限
JMS を AMQP 1.0 と Service Bus で使用する場合は、次の制限があります。

* **Session** ごとに作成できる **MessageProducer** または **MessageConsumer** は&1; つのみです。 アプリケーションで複数の **MessageProducers** または **MessageConsumers** を作成する必要がある場合は、それぞれに専用の**セッション**を作成してください。
* 揮発性トピック サブスクリプションは現在サポートされていません。
* **MessageSelectors** は現在サポートされていません。
* 一時的な送信先である **TemporaryQueue** と **TemporaryTopic**、およびそれらを使用する **QueueRequestor** API と **TopicRequestor** API は現在サポートされていません。
* トランザクション セッションと分散トランザクションはサポートされません。

## <a name="summary"></a>概要
このガイドでは、一般的な JMS API と AMQP 1.0 を使って Java から Service Bus の仲介型メッセージング機能 (キューおよびトピック発行/サブスクライブ) を使用する方法について説明しました。

Service Bus AMQP 1.0 のサポートは、.NET、C、Python、PHP など、その他の言語からも使用できます。 Service Bus で AMQP 1.0 のサポートを使用すると、これらのさまざまな言語を使って作成されたコンポーネントで高い信頼性と十分な忠実度のメッセージ交換が実現されます。 詳細については、「[Service Bus AMQP 1.0 開発者ガイド](service-bus-amqp-dotnet.md)」を参照してください。

## <a name="next-steps"></a>次のステップ
* [Azure Service Bus での AMQP 1.0 サポート](service-bus-amqp-overview.md)
* [Service Bus .NET API で AMQP 1.0 を使用する方法](service-bus-dotnet-advanced-message-queuing.md)
* [Service Bus AMQP 1.0: 開発者ガイド](service-bus-amqp-dotnet.md)
* [Service Bus キューの使用](service-bus-dotnet-get-started-with-queues.md)
* [Java デベロッパー センター](https://azure.microsoft.com/develop/java/)


