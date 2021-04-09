---
title: 近接配置グループで実行されている Azure VM をレプリケートする
description: Azure Site Recovery を使用して、近接配置グループで実行されている Azure VM をレプリケートする方法について説明します。
author: Sharmistha-Rai
manager: gaggupta
ms.topic: how-to
ms.date: 02/11/2021
ms.openlocfilehash: a58ec80c13ee9ae0eceb019ab2fd7909fd6f369b
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104889282"
---
# <a name="replicate-azure-virtual-machines-running-in-proximity-placement-groups-to-another-region"></a>近接配置グループで実行されている Azure 仮想マシンを別のリージョンにレプリケートする

この記事では、近接配置グループで実行されている仮想マシンをセカンダリ リージョンにレプリケート、フェールオーバー、およびフェールバックする方法について説明します。

[近接配置グループ](../virtual-machines/windows/proximity-placement-groups-portal.md)は Azure 仮想マシンの論理的なグループ化機能であり、これを使用してアプリケーションに関連する VM 間のネットワーク待機時間を減らすことができます。 同じ近接配置グループ内にデプロイされている VM は、可能な限り物理的に接近させて配置されています。 近接配置グループが特に役立つのは、待機時間の影響を受けやすいワークロードの要件に対応する場合です。

## <a name="disaster-recovery-with-proximity-placement-groups"></a>近接配置グループでのディザスター リカバリー

一般的なシナリオでは、アプリケーションの各層の間でネットワーク待機時間が発生しないようにするために、複数の仮想マシンを 1 つの近接配置グループで実行することがあります。 これでアプリケーションのネットワーク待機時間は最適化できますが、Site Recovery を使用してリージョン レベルの障害から当該アプリケーションを保護することが望まれます。 Site Recovery では、あるリージョンから別の Azure リージョンにデータがレプリケートされ、フェールオーバーの際にディザスター リカバリー リージョンでマシンが稼働されます。

## <a name="considerations"></a>考慮事項

- ベスト エフォートは、仮想マシンを近接配置グループにフェールオーバー/フェールバックすることです。 ただし、フェールオーバー/フェールバックの際に近接配置グループ内で VM を稼働できない場合でも、フェールオーバー/フェールバックは行われ、近接配置グループの外部に仮想マシンが作成されます。
- 近接配置グループに可用性セットが固定されていて、かつフェールオーバー/フェールバックの際に可用性セット内の VM の間に割り当て制約が適用される場合は、可用性セットと近接配置グループの外部に仮想マシンが作成されます。
- アンマネージド ディスクでは、近接配置グループでの Site Recovery がサポートされません。

> [!NOTE]
> Azure Site Recovery は、Hyper-V から Azure へのシナリオでマネージド ディスクからのフェールバックをサポートしていません。 そのため、Azure の近接配置グループから Hyper-V へのフェールバックはサポートされていません。

## <a name="set-up-disaster-recovery-for-vms-in-proximity-placement-groups-via-portal"></a>ポータル経由で近接配置グループの VM に対するディザスター リカバリーを設定する

### <a name="azure-to-azure-via-portal"></a>Azure から Azure へ (ポータル経由)

仮想マシンのレプリケーションを有効にするには、VM ディザスター リカバリー ページを使用するか、または事前に作成したコンテナーに移動して [Site Recovery] セクションに移動してからレプリケーションを有効にするかを選択できます。 両方の手法を使用して、PPG 内の VM に対して Site Recovery を設定する方法を見てみましょう。

- IaaS VM DR ブレードを介してレプリケーションを有効にしながら DR リージョンの PPG を選択する方法:
  1. 仮想マシンに移動します。 左側のブレードの [操作] で、[ディザスター リカバリー] を選択します。
  2. [基本] タブで、VM のレプリケート先となる DR リージョンを選択します。 [詳細設定] へ移動します。
  3. ここには、お使いの VM の近接配置グループと、DR リージョンの PPG を選択するオプションが表示されます。 Site Recovery には、新しい近接配置グループを使用するオプションも用意されており、この既定のオプションの使用を選択すると自動的に作成されます。 必要な近接配置グループを自由に選択してから [Review + Start replication]\(レプリケーションの確認と開始\) に移動して、最後にレプリケーションを有効にします。

   :::image type="content" source="media/how-to-enable-replication-proximity-placement-groups/proximity-placement-group-a2a-1.png" alt-text="レプリケーションを有効にする。":::

- コンテナー ブレードを介してレプリケーションを有効にしながら DR リージョンの PPG を選択する方法:
  1. Recovery Services コンテナーに移動し、[Site Recovery] タブに移動します。
  2. [+ Enable Site Recovery]\(+ Site Recovery の有効化\) をクリックしてから、Azure 仮想マシンで [1:Enable Replication]\(1: レプリケーションの有効化\) を選択します (Azure VM をレプリケートしようとしている場合)
  3. [ソース] タブの必須フィールドに入力して、[次へ] をクリックします。
  4. レプリケーションを有効にする VM の一覧を [仮想マシン] タブで選択して、[次へ] をクリックします。
  5. ここには、DR リージョンの PPG を選択するオプションが表示されます。 Site Recovery には、新しい PPG を使用するオプションも用意されており、この既定のオプションの使用を選択すると自動的に作成されます。 必要な PPG を自由に選択してから、レプリケーションの有効化に進みます。

   :::image type="content" source="media/how-to-enable-replication-proximity-placement-groups/proximity-placement-group-a2a-2.png" alt-text="レプリケーションをコンテナー経由で有効にする。":::

VM に対してレプリケーションを有効にした後、DR リージョンの PPG の選択を簡単に更新できることに留意してください。

1. 仮想マシンに移動して左側のブレードの [操作] で、[ディザスター リカバリー] を選択します
2. [Compute and Network]\(コンピューティングとネットワーク\) ブレードに移動して、ページ上部の [編集] をクリックします
3. ターゲットの PPG を含む複数のターゲット設定を編集するためのオプションが表示されます。 VM のフェールオーバー先となる PPG を選択して、[保存] をクリックします。

### <a name="vmware-to-azure-via-portal"></a>VMware から Azure へ (ポータル経由)

ターゲット VM の近接配置グループは、VM のレプリケーションを有効にした後に設定できます。 ターゲット リージョンの PPG は、お客様の要件に応じて個別に作成してください。 その後、VM に対してレプリケーションを有効にした後、DR リージョンの PPG の選択を簡単に更新できます。

1. 仮想マシンをコンテナーから選択し、左側のブレードの [操作] で、[ディザスター リカバリー] を選択します。
2. [Compute and Network]\(コンピューティングとネットワーク\) ブレードに移動して、ページ上部の [編集] をクリックします
3. ターゲットの PPG を含む複数のターゲット設定を編集するためのオプションが表示されます。 VM のフェールオーバー先となる PPG を選択して、[保存] をクリックします。

   :::image type="content" source="media/how-to-enable-replication-proximity-placement-groups/proximity-placement-groups-update-v2a.png" alt-text="PPG V2A の更新":::

### <a name="hyper-v-to-azure-via-portal"></a>Hyper-V から Azure へ (ポータル経由)

ターゲット VM の近接配置グループは、VM のレプリケーションを有効にした後に設定できます。 ターゲット リージョンの PPG は、お客様の要件に応じて個別に作成してください。 その後、VM に対してレプリケーションを有効にした後、DR リージョンの PPG の選択を簡単に更新できます。

1. 仮想マシンをコンテナーから選択し、左側のブレードの [操作] で、[ディザスター リカバリー] を選択します。
2. [Compute and Network]\(コンピューティングとネットワーク\) ブレードに移動して、ページ上部の [編集] をクリックします
3. ターゲットの PPG を含む複数のターゲット設定を編集するためのオプションが表示されます。 VM のフェールオーバー先となる PPG を選択して、[保存] をクリックします。

   :::image type="content" source="media/how-to-enable-replication-proximity-placement-groups/proximity-placement-groups-update-h2a.png" alt-text="PPG H2A の更新":::

## <a name="set-up-disaster-recovery-for-vms-in-proximity-placement-groups-via-powershell"></a>PowerShell 経由で近接配置グループの VM に対するディザスター リカバリーを設定する

### <a name="prerequisites"></a>前提条件 

1. Azure PowerShell Az モジュールがあることを確認します。 Azure PowerShell をインストールまたはアップグレードする必要がある場合は、[Azure PowerShell モジュールのインストールと構成のガイド](/powershell/azure/install-az-ps)に関するページをご覧ください。
2. Azure PowerShell Az のバージョンは 4.1.0 以上である必要があります。 現在のバージョンを確認するには、次のコマンドを使用します。

    ```
    Get-InstalledModule -Name Az
    ```

### <a name="set-up-site-recovery-for-virtual-machines-in-proximity-placement-group"></a>近接配置グループ内の仮想マシンに対する Site Recovery の設定

> [!NOTE]
> ターゲットの近接配置グループの一意の ID が手元にあることを確認します。 新しい近接配置グループを作成している場合は、[こちら](../virtual-machines/windows/proximity-placement-groups.md#create-a-proximity-placement-group)のコマンドを確認し、既存の近接配置グループを使用している場合は、[こちら](../virtual-machines/windows/proximity-placement-groups.md#list-proximity-placement-groups)のコマンドを使用します。

### <a name="azure-to-azure"></a>Azure から Azure

1. お使いのアカウントに[サインイン](./azure-to-azure-powershell.md#sign-in-to-your-microsoft-azure-subscription)し、サブスクリプションを設定します。
2. レプリケートする予定の仮想マシンの詳細を取得します ([こちら](./azure-to-azure-powershell.md#get-details-of-the-virtual-machine-to-be-replicated)を参照)。
3. Recovery Services のコンテナーを[作成](./azure-to-azure-powershell.md#create-a-recovery-services-vault)し、コンテナーのコンテキストを[設定](./azure-to-azure-powershell.md#set-the-vault-context)します。
4. 仮想マシンのレプリケートを開始するようにコンテナーを準備します。 その際に、プライマリ リージョンと復旧リージョンの両方に対して[サービス ファブリック オブジェクト](./azure-to-azure-powershell.md#create-a-site-recovery-fabric-object-to-represent-the-primary-source-region)を作成します。
5. プライマリ ファブリックと復旧ファブリックの両方に対して Site Recovery 保護コンテナーを[作成](./azure-to-azure-powershell.md#create-a-site-recovery-protection-container-in-the-primary-fabric)します。
6. レプリケーション ポリシーを[作成](./azure-to-azure-powershell.md#create-a-replication-policy)します。
7. プライマリ保護コンテナーと復旧保護コンテナーの間の保護コンテナー マッピングを作成し (手順は[こちら](./azure-to-azure-powershell.md#create-a-protection-container-mapping-between-the-primary-and-recovery-protection-container))、フェールバック用の保護コンテナー マッピングを作成します ([こちら](./azure-to-azure-powershell.md#create-a-protection-container-mapping-for-failback-reverse-replication-after-a-failover)を参照)。
8. キャッシュ ストレージ アカウントを作成します (手順は[こちら](./azure-to-azure-powershell.md#create-cache-storage-account-and-target-storage-account))。
9. 必要なネットワーク マッピングを作成します ([こちら](./azure-to-azure-powershell.md#create-network-mappings)を参照)。
10. マネージド ディスクと一緒に Azure 仮想マシンをレプリケートするには、次の PowerShell コマンドレットを使用します。

```azurepowershell
#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)
#Make sure to replace the variables $OSdiskName with OS disk name.

#OS Disk
$OSdisk = Get-AzDisk -DiskName $OSdiskName -ResourceGroupName "A2AdemoRG"
$OSdiskId = $OSdisk.Id
$RecoveryOSDiskAccountType = $OSdisk.Sku.Name
$RecoveryReplicaDiskAccountType = $OSdisk.Sku.Name

$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id -DiskId $OSdiskId -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

#Make sure to replace the variables $datadiskName with data disk name.

#Data disk
$datadisk = Get-AzDisk -DiskName $datadiskName -ResourceGroupName "A2AdemoRG"
$datadiskId1 = $datadisk[0].Id
$RecoveryReplicaDiskAccountType = $datadisk[0].Sku.Name
$RecoveryTargetDiskAccountType = $datadisk[0].Sku.Name

$DataDisk1ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id -DiskId $datadiskId1 -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.

$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk1ReplicationConfig

#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.

$TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryProximityPlacementGroupId $targetPpg.Id
```

複数のデータ ディスクに対してレプリケーションを有効にする場合は、次の PowerShell コマンドレットを使用します。

```azurepowershell
#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)
#Make sure to replace the variables $OSdiskName with OS disk name.

#OS Disk
$OSdisk = Get-AzDisk -DiskName $OSdiskName -ResourceGroupName "A2AdemoRG"
$OSdiskId = $OSdisk.Id
$RecoveryOSDiskAccountType = $OSdisk.Sku.Name
$RecoveryReplicaDiskAccountType = $OSdisk.Sku.Name

$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id -DiskId $OSdiskId -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig

#Data disk

# Add data disks
Foreach( $disk in $VM.StorageProfile.DataDisks)
{
    $datadisk = Get-AzDisk -DiskName $datadiskName -ResourceGroupName "A2AdemoRG"
    $dataDiskId1 = $datadisk[0].Id
    $RecoveryReplicaDiskAccountType = $datadisk[0].Sku.Name
    $RecoveryTargetDiskAccountType = $datadisk[0].Sku.Name
    $DataDisk1ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id `
         -DiskId $dataDiskId1 -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType
    $diskconfigs += $DataDisk1ReplicationConfig
}

#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.

$TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryProximityPlacementGroupId $targetPpg.Id
```

PPG によるゾーン間レプリケーションを有効にすると、レプリケーションを開始するコマンドが次のように PowerShell コマンドレットと交換されます。

```azurepowershell
$TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryProximityPlacementGroupId $targetPpg.Id -RecoveryAvailabilityZone "2"
```

レプリケーションの開始操作が成功すると、仮想マシンのデータが復旧リージョンにレプリケートされます。

レプリケーション プロセスは、最初に、復旧リージョン内で仮想マシンのレプリケートする側のディスクのコピーをシード処理することによって始まります。 このフェーズは、初期レプリケーション フェーズと呼ばれます。

初期レプリケーションが完了すると、レプリケーションは差分同期フェーズに移行します。 この時点では、仮想マシンは保護されていて、それに対してテスト フェールオーバーの操作を実行できます。 初期レプリケーションの完了後、仮想マシンを表すレプリケートされた項目のレプリケーション状態は、保護された状態に移行します。

それに対応するレプリケーションの保護された項目の詳細を取得することによって、仮想マシンのレプリケーション状態とレプリケーション正常性を監視します。

```azurepowershell
Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $PrimaryProtContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```

11. テスト フェールオーバーを実行し、検証して、クリーンアップします (手順は[こちら](./azure-to-azure-powershell.md#do-a-test-failover-validate-and-cleanup-test-failover))。
12. フェールオーバーを実行します (手順は[こちら](./azure-to-azure-powershell.md#fail-over-to-azure))。
13. 次の PowerShell コマンドレットを使用して、ソース リージョンを再保護します。

```azurepowershell
#Create Cache storage account for replication logs in the primary region
$WestUSCacheStorageAccount = New-AzStorageAccount -Name "a2acachestoragewestus" -ResourceGroupName "A2AdemoRG" -Location 'West US' -SkuName Standard_LRS -Kind Storage


#Use the recovery protection container, new cache storage account in West US and the source region VM resource group 
Update-AzRecoveryServicesAsrProtectionDirection -ReplicationProtectedItem $ReplicationProtectedItem -AzureToAzure -ProtectionContainerMapping $WusToEusPCMapping -LogStorageAccountId $WestUSCacheStorageAccount.Id -RecoveryResourceGroupID $sourceVMResourcegroup.ResourceId -RecoveryProximityPlacementGroupId $vm.ProximityPlacementGroup.Id
```

14. レプリケーションを無効にします (手順は[こちら](./azure-to-azure-powershell.md#disable-replication))。

### <a name="vmware-to-azure-via-powershell"></a>VMware から Azure へ (PowerShell 経由)

1. Azure へのディザスター リカバリーのために[オンプレミス VMware サーバーを準備](./vmware-azure-tutorial-prepare-on-premises.md)してください。
2. お使いのアカウントにサインインし、[こちら](./vmware-azure-disaster-recovery-powershell.md#log-into-azure)で指定されているようにサブスクリプションを設定します。
3. Recovery Services のコンテナーを[設定](./vmware-azure-disaster-recovery-powershell.md#set-up-a-recovery-services-vault)し、[コンテナーのコンテキストを設定](./vmware-azure-disaster-recovery-powershell.md#set-the-vault-context)します。
4. コンテナーの登録を[検証](./vmware-azure-disaster-recovery-powershell.md#validate-vault-registration)します。
5. レプリケーション ポリシーを[作成](./vmware-azure-disaster-recovery-powershell.md#create-a-replication-policy)します。
6. vCenter Server を[追加](./vmware-azure-disaster-recovery-powershell.md#add-a-vcenter-server-and-discover-vms)し、仮想マシンを検出し、レプリケーション用にストレージ アカウントを[作成](./vmware-azure-disaster-recovery-powershell.md#create-storage-accounts-for-replication)します。
7. 次の PowerShell コマンドレットを使用して、VMware 仮想マシンをレプリケートします。詳細は以下をご覧ください。

```azurepowershell
#Get the target resource group to be used
$ResourceGroup = Get-AzResourceGroup -Name "VMwareToAzureDrPs"

#Get the target virtual network to be used
$RecoveryVnet = Get-AzVirtualNetwork -Name "ASR-vnet" -ResourceGroupName "asrrg"

#Get the protection container mapping for replication policy named ReplicationPolicy
$PolicyMap = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $ProtectionContainer | where PolicyFriendlyName -eq "ReplicationPolicy"

#Get the protectable item corresponding to the virtual machine CentOSVM1
$VM1 = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM1"

# Enable replication for virtual machine CentOSVM1 using the Az.RecoveryServices module 2.0.0 onwards to replicate to managed disks
# The name specified for the replicated item needs to be unique within the protection container. Using a random GUID to ensure uniqueness
$Job_EnableReplication1 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM1 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -logStorageAccountId $LogStorageAccount.Id -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1" -RecoveryProximityPlacementGroupId $targetPpg.Id
```

8. Get-ASRReplicationProtectedItem コマンドレットを使って、仮想マシンのレプリケーションの状態と正常性を確認することができます。

```azurepowershell
Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $ProtectionContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```

9. フェールオーバーの設定を構成します (手順は[こちら](./vmware-azure-disaster-recovery-powershell.md#configure-failover-settings))。
10. テスト フェールオーバーを[実行](./vmware-azure-disaster-recovery-powershell.md#run-a-test-failover)します。
11. Azure にフェールオーバーします (手順は[こちら](./vmware-azure-disaster-recovery-powershell.md#fail-over-to-azure))。

### <a name="hyper-v-to-azure-via-powershell"></a>Hyper-V から Azure へ (PowerShell 経由)

1. Azure へのディザスター リカバリーのために[オンプレミス Hyper-V サーバーを準備](./hyper-v-prepare-on-premises-tutorial.md)します。
2. Azure に[サインイン](./hyper-v-azure-powershell-resource-manager.md#step-1-sign-in-to-your-azure-account)します。
3. コンテナーを[設定](./hyper-v-azure-powershell-resource-manager.md#step-2-set-up-the-vault)し、Recovery Services コンテナーのコンテキストを[設定](./hyper-v-azure-powershell-resource-manager.md#step-3-set-the-recovery-services-vault-context)します。
4. Hyper-V サイトを[作成](./hyper-v-azure-powershell-resource-manager.md#step-4-create-a-hyper-v-site)します。
5. プロバイダーとエージェントを[インストール](./hyper-v-azure-powershell-resource-manager.md#step-5-install-the-provider-and-agent)します。
6. レプリケーション ポリシーを[作成](./hyper-v-azure-powershell-resource-manager.md#step-6-create-a-replication-policy)します。
7. 次の手順に従ってレプリケーションを有効にします。 
    
    a. 次のように、保護する VM に対応する保護可能な項目を取得します。

    ```azurepowershell
    $VMFriendlyName = "Fabrikam-app"          #Name of the VM
    $ProtectableItem = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName
    ```
    b. VM を保護します。 保護対象の VM に複数のディスクが接続されている場合は、OSDiskName パラメーターを使用してオペレーティング システム ディスクを指定します。
    
    ```azurepowershell
    $OSType = "Windows"          # "Windows" or "Linux"
    $DRjob = New-AzRecoveryServicesAsrReplicationProtectedItem -ProtectableItem $VM -Name $VM.Name -ProtectionContainerMapping $ProtectionContainerMapping -RecoveryAzureStorageAccountId   $StorageAccountID -OSDiskName $OSDiskNameList[$i] -OS $OSType -RecoveryResourceGroupId $ResourceGroupID -RecoveryProximityPlacementGroupId $targetPpg.Id
    ```
    c. 初回レプリケーション後、VM が保護された状態になるまで待ちます。 これには、レプリケートされるデータ量と Azure で使用できるアップストリーム帯域幅などの要因に応じて、しばらく時間がかかることがあります。 保護された状態になると、ジョブの State および StateDescription は次のように更新されます。 
    
    ```azurepowershell
    $DRjob = Get-AzRecoveryServicesAsrJob -Job $DRjob
    $DRjob | Select-Object -ExpandProperty State

    $DRjob | Select-Object -ExpandProperty StateDescription
    ```
    d. 復旧のプロパティ (VM ロール サイズなど)、およびフェールオーバー後に VM NIC を接続する Azure ネットワークを更新します。

    ```azurepowershell
    $nw1 = Get-AzVirtualNetwork -Name "FailoverNw" -ResourceGroupName "MyRG"

    $VMFriendlyName = "Fabrikam-App"

    $rpi = Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

    $UpdateJob = Set-AzRecoveryServicesAsrReplicationProtectedItem -InputObject $rpi -PrimaryNic $VM.NicDetailsList[0].NicId -RecoveryNetworkId $nw1.Id -RecoveryNicSubnetName $nw1.Subnets[0].Name

    $UpdateJob = Get-AzRecoveryServicesAsrJob -Job $UpdateJob

    $UpdateJob | Select-Object -ExpandProperty state

    Get-AzRecoveryServicesAsrJob -Job $job | Select-Object -ExpandProperty state
    ```
8. テスト [フェールオーバー](./hyper-v-azure-powershell-resource-manager.md#step-8-run-a-test-failover)を実行します。


## <a name="next-steps"></a>次のステップ

VMware の再保護と Azure へのフェールバックを実行する場合は、[こちら](./vmware-azure-prepare-failback.md)で説明されている手順に従います。

Hyper-V から Azure へのフェールオーバーを実行する場合は[こちら](./site-recovery-failover.md)で説明されている手順に従い、フェールバックを実行する場合は[こちら](./hyper-v-azure-failback.md)で説明されている手順に従います。

詳しくは、「[Site Recovery でのフェールオーバー](site-recovery-failover.md)」をご覧ください。
