---
title: Azure SQL Database:長期のバックアップ リテンション期間の管理
description: Azure portal と PowerShell を使用して、Azure SQL Database の自動バックアップを Azure ストレージに (最大 10 年間) 格納および復元する方法について説明します。
services: sql-database
ms.service: sql-db-mi
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 12/16/2020
ms.openlocfilehash: fad19d360f7c476ba71a9bbe00b58387b92f8ac4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101690560"
---
# <a name="manage-azure-sql-database-long-term-backup-retention"></a>Azure SQL Database の長期的なバックアップ保有期間を管理する
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL Database を使用すると、バックアップが別々の Azure Blob Storage コンテナーに最長 10 年間自動的に保持されるように、[長期的なバックアップ保有期間](long-term-retention-overview.md)ポリシー (LTR) を設定できます。 Azure Portal または PowerShell でこのようなバックアップを使用して、データベースを復旧できます。 長期保有ポリシーは、[Azure SQL Managed Instance](../managed-instance/long-term-backup-retention-configure.md) でもサポートされています。

## <a name="using-the-azure-portal"></a>Azure ポータルの使用

以下のセクションでは、Azure portal を使用して長期保有ポリシーを設定する方法、使用可能な長期保有バックアップを管理する方法、使用可能なバックアップから復元する方法について説明します。

### <a name="configure-long-term-retention-policies"></a>長期保存ポリシーを構成する

ご利用のサービス レベルのリテンション期間より長く[自動バックアップを保持](long-term-retention-overview.md)するように SQL Database を構成できます。

1. Azure portal で、ご自分のサーバーに移動してから、 **[バックアップ]** を選択します。 **[保持ポリシー]** タブを選択して、バックアップの保持設定を変更します。

   ![保持ポリシーのエクスペリエンス](./media/long-term-backup-retention-configure/ltr-policies-tab.png)

2. [保持ポリシー] タブで、長期的なバックアップ保持ポリシーを設定または変更するデータベースを選択します。 選択されていないデータベースへの影響はありません。

   ![バックアップ保持ポリシーを構成するデータベースを選択する](./media/long-term-backup-retention-configure/ltr-policies-tab-configure.png)

3. **[ポリシーの構成]** ウィンドウで、週単位、月単位、または年単位のバックアップに必要な保有期間を指定します。 長期的なバックアップ保有期間を設定しないことを示すには、保有期間を "0" にします。

   ![ポリシーの構成ウィンドウ](./media/long-term-backup-retention-configure/ltr-configure-policies.png)

4. **[適用]** を選択して、指定した保有期間設定を選択したすべてのデータベースに適用します。

> [!IMPORTANT]
> 長期的なバックアップ保有期間ポリシーを有効にすると、初回バックアップが表示されて復元に利用できる状態になるまでに、最大 7 日かかる場合があります。 LTR バックアップ周期の詳細については、[長期的なバックアップ保有期間](long-term-retention-overview.md)に関するページを参照してください。

### <a name="view-backups-and-restore-from-a-backup"></a>バックアップを表示してバックアップから復元する

LTR ポリシーを使用して保持されている特定のデータベースのバックアップを表示し、それらのバックアップから復元します。

1. Azure portal で、ご自分のサーバーに移動してから、 **[バックアップ]** を選択します。 特定のデータベースに使用可能な LTR バックアップを表示するには、[使用可能な LTR バックアップ] 列の下にある **[管理]** を選択します。 選択したデータベースに使用可能な LTR バックアップの一覧を示すウィンドウが表示されます。

   ![使用可能なバックアップのエクスペリエンス](./media/long-term-backup-retention-configure/ltr-available-backups-tab.png)

1. 表示されている **[使用可能な LTR バックアップ]** ウィンドウで、使用可能なバックアップを確認します。 復元元のバックアップまたは削除するバックアップを選択できます。

   ![使用可能な LTR バックアップを表示する](./media/long-term-backup-retention-configure/ltr-available-backups-manage.png)

1. 使用可能な LTR バックアップから復元するには、復元元のバックアップを選択してから、 **[復元]** を選択します。

   ![使用可能な LTR バックアップから復元する](./media/long-term-backup-retention-configure/ltr-available-backups-restore.png)

1. 新しいデータベースの名前を選択してから、 **[確認と作成]** を選択して、復元の詳細を確認します。 **[作成]** を選択して、選択したバックアップからデータベースを復元します。

   ![復元の詳細を構成する](./media/long-term-backup-retention-configure/restore-ltr.png)

1. ツール バーの通知アイコンを選択して、復元ジョブの状態を確認します。

   ![復元ジョブの進行状況](./media/long-term-backup-retention-configure/restore-job-progress-long-term.png)

1. 復元ジョブが完了したら、 **[SQL データベース]** ページを開き、新しく復元されたデータベースを確認します。

> [!NOTE]
> ここから、SQL Server Management Studio を使用して、復元されたデータベースに接続し、必要なタスクを実行できます。たとえば、[復元されたデータベースからデータを少し抽出して既存のデータベースにコピーしたり、既存のデータベースを削除し、復元されたデータベースの名前を既存のデータベース名に変更したり](recovery-using-backups.md#point-in-time-restore)できます。

## <a name="using-powershell"></a>PowerShell の使用

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> PowerShell Azure Resource Manager モジュールは Azure SQL Database で引き続きサポートされますが、今後の開発はすべて Az.Sql モジュールを対象に行われます。 これらのコマンドレットについては、「[AzureRM.Sql](/powershell/module/AzureRM.Sql/)」を参照してください。 Az モジュールと AzureRm モジュールのコマンドの引数は実質的に同じです。

以下のセクションでは、PowerShell を使用して長期的なバックアップ保有期間を構成し、Azure ストレージ内のバックアップを表示し、Azure ストレージ内のバックアップから復元する方法について説明します。

### <a name="azure-roles-to-manage-long-term-retention"></a>長期保有を管理するための Azure ロール

**Get-AzSqlDatabaseLongTermRetentionBackup** および **Restore-AzSqlDatabase** の場合、次のいずれかのロールが必要です。

- サブスクリプションの所有者ロール、または
- SQL Server 共同作成者ロールまたは
- 次のアクセス許可を持つカスタム ロール:

   Microsoft.Sql/locations/longTermRetentionBackups/read  Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionBackups/read  Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/read

**Remove-AzSqlDatabaseLongTermRetentionBackup** の場合、ロールには、次のいずれかのロールが必要です。

- サブスクリプションの所有者ロール、または
- 次のアクセス許可を持つカスタム ロール:

   Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete

> [!NOTE]
> SQL Server の共同作成者ロールには、LTR バックアップを削除するアクセス許可はありません。

Azure RBAC のアクセス許可は、"*サブスクリプション*" または "*リソース グループ*" のいずれかのスコープで付与できます。 ただし、削除されたサーバーに属する LTR バックアップにアクセスするには、そのサーバーの *サブスクリプション* スコープにアクセス許可を付与する必要があります。

- Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete

### <a name="create-an-ltr-policy"></a>LTR ポリシーを作成する

```powershell
# get the SQL server
$subId = "<subscriptionId>"
$serverName = "<serverName>"
$resourceGroup = "<resourceGroupName>"
$dbName = "<databaseName>"

Connect-AzAccount
Select-AzSubscription -SubscriptionId $subId

$server = Get-AzSqlServer -ServerName $serverName -ResourceGroupName $resourceGroup

# create LTR policy with WeeklyRetention = 12 weeks. MonthlyRetention and YearlyRetention = 0 by default.
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -WeeklyRetention P12W

# create LTR policy with WeeklyRetention = 12 weeks, YearlyRetention = 5 years and WeekOfYear = 16 (week of April 15). MonthlyRetention = 0 by default.
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -WeeklyRetention P12W -YearlyRetention P5Y -WeekOfYear 16
```

### <a name="view-ltr-policies"></a>LTR ポリシーを表示する

サーバー内の LTR ポリシーを一覧表示する例を次に示します

```powershell
# get all LTR policies within a server
$ltrPolicies = Get-AzSqlDatabase -ResourceGroupName $resourceGroup -ServerName $serverName | `
    Get-AzSqlDatabaseLongTermRetentionPolicy

# get the LTR policy of a specific database
$ltrPolicies = Get-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup
```

### <a name="clear-an-ltr-policy"></a>LTR ポリシーをクリアする

LTR ポリシーをデータベースから消去する例を次に示します

```powershell
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -RemovePolicy
```

### <a name="view-ltr-backups"></a>LTR バックアップを表示する

サーバー内の LTR バックアップを一覧表示する例を次に示します。

```powershell
# get the list of all LTR backups in a specific Azure region
# backups are grouped by the logical database id, within each group they are ordered by the timestamp, the earliest backup first
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location

# get the list of LTR backups from the Azure region under the named server
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName

# get the LTR backups for a specific database from the Azure region under the named server
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName -DatabaseName $dbName

# list LTR backups only from live databases (you have option to choose All/Live/Deleted)
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -DatabaseState Live

# only list the latest LTR backup for each database
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName -OnlyLatestPerDatabase
```

### <a name="delete-ltr-backups"></a>LTR バックアップを削除する

バックアップの一覧から LTR バックアップを削除する例を次に示します。

```powershell
# remove the earliest backup
$ltrBackup = $ltrBackups[0]
Remove-AzSqlDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```

> [!IMPORTANT]
> LTR バックアップの削除は、元に戻せません。 サーバーが削除された後に LTR バックアップを削除するには、サブスクリプション スコープのアクセス許可が必要です。 Azure Monitor では、"長期保有バックアップを削除します" という操作をフィルター処理することで、それぞれの削除に関する通知を設定できます。 アクティビティ ログには、どのユーザーがいつ要求を行ったかに関する情報が含まれています。 詳しい手順については、[アクティビティ ログ アラートの作成](../../azure-monitor/alerts/alerts-activity-log.md)に関するページを参照してください。

### <a name="restore-from-ltr-backups"></a>LTR バックアップから復元する

LTR バックアップから復元する例を次に示します。 このインターフェイスは変更されませんでしたが、リソース ID パラメーターでは LTR バックアップ リソース ID が必須になりました。

```powershell
# restore a specific LTR backup as an P1 database on the server $serverName of the resource group $resourceGroup
Restore-AzSqlDatabase -FromLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId -ServerName $serverName -ResourceGroupName $resourceGroup `
    -TargetDatabaseName $dbName -ServiceObjectiveName P1
```

> [!IMPORTANT]
> サーバーまたはリソース グループが削除された後に LTR バックアップから復元するには、サーバーのサブスクリプションをスコープとしたアクセス許可が必要であり、そのサブスクリプションがアクティブである必要があります。 また、オプションの -ResourceGroupName パラメーターを省略する必要もあります。

> [!NOTE]
> ここから、SQL Server Management Studio を使用して、復元されたデータベースに接続し、必要なタスクを実行できます。たとえば、復元されたデータベースからデータを少し抽出して既存のデータベースにコピーしたり、既存のデータベースを削除し、復元されたデータベースの名前を既存のデータベース名に変更したりできます。 [ポイントインタイム リストア](recovery-using-backups.md#point-in-time-restore)をご覧ください。

## <a name="limitations"></a>制限事項
- LTR バックアップから復元する場合、読み取りスケール プロパティは無効になります。 復元されたデータベースの読み取りスケールを有効にするには、データベースが作成された後にデータベースを更新します。
- データベースがエラスティック プール内にあるときに作成された LTR バックアップから復元する場合は、ターゲットのサービス レベル目標を指定する必要があります。 

## <a name="next-steps"></a>次のステップ

- サービスによって生成された自動バックアップについては、[自動バックアップ](automated-backups-overview.md)に関する記事を参照してください。
- バックアップの長期保存については、[バックアップの長期保存](long-term-retention-overview.md)に関する記事を参照してください。
