---
title: Azure Site Recovery と PowerShell を使用する Hyper-V VM ディザスター リカバリー
description: PowerShell と Azure Resource Manager を使用して、Azure に対する Hyper-V VM のディザスター リカバリーを Azure Site Recovery サービスで自動化します。
author: sujayt
manager: rochakm
ms.topic: article
ms.date: 01/10/2020
ms.author: sutalasi
ms.openlocfilehash: a4140a0b22f7ca8164d50cf60fe57c861f826eb4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86132514"
---
# <a name="set-up-disaster-recovery-to-azure-for-hyper-v-vms-using-powershell-and-azure-resource-manager"></a>PowerShell と Azure Resource Manager を使用して Azure に対する Hyper-V VM のディザスター リカバリーを設定する

[Azure Site Recovery](site-recovery-overview.md) は、Azure 仮想マシン (VM)、オンプレミスの VM、および物理サーバーのレプリケーション、フェールオーバー、復旧を調整して、ビジネス継続性とディザスター リカバリー (BCDR) 戦略に貢献します。

この記事では、Windows PowerShell と Azure Resource Manager を使って、Hyper-V VM を Azure にレプリケートする方法を説明します。 この記事で使用している例では、Hyper-V ホストで実行されている 1 つの VM を Azure にレプリケートする方法を示します。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell は、Windows PowerShell を使用して Azure を管理するためのコマンドレットを提供します。 Azure 内のサーバーに対する保護と復旧は、Azure PowerShell 用 Azure Resource Manager に用意されている Site Recovery の PowerShell コマンドレットを使って行うことができます。

この記事の内容を使用するには PowerShell の専門家である必要はありませんが、モジュール、コマンドレット、セッションなどの基本的な概念を理解していることを前提としています。 詳細については、「[PowerShell ドキュメント](/powershell)」と [Azure Resource Manager での Azure PowerShell の使用](../azure-resource-manager/management/manage-resources-powershell.md)に関する記事を参照してください。

> [!NOTE]
> クラウド ソリューション プロバイダー (CSP) プログラムに参加しているマイクロソフト パートナーは、顧客のサーバーの保護を、それぞれの CSP サブスクリプション (テナント サブスクリプション) に対して構成および管理することができます。

## <a name="before-you-start"></a>開始する前に

次の前提条件を満たしていることを確認してください。

- [Microsoft Azure](https://azure.microsoft.com/) アカウント。 アカウントがなくても、 [無料試用版](https://azure.microsoft.com/pricing/free-trial/)を使用できます。 また、「 [Azure Site Recovery Manager の料金](https://azure.microsoft.com/pricing/details/site-recovery/)」も参照してください。
- Azure PowerShell。 今回のリリースとそのインストール方法については、「[Azure PowerShell のインストール](/powershell/azure/install-az-ps)」を参照してください。

また、この記事で説明されている特定の例には、次の前提条件があります。

- 1 つ以上の VM を含む Windows Server 2012 R2 または Microsoft Hyper-V Server 2012 R2 を実行する Hyper-V ホスト。 Hyper-V サーバーは、直接、またはプロキシを経由して、インターネットに接続されている必要があります。
- レプリケートする VM は、[こちらの前提条件](hyper-v-azure-support-matrix.md#replicated-vms)に従う必要があります。

## <a name="step-1-sign-in-to-your-azure-account"></a>手順 1. Azure アカウントにログインする

1. PowerShell コンソールを開いて次のコマンドを実行し、Azure アカウントにログインします。 `Connect-AzAccount` コマンドレットを実行すると、アカウントの資格情報の入力を求める Web ページが表示されます。
   - または、**Credential** パラメーターを使用して、`Connect-AzAccount` コマンドレットにパラメーターとしてアカウント資格情報を組み込むこともできます。
   - CSP パートナーがテナントの代理として活動している場合は、tenantID またはテナントのプライマリ ドメイン名を使用して顧客をテナントとして指定します。 例: `Connect-AzAccount -Tenant "fabrikam.com"`
1. 1 つのアカウントが複数のサブスクリプションを持つことができるため、使用するサブスクリプションをアカウントに関連付けます。

   ```azurepowershell
   Set-AzContext -Subscription $SubscriptionName
   ```

1. 次のコマンドを使用して、該当するサブスクリプションに対して Recovery Services 用と Site Recovery 用の Azure プロバイダーが登録され、使用できる状態になっていることを確認します。

   ```azurepowershell
   Get-AzResourceProvider -ProviderNamespace  Microsoft.RecoveryServices
   ```

1. コマンド出力で **RegistrationState** が **Registered** に設定されていることを確認し、手順 2. に進みます。 設定されていない場合は、次のコマンドを実行して、登録されていないプロバイダーを該当するサブスクリプションに登録する必要があります。

   ```azurepowershell
   Register-AzResourceProvider -ProviderNamespace Microsoft.RecoveryServices
   ```

1. 次のコマンドを使用して、プロバイダーが正しく登録されたことを確認します。

   ```azurepowershell
   Get-AzResourceProvider -ProviderNamespace  Microsoft.RecoveryServices
   ```

## <a name="step-2-set-up-the-vault"></a>手順 2: コンテナーを設定する

1. 資格情報コンテナーの作成先となる Azure Resource Manager リソース グループを作成するか、既存のリソース グループを使用します。 次のように、新しいリソース グループを作成します。 `$ResourceGroupName` 変数には、作成するリソース グループの名前が含まれ、$Geo 変数には、リソース グループを作成する Azure リージョン (たとえば、"Brazil South") が含まれます。

   ```azurepowershell
   New-AzResourceGroup -Name $ResourceGroupName -Location $Geo
   ```

1. サブスクリプション内のリソース グループの一覧を取得するには、`Get-AzResourceGroup` コマンドレットを実行します。
1. 次のコマンドを使用して、Azure Recovery Services の新しい資格情報コンテナーを作成します。

   ```azurepowershell
   $vault = New-AzRecoveryServicesVault -Name <string> -ResourceGroupName <string> -Location <string>
   ```

`Get-AzRecoveryServicesVault` コマンドレットを使用して、既存のコンテナーの一覧を取得できます。

## <a name="step-3-set-the-recovery-services-vault-context"></a>手順 3. Recovery Services 資格情報コンテナーのコンテキストを設定する

次のように、コンテナーのコンテキストを設定します。

```azurepowershell
Set-AzRecoveryServicesAsrVaultContext -Vault $vault
```

## <a name="step-4-create-a-hyper-v-site"></a>手順 4: Hyper-V サイトを作成する

1. 次のコマンドを使用して、新しい Hyper-V サイトを作成します。

   ```azurepowershell
   $sitename = "MySite"                #Specify site friendly name
   New-AzRecoveryServicesAsrFabric -Type HyperVSite -Name $sitename
   ```

1. このコマンドレットは Site Recovery ジョブを起動してサイトを作成し、Site Recovery ジョブ オブジェクトを返します。 ジョブの完了を待って、ジョブが正常に完了したことを確認します。
1. `Get-AzRecoveryServicesAsrJob` コマンドレットを使用してジョブ オブジェクトを取得し、ジョブの現在の状態を確認します。
1. 次のように、サイトの登録キーを生成してダウンロードします。

   ```azurepowershell
   $SiteIdentifier = Get-AzRecoveryServicesAsrFabric -Name $sitename | Select-Object -ExpandProperty SiteIdentifier
   $path = Get-AzRecoveryServicesVaultSettingsFile -Vault $vault -SiteIdentifier $SiteIdentifier -SiteFriendlyName $sitename
   ```

1. ダウンロードしたキーを Hyper-V ホストにコピーします。 このキーは、Hyper-V ホストをサイトに登録するときに必要になります。

## <a name="step-5-install-the-provider-and-agent"></a>手順 5: プロバイダーとエージェントをインストールする

1. 最新版のプロバイダーのインストーラーを[マイクロソフト](https://aka.ms/downloaddra)からダウンロードします。
1. Hyper-V ホストでインストーラーを実行します。
1. インストールが完了したら、そのまま登録手順に進みます。
1. メッセージに従って、ダウンロードしたキーを入力し、Hyper-V ホストの登録を完了します。
1. 次のように、Hyper-V ホストがサイトに登録されたことを確認します。

   ```azurepowershell
   $server = Get-AzRecoveryServicesAsrFabric -Name $siteName | Get-AzRecoveryServicesAsrServicesProvider -FriendlyName $server-friendlyname
   ```

Hyper-V コア サーバーを実行している場合は、セットアップ ファイルをダウンロードして、これらの手順に従います。

1. 次のコマンドを実行して、_AzureSiteRecoveryProvider.exe_ からローカル ディレクトリにファイルを抽出します。

   ```console
   AzureSiteRecoveryProvider.exe /x:. /q
   ```

1. 次のコマンドを実行します。

   ```console
   .\setupdr.exe /i
   ```

   結果は、 _%ProgramData%\ASRLogs\DRASetupWizard.log_ に記録されます。

1. このコマンドを実行してサーバーを登録します。

   ```console
   cd  C:\Program Files\Microsoft Azure Site Recovery Provider\DRConfigurator.exe" /r /Friendlyname "FriendlyName of the Server" /Credentials "path to where the credential file is saved"
   ```

## <a name="step-6-create-a-replication-policy"></a>手順 6: レプリケーション ポリシーを作成する

開始する前に、指定したストレージ アカウントがコンテナーと同じ Azure リージョンに存在しており、geo レプリケーションが有効になっている必要があります。

1. 次のように、レプリケーション ポリシーを作成します。

   ```azurepowershell
   $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
   $PolicyName = “replicapolicy”
   $Recoverypoints = 6                    #specify the number of recovery points
   $storageaccountID = Get-AzStorageAccount -Name "mystorea" -ResourceGroupName "MyRG" | Select-Object -ExpandProperty Id

   $PolicyResult = New-AzRecoveryServicesAsrPolicy -Name $PolicyName -ReplicationProvider “HyperVReplicaAzure” -ReplicationFrequencyInSeconds $ReplicationFrequencyInSeconds -NumberOfRecoveryPointsToRetain $Recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId $storageaccountID
   ```

1. 返されたジョブをチェックし、レプリケーション ポリシーが正常に作成されたことを確認します。

1. 次のように、サイトに対応する保護コンテナーを取得します。

   ```azurepowershell
   $protectionContainer = Get-AzRecoveryServicesAsrProtectionContainer
   ```

1. 次のように、保護コンテナーをレプリケーション ポリシーに関連付けます。

   ```azurepowershell
   $Policy = Get-AzRecoveryServicesAsrPolicy -FriendlyName $PolicyName
   $associationJob = New-AzRecoveryServicesAsrProtectionContainerMapping -Name $mappingName -Policy $Policy -PrimaryProtectionContainer $protectionContainer[0]
   ```

1. 関連付けが正常に完了するのを待ちます。

1. 保護コンテナー マッピングを取得します。

   ```azurepowershell
   $ProtectionContainerMapping = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $protectionContainer
   ```

## <a name="step-7-enable-vm-protection"></a>手順 7: VM 保護を有効にする

1. 次のように、保護する VM に対応する保護可能な項目を取得します。

   ```azurepowershell
   $VMFriendlyName = "Fabrikam-app"          #Name of the VM
   $ProtectableItem = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName
   ```

1. VM を保護します。 保護対象の VM に複数のディスクが接続されている場合は、**OSDiskName** パラメーターを使用してオペレーティング システム ディスクを指定します。

   ```azurepowershell
   $OSType = "Windows"          # "Windows" or "Linux"
   $DRjob = New-AzRecoveryServicesAsrReplicationProtectedItem -ProtectableItem $VM -Name $VM.Name -ProtectionContainerMapping $ProtectionContainerMapping -RecoveryAzureStorageAccountId $StorageAccountID -OSDiskName $OSDiskNameList[$i] -OS $OSType -RecoveryResourceGroupId $ResourceGroupID
   ```

1. 初回レプリケーション後、VM が保護された状態になるまで待ちます。 これには、レプリケートされるデータ量と Azure で使用できるアップストリーム帯域幅などの要因に応じて、しばらく時間がかかることがあります。 保護された状態になると、ジョブの State および StateDescription は次のように更新されます。

   ```console
   PS C:\> $DRjob = Get-AzRecoveryServicesAsrJob -Job $DRjob

   PS C:\> $DRjob | Select-Object -ExpandProperty State
   Succeeded

   PS C:\> $DRjob | Select-Object -ExpandProperty StateDescription
   Completed
   ```

1. 復旧のプロパティ (VM ロール サイズなど)、およびフェールオーバー後に VM NIC を接続する Azure ネットワークを更新します。

   ```console
   PS C:\> $nw1 = Get-AzVirtualNetwork -Name "FailoverNw" -ResourceGroupName "MyRG"

   PS C:\> $VMFriendlyName = "Fabrikam-App"

   PS C:\> $rpi = Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

   PS C:\> $UpdateJob = Set-AzRecoveryServicesAsrReplicationProtectedItem -InputObject $rpi -PrimaryNic $VM.NicDetailsList[0].NicId -RecoveryNetworkId $nw1.Id -RecoveryNicSubnetName $nw1.Subnets[0].Name

   PS C:\> $UpdateJob = Get-AzRecoveryServicesAsrJob -Job $UpdateJob

   PS C:\> $UpdateJob | Select-Object -ExpandProperty state

   PS C:\> Get-AzRecoveryServicesAsrJob -Job $job | Select-Object -ExpandProperty state

   Succeeded
   ```

> [!NOTE]
> Azure で CMK が有効になっているマネージド ディスクにレプリケートする場合は、Az PowerShell 3.3.0 以降を使用して次の手順を実行します。
>
> 1. VM のプロパティを更新して、マネージド ディスクへのフェールオーバーを有効にします
> 1. `Get-AzRecoveryServicesAsrReplicationProtectedItem` コマンドレットを使用して、保護された項目の各ディスクのディスク ID を取得します
> 1. `New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"` コマンドレットを使用して、ディスク暗号化セットに対するディスク ID のマッピングを含めるディクショナリ オブジェクトを作成します。 これらのディスク暗号化セットは、ターゲット リージョンで事前に作成されている必要があります。
> 1. ディクショナリ オブジェクトを **DiskIdToDiskEncryptionSetMap** パラメーターで渡すことにより、`Set-AzRecoveryServicesAsrReplicationProtectedItem` コマンドレットを使用して VM のプロパティを更新します。

## <a name="step-8-run-a-test-failover"></a>手順 8:テスト フェールオーバーの実行

1. 次のように、テスト フェールオーバーを実行します。

   ```azurepowershell
   $nw = Get-AzVirtualNetwork -Name "TestFailoverNw" -ResourceGroupName "MyRG" #Specify Azure vnet name and resource group

   $rpi = Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

   $TFjob = Start-AzRecoveryServicesAsrTestFailoverJob -ReplicationProtectedItem $VM -Direction PrimaryToRecovery -AzureVMNetworkId $nw.Id
   ```

1. Azure でテスト VM が作成されていることを確認します。 テスト フェールオーバー ジョブは、Azure にテスト VM を作成した後に中断されます。
1. クリーンアップして、テスト フェールオーバーを完了するには、次を実行します。

   ```azurepowershell
   $TFjob = Start-AzRecoveryServicesAsrTestFailoverCleanupJob -ReplicationProtectedItem $rpi -Comment "TFO done"
   ```

## <a name="next-steps"></a>次のステップ

Azure Site Recovery と Azure Resource Manager PowerShell コマンドレットの[詳細を確認](/powershell/module/az.recoveryservices)します。
