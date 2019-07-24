---
title: Azure Site Recovery - Azure PowerShell を使用した Azure 仮想マシンのレプリケーション時にディスクを除外する | Microsoft Docs
description: Azure PowerShell を使用した Azure Site Recovery で Azure 仮想マシンのディスクを除外する方法を説明します。
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/18/2019
ms.author: asgang
ms.openlocfilehash: 1c278d810df7e5ba8701529a59987c9bb16fa40c
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/05/2019
ms.locfileid: "59044124"
---
# <a name="exclude-disks-from-replication-of-azure-vms-to-azure-using-azure-powershell"></a>Azure PowerShell を使用した Azure VM の Azure へのレプリケーションからディスクを除外する

この記事では、Azure VM をレプリケートするときにディスクを除外する方法について説明します。 除外することにより、レプリケーションによる帯域幅の消費や、このようなディスクによって使用されるターゲット側のリソースを最適化できます。 現在、この機能は、Azure PowerShell を介してのみ公開されています。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>前提条件

開始する前に次の操作を実行してください。

- [シナリオのアーキテクチャとコンポーネント](azure-to-azure-architecture.md)を理解している。
- すべてのコンポーネントの[サポート要件](azure-to-azure-support-matrix.md)を確認する。
- Azure PowerShell `Az` モジュールがある。 Azure PowerShell をインストールまたはアップグレードする必要がある場合は、[Azure PowerShell モジュールのインストールと構成のガイド](/powershell/azure/install-az-ps)に関するページをご覧ください。
- Recovery Services コンテナーを既に作成し、仮想マシンの保護を少なくとも 1 回実行している。 該当しない場合は、[こちら](azure-to-azure-powershell.md)のドキュメントを参照して実行します。

## <a name="why-exclude-disks-from-replication"></a>レプリケーションからディスクを除外する理由
レプリケーションからディスクを除外する必要性は、多くの場合、次の理由から生じます。

- お使いの仮想マシンが、[データ変化率のレプリケートに対する Azure Site Recovery の制限](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix)に達している。

- 除外対象のディスクに頻繁に変更されるデータが存在するものの重要度が低いか、レプリケートする必要がない。

- そのような変更頻度の高いデータをレプリケートしないことで、ストレージ リソースとネットワーク リソースを節約したい。


## <a name="how-to-exclude-disks-from-replication"></a>レプリケーションからディスクを除外する方法

この記事の例では、米国東部リージョン内の 1 個の OS ディスクと 3 個のデータ ディスクを持つ仮想マシンが米国西部 2 リージョンにレプリケートされます。 この例で使用されている仮想マシンの名前は AzureDemoVM です。 ディスク 1 が除外され、ディスク 2 と 3 が保持されます。

## <a name="get-details-of-the-virtual-machines-to-be-replicated"></a>レプリケートする仮想マシンの詳細を取得する

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


仮想マシンのディスクについて、ディスクの詳細を取得します。 ディスクの詳細は、後ほど、仮想マシンのレプリケーションを開始するときに使用されます。

```azurepowershell
$OSDiskVhdURI = $VM.StorageProfile.OsDisk.Vhd
$DataDisk1VhdURI = $VM.StorageProfile.DataDisks[0].Vhd
```

## <a name="replicate-azure-virtual-machine"></a>Azure 仮想マシンをレプリケートする

下の例では、キャッシュ ストレ―ジ アカウント、レプリケーション ポリシー、およびマッピングが既に用意されていることを前提としています。 該当しない場合は、[こちら](azure-to-azure-powershell.md)のドキュメントを参照して実行します。 


**マネージド ディスク**を使用している Azure 仮想マシンをレプリケートします。

```azurepowershell

#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)

#OsDisk
$OSdiskId =  $vm.StorageProfile.OsDisk.ManagedDisk.Id
$RecoveryOSDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType

$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id `
         -DiskId $OSdiskId -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

# Data Disk 1 i.e StorageProfile.DataDisks[0] is excluded so we will provide it during the time of replication 

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


#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.
$TempASRJob = New-ASRReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId
```

レプリケーションの開始操作が成功すると、仮想マシンのデータが復旧リージョンにレプリケートされます。

Azure portal に移動し、レプリケートされた項目で、仮想マシンがレプリケートされていることを確認できます。
レプリケーション プロセスは、最初に、復旧リージョン内で仮想マシンのレプリケートする側のディスクのコピーをシード処理することによって始まります。 このフェーズは、初期レプリケーション フェーズと呼ばれます。

初期レプリケーションが完了すると、レプリケーションは差分同期フェーズに移行します。 この時点で、仮想マシンが保護されます。 保護された仮想マシンのディスクをクリックして、そのディスクが除外されるかどうかを確認します。

## <a name="next-steps"></a>次の手順

テスト フェールオーバーの実行に関する[詳細を確認](site-recovery-test-failover-to-azure.md)する。
