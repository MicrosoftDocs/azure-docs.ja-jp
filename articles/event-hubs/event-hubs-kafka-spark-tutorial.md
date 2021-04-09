---
title: Apache Spark アプリの接続 - Azure Event Hubs | Microsoft Docs
description: この記事では、Kafka 用 Azure Event Hubs で Apache Spark を使用する方法について取り上げます。
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: 84184ed3dffee97863b93c592d1cd577df313605
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92913740"
---
# <a name="connect-your-apache-spark-application-with-azure-event-hubs"></a>Apache Spark アプリケーションを Azure Event Hubs と接続する
このチュートリアルでは、リアルタイム ストリーミングのために Spark アプリケーションを Event Hubs に接続する方法について説明します。 この統合により、お使いのプロトコル クライアントを変更せずにストリーミングを行ったり、独自の Kafka または Zookeeper クラスターを実行したりすることができます。 このチュートリアルには、Apache Spark v2.4 以上および Apache Kafka v2.0 以上が必要となります。

> [!NOTE]
> このサンプルは [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/spark/) で入手できます。

このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
> * Event Hubs 名前空間を作成します
> * サンプル プロジェクトを複製する
> * Spark を実行する
> * Kafka 用 Event Hubs から読み取る
> * Kafka 用 Event Hubs に書き込む

## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に、以下のものを用意してください。
-   Azure のサブスクリプション。 アカウントがない場合は、[無料アカウントを作成](https://azure.microsoft.com/free/)してください。
-   [Apache Spark v2.4](https://spark.apache.org/downloads.html)
-   [Apache Kafka v2.0]( https://kafka.apache.org/20/documentation.html)
-   [Git](https://www.git-scm.com/downloads)

> [!NOTE]
> Spark v2.4 以降、Spark-Kafka アダプターは、Kafka v2.0 をサポートするように更新されています。 それより前にリリースされた Spark のアダプターは、Kafka v0.10 以降をサポートしますが、厳密には Kafka v0.10 の API に依存します。 Kafka 用 Event Hubs は Kafka v0.10 をサポートしていないため、Kafka エコシステム用 Event Hubs では、バージョン v2.4 未満の Spark の Spark-Kafka アダプターがサポートされません。


## <a name="create-an-event-hubs-namespace"></a>Event Hubs 名前空間を作成します
Event Hubs サービスとの間で送受信を行うには、イベント ハブの名前空間が必要です。 名前空間とイベント ハブを作成する手順については、[イベント ハブの作成](event-hubs-create.md)に関するページを参照してください。 Event Hubs の接続文字列と完全修飾ドメイン名 (FQDN) を、後で使用するために取得します。 手順については、「[Get an Event Hubs connection string (Event Hubs の接続文字列を取得する)](event-hubs-get-connection-string.md)」を参照してください。 

## <a name="clone-the-example-project"></a>サンプル プロジェクトを複製する
Azure Event Hubs リポジトリを複製し、`tutorials/spark` サブフォルダーに移動します。

```bash
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/spark
```

## <a name="read-from-event-hubs-for-kafka"></a>Kafka 用 Event Hubs から読み取る
構成にわずかな変更を加えれば、Kafka 用 Event Hubs からの読み取りを開始することができます。 **BOOTSTRAP_SERVERS** と **EH_SASL** を実際の名前空間の情報で更新すれば、Kafka を使った場合と同じように、Event Hubs を使ったストリーミングを開始できます。 完全なサンプル コードについては、GitHub にある sparkConsumer.scala ファイルを参照してください。 

```scala
//Read from your Event Hub!
val df = spark.readStream
    .format("kafka")
    .option("subscribe", TOPIC)
    .option("kafka.bootstrap.servers", BOOTSTRAP_SERVERS)
    .option("kafka.sasl.mechanism", "PLAIN")
    .option("kafka.security.protocol", "SASL_SSL")
    .option("kafka.sasl.jaas.config", EH_SASL)
    .option("kafka.request.timeout.ms", "60000")
    .option("kafka.session.timeout.ms", "30000")
    .option("kafka.group.id", GROUP_ID)
    .option("failOnDataLoss", "true")
    .load()

//Use dataframe like normal (in this example, write to console)
val df_write = df.writeStream
    .outputMode("append")
    .format("console")
    .start()
```

## <a name="write-to-event-hubs-for-kafka"></a>Kafka 用 Event Hubs に書き込む
Event Hubs に対する書き込みも、Kafka に対する書き込みと同様の方法で行えます。 **BOOTSTRAP_SERVERS** と **EH_SASL** は、実際の Event Hubs 名前空間の情報に変更します。この構成を忘れずに更新してください。  完全なサンプル コードについては、GitHub にある sparkProducer.scala ファイルを参照してください。 

```scala
df = /**Dataframe**/

//Write to your Event Hub!
df.writeStream
    .format("kafka")
    .option("topic", TOPIC)
    .option("kafka.bootstrap.servers", BOOTSTRAP_SERVERS)
    .option("kafka.sasl.mechanism", "PLAIN")
    .option("kafka.security.protocol", "SASL_SSL")
    .option("kafka.sasl.jaas.config", EH_SASL)
    .option("checkpointLocation", "./checkpoint")
    .start()
```



## <a name="next-steps"></a>次のステップ
Event Hubs と Kafka 用 Event Hubs の詳細については、次の記事を参照してください。  

- [イベント ハブ内の Kafka ブローカーをミラーリングする](event-hubs-kafka-mirror-maker-tutorial.md)
- [Apache Flink をイベント ハブに接続する](event-hubs-kafka-flink-tutorial.md)
- [Kafka Connect をイベント ハブと統合する](event-hubs-kafka-connect-tutorial.md)
- [GitHub 上でサンプルを調べる](https://github.com/Azure/azure-event-hubs-for-kafka)
- [Akka Streams をイベント ハブに接続する](event-hubs-kafka-akka-streams-tutorial.md)
- [Azure Event Hubs のための Apache Kafka 開発者ガイド](apache-kafka-developer-guide.md)

