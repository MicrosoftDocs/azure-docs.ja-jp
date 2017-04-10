---
title: "Azure Backup: PowerShell を使用して DPM ワークロードをバックアップする | Microsoft Docs"
description: "PowerShell を使用して、Data Protection Manager (DPM) 用に Microsoft Azure Backup をデプロイおよび管理する手順の説明"
services: backup
documentationcenter: 
author: Nkolli1
manager: shreeshd
editor: 
ms.assetid: bcbcef79-9d33-4e84-a558-9866614f2cae
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/23/2017
ms.author: nkolli;trinadhk;anuragm;markgal
translationtype: Human Translation
ms.sourcegitcommit: 127484103706be5acd0f988aee3d13217d4d87f2
ms.openlocfilehash: f73bdcf056dc745f9f40e96d3dc51e5e4b88f77d


---
# <a name="deploy-and-manage-backup-to-azure-for-data-protection-manager-dpm-servers-using-powershell"></a>PowerShell を使用して Data Protection Manager (DPM) サーバーに Microsoft Azure Backup をデプロイおよび管理する手順
> [!div class="op_single_selector"]
> * [ARM](backup-dpm-automation.md)
> * [クラシック](backup-dpm-automation-classic.md)
>
>

この記事では、PowerShell を使用して、DPM サーバー上に Microsoft Azure Backup をセットアップし、バックアップと回復を管理する方法を示します。

## <a name="setting-up-the-powershell-environment"></a>PowerShell 環境のセットアップ
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]

PowerShell を使用して Data Protection Manager から Azure へのバックアップを管理する前に、PowerShell に正しい環境があることを確認してください。 PowerShell セッションの開始時に、必ず次のコマンドレットを実行して適切なモジュールをインポートし、DPM コマンドレットを適切に参照できるようにしてください。

```
PS C:> & "C:\Program Files\Microsoft System Center 2012 R2\DPM\DPM\bin\DpmCliInitScript.ps1"

Welcome to the DPM Management Shell!

Full list of cmdlets: Get-Command
Only DPM cmdlets: Get-DPMCommand
Get general help: help
Get help for a cmdlet: help <cmdlet-name> or <cmdlet-name> -?
Get definition of a cmdlet: Get-Command <cmdlet-name> -Syntax
Sample DPM scripts: Get-DPMSampleScript
```

## <a name="setup-and-registration"></a>セットアップと登録
開始するには

1. [最新の PowerShell](https://github.com/Azure/azure-powershell/releases) (1.0.0 以降のバージョンが必要) をダウンロードします。
2. *Switch-azuremode* コマンドレットを使用して、 **AzureResourceManager** モードに切り替えて Azure Backup コマンドレットを使用可能にします。

```
PS C:\> Switch-AzureMode AzureResourceManager
```

PowerShell を使用して次のセットアップおよび登録タスクを自動化できます。

* バックアップ資格情報コンテナーの作成
* Microsoft Azure Backup エージェントのインストール
* Microsoft Azure Backup サービスへの登録
* ネットワークの設定
* 暗号化の設定

### <a name="create-a-backup-vault"></a>バックアップ資格情報コンテナーの作成
> [!WARNING]
> 顧客が初めて Azure Backup を使用する場合、サブスクリプションで使用する Azure Backup プロバイダーを登録する必要があります。 これは、Register-AzureProvider -ProviderNamespace "Microsoft.Backup" コマンドを実行して行うことができます。
>
>

**New-AzureRMBackupVault** コマンドレットを使用すると、新しいバックアップ コンテナーを作成できます。 バックアップ コンテナーは ARM リソースであるため、リソース グループ内に配置する必要があります。 管理者特権の Azure PowerShell コンソールで、次のコマンドを実行します。

```
PS C:\> New-AzureResourceGroup –Name “test-rg” -Region “West US”
PS C:\> $backupvault = New-AzureRMBackupVault –ResourceGroupName “test-rg” –Name “test-vault” –Region “West US” –Storage GRS
```

**Get-AzureRMBackupVault** コマンドレットを使用して、特定のサブスクリプション内のすべてのバックアップ コンテナーの一覧を取得できます。

### <a name="installing-the-azure-backup-agent-on-a-dpm-server"></a>DPM サーバーへの Microsoft Azure Backup エージェントのインストール
Microsoft Azure Backup エージェントをインストールする前に、Windows Server に、インストーラーをダウンロードする必要があります。 最新バージョンのインストーラーは、 [Microsoft ダウンロード センター](http://aka.ms/azurebackup_agent) またはバックアップ コンテナーの [ダッシュボード] ページから入手することができます。 インストーラーを、*C:\Downloads\* などの、簡単にアクセスできる場所に保存します。

エージェントをインストールするには、 **DPM サーバー**の管理者特権の PowerShell コンソールで、次のコマンドを実行します。

```
PS C:\> MARSAgentInstaller.exe /q
```

これにより、エージェントはすべて既定のオプションが指定されてインストールされます。 インストールは、バックグラウンドで数分かかります。 */nu* オプションを指定しない場合、インストールの最後に **[Windows Update]** ウィンドウが開き、更新プログラムが確認されます。

エージェントが、インストールされているプログラムの一覧に表示されます。 インストールされているプログラムの一覧を表示するには、**[コントロール パネル]**  >  **[プログラム]**  >  **[プログラムと機能]** に移動します。

![インストールされているエージェント](./media/backup-dpm-automation/installed-agent-listing.png)

#### <a name="installation-options"></a>インストール オプション
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

### <a name="registering-with-the-azure-backup-service"></a>Microsoft Azure Backup サービスへの登録
Microsoft Azure Backup サービスへの登録を実行する前に、 [前提条件](backup-azure-dpm-introduction.md) が満たされていることを確認する必要があります。 前提条件は、以下のとおりです。

* 有効な Azure サブスクリプションがあること
* バックアップ コンテナーがあること

コンテナーの資格情報をダウンロードするには、Azure PowerShell コンソールで **Get-AzureBackupVaultCredentials** コマンドレットを実行し、*C:\Downloads\* などのアクセスしやすい場所に保管します。

```
PS C:\> $credspath = "C:\"
PS C:\> $credsfilename = Get-AzureRMBackupVaultCredentials -Vault $backupvault -TargetLocation $credspath
PS C:\> $credsfilename
f5303a0b-fae4-4cdb-b44d-0e4c032dde26_backuprg_backuprn_2015-08-11--06-22-35.VaultCredentials
```

コンテナーへのマシンの登録は、 [Start-DPMCloudRegistration](https://technet.microsoft.com/library/jj612787) コマンドレットを使用して実行します。

```
PS C:\> $cred = $credspath + $credsfilename
PS C:\> Start-DPMCloudRegistration -DPMServerName "TestingServer" -VaultCredentialsFilePath $cred
```

これにより、"TestingServer" という名前の DPM サーバーは、Microsoft Azure Backup コンテナーに、指定されたコンテナーの資格情報を使用して登録されます。

> [!IMPORTANT]
> コンテナーの資格情報ファイルを指定する際には、相対パスは使用しないでください。 このコマンドレットへの入力には、絶対パスを指定する必要があります。
>
>

### <a name="initial-configuration-settings"></a>初期構成の設定
DPM サーバーが Azure Backup 資格情報コンテナーに登録されると、既定のサブスクリプション設定で起動されます。 これらのサブスクリプションの設定には、ネットワーク、暗号化、ステージング領域が含まれます。 サブスクリプション設定の変更を開始するには、まず既存 (既定) の設定のハンドルを [Get-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612793) コマンドレットを使用して取得する必要があります。

```
$setting = Get-DPMCloudSubscriptionSetting -DPMServerName "TestingServer"
```

このローカル PowerShell オブジェクト ```$setting``` にすべての変更が行われ、[Set-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612791) コマンドレットを使用して保存するために、完全なオブジェクトが DPM および Azure Backup にコミットされます。 変更が保持されていることを確認するには、 ```–Commit``` フラグを使用します。 コミットしない限り、Azure Backup により設定が適用または使用されません。

```
PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -Commit
```

### <a name="networking"></a>ネットワーク
インターネット上の DPM コンピューターの Azure Backup サービスの接続にプロキシ サーバーを使用している場合、続行するには、プロキシ サーバーの設定を指定する必要があります。 これを行うには、```-ProxyServer```、```-ProxyPort```、```-ProxyUsername```、および ```ProxyPassword``` パラメーターと [Set-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612791) コマンドレットを使用します。 この例では、プロキシ サーバーがないため、プロキシ関連の情報はないことを明示的に示しています。

```
PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -NoProxy
```

特定の曜日セットに対して、帯域幅の使用を、```-WorkHourBandwidth```オプションと```-NonWorkHourBandwidth```オプションで制御することもできます。 この例ではスロットルは設定しません。

```
PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -NoThrottle
```

### <a name="configuring-the-staging-area"></a>ステージング領域の構成
DPM サーバーで実行されている Microsoft Azure Backup エージェントには、クラウドから復元されるデータ用の一時的なストレージ (ローカル ステージング領域) が必要です。 [Set-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612791) コマンドレットと ```-StagingAreaPath``` パラメーターを使用してステージング領域を構成します。

```
PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -StagingAreaPath "C:\StagingArea"
```

上の例では、ステージング領域は PowerShell オブジェクト ```$setting``` の *C:\StagingArea* に設定されます。 指定したフォルダーが既に存在することを確認します。存在しない場合は、サブスクリプション設定の最終コミットが失敗します。

### <a name="encryption-settings"></a>暗号化の設定
データの機密性を保護するために、Microsoft Azure Backup に送信されるバックアップ データは暗号化されます。 暗号化パスフレーズは、復元時にデータの暗号化を解除するための "パスワード" になります。 設定したら、この情報をセキュリティで保護された安全な場所に保管することが重要です。

次の例では、最初のコマンドは、文字列 ```passphrase123456789``` をセキュリティ保護された文字列に変換し、そのセキュリティ保護された文字列を ```$Passphrase``` という名前の変数に割り当てます。 次のコマンドは、セキュリティ保護された文字列を ```$Passphrase``` にバックアップ暗号化のパスワードとして設定します。

```
PS C:\> $Passphrase = ConvertTo-SecureString -string "passphrase123456789" -AsPlainText -Force

PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -EncryptionPassphrase $Passphrase
```

> [!IMPORTANT]
> 設定したら、パスフレーズ情報をセキュリティで保護された安全な場所に保管してください。 このパスフレーズがないと、Azure からデータを復元できません。
>
>

この時点で、必要な変更はすべて ```$setting``` オブジェクトに行われています。 変更を忘れずにコミットします。

```
PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -Commit
```

## <a name="protect-data-to-azure-backup"></a>Microsoft Azure Backup へのデータの保護
このセクションでは、運用サーバーを DPM に追加し、ローカルの DPM 記憶域、Azure Backup の順にデータを保護します。 この例では、ファイルとフォルダーをバックアップする方法を示します。 このロジックは、DPM がサポートされたすべてのデータ ソースのバックアップに簡単に拡張して適用できます。 すべての DPM バックアップは、4 つの部分で保護グループ (PG) によって制御されます。

1. **グループ メンバー** は、同じ保護グループ内で保護するすべての保護可能オブジェクト (DPM では *データソース* とも呼ばれます) の一覧です。 たとえば、運用 VM とテスト SQL Server データベースがそれぞれ異なるバックアップ要件を持つため、別々の PG で保護したいという場合があります。 運用サーバー上のデータソースをバックアップする前に、DPM エージェントがサーバーにインストールされており、DPM により管理されていることを確認する必要があります。 [DPM エージェントをインストールする](https://technet.microsoft.com/library/bb870935.aspx) 手順と、適切な DPM サーバーにリンクする手順に従います。
2. **データ保護方法** では、目的のバックアップ場所 (テープ、ディスク、クラウドなど) を指定します。 この例では、ローカル ディスクとクラウドへのデータを保護します。
3. **バックアップ スケジュール** は、バックアップを実行する必要があるタイミングと、DPM サーバーと運用サーバーの間でデータを同期すべき頻度を指定します。
4. **保持スケジュール** は、Azure で回復ポイントを保持する期間を指定します。

### <a name="creating-a-protection-group"></a>保護グループの作成
まず新しい PG を、 [New-DPMProtectionGroup](https://technet.microsoft.com/library/hh881722) コマンドレットを使用して作成します。

```
PS C:\> $PG = New-DPMProtectionGroup -DPMServerName " TestingServer " -Name "ProtectGroup01"
```

前記のコマンドレットは、 *ProtectGroup01*という名前の保護グループを作成します。 既存の保護グループを後で変更して、バックアップを Azure クラウドに追加することもできます。 ただし、新しいか既存かを問わず保護グループに変更を加えるには、 *Get-DPMModifiableProtectionGroup* コマンドレットを使って [変更可能な](https://technet.microsoft.com/library/hh881713) オブジェクトのハンドルを取得する必要があります。

```
PS C:\> $MPG = Get-ModifiableProtectionGroup $PG
```

### <a name="adding-group-members-to-the-protection-group"></a>保護グループへのグループ メンバーの追加
各 DPM エージェントは、インストール先のサーバー上でデータソースの一覧を認識しています。 保護グループにデータソースを追加するには、DPM エージェントは最初にデータソースの一覧を DPM サーバーに送信する必要があります。 1 つ以上のデータソースが選択され、保護グループに追加されます。 このために必要な PowerShell の手順は、次のとおりです。

1. DPM エージェントから DPM が管理するすべてのサーバーの一覧を取得します。
2. 特定のサーバーの選択
3. サーバー上のすべてのデータソースの一覧を取得します。
4. 1 つ以上のデータソースの選択と保護グループへの追加

DPM エージェントがインストールされており、DPM サーバーによって管理されるサーバーの一覧を、 [Get-DPMProductionServer](https://technet.microsoft.com/library/hh881600) コマンドレットを使用して取得します。 この例では、フィルター処理をして *productionserver01* という名前を持つ PS のみをバックアップ用に構成します。

```
PS C:\> $server = Get-ProductionServer -DPMServerName "TestingServer" | where {($_.servername) –contains “productionserver01”
```

[Get-DPMDatasource](https://technet.microsoft.com/library/hh881605) コマンドレットを使用して ```$server``` のデータソースの一覧を取得します。 この例では、バックアップ用に構成するボリューム *D:\* をフィルター処理します。次に、このデータソースを [Add-DPMChildDatasource](https://technet.microsoft.com/library/hh881732) コマンドレットを使用して保護グループに追加します。*変更可能な*保護グループ オブジェクト```$MPG```を使用して、忘れずに追加します。

```
PS C:\> $DS = Get-Datasource -ProductionServer $server -Inquire | where { $_.Name -contains “D:\” }

PS C:\> Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS
```

この手順を、選択したすべてのデータソースが保護グループに追加されるまで必要なだけ繰り返します。 1 つのデータソースで開始して、保護グループを作成するためのワークフローを完了し、後で保護グループにさらにデータソースを追加することもできます。

### <a name="selecting-the-data-protection-method"></a>データ保護方法の選択
データソースが保護グループに追加されたら、次の手順は、 [Set-DPMProtectionType](https://technet.microsoft.com/library/hh881725) コマンドレットを使用した保護方法の指定です。 この例では、保護グループをローカル ディスクとクラウド バックアップ用にセットアップします。 また、 [Add-DPMChildDatasource](https://technet.microsoft.com/library/hh881732.aspx) コマンドレットに -Online フラグを付けて、保護対象のデータソースをクラウドに指定する必要があります。

```
PS C:\> Set-DPMProtectionType -ProtectionGroup $MPG -ShortTerm Disk –LongTerm Online
PS C:\> Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS –Online
```

### <a name="setting-the-retention-range"></a>保有期間の範囲設定
[Set-DPMPolicyObjective](https://technet.microsoft.com/library/hh881762) コマンドレットを使用してバックアップ ポイントの保有期間を設定します。 バックアップ スケジュールが定義される前に保有期間を設定するのは不自然な操作に思えるかもしれませんが、 ```Set-DPMPolicyObjective``` コマンドレットでは、後で変更できる既定のバックアップ スケジュールが自動的に設定されます。 バックアップ スケジュールを設定した後で、保有ポリシーを設定することも常に可能です。

次の例では、コマンドレットはディスク バックアップ用の保有期間パラメーターを設定します。 ここではバックアップの保有期間は 10 日間で、運用サーバーと DPM サーバーの間でデータが 6 時間ごとに同期されます。 ```SynchronizationFrequencyMinutes``` ではバックアップ ポイントの作成頻度は定義されませんが、データの DPM サーバーへのコピー頻度は定義されます。これにより、バックアップの肥大化を回避できます。

```
PS C:\> Set-DPMPolicyObjective –ProtectionGroup $MPG -RetentionRangeInDays 10 -SynchronizationFrequencyMinutes 360
```

Azure へのバックアップ (DPM ではオンライン バックアップの意味) では、保有期間の範囲は [Grandfather-Father-Son スキーム (GFS) を使用して長期間保有](backup-azure-backup-cloud-as-tape.md)用に構成できます。 つまり、日次、週次、月次、年次の保有期間ポリシーを組み合わせた保有ポリシーを定義できます。 この例では、必要な複雑な保有期間スキームを表す配列を作成し、 [Set-DPMPolicyObjective](https://technet.microsoft.com/library/hh881762) コマンドレットを使用して保有期間の範囲を構成します。

```
PS C:\> $RRlist = @()
PS C:\> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 180, Days)
PS C:\> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 104, Weeks)
PS C:\> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 60, Month)
PS C:\> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 10, Years)
PS C:\> Set-DPMPolicyObjective –ProtectionGroup $MPG -OnlineRetentionRangeList $RRlist
```

### <a name="set-the-backup-schedule"></a>バックアップ スケジュールの設定
```Set-DPMPolicyObjective``` コマンドレットを使用して保護目標を指定する場合、DPM は既定のバックアップ スケジュールを自動的に設定します。 既定のスケジュールを変更するには、[Get-DPMPolicySchedule](https://technet.microsoft.com/library/hh881749) コマンドレットと、それに続いて [Set-DPMPolicySchedule](https://technet.microsoft.com/library/hh881723) コマンドレットを使用します。

```
PS C:\> $onlineSch = Get-DPMPolicySchedule -ProtectionGroup $mpg -LongTerm Online
PS C:\> Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[0] -TimesOfDay 02:00
PS C:\> Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[1] -TimesOfDay 02:00 -DaysOfWeek Sa,Su –Interval 1
PS C:\> Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[2] -TimesOfDay 02:00 -RelativeIntervals First,Third –DaysOfWeek Sa
PS C:\> Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[3] -TimesOfDay 02:00 -DaysOfMonth 2,5,8,9 -Months Jan,Jul
PS C:\> Set-DPMProtectionGroup -ProtectionGroup $MPG
```

上記の例で ```$onlineSch``` は、GFS スキームの保護グループ用の既存のオンライン保護スケジュールを含む&4; つの要素を持つ配列です。

1. ```$onlineSch[0]``` は日次スケジュールを含みます
2. ```$onlineSch[1]``` は週次スケジュールを含みます
3. ```$onlineSch[2]``` は月次スケジュールを含みます
4. ```$onlineSch[3]``` は年次スケジュールを含みます

したがって、週次のスケジュールを変更する必要がある場合は、 ```$onlineSch[1]```を参照します。

### <a name="initial-backup"></a>初回バックアップ
データソースを初めてバックアップする場合、DPM レプリカ ボリューム上に保護対象データソースのコピーを作成する初期レプリカを、DPM により作成する必要があります。 このアクティビティは特定の期間スケジュール設定することも、[Set-DPMReplicaCreationMethod](https://technet.microsoft.com/library/hh881715) コマンドレットと ```-NOW``` パラメーターを使用して手動でトリガーすることもできます。

```
PS C:\> Set-DPMReplicaCreationMethod -ProtectionGroup $MPG -NOW
```
### <a name="changing-the-size-of-dpm-replica--recovery-point-volume"></a>DPM レプリカと回復ポイントのボリューム サイズの変更
DPM レプリカとシャドウ コピーのボリューム サイズは、[Set-DPMDatasourceDiskAllocation](https://technet.microsoft.com/library/hh881618.aspx) コマンドレットを使用して変更することもできます。次に例を示します。Get-DatasourceDiskAllocation -Datasource $DS Set-DatasourceDiskAllocation -Datasource $DS -ProtectionGroup $MPG -manual -ReplicaArea (2gb) -ShadowCopyArea (2gb)

### <a name="committing-the-changes-to-the-protection-group"></a>保護グループに対する変更のコミット
最後に、DPM が新しい保護グループの構成ごとにバックアップを実行する前に、変更をコミットする必要があります。 これを実行するには、 [Set-DPMProtectionGroup](https://technet.microsoft.com/library/hh881758) コマンドレットを使用します。

```
PS C:\> Set-DPMProtectionGroup -ProtectionGroup $MPG
```
## <a name="view-the-backup-points"></a>バックアップ ポイントの表示
データソースのすべての回復ポイントの一覧を取得するには、 [Get DPMRecoveryPoint](https://technet.microsoft.com/library/hh881746) コマンドレットを使用します。 この例の内容:

* 配列 ```$PG```
* ```$PG[0]```
* データソースのすべての回復ポイントを取得します。

```
PS C:\> $PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
PS C:\> $DS = Get-DPMDatasource -ProtectionGroup $PG[0]
PS C:\> $RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online
```

## <a name="restore-data-protected-on-azure"></a>Azure で保護されているデータの復元
データの復元は、```RecoverableItem``` オブジェクトと ```RecoveryOption``` オブジェクトの組み合わせです。 前のセクションで、データソースのバックアップ ポイントの一覧を取得しました。

次の例では、バックアップ ポイントと回復対象を組み合わせて、Hyper-V 仮想マシンを Microsoft Azure Backup から復元する方法を示します。 次のトピックがあります。

* [New-DPMRecoveryOption](https://technet.microsoft.com/library/hh881592) コマンドレットを使用した回復オプションの作成。
* ```Get-DPMRecoveryPoint``` コマンドレットを使用したバックアップ ポイントの配列の取得。
* 復元元のバックアップ ポイントの選択。

```
PS C:\> $RecoveryOption = New-DPMRecoveryOption -HyperVDatasource -TargetServer "HVDCenter02" -RecoveryLocation AlternateHyperVServer -RecoveryType Recover -TargetLocation “C:\VMRecovery”

PS C:\> $PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
PS C:\> $DS = Get-DPMDatasource -ProtectionGroup $PG[0]
PS C:\> $RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online

PS C:\> Restore-DPMRecoverableItem -RecoverableItem $RecoveryPoints[0] -RecoveryOption $RecoveryOption
```

使用されているコマンドは、任意のデータソースの種類に合わせて簡単に拡張できます。

## <a name="next-steps"></a>次のステップ
* Azure DPM Backup の詳細については、「 [DPM Backup の概要](backup-azure-dpm-introduction.md)



<!--HONumber=Jan17_HO4-->


