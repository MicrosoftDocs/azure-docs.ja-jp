---
title: 'Azure Backup: Azure Backup と PowerShell を使用して Azure VM 内の SQL Database をバックアップおよび復元する'
description: Azure Backup と PowerShell を使用して Azure VM 内の SQL Database をバックアップおよび復元します。
author: pvrk
manager: vijayts
keywords: Azure Backup; SQL;
ms.service: backup
ms.topic: conceptual
ms.date: 03/15/2019
ms.author: pullabhk
ms.assetid: 57854626-91f9-4677-b6a2-5d12b6a866e1
ms.openlocfilehash: 9d3f71eb83609d09d6e4f42b15163dbfae4fca32
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68465386"
---
# <a name="back-up-and-restore-sql-databases-in-azure--vms-with-powershell"></a>PowerShell を使用して Azure VM 内の SQL Database をバックアップおよび復元する

この記事では、[Azure Backup](backup-overview.md) Recovery Services コンテナーを使用して Azure VM 内の SQL DB をバックアップおよび復元するために Azure PowerShell を使用する方法を説明します。

このチュートリアルでは、次の方法について説明します。

> [!div class="checklist"]
> * PowerShell を設定し、Azure Recovery Services プロバイダーを登録します。
> * Recovery Services コンテナーを作成する。
> * Azure VM 内の SQL DB のバックアップを構成します。
> * バックアップ ジョブを実行します。
> * バックアップした SQL DB を復元します。
> * バックアップ ジョブと復元ジョブを監視します。

## <a name="before-you-start"></a>開始する前に

* Recovery Services コンテナーについての[詳細情報](backup-azure-recovery-services-vault-overview.md)を確認します。
* [Azure VM 内の SQL DB をバックアップ](backup-azure-sql-database.md#before-you-start)するための機能について確認します。
* Recovery Services の PowerShell オブジェクト階層を確認します。

### <a name="recovery-services-object-hierarchy"></a>Recovery Services オブジェクトの階層

オブジェクト階層の概要を次の図に示します。

![Recovery Services オブジェクトの階層](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Azure ライブラリに含まれる **Az.RecoveryServices** [コマンドレット リファレンス](/powershell/module/az.recoveryservices)を確認します。

### <a name="set-up-and-install"></a>設定とインストール

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

PowerShell を次のように設定します。

1. [最新バージョンの Az PowerShell をダウンロードします](/powershell/azure/install-az-ps)。 必要な最小バージョンは 1.5.0 です。

2. 次のコマンドを使用して、Azure Backup PowerShell コマンドレットを検索します。

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. Azure Backup および Recovery Services コンテナーのエイリアスとコマンドレットを確認します。 表示例を次に示します。 これはコマンドレットの完全な一覧ではありません。

    ![Recovery Services コマンドレットの一覧](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. **Connect-AzAccount** を使用して Azure アカウントにサインインします。
5. 表示される Web ページで、アカウントの資格情報を入力するように求められます。

    * または、 **-Credential** を使用して、**Connect-AzAccount** コマンドレットのパラメーターとしてアカウントの資格情報を含めることもできます。
    * CSP パートナーがテナントのために活動している場合は、そのテナントの tenantID またはテナントのプライマリ ドメイン名をして、その顧客をテナントとして指定します。 たとえば、「**Connect-AzAccount -Tenant** fabrikam.com」を指定します。

6. 1 つのアカウントが複数のサブスクリプションを持つことができるため、使用するサブスクリプションをアカウントに関連付けます。

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

7. Azure Backup を初めて使用する場合、**Register-AzResourceProvider** コマンドレットを使って Azure Recovery Services プロバイダーをサブスクリプションに登録します。

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

8. プロバイダーが正常に登録されたことを確認します。

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

9. コマンド出力で、**RegistrationState** が **Registered** に変わっていることを確認します。 変わっていない場合は、**Register-AzResourceProvider** コマンドレットをもう一度実行します。

## <a name="create-a-recovery-services-vault"></a>Recovery Services コンテナーを作成する

Recovery Services コンテナーを作成するには、次の手順に従います。

Recovery Services コンテナーは Resource Manager のリソースであるため、リソース グループ内に配置する必要があります。 既存のリソース グループを使用することも、**New-AzResourceGroup** コマンドレットを使ってリソース グループを作成することもできます。 リソース グループを作成するときは、リソース グループの名前と場所を指定します。

1. コンテナーはリソース グループに配置されます。 既存のリソース グループがない場合は、[New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-1.4.0) を使用して新しいリソース グループを作成します。 この例では、新しいリソース グループを米国西部リージョンに作成します。

    ```powershell
    New-AzResourceGroup -Name "test-rg" -Location "West US"
    ```

2. [New-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/New-AzRecoveryServicesVault?view=azps-1.4.0) コマンドレットを使用して、コンテナーを作成します。 リソース グループに使用したのと同じコンテナーの場所を指定します。

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

3. コンテナー ストレージに使用する冗長性の種類を指定します。

    * [ローカル冗長ストレージ](../storage/common/storage-redundancy-lrs.md)または [geo 冗長ストレージ](../storage/common/storage-redundancy-grs.md)を使用できます。
    * 次の例では、**testvault** に対する [Set-AzRecoveryServicesBackupProperty](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) コマンドの **-BackupStorageRedundancy** オプションが **GeoRedundant** に設定されています。

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

### <a name="view-the-vaults-in-a-subscription"></a>サブスクリプション内のコンテナーの表示

サブスクリプション内のコンテナーをすべて表示するには、[Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0) を使用します。

```powershell
Get-AzRecoveryServicesVault
```

次のように出力されます。 関連するリソース グループと場所が示されています。

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

* Azure Backup コマンドレットの多くは、入力として Recovery Services コンテナー オブジェクトを必要とするので、コンテナー オブジェクトを変数に格納すると便利です。
* コンテナーのコンテキストとは、コンテナーで保護されるデータの種類です。 これを、[Set-AzRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0) を使用して設定します。 コンテキストが設定されると、それが後続のすべてのコマンドレットに適用されます。

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

* バックアップ ポリシーは、少なくとも 1 つのアイテム保持ポリシーと関連付けられています。 アイテム保持ポリシーでは、復旧ポイントが削除される前に保持される期間が定義されています。
* [Get-AzRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject?view=azps-1.4.0) を使用して、既定のバックアップ ポリシー リテンション期間を表示します。
* [Get-AzRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject?view=azps-1.4.0) を使用して、既定のバックアップ ポリシー スケジュールを表示します。
* 新しいバックアップ ポリシーを作成するには、[New-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0) コマンドレットを使用します。 スケジュール ポリシー オブジェクトとアイテム保持ポリシー オブジェクトを入力します。

次の例では、スケジュール ポリシーとアイテム保持ポリシーを変数に格納します。 その後に、それらの変数を新しいポリシー (**NewSQLPolicy**) のパラメーターとして使用しています。 **NewSQLPolicy** では、毎日 "完全" バックアップを作成して 180 日間保存し、2 時間ごとにログ バックアップを作成します。

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "MSSQL"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "MSSQL"
$NewSQLPolicy = New-AzRecoveryServicesBackupProtectionPolicy -Name "NewSQLPolicy" -WorkloadType "MSSQL" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

次のように出力されます。

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                Frequency                                IsDifferentialBackup IsLogBackupEnabled
                                                                                                                                Enabled
----                 ------------       -------------------- ----------                ---------                                -------------------- ------------------
NewSQLPolicy         MSSQL              AzureWorkload        3/15/2019 9:00:00 PM      Daily                                    False                True
```

## <a name="enable-backup"></a>バックアップの有効化

### <a name="registering-the-sql-vm"></a>SQL VM を登録する

Azure VM バックアップおよび Azure File 共有では、バックアップ サービスはそれらの Azure Resource Manager リソースに接続して、関連する詳細をフェッチすることができます。 SQL は Azure VM 内のアプリケーションであるため、バックアップ サービスがアプリケーションにアクセスして必要な詳細をフェッチするにはアクセス許可が必要です。 そのためには、SQL アプリケーションを含む Azure VM を Recovery Services コンテナーに "*登録*" する必要があります。 SQL VM をコンテナーに登録すると、そのコンテナーでのみ SQL DB を保護できます。 [Register-AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/Register-AzRecoveryServicesBackupContainer?view=azps-1.5.0) PS コマンドレットを使用して VM を登録します。

````powershell
 $myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
````

このコマンドレットによって、このリソースのバックアップ コンテナーが返され、状態は登録済みになります。

> [!NOTE]
> force パラメーターを指定しないと、"Do you want to disable protection for this container" というテキストによって確認を求められます。 このテキストを無視し、確認のために "Y" を入力します。 これは既知の問題です。このテキストおよび force パラメーターの要件はいずれ削除される予定です。

### <a name="fetching-sql-dbs"></a>SQL DB をフェッチする

登録が完了すると、バックアップ サービスが VM 内の使用可能なすべての SQL コンポーネントを一覧表示できるようになります。 このコンテナーにこれからバックアップできるすべての SQL コンテナーを表示するには、[Get-AzRecoveryServicesBackupProtectableItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) PS コマンドレットを使用します。

````powershell
Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -VaultId $targetVault.ID
````

出力には、このコンテナーに登録されたすべての SQL VM 内の、保護されていないすべての SQL コンポーネントの項目の種類とサーバー名が表示されます。 -Container パラメーターを渡して特定の SQL VM に絞り込むか、Name と ServerName の組み合わせを ItemType フラグと一緒に使用して一意の SQL 項目に到達できます。

````powershell
$SQLDB = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID -Name "<Item Name>" -ServerName "<Server Name>"
````

### <a name="configuring-backup"></a>バックアップを構成する

必要な SQL DB とバックアップのために必要なポリシーが用意できたところで、[Enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) コマンドレットを使用して、この SQL DB のバックアップを構成できます。

````powershell
Enable-AzRecoveryServicesBackupProtection -ProtectableItem $SQLDB -Policy $NewSQLPolicy
````

このコマンドは、バックアップの構成が完了するまで待機してから、次の出力を返します。

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 6:00:21 PM      3/18/2019 6:01:35 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="fetching-new-sql-dbs"></a>新しい SQL DB をフェッチする

マシンを登録すると、バックアップ サービスはその時点で入手できる DB の詳細をフェッチします。 ユーザーが SQL DB/SQL インスタンスを登録済みマシンに後から追加した場合は、バックアップ サービスを手動でトリガーして新たな照会を実行し、保護されていないすべての DB (新たに追加されたものを含む) を取得する必要があります。 [Initialize-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Initialize-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) PS コマンドレットを SQL VM 上で使用して、新しい照会を実行します。 このコマンドが操作が完了するまで待機します。 その後、[Get-AzRecoveryServicesBackupProtectableItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) PS コマンドレットを使用して、保護されていない SQL コンポーネントの最新リストを取得します。

````powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
Initialize-AzRecoveryServicesBackupProtectableItem -Container $SQLContainer -WorkloadType MSSQL -VaultId $targetvault.ID
Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID
````

関連する保護対象の項目がフェッチされたら、[前のセクション](#configuring-backup)で説明したようにバックアップを有効にします。
新しい DB を手動で検出したくない場合は、[下記](#enable-autoprotection)の説明に従って自動保護を選択できます。

## <a name="enable-autoprotection"></a>自動保護を有効にする

ユーザーは、特定のポリシーを使用して、将来追加されるすべての DB を自動的に保護するようにバックアップを構成できます。 自動保護を有効にするには、[Enable-AzRecoveryServicesBackupAutoProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupAutoProtection?view=azps-1.5.0) PS コマンドレットを使用します。

この命令は将来のすべての DB をバックアップするため、処理は SQLInstance レベルで行われます。

```powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Enable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -Policy $targetPolicy -VaultId $targetvault.ID
```

自動保護を行うように指定すると、新たに追加された DB をフェッチするためのマシンの照会が、スケジュールに従って 8 時間ごとにバックグラウンド タスクとして実行されます。

## <a name="restore-sql-dbs"></a>SQL DB を復元する

Azure Backup は、Azure VM 上で実行されている SQL Server データベースを次のように復元できます。

1. トランザクション ログ バックアップを使用して、特定の日付または時刻 (秒) に復元します。 Azure Backup は、選択された時刻に基づいて復元するために必要な、適切な完全または差分バックアップおよび一連のログ バックアップを自動的に判定します。
2. 特定の復旧ポイントに復元するには、特定の完全または差分バックアップを復元します。

SQL DB を復元する前に[前提条件](restore-sql-database-azure-vm.md#prerequisites)を確認してください。

最初に、[Get-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem?view=azps-1.5.0) PS コマンドレットを使用して、該当するバックアップ済みの SQL DB をフェッチします。

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
````

### <a name="fetch-the-relevant-restore-time"></a>関連する復元時間をフェッチする

前述したように、ユーザーはバックアップ対象の SQL DB を完全/差分コピーに復元、**または**ログの特定の時点に復元できます。

#### <a name="fetch-distinct-recovery-points"></a>個別の復旧ポイントをフェッチする

[Get-AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupRecoveryPoint?view=azps-1.5.0) を使用して、バックアップ対象 SQL DB の個別の (完全/差分) 復旧ポイントをフェッチします。

````powershell
$startDate = (Get-Date).AddDays(-7).ToUniversalTime()
$endDate = (Get-Date).ToUniversalTime()
Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $targetVault.ID -StartDate $startdate -EndDate $endDate
````

出力は次の例のようになります。

````powershell
RecoveryPointId    RecoveryPointType  RecoveryPointTime      ItemName                             BackupManagemen
                                                                                                  tType
---------------    -----------------  -----------------      --------                             ---------------
6660368097802      Full               3/18/2019 8:09:35 PM   MSSQLSERVER;model             AzureWorkload
````

RecoveryPointId フィルターまたは配列フィルターを使用して、関連する復旧ポイントをフェッチします。

````powershell
$FullRP = Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $targetVault.ID -RecoveryPointId "6660368097802"
````

#### <a name="fetch-point-in-time-recovery-point"></a>特定の時点の復旧ポイントをフェッチする

特定の時点に DB を復元したい場合は、[Get-AzRecoveryServicesBackupRecoveryLogChain](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupRecoveryLogChain?view=azps-1.5.0) PS コマンドレットを使用します。 このコマンドレットは、SQL バックアップ項目の途切れずに続いているログ チェーンの開始時刻と終了時刻を表す日付の一覧を返します。 必要な時点はこの範囲内にあります。

```powershell
Get-AzRecoveryServicesBackupRecoveryLogChain -Item $bkpItem -Item -VaultId $targetVault.ID
```

出力は次の例のようになります。

````powershell
ItemName                       StartTime                      EndTime
--------                       ---------                      -------
SQLDataBase;MSSQLSERVER;azu... 3/18/2019 8:09:35 PM           3/19/2019 12:08:32 PM
````

上記の出力は、表示されている開始時刻と終了時刻の間の任意の時点にユーザーが復元できることを意味します。 時刻は UTC 形式です。 上記の範囲内の任意の時点を PS に設定します。

> [!NOTE]
> 復元のためにログの特定の時点を選択した場合は、開始地点 (つまり DB の復元に使用する完全バックアップ) を指定する必要はありません。 Azure Backup サービスによって、どの完全バックアップを選択するか、どのログ バックアップを適用するかなど、復旧プラン全体が処理されます。

### <a name="determine-recovery-configuration"></a>復旧構成を決定する

SQL DB の復元では、次の復元シナリオがサポートされます。

1. バックアップ対象の SQL DB を別の復旧ポイントのデータでオーバーライドする - OriginalWorkloadRestore
2. 同じ SQL インスタンス内の新規 DB として SQL DB を復旧する - AlternateWorkloadRestore
3. 別の SQL VM 内の別の SQL インスタンスの新規 DB として SQL DB を復旧する - AlternateWorkloadRestore

関連する復旧ポイント (個別の復旧ポイントまたは特定の時点) をフェッチしたら、[Get-AzRecoveryServicesBackupWorkloadRecoveryConfig](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupWorkloadRecoveryConfig?view=azps-1.5.0) PS コマンドレットを使用し、必要な復旧プランに従って復旧構成オブジェクトをフェッチします。

#### <a name="original-workload-restore"></a>元のワークロードの復元

バックアップ対象の DB を復旧ポイントのデータでオーバーライドするには、次の例のように適切なフラグと該当する復旧ポイントを指定するだけです。

##### <a name="original-restore-with-distinct-recovery-point"></a>個別の復旧ポイントを使用した元への復元

````powershell
$OverwriteWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -OriginalWorkloadRestore -VaultId $targetVault.ID
````

##### <a name="original-restore-with-log-point-in-time"></a>ログの特定の時点を使用した元への復元

```powershell
$OverwriteWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem  -OriginalWorkloadRestore -VaultId $targetVault.ID
```

#### <a name="alternate-workload-restore"></a>代替のワークロードの復元

> [!IMPORTANT]
> バックアップ対象の SQL DB を新しい DB として別の SQLInstance に復元できるのは、このコンテナーに登録されている Azure VM のみです。

前述のように、復元先の SQLInstance が別の Azure VM にある場合は、[このコンテナーに登録](#registering-the-sql-vm)されており、該当の SQLInstance が保護可能項目として表示されることを確認します。

````powershell
$TargetInstance = Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -ItemType SQLInstance -Name "<SQLInstance Name>" -ServerName "<SQL VM name>" -VaultId $targetVault.ID
````

その場合は、次に示すように、該当する復旧ポイント、復元先の SQL インスタンスを適切なフラグを使用して渡すだけです。

##### <a name="alternate-restore-with-distinct-recovery-point"></a>個別の復旧ポイントを使用した代替への復元

````powershell
$AnotherInstanceWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -TargetItem $TargetInstance -AlternateWorkloadRestore -VaultId $targetVault.ID
````

##### <a name="alternate-restore-with-log-point-in-time"></a>ログの特定の時点を使用した代替への復元

```powershell
$AnotherInstanceWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem -AlternateWorkloadRestore -VaultId $targetVault.ID
```

[Get-AzRecoveryServicesBackupWorkloadRecoveryConfig](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupWorkloadRecoveryConfig?view=azps-1.5.0) PS コマンドレットから取得した最終復旧ポイントの構成オブジェクトには、次に示すように復元に関連するすべての情報が含まれます。

````powershell
TargetServer         : <SQL server name>
TargetInstance       : <Target Instance name>
RestoredDBName       : <Target Instance name>/azurebackup1_restored_3_19_2019_1850
OverwriteWLIfpresent : No
NoRecoveryMode       : Disabled
targetPhysicalPath   : {azurebackup1, azurebackup1_log}
ContainerId          : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/vmappcontainer;compute;computeRG;SQLVMName
SourceResourceId     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/computeRG/VMAppContainer/SQLVMName
RestoreRequestType   : Alternate WL Restore
RecoveryPoint        : Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureWorkloadRecoveryPoint
PointInTime          : 1/1/0001 12:00:00 AM
````

復元される DB 名、OverwriteWLIfpresent、NoRecoveryMode、targetPhysicalPath の各フィールドを編集できます。 次に示すようにターゲット ファイル パスの詳細を取得します。

````powershell
$AnotherInstanceWithFullConfig.targetPhysicalPath

MappingType SourceLogicalName SourcePath                  TargetPath
----------- ----------------- ----------                  ----------
Data        azurebackup1      F:\Data\azurebackup1.mdf    F:\Data\azurebackup1_1553001753.mdf
Log         azurebackup1_log  F:\Log\azurebackup1_log.ldf F:\Log\azurebackup1_log_1553001753.ldf
````

次に示すように、文字列値として関連する PS プロパティを設定します。

````powershell
$AnotherInstanceWithFullConfig.OverwriteWLIfpresent = "Yes"
$AnotherInstanceWithFullConfig | fl

TargetServer         : <SQL server name>
TargetInstance       : <Target Instance name>
RestoredDBName       : <Target Instance name>/azurebackup1_restored_3_19_2019_1850
OverwriteWLIfpresent : Yes
NoRecoveryMode       : Disabled
targetPhysicalPath   : {azurebackup1, azurebackup1_log}
ContainerId          : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/vmappcontainer;compute;computeRG;SQLVMName
SourceResourceId     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/computeRG/VMAppContainer/SQLVMName
RestoreRequestType   : Alternate WL Restore
RecoveryPoint        : Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureWorkloadRecoveryPoint
PointInTime          : 1/1/0001 12:00:00 AM
````

> [!IMPORTANT]
> 復元操作は最終復旧構成オブジェクトに基づいて行われるため、この構成オブジェクトに必要な値と適切な値がすべて含まれるようにしてください。

### <a name="restore-with-relevant-configuration"></a>関連する構成を使用して復元する

関連する復旧構成オブジェクトを取得して確認したら、[Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Restore-AzRecoveryServicesBackupItem?view=azps-1.5.0) PS コマンドレットを使用して復元プロセスを開始します。

````powershell
Restore-AzRecoveryServicesBackupItem -WLRecoveryConfig $AnotherInstanceWithLogConfig -VaultId $targetVault.ID
````

復元操作によってジョブが返されて追跡できます。

````powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Restore              InProgress           3/17/2019 10:02:45 AM                                3274xg2b-e4fg-5952-89b4-8cb566gc1748
````

## <a name="manage-sql-backups"></a>SQL バックアップを管理する

### <a name="on-demand-backup"></a>オンデマンド バックアップ

DB のバックアップをいったん有効にすると、ユーザーは [Backup-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Backup-AzRecoveryServicesBackupItem?view=azps-1.5.0) PS コマンドレットを使用して DB のオンデマンド バックアップをトリガーすることもできます。 次の例では、圧縮を有効にして SQL DB に対する完全バックアップをトリガーします。完全バックアップは 60 日間保持する必要があります。

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
$endDate = (Get-Date).AddDays(60).ToUniversalTime()
Backup-AzRecoveryServicesBackupItem -Item $bkpItem -BackupType Full -EnableCompression -VaultId $targetVault.ID -ExpiryDateTimeUTC $endDate
````

このアドホック バックアップ コマンドによってジョブが返されて追跡できます。

````powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Backup               InProgress           3/18/2019 8:41:27 PM                                2516bb1a-d3ef-4841-97a3-9ba455fb0637
````

出力がなくなった場合、または関連するジョブ ID を取得したい場合は、Azure Backup サービスから[ジョブの一覧を取得](#track-azure-backup-jobs)して、そのジョブおよび詳細を追跡します。

### <a name="change-policy-for-backup-items"></a>バックアップ項目のポリシーを変更する

ユーザーは既存のポリシーを変更したり、バックアップ対象項目のポリシーを Policy1 から Policy2 に変えたりすることができます。 バックアップ対象項目のポリシーを切り替えるには、該当するポリシーとバックアップ項目をフェッチして、バックアップ項目をパラメーターに指定して [Enable-AzRecoveryServices](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) コマンドを使用します。

````powershell
$TargetPol1 = Get-AzRecoveryServicesBackupProtectionPolicy -Name <PolicyName>
$anotherBkpItem = Get-AzRecoveryServicesBackupItem -WorkloadType MSSQL -BackupManagementType AzureWorkload -Name "<BackupItemName>"
Enable-AzRecoveryServicesBackupProtection -Item $anotherBkpItem -Policy $TargetPol1
````

このコマンドは、バックアップの構成が完了するまで待機してから、次の出力を返します。

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 8:00:21 PM      3/18/2019 8:02:16 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="re-register-sql-vms"></a>SQL VM の再登録

> [!WARNING]
> 再登録を試行する前に、必ずこちらの[ドキュメント](backup-sql-server-azure-troubleshoot.md#re-registration-failures)を確認して、エラーの現象と原因を把握してください。

SQL VM の再登録をトリガーするには、関連するバックアップ コンテナーをフェッチして、登録のコマンドレットに渡します。

````powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
Register-AzRecoveryServicesBackupContainer -Container $SQLContainer -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID
````

### <a name="stop-protection"></a>保護の停止

#### <a name="retain-data"></a>データの保持

ユーザーが保護を停止しようとするとき、[Disable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) PS コマンドレットを使用できます。 これによって、スケジュールされているバックアップは停止されますが、それまでにバックアップされたデータは永続的に保持されます。

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID
````

#### <a name="delete-backup-data"></a>バックアップ データの削除

コンテナーに格納されたバックアップ データを完全に削除するには、[保護を無効にするコマンド](#retain-data)に -RemoveRecoveryPoints フラグ/スイッチを付けるだけです。

````powershell
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID -RemoveRecoveryPoints
````

#### <a name="disable-auto-protection"></a>自動保護の無効化

自動保護が SQLInstance で構成されていた場合は、[Disable-AzRecoveryServicesBackupAutoProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupAutoProtection?view=azps-1.5.0) PS コマンドレットを使用して無効にすることができます。

````powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Disable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetvault.ID
````

#### <a name="unregister-sql-vm"></a>SQL VM の登録解除

SQL サーバーのすべての DB が[保護されなくなり、バックアップ データが存在しなくなったら](#delete-backup-data)、ユーザーはその SQL VM をこのコンテナーから登録解除できます。 その後でないと、別のコンテナーで DB を保護することはできません。 [Unregister-AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/Unregister-AzRecoveryServicesBackupContainer?view=azps-1.5.0) PS コマンドレットを使用して SQL VM を登録解除します。

````powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
 Unregister-AzRecoveryServicesBackupContainer -Container $SQLContainer -VaultId $targetvault.ID
````

### <a name="track-azure-backup-jobs"></a>Azure Backup ジョブを追跡する

Azure Backup はユーザーがトリガーしたジョブしか SQL バックアップで追跡できないことに注意してください。 スケジュールされたバックアップ (ログ バックアップを含む) はポータル/powershell には表示されません。 ただし、スケジュールされたジョブが失敗した場合は、[バックアップ アラート](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault)が生成されてポータルに表示されます。 スケジュールされたすべてのジョブとその他の関連情報を追跡するには、[Azure Monitor を使用](backup-azure-monitoring-use-azuremonitor.md)します。

ユーザーは、バックアップなど非同期ジョブの[出力](#on-demand-backup)に返される JobID を使用して、アドホック操作やユーザーがトリガーした操作を追跡できます。 [Get-AzRecoveryServicesBackupJobDetail](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupJobDetail) PS コマンドレットを使用して、ジョブとその詳細を追跡します。

````powershell
 Get-AzRecoveryServicesBackupJobDetails -JobId 2516bb1a-d3ef-4841-97a3-9ba455fb0637 -VaultId $targetVault.ID
````

アドホック ジョブとその状態の一覧を Azure Backup サービスから取得するには、[Get-AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupJob?view=azps-1.5.0) PS コマンドレットを使用します。 次の例は、進行中のすべての SQL ジョブを返します。

```powershell
Get-AzRecoveryServicesBackupJob -Status InProgress -BackupManagementType AzureWorkload
```

進行中のジョブをキャンセルするには、[Stop-AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/Stop-AzRecoveryServicesBackupJob?view=azps-1.5.0) PS コマンドレットを使用します。

## <a name="managing-sql-always-on-availability-groups"></a>SQL Always On 可用性グループを管理する

SQL Always On 可用性グループでは、必ず可用性グループ (AG) の[すべてのノードを登録](#registering-the-sql-vm)してください。 すべてのノードの登録が完了すると、SQL 可用性グループ オブジェクトが保護可能項目の下に論理的に作成されます。 SQL AG の下のデータベースは SQLDatabase として表示されます。 ノードはスタンドアロン インスタンスとして表示され、それらの下にある既定の SQL データベース SQL データベースとして一覧表示されます。

たとえば、SQL AG に sql-server-0 と sql-server-1 という 2 つのノードと、1 つの SQL AG DB があるとします。 両方のノードが登録された後で、ユーザーが[保護可能な項目を一覧表示](#fetching-sql-dbs)すると、次のコンポーネントが表示されます。

1. SQL AG オブジェクト - 保護可能な項目の種類: SQLAvailabilityGroup
2. SQL AG DB - 保護可能な項目の種類: SQLDatabase
3. sql-server-0 - 保護可能な項目の種類: SQLInstance
4. sql-server-1 - 保護可能な項目の種類: SQLInstance
5. sql-server-0 内のすべての既定 SQL DB (master、model、msdb) - 保護可能な項目の種類: SQLDatabase
6. sql-server-1 内のすべての既定 SQL DB (master、model、msdb) - 保護可能な項目の種類: SQLDatabase

sql-server-0 と sql-server-1 は、[バックアップ コンテナーが一覧表示される](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupContainer?view=azps-1.5.0)ときは "AzureVMAppContainer" としても一覧表示されます。

該当する SQL データベースをフェッチして[バックアップを有効化](#configuring-backup)してください。[アドホック バックアップ](#on-demand-backup)と[復元の PS コマンドレット](#restore-sql-dbs)は同じです。
