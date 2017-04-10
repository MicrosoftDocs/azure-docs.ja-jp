---
title: "CSV ファイルから Azure SQL Database へのデータの読み込み (bcp) | Microsoft Docs"
description: "データ サイズが小さい場合は、bcp を使用して Azure SQL Database にデータをインポートできます。"
services: sql-database
documentationcenter: NA
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 875f9b8d-f1a1-4895-b717-f45570fb7f80
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 01/10/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 36748312506a08ed7932a6bb355a5dc7393bb002
ms.openlocfilehash: 5e15f8a0ebb8cab5dce5b3c1cf6b62dee362a8d0
ms.lasthandoff: 01/28/2017


---
# <a name="load-data-from-csv-into-azure-sql-database-flat-files"></a>CSV から Azure SQL Database へのデータの読み込み (フラット ファイル)
bcp コマンドライン ユーティリティを使用して、CSV ファイルから Azure SQL Database にデータをインポートできます。

## <a name="before-you-begin"></a>開始する前に
### <a name="prerequisites"></a>前提条件
このチュートリアルを進めるには、次が必要です。

* Azure SQL Database の論理サーバーとデータベース
* インストールされた bcp コマンド ライン ユーティリティ
* インストールされた sqlcmd コマンド ライン ユーティリティ

bcp ユーティリティと sqlcmd ユーティリティは [Microsoft ダウンロード センター][Microsoft Download Center]からダウンロードできます。

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


## <a name="2-create-a-source-data-file"></a>手順&2;.ソース データ ファイルを作成する
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

```sql
bcp <TableName> out C:\Temp\DimDate2_export.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <Password> -q -c -t ','
```

## <a name="3-load-the-data"></a>3.データを読み込む
データを読み込むには、コマンド プロンプトを開き、次のコマンドを実行します。ここでは、ServerName、DatabaseName、Username、および Password を自身の情報に置き換えます。

```sql
bcp DimDate2 in C:\Temp\DimDate2.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <password> -q -c -t  ','
```

次のコマンドを使用して、データが正しく読み込まれたことを確認します。

```sql
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

## <a name="next-steps"></a>次のステップ
SQL Server データベースを移行するには、 [SQL Server データベースの移行](sql-database-cloud-migrate.md)に関するページを参照してください。

<!--MSDN references-->
[bcp]: https://msdn.microsoft.com/library/ms162802.aspx
[CREATE TABLE syntax]: https://msdn.microsoft.com/library/mt203953.aspx

<!--Other Web references-->
[Microsoft Download Center]: https://www.microsoft.com/download/details.aspx?id=36433

