---
title: "Azure SQL Data Warehouse の復元 (PowerShell) | Microsoft Docs"
description: "Azure SQL Data Warehouse を復元するための PowerShell タスク。"
services: sql-data-warehouse
documentationcenter: NA
author: Lakshmi1812
manager: jhubbard
editor: 
ms.assetid: ac62f154-c8b0-4c33-9c42-f480808aa1d2
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: backup-restore
ms.date: 10/31/2016
ms.author: lakshmir;barbkess
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 0b6f4fa657a16196ba8f42c2137dc3c245d25a07
ms.lasthandoff: 12/08/2016


---
# <a name="restore-an-azure-sql-data-warehouse-powershell"></a>Azure SQL Data Warehouse の復元 (PowerShell)
> [!div class="op_single_selector"]
> * [概要][Overview]
> * [ポータル][Portal]
> * [PowerShell][PowerShell]
> * [REST][REST]
> 
> 

この記事では、PowerShell を使用して Azure SQL Data Warehouse を復元する方法について説明します。

## <a name="before-you-begin"></a>開始する前に
**DTU 容量を確認します。** 各 SQL Data Warehouse は、既定の DTU クォータが割り当てられている SQL サーバー (例: myserver.database.windows.net) でホストされます。  SQL Data Warehouse を復元する前に、データベースの復元に必要な量の DTU クォータがSQL server に残っていることを確認してください。 必要な DTU を計算する方法と DTU を要求する方法については、[DTU クォータの変更の要求][Request a DTU quota change]に関するトピックをご覧ください。

### <a name="install-powershell"></a>PowerShell をインストールする
SQL Data Warehouse で Azure PowerShell を使用するには、Azure PowerShell Version 1.0 以降をインストールする必要があります。  **Get-Module -ListAvailable -Name AzureRM**を実行することで、バージョンを確認できます。  最新バージョンは、[Microsoft Web Platform Installer][Microsoft Web Platform Installer] からインストールできます。  最新バージョンのインストールの詳細については、[Azure PowerShell のインストールと構成方法][How to install and configure Azure PowerShell]に関するページを参照してください。

## <a name="restore-an-active-or-paused-database"></a>アクティブまたは一時停止中のデータベースを復元する
スナップショットからデータベースを復元するには、[Restore-AzureRmSqlDatabase PowerShell][Restore-AzureRmSqlDatabase] コマンドレットを使用します。

1. Windows PowerShell を開きます。
2. Azure アカウントに接続して、アカウントに関連付けられているすべてのサブスクリプションを一覧表示します。
3. 復元するデータベースを含むサブスクリプションを選択します。
4. データベースの復元ポイントを表示します。
5. RestorePointCreationDate を使用して、目的の復元ポイントを選択します。
6. 目的の復元ポイントに、データベースを復元します。
7. 復元されたデータベースがオンラインであることを確認します。

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# List the last 10 database restore points
((Get-AzureRMSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName ($DatabaseName).RestorePointCreationDate)[-10 .. -1]

# Or list all restore points
Get-AzureRmSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Get the specific database to restore
$Database = Get-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Pick desired restore point using RestorePointCreationDate
$PointInTime="<RestorePointCreationDate>"  

# Restore database from a restore point
$RestoredDatabase = Restore-AzureRmSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.$ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

> [!NOTE]
> 復元が完了したら、「[復旧後のデータベースの構成][Configure your database after recovery]」の手順に従って、復旧されたデータベースを構成できます。
> 
> 

## <a name="restore-a-deleted-database"></a>削除されたデータベースの復元
削除されたデータベースを復元するには、[Restore-AzureRmSqlDatabase][Restore-AzureRmSqlDatabase] コマンドレットを使用します。

1. Windows PowerShell を開きます。
2. Azure アカウントに接続して、アカウントに関連付けられているすべてのサブスクリプションを一覧表示します。
3. 復元する削除済みデータベースを含むサブスクリプションを選択します。
4. 特定の削除済みデータベースを取得します。
5. その削除済みデータベースを復元します。
6. 復元されたデータベースがオンラインであることを確認します。

```Powershell
$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# Get the deleted database to restore
$DeletedDatabase = Get-AzureRmSqlDeletedDatabaseBackup -ResourceGroupName $ResourceGroupNam -ServerName $ServerName -DatabaseName $DatabaseName

# Restore deleted database
$RestoredDatabase = Restore-AzureRmSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Verify the status of restored database
$RestoredDatabase.status
```

> [!NOTE]
> 復元が完了したら、「[復旧後のデータベースの構成][Configure your database after recovery]」の手順に従って、復旧されたデータベースを構成できます。
> 
> 

## <a name="restore-from-an-azure-geographical-region"></a>Azure 地理的リージョンからの復元
データベースを復旧するには、[Restore-AzureRmSqlDatabase][Restore-AzureRmSqlDatabase] コマンドレットを使用します。

1. Windows PowerShell を開きます。
2. Azure アカウントに接続して、アカウントに関連付けられているすべてのサブスクリプションを一覧表示します。
3. 復元するデータベースを含むサブスクリプションを選択します。
4. 復旧するデータベースを取得します。
5. データベースの復旧要求を作成します。
6. geo リストアされたデータベースの状態を確認します。

```Powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<Subscription_name>"

# Get the database you want to recover
$GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>"

# Recover database
$GeoRestoredDatabase = Restore-AzureRmSqlDatabase –FromGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourTargetServer>" -TargetDatabaseName "<NewDatabaseName>" –ResourceId $GeoBackup.ResourceID

# Verify that the geo-restored database is online
$GeoRestoredDatabase.status
```

> [!NOTE]
> 復元が完了した後にデータベースを構成する方法については、「[復旧後のデータベースの構成][Configure your database after recovery]」をご覧ください。
> 
> 

ソース データベースの TDE が有効な場合、復旧したデータベースも TDE が有効になります。

## <a name="next-steps"></a>次のステップ
Azure SQL Database の各エディションのビジネス継続性機能については、[Azure SQL Database のビジネス継続性の概要][Azure SQL Database business continuity overview]に関する記事をご覧ください。

<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Request a DTU quota change]: ./sql-data-warehouse-get-started-create-support-ticket.md#request-quota-change
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery

<!--MSDN references-->
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps

