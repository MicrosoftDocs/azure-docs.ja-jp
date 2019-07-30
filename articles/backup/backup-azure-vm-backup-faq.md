---
title: Azure Backup による Azure VM のバックアップについてよく寄せられる質問
description: Azure Backup による Azure VM のバックアップに関する一般的な質問への回答。
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: sogup
ms.openlocfilehash: a26cc2fcdc381361912085b0980a736f040c5b0a
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68465289"
---
# <a name="frequently-asked-questions-back-up-azure-vms"></a>よく寄せられる質問 - Azure VM のバックアップ

この記事では、[Azure Backup](backup-introduction-to-azure-backup.md) サービスによる Azure VM のバックアップについてよくある質問にお答えします。


## <a name="backup"></a>バックアップ

### <a name="which-vm-images-can-be-enabled-for-backup-when-i-create-them"></a>作成時にバックアップの対象として有効にできるのは、どの VM イメージですか。
VM を作成するとき、[サポートされているオペレーティング システム](backup-support-matrix-iaas.md#supported-backup-actions)を実行している VM に対してバックアップを有効にできます。

### <a name="is-the-backup-cost-included-in-the-vm-cost"></a>バックアップのコストは VM のコストに含まれますか。

いいえ。 バックアップのコストは VM のコストとは別です。 [Azure Backup の価格については、こちらを参照してください](https://azure.microsoft.com/pricing/details/backup/)。

### <a name="which-permissions-are-required-to-enable-backup-for-a-vm"></a>VM のバックアップを有効にするにはどのアクセス許可が必要ですか。

VM の共同作成者の場合は、VM でバックアップを有効にできます。 カスタム ロールを使用している場合、VM でバックアップを有効にするには、次のアクセス許可が必要です。

- Microsoft.RecoveryServices/Vaults/write
- Microsoft.RecoveryServices/Vaults/read
- Microsoft.RecoveryServices/locations/*
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write
- Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write
- Microsoft.RecoveryServices/Vaults/backupPolicies/read
- Microsoft.RecoveryServices/Vaults/backupPolicies/write

Recovery Services コンテナーと VM が異なるリソース グループに属している場合は、Recovery Services コンテナーのリソース グループへの書き込みアクセス許可があることを確認してください。  


### <a name="does-an-on-demand-backup-job-use-the-same-retention-schedule-as-scheduled-backups"></a>オンデマンド バックアップ ジョブのリテンション期間のスケジュールは、スケジュールされたバックアップと同じですか。
いいえ。 オンデマンド バックアップ ジョブのリテンション期間を指定してください。 ポータルからトリガーした場合、既定では 30 日間保存されます。

### <a name="i-recently-enabled-azure-disk-encryption-on-some-vms-will-my-backups-continue-to-work"></a>最近いくつかの VM で Azure Disk Encryption を有効にしました。 既存のバックアップは今後も正常に機能しますか。
Azure Backup に Key Vault へのアクセス許可を付与してください。 [Azure Backup PowerShell](backup-azure-vms-automation.md) ドキュメントの**バックアップの有効化**に関するセクションの説明に従って、PowerShell でアクセス許可を指定します。

### <a name="i-migrated-vm-disks-to-managed-disks-will-my-backups-continue-to-work"></a>VM ディスクをマネージド ディスクに移行しました。 既存のバックアップは今後も正常に機能しますか。
はい。バックアップはシームレスに機能します。 何も再構成する必要はありません。

### <a name="why-cant-i-see-my-vm-in-the-configure-backup-wizard"></a>バックアップ構成ウィザードに VM が表示されていません。なぜでしょうか。
ウィザードにはコンテナーと同じリージョンで、まだバックアップされていない VM のみが表示されます。

### <a name="my-vm-is-shut-down-will-an-on-demand-or-a-scheduled-backup-work"></a>VM がシャットダウンされています。 オンデマンドまたはスケジュールされたバックアップは機能しますか。
はい。 マシンがシャットダウンされていても、バックアップは動作します。 復旧ポイントはクラッシュ整合としてマークされます。

### <a name="can-i-cancel-an-in-progress-backup-job"></a>進行中のバックアップ ジョブを取り消すことはできますか。
はい。 **スナップショット作成**状態のバックアップ ジョブは取り消すことができます。 スナップショットからのデータ転送が進行中である場合は、ジョブを取り消せません。

### <a name="i-enabled-lock-on-resource-group-created-by-azure-backup-service-ie-azurebackuprggeonumber-will-my-backups-continue-to-work"></a>Azure Backup サービスによって作成されたリソース グループ ( `AzureBackupRG_<geo>_<number>`) に対してロックを有効にしました。バックアップは引き続き動作しますか。
Azure Backup サービスによって作成されたリソース グループをロックする場合は、復元ポイントの最大限度が 18 であるため、バックアップは失敗し始めます。

将来のバックアップを成功させるために、ユーザーはロックを解除しリソース グループからの復元ポイント コレクションをクリアする必要があります。その場合は[次の手順に従って](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#clean-up-restore-point-collection-from-azure-portal)復元ポイント コレクションを削除します。


### <a name="does-azure-backup-support-standard-ssd-managed-disk"></a>Azure Backup は Standard SSD マネージド ディスクをサポートしていますか。
Azure Backup では、[Standard SSD マネージド ディスク](https://azure.microsoft.com/blog/announcing-general-availability-of-standard-ssd-disks-for-azure-virtual-machine-workloads/)がサポートされています。 SSD マネージド ディスクは、Azure VM に対して新しい種類の永続ストレージを提供します。 SSD マネージド ディスクのサポートは、[インスタント リストア](backup-instant-restore-capability.md)で提供されています。

### <a name="can-we-back-up-a-vm-with-a-write-accelerator-wa-enabled-disk"></a>書き込みアクセラレータ (WA) 対応ディスクを使用して VM をバックアップできますか。
WA 対応ディスクでスナップショットを作成することはできません。 ただし、Azure Backup サービスでは、WA 対応ディスクをバックアップから除外できます。

### <a name="i-have-a-vm-with-write-accelerator-wa-disks-and-sap-hana-installed-how-do-i-back-up"></a>書き込みアクセラレータ (WA) ディスク と SAP HANA がインストールされている VM があります。 バックアップするには、どうすればよいですか。
Azure Backup では WA 対応ディスクをバックアップできませんが、バックアップから除外することはできます。 ただし、バックアップによってデータベース整合性が維持されなくなります。WA 対応ディスクの情報がバックアップされないためです。 オペレーティング システム ディスクのバックアップ、および WA 対応ではないディスクのバックアップが必要な場合は、この構成でディスクをバックアップできます。

RPO が 15 分の SAP HANA バックアップに対するプライベート プレビューを実行中です。 このプレビューは同様の方法で SQL DB バックアップに組み込まれていて、backInt インターフェイスが、SAP HANA 認定のサード パーティ ソリューションに対して使用されています。 関心をお持ちの場合は、"**Azure VM での SAP HANA バックアップのプライベート プレビューへのサインアップ**" という件名で、`AskAzureBackupTeam@microsoft.com` 宛てにメールでご連絡ください。

### <a name="what-is-the-maximum-delay-i-can-expect-in-backup-start-time-from-the-scheduled-backup-time-i-have-set-in-my-vm-backup-policy"></a>自分の VM バックアップ ポリシーで設定した、スケジュールされたバックアップ時刻からバックアップ開始時刻までの最大遅延時間はどれぐらいですか。
スケジュールされたバックアップは、スケジュールされたバックアップ時刻から 2 時間以内にトリガーされます。 たとえば、 100 個の VM のバックアップ開始時刻が午前 2 時にスケジュールされている場合、最長で午前 4 時までに 100 個の VM すべてのバックアップ ジョブが進行中になります。 スケジュールされたバックアップが停電のために一時停止され、再開または再試行された場合、バックアップはこのスケジュールされた 2 時間の枠外で開始される可能性があります。

### <a name="what-is-the-minimum-allowed-retention-range-for-daily-backup-point"></a>毎日のバックアップ ポイントに許容される最小リテンション期間の範囲はどれぐらいですか。
Azure Virtual Machine のバックアップ ポリシーでは、最小リテンション期間の範囲として 7 日から 9999 日までがサポートされています。 7 日間未満に設定された既存の VM バックアップ ポリシーを変更するには、7 日間という最小リテンション期間の範囲を満たすように更新する必要があります。

## <a name="restore"></a>復元

### <a name="how-do-i-decide-whether-to-restore-disks-only-or-a-full-vm"></a>ディスクのみを復元するか、VM 全体を復元するかは、どのように判断すればよいでしょうか。
VM の復元は、Azure VM 用の簡易的な作成オプションと考えてください。 このオプションでは、ディスク名、そのディスクが使用するコンテナー、パブリック IP アドレス、およびネットワーク インターフェイス名が変更されます。 この変更では、VM 作成時の一意のリソースが維持されます。 VM は可用性セットには追加されません。

ディスクの復元オプションは、次のような場合に使用できます。
  * 作成された VM をカスタマイズする。 たとえば、サイズを変更します。
  * バックアップ時に存在しなかった構成設定を追加する。
  * 作成されたリソースの名前付け規則を制御する。
  * VM を可用性セットに追加する。
  * PowerShell またはテンプレートを使用して構成する必要があるその他の設定を追加する

### <a name="can-i-restore-backups-of-unmanaged-vm-disks-after-i-upgrade-to-managed-disks"></a>マネージド ディスクへのアップグレード後に、アンマネージド VM ディスクのバックアップを復元できますか。
はい。ディスクをアンマネージドからマネージドに移行する前に作成したバックアップを使用できます。
- 既定では、VM 復元ジョブによって、アンマネージド VM が作成されます。
- ただし、ディスクを復元して、それをマネージド VM の作成に使用することもできます。

### <a name="how-do-i-restore-a-vm-to-a-restore-point-before-the-vm-was-migrated-to-managed-disks"></a>VM がマネージド ディスクに移行されたときよりも前の復元ポイントにその VM を復元するには、どうすればよいですか。
既定では、VM の復元ジョブでは、アンマネージド ディスクで VM が作成されます。 マネージド ディスクで VM を作成するには、次の手順に従います。
1. [アンマネージド ディスクに復元します](tutorial-restore-disk.md#restore-a-vm-disk)。
2. [復元したディスクをマネージド ディスクに変換します](tutorial-restore-disk.md#convert-the-restored-disk-to-a-managed-disk)。
3. [マネージド ディスクで VM を作成します](tutorial-restore-disk.md#create-a-vm-from-the-restored-disk)。

PowerShell でこれを行う方法の詳細については、[こちら](backup-azure-vms-automation.md#restore-an-azure-vm)をご覧ください。

### <a name="can-i-restore-the-vm-thats-been-deleted"></a>削除された VM を復元できますか。
はい。 VM を削除しても、コンテナー内の対応するバックアップ項目に移動して、復旧ポイントから復元できます。

### <a name="how-to-restore-a-vm-to-the-same-availability-sets"></a>同じ可用性セットに VM を復元するには、どうすればよいですか。
マネージド ディスクの Azure VM については、マネージド ディスクとして復元しているときに、可用性セットへの復元オプションがテンプレートに提供されます。 このテンプレートには、**可用性セット**と呼ばれる入力パラメーターがあります。

### <a name="how-do-we-get-faster-restore-performances"></a>復元のパフォーマンスを高めるには、どうすればよいですか。
復元のパフォーマンスを高めるために、[インスタント リストア](backup-instant-restore-capability.md)機能に移行しています。

## <a name="manage-vm-backups"></a>VM バックアップの管理

### <a name="what-happens-if-i-modify-a-backup-policy"></a>バックアップ ポリシーを変更した場合は、どのようになりますか。
VM のバックアップは、変更されたポリシーまたは新しいポリシーのスケジュールおよびリテンション期間の設定を使用して実行されます。

- リテンション期間が延長された場合、既存の復旧ポイントは、新しいポリシーに従ってマーキングおよび保持されます。
- リテンション期間が短縮された場合、復旧ポイントは、次回のクリーンアップ ジョブで排除対象としてマーキングされて、その後削除されます。

### <a name="how-do-i-move-a-vm-backed-up-by-azure-backup-to-a-different-resource-group"></a>Azure Backup でバックアップされた VM を別のリソース グループに移動するには、どうすればよいですか。

1. バックアップを一時的に停止し、バックアップ データを保持します。
2. VM をターゲット リソース グループに移動します。
3. バックアップを同じコンテナーまたは新しいコンテナーで再度有効にします。

VM は、移動操作の前に作成された使用可能な復元ポイントから復元できます。

### <a name="is-there-a-limit-on-number-of-vms-that-can-beassociated-with-a-same-backup-policy"></a>同じバックアップ ポリシーに関連付けることができる VM 数の上限はありますか。
はい。ポータルから同じバックアップ ポリシーに関連付けることができる VM は最大 100 個です。 VM が 100 個を上回る場合、同じスケジュールまたは異なるスケジュールで複数のバックアップ ポリシーを作成することをお勧めします。
