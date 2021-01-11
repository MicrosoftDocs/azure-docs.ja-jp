---
title: マネージド ディスク ストレージを Standard SSD と Premium SSD の間で変換する
description: Azure PowerShell を使用し、Azure マネージド ディスクを Standard から Premium に、または Premium から Standard に変換する方法。
author: roygara
ms.service: virtual-machines-windows
ms.topic: how-to
ms.date: 02/22/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: d81cd0ac57a5a18d90144584e8705cbffcba6f9b
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88871430"
---
# <a name="update-the-storage-type-of-a-managed-disk"></a>マネージド ディスクのストレージの種類を更新する

Azure マネージド ディスクには、ディスクの種類が 4 つあります。Azure Ultra Disk、Premium SSD、Standard SSD、Standard HDD です。 パフォーマンスのニーズに合わせて、3 つの GA ディスクの種類 (Premium SSD、Standard SSD、および Standard HDD) を切り替えることができます。 まだ Ultra Disk との切り替えはできません。新しいディスクをデプロイする必要があります。

この機能は、アンマネージド ディスクではサポートされていません。 ただし、ディスクの種類を切り替えられるようにするために、簡単に[アンマネージド ディスクをマネージド ディスクに変換](convert-unmanaged-to-managed-disks.md)できます。

 

## <a name="prerequisites"></a>前提条件

* 変換作業には仮想マシン (VM) の再起動が必要なので、既に設定されているメンテナンス期間中にディスク ストレージの移行をスケジュールするようにします。
* お使いのディスクがアンマネージド ディスクの場合、最初に[マネージド ディスクに変換する](convert-unmanaged-to-managed-disks.md)と、ストレージ オプションを切り替えることができます。

## <a name="switch-all-managed-disks-of-a-vm-between-premium-and-standard"></a>VM のすべてのマネージド ディスクを Premium と Standard の間で切り替える

この例では、VM のすべてのディスクを Standard ストレージから Premium ストレージに、または Premium ストレージから Standard ストレージに変換する方法について説明します。 Premium マネージド ディスクを使用するには、Premium Storage に対応している [VM のサイズ](../sizes.md)を使用している必要があります。 この例は、Premium ストレージに対応するサイズへの切り替えも行います。

```azurepowershell-interactive
# Name of the resource group that contains the VM
$rgName = 'yourResourceGroup'

# Name of the your virtual machine
$vmName = 'yourVM'

# Choose between Standard_LRS and Premium_LRS based on your scenario
$storageType = 'Premium_LRS'

# Premium capable size
# Required only if converting storage from Standard to Premium
$size = 'Standard_DS2_v2'

# Stop and deallocate the VM before changing the size
Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force

$vm = Get-AzVM -Name $vmName -resourceGroupName $rgName

# Change the VM size to a size that supports Premium storage
# Skip this step if converting storage from Premium to Standard
$vm.HardwareProfile.VmSize = $size
Update-AzVM -VM $vm -ResourceGroupName $rgName

# Get all disks in the resource group of the VM
$vmDisks = Get-AzDisk -ResourceGroupName $rgName 

# For disks that belong to the selected VM, convert to Premium storage
foreach ($disk in $vmDisks)
{
    if ($disk.ManagedBy -eq $vm.Id)
    {
        $disk.Sku = [Microsoft.Azure.Management.Compute.Models.DiskSku]::new($storageType)
        $disk | Update-AzDisk
    }
}

Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>Standard と Premium の間で個別のマネージド ディスクを切り替える

開発/テスト ワークロードでは、コストを削減するために Standard ディスクと Premium ディスクを混在させたい場合があります。 パフォーマンスを上げる必要があるディスクだけをアップグレードするように選択できます。 この例では、1 つの VM ディスクを Standard ストレージから Premium ストレージに、または Premium ストレージから Standard ストレージに変換する方法について説明します。 Premium マネージド ディスクを使用するには、Premium Storage に対応している [VM のサイズ](../sizes.md)を使用している必要があります。 この例では、Premium ストレージに対応するサイズに切り替える方法も示しています。

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between Standard_LRS and Premium_LRS based on your scenario
$storageType = 'Premium_LRS'
# Premium capable size 
$size = 'Standard_DS2_v2'

$disk = Get-AzDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Change the VM size to a size that supports Premium storage
# Skip this step if converting storage from Premium to Standard
$vm.HardwareProfile.VmSize = $size
Update-AzVM -VM $vm -ResourceGroupName $rgName

# Update the storage type
$disk.Sku = [Microsoft.Azure.Management.Compute.Models.DiskSku]::new($storageType)
$disk | Update-AzDisk

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="convert-managed-disks-from-standard-to-premium-in-the-azure-portal"></a>Azure portal でマネージド ディスクを Standard から Premium に変換する

次の手順に従います。

1. [Azure portal](https://portal.azure.com) にサインインします。
2. ポータルの**仮想マシン**の一覧から VM を選択します。
3. VM が停止していない場合、VM の **[概要]** ウィンドウの一番上で **[停止]** を選択し、VM が停止するまで待ちます。
3. VM のウィンドウで、メニューから **[ディスク]** を選択します。
4. 変換するディスクを選択します。
5. メニューから **[構成]** を選択します。
6. **アカウントの種類** を **Standard HDD** から **Premium SSD** に変更します。
7. **[保存]** をクリックし、ディスク ウィンドウを閉じます。

ディスクの種類の変換は瞬時に行われます。 変換後、VM を起動できます。

## <a name="switch-managed-disks-between-standard-hdd-and-standard-ssd"></a>Standard HDD と Standard SSD の間でマネージド ディスクを切り替える 

この例では、1 つの VM ディスクを Standard HDD から Standard SSD に、または Standard SSD から Standard HDD に変換する方法について説明します。

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between Standard_LRS and StandardSSD_LRS based on your scenario
$storageType = 'StandardSSD_LRS'

$disk = Get-AzDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Update the storage type
$disk.Sku = [Microsoft.Azure.Management.Compute.Models.DiskSku]::new($storageType)
$disk | Update-AzDisk

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="next-steps"></a>次のステップ

[スナップショット](snapshot-copy-managed-disk.md)を使用して、VM の読み取り専用コピーを取得します。