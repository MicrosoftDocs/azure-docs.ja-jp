---
title: 機能の概要 - Azure Event Hubs | Microsoft Docs
description: この記事では、Azure Event Hubs の機能と用語に関する詳細を示します。
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: e0505960a413308283c4e67e33ec495eedd3b092
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827720"
---
# <a name="features-and-terminology-in-azure-event-hubs"></a>Azure Event Hubs の機能と用語

Azure Event Hubs は、大量のイベントやデータを取り込んで処理するスケーラブルなイベント処理サービスで、短い待機時間と高い信頼性を実現します。 概要については、「[Event Hubs とは](event-hubs-what-is-event-hubs.md)」を参照してください。

[概要記事](event-hubs-what-is-event-hubs.md)内の情報に基づいて作成されたこの記事では、Event Hubs のコンポーネントと機能に関する実装の技術的な詳細を説明します。

## <a name="namespace"></a>名前空間
Event Hubs 名前空間は一意のスコープ コンテナーを提供します。このコンテナーは、1 つ以上のイベント ハブまたは Kafka トピックを作成する[完全修飾ドメイン名](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)によって参照されます。 

## <a name="event-hubs-for-apache-kafka"></a>Apache Kafka 用 Event Hubs

[この機能](event-hubs-for-kafka-ecosystem-overview.md)は、顧客が Kafka プロトコルを使用して Event Hubs に接続できるようにするエンドポイントを提供します。 この統合によって顧客に Kafka エンドポイントが提供されます。 これにより、顧客は Event Hubs に接続するように既存の Kafka アプリケーションを構成できるため、独自の Kafka クラスターを実行するための代替手段が提供されます。 Apache Kafka 用の Event Hubs は、Kafka プロトコル 1.0 以降をサポートしています。 

この統合により、Kafka クラスターを実行したり、Zookeeper を使用してそれらを管理したりする必要はありません。 また、これにより、キャプチャ、自動インフレ、geo ディザスター リカバリーなどの Event Hubs の最も要求の厳しい機能のいくつかを操作することもできます。

この統合ではまた、Mirror Maker などのアプリケーションまたは Kafka Connect などのフレームワークが、構成変更だけでクラスターなしで動作できます。 

## <a name="event-publishers"></a>イベント発行元

イベント ハブにデータを送信するエンティティは、イベント プロデューサー ("*イベント発行元*") です。 イベント パブリッシャーは、HTTPS、AMQP 1.0、または Kafka 1.0 以降を使用してイベントを発行できます。 イベント発行元は、Shared Access Signature (SAS) トークンを使用してイベント ハブに対して身元を明らかにし、一意の ID を備えることも共通の SAS トークンを使用することもできます。

### <a name="publishing-an-event"></a>イベントの発行

AMQP 1.0、Kafka 1.0 (以降)、または HTTPS 経由でイベントを発行できます。 Event Hubs は、.NET クライアントからイベント ハブへのイベント発行で使用できる[クライアント ライブラリとクラス](event-hubs-dotnet-framework-api-overview.md)を提供します。 その他のランタイムとプラットフォームには、 [Apache Qpid](https://qpid.apache.org/)などの任意の AMQP 1.0 クライアントを使用できます。 イベントを個別に発行することも、複数のイベントを一括して発行すること (バッチ) もできます。 単一イベントまたはバッチのどちらであるかには関係なく、単一パブリケーション (イベント データ インスタンス) には 1 MB の制限があります。 このしきい値より大きいイベントを発行すると、エラーが発生します。 発行元にとっては、イベント ハブ内のパーティションを意識せずに、次のセクションで説明する "*パーティション キー*" のみを指定するか、または SAS トークンを介して ID のみを指定するのがベスト プラクティスです。

AMQP または HTTPS のどちらを使用するかは、使用シナリオによって決まります。 AMQP では、トランスポート レベルのセキュリティ (TLS) または SSL/TLS に加えて、永続的な双方向ソケットを確立する必要があります。 AMQP ではセッション初期化時のネットワーク コストが高くなりますが、HTTPS では要求ごとに追加の SSL オーバーヘッドが必要になります。 発行の頻度が高い場合は、AMQP の方が高パフォーマンスになります。

![Event Hubs](./media/event-hubs-features/partition_keys.png)

Event Hubs によって、1 つのパーティション キー値を共有するすべてのイベントが、正しい順序で同じパーティションに確実に配信されます。 パーティション キーと発行元ポリシーを併用する場合は、発行元の ID とパーティション キーの値が一致する必要があります。 一致しないと、エラーが発生します。

### <a name="publisher-policy"></a>発行元ポリシー

Event Hubs では、 *発行元ポリシー*を介してイベント プロデューサーをきめ細かく制御できます。 発行元ポリシーは、多数の独立したイベント発行元を支援するために設計されたランタイム機能です。 発行元ポリシーでは、次のメカニズムを使用してイベント ハブにイベントを発行する際に、各発行元は独自の一意の識別子を使用します。

```http
//[my namespace].servicebus.windows.net/[event hub name]/publishers/[my publisher name]
```

前もって発行元名を作成しておく必要はありませんが、独立した発行元 ID を保証するために、発行元名はイベントを発行するときに使用される SAS トークンと一致する必要があります。 発行元ポリシーを使用する場合は、 **PartitionKey** 値を発行元名に設定します。 適切に機能するために、これらの値が一致する必要があります。

## <a name="capture"></a>キャプチャ

[Event Hubs Capture](event-hubs-capture-overview.md) では、Event Hubs のストリーミング データを自動でキャプチャし、任意の BLOB ストレージ アカウントまたは Azure Data Lake Service アカウントのいずれかに保存することができます。 Azure Portal から Capture を有効にし、キャプチャを実行する最小サイズと時間枠を指定できます。 Event Hubs Capture を使用すると、キャプチャされたデータを格納するための独自の Azure Blob Storage アカウントとコンテナーまたは Azure Data Lake Service アカウントを指定することができます。 キャプチャされたデータは、Apache Avro 形式で書き込まれます。

## <a name="partitions"></a>パーティション
[!INCLUDE [event-hubs-partitions](../../includes/event-hubs-partitions.md)]


## <a name="sas-tokens"></a>SAS トークン

Event Hubs は、名前空間とイベント ハブのレベルで利用可能な *Shared Access Signature* を使用します。 SAS トークンは、SAS キーから生成されるものであり、特定の形式でエンコードされた URL の SHA ハッシュです。 Event Hubs は、キー (ポリシー) の名前とトークンを使用することで、ハッシュを再生成し、送信者を認証できます。 通常、イベント発行元の SAS トークンは特定のイベント ハブへの**送信**特権のみを付加して作成されます。 この SAS トークン URL のメカニズムは、発行元ポリシーに導入された発行元識別のための基盤です。 SAS を使用する方法の詳細については、「[Service Bus による Shared Access Signature 認証](../service-bus-messaging/service-bus-sas.md)」を参照してください。

## <a name="event-consumers"></a>イベント コンシューマー

イベント ハブからイベント データを読み取るエンティティは、いずれも "*イベント コンシューマー*" です。 Event Hubs のすべてのコンシューマーは AMQP 1.0 セッションを介して接続します。イベントは使用可能になると、このセッションを使用して配信されます。 クライアントがデータの可用性をポーリングする必要はありません。

### <a name="consumer-groups"></a>コンシューマー グループ

Event Hubs の発行/サブスクライブのメカニズムは、"*コンシューマー グループ*" によって有効になります。 コンシューマー グループは、イベント ハブ全体のビュー (状態、位置、またはオフセット) を表します。 コンシューマー グループを使用することにより、複数のコンシューマー アプリケーションは、イベント ストリームの個別のビューをそれぞれ保有し、独自のペースで独自のオフセットによってストリームを別々に読み取ることができます。

ストリーム処理アーキテクチャにおいて、各ダウンストリーム アプリケーションはコンシューマー グループに相当します。 (パーティションからさらに) 長期的なストレージにイベント データを書き込む場合、そのストレージ ライター アプリケーションはコンシューマー グループとなります。 複雑なイベント処理は、別の異なるコンシューマー グループで実行できます。 パーティションにはコンシューマー グループを介してのみアクセスできます。 イベント ハブには既定のコンシューマー グループが常に存在します。Standard レベルのイベント ハブに対して最大 20 個のコンシューマー グループを作成できます。

コンシューマー グループ内の 1 つのパーティションが同時に接続できるリーダーは最大 5 つですが、**コンシューマー グループごとのパーティションのアクティブな受信者は 1 つだけにすることをお勧めします**。 1 つのパーティション内で、各リーダーがすべてのメッセージを受信します。 同じパーティションに複数のリーダーがある場合、重複したメッセージを処理します。 お使いのコード内でこれを処理する必要があり、相応の作業量が生じる場合があります。 ただし、これは一部のシナリオで有効な手法です。


コンシューマー グループ URI 表記の例を次に示します。

```http
//[my namespace].servicebus.windows.net/[event hub name]/[Consumer Group #1]
//[my namespace].servicebus.windows.net/[event hub name]/[Consumer Group #2]
```

次の図は、Event Hubs ストリーム処理のアーキテクチャを示しています。

![Event Hubs](./media/event-hubs-features/event_hubs_architecture.png)

### <a name="stream-offsets"></a>ストリームのオフセット

"*オフセット*" は、パーティション内のイベントの位置です。 オフセットは、クライアント側のカーソルと考えることができます。 オフセットはイベントのバイト位置です。 このオフセットにより、イベント コンシューマー (リーダー) は、イベント ストリーム内でのイベント読み取りの開始点を指定することができます。 オフセットは、タイムスタンプとして、またはオフセット値として指定することができます。 Event Hubs サービスの外部で独自のオフセット値を格納する場合は、コンシューマーの責任で行います。 パーティション内では、各イベントにオフセットが含まれます。

![Event Hubs](./media/event-hubs-features/partition_offset.png)

### <a name="checkpointing"></a>チェックポイント機能

"*チェックポイント処理*" とは、リーダーがパーティションにおけるイベント シーケンス内の位置をマークまたはコミットするために使用する処理です。 チェックポイント処理はコンシューマーの責任で行います。この処理はコンシューマー グループ内でパーティションごとに発生します。 つまり、コンシューマー グループごとに、各パーティション リーダーは、イベント ストリーム内でのその現在の位置を追跡する必要があり、データ ストリームが完了したと見なしたときにサービスに通知することができます。

リーダーがパーティションから切断し、その後再び接続すると、該当するコンシューマー グループ内の該当するパーティションの最後のリーダーによって最後に送信されたチェックポイントから読み取りが開始されます。 リーダーは接続の際に、このオフセットをイベント ハブに渡して、読み取りを開始する場所を指定します。 このように、チェックポイント処理を使用することで、ダウンストリーム アプリケーションごとにイベントに "完了" のマークを付けると共に、異なるコンピューター上で実行中のリーダー間でフェールオーバーが発生した場合に回復性をもたらすことができます。 このチェックポイント処理で、より小さなオフセットを指定すると、古いデータに戻ることができます。 このメカニズムにより、チェックポイント処理ではフェールオーバーの回復性とイベント ストリームの再生の両方を実現できます。

### <a name="common-consumer-tasks"></a>一般的なコンシューマー タスク

すべての Event Hubs コンシューマーは、AMQP 1.0 セッション (状態に対応する双方向の通信チャネル) を介して接続します。 各パーティションには、パーティションによって分離されたイベントの転送を容易にする AMQP 1.0 セッションがあります。

#### <a name="connect-to-a-partition"></a>パーティションに接続する

パーティションに接続する場合は、特定のパーティションへのリーダーの接続を調整するためにリース メカニズムを使用するのが一般的です。 このため、コンシューマー グループ内のどのパーティションもアクティブなリーダーが 1 つだけである可能性があります。 チェックポイント処理、リース、リーダー管理は、.NET クライアントの [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) クラスを使用して簡略化されます。 イベント プロセッサ ホストはインテリジェントなコンシューマー エージェントです。

#### <a name="read-events"></a>イベントを読み取る

特定のパーティションに対して AMQP 1.0 のセッションおよびリンクが開かれると、Event Hubs サービスによってイベントが AMQP 1.0 クライアントに配信されます。 この配信メカニズムでは、HTTP GET などのプル ベースのメカニズムよりも高いスループットおよび短い遅延時間を実現します。 イベントがクライアントに送信されるとき、イベント データの各インスタンスには、イベント シーケンスでのチェックポイント処理を容易にするために使用されるオフセットやシーケンス番号などの重要なメタデータが含まれます。

イベント データ:
* Offset
* Sequence number
* 本文
* ユーザー プロパティ
* システム プロパティ

ユーザーはオフセットを管理する必要があります。

## <a name="next-steps"></a>次の手順

Event Hubs の詳細については、次のリンクを参照してください。

* [Event Hubs のチュートリアル][Event Hubs tutorial]を開始する
* [Event Hubs のプログラミング ガイド](event-hubs-programming-guide.md)
* [Event Hubs における可用性と一貫性](event-hubs-availability-and-consistency.md)
* [Event Hubs の FAQ](event-hubs-faq.md)
* [Event Hubs サンプル][]

[Event Hubs tutorial]: event-hubs-dotnet-standard-getstarted-send.md
[Event Hubs サンプル]: https://github.com/Azure/azure-event-hubs/tree/master/samples
