---
title: Azure Files のバックアップに関する FAQ
description: この記事では、Azure ファイル共有を保護する方法について詳しく説明します。
author: dcurwin
ms.author: dacurwin
ms.date: 01/31/2019
ms.topic: tutorial
ms.service: backup
manager: carmonm
ms.openlocfilehash: e3f94badb3af2e3f26a857f27f0b68a78338db92
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68466305"
---
# <a name="questions-about-backing-up-azure-files"></a>Azure Files のバックアップに関する質問
この記事では、Azure Files のバックアップについてよくある質問への回答を示します。 一部の回答は、より詳しい情報を扱った記事にリンクされています。 また、 [ディスカッション フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup)でも、Azure Backup サービスに関する質問を投稿できます。

この記事の各セクションの内容をひととおり確認するには、右側の「**この記事の内容**」にあるリンクをご利用ください。

## <a name="configuring-the-backup-job-for-azure-files"></a>Azure Files のバックアップ ジョブの構成

### <a name="why-cant-i-see-some-of-my-storage-accounts-i-want-to-protect-that-contain-valid-azure-file-shares-br"></a>有効な Azure ファイル共有を含む、保護したいストレージ アカウントの一部が表示されないのはなぜですか。 <br/>
プレビュー期間中は、Azure ファイル共有のバックアップであらゆる種類のストレージ アカウントがサポートされるわけではありません。 サポートされるストレージ アカウントの一覧は、[こちら](troubleshoot-azure-files.md#limitations-for-azure-file-share-backup-during-preview)を参照してください。 探しているストレージ アカウントが既に保護されているか、別のコンテナーに登録されている可能性もあります。 コンテナーから[登録を解除](troubleshoot-azure-files.md#configuring-backup)して、保護のために他のコンテナー内のストレージ アカウントを検出します。

### <a name="why-cant-i-see-some-of-my-azure-file-shares-in-the-storage-account-when-im-trying-to-configure-backup-br"></a>バックアップを構成しようとしているときに、ストレージ アカウントに Azure ファイル共有の一部が表示されないのはなぜですか。 <br/>
その Azure ファイル共有が既に同じ Recovery Services コンテナー内で保護されているかどうかと、その Azure ファイル共有を最近削除したかどうかを確認してください。

### <a name="can-i-protect-file-shares-connected-to-a-sync-group-in-azure-files-sync-br"></a>Azure Files Sync で同期グループに接続されているファイル共有を保護することはできますか? <br/>
はい。 同期グループに接続されている Azure ファイル共有の保護は有効になっており、パブリック プレビューに含まれています。

### <a name="when-trying-to-back-up-file-shares-i-clicked-on-a-storage-account-for-discovering-the-file-shares-in-it-however-i-did-not-protect-them-how-do-i-protect-these-file-shares-with-any-other-vault"></a>ファイル共有をバックアップしようと、ストレージ アカウント内のファイル共有を検出するために、そのストレージ アカウントをクリックしました。 しかし、それらを保護することはしませんでした。 これらのファイル共有を他のコンテナーで保護するにはどうすればよいのですか?
バックアップを試みる際、いずれかのストレージ アカウントを選択して、その中からファイル共有を検出すると、その操作を行ったストレージ アカウントがコンテナーに登録されます。 別のコンテナーでファイル共有を保護する場合は、このコンテナーから選択したストレージ アカウントを[登録解除](troubleshoot-azure-files.md#configuring-backup)してください。

### <a name="can-i-change-the-vault-to-which-i-backup-my-file-shares"></a>ファイル共有のバックアップ先コンテナーを変更することはできますか?
はい。 ただし、接続されているコンテナーからの[保護を停止](backup-azure-files.md#stop-protecting-an-azure-file-share)し、そのストレージ アカウントを[登録解除](troubleshoot-azure-files.md#configuring-backup)したうえで、別のコンテナーから保護する必要があります。

### <a name="in-which-geos-can-i-back-up-azure-file-shares-br"></a>どの geo に Azure ファイル共有をバックアップできますか <br/>
Azure ファイル共有のバックアップは現在プレビュー段階であり、次の geo でのみ利用できます。
- オーストラリア東部 (AE)
- オーストラリア南東部 (ASE)
- ブラジル南部 (BRS)
- カナダ中部 (CNC)
- カナダ東部 (CE)
- 米国中部 (CUS)
- 東アジア (EA)
- 米国東部 (EUS)
- 米国東部 2 (EUS2)
- 東日本 (JPE)
- 西日本 (JPW)
- インド中部 (INC)
- インド南部 (INS)
- 韓国中部 (KRC)
- 韓国南部 (KRS)
- 米国中北部 (NCUS)
- 北ヨーロッパ (NE)
- 米国中南部 (SCUS)
- 東南アジア (SEA)
- 英国南部 (UKS)
- 英国西部 (UKW)
- 西ヨーロッパ (WE)
- 米国西部 (WUS)
- 米国中西部 (WCUS)
- 米国西部 2 (WUS 2)
- US Gov アリゾナ (UGA)
- US Gov テキサス (UGT)
- US Gov バージニア (UGV)

上記以外の特定の geo で使用する必要がある場合は、[AskAzureBackupTeam@microsoft.com](email:askazurebackupteam@microsoft.com) 宛てにメールをお送りください。

### <a name="how-many-azure-file-shares-can-i-protect-in-a-vaultbr"></a>コンテナーで保護できる Azure ファイル共有はいくつですか。<br/>
プレビュー期間中は、コンテナーにつき最大 50 個のストレージ アカウントから Azure ファイル共有を保護できます。 また、1 つのコンテナーで保護できる Azure ファイル共有は最大 200 個です。

### <a name="can-i-protect-two-different-file-shares-from-the-same-storage-account-to-different-vaults"></a>同じストレージ アカウントにある 2 つの異なるファイル共有を別々のコンテナーで保護することはできますか?
いいえ。 1 つのストレージ アカウントに存在するすべてのファイル共有は、必ず同じコンテナーで保護する必要があります。

## <a name="backup"></a>バックアップ

### <a name="how-many-on-demand-backups-can-i-take-per-file-share-br"></a>ファイル共有につき、いくつのオンデマンド バックアップを作成できますか? <br/>
どの時点でも、ファイル共有のスナップショットを 200 個まで作成することができます。 この制限には、ポリシーの定義に従って Azure Backup により作成されたスナップショットの数も含まれます。 この制限に達した後でバックアップが失敗するようになったら、将来のバックアップを正常に実行できるよう、オンデマンドの復元ポイントを削除してください。

### <a name="after-enabling-virtual-networks-on-my-storage-account-the-backup-of-file-shares-in-the-account-started-failing-why"></a>ストレージ アカウントで仮想ネットワークを有効にすると、そのアカウントでのファイル共有のバックアップが失敗し始めました。 なぜですか?
Azure ファイル共有のバックアップでは、仮想ネットワークが有効になっているストレージ アカウントがサポートされていません。 バックアップを正常に実行できるよう、ストレージ アカウントでは仮想ネットワークを無効にしてください。

## <a name="restore"></a>復元

### <a name="can-i-recover-from-a-deleted-azure-file-share-br"></a>削除した Azure ファイル共有から復旧できますか。 <br/>
Azure ファイル共有を削除するときは、削除されるバックアップの一覧が表示され、確認が求められます。 削除した Azure ファイル共有を復元することはできません。

### <a name="can-i-restore-from-backups-if-i-stopped-protection-on-an-azure-file-share-br"></a>Azure ファイル共有の保護を停止した場合、バックアップから復元することはできますか。 <br/>
はい。 保護を停止するときに **[バックアップ データの保持]** を選択した場合は、既存のすべての復元ポイントから復元できます。

### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>進行中の復元ジョブをキャンセルした場合、どうなりますか。
進行中の復元ジョブがキャンセルされると、復元プロセスが停止され、すべてのファイルがキャンセル前に復元されます。ロールバックすることなく、構成された変換先 (元の場所または別の場所) は保持されます。 


## <a name="manage-backup"></a>バックアップの管理

### <a name="can-i-use-powershell-to-configuremanagerestore-backups-of-azure-file-shares-br"></a>PowerShell を使用して、Azure ファイル共有のバックアップを構成/管理/復元できますか。 <br/>
はい。 詳細なドキュメントを[こちら](backup-azure-afs-automation.md)で参照してください

### <a name="can-i-access-the-snapshots-taken-by-azure-backups-and-mount-it-br"></a>Azure Backup によって作成されたスナップショットにアクセスしてマウントできますか? <br/>
Azure Backup によって作成されたスナップショットはすべて、ポータル、PowerShell、または CLI でスナップショットを表示することでアクセスできます。 Azure Files 共有スナップショットについて詳しくは、[Azure Files の共有スナップショット (プレビュー) の概要](../storage/files/storage-snapshots-files.md)に関するページを参照してください。

### <a name="what-is-the-maximum-retention-i-can-configure-for-backups-br"></a>バックアップについて構成できる保持期間の上限はどの位ですか? <br/>
Azure ファイル共有のバックアップでは、最大 180 日のリテンション期間でポリシーを構成できます。 ただし、[PowerShell の "オンデマンド バックアップ" オプション](backup-azure-afs-automation.md#trigger-an-on-demand-backup)を使用して、復旧ポイントを 10 年間でも保持することができます。

### <a name="what-happens-when-i-change-the-backup-policy-for-an-azure-file-share-br"></a>Azure ファイル共有のバックアップ ポリシーを変更した場合はどうなりますか。 <br/>
新しいポリシーをファイル共有に適用すると、新しいポリシーのスケジュールと保持期間が適用されます。 リテンション期間が延長された場合、既にある復旧ポイントは、新しいポリシーに従って保存するようにマーキングされます。 リテンション期間が短縮された場合、次回のクリーンアップ ジョブで排除対象としてマーキングされて、その後削除されます。

## <a name="see-also"></a>関連項目
この情報は、Azure Files のバックアップだけを対象としています。Azure Backup のその他の領域の詳細については、以下の Backup に関する他の FAQ のいくつかを参照してください。
-  [Recovery Services コンテナーの FAQ](backup-azure-backup-faq.md)
-  [Azure VM バックアップの FAQ](backup-azure-vm-backup-faq.md)
-  [Azure Backup エージェントの FAQ](backup-azure-file-folder-backup-faq.md)