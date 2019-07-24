---
title: Azure SQL Data Warehouse の PowerShell コマンドレット
description: データベースの一時停止と再開など、Azure SQL Data Warehouse でよく使用される PowerShell コマンドレットを紹介します。
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: e62f12123ae9af4f5e09d19622ba1558c2f43184
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59790674"
---
# <a name="powershell-cmdlets-and-rest-apis-for-sql-data-warehouse"></a>SQL Data Warehouse の PowerShell コマンドレットと REST API
SQL Data Warehouse の管理タスクの多くは、Azure PowerShell コマンドレットまたは REST API を使用して管理できます。  以下に示す例では、PowerShell コマンドを使用して、SQL Data Warehouse で一般的なタスクを自動化する方法を示しています。  適切な REST の例については、[REST を使用したスケーラビリティの管理][Manage scalability with REST]に関する記事をご覧ください。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="get-started-with-azure-powershell-cmdlets"></a>Azure PowerShell コマンドレットの使用開始
1. Windows PowerShell を開きます。
2. PowerShell プロンプトで、次のコマンドを実行して Azure Resource Manager にサインインし、サブスクリプションを選択します。
   
    ```powershell
    Connect-AzAccount
    Get-AzSubscription
    Select-AzSubscription -SubscriptionName "MySubscription"
    ```

## <a name="pause-sql-data-warehouse-example"></a>SQL Data Warehouse の一時停止の例
"Server01" という名前のサーバーでホストされている "Database02" という名前のデータベースを一時停止します。  サーバーは "ResourceGroup1" という名前の Asure リソース グループ内にあります。

```Powershell
Suspend-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
```
バリエーションであるこの例では、取得したオブジェクトを [Suspend-AzSqlDatabase][Suspend-AzSqlDatabase] にパイプ処理します。  その結果、データベースが一時停止されます。 最後のコマンドは結果を表示します。

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzSqlDatabase
$resultDatabase
```

## <a name="start-sql-data-warehouse-example"></a>SQL Data Warehouse の開始の例
"Server01" という名前のサーバーでホストされている "Database02" という名前のデータベースを再開します。 サーバーは "ResourceGroup1" という名前のリソース グループ内にあります。

```Powershell
Resume-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" -DatabaseName "Database02"
```

この例では、"ResourceGroup1" という名前のリソース グループに含まれている "Server01" という名前のサーバーから "Database02" という名前のデータベースを取得します。 取得したオブジェクトを [Resume-AzSqlDatabase][Resume-AzSqlDatabase] にパイプ処理します。

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzSqlDatabase
```

> [!NOTE]
> サーバーが foo.database.windows.net である場合は、PowerShell コマンドレットでサーバー名として "foo" を使用してください。
> 
> 

## <a name="other-supported-powershell-cmdlets"></a>その他のサポートされている PowerShell コマンドレット
Azure SQL Data Warehouse でサポートされている PowerShell コマンドレットを次に示します。

* [Get-AzSqlDatabase][Get-AzSqlDatabase]
* [Get-AzSqlDeletedDatabaseBackup][Get-AzSqlDeletedDatabaseBackup]
* [Get-AzSqlDatabaseRestorePoints][Get-AzSqlDatabaseRestorePoints]
* [New-AzSqlDatabase][New-AzSqlDatabase]
* [Remove-AzSqlDatabase][Remove-AzSqlDatabase]
* [Restore-AzSqlDatabase][Restore-AzSqlDatabase]
* [Resume-AzSqlDatabase][Resume-AzSqlDatabase]
* [Select-AzSubscription][Select-AzSubscription]
* [Set-AzSqlDatabase][Set-AzSqlDatabase]
* [Suspend-AzSqlDatabase][Suspend-AzSqlDatabase]

## <a name="next-steps"></a>次の手順
PowerShell のその他の例については、次のトピックをご覧ください。

* [PowerShell を使用して SQL Data Warehouse を作成する][Create a SQL Data Warehouse using PowerShell]
* [データベースの復元][Database restore]

PowerShell で自動化できるその他のタスクについては、[Azure SQL Database コマンドレット][Azure SQL Database Cmdlets]に関するページを参照してください。 Azure SQL Data Warehouse ではサポートされていない Azure SQL Database コマンドレットがあります。  REST で自動化できるタスクの一覧については、「[Operations for Azure SQL Database][Operations for Azure SQL Database]」 (Azure SQL Database の操作) を参照してください。

<!--Image references-->

<!--Article references-->
[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Create a SQL Data Warehouse using PowerShell]: ./create-data-warehouse-powershell.md
[Database restore]: ./sql-data-warehouse-restore-database-powershell.md
[Manage scalability with REST]: ./sql-data-warehouse-manage-compute-rest-api.md

<!--MSDN references-->
[Azure SQL Database Cmdlets]: https://docs.microsoft.com/powershell/module/az.sql
[Operations for Azure SQL Database]: https://msdn.microsoft.com/library/azure/dn505719.aspx
[Get-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabase
[Get-AzSqlDeletedDatabaseBackup]: https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup
[Get-AzSqlDatabaseRestorePoints]: https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserestorepoints
[New-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabase
[Remove-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabase
[Restore-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase
[Resume-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/resume-azsqldatabase
<!-- It appears that Select-AzSubscription isn't documented, so this points to Select-AzureSubscription -->
[Select-AzSubscription]: https://msdn.microsoft.com/library/dn722499.aspx
[Set-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase
[Suspend-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/suspend-azsqldatabase

<!--Other Web references-->
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
