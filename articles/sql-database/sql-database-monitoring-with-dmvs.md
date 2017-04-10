---
title: "動的管理ビューを使用した Azure SQL Database の監視 | Microsoft Docs"
description: "動的管理ビューを使用して Microsoft Azure SQL Database を監視することで、一般的なパフォーマンスの問題を検出および診断する方法について説明します。"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
tags: 
ms.assetid: d08f505f-3c62-47d4-bab7-35c9a834b79b
ms.service: sql-database
ms.custom: monitor and tune
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 01/10/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: ae0054ac9d87562f6babbfaeaf440d653d60963a


---
# <a name="monitoring-azure-sql-database-using-dynamic-management-views"></a>動的管理ビューを使用した Azure SQL Database の監視
Microsoft Azure SQL Database では、クエリのブロック、クエリの長時間実行、リソースのボトルネック、不適切なクエリ プランなどが原因で発生するパフォーマンスの問題を、動的管理ビューの一部を使用して診断できます。 このトピックでは、動的管理ビューを使用して一般的なパフォーマンスの問題を検出する方法について説明します。

SQL Database は、次に示す 3 つの動的管理ビューを一部サポートしています。

* データベース関連の動的管理ビュー。
* 実行関連の動的管理ビュー。
* トランザクション関連の動的管理ビュー。

動的管理ビューの詳細については、SQL Server オンライン ブックの「 [動的管理ビューおよび関数 (Transact-SQL)](https://msdn.microsoft.com/library/ms188754.aspx) 」を参照してください。

## <a name="permissions"></a>アクセス許可
SQL Database で、動的管理ビューに対してクエリを実行するには、 **VIEW DATABASE STATE** アクセス許可が必要です。 **VIEW DATABASE STATE** アクセス許可は、現在のデータベース内のすべてのオブジェクトに関する情報を返します。
**VIEW DATABASE STATE** アクセス許可を特定のデータベース ユーザーに付与するには、次のクエリを実行します。

```GRANT VIEW DATABASE STATE TO database_user; ```

オンプレミスの SQL Server のインスタンスでは、動的管理ビューにサーバーの状態についての情報が表示されます。 SQL Database では、動的管理ビューには現在の論理データベースに関する情報のみが表示されます。

## <a name="calculating-database-size"></a>データベースのサイズを計算しています
次のクエリは、データベースのサイズ (MB 単位) を返します。

```
-- Calculates the size of the database.
SELECT SUM(reserved_page_count)*8.0/1024
FROM sys.dm_db_partition_stats;
GO
```

次のクエリは、データベース内の個々のオブジェクトのサイズ (MB 単位) を返します。

```
-- Calculates the size of individual database objects.
SELECT sys.objects.name, SUM(reserved_page_count) * 8.0 / 1024
FROM sys.dm_db_partition_stats, sys.objects
WHERE sys.dm_db_partition_stats.object_id = sys.objects.object_id
GROUP BY sys.objects.name;
GO
```

## <a name="monitoring-connections"></a>接続の監視
[sys.dm_exec_connections](https://msdn.microsoft.com/library/ms181509.aspx) ビューを使用して、特定の Azure SQL Database サーバーに対して確立されている接続についての情報と、各接続の詳細を取得できます。 また、[sys.dm_exec_sessions](https://msdn.microsoft.com/library/ms176013.aspx) ビューは、すべてのアクティブなユーザー接続と内部タスクについての情報を取得する場合に役立ちます。
次のクエリは、現在の接続に関する情報を取得します。

```
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
> 
> 

## <a name="monitoring-query-performance"></a>クエリのパフォーマンスの監視
クエリが低速または実行時間が長いと、大量のシステム リソースが消費される可能性があります。 ここでは、動的管理ビューを使用して、いくつかの一般的なクエリ パフォーマンスの問題を検出する方法について説明します。 Microsoft TechNet の [SQL Server 2008 のパフォーマンスに関する問題のトラブルシューティング](http://download.microsoft.com/download/D/B/D/DBDE7972-1EB9-470A-BA18-58849DB3EB3B/TShootPerfProbs2008.docx) についての記事が、古いものの今でもトラブルシューティングに役立ちます。

### <a name="finding-top-n-queries"></a>上位 N 個のクエリの検索
次の例では、平均 CPU 時間の上位 5 個のクエリに関する情報を返します。 この例では、論理的に等価なクエリがリソースの累計消費量ごとにグループ化されるように、クエリ ハッシュに応じてクエリを集計します。

```
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
クエリが低速または実行時間が長いと、大量のリソースが消費され、結果としてクエリがブロックされる可能性があります。 ブロックの原因には、不適切なアプリケーション設計、不適切なクエリ プラン、有効なインデックスの欠如などがあります。 sys.dm_tran_locks ビューを使用すると、Azure SQL Database で現在ロックされているアクティビティに関する情報を取得できます。 コード例については、SQL Server オンライン ブックの「[sys.dm_tran_locks (Transact-SQL)](https://msdn.microsoft.com/library/ms190345.aspx)」をご覧ください。

### <a name="monitoring-query-plans"></a>クエリ プランの監視
クエリ プランの効率が悪いと、CPU の消費量が増える可能性があります。 次の例では、[sys.dm_exec_query_stats](https://msdn.microsoft.com/library/ms189741.aspx) ビューを使用して、累積 CPU 時間が最も多いクエリを特定します。

```
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
[SQL Database の概要](sql-database-technical-overview.md)




<!--HONumber=Nov16_HO3-->


