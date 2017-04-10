---
title: "SqlPackage: BACPAC ファイルから Azure SQL Database にインポートする | Microsoft Docs"
description: "この記事では、SqlPackage コマンドライン ユーティリティを使用して BACPAC ファイルから SQL Database にインポートする方法について説明します。"
keywords: "Microsoft Azure SQL Database、データベースの移行、データベースのインポート、BACPAC ファイルのインポート、sqlpackage"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 424afa27-5f13-4ec3-98f6-99a511a6a2df
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 02/08/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: e6f0d661465c813ec310b8c69ab1ee06e4f95401
ms.openlocfilehash: 23c5da7b9e171312274aec02262b139ee701deb1


---
# <a name="import-a-database-from-a-bacpac-file-to-azure-sql-database-using-sqlpackage"></a>SqlPackage を使用して BACPAC ファイルから Azure SQL Database にデータベースをインポートする

この記事では、[SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) コマンドライン ユーティリティを使用して [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) ファイルから SQL Database にインポートする方法について説明します。 このユーティリティは、最新バージョンの [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) と [SQL Server Data Tools for Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx) に付属しています。また、Microsoft ダウンロード センターから最新バージョンの [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) を直接ダウンロードすることもできます。

> [!NOTE]
> 以下の手順では、SQL Database サーバーを既にプロビジョニングしてあり、接続情報がわかっていて、移行元データベースに互換性があると検証済みであることを前提にしています。
> 
> 

## <a name="import-the-database"></a>データベースのインポート
[SqlPackage.exe](https://msdn.microsoft.com/library/hh550080.aspx) コマンドライン ユーティリティを使用して、互換性のある SQL Server データベース (または Azure SQL Database) を BACPAC ファイルからインポートします。

> [!NOTE]
> 以下の手順では、Azure SQL Database サーバーを既にプロビジョニングしてあり、接続情報がわかっていることを前提にしています。
>  

コマンド プロンプトを使用して、最新バージョンの sqlpackage.exe コマンドライン ユーティリティを含むディレクトリで、次のサンプル コマンドのようなコマンドを実行します。このコマンドでは、BACPAC ファイルが Azure SQL Database に Premium P11 としてインポートされます。

```
SqlPackage.exe /a:import /tcs:"Data Source=SERVER;Initial Catalog=DBNAME;User Id=USER;Password=PASSWORD" /sf:C:\db.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P11
```   

## <a name="next-steps"></a>次のステップ

* パフォーマンスの推奨事項も含む、SQL Server データベースの移行プロセス全体の詳細については、[Azure SQL Database への SQL Server データベースの移行](sql-database-cloud-migrate.md)に関するページを参照してください。
* SQLPackage のリファレンスは、「[SqlPackage.exe](https://msdn.microsoft.com/library/hh550080.aspx)」を参照してください。
* 最新バージョンの SQLPackage をダウンロードするには、[SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) のダウンロード ページをご覧ください。
* BACPAC ファイルを使用した移行に関する SQL Server Customer Advisory Team のブログについては、「[Migrating from SQL Server to Azure SQL Database using BACPAC Files (BACPAC ファイルを使用した SQL Server から Azure SQL Database への移行)](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/)」を参照してください。




<!--HONumber=Feb17_HO2-->


