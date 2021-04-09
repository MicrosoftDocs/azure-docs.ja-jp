---
title: 仮想マシンの論理的な削除
description: 仮想マシンの論理的な削除によって、バックアップの安全性が向上するしくみについて説明します。
ms.topic: conceptual
ms.date: 04/30/2020
ms.custom: references_regions
ms.openlocfilehash: a8b70d4c8240d096c19e5a8d7449921557b8896c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89022243"
---
# <a name="soft-delete-for-virtual-machines"></a>仮想マシンの論理的な削除

VM の論理的な削除によって、ご利用の VM のバックアップが意図しない削除から保護されます。 バックアップは、削除された後でも、追加で 14 日間は論理的な削除状態で保持されます。

> [!NOTE]
> 論理的な削除では、削除されたバックアップ データのみが保護されます。 バックアップせずに VM が削除された場合、論理的な削除機能ではデータは保持されません。 完全な復元性を確保するには、すべてのリソースを Azure Backup で保護する必要があります。
>

## <a name="supported-regions"></a>サポートされているリージョン

現在、論理的な削除は、米国中西部、東アジア、カナダ中部、カナダ東部、フランス中部、フランス南部、韓国中部、韓国南部、英国南部、英国西部、オーストラリア東部、オーストラリア南東部、北ヨーロッパ、米国西部、米国西部 2、米国中部、東南アジア、米国中北部、米国中南部、東日本、西日本、インド南部、インド中部、インド西部、米国東部 2、スイス北部、スイス西部、ノルウェー西部、ノルウェー東部など、すべての国のリージョンでサポートされています。

## <a name="soft-delete-for-vms-using-azure-portal"></a>Azure portal を使用した VM の論理的な削除

1. VM のバックアップ データを削除するには、バックアップを停止する必要があります。 Azure portal で、Recovery Services コンテナーにアクセスし、バックアップ項目を右クリックして、 **[バックアップの停止]** を選択します。

   ![Azure portal の [バックアップ項目] のスクリーンショット](./media/backup-azure-security-feature-cloud/backup-stopped.png)

2. 次のウィンドウでは、バックアップ データを削除するか保持するかを選択できます。 **[バックアップデータの削除]** を選択してから **[バックアップの停止]** を選択した場合、VM のバックアップは完全に削除されません。 もっと正確に言えば、バックアップ データは論理的な削除状態で 14 日間保持されます。 **[バックアップ データの削除]** を選択した場合は、削除の電子メール アラートが構成済みのメール ID に送信され、バックアップ データの保有延長が 14 日間残っていることがユーザーに通知されます。 また、12 日目に、削除されたデータを復元できる期間があと 2 日間残っていることを知らせる電子メール アラートが送信されます。 削除は 15 日目まで延期されます。ここで完全な削除が行われ、データが永久に削除されたことを知らせる最終的な電子メール アラートが送信されます。

   ![Azure portal の [バックアップの停止] 画面のスクリーンショット](./media/backup-azure-security-feature-cloud/delete-backup-data.png)

3. この 14 日間は、Recovery Services コンテナーで、論理的に削除された VM の横に赤い "論理的な削除" アイコンが付けられて表示されます。

   ![論理的な削除状態の VM を示している Azure portal のスクリーンショット](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

   > [!NOTE]
   > 論理的に削除されたバックアップ項目がコンテナーに存在する場合、その時点でコンテナーを削除することはできません。 バックアップ項目が完全に削除され、コンテナーに論理的な削除状態の項目がなくなってからコンテナーの削除を試みてください。

4. 論理的に削除された VM を復元するには、最初に削除を取り消す必要があります。 削除を取り消すには、論理的に削除された VM を選択し、 **[削除の取り消し]** オプションを選択します。

   ![VM の削除の取り消しを示している Azure portal のスクリーンショット](./media/backup-azure-security-feature-cloud/choose-undelete.png)

   [削除の取り消し] を選択すると、VM のすべての復元ポイントの削除が取り消され、復元操作の実行が可能になるという警告がウィンドウに表示されます。 VM は、"データを保持して保護を停止" の状態で保持され、バックアップは一時停止され、バックアップ データはバックアップ ポリシーが有効になっていない状態で永久に保持されます。

   ![VM の削除の取り消しを確認する Azure portal のスクリーンショット](./media/backup-azure-security-feature-cloud/undelete-vm.png)

   この時点で、選択した復元ポイントから **[VM の復元]** を選択して VM を復元することもできます。  

   ![Azure portal の [VM の復元] オプションのスクリーンショット](./media/backup-azure-security-feature-cloud/restore-vm.png)

   > [!NOTE]
   > ユーザーが **[バックアップの再開]** 操作を実行した場合にのみ、ガベージ コレクターが実行され、期限切れの回復ポイントがクリーンアップされます。

5. 削除の取り消し処理が完了すると、状態は "データを保持して保護を停止" に戻り、 **[バックアップの再開]** を選択できるようになります。 **[バックアップの再開]** 操作により、ユーザー定義のバックアップと保持スケジュールで選択されたバックアップ ポリシーに関連付けられているバックアップ項目が、アクティブ状態に戻ります。

   ![Azure portal の [バックアップの再開] オプションのスクリーンショット](./media/backup-azure-security-feature-cloud/resume-backup.png)

## <a name="soft-delete-for-vms-using-azure-powershell"></a>Azure PowerShell を使用した VM の論理的な削除

> [!IMPORTANT]
> Azure PowerShell で論理的に削除するために必要な Az.RecoveryServices の最小バージョンは 2.2.0 です。 ```Install-Module -Name Az.RecoveryServices -Force``` を使用し、最新バージョンを取得してください。

Azure portal に関する上記の説明にあるように、Azure PowerShell の使用時も手順は同じです。

### <a name="delete-the-backup-item-using-azure-powershell"></a>Azure PowerShell を使用したバックアップ項目の削除

[Disable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection) PowerShell コマンドレットを使用してバックアップ項目を削除します。

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           DeleteBackupData     Completed            12/5/2019 12:44:15 PM     12/5/2019 12:44:50 PM     0488c3c2-accc-4a91-a1e0-fba09a67d2fb
```

バックアップ項目の "DeleteState" が "NotDeleted" から "ToBeDeleted" に変更されます。 バックアップ データは 14 日間保持されます。 削除操作を元に戻す場合、削除操作を元に戻します。

### <a name="undoing-the-deletion-operation-using-azure-powershell"></a>Azure PowerShell を使用して削除操作を元に戻す

まず、論理的な削除状態 (つまり、削除される予定) の関連バックアップ項目をフェッチします。

```powershell

Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

Name                                     ContainerType        ContainerUniqueName                      WorkloadType         ProtectionStatus     HealthStatus         DeleteState
----                                     -------------        -------------------                      ------------         ----------------     ------------         -----------
VM;iaasvmcontainerv2;selfhostrg;AppVM1    AzureVM             iaasvmcontainerv2;selfhostrg;AppVM1       AzureVM              Healthy              Passed               ToBeDeleted

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID -Name AppVM1
```

次に、[Undo-AzRecoveryServicesBackupItemDeletion](/powershell/module/az.recoveryservices/undo-azrecoveryservicesbackupitemdeletion) PowerShell コマンドレットで削除操作を元に戻します。

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           Undelete             Completed            12/5/2019 12:47:28 PM     12/5/2019 12:47:40 PM     65311982-3755-46b5-8e53-c82ea4f0d2a2
```

バックアップ項目の "DeleteState" が "NotDeleted" に戻ります。 しかし、保護は引き続き停止状態にあります。 [バックアップを再開](./backup-azure-vms-automation.md#change-policy-for-backup-items)して、保護を再び有効にします。

## <a name="soft-delete-for-vms-using-rest-api"></a>REST API を使用した VM の論理的な削除

- [こちら](backup-azure-arm-userestapi-backupazurevms.md#stop-protection-and-delete-data)の説明に従って、REST API を使用してバックアップを削除します。
- これらの削除操作を元に戻す場合は、[こちら](backup-azure-arm-userestapi-backupazurevms.md#undo-the-deletion)で説明されている手順を参照してください。

## <a name="how-to-disable-soft-delete"></a>論理的な削除を無効にする方法

この機能を無効にすることは推奨されません。 論理的な削除を無効にすることを検討する必要があるのは、保護された項目を新しいコンテナーに移動することを計画していて、削除と再保護の前に (テスト環境などで) 必要な 14 日間待機できない場合のみです。論理的な削除を無効にする方法については、「[論理的な削除を有効または無効にする](backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete)」を参照してください。

## <a name="next-steps"></a>次のステップ

- 論理的な削除に関して[よく寄せられる質問](backup-azure-security-feature-cloud.md#frequently-asked-questions)を読む
- [Azure Backup のセキュリティ機能](security-overview.md)に関する記事を読む
