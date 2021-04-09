---
title: Azure ディスク バックアップでのバックアップ エラーのトラブルシューティング
description: Azure ディスク バックアップでのバックアップ エラーをトラブルシューティングする方法について説明します。
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: 855c6c5b19b10bdb699a25f89ebc29001b7941ac
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98737729"
---
# <a name="troubleshooting-backup-failures-in-azure-disk-backup-in-preview"></a>Azure ディスク バックアップでのバックアップ エラーのトラブルシューティング (プレビュー)

>[!IMPORTANT]
>Azure ディスク バックアップはサービス レベル アグリーメントのないプレビュー段階であるため、運用環境のワークロードに使用することはお勧めできません。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。 利用可能なリージョンについては、[サポート マトリックス](disk-backup-support-matrix.md)に関するページをご覧ください。
>
>プレビューにサインアップするには、[こちらフォームに記入](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR1vE8L51DIpDmziRt_893LVUNFlEWFJBN09PTDhEMjVHS05UWFkxUlUzUS4u)してください。

この記事では、Azure ディスクで発生するバックアップと復元に関する問題のトラブルシューティングについて説明します。 [Azure ディスク バックアップ](disk-backup-overview.md)の利用可能なリージョン、サポートされるシナリオ、制限事項の詳細については、[サポート マトリックス](disk-backup-support-matrix.md)に関するページをご覧ください。

## <a name="common-issues-faced-with-azure-disk-backup"></a>Azure ディスク バックアップで発生する一般的な問題

### <a name="error-code-usererrorsnapshotrgsubscriptionmismatch"></a>Error Code:UserErrorSnapshotRGSubscriptionMismatch

エラー メッセージ:Invalid subscription selected for Snapshot Data store (スナップショット データ ストアに対して無効なサブスクリプションが選択されました)

推奨される操作:ディスクとディスク スナップショットは、同じサブスクリプション内に保存されます。 サブスクリプション内にディスク スナップショットを保存するために、任意のリソース グループを選択できます。 ソース ディスクと同じサブスクリプションを選択してください。 詳細については、[サポート マトリックス](disk-backup-support-matrix.md)に関するページをご覧ください。

### <a name="error-code-usererrorsnapshotrgnotfound"></a>Error Code:UserErrorSnapshotRGNotFound

エラー メッセージ:スナップショット データ ストア リソース グループが存在しないため、操作を実行できませんでした。

推奨される操作:リソース グループを作成して、必要なアクセス許可を指定してください。 詳細については、「[バックアップの構成](backup-managed-disks.md#configure-backup)」を参照してください。

### <a name="error-code-usererrormanageddisknotfound"></a>Error Code:UserErrorManagedDiskNotFound

エラー メッセージ:マネージド ディスクが存在しなくなったため、操作を実行できませんでした。

推奨される操作:ソース ディスクが削除されたか、別の場所に移動された可能性があるため、バックアップは失敗し続けます。 ディスクが誤って削除された場合は、既存の復元ポイントを使用してそのディスクを復元してください。 ディスクが別の場所に移動された場合は、ディスクのバックアップを構成してください。

### <a name="error-code-usererrornotenoughpermissionondisk"></a>Error Code:UserErrorNotEnoughPermissionOnDisk

エラー メッセージ:Azure Backup サービスには、この操作を実行するためにこのディスクに対する追加のアクセス許可が必要です。

推奨される操作:バックアップ コンテナーのマネージド ID に、このディスクに関する適切なアクセス許可を付与してください。 [ドキュメント](backup-managed-disks.md)を参照して、バックアップ コンテナーのマネージド ID に必要なアクセス許可と、その指定方法を確認してください。

### <a name="error-code-usererrornotenoughpermissiononsnapshotrg"></a>Error Code:UserErrorNotEnoughPermissionOnSnapshotRG

エラー メッセージ:Azure Backup サービスには、この操作を実行するためにスナップショット データ ストア リソース グループに対する追加のアクセス許可が必要です。

推奨される操作:バックアップ コンテナーのマネージド ID に、スナップショット データ ストアのリソース グループに関する適切なアクセス許可を付与してください。 スナップショット データ ストアのリソース グループは、ディスク スナップショットが保存される場所です。 [ドキュメント](backup-managed-disks.md)を参照して、リソース グループの概要、バックアップ コンテナーのマネージド ID に必要なアクセス許可、およびその指定方法を確認してください。

### <a name="error-code-usererrordiskbackupdiskormsipermissionsnotpresent"></a>Error Code:UserErrorDiskBackupDiskOrMSIPermissionsNotPresent

エラー メッセージ:無効なディスクです。または Azure Backup サービスには、この操作を実行するためにこのディスクに対する追加のアクセス許可が必要です

推奨される操作:ソース ディスクが削除されたか、別の場所に移動された可能性があるため、バックアップは失敗し続けます。 ディスクが誤って削除された場合は、既存の復元ポイントを使用してそのディスクを復元してください。 ディスクが別の場所に移動された場合は、ディスクのバックアップを構成してください。 ディスクが削除も移動もされていない場合は、バックアップ コンテナーのマネージド ID に、このディスクに関する適切なアクセス許可を付与してください。 [ドキュメント](backup-managed-disks.md)を参照して、バックアップ コンテナーのマネージド ID に必要なアクセス許可と、その指定方法を確認してください。

### <a name="error-code-usererrordiskbackupsnapshotrgormsipermissionsnotpresent"></a>Error Code:UserErrorDiskBackupSnapshotRGOrMSIPermissionsNotPresent

エラー メッセージ:Could not perform the operation as Snapshot Data store Resource Group no longer exists. (スナップショット データ ストアのリソース グループが存在しないため、操作を実行できませんでした。) Or Azure Backup Service requires additional permissions on the Snapshot Data store Resource Group to do this operation. (または、Azure Backup サービスで、この操作を実行するには、スナップショット データ ストアのリソース グループに関する追加のアクセス許可が必要です。)

推奨される操作:リソース グループを作成し、バックアップ コンテナーのマネージド ID に、スナップショット データ ストアのリソース グループに関する適切なアクセス許可を付与してください。 スナップショット データ ストアのリソース グループは、ディスク スナップショットが保存される場所です。 [ドキュメント](backup-managed-disks.md)を参照して、リソース グループの概要、バックアップ コンテナーのマネージド ID に必要なアクセス許可、およびその指定方法を確認してください。

### <a name="error-code-usererrordiskbackupauthorizationfailed"></a>Error Code:UserErrorDiskBackupAuthorizationFailed

エラー メッセージ:Backup Vault managed identity is missing the necessary permissions to do this operation. (バックアップ コンテナーのマネージド ID に、この操作を実行するために必要なアクセス許可がありません。)

推奨される操作:バックアップ コンテナーのマネージド ID に、バックアップするディスクに関する適切なアクセス許可と、スナップショットが保存されるスナップショット データ ストアのリソース グループに関する適切なアクセス許可を付与してください。 [ドキュメント](backup-managed-disks.md)を参照して、バックアップ コンテナーのマネージド ID に必要なアクセス許可と、その指定方法を確認してください。

### <a name="error-code-usererrorsnapshotrgormsipermissionsnotpresent"></a>Error Code:UserErrorSnapshotRGOrMSIPermissionsNotPresent

エラー メッセージ:Could not perform the operation as Snapshot Data store Resource Group no longer exists. (スナップショット データ ストアのリソース グループが存在しないため、操作を実行できませんでした。) または、Azure Backup サービスには、この操作を実行するためにスナップショット データ ストア リソース グループに対する追加のアクセス許可が必要です。

推奨される操作:リソース グループを作成し、バックアップ コンテナーのマネージド ID に、スナップショット データ ストアのリソース グループに関する適切なアクセス許可を付与してください。 スナップショット データ ストアのリソース グループは、スナップショットが保存される場所です。 [ドキュメント](backup-managed-disks.md)を参照して、リソース グループの概要、バックアップ コンテナーのマネージド ID に必要なアクセス許可、およびその指定方法を確認してください。

### <a name="error-code-usererroroperationalstoreparametersnotprovided"></a>Error Code:UserErrorOperationalStoreParametersNotProvided

エラー メッセージ:スナップショット データ ストア リソース グループのパラメーターが指定されていないため、操作を実行できませんでした

推奨される操作:スナップショット データ ストアのリソース グループのパラメーターを指定してください。 スナップショット データ ストアのリソース グループは、ディスク スナップショットが保存される場所です。 詳細については、[こちらのドキュメント](backup-managed-disks.md)をご覧ください。

### <a name="error-code-usererrorinvalidoperationalstoreresourcegroup"></a>Error Code:UserErrorInvalidOperationalStoreResourceGroup

エラー メッセージ:指定されたスナップショット データ ストア リソース グループが無効です

推奨される操作:スナップショット データ ストアのための有効なリソース グループを指定してください。 スナップショット データ ストアのリソース グループは、ディスク スナップショットが保存される場所です。 詳細については、[こちらのドキュメント](backup-managed-disks.md)をご覧ください。

### <a name="error-code-usererrordiskbackupdisktypenotsupported"></a>Error Code:UserErrorDiskBackupDiskTypeNotSupported

エラー メッセージ:サポートされていないディスクの種類

推奨される操作:[サポート マトリックス](disk-backup-support-matrix.md)を参照して、サポートされないシナリオと制限事項について確認してください。

### <a name="error-code-usererrorsamenamediskalreadyexists"></a>Error Code:UserErrorSameNameDiskAlreadyExists

エラー メッセージ:Could not restore as a Disk with same name already exists in the selected target resource group (選択したターゲット リソース グループに同じ名前のディスクが既に存在するため、復元できませんでした)

推奨される操作:復元用に別のディスク名を指定してください。 詳細については、[Azure Managed Disks の復元](restore-managed-disks.md)に関するページをご覧ください。

### <a name="error-code-usererrorrestoretargetrgnotfound"></a>Error Code:UserErrorRestoreTargetRGNotFound

エラー メッセージ:Operation failed as the Target Resource Group does not exist. (ターゲット リソース グループが存在しないため、操作が失敗しました。)

推奨される操作:復元する有効なリソース グループを指定してください。 詳細については、[Azure Managed Disks の復元](restore-managed-disks.md)に関するページをご覧ください。

### <a name="error-code-usererrornotenoughpermissionontargetrg"></a>Error Code:UserErrorNotEnoughPermissionOnTargetRG

エラー メッセージ:Azure Backup Service requires additional permissions on the Target Resource Group to do this operation. (Azure Backup サービスで、この操作を実行するには、ターゲット リソース グループに関する追加のアクセス許可が必要です。)

推奨される操作:バックアップ コンテナーのマネージド ID に、ターゲット リソース グループに関する適切なアクセス許可を付与してください。 ターゲット リソース グループは、ディスクの復元先として選択されている場所です。 [復元に関するドキュメント](restore-managed-disks.md)を参照して、バックアップ コンテナーのマネージド ID に必要なアクセス許可と、その指定方法を確認してください。

### <a name="error-code-usererrorsubscriptiondiskquotalimitreached"></a>Error Code:UserErrorSubscriptionDiskQuotaLimitReached

エラー メッセージ:Operation has failed as the Disk quota maximum limit has been reached on the subscription. (サブスクリプションのディスク クォータの上限に達したため、操作が失敗しました。)

推奨される操作:[Azure サブスクリプション、サービスの制限、クォータのドキュメント](../azure-resource-manager/management/azure-subscription-service-limits.md)を参照するか、Microsoft サポートに連絡して詳しいガイダンスを受けてください。

### <a name="error-code-usererrordiskbackuprestorergormsipermissionsnotpresent"></a>Error Code:UserErrorDiskBackupRestoreRGOrMSIPermissionsNotPresent

エラー メッセージ:Operation failed as the Target Resource Group does not exist. (ターゲット リソース グループが存在しないため、操作が失敗しました。) Or Azure Backup Service requires additional permissions on the Target Resource Group to do this operation. (または、Azure Backup サービスで、この操作を実行するには、ターゲット リソース グループに関する追加のアクセス許可が必要です。)

推奨される操作:復元する有効なリソース グループを指定し、バックアップ コンテナーのマネージド ID に、ターゲット リソース グループに関する適切なアクセス許可を付与してください。 ターゲット リソース グループは、ディスクの復元先として選択されている場所です。 [復元に関するドキュメント](restore-managed-disks.md)を参照して、バックアップ コンテナーのマネージド ID に必要なアクセス許可と、その指定方法を確認してください。

### <a name="error-code-usererrordeskeyvaultkeydisabled"></a>Error Code:UserErrorDESKeyVaultKeyDisabled

エラー メッセージ:ディスク暗号化セットに使用されるキー コンテナーのキーが有効な状態ではありません。

推奨される操作:ディスク暗号化セットに使用されるキー コンテナーのキーを有効にします。 [サポート マトリックス](disk-backup-support-matrix.md)で制限事項を参照してください。

### <a name="error-code-usererrormsipermissionsnotpresentondes"></a>Error Code:UserErrorMSIPermissionsNotPresentOnDES

エラー メッセージ:Azure Backup サービスには、ディスクで使用されるディスク暗号化セットにアクセスする許可が必要です。

推奨される操作:バックアップ コンテナーのマネージド ID に、ディスク暗号化セット (DES) への読み取りアクセス権を付与してください。

### <a name="error-code-usererrordeskeyvaultkeynotavailable"></a>Error Code:UserErrorDESKeyVaultKeyNotAvailable

エラー メッセージ:The key vault key used for disk encryption set is not available. (ディスク暗号化セットに使用されるキー コンテナーのキーが使用不可です。)

推奨される操作:ディスク暗号化セットに使用されるキー コンテナーのキーが無効になっていないこと、削除されていないことを確認してください。

### <a name="error-code-usererrordisksnapshotnotfound"></a>Error Code:UserErrorDiskSnapshotNotFound

エラー メッセージ:The disk snapshot for this Restore point has been deleted. (この復元ポイントのディスク スナップショットは削除されました。)

推奨される操作:スナップショットは、サブスクリプション内のスナップショット データ ストア リソース グループに保存されます。 選択した復元ポイントに関連するスナップショットがこのリソース グループから削除されたか、移動された可能性があります。 別の復旧ポイントを使用して復元することを検討してください。 また、[復元に関するドキュメント](restore-managed-disks.md)で説明されている、スナップショット リソース グループを選択するための推奨ガイドラインに従ってください。

### <a name="error-code-usererrorsnapshotmetadatanotfound"></a>Error Code:UserErrorSnapshotMetadataNotFound

エラー メッセージ:The disk snapshot metadata for this Restore point has been deleted (この復元ポイントのディスク スナップショット メタデータは削除されました。)

推奨される操作:別の復旧ポイントを使用して復元することを検討してください。 詳細については、[復元に関するドキュメント](restore-managed-disks.md)を参照してください。

### <a name="error-code-backupagentpluginhostvalidateprotectionerror"></a>Error Code:BackupAgentPluginHostValidateProtectionError

エラー メッセージ:Disk Backup is not yet available in the region of the Backup Vault under which Configure Protection is being tried. (ディスクのバックアップは、保護の構成が試行されているバックアップ コンテナーのリージョンではまだ使用できません。)

推奨される操作:バックアップ コ ンテナーは、プレビューがサポートされているリージョンに存在しなければなりません。 利用可能なリージョンについては、[サポート マトリックス](disk-backup-support-matrix.md)に関するページを参照してください。

### <a name="error-code-usererrordppdatasourcealreadyhasbackupinstance"></a>Error Code:UserErrorDppDatasourceAlreadyHasBackupInstance

エラー メッセージ:The disk you are trying to configure backup is already being protected. (バックアップを構成しようとしているディスクは既に保護されています。) Disk is already associated with a backup instance in a Backup vault. (ディスクは、バックアップ コンテナー内のバックアップ インスタンスに既に関連付けられています。)

推奨される操作:このディスクは、バックアップ コンテナー内のバックアップ インスタンスに既に関連付けられています。 このディスクを保護し直す場合は、バックアップ インスタンスを、それが現在保護されているバックアップ コンテナーから削除し、他のコンテナー内でディスクの保護をやり直します。

### <a name="error-code-usererrordppdatasourcealreadyprotected"></a>Error Code:UserErrorDppDatasourceAlreadyProtected

エラー メッセージ:The disk you are trying to configure backup is already being protected. (バックアップを構成しようとしているディスクは既に保護されています。) Disk is already associated with a backup instance in a Backup vault. (ディスクは、バックアップ コンテナー内のバックアップ インスタンスに既に関連付けられています。)

推奨される操作:このディスクは、バックアップ コンテナー内のバックアップ インスタンスに既に関連付けられています。 このディスクを保護し直す場合は、バックアップ インスタンスを、それが現在保護されているバックアップ コンテナーから削除し、他のコンテナー内でディスクの保護をやり直します。

### <a name="error-code-usererrormaxconcurrentoperationlimitreached"></a>Error Code:UserErrorMaxConcurrentOperationLimitReached

エラー メッセージ:Unable to start the operation as maximum number of allowed concurrent backups has reached. (許可されている同時バックアップの最大数に達したため、操作を開始できません。)

推奨される操作:先に実行中のバックアップが完了するまで待ちます。

## <a name="next-steps"></a>次の手順

- [Azure ディスク バックアップのサポート マトリックス](disk-backup-support-matrix.md)