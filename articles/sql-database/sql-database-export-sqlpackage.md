---
title: "SqlPackage: SQL Server データベースを BACPAC ファイルにエクスポートする (Azure) | Microsoft Docs"
description: "この記事では、SqlPackage コマンドライン ユーティリティを使用して SQL Server データベースを BACPAC ファイルにエクスポートする方法について説明します。"
keywords: "Microsoft Azure SQL Database、データベースの移行、データベースのエクスポート、BACPAC ファイルのエクスポート、sqlpackage"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 7b9541c5-5590-4c70-ad36-73007389f6dc
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 02/07/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 3d04be3d2427bc59d24bfaad227730991b61265b
ms.openlocfilehash: a17bfa6868ac581dc5ae562b3174f28571d52698
ms.lasthandoff: 02/11/2017


---
# <a name="export-an-azure-sql-database-or-a-sql-server-database-to-a-bacpac-file-using-sqlpackage"></a>SqlPackage を使用して Azure SQL Database または SQL Server データベースを BACPAC ファイルにエクスポートする

この記事では、[SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) コマンドライン ユーティリティを使用して Azure SQL Database または SQL Server データベースを BACPAC ファイルにエクスポートする方法について説明します。 このユーティリティは、最新バージョンの [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) と [SQL Server Data Tools for Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx) に付属しています。また、Microsoft ダウンロード センターから最新バージョンの [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) を直接ダウンロードすることもできます。

BACPAC ファイルへのエクスポートの概要については、[BACPAC へのエクスポート](sql-database-export.md)に関する記事をご覧ください。

> [!NOTE]
> また、[Azure Portal](sql-database-export-portal.md)、[SQL Server Management Studio](sql-database-export-ssms.md) または [PowerShell](sql-database-export-powershell.md) を使用して、Azure SQL データベース フィルを BACPAC ファイルにエクスポートすることもできます。
>

## <a name="sqlpackage-utility"></a>SQLPackage ユーティリティ

1. コマンド プロンプトを開き、sqlpackage.exe コマンドライン ユーティリティのあるディレクトリに移動します。このユーティリティは、Visual Studio と SQL Server の両方に付属します。 コンピュータの検索機能を使用してご利用の環境のパスを検索します。
2. 次の sqlpackage.exe コマンドをご利用の環境に合わせた引数で実行します。
   
```    sqlpackage.exe /Action:Export /ssn:< server_name > /sdn:< database_name > /tf:< target_file >
```
   
   | Argument | Description |
   | --- | --- |
   | < server_name > |source server name |
   | < database_name > |source database name |
   | < target_file > |file name and location for BACPAC file |
   
   ![Export a data-tier application from the Tasks menu](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01b.png)

## Next steps

* To learn about importing a BACPAC using SQLPackage, see [Import a BACPAC to Azure SQL Database using SqlPackage](sql-database-import-sqlpackage.md)
* To learn about importing a BACPAC using the Azure portal, see [Import a BACPAC to Azure SQL Database using the Azure portal](sql-database-import-portal.md)
* To learn about importing a BACPAC using PowerShell, see [Import a BACPAC to Azure SQL Database using PowerShell](scripts/sql-database-import-from-bacpac-powershell.md)
* For a discussion of the entire SQL Server database migration process, including performance recommendations, see [Migrate a SQL Server database to Azure SQL Database](sql-database-cloud-migrate.md).
* To learn about long-term backup retention of an Azure SQL database backup as an alternative to exported a database for archive purposes, see [Long term backup retention](sql-database-long-term-retention.md)
* To learn about importing a BACPAC to a SQL Server database, see [Import a BACPCAC to a SQL Server database](https://msdn.microsoft.com/library/hh710052.aspx)



