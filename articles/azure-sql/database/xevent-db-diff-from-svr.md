---
title: 拡張イベント
description: Azure SQL Database での拡張イベント (XEvents) について、またイベント セッションが Microsoft SQL Server におけるイベント セッションと若干異なる点について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: reference
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.date: 12/19/2018
ms.openlocfilehash: 139673e46421aa0dc19298697872fbff5fe587af
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96501211"
---
# <a name="extended-events-in-azure-sql-database"></a>Azure SQL Database での拡張イベント 
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[!INCLUDE [sql-database-xevents-selectors-1-include](../../../includes/sql-database-xevents-selectors-1-include.md)]

Azure SQL Database での拡張イベントの機能セットは SQL Server および Azure SQL Managed Instance における機能の堅牢なサブセットです。

*XEvents* は「拡張イベント」の非公式のニックネームで、ブログや他の非公式な場所で使われます。

拡張イベントの詳細については、以下を参照してください。

- [クイック スタート:SQL Server の拡張イベント](/sql/relational-databases/extended-events/quick-start-extended-events-in-sql-server)
- [拡張イベント](/sql/relational-databases/extended-events/extended-events)

## <a name="prerequisites"></a>前提条件

このトピックは、以下の知識をお持ちのユーザーを想定しています。

- [Azure SQL Database](https://azure.microsoft.com/services/sql-database/)
- [拡張イベント](/sql/relational-databases/extended-events/extended-events)

- 拡張イベントに関するドキュメントの大部分は、SQL Server、Azure SQL Database、および Azure SQL Managed Instance に適用されます。

イベント ファイルを [ターゲット](#AzureXEventsTargets)として選択する際、以下の項目についての知識が役立ちます。

- [Azure Storage サービス](https://azure.microsoft.com/services/storage/)

- [Azure Storage での Azure PowerShell](/powershell/module/az.storage/)

## <a name="code-samples"></a>コード サンプル

関連するトピックで次の 2 つのコード サンプルを提供します。

- [Azure SQL Database での拡張イベント向けリング バッファー ターゲット コード](xevent-code-ring-buffer.md)

  - 短く単純な Transact-SQL スクリプト。
  - コード サンプルのトピックで強調しているように、リング バッファー ターゲットの作業が完了したら、alter-drop `ALTER EVENT SESSION ... ON DATABASE DROP TARGET ...;` ステートメントの実行によりそのリソースを解放する必要があります。 後ほど、 `ALTER EVENT SESSION ... ON DATABASE ADD TARGET ...`によりリング バッファーの他のインスタンスを追加できます。

- [Azure SQL Database の拡張イベントのためのイベント ファイル ターゲット コード](xevent-code-event-file.md)

  - フェーズ 1 は PowerShell で、Azure Storage コンテナーを作成します。
  - フェーズ 2 は Trasact-SQL で、Azure Storage コンテナーを使用します。

## <a name="transact-sql-differences"></a>Transact-SQL の相違点

- [CREATE EVENT SESSION](/sql/t-sql/statements/create-event-session-transact-sql) コマンドを SQL Server で実行する際は、 **ON SERVER** 句を使用します。 ところが、Azure SQL Database では **ON DATABASE** 句を使用します。
- **ON DATABASE** 句も [ALTER EVENT SESSION](/sql/t-sql/statements/alter-event-session-transact-sql) および [DROP EVENT SESSION Transact-SQL](/sql/t-sql/statements/drop-event-session-transact-sql) コマンドに適用されます。

- **CREATE EVENT SESSION** または **ALTER EVENT SESSION** ステートメントで **STARTUP_STATE = ON** のイベント セッション オプションを含ませるのがベスト プラクティスです。
  - **= ON** 値は、フェールオーバーに伴う論理データベース再構成の後の自動再起動をサポートします。

## <a name="new-catalog-views"></a>新しいカタログ ビュー

拡張イベント機能をサポートする [カタログ ビュー](/sql/relational-databases/system-catalog-views/catalog-views-transact-sql)がいくつかあります。 カタログ ビューでは、現在のデータベースにおけるユーザー作成のイベント セッションの *メタデータまたは定義* がわかります。 ビューでは、アクティブなイベント セッションのインスタンスについてはわかりません。

| カタログ ビューの名前<br/>カタログ ビュー | 説明 |
|:--- |:--- |
| **sys.database_event_session_actions** |イベント セッションの各イベントのアクションごとに 1 行のデータを返します。 |
| **sys.database_event_session_events** |イベント セッションのイベントごとに行を返します。 |
| **sys.database_event_session_fields** |イベントとターゲットに明示的に設定されたカスタマイズ可能な列ごとに行を返します。 |
| **sys.database_event_session_targets** |イベント セッションのイベント ターゲットごとに 1 行のデータを返します。 |
| **sys.database_event_sessions** |データベース内のイベント セッションごとに行を返します。 |

Microsoft SQL Server では、同様のカタログ ビュー名には *.database\_* ではなく、 *.server\_* が含まれています。 名前のパターンは、**sys.server_event_%** のようになっています。

## <a name="new-dynamic-management-views-dmvs"></a>新しい動的管理ビュー [(DMV)](/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views)

Azure SQL Database には、拡張イベントをサポートする [動的管理ビュー (DMV)](/sql/relational-databases/system-dynamic-management-views/extended-events-dynamic-management-views) があります。 DMV では *アクティブな* イベント セッションについて参照できます。

| DMV の名前 | 説明 |
|:--- |:--- |
| **sys.dm_xe_database_session_event_actions** |イベント セッション アクションに関する情報を返します。 |
| **sys.dm_xe_database_session_events** |セッション イベントに関する情報を返します。 |
| **sys.dm_xe_database_session_object_columns** |セッションにバインドされたオブジェクトの構成値を示します。 |
| **sys.dm_xe_database_session_targets** |セッション ターゲットに関する情報を返します。 |
| **sys.dm_xe_database_sessions** |現在のデータベースを対象としたイベント セッションごとに行を返します。 |

Microsoft SQL Server では、同様のカタログ ビューは次のように名前から *\_database* 部分を削除した命名がなされています。

- **sys.dm_xe_sessions** 名前の代わり<br/>**sys.dm_xe_database_sessions**

### <a name="dmvs-common-to-both"></a>両者に共通の DMV

拡張イベントについては、次のような Azure SQL Database、Azure SQL Managed Instance、および Microsoft SQL Server に共通した DMV も存在します。

- **sys.dm_xe_map_values**
- **sys.dm_xe_object_columns**
- **sys.dm_xe_objects**
- **sys.dm_xe_packages**

<a name="sqlfindseventsactionstargets" id="sqlfindseventsactionstargets"></a>

## <a name="find-the-available-extended-events-actions-and-targets"></a>使用可能な拡張イベント、アクション、ターゲットを検索

簡単な SQL **SELECT** を実行して、使用可能なイベント、アクション、ターゲットのリストを取得できます。

```sql
SELECT
        o.object_type,
        p.name         AS [package_name],
        o.name         AS [db_object_name],
        o.description  AS [db_obj_description]
    FROM
                   sys.dm_xe_objects  AS o
        INNER JOIN sys.dm_xe_packages AS p  ON p.guid = o.package_guid
    WHERE
        o.object_type in
            (
            'action',  'event',  'target'
            )
    ORDER BY
        o.object_type,
        p.name,
        o.name;
```

<a name="AzureXEventsTargets" id="AzureXEventsTargets"></a> &nbsp;

## <a name="targets-for-your-azure-sql-database-event-sessions"></a>Azure SQL Database イベント セッションのターゲット

Azure SQL Database のイベント セッションから結果を取得できるターゲットを次に挙げます。

- [リング バッファー ターゲット](/previous-versions/sql/sql-server-2016/bb630339(v=sql.130)) - イベント データを一時的にメモリに保持します。
- [イベント カウンター ターゲット](/previous-versions/sql/sql-server-2016/ff878025(v=sql.130)) - 拡張イベント セッションの間に発生したすべてのイベントをカウントします。
- [イベント ファイル ターゲット](/previous-versions/sql/sql-server-2016/ff878115(v=sql.130)) - Azure Storage コンテナーに完了したバッファーを書き込みます。

[Event Tracing for Windows (ETW)](/dotnet/framework/wcf/samples/etw-tracing) API は Azure SQL Database の拡張イベントでは使用できません。

## <a name="restrictions"></a>制限

Azure SQL Database のクラウド環境にふさわしいセキュリティ関連の相違点を次にいくつか挙げます。

- 拡張イベントは単一テナントの分離モデルが元になっています。 あるデータベースのイベント セッションが他のデータベースからのデータやイベントにアクセスすることはできません。
- **マスター** データベースのコンテキストで、**CREATE EVENT SESSION** ステートメントを実行することはできません。

## <a name="permission-model"></a>権限モデル

**CREATE EVENT SESSION** ステートメントを実行するには、データベースで **コントロール** 権限が必要です。 データベース所有者 (dbo) には **コントロール** 権限があります。

### <a name="storage-container-authorizations"></a>ストレージ コンテナーの承認

Azure Storage コンテナーのために生成した SAS トークンには、権限として **rwl** を指定する必要があります。 **rwl** 値により次のアクセスが許可されます。

- Read
- Write
- List

## <a name="performance-considerations"></a>パフォーマンスに関する考慮事項

システム全体にとって不健全と言える程、拡張イベントの使い過ぎによるアクティブメモリの蓄積が起きるシナリオがあります。 そのため、Azure SQL Database はイベント セッションによって蓄積され得るアクティブ メモリの量に対する制限を動的に設定、調整します。 多くの要因が動的な計算に影響します。

メモリの最大量が使用されたというエラー メッセージを受信した場合、次の修正措置を実行することができます。

- 同時実行するイベント セッションを減少させる。
- イベント セッションの **CREATE** と **ALTER** ステートメントにより、**MAX\_MEMORY** 句で指定するメモリ量を減少させる。

### <a name="network-latency"></a>ネットワーク待ち時間

Azure Storage BLOB にデータを保持する際に、 **イベント ファイル** ターゲットでネットワークの遅延や障害が発生することがあります。 ネットワーク通信の完了を待機する際に、Azure SQL Database の他のイベントが遅延することがあります。 この遅延によってワークロードが遅くなる可能性があります。

- このパフォーマンス リスクを防ぐため、イベント セッション定義の **NO_EVENT_LOSS** に対して **EVENT_RETENTION_MODE** オプションの設定を避けてください。

## <a name="related-links"></a>関連リンク

- [Azure Storage における Azure PowerShell の使用](/powershell/module/az.storage/)。
- [Azure Storage コマンドレット](/powershell/module/Azure.Storage)
- [Azure Storage での Azure PowerShell の使用](/powershell/module/az.storage/)
- [.NET から BLOB ストレージを使用する方法](../../storage/blobs/storage-quickstart-blobs-dotnet.md)
- [CREATE CREDENTIAL (Transact-SQL)](/sql/t-sql/statements/create-credential-transact-sql)
- [CREATE EVENT SESSION (Transact-SQL)](/sql/t-sql/statements/create-event-session-transact-sql)
- [Jonathan Kehayias の Microsoft SQL Server の拡張イベントに関するブログ投稿](https://www.sqlskills.com/blogs/jonathan/category/extended-events/)
- パラメーターを Azure SQL Database に絞り込んだ、Azure "*サービスの更新情報*" の Web ページ。
  - [https://azure.microsoft.com/updates/?service=sql-database](https://azure.microsoft.com/updates/?service=sql-database)

<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](/sql/relational-databases/extended-events/determine-which-queries-are-holding-locks)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](/sql/relational-databases/extended-events/find-the-objects-that-have-the-most-locks-taken-on-them)
-->