---
title: Apache Kafka 用に Apache Flink を使用する - Azure Event Hubs | Microsoft Docs
description: この記事では、Apache Flink を Azure イベント ハブに接続する方法に関する情報を提供します。
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: 1d382270248e95b1b973f57f67ebf81160f03a16
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92369507"
---
# <a name="use-apache-flink-with-azure-event-hubs-for-apache-kafka"></a>Apache Kafka 用の Azure Event Hubs での Apache Flink の使用
このチュートリアルでは、プロトコル クライアントを変更したり、独自のクラスターを実行したりせずに、Apache Flink をイベント ハブに接続する方法について説明します。 Apache Kafka コンシューマー プロトコルの Event Hubs サポートの詳細については、[Apache Kafka 用の Event Hubs](event-hubs-for-kafka-ecosystem-overview.md) に関するページを参照してください。


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
* [Java Development Kit (JDK) 1.7 以降](/azure/developer/java/fundamentals/java-jdk-long-term-support)
    * Ubuntu で `apt-get install default-jdk` を実行して JDK をインストールします。
    * 必ず、JDK のインストール先フォルダーを指すように JAVA_HOME 環境変数を設定してください。
* Maven バイナリ アーカイブの[ダウンロード](https://maven.apache.org/download.cgi)と[インストール](https://maven.apache.org/install.html)
    * Ubuntu で `apt-get install maven` を実行して Maven をインストールします。
* [Git](https://www.git-scm.com/downloads)
    * Ubuntu で `sudo apt-get install git` を実行して Git をインストールします。

## <a name="create-an-event-hubs-namespace"></a>Event Hubs 名前空間を作成します

Event Hubs サービスとの間で送受信を行うには、Event Hubs 名前空間が必要です。 名前空間とイベント ハブを作成する手順については、[イベント ハブの作成](event-hubs-create.md)に関するページを参照してください。 後で使うので、イベント ハブの接続文字列をコピーしておきます。

## <a name="clone-the-example-project"></a>サンプル プロジェクトを複製する

Event Hubs の接続文字列を入手したので、Kafka 用 Azure Event Hubs リポジトリをクローンし、`flink` サブフォルダーに移動します。

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

> [!IMPORTANT]
> `{YOUR.EVENTHUBS.CONNECTION.STRING}` を Event Hubs 名前空間への接続文字列に置き換えます。 接続文字列を取得する手順については、「[Event Hubs の接続文字列の取得](event-hubs-get-connection-string.md)」を参照してください。 構成の例には、`sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://mynamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXX";` などがあります。

### <a name="run-producer-from-the-command-line"></a>コマンド ラインからプロデューサーを実行する

コマンドラインからプロデューサーを実行するには、JAR を生成し、Maven 内から実行します (または、Maven を使用して JAR を生成し、必要な Kafka JAR をクラスパスに追加することによって、Java 内で実行します)。

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="FlinkTestProducer"
```

次にプロデューサーは、トピック `test` にあるイベント ハブへのイベントの送信を開始し、それらのイベントを stdout に出力します。

## <a name="run-flink-consumer"></a>Flink コンシューマーを実行する

提供されたコンシューマーの例を使用して、イベント ハブからメッセージを受信します。 

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

> [!IMPORTANT]
> `{YOUR.EVENTHUBS.CONNECTION.STRING}` を Event Hubs 名前空間への接続文字列に置き換えます。 接続文字列を取得する手順については、「[Event Hubs の接続文字列の取得](event-hubs-get-connection-string.md)」を参照してください。 構成の例には、`sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://mynamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXX";` などがあります。


### <a name="run-consumer-from-the-command-line"></a>コマンド ラインからコンシューマーを実行する

コマンドラインからコンシューマーを実行するには、JAR を生成し、Maven 内から実行します (または、Maven を使用して JAR を生成し、必要な Kafka JAR をクラスパスに追加することによって、Java 内で実行します)。

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="FlinkTestConsumer"
```

Event Hubs にイベントがある場合 (たとえば、プロデューサーも実行されている場合)、コンシューマーはトピック `test` からのイベントの受信を開始します。

Flink を Kafka に接続する方法についての詳細は、[Flink の Kafka コネクタ ガイド](https://ci.apache.org/projects/flink/flink-docs-stable/dev/connectors/kafka.html)を調べてください。

## <a name="next-steps"></a>次のステップ
Kafka 用 Event Hubs の詳細については、次の記事を参照してください。  

- [イベント ハブ内の Kafka ブローカーをミラーリングする](event-hubs-kafka-mirror-maker-tutorial.md)
- [Apache Spark をイベント ハブに接続する](event-hubs-kafka-spark-tutorial.md)
- [Kafka Connect をイベント ハブと統合する](event-hubs-kafka-connect-tutorial.md)
- [GitHub 上でサンプルを調べる](https://github.com/Azure/azure-event-hubs-for-kafka)
- [Akka Streams をイベント ハブに接続する](event-hubs-kafka-akka-streams-tutorial.md)
- [Azure Event Hubs のための Apache Kafka 開発者ガイド](apache-kafka-developer-guide.md)