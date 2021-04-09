---
title: Apache Kafka MirrorMaker を使用する - Azure Event Hubs | Microsoft Docs
description: この記事では、Kafka MirrorMaker を使用して Azure Event Hubs で Kafka クラスターをミラーリングする方法について説明します。
ms.topic: how-to
ms.date: 01/04/2021
ms.openlocfilehash: 654e9e19dfde0d0c58d00e41cf8ab0ba8e1484d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97860999"
---
# <a name="use-apache-kafka-mirrormaker-with-event-hubs"></a>Event Hubs で Apache Kafka MirrorMaker を使用する

このチュートリアルでは、Kafka MirrorMaker を使用して Azure Event Hub に Kafka ブローカーをミラーリングする方法について説明します。 CNCF Strimzi オペレーターを使用して Kubernetes で Apache Kafka をホストしている場合は、[こちらのブログ記事](https://strimzi.io/blog/2020/06/09/mirror-maker-2-eventhub/)に記載されているチュートリアルを参照してください。そこでは、Strimzi と Mirror Maker 2 を使用して Kafka を設定する方法について説明しています。 

   ![Kafka MirrorMaker とイベント ハブ](./media/event-hubs-kafka-mirror-maker-tutorial/evnent-hubs-mirror-maker1.png)

> [!NOTE]
> このサンプルは [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker) で入手できます。

> [!NOTE]
> この記事には、Microsoft が使用しなくなった "*ホワイトリスト*" という用語への言及があります。 ソフトウェアからこの用語が削除された時点で、この記事から削除します。

このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
> * Event Hubs 名前空間を作成します
> * サンプル プロジェクトを複製する
> * Kafka クラスターを設定する
> * Kafka MirrorMaker を構成する
> * Kafka MirrorMaker を実行する

## <a name="introduction"></a>はじめに
このチュートリアルでは、イベント ハブと Kafka MirrorMaker を使用し、Kafka 入力ストリームを Event Hubs サービスに "ミラーリング" することで、Azure に既存の Kafka パイプラインを統合する方法について説明します。この方法により、いくつかの[フェデレーション パターン](event-hubs-federation-overview.md)を使用した、Apache Kafka ストリームの統合が可能になります。 

Azure Event Hubs Kafka エンドポイントでは、Kafka プロトコル (つまり Kafka クライアント) を使って Azure Event Hubs に接続できます。 Kafka アプリケーションに最小限の変更を行うことで、Azure Event Hubs に接続し、Azure エコシステムの利点を活用することができます。 Event Hubs は現在、Apache Kafka バージョン 1.0 以降のプロトコルをサポートしています。

Apache Kafka の MirrorMaker 1 は、Apache Kafka から Event Hubs に一方向で使用することができます。 MirrorMaker 2 は双方向で使用できますが、[MirrorMaker 2 で構成可能な `MirrorCheckpointConnector` と `MirrorHeartbeatConnector`](https://cwiki.apache.org/confluence/display/KAFKA/KIP-382%3A+MirrorMaker+2.0) は、両方とも、Event Hubs ではなく、Apache Kafka ブローカーをポイントするように構成する必要があります。 このチュートリアルでは、MirrorMaker 1 の構成について説明します。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下のものが必要です。

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

Event Hubs サービスとの間で送受信を行うには、イベント ハブの名前空間が必要です。 名前空間とイベント ハブを作成する手順については、[イベント ハブの作成](event-hubs-create.md)に関するページを参照してください。 後で使うので、イベント ハブの接続文字列をコピーしておきます。

## <a name="clone-the-example-project"></a>サンプル プロジェクトを複製する

Event Hubs の接続文字列が用意できたので、Kafka 用 Azure Event Hubs リポジトリを複製し、`mirror-maker` サブフォルダーに移動します。

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/mirror-maker
```

## <a name="set-up-a-kafka-cluster"></a>Kafka クラスターを設定する

[Kafka クイック スタート ガイド](https://kafka.apache.org/quickstart)を使って、必要な設定でクラスターをセットアップします (または、既存の Kafka クラスターを使います)。

## <a name="configure-kafka-mirrormaker"></a>Kafka MirrorMaker を構成する

Kafka MirrorMaker を使うと、ストリームを "ミラーリング" できます。 ミラー元とミラー先の Kafka クラスターを指定すると、MirrorMaker は、ミラー元クラスターに送信されたメッセージを、ミラー元とミラー先の両方のクラスターが受信するようにします。 この例では、ミラー元の Kafka クラスターをミラー先のイベント ハブにミラーリングする方法を示します。 このシナリオを使って、データのフローを中断することなく、既存の Kafka パイプラインからイベント ハブにデータを送信できます。 

Kafka MirrorMaker について詳しくは、[Kafka ミラーリング/MirrorMaker ガイド](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330)に関するページをご覧ください。

Kafka MirrorMaker を構成するには、Kafka クラスターをコンシューマーつまりミラー元として指定し、イベント ハブをプロデューサーつまりミラー先として指定します。

#### <a name="consumer-configuration"></a>コンシューマーの構成

コンシューマー構成ファイル `source-kafka.config` を更新し、ミラー元 Kafka クラスターのプロパティを MirrorMaker に提供します。

##### <a name="source-kafkaconfig"></a>source-kafka.config

```
bootstrap.servers={SOURCE.KAFKA.IP.ADDRESS1}:{SOURCE.KAFKA.PORT1},{SOURCE.KAFKA.IP.ADDRESS2}:{SOURCE.KAFKA.PORT2},etc
group.id=example-mirrormaker-group
exclude.internal.topics=true
client.id=mirror_maker_consumer
```

#### <a name="producer-configuration"></a>プロデューサーの構成

次に、プロデューサー構成ファイル `mirror-eventhub.config` を更新し、複製された (つまり "ミラーリングされた") データを Event Hubs サービスに送るよう MirrorMaker に指示します。 具体的には、`bootstrap.servers` と `sasl.jaas.config` を、Event Hubs Kafka エンドポイントを指すように変更します。 Event Hubs サービスにはセキュリティで保護された (SASL) 通信が必要であり、これは次の構成の最後の 3 つのプロパティを設定することによって実現します。 

##### <a name="mirror-eventhubconfig"></a>mirror-eventhub.config

```
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
client.id=mirror_maker_producer

#Required for Event Hubs
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

> [!IMPORTANT]
> `{YOUR.EVENTHUBS.CONNECTION.STRING}` を Event Hubs 名前空間への接続文字列に置き換えます。 接続文字列を取得する手順については、「[Event Hubs の接続文字列の取得](event-hubs-get-connection-string.md)」を参照してください。 構成の例には、`sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://mynamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXX";` などがあります。

## <a name="run-kafka-mirrormaker"></a>Kafka MirrorMaker を実行する

新しく更新した構成ファイルを使って、Kafka のルート ディレクトリから Kafka MirrorMaker スクリプトを実行します。 構成ファイルを Kafka のルート ディレクトリにコピーするか、または次のコマンドを使って構成ファイルのパスを更新します。

```shell
bin/kafka-mirror-maker.sh --consumer.config source-kafka.config --num.streams 1 --producer.config mirror-eventhub.config --whitelist=".*"
```

イベント ハブにイベントが届いていることを確認するには、[Azure portal](https://azure.microsoft.com/features/azure-portal/) でイングレス統計を確認するか、またはイベント ハブに対してコンシューマーを実行します。

MirrorMaker が実行されている場合、ミラー元の Kafka クラスターに送信されたイベントはすべて、Kafka クラスターとミラーリングされたイベント ハブの両方によって受信されます。 MirrorMaker と Event Hubs Kafka エンドポイントを使うと、既存のクラスターを変更したり、実行中のデータ フローを中断したりすることなく、既存の Kafka パイプラインを、マネージド Azure Event Hubs サービスに移行できます。

## <a name="samples"></a>サンプル
GitHub の次のサンプルを参照してください。

- [GitHub 上のこのチュートリアルに対するサンプル コード](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker)
- [Azure コンテナー インスタンスで実行されている Azure Event Hubs Kafka MirrorMaker](https://github.com/djrosanova/EventHubsMirrorMaker)

## <a name="next-steps"></a>次のステップ
Kafka 用 Event Hubs の詳細については、次の記事を参照してください。  

- [Apache Spark をイベント ハブに接続する](event-hubs-kafka-spark-tutorial.md)
- [Apache Flink をイベント ハブに接続する](event-hubs-kafka-flink-tutorial.md)
- [Kafka Connect をイベント ハブと統合する](event-hubs-kafka-connect-tutorial.md)
- [GitHub 上でサンプルを調べる](https://github.com/Azure/azure-event-hubs-for-kafka)
- [Akka Streams をイベント ハブに接続する](event-hubs-kafka-akka-streams-tutorial.md)
- [Azure Event Hubs のための Apache Kafka 開発者ガイド](apache-kafka-developer-guide.md)