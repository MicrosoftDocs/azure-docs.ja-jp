---
title: CSV ファイルからデータベースへのデータの読み込み (bcp)
description: データ サイズが小さい場合は、bcp を使用して Azure SQL Database にデータをインポートできます。
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 01/25/2019
ms.openlocfilehash: b0df3d588f1d9b0a50c3ea7a583b0704e7e85c39
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827484"
---
# <a name="load-data-from-csv-into-azure-sql-database-flat-files"></a>CSV から Azure SQL Database へのデータの読み込み (フラット ファイル)

bcp コマンドライン ユーティリティを使用して、CSV ファイルから Azure SQL Database にデータをインポートできます。

## <a name="before-you-begin"></a>開始する前に

### <a name="prerequisites"></a>前提条件

この記事の手順を完了するには、次のものが必要です。

* Azure SQL Database のサーバーとデータベース
* インストールされた bcp コマンド ライン ユーティリティ
* インストールされた sqlcmd コマンド ライン ユーティリティ

bcp および sqlcmd ユーティリティは [Microsoft ダウンロード センター][Microsoft Download Center]からダウンロードできます。

### <a name="data-in-ascii-or-utf-16-format"></a>ASCII または UTF-16 形式のデータ

自身のデータを使ってこのチュートリアルを試す場合、bcp では UTF-8 がサポートされないため、データには ASCII または UTF-16 エンコードを使用する必要があります。 

## <a name="1-create-a-destination-table"></a>1.ターゲット テーブルを作成する

SQL Database 内でターゲット テーブルとなるテーブルを定義します。 テーブル内の各列は、データ ファイルの各行のデータに対応する必要があります。

テーブルを作成するには、コマンド プロンプトを開き、sqlcmd.exe を使用して次のコマンドを実行します。

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    CREATE TABLE DimDate2
    (
        DateId INT NOT NULL,
        CalendarQuarter TINYINT NOT NULL,
        FiscalQuarter TINYINT NOT NULL
    )
    ;
"
```


## <a name="2-create-a-source-data-file"></a>2.ソース データ ファイルを作成する

メモ帳を開き、データの以下の行を新しいテキスト ファイルにコピーして、このファイルをローカルの一時ディレクトリに保存します (C:\Temp\DimDate2.txt)。 このデータは ASCII 形式です。

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

(オプション) 自身のデータを SQL Server データベースからエクスポートするには、コマンド プロンプトを開き、次のコマンドを実行します。 TableName、ServerName、DatabaseName、Username、および Password を自身の情報に置き換えてください。

```bcp
bcp <TableName> out C:\Temp\DimDate2_export.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <Password> -q -c -t , 
```

## <a name="3-load-the-data"></a>3.データを読み込む

データを読み込むには、コマンド プロンプトを開き、次のコマンドを実行します。ここでは、ServerName、DatabaseName、Username、および Password を自身の情報に置き換えます。

```bcp
bcp DimDate2 in C:\Temp\DimDate2.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <password> -q -c -t  ,
```

次のコマンドを使用して、データが正しく読み込まれたことを確認します。

```bcp
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "SELECT * FROM DimDate2 ORDER BY 1;"
```

結果は次のようになります。

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

## <a name="next-steps"></a>次の手順

SQL Server データベースを移行するには、 [SQL Server データベースの移行](sql-database-single-database-migrate.md)に関するページを参照してください。

<!--MSDN references-->
[bcp]: https://msdn.microsoft.com/library/ms162802.aspx
[CREATE TABLE syntax]: https://msdn.microsoft.com/library/mt203953.aspx

<!--Other Web references-->
[Microsoft Download Center]: https://www.microsoft.com/download/details.aspx?id=36433
