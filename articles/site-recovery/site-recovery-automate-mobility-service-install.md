---
title: "Azure Automation DSC を使用して Site Recovery モビリティ サービスをデプロイする | Microsoft Docs"
description: "Azure に VMware VM と物理サーバーをレプリケートするために Azure Automation DSC を使用して Azure Site Recovery モビリティ サービスと Azure エージェントを自動デプロイする方法についての説明"
services: site-recovery
documentationcenter: 
author: krnese
manager: lorenr
editor: 
ms.assetid: 1f8cd3ac-0522-48eb-a5f0-679ee9192ddb
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: krnese
translationtype: Human Translation
ms.sourcegitcommit: 2b9a8b5d7b889926f1cdbb6787fea1579dd21190
ms.openlocfilehash: 941bc07fa9be765403e8fe541caf660eef15585e
ms.lasthandoff: 02/22/2017


---
# <a name="deploy-the-mobility-service-with-azure-automation-dsc-for-replication-of-vm"></a>VM レプリケーションのために Azure Automation DSC を使用してモビリティ サービスをデプロイする
Operations Management Suite では、ビジネス継続性計画の一部として利用できる包括的なバックアップおよび障害復旧ソリューションが提供されています。

当初は HYPER-V レプリカを使用した HYPER-V に対応していました。 しかし、お客様が複数のハイパーバイザーとプラットフォームをクラウドでデプロイしていることを認識したため、多様なセットアップをサポートするように拡張しました。

現在 VMware のワークロードや物理サーバーを実行していて、Azure が接続先である場合、その環境では管理サーバーがすべての Azure Site Recovery コンポーネントを実行し、Azure との通信およびデータ レプリケーションを処理します。

## <a name="deploy-the-site-recovery-mobility-service-by-using-automation-dsc"></a>Automation DSC を使用して Site Recovery モビリティ サービスをデプロイする
初めに、この管理サーバーが行う処理を簡単に説明します。

管理サーバーは複数のサーバー ロールを実行します。 これらのロールの1 つが ” *構成*” で、通信を調整し、データ レプリケーションと復旧プロセスを管理します。

また ” *プロセス* ” ロールは、レプリケーションのゲートウェイとして機能します。 プロセス ロールは保護されたソース マシンからレプリケーション データを受信し、そのデータをキャッシュ、圧縮、暗号化によって最適化して、Azure ストレージ アカウントに送信します。 また、プロセス ロールの機能の&1; つとして、保護されたマシンへのモビリティ サービスのインストールをプッシュし、VMware VM の自動検出を実行します。

Azure でのフェールバックがある場合は、” *マスター ターゲット* ” ロールが、この操作の一環としてレプリケーション データを処理します。

保護されたマシンについては、” *モビリティ サービス*” を活用しています。 このコンポーネントは、Azure にレプリケートするすべてのマシン (VMware VM または物理サーバー) にデプロイされます。 マシン上のデータ書き込みをキャプチャして、管理サーバー (プロセス ロール) に転送します。

ビジネス継続性を扱う際には、ワークロード、インフラストラクチャ、および関連するコンポーネントを理解することが重要です。 それにより、目標復旧時間 (RTO) と目標復旧時点 (RPO) の要件を満たすことができます。 このコンテキストでは、モビリティ サービスが、ワークロードを想定どおりに保護するための鍵となります。

それでは、いくつかの Operations Management Suite コンポーネントを使用して、セットアップを信頼性が高く保護されたものにする最適な方法とはどのようなものでしょうか。

この記事では、Site Recovery と共に Azure Automation Desired State Configuration (DSC) を利用して、

* モビリティ サービスと Azure VM エージェントを保護対象の Windows マシンにデプロイし、
* Azure がレプリケーション対象の場合はこれらが常に実行されるようにする方法についての例を示します。

## <a name="prerequisites"></a>前提条件
* 必須のセットアップを格納するリポジトリ
* 管理サーバーへの登録に必要なパスフレーズを格納するリポジトリ

  > [!NOTE]
  > 管理サーバーごとに一意のパスフレーズが生成されます。 複数の管理サーバーをデプロイする場合は、passphrase.txt ファイルに正しいパスフレーズが保存されていることを確認してください。
  >
  >
* 保護を有効にするコンピューターにインストールされている Windows Management Framework (WMF) 5.0 (Automation DSC の要件)

  > [!NOTE]
  > WMF 4.0 がインストールされている Windows コンピューターに対して DSC を使用する場合は、[分離された環境での DSC の使用](#Use DSC in disconnected environments)セクションをご覧ください。
  >
  >

モビリティ サービスは、コマンドラインからインストールでき、いくつかの引数を使用できます。 このため、バイナリを (セットアップからの抽出後に) 取得し、DSC 構成を使用して取得できる場所に保存する必要があります。

## <a name="step-1-extract-binaries"></a>手順 1: バイナリの抽出
1. このセットアップに必要なファイルを抽出するために、管理サーバーで次のディレクトリに移動します。

    **\Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository**

    このフォルダーに、次の名前の MSI ファイルがあります。

    **Microsoft-ASR_UA_version_Windows_GA_date_Release.exe**

    次のコマンドを使用してインストーラーを抽出します。

    **.\Microsoft-ASR_UA_9.1.0.0_Windows_GA_02May2016_release.exe /q /x:C:\Users\Administrator\Desktop\Mobility_Service\Extract**
2. すべてのファイルを選択し、圧縮 (zip 形式) フォルダーに送ります。

Automation DSC を使用してモビリティ サービスのセットアップを自動化するために必要なバイナリを取得できました。

### <a name="passphrase"></a>パスフレーズ
次に、この zip 形式フォルダーを配置する場所を決定する必要があります。 Azure ストレージ アカウントを使用して、セットアップに必要なパスフレーズを保存できます。これについては後で説明します。 エージェントはこれをプロセスの一部として管理サーバーに登録します。

管理サーバーをデプロイするときに取得したパスフレーズは、passphrase.txt という txt ファイルに保存します。

zip 形式フォルダーとパスフレーズの両方を Azure ストレージ アカウントの専用コンテナーに配置します。

![フォルダーの場所](./media/site-recovery-automate-mobilitysevice-install/folder-and-passphrase-location.png)

ネットワーク内の共有上にこれらのファイルを保持することもできます。 ただし、後で使用する DSC リソースにアクセスが設定され、セットアップとパスフレーズを取得できることを確認する必要があります。

## <a name="step-2-create-the-dsc-configuration"></a>手順 2: DSC 構成の作成
このセットアップでは WMF 5.0 を使用します。 つまり、Automation DSC によって正常に構成を適用するために、WMF 5.0 が必要となります。

この環境では、次の例の DSC 構成を使用します。

```powershell
configuration ASRMobilityService {

    $RemoteFile = 'https://knrecstor01.blob.core.windows.net/asr/ASR.zip'
    $RemotePassphrase = 'https://knrecstor01.blob.core.windows.net/asr/passphrase.txt'
    $RemoteAzureAgent = 'http://go.microsoft.com/fwlink/p/?LinkId=394789'
    $LocalAzureAgent = 'C:\Temp\AzureVmAgent.msi'
    $TempDestination = 'C:\Temp\asr.zip'
    $LocalPassphrase = 'C:\Temp\Mobility_service\passphrase.txt'
    $Role = 'Agent'
    $Install = 'C:\Program Files (x86)\Microsoft Azure Site Recovery'
    $CSEndpoint = '10.0.0.115'
    $Arguments = '/Role "{0}" /InstallLocation "{1}" /CSEndpoint "{2}" /PassphraseFilePath "{3}"' -f $Role,$Install,$CSEndpoint,$LocalPassphrase

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    node localhost {

        File Directory {
            DestinationPath = 'C:\Temp\ASRSetup\'
            Type = 'Directory'            
        }

        xRemoteFile Setup {
            URI = $RemoteFile
            DestinationPath = $TempDestination
            DependsOn = '[File]Directory'
        }

        xRemoteFile Passphrase {
            URI = $RemotePassphrase
            DestinationPath = $LocalPassphrase
            DependsOn = '[File]Directory'
        }

        xRemoteFile AzureAgent {
            URI = $RemoteAzureAgent
            DestinationPath = $LocalAzureAgent
            DependsOn = '[File]Directory'
        }

        Archive ASRzip {
            Path = $TempDestination
            Destination = 'C:\Temp\ASRSetup'
            DependsOn = '[xRemotefile]Setup'
        }

        Package Install {
            Path = 'C:\temp\ASRSetup\ASR\UNIFIEDAGENT.EXE'
            Ensure = 'Present'
            Name = 'Microsoft Azure Site Recovery mobility Service/Master Target Server'
            ProductId = '275197FC-14FD-4560-A5EB-38217F80CBD1'
            Arguments = $Arguments
            DependsOn = '[Archive]ASRzip'
        }

        Package AzureAgent {
            Path = 'C:\Temp\AzureVmAgent.msi'
            Ensure = 'Present'
            Name = 'Windows Azure VM Agent - 2.7.1198.735'
            ProductId = '5CF4D04A-F16C-4892-9196-6025EA61F964'
            Arguments = '/q /l "c:\temp\agentlog.txt'
            DependsOn = '[Package]Install'
        }

        Service ASRvx {
            Name = 'svagents'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service ASR {
            Name = 'InMage Scout Application Service'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service AzureAgentService {
            Name = 'WindowsAzureGuestAgent'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }

        Service AzureTelemetry {
            Name = 'WindowsAzureTelemetryService'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }
    }
}
```
この構成では、次の操作を行います。

* 変数によって、モビリティ サービスと Azure VM エージェントのバイナリ、およびパスフレーズの取得場所が構成に指定され、出力の保存先も示されます。
* `xRemoteFile` を使用してリポジトリからファイルをダウンロードできるように、xPSDesiredStateConfiguration DSC リソースをインポートします。
* バイナリを格納するディレクトリを作成します。
* Archive リソースにより、zip 形式フォルダーからファイルを抽出します。
* パッケージ 'Install' により、特定の引数を指定して、UNIFIEDAGENT.EXE インストーラーからモビリティ サービスをインストールします  (引数を作成する変数は、環境に合わせて変更する必要があります)。
* パッケージ 'AzureAgent' により、Azure で実行されるすべての VM で推奨される Azure VM エージェントをインストールします。 このエージェントにより、VM のフェールオーバー後に拡張機能を追加することもできるようになります。
* Service リソースにより、関連するモビリティ サービスと Azure のサービスが常に実行されているようにします。

**ASRMobilityService**として構成を保存します。

> [!NOTE]
> 実際の管理サーバーに合わせて構成の CSIP を置換して、エージェントが正しく接続され、正しいパスフレーズが使用されるようにしてください。
>
>

## <a name="step-3-upload-to-automation-dsc"></a>手順 3: Automation DSC へのアップロード
作成した DSC 構成によって必要な DSC リソース モジュール (xPSDesiredStateConfiguration) がインポートされるため、DSC 構成をアップロードする前に、Automation にこのモジュールをインポートする必要があります。

Automation アカウントにサインインし、**[アセット]** > **[モジュール]** に移動して、**[Browse Gallery (ギャラリーの参照)]** をクリックします。

ここで、モジュールを検索し、アカウントにインポートすることができます。

![モジュールのインポート](./media/site-recovery-automate-mobilitysevice-install/search-and-import-module.png)

この作業が完了したら、Azure Resource Manager モジュールをインストールしたコンピューターに移動し、新しく作成した DSC 構成のインポートを開始します。

### <a name="import-cmdlets"></a>コマンドレットのインポート
PowerShell で、Azure サブスクリプションにサインインします。 環境に合わせてコマンドレットを変更し、Automation アカウント情報を変数内にキャプチャします。

```powershell
$AAAccount = Get-AzureRmAutomationAccount -ResourceGroupName 'KNOMS' -Name 'KNOMSAA'
```

次のコマンドレットを使用して Automation DSC へ構成をアップロードします。

```powershell
$ImportArgs = @{
    SourcePath = 'C:\ASR\ASRMobilityService.ps1'
    Published = $true
    Description = 'DSC Config for Mobility Service'
}
$AAAccount | Import-AzureRmAutomationDscConfiguration @ImportArgs
```

### <a name="compile-the-configuration-in-automation-dsc"></a>Automation DSC での構成のコンパイル
次に、Automation DSC で構成をコンパイルして、ノードを登録できるようにします。 これを行うために、次のコマンドレットを実行します。

```powershell
$AAAccount | Start-AzureRmAutomationDscCompilationJob -ConfigurationName ASRMobilityService
```

実際にはホストされている DSC プル サービスに構成をデプロイしているため、この処理には数分かかることがあります。

構成をコンパイルしたら、PowerShell (Get AzureRmAutomationDscCompilationJob) または [Azure ポータル](https://portal.azure.com/)を使用して、ジョブの情報を取得できます。

![ジョブの取得](./media/site-recovery-automate-mobilitysevice-install/retrieve-job.png)

DSC 構成が正常に Automation DSC へ公開およびアップロードされました。

## <a name="step-4-onboard-machines-to-automation-dsc"></a>手順 4: Automation DSC へのマシンの追加
> [!NOTE]
> このシナリオを完了するための前提条件の&1; つとして、Windows コンピューターが WMF の最新バージョンで更新されている必要があります。 お使いのプラットフォームに適切なバージョンは、 [ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=50395)からダウンロードおよびインストールできます。
>
>

ノードに適用する DSC の metaconfig を作成します。 正常に作成するには、Azure で選択した Automation アカウントのエンドポイント URL とプライマリ キーを取得する必要があります。 これらの値は、Automation アカウントの **[すべての設定]** ブレードの **[キー]** にあります。

![キーの値](./media/site-recovery-automate-mobilitysevice-install/key-values.png)

この例には、Site Recovery で保護する Windows Server 2012 R2 物理サーバーがあります。

### <a name="check-for-any-pending-file-rename-operations-in-the-registry"></a>レジストリ内における保留中のファイル名変更操作の確認
サーバーを Automation DSC のエンドポイントに関連付ける前に、レジストリに保留中のファイル名変更操作がないかどうかを確認することをお勧めします。 保留中のファイル名変更操作があると、再起動が保留されてセットアップを完了できない可能性があります。

次のコマンドレットを実行して、サーバー上に保留中の再起動がないかどうかを確認します。

```powershell
Get-ItemProperty 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\' | Select-Object -Property PendingFileRenameOperations
```
空白が返された場合は、セットアップを続行できます。 それ以外の場合は、メンテナンス期間中にサーバーを再起動して、これに対処する必要があります。

サーバーに構成を適用するために、PowerShell Integrated Scripting Environment (ISE) を起動して次のスクリプトを実行します。 これは、本質的には DSC ローカル構成であり、ローカル構成マネージャーのエンジンに対して Automation DSC サービスに登録し、特定の構成 (ASRMobilityService.localhost) を取得するように指示します。

```powershell
[DSCLocalConfigurationManager()]
configuration metaconfig {
    param (
        $URL,
        $Key
    )
    node localhost {
        Settings {
            RefreshFrequencyMins = '30'
            RebootNodeIfNeeded = $true
            RefreshMode = 'PULL'
            ActionAfterReboot = 'ContinueConfiguration'
            ConfigurationMode = 'ApplyAndMonitor'
            AllowModuleOverwrite = $true
        }

        ResourceRepositoryWeb AzureAutomationDSC {
            ServerURL = $URL
            RegistrationKey = $Key
        }

        ConfigurationRepositoryWeb AzureAutomationDSC {
            ServerURL = $URL
            RegistrationKey = $Key
            ConfigurationNames = 'ASRMobilityService.localhost'
        }

        ReportServerWeb AzureAutomationDSC {
            ServerURL = $URL
            RegistrationKey = $Key
        }
    }
}
metaconfig -URL 'https://we-agentservice-prod-1.azure-automation.net/accounts/<YOURAAAccountID>' -Key '<YOURAAAccountKey>'

Set-DscLocalConfigurationManager .\metaconfig -Force -Verbose
```

この構成は、ローカル構成マネージャーがマネージャー自体を Automation DSC に登録するように構成します。 基本的には、エンジンの動作方法と構成のずれ (ApplyAndAutoCorrect) が発生した場合の動作を指示するとともに、必要な再起動が行われた場合に以降の構成を続行するように指示します。

このスクリプトを実行すると、ノードは Automation DSC への登録を開始します。

![進行中のノード登録](./media/site-recovery-automate-mobilitysevice-install/register-node.png)

Azure ポータルに戻ると、新しく登録したノードがポータルに表示されていることがわかります。

![ポータルの登録されたノード](./media/site-recovery-automate-mobilitysevice-install/registered-node.png)

サーバーで、次の PowerShell コマンドレットを実行して、ノードが正しく登録されたことを確認できます。

```powershell
Get-DscLocalConfigurationManager
```

構成がプルされてサーバーに適用された後で、次のコマンドレットを実行してこれを確認できます。

```powershell
Get-DscConfigurationStatus
```

出力には、サーバーがその構成を正常にプルしていることが示されます。

![出力](./media/site-recovery-automate-mobilitysevice-install/successful-config.png)

さらに、モビリティ サービスのセットアップでは、*SystemDrive*\ProgramData\ASRSetupLogs に独自のログが配置されます。

これで完了です。 Site Recovery で保護するコンピューター上にモビリティ サービスが正常にデプロイおよび登録されます。 DSC により、必要なサービスが常に実行されるようになります。

![デプロイに成功](./media/site-recovery-automate-mobilitysevice-install/successful-install.png)

正常なデプロイが管理サーバーによって検出された後で、保護の構成に進んで Site Recovery を使用したマシンでのレプリケーションを有効にできます。

## <a name="use-dsc-in-disconnected-environments"></a>分離された環境での DSC の使用
コンピューターがインターネットに接続されていない場合でも、DSC を使用して、保護するワークロード上にモビリティ サービスをデプロイおよび構成することができます。

あるいは、環境内で、Automation DSC で使用できるものと同じ機能を提供する独自の DSC プル サーバーをインスタンス化できます。 この場合、クライアントは、DSC エンドポイントに登録された後に構成をプルします。 ただし、プッシュを使用する別のオプションもあります。この場合、ローカルまたはリモートのどちらかにより手動で DSC 構成をマシンにプッシュします。

この例では、このコンピューター名に追加のパラメーターがあることに注意してください。 リモートのファイルは現在、保護対象のマシンからアクセスできるリモート共有に配置されています。 スクリプトの末尾で構成を設定してから、ターゲット コンピューターへの DSC 構成の適用を開始しています。

### <a name="prerequisites"></a>前提条件
xPSDesiredStateConfiguration PowerShell モジュールがインストールされていることを確認します。 WMF 5.0 がインストールされている Windows マシンでは、ターゲット コンピューターで次のコマンドレットを実行するだけで xPSDesiredStateConfiguration モジュールをインストールできます。

```powershell
Find-Module -Name xPSDesiredStateConfiguration | Install-Module
```

また、WMF 4.0 がインストール済みの Windows コンピューターにモジュールを配信する必要がある場合は、モジュールをダウンロードおよび保存することもできます。 PowerShellGet (WMF 5.0) が存在するコンピューターでこのコマンドレットを実行します。

```powershell
Save-Module -Name xPSDesiredStateConfiguration -Path <location>
```

また WMF 4.0 では、 [Windows 8.1 update KB2883200](https://www.microsoft.com/download/details.aspx?id=40749) がコンピューターにインストールされているかどうかも確認します。

WMF 5.0 または WMF 4.0 がインストール済みの Windows コンピューターに、次の構成をプッシュできます。

```powershell
configuration ASRMobilityService {
    param (
        [Parameter(Mandatory=$true)]
        [ValidateNotNullOrEmpty()]
        [System.String] $ComputerName
    )

    $RemoteFile = '\\myfileserver\share\asr.zip'
    $RemotePassphrase = '\\myfileserver\share\passphrase.txt'
    $RemoteAzureAgent = '\\myfileserver\share\AzureVmAgent.msi'
    $LocalAzureAgent = 'C:\Temp\AzureVmAgent.msi'
    $TempDestination = 'C:\Temp\asr.zip'
    $LocalPassphrase = 'C:\Temp\Mobility_service\passphrase.txt'
    $Role = 'Agent'
    $Install = 'C:\Program Files (x86)\Microsoft Azure Site Recovery'
    $CSEndpoint = '10.0.0.115'
    $Arguments = '/Role "{0}" /InstallLocation "{1}" /CSEndpoint "{2}" /PassphraseFilePath "{3}"' -f $Role,$Install,$CSEndpoint,$LocalPassphrase

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    node $ComputerName {      
        File Directory {
            DestinationPath = 'C:\Temp\ASRSetup\'
            Type = 'Directory'            
        }

        xRemoteFile Setup {
            URI = $RemoteFile
            DestinationPath = $TempDestination
            DependsOn = '[File]Directory'
        }

        xRemoteFile Passphrase {
            URI = $RemotePassphrase
            DestinationPath = $LocalPassphrase
            DependsOn = '[File]Directory'
        }

        xRemoteFile AzureAgent {
            URI = $RemoteAzureAgent
            DestinationPath = $LocalAzureAgent
            DependsOn = '[File]Directory'
        }

        Archive ASRzip {
            Path = $TempDestination
            Destination = 'C:\Temp\ASRSetup'
            DependsOn = '[xRemotefile]Setup'
        }

        Package Install {
            Path = 'C:\temp\ASRSetup\ASR\UNIFIEDAGENT.EXE'
            Ensure = 'Present'
            Name = 'Microsoft Azure Site Recovery mobility Service/Master Target Server'
            ProductId = '275197FC-14FD-4560-A5EB-38217F80CBD1'
            Arguments = $Arguments
            DependsOn = '[Archive]ASRzip'
        }

        Package AzureAgent {
            Path = 'C:\Temp\AzureVmAgent.msi'
            Ensure = 'Present'
            Name = 'Windows Azure VM Agent - 2.7.1198.735'
            ProductId = '5CF4D04A-F16C-4892-9196-6025EA61F964'
            Arguments = '/q /l "c:\temp\agentlog.txt'
            DependsOn = '[Package]Install'
        }

        Service ASRvx {
            Name = 'svagents'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service ASR {
            Name = 'InMage Scout Application Service'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service AzureAgentService {
            Name = 'WindowsAzureGuestAgent'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }

        Service AzureTelemetry {
            Name = 'WindowsAzureTelemetryService'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }
    }
}
ASRMobilityService -ComputerName 'MyTargetComputerName'

Start-DscConfiguration .\ASRMobilityService -Wait -Force -Verbose
```

独自の DSC プル サーバーを社内ネットワーク内でインスタンス化して Automation DSC で使用できる機能を模倣するには、「 [Setting up a DSC web pull server (DSC Web プル サーバーのセットアップ)](https://msdn.microsoft.com/powershell/dsc/pullserver?f=255&MSPPError=-2147217396)」を参照してください。

## <a name="optional-deploy-a-dsc-configuration-by-using-an-azure-resource-manager-template"></a>省略可能: Azure Resource Manager テンプレートを使用した DSC 構成のデプロイ
この記事では、独自の DSC 構成を作成して自動的にモビリティ サービスと Azure VM エージェントをデプロイし、これらが保護対象のコンピューターで実行されるようにする方法に重点を置いてきました。 こうした方法以外にも、この DSC 構成を新規または既存の Azure Automation アカウントにデプロイする Azure Resource Manager テンプレートがあります。 このテンプレートの入力パラメーターを使用して、環境の変数が含まれる Automation 資産を作成できます。

テンプレートのデプロイ後は、このガイドの手順 4 を参照してコンピューターを追加できます。

このテンプレートは、次の操作を行います。

1. 既存の Automation アカウントの使用、または新規 Automation アカウントの作成
2. 次に対する入力パラメーターの受け取り:
   * ASRRemoteFile--モビリティ サービスのセットアップを保存した場所
   * ASRPassphrase--passphrase.txt ファイルを保存した場所
   * ASRCSEndpoint--管理サーバーの IP アドレス
3. xPSDesiredStateConfiguration PowerShell モジュールのインポート
4. DSC 構成の作成とコンパイル

上記の手順をすべて正しい順序で実行すると、コンピューターを保護対象として追加できるようになります。

デプロイの手順も含まれるテンプレートは [GitHub](https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/DSC)にあります。

PowerShell を使用してテンプレートをデプロイする

```powershell
$RGDeployArgs = @{
    Name = 'DSC3'
    ResourceGroupName = 'KNOMS'
    TemplateFile = 'https://raw.githubusercontent.com/krnese/AzureDeploy/master/OMS/MSOMS/DSC/azuredeploy.json'
    OMSAutomationAccountName = 'KNOMSAA'
    ASRRemoteFile = 'https://knrecstor01.blob.core.windows.net/asr/ASR.zip'
    ASRRemotePassphrase = 'https://knrecstor01.blob.core.windows.net/asr/passphrase.txt'
    ASRCSEndpoint = '10.0.0.115'
    DSCJobGuid = [System.Guid]::NewGuid().ToString()
}
New-AzureRmResourceGroupDeployment @RGDeployArgs -Verbose
```

## <a name="next-steps"></a>次のステップ
モビリティ サービスのエージェントをデプロイした後は、仮想マシンの [レプリケーションを有効にする](site-recovery-vmware-to-azure.md#enable-replication) ことができます。

