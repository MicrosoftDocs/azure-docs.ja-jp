---
title: サーバーレス SQL プールを使用して、フォルダーと複数のファイルに対してクエリを実行する
description: サーバーレス SQL プールでは、Windows OS で使用されるワイルドカードとよく似たワイルドカードを使用して複数のファイルまたはフォルダーを読み取ることができます。
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 83c4d88e1a87f6b546e26dd55da338a36f16ebe4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96462629"
---
# <a name="query-folders-and-multiple-files"></a>クエリ フォルダーと複数のファイル  

この記事では、Azure Synapse Analytics のサーバーレス SQL プールを使用してクエリを作成する方法について説明します。

サーバーレス SQL プールでは、Windows OS で使用されるワイルドカードとよく似たワイルドカードを使用して複数のファイルまたはフォルダーを読み取ることができます。 しかし、複数のワイルドカードを使用できるため、柔軟性がより高くなります。

## <a name="prerequisites"></a>前提条件

最初の手順として、クエリを実行する **データベースを作成** します。 次に、そのデータベースで[セットアップ スクリプト](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql)を実行して、オブジェクトを初期化します。 このセットアップ スクリプトにより、この記事のサンプルで使用されるデータ ソース、データベース スコープの資格情報、および外部ファイル形式が作成されます。

フォルダー *csv/taxi* を使用してサンプル クエリを実行します。 これには、2016 年 7 月から 2018 年 6 月までの NYC のタクシーのデータ (イエロー タクシーの運行記録) が含まれています。 *csv/taxi* のファイルには、次のパターンを使用して、年月に従って名前が付けられます: yellow_tripdata_<year>-<month>.csv

## <a name="read-all-files-in-folder"></a>フォルダー内のすべてのファイルを読み取る

次の例では、*csv/taxi* フォルダーから NYC イエロー タクシーのすべてのデータ ファイルを読み取り、年間の合計乗客数および乗車回数を返します。 また、集計関数の使用方法も示します。

```sql
SELECT 
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
        BULK 'csv/taxi/*.csv',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        pickup_datetime DATETIME2 2, 
        passenger_count INT 4
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> 1 回の OPENROWSET でアクセスされるファイルはすべて同じ構造である (つまり、列数とデータ型が同じである) 必要があります。

### <a name="read-subset-of-files-in-folder"></a>フォルダー内のファイルのサブセットを読み取る

次の例では、ワイルドカードを使用して、*csv/taxi* フォルダーから NYC イエロー タクシーの 2017 年のデータ ファイルを読み取り、支払いの種類ごとの合計料金を返します。

```sql
SELECT 
    payment_type,  
    SUM(fare_amount) AS fare_total
FROM OPENROWSET(
        BULK 'csv/taxi/yellow_tripdata_2017-*.csv',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        payment_type INT 10,
        fare_amount FLOAT 11
    ) AS nyc
GROUP BY payment_type
ORDER BY payment_type;
```

> [!NOTE]
> 1 回の OPENROWSET でアクセスされるファイルはすべて同じ構造である (つまり、列数とデータ型が同じである) 必要があります。

## <a name="read-folders"></a>フォルダーを読み取る

OPENROWSET には、フォルダーへのパスを指定することもできます。 以下のセクションでは、このクエリの種類について説明します。

### <a name="read-all-files-from-specific-folder"></a>特定のフォルダーからすべてのファイルを読み取る

「[フォルダー内のすべてのファイルを読み取る](#read-all-files-in-folder)」で示したように、ファイル レベルのワイルドカードを使用して、フォルダー内のすべてのファイルを読み取ることができます。 しかし、フォルダーに対してクエリを実行し、そのフォルダー内のすべてのファイルを使用する方法があります。

OPENROWSET で指定するパスがフォルダーを指す場合、そのフォルダー内のすべてのファイルが、クエリのソースとして使用されます。 次のクエリは、*csv/taxi* フォルダー内のすべてのファイルを読み取ります。

> [!NOTE]
> 次のクエリで、パスの最後に / があることに注意してください。 これはフォルダーを表します。 / を省略すると、クエリは *taxi* という名前のファイルに対して実行されます。

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
        BULK 'csv/taxi/',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> 1 回の OPENROWSET でアクセスされるファイルはすべて同じ構造である (つまり、列数とデータ型が同じである) 必要があります。

### <a name="read-all-files-from-multiple-folders"></a>複数のフォルダーからすべてのファイルを読み取る

ワイルドカードを使用して複数のフォルダーからファイルを読み取ることができます。 次のクエリでは、*csv* フォルダー内にある、*t* で始まり、*i* で終わる名前を持つすべてのフォルダーからすべてのファイルを読み取ります。

> [!NOTE]
> 次のクエリで、パスの最後に / があることに注意してください。 これはフォルダーを表します。 / を省略すると、クエリは *t&ast;i* という名前のファイルに対して実行されます。

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
        BULK 'csv/t*i/', 
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> 1 回の OPENROWSET でアクセスされるファイルはすべて同じ構造である (つまり、列数とデータ型が同じである) 必要があります。

条件に一致するフォルダーは 1 つだけであるため、クエリの結果は、「[フォルダー内のすべてのファイルを読み取る](#read-all-files-in-folder)」場合と同じです。

## <a name="traverse-folders-recursively"></a>フォルダーを再帰的にスキャンする

パスの末尾に /** を指定すると、サーバーレス SQL プールでフォルダーを再帰的にスキャンできます。 次のクエリでは、*csv* フォルダー内にあるすべてのフォルダーとサブフォルダーからすべてのファイルが読み取られます。

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
        BULK 'csv/taxi/**', 
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> 1 回の OPENROWSET でアクセスされるファイルはすべて同じ構造である (つまり、列数とデータ型が同じである) 必要があります。

## <a name="multiple-wildcards"></a>複数のワイルドカード

複数のワイルドカードを異なるパス レベルで使用できます。 たとえば、前述のクエリを強化して、名前が *t* で始まり、*i* で終わるすべてのフォルダーから、2017 年のデータを含むファイルのみを読み取るようにすることができます。

> [!NOTE]
> 次のクエリで、パスの最後に / があることに注意してください。 これはフォルダーを表します。 / を省略すると、クエリは *t&ast;i* という名前のファイルに対して実行されます。
> クエリあたりのワイルドカードの上限数は 10 個です。

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
        BULK 'csv/t*i/yellow_tripdata_2017-*.csv',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> 1 回の OPENROWSET でアクセスされるファイルはすべて同じ構造である (つまり、列数とデータ型が同じである) 必要があります。

条件に一致するフォルダーは 1 つだけであるため、クエリの結果は、「[フォルダー内のファイルのサブセットを読み取る](#read-subset-of-files-in-folder)」場合および「[特定のフォルダーからすべてのファイルを読み取る](#read-all-files-from-specific-folder)」場合と同じです。 より複雑なワイルドカードの使用シナリオについては、[Parquet ファイルに対するクエリ](query-parquet-files.md)に関するページをご覧ください。

## <a name="next-steps"></a>次のステップ

詳細については、[特定のファイルに対するクエリ](query-specific-files.md)に関する記事を参照してください。
