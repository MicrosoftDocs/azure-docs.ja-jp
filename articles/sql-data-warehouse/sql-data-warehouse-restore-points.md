---
title: ユーザー定義の復元ポイント | Microsoft Docs
description: Azure SQL Data Warehouse の復元ポイントを作成する方法。
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 07/03/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.openlocfilehash: 9dcb8b2e9c1b75aac3c195f89777ac9c6eb030d7
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2019
ms.locfileid: "69575252"
---
# <a name="user-defined-restore-points"></a>ユーザー定義の復元ポイント

この記事では、PowerShell と Azure portal を使用して Azure SQL Data Warehouse のユーザー定義の復元ポイントを新しく作成する方法について説明します。

## <a name="create-user-defined-restore-points-through-powershell"></a>PowerShell を使用してユーザー定義の復元ポイントを作成する

ユーザー定義の復元ポイントを作成するには、[New-AzSqlDatabaseRestorePoint][New-AzSqlDatabaseRestorePoint] PowerShell コマンドレットを使用します。

1. 開始する前に、必ず [Azure PowerShell をインストール][Install Azure PowerShell]してください。
2. PowerShell を開きます。
3. Azure アカウントに接続して、アカウントに関連付けられているすべてのサブスクリプションを一覧表示します。
4. 復元するデータベースを含むサブスクリプションを選択します。
5. データ ウェアハウスの即時コピーに対する復元ポイントを作成します。

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$Label = "<YourRestorePointLabel>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# Create a restore point of the original database
New-AzSqlDatabaseRestorePoint -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -RestorePointLabel $Label

```

6. すべての既存の復元ポイントを一覧表示します。

```Powershell
# List all restore points
Get-AzSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName
```

## <a name="create-user-defined-restore-points-through-the-azure-portal"></a>Azure portal を使用してユーザー定義の復元ポイントを作成する

ユーザー定義の復元ポイントは、Azure portal を使用して作成することもできます。

1. [Azure portal][Azure portal] アカウントにサインインします。

2. 復元ポイントを作成する SQL データ ウェアハウスに移動します。

3. 左側のウィンドウで **[概要]** を選択し、 **[+ 新しい復元ポイント]** を選択します。 [新しい復元ポイント] ボタンが有効になっていない場合は、データ ウェアハウスが一時停止されていないことを確認します。

    ![新しい復元ポイント](./media/sql-data-warehouse-restore-points/creating-restore-point-01.png)

4. ユーザー定義の復元ポイントの名前を指定して **[適用]** をクリックします。 ユーザー定義の復元ポイントには、7 日間の既定の保有期間があります。

    ![復元ポイントの名前](./media/sql-data-warehouse-restore-points/creating-restore-point-11.png)

## <a name="next-steps"></a>次の手順

- [既存のデータ ウェアハウスの復元][Restore an existing data warehouse]
- [削除されたデータ ウェアハウスを復元する][Restore a deleted data warehouse]
- [geo バックアップ データ ウェアハウスから復元する][Restore from a geo-backup data warehouse]

<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Request a DTU quota change]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[Install Azure PowerShell]: https://docs.microsoft.com/powershell/azure/overview
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[PowerShelldoc]:./sql-data-warehouse-restore-points.md#create-user-defined-restore-points-through-powershell
[Restore an existing data warehouse]:./sql-data-warehouse-restore-active-paused-dw.md
[Restore a deleted data warehouse]:./sql-data-warehouse-restore-deleted-dw.md
[Restore from a geo-backup data warehouse]:./sql-data-warehouse-restore-from-geo-backup.md
<!--MSDN references-->
[New-AzSqlDatabaseRestorePoint]: https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabaserestorepoint?view=azps-2.4.0

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
