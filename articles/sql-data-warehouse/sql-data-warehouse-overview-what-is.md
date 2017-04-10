---
title: "Azure SQL Data Warehouse の概要 | Microsoft Docs"
description: "エンタープライズ クラスの分散データベースであり、ペタバイト単位の量までリレーショナル データと非リレーショナル データを処理できます。 業界初のクラウド データ ウェアハウスであり、数秒で拡大、縮小、および一時停止できます。"
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: bjhubbard
editor: 
ms.assetid: 4006c201-ec71-4982-b8ba-24bba879d7bb
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: overview
ms.date: 2/28/2017
ms.author: jrj;mausher;kevin;barbkess;elbutter
translationtype: Human Translation
ms.sourcegitcommit: bf73ad830226626ddf41cc4ae80e714abf8bcfc2
ms.openlocfilehash: 19e87c61493bd4620120b39a533e9e4b64517538
ms.lasthandoff: 03/01/2017


---
# <a name="what-is-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse の概要
Azure SQL Data Warehouse は、クラウドベースの超並列処理 (MPP) スケールアウト データベースであり、大規模なデータを処理できます。 

SQL Data Warehouse では、次の処理が行われます。

* SQL Server リレーショナル データベースを、Azure クラウド スケールアウト機能と組み合わせます。 
* コンピューティングからストレージを分離します。
* コンピューティングを増加、減少、一時停止、または再開できるようにします。 
* Azure プラットフォーム全体で統合します。
* SQL Server Transact-SQL (T-SQL) とツールを利用します。
* SOC や ISO など、法律上およびビジネス上のさまざまなセキュリティ要件に準拠します。

この記事では、SQL Data Warehouse の主な機能について説明します。

## <a name="massively-parallel-processing-architecture"></a>超並列処理アーキテクチャ
SQL Data Warehouse は、超並列処理 (MPP) 分散データベース システムです。 SQL Data Warehouse は、バックグラウンドで多数のシェアード ナッシング ストレージと処理装置にデータを分散します。 データは、Premium ローカル冗長ストレージ レイヤーに格納されます。そのレイヤーの上で、動的にリンクされたコンピューティング ノードによってクエリが実行されます。 SQL Data Warehouse は読み込みと複雑なクエリの実行に "分割統治" 手法を利用できます。 要求は制御ノードが受け取り、配布用に最適化して計算ノードに渡します。それらのノードで、作業が並列に行われます。

ストレージとコンピューティングを分離することで、SQL Data Warehouse は次のことができます。

* コンピューティングとは無関係に、ストレージ サイズを拡大または縮小する。
* データを移動せずに、コンピューティング能力を拡大または縮小する。
* データをそのままの状態で保持しながら、コンピューティング能力を一時停止し、支払いをストレージの分だけにする。
* 稼働時間中にコンピューティング能力を再開する。

次の図に、このアーキテクチャの詳細を示します。

![SQL Data Warehouse のアーキテクチャ][1]

**制御ノード:** 制御ノードは、クエリを管理および最適化します。 すべてのアプリケーションおよび接続と対話するフロントエンドです。 SQL Data Warehouse の制御ノードは SQL Database をベースにしており、接続時の外観や操作性は SQL Database と同じです。 制御ノードは、分散したデータに対する並列クエリを実行するために必要なすべてのデータ移動と計算を内部的に調整します。 SQL Data Warehouse に T-SQL クエリを送信すると、制御ノードはそれを各コンピューティング ノードで並列で実行される個々のクエリに変換します。

**コンピューティング ノード:** コンピューティング ノードは、SQL Data Warehouse を陰から動かす原動力として機能します。 これは、データを格納し、クエリを処理する SQL Database です。 データを追加すると、SQL Data Warehouse は行を計算ノードに分散します。 計算ノードは、データに対する並列クエリを実行するワーカーです。 処理後は、制御ノードに結果を返します。 クエリを終了するために、制御ノードは結果を集計し、最終的な結果を返します。

**ストレージ:** データは Azure BLOB ストレージに格納されます。 計算ノードは、データと対話する際に、直接 Blob Storage に対して書き込みと読み取りを行います。 Azure ストレージは透過的に大幅に拡大されるため、SQL Data Warehouse も同様に拡大できます。 コンピューティングとストレージは独立しているため、SQL Data Warehouse は、コンピューティングのスケーリングとは別に、ストレージを自動的にスケーリングできます。 Azure Blob Storage は完全にフォールト トレラントでもあり、バックアップと復元のプロセスが合理化されています。

**データ移動サービス:** データ移動サービス (DMS) は、ノード間でデータを移動します。 DMS は、コンピューティング ノードに、結合および集計に必要なデータへのアクセスを提供します。 DMS は Azure サービスではありません。 これは、すべてのノードで SQL Database と共に実行される Windows サービスです。 DMS は、直接操作できないバックグラウンド プロセスです。 ただし、クエリ プランを見て、DMS 操作がいつ実行されるかを確認できます。これは、各クエリを並列で実行するにはデータ移動が必要であるためです。

## <a name="optimized-for-data-warehouse-workloads"></a>データ ウェアハウスのワークロード用の最適化
MPP 手法は、データ ウェアハウスに固有のさまざまなパフォーマンスの最適化によって支援されています。次に例を示します。

* 分散クエリ オプティマイザーと、すべてのデータにわたる複雑な統計情報のセット。 データ サイズと分散に関する情報を使用するサービスは、特定の分散クエリ操作のコストの評価結果に基づいてクエリを最適化することができます。
* クエリ実行の必要に応じてコンピューティング リソース間でデータを効率的に移動するためのデータ移動処理に統合されている、高度なアルゴリズムや手法。 これらのデータ移動操作は組み込みであり、データ移動サービスのすべての最適化は自動的に行われます。
* クラスター化 **列ストア** インデックス (既定)。 列ベースのストレージを使用することで、SQL Data Warehouse では従来の行指向型ストレージと比較して平均 5 倍の圧縮率を実現し、最大で 10 倍以上のクエリ パフォーマンスを得ることができます。 列ストア インデックスでは、多数の行をスキャンする必要がある分析クエリがうまく機能します。


## <a name="predictable-and-scalable-performance-with-data-warehouse-units"></a>Data Warehouse ユニットを使用した予測可能でスケーラブルなパフォーマンス
SQL Data Warehouse は、SQL Database と同様の技術で構築されています。そのため、ユーザーは分析クエリに対して一貫性のある予測可能なパフォーマンスを期待できます。 コンピューティング ノードを追加または削減すると、それに比例してパフォーマンスが変化します。 SQL Data Warehouse へのリソースの割り当ては、Data Warehouse ユニット (DWU) で計測されます。 DWU は、SQL Data Warehouse に割り当てられる CPU、メモリ、IOPS など、基礎となるリソースの測定単位です。 DWU の数が増えると、リソースの数が増え、パフォーマンスが高くなります。 具体的には、DWU によって次のことが実現されます。

* 基になるハードウェアまたはソフトウェアを気にせず、データ ウェアハウスをスケーリングすることができます。
* データ ウェアハウスのコンピューティングを変更する前に、DWU レベルでのパフォーマンスの向上を把握することができます。
* インスタンスの基になるハードウェアとソフトウェアは、ワークロードのパフォーマンスに影響を与えることなく変更または移動することができます。
* Microsoft は、ワークロードのパフォーマンスに影響を与えることなく、サービスの基になるアーキテクチャの改善を行うことができます。
* Microsoft は、スケーラブルかつシステムに均等に影響が及ぶような方法で、SQL Data Warehouse のパフォーマンスを迅速に向上させることができます。

Data Warehouse ユニットによって、データ ウェアハウスのワークロードのパフォーマンスとの関連性が高い&3; つのメトリックの尺度が提供されます。 次の重要なワークロード メトリックは、DWU の数に比例して変化します。

**スキャン/集計:** 大量の行をスキャンして複雑な集計を実行する標準的なデータ ウェアハウス クエリ。 これは I/O と CPU を集中的に使用する操作です。

**読み込み:** サービスにデータを取り込む機能。 Azure Storage Blob または Azure Data Lake からの読み込みは、PolyBase を使用した場合に最適なパフォーマンスが得られます。 このメトリックは、サービスのネットワークおよび CPU 要素にストレスをかけるように設計されています。

**CREATE TABLE AS SELECT (CTAS):** CTAS はテーブルをコピーする機能を測定します。 この測定には、ストレージからのデータの読み取り、アプライアンスのノード間でのデータ分散、ストレージへのデータの再書き込みが必要です。 CPU、IO、およびネットワークを集中的に使用する操作です。

## <a name="built-on-sql-server"></a>SQL Server ベースに構築
SQL Data Warehouse は SQL Server リレーショナル データベース エンジンに基づいており、エンタープライズ データ ウェアハウスに期待される機能の多くを備えています。 T-SQL を既に知っている場合、その知識を SQL Data Warehouse に当てはめて理解するのは簡単です。 上級者か初心者かに関係なく、ドキュメントの中で紹介している例は導入の際に役立ちます。 全体的に、SQL Data Warehouse の言語要素の構成方法について、次のように考えることができます。

* SQL Data Warehouse では、多くの操作で T-SQL 構文を使用します。 また、従来の広範な SQL コンストラクト セット (ストアド プロシージャ、ユーザー定義関数、テーブルのパーティション分割、インデックス、照合順序など) もサポートされています。
* SQL Data Warehouse には、クラスター化**列ストア** インデックス、PolyBase 統合、データ監査 (脅威の評価による) など、最新の SQL Server 機能も複数用意されています。
* データ ウェアハウス ワークロードであまり一般的でないか、SQL Server にとって新しい特定の T-SQL 言語要素は、現時点では利用できない場合があります。 詳細については、[移行に関するドキュメント][Migration documentation]を参照してください。

Transact-SQL と、SQL Server、SQL Data Warehouse、SQL Database、および Analytics Platform System 間の共通する機能を使用して、データのニーズに合ったソリューションを作成できます。 パフォーマンス、セキュリティ、およびスケール要件に基づいて、データを保持する場所を決定し、必要に応じてデータをさまざまなシステム間で転送することができます。

## <a name="data-protection"></a>データ保護
SQL Data Warehouse は、Azure Premium のローカル冗長ストレージにすべてのデータを格納します。 複数の同期されたデータ コピーがローカル データ センターに保持され、ローカルで障害が発生した場合には透過的なデータ保護が保証されます。 さらに、SQL Data Warehouse では、Azure Storage Snapshots を使用して、アクティブな (一時停止されていない) データベースが定期的に自動でバックアップされます。 バックアップと復元のしくみについては、[バックアップと復元の概要][Backup and restore overview]に関するページを参照してください。

## <a name="integrated-with-microsoft-tools"></a>Microsoft のツールとの統合
また、SQL Data Warehouse には、SQL Server ユーザーが使い慣れている多くのツールが統合されています。 これには以下のツールが含まれます。

**従来の SQL Server ツール:** SQL Data Warehouse は、SQL Server Analysis Services、Integration Services、Reporting Services と完全に統合されています。

**クラウドベースのツール:** SQL Data Warehouse は、Data Factory、Stream Analytics、Machine Learning、Power BI など、Azure の各種サービスと統合できます。 詳細な一覧については、[統合されているツールの概要][Integrated tools overview]に関するページを参照してください。

**サード パーティ製のツール:** 多数のサード パーティ ツール プロバイダーが自社のツールと SQL Data Warehouse との統合を認定しています。 完全な一覧については、[SQL Data Warehouse ソリューション パートナー][SQL Data Warehouse solution partners]に関するページを参照してください。

## <a name="hybrid-data-sources-scenarios"></a>ハイブリッド データ ソースのシナリオ
Polybase では、使い慣れている T-SQL コマンドを使用してさまざまなソースのデータを活用できます。 Polybase を使用すると、通常のテーブルと同じように、Azure Blob Storage に保持されている非リレーショナル データを照会できます。 Polybase を使用して非リレーショナル データを照会したり、非リレーショナル データを SQL Data Warehouse にインポートしたりします。

* PolyBase は外部テーブルを使用して非リレーショナル データにアクセスします。 テーブルの定義は SQL Data Warehouse に格納され、通常のリレーショナル データにアクセスする場合と同様に SQL とツールを使用してアクセスできます。
* Polybase は統合時に依存性がありません。 Polybase がサポートしているすべてのソースに同じ機能を公開します。 Polybase によって読み取られるデータは、区切り記号で区切られたファイルや ORC ファイルなど、さまざまな形式を使用できます。
* PolyBase は、HD Insight クラスターのストレージとしても使われている Blob Storage にアクセスするのに使用できます。 これにより、リレーショナル ツールと非リレーショナル ツールを使用して同じデータにアクセスできます。

## <a name="sla"></a>SLA
SQL Data Warehouse には、Microsoft Online Services の SLA の一部として、製品レベルのサービス レベル アグリーメント (SLA) が用意されています。 詳細については、「[SQL Data Warehouse の SLA][SLA for SQL Data Warehouse]」を参照してください。 その他すべての製品の SLA については、Azure の[サービス レベル アグリーメント]のページにアクセスするか、[ボリューム ライセンス][Volume Licensing]のページからダウンロードしてください。 

## <a name="next-steps"></a>次のステップ
SQL Data Warehouse の概要については学習したので、次はすばやく [SQL Data Warehouse を作成][create a SQL Data Warehouse]し、[サンプル データを読み込む][load sample data]方法について学習してください。 Azure に慣れていない場合に新しい用語を調べるには、[Azure 用語集][Azure glossary]が役立ちます。 または、次の SQL Data Warehouse リソースも確認できます。  

* [顧客の成功事例]
* [ブログ]
* [機能に関する要求]
* [ビデオ]
* [Customer Advisory Team のブログ]
* [サポート チケットを作成する]
* [MSDN フォーラム]
* [Stack Overflow フォーラム]
* [Twitter]

<!--Image references-->
[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[サポート チケットを作成する]: ./sql-data-warehouse-get-started-create-support-ticket.md
[load sample data]: ./sql-data-warehouse-load-sample-databases.md
[create a SQL Data Warehouse]: ./sql-data-warehouse-get-started-provision.md
[Migration documentation]: ./sql-data-warehouse-overview-migrate.md
[SQL Data Warehouse solution partners]: ./sql-data-warehouse-partner-business-intelligence.md
[Integrated tools overview]: ./sql-data-warehouse-overview-integrate.md
[Backup and restore overview]: ./sql-data-warehouse-restore-database-overview.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md

<!--MSDN references-->

<!--Other Web references-->
[顧客の成功事例]: https://customers.microsoft.com/search?sq=&ff=story_products_services%26%3EAzure%2FAzure%2FAzure%20SQL%20Data%20Warehouse%26%26story_product_families%26%3EAzure%2FAzure%26%26story_product_categories%26%3EAzure&p=0
[ブログ]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Customer Advisory Team のブログ]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[機能に関する要求]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[MSDN フォーラム]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureSQLDataWarehouse
[Stack Overflow フォーラム]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[ビデオ]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[SLA for SQL Data Warehouse]: https://azure.microsoft.com/en-us/support/legal/sla/sql-data-warehouse/v1_0/
[Volume Licensing]: http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=37
[サービス レベル アグリーメント]: https://azure.microsoft.com/en-us/support/legal/sla/

