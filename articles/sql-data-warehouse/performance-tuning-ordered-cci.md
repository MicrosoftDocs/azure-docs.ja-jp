---
title: Azure SQL Data Warehouse の順序指定クラスター化列ストア インデックスを使用したパフォーマンス チューニング | Microsoft Docs
description: 順序指定クラスター化列ストア インデックスを使用したクエリ パフォーマンスの向上に関して知っておくべき推奨事項と考慮事項。
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 09/05/2019
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.openlocfilehash: 0aecb2309743ffecc2fb68435192224c6c690aee
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/08/2019
ms.locfileid: "72035108"
---
# <a name="performance-tuning-with-ordered-clustered-columnstore-index"></a>順序指定クラスター化列ストア インデックスを使用したパフォーマンス チューニング  

ユーザーが Azure SQL Data Warehouse の列ストア テーブルに対してクエリを実行すると、各セグメントに格納されている最小値と最大値がオプティマイザーによってチェックされます。  クエリ述語の範囲外にあるセグメントは、ディスクからメモリに読み取られません。  読み取るセグメントの数が少なく、その合計サイズが小さい場合、クエリのパフォーマンスを向上させることができます。   

## <a name="ordered-vs-non-ordered-clustered-columnstore-index"></a>順序指定と非順序指定のクラスター化列ストア インデックス 
既定では、インデックス オプションを指定せずに作成された Azure Data Warehouse テーブルごとに、内部コンポーネント (インデックス ビルダー) によって非順序指定クラスター化列ストア インデックス (CCI) が作成されます。  各列のデータは、個別の CCI 行グループ セグメントに圧縮されます。  各セグメントの値の範囲にメタデータがあるため、クエリ述語の範囲外にあるセグメントがクエリの実行時にディスクから読み取られることはありません。  CCI では、最高レベルのデータ圧縮が提供され、読み取るセグメントのサイズが抑制されるため、クエリをより高速に実行できます。 ただし、インデックス ビルダーはデータをセグメントに圧縮する前に並べ替えないため、値の範囲が重複するセグメントが発生し、その結果、クエリがディスクから読み取るセグメントが増えて、完了にかかる時間が長くなる可能性があります。  

順序指定 CCI を作成する場合、Azure SQL Data Warehouse エンジンは、インデックス ビルダーがインデックス セグメントへと圧縮する前に、メモリ内の既存のデータを順序キーで並べ替えます。  データの並べ替えによってセグメントの重複が減少することで、ディスクから読み取るセグメントの数が少なくなるため、クエリでより効率的なセグメントの除外が行われ、パフォーマンスの高速化が実現します。  メモリ内ですべてのデータを一度に並べ替えられる場合、セグメントの重複を回避することができます。  データ ウェアハウス テーブル内のデータのサイズが大きい場合、このシナリオはあまり発生しません。  

列のセグメント範囲を確認するには、テーブル名と列名を指定してこのコマンドを実行します。

```sql
SELECT o.name, pnp.index_id, cls.row_count, pnp.data_compression_desc, pnp.pdw_node_id, 
pnp.distribution_id, cls.segment_id, cls.column_id, cls.min_data_id, cls.max_data_id, cls.max_data_id-cls.min_data_id as difference
FROM sys.pdw_nodes_partitions AS pnp
   JOIN sys.pdw_nodes_tables AS Ntables ON pnp.object_id = NTables.object_id AND pnp.pdw_node_id = NTables.pdw_node_id
   JOIN sys.pdw_table_mappings AS Tmap  ON NTables.name = TMap.physical_name AND substring(TMap.physical_name,40, 10) = pnp.distribution_id
   JOIN sys.objects AS o ON TMap.object_id = o.object_id
   JOIN sys.pdw_nodes_column_store_segments AS cls ON pnp.partition_id = cls.partition_id AND pnp.distribution_id  = cls.distribution_id
   JOIN sys.columns as cols ON o.object_id = cols.object_id AND cls.column_id = cols.column_id
WHERE o.name = '<Table_Name>' and cols.name = '<Column_Name>' 
ORDER BY o.name, pnp.distribution_id, cls.min_data_id

```

> [!NOTE] 
> 順序指定 CCI テーブルで、DML またはデータ読み込みの操作によって作成された新しいデータは自動的に並べ替えられません。  ユーザーは、順序指定 CCI を再構築して、テーブ内のすべてのデータを並べ替えることができます。  Azure SQL Data Warehouse で、列ストア インデックスの再構築はオフライン操作です。  パーティション テーブルの場合、再構築は一度に 1 つのパーティションずつ実行されます。  再構築されるパーティション内のデータは "オフライン" であり、そのパーティションの再構築が完了するまで使用できません。 

## <a name="query-performance"></a>クエリ パフォーマンス

順序指定 CCI から得られるクエリのパフォーマンスの向上は、クエリのパターン、データのサイズ、データの並べ替えがどの程度適切に行われているか、セグメントの物理的構造、およびクエリの実行に対して選択された DWU とリソース クラスによって異なります。  ユーザーは、順序指定 CCI テーブルを設計する際、順序付け列を選択する前に、これらすべての要素を確認する必要があります。

次のすべてのパターンを持つクエリは、通常、順序指定 CCI でより速く実行されます。  
1. クエリに、等値、非等値、または範囲の述語がある
1. 述語列と順序指定 CCI 列が同じである。  
1. 述語列が、順序指定 CCI 列の列序数と同じ順序で使用されている。  
 
この例で、テーブル T1 には、Col_C、Col_B、および Col_A のシーケンスで順序指定されたクラスター化列ストア インデックスがあります。

```sql

CREATE CLUSTERED COLUMNSTORE INDEX MyOrderedCCI ON  T1
ORDER (Col_C, Col_B, Col_A)

```

クエリ 1 のパフォーマンスは、他の 3 つのクエリよりも、順序指定 CCI の恩恵をより多く受けることができます。 

```sql
-- Query #1: 

SELECT * FROM T1 WHERE Col_C = 'c' AND Col_B = 'b' AND Col_A = 'a';

-- Query #2

SELECT * FROM T1 WHERE Col_B = 'b' AND Col_C = 'c' AND Col_A = 'a';

-- Query #3
SELECT * FROM T1 WHERE Col_B = 'b' AND Col_A = 'a';

-- Query #4
SELECT * FROM T1 WHERE Col_A = 'a' AND Col_C = 'c';

```

## <a name="data-loading-performance"></a>データ読み込みのパフォーマンス

順序指定 CCI テーブルへのデータ読み込みのパフォーマンスは、パーティション テーブルと似ています。  順序指定 CCI テーブルへのデータの読み込みは、データの並べ替え操作のため、非順序指定 CCI テーブルよりも時間がかかる可能性があります。ただし、その後、順序付けされた CCI では、クエリをより高速で実行できます。  

例として、スキーマが異なるテーブルへのデータ読み込みのパフォーマンス比較を次に示します。

![Performance_comparison_data_loading](media/performance-tuning-ordered-cci/cci-data-loading-performance.png)


CCI と順序指定 CCI のクエリ パフォーマンスの比較の例を次に示します。

![Performance_comparison_data_loading](media/performance-tuning-ordered-cci/occi_query_performance.png)

 
## <a name="reduce-segment-overlapping"></a>セグメントの重複の抑制

重複するセグメントの数は、並べ替えるデータのサイズ、使用可能なメモリ、および順序指定 CCI 作成時の並列処理の最大限度 (MAXDOP) 設定によって異なります。 次に示すのは、順序指定 CCI を作成するときのセグメントの重複を減らすためのオプションです。

- インデックス ビルダーがデータを複数のセグメントに圧縮する前に、より高い DWU で xlargerc リソース クラスを使用して、データの並べ替え用に、より多くのメモリを準備します。  インデックス セグメント内では、データの物理的な場所を変更することはできません。  セグメント内またはセグメント間でデータの並べ替えが行われることはありません。  

- MAXDOP = 1 を指定して順序指定 CCI を作成します。  順序指定 CCI の作成に使用される各スレッドでは、データのサブセットが処理され、ローカルで並べ替えられます。  異なるスレッドによって並べ替えられたデータ全体での並べ替えは行われません。  並列スレッドを使用すると、順序指定 CCI を作成する時間を短縮できますが、単一のスレッドを使用するよりも生成されるセグメントの重複が多くなります。  現在、MAXDOP オプションは、CREATE TABLE AS SELECT コマンドを使用した順序指定 CCI テーブルの作成でのみサポートされます。  CREATE INDEX コマンドまたは CREATE TABLE コマンドを使用した順序指定 CCI の作成では、MAXDOP オプションはサポートされません。 たとえば、次のように入力します。

```sql
CREATE TABLE Table1 WITH (DISTRIBUTION = HASH(c1), CLUSTERED COLUMNSTORE INDEX ORDER(c1) )
AS SELECT * FROM ExampleTable
OPTION (MAXDOP 1);
```
- Azure SQL Data Warehouse テーブルに読み込む前に、並べ替えキー順にデータを事前に並べ替える。


次に示すのは、上記の推奨事項に従って、重複するセグメントの数が 0 個の順序指定 CCI テーブルの分散の例です。 順序指定 CCI テーブルは、MAXDOP 1 と xlargerc を使用して 20GB のヒープ テーブルから CTAS を介して DWU1000c データベースに作成されます。  CCI は、重複なしで BIGINT 列で順序付けされます。  

![Segment_No_Overlapping](media/performance-tuning-ordered-cci/perfect-sorting-example.png)

## <a name="create-ordered-cci-on-large-tables"></a>大きなテーブルでの順序指定 CCI の作成
順序指定 CCI の作成はオフライン操作です。  パーティションがないテーブルの場合、順序指定 CCI の作成プロセスが完了するまで、ユーザーはデータにアクセスできません。   パーティション テーブルの場合、エンジンはパーティションごとに順序指定 CCI パーティションを作成するため、ユーザーは引き続き、順序指定 CCI の作成が処理中でないパーティションのデータにアクセスできます。   このオプションを使用すると、大きなテーブルでの順序指定 CCI の作成時に、ダウンタイムを最小限に抑えることができます。 

1.  ターゲットとなる大きなテーブル (Table A) にパーティションを作成します。
2.  テーブルとパーティションのスキーマが Table A と同じ、空の順序指定 CCI テーブル (Table B) を作成します。
3.  Table A から Table B にパーティションを 1 つ切り替えます。
4.  Table B で ALTER INDEX <Ordered_CCI_Index> REBUILD PARTITION = <Partition_ID> を実行して、切り替え先のパーティションを再構築します。  
5.  Table A のパーティションごとに手順 3 および 4 を繰り返します。
6.  すべてのパーティションを Table A から Table B に切り替えて再構築したら、Table A を削除して Table B の名前を Table A に変更します。 

## <a name="examples"></a>例

**A.順序指定された列と序数を確認するには:**
```sql
SELECT object_name(c.object_id) table_name, c.name column_name, i.column_store_order_ordinal 
FROM sys.index_columns i 
JOIN sys.columns c ON i.object_id = c.object_id AND c.column_id = i.column_id
WHERE column_store_order_ordinal <>0
```

**B.列序数の変更、順序のリストに対する列の追加または削除を行ったり、CCI から順序指定 CCI に変更したりするには:**
```sql
CREATE CLUSTERED COLUMNSTORE INDEX InternetSales ON  InternetSales
ORDER (ProductKey, SalesAmount)
WITH (DROP_EXISTING = ON)
```

## <a name="next-steps"></a>次の手順
開発に関するその他のヒントについては、「 [SQL Data Warehouse development overview (SQL Data Warehouse の開発の概要)](sql-data-warehouse-overview-develop.md)」をご覧ください。
