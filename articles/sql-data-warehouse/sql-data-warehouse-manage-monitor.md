---
title: "DMV を利用してワークロードを監視する | Microsoft Docs"
description: "DMV を利用してワークロードを監視するについて説明します。"
services: sql-data-warehouse
documentationcenter: NA
author: sqlmojo
manager: jhubbard
editor: 
ms.assetid: 69ecd479-0941-48df-b3d0-cf54c79e6549
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: performance
ms.date: 10/31/2016
ms.author: joeyong;barbkess
ms.openlocfilehash: 7ce6c2cdf1e28852da536414533ccdcdaeb437e5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-your-workload-using-dmvs"></a>DMV を利用してワークロードを監視する
この記事では、動的管理ビュー (DMV) を使用し、Azure SQL データ ウェアハウスでワークロードを監視し、クエリの実行を調査する方法について説明します。

## <a name="permissions"></a>アクセス許可
この記事で DMV をクエリするには、VIEW DATABASE STATE または CONTROL のいずれかのアクセス許可が必要です。 通常は、さらに制限の厳しい VIEW DATABASE STATE のアクセス許可を付与することが推奨されます。

```sql
GRANT VIEW DATABASE STATE TO myuser;
```

## <a name="monitor-connections"></a>接続を監視する
SQL Data Warehouse へのすべてのログインは、[sys.dm_pdw_exec_sessions][sys.dm_pdw_exec_sessions] に記録されます。  この DMV には、過去 10,000 件のログインが含まれています。  プライマリ キーである session_id が、新規ログオンのたびに順次割り当てられます。

```sql
-- Other Active Connections
SELECT * FROM sys.dm_pdw_exec_sessions where status <> 'Closed' and session_id <> session_id();
```

## <a name="monitor-query-execution"></a>クエリ実行を監視する
SQL Data Warehouse で実行されたすべてのクエリは、[sys.dm_pdw_exec_requests][sys.dm_pdw_exec_requests] に記録されます。  この DMV には、実行された過去 10,000 件のクエリが含まれています。  request_id により各クエリが一意に識別されます。これはこの DMV のプライマリ キーです。  request_id は、新しいクエリごとに順番に割り当てられ、クエリ ID を表す QID がプレフィックスとして付加されます。  特定の session_id のこの DMV にクエリを実行すると、そのログオンのクエリがすべて表示されます。

> [!NOTE]
> ストアド プロシージャでは複数の要求 ID が使用されます。  要求 ID は順次割り当てられます。 
> 
> 

クエリ実行プラン、および特定のクエリの実行時間を調査するには、次の手順に従います。

### <a name="step-1-identify-the-query-you-wish-to-investigate"></a>手順 1: 調査するクエリを特定する
```sql
-- Monitor active queries
SELECT * 
FROM sys.dm_pdw_exec_requests 
WHERE status not in ('Completed','Failed','Cancelled')
  AND session_id <> session_id()
ORDER BY submit_time DESC;

-- Find top 10 queries longest running queries
SELECT TOP 10 * 
FROM sys.dm_pdw_exec_requests 
ORDER BY total_elapsed_time DESC;

-- Find a query with the Label 'My Query'
-- Use brackets when querying the label column, as it it a key word
SELECT  *
FROM    sys.dm_pdw_exec_requests
WHERE   [label] = 'My Query';
```

上記のクエリ結果から、調査するクエリの **要求 ID を書き留めます** 。

**中断** 状態のクエリは、同時実行の制限が原因でクエリが実行されています。 これらのクエリは、UserConcurrencyResourceType 型の sys.dm_pdw_waits 待機クエリにも表示されます。 同時実行の制限の詳細については、 [同時実行とワークロード管理][Concurrency and workload management] に関するページをご覧ください。 クエリの待機は、オブジェクト ロックなど、他の理由によっても発生します。  クエリがリソースを待機している場合は、この記事の下にある [「リソースを待機しているクエリの調査」][Investigating queries waiting for resources]を参照してください。

sys.dm_pdw_exec_requests テーブルのクエリの検索を単純化するには、[LABEL][LABEL] を使用して、sys.dm_pdw_exec_requests ビューで検索できるクエリにコメントを割り当てます。

```sql
-- Query with Label
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query')
;
```

### <a name="step-2-investigate-the-query-plan"></a>手順 2: クエリ プランを調査する
要求 ID を使用して、[sys.dm_pdw_request_steps][sys.dm_pdw_request_steps] からクエリの分散 SQL (DSQL) プランを取得します。

```sql
-- Find the distributed query plan steps for a specific query.
-- Replace request_id with value from Step 1.

SELECT * FROM sys.dm_pdw_request_steps
WHERE request_id = 'QID####'
ORDER BY step_index;
```

DSQL プランに予想よりも時間がかかる場合は、多くの DSQL 手順が存在する複雑なプランであったり、1 つの手順に長い時間を要することが原因であったりする可能性があります。  プランにいくつかの移動操作を含む多くの手順が存在する場合は、テーブルのディストリビューションを最適化して、データの移動を削減することを検討してください。 [テーブルのディストリビューション][Table distribution] に関する記事では、クエリを解決するためにデータを移動する必要がある理由と、データの移動を最小限に抑えるためのいくつかの分散方法について説明します。

1 つの手順に関する詳細を調査するには、実行時間の長いクエリ手順の *operation_type* 列を確認し、**手順インデックス**を書き留めます。

* 手順 3a の **SQL 操作**(OnOperation、RemoteOperation、ReturnOperation) に進みます。
* 手順 3b の **Data Movement 操作**(ShuffleMoveOperation、BroadcastMoveOperation、TrimMoveOperation、PartitionMoveOperation、MoveOperation、CopyOperation) に進みます。

### <a name="step-3a-investigate-sql-on-the-distributed-databases"></a>手順 3a: 分散データベースでの SQL を調査する
要求 ID と手順インデックスを使用して、[sys.dm_pdw_sql_requests][sys.dm_pdw_sql_requests] から詳細情報を取得します。これには、すべての分散データベースに対するクエリ手順の実行情報が含まれます。

```sql
-- Find the distribution run times for a SQL step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_sql_requests
WHERE request_id = 'QID####' AND step_index = 2;
```

クエリ手順が実行中の場合は、[DBCC PDW_SHOWEXECUTIONPLAN][DBCC PDW_SHOWEXECUTIONPLAN] を使用して、特定のディストリビューションで実行中の手順に対する SQL Server プラン キャッシュから、SQL Server 推定プランを取得できます。

```sql
-- Find the SQL Server execution plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(1, 78);
```

### <a name="step-3b-investigate-data-movement-on-the-distributed-databases"></a>手順 3b: 分散データベース上のデータ移動を調査する
要求 ID と手順インデックスを利用して、[sys.dm_pdw_dms_workers][sys.dm_pdw_dms_workers] から、各ディストリビューションで実行されているデータ移動手順に関する情報を取得します。

```sql
-- Find the information about all the workers completing a Data Movement Step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_dms_workers
WHERE request_id = 'QID####' AND step_index = 2;
```

* *total_elapsed_time* 列で、特定の配布で他の配布よりデータ移動に大幅に時間がかかっていないか確認します。
* 実行時間の長い配布に対して、*rows_processed* 列で、その配布から移動された行の数が他の配布より大幅に大きいか確認します。 大きい場合は、基になるデータの傾斜を示している可能性があります。

クエリが実行中の場合は、[DBCC PDW_SHOWEXECUTIONPLAN][DBCC PDW_SHOWEXECUTIONPLAN] を使用して、特定のディストリビューション内で現在実行中の SQL 手順に対する SQL Server プラン キャッシュから、SQL Server 推定プランを取得できます。

```sql
-- Find the SQL Server estimated plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(55, 238);
```

<a name="waiting"></a>

## <a name="monitor-waiting-queries"></a>待機クエリを監視する
クエリがリソースを待っていることが原因で進行状況に変化がないことがわかった場合は、次のクエリを使用すると、待機対象のすべてのリソースが表示されます。

```sql
-- Find queries 
-- Replace request_id with value from Step 1.

SELECT waits.session_id,
      waits.request_id,  
      requests.command,
      requests.status,
      requests.start_time,  
      waits.type,
      waits.state,
      waits.object_type,
      waits.object_name
FROM   sys.dm_pdw_waits waits
   JOIN  sys.dm_pdw_exec_requests requests
   ON waits.request_id=requests.request_id
WHERE waits.request_id = 'QID####'
ORDER BY waits.object_name, waits.object_type, waits.state;
```

クエリが別のクエリからのリソースを積極的に待っている場合、状態は **AcquireResources**になります。  クエリに必要なリソースがすべて揃っている場合、状態は **Granted**になります。

## <a name="monitor-tempdb"></a>tempdb を監視する
tempdb の高使用率が、パフォーマンスの低下とメモリ不足の問題の根本原因になることがあります。 まず、データ スキューや低品質の行グループが存在するかどうかを確認し、適切なアクションを実行してください。 クエリの実行中に tempdb がその制限に達していることがわかった場合は、データ ウェアハウスのスケーリングを検討してください。 以下に、各ノードのクエリごとの tempdb 使用率を識別する方法について説明します。 

以下のビューを作成し、sys.dm_pdw_sql_requests に適切なノード ID を関連付けます。 これにより、他のパススルー DMV を利用して、それらのテーブルを sys.dm_pdw_sql_requests と結合できます。

```sql
-- sys.dm_pdw_sql_requests with the correct node id
CREATE VIEW sql_requests AS
(SELECT
       sr.request_id,
       sr.step_index,
       (CASE 
              WHEN (sr.distribution_id = -1 ) THEN 
              (SELECT pdw_node_id FROM sys.dm_pdw_nodes WHERE type = 'CONTROL') 
              ELSE d.pdw_node_id END) AS pdw_node_id,
       sr.distribution_id,
       sr.status,
       sr.error_id,
       sr.start_time,
       sr.end_time,
       sr.total_elapsed_time,
       sr.row_count,
       sr.spid,
       sr.command
FROM sys.pdw_distributions AS d
RIGHT JOIN sys.dm_pdw_sql_requests AS sr ON d.distribution_id = sr.distribution_id)
```
tempdb を監視するには、次のクエリを実行します。

```sql
-- Monitor tempdb
SELECT
    sr.request_id,
    ssu.session_id,
    ssu.pdw_node_id,
    sr.command,
    sr.total_elapsed_time,
    es.login_name AS 'LoginName',
    DB_NAME(ssu.database_id) AS 'DatabaseName',
    (es.memory_usage * 8) AS 'MemoryUsage (in KB)',
    (ssu.user_objects_alloc_page_count * 8) AS 'Space Allocated For User Objects (in KB)',
    (ssu.user_objects_dealloc_page_count * 8) AS 'Space Deallocated For User Objects (in KB)',
    (ssu.internal_objects_alloc_page_count * 8) AS 'Space Allocated For Internal Objects (in KB)',
    (ssu.internal_objects_dealloc_page_count * 8) AS 'Space Deallocated For Internal Objects (in KB)',
    CASE es.is_user_process
    WHEN 1 THEN 'User Session'
    WHEN 0 THEN 'System Session'
    END AS 'SessionType',
    es.row_count AS 'RowCount'
FROM sys.dm_pdw_nodes_db_session_space_usage AS ssu
    INNER JOIN sys.dm_pdw_nodes_exec_sessions AS es ON ssu.session_id = es.session_id AND ssu.pdw_node_id = es.pdw_node_id
    INNER JOIN sys.dm_pdw_nodes_exec_connections AS er ON ssu.session_id = er.session_id AND ssu.pdw_node_id = er.pdw_node_id
    INNER JOIN sql_requests AS sr ON ssu.session_id = sr.spid AND ssu.pdw_node_id = sr.pdw_node_id
WHERE DB_NAME(ssu.database_id) = 'tempdb'
    AND es.session_id <> @@SPID
    AND es.login_name <> 'sa' 
ORDER BY sr.request_id;
```
## <a name="monitor-memory"></a>メモリを監視する

メモリが、パフォーマンスの低下とメモリ不足の問題の根本原因になることがあります。 まず、データ スキューや低品質の行グループが存在するかどうかを確認し、適切なアクションを実行してください。 クエリの実行中に SQL Server のメモリ使用率が制限に達していることがわかった場合は、データ ウェアハウスのスケーリングを検討してください。

次のクエリでは、ノードあたりの SQL Server のメモリ使用率とメモリ負荷が返されます。   
```sql
-- Memory consumption
SELECT
  pc1.cntr_value as Curr_Mem_KB, 
  pc1.cntr_value/1024.0 as Curr_Mem_MB,
  (pc1.cntr_value/1048576.0) as Curr_Mem_GB,
  pc2.cntr_value as Max_Mem_KB,
  pc2.cntr_value/1024.0 as Max_Mem_MB,
  (pc2.cntr_value/1048576.0) as Max_Mem_GB,
  pc1.cntr_value * 100.0/pc2.cntr_value AS Memory_Utilization_Percentage,
  pc1.pdw_node_id
FROM
-- pc1: current memory
sys.dm_pdw_nodes_os_performance_counters AS pc1
-- pc2: total memory allowed for this SQL instance
JOIN sys.dm_pdw_nodes_os_performance_counters AS pc2 
ON pc1.object_name = pc2.object_name AND pc1.pdw_node_id = pc2.pdw_node_id
WHERE
pc1.counter_name = 'Total Server Memory (KB)'
AND pc2.counter_name = 'Target Server Memory (KB)'
```
## <a name="monitor-transaction-log-size"></a>トランザクション ログのサイズを監視する
次のクエリでは、配布ごとのトランザクション ログのサイズが返されます。 データ スキューや低品質の行グループが存在するかどうかを確認し、適切なアクションを実行してください。 ログ ファイルのいずれかが 160 GB に達している場合は、インスタンスのスケールアップまたはトランザクション サイズの制限を検討する必要があります。 
```sql
-- Transaction log size
SELECT
  instance_name as distribution_db,
  cntr_value*1.0/1048576 as log_file_size_used_GB,
  pdw_node_id 
FROM sys.dm_pdw_nodes_os_performance_counters 
WHERE 
instance_name like 'Distribution_%' 
AND counter_name = 'Log File(s) Used Size (KB)'
AND counter_name = 'Target Server Memory (KB)'
```
## <a name="monitor-transaction-log-rollback"></a>トランザクション ログのロールバックを監視する
クエリが失敗するか、続行するのに長時間かかる場合は、トランザクションのロールバックを確認および監視できます。
```sql
-- Monitor rollback
SELECT 
    SUM(CASE WHEN t.database_transaction_next_undo_lsn IS NOT NULL THEN 1 ELSE 0 END),
    t.pdw_node_id,
    nod.[type]
FROM sys.dm_pdw_nodes_tran_database_transactions t
JOIN sys.dm_pdw_nodes nod ON t.pdw_node_id = nod.pdw_node_id
GROUP BY t.pdw_node_id, nod.[type]
```

## <a name="next-steps"></a>次のステップ
DMV の詳細については、「[システム ビュー][System views]」を参照してください。
ベスト プラクティスの詳細については、「[SQL Data Warehouse のベスト プラクティス][SQL Data Warehouse best practices]」を参照してください。

<!--Image references-->

<!--Article references-->
[Manage overview]: ./sql-data-warehouse-overview-manage.md
[SQL Data Warehouse best practices]: ./sql-data-warehouse-best-practices.md
[System views]: ./sql-data-warehouse-reference-tsql-system-views.md
[Table distribution]: ./sql-data-warehouse-tables-distribute.md
[Concurrency and workload management]: ./sql-data-warehouse-develop-concurrency.md
[Investigating queries waiting for resources]: ./sql-data-warehouse-manage-monitor.md#waiting

<!--MSDN references-->
[sys.dm_pdw_dms_workers]: http://msdn.microsoft.com/library/mt203878.aspx
[sys.dm_pdw_exec_requests]: http://msdn.microsoft.com/library/mt203887.aspx
[sys.dm_pdw_exec_sessions]: http://msdn.microsoft.com/library/mt203883.aspx
[sys.dm_pdw_request_steps]: http://msdn.microsoft.com/library/mt203913.aspx
[sys.dm_pdw_sql_requests]: http://msdn.microsoft.com/library/mt203889.aspx
[DBCC PDW_SHOWEXECUTIONPLAN]: http://msdn.microsoft.com/library/mt204017.aspx
[DBCC PDW_SHOWSPACEUSED]: http://msdn.microsoft.com/library/mt204028.aspx
[LABEL]: https://msdn.microsoft.com/library/ms190322.aspx
