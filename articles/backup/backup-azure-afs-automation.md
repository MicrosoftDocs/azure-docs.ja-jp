---
title: Azure Backup と PowerShell を使用して Azure Files をバックアップおよび復元する
description: Azure Backup と PowerShell を使用して Azure Files をバックアップおよび復元します。
author: pvrk
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 03/05/2018
ms.author: pullabhk
ms.openlocfilehash: 83fe8d17699c19d442fd734d71d828eb9fd9d6ed
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2019
ms.locfileid: "58258362"
---
# <a name="back-up-and-restore-azure-files-with-powershell"></a>PowerShell を使用して Azure Files をバックアップおよび復元する

この記事では、[Azure Backup](backup-overview.md) Recovery Services コンテナーを使用して Azure Files のファイル共有をバックアップおよび復元するために Azure PowerShell を使用する方法を説明します。 

このチュートリアルでは、次の方法について説明します。

> [!div class="checklist"]
> * PowerShell を設定し、Azure Recovery Services プロバイダーを登録する。
> * Recovery Services コンテナーを作成する。
> * Azure ファイル共有のバックアップを構成する。
> * バックアップ ジョブを実行する。
> * バックアップされた Azure ファイル共有、または共有に含まれる個々のファイルを復元する。
> * バックアップ ジョブと復元ジョブを監視する。


## <a name="before-you-start"></a>開始する前に

- Recovery Services コンテナーについての[詳細情報](backup-azure-recovery-services-vault-overview.md)を確認します。
- [Azure ファイル共有のバックアップ](backup-azure-files.md)に関するプレビュー機能を確認します。
- Recovery Services の PowerShell オブジェクト階層を確認します。


## <a name="recovery-services-object-hierarchy"></a>Recovery Services オブジェクトの階層

オブジェクト階層の概要を次の図に示します。

![Recovery Services オブジェクトの階層](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Azure ライブラリに含まれる **Az.RecoveryServices** [コマンドレット リファレンス](/powershell/module/az.recoveryservices)のリファレンスを確認します。


## <a name="set-up-and-install"></a>設定とインストール

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

PowerShell を次のように設定します。

1. [最新バージョンの Az PowerShell をダウンロードします](/powershell/azure/install-az-ps)。 必要な最小バージョンは 1.0.0 です。

2. 次のコマンドを使用して、Azure Backup PowerShell コマンドレットを検索します。

    ```powershell
    Get-Command *azrecoveryservices*
    ```
3. Azure Backup、Azure Site Recovery、Recovery Services コンテナーの別名とコマンドレットを確認します。 表示例を次に示します。 これはコマンドレットの完全な一覧ではありません。

    ![Recovery Services コマンドレットの一覧](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

3. **Connect-AzAccount** を使用して Azure アカウントにサインインします。
4. 表示される Web ページで、アカウントの資格情報を入力するように求められます。

    - または、**-Credential** を使用して、**Connect-AzAccount** コマンドレットのパラメーターとしてアカウントの資格情報を含めることもできます。
    - CSP パートナーがテナントの代理としてサインインする場合は、その顧客をテナントとして指定します。該当する tenantID またはテナントのプライマリ ドメイン名で指定してください。 たとえば、「**Connect-AzAccount -Tenant** fabrikam.com」を指定します。

4. 1 つのアカウントが複数のサブスクリプションを持つことができるため、使用するサブスクリプションをアカウントに関連付けます。

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

5. Azure Backup を初めて使用する場合、**Register-AzResourceProvider** コマンドレットを使って Azure Recovery Services プロバイダーをサブスクリプションに登録します。

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

6. プロバイダーが正常に登録されたことを確認します。

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```
7. コマンド出力で、**RegistrationState** が **Registered** に変わっていることを確認します。 変わっていない場合は、**Register-AzResourceProvider** コマンドレットをもう一度実行します。



## <a name="create-a-recovery-services-vault"></a>Recovery Services コンテナーを作成する

Recovery Services コンテナーを作成するには、次の手順に従います。

- Recovery Services コンテナーは Resource Manager のリソースであるため、リソース グループ内に配置する必要があります。 既存のリソース グループを使用することも、**New-AzResourceGroup** コマンドレットを使ってリソース グループを作成することもできます。 リソース グループを作成するときは、リソース グループの名前と場所を指定します。 

1. コンテナーはリソース グループに配置されます。 既存のリソース グループがない場合は、[New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-1.4.0) を使用して新しいリソース グループを作成します。 この例では、新しいリソース グループを米国西部リージョンに作成します。

   ```powershell
   New-AzResourceGroup -Name "test-rg" -Location "West US"
   ```
2. [New-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/New-AzRecoveryServicesVault?view=azps-1.4.0) コマンドレットを使用して、コンテナーを作成します。 リソース グループに使用したのと同じコンテナーの場所を指定します。

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```
3. コンテナー ストレージに使用する冗長性の種類を指定します。

   - [ローカル冗長ストレージ](../storage/common/storage-redundancy-lrs.md)または [geo 冗長ストレージ](../storage/common/storage-redundancy-grs.md)を使用できます。
   - 次の例では、**testvault** の [Set-AzRecoveryServicesBackupProperties](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperties?view=azps-1.4.0) コマンドの **-BackupStorageRedundancy** オプションが **GeoRedundant** に設定されています。

     ```powershell
     $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
     Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
     ```

### <a name="view-the-vaults-in-a-subscription"></a>サブスクリプション内のコンテナーの表示

サブスクリプション内のコンテナーをすべて表示するには、[Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0) を使用します。

```powershell
Get-AzRecoveryServicesVault
```

出力は次のようになります。 関連するリソース グループと場所が指定されていることに注目してください。

```powershell
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

### <a name="set-the-vault-context"></a>コンテナーのコンテキストを設定する

このコンテナー オブジェクトを変数に格納し、そのコンテナーのコンテキストを設定します。

- Azure Backup コマンドレットの多くは、入力として Recovery Services コンテナー オブジェクトを必要とするので、コンテナー オブジェクトを変数に格納すると便利です。
- コンテナーのコンテキストとは、コンテナーで保護されるデータの種類です。 これを、[Set-AzRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0) を使用して設定します。 コンテキストが設定されると、それが後続のすべてのコマンドレットに適用されます。


次の例では、**testvault** のコンテナーのコンテキストを設定します。

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>コンテナー ID をフェッチする

コンテナーのコンテキストの設定は、Azure PowerShell のガイドラインに従って廃止される予定です。 代わりに、次のようにしてコンテナー ID を格納またはフェッチしたり、関連するコマンドに渡したりできます。

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

## <a name="configure-a-backup-policy"></a>バックアップ ポリシーを構成する

バックアップ ポリシーは、バックアップのスケジュール、およびバックアップの復旧ポイントを保持する期間を指定します。

- バックアップ ポリシーは、少なくとも 1 つのアイテム保持ポリシーと関連付けられています。 アイテム保持ポリシーでは、復旧ポイントが削除される前に保持される期間が定義されています。
- [Get-AzRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject?view=azps-1.4.0) を使用して、既定のバックアップ ポリシー リテンション期間を表示します。
- [Get-AzRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject?view=azps-1.4.0) を使用して、既定のバックアップ ポリシー スケジュールを表示します。
-  新しいバックアップ ポリシーを作成するには、[New-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0) コマンドレットを使用します。 スケジュール ポリシー オブジェクトとアイテム保持ポリシー オブジェクトを入力します。

次の例では、スケジュール ポリシーとアイテム保持ポリシーを変数に格納します。 その後に、それらの変数を新しいポリシー (**NewAFSPolicy**) のパラメーターとして使用しています。 **NewAFSPolicy** は毎日のバックアップを受け取り、30 日間保持します。

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureFiles"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

出力は次のようになります。

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewAFSPolicy           AzureFiles            AzureStorage              10/24/2017 1:30:00 AM
```



## <a name="enable-backup"></a>バックアップの有効化

バックアップ ポリシーを定義したら、このポリシーを使用して、Azure ファイル共有の保護を有効にできます。

### <a name="retrieve-a-backup-policy"></a>バックアップ ポリシーを取得する

[Get-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0) を使用して、関連するポリシー オブジェクトをフェッチします。 このコマンドレットを使用して、特定のポリシーを取得したり、ワークロードの種類に関連付けられているポリシーを表示したりできます。

#### <a name="retrieve-a-policy-for-a-workload-type"></a>ワークロードの種類のポリシーを取得する

次の例では、ワークロードの種類が **AzureFiles** であるポリシーを取得します。

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureFiles"
```

出力は次のようになります。

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
dailyafs             AzureFiles         AzureStorage         1/10/2018 12:30:00 AM
```
> [!NOTE]
> PowerShell の **BackupTime** フィールドのタイム ゾーンは協定世界時 (UTC) です。 Azure portal 上でバックアップ時刻が表示されるときに、時刻はローカル タイム ゾーンに調整されます。

### <a name="retrieve-a-specific-policy"></a>特定のポリシーを取得する

次のポリシーでは、**dailyafs** という名前のバックアップ ポリシーを取得します。

```powershell
$afsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "dailyafs"
```

### <a name="enable-backup-and-apply-policy"></a>バックアップを有効にしてポリシーを適用する

[Enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection?view=azps-1.4.0) を使用して、保護を有効にします。 ポリシーがコンテナーに関連付けられると、ポリシーのスケジュールに従ってバックアップがトリガーされます。

次の例では、ポリシー **dailyafs** を使用して、ストレージ アカウント **testStorageAcct** の Azure ファイル共有 **testAzureFileShare** の保護を有効にしています。

```powershell
Enable-AzRecoveryServicesBackupProtection -StorageAccountName "testStorageAcct" -Name "testAzureFS" -Policy $afsPol
```

このコマンドは、保護の構成ジョブが完了し、下に示すような出力が得られるまで待機します。

```cmd
WorkloadName       Operation            Status                 StartTime                                                                                                         EndTime                   JobID
------------             ---------            ------               ---------                                  -------                   -----
testAzureFS       ConfigureBackup      Completed            11/12/2018 2:15:26 PM     11/12/2018 2:16:11 PM     ec7d4f1d-40bd-46a4-9edb-3193c41f6bf6
```

## <a name="trigger-an-on-demand-backup"></a>オンデマンド バックアップをトリガーする

[Backup-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem?view=azps-1.4.0) を使用して、保護されている Azure ファイル共有のオンデマンド バックアップを実行します。

1. [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer) を使用して、バックアップ データを保持するコンテナー内のコンテナーからストレージ アカウントとファイル共有を取得します。
2. バックアップ ジョブを開始するには、[Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem) を使用して、VM に関する情報を取得します。
3. [Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem) を使用して、オンデマンド バックアップを実行します。

次のようにしてオンデマンド バックアップを実行します。
    
```powershell
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType "AzureFiles" -Name "testAzureFS"
$job =  Backup-AzRecoveryServicesBackupItem -Item $afsBkpItem
```

このコマンドでは、次の例に示すように、追跡する必要がある ID を持つジョブが返されます。

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS       Backup               Completed            11/12/2018 2:42:07 PM     11/12/2018 2:42:11 PM     8bdfe3ab-9bf7-4be6-83d6-37ff1ca13ab6
```

バックアップ作成時には Azure ファイル共有のスナップショットが使用されるため、通常は、コマンドからこの出力が返されるまでにジョブが完了します。

### <a name="modify-the-protection-policy"></a>保護ポリシーを変更する

Azure ファイル共有のバックアップに使用するポリシーを変更するには、[Enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection?view=azps-1.4.0) を使用します。 関連するバックアップ項目と新しいバックアップ ポリシーを指定します。

次の例では、**testAzureFS** の保護ポリシーを **dailyafs** から **monthlyafs** に変更します。

```powershell
$monthlyafsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "monthlyafs"
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType AzureFiles -Name "testAzureFS"
Enable-AzRecoveryServicesBackupProtection -Item $afsBkpItem -Policy $monthlyafsPol
```

## <a name="restore-azure-file-shares-and-files"></a>Azure ファイル共有とファイルを復元する

ファイル共有全体を復元することも、共有上の特定のファイルを復元することもできます。 元の場所に復元することも、代わりの場所に復元することもできます。 

### <a name="fetch-recovery-points"></a>復旧ポイントをフェッチする

[Get-AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint?view=azps-1.4.0) を使用して、バックアップされた項目のすべての復旧ポイントを一覧表示します。

スクリプトの説明:

- 変数 **$rp** は、過去 7 日間に選択したバックアップ項目の復旧ポイントの配列です。
- この配列は時間の逆順で並べ替えられています。最新の復旧ポイントのインデックスは **0** です。
- 標準の PowerShell 配列のインデックスを使用して、復旧ポイントを選択します。
- 次の例では、**$rp[0]** によって最新の復旧ポイントが選択されます。

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $afsBkpItem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()

$rp[0] | fl
```

出力は次のようになります。

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

### <a name="restore-an-azure-file-share-to-an-alternate-location"></a>Azure ファイル共有を代わりの場所に復元する

[Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/en-us/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0) を使用して、選択された復旧ポイントに復元します。 代わりの場所を特定するため、次のパラメーターを指定します。 

- **TargetStorageAccountName**:バックアップされたコンテンツの復元先となるストレージ アカウント。 このストレージ アカウントは、コンテナーと同じ場所にある必要があります。
- **TargetFileShareName**:バックアップされたコンテンツの復元先となるターゲット ストレージ アカウント内のファイル共有。
- **TargetFolder**:データの復元先となるファイル共有の下のフォルダー。 バックアップされたコンテンツをルート フォルダーに復元する必要がある場合は、ターゲット フォルダーの値として空の文字列を指定します。
- **ResolveConflict**:復元されたデータとの競合が発生した場合の指示。 **Overwrite** または **Skip** を指定できます。

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

### <a name="restore-an-azure-file-to-an-alternate-location"></a>Azure ファイルを代わりの場所に復元する

[Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/en-us/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0) を使用して、選択された復旧ポイントに復元します。 代わりの場所を特定し、復元するファイルを一意に特定するため、次のパラメーターを指定します。

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

### <a name="restore-azure-file-shares-and-files-to-the-original-location"></a>Azure ファイル共有およびファイルを元の場所に復元する

元の場所に復元する場合、復元先とターゲットに関連するパラメーターを指定する必要はありません。 **ResolveConflict** のみ指定する必要があります。

#### <a name="overwrite-an-azure-file-share"></a>Azure ファイル共有を上書きする

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -ResolveConflict Overwrite
```

#### <a name="overwrite-an-azure-file"></a>Azure ファイルを上書きする

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

## <a name="track-backup-and-restore-jobs"></a>バックアップ ジョブと復元ジョブを追跡する

オンデマンドのバックアップ操作や復元操作では、[オンデマンド バックアップの実行](#trigger-an-on-demand-backup)で示したように、ID と共にジョブが返されます。 ジョブの進行状況と詳細を追跡するには、[Get-AzRecoveryServicesBackupJobDetails](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob?view=azps-1.4.0) コマンドレットを使用します。

```powershell
$job = Get-AzRecoveryServicesBackupJob -JobId 00000000-6c46-496e-980a-3740ccb2ad75 -VaultId $vaultID

 $job | fl


IsCancellable        : False
IsRetriable          : False
ErrorDetails         : {Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureFileShareJobErrorInfo}
ActivityId           : 00000000-5b71-4d73-9465-8a4a91f13a36
JobId                : 00000000-6c46-496e-980a-3740ccb2ad75
Operation            : Restore
Status               : Failed
WorkloadName         : testAFS
StartTime            : 12/10/2018 9:56:38 AM
EndTime              : 12/10/2018 11:03:03 AM
Duration             : 01:06:24.4660027
BackupManagementType : AzureStorage

$job.ErrorDetails

 ErrorCode ErrorMessage                                          Recommendations
 --------- ------------                                          ---------------
1073871825 Microsoft Azure Backup encountered an internal error. Wait for a few minutes and then try the operation again. If the issue persists, please contact Microsoft support.
```
## <a name="next-steps"></a>次の手順
Azure portal での Azure Files のバックアップについて[学習します](backup-azure-files.md)。
