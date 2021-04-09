---
title: Advisor を使用して Azure アプリのパフォーマンスを向上させる
description: Azure Advisor のパフォーマンスに関する推奨事項を使用すると、ビジネスに不可欠なアプリケーションのスピードと応答性を向上させることができます。
ms.topic: article
ms.date: 07/29/2020
ms.openlocfilehash: 69fd86226375073c71903af5704ef3a635eeaffb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100579913"
---
# <a name="improve-the-performance-of-azure-applications-by-using-azure-advisor"></a>Azure Advisor を使用して Azure アプリケーションのパフォーマンスを向上させる

Azure Advisor のパフォーマンスに関する推奨事項は、ビジネスに不可欠なアプリケーションのスピードと応答性を向上させるために役立ちます。 Advisor のパフォーマンスに関する推奨事項は、Advisor ダッシュボードの **[パフォーマンス]** タブで取得できます。

## <a name="reduce-dns-time-to-live-on-your-traffic-manager-profile-to-fail-over-to-healthy-endpoints-faster"></a>Traffic Manager プロファイルの DNS Time to Live を短縮して迅速に正常なエンドポイントにフェールオーバーする

Azure Traffic Manager プロファイルで[有効期限 (TTL) 設定](../traffic-manager/traffic-manager-performance-considerations.md)を使用すると、特定のエンドポイントがクエリに応答しなくなった場合にエンドポイントを切り替える時間を指定できます。 TTL 値を減らすと、クライアントは機能しているエンドポイントに短時間でルーティングされます。

Azure Advisor によって、長い TTL が構成されている Traffic Manager プロファイルが特定されます。 プロファイルが [Fast Failover](https://azure.microsoft.com/roadmap/fast-failover-and-tcp-probing-in-azure-traffic-manager/) 用に構成されているかどうかに応じて、TTL を 20 秒または 60 秒のいずれかに構成することをお勧めします。

## <a name="improve-database-performance-by-using-sql-database-advisor-temporarily-disabled"></a>SQL Database Advisor を使用してデータベースのパフォーマンスを向上させる (一時的に無効)

Azure Advisor では、すべての Azure リソースに関して一貫性のある推奨事項が一元的にまとめて示されます。 SQL Database Advisor と統合して、データベースのパフォーマンスを向上させるための推奨事項を生成します。  SQL Database Advisor は、データベースの使用履歴を分析することで、パフォーマンスを評価します。 その後、データベースの一般的なワークロードを実行する上で最適な推奨事項が提示されます。

> [!NOTE]
> 推奨事項を取得する前に、データベースを約 1 週間使用する必要があり、その 1 週間にある程度の一貫性のあるアクティビティが存在する必要があります。 SQL Database Advisor は、ランダムでむらのあるアクティビティよりも、一貫性のあるアクティビティのクエリ パターンをより簡単に最適化できます。

詳細については、[SQL Database Advisor](../azure-sql/database/database-advisor-implement-performance-recommendations.md) に関するページを参照してください。

## <a name="upgrade-your-storage-client-library-to-the-latest-version-for-better-reliability-and-performance"></a>信頼性とパフォーマンスを向上させるために、ストレージ クライアント ライブラリを最新バージョンにアップグレードする

Storage クライアント ライブラリ SDK の最新バージョンには、お客様から報告された問題に対する修正と、QA プロセスを通じて事前に明らかになった問題の修正が含まれています。 また、最新バージョンには、Azure Storage の使用に関する全体的なエクスペリエンスを向上させる新機能に加えて、信頼性とパフォーマンスの最適化も含まれています。 古いバージョンを使用している場合、Advisor からは SDK の最新バージョンにアップグレードするために必要な推奨事項と手順が提供されます。 推奨事項は、サポートされている言語であるC++ と .NET 向けです。

## <a name="improve-app-service-performance-and-reliability"></a>App Service のパフォーマンスと信頼性の向上

Azure Advisor では、App Service のエクスペリエンスを向上させ、関連するプラットフォームの機能を検出するための推奨事項が統合されています。 App Service に関する推奨事項の例を次に示します。
* アプリの実行時にメモリや CPU のリソースが使い尽くされるインスタンスの検出と軽減オプションの提供。
* Web アプリやデータベースなどのリソースの配置によってパフォーマンスの向上とコストの削減を実現できるインスタンスの検出。

詳細については、「[Azure App Service のベスト プラクティス](../app-service/app-service-best-practices.md)」を参照してください。

## <a name="use-managed-disks-to-prevent-disk-io-throttling"></a>マネージド ディスクを使用してディスク I/O スロットリングを防ぐ

Advisor によって、スケーラビリティ ターゲットに達しているストレージ アカウントに属する仮想マシンが特定されます。 この条件により、これらの VM は I/O スロットリングを受けやすくなります。 Advisor によって、パフォーマンスの低下を防ぐためにマネージド ディスクを使用することが推奨されます。

## <a name="improve-the-performance-and-reliability-of-virtual-machine-disks-by-using-premium-storage"></a>Premium Storage を使用して仮想マシンのディスクのパフォーマンスと信頼性を向上させる

Advisor によって、ストレージ アカウントに大量のトランザクションがある Standard ディスクを使用する仮想マシンが特定されます。 Premium ディスクにアップグレードすることが推奨されます。 

Azure Premium Storage は、高負荷の I/O ワークロードを実行する仮想マシン向けに高パフォーマンスで待ち時間の少ないディスク サポートを提供します。 Premium Storage アカウントを使用する仮想マシンのディスクでは、ソリッド ステート ドライブ (SSD) にデータを格納します。 アプリケーションで最適なパフォーマンスを実現するには、高い IOPS を必要とする仮想マシン ディスクは Premium Storage に移行することをお勧めします。

## <a name="remove-data-skew-on-your-azure-synapse-analytics-tables-to-increase-query-performance"></a>Azure Synapse Analytics テーブルのデータ スキューを除去してクエリのパフォーマンスを向上させる

データ スキューは、ワークロードの実行時に不要なデータ移動やリソースのボトルネックを引き起こす可能性があります。 Advisor によって、15% を超える分散データ スキューが検出されます。 データを再配分して、テーブルの分散キーの選択を見直すことをお勧めします。 スキューの識別と除去の詳細については、[スキューのトラブルシューティング](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-distribute.md#how-to-tell-if-your-distribution-column-is-a-good-choice)に関する記事を参照してください。

## <a name="create-or-update-outdated-table-statistics-in-your-azure-synapse-analytics-tables-to-increase-query-performance"></a>Azure Synapse Analytics テーブルの古いテーブル統計を作成するか更新してクエリのパフォーマンスを向上させる

Advisor によって最新の[テーブル統計](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-statistics.md)状態がないテーブルが特定され、統計の作成または更新が推奨されます。 Azure Synapse Analytics のクエリ オプティマイザーでは、最新の統計が使用され、クエリ結果のカーディナリティまたは行数が推定されます。 このような推定値を使用すると、クエリ オプティマイザーでクエリ プランを作成し、最速のパフォーマンスを実現することができます。

## <a name="improve-mysql-connection-management"></a>MySQL 接続管理を向上させる

Advisor の分析には、MySQL サーバーに接続するアプリケーションで接続が効率的に管理されていない可能性が示されることがあります。 このような状態は、不必要なリソース消費とアプリケーション待機時間の全体的な増加につながる可能性があります。 接続管理を向上させるには、有効期間の短い接続の数を減らし、不要なアイドル状態の接続を排除することをお勧めします。 こうした機能強化は、ProxySQL のようなサーバー側の接続プーラーを構成することで実現できます。


## <a name="scale-up-to-optimize-cache-utilization-on-your-azure-synapse-analytics-tables-to-increase-query-performance"></a>スケールアップして Azure Synapse Analytics テーブルでのキャッシュ使用率を最適化し、クエリのパフォーマンスを向上させる

Azure Advisor を使用すると、Azure Synapse Analytics テーブルのキャッシュ使用率が高く、ヒット率が低いかどうかを検出できます。 この状態はキャッシュ削除が多いことを示し、Azure Synapse Analytics インスタンスのパフォーマンスに影響を及ぼす可能性があります。 Advisor によって、ワークロードに十分なキャッシュ容量を割り当てられるように、Azure Synapse Analytics インスタンスをスケールアップすることが推奨されます。

## <a name="convert-azure-synapse-analytics-tables-to-replicated-tables-to-increase-query-performance"></a>Azure Synapse Analytics のテーブルをレプリケートされたテーブルに変換し、クエリのパフォーマンスを向上させる

Advisor によって、レプリケートされておらず、変換によってメリットが得られるテーブルが特定されます。 このようなテーブルを変換することをお勧めします。 推奨事項は以下に基づいています。
- レプリケートされるテーブルのサイズ。 
- 列数です。 
- テーブルの分散の種類。 
- Azure Synapse Analytics テーブルのパーティション数。 

コンテキストに対する推奨では、追加のヒューリスティックが提供される場合があります。 この推奨事項が決定される方法の詳細については、[Azure Synapse Analytics の推奨事項](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-concept-recommendations.md#replicate-tables)に関する記事を参照してください。 

## <a name="migrate-your-storage-account-to-azure-resource-manager-to-get-the-latest-azure-features"></a>ストレージ アカウントを Azure Resource Manager に移行して最新の Azure 機能を手に入れる

ストレージ アカウントのデプロイ モデルを Azure Resource Manager に移行すると、次の利点があります。
- テンプレートのデプロイ。
- 追加のセキュリティ オプション。
- GPv2 アカウントにアップグレードして最新の Azure Storage 機能を使用できるようになる機能。 

Advisor を使用すると、クラシック デプロイ モデルを使用しているスタンドアロンのストレージ アカウントがすべて特定して、Resource Manager デプロイ モデルに移行することを推奨します。

> [!NOTE]
> Azure Monitor のクラシック アラートは 2019 年 8 月に廃止されました。 新しいプラットフォームでアラート機能を保持するには、Resource Manager を使用するようにクラシック ストレージ アカウントをアップグレードすることをお勧めします。 詳細については、[クラシック アラートの廃止](../azure-monitor/alerts/monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform)に関するページを参照してください。

## <a name="design-your-storage-accounts-to-prevent-reaching-the-maximum-subscription-limit"></a>サブスクリプションの上限に到達しないようにストレージ アカウントを設計する

Azure リージョンでは、サブスクリプションごとに最大 250 個のストレージ アカウントがサポートされています。 この制限に達すると、そのリージョンとサブスクリプションの組み合わせでストレージ アカウントを作成することができなくなります。 Advisor では、サブスクリプションが確認されて、サブスクリプションとリージョンが上限に近づいている場合はストレージ アカウント数を減らして設計するように勧告されます。

## <a name="consider-increasing-the-size-of-your-vpn-gateway-sku-to-address-high-p2s-use"></a>高 P2S 使用率に対処するために VPN Gateway の SKU のサイズを大きくすることを検討する

各 Azure VPN Gateway SKU では、指定された数の同時 P2S 接続のみをサポートできます。 接続数がゲートウェイの上限に迫っていると、追加の接続試行が失敗する可能性があります。 ゲートウェイのサイズを大きくすると、より多くの同時 P2S ユーザーをサポートできるようになります。 Advisor からは、ゲートウェイのサイズを増やすための推奨事項と手順が提供されます。

## <a name="consider-increasing-the-size-of-your-vpn-gateway-sku-to-address-high-cpu"></a>高 CPU 使用率に対処するために VPN Gateway SKU のサイズを大きくすることを検討する

トラフィック負荷が高い場合、CPU が高いために VPN ゲートウェイによってパケットがドロップされる可能性があります。 VPN Gateway SKU のアップグレードを検討してください。 VPN ゲートウェイのサイズを大きくすると、高 CPU 使用率が原因で接続が切断されなくなります。 Advisor からは、この問題に事前に対処するための推奨事項が提供されます。 

## <a name="increase-batch-size-when-loading-to-maximize-load-throughput-data-compression-and-query-performance"></a>読み込み時のバッチ サイズを増加して、読み込みスループット、データ圧縮、クエリ パフォーマンスを最大化する

Advisor では、データベースに読み込む際にバッチ サイズを増加することで、読み込みのパフォーマンスとスループットを向上できるかどうかが検出されます。 COPY ステートメントの使用を検討する必要があります。 COPY ステートメントを使用できない場合は、SQLBulkCopy API や BCP などの読み込みユーティリティを使用するときにバッチ サイズを増やすことを検討してください。 一般的な規則として、10 万行から 100 万行のバッチ サイズを使用することをお勧めします。 バッチ サイズを大きくすると、読み込みのスループット、データ圧縮、クエリ パフォーマンスが向上します。

## <a name="co-locate-the-storage-account-in-the-same-region-to-minimize-latency-when-loading"></a>読み込み時の待機時間を最小限に抑えるために、ストレージ アカウントを同じリージョン内に配置する

Advisor によって、専用 SQL プールとは異なるリージョンから読み込んでいるかどうかが検出されます。 データを読み込むときの待機時間を最小限に抑えるには、専用 SQL プールと同じリージョン内にあるストレージ アカウントから読み込むことを検討してください。 この変更により、待機時間を最小限に抑え、読み込みパフォーマンスを向上させることができます。

## <a name="use-a-supported-kubernetes-version"></a>サポートされている Kubernetes バージョンを使用する

Advisor によって、サポートされていないバージョンの Kubernetes が検出されます。

## <a name="optimize-the-performance-of-your-azure-database-for-mysql-azure-database-for-postgresql-and-azure-database-for-mariadb-servers"></a>Azure Database for MySQL、Azure Database for PostgreSQL、および Azure Database for MariaDB サーバーのパフォーマンスを最適化する

### <a name="fix-the-cpu-pressure-of-your-azure-database-for-mysql-azure-database-for-postgresql-and-azure-database-for-mariadb-servers-with-cpu-bottlenecks"></a>CPU のボトルネックがある Azure Database for MySQL、Azure Database for PostgreSQL、Azure Database for MariaDB サーバーの CPU 負荷を修正する
長期間にわたって CPU の使用率が高いと、ワークロードのクエリ パフォーマンスが低速に陥る可能性があります。 CPU サイズを増やすと、データベース クエリのランタイムを最適化し、全体のパフォーマンスを向上させることができます。 Advisor によって、CPU 使用率が高く CPU 制約があるワークロードを実行している可能性が高いサーバーが特定され、コンピューティングのスケーリングが推奨されます。

### <a name="reduce-memory-constraints-on-your-azure-database-for-mysql-azure-database-for-postgresql-and-azure-database-for-mariadb-servers-or-move-to-a-memory-optimized-sku"></a>Azure Database for MySQL、Azure Database for PostgreSQL、および Azure Database for MariaDB サーバーのメモリの制約を減らすか、メモリ最適化 SKU に移行します。
キャッシュ ヒット率が低いと、クエリ パフォーマンスが低下し IOPS が高くなる可能性があります。 この状態は、不適切なクエリ プランまたはメモリを集中的に使用するワークロードによって発生する可能性があります。 クエリ プランを修正するか、Azure Database for PostgreSQL、Azure Database for MySQL、または Azure Database for MariaDB サーバーの[メモリを増やす](../postgresql/concepts-pricing-tiers.md)と、データベース ワークロードの実行を最適化する上で役立ちます。 Azure Advisor によって、この高いバッファー プール チャーンの影響を受けるサーバーが特定されます。 次のいずれかの操作を実行することをお勧めします。 
- クエリ プランを修正する
- より多くのメモリを持つ SKU に移行する 
- ストレージ サイズを増やして IOPS を増やす。

### <a name="use-an-azure-database-for-mysql-or-azure-database-for-postgresql-read-replica-to-scale-out-reads-for-read-intensive-workloads"></a>Azure Database for MySQL または Azure Database for PostgreSQL の読み取りレプリカを使用して、読み取りの負荷が高いワークロードの読み取りをスケールアウトする
Advisor を使用すると、サーバー上での過去 7 日間にわたる書き込みに対する読み取りの比率など、ワークロードベースのヒューリステックを活用して、読み取りの負荷が高いワークロードを特定できます。 読み取りまたは書き込みの比率が高い Azure Database for PostgreSQL または Azure Database for MySQL のリソースがあると、CPU またはメモリの競合が発生し、クエリのパフォーマンスが低下する可能性があります。 [レプリカ](../postgresql/howto-read-replicas-portal.md)を追加すると、レプリカ サーバーへの読み取りをスケールアウトし、プライマリ サーバーでの CPU またはメモリの制約を回避できます。 Advisor によってワークロードの読み取りの負荷が高いサーバーが特定され、[読み取りレプリカ](../postgresql/concepts-read-replicas.md)を追加して読み取りワークロードの一部をオフロードすることが推奨されます。


### <a name="scale-your-azure-database-for-mysql-azure-database-for-postgresql-or-azure-database-for-mariadb-server-to-a-higher-sku-to-prevent-connection-constraints"></a>Azure Database for MySQL、Azure Database for PostgreSQL、または Azure Database for MariaDB サーバーをより高い SKU に拡張して接続の制約を防ぐ
データベース サーバーへの新しい接続ごとにメモリが占有されます。 メモリの[上限](../postgresql/concepts-limits.md)が原因でサーバーへの接続がエラーになっている場合は、データベース サーバーのパフォーマンスが低下します。 Azure Advisor によって、実行中のサーバーのうち、多数の接続エラーが発生しているものが特定されます。 次のいずれかのアクションを実行して、サーバーの接続制限をアップグレードし、サーバーにより多くのメモリを割り当てることが推奨されます。
- コンピューティングをスケールアップする。 
- メモリ最適化 SKU を使用する (これにより、コアあたりのコンピューティングが増えます)。

## <a name="scale-your-cache-to-a-different-size-or-sku-to-improve-cache-and-application-performance"></a>キャッシュやアプリケーションのパフォーマンスを向上させるためにキャッシュを別のサイズまたは SKU にスケーリングする

キャッシュ インスタンスは、高いメモリ負荷、高いサーバー負荷、または高いネットワーク帯域幅という条件下で実行されていない場合に最高のパフォーマンスを発揮します。 このような条件があると、応答しなくなる、データが失われる、または使用不能になる可能性があります。 Advisor によって、これらの条件に該当するキャッシュ インスタンスが特定されます。 次のいずれかの操作を実行することをお勧めします。
- ベスト プラクティスを適用して、メモリの負荷、サーバーの負荷、またはネットワーク帯域幅を減らす。
- 容量が多い別のサイズまたは SKU にスケーリングする。

## <a name="add-regions-with-traffic-to-your-azure-cosmos-db-account"></a>トラフィックが存在するリージョンを Azure Cosmos DB アカウントに追加する

Advisor によって、現在構成されていないリージョンからのトラフィックがある Azure Cosmos DB アカウントが検出されます。 そのリージョンを追加することをお勧めします。 そうすることで、そのリージョンからの要求の待機時間が短縮され、リージョンが停止した場合の可用性が確保されます。 [Azure Cosmos DB でのグローバルなデータの分散の詳細を確認してください。](../cosmos-db/distribute-data-globally.md)

## <a name="configure-your-azure-cosmos-db-indexing-policy-by-using-custom-included-or-excluded-paths"></a>カスタムの含まれるパスまたは除外されるパスを使用して Azure Cosmos DB インデックス作成ポリシーを構成する

Advisor によって、既定のインデックス作成ポリシーが使用され、カスタム インデックス作成ポリシーの恩恵を受ける可能性がある Azure Cosmos DB コンテナーが特定されます。 この決定は、ワークロード パターンに基づいて行われます。 既定のインデックス作成ポリシーでは、すべてのプロパティのインデックスが作成されます。 クエリ フィルターで明示的に包含または除外されたパスを持つカスタム インデックス作成ポリシーを使用すると、インデックス作成に使用される RU とストレージを減らすことができます。 [インデックス ポリシーの変更の詳細を確認してください。](../cosmos-db/index-policy.md)

## <a name="set-your-azure-cosmos-db-query-page-size-maxitemcount-to--1"></a>Azure Cosmos DB クエリ ページ サイズ (MaxItemCount) を -1 に設定する 

Azure Advisor によって、100 のクエリ ページ サイズを使用している Azure Cosmos DB コンテナーが特定されます。 高速なスキャンのために、-1 のページ サイズの使用が推奨されます。 [MaxItemCount の詳細を確認してください。](../cosmos-db/sql-api-query-metrics.md)

## <a name="consider-using-accelerated-writes-feature-in-your-hbase-cluster-to-improve-cluster-performance"></a>お使いの HBase クラスターで、クラスターのパフォーマンスの向上に高速書き込み機能を使用することを検討する
Azure Advisor は過去 7 日間のシステム ログを分析し、お使いのクラスターで次のシナリオが発生したかどうかを識別します。
1. WAL の同期時に長い待機時間が発生 
2. 大量の書き込み要求 (1 時間に 1000 を超える avg_write_requests/second/node)

これらの条件は、クラスターで書き込み時に長い待機時間が発生していることを示しています。 これは、お使いのクラスターで大量のワークロードが実行されたことが原因である可能性があります。お使いのクラスターのパフォーマンスを向上させるには、Azure HDInsight HBase の高速書き込み機能の利用を検討することをお勧めします。 HDInsight の Apache HBase クラスター用高速書き込み機能では、クラウド ストレージを使用する代わりに、Premium SSD マネージド ディスクをすべての RegionServer (ワーカー ノード) にアタッチします。 その結果、書き込み待機時間が短縮され、アプリケーションの回復性が向上します。 この機能の詳細については、[こちら](../hdinsight/hbase/apache-hbase-accelerated-writes.md#how-to-enable-accelerated-writes-for-hbase-in-hdinsight)を参照してください。

## <a name="review-azure-data-explorer-table-cache-period-policy-for-better-performance-preview"></a>パフォーマンスの向上に、Azure Data Explorer のテーブルのキャッシュ期間 (ポリシー) を確認する (プレビュー)
この推奨事項では、構成されているキャッシュ期間 (ポリシー) より過去のクエリが多い Azure Data Explorer のテーブルが表示されます (キャッシュ範囲外のデータにアクセスするクエリの割合で、上位 10 個のテーブルが示されます)。 クラスターのパフォーマンスを向上させるために推奨されるアクション:このテーブルのクエリを、(定義されたポリシー内で) 必要最小限の時間範囲に制限します。 または、時間範囲全体のデータが必要な場合は、キャッシュ期間を推奨される値に増やします。

## <a name="improve-performance-by-optimizing-mysql-temporary-table-sizing"></a>MySQL の一時テーブルのサイズを最適化してパフォーマンスを向上させる
Advisor で、一時テーブルのパラメーター設定が低いため、お使いの MySQL サーバーで不要な I/O オーバーヘッドが発生している可能性があることが解析されました。 これにより、ディスク ベースの不要なトランザクションが発生し、パフォーマンスが低下する可能性があります。 "tmp_table_size" パラメーターと "max_heap_table_size" パラメーターの値を大きくして、ディスク ベースのトランザクションの数を減らすことをお勧めします。 [詳細情報](https://aka.ms/azure_mysql_tmp_table)

## <a name="distribute-data-in-server-group-to-distribute-workload-among-nodes"></a>ノード間にワークロードを分散させるためにサーバー グループ内でデータを分散させる
コーディネーター上には残っているが、データは分散されないサーバー グループが Advisor により識別されました。 これに基づき、Hyperscale (Citus) のメリットをすべて得るために、お使いのサーバー グループ内のワーカー ノードにデータを分散させることが Advisor によって推奨されています。 これによって、サーバー グループ内の各ノードのリソースが活用され、クエリのパフォーマンスが向上します。 [詳細情報](https://go.microsoft.com/fwlink/?linkid=2135201) 

## <a name="improve-user-experience-and-connectivity-by-deploying-vms-closer-to-windows-virtual-desktop-deployment-location"></a>Windows Virtual Desktop のデプロイ場所の近くに VM をデプロイすることにより、ユーザー エクスペリエンスと接続性を向上させます
Windows Virtual Desktop (WVD) を使用して、お客様の VM はユーザーの接続元とは異なる、または遠い場所にあると判断しました。 これにより、接続応答時間が長くなる可能性があり、WVD の全体的なユーザー エクスペリエンスに影響します。 ホスト プールの VM を作成するときは、ユーザーに近いリージョンを使用するようにします。 近接していることで、WVD サービスに対する満足度を維持し、全体的なエクスペリエンスの品質を向上させることができます。 接続の待機時間の詳細については、[こちら](../virtual-desktop/connection-latency.md)をご覧ください。

## <a name="upgrade-to-the-latest-version-of-the-immersive-reader-sdk"></a>イマーシブ リーダー SDK の最新バージョンにアップグレードする
このサブスクリプションのリソースに、古いバージョンのイマーシブ リーダー SDK が使用されていることがわかりました。 イマーシブ リーダー SDK の最新バージョンを使用すると、更新されたセキュリティ、パフォーマンス、および統合エクスペリエンスをカスタマイズおよび強化するための一連の拡張機能が提供されます。
イマーシブ リーダー SDK の詳細については、[こちら](../cognitive-services/immersive-reader/index.yml)をご覧ください。

## <a name="improve-vm-performance-by-changing-the-maximum-session-limit"></a>セッションの上限を変更して VM のパフォーマンスを向上させる

Advisor では、深さ優先が負荷分散アルゴリズムとして設定されているホスト プールがあること、およびそのホスト プールのセッション上限が 999999 以上であることが検出されます。 深さ優先の負荷分散では、最大セッション数を使用して、1 つのセッション ホストで同時セッションを持つことができるユーザーの最大数が決定されます。 最大セッション数が高すぎる場合、すべてのユーザー セッションは同じセッション ホストに送られるので、パフォーマンスと信頼性の問題が発生します。 そのため、ホスト プールを深さ優先で負荷分散するように設定する場合は、VM のデプロイと容量の構成に応じて、適切な最大セッション数を設定する必要があります。 

Windows Virtual Desktop での負荷分散の詳細については、「[Windows 仮想デスクトップの負荷分散方法の構成](../virtual-desktop/troubleshoot-set-up-overview.md)」を参照してください。

## <a name="how-to-access-performance-recommendations-in-advisor"></a>Advisor のパフォーマンスに関する推奨事項にアクセスする方法

1. [Azure Portal](https://portal.azure.com) にサインインし、[Advisor](https://aka.ms/azureadvisordashboard) を開きます。

2.  Advisor ダッシュボードで、 **[パフォーマンス]** タブを選択します。

## <a name="next-steps"></a>次のステップ

Advisor の推奨事項の詳細については、以下を参照してください。

* [Advisor 入門](advisor-overview.md)
* [Advisor の使用を開始する](advisor-get-started.md)
* [Advisor スコア](azure-advisor-score.md)
* [Advisor のコストに関する推奨事項](advisor-cost-recommendations.md)
* [Advisor の信頼性に関する推奨事項](advisor-high-availability-recommendations.md)
* [Advisor のセキュリティに関する推奨事項](advisor-security-recommendations.md)
* [Advisor のオペレーショナル エクセレンスに関する推奨事項](advisor-operational-excellence-recommendations.md)
* [Advisor REST API](/rest/api/advisor/)