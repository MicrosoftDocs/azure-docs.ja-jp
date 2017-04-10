---
title: "T-SQL: 単一の Azure SQL データベースの作成と管理 | Microsoft Docs"
description: "Azure Portal を使用して単一の Azure SQL データベースを作成および管理する方法のクイック リファレンス"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: single databases
ms.devlang: NA
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
ms.date: 02/06/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 951c88124219a6962f655b91275aec64c716b1cd
ms.lasthandoff: 03/28/2017


---
# <a name="create-and-manage-single-azure-sql-databases-with-transact-sql"></a>Transact-SQL を使用した単一の Azure SQL データベースの作成と管理

[Azure Portal](https://portal.azure.com/)、PowerShell、Transact-SQL、REST API、または C# を使用して、単一の Azure SQL データベースを作成および管理できます。 このトピックでは、Azure Portal を使用した場合の方法について説明します。 PowerShell を使用した場合の方法については、[Powershell を使用した単一のデータベースの作成と管理](scripts/sql-database-create-and-configure-database-powershell.md)に関するページを参照してください。 Transact-SQL を使用した場合の方法については、[Transact-SQL を使用した単一のデータベースの作成と管理](sql-database-manage-single-databases-tsql.md)に関するページを参照してください。 

## <a name="create-an-azure-sql-database-using-transact-sql-in-sql-server-management-studio"></a>SQL Server Management Studio で Transact-SQL を使用して Azure SQL データベースを作成する

SQL Server Management Studio で Transact-SQL を使用して SQL Database を作成するには:

1. SQL Server Management Studio で、サーバー レベルのプリンシパル ログインまたは **dbmanager** ロールのメンバーのログインを使用して、Azure Database サーバーに接続します。 詳細については、[ログインの管理](sql-database-manage-logins.md)に関するページをご覧ください。
2. オブジェクト エクスプローラーで [データベース] ノードを開き、**[システム データベース]** フォルダーを展開し、**[master]** を右クリックして、**[新しいクエリ]** をクリックします。
3. データベースを作成するには、 **CREATE DATABASE** ステートメントを使用します。 詳細については、「 [CREATE DATABASE (Azure SQL データベース)](https://msdn.microsoft.com/library/dn268335.aspx)」を参照してください。 次のステートメントは、 **myTestDB** という名前のデータベースを作成し、既定の最大サイズが 250 GB の Standard S0 エディションのデータベースとして指定します。
  
      CREATE DATABASE myTestDB    (EDITION='Standard',     SERVICE_OBJECTIVE='S0');

4. **[実行]** をクリックしてクエリを実行します。
5. オブジェクト エクスプローラーで [データベース] ノードを右クリックし、**[更新]** をクリックして新しいデータベースを表示します。 


## <a name="change-the-service-tier-and-performance-level-of-a-single-database"></a>単一データベースのサービス レベルとパフォーマンス レベルの変更
[Transact-SQL (T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) を使用して、データベースの最大サイズを変更します。

> [!TIP]
> Transact-SQL を使用してデータベースを作成するチュートリアルについては、[Azure Portal を使用したデータベースの作成](sql-database-get-started.md)に関するページを参照してください。
>

## <a name="next-steps"></a>次のステップ
* 管理ツールの概要については、[管理ツールの概要](sql-database-manage-overview.md)に関するページをご覧ください。
* Azure Portal を使用して管理タスクを実行する方法については、「[Azure ポータルを使用した Azure SQL データベースの管理](sql-database-manage-portal.md)」をご覧ください。
* PowerShell を使用して管理タスクを実行する方法については、「[PowerShell を使用した Azure SQL Database の管理](sql-database-manage-powershell.md)」をご覧ください。
* SQL Server Management Studio を使用して管理タスクを実行する方法については、「[SQL Server Management Studio を使用した Azure SQL データベースの管理](sql-database-manage-azure-ssms.md)」をご覧ください。
* SQL Database のサービスについては、「[SQL Database とは](sql-database-technical-overview.md)」をご覧ください。 
* Azure Database のサーバーとデータベースの機能については、[機能](sql-database-features.md)に関するページをご覧ください。

