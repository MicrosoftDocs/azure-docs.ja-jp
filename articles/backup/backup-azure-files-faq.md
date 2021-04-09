---
title: Azure Files のバックアップに関する FAQ
description: この記事では、Azure Backup サービスを使用して Azure ファイル共有を保護する方法に関してよく寄せられる質問への回答を示します。
ms.date: 04/22/2020
ms.topic: conceptual
ms.openlocfilehash: e2b6afb25e189ee2848f25c0ba59d843baf37090
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91940837"
---
# <a name="questions-about-backing-up-azure-files"></a>Azure Files のバックアップに関する質問

この記事では、Azure Files のバックアップについてよくある質問への回答を示します。 一部の回答は、より詳しい情報を扱った記事にリンクされています。 また、[Microsoft Q&A の質問ページ](/answers/topics/azure-backup.html)に Azure Backup サービスに関する質問を投稿してディスカッションすることもできます。

この記事の各セクションの内容をひととおり確認するには、右側の「**この記事の内容**」にあるリンクをご利用ください。

## <a name="configuring-the-backup-job-for-azure-files"></a>Azure Files のバックアップ ジョブの構成

### <a name="why-cant-i-see-some-of-my-storage-accounts-that-i-want-to-protect-which-contain-valid-azure-file-shares"></a>有効な Azure ファイル共有を含む、保護したいストレージ アカウントの一部が表示されないのはなぜですか?

ストレージ アカウントがサポートされているストレージ アカウントの種類のいずれかに属していることを確実にするために、「[Azure ファイル共有のバックアップのサポート マトリックス](azure-file-share-support-matrix.md)」を参照してください。 探しているストレージ アカウントが既に保護されている、または別のコンテナーに登録されている可能性もあります。 コンテナーから[ストレージ アカウントの登録を解除](manage-afs-backup.md#unregister-a-storage-account)して、保護のために他のコンテナー内のストレージ アカウントを検出します。

### <a name="why-cant-i-see-some-of-my-azure-file-shares-in-the-storage-account-when-im-trying-to-configure-backup"></a>バックアップを構成しようとしているときに、ストレージ アカウントに Azure ファイル共有の一部が表示されないのはなぜですか。

その Azure ファイル共有が既に同じ Recovery Services コンテナー内で保護されているかどうかと、その Azure ファイル共有を最近削除したかどうかを確認してください。

### <a name="can-i-protect-file-shares-connected-to-a-sync-group-in-azure-files-sync"></a>Azure Files Sync で同期グループに接続されているファイル共有を保護することはできますか?

はい。 同期グループに接続されている Azure ファイル共有の保護は有効になっています。

### <a name="when-trying-to-back-up-file-shares-i-selected-a-storage-account-to-discover-the-file-shares-in-it-however-i-didnt-protect-them-how-do-i-protect-these-file-shares-with-any-other-vault"></a>ファイル共有をバックアップしようとして、ストレージ アカウント内のファイル共有を検出するために、そのストレージ アカウントを選択しました。 しかし、それらを保護することはしませんでした。 これらのファイル共有を他のコンテナーで保護するにはどうすればよいのですか?

バックアップを試みる際、ストレージ アカウントを選択して、その中からファイル共有を検出すると、これを行ったストレージ アカウントがコンテナーに登録されます。 別のコンテナーでファイル共有を保護する場合は、このコンテナーから選択したストレージ アカウントを[登録解除](manage-afs-backup.md#unregister-a-storage-account)してください。

### <a name="why-cant-i-change-the-vault-to-configure-backup-for-the-file-share"></a>ファイル共有のバックアップを構成するためにコンテナーを変更できないのはなぜですか?

ストレージ アカウントが既にコンテナーに登録されているか、またはストレージ アカウント内の他のファイル共有がコンテナーを使用して保護されている場合、それを変更するオプションが与えられません。 1 つのストレージ アカウントに存在するすべてのファイル共有は、必ず同じコンテナーで保護する必要があります。 コンテナーを変更する必要がある場合は、接続されているコンテナーから[ストレージ アカウント内のすべてのファイル共有の保護を停止](manage-afs-backup.md#stop-protection-on-a-file-share)し、ストレージ アカウントの[登録を解除](manage-afs-backup.md#unregister-a-storage-account)してから、保護する別のコンテナーを選択する必要があります。

### <a name="can-i-change-the-vault-to-which-i-back-up-my-file-shares"></a>自分のファイル共有のバックアップ先コンテナーを変更することはできますか?

はい。 しかし、接続されているコンテナーからの[ファイル共有の保護を停止](manage-afs-backup.md#stop-protection-on-a-file-share)し、このストレージ アカウントを[登録解除](manage-afs-backup.md#unregister-a-storage-account)したうえで、別のコンテナーから保護する必要があります。

### <a name="can-i-protect-two-different-file-shares-from-the-same-storage-account-to-different-vaults"></a>同じストレージ アカウントにある 2 つの異なるファイル共有を別々のコンテナーで保護することはできますか?

いいえ。 1 つのストレージ アカウントに存在するすべてのファイル共有は、必ず同じコンテナーで保護する必要があります。

## <a name="backup"></a>バックアップ

### <a name="what-should-i-do-if-my-backups-start-failing-due-to-the-maximum-limit-reached-error"></a>上限に達したエラーのためにバックアップが失敗し始めた場合はどうすればよいですか?

どの時点でも、ファイル共有のスナップショットを 200 個まで作成することができます。 この制限には、ポリシーの定義に従って Azure Backup により作成されたスナップショットの数も含まれます。 この制限に達した後でバックアップが失敗するようになったら、将来のバックアップを正常に実行できるよう、オンデマンド スナップショットを削除してください。

## <a name="restore"></a>復元

### <a name="can-i-recover-from-a-deleted-azure-file-share"></a>削除した Azure ファイル共有から復旧できますか。

ファイル共有が論理的に削除された状態にある場合、復元操作を実行するには、ファイル共有の削除を先に取り消しておく必要があります。 削除の取り消し操作によって、ファイル共有がアクティブ状態になり、任意の時点への復元を実行できるようになります。 ファイル共有の削除を取り消す方法については、[こちらのリンク](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal#restore-soft-deleted-file-share)をクリックするか、または[ファイル共有の削除を取り消すためのスクリプト](./scripts/backup-powershell-script-undelete-file-share.md)を参照してください。 ファイル共有が完全に削除されると、コンテンツとスナップショットを復元することはできなくなります。

### <a name="can-i-restore-from-backups-if-i-stopped-protection-on-an-azure-file-share"></a>Azure ファイル共有の保護を停止した場合、バックアップから復元することはできますか。

はい。 保護を停止するときに **[バックアップ データの保持]** を選択した場合は、既存のすべての復元ポイントから復元できます。

### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>進行中の復元ジョブをキャンセルした場合、どうなりますか。

進行中の復元ジョブがキャンセルされると、復元プロセスが停止され、すべてのファイルがキャンセル前に復元されます。ロールバックされずに、構成された変換先 (元の場所または別の場所) は保持されます。

## <a name="manage-backup"></a>バックアップの管理

### <a name="can-i-use-powershell-to-configuremanagerestore-backups-of-azure-file-shares"></a>PowerShell を使用して、Azure ファイル共有のバックアップを構成/管理/復元できますか。

はい。 詳細なドキュメントを[こちら](backup-azure-afs-automation.md)で参照してください

### <a name="can-i-access-the-snapshots-taken-by-azure-backups-and-mount-them"></a>Azure Backup によって作成されたスナップショットにアクセスしてマウントできますか?

Azure Backup によって作成されたスナップショットはすべて、ポータル、PowerShell、または CLI でスナップショットを表示することでアクセスできます。 Azure Files 共有スナップショットの詳細については、「[Azure Files の共有スナップショットの概要](../storage/files/storage-snapshots-files.md)」を参照してください。

### <a name="what-happens-after-i-move-a-backed-up-file-share-to-a-different-subscription"></a>バックアップされたファイル共有を別のサブスクリプションに移動した後はどうなりますか?

ファイル共有を別のサブスクリプションに移動すると、Azure Backup によって新しいファイル共有と見なされます。 推奨される手順を以下に示します。
 
シナリオ:ファイル共有 *FS1* がサブスクリプション *S1* にあり、*V1* コンテナーを使用して保護されているとします。 現在、そのファイル共有をサブスクリプション *S2* に移動したいと考えています。
 
1.  目的のストレージ アカウントとファイル共有 (FS1) を別のサブスクリプション (S2) に移動します。
2.  V1 コンテナーで、FS1 に対して "データを削除して保護を停止" 操作をトリガーします。
3.  FS1 をホストしているストレージ アカウントを V1 コンテナーから登録解除します。
4.  S2 サブスクリプションでコンテナー (V2) を使用して、先ほど S2 に移動した FS1 のバックアップを再構成します。 
 
V2 を使用してバックアップを再構成した後、V1 で作成されたスナップショットは Azure Backup によって管理されなくなることに注意してください。 そのため、必要に応じて、これらのスナップショットを手動で削除する必要があります。

### <a name="can-i-move-my-backed-up-file-share-to-a-different-resource-group"></a>バックアップしたファイル共有を別のリソース グループに移動できますか?
 
はい、バックアップしたファイル共有を別のリソース グループに移動できます。 ただし、Azure Backup によって新しいリソースと見なされるため、そのファイル共有のバックアップを再構成する必要があります。 また、リソース グループの移動前に作成されたスナップショットは、Azure backup によって管理されなくなります。 そのため、必要に応じて、これらのスナップショットを手動で削除する必要があります。

### <a name="what-is-the-maximum-retention-i-can-configure-for-backups"></a>バックアップについて構成できる保持期間の上限はどの位ですか?

最大保有期間の詳細については、[サポート マトリックス](azure-file-share-support-matrix.md)を参照してください。 バックアップ ポリシーの構成中に保有期間の値を入力すると、Azure Backup によってスナップショットの数がリアルタイムで計算されます。 定義された保有期間の値に対応するスナップショットの数が 200 を超えるとすぐに、ポータルには保有期間の値を調整するよう要求する警告が表示されます。 これは、任意の時点で任意のファイル共有に対して Azure Files でサポートされるスナップショットの最大数の上限を超えないようにするためです。

### <a name="what-is-the-impact-on-existing-recovery-points-and-snapshots-when-i-modify-the-backup-policy-for-an-azure-file-share-to-switch-from-daily-policy-to-gfs-policy"></a>Azure ファイル共有のバックアップ ポリシーを "日単位のポリシー" から "GFS ポリシー" に変更すると、既存の復旧ポイントとスナップショットにどのような影響がありますか?

日単位のバックアップ ポリシーを GFS ポリシーに変更すると (週/月/年単位の保持期間を追加)、動作は次のようになります。

- **保持期間**:ポリシーを変更する際に週/月/年単位の保持期間を追加すると、スケジュールされたバックアップの一部として作成される将来のすべての復旧ポイントは、新しいポリシーに基づいてタグ付けされます。 既存のすべての復旧ポイントは、日単位の復旧ポイントと見なされるので、週/月/年単位としてタグ付けされません。

- **スナップショットと復旧ポイントのクリーンアップ**:

  - 日単位の保持期間が延長される場合、既存の復旧ポイントの有効期限が、新しいポリシーに構成された日単位の保持期間の値に応じて更新されます。
  - 日単位の保持期間が短縮される場合、既存の復旧ポイントとスナップショットは、新しいポリシーに構成された日単位の保持期間の値に応じて次のクリーンアップ実行ジョブの削除対象としてマークされ、削除されます。

この動作の例を次に示します。

#### <a name="existing-policy-p1"></a>既存のポリシー [P1]

|リテンション期間の種類 |スケジュール |保持  |
|---------|---------|---------|
|毎日    |    毎日午後 8 時    |  100 日間       |

#### <a name="new-policy-modified-p1"></a>新しいポリシー [変更された P1]

| リテンション期間の種類 | スケジュール                       | 保持 |
| -------------- | ------------------------------ | --------- |
| 毎日          | 毎日午後 9 時              | 50 日間   |
| 週単位         | 日曜日午後 9 時              | 3 週間   |
| 月単位        | 最終月曜日午後 9 時         | 1 か月   |
| 年単位         | 1 月第 3 日曜日午後 9 時 | 4 年間   |

#### <a name="impact"></a>影響

1. 既存の復旧ポイントの有効期限は、新しいポリシーの日単位の保持期間の値 (50 日) に従って調整されます。 したがって、50 日よりも古い復旧ポイントは削除対象としてマークされます。

2. 既存の復旧ポイントには、新しいポリシーに基づいた週/月/年単位のタグ付けはされません。

3. 今後のすべてのバックアップは、新しいスケジュール (午後 9 時) に従ってトリガーされます。

4. 今後のすべての復旧ポイントの有効期限は、新しいポリシーに合わせて調整されます。

>[!NOTE]
>ポリシーの変更は、スケジュールされたバックアップ ジョブ実行の一部として作成された復旧ポイントにのみ影響します。 オンデマンド バックアップの場合、保持期間はバックアップ作成時に指定する **[Retain Till]\(保持期限\)** 値によって決まります。

### <a name="what-is-the-impact-on-existing-recovery-points-when-i-modify-an-existing-gfs-policy"></a>既存の GFS ポリシーを変更すると、既存の復旧ポイントにどのような影響がありますか?

ファイル共有に新しいポリシーが適用されると、今後スケジュールされているバックアップはすべて、変更されたポリシーで構成されているスケジュールに従って実行されます。  既存のすべての復旧ポイントの保持期間は、構成された新しい保持値に従って調整されます。 つまり、保持期間が延長された場合、既存の復旧ポイントは、新しいポリシーに従って保持されるようにマーキングされます。 保持期間が短縮された場合、それらは次回のクリーンアップ ジョブで排除対象としてマーキングされて、その後削除されます。

この動作の例を次に示します。

#### <a name="existing-policy-p2"></a>既存のポリシー [P2]

| リテンション期間の種類 | スケジュール           | 保持 |
| -------------- | ------------------ | --------- |
| 毎日          | 毎日午後 8 時 | 50 日間   |
| 週単位         | 月曜日午後 8 時  | 3 週間   |

#### <a name="new-policy-modified-p2"></a>新しいポリシー [変更された P2]

| リテンション期間の種類 | スケジュール               | 保持 |
| -------------- | ---------------------- | --------- |
| 毎日          | 毎日午後 9 時     | 10 日   |
| 週単位         | 月曜日午後 9 時      | 2 週間   |
| 月単位        | 最終月曜日午後 9 時 | 2 か月間  |

#### <a name="impact-of-change"></a>変更の影響

1. 既存の日単位の復旧ポイントの有効期限は、新しい日単位の保持期間の値 (10 日間) に従って調整されます。 そのため、10 日よりも古い日単位の復旧ポイントは削除されます。

2. 既存の週単位の復旧ポイントの有効期限は、新しい週単位の保持期間の値 (2 週間) に従って調整されます。 そのため、2 週間よりも古い週単位の復旧ポイントは削除されます。

3. 月単位の復旧ポイントは、新しいポリシー構成に基づいた今後のバックアップの一部としてのみ作成されます。

4. 今後のすべての復旧ポイントの有効期限は、新しいポリシーに合わせて調整されます。

>[!NOTE]
>ポリシーの変更は、スケジュールされたバックアップの一部として作成された復旧ポイントにのみ影響します。 オンデマンド バックアップの場合、保持期間はバックアップ作成時に指定する **[Retain Till]\(保持期限\)** 値によって決まります。

## <a name="next-steps"></a>次のステップ

- [Azure ファイル共有のバックアップ中の問題のトラブルシューティング](troubleshoot-azure-files.md)
