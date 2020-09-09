---
title: Azure Synapse Analytics (旧称 SQL DW) での Synapse SQL プールのベスト プラクティス
description: Azure Synapse Analytics (旧称 SQL DW) で SQL プールのソリューションを開発するための推奨事項とベスト プラクティス。
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 6978855e9b32a3842e76d02ef543d86cf0673019
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85206650"
---
# <a name="best-practices-for-synapse-sql-pool-in-azure-synapse-analytics-formerly-sql-dw"></a>Azure Synapse Analytics (旧称 SQL DW) での Synapse SQL プールのベスト プラクティス

この記事には、[SQL プール](sql-data-warehouse-overview-what-is.md)のデプロイで最適なパフォーマンスを実現するのに役立つベスト プラクティスがまとめられています。  この記事の目的は、基本的なガイダンスをいくつか提供し、注目すべき重要な領域を強調することです。  

## <a name="reduce-cost-with-pause-and-scale"></a>一時停止とスケールでコストを削減する

一時停止とスケーリングを通じてコストを削減する方法については、[コンピューティングの管理](sql-data-warehouse-manage-compute-overview.md)に関するページを参照してください。

## <a name="maintain-statistics"></a>統計を管理する

列の統計を自動的に検出して作成するように、SQL プールを構成できます。  オプティマイザーによって作成されたクエリ プランの有効性は、使用可能な統計によって決まります。  

ご使用のデータベースに対して AUTO_CREATE_STATISTICS を有効にし、クエリで使用されている列の統計が確実に最新の状態になるように、統計を毎日または読み込みのたびに更新することをお勧めします。

すべての統計を更新するのに時間がかかりすぎる場合は、統計を頻繁に更新する必要がある列を限定することをお勧めします。 たとえば、新しい値が毎日追加される日付列を更新します。

> [!TIP]
> 結合に使用されている列、WHERE 句で使用されている列、および GROUP BY に含まれている列に関する統計を更新すると、最も大きなメリットが得られます。

[テーブル統計の管理](sql-data-warehouse-tables-statistics.md)、[CREATE STATISTICS](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)、[UPDATE STATISTICS](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) に関するページも参照してください。

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>DMV を使用して、クエリを監視および最適化する

SQL プールには、クエリの実行を監視するために使用できる DMV がいくつか用意されています。  DMV を使用したワークロードの監視に関するページでは、実行中のクエリの詳細を確認する方法の詳細な手順が説明されています。  

これらの DMV でクエリをすばやく見つけるには、クエリで LABEL オプションを使用すると便利です。

[DMV を使用したワークロードの監視](sql-data-warehouse-manage-monitor.md)、[LABEL](sql-data-warehouse-develop-label.md)、[OPTION](/sql/t-sql/queries/option-clause-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)、[sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)、[sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)、[sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)、[sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)、[sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)、[DBCC PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)、[sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) に関するページも参照してください。

## <a name="tune-query-performance-with-new-product-enhancements"></a>新しい製品の機能強化でクエリのパフォーマンスを調整する

- [具体化されたビューを使用したパフォーマンスのチューニング](performance-tuning-materialized-views.md)
- [順序指定クラスター化列ストア インデックスを使用したパフォーマンスのチューニング](performance-tuning-ordered-cci.md)
- [結果セットのキャッシュを使用したパフォーマンスのチューニング](performance-tuning-result-set-caching.md)

## <a name="group-insert-statements-into-batches"></a>INSERT ステートメントをバッチにグループ化する

INSERT ステートメントで小さなテーブルに 1 回だけ読み込む場合や、検索を定期的に再読み込みする場合は、`INSERT INTO MyLookup VALUES (1, 'Type 1')` などのステートメントで、ニーズに十分応えることができます。  

ただし、1 日を通して数千や数百万行を読み込む必要がある場合、シングルトンの INSERT だけでは対応できない可能性があります。  代わりに、ファイルに書き込み、定期的に別のプロセスを利用してそのファイルを読み込むプロセスを作成します。

[INSERT](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) に関するページも参照してください。

## <a name="use-polybase-to-load-and-export-data-quickly"></a>PolyBase を使用して、データの読み込みとエクスポートをすばやく実行する

SQL プールでは、Azure Data Factory、PolyBase、BCP など、さまざまなツールを使用したデータの読み込みとエクスポートがサポートされています。  パフォーマンスが重要でない少量のデータについては、どのツールでもニーズに十分応えることができます。  ただし、大量のデータを読み込んだり、エクスポートしたりする場合や、高速なパフォーマンスが必要な場合は、PolyBase が最適な選択肢です。  

PolyBase は、MPP (超並列処理) アーキテクチャを活用するように設計されており、他のツールよりも速く大量のデータを読み込むことや、エクスポートすることができます。  PolyBase の読み込みは、CTAS または INSERT INTO を使用して実行できます。  

> [!TIP]
> CTAS を使用すると、トランザクション ログが最小限に抑えられ、データを一番速く読み込むことができます。

Azure Data Factory では PolyBase の読み込みもサポートされ、CTAS と同様のパフォーマンスを実現できます。  PolyBase では、Gzip ファイルなど、さまざまなファイル形式をサポートしています。  
  
> [!NOTE]
> gzip テキスト ファイルを使用する場合にスループットを最大限引き上げるには、ファイルを 60 個以上に分割して、読み込みの並列処理を最大化してください。  全体のスループットを引き上げるには、データを同時に読み込むことを検討してください。

[データの読み込み](design-elt-data-loading.md)、[PolyBase の使用ガイド](guidance-for-loading-data.md)、[SQL プールの読み込みパターンと戦略](https://blogs.msdn.microsoft.com/sqlcat/20../../)、[Azure Data Factory を使用したデータの読み込み]( ../../data-factory/load-azure-sql-data-warehouse.md)、[Azure Data Factory を使用したデータ移動](../../data-factory/transform-data-using-machine-learning.md)、[CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)、[Create table as select (CTAS)](sql-data-warehouse-develop-ctas.md) に関するページも参照してください。

## <a name="load-then-query-external-tables"></a>外部テーブルを読み込んで、クエリを実行する

外部テーブルとも呼ばれる Polybase は、最も高速にデータを読み込むことができますが、クエリには適していません。 Polybase テーブルで現在サポートされているのは、Azure BLOB ファイルと Azure Data Lake ストレージのみです。 このファイルには、それをバックアップするためのコンピューティング リソースがありません。  

つまり、SQL プールではこの作業をオフロードできないため、データを読み取るには、ファイルを tempdb に読み込んで、ファイル全体を読み取る必要があります。  したがって、このデータに対して複数のクエリを実行する場合は、データを一度読み込んで、クエリがローカル テーブルを使うように指定することをお勧めします。

[PolyBase の使い方ガイド](guidance-for-loading-data.md)も参照してください。

## <a name="hash-distribute-large-tables"></a>ハッシュで大規模なテーブルを分散させる

既定では、テーブルはラウンド ロビン分散です。  そのため、ユーザーはテーブルの分散方法を決定することなくテーブルの作成を簡単に開始できます。  ラウンド ロビン テーブルは一部のワークロードでは十分なパフォーマンスを示しますが、多くの場合、分散列を選択すると、パフォーマンスが大幅に向上します。  

列で分散したテーブルのパフォーマンスがラウンド ロビン テーブルをはるかに上回る最も一般的な例としては、2 つの大規模なファクト テーブルが結合されている場合が挙げられます。  

たとえば、orders テーブルが order_id で分散されており、transactions テーブルも order_id で分散されている場合に、orders テーブルを transactions テーブルに order_id で結合すると、このクエリはパススルー クエリになり、データの移動処理が行われなくなります。  手順が減るため、クエリは高速になります。  また、データの移動の減少もクエリの高速化に貢献します。  

> [!TIP]
> 分散テーブルを読み込む場合は、受信データを分散キーで並べ替えないでください。読み込みが遅くなります。  

分散列を選択した場合にパフォーマンスが向上するしくみや、CREATE TABLE ステートメントの WITH 句で分散テーブルを定義する方法の詳細については、次のリンクを参照してください。

[テーブルの概要](sql-data-warehouse-tables-overview.md)、[テーブル分散](sql-data-warehouse-tables-distribute.md)、[テーブル分散の選択](https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/)、[CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)、[CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) に関するページも参照してください。

## <a name="do-not-over-partition"></a>パーティション分割しすぎないようにする

データをパーティション分割すると、パーティション切り替えを利用してデータを管理したり、パーティションを除外してスキャンを最適化したりできるため、有用ですが、パーティションが多すぎると、クエリの速度が低下する場合があります。  多くの場合、高い粒度でパーティション分割する戦略は、SQL Server では効果的ですが、SQL プールでは効果的ではありません。  

パーティションが多すぎると、各パーティションの行数が 100 万を下回る場合に、クラスター化列ストア インデックスの効果が減少する可能性もあります。  SQL プールでは、バックグラウンドでデータを 60 個のデータベースにパーティション分割します。そのため、パーティションが 100 個あるテーブルを作成すると、実質的にはパーティションが 6000 個になることに留意してください。  

ワークロードはそれぞれに異なるため、パーティション分割を試して、自分のワークロードに最適な数を判断することをお勧めします。  SQL Server の場合よりも粒度を下げることを検討してください。  たとえば、日単位ではなく、週単位や月単位のパーティションを使用します。

[テーブル パーティション](sql-data-warehouse-tables-partition.md)に関するページも参照してください。

## <a name="minimize-transaction-sizes"></a>トランザクション サイズを最小限に抑える

トランザクションで INSERT、UPDATE、DELETE ステートメントを実行して、失敗した場合は、ロールバックする必要があります。  ロールバック時間が長くならないようにするには、できる限りトランザクション サイズを最小限に抑えます。  そのためには、INSERT、UPDATE、DELETE ステートメントを複数に分割します。  

たとえば、INSERT に 1 時間かかると予測される場合は、可能であれば、INSERT を 4 つに分割すると、それぞれの実行時間は 15 分になります。  CTAS、TRUNCATE、DROP TABLE、空のテーブルへの INSERT など、特殊な最小ログ記録のケースを活用すると、ロールバックのリスクが軽減されます。  

ロールバックを回避するもう 1 つの方法としては、データ管理のためのパーティション切り替えなど、メタデータのみの操作を使用します。  たとえば、DELETE ステートメントを実行して、テーブル内の order_date が 2001 年 10 月のすべての行を削除する代わりに、月単位でデータをパーティション分割し、該当するデータを含むパーティションを別のテーブルの空のパーティションに切り替えします ([ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) の例を参照してください)。  

パーティション分割されていないテーブルについては、DELETE を使用する代わりに、CTAS を使用して、テーブルに保持するデータを書き込むことを検討してください。  CTAS にかかる時間が同じ場合でも、トランザクション ログが最小限に抑えられ、必要なときにすばやく取り消すことができるため、CTAS は非常に安全に実行できる操作です。

[トランザクションの概要](sql-data-warehouse-develop-transactions.md)、[トランザクションの最適化](sql-data-warehouse-develop-best-practices-transactions.md)、[テーブル パーティション](sql-data-warehouse-tables-partition.md)、[TRUNCATE TABLE](/sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)、[ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)、[Create table as select (CTAS)](sql-data-warehouse-develop-ctas.md) に関するページも参照してください。

## <a name="reduce-query-result-sizes"></a>クエリ結果のサイズを縮小する

この手順は、大きなクエリ結果によって発生するクライアント側の問題を回避するのに役立ちます。  クエリを編集して、返される行の数を減らすことができます。 クエリ生成ツールによって、各クエリに "上位 N" 構文を追加することができます。  また、クエリ結果を一時テーブルに CETAS を行ってから、ダウンレベル処理に PolyBase エクスポートを使用することもできます。

## <a name="use-the-smallest-possible-column-size"></a>できる限り最小の列サイズを使用する

DDL を定義するときに、データをサポートする最小のデータ型を使用すると、クエリのパフォーマンスが向上します。  この方法は、CHAR 列と VARCHAR 列では特に重要です。  

列の最長の値が 25 文字の場合は、列を VARCHAR(25) として定義します。  すべての文字列を既定の長さで定義しないようにします。  さらに、VARCHAR で済む場合は、NVARCHAR を使用せずに、列を VARCHAR として定義します。

[テーブルの概要](sql-data-warehouse-tables-overview.md)、[テーブルのデータ型](sql-data-warehouse-tables-data-types.md)、[CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) に関するページも参照してください。

## <a name="use-temporary-heap-tables-for-transient-data"></a>一時的なデータには一時的なヒープ テーブルを使用する

データを一時的に読み込む際は、ヒープ テーブルを使用すると、プロセス全体が高速になる場合があります。  さらに変換を実行する前で、ステージングにのみデータを読み込んでいる場合は、ヒープ テーブルにデータを読み込むほうが、クラスター化列ストア テーブルにデータを読み込むよりも高速になります。  

さらに、テーブルを永続記憶域に読み込むよりも、データを一時テーブルに読み込んだ方が読み込み速度が大幅に向上します。  一時テーブルは、"#" で始まり、作成元のセッションからしかアクセスできないため、一部のシナリオでしか動作しません。

ヒープ テーブルは、CREATE TABLE の WITH 句で定義します。  一時テーブルを使用する場合は、その一時テーブルの統計も必ず作成してください。

[一時テーブル](sql-data-warehouse-tables-temporary.md)、[CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)、[CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) に関するページも参照してください。

## <a name="optimize-clustered-columnstore-tables"></a>クラスター化列ストア テーブルを最適化する

クラスター化列ストア インデックスは、SQL プールにデータを格納する最も効率的な方法の 1 つです。  既定では、SQL プールのテーブルは、クラスター化列ストアとして作成されます。  列ストア テーブルに対するクエリのパフォーマンスを最大限に引き出すには、セグメントの質が高いことが重要です。  

行を列ストア テーブルに書き込む際にメモリ負荷が発生すると、列ストア セグメントの質が低下する可能性があります。  セグメントの質は、圧縮後の行グループに含まれる行の数を使って判断できます。  クラスター化列ストア テーブルのセグメントの質を検出して向上させる詳細な手順については、[テーブル インデックス](sql-data-warehouse-tables-index.md)に関するページの「[列ストア インデックスの品質の低さの原因](sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality)」を参照してください。  

列ストア セグメントの質を高めることが非常に重要であるため、中規模または大規模リソース クラスのユーザー ID を使用してデータを読み込むことをお勧めします。 低い[データ ウェアハウス ユニット](what-is-a-data-warehouse-unit-dwu-cdwu.md)を使用すると、大きいリソース クラスを読み込みユーザーに割り当てることになります。

通常、テーブルあたりの行数が 100 万を超え、各 SQL プール テーブルが 60 個にパーティション分割されるまで、列ストア テーブルは圧縮された列ストア セグメントにデータをプッシュしません。そのため、経験上、テーブルの行数が 6,000 万を超えない限り、列ストア テーブルはクエリにとってメリットがありません。  6,000 万行未満のテーブルについては、列ストア インデックスを作成してもメリットがありません。  また、デメリットもありません。  

さらに、データをパーティション分割する場合は、クラスター化列ストア インデックスの恩恵を受けるには、各パーティションに 100 万行が必要なことを考慮に入れる必要があります。  テーブルに 100 個のパーティションがある場合に、クラスター化列ストアの恩恵を受けるには、少なくとも 60 億行必要です (60 個のディストリビューション "*100 個のパーティション*" 100 万行)。  

この例でテーブルに 60 億行もない場合は、パーティションの数を減らすか、代わりにヒープ テーブルを使用することを検討してください。  列ストア テーブルの代わりに、ヒープ テーブルとセカンダリ インデックスを使用してパフォーマンスが向上するかどうかを試してみる価値もあります。

> [!TIP]
> 列ストア テーブルに対してクエリを実行する場合は、必要な列のみを選択すると、クエリの実行速度が向上します。  

[テーブル インデックス](sql-data-warehouse-tables-index.md)、[列ストア インデックス](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)、[列ストア インデックスの再構築](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality)に関するページもご覧ください。

## <a name="use-larger-resource-class-to-improve-query-performance"></a>大きなリソース クラスを使用して、クエリのパフォーマンスを向上させる

SQL プールでは、クエリにメモリを割り当てる方法としてリソース グループが使用されます。  既定では、ディストリビューションごとに 100 MB のメモリが与えられる小規模リソース クラスにすべてのユーザーが割り当てられます。  常に 60 個のディストリビューションが存在し、各ディストリビューションに最低 100 MB が割り当てられるため、システム全体のメモリの割り当ての合計は 6,000 MB (6 GB 弱) です。  

大規模な結合やクラスター化列ストア テーブルへの読み込みなど、特定のクエリについては、割り当てるメモリを増やすと効果的です。  純粋なスキャンなどのクエリでは、効果はありません。  一方で、より大きなリソース クラスを利用すると、コンカレンシーが減ります。そのため、すべてのユーザーをより大きなリソース クラスに移行する前に、この影響を考慮する必要があります。

「[ワークロード管理用のリソース クラス](resource-classes-for-workload-management.md)」も参照してください。

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>小さいリソース クラスを使用して、コンカレンシーを増やす

ユーザー クエリの遅延が長いと感じている場合は、ユーザーが大きなリソース クラスで実行しており、コンカレンシー スロットを大量に使用していることが原因で、他のクエリがキューに配置されている可能性があります。  ユーザー クエリがキューに配置されているかどうかを確認するには、 `SELECT * FROM sys.dm_pdw_waits` を実行して、行が返されるかどうかを確認します。

「[ワークロード管理用のリソース クラス](resource-classes-for-workload-management.md)」、[sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) に関するページも参照してください。

## <a name="other-resources"></a>その他のリソース

一般的な問題と解決方法については、 [トラブルシューティング](sql-data-warehouse-troubleshoot.md) に関する記事もご覧ください。

この記事で目的のトピックが見つからなかった場合は、ページの左側にある [Search for docs] を使用して、すべての Azure Synapse ドキュメントで検索を実行してみてください。  [Azure Synapse の Microsoft Q&A 質問ページ](https://docs.microsoft.com/answers/topics/azure-synapse-analytics.html)は、他のユーザーや Azure Synapse 製品グループに質問を投稿できる場所です。 Microsoft では、このフォーラムを積極的に監視し、お客様からの質問に他のユーザーや Microsoft のスタッフが回答しているかどうかを確認しています。  

Stack Overflow で質問したい方のために、[Azure Synapse Stack Overflow フォーラム](https://stackoverflow.com/questions/tagged/azure-sqldw)も用意しています。

[Azure Synapse のフィードバック](https://feedback.azure.com/forums/307516-sql-data-warehouse) ページを使用して、機能に関するご要望を是非お寄せください。  要望の追加や他の要求への投票は、機能の優先順位を決める際に役立ちます。
