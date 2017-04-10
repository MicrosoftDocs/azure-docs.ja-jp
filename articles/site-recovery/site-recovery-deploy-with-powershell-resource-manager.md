---
title: "PowerShell と Azure Resource Manager を使用して Hyper-V VM をレプリケートする | Microsoft Docs"
description: "PowerShell とAzure Resource Manager を使用して、Azure に対する Hyper-V VM レプリケーションを Azure Site Recovery で自動化します。"
services: site-recovery
documentationcenter: 
author: bsiva
manager: abhiag
editor: 
ms.assetid: 05e0d76e-c3f5-4845-8052-094019b6d102
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: backup-recovery
ms.date: 02/06/2017
ms.author: bsiva
translationtype: Human Translation
ms.sourcegitcommit: a084cecddc2af36ee087b2e0e63a2b18b20f07f0
ms.openlocfilehash: 3df4aaa018d31e9ee9526679ac1febbe5b75bb7e
ms.lasthandoff: 02/22/2017


---
# <a name="replicate-between-on-premises-hyper-v-virtual-machines-and-azure-by-using-powershell-and-azure-resource-manager"></a>PowerShell と Azure Resource Manager を使用して、オンプレミスの Hyper-V 仮想マシンと Azure 間でレプリケートする
> [!div class="op_single_selector"]
> * [Azure ポータル](site-recovery-hyper-v-site-to-azure.md)
> * [PowerShell - Resource Manager](site-recovery-deploy-with-powershell-resource-manager.md)
> * [クラシック ポータル](site-recovery-hyper-v-site-to-azure-classic.md)
>
>

## <a name="overview"></a>Overview
Azure Site Recovery は、さまざまなデプロイ シナリオでの仮想マシンのレプリケーション、フェールオーバー、復旧を調整してビジネス継続性と障害復旧戦略に貢献します。 デプロイ シナリオのすべての一覧については、「 [Azure Site Recovery の概要](site-recovery-overview.md)」を参照してください。

Azure PowerShell は、Windows PowerShell から Azure を管理するコマンドレットを提供するモジュールです。 これを&2; 種類のモジュール (Azure Profile モジュールまたは Azure Resource Manager モジュール) と共に使用できます。

Azure 内のサーバーに対する保護と復旧は、Azure PowerShell 用 Azure Resource Manager に用意されている Site Recovery の PowerShell コマンドレットを使って行うことができます。

この記事では、Windows PowerShell と Azure Resource Manager を使って Site Recovery をデプロイし、Azure に対してサーバーの保護を構成し、調整する方法を説明します。 この記事で紹介している例は、Azure PowerShell と Azure Resource Manager を使って Hyper-V ホスト上の仮想マシンを保護し、Azure にフェールオーバーして復旧させる方法を示しています。

> [!NOTE]
> Site Recovery の PowerShell コマンドレットを使用することで、現在は、Virtual Machine Manager サイト間、Virtual Machine Manager サイトから Azure、HYPER-V サイトから Azure を構成できます。
>
>

この記事の内容を使用するには PowerShell の専門家である必要はありませんが、モジュール、コマンドレット、セッションなどの基本的な概念を理解していることを前提としています。 Windows PowerShell の詳細については、「 [Getting Started with Windows PowerShell (Windows PowerShell の概要)](http://technet.microsoft.com/library/hh857337.aspx)」を参照してください。

また、「 [Azure Resource Manager での Azure PowerShell の使用](../powershell-azure-resource-manager.md)」も参照してください。

> [!NOTE]
> クラウド ソリューション プロバイダー (CSP) プログラムに参加しているマイクロソフト パートナーは、顧客のサーバーの保護を、その顧客の各 CSP サブスクリプション (テナント サブスクリプション) に対して構成、管理することができます。
>
>

## <a name="before-you-start"></a>開始する前に
次の前提条件を満たしていることを確認してください。

* [Microsoft Azure](https://azure.microsoft.com/) アカウント。 アカウントがなくても、 [無料試用版](https://azure.microsoft.com/pricing/free-trial/)を使用できます。 また、「 [Azure Site Recovery Manager の料金](https://azure.microsoft.com/pricing/details/site-recovery/)」も参照してください。
* Azure PowerShell 1.0 今回のリリースとそのインストール方法については、「[Azure PowerShell 1.0](https://azure.microsoft.com/)」を参照してください。
* [AzureRM.SiteRecovery](https://www.powershellgallery.com/packages/AzureRM.SiteRecovery/) モジュールと [AzureRM.RecoveryServices](https://www.powershellgallery.com/packages/AzureRM.RecoveryServices/) モジュール。 最新版のモジュールを [PowerShell ギャラリー](https://www.powershellgallery.com/)

この記事では、Azure Powershell と Azure Resource Manager を使ってサーバーの保護を構成、管理する方法を紹介しています。 この記事で使用している例では、HYPER-V ホストで実行されている仮想マシンから Azure の構成での仮想マシンを保護する方法を示します。 次の前提条件は、この例のみに適用されます。 各種 Site Recovery シナリオごとの詳細な要件については、そのシナリオに関連するドキュメントを参照してください。

* 1 つ以上の仮想マシンを含む Windows Server 2012 R2 または Microsoft Hyper-V Server 2012 R2 を実行する Hyper-V ホスト。
* 直接、またはプロキシを経由して、インターネットに接続するHyper-V サーバー。
* 保護する仮想マシンは、 [仮想マシンの前提条件](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)に従う必要があります。

## <a name="step-1-sign-in-to-your-azure-account"></a>手順 1. Azure アカウントにログインする
1. PowerShell コンソールを開いて次のコマンドを実行し、Azure アカウントにログインします。 コマンドレットを実行すると Web ページが表示され、アカウントの資格情報を入力するように求められます。

        Login-AzureRmAccount

    また、`Login-AzureRmAccount` コマンドレットの `-Credential` パラメーターにアカウントの資格情報を含めて指定することもできます。

    CSP パートナーがテナントの代理としてサインインする場合は、その顧客をテナントとして指定します。該当するテナント ID またはテナントのプライマリ ドメイン名で指定してください。

        Login-AzureRmAccount -Tenant "fabrikam.com"
2. 1 つのアカウントで複数のサブスクリプションを持つことができるため、アカウントで使用するサブスクリプションを関連付ける必要があります。

        Select-AzureRmSubscription -SubscriptionName $SubscriptionName
3. 該当するサブスクリプションに対して Recovery Services 用と Site Recovery 用の Azure プロバイダーが登録され、使用できる状態になっていることを次のコマンドで確認します。

   * `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.RecoveryServices`
   * `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.SiteRecovery`

   上の 2 つのコマンドの出力で **RegistrationState** が **Registered** に設定されている場合は、手順 2. に進んでください。 設定されていない場合は、登録されていないプロバイダーを該当するサブスクリプションに登録する必要があります。

   Site Recovery および Recovery Services 用の Azure プロバイダーを登録するには、次のコマンドを実行します。

       Register-AzureRmResourceProvider -ProviderNamespace Microsoft.SiteRecovery
       Register-AzureRmResourceProvider -ProviderNamespace Microsoft.RecoveryServices

   `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.RecoveryServices` コマンドと `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.SiteRecovery` コマンドを使用して、プロバイダーが正しく登録されたことを確認します。

## <a name="step-2-set-up-the-recovery-services-vault"></a>手順 2: Recovery Services の資格情報コンテナーを設定する
1. 資格情報コンテナーの作成先となる Azure Resource Manager リソース グループを作成するか、既存のリソース グループを使用します。 新しいリソース グループを作成するには、次のコマンドを使用します。

        New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Geo  

    $ResourceGroupName 変数には、作成するリソース グループの名前が格納され、$Geo 変数には、リソース グループの作成先となる Azure リージョン (例: 「ブラジル南部」) が格納されます。

    サブスクリプションに存在する一連のリソース グループは、 `Get-AzureRmResourceGroup` コマンドレットを使用して取得できます。
2. 次のコマンドを使用して、Azure Recovery Services の新しい資格情報コンテナーを作成します。

        $vault = New-AzureRmRecoveryServicesVault -Name <string> -ResourceGroupName <string> -Location <string>

    既存の資格情報コンテナーの一覧は、`Get-AzureRmRecoveryServicesVault` コマンドレットを使用して取得できます。

> [!NOTE]
> クラシック ポータルまたは Azure サービス管理 PowerShell モジュールを使って作成した Site Recovery 資格情報コンテナーに対して操作を実行する場合は、`Get-AzureRmSiteRecoveryVault` コマンドレットを使用して、そうした資格情報コンテナーの一覧を取得できます。 新たに実行するすべての操作について、Recovery Services の資格情報コンテナーを新しく作成する必要があります。 過去に作成された Site Recovery の資格情報コンテナーもサポートされますが、最新の機能が備わっていません。
>
>

## <a name="step-3-set-the-recovery-services-vault-context"></a>手順 3. Recovery Services 資格情報コンテナーのコンテキストを設定する
1. 次のコマンドを実行して、資格情報コンテナーのコンテキストを設定します。

       Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault

## <a name="step-4-create-a-hyper-v-site-and-generate-a-new-vault-registration-key-for-the-site"></a>手順 4. Hyper-V サイトを作成し、そのサイト用に新しい資格情報コンテナー登録キーを生成する
1. 次のコマンドを使用して、新しい Hyper-V サイトを作成します。

        $sitename = "MySite"                #Specify site friendly name
        New-AzureRmSiteRecoverySite -Name $sitename

    このコマンドレットは Site Recovery ジョブを起動してサイトを作成し、Site Recovery ジョブ オブジェクトを返します。 ジョブの完了を待って、ジョブが正常に完了したことを確認します。

    Get-AzureRmSiteRecoveryJob コマンドレットを使用して、ジョブ オブジェクトを取得し、現在のジョブ ステータスをチェックできます。
2. 次のように、サイトの登録キーを生成してダウンロードします。

        $SiteIdentifier = Get-AzureRmSiteRecoverySite -Name $sitename | Select -ExpandProperty SiteIdentifier
        Get-AzureRmRecoveryServicesVaultSettingsFile -Vault $vault -SiteIdentifier $SiteIdentifier -SiteFriendlyName $sitename -Path $Path

    ダウンロードしたキーを Hyper-V ホストにコピーします。 このキーは、Hyper-V ホストをサイトに登録するときに必要になります。

## <a name="step-5-install-the-azure-site-recovery-provider-and-azure-recovery-services-agent-on-your-hyper-v-host"></a>手順 5. Azure Site Recovery プロバイダーと Azure Recovery Services エージェントを Hyper-V ホストにインストールする
1. 最新版のプロバイダーのインストーラーを [マイクロソフト](https://aka.ms/downloaddra)からダウンロードします。
2. Hyper-V ホストでインストーラーを実行し、インストールの完了時に、そのまま登録手順に進みます。
3. サイト登録キーを求められたら、ダウンロードしたキーを指定し、サイトに対する Hyper-V ホストの登録を完了します。
4. 次のコマンドを使用して、Hyper-V ホストがサイトに登録されたことを確認します。

        $server =  Get-AzureRmSiteRecoveryServer -FriendlyName $server-friendlyname

## <a name="step-6-create-a-replication-policy-and-associate-it-with-the-protection-container"></a>手順 6. レプリケーション ポリシーを作成して保護コンテナーに関連付ける
1. 次のように、レプリケーション ポリシーを作成します。

        $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
        $PolicyName = “replicapolicy”
        $Recoverypoints = 6                    #specify the number of recovery points
        $storageaccountID = Get-AzureRmStorageAccount -Name "mystorea" -ResourceGroupName "MyRG" | Select -ExpandProperty Id

        $PolicyResult = New-AzureRmSiteRecoveryPolicy -Name $PolicyName -ReplicationProvider “HyperVReplicaAzure” -ReplicationFrequencyInSeconds $ReplicationFrequencyInSeconds  -RecoveryPoints $Recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId $storageaccountID

    返されたジョブをチェックし、レプリケーション ポリシーが正常に作成されたことを確認します。

   > [!IMPORTANT]
   > 指定するストレージ アカウントは、Recovery Services の資格情報コンテナーと同じ Azure リージョンに存在すること、また geo レプリケーションが有効になっていることが必要です。
   >
   > * 指定した Recovery ストレージ アカウントのタイプが Azure Storage (クラシック) である場合、保護対象マシンのフェールオーバー後の復旧先は Azure IaaS (クラシック) となります。
   > * 指定した Recovery ストレージ アカウントのタイプが Azure Storage (Azure Resource Manager) である場合、保護対象マシンのフェールオーバー後の復旧先は Azure IaaS (Azure Resource Manager) となります。
   >
   >
2. 次のように、サイトに対応する保護コンテナーを取得します。

        $protectionContainer = Get-AzureRmSiteRecoveryProtectionContainer
3. 次のように、保護コンテナーとレプリケーション ポリシーの関連付けを開始します。

     $Policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $PolicyName   $associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy $Policy -PrimaryProtectionContainer $protectionContainer

   関連付けジョブの完了を待って、正常に完了したことを確認します。

## <a name="step-7-enable-protection-for-virtual-machines"></a>ステップ 7: 仮想マシンの保護を有効化する
1. 次のように、保護する VM に対応する保護エンティティを取得します。

        $VMFriendlyName = "Fabrikam-app"                    #Name of the VM
        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName
2. 次のように、仮想マシンの保護を開始します。

        $Ostype = "Windows"                                 # "Windows" or "Linux"
        $DRjob = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionEntity -Policy $Policy -Protection Enable -RecoveryAzureStorageAccountId $storageaccountID  -OS $OStype -OSDiskName $protectionEntity.Disks[0].Name

   > [!IMPORTANT]
   > 指定するストレージ アカウントは、Recovery Services の資格情報コンテナーと同じ Azure リージョンに存在すること、また geo レプリケーションが有効になっていることが必要です。
   >
   > * 指定した Recovery ストレージ アカウントのタイプが Azure Storage (クラシック) である場合、保護対象マシンのフェールオーバー後の復旧先は Azure IaaS (クラシック) となります。
   > * 指定した Recovery ストレージ アカウントのタイプが Azure Storage (Azure Resource Manager) である場合、保護対象マシンのフェールオーバー後の復旧先は Azure IaaS (Azure Resource Manager) となります。
   >
   > 保護対象の VM に複数のディスクが接続されている場合は、 *OSDiskName* パラメーターを使ってオペレーティング システム ディスクを指定します。
   >
   >
3. 初回レプリケーション後、仮想マシンが保護された状態になるまで待ちます。 これには、レプリケートされるデータ量と Azure で使用できるアップストリーム帯域幅などの要因に応じて、しばらく時間がかかることがあります。 ジョブの State および StateDescription は、VM が保護された状態に到達すると、次のように更新されます。

        PS C:\> $DRjob = Get-AzureRmSiteRecoveryJob -Job $DRjob

        PS C:\> $DRjob | Select-Object -ExpandProperty State
        Succeeded

        PS C:\> $DRjob | Select-Object -ExpandProperty StateDescription
        Completed
4. フェールオーバーの発生時に仮想マシンのネットワーク インターフェイス カードの接続先となる Azure ネットワークや VM ロール サイズなど、復旧のプロパティを更新します。

        PS C:\> $nw1 = Get-AzureRmVirtualNetwork -Name "FailoverNw" -ResourceGroupName "MyRG"

        PS C:\> $VMFriendlyName = "Fabrikam-App"

        PS C:\> $VM = Get-AzureRmSiteRecoveryVM -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

        PS C:\> $UpdateJob = Set-AzureRmSiteRecoveryVM -VirtualMachine $VM -PrimaryNic $VM.NicDetailsList[0].NicId -RecoveryNetworkId $nw1.Id -RecoveryNicSubnetName $nw1.Subnets[0].Name

        PS C:\> $UpdateJob = Get-AzureRmSiteRecoveryJob -Job $UpdateJob

        PS C:\> $UpdateJob

        Name             : b8a647e0-2cb9-40d1-84c4-d0169919e2c5
        ID               : /Subscriptions/a731825f-4bf2-4f81-a611-c331b272206e/resourceGroups/MyRG/providers/Microsoft.RecoveryServices/vault
                           s/MyVault/replicationJobs/b8a647e0-2cb9-40d1-84c4-d0169919e2c5
        Type             : Microsoft.RecoveryServices/vaults/replicationJobs
        JobType          : UpdateVmProperties
        DisplayName      : Update the virtual machine
        ClientRequestId  : 805a22a3-be86-441c-9da8-f32685673112-2015-12-10 17:55:51Z-P
        State            : Succeeded
        StateDescription : Completed
        StartTime        : 10-12-2015 17:55:53 +00:00
        EndTime          : 10-12-2015 17:55:54 +00:00
        TargetObjectId   : 289682c6-c5e6-42dc-a1d2-5f9621f78ae6
        TargetObjectType : ProtectionEntity
        TargetObjectName : Fabrikam-App
        AllowedActions   : {Restart}
        Tasks            : {UpdateVmPropertiesTask}
        Errors           : {}



## <a name="step-8-run-a-test-failover"></a>ステップ 8: テスト フェールオーバーを実行する
1. 次のように、テスト フェールオーバー ジョブを実行します。

        $nw = Get-AzureRmVirtualNetwork -Name "TestFailoverNw" -ResourceGroupName "MyRG" #Specify Azure vnet name and resource group

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -FriendlyName $VMFriendlyName -ProtectionContainer $protectionContainer

        $TFjob = Start-AzureRmSiteRecoveryTestFailoverJob -ProtectionEntity $protectionEntity -Direction PrimaryToRecovery -AzureVMNetworkId $nw.Id
2. Azure でテスト VM が作成されていることを確認します。 (テスト フェールオーバー ジョブは、Azure にテスト VM を作成した後に中断されます。 次の手順に示すようにジョブを再開すると、作成されたアーティファクトがクリーンアップされてジョブが完了します)。
3. 次のようにテスト フェールオーバーを完了します。

        $TFjob = Resume-AzureRmSiteRecoveryJob -Job $TFjob

## <a name="next-steps"></a>次のステップ
[詳細を確認します](https://msdn.microsoft.com/library/azure/mt637930.aspx) 。

