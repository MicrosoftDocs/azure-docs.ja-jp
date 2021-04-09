---
title: Java Message Service (JMS) アプリケーションを Apache ActiveMQ から Azure Service Bus に移行する |Microsoft Docs
description: この記事では、Apache ActiveMQ を操作する既存の JMS アプリケーションを移行して、Azure Service Bus を操作する方法について説明します。
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/07/2020
ms.author: aschhab
ms.custom: devx-track-java
ms.openlocfilehash: b8408dde86d1902cf5b4899c4783c9dd185449ee
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92515748"
---
# <a name="migrate-existing-java-message-service-jms-20-applications-from-apache-activemq-to-azure-service-bus"></a>既存の Java Message Service (JMS) 2.0 アプリケーションを Apache ActiveMQ から Azure Service Bus に移行する

この記事では、JMS ブローカーを操作する既存の Java Message Service (JMS) 2.0 アプリケーションを変更して、代わりに Azure Service Bus を操作する方法について説明します。 具体的には、Apache ActiveMQ または Amazon MQ から移行する方法について説明します。

Azure Service Bus では、Advanced Message Queuing Protocol (AMQP) を介して JMS 2.0 API を使用する Java 2 Platform, Enterprise Edition および Spring のワークロードがサポートされています。

## <a name="before-you-start"></a>開始する前に

### <a name="differences-between-azure-service-bus-and-apache-activemq"></a>Azure Service Bus と Apache ActiveMQ との相違点

Azure Service Bus と Apache ActiveMQ は両方とも、クライアント アプリケーションがメッセージを送受信するための JMS プロバイダーとして機能するメッセージ ブローカーです。 どちらの場合も、キューでのポイントツーポイント セマンティクスと、トピックおよびサブスクリプションでのパブリッシュ/サブスクライブ セマンティクスが有効になります。 

それでも、次の表に示すように、両者にはいくつかの違いがあります。

| カテゴリ | ActiveMQ | Azure Service Bus |
| --- | --- | --- |
| アプリケーションの階層化 | クラスター化モノリス | 2 層 <br> (ゲートウェイ + バックエンド) |
| プロトコルのサポート | <ul> <li>AMQP</li> <li> STOMP </li> <li> OpenWire </li> </ul> | AMQP |
| プロビジョニング モード | <ul> <li> サービスとしてのインフラストラクチャ (IaaS)、オンプレミス </li> <li> Amazon MQ (マネージド PaaS) </li> | マネージド PaaS (サービスとしてのプラットフォーム) |
| メッセージ サイズ | ユーザーが構成可能 | 1 MB (Premium レベル) |
| 高可用性 | お客様による管理 | プラットフォーム管理 |
| 障害復旧 | お客様による管理 | プラットフォーム管理 | 

### <a name="current-supported-and-unsupported-features"></a>現在サポートされている機能とサポートされていない機能

[!INCLUDE [service-bus-jms-features-list](../../includes/service-bus-jms-feature-list.md)]

### <a name="considerations"></a>考慮事項

2 層を持つという Azure Service Bus の性質によって、さまざまな事業継続機能 (高可用性とディザスター リカバリー) が提供されます。 ただし、JMS 機能を使用する際には、いくつかの点を考慮する必要があります。

#### <a name="service-upgrades"></a>サービスのアップグレード

Service Bus をアップグレードおよび再起動すると、一時キューまたはトピックが削除されます。 アプリケーションが一時キューまたはトピックのデータ損失の影響を受ける場合は、一時キューおよびトピックを使用しないでください。 代わりに、永続キュー、トピック、およびサブスクリプションを使用してください。

#### <a name="data-migration"></a>データ移行

Azure Service Bus を操作するためのクライアント アプリケーションを移行および変更した場合でも、ActiveMQ に保持されているデータは Service Bus には移行されません。 ActiveMQ のキュー、トピック、およびサブスクリプションをドレインし、メッセージを Service Bus のキュー、トピック、およびサブスクリプションに再生する際に、カスタム アプリケーションが必要になる場合があります。

#### <a name="authentication-and-authorization"></a>認証と権限承認

Azure Active Directory によってサポートされる Azure のロール ベースのアクセス制御 (Azure RBAC) は、Service Bus に適した認証メカニズムです。 ただし、現在、Azure RBAC またはクレームベースの認証は Apache QPID JMS によってサポートされていないため、認証には SAS キーを使用する必要があります。

## <a name="pre-migration"></a>移行前

### <a name="version-check"></a>バージョン チェック

JMS アプリケーションの作成時には、次のコンポーネントとバージョンを使用します。 

| コンポーネント | Version |
|---|---|
| Java Message Service (JMS) API | 1.1 以上 |
| AMQP プロトコル | 1.0 |

### <a name="ensure-that-amqp-ports-are-open"></a>AMQP ポートが開いていることを確認する

Service Bus では、AMQP プロトコルを介した通信がサポートされます。 このためには、ポート 5671 (AMQP) と 443 (TCP) を介した通信を有効にします。 クライアント アプリケーションがホストされている場所によっては、これらのポートを介した通信を許可するためのサポート チケットが必要になる場合があります。

> [!IMPORTANT]
> Service Bus では、AMQP 1.0 プロトコルのみがサポートされています。

### <a name="set-up-enterprise-configurations"></a>エンタープライズ構成を設定する

Service Bus によって、さまざまなエンタープライズ セキュリティおよび高可用性機能が有効になります。 詳細については、次を参照してください。 

  * [仮想ネットワーク サービス エンドポイント](service-bus-service-endpoints.md)
  * [ファイアウォール](service-bus-ip-filtering.md)
  * [カスタマー マネージド キー (BYOK) を使用したサービス側の暗号化](configure-customer-managed-key.md)
  * [プライベート エンドポイント](private-link-service.md)
  * [認証と権限承認](service-bus-authentication-and-authorization.md)

### <a name="monitoring-alerts-and-tracing"></a>監視、アラート、トレース

Service Bus 名前空間ごとに、Azure Monitor にメトリックを発行します。 これらのメトリックを使用して、名前空間に割り当てられたリソースのアラートと動的スケーリングを行うことができます。

さまざまなメトリックの詳細と、それらに対するアラートの設定方法については、「[Azure Monitor での Service Bus メトリック](service-bus-metrics-azure-monitor.md)」をご覧ください。 また、データ操作のクライアント側のトレースについては[こちら](service-bus-end-to-end-tracing.md)を、管理操作の操作/診断ログについては[こちら](service-bus-diagnostic-logs.md)をご覧ください。

### <a name="metrics---new-relic"></a>メトリック - New Relic

ActiveMQ のどのメトリックが Azure Service Bus のどのメトリックにマップされるかを関連付けることができます。 New Relic の Web サイトで以下をご覧ください。

  * [ActiveMQ/Amazon MQ New Relic メトリック](https://docs.newrelic.com/docs/integrations/amazon-integrations/aws-integrations-list/aws-mq-integration)
  * [Azure Service Bus New Relic メトリック](https://docs.newrelic.com/docs/integrations/microsoft-azure-integrations/azure-integrations-list/azure-service-bus-monitoring-integration)

> [!NOTE]
> 現在、New Relic では ActiveMQ との直接的でシームレスな統合は実現されていませんが、Amazon MQ に使用できるメトリックが用意されています。 Amazon MQ は ActiveMQ から派生しているため、次の表では、AmazonMQ の New Relic メトリックを Azure Service Bus にマップしています。
>

|メトリックのグループ化| Amazon MQ/ActiveMQ のメトリック | Azure Service Bus のメトリック |
|------------|---------------------------|--------------------------|
|ブローカー|`CpuUtilization`|`CPUXNS`|
|ブローカー|`MemoryUsage`|`WSXNS`|
|ブローカー|`CurrentConnectionsCount`|`activeConnections`|
|ブローカー|`EstablishedConnectionsCount`|`activeConnections` + `connectionsClosed`|
|ブローカー|`InactiveDurableTopicSubscribersCount`|サブスクリプションのメトリックを使用する|
|ブローカー|`TotalMessageCount`|キュー/トピック/サブスクリプション レベルの `activeMessages` を使用する|
|キュー/トピック|`EnqueueCount`|`incomingMessages`|
|キュー/トピック|`DequeueCount`|`outgoingMessages`|
|キュー|`QueueSize`|`sizeBytes`|



## <a name="migration"></a>移行

Service Bus を操作するために既存の JMS 2.0 アプリケーションを移行するには、以降のセクションの手順に従ってください。

### <a name="export-the-topology-from-activemq-and-create-the-entities-in-service-bus-optional"></a>ActiveMQ からトポロジをエクスポートして Service Bus でエンティティを作成する (省略可能)

クライアント アプリケーションが Service Bus にシームレスに接続できるようにするには、トポロジ (キュー、トピック、サブスクリプションを含む) を Apache ActiveMQ から Service Bus に移行します。

> [!NOTE]
> JMS アプリケーションの場合は、ランタイム操作としてキュー、トピック、およびサブスクリプションを作成します。 ほとんどの JMS プロバイダー (メッセージ ブローカー) には、これらを実行時に作成する機能が用意されています。 そのため、このエクスポート手順は省略可能としています。 アプリケーションが実行時にトポロジを作成する権限を持っていることを確認するには、SAS の `Manage` 権限を含む接続文字列を使用してください。

これを行うには、次の手順を実行します。

1. [ActiveMQ コマンド ライン ツール](https://activemq.apache.org/activemq-command-line-tools-reference) を使用してトポロジをエクスポートします。
1. [Azure Resource Manager テンプレート](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)を使用して同じトポロジを再作成します。
1. Azure Resource Manager のテンプレートを実行します。


### <a name="import-the-maven-dependency-for-service-bus-jms-implementation"></a>Service Bus JMS 実装の Maven の依存関係をインポートする

Service Bus とシームレスに接続できるようにするには、次のように、Maven `pom.xml` ファイルへの依存関係として `azure-servicebus-jms` パッケージを追加します。

```xml
<dependencies>
...
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-servicebus-jms</artifactId>
    </dependency>
...
</dependencies>
```

### <a name="application-server-configuration-changes"></a>アプリケーション サーバー構成の変更

この部分は、ActiveMQ に接続するクライアント アプリケーションをホストしているアプリケーション サーバーに合わせてカスタマイズされています。

#### <a name="tomcat"></a>Tomcat

ここでは、`/META-INF/context.xml` ファイルに示されているように、ActiveMQ に固有の構成から開始します。

```XML
<Context antiJARLocking="true">
    <Resource
        name="jms/ConnectionFactory"
        auth="Container"
        type="org.apache.activemq.ActiveMQConnectionFactory"
        description="JMS Connection Factory"
        factory="org.apache.activemq.jndi.JNDIReferenceFactory"
        brokerURL="tcp://localhost:61616"
        brokerName="LocalActiveMQBroker"
        useEmbeddedBroker="false"/>

    <Resource name="jms/topic/MyTopic"
        auth="Container"
        type="org.apache.activemq.command.ActiveMQTopic"
        factory="org.apache.activemq.jndi.JNDIReferenceFactory"
        physicalName="MY.TEST.FOO"/>
    <Resource name="jms/queue/MyQueue"
        auth="Container"
        type="org.apache.activemq.command.ActiveMQQueue"
        factory="org.apache.activemq.jndi.JNDIReferenceFactory"
        physicalName="MY.TEST.FOO.QUEUE"/>
</Context>
```

これを、次のように、Service Bus を指すように調整します。

```xml
<Context antiJARLocking="true">
    <Resource
        name="jms/ConnectionFactory"
        auth="Container"
        type="com.microsoft.azure.servicebus.jms.ServiceBusJmsConnectionFactory"
        description="JMS Connection Factory"
        factory="org.apache.qpid.jms.jndi.JNDIReferenceFactory"
        connectionString="<INSERT YOUR SERVICE BUS CONNECTION STRING HERE>"/>

    <Resource name="jms/topic/MyTopic"
        auth="Container"
        type="org.apache.qpid.jms.JmsTopic"
        factory="org.apache.qpid.jms.jndi.JNDIReferenceFactory"
        physicalName="MY.TEST.FOO"/>
    <Resource name="jms/queue/MyQueue"
        auth="Container"
        type="org.apache.qpid.jms.JmsQueue"
        factory="org.apache.qpid.jms.jndi.JNDIReferenceFactory"
        physicalName="MY.TEST.FOO.QUEUE"/>
</Context>
```

#### <a name="spring-applications"></a>Spring アプリケーション

##### <a name="update-the-applicationproperties-file"></a>`application.properties` ファイルを更新する

Spring Boot アプリケーションを使用して ActiveMQ に接続している場合は、`application.properties` ファイルから ActiveMQ 固有のプロパティを削除する必要があります。

```properties
spring.activemq.broker-url=<ACTIVEMQ BROKER URL>
spring.activemq.user=<ACTIVEMQ USERNAME>
spring.activemq.password=<ACTIVEMQ PASSWORD>
```

次に、Service Bus 固有のプロパティを `application.properties` ファイルに追加します。

```properties
azure.servicebus.connection-string=Endpoint=myEndpoint;SharedAccessKeyName=mySharedAccessKeyName;SharedAccessKey=mySharedAccessKey
```

##### <a name="replace-activemqconnectionfactory-with-servicebusjmsconnectionfactory"></a>`ActiveMQConnectionFactory` を `ServiceBusJmsConnectionFactory` に置き換えます。

次の手順では、`ActiveMQConnectionFactory` のインスタンスを `ServiceBusJmsConnectionFactory` に置き換えます。

> [!NOTE] 
> 実際にコードをどのように変更するかは、アプリケーションと、依存関係の管理方法によって異なります。次のサンプルでは、変更する必要がある内容についてのガイダンスを示します。
>

以前は、次のように、`ActiveMQConnectionFactory` のオブジェクトをインスタンス化していた可能性があります。

```java

String BROKER_URL = "<URL of the hosted ActiveMQ broker>";
ConnectionFactory factory = new ActiveMQConnectionFactory(BROKER_URL);

Connection connection = factory.createConnection();
connection.start();

```

これを、次のように、`ServiceBusJmsConnectionFactory` のオブジェクトをインスタンス化するように変更します。

```java

ServiceBusJmsConnectionFactorySettings settings = new ServiceBusJmsConnectionFactorySettings();
String SERVICE_BUS_CONNECTION_STRING = "<Service Bus Connection string>";

ConnectionFactory factory = new ServiceBusJmsConnectionFactory(SERVICE_BUS_CONNECTION_STRING, settings);

Connection connection = factory.createConnection();
connection.start();

```

## <a name="post-migration"></a>移行後

Service Bus でメッセージの送受信を開始するようにアプリケーションを変更したので、それが想定どおりに動作することを確認する必要があります。 完了したら、アプリケーション スタックをさらに調整し、最新化できます。

## <a name="next-steps"></a>次のステップ

[Azure Service Bus JMS 用の Spring Boot スターター](/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-azure-service-bus)を使用して、Service Bus とのシームレスな統合を実現します。

Service Bus メッセージングと JMS の詳細については、次をご覧ください。

* [Service Bus - JMS](service-bus-java-how-to-use-jms-api-amqp.md)
* [Service Bus のキュー、トピック、サブスクリプション](service-bus-queues-topics-subscriptions.md)
* [Service Bus キューの使用](service-bus-dotnet-get-started-with-queues.md)