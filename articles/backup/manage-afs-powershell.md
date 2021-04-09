---
title: PowerShell を使用して Azure ファイル共有のバックアップを管理する
description: PowerShell を使用して、Azure Backup サービスによってバックアップされた Azure ファイル共有を管理および監視する方法について説明します。
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: e2f07e56fb9a8715b1b53165ab5f4b45b4e20ccb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89000228"
---
# <a name="manage-azure-file-share-backups-with-powershell"></a>PowerShell を使用して Azure ファイル共有のバックアップを管理する

この記事では、Azure PowerShell を使用して、Azure Backup サービスによってバックアップされた Azure ファイル共有を管理および監視する方法について説明します。

> [!WARNING]
> PowerShell のバージョンが、AFS バックアップ用の "Az.RecoveryServices 2.6.0" のための最小バージョンにアップグレードされていることを確認してください。 詳細については、この変更の要件の概要が記載されている[こちらのセクション](backup-azure-afs-automation.md#important-notice-backup-item-identification)を参照してください。

## <a name="modify-the-protection-policy"></a>保護ポリシーを変更する

Azure ファイル共有のバックアップに使用するポリシーを変更するには、[Enable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) を使用します。 関連するバックアップ項目と新しいバックアップ ポリシーを指定します。

次の例では、**testAzureFS** の保護ポリシーを **dailyafs** から **monthlyafs** に変更します。

```powershell
$monthlyafsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "monthlyafs"
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType AzureFiles -Name "testAzureFS"
Enable-AzRecoveryServicesBackupProtection -Item $afsBkpItem -Policy $monthlyafsPol
```

## <a name="track-backup-and-restore-jobs"></a>バックアップ ジョブと復元ジョブを追跡する

オンデマンドのバックアップ操作や復元操作では、[オンデマンド バックアップの実行](backup-azure-afs-automation.md#trigger-an-on-demand-backup)で示したように、ID と共にジョブが返されます。 ジョブの進行状況と詳細を追跡するには、[Get-AzRecoveryServicesBackupJobDetails](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) コマンドレットを使用します。

```powershell
$job = Get-AzRecoveryServicesBackupJob -JobId 00000000-6c46-496e-980a-3740ccb2ad75 -VaultId $vaultID

 $job | fl


IsCancellable        : False
IsRetriable          : False
ErrorDetails         : {Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureFileShareJobErrorInfo}
ActivityId           : 00000000-5b71-4d73-9465-8a4a91f13a36
JobId                : 00000000-6c46-496e-980a-3740ccb2ad75
Operation            : Restore
Status               : Failed
WorkloadName         : testAFS
StartTime            : 12/10/2018 9:56:38 AM
EndTime              : 12/10/2018 11:03:03 AM
Duration             : 01:06:24.4660027
BackupManagementType : AzureStorage

$job.ErrorDetails

 ErrorCode ErrorMessage                                          Recommendations
 --------- ------------                                          ---------------
1073871825 Microsoft Azure Backup encountered an internal error. Wait for a few minutes and then try the operation again. If the issue persists, please contact Microsoft support.
```

## <a name="stop-protection-on-a-file-share"></a>ファイル共有の保護の停止

Azure ファイル共有の保護を停止するには、次の 2 つの方法があります。

* 以降のすべてのバックアップ ジョブを停止し、すべての復旧ポイントを "*削除*" します
* 以降のすべてのバックアップ ジョブを停止しますが、復旧ポイントは "*そのまま*" にします

Azure Backup によって作成された基になるスナップショットが保持されるため、ストレージに復旧ポイントをそのまま残す場合にはコストが伴う可能性があります。 一方、復旧ポイントを残す利点は、必要に応じて後でファイル共有を復元できることです。 復旧ポイントを保持するためのコストについては、「 [価格の詳細](https://azure.microsoft.com/pricing/details/storage/files/)」を参照してください。 すべての復旧ポイントを削除するように選択した場合、ファイル共有を復元することはできません。

## <a name="stop-protection-and-retain-recovery-points"></a>保護を停止して復旧ポイントを保持する

データを保持したまま保護を停止するには、[Disable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection) コマンドレットを使用します。

次の例では、すべての回復ポイントを保持しつつ、*afsfileshare* ファイル共有の保護を停止します。

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "afstesting" -Name "afstest" | select -ExpandProperty ID
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureStorage -WorkloadType AzureFiles -Name "afsfileshare" -VaultId $vaultID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $vaultID
```

```output
WorkloadName     Operation         Status         StartTime                 EndTime                   JobID
------------     ---------         ------         ---------                 -------                   -----
afsfileshare     DisableBackup     Completed      1/26/2020 2:43:59 PM      1/26/2020 2:44:21 PM      98d9f8a1-54f2-4d85-8433-c32eafbd793f
```

出力の Job ID 属性は、"保護停止" 操作のためにバックアップ サービスによって作成されたジョブの Job ID に対応しています。 ジョブの状態を追跡するには、[Get-AzRecoveryServicesBackupJob](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) コマンドレットを使用します。

## <a name="stop-protection-without-retaining-recovery-points"></a>復旧ポイントを保持しないで保護を停止する

回復ポイントを保持しないで保護を停止するには、[Disable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection) コマンドレットを使用して、 **-RemoveRecoveryPoints** パラメーターを追加します。

次の例では、復旧ポイントを保持しないで、*afsfileshare* ファイル共有の保護を停止します。

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "afstesting" -Name "afstest" | select -ExpandProperty ID
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureStorage -WorkloadType AzureFiles -Name "afsfileshare" -VaultId $vaultID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $vaultID -RemoveRecoveryPoints
```

```output
WorkloadName     Operation            Status         StartTime                 EndTime                   JobID
------------     ---------            ------         ---------                 -------                   -----
afsfileshare     DeleteBackupData     Completed      1/26/2020 2:50:57 PM      1/26/2020 2:51:39 PM      b1a61c0b-548a-4687-9d15-9db1cc5bcc85
```

## <a name="next-steps"></a>次のステップ

Azure portal で Azure ファイル共有のバックアップを管理する方法について[学習します](manage-afs-backup.md)。
