---
title: "SQL Data Warehouse のテーブルの分散 | Microsoft Docs"
description: "Azure SQL Data Warehouse のテーブルの分散の概要です。"
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: barbkess
editor: 
ms.assetid: 5ed4337f-7262-4ef6-8fd6-1809ce9634fc
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: jrj;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 5a101aa78dbac4f1a0edb7f414b44c14db392652
ms.openlocfilehash: 4becdace365fce1fb70e9e870dd4c8b81d1c7496


---
# <a name="distributing-tables-in-sql-data-warehouse"></a>SQL Data Warehouse のテーブルの分散
> [!div class="op_single_selector"]
> * [概要][概要]
> * [データ型][データ型]
> * [分散][分散]
> * [Index][Index]
> * [Partition][Partition]
> * [統計][統計]
> * [一時的な][一時]
>
>

SQL Data Warehouse は、超並列処理 (MPP) 分散データベース システムです。  SQL Data Warehouse は、データと処理機能を複数のノードに分割することで、単一のシステムをはるかに超えた大規模なスケーラビリティを実現します。  SQL Data Warehouse でデータの分散方法を決定することは、最適なパフォーマンスを実現するための最も重要な要因の 1 つです。   最適なパフォーマンスを実現する鍵は、データの移動を最小限に抑えることです。同様に、データの移動を最小限に抑える鍵は、適切なディストリビューション戦略を選択することです。

## <a name="understanding-data-movement"></a>データの移動について
MPP システムでは、各テーブルのデータは、基になる複数のデータベースにわたって分割されます。  MPP システムの高度に最適化されたクエリは、パススルーするだけで、他のデータベースとの対話なしで個々の分散データベース上で実行できます。  たとえば、2 つのテーブル (売上テーブルと顧客テーブル) が含まれる売上データのデータベースがあるとします。  売上テーブルを顧客テーブルに結合し、売上テーブルと顧客テーブルの両方を顧客番号で分割してそれぞれの顧客を別のデータベースに配置する必要のあるクエリがある場合、売上と顧客を結合するクエリは、他のデータベースに関する情報なしで各データベース内で解決できます。  これに対し、売上データが注文番号で分割され、顧客データが顧客番号で分割されている場合、特定のデータベースでそれぞれの顧客の対応するデータが存在しないケースがあります。そのため、売上データを顧客データに結合する場合に、それぞれの顧客のデータを他のデータベースから取得する必要があります。  この 2 つ目の例では、データの移動を行って顧客データを売上データに移動し、2 つのテーブルを結合できるようにする必要があります。  

データの移動は必ずしも悪いことではなく、場合によってはクエリを解決するために必要になります。  ただし、この余分な手順を回避できれば、当然ながら、クエリの実行速度が向上します。  データ移動は、テーブルが結合される、または集計が実行されるときに最もよく起こります。  多くの場合、この両方の操作を実行する必要があります。したがって、結合などの 1 つのシナリオ用に最適化できるとしても、やはり集計など、他のシナリオの解決に役立つデータ移動が必要になります。  その際のこつは、作業量が少ない方法を見極めることです。  ほとんどの場合、頻繁に結合される列を使用してサイズの大きいファクト テーブルを分散すると、データの移動を大きく抑えることができます。  データの移動を減らすには、集計に関係する列でデータを分散する方法よりも結合列でデータを分散する方法の方がより一般的です。

## <a name="select-distribution-method"></a>ディストリビューション方法を選択する
SQL Data Warehouse では、データが 60 個のデータベースにバックグラウンドで分割されます。  個々のデータベースを **ディストリビューション**と呼びます。  各テーブルにデータが読み込まれると、SQL Data Warehouse は、60 個のディストリビューション全体でデータを分割する方法を認識する必要があります。  

ディストリビューション方法はテーブル レベルで定義され、現在は次の 2 つの選択肢があります。

1. **ラウンド ロビン** : 均等でありながらランダムにデータを分散させます。
2. **ハッシュ分散** : 1 つの列の値のハッシュに基づいてデータを分散させます。

既定では、データのディストリビューション方法が定義されていない場合、テーブルは**ラウンド ロビン** ディストリビューション方法を使用して分散されます。  ただし、実装に慣れてきたら、データの移動を最小限に抑えるために、**ハッシュ分散**テーブルの使用を検討してもかまいません。これによってデータの移動が最小化されるため、クエリのパフォーマンスが最適化されます。

### <a name="round-robin-tables"></a>ラウンド ロビン テーブル
データの分散にラウンド ロビン方式を使用することは、非常によくあることです。  データが読み込まれると、各行は単純に最も近いディストリビューションに送信されます。  データのこの分散方法では、ディストリビューション全体でデータを常にランダムに、極めて均一に分散させます。  つまり、データを配置するラウンド ロビン プロセス中に並び替えは実行されません。  そのため、ラウンド ロビン ディストリビューションは、ランダム ハッシュと呼ばれる場合もあります。  ラウンド ロビン分散テーブルでは、データを理解する必要はありません。  そのため、多くの場合、ラウンド ロビン テーブルは優れた読み込みターゲットとなっています。

既定では、ディストリビューション方法が選択されていない場合は、ラウンド ロビン ディストリビューション方法が適用されます。  ただし、ラウンド ロビン テーブルは簡単に使用できますが、データがシステム全体でランダムに分散されるため、各行がどのディストリビューションに配置されたかをシステムで保証できないことになります。  その結果、クエリを解決するために、システムでデータの移動操作を呼び出して、データを整理することが必要になる場合があります。  この特別な手順のために、クエリが遅くなる可能性があります。

次のシナリオでは、テーブルにラウンド ロビン ディストリビューションを使用することを検討してください。

* 単純な開始ポイントとして作業を開始する場合
* 明確な結合キーが存在しない場合
* テーブルをハッシュ分散するのに適した候補列がない場合
* テーブルが共通の結合キーを他のテーブルと共有していない場合
* 結合がクエリの他の結合ほど重要ではない場合
* テーブルが一時ステージング テーブルである場合

次のいずれの例でも、ラウンド ロビン テーブルが作成されます。

```SQL
-- Round Robin created by default
CREATE TABLE [dbo].[FactInternetSales]
(   [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
;

-- Explicitly Created Round Robin Table
CREATE TABLE [dbo].[FactInternetSales]
(   [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = ROUND_ROBIN
)
;
```

> [!NOTE]
> ラウンド ロビン方式は既定のテーブルの種類ですが、この方式を DDL で明示することがベスト プラクティスと考えられます。これはテーブル レイアウトの意図が他から明確になるからです。
>
>

### <a name="hash-distributed-tables"></a>ハッシュ分散テーブル
**ハッシュ分散** アルゴリズムを使用してテーブルを分散すると、クエリを実行する際のデータの移動が少なくなり、多くのシナリオでパフォーマンスを向上させることができます。  ハッシュ分散テーブルとは、選択された単一列でハッシュ アルゴリズムを使用して、分散型データベース間で分割されたテーブルのことです。  ディストリビューション列によって、分散型データベース間でデータを分割する方法が決まります。  ハッシュ関数は、ディストリビューション列を使用して分散に行を割り当てます。  ハッシュ アルゴリズムと結果のディストリビューションは確定的です。  同じデータ型の同じ値は常に同じディストリビューションになります。    

次の例では、ID で分散されるテーブルが作成されます。

```SQL
CREATE TABLE [dbo].[FactInternetSales]
(   [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,  DISTRIBUTION = HASH([ProductKey])
)
;
```

## <a name="select-distribution-column"></a>ディストリビューション列を選択する
テーブルを **ハッシュ分散** する場合は、単一のディストリビューション列を選択する必要があります。  ディストリビューション列を選択するとき、考慮する必要がある要素が主に 3 つあります。  

次の条件の単一列を選択します。

1. 更新されない
2. データが均等に分散されていて、データ傾斜を回避できる
3. データの移動を最小化

### <a name="select-distribution-column-which-will-not-be-updated"></a>更新されないディストリビューション列を選択する
ディストリビューション列は更新できないため、静的な値を持つ列を選択します。  列を更新する必要がある場合は、一般的にディストリビューションの候補として適切ではありません。  ディストリビューション列を更新する必要がある場合は、最初に行を削除し、新しい行を挿入することで対処できます。

### <a name="select-distribution-column-which-will-distribute-data-evenly"></a>データを均等に分散するディストリビューション列を選択する
分散システムの実行速度は、最も低速なディストリビューションの速さと同じになるため、システム全体にわたってバランスの取れた実行を実現するには、ディストリビューション全体で作業を均等に分割する必要があります。  作業を分散システムで分割する方法は、各ディストリビューションのデータが配置された場所に基づきます。  これにより、各ディストリビューションの作業が等しくなり、作業のその部分の完了に同じ時間がかかるように、データ分散するための適切なディストリビューション列を選択することが非常に重要になります。  作業をシステム全体で適切に分割すると、ディストリビューション全体でのデータのバランスを取ることができます。  データが均等に分割されていない状態を **データ傾斜**と言います。  

データを均等に分割し、データ傾斜を回避するには、ディストリビューション列を選択するときに次の点を考慮してください。

1. かなり多くの個別の値が含まれている列を選択します。
2. 含まれる個別の値が少ない列でデータを分散しないでください。
3. null になることが多い列でデータを分散しないでください。
4. 日付の列でデータを分散しないでください。

それぞれの値は 60 個のディストリビューションのいずれかにハッシュされるため、均等な分散を実現するには、一意性が高く、一意の値が 61 個以上含まれている列を選択する必要があります。  説明のために、1 列のみが 40 個の値を持つケースを考えます。  この列をディストリビューション キーとして選択した場合、そのテーブルのデータは最大で 40 個のディストリビューションに配置され、残りの 20 個のディストリビューションにはデータが配置されず処理対象もありません。  逆に、その他の 40 個のディストリビューションでは、データが 60 個を超えるディストリビューションに均等に分散された場合より作業が多くなります。  このシナリオが、データ傾斜の例になります。

MPP システムの各クエリ ステップは、すべてのディストリビューションがそれぞれの担当作業を完了するまで待機します。  あるディストリビューションで行う作業が他のディストリビューションよりも多い場合、処理の多いディストリビューションの作業完了を待っている間は、他のディストリビューションのリソースが実質的に無駄になります。  このようにディストリビューション全体で作業が均等に分散されていない状態を、 **処理の傾斜**と言います。  処理の傾斜により、クエリの実行速度は、ワークロードがディストリビューション全体で均等に分散されている場合よりも遅くなります。  処理の傾斜の原因はデータ傾斜です。

null 値はすべて同一のディストリビューションに配置されるため、null になる可能性が高い列では分散を行わないでください。 また、特定の日のデータはすべて同一のディストリビューションに配置されるため、日付列での分散も処理の傾斜の原因となります。 複数のユーザーが同じ日付を条件としてフィルター処理を行うクエリを実行する場合、指定された日付は 1 つのディストリビューションにのみ配置されるため、60 個のディストリビューションのうち 1 つだけがすべての作業を行うことになります。 このようなシナリオでは、クエリの実行速度は、ディストリビューション全体にデータが均等に分散された場合の 60 分の 1 になる可能性があります。

候補として適切な列が存在しない場合は、ディストリビューション方法としてラウンド ロビンを使用することを検討してください。

### <a name="select-distribution-column-which-will-minimize-data-movement"></a>データの移動を最小化するディストリビューション列を選択する
適切なディストリビューション列を選択してデータの移動を最小化することが、SQL Data Warehouse のパフォーマンスを最適化するための最も重要な戦略の 1 つです。  データ移動は、テーブルが結合される、または集計が実行されるときに最もよく起こります。  `JOIN`、`GROUP BY`、`DISTINCT`、`OVER`、および `HAVING` 句で使用される列はすべて、ハッシュ ディストリビューションの候補として**適しています**。

その一方で、 `WHERE` 句内の列は、クエリに参加するディストリビューションを制限し、処理の傾斜を引き起こすため、ハッシュ列の候補には **適していません** 。  分散の対象になる可能性が高いものの処理の傾斜を引き起こす可能性が高い列の典型例としては、日付列があります。

一般的に、頻繁に結合に関係する 2 つの大規模なファクト テーブルがある場合は、いずれかの結合列で分散することで最適なパフォーマンスが得られます。  大規模な別のファクト テーブルに結合されないテーブルがある場合、 `GROUP BY` 句で頻繁に使用される列に注目してください。

結合中のデータ移動を避けるために満たす必要がある重要な基準がいくつかあります。

1. 結合に関係するテーブルでは、結合に参加する列の **いずれか** でハッシュ分散する必要があります。
2. 両方のテーブルで結合列のデータ型が一致する必要があります。
3. 列を equal 演算子で結合する必要があります。
4. 結合の種類に `CROSS JOIN`は許可されません。

## <a name="troubleshooting-data-skew"></a>データ傾斜のトラブルシューティング
ハッシュ ディストリビューション方法でテーブル データを分散するとき、一部のディストリビューションが傾斜し、ディストリビューション間でデータ量が不均衡になることがあります。 過度のデータ傾斜では、最も時間のかかるディストリビューションが終了するまで分散クエリの最終的な結果が待機する必要があるため、クエリのパフォーマンスに影響を与えることがあります。 データ傾斜の程度によっては対処が必要になる場合があります。

### <a name="identifying-skew"></a>傾斜の識別
テーブルの傾斜は、 `DBCC PDW_SHOWSPACEUSED`を使用すると簡単に識別できます。  これは、データベースの 60 のディストリビューションそれぞれに格納されるテーブル行の数を確認するには非常に簡単な方法です。  最もバランスの取れたパフォーマンスを実現するには、分散テーブルの行をディストリビューション全体で均等に広げる必要があることに注意してください。

```sql
-- Find data skew for a distributed table
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

ただし、Azure SQL Data Warehouse 動的管理ビュー (DMV) を照会する場合は、さらに詳細な分析を実行できます。  最初に、[テーブルの概要][概要]に関する記事の SQL を使用して、[dbo.vTableSizes] [dbo.vTableSizes] ビューを作成します。  ビューを作成したら、次のクエリを実行して、どのテーブルでデータ傾斜が 10% を超えるかを識別します。

```sql
select *
from dbo.vTableSizes
where two_part_name in
    (
    select two_part_name
    from dbo.vTableSizes
    where row_count > 0
    group by two_part_name
    having min(row_count * 1.000)/max(row_count * 1.000) > .10
    )
order by two_part_name, row_count
;
```

### <a name="resolving-data-skew"></a>データ傾斜の解決
すべての傾斜に修正プログラムが対応しているわけではありません。  場合によっては、一部のクエリのテーブルのパフォーマンスが、データ傾斜の問題を超える可能性があります。  テーブルでデータ傾斜を解決する必要があるかを決定するには、ワークロード内のデータ ボリュームとクエリについて可能な限り理解する必要があります。   傾斜の影響を確認する方法の 1 つに、[クエリ監視][クエリ監視]に関する記事の手順を使用して、クエリのパフォーマンス、特に個々のディストリビューションでのクエリの完了時間に対する影響を監視する方法があります。

データの分散には、データ傾斜を最小限に抑え、データ移動を最小限に抑える適切なバランスを見つけることが重要です。 これらは相反する目標となる場合がありますが、場合によってはデータ移動を抑えるためにデータ傾斜を保持する必要があります。 たとえば、ディストリビューション列が頻繁に結合および集計に共有の列である場合は、データ移動を最小限に抑えます。 最小限のデータ移動による利点は、データ傾斜による影響を上回る可能性があることです。

データ傾斜を解決する一般的な方法は、他のディストリビューション列を含むテーブルを再作成することです。 既存のテーブルではディストリビューション列を変更できないため、テーブルのディストリビューションを変更するには、[CTAS][] を使用してテーブルを再作成します。  データ傾斜を解決する方法の 2 つの例を次に示します。

### <a name="example-1-re-create-the-table-with-a-new-distribution-column"></a>例 1: 新しいディストリビューション列を含むテーブルを再作成する
この例では、[CTAS][] を使用して、他のハッシュ ディストリビューション列を含むテーブルを再作成します。

```sql
CREATE TABLE [dbo].[FactInternetSales_CustomerKey]
WITH (  CLUSTERED COLUMNSTORE INDEX
     ,  DISTRIBUTION =  HASH([CustomerKey])
     ,  PARTITION       ( [OrderDateKey] RANGE RIGHT FOR VALUES (   20000101, 20010101, 20020101, 20030101
                                                                ,   20040101, 20050101, 20060101, 20070101
                                                                ,   20080101, 20090101, 20100101, 20110101
                                                                ,   20120101, 20130101, 20140101, 20150101
                                                                ,   20160101, 20170101, 20180101, 20190101
                                                                ,   20200101, 20210101, 20220101, 20230101
                                                                ,   20240101, 20250101, 20260101, 20270101
                                                                ,   20280101, 20290101
                                                                )
                        )
    )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
OPTION  (LABEL  = 'CTAS : FactInternetSales_CustomerKey')
;

--Create statistics on new table
CREATE STATISTICS [ProductKey] ON [FactInternetSales_CustomerKey] ([ProductKey]);
CREATE STATISTICS [OrderDateKey] ON [FactInternetSales_CustomerKey] ([OrderDateKey]);
CREATE STATISTICS [CustomerKey] ON [FactInternetSales_CustomerKey] ([CustomerKey]);
CREATE STATISTICS [PromotionKey] ON [FactInternetSales_CustomerKey] ([PromotionKey]);
CREATE STATISTICS [SalesOrderNumber] ON [FactInternetSales_CustomerKey] ([SalesOrderNumber]);
CREATE STATISTICS [OrderQuantity] ON [FactInternetSales_CustomerKey] ([OrderQuantity]);
CREATE STATISTICS [UnitPrice] ON [FactInternetSales_CustomerKey] ([UnitPrice]);
CREATE STATISTICS [SalesAmount] ON [FactInternetSales_CustomerKey] ([SalesAmount]);

--Rename the tables
RENAME OBJECT [dbo].[FactInternetSales] TO [FactInternetSales_ProductKey];
RENAME OBJECT [dbo].[FactInternetSales_CustomerKey] TO [FactInternetSales];
```

### <a name="example-2-re-create-the-table-using-round-robin-distribution"></a>例 2: ラウンド ロビン ディストリビューションを使用してテーブルを再作成する
この例では、[CTAS][] を使用して、ハッシュ ディストリビューションではなくラウンド ロビンを含むテーブルを再作成します。 この変更により、データ移動で増加するコストで、均等なデータ分布が実現します。

```sql
CREATE TABLE [dbo].[FactInternetSales_ROUND_ROBIN]
WITH (  CLUSTERED COLUMNSTORE INDEX
     ,  DISTRIBUTION =  ROUND_ROBIN
     ,  PARTITION       ( [OrderDateKey] RANGE RIGHT FOR VALUES (   20000101, 20010101, 20020101, 20030101
                                                                ,   20040101, 20050101, 20060101, 20070101
                                                                ,   20080101, 20090101, 20100101, 20110101
                                                                ,   20120101, 20130101, 20140101, 20150101
                                                                ,   20160101, 20170101, 20180101, 20190101
                                                                ,   20200101, 20210101, 20220101, 20230101
                                                                ,   20240101, 20250101, 20260101, 20270101
                                                                ,   20280101, 20290101
                                                                )
                        )
    )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
OPTION  (LABEL  = 'CTAS : FactInternetSales_ROUND_ROBIN')
;

--Create statistics on new table
CREATE STATISTICS [ProductKey] ON [FactInternetSales_ROUND_ROBIN] ([ProductKey]);
CREATE STATISTICS [OrderDateKey] ON [FactInternetSales_ROUND_ROBIN] ([OrderDateKey]);
CREATE STATISTICS [CustomerKey] ON [FactInternetSales_ROUND_ROBIN] ([CustomerKey]);
CREATE STATISTICS [PromotionKey] ON [FactInternetSales_ROUND_ROBIN] ([PromotionKey]);
CREATE STATISTICS [SalesOrderNumber] ON [FactInternetSales_ROUND_ROBIN] ([SalesOrderNumber]);
CREATE STATISTICS [OrderQuantity] ON [FactInternetSales_ROUND_ROBIN] ([OrderQuantity]);
CREATE STATISTICS [UnitPrice] ON [FactInternetSales_ROUND_ROBIN] ([UnitPrice]);
CREATE STATISTICS [SalesAmount] ON [FactInternetSales_ROUND_ROBIN] ([SalesAmount]);

--Rename the tables
RENAME OBJECT [dbo].[FactInternetSales] TO [FactInternetSales_HASH];
RENAME OBJECT [dbo].[FactInternetSales_ROUND_ROBIN] TO [FactInternetSales];
```

## <a name="next-steps"></a>次のステップ
テーブル デザインの詳細について、[分散][分散]、[Index][Index]、[Partition][Partition]、[データ型][データ型]、[統計][統計]、[一時テーブル][一時]に関する各記事をご覧ください。

ベスト プラクティスの概要については、[SQL Data Warehouse のベスト プラクティス][SQL Data Warehouse のベスト プラクティス]をご覧ください。

<!--Image references-->

<!--Article references-->
[概要]: ./sql-data-warehouse-tables-overview.md
[データ型]: ./sql-data-warehouse-tables-data-types.md
[分散]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[統計]: ./sql-data-warehouse-tables-statistics.md
[一時]: ./sql-data-warehouse-tables-temporary.md
[SQL Data Warehouse のベスト プラクティス]: ./sql-data-warehouse-best-practices.md
[クエリ監視]: ./sql-data-warehouse-manage-monitor.md
[dbo.vTableSizes]: ./sql-data-warehouse-tables-overview.md#table-size-queries

<!--MSDN references-->
[DBCC PDW_SHOWSPACEUSED]: https://msdn.microsoft.com/library/mt204028.aspx

<!--Other Web references-->



<!--HONumber=Nov16_HO3-->


