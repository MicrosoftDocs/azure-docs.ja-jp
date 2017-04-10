---
title: "bcp を使用した SQL Data Warehouse へのデータの読み込み | Microsoft Docs"
description: "データ ウェアハウジングのシナリオに沿って、bcp の概要と、その使用方法を学習します。"
services: sql-data-warehouse
documentationcenter: NA
author: twounder
manager: barbkess
editor: 
ms.assetid: f9467d11-fcd6-4131-a65a-2022d2c32d24
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 10/31/2016
ms.author: mausher;barbkess
translationtype: Human Translation
ms.sourcegitcommit: c0e2324a2b2e6294df6e502f2e7a0ae36ff94158
ms.openlocfilehash: e368ae8b249fe3c33371794160440e472b0f35e3
ms.lasthandoff: 01/30/2017



---
# <a name="load-data-with-bcp"></a>bcp を使用したデータの読み込み
> [!div class="op_single_selector"]
> * [Redgate](sql-data-warehouse-load-with-redgate.md)  
> * [Data Factory](sql-data-warehouse-get-started-load-with-azure-data-factory.md)  
> * [PolyBase](sql-data-warehouse-get-started-load-with-polybase.md)  
> * [BCP](sql-data-warehouse-load-with-bcp.md)
> 
> 

**[bcp][bcp]** はコマンド ライン形式の一括読み込みユーティリティの一種であり、これを使用して SQL Server、データ ファイル、および SQL Data Warehouse の間でデータをコピーできます。 bcp を使用して SQL Data Warehouse のテーブルに多数の行をインポートしたり、SQL Server のテーブルからデータ ファイルにデータをエクスポートしたりします。 queryout オプションと併用する場合を除き、bcp を使用するときに Transact-SQL の知識は必要ありません。

bcp を使用すれば、すばやく、簡単に SQL Data Warehouse データベースとの間で小規模なデータ セットを読み込み/抽出できます。 bcp を使用して読み込み/抽出する場合に推奨される正確なデータ量は、Azure データ センターへのネットワーク接続によって異なります。  一般に、ディメンション テーブルは bcp で簡単に読み込んで抽出できますが、bcp は大量のデータの読み込みや抽出にはお勧めできません。  大量のデータの読み込みと抽出に適したツールは、Polybase です。SQL Data Warehouse の大量並列処理アーキテクチャをうまく活用できます。

bcp では次のことができます。

* 簡単なコマンド ライン ユーティリティを使用して、SQL Data Warehouse にデータを読み込みます。
* 簡単なコマンド ライン ユーティリティを使用して、SQL Data Warehouse からデータを抽出します。

ここでは、次の操作方法について説明します。

* bcp in コマンドを使用してテーブルにデータをインポートする
* bcp out コマンドを使用してテーブルからデータをエクスポートする

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Loading-data-into-Azure-SQL-Data-Warehouse-with-BCP/player]
> 
> 

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

