---
title: 既存の Azure SQL Data Warehouse を復元する | Microsoft Docs
description: 既存の Azure SQL Data Warehouse を復元するための攻略ガイド。
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 08/29/2018
ms.author: anjangsh
ms.reviewer: igorstan
ms.openlocfilehash: 7470dd96109823057a174d2ecf097583dcb51898
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2019
ms.locfileid: "69575275"
---
# <a name="restore-an-existing-azure-sql-data-warehouse"></a>既存の Azure SQL Data Warehouse を復元する

この記事では、Azure portal と PowerShell を使用して既存の SQL Data Warehouse を復元する方法について説明します。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

**DTU 容量を確認します。** 各 SQL Data Warehouse は、既定の DTU クォータが割り当てられている SQL サーバー (例: myserver.database.windows.net) でホストされます。 データベースを復元するための十分な DTU クォータが SQL Server に残っていることを確認します。 必要な DTU を計算する方法と DTU を要求する方法については、 [DTU クォータの変更の要求][Request a DTU quota change]に関するトピックをご覧ください。

## <a name="before-you-begin"></a>開始する前に

1. 必ず [Azure PowerShell をインストール][Install Azure PowerShell]してください。
2. 復元する既存の復元ポイントがあること。 新しい復元を作成する場合は、「[the tutorial to create a new user-defined restore point][the tutorial to create a new user-defined restore point]」 (新しいユーザー定義の復元ポイントを作成するチュートリアル) を参照してください。

## <a name="restore-an-existing-data-warehouse-through-powershell"></a>PowerShell を使用して既存のデータ ウェアハウスを復元する

復元ポイントから既存のデータ ウェアハウスを復元するには、[Restore-AzSqlDatabase][Restore-AzSqlDatabase] PowerShell コマンドレットを使用します。

1. PowerShell を開きます。

2. Azure アカウントに接続して、アカウントに関連付けられているすべてのサブスクリプションを一覧表示します。

3. 復元するデータベースを含むサブスクリプションを選択します。

4. データ ウェアハウスの復元ポイントを一覧表示します。

5. RestorePointCreationDate を使用して、目的の復元ポイントを選択します。

6. [Restore-AzSqlDatabase][Restore-AzSqlDatabase] PowerShell コマンドレットを使用して、目的の復元ポイントにデータ ウェアハウスを復元します。
        1. SQL Data Warehouse を別の論理サーバーに復元するには、必ず他の論理サーバー名を指定します。  この論理サーバーは、別のリソース グループとリージョン内に存在していても指定できます。
        2. 別のサブスクリプションに復元するには、[移動] ボタンを使用して、論理サーバーを別のサブスクリプションに移動します。

7. 復元されたデータ ウェアハウスがオンラインになっていることを確認します。

8. 復元が完了したら、「[復旧後のデータベースの構成][Configure your database after recovery]」の手順に従って、復旧されたデータ ウェアハウスを構成できます。

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
#$TargetResourceGroupName="<YourTargetResourceGroupName>" # uncomment to restore to a different logical server.
#$TargetServerName="<YourtargetServerNameWithoutURLSuffixSeeNote>"  
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# Or list all restore points
Get-AzSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Get the specific database to restore
$Database = Get-AzSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Pick desired restore point using RestorePointCreationDate "xx/xx/xxxx xx:xx:xx xx"
$PointInTime="<RestorePointCreationDate>"  

# Restore database from a restore point
$RestoredDatabase = Restore-AzSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Use the following command to restore to a different logical server
#$RestoredDatabase = Restore-AzSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceTargetGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

## <a name="restore-an-existing-data-warehouse-through-the-azure-portal"></a>Azure portal を使用して既存のデータ ウェアハウスを復元する

1. [Azure Portal][Azure portal] にサインインします。
2. 復元元の SQL Data Warehouse に移動します。
3. [概要] ブレードの上部にある **[復元]** を選択します。

    ![ 復元の概要](./media/sql-data-warehouse-restore-active-paused-dw/restoring-01.png)

4. **[自動復元ポイント]** または **[ユーザー定義の復元ポイント]** を選択します。 データ ウェアハウスに自動復元ポイントがない場合は、数時間待つか、復元する前にユーザー定義の復元ポイントを作成します。 ユーザー定義の復元ポイントを使用する場合は、既存のものを選択するか、新しく作成します。 **サーバー**の場合は、別のリソース グループとリージョン内の論理サーバーを選択するか、新しく作成することができます。 すべてのパラメーターを指定したら、 **[Review + Restore]\(確認+ 復元\)** をクリックします。

    ![自動復元ポイント](./media/sql-data-warehouse-restore-active-paused-dw/restoring-11.png)

## <a name="next-steps"></a>次の手順
- [削除されたデータ ウェアハウスを復元する][Restore a deleted data warehouse]
- [geo バックアップ データ ウェアハウスから復元する][Restore from a geo-backup data warehouse]
 
<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Request a DTU quota change]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[the tutorial to create a new user-defined restore point]:../sql-data-warehouse/sql-data-warehouse-restore-points.md
[Install Azure PowerShell]: https://docs.microsoft.com/powershell/azure/overview
[Restore an existing data warehouse]:./sql-data-warehouse-restore-active-paused-dw.md
[Restore a deleted data warehouse]:./sql-data-warehouse-restore-deleted-dw.md
[Restore from a geo-backup data warehouse]:./sql-data-warehouse-restore-from-geo-backup.md

<!--MSDN references-->
[Restore-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
