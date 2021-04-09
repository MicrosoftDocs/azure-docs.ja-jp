---
title: PowerShell を使用して Azure ファイル共有をバックアップする
description: この記事では、Azure Backup サービスと PowerShell を使用して Azure Files のファイル共有をバックアップする方法について説明します。
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: 948931764769bc967b88e7942b7e8384b0f93dff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87077002"
---
# <a name="back-up-an-azure-file-share-by-using-powershell"></a>PowerShell を使用して Azure ファイル共有をバックアップする

この記事では、[Azure Backup](backup-overview.md) Recovery Services コンテナーを通して Azure Files のファイル共有をバックアップするために Azure PowerShell を使用する方法を説明します。

この記事では、以下の方法について説明します。

> [!div class="checklist"]
>
> * PowerShell を設定し、Recovery Services プロバイダーを登録します。
> * Recovery Services コンテナーを作成する。
> * Azure ファイル共有のバックアップを構成する。
> * バックアップ ジョブを実行します。

## <a name="before-you-start"></a>開始する前に

* Recovery Services コンテナーについての[詳細情報](backup-azure-recovery-services-vault-overview.md)を確認します。
* Azure ライブラリの Az.RecoveryServices [コマンドレット リファレンス](/powershell/module/az.recoveryservices)のリファレンスを確認します。
* 次に示す Recovery Services の PowerShell オブジェクト階層を確認します。

  ![Recovery Services オブジェクトの階層](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

## <a name="set-up-powershell"></a>PowerShell のセットアップ

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

PowerShell を次のように設定します。

1. [Azure PowerShell の最新バージョンをダウンロードしてください](/powershell/azure/install-az-ps)。

    > [!NOTE]
    > Azure ファイル共有のバックアップに必要な最小 PowerShell バージョンは Az.RecoveryServices 2.6.0 です。 最新バージョン、または少なくとも最小バージョンを使用すれば、既存のスクリプトに関する問題を回避するのに役立ちます。 次の PowerShell コマンドを使用して、最小バージョンをインストールしてください。
    >
    > ```powershell
    > Install-module -Name Az.RecoveryServices -RequiredVersion 2.6.0
    > ```

2. 次のコマンドを使用して、Azure Backup 用の PowerShell コマンドレットを検索します。

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. Azure Backup、Azure Site Recovery、Recovery Services コンテナーの別名とコマンドレットを確認します。 表示例を次に示します。 これはコマンドレットの完全な一覧ではありません。

    ![Recovery Services コマンドレットの一覧](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. **Connect-AzAccount** を使用して Azure アカウントにサインインします。
5. 表示される Web ページで、アカウントの資格情報を入力するように求められます。

    または、 **-Credential** を使用して、**Connect-AzAccount** コマンドレットのパラメーターとしてアカウントの資格情報を含めることもできます。

    CSP パートナーとしてテナントに代わって作業する場合は、顧客をテナントとして指定します。 彼らのテナント ID またはテナントプライマリ ドメイン名を使用します。 たとえば、**Connect-AzAccount -Tenant "fabrikam.com"** 。

6. 1 つのアカウントが複数のサブスクリプションを持つことができるため、使用するサブスクリプションをアカウントに関連付けます。

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

7. Azure Backup を初めて使用する場合、**Register-AzResourceProvider** コマンドレットを使用して Azure Recovery Services プロバイダーをサブスクリプションに登録します。

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

8. プロバイダーが正常に登録されたことを確認します。

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

9. コマンド出力で、**RegistrationState** が **Registered** に変わっていることを確認します。 変わっていない場合は、**Register-AzResourceProvider** コマンドレットをもう一度実行します。

## <a name="create-a-recovery-services-vault"></a>Recovery Services コンテナーを作成する

Recovery Services コンテナーは Resource Manager のリソースであるため、リソース グループ内に配置する必要があります。 既存のリソース グループを使用することも、**New-AzResourceGroup** コマンドレットを使ってリソース グループを作成することもできます。 リソース グループを作成するときは、その名前と場所を指定します。

Recovery Services コンテナーを作成するには、次の手順に従います。

1. 既存のリソース グループがない場合は、[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) コマンドレットを使用して新しいものを作成します。 この例では、リソース グループを米国西部リージョンに作成します。

   ```powershell
   New-AzResourceGroup -Name "test-rg" -Location "West US"
   ```

1. [New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) コマンドレットを使用して、コンテナーを作成します。 リソース グループに使用したのと同じコンテナーの場所を指定します。

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

### <a name="view-the-vaults-in-a-subscription"></a>サブスクリプション内のコンテナーの表示

サブスクリプション内のコンテナーをすべて表示するには、[Get-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/get-azrecoveryservicesvault) を使用します。

```powershell
Get-AzRecoveryServicesVault
```

次のように出力されます。 出力には、関連付けられたリソース グループと場所が表示されることに注意してください。

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

Azure Backup コマンドレットの多くは、入力として Recovery Services コンテナー オブジェクトを必要とするので、コンテナー オブジェクトを変数に格納すると便利です。

コンテナーのコンテキストとは、コンテナーで保護されるデータの種類です。 これは、[Set-AzRecoveryServicesVaultContext](/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext) を使用して設定します。 コンテキストが設定されると、それが後続のすべてのコマンドレットに適用されます。

次の例では、**testvault** のコンテナーのコンテキストを設定します。

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>コンテナー ID をフェッチする

コンテナーのコンテキストの設定は、Azure PowerShell のガイドラインに従って廃止される予定です。 代わりに、次のようにコンテナー ID を格納またはフェッチし、関連するコマンドに渡すことができます。 コンテナーのコンテキストを設定していない場合または特定のコンテナーに対して実行するコマンドを指定する場合は、次のように関連するすべてのコマンドにコンテナー ID を `-vaultID` として渡します。

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol -VaultID $vaultID
```

## <a name="configure-a-backup-policy"></a>バックアップ ポリシーを構成する

バックアップ ポリシーでは、バックアップのスケジュール、およびバックアップの復旧ポイントを保持する期間を指定します。

バックアップ ポリシーは、少なくとも 1 つのアイテム保持ポリシーと関連付けられています。 アイテム保持ポリシーでは、復旧ポイントが削除される前に保持される期間が定義されています。 日単位、週単位、月単位、年単位の保持期間でバックアップを構成することができます。

バックアップ ポリシー用のコマンドレットをいくつか次に示します。

* [Get-AzRecoveryServicesBackupRetentionPolicyObject](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject) を使用して、既定のバックアップ ポリシー保持期間を表示します。
* [Get-AzRecoveryServicesBackupSchedulePolicyObject](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject) を使用して、既定のバックアップ ポリシー スケジュールを表示します。
* [New-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy) を使用して、新しいバックアップ ポリシーを作成します。 入力としてスケジュールとアイテム保持ポリシー オブジェクトを入力します。

既定では、開始時刻はスケジュール ポリシー オブジェクトで定義されます。 開始時刻を希望の時刻に変更するには、次の例を使用します。 希望する開始時刻は、協定世界時 (UTC) とする必要があります。 次の例では、毎日のバックアップの希望の開始時刻が午前 01:00 UTC であるものとします。

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$UtcTime = Get-Date -Date "2019-03-20 01:30:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> 開始時刻は 30 分単位のみで指定する必要があります。 前の例では、"01:00:00" または "02:30:00" のみを指定できます。 開始時刻を "01:15:00" にすることはできません。

次の例では、スケジュール ポリシーとアイテム保持ポリシーを変数に格納します。 その後に、それらの変数を新しいポリシー (**NewAFSPolicy**) のパラメーターとして使用しています。 **NewAFSPolicy** は毎日のバックアップを受け取り、30 日間保持します。

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureFiles"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

次のように出力されます。

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewAFSPolicy           AzureFiles            AzureStorage              10/24/2019 1:30:00 AM
```

## <a name="enable-backup"></a>バックアップの有効化

バックアップ ポリシーを定義したら、このポリシーを使用して、Azure ファイル共有の保護を有効にできます。

### <a name="retrieve-a-backup-policy"></a>バックアップ ポリシーを取得する

[Get-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy) を使用して、関連するポリシー オブジェクトをフェッチします。 このコマンドレットを使用して、ワークロードの種類に関連付けられているポリシーを表示したり、特定のポリシーを取得したりできます。

#### <a name="retrieve-a-policy-for-a-workload-type"></a>ワークロードの種類のポリシーを取得する

次の例では、ワークロードの種類が **AzureFiles** であるポリシーを取得します。

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureFiles"
```

次のように出力されます。

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
dailyafs             AzureFiles         AzureStorage         1/10/2018 12:30:00 AM
```

> [!NOTE]
> PowerShell の **[BackupTime]** フィールドのタイムゾーンは UTC です。 Azure portal 上でバックアップ時刻が表示されるときに、時刻はローカル タイム ゾーンに調整されます。

#### <a name="retrieve-a-specific-policy"></a>特定のポリシーを取得する

次のポリシーでは、**dailyafs** という名前のバックアップ ポリシーを取得します。

```powershell
$afsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "dailyafs"
```

### <a name="enable-protection-and-apply-the-policy"></a>保護を有効にし、ポリシーを適用する

[Enable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) を使用して、保護を有効にします。 ポリシーがコンテナーに関連付けられると、ポリシーのスケジュールに従ってバックアップがトリガーされます。

次の例では、ポリシー **dailyafs** を使用して、ストレージ アカウント **testStorageAcct** の Azure ファイル共有 **testAzureFileShare** の保護を有効にしています。

```powershell
Enable-AzRecoveryServicesBackupProtection -StorageAccountName "testStorageAcct" -Name "testAzureFS" -Policy $afsPol
```

コマンドは、保護の構成ジョブが完了するまで待機し、次の例のような出力を提供します。

```cmd
WorkloadName       Operation            Status                 StartTime                                                                                                         EndTime                   JobID
------------             ---------            ------               ---------                                  -------                   -----
testAzureFS       ConfigureBackup      Completed            11/12/2018 2:15:26 PM     11/12/2018 2:16:11 PM     ec7d4f1d-40bd-46a4-9edb-3193c41f6bf6
```

ストレージ アカウントのファイル共有の一覧を取得する方法の詳細については、[こちらの記事](/powershell/module/az.storage/get-azstorageshare)をご覧ください。

## <a name="important-notice-backup-item-identification"></a>重要な注意: バックアップ項目の識別

このセクションでは、一般公開の準備として、Azure ファイル共有のバックアップでの重要な変更について説明します。

Azure ファイル共有のバックアップを有効にするときに、ユーザーは顧客にファイル共有名をエンティティ名として与え、バックアップ項目が作成されます。 バックアップ項目の名前は、Azure Backup サービスによって作成される一意の識別子です。 通常、識別子はユーザーフレンドリ名です。 しかし、ファイル共有を削除して同じ名前で別のファイル共有を作成できるという論理的な削除のシナリオを処理するために、Azure ファイル共有の一意の ID を ID とすることになりました。

各項目の一意の ID を把握するには、**backupManagementType** および **WorkloadType** に関連するフィルターを指定して **Get-AzRecoveryServicesBackupItem** コマンドを実行することで、関連するすべての項目を取得します。 次に、返された PowerShell オブジェクトまたは応答の名前フィールドを確認します。

項目を一覧表示したうえで、応答の名前フィールドからそれらの一意の名前を取得することをお勧めします。 この値を *Name* パラメーターで使用して、項目をフィルター処理します。 または、*FriendlyName* パラメーターを使用して、項目とその ID を取得します。

> [!IMPORTANT]
> PowerShell が Azure ファイル共有のバックアップ用の最小バージョン (Az. RecoveryServices 2.6.0) にアップグレードされていることを確認します。 このバージョンでは、*FriendlyName* フィルターが **Get-AzRecoveryServicesBackupItem** コマンドを使用できます。
>
> Azure ファイル共有の名前を *FriendlyName* パラメーターに渡します。 ファイル共有の名前を *Name* パラメーターに渡した場合、このバージョンでは、*FriendlyName* パラメーターに名前を渡すよう求める警告がスローされます。
>
> 最小バージョンをインストールしないと、既存のスクリプトでエラーが発生する可能性があります。 次のコマンドを使用して、PowerShell の最小バージョンをインストールしてください。
>
>```powershell
>Install-module -Name Az.RecoveryServices -RequiredVersion 2.6.0
>```

## <a name="trigger-an-on-demand-backup"></a>オンデマンド バックアップをトリガーする

[Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem) を使用して、保護されている Azure ファイル共有のオンデマンド バックアップを実行します。

1. [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer) を使用して、バックアップ データを保持するコンテナー内のコンテナーからストレージ アカウントを取得します。
2. バックアップ ジョブを開始するには、[Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem) を使用して、Azure ファイル共有に関する情報を取得します。
3. [Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem) を使用して、オンデマンド バックアップを実行します。

次のようにしてオンデマンド バックアップを実行します。

```powershell
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType "AzureFiles" -FriendlyName "testAzureFS"
$job =  Backup-AzRecoveryServicesBackupItem -Item $afsBkpItem
```

このコマンドでは、次の例に示すように、追跡する必要がある ID を持つジョブが返されます。

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS       Backup               Completed            11/12/2018 2:42:07 PM     11/12/2018 2:42:11 PM     8bdfe3ab-9bf7-4be6-83d6-37ff1ca13ab6
```

Azure ファイル共有スナップショットは、バックアップの実行中に使用されます。 通常、ジョブは、コマンドからこの出力が返されるまでに終了します。

## <a name="next-steps"></a>次のステップ

* [Azure portal での Azure Files のバックアップ](backup-afs.md)について学習します。
* Azure Automation Runbook を使用してバックアップをスケジュールする方法については、[GitHub のサンプル スクリプト](https://github.com/Azure-Samples/Use-PowerShell-for-long-term-retention-of-Azure-Files-Backup)を参照してください。
