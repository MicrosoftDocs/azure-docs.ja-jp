---
title: Azure Site Recovery - Azure PowerShell を使用して Azure 仮想マシンのレプリケーション時にディスクを除外する | Microsoft Docs
description: Azure PowerShell を使用して Azure Site Recovery 時に Azure 仮想マシンのディスクを除外する方法を説明します。
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 02/18/2019
ms.author: asgang
ms.openlocfilehash: 81d22250262351e3c1bbb2fe28960b3d158bbf57
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/29/2019
ms.locfileid: "70147062"
---
# <a name="exclude-disks-from-powershell-replication-of-azure-vms"></a>PowerShell を使用して Azure VM のレプリケーション時にディスクを除外する

この記事では、Azure VM をレプリケートするときにディスクを除外する方法について説明します。 ディスクを除外すると、消費されるレプリケーションの帯域幅や、それらのディスクで使用されるターゲット側のリソースを最適化できる場合があります。 現在、この機能は、Azure PowerShell を介してのみ使用できます。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>前提条件

開始する前に次の操作を実行してください。

- [ディザスター リカバリーのアーキテクチャとコンポーネント](azure-to-azure-architecture.md)について理解していることを確認する。
- すべてのコンポーネントの[サポート要件](azure-to-azure-support-matrix.md)を確認する。
- AzureRm PowerShell "Az" モジュールがあることを確認する。 PowerShell をインストールまたは更新するには、「[Install the Azure PowerShell module (Azure PowerShell モジュールのインストール)](https://docs.microsoft.com/powershell/azure/install-az-ps)」を参照してください。
- Recovery Services コンテナー、および保護された仮想マシンを 1 回以上作成したことを確認する。 まだの場合は、「[Set up disaster recovery for Azure virtual machines using Azure PowerShell (Azure PowerShell を使用して Azure 仮想マシンのディザスター リカバリーをセットアップする)](azure-to-azure-powershell.md)」の手順に従ってください。

## <a name="why-exclude-disks-from-replication"></a>レプリケーションからディスクを除外する理由
次の理由のために、レプリケーションからディスクを除外しなければならない場合があります。

- お使いの仮想マシンが、[データ変化率のレプリケートに対する Azure Site Recovery の制限](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix)に達している。

- 除外対象のディスクに頻繁に変更されるデータが存在するものの重要度が低いか、レプリケートする必要がない。

- データをレプリケートしないことで、ストレージ リソースとネットワーク リソースを節約したい。

## <a name="how-to-exclude-disks-from-replication"></a>レプリケーションからディスクを除外する方法

この例では、1 つの OS を持つ仮想マシンと米国東部リージョンにある 3 つのデータ ディスクを米国西部 2 リージョンにレプリケートします。 仮想マシンの名前は *AzureDemoVM* です。 ディスク 1 を除外し、ディスク 2 および 3 は保持します。

## <a name="get-details-of-the-virtual-machines-to-replicate"></a>レプリケートする仮想マシンの詳細を取得する

```azurepowershell
# Get details of the virtual machine
$VM = Get-AzVM -ResourceGroupName "A2AdemoRG" -Name "AzureDemoVM"

Write-Output $VM     
```

```
ResourceGroupName  : A2AdemoRG
Id                 : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/A2AdemoRG/providers/Microsoft.Compute/virtualMachines/AzureDemoVM
VmId               : 1b864902-c7ea-499a-ad0f-65da2930b81b
Name               : AzureDemoVM
Type               : Microsoft.Compute/virtualMachines
Location           : eastus
Tags               : {}
DiagnosticsProfile : {BootDiagnostics}
HardwareProfile    : {VmSize}
NetworkProfile     : {NetworkInterfaces}
OSProfile          : {ComputerName, AdminUsername, WindowsConfiguration, Secrets}
ProvisioningState  : Succeeded
StorageProfile     : {ImageReference, OsDisk, DataDisks}
```

仮想マシンのディスクの詳細を取得します。 この情報は、後で VM のレプリケーションを開始するときに使用されます。

```azurepowershell
$OSDiskVhdURI = $VM.StorageProfile.OsDisk.Vhd
$DataDisk1VhdURI = $VM.StorageProfile.DataDisks[0].Vhd
```

## <a name="replicate-an-azure-virtual-machine"></a>Azure 仮想マシンをレプリケートする

次の例では、キャッシュ ストレージ アカウント、レプリケーション ポリシー、およびマッピングが既に用意されていることを前提としています。 まだの場合は、「[Set up disaster recovery for Azure virtual machines using Azure PowerShell (Azure PowerShell を使用して Azure 仮想マシンのディザスター リカバリーをセットアップする)](azure-to-azure-powershell.md)」の手順に従ってください。

"*マネージド ディスク*" を使用している Azure 仮想マシンをレプリケートします。

```azurepowershell

#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration).

#OsDisk
$OSdiskId =  $vm.StorageProfile.OsDisk.ManagedDisk.Id
$RecoveryOSDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType

$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id `
         -DiskId $OSdiskId -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

# Data Disk 1 i.e StorageProfile.DataDisks[0] is excluded, so we will provide it during the time of replication. 

# Data disk 2
$datadiskId2  = $vm.StorageProfile.DataDisks[1].ManagedDisk.id
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.DataDisks[1]. StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[1]. StorageAccountType

$DataDisk2ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $CacheStorageAccount.Id `
         -DiskId $datadiskId2 -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

# Data Disk 3

$datadiskId3  = $vm.StorageProfile.DataDisks[2].ManagedDisk.id
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.DataDisks[2]. StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[2]. StorageAccountType

$DataDisk3ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $CacheStorageAccount.Id `
         -DiskId $datadiskId3 -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.
$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk2ReplicationConfig, $DataDisk3ReplicationConfig


#Start replication by creating a replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.
$TempASRJob = New-ASRReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId
```

レプリケーションの開始操作が成功すると、VM のデータが復旧リージョンにレプリケートされます。

Azure portal に移動すると、[レプリケートされたアイテム] の下にレプリケートされた VM が表示されます。

レプリケーション プロセスは、復旧リージョン内で仮想マシンのレプリケートする側のディスクのコピーをシード処理することによって始まります。 このフェーズは、初期レプリケーション フェーズと呼ばれます。

初期レプリケーションが完了すると、レプリケーションは差分同期フェーズに移行します。 この時点で、仮想マシンが保護されます。 保護された仮想マシンを選択すると、除外されたディスクがないかを確認できます。

## <a name="next-steps"></a>次の手順

[テスト フェールオーバーの実行](site-recovery-test-failover-to-azure.md)について学習する。
