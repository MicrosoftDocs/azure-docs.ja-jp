---
title: Event Hubs 用 Apache Kafka 開発者ガイド
description: この記事では、Kafka アプリケーションを Azure Event Hubs と統合する方法を説明している記事のリンクを紹介します。
ms.date: 06/23/2020
ms.topic: article
ms.openlocfilehash: 757db4faf2a933e366bc818d5cf6aab04d0b08ba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "90061735"
---
# <a name="apache-kafka-developer-guide-for-azure-event-hubs"></a>Azure Event Hubs のための Apache Kafka 開発者ガイド
この記事では、Apache Kafka アプリケーションを Azure Event Hubs と統合する方法を説明している記事のリンクを紹介します。 

## <a name="overview"></a>概要
Event Hubs は、独自の Kafka クラスターを実行する代わりに、既存の Kafka ベースのアプリケーションが使用できる Kafka エンドポイントを提供します。 Event Hubs は、既存の Kafka アプリケーションの多くで動作します。 詳細については、[Apache Kafka 用の Event Hubs](event-hubs-for-kafka-ecosystem-overview.md) に関するページを参照してください

## <a name="quickstarts"></a>クイックスタート
GitHub とこのコンテンツ セットには、Kafka 用の Event Hubs をすぐに把握できるクイックスタートがあります。

### <a name="quickstarts-in-github"></a>GitHub のクイックスタート
**azure-event-hubs-for-kafka** リポジトリの次のクイックスタートを参照してください。 

| クライアント言語またはフレームワーク | 説明 | 
| ------------------------- | ----------- | 
| [.NET](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/dotnet) | <p>このクイックスタートでは、.NET Core 2.0 を使用して C# で記述されたプロデューサーとコンシューマーの例を使用して、Event Hubs Kafka エンドポイントを作成して接続する方法について説明します。</p><p>このサンプルは、[Confluent の Apache Kafka .NET クライアント](https://github.com/confluentinc/confluent-kafka-dotnet)に基づいており、Kafka 用の Event Hubs に使用できるように変更されています。</p> | 
| [Java](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/java) | このクイックスタートでは、Java で記述されたプロデューサーとコンシューマーの例を使用して、Event Hubs Kafka エンドポイントを作成して接続する方法について説明します。 |
| [Node.js](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/node) | <p>このクイックスタートでは、Node で記述されたプロデューサーとコンシューマーの例を使用して、Event Hubs Kafka エンドポイントを作成して接続する方法について説明します。</p><p>このサンプルでは、[node-rdkafka](https://github.com/Blizzard/node-rdkafka) ライブラリを使用しています。 </p>| 
| [Python](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/python) | <p>このクイックスタートでは、Python で記述されたプロデューサーとコンシューマーの例を使用して、Event Hubs Kafka エンドポイントを作成して接続する方法について説明します。</p><p>このサンプルは、[Confluent の Apache Kafka Python クライアント](https://github.com/confluentinc/confluent-kafka-python)に基づいており、Kafka 用の Event Hubs に使用できるように変更されています。</p>|
| [Go](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/go) | <p>このクイックスタートでは、Go で記述されたプロデューサーとコンシューマーの例を使用して、Event Hubs Kafka エンドポイントを作成して接続する方法について説明します。</p><p>このサンプルは、[Confluent の Apache Kafka Golang クライアント](https://github.com/confluentinc/confluent-kafka-go)に基づいており、Kafka 用の Event Hubs に使用できるように変更されています。</p>| 
| [Sarama kafka Go](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/go-sarama-client) | このクイックスタートでは、[Sarama Kafka クライアント](https://github.com/Shopify/sarama) ライブラリを使用して Go で記述されたプロデューサーとコンシューマーの例を使用して、Event Hubs Kafka エンドポイントを作成して接続する方法について説明します。 |
| [Kafka](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/kafka-cli) | このクイックスタートでは、Apache Kafka ディストリビューションにバンドルされている CLI を使用して、Event Hubs Kafka エンドポイントを作成して接続する方法について説明します。| 
| [kafkacat](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/kafkacat) | kafkacat は、JVM ではないコマンドライン コンシューマーおよびプロデューサーであり、librdkafka に基づいています。その速度と小さなフットプリントから人気があります。 このクイックスタートには、サンプル構成といくつかの簡単なサンプル kafkacat コマンドが含まれています。 | 
 
### <a name="quickstarts-in-docs"></a>DOCS 内のクイックスタート
このコンテンツ セットの「クイックスタート: [Kafka プロトコルを使用した Event Hubs によるデータ ストリーミング](event-hubs-quickstart-kafka-enabled-event-hubs.md)」を参照してください。Event Hubs にストリーム配信する手順が説明されています。 プロデューサーとコンシューマーを使用して、アプリケーションの構成変更だけで Event Hubs と通信する方法を説明します。 


## <a name="tutorials"></a>チュートリアル 

### <a name="tutorials-in-github"></a>GitHub のチュートリアル
GitHub の次のチュートリアルを参照してください。

| チュートリアル | 説明 | 
| ------------------------- | ----------- | 
| [Akka](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/akka/java) | このチュートリアルでは、プロトコル クライアントを変更したり、独自のクラスターを実行したりせずに、Akka Streams を Kafka 対応 Event Hubs に接続する方法を示します。 **Java** および **Scala** プログラミング言語を使用する 2 つの個別のチュートリアルがあります。 | 
| [のインスタンスに接続するときには、](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect) | このドキュメントでは、Kafka Connect を Azure Event Hubs と統合し、基本的な FileStreamSource および FileStreamSink コネクタをデプロイする方法について説明します。 これらのコネクタは運用環境での使用を想定したものではありませんが、Azure Event Hubs が Kafka ブローカーとして偽装する Kafka Connect のシナリオをエンド ツー エンドで示しています。| 
| [Filebeat](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/filebeat) | このドキュメントでは、Filebeat の Kafka の出力を介して Filebeat と Event Hubs を統合する手順について説明します。 | 
| [Flink](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/flink) | このチュートリアルでは、プロトコル クライアントを変更したり、独自のクラスターを実行したりせずに、Apache Flink を Kafka 対応 Event Hubs に接続する方法を示します。 | 
| [FluentD](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/fluentd) | このドキュメントでは、Fluentd 用の `out_kafka` 出力プラグインを使用して、Fluentd と Event Hubs を統合する方法について説明します。 |
| [相互運用](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/interop) | このチュートリアルでは、さまざまなプロトコルを使用してコンシューマーとプロデューサー間でイベントを交換する方法について説明します。 |
| [Logstash](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/logstash) | このチュートリアルでは、Logstash Kafka の入力および出力プラグインを使用して、Logstash を Kafka 対応の Event Hubs と統合する方法について説明します。 | 
| [MirrorMaker](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker) | このチュートリアルでは、イベント ハブと Kafka MirrorMaker を使用し、Kafka 入力ストリームを Event Hubs サービスにミラーリングすることで Azure に Kafka の既存のパイプラインを統合する方法について説明します。 |
| [NiFi](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/nifi) | このチュートリアルでは、Apache NiFi を Event Hubs 名前空間に接続する方法について説明します。 | 
| [OAuth](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth) | クイックスタートでは、Go および Java プログラミング言語で記述されたサンプルのプロデューサーとコンシューマーを使用して、Event Hubs Kafka エンドポイントを作成して接続する方法について説明します。 |
| [Confluent の Schema Registry](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/schema-registry) | このチュートリアルでは、Schema Registry と Kafka 用の Event Hubs の統合について説明します。 | 
| [Spark](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/spark) | このチュートリアルでは、プロトコル クライアントを変更したり、独自の Kafka クラスターを実行したりせずに、Spark アプリケーションを Event Hubs に接続する方法について説明します。 | 

### <a name="tutorials-in-docs"></a>DOCS 内のチュートリアル
このコンテンツ セットの「チュートリアル: [Stream Analytics を使用して Event Hubs イベントの Apache Kafka を処理する](event-hubs-kafka-stream-analytics.md)」も参照してください。データを Event Hubs にストリーム配信して、Azure Stream Analytics で処理する方法が説明されています。

## <a name="how-to-guides"></a>操作方法ガイド
Microsoft のドキュメントの次のハウツー ガイドを参照してください。

| [アーティクル] | 説明 | 
| ------- | ----------- | 
| [イベント ハブ内の Kafka ブローカーをミラーリングする](event-hubs-kafka-mirror-maker-tutorial.md) | Kafka MirrorMaker を使用してイベント ハブ内の Kafka ブローカーをミラーリングする方法について説明します。 |
| [Apache Spark をイベント ハブに接続する](event-hubs-kafka-spark-tutorial.md) | リアルタイム ストリーミングのために Spark アプリケーションを Event Hubs に接続する手順について説明します。 |
| [Apache Flink をイベント ハブに接続する](event-hubs-kafka-flink-tutorial.md) | プロトコル クライアントを変更したり、独自のクラスターを実行したりせずに、Apache Flink をイベント ハブに接続する方法について説明します。 |
| [Apache Kafka Connect をイベント ハブと統合する (プレビュー)](event-hubs-kafka-connect-tutorial.md) | イベント ハブに Kafka Connect を統合し、基本的な FileStreamSource および FileStreamSink コネクタをデプロイする手順について説明します。 |
| [Akka Streams をイベント ハブに接続する](event-hubs-kafka-akka-streams-tutorial.md) | プロトコル クライアントを変更したり、独自のクラスターを実行したりせずに、Akka Streams をイベント ハブに接続する方法について説明します。 |
| [Azure Event Hubs で Apache Kafka 用 Spring Boot Starter を使用する](/azure/developer/java/spring-framework/configure-spring-cloud-stream-binder-java-app-kafka-azure-event-hub) | Spring Boot Initializer を使用して作成された Java ベースの Spring Cloud Stream Binder を、Apache Kafka と Azure Event Hubs を使用するように構成する方法について説明します。 |

## <a name="next-steps"></a>次のステップ
GitHub リポジトリ [azure-event-hubs-for-kafka](https://github.com/Azure/azure-event-hubs-for-kafka) のクイックスタートおよびチュートリアル フォルダーの下にあるサンプルを確認します。

また、次の記事を参照してください。

- [Event Hubs 用 Apache Kafka トラブルシューティング ガイド](apache-kafka-troubleshooting-guide.md)
- [よく寄せられる質問 - Apache Kafka 用 Event Hubs](apache-kafka-frequently-asked-questions.md)
- [Event Hubs 用 Apache Kafka 移行ガイド](apache-kafka-migration-guide.md)



