---
title: "PowerShell を使用した Azure VM のバックアップのデプロイおよび管理 | Microsoft Docs"
description: "PowerShell を使って Microsoft Azure Backup をデプロイおよび管理する手順を説明します。"
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
ms.assetid: 2e24b1d9-4375-4049-a28d-e3bc01152f32
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2016
ms.author: markgal;trinadhk;jimpark
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 82b7541ab1434179353247ffc50546812346bda9
ms.openlocfilehash: aa1934447b53b725a08cebb47da9171a136b76ff
ms.lasthandoff: 03/02/2017


---
# <a name="use-azurermbackup-cmdlets-to-back-up-virtual-machines"></a>AzureRM.Backup コマンドレットを使って仮想マシンをバックアップする
> [!div class="op_single_selector"]
> * [リソース マネージャー](backup-azure-vms-automation.md)
> * [クラシック](backup-azure-vms-classic-automation.md)
>
>

この記事では、Azure の VM をバックアップおよび回復するために Azure PowerShell を使用する方法を示します。 Azure には、リソースの作成と操作に関して&2; 種類のデプロイメント モデルがあります。Resource Manager デプロイメント モデルとクラシック デプロイメント モデルです。 この記事では、クラシック デプロイ モデルの使用方法について説明します。 最新のデプロイメントでは、リソース マネージャー モデルを使用することをお勧めします。

## <a name="concepts"></a>概念
この記事では、仮想マシンのバックアップに使用する PowerShell コマンドレットに特有の情報を提供します。 Azure VM の保護に関する概要については、「 [Azure における VM バックアップ インフラストラクチャの計画を立てる](backup-azure-vms-introduction.md)」を参照してください。

> [!NOTE]
> 開始する前に、Azure Backup を使用するうえで必要な[前提条件](backup-azure-vms-prepare.md)を確認し、現在の VM バックアップ ソリューションの[制限事項](backup-azure-vms-prepare.md#limitations-when-backing-up-and-restoring-a-vm)を把握してください。
>
>

PowerShell を効果的に使用できるように、オブジェクトの階層と開始地点を理解しておいてください。

![オブジェクト階層](./media/backup-azure-vms-classic-automation/object-hierarchy.png)

2 つの最も重要なフローは、VM の保護の有効化と、復旧ポイントからのデータの復元です。 この記事は、これらの&2; つのシナリオを実現するために、PowerShell コマンドレットの操作に熟達することを目的としています。

## <a name="setup-and-registration"></a>セットアップと登録
開始するには

1. [最新の PowerShell](https://github.com/Azure/azure-powershell/releases) (1.0.0 以降のバージョンが必要) をダウンロードします。
2. 以下のコマンドを入力して、使用可能な Azure Backup の PowerShell コマンドレットを検索します。

```
PS C:\> Get-Command *azurermbackup*

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Backup-AzureRmBackupItem                           1.0.1      AzureRM.Backup
Cmdlet          Disable-AzureRmBackupProtection                    1.0.1      AzureRM.Backup
Cmdlet          Enable-AzureRmBackupContainerReregistration        1.0.1      AzureRM.Backup
Cmdlet          Enable-AzureRmBackupProtection                     1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupContainer                         1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupItem                              1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupJob                               1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupJobDetails                        1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupProtectionPolicy                  1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupRecoveryPoint                     1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupVault                             1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupVaultCredentials                  1.0.1      AzureRM.Backup
Cmdlet          New-AzureRmBackupProtectionPolicy                  1.0.1      AzureRM.Backup
Cmdlet          New-AzureRmBackupRetentionPolicyObject             1.0.1      AzureRM.Backup
Cmdlet          New-AzureRmBackupVault                             1.0.1      AzureRM.Backup
Cmdlet          Register-AzureRmBackupContainer                    1.0.1      AzureRM.Backup
Cmdlet          Remove-AzureRmBackupProtectionPolicy               1.0.1      AzureRM.Backup
Cmdlet          Remove-AzureRmBackupVault                          1.0.1      AzureRM.Backup
Cmdlet          Restore-AzureRmBackupItem                          1.0.1      AzureRM.Backup
Cmdlet          Set-AzureRmBackupProtectionPolicy                  1.0.1      AzureRM.Backup
Cmdlet          Set-AzureRmBackupVault                             1.0.1      AzureRM.Backup
Cmdlet          Stop-AzureRmBackupJob                              1.0.1      AzureRM.Backup
Cmdlet          Unregister-AzureRmBackupContainer                  1.0.1      AzureRM.Backup
Cmdlet          Wait-AzureRmBackupJob                              1.0.1      AzureRM.Backup
```

PowerShell を使用して次のセットアップおよび登録タスクを自動化できます。

* バックアップ資格情報コンテナーの作成
* Microsoft Azure Backup サービスを使用した VM の登録

### <a name="create-a-backup-vault"></a>バックアップ資格情報コンテナーの作成
> [!WARNING]
> 顧客が初めて Azure Backup を使用する場合、サブスクリプションで使用する Azure Backup プロバイダーを登録する必要があります。 これは、Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Backup" コマンドを実行して行うことができます。
>
>

**New-AzureRmBackupVault** コマンドレットを使用すると、新しいバックアップ コンテナーを作成できます。 バックアップ コンテナーは ARM リソースであるため、リソース グループ内に配置する必要があります。 管理者特権の Azure PowerShell コンソールで、次のコマンドを実行します。

```
PS C:\> New-AzureRmResourceGroup –Name “test-rg” –Location “West US”
PS C:\> $backupvault = New-AzureRmBackupVault –ResourceGroupName “test-rg” –Name “test-vault” –Region “West US” –Storage GeoRedundant
```

**Get-AzureRmBackupVault** コマンドレットを使用して、特定のサブスクリプション内のすべてのバックアップ コンテナーの一覧を取得できます。

> [!NOTE]
> バックアップ コンテナー オブジェクトは変数に格納すると便利です。 コンテナー オブジェクトは、多くの Azure Backup コマンドレットの入力に必要です。
>
>

### <a name="registering-the-vms"></a>VM の登録
Azure Backup のバックアップを構成するには、まず Azure Backup コンテナーにコンピューターまたは VM を登録します。 **Register-AzureRmBackupContainer** コマンドレットは、Azure IaaS 仮想マシンの入力情報を受け取り、指定のコンテナーへの登録を行います。 登録操作は、Azure virtual machine とバックアップ コンテナーを関連付け、VM をバックアップのライフサイクルを通じて追跡します。

Azure Backup サービスを VM に登録する場合、最上位のコンテナー オブジェクトが作成されます。 通常コンテナーには、バックアップ可能な項目が複数ありますが、VM の場合、コンテナーのバックアップ項目は&1; つのみとなります。

```
PS C:\> $registerjob = Register-AzureRmBackupContainer -Vault $backupvault -Name "testvm" -ServiceName "testvm"
```

## <a name="backup-azure-vms"></a>Azure VM のバックアップ
### <a name="create-a-protection-policy"></a>保護ポリシーの作成
VM のバックアップを開始する際、新しい保護ポリシーを作成する必要はありません。 コンテナーには、後ですぐに正しい詳細に編集できる、保護をすぐに有効にするために使用できる '既定のポリシー' が含まれます。 **Get-AzureRmBackupProtectionPolicy** コマンドレットを使用すると、コンテナーにあるポリシーの一覧を取得することができます。

```
PS C:\> Get-AzureRmBackupProtectionPolicy -Vault $backupvault

Name                      Type               ScheduleType       BackupTime
----                      ----               ------------       ----------
DefaultPolicy             AzureVM            Daily              26-Aug-15 12:30:00 AM
```

> [!NOTE]
> PowerShell の BackupTime フィールドのタイムゾーンは UTC です。 ただし、Azure ポータルでバックアップ時刻が表示されるときには、UTC オフセットとローカル システムに合わせてタイムゾーンが調整されます。
>
>

バックアップ ポリシーは、少なくとも&1; つのアイテム保持ポリシーと関連付けられています。 アイテム保持ポリシーには、Azure Backup で復旧ポイントを保持する期間が定義されています。 **New-AzureRmBackupRetentionPolicy** コマンドレットは、アイテム保持ポリシー情報を保持する PowerShell オブジェクトを作成します。 これらのアイテム リテンション期間ポリシー オブジェクトは、*New-AzureRmBackupProtectionPolicy* コマンドレットへの入力として使用されるか、*Enable-AzureRmBackupProtection* コマンドレットで直接使用されます。

バックアップ ポリシーには、アイテムのバックアップが実行されるタイミングと方法を定義します。 **New-AzureRmBackupProtectionPolicy** コマンドレットは、バックアップ ポリシー情報を保持する PowerShell オブジェクトを作成します。 バックアップ ポリシーは、 *Enable-AzureRmBackupProtection* コマンドレットへの入力として使用されます。

```
PS C:\> $Daily = New-AzureRmBackupRetentionPolicyObject -DailyRetention -Retention 30
PS C:\> $newpolicy = New-AzureRmBackupProtectionPolicy -Name DailyBackup01 -Type AzureVM -Daily -BackupTime ([datetime]"3:30 PM") -RetentionPolicy $Daily -Vault $backupvault

Name                      Type               ScheduleType       BackupTime
----                      ----               ------------       ----------
DailyBackup01             AzureVM            Daily              01-Sep-15 3:30:00 PM
```

### <a name="enable-protection"></a>保護を有効にする
保護を有効にするには、同じコンテナーに属する、アイテムとポリシーの&2; つのオブジェクトが必要です。 ポリシーとアイテムの関連付けが完了すると、バックアップのワークフローが定義されたスケジュールで開始されます。

```
PS C:\> Get-AzureRmBackupContainer -Type AzureVM -Status Registered -Vault $backupvault | Get-AzureRmBackupItem | Enable-AzureRmBackupProtection -Policy $newpolicy
```

### <a name="initial-backup"></a>初回バックアップ
バックアップのスケジュールによって、アイテムの完全な初回コピーと以降のバックアップの増分コピーが行われます。 ただし、初回バックアップが特定の時刻に行われるか、すぐに行われるようにする場合は、 **Backup-AzureRmBackupItem** コマンドレットを使用します。

```
PS C:\> $container = Get-AzureRmBackupContainer -Vault $backupvault -Type AzureVM -Name "testvm"
PS C:\> $backupjob = Get-AzureRmBackupItem -Container $container | Backup-AzureRmBackupItem
PS C:\> $backupjob

WorkloadName    Operation       Status          StartTime              EndTime
------------    ---------       ------          ---------              -------
testvm          Backup          InProgress      01-Sep-15 12:24:01 PM  01-Jan-01 12:00:00 AM
```

> [!NOTE]
> PowerShell で表示される StartTime フィールドと EndTime フィールドのタイムゾーンは UTC です。 ただし、Azure ポータルで同様の情報が表示されるときには、ローカル システム クロックに合わせてタイムゾーンが調整されます。
>
>

### <a name="monitoring-a-backup-job"></a>バックアップ ジョブの監視
Azure Backup で長時間実行される多くの操作は、ジョブとしてモデル化されています。 これにより、Azure ポータルを常に開いていなくても進捗を簡単に追跡できるようになります。

進行中のジョブの最新の状態を取得するには、 **Get-AzureRmBackupJob** コマンドレットを使用します。

```
PS C:\> $joblist = Get-AzureRmBackupJob -Vault $backupvault -Status InProgress
PS C:\> $joblist[0]

WorkloadName    Operation       Status          StartTime              EndTime
------------    ---------       ------          ---------              -------
testvm          Backup          InProgress      01-Sep-15 12:24:01 PM  01-Jan-01 12:00:00 AM
```

完了確認のためにこれらのジョブをポーリングすると、不要な追加コードが発生します。代わりに、**Wait-AzureRmBackupJob** コマンドレットを使用する方が簡単です。 スクリプトで使用する場合、ジョブが完了するまで、または指定したタイムアウト値に到達するまでコマンドレットは実行を停止します。

```
PS C:\> Wait-AzureRmBackupJob -Job $joblist[0] -Timeout 43200
```


## <a name="restore-an-azure-vm"></a>Azure VM の復元
バックアップ データを復元するには、バックアップ項目と復元する特定の時点のデータを保持する復元ポイントを特定する必要があります。 この情報は、コンテナーからお客様のアカウントにデータの復元を開始するために、Restore-AzureRmBackupItem コマンドレットに提供されます。

### <a name="select-the-vm"></a>VM の選択
バックアップする正しい項目を特定する PowerShell オブジェクトを取得するには、コンテナー内のコンテナーから開始し、次いでオブジェクト階層の上から下に進みます。 VM を表すコンテナーを選択するには、**Get-AzureRmBackupContainer** コマンドレットを使用し、**Get-AzureRmBackupItem** コマンドレットへのパイプとして使用します。

```
PS C:\> $backupitem = Get-AzureRmBackupContainer -Vault $backupvault -Type AzureVM -name "testvm" | Get-AzureRmBackupItem
```

### <a name="choose-a-recovery-point"></a>復元ポイントの選択
これで **Get-AzureRmBackupRecoveryPoint** コマンドレットを使用して、バックアップ アイテムのすべての復元ポイントを一覧表示し、復元する復元ポイントを選択できます。 通常、ユーザーはこのリスト内の最新の *AppConsistent* ポイントを選択します。

```
PS C:\> $rp =  Get-AzureRmBackupRecoveryPoint -Item $backupitem
PS C:\> $rp

RecoveryPointId    RecoveryPointType  RecoveryPointTime      ContainerName
---------------    -----------------  -----------------      -------------
15273496567119     AppConsistent      01-Sep-15 12:27:38 PM  iaasvmcontainer;testvm;testv...
```

```$rp``` 変数は、選択したバックアップ アイテムの復旧ポイントの配列であり、時間の逆順に並べ替えられています。つまり、最新の復旧ポイントがインデックス 0 の位置になります。 標準の PowerShell 配列のインデックスを使用して、復旧ポイントを選択します。 例: ```$rp[0]``` は、最新の復旧ポイントを選択します。

### <a name="restoring-disks"></a>データの復元
Azure ポータルと Azure PowerShell を使用して実行する復元処理には決定的な違いがあります。 Powershell では、復元操作は、復元ポイントからディスクと構成情報を復元すると停止します。 仮想マシンは作成されません。

> [!WARNING]
> Restore-AzureRmBackupItem では、VM は作成されません。 指定したストレージ アカウントにディスクを復元するのみです。 これは、Azure ポータルの動作とは異なります。
>
>

```
PS C:\> $restorejob = Restore-AzureRmBackupItem -StorageAccountName "DestAccount" -RecoveryPoint $rp[0]
PS C:\> $restorejob

WorkloadName    Operation       Status          StartTime              EndTime
------------    ---------       ------          ---------              -------
testvm          Restore         InProgress      01-Sep-15 1:14:01 PM   01-Jan-01 12:00:00 AM
```

復元ジョブが完了したら、 **Get-AzureRmBackupJobDetails** コマンドレットを使用して復元操作の詳細を取得できます。 *ErrorDetails* プロパティには、VM を再構築するために必要な情報が含まれます。

```
PS C:\> $restorejob = Get-AzureRmBackupJob -Job $restorejob
PS C:\> $details = Get-AzureRmBackupJobDetails -Job $restorejob
```

### <a name="build-the-vm"></a>VM の構築
復元したディスクから VM を構築するには、古い Azure サービス管理 PowerShell コマンドレット、新しい Azure Resource Manager テンプレート、または Azure ポータルを使用して実行できます。 Azure サービス管理コマンドレットを使用して、これを実行する簡単な例を示します。

```
$properties  = $details.Properties

$storageAccountName = $properties["Target Storage Account Name"]
$containerName = $properties["Config Blob Container Name"]
$blobName = $properties["Config Blob Name"]

$keys = Get-AzureStorageKey -StorageAccountName $storageAccountName
$storageAccountKey = $keys.Primary
$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey


$destination_path = "C:\Users\admin\Desktop\vmconfig.xml"
Get-AzureStorageBlobContent -Container $containerName -Blob $blobName -Destination $destination_path -Context $storageContext


$obj = [xml](((Get-Content -Path $destination_path -Encoding UniCode)).TrimEnd([char]0x00))
$pvr = $obj.PersistentVMRole
$os = $pvr.OSVirtualHardDisk
$dds = $pvr.DataVirtualHardDisks
$osDisk = Add-AzureDisk -MediaLocation $os.MediaLink -OS $os.OS -DiskName "panbhaosdisk"
$vm = New-AzureVMConfig -Name $pvr.RoleName -InstanceSize $pvr.RoleSize -DiskName $osDisk.DiskName

if (!($dds -eq $null))
{
    foreach($d in $dds.DataVirtualHardDisk)
    {
        $lun = 0
        if(!($d.Lun -eq $null))
        {
            $lun = $d.Lun
        }
        $name = "panbhadataDisk" + $lun
        Add-AzureDisk -DiskName $name -MediaLocation $d.MediaLink
        $vm | Add-AzureDataDisk -Import -DiskName $name -LUN $lun
    }
}

New-AzureVM -ServiceName "panbhasample" -Location "SouthEast Asia" -VM $vm
```

復元したディスクから VM を構築する方法の詳細については、次のコマンドレットに関する説明を参照してください。

* [Add-AzureDisk](https://msdn.microsoft.com/library/azure/dn495252.aspx)
* [New-AzureVMConfig](https://msdn.microsoft.com/library/azure/dn495159.aspx)
* [New-AzureVM](https://msdn.microsoft.com/library/azure/dn495254.aspx)

## <a name="code-samples"></a>コード サンプル
### <a name="1-get-the-completion-status-of-job-sub-tasks"></a>1.ジョブのサブタスクの完了状態の取得
個々のサブタスクの完了状態を追跡するには、 **Get-AzureRmBackupJobDetails** コマンドレットを使用します。

```
PS C:\> $details = Get-AzureRmBackupJobDetails -JobId $backupjob.InstanceId -Vault $backupvault
PS C:\> $details.SubTasks

Name                                                        Status
----                                                        ------
Take Snapshot                                               Completed
Transfer data to Backup vault                               InProgress
```

### <a name="2-create-a-dailyweekly-report-of-backup-jobs"></a>2.バックアップ ジョブの日次/週次レポートの作成
通常、管理者は過去 24 時間に実行されたバックアップ ジョブと、それらのバックアップ ジョブの状態を知りたいと考えています。 また、転送されたデータの量によって、管理者は毎月のデータ使用量を推定できます。 次のスクリプトは、Azure Backup サービスから生データを取得し、PowerShell コンソールに情報を表示します。

```
param(  [Parameter(Mandatory=$True,Position=1)]
        [string]$backupvaultname,

        [Parameter(Mandatory=$False,Position=2)]
        [int]$numberofdays = 7)


#Initialize variables
$DAILYBACKUPSTATS = @()
$backupvault = Get-AzureRmBackupVault -Name $backupvaultname
$enddate = ([datetime]::Today).AddDays(1)
$startdate = ([datetime]::Today)

for( $i = 1; $i -le $numberofdays; $i++ )
{
    # We query one day at a time because pulling 7 days of data might be too much
    $dailyjoblist = Get-AzureRmBackupJob -Vault $backupvault -From $startdate -To $enddate -Type AzureVM -Operation Backup
    Write-Progress -Activity "Getting job information for the last $numberofdays days" -Status "Day -$i" -PercentComplete ([int]([decimal]$i*100/$numberofdays))

    foreach( $job in $dailyjoblist )
    {
        #Extract the information for the reports
        $newstatsobj = New-Object System.Object
        $newstatsobj | Add-Member -Type NoteProperty -Name Date -Value $startdate
        $newstatsobj | Add-Member -Type NoteProperty -Name VMName -Value $job.WorkloadName
        $newstatsobj | Add-Member -Type NoteProperty -Name Duration -Value $job.Duration
        $newstatsobj | Add-Member -Type NoteProperty -Name Status -Value $job.Status

        $details = Get-AzureRmBackupJobDetails -Job $job
        $newstatsobj | Add-Member -Type NoteProperty -Name BackupSize -Value $details.Properties["Backup Size"]
        $DAILYBACKUPSTATS += $newstatsobj
    }

    $enddate = $enddate.AddDays(-1)
    $startdate = $startdate.AddDays(-1)
}

$DAILYBACKUPSTATS | Out-GridView
```

このレポート出力にグラフ作成機能を追加する場合は、TechNet ブログ投稿「 [Charting with PowerShell (PowerShell でのグラフ作成)](http://blogs.technet.com/b/richard_macdonald/archive/2009/04/28/3231887.aspx)

## <a name="next-steps"></a>次のステップ
PowerShell を使用して Azure リソースを操作する場合は、Windows Server の保護について記載されている、[Windows Server のバックアップのデプロイと管理](backup-client-automation-classic.md)に関する PowerShell の記事をご覧ください。 [DPM のバックアップのデプロイと管理](backup-dpm-automation-classic.md)に関する PowerShell の記事で、DPM バックアップの管理について確認することもできます。 両方の記事で、Resource Manager デプロイとクラシック デプロイの両方のモデルについて説明しています。

