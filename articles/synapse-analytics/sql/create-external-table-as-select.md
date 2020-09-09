---
title: クエリ結果をストレージに格納する
description: この記事では、SQL オンデマンド (プレビュー) を使用してクエリ結果をストレージに格納する方法を学習します。
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 610391cefe88f6d066f4af12f6fb88f55b1fe56b
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2020
ms.locfileid: "85206549"
---
# <a name="store-query-results-to-storage-using-sql-on-demand-preview-using-azure-synapse-analytics"></a>Azure Synapse Analytics を使用して SQL オンデマンド (プレビュー) でクエリ結果をストレージに格納する

この記事では、SQL オンデマンド (プレビュー) を使用してクエリ結果をストレージに格納する方法を学習します。

## <a name="prerequisites"></a>前提条件

最初の手順として、クエリを実行する**データベースを作成**します。 次に、そのデータベースで[セットアップ スクリプト](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql)を実行して、オブジェクトを初期化します。 このセットアップ スクリプトにより、データ ソース、データベース スコープの資格情報、これらのサンプルでデータの読み取りに使用される外部ファイル形式が作成されます。

この記事の手順に従って、データ ソース、データベース スコープの資格情報、出力ストレージへのデータの書き込みに使用される外部ファイル形式を作成してください。

## <a name="create-external-table-as-select"></a>CREATE EXTERNAL TABLE AS SELECT

CREATE EXTERNAL TABLE AS SELECT (CETAS) ステートメントを使用して、クエリ結果をストレージに格納することができます。

> [!NOTE]
> クエリの最初の行 ([mydbname]) は、自分で作成したデータベースを使用するように変更してください。

```sql
USE [mydbname];
GO

CREATE DATABASE SCOPED CREDENTIAL [SasTokenWrite]
WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
     SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO

CREATE EXTERNAL DATA SOURCE [MyDataSource] WITH (
    LOCATION = 'https://<storage account name>.blob.core.windows.net/csv', CREDENTIAL = [SasTokenWrite]
);
GO

CREATE EXTERNAL FILE FORMAT [ParquetFF] WITH (
    FORMAT_TYPE = PARQUET,
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'
);
GO

CREATE EXTERNAL TABLE [dbo].[PopulationCETAS] WITH (
        LOCATION = 'populationParquet/',
        DATA_SOURCE = [MyDataSource],
        FILE_FORMAT = [ParquetFF]
) AS
SELECT
    *
FROM
    OPENROWSET(
        BULK 'csv/population-unix/population.csv',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0'
    ) WITH (
        CountryCode varchar(4),
        CountryName varchar(64),
        Year int,
        PopulationCount int
    ) AS r;

```

## <a name="use-the-external-table"></a>外部テーブルを使用する

CETAS で作成した外部テーブルは、通常の外部テーブルと同じように使用できます。

> [!NOTE]
> クエリの最初の行 ([mydbname]) は、自分で作成したデータベースを使用するように変更してください。

```sql
USE [mydbname];
GO

SELECT
    country_name, population
FROM PopulationCETAS
WHERE
    [year] = 2019
ORDER BY
    [population] DESC;
```

## <a name="next-steps"></a>次のステップ

さまざまな種類のファイルに対するクエリの実行については、[単一の CSV ファイルに対するクエリの実行](query-single-csv-file.md)、[Parquet ファイルに対するクエリの実行](query-parquet-files.md)、および [JSON ファイルに対するクエリの実行](query-json-files.md)に関するページを参照してください。
