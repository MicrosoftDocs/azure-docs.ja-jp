---
title: "PowerShell を使用してクラシック ポータルで Azure に VMware VM をレプリケートする | Microsoft Docs"
description: "クラシック ポータルで Site Recovery と PowerShell を使用して VMM クラウド内の Hyper-V 仮想マシンのレプリケートを自動化する"
services: site-recovery
documentationcenter: 
author: bsiva
manager: abhiag
editor: tysonn
ms.assetid: 9011f567-e0b4-4306-951a-b30da19f5db6
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: bsiva
translationtype: Human Translation
ms.sourcegitcommit: a084cecddc2af36ee087b2e0e63a2b18b20f07f0
ms.openlocfilehash: d5fed9feb2292002a06c426cdd9e4e18f67bd3ec
ms.lasthandoff: 02/22/2017


---
# <a name="replicate-hyper-v-vms-to-azure-with-powershell-in-the-classic-portal"></a>クラシック ポータルで PowerShell を使用して Azure に VMware VM をレプリケートする
> [!div class="op_single_selector"]
> * [Azure ポータル](site-recovery-vmm-to-azure.md)
> * [PowerShell - Resource Manager](site-recovery-vmm-to-azure-powershell-resource-manager.md)
> * [クラシック ポータル](site-recovery-vmm-to-azure-classic.md)
> * [PowerShell - クラシック](site-recovery-deploy-with-powershell.md)
>
>

## <a name="overview"></a>Overview
Azure Site Recovery は、さまざまなデプロイ シナリオでの仮想マシンのレプリケーション、フェールオーバー、復旧を調整してビジネス継続性と障害復旧 (BCDR) 戦略に貢献します。 デプロイ シナリオのすべての一覧については、「 [Azure Site Recovery の概要](site-recovery-overview.md)」を参照してください。

この記事では、System Center VMM クラウドの Hyper-V 仮想マシンを Azure ストレージにレプリケートするように Azure Site Recovery を設定するときに実行する必要がある一般的なタスクを PowerShell を使用して自動化する方法を示します。

ガイドには、シナリオの前提条件が含まれています。また、Site Recovery コンテナーを設定する方法、ソース VMM サーバーに Azure Site Recovery プロバイダーをインストールする方法、このコンテナーにサーバーを登録する方法、Azure ストレージ アカウントを追加する方法、Hyper-V ホスト サーバーに Azure Recovery Services エージェントをインストールする方法、保護されるすべての仮想マシンに適用される VMM クラウドの保護設定を構成する方法、およびこれらの仮想マシンの保護を有効にする方法についても説明しています。 すべてが正しく動作していることを確認するために、最後にフェールオーバーをテストします。

このシナリオの設定時に問題が発生した場合は、 [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)に質問を投稿してください。

> [!NOTE]
> Azure には、リソースの作成と操作に関して、 [Resource Manager とクラシック](../azure-resource-manager/resource-manager-deployment-model.md)の&2; 種類のデプロイメント モデルがあります。 この記事では、クラシック デプロイ モデルの使用方法について説明します。
>
>

## <a name="before-you-start"></a>開始する前に
次の前提条件を満たしていることを確認してください。

### <a name="azure-prerequisites"></a>Azure の前提条件
* [Microsoft Azure](https://azure.microsoft.com/) のアカウントが必要です。 アカウントがなくても、 [無料試用版](https://azure.microsoft.com/pricing/free-trial/)を使用できます。
* レプリケートしたデータを格納するには Azure ストレージ アカウントが必要になります。 アカウントでは geo レプリケーションを有効にする必要があります。 アカウントは Azure Site Recovery コンテナーと同じリージョンにあり、同じサブスクリプションに関連付けられている必要があります。 [Azure ストレージについてはこちらを参照してください。](../storage/storage-introduction.md)
* 保護する仮想マシンが [Azure 仮想マシンの前提条件](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)に準拠していることを確認する必要があります。

### <a name="vmm-prerequisites"></a>VMM の前提条件
* System Center 2012 R2 で実行される VMM サーバーが必要です。
* 保護する VMM サーバーにクラウドが少なくとも&1; つ必要です。 このクラウドには以下のものが含まれている必要があります。
  * 1 つ以上の VMM ホスト グループ。
  * 各ホスト グループ内に&1; つ以上の Hyper-V ホスト サーバーまたはクラスター。
  * ソース Hyper-V サーバー上に配置された&1; つ以上の仮想マシン。

### <a name="hyper-v-prerequisites"></a>Hyper-V の前提条件
* ホスト Hyper-V サーバーは、Hyper-V ロールを持つ **Windows Server 2012** 以降か、**Microsoft Hyper-V Server 2012** が実行され、最新の更新プログラムがインストールされている必要があります。
* クラスターで Hyper-V を実行している場合に、静的 IP アドレス ベースのクラスターが存在すると、クラスター ブローカーが自動的に作成されません。 クラスター ブローカーを手動で構成する必要があります。 これを行うには、Server Manager でフェールオーバー クラスター マネージャーを選択します。クラスターに接続し、**[ロールの構成]** をクリックし、高可用性ウィザードの **[ロールの選択]** 画面で **[Hyper-V レプリカ ブローカー]** を選択します。
* 保護を管理するすべての Hyper-V ホスト サーバーまたはクラスターが VMM クラウドに属している必要があります。

### <a name="network-mapping-prerequisites"></a>ネットワーク マッピングの前提条件
仮想マシンを Azure ネットワーク マッピングで保護する場合、ソース VMM サーバー上の VM ネットワークとターゲットの Azure ネットワークをマップして以下を実現します。

* 同じネットワーク上でフェールオーバーするすべてのマシンは、どの復旧計画に含まれていても、相互に接続できる。
* ターゲットの Azure ネットワークでネットワーク ゲートウェイをセットアップすると、仮想マシンを他のオンプレミスの仮想マシンに接続できる。
* ネットワーク マッピングを構成しない場合、Azure へのフェールオーバー後、同じ復旧計画でフェールオーバーする仮想マシンのみ相互に接続できる。

ネットワーク マッピングをデプロイする場合は、以下のことが必要になります。

* ソース VMM サーバー上の保護する仮想マシンが VM ネットワークに接続している。 そのネットワークは、クラウドに関連付けられた論理ネットワークにリンクされている必要があります。
* レプリケートされた仮想マシンがフェールオーバー後に接続できる Azure ネットワーク。 フェールオーバー時にこのネットワークを選択します。 ネットワークは Azure Site Recovery サブスクリプションと同じリージョンにある必要があります。

### <a name="powershell-prerequisites"></a>PowerShell の前提条件
Azure PowerShell を使用する準備が整っていることを確認してください。 PowerShell を使用している場合は、0.8.10 以降のバージョンにアップグレードする必要があります。 PowerShell の設定方法の詳細については、 [Azure PowerShell のインストールと構成の方法](/powershell/azureps-cmdlets-docs)に関するページを参照してください。 PowerShell を設定して構成したら、サービスで使用可能なすべてのコマンドレットを [ここ](https://msdn.microsoft.com/library/dn850420.aspx)に表示できます。

Azure PowerShell でのパラメーター値、入力、出力の一般的な処理方法など、コマンドレットの使用に役立つヒントについては、「 [Azure コマンドレットの概要](https://msdn.microsoft.com/library/azure/jj554332.aspx)」を参照してください。

## <a name="step-1-set-the-subscription"></a>ステップ 1: サブスクリプションを設定する
PowerShell で、次のコマンドレットを実行します。

```
$UserName = "<user@live.com>"
$Password = "<password>"
$AzureSubscriptionName = "prod_sub1"

$SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
$Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $securePassword
Add-AzureAccount -Credential $Cred;
$AzureSubscription = Select-AzureSubscription -SubscriptionName $AzureSubscriptionName

```

"< >" 内の要素をユーザー固有の情報に置き換えます。

## <a name="step-2-create-a-site-recovery-vault"></a>ステップ 2: Site Recovery コンテナーを作成する
PowerShell で "< >" 内の要素をユーザー固有の情報に置き換え、次のコマンドを実行します。

```

$VaultName = "<testvault123>"
$VaultGeo  = "<Southeast Asia>"
$OutputPathForSettingsFile = "<c:\>"

```


```
New-AzureSiteRecoveryVault -Location $VaultGeo -Name $VaultName;
$vault = Get-AzureSiteRecoveryVault -Name $VaultName;

```

## <a name="step-3-generate-a-vault-registration-key"></a>ステップ 3: コンテナー登録キーを生成する
コンテナーの登録キーを生成します。 Azure Site Recovery プロバイダーをダウンロードして VMM サーバーにインストールした後で、このキーを使用して、VMM サーバーをコンテナーに登録します。

1. コンテナー設定ファイルを取得し、コンテキストを設定します。

   ```

   $VaultName = "<testvault123>"
   $VaultGeo  = "<Southeast Asia>"
   $OutputPathForSettingsFile = "<c:\>"

   $VaultSetingsFile = Get-AzureSiteRecoveryVaultSettingsFile -Location $VaultGeo -Name $VaultName -Path $OutputPathForSettingsFile;

   ```
2. 次のコマンドを実行して、コンテナーのコンテキストを設定します。

   ```

   $VaultSettingFilePath = $vaultSetingsFile.FilePath
   $VaultContext = Import-AzureSiteRecoveryVaultSettingsFile -Path $VaultSettingFilePath -ErrorAction Stop

   ```

## <a name="step-4-install-the-azure-site-recovery-provider"></a>ステップ 4: Azure Site Recovery プロバイダーをインストールする
1. VMM マシンで、次のコマンドを実行してディレクトリを作成します。

   ```

   pushd C:\ASR\

   ```
2. 次のコマンドを実行して、ダウンロードしたプロバイダーを使用して、ファイルを抽出します。

   ```

   AzureSiteRecoveryProvider.exe /x:. /q

   ```
3. 次のコマンドを使用してプロバイダーをインストールします。

   ```

   .\SetupDr.exe /i

   ```

   ```

   $installationRegPath = "hklm:\software\Microsoft\Microsoft System Center Virtual Machine Manager Server\DRAdapter"
   do
   {
     $isNotInstalled = $true;
     if(Test-Path $installationRegPath)
     {
         $isNotInstalled = $false;
     }
   }While($isNotInstalled)

   ```

   インストールが完了するのを待ちます。
4. 次のコマンドを使用して、コンテナーにサーバーを登録します。

   ```

   $BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
   pushd $BinPath
   $encryptionFilePath = "C:\temp\"
   .\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice

   ```

## <a name="step-5-create-an-azure-storage-account"></a>ステップ 5: Azure のストレージ アカウントを作成する
Azure のストレージ アカウントを持っていない場合、次のコマンドを実行して、geo レプリケーションが有効になっているアカウントを作成します。

```

$StorageAccountName = "teststorageacc1"
$StorageAccountGeo  = "Southeast Asia"

New-AzureStorageAccount -StorageAccountName $StorageAccountName -Label $StorageAccountName -Location $StorageAccountGeo;

```

ストレージ アカウントは Azure Site Recovery サービスと同じリージョンである必要があり、同じサブスクリプションに関連付けられている必要があります。

## <a name="step-6-install-the-azure-recovery-services-agent"></a>ステップ 6: Azure Recovery Services エージェントをインストールする
Azure ポータルから、保護する VMM クラウドに配置されている Hyper-V ホスト サーバーごとに、Azure Recovery Services エージェントをインストールします。

すべての VMM ホストで次のコマンドを実行します。

```

marsagentinstaller.exe /q /nu

```


## <a name="step-7-configure-cloud-protection-settings"></a>ステップ 7: クラウドの保護設定を構成する
1. 次のコマンドを実行して、Azure にクラウド保護のプロファイルを作成します。

   ```

   $ReplicationFrequencyInSeconds = "300";
   $ProfileResult = New-AzureSiteRecoveryProtectionProfileObject -ReplicationProvider HyperVReplica -RecoveryAzureSubscription $AzureSubscriptionName -RecoveryAzureStorageAccount $StorageAccountName -ReplicationFrequencyInSeconds     $ReplicationFrequencyInSeconds;

   ```
2. 次のコマンドを実行して、保護コンテナーを取得します。

   ```

   $PrimaryCloud = "testcloud"
   $protectionContainer = Get-AzureSiteRecoveryProtectionContainer -Name $PrimaryCloud;

   ```
3. クラウドと保護コンテナーとのアソシエーションを開始します。

   ```

   $associationJob = Start-AzureSiteRecoveryProtectionProfileAssociationJob -ProtectionProfile $profileResult -PrimaryProtectionContainer $protectionContainer;        

   ```
4. ジョブが完了したら、次のコマンドを実行します。

        $job = Get-AzureSiteRecoveryJob -Id $associationJob.JobId;
        if($job -eq $null -or $job.StateDescription -ne "Completed")
        {
        $isJobLeftForProcessing = $true;
        }


1. ジョブの処理が完了したら、次のコマンドを実行します。

        Do
        {
        $job = Get-AzureSiteRecoveryJob -Id $associationJob.JobId;
        Write-Host "Job State:{0}, StateDescription:{1}" -f Job.State, $job.StateDescription;
        if($job -eq $null -or $job.StateDescription -ne "Completed")
        {
            $isJobLeftForProcessing = $true;
        }

        if($isJobLeftForProcessing)
        {
            Start-Sleep -Seconds 60
        }
        }While($isJobLeftForProcessing)



操作の完了を確認するには、 [アクティビティを監視する](#monitor)の手順に従います。

## <a name="step-8-configure-network-mapping"></a>ステップ 8: ネットワーク マッピングを構成する
ネットワーク マッピングを開始する前に、ソース VMM サーバー上の仮想マシンが VM ネットワークに接続されていることを確認してください。 さらに、1 つまたは複数の Azure 仮想ネットワークを作成します。 複数の VM ネットワークを&1; つの Azure ネットワークにマップできることに注意してください。

ターゲット ネットワークに複数のサブネットがあり、そのサブネットのいずれかが、ソースの仮想マシンが配置されているサブネットと同じ名前である場合、フェールオーバー後、レプリカの仮想マシンはそのターゲット サブネットに接続することに注意してください。 ターゲットのサブネットで名前が一致するものがなければ、仮想マシンはネットワークの最初のサブネットに接続されます。

最初のコマンドは、現在の Azure Site Recovery コンテナーのサーバーを取得します。 このコマンドは、$Servers 配列変数に、Microsoft Azure Site Recovery のサーバーを格納します。

    $Servers = Get-AzureSiteRecoveryServer


2 番目のコマンドは、$Servers 配列内の最初のサーバーの Site Recovery ネットワークを取得します。 コマンドは、$Networks 変数に、ネットワークを格納します。

    $Networks = Get-AzureSiteRecoveryNetwork -Server $Servers[0]

3 番目のコマンドは、Get-AzureSubscription コマンドレットを使用して Azure のサブスクリプションを取得し、$Subscriptions 変数にその値を格納します。

    $Subscriptions = Get-AzureSubscription



4 番目のコマンドは、Get AzureVNetSite コマンドレットを使用して Azure の仮想ネットワークを取得し、$AzureVmNetworks 変数にその値を格納します。

    $AzureVmNetworks = Get-AzureVNetSite



最後のコマンドレットは、プライマリ ネットワークと Azure の仮想マシンのネットワーク間のマッピングを作成します。 このコマンドレットは $Networks の最初の要素として、プライマリ ネットワークを指定します。 このコマンドレットは、仮想マシンのネットワークを、その ID を使用して $AzureVmNetworks の最初の要素として指定します。 このコマンドは、Azure サブスクリプションの ID を含みます。

    New-AzureSiteRecoveryNetworkMapping -PrimaryNetwork $Networks[0] -AzureSubscriptionId $Subscriptions[0].SubscriptionId -AzureVMNetworkId $AzureVmNetworks[0].Id


## <a name="step-9-enable-protection-for-virtual-machines"></a>ステップ 9: 仮想マシンの保護を有効化する
サーバー、クラウド、およびネットワークを正しく構成した後で、クラウド内の仮想マシンの保護を有効にすることができます。 以下の点に注意してください。

仮想マシンが [Azure 仮想マシンの前提条件](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)を満たしている必要があります。

オペレーティング システムとオペレーティング システム ディスクの保護を有効にするには、仮想マシンにプロパティを設定する必要があります。 仮想マシン テンプレートを使用して VMM 内で仮想マシンを作成する際に、プロパティを設定できます。 また、仮想マシンのプロパティの **[全般]** タブと **[ハードウェア構成]** タブで既存の仮想マシンに対してこれらのプロパティを設定することもできます。 VMM でこれらのプロパティを設定していない場合は、Azure Site Recovery ポータルで構成できます。

1. 保護を有効にするには、次のコマンドを実行して保護コンテナーを取得します。

     $ProtectionContainer = Get-AzureSiteRecoveryProtectionContainer -Name $CloudName
2. 次のコマンドを実行して、保護エンティティ (VM) を取得します。

        $protectionEntity = Get-AzureSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer



1. 次のコマンドを実行して VM の DR を有効にします。

        $jobResult = Set-AzureSiteRecoveryProtectionEntity -ProtectionEntity $protectionEntity     -Protection Enable -Force



## <a name="test-your-deployment"></a>デプロイのテスト
デプロイをテストするために、1 台の仮想マシンに対するテスト フェールオーバーを実行することや、複数の仮想マシンで構成される復旧計画を作成して、その計画のテスト フェールオーバーを実行することができます。 テスト フェールオーバーは、孤立したネットワークでフェールオーバーと復旧のシミュレーションを実行します。 以下の点に注意してください。

* フェールオーバー後に Azure でリモート デスクトップを使用して仮想マシンに接続する場合は、テストのフェールオーバーを実行する前に、仮想マシンのリモート デスクトップ接続を有効にします。
* フェールオーバー後、パブリック IP アドレスを使用して、Azure の仮想マシンにリモート デスクトップで接続します。 この接続では、パブリック アドレスを使用する仮想マシンの接続を妨げるドメイン ポリシーを使用していないことを確認してください。

操作の完了を確認するには、 [アクティビティを監視する](#monitor)の手順に従います。

### <a name="create-a-recovery-plan"></a>復旧計画の作成
1. 次のデータを使用して .xml ファイルを復旧計画のテンプレートとして作成し、「C:\RPTemplatePath.xml」として保存します。
2. RecoveryPlan ノードの Id、Name、PrimaryServerId、SecondaryServerId を変更します。
3. ProtectionEntity ノードの PrimaryProtectionEntityId (VMM の vmid) を変更します。
4. ProtectionEntity ノードを追加することでさらに VM を追加することができます。

        <#
        <?xml version="1.0" encoding="utf-16"?>
        <RecoveryPlan Id="d0323b26-5be2-471b-addc-0a8742796610" Name="rp-test"     PrimaryServerId="9350a530-d5af-435b-9f2b-b941b5d9fcd5"     SecondaryServerId="21a9403c-6ec1-44f2-b744-b4e50b792387" Description=""     Version="V2014_07">
          <Actions />
          <ActionGroups>
            <ShutdownAllActionGroup Id="ShutdownAllActionGroup">
              <PreActionSequence />
              <PostActionSequence />
            </ShutdownAllActionGroup>
            <FailoverAllActionGroup Id="FailoverAllActionGroup">
              <PreActionSequence />
              <PostActionSequence />
            </FailoverAllActionGroup>
            <BootActionGroup Id="DefaultActionGroup">
              <PreActionSequence />
              <PostActionSequence />
              <ProtectionEntity PrimaryProtectionEntityId="d4c8ce92-a613-4c63-9b03-    cf163cc36ef8" />
            </BootActionGroup>
          </ActionGroups>
          <ActionGroupSequence>
            <ActionGroup Id="ShutdownAllActionGroup" ActionId="ShutdownAllActionGroup"     Before="FailoverAllActionGroup" />
            <ActionGroup Id="FailoverAllActionGroup" ActionId="FailoverAllActionGroup"     After="ShutdownAllActionGroup" Before="DefaultActionGroup" />
            <ActionGroup Id="DefaultActionGroup" ActionId="DefaultActionGroup" After="FailoverAllActionGroup"/>
          </ActionGroupSequence>
        </RecoveryPlan>
        #>



1. テンプレートのデータを入力します。

        $TemplatePath = "C:\RPTemplatePath.xml";



1. RecoveryPlan を作成します。

        $RPCreationJob = New-AzureSiteRecoveryRecoveryPlan -File $TemplatePath -WaitForCompletion;

### <a name="run-a-test-failover"></a>テスト フェールオーバーの実行
1. 次のコマンドを実行して RecoveryPlan オブジェクトを取得します。

     $RPObject = Get-AzureSiteRecoveryRecoveryPlan -Name $RPName;
2. 次のコマンドを実行してテスト フェールオーバーを開始します。

        $jobIDResult = Start-AzureSiteRecoveryTestFailoverJob -RecoveryPlan $RPObject -Direction PrimaryToRecovery;


## <a name="a-namemonitora-monitor-activity"></a><a name=monitor></a> アクティビティを監視する
アクティビティを監視するには、次のコマンドを使用します。 ジョブの処理が終了するまで待機する必要があります。

    Do
    {
            $job = Get-AzureSiteRecoveryJob -Id $associationJob.JobId;
            Write-Host "Job State:{0}, StateDescription:{1}" -f Job.State, $job.StateDescription;
            if($job -eq $null -or $job.StateDescription -ne "Completed")
            {
                $isJobLeftForProcessing = $true;
            }

        if($isJobLeftForProcessing)
            {
                Start-Sleep -Seconds 60
            }
    }While($isJobLeftForProcessing)



## <a name="next-steps"></a>次のステップ
Azure Site Recovery PowerShell コマンドレットの詳細を[確認します](https://msdn.microsoft.com/library/dn850420.aspx)。 </a>」を参照してください。

