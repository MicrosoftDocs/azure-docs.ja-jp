---
title: "PowerShell: Azure SQL データベースのバックアップと復元 | Microsoft Docs"
description: "このチュートリアルでは、PowerShell を使用して自動バックアップから特定の時点に復元する方法、自動バックアップを Azure Recovery Services コンテナーに保存する方法、Azure Recovery Services コンテナーから復元する方法について説明します。"
keywords: "SQL データベース チュートリアル"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: tutorial
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 12/19/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: 85897322bc341f549c52dbe4758e8c7f860df22c
ms.lasthandoff: 03/10/2017


---


# <a name="tutorial-back-up-and-restore-an-azure-sql-database-using-powershell"></a>チュートリアル: PowerShell を使用した Azure SQL データベースのバックアップと復元

このチュートリアルでは、Azure PowerShell を使用して次のことを行う方法を説明します。

- データベースの既存のバックアップを表示する
- データベースを以前の状態に復元する
- Azure Recovery Services コンテナーのデータベース バックアップ ファイルの長期的な保有期間を構成する
- Azure Recovery Services コンテナーからバックアップを復元する

**推定所要時間**: このチュートリアルの完了には約 30 分かかります (既に前提条件を満たしていることが前提です)。


## <a name="prerequisites"></a>前提条件

* **Azure アカウント**。 Azure アカウントが必要です。 [無料の Azure アカウントを作成する](https://azure.microsoft.com/free/)か、[Visual Studio サブスクライバーの特典を有効にする](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/)ことができます。 

* **Azure の作成のアクセス許可**。 サブスクリプションの所有者または共同作成者ロールのメンバーであるアカウントを使用して Azure Portal に接続できることが必要です。 ロールベースのアクセス制御 (RBAC) の詳細については、「[Azure Portal でのアクセス管理の概要](../active-directory/role-based-access-control-what-is.md)」を参照してください。

* **PowerShell**。最新の Azure PowerShell がインストールされ、実行されている必要があります。 詳細については、「 [Azure PowerShell のインストールと構成の方法](/powershell/azureps-cmdlets-docs)」をご覧ください。

* **SQL Server Management Studio**。 最新バージョンの SQL Server Management Studio (SSMS) は、「[SQL Server Management Studio (SSMS) のダウンロード](https://msdn.microsoft.com/library/mt238290.aspx)」からダウンロードしてインストールすることができます。 新機能が継続的にリリースされているため、Azure SQL Database に接続する場合は、常に最新バージョンの SSMS を使用してください。

* **基本サーバーおよびデータベース**。このチュートリアルで使用するサーバーと&2; つのデータベースをインストールして構成するには、**[Deploy to Azure (Azure へのデプロイ)]** ボタンをクリックします。 ボタンをクリックすると **[Deploy from a template (テンプレートからのデプロイ)]** ブレードが開くので、新しいリソース グループを作成し、作成予定の新しいサーバーの **[管理者ログイン パスワード]** を指定します。

   [![ダウンロード](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fsqldbtutorial.blob.core.windows.net%2Ftemplates%2Fsqldbgetstarted.json)

> [!TIP]
> これらのタスクは、入門用チュートリアルで [Azure Portal](sql-database-get-started-backup-recovery-portal.md) を使用して実行することができます。

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="view-the-oldest-restore-point-from-the-service-generated-backups-of-a-database"></a>サービスによって生成されたデータベース バックアップから最も古い復元ポイントを確認する

チュートリアルのこのセクションでは、データベースの[サービスによって生成された自動バックアップ](sql-database-automated-backups.md)から最も古い復元ポイントに関する情報を確認します。 

データベースは、最初の復元ポイントと最新の利用可能なバックアップ (現在時刻から&6; 分前) の間の任意の時点に復元できます。 

次のスニペットでは、[Get-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/get-azurermsqldatabase) コマンドレットを使用して、復元するデータベースの最初の復元ポイントを取得します。 時刻は UTC で返されますが、次のスニペットでは、ローカル時刻で処理する方法を示します。 ライブ データベースの最新の利用可能な復元ポイントは通常、約&6; 分前となるため、最新の復元ポイントは単純に現在時刻から&6; 分前に設定されます。 

```
# Get available restore points

$resourceGroupName = "{resource-group-name}"
$serverName = "{server-name}"
$databaseName = "{database-name}"

$databaseToRestore = Get-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName

$earliestRestorePoint = $databaseToRestore.EarliestRestoreDate.ToLocalTime()
$latestRestorePoint = (Get-Date).AddMinutes(-6).ToLocalTime()

Write-Host "'$databaseName' on '$serverName' can be restored to any point-in-time between '$earliestRestorePoint' thru '$latestRestorePoint'"
```



## <a name="restore-a-database-to-a-previous-point-in-time"></a>データベースを以前の状態に復元する

チュートリアルのこのセクションでは、特定の時点のデータベースを新しいデータベースに復元します。 

>[!NOTE]
>特定の時点への復元の対象となるサーバーを変更できません。 別のサーバーに復元するには、[geo リストア](sql-database-disaster-recovery.md#recover-using-geo-restore)を使用します。 また、[エラスティック データベース プール](sql-database-elastic-jobs-overview.md)または別の価格レベルに復元できることに注意してください。 

次のスニペットでは、[Restore-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/restore-azurermsqldatabase) コマンドレットを使用して、前のスニペットで設定した $databaseToRestore を復元します。 **-PointInTime** パラメーターには、*12/09/2016 20:00:00* のような UTC 形式の時刻値が必要です。 このスニペットによって、ローカル時刻が変換されます。

```
# Restore a database to a previous point in time

#$resourceGroupName = {resource-group-name}
#$serverName = {server-name}
$newRestoredDatabaseName = "{new-database-name}"
$localTimeToRestoreTo = "{12/9/2016 12:00:00 PM}" # change to a valid restore point for your database
$restorePointInTime = (Get-Date $localTimeToRestoreTo).ToUniversalTime()
$newDatabaseEdition = "Basic"
$newDatabaseServiceLevel = "Basic"

Write-Host "Restoring database '$databaseName' to its state at:"(Get-Date $restorePointInTime).ToLocalTime()", to a new database named '$newRestoredDatabaseName'."

$restoredDb = Restore-AzureRmSqlDatabase -FromPointInTimeBackup -PointInTime $restorePointInTime -ResourceGroupName $resourceGroupName `
 -ServerName $serverName -TargetDatabaseName $newRestoredDatabaseName -Edition $newDatabaseEdition -ServiceObjectiveName $newDatabaseServiceLevel `
 -ResourceId $databaseToRestore.ResourceID

$restoredDb
```

> [!NOTE]
> ここから、[SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) を使用して、復元されたデータベースに接続し、必要なタスクを実行できます。たとえば、[復元されたデータベースからデータを少し抽出して既存のデータベースにコピーしたり、既存のデータベースを削除し、復元されたデータベースの名前を既存のデータベース名に変更したり](sql-database-recovery-using-backups.md#point-in-time-restore)できます。

## <a name="configure-long-term-retention-of-automated-backups-in-an-azure-recovery-services-vault"></a>Azure Recovery Services コンテナーの自動バックアップの長期的な保有期間を構成する 

チュートリアルのこのセクションでは、ご利用のサービス レベルの保有期間より長い間 (最大 10 年間)、[自動バックアップを保有するよう Azure Recovery Services コンテナーを構成](sql-database-long-term-retention.md)します。 


> [!TIP]
> 長期的なリテンション期間のバックアップを削除するには、[PowerShell を使った長期的なバックアップ リテンション期間の管理](sql-database-manage-long-term-backup-retention-powershell.md)に関する記事をご覧ください。


### <a name="create-a-recovery-services-vault"></a>Recovery Services コンテナーを作成する

> [!IMPORTANT]
> コンテナーは、Azure SQL 論理サーバーと同じリージョンに配置する必要があります。また、論理サーバーと同じリソース グループを使用する必要があります。

次のスニペットでは、[New-AzureRmRecoveryServicesVault](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices/v2.3.0/new-azurermrecoveryservicesvault) コマンドレットと [Set-AzureRmRecoveryServicesBackupProperties](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices/v2.3.0/set-azurermrecoveryservicesbackupproperties) コマンドレットを使用して、新しい Recovery Services コンテナーを作成し、そのコンテナー内のバックアップの冗長性レベルを設定します。 

```
# Create a recovery services vault

#$resourceGroupName = "{resource-group-name}"
#$serverName = "{server-name}"
$serverLocation = (Get-AzureRmSqlServer -ServerName $serverName -ResourceGroupName $resourceGroupName).Location
$recoveryServiceVaultName = "{new-vault-name}"

$vault = New-AzureRmRecoveryServicesVault -Name $recoveryServiceVaultName -ResourceGroupName $ResourceGroupName -Location $serverLocation 
Set-AzureRmRecoveryServicesBackupProperties -BackupStorageRedundancy LocallyRedundant -Vault $vault
```

### <a name="set-your-server-to-use-the-recovery-vault-you-just-created-for-its-long-term-retention-backups"></a>長期保存のバックアップ向けに作成した Recovery コンテナーを使用するようサーバーを設定する

次のスニペットでは、[Set-AzureRmSqlServerBackupLongTermRetentionVault](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/set-azurermsqlserverbackuplongtermretentionvault) コマンドレットを使用して、以前に作成された Recovery Services コンテナーを特定の Azure SQL サーバーに関連付けます。

```
# Set your server to use the vault to for long-term backup retention 

Set-AzureRmSqlServerBackupLongTermRetentionVault -ResourceGroupName $resourceGroupName -ServerName $serverName -ResourceId $vault.Id
```

### <a name="create-a-retention-policy"></a>保持ポリシーを作成する

保持ポリシーで、データベースのバックアップの保存期間を設定できます。 

次のスニペットでは、[Get-AzureRmRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/get-azurermrecoveryservicesbackupretentionpolicyobject) を使用して、新しいポリシーを作成するためのテンプレートとして使用する既定の保持ポリシーを取得します。 バックアップを 2 年間保持するようテンプレートを設定した後、[New-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/new-azurermrecoveryservicesbackupprotectionpolicy) を実行して、最終的に新しいポリシーを作成します。 

一部のコマンドレットでは、実行する前にコンテナーのコンテキストを設定する必要があるため ([Set-AzureRmRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices/v2.3.0/set-azurermrecoveryservicesvaultcontext))、このコマンドレットは関連するいくつかのスニペットに見られます。 ポリシーはコンテナーに含まれるため、コンテキストが設定されます。 コンテナーごとに複数の保持ポリシーを作成した後に、目的のポリシーを特定のデータベースに適用することができます。 


```
# Retrieve the default retention policy for the AzureSQLDatabase workload type
$retentionPolicy = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType AzureSQLDatabase

# Set the retention value to two years (you can set to any time between 1 week and 10 years)
$retentionPolicy.RetentionDurationType = "Years"
$retentionPolicy.RetentionCount = 2
$retentionPolicyName = "my2YearRetentionPolicy"

# Set the vault context to the vault you are creating the policy for
Set-AzureRmRecoveryServicesVaultContext -Vault $vault

# Create the new policy
$policy = New-AzureRmRecoveryServicesBackupProtectionPolicy -name $retentionPolicyName -WorkloadType AzureSQLDatabase -retentionPolicy $retentionPolicy
$policy
```

### <a name="configure-a-database-to-use-the-previously-defined-retention-policy"></a>以前に定義した保持ポリシーを使用するデータベースを構成する

次のスニペットでは、[Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/set-azurermsqldatabasebackuplongtermretentionpolicy) コマンドレットを使用して、新しいポリシーを特定のデータベースに適用します。

```
# Enable long-term retention for a specific SQL database
$policyState = "enabled"
Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -State $policyState -ResourceId $policy.Id
```

> [!IMPORTANT]
> 構成が完了すると、その後&7; 日以内に、コンテナーにバックアップが表示されます。 バックアップがコンテナーに表示されたら、このチュートリアルを先に進めてください。

## <a name="view-backup-info-and-backups-in-long-term-retention"></a>バックアップ情報と、長期的な保有期間内のバックアップを確認する

チュートリアルのこのセクションでは、[長期的なバックアップ保有期間](sql-database-long-term-retention.md)内のデータベース バックアップに関する情報を確認します。 

次のスニペットでは、[Get-AzureRmRecoveryServicesBackupContainer](https://docs.microsoft.com/en-us/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/get-azurermrecoveryservicesbackupcontainer)、[Get-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/en-us/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/get-azurermrecoveryservicesbackupitem)、[Get-AzureRmRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/en-us/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/get-azurermrecoveryservicesbackuprecoverypoint) コマンドレットを使用して、コンテナーの情報を照会します。

```
#$resourceGroupName = "{resource-group-name}"
#$serverName = "{server-name}"
$databaseNeedingRestore = $databaseName

# Set the vault context to the vault we want to restore from
#$vault = Get-AzureRmRecoveryServicesVault -ResourceGroupName $resourceGroupName
Set-AzureRmRecoveryServicesVaultContext -Vault $vault

# the following commands find the container associated with the server 'myserver' under resource group 'myresourcegroup'
$container = Get-AzureRmRecoveryServicesBackupContainer -ContainerType AzureSQL -FriendlyName $vault.Name

# Get the long-term retention metadata associated with a specific database
$item = Get-AzureRmRecoveryServicesBackupItem -Container $container -WorkloadType AzureSQLDatabase -Name $databaseNeedingRestore


# Get all available backups for the previously indicated database
# Optionally, set the -StartDate and -EndDate parameters to return backups within a specific time period
$availableBackups = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $item
$availableBackups
```


## <a name="restore-a-database-from-a-backup-in-long-term-backup-retention"></a>長期的なバックアップ保有期間内のバックアップからデータベースを復元する

チュートリアルのこのセクションでは、データベースを Azure Recovery Services コンテナーにあるバックアップから新しいデータベースに復元します。

このチュートリアルに既に出てきた、特定の時点に関するスニペットと同様に、長期的なバックアップ保有期間からの復元には [Restore-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/restore-azurermsqldatabase) コマンドレットを使用します。ただし、今回は **-FromLongTermRetentionBackup** パラメーターを使用します (以前に使用した **-FromPointInTimeBackup** パラメーターではありません)。

```
# Restore the most recent backup: $availableBackups[0]
#$resourceGroupName = "{resource-group-name}"
#$serverName = "{server-name}"
$restoredDatabaseName = "{new-database-name}"
$edition = "Basic"
$performanceLevel = "Basic"

$restoredDb = Restore-AzureRmSqlDatabase -FromLongTermRetentionBackup -ResourceId $availableBackups[0].Id -ResourceGroupName $resourceGroupName `
 -ServerName $serverName -TargetDatabaseName $restoredDatabaseName -Edition $edition -ServiceObjectiveName $performanceLevel
$restoredDb
```


> [!NOTE]
> ここから、SQL Server Management Studio を使用して、復元されたデータベースに接続し、必要なタスクを実行できます。たとえば、[復元されたデータベースからデータを少し抽出して既存のデータベースにコピーしたり、既存のデータベースを削除し、復元されたデータベースの名前を既存のデータベース名に変更したり](sql-database-recovery-using-backups.md#point-in-time-restore)できます。

## <a name="complete-azure-powershell-script-to-restore-from-a-previous-point-in-time-and-to-configure-and-restore-from-long-term-backup-retention-using-powershell"></a>PowerShell を使用して以前の状態から復元し、長期的なバックアップ保有期間を構成してそこから復元するための完全な Azure PowerShell スクリプト

> [!NOTE]
> このスクリプトの最後で最新のバックアップを復元しようとするときに、コンテナーにバックアップが存在しない場合は、"*null 配列にインデックスを付けることはできません*" という例外が表示されます。

```
# Sign in to Azure and set the subscription to work with
########################################################

$subscriptionId = "{subscription-id}"

Add-AzureRmAccount
Set-AzureRmContext -SubscriptionId $subscriptionId


# User variables
################

$myResourceGroupName = "{resource-group-name}"
$myServerName = "{server-name}"
$myDatabaseToRestoreFromPointInTime = "{database-name}"
$myLocalTimeToRestoreTo = "{12/08/2016 16:00:00}" # change to a valid restore point for your database
$myNewDatabaseRestoredFromPointInTime = "{new-database-name}"

$myRecoveryServiceVaultName = "{vault-name}"
$myRetentionPolicyDurationType = "{duration-period}" # set to Years, Months, or Weeks
$myRetentionPolicyDuration = {2}
$myRetentionPolicyName = "{retention-policy-name}"
$myDatabaseToRestoreFromLTR = "{database-name}"
$myNewDatabaseRestoredFromLTR = "{new-database-name}"


# Get available restore points for a specific database
######################################################

$resourceGroupName = $myResourceGroupName
$serverName = $myServerName
$databaseName = $myDatabaseToRestoreFromPointInTime


$databaseToRestore = Get-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName

$earliestRestorePoint = $databaseToRestore.EarliestRestoreDate.ToLocalTime()
$latestRestorePoint = (Get-Date).AddMinutes(-6).ToLocalTime()

Write-Host "'$databaseName' on '$serverName' can be restored to any point-in-time between '$earliestRestorePoint' thru '$latestRestorePoint'"


# Restore a database to a previous point in time
################################################

$newRestoredDatabaseName = $myNewDatabaseRestoredFromPointInTime
$localTimeToRestoreTo = $myLocalTimeToRestoreTo
$restorePointInTime = (Get-Date $localTimeToRestoreTo).ToUniversalTime()
$newDatabaseEdition = "Basic"
$newDatabaseServiceLevel = "Basic"

Write-Host "Restoring database '$databaseName' to its state at:"(Get-Date $restorePointInTime).ToLocalTime()", to a new database named '$newRestoredDatabaseName'."

$restoredDb = Restore-AzureRmSqlDatabase -FromPointInTimeBackup -PointInTime $restorePointInTime -ResourceGroupName $resourceGroupName `
 -ServerName $serverName -TargetDatabaseName $newRestoredDatabaseName -Edition $newDatabaseEdition -ServiceObjectiveName $newDatabaseServiceLevel `
 -ResourceId $databaseToRestore.ResourceID

$restoredDb



# CONFIGURE LONG-TERM RETENTION

# Create a recovery services vault
##################################

$resourceGroupName = $myResourceGroupName
$serverName = $myServerName
$serverLocation = (Get-AzureRmSqlServer -ServerName $serverName -ResourceGroupName $resourceGroupName).Location
$recoveryServiceVaultName = $myRecoveryServiceVaultName

$vault = New-AzureRmRecoveryServicesVault -Name $recoveryServiceVaultName -ResourceGroupName $resourceGroupName -Location $serverLocation 
Set-AzureRmRecoveryServicesBackupProperties -BackupStorageRedundancy LocallyRedundant -Vault $vault
$vault

Set-AzureRmSqlServerBackupLongTermRetentionVault -ResourceGroupName $resourceGroupName -ServerName $serverName -ResourceId $vault.Id

# Retrieve the default retention policy for the AzureSQLDatabase workload type
##############################################################################

$retentionPolicy = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType AzureSQLDatabase

# Set the retention values
$retentionPolicy.RetentionDurationType = $myRetentionPolicyDurationType
$retentionPolicy.RetentionCount = $myRetentionPolicyDuration

$retentionPolicyName = $myRetentionPolicyName

# Set the vault context to the vault you are creating the policy for
Set-AzureRmRecoveryServicesVaultContext -Vault $vault

# Create the new policy
$policy = New-AzureRmRecoveryServicesBackupProtectionPolicy -name $retentionPolicyName -WorkloadType AzureSQLDatabase -retentionPolicy $retentionPolicy
$policy

$policyState = "enabled"
Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -State $policyState -ResourceId $policy.Id


$databaseNeedingRestore = $myDatabaseToRestoreFromLTR

# Set the vault context to the vault we want to restore from
Set-AzureRmRecoveryServicesVaultContext -Vault $vault

# Get the container associated with your vault
$container = Get-AzureRmRecoveryServicesBackupContainer -ContainerType AzureSQL -FriendlyName $vault.Name

# Get the long-term retention metadata associated with a specific database
$item = Get-AzureRmRecoveryServicesBackupItem -Container $container -WorkloadType AzureSQLDatabase -Name $databaseNeedingRestore


# Get all available backups for the previously indicated database
# Optionally, set the -StartDate and -EndDate parameters to return backups within a specific time period
$availableBackups = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $item
$availableBackups
###

# This command restores the most recent backup: $availableBackups[0]
$resourceGroupName = $myResourceGroupName
$serverName = $myServerName
$restoredDatabaseName = $myNewDatabaseRestoredFromLTR
$edition = "Basic"
$performanceLevel = "Basic"

$restoredDbFromLtr = Restore-AzureRmSqlDatabase -FromLongTermRetentionBackup -ResourceId $availableBackups[0].Id -ResourceGroupName $resourceGroupName `
 -ServerName $serverName -TargetDatabaseName $restoredDatabaseName -Edition $edition -ServiceObjectiveName $performanceLevel

$restoredDbFromLtr
```

## <a name="next-steps"></a>次のステップ

- サービスによって生成された自動バックアップについては、[自動バックアップ](sql-database-automated-backups.md)に関する記事を参照してください。
- バックアップの長期保存については、[バックアップの長期保存](sql-database-long-term-retention.md)に関する記事を参照してください。
- バックアップからの復元については、[バックアップからの復元](sql-database-recovery-using-backups.md)に関する記事を参照してください。

