---
title: Apache Kafka に対する Akka Streams の使用 - Azure Event Hubs| Microsoft Docs
description: この記事では、Apache Kafka が有効になっている Azure イベント ハブに Akka Streams を接続する方法について説明します。
services: event-hubs
documentationcenter: ''
author: basilhariri
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: bahariri
ms.openlocfilehash: 32d710464cf61f998e18af28887561cefd2b1b3f
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2019
ms.locfileid: "57534894"
---
# <a name="using-akka-streams-with-event-hubs-for-apache-kafka"></a>Kafka エコシステム用の Event Hubs での Apache Kafka の使用
このチュートリアルでは、プロトコル クライアントを変更したり、独自のクラスターを実行したりせずに、Akka Streams を Kafka 対応 Event Hubs に接続する方法を示します。 Kafka 用 Azure Event Hubs では、[Apache Kafka バージョン 1.0](https://kafka.apache.org/10/documentation.html) がサポートされています。

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
* [Java Development Kit (JDK) 1.8 以降](https://aka.ms/azure-jdks)
    * Ubuntu で `apt-get install default-jdk` を実行して JDK をインストールします。
    * 必ず、JDK のインストール先フォルダーを指すように JAVA_HOME 環境変数を設定してください。
* Maven バイナリ アーカイブの[ダウンロード](https://maven.apache.org/download.cgi)と[インストール](https://maven.apache.org/install.html)
    * Ubuntu で `apt-get install maven` を実行して Maven をインストールします。
* [Git](https://www.git-scm.com/downloads)
    * Ubuntu で `sudo apt-get install git` を実行して Git をインストールします。

## <a name="create-an-event-hubs-namespace"></a>Event Hubs 名前空間を作成します

Event Hubs サービスとの間で送受信を行うには、Event Hubs 名前空間が必要です。 Event Hubs Kafka エンドポイントを取得する方法については、[Kafka 対応 Event Hubs の作成](event-hubs-create-kafka-enabled.md)に関するページをご覧ください。 後で使うので、イベント ハブの接続文字列をコピーしておきます。

## <a name="clone-the-example-project"></a>サンプル プロジェクトを複製する

Kafka が有効なイベント ハブの接続文字列を入手したので、Kafka 用 Azure Event Hubs リポジトリを複製し、`akka` サブフォルダーに移動します。

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

### <a name="run-producer-from-the-command-line"></a>コマンド ラインからプロデューサーを実行する

コマンドラインからプロデューサーを実行するには、JAR を生成し、Maven 内から実行します (または、Maven を使用して JAR を生成し、必要な Kafka JAR をクラスパスに追加することによって、Java 内で実行します)。

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="AkkaTestProducer"
```

プロデューサーは、トピック `test` にある Kafka 対応 Event Hubs に対してイベントの送信を開始し、stdout にイベントを出力します。

## <a name="run-akka-streams-consumer"></a>Akka Streams コンシューマーを実行する

提供されたコンシューマーの例を使用して、Kafka 対応 Event Hubs からメッセージを受信します。

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

### <a name="run-consumer-from-the-command-line"></a>コマンド ラインからコンシューマーを実行する

コマンドラインからコンシューマーを実行するには、JAR を生成し、Maven 内から実行します (または、Maven を使用して JAR を生成し、必要な Kafka JAR をクラスパスに追加することによって、Java 内で実行します)。

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="AkkaTestConsumer"
```

Kafka 対応 Event Hubs にイベントがある場合 (たとえば、プロデューサーも実行されている場合)、コンシューマーはトピック `test` からのイベントの受信を開始します。 

Akka Streams の詳細については、[Akka Streams Kafka ガイド](https://doc.akka.io/docs/akka-stream-kafka/current/home.html)を参照してください。

## <a name="next-steps"></a>次の手順
このチュートリアルでは、プロトコル クライアントを変更したり、独自のクラスターを実行したりせずに、Akka Streams を Kafka 対応 Event Hubs に接続する方法について説明しました。 Kafka 用 Azure Event Hubs では、[Apache Kafka バージョン 1.0.](https://kafka.apache.org/10/documentation.html) がサポートされています。 このチュートリアルでは、次の操作を行いました。 

> [!div class="checklist"]
> * Event Hubs 名前空間を作成します
> * サンプル プロジェクトを複製する
> * Akka Streams プロデューサーを実行する 
> * Akka Streams コンシューマーを実行する

Event Hubs と Kafka 用 Event Hubs の詳細については、次のトピックを参照してください。  

- [Event Hubs について確認する](event-hubs-what-is-event-hubs.md)
- [Apache Kafka 用の Event Hubs](event-hubs-for-kafka-ecosystem-overview.md)
- [Kafka 対応 Event Hubs の作成方法](event-hubs-create-kafka-enabled.md)
- [Kafka アプリケーションから Event Hubs へのストリーム](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Kafka 対応イベント ハブでの Kafka ブローカーのミラーリング](event-hubs-kafka-mirror-maker-tutorial.md)
- [Kafka 対応イベント ハブへの Apache Spark の接続](event-hubs-kafka-spark-tutorial.md)
- [Kafka 対応イベント ハブへの Apache Flink の接続](event-hubs-kafka-flink-tutorial.md)
- [Kafka Connect を Kafka 対応イベント ハブに統合する](event-hubs-kafka-connect-tutorial.md)
- [GitHub 上でサンプルを調べる](https://github.com/Azure/azure-event-hubs-for-kafka)
