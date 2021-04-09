---
title: Azure Cosmos DB 用 Cassandra API についてよく寄せられる質問
description: Azure Cosmos DB 用 Cassandra API についてよく寄せられる質問とその回答を示します。
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 08/12/2020
ms.author: thvankra
ms.openlocfilehash: 1368a3174af08f557b6d08f298fba015601d568c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96030834"
---
# <a name="frequently-asked-questions-about-the-cassandra-api-in-azure-cosmos-db"></a>Azure Cosmos DB での Cassandra API についてよく寄せられる質問
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

この記事では、Azure Cosmos DB での Apache Cassandra と Cassandra API の機能の違いについて説明します。 Azure Cosmos DB での Cassandra API についてよく寄せられる質問とその回答も示します。

## <a name="key-differences-between-apache-cassandra-and-the-cassandra-api"></a>Apache Cassandra と Cassandra API の主な違い

- Apache Cassandra では、パーティション キー サイズの上限に 100 MB が推奨されます。 Azure Cosmos DB 用 Cassandra API では、パーティションごとに最大 20 GB を使用できます。
- Apache Cassandra を使用すると、永続的なコミットを無効にすることができます。 コミット ログへの書き込みをスキップし、memtable に直接移動できます。 これにより、memtable がディスク上の SSTable にフラッシュされる前にノードがダウンすると、データが失われる可能性があります。 Azure Cosmos DB では、データの損失を防ぐために常に永続的なコミットが行われます。
- Apache Cassandra では、多数の置換または削除を伴うワークロードの場合にパフォーマンスが低下する可能性があります。 この理由は、最新のデータを取り込むために読み取りワークロードでスキップする必要がある廃棄標識です。 Cassandra API では、多数の置換または削除があるワークロードの場合に読み取りのパフォーマンスが低下しません。
- 置換数の多いワークロードのシナリオでは、圧縮を実行してディスク上の SSTable をマージする必要があります (Apache Cassandra の書き込みは追加のみであるため、マージが必要です。 複数の更新は個々の SSTable エントリとして保存され、定期的にマージする必要があります)。 このような状況の場合、圧縮中の読み取りのパフォーマンスも低下する可能性があります。 このパフォーマンスへの影響は Cassandra API では発生しません。API に圧縮が実装されていないためです。
- Apache Cassandra では、レプリケーション係数として 1 を設定できます。 しかし、データがある唯一のノードがダウンした場合、可用性が低下します。 レプリケーション係数は常に 4 (クォーラム 3) であるため、これは Azure Cosmos DB 用 Cassandra API に関する問題ではありません。
- Apache Cassandra でノードを追加または削除するには、手動による介入が必要です。さらに、新しいノードの CPU 使用率は高くなり、既存のノードではトークン範囲の一部が新しいノードに移動されます。 この状況は、既存のノードを使用停止する場合も同じです。 一方、Cassandra API の場合は、サービスまたはアプリケーションで問題が発生することなくスケールアウトします。
- Apache Cassandra のように、クラスター内の各ノードに **num_tokens** を設定する必要はありません。 Azure Cosmos DB では、ノードとトークン範囲が完全に管理されます。
- Cassandra API はフル マネージドです。 Apache Cassandra で使用される **nodetool** コマンド (repair や decommission など) は必要ありません。

## <a name="other-frequently-asked-questions"></a>その他のよく寄せられる質問

### <a name="what-protocol-version-does-the-cassandra-api-support"></a>Cassandra API はどのプロトコル バージョンをサポートしていますか?

Azure Cosmos DB 用 Cassandra API では CQL バージョン 3.x がサポートされています。 CQL の互換性は、パブリック [Apache Cassandra GitHub リポジトリ](https://github.com/apache/cassandra/blob/trunk/doc/cql3/CQL.textile)に基づいています。 他のプロトコルのサポートについてフィードバックがある場合は、[UserVoice のフィードバック](https://feedback.azure.com/forums/263030-azure-cosmos-db)を投稿するか、[askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) にメールを送信してください。

### <a name="why-is-choosing-throughput-for-a-table-a-requirement"></a>テーブルのスループットの選択が必須なのはなぜですか?

Azure Cosmos DB では、テーブルの作成元に基づいてコンテナーの既定のスループットが設定されます。つまり、Azure portal または CQL です。

Azure Cosmos DB では、操作に上限を設定してパフォーマンスと待機時間を保証します。 このような保証は、エンジンがテナントの操作にガバナンスを適用できる場合に可能になります。 スループットを設定すると、プラットフォームでこの容量が予約され、操作が正常に完了することが保証されるので、保証されたスループットと待機時間が確保されます。
アプリケーションの季節性を利用し、コストを削減するために、[スループットをエラスティックに変更する](manage-scale-cassandra.md)ことができます。

スループットの概念については、「[Azure Cosmos DB の要求ユニット](request-units.md)」を参照してください。 テーブルのスループットは、基になる物理パーティション全体で均等に分散されます。

### <a name="what-is-the-throughput-of-a-table-thats-created-through-cql"></a>CQL を使用して作成されたテーブルのスループットはどのくらいですか?

Azure Cosmos DB では、スループットの単位として 1 秒あたりの要求単位数 (RU/秒) が使われています。 CQL を使用して作成されたテーブルの既定値は 400 RU です。 RU は Azure portal から変更できます。

CQL

```shell
CREATE TABLE keyspaceName.tablename (user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=1200
```

.NET

```csharp
int provisionedThroughput = 400;
var simpleStatement = new SimpleStatement($"CREATE TABLE {keyspaceName}.{tableName} (user_id int PRIMARY KEY, lastname text)");
var outgoingPayload = new Dictionary<string, byte[]>();
outgoingPayload["cosmosdb_provisioned_throughput"] = Encoding.UTF8.GetBytes(provisionedThroughput.ToString());
simpleStatement.SetOutgoingPayload(outgoingPayload);
```

### <a name="what-happens-when-throughput-is-used-up"></a>スループットを使い切った場合はどうなりますか?

Azure Cosmos DB では、操作に上限を設定してパフォーマンスと待機時間を保証します。 このような保証は、エンジンがテナントの操作にガバナンスを適用できる場合に可能になります。 スループットを設定すると、プラットフォームでこの容量が予約され、操作が正常に完了することが保証されるので、保証されたスループットと待機時間が確保されます。

この容量を超えると、容量が使い果たされたことを示す次のエラー メッセージを受け取ります。

**0x1001 Overloaded: the request can't be processed because "Request Rate is large" (0x1001 オーバーロード: "要求レートが大きい" ため要求を処理できませんでした)** 

この問題の原因となっている操作 (とそのボリューム) を確認することが重要です。 Azure portal でメトリックを使用すると、プロビジョニングした容量を超えた容量の使用について理解できることがあります。 次に、基になるすべてのパーティションでほぼ均等に容量が使用されていることを確保する必要があります。 1 つのパーティションがほとんどのスループットを消費していることがわかった場合は、ワークロードに偏りがあります。

複数のパーティション全体、または集計して、時間単位、日単位、7 日間単位で使用されたスループットを示すメトリックを使用できます。 詳細については、「[Azure Cosmos DB のメトリックを使用した監視とデバッグ](use-metrics.md)」を参照してください。

診断ログについては、「[Azure Cosmos DB 診断ログ](./monitor-cosmos-db.md)」の記事を参照してください。

### <a name="does-the-primary-key-map-to-the-partition-key-concept-of-azure-cosmos-db"></a>プライマリ キーは、Azure Cosmos DB のパーティション キーの概念と対応していますか?

はい。パーティション キーは、適切な場所にエンティティを配置するために使用されます。 Azure Cosmos DB では、物理パーティションに格納されている適切な論理パーティションを見つけるために使用されます。 パーティション分割の概念については、「[Azure Cosmos DB でのパーティション分割とスケーリング](partitioning-overview.md)」の記事でわかりやすく説明されています。 ここで重要な点は、論理パーティションは 20 GB の制限を超えないようにすることです。

### <a name="what-happens-when-i-get-a-notification-that-a-partition-is-full"></a>パーティションがいっぱいになったという通知が表示された場合はどうなりますか?

Azure Cosmos DB はサービス レベル アグリーメント (SLA) に基づくシステムです。 待機時間、スループット、可用性、一貫性が保証され、無制限のスケールが提供されます。 この無制限のストレージは、主要な概念としてパーティション分割を使用し、データの水平スケールアウトに基づいています。 パーティション分割の概念については、「[Azure Cosmos DB でのパーティション分割とスケーリング](partitioning-overview.md)」の記事でわかりやすく説明されています。

論理パーティションあたりのエンティティ数または項目数に関する 20 GB の制限に従うことをお勧めします。 すべての情報を 1 つのパーティションに格納し、そのパーティションに対してクエリを実行すると、ホット パーティションになります。アプリケーションが適切にスケールできるように、ホット パーティションが発生 *しない* ようにすることをお勧めします。 このエラーは、データが均等ではない場合、つまり、1 つのパーティション キーに大量の (20 GB を超える) データが偏っている場合にのみ発生する可能性があります。 ストレージ ポータルを使用して、データの分散を確認できます。 このエラーを解決するには、テーブルを作成し直し、より細分化されたプライマリ (パーティション キー) を選択すると、データの分散が改善されます。

### <a name="can-i-use-the-cassandra-api-as-a-key-value-store-with-millions-or-billions-of-partition-keys"></a>数百万個または数十億個のパーティション キーを擁するキー値ストアとして Cassandra API を使用できますか?

Azure Cosmos DB はストレージをスケールアウトすることで無制限のデータを格納できます。 このストレージはスループットに依存しません。 そのため、Cassandra API を使用すると、常にキーと値を格納し、適切な主キーまたはパーティション キーを指定して取得することができます。 これらの個々のキーには独自の論理パーティションが割り当てられ、物理パーティションに問題なく配置されます。

### <a name="can-i-create-more-than-one-table-with-the-cassandra-api"></a>Cassandra API を使用して複数のテーブルを作成できますか?

はい、Cassandra API を使用して複数のテーブルを作成できます。 これらの各テーブルは、スループットとストレージのユニットとして扱われます。

### <a name="can-i-create-more-than-one-table-in-succession"></a>複数のテーブルを連続して作成することはできますか?

Azure Cosmos DB は、データ アクティビティとコントロール プレーン アクティビティ両方のリソース管理システムです。 コレクションやテーブルなどのコンテナーは、特定のスループット容量に対してプロビジョニングされるランタイム エンティティです。 これらのコンテナーの連続的な作成は予期されるアクティビティではないため、調整される可能性があります。 テーブルを即時に削除または作成するテストを行う場合は、間隔を空けるようにしてください。

### <a name="what-is-the-maximum-number-of-tables-that-i-can-create"></a>作成できるテーブルの最大数はいくつですか?

テーブル数に物理的な制限はありません。 通常の数十個や数百個ではなく、多数のテーブル (合計のサイズが常に 10 TB を超えるデータ) を作成する必要がある場合は、[askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) にメールを送信してください。

### <a name="what-is-the-maximum-number-of-keyspaces-that-i-can-create"></a>作成できるキースペースの最大数はいくつですか?

キースペースはメタデータ コンテナーであるため、その数に物理的な制限はありません。 キースペースが多数ある場合は、[askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) にメールをお送りください。

### <a name="can-i-bring-in-a-lot-of-data-after-starting-from-a-normal-table"></a>通常のテーブルから開始した後に、大量のデータを取り込むことはできますか?

はい。 パーティションが均等に分散されていると仮定した場合、ストレージ容量は自動的に管理され、取り込むデータが増えると拡大されます。 そのため、必要に応じて任意の量のデータを安心してインポートできます。ノードの管理やプロビジョニングなどは必要ありません。 ただし、大量のデータの急速な増加が予想される場合は、少量のものから始めて、すぐに増やすのではなく、[予想されるスループットに対してプロビジョニング](set-throughput.md)を直接行うことをお勧めします。

### <a name="can-i-use-yaml-file-settings-to-configure-api-behavior"></a>YAML ファイル設定を使用して API の動作を構成できますか?

Azure Cosmos DB 用 Cassandra API を使用すると、操作の実行に関してプロトコルレベルの互換性を実現できます。 管理、監視、および構成の複雑な部分は隠されています。 開発者やユーザーは、可用性、廃棄標識、キー キャッシュ、行キャッシュ、ブルーム フィルター、他のさまざまな設定について心配する必要はありません。 Cassandra API は、構成と管理のオーバーヘッドを発生させることなく、必要な読み取りと書き込みのパフォーマンスを実現することに重点を置いています。

### <a name="will-the-cassandra-api-support-node-addition-cluster-status-and-node-status-commands"></a>Cassandra API はノードの追加、クラスター状態、ノード状態の各コマンドをサポートしていますか?

Cassandra API を使用すると、容量計画を簡素化し、スループットとストレージに対する弾力性要求に対応できます。 Azure Cosmos DB を使って必要なスループットをプロビジョニングします。 その後は、ノードの追加、削除、管理を心配せずに、1 日に何度でもスケールアップおよびスケールダウンできます。 ノードとクラスターの管理にツールを使用する必要はありません。

### <a name="what-happens-with-various-configuration-settings-for-keyspace-creation-like-simplenetwork"></a>simple/network のようなキースペース作成にさまざまな構成設定を使用するとどうなりますか?

Azure Cosmos DB では、可用性と低待機時間のために、最初からグローバルな分散が提供されています。 レプリカやなどを設定する必要はありません。 書き込みは、書き込み対象のすべてのリージョンで常に永続的にクォーラム コミットされ、パフォーマンスが保証されます。

### <a name="what-happens-with-various-settings-for-table-metadata"></a>テーブル メタデータのさまざまな設定を使用するとどうなりますか?

Azure Cosmos DB では、読み取り、書き込み、スループットのパフォーマンスが保証されています。 そのため、構成設定に触れて誤って操作することを心配する必要はありません。 このような設定には、ブルーム フィルター、キャッシュ、読み取り修復の可能性、gc_grace、圧縮の memtable_flush_period などがあります。

### <a name="is-time-to-live-supported-for-cassandra-tables"></a>Cassandra のテーブルでは Time-To-Live がサポートされていますか?

はい。TTL がサポートされています。

### <a name="how-can-i-monitor-infrastructure-along-with-throughput"></a>インフラストラクチャとスループットを監視するにはどうすればよいですか?

Azure Cosmos DB はプラットフォーム サービスであり、生産性を向上させ、インフラストラクチャの管理と監視の心配を取り除きます。 たとえば、以前のようにさまざまなツールを使用してノードの状態、レプリカの状態、gc、OS パラメーターを監視する必要はありません。 ポータルのメトリックで使用できるスループットを監視し、調整されているかどうかを確認し、そのスループットを増減するだけです。 次のようにすることができます。

- [SLA](./monitor-cosmos-db.md) を監視する
- [メトリック](use-metrics.md)を使用する
- [診断ログ](./monitor-cosmos-db.md)を使用する

### <a name="which-client-sdks-can-work-with-the-cassandra-api"></a>Cassandra API ではどのクライアント SDK を使用できますか?

CQLv3 を使用する Apache Cassandra SDK のクライアント ドライバーがクライアント プログラムに使用されていました。 他のドライバーを使用している場合、または問題が発生している場合は、[askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) まで電子メールでお問い合わせください。

### <a name="are-composite-partition-keys-supported"></a>複合パーティション キーはサポートされていますか?

はい。通常の構文を使って、複合パーティション キーを作成できます。

### <a name="can-i-use-sstableloader-for-data-loading"></a>データの読み込みに sstableloader を使用できますか?

いいえ。sstableloader はサポートされていません。

### <a name="can-i-pair-an-on-premises-apache-cassandra-cluster-with-the-cassandra-api"></a>オンプレミスの Apache Cassandra クラスターと Cassandra API を組み合わせることはできますか?

現在、Azure Cosmos DB では、操作のオーバーヘッドがないクラウド環境に合わせてエクスペリエンスが最適化されています。 ペアリングが必要な場合は、シナリオの説明を添えて [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) まで電子メールでお問い合わせください。 Microsoft では、オンプレミスまたはクラウドの Cassandra クラスターと Azure Cosmos DB 用 Cassandra API を組み合わせることができるオファリングに取り組んでいます。

### <a name="does-the-cassandra-api-provide-full-backups"></a>Cassandra API に完全バックアップ機能はありますか?

Azure Cosmos DB では、すべての API について 4 時間間隔で 2 つの無料の完全バックアップが提供されています。 そのため、バックアップ スケジュールを設定する必要はありません。 

リテンション期間と頻度を変更したい場合は、[askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) までメールでお問い合わせいただくか、サポート ケースを作成してください。 バックアップ機能については、「[Azure Cosmos DB での自動オンライン バックアップと復元](online-backup-and-restore.md)」の記事を参照してください。

### <a name="how-does-the-cassandra-api-account-handle-failover-if-a-region-goes-down"></a>リージョンがダウンした場合、Cassandra API アカウントはフェールオーバーをどのように処理しますか?

Cassandra API には、Azure Cosmos DB のグローバルに分散されたプラットフォームが利用されています。 アプリケーションでデータ センターのダウンタイムが確実に許容されるようにするには、Azure portal でアカウントのリージョンを少なくとももう 1 つ有効にします。 詳細については、「[Azure Cosmos DB での高可用性](high-availability.md)」を参照してください。

アカウントのリージョンを必要な数だけ追加し、フェールオーバーの優先順位を指定してフェールオーバー先を制御できます。 データベースを使用するには、そのリージョンでもアプリケーションを提供する必要があります。 そうすれば、ダウンタイムが発生しなくなります。

### <a name="does-the-cassandra-api-index-all-attributes-of-an-entity-by-default"></a>Cassandra API では、既定でエンティティのすべての属性のインデックスが作成されますか?

いいえ。 Cassandra API では[セカンダリ インデックス](cassandra-secondary-index.md)がサポートされます。これは Apache Cassandra と同様に動作します。 API の既定では、すべての属性のインデックスが作成されるわけではありません。  


### <a name="can-i-use-the-new-cassandra-api-sdk-locally-with-the-emulator"></a>エミュレーターで新しい Cassandra API SDK をローカルに使うことができますか?

はい、これはサポートされています。 これを有効にする方法の詳細については、「[ローカルでの開発とテストに Azure Cosmos DB Emulator を使用する](local-emulator.md#cassandra-api)」という記事を参照してください。


### <a name="how-can-i-migrate-data-from-apache-cassandra-clusters-to-azure-cosmos-db"></a>Apache Cassandra クラスターから Azure Cosmos DB にデータを移行するにはどうすればよいですか?

移行オプションの詳細については、「[Azure Cosmos DB の Cassandra API アカウントにデータを移行する](cassandra-import-data.md)」のチュートリアルを参照してください。


### <a name="where-can-i-give-feedback-on-cassandra-api-features"></a>Cassandra API の機能に関するフィードバックはどこに送ればよいですか?

フィードバックは [UserVoice のフィードバック](https://feedback.azure.com/forums/263030-azure-cosmos-db)でお寄せください。

[azure-portal]: https://portal.azure.com
[query]: ./sql-query-getting-started.md

## <a name="next-steps"></a>次のステップ

- [Azure Cosmos DB Cassandra API アカウントのエラスティック スケーリング](manage-scale-cassandra.md)を開始します。
