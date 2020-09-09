---
title: geo バックアップからデータ ウェアハウスを復元する
description: SQL プールを geo リストアするためのハウツー ガイド。
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 07/12/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 3c8d78c164cefbab03d9d3fa783c75ded9773d38
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87075821"
---
# <a name="geo-restore-for-sql-pool"></a>SQL プールの geo リストア

この記事では、Azure portal と PowerShell を使用して geo バックアップから SQL プールを復元する方法について説明します。

## <a name="before-you-begin"></a>開始する前に

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**DTU 容量を確認します。** 各 SQL プールは、既定の DTU クォータが割り当てられている[論理 SQL サーバー](../../azure-sql/database/logical-servers.md) (例: myserver.database.windows.net) でホストされます。 データベースを復元するための十分な DTU クォータが SQL Server に残っていることを確認する必要があります。 必要な DTU を計算する方法と DTU を要求する方法については、 [DTU クォータの変更の要求](sql-data-warehouse-get-started-create-support-ticket.md)に関するトピックをご覧ください。

## <a name="restore-from-an-azure-geographical-region-through-powershell"></a>PowerShell を使用して Azure 地理的リージョンから復元する

Geo バックアップから復元するには、[Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) および [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) コマンドレットを使用します。

> [!NOTE]
> Gen2 への geo リストアを行うことができます。 そのためには、省略可能なパラメーターとして Gen2 の ServiceObjectiveName (例: DW1000**c**) を指定します。
>

1. 開始する前に、必ず [Azure PowerShell をインストール](/powershell/azure/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)してください。
2. PowerShell を開きます。
3. Azure アカウントに接続して、アカウントに関連付けられているすべてのサブスクリプションを一覧表示します。
4. 復元するデータ ウェアハウスを含むサブスクリプションを選択します。
5. 復旧するデータ ウェアハウスを取得します。
6. データ ウェアハウスの復旧要求を作成します。
7. geo リストアされたデータ ウェアハウスの状態を確認します。
8. 復元が完了した後にデータ ウェアハウスを構成する方法については、「[復旧後のデータベースの構成]( ../../sql-database/sql-database-disaster-recovery.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#configure-your-database-after-recovery)」を参照してください。

```Powershell
$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$TargetResourceGroupName="<YourTargetResourceGroupName>" # Restore to a different server.
$TargetServerName="<YourtargetServerNameWithoutURLSuffixSeeNote>"  
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"
$TargetServiceObjective="<YourTargetServiceObjective-DWXXXc>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName
Get-AzureSqlDatabase -ServerName $ServerName

# Get the data warehouse you want to recover
$GeoBackup = Get-AzSqlDatabaseGeoBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Recover data warehouse
$GeoRestoredDatabase = Restore-AzSqlDatabase –FromGeoBackup -ResourceGroupName $TargetResourceGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $GeoBackup.ResourceID -ServiceObjectiveName $TargetServiceObjective

# Verify that the geo-restored data warehouse is online
$GeoRestoredDatabase.status
```

ソース データベースの TDE が有効な場合、復旧したデータベースも TDE が有効になります。

## <a name="restore-from-an-azure-geographical-region-through-azure-portal"></a>Azure portal を使用して Azure 地理的リージョンから復元する

次の手順に従って、geo バックアップから SQL プールを復元します。

1. [Azure portal](https://portal.azure.com/) アカウントにサインインします。
2. **[+ リソースの作成]** をクリックします。

   ![新しい DW](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new.png)

3. **[データベース]** をクリックしてから、 **[Azure Synapse Analytics (以前の SQL DW)]** をクリックします。

   ![新しい DW 2](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new-02.png)

4. **[基本]** タブで要求された情報を入力し、 **[次へ: 追加設定]** を選択します。

   ![基本](./media/sql-data-warehouse-restore-from-geo-backup/georestore-dw-1.png)

5. **[既存のデータを使用します]** パラメーターで、 **[バックアップ]** を選択し、スクロール ダウン オプションから適切なバックアップを選択します。 **[Review + Create]\(レビュー + 作成\)** をクリックします。

   ![バックアップ (backup)](./media/sql-data-warehouse-restore-from-geo-backup/georestore-select.png)

6. データ ウェアハウスが復元されたら、 **[状態]** がオンラインになっていることを確認します。

## <a name="next-steps"></a>次の手順

- [既存の SQL プールを復元する](sql-data-warehouse-restore-active-paused-dw.md)
- [削除された SQL プールを復元する](sql-data-warehouse-restore-deleted-dw.md)
