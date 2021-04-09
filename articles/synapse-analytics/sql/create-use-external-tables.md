---
title: サーバーレス SQL プールで外部テーブルを作成および使用する
description: このセクションでは、サーバーレス SQL プールで外部テーブルを作成および使用する方法を学習します。
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: 5dcd4b7b76752ca5396fc68afc8d4c8e4e1edca5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96462302"
---
# <a name="create-and-use-external-tables-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Azure Synapse Analytics のサーバーレス SQL プールを使用して外部テーブルを作成および使用する

このセクションでは、サーバーレス SQL プールで[外部テーブル](develop-tables-external-tables.md)を作成および使用する方法を学習します。 外部テーブルは、サーバーレス SQL プールで外部データへのアクセスを制御する場合や、Power BI などのツールをサーバーレス SQL プールと組み合わせて使用する場合に便利です。 外部テーブルは、次の 2 種類のストレージにアクセスできます。
- パブリック ストレージ。ユーザーは、パブリック ストレージ ファイルにアクセスします。
- 保護されたストレージ。ユーザーは、Synapse ワークスペースの SAS 資格情報、Azure AD ID、またはマネージド ID を使用して、ストレージ ファイルにアクセスします。

## <a name="prerequisites"></a>前提条件

最初の手順として、テーブルが作成されるデータベースを作成します。 次に、そのデータベースで[セットアップ スクリプト](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql)を実行して、オブジェクトを初期化します。 このセットアップ スクリプトによって、このサンプルで使用する次のオブジェクトが作成されます。
- データベース スコープ資格情報 `sqlondemand`。SAS で保護された `https://sqlondemandstorage.blob.core.windows.net` Azure ストレージ アカウントへのアクセスを有効にします。

    ```sql
    CREATE DATABASE SCOPED CREDENTIAL [sqlondemand]
    WITH IDENTITY='SHARED ACCESS SIGNATURE',  
    SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D'
    ```

- 外部データ ソース `sqlondemanddemo`。SAS キーで保護されたデモ ストレージ アカウントを参照し、外部データ ソース `YellowTaxi`。`https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/` という場所で一般公開されている Azure ストレージ アカウントを参照します。

    ```sql
    CREATE EXTERNAL DATA SOURCE SqlOnDemandDemo WITH (
        LOCATION = 'https://sqlondemandstorage.blob.core.windows.net',
        CREDENTIAL = sqlondemand
    );
    GO
    CREATE EXTERNAL DATA SOURCE YellowTaxi
    WITH ( LOCATION = 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/')
    ```

- ファイル形式 `QuotedCSVWithHeaderFormat` および `ParquetFormat`。CSV ファイルおよび parquet ファイルの種類を記述します。

    ```sql
    CREATE EXTERNAL FILE FORMAT QuotedCsvWithHeaderFormat
    WITH (  
        FORMAT_TYPE = DELIMITEDTEXT,
        FORMAT_OPTIONS ( FIELD_TERMINATOR = ',', STRING_DELIMITER = '"', FIRST_ROW = 2   )
    );
    GO
    CREATE EXTERNAL FILE FORMAT ParquetFormat WITH (  FORMAT_TYPE = PARQUET );
    ```

この記事のクエリは、サンプル データベースで実行され、これらのオブジェクトを使用します。 

## <a name="create-an-external-table-on-protected-data"></a>保護されたデータに外部テーブルを作成する

特定の Azure AD ID または SAS キーを持つユーザーにアクセス可能な Azure ストレージ アカウントのデータにアクセスする外部テーブルを作成できます。 外部テーブルは、通常の SQL Server 外部テーブルを作成するのと同じ方法で作成できます。 

次のクエリでは、`sqlondemanddemo` データ ソースを使用して参照され、`sqlondemand` と呼ばれるデータベース スコープの資格情報で保護される、SynapseSQL のデモ Azure ストレージ アカウントから *population.csv* ファイルを読み取る外部テーブルを作成します。 

データ ソースとデータベース スコープ資格情報は、[セットアップ スクリプト](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql)で作成されます。

> [!NOTE]
> クエリの最初の行 ([mydbname]) は、自分で作成したデータベースを使用するように変更してください。 

```sql
USE [mydbname];
GO
CREATE EXTERNAL TABLE populationExternalTable
(
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
)
WITH (
    LOCATION = 'csv/population/population.csv',
    DATA_SOURCE = sqlondemanddemo,
    FILE_FORMAT = QuotedCSVWithHeaderFormat
);
```

## <a name="create-an-external-table-on-public-data"></a>パブリック データに外部テーブルを作成する

一般公開されている Azure ストレージに配置されたファイルからデータを読み取る外部テーブルを作成できます。 この[セットアップ スクリプト](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql)によって、次のクエリで使用されるパブリック外部データ ソースと Parquet ファイル形式の定義が作成されます。

```sql
CREATE EXTERNAL TABLE Taxi (
     vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
     pickup_datetime DATETIME2, 
     dropoff_datetime DATETIME2,
     passenger_count INT,
     trip_distance FLOAT,
     fare_amount FLOAT,
     tip_amount FLOAT,
     tolls_amount FLOAT,
     total_amount FLOAT
) WITH (
         LOCATION = 'puYear=*/puMonth=*/*.parquet',
         DATA_SOURCE = YellowTaxi,
         FILE_FORMAT = ParquetFormat
);
```
## <a name="use-an-external-table"></a>外部テーブルを使用する

[外部テーブル](develop-tables-external-tables.md)は、SQL Server クエリ内で外部テーブルを使用するのと同じ方法で、クエリ内で使用できます。

次のクエリでは、前のセクションで作成した *population* 外部テーブルの使用について説明しています。 これにより、国/地域名が 2019 年の人口の降順に返されます。

> [!NOTE]
> クエリの最初の行 ([mydbname]) は、自分で作成したデータベースを使用するように変更してください。

```sql
USE [mydbname];
GO

SELECT
    country_name, population
FROM populationExternalTable
WHERE
    [year] = 2019
ORDER BY
    [population] DESC;
```

## <a name="next-steps"></a>次のステップ

クエリの結果をストレージに格納する方法については、[クエリ結果をストレージに格納する方法](../sql/create-external-table-as-select.md)に関する記事を参照してください。
