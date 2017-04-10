---
title: "PowerShell を使用して VMM 内の Hyper-V VM をセカンダリ サイトにレプリケートする (Azure Resource Manager) | Microsoft Docs"
description: "PowerShell (Resource Manager) を使用して、Azure Site Recovery をデプロイし、VMM クラウド内の Hyper-V VM の、セカンダリ VMM サイトへのレプリケーション、フェールオーバー、復旧を調整する方法について説明します"
services: site-recovery
documentationcenter: 
author: sujaytalasila
manager: rochakm
editor: raynew
ms.assetid: 9d38e9c3-217c-4e44-830c-575e9a4141f2
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: sutalasi
translationtype: Human Translation
ms.sourcegitcommit: 0400369eb7ae3a2ebd506605b50afe08fe563d22
ms.openlocfilehash: 33b3e7322afafd623a10661e33abe7b959eeb512
ms.lasthandoff: 02/21/2017


---
# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-a-secondary-vmm-site-using-powershell-resource-manager"></a>PowerShell (Resource Manager) を使用して VMM クラウド内の Hyper-V 仮想マシンをセカンダリ VMM サイトにレプリケートする
> [!div class="op_single_selector"]
> * [Azure ポータル](site-recovery-vmm-to-vmm.md)
> * [クラシック ポータル](site-recovery-vmm-to-vmm-classic.md)
> * [PowerShell - Resource Manager](site-recovery-vmm-to-vmm-powershell-resource-manager.md)
>
>

Azure Site Recovery へようこそ。 この記事では、System Center Virtual Machine Manager (VMM) クラウドで管理されているオンプレミスの Hyper-V 仮想マシンをセカンダリ サイトにレプリケートする方法について説明します。

この記事では、System Center VMM クラウドの Hyper-V 仮想マシンを、セカンダリ サイトの System Center VMM クラウドにレプリケートするように Azure Site Recovery を設定するときに実行する必要がある一般的なタスクを、PowerShell を使用して自動化する方法を示します。

この記事にはシナリオの前提条件が含まれています。また、次の方法について説明しています。

* Recovery Services コンテナーを設定する
* ソース VMM サーバーとターゲット VMM サーバーに Azure Site Recovery プロバイダーをインストールする
* VMM サーバーをコンテナーに登録する
* VMM クラウドのレプリケーション ポリシーを構成する。 ポリシーのレプリケーション設定は、保護された仮想マシンすべてに適用されます
* 仮想マシンの保護を有効にする。
* VM のフェールオーバーを個別に、または復旧計画の一環としてテストし、すべてが想定どおりに動作していることを確認する。
* VM の計画されたフェールオーバーまたは計画されていないフェールオーバーを、個別に、または復旧計画の一環として実行し、すべてが想定どおりに動作していることを確認する。

このシナリオの設定時に問題が発生した場合は、 [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)に質問を投稿してください。

> [!NOTE]
> Azure には、リソースの作成と操作に関して、Azure Resource Manager とクラシックの&2; 種類の[デプロイメント モデル](../azure-resource-manager/resource-manager-deployment-model.md)があります。 また、Azure にも&2; つのポータルがあります。クラシック デプロイメント モデルをサポートする Azure クラシック ポータルと、両方のデプロイメント モデルをサポートする Azure ポータルです。 この記事では、リソース マネージャーのデプロイ モデルについて説明します。
>
>

## <a name="on-premises-prerequisites"></a>オンプレミスの前提条件
このシナリオをデプロイするためにプライマリとセカンダリのオンプレミス サイトで必要となるものを次に示します。

| **前提条件** | **詳細** |
| --- | --- |
| **VMM** |プライマリ サイトとセカンダリ サイトに VMM サーバーを&1; 台ずつデプロイすることをお勧めします。<br/><br/> また、[単一の VMM サーバー上のクラウド間でレプリケートする](site-recovery-vmm-to-vmm.md#prepare-for-single-server-deployment)こともできます。 これを行うには、VMM サーバーに少なくとも&2; つのクラウドが構成されている必要があります。<br/><br/> VMM サーバーは、最新の更新プログラムがインストールされている System Center 2012 SP1 以降を実行している必要があります。<br/><br/> 各 VMM サーバーには&1; つ以上のクラウドが構成され、すべてのクラウドには Hyper-V キャパシティ プロファイルが設定されている必要があります。 <br/><br/>クラウドには、1 つ以上の VMM ホスト グループが含まれている必要があります。<br/><br/>VMM クラウドの設定の詳細については、[VMM クラウド ファブリックの構成](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric)に関するページ、および [System Center 2012 SP1 VMM を使用したプライベート クラウド作成のチュートリアル](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx)をご覧ください。<br/><br/> VMM サーバーにはインターネット アクセスが必要です。 |
| **Hyper-V** |Hyper-V サーバーは、Hyper-V ロールがインストールされた Windows Server 2012 以降が実行され、最新の更新プログラムがインストールされている必要があります。<br/><br/> Hyper-V サーバーに&1; つ以上の VM が含まれている必要があります。<br/><br/>  Hyper-V ホスト サーバーが、プライマリおよびセカンダリの VMM クラウドに配置されている必要があります。<br/><br/> Windows Server 2012 R2 のクラスターで Hyper-V を実行している場合は、[更新プログラム 2961977](https://support.microsoft.com/kb/2961977) をインストールする必要があります。<br/><br/> Windows Server 2012 上のクラスターで Hyper-V を実行している場合に、静的 IP アドレス ベースのクラスターが存在すると、クラスター ブローカーが自動的に作成されません。 クラスター ブローカーを手動で構成する必要があります。 詳細については、[こちら](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx)を参照してください。 |
| **プロバイダー** |Site Recovery をデプロイする際に Azure Site Recovery プロバイダーを VMM サーバーにインストールします。 プロバイダーは、HTTPS 443 経由で Azure Site Recovery と通信して、レプリケーションを調整します。 データのレプリケーションは、LAN または VPN 接続を経由してプライマリとセカンダリの Hyper-V サーバー間で実行されます。<br/><br/> VMM サーバー上で実行されるプロバイダーでは、*.hypervrecoverymanager.windowsazure.com、*.accesscontrol.windows.net、*.backup.windowsazure.com、*.blob.core.windows.net、*.store.core.windows.net の各 URL へのアクセスを許可する必要があります。<br/><br/> また、VMM サーバーから [Azure データセンターの IP 範囲](https://www.microsoft.com/download/confirmation.aspx?id=41653)へのファイアウォール通信と、HTTPS (443) プロトコルの使用を許可する必要があります。 |

### <a name="network-mapping-prerequisites"></a>ネットワーク マッピングの前提条件
ネットワーク マッピングは、プライマリ VMM サーバーとセカンダリ VMM サーバーの VMM VM ネットワークをマップすることで、次の項目を実現します。

* フェールオーバー後にセカンダリ Hyper-V ホストにレプリカ VM を最適に配置します。
* レプリカ VM を適切な VM ネットワークに接続します。
* ネットワーク マッピングを構成しない場合、レプリカ VM はフェールオーバー後にどの VM ネットワークにも接続されません。
* Site Recovery のデプロイ中にネットワーク マッピングをセットアップする場合は、次のことを実行する必要があります。

  * ソース Hyper-V ホスト サーバー上の VM が VMM VM ネットワークに接続されていることを確認します。 そのネットワークは、クラウドに関連付けられた論理ネットワークにリンクされている必要があります。
  * 回復に使用するセカンダリ クラウドに、対応する VM ネットワークが構成されていることを確認します。 この VM ネットワークは、セカンダリ クラウドに関連付けられた論理ネットワークにリンクされている必要があります。

VMM ネットワークの構成の詳細については、次の記事を参照してください

* [VMM での論理ネットワークの構成の概要](http://go.microsoft.com/fwlink/p/?LinkId=386307)
* [VMM での VM ネットワークとゲートウェイの構成](http://go.microsoft.com/fwlink/p/?LinkId=386308)

[こちら](site-recovery-vmm-to-vmm.md#prepare-for-network-mapping) をご覧ください。

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
1. Azure Resource Manager リソース グループを作成します (まだ存在しない場合)

        New-AzureRmResourceGroup -Name #ResourceGroupName -Location #location
2. 新しい Recovery Services コンテナーを作成し、作成した ASR コンテナー オブジェクトを変数に格納します (後で使用します)。 Get-AzureRMRecoveryServicesVault コマンドレットを使用して、ASR コンテナー オブジェクトの post 作成を取得することもできます。

        $vault = New-AzureRmRecoveryServicesVault -Name #vaultname -ResouceGroupName #ResourceGroupName -Location #location

## <a name="step-3-set-the-recovery-services-vault-context"></a>ステップ 3: Recovery Services コンテナーのコンテキストを設定する
1. 既にコンテナーが作成されている場合は、次のコマンドを実行してコンテナーを取得します。

       $vault = Get-AzureRmRecoveryServicesVault -Name #vaultname
2. 次のコマンドを実行して、コンテナーのコンテキストを設定します。

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

## <a name="step-5-create-and-associate-a-replication-policy"></a>ステップ 5: レプリケーション ポシリーを作成して関連付ける
1. 次のコマンドを実行して、Hyper-V 2012 R2 レプリケーション ポリシーを作成します。

        $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
        $PolicyName = “replicapolicy”
        $RepProvider = HyperVReplica2012R2
        $Recoverypoints = 24                    #specify the number of hours to retain recovery pints
        $AppConsistentSnapshotFrequency = 4 #specify the frequency (in hours) at which app consistent snapshots are taken
        $AuthMode = "Kerberos"  #options are "Kerberos" or "Certificate"
        $AuthPort = "8083"  #specify the port number that will be used for replication traffic on Hyper-V hosts
        $InitialRepMethod = "Online" #options are "Online" or "Offline"

        $policyresult = New-AzureRmSiteRecoveryPolicy -Name $policyname -ReplicationProvider $RepProvider -ReplicationFrequencyInSeconds $Replicationfrequencyinseconds -RecoveryPoints $recoverypoints -ApplicationConsistentSnapshotFrequencyInHours $AppConsistentSnapshotFrequency -Authentication $AuthMode -ReplicationPort $AuthPort -ReplicationMethod $InitialRepMethod

    > [!NOTE]
    > VMM クラウドには、(Hyper-V の前提条件で説明したように) さまざまなバージョンの Windows Server が実行されている Hyper-V ホストを含めることができますが、レプリケーション ポリシーは OS バージョンによって異なります。 さまざまなホストがあり、さまざまなバージョンのオペレーティング システムが実行されている場合は、OS バージョンごとにレプリケーション ポリシーを作成してください。 たとえば、Windows Server 2012 で実行されているホストが 5 台、Windows Server 2012 R2 のホストが 3 台ある場合は、それぞれのオペレーティング システム バージョンに対して、つまり 2 つのレプリケーション ポリシーを作成します。

1. 次のコマンドを実行して、プライマリ保護コンテナー (プライマリ VMM クラウド) と復旧保護コンテナー (復旧 VMM クラウド) を取得します。

       $PrimaryCloud = "testprimarycloud"
       $primaryprotectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloud;  

       $RecoveryCloud = "testrecoverycloud"
       $recoveryprotectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $RecoveryCloud;  
2. ポリシーのフレンドリ名を使用して、ステップ 1 で作成したポリシーを取得します

       $policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $policyname
3. 保護コンテナー (VMM クラウド) とレプリケーション ポリシーの関連付けを開始します。

       $associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy     $Policy -PrimaryProtectionContainer $primaryprotectionContainer -RecoveryProtectionContainer $recoveryprotectionContainer
4. ポリシー関連付けジョブが完了するのを待ちます。 次の PowerShell スニペットを使用して、ジョブが完了したかどうかを確認することができます。

       $job = Get-AzureRmSiteRecoveryJob -Job $associationJob

       if($job -eq $null -or $job.StateDescription -ne "Completed")
       {
         $isJobLeftForProcessing = $true;
       }

   ジョブの処理が完了したら、次のコマンドを実行します。

       if($isJobLeftForProcessing)
       {
         Start-Sleep -Seconds 60
       }
       }While($isJobLeftForProcessing)

操作の完了を確認するには、 [アクティビティを監視する](#monitor)の手順に従います。

## <a name="step-6-configure-network-mapping"></a>ステップ 6: ネットワーク マッピングの構成
1. 最初のコマンドは、現在の Azure Site Recovery コンテナーのサーバーを取得します。 このコマンドは、$Servers 配列変数に、Microsoft Azure Site Recovery のサーバーを格納します。

        $Servers = Get-AzureRmSiteRecoveryServer
2. 次のコマンドは、ソース VMM サーバーとターゲット VMM サーバーの Site Recovery ネットワークを取得します。

        $PrimaryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[0]        

        $RecoveryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[1]

    > [!NOTE]
    > ソース VMM サーバーは、サーバー配列で&1; つ目または&2; つ目として指定できます。 VMM サーバーの名前を確認し、それに応じてネットワークを取得してください


1. 最後のコマンドレットは、プライマリ ネットワークと復旧ネットワークの間のマッピングを作成します。 このコマンドレットでは、プライマリ ネットワークを $PrimaryNetworks の最初の要素として、復旧ネットワークを $RecoveryNetworks の最初の要素として指定します。

        New-AzureRmSiteRecoveryNetworkMapping -PrimaryNetwork $PrimaryNetworks[0] -RecoveryNetwork $RecoveryNetworks[0]

## <a name="step-7-configure-storage-mapping"></a>ステップ 7: ストレージ マッピングを構成する
1. 次のコマンドは、記憶域の分類のリストを $storageclassifications 変数に格納します。

        $storageclassifications = Get-AzureRmSiteRecoveryStorageClassification
2. 次のコマンドは、ソースの分類を $SourceClassificaion 変数に格納し、ターゲットの分類を $TargetClassification 変数に格納します。

        $SourceClassificaion = $storageclassifications[0]

        $TargetClassification = $storageclassifications[1]

    > [!NOTE]
    > ソースとターゲットの分類には、配列内の任意の要素を指定できます。 $storageclassifications 配列内のソースとターゲットの分類のインデックスを確認するには、次のコマンドの出力を参照してください。

    > Get-AzureRmSiteRecoveryStorageClassification | Select-Object -Property FriendlyName, Id | Format-Table


1. 次のコマンドレットは、ソースの分類とターゲットの分類間のマッピングを作成します。

        New-AzureRmSiteRecoveryStorageClassificationMapping -PrimaryStorageClassification $SourceClassificaion -RecoveryStorageClassification $TargetClassification

## <a name="step-8-enable-protection-for-virtual-machines"></a>ステップ 8: 仮想マシンの保護を有効化する
サーバー、クラウド、ネットワークを正しく構成したら、クラウド内の仮想マシンの保護を有効にすることができます。

1. 保護を有効にするには、次のコマンドを実行して保護コンテナーを取得します。

          $PrimaryProtectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloudName
2. 次のコマンドを実行して、保護エンティティ (VM) を取得します。

           $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -friendlyName $VMName -ProtectionContainer $PrimaryProtectionContainer
3. 次のコマンドを実行して、VM のレプリケーションを有効にします。

          $jobResult = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionentity -Protection Enable -Policy $policy

## <a name="test-your-deployment"></a>デプロイのテスト
デプロイをテストするために、1 台の仮想マシンに対するテスト フェールオーバーを実行することや、複数の仮想マシンで構成される復旧計画を作成して、その計画のテスト フェールオーバーを実行することができます。 テスト フェールオーバーは、孤立したネットワークでフェールオーバーと復旧のシミュレーションを実行します。

> [!NOTE]
> Azure ポータルでアプリケーションの復旧計画を作成できます。
>
>

操作の完了を確認するには、 [アクティビティを監視する](#monitor)の手順に従います。

### <a name="run-a-test-failover"></a>テスト フェールオーバーの実行
1. 次のコマンドレットを実行して、VM のテスト フェールオーバー先の VM ネットワークを取得します。

       $Servers = Get-AzureRmSiteRecoveryServer
       $RecoveryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[1]
2. 次を実行して、VM のテスト フェールオーバーを実行します。

       $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -FriendlyName $VMName -ProtectionContainer $PrimaryprotectionContainer

       $jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -VMNetwork $RecoveryNetworks[1]
3. 次を実行して、復旧計画のテスト フェールオーバーを実行します。

       $recoveryplanname = "test-recovery-plan"

       $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

       $jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan -VMNetwork $RecoveryNetworks[1]

### <a name="run-a-planned-failover"></a>計画されたフェールオーバーの実行
1. 次を実行して、VM の計画されたフェールオーバーを実行します。

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity
2. 次を実行して、復旧計画の計画されたフェールオーバーを実行します。

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan

### <a name="run-an-unplanned-failover"></a>計画されていないフェールオーバーの実行
1. 次を実行して、VM の計画されていないフェールオーバーを実行します。

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

2. 次を実行して、復旧計画の計画されていないフェールオーバーを実行します。

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

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

