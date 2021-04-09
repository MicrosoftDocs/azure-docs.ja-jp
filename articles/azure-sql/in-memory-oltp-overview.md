---
title: インメモリ テクノロジ
description: インメモリ テクノロジを使用すると、Azure SQL Database と Azure SQL Managed Instance のトランザクション ワークロードと分析ワークロードのパフォーマンスが大幅に向上します。
services: sql-database
ms.service: sql-db-mi
ms.subservice: ''
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/19/2019
ms.openlocfilehash: 48b74a5507eb4a1d48b7bf70133e476a30fe8169
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92779953"
---
# <a name="optimize-performance-by-using-in-memory-technologies-in-azure-sql-database-and-azure-sql-managed-instance"></a>Azure SQL Database と Azure SQL Managed Instance でインメモリ テクノロジを使用してパフォーマンスを最適化する
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

インメモリ テクノロジにより、アプリケーションのパフォーマンスを向上させることができ、また、データベースのコストを削減できる可能性があります。

## <a name="when-to-use-in-memory-technologies"></a>インメモリ テクノロジをいつ使用するか

インメモリ テクノロジを使用すれば、さまざまなワークロードでパフォーマンスの向上を実現できます。

- **トランザクション** (オンライン トランザクション処理 (OLTP))。ほとんどの要求でより小さなデータ セットの読み取りや更新を行います (CRUD 操作など)。
- **分析** (オンライン分析処理 (OLAP))。ほとんどのクエリにレポート目的の複雑な計算が含まれます。一定数のクエリでは、既存のテーブルに対するデータの読み込みや追加が行われたり (一括読み込みと呼ばれる)、テーブルからのデータの削除が行われたりします。
- **混合** (ハイブリッド トランザクション/分析処理 (HTAP))。OLTP と OLAP の両方のクエリが同じデータ セットで実行されます。

インメモリ テクノロジでは、メモリに処理する必要があるデータを保持し、クエリのネイティブ コンパイルを使用するか、バッチ処理などの高度な処理および基になるハードウェアで利用可能な SIMD 命令を使用して、これらのワークロードのパフォーマンスを向上させることができます。

## <a name="overview"></a>概要

Azure SQL Database と Azure SQL Managed Instance には、次のインメモリ テクノロジがあります。

- *[インメモリ OLTP](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)* により、1 秒あたりのトランザクション数が増え、トランザクション処理の遅延が少なくなります。 インメモリ OLTP が有益なシナリオには、取引やゲームなどのスループットの高いトランザクション処理、イベントまたは IoT デバイスからのデータの取り込み、キャッシュ、データの読み込み、一時テーブルやテーブル変数のシナリオなどがあります。
- "*クラスター化列ストア インデックス*": ストレージのフットプリントを減らし (最大 10 倍)、レポートと分析のクエリのパフォーマンスを向上させます。 データ マートでファクト テーブルと共に使用してデータベースにより多くのデータを格納し、パフォーマンスを向上させることができます。 さらに、オペレーション データベースで履歴データと共に使用してアーカイブし、最大で 10 倍のデータのクエリを実行可能にすることができます。
- HTAP 用の "*非クラスター化列ストア インデックス*": オペレーション データベースに直接クエリを実行して、ビジネスのリアルタイムの情報を取得します。抽出、変換、ロード (ETL) の高コストなプロセスを実行してデータ ウェアハウスが設定されるまで待機する必要はありません。 非クラスター化列ストア インデックスにより、OLTP データベースで高速の分析クエリを実行しながら、運用ワークロードの影響を軽減できます。
- HTAP 用に *メモリが最適化されたクラスター化列ストア インデックス* では、高速なトランザクション処理を実行すると *同時に*、同じデータに対して分析クエリを非常に迅速に行うことができます。

列ストア インデックスは 2012 年以降、インメモリ OLTP は 2014 年以降、SQL Server 製品の一部です。 Azure SQL Database、Azure SQL Managed Instance、SQL Server では、インメモリ テクノロジの同一の実装が使用されています。

## <a name="benefits-of-in-memory-technology"></a>インメモリ テクノロジの利点

クエリとトランザクションの処理が効率化するため、インメモリ テクノロジはコストの削減にも役立ちます。 通常は、パフォーマンスの向上を実現するためにデータベースの価格レベルをアップグレードする必要はありません。 場合によっては、インメモリ テクノロジでパフォーマンスを向上させながら価格レベルを下げられる場合さえあります。

インメモリ OLTP がパフォーマンスの著しい向上を促した例を 2 つ紹介します。

- インメモリ OLTP を利用することで、[クォーラム ビジネス ソリューションで DTU を 70% 向上させながら、ワークロードを倍増させることができました](https://resources.quorumsoftware.com/case-studies/quorum-doubles-key-database-s-workload-while-lowering-dtu)。
- サンプル ワークロードでリソースの消費量が大幅に向上したことが、[インメモリ OLTP](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB) に関するビデオで示されています。 詳細については、[インメモリ OLTP](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)

> [!NOTE]  
> インメモリ テクノロジは、Premium レベルと Business Critical レベルで使用できます。

次のビデオでは、インメモリ テクノロジによるパフォーマンス向上の可能性について説明されています。 表示されるパフォーマンスの向上は、常にさまざまな要因 (ワークロードとデータの性質、データベースのアクセス パターンなど) に依存していることに注意してください。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-In-Memory-Technologies/player]
>

この記事では、Azure SQL Database と Azure SQL Managed Instance に固有のインメモリ OLTP と列ストア インデックスの側面について説明し、サンプルも示します。

- これらのテクノロジがストレージに及ぼす影響と、データ サイズの上限について説明します。
- これらのテクノロジを活用するデータベースを、異なる価格レベルの間で移動する際の管理方法を説明します。
- インメモリ OLTP と列ストア インデックスを使用する方法を示す 2 つのサンプルを確認します。

SQL Server でのインメモリの詳細については、次を参照してください。

- [インメモリ OLTP の概要と使用シナリオ](/sql/relational-databases/in-memory-oltp/overview-and-usage-scenarios) (開始するためのお客様の導入事例と情報への参照)
- [インメモリ OLTP のドキュメント](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)
- [列ストア インデックスの説明](/sql/relational-databases/indexes/columnstore-indexes-overview)
- [リアルタイム運用分析](/sql/relational-databases/indexes/get-started-with-columnstore-for-real-time-operational-analytics)とも呼ばれる、ハイブリッド トランザクション/分析処理 (HTAP)

## <a name="in-memory-oltp"></a>インメモリ OLTP

インメモリ OLTP テクノロジでは、メモリ内のすべてのデータを保持することで、非常に高速なデータ アクセス操作が提供されます。 また、特殊なインデックス、クエリのネイティブ コンパイル、ラッチフリーのデータ アクセスを使用して、OLTP ワークロードのパフォーマンスを向上させます。 インメモリ OLTP データを整理する方法には、次の 2 つがあります。

- **メモリ最適化行ストア** 形式。行はそれぞれ別のメモリ オブジェクトになります。 これは、高パフォーマンス OLTP ワークロード用に最適化されたクラシック インメモリ OLTP 形式です。 メモリ最適化行ストア形式で使用できるメモリ最適化テーブルには、次の 2 種類があります。

  - *持続的テーブル* (SCHEMA_AND_DATA)。メモリに配置された行が、サーバーの再起動後に保持されます。 この種のテーブルは、インメモリ最適化の他の利点がある従来の行ストア テーブルのように動作します。
  - "*非持続的テーブル*" (SCHEMA_ONLY)。再起動後に行は保持されません。 この種のテーブルは一時データ用に設計されています (たとえば、一時テーブルの置換など)。つまり、データを一部の永続化されたテーブルに移動する前に迅速に読み込む必要があるテーブル (ステージング テーブルと呼ばれる) です。

- **メモリ最適化列ストア** 形式。データは列形式で整理されます。 この構造は HTAP シナリオ用に設計されています。OLTP ワークロードが実行されるのと同じデータ構造で分析クエリを行う必要があります。

> [!Note]
> インメモリ OLTP テクノロジは、メモリに完全に格納できるデータ構造用に設計されています。 インメモリ データはディスクにオフロードできないため、十分なメモリがあるデータベースを使用するようにしてください。 詳細については、「[インメモリ OLTP のデータのサイズとストレージ上限](#data-size-and-storage-cap-for-in-memory-oltp)」を参照してください。

インメモリ OLTP の簡単な手引き:[クイック スタート 1:T-SQL のパフォーマンスの高速化のためのインメモリ OLTP テクノロジ](/sql/relational-databases/in-memory-oltp/survey-of-initial-areas-in-in-memory-oltp) (作業の開始に役立つ別の記事)

テクノロジについての詳細なビデオ:

- [インメモリ OLTP](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB) (パフォーマンス上のメリットと、こうした結果を自身で再現する手順を示しています)
- [インメモリ OLTP のビデオ:概要および使用するタイミングと方法](/archive/blogs/sqlserverstorageengine/in-memory-oltp-video-what-it-is-and-whenhow-to-use-it)

特定のデータベースがインメモリ OLTP をサポートしているかどうかをプログラムによって確認する方法があります。 次の Transact-SQL クエリを実行できます。

```sql
SELECT DatabasePropertyEx(DB_NAME(), 'IsXTPSupported');
```

クエリが **1** を返す場合、インメモリ OLTP はこのデータベースでサポートされています。 次のクエリは、データベースを General Purpose、Standard または Basic にダウングレードする前に削除しておく必要のあるオブジェクトをすべて特定します。

```sql
SELECT * FROM sys.tables WHERE is_memory_optimized=1
SELECT * FROM sys.table_types WHERE is_memory_optimized=1
SELECT * FROM sys.sql_modules WHERE uses_native_compilation=1
```

### <a name="data-size-and-storage-cap-for-in-memory-oltp"></a>インメモリ OLTP のデータのサイズとストレージ上限

インメモリ OLTP には、ユーザー データの格納に使用されるメモリ最適化テーブルが含まれています。 これらのテーブルは、メモリに格納する必要があります。 メモリは SQL Database で直接管理するため、ユーザー データについてクォータの概念があります。 この考え方は、"*インメモリ OLTP ストレージ*" と呼ばれます。

サポートされている各単一データベースの価格レベルと各エラスティック プールの価格レベルには、一定量のインメモリ OLTP ストレージが含まれます。

- [DTU ベースのリソース制限 - 単一データベース](database/resource-limits-dtu-single-databases.md)
- [DTU ベースのリソース制限 - エラスティック プール](database/resource-limits-dtu-elastic-pools.md)
- [仮想コアベースのリソース制限 - 単一データベース](database/resource-limits-vcore-single-databases.md)
- [仮想コアベースのリソース制限 - エラスティック プール](database/resource-limits-vcore-elastic-pools.md)
- [仮想コアベースのリソース制限 - マネージド インスタンス](managed-instance/resource-limits.md)

インメモリ OLTP ストレージ上限では、以下が考慮されます。

- メモリ最適化テーブルとテーブル変数内のアクティブなユーザー データの行。 古い行バージョンはこの上限では考慮されません。
- メモリ最適化テーブル上のインデックス。
- ALTER TABLE 操作の運用上のオーバーヘッド。

上限に達した場合、クォータ不足エラーが発生し、データの挿入や更新ができなくなります。 このエラーを軽減するには、データを削除するか、データベースまたはプールの価格レベルを上げます。

インメモリ OLTP のストレージ使用率を監視する方法と、ほぼ上限に達したときのアラートを構成する方法の詳細については、[インメモリ ストレージの監視](in-memory-oltp-monitor-space.md)に関するページを参照してください。

#### <a name="about-elastic-pools"></a>エラスティック プールについて

エラスティック プールでは、インメモリ OLTP ストレージはプール内のすべてのデータベースで共有されます。 したがって、1 つのデータベースでの使用が他のデータベースに影響を及ぼす可能性があります。 これに対する軽減策は次の 2 つです。

- データベースに対し、プール全体の eDTU または仮想コア数よりも少ない `Max-eDTU` または `MaxvCore` を構成します。 これにより、プール内のすべてのデータベースでインメモリ OLTP のストレージ使用率について、この eDTU に対応する上限が設定されます。
- 0 より大きい `Min-eDTU` または `MinvCore` を構成します。 これにより、プール内の各データベースに、構成された `Min-eDTU` または `vCore` に対応する利用可能なインメモリ OLTP ストレージの量が確保されます。

### <a name="changing-service-tiers-of-databases-that-use-in-memory-oltp-technologies"></a>インメモリ OLTP テクノロジを使用するデータベースのサービス レベルの変更

データベースまたはインスタンスはいつでも、General Purpose から Business Critical (または Standard から Premium) など、より高いレベルにアップグレードすることができます。 使用可能な機能とリソースが増えるだけです。

しかし、レベルをダウングレードすると、データベースに悪影響を与えることがあります。 データベースにインメモリ OLTP オブジェクトが含まれている場合に、Business Critical から General Purpose (または Premium から Standard または Basic) にダウングレードすると、影響が特に大きくなります。 ダウングレードした後は、メモリ最適化テーブルは (引き続き表示されたとしても) 使用できません。 エラスティック プールの価格レベルを下げる場合、またはインメモリ テクノロジを使用しているデータベースを General Purpose、Standard または Basic のエラスティック プールに移動する場合にも同じ考慮事項が該当します。

> [!Important]
> General Purpose、Standard または Basic レベルでは、インメモリ OLTP はサポートされていません。 したがって、インメモリ OLTP オブジェクトがあるデータベースをこれらのいずれかのレベルに移動することはできません。

データベースを General Purpose、Standard または Basic にダウングレードする前に、すべてのメモリ最適化テーブルとテーブルの種類に加えて、すべてのネイティブ コンパイル T-SQL モジュールを削除してください。

*Business Critical レベルのリソースのスケール ダウン*:メモリ最適化テーブルのデータは、データベースのレベルやマネージド インスタンスに関連付けられているか、またはエラスティック プールで使用可能な、インメモリ OLTP ストレージ内に格納する必要があります。 レベルをスケールダウンしようとしたり、使用できるインメモリ OLTP ストレージが十分ではないプールにデータベースを移動しようとしたりすると、操作が失敗します。

## <a name="in-memory-columnstore"></a>インメモリ列ストア

インメモリ列ストア テクノロジを使用することで、テーブルに大量のデータを格納してクエリを実行することができます。 列ストア テクノロジでは列ベースのデータ ストレージ形式とバッチ クエリ処理が使用され、OLAP ワークロードでは、従来の行指向型ストレージと比較して最大で 10 倍のクエリ パフォーマンスが得られます。 また、非圧縮データのサイズと比較して最大で 10 倍のデータ圧縮を実現できます。
データの整理に使用できる列ストア モデルには、次の 2 種類があります。

- **クラスター化列ストア**。列形式で、テーブル内のすべてのデータが整理されます。 このモデルでは、テーブル内のすべての行が列形式で配置され、データを高圧縮し、テーブルでの高速な分析クエリとレポートを実行することができます。 データの性質によっては、データのサイズが 10 倍から 100 倍小さくなる可能性があります。 また、クラスター化列ストア モデルでは、大量のデータを高速で取り込むことができます (一括読み込み)。これは、100,000 行を超えるデータの大きなバッチがディスクに格納される前に圧縮されるためです。 このモデルは、従来のデータ ウェアハウス シナリオに最適です。
- **非クラスター化列ストア**。データは従来の行ストア テーブルに格納され、インデックスは、分析クエリに使用される列ストア形式となります。 このモデルではハイブリッド トランザクション分析処理 (HTAP) が可能であり、従来のワークロードでパフォーマンスのリアルタイム分析を実行できます。 OLTP クエリは、小さな行セットにアクセスするために最適化されている行ストア テーブルで実行されますが、OLAP クエリは、スキャンと分析により適した選択肢である列ストア インデックスで実行されます。 クエリ オプティマイザーでは、クエリに基づき、動的に行ストアまたは列ストアを選択します。 非クラスター化列ストア インデックスではデータのサイズは小さくなりません。これは、元のデータセットが変更されずに元の行ストア テーブルで保持されるためです。 しかし、追加の列ストア インデックスのサイズは、同等の B ツリー インデックスより 1 桁小さくする必要があります。

> [!Note]
> メモリ内列ストア テクノロジでは、メモリでの処理に必要なデータのみが保持されますが、メモリに収まりきらないデータはディスク上に格納されます。 そのため、メモリ内列ストア構造のデータの量が、使用可能なメモリの量を超える場合があります。

テクノロジについての詳細なビデオ:

- [列ストア インデックス:インメモリ分析のビデオ (Ignite 2016 から)](/archive/blogs/sqlserverstorageengine/columnstore-index-in-memory-analytics-i-e-columnstore-index-videos-from-ignite-2016)

### <a name="data-size-and-storage-for-columnstore-indexes"></a>列ストア インデックスのデータ サイズとストレージ

列ストア インデックスはメモリに収まる必要がありません。 そのため、インデックス サイズの唯一の上限は、[DTU ベースの購入モデル](database/service-tiers-dtu.md)および[仮想コアベースの購入モデル](database/service-tiers-vcore.md)に関する記事で記述されているデータベース全体の最大サイズです。

クラスター化列ストア インデックスを使用する場合、ベース テーブル ストレージでは列圧縮が使用されます。 この圧縮により、ユーザー データのストレージ フットプリントが大幅に削減されるため、データベースにより多くのデータを格納できます。 これは、[列アーカイブ圧縮](/sql/relational-databases/data-compression/data-compression#using-columnstore-and-columnstore-archive-compression)でさらに拡張できます。 実行できる圧縮の量はデータの性質に依存しますが、10 倍の圧縮は珍しくありません。

たとえば、最大サイズが 1 テラバイト (TB) のデータベースがあり、列ストア インデックスを使用して 10 倍の比率で圧縮した場合、データベースに合計 10 TB のユーザー データを書き込むことができます。

非クラスター化列ストア インデックスを使用する場合、ベース テーブルは従来の行ストア形式のままで格納されます。 そのため、ストレージはクラスター化列ストア インデックスほど大きく節約されません。 ただし、多数の従来の非クラスター化インデックスを 1 つの列ストア インデックスに置き換えても、テーブルのストレージ フットプリントにおける節約全体を確認できます。

### <a name="changing-service-tiers-of-databases-containing-columnstore-indexes"></a>列ストア インデックスを含むデータベースのサービス レベルの変更

ターゲット レベルが S3 より低い場合、*Basic または Standard に単一データベースをダウングレード* できないことがあります。 列ストア インデックスは Business Critical/Premium 価格レベルと Standard レベル、S3 以上でのみサポートされています。Basic レベルではサポートされません。 サポートされていないレベルにデータベースをダウングレードすると、列ストア インデックスは使用できなくなります。 システムには列ストア インデックスが維持されますが、インデックスは使用されません。 後でサポートされているレベルに戻すと、列ストア インデックスはまたすぐに利用できるようになります。

**クラスター化された** 列ストア インデックスがある場合、ダウングレード後はテーブル全体が使用できなくなります。 そのため、サポートされていないレベルにデータベースをダウングレードする前に、*クラスター化された* 列ストア インデックスをすべて削除することをお勧めします。

> [!Note]
> SQL Managed Instance では、すべてのレベルの列ストア インデックスがサポートされます。

<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

## <a name="next-steps"></a>次のステップ

- [クイック スタート 1:T-SQL のパフォーマンスの高速化のためのインメモリ OLTP テクノロジ](/sql/relational-databases/in-memory-oltp/survey-of-initial-areas-in-in-memory-oltp)
- [既存の Azure SQL アプリケーションにおけるインメモリ OLTP の使用](in-memory-oltp-configure.md)
- インメモリ OLTP の[インメモリ OLTP ストレージの監視](in-memory-oltp-monitor-space.md)
- [インメモリ機能を試す](in-memory-sample.md)

## <a name="additional-resources"></a>その他のリソース

### <a name="deeper-information"></a>詳細情報

- [Learn how Quorum doubles key database’s workload while lowering DTU by 70% with In-Memory OLTP in SQL Database (クォーラムが SQL Database でインメモリ OLTP を使用して DTU の 70% を削減しながら主要なデータベースのワークロードを 2 倍にする方法について)](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)
- [インメモリ OLTP のブログ記事](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)
- [インメモリ OLTP の詳細情報](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)
- [列ストア インデックスの詳細情報](/sql/relational-databases/indexes/columnstore-indexes-overview)
- [リアルタイム運用分析の詳細情報](/sql/relational-databases/indexes/get-started-with-columnstore-for-real-time-operational-analytics)
- (インメモリ OLTP によってパフォーマンスが大幅に向上する一般的なワークロード パターンが記載されている) [一般的なワークロード パターンと移行の考慮事項](/previous-versions/dn673538(v=msdn.10))に関するホワイトペーパーを参照してください

### <a name="application-design"></a>アプリケーションの設計

- [インメモリ OLTP (インメモリ最適化)](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)
- [既存の Azure SQL アプリケーションにおけるインメモリ OLTP の使用](in-memory-oltp-configure.md)

### <a name="tools"></a>ツール

- [Azure Portal](https://portal.azure.com/)
- [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms)
- [SQL Server Data Tools (SSDT)](/sql/ssdt/download-sql-server-data-tools-ssdt)