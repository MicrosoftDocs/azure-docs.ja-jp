---
title: Contoso Retail データを専用 SQL プールに読み込む
description: PolyBase および T-SQL コマンドを使用して、Contoso Retail データの 2 つのテーブルを専用 SQL プールに読み込みます。
services: synapse-analytics
author: gaursa
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/20/2020
ms.author: gaursa
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 13e78c707ca3bda338f9255c015c0e926fca90d8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104606142"
---
# <a name="load-contoso-retail-data-into-dedicated-sql-pools-in-azure-synapse-analytics"></a>Azure Synapse Analytics の専用 SQL プールに Contoso Retail データを読み込む

このチュートリアルでは、PolyBase および T-SQL コマンドを使用して、Contoso Retail データの 2 つのテーブルを専用 SQL プールに読み込む方法を説明します。

このチュートリアルでは、次のことについて説明します。

1. Azure Blob Storage から読み込むために PolyBase を構成する
2. パブリック データをデータベースに読み込む
3. 読み込みの完了後、最適化を実行する。

## <a name="before-you-begin"></a>開始する前に

このチュートリアルを実行するには、専用 SQL プールが既に設定されている Azure アカウントが必要です。 データ ウェアハウスがプロビジョニングされていない場合は、[データ ウェアハウスの作成とサーバーレベルのファイアウォール規則の設定](create-data-warehouse-portal.md)に関する記事を参照してください。

## <a name="configure-the-data-source"></a>データ ソースの構成

PolyBase では T-SQL 外部オブジェクトを使用して、外部データの場所と属性を定義します。 外部オブジェクトの定義は、専用 SQL プールに格納されます。 データは外部に保存されます。

## <a name="create-a-credential"></a>資格情報の作成

Contoso のパブリック データを読み込もうとしている場合は、**この手順をスキップ** してください。 パブリック データにはだれでもアクセスできるため、セキュリティで保護されたアクセスは必要ありません。

独自のデータを読み込むためにこのチュートリアルをテンプレートとして使用している場合は、**この手順をスキップしないでください**。 資格情報を使用してデータにアクセスするには、次のスクリプトを使用してデータベース スコープの資格情報を作成します。 その後、データ ソースの場所を定義するときにそれを使用します。

```sql
-- A: Create a master key.
-- Only necessary if one does not already exist.
-- Required to encrypt the credential secret in the next step.

CREATE MASTER KEY;


-- B: Create a database scoped credential
-- IDENTITY: Provide any string, it is not used for authentication to Azure storage.
-- SECRET: Provide your Azure storage account key.


CREATE DATABASE SCOPED CREDENTIAL AzureStorageCredential
WITH
    IDENTITY = 'user',
    SECRET = '<azure_storage_account_key>'
;


-- C: Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure blob storage.
-- LOCATION: Provide Azure storage account name and blob container name.
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureStorage
WITH (
    TYPE = HADOOP,
    LOCATION = 'wasbs://<blob_container_name>@<azure_storage_account_name>.blob.core.windows.net',
    CREDENTIAL = AzureStorageCredential
);
```

## <a name="create-the-external-data-source"></a>外部データ ソースを作成する

この [CREATE EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) コマンドを使って、データの場所とデータ型を格納します。

```sql
CREATE EXTERNAL DATA SOURCE AzureStorage_west_public
WITH
(  
    TYPE = Hadoop
,   LOCATION = 'wasbs://contosoretaildw-tables@contosoretaildw.blob.core.windows.net/'
);
```

> [!IMPORTANT]
> Azure Blob Storage コンテナーをパブリックにすると、データがデータ センターから発信されるときに、データ所有者としてデータ送信料金が課金されることに注意してください。

## <a name="configure-the-data-format"></a>データ形式の構成

データは Azure Blob Storage 内のテキスト ファイルに格納され、各フィールドは区切り記号で区切られます。 SSMS で、次の CREATE EXTERNAL FILE FORMAT コマンドを実行して、テキスト ファイル内のデータ形式を指定します。 Contoso データは圧縮されず、パイプで区切られます。

```sql
CREATE EXTERNAL FILE FORMAT TextFileFormat
WITH
(   FORMAT_TYPE = DELIMITEDTEXT
,    FORMAT_OPTIONS    (   FIELD_TERMINATOR = '|'
                    ,    STRING_DELIMITER = ''
                    ,    DATE_FORMAT         = 'yyyy-MM-dd HH:mm:ss.fff'
                    ,    USE_TYPE_DEFAULT = FALSE
                    )
);
```

## <a name="create-the-schema-for-the-external-tables"></a>外部テーブルのスキーマを作成する

データ ソースとファイル形式を指定したので、外部テーブルのスキーマを作成する準備ができました。

データベースの Contoso データを格納する場所を作成するには、スキーマを作成します。

```sql
CREATE SCHEMA [asb]
GO
```

## <a name="create-the-external-tables"></a>外部テーブルの作成

次のスクリプトを実行して、DimProduct と FactOnlineSales 外部テーブルを作成します。 ここでは、列名とデータ型を定義し、それらを Azure Blob Storage ファイルの場所と形式にバインドするだけです。 定義はデータ ウェアハウスに格納されますが、データはまだ Azure Storage BLOB にあります。

**LOCATION** パラメーターは、Azure Storage BLOB のルート フォルダーの下にあるフォルダーです。 各テーブルは、別のフォルダーにあります。

```sql
--DimProduct
CREATE EXTERNAL TABLE [asb].DimProduct (
    [ProductKey] [int] NOT NULL,
    [ProductLabel] [nvarchar](255) NULL,
    [ProductName] [nvarchar](500) NULL,
    [ProductDescription] [nvarchar](400) NULL,
    [ProductSubcategoryKey] [int] NULL,
    [Manufacturer] [nvarchar](50) NULL,
    [BrandName] [nvarchar](50) NULL,
    [ClassID] [nvarchar](10) NULL,
    [ClassName] [nvarchar](20) NULL,
    [StyleID] [nvarchar](10) NULL,
    [StyleName] [nvarchar](20) NULL,
    [ColorID] [nvarchar](10) NULL,
    [ColorName] [nvarchar](20) NOT NULL,
    [Size] [nvarchar](50) NULL,
    [SizeRange] [nvarchar](50) NULL,
    [SizeUnitMeasureID] [nvarchar](20) NULL,
    [Weight] [float] NULL,
    [WeightUnitMeasureID] [nvarchar](20) NULL,
    [UnitOfMeasureID] [nvarchar](10) NULL,
    [UnitOfMeasureName] [nvarchar](40) NULL,
    [StockTypeID] [nvarchar](10) NULL,
    [StockTypeName] [nvarchar](40) NULL,
    [UnitCost] [money] NULL,
    [UnitPrice] [money] NULL,
    [AvailableForSaleDate] [datetime] NULL,
    [StopSaleDate] [datetime] NULL,
    [Status] [nvarchar](7) NULL,
    [ImageURL] [nvarchar](150) NULL,
    [ProductURL] [nvarchar](150) NULL,
    [ETLLoadID] [int] NULL,
    [LoadDate] [datetime] NULL,
    [UpdateDate] [datetime] NULL
)
WITH
(
    LOCATION='/DimProduct/'
,   DATA_SOURCE = AzureStorage_west_public
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;

--FactOnlineSales
CREATE EXTERNAL TABLE [asb].FactOnlineSales
(
    [OnlineSalesKey] [int]  NOT NULL,
    [DateKey] [datetime] NOT NULL,
    [StoreKey] [int] NOT NULL,
    [ProductKey] [int] NOT NULL,
    [PromotionKey] [int] NOT NULL,
    [CurrencyKey] [int] NOT NULL,
    [CustomerKey] [int] NOT NULL,
    [SalesOrderNumber] [nvarchar](20) NOT NULL,
    [SalesOrderLineNumber] [int] NULL,
    [SalesQuantity] [int] NOT NULL,
    [SalesAmount] [money] NOT NULL,
    [ReturnQuantity] [int] NOT NULL,
    [ReturnAmount] [money] NULL,
    [DiscountQuantity] [int] NULL,
    [DiscountAmount] [money] NULL,
    [TotalCost] [money] NOT NULL,
    [UnitCost] [money] NULL,
    [UnitPrice] [money] NULL,
    [ETLLoadID] [int] NULL,
    [LoadDate] [datetime] NULL,
    [UpdateDate] [datetime] NULL
)
WITH
(
    LOCATION='/FactOnlineSales/'
,   DATA_SOURCE = AzureStorage_west_public
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;
```

## <a name="load-the-data"></a>データを読み込む

外部データにはさまざまな方法でアクセスできます。  外部テーブルから直接データをクエリできます。また、そのデータをデータ ウェアハウスの新しいテーブルに読み込んだり、外部データを既存のデータ ウェアハウス テーブルに追加したりできます。  

### <a name="create-a-new-schema"></a>新しいスキーマを作成する

CTAS により、データを含む新しいテーブルが作成されます。  最初に、contoso データのスキーマを作成します。

```sql
CREATE SCHEMA [cso]
GO
```

### <a name="load-the-data-into-new-tables"></a>データを新しいテーブルに読み込む

Azure Blob Storage からデータ ウェアハウス テーブルにデータを読み込むには、[CREATE TABLE AS SELECT (Transact-SQL)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) ステートメントを使用します。 [CTAS](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) による読み込みでは、自分で作成した厳密に型指定された外部テーブルを使用します。 新しいテーブルにデータを読み込む場合は、テーブルごとに 1 つの CTAS ステートメントを使用してください。

CTAS により新しいテーブルが作成され、select ステートメントの結果が設定されます。 CTAS では、select ステートメントの結果と同じ列とデータ型が保持されるように、新しいテーブルが定義されます。 外部テーブルからすべての列を選択すると、新しいテーブルは、外部テーブルの列とデータ型のレプリカになります。

この例では、ディメンションとファクト テーブルの両方を、ハッシュ分散テーブルとして作成します。

```sql
SELECT GETDATE();
GO

CREATE TABLE [cso].[DimProduct]            WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[DimProduct]             OPTION (LABEL = 'CTAS : Load [cso].[DimProduct]             ');
CREATE TABLE [cso].[FactOnlineSales]       WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[FactOnlineSales]        OPTION (LABEL = 'CTAS : Load [cso].[FactOnlineSales]        ');
```

### <a name="track-the-load-progress"></a>読み込みの進行状況の追跡

読み込みの進捗状況を追跡するには、動的管理ビュー (DMV) を使用します。

```sql
-- To see all requests
SELECT * FROM sys.dm_pdw_exec_requests;

-- To see a particular request identified by its label
SELECT * FROM sys.dm_pdw_exec_requests as r
WHERE r.[label] = 'CTAS : Load [cso].[DimProduct]             '
      OR r.[label] = 'CTAS : Load [cso].[FactOnlineSales]        '
;

-- To track bytes and files
SELECT
    r.command,
    s.request_id,
    r.status,
    count(distinct input_name) as nbr_files,
    sum(s.bytes_processed)/1024/1024/1024 as gb_processed
FROM
    sys.dm_pdw_exec_requests r
    inner join sys.dm_pdw_dms_external_work s
        on r.request_id = s.request_id
WHERE
    r.[label] = 'CTAS : Load [cso].[DimProduct]             '
    OR r.[label] = 'CTAS : Load [cso].[FactOnlineSales]        '
GROUP BY
    r.command,
    s.request_id,
    r.status
ORDER BY
    nbr_files desc,
    gb_processed desc;
```

## <a name="optimize-columnstore-compression"></a>列ストア圧縮の最適化

既定では、専用 SQL プールには、テーブルがクラスター化列ストア インデックスとして格納されます。 読み込みの完了時、一部のデータ行が、列ストアに圧縮されない可能性があります。  これが発生する理由はさまざまです。 詳しくは、[列ストア インデックスの管理](sql-data-warehouse-tables-index.md)に関するページをご覧ください。

読み込み後のクエリのパフォーマンスと列ストア圧縮を最適化するには、列ストア インデックスですべての行が強制的に圧縮されるようにテーブルを再構築します。

```sql
SELECT GETDATE();
GO

ALTER INDEX ALL ON [cso].[DimProduct]               REBUILD;
ALTER INDEX ALL ON [cso].[FactOnlineSales]          REBUILD;
```

列ストア インデックスの保守の詳細については、 [列ストア インデックスの管理](sql-data-warehouse-tables-index.md) に関する記事をご覧ください。

## <a name="optimize-statistics"></a>統計の最適化

読み込みの直後に単一列の統計を作成することをお勧めします。 クエリ述語に含まれない列があることがわかっている場合は、その列に対する統計の作成はスキップできます。 すべての列に対して単一列の統計を作成する場合は、すべての統計の再構築に時間がかかる場合があります。

すべてのテーブルのすべての列で単一列の統計を作成する場合は、[統計に関する記事](sql-data-warehouse-tables-statistics.md)のストアド プロシージャのコード サンプル `prc_sqldw_create_stats` を使うことができます。

次の例は、統計の作成の出発点として適しています。 これによりディメンション テーブルの各列と、ファクト テーブルの各結合列に、単一列の統計が作成されます。 他のファクト テーブルの列には、後で単一列または複数列の統計をいつでも追加できます。

```sql
CREATE STATISTICS [stat_cso_DimProduct_AvailableForSaleDate] ON [cso].[DimProduct]([AvailableForSaleDate]);
CREATE STATISTICS [stat_cso_DimProduct_BrandName] ON [cso].[DimProduct]([BrandName]);
CREATE STATISTICS [stat_cso_DimProduct_ClassID] ON [cso].[DimProduct]([ClassID]);
CREATE STATISTICS [stat_cso_DimProduct_ClassName] ON [cso].[DimProduct]([ClassName]);
CREATE STATISTICS [stat_cso_DimProduct_ColorID] ON [cso].[DimProduct]([ColorID]);
CREATE STATISTICS [stat_cso_DimProduct_ColorName] ON [cso].[DimProduct]([ColorName]);
CREATE STATISTICS [stat_cso_DimProduct_ETLLoadID] ON [cso].[DimProduct]([ETLLoadID]);
CREATE STATISTICS [stat_cso_DimProduct_ImageURL] ON [cso].[DimProduct]([ImageURL]);
CREATE STATISTICS [stat_cso_DimProduct_LoadDate] ON [cso].[DimProduct]([LoadDate]);
CREATE STATISTICS [stat_cso_DimProduct_Manufacturer] ON [cso].[DimProduct]([Manufacturer]);
CREATE STATISTICS [stat_cso_DimProduct_ProductDescription] ON [cso].[DimProduct]([ProductDescription]);
CREATE STATISTICS [stat_cso_DimProduct_ProductKey] ON [cso].[DimProduct]([ProductKey]);
CREATE STATISTICS [stat_cso_DimProduct_ProductLabel] ON [cso].[DimProduct]([ProductLabel]);
CREATE STATISTICS [stat_cso_DimProduct_ProductName] ON [cso].[DimProduct]([ProductName]);
CREATE STATISTICS [stat_cso_DimProduct_ProductSubcategoryKey] ON [cso].[DimProduct]([ProductSubcategoryKey]);
CREATE STATISTICS [stat_cso_DimProduct_ProductURL] ON [cso].[DimProduct]([ProductURL]);
CREATE STATISTICS [stat_cso_DimProduct_Size] ON [cso].[DimProduct]([Size]);
CREATE STATISTICS [stat_cso_DimProduct_SizeRange] ON [cso].[DimProduct]([SizeRange]);
CREATE STATISTICS [stat_cso_DimProduct_SizeUnitMeasureID] ON [cso].[DimProduct]([SizeUnitMeasureID]);
CREATE STATISTICS [stat_cso_DimProduct_Status] ON [cso].[DimProduct]([Status]);
CREATE STATISTICS [stat_cso_DimProduct_StockTypeID] ON [cso].[DimProduct]([StockTypeID]);
CREATE STATISTICS [stat_cso_DimProduct_StockTypeName] ON [cso].[DimProduct]([StockTypeName]);
CREATE STATISTICS [stat_cso_DimProduct_StopSaleDate] ON [cso].[DimProduct]([StopSaleDate]);
CREATE STATISTICS [stat_cso_DimProduct_StyleID] ON [cso].[DimProduct]([StyleID]);
CREATE STATISTICS [stat_cso_DimProduct_StyleName] ON [cso].[DimProduct]([StyleName]);
CREATE STATISTICS [stat_cso_DimProduct_UnitCost] ON [cso].[DimProduct]([UnitCost]);
CREATE STATISTICS [stat_cso_DimProduct_UnitOfMeasureID] ON [cso].[DimProduct]([UnitOfMeasureID]);
CREATE STATISTICS [stat_cso_DimProduct_UnitOfMeasureName] ON [cso].[DimProduct]([UnitOfMeasureName]);
CREATE STATISTICS [stat_cso_DimProduct_UnitPrice] ON [cso].[DimProduct]([UnitPrice]);
CREATE STATISTICS [stat_cso_DimProduct_UpdateDate] ON [cso].[DimProduct]([UpdateDate]);
CREATE STATISTICS [stat_cso_DimProduct_Weight] ON [cso].[DimProduct]([Weight]);
CREATE STATISTICS [stat_cso_DimProduct_WeightUnitMeasureID] ON [cso].[DimProduct]([WeightUnitMeasureID]);
CREATE STATISTICS [stat_cso_FactOnlineSales_CurrencyKey] ON [cso].[FactOnlineSales]([CurrencyKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_CustomerKey] ON [cso].[FactOnlineSales]([CustomerKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_DateKey] ON [cso].[FactOnlineSales]([DateKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_OnlineSalesKey] ON [cso].[FactOnlineSales]([OnlineSalesKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_ProductKey] ON [cso].[FactOnlineSales]([ProductKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_PromotionKey] ON [cso].[FactOnlineSales]([PromotionKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_StoreKey] ON [cso].[FactOnlineSales]([StoreKey]);
```

## <a name="achievement-unlocked"></a>結果

パブリック データがデータ ウェアハウスに正常に読み込まれました。 すばらしい結果です。

これで、テーブルのクエリを開始して、データを探索することができます。 ブランドごとの売上合計を調べるには、次のクエリを実行します。

```sql
SELECT  SUM(f.[SalesAmount]) AS [sales_by_brand_amount]
,       p.[BrandName]
FROM    [cso].[FactOnlineSales] AS f
JOIN    [cso].[DimProduct]      AS p ON f.[ProductKey] = p.[ProductKey]
GROUP BY p.[BrandName]
```

## <a name="next-steps"></a>次のステップ

完全なデータ セットを読み込むには、Microsoft SQL Server のサンプル リポジトリから[完全な Contoso Retail データ ウェアハウスを読み込む](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/contoso-data-warehouse/readme.md)例を実行します。
開発に関するその他のヒントについては、[データ ウェアハウスの設計上の決定とコーディング技法](sql-data-warehouse-overview-develop.md)に関する記事を参照してください。
