---
title: Apache Kafka アプリからイベント ハブを使用する - Azure Event Hubs | Microsoft Docs
description: この記事では、Azure Event Hubs での Apache Kafka のサポートに関する情報を提供します。
services: event-hubs
documentationcenter: .net
author: shvija
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: bahariri
ms.openlocfilehash: 8bf381e7c66e06bbaa140ed865f0f7c9b4f001af
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/22/2019
ms.locfileid: "54452345"
---
# <a name="use-azure-event-hubs-from-apache-kafka-applications"></a>Apache Kafka アプリケーションから Azure Event Hubs を使用する
Event Hubs は、独自の Kafka クラスターを実行する代わりに、既存の Kafka ベースのアプリケーションが使用できる Kafka エンドポイントを提供します。 Event Hubs は、[Apache Kafka 1.0 以降](https://kafka.apache.org/documentation/)をサポートしており、MirrorMaker を含む既存の Kafka アプリケーションに対応します。  

## <a name="what-does-event-hubs-for-kafka-provide"></a>Kafka 用 Event Hubs で提供されるもの

Kafka 機能用 Event Hubs は、Azure Event Hubs 上で、Kafka トピックに対する読み取りと書き込みの両方について、Kafka バージョン 1.0 以降とバイナリ互換性のあるプロトコル ヘッドを提供します。 コードを変更する必要はなく、構成の最小限の変更だけで、アプリケーションから Kafka エンドポイントを使い始めることができます。 Kafka クラスターを指すのではなく、イベント ハブによって公開されている Kafka エンドポイントを指すように、構成の接続文字列を更新します。 その後、Kafka プロトコルを使用するアプリケーションから Event Hubs へのイベントのストリーミングを開始できます。 この統合により、現在プレビュー段階の [Kafka 接続](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect)ようなフレームワークもサポートされます。 

Kafka と Event Hubs は、概念的にはほぼ同じものです。これらはどちらも、ストリーミング データ用に構築されたパーティション分割型のログです。 次の表に、Kafka の概念と Event Hubs の概念のマッピングを示します。

### <a name="kafka-and-event-hub-conceptual-mapping"></a>Kafka と Event Hubs の概念のマッピング

| Kafka での概念 | Event Hubs での概念|
| --- | --- |
| クラスター | 名前空間 |
| トピック | イベント ハブ |
| Partition | Partition|
| コンシューマー グループ | コンシューマー グループ |
| offset | offset|

### <a name="key-differences-between-kafka-and-event-hubs"></a>Kafka と Event Hubs の主な違い

[Apache Kafka](https://kafka.apache.org/) は、選択した任意の場所で実行できるソフトウェアであるのに対して、Event Hubs は、Azure Blob Storage に似たクラウド サービスです。 管理するサーバーやネットワークはなく、構成するブローカーがありません。 ユーザーは名前空間を作成します。それが、トピックが存在する FQDN であり、次に、その名前空間内に Event Hubs またはトピックを作成します。 Event Hubs と名前空間について詳しくは、「[Event Hubs の機能](event-hubs-features.md#namespace)」をご覧ください。 クラウド サービスとしての Event Hubs は、単一の安定した仮想 IP アドレスをエンドポイントとして使用するため、クライアントは、ブローカーやクラスター内のマシンについて知っておく必要はありません。 

Event Hubs のスケールは、購入するスループット単位の数によって制御されます。各スループット単位では、1 秒あたり 1 MB、または受信の 1 秒あたり 1,000 イベントを利用できます。 既定では、Event Hubs は上限に達すると[自動インフレ](event-hubs-auto-inflate.md)機能によってスループット ユニットをスケールアップします。この機能は Kafka 機能用 Event Hubs でも働きます。 

### <a name="security-and-authentication"></a>セキュリティと認証

Azure Event Hubs は、すべての通信に対して SSL または TLS を要求し、認証のために Shared Access Signatures (SAS) を使用します。 この要件は、Event Hubs 内の Kafka エンドポイントにも適用されます。 Kafka との互換性のため、Event Hubs は認証用には SASL PLAIN を、トランスポート セキュリティのためには SASL SSL を使用します。 Event Hubs でのセキュリティに関する詳細については、「[Event Hubs の認証とセキュリティ](event-hubs-authentication-and-security-model-overview.md)」をご覧ください。

## <a name="other-event-hubs-features-available-for-kafka"></a>Kafka で使用できるその他の Event Hubs 機能

Kafka 機能用 Event Hubs では、あるプロトコルで書き込みを、別のプロトコルで読み取りを行えます。そのため、現在の Kafka プロデューサーは Kafka による発行を続けることができます。また、Event Hub で Azure Stream Analytics や Azure Functions などのリーダーを追加できます。 さらに、[Capture](event-hubs-capture-overview.md) や [Geo ディザスター リカバリー](event-hubs-geo-dr.md)などの Event Hubs 機能も、Kafka 機能用 Event Hubs で動作します。

## <a name="features-that-are-not-yet-supported"></a>まだサポートされていない機能 

まだサポートされていない Kafka の機能の一覧を次に示します。

*   べき等プロデューサー
*   トランザクション
*   圧縮
*   サイズ ベースのリテンション期間
*   ログの圧縮
*   既存トピックへのパーティションの追加
*   HTTP Kafka API のサポート
*   Kafka Streams

## <a name="next-steps"></a>次の手順

この記事では、Kafka 用 Event Hubs の概要について説明しました。 詳細については、以下のリンクをご覧ください。

- [Kafka 対応 Event Hubs の作成方法](event-hubs-create-kafka-enabled.md)
- [Kafka アプリケーションから Event Hubs へのストリーム](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Kafka 対応イベント ハブでの Kafka ブローカーのミラーリング](event-hubs-kafka-mirror-maker-tutorial.md)
- [Kafka 対応イベント ハブへの Apache Spark の接続](event-hubs-kafka-spark-tutorial.md)
- [Kafka 対応イベント ハブへの Apache Flink の接続](event-hubs-kafka-flink-tutorial.md)
- [Kafka 対応イベント ハブとの Kafka Connect の統合](event-hubs-kafka-connect-tutorial.md)
- [Kafka 対応イベント ハブへの Akka Streams の接続](event-hubs-kafka-akka-streams-tutorial.md)
- [GitHub 上でサンプルを調べる](https://github.com/Azure/azure-event-hubs-for-kafka)


