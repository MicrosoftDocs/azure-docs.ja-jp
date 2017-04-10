---
title: "PowerShell を使用して Windows Server を Azure にバックアップする | Microsoft Docs"
description: "PowerShell を使用して Microsoft Azure Backup をデプロイおよび管理する手順の説明"
services: backup
documentationcenter: 
author: saurabhsensharma
manager: shivamg
editor: 
ms.assetid: 65218095-2996-44d9-917b-8c84fc9ac415
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2016
ms.author: saurse;markgal;jimpark;nkolli;trinadhk
translationtype: Human Translation
ms.sourcegitcommit: 2224ddf52283d7da599b1b4842ca617d28b28668
ms.openlocfilehash: 87384588e9e2a77a5b545ce30db2776541223001


---
# <a name="deploy-and-manage-backup-to-azure-for-windows-serverwindows-client-using-powershell"></a>PowerShell を使用して Windows Server/Windows Client に Microsoft Azure Backup をデプロイおよび管理する手順
> [!div class="op_single_selector"]
> * [ARM](backup-client-automation.md)
> * [クラシック](backup-client-automation-classic.md)
>
>

この記事では、PowerShell を使用して、Windows Server または Windows クライアント上に Microsoft Azure Backup をセットアップし、バックアップと回復を管理する方法を示します。

## <a name="install-azure-powershell"></a>Azure PowerShell をインストールするには
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]

この記事では、リソース グループで Recovery Services コンテナーを使用できるようにする Azure Resource Manager (ARM) PowerShell コマンドレットを中心に説明します。

Azure PowerShell 1.0 は、2015 年 10 月にリリースされました。 これは 0.9.8 リリースの次のリリースです。特にコマンドレットの命名パター名など、いくつかの重大な変更点があります。 1.0 のコマンドレットは、{動詞}-AzureRm{名詞}; という命名パターンに従っているのに対し、0.9.8 の名前には **Rm** が含まれません (たとえば、New-AzureResourceGroup ではなく New-AzureRmResourceGroup)。 Azure PowerShell 0.9.8 を使用している場合、まず、 **Switch-AzureMode AzureResourceManager** コマンドを実行してリソース マネージャー モードを有効にする必要があります。 このコマンドは、1.0 以降では必要ありません。

0.9.8 環境向けに作成されたスクリプトを 1.0 以降の環境で使用する場合、運用前環境でスクリプトを慎重にテストしてから運用環境で使用し、予期しない影響が発生しないようにします。

[PowerShell の最新リリースのダウンロード](https://github.com/Azure/azure-powershell/releases) (バージョン 1.0.0 以降が必要)

[!INCLUDE [arm-getting-setup-powershell](../../includes/arm-getting-setup-powershell.md)]

## <a name="create-a-recovery-services-vault"></a>Recovery Services コンテナーの作成
次の手順では、Recovery Services コンテナーの作成について説明します。 Recovery Services コンテナーは Backup コンテナーとは異なります。

1. Azure Backup を初めて使用する場合、 **Register-AzureRMResourceProvider** コマンドレットを使って Azure Recovery Services プロバイダーをサブスクリプションに登録する必要があります。

    ```
    PS C:\> Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```
2. Recovery Services コンテナーは ARM リソースであるため、リソース グループ内に配置する必要があります。 既存のリソース グループを使用することも、新しいリソース グループを作成することもできます。 新しいリソース グループを作成する場合、リソース グループの名前と場所を指定します。  

    ```
    PS C:\> New-AzureRmResourceGroup –Name "test-rg" –Location "West US"
    ```
3. **New-AzureRmRecoveryServicesVault** コマンドレットを使用して新しいコンテナーを作成します。 リソース グループに使用したのと同じコンテナーの場所を指定してください。

    ```
    PS C:\> New-AzureRmRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "West US"
    ```
4. 使用するストレージ冗長性の種類を指定します。[ローカル冗長ストレージ (LRS)](../storage/storage-redundancy.md#locally-redundant-storage) または [geo 冗長ストレージ (GRS)](../storage/storage-redundancy.md#geo-redundant-storage) を使用できます。 次に示す例では、testVault の -BackupStorageRedundancy オプションが GeoRedundant に設定されています。

   > [!TIP]
   > Azure Backup コマンドレットの多くは、入力として Recovery Services コンテナー オブジェクトを必要としています。 このため、Backup Recovery Services コンテナー オブジェクトを変数に格納すると便利です。
   >
   >

    ```
    PS C:\> $vault1 = Get-AzureRmRecoveryServicesVault –Name "testVault"
    PS C:\> Set-AzureRmRecoveryServicesBackupProperties  -vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

## <a name="view-the-vaults-in-a-subscription"></a>サブスクリプション内のコンテナーの表示
**Get-AzureRmRecoveryServicesVault** を使用して、現在のサブスクリプション内のすべてのコンテナーを一覧表示します。 このコマンドは、新しく作成したコンテナーを確認したり、サブスクリプション内の利用可能なコンテナーを確認したりするのに使用できます。

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


## <a name="installing-the-azure-backup-agent"></a>Microsoft Azure Backup エージェントのインストール
Microsoft Azure Backup エージェントをインストールする前に、Windows Server に、インストーラーをダウンロードする必要があります。 最新バージョンのインストーラーは、 [Microsoft ダウンロード センター](http://aka.ms/azurebackup_agent) または Recovery Services コンテナーの [ダッシュボード] ページから入手することができます。 インストーラーを、*C:\Downloads\* などの、簡単にアクセスできる場所に保存します。

エージェントをインストールするには、管理者特権の PowerShell コンソールで、次のコマンドを実行します。

```
PS C:\> MARSAgentInstaller.exe /q
```

これにより、エージェントはすべて既定のオプションが指定されてインストールされます。 インストールは、バックグラウンドで数分かかります。 */nu* オプションを指定しない場合、インストールの最後に **[Windows Update]** ウィンドウが開き、更新プログラムが確認されます。 インストールすると、エージェントが、インストールされているプログラムの一覧に表示されます。

インストールされているプログラムの一覧を表示するには、**[コントロール パネル]**  >  **[プログラム]**  >  **[プログラムと機能]** に移動します。

![インストールされているエージェント](./media/backup-client-automation/installed-agent-listing.png)

### <a name="installation-options"></a>インストール オプション
コマンドラインで利用可能なすべてのオプションを表示するには、次のコマンドを使用します。

```
PS C:\> MARSAgentInstaller.exe /?
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

```
PS C:\> $credspath = "C:\downloads"
PS C:\> $credsfilename = Get-AzureRmRecoveryServicesVaultSettingsFile -Backup -Vault $vault1 -Path  $credspath
PS C:\> $credsfilename C:\downloads\testvault\_Sun Apr 10 2016.VaultCredentials
```

Windows Server または Windows クライアント コンピューターで [Start-OBRegistration](https://technet.microsoft.com/library/hh770398%28v=wps.630%29.aspx) コマンドレットを実行し、コンピューターをコンテナーに登録します。

```
PS C:\> $cred = $credspath + $credsfilename
PS C:\> Start-OBRegistration-VaultCredentials $cred -Confirm:$false
CertThumbprint      :7a2ef2caa2e74b6ed1222a5e89288ddad438df2
SubscriptionID      : ef4ab577-c2c0-43e4-af80-af49f485f3d1
ServiceResourceName: testvault
Region              :West US
Machine registration succeeded.
```

> [!IMPORTANT]
> コンテナーの資格情報ファイルを指定する際には、相対パスは使用しないでください。 このコマンドレットへの入力には、絶対パスを指定する必要があります。
>
>

## <a name="networking-settings"></a>ネットワークの設定
プロキシ サーバーを介して Windows コンピューターをインターネットに接続した場合、プロキシ設定もエージェントに指定できます。 この例では、プロキシ サーバーがないため、プロキシ関連の情報はないことを明示的に示しています。

特定の曜日セットに対して、帯域幅の使用を、```work hour bandwidth```オプションと```non-work hour bandwidth```オプションで制御することもできます。

プロキシと帯域幅の詳細の設定は、 [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409%28v=wps.630%29.aspx) コマンドレットを使用して実行します。

```
PS C:\> Set-OBMachineSetting -NoProxy
Server properties updated successfully.

PS C:\> Set-OBMachineSetting -NoThrottle
Server properties updated successfully.
```

## <a name="encryption-settings"></a>暗号化の設定
データの機密性を保護するために、Microsoft Azure Backup に送信されるバックアップ データは暗号化されます。 暗号化パスフレーズは、復元時にデータの暗号化を解除するための "パスワード" になります。

```
PS C:\> ConvertTo-SecureString -String "Complex!123_STRING" -AsPlainText -Force | Set-OBMachineSetting
Server properties updated successfully
```

> [!IMPORTANT]
> 設定したら、パスフレーズ情報をセキュリティで保護された安全な場所に保管してください。 このパスフレーズがないと、Azure からデータを復元できません。
>
>

## <a name="back-up-files-and-folders"></a>ファイルとフォルダーのバックアップ
Windows Server およびクライアントから Azure Backup へのすべてのバックアップは、ポリシーによって管理されます。 ポリシーは&3; つの部分で構成されます。

1. **バックアップ スケジュール**。バックアップをいつ実行し、いつサービスと同期するかを指定します。
2. **保持スケジュール** は、Azure で回復ポイントを保持する期間を指定します。
3. **ファイル内包/除外仕様**。何をバックアップするかを指定します。

このドキュメントでは、バックアップを自動化するため、構成を何も行っていないことを前提としています。 まず、 [New-OBPolicy](https://technet.microsoft.com/library/hh770416.aspx) コマンドレットを使って新しいバックアップ ポリシーを作成します。

```
PS C:\> $newpolicy = New-OBPolicy
```

この時点でポリシーは空で、どの項目を含むまたは除外するか、またバックアップをいつ実行するか、どこに格納するかを定義する他のコマンドレットが必要です。

### <a name="configuring-the-backup-schedule"></a>バックアップ スケジュールの構成
ポリシーの 3 つの構成要素の最初はバックアップ スケジュールです。これは、[New-OBSchedule](https://technet.microsoft.com/library/hh770401) コマンドレットを使って作成します。 バックアップ スケジュールでは、バックアップをいつ実行するかを定義します。 スケジュールを作成するとき、次の 2 つの入力パラメーターを指定する必要があります。

* **曜日** 。 バックアップ ジョブは、週に&1; 回、毎日、またはこれらを組み合わせたさまざまな間隔で実行できます。
* **1 日のうちの時間帯** 。 1 日最大 3 回のバックアップを起動する時間を定義できます。

たとえば、毎週土曜日と日曜日の午後 4 時に実行されるようにバックアップ ポリシーを構成できます。

```
PS C:\> $sched = New-OBSchedule -DaysofWeek Saturday, Sunday -TimesofDay 16:00
```

バックアップ スケジュールはポリシーに関連付ける必要があります。これを行うには、[Set-OBSchedule](https://technet.microsoft.com/library/hh770407) コマンドレットを使用します。

```
PS C:> Set-OBSchedule -Policy $newpolicy -Schedule $sched
BackupSchedule : 4:00 PM Saturday, Sunday, Every 1 week(s) DsList : PolicyName : RetentionPolicy : State : New PolicyState : Valid
```
### <a name="configuring-a-retention-policy"></a>保有ポリシーの構成
保有ポリシーでは、バックアップ ジョブから作成した回復ポイントをどのくらいの期間保持するかを定義します。 [New-OBRetentionPolicy](https://technet.microsoft.com/library/hh770425) コマンドレットを使って新しい保有ポリシーを作成するとき、Microsoft Azure Backup でバックアップの回復ポイントを保持する日数を指定できます。 次の例では、7 日間の保有ポリシーを設定します。

```
PS C:\> $retentionpolicy = New-OBRetentionPolicy -RetentionDays 7
```

保有ポリシーは、コマンドレット [Set-OBRetentionPolicy](https://technet.microsoft.com/library/hh770405)を使用してメインのポリシーと関連付ける必要があります。

```
PS C:\> Set-OBRetentionPolicy -Policy $newpolicy -RetentionPolicy $retentionpolicy

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
```OBFileSpec``` オブジェクトにより、ファイルをバックアップに含めるか除外するかを定義します。 ここでは、コンピューター上の保護されたファイルとフォルダーを範囲から除外する一連のルールを示します。 ファイル内包または除外ルールは必要に応じていくつでも保持でき、ポリシーに関連付けることができます。 新しい OBFileSpec オブジェクトを作成して、次の操作を実行できます。

* 含めるファイルおよびフォルダーを指定
* 除外するファイルおよびフォルダーを指定
* フォルダー内のデータの再帰バックアップを指定。または、指定したフォルダー内の最上位レベルのファイルのみをバックアップするかどうかを指定。

後者は、New-OBFileSpec コマンドの NonRecursive フラグを使用して実行できます。

次の例では、ボリューム C: および D: をバックアップし、Windows フォルダーと任意の一時フォルダー内の OS バイナリを除外します。 このためには、 [New-OBFileSpec](https://technet.microsoft.com/library/hh770408) コマンドレットを使用して&2; つのファイル指定 (内包用と除外用にそれぞれ&1; つずつ) を作成します。 ファイル指定が作成されたら、 [Add-OBFileSpec](https://technet.microsoft.com/library/hh770424) コマンドレットを使用してポリシーに関連付けます。

```
PS C:\> $inclusions = New-OBFileSpec -FileSpec @("C:\", "D:\")

PS C:\> $exclusions = New-OBFileSpec -FileSpec @("C:\windows", "C:\temp") -Exclude

PS C:\> Add-OBFileSpec -Policy $newpolicy -FileSpec $inclusions

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


PS C:\> Add-OBFileSpec -Policy $newpolicy -FileSpec $exclusions

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

### <a name="applying-the-policy"></a>ポリシーの適用
これで、ポリシー オブジェクトが完了し、バックアップ スケジュール、保有ポリシー、およびファイルの包含/除外リストに関連付けられました。 次に、Microsoft Azure Backup で使用するためにこのポリシーをコミットできます。 新しく作成されたポリシーを適用する前に、 [Remove-OBPolicy](https://technet.microsoft.com/library/hh770415) コマンドレットを使用して、サーバーに関連付けられた既存のバックアップ ポリシーがないことを確認します。 ポリシーを削除すると確認のダイアログが表示されます。 確認をスキップするには、コマンドレットで ```-Confirm:$false``` フラグを使用します。

```
PS C:> Get-OBPolicy | Remove-OBPolicy
Microsoft Azure Backup Are you sure you want to remove this backup policy? This will delete all the backed up data. [Y] Yes [A] Yes to All [N] No [L] No to All [S] Suspend [?] Help (default is "Y"):
```

[Set-OBPolicy](https://technet.microsoft.com/library/hh770421) コマンドレットで、ポリシー オブジェクトのコミットを実行します。 ここでも、確認のダイアログが表示されます。 確認をスキップするには、コマンドレットで ```-Confirm:$false``` フラグを使用します。

```
PS C:> Set-OBPolicy -Policy $newpolicy
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

```
PS C:> Get-OBPolicy | Get-OBSchedule
SchedulePolicyName : 71944081-9950-4f7e-841d-32f0a0a1359a
ScheduleRunDays : {Saturday, Sunday}
ScheduleRunTimes : {16:00:00}
State : Existing

PS C:> Get-OBPolicy | Get-OBRetentionPolicy
RetentionDays : 7
RetentionPolicyName : ca3574ec-8331-46fd-a605-c01743a5265e
State : Existing

PS C:> Get-OBPolicy | Get-OBFileSpec
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

```
PS C:> Get-OBPolicy | Start-OBBackup
Taking snapshot of volumes...
Preparing storage...
Estimating size of backup items...
Estimating size of backup items...
Transferring data...
Verifying backup...
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

```
PS C:> $source = Get-OBRecoverableSource
PS C:> $source
FriendlyName : C:\
RecoverySourceName : C:\
ServerName : myserver.microsoft.com

FriendlyName : D:\
RecoverySourceName : D:\
ServerName : myserver.microsoft.com
```

### <a name="choosing-a-backup-point-to-restore"></a>バックアップの復元ポイントの選択
バックアップ ポイントの一覧は、 [Get-OBRecoverableItem](https://technet.microsoft.com/library/hh770399.aspx) コマンドレットと適切なパラメーターを実行して取得できます。 この例では、ソース ボリューム *D:* の最新のバックアップ ポイントを選択し、これを使用して特定のファイルを復旧します。

```
PS C:> $rps = Get-OBRecoverableItem -Source $source[1]
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
オブジェクト ```$rps``` は、バックアップ ポイントの配列です。 最初の要素は最新のポイントで、N 番目の要素は最も古いポイントです。 最新のポイントを選択するには、 ```$rps[0]```を使用します。

### <a name="choosing-an-item-to-restore"></a>復元する項目の選択
復元する正確なファイルまたはフォルダーを特定するには、 [Get-OBRecoverableItem](https://technet.microsoft.com/library/hh770399.aspx) コマンドレットを再帰的に使用します。 ここでは、フォルダー階層を ```Get-OBRecoverableItem```のみを使用して参照できます。

この例では、ファイル *finances.xls* を復元するには、オブジェクト ```$filesFolders[1]``` を使用してこれを参照できます。

```
PS C:> $filesFolders = Get-OBRecoverableItem $rps[0]
PS C:> $filesFolders
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

PS C:> $filesFolders = Get-OBRecoverableItem $filesFolders[0]
PS C:> $filesFolders
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

```
PS C:\> $item = Get-OBRecoverableItem -RecoveryPoint $rps[0] -Location "D:\MyData" -SearchString "finance*"
```

### <a name="triggering-the-restore-process"></a>復元プロセスのトリガー
復元プロセスをトリガーするには、まず、回復オプションを指定する必要があります。 これを行うには、 [New-OBRecoveryOption](https://technet.microsoft.com/library/hh770417.aspx) コマンドレットを使用します。 この例では、ファイルを *C:\temp* に復元すると仮定します。 また、宛先フォルダー *C:\temp* に既に存在するファイルをスキップすると仮定します。 こうした回復オプションを作成するには、次のコマンドを使用します。

```
PS C:\> $recovery_option = New-OBRecoveryOption -DestinationPath "C:\temp" -OverwriteType Skip
```

次に、```$item``` コマンドレットの出力から選択した ```Get-OBRecoverableItem``` で [Start-OBRecovery](https://technet.microsoft.com/library/hh770402.aspx) コマンドを使用して復元をトリガーします。

```
PS C:\> Start-OBRecovery -RecoverableItem $item -RecoveryOption $recover_option
Estimating size of backup items...
Estimating size of backup items...
Estimating size of backup items...
Estimating size of backup items...
Job completed.
The recovery operation completed successfully.
```


## <a name="uninstalling-the-azure-backup-agent"></a>Microsoft Azure Backup エージェントのアンインストール
Microsoft Azure Backup エージェントのアンインストールは、次のコマンドを使用して実行できます。

```
PS C:\> .\MARSAgentInstaller.exe /d /q
```

エージェント バイナリをコンピューターからアンインストールすると、考慮すべき別の結果が生じます。

* ファイル フィルターがコンピューターから削除され、変更の追跡が停止されます。
* すべてのポリシー情報がコンピューターから削除されますが、ポリシー情報は引き続きサービスに格納されます。
* すべてのバックアップ スケジュールが削除され、以降のバックアップは実施されません。

ただし、Azure に格納されたデータは、設定した保有ポリシーに基づいて維持されます。 古いポイントの期限は自動的に切れます。

## <a name="remote-management"></a>リモート管理
Microsoft Azure Backup エージェント、ポリシー、およびデータ ソースに関する管理はすべて、PowerShell を使ってリモートで実行できます。 リモートで管理されるコンピューターは、適切に準備されている必要があります。

既定では、WinRM サービスは手動で開始されるように設定されています。 スタートアップの種類は "*自動*" に設定する必要があります。これにより、サービスが開始されます。 WinRM サービスが実行していることを確認するには、Status プロパティの値が *[実行中]* になっていることを確認します。

```
PS C:\> Get-Service WinRM

Status   Name               DisplayName
------   ----               -----------
Running  winrm              Windows Remote Management (WS-Manag...
```

リモート処理用に PowerShell を構成します。

```
PS C:\> Enable-PSRemoting -force
WinRM is already set up to receive requests on this computer.
WinRM has been updated for remote management.
WinRM firewall exception enabled.

PS C:\> Set-ExecutionPolicy unrestricted -force
```

コンピューターを、エージェントのインストールを始め、リモートで管理できるようになりました。 たとえば、次のスクリプトは、エージェントをリモート コンピューターにコピーし、インストールします。

```
PS C:\> $dloc = "\\REMOTESERVER01\c$\Windows\Temp"
PS C:\> $agent = "\\REMOTESERVER01\c$\Windows\Temp\MARSAgentInstaller.exe"
PS C:\> $args = "/q"
PS C:\> Copy-Item "C:\Downloads\MARSAgentInstaller.exe" -Destination $dloc - force

PS C:\> $s = New-PSSession -ComputerName REMOTESERVER01
PS C:\> Invoke-Command -Session $s -Script { param($d, $a) Start-Process -FilePath $d $a -Wait } -ArgumentList $agent $args
```

## <a name="next-steps"></a>次のステップ
Azure Backup for Windows Server/Client の詳細については、以下を参照してください。

* [Azure Backup の概要](backup-introduction-to-azure-backup.md)
* [Windows Server のバックアップ](backup-configure-vault.md)



<!--HONumber=Jan17_HO4-->


