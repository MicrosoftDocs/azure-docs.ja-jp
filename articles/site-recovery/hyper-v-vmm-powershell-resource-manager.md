---
title: Azure Site Recovery と PowerShell を使用してセカンダリ サイトに Hyper-V (VMM 使用) のディザスター リカバリーを設定する
description: Azure Site Recovery と PowerShell を使用して、VMM クラウド内の Hyper-V VM のセカンダリ VMM サイトへのディザスター リカバリーを設定する方法について説明します。
services: site-recovery
author: sujayt
manager: rochakm
ms.topic: article
ms.date: 1/10/2020
ms.author: sutalasi
ms.openlocfilehash: a4f7d330db9a4a0d9b435ebe7527e55e37c254e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87086219"
---
# <a name="set-up-disaster-recovery-of-hyper-v-vms-to-a-secondary-site-by-using-powershell-resource-manager"></a>PowerShell (Resource Manager) を使用して、Hyper-V VM のセカンダリ サイトへのディザスター リカバリーを設定する

この記事では、[Azure Site Recovery](site-recovery-overview.md) を使用して、System Center Virtual Machine Manager クラウドの Hyper-V VM をセカンダリ オンプレミス サイトの Virtual Machine Manager クラウドにレプリケートする手順を自動化する方法について説明します。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>前提条件

- [シナリオのアーキテクチャとコンポーネント](hyper-v-vmm-architecture.md)を確認する。
- すべてのコンポーネントの[サポート要件](./vmware-physical-secondary-support-matrix.md)を確認する。
- Virtual Machine Manager サーバーと Hyper-V ホストが[サポート要件](./vmware-physical-secondary-support-matrix.md)に準拠していることを確認する。
- レプリケートする VM が[レプリケートされるマシンのサポート要件](./vmware-physical-secondary-support-matrix.md)に準拠していることを確認する。

## <a name="prepare-for-network-mapping"></a>ネットワーク マッピングを準備する

[ネットワーク マッピング](hyper-v-vmm-network-mapping.md)は、ソース クラウドとターゲット クラウド内のオンプレミス Virtual Machine Manager VM ネットワーク間をマップします。 マッピングでは次が行われます。

- フェールオーバー後に VM を適切なターゲット VM ネットワークに接続します。
- ターゲット Hyper-V ホスト サーバーにレプリカ VM を適切に配置します。
- ネットワーク マッピングを構成しない場合、レプリカ VM はフェールオーバー後に VM ネットワークに接続されません。

次のように Virtual Machine Manager を準備します。

- ソースおよびターゲットの Virtual Machine Manager サーバー上にそれぞれ [Virtual Machine Manager 論理ネットワーク](/system-center/vmm/network-logical)があることを確認します。
  - ソース サーバー上の論理ネットワークは、Hyper-V ホストが配置されているソース クラウドと関連付けられている必要があります。
  - ターゲット サーバーの論理ネットワークは、ターゲット クラウドと関連付けられている必要があります。
- ソースおよびターゲットの Virtual Machine Manager サーバー上にそれぞれ [VM ネットワーク](/system-center/vmm/network-virtual)があることを確認します。 VM ネットワークは、各場所の論理ネットワークにリンクされている必要があります。
- ソース Hyper-V ホスト上の VM をソース VM ネットワークに接続します。

## <a name="prepare-for-powershell"></a>PowerShell の準備

Azure PowerShell を使用する準備が整っていることを確認します。

- PowerShell を既に使用している場合は、バージョン 0.8.10 以降にアップグレードします。 PowerShell の設定方法については、[こちら](/powershell/azure/)を参照してください。
- PowerShell の設定と構成を完了したら、[サービス コマンドレット](/powershell/azure/)を確認します。
- PowerShell のパラメーター値、入力、出力の使用方法の詳細については、[概要](/powershell/azure/get-started-azureps)に関するガイドを参照してください。

## <a name="set-up-a-subscription"></a>サブスクリプションを設定する

1. PowerShell から、Azure アカウントにサインインします。

   ```azurepowershell
   $UserName = "<user@live.com>"
   $Password = "<password>"
   $SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
   $Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $SecurePassword
   Connect-AzAccount #-Credential $Cred
   ```

1. サブスクリプションとサブスクリプション ID の一覧を取得します。 Recovery Services コンテナーを作成するサブスクリプションの ID をメモします。

   ```azurepowershell
   Get-AzSubscription
   ```

1. コンテナーのサブスクリプションを設定します。

   ```azurepowershell
   Set-AzContext –SubscriptionID <subscriptionId>
   ```

## <a name="create-a-recovery-services-vault"></a>Recovery Services コンテナーを作成する

1. Azure Resource Manager リソース グループを作成します (まだ存在しない場合)。

   ```azurepowershell
   New-AzResourceGroup -Name #ResourceGroupName -Location #location
   ```

1. 新しい Recovery Services コンテナーを作成します。 後ほど使用できるように、このコンテナー オブジェクトを変数に格納します。

   ```azurepowershell
   $vault = New-AzRecoveryServicesVault -Name #vaultname -ResourceGroupName #ResourceGroupName -Location #location
   ```

   コンテナー オブジェクトは、作成後に `Get-AzRecoveryServicesVault` コマンドレットを使用して取得できます。

## <a name="set-the-vault-context"></a>コンテナーのコンテキストを設定する

1. 既存のコンテナーを取得します。

   ```azurepowershell
   $vault = Get-AzRecoveryServicesVault -Name #vaultname
   ```

1. コンテナーのコンテキストを設定します。

   ```azurepowershell
   Set-AzRecoveryServicesAsrVaultContext -Vault $vault
   ```

## <a name="install-the-site-recovery-provider"></a>Site Recovery プロバイダーをインストールする

1. Virtual Machine Manager マシンで、次のコマンドを実行してディレクトリを作成します。

   ```azurepowershell
   New-Item -Path C:\ASR -ItemType Directory
   ```

1. ダウンロードしたプロバイダー設定ファイルを使用してファイルを抽出します。

   ```console
   pushd C:\ASR\
   .\AzureSiteRecoveryProvider.exe /x:. /q
   ```

1. プロバイダーをインストールし、インストールが完了するまで待ちます。

   ```console
   .\SetupDr.exe /i
   $installationRegPath = "HKLM:\Software\Microsoft\Microsoft System Center Virtual Machine Manager Server\DRAdapter"
   do
   {
     $isNotInstalled = $true;
     if(Test-Path $installationRegPath)
     {
       $isNotInstalled = $false;
     }
   }While($isNotInstalled)
   ```

1. サーバーをコンテナーに登録します。

   ```console
   $BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
   pushd $BinPath
   $encryptionFilePath = "C:\temp\".\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice
   ```

## <a name="create-and-associate-a-replication-policy"></a>レプリケーション ポリシーを作成して関連付ける

1. 次のように、レプリケーション ポリシーを作成します。この場合は Hyper-V 2012 R2 です。

   ```azurepowershell
   $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
   $PolicyName = “replicapolicy”
   $RepProvider = HyperVReplica2012R2
   $Recoverypoints = 24                    #specify the number of hours to retain recovery points
   $AppConsistentSnapshotFrequency = 4 #specify the frequency (in hours) at which app consistent snapshots are taken
   $AuthMode = "Kerberos"  #options are "Kerberos" or "Certificate"
   $AuthPort = "8083"  #specify the port number that will be used for replication traffic on Hyper-V hosts
   $InitialRepMethod = "Online" #options are "Online" or "Offline"

   $policyresult = New-AzRecoveryServicesAsrPolicy -Name $policyname -ReplicationProvider $RepProvider -ReplicationFrequencyInSeconds $Replicationfrequencyinseconds -NumberOfRecoveryPointsToRetain $recoverypoints -ApplicationConsistentSnapshotFrequencyInHours $AppConsistentSnapshotFrequency -Authentication $AuthMode -ReplicationPort $AuthPort -ReplicationMethod $InitialRepMethod
   ```

   > [!NOTE]
   > Virtual Machine Manager クラウドには、複数バージョンの Windows Server を実行する Hyper-V ホストを含めることができますが、レプリケーション ポリシーはオペレーティング システムの特定のバージョン用です。 さまざまなオペレーティング システムでさまざまなホストを実行している場合は、システムごとに個別のレプリケーション ポリシーを作成してください。 たとえば、Windows Server 2012 で実行されているホストが 5 台、Windows Server 2012 R2 で実行されているホストが 3 台ある場合は、2 つのレプリケーション ポリシーを作成します。 オペレーティング システムの種類ごとに 1 つずつ作成します。

1. プライマリ保護コンテナー (プライマリ Virtual Machine Manager クラウド) と復旧保護コンテナー (復旧 Virtual Machine Manager クラウド) を取得します。

   ```azurepowershell
   $PrimaryCloud = "testprimarycloud"
   $primaryprotectionContainer = Get-AzRecoveryServicesAsrProtectionContainer -FriendlyName $PrimaryCloud;

   $RecoveryCloud = "testrecoverycloud"
   $recoveryprotectionContainer = Get-AzRecoveryServicesAsrProtectionContainer -FriendlyName $RecoveryCloud;
   ```

1. フレンドリ名を使用して、作成したレプリケーション ポリシーを取得します。

   ```azurepowershell
   $policy = Get-AzRecoveryServicesAsrPolicy -FriendlyName $policyname
   ```

1. 保護コンテナー (Virtual Machine Manager クラウド) とレプリケーション ポリシーの関連付けを開始します。

   ```azurepowershell
   $associationJob  = New-AzRecoveryServicesAsrProtectionContainerMapping -Policy $Policy -PrimaryProtectionContainer $primaryprotectionContainer -RecoveryProtectionContainer $recoveryprotectionContainer
   ```

1. ポリシー関連付けジョブが完了するのを待ちます。 ジョブが完了したかどうかを確認するには、次の PowerShell スニペットを使用します。

   ```azurepowershell
   $job = Get-AzRecoveryServicesAsrJob -Job $associationJob

   if($job -eq $null -or $job.StateDescription -ne "Completed")
   {
     $isJobLeftForProcessing = $true;
   }
   ```

1. ジョブの処理が完了したら、次のコマンドを実行します。

   ```azurepowershell
   if($isJobLeftForProcessing)
   {
     Start-Sleep -Seconds 60
   }
   While($isJobLeftForProcessing)
   ```

操作の完了を確認するには、「[アクティビティを監視する](#monitor-activity)」の手順を実行します。

##  <a name="configure-network-mapping"></a>ネットワーク マッピングの構成

1. このコマンドを使用して、現在のコンテナーのサーバーを取得します。 このコマンドは、`$Servers` 配列変数に Site Recovery サーバーを格納します。

   ```azurepowershell
   $Servers = Get-AzRecoveryServicesAsrFabric
   ```

1. このコマンドを実行して、ソース Virtual Machine Manager サーバー用とターゲット Virtual Machine Manager サーバー用のネットワークを取得します。

   ```azurepowershell
   $PrimaryNetworks = Get-AzRecoveryServicesAsrNetwork -Fabric $Servers[0]

   $RecoveryNetworks = Get-AzRecoveryServicesAsrNetwork -Fabric $Servers[1]
   ```

   > [!NOTE]
   > ソース Virtual Machine Manager サーバーは、サーバー配列で 1 つ目または 2 つ目として指定できます。 Virtual Machine Manager サーバー名を確認し、ネットワークを適切に取得します。

1. このコマンドレットは、プライマリ ネットワークと復旧ネットワークの間のマッピングを作成します。 プライマリ ネットワークは、`$PrimaryNetworks` の最初の要素として指定されます。 復旧ネットワークは、`$RecoveryNetworks` の最初の要素として指定されます。

   ```azurepowershell
   New-AzRecoveryServicesAsrNetworkMapping -PrimaryNetwork $PrimaryNetworks[0] -RecoveryNetwork $RecoveryNetworks[0]
   ```

## <a name="enable-protection-for-vms"></a>VM の保護を有効にする

サーバー、クラウド、ネットワークを正しく構成したら、クラウド内の VM の保護を有効にすることができます。

1. 保護を有効にするには、次のコマンドを実行して保護コンテナーを取得します。

   ```azurepowershell
   $PrimaryProtectionContainer = Get-AzRecoveryServicesAsrProtectionContainer -FriendlyName $PrimaryCloudName
   ```

1. 次のように、保護エンティティ (VM) を取得します。

   ```azurepowershell
   $protectionEntity = Get-AzRecoveryServicesAsrProtectableItem -FriendlyName $VMName -ProtectionContainer $PrimaryProtectionContainer
   ```

1. VM のレプリケーションを有効にします。

   ```azurepowershell
   $jobResult = New-AzRecoveryServicesAsrReplicationProtectedItem -ProtectableItem $protectionentity -ProtectionContainerMapping $policy -VmmToVmm
   ```

> [!NOTE]
> Azure で CMK が有効になっているマネージド ディスクにレプリケートする場合は、Az PowerShell 3.3.0 以降を使用して次の手順を実行します。
>
> 1. VM のプロパティを更新して、マネージド ディスクへのフェールオーバーを有効にします
> 1. `Get-AzRecoveryServicesAsrReplicationProtectedItem` コマンドレットを使用して、保護された項目の各ディスクのディスク ID を取得します
> 1. `New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"` コマンドレットを使用して、ディスク暗号化セットに対するディスク ID のマッピングを含めるディクショナリ オブジェクトを作成します。 これらのディスク暗号化セットは、ターゲット リージョンで事前に作成されている必要があります。
> 1. ディクショナリ オブジェクトを **DiskIdToDiskEncryptionSetMap** パラメーターで渡すことにより、`Set-AzRecoveryServicesAsrReplicationProtectedItem` コマンドレットを使用して VM のプロパティを更新します。

## <a name="run-a-test-failover"></a>テスト フェールオーバーの実行

デプロイをテストするには、単一の仮想マシンについてテスト フェールオーバーを実行します。 複数の VM が含まれる復旧計画を作成し、その計画についてテスト フェールオーバーを実行することもできます。 テスト フェールオーバーは、孤立したネットワークでフェールオーバーと復旧のシミュレーションを実行します。

1. VM がフェールオーバーする VM を取得します。

   ```azurepowershell
   $Servers = Get-AzRecoveryServicesASRFabric
   $RecoveryNetworks = Get-AzRecoveryServicesAsrNetwork -Name $Servers[1]
   ```

1. テスト フェールオーバーを実行します。

   単一の VM の場合:

   ```azurepowershell
   $protectionEntity = Get-AzRecoveryServicesAsrProtectableItem -FriendlyName $VMName -ProtectionContainer $PrimaryprotectionContainer

   $jobIDResult = Start-AzRecoveryServicesAsrTestFailoverJob -Direction PrimaryToRecovery -ReplicationProtectedItem $protectionEntity -VMNetwork $RecoveryNetworks[1]
   ```

   復旧計画の場合:

   ```azurepowershell
   $recoveryplanname = "test-recovery-plan"

   $recoveryplan = Get-AzRecoveryServicesAsrRecoveryPlan -FriendlyName $recoveryplanname

   $jobIDResult = Start-AzRecoveryServicesAsrTestFailoverJob -Direction PrimaryToRecovery -RecoveryPlan $recoveryplan -VMNetwork $RecoveryNetworks[1]
   ```

操作の完了を確認するには、「[アクティビティを監視する](#monitor-activity)」の手順を実行します。

## <a name="run-planned-and-unplanned-failovers"></a>計画フェールオーバーと計画外のフェールオーバーの実行

1. 計画フェールオーバーを実行します。

   単一の VM の場合:

   ```azurepowershell
   $protectionEntity = Get-AzRecoveryServicesAsrProtectableItem -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

   $jobIDResult = Start-AzRecoveryServicesAsrPlannedFailoverJob -Direction PrimaryToRecovery -ReplicationProtectedItem $protectionEntity
   ```

   復旧計画の場合:

   ```azurepowershell
   $recoveryplanname = "test-recovery-plan"

   $recoveryplan = Get-AzRecoveryServicesAsrRecoveryPlan -FriendlyName $recoveryplanname

   $jobIDResult = Start-AzRecoveryServicesAsrPlannedFailoverJob -Direction PrimaryToRecovery -RecoveryPlan $recoveryplan
   ```

1. 計画外のフェールオーバーを実行します。

   単一の VM の場合:

   ```azurepowershell
   $protectionEntity = Get-AzRecoveryServicesAsrProtectableItem -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

   $jobIDResult = Start-AzRecoveryServicesAsrUnplannedFailoverJob -Direction PrimaryToRecovery -ReplicationProtectedItem $protectionEntity
   ```

   復旧計画の場合:

   ```azurepowershell
   $recoveryplanname = "test-recovery-plan"

   $recoveryplan = Get-AzRecoveryServicesAsrRecoveryPlan -FriendlyName $recoveryplanname

   $jobIDResult = Start-AzRecoveryServicesAsrUnplannedFailoverJob -Direction PrimaryToRecovery -RecoveryPlan $recoveryplan
   ```

## <a name="monitor-activity"></a>アクティビティを監視する

フェールオーバー アクティビティを監視するには、次のコマンドを使用します。 ジョブ間の処理が終了するまで待ちます。

```azurepowershell
Do
{
    $job = Get-AzRecoveryServicesAsrJob -TargetObjectId $associationJob.JobId;
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
```

## <a name="next-steps"></a>次のステップ

Site Recovery と Resource Manager PowerShell コマンドレットの詳細については、[こちら](/powershell/module/az.recoveryservices)を参照してください。
