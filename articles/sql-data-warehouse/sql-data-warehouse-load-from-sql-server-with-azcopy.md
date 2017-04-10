---
title: "SQL Server から Azure SQL Data Warehouse へのデータの読み込み (PolyBase) | Microsoft Docs"
description: "bcp を使用して SQL Server からフラット ファイルにデータをエクスポートし、AZCopy を使用してデータを Azure Blob Storage にインポートし、PolyBase を使用してデータを Azure SQL Data Warehouse に取り込みます。"
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: barbkess
editor: 
ms.assetid: 4d42786a-fb28-43c9-9c3b-72d19c0ecc11
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 10/31/2016
ms.author: cakarst;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 21b4cc704e271ac220fd606305f8f97c9b2593bb
ms.lasthandoff: 03/22/2017



---
# <a name="load-data-from-sql-server-into-azure-sql-data-warehouse-azcopy"></a>SQL Server から Azure SQL Data Warehouse へのデータの読み込み (AZCopy)
SQL Server から Azure Blob Storage にデータを読み込むには、bcp および AZCopy コマンド ライン ユーティリティを使用します。 その後、データを Azure SQL Data Warehouse に読み込むには、PolyBase または Azure Data Factory を使用します。 

## <a name="prerequisites"></a>前提条件
このチュートリアルを進めるには、次が必要です。

* SQL Data Warehouse データベース
* インストールされた bcp コマンド ライン ユーティリティ
* インストールされた SQLCMD コマンド ライン ユーティリティ

> [!NOTE]
> bcp ユーティリティと sqlcmd ユーティリティは [Microsoft ダウンロード センター][Microsoft Download Center]からダウンロードできます。
> 
> 

## <a name="import-data-into-sql-data-warehouse"></a>SQL Data Warehouse へのデータのインポート
このチュートリアルでは、Azure SQL Data Warehouse でテーブルを作成し、そのデータをテーブルにインポートします。

### <a name="step-1-create-a-table-in-azure-sql-data-warehouse"></a>手順 1: Azure SQL Data Warehouse でテーブルを作成する
コマンド プロンプトで sqlcmd を使用して次のクエリを実行し、インスタンスにテーブルを作成します。

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    CREATE TABLE DimDate2
    (
        DateId INT NOT NULL,
        CalendarQuarter TINYINT NOT NULL,
        FiscalQuarter TINYINT NOT NULL
    )
    WITH
    (
        CLUSTERED COLUMNSTORE INDEX,
        DISTRIBUTION = ROUND_ROBIN
    );
"
```

> [!NOTE]
> SQL Data Warehouse でのテーブルの作成と、WITH 句で使用できるオプションの詳細については、[テーブルの概要][Table Overview]に関する記事または [CREATE TABLE 構文][CREATE TABLE syntax]に関するページを参照してください。
> 
> 

### <a name="step-2-create-a-source-data-file"></a>手順 2: ソース データ ファイルを作成する
メモ帳を開き、データの以下の行を新しいテキスト ファイルにコピーして、このファイルをローカルの一時ディレクトリに保存します (C:\Temp\DimDate2.txt)。

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

> [!NOTE]
> bcp.exe は、UTF-8 のファイルのエンコーディングをサポートしていないことに注意してください。 bcp.exe を使用する場合は、ASCII ファイルまたは UTF-16 エンコード ファイルを使用してください。
> 
> 

### <a name="step-3-connect-and-import-the-data"></a>手順 3: データに接続し、インポートする
bcp を使用して、次のコマンドでデータに接続し、インポートできます。値は適宜置き換えて使用してください。

```sql
bcp DimDate2 in C:\Temp\DimDate2.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t  ','
```

sqlcmd を使用して次のクエリを実行すると、データが読み込まれたかどうかを確認することができます。

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "SELECT * FROM DimDate2 ORDER BY 1;"
```

これにより、次の結果が得られます。

| DateId | CalendarQuarter | FiscalQuarter |
| --- | --- | --- |
| 20150101 |1 |3 |
| 20150201 |1 |3 |
| 20150301 |1 |3 |
| 20150401 |2 |4 |
| 20150501 |2 |4 |
| 20150601 |2 |4 |
| 20150701 |3 |1 |
| 20150801 |3 |1 |
| 20150801 |3 |1 |
| 20151001 |4 |2 |
| 20151101 |4 |2 |
| 20151201 |4 |2 |

### <a name="step-4-create-statistics-on-your-newly-loaded-data"></a>手順 4: 新しくロードしたデータの統計を作成する
Azure SQL Data Warehouse は、統計の自動作成または自動更新をまだサポートしていません。 クエリから最高のパフォーマンスを取得するには、最初の読み込み後またはそれ以降のデータの変更後に、すべてのテーブルのすべての列で統計を作成することが重要です。 統計の詳細については、開発トピック グループの[統計][Statistics]に関するトピックを参照してください。 この例でロードしたテーブルの統計を作成する方法の簡単な例を次に示します

sqlcmd プロンプトから次の CREATE STATISTICS ステートメントを実行します。

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    create statistics [DateId] on [DimDate2] ([DateId]);
    create statistics [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
    create statistics [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
"
```

## <a name="export-data-from-sql-data-warehouse"></a>SQL Data Warehouse からのデータのエクスポート
このチュートリアルでは SQL Data Warehouse 内のテーブルからデータ ファイルを作成します。 上記で作成したデータを DimDate2_export.txt という名前の新しいデータ ファイルにエクスポートします。

### <a name="step-1-export-the-data"></a>手順 1: データをエクスポートする
bcp ユーティリティを使用して、次のコマンドでデータに接続し、エクスポートできます。値は適宜置き換えて使用してください。

```sql
bcp DimDate2 out C:\Temp\DimDate2_export.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t ','
```
新しいファイルを開き、データが正しくエクスポートされたことを確認できます。 ファイル内のデータは、次のテキストと一致する必要があります。

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

> [!NOTE]
> 分散システムの性質上、データの順序は SQL Data Warehouse データベース全体で異なる場合があります。 別のオプションでは、bcp の **queryout** 関数を使用して、テーブル全体をエクスポートするのではなく、抽出するクエリを記述します。
> 
> 

## <a name="next-steps"></a>次のステップ
読み込みの概要については、[SQL Data Warehouse へのデータの読み込み][Load data into SQL Data Warehouse]に関するページを参照してください。
開発に関するその他のヒントについては、[SQL Data Warehouse の開発の概要][SQL Data Warehouse development overview]に関する記事をご覧ください。

<!--Image references-->

<!--Article references-->

[Load data into SQL Data Warehouse]: ./sql-data-warehouse-overview-load.md
[SQL Data Warehouse development overview]: ./sql-data-warehouse-overview-develop.md
[Table Overview]: ./sql-data-warehouse-tables-overview.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md

<!--MSDN references-->
[bcp]: https://msdn.microsoft.com/library/ms162802.aspx
[CREATE TABLE syntax]: https://msdn.microsoft.com/library/mt203953.aspx

<!--Other Web references-->
[Microsoft Download Center]: https://www.microsoft.com/download/details.aspx?id=36433

