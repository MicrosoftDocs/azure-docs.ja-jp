---
title: "Resource Manager でデプロイされた VM のバックアップを PowerShell を使用してデプロイおよび管理する | Microsoft Docs"
description: "PowerShell を使用して Azure で Resource Manager によりデプロイされた VM のバックアップをデプロイおよび管理する"
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
ms.assetid: 68606e4f-536d-4eac-9f80-8a198ea94d52
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 02/09/2017
ms.author: markgal;trinadhk
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 82b7541ab1434179353247ffc50546812346bda9
ms.openlocfilehash: 754ad53c46fd6bc00be0282138480e73d560fdc6
ms.lasthandoff: 03/02/2017


---
# <a name="use-azurermrecoveryservicesbackup-cmdlets-to-back-up-virtual-machines"></a>AzureRM.RecoveryServices.Backup コマンドレットを使って仮想マシンをバックアップする
> [!div class="op_single_selector"]
> * [リソース マネージャー](backup-azure-vms-automation.md)
> * [クラシック](backup-azure-vms-classic-automation.md)
>
>

この記事では、Azure PowerShell コマンドレットを使用して Recovery Services コンテナーに Azure 仮想マシン (VM) をバックアップする方法と Recovery Services コンテナーから Azure 仮想マシンを回復する方法について説明します。 Recovery Services コンテナーは、Azure Resource Manager のリソースであり、Azure Backup サービスと Azure Site Recovery サービスの両方でデータと資産を保護するために使用されます。 Recovery Services コンテナーを使用すると、Azure Resource Manager でデプロイされた VM と同様に、Azure Service Manager でデプロイされた VM も保護できます。

> [!NOTE]
> Azure には、リソースの作成と操作に関して&2; 種類のデプロイ モデルがあります。[Resource Manager デプロイ モデルとクラシック デプロイ モデル](../azure-resource-manager/resource-manager-deployment-model.md)です。 この記事では、Resource Manager モデルで作成された VM を対象とします。
>
>

PowerShell を使用した VM の保護および復旧ポイントからデータの復元について説明します。

## <a name="concepts"></a>概念
Azure Backup サービスに関する知識が十分でない場合は、「[Azure Backup とは](backup-introduction-to-azure-backup.md)」を参照してください。 開始する前に、Azure Backup を使用するのに必要な前提条件が満たされていることを確認し、現在の VM バックアップ ソリューションの制限事項を把握してください。

PowerShell を効果的に使用するには、オブジェクトの階層および開始地点を理解しておく必要があります。

![Recovery Services オブジェクトの階層](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

AzureRmRecoveryServicesBackup PowerShell コマンドレット リファレンスを確認するには、Azure ライブラリの「 [Azure Backup - Recovery Services Cmdlets (Azure Backup - Recovery Services コマンドレット)](https://msdn.microsoft.com/library/mt723320.aspx) 」を参照してください。
AzureRmRecoveryServicesVault PowerShell コマンドレット リファレンスを確認するには、「 [Azure Recovery Service Cmdlets (Azure Recovery Services コマンドレット)](https://msdn.microsoft.com/library/mt643905.aspx)」を参照してください。

## <a name="setup-and-registration"></a>セットアップと登録
開始するには

1. [最新バージョンの PowerShell をダウンロードします](https://github.com/Azure/azure-powershell/releases) (バージョン 1.4.0 以降が必要)。
2. 以下のコマンドを入力して、使用可能な Azure Backup の PowerShell コマンドレットを検索します。

```
PS C:\> Get-Command *azurermrecoveryservices*

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Backup-AzureRmRecoveryServicesBackupItem           1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Disable-AzureRmRecoveryServicesBackupProtection    1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Enable-AzureRmRecoveryServicesBackupProtection     1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupContainer         1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupItem              1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupJob               1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupJobDetails        1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupManagementServer  1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupProperties        1.4.0      AzureRM.RecoveryServices
Cmdlet          Get-AzureRmRecoveryServicesBackupProtectionPolicy  1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRMRecoveryServicesBackupRecoveryPoint     1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupRetentionPolic... 1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupSchedulePolicy... 1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesVault                   1.4.0      AzureRM.RecoveryServices
Cmdlet          Get-AzureRmRecoveryServicesVaultSettingsFile       1.4.0      AzureRM.RecoveryServices
Cmdlet          New-AzureRmRecoveryServicesBackupProtectionPolicy  1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          New-AzureRmRecoveryServicesVault                   1.4.0      AzureRM.RecoveryServices
Cmdlet          Remove-AzureRmRecoveryServicesProtectionPolicy     1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Remove-AzureRmRecoveryServicesVault                1.4.0      AzureRM.RecoveryServices
Cmdlet          Restore-AzureRMRecoveryServicesBackupItem          1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Set-AzureRmRecoveryServicesBackupProperties        1.4.0      AzureRM.RecoveryServices
Cmdlet          Set-AzureRmRecoveryServicesBackupProtectionPolicy  1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Set-AzureRmRecoveryServicesVaultContext            1.4.0      AzureRM.RecoveryServices
Cmdlet          Stop-AzureRmRecoveryServicesBackupJob              1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Unregister-AzureRmRecoveryServicesBackupContainer  1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Unregister-AzureRmRecoveryServicesBackupManagem... 1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Wait-AzureRmRecoveryServicesBackupJob              1.4.0      AzureRM.RecoveryServices.Backup
```


次のタスクは PowerShell を使用して自動化することができます。

* Recovery Services コンテナーを作成する
* Azure VM をバックアップまたは保護する
* バックアップ ジョブをトリガーする
* バックアップ ジョブを監視する
* Azure VM の復元

## <a name="create-a-recovery-services-vault"></a>Recovery Services コンテナーを作成する
次の手順では、Recovery Services コンテナーの作成について説明します。 Recovery Services コンテナーは Backup コンテナーとは異なります。

1. Azure Backup を初めて使用する場合、**[Register-AzureRMResourceProvider](https://msdn.microsoft.com/library/mt679020.aspx)** コマンドレットを使って Azure Recovery Services プロバイダーをサブスクリプションに登録する必要があります。

    ```
    PS C:\> Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```
2. Recovery Services コンテナーは Resource Manager リソースであるため、リソース グループ内に配置する必要があります。 既存のリソース グループを使用することも、**[New-AzureRmResourceGroup](https://msdn.microsoft.com/library/mt678985.aspx)** コマンドレットを使って新しいリソース グループを作成することもできます。 新しいリソース グループを作成する場合、リソース グループの名前と場所を指定します。  

    ```
    PS C:\> New-AzureRmResourceGroup –Name "test-rg" –Location "West US"
    ```
3. **[New-AzureRmRecoveryServicesVault](https://msdn.microsoft.com/library/mt643910.aspx)** コマンドレットを使用して新しいコンテナーを作成します。 リソース グループに使用したのと同じコンテナーの場所を指定してください。

    ```
    PS C:\> New-AzureRmRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "West US"
    ```
4. 使用するストレージ冗長性の種類を指定します。[ローカル冗長ストレージ (LRS)](../storage/storage-redundancy.md#locally-redundant-storage) または [geo 冗長ストレージ (GRS)](../storage/storage-redundancy.md#geo-redundant-storage) を使用できます。 次に示す例では、testVault の -BackupStorageRedundancy オプションが GeoRedundant に設定されています。

    ```
    PS C:\> $vault1 = Get-AzureRmRecoveryServicesVault –Name "testVault"
    PS C:\> Set-AzureRmRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

   > [!TIP]
   > Azure Backup コマンドレットの多くは、入力として Recovery Services コンテナー オブジェクトを必要としています。 このため、Backup Recovery Services コンテナー オブジェクトを変数に格納すると便利です。
   >
   >

## <a name="view-the-vaults-in-a-subscription"></a>サブスクリプション内のコンテナーの表示
**[Get-AzureRmRecoveryServicesVault](https://msdn.microsoft.com/library/mt643907.aspx)** を使用して、現在のサブスクリプション内のすべてのコンテナーを一覧表示します。 このコマンドは、新しく作成したコンテナーを確認したり、サブスクリプション内の利用可能なコンテナーを確認したりするのに使用できます。

Get-AzureRmRecoveryServicesVault コマンドを実行すると、サブスクリプション内のすべてのコンテナーが一覧表示されます。

```
PS C:\> Get-AzureRmRecoveryServicesVault
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```


## <a name="backup-azure-vms"></a>Azure VM のバックアップ
これで Recovery Services コンテナーが作成されました。このコンテナーを使用して仮想マシンを保護できます。 ただし、保護を行う前に、コンテナーのコンテキストを設定し、保護ポリシーを確認する必要があります。 コンテナーのコンテキストは、コンテナーで保護されるデータの種類を定義するものです。 保護ポリシーは、バックアップ ジョブが実行される時間と各バックアップ スナップショットの保持期間を設定するためのスケジュールです。

VM に対する保護を有効にする前に、資格情報コンテナーのコンテキストを設定する必要があります。 設定したコンテキストは、この後のすべてのコマンドレットに適用されます。

```
PS C:\> Get-AzureRmRecoveryServicesVault -Name testvault | Set-AzureRmRecoveryServicesVaultContext
```

### <a name="create-a-protection-policy"></a>保護ポリシーの作成
新しい資格情報コンテナーを作成すると、これには既定のポリシーが付属します。 このポリシーは、各日の指定した時間にバックアップ ジョブをトリガーします。 既定のポリシーでは、バックアップ スナップショットは 30 日間保持されます。 既定のポリシーを使用すると、VM を迅速に保護することができ、後で異なる詳細な内容にポリシーを編集することもできます。

**[Get-AzureRmRecoveryServicesBackupProtectionPolicy](https://msdn.microsoft.com/library/mt723300.aspx)** を使用して、コンテナーの使用可能なポリシーの一覧を表示します。

```
PS C:\> Get-AzureRmRecoveryServicesBackupProtectionPolicy -WorkloadType AzureVM
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
DefaultPolicy        AzureVM            AzureVM              4/14/2016 5:00:00 PM
```

> [!NOTE]
> PowerShell の BackupTime フィールドのタイムゾーンは UTC です。 ただし、Azure ポータルにバックアップ時刻が表示されるとき、時刻はローカル タイムゾーンに調整されます。
>
>

バックアップ保護ポリシーは、少なくとも&1; つのアイテム保持ポリシーと関連付けられます。  アイテム保持ポリシーには、Azure Backup で復旧ポイントを保持する期間が定義されています。 既定のアイテム保持ポリシーを表示するには、 **Get-AzureRmRecoveryServicesBackupRetentionPolicyObject** を使用します。  同様に **Get-AzureRmRecoveryServicesBackupSchedulePolicyObject** を使用して、既定のスケジュール ポリシーを取得できます。 スケジュール ポリシーとアイテム保持ポリシー オブジェクトは、 **New-AzureRmRecoveryServicesBackupProtectionPolicy** コマンドレットの入力として使用されます。

バックアップ保護ポリシーには、アイテムのバックアップが実行されるタイミングと方法を定義します。 New-AzureRmRecoveryServicesBackupProtectionPolicy コマンドレットは、バックアップ ポリシー情報を保持する PowerShell オブジェクトを作成します。 バックアップ ポリシーは、Enable-AzureRmRecoveryServicesBackupProtection コマンドレットへの入力として使用されます。

```
PS C:\> $schPol = Get-AzureRmRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
PS C:\>  $retPol = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
PS C:\>  New-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -WorkloadType AzureVM -RetentionPolicy $retPol -SchedulePolicy $schPol
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewPolicy           AzureVM            AzureVM              4/24/2016 1:30:00 AM
```

### <a name="enable-protection"></a>保護を有効にする
保護を有効にするには、アイテムとポリシーの&2; つのオブジェクトが必要です。 資格情報コンテナーでの保護を有効にするには両方のオブジェクトが必要です。 ポリシーがコンテナーに関連付けられると、ポリシーのスケジュールで定義された時刻にバックアップのワークフローが開始されます。

暗号化されていない Resource Manager VM で保護を有効にする場合

```
PS C:\> $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

暗号化された VM (BEK と KEK を使用して暗号化) で保護を有効にするには、Azure Backup サービスに許可を付与し、キーとシークレットをキー コンテナーから読み取る必要があります。

```
PS C:\> Set-AzureRmKeyVaultAccessPolicy -VaultName 'KeyVaultName' -ResourceGroupName 'RGNameOfKeyVault' -PermissionsToKeys backup,get,list -PermissionsToSecrets get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
PS C:\> $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

> [!NOTE]
> Azure Government クラウドの場合は、Set-AzureRmKeyVaultAccessPolicy コマンドレットの **-ServicePrincipalName** パラメーターで、値 ff281ffe-705c-4f53-9f37-a40e6f2c68f3 を使用します。
>
>

クラシック VM の場合

```
PS C:\>  $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\>  Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V1VM" -ServiceName "ServiceName1"
```

### <a name="modify-a-protection-policy"></a>保護ポリシーの変更
ポリシーを変更する場合は、BackupSchedulePolicyObject または BackupRetentionPolicy オブジェクトを変更し、Set-AzureRmRecoveryServicesBackupProtectionPolicy を使用してポリシーを変更します。

次の例では、保持期間を 365 に変更します。

```
PS C:\> $retPol = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
PS C:\> $retPol.DailySchedule.DurationCountInDays = 365
PS C:\> $pol= Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name NewPolicy
PS C:\> Set-AzureRmRecoveryServicesBackupProtectionPolicy -Policy $pol  -RetentionPolicy  $RetPol
```

## <a name="run-an-initial-backup"></a>初回バックアップの実行
バックアップのスケジュールでは、アイテムの初回のバックアップ時に完全なバックアップをトリガーします。 以後のバックアップでは、バックアップは増分コピーとなります。 初回バックアップを強制的に特定の時刻に行うか、すぐに行う場合は、**[Backup-AzureRmRecoveryServicesBackupItem](https://msdn.microsoft.com/library/mt723312.aspx)** コマンドレットを使用します。

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -FriendlyName 'V2VM'
PS C:\> $item = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM"
PS C:\> $job = Backup-AzureRmRecoveryServicesBackupItem -Item $item
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM              Backup               InProgress            4/23/2016 5:00:30 PM                       cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

> [!NOTE]
> PowerShell での StartTime フィールドと EndTime フィールドのタイムゾーンは UTC です。 ただし、Azure ポータルに時刻が表示されるとき、時刻はローカル タイムゾーンに調整されます。
>
>

## <a name="monitoring-a-backup-job"></a>バックアップ ジョブの監視
Azure Backup で長時間実行される多くの操作は、ジョブとしてモデル化されています。 これにより、Azure ポータルを常に開いていなくても進捗を簡単に追跡できるようになります。

進行中のジョブの最新の状態を取得するには、Get-AzureRmRecoveryservicesBackupJob コマンドレットを使用します。

```
PS C:\ > $joblist = Get-AzureRmRecoveryservicesBackupJob –Status InProgress
PS C:\ > $joblist[0]
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM             Backup               InProgress            4/23/2016 5:00:30 PM           cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

完了確認のためにこれらのジョブをポーリングすると、不要な追加コードが発生します。そのため、代わりに **[Wait-AzureRmRecoveryServicesBackupJob](https://msdn.microsoft.com/library/mt723321.aspx)** コマンドレットを使用します。 このコマンドレットは、ジョブが完了するまで、または指定したタイムアウト値に到達するまで、実行を一時停止します。

```
PS C:\> Wait-AzureRmRecoveryServicesBackupJob -Job $joblist[0] -Timeout 43200
```

## <a name="restore-an-azure-vm"></a>Azure VM の復元
VM の復元に Azure ポータルを使用した場合と PowerShell を使用した場合の間には決定的な違いがあります。 PowerShell の場合、復旧ポイントからディスクと構成情報が作成されたら、復元操作は完了します。 復元操作で仮想マシンは作成されません。 ディスクから仮想マシンを作成する手順が示されます。 しかし、VM を完全に復元するには、次の手順に従って作業する必要があります。

* VM の選択
* 復元ポイントの選択
* ディスクの復元
* 保存されたディスクからの VM の作成

次の図には、RecoveryServicesVault から BackupRecoveryPoint までのオブジェクト階層が示されています。

![Recovery Services object hierarchy showing BackupContainer](./media/backup-azure-vms-arm-automation/backuprecoverypoint-only.png)

バックアップ データを復元するには、バックアップ項目と、復元する特定の時点のデータを保持する復旧ポイントを特定します。 その後、**[Restore-AzureRmRecoveryServicesBackupItem](https://msdn.microsoft.com/library/mt723316.aspx)** コマンドレットを使用して、コンテナーからお客様のアカウントにデータを復元します。

### <a name="select-the-vm"></a>VM の選択
バックアップする正しいアイテムを特定する PowerShell オブジェクトを取得するには、資格情報コンテナー内のコンテナーから開始し、オブジェクト階層の上から下に進みます。 VM を表すコンテナーを選択するには、**[Get-AzureRmRecoveryServicesBackupContainer](https://msdn.microsoft.com/library/mt723319.aspx)** コマンドレットを使用し、**[Get-AzureRmRecoveryServicesBackupItem](https://msdn.microsoft.com/library/mt723305.aspx)** コマンドレットへのパイプとして使用します。

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer  -ContainerType AzureVM –Status Registered -FriendlyName 'V2VM'
PS C:\> $backupitem = Get-AzureRmRecoveryServicesBackupItem –Container $namedContainer  –WorkloadType "AzureVM"
```

### <a name="choose-a-recovery-point"></a>復元ポイントの選択
**[Get-AzureRmRecoveryServicesBackupRecoveryPoint](https://msdn.microsoft.com/library/mt723308.aspx)** コマンドレットを使用して、バックアップ項目のすべての復旧ポイントを一覧表示します。 その後、復元に使用する復旧ポイントを選択します。 使用する復旧ポイントがわからない場合、一覧にある最新の RecoveryPointType = AppConsistent ポイントを選択することをお勧めします。

次のスクリプトでは、**$rp** 変数が、選択したバックアップ項目の復旧ポイントの配列になっています。 この配列は時間の逆順で並べ替えられています。最新の復旧ポイントのインデックスは 0 です。 標準の PowerShell 配列のインデックスを使用して、復旧ポイントを選択します。 例: $rp[0] は、最新の復旧ポイントを選択します。

```
PS C:\> $startDate = (Get-Date).AddDays(-7)
PS C:\> $endDate = Get-Date
PS C:\> $rp = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
PS C:\> $rp[0]
RecoveryPointAdditionalInfo :
SourceVMStorageType         : NormalStorage
Name                        : 15260861925810
ItemName                    : VM;iaasvmcontainer;RGName1;V2VM
RecoveryPointId             : /subscriptions/XX/resourceGroups/ RGName1/providers/Microsoft.RecoveryServices/vaults/testvault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainer;RGName1;V2VM/protectedItems/VM;iaasvmcontainer; RGName1;V2VM
                              /recoveryPoints/15260861925810
RecoveryPointType           : AppConsistent
RecoveryPointTime           : 4/23/2016 5:02:04 PM
WorkloadType                : AzureVM
ContainerName               : IaasVMContainer;iaasvmcontainer; RGName1;V2VM
ContainerType               : AzureVM
BackupManagementType        : AzureVM
```



### <a name="restore-the-disks"></a>ディスクの復元
**[Restore-AzureRmRecoveryServicesBackupItem](https://msdn.microsoft.com/library/mt723316.aspx)** コマンドレットを使用して、復旧ポイントまでバックアップ項目のデータと構成を復元します。 復旧ポイントの特定が終わったら、その復旧ポイントを **-RecoveryPoint** パラメーターの値として使用します。 前のコード例では、使用する復旧ポイントとして **$rp[0]** が選択されていました。 次のコード例では、 **$rp[0]** は、ディスクの復元に使用する復旧ポイントとして指定されています。

ディスクと構成情報を復元するには

```
PS C:\> $restorejob = Restore-AzureRmRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName DestAccount -StorageAccountResourceGroupName DestRG
PS C:\> $restorejob
WorkloadName     Operation          Status               StartTime                 EndTime            JobID
------------     ---------          ------               ---------                 -------          ----------
V2VM              Restore           InProgress           4/23/2016 5:00:30 PM                        cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

**[Wait-AzureRmRecoveryServicesBackupJob](https://msdn.microsoft.com/library/mt723321.aspx)** を使用して、復元ジョブが完了するまで待機することができます。

```
PS C:\> Wait-AzureRmRecoveryServicesBackupJob -Job $restorejob -Timeout 43200
```

復元ジョブが完了したら、**[Get-AzureRmRecoveryServicesBackupJobDetails](https://msdn.microsoft.com/library/mt723310.aspx)** コマンドレットを使用して復元操作の詳細を取得します。 JobDetails プロパティには、VM を再構築するために必要な情報が含まれています。

```
PS C:\> $restorejob = Get-AzureRmRecoveryServicesBackupJob -Job $restorejob
PS C:\> $details = Get-AzureRmRecoveryServicesBackupJobDetails -Job $restorejob
```

ディスクの復元後、VM の作成について説明した次のセクションに移動します。

## <a name="create-a-vm-from-restored-disks"></a>復元されたディスクからの VM の作成
ディスクの復元が完了したら、次の手順を使用してディスクから仮想マシンを作成し、構成します。

> [!NOTE]
> 復元されたディスクを使用して暗号化された VM を作成する場合は、ロールに **Microsoft.KeyVault/vaults/deploy/action** の実行が許可されている必要があります。 ロールにこのアクセス許可がない場合は、この操作でカスタム ロールを作成します。 詳細については、「[Azure RBAC のカスタム ロール](../active-directory/role-based-access-control-custom-roles.md)を参照してください。
>
>

1. 復元されたディスクのプロパティに対し、ジョブの詳細を照会します。

    ```
    PS C:\> $properties = $details.properties
    PS C:\> $storageAccountName = $properties["Target Storage Account Name"]
    PS C:\> $containerName = $properties["Config Blob Container Name"]
    PS C:\> $blobName = $properties["Config Blob Name"]
    ```
2. Azure Storage コンテキストを設定し、JSON 構成ファイルを復元します。

    ```
    PS C:\> Set-AzureRmCurrentStorageAccount -Name $storageaccountname -ResourceGroupName testvault
    PS C:\> $destination_path = "C:\vmconfig.json"
    PS C:\> Get-AzureStorageBlobContent -Container $containerName -Blob $blobName -Destination $destination_path
    PS C:\> $obj = ((Get-Content -Path $destination_path -Raw -Encoding Unicode)).TrimEnd([char]0x00) | ConvertFrom-Json
    ```
3. JSON 構成ファイルを使用して VM 構成を作成します。

    ```
   PS C:\> $vm = New-AzureRmVMConfig -VMSize $obj.HardwareProfile.VirtualMachineSize -VMName "testrestore"
    ```
4. OS ディスクとデータ ディスクを接続します。

      暗号化されていない VM の場合

      ```
      PS C:\> Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.StorageProfile.OSDisk.VirtualHardDisk.Uri -CreateOption “Attach”
      PS C:\> $vm.StorageProfile.OsDisk.OsType = $obj.StorageProfile.OSDisk.OperatingSystemType
      PS C:\> foreach($dd in $obj.StorageProfile.DataDisks)
       {
       $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.VirtualHardDisk.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption Attach
       }
       ```
      For encrypted VMs, you need to specify [Key vault information](https://msdn.microsoft.com/library/dn868052.aspx) before you can attach disks.

      ```
      PS C:\> Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.StorageProfile.OSDisk.VirtualHardDisk.Uri -DiskEncryptionKeyUrl "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007" -DiskEncryptionKeyVaultId "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault" -KeyEncryptionKeyUrl "https://ContosoKeyVault.vault.azure.net:443/keys/ContosoKey007" -KeyEncryptionKeyVaultId "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault" -CreateOption "Attach" -Windows    PS C:\> $vm.StorageProfile.OsDisk.OsType = $obj.StorageProfile.OSDisk.OperatingSystemType    PS C:\> foreach($dd in $obj.StorageProfile.DataDisks)     {     $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.VirtualHardDisk.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption Attach     }
       ```
5. ネットワーク設定を設定します。

    ```
    PS C:\> $nicName="p1234"
    PS C:\> $pip = New-AzureRmPublicIpAddress -Name $nicName -ResourceGroupName "test" -Location "WestUS" -AllocationMethod Dynamic
    PS C:\> $vnet = Get-AzureRmVirtualNetwork -Name "testvNET" -ResourceGroupName "test"
    PS C:\> $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName "test" -Location "WestUS" -SubnetId $vnet.Subnets[$subnetindex].Id -PublicIpAddressId $pip.Id
    PS C:\> $vm=Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
    ```
6. 仮想マシンを作成します。

    ```    
    PS C:\> New-AzureRmVM -ResourceGroupName "test" -Location "WestUS" -VM $vm
    ```

## <a name="next-steps"></a>次のステップ
PowerShell を使用して Azure リソースを操作する場合は、Windows Server の保護について記載されている、[Windows Server のバックアップのデプロイと管理](backup-client-automation.md)に関する PowerShell の記事をご覧ください。 [DPM のバックアップのデプロイと管理](backup-dpm-automation.md)に関する PowerShell の記事で、DPM バックアップの管理について確認することもできます。 両方の記事で、Resource Manager デプロイとクラシック デプロイの両方のモデルについて説明しています。  

