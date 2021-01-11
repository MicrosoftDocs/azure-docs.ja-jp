---
title: 削除された SQL プールを復元する
description: 削除された SQL プールを復元するためのハウツー ガイド。
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 08/29/2018
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: fc21dc67dd6c686eedf307660a8d8b7afd60db71
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87075856"
---
# <a name="restore-a-deleted-sql-pool-using-azure-synapse-analytics"></a>Azure Synapse Analytics を使用して削除された SQL プールを復元する

この記事では、Azure portal または PowerShell を使用して SQL を復元する方法について説明します。

## <a name="before-you-begin"></a>開始する前に

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**DTU 容量を確認します。** 各 SQL プールは、既定の DTU クォータが割り当てられている[論理 SQL サーバー](../../azure-sql/database/logical-servers.md) (例: myserver.database.windows.net) によってホストされます。  データベースを復元するための十分な DTU クォータがサーバーに残っていることを確認する必要があります。 必要な DTU を計算する方法と DTU を要求する方法については、 [DTU クォータの変更の要求](sql-data-warehouse-get-started-create-support-ticket.md)に関するトピックをご覧ください。

## <a name="restore-a-deleted-data-warehouse-through-powershell"></a>削除されたデータ ウェアハウスを PowerShell を使用して復元する

削除された SQL プールを復元するには、[Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) コマンドレットを使用します。 対応する論理サーバーも削除されている場合、そのデータ ウェアハウスを復元することはできません。

1. 開始する前に、必ず [Azure PowerShell をインストール](/powershell/azure/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)してください。
2. PowerShell を開きます。
3. Azure アカウントに接続して、アカウントに関連付けられているすべてのサブスクリプションを一覧表示します。
4. 復元する削除済み SQL プールが含まれるサブスクリプションを選択します。
5. 特定の削除済みデータ ウェアハウスを取得します。
6. 削除された SQL プールを復元する
    1. 削除された SQL プールを別のサーバーに復元するには、必ず他のサーバー名を指定します。  このサーバーは、別のリソース グループとリージョン内に存在していてもかまいません。
    1. 別のサブスクリプションに復元するには、[[移動]](../../azure-resource-manager/management/move-resource-group-and-subscription.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#use-the-portal) ボタンを使用して、サーバーを別のサブスクリプションに移動します。
7. 復元されたデータ ウェアハウスがオンラインになっていることを確認します。
8. 復元が完了したら、「[復旧後のデータベースの構成](../../azure-sql/database/disaster-recovery-guidance.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#configure-your-database-after-recovery)」の手順に従って、復旧されたデータ ウェアハウスを構成できます。

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

# Get the deleted database to restore
$DeletedDatabase = Get-AzSqlDeletedDatabaseBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Restore deleted database
$RestoredDatabase = Restore-AzSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Use the following command to restore deleted data warehouse to a different server
#$RestoredDatabase = Restore-AzSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $TargetResourceGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Verify the status of restored database
$RestoredDatabase.status
```

## <a name="restore-a-deleted-database-using-the-azure-portal"></a>Azure portal を使用して削除されたデータベースを復元する

1. [Azure portal](https://portal.azure.com/) にサインインします。
2. 削除されたデータ ウェアハウスがホストされていたサーバーに移動します。
3. 目次で **[削除されたデータベース]** アイコンを選択します。

    ![削除済みデータベース](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-01.png)

4. 復元する削除済み SQL Data Warehouse を選択します。

    ![削除済みデータベースを選択する](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-11.png)

5. 新しい **[データベース名]** を指定して **[OK]** をクリックします

    ![データベース名を指定する](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-21.png)

## <a name="next-steps"></a>次の手順

- [既存の SQL プールを復元する](sql-data-warehouse-restore-active-paused-dw.md)
- [geo バックアップ SQL プールからの復元](sql-data-warehouse-restore-from-geo-backup.md)
