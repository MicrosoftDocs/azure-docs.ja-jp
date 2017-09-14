---
title: "IDENTITY を使用して代理キーを作成する | Microsoft Docs"
description: "IDENTITY を使ってテーブルに代理キーを作成する方法を説明します。"
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: faa1034d-314c-4f9d-af81-f5a9aedf33e4
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 06/13/2017
ms.author: jrj;barbkess
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: 3ab5d159e6eaeb830135fe134e108b0e4de4b7d6
ms.contentlocale: ja-jp
ms.lasthandoff: 07/04/2017


---
# <a name="create-surrogate-keys-by-using-identity"></a>IDENTITY を使用して代理キーを作成する
> [!div class="op_single_selector"]
> * [概要][Overview]
> * [データ型][Data Types]
> * [分散][Distribute]
> * [インデックス][Index]
> * [パーティション][Partition]
> * [統計][Statistics]
> * [一時][Temporary]
> * [IDENTITY][Identity]
> 
> 

多くのデータ モデラーは、データ ウェアハウス モデルを設計するときに、テーブルに代理キーを作成するのを好みます。 IDENTITY プロパティを使うと、この目的を簡単かつ効果的に達成でき、読み込みのパフォーマンスが影響を受けることもありません。 

## <a name="get-started-with-identity"></a>IDENTITY の概要
次のステートメントのような構文を使って、テーブルを最初に作成するときに、IDENTITY プロパティを持つようにテーブルを定義できます。

```sql
CREATE TABLE dbo.T1
(   C1 INT IDENTITY(1,1) NOT NULL
,   C2 INT NULL
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;
```

その後、`INSERT..SELECT` を使ってテーブルを設定します。

## <a name="behavior"></a>動作
IDENTITY プロパティは、読み込みパフォーマンスに影響を与えずに、データ ウェアハウス内のすべてのディストリビューションにスケールアウトするように設計されています。 そのため、IDENTITY の実装はこれらの目標を達成するようになっています。 このセクションでは、理解を深めるのに役立つ実装の詳細に注目します。  

### <a name="allocation-of-values"></a>値の割り当て
IDENTITY プロパティは、代理値が割り当てられる順序を保証しません。順序は、SQL Server と Azure SQL Database の動作を反映します。 ただし、Azure SQL Data Warehouse では保証のなさがいっそう顕著です。 

次にその例を示します。

```sql
CREATE TABLE dbo.T1
(   C1 INT IDENTITY(1,1)    NOT NULL
,   C2 VARCHAR(30)              NULL
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
- SELECT ステートメントに結合が含まれる。
- 複数の SELECT ステートメントが UNION を使って結合されている。
- IDENTITY 列が SELECT リストに複数回出現する。
- IDENTITY 列が式の一部である。
    
これらの条件のいずれかが当てはまる場合、列は IDENTITY プロパティを継承する代わりに NOT NULL で作成されます。

### <a name="create-table-as-select"></a>CREATE TABLE AS SELECT
CREATE TABLE AS SELECT (CTAS) は、SELECT..INTO と同じ SQL Server 動作に従います。 ただし、ステートメントの `CREATE TABLE` 部分の列定義で IDENTITY プロパティを指定することはできません。 また、CTAS の `SELECT` 部分で IDENTITY 関数を使うこともできません。 テーブルに値を設定するには、`CREATE TABLE` を使ってテーブルを定義した後、`INSERT..SELECT` で値を設定する必要があります。

## <a name="explicitly-insert-values-into-an-identity-column"></a>IDENTITY 列に値を明示的に挿入する 
SQL Data Warehouse は、`SET IDENTITY_INSERT <your table> ON|OFF` 構文をサポートしています。 この構文を使って、IDENTITY 列に値を明示的に挿入できます。

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

SELECT  *
FROM    dbo.T1
;
```    

## <a name="load-data-into-a-table-with-identity"></a>IDENTITY のあるテーブルにデータを読み込む

IDENTITY プロパティが存在すると、データ読み込みコードに影響があります。 ここでは、IDENTITY を使ってテーブルにデータを読み込む場合のいくつかの基本的なパターンを示します。 

### <a name="load-data-with-polybase"></a>PolyBase を使用したデータのロード
IDENTITY を使ってテーブルにデータを読み込んで代理キーを生成するには、テーブルを作成した後、INSERT..SELECT または INSERT..VALUES を使って読み込みを実行します。

次の例では基本的なパターンを示します。
 
```sql
--CREATE TABLE with IDENTITY
CREATE TABLE dbo.T1
(   C1 INT IDENTITY(1,1)
,   C2 VARCHAR(30)
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;

--Use INSERT..SELECT to populate the table from an external table
INSERT INTO dbo.T1
(C2)
SELECT  C2
FROM    ext.T1
;

SELECT  *
FROM    dbo.T1
;

DBCC PDW_SHOWSPACEUSED('dbo.T1');
```

> [!NOTE] 
> 現在は、IDENTITY 列のあるテーブルへのデータの読み込みに、`CREATE TABLE AS SELECT` を使うことはできません。
> 

一括コピー プログラム (BCP) ツールを使ってデータを読み込む方法について詳しくは、次の記事をご覧ください。

- [PolyBase での読み込み][]
- [PolyBase のベスト プラクティス][]

### <a name="load-data-with-bcp"></a>BCP を使用したデータの読み込み
コマンドライン ツールの BCP を使って、SQL Data Warehouse にデータを読み込むことができます。 パラメーターの 1 つ (-E) が、IDENTITY 列のあるテーブルにデータを読み込むときの BCP の動作を制御します。 

-E を指定すると、IDENTITY 列に対して入力ファイルで保持されている値が維持されます。 -E を "*指定しない*" と、この列の値は無視されます。 IDENTITY 列が含まれない場合は、データは普通に読み込まれます。 値は、プロパティの増分とシード ポリシーに従って生成されます。

BCP を使ったデータの読み込みについて詳しくは、次の記事をご覧ください。

- [BCP での読み込み][]
- [MSDN での BCP][]

## <a name="catalog-views"></a>カタログ ビュー
SQL Data Warehouse は、`sys.identity_columns` カタログ ビューをサポートしています。 このビューを使って、IDENTITY プロパティを持つ列を識別できます。

データベース スキーマを理解しやすいように、次の例では `sys.identity_columns` を他のシステム カタログ ビューと統合する方法を示します。

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
IDENTITY プロパティは、次のシナリオでは使うことができません。
- 列のデータ型が INT または BIGINT ではない場合
- 列が分散キーでもある場合
- テーブルが外部テーブルである場合 

次の関連する関数は、SQL Data Warehouse ではサポートされません。

- [IDENTITY()][]
- [@@IDENTITY][]
- [SCOPE_IDENTITY][]
- [IDENT_CURRENT][]
- [IDENT_INCR][]
- [IDENT_SEED][]
- [DBCC CHECK_IDENT()][]

## <a name="tasks"></a>タスク

このセクションでは、IDENTITY 列を操作するときの一般的なタスクを実行するために使うことができるいくつかのサンプル コードを提供します。

> [!NOTE] 
> 以下のすべてのタスクで、列 C1 が IDENTITY です。
> 
 
### <a name="find-the-highest-allocated-value-for-a-table"></a>テーブルに割り当てられた最も高い値を見つける
分散テーブルに割り当てられた最も高い値を特定するには、`MAX()` 関数を使います。

```sql
SELECT  MAX(C1)
FROM    dbo.T1
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

* テーブルの開発について詳しくは、[テーブルの概要][Overview]、[テーブルのデータ型][Data Types]、[テーブルの分散][Distribute]、[テーブルのインデックス作成][Index]、[テーブルのパーティション分割][Partition]、[一時テーブル][Temporary]に関する各記事をご覧ください。 
* ベスト プラクティスについて詳しくは、「[SQL Data Warehouse のベスト プラクティス][SQL Data Warehouse Best Practices]」をご覧ください。  

<!--Image references-->

<!--Article references-->
[Overview]: ./sql-data-warehouse-tables-overview.md
[Data Types]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[Identity]: ./sql-data-warehouse-tables-identity.md
[SQL Data Warehouse Best Practices]: ./sql-data-warehouse-best-practices.md

[BCP での読み込み]: https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-with-bcp/
[PolyBase での読み込み]: https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-from-azure-blob-storage-with-polybase/
[PolyBase のベスト プラクティス]: https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-polybase-guide/


<!--MSDN references-->
[Identity property]: https://msdn.microsoft.com/library/ms186775.aspx
[sys.identity_columns]: https://msdn.microsoft.com/library/ms187334.aspx
[IDENTITY()]: https://msdn.microsoft.com/library/ms189838.aspx
[@@IDENTITY]: https://msdn.microsoft.com/library/ms187342.aspx
[SCOPE_IDENTITY]: https://msdn.microsoft.com/library/ms190315.aspx
[IDENT_CURRENT]: https://msdn.microsoft.com/library/ms175098.aspx
[IDENT_INCR]: https://msdn.microsoft.com/library/ms189795.aspx
[IDENT_SEED]: https://msdn.microsoft.com/library/ms189834.aspx
[DBCC CHECK_IDENT()]: https://msdn.microsoft.com/library/ms176057.aspx

[MSDN での BCP]: https://msdn.microsoft.com/library/ms162802.aspx
  

<!--Other Web references-->  

