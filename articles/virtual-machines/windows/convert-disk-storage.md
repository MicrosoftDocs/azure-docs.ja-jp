---
title: "Azure 管理ディスクのストレージを Standard から Premium に (または Premium から Standard に) 変換する | Microsoft Docs"
description: "Azure 管理ディスクを Azure PowerShell を使用して Standard から Premium に (または Premium から Standard に) 変換する方法。"
services: virtual-machines-windows
documentationcenter: 
author: ramankum
manager: kavithag
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 08/07/2017
ms.author: ramankum
ms.translationtype: HT
ms.sourcegitcommit: 760543dc3880cb0dbe14070055b528b94cffd36b
ms.openlocfilehash: 9e5c73ceb0ff7d9c18c9cf7128b69e40b9796874
ms.contentlocale: ja-jp
ms.lasthandoff: 08/10/2017

---

# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-and-vice-versa"></a>Azure 管理ディスクのストレージを Standard から Premium に (または Premium から Standard に) 変換する

管理ディスクには、[Premium](../../storage/storage-premium-storage.md) (SSD ベース) と [Standard](../../storage/storage-standard-storage.md) (HDD ベース) という 2 つのストレージ オプションがあります。 パフォーマンス上のニーズに基づいて、この 2 つのオプションを最小限のダウンタイムで簡単に切り替えることができます。 この機能は、管理されていないディスクに対して使用することはできません。 ただし、2 つのオプションを簡単に切り替えるために、簡単に[管理ディスクに変換](convert-unmanaged-to-managed-disks.md)できます。

この記事では、管理ディスクを Azure PowerShell を使用して Standard から Premium に (または Premium から Standard に) 変換する方法を示します。 インストールまたはアップグレードする必要がある場合は、[Azure PowerShell モジュールのインストールと構成](/powershell/azure/install-azurerm-ps.md)に関するページを参照してください。

## <a name="before-you-begin"></a>開始する前に

* 変換作業は VM の再起動を伴うので、既に設定されているメンテナンス期間中に ディスク ストレージの移行をスケジュールしてください。 
* 管理されていないディスクを使用している場合は、まず[管理ディスクに変換](convert-unmanaged-to-managed-disks.md)してから、この記事を参照して 2 つのストレージ オプションを切り替えてください。 


## <a name="convert-all-the-managed-disks-of-a-vm-from-standard-to-premium-and-vice-versa"></a>VM のすべての管理ディスクを Standard から Premium に (または Premium から Standard に) 変換する

次の例は、VM のすべてのディスクを Standard ストレージから Premium ストレージに切り替える方法を示しています。 Premium Managed Disks を使用するには、Premium Storage に対応している [VM のサイズ](sizes.md)を使用している必要があります。 この例は、Premium ストレージに対応するサイズへの切り替えも行います。

```powershell
# Name of the resource group that contains the VM
$rgName = 'yourResourceGroup'

# Name of the your virtual machine
$vmName = 'yourVM'

# Choose between StandardLRS and PremiumLRS based on your scenario
$storageType = 'PremiumLRS'

# Premium capable size
# Required only if converting storage from standard to premium
$size = 'Standard_DS2_v2'
$vm = Get-AzureRmVM -Name $vmName -resourceGroupName $rgName

# Stop and deallocate the VM before changing the size
Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force

# Change the VM size to a size that supports premium storage
# Skip this step if converting storage from premium to standard
$vm.HardwareProfile.VmSize = $size
Update-AzureRmVM -VM $vm -ResourceGroupName $rgName

# Get all disks in the resource group of the VM
$vmDisks = Get-AzureRmDisk -ResourceGroupName $rgName 

# For disks that belong to the selected VM, convert to premium storage
foreach ($disk in $vmDisks)
{
    if ($disk.OwnerId -eq $vm.Id)
    {
        $diskUpdateConfig = New-AzureRmDiskUpdateConfig –AccountType $storageType
        Update-AzureRmDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
        -DiskName $disk.Name
    }
}

Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
```
## <a name="convert-a-managed-disk-from-standard-to-premium-and-vice-versa"></a>管理ディスクを Standard から Premium に (または Premium から Standard に) 変換する

開発/テスト ワークロードでは、コストを削減するために Standard ディスクと Premium ディスクを混在させたい場合があります。 これは、優れたパフォーマンスを必要とするディスクのみを Premium ストレージにアップグレードすることで実現できます。 次の例は、VM の 1 つのディスクを Standard から Premium に (または Premium から Standard に) 切り替える方法を示しています。 Premium Managed Disks を使用するには、Premium Storage に対応している [VM のサイズ](sizes.md)を使用している必要があります。 この例は、Premium ストレージに対応するサイズへの切り替えも行います。

```powershell

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between StandardLRS and PremiumLRS based on your scenario
$storageType = 'PremiumLRS'
# Premium capable size 
$size = 'Standard_DS2_v2'

$disk = Get-AzureRmDisk -DiskName $diskName -ResourceGroupName $rgName

# Get the ARM resource to get name and resource group of the VM
$vmResource = Get-AzureRmResource -ResourceId $disk.OwnerId
$vm = Get-AzureRmVM $vmResource.ResourceGroupName -Name $vmResource.ResourceName 

# Stop and deallocate the VM before changing the storage type
Stop-AzureRmVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name -Force

# Change the VM size to a size that supports premium storage
# Skip this step if converting storage from premium to standard
$vm.HardwareProfile.VmSize = $size
Update-AzureRmVM -VM $vm -ResourceGroupName $rgName

# Update the storage type
$diskUpdateConfig = New-AzureRmDiskUpdateConfig –AccountType $storageType
Update-AzureRmDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
-DiskName $disk.Name

Start-AzureRmVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="next-steps"></a>次のステップ

[スナップショット](snapshot-copy-managed-disk.md)を使用して、VM の読み取り専用コピーを取得します。


