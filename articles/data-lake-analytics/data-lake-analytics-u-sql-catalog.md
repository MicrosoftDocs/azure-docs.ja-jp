---
title: Azure Data Lake Analytics で U-SQL カタログを使用する
description: U-SQL カタログを使用して、コードとデータを共有する方法について説明します。 テーブル値関数を作成し、ビューを作成し、テーブルを作成し、それらにクエリを実行します。
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 05/09/2017
ms.openlocfilehash: f92aadc8ccf18dd91b5dd4b35285f60b174e4cf7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92220076"
---
# <a name="get-started-with-the-u-sql-catalog-in-azure-data-lake-analytics"></a>Azure Data Lake Analytics の U-SQL カタログを使用する

## <a name="create-a-tvf"></a>TVF の作成

前述の U-SQL スクリプトでは、同じソース ファイルからの読み取りで EXTRACT の使用を繰り返しました。 U-SQL のテーブル値関数 (TVF) では、後で再利用するためにデータをカプセル化することができます。  

次のスクリプトでは、既定のデータベースとスキーマで `Searchlog()` という TVF を作成します。

```usql
DROP FUNCTION IF EXISTS Searchlog;

CREATE FUNCTION Searchlog()
RETURNS @searchlog TABLE
(
            UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
)
AS BEGIN
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM "/Samples/Data/SearchLog.tsv"
USING Extractors.Tsv();
RETURN;
END;
```

次のスクリプトは、前述のスクリプトで定義された TVF を使用する方法を示しています。

```usql
@res =
    SELECT
        Region,
        SUM(Duration) AS TotalDuration
    FROM Searchlog() AS S
GROUP BY Region
HAVING SUM(Duration) > 200;

OUTPUT @res
    TO "/output/SearchLog-use-tvf.csv"
    ORDER BY TotalDuration DESC
    USING Outputters.Csv();
```

## <a name="create-views"></a>ビューの作成

単一のクエリ式がある場合は、TVF ではなく、U-SQL ビューを使用してその式をカプセル化できます。

次のスクリプトでは、既定のデータベースとスキーマで `SearchlogView` というビューを作成します。

```usql
DROP VIEW IF EXISTS SearchlogView;

CREATE VIEW SearchlogView AS  
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM "/Samples/Data/SearchLog.tsv"
USING Extractors.Tsv();
```

次のスクリプトは定義されたビューの使用方法を示しています。

```usql
@res =
    SELECT
        Region,
        SUM(Duration) AS TotalDuration
    FROM SearchlogView
GROUP BY Region
HAVING SUM(Duration) > 200;

OUTPUT @res
    TO "/output/Searchlog-use-view.csv"
    ORDER BY TotalDuration DESC
    USING Outputters.Csv();
```

## <a name="create-tables"></a>テーブルを作成する
リレーショナル データベースのテーブルと同様に、U-SQL では、定義済みのスキーマでテーブルを作成したり、テーブルを作成して、そのテーブルを設定するクエリからスキーマを推測したりすることができます (CREATE TABLE AS SELECT または CTAS ともいう)。

次のスクリプトを使用して、1 つのデータベースと 2 つのテーブルを作成します。

```usql
DROP DATABASE IF EXISTS SearchLogDb;
CREATE DATABASE SearchLogDb;
USE DATABASE SearchLogDb;

DROP TABLE IF EXISTS SearchLog1;
DROP TABLE IF EXISTS SearchLog2;

CREATE TABLE SearchLog1 (
            UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string,

            INDEX sl_idx CLUSTERED (UserId ASC)
                DISTRIBUTED BY HASH (UserId)
);

INSERT INTO SearchLog1 SELECT * FROM master.dbo.Searchlog() AS s;

CREATE TABLE SearchLog2(
    INDEX sl_idx CLUSTERED (UserId ASC)
            DISTRIBUTED BY HASH (UserId)
) AS SELECT * FROM master.dbo.Searchlog() AS S; // You can use EXTRACT or SELECT here
```

## <a name="query-tables"></a>テーブルの照会
データ ファイルの照会と同じ方法で、前述のスクリプトで作成されたテーブルのようなテーブルを照会できます。 EXTRACT を使用して行セットを作成する代わりに、テーブル名を参照できるようになりました。

テーブルから読み取る場合、前に使用した変換スクリプトは以下のように変更されます。

```usql
@rs1 =
    SELECT
        Region,
        SUM(Duration) AS TotalDuration
    FROM SearchLogDb.dbo.SearchLog2
GROUP BY Region;

@res =
    SELECT *
    FROM @rs1
    ORDER BY TotalDuration DESC
    FETCH 5 ROWS;

OUTPUT @res
    TO "/output/Searchlog-query-table.csv"
    ORDER BY TotalDuration DESC
    USING Outputters.Csv();
```

 >[!NOTE]
 >現時点では、テーブルを作成する場合と同じスクリプトで、テーブルに対して SELECT を実行することはできません。

## <a name="next-steps"></a>次の手順
* [Microsoft Azure Data Lake Analytics の概要](data-lake-analytics-overview.md)
* [Data Lake Tools for Visual Studio を使用して U-SQL スクリプトを開発する](data-lake-analytics-data-lake-tools-get-started.md)
* [Azure Portal を使用して Azure Data Lake Analytics ジョブの監視とトラブルシューティングを行う](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
