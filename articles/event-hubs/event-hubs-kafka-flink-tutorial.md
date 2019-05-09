---
title: Apache Kafka 用に Apache Flink を使用する - Azure Event Hubs | Microsoft Docs
description: この記事では、Apache Kafka が有効になっている Azure イベント ハブに Apache Flink を接続する方法について説明します。
services: event-hubs
documentationcenter: ''
author: basilhariri
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: bahariri
ms.openlocfilehash: dc4a982dde62f62eb8f2d91a61fd70ba79fa13d5
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2019
ms.locfileid: "57539979"
---
# <a name="use-apache-flink-with-azure-event-hubs-for-apache-kafka"></a>Apache Kafka 用の Azure Event Hubs での Apache Flink の使用
このチュートリアルでは、プロトコル クライアントを変更したり、独自のクラスターを実行したりせずに、Apache Flink を Kafka 対応 Event Hubs に接続する方法を示します。 Azure Event Hubs では、[Apache Kafka バージョン 1.0.](https://kafka.apache.org/10/documentation.html) がサポートされています。

Apache Kafka を使用する主な利点の 1 つは、そこから接続できるフレームワークのエコシステムにあります。 Kafka 対応 Event Hubs では、Kafka の柔軟性と、Azure エコシステムのスケーラビリティ、一貫性、およびサポートが組み合わされます。

このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
> * Event Hubs 名前空間を作成します
> * サンプル プロジェクトを複製する
> * Flink プロデューサーを実行する 
> * Flink コンシューマーを実行する

> [!NOTE]
> このサンプルは [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/flink) で入手できます。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次の前提条件を満たしている必要があります。

* [Apache Kafka 用の Event Hubs](event-hubs-for-kafka-ecosystem-overview.md) に関する記事を読む。 
* Azure サブスクリプション。 お持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)を作成してください。
* [Java Development Kit (JDK) 1.7 以降](https://aka.ms/azure-jdks)
    * Ubuntu で `apt-get install default-jdk` を実行して JDK をインストールします。
    * 必ず、JDK のインストール先フォルダーを指すように JAVA_HOME 環境変数を設定してください。
* Maven バイナリ アーカイブの[ダウンロード](https://maven.apache.org/download.cgi)と[インストール](https://maven.apache.org/install.html)
    * Ubuntu で `apt-get install maven` を実行して Maven をインストールします。
* [Git](https://www.git-scm.com/downloads)
    * Ubuntu で `sudo apt-get install git` を実行して Git をインストールします。

## <a name="create-an-event-hubs-namespace"></a>Event Hubs 名前空間を作成します

Event Hubs サービスとの間で送受信を行うには、Event Hubs 名前空間が必要です。 Event Hubs Kafka エンドポイントを取得する方法については、[Kafka 対応 Event Hubs の作成](event-hubs-create-kafka-enabled.md)に関するページをご覧ください。 後で使うので、イベント ハブの接続文字列をコピーしておきます。

## <a name="clone-the-example-project"></a>サンプル プロジェクトを複製する

Kafka が有効なイベント ハブの接続文字列を入手したので、Kafka 用 Azure Event Hubs リポジトリを複製し、`flink` サブフォルダーに移動します。

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/flink
```

## <a name="run-flink-producer"></a>Flink プロデューサーを実行する

提供された Flink プロデューサーの例を使用して、Event Hubs サービスにメッセージを送信します。

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Event Hubs Kafka エンドポイントを指定する

#### <a name="producerconfig"></a>producer.config

`producer/src/main/resources/producer.config` の `bootstrap.servers` 値と `sasl.jaas.config` 値を更新し、正しい認証を使用してプロデューサーを Event Hubs Kafka エンドポイントに転送します。

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
client.id=FlinkExampleProducer
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required \
   username="$ConnectionString" \
   password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

### <a name="run-producer-from-the-command-line"></a>コマンド ラインからプロデューサーを実行する

コマンドラインからプロデューサーを実行するには、JAR を生成し、Maven 内から実行します (または、Maven を使用して JAR を生成し、必要な Kafka JAR をクラスパスに追加することによって、Java 内で実行します)。

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="FlinkTestProducer"
```

プロデューサーは、トピック `test` にある Kafka 対応 Event Hub に対してイベントの送信を開始し、stdout にイベントを出力します。

## <a name="run-flink-consumer"></a>Flink コンシューマーを実行する

提供されたコンシューマーの例を使用して、Kafka 対応 Event Hubs からメッセージを受信します。

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Event Hubs Kafka エンドポイントを指定する

#### <a name="consumerconfig"></a>consumer.config

`consumer/src/main/resources/consumer.config` の `bootstrap.servers` 値と `sasl.jaas.config` 値を更新し、正しい認証を使用してコンシューマーを Event Hubs Kafka エンドポイントに転送します。

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
group.id=FlinkExampleConsumer
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required \
   username="$ConnectionString" \
   password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

### <a name="run-consumer-from-the-command-line"></a>コマンド ラインからコンシューマーを実行する

コマンドラインからコンシューマーを実行するには、JAR を生成し、Maven 内から実行します (または、Maven を使用して JAR を生成し、必要な Kafka JAR をクラスパスに追加することによって、Java 内で実行します)。

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="FlinkTestConsumer"
```

Kafka 対応 Event Hubs にイベントがある場合 (たとえば、プロデューサーも実行されている場合)、コンシューマーはトピック `test` からのイベントの受信を開始します。

Flink を Kafka に接続する方法についての詳細は、[Flink の Kafka コネクタ ガイド](https://ci.apache.org/projects/flink/flink-docs-stable/dev/connectors/kafka.html)を調べてください。

## <a name="next-steps"></a>次の手順
このチュートリアルでは、プロトコル クライアントを変更したり、独自のクラスターを実行したりせずに、Apache Flink を Kafka 対応 Event Hubs に接続する方法を説明しました。 このチュートリアルでは、以下の手順を実行しました。 

> [!div class="checklist"]
> * Event Hubs 名前空間を作成します
> * サンプル プロジェクトを複製する
> * Flink プロデューサーを実行する 
> * Flink コンシューマーを実行する

Event Hubs と Kafka 用 Event Hubs の詳細については、次のトピックを参照してください。  

- [Event Hubs について確認する](event-hubs-what-is-event-hubs.md)
- [Apache Kafka 用の Event Hubs](event-hubs-for-kafka-ecosystem-overview.md)
- [Kafka 対応 Event Hubs の作成方法](event-hubs-create-kafka-enabled.md)
- [Kafka アプリケーションから Event Hubs へのストリーム](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Kafka 対応イベント ハブでの Kafka ブローカーのミラーリング](event-hubs-kafka-mirror-maker-tutorial.md)
- [Kafka 対応イベント ハブへの Apache Spark の接続](event-hubs-kafka-spark-tutorial.md)
- [Kafka Connect を Kafka 対応イベント ハブに統合する](event-hubs-kafka-connect-tutorial.md)
- [Kafka 対応イベント ハブへの Akka Streams の接続](event-hubs-kafka-akka-streams-tutorial.md)
- [GitHub 上でサンプルを調べる](https://github.com/Azure/azure-event-hubs-for-kafka)
