---
title: Azure SQL Managed Instance:長期的なバックアップ保有期間 (PowerShell)
description: PowerShell を使用して、Azure SQL Managed Instance の個別の Azure BLOB ストレージ コンテナーに自動バックアップを格納して復元する方法について説明します。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 04/29/2020
ms.openlocfilehash: ab4eaf5ad40b5ef8bee68ef0e56ab8f53db8a8a2
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2020
ms.locfileid: "87533837"
---
# <a name="manage-azure-sql-managed-instance-long-term-backup-retention-powershell"></a>Azure SQL Managed Instance の長期的なバックアップ保有期間を管理する (PowerShell)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL Managed Instance では、[長期的なバックアップ保有期間](../database/long-term-retention-overview.md#sql-managed-instance-support)ポリシー (LTR) を、限定されたパブリック プレビュー機能として構成できます。 これにより、データベースのバックアップを個別の Azure BLOB ストレージ コンテナーに最大 10 年間自動的に保持できます。 そして、PowerShell でこれらのバックアップを使用して、データベースを復旧できます。

   > [!IMPORTANT]
   > 現在、マネージド インスタンスの LTR は限定プレビュー段階にあり、個々のケースに応じて EA および CSP サブスクリプションで使用できます。 登録を要求するには、[Azure サポート チケット](https://azure.microsoft.com/support/create-ticket/)を作成してください。 [問題の種類] については [技術的な問題] を選択し、[サービス] には [SQL Database Managed Instance] を選択し、[問題の種類] には **[Backup, Restore, and Business Continuity/Long-term backup retention]\(バックアップ、復元、およびビジネス継続性/長期的なバックアップ保有期間\)** を選択します。 要求に、マネージド インスタンスの LTR の限定パブリック プレビューに登録したい旨を明記してください。

以下のセクションでは、PowerShell を使用して長期的なバックアップ保有期間を構成し、Azure SQL ストレージ内のバックアップを表示し、Azure SQL ストレージ内のバックアップから復元する方法について説明します。

## <a name="azure-roles-to-manage-long-term-retention"></a>長期保有を管理するための Azure ロール

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

RBAC のアクセス許可は、*サブスクリプション*または*リソース グループ*のいずれかのスコープで付与できます。 ただし、削除されたインスタンスに属する LTR バックアップにアクセスするには、そのインスタンスの "*サブスクリプション*" スコープにアクセス許可を付与する必要があります。

- `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/delete`

## <a name="create-an-ltr-policy"></a>LTR ポリシーを作成する

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
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy -InstanceName $instanceName `
   -DatabaseName $dbName -ResourceGroupName $resourceGroup -WeeklyRetention P12W

# create LTR policy with WeeklyRetention = 12 weeks, YearlyRetention = 5 years and WeekOfYear = 16 (week of April 15). MonthlyRetention = 0 by default.
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy -InstanceName $instanceName `
    -DatabaseName $dbName -ResourceGroupName $resourceGroup -WeeklyRetention P12W -YearlyRetention P5Y -WeekOfYear 16
```

## <a name="view-ltr-policies"></a>LTR ポリシーを表示する

次の例は、インスタンス内の LTR ポリシーを一覧表示する方法を示しています

```powershell
# gets the current version of LTR policy for the database
$ltrPolicies = Get-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy -InstanceName $instanceName `
    -DatabaseName $dbName -ResourceGroupName $resourceGroup
```

## <a name="clear-an-ltr-policy"></a>LTR ポリシーをクリアする

LTR ポリシーをデータベースから消去する例を次に示します

```powershell
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy -InstanceName $instanceName `
   -DatabaseName $dbName -ResourceGroupName $resourceGroup -RemovePolicy
```

## <a name="view-ltr-backups"></a>LTR バックアップを表示する

次の例は、インスタンス内の LTR バックアップを一覧表示する方法を示しています。

```powershell
# get the list of all LTR backups in a specific Azure region
# backups are grouped by the logical database id, within each group they are ordered by the timestamp, the earliest backup first
$ltrBackups = Get-AzSqlInstanceDatabaseLongTermRetentionBackup -Location $instance.Location

# get the list of LTR backups from the Azure region under the given managed instance
$ltrBackups = Get-AzSqlInstanceDatabaseLongTermRetentionBackup -Location $instance.Location -InstanceName $instanceName

# get the LTR backups for a specific database from the Azure region under the given managed instance
$ltrBackups = Get-AzSqlInstanceDatabaseLongTermRetentionBackup -Location $instance.Location -InstanceName $instanceName -DatabaseName $dbName

# list LTR backups only from live databases (you have option to choose All/Live/Deleted)
$ltrBackups = Get-AzSqlInstanceDatabaseLongTermRetentionBackup -Location $instance.Location -DatabaseState Live

# only list the latest LTR backup for each database
$ltrBackups = Get-AzSqlInstanceDatabaseLongTermRetentionBackup -Location $instance.Location -InstanceName $instanceName -OnlyLatestPerDatabase
```

## <a name="delete-ltr-backups"></a>LTR バックアップを削除する

バックアップの一覧から LTR バックアップを削除する例を次に示します。

```powershell
# remove the earliest backup
$ltrBackup = $ltrBackups[0]
Remove-AzSqlInstanceDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```

> [!IMPORTANT]
> LTR バックアップの削除は、元に戻せません。 インスタンスが削除された後に LTR バックアップを削除するには、サブスクリプション スコープのアクセス許可が必要です。 Azure Monitor では、"長期保有バックアップを削除します" という操作をフィルター処理することで、それぞれの削除に関する通知を設定できます。 アクティビティ ログには、どのユーザーがいつ要求を行ったかに関する情報が含まれています。 詳しい手順については、[アクティビティ ログ アラートの作成](../../azure-monitor/platform/alerts-activity-log.md)に関するページを参照してください。

## <a name="restore-from-ltr-backups"></a>LTR バックアップから復元する

LTR バックアップから復元する例を次に示します。 このインターフェイスは変更されませんでしたが、リソース ID パラメーターでは LTR バックアップ リソース ID が必須になりました。

```powershell
# restore a specific LTR backup as an P1 database on the instance $instanceName of the resource group $resourceGroup
Restore-AzSqlInstanceDatabase -FromLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId `
   -TargetInstanceName $instanceName -TargetResourceGroupName $resourceGroup -TargetInstanceDatabaseName $dbName
```

> [!IMPORTANT]
> インスタンスが削除された後に LTR バックアップから復元するには、インスタンスのサブスクリプションをスコープとしたアクセス許可が必要であり、そのサブスクリプションがアクティブである必要があります。 また、オプションの -ResourceGroupName パラメーターを省略する必要もあります。

> [!NOTE]
> ここから、SQL Server Management Studio を使用して、復元されたデータベースに接続し、必要なタスクを実行できます。たとえば、復元されたデータベースからデータを少し抽出して既存のデータベースにコピーしたり、既存のデータベースを削除し、復元されたデータベースの名前を既存のデータベース名に変更したりできます。 [ポイントインタイム リストア](../database/recovery-using-backups.md#point-in-time-restore)をご覧ください。

## <a name="next-steps"></a>次のステップ

- サービスによって生成された自動バックアップについては、[自動バックアップ](../database/automated-backups-overview.md)に関する記事を参照してください。
- バックアップの長期保存については、[バックアップの長期保存](../database/long-term-retention-overview.md)に関する記事を参照してください。
