---
title: PowerShell を使用して Windows Server を Azure にバックアップする
description: この記事では、PowerShell を使用して Windows Server または Windows クライアント上に Azure Backup を設定したり、バックアップと回復を管理したりする方法について説明します。
ms.topic: conceptual
ms.date: 12/2/2019
ms.openlocfilehash: 582d8123f16b2d5a543d862b8eb3e45895087e4a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "90987108"
---
# <a name="deploy-and-manage-backup-to-azure-for-windows-serverwindows-client-using-powershell"></a>PowerShell を使用して Windows Server/Windows Client に Microsoft Azure Backup をデプロイおよび管理する手順

この記事では、PowerShell を使用して Windows Server または Windows クライアント上に Azure Backup を設定したり、バックアップと回復を管理したりする方法について説明します。

## <a name="install-azure-powershell"></a>Azure PowerShell をインストールする

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

開始するには、[PowerShell の最新リリースをインストール](/powershell/azure/install-az-ps)します。

## <a name="create-a-recovery-services-vault"></a>Recovery Services コンテナーを作成する

次の手順では、Recovery Services コンテナーの作成について説明します。 Recovery Services コンテナーは Backup コンテナーとは異なります。

1. Azure Backup を初めて使用する場合、**Register-AzResourceProvider** コマンドレットを使って Azure Recovery Services プロバイダーをサブスクリプションに登録する必要があります。

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

2. Recovery Services コンテナーは Azure Resource Manager リソースであるため、リソース グループ内に配置する必要があります。 既存のリソース グループを使用することも、新しいリソース グループを作成することもできます。 新しいリソース グループを作成する場合、リソース グループの名前と場所を指定します。  

    ```powershell
    New-AzResourceGroup –Name "test-rg" –Location "WestUS"
    ```

3. **New-AzRecoveryServicesVault** コマンドレットを使用して、新しいコンテナーを作成します。 リソース グループに使用したのと同じコンテナーの場所を指定してください。

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "WestUS"
    ```

4. 使用するストレージ冗長性の種類を指定します。 [ローカル冗長ストレージ (LRS)](../storage/common/storage-redundancy.md#locally-redundant-storage)、[geo 冗長ストレージ (GRS)](../storage/common/storage-redundancy.md#geo-redundant-storage)、または [ゾーン冗長ストレージ (ZRS)](../storage/common/storage-redundancy.md#zone-redundant-storage) を使用できます。 次に示す例では、*testVault* の **-BackupStorageRedundancy** オプションが **GeoRedundant** に設定されています。

   > [!TIP]
   > Azure Backup コマンドレットの多くは、入力として Recovery Services コンテナー オブジェクトを必要としています。 このため、Backup Recovery Services コンテナー オブジェクトを変数に格納すると便利です。
   >
   >

    ```powershell
    $Vault1 = Get-AzRecoveryServicesVault –Name "testVault"
    Set-AzRecoveryServicesBackupProperties -Vault $Vault1 -BackupStorageRedundancy GeoRedundant
    ```

## <a name="view-the-vaults-in-a-subscription"></a>サブスクリプション内のコンテナーの表示

**Get-AzRecoveryServicesVault** を使用して、現在のサブスクリプション内にあるすべてのコンテナーを一覧表示します。 このコマンドを使用すると、新しいコンテナーが作成されたことを確認したり、サブスクリプション内のどのコンテナーが利用できるかを調べたりすることができます。

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

Microsoft Azure Backup エージェントをインストールする前に、Windows Server に、インストーラーをダウンロードする必要があります。 最新バージョンのインストーラーは、 [Microsoft ダウンロード センター](https://aka.ms/azurebackup_agent) または Recovery Services コンテナーの [ダッシュボード] ページから入手することができます。 インストーラーを、`C:\Downloads\*` などの、簡単にアクセスできる場所に保存します。

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

これにより、エージェントはすべて既定のオプションが指定されてインストールされます。 インストールは、バックグラウンドで数分かかります。 */nu* オプションを指定しない場合は、インストールの最後に **[Windows Update]** ウィンドウが開き、更新プログラムがないかどうかが確認されます。 インストールすると、エージェントが、インストールされているプログラムの一覧に表示されます。

インストールされているプログラムの一覧を表示するには、 **[コントロール パネル]**  >  **[プログラム]**  >  **[プログラムと機能]** に移動します。

![インストールされているエージェント](./media/backup-client-automation/installed-agent-listing.png)

### <a name="installation-options"></a>インストール オプション

コマンド ラインで使用できるすべてのオプションを表示するには、次のコマンドを使用します。

```powershell
MARSAgentInstaller.exe /?
```

利用可能なオプションは、次のとおりです。

| オプション | 詳細 | Default |
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

### <a name="registering-using-the-ps-az-module"></a>PS Az モジュールを使用して登録する

> [!NOTE]
> コンテナー証明書の生成に関するバグは、Az 3.5.0 リリースで修正されています。 コンテナー証明書をダウンロードするには、Az 3.5.0 リリース バージョン以上を使用してください。

PowerShell の最新の Az モジュールでは、基になるプラットフォームの制限のために、コンテナーの資格情報をダウンロードするには自己署名証明書が必要になります。 自己署名証明書を指定してコンテナーの資格情報をダウンロードする例を次に示します。

```powershell
$dt = $(Get-Date).ToString("M-d-yyyy")
$cert = New-SelfSignedCertificate -CertStoreLocation Cert:\CurrentUser\My -FriendlyName 'test-vaultcredentials' -subject "Windows Azure Tools" -KeyExportPolicy Exportable -NotAfter $(Get-Date).AddHours(48) -NotBefore $(Get-Date).AddHours(-24) -KeyProtection None -KeyUsage None -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2") -Provider "Microsoft Enhanced Cryptographic Provider v1.0"
$certficate = [convert]::ToBase64String($cert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pfx))
$CredsFilename = Get-AzRecoveryServicesVaultSettingsFile -Backup -Vault $Vault -Path $CredsPath -Certificate $certficate
```

Windows Server または Windows クライアント コンピューターで [Start-OBRegistration](/powershell/module/msonlinebackup/start-obregistration) コマンドレットを実行し、コンピューターをコンテナーに登録します。
これと、バックアップに使用されるその他のコマンドレットは、インストール プロセスの一部として MARS AgentInstaller で追加された MSONLINE モジュールに含まれています。

このエージェント インストーラーでは、$Env:PSModulePath 変数が更新されません。 つまり、モジュールの自動読み込みに失敗します。 これを解決するために、次を実行できます。

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

プロキシ サーバーを介して Windows コンピューターをインターネットに接続した場合、プロキシ設定もエージェントに指定できます。 この例では、プロキシ サーバーがないため、プロキシ関連の情報を明示的にクリアしています。

特定の曜日セットに対して、帯域幅の使用を、`work hour bandwidth`オプションと`non-work hour bandwidth`オプションで制御することもできます。

プロキシと帯域幅の詳細の設定は、 [Set-OBMachineSetting](/powershell/module/msonlinebackup/set-obmachinesetting) コマンドレットを使用して実行します。

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

Azure portal の **[Recovery Services コンテナー]** セクションにある **[設定]**  >  **[プロパティ]**  >  **[セキュリティ PIN]** 下の **[生成]** を選択して、セキュリティ PIN を生成する必要があります。

>[!NOTE]
> セキュリティ PIN は、Azure portal でのみ生成できます。

その後、コマンド内で `generatedPIN` としてこれを使用します。

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

Windows Server およびクライアントから Azure Backup へのすべてのバックアップは、ポリシーによって管理されます。 ポリシーには、次の 3 つの部分があります。

1. **バックアップ スケジュール**。バックアップをいつ実行し、いつサービスと同期するかを指定します。
2. **保持スケジュール** は、Azure で回復ポイントを保持する期間を指定します。
3. **ファイル内包/除外仕様**。何をバックアップするかを指定します。

このドキュメントでは、バックアップを自動化するため、構成を何も行っていないことを前提としています。 まず、 [New-OBPolicy](/powershell/module/msonlinebackup/new-obpolicy) コマンドレットを使って新しいバックアップ ポリシーを作成します。

```powershell
$NewPolicy = New-OBPolicy
```

この時点でポリシーは空であり、どの項目を含めるか、どの項目を除外するか、いつバックアップを実行するか、どこにバックアップを格納するかを定義するために、他のコマンドレットが必要です。

### <a name="configuring-the-backup-schedule"></a>バックアップ スケジュールの構成

ポリシーの 3 つの部分の最初は、[New-OBSchedule](/powershell/module/msonlinebackup/new-obschedule) コマンドレットを使用して作成されるバックアップ スケジュールです。 バックアップ スケジュールでは、バックアップをいつ実行するかを定義します。 スケジュールを作成するときは、次の 2 つの入力パラメーターを指定する必要があります。

* **曜日** 。 バックアップ ジョブは、週に 1 回、毎日、またはこれらを組み合わせたさまざまな間隔で実行できます。
* **1 日のうちの時間帯** 。 バックアップがトリガーされる最大 3 つの異なる時間帯を定義できます。

たとえば、毎週土曜日と日曜日の午後 4 時に実行されるようにバックアップ ポリシーを構成できます。

```powershell
$Schedule = New-OBSchedule -DaysOfWeek Saturday, Sunday -TimesOfDay 16:00
```

バックアップ スケジュールはポリシーに関連付ける必要があります。これを行うには、[Set-OBSchedule](/powershell/module/msonlinebackup/set-obschedule) コマンドレットを使用します。

```powershell
Set-OBSchedule -Policy $NewPolicy -Schedule $Schedule
```

```Output
BackupSchedule : 4:00 PM Saturday, Sunday, Every 1 week(s) DsList : PolicyName : RetentionPolicy : State : New PolicyState : Valid
```

### <a name="configuring-a-retention-policy"></a>保有ポリシーの構成

保有ポリシーでは、バックアップ ジョブから作成した回復ポイントをどのくらいの期間保持するかを定義します。 [New-OBRetentionPolicy](/powershell/module/msonlinebackup/new-obretentionpolicy) コマンドレットを使って新しいアイテム保持ポリシーを作成するとき、Azure Backup でバックアップの回復ポイントを保持する日数を指定できます。 次の例では、7 日間の保有ポリシーを設定します。

```powershell
$RetentionPolicy = New-OBRetentionPolicy -RetentionDays 7
```

保有ポリシーは、コマンドレット [Set-OBRetentionPolicy](/powershell/module/msonlinebackup/set-obretentionpolicy)を使用してメインのポリシーと関連付ける必要があります。

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

次の例では、ボリューム C: および D: をバックアップし、Windows フォルダーと任意の一時フォルダー内の OS バイナリを除外します。 それを行うには、[New-OBFileSpec](/powershell/module/msonlinebackup/new-obfilespec) コマンドレットを使用して 2 つのファイル指定 (包含用に 1 つと除外用に 1 つ) を作成します。 ファイル指定が作成されたら、 [Add-OBFileSpec](/powershell/module/msonlinebackup/add-obfilespec) コマンドレットを使用してポリシーに関連付けます。

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

### <a name="applying-the-policy"></a>ポリシーの適用

これで、ポリシー オブジェクトが完了し、バックアップ スケジュール、保有ポリシー、およびファイルの包含/除外リストに関連付けられました。 次に、Microsoft Azure Backup で使用するためにこのポリシーをコミットできます。 新しく作成されたポリシーを適用する前に、[Remove-OBPolicy](/powershell/module/msonlinebackup/remove-obpolicy) コマンドレットを使用して、サーバーに関連付けられた既存のバックアップ ポリシーが存在しないことを確認してください。 ポリシーを削除すると確認のダイアログが表示されます。 確認をスキップするには、コマンドレットで `-Confirm:$false` フラグを使用します。

```powershell
Get-OBPolicy | Remove-OBPolicy
```

```Output
Microsoft Azure Backup Are you sure you want to remove this backup policy? This will delete all the backed up data. [Y] Yes [A] Yes to All [N] No [L] No to All [S] Suspend [?] Help (default is "Y"):
```

[Set-OBPolicy](/powershell/module/msonlinebackup/set-obpolicy) コマンドレットで、ポリシー オブジェクトのコミットを実行します。 ここでも、確認のダイアログが表示されます。 確認をスキップするには、コマンドレットで `-Confirm:$false` フラグを使用します。

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

既存のバックアップ ポリシーの詳細を表示するには、 [Get-OBPolicy](/powershell/module/msonlinebackup/get-obpolicy) コマンドレットを使用します。 バックアップ スケジュール用の [Get-OBSchedule](/powershell/module/msonlinebackup/get-obschedule) コマンドレット、リテンション期間ポリシー用の [Get-OBRetentionPolicy](/powershell/module/msonlinebackup/get-obretentionpolicy) コマンドレットを使用してさらにドリルダウンできます。

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

### <a name="performing-an-on-demand-backup"></a>オンデマンド バックアップの実行

バックアップ ポリシーが設定されると、スケジュールに従ってバックアップが実行されます。 [Start-OBBackup](/powershell/module/msonlinebackup/start-obbackup) コマンドレットを使って、オンデマンド バックアップを起動することもできます。

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

## <a name="back-up-windows-server-system-state-in-mars-agent"></a>MARS エージェントでの Windows Server のシステム状態のバックアップ

このセクションでは、MARS エージェントでシステムの状態を設定する PowerShell コマンドについて説明します

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

## <a name="restore-data-from-azure-backup"></a>Microsoft Azure Backup からのデータの復元

このセクションでは、Microsoft Azure Backup からのデータの復元を自動化する方法を手順に従って説明します。 次の手順を実行します。

1. ソース ボリュームを選択する
2. バックアップの復元ポイントを選択する
3. 復元する項目を選指定する
4. 復元プロセスをトリガーする

### <a name="picking-the-source-volume"></a>ソース ボリュームの選択

Azure Backup から項目を復元するには、まず、項目のソースを特定する必要があります。 Windows サーバーまたは Windows クライアントのコンテキストでコマンドを実行するため、コンピューターは既に特定されています。 ソースを特定する次の手順は、それを含むボリュームを特定することです。 該当のコンピューターからバックアップするボリュームまたはソースの一覧は、 [Get-OBRecoverableSource](/powershell/module/msonlinebackup/get-obrecoverablesource) コマンドレットを実行して取得できます。 このコマンドは、このサーバー/クライアントでバックアップされるすべてのソースの配列を返します。

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

バックアップ ポイントの一覧は、[Get-OBRecoverableItem](/powershell/module/msonlinebackup/get-obrecoverableitem) コマンドレットと適切なパラメーターを実行して取得できます。 この例では、ソース ボリューム *C:* の最新のバックアップ ポイントを選択し、これを使用して特定のファイルを復旧します。

```powershell
$Rps = Get-OBRecoverableItem $Source[0]
$Rps
```

```Output

IsDir                : False
ItemNameFriendly     : C:\
ItemNameGuid         : \\?\Volume{297cbf7a-0000-0000-0000-401f00000000}\
LocalMountPoint      : C:\
MountPointName       : C:\
Name                 : C:\
PointInTime          : 10/17/2019 7:52:13 PM
ServerName           : myserver.microsoft.com
ItemSize             :
ItemLastModifiedTime :

IsDir                : False
ItemNameFriendly     : C:\
ItemNameGuid         : \\?\Volume{297cbf7a-0000-0000-0000-401f00000000}\
LocalMountPoint      : C:\
MountPointName       : C:\
Name                 : C:\
PointInTime          : 10/16/2019 7:00:19 PM
ServerName           : myserver.microsoft.com
ItemSize             :
ItemLastModifiedTime :
```

オブジェクト `$Rps` は、バックアップ ポイントの配列です。 最初の要素は最新のポイントで、N 番目の要素は最も古いポイントです。 最新のポイントを選択するには、`$Rps[0]` を使用します。

### <a name="specifying-an-item-to-restore"></a>復元する項目の指定

特定のファイルを復元するには、ルートボリュームに対して相対的なファイル名を指定します。 たとえば、C:\Test\Cat.job を取得するには、次のコマンドを実行します。

```powershell
$Item = New-OBRecoverableItem $Rps[0] "Test\cat.jpg" $FALSE
$Item
```

```Output
IsDir                : False
ItemNameFriendly     : C:\Test\cat.jpg
ItemNameGuid         :
LocalMountPoint      : C:\
MountPointName       : C:\
Name                 : cat.jpg
PointInTime          : 10/17/2019 7:52:13 PM
ServerName           : myserver.microsoft.com
ItemSize             :
ItemLastModifiedTime : 21-Jun-14 6:43:02 AM

```

### <a name="triggering-the-restore-process"></a>復元プロセスのトリガー

復元プロセスをトリガーするには、まず、回復オプションを指定する必要があります。 これを行うには、 [New-OBRecoveryOption](/powershell/module/msonlinebackup/new-obrecoveryoption) コマンドレットを使用します。 この例では、ファイルを *C:\temp* に復元すると仮定します。また、宛先フォルダー *C:\temp* に既に存在するファイルをスキップすると仮定します。こうした回復オプションを作成するには、次のコマンドを使用します。

```powershell
$RecoveryOption = New-OBRecoveryOption -DestinationPath "C:\temp" -OverwriteType Skip
```

次に、`Get-OBRecoverableItem` コマンドレットの出力から選択した `$Item` で [Start-OBRecovery](/powershell/module/msonlinebackup/start-obrecovery) コマンドを使用して復元プロセスをトリガーします。

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

## <a name="next-steps"></a>次のステップ

Windows Server/Windows クライアント用の Azure Backup の詳細については、次を参照してください。

* [Azure Backup の概要](./backup-overview.md)
* [Windows Server のバックアップ](backup-windows-with-mars-agent.md)
