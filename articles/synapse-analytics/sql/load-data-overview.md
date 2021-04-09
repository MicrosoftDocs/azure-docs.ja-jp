---
title: 専用 SQL プール用の PolyBase データ読み込み戦略を設計する
description: ETL の代わりに、専用 SQL を使用したデータの読み込み用に抽出、読み込み、変換 (ELT) プロセスを設計します。
services: synapse-analytics
author: gaursa
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: gaursa
ms.reviewer: igorstan
ms.openlocfilehash: 850c03d5cbda8fb11bbc804f2cd2c848a5a2411f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104589501"
---
# <a name="design-a-polybase-data-loading-strategy-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Azure Synapse Analytics で専用 SQL プール用の PolyBase データ読み込み戦略を設計する

従来の SMP データ ウェアハウスでは、データの読み込みに ETL (抽出、変換、読み込み) プロセスが使用されます。 Azure SQL プールは、コンピューティング リソースとストレージ リソースのスケーラビリティと柔軟性を活かした超並列処理 (MPP: Massively Parallel Processing) アーキテクチャです。 抽出、読み込み、変換 (ELT) プロセスを利用することで、組み込みの分散クエリ処理機能の利点を活かすと共に、読み込む前にデータを事前に変換するために必要なリソースをなくすことができます。

SQL プールは、Polybase 以外にもさまざまな読み込み方法 (BCP、SQL BulkCopy API など) をサポートしていますが、データを読み込むための最速かつ最もスケーラブルな方法は PolyBase です。  PolyBase は、Azure Blob Storage または Azure Data Lake Store に格納されている外部データに T-SQL 言語でアクセスするテクノロジです。

> [!VIDEO https://www.youtube.com/embed/l9-wP7OdhDk]

## <a name="extract-load-and-transform-elt"></a>抽出、読み込み、変換 (ELT)

ELT (抽出、読み込み、変換) とは、データがソース システムから抽出されてデータ ウェアハウスに読み込まれ、その後変換されるプロセスです。

専用 SQL プール用に PolyBase ELT を実装する基本的な手順は次のとおりです。

1. ソース データをテキスト ファイルに抽出します。
2. そのデータを Azure Blob Storage または Azure Data Lake Store に配置します。
3. 読み込むデータを準備します。
4. PolyBase を使用して専用 SQL プール ステージング テーブルにデータを読み込みます。
5. データを変換します。
6. 運用環境テーブルにデータを挿入します。

読み込みのチュートリアルについては、[PolyBase を使用した Azure Blob Storage から Azure Synapse Analytics へのデータの読み込み](../sql-data-warehouse/load-data-from-azure-blob-storage-using-copy.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)に関するページをご覧ください。

詳細については、[読み込みパターンに関するブログ](/archive/blogs/sqlcat/azure-sql-data-warehouse-loading-patterns-and-strategies)をご覧ください。

## <a name="1-extract-the-source-data-into-text-files"></a>1.ソース データをテキスト ファイルに抽出する

ソース システムからのデータの取得方法は、保存場所によって異なります。  最終的な目標は、PolyBase でサポートされている区切りテキスト ファイルにデータを移動することです。

### <a name="polybase-external-file-formats"></a>PolyBase の外部ファイル形式

PolyBase は、UTF-8 と UTF-16 でエンコードされた区切りテキスト ファイルからデータを読み込みます。 この区切りテキスト ファイルに加え、Hadoop ファイル形式の RC ファイル、ORC、Parquet からも読み込みます。 PolyBase は、Gzip や Snappy の圧縮ファイルからデータを読み込むこともできます。 PolyBase では現在、拡張 ASCII、固定幅形式、および WinZip、JSON、XML などの入れ子形式はサポートされていません。

SQL Server からエクスポートする場合は、[bcp コマンドライン ツール](/sql/tools/bcp-utility?view=azure-sqldw-latest&preserve-view=true)を使用して、区切りテキスト ファイルにデータをエクスポートすることができます。 Parquet と Azure Synapse Analytics のデータ型マッピングは次のとおりです。

| **Parquet データ型** |                      **SQL データ型**                       |
| :-------------------: | :----------------------------------------------------------: |
|        tinyint        |                           tinyint                            |
|       smallint        |                           smallint                           |
|          INT          |                             INT                              |
|        bigint         |                            bigint                            |
|        boolean        |                             bit                              |
|        double         |                            float                             |
|         float         |                             real                             |
|        double         |                            money                             |
|        double         |                          smallmoney                          |
|        string         |                            nchar                             |
|        string         |                           nvarchar                           |
|        string         |                             char                             |
|        string         |                           varchar                            |
|        binary         |                            binary                            |
|        binary         |                          varbinary                           |
|       timestamp       |                             date                             |
|       timestamp       |                        smalldatetime                         |
|       timestamp       |                          datetime2                           |
|       timestamp       |                           DATETIME                           |
|       timestamp       |                             time                             |
|       date            |                             date                             |
|        decimal        |                            decimal                           |

## <a name="2-land-the-data-into-azure-blob-storage-or-azure-data-lake-store"></a>2.データを Azure Blob Storage または Azure Data Lake Store に配置する

Azure Storage にデータを配置するには、[Azure Blob Storage](../../storage/blobs/storage-blobs-introduction.md) または [Azure Data Lake Store](../../data-lake-store/data-lake-store-overview.md) にデータを移動します。 どちらの場合も、データはテキスト ファイルに格納されている必要があります。 PolyBase では、どちらの場所からでも読み込みが可能です。

Azure Storage へのデータの移動で使用できるツールやサービスは、次のとおりです。

- [Azure ExpressRoute](../../expressroute/expressroute-introduction.md) サービス - ネットワークのスループット、パフォーマンス、予測可能性を向上させます。 ExpressRoute は、専用プライベート接続を通してデータを Azure にルーティングするサービスです。 ExpressRoute 接続では、パブリック インターネットを通してデータをルーティングすることはありません。 ExpressRoute 接続は、パブリック インターネットを通る一般的な接続に比べて安全性と信頼性が高く、待機時間も短く、高速です。
- [AZCopy ユーティリティ](../../storage/common/storage-use-azcopy-v10.md) - パブリック インターネットを通してデータを Azure Storage に移動します。 このユーティリティは、データ サイズが 10 TB より小さい場合に機能します。 AZCopy を使用して読み込みを定期的に実行するには、ネットワーク速度をテストして、許容可能かどうかを確認してください。
- [Azure Data Factory (ADF)](../../data-factory/introduction.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) - ゲートウェイをローカル サーバーにインストールできます。 その後、ローカル サーバーから Azure Storage にデータを移動するためのパイプラインを作成できます。 専用 SQL プールで Data Factory を使用する方法については、[専用 SQL プールへのデータの読み込み](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)に関するページを参照してください。

## <a name="3-prepare-the-data-for-loading"></a>3.読み込むデータを準備する

専用 SQL プールに読み込む前に、ストレージ アカウント内でデータを準備し、整理する必要がある場合があります。 データの準備は、データがソース内にあるとき、データをテキスト ファイルにエクスポートするとき、またはデータが Azure Storage に配置された後に実施できます。  データの操作は、プロセスの早い段階の方が、最も簡単に行えます。  

### <a name="define-external-tables"></a>外部テーブルを定義する

データを読み込む前に、データ ウェアハウスに外部テーブルを定義する必要があります。 PolyBase は、外部テーブルを使用して Azure Storage のデータを定義し、それにアクセスします。 外部テーブルは、データベースのビューに似ています。 外部テーブルは、テーブル スキーマを含んでおり、またデータ ウェアハウスの外部に格納されたデータをポイントします。

外部テーブルを定義するには、データ ソース、テキスト ファイルの形式、テーブル定義を指定する必要があります。 必要な T-SQL 構文のトピックは次のとおりです。

- [CREATE EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [CREATE EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest&preserve-view=true)

### <a name="format-text-files"></a>テキスト ファイルの書式設定

外部オブジェクトを定義したら、外部テーブルおよびファイル形式の定義に合わせて、テキスト ファイルの行を配置する必要があります。 テキスト ファイルの各行のデータは、テーブル定義と一致させる必要があります。
テキスト ファイルを書式設定するには、次の処理を行います。

- データが非リレーショナル ソースから読み込まれる場合は、データを行と列に変換する必要があります。 データの読み込み元がリレーショナル ソースの場合も、非リレーショナル ソースの場合も、データを読み込むテーブルの列定義に合わせてデータを変換する必要があります。
- SQL プールの変換先テーブルの列とデータ型に合わせて、テキスト ファイルのデータを書式設定します。 外部テキスト ファイルとデータ ウェアハウス テーブルの間でデータ型の不整合があると、読み込みの際に行が拒否されます。
- テキスト ファイル内のフィールドは終端記号で区切ります。  ソース データに含まれていない文字または文字シーケンスを使用するようにしてください。 [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest&preserve-view=true) で指定した終端記号を使用します。

## <a name="4-load-the-data-into-dedicated-sql-pool-staging-tables-using-polybase"></a>4.PolyBase を使用して専用 SQL プール ステージング テーブルにデータを読み込みます

これがステージング テーブルにデータを読み込むための最善の方法です。 ステージング テーブルを使用すると、運用環境のテーブルに支障をきたすことなく、エラーを処理することができます。 また、ステージング テーブルを使用すれば、運用テーブルにデータを挿入する前に、SQL プールの組み込みの分散クエリ処理機能を使用してデータを変換できます。

### <a name="options-for-loading-with-polybase"></a>PolyBase を使用してデータを読み込むためのオプション

PolyBase を使用してデータを読み込むには、次のいずれかの読み込みオプションを使用します。

- [T-SQL を使用した PolyBase](../sql-data-warehouse/load-data-from-azure-blob-storage-using-copy.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) - データが Azure Blob Storage または Azure Data Lake Store 内にある場合に最適です。 読み込みプロセスを細かく制御できますが、外部データ オブジェクトの定義も必要となります。 その他の方法では、外部データ オブジェクトは、ソース テーブルを移行先テーブルにマップするときにバック グラウンドで定義されます。  T-SQL の読み込みを調整するには、Azure Data Factory、SSIS、または Azure Functions を使用します。
- [SSIS を使用した PolyBase](/sql/integration-services/load-data-to-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true) は、ソースデータが SQL Server にある場合に適しています。 SSIS は、移動元テーブルと移動先テーブルのマッピングを定義するほか、読み込みの調整も行います。 SSIS パッケージが既にある場合、そのパッケージが移動先の新しいデータ ウェアハウスで機能するように変更できます。
- [Azure Data Factory (ADF) を使用した PolyBase](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) - もう 1 つのオーケストレーション ツールです。  このツールはパイプラインを定義し、ジョブのスケジュールを設定します。
- [Azure Databricks を使用した PolyBase](/azure/databricks/scenarios/databricks-extract-load-sql-data-warehouse?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) - PolyBase を使用して、Azure Synapse Analytic テーブルから Databricks データ フレームにデータを転送することや、Databricks データ フレームから Azure Synapse Analytics テーブルにデータを書き込むことができます。

### <a name="non-polybase-loading-options"></a>PolyBase 以外の読み込みオプション

使用するデータが PolyBase と互換性がない場合は、[bcp](/sql/tools/bcp-utility?view=azure-sqldw-latest&preserve-view=true) または [SQLBulkCopy API](/dotnet/api/system.data.sqlclient.sqlbulkcopy) を使用できます。 bcp を使用すると、Azure Blob Storage を通さずに専用 SQL プールに直接読み込まれます。また、これは小規模の読み込みのみを対象とします。 これらのオプションの読み込みパフォーマンスは、PolyBase と比べてはるかに低速であることに注意してください。

## <a name="5-transform-the-data"></a>5.データの変換

データがステージング テーブルにある間に、ワークロードに必要な変換を実行します。 その後、運用環境テーブルにデータを移動します。

## <a name="6-insert-the-data-into-production-tables"></a>6.運用環境テーブルにデータを挿入する

INSERT INTO ...SELECT ステートメントを実行すると、データがステージング テーブルから永続テーブルに移動します。

ETL プロセスを設計する際は、小規模のテスト サンプルでプロセスを実行してみてください。 テーブルから 1 つのファイルに 1000 行を抽出し、Azure に移動してから、ステージング テーブルに読み込んでみてください。

## <a name="partner-loading-solutions"></a>パートナー読み込みソリューション

パートナーの多くが読み込みソリューションを提供しています。 詳細については、[ソリューション パートナー](../sql-data-warehouse/sql-data-warehouse-partner-business-intelligence.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)の一覧をご覧ください。

## <a name="next-steps"></a>次のステップ

読み込みのガイダンスについては、[データ読み込みのガイダンス](data-loading-best-practices.md)に関するページをご覧ください。