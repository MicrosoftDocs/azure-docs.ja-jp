---
title: XEvent リング バッファーのコード
description: Azure SQL Database で、リング バッファー ターゲットの使用によって簡素化された TRANSACT-SQL のコード サンプルを提供します。
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: PowerShell
ms.topic: sample
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.date: 12/19/2018
ms.openlocfilehash: a646588616b874e40b1ed2a5a0b5e691b075075d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96487305"
---
# <a name="ring-buffer-target-code-for-extended-events-in-azure-sql-database"></a>Azure SQL Database での拡張イベント向けリング バッファー ターゲット コード
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[!INCLUDE [sql-database-xevents-selectors-1-include](../../../includes/sql-database-xevents-selectors-1-include.md)]

テスト中の拡張イベントに関する情報を、最も簡単な方法で取得およびレポートするための完全なコード サンプルが必要です。 拡張イベントのデータの最も簡単なターゲットは、 [リング バッファー ターゲット](/previous-versions/sql/sql-server-2016/bb630339(v=sql.130))です。

このトピックでは、以下を実行する TRANSACT-SQL コードのサンプルについて説明します。

1. 表示させるデータでテーブルを作成する。
2. 既存の拡張イベントのセッション (つまり **sqlserver.sql_statement_starting**) を作成する。

   * イベントは、次の特定の Update 文字列を含む SQL ステートメントに限定される: **statement LIKE ’%UPDATE tabEmployee%’** 。
   * イベントの出力をリング バッファー タイプのターゲット (つまり **package0.ring_buffer**) に送信するよう選択できる。
3. イベント セッションを開始する。
4. 単純な SQL UPDATE ステートメントをいくつか発行する。
5. リング バッファーからイベント出力を取得する SQL SELECT を発行する。

   * **sys.dm_xe_database_session_targets** と他の動的管理ビュー (DMV) を結合する。
6. イベント セッションを停止する。
7. リング バッファー ターゲットを削除して、そのリソースを解放する。
8. イベント セッションとデモ テーブルを削除する。

## <a name="prerequisites"></a>前提条件

* Azure アカウントとサブスクリプション。 [無料試用版](https://azure.microsoft.com/pricing/free-trial/)にサインアップできます。
* テーブルを作成できるデータベース。
  
  * 必要に応じて、数分で [**AdventureWorksLT** デモ データベースを作成](single-database-create-quickstart.md)できる。
* SQL Server Management Studio (ssms.exe)。できれば、最新の月次更新バージョン。
  最新の ssms.exe をダウンロードすることができる。
  
  * 「 [SQL Server Management Studio のダウンロード](/sql/ssms/download-sql-server-management-studio-ssms)」というタイトルのトピック。
  * [ダウンロードへの直接リンク。](https://go.microsoft.com/fwlink/?linkid=616025)

## <a name="code-sample"></a>コード サンプル

わずかな変更を加えると、以下のリング バッファーのコード サンプルを、Azure SQL Database または Microsoft SQL Server のいずれかで実行できます。 異なる点は、手順 5. の FROM 句で使用されるいくつかの動的管理ビュー (DMV) の名前の中に「_database」というノード名があることです。 次に例を示します。

* sys.dm_xe<strong>_database</strong>_session_targets
* sys.dm_xe_session_targets

&nbsp;

```sql
GO
----  Transact-SQL.
---- Step set 1.

SET NOCOUNT ON;
GO


IF EXISTS
    (SELECT * FROM sys.objects
        WHERE type = 'U' and name = 'tabEmployee')
BEGIN
    DROP TABLE tabEmployee;
END
GO


CREATE TABLE tabEmployee
(
    EmployeeGuid         uniqueIdentifier   not null  default newid()  primary key,
    EmployeeId           int                not null  identity(1,1),
    EmployeeKudosCount   int                not null  default 0,
    EmployeeDescr        nvarchar(256)          null
);
GO


INSERT INTO tabEmployee ( EmployeeDescr )
    VALUES ( 'Jane Doe' );
GO

---- Step set 2.


IF EXISTS
    (SELECT * from sys.database_event_sessions
        WHERE name = 'eventsession_gm_azuresqldb51')
BEGIN
    DROP EVENT SESSION eventsession_gm_azuresqldb51
        ON DATABASE;
END
GO


CREATE
    EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    ADD EVENT
        sqlserver.sql_statement_starting
            (
            ACTION (sqlserver.sql_text)
            WHERE statement LIKE '%UPDATE tabEmployee%'
            )
    ADD TARGET
        package0.ring_buffer
            (SET
                max_memory = 500   -- Units of KB.
            );
GO

---- Step set 3.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    STATE = START;
GO

---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
GO

---- Step set 5.


SELECT
    se.name                      AS [session-name],
    ev.event_name,
    ac.action_name,
    st.target_name,
    se.session_source,
    st.target_data,
    CAST(st.target_data AS XML)  AS [target_data_XML]
FROM
               sys.dm_xe_database_session_event_actions  AS ac

    INNER JOIN sys.dm_xe_database_session_events         AS ev  ON ev.event_name = ac.event_name
        AND CAST(ev.event_session_address AS BINARY(8)) = CAST(ac.event_session_address AS BINARY(8))

    INNER JOIN sys.dm_xe_database_session_object_columns AS oc
         ON CAST(oc.event_session_address AS BINARY(8)) = CAST(ac.event_session_address AS BINARY(8))

    INNER JOIN sys.dm_xe_database_session_targets        AS st
         ON CAST(st.event_session_address AS BINARY(8)) = CAST(ac.event_session_address AS BINARY(8))

    INNER JOIN sys.dm_xe_database_sessions               AS se
         ON CAST(ac.event_session_address AS BINARY(8)) = CAST(se.address AS BINARY(8))
WHERE
        oc.column_name = 'occurrence_number'
    AND
        se.name        = 'eventsession_gm_azuresqldb51'
    AND
        ac.action_name = 'sql_text'
ORDER BY
    se.name,
    ev.event_name,
    ac.action_name,
    st.target_name,
    se.session_source
;
GO

---- Step set 6.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    STATE = STOP;
GO

---- Step set 7.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    DROP TARGET package0.ring_buffer;
GO

---- Step set 8.


DROP EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE;
GO

DROP TABLE tabEmployee;
GO
```

&nbsp;

## <a name="ring-buffer-contents"></a>リング バッファーの内容

ここでは、`ssms.exe` を使用してコード サンプルを実行しました。

結果を表示するために、列ヘッダー **target_data_XML** の下のセルをクリックしました。

結果ウィンドウで、列ヘッダー **target_data_XML** の下のセルをクリックしました。 これにより、ssms.exe にもう 1 つのファイル タブが作成され、結果のセルの内容が XML として表示されました。

出力は、次のブロックに示されています。 ここに含まれるのは、2 つの **\<event>** 要素です。

&nbsp;

```
<RingBufferTarget truncated="0" processingTime="0" totalEventsProcessed="2" eventCount="2" droppedCount="0" memoryUsed="1728">
  <event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T15:29:31.317Z">
    <data name="state">
      <type name="statement_starting_state" package="sqlserver" />
      <value>0</value>
      <text>Normal</text>
    </data>
    <data name="line_number">
      <type name="int32" package="package0" />
      <value>7</value>
    </data>
    <data name="offset">
      <type name="int32" package="package0" />
      <value>184</value>
    </data>
    <data name="offset_end">
      <type name="int32" package="package0" />
      <value>328</value>
    </data>
    <data name="statement">
      <type name="unicode_string" package="package0" />
      <value>UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102</value>
    </data>
    <action name="sql_text" package="sqlserver">
      <type name="unicode_string" package="package0" />
      <value>
---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
</value>
    </action>
  </event>
  <event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T15:29:31.327Z">
    <data name="state">
      <type name="statement_starting_state" package="sqlserver" />
      <value>0</value>
      <text>Normal</text>
    </data>
    <data name="line_number">
      <type name="int32" package="package0" />
      <value>10</value>
    </data>
    <data name="offset">
      <type name="int32" package="package0" />
      <value>340</value>
    </data>
    <data name="offset_end">
      <type name="int32" package="package0" />
      <value>486</value>
    </data>
    <data name="statement">
      <type name="unicode_string" package="package0" />
      <value>UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015</value>
    </data>
    <action name="sql_text" package="sqlserver">
      <type name="unicode_string" package="package0" />
      <value>
---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
</value>
    </action>
  </event>
</RingBufferTarget>
```

### <a name="release-resources-held-by-your-ring-buffer"></a>リング バッファーが保持するリソースの解放

リング バッファーでの作業が終わったら、リング バッファーを削除して、次のように **ALTER** リソースを発行することでリソースを解放できます。

```sql
ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    DROP TARGET package0.ring_buffer;
GO
```

イベント セッションの定義は更新されますが、削除はされません。 後で、イベント セッションにリング バッファーの別のインスタンスを追加できます。

```sql
ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    ADD TARGET
        package0.ring_buffer
            (SET
                max_memory = 500   -- Units of KB.
            );
```

## <a name="more-information"></a>詳細情報

Azure SQL Database での拡張イベントに関する主なトピックは次のとおりです。

* [Azure SQL Database での拡張イベントに関する考慮事項](xevent-db-diff-from-svr.md)。この記事では、Microsoft SQL Server と Azure SQL Database の間で異なる拡張イベントの一部の機能を比較します。

拡張イベントの他のコード サンプル トピックは次のリンクから入手可能です。 ただし、対象が Azure SQL Database または Microsoft SQL Server のどちらかを確認するために、サンプルを定期的にチェックする必要があります。 これにより、変更がサンプル実行に十分であるかを判断できます。

* Azure SQL Database のコード サンプル:[Azure SQL Database の拡張イベントのためのイベント ファイル ターゲット コード](xevent-code-event-file.md)

<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](/sql/relational-databases/extended-events/determine-which-queries-are-holding-locks)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](/sql/relational-databases/extended-events/find-the-objects-that-have-the-most-locks-taken-on-them)
-->