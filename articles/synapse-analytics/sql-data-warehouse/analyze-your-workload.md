---
title: 専用 SQL プールのワークロードを分析する
description: Azure Synapse Analytics での専用 SQL プールに対するクエリの優先順位付けを分析するための手法。
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 14c3ad30bac7cec4c11822d825323bb9db2ba440
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96454541"
---
# <a name="analyze-your-workload-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Azure Synapse Analytics で専用 SQL プールのワークロードを分析する

Azure Synapse Analytics で専用 SQL プールのワークロードを分析するための手法。 

## <a name="resource-classes"></a>リソース クラス

専用 SQL プールには、システム リソースをクエリに割り当てるリソース クラスが用意されています。  リソース クラスの詳細については、[「ワークロード管理とリソース クラス](resource-classes-for-workload-management.md)」を参照してください。  クエリに割り当てられたリソース クラスが現在利用可能なリソースより多くを必要とする場合、クエリは待機します。

## <a name="queued-query-detection-and-other-dmvs"></a>キューに配置されたクエリの検出とその他の DMV

`sys.dm_pdw_exec_requests` DMV を使用すると、コンカレンシー キューで待機中のクエリを特定できます。 コンカレンシー スロットを待機しているクエリは、**中断** 状態になります。

```sql
SELECT  r.[request_id]                           AS Request_ID
,       r.[status]                               AS Request_Status
,       r.[submit_time]                          AS Request_SubmitTime
,       r.[start_time]                           AS Request_StartTime
,       DATEDIFF(ms,[submit_time],[start_time])  AS Request_InitiateDuration_ms
,       r.resource_class                         AS Request_resource_class
FROM    sys.dm_pdw_exec_requests r
;
```

ワークロード管理ロールを確認するには、 `sys.database_principals`を使用します。

```sql
SELECT  ro.[name]           AS [db_role_name]
FROM    sys.database_principals ro
WHERE   ro.[type_desc]      = 'DATABASE_ROLE'
AND     ro.[is_fixed_role]  = 0
;
```

次のクエリは、各ユーザーが割り当てられているロールを示します。

```sql
SELECT  r.name AS role_principal_name
,       m.name AS member_principal_name
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r            ON rm.role_principal_id      = r.principal_id
JOIN    sys.database_principals AS m            ON rm.member_principal_id    = m.principal_id
WHERE   r.name IN ('mediumrc','largerc','xlargerc')
;
```

専用 SQL プールの待機の種類は次のとおりです。

* **LocalQueriesConcurrencyResourceType**: コンカレンシー スロットのフレームワークの外に配置されたクエリ。 DMV クエリと、 `SELECT @@VERSION` のようなシステム関数は、ローカル クエリの例です。
* **UserConcurrencyResourceType**: コンカレンシー スロットのフレームワーク内に配置されたクエリ。 エンドユーザー テーブルに対するクエリは、このリソースの種類を使用した例です。
* **DmsConcurrencyResourceType**: データ移動操作に起因する待機。
* **BackupConcurrencyResourceType**: この待機は、データベースがバックアップ中であることを示します。 この種類のリソースの最大値は 1 です。 同時に複数のバックアップが要求された場合は、他の要求はキューに配置されます。 一般に、連続したスナップショットでは、最小で 10 分の間を空けることをお勧めします。

`sys.dm_pdw_waits` DMV を使用すると、要求がどのリソースを待っているのかを調べることができます。

```sql
SELECT  w.[wait_id]
,       w.[session_id]
,       w.[type]                                           AS Wait_type
,       w.[object_type]
,       w.[object_name]
,       w.[request_id]
,       w.[request_time]
,       w.[acquire_time]
,       w.[state]
,       w.[priority]
,       SESSION_ID()                                       AS Current_session
,       s.[status]                                         AS Session_status
,       s.[login_name]
,       s.[query_count]
,       s.[client_id]
,       s.[sql_spid]
,       r.[command]                                        AS Request_command
,       r.[label]
,       r.[status]                                         AS Request_status
,       r.[submit_time]
,       r.[start_time]
,       r.[end_compile_time]
,       r.[end_time]
,       DATEDIFF(ms,r.[submit_time],r.[start_time])        AS Request_queue_time_ms
,       DATEDIFF(ms,r.[start_time],r.[end_compile_time])   AS Request_compile_time_ms
,       DATEDIFF(ms,r.[end_compile_time],r.[end_time])     AS Request_execution_time_ms
,       r.[total_elapsed_time]
FROM    sys.dm_pdw_waits w
JOIN    sys.dm_pdw_exec_sessions s  ON w.[session_id] = s.[session_id]
JOIN    sys.dm_pdw_exec_requests r  ON w.[request_id] = r.[request_id]
WHERE    w.[session_id] <> SESSION_ID()
;
```

`sys.dm_pdw_resource_waits` DMV には特定のクエリに対する待機情報が表示されます。 リソース待機時間は、提供されるリソースを待機する時間を測定します。 シグナルの待機時間は、基になる SQL Server によりクエリが CPU にスケジュール設定されるまでの時間です。

```sql
SELECT  [session_id]
,       [type]
,       [object_type]
,       [object_name]
,       [request_id]
,       [request_time]
,       [acquire_time]
,       DATEDIFF(ms,[request_time],[acquire_time])  AS acquire_duration_ms
,       [concurrency_slots_used]                    AS concurrency_slots_reserved
,       [resource_class]
,       [wait_id]                                   AS queue_position
FROM    sys.dm_pdw_resource_waits
WHERE    [session_id] <> SESSION_ID()
;
```

`sys.dm_pdw_resource_waits` DMV を使用して、許可されたコンカレンシー スロットの数を計算することもできます。

```sql
SELECT  SUM([concurrency_slots_used]) as total_granted_slots
FROM    sys.[dm_pdw_resource_waits]
WHERE   [state]           = 'Granted'
AND     [resource_class] is not null
AND     [session_id]     <> session_id()
;
```

`sys.dm_pdw_wait_stats` DMV を使用すると、待機のこれまでの傾向を分析できます。

```sql
SELECT   w.[pdw_node_id]
,        w.[wait_name]
,        w.[max_wait_time]
,        w.[request_count]
,        w.[signal_time]
,        w.[completed_count]
,        w.[wait_time]
FROM    sys.dm_pdw_wait_stats w
;
```

## <a name="next-steps"></a>次のステップ

データベース ユーザーの管理とセキュリティの詳細については、[専用 SQL プール (以前の SQL DW) のセキュリティ保護](sql-data-warehouse-overview-manage-security.md)に関する記事を参照してください。 大規模なリソース クラスを使用して、クラスター化された列ストア インデックスの品質を向上させる方法については、「[セグメントの品質を向上させるためのインデックスの再構築](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality)」を参照してください。
