---
title: PowerShell を使用して Windows Server を Azure にバックアップする
description: PowerShell を使用して Microsoft Azure Backup をデプロイおよび管理する手順の説明
author: pvrk
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 5/24/2018
ms.author: shivamg
ms.openlocfilehash: 5533b52ab984510b0e860f7fdfded8ac9005e5a8
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68465234"
---
# <a name="deploy-and-manage-backup-to-azure-for-windows-serverwindows-client-using-powershell"></a>PowerShell を使用して Windows Server/Windows Client に Microsoft Azure Backup をデプロイおよび管理する手順

この記事では、PowerShell を使用して、Windows Server または Windows クライアント上に Microsoft Azure Backup をセットアップし、バックアップと回復を管理する方法を示します。

## <a name="install-azure-powershell"></a>Azure PowerShell をインストールする
[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

開始するには、[PowerShell の最新リリースをインストール](/powershell/azure/install-az-ps)します。

## <a name="create-a-recovery-services-vault"></a>Recovery Services コンテナーの作成

次の手順では、Recovery Services コンテナーの作成について説明します。 Recovery Services コンテナーは Backup コンテナーとは異なります。

1. Azure Backup を初めて使用する場合、**Register-AzResourceProvider** コマンドレットを使って Azure Recovery Services プロバイダーをサブスクリプションに登録する必要があります。

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

2. Recovery Services コンテナーは ARM リソースであるため、リソース グループ内に配置する必要があります。 既存のリソース グループを使用することも、新しいリソース グループを作成することもできます。 新しいリソース グループを作成する場合、リソース グループの名前と場所を指定します。  

    ```powershell
    New-AzResourceGroup –Name "test-rg" –Location "WestUS"
    ```

3. **New-AzRecoveryServicesVault** コマンドレットを使用して、新しいコンテナーを作成します。 リソース グループに使用したのと同じコンテナーの場所を指定してください。

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "WestUS"
    ```

4. 使用するストレージ冗長性の種類を指定します。[ローカル冗長ストレージ (LRS)](../storage/common/storage-redundancy-lrs.md) または [geo 冗長ストレージ (GRS)](../storage/common/storage-redundancy-grs.md) を使用できます。 次に示す例では、testVault の -BackupStorageRedundancy オプションが GeoRedundant に設定されています。

   > [!TIP]
   > Azure Backup コマンドレットの多くは、入力として Recovery Services コンテナー オブジェクトを必要としています。 このため、Backup Recovery Services コンテナー オブジェクトを変数に格納すると便利です。
   >
   >

    ```powershell
    $Vault1 = Get-AzRecoveryServicesVault –Name "testVault"
    Set-AzRecoveryServicesBackupProperties -Vault $Vault1 -BackupStorageRedundancy GeoRedundant
    ```

## <a name="view-the-vaults-in-a-subscription"></a>サブスクリプション内のコンテナーの表示

**Get-AzRecoveryServicesVault** を使用して、現在のサブスクリプション内にあるすべてのコンテナーを一覧表示します。 このコマンドは、新しく作成したコンテナーを確認したり、サブスクリプション内の利用可能なコンテナーを確認したりするのに使用できます。

**Get-AzRecoveryServicesVault** コマンドを実行すると、サブスクリプション内にあるすべてのコンテナーが一覧表示されます。

```powershell
Get-AzRecoveryServicesVault
```

```Output
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```


[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="installing-the-azure-backup-agent"></a>Microsoft Azure Backup エージェントのインストール

Microsoft Azure Backup エージェントをインストールする前に、Windows Server に、インストーラーをダウンロードする必要があります。 最新バージョンのインストーラーは、 [Microsoft ダウンロード センター](https://aka.ms/azurebackup_agent) または Recovery Services コンテナーの [ダッシュボード] ページから入手することができます。 インストーラーを、*C:\Downloads\* などの、簡単にアクセスできる場所に保存します。

または、PowerShell を使用して、ダウンローダーを取得します。

 ```powershell
 $MarsAURL = 'https://aka.ms/Azurebackup_Agent'
 $WC = New-Object System.Net.WebClient
 $WC.DownloadFile($MarsAURL,'C:\downloads\MARSAgentInstaller.EXE')
 C:\Downloads\MARSAgentInstaller.EXE /q
 ```

エージェントをインストールするには、管理者特権の PowerShell コンソールで、次のコマンドを実行します。

```powershell
MARSAgentInstaller.exe /q
```

これにより、エージェントはすべて既定のオプションが指定されてインストールされます。 インストールは、バックグラウンドで数分かかります。 */nu* オプションを指定しない場合、インストールの最後に **[Windows Update]** ウィンドウが開き、更新プログラムが確認されます。 インストールすると、エージェントが、インストールされているプログラムの一覧に表示されます。

インストールされているプログラムの一覧を表示するには、 **[コントロール パネル]**  >  **[プログラム]**  >  **[プログラムと機能]** に移動します。

![インストールされているエージェント](./media/backup-client-automation/installed-agent-listing.png)

### <a name="installation-options"></a>インストール オプション

コマンドラインで利用可能なすべてのオプションを表示するには、次のコマンドを使用します。

```powershell
MARSAgentInstaller.exe /?
```

利用可能なオプションは、次のとおりです。

| オプション | 詳細 | 既定値 |
| --- | --- | --- |
| /q |サイレント インストール |- |
| /p:"location" |Azure Backup エージェントのインストール フォルダーへのパス |C:\Program Files\Microsoft Azure Recovery Services Agent |
| /s:"location" |Azure Backup エージェントのキャッシュ フォルダーへのパス |C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch |
| /m |Microsoft Update へのオプトイン |- |
| /nu |インストールが完了するまで更新プログラムを確認しない |- |
| /d |Microsoft Azure Recovery Services エージェントをアンインストールする |- |
| /ph |プロキシ ホストのアドレス |- |
| /po |プロキシ ホストのポート番号 |- |
| /pu |プロキシ ホストのユーザー名 |- |
| /pw |プロキシ パスワード |- |

## <a name="registering-windows-server-or-windows-client-machine-to-a-recovery-services-vault"></a>Recovery Services コンテナーへの Windows Server または Windows クライアント コンピューターの登録

Recovery Services コンテナーを作成したら、最新のエージェントとコンテナーの資格情報をダウンロードし、C:\Downloads のような便利な場所に保管します。

```powershell
$CredsPath = "C:\downloads"
$CredsFilename = Get-AzRecoveryServicesVaultSettingsFile -Backup -Vault $Vault1 -Path $CredsPath
```

Windows Server または Windows クライアント コンピューターで [Start-OBRegistration](https://technet.microsoft.com/library/hh770398%28v=wps.630%29.aspx) コマンドレットを実行し、コンピューターをコンテナーに登録します。
このコマンドレットをはじめとする、バックアップに使用される一連のコマンドレットは、インストール プロセスの過程で Mars AgentInstaller によって追加される MSONLINE モジュールに属しています。

このエージェント インストーラーでは、$Env:PSModulePath 変数が更新されません。 つまり、モジュールの自動読み込みに失敗します。 この問題を解決するには、次のコマンドレットを実行してください。

```powershell
$Env:PSModulePath += ';C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules'
```

または、次のようにして、スクリプトにモジュールを手動で読み込んでもかまいません。

```powershell
Import-Module -Name 'C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup'
```

Online Backup のコマンドレットを読み込んだら、コンテナーの資格情報を登録します。

```powershell
Start-OBRegistration -VaultCredentials $CredsFilename.FilePath -Confirm:$false
```

```Output
CertThumbprint      : 7a2ef2caa2e74b6ed1222a5e89288ddad438df2
SubscriptionID      : ef4ab577-c2c0-43e4-af80-af49f485f3d1
ServiceResourceName : testvault
Region              : WestUS
Machine registration succeeded.
```

> [!IMPORTANT]
> コンテナーの資格情報ファイルを指定する際には、相対パスは使用しないでください。 このコマンドレットへの入力には、絶対パスを指定する必要があります。
>
>

## <a name="networking-settings"></a>ネットワークの設定

プロキシ サーバーを介して Windows コンピューターをインターネットに接続した場合、プロキシ設定もエージェントに指定できます。 この例では、プロキシ サーバーがないため、プロキシ関連の情報はないことを明示的に示しています。

特定の曜日セットに対して、帯域幅の使用を、`work hour bandwidth`オプションと`non-work hour bandwidth`オプションで制御することもできます。

プロキシと帯域幅の詳細の設定は、 [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409%28v=wps.630%29.aspx) コマンドレットを使用して実行します。

```powershell
Set-OBMachineSetting -NoProxy
```

```Output
Server properties updated successfully.
```

```powershell
Set-OBMachineSetting -NoThrottle
```

```Output
Server properties updated successfully.
```

## <a name="encryption-settings"></a>暗号化の設定

データの機密性を保護するために、Microsoft Azure Backup に送信されるバックアップ データは暗号化されます。 暗号化パスフレーズは、復元時にデータの暗号化を解除するための "パスワード" になります。

Azure portal の **[Recovery Services コンテナー]** セクションにある **[設定]**  >  **[プロパティ]**  >  **[セキュリティ PIN]** 下の **[生成]** を選択して、セキュリティ PIN を生成する必要があります。 その後、コマンド内で `generatedPIN` としてこれを使用します。

```powershell
$PassPhrase = ConvertTo-SecureString -String "Complex!123_STRING" -AsPlainText -Force
Set-OBMachineSetting -EncryptionPassPhrase $PassPhrase -SecurityPin "<generatedPIN>"
```

```Output
Server properties updated successfully
```

> [!IMPORTANT]
> 設定したら、パスフレーズ情報をセキュリティで保護された安全な場所に保管してください。 このパスフレーズがないと、Azure からデータを復元できません。
>
>

## <a name="back-up-files-and-folders"></a>ファイルとフォルダーのバックアップ

Windows Server およびクライアントから Azure Backup へのすべてのバックアップは、ポリシーによって管理されます。 ポリシーは 3 つの部分で構成されます。

1. **バックアップ スケジュール**。バックアップをいつ実行し、いつサービスと同期するかを指定します。
2. **保持スケジュール** は、Azure で回復ポイントを保持する期間を指定します。
3. **ファイル内包/除外仕様**。何をバックアップするかを指定します。

このドキュメントでは、バックアップを自動化するため、構成を何も行っていないことを前提としています。 まず、 [New-OBPolicy](https://technet.microsoft.com/library/hh770416.aspx) コマンドレットを使って新しいバックアップ ポリシーを作成します。

```powershell
$NewPolicy = New-OBPolicy
```

この時点でポリシーは空で、どの項目を含むまたは除外するか、またバックアップをいつ実行するか、どこに格納するかを定義する他のコマンドレットが必要です。

### <a name="configuring-the-backup-schedule"></a>バックアップ スケジュールの構成

ポリシーの 3 つの構成要素の最初はバックアップ スケジュールです。これは、[New-OBSchedule](https://technet.microsoft.com/library/hh770401) コマンドレットを使って作成します。 バックアップ スケジュールでは、バックアップをいつ実行するかを定義します。 スケジュールを作成するとき、次の 2 つの入力パラメーターを指定する必要があります。

* **曜日** 。 バックアップ ジョブは、週に 1 回、毎日、またはこれらを組み合わせたさまざまな間隔で実行できます。
* **1 日のうちの時間帯** 。 1 日最大 3 回のバックアップを起動する時間を定義できます。

たとえば、毎週土曜日と日曜日の午後 4 時に実行されるようにバックアップ ポリシーを構成できます。

```powershell
$Schedule = New-OBSchedule -DaysOfWeek Saturday, Sunday -TimesOfDay 16:00
```

バックアップ スケジュールはポリシーに関連付ける必要があります。これを行うには、[Set-OBSchedule](https://technet.microsoft.com/library/hh770407) コマンドレットを使用します。

```powershell
Set-OBSchedule -Policy $NewPolicy -Schedule $Schedule
```

```Output
BackupSchedule : 4:00 PM Saturday, Sunday, Every 1 week(s) DsList : PolicyName : RetentionPolicy : State : New PolicyState : Valid
```
### <a name="configuring-a-retention-policy"></a>保有ポリシーの構成

保有ポリシーでは、バックアップ ジョブから作成した回復ポイントをどのくらいの期間保持するかを定義します。 [New-OBRetentionPolicy](https://technet.microsoft.com/library/hh770425) コマンドレットを使って新しい保有ポリシーを作成するとき、Microsoft Azure Backup でバックアップの回復ポイントを保持する日数を指定できます。 次の例では、7 日間の保有ポリシーを設定します。

```powershell
$RetentionPolicy = New-OBRetentionPolicy -RetentionDays 7
```

保有ポリシーは、コマンドレット [Set-OBRetentionPolicy](https://technet.microsoft.com/library/hh770405)を使用してメインのポリシーと関連付ける必要があります。

```powershell
Set-OBRetentionPolicy -Policy $NewPolicy -RetentionPolicy $RetentionPolicy
```

```Output
BackupSchedule  : 4:00 PM
                  Saturday, Sunday,
                  Every 1 week(s)
DsList          :
PolicyName      :
RetentionPolicy : Retention Days : 7

                  WeeklyLTRSchedule :
                  Weekly schedule is not set

                  MonthlyLTRSchedule :
                  Monthly schedule is not set

                  YearlyLTRSchedule :
                  Yearly schedule is not set

State           : New
PolicyState     : Valid
```
### <a name="including-and-excluding-files-to-be-backed-up"></a>バックアップするファイルを含むまたは除外する

`OBFileSpec` オブジェクトにより、ファイルをバックアップに含めるか除外するかを定義します。 ここでは、コンピューター上の保護されたファイルとフォルダーを範囲から除外する一連のルールを示します。 ファイル内包または除外ルールは必要に応じていくつでも保持でき、ポリシーに関連付けることができます。 新しい OBFileSpec オブジェクトを作成して、次の操作を実行できます。

* 含めるファイルおよびフォルダーを指定
* 除外するファイルおよびフォルダーを指定
* フォルダー内のデータの再帰バックアップを指定。または、指定したフォルダー内の最上位レベルのファイルのみをバックアップするかどうかを指定。

後者は、New-OBFileSpec コマンドの NonRecursive フラグを使用して実行できます。

次の例では、ボリューム C: および D: をバックアップし、Windows フォルダーと任意の一時フォルダー内の OS バイナリを除外します。 このためには、 [New-OBFileSpec](https://technet.microsoft.com/library/hh770408) コマンドレットを使用して 2 つのファイル指定 (内包用と除外用にそれぞれ 1 つずつ) を作成します。 ファイル指定が作成されたら、 [Add-OBFileSpec](https://technet.microsoft.com/library/hh770424) コマンドレットを使用してポリシーに関連付けます。

```powershell
$Inclusions = New-OBFileSpec -FileSpec @("C:\", "D:\")
$Exclusions = New-OBFileSpec -FileSpec @("C:\windows", "C:\temp") -Exclude
Add-OBFileSpec -Policy $NewPolicy -FileSpec $Inclusions
```

```Output
BackupSchedule  : 4:00 PM
                  Saturday, Sunday,
                  Every 1 week(s)
DsList          : {DataSource
                  DatasourceId:0
                  Name:C:\
                  FileSpec:FileSpec
                  FileSpec:C:\
                  IsExclude:False
                  IsRecursive:True

                  , DataSource
                  DatasourceId:0
                  Name:D:\
                  FileSpec:FileSpec
                  FileSpec:D:\
                  IsExclude:False
                  IsRecursive:True

                  }
PolicyName      :
RetentionPolicy : Retention Days : 7

                  WeeklyLTRSchedule :
                  Weekly schedule is not set

                  MonthlyLTRSchedule :
                  Monthly schedule is not set

                  YearlyLTRSchedule :
                  Yearly schedule is not set

State           : New
PolicyState     : Valid
```

```powershell
Add-OBFileSpec -Policy $NewPolicy -FileSpec $Exclusions
```

```Output
BackupSchedule  : 4:00 PM
                  Saturday, Sunday,
                  Every 1 week(s)
DsList          : {DataSource
                  DatasourceId:0
                  Name:C:\
                  FileSpec:FileSpec
                  FileSpec:C:\
                  IsExclude:False
                  IsRecursive:True
                  ,FileSpec
                  FileSpec:C:\windows
                  IsExclude:True
                  IsRecursive:True
                  ,FileSpec
                  FileSpec:C:\temp
                  IsExclude:True
                  IsRecursive:True

                  , DataSource
                  DatasourceId:0
                  Name:D:\
                  FileSpec:FileSpec
                  FileSpec:D:\
                  IsExclude:False
                  IsRecursive:True

                  }
PolicyName      :
RetentionPolicy : Retention Days : 7

                  WeeklyLTRSchedule :
                  Weekly schedule is not set

                  MonthlyLTRSchedule :
                  Monthly schedule is not set

                  YearlyLTRSchedule :
                  Yearly schedule is not set

State           : New
PolicyState     : Valid
```
## <a name="back-up-windows-server-system-state-in-mabs-agent"></a>MABS エージェントでの Windows Server のシステム状態のバックアップ

このセクションでは、MABS エージェントでシステムの状態を設定する PowerShell コマンドについて説明します

### <a name="schedule"></a>スケジュール
```powershell
$sched = New-OBSchedule -DaysOfWeek Sunday,Monday,Tuesday,Wednesday,Thursday,Friday,Saturday -TimesOfDay 2:00
```

### <a name="retention"></a>保持

```powershell
$rtn = New-OBRetentionPolicy -RetentionDays 32 -RetentionWeeklyPolicy -RetentionWeeks 13 -WeekDaysOfWeek Sunday -WeekTimesOfDay 2:00  -RetentionMonthlyPolicy -RetentionMonths 13 -MonthDaysOfMonth 1 -MonthTimesOfDay 2:00
```

### <a name="configuring-schedule-and-retention"></a>スケジュールと保持期間の構成

```powershell
New-OBPolicy | Add-OBSystemState |  Set-OBRetentionPolicy -RetentionPolicy $rtn | Set-OBSchedule -Schedule $sched | Set-OBSystemStatePolicy
 ```

### <a name="verifying-the-policy"></a>ポリシーの確認

```powershell
Get-OBSystemStatePolicy
 ```

### <a name="applying-the-policy"></a>ポリシーの適用

これで、ポリシー オブジェクトが完了し、バックアップ スケジュール、保有ポリシー、およびファイルの包含/除外リストに関連付けられました。 次に、Microsoft Azure Backup で使用するためにこのポリシーをコミットできます。 新しく作成されたポリシーを適用する前に、 [Remove-OBPolicy](https://technet.microsoft.com/library/hh770415) コマンドレットを使用して、サーバーに関連付けられた既存のバックアップ ポリシーがないことを確認します。 ポリシーを削除すると確認のダイアログが表示されます。 確認をスキップするには、コマンドレットで `-Confirm:$false` フラグを使用します。

```powershell
Get-OBPolicy | Remove-OBPolicy
```

```Output
Microsoft Azure Backup Are you sure you want to remove this backup policy? This will delete all the backed up data. [Y] Yes [A] Yes to All [N] No [L] No to All [S] Suspend [?] Help (default is "Y"):
```

[Set-OBPolicy](https://technet.microsoft.com/library/hh770421) コマンドレットで、ポリシー オブジェクトのコミットを実行します。 ここでも、確認のダイアログが表示されます。 確認をスキップするには、コマンドレットで `-Confirm:$false` フラグを使用します。

```powershell
Set-OBPolicy -Policy $NewPolicy
```

```Output
Microsoft Azure Backup Do you want to save this backup policy ? [Y] Yes [A] Yes to All [N] No [L] No to All [S] Suspend [?] Help (default is "Y"):
BackupSchedule : 4:00 PM Saturday, Sunday, Every 1 week(s)
DsList : {DataSource
         DatasourceId:4508156004108672185
         Name:C:\
         FileSpec:FileSpec
         FileSpec:C:\
         IsExclude:False
         IsRecursive:True,

         FileSpec
         FileSpec:C:\windows
         IsExclude:True
         IsRecursive:True,

         FileSpec
         FileSpec:C:\temp
         IsExclude:True
         IsRecursive:True,

         DataSource
         DatasourceId:4508156005178868542
         Name:D:\
         FileSpec:FileSpec
         FileSpec:D:\
         IsExclude:False
         IsRecursive:True
    }
PolicyName : c2eb6568-8a06-49f4-a20e-3019ae411bac
RetentionPolicy : Retention Days : 7
              WeeklyLTRSchedule :
              Weekly schedule is not set

              MonthlyLTRSchedule :
              Monthly schedule is not set

              YearlyLTRSchedule :
              Yearly schedule is not set
State : Existing PolicyState : Valid
```

既存のバックアップ ポリシーの詳細を表示するには、 [Get-OBPolicy](https://technet.microsoft.com/library/hh770406) コマンドレットを使用します。 バックアップ スケジュール用の [Get-OBSchedule](https://technet.microsoft.com/library/hh770423) コマンドレット、リテンション期間ポリシー用の [Get-OBRetentionPolicy](https://technet.microsoft.com/library/hh770427) コマンドレットを使用してさらにドリルダウンできます。

```powershell
Get-OBPolicy | Get-OBSchedule
```

```Output
SchedulePolicyName : 71944081-9950-4f7e-841d-32f0a0a1359a
ScheduleRunDays : {Saturday, Sunday}
ScheduleRunTimes : {16:00:00}
State : Existing
```

```powershell
Get-OBPolicy | Get-OBRetentionPolicy
```

```Output
RetentionDays : 7
RetentionPolicyName : ca3574ec-8331-46fd-a605-c01743a5265e
State : Existing
```

```powershell
Get-OBPolicy | Get-OBFileSpec
```

```Output
FileName : *
FilePath : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\
FileSpec : D:\
IsExclude : False
IsRecursive : True

FileName : *
FilePath : \?\Volume{cdd41007-a22f-11e2-be6c-806e6f6e6963}\
FileSpec : C:\
IsExclude : False
IsRecursive : True

FileName : *
FilePath : \?\Volume{cdd41007-a22f-11e2-be6c-806e6f6e6963}\windows
FileSpec : C:\windows
IsExclude : True
IsRecursive : True

FileName : *
FilePath : \?\Volume{cdd41007-a22f-11e2-be6c-806e6f6e6963}\temp
FileSpec : C:\temp
IsExclude : True
IsRecursive : True
```

### <a name="performing-an-ad-hoc-backup"></a>アドホック バックアップの実行

バックアップ ポリシーが設定されると、スケジュールに従ってバックアップが実行されます。 [Start-OBBackup](https://technet.microsoft.com/library/hh770426) コマンドレットを使って、アドホック バックアップを起動することもできます。

```powershell
Get-OBPolicy | Start-OBBackup
```

```Output
Initializing
Taking snapshot of volumes...
Preparing storage...
Generating backup metadata information and preparing the metadata VHD...
Data transfer is in progress. It might take longer since it is the first backup and all data needs to be transferred...
Data transfer completed and all backed up data is in the cloud. Verifying data integrity...
Data transfer completed
In progress...
Job completed.
The backup operation completed successfully.
```

## <a name="restore-data-from-azure-backup"></a>Microsoft Azure Backup からのデータの復元

このセクションでは、Microsoft Azure Backup からのデータの復元を自動化する方法を手順に従って説明します。 次の手順を実行します。

1. ソース ボリュームを選択する
2. バックアップの復元ポイントを選択する
3. 復元する項目を選択する
4. 復元プロセスをトリガーする

### <a name="picking-the-source-volume"></a>ソース ボリュームの選択

Microsoft Azure Backup から項目を復元するには、まず、項目のソースを特定する必要があります。 Windows サーバーまたは Windows クライアントのコンテキストでコマンドを実行するため、コンピューターは既に特定されています。 ソースを特定する次の手順は、それを含むボリュームを特定することです。 該当のコンピューターからバックアップするボリュームまたはソースの一覧は、 [Get-OBRecoverableSource](https://technet.microsoft.com/library/hh770410) コマンドレットを実行して取得できます。 このコマンドは、このサーバー/クライアントでバックアップされるすべてのソースの配列を返します。

```powershell
$Source = Get-OBRecoverableSource
$Source
```

```Output
FriendlyName : C:\
RecoverySourceName : C:\
ServerName : myserver.microsoft.com

FriendlyName : D:\
RecoverySourceName : D:\
ServerName : myserver.microsoft.com
```

### <a name="choosing-a-backup-point-from-which-to-restore"></a>復元元のバックアップ ポイントの選択

バックアップ ポイントの一覧は、[Get-OBRecoverableItem](https://technet.microsoft.com/library/hh770399.aspx) コマンドレットと適切なパラメーターを実行して取得できます。 この例では、ソース ボリューム *D:* の最新のバックアップ ポイントを選択し、これを使用して特定のファイルを復旧します。

```powershell
$Rps = Get-OBRecoverableItem -Source $Source[1]
```

```Output
IsDir : False
ItemNameFriendly : D:\
ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\
LocalMountPoint : D:\
MountPointName : D:\
Name : D:\
PointInTime : 18-Jun-15 6:41:52 AM
ServerName : myserver.microsoft.com
ItemSize :
ItemLastModifiedTime :

IsDir : False
ItemNameFriendly : D:\
ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\
LocalMountPoint : D:\
MountPointName : D:\
Name : D:\
PointInTime : 17-Jun-15 6:31:31 AM
ServerName : myserver.microsoft.com
ItemSize :
ItemLastModifiedTime :
```

オブジェクト `$Rps` は、バックアップ ポイントの配列です。 最初の要素は最新のポイントで、N 番目の要素は最も古いポイントです。 最新のポイントを選択するには、 `$Rps[0]`を使用します。

### <a name="choosing-an-item-to-restore"></a>復元する項目の選択

復元する正確なファイルまたはフォルダーを特定するには、 [Get-OBRecoverableItem](https://technet.microsoft.com/library/hh770399.aspx) コマンドレットを再帰的に使用します。 ここでは、フォルダー階層を `Get-OBRecoverableItem`のみを使用して参照できます。

この例では、ファイル *finances.xls* を復元するには、オブジェクト `$FilesFolders[1]` を使用してこれを参照できます。

```powershell
$FilesFolders = Get-OBRecoverableItem $Rps[0]
$FilesFolders
```

```Output
IsDir : True
ItemNameFriendly : D:\MyData\
ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\MyData\
LocalMountPoint : D:\
MountPointName : D:\
Name : MyData
PointInTime : 18-Jun-15 6:41:52 AM
ServerName : myserver.microsoft.com
ItemSize :
ItemLastModifiedTime : 15-Jun-15 8:49:29 AM
```

```powershell
$FilesFolders = Get-OBRecoverableItem $FilesFolders[0]
$FilesFolders
```

```Output
IsDir : False
ItemNameFriendly : D:\MyData\screenshot.oxps
ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\MyData\screenshot.oxps
LocalMountPoint : D:\
MountPointName : D:\
Name : screenshot.oxps
PointInTime : 18-Jun-15 6:41:52 AM
ServerName : myserver.microsoft.com
ItemSize : 228313
ItemLastModifiedTime : 21-Jun-14 6:45:09 AM

IsDir : False
ItemNameFriendly : D:\MyData\finances.xls
ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\MyData\finances.xls
LocalMountPoint : D:\
MountPointName : D:\
Name : finances.xls
PointInTime : 18-Jun-15 6:41:52 AM
ServerName : myserver.microsoft.com
ItemSize : 96256
ItemLastModifiedTime : 21-Jun-14 6:43:02 AM
```

復元する項目を検索するには、 ```Get-OBRecoverableItem``` コマンドレットを使用します。 この例では、 *finances.xls* を検索するために、次のコマンドを実行して、ファイルのハンドルを取得します。

```powershell
$Item = Get-OBRecoverableItem -RecoveryPoint $Rps[0] -Location "D:\MyData" -SearchString "finance*"
```

### <a name="triggering-the-restore-process"></a>復元プロセスのトリガー

復元プロセスをトリガーするには、まず、回復オプションを指定する必要があります。 これを行うには、 [New-OBRecoveryOption](https://technet.microsoft.com/library/hh770417.aspx) コマンドレットを使用します。 この例では、ファイルを *C:\temp* に復元すると仮定します。また、宛先フォルダー *C:\temp* に既に存在するファイルをスキップすると仮定します。こうした回復オプションを作成するには、次のコマンドを使用します。

```powershell
$RecoveryOption = New-OBRecoveryOption -DestinationPath "C:\temp" -OverwriteType Skip
```

次に、`Get-OBRecoverableItem` コマンドレットの出力から選択した `$Item` で [Start-OBRecovery](https://technet.microsoft.com/library/hh770402.aspx) コマンドを使用して復元プロセスをトリガーします。

```powershell
Start-OBRecovery -RecoverableItem $Item -RecoveryOption $RecoveryOption
```

```Output
Estimating size of backup items...
Estimating size of backup items...
Estimating size of backup items...
Estimating size of backup items...
Job completed.
The recovery operation completed successfully.
```

## <a name="uninstalling-the-azure-backup-agent"></a>Microsoft Azure Backup エージェントのアンインストール

Microsoft Azure Backup エージェントのアンインストールは、次のコマンドを使用して実行できます。

```powershell
.\MARSAgentInstaller.exe /d /q
```

エージェント バイナリをコンピューターからアンインストールすると、考慮すべき別の結果が生じます。

* ファイル フィルターがコンピューターから削除され、変更の追跡が停止されます。
* すべてのポリシー情報がコンピューターから削除されますが、ポリシー情報は引き続きサービスに格納されます。
* すべてのバックアップ スケジュールが削除され、以降のバックアップは実施されません。

ただし、Azure に格納されたデータは、設定した保有ポリシーに基づいて維持されます。 古いポイントの期限は自動的に切れます。

## <a name="remote-management"></a>リモート管理

Microsoft Azure Backup エージェント、ポリシー、およびデータ ソースに関する管理はすべて、PowerShell を使ってリモートで実行できます。 リモートで管理されるコンピューターは、適切に準備されている必要があります。

既定では、WinRM サービスは手動で開始されるように設定されています。 スタートアップの種類は "*自動*" に設定する必要があります。これにより、サービスが開始されます。 WinRM サービスが実行していることを確認するには、Status プロパティの値が *[実行中]* になっていることを確認します。

```powershell
Get-Service -Name WinRM
```

```Output
Status   Name               DisplayName
------   ----               -----------
Running  winrm              Windows Remote Management (WS-Manag...
```

リモート処理用に PowerShell を構成します。

```powershell
Enable-PSRemoting -Force
```

```Output
WinRM is already set up to receive requests on this computer.
WinRM has been updated for remote management.
WinRM firewall exception enabled.
```

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force
```

コンピューターを、エージェントのインストールを始め、リモートで管理できるようになりました。 たとえば、次のスクリプトは、エージェントをリモート コンピューターにコピーし、インストールします。

```powershell
$DLoc = "\\REMOTESERVER01\c$\Windows\Temp"
$Agent = "\\REMOTESERVER01\c$\Windows\Temp\MARSAgentInstaller.exe"
$Args = "/q"
Copy-Item "C:\Downloads\MARSAgentInstaller.exe" -Destination $DLoc -Force

$Session = New-PSSession -ComputerName REMOTESERVER01
Invoke-Command -Session $Session -Script { param($D, $A) Start-Process -FilePath $D $A -Wait } -ArgumentList $Agent, $Args
```

## <a name="next-steps"></a>次の手順

Azure Backup for Windows Server/Client の詳細については、以下を参照してください。

* [Azure Backup の概要](backup-introduction-to-azure-backup.md)
* [Windows Server のバックアップ](backup-configure-vault.md)
