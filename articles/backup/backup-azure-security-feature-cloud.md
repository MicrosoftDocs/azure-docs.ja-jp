---
title: Azure Backup の論理的な削除
description: Azure Backup のセキュリティ機能を使用してバックアップのセキュリティを強化する方法について説明します。
ms.topic: conceptual
ms.date: 04/30/2020
ms.openlocfilehash: da473b1d886ec2fe95a7baae76b09aff38fb3cd7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95254051"
---
# <a name="soft-delete-for-azure-backup"></a>Azure Backup の論理的な削除

マルウェア、ランサムウェア、侵入などのセキュリティ問題への懸念が高まっています。 これらのセキュリティ問題は、金銭とデータの両方の観点からコストがかかる可能性があります。 このような攻撃から保護するために、Azure Backup では、削除後もバックアップ データを保護するためのセキュリティ機能が提供されるようになりました。

このような機能の 1 つに、論理的な削除があります。 論理的な削除を使用すると、悪意のあるアクターによってバックアップが削除 (またはバックアップ データが誤って削除) された場合でも、バックアップ データは追加で 14 日間保持されるので、データを失うことなくバックアップ項目を回復できます。 バックアップ データが "論理的な削除" 状態にあるこの追加の 14 日間のリテンション期間中は、お客様にコストは発生しません。

次のサービスでは、論理的な削除による保護を利用できます。

- [Azure 仮想マシンの論理的な削除](soft-delete-virtual-machines.md)
- [Azure VM での SQL Server の論理的な削除、および Azure VM ワークロードの SAP HANA の論理的な削除](soft-delete-sql-saphana-in-azure-vm.md)

このフロー チャートは、論理的な削除が有効にされているときのバックアップ項目のさまざまな手順と状態を示しています。

![論理的に削除されたバックアップ項目のライフサイクル](./media/backup-azure-security-feature-cloud/lifecycle.png)

## <a name="enabling-and-disabling-soft-delete"></a>論理的な削除を有効または無効にする

論理的な削除は、偶発的または悪意のある削除からバックアップ データを保護するために、新しく作成されたコンテナーでは既定で有効になっています。  この機能を無効にすることは推奨されません。 論理的な削除を無効にすることを検討する必要があるのは、保護された項目を新しいコンテナーに移動することを計画していて、削除と再保護の前に (テスト環境などで) 必要な 14 日間待機できない場合のみです。この機能を無効にできるのは、コンテナーの所有者だけです。 この機能を無効にした場合、保護された項目を今後削除すると、復元する機能はなく、すべてがすぐに削除されます。 この機能を無効にする前に、論理的に削除された状態で存在するバックアップ データは、14 日間論理的に削除された状態のままになります。 これらをすぐに完全に削除する場合、完全に削除されるように、削除を取り消してからもう一度削除する必要があります。

論理的な削除を無効にすると、すべての種類のワークロードでこの機能が無効になることに注意してください。 たとえば、SQL Server や SAP HANA DB に対してのみ論理的な削除を無効にし、同じコンテナー内の仮想マシンに対して有効にすることはできません。 詳細に制御するには、個別のコンテナーを作成します。

### <a name="disabling-soft-delete-using-azure-portal"></a>Azure portal を使用した論理的な削除を無効にする

論理的な削除を無効にするには、次の手順に従います。

1. Azure portal で、ご利用のコンテナーに移動して、 **[設定]**  ->  **[プロパティ]** に移動します。
2. プロパティ ウィンドウで、 **[セキュリティ設定]**  ->  **[更新]** を選択します。  
3. [セキュリティ設定] ウィンドウの **[論理的な削除]** で、 **[無効にする]** を選択します。

![論理的な削除の無効化](./media/backup-azure-security-feature-cloud/disable-soft-delete.png)

### <a name="disabling-soft-delete-using-azure-powershell"></a>Azure PowerShell を使用した論理的な削除を無効にする

> [!IMPORTANT]
> Azure PowerShell で論理的に削除するために必要な Az.RecoveryServices の最小バージョンは 2.2.0 です。 ```Install-Module -Name Az.RecoveryServices -Force``` を使用し、最新バージョンを取得してください。

無効にするには、[Set-AzRecoveryServicesVaultBackupProperty](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) PowerShell コマンドレットを使用します。

```powershell
Set-AzRecoveryServicesVaultProperty -VaultId $myVaultID -SoftDeleteFeatureState Disable


StorageModelType       :
StorageType            :
StorageTypeState       :
EnhancedSecurityState  : Enabled
SoftDeleteFeatureState : Disabled
```

### <a name="disabling-soft-delete-using-rest-api"></a>REST API を使用した論理的な削除の無効化

REST API を使用して論理的な削除機能を無効にする方法については、[こちら](use-restapi-update-vault-properties.md#update-soft-delete-state-using-rest-api)で説明されている手順を参照してください。

## <a name="permanently-deleting-soft-deleted-backup-items"></a>論理的に削除されたバックアップ項目を完全に削除する

この機能を無効にする前に、論理的に削除された状態のバックアップ データは、論理的に削除された状態のままになります。 これらをすぐに完全に削除する場合、削除を取り消し、もう一度削除して完全に削除します。

### <a name="using-azure-portal"></a>Azure Portal の使用

次の手順に従います。

1. [論理的な削除を無効にする](#enabling-and-disabling-soft-delete)には、この手順に従います。

2. Azure portal で、お使いのコンテナーにアクセスし、**バックアップ項目** にアクセスして論理的に削除された項目を選択します。

   ![論理的に削除された項目の選択](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

3. **[削除の取り消し]** オプションを選択します。

   ![[削除の取り消し] の選択](./media/backup-azure-security-feature-cloud/choose-undelete.png)

4. ウィンドウが表示されます。 **[削除の取り消し]** を選択します。

   ![[削除の取り消し] の選択](./media/backup-azure-security-feature-cloud/undelete-vm.png)

5. バックアップ データを完全に削除するには、 **[バックアップ データの削除]** を選択します。

   ![[バックアップ データの削除] の選択](./media/backup-azure-manage-vms/delete-backup-button.png)

6. バックアップ項目の名前を入力して、復旧ポイントを削除してもよいことを確認します。

   ![バックアップ項目の名前の入力](./media/backup-azure-manage-vms/delete-backup-data.png)

7. 項目のバックアップ データを削除するには、 **[削除]** を選択します。 バックアップ データが削除されたことを示す通知メッセージが表示されます。

### <a name="using-azure-powershell"></a>Azure PowerShell の使用

論理的な削除を無効にする前に項目を削除した場合、項目は論理的に削除されている状態になります。 すぐに削除するには、削除操作を元に戻してからもう一度削除する必要があります。

論理的に削除された状態にある項目を特定します。

```powershell

Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

Name                                     ContainerType        ContainerUniqueName                      WorkloadType         ProtectionStatus     HealthStatus         DeleteState
----                                     -------------        -------------------                      ------------         ----------------     ------------         -----------
VM;iaasvmcontainerv2;selfhostrg;AppVM1    AzureVM             iaasvmcontainerv2;selfhostrg;AppVM1       AzureVM              Healthy              Passed               ToBeDeleted

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID -Name AppVM1
```

次に、論理的な削除が有効になっていたときに実行された削除操作を元に戻します。

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           Undelete             Completed            12/5/2019 12:47:28 PM     12/5/2019 12:47:40 PM     65311982-3755-46b5-8e53-c82ea4f0d2a2
```

これで論理的な削除が無効になったため、削除操作後、バックアップ データがすぐに削除されるようになります。

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           DeleteBackupData     Completed            12/5/2019 12:44:15 PM     12/5/2019 12:44:50 PM     0488c3c2-accc-4a91-a1e0-fba09a67d2fb
```

### <a name="using-rest-api"></a>REST API の使用

論理的な削除を無効にする前に項目を削除した場合、項目は論理的に削除されている状態になります。 すぐに削除するには、削除操作を元に戻してからもう一度削除する必要があります。

1. まず、[こちら](backup-azure-arm-userestapi-backupazurevms.md#undo-the-deletion)で説明されている手順に従って、削除操作を元に戻します。
2. 次に、[こちら](use-restapi-update-vault-properties.md#update-soft-delete-state-using-rest-api)で説明されている手順に従って、REST API を使用して論理的な削除機能を無効にします。
3. 次に、[こちら](backup-azure-arm-userestapi-backupazurevms.md#stop-protection-and-delete-data)の説明に従って、REST API を使用してバックアップを削除します。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="do-i-need-to-enable-the-soft-delete-feature-on-every-vault"></a>すべてのコンテナーで論理的な削除機能を有効にする必要はありますか?

いいえ。これはすべての Recovery Services コンテナーに組み込まれており、既定で有効になっています。

### <a name="can-i-configure-the-number-of-days-for-which-my-data-will-be-retained-in-soft-deleted-state-after-the-delete-operation-is-complete"></a>削除操作の完了後にデータが論理的に削除された状態で保持される日数を構成できますか?

いいえ。削除操作後、追加の保持期間は 14 日間に固定されています。

### <a name="do-i-need-to-pay-the-cost-for-this-additional-14-day-retention"></a>この 14 日間の保持期間に対するコストを支払う必要がありますか?

いいえ。この 14 日間の追加の保持期間は、論理的な削除機能の一部として無料で提供されます。

### <a name="can-i-perform-a-restore-operation-when-my-data-is-in-soft-delete-state"></a>データが論理的な削除状態であるときに復元操作を実行できますか?

いいえ。復元するには、論理的に削除されたリソースの削除を取り消す必要があります。 削除の取り消し操作により、リソースが **データを保持して保護を停止の状態** に戻り、任意の時点に復元できるようになります。 ガベージ コレクターは、この状態で一時停止のままになります。

### <a name="will-my-snapshots-follow-the-same-lifecycle-as-my-recovery-points-in-the-vault"></a>スナップショットは、コンテナー内の回復ポイントと同じライフサイクルに従いますか?

はい。

### <a name="how-can-i-trigger-the-scheduled-backups-again-for-a-soft-deleted-resource"></a>論理的に削除されたリソースに対してスケジュールされたバックアップを再びトリガーするにはどうすればよいですか?

削除の取り消し後に再開操作を実行すると、リソースが再度保護されます。 再開操作により、選択した保持期間でスケジュールされたバックアップをトリガーするため、バックアップ ポリシーが関連付けられます。 また、再開操作が完了するとすぐにガベージ コレクターが実行されます。 有効期限を過ぎた回復ポイントから復元を実行する場合は、再開操作をトリガーする前に実行することをお勧めします。

### <a name="can-i-delete-my-vault-if-there-are-soft-deleted-items-in-the-vault"></a>コンテナー内に論理的に削除された項目がある場合、コンテナーを削除できますか?

Recovery Services コンテナー内に論理的に削除された状態のバックアップ項目がある場合、そのコンテナーを削除することはできません。 論理的に削除された項目は、削除操作の 14 日後に完全に削除されます。 14 日間待機できない場合は、[論理的な削除を無効にし](#enabling-and-disabling-soft-delete)、論理的に削除された項目の削除を取り消して、もう一度削除して完全に削除します。 保護された項目がなく、論理的に削除された項目がないことが確保されたら、コンテナーを削除することができます。  

### <a name="can-i-delete-the-data-earlier-than-the-14-days-soft-delete-period-after-deletion"></a>削除後の 14 日間の論理的な削除期間より前にデータを削除することはできますか?

いいえ。 論理的に削除された項目を強制的に削除することはできません。 これらは 14 日後に自動的に削除されます。 このセキュリティ機能は、偶発的または悪意のある削除からバックアップされたデータを保護するために有効になっています。  項目に対して他の操作を実行する前に、14 日間待機する必要があります。  論理的に削除された項目については課金されません。  新しいコンテナーで 14 日以内に論理的な削除のマークが付けられた項目を再保護する必要がある場合は、Microsoft サポートにお問い合わせください。

### <a name="can-soft-delete-operations-be-performed-in-powershell-or-cli"></a>PowerShell または CLI で論理的な削除操作を実行できますか?

論理的な削除の操作は PowerShell で実行できます。 現在、CLI はサポートされていません。

## <a name="next-steps"></a>次のステップ

- [Azure Backup のセキュリティ機能の概要](security-overview.md)
