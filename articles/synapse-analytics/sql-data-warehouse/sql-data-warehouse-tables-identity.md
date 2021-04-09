---
title: IDENTITY を使用して代理キーを作成する
description: IDENTITY プロパティを使用して専用 SQL プール内のテーブルに代理キーを作成する場合の推奨事項と例。
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 07/20/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 5c620aa60e134379614a905226caa4a66d179fae
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98673537"
---
# <a name="using-identity-to-create-surrogate-keys-using-dedicated-sql-pool-in-azuresynapse-analytics"></a>IDENTITY を使用して、Azure Synapse Analytics の専用 SQL プールで代理キーを作成する

この記事では、IDENTITY プロパティを使用して専用 SQL プール内のテーブルに代理キーを作成する場合の推奨事項と例を紹介します。

## <a name="what-is-a-surrogate-key"></a>代理キーとは

テーブルの代理キーは、各行の一意の識別子を持つ列です。 代理キーはテーブル データからは生成されません。 データ モデラーは、データ ウェアハウス モデルを設計するときに、テーブルに代理キーを作成するのを好みます。 IDENTITY プロパティを使うと、この目的を簡単かつ効果的に達成でき、読み込みのパフォーマンスが影響を受けることもありません。
> [!NOTE]
> Azure Synapse Analytics では、IDENTITY 値は各ディストリビューションで自動的に増加し、他のディストリビューションの IDENTITY 値と重複しません。  Synapse の IDENTITY 値は、ユーザーが明示的に "SET IDENTITY_INSERT ON" と重複する値を挿入するか IDENTITY を再シードする場合は、一意であるとは限りません。 詳細については、「[CREATE TABLE (Transact-SQL) IDENTITY (プロパティ)](/sql/t-sql/statements/create-table-transact-sql-identity-property?view=azure-sqldw-latest&preserve-view=true)」を参照してください。 


## <a name="creating-a-table-with-an-identity-column"></a>IDENTITY 列があるテーブルを作成する

IDENTITY プロパティは、読み込みパフォーマンスに影響を与えずに、専用 SQL プール内のすべてのディストリビューションにスケールアウトするように設計されています。 そのため、IDENTITY の実装はこれらの目標を達成するようになっています。

次のステートメントのような構文を使って、テーブルを最初に作成するときに、IDENTITY プロパティを持つようにテーブルを定義できます。

```sql
CREATE TABLE dbo.T1
(    C1 INT IDENTITY(1,1) NOT NULL
,    C2 INT NULL
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;
```

その後、`INSERT..SELECT` を使ってテーブルを設定します。

以降、このセクションでは、理解を深めるのに役立つ実装の詳細に注目します。  

### <a name="allocation-of-values"></a>値の割り当て

IDENTITY プロパティでは、データ ウェアハウスの分散アーキテクチャにより、サロゲート値が割り当てられる順序は保証されません。 IDENTITY プロパティは、読み込みパフォーマンスに影響を与えずに、専用 SQL プール内のすべてのディストリビューションにスケールアウトするように設計されています。 

次にその例を示します。

```sql
CREATE TABLE dbo.T1
(    C1 INT IDENTITY(1,1)    NOT NULL
,    C2 VARCHAR(30)                NULL
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;

INSERT INTO dbo.T1
VALUES (NULL);

INSERT INTO dbo.T1
VALUES (NULL);

SELECT *
FROM dbo.T1;

DBCC PDW_SHOWSPACEUSED('dbo.T1');
```

この例では、2 つの行はディストリビューション 1 に格納されます。 1 番目の行の代理値は `C1` 列の 1 であり、2 番目の行の代理値は 61 です。 これらの値はどちらも IDENTITY プロパティによって生成されたものです。 ただし、値の割り当ては連続していません。 この動作は仕様です。

### <a name="skewed-data"></a>非対称のデータ

データ型の値の範囲は、ディストリビューション全体に均等に分散されます。 分散テーブルが非対称データによって悪影響を受ける場合、データ型に対して使用可能な値の範囲が早く不足する可能性があります。 たとえば、すべてのデータが最終的に 1 つのディストリビューションに格納される場合、実質的にテーブルはそのデータ型の値の 60 分の 1 にのみアクセスすることになります。 このため、IDENTITY プロパティは `INT` および `BIGINT` データ型だけに制限されます。

### <a name="selectinto"></a>SELECT..INTO

既存の IDENTITY 列を選択して新しいテーブルにすると、次のいずれかの条件が満たされている場合を除き、新しい列は IDENTITY プロパティを継承します。

- SELECT ステートメントに結合が含まれています。
- 複数の SELECT ステートメントが UNION を使用して結合されている。
- IDENTITY 列が SELECT リストに複数回出現する。
- IDENTITY 列が式の一部である。

これらの条件が 1 つでも満たされている場合は、列に IDENTITY プロパティは継承されず、代わりに NOT NULL として作成されます。

### <a name="create-table-as-select"></a>CREATE TABLE AS SELECT

CREATE TABLE AS SELECT (CTAS) は、SELECT..INTO と同じ SQL Server 動作に従います。 ただし、ステートメントの `CREATE TABLE` 部分の列定義で IDENTITY プロパティを指定することはできません。 また、CTAS の `SELECT` 部分で IDENTITY 関数を使うこともできません。 テーブルに値を設定するには、`CREATE TABLE` を使ってテーブルを定義した後、`INSERT..SELECT` で値を設定する必要があります。

## <a name="explicitly-inserting-values-into-an-identity-column"></a>IDENTITY 列に値を明示的に挿入する

専用 SQL プールでは `SET IDENTITY_INSERT <your table> ON|OFF` 構文がサポートされています。 この構文を使って、IDENTITY 列に値を明示的に挿入できます。

多くのデータ モデラーは、ディメンションの特定の行に定義済みの負の値を使うことを好みます。 たとえば、-1 や "unknown member" 行です。

次のスクリプトでは、SET IDENTITY_INSERT を使ってこの行を明示的に追加する方法を示します。

```sql
SET IDENTITY_INSERT dbo.T1 ON;

INSERT INTO dbo.T1
(   C1
,   C2
)
VALUES (-1,'UNKNOWN')
;

SET IDENTITY_INSERT dbo.T1 OFF;

SELECT     *
FROM    dbo.T1
;
```

## <a name="loading-data"></a>データの読み込み

IDENTITY プロパティが存在すると、データ読み込みコードに影響があります。 ここでは、IDENTITY を使ってテーブルにデータを読み込む場合のいくつかの基本的なパターンを示します。

IDENTITY を使ってテーブルにデータを読み込んで代理キーを生成するには、テーブルを作成した後、INSERT..SELECT または INSERT..VALUES を使って読み込みを実行します。

次の例では基本的なパターンを示します。

```sql
--CREATE TABLE with IDENTITY
CREATE TABLE dbo.T1
(    C1 INT IDENTITY(1,1)
,    C2 VARCHAR(30)
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;

--Use INSERT..SELECT to populate the table from an external table
INSERT INTO dbo.T1
(C2)
SELECT     C2
FROM    ext.T1
;

SELECT *
FROM   dbo.T1
;

DBCC PDW_SHOWSPACEUSED('dbo.T1');
```

> [!NOTE]
> 現在は、IDENTITY 列のあるテーブルへのデータの読み込みに、`CREATE TABLE AS SELECT` を使うことはできません。
>

データの読み込みの詳細については、[専用 SQL プール向けの抽出、読み込み、変換 (ELT) の設計](design-elt-data-loading.md)と[読み込みのベスト プラクティス](guidance-for-loading-data.md)に関するページを参照してください。

## <a name="system-views"></a>システム ビュー

[sys.identity_columns](/sql/relational-databases/system-catalog-views/sys-identity-columns-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) カタログ ビューを使用して、IDENTITY プロパティを持つ列を識別できます。

データベース スキーマを理解しやすいように、次の例では sys.identity_column を他のシステム カタログ ビューと統合する方法を示します。

```sql
SELECT  sm.name
,       tb.name
,       co.name
,       CASE WHEN ic.column_id IS NOT NULL
             THEN 1
        ELSE 0
        END AS is_identity
FROM        sys.schemas AS sm
JOIN        sys.tables  AS tb           ON  sm.schema_id = tb.schema_id
JOIN        sys.columns AS co           ON  tb.object_id = co.object_id
LEFT JOIN   sys.identity_columns AS ic  ON  co.object_id = ic.object_id
                                        AND co.column_id = ic.column_id
WHERE   sm.name = 'dbo'
AND     tb.name = 'T1'
;
```

## <a name="limitations"></a>制限事項

次の場合、IDENTITY プロパティは使用できません。

- 列のデータ型が INT または BIGINT ではない場合
- 列が分散キーでもある場合
- テーブルが外部テーブルである場合

次の関連する関数は、専用 SQL プールではサポートされません。

- [IDENTITY()](/sql/t-sql/functions/identity-function-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [@@IDENTITY](/sql/t-sql/functions/identity-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [SCOPE_IDENTITY](/sql/t-sql/functions/scope-identity-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [IDENT_CURRENT](/sql/t-sql/functions/ident-current-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [IDENT_INCR](/sql/t-sql/functions/ident-incr-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [IDENT_SEED](/sql/t-sql/functions/ident-seed-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

## <a name="common-tasks"></a>一般的なタスク

このセクションでは、IDENTITY 列を操作するときの一般的なタスクを実行するために使うことができるいくつかのサンプル コードを提供します。

以下のすべてのタスクで、列 C1 が IDENTITY です。

### <a name="find-the-highest-allocated-value-for-a-table"></a>テーブルに割り当てられた最も高い値を見つける

分散テーブルに割り当てられた最も高い値を特定するには、`MAX()` 関数を使います。

```sql
SELECT MAX(C1)
FROM dbo.T1
```

### <a name="find-the-seed-and-increment-for-the-identity-property"></a>IDENTITY プロパティのシードと増分を調べる

カタログ ビューで次のクエリを使って、テーブルの ID 増分とシード構成値を調べることができます。

```sql
SELECT  sm.name
,       tb.name
,       co.name
,       ic.seed_value
,       ic.increment_value
FROM        sys.schemas AS sm
JOIN        sys.tables  AS tb           ON  sm.schema_id = tb.schema_id
JOIN        sys.columns AS co           ON  tb.object_id = co.object_id
JOIN        sys.identity_columns AS ic  ON  co.object_id = ic.object_id
                                        AND co.column_id = ic.column_id
WHERE   sm.name = 'dbo'
AND     tb.name = 'T1'
;
```

## <a name="next-steps"></a>次のステップ

- [テーブルの概要](sql-data-warehouse-tables-overview.md)
- [CREATE TABLE (Transact-SQL) IDENTITY (プロパティ)](/sql/t-sql/statements/create-table-transact-sql-identity-property?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [DBCC CHECKINDENT](/sql/t-sql/database-console-commands/dbcc-checkident-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
