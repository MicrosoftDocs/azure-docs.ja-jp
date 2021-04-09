---
title: PowerShell を使用して Azure Files を復元する
description: この記事では、Azure Backup サービスと PowerShell を使用して Azure Files を復元する方法について説明します。
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: 60c9848e12de80bcafe4553a9e8f3e27e8876d41
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96021386"
---
# <a name="restore-azure-files-with-powershell"></a>PowerShell を使用して Azure Files を復元する

この記事では、Azure Powershell を使用して、[Azure Backup](backup-overview.md) サービスによって作成された復元ポイントから、ファイル共有全体または特定のファイルを復元する方法について説明します。

ファイル共有全体を復元することも、共有上の特定のファイルを復元することもできます。 元の場所に復元することも、代わりの場所に復元することもできます。

> [!WARNING]
> PowerShell のバージョンが、AFS バックアップ用の "Az.RecoveryServices 2.6.0" のための最小バージョンにアップグレードされていることを確認してください。 詳細については、この変更の要件の概要が記載されている[このセクション](backup-azure-afs-automation.md#important-notice-backup-item-identification)を参照してください。

>[!NOTE]
>Azure Backup では、PowerShell を使用して、元の場所または別の場所への複数のファイルやフォルダーの復元をサポートするようになりました。 方法については、ドキュメントの[このセクション](#restore-multiple-files-or-folders-to-original-or-alternate-location)を参照してください。

## <a name="fetch-recovery-points"></a>復旧ポイントをフェッチする

[Get-AzRecoveryServicesBackupRecoveryPoint](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint) を使用して、バックアップされた項目のすべての復旧ポイントを一覧表示します。

スクリプトの説明:

* 変数 **$rp** は、過去 7 日間に選択したバックアップ項目の復旧ポイントの配列です。
* この配列は時間の逆順で並べ替えられています。最新の復旧ポイントのインデックスは **0** です。
* 標準の PowerShell 配列のインデックスを使用して、復旧ポイントを選択します。
* 次の例では、 **$rp[0]** によって最新の復旧ポイントが選択されます。

```powershell
$vault = Get-AzRecoveryServicesVault -ResourceGroupName "azurefiles" -Name "azurefilesvault"
$Container = Get-AzRecoveryServicesBackupContainer -ContainerType AzureStorage -Status Registered -FriendlyName "afsaccount" -VaultId $vault.ID
$BackupItem = Get-AzRecoveryServicesBackupItem -Container $Container -WorkloadType AzureFiles -VaultId $vault.ID -FriendlyName "azurefiles"
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $BackupItem -VaultId $vault.ID -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
$rp[0] | fl
```

次のように出力されます。

```powershell
FileShareSnapshotUri : https://testStorageAcct.file.core.windows.net/testAzureFS?sharesnapshot=2018-11-20T00:31:04.00000
                       00Z
RecoveryPointType    : FileSystemConsistent
RecoveryPointTime    : 11/20/2018 12:31:05 AM
RecoveryPointId      : 86593702401459
ItemName             : testAzureFS
Id                   : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Micros                      oft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;teststorageRG;testStorageAcct/protectedItems/AzureFileShare;testAzureFS/recoveryPoints/86593702401462
WorkloadType         : AzureFiles
ContainerName        : storage;teststorageRG;testStorageAcct
ContainerType        : AzureStorage
BackupManagementType : AzureStorage
```

関連する復旧ポイントが選択されたら、ファイル共有またはファイルを、その元の場所か代わりの場所に復元します。

## <a name="restore-an-azure-file-share-to-an-alternate-location"></a>Azure ファイル共有を代わりの場所に復元する

[Restore-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) を使用して、選択された復旧ポイントに復元します。 代わりの場所を特定するため、次のパラメーターを指定します。

* **TargetStorageAccountName**:バックアップされたコンテンツの復元先となるストレージ アカウント。 このストレージ アカウントは、コンテナーと同じ場所にある必要があります。
* **TargetFileShareName**:バックアップされたコンテンツの復元先となるターゲット ストレージ アカウント内のファイル共有。
* **TargetFolder**:データの復元先となるファイル共有の下のフォルダー。 バックアップされたコンテンツをルート フォルダーに復元する必要がある場合は、ターゲット フォルダーの値として空の文字列を指定します。
* **ResolveConflict**:復元されたデータとの競合が発生した場合の指示。 **Overwrite** または **Skip** を指定できます。

次のようにパラメーターを指定してコマンドレットを実行します。

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -ResolveConflict Overwrite
```

このコマンドでは、次の例に示すように、追跡する必要がある ID を持つジョブが返されます。

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS        Restore              InProgress           12/10/2018 9:56:38 AM                               9fd34525-6c46-496e-980a-3740ccb2ad75
```

## <a name="restore-an-azure-file-to-an-alternate-location"></a>Azure ファイルを代わりの場所に復元する

[Restore-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) を使用して、選択された復旧ポイントに復元します。 代わりの場所を特定し、復元するファイルを一意に特定するため、次のパラメーターを指定します。

* **TargetStorageAccountName**:バックアップされたコンテンツの復元先となるストレージ アカウント。 このストレージ アカウントは、コンテナーと同じ場所にある必要があります。
* **TargetFileShareName**:バックアップされたコンテンツの復元先となるターゲット ストレージ アカウント内のファイル共有。
* **TargetFolder**:データの復元先となるファイル共有の下のフォルダー。 バックアップされたコンテンツをルート フォルダーに復元する必要がある場合は、ターゲット フォルダーの値として空の文字列を指定します。
* **SourceFilePath**:文字列で表わされた、ファイル共有内の復元するファイルの絶対パス。 このパスは **Get-AzStorageFile** PowerShell コマンドレットで使用されるのと同じパスです。
* **SourceFileType**:選択されるのはディレクトリかファイルか。 **Directory** または **File** を指定できます。
* **ResolveConflict**:復元されたデータとの競合が発生した場合の指示。 **Overwrite** または **Skip** を指定できます。

追加のパラメーター (SourceFilePath および SourceFileType) は、復元する個々のファイルにのみ関連しています。

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

前のセクションで示したように、このコマンドは追跡する必要がある ID を持つジョブを返します。

## <a name="restore-azure-file-shares-and-files-to-the-original-location"></a>Azure ファイル共有およびファイルを元の場所に復元する

元の場所に復元する場合、復元先とターゲットに関連するパラメーターを指定する必要はありません。 **ResolveConflict** のみ指定する必要があります。

### <a name="overwrite-an-azure-file-share"></a>Azure ファイル共有を上書きする

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -ResolveConflict Overwrite
```

### <a name="overwrite-an-azure-file"></a>Azure ファイルを上書きする

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

## <a name="restore-multiple-files-or-folders-to-original-or-alternate-location"></a>複数のファイルまたはフォルダーを元の場所または別の場所に復元する

復元するすべてのファイルまたはフォルダーのパスを **MultipleSourceFilePath** パラメーターの値として渡すことによって、[Restore-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) コマンドを使用します。

### <a name="restore-multiple-files"></a>複数のファイルを復元する

次のスクリプトでは、*FileSharePage.png* と、*MyTestFile .txt* ファイルの復元を試みます。

```powershell
$vault = Get-AzRecoveryServicesVault -ResourceGroupName "azurefiles" -Name "azurefilesvault"

$Container = Get-AzRecoveryServicesBackupContainer -ContainerType AzureStorage -Status Registered -FriendlyName "afsaccount" -VaultId $vault.ID

$BackupItem = Get-AzRecoveryServicesBackupItem -Container $Container -WorkloadType AzureFiles -VaultId $vault.ID -FriendlyName "azurefiles"

$RP = Get-AzRecoveryServicesBackupRecoveryPoint -Item $BackupItem -VaultId $vault.ID

$files = ("FileSharePage.png", "MyTestFile.txt")

Restore-AzRecoveryServicesBackupItem -RecoveryPoint $RP[0] -MultipleSourceFilePath $files -SourceFileType File -ResolveConflict Overwrite -VaultId $vault.ID -VaultLocation $vault.Location
```

### <a name="restore-multiple-directories"></a>複数のディレクトリを復元する

次のスクリプトでは、*zrs1_restore* を復元し、ディレクトリを *復元* しようとしています。

```powershell
$vault = Get-AzRecoveryServicesVault -ResourceGroupName "azurefiles" -Name "azurefilesvault"

$Container = Get-AzRecoveryServicesBackupContainer -ContainerType AzureStorage -Status Registered -FriendlyName "afsaccount" -VaultId $vault.ID

$BackupItem = Get-AzRecoveryServicesBackupItem -Container $Container -WorkloadType AzureFiles -VaultId $vault.ID -FriendlyName "azurefiles"

$RP = Get-AzRecoveryServicesBackupRecoveryPoint -Item $BackupItem -VaultId $vault.ID

$files = ("Restore","zrs1_restore")

Restore-AzRecoveryServicesBackupItem -RecoveryPoint $RP[0] -MultipleSourceFilePath $files -SourceFileType Directory -ResolveConflict Overwrite -VaultId $vault.ID -VaultLocation $vault.Location
```

出力は次のようになります。

```output
WorkloadName         Operation         Status          StartTime                EndTime       JobID
------------         ---------         ------          ---------                -------       -----
azurefiles           Restore           InProgress      4/5/2020 8:01:24 AM                    cd36abc3-0242-44b1-9964-0a9102b74d57
```

複数のファイルまたはフォルダーを別の場所に復元する場合は、上記の「[Azure ファイルを代わりの場所に復元する](#restore-an-azure-file-to-an-alternate-location)」の説明に従って、ターゲットの場所に関連するパラメーター値を指定して、上記のスクリプトを使用します。

## <a name="next-steps"></a>次のステップ

Azure portal での Azure Files の復元について[学習します](restore-afs.md)。
