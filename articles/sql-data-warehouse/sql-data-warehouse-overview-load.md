---
title: "Azure SQL Data Warehouse へのデータの読み込み | Microsoft Docs"
description: "SQL Data Warehouse にデータを読み込むための一般的なシナリオについて説明します。 これには PolyBase、Azure Blob Storage、フラット ファイル、およびディスク発送の使用が含まれます。 サード パーティ製のツールを使用することもできます。"
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: 2253bf46-cf72-4de7-85ce-f267494d55fa
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 9366bae67be7d1abf932d07971d2062c7bd29f3c
ms.openlocfilehash: 49825cc7455ae082ef750a2a31abbd2d71693c79
ms.lasthandoff: 02/27/2017



---
# <a name="load-data-into-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse へのデータの読み込み
データを SQL Data Warehouse に読み込むためのシナリオ オプションと推奨事項の概要。

データを読み込むとき、通常、最も大変なのは読み込み対象のデータを準備する作業です。 Azure では、多くのサービスの共通データ ストアとして Azure Blob Storage を使用し、Azure Data Factory で Azure サービス間の通信とデータ移動を調整することで、この読み込み作業が簡単になっています。 こうしたプロセスは、超並列処理 (MPP) によって Azure Blob Storage から SQL Data Warehouse へのデータ読み込みを並列処理する PolyBase テクノロジと統合されています。 

サンプル データベースを読み込むためのチュートリアルについては、[サンプル データベースの読み込み][Load sample databases]に関するページをご覧ください。

## <a name="load-from-azure-blob-storage"></a>Azure Blob Storage からの読み込み
PolyBase を使用して Azure Blob Storage からデータを読み込むと、SQL Data Warehouse へのデータ インポートが最速になります。 PolyBase では、SQL Data Warehouse の超並列処理 (MPP) 設計を使用して、Azure Blob Storage からのデータ読み込みを並列処理します。 PolyBase を使用するには、T-SQL コマンドまたは Azure Data Factory パイプラインを使用します。

### <a name="1-use-polybase-and-t-sql"></a>1.PolyBase と T-SQL を使用する
読み込みプロセスの概要:

1. データを Azure Blob Storage または Azure Data Lake Store に移動し、テキスト ファイルに格納します。
2. SQL Data Warehouse で外部オブジェクトを構成して、データの場所とデータ形式を定義します。
3. 新しいデータベース テーブルへのデータ読み込みを並列処理する T-SQL コマンドを実行します。

<!-- 5. Schedule and run a loading job. --> 

チュートリアルについては、「[Azure Blob Storage から SQL Data Warehouse へのデータの読み込み (PolyBase)][Load data from Azure blob storage to SQL Data Warehouse (PolyBase)]」を参照してください。

### <a name="2-use-azure-data-factory"></a>2.Azure Data Factory を使用する
PolyBase をさらに簡単に使用できるように、PolyBase を使用する Azure Data Factory パイプラインを作成して、Azure Blob Storage から SQL Data Warehouse にデータを読み込むことができます。 この場合、T-SQL オブジェクトを定義する必要がないため、すばやく構成できます。 外部データをインポートせずに、そのデータに対してクエリを実行する必要がある場合は、T-SQL を使用します。 

読み込みプロセスの概要:

1. データを Azure Blob Storage に移動し、テキスト ファイルに格納します。 Azure Data Factory では現在、ADLS と PolyBase の接続 はサポートされていません。
2. Azure Data Factory パイプラインを作成して、データを取り込みます。 PolyBase オプションを使用します。
4. パイプラインのスケジュールを設定し、実行します。

チュートリアルについては、[Azure Blob Storage から Azure SQL Data Warehouse へのデータの読み込み (Azure Data Factory)][Load data from Azure blob storage to SQL Data Warehouse (Azure Data Factory)] に関するページをご覧ください。

## <a name="load-from-sql-server"></a>SQL Server からの読み込み
SQL Server から SQL Data Warehouse にデータを読み込むには、Integration Services (SSIS) を使用するか、フラット ファイルを転送するか、ディスクを Microsoft に送付します。 他の読み込みプロセスの概要とチュートリアルへのリンクをご覧ください。

SQL Server から SQL Data Warehouse への完全なデータ移行を計画するには、[移行の概要][Migration overview]に関するページをご覧ください。 

### <a name="use-integration-services-ssis"></a>Integration Services (SSIS) の使用
Integration Services (SSIS) パッケージを使用して SQL Server に読み込む場合は、SQL Server を移行元として、また SQL Data Warehouse を移行先として使用するようにパッケージを更新します。 この手軽で簡単な方法は、読み込みプロセスを移行せず、クラウドに既にあるデータを使用する場合に適しています。 ただし、SSIS では読み込みの並列処理は実行されないため、PolyBase を使用するよりも読み込み速度が遅くなります。

読み込みプロセスの概要:

1. 移行元として SQL Server インスタンスを指定し、移行先として SQL Data Warehouse データベースを指定するように、Integration Services パッケージを変更します。
2. スキーマを SQL Data Warehouse に移行します (その場所にまだ存在しない場合)。
3. SQL Data Warehouse でサポートされているデータ型のみを使用するように、パッケージのマッピングを変更します。
4. パッケージのスケジュールを設定し、実行します。

チュートリアルについては、「[SQL Server から Azure SQL Data Warehouse へのデータの読み込み (SSIS)][Load data from SQL Server to Azure SQL Data Warehouse (SSIS)]」を参照してください。

### <a name="use-azcopy-recommended-for--10-tb-data"></a>AZCopy の使用 (データが 10 TB 未満の場合に推奨)
データのサイズが 10 TB 未満の場合、SQL Server からフラット ファイルにデータをエクスポートし、ファイルを Azure Blob Storage にコピーして、PolyBase を使って SQL Data Warehouse に読み込むことができます。

読み込みプロセスの概要:

1. bcp コマンドライン ユーティリティを使用して、SQL Server からフラット ファイルにデータをエクスポートします。
2. AZCopy コマンドライン ユーティリティを使用して、フラット ファイルから Azure Blob Storage にデータをコピーします。
3. PolyBase を使用して SQL Data Warehouse に読み込みます。

チュートリアルについては、「[Azure Blob Storage から SQL Data Warehouse へのデータの読み込み (PolyBase)][Load data from Azure blob storage to SQL Data Warehouse (PolyBase)]」を参照してください。

### <a name="use-bcp"></a>bcp の使用
データ量が少ない場合は、bcp を使用して、Azure SQL Data Warehouse に直接読み込むことができます。

読み込みプロセスの概要:

1. bcp コマンドライン ユーティリティを使用して、SQL Server からフラット ファイルにデータをエクスポートします。
2. bcp を使用して、フラット ファイルから SQL Data Warehouse にデータを直接読み込みます。

チュートリアルについては、[SQL Server から Azure SQL Data Warehouse へのデータの読み込み (bcp)][Load data from SQL Server to Azure SQL Data Warehouse (bcp)] に関するページをご覧ください。

### <a name="use-importexport-recommended-for--10-tb-data"></a>Import/Export の使用 (データが 10 TB を超える場合に推奨)
10 TB を超えるデータを Azure に移動する場合は、Microsoft のディスク発送サービス [Import/Export][Import/Export] を使用することをお勧めします。 

読み込みプロセスの概要

1. bcp コマンド ライン ユーティリティを使用して、SQL Server から、転送可能なディスクのフラット ファイルにデータをエクスポートします。
2. ディスクを Microsoft に送付します。
3. Microsoft がデータを SQL Data Warehouse に読み込みます

## <a name="load-from-hdinsight"></a>HDInsight からの読み込み
SQL Data Warehouse は、PolyBase を介した HDInsight からのデータの読み込みをサポートしています。 そのプロセスは、Azure Blob Storage からデータを読み込む操作と同じです。つまり、PolyBase を使用して HDInsight に接続し、データを読み込みます。 

### <a name="1-use-polybase-and-t-sql"></a>1.PolyBase と T-SQL を使用する
読み込みプロセスの概要:

1. データを HDInsight に移行し、テキスト ファイル、ORC、または Parquet 形式で保存します。
2. SQL Data Warehouse で外部オブジェクトを構成して、データの場所とデータ形式を定義します。
3. 新しいデータベース テーブルへのデータ読み込みを並列処理する T-SQL コマンドを実行します。

チュートリアルについては、「[Azure Blob Storage から SQL Data Warehouse へのデータの読み込み (PolyBase)][Load data from Azure blob storage to SQL Data Warehouse (PolyBase)]」を参照してください。

## <a name="recommendations"></a>推奨事項
パートナーの多くが読み込みソリューションを提供しています。 詳細については、[ソリューション パートナー][solution partners]の一覧を参照してください。 

非リレーショナル ソースのデータを SQL Data Warehouse に読み込む場合は、事前にそのデータを行と列に変換しておく必要があります。 変換したデータはデータベースに格納しなくても、テキスト ファイルに保存できます。

新しく読み込んだデータの統計を作成してください。 Azure SQL Data Warehouse は、統計の自動作成または自動更新をまだサポートしていません。  クエリで最高のパフォーマンスを得るには、最初の読み込み後またはそれ以降のデータの変更後に、すべてのテーブルのすべての列で統計を作成することが重要です。  詳細については、[統計][Statistics]に関するページを参照してください。

## <a name="next-steps"></a>次のステップ
開発に関するその他のヒントについては、[開発の概要][development overview]に関するページをご覧ください。

<!--Image references-->

<!--Article references-->
[Load data from Azure blob storage to SQL Data Warehouse (PolyBase)]: ./sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md
[Load data from Azure blob storage to SQL Data Warehouse (Azure Data Factory)]: ./sql-data-warehouse-load-from-azure-blob-storage-with-data-factory.md
[Load data from SQL Server to Azure SQL Data Warehouse (SSIS)]: ./sql-data-warehouse-load-from-sql-server-with-integration-services.md
[Load data from SQL Server to Azure SQL Data Warehouse (bcp)]: ./sql-data-warehouse-load-from-sql-server-with-bcp.md
[Load data from SQL Server to Azure SQL Data Warehouse (AZCopy)]: ./sql-data-warehouse-load-from-sql-server-with-azcopy.md

[Load sample databases]: ./sql-data-warehouse-load-sample-databases.md
[Migration overview]: ./sql-data-warehouse-overview-migrate.md
[solution partners]: ./sql-data-warehouse-partner-business-intelligence.md
[development overview]: ./sql-data-warehouse-overview-develop.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md

<!--MSDN references-->

<!--Other Web references-->
[Import/Export]: https://azure.microsoft.com/documentation/articles/storage-import-export-service/

