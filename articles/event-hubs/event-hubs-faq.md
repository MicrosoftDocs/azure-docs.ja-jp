---
title: よく寄せられる質問 - Azure Event Hubs | Microsoft Docs
description: この記事では、Azure Event Hubs に関するよく寄せられる質問 (FAQ) とその回答の一覧を示します。
ms.topic: article
ms.date: 01/20/2021
ms.openlocfilehash: e6fd4814e771d03827e51f1cd5ee182c9e432cc5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98696110"
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

### <a name="can-i-use-a-single-advanced-message-queuing-protocol-amqp-connection-to-send-and-receive-from-multiple-event-hubs"></a>単一の Advanced Message Queuing Protocol (AMQP) 接続を使用して、複数のイベント ハブから送受信を実行できますか。

はい。すべてのイベント ハブが同じ名前空間にある限り可能です。

### <a name="what-is-the-maximum-retention-period-for-events"></a>イベントの最大保有期間は何日ですか。

Event Hubs Standard レベルは現在、最大 7 日間の保有期間をサポートしています。 イベント ハブは、永続的なデータ ストアとしては考慮されていません。 24 時間を超える保有期間の目的は、同じシステムでイベント ストリームを再生すると便利なシナリオに対応することです。 たとえば、既存データで新しい機械学習モデルのトレーニングや検証を行う場合です。 7 日間を超えるメッセージの保有期間が必要な場合は、イベント ハブで [Event Hubs Capture](event-hubs-capture-overview.md) を有効にすると、イベント ハブのデータが、選択したストレージ アカウントまたは Azure Data Lake サービス アカウントにプルされます。 Capture を有効にすると、購入済みのスループット ユニットに基づく料金が発生します。

ストレージアカウントでキャプチャされたデータの保有期間を構成できます。 Azure Storage の **ライフサイクル管理** 機能には、汎用 v2 アカウントと BLOB ストレージ アカウントのためのルール ベースのポリシーが豊富に用意されています。 このポリシーを使用して、適切なアクセス層にデータを移行します。または、データのライフサイクルの終了時に期限切れにします。 詳細については、「[Azure Blob Storage のライフサイクルの管理](../storage/blobs/storage-lifecycle-management-concepts.md)」を参照してください。 

### <a name="how-do-i-monitor-my-event-hubs"></a>Event Hubs を監視するにはどうしたらよいですか。
Event Hubs は、リソースの状態を示す網羅的なメトリックを [Azure Monitor](../azure-monitor/overview.md) に出力します。 また、Event Hubs サービスの全体的な正常性を名前空間レベルだけでなく、エンティティ レベルでも評価することができます。 どのような監視が提供されるかについては、[Azure Event Hubs](event-hubs-metrics-azure-monitor.md) に関するページを参照してください。

### <a name="where-does-azure-event-hubs-store-data"></a><a name="in-region-data-residency"></a>Azure Event Hubs ではデータはどこに格納されますか。
Azure Event Hubs の Standard および Dedicated レベルでは、選択されたリージョンにメタデータとデータが格納されます。 Azure Event Hubs 名前空間に対して geo ディザスター リカバリーを設定すると、メタデータは、選択されたセカンダリ リージョンにコピーされます。 したがって、このサービスによって、[トラスト センター](https://azuredatacentermap.azurewebsites.net/)に指定されているものも含めて、リージョンのデータ所在地の要件が自動的に満たされます。

[!INCLUDE [event-hubs-connectivity](../../includes/event-hubs-connectivity.md)]

## <a name="apache-kafka-integration"></a>Apache Kafka の統合

### <a name="how-do-i-integrate-my-existing-kafka-application-with-event-hubs"></a>既存の Kafka アプリケーションを Event Hubs と統合するにはどうしたらよいですか。
Event Hubs は、既存の Apache Kafka ベースのアプリケーションが使用できる Kafka エンドポイントを提供します。 構成変更が、PaaS Kafka エクスペリエンスを得るために必要なすべてのことです。 これにより、独自の Kafka クラスターを実行するための代替手段が提供されます。 Event Hubs は Apache Kafka 1.0 以降のクライアント バージョンをサポートし、既存の Kafka アプリケーション、ツール、およびフレームワークと連携して動作します。 詳細については、[Kafka リポジトリ用の Event Hubs](https://github.com/Azure/azure-event-hubs-for-kafka) に関するページを参照してください。

### <a name="what-configuration-changes-need-to-be-done-for-my-existing-application-to-talk-to-event-hubs"></a>既存のアプリケーションが Event Hubs に接続するにはどのような構成変更を行う必要がありますか。
イベント ハブに接続するには、Kafka クライアントの構成を更新する必要があります。 これは、Event Hubs 名前空間を作成し、[接続文字列](event-hubs-get-connection-string.md)を取得することによって実行されます。 Event Hubs の FQDN を指すように bootstrap.servers を変更し、ポートを 9093 に変更します。 以下に示す正しい認証を使用し、sasl.jaas.config を更新して、Kafka クライアントを Event Hubs エンドポイント (取得した接続文字列) に転送します。

```properties
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
request.timeout.ms=60000
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

例:

```properties
bootstrap.servers=dummynamespace.servicebus.windows.net:9093
request.timeout.ms=60000
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://dummynamespace.servicebus.windows.net/;SharedAccessKeyName=DummyAccessKeyName;SharedAccessKey=XXXXXXXXXXXXXXXXXXXXX";
```
注:sasl.jaas.config がフレームワークでサポートされる構成ではない場合、SASL のユーザー名とパスワードを設定に使用される構成を見つけ、代わりにそれらを使用します。 ユーザー名を $ConnectionString に、パスワードを Event Hubs の接続文字列に設定します。

### <a name="what-is-the-messageevent-size-for-event-hubs"></a>Event Hubs のメッセージ/イベント サイズはどれくらいですか。
Event Hubs に許可されている最大メッセージ サイズは 1 MB です。

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
自動インフレ機能を使用すると、スループット ユニット (TU) をスケールアップできます。 つまり、最初は少ない TU を購入し、イングレスが増加したら自動インフレで TU をスケールアップすることが可能です。 これにより、コスト効率に優れたオプションと、管理する TU の数の完全な制御が提供されます。 この機能は **スケールアップのみ** の機能であり、それを更新することによって TU の数のスケールダウンを完全に制御できます。 

少ないスループット ユニット (TU) (2 TU など) で始めることもできます。 トラフィックが 15 TU に増える可能性が予測される場合は、名前空間で自動インフレ機能を有効にし、上限を 15 TU に設定します。 これで、トラフィックの増加に従って TU を自動的に増やすことができます。

### <a name="is-there-a-cost-associated-when-i-turn-on-the-auto-inflate-feature"></a>自動インフレ機能を有効にした場合、関連するコストは発生しますか。
この機能に関連して生じる **コストはありません**。 

### <a name="how-are-throughput-limits-enforced"></a>スループットの制限はどのように適用されるのですか。
名前空間内のすべてのイベント ハブの合計 **イングレス** スループットまたは合計イングレス イベント レートがスループット ユニットの上限の総計を超過した場合は、送信側が調整され、受信クォータを超えたことを示すエラーを受信します。

名前空間内のすべてのイベント ハブの合計 **エグレス** スループットまたは合計イベント エグレス レートがスループット ユニットの上限の総計を超過した場合は、受信側が調整されますが、調整エラーは生成されません。 

送信側でイベントの使用速度が低下しないようにするため、また受信側でイベント ハブにイベントを送信できなくなることを避けるために、イングレス クォータとエグレス クォータは別々に適用されます。

### <a name="is-there-a-limit-on-the-number-of-throughput-units-that-can-be-reservedselected"></a>予約または選択できるスループット ユニットの数に制限はありますか。

Azure portal で Basic または Standard レベルの名前空間を作成する場合、名前空間に対して最大 20 個の TU を選択できます。 これを **正確に** 40 TU に上げるには、[サポート リクエスト](../azure-portal/supportability/how-to-create-azure-support-request.md)を送信します。  

1. **[Event Bus Namespace]\(イベント バス名前空間\)** ページで、左側のメニューの **[新しいサポート リクエスト]** を選択します。 
1. **[新しいサポート リクエスト]** ページで、次の手順を行います。
    1. **[概要]** には、いくつかの単語で問題を説明してください。 
    1. **[問題の種類]** で、**[クォータ]** を選択します。 
    1. **[問題のサブタイプ]** には、 **[スループット ユニットの増減を要求する]** を選択します。 
    
        :::image type="content" source="./media/event-hubs-faq/support-request-throughput-units.png" alt-text="サポート リクエスト ページ":::

40 TU を超える場合、Event Hubs には、Event Hubs Dedicated クラスターと呼ばれるリソース/容量ベースのモデルが用意されています。 Dedicated クラスターは、容量ユニット (CU) で販売されます。 詳細については、「[Event Hubs Dedicated の概要](event-hubs-dedicated-overview.md)」を参照してください。

## <a name="dedicated-clusters"></a>Dedicated クラスター

### <a name="what-are-event-hubs-dedicated-clusters"></a>Event Hubs Dedicated クラスターとは何ですか。
Event Hubs Dedicated クラスターは、最も要求の厳しい要件を持つ顧客にシングルテナント デプロイを提供します。 このオファリングは、スループット ユニットで縛られない容量ベースのクラスターを構築します。 つまり、このクラスターを利用すると、クラスターの CPU とメモリの使用量に応じてデータを取り込み、ストリーミングできます。 詳細については、[Event Hubs Dedicated クラスター](event-hubs-dedicated-overview.md)に関するページを参照してください。

### <a name="how-do-i-create-an-event-hubs-dedicated-cluster"></a>Event Hubs Dedicated クラスターを作成するにはどうしたらよいですか。
詳細な手順と Event Hubs Dedicated クラスターの設定の詳細については、「[クイックスタート: Azure portal を使用して専用の Event Hubs クラスターを作成する](event-hubs-dedicated-cluster-create-portal.md)」を参照してください。 


[!INCLUDE [event-hubs-dedicated-clusters-faq](../../includes/event-hubs-dedicated-clusters-faq.md)]


## <a name="partitions"></a>メジャー グループ

### <a name="how-many-partitions-do-i-need"></a>パーティションはいくつ必要ですか。
パーティションの数は作成時に 1 - 32 の間で指定する必要があります。 パーティション数は[専用レベル](event-hubs-dedicated-overview.md)以外のすべてのレベルで変更できないため、パーティション数を設定する際には、長期的な規模を考慮する必要があります。 パーティションはデータ編成メカニズムであり、コンシューマー アプリケーションで必要とされるダウンストリーム並列処理に関連します。 イベント ハブでのパーティションの数は、予想される同時接続のリーダー数に直接関連します。 パーティションの詳細については、[パーティション](event-hubs-features.md#partitions)に関するページをご覧ください。

作成時点では、選択可能な最大値 (32) に設定しておくとよいでしょう。 複数のパーティションがある場合、イベントは、その順序を維持せずに、複数のパーティションに送信されることに注意してください (ただし、32 個中 1 つのパーティションにのみ送信し、残りの 31 個を冗長パーティションとするように送信側を構成した場合を除く)。 前者の場合、32 個のパーティションすべてにわたってイベントを読み取る必要があります。 後者の場合は、イベント プロセッサ ホストで行う必用のある構成が生じること以外、特別な追加コストは発生しません。

Event Hubs は、コンシューマー グループ 1 つにつきパーティション リーダーを 1 つ許可するように設計されています。 ほとんどのユース ケースでは、既定の設定の 4 つのパーティションで十分です。 イベント処理のスケール設定を予定している場合は、パーティションを追加したほうが良い場合があります。 パーティションには、特定のスループット制限はありませんが、名前空間の総スループットは、スループット ユニットの数によって制限されます。 名前空間内のスループット ユニットの数を増やすときは、独自の最大スループットを実現するために、同時読み取りを許可するための追加のパーティションが必要になる場合があります。

ただし、アプリケーションで特定のパーティションに対してアフィニティが設定されているモデルがある場合は、パーティションの数を増やすことによる利点がない場合があります。 詳細については、[可用性と一貫性](event-hubs-availability-and-consistency.md)に関するページを参照してください。

### <a name="increase-partitions"></a>パーティションを増やす
サポート リクエストを送信することで、パーティション数を 40 (ちょうど) に増やすように依頼できます。 

1. **[Event Bus Namespace]\(イベント バス名前空間\)** ページで、左側のメニューの **[新しいサポート リクエスト]** を選択します。 
1. **[新しいサポート リクエスト]** ページで、次の手順を行います。
    1. **[概要]** には、いくつかの単語で問題を説明してください。 
    1. **[問題の種類]** で、**[クォータ]** を選択します。 
    1. **[問題のサブタイプ]** には、 **[Request for partition change]\(パーティションの変更を要求する\)** を選択します。 
    
        :::image type="content" source="./media/event-hubs-faq/support-request-increase-partitions.png" alt-text="パーティション数を増やす":::

パーティション数は、正確に 40 に増やすことができます。 この場合は、TU の数も 40 に増やす必要があります。 後で TU の制限を 20 以下に下げることにした場合、パーティションの最大数も 32 に減ります。 

パーティションの減少は、既存のイベント ハブには影響しません。これは、パーティションがイベント ハブ レベルで適用されており、ハブの作成後は変更できないためです。 

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

Event Hubs のテクニカル サポートについては、[Microsoft Q&A の Azure Service Bus に関する質問ページ](/answers/topics/azure-service-bus.html)を参照してください。 課金とサブスクリプション管理のサポートは無料で提供されます。

SLA の詳細については、「 [サービス レベル アグリーメント](https://azure.microsoft.com/support/legal/sla/) 」ページを参照してください。

## <a name="azure-stack-hub"></a>Azure Stack Hub

### <a name="how-can-i-target-a-specific-version-of-azure-storage-sdk-when-using-azure-blob-storage-as-a-checkpoint-store"></a>Azure Blob Storage をチェックポイント ストアとして使用するときに、特定のバージョンの Azure Storage SDK を対象にするにはどうすればよいですか?
このコードを Azure Stack Hub で実行すると、特定の Storage API バージョンを対象としている場合を除き、実行時エラーが発生します。 これは、Event Hubs SDK では、Azure で利用できる最新の Azure Storage API が使用されますが、Azure Stack Hub プラットフォームではこれを利用できない可能性があるためです。 Azure Stack Hub でサポートされる Storage Blob SDK のバージョンは、Azure で一般的に利用できるバージョンと異なる場合があります。 チェックポイント ストアとして Azure Blob Storage を使用している場合は、[Azure Stack Hub ビルドでサポートされている Azure Storage API バージョン](/azure-stack/user/azure-stack-acs-differences?#api-version)を確認し、コード内でそのバージョンを対象にします。 

たとえば、Azure Stack Hub バージョン 2005 上で実行している場合、Storage サービスで利用できる最も高いバージョンは 2019-02-02 となります。 既定では、Event Hubs SDK クライアント ライブラリでは、Azure で利用できる最も高いバージョン (SDK のリリース時点では 2019-07-07) が使用されます。 この場合は、このセクションの手順に従うことに加え、Storage サービス API バージョン 2019-02-02 を対象とするコードを追加する必要もあります。 特定の Storage API バージョンを対象にする方法の例については、次の C#、Java、Python、および JavaScript/TypeScript 用のサンプルを参照してください。  

コードから特定の Storage API バージョンを対象にする方法の例については、次の GitHub 上のサンプルを参照してください。 

- [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/)
- [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorWithCustomStorageVersion.java)
- Python - [同期](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob/samples/receive_events_using_checkpoint_store_storage_api_version.py)、[非同期](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/receive_events_using_checkpoint_store_storage_api_version_async.py)
- [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/javascript/receiveEventsWithApiSpecificStorage.js) および [TypeScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/typescript/src/receiveEventsWithApiSpecificStorage.ts)

## <a name="next-steps"></a>次のステップ

Event Hubs の詳細については、次のリンク先を参照してください:

* [Event Hubs の概要](./event-hubs-about.md)
* [Event Hub を作成する](event-hubs-create.md)
* [Event Hubs の自動インフレ](event-hubs-auto-inflate.md)
