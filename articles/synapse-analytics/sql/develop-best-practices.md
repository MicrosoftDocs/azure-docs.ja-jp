---
title: Synapse SQL 向けの開発に関するベスト プラクティス
description: Synapse SQL 向けの開発を行う際に知っておく必要がある推奨事項とベスト プラクティス。
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 479b20bcb0803d5483d139939da627d53ceccb49
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101667625"
---
# <a name="development-best-practices-for-synapse-sql"></a>Synapse SQL 向けの開発に関するベスト プラクティス

この記事では、独自のデータ ウェアハウス ソリューションを開発するにあたってのガイダンスとベスト プラクティスについて説明します。 

## <a name="dedicated-sql-pool-development-best-practices"></a>専用 SQL プールの開発に関するベスト プラクティス

### <a name="reduce-cost-with-pause-and-scale"></a>一時停止とスケールでコストを削減する

一時停止とスケーリングを通じてコストを削減する方法については、[コンピューティングの管理](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)に関するページを参照してください。

### <a name="maintain-statistics"></a>統計を管理する

毎日または各読み込みの後に必ず統計を更新します。  パフォーマンスと統計を作成および更新するコストの間には常にトレードオフの関係が存在します。 すべての統計を管理するのは時間がかかりすぎる場合は、統計を作成する列や頻繁に更新する列を限定してください。  

たとえば、毎日新しい値が追加される可能性のある日付列を更新することをお勧めします。 

> [!NOTE]
> 結合に使用されている列、WHERE 句で使用されている列、および GROUP BY に含まれている列に関する統計を作成すると、最も大きなメリットが得られます。

[テーブル統計の管理](develop-tables-statistics.md)、[CREATE STATISTICS](/sql/t-sql/statements/create-statistics-transact-sql?view=azure-sqldw-latest&preserve-view=true)、[UPDATE STATISTICS](/sql/t-sql/statements/update-statistics-transact-sql?view=azure-sqldw-latest&preserve-view=true) に関するページもご覧ください。

### <a name="hash-distribute-large-tables"></a>ハッシュで大規模なテーブルを分散させる

既定では、テーブルはラウンド ロビン分散です。 この機能により、ユーザーはテーブルの分散方法を決定しなくてもテーブルの作成を簡単に開始できます。  ラウンド ロビン テーブルは、一部のワークロードでは十分なパフォーマンスを示す可能性があります。 ただし、ほとんどの場合、分散列を選択するとパフォーマンスが大幅に向上します。  

列で分散したテーブルのパフォーマンスがラウンド ロビン テーブルをはるかに上回る最も一般的な例としては、2 つの大規模なファクト テーブルが結合されている場合が挙げられます。  

たとえば、orders テーブルが order_id で分散されており、transactions テーブルも order_id で分散されている場合に、orders テーブルを transactions テーブルに order_id で結合すると、このクエリはパススルー クエリになります。 

これは、データ移動操作をなくすことを意味します。  手順が減るため、クエリは高速になります。  また、データの移動の減少もクエリの高速化に貢献します。

> [!TIP]
> 分散テーブルを読み込む場合は、受信データを分散キーで並べ替えないでください。読み込みが遅くなります。  

分散列を選択した場合にパフォーマンスが向上するしくみや、CREATE TABLE ステートメントの WITH 句で分散テーブルを定義する方法の詳細については、以下のリンクを参照してください。

[テーブルの概要](develop-tables-overview.md)、[テーブル分散](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)、[テーブル分散の選択](/archive/blogs/sqlcat/choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service)、[CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true)、[CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true) に関するページも参照してください。

### <a name="do-not-over-partition"></a>パーティション分割しすぎないようにする
データをパーティション分割すると、パーティション切り替えを利用してデータを管理したり、パーティションを除外してスキャンを最適化したりできるため、有用ですが、パーティションが多すぎると、クエリの速度が低下する場合があります。  多くの場合、高い粒度でパーティション分割する戦略は、SQL Server では効果的ですが、専用 SQL プールでは効果的ではありません。  

> [!NOTE]
> 多くの場合、高い粒度でパーティション分割する戦略は、SQL Server では効果的ですが、専用 SQL プールでは効果的ではありません。  

パーティションが多すぎると、各パーティションの行数が 100 万を下回る場合に、クラスター化列ストア インデックスの効果が減少する可能性もあります。 専用 SQL プールによって、データが 60 のデータベースにパーティション分割されます。 

そのため、パーティションが 100 個あるテーブルを作成すると、パーティションが 6000 個になります。  ワークロードはそれぞれに異なるため、パーティション分割を試して、自分のワークロードに最適な数を判断することをお勧めします。  

考慮すべき選択肢の 1 つは、SQL Server で機能していた粒度よりも低い粒度を使用することです。  たとえば、日単位ではなく、週単位や月単位のパーティションを使用します。

[テーブル パーティション](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)に関するページも参照してください。

### <a name="minimize-transaction-sizes"></a>トランザクション サイズを最小限に抑える

トランザクションで INSERT、UPDATE、DELETE ステートメントを実行して、失敗した場合は、ロールバックする必要があります。  ロールバック時間が長くならないようにするには、できる限りトランザクション サイズを最小限に抑えます。  そのためには、INSERT、UPDATE、DELETE ステートメントを複数に分割します。  

たとえば、INSERT に 1 時間かかると予測される場合は、INSERT を 4 つに分割することで、それぞれの実行時間を 15 分に短縮することができます。

> [!TIP]
> CTAS、TRUNCATE、DROP TABLE、空のテーブルへの INSERT など、特殊な最小ログ記録のケースを活用すると、ロールバックのリスクが軽減されます。  

ロールバックを回避するもう 1 つの方法としては、データ管理のためのパーティション切り替えなど、メタデータのみの操作を使用します。  

たとえば、DELETE ステートメントを実行して、テーブル内の order_date が 2001 年 10 月のすべての行を削除する代わりに、月単位でデータをパーティション分割し、該当するデータを含むパーティションを別のテーブルの空のパーティションに切り替えします (ALTER TABLE の例を参照してください)。  

パーティション分割されていないテーブルについては、DELETE を使用する代わりに、CTAS を使用して、テーブルに保持するデータを書き込むことを検討してください。  CTAS にかかる時間が同じ場合でも、トランザクション ログが最小限に抑えられ、必要なときにすばやく取り消すことができるため、CTAS は非常に安全に実行できる操作です。

[トランザクションの概要](develop-transactions.md)、[トランザクションの最適化](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)、[テーブル パーティション](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)、[TRUNCATE TABLE](/sql/t-sql/statements/truncate-table-transact-sql?view=azure-sqldw-latest&preserve-view=true)、[ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?view=azure-sqldw-latest&preserve-view=true)、[Create table as select (CTAS)](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) に関するページも参照してください。

### <a name="use-the-smallest-possible-column-size"></a>できる限り最小の列サイズを使用する

DDL を定義するときに、データをサポートする最小のデータ型を使用すると、クエリのパフォーマンスが向上します。 このアクションは、CHAR および VARCHAR 列の場合に特に重要です。  

列の最長の値が 25 文字の場合は、列を VARCHAR(25) として定義します。  すべての文字列を既定の長さで定義しないようにします。  また、VARCHAR で済む場合は、NVARCHAR を使用せずに、列を VARCHAR として定義します。

[テーブルの概要](develop-tables-overview.md)、[テーブルのデータ型](develop-tables-data-types.md)、[CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true) に関するページも参照してください。

### <a name="optimize-clustered-columnstore-tables"></a>クラスター化列ストア テーブルを最適化する

クラスター化列ストア インデックスは、専用 SQL プールにデータを格納する最も効率的な方法の 1 つです。  既定では、専用 SQL プールのテーブルは、クラスター化された ColumnStore として作成されます。  

列ストア テーブルに対するクエリのパフォーマンスを最大限に引き出すには、セグメントの質が高いことが重要です。  行を列ストア テーブルに書き込む際にメモリ負荷が発生すると、列ストア セグメントの質が低下する可能性があります。  

セグメントの質は、圧縮後の行グループに含まれる行の数を使って判断できます。  クラスター化列ストア テーブルのセグメントの質を検出して向上させる詳細な手順については、「[列ストア インデックスの品質の低さの原因](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#causes-of-poor-columnstore-index-quality)」と[テーブル インデックス](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)に関する記事を参照してください。  

列ストア セグメントの質を高めることが非常に重要であるため、中規模または大規模リソース クラスのユーザー ID を使用してデータを読み込むことをお勧めします。 低い[データ ウェアハウス ユニット](resource-consumption-models.md)を使用すると、大きいリソース クラスを読み込みユーザーに割り当てることになります。

通常、テーブルあたりの行数が 100 万を超え、各専用 SQL プール テーブルが 60 個にパーティション分割されるまで、列ストア テーブルでは圧縮された列ストア セグメントにデータがプッシュされません。そのため、テーブルの行数が 6,000 万を超えない限り、列ストア テーブルはクエリにとってメリットがありません。  

> [!TIP]
> 6,000 万行未満のテーブルについては、列ストア インデックスを使用しても最適なソリューションを得られない可能性があります。  

さらに、データをパーティション分割する場合は、クラスター化列ストア インデックスの恩恵を受けるには、各パーティションに 100 万行が必要なことを考慮に入れる必要があります。  テーブルに 100 個のパーティションがある場合に、クラスター化列ストアの恩恵を受けるには、少なくとも 60 億行必要です (60 個のディストリビューション "*100 個のパーティション*" 100 万行)。  

テーブルに 60 億行もない場合は、パーティションの数を減らすか、代わりにヒープ テーブルを使用することを検討してください。  列ストア テーブルの代わりに、ヒープ テーブルをセカンダリ インデックスとともに使用して、パフォーマンスが向上するかどうかを試してみる価値もあります。

列ストア テーブルに対してクエリを実行する場合は、必要な列のみを選択すると、クエリの実行速度が向上します。  

[テーブル インデックス](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)、[列ストア インデックス](/sql/relational-databases/indexes/columnstore-indexes-overview?view=azure-sqldw-latest&preserve-view=true)、[列ストア インデックスの再構築](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#rebuilding-indexes-to-improve-segment-quality)に関するページもご覧ください。

## <a name="serverless-sql-pool-development-best-practices"></a>サーバーレス SQL プールの開発に関するベスト プラクティス

### <a name="general-considerations"></a>一般的な考慮事項

サーバーレス SQL プールを使用すると、Azure ストレージ アカウント内のファイルに対してクエリを実行できます。 ローカル ストレージやインジェストの機能はありません。つまり、クエリの対象となるすべてのファイルはサーバーレス SQL プールの外部にあります。 そのため、ストレージからのファイルの読み取りに関連するものはすべて、クエリのパフォーマンスに影響を与える可能性があります。

### <a name="colocate-azure-storage-account-and-serverless-sql-pool"></a>Azure Storage アカウントとサーバーレス SQL プールの併置

待機時間を最小限に抑えるには、Azure Storage アカウントとサーバーレス SQL プールのエンドポイントを併置します。 ワークスペースの作成中にプロビジョニングされたストレージ アカウントとエンドポイントは同じリージョンに配置されます。

最適なパフォーマンスを得るために、サーバーレス SQL プールを使用して他のストレージ アカウントにアクセスする場合は、それらが同じリージョンにあることを確認してください。 そうでない場合、リモート リージョンからエンドポイントのリージョンにデータをネットワーク転送するときの待機時間が長くなります。

### <a name="azure-storage-throttling"></a>Azure Storage の帯域幅調整

複数のアプリケーションとサービスがストレージ アカウントにアクセスする場合があります。 アプリケーション、サービス、およびサーバーレス SQL プールのワークロードによって生成される合計 IOPS またはスループットの合計がストレージ アカウントの制限を超えると、ストレージ調整が発生します。 ストレージの帯域幅調整が発生すると、クエリのパフォーマンスに多大な悪影響が生じます。

調整が検出されると、サーバーレス SQL プールには、このシナリオの処理が組み込まれています。 サーバーレス SQL プールでは、調整が解決されるまで、より遅いペースでストレージへの要求を行います。 

ただし、最適なクエリ実行のために、クエリの実行中は他のワークロードでストレージ アカウントに負荷をかけないことをお勧めします。

### <a name="prepare-files-for-querying"></a>クエリ用のファイルを準備する

可能であれば、ファイルを準備してパフォーマンスを向上させることができます。

- CSV を Parquet に変換 - Parquet は列形式です。 圧縮されているため、同じデータが含まれる CSV ファイルよりもファイル サイズが小さくなり、サーバーレス SQL プールで読み取るために必要な時間とストレージ要求も少なくなります。
- クエリが 1 つの大きなファイルを対象としている場合は、複数の小さなファイルに分割すると効果があります。
- CSV ファイルのサイズを 10 GB 未満にしてください。
- 1 つの OPENROWSET パスまたは外部テーブル LOCATION に対して、ファイルのサイズを同じにすることをお勧めします。
- さまざまなフォルダーまたはファイル名にパーティションを格納することによって、データをパーティション分割します。「[filename および filepath 関数を使用して特定のパーティションを対象にする](#use-fileinfo-and-filepath-functions-to-target-specific-partitions)」を確認してください。

### <a name="use-fileinfo-and-filepath-functions-to-target-specific-partitions"></a>fileinfo および filepath 関数を使用して特定のパーティションを対象にする

多くの場合、データはパーティションに編成されます。 特定のフォルダーやファイルに対してクエリを実行するよう、サーバーレス SQL プールに指示することができます。 こうすることで、クエリで読み取りおよび処理する必要があるファイルの数とデータの量が減ります。 

その結果、パフォーマンスが向上します。 詳細については、[filename](query-data-storage.md#filename-function) 関数、[filepath](query-data-storage.md#filepath-function) 関数、および、[特定のファイルに対してクエリを実行する](query-specific-files.md)方法の例を参照してください。

ストレージ内のデータがパーティション分割されていない場合は、これらの関数を使用してファイルを対象とするクエリを最適化できるよう、データのパーティション分割を検討してください。

サーバーレス SQL プールから、[パーティション分割された Apache Spark for Azure Synapse 外部テーブルに対してクエリを実行](develop-storage-files-spark-tables.md)すると、必要なファイルだけが自動的にクエリの対象となります。

### <a name="use-cetas-to-enhance-query-performance-and-joins"></a>CETAS を使用してクエリのパフォーマンスと結合を強化する

[CETAS](develop-tables-cetas.md) は、サーバーレス SQL プールで利用できる最も重要な機能の 1 つです。 CETAS は、外部テーブルのメタデータを作成し、SELECT クエリの結果をストレージ アカウント内の一連のファイルにエクスポートする並列操作です。

CETAS を使用して、結合された参照テーブルなど、クエリの頻繁に使用される部分を新しいファイル セットに格納できます。 後で、複数のクエリで共通の結合を繰り返す代わりに、この単一の外部テーブルに結合することができます。 

CETAS によって Parquet ファイルが生成されると、最初のクエリがこの外部テーブルを対象とするときに統計が自動的に作成され、パフォーマンスが向上します。

### <a name="next-steps"></a>次のステップ

この記事に記載されていない情報が必要な場合は、このページの左側にある **ドキュメントの検索** 機能を使用して、SQL プールのすべてのドキュメントを検索してください。  [Azure Synapse Analytics に関する Microsoft Q&A 質問ページ](/answers/topics/azure-synapse-analytics.html)は、他のユーザーや Azure Synapse Analytics 製品グループに質問を投稿できる場所です。 Microsoft では、このフォーラムを積極的に監視し、お客様からの質問に他のユーザーや Microsoft のスタッフが回答しているかどうかを確認しています。  

Stack Overflow で質問したい方のために、[Azure Synapse Analytics Stack Overflow フォーラム](https://stackoverflow.com/questions/tagged/azure-sqldw)も用意しています。
