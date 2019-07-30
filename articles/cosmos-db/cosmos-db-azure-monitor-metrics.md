---
title: Azure Monitor から Azure Cosmos DB メトリックを入手する
description: ''
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: 2eb61a6b9afa3cabf1733be120dfbdacb7de4534
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276532"
---
# <a name="monitor-and-debug-azure-cosmos-db-metrics-from-azure-monitor"></a>Azure Monitor から Azure Cosmos DB メトリックを監視およびデバッグする

Azure Cosmos DB メトリックは Azure Monitor API から表示できます。 Azure Monitor では、複数の方法でメトリックを操作できます。たとえば、Azure ポータルや、REST API でアクセスしたり、PowerShell や CLI を使ってクエリを実行したりできます。 Azure Cosmos DB メトリックは、既定では 1 分間隔で収集される、待ち時間が短い数値です。これらのメトリックを集計することもできます。 これらのメトリックは、リアルタイム シナリオをサポートできます。  

この記事では、Azure portal を使って Azure Monitor から表示できるさまざまな Azure Cosmos DB メトリックについて説明します。 一般的なユース ケースと、Azure Cosmos DB メトリックを使用してこれらの問題を分析およびデバッグする方法に興味をお持ちの場合は、「[Azure Cosmos DB のメトリックを使用した監視とデバッグ](use-metrics.md)」の記事をご覧ください。 既存の Azure Cosmos アカウントの 1 つを使用して、データベース、コンテナー、リージョン、要求、オペレーションの各レベルでさまざまなメトリックを表示します。 そのため、サンプル データが含まれた Azure Cosmos アカウントがあることを確認し、そのデータに対して CRUD 操作を実行してください。

## <a name="view-metrics-from-azure-portal"></a>Azure portal からメトリックを表示する

1. [Azure Portal](https://portal.azure.com/) にサインインします。

1. 左側のナビゲーション バーから **[監視]** を選択し、 **[メトリック]** を選択します。

   ![Azure Monitor のメトリック ウィンドウ](./media/cosmos-db-azure-monitor-metrics/monitor-metrics-blade.png)

1. **[メトリック]** ウィンドウから、 **[リソースの選択]** を選択し、必要な**サブスクリプション**と**リソース グループ**を選択します。 **[リソースの種類]** で、 **[Azure Cosmos DB accounts]\(Azure Cosmos DB アカウント\)** を選択し、既存の Azure Cosmos アカウントの一つを選択し、 **[適用]** を選択します。 

   ![メトリックを表示する Cosmos DB アカウントの選択](./media/cosmos-db-azure-monitor-metrics/select-cosmosdb-account.png)

1. 次に、使用可能なメトリックの一覧からメトリックを選択できます。 要求ユニット、ストレージ、待機時間、可用性、Cassandra などに固有のメトリックを選択できます。 この一覧で使用可能なすべてのメトリックの詳細については、この記事の「[カテゴリ別のメトリック](#metrics-by-category)」セクションを参照してください。 この例では、 **[要求ユニット]** および集計値として **[Avg]** を選択します。 

   これらの詳細に加えて、メトリックの **[時間の範囲]** と **[時間の粒度]** を選択することもできます。 最大で、過去 30 日間のメトリックを表示できます。  フィルターを適用すると、そのフィルターに基づいてグラフが表示されます。 選択した期間に消費された要求ユニットの 1 分あたりの平均数を確認できます。  

   ![Azure portal からのメトリックの選択](./media/cosmos-db-azure-monitor-metrics/metric-types.png)

## <a name="add-filters-to-metrics"></a>メトリックにフィルターを追加する

メトリックと、特定の **CollectionName**、**DatabaseName**、**OperationType**、**Region**、および **StatusCode** によって表示されるグラフをフィルターすることもできます。 メトリックにフィルターを適用するには、 **[フィルターの追加]** を選択し、**OperationType** などの必要なプロパティを選択し、**Query** などの値を選択します。 その後グラフには、選択した期間のクエリ操作で消費された要求ユニットが表示されます。 ストアド プロシージャを介して実行された操作は、ログに記録されないため、OperationType メトリックでは使用できません。

![メトリック細分性を選択するためのフィルターの追加](./media/cosmos-db-azure-monitor-metrics/add-metrics-filter.png)

**[Apply splitting]\(分割の適用\)** オプションを使用すると、メトリックをグループ化できます。 たとえば、次の図に示すように、要求ユニットを操作の種類ごとにグループ化し、すべての操作のグラフを一度に表示できます。 

![分割の適用フィルターの追加](./media/cosmos-db-azure-monitor-metrics/apply-metrics-splitting.png)


## <a id="metrics-by-category"></a>カテゴリ別のメトリック

### <a name="request-metrics"></a>要求のメトリック
            
|メトリック (メトリックの表示名)|ユニット (集計の種類) |説明|Dimensions| 時間の細分性| 従来のメトリックのマッピング | 使用法 |
|---|---|---|---| ---| ---| ---|
| TotalRequests (要求の合計数) | カウント (カウント) | 行われた要求の数| DatabaseName、CollectionName、Region、StatusCode| All | TotalRequests、Http 2xx、Http 3xx、Http 400、Http 401、内部サーバー エラー、サービス使用不可、要求の調整、1 秒あたりの平均要求数 | 分単位の細分性で、コレクションの状態コードごとの要求数を監視するために使用されます。 1 秒あたりの平均要求数を取得するには、分単位の Count 集計を使用して 60 で割ります。 |
| MetadataRequests (メタデータの要求数) |カウント (カウント) | メタデータの要求数。 Azure Cosmos DB はメタデータ コレクションをアカウントごとに保持します。これにより、コレクションやデータベースなどとそれらの構成を無料で列挙できます。 | DatabaseName、CollectionName、Region、StatusCode| All| |メタデータ要求によるスロットルを監視するために使用されます。|
| MongoRequests (Mongo 要求数) | カウント (カウント) | 実行された Mongo 要求の数 | DatabaseName、CollectionName、Region、CommandName、ErrorCode| All |Mongo クエリ要求率、Mongo 更新要求率、Mongo 削除要求率、Mongo 挿入要求率、Mongo カウント要求率| Mongo 要求エラー、コマンドのタイプごとの使用量を監視するために使用されます。 |

### <a name="request-unit-metrics"></a>要求ユニット メトリック

|メトリック (メトリックの表示名)|ユニット (集計の種類)|説明|Dimensions| 時間の細分性| 従来のメトリックのマッピング | 使用法 |
|---|---|---|---| ---| ---| ---|
| MongoRequestCharge (Mongo 要求の料金) | カウント (合計) |使用された Mongo 要求の単位数| DatabaseName、CollectionName、Region、CommandName、ErrorCode| All |Mongo クエリ要求の料金、Mongo 更新要求の料金、Mongo 削除要求の料金、Mongo 挿入要求の料金、Mongo カウント要求の料金| 1 分間の Mongo リソース RU を監視するために使用されます。|
| TotalRequestUnits (合計要求ユニット数)| カウント (合計) | 消費された要求の単位数| DatabaseName、CollectionName、Region、StatusCode |All| TotalRequestUnits| 1 分単位の細分性で RU の合計使用量を監視するために使用されます。 1 秒あたりの RU の平均使用量を取得するには、分単位の合計量を使用して 60 で割ります。|
| ProvisionedThroughput (プロビジョニングされたスループット)| カウント (最大) |コレクション単位の細分性でプロビジョニングされたスループット| DatabaseName、CollectionName| 5 M| | コレクションあたりのプロビジョニングされたスループットを監視するために使用されます。|

### <a name="storage-metrics"></a>Storage のメトリック

|メトリック (メトリックの表示名)|ユニット (集計の種類)|説明|Dimensions| 時間の細分性| 従来のメトリックのマッピング | 使用法 |
|---|---|---|---| ---| ---| ---|
| AvailableStorage (使用可能なストレージ) |バイト (合計) | リージョンあたりの 5 分単位の細分性で報告された使用可能なストレージの合計| DatabaseName、CollectionName、Region| 5 M| 使用可能なストレージ| 使用可能なストレージ容量を監視するために使用されます (固定ストレージ コレクションにのみ適用されます)。最小の細分性は 5 分にする必要があります。| 
| DataUsage (データ利用状況) |バイト (合計) |リージョンあたりの 5 分単位の細分性で報告されたデータ使用量の合計| DatabaseName、CollectionName、Region| 5 M |データ サイズ | 収集時およびリージョンごとの合計データ使用量を監視するために使用されます。最小の細分性は 5 分にする必要があります。|
| IndexUsage (インデックスの使用量) | バイト (合計) |リージョンあたりの 5 分単位の細分性で報告されたインデックス使用量の合計| DatabaseName、CollectionName、Region| 5 M| インデックス サイズ| 収集時およびリージョンごとの合計データ使用量を監視するために使用されます。最小の細分性は 5 分にする必要があります。 |
| DocumentQuota (ドキュメントのクォータ) | バイト (合計) | リージョンあたりの 5 分単位の細分性で報告されたストレージ クォータの合計。| DatabaseName、CollectionName、Region| 5 M |ストレージの容量| 収集時およびリージョンごとの合計クォータを監視するために使用されます。最小の細分性は 5 分にする必要があります。|
| DocumentCount (ドキュメント数) | カウント (合計) |リージョンあたりの 5 分単位の細分性で報告された合計ドキュメント数| DatabaseName、CollectionName、Region| 5 M |ドキュメント数|収集時およびリージョンごとのドキュメント数を監視するために使用されます。最小の細分性は 5 分にする必要があります。|

### <a name="latency-metrics"></a>待機時間のメトリック

|メトリック (メトリックの表示名)|ユニット (集計の種類)|説明|Dimensions| 時間の細分性| 使用法 |
|---|---|---|---| ---| ---|
| ReplicationLatency (レプリケーションの待機時間)| ミリ秒 (最小、最大、平均) | geo 対応アカウントのソースおよびターゲット リージョン全体の P99 のレプリケーション待機時間| SourceRegion、TargetRegion| All | geo レプリケートされたアカウントの任意の 2 つのリージョン間で P99 のレプリケーション待機時間を監視するために使用されます。 |


### <a name="availability-metrics"></a>可用性のメトリック

|メトリック (メトリックの表示名) |ユニット (集計の種類)|説明| 時間の細分性| 従来のメトリックのマッピング | 使用法 |
|---|---|---|---| ---| ---|
| ServiceAvailability (サービスの可用性)| パーセント (最小、最大) | アカウントは 1 時間単位の細分性で可用性を要求します| 1 時間 | サービスの可用性 | 成功した要求の合計数に対する割合を表します。 状態コードが 410、500、または 503 の場合、要求はシステム エラーのために失敗したと見なされます。時間単位の細分性でアカウントの可用性を監視するために使用されます。 |


### <a name="cassandra-api-metrics"></a>Cassandra API のメトリック

|メトリック (メトリックの表示名)|ユニット (集計の種類)|説明|Dimensions| 時間の細分性| 使用法 |
|---|---|---|---| ---| ---|
| CassandraRequests (Cassandra 要求) | カウント (カウント) | 実行された Cassandra API 要求の数| DatabaseName、CollectionName、ErrorCode、Region、OperationType、ResourceType| All| Cassandra 要求を 1 単単位の細分性で監視するために使用されます。 1 秒あたりの平均要求数を取得するには、分単位の Count 集計を使用して 60 で割ります。|
| CassandraRequestCharges (Cassandra 要求の料金) | カウント (Sum、Min、Max、Avg) | Cassandra API 要求によって使用される要求ユニット数| DatabaseName、CollectionName、Region、OperationType、ResourceType| All| Cassandra API アカウントによって 1 分あたりで使用される RU を監視するために使用されます。|
| CassandraConnectionClosures (Cassandra 接続の終了) |カウント (カウント) |終了した Cassandra 接続の数| ClosureReason、Region| All | クライアントと Azure Cosmos DB Cassandra API の間の接続を監視するために使用されます。|

## <a name="next-steps"></a>次の手順

* [Azure Cosmos DB アカウント メトリック ウィンドウからメトリックを表示および監視する](use-metrics.md)

* [Azure Cosmos DB での診断ログ](logging.md)
