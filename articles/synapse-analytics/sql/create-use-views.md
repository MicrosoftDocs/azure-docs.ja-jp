---
title: サーバーレス SQL プールでビューを作成および使用する
description: このセクションでは、ビューを作成および使用して、サーバーレス SQL プール クエリをラップする方法を学習します。 ビューを使用すると、これらのクエリを再利用できます。 ビューは、Power BI などのツールをサーバーレス SQL プールと組み合わせて使用する場合にも必要になります。
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 05/20/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 0948c7c82d7577bae07057bff9d1be4d7e09f978
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96462287"
---
# <a name="create-and-use-views-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Azure Synapse Analytics のサーバーレス SQL プールを使用してビューを作成および使用する

このセクションでは、ビューを作成および使用して、サーバーレス SQL プール クエリをラップする方法を学習します。 ビューを使用すると、これらのクエリを再利用できます。 ビューは、Power BI などのツールをサーバーレス SQL プールと組み合わせて使用する場合にも必要になります。

## <a name="prerequisites"></a>前提条件

最初の手順では、ビューが作成されるデータベースを作成し、そのデータベースで[設定スクリプト](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql)を実行して、Azure Storage での認証に必要なオブジェクトを初期化します。 この記事のクエリはすべて、サンプル データベースで実行されます。

## <a name="create-a-view"></a>ビューの作成

ビューは、通常の SQL Server ビューを作成するのと同じ方法で作成できます。 次のクエリによって、*population.csv* ファイルを読み取るビューが作成されます。

> [!NOTE]
> クエリの最初の行 ([mydbname]) は、自分で作成したデータベースを使用するように変更してください。

```sql
USE [mydbname];
GO

DROP VIEW IF EXISTS populationView;
GO

CREATE VIEW populationView AS
SELECT * 
FROM OPENROWSET(
        BULK 'csv/population/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', 
        FIELDTERMINATOR =',', 
        ROWTERMINATOR = '\n'
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
) AS [r];
```

この例のビューでは、基になるファイルへの絶対パスを使用する `OPENROWSET` 関数が使用されます。 ストレージのルート URL の `EXTERNAL DATA SOURCE` がある場合は、`DATA_SOURCE` と相対ファイル パスと共に `OPENROWSET` を使用できます。

```sql
CREATE VIEW TaxiView
AS SELECT *, nyc.filepath(1) AS [year], nyc.filepath(2) AS [month]
FROM
    OPENROWSET(
        BULK 'parquet/taxi/year=*/month=*/*.parquet',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT='PARQUET'
    ) AS nyc
```

## <a name="use-a-view"></a>ビューの使用

ビューは、SQL Server クエリ内でビューを使用するのと同じ方法でクエリ内で使用できます。

次のクエリは、「[ビューの作成](#create-a-view)」で作成した *population_csv* ビューの使用方法を示しています。 これにより、国/地域名が 2019 年の人口の降順に返されます。

> [!NOTE]
> クエリの最初の行 ([mydbname]) は、自分で作成したデータベースを使用するように変更してください。

```sql
USE [mydbname];
GO

SELECT
    country_name, population
FROM populationView
WHERE
    [year] = 2019
ORDER BY
    [population] DESC;
```

## <a name="next-steps"></a>次のステップ

さまざまな種類のファイルに対するクエリの実行については、[単一の CSV ファイルに対するクエリの実行](query-single-csv-file.md)、[Parquet ファイルに対するクエリの実行](query-parquet-files.md)、および [JSON ファイルに対するクエリの実行](query-json-files.md)に関するページを参照してください。
