---
title: クエリでファイルのメタデータを使用する
description: OPENROWSET 関数では、ファイル名やフォルダー パスに基づいてデータのフィルター処理または分析を行えるように、クエリで使用されるすべてのファイルについてファイルおよびパスの情報が提供されます。
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: d24ae1f42c685589309506b2d5e0eab157b2bc42
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96299617"
---
# <a name="use-file-metadata-in-serverless-sql-pool-queries"></a>サーバーレス SQL プール クエリでファイルのメタデータを使用する

「[クエリ フォルダーと複数のファイル](query-folders-multiple-csv-files.md)」の記事で説明されているように、サーバーレス SQL プールは、複数のファイルとフォルダーに対応できます。 この記事では、クエリ内でファイル名とフォルダー名に関するメタデータ情報を使用する方法について説明します。

場合によっては、結果セット内の特定の行に関連するファイル ソースまたはフォルダー ソースを把握しておく必要があります。

関数 `filepath` および `filename` を使用すれば、結果セット内にファイル名およびそのパスを返すことができます。 また、それらを使用することで、ファイル名やフォルダー パスに基づいてデータをフィルター処理することもできます。 これらの関数については、「[filename 関数](query-data-storage.md#filename-function)」と「[filepath 関数](query-data-storage.md#filepath-function)」の構文セクションで説明されています。 以降のセクションでは、サンプルに沿って簡単な説明があります。

## <a name="prerequisites"></a>前提条件

最初の手順は、参照するストレージ アカウントを使用して **データベースを作成** することです。 次に、そのデータベースに対して[セットアップ スクリプト](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql)を実行して、オブジェクトを初期化します。 このセットアップ スクリプトにより、これらのサンプルで使用されるデータ ソース、データベース スコープの資格情報、および外部ファイル形式が作成されます。

## <a name="functions"></a>関数

### <a name="filename"></a>ファイル名

この関数からは、行の生成元のファイル名が返されます。

次のサンプルでは、2017 年の過去 3 か月間について NYC イエロー タクシーのデータ ファイルが読み取られ、ファイルごとの乗車数が返されます。 クエリの OPENROWSET 部分では、読み取るファイルを指定します。

```sql
SELECT
    nyc.filename() AS [filename]
    ,COUNT_BIG(*) AS [rows]
FROM  
    OPENROWSET(
        BULK 'parquet/taxi/year=2017/month=9/*.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) nyc
GROUP BY nyc.filename();
```

次の例では、WHERE 句内に *filename ()* を使用して、読み取るファイルをフィルター処理する方法を示します。 クエリの OPENROWSET 部分のフォルダー全体にアクセスし、WHERE 句内のファイルをフィルター処理します。

結果は前の例と同じになります。

```sql
SELECT
    r.filename() AS [filename]
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
    BULK 'csv/taxi/yellow_tripdata_2017-*.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV',
        PARSER_VERSION = '2.0',
        FIRSTROW = 2) 
        WITH (C1 varchar(200) ) AS [r]
WHERE
    r.filename() IN ('yellow_tripdata_2017-10.csv', 'yellow_tripdata_2017-11.csv', 'yellow_tripdata_2017-12.csv')
GROUP BY
    r.filename()
ORDER BY
    [filename];
```

### <a name="filepath"></a>Filepath

filepath 関数からは、完全なパスまたは部分的なパスが返されます。

- パラメーターを指定せずに呼び出した場合、行の生成元である完全なファイル パスが返されます。 OPENROWSET で DATA_SOURCE を使用すると、DATA_SOURCE を基準とした相対パスが返されます。 
- パラメーターを指定して呼び出すと、パラメーターで指定した位置にあるワイルドカードと一致するパスの一部が返されます。 たとえば、パラメーター値 1 の場合は、最初のワイルドカードと一致するパスの一部が返されます。

次のサンプルでは、2017 年の過去 3 か月間について、NYC イエロー タクシーのデータ ファイルが読み取られます。 ファイル パスごとの乗車数が返されます。 クエリの OPENROWSET 部分では、読み取るファイルを指定します。

```sql
SELECT
    r.filepath() AS filepath
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
        BULK 'csv/taxi/yellow_tripdata_2017-1*.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV',
        PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        vendor_id INT
    ) AS [r]
GROUP BY
    r.filepath()
ORDER BY
    filepath;
```

次の例では、WHERE 句内に *filepath()* を使用して、読み取るファイルをフィルター処理する方法を示します。

クエリの OPENROWSET 部分にワイルドカードを使用し、WHERE 句内のファイルをフィルター処理することができます。 結果は前の例と同じになります。

```sql
SELECT
    r.filepath() AS filepath
    ,r.filepath(1) AS [year]
    ,r.filepath(2) AS [month]
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
        BULK 'csv/taxi/yellow_tripdata_*-*.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV',
        PARSER_VERSION = '2.0',        
        FIRSTROW = 2
    )
WITH (
    vendor_id INT
) AS [r]
WHERE
    r.filepath(1) IN ('2017')
    AND r.filepath(2) IN ('10', '11', '12')
GROUP BY
    r.filepath()
    ,r.filepath(1)
    ,r.filepath(2)
ORDER BY
    filepath;
```

## <a name="next-steps"></a>次のステップ

次の記事では、[Parquet ファイルに対してクエリを実行](query-parquet-files.md)する方法について説明します。
