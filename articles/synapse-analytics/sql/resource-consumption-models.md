---
title: Synapse SQL のリソースの使用
description: Azure Synapse Analytics の Synapse SQL 使用モデルについて学習します。
services: synapse analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: overview
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: 03144d478be0053ac77709132b08cdf17b062fb0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101666302"
---
# <a name="synapse-sql-resource-consumption"></a>Synapse SQL のリソースの使用

この記事では、Synapse SQL のリソースの使用モデルについて説明します。

## <a name="serverless-sql-pool"></a>サーバーレス SQL プール

サーバーレス SQL プールは、適切なサイズを選択する必要がない、クエリごとの従量課金サービスです。 システムは要件に基づいて自動的に調整されるため、インフラストラクチャの管理やソリューションに適したサイズを選択する作業から解放されます。

## <a name="dedicated-sql-pool---data-warehouse-units-dwus-and-compute-data-warehouse-units-cdwus"></a>専用 SQL プール - Data Warehouse ユニット (DWU) とコンピューティング Data Warehouse ユニット (cDWU)

最適な Data Warehouse ユニット (DWU) の数の選択についての推奨事項と、ユニットの数を変更する方法を示します。

### <a name="data-warehouse-units"></a>Data Warehouse ユニット

Synapse SQL プールは、プロビジョニングされる分析リソースのコレクションを表します。 分析リソースは、CPU、メモリ、および IO の組み合わせとして定義されます。 これらの 3 つのリソースは、Data Warehouse ユニット (DWU) と呼ばれるコンピューティング スケールのユニットにバンドルされます。 DWU は、コンピューティング リソースとパフォーマンスの抽象的な正規化された単位を表します。 サービス レベルを変更すると、システムで使用できる DWU の数も変わります。 また、この変更によって、システムのパフォーマンスとコストが調整されます。

パフォーマンス向上のために、Data Warehouse ユニットの数を増やすことができます。 パフォーマンスを低下させるには、Data Warehouse ユニットの数を減らします。 ストレージのコストとコンピューティングのコストは、別々に請求されます。したがって、Data Warehouse ユニットの数を変更してもストレージ コストには影響しません。

Data Warehouse ユニットのパフォーマンスは、次のようなデータ ウェアハウスのワークロードのメトリックに基づいています。

- 標準的なデータ ウェアハウス クエリがどれだけ速く多数の行をスキャンし、次に複雑な集計を実行できるか。 これは I/O と CPU を集中的に使用する操作です。
- データ ウェアハウスがどれだけ速く Azure Storage BLOB または Azure Data Lake からデータを取り込むことができるか。 これはネットワークと CPU を集中的に使用する操作です。
- [`CREATE TABLE AS SELECT`](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true) T-SQL コマンドがどれだけ速くテーブルをコピーできるか。 この操作には、ストレージからのデータの読み取り、アプライアンスのノード間でのデータ分散、ストレージへのデータの再書き込みが必要です。 これは CPU、IO、ネットワークを集中的に使用する操作です。

DWU の数を増やすと、次のメリットが得られます。

- スキャン、集計、CTAS ステートメントに関するシステムのパフォーマンスが直線的に向上します。
- PolyBase の読み込み操作のリーダーとライターの数が増えます。
- コンカレント クエリとコンカレンシー スロットの最大数が増えます。

### <a name="service-level-objective"></a>サービス レベル目標

サービス レベル目標 (SLO) は、データ ウェアハウスのコストとパフォーマンス レベルを決定するスケーラビリティ設定です。 Gen2 用のサービス レベルは、コンピューティング データ ウェアハウス単位 (cDWU) で測定されます (例: DW2000c)。 Gen1 サービス レベルは DWU の単位で計測されます (例: DW2000)。

サービス レベル目標 (SLO) は、データ ウェアハウスのコストとパフォーマンス レベルを決定するスケーラビリティ設定です。 Gen2 専用 SQL プールのサービス レベルは、DW2000c など、Data Warehouse ユニット (DWU) で測定されます。

> [!NOTE]
> Azure Synapse Analytics Gen2 には、最近になって、最低 100 cDWU のコンピューティング レベルをサポートするための新しいスケール機能が追加されました。 現在 Gen1 を使用していて小さいコンピューティング レベルを必要とする既存のデータ ウェアハウスでは、現在追加コストなしで利用可能なリージョンで Gen2 にアップグレードできます。  お使いのリージョンがまだサポートされていない場合は、サポートされているリージョンにアップグレードできます。 詳細については、[Gen2 へのアップグレード](../sql-data-warehouse/upgrade-to-latest-generation.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)に関するページを参照してください。

T-SQL では、SERVICE_OBJECTIVE 設定によって専用 SQL プールのサービス レベルとパフォーマンス レベルが決まります。

```sql
CREATE DATABASE mySQLDW
(Edition = 'Datawarehouse'
 ,SERVICE_OBJECTIVE = 'DW1000c'
)
;
```

### <a name="performance-tiers-and-data-warehouse-units"></a>パフォーマンス レベルと Data Warehouse ユニット

各パフォーマンス レベルで、Data Warehouse ユニットの測定単位は若干異なります。 スケールの単位は課金に直接つながるため、この違いは請求書に反映されます。

- Gen1 データ ウェアハウスは、データ ウェアハウス単位 (DWU) で測定されます。
- Gen2 データ ウェアハウスは、コンピューティング データ ウェアハウス単位 (cDWU) で測定されます。

DWU と cDWU はいずれも、コンピューティングのスケール アップとスケール ダウン、データ ウェアハウスの使用が不要になった場合のコンピューティングの一時停止をサポートしています。 これらの操作はすべて、オンデマンドで実行できます。 Gen2 では、パフォーマンス向上のためにコンピューティング ノードでのローカル ディスク ベースのキャッシュを使用します。 スケール操作やシステムの一時停止を行うと、このキャッシュが無効化されるため、最適なパフォーマンスを実現する前にキャッシュの準備期間が必要となります。  

データ ウェアハウス ユニットを増やすと、コンピューティング リソースが直線的に増加します。 Gen2 は最適なクエリ パフォーマンスと最大のスケールを提供します。 Gen2 システムは、キャッシュも最大限に活用します。

#### <a name="capacity-limits"></a>容量制限

各 SQL Server (たとえば myserver.database.windows.net) には、特定の数の Data Warehouse ユニットを許可する[データベース トランザクション ユニット (DTU)](../../azure-sql/database/service-tiers-dtu.md) クォータがあります。 詳細については、[ワークロード管理の容量制限](../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#workload-management)に関する記事を参照してください。

### <a name="assess-the-number-of-data-warehouse-units-you-need"></a>必要な Data Warehouse ユニットの数を評価する

理想的なデータ ウェアハウス ユニットの数は、ワークロードと、システムに読み込まれているデータの量によって大きく変わります。

ワークロードに最適な DWU を特定する手順は次のとおりです。

1. 最初に小さな DWU を選択します。
2. システムへのデータの読み込みをテストする際に、アプリケーションのパフォーマンスを監視し、選択した DWU の数に対するパフォーマンスの変化を観察します。
3. 定期的なピーク アクティビティ期間のための追加要件があれば識別します。 アクティビティに顕著なピークと谷があることを示すワークロードには、頻繁なスケーリングが必要になる場合があります。

SQL プールは、大量のコンピューティングをプロビジョニングし、相当な量のデータにクエリを実行できるスケールアウト システムです。 特に大規模な DWU での実際のスケール機能を確認する場合は、CPU に十分なデータをフィードできるように、スケール時のデータ セットのスケールを決定してください。 スケールのテストでは、少なくとも 1 TB を使用することをお勧めします。

> [!NOTE]
>
> コンピューティング ノード間で作業を分割することができる場合、クエリのパフォーマンスを引き上げるには、並列処理を増やすしかありません。 スケーリングしてもパフォーマンスが変わらない場合は、テーブルのデザインやクエリのチューニングが必要な可能性があります。 クエリのチューニングの概要については、[ユーザー クエリの管理](../overview-terminology.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)に関するページを参照してください。

### <a name="permissions"></a>アクセス許可

Data Warehouse ユニットを変更するには、「[ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?view=azure-sqldw-latest&preserve-view=true)」で説明されているアクセス許可が必要です。

SQL DB 共同作成者や SQL Server 共同作成者などの Azure 組み込みロールで DWU 設定を変更できます。

#### <a name="view-current-dwu-settings"></a>現在の DWU 設定の表示

現在の DWU 設定を表示するには、次の手順に従います。

1. Visual Studio で SQL Server オブジェクト エクスプローラーを開きます。
2. 論理 SQL サーバーに関連付けられている master データベースに接続します。
3. sys.database_service_objectives 動的管理ビューから選択します。 たとえば次のようになります。

```sql
SELECT  db.name [Database]
,        ds.edition [Edition]
,        ds.service_objective [Service Objective]
FROM    sys.database_service_objectives   AS ds
JOIN    sys.databases                     AS db ON ds.database_id = db.database_id
;
```

### <a name="change-data-warehouse-units"></a>Data Warehouse ユニットの変更

#### <a name="azure-portal"></a>Azure portal

DWU を変更するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) を開き、データベースを開いて、 **[スケール]** を選択します。

2. **[スケール]** で、スライダーを左または右に移動して DWU 設定を変更します。

3. **[保存]** を選択します。 確認メッセージが表示されます。 **[はい]** を選択して確定するか、 **[いいえ]** を選択して取り消します。

#### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

DWU を変更するには、[Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) PowerShell コマンドレットを使用します。 次の例では、MyServer サーバーにホストされているデータベース MySQLDW のサービスレベル目標を DW1000 に設定します。

```Powershell
Set-AzSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000c"
```

詳細については、[Azure Synapse Analytics の PowerShell コマンドレット](../sql-data-warehouse/sql-data-warehouse-reference-powershell-cmdlets.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)に関するページを参照してください。

#### <a name="t-sql"></a>T-SQL

T-SQL で現在の DWU の設定を表示したり、設定を変更したり、進行状況を確認したりできます。

DWU を変更するには、次の手順に従います。

1. サーバーに関連付けられている master データベースに接続します。
2. [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?view=azure-sqldw-latest&preserve-view=true) TSQL ステートメントを使います。 次の例では、MySQLDW データベースのサービス レベル目標を DW1000c に設定します。

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000c')
;
```

#### <a name="rest-apis"></a>REST API

DWU を変更するには、[データベースの作成または更新](/sql/t-sql/statements/alter-database-transact-sql?view=azure-sqldw-latest&preserve-view=true) REST API を使います。 次の例では、サーバー MyServer でホストされているデータベース MySQLDW のサービス レベル目標を DW1000c に設定します。 サーバーは "ResourceGroup1" という名前の Asure リソース グループ内にあります。

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

REST API のその他の例については、[Azure Synapse Analytics の REST API](../sql-data-warehouse/sql-data-warehouse-manage-compute-rest-api.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) に関する記事を参照してください。

### <a name="check-status-of-dwu-changes"></a>DWU 変更の状態の確認

DWU の変更は、完了までに数分かかる場合があります。 スケーリングを自動的に行う場合は、別のアクションに進む前に特定の操作が完了していることを確認するロジックを実装することを検討してください。

さまざまなエンドポイントを通じてデータベースの状態を確認することで、自動化を適切に実装できます。 ポータルでは、操作の完了通知とデータベースの現在の状態が提供されます。ただし、プログラムを使って状態を確認することはできません。

Azure portal では、スケールアウト操作についてデータベースの状態を確認することはできません。

DWU の変更の状態を確認するには、次の手順に従います。

1. サーバーに関連付けられている master データベースに接続します。
2. 次のクエリを送信して、データベースの状態を確認します。

```sql
SELECT    *
FROM      sys.databases
;
```

1. 次のクエリを送信して、操作の状態を確認します。

```sql
SELECT    *
FROM      sys.dm_operation_status
WHERE     resource_type_desc = 'Database'
AND       major_resource_id = 'MySQLDW'
;
```

この DMV は、操作や操作の状態 (IN_PROGRESS または COMPLETED のどちらか) などの専用 SQL プールに対するさまざまな管理操作に関する情報を返します。

### <a name="the-scaling-workflow"></a>スケーリングのワークフロー

スケール操作を開始すると、システムはまず、すべての開いているセッションを強制終了し、すべての開いているトランザクションをロールバックして整合性のある状態を確保します。 スケール操作の場合、このトランザクションのロールバックが完了した後でのみスケーリングが行われます。  

- スケールアップ操作の場合、システムはすべてのコンピューティング ノードをデタッチし、追加のコンピューティング ノードをプロビジョニングした後、ストレージ レイヤーに再アタッチします。
- スケールダウン操作の場合、システムはすべてのコンピューティング ノードをデタッチした後、必要なノードのみをストレージ レイヤーに再アタッチします。

## <a name="next-steps"></a>次のステップ

パフォーマンスの管理の詳細については、[ワークロード管理用のリソース クラス](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)と、[メモリとコンカレンシーの制限](../sql-data-warehouse/memory-concurrency-limits.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)に関するページを参照してください。