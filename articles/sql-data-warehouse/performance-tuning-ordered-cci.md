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
ms.openlocfilehash: 41fbebcf4b85f6e48babba30c2d05fedb3e7a5c7
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2019
ms.locfileid: "70985270"
---
# <a name="performance-tuning-with-ordered-clustered-columnstore-index"></a>順序指定クラスター化列ストア インデックスを使用したパフォーマンス チューニング  

ユーザーが Azure SQL Data Warehouse の列ストア テーブルに対してクエリを実行すると、各セグメントに格納されている最小値と最大値がオプティマイザーによってチェックされます。  クエリ述語の範囲外にあるセグメントは、ディスクからメモリに読み取られません。  読み取るセグメントの数が少なく、その合計サイズが小さい場合、クエリのパフォーマンスを向上させることができます。   

## <a name="ordered-vs-non-ordered-clustered-columnstore-index"></a>順序指定と非順序指定のクラスター化列ストア インデックス 
既定では、インデックス オプションを指定せずに作成された Azure Data Warehouse テーブルごとに、内部コンポーネント (インデックス ビルダー) によって非順序指定クラスター化列ストア インデックス (CCI) が作成されます。  各列のデータは、個別の CCI 行グループ セグメントに圧縮されます。  各セグメントの値の範囲にメタデータがあるため、クエリ述語の範囲外にあるセグメントがクエリの実行時にディスクから読み取られることはありません。  CCI では、最高レベルのデータ圧縮が提供され、読み取るセグメントのサイズが抑制されるため、クエリをより高速に実行できます。 ただし、インデックス ビルダーではデータをセグメントに圧縮する前に並べ替えないため、値の範囲が重複するセグメントが発生する可能性があります。このため、クエリによってディスクから読み取られるセグメントが多くなり、完了にかかる時間が長くなります。  

順序指定 CCI を作成する場合、メモリ内のデータは、インデックス ビルダーによってインデックス セグメントへと圧縮される前に、Azure SQL Data Warehouse エンジンによって順序キーの順に並べ替えられます。  データの並べ替えによってセグメントの重複が減少することで、ディスクから読み取るセグメントの数が少なくなるため、クエリでより効率的なセグメントの除外が行われ、パフォーマンスの高速化が実現します。  メモリ内ですべてのデータを一度に並べ替えられる場合、セグメントの重複を回避することができます。  データ ウェアハウス テーブル内のデータのサイズが大きい場合、このシナリオはあまり発生しません。  

列のセグメント範囲を確認するには、テーブル名と列名を指定してこのコマンドを実行します。

```sql
SELECT o.name, pnp.index_id, pnp.rows, pnp.data_compression_desc, pnp.pdw_node_id, 
pnp.distribution_id, cls.segment_id, cls.column_id, cls.min_data_id, cls.max_data_id, cls.max_data_id-cls.min_data_id as difference
FROM sys.pdw_nodes_partitions AS pnp
   JOIN sys.pdw_nodes_tables AS Ntables ON pnp.object_id = NTables.object_id AND pnp.pdw_node_id = NTables.pdw_node_id
   JOIN sys.pdw_table_mappings AS Tmap  ON NTables.name = TMap.physical_name AND substring(TMap.physical_name,40, 10) = pnp.distribution_id
   JOIN sys.objects AS o ON TMap.object_id = o.object_id
   JOIN sys.pdw_nodes_column_store_segments AS cls ON pnp.partition_id = cls.partition_id AND pnp.distribution_id  = cls.distribution_id
   JOIN sys.columns as cols ON o.object_id = cols.object_id AND cls.column_id = cols.column_id
WHERE o.name = '<table_name>' and c.name = '<column_name>'
ORDER BY o.name, pnp.distribution_id, cls.min_data_id
```

## <a name="data-loading-performance"></a>データ読み込みのパフォーマンス

順序指定 CCI テーブルへのデータ読み込みのパフォーマンスは、パーティション テーブルへのデータ読み込みに似ています。  
順序指定 CCI テーブルへのデータ読み込みは、データの並べ替えがあるため、非順序指定 CCI テーブルへのデータ読み込みよりも時間がかかる場合があります。  

例として、スキーマが異なるテーブルへのデータ読み込みのパフォーマンス比較を次に示します。
![Performance_comparison_data_loading](media/performance-tuning-ordered-cci/cci-data-loading-performance.png)
 
## <a name="reduce-segment-overlapping"></a>セグメントの重複の抑制
以下のオプションでは、CTAS による新しいテーブルまたはデータが含まれている既存のテーブルで順序指定 CCI を作成する場合に、セグメントの重複をさらに減らすことができます。

- インデックス ビルダーによるセグメントへの圧縮の前にメモリ内でより多くのデータを一度に並べ替えられるように、使用するリソース クラスを大きくする。  インデックス セグメント内では、データの物理的な場所を変更することはできません。  セグメント内またはセグメント間でデータの並べ替えが行われることはありません。  

- 並列処理の度合いを低くする (DOP = 1 など)。  順序指定 CCI の作成に使用される各スレッドでは、データのサブセットが処理され、ローカルで並べ替えられます。  異なるスレッドによって並べ替えられたデータ全体での並べ替えは行われません。  並列スレッドを使用すると、順序指定 CCI を作成する時間を短縮できますが、単一のスレッドを使用するよりも生成されるセグメントの重複が多くなります。 
- Azure SQL Data Warehouse テーブルに読み込む前に、並べ替えキー順にデータを事前に並べ替える。

## <a name="create-ordered-cci-on-large-tables"></a>大きなテーブルでの順序指定 CCI の作成
順序指定 CCI の作成はオフライン操作です。  パーティションがないテーブルの場合、順序指定 CCI の作成プロセスが完了するまで、ユーザーはデータにアクセスできません。   パーティション テーブルでは、エンジンによってパーティション単位で順序指定 CCI パーティションが作成されるため、ユーザーは、順序指定 CCI の作成が処理中ではないパーティションのデータにアクセスできます。   このオプションを使用すると、大きなテーブルでの順序指定 CCI の作成時に、ダウンタイムを最小限に抑えることができます。 

1.  ターゲットとなる大きなテーブル (Table A) にパーティションを作成します。
2.  テーブルとパーティションのスキーマが Table A と同じ、空の順序指定 CCI テーブル (Table B) を作成します。
3.  Table A から Table B にパーティションを 1 つ切り替えます。
4.  Table B で ALTER INDEX <Ordered_CCI_Index> REBUILD を実行し、切り替え先のパーティションを再構築します。  
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
