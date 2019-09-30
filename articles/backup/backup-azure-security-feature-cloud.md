---
title: Azure Backup を使用したクラウド ワークロードを保護するためのセキュリティ機能
description: Azure Backup のセキュリティ機能を使用してバックアップのセキュリティを強化する方法について説明します。
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: dacurwin
ms.openlocfilehash: 4fb88cbed4e73a7cea2b0ccf01b1429a3ff321f3
ms.sourcegitcommit: 71db032bd5680c9287a7867b923bf6471ba8f6be
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/16/2019
ms.locfileid: "71018184"
---
# <a name="security-features-to-help-protect-cloud-workloads-that-use-azure-backup"></a>Azure Backup を使用したクラウド ワークロードを保護するためのセキュリティ機能

マルウェア、ランサムウェア、侵入などのセキュリティ問題への懸念が高まっています。 これらのセキュリティ問題は、金銭とデータの両方の観点からコストがかかる可能性があります。 このような攻撃から保護するために、Azure Backup では、削除後もバックアップ データを保護するためのセキュリティ機能が提供されるようになりました。 このような機能の 1 つに、論理的な削除があります。 論理的な削除を使用すると、悪意のあるアクターによって VM のバックアップが削除 (またはバックアップ データが誤って削除) された場合でも、バックアップ データは追加で 14 日間保持されるので、データを失うことなくバックアップ項目を回復できます。 バックアップ データが "論理的な削除" 状態にあるこの追加の 14 日間の保持期間中は、お客様にコストは発生しません。

> [!NOTE]
> 論理的な削除では、削除されたバックアップ データのみが保護されます。 バックアップせずに VM が削除された場合、論理的な削除機能ではデータは保持されません。 完全な復元性を確保するには、すべてのリソースを Azure Backup で保護する必要があります。
>

## <a name="soft-delete"></a>論理的な削除

### <a name="supported-regions"></a>サポートされているリージョン

現在、論理的な削除は、米国中西部、東アジア、カナダ中部、カナダ東部、フランス中部、フランス南部、韓国中部、韓国南部、英国南部、英国西部、オーストラリア東部、オーストラリア南東部、北ヨーロッパ、米国西部、米国西部 2、米国中部、東南アジア、米国中北部、米国中南部、東日本、西日本、インド南部、インド中部、インド西部、米国東部 2、スイス北部、スイス西部など、すべての国のリージョンでサポートされています。

### <a name="soft-delete-for-vms"></a>VM の論理的な削除

1. VM のバックアップ データを削除するには、バックアップを停止する必要があります。 Azure portal で、Recovery Services コンテナーにアクセスし、バックアップ項目を右クリックして、 **[バックアップの停止]** を選択します。

   ![Azure portal の [バックアップ項目] のスクリーンショット](./media/backup-azure-security-feature-cloud/backup-stopped.png)

2. 次のウィンドウでは、バックアップ データを削除するか保持するかを選択できます。 **[バックアップデータの削除]** を選択してから **[バックアップの停止]** を選択した場合、VM のバックアップは完全に削除されません。 もっと正確に言えば、バックアップ データは論理的な削除状態で 14 日間保持されます。 **[バックアップ データの削除]** を選択した場合は、削除の電子メール アラートが構成済みのメール ID に送信され、バックアップ データの保有延長が 14 日間残っていることがユーザーに通知されます。 また、12 日目に、削除されたデータを復元できる期間があと 2 日間残っていることを知らせる電子メール アラートが送信されます。 削除は 15 日目まで延期されます。ここで完全な削除が行われ、データが永久に削除されたことを知らせる最終的な電子メール アラートが送信されます。

   ![Azure portal の [バックアップの停止] 画面のスクリーンショット](./media/backup-azure-security-feature-cloud/delete-backup-data.png)

3. この 14 日間は、Recovery Services コンテナーで、論理的に削除された VM の横に赤い "論理的な削除" アイコンが付けられて表示されます。

   ![論理的な削除状態の VM を示している Azure portal のスクリーンショット](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

   > [!NOTE]
   > 論理的に削除されたバックアップ項目がコンテナーに存在する場合、その時点でコンテナーを削除することはできません。 バックアップ項目が完全に削除され、コンテナーに論理的な削除状態の項目がなくなってから、コンテナーの削除を試してください。

4. 論理的に削除された VM を復元するには、最初に削除を取り消す必要があります。 削除を取り消すには、論理的に削除された VM を選択し、 **[削除の取り消し]** オプションをクリックします。

   ![VM の削除の取り消しを示している Azure portal のスクリーンショット](./media/backup-azure-security-feature-cloud/choose-undelete.png)

   [削除の取り消し] を選択すると、VM のすべての復元ポイントの削除が取り消され、復元操作の実行が可能になるという警告がウィンドウに表示されます。 VM は、"データを保持して保護を停止" の状態で保持され、バックアップは一時停止され、バックアップ データはバックアップ ポリシーが有効になっていない状態で永久に保持されます。

   ![VM の削除の取り消しを確認する Azure portal のスクリーンショット](./media/backup-azure-security-feature-cloud/undelete-vm.png)

   この時点で、選択した復元ポイントから **[VM の復元]** を選択して VM を復元することもできます。  

   ![Azure portal の [VM の復元] オプションのスクリーンショット](./media/backup-azure-security-feature-cloud/restore-vm.png)

   > [!NOTE]
   > ユーザーが **[バックアップの再開]** 操作を実行した場合にのみ、ガベージ コレクターが実行され、期限切れの回復ポイントがクリーンアップされます。

5. 削除の取り消し処理が完了すると、状態は "データを保持して保護を停止" に戻り、 **[バックアップの再開]** を選択できるようになります。 **[バックアップの再開]** 操作により、ユーザー定義のバックアップと保持スケジュールで選択されたバックアップ ポリシーに関連付けられているバックアップ項目が、アクティブ状態に戻ります。

   ![Azure portal の [バックアップの再開] オプションのスクリーンショット](./media/backup-azure-security-feature-cloud/resume-backup.png)

このフロー チャートは、バックアップ項目のさまざまな手順と状態を示しています。

![論理的に削除されたバックアップ項目のライフサイクル](./media/backup-azure-security-feature-cloud/lifecycle.png)

詳細については、後述の「[よく寄せられる質問](backup-azure-security-feature-cloud.md#frequently-asked-questions)」を参照してください。

## <a name="other-security-features"></a>その他のセキュリティ機能

### <a name="storage-side-encryption"></a>ストレージ側の暗号化

Azure Storage では、データはクラウドに永続化されるときに自動的に暗号化されます。 暗号化によってデータは保護され、組織のセキュリティおよびコンプライアンス コミットメントを満たすのに役立ちます。 Azure Storage 内のデータは、利用可能な最強のブロック暗号の 1 つである 256 ビット AES 暗号化を使って透過的に暗号化および暗号化解除され、FIPS 140-2 に準拠しています。 Azure Storage 暗号化は、Windows での BitLocker 暗号化に似ています。 Azure Backup では、データは自動的に暗号化されてから保存されます。 Azure Storage では、取得前にデータを暗号化解除します。  

Azure 内では、Azure ストレージとコンテナー間を転送中のデータは HTTPS によって保護されます。 このデータは、Azure バックボーン ネットワークにとどまります。

詳細については、「[保存データに対する Azure Storage 暗号化](https://docs.microsoft.com/en-in/azure/storage/common/storage-service-encryption)」を参照してください。

### <a name="vm-encryption"></a>VM の暗号化

Azure Backup サービスを使用して、暗号化されたディスクを含む Windows または Linux の Azure 仮想マシン (VM) をバックアップして復元することができます。 手順については、「[暗号化された仮想マシンを Azure Backup でバックアップおよび復元する](https://docs.microsoft.com/en-us/azure/backup/backup-azure-vms-encryption)」をご覧ください。

### <a name="protection-of-azure-backup-recovery-points"></a>Azure Backup 回復ポイントの保護

Recovery Services コンテナーによって使用されるストレージ アカウントは分離されており、悪意のある目的でユーザーがアクセスすることはできません。 アクセスが許可されるのは、復元などの Azure Backup 管理操作だけです。 これらの管理操作は、ロールベースのアクセス制御 (RBAC) によって制御されます。

詳細については、「[ロール ベースのアクセス制御を使用した Azure Backup の回復ポイントの管理](https://docs.microsoft.com/en-us/azure/backup/backup-rbac-rs-vault)」を参照してください。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="soft-delete"></a>論理的な削除

#### <a name="do-i-need-to-enable-the-soft-delete-feature-on-every-vault"></a>すべてのコンテナーで論理的な削除機能を有効にする必要はありますか?

いいえ。これはすべての Recovery Services コンテナーに組み込まれており、既定で有効になっています。

#### <a name="can-i-configure-the-number-of-days-for-which-my-data-will-be-retained-in-soft-deleted-state-after-delete-operation-is-complete"></a>削除操作の完了後、データが論理的に削除された状態で保持される日数を構成できますか?

いいえ。削除操作後、追加の保持期間は 14 日間に固定されています。
 
#### <a name="do-i-need-to-pay-the-cost-for-this-additional-14-day-retention"></a>この 14 日間の保持期間に対するコストを支払う必要がありますか?

いいえ。この 14 日間の追加の保持期間は、論理的な削除機能の一部として無料で提供されます。
 
#### <a name="can-i-perform-a-restore-operation-when-my-data-is-in-soft-delete-state"></a>データが論理的な削除状態であるときに復元操作を実行できますか?

いいえ。復元するには、論理的に削除されたリソースの削除を取り消す必要があります。 削除の取り消し操作により、リソースが**データを保持して保護を停止の状態**に戻り、任意の時点に復元できるようになります。 ガベージ コレクターは、この状態で一時停止のままになります。
 
#### <a name="will-my-snapshots-follow-the-same-lifecycle-as-my-recovery-points-in-the-vault"></a>スナップショットは、コンテナー内の回復ポイントと同じライフサイクルに従いますか?

はい。
 
#### <a name="how-can-i-trigger-the-scheduled-backups-again-for-a-soft-deleted-resource"></a>論理的に削除されたリソースに対してスケジュールされたバックアップを再びトリガーするにはどうすればよいですか?

削除の取り消し後に再開操作を実行すると、リソースが再度保護されます。 再開操作により、選択した保持期間でスケジュールされたバックアップをトリガーするため、バックアップ ポリシーが関連付けられます。 また、再開操作が完了するとすぐにガベージ コレクターが実行されます。 有効期限を過ぎた回復ポイントから復元を実行する場合は、再開操作をトリガーする前に実行することをお勧めします。
 
#### <a name="can-i-delete-my-vault-if-there-are-soft-deleted-items-in-the-vault"></a>コンテナー内に論理的に削除された項目がある場合、コンテナーを削除できますか?

Recovery Services コンテナー内に論理的に削除された状態のバックアップ項目がある場合、そのコンテナーを削除することはできません。 論理的に削除された項目は、削除操作の 14 日後に完全に削除されます。 論理的に削除されたすべての項目が消去された場合のみコンテナーを削除できます。  

#### <a name="how-can-i-delete-the-data-earlier-than-the-14-days-soft-delete-period-after-deletion"></a>削除後の 14 日間の論理的な削除期間より前にデータを削除するにはどうすればよいですか?

削除してから 14 日間はデータを消去することはできません。 これが障害またはコンプライアンスの問題になっている場合は、Microsoft サポートにお問い合わせください。

#### <a name="can-soft-delete-operations-be-performed-in-powershell-or-cli"></a>PowerShell または CLI で論理的な削除操作を実行できますか?

いいえ、PowerShell または CLI のサポートは現在使用できません。

#### <a name="is-soft-delete-supported-for-other-cloud-workloads-like-sql-server-in-azure-vms-and-sap-hana-in-azure-vms"></a>他のクラウド ワークロード (Azure VM での SQL Server や Azure VM での SAP HANA など) では、論理的な削除はサポートされていますか?

いいえ。 現在、論理的な削除は Azure 仮想マシンでのみサポートされています。

## <a name="next-steps"></a>次の手順

* [Azure Backup のセキュリティ制御](backup-security-controls.md)を確認する。
