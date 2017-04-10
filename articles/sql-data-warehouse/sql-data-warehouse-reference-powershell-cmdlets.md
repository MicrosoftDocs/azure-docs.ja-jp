---
title: "Azure SQL Data Warehouse の PowerShell コマンドレット"
description: "データベースの一時停止と再開など、Azure SQL Data Warehouse でよく使用される PowerShell コマンドレットを紹介します。"
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: 6f0d5772-f05f-4cc8-9749-4adb153dfd50
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: reference
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 96ac017b469405e0eb106bf5eb391b5273600ab2
ms.lasthandoff: 12/08/2016


---
# <a name="powershell-cmdlets-and-rest-apis-for-sql-data-warehouse"></a>SQL Data Warehouse の PowerShell コマンドレットと REST API
SQL Data Warehouse の管理タスクの多くは、Azure PowerShell コマンドレットまたは REST API を使用して管理できます。  以下に示す例では、PowerShell コマンドを使用して、SQL Data Warehouse で一般的なタスクを自動化する方法を示しています。  適切な REST の例については、[REST を使用したスケーラビリティの管理][Manage scalability with REST]に関する記事をご覧ください。

> [!NOTE]
> SQL Data Warehouse で Azure PowerShell を使用するには、Azure PowerShell バージョン 1.0.3 以降が必要です。  **Get-Module -ListAvailable -Name Azure**を実行することで、バージョンを確認できます。  最新バージョンは、[Microsoft Web Platform Installer][Microsoft Web Platform Installer] からインストールできます。  最新バージョンのインストールの詳細については、[Azure PowerShell のインストールと構成方法][How to install and configure Azure PowerShell]に関するページを参照してください。
> 
> 

## <a name="get-started-with-azure-powershell-cmdlets"></a>Azure PowerShell コマンドレットの使用開始
1. Windows PowerShell を開きます。
2. PowerShell プロンプトで、次のコマンドを実行して Azure Resource Manager にサインインし、サブスクリプションを選択します。
   
    ```PowerShell
    Login-AzureRmAccount
    Get-AzureRmSubscription
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```

## <a name="pause-sql-data-warehouse-example"></a>SQL Data Warehouse の一時停止の例
"Server01" という名前のサーバーでホストされている "Database02" という名前のデータベースを一時停止します。  サーバーは "ResourceGroup1" という名前の Asure リソース グループ内にあります。

```Powershell
Suspend-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
```
バリエーションであるこの例では、取得したオブジェクトを [Suspend-AzureRmSqlDatabase][Suspend-AzureRmSqlDatabase] にパイプ処理します。  その結果、データベースが一時停止されます。 最後のコマンドは結果を表示します。

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzureRmSqlDatabase
$resultDatabase
```

## <a name="start-sql-data-warehouse-example"></a>SQL Data Warehouse の開始の例
"Server01" という名前のサーバーでホストされている "Database02" という名前のデータベースを再開します。 サーバーは "ResourceGroup1" という名前のリソース グループ内にあります。

```Powershell
Resume-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" -DatabaseName "Database02"
```

この例では、"ResourceGroup1" という名前のリソース グループに含まれている "Server01" という名前のサーバーから "Database02" という名前のデータベースを取得します。 取得したオブジェクトを [Resume-AzureRmSqlDatabase][Resume-AzureRmSqlDatabase] にパイプ処理します。

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzureRmSqlDatabase
```

> [!NOTE]
> サーバーが foo.database.windows.net である場合は、PowerShell コマンドレットでサーバー名として "foo" を使用してください。
> 
> 

## <a name="frequently-used-powershell-cmdlets"></a>よく使用される PowerShell コマンドレット
Azure SQL Data Warehouse でよく使用される PowerShell コマンドレットを次に示します。

* [Get-AzureRmSqlDatabase][Get-AzureRmSqlDatabase]
* [Get-AzureRmSqlDeletedDatabaseBackup][Get-AzureRmSqlDeletedDatabaseBackup]
* [Get-AzureRmSqlDatabaseRestorePoints][Get-AzureRmSqlDatabaseRestorePoints]
* [New-AzureRmSqlDatabase][New-AzureRmSqlDatabase]
* [Remove-AzureRmSqlDatabase][Remove-AzureRmSqlDatabase]
* [Restore-AzureRmSqlDatabase][Restore-AzureRmSqlDatabase]
* [Resume-AzureRmSqlDatabase][Resume-AzureRmSqlDatabase]
* [Select-AzureRmSubscription][Select-AzureRmSubscription]
* [Set-AzureRmSqlDatabase][Set-AzureRmSqlDatabase]
* [Suspend-AzureRmSqlDatabase][Suspend-AzureRmSqlDatabase]

## <a name="next-steps"></a>次のステップ
PowerShell のその他の例については、次のトピックをご覧ください。

* [PowerShell を使用して SQL Data Warehouse を作成する][Create a SQL Data Warehouse using PowerShell]
* [データベースの復元][Database restore]

PowerShell で自動化できるタスクの一覧については、[Azure SQL Database コマンドレット][Azure SQL Database Cmdlets]に関するページをご覧ください。  REST で自動化できるタスクの一覧については、「[Operations for Azure SQL Databases (Azure SQL データベースの操作)][Operations for Azure SQL Databases]」をご覧ください。

<!--Image references-->

<!--Article references-->
[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Create a SQL Data Warehouse using PowerShell]: ./sql-data-warehouse-get-started-provision-powershell.md
[Database restore]: ./sql-data-warehouse-restore-database-powershell.md
[Manage scalability with REST]: ./sql-data-warehouse-manage-compute-rest-api.md

<!--MSDN references-->
[Azure SQL Database Cmdlets]: https://msdn.microsoft.com/library/mt574084.aspx
[Operations for Azure SQL Databases]: https://msdn.microsoft.com/library/azure/dn505719.aspx
[Get-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt603648.aspx
[Get-AzureRmSqlDeletedDatabaseBackup]: https://msdn.microsoft.com/library/mt693387.aspx
[Get-AzureRmSqlDatabaseRestorePoints]: https://msdn.microsoft.com/library/mt603642.aspx
[New-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619339.aspx
[Remove-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619368.aspx
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx
[Resume-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619347.aspx
<!-- It appears that Select-AzureRmSubscription isn't documented, so this points to Select-AzureSubscription -->
[Select-AzureRmSubscription]: https://msdn.microsoft.com/library/dn722499.aspx
[Set-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619433.aspx
[Suspend-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619337.aspx

<!--Other Web references-->
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps

