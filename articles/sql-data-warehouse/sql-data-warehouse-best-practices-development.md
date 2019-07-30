---
title: Azure SQL Data Warehouse の開発のベスト プラクティス | Microsoft Docs
description: Azure SQL Data Warehouse のソリューションを開発する際に知っておく必要がある推奨事項とベスト プラクティス。
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 09/04/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 7e0ae5e6159ae0ab4d098d717f433d2ab63770d4
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479733"
---
# <a name="development-best-practices-for-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse の開発のベスト プラクティス
この記事では、独自のデータ ウェアハウス ソリューションを開発するにあたってのガイダンスとベスト プラクティスについて説明します。 

## <a name="reduce-cost-with-pause-and-scale"></a>一時停止とスケールでコストを削減する
一時停止とスケーリングを通じてコストを削減する方法については、[コンピューティングの管理](sql-data-warehouse-manage-compute-overview.md)に関するページを参照してください。 


## <a name="maintain-statistics"></a>統計を管理する
毎日または各読み込みの後に必ず統計を更新します。  パフォーマンスと統計を作成および更新するコストの間には常にトレードオフの関係が存在します。 すべての統計を管理するのは時間がかかりすぎる場合は、統計を作成する列や頻繁に更新する列を限定することをお勧めします。  たとえば、新しい値が毎日追加される日付列を更新します。 **結合に使用されている列、WHERE 句で使用されている列、および GROUP BY に含まれている列に関する統計を作成すると、最も大きなメリットが得られます。**

[テーブル統計の管理][Manage table statistics]、, [CREATE STATISTICS][CREATE STATISTICS]、[UPDATE STATISTICS][UPDATE STATISTICS] に関するページもご覧ください。

## <a name="hash-distribute-large-tables"></a>ハッシュで大規模なテーブルを分散させる
既定では、テーブルはラウンド ロビン分散です。  そのため、ユーザーはテーブルの分散方法を決定することなくテーブルの作成を簡単に開始できます。  ラウンド ロビン テーブルは一部のワークロードでは十分なパフォーマンスを示しますが、多くの場合、分散列を選択すると、パフォーマンスが大幅に向上します。  列で分散したテーブルのパフォーマンスがラウンド ロビン テーブルをはるかに上回る最も一般的な例としては、2 つの大規模なファクト テーブルが結合されている場合が挙げられます。  たとえば、orders テーブルが order_id で分散されており、transactions テーブルも order_id で分散されている場合に、orders テーブルを transactions テーブルに order_id で結合すると、このクエリはパススルー クエリになり、データの移動処理が行われなくなります。  手順が減るため、クエリは高速になります。  また、データの移動の減少もクエリの高速化に貢献します。  ここでは、大まかにのみ説明します。 分散テーブルを読み込む場合は、受信データを分散キーで並べ替えないでください。読み込みが遅くなります。  分散列を選択するとパフォーマンスがどのように向上するのかや、CREATE TABLES ステートメントの WITH 句で分散テーブルを定義する方法の詳細については、次のリンクを参照してください。

[テーブルの概要][Table overview]、, [Table distribution][Table distribution]、[テーブル分散の選択][テーブル分散の選択]、[CREATE TABLE][CREATE TABLE]、, [CREATE TABLE AS SELECT][CREATE TABLE AS SELECT] に関するページもご覧ください。

## <a name="do-not-over-partition"></a>パーティション分割しすぎないようにする
データをパーティション分割すると、パーティション切り替えを利用してデータを管理したり、パーティションを除外してスキャンを最適化したりできるため、非常に有用ですが、パーティションが多すぎると、クエリの速度が低下する場合があります。  多くの場合、高い粒度でパーティション分割する戦略は、SQL Server では効果的ですが、SQL Data Warehouse では効果的ではありません。  パーティションが多すぎると、各パーティションの行数が 100 万を下回る場合に、クラスター化列ストア インデックスの効果が減少する可能性もあります。  SQL Data Warehouse では、バックグラウンドでデータを 60 個のデータベースにパーティション分割します。そのため、パーティションが 100 個あるテーブルを作成すると、実質的にはパーティションが 6000 個になることに留意してください。  ワークロードはそれぞれに異なるため、パーティション分割を試して、自分のワークロードに最適な数を判断することをお勧めします。  SQL Server の場合よりも粒度を下げることを検討してください。  たとえば、日単位ではなく、週単位や月単位のパーティションを使用します。

[テーブル パーティション][Table partitioning]

## <a name="minimize-transaction-sizes"></a>トランザクション サイズを最小限に抑える
トランザクションで INSERT、UPDATE、DELETE ステートメントを実行して、失敗した場合は、ロールバックする必要があります。  ロールバック時間が長くならないようにするには、できる限りトランザクション サイズを最小限に抑えます。  そのためには、INSERT、UPDATE、DELETE ステートメントを複数に分割します。  たとえば、INSERT に 1 時間かかると予測される場合は、可能であれば、INSERT を 4 つに分割すると、それぞれの実行時間は 15 分になります。  CTAS、TRUNCATE、DROP TABLE、空のテーブルへの INSERT など、特殊な最小ログ記録のケースを活用すると、ロールバックのリスクが軽減されます。  ロールバックを回避するもう 1 つの方法としては、データ管理のためのパーティション切り替えなど、メタデータのみの操作を使用します。  たとえば、DELETE ステートメントを実行して、テーブル内の order_date が 2001 年 10 月のすべての行を削除する代わりに、月単位でデータをパーティション分割し、該当するデータを含むパーティションを別のテーブルの空のパーティションに切り替えします (ALTER TABLE の例を参照してください)。  パーティション分割されていないテーブルについては、DELETE を使用する代わりに、CTAS を使用して、テーブルに保持するデータを書き込むことを検討してください。  CTAS にかかる時間が同じ場合でも、トランザクション ログが最小限に抑えられ、必要なときにすばやく取り消すことができるため、CTAS は非常に安全に実行できる操作です。

[トランザクションの概要][Understanding transactions]、, [Optimizing transactions][Optimizing transactions]、[テーブル パーティション][テーブル パーティション]、[TRUNCATE TABLE][TRUNCATE TABLE]、, [ALTER TABLE][ALTER TABLE]、[CREATE TABLE AS SELECT (CTAS)][CREATE TABLE AS SELECT (CTAS)] に関するページもご覧ください。

## <a name="use-the-smallest-possible-column-size"></a>できる限り最小の列サイズを使用する
DDL を定義するときに、データをサポートする最小のデータ型を使用すると、クエリのパフォーマンスが向上します。  これは、CHAR および VARCHAR 列の場合に特に重要です。  列の最長の値が 25 文字の場合は、列を VARCHAR(25) として定義します。  すべての文字列を既定の長さで定義しないようにします。  さらに、VARCHAR で済む場合は、NVARCHAR を使用せずに、列を VARCHAR として定義します。

[テーブルの概要][Table overview]、, [Table data types][Table data types]、[CREATE TABLE][CREATE TABLE] に関するページもご覧ください。

## <a name="optimize-clustered-columnstore-tables"></a>クラスター化列ストア テーブルを最適化する
クラスター化列ストア インデックスは、SQL Data Warehouse にデータを格納する最も効率的な方法の 1 つです。  既定では、SQL Data Warehouse のテーブルは、クラスター化列ストアとして作成されます。  列ストア テーブルに対するクエリのパフォーマンスを最大限に引き出すには、セグメントの質が高いことが重要です。  行を列ストア テーブルに書き込む際にメモリ負荷が発生すると、列ストア セグメントの質が低下する可能性があります。  セグメントの質は、圧縮後の行グループに含まれる行の数を使って判断できます。  クラスター化列ストア テーブルのセグメントの品質を検出して向上させる詳細な手順については、in the [Table indexes][Table indexes] に関する記事の「[列ストア インデックスの品質の低さの原因][Causes of poor columnstore index quality]」を参照してください。  列ストア セグメントの質を高めることが非常に重要であるため、中規模または大規模リソース クラスのユーザー ID を使用してデータを読み込むことをお勧めします。 低い[データ ウェアハウス ユニット](what-is-a-data-warehouse-unit-dwu-cdwu.md)を使用すると、大きいリソース クラスを読み込みユーザーに割り当てることになります。

通常、テーブルあたりの行数が 100 万を超え、各 SQL Data Warehouse テーブルが 60 個にパーティション分割されるまで、列ストア テーブルは圧縮された列ストア セグメントにデータをプッシュしません。そのため、経験上、テーブルの行数が 6,000 万を超えない限り、列ストア テーブルはクエリにとってメリットがありません。  6,000 万行未満のテーブルについては、列ストア インデックスを作成してもメリットがありません。  また、デメリットもありません。  さらに、データをパーティション分割する場合は、クラスター化列ストア インデックスの恩恵を受けるには、各パーティションに 100 万行が必要なことを考慮に入れる必要があります。  テーブルに 100 個のパーティションがある場合に、クラスター化列ストアの恩恵を受けるには、少なくとも 60 億行必要です (60 個のディストリビューション * 100 個のパーティション * 100 万行)。  この例でテーブルに 60 億行もない場合は、パーティションの数を減らすか、代わりにヒープ テーブルを使用することを検討してください。  列ストア テーブルの代わりに、ヒープ テーブルとセカンダリ インデックスを使用してパフォーマンスが向上するかどうかを試してみる価値もあります。

列ストア テーブルに対してクエリを実行する場合は、必要な列のみを選択すると、クエリの実行速度が向上します。  

[テーブル インデックス][Table indexes]、, [Columnstore indexes guide][Columnstore indexes guide]、[列ストア インデックスの再構築][列ストア インデックスの再構築]に関するページもご覧ください。

## <a name="next-steps"></a>次の手順
この記事で目的のトピックが見つからない場合は、ページの左側にある [Search for docs] を使用して、すべての Azure SQL Data Warehouse ドキュメントで検索を実行してみてください。  [Azure SQL Data Warehouse フォーラム][Azure SQL Data Warehouse MSDN Forum] is a place for you to ask questions to other users and to the SQL Data Warehouse Product Group.  We actively monitor this forum to ensure that your questions are answered either by another user or one of us.  If you prefer to ask your questions on Stack Overflow, we also have an [Azure SQL Data Warehouse Stack Overflow Forum][Azure SQL Data Warehouse Stack Overflow Forum]。

最後に、 [Azure SQL Data Warehouse のフィードバック][Azure SQL Data Warehouse Feedback] ページを使用して、機能に関するご要望を是非お寄せください。  要望の追加や他の要求への投票は、機能の優先順位を決める際に役立ちます。

<!--Image references-->

<!--Article references-->
[Create a support ticket]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Create table as select (CTAS)]: ./sql-data-warehouse-develop-ctas.md
[Table overview]: ./sql-data-warehouse-tables-overview.md
[Table data types]: ./sql-data-warehouse-tables-data-types.md
[Table distribution]: ./sql-data-warehouse-tables-distribute.md
[Table indexes]: ./sql-data-warehouse-tables-index.md
[Causes of poor columnstore index quality]: ./sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality
[Rebuilding columnstore indexes]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Table partitioning]: ./sql-data-warehouse-tables-partition.md
[Manage table statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary tables]: ./sql-data-warehouse-tables-temporary.md
[Guide for using PolyBase]: ./guidance-for-loading-data.md
[Load data]: ./design-elt-data-loading.md
[Move data with Azure Data Factory]: ../data-factory/transform-data-using-machine-learning.md
[Load data with Azure Data Factory]: ../data-factory/load-azure-sql-data-warehouse.md
[Load data with bcp]: /sql/tools/bcp-utility
[Load data with PolyBase]: ./load-data-wideworldimportersdw.md
[Monitor your workload using DMVs]: ./sql-data-warehouse-manage-monitor.md
[Pause compute resources]: ./sql-data-warehouse-manage-compute-overview.md#pause-compute-bk
[Resume compute resources]: ./sql-data-warehouse-manage-compute-overview.md#resume-compute-bk
[Scale compute resources]: ./sql-data-warehouse-manage-compute-overview.md#scale-compute
[Understanding transactions]: ./sql-data-warehouse-develop-transactions.md
[Optimizing transactions]: ./sql-data-warehouse-develop-best-practices-transactions.md
[Troubleshooting]: ./sql-data-warehouse-troubleshoot.md
[LABEL]: ./sql-data-warehouse-develop-label.md

<!--MSDN references-->
[ALTER TABLE]: https://msdn.microsoft.com/library/ms190273.aspx
[CREATE EXTERNAL FILE FORMAT]: https://msdn.microsoft.com/library/dn935026.aspx
[CREATE STATISTICS]: https://msdn.microsoft.com/library/ms188038.aspx
[CREATE TABLE]: https://msdn.microsoft.com/library/mt203953.aspx
[CREATE TABLE AS SELECT]: https://msdn.microsoft.com/library/mt204041.aspx
[DBCC PDW_SHOWEXECUTIONPLAN]: https://msdn.microsoft.com/library/mt204017.aspx
[INSERT]: https://msdn.microsoft.com/library/ms174335.aspx
[OPTION]: https://msdn.microsoft.com/library/ms190322.aspx
[TRUNCATE TABLE]: https://msdn.microsoft.com/library/ms177570.aspx
[UPDATE STATISTICS]: https://msdn.microsoft.com/library/ms187348.aspx
[sys.dm_exec_sessions]: https://msdn.microsoft.com/library/ms176013.aspx
[sys.dm_pdw_exec_requests]: https://msdn.microsoft.com/library/mt203887.aspx
[sys.dm_pdw_request_steps]: https://msdn.microsoft.com/library/mt203913.aspx
[sys.dm_pdw_sql_requests]: https://msdn.microsoft.com/library/mt203889.aspx
[sys.dm_pdw_dms_workers]: https://msdn.microsoft.com/library/mt203878.aspx
[sys.dm_pdw_waits]: https://msdn.microsoft.com/library/mt203893.aspx
[Columnstore indexes guide]: https://msdn.microsoft.com/library/gg492088.aspx

<!--Other Web references-->
[Selecting table distribution]: https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/
[Azure SQL Data Warehouse Feedback]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Azure SQL Data Warehouse MSDN Forum]: https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse
[Azure SQL Data Warehouse Stack Overflow Forum]:  https://stackoverflow.com/questions/tagged/azure-sqldw
[Azure SQL Data Warehouse loading patterns and strategies]: https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/
