---
title: Apache Kafka に対する Akka Streams の使用 - Azure Event Hubs| Microsoft Docs
description: この記事では、Akka Streams を Azure イベント ハブに接続する方法に関する情報を提供します。
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: 92ab927189329493696c70b61ffc7f11cad22a66
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92369575"
---
# <a name="using-akka-streams-with-event-hubs-for-apache-kafka"></a>Kafka エコシステム用の Event Hubs での Apache Kafka の使用

このチュートリアルでは、プロトコル クライアントを変更したり、独自のクラスターを実行したりせずに、Event Hubs による Apache Kafka のサポートを使用して Akka Streams を接続する方法を示します。 

このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
> * Event Hubs 名前空間を作成します
> * サンプル プロジェクトを複製する
> * Akka Streams プロデューサーを実行する 
> * Akka Streams コンシューマーを実行する

> [!NOTE]
> このサンプルは [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/akka/java) で入手できます。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次の前提条件を満たしている必要があります。

* [Apache Kafka 用の Event Hubs](event-hubs-for-kafka-ecosystem-overview.md) に関する記事を読む。 
* Azure サブスクリプション。 お持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)を作成してください。
* [Java Development Kit (JDK) 1.8 以降](/azure/developer/java/fundamentals/java-jdk-long-term-support)
    * Ubuntu で `apt-get install default-jdk` を実行して JDK をインストールします。
    * 必ず、JDK のインストール先フォルダーを指すように JAVA_HOME 環境変数を設定してください。
* Maven バイナリ アーカイブの[ダウンロード](https://maven.apache.org/download.cgi)と[インストール](https://maven.apache.org/install.html)
    * Ubuntu で `apt-get install maven` を実行して Maven をインストールします。
* [Git](https://www.git-scm.com/downloads)
    * Ubuntu で `sudo apt-get install git` を実行して Git をインストールします。

## <a name="create-an-event-hubs-namespace"></a>Event Hubs 名前空間を作成します

Event Hubs サービスとの間で送受信を行うには、Event Hubs 名前空間が必要です。 詳細については、[イベント ハブの作成](event-hubs-create.md)に関するページを参照してください。 後で使うので、イベント ハブの接続文字列をコピーしておきます。

## <a name="clone-the-example-project"></a>サンプル プロジェクトを複製する

Event Hubs の接続文字列を入手したので、Kafka 用 Azure Event Hubs リポジトリをクローンし、`akka` サブフォルダーに移動します。

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/akka/java
```

## <a name="run-akka-streams-producer"></a>Akka Streams プロデューサーを実行する

提供された Akka Streams プロデューサーの例を使用して、Event Hubs サービスにメッセージを送信します。

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Event Hubs Kafka エンドポイントを指定する

#### <a name="producer-applicationconf"></a>プロデューサー application.conf

`producer/src/main/resources/application.conf` の `bootstrap.servers` 値と `sasl.jaas.config` 値を更新し、正しい認証を使用してプロデューサーを Event Hubs Kafka エンドポイントに転送します。

```xml
akka.kafka.producer {
    #Akka Kafka producer properties can be defined here


    # Properties defined by org.apache.kafka.clients.producer.ProducerConfig
    # can be defined in this configuration section.
    kafka-clients {
        bootstrap.servers="{YOUR.EVENTHUBS.FQDN}:9093"
        sasl.mechanism=PLAIN
        security.protocol=SASL_SSL
        sasl.jaas.config="org.apache.kafka.common.security.plain.PlainLoginModule required username=\"$ConnectionString\" password=\"{YOUR.EVENTHUBS.CONNECTION.STRING}\";"
    }
}
```

> [!IMPORTANT]
> `{YOUR.EVENTHUBS.CONNECTION.STRING}` を Event Hubs 名前空間への接続文字列に置き換えます。 接続文字列を取得する手順については、「[Event Hubs の接続文字列の取得](event-hubs-get-connection-string.md)」を参照してください。 構成の例には、`sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://mynamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXX";` などがあります。


### <a name="run-producer-from-the-command-line"></a>コマンド ラインからプロデューサーを実行する

コマンドラインからプロデューサーを実行するには、JAR を生成し、Maven 内から実行します (または、Maven を使用して JAR を生成し、必要な Kafka JAR をクラスパスに追加することによって、Java 内で実行します)。

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="AkkaTestProducer"
```

プロデューサーは、トピック `test` でイベント ハブへのイベントの送信を開始し、stdout にイベントを出力します。

## <a name="run-akka-streams-consumer"></a>Akka Streams コンシューマーを実行する

提供されたコンシューマーの例を使用して、イベント ハブからメッセージを受信します。

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Event Hubs Kafka エンドポイントを指定する

#### <a name="consumer-applicationconf"></a>コンシューマー application.conf

`consumer/src/main/resources/application.conf` の `bootstrap.servers` 値と `sasl.jaas.config` 値を更新し、正しい認証を使用してコンシューマーを Event Hubs Kafka エンドポイントに転送します。

```xml
akka.kafka.consumer {
    #Akka Kafka consumer properties defined here
    wakeup-timeout=60s

    # Properties defined by org.apache.kafka.clients.consumer.ConsumerConfig
    # defined in this configuration section.
    kafka-clients {
       request.timeout.ms=60000
       group.id=akka-example-consumer

       bootstrap.servers="{YOUR.EVENTHUBS.FQDN}:9093"
       sasl.mechanism=PLAIN
       security.protocol=SASL_SSL
       sasl.jaas.config="org.apache.kafka.common.security.plain.PlainLoginModule required username=\"$ConnectionString\" password=\"{YOUR.EVENTHUBS.CONNECTION.STRING}\";"
    }
}
```

> [!IMPORTANT]
> `{YOUR.EVENTHUBS.CONNECTION.STRING}` を Event Hubs 名前空間への接続文字列に置き換えます。 接続文字列を取得する手順については、「[Event Hubs の接続文字列の取得](event-hubs-get-connection-string.md)」を参照してください。 構成の例には、`sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://mynamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXX";` などがあります。


### <a name="run-consumer-from-the-command-line"></a>コマンド ラインからコンシューマーを実行する

コマンドラインからコンシューマーを実行するには、JAR を生成し、Maven 内から実行します (または、Maven を使用して JAR を生成し、必要な Kafka JAR をクラスパスに追加することによって、Java 内で実行します)。

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="AkkaTestConsumer"
```

イベント ハブにイベントがある場合 (たとえば、プロデューサーも実行されている場合)、コンシューマーはトピック `test` からのイベントの受信を開始します。 

Akka Streams の詳細については、[Akka Streams Kafka ガイド](https://doc.akka.io/docs/akka-stream-kafka/current/home.html)を参照してください。

## <a name="next-steps"></a>次のステップ
Kafka 用 Event Hubs の詳細については、次の記事を参照してください。  

- [イベント ハブ内の Kafka ブローカーをミラーリングする](event-hubs-kafka-mirror-maker-tutorial.md)
- [Apache Spark をイベント ハブに接続する](event-hubs-kafka-spark-tutorial.md)
- [Apache Flink をイベント ハブに接続する](event-hubs-kafka-flink-tutorial.md)
- [Kafka Connect をイベント ハブと統合する](event-hubs-kafka-connect-tutorial.md)
- [GitHub 上でサンプルを調べる](https://github.com/Azure/azure-event-hubs-for-kafka)
- [Azure Event Hubs のための Apache Kafka 開発者ガイド](apache-kafka-developer-guide.md)