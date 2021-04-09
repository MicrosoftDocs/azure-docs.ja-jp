---
title: DMV を使用してパフォーマンスを監視する
titleSuffix: Azure SQL Database & SQL Managed Instance
description: 動的管理ビューを使用して Microsoft Azure SQL Database および Azure SQL Managed Instance を監視することで、一般的なパフォーマンスの問題を検出および診断する方法について説明します。
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: how-to
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.date: 03/15/2021
ms.openlocfilehash: 5c0de2c1589bfa495ab6ad287b998c403041674c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104592151"
---
# <a name="monitoring-microsoft-azure-sql-database-and-azure-sql-managed-instance-performance-using-dynamic-management-views"></a>動的管理ビューを使用した Microsoft Azure SQL Database および Azure SQL Managed Instance のパフォーマンスの監視
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Microsoft Azure SQL Database および Azure SQL Managed Instance では、クエリのブロック、クエリの長時間実行、リソースのボトルネック、不適切なクエリ プランなどが原因で発生するパフォーマンスの問題を、動的管理ビューの一部を使用して診断できます。 この記事では、動的管理ビューを使用して一般的なパフォーマンスの問題を検出する方法について説明します。

Microsoft Azure SQL Database および Azure SQL Managed Instance では、動的管理ビューの 3 つのカテゴリが部分的にサポートされています。

- データベース関連の動的管理ビュー。
- 実行関連の動的管理ビュー。
- トランザクション関連の動的管理ビュー。

動的管理ビューの詳細については、[動的管理ビューおよび関数 (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) に関する記事をご覧ください。

## <a name="monitor-with-sql-insights"></a>SQL insights での監視

[Azure Monitor SQL insights](../../azure-monitor/insights/sql-insights-overview.md) は、Azure SQL VM 内の Azure SQL マネージド インスタンス、Azure SQL データベース、SQL Server インスタンスを監視するためのツールです。 このサービスでは、リモート エージェントを使用して動的管理ビュー (DMV) からデータをキャプチャして、Azure Log Analytics にルーティングし、ここでそのデータを監視および分析することができます。 このデータは、[Azure Monitor](../../azure-monitor/overview.md) から提供されたビューで表示することも、ログ データに直接アクセスしてクエリを実行したり、傾向を分析したりすることもできます。 Azure Monitor SQL 分析情報の使用を開始するには、「[SQL の分析情報を有効にする](../../azure-monitor/insights/sql-insights-enable.md)」を参照してください。

## <a name="permissions"></a>アクセス許可

Azure SQL Database で、動的管理ビューに対してクエリを実行するには、**VIEW DATABASE STATE** アクセス許可が必要です。 **VIEW DATABASE STATE** アクセス許可は、現在のデータベース内のすべてのオブジェクトに関する情報を返します。
**VIEW DATABASE STATE** アクセス許可を特定のデータベース ユーザーに付与するには、次のクエリを実行します。

```sql
GRANT VIEW DATABASE STATE TO database_user;
```

Azure SQL Managed Instance で、動的管理ビューに対してクエリを実行するには、**VIEW SERVER STATE** アクセス許可が必要です。 詳細については、「[システム動的管理ビュー](/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views#required-permissions)」を参照してください。

SQL Server のインスタンスおよび Azure SQL Managed Instance では、動的管理ビューにサーバーの状態についての情報が返されます。 Azure SQL Database では、現在の論理データベースに関する情報のみが返されます。

この記事には、次の種類のクエリ パフォーマンスの問題を検出するために、SQL Server Management Studio または Azure Data Studio を使用して実行できる DMV クエリのコレクションが含まれています。

- [過剰な CPU 消費に関連するクエリを特定する](#identify-cpu-performance-issues)
- [IO ボトルネックに関連する PAGELATCH_* と WRITE_LOG の待機](#identify-io-performance-issues)
- [TempDB の競合によって発生する PAGELATCH_*](#identify-tempdb-performance-issues)
- [メモリ許可待機の問題によって発生する RESOURCE_SEMAHPORE 待機](#identify-memory-grant-wait-performance-issues)
- [データベースとオブジェクトのサイズの識別](#calculating-database-and-objects-sizes)
- [アクティブ セッションに関する情報の取得](#monitoring-connections)
- [システム全体およびデータベース リソースの使用状況に関する情報の取得](#monitor-resource-use)
- [クエリ パフォーマンス情報の取得](#monitoring-query-performance)

## <a name="identify-cpu-performance-issues"></a>CPU パフォーマンスに関する問題の特定

CPU 消費率が長時間 80% を超えている場合は、次のトラブルシューティングの手順を検討してください。

### <a name="the-cpu-issue-is-occurring-now"></a>CPU に関する問題が現在発生している

現時点で問題が発生している場合、2 つのシナリオが考えられます。

#### <a name="many-individual-queries-that-cumulatively-consume-high-cpu"></a>個別のクエリが多数あり、大量の CPU が累積的に消費されている

上位のクエリ ハッシュを特定するには、次のクエリを使用します。

```sql
PRINT '-- top 10 Active CPU Consuming Queries (aggregated)--';
SELECT TOP 10 GETDATE() runtime, *
FROM (SELECT query_stats.query_hash, SUM(query_stats.cpu_time) 'Total_Request_Cpu_Time_Ms', SUM(logical_reads) 'Total_Request_Logical_Reads', MIN(start_time) 'Earliest_Request_start_Time', COUNT(*) 'Number_Of_Requests', SUBSTRING(REPLACE(REPLACE(MIN(query_stats.statement_text), CHAR(10), ' '), CHAR(13), ' '), 1, 256) AS "Statement_Text"
    FROM (SELECT req.*, SUBSTRING(ST.text, (req.statement_start_offset / 2)+1, ((CASE statement_end_offset WHEN -1 THEN DATALENGTH(ST.text)ELSE req.statement_end_offset END-req.statement_start_offset)/ 2)+1) AS statement_text
          FROM sys.dm_exec_requests AS req
                CROSS APPLY sys.dm_exec_sql_text(req.sql_handle) AS ST ) AS query_stats
    GROUP BY query_hash) AS t
ORDER BY Total_Request_Cpu_Time_Ms DESC;
```

#### <a name="long-running-queries-that-consume-cpu-are-still-running"></a>実行時間の長いクエリが CPU を消費しており、実行中のままである

これらのクエリを特定するには、次のクエリを使用します。

```sql
PRINT '--top 10 Active CPU Consuming Queries by sessions--';
SELECT TOP 10 req.session_id, req.start_time, cpu_time 'cpu_time_ms', OBJECT_NAME(ST.objectid, ST.dbid) 'ObjectName', SUBSTRING(REPLACE(REPLACE(SUBSTRING(ST.text, (req.statement_start_offset / 2)+1, ((CASE statement_end_offset WHEN -1 THEN DATALENGTH(ST.text)ELSE req.statement_end_offset END-req.statement_start_offset)/ 2)+1), CHAR(10), ' '), CHAR(13), ' '), 1, 512) AS statement_text
FROM sys.dm_exec_requests AS req
    CROSS APPLY sys.dm_exec_sql_text(req.sql_handle) AS ST
ORDER BY cpu_time DESC;
GO
```

### <a name="the-cpu-issue-occurred-in-the-past"></a>CPU に関する問題が過去に発生した

過去に問題が発生していて、根本原因分析を行いたい場合は、[クエリ ストア](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)を使用します。 データベースにアクセスできるユーザーは、T-SQL を使用して、クエリ ストア データにクエリを実行できます。 クエリ ストアの既定の構成では、1 時間の粒度が使用されます。 大量の CPU を消費するクエリのアクティビティを確認するには、次のクエリを使用します。 このクエリは、CPU の消費が上位 15 のクエリを返します。 必ず `rsi.start_time >= DATEADD(hour, -2, GETUTCDATE()` を変更してください。

```sql
-- Top 15 CPU consuming queries by query hash
-- note that a query  hash can have many query id if not parameterized or not parameterized properly
-- it grabs a sample query text by min
WITH AggregatedCPU AS (SELECT q.query_hash, SUM(count_executions * avg_cpu_time / 1000.0) AS total_cpu_millisec, SUM(count_executions * avg_cpu_time / 1000.0)/ SUM(count_executions) AS avg_cpu_millisec, MAX(rs.max_cpu_time / 1000.00) AS max_cpu_millisec, MAX(max_logical_io_reads) max_logical_reads, COUNT(DISTINCT p.plan_id) AS number_of_distinct_plans, COUNT(DISTINCT p.query_id) AS number_of_distinct_query_ids, SUM(CASE WHEN rs.execution_type_desc='Aborted' THEN count_executions ELSE 0 END) AS Aborted_Execution_Count, SUM(CASE WHEN rs.execution_type_desc='Regular' THEN count_executions ELSE 0 END) AS Regular_Execution_Count, SUM(CASE WHEN rs.execution_type_desc='Exception' THEN count_executions ELSE 0 END) AS Exception_Execution_Count, SUM(count_executions) AS total_executions, MIN(qt.query_sql_text) AS sampled_query_text
                       FROM sys.query_store_query_text AS qt
                            JOIN sys.query_store_query AS q ON qt.query_text_id=q.query_text_id
                            JOIN sys.query_store_plan AS p ON q.query_id=p.query_id
                            JOIN sys.query_store_runtime_stats AS rs ON rs.plan_id=p.plan_id
                            JOIN sys.query_store_runtime_stats_interval AS rsi ON rsi.runtime_stats_interval_id=rs.runtime_stats_interval_id
                       WHERE rs.execution_type_desc IN ('Regular', 'Aborted', 'Exception')AND rsi.start_time>=DATEADD(HOUR, -2, GETUTCDATE())
                       GROUP BY q.query_hash), OrderedCPU AS (SELECT query_hash, total_cpu_millisec, avg_cpu_millisec, max_cpu_millisec, max_logical_reads, number_of_distinct_plans, number_of_distinct_query_ids, total_executions, Aborted_Execution_Count, Regular_Execution_Count, Exception_Execution_Count, sampled_query_text, ROW_NUMBER() OVER (ORDER BY total_cpu_millisec DESC, query_hash ASC) AS RN
                                                              FROM AggregatedCPU)
SELECT OD.query_hash, OD.total_cpu_millisec, OD.avg_cpu_millisec, OD.max_cpu_millisec, OD.max_logical_reads, OD.number_of_distinct_plans, OD.number_of_distinct_query_ids, OD.total_executions, OD.Aborted_Execution_Count, OD.Regular_Execution_Count, OD.Exception_Execution_Count, OD.sampled_query_text, OD.RN
FROM OrderedCPU AS OD
WHERE OD.RN<=15
ORDER BY total_cpu_millisec DESC;
```

問題となるクエリを特定したら、今度はそれらのクエリを調整して、CPU 使用率を抑えます。  クエリを調整する時間がない場合は、問題を回避するためにデータベースの SLO をアップグレードすることもできます。

## <a name="identify-io-performance-issues"></a>IO パフォーマンスに関する問題の特定

IO パフォーマンスに関する問題を特定する場合、IO の問題に関連している待機の種類のうち、上位のものは以下になります。

- `PAGEIOLATCH_*`

  データ ファイル IO に関する問題の場合 (`PAGEIOLATCH_SH`、`PAGEIOLATCH_EX`、`PAGEIOLATCH_UP` など)。  待機の種類の名前に **IO** が含まれている場合は、それにより IO に関する問題が示されます。 ページ ラッチ待機の名前に **IO** が含まれていない場合は、それにより別の種類の問題 (例: tempdb の競合) が示されます。

- `WRITE_LOG`

  トランザクション ログ IO に関する問題の場合。

### <a name="if-the-io-issue-is-occurring-right-now"></a>IO に関する問題が現時点で発生している場合

[sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) または [sys.dm_os_waiting_tasks](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) を使用して、`wait_type` と `wait_time` を確認します。

#### <a name="identify-data-and-log-io-usage"></a>データ IO およびログ IO の使用率の特定

データ IO およびログ IO の使用率を特定するには、次のクエリを使用します。 データまたはログ IO が 80% を超えている状態は、ユーザーがその SQL Database サービス レベルで利用可能な IO を利用したことを意味します。

```sql
SELECT end_time, avg_data_io_percent, avg_log_write_percent
FROM sys.dm_db_resource_stats
ORDER BY end_time DESC;
```

IO の上限に達した場合は 2 つのオプションがあります。

- オプション 1: コンピューティング サイズまたはサービス レベルをアップグレードする
- オプション 2:最も多くの IO を消費しているクエリを特定して調整する

#### <a name="view-buffer-related-io-using-the-query-store"></a>クエリ ストアを使用したバッファー関連 IO の表示

オプション 2 では、バッファー関連 IO についてクエリ ストアに対して次のクエリを実行して、過去 2 時間の追跡されたアクティビティを表示できます。

```sql
-- top queries that waited on buffer
-- note these are finished queries
WITH Aggregated AS (SELECT q.query_hash, SUM(total_query_wait_time_ms) total_wait_time_ms, SUM(total_query_wait_time_ms / avg_query_wait_time_ms) AS total_executions, MIN(qt.query_sql_text) AS sampled_query_text, MIN(wait_category_desc) AS wait_category_desc
                    FROM sys.query_store_query_text AS qt
                         JOIN sys.query_store_query AS q ON qt.query_text_id=q.query_text_id
                         JOIN sys.query_store_plan AS p ON q.query_id=p.query_id
                         JOIN sys.query_store_wait_stats AS waits ON waits.plan_id=p.plan_id
                         JOIN sys.query_store_runtime_stats_interval AS rsi ON rsi.runtime_stats_interval_id=waits.runtime_stats_interval_id
                    WHERE wait_category_desc='Buffer IO' AND rsi.start_time>=DATEADD(HOUR, -2, GETUTCDATE())
                    GROUP BY q.query_hash), Ordered AS (SELECT query_hash, total_executions, total_wait_time_ms, sampled_query_text, wait_category_desc, ROW_NUMBER() OVER (ORDER BY total_wait_time_ms DESC, query_hash ASC) AS RN
                                                        FROM Aggregated)
SELECT OD.query_hash, OD.total_executions, OD.total_wait_time_ms, OD.sampled_query_text, OD.wait_category_desc, OD.RN
FROM Ordered AS OD
WHERE OD.RN<=15
ORDER BY total_wait_time_ms DESC;
GO
```

#### <a name="view-total-log-io-for-writelog-waits"></a>WRITELOG 待機に関する合計ログ IO の表示

待機の種類が `WRITELOG` の場合、次のクエリを使用して、ステートメントごとに合計ログ IO を表示します。

```sql
-- Top transaction log consumers
-- Adjust the time window by changing
-- rsi.start_time >= DATEADD(hour, -2, GETUTCDATE())
WITH AggregatedLogUsed
AS (SELECT q.query_hash,
           SUM(count_executions * avg_cpu_time / 1000.0) AS total_cpu_millisec,
           SUM(count_executions * avg_cpu_time / 1000.0) / SUM(count_executions) AS avg_cpu_millisec,
           SUM(count_executions * avg_log_bytes_used) AS total_log_bytes_used,
           MAX(rs.max_cpu_time / 1000.00) AS max_cpu_millisec,
           MAX(max_logical_io_reads) max_logical_reads,
           COUNT(DISTINCT p.plan_id) AS number_of_distinct_plans,
           COUNT(DISTINCT p.query_id) AS number_of_distinct_query_ids,
           SUM(   CASE
                      WHEN rs.execution_type_desc = 'Aborted' THEN
                          count_executions
                      ELSE
                          0
                  END
              ) AS Aborted_Execution_Count,
           SUM(   CASE
                      WHEN rs.execution_type_desc = 'Regular' THEN
                          count_executions
                      ELSE
                          0
                  END
              ) AS Regular_Execution_Count,
           SUM(   CASE
                      WHEN rs.execution_type_desc = 'Exception' THEN
                          count_executions
                      ELSE
                          0
                  END
              ) AS Exception_Execution_Count,
           SUM(count_executions) AS total_executions,
           MIN(qt.query_sql_text) AS sampled_query_text
    FROM sys.query_store_query_text AS qt
        JOIN sys.query_store_query AS q
            ON qt.query_text_id = q.query_text_id
        JOIN sys.query_store_plan AS p
            ON q.query_id = p.query_id
        JOIN sys.query_store_runtime_stats AS rs
            ON rs.plan_id = p.plan_id
        JOIN sys.query_store_runtime_stats_interval AS rsi
            ON rsi.runtime_stats_interval_id = rs.runtime_stats_interval_id
    WHERE rs.execution_type_desc IN ( 'Regular', 'Aborted', 'Exception' )
          AND rsi.start_time >= DATEADD(HOUR, -2, GETUTCDATE())
    GROUP BY q.query_hash),
     OrderedLogUsed
AS (SELECT query_hash,
           total_log_bytes_used,
           number_of_distinct_plans,
           number_of_distinct_query_ids,
           total_executions,
           Aborted_Execution_Count,
           Regular_Execution_Count,
           Exception_Execution_Count,
           sampled_query_text,
           ROW_NUMBER() OVER (ORDER BY total_log_bytes_used DESC, query_hash ASC) AS RN
    FROM AggregatedLogUsed)
SELECT OD.total_log_bytes_used,
       OD.number_of_distinct_plans,
       OD.number_of_distinct_query_ids,
       OD.total_executions,
       OD.Aborted_Execution_Count,
       OD.Regular_Execution_Count,
       OD.Exception_Execution_Count,
       OD.sampled_query_text,
       OD.RN
FROM OrderedLogUsed AS OD
WHERE OD.RN <= 15
ORDER BY total_log_bytes_used DESC;
GO
```

## <a name="identify-tempdb-performance-issues"></a>`tempdb` パフォーマンスに関する問題の特定

IO パフォーマンスに関する問題を特定する場合、`tempdb` の問題に関連している待機の種類の上位は `PAGELATCH_*` です (`PAGEIOLATCH_*` ではありません)。 ただし、`PAGELATCH_*` 待機は必ずしも `tempdb` 競合があることを意味しません。  この待機は、同一のデータ ページを対象とする同時要求が原因で、ユーザーオブジェクト データ ページ競合が発生していることを意味する場合もあります。 `tempdb` 競合をさらに確認するには、[sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) を使用して、wait_resource 値が `2:x:y` で始まることを確認します。ここで、2 は `tempdb` (データベース ID)、`x` はファイル ID、`y` はページ ID です。  

tempdb 競合では、`tempdb` が使用されるアプリケーション コードを減らすか、書き直すのが一般的な方法です。  `tempdb` の一般的な使用領域には以下があります。

- 一時テーブル
- テーブル変数
- テーブル値パラメーター
- バージョン ストアの使用 (実行時間の長いトランザクションに関連)
- 並べ替え、ハッシュ結合、スプールが使用されるクエリ プランがあるクエリ

### <a name="top-queries-that-use-table-variables-and-temporary-tables"></a>テーブル変数と一時テーブルが使用される上位のクエリ

テーブル変数と一時テーブルが使用される上位のクエリを特定するには、次のクエリを使用します。

```sql
SELECT plan_handle, execution_count, query_plan
INTO #tmpPlan
FROM sys.dm_exec_query_stats
     CROSS APPLY sys.dm_exec_query_plan(plan_handle);
GO

WITH XMLNAMESPACES('http://schemas.microsoft.com/sqlserver/2004/07/showplan' AS sp)
SELECT plan_handle, stmt.stmt_details.value('@Database', 'varchar(max)') 'Database', stmt.stmt_details.value('@Schema', 'varchar(max)') 'Schema', stmt.stmt_details.value('@Table', 'varchar(max)') 'table'
INTO #tmp2
FROM(SELECT CAST(query_plan AS XML) sqlplan, plan_handle FROM #tmpPlan) AS p
    CROSS APPLY sqlplan.nodes('//sp:Object') AS stmt(stmt_details);
GO

SELECT t.plan_handle, [Database], [Schema], [table], execution_count
FROM(SELECT DISTINCT plan_handle, [Database], [Schema], [table]
     FROM #tmp2
     WHERE [table] LIKE '%@%' OR [table] LIKE '%#%') AS t
    JOIN #tmpPlan AS t2 ON t.plan_handle=t2.plan_handle;
```

### <a name="identify-long-running-transactions"></a>実行時間の長いトランザクションの特定

実行時間の長いトランザクションを特定するには、次のクエリを使用します。 実行時間の長いトランザクションは、バージョン ストアのクリーンアップを妨げます。

```sql
SELECT DB_NAME(dtr.database_id) 'database_name',
       sess.session_id,
       atr.name AS 'tran_name',
       atr.transaction_id,
       transaction_type,
       transaction_begin_time,
       database_transaction_begin_time transaction_state,
       is_user_transaction,
       sess.open_transaction_count,
       LTRIM(RTRIM(REPLACE(
                              REPLACE(
                                         SUBSTRING(
                                                      SUBSTRING(
                                                                   txt.text,
                                                                   (req.statement_start_offset / 2) + 1,
                                                                   ((CASE req.statement_end_offset
                                                                         WHEN -1 THEN
                                                                             DATALENGTH(txt.text)
                                                                         ELSE
                                                                             req.statement_end_offset
                                                                     END - req.statement_start_offset
                                                                    ) / 2
                                                                   ) + 1
                                                               ),
                                                      1,
                                                      1000
                                                  ),
                                         CHAR(10),
                                         ' '
                                     ),
                              CHAR(13),
                              ' '
                          )
                  )
            ) Running_stmt_text,
       recenttxt.text 'MostRecentSQLText'
FROM sys.dm_tran_active_transactions AS atr
    INNER JOIN sys.dm_tran_database_transactions AS dtr
        ON dtr.transaction_id = atr.transaction_id
    LEFT JOIN sys.dm_tran_session_transactions AS sess
        ON sess.transaction_id = atr.transaction_id
    LEFT JOIN sys.dm_exec_requests AS req
        ON req.session_id = sess.session_id
           AND req.transaction_id = sess.transaction_id
    LEFT JOIN sys.dm_exec_connections AS conn
        ON sess.session_id = conn.session_id
    OUTER APPLY sys.dm_exec_sql_text(req.sql_handle) AS txt
    OUTER APPLY sys.dm_exec_sql_text(conn.most_recent_sql_handle) AS recenttxt
WHERE atr.transaction_type != 2
      AND sess.session_id != @@spid
ORDER BY start_time ASC;
```

## <a name="identify-memory-grant-wait-performance-issues"></a>メモリ許可待機パフォーマンスに関する問題の特定

上位の待機の種類が `RESOURCE_SEMAHPORE` であり、なおかつ高 CPU 使用率の問題がない場合は、メモリ許可待機に関する問題が発生している可能性があります。

### <a name="determine-if-a-resource_semahpore-wait-is-a-top-wait"></a>`RESOURCE_SEMAHPORE` 待機が上位の待機であるかどうかの確認

`RESOURCE_SEMAHPORE` 待機が上位の待機であるかどうかを確認するには、次のクエリを使用します

```sql
SELECT wait_type,
       SUM(wait_time) AS total_wait_time_ms
FROM sys.dm_exec_requests AS req
    JOIN sys.dm_exec_sessions AS sess
        ON req.session_id = sess.session_id
WHERE is_user_process = 1
GROUP BY wait_type
ORDER BY SUM(wait_time) DESC;
```

### <a name="identify-high-memory-consuming-statements"></a>大量のメモリを消費するステートメントの特定

大量のメモリを消費するステートメントを特定するには、次のクエリを使用します。

```sql
SELECT IDENTITY(INT, 1, 1) rowId,
    CAST(query_plan AS XML) query_plan,
    p.query_id
INTO #tmp
FROM sys.query_store_plan AS p
    JOIN sys.query_store_runtime_stats AS r
        ON p.plan_id = r.plan_id
    JOIN sys.query_store_runtime_stats_interval AS i
        ON r.runtime_stats_interval_id = i.runtime_stats_interval_id
WHERE start_time > '2018-10-11 14:00:00.0000000'
      AND end_time < '2018-10-17 20:00:00.0000000';
GO
;WITH cte
AS (SELECT query_id,
        query_plan,
        m.c.value('@SerialDesiredMemory', 'INT') AS SerialDesiredMemory
    FROM #tmp AS t
        CROSS APPLY t.query_plan.nodes('//*:MemoryGrantInfo[@SerialDesiredMemory[. > 0]]') AS m(c) )
SELECT TOP 50
    cte.query_id,
    t.query_sql_text,
    cte.query_plan,
    CAST(SerialDesiredMemory / 1024. AS DECIMAL(10, 2)) SerialDesiredMemory_MB
FROM cte
    JOIN sys.query_store_query AS q
        ON cte.query_id = q.query_id
    JOIN sys.query_store_query_text AS t
        ON q.query_text_id = t.query_text_id
ORDER BY SerialDesiredMemory DESC;
```

### <a name="identify-the-top-10-active-memory-grants"></a>上位 10 のアクティブなメモリ許可の特定

上位 10 のアクティブなメモリ許可を特定するには、次のクエリを実行します。

```sql
SELECT TOP 10
    CONVERT(VARCHAR(30), GETDATE(), 121) AS runtime,
       r.session_id,
       r.blocking_session_id,
       r.cpu_time,
       r.total_elapsed_time,
       r.reads,
       r.writes,
       r.logical_reads,
       r.row_count,
       wait_time,
       wait_type,
       r.command,
       OBJECT_NAME(txt.objectid, txt.dbid) 'Object_Name',
       LTRIM(RTRIM(REPLACE(
                              REPLACE(
                                         SUBSTRING(
                                                      SUBSTRING(
                                                                   text,
                                                                   (r.statement_start_offset / 2) + 1,
                                                                   ((CASE r.statement_end_offset
                                                                         WHEN -1 THEN
                                                                             DATALENGTH(text)
                                                                         ELSE
                                                                             r.statement_end_offset
                                                                     END - r.statement_start_offset
                                                                    ) / 2
                                                                   ) + 1
                                                               ),
                                                      1,
                                                      1000
                                                  ),
                                         CHAR(10),
                                         ' '
                                     ),
                              CHAR(13),
                              ' '
                          )
                  )
            ) stmt_text,
       mg.dop,                                               --Degree of parallelism
       mg.request_time,                                      --Date and time when this query requested the memory grant.
       mg.grant_time,                                        --NULL means memory has not been granted
       mg.requested_memory_kb / 1024.0 requested_memory_mb,  --Total requested amount of memory in megabytes
       mg.granted_memory_kb / 1024.0 AS granted_memory_mb,   --Total amount of memory actually granted in megabytes. NULL if not granted
       mg.required_memory_kb / 1024.0 AS required_memory_mb, --Minimum memory required to run this query in megabytes.
       max_used_memory_kb / 1024.0 AS max_used_memory_mb,
       mg.query_cost,                                        --Estimated query cost.
       mg.timeout_sec,                                       --Time-out in seconds before this query gives up the memory grant request.
       mg.resource_semaphore_id,                             --Non-unique ID of the resource semaphore on which this query is waiting.
       mg.wait_time_ms,                                      --Wait time in milliseconds. NULL if the memory is already granted.
       CASE mg.is_next_candidate --Is this process the next candidate for a memory grant
           WHEN 1 THEN
               'Yes'
           WHEN 0 THEN
               'No'
           ELSE
               'Memory has been granted'
       END AS 'Next Candidate for Memory Grant',
       qp.query_plan
FROM sys.dm_exec_requests AS r
    JOIN sys.dm_exec_query_memory_grants AS mg
        ON r.session_id = mg.session_id
           AND r.request_id = mg.request_id
    CROSS APPLY sys.dm_exec_sql_text(mg.sql_handle) AS txt
    CROSS APPLY sys.dm_exec_query_plan(r.plan_handle) AS qp
ORDER BY mg.granted_memory_kb DESC;
```

## <a name="calculating-database-and-objects-sizes"></a>データベース サイズとオブジェクト サイズの計算

次のクエリは、データベースのサイズ (MB 単位) を返します。

```sql
-- Calculates the size of the database.
SELECT SUM(CAST(FILEPROPERTY(name, 'SpaceUsed') AS bigint) * 8192.) / 1024 / 1024 AS DatabaseSizeInMB
FROM sys.database_files
WHERE type_desc = 'ROWS';
GO
```

次のクエリは、データベース内の個々のオブジェクトのサイズ (MB 単位) を返します。

```sql
-- Calculates the size of individual database objects.
SELECT sys.objects.name, SUM(reserved_page_count) * 8.0 / 1024
FROM sys.dm_db_partition_stats, sys.objects
WHERE sys.dm_db_partition_stats.object_id = sys.objects.object_id
GROUP BY sys.objects.name;
GO
```

## <a name="monitoring-connections"></a>接続の監視

[sys.dm_exec_connections](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-connections-transact-sql) ビューを使用して、特定のサーバーおよびマネージド インスタンスに対して確立されている接続についての情報と、各接続の詳細を取得できます。 また、[sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql) ビューは、すべてのアクティブなユーザー接続と内部タスクについての情報を取得する場合に役立ちます。

次のクエリは、現在の接続に関する情報を取得します。

```sql
SELECT
    c.session_id, c.net_transport, c.encrypt_option,
    c.auth_scheme, s.host_name, s.program_name,
    s.client_interface_name, s.login_name, s.nt_domain,
    s.nt_user_name, s.original_login_name, c.connect_time,
    s.login_time
FROM sys.dm_exec_connections AS c
JOIN sys.dm_exec_sessions AS s
    ON c.session_id = s.session_id
WHERE c.session_id = @@SPID;
```

> [!NOTE]
> **sys.dm_exec_requests** と **sys.dm_exec_sessions views** を実行するときに、データベースに対するアクセス許可 **VIEW DATABASE STATE** を持っていると、データベースで実行中のすべてのセッションが表示されます。アクセス許可を持っていない場合は、現在のセッションのみが表示されます。

## <a name="monitor-resource-use"></a>リソース使用量の監視

[SQL Database Query Performance Insight](query-performance-insight-use.md) を使用して、Azure SQL Database リソースの使用量を監視できます。 Azure SQL Database および Azure SQL Managed Instance の場合は、[クエリ ストア](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)を使用して監視できます。

また、次のビューを使用して使用量を監視することもできます。

- Azure SQL Database: [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)
- Azure SQL Managed Instance: [sys.server_resource_stats](/sql/relational-databases/system-catalog-views/sys-server-resource-stats-azure-sql-database)
- Azure SQL Database および Azure SQL Managed Instance の両方: [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)

### <a name="sysdm_db_resource_stats"></a>sys.dm_db_resource_stats

[sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) ビューは、すべてのデータベースで使用できます。 **sys.dm_db_resource_stats** ビューには、サービス レベルに関連した最近のリソース使用率データが表示されます。 CPU、データ IO、ログ書き込み、メモリの平均 (%) が 15 秒ごとに記録され、1 時間保持されます。

このビューにはリソース使用率が詳細に表示されるので、現状の分析やトラブルシューティングが目的の場合、最初に **sys.dm_db_resource_stats** を使用してください。 たとえば次のクエリは、現在のデータベースの過去 1 時間の平均リソース使用率と最大リソース使用率を表示します。

```sql
SELECT  
    AVG(avg_cpu_percent) AS 'Average CPU use in percent',
    MAX(avg_cpu_percent) AS 'Maximum CPU use in percent',
    AVG(avg_data_io_percent) AS 'Average data IO in percent',
    MAX(avg_data_io_percent) AS 'Maximum data IO in percent',
    AVG(avg_log_write_percent) AS 'Average log write use in percent',
    MAX(avg_log_write_percent) AS 'Maximum log write use in percent',
    AVG(avg_memory_usage_percent) AS 'Average memory use in percent',
    MAX(avg_memory_usage_percent) AS 'Maximum memory use in percent'
FROM sys.dm_db_resource_stats;  
```

その他のクエリについては、[sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) の例を参照してください。

### <a name="sysserver_resource_stats"></a>sys.server_resource_stats

[sys.server_resource_stats](/sql/relational-databases/system-catalog-views/sys-server-resource-stats-azure-sql-database) を使用すると、Azure SQL Managed Instance の CPU 使用率、IO、ストレージ データが返されます。 データは、5 分間隔で収集と集計が実行されます。 15 秒ごとの報告につき 1 行作成されます。 返されるデータには、CPU 使用率、ストレージ サイズ、IO 使用率、マネージド インスタンス SKU が含まれます。 履歴データは約 14 日間保持されます。

```sql
DECLARE @s datetime;  
DECLARE @e datetime;  
SET @s= DateAdd(d,-7,GetUTCDate());  
SET @e= GETUTCDATE();  
SELECT resource_name, AVG(avg_cpu_percent) AS Average_Compute_Utilization
FROM sys.server_resource_stats
WHERE start_time BETWEEN @s AND @e  
GROUP BY resource_name  
HAVING AVG(avg_cpu_percent) >= 80;
```

### <a name="sysresource_stats"></a>sys.resource_stats

**master** データベースの [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) ビューには、特定のサービス レベルとコンピューティング サイズでのデータベースのパフォーマンス監視に役立つ追加情報が含まれます。 データは 5 分ごとに集められ、約 14 日間保持されます。 このビューは、過去にデータベースでリソースがどのように使用されたかを長期にわたり分析する場合に役立ちます。

次のグラフは、Premium データベースの CPU リソース使用率を示しています (P2 コンピューティング サイズ、1 週間における毎時間の使用率)。 このグラフは月曜日から始まります。5 営業日が経過した後の週末ではアプリケーションの活動が大幅に減っていることがわかります。

![データベース リソースの使用](./media/monitoring-with-dmvs/sql_db_resource_utilization.png)

このデータから、このデータベースのピーク CPU 負荷は現在のところ、P2 コンピューティング サイズに対して 50% をわずかに超える CPU 利用率になっていることがわかります (火曜日の昼)。 アプリケーションのリソース プロファイルにおいて CPU が支配的要因である場合、P2 がワークロードに常に対処できる最適なコンピューティング サイズであると決定できます。 時間の経過と共にアプリケーションの規模が大きくなると予測される場合、アプリケーションがパフォーマンス レベルの制限に達しないように、リソース バッファーを余分に確保しておくことをお勧めします。 コンピューティング サイズを上げると、要求を効果的に処理するための十分な能力がないデータベースで発生するおそれのある、ユーザーに見えるエラーを回避できます。これは特に待機時間が重要な環境に当てはまります。 たとえば、データベース呼び出しの結果に基づいて Web ページを描画するアプリケーションをサポートしているデータベースなどです。

アプリケーションの種類が異なれば、同じグラフの解釈が異なる場合があります。 たとえば、あるアプリケーションで給与データを毎日処理し、同じグラフを生成する場合、P1 コンピューティング サイズでこの種の "一括ジョブ" モデルに問題なく対応できる可能性があります。 P2 コンピューティング サイズが 200 DTU であるのに対して、P1 コンピューティング サイズは 100 DTU です。 P1 コンピューティング サイズのパフォーマンスは、P2 コンピューティング サイズの半分です。 このため、P2 における 50% の CPU 使用率は、P1 における 100% の CPU 使用率に相当します。 アプリケーションにタイムアウトがない場合、当日に完了するのであれば、ジョブに 2 時間かかっても 2.5 時間かかっても問題ないものと思われます。 このカテゴリのアプリケーションではおそらく、P1 コンピューティング サイズを利用できます。 1 日の中にはリソース使用率が低くなる時間帯があるという事実を利用できます。このため、"大きなピーク" が 1 日のそのような時間帯のいずれかに波及することがあります。 ジョブを毎日定刻に完了できる限り、P1 コンピューティング サイズがこの種のアプリケーションに適している (コストが削減される) 場合があります。

データベース エンジンでは、各アクティブ データベースの使用済みリソース情報が、各サーバーの **master** データベースの **sys.resource_stats** ビューで公開されます。 テーブルのデータは 5 分おきに集計されます。 Basic、Standard、Premium のサービス レベルでは、データをテーブルに表示するのに 5 分を超える時間がかかる可能性があります。このため、このデータはほぼリアルタイムの分析よりも過去の分析に役に立ちます。 **sys.resource_stats** ビューに照会すると、データベースの最近の履歴が表示され、選択した予約で必要なときに望ましいパフォーマンスが発揮されたかどうかを検証できます。

> [!NOTE]
> Azure SQL Database で、次の例の **sys.resource_stats** にクエリを実行するには、**master** データベースに接続する必要があります。

次の例は、このビューのデータが表示されているところです。

```sql
SELECT TOP 10 *
FROM sys.resource_stats
WHERE database_name = 'resource1'
ORDER BY start_time DESC;
```

![The sys.resource_stats catalog view](./media/monitoring-with-dmvs/sys_resource_stats.png)

次の例では、**sys.resource_stats** カタログ ビューを使用して、データベースでのリソースの使用状況に関する情報を取得するさまざまな方法を示します。

1. データベース userdb1 の過去 1 週間のリソース使用率を確認するには、このクエリを実行します。

    ```sql
    SELECT *
    FROM sys.resource_stats
    WHERE database_name = 'userdb1' AND
        start_time > DATEADD(day, -7, GETDATE())
    ORDER BY start_time DESC;
    ```

2. ワークロードがコンピューティング サイズにどの程度適合しているかを評価するには、リソース メトリックの各側面を分析する必要があります。つまり、CPU、読み取り、書き込み、ワーカー数、セッション数です。 次に、**sys.resource_stats** を使用してこれらのリソース メトリックの平均値と最大値を報告するように修正したクエリを示します:

    ```sql
    SELECT
        avg(avg_cpu_percent) AS 'Average CPU use in percent',
        max(avg_cpu_percent) AS 'Maximum CPU use in percent',
        avg(avg_data_io_percent) AS 'Average physical data IO use in percent',
        max(avg_data_io_percent) AS 'Maximum physical data IO use in percent',
        avg(avg_log_write_percent) AS 'Average log write use in percent',
        max(avg_log_write_percent) AS 'Maximum log write use in percent',
        avg(max_session_percent) AS 'Average % of sessions',
        max(max_session_percent) AS 'Maximum % of sessions',
        avg(max_worker_percent) AS 'Average % of workers',
        max(max_worker_percent) AS 'Maximum % of workers'
    FROM sys.resource_stats
    WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
    ```

3. 各リソース メトリックの平均値と最大値に関するこの情報に基づいて、選択したコンピューティング サイズにワークロードが適合しているかどうかを評価できます。 通常、**sys.resource_stats** からの平均値が目標サイズに対する有効な基準となります。 これを主要なものさしとしてください。 たとえば、S2 コンピューティング サイズで Standard サービス レベルを使用しているとします。 CPU と IO の読み取り/書き込みの平均使用率が 40% を下回り、ワーカーの平均数が 50 を下回り、セッションの平均数が 200 を下回っています。 このワークロードには、S1 コンピューティング サイズが適している可能性があります。 データベースがワーカーとセッションの制限内に収まるかどうかは簡単にわかります。 CPU、読み取り、書き込みに関して、データベースが下位のコンピューティング サイズに適合するかどうかを確認するには、下位コンピューティング サイズの DTU 数を現在のコンピューティング サイズの DTU 数で割り、その計算結果に 100 を掛けます。

    `S1 DTU / S2 DTU * 100 = 20 / 50 * 100 = 40`

    この結果は、2 つのコンピューティング サイズの間の相対的パフォーマンス差異を百分率で表したものになります。 リソースの使用がこの量を超えていない場合、ワークロードは下位のコンピューティング サイズに適合する可能性があります。 ただし、リソース使用率の値を全範囲で見て、どのくらいの頻度でデータベースのワークロードが下位のコンピューティング サイズに適合するかを割合の観点から判断する必要があります。 次のクエリは、この例で計算した 40% のしきい値に基づき、リソース ディメンション別の適合率を出力します。

   ```sql
    SELECT
        (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU Fit Percent',
        (COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log Write Fit Percent',
        (COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical Data IO Fit Percent'
    FROM sys.resource_stats
    WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
    ```

    データベースのサービス レベルに基づき、ワークロードが下位のコンピューティング サイズに適合するかどうかを判断できます。 データベース ワークロード目標が 99.9% で、上記のクエリが 3 つすべてのリソース ディメンションに対して 99.9% を超える値を返す場合、そのワークロードはおそらく下位のコンピューティング サイズに適合します。

    適合率を見ると、目標を満たすために上位のコンピューティング サイズに移行する必要があるかどうかもわかります。 たとえば、userdb1 では過去 1 週間の CPU 使用率が次のようになっています。

   | 平均 CPU 使用率 (%) | 最大 CPU 使用率 (%) |
   | --- | --- |
   | 24.5 |100.00 |

    平均 CPU は、コンピューティング サイズの制限の約 4 分の 1 であり、データベースのコンピューティング サイズにうまく適合するでしょう。 ただし最大値は、データベースがコンピューティング サイズの制限に到達することを示しています。 次に上位のコンピューティング サイズに移動する必要がありますか。 ワークロードが 100% に到達する回数を確認し、それをデータベースのワークロード目標と比較します。

    ```sql
    SELECT
        (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log write fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical data IO fit percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
    ```

    このクエリが 3 つのリソース ディメンションのいずれにも 99.9% 未満の値を返す場合、次の上位のコンピューティング サイズに移行すること、またはアプリケーションの調整手法を使用してデータベースの負荷を減らすことを検討してください。

4. この演習では、予測される将来のワークロードの増加も考慮しています。

エラスティック プールでは、このセクションで説明した手法を使用して、プール内の個々のデータベースを監視できます。 ただし、プールを全体として監視することもできます。 詳細については、[エラスティック プールの監視と管理](elastic-pool-overview.md)に関する記事を参照してください。

### <a name="maximum-concurrent-requests"></a>同時要求の最大数

同時要求の数を確認するには、データベースで次の Transact-SQL クエリを実行します。

```sql
SELECT COUNT(*) AS [Concurrent_Requests]
FROM sys.dm_exec_requests R;
```

SQL Server データベースのワークロードを分析するには、分析したい特定のデータベースでフィルター処理するようにこのクエリを変更します。 たとえば、MyDatabase という名前のオンプレミス データベースがある場合、次の Transact-SQL クエリはそのデータベースの同時要求の数を返します。

```sql
SELECT COUNT(*) AS [Concurrent_Requests]
FROM sys.dm_exec_requests R
INNER JOIN sys.databases D ON D.database_id = R.database_id
AND D.name = 'MyDatabase';
```

これはある時点のスナップショットにすぎません。 ワークロードと同時要求の要件をさらに詳しく理解するには、時間をかけて多くのサンプルを収集する必要があります。

### <a name="maximum-concurrent-logins"></a>同時ログインの最大数

ユーザーやアプリケーションのパターンを分析すれば、ログインの頻度を理解できます。 さらに、テスト環境で実際の負荷を実行し、この上限やこの記事で説明されている他の制限に達していないことを確認できます。 同時ログイン数または履歴を表示できる単一のクエリや動的管理ビュー (DMV) はありません。

複数のクライアントで同じ接続文字列を使用している場合、サービスによってそれぞれのログインが認証されます。 10 人のユーザーが同じユーザー名とパスワードを使ってデータベースに同時に接続した場合、10 件の同時ログインが発生します。 この制限は、ログインと認証の期間のみに適用されます。 同じ 10 人のユーザーがデータベースに順番に接続した場合、同時ログイン数が 1 より大きくなることはありません。

> [!NOTE]
> この制限は現在のところ、エラスティック プールのデータベースには適用されません。

### <a name="maximum-sessions"></a>セッションの最大数

現在アクティブなセッションの数を確認するには、データベースで次の Transact-SQL クエリを実行します。

```sql
SELECT COUNT(*) AS [Sessions]
FROM sys.dm_exec_connections;
```

SQL Server のワークロードを分析する場合は、特定のデータベースが対象になるようにクエリを変更してください。 このクエリは、Azure への移行を検討している場合に、データベースの潜在的なセッションのニーズを判断するのに役立ちます。

```sql
SELECT COUNT(*) AS [Sessions]
FROM sys.dm_exec_connections C
INNER JOIN sys.dm_exec_sessions S ON (S.session_id = C.session_id)
INNER JOIN sys.databases D ON (D.database_id = S.database_id)
WHERE D.name = 'MyDatabase';
```

ここでも、これらのクエリはある時点の数を返します。 時間をかけて複数のサンプルを集めると、セッションの使用状況を正確に把握できます。

[sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) ビューにクエリを実行し、**active_session_count** 列を確認して、セッションの過去の統計値を取得できます。

## <a name="monitoring-query-performance"></a>クエリのパフォーマンスの監視

クエリが低速または実行時間が長いと、大量のシステム リソースが消費される可能性があります。 ここでは、動的管理ビューを使用して、いくつかの一般的なクエリ パフォーマンスの問題を検出する方法について説明します。

### <a name="finding-top-n-queries"></a>上位 N 個のクエリの検索

次の例では、平均 CPU 時間の上位 5 個のクエリに関する情報を返します。 この例では、論理的に等価なクエリがリソースの累計消費量ごとにグループ化されるように、クエリ ハッシュに応じてクエリを集計します。

```sql
SELECT TOP 5 query_stats.query_hash AS "Query Hash",
    SUM(query_stats.total_worker_time) / SUM(query_stats.execution_count) AS "Avg CPU Time",
     MIN(query_stats.statement_text) AS "Statement Text"
FROM
    (SELECT QS.*,
        SUBSTRING(ST.text, (QS.statement_start_offset/2) + 1,
            ((CASE statement_end_offset
                WHEN -1 THEN DATALENGTH(ST.text)
                ELSE QS.statement_end_offset END
            - QS.statement_start_offset)/2) + 1) AS statement_text
FROM sys.dm_exec_query_stats AS QS
CROSS APPLY sys.dm_exec_sql_text(QS.sql_handle) as ST) as query_stats
GROUP BY query_stats.query_hash
ORDER BY 2 DESC;
```

### <a name="monitoring-blocked-queries"></a>クエリのブロックの監視

クエリが低速または実行時間が長いと、大量のリソースが消費され、結果としてクエリがブロックされる可能性があります。 ブロックの原因には、不適切なアプリケーション設計、不適切なクエリ プラン、有効なインデックスの欠如などがあります。 sys.dm_tran_locks ビューを使用すると、データベースで現在ロックされているアクティビティに関する情報を取得することができます。 サンプル コードについては、「[sys.dm_tran_locks (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql)」を参照してください。 ブロッキングのトラブルシューティングの詳細については、[Azure SQL のブロックの問題の概要と解決策](understand-resolve-blocking.md)に関するページを参照してください。

### <a name="monitoring-query-plans"></a>クエリ プランの監視

クエリ プランの効率が悪いと、CPU の消費量が増える可能性があります。 次の例では、[sys.dm_exec_query_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql) ビューを使用して、累積 CPU 時間が最も多いクエリを特定します。

```sql
SELECT
    highest_cpu_queries.plan_handle,
    highest_cpu_queries.total_worker_time,
    q.dbid,
    q.objectid,
    q.number,
    q.encrypted,
    q.[text]
FROM
    (SELECT TOP 50
        qs.plan_handle,
        qs.total_worker_time
    FROM
        sys.dm_exec_query_stats qs
ORDER BY qs.total_worker_time desc) AS highest_cpu_queries
CROSS APPLY sys.dm_exec_sql_text(plan_handle) AS q
ORDER BY highest_cpu_queries.total_worker_time DESC;
```

## <a name="see-also"></a>関連項目

[Azure SQL Database と Azure SQL Managed Instance の概要](sql-database-paas-overview.md)