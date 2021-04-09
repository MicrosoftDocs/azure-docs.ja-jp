---
title: geo バックアップから専用 SQL プールを復元する
description: Azure Synapse Analytics で専用 SQL プールを geo リストアするためのハウツー ガイド
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
ms.openlocfilehash: 4683bd84873506483209f4a0eb3751a1b163ed48
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96449834"
---
# <a name="geo-restore-a-dedicated-sql-pool-in-azure-synapse-analytics"></a>Azure Synapse Analytics で専用 SQL プールを geo リストアする

この記事では、Azure portal と PowerShell を使用して geo バックアップから専用 SQL プール (以前の SQL DW) を復元する方法について説明します。

## <a name="before-you-begin"></a>開始する前に

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**DTU 容量を確認します。** 各専用 SQL プール (以前の SQL DW) は、既定の DTU クォータが割り当てられている[論理 SQL サーバー](../../azure-sql/database/logical-servers.md) (例: myserver.database.windows.net) によってホストされます。 データベースを復元するための十分な DTU クォータが SQL Server に残っていることを確認する必要があります。 必要な DTU を計算する方法と DTU を要求する方法については、 [DTU クォータの変更の要求](sql-data-warehouse-get-started-create-support-ticket.md)に関するトピックをご覧ください。

## <a name="restore-from-an-azure-geographical-region-through-powershell"></a>PowerShell を使用して Azure 地理的リージョンから復元する

Geo バックアップから復元するには、[Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) および [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) コマンドレットを使用します。

> [!NOTE]
> Gen2 への geo リストアを行うことができます。 そのためには、省略可能なパラメーターとして Gen2 の ServiceObjectiveName (例: DW1000 **c**) を指定します。
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

下の手順に従って、geo バックアップから専用 SQL プール (以前の SQL DW) を復元します。

1. [Azure portal](https://portal.azure.com/) アカウントにサインインします。
1. **専用 SQL プール (以前の SQL DW)** を検索します。

   ![新しい DW 2](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new.png)

1. [追加] をクリックして、 **[基本]** タブで要求された情報を入力し、 **[次へ: 追加設定]** を選択します。

   ![基本](./media/sql-data-warehouse-restore-from-geo-backup/georestore-dw-1.png)

1. **[既存のデータを使用します]** パラメーターで、 **[バックアップ]** を選択し、スクロール ダウン オプションから適切なバックアップを選択します。 **[Review + Create]\(レビュー + 作成\)** をクリックします。

   ![バックアップ (backup)](./media/sql-data-warehouse-restore-from-geo-backup/georestore-select.png)

1. データ ウェアハウスが復元されたら、 **[状態]** がオンラインになっていることを確認します。

## <a name="next-steps"></a>次の手順

- [既存の専用 SQL プール (以前の SQL DW) を復元する](sql-data-warehouse-restore-active-paused-dw.md)
- [削除された専用 SQL プール (以前の SQL DW) を復元する](sql-data-warehouse-restore-deleted-dw.md)
