---
title: 大規模な抽出、変換、および読み込み (ETL) - Azure HDInsight
description: HDInsight 上で Apache Hadoop を使用して ETL を使用する方法について説明します。
services: hdinsight
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/14/2017
ms.author: ashishth
ms.openlocfilehash: c200ca98f2a5ea32886ec12d3e732af6598254f7
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/21/2019
ms.locfileid: "58337614"
---
# <a name="extract-transform-and-load-etl-at-scale"></a>大規模な抽出、変換、および読み込み (ETL)

抽出、変換、および読み込み (ETL) は、データがさまざまなソースから取得され、標準の場所に収集され、クリーニングと処理が行われ、最終的にクエリが可能なデータ ストアに読み込まれるプロセスです。 従来の ETL プロセスは、データをインポートし、所定の場所でクリーニングした後、リレーショナル データ エンジンに格納します。 HDInsight 上では、さまざまな Apache Hadoop エコシステム コンポーネントで、大規模な ETL の実行がサポートされます。 

ETL プロセスでの HDInsight の使用は、次のパイプラインにまとめることができます。

![HDInsight ETL の概要](./media/apache-hadoop-etl-at-scale/hdinsight-etl-at-scale-overview.png)

次のセクションで、ETL の各フェーズとその関連コンポーネントについて説明します。

## <a name="orchestration"></a>オーケストレーション

オーケストレーションは、ETL パイプラインのすべてのフェーズにまたがります。 HDInsight の ETL ジョブには、多くの場合、相互に連携して動作する複数の異なる製品が含まれます。  Hive を使用してデータの一部がクリーニングされ、Pig を使用して別の部分がクリーンアップされることがあります。  Azure Data Factory を使用して、Azure Data Lake Store から Azure SQL Database にデータを読み込むことがあります。

オーケストレーションは、適切なタイミングで適切なジョブを実行するために必要です。

### <a name="apache-oozie"></a>Apache Oozie

Apache Oozie は、Hadoop ジョブを管理するワークフロー調整システムです。 Oozie は HDInsight クラスター内で実行され、Hadoop スタックと統合されます。 Oozie は、Apache Hadoop MapReduce、Apache Pig、Apache Hive、および Apache Sqoop の Hadoop ジョブをサポートします。 Oozie は、Java プログラムやシェル スクリプトのような、システム特有のジョブのスケジュールを設定するのに使用することもできます。

詳細については、[HDInsight で Apache Oozie と Apache Hadoop を使用してワークフローを定義して実行する方法](../hdinsight-use-oozie-linux-mac.md)に関するページを参照してください。Oozie を使用してエンド ツー エンドのパイプラインを動作させる方法について、さらに詳しい情報については、[データ パイプラインの運用化](../hdinsight-operationalize-data-pipeline.md)に関するページを参照してください。 

### <a name="azure-data-factory"></a>Azure Data Factory

Azure Data Factory では、オーケストレーション機能をサービスとしてのプラットフォームの形で提供します。 クラウドベースのデータ統合サービスを通じて、データの移動と変換を制御して自動化するデータ主導型のワークフローをクラウドに作成することができます。 

Azure Data Factory を使用して、以下を実行できます。

1. 各種のデータ ストアからデータを取り込むデータ主導型のワークフロー (パイプライン) を作成し、スケジュールを設定する。
2. Azure HDInsight Hadoop、Spark、Azure Data Lake Analytics、Azure Batch、Azure Machine Learning などのコンピューティング サービスを使用して、データの処理と変換を行う。
3. ビジネス インテリジェンス (BI) アプリケーションから利用できるよう、Azure SQL Data Warehouse などのデータ ストアに出力データを公開する。

Azure Data Factory の詳細については、[こちらのドキュメント](../../data-factory/introduction.md)を参照してください。

## <a name="ingest-file-storage-and-result-storage"></a>ファイル ストレージと結果ストレージの取り込み

ソース データ ファイルは、通常、Azure Storage または Azure Data Lake Storage 内の場所に読み込まれます。 任意の形式のファイルを使用できますが、通常は CSV などのフラット ファイルが使用されます。 

### <a name="azure-storage"></a>Azure Storage 

[Azure Storage](https://azure.microsoft.com/services/storage/blobs/) には、[固有のスケーラビリティ ターゲット](../../storage/common/storage-scalability-targets.md)があります。  大半の分析ノードでは、Azure Storage は、多数の小さなファイルを処理する場合に最善のスケーリングを行います。  Azure Storage は、ファイルの数やサイズが制限内である限り、数やサイズに関係なく、同等のパフォーマンスを保証します。  つまり、テラバイトのデータを格納し、そのデータのサブセットを使用する場合でもすべてを使用する場合でも、一貫性のあるパフォーマンスを得ることができます。

Azure Storage には、さまざまな種類の BLOB があります。  "*追加 BLOB*" は、Web ログやセンサー データを格納するための優れたオプションです。  

アクセスをスケールアウトするために複数の BLOB を多数のサーバーに分散させることができますが、1 台のサーバーが処理できるのは 1 つの BLOB のみです。 BLOB は BLOB コンテナーに論理的にグループ化できますが、このグループ化によってパーティション分割は影響を受けません。

Azure Storage には、BLOB ストレージ用の WebHDFS API レイヤーもあります。  HDInsight のすべてのサービスは、Hadoop 分散ファイル システム (HDFS) の使用方法に類似する方法で、Azure Blob Storage 内のファイルにアクセスして、データのクリーニングとデータ処理を実行できます。

データは、通常は、PowerShell、Azure Storage SDK、または AZCopy を使用して Azure Storage に取り込まれます。

### <a name="azure-data-lake-storage"></a>Azure Data Lake Storage

Azure Data Lake Storage (ADLS) は、HDFS と互換性がある、分析データ用のハイパースケールのマネージド リポジトリです。  ADLS で使用されている設計パラダイムは HDFS に類似しており、容量の合計と個々のファイルのサイズの点で無制限のスケーラビリティを提供します。 ADLS は大きなファイルの操作で優れた機能を発揮しますが、これは大きなファイルを複数のノードに格納できるためです。  ADLS でのデータのパーティション分割は、バックグラウンドで行われます。  数百のテラバイトのデータに対する読み取りと書き込みを効率的に行う数千の同時実行される Executor によって、分析ジョブの実行で非常に高いスループットを得ることができます。

データは、通常は Azure Data Factory、ADLS SDK、AdlCopy Service、Apache DistCp、または Apache Sqoop を使用して ADLS に取り込まれます。  どのサービスを使用するかは、データの場所に大きく依存します。  現在データが既存の Hadoop クラスターにある場合は、Apache DistCp、AdlCopy Service、または Azure Data Factory を使用します。  Azure Blob Storage にある場合は、Azure Data Lake Storage .NET SDK、Azure PowerShell、または Azure Data Factory を使用します。

ADLS は、Azure Event Hub または Apache Storm を使用したイベントの取り込み用にも最適化されています。

#### <a name="considerations-for-both-storage-options"></a>両方のストレージ オプションに関する考慮事項

テラ バイトの範囲のデータセットのアップロードでは、ネットワークの待機時間が大きな問題になる可能性があります。これはデータがオンプレミスの場所から送信される場合に特に当てはまります。  このような場合は、次のオプションを使用できます。

* Azure ExpressRoute:Azure ExpressRoute を使用すると、Azure データセンターとオンプレミスのインフラストラクチャ間のプライベート接続を作成できます。 これらの接続により、大量のデータを転送するための信頼性の高いオプションが提供されます。 詳細については、 [Azure ExpressRoute のドキュメント](../../expressroute/expressroute-introduction.md)をご覧ください。

* データの "オフライン" アップロード。 [Azure Import/Export サービス](../../storage/common/storage-import-export-service.md)を使用して、データが格納されたハード ディスク ドライブを Azure データ センターに発送できます。 データはまず Azure Storage BLOB にアップロードされます。 その後、[Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md) または [AdlCopy](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md) ツールを使用して、Azure Storage BLOB から Data Lake Storage にデータをコピーできます。

### <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse

Azure SQL DW は、今後の分析用にクリーニングされ、使用する準備ができた結果を格納するための最適の選択肢です。  Azure HDInsight を使用して、Azure SQL DW 用のサービスを実行できます。

Azure SQL Data Warehouse (SQL DW) は、分析ワークロード用に最適化されたリレーショナル データベース ストアです。  Azure SQL DW は、パーティション分割されたテーブルに基づいてスケーリングを行います。  テーブルは、複数のノードにパーティション分割できます。  Azure SQL DW ノードは作成時に選択されます。  それらは後でスケーリングできますが、データ移動が必要になるのはアクティブ プロセスです。 詳細については、[Azure SQL Data Warehouse のコンピューティング管理 ](../../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md)に関する記事を参照してください。

### <a name="apache-hbase"></a>Apache HBase

Apache HBase は、Azure HDInsight で利用可能なキー値ストアです。  Apache HBase は、オープン ソースの NoSQL データベースで、Hadoop 上に構築され、Google BigTable を模範にしています。 HBase は、大量の非構造化データと半構造化データに対する効率の良いランダム アクセスと強力な一貫性を、列ファミリで整理されたスキーマなしのデータベースで実現します。

データはテーブルの行内に格納され、行内のデータは列ファミリによってグループ化されます。 HBase は、列や列内に格納されるデータの型を使用前に定義する必要がないという意味で、スキーマレス データベースです。 オープン ソース コードは、直線的な拡張により何千ものノード上でペタバイト級のデータを扱うことができます。 HBase は、Hadoop エコシステム内の分散アプリケーションの利点であるデータの冗長性やバッチ処理などの機能を利用できます。   

HBase は、今後の分析のためにセンサー データとログ データを格納するための優れた宛先です。

HBase のスケーラビリティは、HDInsight クラスター内のノードの数に依存します。

### <a name="azure-sql-database-and-azure-database"></a>Azure SQL Database と Azure Database

Azure では、サービスとしてのプラットフォーム (PAAS) として、次の 3 つの異なるリレーショナル データベースを用意しています。

* [Azure SQL Database](../../sql-database/sql-database-technical-overview.md) は、Microsoft SQL Server の実装です。 パフォーマンスの詳細については、「[Azure SQL Database のパフォーマンスのチューニング](../../sql-database/sql-database-performance-guidance.md)」を参照してください。
* [Azure Database for MySQL](../../mysql/overview.md)  は、Oracle MySQL の実装です。
* [Azure Database for PostgreSQL](../../postgresql/quickstart-create-server-database-portal.md) は、PostgreSQL の実装です。

これらの製品はスケールアップされます。つまり、 CPU とメモリの追加によってスケーリングされます。  I/O パフォーマンスを向上させるため、製品と共にプレミアム ディスクを使用することも選択できます。

## <a name="azure-analysis-services"></a>Azure Analysis Services 

Azure Analysis Services (AAS) は、意思決定支援とビジネス分析で使用される分析データ エンジンであり、ビジネス レポート用の分析データ、Power BI、Excel、Reporting Services レポートなどのクライアント アプリケーション、およびその他の視覚化ツールを提供します。

分析キューブは、個別のキューブの階層を変更することによって拡張できます。  詳しくは、「[Azure Analysis Services の価格](https://azure.microsoft.com/pricing/details/analysis-services/)」をご覧ください。

## <a name="extract-and-load"></a>抽出と読み込み

Azure 内にデータが存在すれば、多数のサービスを使用してデータを抽出し、他の製品に読み込むことができます。  HDInsight では、Sqoop と Flume をサポートします。 

### <a name="apache-sqoop"></a>Apache Sqoop

Apache Sqoop は、構造化データ ソース、半構造化データ ソース、および非構造化データ ソース間でデータを効率的に転送するように設計されたツールです。 

Sqoop では、MapReduce を使用してデータのインポートとエクスポートを実行し、並列操作とフォールト トレランスを提供しています。

### <a name="apache-flume"></a>Apache Flume

Apache Flume は、大量のログ データを効率的に収集、集計、および移動するために使用できる信頼性の高い分散サービスです。 Flume には、データ フローのストリーミングに基づくシンプルで柔軟性のあるアーキテクチャがあります。 Flume は、堅牢でフォールト トレラントであり、チューニング可能な信頼性メカニズムと多数のフェールオーバーと回復メカニズムを備えています。 Flume は、オンライン分析アプリケーションで使用できるシンプルな拡張可能データ モデルを使用しています。

Apache Flume は、Azure HDInsight では使用できません。  オンプレミスの Hadoop インストールでは、Flume を使用して、Azure Storage Blob または Azure Data Lake Storage にデータを送信できます。  詳細については、[HDInsight での Apache Flume の使用](https://web.archive.org/web/20190217104751/ https://blogs.msdn.microsoft.com/bigdatasupport/2014/03/18/using-apache-flume-with-hdinsight/)に関する記事を参照してください。

## <a name="transform"></a>変換

選択した場所にデータが存在すれば、データのクリーニング、結合、または特定の使用パターンに合わせた準備を行う必要があります。  Hive、Pig、および Spark SQL は、すべてがその種の作業を行うための適切な選択肢です。  それらは、すべて HDInsight でサポートされています。 

## <a name="next-steps"></a>次の手順

* [HDInsight 上の Apache Hadoop で Apache Pig を使用する](hdinsight-use-pig.md)
* [ETL ツールとして Apache Hive を使用する](apache-hadoop-using-apache-hive-as-an-etl-tool.md) 
* [Azure HDInsight クラスターで Azure Data Lake Storage Gen2 を使用する](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
