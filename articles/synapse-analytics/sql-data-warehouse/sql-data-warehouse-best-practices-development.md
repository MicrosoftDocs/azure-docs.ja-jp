---
title: 開発のベスト プラクティス
description: Synapse SQL プールのソリューションを開発する際に知っておく必要がある推奨事項とベスト プラクティス。
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 09/04/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: bc101e6bb743b9290593937a4d1bac656430b438
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98685312"
---
# <a name="development-best-practices-for-synapse-sql-pool"></a>Synapse SQL プール向けの開発に関するベスト プラクティス

この記事では、SQL プール ソリューションを開発する際のガイダンスとベスト プラクティスについて説明します。

## <a name="tune-query-performance-with-new-product-enhancements"></a>新しい製品の機能強化でクエリのパフォーマンスを調整する

- [具体化されたビューを使用したパフォーマンスのチューニング](performance-tuning-materialized-views.md)
- [順序指定クラスター化列ストア インデックスを使用したパフォーマンスのチューニング](performance-tuning-ordered-cci.md)
- [結果セットのキャッシュを使用したパフォーマンスのチューニング](performance-tuning-result-set-caching.md)

## <a name="reduce-cost-with-pause-and-scale"></a>一時停止とスケールでコストを削減する

一時停止とスケーリングを通じてコストを削減する方法については、[コンピューティングの管理](sql-data-warehouse-manage-compute-overview.md)に関する記事を参照してください。

## <a name="maintain-statistics"></a>統計を管理する

列の統計を自動的に検出して作成するように、SQL プールを構成できます。  オプティマイザーによって作成されたクエリ プランの有効性は、使用可能な統計によって決まります。  

ご使用のデータベースに対して AUTO_CREATE_STATISTICS を有効にし、クエリで使用されている列の統計が確実に最新の状態になるように、統計を毎日または読み込みのたびに更新することをお勧めします。

すべての統計を更新するのに時間がかかりすぎる場合は、統計を頻繁に更新する必要がある列を限定することをお勧めします。 たとえば、新しい値が毎日追加される日付列を更新します。

> [!TIP]
> 結合に使用されている列、WHERE 句で使用されている列、および GROUP BY に含まれている列に関する統計を更新すると、最も大きなメリットが得られます。

[テーブル統計の管理](sql-data-warehouse-tables-statistics.md)、[CREATE STATISTICS](sql-data-warehouse-tables-statistics.md)、[UPDATE STATISTICS](sql-data-warehouse-tables-statistics.md#update-statistics) に関するページも参照してください。

## <a name="hash-distribute-large-tables"></a>ハッシュで大規模なテーブルを分散させる

既定では、テーブルはラウンド ロビン分散です。  この設計により、ユーザーはテーブルの分散方法を決定することなくテーブルの作成を簡単に開始できます。  

ラウンド ロビン テーブルは一部のワークロードでは十分なパフォーマンスを示しますが、多くの場合、分散列を選択すると、パフォーマンスが大幅に向上します。  列で分散したテーブルのパフォーマンスがラウンド ロビン テーブルをはるかに上回る最も一般的な例としては、2 つの大規模なファクト テーブルが結合されている場合が挙げられます。  

たとえば、orders テーブルが order_id で分散されており、transactions テーブルも order_id で分散されている場合に、orders テーブルを transactions テーブルに order_id で結合すると、このクエリはパススルー クエリになり、データの移動処理が行われなくなります。  手順が減るため、クエリは高速になります。  また、データの移動の減少もクエリの高速化に貢献します。  

分散テーブルを読み込む場合は、受信データを分散キーで並べ替えないでください。読み込みが遅くなります。  以下の記事では、分散列を選択してパフォーマンスを向上させる方法と、CREATE TABLES ステートメントの WITH 句で分散テーブルを定義する方法について詳しく説明します。

[テーブルの概要](sql-data-warehouse-tables-overview.md)、[テーブル分散](sql-data-warehouse-tables-distribute.md)、[テーブル分散の選択](/archive/blogs/sqlcat/choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service)、[CREATE TABLE](sql-data-warehouse-tables-overview.md)、[CREATE TABLE AS SELECT](sql-data-warehouse-develop-ctas.md) に関するページも参照してください。

## <a name="do-not-over-partition"></a>パーティション分割しすぎないようにする

データをパーティション分割すると、パーティション切り替えを利用してデータを管理したり、パーティションを除外してスキャンを最適化したりできるため、有用ですが、パーティションが多すぎると、クエリの速度が低下する場合があります。  

多くの場合、高い粒度でパーティション分割する戦略は、SQL Server では効果的ですが、SQL プールでは効果的ではありません。  パーティションが多すぎると、各パーティションの行数が 100 万を下回る場合に、クラスター化列ストア インデックスの効果が減少する可能性もあります。  

SQL プールでは、バックグラウンドでデータを 60 個のデータベースにパーティション分割します。そのため、パーティションが 100 個あるテーブルを作成すると、実質的にはパーティションが 6000 個になることに留意してください。  ワークロードはそれぞれに異なるため、パーティション分割を試して、自分のワークロードに最適な数を判断することをお勧めします。  

> [!TIP]
> SQL Server の場合よりも低い粒度を使用することを検討してください。  たとえば、日単位ではなく、週単位や月単位のパーティションを使用します。

[テーブル パーティション](sql-data-warehouse-tables-partition.md)に関するページも参照してください。

## <a name="minimize-transaction-sizes"></a>トランザクション サイズを最小限に抑える

トランザクションで INSERT、UPDATE、DELETE ステートメントを実行して、失敗した場合は、ロールバックする必要があります。  ロールバック時間が長くならないようにするには、できる限りトランザクション サイズを最小限に抑えます。  そのためには、INSERT、UPDATE、DELETE ステートメントを複数に分割します。  

たとえば、INSERT に 1 時間かかると予測される場合は、可能であれば、INSERT を 4 つに分割すると、それぞれの実行時間は 15 分になります。  CTAS、TRUNCATE、DROP TABLE、空のテーブルへの INSERT など、特殊な最小ログ記録のケースを活用すると、ロールバックのリスクが軽減されます。  

ロールバックを回避するもう 1 つの方法としては、データ管理のためのパーティション切り替えなど、メタデータのみの操作を使用します。  たとえば、DELETE ステートメントを実行して、テーブル内の order_date が 2001 年 10 月のすべての行を削除する代わりに、月単位でデータをパーティション分割し、該当するデータを含むパーティションを別のテーブルの空のパーティションに切り替えします (ALTER TABLE の例を参照してください)。  

パーティション分割されていないテーブルについては、DELETE を使用する代わりに、CTAS を使用して、テーブルに保持するデータを書き込むことを検討してください。  CTAS にかかる時間が同じ場合でも、トランザクション ログが最小限に抑えられ、必要なときにすばやく取り消すことができるため、CTAS は非常に安全に実行できる操作です。

[トランザクションの概要](sql-data-warehouse-develop-transactions.md)、[トランザクションの最適化](sql-data-warehouse-develop-best-practices-transactions.md)、[テーブル パーティション](sql-data-warehouse-tables-partition.md)、[TRUNCATE TABLE](/sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)、[ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)、[Create table as select (CTAS)](sql-data-warehouse-develop-ctas.md) に関するページも参照してください。

## <a name="use-the-smallest-possible-column-size"></a>できる限り最小の列サイズを使用する

DDL を定義するときに、データをサポートする最小のデータ型を使用すると、クエリのパフォーマンスが向上します。  このアプローチは、CHAR および VARCHAR 列の場合に特に重要です。  

列の最長の値が 25 文字の場合は、列を VARCHAR(25) として定義します。  すべての文字列を既定の長さで定義しないようにします。  さらに、VARCHAR で済む場合は、NVARCHAR を使用せずに、列を VARCHAR として定義します。

[テーブルの概要](sql-data-warehouse-tables-overview.md)、[テーブルのデータ型](sql-data-warehouse-tables-data-types.md)、[CREATE TABLE](sql-data-warehouse-tables-overview.md) に関するページも参照してください。

## <a name="optimize-clustered-columnstore-tables"></a>クラスター化列ストア テーブルを最適化する

クラスター化列ストア インデックスは、SQL プールにデータを格納する最も効率的な方法の 1 つです。  既定では、SQL プールのテーブルは、クラスター化列ストアとして作成されます。  

> [!NOTE]
> 列ストア テーブルに対するクエリの最適なパフォーマンスを達成するには、セグメントの質が高いことが重要です。  

行を列ストア テーブルに書き込む際にメモリ負荷が発生すると、列ストア セグメントの質が低下する可能性があります。  セグメントの質は、圧縮後の行グループに含まれる行の数を使って判断できます。  

クラスター化列ストア テーブルのセグメントの質を検出して向上させる詳細な手順については、[テーブル インデックス](sql-data-warehouse-tables-index.md)に関するページの「[列ストア インデックスの品質の低さの原因](sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality)」を参照してください。  

列ストア セグメントの質を高めることが非常に重要であるため、中規模または大規模リソース クラスのユーザー ID を使用してデータを読み込むことをお勧めします。 低い[データ ウェアハウス ユニット](what-is-a-data-warehouse-unit-dwu-cdwu.md)を使用すると、大きいリソース クラスを読み込みユーザーに割り当てることになります。

通常、テーブルあたりの行数が 100 万を超え、各 SQL プール テーブルが 60 個にパーティション分割されるまで、列ストア テーブルでは圧縮された列ストア セグメントにデータがプッシュされません。そのため、一般的に、テーブルの行数が 6,000 万を超えない限り、列ストア テーブルはクエリにとってメリットがありません。  

6,000 万行未満のテーブルについては、列ストア インデックスを作成してもメリットがありません。  また、デメリットもありません。  

さらに、データをパーティション分割する場合は、クラスター化列ストア インデックスの恩恵を受けるには、各パーティションに 100 万行が必要なことを考慮に入れる必要があります。  テーブルに 100 個のパーティションがある場合に、クラスター化列ストアの恩恵を受けるには、少なくとも 60 億行必要です (60 個のディストリビューション "*100 個のパーティション*" 100 万行)。  

この例でテーブルに 60 億行もない場合は、パーティションの数を減らすか、代わりにヒープ テーブルを使用することを検討してください。  列ストア テーブルの代わりに、ヒープ テーブルとセカンダリ インデックスを使用してパフォーマンスが向上するかどうかを試してみる価値もあります。

> [!TIP]
> 列ストア テーブルに対してクエリを実行する場合は、必要な列のみを選択すると、クエリの実行速度が向上します。  

[テーブル インデックス](sql-data-warehouse-tables-index.md)、[列ストア インデックス](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)、[列ストア インデックスの再構築](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality)に関するページも参照してください。

## <a name="next-steps"></a>次のステップ

この記事で目的のトピックが見つからない場合は、ページの左側にある [Search for docs] を使用して、すべての Azure Synapse ドキュメントで検索を実行してみてください。  

[Azure Synapse の Microsoft Q&A 質問ページ](/answers/topics/azure-synapse-analytics.html)は、他のユーザーや Azure Synapse 製品グループに質問を投稿できる場所です。  Microsoft では、このフォーラムを積極的に監視し、お客様からの質問に他のユーザーや Microsoft のスタッフが回答しているかどうかを確認しています。  

Stack Overflow で質問したい方のために、[Azure Synapse Analytics Stack Overflow フォーラム](https://stackoverflow.com/questions/tagged/azure-sqldw)も用意しています。

[Azure Synapse のフィードバック](https://feedback.azure.com/forums/307516-sql-data-warehouse) ページを使用して、機能に関するご要望をお寄せください。  要望の追加や他の要求への投票は、機能の優先順位を決める際に役立ちます。