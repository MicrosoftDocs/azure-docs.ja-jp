---
title: Azure VM での SQL Server の論理的な削除、および Azure VM ワークロードの SAP HANA の論理的な削除
description: Azure VM での SQL Server の論理的な削除、および Azure VM ワークロードでの SAP HANA の論理的な削除について説明します。これにより、バックアップのセキュリティが強化されます。
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 2a442997d426ff0bf4c74b0b45f7657cc0593b82
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91254297"
---
# <a name="soft-delete-for-sql-server-in-azure-vm-and-sap-hana-in-azure-vm-workloads"></a>Azure VM での SQL Server の論理的な削除、および Azure VM ワークロードの SAP HANA の論理的な削除

Azure Backup では、Azure VM での SQL Server と、Azure VM ワークロードでの SAP HANA で論理的な削除がサポートされるようになりました。 これは、既にサポートされている [Azure 仮想マシンの論理的な削除のシナリオ](soft-delete-virtual-machines.md)に加えてサポートされます。

[論理的な削除](backup-azure-security-feature-cloud.md)は、削除後もバックアップ データを保護するのに役立つセキュリティ機能です。 論理的な削除では、悪意のあるアクターによってデータベースのバックアップが削除 (またはバックアップ データが誤って削除) された場合でも、バックアップ データは追加で 14 日間保持されます。 これにより、データを失うことなくバックアップ項目を回復できます。 バックアップ データが "論理的な削除" 状態にあるこの追加の 14 日間の保持期間中は、お客様にコストは発生しません。

## <a name="soft-delete-for-sql-server-in-azure-vm-using-azure-portal"></a>Azure portal を使用した Azure VM 内の SQL Server の論理的な削除

>[!NOTE]
>これらの手順は、Azure VM の SAP HANA にも適用されます。

1. SQL Server 内のデータベースのバックアップ データを削除するには、バックアップを停止する必要があります。 Azure portal で、Recovery Services コンテナーにアクセスし、バックアップ項目に移動して、 **[バックアップの停止]** を選択します。

   ![バックアップの停止](./media/soft-delete-sql-saphana-in-azure-vm/stop-backup.png)

2. 次のウィンドウでは、バックアップ データを削除するか保持するかを選択できます。 **[バックアップ データの削除]** を選択した場合、データベースのバックアップは完全に削除されません。 もっと正確に言えば、バックアップ データは論理的な削除状態で 14 日間保持されます。 削除は 15 日目まで延期され、データベースのバックアップ状態をユーザーに通知する標準アラート電子メールが初日、12 日目、および 15 日目に送信されます。

   ![バックアップ データの削除](./media/soft-delete-sql-saphana-in-azure-vm/delete-backup-data.png)

3. この 14 日間は、Recovery Services コンテナーの論理的に削除された項目の横に赤い "論理的な削除" アイコンが付けられて表示されます。

   ![論理的に削除された項目](./media/soft-delete-sql-saphana-in-azure-vm/soft-deleted-items.png)

4. 論理的に削除された DB を復元するには、最初に削除を取り消す必要があります。 削除を取り消すには、論理的に削除された DB を選択し、 **[削除の取り消し]** オプションを選択します。

   ![データベースの削除の取り消し](./media/soft-delete-sql-saphana-in-azure-vm/undelete-database.png)

   [削除の取り消し] を選択すると、データベースのすべての復元ポイントの削除が取り消され、復元操作の実行が可能になるという警告がウィンドウに表示されます。 バックアップ項目は、"データを保持して保護を停止" の状態で保持され、バックアップは一時停止され、バックアップ データはバックアップ ポリシーが有効になっていない状態で永久に保持されます。

   ![削除の取り消しの警告](./media/soft-delete-sql-saphana-in-azure-vm/undelete-warning.png)

5. この時点で、選択した論理的に削除されたバックアップ項目の **[復元]** を選択して、データを復元することもできます。

   ![VM の復元](./media/soft-delete-sql-saphana-in-azure-vm/restore-vm.png)

6. 削除の取り消し処理が完了すると、状態は "データを保持して保護を停止" に戻り、 **[バックアップの再開]** を選択できるようになります。 **[バックアップの再開]** 操作により、ユーザー定義のバックアップと保持スケジュールで選択されたバックアップ ポリシーに関連付けられているバックアップ項目が、アクティブ状態に戻ります。

   ![バックアップの再開](./media/soft-delete-sql-saphana-in-azure-vm/resume-backup.png)

## <a name="soft-delete-for-sql-server-in-vm-using-azure-powershell"></a>Azure PowerShell を使用した VM 内の SQL Server の論理的な削除

>[!NOTE]
>Azure PowerShell で論理的に削除するために必要な Az.RecoveryServices の最小バージョンは 2.2.0 です。 `Install-Module -Name Az.RecoveryServices -Force` を使用し、最新バージョンを取得してください。

Azure PowerShell を使用するための一連の手順は、上記で説明した Azure portal の手順と同じです。

### <a name="delete-the-backup-item-using-azure-powershell"></a>Azure PowerShell を使用したバックアップ項目の削除

[Disable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection) PowerShell コマンドレットを使用してバックアップ項目を削除します。

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force
```

バックアップ項目の **DeleteState** が **NotDeleted** から **ToBeDeleted** に変更されます。 バックアップ データは 14 日間保持されます。 削除操作を元に戻す場合、削除操作を元に戻します。

### <a name="undoing-the-deletion-operation-using-azure-powershell"></a>Azure PowerShell を使用して削除操作を元に戻す

まず、論理的な削除状態 (つまり、削除される予定) の関連バックアップ項目をフェッチします。

```powershell
Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType SQLDataBase -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType SQLDataBase -VaultId $myVaultID -Name AppVM1
```

次に、[Undo-AzRecoveryServicesBackupItemDeletion](/powershell/module/az.recoveryservices/undo-azrecoveryservicesbackupitemdeletion) PowerShell コマンドレットで削除操作を元に戻します。

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force
```

バックアップ項目の **DeleteState** が **NotDeleted** に戻ります。 しかし、保護は引き続き停止状態にあります。 バックアップを再開して、保護を再び有効にします。

## <a name="how-to-disable-soft-delete"></a>論理的な削除を無効にする方法

この機能を無効にすることは推奨されません。 論理的な削除を無効にすることを検討する必要があるのは、保護された項目を新しいコンテナーに移動することを計画していて、削除と再保護の前に (テスト環境などで) 必要な 14 日間待機できない場合のみです。論理的な削除を無効にする方法については、「[論理的な削除を有効または無効にする](backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete)」を参照してください。

## <a name="next-steps"></a>次のステップ

- 論理的な削除に関して[よく寄せられる質問](backup-azure-security-feature-cloud.md#frequently-asked-questions)を読む
- [Azure Backup のセキュリティ機能](security-overview.md)に関する記事を読む
