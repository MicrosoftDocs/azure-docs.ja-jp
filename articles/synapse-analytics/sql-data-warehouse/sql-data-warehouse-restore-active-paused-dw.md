---
title: 既存の専用 SQL プール (旧称 SQL DW) を復元する
description: Azure Synapse Analytics で既存の専用 SQL プールを復元するためのハウツー ガイド。
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/13/2020
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 2ce552a13592c9d26ef70575f98b0b76ecc454ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97591993"
---
# <a name="restore-an-existing-dedicated-sql-pool-formerly-sql-dw"></a>既存の専用 SQL プール (旧称 SQL DW) を復元する

この記事では、Azure portal と PowerShell を使用して、既存の専用 SQL プール (旧称 SQL DW) を復元する方法について説明します。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**DTU 容量を確認します。** 各プールは、既定の DTU クォータがある[論理 SQL サーバー](../../azure-sql/database/logical-servers.md) (例: myserver.database.windows.net) でホストされます。 データベースを復元するための十分な DTU クォータがサーバーに残っていることを確認します。 必要な DTU を計算する方法と DTU を要求する方法については、 [DTU クォータの変更の要求](sql-data-warehouse-get-started-create-support-ticket.md)に関するトピックをご覧ください。

## <a name="before-you-begin"></a>開始する前に

1. 必ず [Azure PowerShell をインストール](/powershell/azure/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)してください。
2. 復元する既存の復元ポイントがあること。 新しい復元を作成する場合は、「[the tutorial to create a new user-defined restore point](sql-data-warehouse-restore-points.md)」 (新しいユーザー定義の復元ポイントを作成するチュートリアル) を参照してください。

## <a name="restore-an-existing-dedicated-sql-pool-formerly-sql-dw-through-powershell"></a>PowerShell を使用して既存の専用 SQL プール (旧称 SQL DW) を復元する

復元ポイントから既存の専用 SQL プール (旧称 SQL DW) を復元するには、[Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) PowerShell コマンドレットを使用します。

1. PowerShell を開きます。

2. Azure アカウントに接続して、アカウントに関連付けられているすべてのサブスクリプションを一覧表示します。

3. 復元するデータベースを含むサブスクリプションを選択します。

4. 専用 SQL プール (旧称 SQL DW) の復元ポイントを一覧表示します。

5. RestorePointCreationDate を使用して、目的の復元ポイントを選択します。

6. [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) PowerShell コマンドレットを使用して、目的の復元ポイントに専用 SQL プール (旧称 SQL DW) を復元します。

    1. 専用 SQL プール (旧称 SQL DW) を別のサーバーに復元するには、必ず他のサーバー名を指定します。  このサーバーは、別のリソース グループとリージョン内に存在していてもかまいません。
    2. 別のサブスクリプションに復元するには、[移動] ボタンを使用して、サーバーを別のサブスクリプションに移動します。

7. 復元された専用 SQL プール (旧称 SQL DW)がオンラインになっていることを確認します。

8. 復元が完了したら、[復旧後のデータベースの構成](../../azure-sql/database/disaster-recovery-guidance.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#configure-your-database-after-recovery)の手順に従って、復旧された専用 SQL プール (旧称 SQL DW) を構成できます。

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
#$TargetResourceGroupName="<YourTargetResourceGroupName>" # uncomment to restore to a different server.
#$TargetServerName="<YourtargetServerNameWithoutURLSuffixSeeNote>"  
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# Or list all restore points
Get-AzSqlDatabaseRestorePoint -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Get the specific database to restore
$Database = Get-AzSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Pick desired restore point using RestorePointCreationDate "xx/xx/xxxx xx:xx:xx xx"
$PointInTime="<RestorePointCreationDate>"  

# Restore database from a restore point
$RestoredDatabase = Restore-AzSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Use the following command to restore to a different server
#$TargetResourceGroupName = $Database.ResourceGroupName # for restoring to different server in same resourcegroup 
#$RestoredDatabase = Restore-AzSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $TargetResourceGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

## <a name="restore-an-existing-dedicated-sql-pool-formerly-sql-dw-through-the-azure-portal"></a>Azure portal を使用して既存の専用 SQL プール (旧称 SQL DW) を復元する

1. [Azure portal](https://portal.azure.com/) にサインインします。
2. 復元元の専用 SQL プールに移動します。
3. [概要] ブレードの上部にある **[復元]** を選択します。

    ![ 復元の概要](./media/sql-data-warehouse-restore-active-paused-dw/restoring-01.png)

4. **[自動復元ポイント]** または **[ユーザー定義の復元ポイント]** を選択します。 専用 SQL プール (旧称 SQL DW) に自動復元ポイントがない場合は、数時間待つか、復元する前にユーザー定義の復元ポイントを作成します。 ユーザー定義の復元ポイントを使用する場合は、既存のものを選択するか、新しく作成します。 **サーバー** の場合は、別のリソース グループとリージョン内のサーバーを選択するか、新しく作成することができます。 すべてのパラメーターを指定したら、 **[Review + Restore]\(確認+ 復元\)** をクリックします。

    ![自動復元ポイント](./media/sql-data-warehouse-restore-active-paused-dw/restoring-11.png)

## <a name="next-steps"></a>次の手順

- [削除された専用 SQL プール (旧称 SQL DW) を復元する](sql-data-warehouse-restore-deleted-dw.md)
- [geo バックアップ専用 SQL プール (旧称 SQL DW) から復元する](sql-data-warehouse-restore-from-geo-backup.md)
