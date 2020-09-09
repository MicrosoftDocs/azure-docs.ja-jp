---
title: ETL の代わりに ELT を設計する
description: Azure Synapse Analytics 内に Synapse SQL プールの柔軟なデータ読み込み戦略を実装する
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 05/13/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 1b73b82b4367d50cc5fbe9881a67e0afa041db86
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85201160"
---
# <a name="data-loading-strategies-for-synapse-sql-pool"></a>Synapse SQL プールのデータの読み込み戦略

従来の SMP SQL プールでは、データの読み込みに抽出、変換、読み込み (ETL) プロセスが使用されています。 Azure Synapse Analytics 内の Synapse SQL プールには、コンピューティング リソースとストレージ リソースのスケーラビリティと柔軟性を活用した超並列処理 (MPP) アーキテクチャがあります。

抽出、読み込み、変換 (ELT) プロセスを使用すると、MPP を活用し、読み込み前にデータ変換に必要なリソースを排除できます。

SQL プールでは、[bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)、[SqlBulkCopy API](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) などの一般的な SQL Server オプションを含む多くの読み込み方法がサポートされていますが、データを読み込むための最速かつ最もスケーラブルな方法は、PolyBase 外部テーブルと [COPY ステートメント](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (プレビュー) を使用することです。

PolyBase と COPY ステートメントを使用すると、Azure Blob Storage または Azure Data Lake Store に格納されている外部データに T-SQL 言語でアクセスできます。 読み込み時の柔軟性を最大限に高めるために、COPY ステートメントを使用することをお勧めします。

> [!NOTE]  
> COPY ステートメントは、現在、パブリック プレビュー段階にあります。 ご意見やご感想は、配布リストの sqldwcopypreview@service.microsoft.com までメールでお寄せください。

> [!VIDEO https://www.youtube.com/embed/l9-wP7OdhDk]

## <a name="what-is-elt"></a>ELT とは?

ELT (抽出、読み込み、変換) とは、データがソース システムから抽出されて SQL プールに読み込まれ、その後変換されるプロセスです。

ELT を実装するための基本的な手順は次のとおりです。

1. ソース データをテキスト ファイルに抽出します。
2. そのデータを Azure Blob Storage または Azure Data Lake Store に配置します。
3. 読み込むデータを準備します。
4. PolyBase または COPY コマンドを使用して、ステージング テーブルにデータを読み込みます。
5. データを変換します。
6. 運用環境テーブルにデータを挿入します。

読み込みのチュートリアルについては、[ Azure Blob Storage からのデータの読み込み](load-data-from-azure-blob-storage-using-polybase.md)に関する記事をご覧ください。

## <a name="1-extract-the-source-data-into-text-files"></a>1.ソース データをテキスト ファイルに抽出する

ソース システムからのデータの取得方法は、保存場所によって異なります。 目標は、サポートされている区切りテキストまたは CSV ファイルにデータを移動することです。

### <a name="supported-file-formats"></a>サポートされるファイル形式

PolyBase および COPY ステートメントを使用すると、UTF-8 および UTF-16 でエンコードされた区切りテキストまたは CSV ファイルから、データを読み込むことができます。 区切りテキストまたは CSV ファイルに加え、ORC や Parquet などの Hadoop ファイル形式からも読み込みます。 また、PolyBase および COPY ステートメントは、Gzip および Snappy 圧縮ファイルからもデータを読み込むことができます。

拡張 ASCII、固定幅形式、および WinZip や XML などの入れ子形式は、サポートされていません。 SQL Server からエクスポートする場合は、[bcp コマンドライン ツール](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)を使用して、区切りテキスト ファイルにデータをエクスポートすることができます。

## <a name="2-land-the-data-into-azure-blob-storage-or-azure-data-lake-store"></a>2.データを Azure Blob Storage または Azure Data Lake Store に配置する

Azure Storage にデータを配置するには、[Azure Blob Storage](../../storage/blobs/storage-blobs-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) または [Azure Data Lake Store Gen2](../../data-lake-store/data-lake-store-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) にデータを移動します。 どちらの場合も、データはテキスト ファイルに格納されている必要があります。 PolyBase および COPY ステートメントは、どちらの場所からも読み込むことができます。

Azure Storage へのデータの移動で使用できるツールやサービスは、次のとおりです。

- [Azure ExpressRoute](../../expressroute/expressroute-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) サービス - ネットワークのスループット、パフォーマンス、予測可能性を向上させます。 ExpressRoute は、専用プライベート接続を通してデータを Azure にルーティングするサービスです。 ExpressRoute 接続では、パブリック インターネットを通してデータをルーティングすることはありません。 ExpressRoute 接続は、パブリック インターネットを通る一般的な接続に比べて安全性と信頼性が高く、待機時間も短く、高速です。
- [AZCopy ユーティリティ](../../storage/common/storage-choose-data-transfer-solution.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) - パブリック インターネットを通してデータを Azure Storage に移動します。 このユーティリティは、データ サイズが 10 TB より小さい場合に機能します。 AZCopy を使用して読み込みを定期的に実行するには、ネットワーク速度をテストして、許容可能かどうかを確認してください。
- [Azure Data Factory (ADF)](../../data-factory/introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) - ゲートウェイをローカル サーバーにインストールできます。 その後、ローカル サーバーから Azure Storage にデータを移動するためのパイプラインを作成できます。 SQL プールで Data Factory を使用する方法については、[SQL プールのデータの読み込み](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)に関するページを参照してください。

## <a name="3-prepare-the-data-for-loading"></a>3.読み込むデータを準備する

読み込む前に、ストレージ アカウントのデータを準備してクリーンアップすることが必要になる場合があります。 データの準備は、データがソース内にあるとき、データをテキスト ファイルにエクスポートするとき、またはデータが Azure Storage に配置された後に実施できます。  データの操作は、プロセスの早い段階の方が、最も簡単に行えます。  

### <a name="define-the-tables"></a>テーブルを定義する

COPY ステートメントを使用する場合は、最初に SQL プールで読み込み先のテーブルを定義する必要があります。

PolyBase を使用している場合は、読み込み前に、SQL プールに外部テーブルを定義する必要があります。 PolyBase は、外部テーブルを使用して Azure Storage のデータを定義し、それにアクセスします。 外部テーブルは、データベースのビューに似ています。 外部テーブルにはテーブル スキーマが含まれており、SQL プールの外部に格納されているデータを指します。

外部テーブルを定義するには、データ ソース、テキスト ファイルの形式、テーブル定義を指定する必要があります。 必要な T-SQL 構文の参照記事は次のとおりです。

- [CREATE EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CREATE EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

Parquet ファイルを読み込む場合、次の SQL データ型マッピングを使用します。

|                         Parquet 型                         |   Parquet 論理型 (注釈)   |  SQL データ型   |
| :----------------------------------------------------------: | :-----------------------------------: | :--------------: |
|                           BOOLEAN                            |                                       |       bit        |
|                     BINARY / BYTE_ARRAY                      |                                       |    varbinary     |
|                            DOUBLE                            |                                       |      float       |
|                            FLOAT                             |                                       |       real       |
|                            INT32                             |                                       |       INT        |
|                            INT64                             |                                       |      bigint      |
|                            INT96                             |                                       |    datetime2     |
|                     FIXED_LEN_BYTE_ARRAY                     |                                       |      binary      |
|                            BINARY                            |                 UTF8                  |     nvarchar     |
|                            BINARY                            |                STRING                 |     nvarchar     |
|                            BINARY                            |                 ENUM                  |     nvarchar     |
|                            BINARY                            |                 UUID                  | UNIQUEIDENTIFIER |
|                            BINARY                            |                DECIMAL                |     decimal      |
|                            BINARY                            |                 JSON                  |  nvarchar(MAX)   |
|                            BINARY                            |                 BSON                  |  varbinary(max)  |
|                     FIXED_LEN_BYTE_ARRAY                     |                DECIMAL                |     decimal      |
|                          BYTE_ARRAY                          |               INTERVAL                |  varchar(max)   |
|                            INT32                             |             INT(8, true)              |     smallint     |
|                            INT32                             |            INT(16, true)            |     smallint     |
|                            INT32                             |             INT(32, true)             |       INT        |
|                            INT32                             |            INT(8, false)            |     tinyint      |
|                            INT32                             |            INT(16, false)             |       INT        |
|                            INT32                             |           INT(32, false)            |      bigint      |
|                            INT32                             |                 DATE                  |       date       |
|                            INT32                             |                DECIMAL                |     decimal      |
|                            INT32                             |            TIME (MILLIS)             |       time       |
|                            INT64                             |            INT(64, true)            |      bigint      |
|                            INT64                             |           INT(64, false)            |  decimal (20,0)   |
|                            INT64                             |                DECIMAL                |     decimal      |
|                            INT64                             |         TIME (MICROS / NANOS)         |       time       |
|                            INT64                             | TIMESTAMP (MILLIS / MICROS / NANOS) |    datetime2     |
| [複合型](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fgithub.com%2Fapache%2Fparquet-format%2Fblob%2Fmaster%2FLogicalTypes.md%23lists&data=02\|01\|kevin%40microsoft.com\|19f74d93f5ca45a6b73c08d7d7f5f111\|72f988bf86f141af91ab2d7cd011db47\|1\|0\|637215323617803168&sdata=6Luk047sK26ijTzfvKMYc%2FNu%2Fz0AlLCX8lKKTI%2F8B5o%3D&reserved=0) |                 リスト                  |   varchar(max)   |
| [複合型](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fgithub.com%2Fapache%2Fparquet-format%2Fblob%2Fmaster%2FLogicalTypes.md%23maps&data=02\|01\|kevin%40microsoft.com\|19f74d93f5ca45a6b73c08d7d7f5f111\|72f988bf86f141af91ab2d7cd011db47\|1\|0\|637215323617803168&sdata=FiThqXxjgmZBVRyigHzfh5V7Z%2BPZHjud2IkUUM43I7o%3D&reserved=0) |                  MAP                  |   varchar(max)   |



外部オブジェクトの作成の例については、[外部テーブルの作成](https://docs.microsoft.com/azure/synapse-analytics/sql/develop-tables-external-tables?tabs=sql-pool)に関する記事を参照してください。

### <a name="format-text-files"></a>テキスト ファイルの書式設定

PolyBase を使用する場合、定義する外部オブジェクトは、外部テーブルおよびファイル形式の定義に合わせて、テキスト ファイルの行を配置する必要があります。 テキスト ファイルの各行のデータは、テーブル定義と一致させる必要があります。
テキスト ファイルを書式設定するには、次の処理を行います。

- データが非リレーショナル ソースから読み込まれる場合は、データを行と列に変換する必要があります。 データの読み込み元がリレーショナル ソースの場合も、非リレーショナル ソースの場合も、データを読み込むテーブルの列定義に合わせてデータを変換する必要があります。
- 変換先テーブルの列とデータ型に合わせて、テキスト ファイルのデータを書式設定します。 外部テキスト ファイルと SQL プール テーブルの間でデータ型の不整合があると、読み込みの際に行が拒否されます。
- テキスト ファイル内のフィールドは終端記号で区切ります。  ソース データに含まれていない文字または文字シーケンスを使用するようにしてください。 [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) で指定した終端記号を使用します。

## <a name="4-load-the-data-using-polybase-or-the-copy-statement"></a>4.PolyBase または COPY ステートメントを使用してデータを読み込む

これがステージング テーブルにデータを読み込むための最善の方法です。 ステージング テーブルを使用すると、運用環境のテーブルに支障をきたすことなく、エラーを処理することができます。 また、ステージング テーブルを使用すれば、運用環境のテーブルにデータを挿入する前に、SQL プールの並列処理アーキテクチャを使用してデータを変換することができます。

### <a name="options-for-loading"></a>読み込みのオプション

データを読み込むには、次のいずれかの読み込みオプションを使用できます。

- [COPY ステートメント](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest)は、シームレスかつ柔軟にデータを読み込むことができるため、推奨されている読み込みユーティリティです。 このステートメントには、PolyBase では提供されない追加の読み込み機能が多数あります。 
- [T-SQL を使用したPolyBase](load-data-from-azure-blob-storage-using-polybase.md) では、外部データ オブジェクトを定義する必要があります。
- [Azure Data Factory (ADF) を使用した PolyBase および COPY ステートメント](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) - もう 1 つのオーケストレーション ツールです。  このツールはパイプラインを定義し、ジョブのスケジュールを設定します。
- [SSIS を使用した PolyBase](/sql/integration-services/load-data-to-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) は、ソースデータが SQL Server にある場合に適しています。 SSIS は、移動元テーブルと移動先テーブルのマッピングを定義するほか、読み込みの調整も行います。 SSIS パッケージが既にある場合、そのパッケージが移動先の新しいデータ ウェアハウスで機能するように変更できます。
- [Azure Databricks を使用した PolyBase](../../azure-databricks/databricks-extract-load-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) - PolyBase を使用して、テーブルから Databricks データ フレームにデータを転送することや、Databricks データ フレームからテーブルにデータを書き込むことができます。

### <a name="other-loading-options"></a>その他の読み込みオプション

PolyBase と COPY ステートメントの他に、[bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) または [SqlBulkCopy API](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) を使用できます。 bcp では、Azure Blob Storage を通さずにデータベースに直接読み込みます。また、小規模の読み込みのみを対象とします。

> [!NOTE]
> これらのオプションの読み込みパフォーマンスは、PolyBase および COPY ステートメントと比べて低速です。

## <a name="5-transform-the-data"></a>5.データの変換

データがステージング テーブルにある間に、ワークロードに必要な変換を実行します。 その後、運用環境テーブルにデータを移動します。

## <a name="6-insert-the-data-into-production-tables"></a>6.運用環境テーブルにデータを挿入する

INSERT INTO ...SELECT ステートメントを実行すると、データがステージング テーブルから永続テーブルに移動します。

ETL プロセスを設計する際は、小規模のテスト サンプルでプロセスを実行してみてください。 テーブルから 1 つのファイルに 1000 行を抽出し、Azure に移動してから、ステージング テーブルに読み込んでみてください。

## <a name="partner-loading-solutions"></a>パートナー読み込みソリューション

パートナーの多くが読み込みソリューションを提供しています。 詳細については、[ソリューション パートナー](sql-data-warehouse-partner-business-intelligence.md)の一覧をご覧ください。

## <a name="next-steps"></a>次のステップ

読み込みのガイダンスについては、[データの読み込みのガイダンス](guidance-for-loading-data.md)に関するページを参照してください。
