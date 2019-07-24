---
title: Azure SQL Database の単一/プールされたデータベースのファイル領域管理 | Microsoft Docs
description: このページでは、Azure SQL Database の単一データベースおよびプールされたデータベースを使用してファイル領域を管理する方法について説明します。また、単一データベースまたはプールされたデータベースを縮小する必要があるかどうかを判断する方法、データベースの縮小操作を実行する方法を示すコード サンプルを紹介します。
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: jrasnick, carlrab
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: 043ceb6c46155ed169c080d08f37688b47e3e4b9
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57881165"
---
# <a name="manage-file-space-for-single-and-pooled-databases-in-azure-sql-database"></a>Azure SQL Database で単一データベースおよびプールされたデータベースのファイル領域を管理する

この記事では、Azure SQL Database の単一データベースおよびプールされたデータベースのさまざまな種類の記憶域スペースと、データベースとエラスティック プールに割り当てられたファイル領域を明示的に管理する必要がある場合に実行できる手順について説明します。

> [!NOTE]
> この記事は、Azure SQL Database のマネージド インスタンスのデプロイ オプションには適用されません。

## <a name="overview"></a>概要

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager モジュールは Azure SQL Database で引き続きサポートされますが、今後の開発はすべて Az.Sql モジュールを対象に行われます。 これらのコマンドレットについては、「[AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)」を参照してください。 Az モジュールと AzureRm モジュールのコマンドの引数は実質的に同じです。

Azure SQL Database の単一データベースおよびプールされたデータベースでは、データベースの基礎となるデータ ファイルの割り当てが使用データ ページ数よりも大きくなるようなワークロード パターンがあります。 この状況は、使用領域が増えた後にデータが削除された場合に発生する可能性があります。 これは、データの削除時に割り当てられていたファイル領域が自動的に再利用されないためです。

次のシナリオでは、ファイル領域の使用率の監視とデータ ファイルの圧縮が必要になる場合があります。

- データベースに割り当てられたファイル領域がプールのサイズ上限に達した場合に、エラスティック プール内のデータの増大を許可する。
- 単一データベースまたはエラスティック プールの最大サイズの縮小を許可する。
- 単一データベースまたはエラスティック プールを、よりサイズ上限の低い異なるサービス レベルまたはパフォーマンス レベルに変更することを許可する。

### <a name="monitoring-file-space-usage"></a>ファイル領域の使用状況の監視

Azure portal に表示されるほとんどのストレージ領域のメトリックと次の API は、使用されるデータ ページのサイズを測定するだけです。

- PowerShell [get-metrics](https://docs.microsoft.com/powershell/module/az.monitor/get-azmetric) などの Azure Resource Manager ベースのメトリック API
- T-SQL: [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)

ただし、次の API は、データベースとエラスティック プールに割り当てられている領域のサイズを測ることもできます。

- T-SQL: [sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)
- T-SQL: [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)

### <a name="shrinking-data-files"></a>データ ファイルの圧縮

データベースのパフォーマンスに影響を与える可能性があるため、未使用の割り当て領域を再利用するために SQL Database サービスがデータ ファイルを自動的に縮小することはありません。  ただし、「[未使用の割り当て済み領域を再利用する](#reclaim-unused-allocated-space)」で説明されている手順に従って、データ ファイルを選択した時点でセルフサービスによって縮小できます。

> [!NOTE]
> データ ファイルとは異なり、ログ ファイルの操作はデータベースのパフォーマンスに影響を与えないため、ログ ファイルは SQL Database サービスによって自動的に縮小されます。 

## <a name="understanding-types-of-storage-space-for-a-database"></a>データベースの記憶域スペースの種類

データベースのファイル領域を管理するには、以下に示す記憶域スペースの量について理解することが重要です。

|データベースの量|定義|説明|
|---|---|---|
|**使用済みのデータ領域**|データベース データを 8 KB ページに格納するために使用された領域の量。|一般的に、使用済みの領域は挿入 (削除) で増加 (減少) します。 操作に関連するデータの量とパターン、および断片化によっては、挿入または削除時に使用される領域が変わらない場合があります。 たとえば、各データ ページから 1 行を削除しても、使用される領域が減らない場合があります。|
|**割り当て済みのデータ領域**|データベース データの格納に使用できるフォーマット済みファイル領域の量。|割り当て済みの領域の量は自動的に増えますが、削除後に自動的に減ることはありません。 このような動作で領域を再フォーマットする必要がないため、以降の挿入はより高速になります。|
|**割り当て済みで未使用のデータ領域**|割り当て済みのデータ領域と使用済みのデータ領域の差。|この量は、データベースのデータ ファイルを縮小して再利用できる空き領域の上限を表します。|
|**データの最大サイズ**|データベース データの格納に使用できる領域の最大量。|データの最大サイズを超えて割り当て済みのデータ領域を拡大することはできません。|
||||

次の図は、データベースの異なる種類の記憶域スペース間の関係を示しています。

![記憶域スペースの種類と関係](./media/sql-database-file-space-management/storage-types.png)

## <a name="query-a-single-database-for-storage-space-information"></a>記憶域スペースの情報について単一データベースのクエリを実行する

次のクエリを使用して、単一データベースの記憶域スペースの量を確認できます。  

### <a name="database-data-space-used"></a>使用済みのデータベース データ領域

使用されているデータベース データ領域の量を返すように次のクエリを変更します。  クエリ結果の単位は MB です。

```sql
-- Connect to master
-- Database data space used in MB
SELECT TOP 1 storage_in_megabytes AS DatabaseDataSpaceUsedInMB
FROM sys.resource_stats
WHERE database_name = 'db1'
ORDER BY end_time DESC
```

### <a name="database-data-space-allocated-and-unused-allocated-space"></a>割り当て済みのデータベース データ領域と未使用の割り当て済み領域

次のクエリを使用して、割り当て済みデータベース データ領域と未使用の割り当て済み領域を返します。  クエリ結果の単位は MB です。

```sql
-- Connect to database
-- Database data space allocated in MB and database data space allocated unused in MB
SELECT SUM(size/128.0) AS DatabaseDataSpaceAllocatedInMB, 
SUM(size/128.0 - CAST(FILEPROPERTY(name, 'SpaceUsed') AS int)/128.0) AS DatabaseDataSpaceAllocatedUnusedInMB 
FROM sys.database_files
GROUP BY type_desc
HAVING type_desc = 'ROWS'
```
 
### <a name="database-data-max-size"></a>データベース データの最大サイズ

データベース データの最大サイズを返すように次のクエリを変更します。  クエリ結果の単位はバイトです。

```sql
-- Connect to database
-- Database data max size in bytes
SELECT DATABASEPROPERTYEX('db1', 'MaxSizeInBytes') AS DatabaseDataMaxSizeInBytes
```

## <a name="understanding-types-of-storage-space-for-an-elastic-pool"></a>エラスティック プールの記憶域スペースの種類

エラスティック プールのファイル領域を管理するには、以下に示す記憶域スペースの量について理解することが重要です。

|エラスティック プールの量|定義|説明|
|---|---|---|
|**使用済みのデータ領域**|エラスティック プール内のすべてのデータベースで使用されるデータ領域の合計。||
|**割り当て済みのデータ領域**|エラスティック プール内のすべてのデータベースで割り当て済みのデータ領域の合計。||
|**割り当て済みで未使用のデータ領域**|エラスティック プール内のすべてのデータベースで割り当て済みのデータ領域と使用済みのデータ領域の差。|この量は、データベースのデータ ファイルを縮小して再利用できる、エラスティック プールに割り当てられた領域の上限を表します。|
|**データの最大サイズ**|エラスティック プールのすべてのデータベースに使用できるデータ領域の最大量。|エラスティック プールに割り当てられた領域は、エラスティック プールの最大サイズを超えないようにする必要があります。  最大サイズを超えた場合は、割り当て済みで未使用の領域を、データベースのデータ ファイルを縮小して再利用できます。|
||||

## <a name="query-an-elastic-pool-for-storage-space-information"></a>記憶域スペースの情報についてエラスティック プールのクエリを実行する

次のクエリを使用して、エラスティック プールの記憶域スペースの量を確認できます。  

### <a name="elastic-pool-data-space-used"></a>使用済みのエラスティック プールのデータ領域

使用済みのエラスティック プールのデータ領域の量を返すように次のクエリを変更します。  クエリ結果の単位は MB です。

```sql
-- Connect to master
-- Elastic pool data space used in MB  
SELECT TOP 1 avg_storage_percent / 100.0 * elastic_pool_storage_limit_mb AS ElasticPoolDataSpaceUsedInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC
```

### <a name="elastic-pool-data-space-allocated-and-unused-allocated-space"></a>割り当て済みのエラスティック プールのデータ領域と未使用の割り当て済み領域

エラスティック プール内の各データベースの割り当て済み領域と未使用の割り当て済み領域の一覧表を返すように、次の PowerShell スクリプトを変更します。 この表では、未使用の割り当て済み領域があるデータベースが、最大サイズのデータベースから順に並んでいます。  クエリ結果の単位は MB です。  

エラスティック プールに割り当てられた領域の合計を確認するために、プール内の各データベースに割り当てられた領域を確認するためのクエリ結果も追加することができます。 割り当て済みエラスティック プール領域は、エラスティック プールの最大サイズを超えないようにする必要があります。  

PowerShell スクリプトには SQL Server PowerShell モジュールが必要です。インストール方法については、[PowerShell モジュールのダウンロード](https://docs.microsoft.com/sql/powershell/download-sql-server-ps-module)に関するページを参照してください。

```powershell
# Resource group name
$resourceGroupName = "rg1" 
# Server name
$serverName = "ls2" 
# Elastic pool name
$poolName = "ep1"
# User name for server
$userName = "name"
# Password for server
$password = "password"

# Get list of databases in elastic pool
$databasesInPool = Get-AzSqlElasticPoolDatabase `
    -ResourceGroupName $resourceGroupName `
    -ServerName $serverName `
    -ElasticPoolName $poolName
$databaseStorageMetrics = @()

# For each database in the elastic pool,
# get its space allocated in MB and space allocated unused in MB.
  
foreach ($database in $databasesInPool)
{
    $sqlCommand = "SELECT DB_NAME() as DatabaseName, `
    SUM(size/128.0) AS DatabaseDataSpaceAllocatedInMB, `
    SUM(size/128.0 - CAST(FILEPROPERTY(name, 'SpaceUsed') AS int)/128.0) AS DatabaseDataSpaceAllocatedUnusedInMB `
    FROM sys.database_files `
    GROUP BY type_desc `
    HAVING type_desc = 'ROWS'"
    $serverFqdn = "tcp:" + $serverName + ".database.windows.net,1433"
    $databaseStorageMetrics = $databaseStorageMetrics + 
        (Invoke-Sqlcmd -ServerInstance $serverFqdn `
        -Database $database.DatabaseName `
        -Username $userName `
        -Password $password `
        -Query $sqlCommand)
}
# Display databases in descending order of space allocated unused
Write-Output "`n" "ElasticPoolName: $poolName"
Write-Output $databaseStorageMetrics | Sort `
    -Property DatabaseDataSpaceAllocatedUnusedInMB `
    -Descending | Format-Table
```

以下のスクリーンショットは、スクリプトの出力例です。

![エラスティック プールの割り当て済み領域と未使用の割り当て領域の例](./media/sql-database-file-space-management/elastic-pool-allocated-unused.png)

### <a name="elastic-pool-data-max-size"></a>エラスティック プール データの最大サイズ

エラスティック プール データの最大サイズを返すように、次の T-SQL クエリを変更します。  クエリ結果の単位は MB です。

```sql
-- Connect to master
-- Elastic pools max size in MB
SELECT TOP 1 elastic_pool_storage_limit_mb AS ElasticPoolMaxSizeInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC
```

## <a name="reclaim-unused-allocated-space"></a>未使用の割り当て済み領域を再利用する

### <a name="dbcc-shrink"></a>DBCC の圧縮

未使用の割り当て済み領域を再利用するためのデータベースが特定されると、各データベースのデータ ファイルを圧縮するように次のコマンドのデータベース名を変更します。

```sql
-- Shrink database data space allocated.
DBCC SHRINKDATABASE (N'db1')
```

このコマンドは、実行中のデータベース パフォーマンスに影響を及ぼす可能性があります。また、可能であれば、使用率が低い期間中に実行してください。  

このコマンドの詳細については、[SHRINKDATABASE](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql) を参照してください。 

### <a name="auto-shrink"></a>自動圧縮

代わりに、データベースの自動圧縮を有効にすることもできます。  自動圧縮では、ファイル管理の複雑さが軽減され、`SHRINKDATABASE` または `SHRINKFILE` よりもデータベース パフォーマンスへの影響が低くなります。  自動圧縮は、多数のデータベースがあるエラスティック プールの管理に、特に役立てることができます。  ただし、自動圧縮は、`SHRINKDATABASE` および `SHRINKFILE` よりもファイル領域の解放の効果が低くなる可能性があります。
自動圧縮を有効にするには、次のコマンドのデータベースの名前を変更します。


```sql
-- Enable auto-shrink for the database.
ALTER DATABASE [db1] SET AUTO_SHRINK ON
```

このコマンドの詳細については、[DATABASE SET](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current) オプションをご覧ください。 

### <a name="rebuild-indexes"></a>インデックスの再構築

データベース データ ファイルの縮小後は、インデックスが断片化され、パフォーマンスの最適化の効果が失われる可能性があります。 パフォーマンスの低下が発生した場合、データベース インデックスの再構築を検討します。 インデックスの断片化と再構築の詳細については、「[インデックスの再構成と再構築](https://docs.microsoft.com/sql/relational-databases/indexes/reorganize-and-rebuild-indexes)」を参照してください。

## <a name="next-steps"></a>次の手順

- データベースの最大サイズについては、以下を参照してください。
  - [Azure SQL Database の単一データベースに対する仮想コアベースの購入モデルの制限](sql-database-vcore-resource-limits-single-databases.md)
  - [DTU ベースの購入モデルを使用した単一データベースに対するリソース制限](sql-database-dtu-resource-limits-single-databases.md)
  - [Azure SQL Database のエラスティック プールに対する仮想コアベースの購入モデルの制限](sql-database-vcore-resource-limits-elastic-pools.md)
  - [DTU ベースの購入モデルを使用したエラスティック プールに対するリソース制限](sql-database-dtu-resource-limits-elastic-pools.md)
- `SHRINKDATABASE` コマンドの詳細については、[SHRINKDATABASE](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql) を参照してください。 
- インデックスの断片化と再構築の詳細については、「[インデックスの再構成と再構築](https://docs.microsoft.com/sql/relational-databases/indexes/reorganize-and-rebuild-indexes)」を参照してください。
