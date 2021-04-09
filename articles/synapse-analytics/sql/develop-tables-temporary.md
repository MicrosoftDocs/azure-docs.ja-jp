---
title: Synapse SQL の一時テーブルを使用する
description: Synapse SQL の一時テーブルの基本的な利用方法について説明します。
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 06faa1da71331c299245a93af96166880e7732de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96451772"
---
# <a name="temporary-tables-in-synapse-sql"></a>Synapse SQL の一時テーブル

この記事では、Synapse SQL 内のセッション レベルの一時テーブルの原則を中心に、一時テーブルの基本的な利用方法について説明します。 

専用 SQL プールとサーバーレス SQL プールの両方のリソースで、一時テーブルを利用できます。 サーバーレス SQL プールにはいくつかの制限事項があり、それについては、この記事の最後で説明しています。 

## <a name="temporary-tables"></a>一時テーブル

特に、中間結果が一時的なものである変換中にデータを処理する場合に、一時テーブルが役立ちます。 Synapse SQL では、一時テーブルはセッション レベルで存在します。  一時テーブルは、それが作成されたセッションにのみ表示されます。 そのため、そのセッションがログオフされたときに自動的に削除されます。 

## <a name="temporary-tables-in-dedicated-sql-pool"></a>専用 SQL プール内の一時テーブル

専用 SQL プール リソースでは、一時テーブルの結果がリモート ストレージではなくローカル ストレージに書き込まれるため、パフォーマンス上の利点があります。

### <a name="create-a-temporary-table"></a>一時テーブルを作成する

一時テーブルは、テーブル名にプレフィックス `#` を付けることで作成できます。  次に例を示します。

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

### <a name="drop-temporary-tables"></a>一時テーブルを削除する

新しいセッションが作成されたとき、一時テーブルは存在しません。  ただし、同じ名前で一時テーブルを作成する同じストアド プロシージャを呼び出す場合、`CREATE TABLE` ステートメントを正常に実行するために、`DROP` による簡単な既存チェックを使用します。 

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END
```

コードの一貫性のために、テーブルと一時テーブルの両方にこのパターンを利用することが推奨されます。  また、一時テーブルの利用を終えたら、`DROP TABLE` を使用してそれを削除することもお勧めします。  

ストアド プロシージャの開発では、一般的に、オブジェクトが消去されるように、プロシージャの終わりに削除コマンドがバンドルされています。

```sql
DROP TABLE #stats_ddl
```

### <a name="modularize-code"></a>コードをモジュール化する

一時テーブルはユーザー セッション内のどこでも使用できます。 この機能を利用して、アプリケーション コードをモジュール化することができます。  試してみるには、次のストアド プロシージャで、データベース内のすべての統計を統計名で更新する DDL を生成します。

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

この段階で、発生した唯一のアクションは、#stats_ddl 一時テーブルを生成するストアド プロシージャの作成です。  #stats_ddl が既に存在する場合は、このストアド プロシージャによって削除されます。 この削除により、一時テーブルがセッション内で複数回実行された場合に失敗することがなくなります。  

ストアド プロシージャの最後に `DROP TABLE` がないため、ストアド プロシージャが完了したときに、作成されたテーブルはそのまま残り、ストアド プロシージャの外部で読み取ることができます。  

他の SQL Server データベースとは対照的に、Synapse SQL では、一時テーブルを作成したプロシージャの外部でその一時テーブルを使用できます。  専用 SQL プールを使用して作成された一時テーブルは、セッション内の **どこでも** 使用できます。 その結果、以下のサンプルに示すように、コードのモジュール性が高まり、コードを管理しやすくなります。

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

### <a name="temporary-table-limitations"></a>一時テーブルの制限事項

専用 SQL プールには、一時テーブルの実装上の制限事項がいくつかあります。

- セッションを範囲とした一時テーブルのみがサポートされています。  グローバル一時テーブルはサポートされていません。
- 一時テーブルでビューを作成することはできません。
- 一時テーブルは、ハッシュまたはラウンド ロビン ディストリビューションでのみ作成できます。  レプリケートされた一時テーブルのディストリビューションはサポートされていません。 

## <a name="temporary-tables-in-serverless-sql-pool"></a>サーバーレス SQL プール内の一時テーブル

サーバーレス SQL プール内の一時テーブルはサポートされていますが、その使用法は制限されています。 一時テーブルは、ファイルを対象とするクエリでは使用できません。 

たとえば、ストレージ内のファイルからのデータと一時テーブルを結合することはできません。 一時テーブルの数は 100 個に制限されており、合計サイズは 100 MB に制限されています。

## <a name="next-steps"></a>次のステップ

テーブルの開発について詳しくは、[Synapse SQL リソースを使用したテーブルの設計](develop-tables-overview.md)に関する記事をご覧ください。

