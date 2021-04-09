---
title: Azure Cosmos DB データの監視のリファレンス | Microsoft Docs
description: Azure Cosmos DB でログとメトリックを監視する際に必要となる重要なリファレンス資料。
author: SnehaGunda
ms.service: cosmos-db
ms.topic: how-to
ms.date: 12/07/2020
ms.author: sngun
ms.custom: subject-monitoring
ms.openlocfilehash: 5f542b35110a6d967640ad91faead75f6cc0e0c2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100593280"
---
# <a name="monitoring-azure-cosmos-db-data-reference"></a>Azure Cosmos DB データの監視のリファレンス

[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

この記事では、Azure Cosmos DB のパフォーマンスと可用性を分析する目的で収集されるログとメトリック データのリファレンスを提供します。 Azure Cosmos DB の監視データの収集と分析の詳細については、[Azure Cosmos DB の監視](monitor-cosmos-db.md)に関する記事を参照してください。

## <a name="metrics"></a>メトリック

Azure Cosmos DB に対応するすべてのメトリックは、**Cosmos DB 標準メトリック** 名前空間に格納されています。 すべての Azure Monitor サポート メトリック (Azure Cosmos DB を含む) の一覧については、[Azure Monitor でサポートされているメトリック](../azure-monitor/essentials/metrics-supported.md)に関するページを参照してください。 このセクションには、Azure Cosmos DB 用に自動的に収集されたすべてのプラットフォーム メトリックが一覧表示されています。  

### <a name="request-metrics"></a>要求のメトリック

|メトリック (メトリックの表示名)|ユニット (集計の種類) |説明|Dimensions| 時間の細分性| 従来のメトリックのマッピング | 使用法 |
|---|---|---|---| ---| ---| ---|
| TotalRequests (要求の合計数) | カウント (カウント) | 行われた要求の数| DatabaseName、CollectionName、Region、StatusCode| All | TotalRequests、Http 2xx、Http 3xx、Http 400、Http 401、内部サーバー エラー、サービス使用不可、要求の調整、1 秒あたりの平均要求数 | 分単位の細分性で、コンテナーの状態コードごとの要求数を監視するために使用されます。 1 秒あたりの平均要求数を取得するには、分単位の Count 集計を使用して 60 で割ります。 |
| MetadataRequests (メタデータの要求数) |カウント (カウント) | メタデータの要求数。 Azure Cosmos DB ではアカウントごとにシステム メタデータ コンテナーが保持されており、コレクションやデータベースなどとそれらの構成を無料で列挙できます。 | DatabaseName、CollectionName、Region、StatusCode| All| |メタデータ要求によるスロットルを監視するために使用されます。|
| MongoRequests (Mongo 要求数) | カウント (カウント) | 実行された Mongo 要求の数 | DatabaseName、CollectionName、Region、CommandName、ErrorCode| All |Mongo クエリ要求率、Mongo 更新要求率、Mongo 削除要求率、Mongo 挿入要求率、Mongo カウント要求率| Mongo 要求エラー、コマンドのタイプごとの使用量を監視するために使用されます。 |

### <a name="request-unit-metrics"></a>要求ユニット メトリック

|メトリック (メトリックの表示名)|ユニット (集計の種類)|説明|Dimensions| 時間の細分性| 従来のメトリックのマッピング | 使用法 |
|---|---|---|---| ---| ---| ---|
| MongoRequestCharge (Mongo 要求の料金) | カウント (合計) |使用された Mongo 要求の単位数| DatabaseName、CollectionName、Region、CommandName、ErrorCode| All |Mongo クエリ要求の料金、Mongo 更新要求の料金、Mongo 削除要求の料金、Mongo 挿入要求の料金、Mongo カウント要求の料金| 1 分間の Mongo リソース RU を監視するために使用されます。|
| TotalRequestUnits (合計要求ユニット数)| カウント (合計) | 消費された要求の単位数| DatabaseName、CollectionName、Region、StatusCode |All| TotalRequestUnits| 1 分単位の細分性で RU の合計使用量を監視するために使用されます。 1 秒あたりの RU の平均使用量を取得するには、分単位の合計量を使用して 60 で割ります。|
| ProvisionedThroughput (プロビジョニングされたスループット)| カウント (最大) |コンテナー単位の細分性でプロビジョニングされたスループット| DatabaseName、ContainerName| 5 M| | コンテナーあたりのプロビジョニングされたスループットを監視するために使用されます。|

### <a name="storage-metrics"></a>Storage のメトリック

|メトリック (メトリックの表示名)|ユニット (集計の種類)|説明|Dimensions| 時間の細分性| 従来のメトリックのマッピング | 使用法 |
|---|---|---|---| ---| ---| ---|
| AvailableStorage (使用可能なストレージ) |バイト (合計) | リージョンあたりの 5 分単位の細分性で報告された使用可能なストレージの合計| DatabaseName、CollectionName、Region| 5 M| 使用可能なストレージ| 使用可能なストレージ容量を監視するために使用されます (固定ストレージ コレクションにのみ適用されます)。最小の細分性は 5 分にする必要があります。| 
| DataUsage (データ利用状況) |バイト (合計) |リージョンあたりの 5 分単位の細分性で報告されたデータ使用量の合計| DatabaseName、CollectionName、Region| 5 M |データ サイズ | コンテナーおよびリージョンでの合計データ使用量を監視するために使用されます。最小の細分性は 5 分にする必要があります。|
| IndexUsage (インデックスの使用量) | バイト (合計) |リージョンあたりの 5 分単位の細分性で報告されたインデックス使用量の合計| DatabaseName、CollectionName、Region| 5 M| インデックス サイズ| コンテナーおよびリージョンでの合計データ使用量を監視するために使用されます。最小の細分性は 5 分にする必要があります。 |
| DocumentQuota (ドキュメントのクォータ) | バイト (合計) | リージョンあたりの 5 分単位の細分性で報告されたストレージ クォータの合計。| DatabaseName、CollectionName、Region| 5 M |ストレージの容量| コンテナーおよびリージョンでの合計クォータを監視するために使用されます。最小の細分性は 5 分にする必要があります。|
| DocumentCount (ドキュメント数) | カウント (合計) |リージョンあたりの 5 分単位の細分性で報告された合計ドキュメント数| DatabaseName、CollectionName、Region| 5 M |ドキュメント数|コンテナーおよびリージョンでのドキュメント数を監視するために使用されます。最小の細分性は 5 分にする必要があります。|

### <a name="latency-metrics"></a>待機時間のメトリック

|メトリック (メトリックの表示名)|ユニット (集計の種類)|説明|Dimensions| 時間の細分性| 使用法 |
|---|---|---|---| ---| ---|
| ReplicationLatency (レプリケーションの待機時間)| ミリ秒 (最小、最大、平均) | geo 対応アカウントのソースおよびターゲット リージョン全体の P99 のレプリケーション待機時間| SourceRegion、TargetRegion| All | geo レプリケートされたアカウントの任意の 2 つのリージョン間で P99 のレプリケーション待機時間を監視するために使用されます。 |
| サーバー側の待機時間| ミリ秒 (平均) | サーバーが要求の処理に要した時間。 | CollectionName、ConnectionMode、DatabaseName、OperationType、PublicAPIType、Region | All | Azure Cosmos DB サーバーでの要求の待機時間の監視に使用されます。 |

### <a name="availability-metrics"></a>可用性のメトリック

|メトリック (メトリックの表示名) |ユニット (集計の種類)|説明| 時間の細分性| 従来のメトリックのマッピング | 使用法 |
|---|---|---|---| ---| ---|
| ServiceAvailability (サービスの可用性)| パーセント (最小、最大) | アカウントは 1 時間単位の細分性で可用性を要求します| 1 時間 | サービスの可用性 | 成功した要求の合計数に対する割合を表します。 状態コードが 410、500、または 503 の場合、要求はシステム エラーのために失敗したと見なされます。時間単位の細分性でアカウントの可用性を監視するために使用されます。 |

### <a name="cassandra-api-metrics"></a>Cassandra API のメトリック

|メトリック (メトリックの表示名)|ユニット (集計の種類)|説明|Dimensions| 時間の細分性| 使用法 |
|---|---|---|---| ---| ---|
| CassandraRequests (Cassandra 要求) | カウント (カウント) | 実行された Cassandra API 要求の数| DatabaseName、CollectionName、ErrorCode、Region、OperationType、ResourceType| All| Cassandra 要求を 1 単単位の細分性で監視するために使用されます。 1 秒あたりの平均要求数を取得するには、分単位の Count 集計を使用して 60 で割ります。|
| CassandraRequestCharges (Cassandra 要求の料金) | カウント (Sum、Min、Max、Avg) | Cassandra API によって使用される要求ユニット数 | DatabaseName、CollectionName、Region、OperationType、ResourceType| All| Cassandra API アカウントによって 1 分あたりで使用される RU を監視するために使用されます。|
| CassandraConnectionClosures (Cassandra 接続の終了) |カウント (カウント) |終了した Cassandra 接続の数| ClosureReason、Region| All | クライアントと Azure Cosmos DB Cassandra API の間の接続を監視するために使用されます。|

詳細については、[Azure Monitor でサポートされているすべてのプラットフォーム メトリック](../azure-monitor/essentials/metrics-supported.md)の一覧を参照してください。

## <a name="resource-logs"></a>リソース ログ

次の表に、Azure Cosmos DB のリソース ログのプロパティを示します。 リソース ログは Azure Monitor ログまたは Azure Storage に収集されます。 Azure Monitor では、ログは `MICROSOFT.DOCUMENTDB` という名前のリソース プロバイダー** の下にある、**AzureDiagnostics** テーブルに収集されます。

| Azure Storage のフィールドまたはプロパティ | Azure Monitor ログのプロパティ | 説明 |
| --- | --- | --- |
| **time** | **TimeGenerated** | 操作が発生した日時 (UTC)。 |
| **resourceId** | **リソース** | ログが有効になっている Azure Cosmos DB アカウント。|
| **category** | **カテゴリ** | Azure Cosmos DB の場合、利用できるログの種類には **DataPlaneRequests**、**MongoRequests**、**QueryRuntimeStatistics**、**PartitionKeyStatistics**、**PartitionKeyRUConsumption**、**ControlPlaneRequests**、**CassandraRequests**、**GremlinRequests** があります。 |
| **operationName** | **OperationName** | 操作の名前。 操作名は、`Create`、`Update`、`Read`、`ReadFeed`、`Delete`、`Replace`、`Execute`、`SqlQuery`、`Query`、`JSQuery`、`Head`、`HeadFeed`、`Upsert` のいずれかです。   |
| **properties** | 該当なし | このフィールドの内容については、以下の行を参照してください。 |
| **activityId** | **activityId_g** | ログに記録された操作の一意の GUID。 |
| **userAgent** | **userAgent_s** | 要求の送信元であったクライアント ユーザー エージェントを示す文字列。 ユーザー エージェントの形式は `{user agent name}/{version}` です。|
| **requestResourceType** | **requestResourceType_s** | アクセスされたリソースの種類。 この値は、database、container、document、attachment、user、permission、stored procedure、trigger、user-defined function、offer のいずれかです。 |
| **statusCode** | **statusCode_s** | 操作の応答状態。 |
| **requestResourceId** | **ResourceId** | 要求に関連するリソース ID。 実行された操作に応じて、この値は `databaseRid`、`collectionRid`、または `documentRid`を指すことがあります。|
| **clientIpAddress** | **clientIpAddress_s** | クライアントの IP アドレス。 |
| **requestCharge** | **requestCharge_s** | 操作で使用された 1 秒あたりの RU 数 |
| **collectionRid** | **collectionId_s** | コレクションの一意の ID。|
| **duration** | **duration_d** | 操作時間 (ミリ秒)。 |
| **requestLength** | **requestLength_s** | 要求の長さ (バイト単位)。 |
| **responseLength** | **responseLength_s** | 応答の長さ (バイト単位)。|
| **resourceTokenPermissionId** | **resourceTokenPermissionId_s** | このプロパティは、指定したリソース トークンのアクセス許可 ID を示します。 アクセス許可の詳細については、[データへのアクセスのセキュリティ保護](./secure-access-to-data.md#permissions)に関するページを参照してください。 |
| **resourceTokenPermissionMode** | **resourceTokenPermissionMode_s** | このプロパティは、リソース トークンの作成時に設定したアクセス許可モードを示します。 アクセス許可モードとして指定できるのは、"all" や "read" などの値です。 アクセス許可の詳細については、[データへのアクセスのセキュリティ保護](./secure-access-to-data.md#permissions)に関するページを参照してください。 |
| **resourceTokenUserRid** | **resourceTokenUserRid_s** | [リソース トークン](./secure-access-to-data.md#resource-tokens)が認証に使われると、この値は空ではありません。 値は、ユーザーのリソース ID を示します。 |
| **responseLength** | **responseLength_s** | 応答の長さ (バイト単位)。|

すべての Azure Monitor ログ カテゴリと関連スキーマのリンクの一覧については、[Azure Monitor のログ カテゴリとスキーマ](../azure-monitor/essentials/resource-logs-schema.md)に関するページを参照してください。 

## <a name="azure-monitor-logs-tables"></a>Azure Monitor ログ テーブル

Azure Cosmos DB は、Azure Monitor ログの Kusto テーブルを使用します。 これらのテーブルに対して Log Analytics を使用してクエリを実行できます。 Kusto bales が使用する一覧については、[Azure Monitor ログのテーブル参照](/azure/azure-monitor/reference/tables/tables-resourcetype#azure-cosmos-db)に関する記事を参照してください。

## <a name="see-also"></a>参照

- Azure Cosmos DB 監視の詳細については、「[Azure Cosmos DB の監視](monitor-cosmos-db.md)」を参照してください。
- Azure リソースの監視の詳細については、「[Azure Monitor を使用した Azure リソースの監視](../azure-monitor/essentials/monitor-azure-resource.md)」を参照してください。