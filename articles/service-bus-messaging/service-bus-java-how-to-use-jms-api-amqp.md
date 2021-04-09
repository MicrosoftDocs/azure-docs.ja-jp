---
title: Java Message Service API と Azure Service Bus で AMQP を使用する
description: Java Message Service (JMS) を Azure Service Bus と Advanced Message Queuing Protodol (AMQP) 1.0 と共に使用する。
ms.topic: article
ms.date: 06/23/2020
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: 97031abaedaa3e5595e290fa0292646feb744d47
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "90086693"
---
# <a name="use-the-java-message-service-with-azure-service-bus-and-amqp-10"></a>Azure Service Bus と AMQP 1.0 で Java Message Service を使用する

> [!WARNING]
> この記事は、Java Message Service (JMS) 1.1 API の "*限定的なサポート*" に対応し、Azure Service Bus Standard レベルのみが対象です。
>
> Java Message Service 2.0 API の完全なサポートは、[プレビューの Azure Service Bus Premium レベル](how-to-use-java-message-service-20.md)でのみ使用できます。 このレベルを使用することをお勧めします。
>

この記事では、一般的な JMS API 標準を使用して Java アプリケーションから Service Bus メッセージング機能を使用する方法について説明します。 これらのメッセージング機能には、キュー、トピックの発行/サブスクライブなどが含まれます。 [関連記事](service-bus-amqp-dotnet.md)では、Azure Service Bus .NET API を使用して同じ作業を実行する方法について説明しています。 これらの 2 つの記事を併せて使用すると、Advanced Message Queuing Protocol (AMQP) 1.0 を使用したクロスプラットフォーム メッセージングについて学習できます。

AMQP 1.0 は、堅牢なクロス プラットフォーム メッセージング アプリケーションを作成するために使用できる、効率的で信頼性の高い回線レベルのメッセージング プロトコルです。

Service Bus での AMQP 1.0 のサポートにより、仲介型メッセージング機能 (キュー、およびトピックの発行やサブスクライブ) をさまざまなプラットフォームから効率的なバイナリ プロトコルを使って利用できます。 また、さまざまな言語、フレームワーク、オペレーティング システムを使って作成されたコンポーネントで構成されるアプリケーションを作成できます。

## <a name="get-started-with-service-bus"></a>Service Bus の概要

この記事は、`basicqueue` という名前のキューが含まれている Service Bus 名前空間が既にあることを前提としています。 まだない場合は、[Azure portal](https://portal.azure.com) を使用して[名前空間とキューを作成する](service-bus-create-namespace-portal.md)ことができます。 Service Bus 名前空間とキューの作成方法の詳細については、「[Service Bus キューの使用](service-bus-dotnet-get-started-with-queues.md)」を参照してください。

> [!NOTE]
> パーティション分割されたキューおよびトピックも AMQP をサポートします。 詳細については、「[パーティション分割されたメッセージング エンティティ](service-bus-partitioning.md)」と「[パーティション分割された Service Bus のキューおよびトピックでの AMQP 1.0 のサポート](./service-bus-amqp-protocol-guide.md)」を参照してください。
> 
> 

## <a name="download-the-amqp-10-jms-client-library"></a>AMQP 1.0 JMS クライアント ライブラリをダウンロードする

Apache Qpid JMS AMQP 1.0 クライアント ライブラリの最新バージョンをダウンロードする場所については、[Apache Qpid のダウンロード サイト](https://qpid.apache.org/download.html)をご覧ください。

Service Bus を使用する JMS アプリケーションをビルドおよび実行するときは、Apache Qpid JMS AMQP 1.0 ディストリビューション アーカイブの以下の JAR ファイルを Java CLASSPATH 環境変数に追加する必要があります。

* geronimo-jms\_1.1\_spec-1.0.jar
* qpid-jms-client-[version].jar

> [!NOTE]
> JMS JAR 名およびバージョンが変更されている可能性があります。 詳細については、[Qpid JMS AMQP 1.0](https://qpid.apache.org/maven.html#qpid-jms-amqp-10) のページをご覧ください。

## <a name="code-java-applications"></a>Java アプリケーションのコーディング

### <a name="java-naming-and-directory-interface"></a>Java Naming and Directory Interface

JMS では、Java Naming and Directory Interface (JNDI) を使用して論理名と物理名が関連付けられます。 JNDI を使用して、2 種類の JMS オブジェクトが解決されます。**ConnectionFactory** と **Destination** です。 JNDI で使用されるプロバイダー モデルでは、さまざまなディレクトリ サービスに接続して、名前解決のタスクを処理できます。 Apache Qpid JMS AMQP 1.0 ライブラリには、次の形式のプロパティ ファイルを使用して構成されるシンプルなプロパティ ファイルベースの JNDI プロバイダーが付属しています。

```TEXT
# servicebus.properties - sample JNDI configuration

# Register a ConnectionFactory in JNDI using the form:
# connectionfactory.[jndi_name] = [ConnectionURL]
connectionfactory.SBCF = amqps://[SASPolicyName]:[SASPolicyKey]@[namespace].servicebus.windows.net

# Register some queues in JNDI using the form
# queue.[jndi_name] = [physical_name]
# topic.[jndi_name] = [physical_name]
queue.QUEUE = queue1
```

#### <a name="set-up-jndi-context-and-configure-the-connectionfactory-object"></a>JNDI コンテキストのセットアップと ConnectionFactory オブジェクトの構成

参照されている接続文字列は、[Azure portal](https://portal.azure.com) にある [共有アクセス ポリシー] の **[プライマリ接続文字列]** で使用可能なものです。

```java
// The connection string builder is the only part of the azure-servicebus SDK library
// we use in this JMS sample and for the purpose of robustly parsing the Service Bus 
// connection string. 
ConnectionStringBuilder csb = new ConnectionStringBuilder(connectionString);
        
// Set up JNDI context
Hashtable<String, String> hashtable = new Hashtable<>();
hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + "?amqp.idleTimeout=120000&amqp.traceFrames=true");
hashtable.put("queue.QUEUE", "BasicQueue");
hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
Context context = new InitialContext(hashtable);

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");

// Look up queue
Destination queue = (Destination) context.lookup("QUEUE");
```

#### <a name="configure-producer-and-consumer-destination-queues"></a>プロデューサーとコンシューマーの送信先キューの構成

Qpid プロパティ ファイルの JNDI プロバイダーで送信先の定義に使用するエントリは、次のような形式になります。

プロデューサーの送信先キューを作成するには
```java
String queueName = "queueName";
Destination queue = (Destination) queueName;

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
Connection connection - cf.createConnection(csb.getSasKeyName(), csb.getSasKey());

Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

// Create producer
MessageProducer producer = session.createProducer(queue);
```

コンシューマーの送信先キューを作成するには
```java
String queueName = "queueName";
Destination queue = (Destination) queueName;

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
Connection connection - cf.createConnection(csb.getSasKeyName(), csb.getSasKey());

Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

// Create consumer
MessageConsumer consumer = session.createConsumer(queue);
```

### <a name="write-the-jms-application"></a>JMS アプリケーションの記述

JMS と Service Bus を使用するときに特殊な API やオプションは必要ありません。 この後で取り上げるようないくつかの制限があります。 いずれの JMS アプリケーションでも、まず、**ConnectionFactory** オブジェクトと送信先の解決が可能になるように JNDI 環境を構成する必要があります。

#### <a name="configure-the-jndi-initialcontext-object"></a>JNDI InitialContext オブジェクトの構成

JNDI 環境を構成するには、構成情報のハッシュ テーブルを javax.naming.InitialContext クラスのコンストラクターに渡します。 ハッシュ テーブル内の 2 つの必須の要素は Initial Context Factory と Provider URL です。 次のコードでは、Qpid プロパティ ファイルベースの JNDI プロバイダーと、**servicebus.properties** という名前のプロパティ ファイルを使用して、JNDI 環境を構成する方法を示しています。

```java
// Set up JNDI context
Hashtable<String, String> hashtable = new Hashtable<>();
hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + \
"?amqp.idleTimeout=120000&amqp.traceFrames=true");
hashtable.put("queue.QUEUE", "BasicQueue");
hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
Context context = new InitialContext(hashtable);
``` 

### <a name="a-simple-jms-application-that-uses-a-service-bus-queue"></a>Service Bus キューを使用するシンプルな JMS アプリケーション

次のサンプル プログラムでは、JNDI 論理名が QUEUE の Service Bus キューに JMS テキスト メッセージを送信し、折り返しそれらのメッセージを受信します。

[Azure Service Bus サンプル JMS キュー クイック スタート](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/qpid-jms-client/JmsQueueQuickstart)から、すべてのソース コードと構成情報にアクセスできます。

```java
// Copyright (c) Microsoft. All rights reserved.
// Licensed under the MIT license. See LICENSE file in the project root for full license information.

package com.microsoft.azure.servicebus.samples.jmsqueuequickstart;

import com.microsoft.azure.servicebus.primitives.ConnectionStringBuilder;
import org.apache.commons.cli.*;
import org.apache.log4j.*;

import javax.jms.*;
import javax.naming.Context;
import javax.naming.InitialContext;
import java.util.Hashtable;
import java.util.concurrent.atomic.AtomicInteger;
import java.util.function.Function;

/**
 * This sample demonstrates how to send messages from a JMS queue producer into
 * an Azure Service Bus queue and receive them with a JMS message consumer.
 * JMS queue. 
 */
public class JmsQueueQuickstart {

    // Number of messages to send
    private static int totalSend = 10;
    //Tracking counter for how many messages have been received; used as termination condition
    private static AtomicInteger totalReceived = new AtomicInteger(0);
    // log4j logger 
    private static Logger logger = Logger.getRootLogger();

    public void run(String connectionString) throws Exception {

        // The connection string builder is the only part of the azure-servicebus SDK library
        // we use in this JMS sample and for the purpose of robustly parsing the Service Bus 
        // connection string. 
        ConnectionStringBuilder csb = new ConnectionStringBuilder(connectionString);
        
        // Set up JNDI context
        Hashtable<String, String> hashtable = new Hashtable<>();
        hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + "?amqp.idleTimeout=120000&amqp.traceFrames=true");
        hashtable.put("queue.QUEUE", "BasicQueue");
        hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
        Context context = new InitialContext(hashtable);
        ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
        
        // Look up queue
        Destination queue = (Destination) context.lookup("QUEUE");

        // We create a scope here so we can use the same set of local variables cleanly 
        // again to show the receive side separately with minimal clutter.
        {
            // Create connection
            Connection connection = cf.createConnection(csb.getSasKeyName(), csb.getSasKey());
            // Create session, no transaction, client ack
            Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

            // Create producer
            MessageProducer producer = session.createProducer(queue);

            // Send messages
            for (int i = 0; i < totalSend; i++) {
                BytesMessage message = session.createBytesMessage();
                message.writeBytes(String.valueOf(i).getBytes());
                producer.send(message);
                System.out.printf("Sent message %d.\n", i + 1);
            }

            producer.close();
            session.close();
            connection.stop();
            connection.close();
        }

        {
            // Create connection
            Connection connection = cf.createConnection(csb.getSasKeyName(), csb.getSasKey());
            connection.start();
            // Create session, no transaction, client ack
            Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
            // Create consumer
            MessageConsumer consumer = session.createConsumer(queue);
            // Create a listener callback to receive the messages
            consumer.setMessageListener(message -> {
                try {
                    // Received message is passed to callback
                    System.out.printf("Received message %d with sq#: %s\n",
                            totalReceived.incrementAndGet(), // increments the tracking counter
                            message.getJMSMessageID());
                    message.acknowledge();
                } catch (Exception e) {
                    logger.error(e);
                }
            });

            // Wait on the main thread until all sent messages have been received
            while (totalReceived.get() < totalSend) {
                Thread.sleep(1000);
            }
            consumer.close();
            session.close();
            connection.stop();
            connection.close();
        }

        System.out.printf("Received all messages, exiting the sample.\n");
        System.out.printf("Closing queue client.\n");
    }

    public static void main(String[] args) {

        System.exit(runApp(args, (connectionString) -> {
            JmsQueueQuickstart app = new JmsQueueQuickstart();
            try {
                app.run(connectionString);
                return 0;
            } catch (Exception e) {
                System.out.printf("%s", e.toString());
                return 1;
            }
        }));
    }

    static final String SB_SAMPLES_CONNECTIONSTRING = "SB_SAMPLES_CONNECTIONSTRING";

    public static int runApp(String[] args, Function<String, Integer> run) {
        try {

            String connectionString = null;

            // Parse connection string from command line
            Options options = new Options();
            options.addOption(new Option("c", true, "Connection string"));
            CommandLineParser clp = new DefaultParser();
            CommandLine cl = clp.parse(options, args);
            if (cl.getOptionValue("c") != null) {
                connectionString = cl.getOptionValue("c");
            }

            // Get overrides from the environment
            String env = System.getenv(SB_SAMPLES_CONNECTIONSTRING);
            if (env != null) {
                connectionString = env;
            }

            if (connectionString == null) {
                HelpFormatter formatter = new HelpFormatter();
                formatter.printHelp("run jar with", "", options, "", true);
                return 2;
            }
            return run.apply(connectionString);
        } catch (Exception e) {
            System.out.printf("%s", e.toString());
            return 3;
        }
    }
}
```

### <a name="run-the-application"></a>アプリケーションの実行

共有アクセス ポリシーから **接続文字列** を渡してアプリケーションを実行します。
次の出力は、アプリケーションを実行するフォームです。

```Output
> mvn clean package
>java -jar ./target/jmsqueuequickstart-1.0.0-jar-with-dependencies.jar -c "<CONNECTION_STRING>"

Sent message 1.
Sent message 2.
Sent message 3.
Sent message 4.
Sent message 5.
Sent message 6.
Sent message 7.
Sent message 8.
Sent message 9.
Sent message 10.
Received message 1 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-1
Received message 2 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-2
Received message 3 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-3
Received message 4 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-4
Received message 5 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-5
Received message 6 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-6
Received message 7 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-7
Received message 8 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-8
Received message 9 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-9
Received message 10 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-10
Received all messages, exiting the sample.
Closing queue client.

```

## <a name="amqp-disposition-and-service-bus-operation-mapping"></a>AMQP の disposition と Service Bus 操作のマッピング

AMQP の disposition が Service Bus 操作にどのように変換されるかを次に示します。

```Output
ACCEPTED = 1; -> Complete()
REJECTED = 2; -> DeadLetter()
RELEASED = 3; (just unlock the message in service bus, will then get redelivered)
MODIFIED_FAILED = 4; -> Abandon() which increases delivery count
MODIFIED_FAILED_UNDELIVERABLE = 5; -> Defer()
```

## <a name="jms-topics-vs-service-bus-topics"></a>JMS トピックと Service Bus トピック

JMS API を通じて Service Bus のトピックとサブスクリプションを使用すると、基本的な送受信機能が提供されます。 これは、JMS 準拠の API を使用して他のメッセージ ブローカーからアプリケーションを移植する場合に便利な選択肢となります。ただし、Service Bus トピックは JMS トピックとは異なるため、多少の調整が必要となります。

Service Bus トピックは、Azure Resource Management インターフェイス、Azure コマンド ライン ツール、または Azure portal を通じて管理される、名前の付いた、共有の、永続的サブスクリプションにメッセージをルーティングします。 各サブスクリプションでは、最大 2,000 の選択ルールを使用できます。それぞれでフィルター条件を使用できるほか、SQL フィルターについては、メタデータ変換アクションも使用できます。 フィルター条件が一致するごとに、サブスクリプション内にコピーされる入力メッセージが選択されます。  

サブスクリプションからのメッセージ受信は、キューからのメッセージ受信と同じです。 各サブスクリプションには配信不能キューが関連付けられ、別のキューやトピックにメッセージを自動的に転送することができます。

JMS トピックでは、非永続的および永続的サブスクライバーをクライアントが動的に作成できます。それらでは、メッセージ セレクターを使用してメッセージをフィルター処理することもできます。 これらの非共有エンティティは、Service Bus ではサポートされていません。 Service Bus の SQL フィルター ルール構文は、JMS でサポートされているメッセージ セレクター構文と似ています。

JMS トピックのパブリッシャー側は (この例で示すように) Service Bus と互換性がありますが、動的サブスクライバーについてはありません。 次のトポロジ関連 JMS API は、Service Bus ではサポートされていません。

## <a name="unsupported-features-and-restrictions"></a>サポートされていない機能および制限

Service Bus で AMQP 1.0 を介して JMS を使用する場合は、次の制限があります。

* セッションごとに許可される **MessageProducer** または **MessageConsumer** オブジェクトは 1 つだけです。 アプリケーションで複数の **MessageProducer** または **MessageConsumer** を作成する必要がある場合は、それぞれに専用のセッションを作成してください。
* 揮発性トピック サブスクリプションは現在サポートされていません。
* **MessageSelector** オブジェクトは現在サポートされていません。
* 分散トランザクションはサポートされませんが、トランザクション セッションはサポートされます。

Service Bus では、データ プレーンからコントロール プレーンが分離されるため、JMS の動的トポロジ関数のいくつかがサポートされていません。

| サポートされていないメソッド          | 置換後の文字列                                                                             |
|-----------------------------|------------------------------------------------------------------------------------------|
| createDurableSubscriber     | メッセージ セレクタを移植するトピック サブスクリプションを作成します。                                |
| createDurableConsumer       | メッセージ セレクタを移植するトピック サブスクリプションを作成します。                                |
| createSharedConsumer        | Service Bus トピックは常に共有可能です。 「JMS トピックと Service Bus トピック」のセクションを参照してください。                                    |
| createSharedDurableConsumer | Service Bus トピックは常に共有可能です。 「JMS トピックと Service Bus トピック」のセクションを参照してください。                                      |
| createTemporaryTopic        | *AutoDeleteOnIdle* に有効期間を設定して、management API、ツール、またはポータルを使用してトピックを作成します。 |
| createTopic                 | management API、ツール、またはポータルを使用してトピックを作成します。                                         |
| unsubscribe                 | management API、ツール、またはポータルを使用してトピックを削除します。                                            |
| createBrowser               | サポートされていません。 Service Bus API の Peek() 機能を使用してください。                         |
| createQueue                 | management API、ツール、またはポータルを使用してキューを作成します。                                           | 
| createTemporaryQueue        | *AutoDeleteOnIdle* に有効期間を設定して、management API、ツール、またはポータルを使用してキューを作成します。 |
| receiveNoWait               | Service Bus SDK によって提供される receive() メソッドを使用し、非常に短いまたはゼロのタイムアウトを指定します。 |

## <a name="summary"></a>まとめ

この記事では、一般的な JMS API と AMQP 1.0 を使って Java から Service Bus の仲介型メッセージング機能 (キュー、およびトピックの発行やサブスクライブ) を使用する方法について説明しました。

Service Bus AMQP 1.0 のサポートは、.NET、C、Python、PHP など、その他の言語からも使用できます。 Service Bus で AMQP 1.0 のサポートを使用すると、これらのさまざまな言語を使って作成されたコンポーネントで高い信頼性と十分な忠実度のメッセージ交換が実現されます。

## <a name="next-steps"></a>次のステップ

* [Azure Service Bus での AMQP 1.0 サポート](service-bus-amqp-overview.md)
* [Service Bus .NET API で AMQP 1.0 を使用する](./service-bus-amqp-dotnet.md)
* [Service Bus AMQP 1.0: 開発者ガイド](service-bus-amqp-dotnet.md)
* [Service Bus キューの使用](service-bus-dotnet-get-started-with-queues.md)
* [Java デベロッパー センター](https://azure.microsoft.com/develop/java/)