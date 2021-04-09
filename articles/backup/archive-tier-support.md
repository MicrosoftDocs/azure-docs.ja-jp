---
title: アーカイブ層のサポート (プレビュー)
description: Azure Backup のアーカイブ層のサポートについて説明します
ms.topic: conceptual
ms.date: 02/18/2021
ms.openlocfilehash: 6c597d640f24dc4c680bfd5db16f9df09017ee54
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/10/2021
ms.locfileid: "102609854"
---
# <a name="archive-tier-support-preview"></a>アーカイブ層のサポート (プレビュー)

お客様は、組織のコンプライアンス ルールによって定義されたデータ保有のニーズがある長期保有 (LTR) バックアップ データを含むバックアップ データを保存するために Azure Backup を使用しています。 ほとんどの場合、古いバックアップ データにアクセスすることはめったになく、コンプライアンスのニーズのためだけに保存されています。

Azure Backup は、スナップショットと Standard 層に加えて、アーカイブ層での長期保有ポイントのバックアップをサポートしています。

## <a name="scope-for-preview"></a>プレビューのスコープ

サポートされているワークロード:

- Azure の仮想マシン
  - 月単位および年単位の復旧ポイントトのみ。 日単位および週単位の復旧ポイントはサポートされていません。
  - 期間 >= 3 か月 (Vault-Standard 層)
  - 保有期間 >= 6 か月
  - アクティブな日単位および週単位の依存関係なし
- Azure 仮想マシンにおける SQL Server
  - 完全復旧ポイントのみ。 ログと差分はサポートされていません。
  - 期間 >= 45 日 (Vault-Standard 層)
  - 保有期間 >= 6 か月
  - 依存関係なし

サポートされるクライアント:

- この機能は PowerShell を使用して提供されます。

>[!NOTE]
>Azure VM と Azure VM 内の SQL Server に対するアーカイブ層サポートは、限定パブリック プレビューの段階にあり、新規登録も限定されています。 アーカイブ サポートに新規登録するには、[こちら](https://aka.ms/ArchivePreviewInterestForm)のリンクをご利用ください。

## <a name="get-started-with-powershell"></a>PowerShell の使用を開始する

1. PowerShell で次のコマンドを実行します。
  
    ```azurepowershell
    install-module -name Az.RecoveryServices -Repository PSGallery -RequiredVersion 4.0.0-preview -AllowPrerelease -force
    ```

1. [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) コマンドレットを使用して Azure に接続します。
1. サブスクリプションにサインインします。

   `Set-AzContext -Subscription "SubscriptionName"`

1. コンテナーの取得:

    `$vault =  Get-AzRecoveryServicesVault -ResourceGroupName "rgName" -Name "vaultName"`

1. バックアップ項目の一覧の取得:

    `$BackupItemList = Get-AzRecoveryServicesBackupItem -vaultId $vault.ID -BackupManagementType "AzureVM/AzureWorkload" -WorkloadType "AzureVM/MSSQL"`

1. バックアップ項目を取得します。

    - Azure 仮想マシンの場合:

        `$bckItm = $BackupItemList | Where-Object {$_.Name -match '<vmName>'}`

    - Azure 仮想マシン内の SQL Server の場合:

        `$bckItm = $BackupItemList | Where-Object {$_.Name -match '<dbName>' -and $_.ContainerName -match '<vmName>'}`

## <a name="use-powershell"></a>PowerShell の使用

### <a name="check-archivable-recovery-points"></a>アーカイブ可能な復旧ポイントを確認する

```azurepowershell
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -VaultId $vault.ID -Item $bckItm  -IsReadyForMove $true -TargetTier VaultArchive
```

これにより、アーカイブに移動する準備ができている特定のバックアップ項目に関連するすべての復旧ポイントが一覧表示されます。

### <a name="check-why-a-recovery-point-cannot-be-moved-to-archive"></a>復旧ポイントをアーカイブに移動できない理由を確認する

```azurepowershell
$rp[0].RecoveryPointMoveReadinessInfo["ArchivedRP"]
```

ここで `$rp[0]` は、アーカイブ可能でない理由を確認する復旧ポイントです。

サンプル出力:

```output
IsReadyForMove  AdditionalInfo
--------------  --------------
False           Recovery-Point Type is not eligible for archive move as it is already moved to archive tier
```

### <a name="check-recommended-set-of-archivable-points-only-for-azure-vms"></a>アーカイブ可能ポイントの推奨セットを確認する (Azure VM の場合のみ)

仮想マシンに関連付けられている復旧ポイントは、本質的には増分です。 特定の復旧ポイントがアーカイブに移動されるときは、完全バックアップに変換されてからアーカイブに移動されます。 そのため、アーカイブへの移動に伴うコスト節約は、データ ソースのチャーンに応じて異なります。

そのため Azure Backup には、まとめて移動した場合にコストを節約できる可能性がある復旧ポイントの推奨セットが用意されています。

>[!NOTE]
>コスト節約はさまざまな理由に依存するため、どの 2 つのインスタンスでも同じになるとは限りません。

```azurepowershell
$RecommendedRecoveryPointList = Get-AzRecoveryServicesBackupRecommendedArchivableRPGroup -Item $bckItm -VaultId $vault.ID
```

### <a name="move-to-archive"></a>アーカイブに移動する

```azurepowershell
Move-AzRecoveryServicesBackupRecoveryPoint -VaultId $vault.ID -RecoveryPoint $rp[2] -SourceTier VaultStandard -DestinationTier VaultArchive
```

このコマンドは、アーカイブ可能な復旧ポイントをアーカイブに移動します。 これによって返されるジョブを使用して、ポータルと PowerShell の両方から移動操作を追跡することができます。

### <a name="view-archived-recovery-points"></a>アーカイブされた復旧ポイントを表示する

このコマンドは、アーカイブされたすべての復旧ポイントを返します。

```azurepowershell
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -VaultId $vault.ID -Item $bckItm -Tier VaultArchive
```

### <a name="restore-with-powershell"></a>PowerShell で復元する

アーカイブ内の復旧ポイントについては、Azure Backup には統合型の復元方法が用意されています。

統合型の復元は、2 つのステップから成るプロセスです。 最初のステップでは、アーカイブに保管されている復旧ポイントをリハイドレートし、それを一時的に Vault-Standard 層に 10 日から 30 日の期間 (リハイドレート期間とも呼ばれます) 保管します。 既定は 15 日間です。 リハイドレートの優先度には、Standard と High の 2 種類があります。 [リハイドレートの優先度](https://docs.microsoft.com/azure/storage/blobs/storage-blob-rehydration#rehydrate-an-archived-blob-to-an-online-tier)に関する詳細をご覧ください。

>[!NOTE]
>
>- リハイドレート期間は、いったん選択したら変更できません。また、リハイドレートした復旧ポイントは、リハイドレート期間中は Standard 層にとどまります。
>- リハイドレートの追加ステップにはコストが発生します。

Azure 仮想マシン用のさまざまな復元方法について詳しくは、[PowerShell を使用した Azure VVM の復元](backup-azure-vms-automation.md#restore-an-azure-vm)に関するページをご覧ください。

```azurepowershell
Restore-AzRecoveryServicesBackupItem -VaultLocation $vault.Location -RehydratePriority "Standard" -RehydrateDuration 15 -RecoveryPoint $rp -StorageAccountName "SampleSA" -StorageAccountResourceGroupName "SArgName" -TargetResourceGroupName $vault.ResourceGroupName -VaultId $vault.ID
```

SQL Server を復元するには、[これらの手順](backup-azure-sql-automation.md#restore-sql-dbs)に従ってください。 必要な追加パラメーターは、**RehydrationPriority** と **RehydrationDuration** です。

### <a name="view-jobs-from-powershell"></a>ジョブを PowerShell から表示する

移動と復元の各ジョブを表示するには、次の PowerShell コマンドレットを使用します。

```azurepowershell
Get-AzRecoveryServicesBackupJob -VaultId $vault.ID
```

## <a name="use-the-portal"></a>ポータルの使用

### <a name="check-archived-recovery-point"></a>アーカイブされた復旧ポイントを確認する

これで、アーカイブに移動されたすべての復旧ポイントを表示できるようになりました。

![すべての復旧ポイント。](./media/archive-tier-support/restore-points.png)

### <a name="restore-in-the-portal"></a>ポータルで復元する

アーカイブに移動された復旧ポイントの場合、復元するにはリハイドレート期間とリハイドレート優先度のパラメーターを追加する必要があります。

![ポータルで復元します。](./media/archive-tier-support/restore-in-portal.png)

### <a name="view-jobs-in-the-portal"></a>ポータルでジョブを表示する

![ポータルでジョブを表示します。](./media/archive-tier-support/view-jobs-portal.png)

### <a name="modify-protection"></a>保護を変更する

データソースの保護を変更するには、2 つの方法があります。

- 既存のポリシーの変更
- 新しいポリシーを使用したデータソースの保護

どちらの場合も、新しいポリシーは、Standard 層とアーカイブ層にある古い復旧ポイントすべてに適用されます。 そのため、ポリシーに変更があった場合は古い復旧ポイントが削除される可能性があります。

復旧ポイントは、アーカイブに移動されると、180 日間の早期削除期間が適用されます。 料金は日割り計算されます。 アーカイブの期間が 180 日間を経過していなかった復旧ポイントを削除する場合は、180 から Standard 層で経過した日数を差し引いた日数に相当するコストが発生します。

アーカイブの期間が最低 6 か月間を経過していなかった復旧ポイントには、削除時に早期削除コストが発生します。

## <a name="stop-protection-and-delete-data"></a>保護を停止してデータを削除する

保護を停止してデータを削除すると、すべての復旧ポイントが削除されます。 アーカイブ層で 180 日間経過していないアーカイブ内の復旧ポイントについては、復旧ポイントを削除すると早期削除コストが発生します。

## <a name="error-codes-and-troubleshooting-steps"></a>エラー コードとトラブルシューティングの手順

復旧ポイントをアーカイブに移動できないときに発生するいくつかのエラー コードがあります。

### <a name="recoverypointtypenoteligibleforarchive"></a>RecoveryPointTypeNotEligibleForArchive

**エラー メッセージ** - 復旧ポイントの種類がアーカイブ移動の対象外です

**説明** - このエラー コードは、選択した復旧ポイントの種類がアーカイブへの移動資格を持たないときに表示されます。

**推奨される操作** – 復旧ポイントの適格性を [こちら](#scope-for-preview)で確認してください。

### <a name="recoverypointhaveactivedependencies"></a>RecoveryPointHaveActiveDependencies

**エラー メッセージ** - 復元のためのアクティブな依存関係がある復旧ポイントが、アーカイブ移動の対象外です

**説明 –** 選択した復旧ポイントにはアクティブな依存関係があるため、アーカイブに移動できません。

**推奨される操作** – 復旧ポイントの適格性を [こちら](#scope-for-preview)で確認してください。

### <a name="minlifespaninstandardrequiredforarchive"></a>MinLifeSpanInStandardRequiredForArchive

**エラー メッセージ** - Vault-Standard 層で経過した存続期間が必要最小値より短いため、復旧ポイントがアーカイブ移動の対象外です

**説明** – 復旧ポイントは Standard 層で、Azure 仮想マシンの場合は最低 3 か月間、Azure 仮想マシンの SQL Server の場合は 45 日間を経過する必要があります。

**推奨される操作** – 復旧ポイントの適格性を [こちら](#scope-for-preview)で確認してください。

### <a name="minremaininglifespaninarchiverequired"></a>MinRemainingLifeSpanInArchiveRequired

**エラー メッセージ** - 復旧ポイントの残りの存続期間が必要最小値より短いです

**説明** – アーカイブ移動適格性に準拠するために復旧ポイントに必要な最小存続期間は 6 か月です。

**推奨される操作** – 復旧ポイントの適格性を [こちら](#scope-for-preview)で確認してください。

### <a name="usererrorrecoverypointalreadyinarchivetier"></a>UserErrorRecoveryPointAlreadyInArchiveTier

**エラー メッセージ** - 復旧ポイントは、既にアーカイブ層に移動しているため、アーカイブ移動の対象ではありません

**説明** – 選択した復旧ポイントは既にアーカイブ中です。 そのため、アーカイブに移動する資格がありません。

### <a name="usererrordatasourcetypeisnotsupportedforrecommendationapi"></a>UserErrorDatasourceTypeIsNotSupportedForRecommendationApi

**エラー メッセージ** - データソースの種類が Recommendation API の対象ではありません。

**説明** – Recommendation API が適用されるのは Azure 仮想マシンのみです。 選択したデータソースの種類には適用されません。

### <a name="usererrorrecoverypointalreadyrehydrated"></a>UserErrorRecoveryPointAlreadyRehydrated

**エラー メッセージ** - 復旧ポイントが既にリハイドレートされています。 リハイドレートがこの RP では許可されていません。

**説明** – 選択した復旧ポイントは既にリハイドレートされています。

### <a name="usererrorrecoverypointisnoteligibleforarchivemove"></a>UserErrorRecoveryPointIsNotEligibleForArchiveMove

**エラー メッセージ** - 復旧ポイントがアーカイブ移動の対象外です

**説明** - 選択した復旧ポイントはアーカイブ移動の対象ではありません。

### <a name="usererrorrecoverypointnotrehydrated"></a>UserErrorRecoveryPointNotRehydrated

**エラー** **メッセージ** - アーカイブ復旧ポイントがリハイドレートされていません。 アーカイブ RP でリハイドレートの完了後に復元を再試行してください。

**説明** – 復旧ポイントはリハイドレートされていません。 復旧ポイントをリハイドレートした後で復元をお試しください。

### <a name="usererrorrecoverypointrehydrationnotallowed"></a>UserErrorRecoveryPointRehydrationNotAllowed

**エラー** **メッセージ** - リハイドレートは、アーカイブの復旧ポイントでのみサポートされています

**説明** – 選択した復旧ポイントではリハイドレートは許可されていません。

### <a name="usererrorrecoverypointrehydrationalreadyinprogress"></a>UserErrorRecoveryPointRehydrationAlreadyInProgress

**エラー メッセージ** – リハイドレートがアーカイブの復旧ポイントで既に進行中です。

**説明** – 選択した復旧ポイントのリハイドレートは既に進行中です。

### <a name="rpmovenotsupportedduetoinsufficientretention"></a>RPMoveNotSupportedDueToInsufficientRetention

**エラー メッセージ** - ポリシーで指定された保持期間が十分でないため、復旧ポイントをアーカイブ層に移動できません

**推奨される操作** - 保護された項目のポリシーを適切な保持設定で更新してから、もう一度お試しください。

### <a name="rpmovereadinesstobedetermined"></a>RPMoveReadinessToBeDetermined

**エラー メッセージ** - この復旧ポイントを移動できるかどうかをまだ調べています。

**説明** – 復旧ポイントの移動準備状況はまだ確認されていません。

**推奨される操作** - しばらく待ってからもう一度確認してください。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="what-will-happen-to-archive-recovery-points-if-i-stop-protection-and-retain-data"></a>保護を停止してデータを保持すると、アーカイブの復旧ポイントはどうなりますか?

復旧ポイントは、いつまでもアーカイブされたままになります。 詳細については、[復旧ポイントに対する保護の停止の影響](manage-recovery-points.md#impact-of-stop-protection-on-recovery-points)に関するページをご覧ください。

## <a name="next-steps"></a>次のステップ

- [Azure Backup の価格](azure-backup-pricing.md)
