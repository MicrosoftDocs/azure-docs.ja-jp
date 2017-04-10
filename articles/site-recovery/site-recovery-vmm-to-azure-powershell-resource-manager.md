---
title: "Azure Site Recovery と PowerShell (Resource Manager) を使用して VMM クラウドの Hyper-V 仮想マシンをレプリケートする | Microsoft Docs"
description: "Azure Site Recovery と PowerShell を使用して VMM クラウドの Hyper-V 仮想マシンをレプリケートする"
services: site-recovery
documentationcenter: 
author: Rajani-Janaki-Ram
manager: rochakm
editor: raynew
ms.assetid: 6ac509ad-5024-43d8-b621-d8fec019b9a9
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/02/2017
ms.author: rajanaki
translationtype: Human Translation
ms.sourcegitcommit: 2c070a6f46e41023ecd2ff7fb5c39b0d021aaef0
ms.openlocfilehash: 0a900d4ddf6a751a4bf54720d3b62cf9e59e0a71
ms.lasthandoff: 02/22/2017


---
# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-azure-using-powershell-and-azure-resource-manager"></a>PowerShell と Azure Resource Manager を使用して VMM クラウドの Hyper-V 仮想マシンを Azure にレプリケートする
> [!div class="op_single_selector"]
> * [Azure App Service で Java Web アプリ](site-recovery-vmm-to-azure.md)
> * [PowerShell - Resource Manager](site-recovery-vmm-to-azure-powershell-resource-manager.md)
> * [クラシック ポータル](site-recovery-vmm-to-azure-classic.md)
> * [PowerShell - クラシック](site-recovery-deploy-with-powershell.md)
>
>

## <a name="overview"></a>Overview
Azure Site Recovery は、さまざまなデプロイ シナリオでの仮想マシンのレプリケーション、フェールオーバー、復旧を調整してビジネス継続性と障害復旧 (BCDR) 戦略に貢献します。 デプロイ シナリオのすべての一覧については、「 [Azure Site Recovery の概要](site-recovery-overview.md)」を参照してください。

この記事では、System Center VMM クラウドの Hyper-V 仮想マシンを Azure ストレージにレプリケートするように Azure Site Recovery を設定するときに実行する必要がある一般的なタスクを PowerShell を使用して自動化する方法を示します。

この記事にはシナリオの前提条件が含まれています。また、次の方法について説明しています。

* Recovery Services コンテナーを設定する
* ソース VMM サーバーに Azure Site Recovery プロバイダーをインストールする
* コンテナーにサーバーを登録し、Azure ストレージ アカウントを追加する
* Hyper-V ホスト サーバーに Azure Recovery Services エージェントをインストールする
* すべての保護された仮想マシンに適用される VMM クラウドの保護設定を構成する
* これらの仮想マシンの保護を有効にする
* すべて問題なく動作していることを確認するために、フェールオーバーをテストする

このシナリオの設定時に問題が発生した場合は、 [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)に質問を投稿してください。

> [!NOTE]
> Azure には、リソースの作成と操作に関して、 [Resource Manager とクラシック](../azure-resource-manager/resource-manager-deployment-model.md)の&2; 種類のデプロイメント モデルがあります。 この記事では、リソース マネージャーのデプロイメント モデルの使用について説明します。
>
>

## <a name="before-you-start"></a>開始する前に
次の前提条件を満たしていることを確認してください。

### <a name="azure-prerequisites"></a>Azure の前提条件
* [Microsoft Azure](https://azure.microsoft.com/) のアカウントが必要です。 アカウントがない場合は、 [無料アカウント](https://azure.microsoft.com/free)を使用できます。 また、「 [Azure Site Recovery の価格](https://azure.microsoft.com/pricing/details/site-recovery/)」もご覧ください。
* CSP サブスクリプションにレプリケートするシナリオを試す場合は、CSP サブスクリプションが必要です。 CSP プログラムの詳細については、「 [CSP プログラムに登録する](https://msdn.microsoft.com/library/partnercenter/mt156995.aspx)」をご覧ください。
* Azure にレプリケートしたデータを格納するために Azure v2 ストレージ (Resource Manager) アカウントが必要になります。 アカウントでは geo レプリケーションを有効にする必要があります。 アカウントは Azure Site Recovery サービスと同じリージョンである必要があり、同じサブスクリプションまたは CSP サブスクリプションに関連付けられている必要があります。 Azure Storage のセットアップの詳細については、「 [Microsoft Azure Storage の概要](../storage/storage-introduction.md) 」をご覧ください。
* 保護対象の仮想マシンが [Azure 仮想マシンの前提条件](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)に準拠していることを確認する必要があります。

> [!NOTE]
> 現時点では、PowerShell を使用して実行できるのは VM レベルの操作のみです。 復旧計画レベルの操作もすぐにサポートされる予定です。  現時点では、フェールオーバーの実行は、復旧計画レベルではなく、"保護された VM" レベルのみに制限されています。
>
>

### <a name="vmm-prerequisites"></a>VMM の前提条件
* System Center 2012 R2 で実行される VMM サーバーが必要です。
* 保護する仮想マシンを含むすべての VMM サーバーが Azure Site Recovery プロバイダーを実行している必要があります。 このプロバイダーは Azure Site Recovery のデプロイ時にインストールされます。
* 保護する VMM サーバーにクラウドが少なくとも&1; つ必要です。 このクラウドには以下のものが含まれている必要があります。
  * 1 つ以上の VMM ホスト グループ。
  * 各ホスト グループ内に&1; つ以上の Hyper-V ホスト サーバーまたはクラスター。
  * ソース Hyper-V サーバー上に配置された&1; つ以上の仮想マシン。
* VMM クラウドの設定について理解を深めます。
  * プライベート VMM クラウドの詳細については、[System Center 2012 R2 VMM のプライベート クラウドの新機能に関するページ](http://go.microsoft.com/fwlink/?LinkId=324952)および [VMM 2012 とクラウドに関するページ](http://go.microsoft.com/fwlink/?LinkId=324956)を参照してください。
  * [VMM クラウド ファブリックの構成に関するページ](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric)
  * クラウド ファブリック要素を配置したら、「[VMM でのプライベート クラウドの作成](http://go.microsoft.com/fwlink/p/?LinkId=324953)」および「[Walkthrough: Creating private clouds with System Center 2012 SP1 VMM (チュートリアル: System Center 2012 SP1 VMM でのプライベート クラウドの作成)](http://go.microsoft.com/fwlink/p/?LinkId=324954)」で、プライベート クラウドの作成方法を確認してください。

### <a name="hyper-v-prerequisites"></a>Hyper-V の前提条件
* ホスト Hyper-V サーバーは、Hyper-V ロールを持つ **Windows Server 2012** 以降か、**Microsoft Hyper-V Server 2012** が実行され、最新の更新プログラムがインストールされている必要があります。
* クラスターで Hyper-V を実行している場合に、静的 IP アドレス ベースのクラスターが存在すると、クラスター ブローカーが自動的に作成されません。 クラスター ブローカーを手動で構成する必要があります。 次に
* 手順については、「 [How to Configure Hyper-V Replica Broker (Hyper-V レプリカ ブローカーを構成する方法)](http://blogs.technet.com/b/haroldwong/archive/2013/03/27/server-virtualization-series-hyper-v-replica-broker-explained-part-15-of-20-by-yung-chou.aspx)」をご覧ください。
* 保護を管理するすべての Hyper-V ホスト サーバーまたはクラスターが VMM クラウドに属している必要があります。

### <a name="network-mapping-prerequisites"></a>ネットワーク マッピングの前提条件
仮想マシンを Azure で保護する場合、ネットワーク マッピングでソース VMM サーバー上の仮想マシン ネットワークとターゲットの Azure ネットワークをマップして以下を実現します。

* 同じネットワーク上でフェールオーバーするすべてのマシンは、どの復旧計画に含まれていても、相互に接続できる。
* ターゲットの Azure ネットワークでネットワーク ゲートウェイをセットアップすると、仮想マシンを他のオンプレミスの仮想マシンに接続できる。
* ネットワーク マッピングを構成しない場合、Azure へのフェールオーバー後、同じ復旧計画でフェールオーバーする仮想マシンのみ相互に接続できる。

ネットワーク マッピングをデプロイする場合は、以下のことが必要になります。

* ソース VMM サーバー上の保護する仮想マシンが VM ネットワークに接続している。 そのネットワークは、クラウドに関連付けられた論理ネットワークにリンクされている必要があります。
* レプリケートされた仮想マシンがフェールオーバー後に接続できる Azure ネットワーク。 フェールオーバー時にこのネットワークを選択します。 ネットワークは Azure Site Recovery サブスクリプションと同じリージョンにある必要があります。

ネットワーク マッピングの詳細については、以下を参照してください。

* [VMM での論理ネットワークの構成の概要](http://go.microsoft.com/fwlink/p/?LinkId=386307)
* [VMM での VM ネットワークとゲートウェイの構成](http://go.microsoft.com/fwlink/p/?LinkId=386308)
* [How to configure and monitor virtual networks in Azure (Azure で仮想ネットワークを構成および監視する方法)](https://azure.microsoft.com/documentation/services/virtual-network/)

### <a name="powershell-prerequisites"></a>PowerShell の前提条件
Azure PowerShell を使用する準備が整っていることを確認してください。 PowerShell を使用している場合は、0.8.10 以降のバージョンにアップグレードする必要があります。 PowerShell の設定については、 [Azure PowerShell のインストールと構成](/powershell/azureps-cmdlets-docs)に関するページをご覧ください。 PowerShell を設定して構成したら、サービスで使用可能なすべてのコマンドレットを [ここ](https://msdn.microsoft.com/library/dn850420.aspx)に表示できます。

Azure PowerShell でのパラメーター値、入力、出力の一般的な処理方法など、コマンドレットの使用に役立つヒントについては、「 [Azure コマンドレットの概要](https://msdn.microsoft.com/library/azure/jj554332.aspx)」をご覧ください。

## <a name="step-1-set-the-subscription"></a>ステップ 1: サブスクリプションを設定する
1. Azure PowerShell から次のコマンドレットを使用して、Azure アカウントにログインします。

        $UserName = "<user@live.com>"
        $Password = "<password>"
        $SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
        $Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $SecurePassword
        Login-AzureRmAccount #-Credential $Cred
2. サブスクリプションの一覧を取得します。 各サブスクリプションの subscriptionID も表示されます。 Recovery Services コンテナーを作成するサブスクリプションの subscriptionID をメモします。

        Get-AzureRmSubscription
3. サブスクリプション ID を指定して、Recovery Services コンテナーを作成するサブスクリプションを設定します。

        Set-AzureRmContext –SubscriptionID <subscriptionId>

## <a name="step-2-create-a-recovery-services-vault"></a>ステップ 2: Recovery Services コンテナーを作成する
1. Azure Resource Manager にリソース グループを作成します (まだ存在しない場合)。

        New-AzureRmResourceGroup -Name #ResourceGroupName -Location #location
2. 新しい Recovery Services コンテナーを作成し、作成した ASR コンテナー オブジェクトを変数に格納します (後で使用します)。 Get-AzureRMRecoveryServicesVault コマンドレットを使用して、ASR コンテナー オブジェクトの post 作成を取得することもできます。

        $vault = New-AzureRmRecoveryServicesVault -Name #vaultname -ResouceGroupName #ResourceGroupName -Location #location

## <a name="step-3-set-the-recovery-services-vault-context"></a>ステップ 3: Recovery Services コンテナーのコンテキストを設定する

次のコマンドを実行して、コンテナーのコンテキストを設定します。

       Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault

## <a name="step-4-install-the-azure-site-recovery-provider"></a>ステップ 4: Azure Site Recovery プロバイダーをインストールする
1. VMM マシンで、次のコマンドを実行してディレクトリを作成します。

       New-Item c:\ASR -type directory
2. 次のコマンドを実行して、ダウンロードしたプロバイダーを使用して、ファイルを抽出します。

       pushd C:\ASR\
       .\AzureSiteRecoveryProvider.exe /x:. /q
3. 次のコマンドを使用してプロバイダーをインストールします。

       .\SetupDr.exe /i
       $installationRegPath = "hklm:\software\Microsoft\Microsoft System Center Virtual Machine Manager Server\DRAdapter"
       do
       {
         $isNotInstalled = $true;
         if(Test-Path $installationRegPath)
         {
           $isNotInstalled = $false;
         }
       }While($isNotInstalled)

   インストールが完了するのを待ちます。
4. 次のコマンドを使用して、コンテナーにサーバーを登録します。

       $BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
       pushd $BinPath
       $encryptionFilePath = "C:\temp\".\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice

## <a name="step-5-create-an-azure-storage-account"></a>ステップ 5: Azure のストレージ アカウントを作成する

Azure ストレージ アカウントを持っていない場合、次のコマンドを実行して、コンテナーと同じ geo 内に geo レプリケーションが有効になっているアカウントを作成します。

        $StorageAccountName = "teststorageacc1"    #StorageAccountname
        $StorageAccountGeo  = "Southeast Asia"     
        $ResourceGroupName =  “myRG”             #ResourceGroupName
        $RecoveryStorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -Type “StandardGRS” -Location $StorageAccountGeo

ストレージ アカウントは Azure Site Recovery サービスと同じリージョンである必要があり、同じサブスクリプションに関連付けられている必要があります。

## <a name="step-6-install-the-azure-recovery-services-agent"></a>ステップ 6: Azure Recovery Services エージェントをインストールする
1. [http://aka.ms/latestmarsagent](http://aka.ms/latestmarsagent) から Azure Recovery Services エージェントをダウンロードし、保護する VMM クラウドに配置されている各 Hyper-V ホスト サーバーにインストールします。
2. すべての VMM ホストで次のコマンドを実行します。

       marsagentinstaller.exe /q /nu

## <a name="step-7-configure-cloud-protection-settings"></a>ステップ 7: クラウドの保護設定を構成する
1. 次のコマンドを実行して、Azure にレプリケーション ポリシーを作成します。

        $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
        $PolicyName = “replicapolicy”
        $Recoverypoints = 6                    #specify the number of recovery points

        $policryresult = New-AzureRmSiteRecoveryPolicy -Name $policyname -ReplicationProvider HyperVReplicaAzure -ReplicationFrequencyInSeconds $replicationfrequencyinseconds -RecoveryPoints $recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId "/subscriptions/q1345667/resourceGroups/test/providers/Microsoft.Storage/storageAccounts/teststorageacc1"

1. 次のコマンドを実行して、保護コンテナーを取得します。

       $PrimaryCloud = "testcloud"
       $protectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloud;  
2. 作成されたジョブを使用し、ポリシーのフレンドリ名を指定して、ポリシーの詳細を変数に格納します。

       $policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $policyname
3. 保護コンテナーとレプリケーション ポリシーの関連付けを開始します。

       $associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy     $Policy -PrimaryProtectionContainer $protectionContainer  
4. ジョブが完了したら、次のコマンドを実行します。

       $job = Get-AzureRmSiteRecoveryJob -Job $associationJob

       if($job -eq $null -or $job.StateDescription -ne "Completed")
       {
         $isJobLeftForProcessing = $true;
       }
5. ジョブの処理が完了したら、次のコマンドを実行します。

       if($isJobLeftForProcessing)
       {
         Start-Sleep -Seconds 60
       }
       }While($isJobLeftForProcessing)

操作の完了を確認するには、 [アクティビティを監視する](#monitor)の手順に従います。

## <a name="step-8-configure-network-mapping"></a>ステップ 8: ネットワーク マッピングを構成する
ネットワーク マッピングを開始する前に、ソース VMM サーバー上の仮想マシンが VM ネットワークに接続されていることを確認してください。 さらに、1 つまたは複数の Azure 仮想ネットワークを作成します。

Azure Resource Manager と PowerShell を使用して仮想ネットワークを作成する方法の詳細については、「 [PowerShell と Azure Resource Manager を使用してサイト間 VPN 接続で仮想ネットワークを作成する](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)

複数の仮想マシン ネットワークを&1; つの Azure ネットワークにマップできることに注意してください。 ターゲット ネットワークに複数のサブネットがあり、そのサブネットのいずれかが、ソースの仮想マシンが配置されているサブネットと同じ名前である場合、フェールオーバー後、レプリカの仮想マシンはそのターゲット サブネットに接続されます。 ターゲットのサブネットで名前が一致するものがなければ、仮想マシンはネットワークの最初のサブネットに接続されます。

1. 最初のコマンドは、現在の Azure Site Recovery コンテナーのサーバーを取得します。 このコマンドは、$Servers 配列変数に、Microsoft Azure Site Recovery のサーバーを格納します。

        $Servers = Get-AzureRmSiteRecoveryServer
2. 2 番目のコマンドは、$Servers 配列内の最初のサーバーの Site Recovery ネットワークを取得します。 コマンドは、$Networks 変数に、ネットワークを格納します。

        $Networks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[0]

1. 3 番目のコマンドは、Azure の仮想ネットワークを取得し、$AzureVmNetworks 変数にその値を格納します。

        $AzureVmNetworks =  Get-AzureRmVirtualNetwork
2. 最後のコマンドレットは、プライマリ ネットワークと Azure の仮想マシンのネットワーク間のマッピングを作成します。 このコマンドレットは $Networks の最初の要素として、プライマリ ネットワークを指定します。 このコマンドレットは、仮想マシンのネットワークを $AzureVmNetworks の最初の要素として指定します。

        New-AzureRmSiteRecoveryNetworkMapping -PrimaryNetwork $Networks[0] -AzureVMNetworkId $AzureVmNetworks[0]

## <a name="step-9-enable-protection-for-virtual-machines"></a>ステップ 9: 仮想マシンの保護を有効化する
サーバー、クラウド、ネットワークを正しく構成したら、クラウド内の仮想マシンの保護を有効にすることができます。

 以下の点に注意してください。

* 仮想マシンは Azure 要件を満たしている必要があります。 計画ガイドの [前提条件とサポート](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements) に関するページで確認してください。
* オペレーティング システムとオペレーティング システム ディスクの保護を有効にするには、仮想マシンのプロパティを設定する必要があります。 仮想マシン テンプレートを使用して VMM 内で仮想マシンを作成する際に、プロパティを設定できます。 また、仮想マシンのプロパティの **[全般]** タブと **[ハードウェア構成]** タブで既存の仮想マシンに対してこれらのプロパティを設定することもできます。 VMM でこれらのプロパティを設定していない場合は、Azure Site Recovery ポータルで構成できます。

1. 保護を有効にするには、次のコマンドを実行して保護コンテナーを取得します。

          $ProtectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $CloudName
2. 次のコマンドを実行して、保護エンティティ (VM) を取得します。

           $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -friendlyName $VMName -ProtectionContainer $protectionContainer
3. 次のコマンドを実行して VM の DR を有効にします。

          $jobResult = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionentity -Protection Enable –Force -Policy $policy -RecoveryAzureStorageAccountId  $storageID "/subscriptions/217653172865hcvkchgvd/resourceGroups/rajanirgps/providers/Microsoft.Storage/storageAccounts/teststorageacc1

## <a name="test-your-deployment"></a>デプロイのテスト
デプロイをテストするために、1 台の仮想マシンに対するテスト フェールオーバーを実行することや、複数の仮想マシンで構成される復旧計画を作成して、その計画のテスト フェールオーバーを実行することができます。 テスト フェールオーバーは、分離されたネットワークでフェールオーバーと復旧のシミュレーションを実行します。 以下の点に注意してください。

* フェールオーバー後に Azure でリモート デスクトップを使用して仮想マシンに接続する場合は、テスト フェールオーバーを実行する前に、仮想マシンのリモート デスクトップ接続を有効にします。
* フェールオーバー後、パブリック IP アドレスを使用して、Azure の仮想マシンにリモート デスクトップで接続します。 この接続では、パブリック アドレスを使用する仮想マシンの接続を妨げるドメイン ポリシーを使用していないことを確認してください。

操作の完了を確認するには、 [アクティビティを監視する](#monitor)の手順に従います。

### <a name="run-a-test-failover"></a>テスト フェールオーバーの実行
- 次のコマンドを実行してテスト フェールオーバーを開始します。

       $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer

       $jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -AzureVMNetworkId <string>  

### <a name="run-a-planned-failover"></a>計画されたフェールオーバーの実行
- 次のコマンドを実行して計画されたフェールオーバーを開始します。

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -AzureVMNetworkId <string>  

### <a name="run-an-unplanned-failover"></a>計画されていないフェールオーバーの実行
- 次のコマンドを実行して計画外のフェールオーバーを開始します。

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -AzureVMNetworkId <string>  

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
[詳細を確認](https://msdn.microsoft.com/library/azure/mt637930.aspx) します。

