---
title: SQL Data Warehouse の一時テーブル | Microsoft Docs
description: セッション レベルの一時テーブルの原則を中心に、一時テーブルの基本的な利用方法について説明します。
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/01/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: e43e52e56ec7abbf5d8eb879defef54bd7d50658
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479828"
---
# <a name="temporary-tables-in-sql-data-warehouse"></a>SQL Data Warehouse の一時テーブル
この記事では、セッション レベルの一時テーブルの原則を中心に、一時テーブルの基本的な利用方法について説明します。 この記事の情報に従うとコードをモジュール化できるため、コードの再利用性が向上し、保守が容易になります。

## <a name="what-are-temporary-tables"></a>一時テーブルとは
特に、中間結果が一時的なものである変換中にデータを処理する場合に、一時テーブルが役立ちます。 SQL Data Warehouse では、一時テーブルはセッション レベルで存在します。  作成されたセッションのみで参照でき、セッションをログオフすると自動的に削除されます。  一時テーブルは、リモート ストレージではなくローカル ストレージに結果が書き込まれるため、パフォーマンス上の利点があります。

## <a name="create-a-temporary-table"></a>一時テーブルを作成する
一時テーブルは、テーブル名にプレフィックス `#` を付けることで作成できます。  例:

```sql
CREATE TABLE #stats_ddl
(
    [schema_name]        NVARCHAR(128) NOT NULL
,    [table_name]            NVARCHAR(128) NOT NULL
,    [stats_name]            NVARCHAR(128) NOT NULL
,    [stats_is_filtered]     BIT           NOT NULL
,    [seq_nmbr]              BIGINT        NOT NULL
,    [two_part_name]         NVARCHAR(260) NOT NULL
,    [three_part_name]       NVARCHAR(400) NOT NULL
)
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
,    HEAP
)
```

一時テーブルは、次のようにまったく同じ手法で `CTAS` を利用して作成することもできます。

```sql
CREATE TABLE #stats_ddl
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
,    HEAP
)
AS
(
SELECT
        sm.[name]                                                                AS [schema_name]
,        tb.[name]                                                                AS [table_name]
,        st.[name]                                                                AS [stats_name]
,        st.[has_filter]                                                            AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,                                 QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,        QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM    sys.objects            AS ob
JOIN    sys.stats            AS st    ON    ob.[object_id]        = st.[object_id]
JOIN    sys.stats_columns    AS sc    ON    st.[stats_id]        = sc.[stats_id]
                                    AND st.[object_id]        = sc.[object_id]
JOIN    sys.columns            AS co    ON    sc.[column_id]        = co.[column_id]
                                    AND    sc.[object_id]        = co.[object_id]
JOIN    sys.tables            AS tb    ON    co.[object_id]        = tb.[object_id]
JOIN    sys.schemas            AS sm    ON    tb.[schema_id]        = sm.[schema_id]
WHERE    1=1
AND        st.[user_created]   = 1
GROUP BY
        sm.[name]
,        tb.[name]
,        st.[name]
,        st.[filter_definition]
,        st.[has_filter]
)
;
``` 

> [!NOTE]
> `CTAS` は強力なコマンドであり、トランザクション ログ領域を効率的に利用するという長所があります。 
> 
> 

## <a name="dropping-temporary-tables"></a>一時テーブルを削除する
新しいセッションが作成されたとき、一時テーブルは存在しません。  ただし、同じストアド プロシージャを呼び出している場合、同じ名前で一時テーブルが作成されるため、`CREATE TABLE` ステートメントを正常に実行するには、次の例のように `DROP` を使用した簡単な既存チェックを使用できます。

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END
```

コードの一貫性のために、テーブルと一時テーブルの両方にこのパターンを利用することが推奨されます。  また、コードで一時テーブルの利用を終えたら、 `DROP TABLE` でそれを削除することもお勧めします。  ストアド プロシージャの開発では、一般的に、オブジェクトが消去されるように、プロシージャの終わりに削除コマンドがバンドルされています。

```sql
DROP TABLE #stats_ddl
```

## <a name="modularizing-code"></a>コードのモジュール化
一時テーブルはユーザー セッションのどこでも表示できるため、アプリケーション コードをモジュール化できます。  たとえば、次のストアド プロシージャは、統計の名前でデータベース内のすべての統計を更新する DDL を生成します。

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_update_stats]
(   @update_type    tinyint -- 1 default 2 fullscan 3 sample 4 resample
    ,@sample_pct     tinyint
)
AS

IF @update_type NOT IN (1,2,3,4)
BEGIN;
    THROW 151000,'Invalid value for @update_type parameter. Valid range 1 (default), 2 (fullscan), 3 (sample) or 4 (resample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END

CREATE TABLE #stats_ddl
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
)
AS
(
SELECT
        sm.[name]                                                                AS [schema_name]
,        tb.[name]                                                                AS [table_name]
,        st.[name]                                                                AS [stats_name]
,        st.[has_filter]                                                            AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,                                 QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,        QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM    sys.objects            AS ob
JOIN    sys.stats            AS st    ON    ob.[object_id]        = st.[object_id]
JOIN    sys.stats_columns    AS sc    ON    st.[stats_id]        = sc.[stats_id]
                                    AND st.[object_id]        = sc.[object_id]
JOIN    sys.columns            AS co    ON    sc.[column_id]        = co.[column_id]
                                    AND    sc.[object_id]        = co.[object_id]
JOIN    sys.tables            AS tb    ON    co.[object_id]        = tb.[object_id]
JOIN    sys.schemas            AS sm    ON    tb.[schema_id]        = sm.[schema_id]
WHERE    1=1
AND        st.[user_created]   = 1
GROUP BY
        sm.[name]
,        tb.[name]
,        st.[name]
,        st.[filter_definition]
,        st.[has_filter]
)
SELECT
    CASE @update_type
    WHEN 1
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+');'
    WHEN 2
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH FULLSCAN;'
    WHEN 3
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH SAMPLE '+CAST(@sample_pct AS VARCHAR(20))+' PERCENT;'
    WHEN 4
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH RESAMPLE;'
    END AS [update_stats_ddl]
,   [seq_nmbr]
FROM    t1
;
GO
```

この段階で、発生した唯一のアクションが、DDL ステートメントで一時テーブル #stats_ddl を生成するストアド プロシージャの作成です。  セッション内で複数回実行された場合に失敗しないように、既に存在する場合は、このストアド プロシージャが #stats_ddl を削除します。  ただし、ストアド プロシージャの最後に `DROP TABLE` がないため、ストアド プロシージャが実行されると、ストアド プロシージャの外から読み取れるように、作成されたテーブルはそのままになります。  他の SQL Server データベースと異なり、SQL Data Warehouse では、一時テーブルを作成したプロシージャの外部でその一時テーブルを使用できます。  SQL Data Warehouse では、一時テーブルはセッション内の **どこでも** 使用できます。 これにより、次の例のように、コードのモジュール性が高まり、コードを管理しやすくなります。

```sql
EXEC [dbo].[prc_sqldw_update_stats] @update_type = 1, @sample_pct = NULL;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''

WHILE @i <= @t
BEGIN
    SET @s=(SELECT update_stats_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

## <a name="temporary-table-limitations"></a>一時テーブルの制限事項
SQL Data Warehouse では、一時テーブルを実装するときに制限事項がいくつかあります。  現時点では、セッションを範囲とした一時テーブルのみがサポートされています。  グローバル一時テーブルはサポートされていません。  また、一時テーブルでビューを作成することはできません。  一時テーブルは、ハッシュまたはラウンド ロビン ディストリビューションでのみ作成できます。  レプリケートされた一時テーブルのディストリビューションはサポートされていません。 

## <a name="next-steps"></a>次の手順
テーブルの開発に関する詳細については、[テーブルの概要](sql-data-warehouse-tables-overview.md)を参照してください。

