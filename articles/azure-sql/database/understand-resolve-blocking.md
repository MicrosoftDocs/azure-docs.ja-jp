---
title: Azure SQL のブロックの問題の概要と解決策
titleSuffix: Azure SQL Database
description: Azure SQL データベース固有のブロックとトラブルシューティングに関するトピックの概要です。
services: sql-database
dev_langs:
- TSQL
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: ''
ms.date: 3/02/2021
ms.openlocfilehash: 3d64336184450514d52095097343a4588213f111
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102034899"
---
# <a name="understand-and-resolve-azure-sql-database-blocking-problems"></a>Azure SQL Database のブロックの問題の概要と解決策
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

## <a name="objective"></a>目的

この記事では、Azure SQL データベースのブロックについて説明し、ブロックのトラブルシューティングと解決の方法を示します。 

この記事では、接続という用語は、データベースの 1 回のログオン セッションを指します。 各接続は、多くの DMV でセッション ID (SPID) または session_id として表示されます。 これらの各 SPID は、多くの場合にプロセスと呼ばれますが、通常の意味での個別のプロセス コンテキストではありません。 各 SPID は、特定のクライアントからの単一の接続の要求を処理するために必要なサーバー リソースとデータ構造で構成されます。 1 つのクライアント アプリケーションで 1 つ以上の接続を確立できます。 Azure SQL Database の観点から、単一のクライアント コンピューター上の単一のクライアント アプリケーションからの複数の接続と、複数のクライアント アプリケーションまたは複数のクライアント コンピューターからの複数の接続の間に違いはなく、それらはアトミックです。 ソース クライアントに関係なく、1 つの接続によって、別の接続がブロックされる可能性があります。

> [!NOTE]
> **この内容は Azure SQL Database が対象です。** Azure SQL Database は、Microsoft SQL Server データベース エンジンの最新の安定バージョンに基づいているため、トラブルシューティングのオプションやツールが異なる場合はありますが、コンテンツの大半は似ています。 SQL Server のブロックの詳細については、「[SQL Server の問題の概要と解決策](/troubleshoot/sql/performance/understand-resolve-blocking)」を参照してください。

## <a name="understand-blocking"></a>ブロックの概要 
 
ブロックは、ロックベースのコンカレンシーを備えるリレーショナル データベース管理システム (RDBMS) の回避不可能な仕様の特性です。 前述のように、SQL Server では、ブロックは、あるセッションによって、特定のリソースのロックが保持されており、2 つ目の SPID によって、同じリソースに対して競合するロックの種類の取得が試みられたときに発生します。 通常、最初の SPID によってリソースがロックされる期間はわずかです。 所有しているセッションでロックが解放されると、2 つ目の接続によって、自由にそのリソースへの独自のロックが取得され、処理が続行されます。 これは通常の動作であり、システムのパフォーマンスに顕著な影響を与えることなく、日に何度も発生する可能性があります。

クエリの期間とトランザクション コンテキストによって、そのロックが保持される長さが決まり、さらにそれによって、他のクエリに与える影響が決まります。 クエリがトランザクション内で実行されていない (およびロック ヒントが使用されていない) 場合、SELECT ステートメントのロックは、クエリの実行中ではなく、実際に読み取られている時点でのみ、リソースに対して保持されます。 INSERT、UPDATE、および DELETE ステートメントでは、データの整合性と、必要に応じてクエリをロールバックできるように、クエリの実行中にロックが保持されます。

トランザクション内で実行されるクエリの場合、ロックが保持される期間は、クエリの種類、トランザクション分離レベル、およびクエリでロック ヒントが使用されているかどうかによって決まります。 ロック、ロック ヒント、およびトランザクション分離レベルについては、次の記事を参照してください。

* [データベース エンジンのロック](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide)
* [ロックおよび行のバージョン管理のカスタマイズ](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide#customizing-locking-and-row-versioning)
* [ロック モード](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide#lock_modes)
* [ロックの互換性](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide#lock_compatibility)
* [トランザクション](/sql/t-sql/language-elements/transactions-transact-sql)

システム パフォーマンスに有害な影響を与えるほどロックとブロックが持続する場合、通常、次のいずれかの理由によります。

* SPID によって、一連のリソースが解放されるまで長期間ロックされている。 この種類のブロックは、時間の経過と共に解決されますが、パフォーマンスが低下する可能性があります。

* SPID によって一連のリソースがロックされ、解放されない。 この種類のブロックは、自動的に解決されず、影響を受けるリソースへのアクセスが無期限に妨げられます。

最初のシナリオでは、時間の経過と共にさまざまな SPID によってさまざまなリソースがブロックされ、ターゲットの移動が発生するため、状況は非常に変わりやすい可能性があります。 このような状況では、[SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) を使用して、個々のクエリに問題を絞り込むトラブルシューティングを行うことが困難です。 一方、2 つ目の状況では、一貫した状態になり、診断が容易な可能性があります。

## <a name="applications-and-blocking"></a>アプリケーションとブロック

ブロックの問題が発生した場合、サーバー側のチューニングとプラットフォームの問題に重点を置きがちになる可能性があります。 ただし、データベースにのみ注目していると、解決につながらないことがあり、クライアント アプリケーションとそれによって送信されたクエリを調べることに向けた方がよい時間とエネルギーを奪われる可能性があります。 実行されたデータベースの呼び出しに関して、アプリケーションで公開される可視性のレベルに関係なく、ブロックの問題では、アプリケーションによって送信された正確な SQL ステートメントの調査と、クエリの取り消し、接続管理、すべての結果行のフェッチなどに関するアプリケーションの正確な動作の両方が多くの場合に必要になります。 開発ツールで、接続管理、クエリの取り消し、クエリのタイムアウト、結果のフェッチなどに対する明示的な制御が許可されていない場合、ブロックの問題を解決できない可能性があります。 この可能性は、特にパフォーマンスに敏感な OLTP 環境で、Azure SQL Database 用のアプリケーション開発ツールを選択する前に、詳しく調べる必要があります。 

データベースとアプリケーションの設計および構築フェーズでは、データベースのパフォーマンスに注意してください。 特に、リソース消費、分離レベル、およびトランザクション パスの長さを、クエリごとに評価する必要があります。 各クエリおよびトランザクションは、可能な限り軽量にする必要があります。 優れた接続管理規範を施行する必要があります。そうしないと、アプリケーションは、ユーザー数が少ないときには許容できるパフォーマンスでも、ユーザー数が増えるにつれてパフォーマンスが著しく低下していく可能性があります。 

アプリケーションとクエリの適切な設計により、Azure SQL Database では、単一のサーバーで、ブロックがほとんど発生せずに、数千の同時ユーザーをサポートすることができます。

> [!Note]
> アプリケーション開発のガイダンスの詳細については、「[Azure SQL Database および Azure SQL Managed Instance の接続に関する問題とその他のエラーのトラブルシューティング](troubleshoot-common-errors-issues.md)」および[一時的な障害の処理](/aspnet/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/transient-fault-handling)に関するページを参照してください。

## <a name="troubleshoot-blocking"></a>ブロックのトラブルシューティング

発生しているブロックの状況に関係なく、ロックのトラブルシューティングの方法は同じです。 これらの論理的な区別によって、この記事の残りの構成が決まります。 概念は、ヘッド ブロッカーを見つけて、そのクエリが何を実行しているかと、なぜそれがブロックしているかを特定することです。 問題のあるクエリが特定されたら (つまり、長時間ロックを保持しているものは何か)、次の手順は、なぜブロックが発生しているのかを分析して判断することです。 その理由を理解したら、クエリとトランザクションを再設計することで、変更を加えることができます。

トラブルシューティングの手順:

1. メイン ブロック セッション (ヘッド ブロッカー) を特定する

2. ブロックの原因となっているクエリとトランザクションを見つける (長時間ロックを保持しているものは何か)

3. 長時間のブロックが発生する原因を分析して理解する

4. クエリとトランザクションを再設計して、ブロックの問題を解決する

ここで、適切なデータ キャプチャによって、メイン ブロック セッションを特定する方法について説明します。

## <a name="gather-blocking-information"></a>ブロック情報の収集

ブロックの問題のトラブルシューティングの難しさを軽減するため、データベース管理者は、Azure SQL データベースのロックとブロックの状態を常に監視する SQL スクリプトを使用できます。 このデータを収集するには、基本的に 2 つの方法があります。 

1 つ目は、動的管理オブジェクト (DMO) のクエリを実行し、経時的な比較の結果を保存することです。 この記事で参照しているオブジェクトには、動的管理ビュー (DMV) と、動的管理関数 (DMF) があります。 2 つ目の方法は、XEvent を使用して、何が実行されているかをキャプチャすることです。 


## <a name="gather-information-from-dmvs"></a>DMV からの情報の収集

ブロックをトラブルシューティングするために DMV を参照することには、ブロック チェーンのヘッドにある SPID (セッション ID) と SQL ステートメントを特定するという目標があります。 ブロックされている犠牲者の SPID を見つけます。 SPID が別の SPID によってブロックされている場合、リソースを所有している SPID (ブロックしている SPID) を調査します。 その所有者の SPID もブロックされていますか。 チェーンをたどってヘッド ブロッカーを見つけて、そのロックが保持されている理由を調査できます。

ターゲットの Azure SQL データベースでこれらの各スクリプトを実行してください。

* sp_who コマンドと sp_who2 コマンドは、現在のすべてのセッションを表示する古いコマンドです。 DMV sys.dm_exec_sessions では、クエリの実行やフィルター処理が容易な結果セットに、より多くのデータが返されます。 sys.dm_exec_sessions は、他のクエリの中心にあることがわかります。 

* 特定のセッションを既に識別している場合、`DBCC INPUTBUFFER(<session_id>)` を使用して、セッションによって最後に送信されたステートメントを見つけることができます。 sys.dm_exec_input_buffer 動的管理関数 (DMF) でも、session_id と request_id を指定して、クエリやフィルター処理が簡単な結果セットで同様の結果が返されます。 たとえば、session_id 66 と request_id 0 によって送信された最新のクエリを返すには:

```sql
SELECT * FROM sys.dm_exec_input_buffer (66,0);
```

* sys.dm_exec_requests を参照し、blocking_session_id 列を参照します。 blocking_session_id = 0 の場合、セッションはブロックされていません。 sys.dm_exec_requests では、現在実行中の要求のみが一覧表示されますが、sys.dm_exec_sessions ではすべての接続 (アクティブまたは非アクティブ) が一覧表示されます。 次のクエリでは、sys.dm_exec_requests と sys.dm_exec_sessions の間のこの共通結合に基づきます。

* このサンプル クエリを実行し、[sys.dm_exec_sql_text](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sql-text-transact-sql) または [sys.dm_exec_input_buffer](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-input-buffer-transact-sql) DMV を使用して、アクティブに実行されているクエリとそれらの現在の SQL バッチ テキストまたは入力バッファー テキストを見つけます。 sys.dm_exec_sql_text の `text` フィールドによって返されたデータが NULL の場合、クエリは現在実行されていません。 その場合、sys.dm_exec_input_buffer の `event_info` フィールドには、SQL エンジンに渡された最後のコマンド文字列が含まれます。 このクエリは、session_id ごとのブロックされている session_ids の一覧など、他のセッションをブロックしているセッションを識別するためにも使用できます。 

```sql
WITH cteBL (session_id, blocking_these) AS 
(SELECT s.session_id, blocking_these = x.blocking_these FROM sys.dm_exec_sessions s 
CROSS APPLY    (SELECT isnull(convert(varchar(6), er.session_id),'') + ', '  
                FROM sys.dm_exec_requests as er
                WHERE er.blocking_session_id = isnull(s.session_id ,0)
                AND er.blocking_session_id <> 0
                FOR XML PATH('') ) AS x (blocking_these)
)
SELECT s.session_id, blocked_by = r.blocking_session_id, bl.blocking_these
, batch_text = t.text, input_buffer = ib.event_info, * 
FROM sys.dm_exec_sessions s 
LEFT OUTER JOIN sys.dm_exec_requests r on r.session_id = s.session_id
INNER JOIN cteBL as bl on s.session_id = bl.session_id
OUTER APPLY sys.dm_exec_sql_text (r.sql_handle) t
OUTER APPLY sys.dm_exec_input_buffer(s.session_id, NULL) AS ib
WHERE blocking_these is not null or r.blocking_session_id > 0
ORDER BY len(bl.blocking_these) desc, r.blocking_session_id desc, r.session_id;
```

* Microsoft サポートによって提供された、より複雑なこのサンプル クエリを実行して、ブロック チェーンに含まれるセッションのクエリ テキストを含め、複数のセッション ブロック チェーンの先頭を識別します。

```sql
WITH cteHead ( session_id,request_id,wait_type,wait_resource,last_wait_type,is_user_process,request_cpu_time
,request_logical_reads,request_reads,request_writes,wait_time,blocking_session_id,memory_usage
,session_cpu_time,session_reads,session_writes,session_logical_reads
,percent_complete,est_completion_time,request_start_time,request_status,command
,plan_handle,sql_handle,statement_start_offset,statement_end_offset,most_recent_sql_handle
,session_status,group_id,query_hash,query_plan_hash) 
AS ( SELECT sess.session_id, req.request_id, LEFT (ISNULL (req.wait_type, ''), 50) AS 'wait_type'
    , LEFT (ISNULL (req.wait_resource, ''), 40) AS 'wait_resource', LEFT (req.last_wait_type, 50) AS 'last_wait_type'
    , sess.is_user_process, req.cpu_time AS 'request_cpu_time', req.logical_reads AS 'request_logical_reads'
    , req.reads AS 'request_reads', req.writes AS 'request_writes', req.wait_time, req.blocking_session_id,sess.memory_usage
    , sess.cpu_time AS 'session_cpu_time', sess.reads AS 'session_reads', sess.writes AS 'session_writes', sess.logical_reads AS 'session_logical_reads'
    , CONVERT (decimal(5,2), req.percent_complete) AS 'percent_complete', req.estimated_completion_time AS 'est_completion_time'
    , req.start_time AS 'request_start_time', LEFT (req.status, 15) AS 'request_status', req.command
    , req.plan_handle, req.[sql_handle], req.statement_start_offset, req.statement_end_offset, conn.most_recent_sql_handle
    , LEFT (sess.status, 15) AS 'session_status', sess.group_id, req.query_hash, req.query_plan_hash
    FROM sys.dm_exec_sessions AS sess
    LEFT OUTER JOIN sys.dm_exec_requests AS req ON sess.session_id = req.session_id
    LEFT OUTER JOIN sys.dm_exec_connections AS conn on conn.session_id = sess.session_id 
    )
, cteBlockingHierarchy (head_blocker_session_id, session_id, blocking_session_id, wait_type, wait_duration_ms,
wait_resource, statement_start_offset, statement_end_offset, plan_handle, sql_handle, most_recent_sql_handle, [Level])
AS ( SELECT head.session_id AS head_blocker_session_id, head.session_id AS session_id, head.blocking_session_id
    , head.wait_type, head.wait_time, head.wait_resource, head.statement_start_offset, head.statement_end_offset
    , head.plan_handle, head.sql_handle, head.most_recent_sql_handle, 0 AS [Level]
    FROM cteHead AS head
    WHERE (head.blocking_session_id IS NULL OR head.blocking_session_id = 0)
    AND head.session_id IN (SELECT DISTINCT blocking_session_id FROM cteHead WHERE blocking_session_id != 0)
    UNION ALL
    SELECT h.head_blocker_session_id, blocked.session_id, blocked.blocking_session_id, blocked.wait_type,
    blocked.wait_time, blocked.wait_resource, h.statement_start_offset, h.statement_end_offset,
    h.plan_handle, h.sql_handle, h.most_recent_sql_handle, [Level] + 1
    FROM cteHead AS blocked
    INNER JOIN cteBlockingHierarchy AS h ON h.session_id = blocked.blocking_session_id and h.session_id!=blocked.session_id --avoid infinite recursion for latch type of blocking
    WHERE h.wait_type COLLATE Latin1_General_BIN NOT IN ('EXCHANGE', 'CXPACKET') or h.wait_type is null
    )
SELECT bh.*, txt.text AS blocker_query_or_most_recent_query 
FROM cteBlockingHierarchy AS bh 
OUTER APPLY sys.dm_exec_sql_text (ISNULL ([sql_handle], most_recent_sql_handle)) AS txt;
```

* 長時間実行しているかまたはコミットされていないトランザクションをキャッチするには、[sys.dm_tran_database_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-database-transactions-transact-sql)、[sys.dm_tran_session_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-session-transactions-transact-sql)、[sys.dm_exec_connections](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-connections-transact-sql)、および sys.dm_exec_sql_text を含む、現在開いているトランザクションを表示するための別の DMV セットを使用します。 トランザクションの追跡に関連付けられているいくつかの DMV があります。こちらで、その他の[トランザクションの DMV](/sql/relational-databases/system-dynamic-management-views/transaction-related-dynamic-management-views-and-functions-transact-sql) を参照してください。 

```sql
SELECT [s_tst].[session_id],
[database_name] = DB_NAME (s_tdt.database_id),
[s_tdt].[database_transaction_begin_time], 
[sql_text] = [s_est].[text] 
FROM sys.dm_tran_database_transactions [s_tdt]
INNER JOIN sys.dm_tran_session_transactions [s_tst] ON [s_tst].[transaction_id] = [s_tdt].[transaction_id]
INNER JOIN sys.dm_exec_connections [s_ec] ON [s_ec].[session_id] = [s_tst].[session_id]
CROSS APPLY sys.dm_exec_sql_text ([s_ec].[most_recent_sql_handle]) AS [s_est];
```

* SQL のスレッドまたはタスク層にある[sys.dm_os_waiting_tasks](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) を参照してください。 これにより、要求で現在発生している SQL の待機の種類に関する情報が返されます。 sys.dm_exec_requests と同様に、sys.dm_os_waiting_tasks によってアクティブな要求のみが返されます。 

> [!Note]
> 経時的に集計される待機の統計情報などの待機の種類の詳細については、DMV「[sys.dm_db_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database)」を参照してください。 この DMV によって、現在のデータベースのみの集計待機統計情報が返されます。

* クエリによってどのロックが設定されているかに関するより詳細な情報については、[sys.dm_tran_locks](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql) DMV を使用します。 この DMV によって、実稼働 SQL Server に関する大量のデータが返される可能性があり、現在どんなロックが保持しているかを診断するのに役立ちます。 

sys.dm_os_waiting_tasks の INNER JOIN のため、次のクエリでは、sys.dm_tran_locks からの出力が、現在ブロックされている要求、それらの待機状態、およびそれらのロックに限定されます。

```sql
SELECT table_name = schema_name(o.schema_id) + '.' + o.name
, wt.wait_duration_ms, wt.wait_type, wt.blocking_session_id, wt.resource_description
, tm.resource_type, tm.request_status, tm.request_mode, tm.request_session_id
FROM sys.dm_tran_locks AS tm
INNER JOIN sys.dm_os_waiting_tasks as wt ON tm.lock_owner_address = wt.resource_address
LEFT OUTER JOIN sys.partitions AS p on p.hobt_id = tm.resource_associated_entity_id
LEFT OUTER JOIN sys.objects o on o.object_id = p.object_id or tm.resource_associated_entity_id = o.object_id
WHERE resource_database_id = DB_ID()
AND object_name(p.object_id) = '<table_name>';
```

* DMV を使用すると、時間の経過と共にクエリ結果が保存されることで、指定した時間間隔でブロックを確認できるデータ ポイントが得られ、持続的なブロックや傾向を特定できます。 

## <a name="gather-information-from-extended-events"></a>拡張イベントからの情報の収集

前の情報に加えて、多くの場合、Azure SQL Database でのブロックの問題を十分に調査するために、サーバー上のアクティビティのトレースをキャプチャする必要があります。 たとえば、セッションによって、トランザクション内で複数のステートメントが実行される場合、最後に送信されたステートメントのみが表示されます。 しかし、それ以前のいずれかのステートメントに、ロックがまだ保持されている理由がある場合があります。 トレースにより、現在のトランザクション内でセッションによって実行されたすべてのコマンドを確認できます。

SQL Server でトレースをキャプチャする方法は 2 つあります。拡張イベント (Xevent) とプロファイラー トレースです。 ただし、[SQL Server Profiler](/sql/tools/sql-server-profiler/sql-server-profiler) は、Azure SQL Database でサポートされていない非推奨のトレース テクノロジです。 [拡張イベント](/sql/relational-databases/extended-events/extended-events)は、より汎用性が高く、監視対象のシステムへの影響が少ない新しいトレース テクノロジであり、そのインターフェイスは SQL Server Management Studio (SSMS) に統合されています。 

SSMS の[拡張イベントの新しいセッション ウィザード](/sql/relational-databases/extended-events/quick-start-extended-events-in-sql-server)の使用方法について説明しているドキュメントを参照してください。 ただし、Azure SQL データベースの場合、SSMS によって、オブジェクト エクスプローラーの各データベースの下に拡張イベント サブフォルダーが提供されます。 拡張イベント セッション ウィザードを使用して、これらの有益なイベントをキャプチャします。 

-   カテゴリ エラー:
    -   Attention
    -   Error_reported  
    -   Execution_warning

-   カテゴリ警告: 
    -   Missing_join_predicate

-   カテゴリ実行:
    -   Rpc_completed
    -   Rpc_starting 
    -   Sql_batch_completed
    -   Sql_batch_starting

-   ロック
    -   Lock_deadlock

-   Session
    -   Existing_connection
    -   ログイン
    -   Logout

## <a name="identify-and-resolve-common-blocking-scenarios"></a>一般的なブロック シナリオの特定と解決

前の情報を調べることで、ほとんどのブロックの問題の原因を特定できます。 この記事の残りの部分では、この情報を使用して、いくつかの一般的なブロック シナリオを特定し、解決する方法について説明します。 この説明では、ブロック スクリプト (前述) を使用して、ブロックしている SPID についての情報をキャプチャし、XEvent セッションを使用して、アプリケーション アクティビティをキャプチャしていることを前提としています。

## <a name="analyze-blocking-data"></a>ブロック データの分析 

* DMV sys.dm_exec_requests と sys.dm_exec_sessions の出力を調べ、blocking_these と session_id を使用して、ブロック チェーンのヘッドを特定します。 これにより、ブロックされている要求とブロックしている要求が最も明確に特定されます。 ブロックされているセッションとブロックしているセッションについて、さらに詳しく調査します。 ブロック チェーンに共通のものまたはルートがありますか。 それらは共通のテーブルを共有し、ブロック チェーンに含まれる 1 つ以上のセッションで、書き込み操作が実行されている可能性があります。 

* DMV sys.dm_exec_requests と sys.dm_exec_sessions の出力で、ブロック チェーンのヘッドにある SPID に関する情報を調べます。 次のフィールドを探します。 

    -    `sys.dm_exec_requests.status`  
    この列には、特定の要求の状態が表示されます。 通常、休止中状態とは、SPID が実行を完了し、アプリケーションから別のクエリやバッチが送信されるのを待機していることを示します。 実行可能または実行中状態は、SPID が現在クエリを処理していることを示します。 次の表に、さまざまな状態値の簡単な説明を示します。

    | Status | 意味 |
    |:-|:-|
    | バックグラウンド | SPID は、デッドロック検出、ログ ライター、チェックポイントなどのバックグラウンド タスクを実行中です。 |
    | 休止中 | SPID は現在実行されていません。 これは通常、SPID がアプリケーションからのコマンドを待機していることを示します。 |
    | 実行中 | SPID は現在スケジューラ上で実行中です。 |
    | 実行可能 | SPID はスケジューラの実行可能キューにあり、スケジューラ時間の取得を待機しています。 |
    | Suspended | SPID は、ロックやラッチなどのリソースを待機しています。 | 
                       
    -    `sys.dm_exec_sessions.open_transaction_count`  
    このフィールドは、このセッションで開いているトランザクションの数を示します。 この値が 0 より大きい場合、SPID は開いているトランザクション内にあり、トランザクション内のいずれかのステートメントによって取得されたロックを保持している可能性があります。

    -    `sys.dm_exec_requests.open_transaction_count`  
    同様に、このフィールドは、この要求で開いているトランザクションの数を示しています。 この値が 0 より大きい場合、SPID は開いているトランザクション内にあり、トランザクション内のいずれかのステートメントによって取得されたロックを保持している可能性があります。

    -   `sys.dm_exec_requests.wait_type`、`wait_time`、`last_wait_type`  
     `sys.dm_exec_requests.wait_type`  が NULL の場合、要求では現在何も待機しておらず、 `last_wait_type`  値は、要求で発生した最後の  `wait_type`  を示します。  `sys.dm_os_wait_stats` の詳細と、最も一般的な待機の種類の説明については、「[sys.dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql)」を参照してください。  `wait_time`  値を使用して、要求が進行中かどうかを判断できます。 sys.dm_exec_requests テーブルに対するクエリで、 `wait_time`  列に、sys.dm_exec_requests の前のクエリからの  `wait_time`  値より少ない値が返された場合、これは前のロックが取得され、解放されており、現在新しいロックを待機中であることを示しています (`wait_time` がゼロ以外であると想定して)。 これは sys.dm_exec_requests の出力間で `wait_resource`  を比較して確認できます。これにより、要求で待機中のリソースが表示されます。

    -   `sys.dm_exec_requests.wait_resource` このフィールドは、ブロックされた要求で待機中のリソースを示しています。 次の表に、一般的な  `wait_resource`  の形式とそれらの意味を示します。

    | リソース | Format | 例 | 説明 | 
    |:-|:-|:-|:-|
    | テーブル | DatabaseID:ObjectID:IndexID | TAB:5:261575970:1 | この場合、データベース ID 5 は pubs サンプル データベース、オブジェクト ID 261575970 は titles テーブル、1 はクラスター化インデックスです。 |
    | ページ | DatabaseID:FileID:PageID | PAGE:5:1:104 | この場合、データベース ID 5 は pubs、ファイル ID 1 はプライマリ データ ファイル、ページ 104 は titles テーブルに属するページです。 ページが属している object_id を特定するには、動的管理関数 [sys.dm_db_page_info](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-page-info-transact-sql) を使用して、`wait_resource` からの DatabaseID、FileId、PageId を渡します。 | 
    | キー | DatabaseID:Hobt_id (インデックス キーのハッシュ値) | KEY:5:72057594044284928 (3300a4f361aa) | この場合、データベース ID 5 は Pubs、Hobt_ID 72057594044284928 は object_id 261575970 の index_id 2 に対応します (titles テーブル)。 sys.partitions カタログ ビューを使用して、特定の index_id と object_id に hobt_id を関連付けます。 インデックス キーのハッシュを特定のキー値に非ハッシュ化する方法はありません。 |
    | 行 | DatabaseID:FileID:PageID:Slot(row) | RID:5:1:104:3 | この場合、データベース ID 5 は pubs、ファイル ID 1 はプライマリ データ ファイル、ページ 104 は titles テーブルに属するページ、スロット 3 はページ上の行の位置を示します。 |
    | Compile  | DatabaseID:FileID:PageID:Slot(row) | RID:5:1:104:3 | この場合、データベース ID 5 は pubs、ファイル ID 1 はプライマリ データ ファイル、ページ 104 は titles テーブルに属するページ、スロット 3 はページ上の行の位置を示します。 |

    -    `sys.dm_tran_active_transactions` [sys.dm_tran_active_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-active-transactions-transact-sql) DMV には、コミットまたはロールバックを待機しているトランザクションの全体像を把握するために、他の DMV に結合できる、開いているトランザクションに関するデータが含まれています。 次のクエリを使用して、[sys.dm_tran_session_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-session-transactions-transact-sql) を含む他の DMV に結合されている開いているトランザクションに関する情報を返します。 トランザクションの現在の状態、`transaction_begin_time`、その他の状況データを考慮して、ブロックの原因である可能性があるかどうかを評価します。

    ```sql
    SELECT tst.session_id, [database_name] = db_name(s.database_id)
    , tat.transaction_begin_time
    , transaction_duration_s = datediff(s, tat.transaction_begin_time, sysdatetime()) 
    , transaction_type = CASE tat.transaction_type  WHEN 1 THEN 'Read/write transaction'
                                                    WHEN 2 THEN 'Read-only transaction'
                                                    WHEN 3 THEN 'System transaction'
                                                    WHEN 4 THEN 'Distributed transaction' END
    , input_buffer = ib.event_info, tat.transaction_uow     
    , transaction_state  = CASE tat.transaction_state    
                WHEN 0 THEN 'The transaction has not been completely initialized yet.'
                WHEN 1 THEN 'The transaction has been initialized but has not started.'
                WHEN 2 THEN 'The transaction is active - has not been committed or rolled back.'
                WHEN 3 THEN 'The transaction has ended. This is used for read-only transactions.'
                WHEN 4 THEN 'The commit process has been initiated on the distributed transaction.'
                WHEN 5 THEN 'The transaction is in a prepared state and waiting resolution.'
                WHEN 6 THEN 'The transaction has been committed.'
                WHEN 7 THEN 'The transaction is being rolled back.'
                WHEN 8 THEN 'The transaction has been rolled back.' END 
    , transaction_name = tat.name, request_status = r.status
    , azure_dtc_state = CASE tat.dtc_state 
                        WHEN 1 THEN 'ACTIVE'
                        WHEN 2 THEN 'PREPARED'
                        WHEN 3 THEN 'COMMITTED'
                        WHEN 4 THEN 'ABORTED'
                        WHEN 5 THEN 'RECOVERED' END
    , tst.is_user_transaction, tst.is_local
    , session_open_transaction_count = tst.open_transaction_count  
    , s.host_name, s.program_name, s.client_interface_name, s.login_name, s.is_user_process
    FROM sys.dm_tran_active_transactions tat 
    INNER JOIN sys.dm_tran_session_transactions tst  on tat.transaction_id = tst.transaction_id
    INNER JOIN Sys.dm_exec_sessions s on s.session_id = tst.session_id 
    LEFT OUTER JOIN sys.dm_exec_requests r on r.session_id = s.session_id
    CROSS APPLY sys.dm_exec_input_buffer(s.session_id, null) AS ib;
    ```

    -   他の列

        [sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql) と [sys.dm_exec_request](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) の残りの列でも、問題の原因に関する分析情報を得ることができます。 これらの有用性は、問題の状況によって異なります。 たとえば、問題が特定のクライアント (ホスト名) から、特定のネットワーク ライブラリ (net_library) でのみ発生しているかどうか、SPID によって送信された最後のバッチが sys.dm_exec_sessions で `last_request_start_time` であった場合、sys.dm_exec_requests で `start_time` を使用して要求が実行されていた時間などを特定できます。


## <a name="common-blocking-scenarios"></a>一般的なブロック シナリオ

下の表に、一般的な現象をそれらの考えられる原因にマップしています。  

`wait_type`、`open_transaction_count`、および `status` 列は [sys.dm_exec_request](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) によって返される情報を示しており、その他の列は [sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql) によって返される可能性があります。 "解決するか" 列は、ブロックが自然に解決されるかどうか、または `KILL` コマンドによってセッションを中止する必要があるかどうかを示しています。 詳細については、「[KILL (Transact-SQL)](/sql/t-sql/language-elements/kill-transact-sql)」を参照してください。

| シナリオ | Waittype | Open_Tran | Status | 解決するか | その他の現象 |  
|:-|:-|:-|:-|:-|:-|--|
| 1 | NOT NULL | >= 0 | 実行可能 | はい。クエリが終了したとき。 | sys.dm_exec_sessions では、**reads**、**cpu_time**、**memory_usage** 列は時間の経過と共に増加します。 完了したときのクエリの実行時間は長くなります。 |
| 2 | NULL | \>0 | 休止中 | いいえ。ただし、SPID は中止できます。 | この SPID の拡張イベントセッションに、クエリ タイムアウトまたはキャンセルが発生したことを示す注意シグナルが表示される可能性があります。 |
| 3 | NULL | \>= 0 | 実行可能 | いいえ。 クライアントによってすべての行がフェッチされるか、接続が閉じられるまで、解決されません。 SPID は中止できますが、最大で 30 秒かかることがあります。 | open_transaction_count = 0 で、トランザクション分離レベルが既定 (READ COMMMITTED) である間、SPID がロックを保持している場合、これが原因である可能性があります。 |  
| 4 | 場合により異なる | \>= 0 | 実行可能 | いいえ。 クライアントによってクエリが取り消されるか、接続を閉じられるまで、解決されません。 SPID は中止できますが、最大で 30 秒かかることがあります。 | ブロック チェーンのヘッドにある SPID の sys.dm_exec_sessions の **hostname** 列は、それによってブロックされている SPID の 1 つと同じになります。 |  
| 5 | NULL | \>0 | ロールバック | はい。 | この SPID の拡張イベント セッションで、クエリ タイムアウトまたはキャンセルが発生したか、または単にロールバック ステートメントが発行されたことを示す注意シグナルが表示される可能性があります。 |  
| 6 | NULL | \>0 | 休止中 | 最終的に。 Windows NT によって、セッションがアクティブでなくなったと判断されると、Azure SQL Database 接続が切断されます。 | sys.dm_exec_sessions の `last_request_start_time` 値は、現在の時刻よりもはるかに前です。 |

## <a name="detailed-blocking-scenarios"></a>ブロックのシナリオ (詳述)

1.  実行時間が長い通常実行行されるクエリに原因があるブロック

    **解決方法**:この種類のブロックの問題の解決方法は、クエリを最適化する方法を探すことです。 実際に、このクラスのブロックの問題は、単にパフォーマンスの問題である場合があるため、そのようなものとして追跡する必要があります。 特定の実行速度の遅いクエリのトラブルシューティングについては、[SQL Server の実行速度の遅いクエリのトラブルシューティング方法](/troubleshoot/sql/performance/troubleshoot-slow-running-queries)に関するページを参照してください。 詳細については、「[パフォーマンスの監視とチューニング](/sql/relational-databases/performance/monitor-and-tune-for-performance)」を参照してください。 

    SSMS の[クエリ ストア](/sql/relational-databases/performance/best-practice-with-the-query-store)からのレポートも、最もコストがかかるクエリ、最適でない実行プランを特定するために、強く推奨される貴重なツールです。 また、Azure portal の[インテリジェント パフォーマンス](intelligent-insights-overview.md)のセクションで、[Query Performance Insight](query-performance-insight-use.md) など、Azure SQL データベースについて確認してください。

    他のユーザーをブロックし、最適化できない実行時間の長いクエリがある場合は、それを OLTP 環境から専用のレポート システム、[データベースの同期読み取り専用レプリカ](read-scale-out.md)に移動することを検討してください。

1.  コミットされていないトランザクションがある休止中の SPID に原因があるブロック

    この種類のブロックは、多くの場合に、休止中またはコマンドを待機している SPID によって識別でき、さらにそれらのトランザクション入れ子レベル (`@@TRANCOUNT`、sys.dm_exec_requests からの `open_transaction_count`) が 0 よりも大きくなります。 これは、アプリケーションでクエリのタイムアウトが発生した場合、または必要な数の ROLLBACK ステートメントや COMMIT ステートメントを発行せずに、キャンセルが発行された場合に発生する可能性があります。 SPID によって、クエリのタイムアウトやキャンセルが受信されると、現在のクエリとバッチが終了しますが、トランザクションが自動的にロールバックされたり、コミットされたりしません。 Azure SQL Database では、1 つのクエリが取り消されたためにトランザクション全体をロールバックする必要があることを想定できないため、この責任はアプリケーションにあります。 クエリのタイムアウトやキャンセルは、拡張イベント セッションで SPID の ATTENTION シグナル イベントとして表示されます。

    コミットされていない明示的なトランザクションを明らかにするには、次のクエリを実行します。

    ```sql
    CREATE TABLE #test (col1 INT);
    INSERT INTO #test SELECT 1;
    BEGIN TRAN
    UPDATE #test SET col1 = 2 where col1 = 1;
    ```

    次に、同じウィンドウでこのクエリを実行します。
    ```sql
    SELECT @@TRANCOUNT;
    ROLLBACK TRAN
    DROP TABLE #test;
    ```

    2 つ目のクエリの出力は、トランザクションの入れ子レベルが 1 であることを示しています。 トランザクションで取得されたすべてのロックは、トランザクションがコミットまたはロールバックされるまでまだ保持されます。 アプリケーションによって、明示的にトランザクションが開かれ、コミットされる場合、通信またはその他のエラーによって、セッションとそのトランザクションが開いた状態のままになる可能性があります。 

    この記事で先に紹介したスクリプトを sys.dm_tran_active_transactions に基づいて使用し、インスタンス全体で現在コミットされていないトランザクションを特定します。

    **解決方法**:

     -   さらに、このクラスのブロックの問題は、パフォーマンスの問題である場合もあるため、そのようなものとして追跡する必要があります。 クエリの実行時間を短縮できる場合、クエリのタイムアウトやキャンセルが発生しなくなることがあります。 アプリケーションで、タイムアウトやキャンセルのシナリオが発生した場合にそれらを処理できることが重要ですが、クエリのパフォーマンスを調べることによってメリットが得られる場合もあります。 
     
     -    アプリケーションでは、トランザクションの入れ子レベルを適切に管理する必要があります。そうしないと、このように、クエリのキャンセル後にブロックの問題が発生する可能性があります。 以下、具体例に沿って説明します。  

            *    クライアント アプリケーションで、トランザクションが開いていると思われない場合でも、何らかのエラーの発生後に、クライアント アプリケーションの エラーハンドラーで、`IF @@TRANCOUNT > 0 ROLLBACK TRAN` を実行します。 開いているトランザクションの確認が必要です。バッチ処理中に呼び出されたストアド プロシージャによって、クライアント アプリケーションの認識なく、トランザクションが開始された可能性があるためです。 クエリの取り消しなど、特定の条件によって、現在のステートメントを過ぎたプロシージャの実行が妨げられるため、プロシージャに `IF @@ERROR <> 0` をチェックしてトランザクションを中止するロジックがあったとしても、そのような場合に、このロールバック コードが実行されません。  
            *    Web ベースのアプリケーションなど、接続を開き、接続をプールに解放するまでに少数のクエリを実行するアプリケーションで、接続プールが使用されている場合、接続プールを一時的に無効にすることで、クライアント アプリケーションがエラーを適切に処理するように変更するまで、問題を軽減するのに役立つ可能性があります。 接続プールを無効にすると、接続を解放することで、Azure SQL Database 接続の物理的な切断が発生し、サーバーによって開いているトランザクションのロールバックが行われます。  
            *    接続に対して、またはトランザクションを開始し、エラーの発生後にクリーンアップされないストアド プロシージャで、`SET XACT_ABORT ON` を使用します。 実行時エラーが発生した場合、この設定により、開いているトランザクションが中止され、クライアントに制御が返されます。 詳しくは、「[SET XACT_ABORT (Transact-SQL)](/sql/t-sql/statements/set-xact-abort-transact-sql)」をご覧ください。

    > [!NOTE]
    > 接続は、接続プールから再利用されるまでリセットされないため、ユーザーはトランザクションを開いて、その後接続プールに接続を解放することができますが、数秒間再利用されないことがあり、その間トランザクションが開いたままになる場合があります。 接続が再利用されない場合、接続がタイムアウトし、接続プールから削除されると、トランザクションが中止されます。 このため、クライアント アプリケーションでは、エラー ハンドラーでトランザクションを中止するか、`SET XACT_ABORT ON` を使用して、この遅延の可能性を回避することが最適です。

    > [!CAUTION]
    > エラーが発生したステートメントに続く次の `SET XACT_ABORT ON` T-SQL ステートメントは実行されません。 これは、既存のコードの目的のフローに影響する可能性があります。

1.  対応するクライアント アプリケーションが、完了までにすべての結果行をフェッチしなかった SPID に原因があるブロック

    サーバーにクエリを送信した後、すべてのアプリケーションでは、完了までにすべての結果行を直ちにフェッチする必要があります。 アプリケーションですべての結果行をフェッチしない場合、テーブルへのロックが残され、他のユーザーがブロックされる可能性があります。 サーバーに SQL ステートメントを透過的に送信するアプリケーションを使用している場合、アプリケーションですべての結果行をフェッチする必要があります。 そうしない場合 (およびそうするように構成できない場合)、ブロックの問題を解決できない可能性があります。 問題を回避するには、正常に動作していないアプリケーションを、メインの OLTP データベースから切り離し、レポート データベースまたは意思決定支援データベースに制限することができます。
    
    > [!NOTE]
    > Azure SQL Database に接続するアプリケーションの[再試行ロジックのガイダンス](./troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors)を参照してください。 
    
    **解決方法**:完了までに結果のすべての行をフェッチするように、アプリケーションを書き直す必要があります。 これにより、サーバー側ページングを実行するクエリの [ORDER BY 句での OFFSET および FETCH ](/sql/t-sql/queries/select-order-by-clause-transact-sql#using-offset-and-fetch-to-limit-the-rows-returned)の使用が妨げられるわけではありません。

1.  ロールバック状態のセッションに原因があるブロック

    中止 (KILL) された、またはユーザー定義トランザクションの外部で取り消されたデータ変更クエリはロールバックされます。 これは、クライアント ネットワーク セッションの切断の副作用として、または要求がデッドロックの犠牲者として選択された場合にも発生することがあります。 これは多くの場合、sys.dm_exec_requests の出力を観察することによって識別できます。これは ROLLBACK **コマンド** を示している場合があり、**percent_complete 列** に進行状況が示されている可能性があります。 

    2019 年に導入された[高速データベース復旧機能](../accelerated-database-recovery.md)のため、時間のかかるロールバックはめったに発生しないはずです。
    
    **解決方法**:SPID によって、行われた変更のロールバックが完了するまで待ちます。 

    この状況を回避するには、OLTP システムでビジー時間中に、大規模なバッチ書き込み操作やインデックス作成やメンテナンス操作を実行しないでください。 可能であれば、そのような操作はアクティビティが少ない期間に実行します。

1.  孤立した接続に原因があるブロック

    クライアント アプリケーションでエラーがトラップされた場合、またはクライアント ワークステーションが再起動された場合、一部の条件下で、サーバーへのネットワーク セッションが直ちに取り消されないことがあります。 Azure SQL Database の観点から、クライアントはまだ存在しているように見え、取得されたロックが引き続き保持される場合があります。 詳細については、[SQL Server の孤立した接続のトラブルシューティング方法](/sql/t-sql/language-elements/kill-transact-sql#remarks)に関するページを参照してください。 

    **解決方法**:クライアント アプリケーションで、リソースを適切にクリーンアップせずに切断した場合、`KILL` コマンドを使用して、SPID を終了できます。 `KILL` コマンドは、SPID 値を入力として受け取ります。 たとえば SPID 99 を強制終了するには、次のコマンドを発行します。

    ```sql
    KILL 99
    ```

## <a name="see-also"></a>関連項目

* [Azure SQL Database と Azure SQL Managed Instance での監視とパフォーマンス チューニング](./monitor-tune-overview.md)
* [クエリ ストアを使用したパフォーマンスの監視](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)
* [トランザクションのロックおよび行のバージョン管理ガイド](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide)
* [SET TRANSACTION ISOLATION LEVEL](/sql/t-sql/statements/set-transaction-isolation-level-transact-sql)
* [クイック スタート:SQL Server の拡張イベント](/sql/relational-databases/extended-events/quick-start-extended-events-in-sql-server)
* [AI を使用してデータベース パフォーマンスの監視とトラブルシューティングを行う Intelligent Insights](intelligent-insights-overview.md)

## <a name="learn-more"></a>詳細情報

* [Azure SQL Database: 自動チューニングによるパフォーマンス チューニングの向上](https://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Database-Improving-Performance-Tuning-with-Automatic-Tuning)
* [自動チューニングによる Azure SQL Database パフォーマンスの向上](https://channel9.msdn.com/Shows/Azure-Friday/Improve-Azure-SQL-Database-Performance-with-Automatic-Tuning)
* [Azure SQL で一貫したパフォーマンスを提供する](/learn/modules/azure-sql-performance/)
* [Azure SQL Database および Azure SQL Managed Instance の接続に関する問題とその他のエラーのトラブルシューティング](troubleshoot-common-errors-issues.md)
* [Transient Fault Handling (一時的な障害の処理)](/aspnet/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/transient-fault-handling)
