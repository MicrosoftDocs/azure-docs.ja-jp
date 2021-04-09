---
title: 列ストア インデックスのパフォーマンスの向上
description: メモリ要件を減らすか、使用可能なメモリを増やして列ストア インデックスが各行グループに圧縮する行の数を最大限にします。
services: synapse-analytics
author: gaursa
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: gaursa
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 2cbf38808f69ed2c1f76196fc7580a8ad3dae56a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104602079"
---
# <a name="maximize-rowgroup-quality-for-columnstore-index-performance"></a>列ストア インデックスのパフォーマンスのために行グループの品質を最大化する

行グループの品質は、行グループ内の行数によって決まります。 使用できるメモリを増やすと、列ストア インデックスが各行グループに圧縮する行数を最大化できます。  これらのメソッドを使用して、列ストア インデックスの圧縮率およびクエリ パフォーマンスを向上させます。

## <a name="why-the-rowgroup-size-matters"></a>行グループのサイズが重要な理由

列ストアインデックスは個別の行グループの列セグメントをスキャンすることでテーブルをスキャンし、各行グループの行の数を最大限にしてクエリ パフォーマンスを向上させます。 行グループに多くの行がある場合、データ圧縮が向上します。つまり、ディスクから読み取るデータが少なくなります。

行グループの詳細については、「[列ストア インデックス ガイド](/sql/relational-databases/indexes/columnstore-indexes-overview?view=azure-sqldw-latest&preserve-view=true)」を参照してください。

## <a name="target-size-for-rowgroups"></a>行グループのターゲット サイズ

最高のクエリ パフォーマンスを引き出すために、目標は列ストアインデックスの行グループごとの行の数を最大限にすることです。 行グループには、最大で 1,048,576 行を含められます。 行グループごとの行数が最大数ではなくても問題はありません。 列ストア インデックスは、行グループに 100,000 以上の行が含まれると、良好なパフォーマンスを実現します。

## <a name="rowgroups-can-get-trimmed-during-compression"></a>圧縮時の行グループのトリミング

一括読み込み中または列ストア インデックスの再構築中、各行グループに指定された行をすべて圧縮するのに使用可能なメモリが十分ではないことがあります。 メモリの負荷がある場合、列ストアへの圧縮ができるように、列ストア インデックスは行グループのサイズをトリミングします。

それぞれの行グループに 10,000 行以上を圧縮する十分なメモリがない場合、エラーが生成されます。

一括読み込みの詳細については、「[クラスター化列ストア インデックスへの一括読み込み](/sql/relational-databases/indexes/columnstore-indexes-data-loading-guidance?view=azure-sqldw-latest#bulk&preserve-view=true)」セクションを参照してください。

## <a name="how-to-monitor-rowgroup-quality"></a>行グループの品質を監視する方法

行グループの行数や、トリミングがあった場合はトリミングの理由など、役立つ情報を示す DMV sys.dm_pdw_nodes_db_column_store_row_group_physical_stats があります ([sys.dm_db_column_store_row_group_physical_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-column-store-row-group-physical-stats-transact-sql?view=azure-sqldw-latest&preserve-view=true) には、SQL DB に一致するビュー定義が含まれます)。 次のビューを作成します。これは、この DMV に対してクエリを実行し、行グループのトリミングに関する情報を取得できる便利な方法です。

```sql
create view dbo.vCS_rg_physical_stats
as
with cte
as
(
select   tb.[name]                    AS [logical_table_name]
,        rg.[row_group_id]            AS [row_group_id]
,        rg.[state]                   AS [state]
,        rg.[state_desc]              AS [state_desc]
,        rg.[total_rows]              AS [total_rows]
,        rg.[trim_reason_desc]        AS trim_reason_desc
,        mp.[physical_name]           AS physical_name
FROM    sys.[schemas] sm
JOIN    sys.[tables] tb               ON  sm.[schema_id]          = tb.[schema_id]
JOIN    sys.[pdw_table_mappings] mp   ON  tb.[object_id]          = mp.[object_id]
JOIN    sys.[pdw_nodes_tables] nt     ON  nt.[name]               = mp.[physical_name]
JOIN    sys.[dm_pdw_nodes_db_column_store_row_group_physical_stats] rg      ON  rg.[object_id]     = nt.[object_id]
                                                                            AND rg.[pdw_node_id]   = nt.[pdw_node_id]
                                        AND rg.[distribution_id]    = nt.[distribution_id]
)
select *
from cte;
```

trim_reason_desc は、行グループがトリミングされたかどうかを示します (trim_reason_desc = NO_TRIM は、トリミングがなく、新しいグループが最適な品質であることを示します)。 次のトリミングの理由は、行グループのトリミングが不完全であることを示します。

- BULKLOAD:このトリミング理由は、負荷の行の受信バッチが 100 万行未満の場合に使用されます。 (デルタ ストアへの挿入とは異なり) 挿入される行が 100,000 行を超え、トリミング理由が BULKLOAD に設定されている場合、エンジンでは圧縮された行グループが作成されます。 このシナリオでは、より多くの行を追加できるように、バッチ負荷を増やすことをお勧めします。 また、行グループはパーティション境界をまたぐことはできないため、パーティション構成を評価し直して細かくなり過ぎないようにします。
- MEMORY_LIMITATION:100 万行の行グループを作成するには、エンジンに特定サイズの作業メモリが必要です。 読み込みセッションに使用できるメモリが、必要な作業メモリよりも少ない場合、行グループは途中でトリミングされます。 以下のセクションでは、必要なメモリを見積もり、メモリの割り当てを増やす方法について説明します。
- DICTIONARY_SIZE:このトリミング理由は、カーディナリティ文字列の桁数が多い、または高い文字列の列が 1 つ以上あったため、行グループのトリミングが発生したことを示します。 ディクショナリのサイズはメモリ内の 16 MB に制限されています。この制限に達すると、行グループは圧縮されます。 このような場合は、問題のある列を別のテーブルに分離することをお勧めします。

## <a name="how-to-estimate-memory-requirements"></a>メモリ要件の見積もり方法

1 つの行グループを圧縮するために必要なメモリの最大値は、おおよそ次のとおりです:

- 72 MB 以上
- \#行 \* \#列 \* 8 バイト +
- \#行 \* \#短い文字列の列 \* 32 バイト +
- \#長い文字列の列 \* 圧縮ディクショナリに 16 MB

> [!NOTE]
> 短い文字列の列は 32 バイト以下の文字列データを使用し、長い文字列の列は 32 バイト超の文字列データを使用します。

長い文字列は、テキストの圧縮に指定されている圧縮方法で圧縮されます。 この圧縮方法では、*ディクショナリ* を使用してテキスト パターンを格納します。 ディクショナリの最大サイズは 16 MB です。 ディクショナリは、行グループ内の長い文字列の列ごとに 1 つだけです。

列ストアのメモリ要件の詳細については、[Synapse SQL のスケーリング: 構成とガイダンス](https://channel9.msdn.com/Events/Ignite/2016/BRK3291)に関するビデオをご覧ください。

## <a name="ways-to-reduce-memory-requirements"></a>メモリ要件を軽減する方法

次の方法を使用して、行グループを列ストア インデックスに圧縮するためのメモリ要件を軽減します。

### <a name="use-fewer-columns"></a>より少ない列の使用

可能であれば、より少ない列を含むテーブルを設計します。 行グループが列ストアに圧縮されると、列ストア インデックスは各列セグメントを個別に圧縮します。 したがって、行グループを圧縮するためのメモリ要件は、列数が増えると増加します。

### <a name="use-fewer-string-columns"></a>より少ない文字列の列の使用

文字列データ型の列には、数値および日付のデータ型より多くのメモリが必要です。 メモリ要件を減らすため、ファクト テーブルから文字列の列を削除して、これらをよりディメンションの小さなテーブルに格納することを検討してください。

文字列圧縮の追加のメモリ要件:

- 32 文字以内の文字列データ型には、値ごとに追加で 32 バイトが必要になる可能性があります。
- 32 文字超の文字列データ型は、ディクショナリ メソッドを使用して圧縮されます。  行グループ内の各列には、ディクショナリを構築するため、最大で 16 MB のメモリが追加で必要になる可能性があります。

### <a name="avoid-over-partitioning"></a>過剰なパーティション分割の回避

列ストア インデックスは、パーティションごとに 1 つまたは複数の行グループを作成します。 Azure Synapse Analytics のデータ ウェアハウスでは、データが分散されており、それぞれのディストリビューションがパーティション分割されているため、パーティションの数が急速に増加します。 テーブルに多数のパーティションがある場合は、行グループを入力するのに十分な行がない可能性があります。 行の欠如によって、圧縮時にメモリの負荷が発生することはありませんが、行グループの最適な列ストア クエリ パフォーマンスが実現しなくなります。

過剰なパーティション分割を回避する別の理由として、パーティション テーブルには、列ストア インデックスに行を読み込むためのメモリ オーバーヘッドがあります。 読み込み中、各パーティションに圧縮するのに十分な行が格納されるまでメモリで保持されていた行を、多数のパーティションが受信する可能性があります。 パーティションが多すぎると、追加のメモリ負荷が発生します。

### <a name="simplify-the-load-query"></a>クエリの読み込みの簡素化

データベースは、クエリ内のすべての演算子間でクエリのメモリ許可を共有します。 読み込みクエリに複雑な並べ替えや結合がある場合は、圧縮に使用可能なメモリが少なくなります。

クエリの読み込みにのみ重点を置くように、読み込みクエリを設計します。 データに対して変換を実行する必要がある場合は、変換を読み込みクエリとは別に実行します。 たとえば、ヒープ テーブル内のデータをステージングし、変換を実行して、その後列ストア インデックスにステージング テーブルを読み込みます。 

### <a name="adjust-maxdop"></a>MAXDOP の調整

各ディストリビューションで、ディストリビューションごとに複数の CPU コアが使用可能な場合、行グループが並列で列ストアに圧縮されます。 並列処理には追加のメモリ リソースが必要となり、これによってメモリ負荷と行グループのトリミングが発生することがあります。

メモリ負荷を減らすために、MAXDOP クエリ ヒントを使用して、各ディストリビューション内において読み込み操作をシリアル モードで強制的に実行できます。

```sql
CREATE TABLE MyFactSalesQuota
WITH (DISTRIBUTION = ROUND_ROBIN)
AS SELECT * FROM FactSalesQuota
OPTION (MAXDOP 1);
```

## <a name="ways-to-allocate-more-memory"></a>より多くのメモリを割り当てる方法

DWU のサイズとユーザー リソースのクラスによって、ユーザー クエリで使用可能なメモリの量が決まります。 読み込みクエリのメモリ許可を増やすために、DWU の数を増やすか、リソース クラスを増やすことができます。

- DWU を増やす方法については、[パフォーマンスのスケーリング方法](../sql-data-warehouse/quickstart-scale-compute-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)に関するセクションを参照してください。
- クエリのリソース クラスを変更する方法については、「[ユーザー リソース クラスの変更例](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#change-a-users-resource-class)」を参照してください。

## <a name="next-steps"></a>次のステップ

Synapse SQL でパフォーマンスを向上させるその他の方法については、[パフォーマンスの概要](../overview-terminology.md)に関する記事を参照してください。

