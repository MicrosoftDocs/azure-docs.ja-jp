---
title: Azure SQL Data Warehouse の Data Warehouse ユニット (DWU、cDWU) | Microsoft Docs
description: 最適な Data Warehouse ユニット (DWU、cDWU) の数の選択についての推奨事項と、ユニットの数を変更する方法を示します。
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 5f6e24dfa1b5c4ea4f0748af81104edfe88ceeae
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58099105"
---
# <a name="data-warehouse-units-dwus-and-compute-data-warehouse-units-cdwus"></a>Data Warehouse ユニット (DWU) とコンピューティング Data Warehouse ユニット (cDWU)
最適な Data Warehouse ユニット (DWU、cDWU) の数の選択についての推奨事項と、ユニットの数を変更する方法を示します。 

## <a name="what-are-data-warehouse-units"></a>Data Warehouse ユニットとは
SQL Data Warehouse では、CPU、メモリ、および IO が Data Warehouse ユニット (DWU) と呼ばれるコンピューティング スケール単位にまとめられます。 DWU は、コンピューティング リソースとパフォーマンスの抽象的な正規化された単位を表します。 サービス レベルを変更すると、システムに割り当てられている DWU の数が変更され、システムのパフォーマンスとコストが調整されます。 

パフォーマンスの向上にコストをかける場合は、Data Warehouse ユニットの数を増やすことができます。 パフォーマンスのコストを抑える場合は、Data Warehouse ユニットの数を減らします。 ストレージのコストとコンピューティングのコストは、別々に請求されます。したがって、Data Warehouse ユニットの数を変更してもストレージ コストには影響しません。

Data Warehouse ユニットのパフォーマンスは、次のようなデータ ウェアハウスのワークロードのメトリックに基づいています。

- 標準的なデータ ウェアハウス クエリが大量の行をスキャンして、複雑な集計を実行するときの速度。 これは I/O と CPU を集中的に使用する操作です。
- データ ウェアハウスが Azure Storage Blob または Azure Data Lake からのデータを取り込むときの速度。 これはネットワークと CPU を集中的に使用する操作です。 
- [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) T-SQL コマンドがテーブルをコピーするときの速度。 この操作には、ストレージからのデータの読み取り、アプライアンスのノード間でのデータ分散、ストレージへのデータの再書き込みが必要です。 これは CPU、IO、ネットワークを集中的に使用する操作です。

DWU の数を増やすと、次のメリットが得られます。
- スキャン、集計、CTAS ステートメントに関するシステムのパフォーマンスが直線的に向上します。
- PolyBase の読み込み操作のリーダーとライターの数が増えます。
- コンカレント クエリとコンカレンシー スロットの最大数が増えます。

## <a name="service-level-objective"></a>サービス レベル目標
サービス レベル目標 (SLO) は、データ ウェアハウスのコストとパフォーマンス レベルを決定するスケーラビリティ設定です。 Gen2 用のサービス レベルは、コンピューティング データ ウェアハウス単位 (cDWU) で測定されます (例: DW2000c)。 Gen1 サービス レベルは DWU の単位で計測されます (例: DW2000)。
  > [!NOTE]
  > Azure SQL Data Warehouse Gen2 には、最近、最低 100 cDWU のコンピューティング レベルをサポートするための新しいスケール機能が追加されました。 現在 Gen1 を使用していて小さいコンピューティング レベルを必要とする既存のデータ ウェアハウスでは、現在追加コストなしで利用可能なリージョンで Gen2 にアップグレードできます。  お使いのリージョンがまだサポートされていない場合は、サポートされているリージョンにアップグレードできます。 詳細については、[Gen2 へのアップグレード](upgrade-to-latest-generation.md)に関するページを参照してください。

T-SQL では、サービス レベルと、データ ウェアハウスのパフォーマンス レベルは、SERVICE_OBJECTIVE 設定によって決定されます。

```sql
--Gen1
CREATE DATABASE myElasticSQLDW
WITH
(    SERVICE_OBJECTIVE = 'DW1000'
)
;

--Gen2
CREATE DATABASE myComputeSQLDW
WITH
(    SERVICE_OBJECTIVE = 'DW1000c'
)
;
```

## <a name="performance-tiers-and-data-warehouse-units"></a>パフォーマンス レベルと Data Warehouse ユニット

各パフォーマンス レベルで、Data Warehouse ユニットの測定単位は若干異なります。 スケールの単位は課金に直接つながるため、この違いは請求書に反映されます。

- Gen1 データ ウェアハウスは、データ ウェアハウス単位 (DWU) で測定されます。
- Gen2 データ ウェアハウスは、コンピューティング データ ウェアハウス単位 (cDWU) で測定されます。 

DWU と cDWU はいずれも、コンピューティングのスケール アップとスケール ダウン、データ ウェアハウスの使用が不要になった場合のコンピューティングの一時停止をサポートしています。 これらの操作はすべて、オンデマンドで実行できます。 Gen2 では、パフォーマンス向上のためにコンピューティング ノードでのローカル ディスク ベースのキャッシュを使用します。 スケール操作やシステムの一時停止を行うと、このキャッシュが無効化されるため、最適なパフォーマンスを実現する前にキャッシュの準備期間が必要となります。  

Data Warehouse ユニットを増やすと、コンピューティング リソースが直線的に増加します。 Gen2 は最適なクエリ パフォーマンスと最大のスケールを提供します。 これらのシステムは、キャッシュを最大限に活用します。

### <a name="capacity-limits"></a>容量制限
各 SQL Server (たとえば myserver.database.windows.net) には、特定の数の Data Warehouse ユニットを許可する[データベース トランザクション ユニット (DTU)](../sql-database/sql-database-what-is-a-dtu.md) クォータがあります。 詳細については、[ワークロード管理の容量制限](sql-data-warehouse-service-capacity-limits.md#workload-management)に関する記事を参照してください。


## <a name="how-many-data-warehouse-units-do-i-need"></a>必要な Data Warehouse ユニットの数
理想的な Data Warehouse ユニットの数は、ワークロードと、システムに読み込まれているデータの量に大きく依存します。

ワークロードに最適な DWU を特定する手順は次のとおりです。

1. 最初に小さな DWU を選択します。 
2. システムへのデータの読み込みをテストする際に、アプリケーションのパフォーマンスを監視し、選択した DWU の数に対するパフォーマンスの変化を観察します。
3. 定期的なピーク アクティビティ期間のための追加要件があれば識別します。 ワークロードがアクティビティに顕著なピークと谷があることを示しており、頻繁にスケール操作を行う正当な理由がある場合。

SQL Data Warehouse は、膨大な量のコンピューティングをプロビジョニングし、相当な量のデータのクエリを実行することができるスケールアウト システムです。 特に大規模な DWU での実際のスケール機能を確認する場合は、CPU に十分なデータをフィードできるように、スケール時のデータ セットのスケールを決定してください。 スケールのテストでは、少なくとも 1 TB を使用することをお勧めします。

> [!NOTE]
>
> コンピューティング ノード間で作業を分割することができる場合、クエリのパフォーマンスを引き上げるには、並列処理を増やすしかありません。 スケーリングしてもパフォーマンスが変わらない場合は、テーブルのデザインやクエリのチューニングが必要な可能性があります。 クエリのチューニングの概要については、[ユーザー クエリの管理](sql-data-warehouse-overview-manage-user-queries.md)に関するページを参照してください。 

## <a name="permissions"></a>アクセス許可

Data Warehouse ユニットを変更するには、「[ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql)」で説明されているアクセス許可が必要です。 

SQL DB 共同作成者や SQL Server 共同作成者などの Azure リソースの組み込みロールで DWU 設定を変更できます。 

## <a name="view-current-dwu-settings"></a>現在の DWU 設定の表示

現在の DWU 設定を表示するには、次の手順に従います。

1. Visual Studio で SQL Server オブジェクト エクスプローラーを開きます。
2. SQL Database 論理サーバーに関連付けられている master データベースに接続します。
3. sys.database_service_objectives 動的管理ビューから選択します。 たとえば次のようになります。 

```sql
SELECT  db.name [Database]
,       ds.edition [Edition]
,       ds.service_objective [Service Objective]
FROM    sys.database_service_objectives   AS ds
JOIN    sys.databases                     AS db ON ds.database_id = db.database_id
;
```

## <a name="change-data-warehouse-units"></a>Data Warehouse ユニットの変更

### <a name="azure-portal"></a>Azure ポータル
DWU または cDWU を変更するには、次の手順に従います。

1. [Azure Portal](https://portal.azure.com) を開き、データベースを開いて、**[スケール]** をクリックします。

2. **[スケール]** で、スライダーを左または右に移動して DWU 設定を変更します。

3. **[Save]** をクリックします。 確認メッセージが表示されます。 **[はい]** をクリックして確定します。キャンセルするには、**[いいえ]** をクリックします。

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

DWU または cDWU を変更するには、[Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) PowerShell コマンドレットを使用します。 次の例では、MyServer サーバーにホストされているデータベース MySQLDW のサービスレベル目標を DW1000 に設定します。

```Powershell
Set-AzSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000"
```

詳細については、[SQL Data Warehouse の PowerShell コマンドレット](sql-data-warehouse-reference-powershell-cmdlets.md)に関するページを参照してください。

### <a name="t-sql"></a>T-SQL
T-SQL で現在の DWU または cDWU の設定を表示したり、設定を変更したり、進行状況を確認したりできます。 

DWU または cDWU を変更するには、次の手順に従います。

1. SQL Database 論理サーバーに関連付けられている master データベースに接続します。
2. [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql) TSQL ステートメントを使用します。 次の例では、MySQLDW データベースのサービス レベル目標を DW1000 に設定します。 

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000')
;
```

### <a name="rest-apis"></a>REST API

DWU を変更するには、[データベースの作成または更新](/rest/api/sql/databases/createorupdate) REST API を使います。 次の例では、MyServer サーバーにホストされているデータベース MySQLDW のサービスレベル目標を DW1000 に設定します。 サーバーは "ResourceGroup1" という名前の Asure リソース グループ内にあります。

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

REST API の例については、「[SQL Data Warehouse の REST API](sql-data-warehouse-manage-compute-rest-api.md)」を参照してください。

## <a name="check-status-of-dwu-changes"></a>DWU 変更の状態の確認

DWU の変更は、完了までに数分かかる場合があります。 スケーリングを自動的に行う場合は、別のアクションに進む前に特定の操作が完了していることを確認するロジックを実装することを検討してください。 

さまざまなエンドポイントを通じてデータベースの状態を確認することで、自動化を適切に実装できます。 ポータルでは、操作の完了通知とデータベースの現在の状態が提供されます。ただし、プログラムを使って状態を確認することはできません。 

Azure Portal でスケールアウト操作のデータベースの状態を確認することはできません。

DWU の変更の状態を確認するには、次の手順に従います。

1. SQL Database 論理サーバーに関連付けられている master データベースに接続します。
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

この DMV は、SQL Data Warehouse 上の操作と操作の状態 (IN_PROGRESS または COMPLETED のいずれか) などのさまざまな管理操作に関する情報を返します。

## <a name="the-scaling-workflow"></a>スケーリングのワークフロー

スケール操作を開始すると、システムは最初にすべての開いているトランザクションを中止し、すべての開いているトランザクションをロールバックして一貫性のある状態を確保します。 スケール操作の場合、このトランザクションのロールバックが完了した後でのみスケーリングが行われます。  

- スケールアップ操作の場合、システムは追加のコンピューティングをプロビジョニングした後で、ストレージ レイヤーに再アタッチします。 
- スケールダウン操作の場合は、不要なノードがストレージからデタッチされ、残りのノードに再アタッチします。

## <a name="next-steps"></a>次の手順
パフォーマンスの管理の詳細については、[ワークロード管理用のリソース クラス](resource-classes-for-workload-management.md)と、[メモリとコンカレンシーの制限](memory-and-concurrency-limits.md)に関するページを参照してください。



