---
title: Azure SQL Data Warehouse でのテーブルのインデックス作成 | Microsoft Azure
description: Azure SQL Data Warehouse でのテーブル のインデックス作成に関する推奨事項と例。
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 03/18/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seoapril2019
ms.openlocfilehash: 4d51bd6906a8299a25fe50ca817b1a2b6082ab91
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479852"
---
# <a name="indexing-tables-in-sql-data-warehouse"></a>SQL Data Warehouse でのテーブルのインデックス作成

Azure SQL Data Warehouse でのテーブル のインデックス作成に関する推奨事項と例。

## <a name="index-types"></a>インデックスの種類

SQL Data Warehouse には、[クラスター化列ストア インデックス](/sql/relational-databases/indexes/columnstore-indexes-overview)、[クラスター化インデックスと非クラスター化インデックス](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described)、[ヒープ](/sql/relational-databases/indexes/heaps-tables-without-clustered-indexes)とも呼ばれる非インデックス オプションなど、いくつかのインデックス作成オプションが用意されています。  

インデックスを持つテーブルを作成する方法については、「[CREATE TABLE (Azure SQL Data Warehouse)](/sql/t-sql/statements/create-table-azure-sql-data-warehouse)」のドキュメントを参照してください。

## <a name="clustered-columnstore-indexes"></a>クラスター化列ストア インデックス

既定では、SQL Data Warehouse では、テーブルにインデックス オプションが指定されていない場合、クラスター化列ストア インデックスが作成されます。 クラスター化列ストア テーブルは、クエリの全体的なパフォーマンスを最適化するだけでなく、最上位のレベルのデータ圧縮が可能になります。  クラスター化列ストア テーブルは、一般的にクラスター化インデックスまたはヒープ テーブルより優れており、大きなテーブルの選択肢として通常最適です。  こうした理由から、テーブルのインデックスを作成する方法に確信がない場合は、クラスター化列ストアを使用することをお勧めします。  

クラスター化列ストア テーブルを作成するには、単純に WITH 句で CLUSTERED COLUMNSTORE INDEX を指定するか、WITH 句を省略します。

```SQL
CREATE TABLE myTable
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED COLUMNSTORE INDEX );
```

クラスター化列ストアが最適なオプションでない可能性があるシナリオを次に示します。

- 列ストア テーブルでは、varchar(max)、nvarchar(max)、および varbinary(max) は使用できません。 代わりに、ヒープまたはクラスター化インデックスを検討します。
- 列ストア テーブルでは、一時的なデータで効率が低下する可能性があります。 ヒープと、場合によっては一時テーブルも検討してください。
- 6,000 万行未満の小さなテーブル。 ヒープ テーブルを検討してください。

## <a name="heap-tables"></a>ヒープ テーブル

データを一時的に SQL Data Warehouse に読み込む際は、ヒープ テーブルを使用すると、プロセス全体が高速になる場合があります。 これは、ヒープの読み込みがインデックス テーブルの読み込みより高速になり、場合によってはキャッシュから後続の読み取りを実行できる場合があるためです。  さまざまな変換を実行する前にデータをステージングするためにのみ読み込む場合は、ヒープ テーブルにテーブルを読み込むと、データをクラスター化列ストア テーブルに読み込む場合よりもはるかに高速に読み込まれます。 さらに、テーブルを永続記憶域に読み込むよりも、データを[一時テーブル](sql-data-warehouse-tables-temporary.md)に読み込んだ方が読み込みが速くなります。  

6,000 万行未満の小さなルックアップ テーブルでは、多くの場合、ヒープ テーブルが役立ちます。  クラスター列ストア テーブルは、6,000 万行を超えて初めて最適な圧縮が実現されます。

ヒープ テーブルを作成するには、単純に WITH 句で HEAP を指定します。

```SQL
CREATE TABLE myTable
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( HEAP );
```

## <a name="clustered-and-nonclustered-indexes"></a>クラスター化インデックスと非クラスター化インデックス

クラスター化インデックスは、1 つの行をすばやく取得する必要がある場合に、クラスター化列ストア テーブルを上回る可能性があります。 極めて高速で実行するために 1 行または極めて少数の行の検索が必要とされるクエリの場合、クラスター化インデックスまたは非クラスター化セカンダリ インデックスを検討してください。 クラスター化インデックスを使用するデメリットは、クラスター化インデックスの列で非常に選択的なフィルターを使用するクエリのみに効果が得られることです。 他の列のフィルターを改善するには、非クラスター化インデックスを他の列に追加できます。 ただし、テーブルに各インデックスを追加すると、領域と読み込みの処理時間の両方が増加します。

クラスター化インデックス テーブルを作成するには、単純に WITH 句で CLUSTERED INDEX を指定します。

```SQL
CREATE TABLE myTable
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED INDEX (id) );
```

テーブルに非クラスター化インデックスを追加するには、次の構文を使用します。

```SQL
CREATE INDEX zipCodeIndex ON myTable (zipCode);
```

## <a name="optimizing-clustered-columnstore-indexes"></a>クラスター化列ストア インデックスの最適化

クラスター化列ストア テーブルは、データ内のセグメントにまとめられます。  セグメントの品質を高くすることは、列ストア テーブルで最適なクエリ パフォーマンスを実現するために不可欠です。  セグメントの品質は、圧縮後の行グループに含まれる行の数を使って判断できます。  セグメントの品質が最も最適化されるのは、圧縮された行グループごとに少なくとも 10 万行が存在するときで、行グループごとの行数が 1,048, 576 行に近づくほどパフォーマンスが向上します。この行数は、行グループに含むことができる最大の行数です。

以下のビューを作成してご使用のシステムで使用し、行グループごとの平均行数を計算して最適化されていないクラスター化列ストア インデックスを特定することができます。  このビューの最後の列は、インデックスを再構築するために使用できる SQL ステートメントとして生成されます。

```sql
CREATE VIEW dbo.vColumnstoreDensity
AS
SELECT
        GETDATE()                                                               AS [execution_date]
,       DB_Name()                                                               AS [database_name]
,       s.name                                                                  AS [schema_name]
,       t.name                                                                  AS [table_name]
,    COUNT(DISTINCT rg.[partition_number])                    AS [table_partition_count]
,       SUM(rg.[total_rows])                                                    AS [row_count_total]
,       SUM(rg.[total_rows])/COUNT(DISTINCT rg.[distribution_id])               AS [row_count_per_distribution_MAX]
,    CEILING    ((SUM(rg.[total_rows])*1.0/COUNT(DISTINCT rg.[distribution_id]))/1048576) AS [rowgroup_per_distribution_MAX]
,       SUM(CASE WHEN rg.[State] = 0 THEN 1                   ELSE 0    END)    AS [INVISIBLE_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE 0    END)    AS [INVISIBLE_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 1 THEN 1                   ELSE 0    END)    AS [OPEN_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE 0    END)    AS [OPEN_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 2 THEN 1                   ELSE 0    END)    AS [CLOSED_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE 0    END)    AS [CLOSED_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 3 THEN 1                   ELSE 0    END)    AS [COMPRESSED_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE 0    END)    AS [COMPRESSED_rowgroup_rows]
,       SUM(CASE WHEN rg.[State] = 3 THEN rg.[deleted_rows]   ELSE 0    END)    AS [COMPRESSED_rowgroup_rows_DELETED]
,       MIN(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_AVG]
,       'ALTER INDEX ALL ON ' + s.name + '.' + t.NAME + ' REBUILD;'             AS [Rebuild_Index_SQL]
FROM    sys.[pdw_nodes_column_store_row_groups] rg
JOIN    sys.[pdw_nodes_tables] nt                   ON  rg.[object_id]          = nt.[object_id]
                                                    AND rg.[pdw_node_id]        = nt.[pdw_node_id]
                                                    AND rg.[distribution_id]    = nt.[distribution_id]
JOIN    sys.[pdw_table_mappings] mp                 ON  nt.[name]               = mp.[physical_name]
JOIN    sys.[tables] t                              ON  mp.[object_id]          = t.[object_id]
JOIN    sys.[schemas] s                             ON t.[schema_id]            = s.[schema_id]
GROUP BY
        s.[name]
,       t.[name]
;
```

ビューが作成できたので、このクエリを実行して、10 万行未満の行グループを持つテーブルを特定します。 もちろん、セグメントの品質をさらに高める必要がある場合は、10 万行のしきい値を高くすることもできます。

```sql
SELECT    *
FROM    [dbo].[vColumnstoreDensity]
WHERE    COMPRESSED_rowgroup_rows_AVG < 100000
        OR INVISIBLE_rowgroup_rows_AVG < 100000
```

クエリを実行したら、データの確認と結果の分析を開始できます。 次の表では、行グループ分析で確認する項目について説明します。

| 列 | このデータの使用方法 |
| --- | --- |
| [table_partition_count] |テーブルがパーティション分割されている場合は、より多くの開いている行グループが表示されます。 ディストリビューションの各パーティションに、理論上、開いている行グループが関連付けられています。 これを分析に組み込みます。 パーティション分割されている小さなテーブルを最適化するには、パーティション分割をすべて削除して、圧縮を向上させます。 |
| [row_count_total] |テーブルの合計行数。 たとえば、この値を使用して、圧縮状態の行の割合を計算できます。 |
| [row_count_per_distribution_MAX] |すべての行が均等に分散されている場合、この値は、ディストリビューションあたりの目標行数になります。 この値を compressed_rowgroup_count と比較します。 |
| [COMPRESSED_rowgroup_rows] |テーブルの列ストア形式の合計行数。 |
| [COMPRESSED_rowgroup_rows_AVG] |平均行数が行グループの最大行数を大幅に下回る場合は、CTAS または ALTER INDEX REBUILD を使用してデータを再圧縮することを検討してください。 |
| [COMPRESSED_rowgroup_count] |列ストア形式の行グループの数。 この数がテーブルに対して非常に多い場合は、列ストアの密度が低いことを示します。 |
| [COMPRESSED_rowgroup_rows_DELETED] |行が列ストア形式で論理的に削除されます。 この数がテーブル サイズと比べて多い場合は、パーティションを再作成するか、インデックスを再構築して、行を物理的に削除することを検討してください。 |
| [COMPRESSED_rowgroup_rows_MIN] |この値と、AVG 列および MAX 列を組み合わせて使用して、列ストアの行グループに対する値の範囲を把握します。 読み込みのしきい値 (パーティションに合わせて整列されたディストリビューションあたり 102,400) をわずかに上回っている場合は、データ読み込みで最適化が可能であることを示します。 |
| [COMPRESSED_rowgroup_rows_MAX] |上記と同じ。 |
| [OPEN_rowgroup_count] |開いている行グループは正常です。 テーブル ディストリビューションごとに 1 つの行グループが開いていることが期待できます (60)。 数が多すぎる場合は、パーティションをまたいでデータを読み込むことをお勧めします。 パーティション分割の計画が適切であることを再確認してください。 |
| [OPEN_rowgroup_rows] |各行グループには最大で 1,048,576 行を含めることができます。 この値は、開いている行グループの使用率を確認するときに使用します。 |
| [OPEN_rowgroup_rows_MIN] |開いているグループは、テーブルに読み込まれたデータが少量であることか、以前の読み込みで残りの行がこの行グループにあふれたことを示します。 MIN、MAX、AVG の各列を使用して、開いている行グループに含まれるデータ量を確認します。 テーブルが小さい場合は、100% になることもあります。 この場合は、ALTER INDEX REBUILD でデータを列ストアに強制的に移動します。 |
| [OPEN_rowgroup_rows_MAX] |上記と同じ。 |
| [OPEN_rowgroup_rows_AVG] |上記と同じ。 |
| [CLOSED_rowgroup_rows] |サニティ チェックとして、終了した行グループの行を確認します。 |
| [CLOSED_rowgroup_count] |終了した行グループの数は少なくなります (少しでも値が示されている場合)。 終了した行グループを圧縮行グループに変換するには、ALTER INDEX ...REORGANIZE コマンドを使用します。 ただし、通常、これは必要ありません。 終了したグループを自動的に列ストアの行グループに変換するには、バックグラウンドの "組ムーバー" プロセスを使用します。 |
| [CLOSED_rowgroup_rows_MIN] |終了した行グループのフィル レートは非常に高くなります。 終了した行グループのフィル レートが低い場合は、列ストアをさらに分析する必要があります。 |
| [CLOSED_rowgroup_rows_MAX] |上記と同じ。 |
| [CLOSED_rowgroup_rows_AVG] |上記と同じ。 |
| [Rebuild_Index_SQL] |テーブルの列ストア インデックスを再構築するための SQL です。 |

## <a name="causes-of-poor-columnstore-index-quality"></a>列ストア インデックスの品質の低さの原因

セグメントの品質が低いテーブルを識別したら、根本原因を特定する必要があります。  セグメントの品質が低くなるその他の一般的な原因をいくつか次に示します。

1. インデックスが構築されたときのメモリ不足
2. 大量の DML 操作
3. 小規模または少量の読み込み操作
4. 多すぎるパーティション

これらの根本原因により、列ストア インデックスの列グループあたりの行が最適な 100 万行より大幅に少なくなる可能性があります。 また、行が圧縮行グループではなくデルタ行グループに移動される可能性もあります。

### <a name="memory-pressure-when-index-was-built"></a>インデックスが構築されたときのメモリ不足

圧縮行グループあたりの行の数は、行の幅と行グループの処理に使用可能なメモリの量に直接関連します。  行を列ストア テーブルに書き込む際にメモリ負荷が発生すると、列ストア セグメントの質が低下する可能性があります。  そのため、列ストア インデックス テーブルへの書き込みセッションに、できるだけ多くメモリへのアクセスを与えることをお勧めします。  メモリとコンカレンシーの間にトレードオフがあるため、適切なメモリの割り当てに関するガイドラインは、テーブルの各行のデータ、システムに割り当てられた Data Warehouse ユニット、テーブルにデータを書き込むセッションに提供するコンカレンシー スロットの数によって異なります。

### <a name="high-volume-of-dml-operations"></a>大量の DML 操作

行を更新および削除する大量の DML 操作では、列ストアの効率性が低下する可能性があります。 これは、行グループ内の多数の行が変更される場合に特に当てはまります。

- 圧縮行グループから行を削除すると、行は論理的にのみ削除済みとしてマークされます。 パーティションまたはテーブルが再構築されるまで、行は圧縮行グループに残ります。
- 行を挿入すると、行はデルタ行グループと呼ばれる内部の行ストア テーブルに追加されます。 挿入された行は、デルタ行グループがいっぱいになりクローズとしてマークされるまで、列ストアに変換されません。 行グループは、1,048,576 行の最大容量に到達した時点で閉じられます。
- 列ストア形式での行の更新は、論理的な削除、挿入として処理されます。 挿入された行は、デルタ ストアに格納される可能性があります。

パーティションに合わせて整列されたディストリビューションあたりの一括しきい値 102,400 行を超えるバッチ更新および挿入操作は、列ストア形式に直接移動されます。 ただし、そのためには、均等なディストリビューションを想定すると、1 回の操作で 6,144,000 を超える行を変更する必要があります。 パーティションに合わせて整列された特定のディストリビューションの行数が 102,400 未満である場合、行はデルタ ストアに移動されます。これらの行は、十分な行が挿入または変更されて行グループが閉じられるか、またはインデックスが再構築されるまで、その場所に残ります。

### <a name="small-or-trickle-load-operations"></a>小規模または少量の読み込み操作

SQL Data Warehouse にフローする小規模な読み込みは、少量の読み込みとも呼ばれます。 通常、これらの読み込みは、システムによってインジェストされるほぼ一定のデータ ストリームを表します。 ただし、このストリームはほぼ連続的であるため、行の量はあまり多くありません。 多くの場合、データは、列ストア形式への直接読み込みに必要なしきい値を大幅に下回ります。

こうした状況では、多くの場合、データを最初に Azure BLOB ストレージに配置し、読み込む前に蓄積する方が適切です。 この手法は、多くの場合に *"マイクロ バッチ処理"* と呼ばれます。

### <a name="too-many-partitions"></a>多すぎるパーティション

考慮する必要があるもう 1 つの点は、クラスター化列ストア テーブルへのパーティション分割の影響です。  パーティション分割する前に、SQL Data Warehouse では、データが 60 個のデータベースに分割されます。  パーティション分割で、データはさらに分割されます。  データをパーティション分割する場合、クラスター化列ストア インデックスの恩恵を得るには、**個々の**パーティションに少なくとも 100 万行が必要なことを考慮に入れる必要があります。  テーブルを 100 個のパーティションにパーティション分割する場合、クラスター化列ストア インデックスの恩恵を受けるには、テーブルに少なくとも 60 億行必要です (60 個のディストリビューション *100 個のパーティション* 100 万行)。 100 個のパーティション テーブルに 60 億行もない場合は、パーティションの数を減らすか、代わりにヒープ テーブルを使用することを検討してください。

テーブルが一部のデータと共に読み込まれたら、以下の手順に従って、最適化されていないクラスター化列ストア インデックスを持つテーブルを特定して再構築します。

## <a name="rebuilding-indexes-to-improve-segment-quality"></a>セグメントの品質を向上させるためのインデックスの再構築

### <a name="step-1-identify-or-create-user-which-uses-the-right-resource-class"></a>手順 1:適切なリソース クラスを使用しているユーザーを特定または作成する

セグメントの品質を簡単に高める方法の 1 つが、インデックスの再構築です。  上記のビューが返す SQL によって、インデックスを再構築するために使用できる ALTER INDEX REBUILD ステートメントが返されます。 インデックスを再構築するときは、インデックスを再構築するセッションに必ず十分なメモリを割り当ててください。  これを実行するには、次のテーブルのインデックスを再構築するためのアクセス許可を持っているユーザーのリソース クラスを、推奨される最小値に変更します。

次に、リソース クラスを増やすことでより多くのメモリをユーザーに割り当てる方法の例を示します。 リソース クラスの使用については、[「ワークロード管理用のリソース クラス](resource-classes-for-workload-management.md)」を参照してください。

```sql
EXEC sp_addrolemember 'xlargerc', 'LoadUser'
```

### <a name="step-2-rebuild-clustered-columnstore-indexes-with-higher-resource-class-user"></a>手順 2:より高いリソース クラス ユーザーでクラスター化列ストア インデックスを再構築する

手順 1 でリソース クラスを上位に変更したユーザー (たとえば LoadUser) としてサインインし、ALTER INDEX ステートメントを実行します。 このユーザーは、インデックスが再構築されるテーブルに対して ALTER 権限を持っている必要があるのでご注意ください。 これらの例では、列ストア インデックス全体を再構築する方法や単一のパーティションを再構築する方法を示します。 大規模なテーブルでは、単一のパーティションとインデックスを同時に再構築すると、より実用的です。

または、インデックスを再構築せずに、[CTAS を使用して](sql-data-warehouse-develop-ctas.md)テーブルを新しいテーブルにコピーできます。 最良の方法はどちらでしょうか。 大量のデータの場合は、通常、CTAS の方が [ALTER INDEX](/sql/t-sql/statements/alter-index-transact-sql) より高速です。 少量のデータの場合は、ALTER INDEX を簡単に使用できるため、テーブルを入れ替える必要はありません。

```sql
-- Rebuild the entire clustered index
ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD
```

```sql
-- Rebuild a single partition
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5
```

```sql
-- Rebuild a single partition with archival compression
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5 WITH (DATA_COMPRESSION = COLUMNSTORE_ARCHIVE)
```

```sql
-- Rebuild a single partition with columnstore compression
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5 WITH (DATA_COMPRESSION = COLUMNSTORE)
```

SQL Data Warehouse でのインデックスの再構築は、オフライン操作です。  インデックスの再構築の詳細については、「[列ストア インデックス - 最適化](/sql/relational-databases/indexes/columnstore-indexes-defragmentation)」の ALTER INDEX REBUILD に関するセクションと、「[ALTER INDEX](/sql/t-sql/statements/alter-index-transact-sql)」を参照してください。

### <a name="step-3-verify-clustered-columnstore-segment-quality-has-improved"></a>手順 3:クラスター化列ストア セグメントの品質改善を確認する

セグメントの品質が低いテーブルを特定するクエリを再実行し、セグメントの品質が改善したことを確認します。  セグメントの品質が改善されていない場合は、テーブル内の行の幅が余分である可能性があります。  インデックスを再構築するときに、より高いリソース クラスまたは DWU の使用を検討してください。

## <a name="rebuilding-indexes-with-ctas-and-partition-switching"></a>CTAS とパーティションの切り替えを使用したインデックスの再構築

この例では、[CREATE TABLE AS SELECT (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) ステートメントとパーティション切り替えを使用してテーブル パーティションを再構築します。

```sql
-- Step 1: Select the partition of data and write it out to a new table using CTAS
CREATE TABLE [dbo].[FactInternetSales_20000101_20010101]
    WITH    (   DISTRIBUTION = HASH([ProductKey])
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101,20010101
                                )
                            )
            )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

-- Step 2: Switch IN the rebuilt data with TRUNCATE_TARGET option
ALTER TABLE [dbo].[FactInternetSales_20000101_20010101] SWITCH PARTITION 2 TO  [dbo].[FactInternetSales] PARTITION 2 WITH (TRUNCATE_TARGET = ON);
```

CTAS を使用してパーティションを再作成する方法の詳細については、[SQL Data Warehouse でのパーティション分割の使用](sql-data-warehouse-tables-partition.md)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

テーブルの開発の詳細については、[テーブルの開発](sql-data-warehouse-tables-overview.md)に関するページを参照してください。
