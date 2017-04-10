---
title: "PowerShell: 長期的なデータベース バックアップ保有期間を管理する (Azure) | Microsoft Docs"
description: "PowerShell を使って、Azure Recovery Services コンテナー内の自動化された Azure SQL Database バックアップの長期的な保有期間を構成、管理、および復元する方法についてのクイック リファレンスです"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
ms.date: 12/22/2016
ms.author: carlrab; sstein
translationtype: Human Translation
ms.sourcegitcommit: f234e50d194c1b8b8deed8282a6ab1250b53d075
ms.openlocfilehash: c0f073578ecf067d115a143edf4e797c4e8b5200
ms.lasthandoff: 02/16/2017


---
# <a name="configure-long-term-retention-of-database-backups-in-an-azure-recovery-services-vault-using-powershell"></a>PowerShell を使って Azure Recovery Services コンテナーのデータベース バックアップの長期的な保有期間を構成する
このトピックでは、PowerShell を使って Azure Recovery Services コンテナー内の自動バックアップの長期的な保有期間を構成、管理、および復元する方法について説明します。 このタスクは、[Azure Portal](sql-database-manage-long-term-backup-retention-portal.md) を使って実行することもできます。

長期的なバックアップ保有期間について詳しくは、「[長期のバックアップ リテンション期間](sql-database-long-term-retention.md)」をご覧ください。

> [!TIP]
> チュートリアルについては、「[PowerShell を使用したデータの保護と回復のためのバックアップと復元の概要](sql-database-get-started-backup-recovery-powershell.md)」をご覧ください。
>

## <a name="configure-long-term-backup-retention-using-powershell"></a>PowerShell を使用した長期のバックアップ リテンション期間の構成

長期的な保有期間を構成するには、最新の [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) を使用して、次の手順を実行する必要があります。

1. SQL Database サーバーと同じリージョン、サブスクリプション、およびリソース グループで Azure Recovery Services コンテナーを作成します。 ([New-AzureRmRecoveryServicesVault](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices/v2.3.0/new-azurermrecoveryservicesvault)、[Set-AzureRmRecoveryServicesBackupProperties](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices/v2.3.0/set-azurermrecoveryservicesbackupproperties))
2. Azure SQL サーバーをコンテナーに登録します ([Set-AzureRmSqlServerBackupLongTermRetentionVault](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/set-azurermsqlserverbackuplongtermretentionvault))
3. 保持ポリシーを作成します ([New-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/new-azurermrecoveryservicesbackupprotectionpolicy))
4. 長期的なバックアップの保有を必要とするデータベースに、保護ポリシーを適用します ([Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/set-azurermsqldatabasebackuplongtermretentionpolicy))

詳しくは、「[最大で 10 年間の Azure SQL Database のバックアップを格納する](sql-database-long-term-retention.md)」をご覧ください。

チュートリアルについては、「[PowerShell を使用したデータの保護と回復のためのバックアップと復元の概要](sql-database-get-started-backup-recovery-powershell.md)」をご覧ください。

```
# Configure long-term backup retention

# User variables
################
$resourceGroupName = "{resource-group-name}"
$serverName = "{server-name}"
$databaseToBackup = "{database-name}"
$recoveryServiceVaultName = "{vault-name}"

$serverLocation = (Get-AzureRmSqlServer -ServerName $serverName -ResourceGroupName $resourceGroupName).Location


# Create an Azure recovery services vault
#########################################
$vault = New-AzureRmRecoveryServicesVault -Name $recoveryServiceVaultName -ResourceGroupName $resourceGroupName -Location $serverLocation 
Set-AzureRmRecoveryServicesBackupProperties -BackupStorageRedundancy LocallyRedundant -Vault $vault


# Register your Azure SQL server to the vault
#############################################
Set-AzureRmSqlServerBackupLongTermRetentionVault -ResourceGroupName $resourceGroupName -ServerName $serverName -ResourceId $vault.Id

# Create a retention policy 
###########################
$retentionPolicy = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType AzureSQLDatabase

# Set the retention values to 1 year (set to anytime between 1 week and 10 years)
#################################################################################
$retentionPolicy.RetentionDurationType = "Years"
$retentionPolicy.RetentionCount = 1

$retentionPolicyName = "myOneYearRetentionPolicy"

# Set the vault context to the vault you are creating the policy for
####################################################################
Set-AzureRmRecoveryServicesVaultContext -Vault $vault

# Create the new policy
#######################
$policy = New-AzureRmRecoveryServicesBackupProtectionPolicy -name $retentionPolicyName -WorkloadType AzureSQLDatabase -retentionPolicy $retentionPolicy


# Apply the retention policy to the database to backup 
######################################################
Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseToBackup -State "enabled" -ResourceId $policy.Id
```

## <a name="restore-from-long-term-backup-retention-using-powershell"></a>PowerShell を使って長期のバックアップ リテンション期間から復元する

長期リテンション期間のバックアップからデータベースを復元するには、最新の [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) が必要であり、次のコマンドレットを使います。

- [Get-AzureRmRecoveryServicesVault](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices/v2.3.0/get-azurermrecoveryservicesvault)
- [Get-AzureRmRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/get-azurermrecoveryservicesbackupcontainer)
- [Get-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/get-azurermrecoveryservicesbackupitem)
- [Get-AzureRmRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/get-azurermrecoveryservicesbackuprecoverypoint)
- [Restore-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/restore-azurermsqldatabase)


```
# Restore a database from long-term backup retention

# User variables
################
$resourceGroupName = "{resource-group-name}"
$serverName = "{server-name}"
$databaseNeedingRestore = "{database-name}"
$recoveryServiceVaultName = "{vault-name}"

$restoredDatabaseName = "{restored-db-name}"
$edition = "{restored-db-edition}"
$performanceLevel = "{restored-db-slo}"


# Set the vault context to the vault we want to restore from
############################################################
$vault = Get-AzureRmRecoveryServicesVault -ResourceGroupName $resourceGroupName -Name $recoveryServiceVaultName
Set-AzureRmRecoveryServicesVaultContext -Vault $vault


# Get the container associated with the selected vault
######################################################
$container = Get-AzureRmRecoveryServicesBackupContainer -ContainerType AzureSQL -FriendlyName $vault.Name

# Get the long-term retention metadata associated with a specific database
##########################################################################
$item = Get-AzureRmRecoveryServicesBackupItem -Container $container -WorkloadType AzureSQLDatabase -Name $databaseNeedingRestore


# Get all available backups for the previously indicated database
# Optionally, set the -StartDate and -EndDate parameters for specific backups
#############################################################################
$availableBackups = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $item
$availableBackups

if (!$availableBackups)
{
    Write-Host "No backups available"
}
else
{
    # Restore the most recent available backup: $availableBackups[0]
    ################################################################
    $restoredDb = Restore-AzureRmSqlDatabase -FromLongTermRetentionBackup -ResourceId $availableBackups[0].Id -ResourceGroupName $resourceGroupName ` 
     -ServerName $serverName -TargetDatabaseName $restoredDatabaseName -Edition $edition -ServiceObjectiveName $performanceLevel
    $restoredDb
}
```

## <a name="view-long-term-backup-retention-information-using-the-powershell"></a>PowerShell を使用して長期のバックアップ リテンション期間の情報を確認する

長期保存のバックアップを表示するには、最新の [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) と、次のコマンドレットを使用する必要があります。

- [Get-AzureRmRecoveryServicesVault](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices/v2.3.0/get-azurermrecoveryservicesvault)
- [Get-AzureRmRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/get-azurermrecoveryservicesbackupcontainer)
- [Get-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/get-azurermrecoveryservicesbackupitem)
- [Get-AzureRmRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/get-azurermrecoveryservicesbackuprecoverypoint)

詳しくは、「[最大で 10 年間の Azure SQL Database のバックアップを格納する](sql-database-long-term-retention.md)」をご覧ください。

```
# View the available backups in long-term backup retention

# User variables
################
$resourceGroupName = "{resource-group-name}"
$serverName = "{server-name}"
$recoveryServiceVaultName = "{vault-name}"

# Set the vault context to the vault we want to query
#####################################################
$vault = Get-AzureRmRecoveryServicesVault -ResourceGroupName $resourceGroupName -Name $recoveryServiceVaultName
Set-AzureRmRecoveryServicesVaultContext -Vault $vault


# Get the container associated with the selected vault
######################################################
$container = Get-AzureRmRecoveryServicesBackupContainer -ContainerType AzureSQL -FriendlyName $vault.Name

# Get the long-term retention metadata associated with the container
####################################################################
$item = Get-AzureRmRecoveryServicesBackupItem -Container $container -WorkloadType AzureSQLDatabase


# Get all available backups
# Optionally, set the -StartDate and -EndDate parameters
########################################################
$availableBackups = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $item
$availableBackups
```

## <a name="delete-long-term-retention-backups-using-powershell"></a>PowerShell を使用して長期保存のバックアップを削除する

>[!Warning]
>このコードを実行する前に、長期保存のバックアップを削除してもよいことを確認してください。 このコード スニペットは、[長期的なバックアップ保有期間](sql-database-long-term-retention.md)の使用方法を学習する際に作成したバックアップとコンテナーや、不要になったバックアップとコンテナーをクリーンアップすることで、不要な料金の発生を回避するためのものです。

長期保存のバックアップを削除するには、最新の [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) と、次のコマンドレットを使用する必要があります。

- [Get-AzureRmRecoveryServicesVault](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices/v2.3.0/get-azurermrecoveryservicesvault)
- [Get-AzureRmRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/get-azurermrecoveryservicesbackupcontainer)
- [Get-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/get-azurermrecoveryservicesbackupitem)
- [Disable-AzureRmRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/disable-azurermrecoveryservicesbackupprotection)
- [Unregister-AzureRmRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/unregister-azurermrecoveryservicesbackupcontainer)
- [Remove-AzureRmRecoveryServicesVault](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices/v2.3.0/remove-azurermrecoveryservicesvault)


詳しくは、「[最大で 10 年間の Azure SQL Database のバックアップを格納する](sql-database-long-term-retention.md)」をご覧ください。

```
# Delete long-term backups
##########################

$resourceGroupName = "{resource-group-name}"
$recoveryServiceVaultName = "{vault-name}"

$vault = Get-AzureRmRecoveryServicesVault -ResourceGroupName $resourceGroupName -Name $recoveryServiceVaultName
Set-AzureRmRecoveryServicesVaultContext -Vault $vault

$containers = Get-AzureRmRecoveryServicesBackupContainer -ContainerType AzureSQL -FriendlyName $vault.Name

ForEach ($container in $containers)
{
   $items = Get-AzureRmRecoveryServicesBackupItem -container $container -WorkloadType AzureSQLDatabase

   ForEach ($item in $items)
   {
      # Remove the backups from the vault
      ###################################
      Disable-AzureRmRecoveryServicesBackupProtection -item $item -RemoveRecoveryPoints -ea SilentlyContinue
   }
   
   Unregister-AzureRmRecoveryServicesBackupContainer -Container $container
}
# Delete the recovery services vault
####################################
Remove-AzureRmRecoveryServicesVault -Vault $vault
```

## <a name="next-steps"></a>次のステップ

- Azure Portal を使って長期的なバックアップ保有期間のバックアップを管理する方法については、[Azure Portal を使った長期的なバックアップ保有期間の管理に関する記事](sql-database-manage-long-term-backup-retention-portal.md)をご覧ください
- サービスによって生成された自動バックアップについては、[自動バックアップ](sql-database-automated-backups.md)に関する記事を参照してください。
- バックアップの長期保存については、[バックアップの長期保存](sql-database-long-term-retention.md)に関する記事を参照してください。
- サービスによって生成された自動バックアップについては、[自動バックアップ](sql-database-automated-backups.md)に関する記事を参照してください。
