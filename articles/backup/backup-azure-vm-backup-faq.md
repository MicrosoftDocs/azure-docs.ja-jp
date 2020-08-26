---
title: FAQ-Azure VM をバックアップする
description: この記事では、Azure Backup サービスを使用した Azure VM のバックアップに関する一般的な質問への回答を示します。
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 09/17/2019
ms.openlocfilehash: 03e2f004fa54ee235eabc49afd6abd7532a6ee44
ms.sourcegitcommit: 64ad2c8effa70506591b88abaa8836d64621e166
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/17/2020
ms.locfileid: "88262774"
---
# <a name="frequently-asked-questions-back-up-azure-vms"></a>よく寄せられる質問 - Azure VM のバックアップ

この記事では、[Azure Backup](./backup-overview.md) サービスによる Azure VM のバックアップについてよくある質問にお答えします。

## <a name="backup"></a>バックアップ

### <a name="which-vm-images-can-be-enabled-for-backup-when-i-create-them"></a>作成時にバックアップの対象として有効にできるのは、どの VM イメージですか。

VM を作成するときに、[サポートされているオペレーティング システム](backup-support-matrix-iaas.md#supported-backup-actions)を実行している VM に対してバックアップを有効にできます。

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

はい。 **スナップショットの作成中**状態のバックアップ ジョブは取り消すことができます。 スナップショットからのデータ転送が進行中である場合は、ジョブを取り消せません。

### <a name="i-enabled-a-lock-on-the-resource-group-created-by-azure-backup-service-for-example-azurebackuprg_geo_number-will-my-backups-continue-to-work"></a>Azure Backup サービスによって作成されたリソース グループ (例: `AzureBackupRG_<geo>_<number>`) に対するロックを有効にしました。 既存のバックアップは今後も正常に機能しますか。

Azure Backup サービスによって作成されたリソース グループをロックする場合は、復元ポイントの最大限度が 18 であるため、バックアップは失敗し始めます。

今後のバックアップを成功させるために、ロックを解除し、そのリソース グループからの復元ポイント コレクションをクリアします。 復元ポイント コレクションをクリアするには、[こちらの手順に従います](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#clean-up-restore-point-collection-from-azure-portal)。

### <a name="does-azure-backup-support-standard-ssd-managed-disks"></a>Azure Backup は Standard SSD マネージド ディスクをサポートしていますか。

はい、Azure Backup では、[Standard SSD マネージド ディスク](https://azure.microsoft.com/blog/announcing-general-availability-of-standard-ssd-disks-for-azure-virtual-machine-workloads/)がサポートされています。

### <a name="can-we-back-up-a-vm-with-a-write-accelerator-wa-enabled-disk"></a>書き込みアクセラレータ (WA) 対応ディスクを使用して VM をバックアップできますか。

WA 対応ディスクでスナップショットを作成することはできません。 ただし、Azure Backup サービスでは、WA 対応ディスクをバックアップから除外できます。

### <a name="i-have-a-vm-with-write-accelerator-wa-disks-and-sap-hana-installed-how-do-i-back-up"></a>書き込みアクセラレータ (WA) ディスク と SAP HANA がインストールされている VM があります。 バックアップするには、どうすればよいですか。

Azure Backup では WA 対応ディスクをバックアップできませんが、バックアップから除外することはできます。 ただし、バックアップによってデータベース整合性が維持されなくなります。WA 対応ディスクの情報がバックアップされないためです。 オペレーティング システム ディスクのバックアップ、および WA 対応ではないディスクのバックアップが必要な場合は、この構成でディスクをバックアップできます。

Azure Backup は、SAP HANA データベース用に RPO が 15 分のストリーミング バックアップ ソリューションを提供します。 これは、SAP による Backint 認定がされており、SAP HANA のネイティブ API を活用してネイティブ バックアップ サポートを提供します。 [Azure VM での SAP HANA データベースのバックアップ](./sap-hana-db-about.md)について参照してください。

### <a name="what-is-the-maximum-delay-i-can-expect-in-backup-start-time-from-the-scheduled-backup-time-i-have-set-in-my-vm-backup-policy"></a>自分の VM バックアップ ポリシーで設定した、スケジュールされたバックアップ時刻からバックアップ開始時刻までの最大遅延時間はどれぐらいですか。

スケジュールされたバックアップは、スケジュールされたバックアップ時刻から 2 時間以内にトリガーされます。 たとえば、100 個の VM のバックアップ開始時刻が午前 2 時にスケジュールされている場合は、遅くとも午前 4 時までに 100 個のすべての VM のバックアップ ジョブが進行中になります。 スケジュールされたバックアップが停電のために一時停止され、再開または再試行された場合は、バックアップがこのスケジュールされた 2 時間の枠外で開始される可能性があります。

### <a name="what-is-the-minimum-allowed-retention-range-for-a-daily-backup-point"></a>毎日のバックアップ ポイントに許容される最小リテンション期間の範囲はどれぐらいですか。

Azure Virtual Machine のバックアップ ポリシーでは、最小リテンション期間の範囲として 7 日から 9999 日までがサポートされています。 7 日間未満に設定された既存の VM バックアップ ポリシーを変更するには、7 日間という最小リテンション期間の範囲を満たすように更新する必要があります。

### <a name="what-happens-if-i-change-the-case-of-the-name-of-my-vm-or-my-vm-resource-group"></a>VM または VM リソース グループの名前の大文字と小文字を変更するとどうなりますか。

VM または VM リソース グループの大文字と小文字を変更しても、バックアップ項目の名前の大文字と小文字は変更されません。 ただし、これは Azure Backup の想定される動作です。 大文字と小文字の変更はバックアップ項目には反映されませんが、バックエンドで更新されます。

### <a name="can-i-back-up-or-restore-selective-disks-attached-to-a-vm"></a>VM に接続されているディスクを選択的にバックアップまたは復元できますか。

Azure Backup では、Azure 仮想マシン バックアップ ソリューションを使用した選択的ディスク バックアップと復元がサポートされるようになりました。 詳細については、「[選択的ディスク バックアップと Azure VM の復元](selective-disk-backup-restore.md)」を参照してください。

### <a name="are-managed-identities-preserved-if-a-tenant-change-occurs-during-backup"></a>バックアップ中にテナントの変更が発生した場合、マネージド ID は保持されますか。

[テナントの変更](https://docs.microsoft.com/azure/devops/organizations/accounts/change-azure-ad-connection)が発生した場合、バックアップを再び機能させるには、[マネージド ID](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) を無効にしてから再度有効にする必要があります。

## <a name="restore"></a>復元

### <a name="how-do-i-decide-whether-to-restore-disks-only-or-a-full-vm"></a>ディスクのみを復元するか、VM 全体を復元するかは、どのように判断すればよいでしょうか。

VM の復元は、Azure VM 用の簡易的な作成オプションと考えてください。 このオプションでは、ディスク名、そのディスクが使用するコンテナー、パブリック IP アドレス、およびネットワーク インターフェイス名が変更されます。 この変更では、VM 作成時の一意のリソースが維持されます。 VM は可用性セットには追加されません。

ディスクの復元オプションは、次のような場合に使用できます。

- 作成された VM をカスタマイズする。 たとえば、サイズを変更します。
- バックアップ時に存在しなかった構成設定を追加する。
- 作成されたリソースの名前付け規則を制御する。
- VM を可用性セットに追加する。
- PowerShell またはテンプレートを使用して構成する必要があるその他の設定を追加する

### <a name="can-i-restore-backups-of-unmanaged-vm-disks-after-i-upgrade-to-managed-disks"></a>マネージド ディスクへのアップグレード後に、アンマネージド VM ディスクのバックアップを復元できますか。

はい。ディスクをアンマネージドからマネージドに移行する前に作成したバックアップを使用できます。

### <a name="how-do-i-restore-a-vm-to-a-restore-point-before-the-vm-was-migrated-to-managed-disks"></a>VM がマネージド ディスクに移行されたときよりも前の復元ポイントにその VM を復元するには、どうすればよいですか。

復元プロセスは同じままです。 復旧ポイントが VM にアンマネージド ディスクが存在していた時点のものである場合は、[ディスクをアンマネージドとして復元](tutorial-restore-disk.md#unmanaged-disks-restore)できます。 その時点で VM にマネージド ディスクが存在していた場合は、[ディスクをマネージド ディスクとして復元](tutorial-restore-disk.md#managed-disk-restore)できます。 その後、[これらのディスクから VM を作成](tutorial-restore-disk.md#create-a-vm-from-the-restored-disk)できます。

PowerShell でこれを行う方法の詳細については、[こちら](backup-azure-vms-automation.md#restore-an-azure-vm)をご覧ください。

### <a name="if-the-restore-fails-to-create-the-vm-what-happens-to-the-disks-included-in-the-restore"></a>復元によって VM の作成に失敗した場合、復元に含まれるディスクはどうなりますか。

マネージド VM の復元が発生した場合、VM の作成に失敗した場合でも、ディスクは復元されます。

### <a name="can-i-restore-a-vm-thats-been-deleted"></a>削除された VM を復元することはできますか。

はい。 VM を削除しても、コンテナー内の対応するバックアップ項目に移動して、復元ポイントから復元できます。

### <a name="how-do-i-restore-a-vm-to-the-same-availability-sets"></a>同じ可用性セットに VM を復元するには、どうすればよいですか。

マネージド ディスクの Azure VM については、マネージド ディスクとして復元しているときに、可用性セットへの復元オプションがテンプレートに提供されます。 このテンプレートには、**可用性セット**と呼ばれる入力パラメーターがあります。

### <a name="how-do-we-get-faster-restore-performances"></a>復元のパフォーマンスを高めるには、どうすればよいですか。

[インスタント リストア](backup-instant-restore-capability.md)機能は、迅速なバックアップと、スナップショットからの即時のリストアに役立ちます。

### <a name="what-happens-when-we-change-the-key-vault-settings-for-the-encrypted-vm"></a>暗号化された VM のキー コンテナーの設定を変更するとどうなりますか。

暗号化された VM のキー コンテナーの設定を変更した後も、バックアップは新しい詳細情報のセットで引き続き動作します。 ただし、変更前の復元ポイントからの復元後は、キー コンテナー内のシークレットを復元してから VM を作成する必要があります。 詳細については、[こちらの記事](./backup-azure-restore-key-secret.md)を参照してください。

シークレットまたはキーのロールオーバーなどの操作では、この手順は必要ありません。復元後も同じキー コンテナーを使用できます。

### <a name="can-i-access-the-vm-once-restored-due-to-a-vm-having-broken-relationship-with-domain-controller"></a>VM とドメイン コントローラーとの関係が壊れたために復元された VM にアクセスできますか。

はい。VM とドメイン コントローラーとの関係が壊れたために復元された VM にアクセスできます。 詳細については、こちらの[記事](./backup-azure-arm-restore-vms.md#post-restore-steps)を参照してください

## <a name="manage-vm-backups"></a>VM バックアップの管理

### <a name="what-happens-if-i-modify-a-backup-policy"></a>バックアップ ポリシーを変更した場合は、どのようになりますか。

VM のバックアップは、変更されたポリシーまたは新しいポリシーのスケジュールおよびリテンション期間の設定を使用して実行されます。

- リテンション期間が延長された場合、既存の復旧ポイントは、新しいポリシーに従ってマーキングおよび保持されます。
- リテンション期間が短縮された場合、復旧ポイントは、次回のクリーンアップ ジョブで排除対象としてマーキングされて、その後削除されます。

### <a name="how-do-i-move-a-vm-backed-up-by-azure-backup-to-a-different-resource-group"></a>Azure Backup でバックアップされた VM を別のリソース グループに移動するには、どうすればよいですか。

1. バックアップを一時的に停止し、バックアップ データを保持します。
2. Azure Backup で構成された仮想マシンを移動するには、次の手順を実行します。

   1. 仮想マシンの場所を探します。
   2. 名前付けパターン `AzureBackupRG_<location of your VM>_1` を持つリソース グループを探します。 たとえば、*AzureBackupRG_westus2_1* となります
   3. Azure portal で、 **[非表示の型の表示]** をオンにします。
   4. `AzureBackup_<name of your VM that you're trying to move>_###########` という名前パターンを持つ、**Microsoft.Compute/restorePointCollections** 型のリソースを検索します。
   5. このリソースを削除します。 この操作では、インスタント復旧ポイントのみが削除され、コンテナー内のバックアップされたデータは削除されません。
   6. 削除操作が完了したら、仮想マシンを移動できます。

3. VM をターゲット リソース グループに移動します。
4. バックアップを再開します。

VM は、移動操作の前に作成された使用可能な復元ポイントから復元できます。

### <a name="what-happens-after-i-move-a-vm-to-a-different-resource-group"></a>VM を別のリソース グループに移動した後は、どのようになりますか。

VM が別のリソース グループに移動されると、Azure Backup からは新しい VM であると認識されます。

VM を新しいリソース グループに移動した後は、その VM を同じコンテナーで、または別のコンテナーで再度保護することができます。 Azure Backup から見るとこれは新しい VM であるため、別個に課金されます。

古い VM の復元ポイントは、必要な場合は復元のために使用できるようになります。 このバックアップ データが不要な場合は、データの削除によって古い VM の保護を停止できます。

### <a name="is-there-a-limit-on-number-of-vms-that-can-beassociated-with-the-same-backup-policy"></a>同じバックアップ ポリシーに関連付けることができる VM 数の上限はありますか。

はい。ポータルから同じバックアップ ポリシーに関連付けることができる VM は最大 100 個です。 VM が 100 個を上回る場合、同じスケジュールまたは異なるスケジュールで複数のバックアップ ポリシーを作成することをお勧めします。

### <a name="how-can-i-view-the-retention-settings-for-my-backups"></a>バックアップの保有期間の設定を表示するにはどうすればよいですか。

現在、保有期間の設定は、VM に割り当てられているバックアップ ポリシーに基づいて、バックアップ項目 (VM) レベルで表示できます。 

バックアップの保有期間の設定を表示する方法の 1 つとして、Azure portal で VM のバックアップ項目の[ダッシュボード](https://docs.microsoft.com/azure/backup/backup-azure-manage-vms#view-vms-on-the-dashboard)にアクセスする方法があります。 バックアップ ポリシーへのリンクをクリックすると、その VM に関連付けられているすべての日、週、月、および年単位の保有ポイントの保有期間を表示できます。

また、[バックアップ エクスプローラー](https://docs.microsoft.com/azure/backup/monitor-azure-backup-with-backup-explorer)を使用して、すべての VM の保有期間の設定を 1 つのウィンドウに表示することもできます。 任意の Recovery Services コンテナーからバックアップ エクスプローラーに移動し、 **[バックアップ項目]** タブに移動して [詳細ビュー] を選択して、各 VM の詳細な保有期間の情報を確認します。  