---
title: "DMV を利用してワークロードを監視する | Microsoft Docs"
description: "DMV を利用してワークロードを監視するについて説明します。"
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: 69ecd479-0941-48df-b3d0-cf54c79e6549
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 6877a54f77a4c0137e4f6a8b2b2fcff41664a4b5


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
SQL Data Warehouse で実行されるすべてのクエリは、[sys.dm_pdw_exec_requests][sys.dm_pdw_exec_requests] に記録されます。  この DMV には、実行された過去 10,000 件のクエリが含まれています。  request_id により各クエリが一意に識別されます。これはこの DMV のプライマリ キーです。  request_id は、新しいクエリごとに順番に割り当てられ、クエリ ID を表す QID がプレフィックスとして付加されます。  特定の session_id のこの DMV にクエリを実行すると、そのログオンのクエリがすべて表示されます。

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

**中断** 状態のクエリは、同時実行の制限が原因でクエリが実行されています。 これらのクエリは、UserConcurrencyResourceType 型の sys.dm_pdw_waits 待機クエリにも表示されます。 同時実行の制限の詳細については、[同時実行とワークロード管理][同時実行とワークロード管理] に関するページをご覧ください。 クエリの待機は、オブジェクト ロックなど、他の理由によっても発生します。  クエリがリソースを待っている場合は、この記事の下にある [リソースを待機しているクエリの調査][リソースを待機しているクエリの調査] に関するトピックをご覧ください。

sys.dm_pdw_exec_requests テーブルのクエリの参照を簡略化するには、[ラベル][ラベル] に関するページを参照して、sys.dm_pdw_exec_requests ビューで参照できるクエリにコメントを割り当てます。

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

DSQL プランに予想よりも時間がかかる場合は、多くの DSQL 手順が存在する複雑なプランであったり、1 つの手順に長い時間を要することが原因であったりする可能性があります。  プランにいくつかの移動操作を含む多くの手順が存在する場合は、テーブルのディストリビューションを最適化して、データの移動を削減することを検討してください。 [テーブルのディストリビューション][テーブルのディストリビューション] に関する記事では、データを移動してクエリを解決する必要がある理由と、データの移動を最小限に抑えるためのいくつかのディストリビューションの方法について説明します。

1 つの手順に関する詳細を調査するには、実行時間の長いクエリ手順の *operation_type* 列を確認し、**手順インデックス**を書き留めます。

* 手順 3a の **SQL 操作**(OnOperation、RemoteOperation、ReturnOperation) に進みます。
* 手順 3b の **Data Movement 操作**(ShuffleMoveOperation、BroadcastMoveOperation、TrimMoveOperation、PartitionMoveOperation、MoveOperation、CopyOperation) に進みます。

### <a name="step-3a-investigate-sql-on-the-distributed-databases"></a>手順 3a: 分散データベースでの SQL を調査する
要求 ID と手順インデックスを使用して、[sys.dm_pdw_sql_requests][sys.dm_pdw_sql_requests] から詳細情報を取得します。これには、配布されたすべてのデータベースに対するクエリの実行情報が含まれます。

```sql
-- Find the distribution run times for a SQL step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_sql_requests
WHERE request_id = 'QID####' AND step_index = 2;
```

クエリ手順が実行中の場合は、[DBCC PDW_SHOWEXECUTIONPLAN][DBCC PDW_SHOWEXECUTIONPLAN] を使用して、特定の配布で実行中の手順に対する SQL Server プラン キャッシュから、SQL Server 推定プランを取得できます。

```sql
-- Find the SQL Server execution plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(1, 78);
```

### <a name="step-3b-investigate-data-movement-on-the-distributed-databases"></a>手順 3b: 分散データベース上のデータ移動を調査する
要求 ID と手順インデックスを利用し、[sys.dm_pdw_dms_workers][sys.dm_pdw_dms_workers] から各配布で実行されているデータ移動手順に関する情報を取得します。

```sql
-- Find the information about all the workers completing a Data Movement Step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_dms_workers
WHERE request_id = 'QID####' AND step_index = 2;
```

* *total_elapsed_time* 列で、特定の配布で他の配布よりデータ移動に大幅に時間がかかっていないか確認します。
* 実行時間の長い配布に対して、*rows_processed* 列で、その配布から移動された行の数が他の配布より大幅に大きいか確認します。 大きい場合は、基になるデータの傾斜を示している可能性があります。

クエリが実行中の場合は、[DBCC PDW_SHOWEXECUTIONPLAN][DBCC PDW_SHOWEXECUTIONPLAN] を使用して、特定の配布内で現在実行中の SQL 手順に対する SQL Server プラン キャッシュから、SQL Server 推定プランを取得できます。

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

## <a name="next-steps"></a>次のステップ
DMV の詳細については、[「システム ビュー」][システム ビュー] をご覧ください。
ベスト プラクティスについては、 [「SQL Data Warehouse のベスト プラクティス」][SQL Data Warehouse のベスト プラクティス] をご覧ください。

<!--Image references-->

<!--Article references-->
[管理の概要]: ./sql-data-warehouse-overview-manage.md
[SQL Data Warehouse のベスト プラクティス]: ./sql-data-warehouse-best-practices.md
[システム ビュー]: ./sql-data-warehouse-reference-tsql-system-views.md
[テーブルのディストリビューション]: ./sql-data-warehouse-tables-distribute.md
[同時実行とワークロード管理]: ./sql-data-warehouse-develop-concurrency.md
[リソースを待機しているクエリの調査]: ./sql-data-warehouse-manage-monitor.md#waiting

<!--MSDN references-->
[sys.dm_pdw_dms_workers]: http://msdn.microsoft.com/library/mt203878.aspx
[sys.dm_pdw_exec_requests]: http://msdn.microsoft.com/library/mt203887.aspx
[sys.dm_pdw_exec_sessions]: http://msdn.microsoft.com/library/mt203883.aspx
[sys.dm_pdw_request_steps]: http://msdn.microsoft.com/library/mt203913.aspx
[sys.dm_pdw_sql_requests]: http://msdn.microsoft.com/library/mt203889.aspx
[DBCC PDW_SHOWEXECUTIONPLAN]: http://msdn.microsoft.com/library/mt204017.aspx
[DBCC PDW_SHOWSPACEUSED]: http://msdn.microsoft.com/library/mt204028.aspx
[ラベル]: https://msdn.microsoft.com/library/ms190322.aspx



<!--HONumber=Nov16_HO3-->


