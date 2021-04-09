---
title: Azure ファイル共有のバックアップのトラブルシューティング
description: この記事は、Azure ファイル共有を保護する際に発生する問題に関するトラブルシューティング情報です。
ms.date: 02/10/2020
ms.topic: troubleshooting
ms.openlocfilehash: 4908b8ed97bad43d9d24427660a8691ee43d7eaf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89376980"
---
# <a name="troubleshoot-problems-while-backing-up-azure-file-shares"></a>Azure ファイル共有のバックアップ中の問題のトラブルシューティング

この記事では、Azure Backup サービスを使用して、Azure ファイル共有のバックアップを構成したり復元したりする際に発生する問題に対処するためのトラブルシューティング情報を提供します。

## <a name="common-configuration-issues"></a>一般的な構成の問題

### <a name="could-not-find-my-storage-account-to-configure-backup-for-the-azure-file-share"></a>Azure ファイル共有のバックアップを構成するストレージ アカウントが見つかりませんでした

- 検出が完了するまで待ってください。
- そのストレージ アカウントのファイル共有が既に別の Recovery Services コンテナーによって保護されているかどうかを確認してください。

  >[!NOTE]
  >ストレージ アカウントのすべてのファイル共有は、1 つの Recovery Services コンテナーのみで保護できます。 [このスクリプト](scripts/backup-powershell-script-find-recovery-services-vault.md)を使用すると、ご自分のストレージ アカウントが登録されている Recovery Services コンテナーを見つけるのに役立ちます。

- サポートされていないストレージ アカウントのいずれにもファイル共有が存在しないようにしてください。 サポートされているストレージ アカウントを見つけるには、「[Azure ファイル共有のバックアップのサポート マトリックス](azure-file-share-support-matrix.md)」を参照してください。
- 新しいストレージ アカウントの場合、ストレージ アカウント名とリソース グループ名を組み合わせた長さが確実に 84 文字を超えないようにしてください。従来のストレージ アカウントの場合は 77 文字です。
- ストレージ アカウントのファイアウォール設定を確認して、信頼された Microsoft サービスからストレージ アカウントへのアクセスを許可するオプションが有効になっていることを確認します。

### <a name="error-in-portal-states-discovery-of-storage-accounts-failed"></a>ポータルのエラーが、ストレージ アカウントの検出に失敗したことを示しています

パートナー サブスクリプション (CSP 対応) をお持ちの場合は、このエラーを無視してください。 サブスクリプションが CSP 対応でなく、ストレージ アカウントを検出できない場合は、サポートにお問い合わせください。

### <a name="selected-storage-account-validation-or-registration-failed"></a>選択したストレージ アカウントの検証または登録に失敗しました

もう一度登録してみてください。 問題が解決しない場合は、サポートにお問い合わせください。

### <a name="could-not-list-or-find-file-shares-in-the-selected-storage-account"></a>選択したストレージ アカウントでファイル共有を一覧表示または検索できませんでした

- ストレージ アカウントがリソース グループに存在していて、コンテナーで最後に検証または登録した後に削除または移動されていないことを確認してください。
- 保護しようとしているファイル共有が削除されていないことを確認してください。
- ストレージ アカウントが、ファイル共有のバックアップ用にサポートされているストレージ アカウントであることを確認してください。 サポートされているストレージ アカウントを見つけるには、「[Azure ファイル共有のバックアップのサポート マトリックス](azure-file-share-support-matrix.md)」を参照してください。
- ファイル共有が、同じ Recovery Services コンテナー内で既に保護されているかどうかを確認してください。

### <a name="backup-file-share-configuration-or-the-protection-policy-configuration-is-failing"></a>ファイル共有のバックアップの構成 (または保護ポリシーの構成) が失敗しています

- 再度構成して、問題が解決していないかどうかを確認してください。
- 保護するファイル共有が削除されていないことを確認してください。
- 複数のファイル共有を一度に保護しようとして、一部のファイル共有が失敗する場合は、失敗したファイル共有のバックアップを再度構成してみてください。

### <a name="unable-to-delete-the-recovery-services-vault-after-unprotecting-a-file-share"></a>ファイル共有の保護を解除した後に Recovery Services コンテナーを削除できません

Azure portal で、お使いの **コンテナー** >  **[バックアップ インフラストラクチャ]**  >  **[ストレージ アカウント]** の順に開きます。 **[登録解除]** を選択して、Recovery Services コンテナーからストレージ アカウントを削除してください。

>[!NOTE]
>Recovery Services コンテナーは、コンテナーに登録されているすべてのストレージ アカウントの登録を解除した後にのみ削除できます。

## <a name="common-backup-or-restore-errors"></a>バックアップまたは復元に関する一般的なエラー

>[!NOTE]
>バックアップ操作または復元操作を実行するための十分なアクセス許可があることを確認するには、[このドキュメント](./backup-rbac-rs-vault.md#minimum-role-requirements-for-the-azure-file-share-backup)を参照してください。

### <a name="filesharenotfound--operation-failed-as-the-file-share-is-not-found"></a>FileShareNotFound- ファイル共有が見つからないため、操作が失敗しました

Error Code:FileShareNotFound

エラー メッセージ:ファイル共有が見つからないため、操作が失敗しました

保護しようとしているファイル共有が削除されていないことを確認してください。

### <a name="usererrorfileshareendpointunreachable--storage-account-not-found-or-not-supported"></a>UserErrorFileShareEndpointUnreachable- ストレージ アカウントが見つからないか、サポートされていません

Error Code:UserErrorFileShareEndpointUnreachable

エラー メッセージ:ストレージ アカウントが見つからないか、サポートされていません

- ストレージ アカウントがリソース グループに存在し、最後の検証後にリソース グループから削除または除外されていないことを確認してください。

- ストレージ アカウントが、ファイル共有のバックアップ用にサポートされているストレージ アカウントであることを確認してください。

### <a name="afsmaxsnapshotreached--you-have-reached-the-max-limit-of-snapshots-for-this-file-share-you-will-be-able-to-take-more-once-the-older-ones-expire"></a>AFSMaxSnapshotReached- このファイル共有のスナップショットの上限に達しました。以前のものの有効期限が切れると、スナップショットを実行できるようになります

Error Code:AFSMaxSnapshotReached

エラー メッセージ:このファイル共有のスナップショットの上限に達しました。以前のものの有効期限が切れると、スナップショットを実行できるようになります。

- このエラーは、1 つのファイル共有に対してオンデマンド バックアップを複数作成したときに発生する場合があります。
- スナップショットには、ファイル共有につき 200 個という上限があります (これには、Azure Backup によって作成されたものも含まれます)。 スケジュールされた古いバックアップ (またはスナップショット) は自動的にクリーンアップされます。 上限に達した場合は、オンデマンド バックアップ (またはスナップショット) を削除する必要があります。

Azure Files ポータルからオンデマンド バックアップ (Azure ファイル共有スナップショット) を削除してください。

>[!NOTE]
> Azure Backup によって作成されたスナップショットを削除すると、復旧ポイントが失われます。

### <a name="usererrorstorageaccountnotfound--operation-failed-as-the-specified-storage-account-does-not-exist-anymore"></a>UserErrorStorageAccountNotFound- 指定されたストレージ アカウントは存在しなくなったため、操作は失敗しました

Error Code:UserErrorStorageAccountNotFound

エラー メッセージ:指定されたストレージ アカウントは存在しなくなったため、操作は失敗しました。

ストレージ アカウントがまだ存在していて、削除されていないことを確認してください。

### <a name="usererrordtsstorageaccountnotfound--the-storage-account-details-provided-are-incorrect"></a>UserErrorDTSStorageAccountNotFound- 指定されたストレージ アカウントの詳細が正しくありません

Error Code:UserErrorDTSStorageAccountNotFound

エラー メッセージ:指定されたストレージ アカウントの詳細が正しくありません。

ストレージ アカウントがまだ存在していて、削除されていないことを確認してください。

### <a name="usererrorresourcegroupnotfound--resource-group-doesnt-exist"></a>UserErrorResourceGroupNotFound- リソース グループが存在しません

Error Code:UserErrorResourceGroupNotFound

エラー メッセージ:リソース グループが存在しません

既存のリソース グループを選択するか、新しいリソース グループを作成します。

### <a name="parallelsnapshotrequest--a-backup-job-is-already-in-progress-for-this-file-share"></a>ParallelSnapshotRequest- このファイル共有では、バックアップ ジョブが既に進行中です

Error Code:ParallelSnapshotRequest

エラー メッセージ:このファイル共有では、バックアップ ジョブが既に進行中です。

- ファイル共有のバックアップでは、同じファイル共有に対する並列スナップショット要求はサポートされていません。

- 既存のバックアップ ジョブが完了するのを待ってから、もう一度やり直してください。 Recovery Services コンテナーにバックアップ ジョブが見つからない場合は、同じサブスクリプション内の他の Recovery Services コンテナーを確認してください。

### <a name="filesharebackupfailedwithazurerprequestthrottling-filesharerestorefailedwithazurerprequestthrottling--file-share-backup-or-restore-failed-due-to-storage-service-throttling-this-may-be-because-the-storage-service-is-busy-processing-other-requests-for-the-given-storage-account"></a>FileshareBackupFailedWithAzureRpRequestThrottling/ FileshareRestoreFailedWithAzureRpRequestThrottling- ストレージ サービスの調整のため、ファイル共有のバックアップまたは復元に失敗しました。 ストレージ サービスが特定のストレージ アカウントの他の要求を処理中であることが原因である可能性があります

Error Code:FileshareBackupFailedWithAzureRpRequestThrottling/ FileshareRestoreFailedWithAzureRpRequestThrottling

エラー メッセージ:File share backup or restore failed due to storage service throttling. This may be because the storage service is busy processing other requests for the given storage account. (ストレージ サービスの調整のため、ファイル共有のバックアップまたは復元に失敗しました。その理由として考えられるのは、ストレージ サービスが特定のストレージ アカウントの他の要求が処理中であることです。)

後でバックアップまたは復元操作を試してください。

### <a name="targetfilesharenotfound--target-file-share-not-found"></a>TargetFileShareNotFound- ターゲット ファイル共有が見つかりません

Error Code:TargetFileShareNotFound

エラー メッセージ:ターゲット ファイル共有が見つかりません。

- 選択したストレージ アカウントが存在していて、ターゲット ファイル共有が削除されていないことを確認してください。

- ストレージ アカウントが、ファイル共有のバックアップ用にサポートされているストレージ アカウントであることを確認してください。

### <a name="usererrorstorageaccountislocked--backup-or-restore-jobs-failed-due-to-storage-account-being-in-locked-state"></a>UserErrorStorageAccountIsLocked- ストレージ アカウントがロック状態のため、バックアップ ジョブまたは復元ジョブに失敗しました

Error Code:UserErrorStorageAccountIsLocked

エラー メッセージ:ストレージ アカウントがロック状態のため、バックアップ ジョブまたは復元ジョブに失敗しました。

ストレージ アカウントのロックを解除するか、**読み取りロック** ではなく **削除ロック** を使用して、バックアップまたは復元操作を再試行してください。

### <a name="datatransferservicecoflimitreached--recovery-failed-because-number-of-failed-files-are-more-than-the-threshold"></a>DataTransferServiceCoFLimitReached- 失敗したファイルの数がしきい値を超えているため、回復が失敗しました

Error Code:DataTransferServiceCoFLimitReached

エラー メッセージ:失敗したファイルの数がしきい値を超えているため、回復が失敗しました。

- 回復が失敗した理由はファイル (ジョブの詳細で指定されたパス) に示されています。 エラーを解決し、失敗したファイルに対してのみ復元操作を再試行してください。

- ファイル復元が失敗した一般的な理由:

  - 失敗したファイルが現在使用中
  - 失敗したファイルと同じ名前のディレクトリが親ディレクトリに存在する

### <a name="datatransferserviceallfilesfailedtorecover--recovery-failed-as-no-file-could-be-recovered"></a>DataTransferServiceAllFilesFailedToRecover- 回復は失敗し、ファイルを回復できませんでした

Error Code:DataTransferServiceAllFilesFailedToRecover

エラー メッセージ:回復は失敗し、ファイルを回復できませんでした。

- 回復が失敗した理由はファイル (ジョブの詳細で指定されたパス) に示されています。 エラーを解決し、失敗したファイルに対してのみ復元操作を再試行してください。

- ファイル復元が失敗した一般的な理由:

  - 失敗したファイルが現在使用中
  - 失敗したファイルと同じ名前のディレクトリが親ディレクトリに存在する

### <a name="usererrordtssourceurinotvalid---restore-fails-because-one-of-the-files-in-the-source-does-not-exist"></a>UserErrorDTSSourceUriNotValid - ソース内のファイルの 1 つが存在しないため、復元に失敗します

Error Code:DataTransferServiceSourceUriNotValid

エラー メッセージ:Restore fails because one of the files in the source does not exist. (ソース内のファイルの 1 つが存在しないため、復元に失敗します。)

- 選択した項目は復旧ポイントのデータに存在しません。 ファイルを復旧するには、正しいファイル リストを指定してください。
- 復旧ポイントに対応するファイル共有スナップショットは手動で削除されます。 別の復旧ポイントを選択し、復元操作を再試行してください。

### <a name="usererrordtsdestlocked--a-recovery-job-is-in-process-to-the-same-destination"></a>UserErrorDTSDestLocked- 同じ宛先への回復ジョブが処理中です

Error Code:UserErrorDTSDestLocked

エラー メッセージ:同じ宛先への回復ジョブが処理中です。

- ファイル共有のバックアップでは、同じターゲット ファイル共有への並列復旧がサポートされていません。

- 既存の回復が完了するのを待ってから、もう一度試してください。 Recovery Services コンテナーに回復ジョブが見つからない場合は、同じサブスクリプション内の他の Recovery Services コンテナーを確認してください。

### <a name="usererrortargetfilesharefull--restore-operation-failed-as-target-file-share-is-full"></a>UserErrorTargetFileShareFull- ターゲット ファイルの共有がいっぱいのため、復元操作に失敗しました

エラー コード:UserErrorTargetFileShareFull

エラー メッセージ:ターゲット ファイルの共有がいっぱいのため、復元操作に失敗しました。

復元データに対応できるようにターゲット ファイル共有のサイズ クォータを引き上げ、復元操作を再試行してください。

### <a name="usererrortargetfilesharequotanotsufficient--target-file-share-does-not-have-sufficient-storage-size-quota-for-restore"></a>UserErrorTargetFileShareQuotaNotSufficient- ターゲット ファイル共有には、復元のための十分なストレージ サイズのクォータがありません

Error Code:UserErrorTargetFileShareQuotaNotSufficient

エラー メッセージ:ターゲット ファイル共有には、復元のための十分なストレージ サイズのクォータがありません

復元データに対応できるようにターゲット ファイル共有のサイズ クォータを引き上げ、操作を再試行してください

### <a name="file-sync-prerestorefailed--restore-operation-failed-as-an-error-occurred-while-performing-pre-restore-operations-on-file-sync-service-resources-associated-with-the-target-file-share"></a>File Sync PreRestoreFailed- ターゲット ファイル共有に関連付けられている File Sync Service リソースで復元前の操作を実行しているときにエラーが発生したため復元操作に失敗しました

Error Code:File Sync PreRestoreFailed

エラー メッセージ:Restore operation failed as an error occurred while performing pre restore operations on File Sync Service resources associated with the target file share. (ターゲット ファイル共有に関連付けられている File Sync Service リソースで復元前の操作を実行しているときにエラーが発生したため復元操作に失敗しました。)

後でデータを復元してみてください。 引き続き問題が発生する場合は、Microsoft サポートにお問い合わせください。

### <a name="azurefilesyncchangedetectioninprogress--azure-file-sync-service-change-detection-is-in-progress-for-the-target-file-share-the-change-detection-was-triggered-by-a-previous-restore-to-the-target-file-share"></a>AzureFileSyncChangeDetectionInProgress- ターゲット ファイル共有に対して Azure File Sync サービスの変更検出が進行中です。 変更検出は、ターゲット ファイル共有に対する以前の復元によってトリガーされました

Error Code:AzureFileSyncChangeDetectionInProgress

エラー メッセージ:ターゲット ファイル共有に対して Azure File Sync サービスの変更検出が進行中です。 変更検出は、ターゲット ファイル共有に対する以前の復元によってトリガーされました。

別のターゲット ファイル共有を使用してください。 または、復元を再試行する前に、ターゲット ファイル共有の Azure File Sync サービスの変更検出が完了するまでお待ちください。

### <a name="usererrorafsrecoverysomefilesnotrestored--one-or-more-files-could-not-be-recovered-successfully-for-more-information-check-the-failed-file-list-in-the-path-given-above"></a>UserErrorAFSRecoverySomeFilesNotRestored- いくつかのファイルを正常に回復できませんでした。 詳しくは、上記のパスにある失敗したファイルの一覧を確認してください

Error Code:UserErrorAFSRecoverySomeFilesNotRestored

エラー メッセージ:いくつかのファイルを正常に回復できませんでした。 詳しくは、上記のパスにある失敗したファイルの一覧を確認してください。

- 回復が失敗した理由はファイル (ジョブの詳細で指定されたパス) に示されています。 理由を解決し、失敗したファイルに対してのみ復元操作を再試行してください。
- ファイル復元が失敗した一般的な理由:

  - 失敗したファイルが現在使用中
  - 失敗したファイルと同じ名前のディレクトリが親ディレクトリに存在する

### <a name="usererrorafssourcesnapshotnotfound--azure-file-share-snapshot-corresponding-to-recovery-point-cannot-be-found"></a>UserErrorAFSSourceSnapshotNotFound- 回復ポイントに対応している Azure ファイル共有スナップショットが見つかりません

Error Code:UserErrorAFSSourceSnapshotNotFound

エラー メッセージ:回復ポイントに対応している Azure ファイル共有スナップショットが見つかりません

- 回復に使用しようとしている回復ポイントに対応するファイル共有スナップショットがまだ存在していることを確認してください。

  >[!NOTE]
  >Azure Backup によって作成されたファイル共有スナップショットを削除すると、対応する回復ポイントが使用できなくなります。 確実に復旧できるように、スナップショットは削除しないことをお勧めします。

- データを回復するには、別の復元ポイントを選択してみてください。

### <a name="usererroranotherrestoreinprogressonsametarget--another-restore-job-is-in-progress-on-the-same-target-file-share"></a>UserErrorAnotherRestoreInProgressOnSameTarget- 別の復元ジョブが同じターゲット ファイル共有で実行中です

Error Code:UserErrorAnotherRestoreInProgressOnSameTarget

エラー メッセージ:別の復元ジョブが同じターゲット ファイル共有で実行中です

別のターゲット ファイル共有を使用してください。 または、キャンセルするか他の復元が完了するのをお待ちください。

## <a name="common-modify-policy-errors"></a>ポリシーの変更に関する一般的なエラー

### <a name="bmsusererrorconflictingprotectionoperation--another-configure-protection-operation-is-in-progress-for-this-item"></a>BMSUserErrorConflictingProtectionOperation- この項目には別の保護構成操作が進行中です

Error Code:BMSUserErrorConflictingProtectionOperation

エラー メッセージ:この項目には別の保護構成操作が進行中です。

先行するポリシー変更操作が完了するのを待って、しばらくしてから再試行してください。

### <a name="bmsusererrorobjectlocked--another-operation-is-in-progress-on-the-selected-item"></a>BMSUserErrorObjectLocked- 選択された項目で別の操作が実行中です

Error Code:BMSUserErrorObjectLocked

エラー メッセージ:選択された項目で別の操作が実行中です。

進行中の他の操作が完了するのを待って、しばらくしてから再試行してください。

## <a name="common-soft-delete-related-errors"></a>論理的な削除に関連する一般的なエラー

### <a name="usererrorrestoreafsinsoftdeletestate--this-restore-point-is-not-available-as-the-snapshot-associated-with-this-point-is-in-a-file-share-that-is-in-soft-deleted-state"></a>UserErrorRestoreAFSInSoftDeleteState - この復元ポイントに関連付けられているスナップショットが論理的に削除された状態のファイル共有に含まれるため、このポイントを使用できません

Error Code:UserErrorRestoreAFSInSoftDeleteState

エラー メッセージ:この復元ポイントに関連付けられているスナップショットが論理的に削除された状態のファイル共有に含まれるため、このポイントを使用できません。

データが論理的な削除状態である場合は復元操作を実行できません。 Files ポータルから、または[削除を取り消すためのスクリプト](scripts/backup-powershell-script-undelete-file-share.md)を使用して、ファイル共有の削除を取り消してから、復元を試行します。

### <a name="usererrorrestoreafsindeletestate--listed-restore-points-are-not-available-as-the-associated-file-share-containing-the-restore-point-snapshots-has-been-deleted-permanently"></a>UserErrorRestoreAFSInDeleteState - 復元ポイントのスナップショットが含まれている関連付けられたファイル共有が完全に削除されているため、一覧の復元ポイントは利用できません

Error Code:UserErrorRestoreAFSInDeleteState

エラー メッセージ:復元ポイントのスナップショットが含まれている関連付けられたファイル共有が完全に削除されているため、一覧の復元ポイントは利用できません。

バックアップされたファイル共有が削除されているかどうかを確認します。 論理的に削除された状態の場合、論理的な削除の保持期間が経過しているために復旧されなかったのか確認します。 どちらの場合も、すべてのスナップショットが完全に失われ、データを復旧することはできません。

>[!NOTE]
> すべての復元ポイントが失われるのを防ぐために、バックアップされたファイル共有を削除しないようにすること、または論理的に削除された状態の場合は、論理的な削除の保持期間が終了する前に削除を取り消すことをお勧めします。

### <a name="usererrorbackupafsinsoftdeletestate---backup-failed-as-the-azure-file-share-is-in-soft-deleted-state"></a>UserErrorBackupAFSInSoftDeleteState - Azure ファイル共有が論理的に削除された状態であるため、バックアップに失敗しました

Error Code:UserErrorBackupAFSInSoftDeleteState

エラー メッセージ:Azure ファイル共有が論理的に削除された状態であるため、バックアップに失敗しました

**Files ポータル** から、または [削除を取り消すためのスクリプト](scripts/backup-powershell-script-undelete-file-share.md)を使用して、ファイル共有の削除を取り消します。その後、バックアップを継続し、データが完全に削除されるのを回避します。

### <a name="usererrorbackupafsindeletestate--backup-failed-as-the-associated-azure-file-share-is-permanently-deleted"></a>UserErrorBackupAFSInDeleteState- 関連付けられている Azure ファイル共有が完全に削除されているため、バックアップに失敗しました

Error Code:UserErrorBackupAFSInDeleteState

エラー メッセージ:関連付けられている Azure ファイル共有が完全に削除されているため、バックアップに失敗しました

バックアップされたファイル共有が完全に削除されているかどうかを確認します。 されている場合は、バックアップ エラーの繰り返しを回避するために、このファイル共有のバックアップを停止してください。 保護を停止する方法については、[Azure ファイル共有の保護の停止](./manage-afs-backup.md#stop-protection-on-a-file-share)に関する記事を参照してください

## <a name="next-steps"></a>次のステップ

Azure ファイル共有のバックアップの詳細については、以下を参照してください。

- [Azure ファイル共有のバックアップ](backup-afs.md)
- [Azure ファイル共有のバックアップに関する FAQ](backup-azure-files-faq.md)
