---
title: よく寄せられる質問 - Azure Event Hubs | Microsoft Docs
description: この記事では、Azure Event Hubs に関するよく寄せられる質問 (FAQ) とその回答の一覧を示します。
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 05/15/2019
ms.author: shvija
ms.openlocfilehash: e1ec6987f1a142e9bf9cd4413cfb4444bde1b7dd
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67796994"
---
# <a name="event-hubs-frequently-asked-questions"></a>Event Hubs のよく寄せられる質問

## <a name="general"></a>全般

### <a name="what-is-an-event-hubs-namespace"></a>Event Hubs 名前空間とはどういうものですか。
名前空間は、Event Hub/Kafka トピックのためのスコーピング コンテナーです。 これにより、一意の [FQDN](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) が提供されます。 名前空間は、複数の Event Hub/Kafka トピックを収容できるアプリケーション コンテナーとして機能します。 

### <a name="when-do-i-create-a-new-namespace-vs-use-an-existing-namespace"></a>どのような場合に新しい名前空間を作成し、どのような場合に既存の名前空間を使用すればよいですか。
容量の割り当て ([スループット ユニット (TU)](#throughput-units)) は、名前空間レベルで課金されます。 名前空間は、リージョンにも関連付けられています。

次のシナリオのいずれかに該当する場合は、既存の名前空間を使用するのではなく、新しい名前空間を作成することをお勧めします。 

- イベント ハブを新しいリージョンに関連付ける必要がある。
- イベント ハブを別のサブスクリプションに関連付ける必要がある。
- 異なる容量が割り当てられたイベント ハブが必要である (つまり、追加されたイベント ハブを含む名前空間の容量ニーズが 40 TU のしきい値を超え、専用のクラスターも使用したくない)  

### <a name="what-is-the-difference-between-event-hubs-basic-and-standard-tiers"></a>Event Hubs の Basic レベルと Standard レベルは何が違いますか。

Azure Event Hubs の Standard レベルは、Basic レベルでは使用できない機能を提供します。 Standard レベルには次の機能が含まれています。

* 長いイベント保有期間
* 追加の仲介型接続。提供される数を超える場合は超過料金が適用されます
* 複数の[コンシューマー グループ](event-hubs-features.md#consumer-groups)
* [キャプチャ](event-hubs-capture-overview.md)
* [Kafka 統合](event-hubs-for-kafka-ecosystem-overview.md)

専用の Event Hubs を含む価格レベルの詳細については、「[Event Hubs の価格](https://azure.microsoft.com/pricing/details/event-hubs/)」を参照してください。

### <a name="where-is-azure-event-hubs-available"></a>Azure Event Hubs はどこで利用できますか。

Azure Event Hubs は、サポートされているすべての Azure リージョンで利用できます。 一覧については、「[Azure リージョン](https://azure.microsoft.com/regions/)」ページを参照してください。  

### <a name="can-i-use-a-single-amqp-connection-to-send-and-receive-from-multiple-event-hubs"></a>単一の AMQP 接続を使用して、複数の イベント ハブから送受信を実行できますか。

はい。すべてのイベント ハブが同じ名前空間にある限り可能です。

### <a name="what-is-the-maximum-retention-period-for-events"></a>イベントの最大保有期間は何日ですか。

Event Hubs Standard レベルは現在、最大 7 日間の保有期間をサポートしています。 イベント ハブは、永続的なデータ ストアとしては考慮されていません。 24 時間を超える保有期間の目的は、同じシステムでイベント ストリームを再生すると便利なシナリオ (たとえば既存データで新しい機械学習モデルのトレーニングや検証を行うこと) に対応することです。 7 日間を超えるメッセージの保有期間が必要な場合は、イベント ハブで [Event Hubs Capture](event-hubs-capture-overview.md) を有効にすると、イベント ハブのデータが、選択したストレージ アカウントまたは Azure Data Lake サービス アカウントにプルされます。 Capture を有効にすると、購入済みのスループット ユニットに基づく料金が発生します。

### <a name="how-do-i-monitor-my-event-hubs"></a>Event Hubs を監視するにはどうしたらよいですか。
Event Hubs は、リソースの状態を示す網羅的なメトリックを [Azure Monitor](../azure-monitor/overview.md) に出力します。 また、Event Hubs サービスの全体的な正常性を名前空間レベルだけでなく、エンティティ レベルでも評価することができます。 どのような監視が提供されるかについては、[Azure Event Hubs](event-hubs-metrics-azure-monitor.md) に関するページを参照してください。

### <a name="what-ports-do-i-need-to-open-on-the-firewall"></a>ファイアウォールで開く必要があるのはどのポートですか。 
Azure Service Bus でメッセージを送受信する場合、次のプロトコルを使用できます。

- Advanced Message Queuing Protocol (AMQP)
- HTTP
- Apache Kafka

これらのプロトコルを使用して Azure Event Hubs と通信するために開く必要がある送信ポートについては、次の表を参照してください。 

| Protocol | Port | 詳細 | 
| -------- | ----- | ------- | 
| AMQP | 5671 と 5672 | [AMQP プロトコル ガイド](../service-bus-messaging/service-bus-amqp-protocol-guide.md)に関するページを参照してください | 
| HTTP、HTTPS | 80、443 |  |
| Kafka | 9093 | [Kafka アプリケーションからの Event Hubs の使用](event-hubs-for-kafka-ecosystem-overview.md)に関するページをご覧ください

### <a name="what-ip-addresses-do-i-need-to-whitelist"></a>どのような IP アドレスをホワイトリストに登録する必要がありますか。
接続のためにホワイトリストに登録する必要がある適切な IP アドレスを検索するには、次の手順に従います。

1. コマンド プロンプトで、次のコマンドを実行します。 

    ```
    nslookup <YourNamespaceName>.servicebus.windows.net
    ```
2. `Non-authoritative answer` で返された IP アドレスをメモします。 これが変更されるのは、名前空間を別のクラスターに復元した場合のみです。

名前空間にゾーン冗長性を使用している場合は、次の追加手順を実行する必要があります。 

1. まず、名前空間に対して nslookup を実行します。

    ```
    nslookup <yournamespace>.servicebus.windows.net
    ```
2. **non-authoritative answer** セクションの名前をメモします。これは、次のいずれかの形式になります。 

    ```
    <name>-s1.servicebus.windows.net
    <name>-s2.servicebus.windows.net
    <name>-s3.servicebus.windows.net
    ```
3. s1、s2、s3 のサフィックスが付いているそれぞれについて nslookup を実行し、3 つの可用性ゾーンで実行されている 3 つのインスタンスすべての IP アドレスを取得します。 

## <a name="apache-kafka-integration"></a>Apache Kafka の統合

### <a name="how-do-i-integrate-my-existing-kafka-application-with-event-hubs"></a>既存の Kafka アプリケーションを Event Hubs と統合するにはどうしたらよいですか。
Event Hubs は、既存の Apache Kafka ベースのアプリケーションが使用できる Kafka エンドポイントを提供します。 構成変更が、PaaS Kafka エクスペリエンスを得るために必要なすべてのことです。 これにより、独自の Kafka クラスターを実行するための代替手段が提供されます。 Event Hubs は Apache Kafka 1.0 以降のクライアント バージョンをサポートし、既存の Kafka アプリケーション、ツール、およびフレームワークと連携して動作します。 詳細については、[Kafka リポジトリ用の Event Hubs](https://github.com/Azure/azure-event-hubs-for-kafka) に関するページを参照してください。

### <a name="what-configuration-changes-need-to-be-done-for-my-existing-application-to-talk-to-event-hubs"></a>既存のアプリケーションが Event Hubs に接続するにはどのような構成変更を行う必要がありますか。
Kafka 対応 Event Hub に接続するには、Kafka クライアントの構成を更新する必要があります。 これは、Event Hubs 名前空間を作成し、[接続文字列](event-hubs-get-connection-string.md)を取得することによって実行されます。 Event Hubs の FQDN を指すように bootstrap.servers を変更し、ポートを 9093 に変更します。 次に示すように、正しい認証を使用して、Kafka クライアントを Kafka 対応 Event Hubs のエンドポイント (取得した接続文字列) に転送するように sasl.jaas.config を更新します。

bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093 request.timeout.ms=60000 security.protocol=SASL_SSL sasl.mechanism=PLAIN sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

例:

bootstrap.servers=dummynamespace.servicebus.windows.net:9093 request.timeout.ms=60000 security.protocol=SASL_SSL sasl.mechanism=PLAIN sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://dummynamespace.servicebus.windows.net/;SharedAccessKeyName=DummyAccessKeyName;SharedAccessKey=5dOntTRytoC24opYThisAsit3is2B+OGY1US/fuL3ly=";

注:sasl.jaas.config がフレームワークでサポートされる構成でない場合は、SASL のユーザー名とパスワードを設定するために使用される構成を見つけ、代わりにそれらを使用します。 ユーザー名を $ConnectionString に、パスワードを Event Hubs の接続文字列に設定します。

### <a name="what-is-the-messageevent-size-for-kafka-enabled-event-hubs"></a>Kafka 対応 Event Hubs のメッセージ/イベント サイズはどれくらいですか。
Kafka 対応 Event Hubs に許可されている最大メッセージ サイズは 1MB です。

## <a name="throughput-units"></a>スループット ユニット

### <a name="what-are-event-hubs-throughput-units"></a>Event Hubs のスループット ユニットとは何ですか。
Event Hubs でのスループットは、Event Hubs 経由で入出力されるデータ量 (MB 単位) または 1 KB のイベントの個数 (千個単位) を定義します。 このスループットは、スループット ユニット (TU) で測定されます。 Event Hubs サービスの使用を開始するには、その前に TU を購入します。 Event Hubs の TU は、ポータルまたは Event Hubs Resource Manager テンプレートのどちらかを使用して明示的に選択できます。 


### <a name="do-throughput-units-apply-to-all-event-hubs-in-a-namespace"></a>スループット ユニットは名前空間内のすべてのイベント ハブに適用されますか。
はい。スループット ユニット (TU) は、Event Hubs 名前空間内のすべてのイベント ハブに適用されます。 つまり、TU は名前空間レベルで購入され、その名前空間の下のイベント ハブ間で共有されます。 各 TU により、名前空間に次の機能が与えられます。

- 最大 1 MB/秒のイングレス イベント (イベント ハブに送信されるイベント)。ただし、1 秒あたり 1000 個未満のイングレス イベント、管理操作、または制御 API 呼び出し。
- 最大 2 MB/秒のエグレス イベント (イベント ハブから使用されるイベント)。ただし、4096 個未満のエグレス イベント。
- 最大 84 GB のイベント ストレージ (既定の 24 時間の保有期間に十分な量)。

### <a name="how-are-throughput-units-billed"></a>スループット ユニットはどのように課金されますか。
スループット ユニット (TU) は、時間単位で課金されます。 課金は、特定の 1 時間内に選択されたユニットの最大数に基づきます。 

### <a name="how-can-i-optimize-the-usage-on-my-throughput-units"></a>スループット ユニットでの使用はどのように最適化できますか。
最初は 1 スループット ユニット (TU) から始め、[自動インフレ](event-hubs-auto-inflate.md)を有効にすることができます。 自動インフレ機能を使用すると、トラフィック/ペイロードの増加に従って TU を増やすことができます。 また、TU の数に上限を設定することもできます。

### <a name="how-does-auto-inflate-feature-of-event-hubs-work"></a>Event Hubs の自動インフレ機能はどのように働くのですか。
自動インフレ機能を使用すると、スループット ユニット (TU) をスケールアップできます。 つまり、最初は少ない TU を購入し、イングレスが増加したら自動インフレで TU をスケールアップすることが可能です。 これにより、コスト効率に優れたオプションと、管理する TU の数の完全な制御が提供されます。 この機能は**スケールアップのみ**の機能であり、それを更新することによって TU の数のスケールダウンを完全に制御できます。 

少ないスループット ユニット (TU) (2 TU など) で始めることもできます。 トラフィックが 15 TU に増える可能性が予測される場合は、名前空間で自動インフレ機能を有効にし、上限を 15 TU に設定します。 これで、トラフィックの増加に従って TU を自動的に増やすことができます。

### <a name="is-there-a-cost-associated-when-i-turn-on-the-auto-inflate-feature"></a>自動インフレ機能を有効にした場合、関連するコストは発生しますか。
この機能に関連した**コストはありません**。 

### <a name="how-are-throughput-limits-enforced"></a>スループットの制限はどのように適用されるのですか。
名前空間内のすべてのイベント ハブの合計イングレス スループットまたは合計イングレス イベント レートがスループット ユニットの上限の総計を超過した場合は、送信側が調整され、受信クォータを超えたことを示すエラーを受信します。

名前空間内のすべてのイベント ハブの合計エグレス スループットまたは合計イベント エグレス レートがスループット ユニットの上限の総計を超過した場合は、受信側が調整され、送信クォータが超過したことを示すエラーを受信します。 送信側でイベントの使用速度が低下しないようにするため、また受信側でイベント ハブにイベントを送信できなくなることを避けるために、イングレス クォータとエグレス クォータは別々に適用されます。

### <a name="is-there-a-limit-on-the-number-of-throughput-units-tus-that-can-be-reservedselected"></a>予約または選択できるスループット ユニット (TU) の数に制限はありますか。
マルチテナント オファリングでは、スループット ユニットは 40 TU まで増やすことができます (ポータルで最大 20 TU を選択し、同じ名前空間でそれを 40 TU に増やすためのサポート チケットを生成できます)。 40 TU を超える場合、Event Hubs には、**Event Hubs Dedicated クラスター**と呼ばれるリソース/容量ベースのモデルが用意されています。 Dedicated クラスターは、容量ユニット (CU) で販売されます。

## <a name="dedicated-clusters"></a>Dedicated クラスター

### <a name="what-are-event-hubs-dedicated-clusters"></a>Event Hubs Dedicated クラスターとは何ですか。
Event Hubs Dedicated クラスターは、最も要求の厳しい要件を持つ顧客にシングルテナント デプロイを提供します。 このオファリングは、スループット ユニットで縛られない容量ベースのクラスターを構築します。 つまり、このクラスターを利用すると、クラスターの CPU とメモリの使用量に従ってデータを取り込み、ストリーミングできます。 詳細については、[Event Hubs Dedicated クラスター](event-hubs-dedicated-overview.md)に関するページを参照してください。

### <a name="how-much-does-a-single-capacity-unit-let-me-achieve"></a>1 容量ユニットではどれだけの容量を実現できますか。
専用クラスターの場合、どれだけの容量を取り込んでストリーミングできるかは、プロデューサー、コンシューマー、取り込みや処理の速度などのさまざまな要因によって異なります。 

次の表は、当社でのテスト中に実現したベンチマーク結果を示しています。

| ペイロードの形態 | 受信者 | イングレス帯域幅| イングレス メッセージ | エグレス帯域幅 | エグレス メッセージ | 合計 TU 数 | CU あたりの TU 数 |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| 100x1KB のバッチ | 2 | 400 MB/秒 | 400k メッセージ/秒 | 800 MB/秒 | 800k メッセージ/秒 | 400 TU | 100 TU | 
| 10x10KB のバッチ | 2 | 666 MB/秒 | 66.6k メッセージ/秒 | 1.33 GB/秒 | 133k メッセージ/秒 | 666 TU | 166 TU |
| 6x32KB のバッチ | 1 | 1.05 GB/秒 | 34k メッセージ/秒 | 1.05 GB/秒 | 34k メッセージ/秒 | 1000 TU | 250 TU |

このテストでは、次の条件が使用されました。

- 4 容量ユニット (CU) を持つ専用の Event Hubs クラスターが使用されました。 
- 取り込みに使用されたイベント ハブには 200 パーティションが存在しました。 
- 取り込まれたデータは、すべてのパーティションから受信している 2 つの受信側アプリケーションによって受信されました。

これらの結果は、専用の Event Hubs クラスターでどれだけの容量を実現できるかを示しています。 さらに、マイクロバッチおよび長期保有シナリオで有効になる Event Hubs Capture には専用クラスターが付属しています。

### <a name="how-do-i-create-an-event-hubs-dedicated-cluster"></a>Event Hubs Dedicated クラスターを作成するにはどうしたらよいですか。
Event Hubs Dedicated クラスターは、[クォータ引き上げサポート要求](https://portal.azure.com/#create/Microsoft.Support)を送信するか、または [Event Hubs チーム](mailto:askeventhubs@microsoft.com)に連絡することによって作成します。 通常、クラスターをデプロイし、ユーザーが使用できるようにして渡すには約 2 週間かかります。 このプロセスは、Azure Portal または Azure Resource Manager テンプレートを通して完全なセルフサービスが使用可能になるまでの一時的なものです。これでクラスターをデプロイするには、約 2 時間かかります。

## <a name="best-practices"></a>ベスト プラクティス

### <a name="how-many-partitions-do-i-need"></a>パーティションはいくつ必要ですか。

イベント ハブのパーティション数は、セットアップ後には変更できません。 作業を開始する前に、その点を考慮して、必要なパーティションの数を検討する必要があります。 

Event Hubs は、コンシューマー グループ 1 つにつきパーティション リーダーを 1 つ許可するように設計されています。 ほとんどのユース ケースでは、既定の設定の 4 つのパーティションで十分です。 イベント処理のスケール設定を予定している場合は、パーティションを追加したほうが良い場合があります。 パーティションには特定のスループット制限はありませんが、名前空間の総スループットは、スループット ユニットの数によって制限されます。 名前空間内のスループット ユニットの数を増やすときは、独自の最大スループットを実現するために、同時読み取りを許可するための追加のパーティションが必要になる場合があります。

ただし、アプリケーションで特定のパーティションに対してアフィニティが設定されているモデルがある場合は、パーティションの数を増やすことによる利点がない場合があります。 詳細については、[可用性と一貫性](event-hubs-availability-and-consistency.md)に関するページを参照してください。

## <a name="pricing"></a>価格

### <a name="where-can-i-find-more-pricing-information"></a>価格についての詳細情報はどこにありますか。

Event Hubs 料金の詳細については、「 [Event Hubs 料金](https://azure.microsoft.com/pricing/details/event-hubs/)」を参照してください。

### <a name="is-there-a-charge-for-retaining-event-hubs-events-for-more-than-24-hours"></a>Event Hubs のイベントを 24 時間以上保有する場合に料金はかかりますか。

Event Hubs Standard レベルでは、24 時間より長いメッセージ保有期間 (最大 7 日間) が許可されます。 保存されたイベントの合計数が選択したスループット ユニット数のストレージの上限 (スループット ユニットあたり 84 GB) 上限を超えるサイズには公開された Azure Blob Storage レートの料金が発生します。 各スループット ユニットのストレージの上限は、スループット ユニットが受信の上限まで使用された場合でも、24 時間の保持期間に対するすべてのストレージ コストをカバーします。

### <a name="how-is-the-event-hubs-storage-size-calculated-and-charged"></a>Event Hubs のストレージ サイズはどのように計算され、課金されますか。

保存されたすべてのイベントの合計サイズは、すべてのイベント ハブのイベント ヘッダーまたはディスク ストレージ構造の内部オーバーヘッドを含めて、1 日中測定されます。 1 日の終わりに、ピーク ストレージ サイズが計算されます。 1 日あたりのストレージの上限は、その日に選択されたスループット ユニットの最小数に基づいて計算されます (それぞれのスループット ユニットには 84 GB の上限が与えらえます)。 合計サイズが計算された 1 日あたりのストレージの上限を超過した場合は、超過したストレージが、Azure Blob ストレージ レート ( **Locally Redundant Storage (LRS)** ) を使用して課金されます。

### <a name="how-are-event-hubs-ingress-events-calculated"></a>Event Hubs のイングレス イベントはどのように計算されますか。

イベント ハブに送信されたイベントは、それぞれが課金対象メッセージとしてカウントされます。 1 つの *イングレス イベント* は64 KB 以下の単位のデータと定義されます。 サイズが 64 KB 以下のイベントは、1 つの課金対象イベントとみなされます。 イベントが 64 KB よりも大きい場合、課金対象イベントの数はイベント サイズに従って計算され、64 KB の倍数になります。 たとえば、イベント ハブに送信された 8 KB のイベントは 1 つのイベントとして課金されますが、イベント ハブに送信された 96 KB のメッセージは 2 つのイベントとして課金されます。

イベント ハブや管理操作、チェックポイントなどの制御呼び出しで使用されるイベントは、課金対象イングレス イベントとしてはカウントされませんが、スループット ユニットの上限まで蓄積されます。

### <a name="do-brokered-connection-charges-apply-to-event-hubs"></a>仲介型接続料金は Event Hubs に適用されますか。

接続料金は AMQP プロトコルが使用されている場合にのみ適用されます。 送信側システムまたはデバイスの数に関係なく、HTTP を使用したベントの送信には接続料金は発生しません。 AMQP を使用する場合 (たとえば、イベント ストリーミングの効率を高めたり、IoT のコマンドと制御のシナリオで双方向通信を可能にする場合) は、[Event Hubs の価格情報](https://azure.microsoft.com/pricing/details/event-hubs/)に関するページで各サービスレベルに含まれる接続数に関する詳細を参照してください。

### <a name="how-is-event-hubs-capture-billed"></a>Event Hubs Capture はどのように課金されますか。

Capture が有効になるのは、名前空間内のいずれかのイベント ハブで Capture オプションが有効になっている場合です。 Event Hubs Capture は購入済みのスループット ユニットごとに時間単位で課金されます。 スループット ユニット数が増減すると、Event Hubs Capture の課金についても、全体の時間の増分にスループット ユニット数の変化が反映されます。 Event Hubs Capture の課金の詳細については、[Event Hubs の価格情報](https://azure.microsoft.com/pricing/details/event-hubs/)に関するページを参照してください。

### <a name="do-i-get-billed-for-the-storage-account-i-select-for-event-hubs-capture"></a>Event Hubs Capture 用に選択したストレージ アカウントに対しては課金されますか。

Event Hub で有効にされた場合、Capture はお客様の指定したストレージ アカウントを使用します。 これはお客様のストレージ アカウントであるため、この構成に関する変更はすべてお客様の Azure サブスクリプションに課金されます。

## <a name="quotas"></a>Quotas (クォータ)

### <a name="are-there-any-quotas-associated-with-event-hubs"></a>Event Hubs に関連付けられているクォータはありますか。

Event Hubs のすべてのクォータの一覧については、[クォータ](event-hubs-quotas.md)に関するページを参照してください。

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="why-am-i-not-able-to-create-a-namespace-after-deleting-it-from-another-subscription"></a>別のサブスクリプションから名前空間を削除した後に、その名前空間を作成できないのはなぜですか。 
サブスクリプションから名前空間を削除した場合、別のサブスクリプションで同じ名前を使用して再作成するには、4 時間ほど時間を空けてから行ってください。 そうしないと、`Namespace already exists` というエラー メッセージが表示される場合があります。 

### <a name="what-are-some-of-the-exceptions-generated-by-event-hubs-and-their-suggested-actions"></a>Event Hubs によって生成される例外とその推奨されるアクションをいくつか教えてください。

発生する可能性がある Event Hubs 例外の一覧については、[例外の概要](event-hubs-messaging-exceptions.md)に関するページを参照してください。

### <a name="diagnostic-logs"></a>診断ログ

Event Hubs では、Capture エラー ログと運用ログの 2 種類の[診断ログ](event-hubs-diagnostic-logs.md)がサポートされており、両方とも JSON で表されます。この診断ログは Azure Portal で有効にできます。

### <a name="support-and-sla"></a>サポートと SLA

Event Hubs のテクニカル サポートは、 [コミュニティ フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=servbus)を通して利用できます。 課金とサブスクリプション管理のサポートは無料で提供されます。

SLA の詳細については、「 [サービス レベル アグリーメント](https://azure.microsoft.com/support/legal/sla/) 」ページを参照してください。

## <a name="next-steps"></a>次の手順

Event Hubs の詳細については、次のリンク先を参照してください:

* [Event Hubs の概要](event-hubs-what-is-event-hubs.md)
* [Event Hub を作成する](event-hubs-create.md)
* [Event Hubs の自動インフレ](event-hubs-auto-inflate.md)
