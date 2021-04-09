---
title: Azure 仮想マシンの選択的なディスク バックアップと復元
description: この記事では、Azure 仮想マシン バックアップ ソリューションを使用した選択的なディスク バックアップと復元について説明します。
ms.topic: conceptual
ms.date: 07/17/2020
ms.custom: references_regions , devx-track-azurecli
ms.openlocfilehash: e82c959dc63222e8565243cc9ac805283cab6617
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102501833"
---
# <a name="selective-disk-backup-and-restore-for-azure-virtual-machines"></a>Azure 仮想マシンの選択的なディスク バックアップと復元

Azure Backup では、仮想マシン バックアップ ソリューションを使用して、VM 内のすべてのディスク (オペレーティング システムとデータ) をまとめてバックアップすることがサポートされています。 現在、選択的なディスク バックアップと復元の機能を使用して、VM 内のデータ ディスクのサブセットをバックアップできるようになっています。 これにより、バックアップと復元のニーズに応じた効率的で費用対効果の高いソリューションが提供されます。 各復旧ポイントには、バックアップ操作に含まれるディスクだけが含まれます。 これにより、復元操作中に特定の復旧ポイントからディスクのサブセットを復元できるようになります。 これは、スナップショットからの復元とコンテナーからの復元の両方に適用されます。

## <a name="scenarios"></a>シナリオ

このソリューションは、次のようなシナリオで特に役立ちます。

1. 1 つのディスクのみ、またはディスクのサブセットの重要なデータのバックアップを行い、バックアップ ストレージのコストを最小限に抑えるために、VM に接続されている残りのディスクをバックアップしない場合。
2. VM またはデータの一部について、他のバックアップ ソリューションがある場合。 たとえば、データベースまたはデータを別のワークロード バックアップ ソリューションを使用してバックアップし、残りのデータまたはディスクに対して Azure VM レベルのバックアップを使用して、使用可能な最適な機能を活用した効率的で堅牢なシステムを構築できます。

PowerShell または Azure CLI を使用すると、Azure VM の選択的なディスク バックアップを構成できます。  スクリプトを使用すると、LUN 番号を使用してデータ ディスクを含めたり除外したりすることができます。  現時点では、Azure portal を介して選択的なディスク バックアップを構成する機能は、**OS ディスクのみバックアップ** オプションに制限されています。 そのため、OS ディスクとともに Azure VM のバックアップを構成し、それに接続されているすべてのデータ ディスクを除外することができます。

>[!NOTE]
> OS ディスクは既定で VM バックアップに追加されるため、除外することはできません。

## <a name="using-azure-cli"></a>Azure CLI の使用

Az CLI バージョン 2.0.80 以上を使用していることを確認します。 CLI のバージョンは、次のコマンドを使用して取得できます。

```azurecli
az --version
```

Recovery Services コンテナーと VM が存在するサブスクリプション ID にサインインします。

```azurecli
az account set -s {subscriptionID}
```

>[!NOTE]
>次の各コマンドでは、コンテナーに対応する **resourcegroup** 名 (オブジェクトではありません) のみが必要です。

### <a name="configure-backup-with-azure-cli"></a>Azure CLI を使用してバックアップを構成する

保護の構成操作中に、**inclusion** / **exclusion** パラメーターを使用して、ディスク リスト設定を指定する必要があり、バックアップに含めるまたは除外するディスクの LUN 番号を指定します。

```azurecli
az backup protection enable-for-vm --resource-group {resourcegroup} --vault-name {vaultname} --vm {vmname} --policy-name {policyname} --disk-list-setting include --diskslist {LUN number(s) separated by space}
```

```azurecli
az backup protection enable-for-vm --resource-group {resourcegroup} --vault-name {vaultname} --vm {vmname} --policy-name {policyname} --disk-list-setting exclude --diskslist 0 1
```

VM がコンテナーと同じリソース グループにない場合、**ResourceGroup** は、コンテナーが作成されたリソース グループを参照します。 VM 名の代わりに、次に示すように VM ID を指定します。

```azurecli
az backup protection enable-for-vm  --resource-group {ResourceGroup} --vault-name {vaultname} --vm $(az vm show -g VMResourceGroup -n MyVm --query id --output tsv) --policy-name {policyname} --disk-list-setting include --diskslist {LUN number(s) separated by space}
```

### <a name="modify-protection-for-already-backed-up-vms-with-azure-cli"></a>Azure CLI を使用して、既にバックアップされた VM の保護を変更する

```azurecli
az backup protection update-for-vm --resource-group {resourcegroup} --vault-name {vaultname} -c {vmname} -i {vmname} --backup-management-type AzureIaasVM --disk-list-setting exclude --diskslist {LUN number(s) separated by space}
```

### <a name="backup-only-os-disk-during-configure-backup-with-azure-cli"></a>Azure CLI を使用して、バックアップの構成中に OS ディスクのみをバックアップする

```azurecli
az backup protection enable-for-vm --resource-group {resourcegroup} --vault-name {vaultname} --vm {vmname} --policy-name {policyname} --exclude-all-data-disks
```

### <a name="backup-only-os-disk-during-modify-protection-with-azure-cli"></a>Azure CLI を使用して、保護の変更中に OS ディスクのみをバックアップする

```azurecli
az backup protection update-for-vm --resource-group {resourcegroup} --vault-name {vaultname} -c {vmname} -i {vmname} --backup-management-type AzureIaasVM --exclude-all-data-disks
```

### <a name="restore-disks-with-azure-cli"></a>Azure CLI を使用してディスクを復元する

```azurecli
az backup restore restore-disks --resource-group {resourcegroup} --vault-name {vaultname} -c {vmname} -i {vmname} -r {restorepoint} --target-resource-group {targetresourcegroup} --storage-account {storageaccountname} --diskslist {LUN number of the disk(s) to be restored}
```

### <a name="restore-only-os-disk-with-azure-cli"></a>Azure CLI を使用して OS ディスクのみを復元する

```azurecli
az backup restore restore-disks --resource-group {resourcegroup} --vault-name {vaultname} -c {vmname} -i {vmname} -r {restorepoint} } --target-resource-group {targetresourcegroup} --storage-account {storageaccountname} --restore-only-osdisk
```

### <a name="get-protected-item-to-get-disk-exclusion-details-with-azure-cli"></a>Azure CLI を使用して、ディスク除外の詳細を取得するために保護された項目を取得する

```azurecli
az backup item show -c {vmname} -n {vmname} --vault-name {vaultname} --resource-group {resourcegroup} --backup-management-type AzureIaasVM
```

次に示すように、保護された項目に追加の **diskExclusionProperties** パラメーターが追加されています。

```azurecli
"extendedProperties": {
      "diskExclusionProperties": {
        "diskLunList": [
          0,
          1
        ],
        "isInclusionList": true
      }
```

### <a name="get-backup-job-with-azure-cli"></a>Azure CLI を使用してバックアップ ジョブを取得する

```azurecli
az backup job show --vault-name {vaultname} --resource-group {resourcegroup} -n {BackupJobID}
```

このコマンドは、次に示すように、バックアップされたディスクと除外されたディスクの詳細を取得するのに役立ちます。

```output
   "Backed-up disk(s)": "diskextest_OsDisk_1_170808a95d214428bad92efeecae626b; diskextest_DataDisk_0; diskextest_DataDisk_1",  "Backup Size": "0 MB",
   "Excluded disk(s)": "diskextest_DataDisk_2",
```

### <a name="list-recovery-points-with-azure-cli"></a>Azure CLI を使用して復旧ポイントを一覧表示する

```azurecli
az backup recoverypoint list --vault-name {vaultname} --resource-group {resourcegroup} -c {vmname} -i {vmname} --backup-management-type AzureIaasVM
```

これにより、VM に接続されているディスクと、バックアップされているディスクの数の情報が得られます。

```azurecli
      "recoveryPointDiskConfiguration": {
        "excludedDiskList": null,
        "includedDiskList": null,
        "numberOfDisksAttachedToVm": 4,
        "numberOfDisksIncludedInBackup": 3
};
```

### <a name="get-recovery-point-with-azure-cli"></a>Azure CLI を使用して復旧ポイントを取得する

```azurecli
az backup recoverypoint show --vault-name {vaultname} --resource-group {resourcegroup} -c {vmname} -i {vmname} --backup-management-type AzureIaasVM -n {recoverypointID}
```

各復旧ポイントには、含まれているディスクと除外されているディスクの情報があります。

```azurecli
  "recoveryPointDiskConfiguration": {
      "excludedDiskList": [
        {
          "lun": 2,
          "name": "diskextest_DataDisk_2"
        }
      ],
      "includedDiskList": [
        {
          "lun": -1,
          "name": "diskextest_OsDisk_1_170808a95d214428bad92efeecae626b"
        },
        {
          "lun": 0,
          "name": "diskextest_DataDisk_0"
        },
        {
          "lun": 1,
          "name": "diskextest_DataDisk_1"
        }
      ],
      "numberOfDisksAttachedToVm": 4,
      "numberOfDisksIncludedInBackup": 3
```

### <a name="remove-disk-exclusion-settings-and-get-protected-item-with-azure-cli"></a>Azure CLI を使用して、ディスクの除外設定を削除し、保護された項目を取得する

```azurecli
az backup protection update-for-vm --vault-name {vaultname} --resource-group {resourcegroup} -c {vmname} -i {vmname} --backup-management-type AzureIaasVM --disk-list-setting resetexclusionsettings

az backup item show -c {vmname} -n {vmname} --vault-name {vaultname} --resource-group {resourcegroup} --backup-management-type AzureIaasVM
```

これらのコマンドを実行すると、`"diskExclusionProperties": null` が表示されます。

## <a name="using-powershell"></a>PowerShell の使用

Azure PowerShell バージョン 3.7.0 以上を使用していることを確認します。

保護の構成操作中に、バックアップに含めるか、または除外するディスクの LUN 番号を示す inclusion または exclusion パラメーターを含むディスク リスト設定を指定する必要があります。

### <a name="enable-backup-with-powershell"></a>PowerShell を使用してバックアップを有効にする

次に例を示します。

```azurepowershell
$disks = ("0","1")
$targetVault = Get-AzRecoveryServicesVault -ResourceGroupName "rg-p-recovery_vaults" -Name "rsv-p-servers"
Get-AzRecoveryServicesBackupProtectionPolicy
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "P-Servers"
```

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"  -InclusionDisksList $disks -VaultId $targetVault.ID
```

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"  -ExclusionDisksList $disks -VaultId $targetVault.ID
```

### <a name="backup-only-os-disk-during-configure-backup-with-powershell"></a>PowerShell を使用して、バックアップの構成中に OS ディスクのみをバックアップする

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"  -ExcludeAllDataDisks -VaultId $targetVault.ID
```

### <a name="get-backup-item-object-to-be-passed-in-modify-protection-with-powershell"></a>PowerShell を使用して、保護の変更に渡すバックアップ項目オブジェクトを取得する

```azurepowershell
$item= Get-AzRecoveryServicesBackupItem -BackupManagementType "AzureVM" -WorkloadType "AzureVM" -VaultId $targetVault.ID -FriendlyName "V2VM"
```

上記で取得した **$item** オブジェクトを、次のコマンドレットの **–Item** パラメーターに渡す必要があります。

### <a name="modify-protection-for-already-backed-up-vms-with-powershell"></a>PowerShell を使用して、既にバックアップされた VM の保護を変更する

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Item $item -InclusionDisksList[Strings] -VaultId $targetVault.ID
```

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Item $item -ExclusionDisksList[Strings] -VaultId $targetVault.ID
```

### <a name="backup-only-os-disk-during-modify-protection-with-powershell"></a>PowerShell を使用して、保護の変更中に OS ディスクのみをバックアップする

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Item $item  -ExcludeAllDataDisks -VaultId $targetVault.ID
```

### <a name="reset-disk-exclusion-setting-with-powershell"></a>PowerShell を使用して、ディスク除外設定をリセットする

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Item $item -ResetExclusionSettings -VaultId $targetVault.ID
```

### <a name="restore-selective-disks-with-powershell"></a>PowerShell を使用して、ディスクを選択的に復元する

```azurepowershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $item -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime() -VaultId $targetVault.ID
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks" -VaultId $targetVault.ID -RestoreDiskList [$disks]
```

### <a name="restore-only-os-disk-with-powershell"></a>PowerShell を使用して、OS ディスクのみを復元する

```azurepowershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks" -VaultId $targetVault.ID -RestoreOnlyOSDisk
```

## <a name="using-the-azure-portal"></a>Azure ポータルの使用

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

Azure portal を使用すると、VM バックアップの詳細ペインとバックアップ ジョブの詳細ペインから、含まれているディスクと除外されているディスクを表示できます。  復元時に、復元する復旧ポイントを選択すると、その復旧ポイントのバックアップ ディスクを表示できます。

ここでは、VM バックアップの詳細ペインから、ポータルの仮想マシンについて、含まれているディスクと除外されているディスクを確認できます。

![バックアップの詳細ペインから、含まれているディスクと除外されているディスクを表示する](./media/selective-disk-backup-restore/backup-details.png)

ここでは、ジョブの詳細ペインから、バックアップに含まれているディスクと除外されているディスクを確認できます。

![ジョブの詳細ペインから、含まれているディスクと除外されているディスクを表示する](./media/selective-disk-backup-restore/job-details.png)

ここでは、復元する復旧ポイントを選択すると、復元時にバックアップ ディスクを表示できます。

![復元時にバックアップ ディスクを表示する](./media/selective-disk-backup-restore/during-restore.png)

Azure portal を介した VM の選択的なディスク バックアップ エクスペリエンスの構成は、**OS ディスクのみバックアップ** オプションに制限されています。 既にバックアップされている VM 上で選択的なディスク バックアップを使用する場合、または VM の特定のデータ ディスクの高度な包含または除外を行う場合は、PowerShell または Azure CLI を使用します。

>[!NOTE]
>データが複数のディスクにまたがっている場合は、すべての依存ディスクがバックアップに含まれていることを確認します。 ボリューム内のすべての依存ディスクをバックアップしないと、復元中に、一部のバックアップされていないディスクで構成されたボリュームが作成されません。

### <a name="backup-os-disk-only-in-the-azure-portal"></a>Azure portal で OS ディスクのみをバックアップする

Azure portal を使用してバックアップを有効にした場合は、**OS ディスクのみバックアップ** オプションを選択できます。 そのため、OS ディスクとともに Azure VM のバックアップを構成し、それに接続されているすべてのデータ ディスクを除外することができます。

![OS ディスクのみのバックアップを構成する](./media/selective-disk-backup-restore/configure-backup-operating-system-disk.png)

## <a name="using-azure-rest-api"></a>Azure REST API の使用

いくつかのディスクを選択して Azure VM のバックアップを構成するか、または既存の VM の保護を変更して、[こちら](backup-azure-arm-userestapi-backupazurevms.md#excluding-disks-in-azure-vm-backup)で説明されているようにいくつかのディスクを含めたり除外したりすることができます。

## <a name="selective-disk-restore"></a>選択的なディスクの復元

選択的なディスクの復元は、選択的なディスク バックアップ機能を有効にすると得られる追加機能です。 この機能を使用すると、復旧ポイントでバックアップされたすべてのディスクからディスクを選択的に復元できます。 これはより効率的で、どのディスクを復元する必要があるかを把握しているシナリオで時間を節約できます。

- OS ディスクは VM のバックアップと復元に既定で含まれており、除外することはできません。
- 選択的なディスクの復元は、ディスクの除外機能が有効になった後に作成された復旧ポイントに対してのみサポートされます。
- ディスクの除外設定を **[オン]** にしたバックアップでは、**ディスクの復元** オプションのみがサポートされます。 この場合、**VM の復元** または **既存のものを置き換える** 復元オプションはサポートされません。

![VM の復元および既存のものを置き換えるオプションは、復元操作中には使用できません](./media/selective-disk-backup-restore/options-not-available.png)

## <a name="limitations"></a>制限事項

選択的なディスクのバックアップ機能は、従来の仮想マシンおよび暗号化された仮想マシンではサポートされません。 そのため、Windows VM の暗号用の BitLocker を使用して Azure Disk Encryption (ADE) で暗号化された Azure VM と、Linux VM 用の dm-crypt 機能はサポートされません。

**新しい VM の作成** および **既存のものを置き換える** 復元オプションは、選択的なディスクのバックアップ機能が有効になっている VM ではサポートされません。

現在、Azure VM バックアップでは、Ultra Disk または共有ディスクが接続されている VM はサポートされていません。 ディスクを除外して VM をバックアップするこのような場合は、選択的ディスク バックアップを使用することはできません。

## <a name="billing"></a>課金

Azure 仮想マシンのバックアップは、既存の価格モデルに従います。詳細については[こちら](https://azure.microsoft.com/pricing/details/backup/)を参照してください。

**OS ディスクのみ** のオプションを使用してバックアップすることを選択した場合、**保護されたインスタンス (PI) のコスト** は OS ディスクに対してのみ計算されます。  バックアップを構成し、少なくとも 1 つのデータ ディスクを選択すると、その VM に接続されているすべてのディスクに対して PI コストが計算されます。 **バックアップ ストレージのコスト** は、含まれているディスクのみに基づいて計算されるので、ストレージ コストを節約できます。 **スナップショット コスト** は、常に VM 内のすべてのディスク (含まれているディスクと除外されているディスクの両方) に対して計算されます。

リージョンをまたがる復元 (CRR) 機能を選択した場合は、ディスクを除外した後に、[CRR 価格](https://azure.microsoft.com/pricing/details/backup/) がバックアップ ストレージのコストに適用されます。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="how-is-protected-instance-pi-cost-calculated-for-only-os-disk-backup-in-windows-and-linux"></a>Windows と Linux では、OS ディスクのバックアップのみに対する保護されたインスタンス (PI) のコストはどのように計算されますか?

PI コストは、VM の実際の (使用されている) サイズに基づいて計算されます。

- Windows の場合:使用領域の計算は、オペレーティング システムが格納されているドライブ (通常は C:) に基づいています。
- Linux の場合:使用領域の計算は、ルート ファイルシステム (/) がマウントされているデバイスに基づいています。

### <a name="i-have-configured-only-os-disk-backup-why-is-the-snapshot-happening-for-all-the-disks"></a>OS ディスク バックアップのみを構成しましたが、すべてのディスクでスナップショットが発生するのはなぜですか?

選択的ディスク バックアップ機能を使用すると、バックアップの一部である含まれているディスクのセキュリティを強化することで、バックアップ コンテナーのストレージ コストを節約できます。 ただし、スナップショットは、VM に接続されているすべてのディスクに対して取得されます。 そのため、スナップショット コストは、常に VM 内のすべてのディスク (含まれているディスクと除外されているディスクの両方) に対して計算されます。 詳細については、「[課金](#billing)」を参照してください。

### <a name="i-cant-configure-backup-for-the-azure-virtual-machine-by-excluding-ultra-disk-or-shared-disks-attached-to-the-vm"></a>VM に接続されている Ultra Disk または共有ディスクを除外して、Azure 仮想マシンのバックアップを構成できません

選択的ディスク バックアップ機能は、Azure 仮想マシンのバックアップ ソリューションの上に提供される機能です。 現在、Azure VM バックアップでは、Ultra Disk または共有ディスクがアタッチされている VM はサポートされていません。

## <a name="next-steps"></a>次のステップ

- [Azure VM バックアップのサポート マトリックス](backup-support-matrix-iaas.md)
- [よく寄せられる質問 - Azure VM のバックアップ](backup-azure-vm-backup-faq.yml)
