---
title: Azure SQL Managed Instance:長期的なバックアップ保有期間
description: PowerShell を使用して、Azure SQL Managed Instance の個別の Azure BLOB ストレージ コンテナーに自動バックアップを格納して復元する方法について説明します。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: shkale-msft
ms.author: shkale
ms.reviewer: mathoma, sstein
ms.date: 02/25/2021
ms.openlocfilehash: f298f0f9d76750be932db79b5a08b6385e984f88
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102052028"
---
# <a name="manage-azure-sql-managed-instance-long-term-backup-retention-powershell"></a>Azure SQL Managed Instance の長期的なバックアップ保有期間を管理する (PowerShell)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL Managed Instance では、[長期的なバックアップ保有期間](../database/long-term-retention-overview.md)ポリシー (LTR) を、パブリック プレビュー機能として構成できます。 これにより、データベースのバックアップを個別の Azure BLOB ストレージ コンテナーに最大 10 年間自動的に保持できます。 そして、PowerShell でこれらのバックアップを使用して、データベースを復旧できます。

   > [!IMPORTANT]
   > マネージド インスタンスの LTR は現在、Azure パブリック リージョンでパブリック プレビューとして利用できます。 

以下のセクションでは、PowerShell を使用して長期的なバックアップ保有期間を構成し、Azure SQL ストレージ内のバックアップを表示し、Azure SQL ストレージ内のバックアップから復元する方法について説明します。


## <a name="using-the-azure-portal"></a>Azure ポータルの使用

以下のセクションでは、Azure portal を使用して長期保有ポリシーを設定する方法、使用可能な長期保有バックアップを管理する方法、使用可能なバックアップから復元する方法について説明します。

### <a name="configure-long-term-retention-policies"></a>長期保存ポリシーを構成する

ご利用のサービス レベルのリテンション期間より長く[自動バックアップを保持](../database/long-term-retention-overview.md)するように SQL Managed Instance を構成できます。

1. Azure portal で、マネージド インスタンスを選択し、 **[バックアップ]** をクリックします。 **[アイテム保持ポリシー]** タブで、長期的なバックアップ保持ポリシーを設定または変更するデータベースを選択します。 変更は、選択されていないデータベースには適用されません。 

   ![バックアップの管理リンク](./media/long-term-backup-retention-configure/ltr-configure-ltr.png)

2. **[ポリシーの構成]** ウィンドウで、週単位、月単位、または年単位のバックアップに必要な保有期間を指定します。 長期的なバックアップ保有期間を設定しないことを示すには、保有期間を "0" にします。

   ![ポリシーを構成する](./media/long-term-backup-retention-configure/ltr-configure-policies.png)

3. 完了したら、 **[適用]** をクリックします。

> [!IMPORTANT]
> 長期的なバックアップ保有期間ポリシーを有効にすると、初回バックアップが表示されて復元に利用できる状態になるまでに、最大 7 日かかる場合があります。 LTR バックアップ周期の詳細については、[長期的なバックアップ保有期間](../database/long-term-retention-overview.md)に関するページを参照してください。

### <a name="view-backups-and-restore-from-a-backup"></a>バックアップを表示してバックアップから復元する

LTR ポリシーを使用して保持されている特定のデータベースのバックアップを表示し、それらのバックアップから復元します。

1. Azure portal で、マネージド インスタンスを選択し、 **[バックアップ]** をクリックします。 **[利用可能なバックアップ]** タブで、利用可能なバックアップを表示するデータベースを選択します。 **Manage** をクリックします。

   ![データベースを選択する](./media/long-term-backup-retention-configure/ltr-available-backups-select-database.png)

1. **[バックアップの管理]** ウィンドウで、利用可能なバックアップを確認します。

   ![バックアップを確認する](./media/long-term-backup-retention-configure/ltr-available-backups.png)

1. 復元元になるバックアップを選択し、 **[復元]** をクリックします。次に、復元ページで新しいデータベース名を指定します。 バックアップとソースはこのページにあらかじめ入力されます。 

   ![復元するバックアップの選択](./media/long-term-backup-retention-configure/ltr-available-backups-restore.png)
   
   ![復元](./media/long-term-backup-retention-configure/ltr-restore.png)

1. **[確認および作成]** をクリックし、復元の詳細を確認します。 次に **[作成]** をクリックし、選択したバックアップからデータベースを復元します。

1. ツール バーの通知アイコンをクリックして、復元ジョブの状態を確認します。

   ![復元ジョブの進行状況](./media/long-term-backup-retention-configure/restore-job-progress-long-term.png)

1. 復元ジョブが完了したら、 **[マネージド インスタンスの概要]** ページを開き、新しく復元されたデータベースを確認します。

> [!NOTE]
> ここから、SQL Server Management Studio を使用して、復元されたデータベースに接続し、必要なタスクを実行できます。たとえば、[復元されたデータベースからデータを少し抽出して既存のデータベースにコピーしたり、既存のデータベースを削除し、復元されたデータベースの名前を既存のデータベース名に変更したり](../database/recovery-using-backups.md#point-in-time-restore)できます。


## <a name="using-powershell"></a>PowerShell の使用
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> PowerShell Azure Resource Manager モジュールは Azure SQL Database で引き続きサポートされますが、今後の開発は Az.Sql モジュールで行われます。 これらのコマンドレットについては、「[AzureRM.Sql](/powershell/module/AzureRM.Sql/)」を参照してください。 Az モジュールと AzureRm モジュールのコマンドの引数は実質的に同じです。

以下のセクションでは、PowerShell を使用して長期的なバックアップ保有期間を構成し、Azure ストレージ内のバックアップを表示し、Azure ストレージ内のバックアップから復元する方法について説明します。

### <a name="azure-rbac-roles-to-manage-long-term-retention"></a>長期保有を管理するための Azure RBAC ロール

**Get-AzSqlInstanceDatabaseLongTermRetentionBackup** および **Restore-AzSqlInstanceDatabase** の場合は、次のいずれかのロールが必要です。

- サブスクリプションの所有者ロール、または
- マネージド インスタンス共同作成者ロール、または
- 次のアクセス許可を持つカスタム ロール:
  - `Microsoft.Sql/locations/longTermRetentionManagedInstanceBackups/read`
  - `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionManagedInstanceBackups/read`
  - `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/read`

**Remove-AzSqlInstanceDatabaseLongTermRetentionBackup** の場合は、次のいずれかのロールが必要です。

- サブスクリプションの所有者ロール、または
- 次のアクセス許可を持つカスタム ロール:
  - `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/delete`

> [!NOTE]
> マネージド インスタンス共同作成者ロールには、LTR バックアップを削除するアクセス許可がありません。

Azure RBAC のアクセス許可は、"*サブスクリプション*" または "*リソース グループ*" のいずれかのスコープで付与できます。 ただし、削除されたインスタンスに属する LTR バックアップにアクセスするには、そのインスタンスの "*サブスクリプション*" スコープにアクセス許可を付与する必要があります。

- `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/delete`

### <a name="create-an-ltr-policy"></a>LTR ポリシーを作成する

```powershell
# get the Managed Instance
$subId = "<subscriptionId>"
$instanceName = "<instanceName>"
$resourceGroup = "<resourceGroupName>"
$dbName = "<databaseName>"

Connect-AzAccount

Select-AzSubscription -SubscriptionId $subId

$instance = Get-AzSqlInstance -Name $instanceName -ResourceGroupName $resourceGroup

# create LTR policy with WeeklyRetention = 12 weeks. MonthlyRetention and YearlyRetention = 0 by default.
$LTRPolicy = @{
    InstanceName = $instanceName 
    DatabaseName = $dbName 
    ResourceGroupName = $resourceGroup 
    WeeklyRetention = 'P12W'
}
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy @LTRPolicy

# create LTR policy with WeeklyRetention = 12 weeks, YearlyRetention = 5 years and WeekOfYear = 16 (week of April 15). MonthlyRetention = 0 by default.
$LTRPolicy = @{
    InstanceName = $instanceName 
    DatabaseName = $dbName 
    ResourceGroupName = $resourceGroup 
    WeeklyRetention = 'P12W' 
    YearlyRetention = 'P5Y' 
    WeekOfYear = '16'
}
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy @LTRPolicy
```

### <a name="view-ltr-policies"></a>LTR ポリシーを表示する

次の例は、1 つのデータベースのインスタンス内の LTR ポリシーを一覧表示する方法を示しています

```powershell
# gets the current version of LTR policy for a database
$LTRPolicies = @{
    InstanceName = $instanceName 
    DatabaseName = $dbName 
    ResourceGroupName = $resourceGroup
}
Get-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy @LTRPolicy 
```

次の例は、1 つのインスタンス上にあるすべてのデータベースの LTR ポリシーを一覧表示する方法を示しています

```powershell
# gets the current version of LTR policy for all of the databases on an instance

$Databases = Get-AzSqlInstanceDatabase -ResourceGroupName $resourceGroup -InstanceName $instanceName

$LTRParams = @{
    InstanceName = $instanceName
    ResourceGroupName = $resourceGroup
}

foreach($database in $Databases.Name){
    Get-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy @LTRParams  -DatabaseName $database
 }
```

### <a name="clear-an-ltr-policy"></a>LTR ポリシーをクリアする

LTR ポリシーをデータベースから消去する例を次に示します

```powershell
# remove the LTR policy from a database
$LTRPolicy = @{
    InstanceName = $instanceName 
    DatabaseName = $dbName 
    ResourceGroupName = $resourceGroup 
    RemovePolicy = $true
}
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy @LTRPolicy
```

### <a name="view-ltr-backups"></a>LTR バックアップを表示する

次の例は、インスタンス内の LTR バックアップを一覧表示する方法を示しています。

```powershell

$instance = Get-AzSqlInstance -Name $instanceName -ResourceGroupName $resourceGroup

# get the list of all LTR backups in a specific Azure region
# backups are grouped by the logical database id, within each group they are ordered by the timestamp, the earliest backup first
Get-AzSqlInstanceDatabaseLongTermRetentionBackup -Location $instance.Location

# get the list of LTR backups from the Azure region under the given managed instance
$LTRBackupParam = @{
    Location = $instance.Location 
    InstanceName = $instanceName
}
Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam

# get the LTR backups for a specific database from the Azure region under the given managed instance
$LTRBackupParam = @{
    Location = $instance.Location 
    InstanceName = $instanceName
    DatabaseName = $dbName
}
Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam

# list LTR backups only from live databases (you have option to choose All/Live/Deleted)
$LTRBackupParam = @{
    Location = $instance.Location 
    DatabaseState = 'Live'
}
Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam

# only list the latest LTR backup for each database
$LTRBackupParam = @{
    Location = $instance.Location 
    InstanceName = $instanceName
    OnlyLatestPerDatabase = $true
}
Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam 
```

### <a name="delete-ltr-backups"></a>LTR バックアップを削除する

バックアップの一覧から LTR バックアップを削除する例を次に示します。

```powershell
# remove the earliest backup
# get the LTR backups for a specific database from the Azure region under the given managed instance
$LTRBackupParam = @{
    Location = $instance.Location 
    InstanceName = $instanceName
    DatabaseName = $dbName
}
$ltrBackups = Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam
$ltrBackup = $ltrBackups[0]
Remove-AzSqlInstanceDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```

> [!IMPORTANT]
> LTR バックアップの削除は、元に戻せません。 インスタンスが削除された後に LTR バックアップを削除するには、サブスクリプション スコープのアクセス許可が必要です。 Azure Monitor では、"長期保有バックアップを削除します" という操作をフィルター処理することで、それぞれの削除に関する通知を設定できます。 アクティビティ ログには、どのユーザーがいつ要求を行ったかに関する情報が含まれています。 詳しい手順については、[アクティビティ ログ アラートの作成](../../azure-monitor/alerts/alerts-activity-log.md)に関するページを参照してください。

### <a name="restore-from-ltr-backups"></a>LTR バックアップから復元する

LTR バックアップから復元する例を次に示します。 このインターフェイスは変更されませんでしたが、リソース ID パラメーターでは LTR バックアップ リソース ID が必須になりました。

```powershell
# restore a specific LTR backup as an P1 database on the instance $instanceName of the resource group $resourceGroup
$LTRBackupParam = @{
    Location = $instance.Location 
    InstanceName = $instanceName
    DatabaseName = $dbname
    OnlyLatestPerDatabase = $true
}
$ltrBackup = Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam 

$RestoreLTRParam = @{
    TargetInstanceName          = $instanceName 
    TargetResourceGroupName     = $resourceGroup 
    TargetInstanceDatabaseName  = $dbName
    FromLongTermRetentionBackup = $true
    ResourceId                  = $ltrBackup.ResourceId 
}
Restore-AzSqlInstanceDatabase @RestoreLTRParam
```

> [!IMPORTANT]
> インスタンスが削除された後に LTR バックアップから復元するには、インスタンスのサブスクリプションをスコープとしたアクセス許可が必要であり、そのサブスクリプションがアクティブである必要があります。 また、オプションの -ResourceGroupName パラメーターを省略する必要もあります。

> [!NOTE]
> ここから、SQL Server Management Studio を使用して、復元されたデータベースに接続し、必要なタスクを実行できます。たとえば、復元されたデータベースからデータを少し抽出して既存のデータベースにコピーしたり、既存のデータベースを削除し、復元されたデータベースの名前を既存のデータベース名に変更したりできます。 [ポイントインタイム リストア](../database/recovery-using-backups.md#point-in-time-restore)をご覧ください。

## <a name="next-steps"></a>次のステップ

- サービスによって生成された自動バックアップについては、[自動バックアップ](../database/automated-backups-overview.md)に関する記事を参照してください。
- バックアップの長期保存については、[バックアップの長期保存](../database/long-term-retention-overview.md)に関する記事を参照してください。
